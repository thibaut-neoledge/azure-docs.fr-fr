<properties
   pageTitle="Vue d’ensemble du fournisseur de ressources réseau | Microsoft Azure"
   description="En savoir plus sur le nouveau fournisseur de ressources réseau dans Azure Resource Manager"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="telmos" />

# Fournisseur de ressources réseau
Un besoin sous-jacent dans la réussite des entreprises aujourd'hui est la possibilité de créer et de gérer des applications prenant en charge des réseaux à grande échelle d'une manière flexible, souple, sécurisée et reproductible. Le gestionnaire des ressources Azure (ARM, Azure Resource Manager) vous permet de créer de telles applications, sous la forme d'une collection unique de ressources dans des groupes de ressources. Ces ressources sont gérées via divers fournisseurs de ressources sous ARM.

Azure Resource Manager s’appuie sur différents fournisseurs de ressources pour fournir l’accès à vos ressources. Il existe trois fournisseurs de ressources principaux : réseau, stockage et calcul. Ce document présente les caractéristiques et les avantages du fournisseur de ressources réseau, à savoir :

- **Métadonnées** : vous pouvez ajouter des informations aux ressources à l'aide de balises. Ces balises peuvent être utilisées pour suivre l'utilisation des ressources entre les groupes de ressources et les abonnements.
- **Contrôle accru de votre réseau** : les ressources réseau sont faiblement couplées et vous pouvez les contrôler de manière plus précise. Cela signifie que vous disposez de davantage de flexibilité dans la gestion des ressources réseau.
- **Configuration plus rapide** : étant donné que les ressources réseau sont faiblement couplées, vous pouvez créer et organiser les ressources réseau en parallèle. Cela a considérablement réduit le temps de configuration.
- **Contrôle d'accès en fonction du rôle (RBAC) ** : RBAC fournit des rôles par défaut, avec une étendue de sécurité spécifique, en plus de permettre la création de rôles personnalisés pour une gestion sécurisée.
- **Gestion et déploiement facilités** : il est plus facile de déployer et de gérer des applications dans la mesure où vous pouvez créer toute une pile d'applications sous la forme d'une collection unique de ressources dans un groupe de ressources. Le déploiement est également plus rapide, car vous pouvez l'effectuer en fournissant simplement une charge utile JSON de modèle.
- **Personnalisation rapide** : vous pouvez utiliser des modèles de style déclaratif pour activer la personnalisation rapide et reproductible des déploiements.
- **Personnalisation reproductible** : vous pouvez utiliser des modèles de style déclaratif pour activer la personnalisation rapide et reproductible des déploiements.
- **Interfaces de gestion** : vous pouvez utiliser une des interfaces suivantes pour gérer vos ressources :
	- API REST
	- PowerShell
	- Kit de développement logiciel (SDK) .NET
	- Kit de développement logiciel (SDK) Node.JS
	- Kit de développement logiciel (SDK) Java
	- Interface de ligne de commande Azure
	- Portail en version préliminaire
	- Langue du modèle ARM

## Ressources réseau
Vous pouvez désormais gérer les ressources réseau indépendamment, au lieu qu'elles soient toutes gérées via une ressource de calcul unique (un machine virtuelle). Cela garantit un degré de flexibilité et de souplesse plus élevé dans la composition d'une infrastructure complexe et à grande échelle dans un groupe de ressources.

Une vue conceptuelle d'un exemple de déploiement impliquant une application multicouche est présentée ci-dessous. Chaque ressource que vous voyez, par exemple, les cartes réseau, les adresses IP publiques et les machines virtuelles, peut être gérée indépendamment.

![Modèle de ressource réseau](./media/resource-groups-networking/Figure2.png)

Chaque ressource contient un ensemble commun de propriétés et son jeu de propriétés individuelles. Les propriétés communes sont les suivantes :

|Propriété|Description|Exemples de valeurs|
|---|---|---|
|**name**|Nom de ressource unique. Chaque type de ressource a ses propres restrictions d’affectation de noms.|PIP01, VM01, NIC01|
|**location**|Région Azure dans laquelle se trouve la ressource|westus, eastus|
|**id**|Identification en fonction d’un URI unique|/subscriptions/<subGUID>/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP|

Vous pouvez vérifier les propriétés individuelles des ressources dans les sections ci-dessous.

[AZURE.INCLUDE [virtual-networks-nrp-pip-include](../../includes/virtual-networks-nrp-pip-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-nic-include](../../includes/virtual-networks-nrp-nic-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-nsg-include](../../includes/virtual-networks-nrp-nsg-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-udr-include](../../includes/virtual-networks-nrp-udr-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-vnet-include](../../includes/virtual-networks-nrp-vnet-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-dns-include](../../includes/virtual-networks-nrp-dns-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-lb-include](../../includes/virtual-networks-nrp-lb-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-appgw-include](../../includes/virtual-networks-nrp-appgw-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-vpn-include](../../includes/virtual-networks-nrp-vpn-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-tm-include](../../includes/virtual-networks-nrp-tm-include.md)]

## Interfaces de gestion
Vous pouvez gérer vos ressources de réseau Azure à l’aide de différentes interfaces. Dans ce document nous allons nous concentrer sur le dépannage de ces interfaces : API REST et modèles.

### API REST
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

### Langue du modèle ARM
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

### Utilisation d’un modèle

Vous pouvez déployer des services dans Azure à partir d’un modèle à l’aide de PowerShell, AzureCLI, ou en cliquant pour les déployer à partir de GitHub. Pour déployer des services à partir d’un modèle dans GitHub, procédez comme suit :

1. Ouvrez le fichier template3 dans GitHub. Par exemple, ouvrez [Réseau virtuel avec deux sous-réseaux](https://github.com/Azure/azure-quickstart-templates/tree/master/101-virtual-network).
2. Cliquez sur **Déployer dans Azure**, puis connectez-vous au portail Azure avec vos informations d’identification.
3. Vérifiez le modèle, puis cliquez sur **Enregistrer**.
4. Cliquez sur **Modifier les paramètres** et sélectionnez un emplacement, par exemple *ouest des États-Unis*, pour le réseau virtuel et les sous-réseaux.
5. Au besoin, modifiez les paramètres **ADDRESSPREFIX** et **SUBNETPREFIX**, puis cliquez sur **OK**.
6. Cliquez sur **Sélectionner un groupe de ressources**, puis cliquez sur le groupe de ressources auquel vous souhaitez ajouter le réseau virtuel et les sous-réseaux. Vous pouvez également créer un nouveau groupe de ressources en cliquant sur **Ou créer un nouveau**.
3. Cliquez sur **Créer**. Notez la vignette qui affiche **Approvisionnement du déploiement du modèle**. Une fois le déploiement effectué, vous verrez un écran semblable à celle ci-dessous.

![Exemple de déploiement de modèle](./media/resource-groups-networking/Figure6.png)


## Étapes suivantes

[Langue du modèle Azure Resource Manager](../resource-group-authoring-templates.md)

[Mise en réseau Azure : modèles couramment utilisés](https://github.com/Azure/azure-quickstart-templates)

[Fournisseur de ressources de calcul](../virtual-machines/virtual-machines-windows-compare-deployment-models.md)

[Présentation d’Azure Resource Manager](../resource-group-overview.md)

<!---HONumber=AcomDC_0413_2016-->