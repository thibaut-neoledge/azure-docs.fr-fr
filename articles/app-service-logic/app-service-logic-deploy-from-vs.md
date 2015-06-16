<properties 
	pageTitle="Déploiement depuis Visual Studio" 
	description="Créez un projet dans Visual Studio pour gérer votre application logique." 
	authors="stepsic-microsoft-com" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2015"
	ms.author="stepsic"/>
	
# Déploiement depuis Visual Studio

Bien que le [portail Azure](https://portal.azure.com) vous offre un excellent moyen de concevoir et gérer vos applications logiques, vous pouvez également déployer votre application logique à partir de Visual Studio. Visual Studio propose quelques fonctionnalités clés pour :

- Stocker votre application logique en même temps que d’autres ressources dans votre solution, pour contenir tous les aspects de votre application
- Garder la définition de votre application logique archivée dans le contrôle de code source pour que vous puissiez utiliser TFS ou Git pour suivre les versions 

Vous devez avoir installé la version 2.6 du Kit de développement logiciel (SDK) Azure pour pouvoir suivre les étapes ci-dessous. Recherchez [la dernière version du Kit de développement logiciel (SDK) de Visual Studio](http://azure.microsoft.com/downloads/) ici.

## Création d’un projet

1. Accédez au menu **Fichier** et sélectionnez **Nouveau** > **Projet** (ou accédez à **Ajouter** puis sélectionnez **Nouveau projet** pour l'ajouter à une solution existante) ![Menu Fichier](./media/app-service-logic-deploy-from-vs/filemenu.png)

2. Dans la boîte de dialogue, recherchez **Cloud**, puis sélectionnez **Groupe de ressources Azure**. Entrez un **Nom** puis cliquez sur **OK**. ![Ajouter un nouveau projet](./media/app-service-logic-deploy-from-vs/addnewproject.png)

3. Vous devez maintenant choisir entre **Application logique** ou **Application logique + application API**. Si vous choisissez **Application logique**, vous devez pointer vers l'API existante. Si vous choisissez **Application logique + application API** vous pouvez également créer simultanément une application API vide. ![Sélection du modèle Azure](./media/app-service-logic-deploy-from-vs/selectazuretemplate.png)

4. Une fois que vous avez sélectionné votre **modèle**, cliquez sur **OK**.

Maintenant, votre projet d'application logique est ajouté à votre solution. Vous devez voir le déploiement dans l'Explorateur de solutions : ![Déploiement](./media/app-service-logic-deploy-from-vs/deployment.png)

## Configuration de votre application logique

Une fois que vous avez un projet, vous pouvez modifier la définition de votre application logique à l'intérieur de Visual Studio. Cliquez sur le fichier JSON dans l’Explorateur de solutions. Une définition d'espace réservé s’affiche : remplissez-la avec la logique de votre application.

Il est recommandé d'utiliser des **paramètres** tout au long de votre définition. Cela sera utile si vous souhaitez procéder à un déploiement dans un environnement de développement et de production. Dans ce cas, vous devez placer l’intégralité de la configuration propre à un environnement dans fichier `.param` et utiliser les paramètres au lieu des chaînes réelles.

Actuellement, Visual Studio n'a pas de concepteur intégré, donc si vous souhaitez utiliser une interface graphique (au lieu de l'écriture JSON), vous devez utiliser le portail Azure.

Si vous avez déjà créé une application logique dans le portail Azure et que vous souhaitez l’archiver dans le contrôle de code source, vous pouvez accéder au **mode Code** dans le portail et copier la définition, utiliser des applications logiques [API REST](https://msdn.microsoft.com/library/azure/dn948510.aspx) pour obtenir la définition et utiliser [Windows PowerShell avec le gestionnaire des ressources](../powershell-azure-resource-manager.md), tout particulièrement la commande [`Get-AzureResource`](https://msdn.microsoft.com/library/dn654579.aspx) pour télécharger la définition.

## Déploiement de votre application logique

Enfin, après avoir configuré votre application, vous pouvez procéder directement au déploiement depuis Visual Studio en quelques étapes.

1. Cliquez avec le bouton droit sur le déploiement dans l'Explorateur de solutions, puis accédez à **Déployer** > **Nouveau déploiement...** ![Nouveau déploiement](./media/app-service-logic-deploy-from-vs/newdeployment.png)

2. Vous serez invité à vous connecter à votre abonnement Azure.

3. Vous devez maintenant choisir les détails du groupe de ressources dans lequel vous souhaitez déployer l'application logique. ![Déploiement vers un groupe de ressources](./media/app-service-logic-deploy-from-vs/deploytoresourcegroup.png)

    Veillez à sélectionner les fichiers de modèle et de paramètres correspondant le groupe de ressources (par exemple si vous procédez au déploiement dans un environnement de production, choisissez le fichier de paramètres de production).
    
4. L'état du déploiement s'affiche dans la fenêtre **Sortie** (vous devrez peut-être choisir **Configuration Azure**. ![Sortie](./media/app-service-logic-deploy-from-vs/output.png)

À l'avenir, vous pourrez modifier votre application logique dans le contrôle de code source et utiliser Visual Studio pour déployer de nouvelles versions. Notez que si vous modifiez directement la définition dans le portail Azure, ces modifications seront remplacées lors de votre prochain déploiement à partir de Visual Studio.

Si vous ne souhaitez pas utiliser Visual Studio tout en ayant des outils pour déployer votre application logique à partir du contrôle de code source, vous pouvez toujours utiliser l’[API](https://msdn.microsoft.com/library/azure/dn948510.aspx) ou [Powershell](../powershell-azure-resource-manager.md) directement pour automatiser vos déploiements.
<!--HONumber=54--> 