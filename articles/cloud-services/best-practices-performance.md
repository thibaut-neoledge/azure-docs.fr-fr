<properties 
	pageTitle="Meilleures pratiques pour les performances - Azure" 
	description="Learn about best practices for performance in Azure." 
	services="cloud-services, sql-database, storage, service-bus, virtual-network" 
	documentationCenter=".net" 
	authors="Rboucher" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="9/1/2014" 
	ms.author="robb"/>

# Meilleures pratiques pour les performances dans les applications Azure #

Ce guide propose des orientations normatives sur les meilleures pratiques et techniques à suivre pour optimiser les performances des applications Azure. 

Notez que les performances ne sont que l'un des nombreux avantages de l'utilisation d'Azure. Les recommandations de ce document sont centrées principalement sur les performances. Il existe d'autres scénarios où les performances sont moins déterminantes : par exemple, la possibilité de vous décharger de la gestion des équipements physiques sur Azure, ou la fonctionnalité de paiement à l'utilisation peuvent être particulièrement intéressantes. Ce document n'essaie pas d'évaluer les scénarios où les performances sont un élément de moindre importance. 

## Vue d'ensemble ##
 Les performances peuvent être définies comme [" la quantité de travail utile accompli par rapport au temps et aux ressources utilisés ".](http://go.microsoft.com/fwlink/?LinkId=252650) 


La définition présente deux facettes : les mesures et les ressources. Les mesures des performances sont des chiffres qui doivent être atteints pour répondre aux besoins métier. Elles incluent des éléments tels que le temps de réponse, le débit, la disponibilité, etc. Les performances incluent également le niveau d'utilisation des ressources requis pour atteindre un certain niveau de mesures de performances. Comme le coût est presque toujours un besoin métier et que les ressources coûtent de l'argent, les performances impliquent une utilisation des ressources aussi efficace que possible. 

### Cycle de vie des performances
Vous pouvez influer sur les performances à deux moments différents dans le cycle de vie d'une application :

- pendant la conception, où vous prenez les décisions architecturales fondamentales qui peuvent avoir une incidence sur les performances ; et
- au moment de l'exécution, où vous identifiez les goulots d'étranglement, effectuez la surveillance et les mesures, etc.

Les deux activités sont nécessaires. Ce livre blanc est consacré principalement à la phase de conception, car les erreurs architecturales sont plus difficiles à résoudre pendant l'exécution.

#### Modélisation d'application
Il est important d'élaborer un modèle des principaux scénarios client de votre application. Ici, " important " signifie " qui a le plus grand impact sur les performances ". L'identification de ces scénarios et des activités requises relatives à l'application vous permettra de réaliser des tests de validation technique.

#### Tests de validation technique ###

Les tests de performances complets, de bout en bout, sont une étape critique pendant la conception et le déploiement d'une application. Les applications Azure sont composées de différentes parties, qui peuvent inclure des composants personnalisés, ainsi que ceux fournis par Microsoft. Microsoft n'est pas en mesure de tester les performances pour toutes les combinaisons possibles de ces composants. Par conséquent, des tests de performances complets et réalisés correctement constituent une étape critique dans tout déploiement. 

Sur la base du modèle d'application que vous avez créé, vous devez ensuite réaliser les tests de validation technique de votre application dès que possible et la soumettre à un test de charge pour valider son architecture, afin de vous assurer que votre application répond aux exigences de performances en termes d'extensibilité et de latence. Il est extrêmement important de valider votre architecture et vos hypothèses initiales. En effet, vous n'avez pas envie de découvrir que votre application est incapable de supporter la charge prévue au moment de sa mise en service ! Visual Studio propose des fonctions pour effectuer les tests de charge. Pour une description de ces fonctions, consultez [Vue d'ensemble des tests de charge Visual Studio dans Azure](http://www.visualstudio.com/get-started/load-test-your-app-vs). 

### Différences à propos des performances dans Azure ###

Les améliorations les plus radicales qui soient réalisables sur les applications Azure en termes de performances viennent de la montée en charge et du partitionnement des ressources. Pour créer des applications évolutives dans Azure, vous devez exploiter les possibilités de montée en charge des ressources (bases de données SQL, stockage, nœuds de calcul, etc.) avec leur partitionnement physique : Ce partitionnement permet l'exécution parallèle des tâches de l'application. Il constitue donc une base pour des performances élevées, dans la mesure où Azure dispose des ressources d'un centre de données entier et gère le partitionnement physique à votre place. Pour atteindre ce niveau de performances générales, vous devez utiliser des modèles de conception de la montée en charge appropriés. 


Paradoxalement, bien qu'il soit possible d'atteindre ces performances élevées pour l'ensemble de l'application, chaque opération individuelle est moins performante dans Azure que son équivalent local, en raison de la latence accrue du réseau, de la fiabilité renforcée due aux opérations de basculement, etc. Mais le parallélisme, rendu possible par le bon usage des ressources de partitionnement, compense largement le " manque à gagner " au niveau des performances individuelles. 

### Activités de conception nécessaires ###

Certains facteurs de performances changent en fonction du scénario de votre application. Le chapitre suivant aborde l'extensibilité et le partitionnement des ressources, le choix d'emplacements appropriés pour les données et l'optimisation de l'utilisation des services Azure. 


Ensuite, nous aborderons les facteurs de performances qui se rapportent à toute application Azure : latence du réseau, connexions temporaires, etc. 

## Conception pour les performances dans un environnement cloud ##

Les domaines dépendants du scénario suivants doivent être pris en considération lors de la conception d'une application Azure ou de la migration d'une application locale vers Azure : 

- Montée en charge et partitionnement des ressources : il s'agit du mécanisme fondamental pour accéder au parallélisme et, donc, atteindre des performances élevées. 
* Architecture des donnée : quel type de stockage de données utiliser pour les différentes parties des données de votre application. 
* Optimisations des services Azure individuels 

Azure tire un avantage maximum en termes de performances de sa capacité à assurer la montée en charge et le partitionnement des ressources, qui permettent une parallélisation considérable des activités. C'est assez évident si l'on songe à une base de données SQL Azure extrêmement volumineuse, mais c'est vrai également de toute ressource qui peut devenir un goulot d'étranglement. 

Azure propose les solutions suivantes pour le stockage des données, et un choix pertinent aura un impact important sur les performances : 

* Base de données SQL Azure 
* Mise en cache Azure 
* Stockage de table Azure 
* Stockage des objets blob 
* Disques Azure 
* Files d'attente Azure 
* Messagerie répartie d'Azure Service Bus 
* Solutions de stockage des données volumineuses (" Big Data ") telles que Hadoop 

Étant donné que les caractéristiques détaillées peuvent varier, nous aborderons ces solutions dans le cadre des scénarios suivants : 

* Service cloud Azure utilisant une base de données SQL 
* Service cloud Azure utilisant abondamment des files d'attente de stockage 
* Site web Azure utilisant MySQL comme base de données principale 
* Applications " Big Data " 
* Applications avec une base de données principale MySQL 

### Scénario : base de données SQL dans un service cloud ###

La plupart des principes de bonne conception d'une base de données s'appliquent également à la base de données SQL Azure. Il existe un immense corpus de documents expliquant comment concevoir des schémas de base de données SQL Server ou SQL Azure efficaces. Les références relatives à la conception d'un schéma de base de données SQL sont les suivantes : 

* [Principes fondamentaux de conception et de modélisation d'une base de données](http://go.microsoft.com/fwlink/?LinkId=252675) 
* [Étapes de la conception d'une base de données](http://go.microsoft.com/fwlink/?LinkId=252676) 
* [Conception d'une base de données](http://go.microsoft.com/fwlink/?LinkId=252677) 

Deux activités clés liées à la conception sont différentes dans Azure : 

* le choix d'emplacements appropriés pour les données : cette activité peut impliquer le transfert de certaines données relationnelles vers des objets blob Azure ou des tables Azure, s'il y a lieu ; 
* la recherche d'une extensibilité maximale : décision relative au partitionnement et aux modalités de partitionnement de vos données. 

#### Architecture des données : déplacement des données hors d'une base de données SQL ####

Certaines données qui résident dans une instance SQL Server locale doivent être transférées vers un autre emplacement dans Azure. 

##### Transfert de données vers des objets blob Azure ####

Les données d'objets blob telles que les images ou les documents ne doivent pas être stockées dans une base de données SQL, mais dans le stockage d'objets blob Azure. Ces données sont souvent stockées dans une instance SQL Server locale, mais dans le cloud, l'utilisation du stockage d'objets blob est nettement plus économique. La procédure normale consisterait à remplacer les clés étrangères qui désignent les données d'objets blob par des identificateurs du stockage d'objets blob afin de préserver la possibilité de récupérer ces données, et les requêtes référençant les données devraient être modifiées. 

##### Transfert de tables SQL vers le stockage de table Azure #####

Lorsque vous prenez une décision relative à l'utilisation du stockage de table Azure, vous devez vous intéresser aux coûts et aux performances. Le stockage de table est beaucoup plus économique que le stockage des mêmes données dans une base de données SQL. Néanmoins, vous devez examiner attentivement dans quelle mesure les données utilisent les fonctions relationnelles de SQL, notamment les jonctions, le filtrage, les requêtes, etc. Si les données utilisent peu ces fonctions, vous avez intérêt à les stocker dans une table Azure. 

Un modèle de conception courant dans lequel le stockage de table peut être envisagé concerne une table comportant de nombreuses lignes, telle la table Customers dans l'exemple de base de données AdventureWorks, où un certain nombre de colonnes ne sont pas utilisées par une majorité de clients, mais seulement par un petit sous-ensemble de clients. Un modèle de conception courant consiste à séparer les colonnes dans une seconde table (nommée, par exemple, CustomerMiscellany), avec une relation 1-1 facultative entre Customer et la seconde table. Vous pouvez envisager de transférer la seconde table dans le stockage de table. Vous devez déterminer si l'opération est rentable étant donné la taille de la table et les modèles d'accès. 

Pour plus d'informations sur le stockage de table, consultez les pages suivantes : 

* [Stockage de table Microsoft Azure et base de données SQL Microsoft Azure - Comparaison et différences](http://msdn.microsoft.com/library/jj553018.aspx)
* [Considérations sur les performances du stockage de table Azure](http://go.microsoft.com/fwlink/?LinkId=252663) 
* [Base de données SQL et stockage de table Azure](http://go.microsoft.com/fwlink/?LinkId=252664) 
* [Amélioration des performances en regroupant les insertions dans le stockage de table Azure](http://go.microsoft.com/fwlink/?LinkID=252665), qui examine certains résultats de performances. 
* [Guide d'élasticité et de performances de la base de données SQL](http://go.microsoft.com/fwlink/?LinkId=221876) 

#### Partitionnement des données ####

Les données sont l'une des ressources les plus souvent partitionnées. Si vous créez un service cloud Azure, vous devez songer à utiliser le partitionnement intégré de la base de données SQL disponible par l'intermédiaire des fédérations. 

Pour une vue d'ensemble des fédérations de la base de données SQL, consultez [Fédérations dans Base de données SQL Azure](http://go.microsoft.com/fwlink/?LinkId=252668).  

##### Tâches de conception pour les fédérations SQL #####

L'utilisation de fédérations dans la base de données SQL pour un service cloud Azure exige quelques changements par rapport aux principes de conception classiques. Toutefois, la plupart des choses qui sont vraies pour une base de données SQL Server locale bien conçue constituent un point de départ nécessaire pour concevoir des fédérations dans la base de données SQL. Les deux principales tâches de conception consistent à déterminer : 

* ce sur quoi vous voulez établir la fédération ; et 

* où doivent être placées les tables non fédérées. 

Pour déterminer ce sur quoi vous voulez établir la fédération, vous devez examiner votre schéma de base de données et identifier les agrégations des tables liées. Un agrégat est un ensemble de tables, toutes liées par une relation un-à-plusieurs par l'intermédiaire de leurs clés étrangères, à l'exception de la racine de l'agrégat. 

Par exemple, dans l'exemple bien connu de base de données AdventureWorks, un agrégat possible est l'ensemble {Customer, Order, OrderLine et éventuellement quelques autres tables}. Un autre agrégat possible est {Supplier, Product, OrderLine, Order}. 

Chaque agrégat est un candidat pour la fédération. Vous devez déterminer à quel endroit vous prévoyez une augmentation de la taille et examiner également la charge de travail de votre application : les requêtes qui sont " bien adaptées " au schéma de fédération, c'est-à-dire celles qui n'ont pas besoin de données provenant de plusieurs membres de la fédération, fonctionneront correctement. Celles qui ne sont pas bien adaptées exigeront une logique dans la couche d'application, car la base de données SQL ne prend pas en charge actuellement les jonctions entre plusieurs bases de données. 

Pour afficher un exemple d'analyse de conception qui examine la base de données AdventureWorks pour la fédérer et qui présente étape par étape les éléments à considérer dans la conception, consultez la page [Approche " Scale-First " de la conception de base de données avec des fédérations : Partie 1 - Choix des fédérations et de la clé de fédération](http://go.microsoft.com/fwlink/?LinkId=252671). 

Une fois que vous avez déterminé quelles tables doivent être fédérées, vous devez ajouter la clé primaire de la table racine de l'agrégat en tant que colonne dans chacune des tables liées. 

Une fois que vous avez déterminé les tables sur lesquelles vous voulez établir la fédération, un autre problème concerne l'emplacement des tables de référence, ainsi que des autres objets de base de données. Pour une discussion approfondie sur ce sujet, consultez la page [Approche " Scale-First " de la conception de base de données avec des fédérations : Partie 2 - Annotation et déploiement du schéma pour les fédérations](http://go.microsoft.com/fwlink/?LinkId=252672). Les requêtes plus avancées sont décrites dans la [Partie 2](http://go.microsoft.com/fwlink/?LinkId=252673). 

                                            
##### Partitionnement autonome (" Do-It-Yourself ") #####

Il existe plusieurs exemples illustrant différentes méthodes de partitionnement des données. Si vous décidez de ne pas utiliser de fédérations pour partitionner votre instance de base de données SQL, vous devez choisir une méthode de partitionnement qui convient pour votre application. Voici quelques exemples : 

* Pour un compte complet, écrit avant le lancement des fédérations, consultez [Partitionnement avec la base de données SQL](http://go.microsoft.com/fwlink/?LinkId=252678). 
* [Bibliothèque de partitionnement de SQL Server et de la base de données SQL](http://go.microsoft.com/fwlink/?LinkId=252679) 

##### Partitionnement des autres ressources #####

Outre la base de données SQL, vous avez la possibilité de partitionner d'autres ressources. Vous pouvez, par exemple, partitionner des serveurs d'applications et les dédier à des bases de données particulières. Supposons que votre application contenait N serveurs d'applications et également N bases de données. Si chaque serveur d'applications est autorisé à accéder à chaque base de données, N x N connexions de base de données seront utilisées ; dans certains cas, la limite d'Azure risque d'être atteinte. Mais si vous limitez chaque serveur d'applications à quelques bases de données seulement, vous réduirez considérablement le nombre de connexions utilisées. 

Selon votre application, vous pourrez éventuellement appliquer un raisonnement similaire à d'autres ressources. 


#### Mise en cache ####

Le service de mise en cache Azure fournit une mémoire élastique distribuée pour mettre en cache des éléments tels que l'état de session ASP.net ou des valeurs couramment référencées des tables de référence de la base de données SQL. Comme les objets se trouvent dans la mémoire distribuée, les gains de performances possibles sont considérables. Dans la mesure où Azure gère l'infrastructure de mise en cache, les coûts de développement liés à sa mise en œuvre sont peu élevés. 

Prévoyez de fournir une capacité suffisante pour mettre en cache les objets auxquels vous accédez fréquemment. Dans la base de données SQL, il existe souvent des tables de référence utilisées pour convertir des codes numériques en chaînes de caractères descriptives plus longues. Ces tables incluent fréquemment des données telles que des noms de pays ou de ville, des valeurs de code postal valides, des noms de département au sein de votre entreprise, etc. Pour les petites tables, il peut être intéressant de stocker la table entière dans le cache ; pour les autres tables, vous pouvez vous contenter de stocker uniquement les valeurs les plus utilisées. Les gains de performances concernent les requêtes multi-jonctions impliquant ces données : pour chaque valeur trouvée dans le cache, plusieurs accès au disque sont économisés. Pour une introduction et une discussion sur les performances et la mise en cache dans Azure, consultez la [présentation du service caching Azure](http://go.microsoft.com/fwlink/?LinkId=252680). Un billet de blog plus récent sur le sujet est proposé dans [Mise en cache Microsoft #Azure - Considérations sur les performances](http://go.microsoft.com/fwlink/?LinkId=252681). 

#### Scénario : utilisation de la mise en file d'attente dans les applications Azure ####

Ce scénario concerne, par exemple, l'utilisation de StreamInsight pour remplir les files d'attente avec des messages qui seront traités ultérieurement. 

Les files d'attente Azure permettent de transmettre des messages, de découpler temporellement des sous-systèmes et de fournir l'équilibrage et le nivellement de la charge. 

Azure inclut deux technologies de file d'attente différentes : les files d'attente de stockage Azure et Service Bus. 

Les files d'attente de stockage Azure proposent, entre autres, des fonctionnalités telles que les files d'attente de grande taille et le suivi de la progression. Service Bus propose, entre autres, des fonctionnalités telles que la publication et l'abonnement, l'intégration complète à Windows Communication Foundation (" WCF "), la détection automatique des doublons, la livraison garantie de messages sur le principe du premier entré, premier sorti (" FIFO "). 

Pour une comparaison plus complète et détaillée des deux technologies, consultez [Files d'attente Microsoft Azure et files d'attente Microsoft Azure Service Bus - Comparaison et différences](http://go.microsoft.com/fwlink/?LinkId=252682). 

Pour une discussion sur les performances de Service Bus, consultez [Meilleures pratiques relatives aux améliorations de performances à l'aide de la messagerie répartie Service Bus](http://go.microsoft.com/fwlink/?LinkID=252683). 

#### Scénario : applications " Big Data " ####

On trouve souvent les " Big Data " (données volumineuses) comme sous-produit d'un autre système ou d'une autre application. En voici quelques exemples : 

* Journaux web 

* Autres fichiers de diagnostic, d'audit et de surveillance 

* Journaux sismiques d'une société pétrolière 

* Données sur les clics et autres informations laissées par les personnes qui naviguent sur Internet 

Les données " Big Data " peuvent être identifiées par les critères suivants : 

* Taille (en général, des centaines de téraoctets, voire plus) 

* Type : données non relationnelles, schéma variable, fichiers dans un système de fichiers 

Les données, en général, ne conviennent pas pour un traitement dans une base de données relationnelle. 

Il existe quatre principaux types de stockage de données non-SQL : 

* Clé-valeur 

* Document 

* Graph 

* Colonne-famille 

Azure fournit une prise en charge directe de Hadoop et permet également d'utiliser d'autres technologies. Pour obtenir des informations sur le service Azure HDInsight, consultez les pages suivantes : 

* [Données volumineuses (" Big Data ")](/fr-fr/solutions/big-data/) 
* [Azure HDInsight Service](/fr-fr/documentation/services/hdinsight/)
* [Prise en main d'Azure HDInsight Service](../hdinsight-get-started.md)

Pour une discussion sur les problèmes liés aux différentes méthodes de stockage noSQL, consultez les pages suivantes : 

* [Familiarisation avec NoSQL sur Azure](http://go.microsoft.com/fwlink/?LinkId=252729) 
* [Base de données orientée agrégat](http://go.microsoft.com/fwlink/?LinkID=252731)
* [Persistance polyglotte](http://go.microsoft.com/fwlink/?LinkId=252732) 

#### Autres optimisations des performances des services Azure individuels ####

De nombreux services individuels Azure possèdent des fonctionnalités et des paramètres susceptibles d'influer considérablement sur les performances, et doivent donc être analysés. 

##### Disques Azure #####

Vous pouvez simuler l'utilisation du disque dur d'une application existante par un lecteur Azure, qui est soutenu par un objet blob Windows et donc persistant à travers une défaillance individuelle de la machine. 

##### Stockage local #####

Bien qu'il ne soit pas persistant en cas de défaillance de la machine, le stockage local peut servir au stockage des informations qui font l'objet d'accès fréquents ou au stockage de résultats intermédiaires qui seront utilisés autre part. L'opération est rentable, car l'utilisation du stockage local n'entraîne aucun frais. 

##### Azure Access Control Service (ACS) #####

Les deux principaux facteurs ayant une incidence sur l'utilisation des ressources ACS, et donc sur les performances, sont la taille de jeton et le chiffrement. Pour une discussion plus approfondie, consultez [Instructions relatives aux performances du service ACS](http://go.microsoft.com/fwlink/?LinkId=252747). 

##### Sérialisation #####

La sérialisation n'est pas une partie évidente de l'optimisation des performances, mais la réduction du trafic réseau peut avoir son importance dans certains scénarios d'application. Pour un exemple illustrant la manière dont la taille de sérialisation peut varier selon le protocole, examinez les réductions de taille présentées dans [Applications web Azure et sérialisation](http://go.microsoft.com/fwlink/?LinkId=252749). 

Si la quantité de données déplacée pose un problème de performances, utilisez la plus petite sérialisation disponible. Au cas où les performances de la sérialisation ne seraient pas suffisantes, songez à utiliser des formats de sérialisation tiers personnalisés ou non-Microsoft. Comme toujours, les tests de validation technique sont décisifs. 


### Sites web Azure avec MySQL ###

Les liens suivants vous permettront d'obtenir des conseils relatifs aux performances pour MySQL : 

* Une recherche portant sur *performances* sur [http://mysql.com]( http://go.microsoft.com/fwlink/?LinkId=252775) permet d'accéder à de nombreuses ressources. 
* Les forums sur[ http://forums.mysql.com/list.php?24](http://go.microsoft.com/fwlink/?LinkId=252776) sont également des ressources à consulter. 



## Conception pour les systèmes partagés ##

Azure est conçu pour exécuter plusieurs applications simultanées, répliquées pour le basculement sur plusieurs machines. Cela influe sur les performances des applications de plusieurs manières : 

* Connexions temporaires 

* Limitations des ressources qui restreignent l'utilisation maximale 

* Latence du réseau 

* Emplacement physique des services 

Ces considérations s'appliquent à toutes les architectures d'application, car elles sont déterminées par l'infrastructure physique des centres de données Azure. Pour une discussion détaillée, consultez le [Guide d'élasticité et de performances de la base de données SQL](http://go.microsoft.com/fwlink/?LinkID=252666). 

### Latence du réseau ###

Azure est une plateforme de ressources partagées basée sur les services, et cela signifie que deux types de latences ou d'interruptions se produisent régulièrement. Le premier est le temps qu'il faut pour effectuer une demande et recevoir une réponse sur Internet. Comme ces requêtes peuvent traverser un nombre quelconque de routeurs avant de revenir au client, les dépassements du délai d'attente et les déconnexions sont plus fréquents que sur les réseaux locaux fixes. Le second est le temps qui est nécessaire à un système de ressources partagées tel qu'Azure pour créer des versions de sauvegarde des données à des fins de durabilité et pour remplacer et rediriger les demandes envoyées à des instances supprimées. Ces latences et ces défaillances sont importantes pour comprendre comment vous pouvez compenser ces défauts afin de satisfaire aux exigences de performances dans l'application. Des tests de charge dans le monde réel vous fourniront plus d'informations sur les latences que vous constatez. 

Tenez compte du fait qu'il y en aura probablement davantage, car le centre de données dans le cloud risque d'être physiquement plus éloigné que votre serveur local. 

### Emplacement physique des services ###

Si possible, regroupez différents nœuds ou couches d'application dans le même centre de données. Sinon, la latence et le coût du réseau seront supérieurs. 

Par exemple, placez l'application web dans le même centre de données que l'instance de base de données SQL à laquelle elle accède, plutôt que dans un centre de données différent ou dans un centre de données local. 

### Connexions temporaires ###

Votre application DOIT être capable de gérer les pertes de connexion. Celles-ci sont inévitables et intrinsèques à l'architecture cloud (voir, par exemple, les opérations comme le remplacement d'un nœud mort, le fractionnement d'un membre de fédération dans la base de données SQL, etc.). Le meilleur cadre pour gérer les pertes de connexion est actuellement [le bloc applicatif de gestion des erreurs temporaires](http://go.microsoft.com/fwlink/?LinkID=236901). 

### Limitation ###

Dans le monde des services, les ressources peuvent être très granulaires et vous payez uniquement pour ce que vous utilisez. Pour toutes les ressources, il existe cependant une garantie minimale de taille, de vitesse ou de débit (importante, par exemple, si une base de données d'une certaine taille ne vous suffit pas), et également, dans certains cas, des limites extérieures pour un service qui est important. Dans la mesure où les applications Azure s'exécutent dans un environnement partagé avec d'autres applications, Azure applique un certain nombre de limitations des ressources dont vous devez tenir compte. Si vous dépassez la valeur de limitation définie pour une ressource, une nouvelle demande concernant cette ressource donnera lieu à une exception. 

### Capacité physique ###

La planification de la capacité est un élément indispensable de la planification des performances : si vous ne parvenez pas à fournir suffisamment de stockage de différents types, votre application risque de ne pas s'exécuter. De même, des ressources mémoire ou processeur inappropriées peuvent ralentir considérablement l'exécution de votre application. 


Azure réduit de façon spectaculaire le travail nécessaire à la planification de la capacité, car de nombreuses activités anciennes, notamment l'obtention et la mise en service des ordinateurs, ont considérablement évolué. Dans Azure, la planification de la capacité n'est plus axée sur les éléments de calcul physiques, mais fonctionne à un niveau d'abstraction supérieur, en demandant plutôt quelles sont les quantités requises pour les éléments suivants : 

* Nœuds de calcul 
* Stockage d'objets blob 
* Stockage de tables 
* Files d'attente, etc. 

En outre, en raison de l'extensibilité d'Azure, les décisions initiales touchant à la capacité ne sont pas gravées dans le marbre : la montée en charge (ou la réduction de charge) des ressources Azure est relativement simple. Malgré tout, il est important de planifier avec précision la capacité, ce qui permet d'éviter une période d'essai ou une erreur concernant la capacité lors de la mise en service de l'application. 

Pour les applications dont les besoins en ressources fluctuent considérablement dans le temps, songez à utiliser [le bloc applicatif de mise à l'échelle automatique](http://go.microsoft.com/fwlink/?LinkId=252873). Ce bloc vous permet de définir des règles pour mettre à l'échelle (augmentation ou réduction d'échelle) les instances de rôle. Deux types de règles sont définis : 

* les règles de contrainte, qui définissent le nombre maximal/minimal d'instances par heure de la journée ; 

* les règles réactives, qui prennent effet lorsqu'une condition particulière telle qu'un certain pourcentage d'utilisation de l'UC se produit. 

Vous pouvez également définir des règles personnalisées. Pour plus d'informations, consultez [Bloc applicatif de mise à l'échelle automatique](http://go.microsoft.com/fwlink/?LinkId=252873). 



La planification de la capacité est une spécialité à part entière et ce document part du principe que vous n'êtes pas débutant. Pour une discussion détaillée sur la planification de la capacité dans Azure, consultez [Planification de la capacité des files d'attente et rubriques de Service Bus](http://go.microsoft.com/fwlink/?LinkId=252875).



## Surveillance et réglage des performances durant l'exécution ##

Aucune conception, aussi scrupuleuse soit-elle, ne peut garantir l'absence totale de problèmes de performances lors de l'exécution. Il est donc nécessaire de surveiller les performances de l'application de manière continue, afin de vérifier qu'elle atteint les mesures de performances requises et de remédier aux situations dans lesquelles elle ne parvient pas à atteindre ces mesures. Même lorsqu'elles sont bien conçues, les applications sont sujettes à des événements imprévus tels qu'une croissance exponentielle de l'utilisation ou d'éventuelles modifications apportées à l'environnement d'exécution, qui peuvent entraîner des problèmes de performances nécessitant un réglage. Identifier et résoudre les goulots d'étranglement constitue souvent une partie importante du processus. 


Être capable de résoudre les problèmes de performances lors de l'exécution nécessite un travail en amont pour intégrer la journalisation et une gestion correcte des exceptions, de telle sorte que le dépannage soit possible chaque fois que des problèmes surviennent. Pour un traitement complet de ce domaine, consultez [Meilleures pratiques de dépannage pour développer des applications Azure](http://go.microsoft.com/fwlink/?LinkID=252876). 

Des outils sont disponibles pour surveiller les performances continues de chaque service Azure. En outre, des fonctions de journalisation, fournissant les informations détaillées requises pour dépanner et résoudre les problèmes de performances, doivent être intégrées aux applications. 

### Base de données SQL ###

Notez que le Générateur de profils SQL n'est pas disponible actuellement dans Azure. Il existe plusieurs solutions de contournement pour obtenir les informations sur les performances requises. Une alternative pendant le développement consiste à réaliser des tests initiaux dans une version locale de la base de données où le Générateur de profils SQL est disponible. 

Vous pouvez également utiliser la commande Transact-SQL SET STATISTICS et vous servir de SQL Server Management Studio pour afficher le plan d'exécution généré par une requête, dans la mesure où le codage de requêtes efficaces constitue un élément clé pour les performances. Pour une discussion détaillée et une explication étape par étape de la marche à suivre, consultez [Compréhension des performances dans la base de données SQL](http://go.microsoft.com/fwlink/?LinkId=252877). Une autre approche intéressante est décrite à la page Analyse des performances entre la [base de données SQL et SQL Server local](http://go.microsoft.com/fwlink/?LinkId=252878). 

Les deux rubriques suivantes traitent des vues de gestion dynamique : 

* [Contrôle de la base de données SQL à l'aide de vues de gestion dynamique](http://go.microsoft.com/fwlink/?LinkId=236195) 
* [Vues de gestion dynamique de la base de données SQL utiles pour l'analyse en l'absence du Générateur de profils SQL](http://go.microsoft.com/fwlink/?LinkId=252879) 

### Ressources et outils d'analyse ###

Un certain nombre d'outils tiers non-Microsoft sont disponibles pour analyser les performances d'Azure : 

- [Cerebrata](http://go.microsoft.com/fwlink/?LinkId=252880) 
- [Tests de performances de SQL Server et de la base de données SQL : Enzo SQL Baseline](http://enzosqlbaseline.codeplex.com/) 

Autres ressources 

* [Guide d'élasticité et de performances de la base de données SQL](http://go.microsoft.com/fwlink/?LinkID=252666) 
* [Base de données SQL](http://go.microsoft.com/fwlink/?LinkId=246930) 
* [Stockage](http://go.microsoft.com/fwlink/?LinkId=246933) 
* [Mise en réseau](http://go.microsoft.com/fwlink/?LinkId=252882) 
* [Service Bus](http://go.microsoft.com/fwlink/?LinkId=246934) 
* [Planification Azure - Guide postdécision pour intégrer Azure dans votre environnement](http://go.microsoft.com/fwlink/?LinkId=252884) 

<!--HONumber=35.2-->

<!--HONumber=46--> 
 