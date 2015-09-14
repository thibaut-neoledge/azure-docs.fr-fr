<properties 
   pageTitle="Fournisseur de ressources réseau"
	description="Fournisseur de ressources réseau"
	services="virtual-network"
	documentationCenter="na"
	authors="telmosampaio"
	manager="adinah"
	editor="tysonn"/>
<tags 
   ms.service="virtual-network"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="08/23/2015"
	ms.author="telmos"/>

# Fournisseur de ressources réseau
Un besoin sous-jacent dans la réussite des entreprises aujourd'hui est la possibilité de créer et de gérer des applications prenant en charge des réseaux à grande échelle d'une manière flexible, souple, sécurisée et reproductible. Le gestionnaire des ressources Azure (ARM, Azure Resource Manager) vous permet de créer de telles applications, sous la forme d'une collection unique de ressources dans des groupes de ressources. Ces ressources sont gérées via divers fournisseurs de ressources sous ARM.

Le gestionnaire des ressources Azure (ARM, Azure Resource Manager) vous permet de créer de telles applications et la collection de ressources réseau associé sous la forme d'une collection unique de ressources dans des groupes de ressources. L'application et les ressources réseau s'exécutent comme une seule unité dans un groupe de ressources ARM.

Vous pouvez gérer les ressources réseau à l'aide de l'une des interfaces de gestion suivantes :

- API REST
- PowerShell
- Kit de développement logiciel (SDK) .NET
- Kit de développement logiciel (SDK) Node.JS
- Kit de développement logiciel (SDK) Java
- Interface de ligne de commande Azure
- Portail Azure
- Langue du modèle ARM

Avec l'introduction de fournisseurs de ressources réseau, vous pouvez tirer parti des avantages suivants :

- **Métadonnées** : vous pouvez ajouter des informations aux ressources à l'aide de balises. Ces balises peuvent être utilisées pour suivre l'utilisation des ressources entre les groupes de ressources et les abonnements.
- **Contrôle accru de votre réseau** : les ressources réseau sont faiblement couplées et vous pouvez les contrôler de manière plus précise. Cela signifie que vous disposez de davantage de flexibilité dans la gestion des ressources réseau.
- **Configuration plus rapide** : étant donné que les ressources réseau sont faiblement couplées, vous pouvez créer et organiser les ressources réseau en parallèle. Cela a considérablement réduit le temps de configuration.
- **Contrôle d'accès en fonction du rôle (RBAC) ** : RBAC fournit des rôles par défaut, avec une étendue de sécurité spécifique, en plus de permettre la création de rôles personnalisés pour une gestion sécurisée. 
- **Gestion et déploiement facilités** : il est plus facile de déployer et de gérer des applications dans la mesure où vous pouvez créer toute une pile d'applications sous la forme d'une collection unique de ressources dans un groupe de ressources. Le déploiement est également plus rapide, car vous pouvez l'effectuer en fournissant simplement une charge utile JSON de modèle.
- **Personnalisation rapide** : vous pouvez utiliser des modèles de style déclaratif pour activer la personnalisation rapide et reproductible des déploiements. 
- **Personnalisation reproductible** : vous pouvez utiliser des modèles de style déclaratif pour activer la personnalisation rapide et reproductible des déploiements.

## Ressources réseau 
Vous pouvez désormais gérer les ressources réseau indépendamment, au lieu qu'elles soient toutes gérées via une ressource de calcul unique (un machine virtuelle). Cela garantit un degré de flexibilité et de souplesse plus élevé dans la composition d'une infrastructure complexe et à grande échelle dans un groupe de ressources.
 
Le diagramme ci-dessous illustre une vue générale du modèle de ressource réseau et de ses associations. Les ressources de niveau supérieur sont signalées par un contour bleu. En plus des ressources de niveau supérieur, vous pouvez voir les ressources enfants signalées par un contour gris. Vous pouvez gérer chaque ressource individuellement.

![Modèle de ressource réseau](./media/resource-groups-networking/Figure1.png)

Une vue conceptuelle d'un exemple de déploiement impliquant une application multicouche est présentée ci-dessous. Toutes les ressources réseau sont signalées par un contour bleu.

![Modèle de ressource réseau](./media/resource-groups-networking/Figure2.png)

## API REST 
Comme mentionné précédemment, les ressources réseau peuvent être gérées via une variété d'interfaces, notamment l'API REST, le Kit de développement logiciel (SDK) .NET, le Kit de développement logiciel (SDK) Node.JS, le Kit de développement logiciel (SDK) Java, PowerShell, l'interface en ligne de commande, le portail Azure et des modèles.

Les API Rest sont conformes à la spécification du protocole HTTP 1.1. La structure d'URI générale de l'API est présentée ci-dessous :

	https://management.azure.com/subscriptions/{subscription-id}/providers/{resource-provider-namespace}/locations/{region-location}/register?api-version={api-version}

Et les paramètres entre accolades représentent les éléments suivants :

- **subscription-id** : ID de votre abonnement Azure.
- **resource-provider-namespace** : espace de noms pour le fournisseur utilisé. La valeur pour le fournisseur de ressources réseau est *Microsoft.Network*.
- **region-name** : nom de la région Azure.

Les méthodes HTTP suivantes sont prises en charge lors des appels à l'API REST :

- **PUT** : utilisée pour créer une ressource d'un type donné, modifier une propriété de ressource ou changer une association entre des ressources. 
- **GET** : utilisée pour récupérer des informations concernant une ressource configurée.
- **DELETE** : utilisée pour supprimer une ressource existante.

La demande et la réponse sont toutes les deux conformes à un format de charge utile JSON. Pour plus d'informations, consultez [API de gestion des ressources Azure](https://msdn.microsoft.com/library/azure/dn948464.aspx).

## Langue du modèle ARM
Outre la gestion des ressources de manière impérative (via des API ou un Kit de développement logiciel (SDK)), vous pouvez également utiliser un style de programmation déclaratif pour créer et gérer les ressources réseau en utilisant la langue du modèle ARM.

Un exemple de représentation d'un modèle est fourni ci-dessous :

	{
	  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
	  "contentVersion": "<version-number-of-template>",
	  "parameters": { <parameter-definitions-of-template> },
	  "variables": { <variable-definitions-of-template> },
	  "resources": [ { <definition-of-resource-to-deploy> } ],
	  "outputs": { <output-of-template> }    
	}

Le modèle est essentiellement une description JSON des ressources et des valeurs d'instance injectées via des paramètres. L'exemple ci-dessous peut être utilisé pour créer un réseau virtuel comprenant deux sous-réseaux.

	{
	    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VNET.json",
	    "contentVersion": "1.0.0.0",
	    "parameters" : {
	      "location": {
	        "type": "String",
	        "allowedValues": ["East US", "West US", "West Europe", "East Asia", "South East Asia"],
	        "metadata" : {
	          "Description" : "Deployment location"
	        }
	      },
	      "virtualNetworkName":{
	        "type" : "string",
	        "defaultValue":"myVNET",
	        "metadata" : {
	          "Description" : "VNET name"
	        }
	      },
	      "addressPrefix":{
	        "type" : "string",
	        "defaultValue" : "10.0.0.0/16",
	        "metadata" : {
	          "Description" : "Address prefix"
	        }
	
	      },
	      "subnet1Name": {
	        "type" : "string",
	        "defaultValue" : "Subnet-1",
	        "metadata" : {
	          "Description" : "Subnet 1 Name"
	        }
	      },
	      "subnet2Name": {
	        "type" : "string",
	        "defaultValue" : "Subnet-2",
	        "metadata" : {
	          "Description" : "Subnet 2 name"
	        }
	      },
	      "subnet1Prefix" : {
	        "type" : "string",
	        "defaultValue" : "10.0.0.0/24",
	        "metadata" : {
	          "Description" : "Subnet 1 Prefix"
	        }
	      },
	      "subnet2Prefix" : {
	        "type" : "string",
	        "defaultValue" : "10.0.1.0/24",
	        "metadata" : {
	          "Description" : "Subnet 2 Prefix"
	        }
	      }
	    },
	    "resources": [
	    {
	      "apiVersion": "2015-05-01-preview",
	      "type": "Microsoft.Network/virtualNetworks",
	      "name": "[parameters('virtualNetworkName')]",
	      "location": "[parameters('location')]",
	      "properties": {
	        "addressSpace": {
	          "addressPrefixes": [
	            "[parameters('addressPrefix')]"
	          ]
	        },
	        "subnets": [
	          {
	            "name": "[parameters('subnet1Name')]",
	            "properties" : {
	              "addressPrefix": "[parameters('subnet1Prefix')]"
	            }
	          },
	          {
	            "name": "[parameters('subnet2Name')]",
	            "properties" : {
	              "addressPrefix": "[parameters('subnet2Prefix')]"
	            }
	          }
	        ]
	      }
	    }
	    ]
	}

Lorsqu'un modèle est utilisé, vous avez la possibilité de fournir manuellement les valeurs de paramètre, ou vous pouvez utiliser un fichier de paramètres. L'exemple ci-dessous montre un ensemble possible de valeurs de paramètre à utiliser avec le modèle ci-dessus :

	{
	  "location": {
	      "value": "East US"
	  },
	  "virtualNetworkName": {
	      "value": "VNET1"
	  },
	  "subnet1Name": {
	      "value": "Subnet1"
	  },
	  "subnet2Name": {
	      "value": "Subnet2"
	  },
	  "addressPrefix": {
	      "value": "192.168.0.0/16"
	  },
	  "subnet1Prefix": {
	      "value": "192.168.1.0/24"
	  },
	  "subnet2Prefix": {
	      "value": "192.168.2.0/24"
	  }
	}


Les principaux avantages de l'utilisation de modèles sont les suivants :

- Vous pouvez créer dans un style déclaratif une infrastructure complexe dans un groupe de ressources. L'orchestration de la création des ressources, notamment la gestion des dépendances, est gérée par le gestionnaire des ressources Azure (ARM). 
- L'infrastructure peut être créée de manière reproductible entre diverses régions et dans une région en modifiant simplement les paramètres. 
- Le style déclaratif entraîne un délai de création des modèles et de déploiement de l'infrastructure plus court. 

Pour obtenir des exemples de modèles, consultez [Modèles de démarrage rapide Azure](https://github.com/Azure/azure-quickstart-templates).

Pour plus d'informations sur la langue du modèle ARM, consultez [Langue de modèle de gestionnaire des ressources Azure](../resource-group-authoring-templates.md).

L'exemple de modèle ci-dessus utilise le réseau virtuel et des ressources de sous-réseau. Il existe d'autres ressources réseau que vous pouvez utiliser, comme indiqué ci-dessous :

## Carte d'interface réseau
La carte d'interface réseau, ou NIC, représente une interface réseau qui peut être associée à une machine virtuelle. Une machine virtuelle peut comporter une ou plusieurs cartes d'interface réseau.

![Cartes d'interface réseau sur une seule machine virtuelle](./media/resource-groups-networking/Figure3.png)

Les propriétés clés d'une ressource de carte d'interface réseau sont les suivantes :

- Paramètres d'adresse IP
- Nom DNS interne
- Serveurs DNS

Une carte d'interface réseau peut également être associée aux ressources réseau suivantes :

- Groupe de sécurité réseau 
- Équilibrage de charge

## Réseau virtuel et sous-réseau
Les réseaux virtuels et sous-réseaux permettent de définir une limite de sécurité pour les charges de travail s'exécutant dans Azure. Un réseau virtuel est caractérisée par un espace d'adressage, également appelé « bloc CIDR ».

Un sous-réseau est une ressource enfant d'un réseau virtuel, et permet de définir des segments d'espaces d'adressage dans un bloc CIDR, à l'aide de préfixes d'adresses IP. Les machines virtuelles qui exécutent différentes charges de travail fonctionnent essentiellement dans les limites d'un sous-réseau.

![Cartes d'interface réseau sur une seule machine virtuelle](./media/resource-groups-networking/Figure4.png)

Les propriétés clés d'une ressource de réseau virtuel sont les suivantes :

- Espace d'adressage IP (bloc CIDR) 
- Nom du réseau virtuel
- Sous-réseaux
- Serveurs DNS

Un réseau virtuel peut également être associé aux ressources réseau suivantes :

- Passerelle VPN

Les propriétés clés d'un sous-réseau sont les suivantes :

- Préfixe d'adresse IP
- Nom du sous-réseau

Un sous-réseau peut également être associé aux ressources réseau suivantes :

- Groupe de sécurité réseau

## Équilibrage de charge
Un équilibrage de charge est utilisé lorsque vous voulez étendre vos applications. Les scénarios de déploiement classiques impliquent des applications s'exécutant sur plusieurs instances de machine virtuelle. Les instances de machine virtuelle sont pilotées par un équilibrage de charge qui permet de répartir le trafic réseau entre les différentes instances.

![Cartes d’interface réseau sur une seule machine virtuelle](./media/resource-groups-networking/Figure5.png)

Les équilibrages de charge contiennent les ressources enfants suivantes :

- **Configuration d'adresses IP frontales** : un équilibrage de charge peut inclure une ou plusieurs adresses IP frontales, également appelées « adresses IP virtuelles ». Ces adresses IP servent d'entrée pour le trafic. 
- **Pool d'adresses principal** : il s'agit des adresses IP associées aux cartes réseau des machines virtuelles vers lesquelles la charge sera distribuée.
- **Règles d'équilibrage de charge** : une propriété de règle mappe une combinaison d'adresse IP et de port frontaux donnée à un ensemble de combinaisons d'adresse IP et de port principaux. Avec une seule définition d'une ressource d'équilibrage de charge, vous pouvez définir plusieurs règles d'équilibrage de charge, chaque règle reflétant une combinaison d'une adresse IP et d'un port frontaux d'une part, et d'une adresse IP et d'un port principaux d'autre part, associés à des machines virtuelles. 
- **Sondes** : les sondes vous permettent d'effectuer le suivi de l'intégrité des instances de machine virtuelle. En cas d'échec d'une sonde d'intégrité, l'instance de machine virtuelle est automatiquement mise hors service.
- **Règles NAT de trafic entrant** : règles NAT définissant le trafic entrant transitant via l'adresse IP frontale et distribué à l'adresse IP principale.

## Application Gateway

Application Gateway fournit une solution d'équilibrage de la charge HTTP gérée par Azure et basée sur l'équilibrage de la charge de couche 7. L'équilibrage de la charge de l'application permet l'utilisation de règles de routage pour le trafic réseau basé sur HTTP.

Les passerelles Application Gateway contiennent les ressources enfant suivantes :

- **Pool de serveurs principaux** : liste des adresses IP des serveurs principaux. Les adresses IP répertoriées doivent appartenir au sous-réseau de réseau virtuel ou elles doivent être une adresse IP/VIP publique. 
- **Paramètres du pool de serveurs principaux :** chaque pool comporte des paramètres comme le port, le protocole et une affinité basée sur les cookies. Ces paramètres sont liés à un pool et sont appliqués à tous les serveurs du pool.
- **Port frontal** : il s'agit du port public ouvert sur la passerelle Application Gateway. Le trafic atteint ce port, puis il est redirigé vers l’un des serveurs principaux.
- **Écouteur** : l'écouteur a un port frontal, un protocole (Http ou Https, avec respect de la casse) et le nom du certificat SSL (en cas de configuration du déchargement SSL). 
- **Règle** : la règle lie l'écouteur et le pool de serveurs principaux et définit vers quel pool de serveurs principaux le trafic doit être dirigé quand il atteint un écouteur spécifique. Actuellement, seule la règle de base est prise en charge. La règle de base est la distribution de charge par tourniquet (round robin).


## Adresse IP publique
Une ressource d'adresse IP publique fournit une adresse IP publique réservée ou une adresse IP publique dynamique. Une adresse IP publique peut être affectée à un équilibrage de charge, à une adresse IP NAT ou être associée à une adresse IP privée sur une carte d'interface réseau d'une machine virtuelle.

Les propriétés clés d'une ressource d'adresse IP sont les suivantes :

- **Méthode d'allocation d'adresse IP** : réservée ou dynamique. 

## Groupe de sécurité réseau
Une ressource de groupe de sécurité réseau permet de créer des limites de sécurité pour les charges de travail, en implémentant des règles d'autorisation et de refus. Ces règles peuvent être appliquées au niveau de la carte d'interface réseau (niveau des instances de machine virtuelle) ou au niveau du sous-réseau (groupe de machines virtuelles).

Les propriétés clés d'une ressource de groupe de sécurité réseau sont les suivantes :

- **Règle de sécurité** : plusieurs règles de sécurité peuvent être définies pour un groupe de sécurité réseau. Chaque règle peut autoriser ou refuser différents types de trafic.

## Règle de sécurité
Une règle de sécurité est une ressource enfant d'un groupe de sécurité réseau.

Les propriétés clés d'une règle de sécurité sont les suivantes :

- **Protocole** : protocole réseau auquel cette règle s'applique.
- **Plage du port source** : port source, ou plage comprise entre 0 et 65535. Un caractère générique peut être utilisé pour faire correspondre tous les ports. 
- **Plage du port de destination** : port de destination, ou plage comprise entre 0 et 65535. Un caractère générique peut être utilisé pour faire correspondre tous les ports.
- **Préfixe d'adresse source** : plage d'adresses IP sources. 
- **Préfixe d'adresse de destination** : plage d'adresses IP de destination.
- **Accès** : *Autoriser* ou *Refuser* le trafic.
- **Priorité** : valeur comprise entre 100 et 4096. Le numéro de priorité doit être unique pour chaque règle de la collection de règles de sécurité. Plus le numéro de priorité est faible, plus la priorité de la règle est élevée.
- **Sens** : spécifie si la règle sera appliquée au trafic dans le sens *entrant* ou *sortant*. 

## Passerelle VPN 
Une ressource de passerelle VPN vous permet de créer une connexion sécurisée entre le centre de données local et Azure. Une ressource de passerelle VPN peut être configurée de trois manières différentes :
 
- **De pointe à site** : vous pouvez accéder en toute sécurité vos ressources Azure hébergées sur un réseau virtuel à l'aide d'un client VPN à partir de n'importe quel ordinateur. 
- **Connexion multisite** : vous pouvez vous connecter en toute sécurité à partir de vos centres de données locaux à des ressources en cours d'exécution sur un réseau virtuel. 
- **De réseau virtuel à réseau virtuel** – vous pouvez vous connecter en toute sécurité entre des réseaux virtuels Azure de la même région, ou entre régions pour générer des charges de travail à redondance géographique.

Les propriétés clés d'une passerelle VPN sont les suivantes :
 
- **Type de passerelle** : passerelle acheminée de manière dynamique ou de manière statique. 
- **Préfixe du pool d'adresses du client VPN** : adresses IP à affecter aux clients qui se connectent dans une configuration de point à site.



## Profil Traffic Manager
Traffic Manager et ses ressources de point de terminaison enfant activent la répartition de votre trafic vers les points de terminaison dans Azure et en dehors d'Azure. Cette répartition du trafic est régie par des stratégies. Traffic Manager permet également de surveiller l'intégrité des points de terminaison, ainsi que le trafic dévié de manière appropriée en fonction de l'intégrité d'un point de terminaison.

Les propriétés clés d'un profil Traffic Manager sont les suivantes :

- **Méthode de routage du trafic** : les valeurs possibles sont *Performances*, *Pondéré* et *Priorité*.
- **Configuration DNS** : nom de domaine complet du profil.
- **Protocole** : protocole de surveillance. Les valeurs possibles sont *HTTP* et *HTTPS*.
- **Port** : port de surveillance. 
- **Chemin d'accès** : chemin d'accès de surveillance.
- **Points de terminaison** : conteneur pour les ressources des points de terminaison.

## Point de terminaison 
Un point de terminaison est une ressource enfant d'un profil Traffic Manager. Il représente un service ou un point de terminaison web vers lequel le trafic est réparti en fonction de la stratégie configurée dans la ressource de profil Traffic Manager.

Les propriétés clé d'un point de terminaison sont les suivantes :
 
- **Type** : type du point de terminaison. Les valeurs possibles sont *Point de terminaison Azure*, *oint de terminaison externe* et *Point de terminaison imbriqué*. 
- **ID de ressource cible** : adresse IP publique d'un service ou d'un point de terminaison web. Ce peut être un point de terminaison Azure ou un point de terminaison externe.
- **Poids** : poids du point de terminaison utilisé dans la gestion du trafic. 
- **Priorité** : priorité du point de terminaison, utilisée pour définir une action de basculement. 

## DNS Azure

Azure DNS est un service d'hébergement pour les domaines DNS et qui offre une résolution de noms à l'aide de l'infrastructure Microsoft Azure.

Les propriétés clés d'un DNS Azure sont les suivantes :

- **Zones DNS** - Informations relatives à la zone du domaine pour l'hébergement des enregistrements DNS d'un domaine particulier.
- **Jeux d'enregistrements DNS** - Ensemble d'enregistrements d'un type particulier. Les types pris en charge sont A, AAAA, CNAME, MX, NS, SOA,SRV et TXT.


## Utilisation d’un modèle

Vous pouvez déployer des services dans Azure à partir d’un modèle à l’aide de PowerShell, AzureCLI, ou en cliquant pour les déployer à partir de GitHub. Pour déployer des services à partir d’un modèle dans GitHub, procédez comme suit :

1. Ouvrez le fichier template3 dans GitHub. Par exemple, ouvrez [Réseau virtuel avec deux sous-réseaux](https://github.com/Azure/azure-quickstart-templates/tree/master/101-virtual-network).
2. Cliquez sur **Déployer dans Azure**, puis connectez-vous au portail Azure avec vos informations d’identification.
3. Vérifiez le modèle, puis cliquez sur **Enregistrer**.
4. Cliquez sur **Modifier les paramètres** et sélectionnez un emplacement, par exemple *ouest des États-Unis*, pour le réseau virtuel et les sous-réseaux.
5. Au besoin, modifiez les paramètres **ADDRESSPREFIX** et **SUBNETPREFIX**, puis cliquez sur **OK**.
6. Cliquez sur **Sélectionner un groupe de ressources**, puis cliquez sur le groupe de ressources auquel vous souhaitez ajouter le réseau virtuel et les sous-réseaux. Vous pouvez également créer un nouveau groupe de ressources en cliquant sur **Ou créer un nouveau**.
3. Cliquez sur **Créer**. Notez la vignette qui affiche **Approvisionnement du déploiement du modèle**. Une fois le déploiement effectué, vous verrez un écran semblable à celle ci-dessous.

![Exemple de déploiement de modèle](./media/resource-groups-networking/Figure6.png)


## Voir aussi

[Référence des API de réseau Azure](https://msdn.microsoft.com/library/azure/dn948464.aspx)

[Référence d'Azure PowerShell pour la mise en réseau](https://msdn.microsoft.com/library/azure/mt163510.aspx)

[Langue du modèle Azure Resource Manager](../resource-group-authoring-templates.md)

[Mise en réseau Azure : modèles couramment utilisés](https://github.com/Azure/azure-quickstart-templates)

[Fournisseur de ressources de calcul](../virtual-machines-azurerm-versus-azuresm)

[Présentation du gestionnaire des ressources Azure](../resource-group-overview)

[Contrôle d'accès basé sur des rôles dans Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn906885.aspx)

[Utilisation de balises dans le gestionnaire des ressources Azure](https://msdn.microsoft.com/library/azure/dn848368.aspx)

[Déploiements de modèles](https://msdn.microsoft.com/library/azure/dn790549.aspx)

<!---HONumber=September15_HO1-->