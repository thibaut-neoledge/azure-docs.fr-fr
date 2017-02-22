---
title: "Créer et déployer des applications logiques Azure dans Visual Studio | Microsoft Docs"
description: "Créez des projets Visual Studio pour concevoir, développer et déployer des applications logiques dans Azure."
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: e484e5ce-77e9-4fa9-bcbe-f851b4eb42a6
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/14/2017
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: bbee0bae5a20375f0de8adaedadd682dc051fb64
ms.openlocfilehash: d1b0ca953e5ee40129f00b78ad1a46514270d448


---
# <a name="build-and-deploy-azure-logic-apps-in-visual-studio"></a>Créer et déployer des applications logiques Azure dans Visual Studio

Bien que le [portail Azure](https://portal.azure.com/) constitue un excellent moyen pour concevoir et gérer les applications logiques, vous pouvez utiliser Visual Studio afin de créer et de déployer des applications logiques. Visual Studio met à votre disposition des outils riches vous permettant de créer des applications logiques à l’aide du Concepteur d’applications logiques, de configurer les modèles de déploiement et d’automatisation, et de déployer les applications sur n’importe quel environnement.

## <a name="installation-steps"></a>Procédure d’installation :

Pour installer et configurer les outils Visual Studio pour Azure Logic Apps, suivez ces étapes.

### <a name="prerequisites"></a>Composants requis

* [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* [Dernier kit de développement logiciel (SDK) Azure](https://azure.microsoft.com/downloads/) (2.9.1 ou supérieur)
* [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)
* Accès au web lors de l’utilisation du concepteur intégré

### <a name="install-visual-studio-tools-for-azure-logic-apps"></a>Installer les outils Visual Studio pour Azure Logic Apps

Une fois les composants requis installés :

1. Ouvrez Visual Studio 2015. Dans le menu **Outils**, sélectionnez **Extensions et mises à jour**.
2. Développez la catégorie **En ligne** pour effectuer une recherche en ligne.
3. Parcourez les résultats ou recherchez **Logic Apps** jusqu’à ce que vous trouviez **Outils Azure Logic Apps pour Visual Studio**.
4. Pour télécharger et installer l’extension, cliquez sur **Télécharger**.
5. Redémarrez Visual Studio après l’installation.

> [!NOTE]
> Vous pouvez également télécharger des outils Azure Logic Apps pour Visual Studio directement à partir de la [Place de marché Visual Studio](https://visualstudiogallery.msdn.microsoft.com/e25ad307-46cf-412e-8ba5-5b555d53d2d9).

Une fois l’installation effectuée, vous pouvez utiliser le projet de groupe de ressources Azure avec le Concepteur d’applications logiques.

## <a name="create-your-project"></a>Créer votre projet

1. Dans le menu **Fichier**, accédez à **Nouveau**, puis sélectionnez **Projet**. Ou pour ajouter votre projet à une solution existante, accédez à **Ajouter**, puis sélectionnez **Nouveau projet**.

    ![Menu Fichier](./media/logic-apps-deploy-from-vs/filemenu.png)

2. Dans la fenêtre **Nouveau projet**, recherchez **Cloud**, puis sélectionnez **Groupe de ressources Azure**. Nommez votre projet, puis cliquez sur **OK**.

    ![Ajouter un nouveau projet](./media/logic-apps-deploy-from-vs/addnewproject.png)

3. Sélectionnez le modèle **Application logique**, qui crée un modèle vierge de déploiement d’application logique que vous pourrez utiliser. Après avoir sélectionné votre modèle, cliquez sur **OK**.

    ![Sélectionner le modèle Application logique](./media/logic-apps-deploy-from-vs/selectazuretemplate1.png)

    Vous avez ajouté votre projet d’application logique à votre solution. 
    Votre fichier de déploiement doit apparaître dans l’Explorateur de solutions.

    ![Fichier de déploiement](./media/logic-apps-deploy-from-vs/deployment.png)

## <a name="create-your-logic-app-in-logic-app-designer"></a>Créer votre application logique dans le Concepteur d’applications logiques

Une fois que vous disposez d’un projet de groupe de ressources Azure contenant une application logique, vous pouvez ouvrir le Concepteur d’applications logiques dans Visual Studio pour créer votre flux de travail. 

> [!NOTE]
> Pour demander les propriétés et données disponibles aux connecteurs, le concepteur doit disposer d’une connexion à Internet. Par exemple, si vous utilisez le connecteur Dynamics CRM Online, le concepteur interroge votre instance CRM pour afficher les propriétés par défaut et personnalisées disponibles.

1. Cliquez avec le bouton droit sur votre fichier `<template>.json` et sélectionnez **Open with Logic App Designer (Ouvrir avec le Concepteur d’applications logiques)**. (`Ctrl+L`)

2. Choisissez votre abonnement Azure, le groupe de ressources et l’emplacement de votre modèle de déploiement.

    > [!NOTE]
    > La conception d’une application logique crée des ressources Connexion d’API pour rechercher des propriétés lors de la conception. Visual Studio utilise le groupe de ressources sélectionné pour créer ces connexions au moment de la conception. Pour afficher ou modifier les connexions d’API, accédez au portail Azure, et recherchez **Connexions d’API**.

    ![Sélecteur d’abonnement](./media/logic-apps-deploy-from-vs/designer_picker.png)

    Le concepteur utilise la définition du fichier `<template>.json` pour le rendu.

4. Créez et concevez votre application logique. Votre modèle de déploiement est mis à jour avec vos modifications.

    ![Concepteur d’applications logiques dans Visual Studio](./media/logic-apps-deploy-from-vs/designer_in_vs.png)

Visual Studio ajoute des ressources `Microsoft.Web/connections` à votre fichier de ressources pour toutes les connexions nécessaires au fonctionnement de votre application logique. Ces propriétés de connexion peuvent être définies lors du déploiement et être gérées après le déploiement via le menu **Connexions d’API** du Portail Azure.

### <a name="switch-to-json-code-view"></a>Basculer en mode code JSON

Pour afficher la représentation JSON de votre application logique, sélectionnez l’onglet **Mode Code** situé en bas du concepteur.

Pour revenir au code JSON de la ressource complète, cliquez sur le fichier `<template>.json` et sélectionnez **Ouvrir**.

### <a name="add-references-for-dependent-resources-to-visual-studio-deployment-templates"></a>Ajouter les références des ressources dépendantes aux modèles de déploiement Visual Studio

Si vous souhaitez que votre application logique référence des ressources dépendantes, vous pouvez utiliser les [fonctions de modèle Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions), comme les paramètres, dans votre modèle de déploiement d’application logique. Par exemple, vous souhaiterez peut-être que votre application logique référence une fonction Azure ou un compte d’intégration que vous souhaitez déployer avec votre application logique. Suivez ces instructions pour utiliser les paramètres dans votre modèle de déploiement afin que le Concepteur d’applications logiques offre un rendu correct. 

Vous pouvez utiliser les paramètres d’application logique dans ces types de déclencheurs et d’actions :

*   Flux de travail enfant
*   Conteneur de fonctions
*   Appel APIM
*   URL d’exécution de connexion d’API

Et vous pouvez utiliser ces fonctions de modèle : liste ci-dessous, incluant paramètres, variables, resourceId, concat, etc. Par exemple, voici par quoi vous pouvez remplacer l’ID de ressource de fonction Azure :

```
"parameters":{
    "functionName": {
    "type":"string",
    "minLength":1,
    "defaultValue":"<FunctionName>"
    }
},
```

Et où vous pouvez utiliser les paramètres :

```
"MyFunction": {
        "type": "Function",
        "inputs": {
        "body":{},
        "function":{
        "id":"[resourceid('Microsoft.Web/sites/functions','functionApp',parameters('functionName'))]"
        }
    },
    "runAfter":{}
}
```

> [!NOTE] 
> Pour que le Concepteur d’applications logiques fonctionne lorsque vous utilisez des paramètres, vous devez fournir des valeurs par défaut, par exemple :
> 
> ```
> "parameters": {
>     "IntegrationAccount": {
>     "type":"string",
>     "minLength":1,
>     "defaultValue":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Logic/integrationAccounts/<integrationAccountName>"
>     }
> },
> ```

### <a name="save-your-logic-app"></a>Enregistrer votre application logique

Pour enregistrer votre application logique à tout moment, accédez à **Fichier** > **Enregistrer**. (`Ctrl+S`) 

Si votre application logique comporte des erreurs lors de son enregistrement, elles sont répertoriées dans la fenêtre **Sorties** de Visual Studio.

## <a name="deploy-your-logic-app"></a>Déployer votre application logique

Après avoir configuré votre application, vous pouvez procéder directement au déploiement depuis Visual Studio en quelques étapes. 

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur votre projet, puis sélectionnez **Déployer** > **Nouveau déploiement...**

    ![Nouveau déploiement](./media/logic-apps-deploy-from-vs/newdeployment.png)

2. Lorsque vous y êtes invité, connectez-vous à votre abonnement Azure. 

3. Vous devez alors sélectionner les informations du groupe de ressources dans lequel vous souhaitez déployer votre application logique. Quand vous avez terminé, sélectionnez **Déployer**.

    > [!NOTE]
    > Assurez-vous que vous sélectionnez le bon modèle et les paramètres adéquats pour le groupe de ressources. Par exemple, si vous souhaitez déployer dans un environnement de production, choisissez le fichier de paramètres de production.

    ![Déploiement vers un groupe de ressources](./media/logic-apps-deploy-from-vs/deploytoresourcegroup.png)

    L’état du déploiement s’affiche dans la fenêtre **Sortie**. 
    Vous devrez peut-être sélectionner **Approvisionnement Azure** dans la liste **Afficher la sortie à partir de**.

    ![Sortie d’état du déploiement](./media/logic-apps-deploy-from-vs/output.png)

À l’avenir, vous pourrez modifier votre application logique dans le contrôle de code source et utiliser Visual Studio pour déployer de nouvelles versions.

> [!NOTE]
> Si vous modifiez la définition directement dans le portail Azure, ces modifications seront remplacées lors de votre prochain déploiement à partir de Visual Studio. 

## <a name="add-your-logic-app-to-an-existing-resource-group-project"></a>Ajouter votre application logique à un projet de groupe de ressources existant

Si vous disposez d’un projet de groupe de ressources existant, vous pouvez ajouter votre application logique à ce projet dans la fenêtre Structure JSON. Vous pouvez également ajouter une autre application logique en même temps que l’application que vous avez créée précédemment.

1. Ouvrez le fichier `<template>.json` .

2. Pour ouvrir la fenêtre Structure JSON, rendez-vous à **Affichage** > **Autres fenêtres** > **Structure JSON**.

3. Pour ajouter une ressource au fichier de modèle, cliquez sur **Ajouter une ressource** en haut de la fenêtre Structure JSON. Ou, dans la fenêtre Structure JSON, cliquez avec le bouton droit sur **Ressources**, puis sélectionnez **Ajouter une nouvelle ressource**.

    ![Fenêtre Structure JSON](./media/logic-apps-deploy-from-vs/jsonoutline.png)
    
4. Dans la boîte de dialogue **Ajouter une ressource**, recherchez et sélectionnez **Application logique**. Donnez un nom à votre application logique, puis choisissez **Ajouter**.

    ![Ajouter une ressource](./media/logic-apps-deploy-from-vs/addresource.png)

## <a name="next-steps"></a>Étapes suivantes

* Pour vous familiariser avec Azure Logic Apps, consultez ce didacticiel : [Créez votre première application logique](logic-apps-create-a-logic-app.md)
* [Afficher des exemples et des scénarios courants](logic-apps-examples-and-scenarios.md)
* [Découvrez comment automatiser vos processus métiers avec Azure Logic Apps](http://channel9.msdn.com/Events/Build/2016/T694)
* [Apprenez à intégrer vos systèmes avec Azure Logic Apps](http://channel9.msdn.com/Events/Build/2016/P462)



<!--HONumber=Feb17_HO3-->


