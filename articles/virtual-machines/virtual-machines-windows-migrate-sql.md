<properties
	pageTitle="Migrer une base de données SQL Server vers SQL Server sur une machine virtuelle | Microsoft Azure"
	description="Découvrez comment migrer une base de données utilisateur sur site de SQL Server dans une machine virtuelle Azure."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="sabotta"
	manager="jhubbard"
	editor=""
	tags="azure-service-management" />
<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/26/2016"
	ms.author="carlasab"/>


# Migrer une base de données SQL Server vers SQL Server dans une machine virtuelle Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)] Modèle Resource Manager


Il existe plusieurs méthodes permettant la migration d’une base de données utilisateur SQL Server locale vers SQL Server sur une machine virtuelle Azure. Cet article aborde brièvement les différentes méthodes, recommande la mieux adaptée en fonction de différents scénarios et inclut un [didacticiel](#azure-vm-deployment-wizard-tutorial) qui vous guide dans l’utilisation de l’assistant **Déploiement d’une base de données SQL Server vers une machine virtuelle Microsoft Azure**.

La méthode consistant à utiliser l’Assistant **de déploiement d’une base de données SQL Server sur une machine virtuelle Microsoft Azure** décrit dans le [didacticiel](#azure-vm-deployment-wizard-tutorial) n’est pertinente que pour le modèle de déploiement classique.

## Quelles sont les principales méthodes de migration ?

Les méthodes de migration principales consistent à :

- Utiliser l’assistant de déploiement d’une base de données SQL Server vers une machine virtuelle Microsoft Azure
- Effectuer une sauvegarde locale par compression et copier manuellement le fichier de sauvegarde dans la machine virtuelle Azure
- Effectuer une sauvegarde vers une URL et exécuter une restauration dans la machine virtuelle Azure à partir de cette URL
- Détacher, puis copier les fichiers journaux et de données vers le stockage d’objets blob Azure, puis les rattacher à SQL Server dans la machine virtuelle Azure depuis une URL
- Convertir une machine physique locale en disque dur virtuel Hyper-V, effectuer un téléchargement vers le stockage d’objets Blob Azure, puis exécuter un déploiement en tant que nouvelle machine virtuelle en utilisant le disque dur virtuel téléchargé
- Expédition du disque dur avec le Service d’importation/exportation Windows
- Si vous disposez d’un déploiement AlwaysOn local, utilisez l’[Assistant Ajout d’un réplica Azure](virtual-machines-windows-classic-sql-onprem-availability.md) pour créer un réplica dans Azure, puis basculez en dirigeant les utilisateurs vers l’instance de base de données Azure
- Utiliser la [réplication transactionnelle](https://msdn.microsoft.com/library/ms151176.aspx) de SQL Server pour configurer l’instance Azure SQL Server en tant qu’abonné, puis désactiver la réplication en dirigeant les utilisateurs vers l’instance de base de données Azure



## Choix de votre méthode de migration

Pour obtenir un transfert de performances optimal, c’est la migration des fichiers de base de données de la machine virtuelle Azure par fichier de sauvegarde compressé qui constitue en général la meilleure méthode. C’est cette méthode que l’[assistant de déploiement d’une base de données SQL Server vers Microsoft Azure](#azure-vm-deployment-wizard-tutorial) utilise. Il est conseillé d’utiliser la méthode faisant appel à cet assistant pour faire migrer une base de données utilisateur locale exécutée sous SQL Server 2005 ou version ultérieure vers SQL Server 2014 ou version ultérieure lorsque la base de données concernée, après compression, génère un fichier de sauvegarde au volume inférieur à 1 To.

Pour réduire le temps d’arrêt pendant la migration de base de données, utilisez l’option AlwaysOn ou l’option de réplication transactionnelle.

S’il n’est pas possible d’utiliser les méthodes décrites ci-dessus, migrez manuellement votre base de données. Cette méthode consiste généralement à effectuer une sauvegarde de base de données, puis une copie de la sauvegarde de base de données dans Azure, et enfin à effectuer une restauration de base de données. Vous pouvez également copier les fichiers de base de données eux-mêmes dans Azure, puis les joindre. Il existe plusieurs méthodes permettant d’effectuer manuellement le processus de migration d’une base de données vers une machine virtuelle Azure.

> [AZURE.NOTE] Lorsque vous procédez à une mise à niveau vers SQL Server 2014 ou SQL Server 2016 depuis des versions antérieures de SQL Server, vous devez prendre en compte les éventuelles modifications nécessaires. Nous vous recommandons de gérer toutes les dépendances de fonctionnalités non prises en charge par la nouvelle version de SQL Server dans le cadre de votre projet de migration. Pour plus d’informations sur les éditions et les scénarios pris en charge, consultez [Mise à niveau vers SQL Server](https://msdn.microsoft.com/library/bb677622.aspx).

Le tableau suivant répertorie les principales méthodes de migration et explique dans quelles circonstances utiliser chacune d’elles.

| Méthode | Version de base de données source | Version de base de données de destination | Contrainte de taille pour la sauvegarde de base de données source | Remarques |
|---|---|---|---|---|
| [Utiliser l’assistant de déploiement d’une base de données SQL Server sur une machine virtuelle Microsoft Azure](#azure-vm-deployment-wizard-tutorial) | SQL Server 2005 ou ultérieur | SQL Server 2014 ou ultérieur | < 1 To | Il s’agit de la méthode la plus rapide et la plus simple, à utiliser lorsqu’il est possible d’exécuter la migration vers une instance de serveur nouvelle ou existante dans une machine virtuelle Azure | 
| [Utiliser l’Assistant Ajout d’un réplica Azure](virtual-machines-windows-classic-sql-onprem-availability.md) | SQL Server 2012 ou ultérieur | SQL Server 2012 ou ultérieur | [Limite de stockage de machine virtuelle Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Réduit le temps d’arrêt ; à utiliser lorsque vous disposez d’un déploiement local AlwaysOn. |
| [Utiliser la réplication transactionnelle de SQL Server](https://msdn.microsoft.com/library/ms151176.aspx) | SQL Server 2005 ou ultérieur | SQL Server 2005 ou ultérieur | [Limite de stockage de machine virtuelle Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | À utiliser si vous devez réduire le temps d’arrêt et que vous ne disposez pas d’un déploiement local AlwaysOn. |
| [Effectuer une sauvegarde locale en utilisant la compression et copier manuellement le fichier de sauvegarde dans la machine virtuelle Azure](#backup-to-file-and-copy-to-vm-and-restore) | SQL Server 2005 ou ultérieur | SQL Server 2005 ou ultérieur | [Limite de stockage de machine virtuelle Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Utiliser uniquement lorsqu’il est impossible d’utiliser l’Assistant, par exemple, lorsque la version de base de données de destination est antérieure à SQL Server 2012 SP1 CU2 ou lorsque la taille de la sauvegarde de base de données est supérieure à 1 To (12,8 To avec SQL Server 2016) |
| [Effectuer une sauvegarde vers l’URL et une restauration dans la machine virtuelle Azure depuis l’URL](#backup-to-url-and-restore) | SQL Server 2012 SP1 CU2 ou ultérieur | SQL Server 2012 SP1 CU2 ou ultérieur | < 12,8 To pour SQL Server 2016, sinon < 1 To | En général l’utilisation de la [Sauvegarde vers URL](https://msdn.microsoft.com/library/dn435916.aspx) est équivalente en performances à l’utilisation de l’Assistant, mais la procédure est plus compliquée |
| [Détacher, puis copier les données et les fichiers journaux dans le stockage d’objets blob Azure et les rattacher à SQL Server dans la machine virtuelle Azure à partir d’URL](#detach-and-copy-to-url-and-attach-from-url) | SQL Server 2005 ou ultérieur | SQL Server 2014 ou ultérieur | [Limite de stockage de machine virtuelle Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Utilisez cette méthode quand vous envisagez de [stocker ces fichiers à l’aide du service de stockage d’objets Blob Azure](https://msdn.microsoft.com/library/dn385720.aspx) et de les rattacher à SQL Server s’exécutant dans une machine virtuelle Azure, en particulier avec les bases de données très volumineuses. |
| [Convertir l’ordinateur local pour les disques durs virtuels Hyper-V, charger sur le stockage d’objets Blob Azure, puis déployer une nouvelle machine virtuelle en utilisant le disque dur virtuel chargé](#convert-to-vm-and-upload-to-url-and-deploy-as-new-vm) | SQL Server 2005 ou ultérieur | SQL Server 2005 ou ultérieur | [Limite de stockage de machine virtuelle Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | À utiliser lors de [la mise en service de votre propre licence SQL Server](../data-management-azure-sql-database-and-sql-server-iaas/), lors de la migration d’une base de données que vous allez exécuter sur une version plus ancienne de SQL Server, ou lors de la migration d’un système et de bases de données d’un système utilisateur dans le cadre de la migration de base de données dépendant des autres bases de données utilisateur et/ou bases de données système. |
| [Expédition du disque dur à l’aide du Service d’importation/exportation Windows](#ship-hard-drive) | SQL Server 2005 ou ultérieur | SQL Server 2005 ou ultérieur | [Limite de stockage de machine virtuelle Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Utilisez le [Service Import/Export de Windows](../storage/storage-import-export-service.md) lorsque la méthode de copie manuelle est trop lente, par exemple avec de très grandes bases de données. |

## Didacticiel Assistant de déploiement de machine virtuelle Azure

Utilisez l’Assistant **Déploiement d’une base de données SQL Server sur une machine virtuelle Microsoft Azure** dans Microsoft SQL Server Management Studio pour migrer une base de données utilisateur locale SQL Server 2005, SQL Server 2008, SQL Server 2008 R2, SQL Server 2012, SQL Server 2014 ou SQL Server 2016 (jusqu’à 1 To) vers SQL Server 2014 ou SQL Server 2016 dans une machine virtuelle Azure. Utilisez cet Assistant pour migrer une base de données utilisateur soit vers une machine virtuelle Azure existante, soit vers une machine virtuelle Azure avec SQL Server créé par l’Assistant pendant le processus de migration. Lorsque vous migrez une base de données vers une version plus récente de SQL Server, la base de données est automatiquement mise à niveau au cours du processus.

Cette méthode n’est pertinente que pour le modèle de déploiement classique.

### Procurez-vous la dernière version de l’Assistant Déploiement d’une base de données SQL Server vers une machine virtuelle Microsoft Azure

Utilisez la toute dernière version de Microsoft SQL Server Management Studio pour SQL Server pour vous assurer que vous disposez de la version la plus récente de l’Assistant **Déploiement d’une base de données SQL Server sur une machine virtuelle Microsoft Azure**. La dernière version de cet Assistant intègre les dernières mises à jour sur le portail Azure Classic et prend en charge les images de machine virtuelle Azure les plus récentes dans la galerie (il se peut que les versions antérieures de l’Assistant ne fonctionnent pas). Pour obtenir la toute dernière version de Microsoft SQL Server Management Studio pour SQL Server, [téléchargez-la](http://go.microsoft.com/fwlink/?LinkId=616025) et installez-la sur un ordinateur client avec une connexion à la base de données que vous prévoyez de faire migrer et à Internet.

### Configurez la machine virtuelle Azure et une instance SQL Server (le cas échéant)

Si vous migrez vers une machine virtuelle Azure existante, les opérations de configuration suivantes sont requises :

- Configurez la machine virtuelle Azure et l’instance de SQL Server pour activer la connectivité à partir d’un autre ordinateur en suivant les étapes suivantes pour [vous connecter à une instance de machine virtuelle SQL Server à partir de SSMS sur un autre ordinateur](virtual-machines-windows-sql-connect.md). Seules les images SQL Server 2014 et SQL Server 2016 dans la galerie sont prises en charge si vous effectuez une migration à l’aide de l’Assistant.
- Configurez un point de terminaison ouvert pour le service SQL Server Cloud Adapter sur la passerelle de Microsoft Azure avec le port privé 11435. Ce port est créé dans le cadre de la mise en service de SQL Server 2014 ou SQL Server 2016 sur une machine virtuelle Microsoft Azure. L’adaptateur de Cloud crée également une règle de pare-feu Windows pour autoriser les connexions TCP entrantes sur le port 11435 par défaut. Ce point de terminaison permet à l’Assistant d’utiliser le service de l’adaptateur de Cloud pour copier les fichiers de sauvegarde depuis l’instance sur site sur la machine virtuelle Azure sur site. Pour plus d’informations, voir [Adaptateur cloud pour SQL Server](https://msdn.microsoft.com/library/dn169301.aspx).

	![Créer le point de terminaison de l’adaptateur Cloud](./media/virtual-machines-windows-migrate-sql/cloud-adapter-endpoint.png)

### Exécutez l’Assistant Déployer une base de données SQL Server sur une machine virtuelle Microsoft Azure

1. Ouvrez Microsoft SQL Server Management Studio pour Microsoft SQL Server 2016 et connectez-vous à l’instance de SQL Server contenant la base de données utilisateur dont vous effectuez la migration vers une machine virtuelle Azure.
2. Cliquez avec le bouton droit de la base de données que vous migrez, pointez sur Tâches, puis cliquez sur Déployer sur une machine virtuelle Microsoft Azure.

	![Démarrer l’Assistant](./media/virtual-machines-windows-migrate-sql/start-wizard.png)

3. Dans la page Introduction, cliquez sur Suivant.
4. Dans la page Paramètres de la source, connectez-vous à l’instance SQL Server contenant la base de données que vous allez migrer vers une machine virtuelle Azure.
5. Spécifiez un emplacement temporaire pour les fichiers de sauvegarde. Si vous vous connectez à un serveur distant, vous devez spécifier un lecteur réseau.

	![Paramètres de la source](./media/virtual-machines-windows-migrate-sql/source-settings.png)

6. Cliquez sur Suivant.
7. Dans la page de connexion à Microsoft Azure, cliquez sur Se connecter et connectez-vous à votre compte Azure.
8. Sélectionnez l’abonnement que vous souhaitez utiliser, puis cliquez sur Suivant.

	![Authentification dans Azure](./media/virtual-machines-windows-migrate-sql/azure-signin.png)

9. Dans la page Paramètres de déploiement, vous pouvez spécifier un nouveau nom de service Cloud ou un nom existant et le nom de la machine virtuelle :

 - Spécifiez un nouveau nom de service cloud et un nom de machine virtuelle pour créer un service cloud avec une nouvelle machine virtuelle Azure à l’aide d’une image de la galerie SQL Server 2014 ou de SQL Server 2016.

     - Si vous spécifiez un nouveau nom de service cloud, spécifiez le compte de stockage que vous allez utiliser.

     - Si vous spécifiez un nom de service cloud existant, le compte de stockage est récupéré et saisi pour vous.

 - Spécifiez un nom de service cloud existant et le nouveau nom de machine virtuelle pour créer une machine virtuelle Azure dans un service cloud existant. Spécifiez uniquement une image de la galerie SQL Server 2014 ou SQL Server 2016.
 - Spécifiez un nom de service cloud existant et un nom de machine virtuelle pour utiliser une machine virtuelle Azure existante. Il doit s’agir d’une image générée à l’aide d’une image de la galerie SQL Server 2014 ou SQL Server 2016.

		![Deploymnent Settings](./media/virtual-machines-windows-migrate-sql/deployment-settings.png)

10. Cliquez sur Paramètres
  - Si vous avez spécifié un nom de service cloud existant et un nom de machine virtuelle, vous devrez fournir le nom d’utilisateur et un mot de passe.

		![Azure machine settings](./media/virtual-machines-windows-migrate-sql/azure-machine-settings.png)

	- Si vous avez spécifié un nouveau nom de machine virtuelle, vous devrez sélectionner une image à partir de la liste des images de la galerie et fournir les informations suivantes :
	  - Image : sélectionnez uniquement SQL Server 2014 ou SQL Server 2016
		- Nom d’utilisateur
		- Nouveau mot de passe
		- Confirmer le mot de passe
		- Lieu
		- Taille.
 	- En outre, cliquez ici pour accepter le certificat généré automatiquement pour cette nouvelle Machine virtuelle Microsoft Azure, puis cliquez sur OK.

	![Nouveaux paramètres de machine Azure](./media/virtual-machines-windows-migrate-sql/azure-new-machine-settings.png)

11. Spécifiez le nom de la base de données cible s’il est différent du nom de la base de données source. Si la base de données cible existe déjà, le système incrémentera automatiquement le nom de la base de données plutôt que de remplacer la base de données existante.
12. Ensuite, cliquez sur Suivant, puis sur Terminer.

	![Résultats](./media/virtual-machines-windows-migrate-sql/results.png)

13. Lorsque l’Assistant a terminé, connectez-vous à votre machine virtuelle et vérifiez que votre base de données a été transférée.
14. Si vous avez créé une machine virtuelle, configurez la machine virtuelle Azure et l’instance de SQL Server en suivant les étapes de [connexion à l’instance de machine virtuelle SQL Server à partir de SSMS sur un autre ordinateur](virtual-machines-windows-sql-connect.md).

## Sauvegarde sur un fichier et copie dans la machine virtuelle et restauration

Utilisez cette méthode lorsque vous ne pouvez pas utiliser l’Assistant Déploiement d’une base de données SQL Server sur une machine virtuelle Microsoft Azure soit parce que vous effectuez une migration vers une version de SQL Server antérieure à SQL Server 2014, soit parce que votre fichier de sauvegarde est supérieur à 1 To. Si le volume de votre fichier de sauvegarde est supérieur à 1 To, vous devez le partager parce que la taille maximum d’un disque de machine virtuelle est de 1 To. Utilisez les étapes générales suivantes pour faire migrer une base de données utilisateur à l’aide de cette méthode manuelle :

1.	Effectuez une sauvegarde complète de la base de données vers un emplacement sur site.
2.	Créez ou téléchargez une machine virtuelle avec la version de SQL Server souhaitée.
3.	Configurez la connectivité en fonction de vos besoins. Consultez [Se connecter à une machine virtuelle SQL Server sur Azure (Resource Manager)](virtual-machines-windows-sql-connect.md).
4.	Copiez vos fichiers de sauvegarde à votre machine virtuelle à l’aide du Bureau à distance, de l’Explorateur Windows ou de la commande de copie à partir d’une invite de commandes.

## Sauvegarde vers URL et restauration

Utilisez la méthode [Sauvegarde vers URL](https://msdn.microsoft.com/library/dn435916.aspx) lorsque vous ne pouvez pas utiliser l’Assistant Déploiement d’une base de données SQL Server sur une machine virtuelle Microsoft Azure parce que le fichier de sauvegarde est supérieur à 1 To et que vous effectuez une migration depuis et vers SQL Server 2016. Pour les bases de données inférieures à 1 To ou exécutant une version de SQL Server antérieure à SQL Server 2016, l’utilisation de l’Assistant est recommandée. Avec SQL Server 2016, les sauvegardes agrégées par bandes sont prises en charge sont recommandés pour les performances et doit dépasser les limites de taille par objet blob. Pour les bases de données très volumineuses, il est recommandé d’utiliser le [Service d’importation/exportation Windows](../storage/storage-import-export-service.md).

## Détacher et copier vers une URL, puis rattacher à partir d’une URL

Utilisez cette méthode lorsque vous envisagez de [stocker ces fichiers à l’aide du service de stockage d’objets Blob Azure](https://msdn.microsoft.com/library/dn385720.aspx) et de les rattacher à SQL Server s’exécutant dans une machine virtuelle Azure, en particulier avec les bases de données très volumineuses. Utilisez les étapes générales suivantes pour faire migrer une base de données utilisateur à l’aide de cette méthode manuelle :

1.	Détachez les fichiers de base de données de l’instance de base de données sur site.
2.	Copiez les fichiers de la base de données détachée dans le stockage blob Azure à l’aide de l’[utilitaire de ligne de commande AZCopy](../storage/storage-use-azcopy.md).
3.	Attachez les fichiers de base de données à partir de l’URL Azure à l’instance de SQL Server dans la machine virtuelle Azure.

## Effectuez une conversion vers une machine virtuelle et téléchargez vers une URL et déployez en tant que nouvelle machine virtuelle

Cette méthode permet de migrer toutes les bases de données système et utilisateur dans une instance de SQL Server sur site vers une machine virtuelle Azure. Utilisez les étapes générales suivantes pour exécuter la migration d’une instance de SQL Server entière à l’aide de cette méthode manuelle :

1.	Convertissez les machines physiques ou virtuelles en disques durs virtuels Hyper-V à l’aide du [Convertisseur Microsoft Virtual Machine](http://technet.microsoft.com/library/dn873998.aspx).
2.	Télécharger les fichiers VHD sur Azure Storage à l’aide de l’[applet de commande Add-AzureVHD](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx).
3.	Déployer une nouvelle machine virtuelle en utilisant le VHD téléchargé.

> [AZURE.NOTE] Pour migrer une application entière, envisagez d’utiliser [Azure Site Recovery](../site-recovery/site-recovery-overview.md)].

## Livraison de disque dur

Vous pouvez utiliser la [méthode de service Import/Export Azure](../storage/storage-import-export-service.md) pour transférer de grandes quantités de données de fichiers vers le stockage d’objets blob Azure lorsque le coût de l’opération de téléchargement via le réseau est prohibitif, voire irréalisable. Avec ce service, vous envoyez un ou plusieurs disques durs contenant ces données à un centre de données Azure, où vos données seront téléchargées sur votre compte de stockage.

## Étapes suivantes

Pour plus d’informations sur l’exécution de SQL Server sur des machines virtuelles Azure, voir [SQL Server sur les machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-overview.md).

Pour obtenir des instructions sur la création d’une machine virtuelle Azure SQL Server à partir d’une image capturée, consultez la page [Tips & Tricks on ‘cloning’ Azure SQL virtual machines from captured images](https://blogs.msdn.microsoft.com/psssql/2016/07/06/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images/) (Trucs et astuces sur le « clonage » des machines virtuelles Azure SQL à partir d’images capturées) du blog des ingénieurs CSS SQL Server.

<!---HONumber=AcomDC_0928_2016-->