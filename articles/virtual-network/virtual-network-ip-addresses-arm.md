<properties 
   pageTitle="Adresses publiques et privées dans le fournisseur de ressources réseau Azure | Microsoft Azure"
   description="En savoir plus sur les adresses IP publiques et privées pour le fournisseur de ressources réseau dans le Gestionnaire de ressources Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/07/2015"
   ms.author="joaoma" />

# Adresses IP dans un réseau virtuel Azure
Cet article traite de l’adressage IP pour les machines virtuelles, les équilibreurs de charge, les passerelles VPN et les passerelles APP.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)].

## Adresses IP publiques
Les adresses IP publiques permettent aux ressources Azure de communiquer avec Internet et d’autres services publics Azure tels que [Cache Redis Azure](https://azure.microsoft.com/services/cache), [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs). Une adresse IP publique est une ressource indépendante et peut être associée à différents types de ressources Azure telles que les [machines virtuelles](virtual-machines-about.md) (VM) et les [équilibreurs de charge](load-balancer-overview.md).

### Méthode d’allocation d’une adresse IP publique
Les adresses IP publiques sont allouées à partir d’un pool d’adresses IP disponibles à un emplacement donné. La liste complète des plages d’adresses IP utilisées par les centres de données Microsoft Azure est publiée à [plages d’adresses IP du centre de données Azure](https://www.microsoft.com/download/details.aspx?id=41653).

Il existe deux méthodes qui impliquent l’allocation d’une adresse IP à une ressource IP publique : *dynamique* ou *statique*. La méthode d’allocation par défaut est *dynamique*. Si elle est *dynamique*, l’adresse IP n’est **pas** affectée à la *ressource d’adresse IP publique* au moment de sa création. Lorsqu’une ressource (de type machine virtuelle ou équilibreur de charge) associée à une *ressource d’adresse IP publique* est créée ou démarrée, une adresse IP issue du pool d’adresses IP lui est affectée. Cette adresse IP est désallouée et libérée vers le pool disponible lorsque la ressource qui lui est associée est supprimée ou arrêtée.

Si vous utilisez la méthode d’allocation *statique*, une adresse IP est affectée à la *ressource d’adresse IP publique* au moment de sa création. Dans ce cas, quel que soit l’état de la ressource associée, c’est la même adresse IP qui reste allouée. Elle est restituée au pool disponible uniquement lorsque la *ressource d’adresse IP publique* est supprimée ou si la méthode d’allocation est remplacée par *dynamique*.

### Résolution DNS
Un libellé de nom de domaine DNS peut être spécifiée pour une ressource IP publique, ce qui crée une entrée DNS correspondante dans les serveurs DNS d’Azure. Le nom de domaine complet correspondant *domainnamelabel*.*location*.cloudapp.azure.com sera globalement converti en adresse IP allouée à la ressource IP publique.

Nous allons examiner les différents types de ressources pouvant être associés à une adresse IP publique ci-dessous.

### Machine virtuelle
Une adresse IP publique est associée à une [machine virtuelle](virtual-machines-about.md) via une carte d’interface réseau (NIC). Chaque machine virtuelle ne peut avoir qu’une seule adresse IP publique, quel que soit le nombre de cartes d’interface réseau associées. Seule une *ressource d’adresse IP publique* allouée de façon *dynamique* peut être associée à une carte d’interface réseau de machine virtuelle. Dans le cas d’une machine virtuelle avec plusieurs cartes d’interface réseau, la *ressource d’adresse IP publique* peut être associée à la carte d’interface réseau principale uniquement.

### Équilibrage de charge Azure
Une adresse IP publique peut être associée à une configuration d’[équilibreur de charge Azure](load-balancer-overview.md) frontal, qui sert d’adresse IP virtuelle d’équilibreur de charge (VIP) accessible sur Internet. Les ressources IP publiques *dynamiques* ou *statiques* peuvent être associées à un équilibreur de charge. Il est possible d’associer plusieurs *ressources d’adresse IP publique* à une configuration d’équilibreur de charge frontal, qui autorise l’utilisation de scénarios comme l’environnement mutualisé avec plusieurs sites web SSL.

### Application Gateway
Une adresse IP publique peut être associée à une configuration de [Passerelle d’application Azure](application-gateway-introduction.md) frontale, et être configurée avec un IP virtuel avec équilibreur de charge accessible par Internet (VIP). Actuellement, seule une ressource IP publique allouée de façon *dynamique* peut être associée à une passerelle d’application. Toutefois, il est possible d’associer plusieurs adresses IP publiques.

### Passerelle VPN
Une adresse IP publique doit être associée à une configuration IP de [passerelle VPN Azure](vpn-gateway-about-vpngateways.md) pendant la procédure de création d’une connexion VPN entre un réseau virtuel Azure et un réseau local, ou un autre réseau virtuel Azure. Actuellement, seule une ressource IP publique allouée de façon *dynamique* peut être associée à une passerelle VPN.

### Aperçu

|Ressource|Allocation statique|Allocation dynamique|Plusieurs adresses IP|
|---|---|---|---|
|Machine virtuelle (VM)/Carte d’Interface réseau|Oui|Non|Non|
|Équilibreur de charge frontal|Oui|Oui|Oui|
|Passerelle d’application frontale|Oui|Non|Oui|
|Passerelle VPN|Oui|Non|Non|

## Adresses IP privées
Les adresses IP privées permettent la communication entre ressources dans un réseau virtuel sans adresses IP accessible par Internet. Cette adresse IP est allouée à partir de la plage d’adresses du sous-réseau dans le réseau virtuel.

### Méthode d’allocation d’adresse IP privée
Il existe deux méthodes d’allocation d’adresses IP : *dynamique* ou *statique*. La méthode d’allocation par défaut est *dynamique*, et l’adresse IP est allouée à l’aide du protocole DHCP. La méthode d’allocation peut être spécifiée de façon explicite comme *statique* en même temps que la spécification d’une adresse IP. Dans ce cas, la ressource est affectée à l’adresse IP spécifiée aussi longtemps qu’elle se trouve dans la plage d’adresses du sous-réseau et qu’elle est disponible (non allouée à une autre ressource).

Il existe différents types de ressources pouvant être affectés à une adresse IP privée. Notez que les deux méthodes d’allocation (*statique* ou *dynamique*) fonctionnent pour tous ces types de ressources.

### Machine virtuelle
Une adresse IP privée est affectée à la carte d’interface réseau (NIC) d’une [machine virtuelle](virtual-machines-about.md) (VM). Chaque machine virtuelle peut avoir autant d’adresses IP privées que de cartes réseau associées.

#### Résolution du nom d’hôte DNS interne
Toutes les machines virtuelles Azure sont configurées avec des [serveurs DNS gérés par Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution), sauf spécification explicite au moment de la création. En cas d’utilisation des serveurs DNS gérés par Azure, un enregistrement DNS est créé automatiquement et résout le nom d’hôte de la machine virtuelle sur l’adresse IP privée de la machine virtuelle. Dans le cas d’une machine virtuelle dotée de plusieurs cartes d’interface réseau, le nom d’hôte est résolu sur une adresse IP privée de la carte d’interface réseau principale.

### Équilibreur de charge interne
Une adresse IP privée peut être affectée à un [équilibreur de charge interne Azure](load-balancer-internal-overview.md) (ILB) frontal, qui sert d’adresse IP virtuelle d’équilibreur de charge pour les ressources au sein de son réseau virtuel. Cela permet l’équilibreur de charge sans exposition de l’adresse IP à Internet.

### Passerelle App
Il est possible d’affecter une adresse IP privée à une [passerelle d’application Azure](application-gateway-introduction.md) frontale, de la configurer avec un point de terminaison Internet non exposé à Internet, également appelé point de terminaison d’équilibreur de charge interne (ILB). Le comportement et les méthodes d’allocation sont similaires à ceux de l’équilibreur de charge interne décrit ci-dessus.

### Aperçu
|Ressource|Allocation statique|Allocation dynamique|Plusieurs adresses IP|
|---|---|---|---|
|Machine virtuelle (VM)/Carte d’Interface réseau|Oui|Oui|Oui|
|Équilibreur de charge frontal|Oui|Oui|Oui|
|Passerelle d’application frontale|Oui|Oui|Oui|

## Étapes suivantes


[Référence d'Azure PowerShell pour la mise en réseau](https://msdn.microsoft.com/library/azure/mt163510.aspx)

[Langue du modèle Azure Resource Manager](../resource-group-authoring-templates.md)

[Mise en réseau Azure : modèles couramment utilisés](https://github.com/Azure/azure-quickstart-templates)

[Fournisseur de ressources de calcul](../virtual-machines-azurerm-versus-azuresm)

[Présentation du gestionnaire des ressources Azure](../resource-group-overview)

[Contrôle d'accès basé sur des rôles dans Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn906885.aspx)

[Utilisation de balises dans le gestionnaire des ressources Azure](https://msdn.microsoft.com/library/azure/dn848368.aspx)

[Déploiements de modèles](https://msdn.microsoft.com/library/azure/dn790549.aspx)

<!---HONumber=AcomDC_1210_2015-->