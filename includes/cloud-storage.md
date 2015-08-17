#Gestion des données et analyse marketing

La gestion et l’analyse des données du cloud sont aussi importantes que celles des autres données. C’est pourquoi Azure est doté de diverses technologies permettant d’exploiter des données relationnelles et non relationnelles. Cet article présente chacune des options.

##Sommaire      

- [Stockage d'objets blob](#blob)
- [Exécution d'un SGBD (système de gestion de base de données) dans une machine virtuelle](#dbinvm)
- [Base de données SQL](#sqldb)
	- [Synchronisation des données SQL](#datasync)
	- [Génération de rapports de données SQL à l'aide de machines virtuelles](#datarpt)
- [Stockage de table](#tblstor)
- [Hadoop](#hadoop)

## <a name="blob"></a>Stockage d'objets blob

« Blob » est un diminutif pour « Binary Large OBject » (« Objets volumineux binaires ») : c’est une collection d’informations binaires. Tout en étant simples, les objets blob sont utiles. La [figure 1](#Fig1) illustre les bases du stockage d’objets blob Azure.

<a name="Fig1"></a>![Diagramme d’objets blob][blobs]
 
**Figure 1 : le stockage d'objets blob Azure stocke des données binaires (des objets blob) dans des conteneurs**.

Pour utiliser des objets blob, commencez par créer un *compte de stockage* Azure. Durant cette opération, vous devez indiquer le centre de données Azure qui stockera les objets que vous créez en utilisant ce compte. Où qu’il réside, chaque objet blob que vous créez appartient à un conteneur de votre compte de stockage. Pour accéder à un objet blob, une application fournit une URL au format :

http://&lt;*StorageAccount*&gt;.blob.core.windows.net/&lt;*Container*&gt;/&lt;*BlobName*&gt ;

&lt;*CompteStockage*&gt; correspond à un identificateur unique attribué lors de la création d’un compte de stockage, tandis que &lt;*Conteneur*&gt; et &lt;*NomObjetBlob*&gt; correspondent aux noms d’un conteneur spécifique et d’un objet blob dans ce conteneur.

Azure fournit deux types d’objet blob. Les choix sont les suivants :

- Objets blob de *blocs* : chacun d’eux peut contenir jusqu’à 200 Go de données. Comme son nom l’indique, un objet blob de blocs est divisé en un certain nombre de blocs. En cas de panne lors du transfert d’un objet blob de blocs, il est possible de reprendre la transmission avec le bloc le plus récent, plutôt que de renvoyer l’intégralité de l’objet blob. Les objets blob de blocs s’inscrivent dans une approche globale du stockage. Ils constituent le type d’objet blob le plus utilisé actuellement.

- Les objets blob de *page*, eux, peuvent atteindre jusqu’à un téraoctet. Ils sont conçus pour un accès aléatoire. Chacun d’eux est donc divisé en un certain nombre de pages. Une application est libre de lire et d’écrire sur des pages individuelles au hasard dans l’objet blob. Par exemple, les machines virtuelles créées avec Azure utilisent des objets blob de page pour le stockage permanent pour les disques de système d’exploitation et les disques de données.

Que vous choisissiez des objets blob de blocs ou de page, les applications peuvent accéder aux données d’objets blob de différentes manières. Les options disponibles sont les suivantes :

- Accès direct via un protocole d’accès RESTful (par exemple, HTTP). Les applications Azure, comme les applications externes et notamment les applications locales, peuvent utiliser cette option.
- Accès via la bibliothèque du client de stockage Azure, qui offre une interface plus conviviale pour les développeurs, en plus du protocole d’accès d’objet blob RESTful brut. Une fois encore, les applications Azure, comme les applications externes, peuvent accéder aux objets blob en utilisant cette bibliothèque.
- Accès à l’aide d’un disque Azure, une option permettant à une application Azure de traiter un objet blob de page comme un lecteur local avec un système de fichiers NTFS. Pour l’application, l’objet blob de page ressemble à un système de fichiers Windows ordinaire, auquel l'accès se fait à l’aide d’E/S de fichier standard. Les lectures et écritures sont envoyées vers l’objet blob de page sous-jacent qui implémente le lecteur Azure. 

Chaque objet blob est répliqué entre trois ordinateurs dans un centre de données Azure, afin de réduire les risques de pannes et d’améliorer la disponibilité. Une écriture sur un objet blob entraîne la mise à jour des trois copies, pour éviter toute incohérence lors des lectures ultérieures. Vous pouvez également spécifier que les données d’un objet blob doivent être copiées vers un autre centre de données Azure situé à plus de 800 km du centre de données actuel. Cette copie, nommée *géo-réplication*, se produit quelques minutes après la mise à jour de l’objet blob. Elle est utile en cas de récupération d’urgence.

Les données des objets blob sont également disponibles via le *réseau de distribution de contenu* Azure. En mettant en cache des copies de données d’objet blob sur des dizaines de serveurs à travers le monde, le réseau de distribution de contenu peut accélérer l’accès aux informations consultées régulièrement.

Grâce à leur simplicité, les objets blob sont adaptés à de nombreuses situations. Le stockage et la diffusion de fichiers vidéo et audio sont des exemples évidents, tout comme les sauvegardes et d’autres types d’archivages de données. Les développeurs peuvent également utiliser les objets blob pour conserver un type de données non structurées qui leur convient. Le fait de bénéficier d’une méthode directe pour stocker des données binaires et y accéder peut s’avérer étonnamment utile.


## <a name="dbinvm"></a>Exécution d'un SGBD (système de gestion de base de données) dans une machine virtuelle

De nos jours, de nombreuses applications reposent sur un type de système de gestion de base de données (SGBD). Les systèmes relationnels comme SQL Server font partie des choix les plus répandus. Cependant, les approches non relationnelles, généralement connues sous le nom de « technologies *NoSQL* », deviennent de plus en plus populaires. Pour permettre aux applications cloud d’utiliser ces options de gestion de données, les machines virtuelles Azure peuvent exécuter un SGBD (relationnel ou NoSQL). La [figure 2](#Fig2) montre une machine virtuelle avec SQL Server.

<a name="Fig2"></a>![Diagramme montrant SQL Server dans une machine virtuelle][SQLSvr-vm]
 
**Figure 2 : les machines virtuelles Azure permettent d'exécuter un SGBD dans une machine virtuelle de façon permanente grâce aux objets blob**.

Pour les développeurs et les administrateurs de bases de données, ce scénario revient pratiquement à exécuter le même logiciel dans leur propre centre de données. Dans l’exemple indiqué ici, vous pouvez utiliser presque toutes les capacités de SQL Server, tout en disposant d’un accès administratif complet au système. Bien entendu, vous êtes également responsable de la gestion du serveur de base de données, comme s’il était exécuté en local.

Comme le montre la [Figure 2](#Fig2), vos bases de données sont stockées sur le disque local de la machine virtuelle sur laquelle est exécuté le serveur. En réalité, chacun de ces disques est écrit dans un objet blob Azure. (Cela revient à utiliser un SAN dans votre propre centre de données, avec un objet blob agissant pratiquement comme un numéro d’unité logique.) Tout comme avec n’importe quel objet blob Azure, les données contenues sont répliquées à trois reprises dans le centre de données et, si vous le demandez, géo-répliquées dans un autre centre de données dans la même région. Vous pouvez également utiliser des options telles qu’une mise en miroir de base de données SQL Server pour améliorer la fiabilité.

Une autre méthode d’utilisation de SQL Server dans une machine virtuelle consiste à créer une application hybride, où les données résident sur Azure tandis que la logique de l’application est exécutée en local. Cela peut être utile, par exemple, lorsque des applications exécutées dans plusieurs endroits ou sur plusieurs appareils mobiles doivent partager les mêmes données. Pour simplifier la communication entre la base de données du cloud et la logique locale, une organisation peut utiliser le réseau virtuel Azure pour créer une connexion de réseau privé virtuel entre un centre de données Azure et son propre centre de données local.


## <a name="sqldb"></a>Base de données SQL

De nombreuses personnes pensent d’abord à exécuter un SGBD dans une machine virtuelle pour gérer des données structurées dans le cloud. Cependant, cette option n’est pas la seule possible, ni la meilleure. Dans certains cas, la gestion de données à l’aide d’une approche PaaS (Platform as a Service) s’avère plus efficace. Azure fournit une technologie PaaS nommée « Base de données SQL » qui vous permet de procéder ainsi pour les données relationnelles. La [figure 3](#Fig3) illustre cette option.

<a name="Fig3"></a>![Diagramme de base de données SQL][SQL-db]
 
**Figure 3 : la base de données SQL fournit un service de stockage relationnel PaaS partagé**.

La base de données SQL ne fournit pas à chaque client sa propre instance de SQL Server. Elle fournit plutôt un service mutualisé, avec un serveur de base de données SQL logique pour chaque client. Tous les clients partagent la capacité de calcul et de stockage fournie par le service. Et comme pour le stockage d’objets blob, toutes les données de base de données SQL sont stockées sur trois ordinateurs distincts dans un centre de données Azure, permettant à vos bases de données de bénéficier d’une haute disponibilité intégrée.

Pour une application, la base de données SQL ressemble beaucoup à SQL Server. Les applications peuvent émettre des requêtes SQL à destination des tables relationnelles, utiliser des procédures T-SQL stockées et exécuter des transactions entre plusieurs tables. Et comme les applications peuvent accéder à la base de données SQL en utilisant le protocole TDS (Tabular Data Stream), qui permet également d’accéder à SQL Server, elles peuvent fonctionner avec des données utilisant Entity Framework, ADO.NET, JDBC et d’autres interfaces d’accès aux données répandues.

Mais comme la base de données SQL est un service cloud exécuté dans les centres de données Azure, vous n’avez pas à gérer le côté « physique » du système, comme l’utilisation des disques. De plus, vous n’avez plus besoin de vous soucier de la mise à jour de vos logiciels ou de la gestion d’autres tâches d’administration de bas niveau. Bien sûr, chaque organisation cliente contrôle toujours ses propres bases de données (et notamment ses schémas et connexions d’utilisateurs), mais de nombreuses tâches d’administration courantes sont faites à votre place.

Même si la base de données SQL ressemble assez à SQL Server pour les applications, son comportement n’est pas identique à celui d’un SGBD exécuté sur une machine physique ou virtuelle. Comme il est exécuté sur du matériel partagé, ses performances peuvent varier en fonction de la charge placée sur ce matériel par l’ensemble de ses clients. Cela signifie par exemple que les performances d’une procédure stockée dans la base de données SQL peuvent varier d’un jour sur l’autre.

Actuellement, la base de données SQL vous permet de créer une base de données contenant jusqu’à 150 Go. Si vous devez utiliser des bases de données plus volumineuses, le service fournit une option nommée *Fédération*. Pour l’utiliser, un administrateur de base de données crée au moins deux *membres de fédération*. Chaque « membre » est une base de données distincte dotée de son propre schéma. Les données sont transmises entre ces membres, un processus généralement nommé *partitionnement*, où chaque membre se voit attribuer une *clé de fédération* unique. Une application émet des requêtes SQL à destination de ces données, en spécifiant la clé de fédération identifiant le membre de fédération que la requête doit cibler. Ceci permet d’utiliser une approche relationnelle traditionnelle pour de grandes quantités de données. Comme d’habitude, il y a des inconvénients : par exemple, aucune requête ou transaction ne peut étendre de membres de fédération. Mais lorsqu’un service PaaS relationnel est le meilleur choix et que ces inconvénients sont acceptables, l’utilisation de la fédération SQL peut s’avérer judicieuse.

La base de données SQL peut être utilisée par des applications exécutées sous Azure ou non, comme dans votre centre de données local. Ceci est utile pour les applications cloud nécessitant des données relationnelles, mais aussi pour les applications locales pouvant bénéficier du stockage de données dans le cloud. Une application mobile peut se reposer sur la base de données SQL pour la gestion des données relationnelles partagées, par exemple, une application d’inventaire exécutée chez plusieurs fournisseurs à travers le monde.

L'utilisation d'une base de données SQL pose un problème évident (et important) : à quel moment devez-vous exécuter un serveur SQL sur une machine virtuelle, et dans quel cas est-il préférable de choisir une base de données SQL ? Comme d’habitude, il existe des inconvénients dans les deux cas. La meilleure approche dépend de vos exigences.

La façon la plus simple d’envisager le problème est de considérer que la base de données SQL est destinée aux nouvelles applications, tandis que SQL Server sur une machine virtuelle est un choix plus adapté lorsque vous déplacez une application locale existante sur le cloud. Cependant, il peut être plus utile de considérer cette décision plus en détail. Par exemple, la base de données SQL est plus simple d’utilisation, même si elle nécessite un minimum de tâches de configuration et d’administration. Mais l’exécution de SQL Server dans une machine virtuelle entraîne des performances plus prévisibles (il ne s’agit pas d’un service partagé) et prend aussi en charge des bases de données non fédérées plus importantes qu’une base de données SQL. Cependant, la base de données SQL est dotée d’une capacité de réplication intégrée des données et des traitements, vous permettant de bénéficier d’un SGBD à haute disponibilité sans trop d’efforts. Tandis que SQL Server offre un contrôle accru et un peu plus d’options, la base de données SQL est plus simple à configurer et nécessite moins de travail.

Pour finir, il est important de savoir que la base de données SQL n’est pas le seul service de données PaaS disponible pour Azure. Les partenaires Microsoft peuvent également fournir d’autres options. Par exemple, ClearDB propose une offre PaaS MySQL et Cloudant une option NoSQL. Dans de nombreuses situations, les services de données PaaS constituent la meilleure solution. Cette approche de gestion des données représente donc une partie importante d’Azure.


### <a name="datasync"></a>Synchronisation des données SQL

Si la base de données SQL conserve trois copies de chaque base de données dans un seul centre de données Azure, elle ne réplique pas automatiquement les données entre les centres de données Azure. Au lieu de ça, elle fournit la synchronisation des données SQL, un service qui s’occupe de cette tâche. [La figure 4](#Fig4) illustre ce concept.

<a name="Fig4"></a>![Diagramme de la synchronisation des données SQL][SQL-datasync]
 
**Figure 4 : la synchronisation des données SQL synchronise les données de la base de données SQL avec les données d'autres centres de données Azure et locaux**.

Comme l’indique le diagramme, la synchronisation des données SQL peut synchroniser des données entre différents emplacements. Supposons que vous exécutez une application dans plusieurs centres de données Azure, avec des données stockées dans la base de données SQL. Vous pouvez utiliser la synchronisation des données SQL pour que ces données restent synchronisées. La synchronisation des données SQL peut également synchroniser des données entre un centre de données Azure et une instance de SQL Server exécutée dans un centre de données local. Ceci peut être utile pour conserver une copie locale des données utilisées par des applications locales et une copie sur le cloud, utilisée par des applications exécutées sous Azure. Et même si cette figure ne l’indique pas, vous pouvez aussi utiliser la synchronisation des données SQL pour synchroniser des données entre la base de données SQL et SQL Server dans une machine virtuelle, sous Azure ou ailleurs.

La synchronisation peut être bi-directionnelle et vous pouvez déterminer exactement quelles données sont synchronisées, ainsi que la fréquence de synchronisation. (Cependant, la synchronisation entre les bases de données n’est pas atomique : il y a toujours un léger décalage.) Et quelle que soit son utilisation, la configuration de la synchronisation avec la synchronisation des données SQL est complètement pilotée par la configuration : vous n’avez pas besoin de rédiger du code.


### <a name="datarpt"></a>Génération de rapports de données SQL à l'aide de machines virtuelles

Lorsqu’une base de données contient des données, un utilisateur peut être amené à générer des rapports à l’aide de celles-ci. Azure peut exécuter SQL Server Reporting Services (SSRS) dans les machines virtuelles Azure, ce qui revient à exécuter SQL Server Reporting Services en local. SSRS permet de générer des rapports sur les données stockées dans une base de données SQL Azure. La [figure 5](#Fig5) montre le fonctionnement du processus.

<a name="Fig5"></a>![Diagramme de la génération de rapports SQL][SQL-report]
 
**Figure 5 : le service SQL Server Reporting Services exécuté dans une machine virtuelle Azure fournit des services de génération de rapport pour les données de la base de données SQL**.

Avant qu’un utilisateur puisse afficher un rapport, un autre utilisateur doit définir son apparence (étape 1). Si SSRS est installé sur une machine virtuelle, cette opération peut être effectuée avec l'un de ces deux outils : SQL Server Data Tools, qui est inclus dans SQL Server 2012, ou son prédécesseur, Business Intelligence (BI) Development Studio. Comme avec SSRS, ces définitions de rapport sont exprimées dans la syntaxe RDL (Report Definition Language). Une fois les fichiers RDL créés, ils sont téléchargés vers une machine virtuelle dans le cloud (étape 2). La définition de rapport est à présent prête pour utilisation.

Ensuite, un utilisateur de l’application accède au rapport (étape 3). L’application transmet la demande à la machine virtuelle SSRS (étape 4), qui contacte la base de données SQL ou une autre source de données pour obtenir les données nécessaires (étape 5). SSRS utilise les données et les fichiers RDL adéquats pour créer le rapport (étape 6), puis renvoie ce dernier vers l’application (étape 7), qui l’affiche pour l’utilisateur (étape 8).

L’intégration d’un rapport à une application (le scénario exposé ci-dessus) ne constitue pas la seule option. Vous pouvez également afficher des rapports dans le gestionnaire de rapports de la machine virtuelle, sous SharePoint dans la machine virtuelle, etc. Vous pouvez aussi combiner les rapports : un rapport peut contenir un lien menant vers un autre rapport.

SSRS sur une machine virtuelle Azure vous offre des fonctionnalités complètes, comme une solution de génération de rapports dans le cloud. Les rapports peuvent utiliser n’importe quelle source de données prise en charge par SSRS. Les applications et les rapports peuvent inclure du code intégré ou des assemblys pour la prise en charge de comportements personnalisés. La génération et le rendu des rapports sont rapides, car le contenu du serveur de rapport et le moteur sont exécutés ensemble sur le même serveur virtuel.



## <a name="tblstor"></a>Stockage de tables

Les données relationnelles sont utiles dans bien des situations, mais elles ne constituent pas toujours le meilleur choix. Si votre application doit pouvoir accéder rapidement et simplement à d’importantes quantités de données faiblement structurées, une base de données relationnelle ne fera pas l’affaire. L’usage de la technologie NoSQL semble alors être un meilleur choix.

Le stockage de table Azure est un bon exemple de cette approche NoSQL. Malgré son nom, le stockage de table ne prend pas en charge les tables relationnelles standard. Au lieu de cela, ce type de stockage fournit un *stockage de clés/valeurs*, en associant un ensemble de données avec une clé particulière, puis en permettant à une application d’accéder à ces données en lui fournissant la clé. Les bases de ce concept sont illustrées par la [figure 6](#Fig6).

<a name="Fig6"></a>![Diagramme de stockage de table][SQL-tblstor]
 
**Figure 6 : le stockage de table Azure est un stockage de clés/valeurs fournissant un accès rapide et simplifié à d'importantes quantités de données**.

Comme les objets blob, chaque table est associée à un compte de stockage Azure. Les tables sont également nommées comme des objets blob, avec une URL au format

http://&lt;*StorageAccount*&gt;.table.core.windows.net/&lt;*TableName*&gt ;

Comme l’indique cette figure, chaque table est divisée en un certain nombre de partitions, chacune d’elle pouvant être stockée sur une machine distincte. (Il s’agit d’une forme de partitionnement, comme pour la fédération SQL.) Les applications Azure, comme les applications externes, peuvent accéder à une table en utilisant le protocole OData RESTful ou la bibliothèque du client de stockage Azure.

Chaque partition dans une table contient un certain nombre d’*entités*. Chacune d’entre elles contient jusqu’à 255 *propriétés*. Chaque propriété a un nom, un type (Binary, Bool, DateTime, Int ou String) et une valeur. Contrairement au stockage relationnel, ces tables n’ont pas de schéma fixe. Il est donc possible que différentes entités d’une table contiennent des propriétés de différents types. Par exemple, une entité peut simplement avoir une propriété String contenant un nom, tandis qu’une autre entité de la même table peut avoir deux propriétés Int contenant un numéro d’ID client et un degré de solvabilité.

Pour identifier une entité précise dans une table, une application fournit la clé de cette entité. La clé est constituée de deux parties : une *clé de partition* qui identifie une partition spécifique, ainsi qu'une *clé de ligne* qui identifie une entité dans cette partition. Par exemple, dans la [figure 6](#Fig6), le client demande une entité avec une clé de partition A et une clé de ligne 3, puis le stockage de table renvoie cette entité, incluant toutes les propriétés qu’elle contient.

Cette structure permet aux tables de devenir volumineuses (une seule table peut contenir jusqu’à 100 To de données) mais aussi d’accéder rapidement aux données qu’elles contiennent. Cependant, elle a aussi ses limites. Par exemple, les mises à jour transactionnelles qui étendent les tables, ou même les partitions dans une seule table, ne sont pas prises en charge. Un ensemble de mises à jour pour une table peut uniquement être regroupé dans une transaction atomique si toutes les entités impliquées appartiennent à la même partition. Il est également impossible d’envoyer vers une table une requête basée sur les valeurs de ses propriétés, ni de prendre en charge les jonctions entre plusieurs tâches. Et contrairement aux bases de données relationnelles, les procédures stockées des tables ne sont pas prises en charge.

Le stockage de table Azure est un bon choix pour les applications devant accéder rapidement et facilement à d’importantes quantités de données faiblement structurées. Par exemple, une application Internet stockant des informations de profil pour de nombreux utilisateurs peut utiliser des tables. Dans ce cas, un accès rapide est important, tandis que l’application n’a pas forcément besoin de toute la puissance de SQL. L’abandon de cette fonctionnalité pour gagner en vitesse et en taille peut parfois sembler logique, c’est pourquoi le stockage de table est parfaitement adapté à certains problèmes.


## <a name="hadoop"></a>Hadoop

Les organisations développent des entrepôts de données depuis des décennies. Ces collections d’informations, généralement stockées dans des tables relationnelles, permettent à différentes personnes d’utiliser et d’exploiter des données de bien des façons. Par exemple, avec SQL Server, l’utilisation d’outils tels que SQL Server Analysis Services est répandue.

Supposons que vous souhaitez analyser des données non relationnelles. Vos données peuvent prendre plusieurs formes : des informations provenant de capteurs ou de puces RFID, des fichiers journaux dans des batteries de serveurs, des données de parcours générées par des applications Web, des images obtenues depuis des périphériques de diagnostics médicaux, etc. La taille de ces données peut être importante, trop importante pour qu’elles soient utilisées efficacement dans un entrepôt de données traditionnel. Des problèmes de données volumineuses comme ceux-ci, qui étaient rares il y a quelques années, se posent à présent de plus en plus.

Pour analyser ces types de données volumineuses, notre secteur s’est majoritairement orienté vers une seule solution : la technologie open-source Hadoop. Hadoop est exécuté sur un cluster de machines physiques ou virtuelles, transmettant les données utilisées entre ces machines tout en les traitant en parallèle. Plus Hadoop utilise de machines, plus vite il peut terminer ses tâches.

Ce type de problème est naturel pour un cloud public. Plutôt que d’entretenir une horde de serveurs locaux susceptibles de rester inactifs la plupart du temps, l’exécution de Hadoop dans le cloud vous permet de créer des machines virtuelles en payant uniquement ce dont vous avez besoin. Encore mieux, comme de plus en plus de données volumineuses à analyser avec Hadoop sont créées dans le cloud, vous n’avez plus besoin de les déplacer. Pour vous permettre d’exploiter ces synergies, Microsoft fournit un service Hadoop sous Azure. La [figure 7](#Fig7) montre les composants les plus importants de ce service.

<a name="Fig7"></a>![Diagramme de Hadoop][hadoop]

**Figure 7 : sous Azure, Hadoop exécute des tâches MapReduce qui traitent les données en parallèle en utilisant plusieurs machines virtuelles.**

Pour utiliser Hadoop sous Azure, vous devez d’abord demander à cette plateforme cloud de créer un cluster Hadoop en spécifiant le nombre de machines virtuelles dont vous avez besoin. La configuration d’un cluster Hadoop est une tâche ardue, nous vous conseillons donc de laisser Azure le faire. Lorsque vous avez fini d’utiliser le cluster, arrêtez-le. Vous n’avez pas besoin de payer pour des ressources informatiques que vous n’utilisez pas.

Une application Hadoop, généralement nommée *tâche*, utilise un modèle de programmation appelé *MapReduce*. Comme l’indique cette figure, la logique d’une tâche MapReduce est exécutée simultanément entre plusieurs machines virtuelles. En traitant les données en parallèle, Hadoop peut les analyser bien plus rapidement que dans des solutions incluant une seule machine.

Sous Azure, les données utilisées par une tâche MapReduce sont généralement stockées dans un stockage d’objets blob. Cependant, dans Hadoop, les tâches MapReduce prévoient un stockage des données dans le *HDFS (Hadoop Distributed File System, ou Système de Fichiers Distribués Hadoop)*. Par certains côtés, le HDFS est semblable au stockage d’objets blob : par exemple, il réplique les données entre plusieurs serveurs physiques. Plutôt que de dupliquer cette fonctionnalité, Hadoop sous Azure expose plutôt le stockage d’objets blob via l’API HDFS, comme l’indique cette figure. Tandis que la logique d’une tâche MapReduce pense qu’elle accède à des fichiers HDFS ordinaires, elle utilise en fait des données diffusées depuis les objets blob. Et pour prendre en charge les cas où plusieurs tâches sont exécutées sur les mêmes données, Hadoop sous Azure autorise également la copie de données depuis les objets blob en HDFS complet exécuté dans les machines virtuelles.

De nos jours, les tâches MapReduce sont couramment écrites en Java, ce qui est pris en charge par Hadoop sous Azure. Nous avons également ajouté une prise en charge pour la création de tâches MapReduce dans d’autres langages, notamment C#, F# et JavaScript. L’objectif est de faciliter l’accès à cette technologie de données volumineuses pour un plus grand nombre de développeurs.

Avec HDFS et MapReduce, Hadoop inclut d’autres technologies vous permettant d’analyser des données sans avoir à écrire une tâche MapReduce par vous-même. Par exemple, Pig est un langage de haut niveau conçu pour les analyses de données volumineuses, tandis que Hive offre un langage SQL nommé HiveQL. Pig et Hive génèrent directement des tâches MapReduce qui traitent des données HDFS, tout en restant simples pour leurs utilisateurs. Les deux sont livrés avec Hadoop sous Azure.

Microsoft fournit également un pilote HiveQL pour Excel. Avec un complément pour Excel, les analystes d’entreprise peuvent créer des requêtes HiveQL (et donc des tâches MapReduce) directement à partir d’Excel, puis traiter et virtualiser les résultats à l’aide de PowerPivot et d’autres outils Excel. Hadoop sous Azure inclut d’autres technologies, telles que les bibliothèques à apprentissage automatique Mahout, le système d’exploration de graphiques Pegasus, et bien d’autres encore.

Les analyses des données volumineuses sont importantes, Hadoop l’est donc aussi. En fournissant Hadoop en tant que service géré sous Azure, ainsi que des liens vers des outils familiers comme Excel, Microsoft a pour objectif de rendre cette technologie accessible au plus grand nombre d’utilisateurs.

Plus généralement, les données de toutes sortes sont importantes. C’est pourquoi Azure inclut une gamme d’options pour la gestion des données et les analyses marketing. Peu importe l’application que vous voulez créer : vous trouverez certainement quelque chose d’utile sur cette plateforme cloud.

[blobs]: ./media/cloud-storage/Data_01_Blobs.png
[SQLSvr-vm]: ./media/cloud-storage/Data_02_SQLSvrVM.png
[SQL-db]: ./media/cloud-storage/Data_03_SQLdb.png
[SQL-datasync]: ./media/cloud-storage/Data_04_SQLDataSync.png
[SQL-report]: ./media/cloud-storage/Data_05_SQLReporting.png
[SQL-tblstor]: ./media/cloud-storage/Data_06_TblStorage.png
[hadoop]: ./media/cloud-storage/Data_07_Hadoop.png

<!---HONumber=August15_HO6-->