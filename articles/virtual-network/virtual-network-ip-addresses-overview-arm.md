<properties
   pageTitle="Adressage IP public et privé dans Azure Resource Manager | Microsoft Azure"
   description="En savoir plus sur l’adressage IP public et privé dans Azure Resource Manager"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/27/2016"
   ms.author="jdial" />

# Adresses IP dans Azure
Vous pouvez affecter des adresses IP à des ressources Azure pour communiquer avec d’autres ressources Azure, votre réseau local et Internet. Les adresses IP que vous pouvez utiliser dans Azure sont de deux types :

- **Adresses IP publiques** : elles sont utilisées pour la communication avec Internet, notamment les services Azure accessibles au public.
- **Adresses IP privées** : elles sont utilisées pour la communication au sein d’un réseau virtuel Azure (VNet) et de votre réseau local lorsque vous utilisez une passerelle VPN ou un circuit ExpressRoute pour étendre votre réseau à Azure.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-network-ip-addresses-overview-classic.md).

Si vous êtes familiarisé avec le modèle de déploiement classique, vérifiez les [différences d’adressage IP entre les déploiements classiques et Resource Manager](virtual-network-ip-addresses-overview-classic.md#Differences-between-Resource-Manager-and-classic-deployments).

## Adresses IP publiques
Les adresses IP publiques permettent aux ressources Azure de communiquer avec Internet et des services Azure accessibles au public, tels que le [Cache Redis Azure](https://azure.microsoft.com/services/cache/), [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), les [bases de données SQL](../sql-database/sql-database-technical-overview.md) et [Azure Storage](../storage/storage-introduction.md).

Dans Azure Resource Manager, une [adresse IP publique](resource-groups-networking.md#public-ip-address) est une ressource ayant ses propres propriétés. Vous pouvez associer une ressource d’adresse IP publique à toutes les ressources suivantes :

- Machines virtuelles
- Équilibreurs de charge accessibles sur Internet
- Passerelles VPN
- Passerelles d’application

### Méthode d’allocation
L’allocation d’une adresse IP à une ressource IP *publique* est possible à l’aide de deux méthodes : *dynamique* ou *statique*. La méthode d’allocation par défaut est *dynamique*. Dans ce cas, une adresse IP n’est **pas** allouée au moment de sa création. Au lieu de cela, l’adresse IP publique est allouée lorsque vous démarrez (ou créez) la ressource associée (telle qu’une machine virtuelle ou un équilibreur de charge). L’adresse IP est libérée lorsque vous arrêtez (ou supprimez) la ressource. Ainsi, l’adresse IP change quand vous arrêtez et démarrez une ressource.

Pour vous assurer que l’adresse IP de la ressource associée ne change pas, vous pouvez définir explicitement à la méthode d’allocation sur *statique*. Dans ce cas, une adresse IP est affectée immédiatement. Elle est libérée uniquement lorsque vous supprimez la ressource ou modifiez sa méthode d’allocation en *dynamique*.

>[AZURE.NOTE] Même lorsque vous définissez la méthode d’allocation sur *statique*, vous ne pouvez pas spécifier l’adresse IP réelle affectée à la *ressource IP publique*. Au lieu de cela, elle est allouée à partir d’un pool d’adresses IP disponibles dans l’emplacement Azure dans lequel la ressource est créée.

Des adresses IP publiques statiques sont fréquemment utilisées dans les cas suivants :

- Les utilisateurs finaux doivent mettre à jour les règles de pare-feu pour communiquer avec vos ressources Azure.
- La résolution de noms DNS est telle qu’une modification de l’adresse IP nécessiterait une mise à jour des enregistrements A.
- Vos ressources Azure communiquent avec d’autres applications ou services qui utilisent un modèle de sécurité basé sur une adresse IP.
- Vous utilisez des certificats SSL liés à une adresse IP.

>[AZURE.NOTE] La liste des plages d’adresses IP à partir desquelles les adresses IP publiques (statiques/dynamiques) sont affectées à des ressources Azure est publiée dans [Plages d’adresses IP du centre de données Azure](https://www.microsoft.com/download/details.aspx?id=41653).

### Résolution de nom d’hôte DNS
Vous pouvez spécifier une étiquette de nom de domaine DNS pour une ressource IP publique, qui crée un mappage pour *domainnamelabel*.*emplacement*. cloudapp.azure.com à l’adresse IP publique dans les serveurs DNS gérés par Azure. Par exemple, si vous créez une ressource IP publique avec **contoso** en tant que *domainnamelabel* dans l’*emplacement* Azure **Ouest des États-Unis**, le nom de domaine complet (FQDN) **contoso.westus.cloudapp.azure.com** résout l’adresse IP publique de la ressource. Vous pouvez utiliser ce nom de domaine complet pour créer un enregistrement CNAME de domaine personnalisé qui pointe vers l’adresse IP publique dans Azure.

>[AZURE.IMPORTANT] Chaque étiquette de nom de domaine créée doit être unique dans son emplacement Azure.

### Machines virtuelles
Vous pouvez associer une adresse IP publique à une machine virtuelle [Windows](../virtual-machines/virtual-machines-windows-about.md) ou [Linux](../virtual-machines/virtual-machines-linux-about.md) en l’affectant à son **interface réseau**. Dans le cas d’une machine virtuelle à plusieurs réseaux, vous pouvez l’affecter uniquement à l’interface réseau *principale*. Vous pouvez affecter une adresse IP publique dynamique ou statique à une machine virtuelle.

### Équilibreurs de charge accessibles sur Internet
Vous pouvez associer une adresse IP publique à un [Azure Load Balancer (équilibreur de charge Azure)](../load-balancer/load-balancer-overview.md) en l’affectant à la configuration **frontale** de l’équilibreur de charge. Cette adresse IP publique sert d’adresse IP virtuelle (VIP) à charge équilibrée. Vous pouvez affecter une adresse IP publique dynamique ou statique à un équilibreur de charge frontal. Vous pouvez également affecter plusieurs adresses IP publiques à un équilibreur de charge frontal, ce qui permet des scénarios avec [plusieurs adresses IP virtuelles](../load-balancer/load-balancer-multivip.md) tels qu’un environnement mutualisé avec des sites web SSL.

### Passerelles VPN
Une [passerelle VPN Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md) est utilisée pour connecter un réseau virtuel Azure (VNet) à d’autres réseaux virtuels Azure ou à un réseau local. Vous devez affecter une adresse IP publique à sa **configuration IP** pour lui permettre de communiquer avec le réseau distant. Actuellement, vous pouvez affecter une adresse IP publique *dynamique* uniquement à une passerelle VPN.

### Passerelles d’application
Vous pouvez associer une adresse IP publique à une [Application Gateway](../application-gateway/application-gateway-introduction.md) Azure en l’affectant à la configuration **frontale** de la passerelle. Cette adresse IP publique sert d’adresse IP virtuelle à équilibrage de charge. Actuellement, vous pouvez uniquement affecter une adresse IP publique *dynamique* à une configuration frontale de passerelle d’application.

### Aperçu
Le tableau ci-dessous présente la propriété spécifique par le biais de laquelle une adresse IP publique peut être associée à une ressource de niveau supérieur, ainsi que les méthodes d’allocation possibles (dynamique ou statique) utilisables.

|Ressources de niveau supérieur|Association d’adresse IP|Dynamique|Statique|
|---|---|---|---|
|Machine virtuelle|Interface réseau|Oui|Oui|
|Équilibrage de charge|Configuration frontale|Oui|Oui|
|Passerelle VPN|Configuration IP de la passerelle|Oui|Non|
|Passerelle d’application|Configuration frontale|Oui|Non|

## Adresses IP privées
Les adresses IP privées permettent aux ressources Azure de communiquer avec d’autres ressources dans un [réseau virtuel](virtual-networks-overview.md) ou dans un réseau local par le biais d’une passerelle VPN ou d’un circuit ExpressRoute, sans utiliser d’adresse IP accessible via Internet.

Dans le modèle de déploiement Azure Resource Manager, une adresse IP privée est associée aux types ressources Azure suivants.

- Machines virtuelles
- Équilibreurs de charge interne (ILB)
- Passerelles d’application

### Méthode d’allocation
Une adresse IP privée est allouée à partir de la plage d’adresses du sous-réseau auquel la ressource est attachée. La plage d’adresses du sous-réseau lui-même fait partie de la plage d’adresses du réseau virtuel.

Il existe deux méthodes d’allocation d’adresses IP : *dynamique* ou *statique*. La méthode d’allocation par défaut est *dynamique*, où l’adresse IP est allouée automatiquement à partir du sous-réseau de la ressource (à l’aide de DHCP). Cette adresse IP peut changer lorsque vous arrêtez et démarrez la ressource.

Vous pouvez définir la méthode d’allocation *statique* pour vous assurer que l’adresse IP ne change pas. Dans ce cas, vous devez également fournir une adresse IP valide qui fait partie du sous-réseau de la ressource.

Les adresses IP privées statiques sont couramment utilisées pour :

- les ordinateurs virtuels qui agissent en tant que contrôleurs de domaine ou serveurs DNS ;
- les ressources qui nécessitent des règles de pare-feu utilisant des adresses IP ;
- des ressources auxquelles d’autres applications/ressources accèdent via une adresse IP.

### Machines virtuelles
Une adresse IP privée est affectée à l’**d’interface réseau** d’une machine virtuelle [Windows](../virtual-machines/virtual-machines-windows-about.md) ou [Linux](../virtual-machines/virtual-machines-linux-about.md). Dans le cas d’une machine virtuelle comportant plusieurs interfaces réseau, une adresse IP privée est affectée à chacune d’elles. Vous pouvez spécifier la méthode d’allocation dynamique ou statique pour une interface réseau.

#### Résolution de nom d’hôte DNS interne (pour les machines virtuelles)
Toutes les machines virtuelles Azure sont configurées avec des [serveurs DNS gérés par Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) par défaut, sauf si vous configurez explicitement des serveurs DNS personnalisés. Ces serveurs DNS assurent la résolution de noms interne pour les machines virtuelles qui résident dans le même réseau virtuel.

Lorsque vous créez une machine virtuelle, un mappage du nom d’hôte à son adresse IP privée est ajouté aux serveurs DNS gérés par Azure. Dans le cas d’une machine virtuelle dotée de plusieurs interfaces réseau, le nom d’hôte est mappé sur l’adresse IP privée de l’interface réseau principale.

Les machines virtuelles configurées avec des serveurs DNS gérés par Azure peuvent résoudre les noms d’hôtes de toutes les machines virtuelles figurant au sein de leur réseau virtuel pour les adresses IP privées.

### Équilibreurs de charge internes (ILB) et Application Gateway
Vous pouvez affecter une adresse IP privée à la configuration **frontale** d’un [équilibreur de charge interne Azure](../load-balancer/load-balancer-internal-overview.md) (ILB) ou d’une [passerelle d’application Azure](../application-gateway/application-gateway-introduction.md). Cette adresse IP privée sert de point de terminaison interne, accessible uniquement aux ressources de son réseau virtuel (VNet), et de réseaux distants connectés au réseau virtuel. Vous pouvez affecter une adresse IP privée statique ou dynamique à la configuration frontale.

### Aperçu
Le tableau ci-dessous présente la propriété spécifique par le biais de laquelle une adresse IP privée peut être associée à une ressource de niveau supérieur, ainsi que les méthodes d’allocation possible (dynamique ou statique) utilisables.

|Ressources de niveau supérieur|Association d’adresse IP|Dynamique|Statique|
|---|---|---|---|
|Machine virtuelle|Interface réseau|Oui|Oui|
|Équilibrage de charge|Configuration frontale|Oui|Oui|
|Passerelle d’application|Configuration frontale|Oui|Oui|

## Limites

Les limites imposées pour l’adressage d’adresses IP sont indiquées dans l’ensemble des [limites pour la mise en réseau](azure-subscription-service-limits.md#networking-limits) dans Azure. Ces limites sont exprimées par région et par abonnement. Vous pouvez [contacter le support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour augmenter les limites par défaut dans les limites maximum en fonction des besoins de votre entreprise.

## Tarification

Dans la plupart des cas, les adresses IP publiques sont gratuites. Il existe un coût nominal pour utiliser des adresses IP publiques supplémentaires et/ou statiques. Assurez-vous que vous comprenez la [tarification des adresses IP publiques](https://azure.microsoft.com/pricing/details/ip-addresses/).

## Étapes suivantes
- [Déployer une machine virtuelle avec une adresse IP publique statique à l’aide du portail Azure](virtual-network-deploy-static-pip-arm-portal.md)
- [Déployer une machine virtuelle avec une adresse IP publique statique à l’aide d’un modèle](virtual-network-deploy-static-pip-arm-template.md)
- [Déployer une machine virtuelle avec une adresse IP privée statique à l’aide du portail Azure](virtual-networks-static-private-ip-arm-pportal.md)

<!---HONumber=AcomDC_0810_2016-->