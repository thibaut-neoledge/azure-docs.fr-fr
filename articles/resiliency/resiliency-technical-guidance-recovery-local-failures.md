<properties
   pageTitle="Guide technique de la récupération suite à des défaillances locales dans Azure | Microsoft Azure"
   description="Article dédié à la compréhension et à la conception d’applications robustes, hautement disponibles et tolérantes aux pannes, ainsi qu’à la planification de la récupération d’urgence axée sur les défaillances locales dans Azure."
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

#Guide technique de la résilience Azure - Récupération suite à des défaillances locales dans Azure

La disponibilité des applications est menacée par deux dangers principaux : la défaillance des appareils, comme les disques ou les serveurs, et l’épuisement des ressources critiques, de calcul par exemple, durant les conditions de charge maximale. Azure offre une combinaison de gestion des ressources, d’élasticité, d’équilibrage de charge et de partitionnement pour activer la haute disponibilité dans ces circonstances. Certaines de ces fonctionnalités sont exécutées automatiquement pour tous les services cloud. Cependant, dans certains cas, le développeur d’applications doit effectuer des tâches supplémentaires pour en tirer parti.

##Services cloud
Tous les services cloud hébergés par Azure sont des collections d’un ou de plusieurs rôles web ou de travail. Une ou plusieurs instances d’un rôle donné peuvent s’exécuter simultanément. Le nombre d’instances est déterminé par la configuration. Les instances de rôle sont surveillées et gérées à l’aide d’un composant appelé le contrôleur de structure. Le contrôleur de structure détecte automatiquement les défaillances logicielles et matérielles, et y répond.

  * Chaque instance de rôle s’exécute dans sa propre machine virtuelle et communique avec son contrôleur de structure par le biais d’un agent invité. L’agent invité collecte les métriques de ressource et de nœud, y compris l’utilisation des machines virtuelles, les statuts, les journaux, l’utilisation des ressources, les exceptions et les conditions de défaillance. Le contrôleur de structure interroge l’agent invité en respectant des intervalles configurables et redémarre la machine virtuelle en l’absence de réponse.
  * En cas de défaillance matérielle, le contrôleur de structure associé déplace toutes les instances de rôle affectées vers un nouveau nœud de matériel et reconfigure le réseau pour y acheminer le trafic.

Pour tirer parti de ces fonctionnalités, les développeurs doivent s’assurer que tous les rôles de service évitent de stocker l’état sur les instances de rôle. Au lieu de cela, toutes les données persistantes doivent être accessibles à partir d’un stockage durable, tels que les services de stockage Azure ou la Base de données SQL Azure. Ainsi, les demandes peuvent être traitées par tous les rôles. Cela signifie également que les instances de rôle peuvent tomber en panne à tout moment sans créer d’incohérences dans l’état temporaire ou permanent du service.

La nécessité de stocker l’état en dehors des rôles présente plusieurs implications. Par exemple, cela implique que toutes les modifications associées à une table Azure Storage doivent être modifiées dans une seule transaction de groupe d’entités, si possible. Bien entendu, il n’est pas toujours possible d’apporter toutes les modifications dans une transaction unique. Vous devez veiller à garantir que les défaillances d’instances de rôle ne provoquent aucun problème lorsqu’elles interrompent des opérations à long terme qui englobent un minimum de deux mises à jour de l’état permanent du service. Si un autre rôle tente à nouveau une telle opération, il doit anticiper les situations pour lesquelles le travail a été partiellement effectué, et les gérer efficacement.

Par exemple, dans un service qui partitionne les données entre plusieurs magasins, si un rôle de travail tombe en panne pendant le déplacement d’une partition, cette activité peut être mise en échec ou répétée intégralement par un rôle de travail différent. Le cas échéant, vous pouvez obtenir des données orphelines ou corrompues. Pour éviter ces problèmes,les opérations de longue durée doivent être idempotentes (c.-à-d., répétables sans effet secondaire) et/ou redémarrables de façon incrémentielle (par exemple, pouvant se poursuivre à partir du point de défaillance le plus récent).

  * Pour être idempotente, une opération de longue durée doit présenter un effet identique dans toutes ses itérations, même lorsqu’elle est interrompue pendant l’exécution.
  * Pour être redémarrable de façon incrémentielle, une opération de longue durée doit se composer d’une séquence de plus petites opérations atomiques, et elle doit enregistrer sa progression dans un espace de stockage durable, de manière à ce que chacun des appels ultérieurs reprenne là où son prédécesseur s’est arrêté.

Enfin, toutes les opérations de longue durée doivent être appelées à plusieurs reprises jusqu’à leur réussite. Par exemple, une opération d’approvisionnement peut être placée dans une file d’attente Azure et supprimée de la file d’attente par un rôle de travail uniquement lorsqu’elle réussit. Un nettoyage de la mémoire peut être nécessaire de nettoyer les données créées par des opérations interrompues.

###Élasticité
Le nombre initial d’instances en cours d’exécution pour chaque rôle est déterminé dans la configuration de chaque rôle. Les administrateurs doivent initialement configurer chacun des rôles pour l’exécution avec deux ou plusieurs instances, en fonction de la charge attendue. Cependant, les instances de rôle peuvent facilement être mises à l’échelle en fonction des variations des modèles d’utilisation. Cette opération s’effectue manuellement dans le portail Azure et peut être automatisée à l’aide de Windows PowerShell, de l’API de gestion des services ou d’outils tiers. Cliquez ici pour plus d’informations sur la [Mise à l’échelle automatique d’un service cloud](../cloud-services/cloud-services-how-to-scale.md).

###Partitionnement
Le contrôleur de structure Azure utilise deux types de partitions : les domaines de mise à jour et les domaines d’erreur.

  * Un domaine de mise à jour est utilisé pour mettre à niveau les instances d’un rôle de service dans les groupes. Azure déploie les instances de service dans plusieurs domaines de mise à jour. Dans le cadre d’une mise à jour sur place, le contrôleur de structure interrompt toutes les instances d’un domaine de mise à jour, les met à jour et les redémarre avant de passer au domaine de mise à jour suivant. Cette approche prévient toute indisponibilité de l’intégralité du service durant le processus de mise à jour.
  * Un domaine d’erreur définit les points potentiels de défaillance matérielle ou de réseau. Le contrôleur de structure garantit que les instances d’un rôle en présentant plusieurs sont distribuées entre plusieurs domaines d’erreur, ceci pour éviter toute interruption du service par des défaillances isolées. L’exposition aux défaillances de serveur et de cluster dans Azure est régie par les domaines d’erreur.

En vertu du [contrat de niveau de service Azure](https://azure.microsoft.com/support/legal/sla/), Microsoft garantit que lorsqu’au minimum deux instances de rôles web sont déployées sur des domaines d’erreur et de mise à niveau différents, elles présentent une connectivité externe au moins 99,95 % du temps. Il n’existe aucun moyen de contrôler le nombre de domaines d’erreur, chose possible avec les domaines de mise à jour. Azure alloue automatiquement les domaines d’erreur et distribue les instances de rôle entre elles. Les deux premières instances au minimum de chaque rôle sont placées dans des domaines d’erreur et de mise à niveau différents, ceci pour garantir que les rôles présentant un minimum de deux instances satisferont les contrats de niveau de service. Cela est représenté dans le diagramme suivant.

![Isolement de domaines par défaut (Vue simplifiée)](./media/resiliency-technical-guidance-recovery-local-failures/partitioning-1.png "Isolement de domaines par défaut - Vue simplifiée")

###Équilibrage de la charge.
Tout le trafic entrant d’un rôle web passe par un équilibreur de charge sans état, qui distribue les demandes des clients entre les instances de rôle. Des instances de rôle isolées n’ont pas d’adresses IP publiques et ne sont pas directement adressables à partir d’Internet. Les rôles web sont sans état, ce qui permet de diriger les demandes des clients vers toute instance de rôle. Un événement [StatusCheck](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.statuscheck.aspx) est déclenché toutes les 15 secondes. Il permet d’indiquer si le rôle est prêt à recevoir le trafic, ou s’il est occupé et doit être retiré de la rotation d’équilibrage de charge.

##Machines virtuelles
Le modèle Azure Virtual Machines diffère des rôles de calcul PaaS par plusieurs aspects relatifs à la haute disponibilité. Dans certains cas, vous devez effectuer des opérations supplémentaires pour garantir une haute disponibilité.

###Durabilité des disques
Contrairement aux instances de rôle PaaS, les données stockées sur les disques de machines virtuelles demeurent persistantes, même en cas de déplacement des machines virtuelles. Azure Virtual Machines utilise les disques de machines virtuelles existants en tant qu’objets blob dans Azure Storage. En raison des caractéristiques de disponibilité d’Azure Storage, les données stockées sur des lecteurs d’une machine virtuelle sont également hautement disponibles. Notez que le lecteur D: fait exception à cette règle. Le lecteur D: lecteur correspond au stockage physique sur le serveur monté en rack qui héberge la machine virtuelle ; ses données seront perdues en cas de recyclage de la machine virtuelle. Le lecteur D: est dédié au stockage temporaire uniquement.

###Partitionnement
Azure comprend de manière native les couches d’une application PaaS (rôle web et rôle de travail), et peut par conséquent les distribuer efficacement entre les domaines d’erreur et de mise à jour. En revanche, les niveaux des applications IaaS doivent être définis manuellement à l’aide de groupes à haute disponibilité. Des groupes à haute disponibilité sont requis pour les contrats de niveau de service établis sous le modèle IaaS.

![Groupes à haute disponibilité pour Microsoft Azure Virtual Machines](./media/resiliency-technical-guidance-recovery-local-failures/partitioning-2.png "Groupes à haute disponibilité pour Microsoft Azure Virtual Machines")

Dans le diagramme ci-dessus, le niveau Internet Information Server (IIS, qui fonctionne comme un niveau d’application web) et le niveau SQL (qui fonctionne comme un niveau de données) sont attribués à différents groupes à haute disponibilité. Cela garantit que toutes les instances de chaque couche disposent d’une redondance matérielle, grâce à leur distribution entre les domaines d’erreur, et qu’elles ne sont pas arrêtées pendant une mise à jour.

###Équilibrage de la charge.
Si le trafic est réparti entre les machines virtuelles, vous devez les regrouper dans un service cloud et équilibrer la charge sur un point de terminaison TCP ou UDP spécifique. Pour plus d’informations, consultez la page [Équilibrage de charge des machines virtuelles](../virtual-machines/virtual-machines-linux-load-balance.md). Si les machines virtuelles sont approvisionnées par une autre source (par exemple, un mécanisme de file d’attente), aucun équilibreur de charge n’est requis. L’équilibreur de charge utilise un contrôle d’intégrité de base pour déterminer si le trafic doit être envoyé au nœud. Il est également possible de créer vos propres sondes pour implémenter des mesures spécifiques de contrôle d’intégrité de l’application. Le cas échéant, elles déterminent si la machine virtuelle doit recevoir le trafic.

##Storage
Azure Storage, qui est le service de données durables de référence pour Azure, fournit les objets blob, les tables, les files d’attente et le stockage sur disque de machine virtuelle. Il utilise une combinaison de réplication et de gestion des ressources pour assurer la haute disponibilité dans un centre de données unique. Le contrat de niveau de services d’Azure Storage garantit que des demandes d’ajout, de mise à jour, de lecture et de suppression correctement formatées seront efficacement traitées et que les comptes de stockage disposeront d’une connectivité à la passerelle Internet au moins 99,9 % du temps.

###Réplication
La durabilité des données pour Azure Storage est facilitée par la conservation de plusieurs copies de toutes les données sur des lecteurs différents situés dans des sous-systèmes de stockage physique entièrement indépendants au sein de la région. Les données sont répliquées de façon synchrone et toutes les copies sont validées avant la reconnaissance de l’écriture. Azure Storage est fortement cohérent, ce qui signifie que les lectures correspondent forcément aux écritures les plus récentes. En outre, les copies de données sont analysées en continu pour détecter et corriger une altération binaire, une menace souvent négligée en matière d’intégrité des données stockées. Les services bénéficient de la réplication, simplement par l’utilisation d’Azure Storage. Aucune tâche supplémentaire n’est requise par le développeur de service pour la récupération suivant une défaillance locale.

###Gestion des ressources
Les comptes de stockage créés après que le 7 juin 2012 peuvent être mis à l’échelle jusqu’à 200 To (la taille maximale précédente était de 100 To). Si de l’espace supplémentaire est nécessaire, les applications doivent être conçues pour valoriser plusieurs comptes de stockage.

###Disques de machines virtuelles
Un disque de machine virtuelle Virtual Machines est stocké comme un objet blob de pages dans Azure Storage. Par conséquent, il présente les mêmes propriétés de durabilité et d’extensibilité que le stockage d’objets blob. Cette conception rend les données du disque de la machine virtuelle persistantes, même si le serveur exécutant la machine virtuelle est mis en échec et que la machine virtuelle doit être redémarrée sur un autre serveur.

##Base de données

###Base de données SQL
La Base de données SQL Microsoft Azure fournit la base de données en tant que service, et permet ainsi aux applications de rapidement approvisionner, insérer des données et interroger des bases de données relationnelles. Elle offre de nombreuses fonctionnalités communes de SQL Server, tout vous épargnant la complexité associée au matériel, à la configuration, à la mise à jour corrective et à la résilience.

>[AZURE.NOTE]La Base de données SQL Azure, qui ne fournit pas un ensemble de fonctionnalités identique à celui de SQL Server, est conçue pour répondre à une série d’exigences spécifiques aux applications cloud (l’infrastructure élastique, la base de données en tant que service pour réduire les coûts de maintenance et ainsi de suite). Pour plus d’informations, consultez [Choisir une option de SQL Server cloud : Base de données SQL Azure (PaaS) ou SQL Server sur des machines virtuelles Azure (IaaS)](../sql-database/data-management-azure-sql-database-and-sql-server-iaas.md).

####Réplication
La Base de données SQL Azure fournit la résilience intégrée dédiée aux défaillances au niveau du nœud. Toutes les écritures dans une base de données sont automatiquement répliquées sur au moins deux nœuds en arrière-plan à l’aide d’une technique de validation de quorum (l’instance principale et une moins une instance secondaire doivent confirmer l’écriture de l’activité sur le fichier journal de transaction avant la validation de la réussite de la transaction et son renvoi). En cas de défaillance du nœud, la base de données bascule automatiquement vers un réplica secondaire. Cela entraîne une interruption de connexion temporaire pour les applications clientes. Pour cette raison, tous les clients de la Base de données SQL Microsoft Azure doivent implémenter une certaine forme de gestion des connexions temporaires. Pour plus d’informations, consultez la page [Utilisation du bloc applicatif de gestion des erreurs temporaires avec Azure](https://msdn.microsoft.com/library/hh680899.aspx).

####Gestion des ressources
Lors de la création, chaque base de données est configurée avec une limite de taille supérieure. La taille maximale actuellement disponible est 150 Go. Lorsqu’une base de données atteint sa limite de taille maximale, elle refuse des commandes INSERT ou UPDATE supplémentaires (l’interrogation et la suppression des données sont toujours possibles).

Dans une base de données, la Base de données SQL Microsoft Azure utilise une structure pour gérer les ressources. Toutefois, au lieu d’un contrôleur de structure, elle utilise une topologie en anneau pour détecter les erreurs. Dans un cluster, chaque réplica a deux voisins ; il est chargé de détecter leur mise hors service. Lorsqu’un réplica est mis hors service, ses voisins déclenchent une instance Reconfiguration Agent pour le recréer sur une autre machine. La limitation du moteur fournie vous assure qu’un serveur logique n’utilise pas trop de ressources sur une machine ou ne dépasse les limites physiques de la machine.

###Élasticité
Si l’application nécessite davantage que la limite de base de données de 150 Go, elle doit implémenter une approche de montée en charge. Avec la Base de données SQL Microsoft Azure, la montée en charge s’effectue par le biais d’un partitionnement manuel des données entre plusieurs bases de données SQL Azure. Cette approche de montée en charge permet de respecter une croissance des prix en fonction de la charge linéaire. La croissance élastique ou la capacité à la demande peuvent augmenter avec une tarification incrémentielle en fonction des besoins, car les bases de données sont facturées en fonction de la taille moyenne réelle utilisée chaque jour, pas en fonction de la taille maximale possible.

##SQL Server sur Virtual Machines
En installant SQL Server sur Azure Virtual Machines (version 2014 ou ultérieure), vous pouvez valoriser les fonctionnalités traditionnelles de disponibilité de SQL Server, telles que les groupes de disponibilité AlwaysOn ou la mise en miroir de base de données. Les machines virtuelles Azure, le stockage et le réseau ont des caractéristiques opérationnelles différentes de celles d’une infrastructure informatique non virtualisée sur site. Pour une implémentation réussie d’une solution de haute disponibilité/de récupération d’urgence SQL Server dans Azure, vous devez comprendre ces différences et concevoir votre solution de façon à les gérer.

###Nœuds haute disponibilité d’un groupe à haute disponibilité
Lorsque vous implémentez une solution à haute disponibilité dans Azure, un groupe à haute disponibilité vous permet de placer les nœuds haute disponibilité dans des domaines d’erreur et des domaines de mise à niveau distincts. Pour être clair, le groupe à haute disponibilité est un concept Azure. Nous vous recommandons de suivre cette meilleure pratique pour vous assurer de la haute disponibilité de vos bases de données, que vous utililisez des groupes de disponibilité AlwaysOn, la mise en miroir de base de données ou une autre solution. Si vous ne suivez pas cette meilleure pratique, vous pouvez supposer à tort que votre système est hautement disponible, alors qu’en réalité vos nœuds peuvent tous échouer simultanément, car ils sont placés dans le domaine d’erreur du centre de données Azure. Cette recommandation n’est pas applicable avec la copie des journaux de transaction, qui est une fonctionnalité de récupération d’urgence. Par conséquent, vous devez vous assurer que les serveurs sont en cours d’exécution dans des emplacements de centre de données Azure distincts (régions). Par définition, ces emplacements de centre de données sont des domaines d’erreur distincts.

Pour les machines virtuelles Azure que vous devez placer dans le même groupe à haute disponibilité, vous devez les déployer dans le même service cloud. Seuls les nœuds du même service cloud peuvent faire partie du même groupe à haute disponibilité. En outre, les machines virtuelles doivent se trouver dans le même réseau virtuel pour conserver à coup sûr leur adresse IP même après la réparation du service, ce qui vous épargne ainsi les intervalles de mise à jour DNS.

###Azure uniquement : solutions de haute disponibilité
Disposez d’une solution haute disponibilité pour vos bases de données SQL Server dans Azure à l’aide des groupes de disponibilité AlwaysOn ou de la mise en miroir de bases de données.

Le diagramme suivant illustre l’architecture des groupes de disponibilité AlwaysOn exécutés dans Azure Virtual Machines. Ce diagramme a été extrait de l’article détaillé sur ce sujet, [Haute disponibilité et récupération d’urgence pour SQL Server dans Azure Virtual Machines](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

![Groupes de disponibilité AlwaysOn dans Microsoft Azure](./media/resiliency-technical-guidance-recovery-local-failures/high_availability_solutions-1.png "Groupes de disponibilité AlwaysOn dans Microsoft Azure")

Vous pouvez également automatiquement configurer un déploiement de groupe de disponibilité AlwaysOn de bout en bout sur les machines virtuelles Azure, en utilisant un modèle AlwaysOn dans le portail Microsoft Azure. Pour plus d’informations, consultez [Offre AlwaysOn SQL Server dans la galerie du portail Microsoft Azure](https://blogs.technet.microsoft.com/dataplatforminsider/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery/).

Le diagramme suivant illustre l’utilisation de la mise en miroir de base de données sur Azure Virtual Machines. Il a également été extrait de la rubrique détaillée [Haute disponibilité et récupération d’urgence pour SQL Server dans Azure Virtual Machines](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

![Mise en miroir de base de données dans Microsoft Azure](./media/resiliency-technical-guidance-recovery-local-failures/high_availability_solutions-2.png "Mise en miroir de base de données dans Microsoft Azure")

>[AZURE.NOTE]Dans les deux architectures, un contrôleur de domaine est requis. Toutefois, avec la mise en miroir de base de données, il est possible d’utiliser des certificats de serveur pour éliminer le recours à un contrôleur de domaine.

##Autres services de plateforme Azure
Les services Azure Cloud Services étant développés sur Azure, ils sont pourvus des fonctionnalités de plateforme décrites précédemment, dédiées à la récupération suite à des défaillances locales. Dans certains cas, il existe des actions spécifiques que vous pouvez exécuter pour augmenter la disponibilité de votre scénario spécifique.

###Service Bus
Pour éviter une panne temporaire d’Azure Service Bus, envisagez de créer une file d’attente durable côté client. Cette fonctionnalité utilise temporairement un autre mécanisme stockage local pour stocker les messages qui ne peuvent pas être ajoutés à la file d’attente Service Bus. L’application peut décider comment traiter les messages temporairement stockés après la restauration du service. Pour plus d’informations, consultez la page [Meilleures pratiques relatives aux améliorations de performances à l’aide de la messagerie répartie Service Bus](../service-bus/service-bus-performance-improvements.md). Pour plus d’informations, consultez la section [Service Bus (Disaster Recovery)](./resiliency-technical-guidance-recovery-loss-azure-region.md#service-bus) (Service Bus (Récupération d’urgence)).

###Mobile Services
Il existe deux considérations relatives à la disponibilité pour Azure Mobile Services. Tout d’abord, sauvegardez régulièrement la base de données SQL Azure associé à votre service mobile. Sauvegarder également les scripts de service mobile. Pour plus d’informations, consultez la page [Récupération de votre service mobile en cas d’incident](../mobile-services/mobile-services-disaster-recovery.md). Si Mobile Services subit une panne temporaire, il vous faudra éventuellement utiliser temporairement un autre centre de données Azure. Pour plus d’informations, consultez la section [Mobile Services (Disaster Recovery)](./resiliency-technical-guidance-recovery-loss-azure-region.md#mobile-services) (Mobile Services (Récupération d’urgence)).

###HDInsight
Les données associées à HDInsight sont stockées par défaut dans le stockage d’objets blob Azure, qui présente les propriétés de haute disponibilité et de durabilité spécifiées par Azure Storage. Le traitement à plusieurs nœuds associé à des tâches Hadoop MapReduce est effectué sur un système de fichiers temporaire HDFS (Hadoop Distributed File System), qui est configuré au besoin par HDInsight. Les résultats d’une tâche MapReduce sont également stockés par défaut dans le stockage d’objets blob Azure, afin que les données traitées soient durables et demeurent hautement disponibles une fois le cluster Hadoop désaffecté. Pour plus d’informations, consultez la page [HDInsight (Disaster Recovery)](./resiliency-technical-guidance-recovery-loss-azure-region.md#hdinsight) (HDInsight (Récupération d’urgence)

##Listes de vérification : Défaillances locales
 
##Liste de contrôle des services cloud
  1. Consulter la section [Services cloud](#cloud-services) de ce document
  2. Configurer au moins deux instances pour chaque rôle
  3. Conserver l’état dans le stockage durable, non pas sur les instances de rôle
  4. Gérer correctement l’événement StatusCheck
  5. Encapsuler les modifications associées dans les transactions lorsque cela est possible
  6. Vérifiez que les tâches du rôle de travail sont idempotentes et redémarrables
  7. Continuer à appeler les opérations jusqu’à leur réussite
  8. Envisager de mettre en place des stratégies de mise à l’échelle automatique

##Liste de contrôle des machines virtuelles
  1. Consulter la section [Machines virtuelles](#virtual-machines) de ce document
  2. N’utilisez pas le lecteur D: pour le stockage persistant
  3. Grouper les machines dans un niveau de service d’un groupe à haute disponibilité
  4. Configurer l’équilibrage de charge et des sondes facultatives
 
##Liste de contrôle du stockage
  1. Consultez la section [Stockage](#storage) de ce document.
  2. Utiliser plusieurs comptes de stockage lorsque les données ou la bande passante dépassent les quotas

##Liste de contrôle de la Base de données SQL
  1. Consulter la section [Base de données SQL](#sql-database) de ce document
  2. Implémenter une stratégie de nouvelle tentative pour gérer les erreurs temporaires
  3. Utiliser le partitionnement en tant qu’une stratégie de montée en charge
  
##Liste de contrôle de SQL Server sur les machines virtuelles
  1. Consulter la section [SQL Server sur les machines virtuelles](#sql-server-on-virtual-machines) de ce document
  2. Suivez les recommandations précédentes relatives aux machines virtuelles.
  3. Utiliser les fonctionnalités de haute disponibilité de SQL Server, telles que AlwaysOn
  
##Liste de contrôle de Service Bus
  1. Consulter la section [Service Bus](#service-bus) de ce document
  2. Envisager de créer une file d’attente durable côté client, en tant que sauvegarde

##Liste de contrôle de HDInsight
  1. Consulter la section [HDInsight](#hdinsight) de ce document
  2. Aucune tâche supplémentaire de disponibilité requise pour les défaillances locales
 
##Étapes suivantes
Cet article fait partie d’une série intitulée [Guide technique de la résilience Azure](./resiliency-technical-guidance.md). L’article suivant de cette série traite de [la récupération suite à une interruption de service à l’échelle de la région](./resiliency-technical-guidance-recovery-loss-azure-region.md).

<!---HONumber=AcomDC_0525_2016-->