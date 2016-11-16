---
title: "Créer un équilibreur de charge interne dans Resource Manager à l’aide d’un modèle | Microsoft Docs"
description: "Découvrez comment créer un équilibreur de charge interne à l’aide d’un modèle dans Resource Manager "
services: load-balancer
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 64150862-6ced-42de-85dc-89d323257d7c
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/24/2016
ms.author: sewhee
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 482c9cd46902d9e3f4e1e0f001182fdb43ce9367


---
# <a name="create-an-internal-load-balancer-using-a-template"></a>Créer un équilibrage de charge interne à l’aide d’un modèle
[!INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]

<BR>
[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]

[modèle de déploiement classique](load-balancer-get-started-ilb-classic-ps.md).

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="deploy-the-template-by-using-click-to-deploy"></a>Déployer le modèle en un clic
L’exemple de modèle disponible dans le référentiel public utilise un fichier de paramètres contenant les valeurs par défaut utilisées pour générer le scénario décrit ci-dessus. Pour déployer ce modèle en un clic, suivez [ce lien](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-internal-load-balancer), cliquez sur **Déployer dans Azure**, remplacez les valeurs de paramètre par défaut si nécessaire, puis suivez les instructions dans le portail.

## <a name="deploy-the-template-by-using-powershell"></a>Déployer le modèle à l’aide de PowerShell
Pour déployer le modèle téléchargé à l’aide de PowerShell, suivez les étapes ci-dessous.

1. Si vous n’avez jamais utilisé Azure PowerShell, consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md) et suivez les instructions jusqu’à la fin pour vous connecter à Azure et sélectionner votre abonnement.
2. Téléchargez le fichier de paramètres sur votre disque local.
3. Modifiez et enregistrez le fichier.
4. Pour créer un groupe de ressources à l’aide du modèle, exécutez l’applet de commande **New-AzureRmResourceGroupDeployment** .
   
        New-AzureRmResourceGroupDeployment -Name TestRG -Location westus `
            -TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json' `
            -TemplateParameterFile 'C:\temp\azuredeploy.parameters.json'

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Déployer le modèle à l’aide de l’interface de ligne de commande Azure
Pour déployer le modèle à l’aide de l’interface de ligne de commande Azure, procédez comme suit.

1. Si vous n’avez jamais utilisé l’interface de ligne de commande Azure, consultez [Installation et configuration de l’interface de ligne de commande Azure](../xplat-cli-install.md) et suivez les instructions jusqu’à l’étape vous invitant à sélectionner votre compte et votre abonnement Azure.
2. Exécutez la commande **azure config mode** pour passer en mode Resource Manager, comme illustré ci-dessous.
   
        azure config mode arm
   
    Voici le résultat attendu pour la commande ci-dessus :
   
        info:    New mode is arm
3. Ouvrez le fichier de paramètres, sélectionnez son contenu et enregistrez-le dans un fichier sur votre ordinateur. Pour cet exemple, nous avons enregistré le fichier de paramètres sous le nom *parameters.json*.
4. Exécutez la commande **azure group deployment create** pour déployer le nouvel équilibreur de charge interne à l’aide du modèle et des fichiers de paramètres que vous avez téléchargés et modifiés plus tôt. La liste affichée après le résultat présente les différents paramètres utilisés.
   
        azure group create --name TestRG --location westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json --parameters-file parameters.json

## <a name="next-steps"></a>Étapes suivantes
[Configurer le mode de distribution d’équilibreur de charge à l’aide de l’affinité d’IP source](load-balancer-distribution-mode.md)

[Configuration des paramètres de délai d’expiration TCP inactif pour votre équilibrage de charge](load-balancer-tcp-idle-timeout.md)




<!--HONumber=Nov16_HO2-->


