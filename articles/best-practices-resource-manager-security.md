---
title: Considérations de sécurité pour Resource Manager | Microsoft Docs
description: Montre les approches recommandées dans Azure Resource Manager pour la sécurisation des ressources avec des clés et des secrets, l’accès basé sur les rôles, contrôlent et les groupes de sécurité du réseau.
services: azure-resource-manager
documentationcenter: ''
author: george-moore
manager: georgem
editor: tysonn

ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2016
ms.author: georgem;tomfitz

---
# Questions de sécurité relatives à Azure Resource Manager
Lorsqu’on examine les aspects relatifs à la sécurité pour vos modèles Azure Resource Manager, il convient de prendre en compte plusieurs domaines tels que les clés et les secrets, le contrôle d’accès en fonction du rôle et les groupes de sécurité réseau.

Cette rubrique suppose que vous êtes familiarisé avec la notion de contrôle d’accès en fonction du rôle (RBAC) dans Azure Resource Manager. Pour plus d’informations, consultez la page [Contrôle d’accès en fonction du rôle Azure](active-directory/role-based-access-control-configure.md).

Cette rubrique fait partie d’un livre blanc plus volumineux. Pour lire tout le document, téléchargez [World Class ARM Templates Considerations and Proven Practices](http://download.microsoft.com/download/8/E/1/8E1DBEFA-CECE-4DC9-A813-93520A5D7CFE/World Class ARM Templates - Considerations and Proven Practices.pdf).

## Secrets et certificats
Les fonctions Azure Virtual Machines, Azure Resource Manager et Azure Key Vault sont totalement intégrés afin de prendre en charge la gestion sécurisée des certificats qui doivent être déployés dans la machine virtuelle. L’utilisation d’Azure Key Vault avec le gestionnaire de ressources afin d’organiser et de stocker les secrets de machine virtuelle et les certificats fait partie des meilleures pratiques et offre les avantages suivants ordinateur virtuel avec le Gestionnaire de ressources est une meilleure pratique et offre les avantages suivants :

* Les modèles contiennent uniquement des références URI à des secrets, ce qui signifie que les secrets réels ne se trouvent pas dans le code, la configuration ou les référentiels de code source. Cela évite les attaques de type hameçonnage sur les clés dans les référentiels internes ou externes, par exemple, de programmes zombies dans Github.
* Les secrets stockés dans le coffre à clé sont sous le contrôle RBAC total d’un opérateur de confiance. Si l’opérateur de confiance quitte l’entreprise ou est transféré dans un autre groupe au sein de la société, il n’a plus accès aux clés qu’il a créées dans le coffre.
* Cloisonnement complet de toutes les ressources :
  * modèles pour déployer les clés
  * modèles servant à déployer une machine virtuelle avec des références aux clés
  * les documents clés réels dans le coffre. Chaque modèle (et action) peut se trouver sous différents rôles RBAC pour une séparation complète des tâches.
* Le chargement des secrets dans une machine virtuelle au moment du déploiement s’effectue via un canal direct entre Azure Fabric et le coffre de clés, dans les limites du centre de données Microsoft Datacenter. Une fois les clés placées dans le coffre de clés, elles ne sont jamais « visibles » sur un canal non approuvé hors du centre de données.
* Les coffres de clés ont toujours un caractère régional et les secrets disposent toujours d’une localité (et de la souveraineté) auprès des machines virtuelles. Il n’existe pas de coffre de clés global.

### Séparation des clés à partir des déploiements
Une bonne pratique consiste à mettre à jour des modèles distincts pour :

1. La création de coffres (qui contiennent le matériel clé)
2. Déploiement des machines virtuelles (avec des références d’URI pour les clés contenues dans les coffres)

Un scénario d’entreprise classique consiste à offrir à un petit groupe d’opérateurs de confiance l’accès à des secrets cruciaux figurant dans les charges de travail déployées, avec un groupe plus étendu de personnel de développement/opérationnel pouvant créer ou mettre à jour des déploiements de machines virtuelles. Voici un exemple de modèle ARM qui crée et configure un nouveau coffre dans le cadre de l’identification de l’utilisateur actuellement authentifié dans Azure Active Directory. Cet utilisateur aurait par défaut l’autorisation de créer, supprimer, répertorier, mettre à jour, sauvegarder, restaurer et obtenir la moitié publique de clés de ce coffre de clé.

Alors que la plupart des champs de ce modèle doivent être explicites, le paramètre **enableVaultForDeployment** nécessite davantage d’explications : les coffres n’offrent à aucun autre composant d’infrastructure Azure un accès permanent par défaut. Lorsque cette valeur est définie, elle donne aux composants d’infrastructure Calcul Azure un accès en lecture seule à ce coffre nommé spécifiquement. Par conséquent, une autre meilleure pratique consiste à ne pas mélanger des données d’entreprise sensibles dans le même coffre de machine virtuelle.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "keyVaultName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the Vault"
                }
            },
            "location": {
                "type": "string",
                "allowedValues": ["East US", "West US", "West Europe", "East Asia", "South East Asia"],
                "metadata": {
                    "description": "Location of the Vault"
                }
            },
            "tenantId": {
                "type": "string",
                "metadata": {
                    "description": "Tenant Id of the subscription. Get using Get-AzureSubscription cmdlet or Get Subscription API"
                }
            },
            "objectId": {
                "type": "string",
                "metadata": {
                    "description": "Object Id of the AD user. Get using Get-AzureADUser cmdlet"
                }
            },
            "skuName": {
                "type": "string",
                "allowedValues": ["Standard", "Premium"],
                "metadata": {
                    "description": "SKU for the vault"
                }
            },
            "enableVaultForDeployment": {
                "type": "bool",
                "allowedValues": [true, false],
                "metadata": {
                    "description": "Specifies if the vault is enabled for a VM deployment"
                }
            }
        },
        "resources": [{
            "type": "Microsoft.KeyVault/vaults",
            "name": "[parameters('keyVaultName')]",
            "apiVersion": "2014-12-19-preview",
            "location": "[parameters('location')]",
            "properties": {
                "enabledForDeployment": "[parameters('enableVaultForDeployment')]",
                "tenantid": "[parameters('tenantId')]",
                "accessPolicies": [{
                    "tenantId": "[parameters('tenantId')]",
                    "objectId": "[parameters('objectId')]",
                    "permissions": {
                        "secrets": ["all"],
                        "keys": ["all"]
                    }
                }],
                "sku": {
                    "name": "[parameters('skuName')]",
                    "family": "A"
                }
            }
        }]
    }

Une fois le coffre créé, l’étape suivante consiste à faire référence à ce coffre dans le modèle de déploiement d’une nouvelle machine virtuelle. Comme indiqué ci-dessus, une bonne pratique consiste à faire gérer les déploiements de machine virtuelle par un groupe de dév/ops différent, sans donner au groupe aucun accès direct aux clés stockées dans le coffre.

Le fragment de modèle ci-dessous serait inclus dans des constructions de déploiement de niveau supérieur, chacun référençant en toute sécurité les secrets sensibles qui ne seront pas sous le contrôle direct de l’opérateur.

    "vaultName": {
        "type": "string",
        "metadata": {
            "description": "Name of Key Vault that has a secret"
        }
    },
    {
        "apiVersion": "2015-05-01-preview",
        "type": "Microsoft.Compute/virtualMachines",
        "name": "[parameters('vmName')]",
        "location": "[parameters('location')]",
        "properties": {
            "osProfile": {
                "secrets": [{
                    "sourceVault": {
                        "id": "[resourceId('vaultrg', 'Microsoft.KeyVault/vaults', 'kayvault')]"
                    },
                    "vaultCertificates": [{
                        "certificateUrl": "[parameters('secretUrlWithVersion')]",
                        "certificateStore": "My"
                    }]
                }]
            }
        }
    }

Pour passer une valeur d’un coffre de clés en tant que paramètre lors du déploiement d'un modèle, consultez [Passer des valeurs sécurisées lors du déploiement](resource-manager-keyvault-parameter.md).

## Principaux de service pour les interactions inter abonnements
les identités de service sont représentées sous forme de principaux du service dans Active Directory. Les principaux de service seront au centre de scénarios de clés pour les organisations informatiques, les intégrateurs système (SI) et les fournisseurs de services de Cloud (CSV). Dans certains cas particuliers, ces organisations devront interagir avec l’abonnement d’un de leurs clients.

Votre organisation peut fournir une offre qui servant à gérer une solution déployée dans l’environnement et l’abonnement de vos clients. Dans ce cas, vous devrez accéder aux journaux et à d’autres données du compte de clients afin que vous puissiez l’utiliser dans votre solution d’analyse. Si vous êtes une organisation d’entreprise informatique, un intégrateur de systèmes, vous pouvez proposer à votre client une offre où vous allez déployer et gérer pour lui une fonctionnalité, par exemple, une plate-forme d’analyse de données où l’offre réside dans l’abonnement du client.

Dans ces cas d’utilisation, votre organisation a besoin d’une identité pouvant recevoir un accès pour exécuter ces actions dans le contexte d’un abonnement client.

Ces scénarios suscitent chez eux un certain nombre de considérations :

* Pour des raisons de sécurité, l’accès peut être limité à certains types d’actions, par exemple, un accès en lecture seule.
* Comme les ressources déployées sont fournies moyennant des frais, il peut exister des contraintes similaires sur l’accès requis pour des raisons financières.
* Pour des raisons de sécurité, l’accès peut être limité à une ressource spécifique (comptes de stockage) ou des ressources (groupe de ressources contenant une solution ou un environnement)
* Comme une relation avec un fournisseur peut évoluer, le client souhaite avoir la possibilité d’activer/désactiver l’accès SI ou CSV
* Comme les actions sur le compte peuvent avoir un impact en matière de facturation, le client souhaite avoir la possibilité d’auditer et d’assumer la responsabilité de la facturation.
* Du point de vue de la conformité, le client souhaite pouvoir auditer votre comportement dans son environnement

Une combinaison d’un principal de service et de RBAC permet de répondre à ces exigences.

## groupes de sécurité réseau ;
De nombreux scénarios ont des exigences spécifiant la façon dont le trafic vers une ou plusieurs instances de machine virtuelle du réseau virtuel est contrôlé. Vous pouvez utiliser un groupe de sécurité réseau (NSG) à cette fin dans le cadre du déploiement d’un modèle ARM.

Un groupe de sécurité réseau est un objet de niveau supérieur associé à votre abonnement. Un NSG contient les règles de contrôle d’accès qui autorisent ou refusent le trafic vers des instances de machine virtuelle. Les règles d'un groupe de sécurité réseau peuvent être modifiées à tout moment et les modifications sont appliquées à toutes les instances associées. Pour utiliser un groupe de sécurité réseau, vous devez disposer d’un réseau virtuel associé à une région (emplacement). Les groupes de sécurité réseau ne sont pas compatibles avec les réseaux virtuels associés à un groupe d’affinités. Si vous ne disposez pas d’un réseau virtuel régional et souhaitez contrôler le trafic vers vos points de terminaison, consultez [À propos des listes de contrôle d’accès réseau](virtual-network/virtual-networks-acl.md).

Vous pouvez associer un groupe de sécurité réseau à une machine virtuelle ou à un sous-réseau dans un réseau virtuel. Associé à une machine virtuelle, le groupe de sécurité réseau s'applique à tout le trafic envoyé et reçu par l'instance de la machine virtuelle. Lorsqu’il est appliqué à un sous-réseau au sein de votre réseau virtuel, il s’applique à l’ensemble du trafic envoyé et reçu par toutes les instances de la machine virtuelle au sein du sous-réseau. Une machine virtuelle ou un sous-réseau peut être associé à 1 seul groupe de sécurité réseau, et chaque groupe de sécurité réseau peut contenir jusqu’à 200 règles. Vous pouvez avoir 100 groupes de sécurité réseau par abonnement.

> [!NOTE]
> Les contrôles d’accès réseau basés sur le point de terminaison et les groupes de sécurité réseau ne sont pas pris en charge sur la même instance de machine virtuelle. Si vous souhaitez utiliser un groupe de sécurité réseau et une ACL de point de terminaison déjà en place, supprimez d'abord l’ACL de point de terminaison. Pour en savoir plus sur cette procédure, consultez [Gestion des listes de contrôle d’accès (ACL) pour les points de terminaison à l’aide de PowerShell](virtual-network/virtual-networks-acl-powershell.md).
> 
> 

### À propos des groupes de sécurité réseau
Les groupes de sécurité réseau sont différents des ACL basées sur le point de terminaison. Les ACL de point de terminaison fonctionnent uniquement sur le port public exposé via le point de terminaison d’entrée. Un groupe de sécurité réseau fonctionne sur une ou plusieurs instances de machine virtuelle et contrôle tout le trafic entrant et sortant sur la machine virtuelle.

Un groupe de sécurité réseau a un *Nom*, est associé à une *Région* (un des emplacements Azure pris en charge) et reçoit une étiquette descriptive. Il contient deux types de règles : les règles de trafic entrant et les règles de trafic sortant. Les règles de trafic entrant sont appliquées aux paquets entrants sur une machine virtuelle et les règles de trafic sortant sont appliquées aux paquets sortants à partir de la machine virtuelle. Les règles sont appliquées à l’hôte où se trouve la machine virtuelle. Un paquet entrant ou sortant doit respecter une règle d’autorisation pour être autorisé ; dans le cas contraire, il est supprimé.

Les règles sont traitées dans l'ordre de priorité. Par exemple, une règle avec un numéro de priorité inférieur (par exemple, 100) est traitée avant les règles avec une valeur de priorité plus élevée (par exemple, 200). Une fois qu'une correspondance est trouvée, aucune autre règle n'est traitée.

Une règle spécifie les éléments suivants :

* Nom : un identificateur unique pour la règle
* Type : entrant et sortant
* Priorité : entier compris entre 100 et 4 096 (règles traitées de faible à élevée)
* Adresse IP source : CIDR de plage d’IP source
* Plage de ports source : entier ou plage comprise entre 0 et 65536
* Plage d’adresses IP de destination : CIDR de la plage d’adresses IP de destination
* Plage de Port de destination : un entier ou une plage comprise entre 0 et 65536
* Protocole : TCP, UDP ou « * »
* Accès : Autoriser/Refuser

### Règles par défaut
Un groupe de sécurité réseau contient des règles par défaut. Les règles par défaut ne peuvent pas être supprimées, mais comme la priorité la plus basse leur est attribuée, elles peuvent être remplacées par les règles que vous créez. Les règles par défaut décrivent les paramètres par défaut recommandés par la plateforme. Comme illustré par les règles par défaut ci-dessous, le trafic d’origine et de fin d’un réseau virtuel est autorisé à la fois dans les directions entrante et sortante.

La connectivité à Internet est autorisée dans la direction sortante, mais elle est bloquée par défaut dans la direction entrante. Une règle par défaut permet à l’équilibrage de charge Azure de tester l’intégrité d’un ordinateur virtuel. Vous pouvez remplacer cette règle si la machine virtuelle ou l'ensemble de machines virtuelles sous le groupe de sécurité réseau n'est pas inclus dans le jeu d'équilibrage de charge.

Les règles par défaut sont indiquées dans les tableaux ci-dessous.

**Les règles par défaut sont :**

| Nom | Priorité | IP Source | Port source | IP de destination | Port de destination | Protocole | Access |
| --- | --- | --- | --- | --- | --- | --- | --- |
| AUTORISER LE TRAFIC ENTRANT DU RÉSEAU VIRTUEL |65 000 |VIRTUAL\_NETWORK |* |VIRTUAL\_NETWORK |* |* |AUTORISER |
| AUTORISER LE TRAFIC ENTRANT DE L'ÉQUILIBREUR DE CHARGE AZURE |65 001 |AZURE\_LOADBALANCER |* |* |* |* |AUTORISER |
| REFUSER TOUT TRAFIC ENTRANT |65 500 |* |* |* |* |* |REFUSER |

**Les règles sortantes par défaut sont :**

| Nom | Priorité | IP Source | Port source | IP de destination | Port de destination | Protocole | Access |
| --- | --- | --- | --- | --- | --- | --- | --- |
| AUTORISER LE TRAFIC SORTANT DU RÉSEAU VIRTUEL |65 000 |VIRTUAL\_NETWORK |* |VIRTUAL\_NETWORK |* |* |AUTORISER |
| AUTORISER LE TRAFIC SORTANT D’INTERNET |65 001 |* |* |INTERNET |* |* |AUTORISER |
| REFUSER TOUT TRAFIC SORTANT |65 500 |* |* |* |* |* |REFUSER |

### Règles d’infrastructure spéciales
Les règles du groupe de sécurité réseau sont explicites. Aucun trafic n'est autorisé ou refusé au-delà de ce qui est spécifié dans les règles du groupe de sécurité réseau. Toutefois, il existe deux types de trafic qui sont toujours autorisés indépendamment de la spécification du groupe de sécurité réseau. Ces approvisionnements sont effectués pour prendre en charge l’infrastructure.

* **Adresse IP virtuelle du nœud hôte :** des services d’infrastructure de base tels que DHCP, DNS et l’analyse du fonctionnement sont fournis via l’adresse IP d’hôte virtualisé 168.63.129.16. Cette adresse IP publique appartient à Microsoft et la seule adresse IP virtualisée utilisée dans toutes les régions à cet effet. Cette adresse IP mappe vers l’adresse IP physique de l’ordinateur (nœud hôte) du serveur qui héberge la machine virtuelle. Le nœud hôte agit en tant que relais DHCP, le programme de résolution récursif DNS et la sonde source de la sonde d’intégrité de l’équilibreur de charge et de la sonde d’intégrité de la machine. La communication à cette adresse IP ne doit pas être considérée comme une attaque.
* **Gestion des licences (service de gestion de clés) :** les images Windows en cours d’exécution sur les machines virtuelles doivent être acquises sous licence. Pour cela, une demande de licence est envoyée aux serveurs hôtes du service de gestion de clés qui gèrent ces requêtes. Ce sera toujours sur le port 1688 sortant.

### Balises par défaut
Les balises par défaut sont des identificateurs fournis par le système pour adresser une catégorie d'adresses IP. Les balises par défaut peuvent être spécifiées dans les règles définies par le client.

**Balises par défaut pour NSG**

| Balise | Description |
| --- | --- |
| VIRTUAL\_NETWORK |Désigne tous les espaces d’adressage de votre réseau. Il inclut l'espace d'adressage du réseau virtuel (IP CIDR dans Azure), ainsi que tout espace d'adressage local connecté (réseaux locaux). Cela inclut également des espaces d’adressage réseau virtuel - réseau virtuel. |
| AZURE\_LOADBALANCER |Désigne l’équilibrage de charge de l’infrastructure Azure et convertit en une adresse IP de centre de données Azure l’emplacement d’où proviennent les sondes d’intégrité d’Azure. Cela est nécessaire uniquement si la machine virtuelle ou un ensemble de machines virtuelles associées au groupe de sécurité réseau fait partie d'un jeu d'équilibrage de charge. |
| INTERNET |Cette balise par défaut indique l’espace d’adresse IP qui se trouve en dehors du réseau virtuel et est accessible par l’Internet public. Cette plage inclut espace IP public d’Azure. |

### Ports et plages de ports
Les règles de groupe de sécurité réseau peuvent être spécifiées sur un port source ou un port de destination unique, ou sur une plage de ports. Cette approche est particulièrement utile lorsque vous souhaitez ouvrir une grande plage de ports pour une application telle que FTP. La plage doit uniquement être séquentielle et ne peut pas être combinée avec la spécification d’un port individuel. Pour spécifier une plage de ports, utilisez le caractère trait d’union ( –). Par exemple, **100-500**.

### Trafic ICMP
Grâce aux règles NSG actuelles, vous pouvez spécifier les protocoles TCP ou UDP, mais pas ICMP. Toutefois, le trafic ICMP est autorisé dans un réseau virtuel par défaut via les règles de trafic entrant du réseau virtuel qui autorisent le trafic de/vers n’importe quels port et protocole « * » dans le réseau virtuel.

### Association d’un groupe de sécurité réseau à une machine virtuelle
Lorsqu’un groupe de sécurité réseau est directement associé à une machine virtuelle, les règles d’accès réseau dans le groupe de sécurité réseau s’appliquent directement l’ensemble du trafic destiné à la machine virtuelle. À chaque fois que le groupe de sécurité réseau est mis à jour pour les modifications de règle, les modifications sont intégrées au trafic de gestion en quelques minutes. Lorsque le NSG est dissocié de la machine virtuelle, l’état revient à sa condition pre-NSG, autrement dit, aux valeurs par défaut du système avant l’introduction du NSG.

### Association d’un NSG avec un sous-réseau
Lorsqu’un NSG est associé à un sous-réseau, les règles d’accès réseau dans le NSG sont appliquées à toutes les machines virtuelles dans le sous-réseau. Chaque fois que les règles d’accès dans le groupe de sécurité réseau sont mises à jour, les modifications sont appliquées à toutes les machines virtuelles dans le sous-réseau en quelques minutes.

### Associer un NSG à un sous-réseau et à une machine virtuelle
Vous pouvez associer un NSG à une machine virtuelle et un autre NSG au sous-réseau sur lequel réside la machine virtuelle. Ce scénario est pris en charge pour fournir à la machine virtuelle deux niveaux de protection. Sur le trafic entrant, le paquet suit les règles d’accès spécifiées dans le sous-réseau, suivi par les règles de la machine virtuelle. S’il s’agit d’un paquet sortant, il suit d’abord les règles spécifiées dans la machine virtuelle, puis les règles spécifiées dans le sous-réseau comme indiqué ci-dessous.

![Associer un NSG à un sous-réseau et une machine virtuelle](./media/best-practices-resource-manager-security/nsg-subnet-vm.png)

Lorsqu’un groupe de sécurité réseau est associé à une machine virtuelle ou à un sous-réseau, les règles de contrôle d’accès réseau deviennent très explicites. La plate-forme n'insère pas de règle implicite pour autoriser le trafic vers un port particulier. Dans ce cas, si vous créez un point de terminaison dans la machine virtuelle, vous devez également créer une règle pour autoriser le trafic provenant d’Internet. Si vous ne procédez pas ainsi, *VIP:{Port}* n’est pas accessible depuis l’extérieur.

Par exemple, vous pouvez créer une nouvelle machine virtuelle et un nouveau NSG. Associez le NSG à la machine virtuelle. La machine virtuelle peut communiquer avec les autres machines virtuelles dans le réseau virtuel via la règle AUTORISER LE TRAFIC ENTRANT DU RÉSEAU VIRTUEL. La machine virtuelle peut également établir des connexions sortantes à Internet à l'aide de la règle AUTORISER LE TRAFIC SORTANT D’INTERNET. Ensuite, vous créez un point de terminaison sur le port 80 pour recevoir le trafic vers votre site web en cours d'exécution dans la machine virtuelle. Les paquets destinés au port 80 sur l’adresse IP virtuelle (adresse IP virtuelle publique) à partir d’Internet n’atteindront pas la machine virtuelle avant que vous ayez ajouté une règle similaire à la table suivante pour le groupe de sécurité réseau.

**Règle explicite autorisant le trafic vers un port particulier**

| Nom | Priorité | IP Source | Port source | IP de destination | Port de destination | Protocole | Access |
| --- | --- | --- | --- | --- | --- | --- | --- |
| WEB |100 |INTERNET |* |* |80 |TCP |AUTORISER |

## Itinéraires définis par l’utilisateur
Azure utilise une table d’itinéraires pour déterminer comment transférer le trafic IP en fonction de la destination de chaque paquet. Bien qu’Azure fournisse une table d’itinéraires par défaut en fonction de vos paramètres de réseau virtuel, vous devrez peut-être ajouter des itinéraires personnalisés à cette table.

Le besoin le plus courant pour une entrée personnalisée de la table d’itinéraires est l’utilisation d’une appliance virtuelle dans votre environnement Azure. Étudiez le scénario illustré dans la figure ci-dessous. Supposons que vous souhaitez vous assurer que tout le trafic dirigé vers les sous-réseaux intermédiaires et principaux initiés à partir du sous-réseau frontal passe par une appliance de pare-feu virtuelle. L’ajout de l’appliance à votre réseau virtuel et sa connexion aux différents sous-réseaux n’est pas la solution. Vous devez également modifier la table d’itinéraires appliquée à votre sous-réseau pour garantir que les paquets sont transférés vers l’appliance de pare-feu virtuelle.

Il en est de même si vous avez implémenté une appliance virtuelle NAT pour contrôler le trafic entre votre réseau virtuel Azure et Internet. Pour garantir que l’appliance virtuelle est utilisée, vous devez créer un itinéraire spécifiant que tout le trafic destiné à Internet doit être transféré vers l’appliance virtuelle.

### Routage
Les paquets sont acheminés via un réseau TCP/IP basé sur une table d’itinéraires définie sur chaque nœud du réseau physique. Une table d’itinéraires est une collection d’itinéraires individuels permettant de déterminer où transférer les paquets en fonction de l’adresse IP de destination. Un itinéraire se compose des éléments suivants :

* Préfixe d’adresse. CIDR de destination auquel s’applique l’itinéraire, par exemple 10.1.0.0/16.
* Type de tronçon suivant. Type de tronçon Azure vers lequel le paquet doit être envoyé. Les valeurs possibles sont les suivantes :
  * Local. Représente le réseau virtuel local. Par exemple, si vous avez deux sous-réseaux, 10.1.0.0/16 et 10.2.0.0/16 qui sont situés dans le même réseau virtuel, l’itinéraire de chaque sous-réseau de la table d’itinéraires a la valeur de tronçon suivant Local.
  * Passerelle VPN Représente une passerelle VPN de site à site Azure.
  * Internet. Représente la passerelle Internet par défaut fournie par l’infrastructure Azure
  * Appliance virtuelle. Représente une appliance virtuelle que vous avez ajoutée à votre réseau virtuel Azure.
  * NULL. Représente un trou noir. Les paquets transmis à un trou noir ne sont pas du tout transférés.
* Valeur de tronçon suivant. La valeur de tronçon suivant contient l’adresse IP vers laquelle les paquets doivent être transférés. Les valeurs de tronçon suivant sont autorisées uniquement dans les itinéraires où le type de tronçon suivant est *Appliance virtuelle*. Le saut suivant doit avoir lieu sur le sous-réseau (l’interface locale de l’appliance virtuelle selon l’ID de réseau), et non sur un sous-réseau distant.

![Routage](./media/best-practices-resource-manager-security/routing.png)

### Itinéraires par défaut
Chaque sous-réseau créé dans un réseau virtuel est automatiquement associé à une table de routage qui comporte les règles suivantes d’itinéraires par défaut :

* Règle locale Vnet : cette règle est automatiquement créée pour chaque sous-réseau d’un réseau virtuel. Elle indique qu’il existe un lien direct entre les machines virtuelles et le réseau virtuel, et qu’aucun tronçon intermédiaire suivant n’est à signaler. Cela permet aux machines virtuelles situées sur le même sous-réseau, quel que soit l’ID du réseau dans lequel elles existent, de communiquer entre elles sans recourir à une adresse de passerelle par défaut.
* Règle locale : cette règle s’applique à tout le trafic destiné à la plage d’adresses locales et utilise une passerelle VPN en tant que tronçon suivant de destination.
* Règle Internet : cette règle traite l’ensemble du trafic destiné à l’Internet public et utilise la passerelle Internet d’infrastructure en tant que tronçon suivant pour l’ensemble du trafic destiné au réseau Internet.

### Itinéraires BGP
Au moment de la rédaction de cet article, [ExpressRoute](expressroute/expressroute-introduction.md) n’est pas encore pris en charge par le [fournisseur de ressources réseau](virtual-network/resource-groups-networking.md) d’Azure Resource Manager. Si vous disposez d’une connexion ExpressRoute entre votre réseau local et Azure, vous pouvez activer BGP pour propager les itinéraires de votre réseau local vers Azure une fois qu’ExpressRoute est pris en charge dans NRP. Ces itinéraires BGP sont utilisés de la même façon que les itinéraires par défaut et les itinéraires définis par l’utilisateur dans chaque sous-réseau Azure. Pour plus d’informations, consultez la page [Présentation d’ExpressRoute](expressroute/expressroute-introduction.md)

> [!NOTE]
> Une fois ExpressRoute pris en charge sur NRP, vous pouvez configurer votre environnement Azure de manière à ce qu’il utilise le tunneling forcé via votre réseau local en créant un itinéraire défini par l’utilisateur pour le sous-réseau 0.0.0.0/0 qui utilise la passerelle VPN comme tronçon suivant. Toutefois, cela ne fonctionne que si vous utilisez une passerelle VPN, et non ExpressRoute. Pour ExpressRoute, le tunneling forcé est configuré via BGP.
> 
> 

### Itinéraires définis par l’utilisateur
Vous ne pouvez pas afficher les itinéraires par défaut spécifiés ci-dessus dans votre environnement Azure, et pour la plupart des environnements, ce sont les seuls itinéraires dont vous avez besoin. Toutefois, vous pouvez créer une table d’itinéraires et ajouter un ou plusieurs itinéraires dans des cas spécifiques, par exemple :

* Forcer le tunneling vers Internet via votre réseau local.
* Utiliser des appliances virtuelles dans votre environnement Azure.

Dans les scénarios ci-dessus, vous devez créer une table d’itinéraires et lui ajouter des itinéraires définis par l’utilisateur. Vous pouvez avoir plusieurs tables d’itinéraires et celles-ci peuvent être associées à un ou plusieurs sous-réseaux. Chaque sous-réseau ne peut être associé qu’à une seule table d’itinéraires. L’ensemble des machines virtuelles et services cloud d’un sous-réseau utilisent la table d’itinéraires associée à ce sous-réseau.

Les sous-réseaux s’appuient sur les itinéraires par défaut jusqu’à ce qu’une table d’itinéraires soit associée au sous-réseau. Une fois que l’association existe, le routage se base sur la [correspondance de préfixe la plus longue](https://en.wikipedia.org/wiki/Longest_prefix_match) parmi les itinéraires définis par l’utilisateur et les itinéraires par défaut. S’il existe plusieurs itinéraires avec la même correspondance de préfixe la plus longue, un itinéraire est sélectionné en fonction de son origine dans l’ordre suivant :

1. Itinéraire défini par l’utilisateur
2. Itinéraire BGP (lorsque ExpressRoute est utilisé)
3. Itinéraire par défaut

> [!NOTE]
> Les itinéraires définis par l’utilisateur sont appliqués uniquement aux machines virtuelles et services cloud Azure. Par exemple, si vous souhaitez ajouter une appliance virtuelle de pare-feu entre votre réseau local et Azure, vous devez créer un itinéraire défini par l’utilisateur pour vos tables d’itinéraires Azure qui transmettent tout le trafic allant vers l’espace d’adressage local à l’appliance virtuelle. Toutefois, le trafic entrant à partir de l’espace d’adressage local transite de votre passerelle VPN ou circuit ExpressRoute directement vers l’environnement Azure, en ignorant l’appliance virtuelle.
> 
> 

### Transfert IP
Comme décrit ci-dessus, l’une des raisons principales pour créer un itinéraire défini par l’utilisateur est de transférer le trafic vers une appliance virtuelle. Une appliance virtuelle n’est rien de plus qu’une machine virtuelle qui exécute une application permettant de gérer le trafic réseau d’une certaine façon, comme un pare-feu ou un périphérique NAT.

La machine virtuelle d’appliance virtuelle doit être capable de recevoir le trafic entrant qui ne lui est pas adressé. Pour permettre à une machine virtuelle de recevoir le trafic adressé à d’autres destinations, vous devez activer le transfert IP dans la machine virtuelle.

## Étapes suivantes
* Pour comprendre comment définir les principaux de sécurité avec l’accès approprié pour travailler avec des ressources de votre organisation, consultez la rubrique [Authentification d’un principal du service à l’aide d’Azure Resource Manager](resource-group-authenticate-service-principal.md)
* Si vous devez verrouiller l’accès à une ressource, vous pouvez utiliser des verrous de gestion. Consultez [Verrouiller des ressources avec Azure Resource Manager](resource-group-lock-resources.md)
* Pour configurer le routage et le transfert IP, consultez [Création d’itinéraires définis par l’utilisateur (UDR) dans Resource Manager à l’aide d’un modèle](virtual-network/virtual-network-create-udr-arm-template.md)
* Pour obtenir une présentation du contrôle d’accès en fonction du rôle, consultez [Contrôle d’accès en fonction du rôle dans le portail Microsoft Azure](active-directory/role-based-access-control-configure.md).

<!---HONumber=AcomDC_0803_2016-->