---
title: "Déploiement continu pour Azure Functions | Microsoft Docs"
description: "Utilisez les fonctionnalités de déploiement continu d’Azure App Service pour publier vos fonctions Azure."
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/25/2016
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: 3756f1a039730bfd99b0375ce9bfeaf27178f2e0
ms.contentlocale: fr-fr
ms.lasthandoff: 04/25/2017


---
# <a name="continuous-deployment-for-azure-functions"></a>Déploiement continu pour Azure Functions
Azure Functions vous permet de déployer votre Function App facilement à l’aide de l’intégration continue App Service. Functions s’intègre à BitBucket, Dropbox, GitHub et Visual Studio Team Services (VSTS). Cela permet d’activer un workflow dans lequel les mises à jour du code de fonctions sont effectuées à l’aide d’un de ces services intégrés qui déclenchent le déploiement dans Azure. Si vous ne connaissez pas Azure Functions, commencez par consulter l’article [Vue d’ensemble d’Azure Functions](functions-overview.md).

Le déploiement continu est une option intéressante pour les projets auxquels plusieurs contributions fréquentes sont intégrées. Il vous permet également de conserver le contrôle de code source sur le code de vos fonctions. Les sources de déploiement actuellement prises en charge sont les suivantes :

* [Bitbucket](https://bitbucket.org/)
* [Dropbox](https://www.dropbox.com/)
* Référentiel externe (Git ou Mercurial)
* [Référentiel Git local](../app-service-web/app-service-deploy-local-git.md)
* [GitHub](https://github.com)
* [OneDrive](https://onedrive.live.com/)
* [Visual Studio Team Services](https://www.visualstudio.com/team-services/)

Les déploiements sont configurés au cas par cas, selon les Function Apps. Une fois le déploiement continu activé, l’accès au code de fonction dans le portail est défini sur *lecture seule*.

## <a name="continuous-deployment-requirements"></a>Conditions requises pour le déploiement continu

Avant que vous ne configuriez le déploiement continu, votre source de déploiement doit être configurée et contenir votre code de fonctions. Dans un déploiement d’application de fonction donnée, chaque fonction se trouve dans un sous-répertoire nommé, où le nom du répertoire est le nom de la fonction.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

## <a name="set-up-continuous-deployment"></a>Configurer un déploiement continu
Utilisez cette procédure pour configurer le déploiement continu d’une Function App existante. Les étapes suivantes présentent l’intégration avec un référentiel GitHub, mais des étapes similaires s’appliquent à Visual Studio Team Services ou à d’autres services de déploiement.

1. Dans votre Function App, dans le [portail Azure](https://portal.azure.com), cliquez sur **Fonctionnalités de la plate-forme** et **Options de déploiement**. 
   
    ![Configurer un déploiement continu](./media/functions-continuous-deployment/setup-deployment.png)
 
2. Puis, dans le panneau **Déploiements**, cliquez sur **Installation**.
 
    ![Configurer un déploiement continu](./media/functions-continuous-deployment/setup-deployment-1.png)
   
2. Dans le panneau **Source de déploiement**, cliquez sur **Choisir une source**, entrez les informations pour la source de déploiement que vous avez choisie, puis cliquez sur **OK**.
   
    ![Choisir une source de déploiement](./media/functions-continuous-deployment/choose-deployment-source.png)

Une fois le déploiement continu configuré, tous les fichiers de modifications dans votre source de déploiement sont copiés vers votre Function App et un déploiement complet de site est déclenché. Le site est redéployé lorsque les fichiers de la source sont mis à jour.

## <a name="deployment-options"></a>Options de déploiement

Voici quelques scénarios de déploiement classiques :

- [Créer un déploiement intermédiaire](#staging)
- [Déplacer des fonctions existantes vers un déploiement continu](#existing)

<a name="staging"></a>
### <a name="create-a-staging-deployment"></a>Créer un déploiement intermédiaire

Function Apps ne prend pas encore en charge les emplacements de déploiement. Toutefois, vous pouvez tout de même gérer des déploiements intermédiaires et de production distincts à l’aide de l’intégration continue.

Voici à quoi ressemble généralement le processus pour configurer et travailler avec un déploiement intermédiaire :

1. Créez deux applications de fonction dans votre abonnement, une pour le code de production et une pour le code intermédiaire. 

2. Créez une source de déploiement, si vous n’en avez pas déjà. Cet exemple utilise [GitHub].

3. Pour votre Function App de production, effectuez les étapes décrites ci-dessus dans la section **Configurer un déploiement continu**, puis définissez la branche de déploiement sur la branche principale de votre référentiel GitHub.
   
    ![Choisir une branche de déploiement](./media/functions-continuous-deployment/choose-deployment-branch.png)

4. Répétez cette étape pour la Function App intermédiaire, mais cette fois, sélectionnez la branche intermédiaire au lieu de votre référentiel GitHub. Si votre source de déploiement ne prend pas en charge la création de branches, utilisez un autre dossier.
    
5. Mettez à jour votre code dans le dossier ou la branche intermédiaire, puis vérifiez que ces modifications sont répercutées dans le déploiement intermédiaire.

6. Après vérification, fusionnez les modifications de la branche intermédiaire dans la branche principale. Cette opération déclenche le déploiement de la Function App de production. Si votre source de déploiement ne prend pas en charge les branches, remplacez les fichiers dans le dossier de production par les fichiers du dossier intermédiaire.

<a name="existing"></a>
### <a name="move-existing-functions-to-continuous-deployment"></a>Déplacer des fonctions existantes pour un déploiement continu
Lorsque vous disposez de fonctions existantes que vous avez créées et conservées dans le portail, vous devez télécharger vos fichiers de code de fonction existante à l’aide du FTP ou du référentiel Git local avant de pouvoir configurer le déploiement continu comme décrit ci-dessus. Vous pouvez le faire dans les paramètres App Service pour votre application de fonction. Une fois que vos fichiers sont téléchargés, vous pouvez les télécharger vers la source de déploiement continu que vous avez choisie.

> [!NOTE]
> Après avoir configuré l’intégration continue, vous ne serez plus en mesure de modifier vos fichiers sources dans le portail Functions.

- [Comment configurer les informations d’identification de déploiement](#credentials)
- [Comment télécharger des fichiers via FTP](#downftp)
- [Comment télécharger des fichiers via le référentiel Git local](#downgit)

<a name="credentials"></a>
#### <a name="how-to-configure-deployment-credentials"></a>Comment configurer les informations d’identification de déploiement
Avant de pouvoir télécharger des fichiers à partir de votre Function App via FTP ou un référentiel Git local, vous devez configurer vos informations d’identification afin d’accéder au site. Les informations d’identification sont définies au niveau de l’application de fonction. Utilisez la procédure suivante pour définir les informations d’identification de déploiement dans le portail Azure :

1. Dans votre Function App, dans le [portail Azure](https://portal.azure.com), cliquez sur **Fonctionnalités de la plate-forme** et **Informations d’identification du déploiement**.
   
    ![Définir les informations d’identification de déploiement local](./media/functions-continuous-deployment/setup-deployment-credentials.png)

2. Entrez un nom d’utilisateur et un mot de passe, puis cliquez sur **Enregistrer**. Vous pouvez désormais utiliser ces informations d’identification pour accéder à votre application de fonction à partir du FTP ou du référentiel Git intégré.

<a name="downftp"></a>
#### <a name="how-to-download-files-using-ftp"></a>Comment télécharger des fichiers via le FTP

1. Dans votre Function App, dans le [portail Azure](https://portal.azure.com), cliquez sur **Fonctionnalités de la plate-forme** et **Propriétés**, puis copiez les valeurs de **Nom d’utilisateur FTP/déploiement**, **Nom d’hôte FTP** et **Nom d’hôte FTPS**.  

    La valeur **Utilisateur FTP/déploiement** doit être entrée telle qu’elle est affichée dans le portail et inclure le nom de l’application afin de fournir le contexte approprié pour le serveur FTP.
   
    ![Obtenir vos informations de déploiement](./media/functions-continuous-deployment/get-deployment-credentials.png)

2. À partir de votre client FTP, utilisez les informations de connexion que vous avez recueillies pour vous connecter à votre application et télécharger les fichiers sources pour vos fonctions.

<a name="downgit"></a>
#### <a name="how-to-download-files-using-a-local-git-repository"></a>Comment télécharger des fichiers via le référentiel Git local

1. Dans votre Function App, dans le [portail Azure](https://portal.azure.com), cliquez sur **Fonctionnalités de la plate-forme** et **Options de déploiement**. 
   
    ![Configurer un déploiement continu](./media/functions-continuous-deployment/setup-deployment.png)
 
2. Puis, dans le panneau **Déploiements**, cliquez sur **Installation**.
 
    ![Configurer un déploiement continu](./media/functions-continuous-deployment/setup-deployment-1.png)
   
2. Dans le panneau **Source de déploiement**, cliquez sur **Référentiel Git local**, puis **OK**.

3. Dans **Fonctionnalités de la plate-forme**, cliquez sur **Propriétés** et notez la valeur de l’URL Git. 
   
    ![Configurer un déploiement continu](./media/functions-continuous-deployment/get-local-git-deployment-url.png)

4. Clonez le référentiel sur votre machine locale à l’aide d’une invite de commande prenant en charge Git ou de votre outil Git favori. La commande de clone Git se présente comme suit :
   
        git clone https://username@my-function-app.scm.azurewebsites.net:443/my-function-app.git

5. Récupérez les fichiers à partir de votre application de fonction sur le clone de votre ordinateur local, comme dans l’exemple suivant :
   
        git pull origin master
   
    Si nécessaire, fournissez vos [informations d’identification de déploiement configurées](#credentials).  

[GitHub]: https://github.com/

