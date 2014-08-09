<properties linkid="develop-mobile-tutorials-get-started-with-data-dotnet" urlDisplayName="Get Started with Data" pageTitle="Get started with data - Azure Mobile Services" metaKeywords="" description="Learn how to get started using data with Azure Mobile Services." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Get started with data in Mobile Services using Visual Studio 2012" documentationCenter="Mobile" authors="" />

Prise en main des données dans Mobile Services avec Visual Studio 2012
======================================================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012 "Windows Store C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/get-started-with-data-js-vs2012 "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-data-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-data-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-data-android "Android")[HTML](/en-us/develop/mobile/tutorials/get-started-with-data-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android "Xamarin.Android")

Cette rubrique montre comment utiliser Azure Mobile Services pour utiliser les données dans une application Windows Store. Dans ce didacticiel, vous allez télécharger un projet Visual Studio 2012 pour une application qui stocke les données en mémoire, créer un service mobile, intégrer le service mobile avec l'application, puis vous connecter au portail de gestion Azure pour afficher les modifications effectuées sur les données lors de l'exécution de l'application. Vous pouvez regarder une version vidéo de ce didacticiel en cliquant sur le clip à droite.

[regarder le didacticiel](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services) [Lire la vidéo](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services) 15:33:00

**Remarque**

Ce didacticiel ajoute la fonctionnalité Mobile Services à une application Windows Store créée dans Visual Studio 2012. Visual Studio 2013 inclut de nouvelles fonctionnalités qui facilitent la connexion de votre application Windows Store à Mobile Services. Pour plus d'informations, consultez la page [Prise en main des données dans Mobile Services](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet/).

Ce didacticiel vous familiarise avec ces étapes de base :

1.  [Téléchargement d'un projet d'application Windows Store](#download-app)
2.  [Création du service mobile](#create-service)
3.  [Ajout d'une table de données pour le stockage](#add-table)
4.  [Mise à jour de l'application pour utiliser Mobile Services](#update-app)
5.  [Test de l'application avec Mobile Services](#test-app)

**Remarque**

Pour effectuer ce didacticiel, vous avez besoin d'un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=AE564AB28).

Téléchargement du projetTéléchargement du projet GetStartedWithData
-------------------------------------------------------------------

Ce didacticiel est basé sur l'[application GetStartedWithData](http://go.microsoft.com/fwlink/?LinkId=262308), qui est une application Windows Store. L'interface utilisateur de cette application est identique à l'application générée par le démarrage rapide de Mobile Services, sauf que les éléments ajoutés sont stockés en local dans la mémoire.

1.  Téléchargez la version C\# de l'exemple d'application GetStartedWithData sur le [site d'exemples de code développeur](http://go.microsoft.com/fwlink/?LinkId=262308).

      ![](./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-data-sample-download-dotnet.png)

2.  Dans Visual Studio 2012 Express pour Windows 8, ouvrez le projet téléchargé et examinez le fichier MainPage.xaml.cs.

      Notez que les objets **TodoItem** ajoutés sont stockés dans un **ObservableCollection<TodoItem>** en mémoire.

3.  Appuyez sur la touche **F5** pour régénérer le projet et démarrer l'application.

4.  Dans l'application, tapez du texte dans **Insert a TodoItem**, puis cliquez sur **Enregistrer**.

      ![](./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-quickstart-startup.png)  

      Notez que le texte enregistré est affiché dans la deuxième colonne sous **Query and update data**.

Création d'un service mobileCréation d'un service mobile dans le portail de gestion
-----------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

Ajout d'une nouvelle tableAjout d'une nouvelle table au service mobile
----------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

Mise à jour de l'applicationMise à jour de l'application afin d'utiliser le service mobile pour l'accès aux données
-------------------------------------------------------------------------------------------------------------------

Votre service mobile étant prêt, vous pouvez mettre à jour l'application pour stocker les éléments dans Mobile Services et non dans la collection locale.

1.  Dans l'**Explorateur de solutions** de Visual Studio, cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **Manage NuGet Packages**.

2.  Dans le volet gauche, sélectionnez la catégorie **Online**, sélectionnez **Include Prerelease**, recherchez `WindowsAzure.MobileServices`, cliquez sur **Install** au niveau du package **Azure Mobile Services**, puis acceptez le contrat de licence.

  ![](./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-add-nuget-package-dotnet.png)

   La bibliothèque cliente Mobile Services est ajoutée au projet.

1.  Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

2.  Cliquez sur l'onglet **Tableau de bord** et notez la valeur **URL du site**, puis cliquez sur **Gérer les clés** et notez la valeur de **Clé de l'application**.

      ![](./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-dashboard-tab.png)

  Ces valeurs sont nécessaires pour accéder au service mobile à partir de votre code d'application.

1.  Dans Visual Studio, ouvrez le fichier App.xaml.cs et ajoutez l'instruction `using` suivante ou annulez ses marques de commentaire :

         using Microsoft.WindowsAzure.MobileServices;

2.  Dans le même fichier, annulez les marques de commentaire dans le code qui définit la variable **MobileService**, puis indiquez l'URL et la clé d'application du service mobile dans le constructeur **MobileServiceClient**, dans cet ordre.

         //public static MobileServiceClient MobileService = new MobileServiceClient( 
         //    "AppUrl", 
         //    "AppKey" 
         //); 

  Cela permet de créer une nouvelle instance de **MobileServiceClient**, qui est utilisée pour accéder à votre service mobile.

1.  Dans le fichier MainPage.xaml.cs, ajoutez les instructions `using` suivantes ou supprimez leurs marques de commentaire :

         using Microsoft.WindowsAzure.MobileServices;
         using Newtonsoft.Json;

2.  Dans ce même fichier, remplacez la définition de classe **TodoItem** par le code suivant :

         public class TodoItem
         {
             public string Id { get; set; }

             [JsonProperty(PropertyName = "text")]
             public string Text { get; set; }

             [JsonProperty(PropertyName = "complete")]
             public bool Complete { get; set; }
         }

3.  Commentez la ligne qui définit la collection **items** existante, puis annulez les marques de commentaire dans les lignes suivantes :

         private MobileServiceCollection<TodoItem, TodoItem> items;
         private IMobileServiceTable<TodoItem> todoTable = 
             App.MobileService.GetTable<TodoItem>();

      Ce code crée une collection de liaisons (**éléments**) prenant en charge les services mobiles et une classe proxy pour la table de la base de données SQL **TodoItem**(**todoTable**). 

4.  Dans la méthode **InsertTodoItem**, supprimez la ligne de code qui définit la propriété **TodoItem**.**Id**, ajoutez le modificateur **async** à la méthode, puis annulez les marques de commentaire dans la ligne de code suivante :

        await todoTable.InsertAsync(todoItem);

   Ce code permet d'insérer un nouvel élément dans la table.

1.  Dans la méthode **RefreshTodoItems**, ajoutez le modificateur **async** à la méthode, puis annulez les marques de commentaire sur la ligne de code suivante :

         items = await todoTable.ToCollectionAsync();

      Cela définit la liaison sur la collection d'éléments dans todoTable, qui contient tous les objets TodoItem renvoyés depuis le service mobile. 

2.  Dans la méthode **UpdateCheckedTodoItem**, ajoutez le modificateur **async** à la méthode, puis annulez les marques de commentaire sur la ligne de code suivante :

          await todoTable.UpdateAsync(item);

      Cela permet d'envoyer une mise à jour de l'élément au service mobile.

Maintenant que l'application a été mise à jour pour utiliser Mobile Services pour le stockage principal, le moment est venu de tester l'application avec Mobile Services.

Test de l'applicationTest de l'application avec votre nouveau service mobile
----------------------------------------------------------------------------

1.  Dans Visual Studio, appuyez sur la touche F5 pour exécuter l'application.

2.  Comme auparavant, tapez du texte dans **Insert a TodoItem**, puis cliquez sur **Enregistrer**.

      Cela permet d'envoyer un nouvel élément en tant qu'insertion au service mobile.

3.  Dans le [portail de gestion](https://manage.windowsazure.com/), cliquez sur **Mobile Services**, puis sur le service mobile.

4.  Cliquez sur l'onglet **Données**, puis sur **Parcourir**.

      ![](./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-todoitem-data-browse.png)
          
      La table **TodoItem** contient à présent des données, dont les valeurs d'ID ont été générées par Mobile Services, et les colonnes ont été automatiquement ajoutées à la table de manière à correspondre à la classe TodoItem au sein de l'application.

5.  Dans l'application, cochez un des éléments dans la liste, puis revenez à l'onglet Parcourir dans le portail et cliquez sur **Actualiser**.

  Notez que la valeur terminée est passée de **false** à **true**.

1.  Dans le fichier projet MainPage.xaml.cs, remplacez la méthode **RefreshTodoItems** existante par le code suivant qui filtre les éléments terminés :

         private async void RefreshTodoItems()
         {                       
             // Cette requête élimine par filtrage les éléments TodoItems terminés. 
             items = await todoTable
                .Where(todoItem => todoItem.Complete == false)
                .ToCollectionAsync();

             ListItems.ItemsSource = items;            
         }

2.  Dans l'application, cochez un autre élément dans la liste, puis cliquez sur le bouton **Actualiser**.

      Notez que l'élément coché n'est plus affiché dans la liste. Chaque actualisation provoque un aller-retour vers le serveur mobile, qui renvoie les données filtrées.

Cela conclut le didacticiel **Prise en main des données**.

Étapes suivantes
----------------

Ce didacticiel a présenté les bases de l'activation d'une application Windows Store pour utiliser les données dans Mobile Services. Ensuite, pensez à suivre l'un des didacticiels suivants basés sur l'application GetStartedWithData que vous avez créée dans ce didacticiel :

-   [Validation et modification des données avec des scripts](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet)
    <br/>En savoir plus sur l'utilisation des scripts serveur dans Mobile Services pour valider et modifier les données envoyées à partir de votre application.

-   [Affinage des requêtes à la pagination](/en-us/develop/mobile/tutorials/add-paging-to-data-dotnet)
    <br/>En savoir plus sur l'utilisation de la pagination dans les requêtes pour contrôler la quantité de données traitées dans une seule requête.

Une fois que vous avez terminé les séries de données, essayez l'un de ces autres didacticiels :

-   [Prise en main de l'authentification](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet)
    <br/>En savoir plus sur l'authentification des utilisateurs de votre application.

-   [Prise en main des notifications Push](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012/)
    <br/>En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

-   [Guide de fonctionnement Mobile Services .NET](/en-us/develop/mobile/how-to-guides/work-with-net-client-library)
    <br/>En savoir plus sur l'utilisation de Mobile Services avec .NET.

<!-- Anchors. -->

[Get the Windows Store app]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-quickstart-startup.png




[7]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-add-nuget-package-dotnet.png
[8]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-dashboard-tab.png
[9]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-todoitem-data-browse.png
[10]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-data-sample-download-dotnet.png


<!-- URLs. -->
[Validate and modify data with scripts]: /en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Refine queries with paging]: /en-us/develop/mobile/tutorials/add-paging-to-data-dotnet
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012/
[JavaScript and HTML]: /en-us/develop/mobile/tutorials/get-started-with-data-js-vs2012/

[Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Developer Code Samples site]:  http://go.microsoft.com/fwlink/?LinkId=262308
[Mobile Services .NET How-to Conceptual Reference]: /en-us/develop/mobile/how-to-guides/work-with-net-client-library
