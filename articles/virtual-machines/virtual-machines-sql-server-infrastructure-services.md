<properties 
	pageTitle="Présentation de SQL Server sur des machines virtuelles | Microsoft Azure"
	description="Cet article fournit une vue d’ensemble de l’hébergement de SQL Server sur des machines virtuelles Azure. Il comporte des liens vers des contenus approfondis." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="rothja" 
	manager="jeffreyg"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services" 
	ms.date="11/12/2015"
	ms.author="jroth"/>

# Vue d’ensemble de SQL Server dans Azure Virtual Machines

## Prise en main
Vous pouvez héberger [SQL Server dans Azure Virtual Machines](http://azure.microsoft.com/services/virtual-machines/sql-server/) de diverses manières allant de la configuration d’un serveur de base de données unique à la configuration de plusieurs machines à l’aide de groupes de disponibilité AlwaysOn et d’un réseau virtuel Azure.

>[AZURE.NOTE]L’exécution de SQL Server sur une machine virtuelle Azure est une option permettant de stocker des données relationnelles dans Azure. Vous pouvez également utiliser le service Base de données SQL Azure. Pour plus d’informations, consultez [Présentation de Base de données SQL Azure et de SQL Server dans Azure Virtual Machines](../sql-database/data-management-azure-sql-database-and-sql-server-iaas.md).

Pour créer une machine virtuelle SQL Server dans Azure, vous devez d’abord obtenir un abonnement à la plateforme Azure. Vous pouvez acheter un abonnement Azure sur la page [Modes d’achat d’Azure](http://azure.microsoft.com/pricing/purchase-options/). Pour un essai gratuit, consultez la page [Évaluation gratuite d’Azure](https://azure.microsoft.com/pricing/free-trial/).

### Déploiement d’une instance de SQL Server sur une seule machine virtuelle

Après avoir souscrit à un abonnement, le moyen le plus simple de déployer une machine virtuelle SQL Server dans Azure consiste à [approvisionner une image de la galerie de machines SQL Server du portail de gestion Azure](virtual-machines-provision-sql-server.md). Ces images comprennent les frais de licence de SQL Server dans la tarification relative à la machine virtuelle.

>[AZURE.NOTE]Utilisez le [nouveau portail](https://manage.windowsazure.com) afin d’approvisionner et de gérer les machines virtuelles SQL Server Il utilise Premium Storage par défaut et propose des configurations de la mise à jour corrective automatisée, de la sauvegarde automatisée et AlwaysOn.

Le tableau suivant récapitule les images liées à SQL Server actuellement disponibles dans la galerie de machines virtuelles Azure.

|Version de SQL Server|Système d’exploitation|Édition SQL Server|
|---|---|---|
|SQL Server 2008 R2 SP2|Windows Server 2008 R2|Éditions Enterprise, Standard, Web|
|SQL Server 2008 R2 SP3|Windows Server 2008 R2|Éditions Enterprise, Standard, Web|
|SQL Server 2012 SP2|Windows Server 2012|Éditions Enterprise, Standard, Web|
|SQL Server 2012 SP2|Windows Server 2012 R2|Éditions Enterprise, Standard, Web|
|SQL Server 2014|Windows Server 2012 R2|Éditions Enterprise, Standard, Web|
|SQL Server 2014 SP1|Windows Server 2012 R2|Éditions Enterprise, Standard, Web|
|SQL Server 2016 CTP|Windows Server 2012 R2|Évaluation|

>[AZURE.NOTE]Les images de machines virtuelles de la galerie pour l'entreposage de données et les charges de travail transactionnelles (non présentées ci-dessus) sont obsolètes et seront bientôt supprimées de la galerie. Utilisez des images standard du tableau précédent et optimisez les performances de votre charge de travail spécifique.

En plus de ces images pré-configurées, vous pouvez également [créer une machine virtuelle Azure](virtual-machines-windows-tutorial.md) sans que SQL Server soit pré-installé. Vous pouvez installer n’importe quelle instance de SQL Server pour laquelle vous disposez d’une licence. Vous migrez votre licence vers Azure pour exécuter SQL Server dans une machine virtuelle Azure en utilisant [License Mobility via Software Assurance sur Azure](http://azure.microsoft.com/pricing/license-mobility/). Dans ce scénario, vous ne payez que pour les [coûts](http://azure.microsoft.com/pricing/details/virtual-machines) de stockage et de calcul Azure associés à la machine virtuelle.

Pour déterminer les meilleurs paramètres de configuration de machine virtuelle pour votre image SQL Server, consultez les [Meilleures pratiques relatives aux performances de SQL Server sur les machines virtuelles Azure](virtual-machines-sql-server-performance-best-practices.md). Pour les charges de travail de production, **DS3** est la taille de machine virtuelle minimale recommandée pour SQL Server Enterprise Edition, et **DS2** est la taille de machine virtuelle minimale recommandée pour Standard Edition.

Outre l’analyse des meilleures pratiques de performances, les autres tâches initiales sont les suivantes :

- [Vérification des meilleures pratiques relatives à la sécurité de SQL Server dans Azure Virtual Machines](virtual-machines-sql-server-security-considerations.md)
- [Configuration de la connectivité](virtual-machines-sql-server-connectivity.md)

### Migration de vos données

Une fois que votre machine virtuelle SQL Server est en cours d'exécution, vous pouvez souhaiter migrer des bases de données existantes vers la machine. Il existe plusieurs techniques, mais l'Assistant de déploiement dans SQL Server Management Studio fonctionne bien pour la plupart des scénarios. Pour en savoir plus sur les scénarios et obtenir un didacticiel sur l’Assistant, voir [Migration d’une base de données vers SQL Server sur une machine virtuelle Azure](virtual-machines-migrate-onpremises-database.md).

## Haute disponibilité

Si vous avez besoin d’une haute disponibilité, envisagez de configurer les groupes de disponibilité AlwaysOn de SQL Server. Cela implique la présence de plusieurs machines virtuelles Azure dans un réseau virtuel. Le portail Azure en version préliminaire dispose d’un modèle qui définit cette configuration pour vous. Pour plus d’informations, consultez [Offre AlwaysOn SQL Server dans la galerie du portail Microsoft Azure](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx).

Si vous souhaitez configurer manuellement votre groupe de disponibilité et l’écouteur associé, consultez les articles suivants :

- [Configuration de groupes de disponibilité AlwaysOn dans Azure (GUI)](virtual-machines-sql-server-alwayson-availability-groups-gui.md)
- [Configuration d'un écouteur à équilibrage de charge interne pour des groupes de disponibilité AlwaysOn dans Azure](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)
- [Déployer SQL Server AlwaysOn à l’aide d’un modèle Azure Resource Manager](virtual-machines-workload-template-sql-alwayson.md)
- [Extension des groupes de disponibilité AlwaysOn locaux à Azure](virtual-machines-sql-server-extend-on-premises-alwayson-availability-groups.md)

Pour plus d’informations sur la haute disponibilité, consultez [Haute disponibilité et récupération d’urgence pour SQL Server sur des machines virtuelles Azure](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md).

## Sauvegarde et restauration
Pour les bases de données sur site, Azure peut agir comme un centre de données secondaire pour stocker les fichiers de sauvegarde SQL Server. Pour une vue d’ensemble des options de sauvegarde et de restauration, voir [Sauvegarde et restauration de SQL Server dans Azure Virtual Machines](virtual-machines-sql-server-backup-and-restore.md).

La [sauvegarde SQL Server dans l’URL](https://msdn.microsoft.com/library/dn435916.aspx) stocke les fichiers de sauvegarde Azure dans le stockage d’objets blob Azure. La [sauvegarde gérée par SQL Server](https://msdn.microsoft.com/library/dn449496.aspx) vous permet de planifier la sauvegarde et la conservation dans Azure. Ces services peuvent être utilisés avec des instances de SQL Server sur site ou avec SQL Server s’exécutant sur des machines virtuelles Azure. Les machines virtuelles Azure peuvent également tirer parti de la [sauvegarde automatisée](virtual-machines-sql-server-automated-backup.md) et de la [mise à jour corrective automatisée](virtual-machines-sql-server-automated-patching.md) pour SQL Server.

## Détails de configuration d’image de machine virtuelle SQL Server

Les tableaux suivants décrivent la configuration des images de machine virtuelle SQL Server fournies par la plateforme.

### Windows Server

L’installation de Windows Server dans l’image de plateforme contient les paramètres et les composants de configuration suivants :

|Fonctionnalité|Configuration
|---|---|
|Bureau à distance|Activé pour le compte Administrateur|
|Windows Update|Activé|
|Comptes d’utilisateurs|Par défaut, le compte d’utilisateur spécifié lors de l’approvisionnement est un membre du groupe Administrateurs local. Ce compte d’administrateur est également membre du rôle serveur sysadmin SQL Server.|
|Groupes de travail|La machine virtuelle est membre d’un groupe de travail nommé WORKGROUP|
|Compte invité|Désactivé|
|Pare-feu Windows avec fonctions de sécurité avancées|Il en va|
|.NET Framework|Version 4|
|Disques|La taille sélectionnée limite le nombre de disques de données que vous pouvez configurer. Voir [Tailles de machines virtuelles pour Azure](virtual-machines-size-specs.md)|

### SQL Server

L’installation de SQL Server dans l’image de plateforme contient les paramètres et les composants de configuration suivants :

|Fonctionnalité|Configuration|
|---|---|
|Moteur de base de données|Installé|
|Analysis Services|Installé|
|Integration Services|Installé|
|Reporting Services|Configuré en mode natif|
|Groupes de disponibilité AlwaysOn|Disponible dans SQL Server 2012 ou version ultérieure. Requiert une [configuration supplémentaire](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md)
|Réplication|Installé|
|Extractions en texte intégral et sémantiques pour la recherche|Installé (extractions sémantiques dans SQL Server 2012 ou version supérieure uniquement)|
|Data Quality Services|Installé (SQL Server 2012 ou version ultérieure uniquement)|
|Master Data Services|Installé (SQL Server 2012 ou version ultérieure uniquement). Requiert une [configuration et des composants supplémentaires](https://msdn.microsoft.com/library/ee633752.aspx)
|PowerPivot pour SharePoint|Disponible (SQL Server 2012 ou version ultérieure uniquement). Requiert une configuration et des composants supplémentaires (notamment SharePoint)|
|Distributed Replay Client|Disponible (SQL Server 2012 ou version ultérieure uniquement), mais pas installé. Voir [Exécution du programme d’installation de SQL Server à partir de l’image de SQL Server fournie par la plateforme](#run-sql-server-setup-from-the-platform-provided-sql-server-image)|
|Outils|Tous les outils, notamment SQL Server Management Studio, le Gestionnaire de configuration SQL Server, Business Intelligence Development Studio, le programme d’installation de SQL Server, l’option Connectivité des outils clients, le Kit de développement logiciel (SDK) des outils clients, le Kit de développement logiciel (SDK) de l’option Connectivité client de SQL et les outils de mise à niveau et de migration, tels que les applications de la couche Données (DAC), les outils de sauvegarde, de restauration, d’attachement et de détachement|
|Documentation en ligne de SQL Server|Installée mais requiert la configuration en utilisant Help Viewer|

### Configuration du moteur de base de données

Les paramètres du moteur de base de données suivants sont configurés. Pour consulter d’autres paramètres, examinez l’instance de SQL Server.

|Fonctionnalité|Configuration|
|---|---|
|Instance|Contient une instance (sans nom) par défaut du moteur de base de données SQL Server, écoutant uniquement le protocole de mémoire partagée|
|Authentification|Par défaut, Azure sélectionne l’authentification Windows pendant l’installation de la machine virtuelle SQL Server. Si vous souhaitez utiliser la connexion « sa » ou créer un nouveau compte SQL Server, vous devez modifier le mode d’authentification. Pour plus d’informations, voir [Considérations relatives à la sécurité de SQL Server dans Azure Virtual Machines](virtual-machines-sql-server-security-considerations.md).|
|administrateur système|L’utilisateur Azure qui a installé la machine virtuelle est initialement le seul membre du rôle serveur fixe sysadmin de SQL Server|
|Mémoire|La mémoire du moteur de base de données est définie dans la configuration comme mémoire dynamique|
|Authentification de la base de données à relation contenant-contenu|Off|
|Langue par défaut|Français|
|Chaînage d’appartenance entre plusieurs bases de données|Off|

### Programme d’amélioration du produit (CEIP)

Le [Programme d’amélioration du produit (CEIP)](https://technet.microsoft.com/library/cc730757.aspx) est activé. Vous pouvez désactiver le programme CEIP à l’aide de l’utilitaire Rapports d’erreurs et d’utilisation SQL Server. Pour lancer l’utilitaire Rapports d’erreurs et d’utilisation SQL Server : dans le menu Démarrer, cliquez sur Tous les programmes, sur la version Microsoft SQL Server, sur Outils de configuration, puis sur Rapports d’erreurs et d’utilisation SQL Server. Si vous ne souhaitez pas utiliser une instance SQL Server pour laquelle le programme CEIP est activé, vous pouvez envisager de déployer votre propre image de machine virtuelle dans Azure. Pour plus d’informations, voir [Création et téléchargement d’un disque dur virtuel qui contient le système d’exploitation Windows Server](virtual-machines-create-upload-vhd-windows-server.md).

## Exécuter le programme d’installation de SQL Server à partir de l’image de SQL Server fournie par la plateforme

Si vous créez une machine virtuelle à l’aide d’une image SQL Server fournie par la plateforme, vous trouverez les supports d’installation de SQL Server enregistrés dans le répertoire **C:\\SqlServer\_SQLMajorVersion.SQLMinorVersion\_Full** de la machine virtuelle. Vous pouvez exécuter le programme d’installation à partir de ce répertoire pour effectuer toutes les actions d’installation, notamment pour ajouter ou supprimer des fonctionnalités, ajouter une nouvelle instance, ou réparer l’instance si l’espace disque le permet.

>[AZURE.NOTE]Notez que les images SQL Server sont proposées dans plusieurs versions sur le portail Azure. Si la version de l’image fournie par la plateforme SQL Server a été publiée le 15 mai 2014 ou à une date ultérieure, elle contient par défaut la clé de produit. Si vous déployez une machine virtuelle à partir d’une image SQL Server fournie par la plateforme qui a été publiée avant cette date, cette machine virtuelle ne contient pas la clé de produit. En guise de meilleure pratique, nous vous recommandons de toujours sélectionner la dernière version d’image à l’occasion du déploiement d’une nouvelle machine virtuelle.

## Ressources

- [Configuration d'une machine virtuelle SQL Server sur Azure](virtual-machines-provision-sql-server.md)
- [Migrating a Database to SQL Server on an Azure VM](virtual-machines-migrate-onpremises-database.md)
- [Haute disponibilité et récupération d’urgence pour SQL Server dans Azure Virtual Machines](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md)
- [Modèles d'application et stratégies de développement pour SQL Server dans Azure Virtual Machines](virtual-machines-sql-server-application-patterns-and-development-strategies.md)
- [Azure Virtual Machines](virtual-machines-about.md) 

<!---HONumber=Nov15_HO4-->