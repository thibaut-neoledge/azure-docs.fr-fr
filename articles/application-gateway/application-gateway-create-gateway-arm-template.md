
<properties
   pageTitle="Création d’une passerelle d’application à l’aide du modèle Azure Resource Manager | Microsoft Azure"
   description="Cette page fournit des instructions pour la création d’une passerelle Azure Application Gateway à l’aide du modèle Azure Resource Manager"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/06/2016"
   ms.author="gwallace"/>


# Création d’une passerelle d’application à l’aide du modèle Azure Resource Manager

La passerelle Azure Application Gateway est un équilibreur de charge de couche 7. Elle assure l’exécution des requêtes HTTP de basculement et de routage des performances entre serveurs locaux ou dans le cloud. Une passerelle Application Gateway offre les fonctionnalités de livraison d’applications suivantes : équilibrage de charge HTTP, affinité de session basée sur les cookies et déchargement SSL (Secure Sockets Layer).

> [AZURE.SELECTOR]
- [Portail Azure](application-gateway-create-gateway-portal.md)
- [Commandes PowerShell pour Azure Resource Manager](application-gateway-create-gateway-arm.md)
- [Azure Classic PowerShell](application-gateway-create-gateway.md)
- [Modèle Azure Resource Manager](application-gateway-create-gateway-arm-template.md)
- [Interface de ligne de commande Azure](application-gateway-create-gateway-cli.md)

Vous découvrirez comment télécharger et modifier un modèle Azure Resource Manager existant à partir de GitHub, et apprendrez à déployer le modèle à partir de GitHub, de PowerShell et de l’interface de ligne de commande Azure.

Si vous déployez simplement le modèle Azure Resource Manager directement à partir de GitHub sans rien modifier, passez à la section Déployer un modèle à partir de GitHub.

## Scénario

Dans ce scénario, vous allez :

- créer une passerelle Application Gateway avec deux instances ;
- créer un réseau virtuel nommé VirtualNetwork1 avec un bloc CIDR réservé de 10.0.0.0/16 ;
- créer un sous-réseau appelé Appgatewaysubnet qui utilise 10.0.0.0/28 comme bloc CIDR ;
- installer deux adresses IP terminales précédemment configurées pour les serveurs web pour lesquels vous souhaitez équilibrer la charge du trafic. Dans cet exemple de modèle, nous allons utiliser les adresses IP terminales 10.0.1.10 et 10.0.1.11.

>[AZURE.NOTE] Ce sont les paramètres de ce modèle. Pour personnaliser le modèle, vous pouvez modifier les règles, l’écouteur et le protocole SSL en ouvrant le fichier azuredeploy.json.

![Scénario](./media/application-gateway-create-gateway-arm-template/scenario-arm.png)

## Téléchargement et découverte du modèle Azure Resource Manager

Vous pouvez télécharger le modèle Azure Resource Manager existant pour créer un réseau virtuel et deux sous-réseaux sur GitHub, apporter les modifications souhaitées, puis le réutiliser. Pour ce faire, procédez comme suit :

1. Accédez à [Créer une passerelle Application Gateway](https://github.com/Azure/azure-quickstart-templates/tree/master/101-application-gateway-create).
2. Cliquez sur **azuredeploy.json**, puis sur **RAW**.
3. Enregistrez le fichier dans un dossier local sur votre ordinateur.
4. Si vous connaissez déjà les modèles Azure Resource Manager, passez à l’étape 7.
5. Ouvrez le fichier que vous avez enregistré et consultez le contenu sous **parameters** à la ligne 5. Les paramètres du modèle Azure Resource Manager fournissent un espace réservé pour les valeurs à remplir lors du déploiement.

	| Paramètre | Description |
	|---|---|
	| **location** | Région Azure où est créée la passerelle Application Gateway |
	| **VirtualNetwork1** | Nom du nouveau réseau virtuel |
	| **addressPrefix** | Espace d’adressage du réseau virtuel, au format CIDR |
	| **ApplicationGatewaysubnet** | Nom du sous-réseau de la passerelle Application Gateway |
	| **subnetPrefix** | Bloc CIDR du sous-réseau de la passerelle Application Gateway |
	| **skuname** | Taille de l’instance SKU |
	| **capacité** | Nombre d’instances |
	| **backendaddress1** | Adresse IP du premier serveur web |
	| **backendaddress2** | Adresse IP du deuxième serveur web |


	>[AZURE.IMPORTANT] Les modèles Azure Resource Manager de GitHub sont susceptibles d’évoluer. Vérifiez le modèle avant de l’utiliser.

6. Vérifiez le contenu sous **resources** et notez les éléments suivants :

	- **type**. Type de ressource créée par le modèle. Dans ce cas, il s’agit du type **Microsoft.Network/applicationGateways**, qui représente une passerelle Application Gateway.
	- **name**. Nom de la ressource. Remarquez l’utilisation de **[parameters(’applicationGatewayName’)]**, qui signifie que le nom est fourni par vous ou un fichier de paramètres lors du déploiement.
	- **properties**. Liste des propriétés de la ressource. Ce modèle utilise le réseau virtuel et une adresse IP publique lors de la création de la passerelle Application Gateway.

7. Revenez à [https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-create/](https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-create).
8. Cliquez sur **azuredeploy-paremeters.json**, puis cliquez sur **RAW**.
9. Enregistrez le fichier dans un dossier local sur votre ordinateur.
10. Ouvrez le fichier que vous avez enregistré et modifiez les valeurs des paramètres. Utilisez les valeurs suivantes pour déployer la passerelle Application Gateway décrite dans notre scénario.

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

11. Enregistrez le fichier . Vous pouvez tester le modèle JSON et le modèle de paramètres à l’aide des outils de validation JSON en ligne comme [JSlint.com](http://www.jslint.com/).

## Déploiement du modèle Azure Resource Manager à l’aide de PowerShell

Si vous n’avez jamais utilisé Azure PowerShell, consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md) et suivez les instructions pour vous connecter à Azure et sélectionner votre abonnement.

### Étape 1

	Login-AzureRmAccount

### Étape 2

Vérifiez les abonnements associés au compte.

	Get-AzureRmSubscription

Vous êtes invité à saisir vos informations d’identification.<BR>

### Étape 3

Parmi vos abonnements Azure, choisissez celui que vous souhaitez utiliser.<BR>


	Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### Étape 4


Au besoin, créez un groupe de ressources à l’aide de l’applet de commande **New-AzureResourceGroup**. Dans l’exemple suivant, vous allez créer un groupe de ressources appelé AppgatewayRG dans les États-Unis de l’Est.

	New-AzureRmResourceGroup -Name AppgatewayRG -Location "East US"

Exécutez l’applet de commande **New-AzureRmResourceGroupDeployment** pour déployer le nouveau réseau virtuel à l’aide du modèle précédent et des fichiers de paramètres que vous avez téléchargés et modifiés.

	New-AzureRmResourceGroupDeployment -Name TestAppgatewayDeployment -ResourceGroupName AppgatewayRG `
 		-TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json

## Déploiement du modèle Azure Resource Manager à l’aide de l’interface de ligne de commande Azure

Pour déployer le modèle Azure Resource Manager que vous avez téléchargé à l’aide de l’interface de ligne de commande Azure, suivez les étapes ci-dessous :

### Étape 1

Si vous n’avez jamais utilisé l’interface de ligne de commande Azure, consultez [Installer et configurer l’interface de ligne de commande Azure](../xplat-cli-install.md) et suivez les instructions jusqu’à l’étape où vous sélectionnez votre compte et votre abonnement Azure.
### Étape 2

Exécutez la commande **azure config mode** pour passer en mode Resource Manager, comme illustré ci-dessous.

	azure config mode arm

Voici le résultat attendu pour la commande ci-dessus :

	info:	New mode is arm

### Étape 3 :

Au besoin, exécutez la commande **azure group create** pour créer un groupe de ressources, comme illustré ci-dessous. Observez le résultat de la commande. La liste affichée après le résultat présente les différents paramètres utilisés. Pour plus d’informations sur les groupes de ressources, consultez la page [Vue d’ensemble d’Azure Resource Manager](../resource-group-overview.md).

	azure group create -n appgatewayRG -l eastus

**-n (ou --name)**. Nom du nouveau groupe de ressources. Pour notre scénario, il s’agit de *appgatewayRG*.

**-l (ou --location)**. Région Azure où le nouveau groupe de ressources est créé. Pour notre scénario, il s’agit de *eastus*.

### Étape 4

Exécutez l’applet de commande **azure group deployment create** pour déployer le nouveau réseau virtuel à l’aide du modèle et des fichiers de paramètres que vous avez téléchargés et modifiés plus haut. La liste affichée après le résultat présente les différents paramètres utilisés.

	azure group deployment create -g appgatewayRG -n TestAppgatewayDeployment -f C:\ARM\azuredeploy.json -e C:\ARM\azuredeploy-parameters.json

## Déploiement du modèle Azure Resource Manager à l’aide de la fonctionnalité « cliquer pour déployer »

La fonctionnalité « cliquer pour déployer » offre une autre manière d’utiliser les modèles ARM. C’est un moyen facile d’utiliser des modèles avec le portail Azure.

### Étape 1

Accédez à [Créer une passerelle Application Gateway avec une adresse IP publique](https://azure.microsoft.com/documentation/templates/101-application-gateway-public-ip/).

### Étape 2

Cliquez sur **Déployer dans Azure**.

![Déploiement sur Azure](./media/application-gateway-create-gateway-arm-template/deploytoazure.png)

### Étape 3 :

Renseignez les paramètres du modèle de déploiement sur le portail, puis cliquez sur **OK**.

![Paramètres](./media/application-gateway-create-gateway-arm-template/ibiza1.png)

### Étape 4

Sélectionnez **Mentions légales** et cliquez sur **Acheter**.

### Étape 5

Dans le panneau Déploiement personnalisé, cliquez sur **Créer**.

## Étapes suivantes

Si vous souhaitez configurer le déchargement SSL, consultez [Configuration d’une passerelle Application Gateway pour le déchargement SSL](application-gateway-ssl.md).

Si vous voulez configurer une passerelle Application Gateway à utiliser avec l’équilibreur de charge interne, consultez [Création d’une passerelle Application Gateway avec un équilibrage de charge interne (ILB)](application-gateway-ilb.md).

Si vous souhaitez plus d'informations sur les options d'équilibrage de charge en général, consultez :

- [Équilibrage de charge Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=AcomDC_0907_2016-->