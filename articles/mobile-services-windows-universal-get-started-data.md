<properties linkid="develop-mobile-tutorials-get-started-with-data-dotnet-vs2013" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services (universal Windows apps)" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Prise en main des données dans Mobile Services (application Windows universelle)

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/" title="Windows Store C#">Windows Store C#</a><a href="/fr-fr/documentation/articles/mobile-services-windows-store-javascript-get-started-data/" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/fr-fr/documentation/articles/mobile-services-windows-phone-get-started-data/" title="Windows Phone">Windows Phone</a><a href="/fr-fr/documentation/articles/mobile-services-ios-get-started-data/" title="iOS">iOS</a><a href="/fr-fr/documentation/articles/mobile-services-android-get-started-data/" title="Android">Android</a><a href="/fr-fr/documentation/articles/mobile-services-html-get-started-data/" title="HTML">HTML</a><a href="/fr-fr/documentation/articles/partner-xamarin-mobile-services-ios-get-started-data/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/fr-fr/documentation/articles/partner-xamarin-mobile-services-android-get-started-data/" title="Xamarin.Android">Xamarin.Android</a></div>

<!---<div class="dev-center-tutorial-subselector"><a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/" title=".NET backend">.NET backend</a> | <a href="/fr-fr/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/"  title="JavaScript backend" class="current">JavaScript backend</a></div>-->

Cette rubrique vous présente l'utilisation d'Azure Mobile Services pour exploiter des données dans une application Windows 8.1 universelle. Dans ce didacticiel, vous allez télécharger un projet Visual Studio pour une application Windows universelle qui stocke les données en mémoire, créer un service mobile, intégrer le service mobile à l'application, puis vous connecter au portail de gestion Azure pour afficher les modifications effectuées sur les données lors de l'exécution de l'application.

> [WACOM.NOTE]Ce didacticiel nécessite Visual Studio Visual Studio 2013 Update 2.

Ce didacticiel vous familiarise avec ces étapes de base :

1.  [Téléchargement d'un projet d'application Windows Store][Téléchargement d'un projet d'application Windows Store]
2.  [Création du service mobile à partir de Visual Studio][Création du service mobile à partir de Visual Studio]
3.  [Ajout d'une table de données pour le stockage et mise à jour de l'application][Ajout d'une table de données pour le stockage et mise à jour de l'application]
4.  [Test de l'application avec Mobile Services][Test de l'application avec Mobile Services]

> [WACOM.NOTE] Pour effectuer ce didacticiel, vous avez besoin d’un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure][Version d'évaluation gratuite d'Azure].

## <a name="download-app"></a>Téléchargement du projet GetStartedWithData

Ce didacticiel est basé sur l'[application GetStartedWithMobileServices][application GetStartedWithMobileServices], qui est un projet d'application Windows Store dans Visual Studio 2013. L'interface utilisateur de cette application est identique à l'application générée par le démarrage rapide de Mobile Services, sauf que les éléments ajoutés sont stockés en local dans la mémoire.

1.  Téléchargez la version C# de l'exemple d'application GetStartedWithMobileServices sur le [site d'exemples de code développeur][application GetStartedWithMobileServices].

    ![][]

2.  Dans Visual Studio 2013, ouvrez le projet téléchargé et examinez le fichier MainPage.xaml.cs.

    Notez que les objets **TodoItem** ajoutés sont stockés dans un **ObservableCollection\<TodoItem\>** en mémoire.

3.  Appuyez sur la touche **F5** pour régénérer le projet et démarrer l'application.

4.  Dans l'application, tapez du texte dans **Insert a TodoItem**, puis cliquez sur **Enregistrer**.

    ![][1]

    Notez que le texte enregistré est affiché dans la deuxième colonne sous **Rechercher et mettre à jour des données**.

## <a name="create-service"></a><span class="short-header">Création du service mobile</span>Création d'un service mobile à partir de Visual Studio

[WACOM.INCLUDE [mobile-services-create-new-service-vs2013][mobile-services-create-new-service-vs2013]]

1.  Dans l'Explorateur de solutions, ouvrez le fichier de code App.xaml.cs et notez que le nouveau champ static a été ajouté à la classe \*\*App\*\*, comme dans l'exemple suivant :

        public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
                    todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
                        "https://todolist.azure-mobile.net/",
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
                

    Ce code fournit un accès à votre nouveau service mobile dans votre application à l'aide d'une instance de la [classe MobileServiceClient][classe MobileServiceClient]. Le client est créé en fournissant l'URI et la clé de l'application du nouveau service mobile. Ce champ static est disponible pour toutes les pages de votre application.

## <a name="add-table"></a><span class="short-header">Ajout d'une nouvelle table</span>Ajout d'une nouvelle table au service mobile et mise à jour de l'application

[WACOM.INCLUDE [mobile-services-create-new-table-vs2013][mobile-services-create-new-table-vs2013]]

1.  Dans le fichier MainPage.xaml.cs, ajoutez les instructions using suivantes ou supprimez leurs marques de commentaire :

        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json;

2.  Dans ce même fichier, remplacez la définition de classe TodoItem par le code suivant :

        public class TodoItem
        {
            public string Id { get; set; }

            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }

            [JsonProperty(PropertyName = "complete")] 
            public bool Complete { get; set; }
        }

    Le **JsonPropertyAttribute** est utilisé pour définir le mappage entre les noms des propriétés dans le type de client et les noms des colonnes dans la table de données sous-jacente.

3.  Mettez en commentaire la ligne définissant la collection des éléments existants, puis annulez les marques de commentaire ou ajoutez les lignes suivantes et remplacez *\<yourClient\>* par le champ `MobileServiceClient` ajouté dans le fichier App.xaml.cs lorsque vous avez connecté votre projet au service mobile :

        private MobileServiceCollection<TodoItem, TodoItem> items;
        private IMobileServiceTable<TodoItem> todoTable = 
            App.<yourClient>.GetTable<TodoItem>();

    Ce code crée une collection de liaisons (éléments) prenant en charge des services mobiles et une classe proxy pour la table de la base de données (todoTable).

4.  Dans la méthode **InsertTodoItem**, supprimez la ligne de code définissant la propriété **TodoItem.Id**, ajoutez le modificateur **async** à la méthode, puis annulez les marques de commentaire dans la ligne de code suivante :

        await todoTable.InsertAsync(todoItem);

    Ce code permet d'insérer un nouvel élément dans la table.

    <div class="dev-callout"><strong>Remarque</strong><p>Des tables sont cr&eacute;&eacute;es avec les colonnes Id, __createdAt, __updatedAt et __version. Lorsqu'un sch&eacute;ma dynamique est activ&eacute;, Mobile Services g&eacute;n&egrave;re automatiquement de nouvelles colonnes bas&eacute;es sur l'objet JSON dans la requ&ecirc;te d'insertion ou de mise &agrave; jour. Pour plus d'informations, consultez la page <a href="http://msdn.microsoft.com/fr-fr/library/windowsazure/jj193175.aspx">Sch&eacute;ma dynamique</a>.</p></div>

5.  Dans la méthode **RefreshTodoItems**, ajoutez le modificateur **async** à la méthode, puis annulez les marques de commentaire sur la ligne de code suivante :

        items = await todoTable.ToCollectionAsync();

    Cela définit la liaison sur la collection d'éléments dans `todoTable`, qui contient tous les objets **TodoItem** renvoyés depuis le service mobile.

6.  Dans la méthode **UpdateCheckedTodoItem**, ajoutez le modificateur **async** à la méthode, puis annulez les marques de commentaire sur la ligne de code suivante :

        await todoTable.UpdateAsync(item);

    Cela permet d'envoyer une mise à jour de l'élément au service mobile.

Maintenant que l'application a été mise à jour pour utiliser Mobile Services pour le stockage principal, le moment est venu de tester l'application avec Mobile Services.

## <a name="test-app"></a><span class="short-header">Test de l'application</span>Test de l'application avec votre nouveau service mobile

1.  Dans Visual Studio, appuyez sur la touche F5 pour exécuter l'application.

2.  Comme auparavant, tapez du texte dans **Insert a TodoItem**, puis cliquez sur **Enregistrer**.

    Cela permet d'envoyer un nouvel élément en tant qu'insertion au service mobile.

3.  Dans le [portail de gestion][portail de gestion], cliquez sur **Mobile Services**, puis sur le service mobile.

4.  Cliquez sur l'onglet **Données**, puis sur **Parcourir**.

    ![][2]

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

-   [Validation et modification des données avec des scripts][Validation et modification des données avec des scripts]<br />
    En savoir plus sur l'utilisation des scripts serveur dans Mobile Services pour valider et modifier les données envoyées à partir de votre application.

-   [Affinage des requêtes à la pagination][Affinage des requêtes à la pagination]<br />
    En savoir plus sur l'utilisation de la pagination dans les requêtes pour contrôler la quantité de données traitées dans une seule requête.

Une fois que vous avez terminé les séries de données, essayez l'un de ces autres didacticiels :

-   [Prise en main de l'authentification][Prise en main de l'authentification]<br />
    En savoir plus sur l'authentification des utilisateurs de votre application.

-   [Prise en main des notifications Push][Prise en main des notifications Push]<br />
    En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

-   [Guide de fonctionnement Mobile Services .NET][Guide de fonctionnement Mobile Services .NET]<br />
    En savoir plus sur l'utilisation de Mobile Services avec .NET.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->

  [Windows Store C#]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/ "Windows Store C#"
  [Windows Store JavaScript]: /fr-fr/documentation/articles/mobile-services-windows-store-javascript-get-started-data/ "Windows Store JavaScript"
  [Windows Phone]: /fr-fr/documentation/articles/mobile-services-windows-phone-get-started-data/ "Windows Phone"
  [iOS]: /fr-fr/documentation/articles/mobile-services-ios-get-started-data/ "iOS"
  [Android]: /fr-fr/documentation/articles/mobile-services-android-get-started-data/ "Android"
  [HTML]: /fr-fr/documentation/articles/mobile-services-html-get-started-data/ "HTML"
  [Xamarin.iOS]: /fr-fr/documentation/articles/partner-xamarin-mobile-services-ios-get-started-data/ "Xamarin.iOS"
  [Xamarin.Android]: /fr-fr/documentation/articles/partner-xamarin-mobile-services-android-get-started-data/ "Xamarin.Android"
  [Téléchargement d'un projet d'application Windows Store]: #download-app
  [Création du service mobile à partir de Visual Studio]: #create-service
  [Ajout d'une table de données pour le stockage et mise à jour de l'application]: #add-table
  [Test de l'application avec Mobile Services]: #test-app
  [Version d'évaluation gratuite d'Azure]: http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=AE564AB28
  [application GetStartedWithMobileServices]: http://go.microsoft.com/fwlink/p/?LinkId=328660
  []: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-data-sample-download-dotnet-vs12.png
  [1]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-quickstart-startup.png
  [mobile-services-create-new-service-vs2013]: ../includes/mobile-services-create-new-service-vs2013.md
  [classe MobileServiceClient]: http://go.microsoft.com/fwlink/p/?LinkId=302030
  [mobile-services-create-new-table-vs2013]: ../includes/mobile-services-create-new-table-vs2013.md
  [Schéma dynamique]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj193175.aspx
  [portail de gestion]: https://manage.windowsazure.com/
  [2]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-todoitem-data-browse.png
  [Validation et modification des données avec des scripts]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
  [Affinage des requêtes à la pagination]: /fr-fr/develop/mobile/tutorials/add-paging-to-data-dotnet
  [Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-dotnet
  [Prise en main des notifications Push]: ../mobile-services-windows-store-dotnet-get-started-push/
  [Guide de fonctionnement Mobile Services .NET]: /fr-fr/develop/mobile/how-to-guides/work-with-net-client-library
