<properties linkid="develop-mobile-tutorials-get-started-with-data-dotnet" urlDisplayName="Get Started with Data" pageTitle="Get started with data - Azure Mobile Services" metaKeywords="" description="Learn how to get started using data with Azure Mobile Services." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Get started with data in Mobile Services using Visual Studio 2012" documentationCenter="Mobile" authors="glenga" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Prise en main des données dans Mobile Services avec Visual Studio 2012

<div class="dev-center-tutorial-selector sublanding">
<a href="/fr-fr/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012" title="Windows Store C#" class="current">Windows Store C#</a><a href="/fr-fr/develop/mobile/tutorials/get-started-with-data-js-vs2012" title="Windows Store JavaScript" class="current">Windows Store JavaScript</a><a href="/fr-fr/develop/mobile/tutorials/get-started-with-data-wp8" title="Windows Phone" class="current">Windows Phone</a><a href="/fr-fr/develop/mobile/tutorials/get-started-with-data-ios" title="iOS" class="current">iOS</a><a href="/fr-fr/develop/mobile/tutorials/get-started-with-data-android" title="Android" class="current">Android</a><a href="/fr-fr/develop/mobile/tutorials/get-started-with-data-html" title="HTML" class="current">HTML</a><a href="/fr-fr/develop/mobile/tutorials/get-started-with-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/fr-fr/develop/mobile/tutorials/get-started-with-data-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a></div>

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Cette rubrique montre comment utiliser Azure Mobile Services pour utiliser les donn&eacute;es dans une application Windows Store. Dans ce didacticiel, vous allez t&eacute;l&eacute;charger un projet Visual Studio&nbsp;2012 pour une application qui stocke les donn&eacute;es en m&eacute;moire, cr&eacute;er un service mobile, int&eacute;grer le service mobile avec l'application, puis vous connecter au portail de gestion Azure pour afficher les modifications effectu&eacute;es sur les donn&eacute;es lors de l'ex&eacute;cution de l'application. Vous pouvez regarder une version vid&eacute;o de ce didacticiel en cliquant sur le clip &agrave; droite.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="label">regarder le didacticiel</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-with-data-windows-store-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Lire la vid&eacute;o</span></a> <span class="time">15:33:00</span></div>

</div>

<div class="dev-callout"><b>Remarque</b>
<p>Ce didacticiel ajoute la fonctionnalit&eacute; Mobile Services &agrave; une application Windows Store cr&eacute;&eacute;e dans Visual&nbsp;Studio&nbsp;2012. Visual&nbsp;Studio&nbsp;2013 inclut de nouvelles fonctionnalit&eacute;s qui facilitent la connexion de votre application Windows&nbsp;Store &agrave; Mobile Services. Pour plus d'informations, consultez la page <a href="/fr-fr/develop/mobile/tutorials/get-started-with-data-dotnet/">Prise en main des donn&eacute;es dans Mobile Services</a>.</p>
</div>

Ce didacticiel vous familiarise avec ces étapes de base :

1.  [Téléchargement d'un projet d'application Windows Store][Téléchargement d'un projet d'application Windows Store]
2.  [Création du service mobile][Création du service mobile]
3.  [Ajout d'une table de données pour le stockage][Ajout d'une table de données pour le stockage]
4.  [Mise à jour de l'application pour utiliser Mobile Services][Mise à jour de l'application pour utiliser Mobile Services]
5.  [Test de l'application avec Mobile Services][Test de l'application avec Mobile Services]

<div class="dev-callout"><strong>Remarque</strong> <p>Pour effectuer ce didacticiel, vous avez besoin d&rsquo;un compte Azure. Si vous ne poss&eacute;dez pas de compte, vous pouvez cr&eacute;er un compte d'&eacute;valuation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Version d'&eacute;valuation gratuite d'Azure</a>.</p></div>

## <a name="download-app"></a><span class="short-header">Téléchargement du projet</span>Téléchargement du projet GetStartedWithData

Ce didacticiel est basé sur l'[application GetStartedWithData][application GetStartedWithData], qui est une application Windows Store. L'interface utilisateur de cette application est identique à l'application générée par le démarrage rapide de Mobile Services, sauf que les éléments ajoutés sont stockés en local dans la mémoire.

1.  Téléchargez la version C# de l'exemple d'application GetStartedWithData sur le [site d'exemples de code développeur][application GetStartedWithData].

    ![][0]

2.  Dans Visual Studio 2012 Express pour Windows 8, ouvrez le projet téléchargé et examinez le fichier MainPage.xaml.cs.

    Notez que les objets **TodoItem** ajoutés sont stockés dans un **ObservableCollection\<TodoItem\>** en mémoire.

3.  Appuyez sur la touche **F5** pour régénérer le projet et démarrer l'application.

4.  Dans l'application, tapez du texte dans **Insert a TodoItem**, puis cliquez sur **Enregistrer**.

    ![][1]

    Notez que le texte enregistré est affiché dans la deuxième colonne sous **Query and update data**.

## <a name="create-service"></a><span class="short-header">Création d'un service mobile</span>Création d'un service mobile dans le portail de gestion

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

## <a name="add-table"></a><span class="short-header">Ajout d'une nouvelle table</span>Ajout d'une nouvelle table au service mobile

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

## <a name="update-app"></a><span class="short-header">Mise à jour de l'application</span>Mise à jour de l'application afin d'utiliser le service mobile pour l'accès aux données

Votre service mobile étant prêt, vous pouvez mettre à jour l'application pour stocker les éléments dans Mobile Services et non dans la collection locale.

1.  Dans l'**Explorateur de solutions** de Visual Studio, cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **Manage NuGet Packages**.

2.  Dans le volet gauche, sélectionnez la catégorie **Online**, sélectionnez **Include Prerelease**, recherchez `WindowsAzure.MobileServices`, cliquez sur **Install** au niveau du package **Azure Mobile Services**, puis acceptez le contrat de licence.

    ![][2]

    La bibliothèque cliente Mobile Services est ajoutée au projet.

3.  Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

4.  Cliquez sur l'onglet **Tableau de bord** et notez la valeur **URL du site**, puis cliquez sur **Gérer les clés** et notez la valeur de **Clé de l'application**.

    ![][3]

    Ces valeurs sont nécessaires pour accéder au service mobile à partir de votre code d'application.

5.  Dans Visual Studio, ouvrez le fichier App.xaml.cs et ajoutez l'instruction `using` suivante ou annulez ses marques de commentaire :

        using Microsoft.WindowsAzure.MobileServices;

6.  Dans le même fichier, annulez les marques de commentaire dans le code qui définit la variable **MobileService**, puis indiquez l'URL et la clé d'application du service mobile dans le constructeur **MobileServiceClient**, dans cet ordre.

        //public static MobileServiceClient MobileService = new MobileServiceClient( 
        //    "AppUrl", 
        //    "AppKey" 
        //); 

    Cela permet de créer une nouvelle instance de **MobileServiceClient**, qui est utilisée pour accéder à votre service mobile.

7.  Dans le fichier MainPage.xaml.cs, ajoutez les instructions `using` suivantes ou supprimez leurs marques de commentaire :

        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json;

8.  Dans ce même fichier, remplacez la définition de classe **TodoItem** par le code suivant :

        public class TodoItem
        {
            public string Id { get; set; }

            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }

            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }
        }

9.  Commentez la ligne qui définit la collection **items** existante, puis annulez les marques de commentaire dans les lignes suivantes :

        private MobileServiceCollection<TodoItem, TodoItem> items;
        private IMobileServiceTable<TodoItem> todoTable = 
            App.MobileService.GetTable<TodoItem>();

    Ce code crée une collection de liaisons (**éléments**) prenant en charge les services mobiles et une classe proxy pour la table de la base de données SQL **TodoItem** (**todoTable**).

10. Dans la méthode **InsertTodoItem**, supprimez la ligne de code qui définit la propriété **TodoItem**.**Id**, ajoutez le modificateur **async** à la méthode, puis annulez les marques de commentaire dans la ligne de code suivante :

        await todoTable.InsertAsync(todoItem);

    Ce code permet d'insérer un nouvel élément dans la table.

11. Dans la méthode **RefreshTodoItems**, ajoutez le modificateur **async** à la méthode, puis annulez les marques de commentaire sur la ligne de code suivante :

        items = await todoTable.ToCollectionAsync();

    Cela définit la liaison sur la collection d'éléments dans todoTable, qui contient tous les objets TodoItem renvoyés depuis le service mobile.

12. Dans la méthode **UpdateCheckedTodoItem**, ajoutez le modificateur **async** à la méthode, puis annulez les marques de commentaire sur la ligne de code suivante :

         await todoTable.UpdateAsync(item);

    Cela permet d'envoyer une mise à jour de l'élément au service mobile.

Maintenant que l'application a été mise à jour pour utiliser Mobile Services pour le stockage principal, le moment est venu de tester l'application avec Mobile Services.

## <a name="test-app"></a><span class="short-header">Test de l'application</span>Test de l'application avec votre nouveau service mobile

1.  Dans Visual Studio, appuyez sur la touche F5 pour exécuter l'application.

2.  Comme auparavant, tapez du texte dans **Insert a TodoItem**, puis cliquez sur **Enregistrer**.

    Cela permet d'envoyer un nouvel élément en tant qu'insertion au service mobile.

3.  Dans le [portail de gestion][portail de gestion], cliquez sur **Mobile Services**, puis sur le service mobile.

4.  Cliquez sur l'onglet **Données**, puis sur **Parcourir**.

    ![][4]

    La table **TodoItem** contient à présent des données, dont les valeurs d'ID ont été générées par Mobile Services, et les colonnes ont été automatiquement ajoutées à la table de manière à correspondre à la classe TodoItem au sein de l'application.

5.  Dans l'application, cochez un des éléments dans la liste, puis revenez à l'onglet Parcourir dans le portail et cliquez sur **Actualiser**.

    Notez que la valeur terminée est passée de **false** à **true**.

6.  Dans le fichier projet MainPage.xaml.cs, remplacez la méthode **RefreshTodoItems** existante par le code suivant qui filtre les éléments terminés :

        private async void RefreshTodoItems()
        {                       
            // This query filters out completed TodoItems. 
            items = await todoTable
               .Where(todoItem => todoItem.Complete == false)
               .ToCollectionAsync();

            ListItems.ItemsSource = items;            
        }

7.  Dans l'application, cochez un autre élément dans la liste, puis cliquez sur le bouton **Actualiser**.

    Notez que l'élément coché n'est plus affiché dans la liste. Chaque actualisation provoque un aller-retour vers le serveur mobile, qui renvoie les données filtrées.

Vous voici parvenu à la fin du didacticiel **Prise en main des données**.

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel a présenté les bases de l'activation d'une application Windows Store pour utiliser les données dans Mobile Services. Ensuite, pensez à suivre l'un des didacticiels suivants basés sur l'application GetStartedWithData que vous avez créée dans ce didacticiel :

-   [Validation et modification des données avec des scripts][Validation et modification des données avec des scripts]

    En savoir plus sur l'utilisation des scripts serveur dans Mobile Services pour valider et modifier les données envoyées à partir de votre application.

-   [Affinage des requêtes à la pagination][Affinage des requêtes à la pagination]

    En savoir plus sur l'utilisation de la pagination dans les requêtes pour contrôler la quantité de données traitées dans une seule requête.

Une fois que vous avez terminé les séries de données, essayez l'un de ces autres didacticiels :

-   [Prise en main de l'authentification][Prise en main de l'authentification]

    En savoir plus sur l'authentification des utilisateurs de votre application.

-   [Prise en main des notifications Push][Prise en main des notifications Push]

    En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

-   [Guide de fonctionnement Mobile Services .NET][Guide de fonctionnement Mobile Services .NET]

    En savoir plus sur l'utilisation de Mobile Services avec .NET.

  [Téléchargement d'un projet d'application Windows Store]: #download-app
  [Création du service mobile]: #create-service
  [Ajout d'une table de données pour le stockage]: #add-table
  [Mise à jour de l'application pour utiliser Mobile Services]: #update-app
  [Test de l'application avec Mobile Services]: #test-app
  [application GetStartedWithData]: http://go.microsoft.com/fwlink/?LinkId=262308
  [0]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-data-sample-download-dotnet.png
  [1]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-quickstart-startup.png
  [2]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-add-nuget-package-dotnet.png
  [3]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-dashboard-tab.png
  [portail de gestion]: https://manage.windowsazure.com/
  [4]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-todoitem-data-browse.png
  [Validation et modification des données avec des scripts]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
  [Affinage des requêtes à la pagination]: /fr-fr/develop/mobile/tutorials/add-paging-to-data-dotnet
  [Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-dotnet
  [Prise en main des notifications Push]: /fr-fr/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012/
  [Guide de fonctionnement Mobile Services .NET]: /fr-fr/develop/mobile/how-to-guides/work-with-net-client-library
