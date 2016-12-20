---
title: "Déployer des applications logiques dans Visual Studio | Microsoft Docs"
description: "Créez un projet dans Visual Studio pour créer et déployer votre application logique."
author: jeffhollan
manager: erikre
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: e484e5ce-77e9-4fa9-bcbe-f851b4eb42a6
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 73a57310c07ed946480f60f6564d8619e54155a5


---
# <a name="build-and-deploy-logic-apps-in-visual-studio"></a>Créer et déployer des applications logiques dans Visual Studio
Bien que le [portail Azure](https://portal.azure.com/) vous offre un excellent moyen de concevoir et gérer vos applications logiques, vous pouvez également concevoir et déployer votre application logique à partir de Visual Studio.  Logic Apps s’accompagne d’une puissante suite d’outils Visual Studio qui vous permet de développer une application logique à l’aide du concepteur, de configurer des modèles de déploiement et d’automatisation, et de déployer vos applications dans n’importe quel environnement.  

## <a name="installation-steps"></a>Procédure d’installation :
Voici les étapes à suivre pour installer et configurer les outils Visual Studio pour Logic Apps.

### <a name="prerequisites"></a>Composants requis
* [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* [Dernier kit de développement logiciel (SDK) Azure](https://azure.microsoft.com/downloads/) (2.9.1 ou supérieur)
* [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)
* Accès au web lors de l’utilisation du concepteur intégré

### <a name="install-visual-studio-tools-for-logic-apps"></a>Installer les outils Visual Studio pour Logic Apps
Une fois que les composants requis installés, 

1. Ouvrez le menu **Outils** de Visual Studio 2015 et cliquez sur **Extensions et mises à jour**
2. Sélectionnez la catégorie **En ligne** pour effectuer une recherche en ligne
3. Recherchez **Logic Apps** pour afficher les **outils Azure Logic Apps pour Visual Studio**
4. Cliquez sur le bouton **Télécharger** pour télécharger et installer l’extension
5. Redémarrez Visual Studio après l’installation

> [!NOTE]
> Vous pouvez également télécharger l’extension directement à partir de [ce lien](https://visualstudiogallery.msdn.microsoft.com/e25ad307-46cf-412e-8ba5-5b555d53d2d9)
> 
> 

Une fois l’installation effectuée, vous ne pourrez pas utiliser le projet de groupe de ressources Azure avec le Concepteur d’applications logiques.

## <a name="create-a-project"></a>Création d’un projet
1. Accédez au menu **Fichier** et sélectionnez **Nouveau** >  **Projet** (ou accédez à **Ajouter**, puis sélectionnez **Nouveau projet** pour l’ajouter à une solution existante) :  ![menu Fichier](./media/app-service-logic-deploy-from-vs/filemenu.png)
2. Dans la boîte de dialogue, recherchez **Cloud**, puis sélectionnez **Groupe de ressources Azure**. Entrez un **Nom** puis cliquez sur **OK**.
    ![Ajouter un nouveau projet](./media/app-service-logic-deploy-from-vs/addnewproject.png)
3. Sélectionnez le modèle **Application logique** . Vous obtenez un modèle de déploiement d’application logique vide que vous pourrez utiliser comme point de départ.
    ![Sélection du modèle Azure](./media/app-service-logic-deploy-from-vs/selectazuretemplate.png)
4. Une fois que vous avez sélectionné votre **modèle**, appuyez sur **OK**.
   
    Maintenant, votre projet d’application logique est ajouté à votre solution. Vous devez voir le fichier de déploiement dans l’Explorateur de solutions :  
   
    ![Déploiement](./media/app-service-logic-deploy-from-vs/deployment.png)

## <a name="using-the-logic-app-designer"></a>Utilisation du Concepteur d’applications logiques
Une fois que vous disposez d’un projet de groupe de ressources Azure contenant une application logique, vous pouvez ouvrir le concepteur dans Visual Studio pour vous aider à créer le flux de travail.  Le concepteur utilise une connexion internet pour interroger les connecteurs concernant les données et les propriétés disponibles (par exemple, si vous utilisez le connecteur Dynamics CRM Online, le concepteur interrogera votre instance CRM pour répertorier les propriétés par défaut et personnalisées disponibles).

1. Cliquez avec le bouton droit sur le fichier `<template>.json` et sélectionnez **Open with Logic App Designer (Ouvrir avec le Concepteur d’applications logiques)** (ou `Ctrl+L`)
2. Choisissez l’abonnement, le groupe de ressources et l’emplacement du modèle de déploiement
   * Il est important de noter que la conception d’une logique d’application créera des ressources **Connexion d’API** pour rechercher des propriétés lors de la conception.  Le groupe de ressources sélectionné correspond au groupe de ressources utilisé pour créer ces connexions au moment de la conception.  Vous pouvez afficher ou modifier les connexions d’API via le menu **Connexions d’API**du portail Azure.
     ![Sélecteur d’abonnement](./media/app-service-logic-deploy-from-vs/designer_picker.png)
3. Le concepteur doit générer des résultats compte tenu de la définition du fichier `<template>.json` .
4. Vous pouvez désormais créer et concevoir votre application logique ; toutes vos modifications seront mises à jour dans le modèle de déploiement.
    ![Concepteur dans Visual Studio](./media/app-service-logic-deploy-from-vs/designer_in_vs.png)

Les ressources `Microsoft.Web/connections` seront également ajoutées à votre fichier de ressources pour toutes les connexions nécessaires au fonctionnement de l’application logique.  Ces propriétés de connexion peuvent être définies lors du déploiement et être gérées après le déploiement via le menu **Connexions d’API** du portail Azure.

### <a name="switching-to-the-json-code-view"></a>Basculer vers le mode Code JSON
Vous pouvez sélectionner l’onglet **Mode Code** situé en bas du concepteur pour basculer vers la représentation JSON de l’application logique.  Pour revenir au code JSON de la ressource complète, cliquez sur le fichier `<template>.json` et sélectionnez **Ouvrir**.

### <a name="saving-the-logic-app"></a>Enregistrement de l’application logique
Vous pouvez enregistrer l’application logique à tout moment à l’aide du bouton **Enregistrer** ou de la touche `Ctrl+S`.  Si votre application logique contient des erreurs au moment de l’enregistrement, celles-ci s’afficheront dans la fenêtre **Sorties** de Visual Studio.

## <a name="deploying-your-logic-app"></a>Déploiement de votre application logique
Enfin, après avoir configuré votre application, vous pouvez procéder directement au déploiement depuis Visual Studio en quelques étapes. 

1. Cliquez avec le bouton droit sur le projet dans l’Explorateur de solutions, puis accédez à **Déployer** > **Nouveau déploiement...**
    ![Nouveau déploiement](./media/app-service-logic-deploy-from-vs/newdeployment.png)
2. Vous êtes invité à vous connecter à votre abonnement Azure. 
3. Vous devez maintenant choisir les détails du groupe de ressources dans lequel vous souhaitez déployer l'application logique. 
    ![Déploiement vers un groupe de ressources](./media/app-service-logic-deploy-from-vs/deploytoresourcegroup.png)
   
   > [!NOTE]
   > Veillez à sélectionner les fichiers de modèle et de paramètres correspondant le groupe de ressources (par exemple si vous procédez au déploiement dans un environnement de production, choisissez le fichier de paramètres de production). 
   > 
   > 
4. Sélectionnez le bouton Déployer.
5. L’état du déploiement s’affiche dans la fenêtre **Sortie** (vous devrez peut-être choisir **Approvisionnement Azure**. 
    ![Sortie](./media/app-service-logic-deploy-from-vs/output.png)

À l’avenir, vous pourrez modifier votre application logique dans le contrôle de code source et utiliser Visual Studio pour déployer de nouvelles versions. 

> [!NOTE]
> Si vous modifiez directement la définition dans le portail Azure, ces modifications sont remplacées pendant votre prochain déploiement à partir de Visual Studio.
> 
> 

## <a name="next-steps"></a>Étapes suivantes
* Pour vous familiariser avec les applications logiques, effectuez le didacticiel [Créer une application logique](app-service-logic-create-a-logic-app.md) .  
* [Afficher des exemples et des scénarios courants](app-service-logic-examples-and-scenarios.md)
* [Logic Apps vous permet d’automatiser vos processus métiers](http://channel9.msdn.com/Events/Build/2016/T694) 
* [Apprenez à intégrer vos systèmes avec Logic Apps](http://channel9.msdn.com/Events/Build/2016/P462)




<!--HONumber=Nov16_HO3-->


