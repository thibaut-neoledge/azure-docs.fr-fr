<properties 
   pageTitle="Se connecter à une machine virtuelle SQL Server sur Azure"
	description="Cette rubrique décrit comment se connecter à SQL Server exécuté sur une machine virtuelle dans Azure. Les scénarios diffèrent selon la configuration réseau et l’emplacement du client."
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
	ms.date="08/18/2015"
	ms.author="jroth"/>

# Se connecter à une machine virtuelle SQL Server sur Azure
 
## Vue d'ensemble

La configuration de la connectivité à un serveur SQL Server exécuté sur une machine virtuelle Azure n’est pas très différente de la procédure requise pour une instance de serveur SQL Server locale. Les étapes de configuration impliquant le pare-feu, l’authentification et les connexions à une base de données sont également nécessaires.

Cependant, certains aspects de la connectivité à SQL Server sont propres aux machines virtuelles Azure. Cet article aborde certains [scénarios de connectivité générale](#connection-scenarios) et fournit une [procédure détaillée pour configurer la connectivité à SQL Server dans une machine virtuelle Azure](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

>[AZURE.NOTE]Cet article décrit la connectivité. Pour une vue d’ensemble complète de la configuration et de la connectivité, consultez [Configuration d’une machine virtuelle SQL Server sur Azure](virtual-machines-provision-sql-server.md).

## Scénarios de connexion

La méthode utilisée par un client pour se connecter à un serveur SQL Server exécuté sur une machine virtuelle diffère selon l’emplacement du client et la configuration de la machine ou du réseau. Ces scénarios sont les suivants :

- [se connecter à SQL Server dans le même service de cloud computing](#connect-to-sql-server-in-the-same-cloud-service)
- [Se connecter à SQL Server via Internet](#connect-to-sql-server-over-the-internet)
- [se connecter à SQL Server dans le même réseau virtuel](#connect-to-sql-server-in-the-same-virtual-network)

### se connecter à SQL Server dans le même service de cloud computing

Plusieurs machines virtuelles peuvent être créées dans le même service cloud. Pour comprendre ce scénario impliquant des machines virtuelles, consultez la page [Connexion d’ordinateurs virtuels à un réseau virtuel ou un service cloud](cloud-services-connect-virtual-machine.md).

Commencez par effectuer la [procédure décrite dans cet article pour configurer la connectivité](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm). Notez qu’il est inutile de configurer un point de terminaison public si vous devez connecter des machines situées dans le même service cloud.

Vous pouvez utiliser le **nom d’hôte** de la machine virtuelle dans la chaîne de connexion du client. Il s’agit du nom que vous avez donné à votre machine virtuelle lors de sa création. Par exemple, si votre machine virtuelle SQL se nomme **mysqlvm** et le nom DNS de service cloud est **mycloudservice.cloudapp.net**, une machine virtuelle cliente du même service cloud peut utiliser la chaîne de connexion suivante pour se connecter :

	"Server=mysqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

### Se connecter à SQL Server via Internet

Si vous souhaitez vous connecter à votre moteur de base de données SQL Server à partir d’Internet, vous devez créer un point de terminaison de machine virtuelle pour les communications TCP entrantes. Dans cette étape de configuration Azure, le trafic du port TCP entrant est dirigé vers un port TCP accessible à la machine virtuelle.

Commencez par effectuer la [procédure décrite dans cet article pour configurer la connectivité](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm). Un client ayant accès à Internet peut ensuite se connecter à l’instance SQL Server en indiquant le nom DNS du service cloud (par exemple **mycloudservice.cloudapp.net**) et le point de terminaison de la machine virtuelle (par exemple **57500**).

	"Server=mycloudservice.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Même si cette méthode permet aux clients de se connecter via Internet, cela ne signifie pas que tout le monde peut se connecter à votre serveur SQL Server. Les clients externes doivent entrer les nom d’utilisateur et mot de passe corrects. Pour renforcer la sécurité, n’utilisez pas le port commun 1433 pour le point de terminaison de la machine virtuelle publique. Dans la mesure du possible, vous pouvez également envisager d’ajouter une liste de contrôle d’accès réseau (ACL) sur votre point de terminaison pour limiter le trafic uniquement aux clients que vous autorisez. Pour obtenir des instructions sur l’utilisation de listes ACL avec des points de terminaison, consultez la page [Gestion de l’ACL sur un point de terminaison](virtual-machines-set-up-endpoints.md#manage-the-acl-on-an-endpoint).

>[AZURE.NOTE]Il est important de noter que lorsque vous utilisez cette technique pour communiquer avec SQL Server, toutes les données renvoyées sont considérées comme du trafic sortant depuis le centre de données. Elles sont donc soumises à la [tarification sur les transferts de données sortantes](http://azure.microsoft.com/pricing/details/data-transfers) standard. C’est également le cas lorsque vous utilisez cette technique à partir d’une machine ou d’un service cloud différent au sein d’un même centre de données Azure, car le trafic passe toujours par l’équilibreur de charge public d’Azure.

### Se connecter à SQL Server dans le même réseau virtuel

Un [réseau virtuel](..\virtual-network\virtual-networks-overview.md) offre des scénarios supplémentaires. Vous pouvez connecter les machines virtuelles au sein d’un même réseau virtuel, même si elles existent dans différents services cloud. De plus, un [VPN de site à site](../vpn-gateway/vpn-gateway-site-to-site-create.md) permet de créer une architecture hybride qui connecte les machines virtuelles aux réseaux et machines locaux.

Les réseaux virtuels vous permettent également d’associer vos machines virtuelles Azure à un domaine. Il s’agit de la seule façon d’utiliser l’authentification Windows pour SQL Server. Les autres scénarios de connexion requièrent l’authentification SQL avec des noms d’utilisateur et mots de passe.

Commencez par effectuer la [procédure décrite dans cet article pour configurer la connectivité](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm). Si vous souhaitez configurer un environnement de domaine et l’authentification Windows, il est inutile d’effectuer la procédure de configuration de l’authentification SQL et des connexions décrite dans cet article. En outre, un point de terminaison public n’est pas nécessaire dans ce scénario.

En supposant que vous avez configuré le DNS, vous pouvez vous connecter à votre instance SQL Server en spécifiant le nom d’hôte de la machine virtuelle SQL Server dans la chaîne de connexion. L’exemple suivant part du principe que l’authentification Windows a également été configurée et que l’utilisateur a accès à l’instance SQL Server.

	"Server=mysqlvm;Integrated Security=true" 

Notez que dans ce scénario, vous pouvez également spécifier l’adresse IP de la machine virtuelle.

## Procédure de configuration de la connectivité SQL Server dans une machine virtuelle Azure

[AZURE.INCLUDE [Se connecter à SQL Server dans une machine virtuelle](../../includes/virtual-machines-sql-server-connection-steps.md)]

## Étapes suivantes

Pour obtenir des instructions de configuration en plus de ces étapes de connectivité, consultez la page [Configuration d’une machine virtuelle SQL Server sur Azure](virtual-machines-provision-sql-server.md).

Si vous envisagez également d’utiliser des groupes de disponibilité AlwaysOn pour la haute disponibilité et la récupération d’urgence, nous vous recommandons d’implémenter un écouteur. Les clients de base de données se connectent à l’écouteur plutôt que directement à l’une des instances SQL Server. L’écouteur achemine les clients vers le réplica principal du groupe de disponibilité. Pour plus d’informations, consultez la page [Configuration d’un écouteur à équilibrage de charge interne pour des groupes de disponibilité AlwaysOn dans Azure](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md).

Il est important d’examiner toutes les recommandations de sécurité pour SQL Server exécuté sur une machine virtuelle Azure. Pour plus d'informations, consultez la page [Considérations relatives à la sécurité de SQL Server sur les machines virtuelles Azure](virtual-machines-sql-server-security-considerations.md).

Pour d’autres rubriques relatives à l’utilisation de SQL Server sur des machines virtuelles Azure, consultez la rubrique [SQL Server sur les machines virtuelles Azure](virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=August15_HO9-->