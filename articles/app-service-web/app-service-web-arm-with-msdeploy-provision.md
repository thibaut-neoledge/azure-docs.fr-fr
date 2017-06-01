---
title: "Déployer une application web avec MSDeploy, un nom d’hôte et un certificat SSL"
description: "Utiliser un modèle Azure Resource Manager pour déployer une application web à l’aide de MSDeploy et configurer un nom d’hôte personnalisé et un certificat SSL"
services: app-service\web
manager: erikre
documentationcenter: 
author: jodehavi
ms.assetid: 66366a72-cef7-4d75-8779-f4d32ed33cf7
ms.service: app-service-web
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2016
ms.author: jodehavi
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: 071be50ff7f72ecd711b2c3036f39b70df01a6ba
ms.contentlocale: fr-fr
ms.lasthandoff: 05/26/2017


---
# <a name="deploy-a-web-app-with-msdeploy-custom-hostname-and-ssl-certificate"></a>Déployer une application web avec MSDeploy, un nom d’hôte personnalisé et un certificat SSL
Ce guide explique la création d’un déploiement de bout en bout pour une Application Web Azure, exploitant MSDeploy, ainsi que l’ajout d’un nom d’hôte personnalisé et un certificat SSL au modèle ARM.

Pour en savoir plus sur la création de modèles, voir [Création de modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

### <a name="create-sample-application"></a>Créer un exemple d’application
Vous allez déployer une application web ASP.NET. La première étape consiste à créer une application web simple (vous pouvez également choisir d’utiliser une application web existante, auquel cas vous pouvez ignorer cette étape).

Ouvrez Visual Studio 2015 et sélectionnez Fichier > Nouveau Projet. Dans la boîte de dialogue qui s’affiche, choisissez Web > Application Web ASP.NET. Sous Modèles, choisissez Web et le modèle MVC. Pour *Modifier le type d’authentification*, sélectionnez *Aucune authentification*. uniquement pour simplifier le plus possible l’exemple d’application.

À ce stade, vous obtiendrez une application web ASP.Net prête à l’emploi à utiliser dans le cadre de votre processus de déploiement.

### <a name="create-msdeploy-package"></a>Créer un package MSDeploy
L’étape suivante consiste à créer le package afin de déployer l’application web sur Azure. Pour ce faire, enregistrez votre projet, puis exécutez la commande suivante à partir de la ligne de commande :

    msbuild yourwebapp.csproj /t:Package /p:PackageLocation="path\to\package.zip"

Vous créerez ainsi un package compressé dans le dossier PackageLocation. L’application est désormais prête à être déployée, et vous pouvez ainsi créer un modèle Azure Resource Manager pour réaliser l’opération.

### <a name="create-arm-template"></a>Créer un modèle ARM
Nous allons commencer par un modèle ARM de base qui va créer une application web et un plan d’hébergement (Notez que les paramètres et les variables ne sont pas affichés, par souci de concision).

    {
        "name": "[parameters('appServicePlanName')]",
        "type": "Microsoft.Web/serverfarms",
        "location": "[resourceGroup().location]",
        "apiVersion": "2014-06-01",
        "dependsOn": [ ],
        "tags": {
            "displayName": "appServicePlan"
        },
        "properties": {
            "name": "[parameters('appServicePlanName')]",
            "sku": "[parameters('appServicePlanSKU')]",
            "workerSize": "[parameters('appServicePlanWorkerSize')]",
            "numberOfWorkers": 1
        }
    },
    {
        "name": "[variables('webAppName')]",
        "type": "Microsoft.Web/sites",
        "location": "[resourceGroup().location]",
        "apiVersion": "2015-08-01",
        "dependsOn": [
            "[concat('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        ],
        "tags": {
            "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]": "Resource",
            "displayName": "webApp"
        },
        "properties": {
            "name": "[variables('webAppName')]",
            "serverFarmId": "[resourceId('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        }
    }

Vous devrez ainsi modifier la ressource d’application web pour prendre une ressource MSDeploy imbriquée. Vous pourrez ainsi référencer le package créé précédemment et donner à Azure Resource Manager comme instruction d’utiliser MSDeploy afin de déployer le package sur Azure WebApp. Vous trouverez ci-dessous la ressource Microsoft.Web/sites avec une ressource MSDeploy imbriquée :

    {
        "name": "[variables('webAppName')]",
        "type": "Microsoft.Web/sites",
        "location": "[resourceGroup().location]",
        "apiVersion": "2015-08-01",
        "dependsOn": [
            "[concat('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        ],
        "tags": {
            "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]": "Resource",
            "displayName": "webApp"
        },
        "properties": {
            "name": "[variables('webAppName')]",
            "serverFarmId": "[resourceId('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        },
        "resources": [
            {
                "name": "MSDeploy",
                "type": "extensions",
                "location": "[resourceGroup().location]",
                "apiVersion": "2015-08-01",
                "dependsOn": [
                    "[concat('Microsoft.Web/sites/', variables('webAppName'))]"
                ],
                "tags": {
                    "displayName": "webDeploy"
                },
                "properties": {
                    "packageUri": "[concat(parameters('_artifactsLocation'), '/', parameters('webDeployPackageFolder'), '/', parameters('webDeployPackageFileName'), parameters('_artifactsLocationSasToken'))]",
                    "dbType": "None",
                    "connectionString": "",
                    "setParameters": {
                        "IIS Web Application Name": "[variables('webAppName')]"
                    }
                }
            }
        ]
    }

À présent, vous remarquerez que la ressource MSDeploy utilise une propriété **packageUri** est définie comme suit :

    "packageUri": "[concat(parameters('_artifactsLocation'), '/', parameters('webDeployPackageFolder'), '/', parameters('webDeployPackageFileName'), parameters('_artifactsLocationSasToken'))]"

Ce **packageUri** accepte l’uri de compte de stockage qui pointe vers le compte de stockage dans lequel vous allez télécharger le zip de package. Le Gestionnaire de ressources Azure s’appuie sur [Signatures d’accès partagé](../storage/storage-dotnet-shared-access-signature-part-1.md) pour balayer le package localement depuis le compte de stockage lorsque vous déployez le modèle. Ce processus sera automatisé via un script PowerShell qui téléchargera le package et appellera l’API de gestion Azure pour créer les clés requises et transférer ces informations dans le modèle en tant que paramètres (*_artifactsLocation* et *_artifactsLocationSasToken*). Vous devrez définir des paramètres pour le dossier et le nom de fichier dans lequel le package téléchargé sous le conteneur de stockage.

Vous devez ensuite ajouter une autre ressource imbriquée pour configurer les liaisons du nom d’hôte et tirer parti d’un domaine personnalisé. Vous devez d’abord vous assurer que vous êtes propriétaire du nom d’hôte et configurer ce dernier pour qu’Azure le vérifie. Consultez [Configurer un nom de domaine personnalisé dans Azure App Service](app-service-web-tutorial-custom-domain.md). Une fois cette opération effectuée, vous pouvez ajouter les éléments suivants à votre modèle dans la section des ressources Microsoft.Web/sites :

    {
        "apiVersion": "2015-08-01",
        "type": "hostNameBindings",
        "name": "www.yourcustomdomain.com",
        "dependsOn": [
            "[concat('Microsoft.Web/sites/', variables('webAppName'))]"
        ],
        "properties": {
            "domainId": null,
            "hostNameType": "Verified",
            "siteName": "variables('webAppName')"
        }
    }

Enfin, vous devez ajouter une autre ressource de niveau supérieur, Microsoft.Web/certificates. Cette ressource contient votre certificat SSL et se trouve au même niveau que l’application web et le plan d’hébergement.

    {
        "name": "[parameters('certificateName')]",
        "apiVersion": "2014-04-01",
        "type": "Microsoft.Web/certificates",
        "location": "[resourceGroup().location]",
        "properties": {
            "pfxBlob": "pfx base64 blob",
            "password": "some pass"
        }
    }

Vous devez disposer d’un certificat SSL valide pour pouvoir configurer cette ressource. Une fois que vous avez ce certificat valide, vous devez extraire les octets pfx sous forme de chaîne Base64. Pour ce faire, vous pouvez utiliser la commande PowerShell suivante :

    $fileContentBytes = get-content 'C:\path\to\cert.pfx' -Encoding Byte

    [System.Convert]::ToBase64String($fileContentBytes) | Out-File 'pfx-bytes.txt'

Vous pouvez ensuite la transférer en tant que paramètre de modèle de déploiement ARM.

À ce stade, le modèle ARM est prêt.

### <a name="deploy-template"></a>Déployer un modèle
Les étapes finales servent à rassembler les différents éléments pour obtenir un déploiement de bout en bout. Pour rendre le déploiement plus facile, vous pouvez utiliser le script PowerShell **Deploy-azureresourcegroup.ps1** qui est ajouté lorsque vous créez un projet de groupe de ressources Azure dans Visual Studio pour faciliter le téléchargement de tous les artefacts requis dans le modèle. Il vous oblige à créer un compte de stockage que vous voulez utiliser à l’avance. Pour cet exemple, j’ai créé un compte de stockage partagé pour y télécharger le package.zip. Le script utilisera AzCopy pour télécharger le package sur le compte de stockage. Accédez à l’emplacement du dossier de l’artefact et le script téléchargera automatiquement tous les fichiers de ce répertoire dans le conteneur de stockage nommé. Après avoir appelé Deploy-azureresourcegroup.ps1, vous devez mettre à jour les liaisons SSL pour associer le nom d’hôte personnalisé avec votre certificat SSL.

Le PowerShell suivant montre le déploiement complet appelant Deploy-azureresourcegroup.ps1 :

    #Set resource group name
    $rgName = "Name-of-resource-group"

    #call deploy-azureresourcegroup script to deploy web app

    .\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation "East US" `
                                    -ResourceGroupName $rgName `
                                    -UploadArtifacts `
                                    -StorageAccountName "name-of-storage-acct-for-package" `
                                    -StorageAccountResourceGroupName "resource-group-name-storage-acct" `
                                    -TemplateFile "web-app-deploy.json" `
                                    -TemplateParametersFile "web-app-deploy-parameters.json" `
                                    -ArtifactStagingDirectory "C:\path\to\packagefolder\"

    #update web app to bind ssl certificate to hostname. This has to be done after creation above.

    $cert = Get-PfxCertificate -FilePath C:\path\to\certificate.pfx

    $ar = Get-AzureRmResource -Name nameofwebsite -ResourceGroupName $rgName -ResourceType Microsoft.Web/sites -ApiVersion 2014-11-01

    $props = $ar.Properties

    $props.HostNameSslStates[2].'SslState' = 1
    $props.HostNameSslStates[2].'thumbprint' = $cert.Thumbprint
    $props.hostNameSslStates[2].'toUpdate' = $true

    Set-AzureRmResource -ApiVersion 2014-11-01 -Name nameofwebsite -ResourceGroupName $rgName -ResourceType Microsoft.Web/sites -PropertyObject $props

À ce stade, votre application doit être déployée, et vous devez être en mesure d’y accéder via https://www.votredomainepersonnalisé.com


