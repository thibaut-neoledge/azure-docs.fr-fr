---
title: "Création de développement de modèles pour Azure Logic Apps | Microsoft Docs"
description: "Création de modèles Azure Resource Manager pour le déploiement d’applications logiques et la gestion des versions"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 85928ec6-d7cb-488e-926e-2e5db89508ee
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.custom: H1Hack27Feb2017
ms.date: 10/18/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 9cfbb294010d48deaf4b4c78c6a6bcd59a387d87
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="create-templates-for-logic-apps-deployment-and-release-management"></a>Création de modèles pour le déploiement d’applications logiques et la gestion des versions

Après avoir créé une application logique, vous pouvez, si vous le souhaitez, la définir en tant que modèle Azure Resource Manager.
De cette façon, vous pouvez facilement déployer l’application logique dans n’importe quel environnement ou groupe de ressources où elle peut être utile.
Pour en savoir plus sur les modèles Resource Manager, consultez les articles sur la [création de modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) et le [déploiement de ressources à l’aide de modèles Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="logic-app-deployment-template"></a>Modèle de déploiement de l’application logique

Une application logique possède trois composants de base :

* **Ressource d’application logique** : regroupe des informations sur certains éléments tels que le plan de tarification, l’emplacement et la définition de workflow.
* **Définition de workflow** : décrit les étapes de workflow de votre application logique et comment le moteur Logic Apps doit exécuter workflow.
Vous pouvez afficher cette définition dans la fenêtre **Mode Code** de votre application logique.
Dans la ressource d’application logique, vous pouvez trouver cette définition dans la propriété `definition`.
* **Connexions** : il s’agit des ressources distinctes qui permettent de stocker les métadonnées en toute sécurité autour de n’importe quelle connexion de connecteur (par exemple, une chaîne de connexion et un jeton d’accès).
Dans la ressource d’application logique, votre application logique fait référence à ces ressources dans la section `parameters`.

Vous pouvez afficher tous ces éléments pour les applications logiques existantes en utilisant un outil tel que [l’Explorateur de ressources Azure](http://resources.azure.com).

Pour créer un modèle pour une application logique pouvant être utilisée avec les déploiements de groupes de ressources, vous devez définir les ressources et les paramétrer selon vos besoins.
Par exemple, si vous effectuez un déploiement dans un environnement de développement, de test et de production, vous préférez sans doute utiliser différentes chaînes de connexion à une base de données SQL dans chaque environnement.
Ou bien, vous pourrez procéder au déploiement dans différents abonnements ou groupes de ressources.  

## <a name="create-a-logic-app-deployment-template"></a>Création d’un modèle de déploiement d’applications logiques

Le moyen le plus simple pour disposer d’un modèle de déploiement d’application logique valide consiste à utiliser les [outils Visual Studio pour Logic Apps](logic-apps-deploy-from-vs.md).
Les outils Visual Studio génèrent un modèle de déploiement valide qui peut être utilisé sur n’importe quel abonnement ou emplacement.

Certains autres outils peuvent vous être utiles lorsque vous créez un modèle de déploiement d’application logique.
Vous pouvez le créer manuellement, c’est-à-dire, en utilisant les ressources déjà présentées ici pour créer des paramètres en fonction des besoins.
Vous avez également la possibilité d’utiliser un module PowerShell [Logic App Template Creator](https://github.com/jeffhollan/LogicAppTemplateCreator) . Le module open source évalue dans un premier temps l’application logique et les connexions qu’elle utilise, puis génère les ressources de modèle avec les paramètres nécessaires au déploiement.
Par exemple, si votre application logique reçoit un message à partir d’une file d’attente Service Bus Azure et ajoute des données à une base de données SQL Azure, l’outil conserve toute la logique d’orchestration et paramètre les chaînes de connexion SQL et Service Bus afin qu’elles puissent être définies à la phase de déploiement.

> [!NOTE]
> Les connexions doivent se trouver dans le même groupe de ressources que l’application logique
>
>

### <a name="install-the-logic-app-template-powershell-module"></a>Installer le module PowerShell Logic App Template
Le moyen le plus simple d’installer le module consiste à utiliser [PowerShell Gallery](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1) à l’aide de la commande `Install-Module -Name LogicAppTemplate`.  

Vous pouvez également installer le module PowerShell manuellement :

1. Téléchargez la dernière version du module [Logic App Template Creator](https://github.com/jeffhollan/LogicAppTemplateCreator/releases).  
2. Décompressez le dossier dans le dossier de votre module PowerShell (généralement `%UserProfile%\Documents\WindowsPowerShell\Modules`).

Pour que le module puisse fonctionner avec n’importe quel jeton d’accès client et abonnement, il est recommandé d’utiliser conjointement l’outil de ligne de commande [ARMClient](https://github.com/projectkudu/ARMClient) .  Ce [billet de blog ](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) présente ARMClient de façon plus détaillée.

### <a name="generate-a-logic-app-template-by-using-powershell"></a>Générer un modèle d’application logique à l’aide de PowerShell
Une fois PowerShell installé, vous pouvez générer un modèle à l’aide de la commande suivante :

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId` est l’ID d’abonnement Azure. Cette ligne obtient d’abord un jeton d’accès via ARMClient, puis le dirige vers le script PowerShell et enfin crée le modèle dans un fichier JSON.

## <a name="add-parameters-to-a-logic-app-template"></a>Ajouter des paramètres à un modèle d’application logique
Après avoir créé votre modèle d’application logique, vous pouvez continuer à ajouter ou modifier les paramètres dont vous avez besoin. Par exemple, si votre définition inclut un ID de ressource à une fonction Azure ou à un workflow imbriqué que vous envisagez de déployer dans un déploiement unique, vous pouvez ajouter des ressources supplémentaires à votre modèle et  paramétrer les ID en fonction de vos besoins. Cela s’applique également à toutes les références aux API personnalisées ou aux points de terminaison Swagger que vous pensez déployer avec chaque groupe de ressources.

## <a name="deploy-a-logic-app-template"></a>Déployer un modèle d’application logique

Vous pouvez procéder au déploiement à l’aide d’outils tels que PowerShell, API REST, [Visual Studio Team Services Release Management](#team-services) et le déploiement de modèles par le biais du powershell Azure.
Nous vous recommandons également de créer un [fichier de paramètres](../azure-resource-manager/resource-group-template-deploy.md#parameter-files) pour stocker les valeurs du paramètre.
Découvrez comment [déployer des ressources à l’aide de modèles Resource Manager](../azure-resource-manager/resource-group-template-deploy.md) ou [déployer des ressources à l’aide de modèles Resource Manager et du portail Azure](../azure-resource-manager/resource-group-template-deploy-portal.md).

### <a name="authorize-oauth-connections"></a>Autoriser des connexions OAuth

Après le déploiement, l’application logique fonctionne de bout en bout avec des paramètres valides.
Toutefois, vous devez autoriser des connexions OAuth pour générer un jeton d’accès valide.
Pour autoriser les connexions OAuth, ouvrez l’application logique dans le Concepteur d’application logique et autoriser ces connexions. Ou, pour un déploiement automatisé, utilisez un script pour autoriser chacune des connexions OAuth.
Vous trouverez un exemple de script sur GitHub sous le projet [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth) .

<a name="team-services"></a>
## <a name="visual-studio-team-services-release-management"></a>Visual Studio Team Services Release Management

Le déploiement et la gestion d’un environnement passent souvent par l’utilisation d’un outil tel que Release Management dans Visual Studio Team Services avec un modèle de déploiement d’application logique. Visual Studio Team Services inclut une tâche de [déploiement d’un groupe de ressources Azure](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/DeployAzureResourceGroup) que vous pouvez ajouter dans n’importe quel pipeline de build ou de version. Vous devez disposer d’un [principal de service](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/) pour obtenir l’autorisation de déployer, après quoi vous pouvez générer la définition de version.

1. Dans Release Management, sélectionnez **Vide** pour créer une définition vide.

    ![Créer une définition vide][1]

2. Choisissez toute ressource dont vous avez besoin pour ce faire, notamment probablement le modèle d’application logique généré manuellement ou dans le cadre du processus de création.
3. Ajoutez une tâche de **déploiement d’un groupe de ressources Azure** .
4. Configurez-la avec un [principal de service](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/), puis référencez le Modèle et les fichiers Paramètres de modèle.
5. Continuez à suivre les étapes du processus de version pour les autres environnements, tests automatisés ou approbateurs dont vous avez besoin.

<!-- Image References -->
[1]: ./media/logic-apps-create-deploy-template/emptyreleasedefinition.png
