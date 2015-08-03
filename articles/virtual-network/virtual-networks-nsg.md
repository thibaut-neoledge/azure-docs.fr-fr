<properties 
   pageTitle="Présentation du groupe de sécurité réseau"
   description="En savoir plus sur les groupes de sécurité réseau"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/08/2015"
   ms.author="telmos" />

# Présentation du groupe de sécurité réseau

Vous pouvez utiliser un groupe de sécurité réseau pour contrôler le trafic vers une ou plusieurs instances de machine virtuelle dans votre réseau virtuel. Un groupe de sécurité réseau est un objet de niveau supérieur qui est associé à votre abonnement. Un groupe de sécurité réseau contient les règles de contrôle d'accès qui autorisent ou refusent le trafic vers des instances de machine virtuelle. Les règles d'un groupe de sécurité réseau peuvent être modifiées à tout moment et les modifications sont appliquées à toutes les instances associées. Pour utiliser un groupe de sécurité réseau, vous devez disposer d'un réseau virtuel associé à une région (emplacement).

>[AZURE.WARNING]Les groupes de sécurité réseau ne sont pas compatibles avec les réseaux virtuels associés à un groupe d'affinités. Si vous ne disposez pas d’un réseau virtuel régional et que vous souhaitez contrôler le trafic vers vos points de terminaison, consultez [Présentation d’une liste de contrôle d'accès (ACL) au réseau ?](../virtual-networks-acl).

Vous pouvez associer un groupe de sécurité réseau à une machine virtuelle ou à un sous-réseau dans un réseau virtuel. Associé à une machine virtuelle, le groupe de sécurité réseau s'applique à tout le trafic envoyé et reçu par l'instance de la machine virtuelle. Lorsqu'il est appliqué à un sous-réseau au sein de votre réseau virtuel, il s'applique à tout le trafic envoyé et reçu par TOUTES les instances de la machine virtuelle dans le sous-réseau. Une machine virtuelle ou un sous-réseau peut être associé à un seul groupe de sécurité réseau et chaque groupe de sécurité réseau peut contenir jusqu'à 200 règles. Vous pouvez avoir 100 groupes de sécurité réseau par abonnement.

>[AZURE.NOTE]Les contrôles d’accès réseau basés sur le point de terminaison et les groupes de sécurité réseau ne sont pas pris en charge sur la même instance de machine virtuelle. Si vous souhaitez utiliser un groupe de sécurité réseau et une ACL de point de terminaison déjà en place, supprimez d'abord l’ACL de point de terminaison. Pour en savoir plus sur cette procédure, consultez [Gestion des listes de contrôle d’accès (ACL) pour les points de terminaison à l’aide de PowerShell](https://msdn.microsoft.com/library/azure/dn376543.aspx).

## Fonctionnement du groupe de sécurité réseau

Les groupes de sécurité réseau sont différents des ACL basées sur le point de terminaison. Les ACL de point de terminaison fonctionnent uniquement sur le port public qui est exposé via le point de terminaison d'entrée. Un groupe de sécurité réseau fonctionne sur une ou plusieurs instances de machine virtuelle et contrôle tout le trafic entrant et sortant sur la machine virtuelle.

Un groupe de sécurité du réseau a un *Nom*, est associé à un *Région* et a une étiquette descriptive. Il contient deux types de règles : **Entrant** et **Sortant**. Les règles de trafic entrant sont appliquées aux paquets entrants sur une machine virtuelle et les règles de trafic sortant sont appliquées aux paquets sortants à partir de la machine virtuelle. Les règles sont appliquées sur l'hôte où se trouve la machine virtuelle. Un paquet entrant ou sortant doit correspondre à une règle **Autoriser** pour avoir l’autorisation, autrement il sera supprimé.

Les règles sont traitées dans l'ordre de priorité. Par exemple, une règle avec un numéro de priorité inférieur (par exemple, 100) est traitée avant les règles avec une priorité plus élevée (par exemple, 200). Une fois qu'une correspondance est trouvée, aucune autre règle n'est traitée.

Une règle spécifie les éléments suivants :

- **Nom :** un identificateur unique pour la règle

- **Type :** entrant et sortant

- **Priorité :** <You can specify an integer between 100 and 4096>

- **Adresse IP source :** CIDR de plage d'IP source

- **Plage de ports source :** <integer or range between 0 and 65536>

- **Plage d’adresses IP de destination :** CIDR de la plage d'adresses IP de destination

- **Plage de ports de destination :** <integer or range between 0 and 65536>

- **Protocole :** < TCP, UDP ou « * » est autorisé >

- **Accès :** autoriser/refuser

### Règles par défaut

Un groupe de sécurité réseau contient des règles par défaut. Les règles par défaut ne peuvent pas être supprimées, mais comme la priorité la plus basse leur est attribuée, elles peuvent être remplacées par les règles que vous créez. Les règles par défaut décrivent les paramètres par défaut recommandés par la plateforme. Comme illustré par les règles par défaut ci-dessous, le trafic d’origine et de fin d’un réseau virtuel est autorisé à la fois dans les directions entrante et sortante.

Tandis que la connectivité à Internet est autorisée pour la direction sortante, elle est bloquée par défaut pour la direction entrante. Il existe une règle par défaut pour autoriser l'équilibreur de charge d’Azure pour tester l'intégrité de la machine virtuelle. Vous pouvez remplacer cette règle si la machine virtuelle ou l'ensemble de machines virtuelles sous le groupe de sécurité réseau n'est pas inclus dans le jeu d'équilibrage de charge.

Les règles par défaut sont :

**Trafic entrant**

| Nom | Priorité | IP Source | Port source | IP de destination | Port de destination | Protocole | Access |
|-----------------------------------|----------|--------------------|-------------|-----------------|------------------|----------|--------|
| AUTORISER LE TRAFIC ENTRANT DU RÉSEAU VIRTUEL | 65 000 | VIRTUAL_NETWORK | * | VIRTUAL_NETWORK | * | * | AUTORISER |
| AUTORISER LE TRAFIC ENTRANT DE L'ÉQUILIBRAGE DE CHARGE AZURE | 65 001 | AZURE_LOADBALANCER | * | * | * | * | AUTORISER |
| REFUSER TOUT TRAFIC ENTRANT | 65 500 | * | * | * | * | * | REFUSER |

**Trafic sortant**

| Nom | Priorité | IP Source | Port source | IP de destination | Port de destination | Protocole | Access |
|-------------------------|----------|-----------------|-------------|-----------------|------------------|----------|--------|
| AUTORISER LE TRAFIC SORTANT DU RÉSEAU VIRTUEL | 65 000 | VIRTUAL_NETWORK | * | VIRTUAL_NETWORK | * | * | AUTORISER |
| AUTORISER LE TRAFIC SORTANT D’INTERNET | 65 001 | * | * | INTERNET | * | * | AUTORISER |
| REFUSER TOUT TRAFIC SORTANT | 65 500 | * | * | * | * | * | REFUSER |

### Règles d'infrastructure spéciales

Les règles du groupe de sécurité réseau sont explicites. Aucun trafic n'est autorisé ou refusé au-delà de ce qui est spécifié dans les règles du groupe de sécurité réseau. Toutefois, il existe deux types de trafic qui sont toujours autorisés indépendamment de la spécification du groupe de sécurité réseau. Ces approvisionnements sont effectués pour prendre en charge l'infrastructure.

- **Adresse IP virtuelle du nœud hôte :** des services d’infrastructure de base tels que DHCP, DNS et l’analyse du fonctionnement sont fournis via l'adresse IP d’hôte virtualisé 168.63.129.16. Cette adresse IP publique appartient à Microsoft et la seule adresse IP virtualisée utilisée dans toutes les régions à cet effet. Cette adresse IP mappe vers l'adresse IP physique de l’ordinateur (nœud hôte) du serveur qui héberge la machine virtuelle. Le nœud hôte agit en tant que relais DHCP, le programme de résolution récursif DNS et la sonde source de la sonde d’intégrité de l’équilibreur de charge et de la sonde d’intégrité de la machine. La communication à cette adresse IP ne doit pas être considérée comme une attaque.

- **Gestion des licences (service de gestion de clés) :** les images Windows en cours d'exécution sur les machines virtuelles doivent être acquises sous licence. Pour cela, une demande de licence est envoyée aux serveurs hôtes du service de gestion de clés qui gèrent ces requêtes. Ce sera toujours sur le port 1688 sortant.

### Balises par défaut

Les balises par défaut sont des identificateurs fournis par le système pour adresser une catégorie d'adresses IP. Les balises par défaut peuvent être spécifiées dans les règles définies par le client. Les balises par défaut sont les suivantes :

- **VIRTUAL_NETWORK :** cette balise par défaut indique tous les espaces d'adressage de votre réseau. Il inclut l'espace d'adressage du réseau virtuel (IP CIDR dans Azure), ainsi que tout espace d'adressage local connecté (réseaux locaux). Cela inclut également réseau virtuel pour les espaces d'adressage de réseau virtuel.

- **AZURE_LOADBALANCER :** cette balise par défaut indique l'équilibrage de charge de l'infrastructure d'Azure. Il convertit en une adresse IP de centre de données Azure l’emplacement d’où proviennent les sondes d’intégrité d'Azure. Cela est nécessaire uniquement si la machine virtuelle ou un ensemble de machines virtuelles associées au groupe de sécurité réseau fait partie d'un jeu d'équilibrage de charge.

- **INTERNET :** cette balise par défaut indique l'espace d'adresse IP qui se trouve en dehors du réseau virtuel et est accessible par l'Internet public. Cette plage inclut espace IP public d’Azure.

### Ports et plages de ports

Les règles de groupe de sécurité réseau peuvent être spécifiées sur un port source ou un port de destination unique, ou sur une plage de ports. Cela est particulièrement utile lorsque vous souhaitez ouvrir une grande plage de ports pour une application telle que FTP. La plage peut uniquement être séquentielle et ne peut pas être combinée avec la spécification d’un port individuel.

Pour spécifier une plage de ports, utilisez le signe « - » comme indiqué dans le paramètre *DestinationPortRange* ci-dessous :

	Get-AzureNetworkSecurityGroup -Name ApptierSG `
	| Set-AzureNetworkSecurityRule -Name FTP -Type Inbound -Priority 600 -Action Allow `
		-SourceAddressPrefix INTERNET -SourcePortRange * `
		-DestinationAddressPrefix * -DestinationPortRange 100-500 -Protocol *

### Trafic ICMP

Les règles de groupe de sécurité réseau actuelles autorisent uniquement les protocoles « TCP » ou « UDP ». Il n'existe pas de balise spécifique pour « ICMP ». Toutefois, le trafic ICMP est autorisé dans un réseau virtuel par défaut via les règles de trafic entrant du réseau virtuel qui autorisent le trafic de/vers n'importe quels port et protocole « * » dans le réseau virtuel.

## Association de groupe de sécurité réseau

Association d'un groupe de sécurité réseau à une machine virtuelle : lorsqu'un groupe de sécurité réseau est directement associé à une machine virtuelle, les règles d'accès réseau dans le groupe de sécurité réseau s’appliquent directement à tout le trafic destiné à la machine virtuelle. Chaque fois que le groupe de sécurité réseau est mis à jour pour les modifications de règle, les modifications sont intégrées au trafic de gestion en quelques minutes. Lorsque le groupe de sécurité réseau n’est plus associé à la machine virtuelle, l'état revient à ce qu’il était avant le groupe de sécurité réseau, c’est-à-dire le système par défaut avant l'introduction si le groupe de sécurité réseau a été utilisé.

Association d’un groupe de sécurité réseau à un sous-réseau : lorsqu'un groupe de sécurité réseau est associé à un sous-réseau, les règles d’accès réseau du groupe de sécurité réseau sont appliquées à toutes les machines virtuelles du sous-réseau. Chaque fois que les règles d'accès dans le groupe de sécurité réseau sont mises à jour, les modifications sont appliquées à toutes les machines virtuelles dans le sous-réseau en quelques minutes.

Association d'un groupe de sécurité réseau à un sous-réseau et à une machine virtuelle : vous pouvez associer un groupe de sécurité réseau à une machine virtuelle et un autre groupe de sécurité réseau au sous-réseau sur lequel réside la machine virtuelle. Cette association est prise en charge et dans ce cas la machine virtuelle reçoit deux couches de protection. Pour le trafic entrant, le paquet est acheminé via les règles d'accès spécifiées dans le sous-réseau suivi par les règles dans la machine virtuelle. Dans le cas du trafic sortant, il est acheminé via les règles spécifiées d’abord dans la machine virtuelle avant de passer par les règles spécifiées dans le sous-réseau, comme illustré dans le diagramme ci-dessous.

![ACL de groupe de sécurité réseau](./media/virtual-networks-nsg/figure1.png)

Lorsqu'un groupe de sécurité réseau est associé à une machine virtuelle ou à un sous-réseau, les règles de contrôle d'accès réseau deviennent très explicites. La plate-forme n'insère pas de règle implicite pour autoriser le trafic vers un port particulier. Dans ce cas, si vous créez un point de terminaison dans la machine virtuelle, vous devez également créer une règle pour autoriser le trafic provenant d'Internet. Si vous ne faites pas cela, l'adresse IP virtuelle : <Port> ne sera pas accessible depuis l'extérieur.

Par exemple : vous créez une nouvelle machine virtuelle et vous créez également un nouveau groupe de sécurité réseau. Vous associez le groupe de sécurité réseau à la machine virtuelle. La machine virtuelle peut communiquer avec les autres machines virtuelles dans le réseau virtuel via la règle AUTORISER LE TRAFIC ENTRANT DU RÉSEAU VIRTUEL. La machine virtuelle peut également établir des connexions sortantes à Internet à l'aide de la règle AUTORISER LE TRAFIC SORTANT D’INTERNET. Ensuite, vous créez un point de terminaison sur le port 80 pour recevoir le trafic vers votre site web en cours d'exécution dans la machine virtuelle. Les paquets destinés au port 80 sur l'adresse IP virtuelle (adresse IP virtuelle publique) à partir d'Internet n'atteindront pas la machine virtuelle avant que vous n’ayez ajouté une règle similaire à la suivante (voir ci-dessous) pour le groupe de sécurité réseau.

| Nom | Priorité | IP Source | Port source | IP de destination | Port de destination | Protocole | Access |
|------|----------|-----------|-------------|----------------|------------------|----------|--------|
| WEB | 100 | INTERNET | * | * | 80 | TCP | AUTORISER |

## Remarques relatives à la conception

Lorsque vous concevez vos groupes de sécurité réseau, vous devez comprendre comment les machines virtuelles interagissent avec les services d’infrastructure et le service PaaS hébergé par Microsoft Azure. La plupart des services PaaS Microsoft Azure, comme les bases de données et le stockage SQL sont accessibles uniquement via une adresse Internet publique. Cela est également vrai pour les sondes d’équilibrage de charge.

Un scénario courant dans Azure est la ségrégation des rôles de machines virtuelles et PaaS dans les sous-réseaux en fonction de la nécessité pour ces objets d’accéder à Internet. Dans ce scénario, vous disposez par exemple d’un sous-réseau avec des machines virtuelles ou des instances de rôle qui nécessitent un accès aux services PaaS Azure, comme les bases de données et le stockage SQL, mais ce sous-réseau ne requiert aucune communication entrante ou sortante avec l’Internet public.

Imaginez la règle de groupe de sécurité réseau suivante pour un tel scénario :

| Nom | Priorité | IP Source | Port source | IP de destination | Port de destination | Protocole | Access |
|------|----------|-----------|-------------|----------------|------------------|----------|--------|
|PAS D’INTERNET|100| VIRTUAL_NETWORK|&#42;|INTERNET|&#42;|TCP|REFUSER| 

Étant donné que la règle bloque tout accès de ce réseau virtuel à Internet , les machines virtuelles ne pourront pas accéder aux services PaaS Azure qui nécessitent un point de terminaison Internet public, comme les bases de données SQL.

Au lieu d’utiliser une règle de refus, envisagez d’utiliser une règle autorisant l’accès à Internet à partir du réseau virtuel, mais refusant l’accès au réseau virtuel à partir d’Internet, comme représenté ci-dessous :

| Nom | Priorité | IP Source | Port source | IP de destination | Port de destination | Protocole | Access |
|------|----------|-----------|-------------|----------------|------------------|----------|--------|
|VERS INTERNET|100| VIRTUAL_NETWORK|&#42;|INTERNET|&#42;|TCP|AUTORISER|
|À PARTIR D’INTERNET|110| INTERNET|&#42;|VIRTUAL_NETWORK|&#42;|TCP|REFUSER| 


## Planification : flux de travail du groupe de sécurité réseau

Voici les étapes de flux de travail de base pour l'utilisation de groupes de sécurité réseau.

### Flux de travail : créer et associer un groupe de sécurité réseau

1. Création d’un groupe de sécurité réseau.

1. Ajoutez des règles de sécurité réseau sauf si les règles par défaut sont suffisantes.

1. Associez le groupe de sécurité réseau à une machine virtuelle.

1. Mise à jour de la machine virtuelle.

1. Après la mise à jour, les règles du groupe de sécurité réseau prennent effet immédiatement.

### Flux de travail : mise à jour d’un groupe de sécurité réseau existant

1. Ajout, suppression ou mise à jour d’une règle dans un groupe de sécurité réseau existant.

1. Toutes les machines virtuelles associées au groupe de sécurité réseau obtiennent les mises à jour en quelques minutes. Une mise à jour de la machine virtuelle n'est pas requise lorsque la règle du groupe de sécurité réseau est déjà associée à la machine virtuelle.

### Flux de travail : modification d’une association au groupe de sécurité réseau

1. Association d’un nouveau groupe de sécurité réseau à une machine virtuelle qui est déjà associée à un autre groupe de sécurité réseau.

1. Mise à jour de la machine virtuelle.

1. Les règles du nouveau groupe de sécurité réseau prendront effet en quelques minutes.

## Procédure de création, de configuration et de gestion de vos groupes de sécurité réseau

À ce stade, les groupes de sécurité réseau peuvent être configurés et modifiés en utilisant uniquement les cmdlets PowerShell et les API REST. Vous ne pouvez pas configurer des groupes de sécurité réseau à l'aide du Portail de gestion. Les cmdlets PowerShell ci-dessous vous aideront à créer, configurer et gérer vos groupes de sécurité réseau.

**Création d'un groupe de sécurité réseau**

	New-AzureNetworkSecurityGroup -Name "MyVNetSG" -Location uswest `
		-Label "Security group for my Vnet in West US"

**Ajout ou mise à jour de règles**

	Get-AzureNetworkSecurityGroup -Name "MyVNetSG" `
	| Set-AzureNetworkSecurityRule -Name WEB -Type Inbound -Priority 100 `
		-Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' `
		-DestinationAddressPrefix '*' -DestinationPortRange '*' -Protocol TCP


**Suppression d’une règle à partir d'un groupe de sécurité réseau**

	Get-AzureNetworkSecurityGroup -Name "MyVNetSG" `
	| Remove-AzureNetworkSecurityRule -Name WEB

**Association d’un groupe de sécurité réseau à une machine virtuelle**

	Get-AzureVM -ServiceName "MyWebsite" -Name "Instance1" `
	| Set-AzureNetworkSecurityGroupConfig -NetworkSecurityGroupName "MyVNetSG" `
	| Update-AzureVM

**Affichage des groupes de sécurité de réseau associés à une machine virtuelle**

	Get-AzureVM -ServiceName "MyWebsite" -Name "Instance1" `
	| Get-AzureNetworkSecurityGroupAssociation

**Suppression d’un groupe de sécurité réseau d'une machine virtuelle**

	Get-AzureVM -ServiceName "MyWebsite" -Name "Instance1" `
	| Remove-AzureNetworkSecurityGroupConfig -NetworkSecurityGroupName "MyVNetSG" `
	| Update-AzureVM

**Association d’un groupe de sécurité réseau à un sous-réseau**

	Get-AzureNetworkSecurityGroup -Name "MyVNetSG" `
	| Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'VNetUSWest' `
		-SubnetName 'FrontEndSubnet'

**Affichage des groupes de sécurité de réseau associés à un sous-réseau**

	Get-AzureNetworkSecurityGroupForSubnet -SubnetName 'FrontEndSubnet' `
		-VirtualNetworkName 'VNetUSWest' 

**Suppression d’un groupe de sécurité réseau d’un sous-réseau**

	Get-AzureNetworkSecurityGroup -Name "MyVNetSG" `
	| Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'VNetUSWest' `
		-SubnetName 'FrontEndSubnet'

**Suppression d'un groupe de sécurité réseau**

	Remove-AzureNetworkSecurityGroup -Name "MyVNetSG"

**Obtenir les informations et les règles d’un groupe de sécurité réseau**

	Get-AzureNetworkSecurityGroup -Name "MyVNetSG" -Detailed
 
**Affichage de l’ensemble des applets de commande Azure associées aux groupes de sécurité réseau**

	Get-Command *azurenetworksecuritygroup*

<!---HONumber=July15_HO4-->