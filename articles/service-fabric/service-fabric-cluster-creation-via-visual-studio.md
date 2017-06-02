---
title: "Configuration d’un cluster Service Fabric à l’aide de Visual Studio | Microsoft Docs"
description: "Décrit comment configurer un cluster Service Fabric à l’aide d’un modèle Azure Resource Manager créé par un projet de groupe de ressources Azure dans Visual Studio"
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: adegeo
editor: 
ms.assetid: bd2c0511-36c9-4828-8dc3-69e4b6a70567
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/21/2017
ms.author: mikhegn
ms.translationtype: Human Translation
ms.sourcegitcommit: 0eefa64d6292dec14842c031ebad80fa9094436d
ms.openlocfilehash: 535e33eec22ed1c488cace9442328084b416b7a8
ms.contentlocale: fr-fr
ms.lasthandoff: 02/22/2017


---
# <a name="set-up-a-service-fabric-cluster-by-using-visual-studio"></a>Configuration d’un cluster Service Fabric à l’aide de Visual Studio
Cet article décrit comment configurer un cluster Azure Service Fabric à l’aide de Visual Studio et d’un modèle Azure Resource Manager. Nous utiliserons un projet du groupe de ressources Azure Visual Studio pour créer le modèle. Une fois le modèle créé, il peut être déployé directement vers Azure à partir de Visual Studio. Il peut également être utilisé à partir d’un script ou dans le cadre de la fonctionnalité d’intégration continue (CI).

## <a name="create-a-service-fabric-cluster-template-by-using-an-azure-resource-group-project"></a>Création d’un modèle de cluster Service Fabric à l’aide d’un projet de groupe de ressources Azure
Pour commencer, ouvrez Visual Studio et créez un projet de groupe de ressources Azure (disponible dans le dossier **Cloud** ) :

![Boîte de dialogue Nouveau projet avec le projet du groupe de ressources Azure sélectionné][1]

Vous pouvez créer une solution Visual Studio pour ce projet ou l’ajouter à une solution existante.

> [!NOTE]
> Si vous ne voyez pas le projet de groupe de ressources Azure sous le nœud Cloud, le Kit de développement logiciel (SDK) Azure n’est pas installé. Lancez Web Platform Installer ([à installer maintenant](http://www.microsoft.com/web/downloads/platform.aspx) si ce n’est pas déjà fait), puis recherchez « Azure SDK pour .NET » et installez la version compatible avec votre version de Visual Studio.
> 
> 

Une fois que vous avez cliqué sur le bouton OK, Visual Studio vous demande de sélectionner le modèle Resource Manager à créer :

![Boîte de dialogue Sélectionner le modèle Azure avec le modèle de cluster Service Fabric sélectionné][2]

Sélectionnez le modèle Service Fabric Cluster et cliquez à nouveau sur le bouton OK. Le projet et le modèle Resource Manager sont maintenant créés.

## <a name="prepare-the-template-for-deployment"></a>Préparation du modèle pour le déploiement
Avant de déployer le modèle pour créer le cluster, vous devez fournir les valeurs des paramètres obligatoires du modèle. Ces valeurs de paramètres sont lues à partir du fichier `ServiceFabricCluster.parameters.json`, qui se trouve dans le dossier `Templates` du projet de groupe de ressources. Ouvrez le fichier et spécifiez les valeurs suivantes :

| Nom du paramètre | Description |
| --- | --- |
| adminUsername |Nom du compte de l’administrateur pour les machines Service Fabric (nœuds). |
| certificateThumbprint |Empreinte numérique du certificat qui sécurise le cluster. |
| sourceVaultResourceId |*ID de ressource* du coffre de clés où est stocké le certificat qui sécurise le cluster. |
| certificateUrlValue |URL du certificat de sécurité du cluster. |

Le modèle Resource Manager Service Fabric Visual Studio crée un cluster sécurisé, qui est protégé par un certificat. Ce certificat est identifié par les trois derniers paramètres du modèle (`certificateThumbprint`, `sourceVaultValue` et `certificateUrlValue`) et doit être présent dans **Azure Key Vault**. Pour plus d’informations sur la création du certificat de sécurité du cluster, consultez l’article [Scénarios de sécurité d’un cluster Service Fabric](service-fabric-cluster-security.md#x509-certificates-and-service-fabric) .

## <a name="optional-change-the-cluster-name"></a>Facultatif : modifier le nom du cluster
Chaque cluster Service Fabric dispose d’un nom. Lors de la création d’un cluster Fabric dans Azure, le nom de cluster détermine (avec la région Azure) le nom du système DNS (Domain Name System) pour le cluster. Par exemple, si vous nommez votre cluster `myBigCluster`, et que l’emplacement (région Azure) du groupe de ressources qui hébergera le nouveau cluster est États-Unis de l’Est, le nom DNS du cluster sera `myBigCluster.eastus.cloudapp.azure.com`.

Par défaut, le nom du cluster est généré automatiquement et rendu unique en associant un suffixe aléatoire à un préfixe « cluster ». Cela permet d’utiliser très facilement le modèle comme un élément d’un système d’ **intégration continue** (CI). Si vous souhaitez utiliser un nom spécifique (qui a un sens pour vous) pour votre cluster, définissez la valeur de la variable `clusterName` dans le fichier de modèle Resource Manager (`ServiceFabricCluster.json`) avec le nom de votre choix. Il s’agit de la première variable définie dans ce fichier.

## <a name="optional-add-public-application-ports"></a>Facultatif : ajout de ports d’application publics
Vous pouvez également souhaiter modifier les ports d’application publics pour le cluster avant de le déployer. Par défaut, le modèle ouvre seulement deux ports TCP publics (80 et 8081). Si vous avez besoin d’autres ports pour vos applications, modifiez la définition de l’équilibreur de charge Azure dans le modèle. La définition est stockée dans le fichier modèle principal (`ServiceFabricCluster.json`). Ouvrez ce fichier et recherchez `loadBalancedAppPort`. Chaque port est associé à trois artefacts :

1. Une variable de modèle qui définit la valeur du port TCP pour le port :
   
    ```json
    "loadBalancedAppPort1": "80"
    ```
2. Une *sonde* qui définit la fréquence et la durée pendant lesquelles l’équilibrage de charge Azure tente d’utiliser un nœud Service Fabric spécifique avant de basculer vers un autre. Les sondes font partie de la ressource d’équilibreur de charge. Voici la définition de la sonde du premier port d’application par défaut :
   
    ```json
    {
        "name": "AppPortProbe1",
        "properties": {
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "port": "[variables('loadBalancedAppPort1')]",
            "protocol": "Tcp"
        }
    }
    ```
3. Une *règle d’équilibrage de charge* qui relie le port et la sonde, permettant ainsi un équilibrage de charge sur un ensemble de nœuds de cluster Service Fabric :
   
    ```json
    {
        "name": "AppPortLBRule1",
        "properties": {
            "backendAddressPool": {
                "id": "[variables('lbPoolID0')]"
            },
            "backendPort": "[variables('loadBalancedAppPort1')]",
            "enableFloatingIP": false,
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPort": "[variables('loadBalancedAppPort1')]",
            "idleTimeoutInMinutes": 5,
            "probe": {
                "id": "[concat(variables('lbID0'),'/probes/AppPortProbe1')]"
            },
            "protocol": "Tcp"
        }
    }
    ```
   Si les applications que vous envisagez de déployer sur le cluster ont besoin de davantage de ports, vous pouvez les ajouter en créant des définitions de règle de sonde et d’équilibrage de charge supplémentaires. Pour plus d’informations sur l’utilisation de l’équilibreur de charge Azure par le biais des modèles Resource Manager, consultez [Prise en main de la création d’un équilibreur de charge interne à l’aide d’un modèle](../load-balancer/load-balancer-get-started-ilb-arm-template.md).

## <a name="deploy-the-template-by-using-visual-studio"></a>Déploiement du modèle à l’aide de Visual Studio
Une fois que vous avez enregistré toutes les valeurs de paramètres requises dans le fichier`ServiceFabricCluster.param.dev.json` , vous êtes prêt à déployer le modèle et à créer votre cluster Service Fabric. Cliquez avec le bouton droit sur le projet de groupe de ressources dans l’Explorateur de solutions Visual Studio, puis sélectionnez **Déployer | Nouveau déploiement...**. Si nécessaire, Visual Studio affiche la boîte de dialogue **Déployer vers le groupe de ressources**, vous demandant de vous authentifier auprès d’Azure :

![Boîte de dialogue Déployer vers le groupe de ressources][3]

La boîte de dialogue vous permet de choisir un groupe de ressources Resource Manager existant pour le cluster et vous permet d’en créer un. Normalement, il est judicieux d’utiliser un groupe de ressources distinct pour un cluster Service Fabric.

Quand vous cliquez sur le bouton Déployer, Visual Studio vous invite à confirmer les valeurs des paramètres du modèle. Cliquez sur le bouton **Enregistrer** . Un paramètre n’a pas une valeur persistante : le mot de passe du compte d’administrateur pour le cluster. Vous devez fournir une valeur de mot de passe quand Visual Studio vous en demande une.

> [!NOTE]
> À partir d’Azure SDK 2.9, Visual Studio prend en charge la lecture des mots de passe depuis **Azure Key Vault** pendant le déploiement. Dans la boîte de dialogue de paramètres de modèle, notez que la zone de texte des paramètres `adminPassword` possède une petite icône de « clé » à droite. Cette icône vous permet de sélectionner un secret de coffre de clés existant en tant que mot de passe d’administration du cluster. Assurez-vous simplement d’abord d’activer l’accès à Azure Resource Manager pour le déploiement du modèle dans les stratégies d’accès avancé de votre coffre de clés. 
> 
> 

Vous pouvez surveiller la progression du processus de déploiement dans la fenêtre de sortie de Visual Studio. Une fois le déploiement de modèle terminé, votre nouveau cluster est prêt à utiliser.

> [!NOTE]
> Si PowerShell n’a jamais été utilisé pour administrer Azure à partir de l’ordinateur que vous utilisez actuellement, vous devez faire un peu de ménage.
> 
> 1. Activez l’écriture de scripts PowerShell en exécutant la commande [`Set-ExecutionPolicy`](https://technet.microsoft.com/library/hh849812.aspx) . Pour les machines de développement, la stratégie « unrestricted » est généralement acceptable.
> 2. Décidez s’il faut autoriser la collecte de données de diagnostics à partir de commandes Azure PowerShell et exécutez [`Enable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619303.aspx) ou [`Disable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619236.aspx) au besoin. Cela permet d'éviter les invites inutiles lors du déploiement du modèle.
> 
> 

Si des erreurs se produisent, accédez au [portail Azure](https://portal.azure.com/) et ouvrez le groupe de ressources utilisé pour le déploiement. Cliquez sur **Tous les paramètres**, puis sur**Déploiements** dans le panneau Paramètres. Un déploiement de groupe de ressources ayant échoué laisse des informations de diagnostic détaillées à cet endroit.

> [!NOTE]
> Les clusters Service Fabric nécessitent un certain nombre de nœuds actifs pour maintenir la disponibilité et préserver l’état. Cette situation est appelée « conservation du quorum ». Par conséquent, il est déconseillé d’arrêter tous les ordinateurs du cluster, sauf si vous avez d’abord effectué une [sauvegarde complète de votre état](service-fabric-reliable-services-backup-restore.md).
> 
> 

## <a name="next-steps"></a>Étapes suivantes
* [En savoir plus sur la configuration de cluster Service Fabric à l’aide du portail Azure](service-fabric-cluster-creation-via-portal.md)
* [Apprenez à gérer et déployer des applications Service Fabric à l’aide de Visual Studio](service-fabric-manage-application-in-visual-studio.md)

<!--Image references-->
[1]: ./media/service-fabric-cluster-creation-via-visual-studio/azure-resource-group-project-creation.png
[2]: ./media/service-fabric-cluster-creation-via-visual-studio/selecting-azure-template.png
[3]: ./media/service-fabric-cluster-creation-via-visual-studio/deploy-to-azure.png

