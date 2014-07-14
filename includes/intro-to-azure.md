
# Présentation d'Azure

Azure est la plateforme d'application de Microsoft pour le cloud public. Vous pouvez utiliser cette plateforme de nombreuses façons. Par exemple, vous pouvez vous servir d'Azure pour générer une application Web qui exploite et stocke ses données dans le centre de données Azure. Vous pouvez utiliser Azure simplement pour stocker des données, alors que les applications qui utilisent ces données fonctionnent en local (en d'autres termes, hors du cloud public). Azure vous permet également de créer des machines virtuelles pour le développement ou les tests, ou encore pour exécuter SharePoint et d'autres applications. Azure vous aide à concevoir des applications hautement évolutives comptant de nombreux utilisateurs. La plateforme propose une large palette de services pour vous permettre de réaliser toutes ces tâches et bien d'autres encore.

Il vous faut toutefois connaître certaines bases avant de pouvoir donner vie à vos projets. Même si vous êtes novice en matière de cloud computing, cet article vous permettra de vous familiariser avec les concepts de base d'Azure. L'objectif est de vous donner les bases qui vous permettront de comprendre et d'utiliser cette plateforme de cloud.

## Sommaire

* [Composants d'Azure](#components)
* [Modèles d'exécution/Compute](#models)
* [Gestion des données](#data)
* [Mise en réseau](#networking)
* [Analyse décisionnelle](#analytics)
* [Messagerie](#messaging)
* [Mise en cache](#caching)
* [Identité](#identity)
* [Calculs complexes (HPC)](#HPC)
* [Média](#media)
* [Commerce](#commerce)
* [Kits de développement logiciel (SDK)](#sdk)
* [Mise en route](#start)

<h2><a id="components" ></a>Composants d'Azure</h2>


Pour comprendre tout ce qu'Azure peut vous apporter, il est utile de regrouper ses services en plusieurs catégories distinctes. La figure 1 vous en présente un exemple.

![Composants Azure](./media/intro-to-azure/IntroAzure1.png)   
 **Figure 1 : Azure fournit des services d'application accessibles via Internet, exécutés depuis les centres de données Azure.**

Pour bien démarrer avec Azure, il est important de connaître les bases de chacun de ses composants. Vous pouvez également consulter le [poster de présentation d'Azure][1] pour un aperçu visuel rapide. La couleur des cadres de la figure 1 correspond aux groupes du poster.

Le reste de cet article présente les différentes technologies affichées dans la figure. Découvrez les possibilités offertes par chacune d'elles et les meilleurs cas de figure pour les utiliser.

<h2><a id="models" ></a>Modèles d'exécution/Compute</h2>


La mission de base d'une plateforme de cloud est d'exécuter des applications. Azure propose quatre options différentes à cet effet : les machines virtuelles, les services cloud, les sites Web et Mobile Services. Chacun des modèles d'exécution d'Azure a son propre rôle à jouer.

Les machines virtuelles Azure fournissent un environnement informatique général à la demande. Les services cloud sont un choix idéal pour créer des applications évolutives et fiables, avec des coûts d'administration réduits. Les sites Web Azure proposent toute une gamme d'applications, de cadres et de modèles pour vous aider à créer de volumineuses applications Web évolutives, à mettre rapidement en place des sites Web, puis d'en gérer efficacement le développement, les tests et le fonctionnement. Azure Mobile Services accélère le développement d'applications pour les appareils mobiles, en offrant des solutions clé en main pour stocker des données sur le cloud, authentifier les utilisateurs et envoyer des notifications Push.

Vous pouvez utiliser ces technologies de façon séparée ou les combiner en fonction de vos besoins pour créer les meilleures bases pour votre application. Votre approche dépend des problèmes que vous cherchez à résoudre.

### Machines virtuelles

Il peut s'avérer très utile de créer une machine virtuelle à la demande, à partir d'une image standard comme depuis une image que vous fournissez. Ajoutez à cela la possibilité de payer cette machine virtuelle à la minute, uniquement lorsqu'elle est utilisée et tout devient encore plus pratique. Cette approche, communément appelée << Infrastructure as a Service >> (IaaS) est celle adoptée par les machines virtuelles Azure.

Pour créer une machine virtuelle, indiquez le disque dur virtuel à utiliser et la taille de la machine virtuelle. Vous payez ensuite selon la durée d'utilisation de la machine virtuelle. Les machines virtuelles Azure proposent une gamme de disques durs virtuels de stockage. Ils incluent des options fournies par Microsoft, telles que Windows Server 2008 R2, Windows Server 2012 et Windows Server 2008 R2 avec SQL Server, avec les images Linux fournies par les partenaires de Microsoft. Vous pouvez également créer des machines virtuelles à partir de vos propres disques durs virtuels et les ajouter à la galerie.

Quelle que soit la provenance de l'image, vous pouvez stocker toute modification de façon permanente pendant que la machine virtuelle est en cours d'exécution. Lors de la prochaine création d'une machine virtuelle à partir de ce disque dur virtuel, tout reprend là où vous vous étiez arrêté. Il est également possible de copier le disque dur virtuel en dehors d'Azure, puis de l'exécuter localement.

Il existe de nombreuses façons d'utiliser les machines virtuelles Azure. Vous pouvez les utiliser pour créer une plateforme économique de développement et de test, que vous pouvez fermer une fois votre utilisation terminée. Vous pouvez également choisir de créer et d'exécuter des applications utilisant le langage et la bibliothèque que vous préférez. Ces applications peuvent utiliser toutes les options de gestion proposées par Azure. Vous pouvez également exécuter SQL Server ou un autre système de gestion de bases de données sur une ou plusieurs machines virtuelles. Une autre possibilité est d'utiliser les machines virtuelles Azure comme extension de votre centre de données local pour exécuter SharePoint ou d'autres applications. Pour cette opération, il est possible de créer des domaines Windows dans le cloud en exécutant Active Directory sur les machines virtuelles Azure. Cette approche générique du cloud computing peut être utilisée pour gérer de nombreux problèmes. Toutes les possibilités vous sont offertes.

### Sites Web

Dans bien des cas, le cloud est utilisé pour exécuter des sites et des applications Web. Les machines virtuelles Azure permettent ceci, mais vous conservez la charge d'administrer une ou plusieurs machines virtuelles. Pourquoi ne pas simplement opter pour un site Web où vous n'avez pas à vous soucier des tâches d'administration ?

C'est exactement ce que proposent les sites Web Azure. Ce modèle d'exécution offre un environnement Web géré et utilise le portail de gestion Azure ainsi que des API. Vous pouvez transférer un site Web existant vers Azure sans le modifier ou en créer un directement dans le cloud. Une fois que le site Web fonctionne, vous pouvez ajouter ou supprimer des instances de façon dynamique sur les sites Web Azure pour
équilibrer les demandes. Les sites Web Azure proposent une option partagée, dans laquelle votre site est exécuté dans une machine virtuelle avec d'autres sites, et une option standard qui permet au site de fonctionner sur sa propre machine virtuelle. L'option standard vous permet également d'augmenter la taille (la puissance informatique) de vos instances si nécessaire.

Les sites Web Azure sont conçus pour répondre aux besoins des entreprises, des développeurs et des agences de design Web. Les entreprises y trouvent une solution simple à gérer, évolutive, hautement sécurisée et très disponible pour leurs sites Web. Pour le développement, .NET, PHP, Node.js et Python sont pris en charge, ainsi que la base de données SQL et MySQL (de ClearDB, un partenaire Microsoft) La prise en charge de plusieurs applications populaires, notamment WordPress, Joomla et Drupal est également intégrée. L'objectif est de fournir une plateforme économique, évolutive et utile au plus grand nombre pour la création de sites et d'applications Web dans le cloud public.

### Services cloud

Supposons que vous souhaitez générer une application cloud pouvant prendre en charge un grand nombre d'utilisateurs simultanés, en évitant au maximum les tâches d'administration tout en assurant une disponibilité permanente. Que vous soyez un fournisseur de logiciels déjà bien établi et qui, par exemple, décide de se lancer dans les solutions SaaS (software as a service) en transposant une version d'une de vos applications sur le cloud, ou que vous soyez une start-up créatrice d'une application au potentiel prometteur, si vous travaillez dans Azure, quel modèle d'exécution devez-vous choisir ?

Les sites Web Azure vous permettent de créer ce type d'applications Web, mais avec certaines contraintes. Par exemple, vous ne bénéficiez pas d'accès administrateur, ce qui signifie que vous ne pouvez pas installer de logiciel arbitrairement. Les machines virtuelles Azure vous offrent une grande flexibilité, notamment en termes d'accès administrateur. Vous pouvez donc vous en servir pour mettre en place une application hautement évolutive, mais vous devez vous-même gérer de nombreux aspects relatifs à la fiabilité et à l'administration. La solution idéale serait de pouvoir garder le contrôle dont vous avez besoin sans avoir à vous soucier de la plupart des tâches d'administration ou de fiabilité.

C'est exactement ce que proposent les services cloud Azure. Cette technologie est précisément conçue pour prendre en charge les applications évolutives, fiables et à faible niveau d'administration. On parle communément de << Platform as a Service >> (PaaS). Pour l'utiliser, créez une application avec la technologie de votre choix (C#, Java, PHP, Python, Node.js ou autre). Votre code est ensuite
exécuté dans des machines virtuelles (ou instances) fonctionnant avec une version de Windows Server.

Mais ces machines virtuelles sont différentes de celles que vous créez avec les machines virtuelles Azure. Il faut savoir qu'Azure les gère lui-même (par exemple, pour l'installation de correctifs du système d'exploitation, le déploiement automatique de nouvelles images avec correctifs, etc. Ceci implique que votre application ne doit pas entretenir d'état d'instances de rôle Web ou de travail. Elle doit rester dans le cadre d'une des options de gestion de données Azure présentées dans la section suivante.) Azure surveille également les machines virtuelles et les redémarre en cas d'échec.

Vous pouvez choisir entre deux rôles, tous deux basés sur Windows Server, lors de la création d'une instance. La différence principale entre ces deux options est qu'une instance de rôle Web exécute IIS alors que ce n'est pas le cas d'une instance de travail. Les deux sont toutefois gérées de façon identique et il est commun pour une application d'utiliser les deux. Par exemple, une instance de rôle Web peut accepter les requêtes d'utilisateurs, puis les transmettre à une instance de rôle de travail pour traitement. Pour faire évoluer votre application, vous pouvez demander à Azure de créer plus d'instances de chaque rôle ou d'arrêter des instances existantes. Et, de la même façon qu'avec les machines virtuelles Azure, la tarification se base uniquement sur la durée d'exécution de chaque instance Web ou de travail.

### Mobile Services

Si vous créez une application pour un appareil mobile, Azure Mobile Services accélère son développement en proposant des solutions clé en main pour stocker des données dans le cloud, authentifier les utilisateurs et envoyer des notifications Push.  
 Les bibliothèques clientes natives pour Android, iOS, HTML/JavaScript, Windows Phone et Windows Store permettent de générer des applications disponibles sur les principales plateformes pour mobiles. Une API REST ouverte et flexible vous permet également d'utiliser les fonctionnalités de données et d'authentification des services mobiles avec les applications sur la quasi-totalité des plateformes. Un service mobile unique peut être utilisé pour plusieurs applications clientes. Vous pouvez ainsi offrir une expérience utilisateur cohérente sur plusieurs appareils.

Les services mobiles vous permettent d'effectuer une gestion et un provisionnement simples des données stockées dans une base de données SQL, d'authentifier les utilisateurs via des fournisseurs d'identité reconnus, tels que les comptes Facebook, Twitter, Microsoft ou Google, et d'utiliser les services de notification pour envoyer des notifications Push à votre application. Avec un code côté serveur, vous pouvez facilement utiliser des options de stockage supplémentaires, telles que le stockage d'objets blob ou MongoDB, utiliser des modules de l'Azure Store, tels que SendGrid ou Pusher. Vous pouvez également utiliser d'autres services Azure, comme Service Bus ou les rôles de travail, ou même établir une connexion avec vos systèmes locaux. Un service peut évoluer en fonction de la popularité croissante d'une application. En outre, la surveillance et la journalisation sont prises en charge.

Même si vous pouvez certainement concevoir l'infrastructure d'une application mobile à l'aide des machines virtuelles, des services cloud ou des sites Web, en optant pour les services mobiles, vous gagnez un temps précieux car vous n'avez pas à créer les composants de service sous-jacents.

<h2><a id="data" ></a>Gestion des données</h2>


Les applications ont besoin de données, et chaque type d'application a besoin de données différentes. C'est pour cela qu'Azure propose différentes façons de stocker et de gérer les données.

Une de ces options a déjà été abordée : il s'agit de la possibilité d'exécuter SQL Server ou un autre système de gestion de bases de données
(SGBD) sur une machine virtuelle créée avec Azure. (Il est important de savoir que cette option n'est pas limitée aux systèmes relationnels. Vous pouvez également exécuter des technologies NoSQL telles que MongoDB et Cassandra.) Il est facile d'utiliser votre propre système de base de données, ce qui vous permet d'évoluer dans un environnement similaire à celui de votre propre centre de données. Toutefois, cela implique de gérer l'administration de ce SGBD. Pour tout simplifier, Azure propose trois options de gestion des données qui sont, en grande partie, administrées pour vous. La figure 2 vous présente les différentes possibilités.

![Gestion des données
Azure](./media/intro-to-azure/IntroAzure3.png)   
 **Figure 2 : pour la gestion des données, Azure propose du stockage relationnel, des tables NoSQL évolutives et un stockage binaire non structuré.**

Chacune de ces trois options répond à des besoins différents : stockage relationnel, accès rapide à des volumes importants de données entrées simples et stockage binaire non structuré. Dans ces trois cas, les données sont répliquées automatiquement sur trois ordinateurs différents dans un centre de données Azure afin d'offrir une haute disponibilité. Il faut également noter que ces trois options sont accessibles depuis les applications Azure comme les applications exécutées extérieurement, par exemple depuis votre centre de données local, votre ordinateur portable ou votre téléphone. Quel que soit le mode d'application, tous les services de gestion des données Azure sont facturés en fonction de l'utilisation, comprenant des frais par gigaoctet et par mois pour les données stockées.

### Base de données SQL

Pour le stockage relationnel, Azure fournit la base de données SQL. Auparavant nommé SQL Azure, la base de données SQL propose toutes les fonctionnalités essentielles d'un système de gestion de base de données relationnelle, notamment les transactions atomiques, l'accès simultané aux données par plusieurs utilisateurs tout en maintenant l'intégrité des données, les requêtes ANSI SQL et un modèle de programmation familier. Tout comme SQL Server, la base de données SQL est accessible via Entity Framework, ADO.NET, JDBC et les autres principales technologies d'accès aux données. Elle prend également en charge la majorité du langage T-SQL, avec les outils SQL Server tels que SQL Server Management Studio. Pour toute personne familière avec SQL Server (ou toute autre base de données relationnelle), l'utilisation de la base de données SQL ne devrait poser aucun problème.

Mais la base de données SQL n'est pas un simple système SGBD sur le cloud : c'est un service PaaS. Vous continuez de contrôler vos données et les personnes pouvant y accéder, mais la base de données SQL prend en charge tout le travail administratif de base (gestion de l'infrastructure matérielle, mise à jour automatique de la base de données et du système d'exploitation, etc.). La base de données SQL fournit également une option de fédération qui distribue les données sur plusieurs serveurs. Cette fonction est utile pour les applications fonctionnant avec d'importants volumes de données ou qui doivent étendre les demandes d'accès aux données sur plusieurs serveurs afin d'accroître les performances.

Si vous créez une application Azure (à l'aide d'un des trois modèles d'exécution) nécessitant un stockage relationnel, la base de données SQL peut s'avérer la meilleure solution. Les applications fonctionnant hors du cloud peuvent également utiliser ce service, même s'il existe de nombreuses autres possibilités. Par exemple, les données stockées dans la base de données SQL sont accessibles à partir de différents systèmes client, dont les ordinateurs de bureau, les ordinateurs portables, les tablettes et les téléphones. L'utilisation de la base de données SQL permet de réduire les temps d'arrêt grâce à la réplication qui assure une haute disponibilité intégrée.

### Tables

Supposons que vous souhaitiez créer une application Azure bénéficiant d'un accès rapide aux données saisies, en volumes importants, sans avoir besoin d'exécuter des requêtes SQL complexes sur ces données. Imaginons, par exemple, que vous créez une application cliente chargée de stocker les informations de profil pour chaque utilisateur. Votre application va être très populaire, vous devez donc avoir une grande capacité de données, mais vous allez vous contenter de les stocker, puis de les récupérer de façon simple. C'est précisément pour ce type d'utilisation qu'ont été conçues les tables Azure.

Ne vous laissez pas méprendre par le nom : cette technologie ne propose pas de stockage relationnel. En fait, c'est un exemple d'approche NoSQL nommée << stockage clé/valeur >>. À la place du stockage relationnel, les tables Azure laissent à une application la charge de stocker les propriétés de divers types : chaînes, nombres entiers, dates, etc. Une application peut ensuite récupérer un groupe de propriétés en fournissant une clé unique pour ce groupe. Même si les opérations complexes telles que les jonctions ne sont pas prises en charge, les tables permettent surtout un accès rapide aux données saisies. Elles sont également hautement évolutives : une seule table peut contenir jusqu'à un téraoctet de données. Comme elles sont plus simples, les tables sont également plus économiques à l'utilisation qu'un stockage relationnel avec la base de données SQL.

### Objets blob

La troisième option possible pour le stockage de données sont les objets blob Azure. Cette solution est conçue pour stocker des données binaires non structurées. Tout comme les tables, les objets blob offrent un stockage économique, un seul blob pouvant avoir une taille allant jusqu'à un téraoctet. Une application stockant, par exemple, des vidéos, des données de sauvegarde ou d'autres informations binaires peut utiliser les objets blob pour assurer un stockage simple et peu onéreux. Les applications Azure peuvent également utiliser les lecteurs Azure, qui permettent aux objets blob de proposer un stockage permanent pour un système de fichiers Windows monté sur une instance Azure. L'application voit des fichiers Windows normaux, mais le contenu est en fait stocké dans un objet blob.

<h2><a id="networking" ></a>Mise en réseau</h2>


Aujourd'hui, Azure est implanté dans plusieurs centres de données répartis dans tous les États-Unis, l'Europe et l'Asie. Lorsque vous exécutez une application ou que vous stockez des données, vous pouvez choisir d'utiliser un ou plusieurs de ces centres de données. Vous pouvez également vous y connecter de différentes façons :

* Vous pouvez utiliser le réseau virtuel Azure pour connecter votre propre réseau local vers un ensemble défini de machines virtuelles Azure.

* Si votre application Azure est exécutée dans plusieurs centres de données, vous pouvez utiliser Azure Traffic Manager pour orienter intelligemment les requêtes des utilisateurs sur plusieurs instances de l'application.

La figure 3 présente ces différentes options.

![Mise en réseau Azure](./media/intro-to-azure/IntroAzure4.png)  

 **Figure 3 : Azure permet la création d'un VPN sur le cloud et distribue de façon intelligente les requêtes utilisateur dans différents centres de données.**

### Réseau virtuel

Une approche utile pour utiliser un cloud public est de le considérer comme une extension de votre propre centre de données. Comme vous pouvez créer des machines virtuelles à la demande, puis les supprimer (et ainsi arrêter la facturation) lorsque vous n'en avez plus besoin, vous disposez de toute la puissance informatique, juste quand vous en avez besoin. Et puisque les machines virtuelles Azure vous permettent de créer des machines virtuelles exécutant SharePoint, Active Directory et d'autres logiciels familiers utilisés en local, cette approche fonctionne avec les applications que vous avez déjà.

Pour que ceci soit réellement utile, vos utilisateurs doivent pouvoir traiter ces applications comme si elles étaient exécutées sur votre propre centre de données. C'est exactement ce que propose le réseau virtuel Azure. Avec une passerelle VPN, un administrateur peut configurer un réseau privé virtuel (VPN) entre votre réseau local et un groupe défini de machines virtuelles fonctionnant sous Azure. Comme vous attribuez vos propres adresses IP v4 aux machines virtuelles du cloud, elles semblent se trouver sur votre réseau. Les utilisateurs de votre entreprise peuvent accéder aux applications contenues sur ces machines virtuelles de la même façon que si elles étaient exécutées en local.

### Traffic Manager

Une application comptant des utilisateurs concentrés dans une seule zone géographique peut fonctionner dans un centre de données Azure unique. Au contraire, une application avec des utilisateurs répartis dans le monde entier utilisera probablement plusieurs centres de données, voire tous. Ce second cas de figure pose problème. Comment diriger de façon intelligente les utilisateurs vers les instances d'application ? Dans la plupart des cas, vous souhaitez que chaque utilisateur accède au centre de données le plus proche de son emplacement, ce qui implique généralement une réduction du délai de réponse. Mais que se passe-t-il si l'instance de l'application est surchargée ou indisponible ? Dans ce cas, la solution idéale consiste à diriger la requête automatiquement vers un autre centre de données. C'est exactement le rôle d'Azure Traffic Manager.

Le propriétaire d'une application définit des règles qui spécifient comment les requêtes des utilisateurs doivent être dirigées vers les centres de données. Azure Traffic Manager se charge ensuite de les appliquer. Par exemple, les utilisateurs peuvent être dirigés par défaut vers le centre de données Azure le plus proche, mais réacheminés vers un autre centre de données lorsque le délai de réponse du premier centre dépasse celui d'autres centres de données. Pour les applications à distribution globale, avec de nombreux utilisateurs, il est utile de disposer d'un service intégré pour gérer de tels problèmes.

<h2><a id="analytics" ></a>Analyse décisionnelle</h2>


L'analyse de données représente une composante essentielle de l'utilisation des technologies de l'information par les entreprises. Une plateforme cloud propose un ensemble de ressources à la demande, facturées à l'utilisation, et assure une base solide pour ce type d'utilisation. Azure propose deux solutions d'analyse décisionnelle. La figure 4 vous présente les différentes possibilités.

![Analyse Azure](./media/intro-to-azure/IntroAzure5.png)   
 **Figure 4 : pour l'analyse décisionnelle, Azure fournit la prise en charge des données volumineuses et la génération des rapports associés.**

L'analyse des données peut prendre plusieurs formes et ces deux options présentent des différences. Il convient de les étudier séparément.

### Génération de rapports avec la base de données SQL

Une des façons les plus communes d'exploiter des données stockées est de créer des rapports basés sur ces données.

Exécuter SQL Server Reporting Services (SSRS) sur les machines virtuelles Azure vous permet de mettre en place des fonctionnalités de génération de rapports facilement accessibles au sein de votre application Azure. Vous pouvez créer des rapports avec des tables, des graphiques, des cartes, des jauges et d'autres éléments, dans divers formats tels que HTML, XML, PDF et Excel.

Vous pouvez également réaliser des analyses avec les données de la base de données SQL en utilisant vos outils décisionnels en local, notamment SSRS. Pour un client, la base de données SQL ressemble à SQL Server, les mêmes technologies fonctionnent sur les deux.

### HDInsight (Hadoop)

Depuis plusieurs années, le gros des analyses de données est réalisé sur des données relationnelles stockées dans des entrepôts de données formés avec un système SGBD relationnel. Ce type d'analyse décisionnelle demeure important et le restera encore longtemps. Mais que faire si les données que vous souhaitez analyser sont tellement volumineuses qu'elles ne peuvent pas être gérées par des bases de données relationnelles ? Et que se passe-t-il s'il ne s'agit pas de données relationnelles ? Il peut s'agir, entre autres exemples, de journaux de serveur dans un centre de données ou de données historiques d'événements enregistrés par des capteurs Dans ce cas, données importantes est synonyme de problèmes importants. Il vous faut trouver une autre approche.

La technologie dominante aujourd'hui pour l'analyse des données volumineuses est Hadoop. Il s'agit d'une technologie open source Apache qui stocke les données à l'aide du système HDFS (Hadoop Distributed File System), puis permet aux développeurs de créer des tâches MapReduce pour analyser les données. HDFS répartit les données sur plusieurs serveurs, puis exécute des parties de la tâche MapReduce sur chaque serveur, permettant un traitement parallèle des données.

HDInsight est le nom du service Azure basé sur Apache Hadoop. Comme l'indique la figure 4, HDInsight permet à HDFS de stocker des données sur le cluster et de les distribuer sur plusieurs machines virtuelles. Il diffuse également la logique d'une tâche MapReduce sur ces machines virtuelles. Tout comme avec une solution Hadoop locale, les données sont traitées localement. La logique et les données traitées résident dans la même machine virtuelle, avec une exploitation en parallèle pour renforcer les performances. HDInsight peut également stocker des données dans Azure Storage Vault qui utilise des objets blob. Utiliser ASV vous permet de réaliser des économies, car vous pouvez supprimer votre cluster HDInsight lorsque celui-ci n'est pas utilisé tout en conservant vos données dans le cloud.

HDInsight prend en charge d'autres composants de l'écosystème Hadoop, notamment Hive et Pig. Microsoft a également créé des composants qui facilitent le travail sur les données produites par HDInsight à l'aide d'outils répandus d'analyse décisionnelle, tels que l'adaptateur HiveOBC et Data Explorer, qui fonctionnent avec Excel.

<h2><a id="messaging" ></a>Messagerie</h2>


Quelle que soit son utilité, tout morceau de code a régulièrement besoin d'interagir avec d'autres morceaux de code. Dans certaines situations, une simple messagerie de base en file d'attente suffit. Dans d'autres, des interactions plus complexes sont nécessaires. Azure propose différentes façons de résoudre ces problèmes. La figure 5 vous présente les différentes possibilités.

![Messagerie Azure](./media/intro-to-azure/IntroAzure6.png)   
 **Figure 5 : pour connecter les applications, Azure fournit des connexions de type files d'attente, publication/abonnement et synchronisées via le cloud.**

### Files d'attente

La mise en file d'attente est une idée simple : Une application place un message dans une file d'attente afin qu'il soit ensuite lu par une autre application. Si votre application requiert juste ce service simple, les files d'attente Azure peuvent se révéler être le meilleur choix.

Aujourd'hui, une des utilisations les plus courantes des files d'attente est de permettre à une instance de rôle Web de communiquer avec une instance de rôle de travail au sein d'une même application de service cloud. Par exemple, supposons que vous créez une application de partage vidéo avec Azure. Celle-ci consiste en un code PHP exécuté dans un rôle Web qui permet à l'utilisateur de télécharger et de visionner des vidéos, associé à un rôle de travail implémenté en C# qui convertit les vidéos téléchargées en différents formats. Lorsqu'une instance de rôle Web reçoit une nouvelle vidéo d'un utilisateur, elle peut stocker la vidéo dans un objet blob, puis envoyer un message à un rôle de travail via une file d'attente pour indiquer où trouver la nouvelle vidéo. Une instance de rôle de travail, peu importe laquelle, consulte alors le message dans la file d'attente et effectue les conversions vidéo en arrière-plan. Structurer une application ainsi permet un traitement asynchrone et simplifie la mise à l'échelle, puisqu'il est possible de faire varier le nombre d'instances de rôles Web et de travail de façon indépendante.

### Service Bus

Qu'elles soient exécutées sur le cloud, dans votre centre de données, sur un appareil mobile ou autre, les applications doivent interagir. L'objectif d'Azure Service Bus est de laisser les applications effectuer à peu près partout les échanges de données.

Comme le montre la figure 5, Service Bus fournit un service de mise en file d'attente. Toutefois, ce service diffère des files d'attente présentées ci-dessus. Contrairement aux files d'attente Azure, par exemple, Service Bus propose des mécanismes de file d'attente (un à un) et de publication/d'abonnement. Avec le système de publication/d'abonnement, une application peut envoyer des messages vers une rubrique, tandis que d'autres applications peuvent s'abonner à cette dernière. Ceci permet de communiquer vers plusieurs applications depuis une source unique. Un même message est lu par plusieurs destinataires. La mise en file d'attente n'est d'ailleurs pas la seule option : Service Bus permet également la communication directe via son service de relais, offrant ainsi une méthode sécurisée d'interaction à travers les pare-feu. Les relais Service Bus permettent aux applications de communiquer en échangeant des messages via un point de terminaison hébergé dans le cloud, plutôt que localement.

Les applications qui communiquent via Service Bus peuvent être des applications Azure ou des logiciels exécutés sur une autre plateforme cloud. Il peut également s'agir d'applications exécutées hors cloud. On peut prendre l'exemple d'une compagnie aérienne qui implémente ses services de réservation sur des ordinateurs au sein de son propre centre de données. La compagnie aérienne doit rendre ces services accessibles à de nombreux clients (guichets d'enregistrement dans les aéroports, terminaux d'agences de réservation, voire téléphones des clients). Pour ce faire, il est possible d'utiliser Service Bus en créant des interactions souples entre les différentes applications.

<h2><a id="caching" ></a>Mise en cache</h2>


Les applications tendent à accéder toujours aux mêmes données. Une des façons d'améliorer les performances est de conserver une copie de ces données à proximité de l'application, minimisant ainsi le délai de récupération. Azure propose deux services pour cela : la mise en cache en mémoire des données utilisées par les applications Azure et un réseau de distribution de contenu (CDN) qui met en cache les données d'objets blob sur un disque plus proche des utilisateurs. La figure 6 présente ces deux options.

![Mise en cache Azure](./media/intro-to-azure/IntroAzure7.png)   
 **Figure 6 : une application Azure peut mettre en cache des données en mémoire. Des copies d'un objet blob peuvent être mises en cache sur des sites partout dans le monde.**

### Mise en cache

L'accès aux données stockées dans un des services de gestion de données Azure (base de données SQL, tables ou objets blob) est relativement rapide. Mais accéder à des données stockées en mémoire l'est encore plus. C'est pour cela que conserver une copie en mémoire des données fréquemment utilisées peut améliorer les performances des applications. La mise en cache en mémoire proposée par Azure vous permet cela.

Une application de services cloud peut stocker des données dans ce cache, puis les récupérer directement sans avoir besoin d'accéder au stockage permanent. Comme l'indique la figure 6, le cache peut être conservé dans les machines virtuelles de votre application ou fourni par des machines virtuelles exclusivement dédiées à la mise en cache. Dans les deux cas, le cache peut être réparti, avec les données contenues diffusées sur plusieurs machines virtuelles dans un centre de données Azure.

Par exemple, une application qui lit de façon répétée un catalogue produits peut tirer un avantage de ce type de mise en cache puisque les données nécessaires sont disponibles plus rapidement. Cette technologie prend également en charge le verrouillage, permettant une utilisation des données en lecture seule ou en lecture/écriture. Les applications ASP.NET peuvent également utiliser le service pour stocker des données de session en apportant une simple modification de configuration.

### CDN

Supposons que vous devez mettre à disposition d'utilisateurs du monde entier des données sous forme d'objets blob. Il peut s'agir d'une vidéo du dernier match de la Coupe du monde comme d'une mise à jour de pilote ou d'un livre électronique populaire. Stocker une copie des données dans plusieurs centres de données Azure va vous aider, mais ce ne sera probablement pas suffisant si de nombreux utilisateurs sont impliqués. Pour renforcer les performances, vous pouvez utiliser Azure CDN.

Le CDN comporte des dizaines de sites répartis dans le monde entier, chacun étant capable de stocker des données d'objets blob Azure. La première fois qu'un utilisateur accède à un objet blob donné, les informations contenues dans celui-ci sont copiées du centre de données Azure vers le stockage CDN de la zone géographique de l'utilisateur. Par la suite, c'est cette copie en cache dans le CDN de l'objet blob qui est utilisée lorsqu'un utilisateur de cette partie du monde souhaite accéder aux données. Nul besoin d'accéder au centre de données Azure le plus proche. Il en résulte un accès plus rapide aux données les plus consultées par les utilisateurs dans le monde entier.

<h2><a id="identity" ></a>Identité</h2>


La plupart des applications fonctionnent avec des identités. Par exemple, connaître l'identité d'un utilisateur permet à l'application de savoir comment elle doit interagir avec lui. Pour vous aider dans ce domaine, Microsoft propose Azure Active Directory.

Comme pour la plupart des services d'annuaire, Azure Active Directory stocke des informations à propos des utilisateurs et de leur entreprise. Le service permet aux utilisateurs de se connecter, puis leur attribue un jeton qui sera présenté aux applications pour prouver leur identité. Il permet de synchroniser les informations utilisateur avec Windows Server Active Directory exécuté sur votre réseau local. Même si les mécanismes et les formats de données utilisés par Azure Active Directory diffèrent de ceux utilisés dans Windows Server Active Directory, les fonctions assurées par les deux services sont relativement identiques.

Il est important de comprendre qu'Azure Active Directory est conçu en priorité pour les applications cloud. Il peut être utilisé par les applications exécutées sur Azure ou sur d'autres plateformes de cloud. Il est également utilisé par les propres applications cloud de Microsoft, telles qu'Office 365. Toutefois, si vous souhaitez étendre votre centre de données dans le cloud en utilisant les machines virtuelles Azure et le réseau virtuel Azure, Azure Active Directory ne constitue pas la bonne solution. Il est préférable d'utiliser Windows Server Active Directory exécuté sur des machines virtuelles dans le cloud, comme décrit précédemment.

Pour permettre aux applications d'accéder aux informations qu'il renferme, Azure Active Directory propose une API RESTful nommée Azure Active Directory Graph. Cette API permet aux applications exécutées sur tous types de plateformes d'accéder aux objets d'annuaire et aux relations qui les unissent. Par exemple, une application autorisée peut utiliser cette API pour obtenir des informations sur un utilisateur, le groupe auquel il appartient ou d'autres informations. Les applications peuvent également détecter les relations entre utilisateurs (leur schéma social) pour travailler de façon plus intelligente en prenant en compte les connexions entre les personnes.

Une autre fonction de ce service, le contrôle d'accès Azure Active Directory, permet à une application d'accepter plus facilement les informations d'identité provenant de Facebook, Twitter, Google, Windows Live ID et d'autres fournisseurs d'identité répandus. Plutôt que de faire reconnaître par l'application les différents formats et protocoles utilisés par chacun de ces fournisseurs, le contrôle d'accès les convertit tous dans un format commun unique. Il permet aussi à l'application d'accepter des identifiants provenant d'un ou de plusieurs domaines Active Directory. Par exemple, un fournisseur proposant une application SaaS peut utiliser le contrôle d'accès Azure Active Directory pour permettre une authentification unique (SSO) sur l'application à ses clients.

Les services d'annuaire sont une composante essentielle de l'informatique en local. Il n'est pas étonnant qu'ils le soient également sur le cloud.

<h2><a id="media" ></a>Média</h2>


La vidéo prend une part importante du trafic Internet aujourd'hui et cette proportion va continuer à croître. Mais fournir de la vidéo sur Internet n'est toutefois pas chose simple. Il existe de nombreuses variables, telles que l'algorithme d'encodage et la résolution d'affichage sur l'écran de l'utilisateur. Les demandes de vidéos sont irrégulières. Par exemple, un samedi soir, de nombreuses personnes peuvent décider de regarder un film en ligne.

En réponse à cette popularité, on peut logiquement penser que de plus en plus de nouvelles applications utiliseront la vidéo. Mais elles devront toutes résoudre des problèmes communs. Laisser chaque développeur trouver les réponses de son côté n'a aucun sens. La meilleure approche est de créer une plateforme qui propose des solutions communes utilisables par de nombreuses applications. Mettre en place cette plateforme sur le cloud présente certains avantages réels. Elle est alors hautement disponible, sur une base de paiement à l'utilisation, et permet de gérer les variations de demande auxquelles doivent souvent faire face les applications vidéo.

Azure Media Services a été développé pour répondre à ces problèmes. Il fournit un ensemble de composants de cloud pour simplifier le travail des personnes chargées de créer et d'exécuter des applications utilisant de la vidéo ou d'autres médias. La figure 7 vous présente cette technologie.

![Azure Media Services](./media/intro-to-azure/IntroAzure8.png)   
 **Figure 7 : Media Services est une plateforme pour les applications fournissant de la vidéo ou d'autres médias à des clients du monde entier.**

Comme le montre la figure, Media Services propose un ensemble de composants à destination des applications qui fonctionnent avec de la vidéo ou d'autres médias. Par exemple, un service d'ingestion de médias permet d'envoyer des vidéos vers Media Services (avec stockage sur des objets blob Azure), un composant d'encodage prenant en charge divers formats vidéo et audio, un dispositif de protection de contenu qui permet de gérer les droits numériques, un service permettant d'insérer des publicités dans un flux vidéo, des solutions de diffusion en continu, etc. Les partenaires Microsoft peuvent également fournir des composants pour la plateforme. Microsoft assure ensuite la distribution de ces composants et se charge de la facturation au nom des partenaires.

Les applications qui utilisent cette plateforme peuvent être exécutées sur Azure ou autre. Par exemple, une application pour une maison de production vidéo peut permettre à ses utilisateurs d'envoyer du contenu vers Media Services, puis le traiter de différentes façons. Autre exemple, un service de gestion de contenu basé sur le cloud fonctionnant sur Azure peut s'appuyer sur Media Services pour traiter et distribuer du contenu vidéo. Quel que soit son emplacement et sa fonction, chaque application choisit les composants qui lui sont utiles et y accède via des interfaces RESTful.

Pour distribuer ce qu'elle produit, une application peut utiliser le CDN Azure, un autre CDN ou envoyer des portions directement aux utilisateurs. Quelle que soit l'origine, les vidéos créées à l'aide de Media Services peuvent être utilisées par divers systèmes clients, notamment Windows, Macintosh, HTML 5, iOS, Android, Windows Phone, Flash et Silverlight. L'objectif est de simplifier la création d'applications multimédia modernes.

Pour un aperçu visuel du fonctionnement de Media Services, téléchargez le [poster Azure Media Services][2].

<h2><a id="HPC" ></a>Calculs complexes</h2>


Une des façons les plus avantageuses d'utiliser une plateforme cloud est pour les calculs complexes (HPC). L'essence d'HPC est d'exécuter du code sur de nombreuses machines à la fois. Sur Azure, cela se traduit par l'utilisation simultanée de nombreuses machines virtuelles, fonctionnant toutes en parallèle pour résoudre un problème. D'une certaine façon, ceci requiert de programmer une répartition du travail entre les différentes instances. Pour ce faire, Azure propose HPC Scheduler.

Ce composant fonctionne avec les applications HPC conçues pour utiliser l'interface de transfert de messages MPI standard. Les logiciels effectuant des analyses par éléments finis, par exemple une simulation d'accident de voiture, sont un exemple de ce type d'application, mais il en existe beaucoup d'autres. HPC Scheduler peut également être utilisé sur un très grand nombre d'applications parallèles, comme pour les simulations de type Monte Carlo. Quel que soit le problème à résoudre, la valeur fournie reste identique : HPC Scheduler gère le complexe problème de planification du travail informatique en parallèle sur de nombreuses machines virtuelles Azure. L'objectif est de simplifier la conception d'applications HPC exécutées sur le cloud.

<h2><a id="commerce" ></a>Commerce</h2>


La montée en puissance du SaaS (Software as a Service) transforme la façon dont les applications sont créées. Les modes de vente des applications évoluent également. Les applications SaaS résidant dans le cloud, il semble logique que les clients potentiels recherchent des solutions en ligne. Cette évolution concerne autant les données que les applications. Pourquoi les utilisateurs ne se tourneraient-ils pas vers le cloud pour des ensembles de données disponibles ? Microsoft répond à ces demandes avec l'[Azure Marketplace][3] et l'[Azure Store](/en-us/store/overview/), présentés dans la figure 8.

![Azure Commerce](./media/intro-to-azure/IntroAzure9.png)   
 **Figure 8 : l'Azure Marketplace et l'Azure Store vous permettent de rechercher et d'acheter des applications et ensembles de données commerciales d'Azure.**

La différence entre les deux est que le Marketplace se situe hors du portail de gestion Azure alors que le Store est accessible depuis le portail. Les clients potentiels peuvent effectuer leurs recherches d'applications Azure répondant à leurs besoins sur l'un ou l'autre, puis s'enregistrer pour utiliser l'application de leur choix directement via leur créateur ou via le Marketplace ou le Store. Les clients peuvent
également rechercher des ensembles de données commerciales, notamment des données démographiques, financières, géographiques et bien plus encore. Lorsque l'utilisateur trouve un élément répondant à ses besoins, il peut y accéder via le fournisseur, directement sur le MarketPlace ou le Store sur le Web ou encore, dans certains cas, dans le portail de gestion. Les applications peuvent également utiliser l'API de recherche Bing sur le Marketplace, pour accéder aux résultats des recherches Web.

<h2><a id="sdk" ></a>Kits de développement logiciel (SDK)</h2>


En 2008, la toute première version préliminaire d'Azure prenait en charge le développement uniquement en .NET. Aujourd'hui, vous pouvez créer des applications Azure dans la quasi-totalité des langages. Actuellement, Microsoft fournit des Kits de développement logiciel (SDK) spécifiques pour .NET, Java, PHP, Node.js, Ruby et Python. Il existe aussi un Kit de développement logiciel (SDK) Azure général qui assure une prise en charge de base pour tous les langages, tels que C++.

Ces Kits de développement logiciel (SDK) vous aident à créer, à déployer et à gérer vos applications Azure. Ils sont disponibles sur www.windowsazure.com ou GitHub et peuvent être utilisés avec Visual Studio et Eclipse. Azure propose également des outils en ligne de commande que les développeurs peuvent utiliser avec tous les éditeurs et les environnements de développement. Cela comprend les outils pour déployer les applications sur Azure depuis des systèmes Linux et Macintosh.

En plus de vous aider à concevoir des applications Azure, ces Kits de développement logiciel (SDK) fournissent des bibliothèques clientes qui vous permettent de créer des logiciels fonctionnant hors du cloud tout en utilisant les services Azure. Par exemple, vous pouvez créer une application exécutée chez un hébergeur et fonctionnant avec des objets blob Azure ou concevoir un outil qui déploie des applications Azure via l'interface de gestion d'Azure.

<h2><a id="start" ></a>Mise en route</h2>


Maintenant que vous connaissez les grandes lignes, la prochaine étape est de créer votre première application Azure. Choisissez votre langage, [téléchargez le Kit de développement logiciel (SDK) approprié](/en-us/downloads/) et vous n'avez plus qu'à vous lancer. Le cloud computing est la nouvelle norme. Adoptez-le dès maintenant.



[1]: http://www.microsoft.com/en-us/download/details.aspx?id=35473
[2]: http://www.microsoft.com/en-us/download/details.aspx?id=38195
[3]: http://datamarket.azure.com/