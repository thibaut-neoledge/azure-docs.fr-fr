---
title: "Clonage d’application web à l’aide du portail Azure"
description: "Découvrez comment cloner vos applications web vers de nouvelles applications web à l’aide du portail Azure."
services: app-service\web
documentationcenter: 
author: ahmedelnably
manager: stefsch
editor: 
ms.assetid: 20b0ae4e-67e8-4bae-9d74-8a24dc445cce
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2016
ms.author: ahmedelnably
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7b0daaee1f9017ade049909341100a4d0e5f4a59


---
# <a name="azure-app-service-app-cloning-using-azure-portal"></a>Clonage de l’application Azure App Service à l’aide du portail Azure
La fonctionnalité de clonage dans [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) vous permet de cloner facilement des applications web existantes vers une application nouvellement créée dans une autre région ou dans la même région. Les utilisateurs peuvent ainsi déployer de nombreuses applications dans différentes régions.

Le clonage d’application n’est actuellement pris en charge que pour les plans de service d’application de niveau Premium. Cette nouvelle fonctionnalité utilise les mêmes limitations que la fonctionnalité de sauvegarde des applications web. Pour plus d’informations, consultez l’article [Sauvegarder une application web dans Azure App Service](web-sites-backup.md).

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="cloning-an-existing-app"></a>Clonage d’une application existante
L’application web doit être exécutée en mode **Premium** pour vous permettre de créer un clone de l’application web.

1. Dans le [portail Azure](https://portal.azure.com/), ouvrez le panneau de votre application web.
2. Cliquez sur **Outils**. Ensuite, dans le panneau **Outils**, cliquez sur **Cloner l’application**.
   
    ![][1]
   
   > [!NOTE]
   > Si l’application web n’est pas en mode **Premium** , vous recevez un message indiquant les modes pris en charge pour le clonage d’applications. À ce stade, vous avez la possibilité de sélectionner **Mise à niveau**.
   > 
   > 
3. Dans le panneau **Cloner l’application** , entrez un nom pour la nouvelle application web, le groupe de ressources et le plan App Service. L’utilisateur pourra également choisir de cloner plusieurs paramètres de l’application web source.
   
    ![][2]
4. Après avoir cliqué sur **Créer** , la plateforme commence à travailler sur la création d’un clone de l’application web source.

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Clonage d’une application existante vers un App Service Environment
Dans le panneau **Cloner l’application** , le client pourra choisir un pool d’applications dans un environnement App Service existant.

## <a name="current-restrictions"></a>Restrictions actuelles
Cette fonctionnalité en est actuellement à sa version préliminaire. Nous nous efforçons d’ajouter de nouvelles fonctionnalités au fil du temps. La liste suivante répertorie les restrictions connues de la prise en charge actuelle du clonage d’application dans le portail Azure :

* Les paramètres d’Azure Traffic Manager ne sont pas clonés.
* Les paramètres de mise à l’échelle automatique ne sont pas clonés.
* Les paramètres de planification de sauvegarde ne sont pas clonés.
* Les paramètres de réseau virtuel ne sont pas clonés.
* Application Insights n’est pas automatiquement configuré dans l’application web de destination
* Les paramètres d’authentification simple ne sont pas clonés.
* Les paramètres d’extension Kudu ne sont pas clonés.
* Les règles TiP ne sont pas clonées.
* Les contenus de la base de données ne sont pas clonés.

### <a name="references"></a>Références
* [Clonage d’application web à l’aide de PowerShell](app-service-web-app-cloning.md)
* [Sauvegarde d’une application web dans Azure App Service](web-sites-backup.md)
* [Comment créer un environnement App Service](app-service-web-how-to-create-an-app-service-environment.md)
* [Créer une application web dans un environnement App Service](app-service-web-how-to-create-a-web-app-in-an-ase.md)
* [Présentation de l'environnement App Service](app-service-app-service-environment-intro.md)

<!--Image references-->
[1]: ./media/app-service-web-app-cloning-portal/CloningBlade.png
[2]: ./media/app-service-web-app-cloning-portal/CloneSettings.png



<!--HONumber=Nov16_HO3-->


