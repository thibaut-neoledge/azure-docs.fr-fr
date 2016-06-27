<properties
   pageTitle="Guide technique de la résilience Azure pour la récupération après la panne d’une région Azure | Microsoft Azure"
   description="Articles dédiés à la compréhension et à la conception d’applications à tolérance de pannes robustes et hautement disponibles, ainsi qu’à la planification de la récupération d’urgence"
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="hongfeig"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/13/2016"
   ms.author="patw;jroth;aglick"/>

#Guide technique de la résilience Azure - Récupération d’une interruption de service à l’échelle de la région

Azure est divisé physiquement et logiquement en unités appelées régions. Une région se compose d’un ou plusieurs centres de données très proches les uns des autres. Au moment de la rédaction de cet article, Azure dispose de vingt-quatre régions dans le monde.

Exceptionnellement, il est possible que les sites de toute une région deviennent inaccessibles, par exemple en raison de défaillances au niveau du réseau ou en raison d’une catastrophe naturelle. Cette section présente les fonctionnalités d’Azure permettant de créer des applications distribuées entre les régions. Cette distribution permet de minimiser le risque qu’une défaillance dans une région affecte d’autres régions.

##Services cloud

###Gestion des ressources

Vous pouvez distribuer des instances de calcul entre les régions en créant un service cloud distinct dans chaque région cible et en publiant le package de déploiement pour chaque service cloud. Toutefois, notez que la répartition du trafic entre les services cloud dans différentes régions doit être effectuée par le développeur de l’application ou par un service de gestion du trafic.

Lors de la planification de la capacité, il est important de déterminer à l’avance le nombre d’instances de rôle de réserve à déployer pour la récupération d’urgence. Un déploiement secondaire à pleine échelle permet de garantir que la capacité est déjà disponible au moment voulu ; toutefois, le coût est alors doublé. Un modèle courant consiste à disposer d’un petit déploiement secondaire suffisamment grand pour exécuter les services critiques. Ce petit déploiement secondaire vous permettra de réserver de la capacité et de tester la configuration de l’environnement secondaire.

>[AZURE.NOTE]Le quota d’abonnements n’est pas une garantie de capacité. Il constitue simplement une limite de crédit. Pour garantir la capacité, le nombre requis de rôles doit être défini dans le modèle de service et les rôles doivent être déployés.

###Équilibrage de la charge.

Pour équilibrer la charge de trafic entre les régions, il est nécessaire d’utiliser une solution de gestion du trafic. Azure fournit [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/). Vous pouvez également utiliser des services tiers qui offrent des fonctionnalités de gestion de trafic similaires.

###Stratégies

De nombreuses autres stratégies sont disponibles pour implémenter un système de calcul distribué entre les régions. Celles-ci doivent être adaptées aux exigences professionnelles et aux circonstances de la demande. À un niveau élevé, les approches peuvent être divisées selon les catégories suivantes :

  * __Redéploiement après incident__ : dans cette approche, l’application est redéployée de toutes pièces au moment de l’incident. Cette stratégie est appropriée pour les applications non critiques qui ne nécessitent pas une garantie de délai de récupération.

  * __Rechange semi-automatique (actif/passif)__ : un service hébergé secondaire est créé dans une autre région et les rôles sont déployés pour assurer la capacité minimale ; toutefois, les rôles ne reçoivent pas de trafic de production. Cette approche est utile pour les applications qui n’ont pas été conçues pour distribuer le trafic entre les régions.

  * __Échange à chaud (actif)__ : l’application est conçue pour recevoir la charge de production dans plusieurs régions. Les services cloud de chaque région peuvent être configurés pour une capacité plus élevée que celle nécessaire à la récupération d’urgence. La taille des services cloud peut également augmenter selon les besoins au moment de l’incident et du basculement. Cette approche nécessite un investissement important en matière de conception des applications mais elle présente des avantages significatifs, comme par exemple un délai de récupération rapide et garanti, un test continu de tous les emplacements de récupération et une utilisation efficace de la capacité.

Ce document ne fournit pas de description complète de la conception distribuée. Pour plus d’informations, consultez [Récupération d’urgence et haute disponibilité des applications développées sur Microsoft Azure](https://aka.ms/drtechguide).

##Machines virtuelles

La récupération des machines virtuelles Infrastructure as a Service (IaaS) est similaire à la récupération de calcul Platform as a Service (PaaS) à bien des égards. Il existe toutefois des différences importantes en raison du fait qu’une machine virtuelle IaaS est composée de la machine virtuelle et du disque de machine virtuelle.

  * __Utilisez Azure Backup pour créer des sauvegardes cohérentes des applications dans différentes régions__. [Azure Backup](https://azure.microsoft.com/services/backup/) permet aux clients de créer des sauvegardes cohérentes des applications sur plusieurs disques de machine virtuelle et de prendre en charge la réplication des sauvegardes entre différentes régions. Vous pouvez effectuer cette opération en choisissant de géorépliquer le coffre de sauvegarde au moment de la création. Veuillez noter que la réplication du coffre de sauvegarde doit être configurée au moment de la création. Elle ne peut pas être définie ultérieurement. Si une région est perdue, Microsoft met les sauvegardes à disposition des clients. Les clients seront en mesure d’effectuer une restauration à partir des points de restauration configurés.

  * __Séparez le disque de données du disque du système d’exploitation__. Pour les machines virtuelles IaaS, il est important de garder en mémoire que vous ne pouvez pas modifier le disque du système d’exploitation sans recréer la machine virtuelle. Ce n’est pas un problème si votre stratégie de récupération après un incident est le redéploiement. En revanche, cette méthode peut poser problème si vous utilisez l’approche de rechange semi-automatique pour réserver de la capacité. Pour implémenter correctement cette stratégie, vous devez disposer du disque du système d’exploitation déployé aux emplacements principaux et secondaires et les données d’application doivent être stockées sur un lecteur distinct. Si possible, utilisez une configuration de système d’exploitation standard qui peut être fournie aux deux emplacements. Après un basculement, vous devez associer le lecteur de données à vos machines virtuelles IaaS existantes dans le contrôleur de domaine secondaire. Utilisez AzCopy pour copier des instantanés du ou des disques de données sur un site distant.

  * __Ayez conscience des problèmes potentiels de cohérence après un basculement géographique de plusieurs disques de machines virtuelles__. Les disques de machine virtuelle sont implémentés en tant qu’objets blob Azure Storage et ont les mêmes caractéristiques de géoréplication. Si [Azure Backup](https://azure.microsoft.com/services/backup/) n’est pas utilisé, il n’existe aucune garantie de cohérence entre les disques, étant donné que la géoréplication est asynchrone et que la réplication se fait de manière indépendante. Chaque disque de machine virtuelle se trouve dans un état de cohérence en cas d’incident après un basculement géographique, mais cette cohérence n’est pas assurée entre les disques. Dans certains cas, cela peut entraîner des problèmes (par exemple, en cas d’entrelacement de disques).

##Storage

###Récupération à l’aide du stockage géo-redondant des objets blob, des tables, des files d’attente et du stockage sur disque de machine virtuelle

Dans Azure, les objets blob, tables, files d’attente et disques de machine virtuelle sont tous géorépliqués par défaut. Cela s’appelle le stockage géo-redondant (GRS). GRS réplique les données de stockage dans un centre de données couplé, situé à des centaines de kilomètres, dans une région géographique donnée. GRS est conçu pour fournir une durabilité supplémentaire en cas d’incident grave dans le centre de données. Microsoft contrôle quand un basculement a lieu et le basculement est réservé aux incidents majeurs pour lesquels on considère qu’il est impossible de restaurer l’emplacement principal d’origine dans un délai raisonnable. Dans certains cas, cela peut prendre plusieurs jours. Les données sont généralement répliquées en quelques minutes, même si l’intervalle de synchronisation n’est pas encore couvert par un contrat de niveau de service.

En cas de basculement géographique, il n’y aura aucune modification de l’accès du compte (l’URL et la clé du compte ne sont pas modifiées). Toutefois, le compte de stockage sera dans une région différente après le basculement. Cela peut affecter les applications qui nécessitent une affinité régionale avec leur compte de stockage. Même pour les services et applications qui ne nécessitent pas un compte de stockage dans le même centre de données, les frais de latence et de bande passante entre centres de données peuvent être l’une des bonnes raisons pour lesquelles déplacer temporairement le trafic vers la région de basculement. Cela peut jouer un rôle important dans la stratégie globale de récupération d’urgence.

Outre le basculement automatique offert par GRS, Azure propose un service qui vous donne un accès en lecture à la copie de vos données à l’emplacement de stockage secondaire. Il s’agit du stockage géo-redondant avec accès en lecture (RA-GRS).

Pour plus d’informations sur le stockage GRS et RA-GRS, consultez [Réplication Azure Storage](../storage/storage-redundancy.md).

###Mappages de régions de géoréplication :

Il est important de savoir où vos données sont géorépliquées afin de savoir où déployer les autres instances de vos données qui nécessitent une affinité régionale avec votre stockage. Le tableau suivant montre les paires d’emplacements primaires et secondaires :

[AZURE.INCLUDE [paired-region-list](../../includes/paired-region-list.md)]

###Tarif de géoréplication :

La géo-réplication est incluse dans le tarif actuel d’Azure Storage. Il s’agit du stockage géo-redondant (GRS, Geo-Redundant Storage). Si vous ne souhaitez pas que vos données soient géo-répliquées, vous pouvez désactiver la géo-réplication pour votre compte. Il s’agit du stockage localement redondant, qui est facturé à un prix réduit par rapport au GRS.

###Déterminer si un géo-basculement s’est produit

Si un basculement géographique se produit, il est publié dans le [tableau de bord d’état du service Azure](https://azure.microsoft.com/status/). Toutefois, les applications peuvent implémenter un moyen automatisé pour détecter ces basculements géographiques en analysant la région géographique de leur compte de stockage. Cela peut servir à déclencher d’autres opérations de récupération telles que l’activation des ressources de calcul dans la région géographique vers laquelle leur stockage a été déplacé. Vous pouvez effectuer ce type de requête à partir de l’API Gestion des services à l’aide de l’option [d’obtention des propriétés du compte de stockage](https://msdn.microsoft.com/library/ee460802.aspx). Les propriétés pertinentes sont :

    <GeoPrimaryRegion>primary-region</GeoPrimaryRegion>
    <StatusOfPrimary>[Available|Unavailable]</StatusOfPrimary>
    <LastGeoFailoverTime>DateTime</LastGeoFailoverTime>
    <GeoSecondaryRegion>secondary-region</GeoSecondaryRegion>
    <StatusOfSecondary>[Available|Unavailable]</StatusOfSecondary>

###Disques de machine virtuelle et basculement géographique

Comme expliqué dans la section sur les disques de machine virtuelle, il n’existe aucune garantie de la cohérence des données entre les disques de machine virtuelle après un basculement. Pour assurer l’exactitude des sauvegardes, il est nécessaire d’utiliser un logiciel de sauvegarde tel que Data Protection Manager pour sauvegarder et restaurer les données d’application.

##Base de données

###Base de données SQL

La Base de données SQL Azure fournit deux types de restauration : la géo-restauration et la géo-réplication active.

####Restauration géographique

La [géo-restauration](../sql-database/sql-database-geo-restore.md) est également disponible avec les bases de données De base, Standard et Premium. Elle fournit l’option de récupération par défaut lorsque la base de données est indisponible en raison d’un incident dans la région où la base de données est hébergée. À l’image de la limite de restauration dans le temps, la géo-restauration s’appuie sur les sauvegardes de base de données dans le stockage Azure géo-redondant. Elle restaure à partir de la copie de sauvegarde géorépliquée et est par conséquent résistante aux pannes de stockage dans la région primaire. Pour plus d’informations, consultez [Récupération suite à une indisponibilité de service](../sql-database/sql-database-disaster-recovery.md).

####Géo-réplication active

La [géo-réplication active](../sql-database/sql-database-geo-replication-overview.md) est disponible avec tous les niveaux de bases de données. Elle est conçue pour les applications qui ont des exigences de récupération plus agressives que celles proposées par la géo-restauration. À l'aide de la géo-réplication active, vous pouvez créer jusqu'à quatre répliques secondaires sur des serveurs dans différentes régions. Vous pouvez lancer le basculement sur n’importe quelle base de données secondaire. En outre, la géo-réplication active peut être utilisée pour prendre en charge les scénarios de mise à niveau ou de déplacement d'application, ainsi que l'équilibrage de charge pour les charges de travail en lecture seule. Pour plus d’informations sur la [configuration de la géoréplication](../sql-database/sql-database-geo-replication-portal.md) et le [basculement vers la base de données secondaire](../sql-database/sql-database-geo-replication-failover-portal.md), consultez [Conception pour la continuité des activités](../sql-database/sql-database-business-continuity-design.md). Consultez [Mise à niveau de l'application sans interruption de service](../sql-database/sql-database-business-continuity-application-upgrade.md) pour plus d'informations sur la mise en œuvre de la mise à niveau de l'application sans interruption.

###SQL Server sur Virtual Machines

Plusieurs options sont disponibles pour la récupération et la haute disponibilité de SQL Server 2012 (et version ultérieure) sur Azure Virtual Machines. Pour plus d’informations, consultez [Haute disponibilité et récupération d’urgence pour SQL Server dans Azure Virtual Machines](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

##Autres services de plateforme Azure

Lorsque vous tentez d’exécuter votre service cloud dans plusieurs régions Azure, vous devez prendre en compte les implications pour chacune de vos dépendances. Dans les sections suivantes, les instructions spécifiques au service partent du principe que vous devez utiliser le même service Azure dans un autre centre de données Azure. Cela nécessite d’effectuer des tâches de configuration et de réplication des données.

>[AZURE.NOTE]Dans certains cas, ces étapes peuvent aider à limiter l’interruption à un service spécifique plutôt qu’à l’ensemble d’un centre de données. Du point de vue de l’application, l’interruption d’un service donné peut être tout aussi restrictive et nécessiterait la migration temporaire du service dans une autre région Azure.

###Service Bus

Azure Service Bus utilise un espace de noms unique qui ne couvre pas les régions Azure. La première exigence consiste donc à configurer les espaces de noms du Service Bus nécessaires dans l’autre région. Toutefois, il faut également réfléchir à la durabilité des messages mis en file d’attente. Il existe plusieurs stratégies de réplication des messages entre les régions Azure. Pour plus d’informations sur ces stratégies de réplication et d’autres stratégies de récupération d’urgence, consultez [Meilleures pratiques pour protéger les applications contre les pannes de Service Bus et les sinistres](../service-bus/service-bus-outages-disasters.md). Pour d’autres considérations relatives à la disponibilité, consultez la section [Service Bus (Disponibilité)](./resiliency-technical-guidance-recovery-local-failures.md#service-bus).

###Applications Web

Pour migrer une application web Azure vers une région Azure secondaire, vous devez disposer d’une sauvegarde du site web disponible pour la publication. Si la panne ne concerne pas l’ensemble du centre de données Azure, vous pouvez utiliser un FTP pour télécharger une sauvegarde récente du contenu du site. Créez ensuite une nouvelle application web dans l’autre région, sauf si vous l’avez fait précédemment pour réserver de la capacité. Publiez le site dans la nouvelle région et apportez les modifications nécessaires à la configuration. Ces modifications peuvent inclure des chaînes de connexion de base de données ou d’autres paramètres régionaux. Si nécessaire, ajoutez le certificat SSL du site et modifiez l’enregistrement DNS CNAME afin que le nom de domaine personnalisé pointe vers l’URL de l’application web Azure redéployée.

###Services mobiles

Dans la région Azure secondaire, créez un service mobile de sauvegarde pour votre application. Restaurez également la base de données SQL Azure dans une autre région. Utilisez ensuite les outils de ligne de commande Azure pour déplacer le service mobile vers l’autre région. Enfin, configurez le service mobile pour utiliser la base de données restaurée. Pour plus d’informations sur ce processus, consultez [Récupération de votre service mobile en cas d’incident](../mobile-services/mobile-services-disaster-recovery.md). Pour d’autres considérations relatives à la disponibilité, consultez la section [Mobile Services (disponibilité)](./resiliency-technical-guidance-recovery-local-failures.md#mobile-services).

###HDInsight

Par défaut, les données associées à HDInsight sont stockées dans Azure Blob Storage. HDInsight nécessite qu’un cluster Hadoop traitant des tâches MapReduce se trouve dans la même région que le compte de stockage qui contient les données en cours d’analyse. Si vous utilisez la fonctionnalité de géo-réplication disponible dans Azure Storage, vous pouvez accéder à vos données dans la région secondaire dans laquelle les données ont été répliquées si pour une raison quelconque la région principale n’est plus disponible. Vous pouvez créer un nouveau cluster Hadoop dans la région où les données ont été répliquées et poursuivre leur traitement. Pour d’autres considérations relatives à la disponibilité, consultez la section [HDInsight (Disponibilité)](./resiliency-technical-guidance-recovery-local-failures.md#hdinsight).

###SQL Reporting

À ce stade, la récupération après la perte d’une région Azure requiert plusieurs instances SQL Reporting dans différentes régions Azure. Ces instances SQL Reporting doivent accéder aux mêmes données et ces dernières doivent avoir leur propre plan de récupération en cas d’incident. Vous pouvez également conserver des copies de sauvegarde externes du fichier RDL pour chaque rapport.

###Media Services

Azure Media Services adopte une approche de récupération différente pour l’encodage et la diffusion en continu. En règle générale, la diffusion en continu est plus critique pendant une panne régionale. Pour vous préparer à cette éventualité, vous devez disposer d’un compte Media Services dans deux régions Azure différentes. Le contenu encodé doit se trouver dans les deux régions. En cas de panne, vous pouvez rediriger le trafic de diffusion en continu vers l’autre région. L’encodage peut être effectué dans n’importe quelle région Azure. Si l’encodage est urgent, par exemple lors du traitement d’événements en direct, vous devez être prêt à envoyer des tâches à un autre centre de données en cas de panne.

###Réseau virtuel

Les fichiers de configuration constituent le moyen le plus rapide de configurer un réseau virtuel dans une autre région Azure. Après avoir configuré le réseau virtuel dans la région Azure principale, [exportez les paramètres de réseau virtuel](../virtual-network/virtual-networks-create-vnet-classic-portal.md) pour le réseau actuel dans un fichier de configuration réseau. En cas de panne dans la région primaire, [restaurez le réseau virtuel](../virtual-network/virtual-networks-create-vnet-classic-portal.md) à partir du fichier de configuration stocké. Configurez ensuite les autres services cloud, les machines virtuelles ou les paramètres entre sites locaux pour utiliser le nouveau réseau virtuel.

##Listes de contrôle pour la récupération d’urgence

##Liste de contrôle de Cloud Services
  1. Consultez la section [Services cloud](#cloud-services) de ce document.
  2. Créez une stratégie de récupération d’urgence entre les régions.
  3. Maîtrisez les inconvénients de la réservation de capacité dans d’autres régions.
  4. Utilisez les outils de routage du trafic, notamment Azure Traffic Manager.

##Liste de contrôle de Virtual Machines
  1. Consulter la section [Virtual Machines](#virtual-machines) de ce document.
  2. Utilisez [Azure Backup](https://azure.microsoft.com/services/backup/) pour créer des sauvegardes cohérentes des applications dans différentes régions.

##Liste de contrôle du stockage
  1. Consultez la section [Stockage](#storage) de ce document.
  2. Ne désactivez pas la géoréplication des ressources de stockage.
  3. Maîtrisez l’autre région pour la géoréplication en cas de basculement.
  4. Créez des stratégies de sauvegarde personnalisées pour les stratégies de basculement contrôlées par l’utilisateur.

##Liste de contrôle de la Base de données SQL
  1. Consultez la section [Base de données SQL](#sql-database) de ce document.
  2. Utilisez la [géo-restauration](../sql-database/sql-database-geo-restore.md) ou la [géoréplication](../sql-database/sql-database-geo-replication-overview.md) en fonction des besoins.

##Liste de contrôle de SQL Server sur les machines virtuelles
  1. Consultez la section [SQL Server sur Virtual Machines](#sql-server-on-virtual-machines) de ce document.
  2. Utilisez des groupes de disponibilité AlwaysOn entre les régions ou la mise en miroir de bases de données.
  3. Ou utilisez la sauvegarde et la restauration dans le stockage d’objets blob.

##Liste de contrôle de Service Bus
  1. Consultez la section [Service Bus](#service-bus) de ce document.
  2. Configurez un espace de noms Service Bus dans une autre région.
  3. Prenez en compte les stratégies de réplication personnalisées pour les messages entre les régions.

##Liste de contrôle de Web Apps
  1. Consultez la section [Web Apps](#web-apps) de ce document.
  2. Conservez les sauvegardes de sites Web en dehors de la région primaire.
  3. Si une panne est partielle, tentez de récupérer le site actuel via FTP.
  4. Planifiez le déploiement d’un site Web sur un nouveau site Web ou un site Web existant dans une autre région.
  5. Planifiez les modifications de configuration pour les applications et les enregistrements DNS CNAME.

##Liste de contrôle des services mobiles
  1. Consultez la section [Services mobiles](#mobile-services) de ce document.
  2. Créez un service mobile de sauvegarde dans une autre région.
  3. Gérez les sauvegardes de la Base de données SQL Azure associée à restaurer pendant le basculement.
  4. Utilisez les outils de ligne de commande Azure pour déplacer le service mobile.

##Liste de contrôle de HDInsight
  1. Consultez la section [HDInsight](#hdinsight) de ce document.
  2. Créez un cluster Hadoop dans la région contenant les données répliquées.

##Liste de contrôle de SQL Reporting
  1. Consultez la section [SQL Reporting](#sql-reporting) de ce document.
  2. Conservez une autre instance SQL Reporting dans une région différente.
  3. Conservez un plan distinct pour répliquer la cible dans cette région.

##Liste de contrôle de Media Services
  1. Consultez la section [Media Services](#media-services) de ce document.
  2. Créez un compte Media Services dans une autre région.
  3. Encodez le même contenu dans les deux régions pour prendre en charge le basculement de la diffusion en continu.
  4. Envoyez des travaux d’encodage dans une autre région en cas de panne.

##Liste de contrôle du réseau virtuel
  1. Consultez la section [Réseau virtuel](#virtual-network) de ce document.
  2. Utilisez les paramètres du réseau virtuel qui ont été exportés pour le recréer dans une autre région.

##Étapes suivantes

Cet article fait partie d’une série intitulée [Guide technique de la résilience Azure](./resiliency-technical-guidance.md). L’article suivant de cette série traite de [la récupération à partir d’un centre de données local sur Azure](./resiliency-technical-guidance-recovery-on-premises-azure.md).

<!---HONumber=AcomDC_0615_2016-->