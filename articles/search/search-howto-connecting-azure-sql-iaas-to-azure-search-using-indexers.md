<properties 
	pageTitle="Configurer une connexion à partir d’un indexeur Azure Search à SQL Server sur une machine virtuelle Azure | Microsoft Azure | Indexeurs" 
	description="Activer les connexions chiffrées et configurer le pare-feu pour autoriser les connexions à SQL Server sur une machine virtuelle Azure à partir d’un indexeur sur Azure Search." 
	services="search" 
	documentationCenter="" 
	authors="jack4it" 
	manager="pablocas" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="09/06/2016" 
	ms.author="jackma"/>

# Configurer une connexion à partir d’un indexeur Azure Search à SQL Server sur une machine virtuelle Azure

Comme indiqué dans [Connexion d’Azure SQL Database à Azure Search à l’aide d’indexeurs](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md#frequently-asked-questions), la création d’indexeurs dans **SQL Server sur des machines virtuelles Azure** (ou **machines virtuelles SQL Azure** en abrégé) est prise en charge par Azure Search, mais il existe deux conditions préalables liées à la sécurité qu’il faut résoudre en premier lieu.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## Activer des connexions chiffrées

Azure Search requiert une connexion sécurisée pour lire les données de votre base de données. Cela signifie que vous devez activer les connexions chiffrées sur votre machine virtuelle SQL Azure en configurant un certificat SSL.

Les étapes d’activation des connexions chiffrées dans SQL Server sont documentées dans la rubrique [Activer des connexions chiffrées au moteur de base de données](https://msdn.microsoft.com/library/ms191192.aspx), mais pour les connexions Internet publiques, telle qu’une connexion à partir d’Azure Search à une machine virtuelle SQL Azure, des exigences supplémentaires sont requises.

### Indiquer un nom de domaine complet dans le certificat SSL

Le nom du sujet du certificat SSL doit être le nom de domaine complet (ou **FQDN**) de la machine virtuelle SQL Azure. Il s’agit du même nom de domaine complet que celui que vous allez spécifier dans la chaîne de connexion de base de données lors de la création d’une source de données dans votre service de recherche. Un nom de domaine complet a le format `<your-VM-name>.<region>.cloudapp.azure.com` pour les machines virtuelles **Resource Manager**. Si vous avez des machines virtuelles **classiques**, il prend la forme `<your-cloud-service-name.cloudapp.net>`. Vous pouvez trouver le nom de domaine complet de votre machine virtuelle SQL Azure en tant que nom/étiquette DNS dans le [portail Azure](https://portal.azure.com/).

### Utiliser REGEDIT pour configurer le certificat SSL

SQL Server Configuration Manager n’est pas en mesure d’afficher le certificat SSL du FQDN dans la liste déroulante **Certificat** comme décrit dans la documentation. La solution consiste à configurer le certificat SSL en modifiant cette clé de registre : **HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Microsoft SQL Server[MSSQL13.MSSQLSERVER]\\MSSQLServer\\SuperSocketNetLib\\Certificate**. La partie *[MSSQL13.MSSQLSERVER]* varie en fonction du nom de l’instance et la version de SQL Server. Cette clé doit être mise à jour avec **l’empreinte** du certificat SSL que vous avez installé sur la machine virtuelle SQL Azure.

### Accorder des autorisations pour le compte de service

Assurez-vous que le compte de service SQL Server a l’autorisation appropriée sur la clé privée du certificat SSL. Si vous ignorez cette étape, SQL Server ne démarre pas.

## Configurer le pare-feu pour autoriser les connexions à partir d’Azure Search

Il n’est pas inhabituel de configurer le pare-feu et le point de terminaison ou la liste de contrôle d’accès Azure correspondant(e) pour rendre votre machine virtuelle Azure accessible à partir d’autres parties. Vous avez sans doute déjà effectué cette configuration pour permettre à votre propre logique d’application de se connecter à votre machine virtuelle SQL Azure. C’est la même chose pour une connexion d’Azure Search à votre machine virtuelle SQL Azure. Si vous ne l’avez pas encore fait, voici quelques bonnes pratiques de sécurité à prendre en compte.

Si vous utilisez des machines virtuelles **Resource Manager**, consultez l’article [Connect to a SQL Server Virtual Machine on Azure using Resource Manager](../virtual-machines/virtual-machines-windows-sql-connect.md) (Se connecter à une machine virtuelle SQL Server sur Azure à l’aide de Resource Manager). Si vous utilisez encore des machines virtuelles **classiques**, consultez [Se connecter à une machine virtuelle SQL Server sur Azure Classic](../virtual-machines/virtual-machines-windows-classic-sql-connect.md).

### Restreindre l’accès à l’adresse IP du service de recherche

Pour les deux modèles de déploiement, lors de la configuration de connexions à partir d’Azure Search, nous vous recommandons fortement de restreindre l’accès à l’adresse IP de votre service de recherche dans la liste ACL au lieu de rendre vos machines virtuelles SQL Azure disponibles pour toutes les demandes de connexion. Vous pouvez facilement trouver l’adresse IP en exécutant une commande ping sur le nom de domaine complet (par exemple, `<your-search-service-name>.search.windows.net`) de votre service de recherche.

### Configurer une plage d'adresses IP

Notez que si votre service de recherche n'a qu’une seule unité de recherche (autrement dit, un réplica et une partition), l’adresse IP peut changer lors d’un redémarrage du service de routine. Pour éviter les échecs de connexion, vous devez spécifier la plage d’adresses IP de la région Azure où votre service de recherche est configuré. La liste des plages d’adresses IP à partir desquelles les adresses IP publiques sont allouées à des ressources Azure est publiée dans [Plages d’adresses IP du centre de données Azure](https://www.microsoft.com/download/details.aspx?id=41653).

### Inclure les adresses IP du portail Azure Search

En outre, si vous utilisez le portail Azure pour créer un indexeur, la logique de portail Azure Search doit également pouvoir accéder à votre machine virtuelle SQL Azure lors de la création. Exécutez la commande ping sur `stamp1.search.ext.azure.com` et `stamp2.search.ext.azure.com` pour trouver les adresses IP du portail Azure Search.

## Étapes suivantes

Une fois les exigences de configuration ci-dessus résolues, vous pouvez maintenant spécifier un serveur SQL Server sur une machine virtuelle Azure comme source de données pour un indexeur Azure Search. Pour plus d’informations, consultez [Connexion d’Azure SQL Database à Azure Search à l’aide d’indexeurs](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md).

<!---HONumber=AcomDC_0907_2016-->