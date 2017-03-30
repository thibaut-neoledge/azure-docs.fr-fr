---
title: "Créer une passerelle Azure Application Gateway - modèles | Microsoft Docs"
description: "Cette page fournit des instructions pour la création d’une passerelle Azure Application Gateway à l’aide du modèle Azure Resource Manager"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 8192ee25-d9f0-4b32-a45e-1d74629c54e5
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: f550ec9a8d254378d165f0c842459fd50ade7945
ms.openlocfilehash: 5cdd26529ed06b7903ae232ab8da4527e79a8a68
ms.lasthandoff: 03/30/2017


---
# <a name="create-an-application-gateway-by-using-the-azure-resource-manager-template"></a>Création d’une passerelle d’application à l’aide du modèle Azure Resource Manager

> [!div class="op_single_selector"]
> * [portail Azure](application-gateway-create-gateway-portal.md)
> * [Commandes PowerShell pour Azure Resource Manager](application-gateway-create-gateway-arm.md)
> * [Azure Classic PowerShell](application-gateway-create-gateway.md)
> * [Modèle Azure Resource Manager](application-gateway-create-gateway-arm-template.md)
> * [Interface de ligne de commande Azure](application-gateway-create-gateway-cli.md)

La passerelle Azure Application Gateway est un équilibreur de charge de couche 7. Elle assure l’exécution des requêtes HTTP de basculement et de routage des performances entre serveurs locaux ou dans le cloud. Application Gateway offre de nombreuses fonctionnalités de contrôleur de livraison d’applications (ADC) : équilibrage de charge HTTP, affinité de session basée sur les cookies, déchargement SSL (Secure Sockets Layer), sondes d’intégrité personnalisées, prise en charge de plusieurs sites, etc. Pour obtenir une liste complète des fonctionnalités prises en charge, consultez [Vue d’ensemble d’Application Gateway](application-gateway-introduction.md)

Vous découvrirez comment télécharger et modifier un modèle Azure Resource Manager existant à partir de GitHub, et apprendrez à déployer le modèle à partir de GitHub, de PowerShell et de l’interface de ligne de commande Azure.

Si vous déployez simplement le modèle Azure Resource Manager directement à partir de GitHub sans rien modifier, passez à la section Déployer un modèle à partir de GitHub.

## <a name="scenario"></a>Scénario

Dans ce scénario, vous allez :

* créer une passerelle Application Gateway avec deux instances ;
* créer un réseau virtuel nommé VirtualNetwork1 avec un bloc CIDR réservé de 10.0.0.0/16 ;
* créer un sous-réseau appelé Appgatewaysubnet qui utilise 10.0.0.0/28 comme bloc CIDR ;
* installer deux adresses IP terminales précédemment configurées pour les serveurs web pour lesquels vous souhaitez équilibrer la charge du trafic. Dans cet exemple de modèle, nous allons utiliser les adresses IP terminales 10.0.1.10 et 10.0.1.11.

> [!NOTE]
> Ce sont les paramètres de ce modèle. Pour personnaliser le modèle, vous pouvez modifier les règles, l’écouteur et le protocole SSL en ouvrant le fichier azuredeploy.json.

![Scénario](./media/application-gateway-create-gateway-arm-template/scenario.png)

## <a name="download-and-understand-the-azure-resource-manager-template"></a>Téléchargement et découverte du modèle Azure Resource Manager

Vous pouvez télécharger le modèle Azure Resource Manager existant pour créer un réseau virtuel et deux sous-réseaux sur GitHub, apporter les modifications souhaitées, puis le réutiliser. Pour ce faire, procédez comme suit :

1. Accédez à [Créer une passerelle Application Gateway](https://github.com/Azure/azure-quickstart-templates/tree/master/101-application-gateway-create).
2. Cliquez sur **azuredeploy.json**, puis sur **RAW**.
3. Enregistrez le fichier dans un dossier local sur votre ordinateur.
4. Si vous connaissez déjà les modèles Azure Resource Manager, passez à l’étape 7.
5. Ouvrez le fichier que vous avez enregistré et consultez le contenu sous **parameters** à la ligne 5. Les paramètres du modèle Azure Resource Manager fournissent un espace réservé pour les valeurs à remplir lors du déploiement.
   
   | Paramètre | Description |
   | --- | --- |
   | **location** |Région Azure où est créée la passerelle Application Gateway |
   | **VirtualNetwork1** |Nom du nouveau réseau virtuel |
   | **addressPrefix** |Espace d’adressage du réseau virtuel, au format CIDR |
   | **ApplicationGatewaysubnet** |Nom du sous-réseau de la passerelle Application Gateway |
   | **subnetPrefix** |Bloc CIDR du sous-réseau de la passerelle Application Gateway |
   | **skuname** |Taille de l’instance SKU |
   | **capacité** |Nombre d’instances |
   | **backendaddress1** |Adresse IP du premier serveur web |
   | **backendaddress2** |Adresse IP du deuxième serveur web |

    > [!IMPORTANT]
    >Les modèles Azure Resource Manager de GitHub sont susceptibles d’évoluer. Vérifiez le modèle avant de l’utiliser.

6. Vérifiez le contenu sous **ressources** et notez les propriétés suivantes :

   * **type**. Type de ressource créée par le modèle. Dans ce cas, le type qui représente une passerelle d’application est `Microsoft.Network/applicationGateways`.
   * **name**. Nom de la ressource. Remarquez l’utilisation de `[parameters('applicationGatewayName')]`, qui signifie que le nom est fourni par vous ou un fichier de paramètres lors du déploiement.
   * **properties**. Liste des propriétés de la ressource. Ce modèle utilise le réseau virtuel et une adresse IP publique lors de la création de la passerelle Application Gateway.

7. Revenez à [https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-create/](https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-create).
8. Cliquez sur **azuredeploy-paremeters.json**, puis cliquez sur **RAW**.
9. Enregistrez le fichier dans un dossier local sur votre ordinateur.
10. Ouvrez le fichier que vous avez enregistré et modifiez les valeurs des paramètres. Utilisez les valeurs suivantes pour déployer la passerelle Application Gateway décrite dans notre scénario.

    ```json
        {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        {
        "location" : {
        "value" : "West US"
        },
        "addressPrefix": {
        "value": "10.0.0.0/16"
        },
        "subnetPrefix": {
        "value": "10.0.0.0/24"
        },
        "skuName": {
        "value": "Standard_Small"
        },
        "capacity": {
        "value": 2
        },
        "backendIpAddress1": {
        "value": "10.0.1.10"
        },
        "backendIpAddress2": {
        "value": "10.0.1.11"
        }
        }
    ```

11. Enregistrez le fichier . Vous pouvez tester le modèle JSON et le modèle de paramètres à l’aide des outils de validation JSON en ligne comme [JSlint.com](http://www.jslint.com/).

## <a name="deploy-the-azure-resource-manager-template-by-using-powershell"></a>Déploiement du modèle Azure Resource Manager à l’aide de PowerShell

Si vous n’avez jamais utilisé Azure PowerShell, consultez [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs) et suivez les instructions pour vous connecter à Azure et sélectionner votre abonnement.

### <a name="step-1"></a>Étape 1

```powershell
Login-AzureRmAccount
```

### <a name="step-2"></a>Étape 2

Vérifiez les abonnements associés au compte.

```powershell
Get-AzureRmSubscription
```

Vous êtes invité à saisir vos informations d’identification.

### <a name="step-3"></a>Étape 3 :

Parmi vos abonnements Azure, choisissez celui que vous souhaitez utiliser.

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>Étape 4

Au besoin, créez un groupe de ressources à l’aide de l’applet de commande **New-AzureResourceGroup** . Dans l’exemple suivant, vous allez créer un groupe de ressources appelé AppgatewayRG dans les États-Unis de l’Est.

```powershell
New-AzureRmResourceGroup -Name AppgatewayRG -Location "East US"
```

Exécutez l’applet de commande **New-AzureRmResourceGroupDeployment** pour déployer le nouveau réseau virtuel à l’aide du modèle précédent et des fichiers de paramètres que vous avez téléchargés et modifiés.

```powershell
New-AzureRmResourceGroupDeployment -Name TestAppgatewayDeployment -ResourceGroupName AppgatewayRG `
-TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json
```

## <a name="deploy-the-azure-resource-manager-template-by-using-the-azure-cli"></a>Déploiement du modèle Azure Resource Manager à l’aide de l’interface de ligne de commande Azure

Pour déployer le modèle Azure Resource Manager que vous avez téléchargé à l’aide de l’interface de ligne de commande Azure, suivez les étapes ci-dessous :

### <a name="step-1"></a>Étape 1 :

Si vous n’avez jamais utilisé l’interface de ligne de commande Azure, consultez [Installer et configurer l’interface de ligne de commande Azure](../xplat-cli-install.md) et suivez les instructions jusqu’à l’étape où vous sélectionnez votre compte et votre abonnement Azure.

### <a name="step-2"></a>Étape 2 :

Exécutez la commande **azure config mode** pour passer en mode Resource Manager, comme illustré dans l’extrait de code suivant.

```azurecli
azure config mode arm
```

Voici le résultat attendu pour la commande précédente :

```azurecli
info:    New mode is arm
```

### <a name="step-3"></a>Étape 3 :

Au besoin, exécutez la commande **azure group create** pour créer un groupe de ressources, comme illustré dans l’extrait de code suivant. Observez le résultat de la commande. La liste affichée après le résultat présente les différents paramètres utilisés. Pour plus d’informations sur les groupes de ressources, consultez la page [Vue d’ensemble d’Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

```azurecli
azure group create -n appgatewayRG -l eastus
```

**-n (ou --name)**. Nom du nouveau groupe de ressources. Pour notre scénario, il s’agit de *appgatewayRG*.

**-l (ou --location)**. Région Azure où le nouveau groupe de ressources est créé. Pour notre scénario, il s’agit de *eastus*.

### <a name="step-4"></a>Étape 4

Exécutez l’applet de commande **azure group deployment create** pour déployer le nouveau réseau virtuel à l’aide du modèle et des fichiers de paramètres que vous avez téléchargés et modifiés lors de l’étape précédente. La liste affichée après le résultat présente les différents paramètres utilisés.

```azurecli
azure group deployment create -g appgatewayRG -n TestAppgatewayDeployment -f C:\ARM\azuredeploy.json -e C:\ARM\azuredeploy-parameters.json
```

## <a name="deploy-the-azure-resource-manager-template-by-using-click-to-deploy"></a>Déploiement du modèle Azure Resource Manager à l’aide de la fonctionnalité « cliquer pour déployer »

La fonctionnalité « cliquer pour déployer » offre une autre manière d’utiliser les modèles ARM. C’est un moyen facile d’utiliser des modèles avec le portail Azure.

### <a name="step-1"></a>Étape 1

Accédez à [Créer une passerelle Application Gateway avec une adresse IP publique](https://azure.microsoft.com/documentation/templates/101-application-gateway-public-ip/).

### <a name="step-2"></a>Étape 2

Cliquez sur **Déployer dans Azure**.

![Déployer dans Azure](./media/application-gateway-create-gateway-arm-template/deploytoazure.png)

### <a name="step-3"></a>Étape 3

Renseignez les paramètres du modèle de déploiement sur le portail, puis cliquez sur **OK**.

![parameters](./media/application-gateway-create-gateway-arm-template/ibiza1.png)

### <a name="step-4"></a>Étape 4

Sélectionnez **Mentions légales** et cliquez sur **Acheter**.

### <a name="step-5"></a>Étape 5

Dans le panneau Déploiement personnalisé, cliquez sur **Créer**.

## <a name="providing-certificate-data-to-resource-manager-templates"></a>Fournir des données de certificat aux modèles Resource Manager

Si SSL est utilisé avec un modèle, le certificat doit être fourni dans une chaîne base64, et non chargé. Pour convertir un .pfx ou un .cer en chaîne base64, exécutez la commande PowerShell suivante. Cet extrait de code convertit le certificat en une chaîne base64 qui pourra être fournie au modèle. La sortie attendue est une chaîne qui peut être stockée dans une variable et collée dans le modèle.

```powershell
[System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes("<certificate path and name>.pfx"))
```

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez configurer le déchargement SSL, consultez [Configuration d’une passerelle Application Gateway pour le déchargement SSL](application-gateway-ssl.md).

Si vous voulez configurer une passerelle Application Gateway à utiliser avec l’équilibreur de charge interne, consultez [Création d’une passerelle Application Gateway avec un équilibrage de charge interne (ILB)](application-gateway-ilb.md).

Si vous souhaitez plus d’informations sur les options d’équilibrage de charge en général, visitez :

* [Équilibrage de charge Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)


