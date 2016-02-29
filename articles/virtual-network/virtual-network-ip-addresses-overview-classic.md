<properties
   pageTitle="Adressage IP public et privé (classique) dans Azure | Microsoft Azure"
   description="En savoir plus sur l’adressage IP public et privé dans Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/11/2016"
   ms.author="telmos" />

# Adresses IP (classiques) dans Azure
Vous pouvez affecter des adresses IP à des ressources Azure pour communiquer avec d’autres ressources Azure, votre réseau local et Internet. Les adresses IP que vous pouvez utiliser dans Azure sont de deux types : publiques et privées.

Les adresses IP publiques sont utilisées pour la communication avec Internet, y compris les services Azure accessibles au public.

Les adresses IP privées sont utilisées pour la communication au sein d’un réseau virtuel Azure (VNet), un service cloud, et de votre réseau local lorsque vous utilisez une passerelle VPN ou un circuit ExpressRoute pour étendre votre réseau à Azure.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager deployment model](virtual-network-ip-addresses-overview-arm.md).

## Adresses IP publiques
Les adresses IP publiques permettent aux ressources Azure de communiquer avec Internet et des services Azure accessibles au public, tels que le [Cache Redis Azure](https://azure.microsoft.com/services/cache/), [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), les [bases de données SQL](../sql-database/sql-database-technical-overview.md) et [Azure Storage](../storage/storage-introduction.md).

Une adresse IP publique est associée aux types de ressources suivants :

- Services cloud
- Machines virtuelles (VM) IaaS
- Instances de rôle PaaS
- Passerelles VPN
- Passerelles d'application

### Méthode d’allocation
Lorsqu'une adresse IP publique doit être affectée à une ressource Azure, elle est *dynamiquement* allouée à partir d'un pool d'adresses IP publiques disponibles dans l'emplacement où la ressource est créée. Cette adresse IP est libérée lorsque la ressource est arrêtée. Dans le cas d'un service cloud, cela se produit lorsque toutes les instances de rôle sont arrêtées, ce qui peut être évité en utilisant une adresse IP *statique* (réservée) (voir [Services cloud](#Cloud-services)).

>[AZURE.NOTE] La liste des plages d’adresses IP à partir desquelles les adresses IP publiques sont allouées à des ressources Azure est publiée dans [Plages d’adresses IP du centre de données Azure](https://www.microsoft.com/download/details.aspx?id=41653).

### Résolution de nom d’hôte DNS
Lorsque vous créez un service cloud ou une VM IaaS, vous devez fournir un nom DNS du service cloud unique pour toutes les ressources dans Azure. Ce processus crée un mappage dans les serveurs DNS gérés par Azure pour *dnsname*.cloudapp.net à l'adresse IP publique de la ressource. Par exemple, lorsque vous créez un service cloud avec le nom DNS de service cloud **contoso**, le nom de domaine complet (FQDN) **contoso.cloudapp.net** sera résolu en une adresse IP publique (VIP) du service cloud. Vous pouvez utiliser ce nom de domaine complet pour créer un enregistrement CNAME de domaine personnalisé qui pointe vers l’adresse IP publique dans Azure.

### Services cloud
Un service cloud dispose toujours d’une adresse IP publique appelée adresse IP virtuelle (VIP). Vous pouvez créer des points de terminaison dans un service cloud pour associer des ports différents dans l'adresse IP virtuelle pour les ports internes sur les machines virtuelles et les instances de rôle au sein du service cloud.

Un service cloud peut contenir plusieurs machines virtuelles IaaS, ou instances de rôle PaaS, toutes exposées via la même adresse IP virtuelle de service cloud. Vous pouvez également affecter [plusieurs adresses IP virtuelles à un service cloud](../load-balancer/load-balancer-multivip.md), ce qui permet des scénarios avec plusieurs adresses IP virtuelles, tel qu’un environnement mutualisé avec des sites web SSL.

Vous pouvez vous assurer que l'adresse IP publique d'un service cloud reste la même, même si toutes les instances de rôle sont arrêtées, en utilisant une adresse IP publique *statique*, appelée [adresse IP réservée](virtual-networks-reserved-public-ip.md). Vous pouvez créer une ressource IP (réservée) statique dans un emplacement spécifique et l’affecter à un service cloud dans ce même emplacement. Vous ne pouvez pas spécifier l'adresse IP réelle pour l'adresse IP réservée. Elle est allouée à partir du pool d'adresses IP disponibles dans l'emplacement où elle a été créée. Cette adresse IP n'est pas libérée jusqu'à ce que vous la supprimiez explicitement.

Les adresses IP publiques (réservées) statiques sont fréquemment utilisées dans les scénarios où un service cloud :

- nécessite des règles de pare-feu pour être configuré par des utilisateurs finaux ;
- varie selon la résolution de noms DNS externe (une adresse IP dynamique nécessite la mise à jour des enregistrements A) ;
- consomme les services web externes qui utilisent le modèle de sécurité basé sur IP ;
- utilise des certificats SSL liés à une adresse IP.

>[AZURE.NOTE] Quand vous créez une machine virtuelle classique, un *service cloud* conteneur est créé par Azure, qui a une adresse IP virtuelle (VIP). Quand la création est effectuée via le portail, un *point de terminaison* RDP ou SSH par défaut est configuré par le portail pour que vous puissiez vous connecter à la machine virtuelle avec l’adresse IP virtuelle du service cloud. Cette adresse IP virtuelle du service cloud peut être réservée, ce qui fournit une adresse IP réservée pour la connexion à la machine virtuelle. Vous pouvez ouvrir des ports supplémentaires en configurant d’autres points de terminaison.

### Instances de rôle des machines virtuelles IaaS et PaaS
Vous pouvez affecter une adresse IP publique directement à une [machine virtuelle](../virtual-machines/virtual-machines-about.md) IaaS ou instance de rôle PaaS au sein d’un service cloud. Cela correspond à une adresse IP publique de niveau d'instance ([ILPIP](virtual-networks-instance-level-public-ip.md)). Cette adresse IP publique peut uniquement être dynamique.

>[AZURE.NOTE] Cela diffère de l’adresse IP virtuelle du service cloud, qui est un conteneur pour les machines virtuelles IaaS ou instances de rôle PaaS, dans la mesure où un service cloud peut contenir plusieurs machines virtuelles IaaS, ou instances de rôle PaaS, toutes exposées via la même adresse IP virtuelle de service cloud.

### Passerelles VPN
Une [passerelle VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) peut être utilisée pour connecter un réseau virtuel Azure (VNet) à d’autres réseaux virtuels Azure ou à des réseaux locaux. Une passerelle VPN est affectée *dynamiquement* à une adresse IP publique, ce qui permet la communication avec le réseau distant.

### Passerelles d'application
Une [passerelle d’application](../application-gateway/application-gateway-introduction.md) Azure peut être utilisée pour l’équilibrage de la charge Layer7 pour acheminer le trafic réseau basé sur HTTP. La passerelle d'application est affectée *dynamiquement* à une adresse IP publique, qui sert d'adresse IP virtuelle à charge équilibrée.

### Aperçu
Le tableau ci-dessous présente chaque type de ressource avec les méthodes d’allocation possible (statique/dynamique), et la possibilité d’affecter plusieurs adresses IP publiques.

|Ressource|Dynamique|Statique|Plusieurs adresses IP|
|---|---|---|---|
|Service cloud|Oui|Oui|Oui|
|Instance de rôle PaaS ou VM IaaS|Oui|Non|Non|
|Passerelle VPN|Oui|Non|Non|
|Passerelle d’application|Oui|Non|Non|

## Adresses IP privées
Les adresses IP privées permettent aux ressources Azure de communiquer avec d’autres ressources dans un service cloud ou un [réseau virtuel](virtual-networks-overview.md)(VNet), ou dans un réseau local via une passerelle VPN ou un circuit ExpressRoute, sans utiliser d’adresse IP accessible via Internet.

Dans le modèle de déploiement d'Azure Classic, une adresse IP privée peut être affectée aux ressources Azure suivantes :

- Instances de rôle des machines virtuelles IaaS et PaaS
- Équilibreur de charge interne
- Passerelle d’application

### Instances de rôle des machines virtuelles IaaS et PaaS
Les machines virtuelles (VM) créées avec le modèle de déploiement classique sont toujours placées dans un service cloud, de même que les instances de rôle PaaS. Le comportement des adresses IP privées sont donc identiques pour ces ressources.

Il est important de noter qu'un service cloud peut être déployé de deux manières :

- en tant que service cloud *autonome*, en dehors d’un réseau virtuel ;
- déployé dans un réseau virtuel.

#### Méthode d’allocation
Dans le cas d'un service cloud *autonome*, une adresse IP privée est allouée *dynamiquement* aux ressources à partir de la plage d’adresses IP privées du centre de données Azure. Elle peut être utilisée uniquement pour la communication avec d'autres machines virtuelles au sein du même service cloud. Cette adresse IP peut changer lorsque vous arrêtez et démarrez la ressource.

Dans le cas d'un service cloud déployé dans un réseau virtuel, des adresses IP privées sont allouées aux ressources à partir de la plage d'adresses des sous-réseaux associés (comme spécifié dans sa configuration de réseau). Cette ou ces adresses IP privées peuvent être utilisées pour la communication entre toutes les machines virtuelles du VNet.

Par ailleurs, en cas de services cloud au sein d'un VNet, une adresse IP privée est allouée *dynamiquement* (à l'aide de DHCP) par défaut. Cette adresse IP peut changer lorsque vous arrêtez et démarrez la ressource. Pour vous assurer que l'adresse IP reste la même, la méthode d'allocation que vous définissez doit être *statique*, et vous devez fournir une adresse IP valide dans la plage d'adresses correspondante.

Les adresses IP privées statiques sont couramment utilisées pour :

 - les machines virtuelles qui agissent en tant que contrôleurs de domaine ou serveurs DNS ;
 - les machines virtuelles qui nécessitent des règles de pare-feu utilisant des adresses IP ;
 - les machines virtuelles auxquelles d’autres applications accèdent via une adresse IP.

#### Résolution du nom d’hôte DNS interne
Toutes instances de rôle PaaS et les machines virtuelles Azure sont configurées avec des [serveurs DNS gérés par Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) par défaut, sauf si vous configurez explicitement des serveurs DNS personnalisés. Ces serveurs DNS assurent la résolution de noms interne pour les machines virtuelles et les instances de rôle qui résident dans le même VNet ou service cloud.

Lorsque vous créez une machine virtuelle, un mappage du nom d’hôte à son adresse IP privée est ajouté aux serveurs DNS gérés par Azure. Dans le cas d’une machine virtuelle dotée de plusieurs cartes d’interface réseau, le nom d’hôte est mappé à l’adresse IP privée de la carte réseau principale. Toutefois, ces informations de mappage sont limitées aux ressources au sein du même service cloud ou du même VNet.

Dans le cas d'un service cloud *autonome*, vous serez en mesure de résoudre les noms d'hôtes de toutes les instances de rôle ou de toutes les machines virtuelles dans le même service cloud. Dans le cas d'un service cloud dans un VNet, vous serez en mesure de résoudre les noms d'hôtes de toutes les instances de rôle ou de toutes les machines virtuelles dans le même VNet.

### Équilibreurs de charge internes (ILB) et passerelles d’application
Vous pouvez affecter une adresse IP privée à la configuration **frontale** d’un [équilibreur de charge interne Azure](../load-balancer/load-balancer-internal-overview.md) (ILB) ou d’une [passerelle d’application Azure](../application-gateway/application-gateway-introduction.md). Cette adresse IP privée sert de point de terminaison interne, accessible uniquement aux ressources de son réseau virtuel (VNet), et de réseaux distants connectés au réseau virtuel. Vous pouvez affecter une adresse IP privée statique ou dynamique à la configuration frontale. Vous pouvez également affecter plusieurs adresses IP privées pour permettre des scénarios avec plusieurs adresses IP virtuelles.

### Aperçu
Le tableau ci-dessous présente chaque type de ressource avec les méthodes d’allocation possible (statique/dynamique) et la possibilité d’affecter plusieurs adresses IP privées.

|Ressource|Dynamique|Statique|Plusieurs adresses IP|
|---|---|---|---|
|Machine virtuelle (dans un service cloud *autonome*)|Oui|Oui|Oui|
|Instance de rôle PaaS (dans un service cloud *autonome*)|Oui|Non|Oui|
|Instance de rôle PaaS ou VM (dans un VNet)|Oui|Oui|Oui|
|Équilibreur de charge interne frontal|Oui|Oui|Oui|
|Passerelle d’application frontale|Oui|Oui|Oui|

## Limites

Le tableau ci-dessous présente les limites imposées sur l'adressage IP dans Azure par abonnement. Vous pouvez [contacter le support technique](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour augmenter les limites par défaut dans les limites maximum en fonction des besoins de votre entreprise.

|Limite par défaut|Limite maximale|
|---|---|---|
|Adresses IP publiques (dynamiques)|5|contacter le support technique|
|Adresses IP publiques réservées|20|contacter le support technique|
|Adresse IP virtuelle publique par déploiement (service cloud)|5|contacter le support technique|
|Adresse IP virtuelle privée (ILB) par déploiement (service cloud)|1|1|

Assurez-vous de lire l’ensemble des [limite pour la mise en réseau](azure-subscription-service-limits.md#networking-limits) dans Azure.

## Tarification

Dans la plupart des cas, les adresses IP publiques sont gratuites. Il existe un coût nominal pour utiliser des adresses IP publiques supplémentaires et/ou statiques. Assurez-vous que vous comprenez la [tarification des adresses IP publiques](https://azure.microsoft.com/pricing/details/ip-addresses/).

## Différences entre les déploiements Resource Manager et Classic
Voici une comparaison des fonctionnalités d'adressage IP dans Resource Manager et dans le modèle de déploiement classique.

|Ressource|Classique|Gestionnaire de ressources|
|---|---|---|---|
|**Adresse IP publique**|MV|Appelée adresse ILPIP (dynamique uniquement)|Appelée adresse IP publique (dynamique ou statique)|
|Affectée à une machine virtuelle IaaS ou à une instance de rôle PaaS|Associée à la carte réseau de la machine virtuelle|
|Équilibreur de charge accessible par le biais d’Internet|Appelée adresse IP virtuelle (dynamique) ou adresse IP réservée (statique)|Appelée adresse IP publique (dynamique ou statique)|
|Affectée à un service cloud|Associée à la configuration frontale de l’équilibreur de charge|
|
|**Adresse IP privée**|MV|Appelée adresse IP dédiée|Appelée adresse IP privée|
|Affectée à une machine virtuelle IaaS ou à une instance de rôle PaaS|Affectée à la carte réseau de la machine virtuelle|
|Équilibreur de charge interne (ILB)|Affectée à l’équilibreur de charge interne (dynamique ou statique)|Affectée à la configuration frontale de l’équilibreur de charge interne (dynamique ou statique)|

## Étapes suivantes
- [Déployez une machine virtuelle avec une adresse IP privée statique](virtual-networks-static-private-ip-classic-pportal.md) à l’aide du portail classique.

<!---HONumber=AcomDC_0218_2016-->