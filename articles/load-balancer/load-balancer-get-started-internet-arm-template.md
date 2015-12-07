<properties 
   pageTitle="Créer un équilibreur de charge accessible sur Internet dans Resource Manager à l’aide d’un modèle | Microsoft Azure"
   description="Découvrez comment créer un équilibreur de charge accessible sur Internet dans Resource Manager à l'aide d'un modèle ARM"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/20/2015"
   ms.author="joaoma" />

# Prise en main de la création d'un équilibreur de charge accessible sur Internet à l'aide d'un modèle ARM

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Cet article traite du modèle de déploiement de Resource Manager. Vous pouvez également [découvrir comment créer un équilibreur de charge accessible sur Internet à l'aide du modèle de déploiement classique](load-balancer-get-started-internet-classic-portal.md).


[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## Déployer le modèle ARM en cliquant pour déployer

L’exemple de modèle disponible dans le référentiel public utilise un fichier de paramètres contenant les valeurs par défaut utilisées pour générer le scénario décrit ci-dessus. Pour déployer ce modèle en un clic, suivez [ce lien](http://go.microsoft.com/fwlink/?LinkId=544801), cliquez sur **Déployer dans Azure**, remplacez les valeurs de paramètre par défaut si nécessaire, puis suivez les instructions dans le portail.

## Déployer le modèle ARM à l'aide de PowerShell

Pour déployer le modèle ARM téléchargé à l'aide de PowerShell, suivez les étapes ci-dessous.

1. Si vous n’avez jamais utilisé Azure PowerShell, voir [Installation et configuration d’Azure PowerShell](powershell-install-configure.md) et suivre les instructions jusqu’à la fin pour vous connecter à Azure et sélectionner votre abonnement.
2. Exécutez l'applet de commande **Switch-AzureMode** pour passer en mode Resource Manager, comme illustré ci-dessous.

		Switch-AzureMode AzureResourceManager

	Voici le résultat attendu pour la commande ci-dessus :

		WARNING: The Switch-AzureMode cmdlet is deprecated and will be removed in a future release.

	>[AZURE.WARNING]L’applet de commande Switch-AzureMode sera bientôt obsolète. Lorsque ce sera le cas, toutes les applets de commande Resource Manager seront renommées.

3. Pour créer un groupe de ressources à l'aide du modèle, exécutez l'applet de commande **New-AzureResourceGroup**.

		New-AzureResourceGroup -Name TestRG -Location uswest `
		    -TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-natrules/azuredeploy.json' `
		    -TemplateParameterFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-natrules/azuredeploy.parameters.json'	

## Déployer le modèle ARM à l'aide de l'interface de ligne de commande Azure

Pour déployer le modèle ARM à l’aide de l’interface de ligne de commande Azure, procédez comme suit.

1. Si vous n'avez jamais utilisé l'interface de ligne de commande Azure, consultez [Installation et configuration de l'interface de ligne de commande Azure](xplat-cli.md) et suivez les instructions jusqu'à l'étape où vous sélectionnez votre compte et votre abonnement Azure.
2. Exécutez la commande **azure config mode** pour passer en mode Resource Manager, comme illustré ci-dessous.

		azure config mode arm

	Voici le résultat attendu pour la commande ci-dessus :

		info:    New mode is arm

3. Dans votre navigateur, accédez à ****https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-natrules/azuredeploy.parameters.json**, copiez le contenu du fichier json et collez-le dans un nouveau fichier sur votre ordinateur. Pour ce scénario, vous allez copier les valeurs ci-dessous dans un fichier nommé **c:\\lb\\azuredeploy.parameters.json**.
4. Exécutez l'applet de commande **azure group deployment create** pour déployer le nouvel équilibreur de charge à l'aide du modèle et des fichiers de paramètres que vous avez téléchargés et modifiés plus haut. La liste affichée après le résultat présente les différents paramètres utilisés.

		azure group create -n TestRG -l westus -f 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-natrules/azuredeploy.json' -e 'c:\lb\azuredeploy.parameters.json'

<!---HONumber=AcomDC_1125_2015-->