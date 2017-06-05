---
title: "Créer une application web ASP.NET Core dans Visual Studio Code"
description: "Ce tutoriel montre comment créer une application web ASP.NET Core via Visual Studio Code."
services: app-service\web
documentationcenter: .net
author: erikre
manager: erikre
editor: jimbe
ms.assetid: 877bff08-9ef7-405a-a1ca-1194f33c55f2
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: article
ms.date: 02/26/2016
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: 46e3852dc84265de41bb358f482dec06608e7efa
ms.contentlocale: fr-fr
ms.lasthandoff: 05/08/2017


---
# <a name="create-an-aspnet-core-web-app-in-visual-studio-code"></a>Créer une application web ASP.NET Core dans Visual Studio Code
## <a name="overview"></a>Vue d'ensemble
Ce tutoriel vous montre comment créer une application web ASP.NET Core à l’aide de [Visual Studio Code (VS Code)](http://code.visualstudio.com//Docs/whyvscode) et comment la déployer dans [Azure App Service](../app-service/app-service-value-prop-what-is.md). 

> [!NOTE]
> Même si cet article fait référence aux applications Web, il s’applique également aux applications API et aux applications mobiles. 
> 
> 

ASP.NET Core est une refonte importante d’ASP.NET. ASP.NET Core est un nouveau framework open source et interplateforme qui vous permet de créer des applications web modernes basées sur le cloud à l’aide de .NET. Pour plus d’informations, consultez la page [Présentation d’ASP.NET Core](http://docs.asp.net/latest/conceptual-overview/aspnet.html). Pour plus d'informations sur les applications Web Azure App Service, consultez la [Vue d'ensemble de Web Apps](app-service-web-overview.md).

[!INCLUDE [app-service-web-try-app-service.md](../../includes/app-service-web-try-app-service.md)]

## <a name="prerequisites"></a>Composants requis
* Installation de [VS Code](http://code.visualstudio.com/Docs/setup).
* Installation de Git - vous pouvez l’installer depuis l’un de ces emplacements : [Chocolatey](https://chocolatey.org/packages/git) ou [git-scm.com](http://git-scm.com/downloads). Si vous n’êtes pas familiarisé avec Git, choisissez [git-scm.com](http://git-scm.com/downloads) et sélectionnez l’option vous permettant d’ **utiliser Git à partir de l’invite de commandes Windows**. Une fois Git installé, vous devrez également définir le nom d'utilisateur et l’adresse de messagerie Git qui vous seront ultérieurement demandés dans ce didacticiel (lorsque vous effectuerez une validation à partir de VS Code).  

## <a name="install-aspnet-core"></a>Installer ASP.NET Core
ASP.NET Core est une pile .NET lean conçue pour créer des applications web et cloud modernes capables de s’exécuter sur OS X, Linux et Windows. Elle a été construite intégralement pour fournir une infrastructure de développement optimisée pour les applications qui sont déployées sur le cloud ou qui sont exécutées en local. Elle inclut des composants modulaires associés à des frais généraux réduits. Ainsi, vous bénéficiez d’une certaine flexibilité lors de la création de vos solutions.

Ce tutoriel vise à vous aider à créer des applications à l’aide des dernières versions de développement d’ASP.NET Core. Les instructions suivantes sont spécifiques à Windows. Pour obtenir des instructions d’installation sur OS X, Linux et Windows, consultez [Bien démarrer avec ASP.NET Core](https://docs.microsoft.com/aspnet/core/getting-started). 


> [!NOTE]
> Pour obtenir des instructions d’installation plus détaillées pour OS X, Linux et Windows, consultez [Installation d’ASP.NET Core](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx). 
> 
> 

## <a name="create-the-web-app"></a>Créer l’application web
Cette section vous montre comment structurer une nouvelle application web ASP.NET à l’aide de l’outil CLI .NET. 

1. Entrez la commande suivante dans l'invite de commandes pour créer le dossier du projet et structurer l'application.
   
```terminal
mkdir SampleWebApp
cd SampleWebApp
dotnet new mvc
```
![CLI dotnet - Générateur ASP.NET Core](./media/web-sites-create-web-app-using-vscode/dotnetcore-mvc-01.png)

2. Pour restaurer les packages NuGet nécessaires, exécutez la commande suivante :
   
    ```terminal
    dotnet restore
    ```

## <a name="run-the-web-app-locally"></a>Exécuter l’application web localement
Maintenant que vous avez créé l'application Web et extrait tous les packages NuGet pour l'application, vous pouvez exécuter l'application Web localement.

1. Exécutez l’application (la commande `dotnet run` génère l’application quand elle est obsolète) :
    ```terminal
    dotnet run
    ```
2. Ouvrez un navigateur et accédez à l'URL suivante.
   
    **http://localhost:5000**
   
    La page par défaut de l’application Web apparaîtra comme suit.
   
    ![Application Web locale dans un navigateur](./media/web-sites-create-web-app-using-vscode/08-web-app.png)
3. Fermez votre navigateur. Dans la **Fenêtre de commande**, appuyez sur **Ctrl+C** pour arrêter l’application et fermer la **Fenêtre de commande**. 

## <a name="create-a-web-app-in-the-azure-portal"></a>Créer une application web dans le portail Azure
La procédure suivante vous guidera dans la création d'une application Web dans le portail Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **NOUVEAU** en haut à gauche du portail.
3. Cliquez sur **Applications web > Application web**.
   
    ![Nouvelle application Web Azure](./media/web-sites-create-web-app-using-vscode/09-azure-newwebapp.png)
4. Entrez une valeur pour **Nom**, par exemple **SampleWebAppDemo**. Notez que ce nom doit être unique et qu’il sera imposé par le portail lorsque vous essayerez d'entrer le nom. Par conséquent, si vous décidez d’entrer une valeur différente, vous devez remplacer cette valeur pour chaque occurrence de **SampleWebAppDemo** que vous apercevez dans ce didacticiel. 
5. Sélectionnez un **plan App Service** existant ou créez-en un. Si vous créez un plan, sélectionnez le niveau de tarification, l’emplacement et d’autres options. Pour plus d’informations sur les plans App Service, consultez la rubrique [Présentation détaillée des plans d’Azure App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
   
    ![Panneau Nouvelle application Web Azure](./media/web-sites-create-web-app-using-vscode/10-azure-newappblade.png)
6. Cliquez sur **Create**.
   
    ![Panneau Application Web](./media/web-sites-create-web-app-using-vscode/11-azure-webappblade.png)

## <a name="enable-git-publishing-for-the-new-web-app"></a>Activer la publication Git pour la nouvelle application Web
Git est un système de contrôle de version distribué permettant de déployer votre application Web Azure App Service. Vous stockerez le code que vous écrivez pour votre application Web dans un référentiel Git local, et vous déploierez votre code dans Azure par transmission de type Push vers un référentiel distant.   

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **Parcourir**.
3. Cliquez sur **Applications Web** pour afficher la liste des applications Web associées à votre abonnement Azure.
4. Sélectionnez l'application Web que vous avez créée dans ce didacticiel.
5. Dans le panneau de l’application web, cliquez sur **Paramètres** > **Déploiement continu**. 
   
    ![Hôte d’application Web Azure](./media/web-sites-create-web-app-using-vscode/14-azure-deployment.png)
6. Cliquez sur **Choisir la source > Référentiel Git local**.
7. Cliquez sur **OK**.
   
    ![Référentiel Git local dans Microsoft Azure](./media/web-sites-create-web-app-using-vscode/15-azure-localrepository.png)
8. Si vous n'avez pas précédemment configuré les informations d'identification de déploiement pour la publication d'une application Web ou d'une autre application App Service, configurez-les maintenant :
   
   * Cliquez sur **Paramètres** > **Informations d’identification de déploiement**. Le panneau **Définir les informations d’identification de déploiement** s’affiche.
   * Créez un nom d'utilisateur et un mot de passe.  Vous aurez besoin de ce mot de passe lorsque vous configurerez Git.
   * Cliquez sur **Save**.
9. Dans le panneau de l’application web, cliquez sur **Paramètres > Propriétés**. L'URL du référentiel Git distant vers lequel vous allez déployer se trouve sous **URL GIT**.
10. Copiez la valeur **URL GIT** pour pouvoir l’utiliser plus tard dans le didacticiel.
    
    ![URL Git de Microsoft Azure](./media/web-sites-create-web-app-using-vscode/17-azure-giturl.png)

## <a name="publish-your-web-app-to-azure-app-service"></a>Publier votre application Web sur Azure App Service
Dans cette section, vous créerez un référentiel Git local et vous effectuerez une transmission de type Push depuis ce référentiel vers Azure pour déployer votre application Web sur Azure.

1. Dans VS Code, sélectionnez l’option **Git** dans la barre de navigation gauche.
   
    ![Icône Git dans VS Code](./media/web-sites-create-web-app-using-vscode/git-icon.png)
2. Sélectionnez **Initialiser le référentiel Git** pour vous assurer que votre espace de travail est sous le contrôle de code source Git. 
   
    ![Initialiser Git](./media/web-sites-create-web-app-using-vscode/19-initgit.png)
3. Ouvrez la fenêtre de commande et remplacez les répertoires par le répertoire hébergeant votre application web. Puis, entrez la commande suivante :
   
        git config core.autocrlf false
   
    Cette commande empêche un problème lié au texte impliquant des terminaisons CRLF et des terminaisons LF.
4. Dans VS Code, ajoutez un message de validation, puis cliquez sur l’icône de coche **Valider tout** .
   
    ![Git - valider tout ](./media/web-sites-create-web-app-using-vscode/20-git-commit.png)
5. Une fois le traitement de Git terminé, vous apercevrez qu'il n'y a aucun fichier répertorié dans la fenêtre Git sous **Modifications**. 
   
    ![Git - aucune modification](./media/web-sites-create-web-app-using-vscode/no-changes.png)
6. Rétablissez la fenêtre de commande où l'invite de commande désigne le répertoire où se trouve votre application web.
7. Créez une référence distante pour envoyer les mises à jour vers l'application Web en utilisant l'URL Git (finissant par « .git ») que vous avez copiée plus tôt.
   
        git remote add azure [URL for remote repository]
8. Configurez Git pour enregistrer vos informations d'identification localement afin qu'elles soient automatiquement ajoutées à vos commandes push générées à partir de VS Code.
   
        git config credential.helper store
9. Envoyez vos modifications dans Azure en entrant la commande suivante : Après cette commande push initiale vers Azure, vous pourrez exécuter toutes les commandes push à partir de VS Code. 
   
        git push -u azure master
   
    Vous êtes invité à entrer le mot de passe que vous avez créé précédemment dans Azure. **Remarque : votre mot de passe ne sera pas visible.**
   
    La sortie de la commande mentionnée ci-dessus se termine par un message indiquant que le déploiement a réussi.
   
        remote: Deployment successful.
        To https://user@testsite.scm.azurewebsites.net/testsite.git
        [new branch]      master -> master

> [!NOTE]
> Si vous apportez des modifications à votre application, vous pouvez republier directement dans VS Code à l’aide de la fonctionnalité intégrée de Git en sélectionnant l’option **Valider tout** suivie de l’option **Push**. Vous trouverez l’option **Push** dans le menu déroulant à côté des boutons **Valider tout** et **Actualiser**.
> 
> 

Si vous avez besoin de collaborer sur un projet, envisagez d'alterner les commandes push vers GitHub et les commandes push vers Azure.

## <a name="run-the-app-in-azure"></a>Exécuter l’application dans Microsoft Azure
Maintenant que vous avez déployé votre application Web, exécutez l'application lorsque celle-ci est hébergée dans Azure. 

Cette opération peut être réalisée de deux manières :

* Ouvrez un navigateur et entrez le nom de votre application Web comme suit.   
  
        http://SampleWebAppDemo.azurewebsites.net
* Dans le portail Azure, localisez le panneau de l’application web pour votre application web, puis cliquez sur **Parcourir** pour afficher votre application 
* dans votre navigateur par défaut.

![Application Web Azure](./media/web-sites-create-web-app-using-vscode/21-azurewebapp.png)

## <a name="summary"></a>Résumé
Dans ce didacticiel, vous avez appris à créer une application Web dans VS Code et à le déployer dans Azure. Pour plus d’informations sur VS Code, consultez l’article [Pourquoi Visual Studio Code ?](https://code.visualstudio.com/Docs/) Pour plus d’informations sur les applications web App Service, consultez [Vue d’ensemble des applications web](app-service-web-overview.md). 


