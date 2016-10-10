<properties
   pageTitle="Créer un équilibreur de charge accessible sur Internet dans Resource Manager à l’aide d’un modèle | Microsoft Azure"
   description="Découvrez comment créer un équilibreur de charge accessible sur Internet dans Resource Manager à l’aide d’un modèle"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
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
   ms.date="02/09/2016"
   ms.author="sewhee" />

# Création d’un équilibrage de charge accessible sur Internet à l’aide d’un modèle

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] Cet article traite du modèle de déploiement de Resource Manager. Vous pouvez également [découvrir comment créer un équilibreur de charge accessible sur Internet à l'aide du modèle de déploiement classique](load-balancer-get-started-internet-classic-portal.md).


[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## Déployer le modèle en un clic

L’exemple de modèle disponible dans le référentiel public utilise un fichier de paramètres contenant les valeurs par défaut utilisées pour générer le scénario décrit ci-dessus. Pour déployer ce modèle en un clic, suivez [ce lien](http://go.microsoft.com/fwlink/?LinkId=544801), cliquez sur **Déployer dans Azure**, remplacez les valeurs de paramètre par défaut si nécessaire, puis suivez les instructions dans le portail.

## Déployer le modèle à l’aide de PowerShell

Pour déployer le modèle téléchargé à l’aide de PowerShell, suivez les étapes ci-dessous.

1. Si vous n’avez jamais utilisé Azure PowerShell, consultez [Installation et configuration d’Azure PowerShell](../../articles/powershell-install-configure.md) et suivez les instructions jusqu’à la fin pour vous connecter à Azure et sélectionner votre abonnement.

2. Pour créer un groupe de ressources à l'aide du modèle, exécutez l'applet de commande **New-AzureRmResourceGroupDeployment**.

		New-AzureRmResourceGroupDeployment -Name TestRG -Location uswest `
		    -TemplateFile 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json' `
		    -TemplateParameterFile 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.parameters.json'

## Déployer le modèle à l’aide de l’interface de ligne de commande Azure

Pour déployer le modèle à l’aide de l’interface de ligne de commande Azure, procédez comme suit.

1. Si vous n’avez jamais utilisé l’interface de ligne de commande Azure, consultez [Installation et configuration de l’interface de ligne de commande Azure](../../articles/xplat-cli-install.md) et suivez les instructions jusqu’à l’étape vous invitant à sélectionner votre compte et votre abonnement Azure.
2. Exécutez la commande **azure config mode** pour passer en mode Resource Manager, comme illustré ci-dessous.

		azure config mode arm

	Voici le résultat attendu pour la commande ci-dessus :

		info:    New mode is arm

3. Dans votre navigateur, accédez à **https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.parameters.json**, copiez le contenu du fichier json et collez-le dans un nouveau fichier sur votre ordinateur. Pour ce scénario, vous allez copier les valeurs ci-dessous dans un fichier nommé **C:\\lb\\azuredeploy.parameters.json**.
4. Exécutez l’applet de commande **azure group deployment create** pour déployer le nouvel équilibreur de charge à l’aide du modèle et des fichiers de paramètres que vous avez téléchargés et modifiés plus tôt. La liste affichée après le résultat présente les différents paramètres utilisés.

		azure group create -n TestRG -l westus -f 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json' -e 'c:\lb\azuredeploy.parameters.json'

## Étapes suivantes

[Prise en main de la configuration d’un équilibrage de charge interne](load-balancer-get-started-ilb-arm-ps.md)

[Configuration d'un mode de distribution d'équilibrage de charge](load-balancer-distribution-mode.md)

[Configuration des paramètres de délai d’expiration TCP inactif pour votre équilibrage de charge](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0928_2016-->