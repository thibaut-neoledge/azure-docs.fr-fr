<properties
	pageTitle="Utilisation de l'interface de ligne de commande Azure pour Mac, Linux et Windows avec Azure Resource Management"
	description="Découvrez comment utiliser les outils de ligne de commande pour Mac, Linux et Windows afin de gérer les ressources Azure dans le mode arm de l'interface CLI Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/23/2015"
	ms.author="rasquill"/>

# Utilisation de l'interface de ligne de commande Azure pour Mac, Linux et Windows avec Azure Resource Management

Cette rubrique décrit comment utiliser l'interface de ligne de commande Azure (interface CLI Azure) dans le mode **arm** pour créer, gérer et supprimer des services sur la ligne de commande des ordinateurs Mac, Linux et Windows. Vous pouvez effectuer les mêmes tâches à l'aide des différentes bibliothèques des Kits de développement logiciel (SDK) Azure, avec PowerShell, et dans le portail Azure.

La gestion des ressources Azure vous permet de créer un groupe de ressources (machines virtuelles, sites web, bases de données, etc.) en tant qu'unité déployable unique. Vous pouvez ensuite déployer, mettre à jour ou supprimer toutes les ressources de votre application en une opération unique et coordonnée. Vous décrivez vos ressources de groupe dans un modèle JSON pour le déploiement et pouvez ensuite utiliser ce modèle pour les différents environnements (test, intermédiaire et de production).

## Approches impératives et déclaratives

Comme avec le [mode Azure Service Management (**asm**)](../virtual-machines-command-line-tools.md), le mode **arm** de l'interface CLI Azure fournit des commandes qui créent des ressources de manière impérative sur la ligne de commande. Par exemple, si vous tapez `azure group create <groupname> <location>`, vous demandez à Azure de créer un groupe de ressources et si vous tapez `azure group deployment create <resourcegroup> <deploymentname>`, vous indiquez à Azure de créer un déploiement d'un nombre quelconque d'éléments et de les placer dans un groupe. Chaque type de ressource étant associé à des commandes impératives, vous pouvez les chaîner pour créer des déploiements assez complexes.

Toutefois, l'utilisation de _modèles_ de groupe de ressources, qui décrivent un groupe de ressources, est une approche déclarative beaucoup plus puissante, car elle vous permet d'automatiser des déploiements complexes de (presque) n'importe quel nombre de ressources à (presque) toutes les fins. Quand vous utilisez des modèles, la seule commande impérative est d'en déployer un. Pour obtenir une vue d'ensemble des modèles, ressources et groupes de ressources, consultez [Vue d'ensemble des groupes de ressources Azure](resource-groups-overview).

> [AZURE.NOTE]Outre les options propres aux commandes décrites ci-dessous et sur la ligne de commande, vous pouvez utiliser trois options pour afficher une sortie détaillée, comme les options de demande et les codes d'état. Si le paramètre -v fournit une sortie détaillée, le paramètre -vv propose des informations encore plus complètes. L'option --json génère le résultat au format json brut, elle est très utile pour les scénarios de script.
>
> L'utilisation avec le commutateur --json est très courante et joue un rôle important dans l'obtention et la compréhension des résultats des opérations de l'interface CLI Azure, qui retournent des informations sur les ressources, l'état et les journaux, ainsi que dans l'utilisation des modèles. Vous pouvez installer des outils d'analyse JSON tels que **jq** ou **jsawk**, ou utiliser la bibliothèque de votre langage préféré.

##Conditions d'utilisation :

La configuration requise permettant d'utiliser le mode **arm** avec l'interface CLI Azure est la suivante :

- un compte Azure ([obtenir une version d'évaluation gratuite ici](http://azure.microsoft.com/pricing/free-trial/))
- [installation de l'interface CLI Azure](../xplat-cli-install.md)
- [configuration de l'interface CLI Azure](../xplat-cli-connect.md) pour utiliser une identité Azure Active Directory ou un principal du service

Dès que vous disposez d'un compte et que vous avez installé l'interface CLI Azure, vous devez

- basculer en mode **arm** en tapant `azure config mode arm`.
- Connectez-vous à votre compte Azure en tapant `azure login` et en utilisant votre identité professionnelle ou étudiante quand vous y êtes invité

À présent, tapez `azure` pour afficher la liste des commandes de niveau supérieur décrites dans les sections ci-dessous.

## azure account : gérer vos informations de compte et les paramètres de publication
L'outil se sert des informations sur votre abonnement Azure pour se connecter à votre compte. Ces informations peuvent être obtenues depuis le portail Azure dans un fichier de paramètres de publication, comme décrit dans ce document. Vous pouvez importer le fichier de paramètres de publication en tant que paramètre de configuration local persistant dont l'outil se servira pour les opérations ultérieures. Vous importez vos paramètres de publication une fois pour toutes.

**Répertorier les abonnements importés**

	account list [options]

**Afficher les détails d'un abonnement**

	account show [options] [subscriptionNameOrId]

**Définir l'abonnement actif**

	account set [options] <subscriptionNameOrId>

**Supprimer un abonnement ou environnement, ou supprimer toutes les informations de compte et d'environnement stockées**

	account clear [options]

**Commandes pour gérer votre environnement de compte**

	account env list [options]
	account env show [options] [environment]
	account env add [options] [environment]
	account env set [options] [environment]
	account env delete [options] [environment]

## azure ad : commandes pour afficher les objets Active Directory

**Commandes pour afficher les applications Active Directory**

	ad app create [options]
	ad app delete [options] <object-id>

**Commandes pour afficher les groupes Active Directory**

	ad group list [options]
	ad group show [options]

**Commandes pour fournir des informations sur un sous-groupe ou membre Active Directory**

	ad group member list [options] [objectId]

**Commandes pour afficher les principaux du service Active Directory**

	ad sp list [options]
	ad sp show [options]
	ad sp create [options] <application-id>
	ad sp delete [options] <object-id>

**Commandes pour afficher les utilisateurs Active Directory**

	ad user list [options]
	ad user show [options]

## azure availset : commandes pour gérer vos groupes à haute disponibilité

**Créer un groupe à haute disponibilité au sein d'un groupe de ressources**

	availset create [options] <resource-group> <name> <location> [tags]

**Répertorier les groupes à haute disponibilité au sein d'un groupe de ressources**

	availset list [options] <resource-group>

**Obtenir un groupe à haute disponibilité au sein d'un groupe de ressources**

	availset show [options] <resource-group> <name>

**Supprimer un groupe à haute disponibilité au sein d'un groupe de ressources**

	availset delete [options] <resource-group> <name>

## azure config : commandes pour gérer vos paramètres locaux

**Répertorier les paramètres de configuration de l'interface CLI Azure**

	config list [options]

**Supprimer un paramètre de configuration**

	config delete [options] <name>

**Mettre à jour un paramètre de configuration**

	config set <name> <value>

**Définir le mode de fonctionnement de l'interface CLI Azure avec la valeur `arm` ou `asm`**

	config mode [options] <modename>


## azure feature : commandes pour gérer les fonctionnalités de compte

**Répertorier toutes les fonctionnalités disponibles pour votre abonnement**

	feature list [options]

**Présenter une fonctionnalité**

	feature show [options] <providerName> <featureName>

**Enregistrer une fonctionnalité préliminaire d'un fournisseur de ressources**

	feature register [options] <providerName> <featureName>

## azure group : commandes pour gérer vos groupes de ressources

**Créer un groupe de ressources**

	group create [options] <name> <location>

**Définir des balises dans un groupe de ressources**

	group set [options] <name> <tags>

**Supprimer un groupe de ressources**

	group delete [options] <name>

**Répertorier les groupes de ressources pour votre abonnement**

	group list [options]

**Afficher un groupe de ressources pour votre abonnement**

	group show [options] <name>

**Commandes pour gérer les journaux de groupe de ressources**

	group log show [options] [name]

**Commandes pour gérer votre déploiement dans un groupe de ressources**

	group deployment create [options] [resource-group] [name]
	group deployment list [options] <resource-group> [state]
	group deployment show [options] <resource-group> [deployment-name]
	group deployment stop [options] <resource-group> [deployment-name]

**Commandes pour gérer le modèle de votre groupe de ressources local ou de la galerie**

	group template list [options]
	group template show [options] <name>
	group template download [options] [name] [file]
	group template validate [options] <resource-group>

## azure insights : commandes liées à la surveillance d'informations détaillées (événements, règles d'alerte, paramètres de mise à l'échelle automatique, mesures)

**Récupérer les journaux des opérations pour un abonnement, un ID de corrélation, un groupe de ressources, une ressource ou un fournisseur de ressources**

	insights logs list [options]

## azure location : commandes pour obtenir les emplacements disponibles pour tous les types de ressource

**Répertorier les emplacements disponibles**

	location list [options]

## azure network : commandes pour gérer les ressources réseau

**Commandes pour gérer les réseaux virtuels**

	network vnet create [options] <resource-group> <name> <location>
Permet de créer un réseau virtuel. Dans l'exemple suivant, nous créons un réseau virtuel nommé newvnet pour le groupe de ressources myresourcegroup dans la région Ouest des États-Unis.


	azure network vnet create myresourcegroup newvnet "west us"
	info:    Executing command network vnet create
	+ Looking up virtual network "newvnet"
	+ Creating virtual network "newvnet"
	 Loading virtual network state
	data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet
	data:    Name:                 newvnet
	data:    Type:                 Microsoft.Network/virtualNetworks
	data:    Location:             westus
	data:    Tags:
	data:    Provisioning state:   Succeeded
	data:    Address prefixes:
	data:     10.0.0.0/8
	data:    DNS servers:
	data:    Subnets:
	data:
	info:    network vnet create command OK


Options de paramètre :

 	-h, --help                                 output usage information
 	-v, --verbose                              use verbose output
	--json                                     use json output
 	-g, --resource-group <resource-group>      the name of the resource group
 	-n, --name <name>                          the name of the virtual network
 	-l, --location <location>                  the location
 	-a, --address-prefixes <address-prefixes>  the comma separated list of address prefixes for this virtual network
      For example -a 10.0.0.0/24,10.0.1.0/24.
      Default value is 10.0.0.0/8

	-d, --dns-servers <dns-servers>            the comma separated list of DNS servers IP addresses
 	-t, --tags <tags>                          the tags set on this virtual network.
      Can be multiple. In the format of "name=value".
      Name is required and value is optional.
      For example, -t tag1=value1;tag2
	 -s, --subscription <subscription>          the subscription identifier
<BR>

	network vnet set [options] <resource-group> <name>

Met à jour une configuration de réseau virtuel dans un groupe de ressources.

	azure network vnet set myresourcegroup newvnet

	info:    Executing command network vnet set
	+ Looking up virtual network "newvnet"
	+ Updating virtual network "newvnet"
	+ Loading virtual network state
	data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet
	data:    Name:                 newvnet
	data:    Type:                 Microsoft.Network/virtualNetworks
	data:    Location:             westus
	data:    Tags:
	data:    Provisioning state:   Succeeded
	data:    Address prefixes:
	data:     10.0.0.0/8
	data:    DNS servers:
	data:    Subnets:
	data:
	info:    network vnet set command OK

Options de paramètre :

	   -h, --help                                 output usage information
	   -v, --verbose                              use verbose output
	   --json                                     use json output
	   -g, --resource-group <resource-group>      the name of the resource group
	   -n, --name <name>                          the name of the virtual network
	   -a, --address-prefixes <address-prefixes>  the comma separated list of address prefixes for this virtual network.
        For example -a 10.0.0.0/24,10.0.1.0/24.
        This list will be appended to the current list of address prefixes.
        The address prefixes in this list should not overlap between them.
        The address prefixes in this list should not overlap with existing address prefixes in the vnet.

	   -d, --dns-servers [dns-servers]            the comma separated list of DNS servers IP addresses.
        This list will be appended to the current list of DNS server IP addresses.

	   -t, --tags <tags>                          the tags set on this virtual network.
        Can be multiple. In the format of "name=value".
        Name is required and value is optional. For example, -t tag1=value1;tag2.
        This list will be appended to the current list of tags

	   --no-tags                                  remove all existing tags
	   -s, --subscription <subscription>          the subscription identifier
<BR>

	network vnet list [options] <resource-group>

La commande permet de répertorier tous les réseaux virtuels dans un groupe de ressources.


	C:>azure network vnet list myresourcegroup

	info:    Executing command network vnet list
	+ Listing virtual networks
		data:    ID
       Name      Location  Address prefixes  DNS servers
	data:    -------------------------------------------------------------------
	------  --------  --------  ----------------  -----------
	data:    /subscriptions/###############################/resourceGroups/
	wvnet   newvnet   westus    10.0.0.0/8
	info:    network vnet list command OK

Options de paramètre :


      -h, --help                             output usage information
      -v, --verbose                          use verbose output
      --json                                 use json output
      -g, --resource-group <resource-group>  the name of the resource group
      -s, --subscription <subscription>      the subscription identifier

<BR>

	network vnet show [options] <resource-group> <name>
La commande affiche les propriétés du réseau virtuel dans un groupe de ressources.

	azure network vnet show -g myresourcegroup -n newvnet

	info:    Executing command network vnet show
	+ Looking up virtual network "newvnet"
	data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet
	data:    Name:                 newvnet
	data:    Type:                 Microsoft.Network/virtualNetworks
	data:    Location:             westus
	data:    Tags:
	data:    Provisioning state:   Succeeded
	data:    Address prefixes:
	data:     10.0.0.0/8
	data:    DNS servers:
	data:    Subnets:
	data:
	info:    network vnet show command OK
<BR>

	network vnet delete [options] <resource-group> <name>
La commande supprime un réseau virtuel.

	azure network vnet delete myresourcegroup newvnetX

	info:    Executing command network vnet delete
	+ Looking up virtual network "newvnetX"
	Delete virtual network newvnetX? [y/n] y
	+ Deleting virtual network "newvnetX"
	info:    network vnet delete command OK

Options de paramètre :

     -h, --help                             output usage information
     -v, --verbose                          use verbose output
     --json                                 use json output
     -g, --resource-group <resource-group>  the name of the resource group
     -n, --name <name>                      the name of the virtual network
     -q, --quiet                            quiet mode, do not ask for delete confirmation
     -s, --subscription <subscription>      the subscription identifier


**Commandes pour gérer les sous-réseaux de réseau virtuel**

	network vnet subnet create [options] <resource-group> <vnet-name> <name>
La commande permet d'ajouter un autre sous-réseau à un réseau virtuel existant.

	azure network vnet subnet create -g myresourcegroup --vnet-name newvnet -n subnet --address-prefix 10.0.1.0/24

	info:    Executing command network vnet subnet create
	+ Looking up the subnet "subnet"
	+ Creating subnet "subnet"
	+ Looking up the subnet "subnet"
	data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet/subnets/subnet
	data:    Name:                      subnet
	data:    Type:                      Microsoft.Network/virtualNetworks/subnets
	data:    Provisioning state:        Succeeded
	data:    Address prefix:            10.0.1.0/24
	info:    network vnet subnet create command OK

Options de paramètre :

     -h, --help                                                       output usage information
     -v, --verbose                                                    use verbose output
		 --json                                                           use json output
	 -g, --resource-group <resource-group>                            the name of the resource group
	 -e, --vnet-name <vnet-name>                                      the name of the virtual network
     -n, --name <name>                                                the name of the subnet
     -a, --address-prefix <address-prefix>                            the address prefix
     -w, --network-security-group-id <network-security-group-id>      the network security group identifier.
           e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/networkSecurityGroups/<nsg-name>
     -o, --network-security-group-name <network-security-group-name>  the network security group name
     -s, --subscription <subscription>                                the subscription identifier

<BR>

	network vnet subnet set [options] <resource-group> <vnet-name> <name>

Définit un sous-réseau de réseau virtuel spécifique au sein d'un groupe de ressources.


	C:>azure network vnet subnet set -g myresourcegroup --vnet-name newvnet -n subnet1

	info:    Executing command network vnet subnet set
	+ Looking up the subnet "subnet1"
	+ Setting subnet "subnet1"
	+ Looking up the subnet "subnet1"
	data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet/subnets/subnet1
	data:    Name:                      subnet1
	data:    Type:                      Microsoft.Network/virtualNetworks/subnets
	data:    Provisioning state:        Succeeded
	data:    Address prefix:            10.0.1.0/24
	info:    network vnet subnet set command OK
<BR>

	network vnet subnet list [options] <resource-group> <vnet-name>

Répertorie tous les sous-réseaux d'un réseau virtuel spécifique au sein d'un groupe de ressources.

	azure network vnet subnet set -g myresourcegroup --vnet-name newvnet -n subnet1

	info:    Executing command network vnet subnet set
	+ Looking up the subnet "subnet1"
	+ Setting subnet "subnet1"
	+ Looking up the subnet "subnet1"
	data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet/subnets/subnet1
	data:    Name:                      subnet1
	data:    Type:                      Microsoft.Network/virtualNetworks/subnets
	data:    Provisioning state:        Succeeded
	data:    Address prefix:            10.0.1.0/24
	info:    network vnet subnet set command OK
<BR>

	network vnet subnet show [options] <resource-group> <vnet-name> <name>
Affiche les propriétés des sous-réseaux de réseau virtuel

	azure network vnet subnet show -g myresourcegroup --vnet-name newvnet -n subnet1

	info:    Executing command network vnet subnet show
	+ Looking up the subnet "subnet1"
	data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft
	.Network/virtualNetworks/newvnet/subnets/subnet1
	data:    Name:                      subnet1
	data:    Type:                      Microsoft.Network/virtualNetworks/subnets
	data:    Provisioning state:        Succeeded
	data:    Address prefix:            10.0.1.0/24
	info:    network vnet subnet show command OK

Options de paramètre :

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-e, --vnet-name <vnet-name>            the name of the virtual network
	-n, --name <name>                      the name of the subnet
	-s, --subscription <subscription>      the subscription identifier
<BR>

	network vnet subnet delete [options] <resource-group> <vnet-name> <subnet-name>
Supprime un sous-réseau d'un réseau virtuel existant.

	azure network vnet subnet delete -g myresourcegroup --vnet-name newvnet -n subnet1

	info:    Executing command network vnet subnet delete
	+ Looking up the subnet "subnet1"
	Delete subnet "subnet1"? [y/n] y
	+ Deleting subnet "subnet1"
	info:    network vnet subnet delete command OK

Options de paramètre :

 	-h, --help                             output usage information
 	-v, --verbose                          use verbose output
 	--json                                 use json output
 	-g, --resource-group <resource-group>  the name of the resource group
 	-e, --vnet-name <vnet-name>            the name of the virtual network
 	-n, --name <name>                      the subnet name
 	-s, --subscription <subscription>      the subscription identifier
 	-q, --quiet                            quiet mode, do not ask for delete confirmation

**Commandes pour gérer les équilibrages de charge**

	network lb create [options] <resource-group> <name> <location>
Crée un jeu d'équilibrage de charge.

	azure network lb create -g myresourcegroup -n mylb -l westus

	info:    Executing command network lb create
	+ Looking up the load balancer "mylb"
	+ Creating load balancer "mylb"
	+ Looking up the load balancer "mylb"
	data:    Id:                           /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb
	data:    Name:                         mylb
	data:    Type:                         Microsoft.Network/loadBalancers
	data:    Location:                     westus
	data:    Provisioning state:           Succeeded
	info:    network lb create command OK

Options de paramètre :

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-n, --name <name>                      the name of the load balancer
	-l, --location <location>              the location
	-t, --tags <tags>                      the list of tags.
     Can be multiple. In the format of "name=value".
     Name is required and value is optional. For example, -t tag1=value1;tag2
	-s, --subscription <subscription>      the subscription identifier
<BR>

	network lb list [options] <resource-group>
Répertorie les ressources d'équilibrage de charge au sein d'un groupe de ressources.

	azure network lb list myresourcegroup

	info:    Executing command network lb list
	+ Getting the load balancers
	data:    Name  Location
	data:    ----  --------
	data:    mylb  westus
	info:    network lb list command OK

Options de paramètre :

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-s, --subscription <subscription>      the subscription identifier
<BR>

	network lb show [options] <resource-group> <name>

Affiche les informations d'un équilibrage de charge spécifique au sein d'un groupe de ressources

	azure network lb show myresourcegroup mylb -v

	info:    Executing command network lb show
	verbose: Looking up the load balancer "mylb"
	data:    Id:                           /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb
	data:    Name:                         mylb
	data:    Type:                         Microsoft.Network/loadBalancers
	data:    Location:                     westus
	data:    Provisioning state:           Succeeded
	info:    network lb show command OK

Options de paramètre :

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-n, --name <name>                      the name of the load balancer
	-s, --subscription <subscription>      the subscription identifier

<BR>

	network lb delete [options] <resource-group> <name>

Supprime les ressources d'équilibrage de charge.

	azure network lb delete  myresourcegroup mylb

	info:    Executing command network lb delete
	+ Looking up the load balancer "mylb"
	Delete load balancer "mylb"? [y/n] y
	+ Deleting load balancer "mylb"
	info:    network lb delete command OK

Options de paramètre :

 	-h, --help                             output usage information
 	-v, --verbose                          use verbose output
 	--json                                 use json output
 	-g, --resource-group <resource-group>  the name of the resource group
 	-n, --name <name>                      the name of the load balancer
 	-q, --quiet                            quiet mode, do not ask for delete confirmation
 	-s, --subscription <subscription>      the subscription identifier

**Commandes pour gérer les sondes d'équilibrage de charge**

	network lb probe create [options] <resource-group> <lb-name> <name>

Crée la configuration de sonde pour l'état d'intégrité dans l'équilibrage de charge. N'oubliez pas que pour exécuter cette commande, votre équilibrage de charge requiert une ressource IP frontale (consultez la commande « azure network frontend-ip » pour attribuer une adresse IP à l'équilibrage de charge).

	azure network lb probe create -g myresourcegroup --lb-name mylb -n mylbprobe --protocol tcp --port 80 -i 300

	info:    Executing command network lb probe create
	+ Looking up the load balancer "mylb"
	+ Updating load balancer "mylb"
	info:    network lb probe create command OK

Options de paramètre :

 	-h, --help                             output usage information
 	-v, --verbose                          use verbose output
 	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-n, --name <name>                      the name of the probe
	-p, --protocol <protocol>              the probe protocol
	-o, --port <port>                      the probe port
	-f, --path <path>                      the probe path
	-i, --interval <interval>              the probe interval in seconds
	-c, --count <count>                    the number of probes
	-s, --subscription <subscription>      the subscription identifier

<BR>

	network lb probe set [options] <resource-group> <lb-name> <name>

Met à jour la sonde d'un équilibrage de charge existant avec de nouvelles valeurs.

	azure network lb probe set -g myresourcegroup -l mylb -n mylbprobe -p mylbprobe1 -p TCP -o 443 -i 300

	info:    Executing command network lb probe set
		+ Looking up the load balancer "mylb"
	+ Updating load balancer "mylb"
	info:    network lb probe set command OK

Options de paramètre

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-n, --name <name>                      the name of the probe
	-e, --new-probe-name <new-probe-name>  the new name of the probe
	-p, --protocol <protocol>              the new value for probe protocol
	-o, --port <port>                      the new value for probe port
	-f, --path <path>                      the new value for probe path
	-i, --interval <interval>              the new value for probe interval in seconds
	-c, --count <count>                    the new value for number of probes
	-s, --subscription <subscription>      the subscription identifier
<BR>

	network lb probe list [options] <resource-group> <lb-name>

Répertorie les propriétés de sonde pour un jeu d'équilibrage de charge.

	C:>azure network lb probe list -g myresourcegroup -l mylb

	info:    Executing command network lb probe list
	+ Looking up the load balancer "mylb"
	data:    Name       Protocol  Port  Path  Interval  Count
	data:    ---------  --------  ----  ----  --------  -----
	data:    mylbprobe  Tcp       443         300       2
	info:    network lb probe list command OK

Options de paramètre :

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-s, --subscription <subscription>      the subscription identifier


	network lb probe delete [options] <resource-group> <lb-name> <name>
Supprime la sonde créée pour l'équilibrage de charge.

	azure network lb probe delete -g myresourcegroup -l mylb -n mylbprobe

	info:    Executing command network lb probe delete
	+ Looking up the load balancer "mylb"
	Delete a probe "mylbprobe?" [y/n] y
	+ Updating load balancer "mylb"
	info:    network lb probe delete command OK

**Commandes pour gérer les configurations IP frontales d'un équilibrage de charge**

	network lb frontend-ip create [options] <resource-group> <lb-name> <name>
Crée une configuration IP frontale dans un jeu d'équilibrage de charge existant.

	azure network lb frontend-ip create -g myresourcegroup --lb-name mylb -n myfrontendip -o Dynamic -e subnet -m newvnet

	info:    Executing command network lb frontend-ip create
	+ Looking up the load balancer "mylb"
	+ Looking up the subnet "subnet"
	+ Creating frontend IP configuration "myfrontendip"
	+ Looking up the load balancer "mylb"
	data:    Id:                           /subscriptions/###############################/resourceGroups/Myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb
	/frontendIPConfigurations/myfrontendip
	data:    Name:                         myfrontendip
	data:    Type:                         Microsoft.Network/loadBalancers/frontendIPConfigurations
	data:    Provisioning state:           Succeeded
	data:    Private IP allocation method: Dynamic
	data:    Private IP address:           10.0.1.4
	data:    Subnet:                       id=/subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet/subnets/subnet
	data:    Public IP address:
	data:    Inbound NAT rules
	data:    Outbound NAT rules
	data:    Load balancing rules
	data:
	info:    network lb frontend-ip create command OK

<BR>

	network lb frontend-ip set [options] <resource-group> <lb-name> <name>

Permet de mettre à jour une configuration existante d'adresse IP frontale. La commande suivante ajoute une adresse IP publique appelée mypubip5 à une adresse IP frontale d'équilibrage de charge nommée myfrontendip.

	azure network lb frontend-ip set -g myresourcegroup --lb-name mylb -n myfrontendip -i mypubip5

	info:    Executing command network lb frontend-ip set
	+ Looking up the load balancer "mylb"
	+ Looking up the public ip "mypubip5"
	+ Updating load balancer "mylb"
	+ Looking up the load balancer "mylb"
	data:    Id:                           /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
	data:    Name:                         myfrontendip
	data:    Type:                         Microsoft.Network/loadBalancers/frontendIPConfigurations
	data:    Provisioning state:           Succeeded
	data:    Private IP allocation method: Dynamic
	data:    Private IP address:
	data:    Subnet:
	data:    Public IP address:            id=/subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mypubip5
	data:    Inbound NAT rules
	data:    Outbound NAT rules
	data:    Load balancing rules
	data:
	info:    network lb frontend-ip set command OK

Options de paramètre :

	-h, --help                                                         output usage information
	-v, --verbose                                                      use verbose output
	--json                                                             use json output
	-g, --resource-group <resource-group>                              the name of the resource group
	-l, --lb-name <lb-name>                                            the name of the load balancer
	-n, --name <name>                                                  the name of the frontend ip configuration
	-a, --private-ip-address <private-ip-address>                      the private ip address
	-o, --private-ip-allocation-method <private-ip-allocation-method>  the private ip allocation method [Static, Dynamic]
	-u, --public-ip-id <public-ip-id>                                  the public ip identifier.
	e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/publicIPAddresses/<public-ip-name>
	-i, --public-ip-name <public-ip-name>                              the public ip name.
	This public ip must exist in the same resource group as the lb.
	Please use public-ip-id if that is not the case.
	-b, --subnet-id <subnet-id>                                        the subnet id.
	e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/VirtualNetworks/<vnet-name>/subnets/<subnet-name>
	-e, --subnet-name <subnet-name>                                    the subnet name
	-m, --vnet-name <vnet-name>                                        the virtual network name.
	This virtual network must exist in the same resource group as the lb.
	Please use subnet-id if that is not the case.
	-s, --subscription <subscription>                                  the subscription identifier

<BR>

	network lb frontend-ip list [options] <resource-group> <lb-name>

Répertorie toutes les ressources IP frontales configurées pour l'équilibrage de charge.

	azure network lb frontend-ip list -g myresourcegroup -l mylb

	info:    Executing command network lb frontend-ip list
	+ Looking up the load balancer "mylb"
	data:    Name         Provisioning state  Private IP allocation method  Subnet
	data:    -----------  ------------------  ----------------------------  ------
	data:    myprivateip  Succeeded           Dynamic
	info:    network lb frontend-ip list command OK

Options de paramètre :

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-s, --subscription <subscription>      the subscription identifier
<BR>

	network lb frontend-ip delete [options] <resource-group> <lb-name> <name>
Supprime l'objet IP frontal associé à l'équilibrage de charge

	network lb frontend-ip delete -g myresourcegroup -l mylb -n myfrontendip
	info:    Executing command network lb frontend-ip delete
	+ Looking up the load balancer "mylb"
	Delete frontend ip configuration "myfrontendip"? [y/n] y
	+ Updating load balancer "mylb"

Options de paramètre :

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-n, --name <name>                      the name of the frontend ip configuration
	-q, --quiet                            quiet mode, do not ask for delete confirmation
	-s, --subscription <subscription>      the subscription identifier

**Commandes pour gérer les pools d'adresses principales d'un équilibrage de charge**

	network lb address-pool create [options] <resource-group> <lb-name> <name>

Crée un pool d'adresses principales pour un équilibrage de charge

	azure network lb address-pool create -g myresourcegroup --lb-name mylb -n myaddresspool

	info:    Executing command network lb address-pool create
	+ Looking up the load balancer "mylb"
	+ Updating load balancer "mylb"
	+ Looking up the load balancer "mylb"
	data:    Id:                        /subscriptions/###############################/resourceGroups/myresourgroup/providers/Microso.Network/loadBalancers/mylb/backendAddressPools/myaddresspool
	data:    Name:                      myaddresspool
	data:    Type:                      Microsoft.Network/loadBalancers/backendAddressPools
	data:    Provisioning state:        Succeeded
	data:    Backend IP configurations:
	data:    Load balancing rules:
	data:
	info:    network lb address-pool create command OK

Options de paramètre :

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-n, --name <name>                      the name of the backend address pool
	-s, --subscription <subscription>      the subscription identifier

<BR>

	network lb address-pool add [options] <resource-group> <lb-name> <name>

Une plage de pool d'adresses principales permet à un équilibrage de charge de déterminer les ressources pour acheminer le trafic réseau entrant à partir de son point de terminaison à l'aide d'Azure Resource Manager. Une fois que vous créez et nommez la plage du pool d'adresses principales (consultez la commande « azure network lb address-pool create »), vous devez ajouter les points de terminaison qui sont maintenant définis par une ressource appelée « interfaces réseau ».

Pour configurer la plage d'adresses principales, vous avez besoin d'au moins une « interface réseau » (consultez la ligne de commande « azure network lb nic » pour plus de détails).

Dans l'exemple suivant, l'interface réseau « nic1 » préalablement créée a été utilisée pour créer la plage du pool d'adresses principales.

	azure network lb address-pool add -g myresourcegroup -l mylb -n mybackendpool -a nic1

	info:    Executing command network lb address-pool add
	+ Looking up the load balancer "mylb"
	+ Getting network interfaces
	+ Updating network interface "nic1"
	+ Looking up the load balancer "mylb"
	data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/mybackendpool
	data:    Name:                      mybackendpool
	data:    Type:                      Microsoft.Network/loadBalancers/backendAddressPools
	data:    Provisioning state:        Succeeded
	data:    Backend IP configurations:
	data:     id=/subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/networkInterfaces/nic1/ipConfigurations/NIC-config
	data:    Load balancing rules:
	data:
	info:    network lb address-pool add command OK

Options de paramètre :

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-n, --name <name>                      the name of the backend address pool
	-i, --vm-id <vm-id>                    the virtual machine identifier.
	e.g. "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines/<vm-name>,/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines/<vm-name>"
	-m, --vm-name <vm-name>                the name of the virtual machine
	-d, --nic-id <nic-id>                  the network interface identifier.
	e.g. ""/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/networkInterfaces/<nic-name>"
	-a, --nic-name <nic-name>              the name of the network interface
	-s, --subscription <subscription>      the subscription identifier

<BR>

	network lb address-pool remove [options] <resource-group> <lb-name> <name>

Supprime une interface réseau de la plage du pool d'adresses IP principales.

	azure network lb address-pool remove -g myresourcegroup -l mylb -n mybackendpool -a nic1

	info:    Executing command network lb address-pool remove
	+ Looking up the load balancer "mylb"
	+ Getting network interfaces
	+ Updating network interface "nic1"
	+ Looking up the load balancer "mylb"
	data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/mybackendpool
	data:    Name:                      mybackendpool
	data:    Type:                      Microsoft.Network/loadBalancers/backendAddressPools
	data:    Provisioning state:        Succeeded
	data:    Backend IP configurations:
	data:    Load balancing rules:
	data:
	info:    network lb address-pool remove command OK

Options de paramètre :

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-n, --name <name>                      the name of the backend address pool
	-i, --vm-id <vm-id>                    the virtual machine identifier.
	e.g. "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines/<vm-name>,/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines/<vm-name>"
	-m, --vm-name <vm-name>                the name of the virtual machine
	-d, --nic-id <nic-id>                  the network interface identifier.
	e.g. ""/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/networkInterfaces/<nic-name>"
	-a, --nic-name <nic-name>              the name of the network interface
	-s, --subscription <subscription>      the subscription identifier
<BR>

	network lb address-pool list [options] <resource-group> <lb-name>

Indique la plage du pool d'adresses IP principales pour un groupe de ressources spécifique

	azure network lb address-pool list -g myresourcegroup -l mylb

	info:    Executing command network lb address-pool list
	+ Looking up the load balancer "mylb"
	data:    Name           Provisioning state
	data:    -------------  ------------------
	data:    mybackendpool  Succeeded
	info:    network lb address-pool list command OK

Options de paramètre :

 	-h, --help                             output usage information
 	-v, --verbose                          use verbose output
 	--json                                 use json output
 	-g, --resource-group <resource-group>  the name of the resource group
 	-l, --lb-name <lb-name>                the name of the load balancer
 	-s, --subscription <subscription>      the subscription identifier

<BR> network lb address-pool delete [options] <resource-group> <lb-name> <name>

Supprime la ressource de la plage du pool d'adresses IP principales dans l'équilibrage de charge.

	azure network lb address-pool delete -g myresourcegroup -l mylb -n mybackendpool

	info:    Executing command network lb address-pool delete
	+ Looking up the load balancer "mylb"
	Delete backend address pool "mybackendpool"? [y/n] y
	+ Updating load balancer "mylb"
	info:    network lb address-pool delete command OK

Options de paramètre :

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-n, --name <name>                      the name of the backend address pool
	-q, --quiet                            quiet mode, do not ask for delete confirmation
	-s, --subscription <subscription>      the subscription identifier

**Commandes pour gérer les règles d'équilibrage de charge**

	network lb rule create [options] <resource-group> <lb-name> <name>
Crée des règles d'équilibrage de charge.

Vous pouvez créer une règle d'équilibrage de charge qui configure le point de terminaison frontal pour l'équilibrage de charge et la plage du pool d'adresses IP principales qui reçoit le trafic réseau entrant. Les paramètres incluent également les ports du point de terminaison de l'adresse IP frontale et les ports de la plage du pool d'adresses IP principales.

L'exemple suivant montre comment créer une règle d'équilibrage de charge, le point de terminaison frontal écoutant le port TCP 80 et le trafic réseau de l'équilibrage de charge étant envoyé sur le port 8080 pour la plage du pool d'adresses IP principales.

	azure network lb rule create -g myresourcegroup -l mylb -n mylbrule -p tcp -f 80 -b 8080 -i 10


	info:    Executing command network lb rule create
	+ Looking up the load balancer "mylb"
	+ Updating load balancer "mylb"
	+ Loading rule state
	data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/loadBalancingRules/mylbrule
	data:    Name:                      mylbrule
	data:    Type:                      Microsoft.Network/loadBalancers/loadBalancingRules
	data:    Provisioning state:        Succeeded
	data:    Frontend IP configuration: /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
	data:    Backend address pool:      id=/subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/mybackendpool
	data:    Protocol:                  Tcp
	data:    Frontend port:             80
	data:    Backend port:              8080
	data:    Enable floating IP:        false
	data:    Idle timeout in minutes:   10
	data:    Probes
	data:
	info:    network lb rule create command OK

<BR>

	network lb rule set [options] <resource-group> <lb-name> <name>

Met à jour une règle d'équilibrage de charge existante définie dans un groupe de ressources spécifique. Dans l'exemple suivant, nous avons remplacé le nom de la règle mylbrule par mynewlbrule.

	azure network lb rule set -g myresourcegroup -l mylb -n mylbrule -r mynewlbrule -p tcp -f 80 -b 8080 -i 10 -t myfrontendip -o mybackendpool

	info:    Executing command network lb rule set
	+ Looking up the load balancer "mylb"
	+ Updating load balancer "mylb"
	+ Loading rule state
	data:    Id:                        /subscriptions/###############################/resourceGroups/yresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/loadBalancingRules/mynewlbrule
	data:    Name:                      mynewlbrule
	data:    Type:                      Microsoft.Network/loadBalancers/loadBalancingRules
	data:    Provisioning state:        Succeeded
	data:    Frontend IP configuration: /subscriptions/###############################/resourceGroups/yresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
	data:    Backend address pool:      id=/subscriptions/###############################/resourceGroups/yresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/mybackendpool
	data:    Protocol:                  Tcp
	data:    Frontend port:             80
	data:    Backend port:              8080
	data:    Enable floating IP:        false
	data:    Idle timeout in minutes:   10
	data:    Probes
	data:
	info:    network lb rule set command OK

Options de paramètre :

	-h, --help                                         output usage information
	-v, --verbose                                      use verbose output
	--json                                             use json output
	-g, --resource-group <resource-group>              the name of the resource group
	-l, --lb-name <lb-name>                            the name of the load balancer
	-n, --name <name>                                  the name of the rule
	-r, --new-rule-name <new-rule-name>                new rule name
	-p, --protocol <protocol>                          the rule protocol
	-f, --frontend-port <frontend-port>                the frontend port
	-b, --backend-port <backend-port>                  the backend port
	-e, --enable-floating-ip <enable-floating-ip>      enable floating point ip
	-i, --idle-timeout <idle-timeout>                  the idle timeout in minutes
	-a, --probe-name [probe-name]                      the name of the probe defined in the same load balancer
	-t, --frontend-ip-name <frontend-ip-name>          the name of the frontend ip configuration in the same load balancer
	-o, --backend-address-pool <backend-address-pool>  name of the backend address pool defined in the same load balancer
	-s, --subscription <subscription>                  the subscription identifier


	network lb rule list [options] <resource-group> <lb-name>

Répertorie toutes les règles d'équilibrage de charge configurées pour un équilibrage de charge dans un groupe de ressources spécifique.

	azure network lb rule list -g myresourcegroup -l mylb

	info:    Executing command network lb rule list
	+ Looking up the load balancer "mylb"
	data:    Name         Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes  Backend address pool  Probe data

	data:    mynewlbrule  Succeeded           Tcp       80             8080          false               10                       /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/mybackendpool
	info:    network lb rule list command OK

Options de paramètre :

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-s, --subscription <subscription>      the subscription identifier

	network lb rule delete [options] <resource-group> <lb-name> <name>

Supprime une règle d'équilibrage de charge.

	azure network lb rule delete -g myresourcegroup -l mylb -n mynewlbrule

	info:    Executing command network lb rule delete
	+ Looking up the load balancer "mylb"
	Delete load balancing rule mynewlbrule? [y/n] y
	+ Updating load balancer "mylb"
	info:    network lb rule delete command OK

Options de paramètre :

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-n, --name <name>                      the name of the rule
	-q, --quiet                            quiet mode, do not ask for delete confirmation
	-s, --subscription <subscription>      the subscription identifier

**Commandes pour gérer les règles NAT entrantes d'équilibrage de charge**

	network lb inbound-nat-rule create [options] <resource-group> <lb-name> <name>
Crée une règle NAT entrante pour l'équilibrage de charge.

Dans l'exemple ci-dessous, nous avons créé une règle NAT à partir d'une adresse IP frontale (préalablement définie. Consultez la commande « azure network frontend-ip » pour plus d'informations) avec un port d'écoute entrant et un port sortant auquel l'équilibrage de charge envoie le trafic réseau.


	azure network lb inbound-nat-rule create -g myresourcegroup -l mylb -n myinboundnat -p tcp -f 80 -b 8080 -i myfrontendip

	info:    Executing command network lb inbound-nat-rule create
	+ Looking up the load balancer "mylb"
	+ Updating load balancer "mylb"
	+ Looking up the load balancer "mylb"
	data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/inboundNatRules/myinboundnat
	data:    Name:                      myinboundnat
	data:    Type:                      Microsoft.Network/loadBalancers/inboundNatRules
	data:    Provisioning state:        Succeeded
	data:    Frontend IP Configuration: id=/subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
	data:    Backend IP configuration
	data:    Protocol                   Tcp
	data:    Frontend port              80
	data:    Backend port               8080
	data:    Enable floating IP         false
	info:    network lb inbound-nat-rule create command OK

Options de paramètre :

	-h, --help                                     output usage information
	-v, --verbose                                  use verbose output
	--json                                         use json output
	-g, --resource-group <resource-group>          the name of the resource group
	-l, --lb-name <lb-name>                        the name of the load balancer
	-n, --name <name>                              the name of the inbound NAT rule
	-p, --protocol <protocol>                      the rule protocol [tcp,udp]
	-f, --frontend-port <frontend-port>            the frontend port [0-65535]
	-b, --backend-port <backend-port>              the backend port [0-65535]
	-e, --enable-floating-ip <enable-floating-ip>  enable floating point ip [true,false]
	-i, --frontend-ip <frontend-ip>                the name of the frontend ip configuration
	-m, --vm-id <vm-id>                            the VM id.
	e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines/<vm-name>
	-a, --vm-name <vm-name>                        the VM name.This VM must exist in the same resource group as the lb.
	Please use vm-id if that is not the case.
	this parameter will be ignored if --vm-id is specified
	-s, --subscription <subscription>              the subscription identifier
<BR>

	network lb inbound-nat-rule set [options] <resource-group> <lb-name> <name>
Met à jour une règle NAT entrante existante. Dans l'exemple suivant, nous avons remplacé le port d'écoute entrant 80 par 81.

	azure network lb inbound-nat-rule set -g group-1 -l mylb -n myinboundnat -p tcp -f 81 -b 8080 -i myfrontendip

	info:    Executing command network lb inbound-nat-rule set
	+ Looking up the load balancer "mylb"
	+ Updating load balancer "mylb"
	+ Looking up the load balancer "mylb"
	data:    Id:                        /subscriptions/###############################/resourceGroups/group-1/providers/Microsoft.Network/loadBalancers/mylb/inboundNatRules/myinboundnat
	data:    Name:                      myinboundnat
	data:    Type:                      Microsoft.Network/loadBalancers/inboundNatRules
	data:    Provisioning state:        Succeeded
	data:    Frontend IP Configuration: id=/subscriptions/###############################/resourceGroups/group-1/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
	data:    Backend IP configuration
	data:    Protocol                   Tcp
	data:    Frontend port              81
	data:    Backend port               8080
	data:    Enable floating IP         false
	info:    network lb inbound-nat-rule set command OK

Options de paramètre :

	-h, --help                                     output usage information
	-v, --verbose                                  use verbose output
	--json                                         use json output
	-g, --resource-group <resource-group>          the name of the resource group
	-l, --lb-name <lb-name>                        the name of the load balancer
	-n, --name <name>                              the name of the inbound NAT rule
	-p, --protocol <protocol>                      the rule protocol [tcp,udp]
	-f, --frontend-port <frontend-port>            the frontend port [0-65535]
	-b, --backend-port <backend-port>              the backend port [0-65535]
	-e, --enable-floating-ip <enable-floating-ip>  enable floating point ip [true,false]
	-i, --frontend-ip <frontend-ip>                the name of the frontend ip configuration
	-m, --vm-id [vm-id]                            the VM id.
	e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines/<vm-name>
	-a, --vm-name <vm-name>                        the VM name.
	This virtual machine must exist in the same resource group as the lb.
	Please use vm-id if that is not the case
	-s, --subscription <subscription>              the subscription identifier
<BR>

	network lb inbound-nat-rule list [options] <resource-group> <lb-name>

Répertorie toutes les règles NAT entrantes pour un équilibrage de charge.

	azure network lb inbound-nat-rule list -g myresourcegroup -l mylb

	info:    Executing command network lb inbound-nat-rule list
	+ Looking up the load balancer "mylb"
	data:    Name          Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes  Backend IP configuration
	data:    ------------  ------------------  --------  -------------  ------------  ------------------  -----------------------  ---
	---------------------
	data:    myinboundnat  Succeeded           Tcp       81             8080          false               4

	info:    network lb inbound-nat-rule list command OK

Options de paramètre :

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-s, --subscription <subscription>      the subscription identifier
<BR>

	network lb inbound-nat-rule delete [options] <resource-group> <lb-name> <name>

Supprime la règle NAT pour l'équilibrage de charge dans un groupe de ressources spécifique.

	azure network lb inbound-nat-rule delete -g myresourcegroup -l mylb -n myinboundnat

	info:    Executing command network lb inbound-nat-rule delete
	+ Looking up the load balancer "mylb"
	Delete inbound NAT rule "myinboundnat?" [y/n] y
	+ Updating load balancer "mylb"
	info:    network lb inbound-nat-rule delete command OK

Options de paramètre :

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-n, --name <name>                      the name of the inbound NAT rule
	-q, --quiet                            quiet mode, do not ask for delete confirmation
	-s, --subscription <subscription>      the subscription identifier

**Commandes pour gérer les adresses IP publiques**

	network public-ip create [options] <resource-group> <name> <location>
Crée une ressource IP publique. Vous créez la ressource IP publique et l'associez à un nom de domaine.

	azure network public-ip create -g myresourcegroup -n mytestpublicip1 -l eastus -d azureclitest -a "Dynamic"
	info:    Executing command network public-ip create
	+ Looking up the public ip "mytestpublicip1"
	+ Creating public ip address "mytestpublicip1"
	+ Looking up the public ip "mytestpublicip1"
	data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mytestpublicip1
	data:    Name:                 mytestpublicip1
	data:    Type:                 Microsoft.Network/publicIPAddresses
	data:    Location:             eastus
	data:    Provisioning state:   Succeeded
	data:    Allocation method:    Dynamic
	data:    Idle timeout:         4
	data:    Domain name label:    azureclitest
	data:    FQDN:                 azureclitest.eastus.cloudapp.azure.com
	info:    network public-ip create command OK


Options de paramètre : -h, --help output usage information -v, --verbose use verbose output --json use json output -g, --resource-group <resource-group> the name of the resource group -n, --name <name> the name of the public ip -l, --location <location> the location -d, --domain-name-label <domain-name-label> the domain name label. This set DNS to <domain-name-label>.<location>.cloudapp.azure.com -a, --allocation-method <allocation-method> the allocation method [Static][Dynamic] -i, --idletimeout <idletimeout> the idle timeout in minutes -f, --reverse-fqdn <reverse-fqdn> the reverse fqdn -t, --tags <tags> the list of tags. Can be multiple. In the format of "name=value". Name is required and value is optional. For example, -t tag1=value1;tag2 -s, --subscription <subscription> the subscription identifier <br>

	network public-ip set [options] <resource-group> <name>
Met à jour les propriétés d'une ressource IP publique existante. Dans l'exemple suivant, nous avons remplacé l'adresse IP publique dynamique en adresse IP statique.

	azure network public-ip set -g group-1 -n mytestpublicip1 -d azureclitest -a "Static"
	info:    Executing command network public-ip set
	+ Looking up the public ip "mytestpublicip1"
	+ Updating public ip address "mytestpublicip1"
	+ Looking up the public ip "mytestpublicip1"
	data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mytestpublicip1
	data:    Name:                 mytestpublicip1
	data:    Type:                 Microsoft.Network/publicIPAddresses
	data:    Location:             eastus
	data:    Provisioning state:   Succeeded
	data:    Allocation method:    Static
	data:    Idle timeout:         4
	data:    IP Address:           (static IP address)
	data:    Domain name label:    azureclitest
	data:    FQDN:                 azureclitest.eastus.cloudapp.azure.com
	info:    network public-ip set command OK

Options de paramètre :

	-h, --help                                   output usage information
	-v, --verbose                                use verbose output
	--json                                       use json output
	-g, --resource-group <resource-group>        the name of the resource group
	-n, --name <name>                            the name of the public ip
	-d, --domain-name-label [domain-name-label]  the domain name label.
	This set DNS to <domain-name-label>.<location>.cloudapp.azure.com
	-a, --allocation-method <allocation-method>  the allocation method [Static][Dynamic]
	-i, --idletimeout <idletimeout>              the idle timeout in minutes
	-f, --reverse-fqdn [reverse-fqdn]            the reverse fqdn
	-t, --tags <tags>                            the list of tags.
	Can be multiple. In the format of "name=value".
	Name is required and value is optional.
	For example, -t tag1=value1;tag2
	--no-tags                                    remove all existing tags
	-s, --subscription <subscription>            the subscription identifier

<br> network public-ip list [options] <resource-group> Répertorie toutes les ressources IP publiques au sein d'un groupe de ressources.

	azure network public-ip list -g myresourcegroup

	info:    Executing command network public-ip list
	+ Getting the public ip addresses
	data:    Name             Location  Allocation  IP Address    Idle timeout  DNS Name
	data:    ---------------  --------  ----------  ------------  ------------  -------------------------------------------
	data:    mypubip5         westus    Dynamic                   4             "domain name".westus.cloudapp.azure.com
	data:    myPublicIP       eastus    Dynamic                   4             "domain name".eastus.cloudapp.azure.com
	data:    mytestpublicip   eastus    Dynamic                   4             "domain name".eastus.cloudapp.azure.com
	data:    mytestpublicip1  eastus   Static (Static IP address) 4             azureclitest.eastus.cloudapp.azure.com

Options de paramètre :

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-s, --subscription <subscription>      the subscription identifier
<BR> network public-ip show [options] <resource-group> <name> Affiche les propriétés d'adresse IP publique pour une ressource IP publique au sein d'un groupe de ressources.

	azure network public-ip show -g myresourcegroup -n mytestpublicip

	info:    Executing command network public-ip show
	+ Looking up the public ip "mytestpublicip1"
	data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mytestpublicip
	data:    Name:                 mytestpublicip
	data:    Type:                 Microsoft.Network/publicIPAddresses
	data:    Location:             eastus
	data:    Provisioning state:   Succeeded
	data:    Allocation method:    Static
	data:    Idle timeout:         4
	data:    IP Address:           (static IP address)
	data:    Domain name label:    azureclitest
	data:    FQDN:                 azureclitest.eastus.cloudapp.azure.com
	info:    network public-ip show command OK

Options de paramètre :

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-n, --name <name>                      the name of the public IP
	-s, --subscription <subscription>      the subscription identifier


	network public-ip delete [options] <resource-group> <name>

Supprime une ressource IP publique.

	azure network public-ip delete -g group-1 -n mypublicipname
	info:    Executing command network public-ip delete
	+ Looking up the public ip "mypublicipname"
	Delete public ip address "mypublicipname"? [y/n] y
	+ Deleting public ip address "mypublicipname"
	info:    network public-ip delete command OK

Options de paramètre :

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-n, --name <name>                      the name of the public IP
	-q, --quiet                            quiet mode, do not ask for delete confirmation
	-s, --subscription <subscription>      the subscription identifier


**Commandes pour gérer les interfaces réseau**

	network nic create [options] <resource-group> <name> <location>
Crée une ressource appelée interface réseau (NIC) qui peut être utilisée pour les équilibrages de charge ou associée à une machine virtuelle.

	azure network nic create -g myresourcegroup -l eastus -n testnic1 --subnet-name subnet-1 --subnet-vnet-name myvnet

	info:    Executing command network nic create
	+ Looking up the network interface "testnic1"
	+ Looking up the subnet "subnet-1"
	+ Creating network interface "testnic1"
	+ Looking up the network interface "testnic1"
	data:    Id:                     /subscriptions/c4a17ddf-aa84-491c-b6f9-b90d882299f7/resourceGroups/group-1/providers/Microsoft.Network/networkInterfaces/testnic1
	data:    Name:                   testnic1
	data:    Type:                   Microsoft.Network/networkInterfaces
	data:    Location:               eastus
	data:    Provisioning state:     Succeeded
	data:    IP configurations:
	data:       Name:                         NIC-config
	data:       Provisioning state:           Succeeded
	data:       Private IP address:           10.0.0.5
	data:       Private IP Allocation Method: Dynamic
	data:       Subnet:                       /subscriptions/c4a17ddf-aa84-491c-b6f9-b90d882299f7/resourceGroups/group-1/providers/Microsoft.Network/virtualNetworks/myVNET/subnets/Subnet-1

Options de paramètre :

	-h, --help                                                       output usage information
	-v, --verbose                                                    use verbose output
	--json                                                           use json output
	-g, --resource-group <resource-group>                            the name of the resource group
	-n, --name <name>                                                the name of the network interface
	-l, --location <location>                                        the location
	-w, --network-security-group-id <network-security-group-id>      the network security group identifier.
	e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/networkSecurityGroups/<nsg-name>
	-o, --network-security-group-name <network-security-group-name>  the network security group name.
	This network security group must exist in the same resource group as the nic.
	Please use network-security-group-id if that is not the case.
	-i, --public-ip-id <public-ip-id>                                the public IP identifier.
	e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/publicIPAddresses/<public-ip-name>
	-p, --public-ip-name <public-ip-name>                            the public IP name.
	This public ip must exist in the same resource group as the nic.
	Please use public-ip-id if that is not the case.
	-a, --private-ip-address <private-ip-address>                    the private IP address
	-u, --subnet-id <subnet-id>                                      the subnet identifier.
	e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<vnet-name>/subnets/<subnet-name>
	--subnet-name <subnet-name>                                  the subnet name
	-m, --subnet-vnet-name <subnet-vnet-name>                        the vnet name under which subnet-name exists
	-t, --tags <tags>                                                the comma seperated list of tags.
	Can be multiple. In the format of "name=value".
	Name is required and value is optional.
	For example, -t tag1=value1;tag2
	-s, --subscription <subscription>                                the subscription identifier
	data:
	info:    network nic create command OK

<BR>

	network nic set [options] <resource-group> <name>

	network nic list [options] <resource-group>
	network nic show [options] <resource-group> <name>
	network nic delete [options] <resource-group> <name>

**Commandes pour gérer les groupes de sécurité réseau**

	network nsg create [options] <resource-group> <name> <location>
	network nsg set [options] <resource-group> <name>
	network nsg list [options] <resource-group>
	network nsg show [options] <resource-group> <name>
	network nsg delete [options] <resource-group> <name>

**Commandes pour gérer les règles de groupe de sécurité réseau**

	network nsg rule create [options] <resource-group> <nsg-name> <name>
	network nsg rule set [options] <resource-group> <nsg-name> <name>
	network nsg rule list [options] <resource-group> <nsg-name>
	network nsg rule show [options] <resource-group> <nsg-name> <name>
	network nsg rule delete [options] <resource-group> <nsg-name> <name>

**Commandes pour gérer le profil Traffic Manager**

	network traffic-manager profile create [options] <resource-group> <name>
	network traffic-manager profile set [options] <resource-group> <name>
	network traffic-manager profile list [options] <resource-group>
	network traffic-manager profile show [options] <resource-group> <name>
	network traffic-manager profile delete [options] <resource-group> <name>
	network traffic-manager profile is-dns-available [options] <resource-group> <relative-dns-name>

**Commandes pour gérer les points de terminaison Traffic Manager**

	network traffic-manager profile endpoint create [options] <resource-group> <profile-name> <name> <endpoint-location>
	network traffic-manager profile endpoint set [options] <resource-group> <profile-name> <name>
	network traffic-manager profile endpoint delete [options] <resource-group> <profile-name> <name>

**Commandes pour gérer les passerelles de réseau virtuel**

	network gateway list [options] <resource-group>

## azure provider : commandes pour gérer les enregistrements de fournisseur de ressources

**Répertorier les fournisseurs actuellement enregistrés dans ARM**

	provider list [options]

**Afficher les détails de l'espace de noms du fournisseur demandé**

	provider show [options] <namespace>

**Enregistrer le fournisseur avec l'abonnement**

	provider register [options] <namespace>

**Annuler l'enregistrement du fournisseur dans l'abonnement**

	provider unregister [options] <namespace>

## azure resource : commandes pour gérer vos ressources

**Créer une ressource dans un groupe de ressources**

	resource create [options] <resource-group> <name> <resource-type> <location> <api-version>

**Mettre à jour une ressource dans un groupe de ressources sans aucun modèle ou paramètre**

	resource set [options] <resource-group> <name> <resource-type> <properties> <api-version>

**Répertorier les ressources**

	resource list [options] [resource-group]

**Obtenir une ressource au sein d'un groupe de ressources ou d'un abonnement**

	resource show [options] <resource-group> <name> <resource-type> <api-version>

**Supprimer une ressource dans un groupe de ressources**

	resource delete [options] <resource-group> <name> <resource-type> <api-version>

## azure role : commandes pour gérer vos rôles Azure

**Obtenir toutes les définitions de rôles disponibles**

	role list [options]

**Obtenir une définition de rôles disponible**

	role show [options] [name]

**Commandes pour gérer votre attribution de rôle**

	role assignment create [options] [objectId] [upn] [mail] [spn] [role] [scope] [resource-group] [resource-type] [resource-name]
	role assignment list [options] [objectId] [upn] [mail] [spn] [role] [scope] [resource-group] [resource-type] [resource-name]
	role assignment delete [options] [objectId] [upn] [mail] [spn] [role] [scope] [resource-group] [resource-type] [resource-name]

## azure storage : commandes pour gérer vos objets de stockage

**Commandes pour gérer vos comptes de stockage**

	storage account list [options]
	storage account show [options] <name>
	storage account create [options] <name>
	storage account set [options] <name>
	storage account delete [options] <name>

**Commandes pour gérer vos clés de compte de stockage**

	storage account keys list [options] <name>
	storage account keys renew [options] <name>

**Commandes pour afficher votre chaîne de connexion de stockage**

	storage account connectionstring show [options] <name>

**Commandes pour gérer vos conteneurs de stockage**

	storage container list [options] [prefix]
	storage container show [options] [container]
	storage container create [options] [container]
	storage container delete [options] [container]
	storage container set [options] [container]

**Commandes pour gérer les signatures d'accès partagé de votre conteneur de stockage**

	storage container sas create [options] [container] [permissions] [expiry]

**Commandes pour gérer les stratégies d'accès stockées de votre conteneur de stockage**

	storage container policy create [options] [container] [name]
	storage container policy show [options] [container] [name]
	storage container policy list [options] [container]
	storage container policy set [options] [container] [name]
	storage container policy delete [options] [container] [name]

**Commandes pour gérer vos objets blob de stockage**

	storage blob list [options] [container] [prefix]
	storage blob show [options] [container] [blob]
	storage blob delete [options] [container] [blob]
	storage blob upload [options] [file] [container] [blob]
	storage blob download [options] [container] [blob] [destination]

**Commandes pour gérer vos opérations de copie d'objet blob**

	storage blob copy start [options] [sourceUri] [destContainer]
	storage blob copy show [options] [container] [blob]
	storage blob copy stop [options] [container] [blob] [copyid]

**Commandes pour gérer la signature d'accès partagé de votre objet blob de stockage**

	storage blob sas create [options] [container] [blob] [permissions] [expiry]

**Commandes pour gérer vos partages de fichiers de stockage**

	storage share create [options] [share]
	storage share show [options] [share]
	storage share delete [options] [share]
	storage share list [options] [prefix]

**Commandes pour gérer vos fichiers de stockage**

	storage file list [options] [share] [path]
	storage file delete [options] [share] [path]
	storage file upload [options] [source] [share] [path]
	storage file download [options] [share] [path] [destination]

**Commandes pour gérer votre répertoire de fichiers de stockage**

	storage directory create [options] [share] [path]
	storage directory delete [options] [share] [path]

**Commandes pour gérer vos files d'attente de stockage**

	storage queue create [options] [queue]
	storage queue list [options] [prefix]
	storage queue show [options] [queue]
	storage queue delete [options] [queue]

**Commandes pour gérer les signatures d'accès partagé de votre file d'attente de stockage**

	storage queue sas create [options] [queue] [permissions] [expiry]

**Commandes pour gérer les stratégies d'accès stockées de votre file d'attente de stockage**

	storage queue policy create [options] [queue] [name]
	storage queue policy show [options] [queue] [name]
	storage queue policy list [options] [queue]
	storage queue policy set [options] [queue] [name]
	storage queue policy delete [options] [queue] [name]

**Commandes pour gérer vos propriétés de journalisation de stockage**

	storage logging show [options]
	storage logging set [options]

**Commandes pour gérer vos propriétés de mesure de stockage**

	storage metrics show [options]
	storage metrics set [options]

**Commandes pour gérer vos tables de stockage**

	storage table create [options] [table]
	storage table list [options] [prefix]
	storage table show [options] [table]
	storage table delete [options] [table]

**Commandes pour gérer les signatures d'accès partagé de votre table de stockage**

	storage table sas create [options] [table] [permissions] [expiry]

**Commandes pour gérer les stratégies d'accès stockées de votre table de stockage**

	storage table policy create [options] [table] [name]
	storage table policy show [options] [table] [name]
	storage table policy list [options] [table]
	storage table policy set [options] [table] [name]
	storage table policy delete [options] [table] [name]

## azure tag : commandes pour gérer les balises de votre gestionnaire de ressources

**Ajouter une balise**

	tag create [options] <name> <value>

**Supprimer une balise entière ou une valeur de balise**

	tag delete [options] <name> <value>

**Répertorier les informations de balise**

	tag list [options]

**Obtenir une balise**

	tag show [options] [name]

## azure vm : commandes pour gérer vos machines virtuelles Azure

**Créer une machine virtuelle**

	vm create [options] <resource-group> <name> <location> <os-type>

**Créer une machine virtuelle avec les ressources par défaut**

	vm quick-create [options] <resource-group> <name> <location> <os-type> <image-urn> <admin-username> <admin-password>

**Répertorier les machines virtuelles au sein d'un groupe de ressources**

	vm list [options] <resource-group>

**Obtenir une machine virtuelle au sein d'un groupe de ressources**

	vm show [options] <resource-group> <name>

**Supprimer une machine virtuelle au sein d'un groupe de ressources**

	vm delete [options] <resource-group> <name>

**Arrêter une machine virtuelle au sein d'un groupe de ressources**

	vm stop [options] <resource-group> <name>

**Redémarrer une machine virtuelle au sein d'un groupe de ressources**

	vm restart [options] <resource-group> <name>

**Démarrer une machine virtuelle au sein d'un groupe de ressources**

	vm start [options] <resource-group> <name>

**Arrêter une machine virtuelle au sein d'un groupe de ressources et libérer les ressources de calcul**

	vm deallocate [options] <resource-group> <name>

**Répertorier les tailles disponibles de machine virtuelle**

	vm sizes [options]

**Capturer la machine virtuelle en tant qu'image de système d'exploitation ou image de machine virtuelle**

	vm capture [options] <resource-group> <name> <vhd-name-prefix>

**Définir l'état de la machine virtuelle avec la valeur Généralisé**

	vm generalize [options] <resource-group> <name>

**Obtenir la vue d'instance de la machine virtuelle**

	vm get-instance-view [options] <resource-group> <name>

**Vous permet de réinitialiser les paramètres d'accès Bureau à distance ou SSH sur une machine virtuelle et de réinitialiser le mot de passe pour le compte d'administrateur ou sudo**

	vm reset-access [options] <resource-group> <name>

**Mettre à jour une machine virtuelle avec de nouvelles données**

	vm set [options] <resource-group> <name>

**Commandes pour gérer vos disques de données de machine virtuelle**

	vm disk attach-new [options] <resource-group> <vm-name> <size-in-gb> [vhd-name]
	vm disk detach [options] <resource-group> <vm-name> <lun>
	vm disk attach [options] <resource-group> <vm-name> [vhd-url]

**Commandes pour gérer les extensions de ressource de machine virtuelle**

	vm extension set [options] <resource-group> <vm-name> <name> <publisher-name> <version>
	vm extension get [options] <resource-group> <vm-name>

**Commandes pour gérer votre machine virtuelle Docker**

	vm docker create [options] <resource-group> <name> <location> <os-type>

**Commandes pour gérer les images de machine virtuelle**

	vm image list-publishers [options] <location>
	vm image list-offers [options] <location> <publisher>
	vm image list-skus [options] <location> <publisher> <offer>
	vm image list [options] <location> <publisher> [offer] [sku]

<!---HONumber=58--> 