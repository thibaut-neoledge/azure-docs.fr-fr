<properties 
	pageTitle="Ajouter Mobile Services à une application existante (WP8) - Azure Mobile Services" 
	description="Learn how to get started using data from your Azure Mobile Services Windows Phone 8 app." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="glenga"/>


# Ajout de services mobiles à une application existante

[AZURE.INCLUDE [mobile-services-selector-get-started-data-legacy](../includes/mobile-services-selector-get-started-data-legacy.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>Cette rubrique montre comment utiliser Azure Mobile Services pour exploiter des données dans une application Windows Phone&#160;8. Dans ce didacticiel, vous allez télécharger une application qui stocke les données en mémoire, créer un service mobile, intégrer le service mobile à l'application, puis vous connecter au portail de gestion Azure pour afficher les modifications apportées aux données lors de l'exécution de l'application.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkID=298628" target="_blank" class="label">regarder le didacticiel</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-get-started-data-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkID=298628" target="_blank" class="dev-onpage-video"><span class="icon">Lire la vidéo</span></a> <span class="time">12:54:00</span></div>
</div>

Ce didacticiel vous familiarise avec ces étapes de base :

1. [Télécharger le projet d'application Windows Phone 8] 
2. [Créer le service mobile]
3. [Ajouter une table de données pour le stockage]
4. [Mettre à jour l'application pour utiliser Mobile Services]
5. [Tester l'application avec Mobile Services]

Ce didacticiel requiert Visual Studio 2012 Express pour Windows Phone 8 et le [Kit de développement logiciel (SDK) Windows Phone 8] s'exécutant sous Windows 8. Pour suivre ce didacticiel afin de créer une application Windows Phone 8.1, vous devez utiliser Visual Studio 2013 Update 2 ou une version ultérieure.

>[AZURE.NOTE]Pour suivre ce didacticiel, vous avez besoin d'un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-wp8%2F" target="_blank">Version d'évaluation gratuite d'Azure</a>.

##<a name="download-app"></a>Télécharger le projet GetStartedWithData

Ce didacticiel est basé sur [GetStartedWithData][Developer Code Samples site], qui est une application Silverlight pour Windows Phone 8.

1. Téléchargez le projet de l'exemple d'application GetStartedWithData sur le [site d'exemples de code développeur]. 

	>[AZURE.NOTE]Pour créer une application Silverlight pour Windows Phone 8.1, il vous suffit de remplacer par Windows Phone 8.1 le système d'exploitation cible dans le projet d'application Silverlight pour Windows Phone 8 téléchargé. Pour créer une application Windows Phone Store, téléchargez la [version Windows Phone Store de l'application](http://go.microsoft.com/fwlink/p/?LinkId=397372) du projet de l'exemple d'application GetStartedWithData.

2. Dans Visual Studio, ouvrez le projet téléchargé et examinez le fichier MainPage.xaml.cs.

   	Notez que les objets **TodoItem** ajoutés sont stockés dans un **ObservableCollection&lt;TodoItem&gt;** en mémoire.

3. Appuyez sur la touche **F5** pour régénérer le projet et démarrer l'application.

4. Dans l'application, tapez du texte dans la zone de texte, puis cliquez sur le bouton **Enregistrer**.

   	![][0]

   	Le texte enregistré est affiché dans la liste ci-dessous.

<h2><a name="create-service"></a>Création d'un service mobile dans portail de gestion</h2>

[AZURE.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a>Ajout d'une nouvelle table au service mobile</h2>

[AZURE.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

<h2><a name="update-app"></a>Mise à jour de l'application afin d'utiliser le service mobile pour l'accès aux données</h2>

Votre service mobile étant prêt, vous pouvez mettre à jour l'application pour stocker les éléments dans Mobile Services et non dans la collection locale.

1. Dans l'**Explorateur de solutions** de Visual Studio, cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **Manage NuGet Packages**.

2. Dans le volet gauche, sélectionnez la catégorie **En ligne**, recherchez `WindowsAzure.MobileServices`, cliquez sur **Installer** au niveau du package **Azure Mobile Services**, puis acceptez le contrat de licence.

  	![][7]

  	La bibliothèque cliente Mobile Services est ajoutée au projet.

3. Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

4. Cliquez sur l'onglet **Tableau de bord** et notez la valeur **URL du site**, puis cliquez sur **Gérer les clés** et notez la valeur de **Clé de l'application**.

   	![][8]

  	Ces valeurs sont nécessaires pour accéder au service mobile à partir de votre code d'application.

5. Dans Visual Studio, ouvrez le fichier App.xaml.cs et ajoutez l'instruction `using` suivante ou annulez ses marques de commentaire :

       	using Microsoft.WindowsAzure.MobileServices;

6. Dans le même fichier, annulez les marques de commentaire dans le code qui définit la variable **MobileService**, puis indiquez l'URL et la clé d'application du service mobile dans le constructeur **MobileServiceClient**, dans cet ordre.

		//public static MobileServiceClient MobileService = new MobileServiceClient( 
        //    "AppUrl", 
        //    "AppKey" 
        //); 

  	Cela permet de créer une nouvelle instance de **MobileServiceClient**, qui est utilisée pour accéder à votre service mobile.

6. Dans le fichier MainPage.xaml.cs, ajoutez les instructions `using` suivantes ou supprimez leurs marques de commentaire :

       	using Microsoft.WindowsAzure.MobileServices;
		using Newtonsoft.Json;

7. Dans ce même fichier, remplacez la définition de classe **TodoItem** par le code suivant :

        public class TodoItem
        {
            public string Id { get; set; }

            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }

            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }
        }

7. Commentez la ligne qui définit la collection **items** existante, puis annulez les marques de commentaire dans les lignes suivantes :

        private MobileServiceCollection<TodoItem, TodoItem> items;
        private IMobileServiceTable<TodoItem> todoTable = 
			App.MobileService.GetTable<TodoItem>();

   	Ce code crée une collection de liaisons (**éléments**) prenant en charge les services mobiles et une classe proxy pour la table de la base de données SQL **TodoItem** (**todoTable**).

7. Dans la méthode **InsertTodoItem**, supprimez la ligne de code définissant la propriété **TodoItem**.**Id**, ajoutez le modificateur **async** à la méthode, puis annulez les marques de commentaire dans la ligne de code suivante :

        await todoTable.InsertAsync(todoItem);

  	Ce code permet d'insérer un nouvel élément dans la table.

8. Dans la méthode **RefreshTodoItems**, ajoutez le modificateur **async** à la méthode, puis annulez les marques de commentaire sur la ligne de code suivante :

        items = await todoTable.ToCollectionAsync();

   	Cela définit la liaison sur la collection d'éléments dans todoTable, qui contient tous les objets TodoItem renvoyés depuis le service mobile.

9. Dans la méthode **UpdateCheckedTodoItem**, ajoutez le modificateur **async** à la méthode, puis annulez les marques de commentaire sur la ligne de code suivante :

         await todoTable.UpdateAsync(item);

   	Cela permet d'envoyer une mise à jour de l'élément au service mobile.

Maintenant que l'application a été mise à jour pour utiliser Mobile Services pour le stockage principal, le moment est venu de tester l'application avec Mobile Services.

<h2><a name="test-app"></a>Test de l'application sur base de votre nouveau service mobile</h2>

1. Dans Visual Studio, appuyez sur la touche F5 pour exécuter l'application.

2. Comme précédemment, tapez du texte dans la zone de texte, puis cliquez sur **Enregistrer**.

   	Cela permet d'envoyer un nouvel élément en tant qu'insertion au service mobile.

3. Dans le [portail de gestion], cliquez sur **Mobile Services**, puis sur le service mobile.

4. Cliquez sur l'onglet **Données**, puis sur **Parcourir**.

   	![][9]
  
   	La table **TodoItem** contient à présent des données, dont les valeurs d'ID ont été générées par Mobile Services, et les colonnes ont été automatiquement ajoutées à la table de manière à correspondre à la classe TodoItem au sein de l'application.

Cela conclut le didacticiel.

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel a présenté les bases de l'activation d'une application Windows Phone 8 pour utiliser les données dans Mobile Services. Vous pouvez ensuite consulter l'une de ces autres rubriques :

* [Prise en main de l'authentification] <br/>En savoir plus sur l'authentification des utilisateurs de votre application.

* [Prise en main des notifications Push] <br/>En savoir plus sur l'envoi d'une notification Push très basique sur votre application avec Mobile Services.

* [Guide de fonctionnement Mobile Services C#](mobile-services-windows-dotnet-how-to-use-client-library.md) <br/>En savoir plus sur l'utilisation de Mobile Services avec .NET.
 
<!-- Anchors. -->
[Télécharger le projet d'application Windows Phone 8]: #download-app
[Créer le service mobile]: #create-service
[Ajouter une table de données pour le stockage]: #add-table
[Mettre à jour l'application pour utiliser Mobile Services]: #update-app
[Tester l'application avec Mobile Services]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-phone-get-started-data/mobile-quickstart-startup-wp8.png






[7]: ./media/mobile-services-windows-phone-get-started-data/mobile-add-nuget-package-wp.png
[8]: ./media/mobile-services-windows-phone-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-windows-phone-get-started-data/mobile-todoitem-data-browse.png



<!-- URLs. -->
[Validate and modify data with scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-wp8
[Refine queries with paging]: /develop/mobile/tutorials/add-paging-to-data-wp8
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-wp8
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-wp8
[Prise en main de l'authentification]: /develop/mobile/tutorials/get-started-with-users-wp8
[Prise en main des notifications Push]: /develop/mobile/tutorials/get-started-with-push-wp8

[Azure Management Portal]: https://manage.windowsazure.com/
[portail de gestion]: https://manage.windowsazure.com/
[Kit de développement logiciel (SDK) Windows Phone 8]: http://go.microsoft.com/fwlink/p/?LinkID=268374
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268375
[Developer Code Samples site]: http://go.microsoft.com/fwlink/p/?LinkId=271146
[site d'exemples de code développeur]: http://go.microsoft.com/fwlink/p/?LinkId=271146

<!--HONumber=54-->