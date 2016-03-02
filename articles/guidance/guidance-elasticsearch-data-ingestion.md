<properties
   pageTitle="Optimisation des performances d’ingestion de données grâce à Elasticsearch dans Azure | Microsoft Azure"
   description="Comment optimiser les performances d’ingestion de données grâce à Elasticsearch dans Azure"
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/05/2016"
   ms.author="mabsimms"/>

# Optimisation des performances d’ingestion de données grâce à Elasticsearch dans Azure

Cet article fait [partie d’une série](guidance-elasticsearch-introduction.md).

## Vue d'ensemble

Lors de la création d’une base de données de recherche, il est important de déterminer la meilleure façon de structurer le système afin qu’il puisse recevoir rapidement et efficacement des données interrogeables. Il convient à cet égard non seulement de considérer le choix de l’infrastructure sur laquelle sera implémenté le système, mais également de tenir compte des différentes optimisations que vous pouvez utiliser pour vous assurer que le système sera capable d’absorber les niveaux de flux de données attendus. Ce document décrit les options de déploiement et de configuration qu’il convient de prendre en compte pour un cluster Elasticsearch qui attend un taux élevé d’ingestion de données. Pour étayer son contenu sur la base de données solides, ce document présente également les résultats d’une comparaison de différentes configurations utilisant une importante charge d’ingestion de données. Les détails de la charge de travail sont décrits dans l’[Annexe](#appendix-the-bulk-load-data-ingestion-performance-test) fournie à la fin de ce document.

Ces comparatifs n’avaient pour but ni de générer les chiffres de performances absolus de l’exécution d’Elasticsearch, ni même de recommander une topologie donnée, mais plutôt d’illustrer les méthodes que vous pouvez utiliser pour évaluer les performances, dimensionner les nœuds de données et mettre en œuvre des clusters capables de répondre à vos propres exigences de performances. Lorsque vous dimensionnez vos propres systèmes, il est important de tester les performances compte tenu de vos charges de travail spécifiques. Collectez les données télémétriques qui vous permettront d’obtenir des informations sur la configuration matérielle optimale à utiliser ainsi que sur les facteurs de mise à l’échelle horizontale que vous devez prendre en compte. Vous devez notamment :

* Prendre en compte la taille globale de la charge utile envoyée, sans vous cantonner au nombre d’éléments contenus dans chaque demande d’insertion en bloc. Il peut être plus judicieux d’utiliser un plus petit nombre d’éléments en bloc dans chaque demande plutôt qu’un grand nombre, en fonction de la ressource disponible pour traiter chaque demande.

  > [AZURE.NOTE] Vous pouvez surveiller les effets d’une variation de la demande d’insertion en bloc à l’aide de Marvel, en utilisant les compteurs d’E/S *readbytes*/*writebytes* avec JMeter et des outils de système d’exploitation tels que *iostat* et *vmstat* sur Ubuntu.

* Effectuer des tests de performances et collecter les données de télémétrie pour mesurer les temps d’attente du traitement CPU et des E/S, les temps de latence du disque, le débit et les temps de réponse. Ces informations peuvent aider à identifier les goulots d’étranglement potentiels et à évaluer les coûts et les avantages liés à l’utilisation de Premium Storage. N’oubliez pas que l’utilisation du processeur et du disque peut être inégale sur l’ensemble des nœuds selon la manière dont les partitions et les réplicas sont répartis dans le cluster (certains nœuds peuvent contenir plus de partitions que d’autres).

* Évaluer la manière dont le nombre de demandes simultanées de votre charge de travail sera réparti au sein du cluster, et mesurer l’impact de l’utilisation de différents nombres de nœuds pour le traitement de cette charge de travail.

* Prendre en compte les possibilités d’extension des charges de travail à mesure que l’entreprise se développe. Évaluer l’impact de cette croissance sur les coûts des machines virtuelles et du stockage utilisés par les nœuds.

* Reconnaître qu’il peut être plus intéressant d’un point de vue économique d’utiliser un cluster comportant un plus grand nombre de nœuds avec des disques standards si votre scénario requiert un nombre important de demandes et si l’infrastructure de disque maintient un débit conforme à vos SLA. L’augmentation du nombre de nœuds peut toutefois introduire une surcharge, c’est-à-dire des communications inter-nœuds et une synchronisation supplémentaires.

* Comprendre qu’un plus grand nombre de cœurs par nœud peut générer davantage de trafic disque dans la mesure où cela implique potentiellement davantage de documents à traiter. Dans ce cas, mesurer l’utilisation du disque pour déterminer si le sous-système d’E/S risque de devenir un goulot d’étranglement et déterminer les avantages de l’utilisation de Premium Storage.

* Comparer, au moyen de tests et d’analyses, les résultats obtenus avec un plus grand nombre de nœuds associés à moins de cœurs par rapport à ceux obtenus avec moins de nœuds mais davantage de cœurs. Garder à l’esprit que l’augmentation du nombre de réplicas a pour effet de déléguer les demandes au cluster et peut vous obliger à ajouter des nœuds supplémentaires.

* Tenir compte du fait que l’utilisation de disques éphémères peut nécessiter une récupération plus fréquente des index.

* Mesurer l’utilisation du volume de stockage pour évaluer la capacité et la sous-utilisation du stockage. Par exemple, dans notre scénario, nous avons stocké 1,5 milliard de documents à l’aide de 350 Go de stockage.

* Mesurer les taux de transfert de vos charges de travail et évaluer dans quelle mesure vous êtes susceptible d’atteindre la limite de transfert de taux total d’E/S pour un compte de stockage donné dans lequel vous avez créé des disques virtuels.

La dernière partie de ce document décrit ces problèmes plus en détail.

## Remarques relatives à la conception des nœuds et des index

Dans un système qui doit prendre en charge une ingestion de données à grande échelle, vous devez vous poser les questions suivantes :

* **Ces données sont-elles extrêmement dynamiques ou relativement statiques ?** Plus les données sont dynamiques, plus la charge de maintenance pèsera sur Elasticsearch. Si les données sont répliquées, chaque réplica est conservé de façon synchrone. Pour les données très dynamiques qui n’ont qu’une durée de vie limitée ou qui peuvent être facilement reconstruites, il peut être intéressant de désactiver totalement la réplication. Cette option est décrite plus avant dans la section [Adaptation pour une ingestion de données à grande échelle](#_Considerations_for_Tuning).

* **Dans quelle mesure les données découvertes par le biais d’une recherche doivent-elles être à jour ?** Pour préserver les performances, Elasticsearch met en mémoire tampon autant de données que possible. Cela signifie que toutes les modifications sont immédiatement disponibles pour les requêtes de recherche. Le processus utilisé par Elasticsearch pour rendre les modifications persistantes et visibles est décrit en ligne dans le document [Making Changes Persistent](https://www.elastic.co/guide/en/elasticsearch/guide/current/translog.html#translog) (Processus de persistance des modifications). La vitesse à laquelle les données deviennent visibles est régie par le paramètre *refresh\_interval* de l’index approprié. Par défaut, cet intervalle est défini à 1 seconde. Mais toutes les situations ne nécessitent pas des actualisations aussi rapides. Par exemple, les index qui enregistrent des données de journal peuvent devoir faire face à un afflux rapide et continu d’informations qui devront être ingérées rapidement, sans pour autant que les informations soient immédiatement disponibles pour être interrogées. Dans ce cas, envisagez de réduire la fréquence des actualisations. Cette caractéristique est également décrite dans la section [Adaptation pour une ingestion de données à grande échelle](#_Considerations_for_Tuning).

* **À quelle vitesse les données sont-elles susceptibles de croître ?** La capacité de l’index est déterminée par le nombre de partitions spécifié lors de la création de l’index. Pour prendre en charge une croissance des données, spécifiez un nombre suffisant de partitions (la valeur par défaut est de 5). Si l’index est créé initialement sur un seul nœud, les cinq partitions seront situées sur ce nœud ; mais à mesure que le volume de données augmente, il est possible d’ajouter d’autres nœuds, auquel cas Elasticsearch distribuera dynamiquement les partitions entre les nœuds. Cependant, chaque partition présente une surcharge ; toutes les recherches d’un index porteront sur l’ensemble des partitions, ce qui signifie que la création d’un grand nombre de partitions pour une petite quantité de données peut ralentir les extractions de données (évitez le scénario des [partitions Kagillion](https://www.elastic.co/guide/en/elasticsearch/guide/current/kagillion-shards.html)).

    Certaines charges de travail (telles que la journalisation) peuvent créer un nouvel index chaque jour. Si vous constatez que le nombre de partitions est insuffisant compte tenu du volume de données, vous devrez le modifier avant de créer l’index suivant (les index existants ne seront pas affectés). Si vous devez distribuer les données existantes sur plusieurs partitions, vous avez la possibilité de réindexer les informations, de créer un index avec la configuration appropriée et d’y copier les données. Ce processus peut être transparent pour les applications si l’on utilise les [alias d’index](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-aliases.html).

* **Les données doivent-elles être partitionnées entre les utilisateurs dans un scénario d’architecture mutualisée ?** Vous pouvez créer des index distincts pour chaque utilisateur, mais cette approche peut s’avérer coûteuse si chaque utilisateur ne possède qu’une quantité moyenne de données. Au lieu de cela, envisagez de créer des [index partagés](https://www.elastic.co/guide/en/elasticsearch/guide/current/shared-index.html) et d’utiliser des [alias basés sur des filtres](https://www.elastic.co/guide/en/elasticsearch/guide/current/faking-it.html) pour orienter les demandes vers les données de chaque utilisateur. Pour conserver les données d’un utilisateur dans la même partition, remplacez la configuration de routage par défaut de l’index et acheminez les données compte tenu d’un attribut d’identification de l’utilisateur.

* **Quelle est la durée de vie des données ?** Si vous utilisez un ensemble de machines virtuelles Azure pour implémenter un cluster Elasticsearch, vous pouvez stocker des données éphémères sur un disque de système de ressources local plutôt que sur un lecteur connecté. L’utilisation d’une référence (SKU) de machine virtuelle qui utilise un disque SSD comme disque de ressources peut améliorer les performances d’E/S. Toutefois, toutes les informations conservées sur le disque de ressources sont temporaires et peuvent être perdues en cas de redémarrage de la machine virtuelle. Pour plus d’informations, reportez-vous à la section « When Will the Data on a Temporary Drive Be Lost » (À quels moments les données d’un disque temporaire peuvent-elles être perdues) du document [Understanding the temporary drive on Microsoft Azure Virtual Machines](http://blogs.msdn.com/b/mast/archive/2013/12/07/understanding-the-temporary-drive-on-windows-azure-virtual-machines.aspx) (Présentation du disque temporaire sur Microsoft Azure Virtual Machines). Si vous avez besoin de conserver les données entre deux redémarrages, créez des disques durs virtuels persistants pour conserver ces informations et connectez-les à la machine virtuelle.

* **Quel est le degré d’activité des données ?** Les disques durs virtuels Azure sont soumis à une limitation si la quantité d’activité de lecture/écriture dépasse les paramètres spécifiés (actuellement, 500 E/S pour un disque connecté à une machine virtuelle de niveau Standard et 5 000 E/S pour un disque Premium Storage). Pour réduire les risques de limitation et améliorer les performances d’E/S, envisagez de créer plusieurs disques durs virtuels pour chaque machine virtuelle et de configurer Elasticsearch pour répartir les données sur ces disques durs virtuels, tel que décrit dans la section [Disk and File System Requirements](#disk-and-file-system-requirements) (Exigences relatives aux disques et au système de fichiers) du document Implementing Elasticsearch on Azure (Implémentation d’Elasticsearch dans Azure).

    > [AZURE.NOTE] Vous devez sélectionner une configuration matérielle permettant de réduire le nombre d’opérations de lecture d’E/S disque en veillant à ce que vous disposiez d’une quantité de mémoire suffisante pour mettre en cache les données auxquelles vous accédez fréquemment. Ce processus est décrit dans la section [Memory Requirements](#memory-requirements) (Configuration de mémoire requise) du document Implementing Elasticsearch on Azure (Implémentation d’Elasticsearch dans Azure).

* **Quel type de charge de travail chaque nœud doit-il prendre en charge ?** Elasticsearch tire parti de la mémoire disponible pour mettre en cache les données (sous la forme du cache du système de fichiers) et pour conserver le segment de mémoire JVM, comme décrit dans la section [Memory Requirements](#memory-requirements) (Configuration de mémoire requise) du document Implementing Elasticsearch on Azure (Implémentation d’Elasticsearch dans Azure). En outre, le modèle de thread implémenté par Elasticsearch rend plus efficace l’utilisation de processeurs multicœurs sur des processeurs plus puissants dotés d’un moins grand nombre de cœurs.

    > [AZURE.NOTE] La quantité de mémoire, le nombre de cœurs de processeur et la quantité de disques disponibles sont limitées par la référence (SKU) de la machine virtuelle. Pour plus d’informations, consultez la page [Tarification de Virtual Machines](http://azure.microsoft.com/pricing/details/virtual-machines/) sur le site Web Azure.

### Options de machine virtuelle

Vous pouvez configurer des machines virtuelles dans Azure à l’aide d’un certain nombre de SKU différents. Les ressources disponibles pour une machine virtuelle Azure dépendent de la référence SKU sélectionnée. Chaque SKU offre une combinaison différente de cœurs, de mémoire et de stockage. Vous devez sélectionner une taille de machine virtuelle capable de supporter la charge de travail, mais qui sera également très rentable. Commencez par une configuration qui répond à vos exigences actuelles (effectuez un comparatif à titre de test, comme décrit plus loin dans ce document). Vous pouvez ensuite mettre à l’échelle un cluster en y ajoutant davantage de machines virtuelles exécutées sur les nœuds Elasticsearch.

La page [Tailles de machines virtuelles](virtual-machines-size-specs/) du site Web Azure présente les différentes options et références SKU disponibles pour les machines virtuelles.

Vous devez faire correspondre la taille et les ressources d’une machine virtuelle au rôle des nœuds exécutés sur la machine virtuelle.

Pour un nœud de données :

* Allouez jusqu’à 30 Go ou 50 % de la mémoire RAM disponible au segment de mémoire Java, en privilégiant la valeur la plus faible. Laissez le reste au système d’exploitation pour la mise en cache des fichiers. Si vous utilisez Linux, vous pouvez spécifier la quantité de mémoire à allouer au segment de mémoire Java en définissant la variable d’environnement ES\_HEAP\_SIZE avant d’exécuter Elasticsearch. Sinon, si vous utilisez Windows ou Linux, vous pouvez stipuler la taille de la mémoire à l’aide des paramètres *Xmx* et *Xms* lors du démarrage d’Elasticsearch.

    > [AZURE.NOTE]  En fonction de la charge de travail, il peut s’avérer moins efficace, sur le plan des performances, d’utiliser un nombre réduit de machines virtuelles volumineuses plutôt qu’un plus grand nombre de machines virtuelles de taille moyenne. Vous devez effectuer des tests pour déterminer si le surcroît de trafic réseau et d’efforts de maintenance compense la réduction de la contention de disque sur chaque nœud compte tenu des coûts associés à l’augmentation du nombre de cœurs disponibles.

* Utilisez des disques rapides (dans l’idéal, des disques SSD à faible latence) pour stocker les données Elasticsearch. Ce sujet est abordé plus en détail dans la section [Options de stockage](#storage-options).

* Utilisez plusieurs disques (de même taille) et répartissez les données sur ces disques. La référence SKU de vos machines virtuelles détermine le nombre maximal de disques de données que vous pouvez connecter. Pour plus d’informations, consultez la section [Disk and File System Requirements](#disk-and-file-system-requirements) (Exigences relatives aux disques et au système de fichiers).

* Utilisez un processeur multicœur (au moins 2 cœurs, mais 4 ou plus de préférence). Sélectionnez les processeurs en fonction du nombre de cœurs plutôt que de la puissance brute. Le modèle de thread utilisé par Elasticsearch pour gérer les demandes simultanées est plus efficace lorsqu’il est utilisé avec plusieurs cœurs plutôt qu’avec des processeurs hautes performances comportant moins de cœurs.

Pour un nœud de client :

* N’allouez pas de stockage disque aux données Elasticsearch. Les clients dédiés ne stockent pas de données sur le disque.

* Assurez-vous que la quantité de mémoire disponible est suffisante pour gérer les charges de travail. Les demandes d’insertion en bloc sont lues en mémoire avant l’envoi des données à divers nœuds de données, et les résultats des agrégations et requêtes s’accumulent dans la mémoire avant d’être renvoyés à l’application cliente. Pour déterminer la configuration optimale, évaluez vos propres charges de travail et surveillez l’utilisation de la mémoire à l’aide d’un outil tel que Marvel ou à l’aide des [informations JVM](https://www.elastic.co/guide/en/elasticsearch/guide/current/_monitoring_individual_nodes.html#_jvm_section) retournées avec l’API *node/stats* :

    ```http
    GET _nodes/stats
    ```

    Surveillez plus particulièrement la métrique *heap\_used\_percent* pour chaque nœud en veillant à conserver une taille de segment inférieure à 75 % de l’espace disponible.

* Assurez-vous de disposer d’un nombre de cœurs de processeur suffisant pour recevoir et traiter le volume de demandes attendu. Les demandes sont placées en file d’attente à leur réception avant d’être traitées et le volume des éléments pouvant être mis en attente dépend du nombre de cœurs de processeur sur chaque nœud. Vous pouvez surveiller les longueurs de files d’attente en utilisant les données contenues dans les [informations Threadpool](https://www.elastic.co/guide/en/elasticsearch/guide/current/_monitoring_individual_nodes.html#_threadpool_section) renvoyées à l’aide de l’API node/stats. Si le nombre de *refus* pour une file d’attente indique que les demandes sont refusées, cela signifie que le cluster est proche du goulot d’étranglement. Ce problème peut être dû à la bande passante de processeur, mais peut également être imputé à d’autres facteurs, tels que le manque de mémoire ou le ralentissement des performances d’E/S. Vous devez donc rapprocher ces informations d’autres statistiques afin de déterminer la cause du problème.

    > [AZURE.NOTE]  L’utilisation de nœuds de client peut être nécessaire ou non, en fonction de vos charges de travail. Les charges d’ingestion de données n’ont généralement aucun avantage à utiliser des clients dédiés, tandis que certaines recherches et agrégations peuvent s’exécuter plus rapidement. Préparez-vous à tester vos propres scénarios.

    > Les nœuds de client sont principalement utiles pour les applications qui utilisent l’API Transport Client pour se connecter au cluster. Vous pouvez également utiliser l’API Node Client, qui crée dynamiquement un client dédié pour l’application en utilisant les ressources de l’environnement hôte de l’application. Si vos applications utilisent l’API Node Client, il ne sera sans doute pas nécessaire que votre cluster contienne des nœuds d’un client dédié préconfiguré. Toutefois, n’oubliez pas qu’un nœud créé à l’aide de l’API Client Node représente un membre stratégique du cluster et, en tant que tel, participe à l’encombrement du réseau avec les autres nœuds ; le fait de démarrer et arrêter fréquemment des nœuds de client peut créer des perturbations inutiles sur l’ensemble du cluster.

Pour un nœud principal :

* N’allouez pas de stockage disque aux données Elasticsearch. Les nœuds principaux dédiés ne stockent pas de données sur le disque.

* Les besoins en processeur doivent être aussi réduits que possible.

* La quantité de mémoire requise dépend de la taille du cluster. Les informations concernant l’état du cluster sont conservées en mémoire. Pour les clusters de petite taille, la quantité de mémoire requise est minime, mais dans le cas d’un cluster volumineux et très actif, dans lequel des index sont fréquemment créés et où les partitions se déplacent, la quantité d’informations d’état peut augmenter sensiblement. Surveillez la taille du segment JVM pour déterminer s’il est nécessaire d’ajouter davantage de mémoire.

    > [AZURE.NOTE]  Pour garantir la fiabilité du cluster, veillez toujours à créer plusieurs nœuds principaux et à configurer les nœuds restants afin d’éviter tout risque de fractionnement des partitions. Idéalement, vous devez disposer d’un nombre de nœuds principaux impair. Ce sujet est décrit en détail dans le document « Configuring, Testing, and Analyzing Elasticsearch Resilience and Recovery » (Configuration, test et analyse de résilience et de récupération Elasticsearch).

### Options de stockage

Les machines virtuelles Azure offrent diverses options de stockage, avec tout autant de compromis différents sur les coûts, les performances, la disponibilité et la restauration qui méritent d’être soigneusement pris en compte.

Notez que vous devez stocker les données Elasticsearch sur des disques différents de ceux utilisés par le logiciel du système d’exploitation. Cela vous aidera à réduire les conflits avec le système d’exploitation et à éviter que d’importants volumes d’E/S Elasticsearch ne fassent pas concurrence avec les fonctions du système d’exploitation du point de vue de l’utilisation des ressources d’E/S.

Les disques Azure sont soumis à des contraintes de performances. Si vous constatez qu’un cluster subit des pics périodiques d’activité, les requêtes d’E/S risquent d’être limitées. Pour résoudre ce problème, paramétrez votre conception de manière à équilibrer la taille du document dans Elasticsearch compte tenu du volume de demandes que chaque disque est susceptible de recevoir.

Les disques basés sur le stockage standard prennent en charge un taux de demande maximal de 500 E/S par seconde, tandis que les disques basés sur Premium Storage peuvent fonctionner à 5 000 E/S par seconde (le stockage standard utilise un support « rotatif » tandis que Premium Storage s’appuie sur des disques SSD caractérisés par une latence plus faible et un débit plus élevé). Les disques Premium Storage sont uniquement disponibles pour les machines virtuelles de séries DS et GS. Les taux d’E/S de disque maximum pour les machines virtuelles Azure [sont détaillés en ligne](virtual-machines-size-specs/).

> [AZURE.NOTE] Selon la quantité de données retournées par les requêtes, vous n’obtiendrez peut-être pas le taux d’E/S par seconde maximum spécifié pour un disque, car chaque machine virtuelle est également limitée à une bande passante maximale de disque qui dépend de la taille de la machine virtuelle. Par exemple, un disque de données sur une machine virtuelle Standard\_GS5 peut fonctionner à 5 000 E/S par seconde et par disque, mais uniquement si la bande passante de transfert total de données ne dépasse pas 2 000 Mo/s sur l’ensemble des disques connectés à la machine virtuelle.

**Disques de données persistants**

Les disques de données persistants sont des disques durs virtuels couplés à Azure Storage. Si la machine virtuelle doit être recréée à la suite d’une défaillance majeure, les disques durs virtuels existants peuvent être facilement connectés à la nouvelle machine virtuelle. Les disques durs virtuels peuvent être créés avec un stockage standard (support rotatifs) ou premium (SSD). Si vous souhaitez utiliser des disques SSD, vous devez créer des machines virtuelles de série DS minimum. Les machines virtuelles DS sont facturées au même prix que les machines virtuelles équivalentes de la série D, mais sont assorties de frais supplémentaires pour l’utilisation de Premium Storage.

Dans les cas où le taux de transfert maximal par disque est insuffisant pour supporter la charge de travail attendue, vous pouvez soit créer plusieurs disques de données et autoriser Elasticsearch à [répartir les données sur ces disques](#disk-and-file-system-requirements), soit mettre en œuvre un [entrelacement RAID 0 à l’aide de disques virtuels](virtual-machines-linux-configure-raid/) au niveau du système.

> [AZURE.NOTE] L’expérience de Microsoft a montré que l’utilisation de RAID 0 est particulièrement utile pour atténuer les effets d’E/S liés aux charges de travail *irrégulières* qui génèrent de fréquents pics d’activité.

Utilisez des réplicas redondants en local (ou premium) pour le compte de stockage contenant les disques ; il n’est pas nécessaire d’effectuer une réplication entre les régions et les zones pour garantir la haute disponibilité du cluster Elasticsearch.

**Disques éphémères**

L’utilisation de disques persistants basées sur des disques SSD suppose de créer des machines virtuelles qui prennent en charge Premium Storage. Cette exigence présente certaines implications en termes de prix. L’utilisation du disque éphémère local pour contenir les données Elasticsearch peut être une solution économique pour les nœuds de tailles modérée qui nécessitent un maximum de 800 Go de stockage environ. Sur les machines virtuelles de série Standard-D, les disques éphémères sont implémentés à l’aide de disques SSD qui délivrent des performances nettement supérieures et une latence bien plus faible que les disques ordinaires. Lorsque vous utilisez Elasticsearch, les performances peuvent être équivalentes à celles obtenues avec l’utilisation de Premium Storage, sans les coûts associés. Pour plus d’informations, consultez la section [Résolution des problèmes de latence de disque](#addressing-disk-latency-issues).

La taille de la machine virtuelle limite la quantité d’espace disponible dans le stockage éphémère, comme l’explique le document [D-Series Performance Expectations](https://azure.microsoft.com/blog/d-series-performance-expectations/) (Performances attendues avec la série D). Par exemple, une machine virtuelle Standard\_D1 offre 50 Go de stockage éphémère, une machine virtuelle Standard\_D2 100 Go de stockage éphémère et une machine virtuelle Standard\_D14 800 Go d’espace éphémère. Pour les clusters dans lesquels les nœuds n’ont besoin que de cette quantité d’espace, l’utilisation d’une machine virtuelle de série D avec un stockage éphémère peut s’avérer rentable : à la date rédaction du présent article, le coût estimé d’une machine virtuelle D4 sous Linux s’élève à 458 $par mois. À titre de comparaison, une machine virtuelle DS4 équivalente, également exécutée sous Linux mais avec un seul disque SSD P30 de 1 024 Go coûte 645 $ par mois (509 $ pour la machine virtuelle et 136 $ pour le disque SSD). Pour stocker 1 024 Go de données dans un stockage éphémère, 3 machines virtuelles D4 sont nécessaires, ce qui représente un coût mensuel de 1 374 $. Vos calculs ne doivent cependant pas être uniquement fondés sur la capacité de stockage. Une seule machine DS4 offre 8 cœurs de processeur et 28 Go de mémoire, tandis que 3 machines virtuelles D4 fournissent 24 cœurs de processeur et 84 Go de mémoire. Si vos charges de travail sollicitent fortement les ressources processeur, une répartition de la charge sur les 3 machines virtuelles peut produire de meilleures performances qu’une exécution sur une seule machine virtuelle. En outre, l’utilisation d’une seule machine virtuelle (ou d’un nombre limité de machines virtuelles) peut affecter la résilience et la capacité de restauration du cluster.

> [AZURE.NOTE] Les chiffres indiqués ci-dessus sont donnés uniquement à titre indicatif. Ces prix peuvent avoir évolué au moment où vous lisez ce document. Pour connaître les tarifs actuels, consultez la page [Calculatrice de prix](https://azure.microsoft.com/pricing/calculator/).

Vous devez mettre dans la balance l’augmentation du débit disponible grâce au stockage avec le temps et les coûts liés à la récupération de ces données suite au redémarrage de la machine virtuelle. Le contenu du disque éphémère sera perdu si la machine virtuelle est déplacée vers un autre serveur hôte, si l’hôte est mis à jour ou encore si l’hôte subit une défaillance matérielle. Si les données elles-mêmes ont une durée de vie limitée, cette perte de données peut être acceptable. Pour les données de plus longue durée, il peut être possible de reconstruire un index ou de récupérer les informations manquantes à partir d’une sauvegarde. Il est possible de réduire le risque de perte en utilisant les réplicas conservés sur les autres machines virtuelles.

> [AZURE.NOTE] N’utilisez pas **une même** machine virtuelle pour stocker les données de production critiques. En cas de défaillance du nœud, toutes les données seront indisponibles. Pour les informations critiques, veillez à ce que les données soient répliquées sur au moins un autre nœud.

**Azure Files**

[Azure File Service](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx) offre un accès aux fichiers partagés via Azure Storage. Vous pouvez créer des partages de fichiers que vous pouvez ensuite monter sur des machines virtuelles Azure. Plusieurs machines virtuelles peuvent monter le même partage de fichiers pour pouvoir accéder aux mêmes données.

Pour des raisons de performances, il est déconseillé d’utiliser des partages de fichiers pour le stockage des données Elasticsearch qu’il n’est pas nécessaire de partager entre plusieurs nœuds. Les disques de données standard sont plus adaptés dans ce cas. Les partages de fichiers peuvent être utilisés pour la création d’[index de réplicas secondaires](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-shadow-replicas.html) Elasticsearch. Toutefois, cette fonctionnalité est actuellement au stade expérimental et ne doit donc pas être implémentée pour le moment dans un environnement de production. Pour cette raison, les index secondaires ne sont pas étudiés dans ce guide.

**Options réseau**

Azure implémente un schéma de mise en réseau partagée. Les machines virtuelles utilisant les mêmes racks matériels se disputent les ressources réseau. Par conséquent, la bande passante réseau disponible peut varier selon le moment de la journée et le cycle de travail journalier exécuté sur des machines virtuelles qui partagent la même infrastructure réseau physique. Vous ne disposez que de peu de moyens de contrôle sur ces facteurs. Il est important de comprendre que les performances du réseau sont susceptibles de varier au fil du temps ; vous devez donc les attentes des utilisateurs en conséquence.

## Considérations relatives à la montée en charge verticale des nœuds pour la prise en charge de l’ingestion de données à grande échelle

Vous pouvez créer des clusters Elasticsearch en utilisant des ressources matérielles relativement modestes, puis monter en charge selon une approche verticale ou horizontale à mesure que le volume de données et le nombre de demandes augmentent. Azure permet une mise à l’échelle verticale grâce à l’utilisation de machines virtuelles plus grandes et plus coûteuses, ou bien une montée en puissance horizontale basée sur l’utilisation de machines virtuelles supplémentaires, plus petites et économiques. Vous pouvez également combiner ces deux stratégies. Il n’existe pas de solution universelle pour tous les scénarios. Aussi, pour évaluer l’approche la mieux adaptée à toute situation donnée, vous devez être prêt à entreprendre une série de tests de performances.

Cette section concerne l’approche verticale ; l’approche horizontale est décrite dans la section [Considérations relatives à la montée en charge horizontale des clusters pour la prise en charge de l’ingestion de données à grande échelle](#scaling-out-clusters). Cette section décrit les résultats d’une série de tests qui ont été effectuées sur un ensemble de clusters Elasticsearch comprenant des machines virtuelles de différentes tailles. Les clusters ont été désignés selon leur taille : petite, moyenne ou grande. Le tableau suivant résume les ressources allouées aux machines virtuelles dans chaque cluster.

| Cluster | Référence de la machine virtuelle | Nombre de cœurs | Nombre de disques de données | RAM |
|---------|-------------|-----------------|----------------------|------|
| Petite | Standard D2 | 2 | 4 | 7 GO |
| Moyenne | Standard D3 | 4 | 8 | 14 GO |
| Grande | Standard D4 | 8 | 16 | 28 GO |

Chaque cluster Elasticsearch contenait 3 nœuds de données. Ces nœuds de données géraient les demandes des clients ainsi que le traitement des données ; nous n’avons pas utilisé de nœuds clients distincts car ils n’offraient que peu d’avantages pour le scénario d’ingestion de données utilisé dans le cadre des tests. Le cluster contenait également trois nœuds principaux, dont un a été choisi par Elasticsearch pour coordonner le cluster.

Les tests ont été effectuées à l’aide d’ElasticSearch 1.7.3. Les tests ont été initialement effectués sur des clusters exécutés sous Ubuntu Linux 14.0.4, puis reproduits sur Windows Server 2012. Les détails de la charge de travail exécutée par les tests sont décrits dans l’[Annexe](#appendix-the-bulk-load-data-ingestion-performance-test).

> [AZURE.IMPORTANT] Comme décrit dans la section Options réseau, les statistiques de performance des services distribués exécutés dans un environnement de cloud sont largement tributaires de la bande passante réseau disponible pour la transmission et la réception physiques des données entre ces services. Lorsque vous générez et déployez un système tel qu’un cluster Elasticsearch, vous pouvez largement contrôler le processeur, la mémoire et les ressources disque disponibles, en sélectionnant simplement la taille et la référence de la machine virtuelle. Vous avez en revanche beaucoup moins de contrôle sur les ressources réseau disponibles car celles-ci sont partagées par les machines virtuelles situées physiquement dans le même rack matériel, ainsi que par le volume de trafic entrant et sortant du centre de données. Il est donc important, lors de l’exécution des tests de performances comparatifs, d’utiliser le même centre de données approximativement au même moment de la journée pendant la semaine ouvrée. Les tests exécutés sur des machines virtuelles hébergées dans différents centres de données ou exécutées à des moments différents risquent de produire des résultats très différents.

### Performances d’ingestion de données – Ubuntu Linux 14.0.4

Le tableau suivant récapitule les résultats de l’exécution de tests de deux heures pour chaque configuration :

| Configuration | Nombre d’exemples | Temps de réponse moyen (en ms) | Débit (opérations/sec) |
|---------------|-----------|----------------------------|---------------------------|
| Petite | 67 057 | 636 | 9,3 |
| Moyenne | 123 482 | 692 | 17,2 |
| Grande | 197 085 | 839 | 27,4 |

Le débit et le nombre d’échantillons traités pour les trois configurations se situent approximativement dans un rapport de 1:2:3. Toutefois, les ressources disponibles en termes de mémoire, de cœurs de processeur et de disques ont un rapport de 1:2:4. Nous avons trouvé judicieux d’examiner les détails de faibles performances des nœuds du cluster afin d’en évaluer la raison potentielle. Ces informations peuvent aider à déterminer s’il existe des limites à l’évolution verticale et à quel moment il peut être préférable de considérer une montée en charge horizontale.

### Détermination des facteurs limitants : utilisation du réseau

Elasticsearch doit pouvoir disposer d’une bande passante réseau suffisante pour prendre en charge le flux des demandes clients, ainsi que les informations de synchronisation acheminées entre les nœuds du cluster. Comme nous l’avons indiqué précédemment, vous ne disposez que d’un contrôle limité sur la disponibilité de la bande passante, laquelle dépend de différentes variables, comme le centre de données utilisé ou encore la charge réseau actuelle des autres machines virtuelles partageant la même infrastructure réseau. Toutefois, il est toujours intéressant d’examiner l’activité réseau pour chaque cluster, même pour vérifier que le volume de trafic n’est pas excessif. Le graphique ci-dessous présente une comparaison du trafic réseau entrant par le nœud 2 dans chacun des clusters (le volume des autres nœuds dans chaque cluster était très similaire).

![](media/guidance-elasticsearch-data-ingestion-image1.png)

Pour chaque configuration de cluster, la quantité moyenne d’octets reçus par seconde par le nœud 2 sur une période de deux heures était comme suit :

| Configuration | Nombre moyen d’octets reçus/seconde |
|---------------|--------------------------------------|
| Petite | 3 993 640,346 |
| Moyenne | 7 311 689,897 |
| Grande | 11 893 874,2 |

> [AZURE.NOTE] Les tests ont été réalisés pendant que le système était en cours d’exécution à l’état stationnaire. En cas de rééquilibrage d’index, ou de récupération de nœud, les transmissions de données entre des nœuds contenant des réplicas de partition ou des partitions principales peuvent générer un trafic réseau significatif. Les effets de ce processus sont davantage décrits dans le document « Configuring, Testing, and Analyzing Elasticsearch Resilience and Recovery » (Configuration, test et analyse de résilience et de récupération Elasticsearch).

### Déterminer les facteurs limitants : taux d’utilisation du processeur

La vitesse à laquelle les demandes sont traitées est, au moins partiellement, régie par la capacité de traitement disponible. Elasticsearch accepte les demandes d’insertion en bloc dans la file d’attente pour l’insertion en bloc. Chaque nœud possède un ensemble de files d’attente pour des insertions en bloc, déterminées par le nombre de processeurs disponibles. Par défaut, à chaque processeur correspond une file d’attente, et chaque file d’attente peut contenir jusqu’à 50 demandes non traitées, après quoi les demandes seront rejetées. Les applications doivent envoyer des demandes à un rythme qui ne provoque pas un « débordement » des files d’attente. À tout moment, le nombre d’éléments dans chaque file d’attente est fonction de la vitesse à laquelle les demandes sont envoyées par les applications clientes et la fréquence à laquelle ces mêmes demandes sont récupérées et traitées par Elasticsearch. Pour cette raison, une statistique importante résume les préoccupations sur le taux d’erreur présentées dans le tableau suivant.

| Configuration | Nombre total d’exemples | Nombre d’erreurs | Taux d’erreur |
|---------------|---------------|-----------|------------|
| Petite | 67 057 | 0 | 0,00 % |
| Moyenne | 123 483 | 1 | 0,0008 % |
| Grande | 200 702 | 3 617 | 1,8 % |

Chacune de ces erreurs a été provoquée par l’exception Java suivante :

```
org.elasticsearch.action.support.replication.TransportShardReplicationOperationAction$PrimaryPhase$1@75a30c1b]; ]
[219]: index [systembase], type [logs], id [AVEAioKb2TRSNcPa_8YG], message [RemoteTransportException[[esdatavm2][inet[/10.0.1.5:9300]][indices:data/write/bulk[s]]]; nested: EsRejectedExecutionException[rejected execution (queue capacity 50)
```

Augmenter le nombre de files d’attente et/ou la longueur de chaque file d’attente pourrait réduire le nombre d’erreurs, mais cette approche peut uniquement gérer les rafales de courte durée. Cette opération, effectuée pendant l’exécution soutenue d’une série de tâches d’ingestion de données, retardera simplement le moment auquel les erreurs commencent à se produire. En outre, cette modification n’améliore pas le débit et nuira probablement au temps de réponse des applications clientes, car les demandes seront mises en file d’attente pendant plus longtemps avant d’être traitées.

L’ensemble des graphiques suivants illustre le taux d’utilisation du processeur pour le nœud le plus occupé de chaque cluster.

> [AZURE.NOTE] La structure d’index par défaut de 5 partitions avec 1 réplica (10 partitions en tout), déséquilibre légèrement la charge entre les nœuds dans un cluster ; deux nœuds contiennent 3 partitions tandis que l’autre nœud en contient 4. Le nœud le plus occupé est probablement l’élément qui limite le débit le plus ; c’est pour cette raison que ce nœud est sélectionné dans chaque cas :

![](media/guidance-elasticsearch-data-ingestion-image2.png)

![](media/guidance-elasticsearch-data-ingestion-image3.png)

![](media/guidance-elasticsearch-data-ingestion-image4.png)

Pour les petits, moyens et grands clusters, le taux d’utilisation moyen du processeur pour ces nœuds était respectivement de 75,01 %, 64,93 % et 64,64 %. Le taux d’utilisation atteint rarement les 100 %, et ce dernier décroît au fur et à mesure que la taille des nœuds et la puissance disponible du processeur augmentent. Il est donc peu probable que la puissance du processeur constitue un facteur limitant les performances du grand cluster.

### Déterminer les facteurs limitants : la mémoire

L’utilisation de la mémoire est un autre aspect important qui peut avoir un impact sur les performances. Lors des tests, 50 % de la mémoire ont été alloués à Elasticsearch, conformément aux [recommandations documentées](https://www.elastic.co/guide/en/elasticsearch/guide/current/heap-sizing.html#_give_half_your_memory_to_lucene). Pendant que les tests étaient exécutés, la JVM a été analysée à la recherche de nettoyage excessif de la mémoire (ce qui indiquerait une absence de mémoire de tas). Dans tous les cas, la taille du tas est restée stable et la JVM affichait une faible activité de nettoyage de la mémoire. La figure 1 montre une capture instantanée de Marvel, soulignant les statistiques JVM clés sur une courte période, tandis que le test était exécuté sur le grand cluster.

![](media/guidance-elasticsearch-data-ingestion-image5.png)

***Figure 1. Mémoire JVM et activité de nettoyage de la mémoire sur le grand cluster.***

### Déterminer les facteurs limitants : vitesse d’entrée/sortie du disque
La dernière fonction physique restante côté serveur pouvant limiter les performances est la performance du sous-système d’entrée/sortie du disque. Le graphique ci-dessous compare l’activité du disque en termes d’octets écrits pour les nœuds les plus occupés dans chaque cluster.

![](media/guidance-elasticsearch-data-ingestion-image6.png)

Le tableau suivant présente le nombre moyen d’octets écrits par seconde pour le nœud 2, dans chaque configuration de cluster, sur une période de deux heures.

| Configuration | Nombre moyen d’octets écrits par seconde |
|---------------|-------------------------------------|
| Petite | 25 502 361,94 |
| Moyenne | 48 856 124,5 |
| Grande | 88 137 675,46 |

Le volume de données écrites augmente parallèlement au nombre de demandes traitées par un cluster, mais les taux d’entrée/sortie sont dans les limites d’Azure Storage (les disques créés à l’aide d’Azure Storage peuvent prendre en charge un rythme soutenu de dizaines, voire de centaines de Mo/s, selon que le stockage Standard ou Premium est utilisé). L’analyse du temps passé à attendre les entrées/sorties du disque explique pourquoi le débit du disque est bien inférieur au maximum théorique. Les graphiques et le tableau ci-dessous affichent ces statistiques pour les trois mêmes nœuds :

> [AZURE.NOTE] Le délai d’attente du disque est mesuré en surveillant le pourcentage de temps processeur au cours duquel les processeurs sont bloqués en attendant la fin des opérations d’entrée/sortie.

![](media/guidance-elasticsearch-data-ingestion-image7.png)

![](media/guidance-elasticsearch-data-ingestion-image8.png)

![](media/guidance-elasticsearch-data-ingestion-image9.png)

| Configuration | Délai d’attente moyen du disque (en % de temps processeur) |
|---------------|--------------------------------|
| Petite | 21,04 |
| Moyenne | 14,48 |
| Grande | 15,84 |

Ces données indiquent qu’une part importante de temps processeur (entre environ 16 % et 21 %) est passée à attendre que les entrées/sorties du disque se terminent. Cela limite les capacités d’Elasticsearch en matière de traitement de demandes et de stockage de données.

> [AZURE.NOTE]  Pendant la série de tests, le grand cluster a intégré un surplus de cinq cents millions de documents. L’exécution continue du test a permis de découvrir que les délais d’attente augmentaient considérablement lorsque la base de données contenait plus de six cents millions de documents. Les raisons de ce comportement n’ont pas fait l’objet d’examens poussés, mais peuvent être dues à une latence de disque accrue suite à sa défragmentation. Le fait d’étendre la taille du cluster sur plusieurs nœuds peut permettre d’atténuer les effets de ce comportement. Dans les cas les plus extrêmes, il peut être nécessaire de défragmenter un disque affichant des temps d’entrée/sortie excessifs. Toutefois, la défragmentation d’un disque volumineux peut prendre un certain temps (voire plus de 48 heures pour un lecteur VHD de 2 To). Le reformatage du lecteur, suite auquel Elasticsearch récupère les données manquantes provenant de partitions de réplicas, peut être une approche plus rentable.

### Résoudre des problèmes de latence de disque

Les tests ont initialement été effectués à l’aide de machines virtuelles configurées avec des disques standard. Un disque standard est à base de support tournant et peut donc subir des latences de rotation et autres goulots d’étranglement pouvant affaiblir la vitesse d’entrée/sortie. Azure fournit également un stockage premium, dans lequel des disques sont créés à l’aide de périphériques SSD. Ces périphériques n’ont aucune latence de rotation et devraient donc améliorer les vitesses d’entrée/sortie. Le tableau ci-dessous compare les résultats du remplacement de disques standard par des disques premium dans le grand cluster. (Les machines virtuelles D4 standard dans le grand cluster ont été remplacées par des machines virtuelles DS4 standard. Le nombre de cœurs, la mémoire et les disques étaient identiques dans les deux cas, la seule différence étant que les machines virtuelles DS4 utilisaient des SSD).

| Configuration | Nombre d’exemples | Temps de réponse moyen (en ms) | Débit (opérations/sec) |
|------------------|-----------|----------------------------|---------------------------|
| Grand - Standard | 197 085 | 839 | 27,4 |
| Grand - Premium | 255 985 | 581 | 35,6 |

Les temps de réponse étaient nettement meilleurs, ce qui entraîne un débit moyen quatre fois supérieur à celui du petit cluster. Cela est plus aligné avec les ressources disponibles sur une machine virtuelle DS4 standard. Le taux d’utilisation moyen du processeur sur le nœud du cluster le plus occupé (ici, le nœud 1) a augmenté, car le processeur a passé moins de temps à attendre que les entrées/sorties se terminent :

![](media/guidance-elasticsearch-data-ingestion-image10.png)

La réduction du délai d’attente du disque devient évidente lorsque vous étudiez le graphique suivant, qui indique que le délai d’attente a baissé d’environ 1 % en moyenne pour le nœud le plus occupé :

![](media/guidance-elasticsearch-data-ingestion-image11.png)

Il y a cependant un prix à payer pour cette amélioration. Le nombre d’erreurs d’intégration a augmenté, d’un facteur de 10, pour atteindre 35 797 (12,3 %). Là encore, la plupart de ces erreurs sont dues au débordement des files d’attente pour l’insertion en bloc. Étant donné que le matériel est presque à la limite de sa capacité, il peut être nécessaire d’ajouter plus de nœuds ou de limiter la fréquence d’insertions en bloc afin de réduire le nombre d’erreurs. Ces problèmes sont abordés plus loin dans ce document.

### Tests avec stockage éphémère

Les mêmes tests ont été répétés sur un cluster de machines virtuelles D4 à l’aide du stockage éphémère. Sur les machines virtuelles D4, le stockage éphémère est implémenté comme un SSD de 400 Go unique. Le nombre d’exemples traités, le temps de réponse, et le débit étaient tous très semblables aux statistiques rapportées pour le cluster sur machines virtuelles DS14 avec stockage premium.

| Configuration | Nombre d’exemples | Temps de réponse moyen (en ms) | Débit (opérations/sec) |
|-----------------------------------|-----------|----------------------------|---------------------------|
| Grand - Premium | 255 985 | 581 | 35,6 |
| Grand – Standard (disque éphémère) | 255 626 | 585 | 35,5 |

Le taux d’erreur était également similaire (33 862 échecs sur 289 488 demandes au total : 11,7 %).

Les graphiques suivants montrent les statistiques de taux d’utilisation du processeur et de délai d’attente pour le nœud le plus occupé dans le cluster (ici, le nœud 2) :

![](media/guidance-elasticsearch-data-ingestion-image12.png)

![](media/guidance-elasticsearch-data-ingestion-image13.png)

Dans ce cas, en termes de performances, l’utilisation du stockage éphémère peut être considérée comme une solution plus rentable que l’utilisation du stockage premium.

### Performances d’ingestion de données – Windows Server 2012

Les mêmes tests ont été répétés à l’aide d’un ensemble de clusters Elasticsearch avec des nœuds exécutant Windows Server 2012. Ces tests avaient pour objectif de déterminer les effets que le choix du système d’exploitation peut avoir sur les performances du cluster, le cas échéant.

Pour illustrer l’évolutivité d’Elasticsearch sous Windows, le tableau suivant présente le débit et le temps de réponse obtenus pour les configurations du petit, moyen et grand cluster. Notez que ces tests ont été réalisés avec Elasticsearch configuré pour utiliser un stockage SSD éphémère, car les tests sous Ubuntu avaient démontré que la latence de disque constituait un facteur critique pour atteindre des performances maximales :

| Configuration | Nombre d’exemples | Temps de réponse moyen (en ms) | Débit (opérations/sec) |
|---------------|-----------|----------------------------|---------------------------|
| Petite | 90 295 | 476 | 12,5 |
| Moyenne | 169 243 | 508 | 23,5 |
| Grande | 257 115 | 613 | 35,6 |

Ces résultats indiquent comment Elasticsearch s’adapte à la taille des machines virtuelles et aux ressources disponibles sous Windows.

Les tableaux ci-dessous comparent les résultats pour le grand cluster sous Ubuntu et Windows :

| Système d’exploitation | Nombre d’exemples | Temps de réponse moyen (en ms) | Débit (opérations/sec) | Taux d’erreur (en %) |
|------------------|-----------|----------------------------|---------------------------|----------------|
| Ubuntu | 255 626 | 585 | 35,5 | 11,7 |
| Windows | 257 115 | 613 | 35,6 | 7,2 |

Le débit est conforme à celui des grands clusters sous Ubuntu, bien que le temps de réponse soit légèrement plus long. Cela peut être dû au taux d’erreur inférieur (les erreurs sont signalées plus rapidement que les opérations réussies, et ont donc un temps de réponse inférieur).

Le taux d’utilisation du processeur rapporté par les outils de surveillance Windows était légèrement supérieur à celui d’Ubuntu. Toutefois, ce genre de comparaisons directes de mesure entre systèmes d’exploitation est à prendre avec des pincettes, en raison de la différence dans leur manière de rapporter ces statistiques. En outre, les informations sur la latence de disque en termes de temps processeur passé à attendre les entrées/sorties ne sont pas disponibles de la même manière que pour Ubuntu. Le point important est que le taux d’utilisation du processeur était élevé, ce qui indique que le temps passé à attendre les entrées/sorties était faible :

![](media/guidance-elasticsearch-data-ingestion-image14.png)

### La montée en charge verticale : conclusions

Les performances d’Elasticsearch pour un cluster bien réglé seront très probablement équivalentes sous Windows et Ubuntu, et évoluent selon le même rythme sur les deux systèmes d’exploitation. Pour des performances optimales, utilisez des SSD pour accueillir les données de Elasticsearch.

## <a name="scaling-out-clusters"></a> Considérations relatives à la montée en charge horizontale des clusters pour la prise en charge de l’ingestion de données à grande échelle

La montée en charge horizontale complète la montée en charge verticale vue dans la section précédente. Une fonctionnalité importante d’Elasticsearch est son évolutivité horizontale inhérente, intégrée au logiciel. L’augmentation de la taille d’un cluster est une simple question d’ajout de nœuds. Vous n’avez pas besoin d’effectuer des opérations manuelles pour redistribuer des index ou des partitions, car ces tâches sont gérées automatiquement, bien qu’il existe de nombreuses options de configuration que vous pouvez utiliser pour influencer ce processus. L’ajout de plusieurs nœuds permet d’améliorer les performances en répartissant la charge sur plusieurs machines. Lorsque vous ajoutez plusieurs nœuds, vous devrez peut-être réindexer des données pour augmenter le nombre de partitions disponibles. Vous pouvez devancer ce processus, dans une certaine mesure, par la création d’index contenant plus de partitions que de nœuds disponibles. Lorsque d’autres nœuds sont ajoutés, les partitions peuvent être distribuées.

En plus de tirer parti de l’évolutivité horizontale d’Elasticsearch, d’autres raisons existent pour l’implémentation d’index contenant plus de partitions que de nœuds. Chaque partition est implémentée en tant que structure de données distincte (un index de [Lucene](https://lucene.apache.org/)), et dispose de son propre mécanisme interne pour le maintien de la cohérence et la gestion de la concurrence. La création de plusieurs partitions permet d’accroître le parallélisme dans un nœud, mais aussi d’améliorer les performances. Cependant, le maintien des performances pendant la montée en charge est un véritable acte d’équilibriste. Plus un cluster contient de nœuds et de partitions, plus l’effort est grand pour synchroniser le travail effectué par le cluster, ce qui peut réduire le débit. Pour toute charge de travail donnée, il existe un « point optimal » qui maximise la performance d’ingestion de données tout en réduisant les frais de maintenance. Ce point optimal dépend fortement de la nature de la charge de travail et du cluster ; plus précisément, le volume, la taille et le contenu des documents, la vitesse à laquelle se fait l’ingestion, et le matériel sur lequel s’exécute le système.

Cette section résume les résultats des enquêtes portant sur le redimensionnement des clusters, destinés à prendre en charge la charge de travail utilisée lors des tests de performance décrits précédemment. Le même test a été effectué sur des clusters avec des machines virtuelles de grande taille (D4 standard avec 8 cœurs de processeur, 16 disques de données et 28 Go de RAM) exécutés sous Ubuntu Linux 14.0.4, mais contenant un nombre de nœuds et de partitions différent. Les résultats ne sont pas définitifs car ils s’appliquent uniquement à un scénario spécifique, mais ils peuvent faire office de point de départ pour vous aider à analyser l’évolutivité horizontale de vos clusters, et vous aider à calculer le rapport optimal partitions/nœuds qui répond le mieux à vos besoins.

### Résultats de la ligne de base : 3 nœuds

Afin d’obtenir une ligne de base, le test de performance de l’ingestion des données a été exécuté sur un cluster à 3 nœuds contenant 5 partitions et 1 réplica. C’est la configuration par défaut pour un index Elasticsearch. Dans cette configuration, Elasticsearch distribue 2 partitions principales à 2 nœuds, et la partition principale restante est stockée sur le troisième nœud. Le tableau ci-dessous présente le débit en termes d’opérations d’ingestion en bloc par seconde, et le nombre de documents stockés avec succès par le test.

> [AZURE.NOTE] Dans les tableaux qui suivent dans cette section, la distribution des partitions principales est représentée par un nombre pour chaque nœud, séparé par des tirets. Par exemple, la disposition 5 partitions-3 nœuds est ainsi représentée : 2-2-1. La disposition des partitions de réplica n’est pas incluse ;.celles-ci auront une structure semblable aux partitions principales.

| Configuration | Nombre de documents | Débit (opérations/sec) | Disposition de partition |
|---------------|--------------|-----------------------------|--------------|
| 5 partitions | 200 560 412 | 27,86 | 2-2-1 |

### Résultats pour un cluster à 6 nœuds

Le test a été répété sur un cluster à 6 nœuds. L’objectif de ces tests était d’essayer de déterminer plus précisément les effets du stockage de plusieurs partitions sur un nœud.

| Configuration | Nombre de documents | Débit (opérations/sec) | Disposition de partition |
|---------------|--------------|-----------------------------|--------------|
| 4 partitions | 227 360 412 | 31,58 | 1-1-0-1-1-0 |
| 7 partitions | 268 013 252 | 37,22 | 2-1-1-1-1-1 |
| 10 partitions | 258 065 854 | 35,84 | 1-2-2-2-1-2 |
| 11 partitions | 279 788 157 | 38,86 | 2-2-2-1-2-2 |
| 12 partitions | 257 628 504 | 35,78 | 2-2-2-2-2-2 |
| 13 partitions | 300 126 822 | 41,68 | 2-2-2-2-2-3 |

Ces résultats font apparaître les tendances suivantes :

* Plus un nœud contient de partitions, meilleur sera le débit. Avec le nombre réduit de partitions créées par nœud pour ces tests, ce phénomène était attendu, pour les raisons décrites précédemment.

* Un nombre impair de partitions aboutit à de meilleures performances qu’un nombre pair. Les raisons en sont moins claires, mais il est *possible* que l’algorithme de routage qu’utilise Elasticsearch parvienne ainsi à distribuer les données de façon plus équitable entre les partitions, ce qui conduit à une charge plus équilibrée par nœud.

Pour tester ces hypothèses, plusieurs autres tests ont été réalisés avec un plus grand nombre de partitions. Sur les conseils de l’équipe Elasticsearch, on a décidé d’utiliser un nombre premier de partitions pour chaque test, car ceux-ci permettent une distribution raisonnable de nombres impairs pour la plage en question.

| Configuration | Nombre de documents | Débit (opérations/sec) | Disposition de partition |
|---------------|--------------|-----------------------------|-------------------|
| 23 partitions | 312 844 185 | 43,45 | 4-4-4-3-4-4 |
| 31 partitions | 309 930 777 | 43,05 | 5-5-5-5-6-5 |
| 43 partitions | 316 357 076 | 43,94 | 8-7-7-7-7-7 |
| 61 partitions | 305 072 556 | 42,37 | 10-11-10-10-10-10 |
| 91 partitions | 291 073 519 | 40,43 | 15-15-16-15-15-15 |
| 119 partitions | 273 596 325 | 38,00 | 20-20-20-20-20-19 |

Ces résultats suggèrent un point de basculement à environ 23 partitions. Ce nombre passé, l’augmentation du nombre de partitions a provoqué une légère baisse des performances (le débit pour 43 partitions est probablement une anomalie).

### Résultats pour un cluster à 9 nœuds

Les tests ont été répétés pour un cluster à 9 nœuds, là encore en utilisant un nombre premier de partitions.

| Configuration | Nombre de documents | Débit (opérations/sec) | Disposition de partition |
|---------------|--------------|-----------------------------|----------------------------|
| 17 partitions | 325 165 364 | 45,16 | 2-2-2-2-2-2-2-2-1 |
| 19 partitions | 331 272 619 | 46,01 | 2-2-2-2-2-2-2-2-3 |
| 29 partitions | 349 682 551 | 48,57 | 3-3-3-4-3-3-3-4-3 |
| 37 partitions | 352 764 546 | 49,00 | 4-4-4-4-4-4-4-4-5 |
| 47 partitions | 343 684 074 | 47,73 | 5-5-5-6-5-5-5-6-5 |
| 89 partitions | 336 248 667 | 46,70 | 10-10-10-10-10-10-10-10-9 |
| 181 partitions | 297 919 131 | 41,38 | 20-20-20-20-20-20-20-20-21 |

Ces résultats ont permis de constater un modèle semblable, avec un point de basculement autour des 37 partitions.

### Montée en charge horizontale : conclusions

Une extrapolation brute permet de noter que les résultats des tests de clusters à 6 et 9 nœuds indiquent, dans ce cas précis, que le nombre idéal de partitions pour optimiser les performances était 4n+/-1, où « n » est le nombre de nœuds. Cela est *peut-être* fonction du nombre de threads d’insertion en bloc disponibles, qui à son tour dépend du nombre de cœurs de processeur, le raisonnement étant comme suit (voir [Multidocument Patterns](https://www.elastic.co/guide/en/elasticsearch/guide/current/distrib-multi-doc.html#distrib-multi-doc) (Modèles au niveau multi-documents)pour plus d’informations) :

* Chaque demande d’insertion en bloc envoyée par l’application cliente est reçue par un nœud de données unique.

* Le nœud de données génère une nouvelle demande d’insertion en bloc pour chaque partition principale concernée par la demande d’origine, et les transfère en même temps vers les autres nœuds.

* Lorsqu’une partition principale est écrite, une nouvelle demande est envoyée à chaque réplica pour cette partition. La partition principale attend que la demande envoyée au réplica se termine, puis termine à son tour.

Par défaut, Elasticsearch crée un thread d’insertion en bloc pour chaque cœur de processeur disponible dans une machine virtuelle. Dans le cas des machines virtuelles D4 utilisées par ce test, chaque processeur contenait 8 cœurs ; 8 threads d’insertion en bloc ont donc été créés. L’index utilisé s’étendait sur 4 (dans un cas, 5) partitions principales sur chaque nœud, et chaque nœud contenait également 4 (5) réplicas. L’insertion de données dans ces partitions et réplicas pouvait consommer jusqu’à 8 threads sur chaque nœud par demande, ce qui correspondait au nombre de threads disponibles. Augmenter ou réduire le nombre de partitions pourrait nuire à l’efficacité des threads car ceux-ci peuvent se retrouver inoccupés, ou les demandes peuvent être mises en file d’attente. Toutefois, sans l’appui de tests supplémentaires, cela reste une théorie non définitive.

Les tests illustrent également un autre point important. Dans ce cas, l’augmentation du nombre de nœuds peut améliorer le débit d’ingestion de données, mais les résultats n’évoluent pas forcément de manière linéaire. Le fait d’effectuer des tests supplémentaires sur des clusters à 12 et 15 nœuds pourrait aider à discerner à quel point la montée en charge horizontale devient bénéfique. Si ce nombre de nœuds ne permet pas d’avoir un espace de stockage suffisant, il peut être nécessaire de revenir à la stratégie de montée en charge verticale, et commencer à utiliser plus de disques, voire des disques plus volumineux, basés sur le stockage premium.

> [AZURE.IMPORTANT] Le nombre 4n+/-1 ne doit pas être considéré comme une formule magique qui fonctionnera pour chaque cluster. Si vous disposez de plus ou moins de cœurs de processeur, la configuration optimale de partitions peut varier. Les résultats étaient basés sur une charge de travail spécifique, qui avait pour seule fonction l’ingestion des données. Pour les charges de travail comprenant une combinaison de demandes et d’agrégations, les résultats pouvaient varier grandement.

> En outre, la charge de travail d’ingestion de données utilisait un index unique. Dans de nombreux cas, les données sont susceptibles d’être réparties sur plusieurs index conduisant à des modèles ou une utilisation des ressources différents.

> Le point essentiel de cet exercice est de comprendre la méthode utilisée plutôt que les résultats obtenus. Vous devez être prêt à effectuer votre propre évaluation d’évolutivité en fonction de vos charges de travail pour obtenir des informations qui s’appliquent le mieux à votre cas.

<span id="_Considerations_for_Tuning" class="anchor"></span>
## Considérations relatives à l’optimisation de l’ingestion des données à grande échelle

Elasticsearch est hautement configurable, avec de nombreux commutateurs et paramètres servant à optimiser les performances pour les utilisations et scénarios spécifiques. Cette section en décrit des exemples courants. N’oubliez pas que la flexibilité que procure Elasticsearch à cet égard n’est pas donnée. Il est très facile de dérégler Elasticsearch et ainsi aggraver les performances. Lorsque vous réglez les paramètres, effectuez une seule modification à la fois, et mesurez toujours les effets produits par chaque modification pour vous assurer qu’ils ne nuisent pas à votre système.

### Optimisation des ressources pour les opérations d’indexation

La liste suivante décrit quelques points que vous devez prendre en compte lors du paramètre d’un cluster Elasticsearch pour prendre en charge l’ingestion de données à grande échelle. Les deux premiers éléments sont plus susceptibles d’avoir une incidence perceptible sur la performance, alors que le reste est plus marginal, selon la charge de travail :

*  Les nouveaux documents ajoutés à un index deviennent visibles lors des recherches que lorsque l’index est actualisé. L’actualisation d’un index est une opération coûteuse ; celle-ci est donc exécutée périodiquement, et non à chaque fois qu’un document est créé. L’intervalle d’actualisation par défaut est d’1 seconde. Si vous effectuez des opérations en bloc, pensez à désactiver temporairement l’actualisation des index ; définissez l’intervalle d’actualisation (*refresh\_interval*) sur -1.

	```http
	PUT /my_busy_index
	{
		"settings" : {
			"refresh_interval": -1
		}
	}
	```

	Déclenchez une actualisation manuellement à l’aide de l’API [*\_refresh*](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-refresh.html) à la fin de l’opération afin de rendre les données visibles. Pour en savoir plus, consultez la rubrique [Bulk Indexing Usage](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-update-settings.html#bulk) (Utilisation de l’indexation en bloc). [L’impact de la modification de l’intervalle d’actualisation sur l’ingestion de données](#the-impact-of-changing-the-index-refresh-interval-on-data-ingestion-performance) est décrit plus tard dans l’article.

* Si un index est répliqué, chaque opération d’indexation (création, mise à jour, ou suppression de document) est répétée sur les partitions de réplicas lorsqu’elle est effectuée dans la partition principale. Pensez à désactiver la réplication pendant les opérations d’importation en bloc, puis de la réactiver lorsque l’importation est terminée :

    ```http
	PUT /my_busy_index
	{
		"settings" : {
			"number_of_replicas": 0
		}
	}
	```

	Lorsque vous réactivez la réplication, Elasticsearch effectue un transfert réseau octet par octet de données à partir de l’index vers chaque réplica. C’est plus efficace que de répéter le processus d’indexation document par document sur chaque nœud. Il y a un risque de perte de données si le nœud principal échoue lors de l’importation en bloc. Cependant, il est probablement possible de les récupérer en relançant simplement l’importation. L’[impact de la réplication sur les performances d’ingestion de données](#the-impact-of-replicas-on-data-ingestion-performance) est décrit plus en détail ultérieurement.

* Elasticsearch tente d’équilibrer les ressources disponibles entre celles requises pour l’exécution d’une requête et celles nécessaires à l’ingestion de données. Par conséquent, il peut limiter la performance de l’ingestion des données (les événements de limitation sont enregistrés dans le journal d’Elasticsearch). Cette restriction est destinée à empêcher la création simultanée d’un grand nombre de segments d’index, devant être fusionnés et enregistrés sur disque ; un processus qui peut monopoliser les ressources. Si votre système n’exécute pas actuellement des requêtes, vous pouvez désactiver la limitation de l’ingestion de données. Cela devrait améliorer au maximum la performance de l’indexation. Vous pouvez désactiver la limitation pour un cluster entier comme suit :

	```http
	PUT /_cluster/settings
	{
		"transient" : {
			"indices.store.throttle.type": "none"
		}
	}
	```

  > [AZURE.IMPORTANT] Définissez le type de limitation du cluster de nouveau sur *« merge »* (fusionner) lorsque l’ingestion est terminée. Notez également que la désactivation de la limitation peut entraîner une instabilité du cluster. Assurez-vous donc de disposer de procédures permettant de récupérer le cluster si nécessaire.

* Elasticsearch réserve une partie de la mémoire de tas aux opérations d’indexation ; le reste est principalement utilisé par les requêtes et les recherches. L’objectif de ces mémoires tampons est de réduire le nombre d’opérations d’entrée/sortie du disque, dans le but d’effectuer des écritures moins nombreuses et plus volumineuses, au lieu de plus nombreuses et moins volumineuses. La proportion par défaut de la mémoire de tas allouée est de 10 %. Si vous indexez un gros volume de données, cette valeur peut être insuffisante. Pour les systèmes qui prennent en charge la réception de données volumineuses, vous devez autoriser jusqu’à 512 Mo de mémoire pour chaque partition active dans le nœud. Par exemple, si vous exécutez Elasticsearch sur des machines virtuelles D4 (28 Go de RAM) et avez alloué 50 % de la mémoire disponible à la JVM (14 Go), les opérations d’indexation pourront utiliser 1,4 Go de mémoire. Si un nœud contient 3 partitions actives, cette configuration est probablement suffisante. Toutefois, si un nœud contient plus de 3 partitions, pensez à augmenter la valeur du paramètre *indices.memory.index\_buffer\_size* dans le fichier de configuration elasticsearch.yml. Pour en savoir plus, consultez l’article [Performance Considerations for Elasticsearch Indexing](https://www.elastic.co/blog/performance-considerations-elasticsearch-indexing) (Facteurs de performance pour l’indexation Elasticsearch).

  > [AZURE.NOTE] L’allocation de plus de 512 Mo par partition active n’améliore probablement pas les performances d’indexation ; au contraire, cela peut avoir des effets négatifs, étant donné qu’il y a moins de mémoire disponible pour effectuer d’autres tâches. L’allocation d’espace du tas supplémentaire pour des mémoires tampons d’index supprime également de la mémoire allouée à d’autres opérations, telles que la recherche et l’agrégation de données, et peut ralentir les performances des opérations de requête.

* Elasticsearch limite le nombre de threads (la valeur par défaut est 8) pouvant effectuer des opérations d’indexation simultanées dans une partition. Si un nœud contient seulement un petit nombre de partitions, pensez à augmenter la valeur du paramètre *index\_concurrency* pour un index soumis à un volume important d’opérations d’indexation, ou qui est la cible d’une insertion en bloc, comme suit :

	```http
	PUT /my_busy_index
	{
		"settings" : {
			"index_concurrency": 20
		}
	}
	```

* Si vous effectuez un grand nombre d’indexations et d’opérations en bloc pour une courte période de temps, vous pouvez augmenter le nombre de threads destinés aux *index* et aux *opérations en bloc* dans le pool de threads, et agrandir la file d’attente pour l’*insertion en bloc* pour chaque nœud de données. Cela permettra à davantage de demandes d’être mises en file d’attente au lieu d’être ignorées. Pour plus d’informations, consultez l’article [Thread Pool](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-threadpool.html) (pool de threads). Augmenter le nombre de threads destinés aux opérations en bloc n’est pas recommandé si vous effectuez des ingestions de données volumineuses et soutenues. Au lieu de cela, créez des nœuds supplémentaires et utilisez le partitionnement pour répartir la charge de l’indexation sur ces nœuds. Vous pouvez également envisager d’envoyer des lots d’insertion en bloc en série plutôt qu’en parallèle car il agit comme un mécanisme de limitation naturel pouvant réduire les risques d’erreurs en raison du débordement des files d’attente pour l’insertion en bloc.

### L’impact de la modification de l’intervalle d’actualisation sur l’ingestion de données

L’intervalle d’actualisation régit la fréquence à laquelle les données ingérées deviennent visibles pour les requêtes et les agrégations. Cependant, les actualisations trop fréquentes peuvent avoir un effet négatif sur la performance des opérations d’ingestion de données. L’intervalle d’actualisation par défaut est d’1 seconde. Vous pouvez désactiver complètement l’actualisation, mais cela peut ne pas convenir à votre charge de travail. Vous pouvez essayer différents intervalles et déterminer un « point optimal » qui équilibre les performances d’ingestion et la nécessité de présenter des informations à jour.

Pour illustrer l’impact, le test de performance de l’ingestion de données a été répété sur un cluster Elasticsearch composé de 7 partitions réparties entre 3 nœuds de données. L’index avait un seul réplica. Chaque nœud de données était basé sur une machine virtuelle D4 (28 Go de RAM, 8 cœurs de processeur) utilisant un stockage éphémère SSD pour contenir les données. Chaque test a été exécuté pendant 1 heure.

Dans ce test, la fréquence d’actualisation a été définie sur 1 seconde, soit la valeur par défaut. Le tableau suivant indique le débit et le temps de réponse pour ce test par rapport à une exécution distincte dans laquelle la fréquence d’actualisation a été réduite à une fois toutes les 30 secondes.

| Fréquence d’actualisation | Nombre d’exemples | Temps de réponse moyen – opérations réussies (en ms) | Débit – opérations réussies (opérations/sec) |
|--------------|------------|----------------------------------------------------|---------------------------------------------------|
| 1 seconde | 93 755 | 460 | 26,0 |
| 30 secondes | 117 758 | 365 | 32,7 |

Dans ce test, la baisse de la fréquence d’actualisation a permis une amélioration de 18 % du débit, et une réduction de 21 % du temps de réponse moyen. Les graphiques suivants, générés à l’aide de Marvel, illustrent la raison principale de cette différence. La figure 2 ci-dessous illustre l’activité de fusion d’index qui s’est produite avec l’intervalle d’actualisation défini sur 1 seconde, et la figure 3 présente le niveau d’activité avec l’intervalle d’actualisation défini sur 30 secondes. La fusion d’index est exécutée pour empêcher la création d’un nombre trop élevé de segments d’index en mémoire. Un intervalle d’actualisation d’1 seconde génère un grand nombre de petits segments, qui doivent être fusionnés fréquemment, Cependant, un intervalle d’actualisation de 30 secondes génère moins de segments volumineux, qui peuvent alors être fusionnés de façon plus optimale.

![](media/guidance-elasticsearch-data-ingestion-image15.png)

***Figure 2. Activité de fusion d’index pour un intervalle d’actualisation d’1 seconde***

![](media/guidance-elasticsearch-data-ingestion-image16.png)

***Figure 3. Activité de fusion d’index pour un intervalle d’actualisation de 30 secondes***

### L’impact des réplicas sur les performances d’ingestion de données

Les réplicas constituent une fonctionnalité essentielle de n’importe quel cluster robuste et, si vous ne les utilisez pas, vous risquez de perdre des informations si un nœud devient défaillant. Toutefois, les réplicas augmentent la quantité d’entrée/sortie disque et réseau, et peuvent nuire à la fréquence à laquelle les données sont ingérées. Pour des raisons décrites précédemment, il peut être avantageux de désactiver temporairement les réplicas pendant la durée des opérations de téléchargement de données à grande échelle.

Les tests de performances d’ingestion de données ont été répétés avec trois configurations différentes :

* En utilisant un cluster sans aucun réplica.

* En utilisant un cluster avec 1 réplica.

* En utilisant un cluster avec 2 réplicas.

Dans tous les cas, le cluster comprend 7 partitions réparties sur 3 nœuds et a été exécuté sur des machines virtuelles configurées comme décrit dans la série de tests précédente. L’index de test a utilisé un intervalle d’actualisation de 30 secondes.

Le tableau suivant résume le temps de réponse et le débit de chaque test à des fins de comparaison :

| Configuration | Nombre d’exemples | Temps de réponse moyen – opérations réussies (en ms) | Débit – opérations réussies (opérations/sec) | Nombre d’erreurs d’ingestion de données |
|---------------|------------|----------------------------------------------------|---------------------------------------------------|--------------------------|
| 0 réplica | 215 451 | 200 | 59,8 | 0 |
| 1 réplica | 117 758 | 365 | 32,7 | 0 |
| 2 réplicas | 94 218 | 453 | 26,1 | 194 262 |


Il est évident que, plus il y a de réplicas, plus la performance est faible. Notez également l’ampleur d’erreurs d’ingestion de données dans le troisième test. Les messages générés par ces erreurs indiquaient qu’elles étaient dues au débordement de la file d’attente pour l’insertion en bloc, ce qui provoquait le refus des demandes. Ces refus se sont produits très rapidement, d’où le nombre important d’erreurs.

> [AZURE.NOTE]  Les résultats du troisième test soulignent l’importance d’utiliser une stratégie de nouvelle tentative intelligente lorsque des erreurs transitoires, telles que celles-ci, se produisent. Ne faites rien pendant un moment afin de permettre à la file d’attente pour l’insertion en bloc de se vider, puis retentez l’opération d’insertion en bloc.

Les graphiques suivants comparent les temps de réponse lors des tests. Dans chaque cas, le premier graphique affiche les temps de réponse globaux, tandis que le second graphique se concentre sur les temps de réponse pour les opérations plus rapides (Notez que l’échelle du premier graphique est dix fois supérieure à celle du second graphique). Vous pouvez voir comment le profil des temps de réponse varie entre les trois tests.

Sans aucun réplica, la plupart des opérations ont mis entre 75 ms et 750 ms, le temps de réponse le plus rapide étant d’environ 25 ms :

![](media/guidance-elasticsearch-data-ingestion-image17.png)

Avec 1 réplica, les temps de réponse opérationnelle les plus fréquents se trouvaient entre 125 ms et 1 250 ms. Les temps de réponse les plus rapides étaient d’environ 75 ms, bien qu’il n’y en avait pas autant que pour les tests sans aucun réplica. Ce cas comportait également des réponses qui ont mis beaucoup plus de temps que dans la plupart des cas, dépassant les 1 250 ms :

![](media/guidance-elasticsearch-data-ingestion-image18.png)

Avec 2 réplicas, les temps de réponse les plus fréquents se trouvaient entre 200 ms et 1 500 ms, mais il y avait beaucoup moins de résultats sous la fourchette inférieure que pour le test avec 1 réplica. Toutefois, le modèle de résultats se trouvant au-dessus de la limite supérieure était très similaire à celui du test avec 1 réplica. Cela est probablement dû à l’impact provoqué par le débordement de la file d’attente pour l’insertion en bloc (plus de 50 demandes dans la file d’attente). Le travail supplémentaire requis au maintien des 2 réplicas provoque plus fréquemment le débordement de la file d’attente, empêchant ainsi les opérations d’ingestion d’avoir des temps de réponse excessifs. Les opérations sont rejetées rapidement au lieu de prendre beaucoup de temps, ce qui est susceptible de provoquer des exceptions de délai d’attente ou d’avoir un impact négatif sur la réactivité des applications clientes (c’est l’objectif du mécanisme de file d’attente pour l’insertion en bloc) :

![](media/guidance-elasticsearch-data-ingestion-image19.png)

<span id="_The_Impact_of_1" class="anchor"><span id="_Impact_of_Increasing" class="anchor"></span></span>À l’aide de Marvel, vous pouvez voir l’effet du nombre de réplicas sur la file d’attente d’index en bloc. La figure 4 montre les données de Marvel qui illustrent comment la file d’attente pour l’insertion en bloc s’est remplie pendant le test. La longueur moyenne de la file d’attente tournait autour des 40 demandes, mais des rafales périodiques de demandes ont provoqué son débordement, causant ainsi le rejet des demandes :

![](media/guidance-elasticsearch-data-ingestion-image20.png)

***Figure 4. Taille de la file d’attente d’index en bloc et le nombre de demandes rejetées avec 2 réplicas.***

Vous devriez la comparer à la figure 5 ci-dessous, qui affiche les résultats pour un seul réplica. Le moteur Elasticsearch a été en mesure de traiter des demandes assez rapidement pour garder la longueur moyenne de la file d’attente à environ 25. Cette dernière n’a jamais dépassé les 50 demandes ; aucun travail n’a donc été rejeté.

![](media/guidance-elasticsearch-data-ingestion-image21.png)

***Figure 5. Taille de la file d’attente d’index en bloc et le nombre de demandes rejetées avec 1 réplica.***

## Meilleures pratiques pour les clients envoyant des données à Elasticsearch

La performance dépend non seulement du fonctionnement interne du système, mais aussi de comment ce dernier est utilisé par les applications clientes. Elasticsearch offre de nombreuses fonctionnalités qui peuvent être utilisées par le processus d’ingestion de données ; la génération d’identificateurs uniques pour les documents, l’analyse de documents, et l’utilisation de scripts pour transformer les données stockées en sont quelques exemples. Toutefois, ces fonctionnalités s’ajoutent à la charge présente sur le moteur Elasticsearch, et dans de nombreux cas, celles-ci peuvent être effectuées de manière plus efficace par les applications clientes avant la transmission. En outre, envisagez d’implémenter les pratiques suivantes, le cas échéant :

> [AZURE.NOTE] Cette liste de meilleures pratiques concerne principalement l’ingestion de nouvelles données, plutôt que la modification de données existantes déjà stockées dans un index. Les charges de travail pour l’ingestion de données sont exécutées en tant qu’opérations d’ajout par Elasticsearch, tandis que les modifications de données sont effectuées en tant qu’opérations d’ajout/suppression. Cela est dû au fait que les documents dans un index sont immuables. Pour modifier un document, il faut donc le remplacer par sa nouvelle version. Vous pouvez exécuter une demande HTTP PUT pour remplacer un document existant, ou vous pouvez utiliser l’API *update* (mise à jour) d’Elasticsearch, qui effectue une requête pour extraire un document existant, fusionne les modifications, puis exécute une opération PUT pour stocker le nouveau document.

* Désactivez l’analyse de texte pour les champs d’index qui ne nécessitent pas d’être analysés. L’analyse implique de tokeniser du texte pour activer des requêtes pouvant rechercher des termes spécifiques. Toutefois, cela peut consommer beaucoup de ressources du processeur, donc soyez sélectif. Si vous utilisez Elasticsearch pour stocker les données du journal, il peut être utile de tokeniser des messages du journal détaillés pour permettre des recherches plus complexes. D’autres champs, tels que ceux contenant des codes d’erreur ou des identificateurs, ne doivent probablement pas être tokenisés (combien de fois serez-vous amené à demander les détails de tous les messages dont le code d’erreur contient le chiffre « 3 », par exemple?) Le code suivant désactive l’analyse pour les champs *name* et *hostip* du type *logs* dans l’index *systembase* :

	```http
	PUT /systembase
	{
		"settings" : {
			...
		},
		"logs" : {
			...
			"name": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"hostip": {
				"type": "string",
				"index" : "not_analyzed"
			},
			...
		}
	}
	```

* Désactivez le champ *\_all* d’un index si celui-ci n’est pas nécessaire. Le champ *\_all* concatène les valeurs des autres champs du document pour l’analyse et l’indexation. Il est utile d’effectuer des requêtes pouvant correspondre à n’importe quel champ dans un document. Si les clients doivent correspondre aux champs nommés, l’activation du champ *\_all* entraîne simplement une surcharge de stockage et du processeur. L’exemple suivant montre comment désactiver le champ *\_all* du type *logs* dans l’index *systembase*.

	```http
	PUT /systembase
	{
		"settings" : {
			...
		},
		"logs" : {
			"_all": {
				"enabled" : false
			},
			...,
		...
		}
	}
	```

    Notez que vous pouvez créer une version sélective de *\_allt* qui contient uniquement des informations de certains champs spécifiques. Pour en savoir plus, consultez la rubrique [Disabling the \_all Field](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-all-field.html#disabling-all-field) (Désactiver le champ \_all).

* Évitez les mappages dynamiques dans les index. Le mappage dynamique est une fonctionnalité puissante, mais l’ajout de nouveaux champs à un index existant nécessite la coordination des modifications apportées à la structure d’index, et ce sur des nœuds qui peuvent provoquer temporairement le verrouillage de l’index. Le mappage dynamique peut également conduire à une explosion du nombre de champs, et donc du volume des métadonnées de l’index, s’il n’est pas utilisé avec précaution. Cela accroît les besoins et les entrées/sorties de stockage, à la fois pour l’ingestion des données et lors de l’exécution de requêtes. Ces deux problèmes auront un impact négatif sur les performances. Envisagez de désactiver le mappage dynamique et définissez vos structures d’index explicitement. Pour en savoir plus, consultez la page [Dynamic Field Mapping](https://www.elastic.co/guide/en/elasticsearch/reference/current/dynamic-field-mapping.html#dynamic-field-mapping) (Mappage de champs dynamique).

* Apprenez comment équilibrer la charge de travail pour répondre aux exigences contradictoires. Vous devez toujours prendre en considération le fait que l’ingestion de données peut avoir un impact significatif sur les performances d’autres opérations simultanées, par exemple lorsque des utilisateurs effectuent des requêtes. L’ingestion de données peut être soumise à des rafales soudaines, et si le système tente de consommer toutes les données qui arrivent immédiatement, l’afflux pourrait ralentir considérablement le taux de requête. Elasticsearch tente d’éviter cette situation en contrôlant la vitesse à laquelle il traitera les demandes d’ingestion via la file d’attente pour l’insertion en bloc (reportez-vous à la section [Déterminer les facteurs limitants : taux d’utilisation du processeur](#determining-limiting-factors-cpu-utilization) pour en savoir plus). Ce mécanisme devrait cependant être un dernier recours ; si votre code d’application n’est pas prêt à gérer les demandes rejetées, vous risquez de perdre des données. Envisagez plutôt d’utiliser un modèle tel que le [nivellement de charge basé sur la file d’attente](https://msdn.microsoft.com/library/dn589783.aspx) pour contrôler la vitesse à laquelle les données sont transmises à Elasticsearch.

* Assurez-vous que votre cluster dispose de suffisamment de ressources pour gérer la charge de travail, surtout si les index sont configurés avec plusieurs réplicas.

* Utilisez l’API d’insertion en bloc pour télécharger de gros lots de documents. Adaptez la taille des demandes en bloc de façon appropriée. Parfois, des lots plus gros ne conviennent pas à la performance, et peuvent entraîner une surcharge des threads et d’autres ressources d’Elasticsearch, ce qui retarde d’autres opérations simultanées. Les documents se trouvant dans un lot d’insertion en bloc sont conservés en mémoire au niveau du nœud de coordination pendant que l’opération est effectuée ; la taille physique de chaque lot est plus importante que le nombre de documents. Il n’y a aucune règle d’or sur ce qui constitue la taille de lot idéale, bien que la documentation d’Elasticsearch recommande d’utiliser entre 5 et 15 Mo comme point de départ pour vos propres recherches. Effectuez des tests de performance pour déterminer la taille de lot optimale pour vos propres charges de travail/scénarios.

* Assurez-vous que les demandes d’insertion en bloc sont réparties sur différents nœuds plutôt que dirigées vers un seul nœud. Le fait de diriger toutes les demandes vers un seul nœud peut provoquer l’épuisement de la mémoire, car chaque demande d’insertion en bloc en cours de traitement est stockée en mémoire dans le nœud. Cela peut également augmenter la latence du réseau, étant donné que les demandes sont redirigées vers d’autres nœuds.

* Elasticsearch utilise un quorum composé de la majorité des nœuds principaux et de nœuds de réplicas lors de l’écriture des données. Une opération d’écriture se termine que lorsque le quorum signale une réussite. Cette approche aide à garantir que les données ne soient pas écrites si la majorité des nœuds n’est pas disponible en raison d’un événement de partition réseau (échec). L’utilisation d’un quorum peut ralentir les performances des opérations d’écriture. Vous pouvez désactiver l’écriture basée sur le quorum en définissant le paramètre *consistency* sur *one* lors de l’écriture des données. L’exemple suivant ajoute un nouveau document, mais se termine dès que l’écriture dans la partition principale est terminée.

	```http
	PUT /my_index/my_data/104?consistency=one
	{
		"name": "Bert",
		"age": 23
	}
	```

	Notez que, comme avec la réplication asynchrone, la désactivation de l’écriture basée sur le quorum peut entraîner des incohérences entre la partition primaire et tous les réplicas.

* Lorsque vous utilisez des quorums, Elasticsearch attendra si un nombre insuffisant de nœuds est disponible avant de déterminer qu’une opération d’écriture doit être abandonnée, car un quorum ne peut pas être atteint. Ce délai d’attente est déterminé par le paramètre de délai d’attente de requête (la valeur par défaut est d’1 minute). Vous pouvez modifier cette configuration à l’aide du paramètre de délai d’attente de requête. L’exemple ci-dessous crée un nouveau document et attend une réponse du quorum pendant maximum 5 secondes avant d’abandonner :

	```http
	PUT /my_index/my_data/104?timeout=5s
	{
		"name": "Sid",
		"age": 27
	}
	```

	Elasticsearch vous permet également d’utiliser vos propres numéros de version [générés en externe](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-index_.html#_version_types).

* Envisagez de désactiver le champ *\_source* d’un index. Ce champ contient une copie du document JSON d’origine qui a été utilisé lors du stockage d’un document. L’enregistrement de ce champ entraîne des coûts de stockage et des entrées/sorties du disque supplémentaires. Toutefois, ces coûts peuvent être marginaux en fonction de la structure du document, et vous devez également savoir que la désactivation du champ *\_source* empêche le client d’effectuer les opérations suivantes :

	* Utilisation de l’API de mise à jour pour modifier un document.
	* Mise en surbrillance à la volée lors de l’exécution de requêtes.
	* Réindexation des données.
	* Débogage des requêtes et des agrégations en affichant le document d’origine.

	L’exemple suivant montre comment désactiver le champ *\_source* du type *logs* dans l’index *systembase*.

  ```http
  PUT /systembase
  {
		"settings" : {
			...
		},
		"logs" : {
			"_source": {
				"enabled": false
			},
			...,
		...
		}
  }
  ```

## Indications générales pour effectuer des tests de performances d’ingestion de données à l’aide d’Elasticsearch

Les points suivants mettent en évidence certains des éléments que vous devez prendre en compte lors de l’exécution de tests de performances avec Elasticsearch et lors de l’analyse des résultats.

* Les tests de performance sont forcément longs et coûteux. Au minimum, notez des statistiques mesurant les taux de transfert sur disque et réseau, le taux d’utilisation du processeur, les temps d’attente du processeur et la latence de disque (si possible). Vous pouvez ainsi avoir un feedback rapide sur votre effort de test avec un bon retour sur investissement.

* Tirez parti des fonctionnalités de script fournies par votre outil de test de charge pour collecter des mesures qui, autrement, ne seraient peut-être pas disponibles. Par exemple, Linux dispose d’une pléthore de statistiques fiables sur de bonnes performances, que vous pouvez collecter à l’aide d’utilitaires tels que *vmstat* et *iostat*. Vous pouvez utiliser des scripts avec JMeter pour capturer ces données dans le cadre d’un plan de test.

* L’ingénierie des performances repose principalement sur l’analyse des statistiques basées sur des données fiables et reproductibles. Ne vous arrêtez pas aux mesures indiquant un haut niveau, car celles-ci n’apportent pas les éclairages nécessaires. Étudiez les données et faites de l’ingénierie des performances un processus DevOps, avec une boucle de rétroaction rapide. Examinez sans cesse les statistiques comparant les tendances avec les derniers résultats/configurations. En faisant cela régulièrement, vous allez générer des données que vous comprendrez, qui sont reproductibles avec vos charges de travail, et grâce auxquelles vous serez en mesure d’évaluer les effets des modifications de configuration et de déploiement.

* Utilisez un outil tel que Marvel pour surveiller les performances des clusters et des nœuds lors des tests, afin d’obtenir des informations supplémentaires. JMeter peut être efficace pour capturer les données brutes pour une analyse ultérieure, mais Marvel peut vous donner un aperçu en temps réel de la performance et des causes possibles des problèmes et des ralentissements. En outre, plusieurs outils de test de charge ne permettent pas de visualiser les mesures internes d’Elasticsearch. Utilisez et comparez les débits d’indexation, le nombre de segments fusionnés, les statistiques du GC et les intervalles de limitation disponibles dans les statistiques d’index. Répétez cette analyse régulièrement.

* Comparez les statistiques de votre outil de test de charge aux statistiques de nœud dans Marvel (trafic disque et réseau, taux d’utilisation du processeur, utilisation de la mémoire et du pool de thread) pour comprendre le modèle de corrélation entre les chiffres rapportés par l’infrastructure et les statistiques spécifiques d’Elasticsearch.

* En règle générale, basez-vous sur le modèle *un nœud, une partition* pour les tests de performances, et évaluez les coûts de l’application par l’ajout de nœuds. Toutefois, ne vous basez pas seulement sur des extrapolations faites sur un petit nombre de nœuds et de partitions. Plus le nombre de nœuds et de partitions est élevé, plus les coûts de synchronisation et de communication dans le cluster peuvent avoir des effets imprévisibles.

* Examinez l’allocation de partitions sur les nœuds pour comparer les statistiques. Certains nœuds auront moins de réplicas et de partitions, ce qui créera un déséquilibre dans l’utilisation des ressources.

* Si vous effectuez des tests de charge, augmentez le nombre de threads utilisés par votre outil de test pour soumettre du travail au cluster jusqu’à ce que des erreurs se produisent. Pour des tests de débit durables, pensez à conserver votre niveau de test sous un «*plafond de verre*» déterminé au préalable. Si le taux d’erreur dépasse le plafond de verre, les erreurs entraîneront des frais au niveau des ressources principales, en raison de leur récupération. Dans ces cas, le débit baissera de manière inévitable.

* Pour simuler la réaction de votre système face à un pic d’activité plus important qu’attendu, envisagez d’exécuter des tests générant un taux d’erreur dépassant votre plafond de verre. Cela vous donnera des valeurs de débit, non seulement en termes de capacité, mais aussi en matière de coût de la récupération.

* Notez le nombre de documents pour évaluer votre profil de performances, et recyclez les documents suivant vos modèles de charge de travail. Gardez à l’esprit que, plus vous ajoutez de documents, plus le profil de performances est susceptible de changer.

* N’oubliez pas les contrats SLA concernant les limites de taux de transfert et des opérations d’entrée/sortie par seconde pour le type de stockage que vous utilisez. Les différents types de stockage (SSD, supports rotatifs) ont des taux de transfert différents.

* N’oubliez pas que les performances du processeur peuvent ralentir, en raison de l’activité du disque et réseau, mais aussi parce que les applications principales peuvent utiliser des mécanismes de verrouillage et de communication avec des traitements distribués, ce qui peut entraîner la sous-exploitation du processeur.

* Exécutez les tests de performances pendant au moins deux heures (pas seulement pendant quelques minutes). L’indexation peut affecter les performances sans que cela se remarque immédiatement. Par exemple, les statistiques de nettoyage de la mémoire des JVM et les fusions d’index peuvent modifier le profil de performances au fil du temps.

* Prenez en considération l’impact que peuvent avoir les actualisations des index sur le débit de l’ingestion de données et la limitation au sein d’un cluster.

## Résumé

Il est important de comprendre comment faire évoluer votre solution au fur et à mesure que le volume des données et le nombre de demandes augmentent. Elasticsearch, s’exécutant sur Microsoft Azure, permet la montée en charge horizontale et verticale ; vous pouvez exécuter des machines virtuelles plus volumineuses contenant davantage de ressources, et vous pouvez distribuer un cluster Elasticsearch sur un réseau de machines virtuelles. L’étendue d’options peut être déroutante ; est-il plus économique d’implémenter un cluster sur un grand nombre de machines virtuelles de petite taille, sur un petit nombre de machines virtuelles de grande taille, ou faut-il trouver un juste milieu ? En outre, combien de partitions chaque index doit-il contenir, et quels sont les compromis concernant l’ingestion de données/performances des requêtes ? La façon de distribuer les partitions entre les nœuds peut avoir un impact significatif sur le débit d’ingestion de données. L’utilisation de plusieurs partitions peut réduire la contention interne qui se produit au niveau d’une partition, et vous devez utiliser cet avantage pour compenser la surcharge que plusieurs partitions peuvent faire subir à un cluster. Pour répondre efficacement à ces questions, vous devez être prêt à tester votre système pour déterminer la stratégie la plus appropriée.

Pour les charges de travail d’ingestion de données, les performances du sous-système d’entrées/sorties du disque sont un facteur critique. Les SSD peuvent améliorer le débit en réduisant la latence du disque en matière d’opérations d’écriture. Si vous n’avez pas besoin de beaucoup d’espace disque sur un nœud, envisagez d’utiliser des machines virtuelles standard avec stockage éphémère au lieu de machines virtuelles plus chères qui prennent en charge le stockage premium.

## Annexe : test de performances d’ingestion de données de chargement en masse

Cette annexe décrit le test de performances effectué sur le cluster Elasticsearch. Les tests ont été exécutés à l’aide de JMeter exécuté sur un ensemble distinct de machines virtuelles. Pour en savoir plus sur la configuration de l’environnement de test, consultez le document « How-To: Create a Performance Testing Environment for Elasticsearch » (Procédure : création d’un environnement de test de performances pour Elasticsearch). Pour effectuer vos propres tests, vous pouvez créer votre propre plan de test JMeter manuellement, ou vous pouvez utiliser les scripts de test automatisé disponibles à part. Pour en savoir plus, consultez le document « How-To: Run the Automated Elasticsearch Ingestion Tests » (Procédure : exécution des tests d’ingestion automatisés Elasticsearch).

La charge de travail d’ingestion de données a téléchargé un nombre important de documents à l’aide de l’API d’insertion en bloc. L’objectif de cet index était de simuler un référentiel recevant des données de journal, représentant des événements système, pour l’analyse et la recherche subséquentes. Chaque document a été stocké dans un index unique nommé *systembase*, et était de type *logs*. Tous les documents avaient le même schéma fixe décrit dans le tableau suivant :

| Champ | Datatype | Exemple |
|---------------|---------------------|-----------------------------------|
| @timestamp | datetime | 2013-12-11T08:01:45.000Z |
| name | string | checkout.payment |
| message | string | Message de demande entrant |
| severityCode | integer | 1 |
| severity | string | info |
| hostname | string | sixshot |
| hostip | string (adresse IP) | 10\.0.0.4 |
| pid | int | 123 |
| tid | int | 4 325 |
| appId | string (uuid) | {00000000-0000-0000-000000000000} |
| appName | string | mytestapp |
| appVersion | string | 0\.1.0.1234 |
| type | int | 5 |
| subtype | int | 1 |
| correlationId | guid | {00000000-0000-0000-000000000000} |
| SE | string | Linux |
| osVersion | string | 4\.1.1 |
| parameters | [ ] | {key:value,key:value} |

Vous pouvez utiliser la demande suivante pour créer l’index. La valeur des paramètres *number\_of\_replicas*, *refresh\_interval*, et *number\_of\_shards* différait parfois de celles affichées ci-dessous dans plusieurs des tests.

> [AZURE.IMPORTANT] L’index a été supprimé, puis recréé, avant chaque série de tests.

```http
PUT /systembase
{
	"settings" : {
		"number_of_replicas": 1,
		"refresh_interval": "30s",
		"number_of_shards": "5"
	},
	"logs" : {
		"properties" : {
			"@timestamp": {
			"type": "date",
			"index" : "not_analyzed"
			},
			"name": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"message": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"severityCode": {
				"type": "integer",
				"index" : "not_analyzed"
			},
			"severity": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"hostname": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"hostip": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"pid": {
				"type": "integer",
				"index" : "not_analyzed"
			},
			"tid": {
				"type": "integer",
				"index" : "not_analyzed"
			},
			"appId": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"appName": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"appVersion": {
				"type": "integer",
				"index" : "not_analyzed"
			},
			"type": {
				"type": "integer",
				"index" : "not_analyzed"
			},
			"subtype": {
				"type": "integer",
				"index" : "not_analyzed"
			},
			"correlationId": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"os": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"osVersion": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"parameters": {
				"type": "string",     
				"index" : "not_analyzed"
			}
		}
	}
}
```

Chaque lot d’insertion en bloc était composé de 1 000 documents. Chaque document a été généré selon une combinaison de valeurs aléatoires pour les champs *severityCode*, *hostname*, *hostip*, *pid*, *tid*, *appName*, *appVersion*, *type*, *subtype*, et *correlationId*, et selon une sélection aléatoire de texte à partir d’un ensemble fixe de termes pour les champs *name*, *message*, *severity*, *os*, *osVersion*, *parameters*, *data1*, et *data2*. Le nombre d’instances d’applications clientes utilisées pour télécharger des données a été soigneusement étudié pour optimiser le volume d’entrées réussies. Les tests ont été exécutés pendant deux heures pour permettre au cluster de réduire son activité ainsi que l’influence des problèmes temporaires sur les résultats globaux. Pendant cet intervalle, les tests ont téléchargé près de 1,5 milliard de documents.

Les données ont été générées dynamiquement à l’aide d’un échantillonneur de demandes JUnit Request ajouté à un groupe de threads dans le cadre d’un plan de test JMeter. Le code JUnit a été créé à l’aide du modèle JUnit Test Case dans l’IDE d’Eclipse.

> [AZURE.NOTE] Pour en savoir plus sur la création d’un test JUnit pour JMeter, consultez le document « How-To: Create and Deploy a JMeter JUnit Sampler for Testing Elasticsearch Performance » (Procédure : création et déploiement d’un échantillonneur JUnit JMeter pour tester les performances d’Elasticsearch).

L’extrait suivant montre le code Java pour tester Elasticsearch 1.7.3. Notez que la classe de test JUnit est nommée *ElasticSearchLoadTest2* dans cet exemple :

```java
/* Java */
package elasticsearchtest2;

	import static org.junit.Assert.*;

	import org.junit.*;

	import java.util.*;

	import java.io.*;

	import org.elasticsearch.action.bulk.*;
	import org.elasticsearch.common.transport.*;
	import org.elasticsearch.client.transport.*;
	import org.elasticsearch.common.settings.*;
	import org.elasticsearch.common.xcontent.*;

	public class ElasticSearchLoadTest2 {

		private String [] names={"checkout","order","search","payment"};
		private String [] messages={"Incoming request from code","incoming operation succeeded with code","Operation completed time","transaction performed"};
		private String [] severity={"info","warning","transaction","verbose"};
		private String [] apps={"4D24BD62-20BF-4D74-B6DC-31313ABADB82","5D24BD62-20BF-4D74-B6DC-31313ABADB82","6D24BD62-20BF-4D74-B6DC-31313ABADB82","7D24BD62-20BF-4D74-B6DC-31313ABADB82"};

		private String hostname = "";
		private String indexstr = "";
		private String typestr = "";
		private int port = 0;
		private int itemsPerInsert = 0;
		private String clustername = "";
		private static Random rand=new Random();

		@Before
		public void setUp() throws Exception {
		}

		public ElasticSearchLoadTest2(String paras) {
		* Paras is a string containing a set of comma separated values for:
			hostname
			indexstr
			typestr
			port
			clustername
			node
			itemsPerInsert
		*/

			// Note: No checking/validation is performed

			String delims = "[ ]*,[ ]*"; // comma surrounded by zero or more spaces
			String[] items = paras.split(delims);

			hostname = items[0];
			indexstr = items[1];
			typestr = items[2];
			port = Integer.parseInt(items[3]);
			clustername = items[4];
			itemsPerInsert = Integer.parseInt(items[5]);

			if (itemsPerInsert == 0)
				itemsPerInsert = 1000;
			}

		@After
		public void tearDown() throws Exception {
		}

		@Test
		public void BulkBigInsertTest() throws IOException {

			Settings settings = ImmutableSettings.settingsBuilder().put("cluster.name", clustername).build();

			TransportClient client;
			client = new TransportClient(settings);

			try {
				client.addTransportAddress(new InetSocketTransportAddress(hostname, port));
				BulkRequestBuilder bulkRequest = client.prepareBulk();
				Random random = new Random();
				char[] exmarks = new char[12000];
				Arrays.fill(exmarks, 'x');
				String dataString = new String(exmarks);

				for(int i=1; i &lt; itemsPerInsert; i++){
					random.nextInt(10);
					int host=random.nextInt(20);

					bulkRequest.add(client.prepareIndex(indexstr, typestr).setSource(XContentFactory.jsonBuilder().startObject()
						.field("@timestamp", new Date())
						.field("name", names[random.nextInt(names.length)])
						.field("message", messages[random.nextInt(messages.length)])
						.field("severityCode", random.nextInt(10))
						.field("severity", severity[random.nextInt(severity.length)])
						.field("hostname", "Hostname"+host)
						.field("hostip", "10.1.0."+host)
						.field("pid",random.nextInt(10))
						.field("tid",random.nextInt(10))
						.field("appId", apps[random.nextInt(apps.length)])
						.field("appName", "application" + host)
						.field("appVersion", random.nextInt(5))
						.field("type", random.nextInt(6))
						.field("subtype", random.nextInt(6))
						.field("correlationId", UUID.randomUUID().toString())
						.field("os", "linux")
						.field("osVersion", "14.1.5")
						.field("parameters", "{key:value,key:value}")
						.field("data1",dataString)
						.field("data2",dataString)
					.endObject()));
				}

				BulkResponse bulkResponse = bulkRequest.execute().actionGet();
				assertFalse(bulkResponse.hasFailures());
			}
			finally {
				client.close();
			}
		}

		@Test
		public void BulkDataInsertTest() throws IOException {
			Settings settings = ImmutableSettings.settingsBuilder().put("cluster.name", clustername).build();

			TransportClient client;
			client = new TransportClient(settings);

			try {
				client.addTransportAddress(new InetSocketTransportAddress(hostname, port));
				BulkRequestBuilder bulkRequest = client.prepareBulk();

				for(int i=1; i&lt; itemsPerInsert; i++){
					rand.nextInt(10);
					int host=rand.nextInt(20);

					bulkRequest.add(client.prepareIndex(indexstr, typestr).setSource(XContentFactory.jsonBuilder().startObject()
						.field("@timestamp", new Date())
						.field("name", names[rand.nextInt(names.length)])
						.field("message", messages[rand.nextInt(messages.length)])
						.field("severityCode", rand.nextInt(10))
						.field("severity", severity[rand.nextInt(severity.length)])
						.field("hostname", "Hostname" + host)
						.field("hostip", "10.1.0."+host)
						.field("pid",rand.nextInt(10))
						.field("tid",rand.nextInt(10))
						.field("appId", apps[rand.nextInt(apps.length)])
						.field("appName", "application"+host)
						.field("appVersion", rand.nextInt(5))
						.field("type", rand.nextInt(6))
						.field("subtype", rand.nextInt(6))
						.field("correlationId", UUID.randomUUID().toString())
						.field("os", "linux")
						.field("osVersion", "14.1.5")
						.field("parameters", "{key:value,key:value}")
					.endObject()));
				}

				BulkResponse bulkResponse = bulkRequest.execute().actionGet();
				assertFalse(bulkResponse.hasFailures());
			}
			finally {
				client.close();
			}
		}
	}
```

Les tableaux *names*, *messages*, *severity*, et *apps* de private *String* contiennent un petit ensemble de valeurs à partir desquelles les éléments sont sélectionnés au hasard. Les autres éléments de données pour chaque document sont générés lors de l’exécution.

Le constructeur qui prend le paramètre *String* est appelé depuis JMeter, et les valeurs passées dans la chaîne sont spécifiées dans le cadre de la configuration de l’échantillonneur de demandes JUnit Request. Pour ce test JUnit, le paramètre *String* doit contenir les informations suivantes :

* **Hostname**. Il s’agit du nom ou de l’adresse IP de l’équilibreur de charge Azure. L’équilibreur de charge tente de distribuer la demande entre les nœuds de données du cluster. Si vous n’utilisez pas un équilibreur de charge, vous pouvez spécifier l’adresse d’un nœud du cluster. Toutes les demandes seront cependant dirigées vers ce nœud, ce qui peut faire de lui un goulot d’étranglement.

* **Indexstr**. Il s’agit du nom de l’index dans lequel les données générées par le test JUnit sont ajoutées. Si vous avez créé l’index comme décrit ci-dessus, cette valeur doit être *systembase*.

* **Typestr**. C’est le type de l’index dans lequel les données sont stockées. Si vous avez créé l’index comme décrit ci-dessus, cette valeur doit être *logs*.

* **Port**. Il s’agit du port sur lequel la connexion doit être établie sur l’ordinateur hôte. Dans la plupart des cas, celui-ci doit être le port 9300 (le port utilisé par Elasticsearch pour écouter les demandes de l’API cliente ; le port 9200 est utilisé uniquement pour les requêtes HTTP).

* **Clustername**. Il s’agit du nom du cluster Elasticsearch contenant l’index.

* **ItemsPerInsert**. Il s’agit d’un paramètre numérique qui indique le nombre de documents à ajouter dans chaque lot d’insertion en bloc. La taille de lot par défaut est de 1 000 documents.

Vous spécifiez les données pour la chaîne du constructeur dans la page JUnit Request, utilisée pour configurer l’échantillonneur JUnit dans JMeter. L’image suivante en montre un exemple :

![](media/guidance-elasticsearch-data-ingestion-image22.png)

Les méthodes *BulkInsertTest* et *BigBulkInsertTest* réalisent le travail effectif de génération et de téléchargement des données. Les deux méthodes sont très similaires ; elles se connectent au cluster Elasticsearch et créent un lot de documents (comme déterminé par le paramètre de la chaîne du constructeur *ItemsPerInsert*). Les documents sont ajoutés à l’index à l’aide de l’API en bloc Elasticsearch. La différence entre les deux méthodes est que les champs de chaîne *data1* et *data2* sont omis dans chaque document lors du téléchargement pour la méthode *BulkInsertTest*, tandis que la méthode *BigBulkInsertTest* les remplit de chaînes composées de 12 000 caractères. Pour sélectionner quelle méthode exécuter, utilisez l’option *Test Method* de la page JUnit Request dans JMeter (mise en surbrillance dans la figure précédente).

> [AZURE.NOTE] L’exemple de code présenté ici utilise la bibliothèque « Elasticsearch 1.7.3 Transport Client ». Si vous utilisez Elasticsearch 2.0.0 ou une version ultérieure, vous devez utiliser la bibliothèque appropriée pour la version. Pour en savoir plus sur la bibliothèque Elasticsearch 2.0.0 Transport Client, consultez la page [Transport Client](https://www.elastic.co/guide/en/elasticsearch/client/java-api/2.0/transport-client.html) sur le site web d’Elasticsearch.

<!----HONumber=AcomDC_0211_2016-->