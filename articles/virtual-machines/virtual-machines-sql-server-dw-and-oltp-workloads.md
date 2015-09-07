<properties 
	pageTitle="Entreposage de données et charges de travail transactionnelles de SQL Server dans des machines virtuelles Azure"
	description="Décrit les images de machine virtuelle SQL Server préconfigurées et optimisées dans Azure pour l’entreposage de données et les charges de travail OLTP."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"/>
<tags 
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="08/19/2015"
	ms.author="jroth"/>

# Entreposage de données et charges de travail transactionnelles de SQL Server dans des machines virtuelles Azure

Pour utiliser SQL Server pour l’entreposage de données ou les charges de travail transactionnelles dans une machine virtuelle Azure, nous vous recommandons d’utiliser l’une des images de machine virtuelle préconfigurées dans la galerie de machines virtuelles Azure. Ces images ont été optimisées conformément aux recommandations figurant dans [Meilleures pratiques relatives aux performances de SQL Server sur les machines virtuelles Azure](virtual-machines-sql-server-performance-best-practices.md).

Cet article porte sur l’exécution de ces charges de travail sur Azure Virtual Machines (également appelé IaaS ou « Infrastructure-as-a-Service »). Vous pouvez également exécuter l’entreposage de données et les charges de travail transactionnelles en tant que service dans Azure. Pour plus d’informations, consultez [Azure SQL Data Warehouse (version préliminaire)](http://azure.microsoft.com/documentation/services/sql-data-warehouse/) et [Base de données SQL Azure](http://azure.microsoft.com/documentation/services/sql-database/).

## Quelles sont les images de machine virtuelle préconfigurées disponibles ?

Les images de machine virtuelle préconfigurées suivantes sont disponibles dans la galerie de machines virtuelles Azure :

- [SQL Server 2014 Enterprise optimisée pour les charges de travail transactionnelles sur Windows Server 2012 R2](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014fortransactionalworkloadswindowsserver2012r2/)

- [SQL Server 2014 Enterprise optimisée pour l’entreposage de données sur Windows Server 2012 R2](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014datawarehousingwindowsserver2012r2/)

- [SQL Server 2012 SP2 Enterprise optimisée pour les charges de travail transactionnelles sur Windows Server 2012 R2](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2fortransactionalworkloadswindowsserver2012r2)

- [SQL Server 2012 SP2 Enterprise optimisée pour l’entreposage de données sur Windows Server 2012 R2](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2datawarehousingworkloadswindowsserver2012r2)

- [SQL Server 2012 SP2 Enterprise optimisée pour les charges de travail transactionnelles sur Windows Server 2012](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2fortransactionalworkloadswindowsserver2012/)

- [SQL Server 2012 SP2 Enterprise optimisée pour l’entreposage de données sur Windows Server 2012](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2datawarehousingworkloadswindowsserver2012/)

Ces images sont actuellement prises en charge sur des instances de machine virtuelle qui acceptent jusqu’à 16 disques de données associés pour fournir un débit optimal (ou une bande passante globale). Ces instances sont de niveau Standard A4, A7, A8, A9, D4, D13, D14, F3, G4 et G5 et de niveau De base A4. Pour en savoir plus sur les tailles et options, consultez la page [Tailles de machines virtuelles dans Azure](virtual-machines-size-specs.md).

>[AZURE.NOTE]Avant septembre 2014, les précédentes images transactionnelles et d’entreposage de données de la galerie étaient disponibles. Toutefois, vous deviez associer des disques de données pour pouvoir les utiliser. Nous vous recommandons d’utiliser les images plus récentes ci-dessus, car elles sont utilisables dès la configuration.

## Configuration d’une machine virtuelle SQL à partir de la galerie à l’aide des images transactionnelles/d’entreposage de données

1. Connectez-vous au [portail de gestion Azure](http://manage.windowsazure.com/).

1. Cliquez sur **MACHINES VIRTUELLES** dans les éléments du menu Azure dans le volet gauche.

1. Dans le coin inférieur gauche, cliquez sur **NOUVEAU**, **CALCULER**, **MACHINE VIRTUELLE**, puis **À PARTIR DE LA GALERIE**.

1. Sur la page de sélection d’image de machine virtuelle, sélectionnez l’un des serveurs SQL Server pour les images transactionnelles ou d’entreposage de données.

	![Galerie de machines virtuelles Azure](./media/virtual-machines-sql-server-dw-and-oltp-workloads/IC814362.png)

1. Sur la page **Configuration des machines virtuelles**, sous l’option **TAILLE**, choisissez l’une des tailles prises en charge.

	![Configuration de la galerie de machines virtuelles Azure](./media/virtual-machines-sql-server-dw-and-oltp-workloads/IC814363.png)

	>[AZURE.NOTE]Seules les instances de niveau Standard A4, A7, A8, A9, D4, D13, D14, G3, G4 et G5 et de niveau De base A4 sont prises en charge actuellement. Les tentatives de configuration de tailles de machine virtuelle non prises en charge échouent.

1. Attendez la fin de la configuration. Pendant ce temps, vous pouvez consulter l’état de la configuration sur la page des machines virtuelles (image ci-dessous). Lorsque la configuration est terminée, l’état indiqué est **En cours d’exécution**, accompagné une coche.

	![État de la galerie de machines virtuelles Azure](./media/virtual-machines-sql-server-dw-and-oltp-workloads/IC814364.png)

## Utilisation de PowerShell pour créer les images transactionnelles/d’entreposage de données

Vous pouvez également utiliser l’applet de commande PowerShell **New-AzureQuickVM** pour créer la machine virtuelle. Vous devez transmettre le nom de votre service cloud, le nom de machine virtuelle, le nom d’image, le nom d’utilisateur et le mot de passe d’administrateur, ainsi que d’autres informations similaires en tant que paramètres. Pour obtenir le nom de l’image, vous pouvez utiliser **Get-AzureVMImage** pour afficher une liste de toutes les images de machine virtuelle disponibles.

Par exemple, la commande PowerShell suivante renvoie la dernière image correspondant à l’étiquette d’image **SQL Server 2012 SP2 Enterprise optimisée pour l’entreposage de données sur Windows Server 2012 R2** dans la liste de la section précédente.

	(Get-AzureVMImage | where {$_.Label -like "SQL Server 2012 SP2 Enterprise Optimized for DataWarehousing Workloads on Windows Server 2012 R2"} | sort PublishedDate -Descending)[0].ImageName

Pour plus d’informations sur la création d’images avec PowerShell, consultez [Utilisation d’Azure PowerShell pour créer et préconfigurer des machines virtuelles basées sur Windows](virtual-machines-ps-create-preconfigure-windows-vms.md).

## Configurations spécifiques incluses dans les images transactionnelles/d’entreposage de données

Ces images ont été optimisées conformément aux recommandations de la page [Meilleures pratiques relatives aux performances de SQL Server sur les machines virtuelles Azure](virtual-machines-sql-server-performance-best-practices.md). Plus précisément, la configuration de ces images comprend les optimisations suivantes.

>[AZURE.NOTE]Si vous utilisez votre propre licence et que vous créez une machine virtuelle d’entreposage de données ou transactionnelle en partant de zéro, vous pouvez baser vos optimisations sur l’article relatif aux performances et les exemples d’optimisations présentés dans les images de galerie préconfigurées ci-dessous.

### Configurations de disque

|Configuration|Paramètre|
|---|---|
|Nombre de disques de données associés|15|
|Espaces de stockage|Deux pools de stockage :<br/>-- 1 pool de données avec 12 disques de données ; taille fixe de 12 To ; Colonne = 12<br/>--1 pool de journal avec 3 disques de données ; taille fixe 3 To ; Colonne = 3<br/><br/>Un disque de données restant que l'utilisateur doit associer et dont il doit définir l'utilisation.<br/><br/>**DW** : taille de bande = 256 Ko<br/>**Transactionnelle** : taille de bande = 64 Ko|
|Tailles du disque|1 To chacun|
|Mise en cache|HostCache = aucun|
|Taille d'allocation|Taille d'unité d'allocation NTFS = 64 Ko|

### Configurations de SQL Server

|Configuration|Paramètre|
|---|---|
|Paramètres de démarrage|-T1117 pour conserver la taille des fichiers de données en cas de croissance automatique de la base de données<br/><br/>-T1118 pour faciliter l'extensibilité de tempdb (pour plus d'informations, consultez la page [Utilisation de l'indicateur de suivi 1118 (-T1118) SQL Server (2005 et 2008)](http://blogs.msdn.com/b/psssql/archive/2008/12/17/sql-server-2005-and-2008-trace-flag-1118-t1118-usage.aspx?WT.mc_id=Blog_SQL_Announce_Announce).)|
|Modèle de récupération|**DW** : définir sur SIMPLE pour une base de données de modèle utilisant ALTER DATABASE<br/>**Transactionnelle** : aucune modification|
|Emplacements par défaut de l’installation|Déplacer les répertoires du journal d’erreurs et du fichier de trace SQL Server vers les disques de données|
|Emplacements par défaut pour les bases de données|Bases de données système déplacées vers les disques de données.<br/><br/>L'emplacement de création de bases de données utilisateur devient celui des disques de données.|
|Initialisation instantanée des fichiers|Activé|
|Verrouillage des pages en mémoire|Activé (pour plus d'informations, consultez la page [Activer l'option Verrouillage des pages en mémoire (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).|

## Forum Aux Questions

- Le prix des images optimisées est-il différent de celui des images non optimisées ?

	Non. Les images optimisées suivent le même modèle de tarification ([plus d'infos](http://azure.microsoft.com/pricing/details/virtual-machines/)) sans coût supplémentaire. Notez qu’un tarif supérieur est appliqué aux tailles d’instances de machine virtuelle plus importantes.

- Dois-je prendre en compte d’autres correctifs de performances ?

	Oui, vous devez envisager d’appliquer les correctifs de performances appropriés pour SQL Server :

	- [CORRECTIF : performances d’E/S réduites lors de l’exécution de SELECT dans une opération de table temporaire dans SQL Server 2012](https://support.microsoft.com/kb/2958012)

	- [CORRECTIF : « Les compteurs de performances de SQL Server sont désactivés » lorsque vous déplacez la ressource SQL Server dans SQL Server 2014](http://support.microsoft.com/kb/2973444)

- Où trouver des informations supplémentaires sur les espaces de stockage ?

	Pour plus d'informations sur les espaces de stockage, consultez le [Forum aux questions sur les espaces de stockage](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx).

- Quelle est la différence entre la nouvelle image DW et la précédente ?

	L’image DW précédente exige des étapes supplémentaires, telles que l’association des disques de données une fois la machine virtuelle créée. La nouvelle image DW quant à elle est utilisable dès la création. Elle est disponible plus rapidement et comporte moins de risques d’erreurs.

- Que se passe-t-il si je dois utiliser l’image DW précédente ? Existe-t-il un moyen d’y accéder ?

	Les images de machine virtuelle précédentes sont toujours disponibles, mais pas directement accessibles à partir de la galerie. Vous pouvez continuer à utiliser les applets de commande Powershell. Par exemple, vous pouvez utiliser **Get-AzureVMImage** pour répertorier toutes les images et, lorsque vous repérez l'image DW précédente d'après la description et la date de publication, vous pouvez utiliser **New-AzureVM** pour la configurer.

## Étapes suivantes

Après avoir installé une machine virtuelle avec SQL Server, vous allez sans doute :

- [Migrer vos données](virtual-machines-migrate-onpremises-database.md)
- [Configurer la connectivité](virtual-machines-sql-server-connectivity.md)

Pour d'autres rubriques relatives à l'utilisation de SQL Server sur des machines virtuelles Azure, consultez la rubrique [SQL Server sur les machines virtuelles Azure](virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=August15_HO9-->