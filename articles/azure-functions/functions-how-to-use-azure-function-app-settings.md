---
title: "Configurer les paramètres Azure Function App | Microsoft Docs"
description: "Apprenez à configurer les paramètres d’application Azure Functions."
services: 
documentationcenter: .net
author: rachelappel
manager: erikre
editor: 
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: article
ms.date: 10/28/2016
ms.author: rachelap
translationtype: Human Translation
ms.sourcegitcommit: 3c18b1929a78137ff3b53b8e084dc35e0c8dcaac
ms.openlocfilehash: 478db825a269beeaa9fbb0afef3488fc7d49d024
ms.lasthandoff: 02/15/2017


---
# <a name="how-to-configure-azure-function-app-settings"></a>Guide pratique pour configurer les paramètres d’application Azure Functions
## <a name="settings-overview"></a>Vue d’ensemble des paramètres
Vous pouvez gérer les paramètres d’application Azure Functions en cliquant sur le lien **Paramètres de l’application de fonctions** dans le coin inférieur gauche du portail. Les paramètres d’application Azure Functions s’appliquent à toutes les fonctions dans l’application.

1. Accédez au [Portail Azure](http://portal.azure.com) et connectez-vous avec votre compte Azure.
2. Cliquez sur **Paramètres de l’application de fonctions** dans le coin inférieur gauche du portail. Cette action révèle un choix d’options de configuration. 

![paramètres d’application Azure Functions](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

## <a name="develop"></a>Développement
### <a name="app-service-editor"></a>Éditeur App Service
L’éditeur App Service est un éditeur avancé intégré au portail. Vous pouvez l’utiliser pour modifier les fichiers de configuration Json et les fichiers de code. L’activation de cette option entraîne l’ouverture d’un onglet distinct du navigateur avec un éditeur de base. Vous pouvez ainsi l’intégrer à Github, exécuter et déboguer du code et modifiez les paramètres de Function App.

![L’éditeur App Service](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

### <a name="application-settings"></a>Paramètres de l’application
Gérez les variables d’environnement, les versions de Framework, le débogage distant, les paramètres d’application, les chaînes de connexion, les documents par défaut, etc. Ces paramètres sont spécifiques à votre application de fonction. 

Pour configurer les paramètres d’application, cliquez sur le lien **Configurer les paramètres de l’application**. 

![Configurer les paramètres d’application](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-settings.png)

### <a name="dev-console"></a>Console de développement
Vous pouvez exécuter des commandes de style DOS avec la console du portail Azure Functions. Les commandes courantes incluent la création de fichiers et de répertoires et la navigation, ainsi que l’exécution de scripts et de fichiers de commandes. 

> [!NOTE]
> Vous pouvez charger des scripts, mais vous devez d’abord configurer un client FTP dans les **Paramètres avancés** d’Azure Functions.
> 
> 

Pour ouvrir la console du portail, cliquez sur **Ouvrir la console de développement**.

![Configurer la taille de la mémoire de l’application de fonction](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

> [!NOTE]
> Dans une console, utiliser une illustration ASCII telle que celle-ci vous donne un air décontracté.
> 
> 

## <a name="deploy"></a>Déployer
### <a name="continuous-integration"></a>Intégration continue
Vous pouvez intégrer votre application de fonction à GitHub et Visual Studio Team Services, entre autres.

1. Cliquez sur le lien **Configurer l’intégration continue**. Cette opération ouvre un volet **Déploiements** comportant des options.
2. Cliquez sur **Configuration** dans le volet **Déploiements** pour faire apparaître un volet **Source de déploiement** avec une option : cliquez sur **Choisir une source** pour afficher les sources disponibles. 
3. Choisissez la source de déploiement qui vous intéresse (Visual Studio Team Services, OneDrive, dépôt Git local, GitHub, Bitbucket, DropBox ou un dépôt externe) en cliquant dessus. 
   
    ![Configurer l’élément de configuration Function App](./media/functions-how-to-use-azure-function-app-settings/configure-function-ci.png)
4. Entrez vos informations d’identification et les informations demandées par les différentes sources de déploiement. Les informations d’identification et les informations demandées peuvent différer légèrement en fonction de la source que vous avez choisie. 

Une fois que vous avez configuré l’intégration continue, le code associé que vous placez dans la source configurée est automatiquement déployé sur cette application de fonction.

### <a name="kudu"></a>Kudu
Kudu vous permet d’accéder aux fonctionnalités d’administration avancées d’une application de fonction.

Pour ouvrir Kudu, cliquez sur **Accéder à Kudu**. Cette action ouvre une nouvelle fenêtre de navigateur présentant l’environnement d’administration Kudu.

> [!NOTE]
> Vous pouvez également lancer **Kudu** en insérant « scm » dans l’URL de votre fonction, comme indiqué ici : ```https://<YourFunctionName>.scm.azurewebsites.net/```
> 
> 

Dans la page web Kudu, vous pouvez afficher et gérer les informations système, les paramètres d’application, les variables d’environnement, les en-têtes HTTP et les variables de serveur, entre autres.

![Configurer Kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

## <a name="manage-app-service-settings"></a>Gérer : les paramètres d’App Service
Gérez votre application de fonctions comme n'importe quelle autre instance App Service. Cette option vous donne accès à tous les paramètres présentés précédemment, ainsi qu’à plusieurs autres.  

Pour ouvrir les paramètres avancés, cliquez sur le lien **Paramètres avancés**. 

![Configurer les paramètres App Service](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-settings.png)

Pour plus d’informations sur la façon de configurer chaque paramètre App Service, consultez [Configure Azure App Service Settings](../app-service-web/web-sites-configure.md) (Configurer les paramètres Azure App Service).

## <a name="manage-cors"></a>Gérer : CORS
En règle générale, pour des raisons de sécurité, les appels à vos hôtes (domaines) provenant de sources externes, tels que les appels Ajax depuis un navigateur, ne sont pas autorisés. Sinon, du code malveillant pourrait être envoyé au backend et exécuté sur celui-ci. L’approche la plus sûre consiste alors mettre sur une liste noire toutes les sources de code, à l’exception de celles des vôtres qui sont approuvées. Vous pouvez configurer les sources desquelles vous acceptez les appels dans des fonctions Azure en configurant le partage des ressources cross-origin (CORS). CORS vous permet de répertorier les domaines qui constituent la source de JavaScript pouvant appeler des fonctions dans votre application de fonction Azure. 

1. Pour configurer CORS, cliquez sur le lien **Configurer CORS**. 
2. Entrez les domaines que vous souhaitez approuver.

![Configurer l’élément CORS de Function App](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

## <a name="manage-authenticationauthorization"></a>Gérer : authentification/autorisation
Pour les fonctions qui utilisent un déclencheur HTTP, vous pouvez exiger l’authentification des appels.

1. Pour configurer l’authentification, cliquez sur le lien **Configurer l’authentification**.
2. Positionnez le bouton **Authentification App Service** sur **Activé**.

![Configurer l’élément de configuration Function App](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

La plupart des fournisseurs d’authentification demandent une clé API/ID de client et une clé secrète ; toutefois, les options Compte Microsoft et Facebook vous permettent également de définir des étendues (informations d’identification d’autorisation spécifiques). Active Directory possède plusieurs paramètres de configuration express ou avancés que vous pouvez définir.

Pour plus d’informations sur la configuration de fournisseurs d’authentification spécifiques, consultez [Azure App Service authentication overview](../app-service/app-service-authentication-overview.md) (Vue d’ensemble de l’authentification Azure App Service).

## <a name="manage-api-definition"></a>Gérer : définition d’API
Permettez aux clients de consommer plus facilement vos fonctions déclenchées via HTTP.

1. Pour définir une API, cliquez sur **Configurer les métadonnées de l’API**. 
2. Entrez l’URL qui pointe vers un fichier json Swagger.

![Configurer l’API de Function App](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-apidef.png)

Pour plus d’informations sur la création de définitions d’API avec Swagger, consultez [Prise en main d’API Apps, d’ASP.NET et de Swagger dans Azure](../app-service-api/app-service-api-dotnet-get-started.md).

## <a name="daily-usage-quota"></a>Quota d’utilisation quotidienne

Azure Functions vous permet de limite de manière prévisible l’utilisation de la plate-forme en définissant un quota de dépense quotidienne. Une fois ce quota atteint, Function App s’arrête. Une instance de Function App arrêtée parce qu’elle a atteint le quota de dépense peut être réactivée en établissant un nouveau quota de dépense quotidienne. L’unité du quota de dépense correspond à l’unité de facturation : Go-s (gigaoctet-secondes), veuillez consulter la page [Tarification d’Azure Functions](http://azure.microsoft.com/pricing/details/functions/) pour en savoir plus sur le modèle de facturation. 

![Configurer la taille de la mémoire de l’application de fonction](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-quota.png)

## <a name="next-steps"></a>Étapes suivantes
[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]


