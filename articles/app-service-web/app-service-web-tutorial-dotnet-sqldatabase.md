---
title: "Création d’une application ASP.NET dans Azure avec SQL Database | Microsoft Docs"
description: "Découvrez comment faire fonctionner une application ASP.NET dans Azure en établissant une connexion à une instance SQL Database."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 03c584f1-a93c-4e3d-ac1b-c82b50c75d3e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 04/07/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 566a8d7fc595975e065c5a8a42c7f7c94997821b
ms.lasthandoff: 04/18/2017


---
# <a name="create-an-aspnet-app-in-azure-with-sql-database"></a>Création d’une application ASP.NET dans Azure avec SQL Database

Ce didacticiel vous montre comment développer dans Azure une application web ASP.NET axée sur les données, comment la connecter à Azure SQL Database et comment activer vos fonctionnalités orientées données. Lorsque vous aurez terminé, vous disposerez d’une application ASP.NET exécutée dans [Azure App Service](../app-service/app-service-value-prop-what-is.md) et connectée à SQL Database.

![Application ASP.NET publiée dans une application web Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

## <a name="before-you-begin"></a>Avant de commencer

Avant d’exécuter cet exemple, [téléchargez et installez l’édition communautaire gratuite de Visual Studio 2017](https://www.visualstudio.com/downloads/). Veillez à activer **le développement Azure** lors de l’installation de Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="step-1---download-the-sample"></a>Étape 1 : télécharger l’exemple
Dans cette étape, vous allez télécharger un exemple d’application ASP.NET.

### <a name="get-the-sample-project"></a>Récupération de l’exemple de projet

Cliquez [ici](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/archive/master.zip) pour télécharger les exemples de projet.

Extrayez le fichier `dotnet-sqldb-tutorial-master.zip` téléchargé dans un répertoire de travail.

> [!TIP]
> Vous pouvez obtenir l’exemple de projet en clonant le référentiel GitHub :
>
> ```bash
> git clone https://github.com/Azure-Samples/dotnet-sqldb-tutorial.git
> ```
>
>

Cet exemple de projet contient une simple application CRUD (Create-Read-Update-Delete) [ASP.NET MVC](https://www.asp.net/mvc) basée sur [Entity Framework Code First](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application).

### <a name="run-the-application"></a>Exécution de l'application

À partir du répertoire extrait, lancez `dotnet-sqldb-tutorial-master\DotNetAppSqlDb.sln` dans Visual Studio 2017.

Une fois l’exemple de solution ouvert, tapez `F5` pour l’exécuter dans le navigateur.

Une simple liste des tâches à effectuer devrait s’afficher dans la page d’accueil. Essayez d’ajouter plusieurs actions à la liste vide.

![Boîte de dialogue New ASP.NET Project](./media/app-service-web-tutorial-dotnet-sqldatabase/local-app-in-browser.png)

Votre contexte de base de données utilise une chaîne de connexion appelée `MyDbConnection`. Cette chaîne de connexion est définie dans `Web.config` et référencée dans `Models\MyDatabaseContext.cs`. Vous n’aurez besoin que du nom de la chaîne de connexion plus tard lors de la connexion de votre application web Azure à Azure SQL Database. 

## <a name="step-2---publish-to-azure-with-sql-database"></a>Étape 2 : publier dans Azure avec SQL Database

Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet **DotNetAppSqlDb**, puis sélectionnez **Publier**.

![Publier à partir de l’Explorateur de solutions](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Assurez-vous que **Microsoft Azure App Service** est sélectionné et cliquez sur **Publier**.

![Publier à partir de la page de présentation du projet](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-to-app-service.png)

Cette opération ouvre la boîte de dialogue **Créer App Service** qui vous permet de créer toutes les ressources Azure dont vous avez besoin pour exécuter votre application web ASP.NET dans Azure.

### <a name="sign-in-to-azure"></a>Connexion à Azure

Dans la boîte de dialogue **Créer App Service**, cliquez sur **Ajouter un compte** puis connectez-vous à votre abonnement Azure. Si vous êtes déjà connecté à un compte Microsoft, assurez-vous que le compte conserve votre abonnement Azure. Si le compte Microsoft auquel vous êtes connecté ne comporte pas votre abonnement Azure, cliquez dessus pour ajouter le compte approprié.
   
![Connexion à Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/sign-in-azure.png)

Une fois connecté, vous êtes prêt à créer toutes les ressources dont vous avez besoin pour votre application web Azure dans cette boîte de dialogue.

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Dans un premier temps, vous avez besoin d’un _groupe de ressources_. 

> [!NOTE] 
> Un groupe de ressources est un conteneur logique dans lequel les ressources Azure comme les applications web, les bases de données et les comptes de stockage sont déployées et gérées.
>
>

À côté de **Groupe de ressources**, cliquez sur **Nouveau**.

Nommez votre groupe de ressources **myResourceGroup** et cliquez sur **OK**.

### <a name="create-an-app-service-plan"></a>Créer un plan App Service

Votre application web Azure nécessite également un _plan App Service_. 

> [!NOTE]
> Un plan App Service représente la collection des ressources physiques utilisées pour héberger vos applications. Toutes les applications affectées à un plan App Service partagent les ressources qu’il définit, ce qui vous permet de réduire les coûts lors de l’hébergement de plusieurs applications. 
>
> Les plans App Service définissent :
>
> - Région (Europe du Nord, États-Unis de l’Est, Sud-Est asiatique)
> - Taille d’instance (« Petit », « Moyen » ou « Grand »)
> - Comptage (une, deux ou trois instances, etc.) 
> - Référence (SKU) (gratuit, partagé, basique, standard, premium)
>
>

À côté de **Plan App Service**, cliquez sur **Nouveau**. 

Dans la boîte de dialogue **Configurer plan App Service** configurez le nouveau plan App Service avec les paramètres suivants :

- **Plan App Service** : type **myAppServicePlan**. 
- **Emplacement** : choisissez **Europe de l’Ouest**, ou toute autre région.
- **Taille** : choisissez **Gratuit**, ou tout autre [niveau tarifaire](https://azure.microsoft.com/pricing/details/app-service/).

Cliquez sur **OK**.

![Créer un plan App Service](./media/app-service-web-tutorial-dotnet-sqldatabase/configure-app-service-plan.png)

### <a name="configure-the-web-app-name"></a>Configurer le nom de l’application web

Sous **Nom de l’application web**, entrez un nom d’application unique. Ce nom sera utilisé en partie dans le nom DNS par défaut de votre application (`<app_name>.azurewebsites.net`), il doit donc être unique sur l’ensemble des applications dans Azure. Vous pouvez ultérieurement mapper un nom de domaine personnalisé vers votre application web avant de l’exposer à vos utilisateurs.

Vous pouvez également accepter le nom généré automatiquement, qui est déjà unique.

Pour préparer l’étape suivante, cliquez sur **Explorer des services Azure supplémentaires**.

![Configurer le nom de l’application web](./media/app-service-web-tutorial-dotnet-sqldatabase/web-app-name.png)

### <a name="create-a-sql-server-instance"></a>Créer une instance SQL Server

Dans l’onglet **Services**, cliquez sur l’icône **+** en regard de **SQL Database**. 

Dans la boîte de dialogue **Configurer la base de données SQL**, cliquez sur **Nouveau** en regard de **SQL Server**. 

Dans **Nom du serveur**, entrez un nom unique. Ce nom sera utilisé en partie dans le nom DNS par défaut de votre serveur de base de données (`<server_name>.database.windows.net`) et doit donc être unique sur l’ensemble des instances SQL Server dans Azure. 

Configurez le reste des champs à votre guise, puis cliquez sur **OK**.

![Créer une instance SQL Server](./media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database-server.png)

### <a name="configure-the-sql-database"></a>Configurer l’instance SQL Database

Dans **Nom de la base de données**, tapez `myToDoAppDb` ou entrez le nom de votre choix.

Dans **Nom de la chaîne de connexion**, tapez `MyDbConnection`. Ce nom doit correspondre à la chaîne de connexion référencée dans `Models\MyDatabaseContext.cs`.

![Configurer SQL Database](./media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database.png)

### <a name="create-and-publish-the-web-app"></a>Créer et publier l’application web

Cliquez sur **Create**. 

Une fois que l’Assistant a terminé de créer les ressources Azure, il publie automatiquement votre application ASP.NET dans Azure pour la première fois, puis lance l’application web Azure publiée dans votre navigateur par défaut.

Essayez d’ajouter plusieurs actions à la liste vide.

![Application ASP.NET publiée dans une application web Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

Félicitations ! Votre application ASP.NET orientée données s’exécute dans Azure App Service.

## <a name="step-3---access-the-sql-database-locally"></a>Étape 3 : accéder à SQL Database en local

Visual Studio vous permet d’explorer et gérer facilement votre nouvelle instance SQL Database dans **l’Explorateur d’objets SQL Server**.

### <a name="create-a-database-connection"></a>Créer une connexion à la base de données

Ouvrez **l’Explorateur d’objets SQL Server** en tapant `Ctrl`+`\`, `Ctrl`+`S`.

En haut de **l’Explorateur d’objets SQL Server**, cliquez sur le bouton **Ajouter SQL Server**.

### <a name="configure-the-database-connection"></a>Configurer la connexion à la base de données

Dans la boîte de dialogue **Se connecter**, développez le nœud **Azure**. Toutes vos instances SQL Database dans Azure sont répertoriées ici.

Sélectionnez l’instance SQL Database que vous avez créée précédemment. La connexion que vous avez utilisée auparavant est automatiquement renseignée en bas de la page.

Entrez le mot de passe de l’administrateur de base de données que vous avez utilisé précédemment, puis cliquez sur **Se connecter**.

![Configurer la connexion à la base de données à partir de Visual Studio](./media/app-service-web-tutorial-dotnet-sqldatabase/connect-to-sql-database.png)

### <a name="allow-client-connection-from-your-computer"></a>Autoriser une connexion client à partir de votre ordinateur

La boîte de dialogue **Créer une nouvelle règle de pare-feu** s’ouvre. Par défaut, votre instance SQL Server autorise uniquement les connexions provenant des services Azure, tels que votre application web Azure. Pour vous connecter à votre base de données directement à partir de Visual Studio, vous devez créer une règle de pare-feu dans l’instance SQL Server pour autoriser l’adresse IP publique de votre ordinateur local.

Cette étape est très simple à réaliser dans Visual Studio. La boîte de dialogue contient déjà l’adresse IP de votre ordinateur.

Assurez-vous que la case **Ajouter mon adresse IP cliente** est cochée, puis cliquez sur **OK**. 

![Définir le pare-feu pour l’instance SQL Server](./media/app-service-web-tutorial-dotnet-sqldatabase/sql-set-firewall.png)

Une fois que Visual Studio a terminé de créer le paramètre de pare-feu pour votre instance SQL Server, votre connexion s’affiche dans **l’Explorateur d’objets SQL Server**.

Ici, vous pouvez effectuer les opérations de base de données les plus courantes, par exemple exécuter des requêtes, créer des vues et des procédures stockées, etc. L’exemple suivant indique comment afficher les données de la base de données. 

![Explorer les objets SQL Database](./media/app-service-web-tutorial-dotnet-sqldatabase/explore-sql-database.png)

## <a name="step-4---update-app-with-code-first-migrations"></a>Étape 4 : mettre à jour l’application avec les Code First Migrations

Dans cette étape, vous allez utiliser des Code First Migrations dans Entity Framework pour apporter une modification à votre schéma de base de données et la publier sur Azure.

### <a name="update-your-data-model"></a>Mettre à jour votre modèle de données

Dans l’éditeur de code, ouvrez `Models\Todo.cs`. Ajoutez la propriété suivante à la classe `ToDo` :

```csharp
public bool Done { get; set; }
```

### <a name="run-code-first-migrations-locally"></a>Exécuter la fonction Code First Migrations en local

Ensuite, exécutez quelques commandes pour mettre à jour votre base de données localdb. 

Dans le menu **Outils**, cliquez sur **Gestionnaire de package NuGet** > **Console du Gestionnaire de package**. La console s’ouvre généralement dans la fenêtre inférieure.

Activez Code First Migrations comme suit :

```PowerShell
Enable-Migrations
```

Ajoutez une migration de la manière suivante :

```PowerShell
Add-Migration AddProperty
```

Mettez à jour la base de données localdb comme suit :

```PowerShell
Update-Database
```

Tester vos modifications en exécutant l’application avec `F5`.

Si l’application se charge sans erreur, cela signifie que la fonction Code First Migrations a réussi. Votre page garde cependant le même aspect car votre logique d’application n’utilise pas encore cette nouvelle propriété. 

### <a name="use-the-new-property"></a>Utiliser la nouvelle propriété

Apportez maintenant quelques modifications à votre code pour utiliser la propriété `Done`. Pour plus de simplicité dans ce didacticiel, vous allez uniquement modifier les vues `Index` et `Create` pour voir la propriété en action.

Ouvrez `Controllers\TodosController.cs`.

Recherchez la méthode `Create()` et ajoutez `Done` à la liste des propriétés dans l’attribut `Bind`. Lorsque vous avez terminé, la signature de votre méthode `Create()` doit ressembler à ceci :

```csharp
public ActionResult Create([Bind(Include = "id,Description,CreatedDate,Done")] Todo todo)
```

Ouvrez `Views\Todos\Create.cshtml`.

Dans le code Razor, vous devriez voir une balise `<div class="form-group">` qui utilise `model.Description` et une autre balise `<div class="form-group">` qui utilise `model.CreatedDate`. Immédiatement après ces deux balises, ajoutez une autre balise `<div class="form-group">` qui utilise `model.Done`, comme ceci :

```csharp
<div class="form-group">
    @Html.LabelFor(model => model.Done, htmlAttributes: new { @class = "control-label col-md-2" })
    <div class="col-md-10">
        <div class="checkbox">
            @Html.EditorFor(model => model.Done)
            @Html.ValidationMessageFor(model => model.Done, "", new { @class = "text-danger" })
        </div>
    </div>
</div>
```

Ouvrez `Views\Todos\Index.cshtml`.

Recherchez la balise `<th></th>` vide. Juste au-dessus de cette balise, ajoutez le code Razor suivant :

```csharp
<th>
    @Html.DisplayNameFor(model => model.Done)
</th>
```

Recherchez la balise `<td>` contenant les méthodes d’assistance `Html.ActionLink()`. Juste au-dessus de cette balise, ajoutez le code Razor suivant :

```csharp
<td>
    @Html.DisplayFor(modelItem => item.Done)
</td>
```

Voilà tout ce que vous avez à faire pour voir les modifications dans les vues `Index` et `Create`. 

Tapez de nouveau `F5` pour exécuter l’application.

Vous devriez maintenant être en mesure d’ajouter une tâche et de cocher la case **Terminé**. Cette tâche devrait ensuite apparaître dans votre page d’accueil comme un élément terminé. N’oubliez pas que vous ne pouvez rien faire d’autre pour l’instant, car vous n’avez pas modifié la vue `Edit`.

### <a name="enable-code-first-migrations-in-azure"></a>Activer Code First Migrations dans Azure

Maintenant que votre changement de code fonctionne, y compris la migration de la base de données, vous allez le publiez dans votre application web Azure et mettre également à jour votre instance SQL Database avec Code First Migrations.

Comme précédemment, cliquez avec le bouton droit sur votre projet et sélectionnez **Publier**.

Cliquez sur **Paramètres** pour ouvrir l’Assistant Publication.

![Ouvrir les paramètres de publication](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-settings.png)

Dans l’assistant, cliquez sur **Suivant**.

Assurez-vous que la chaîne de connexion pour votre instance SQL Database est renseignée dans **MyDatabaseContext (MyDbConnection)**. Vous devrez peut-être sélectionner la base de données **myToDoAppDb** dans la liste déroulante. 

Sélectionnez **Exécuter les migrations Code First (s’exécute au démarrage de l’application)**, puis cliquez sur **Enregistrer**.

![Activer Code First Migrations dans l’application web Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/enable-migrations.png)

### <a name="publish-your-changes"></a>Publier vos modifications

Maintenant que vous avez activé Code First Migrations dans votre application web Azure, il vous suffit simplement de publier vos modifications du code.

Dans la page de publication, cliquez sur **Publier**.

Essayez à nouveau de créer de nouvelles tâches et de sélectionner **Terminé** : elles doivent apparaître dans votre page d’accueil comme un élément terminé.

![Application web Azure après l’activation de Code First Migration](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

> [!NOTE]
> Notez que toutes les tâches existantes sont toujours affichées. Lorsque vous republiez votre application ASP.NET, les données existantes dans votre instance SQL Database ne sont pas perdues. En outre, Code First Migrations modifie uniquement le schéma de données, sans toucher à vos données existantes.
>
>

## <a name="step-6---stream-application-logs"></a>Étape 6 : diffuser les journaux d’applications

Vous pouvez diffuser des messages de suivi directement entre votre application web Azure et Visual Studio.

Ouvrez `Controllers\TodosController.cs`.

Notez que chaque action démarre avec une méthode `Trace.WriteLine()`. Ce code est ajouté pour vous montrer à quel point il est facile d’ajouter des messages de trace à votre application web Azure.

### <a name="open-server-explorer"></a>Ouvrir l’Explorateur de serveurs

Vous pouvez configurer la journalisation pour votre application web Azure dans **l’Explorateur de serveurs**. 

Pour l’ouvrir, tapez `Ctrl`+`Alt`+`S`.

### <a name="enable-log-streaming"></a>Activer la diffusion de journaux

Dans **l’Explorateur de serveurs**, développez **Azure** > **App Service**.

Développez le groupe de ressources **myResourceGroup** que vous avez créé lorsque vous avez créé l’application web Azure.

Cliquez avec le bouton droit sur votre application web Azure et sélectionnez **Afficher les journaux de streaming**.

![Activer la diffusion de journaux](./media/app-service-web-tutorial-dotnet-sqldatabase/stream-logs.png)

Les journaux sont maintenant transmis à la fenêtre **Sortie**. 

![Diffusion des journaux dans la fenêtre Sortie](./media/app-service-web-tutorial-dotnet-sqldatabase/log-streaming-pane.png)

Vous ne pourrez cependant pas visualiser les messages de suivi à ce stade. La raison est simple : lorsque vous sélectionnez **Afficher les journaux de streaming**, votre application web Azure définit le niveau de suivi sur `Error`, ce qui enregistre uniquement les événements d’erreur (avec la méthode `Trace.TraceError()`).

### <a name="change-trace-levels"></a>Modifier les niveaux de suivi

Pour modifier les niveaux de suivi pour générer d’autres messages de suivi, revenez à **l’Explorateur de serveurs**.

Cliquez de nouveau avec le bouton droit sur votre application web Azure et sélectionnez **Paramètres**.

Dans la liste déroulante **Journal des applications (Système de fichiers)**, sélectionnez **Détaillé**. Cliquez sur **Save**.

![Définir le niveau de suivi sur Détaillé](./media/app-service-web-tutorial-dotnet-sqldatabase/trace-level-verbose.png)

> [!TIP]
> Vous pouvez expérimenter différents niveaux de suivi pour connaître les types de messages qui s’affichent pour chaque niveau. Par exemple, le niveau **Informations** inclut tous les journaux créés par `Trace.TraceInformation()`, `Trace.TraceWarning()`, et `Trace.TraceError()`, mais pas les journaux créés par `Trace.WriteLine()`.
>
>

Dans votre navigateur, essayez de cliquer autour de l’application de la liste des tâches dans Azure. Les messages de suivi sont maintenant diffusés vers la fenêtre **Sortie** dans Visual Studio.

```
Application: 2017-04-06T23:30:41  PID[8132] Verbose     GET /Todos/Index
Application: 2017-04-06T23:30:43  PID[8132] Verbose     GET /Todos/Create
Application: 2017-04-06T23:30:53  PID[8132] Verbose     POST /Todos/Create
Application: 2017-04-06T23:30:54  PID[8132] Verbose     GET /Todos/Index
```

### <a name="stop-log-streaming"></a>Arrêter la diffusion de journaux

Pour arrêter le service de diffusion de journaux, cliquez sur le bouton **Arrêter l’analyse** situé dans la fenêtre **Sortie**.

![Arrêter la diffusion de journaux](./media/app-service-web-tutorial-dotnet-sqldatabase/stop-streaming.png)

## <a name="step-7---manage-your-azure-web-app"></a>Étape 7 : gérer votre application web Azure

Accédez au portail Azure pour voir l’application web que vous avez créée. 

Pour ce faire, connectez-vous au portail : [https://portal.azure.com](https://portal.azure.com).

Dans le menu de gauche, cliquez sur **App Service**, puis cliquez sur le nom de votre application web Azure.

![Navigation au sein du portail pour accéder à l’application web Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/access-portal.png)

Vous accédez au _panneau_ de votre application web (une page du portail qui s’ouvre horizontalement). 

Par défaut, le panneau de votre application web affiche la page **Présentation**. Cette page propose un aperçu de votre application. Ici, vous pouvez également effectuer des tâches de gestion de base (parcourir, arrêter, démarrer, redémarrer et supprimer des éléments, par exemple). Les onglets figurant sur le côté gauche du panneau affichent les différentes pages de configuration que vous pouvez ouvrir. 

![Panneau App Service sur le portail Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/web-app-blade.png)

Ces onglets affichent les nombreuses fonctionnalités exceptionnelles que vous pouvez ajouter à votre application web. La liste suivante fournit quelques exemples des possibilités :

- Mapper un nom DNS personnalisé
- Lier un certificat SSL personnalisé
- Configurer le déploiement continu
- Montée en puissance et augmentation de la taille des instances
- Ajouter une authentification utilisateur

## <a name="next-steps"></a>Étapes suivantes

Explorez les [scripts PowerShell des applications web](app-service-powershell-samples.md) créés au préalable.
