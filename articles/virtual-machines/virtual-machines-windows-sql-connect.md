<properties 
	pageTitle="Se connecter à une machine virtuelle SQL Server (Resource Manager) | Microsoft Azure"
	description="Cette rubrique utilise des ressources créées avec le modèle de déploiement classique, et explique comment se connecter à SQL Server s’exécutant sur une machine virtuelle dans Azure. Les scénarios diffèrent selon la configuration réseau et l’emplacement du client."
	services="virtual-machines-windows"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"    
	tags="azure-service-management"/>
<tags 
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="03/24/2016"
	ms.author="jroth" />

# Se connecter à une machine virtuelle SQL Server sur Azure (Resource Manager)

> [AZURE.SELECTOR]
- [Gestionnaire de ressources](virtual-machines-windows-sql-connect.md)
- [Classique](virtual-machines-windows-classic-sql-connect.md)

## Vue d'ensemble

La configuration de la connectivité à un serveur SQL Server exécuté sur une machine virtuelle Azure dans Resource Manager n’est pas très différente de la procédure requise pour une instance de serveur SQL Server locale. Les étapes de configuration impliquant le pare-feu, l’authentification et les connexions à une base de données sont également nécessaires.

Cependant, certains aspects de la connectivité à SQL Server sont propres aux machines virtuelles Azure. Cet article aborde certains [scénarios de connectivité générale](#connection-scenarios) et fournit une [procédure détaillée pour configurer la connectivité à SQL Server dans une machine virtuelle Azure](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm).

Cet article est consacré à la connexion à une machine virtuelle SQL Server à l’aide du modèle de Resource Manager. Pour une procédure pas-à-pas complète d’approvisionnement et de connectivité, voir [Approvisionnement d’une machine virtuelle SQL Server dans Azure](virtual-machines-windows-portal-sql-server-provision.md).

## Scénarios de connexion

La méthode utilisée par un client pour se connecter à un serveur SQL Server exécuté sur une machine virtuelle diffère selon l’emplacement du client et la configuration de la machine ou du réseau. Ces scénarios sont les suivants :

- [Se connecter à SQL Server via Internet](#connect-to-sql-server-over-the-internet)
- [se connecter à SQL Server dans le même réseau virtuel](#connect-to-sql-server-in-the-same-virtual-network)

### Se connecter à SQL Server via Internet

Si vous souhaitez vous connecter à votre moteur de base de données SQL Server à partir d’Internet, plusieurs étapes sont requises, telles que la configuration du pare-feu, l’activation de l’authentification SQL et la configuration de votre groupe de sécurité réseau que vous devez avoir comme règle de groupe de sécurité réseau pour autoriser le trafic TCP sur le port 1433.

Si vous utilisez le portail pour approvisionner une image de machine virtuelle SQL Server avec Resource Manager, ces étapes sont effectuées automatiquement lorsque vous sélectionnez **Public** comme option de connectivité SQL :

![](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity.png)

Si cela n’a pas été effectué lors de l’approvisionnement, vous pouvez configurer manuellement SQL Server et vos machines virtuelles en suivant les [étapes de configuration manuelle de la connectivité de cet article](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm).

Une fois cette opération effectuée, tout client disposant d’un accès à Internet peut se connecter à l’instance SQL Server en spécifiant l’adresse IP publique de la machine virtuelle ou le nom DNS attribué à cette adresse IP. Si le port SQL Server est le port 1433, il est inutile de le spécifier dans la chaîne de connexion.

	"Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Même si cette méthode permet aux clients de se connecter via Internet, cela ne signifie pas que tout le monde peut se connecter à votre serveur SQL Server. Les clients externes doivent entrer les nom d’utilisateur et mot de passe corrects. Pour renforcer la sécurité, vous pouvez éviter d’utiliser le port 1433 bien connu. Par exemple, si vous avez configuré SQL Server pour écouter sur le port 1500 et établi des règles de pare-feu et de groupe de sécurité réseau appropriées, vous pouvez vous connecter en ajoutant le numéro de port au nom du serveur, comme dans l’exemple suivant :

	"Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

>[AZURE.NOTE] Il est important de noter que lorsque vous utilisez cette technique pour communiquer avec SQL Server, toutes les données renvoyées sont considérées comme du trafic sortant depuis le centre de données. Elles sont donc soumises à la [tarification sur les transferts de données sortantes](https://azure.microsoft.com/pricing/details/data-transfers/) standard. C’est également le cas lorsque vous utilisez cette technique à partir d’une machine ou d’un service cloud différent au sein d’un même centre de données Azure, car le trafic passe toujours par l’équilibreur de charge public d’Azure.

### Se connecter à SQL Server dans le même réseau virtuel

Un [réseau virtuel](../virtual-network/virtual-networks-overview.md) offre des scénarios supplémentaires. Vous pouvez connecter des machines virtuelles au sein d’un même réseau virtuel, même si elles existent dans différents groupes de ressources. De plus, un [VPN de site à site](../vpn-gateway/vpn-gateway-site-to-site-create.md) permet de créer une architecture hybride qui connecte les machines virtuelles aux machines et réseaux locaux.

Les réseaux virtuels vous permettent également d’associer vos machines virtuelles Azure à un domaine. Il s’agit de la seule façon d’utiliser l’authentification Windows pour SQL Server. Les autres scénarios de connexion requièrent l’authentification SQL avec des noms d’utilisateur et mots de passe.

Si vous utilisez le portail pour approvisionner une image de machine virtuelle SQL Server avec Resource Manager, les règles de pare-feu appropriées pour la communication sur le réseau virtuel sont configurées lorsque vous sélectionnez **Privé** comme option de connectivité SQL. Si cela n’a pas été effectué lors de l’approvisionnement, vous pouvez configurer manuellement SQL Server et vos machines virtuelles en suivant les [étapes de configuration manuelle de la connectivité de cet article](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm). Néanmoins, si vous envisagez de configurer un environnement de domaine et l’authentification Windows, il est inutile d’effectuer la procédure de configuration de l’authentification SQL et des connexions décrite dans cet article. Il est également inutile de configurer des règles du groupe de sécurité réseau pour l’accès via internet.

En supposant que vous ayez configuré le DNS sur votre réseau virtuel, vous pouvez vous connecter à votre instance SQL Server en spécifiant le nom d’ordinateur de la machine virtuelle SQL Server dans la chaîne de connexion. L’exemple suivant part également du principe que l’authentification Windows a également été configurée et que l’utilisateur a accès à l’instance SQL Server.

	"Server=mysqlvm;Integrated Security=true" 

Notez que dans ce scénario, vous pouvez également spécifier l’adresse IP de la machine virtuelle.

## Procédure de configuration manuelle de la connectivité SQL Server dans une machine virtuelle Azure

Les étapes suivantes montrent comment configurer manuellement la connectivité à l’instance SQL Server, puis se connecter éventuellement via Internet à l’aide de SQL Server Management Studio (SSMS). Notez que nombre de ces étapes sont effectuées automatiquement si vous sélectionnez les options de connectivité SQL Server appropriées dans le portail.

Avant de pouvoir vous connecter à l’instance de SQL Server à partir d’une autre machine virtuelle ou d’Internet, vous devez effectuer les tâches suivantes, comme indiqué dans les sections ci-dessous :

- [Ouvrir des ports TCP dans le pare-feu Windows](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
- [Configurer SQL Server pour l’écoute du protocole TCP](#configure-sql-server-to-listen-on-the-tcp-protocol)
- [Configurer SQL Server pour l’authentification en mode mixte](#configure-sql-server-for-mixed-mode-authentication)
- [Créer des connexions d’authentification SQL Server](#create-sql-server-authentication-logins)
- [Configurer un nom DNS pour l’adresse IP publique](#configure-a-dns-label-for-the-public-ip-address)
- [Se connecter au moteur de base de données à partir d’un autre ordinateur](#connect-to-the-database-engine-from-another-computer)

[AZURE.INCLUDE [Se connecter à SQL Server dans une machine virtuelle](../../includes/virtual-machines-sql-server-connection-steps.md)]

[AZURE.INCLUDE [Se connecter à SQL Server dans une machine virtuelle (Resource Manager)](../../includes/virtual-machines-sql-server-connection-steps-resource-manager-nsg-rule.md)]

[AZURE.INCLUDE [Se connecter à SQL Server dans une machine virtuelle (Resource Manager)](../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## Étapes suivantes

Pour obtenir des instructions d’approvisionnement en plus de ces étapes de connectivité, voir [Approvisionnement d’une machine virtuelle SQL Server dans Azure](virtual-machines-windows-portal-sql-server-provision.md).

Il est important d’examiner toutes les recommandations de sécurité pour SQL Server exécuté sur une machine virtuelle Azure. Pour plus d’informations, consultez [Considérations relatives à la sécurité de SQL Server sur les machines virtuelles Azure](virtual-machines-windows-sql-security.md).

Pour d’autres rubriques relatives à l’utilisation de SQL Server sur des machines virtuelles Azure, voir [SQL Server sur les machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-overview.md).

<!---HONumber=AcomDC_0601_2016-->