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
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="07/25/2015" 
	ms.author="glenga"/>


# Ajout de services mobiles à une application existante

[AZURE.INCLUDE [mobile-services-selector-get-started-data-legacy](../../includes/mobile-services-selector-get-started-data-legacy.md)]

##Vue d'ensemble

Cette rubrique montre comment utiliser Azure Mobile Services pour exploiter des données dans une application Windows Phone 8. Dans ce didacticiel, vous allez télécharger une application qui stocke les données en mémoire, créer un service mobile, intégrer le service mobile à l'application, puis vous connecter au portail de gestion Azure pour afficher les modifications apportées aux données lors de l'exécution de l'application.

Vous pouvez également voir Nick Harris illustrer cela dans la vidéo suivante :
>[AZURE.VIDEO mobile-get-started-with-data-windows-phone]

##Composants requis 

+ Visual Studio 2012 Express pour Windows Phone 8 et le [Kit de développement logiciel (SDK) Windows Phone 8] en cours d’exécution sur Windows 8. Pour suivre ce didacticiel afin de créer une application Windows Phone 8.1, vous devez utiliser Visual Studio 2013 Update 2 ou une version ultérieure. 

+ Un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Farticles%2Fdocumentation%2Fmobile-services-windows-phone-get-started-data%2F).

##<a name="download-app"></a>Téléchargement du projet GetStartedWithData

Ce didacticiel est basé sur [GetStartedWithData][Developer Code Samples site], qui est une application Silverlight pour Windows Phone 8.

1. Téléchargez le projet de l'exemple d'application GetStartedWithData sur le [site d'exemples de code développeur]. 

	>[AZURE.NOTE]Pour créer une application Silverlight pour Windows Phone 8.1, il vous suffit de remplacer par Windows Phone 8.1 le système d'exploitation cible dans le projet d'application Silverlight pour Windows Phone 8 téléchargé. Pour créer une application Windows Phone Store, téléchargez la [version Windows Phone Store de l'application](http://go.microsoft.com/fwlink/p/?LinkId=397372) du projet de l'exemple d'application GetStartedWithData.

2. Dans Visual Studio, ouvrez le projet téléchargé et examinez le fichier MainPage.xaml.cs.

   	Notez que les objets **TodoItem** ajoutés sont stockés dans un **ObservableCollection&lt;TodoItem&gt;** en mémoire.

3. Appuyez sur la touche **F5** pour régénérer le projet et démarrer l'application.

4. Dans l'application, tapez du texte dans la zone de texte, puis cliquez sur le bouton **Enregistrer**.

   	![][0]

   	Le texte enregistré est affiché dans la liste ci-dessous.

##<a name="create-service"></a>Créer un service mobile dans le portail de gestion

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

##<a name="add-table"></a>Ajout d'une nouvelle table au service mobile

[AZURE.INCLUDE [mobile-services-create-new-service-data-2](../../includes/mobile-services-create-new-service-data-2.md)]

##<a name="update-app"></a>Mise à jour de l’application afin d’utiliser le service mobile pour l’accès aux données

Votre service mobile étant prêt, vous pouvez mettre à jour l’application pour stocker les éléments dans Mobile Services et non dans la collection locale.

1. Dans l'**Explorateur de solutions** de Visual Studio, cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **Manage NuGet Packages**.

2. Dans le volet gauche, sélectionnez la catégorie **En ligne**, recherchez `WindowsAzure.MobileServices`, cliquez sur **Installer** au niveau du package **Azure Mobile Services**, puis acceptez le contrat de licence.

  	![][7]

  	La bibliothèque cliente Mobile Services est ajoutée au projet.

3. Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

4. Cliquez sur l'onglet **Tableau de bord** et notez la valeur **URL du site**, puis cliquez sur **Gérer les clés** et notez la valeur de **Clé de l'application**.

   	![][8]

  	Ces valeurs sont nécessaires pour accéder au service mobile à partir de votre code d'application.

5. Dans Visual Studio, ouvrez le fichier App.xaml.cs et ajoutez l’instruction `using` suivante ou annulez ses marques de commentaire :

       	using Microsoft.WindowsAzure.MobileServices;

6. Dans le même fichier, supprimez les marques de commentaire du code qui définit la variable **MobileService**, puis indiquez l’URL et la clé d’application du service mobile dans le constructeur **MobileServiceClient**, dans cet ordre.

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

   	Ce code crée une collection de liaisons (\*\*éléments\*\*) prenant en charge les services mobiles et une classe proxy pour la table de la base de données SQL **TodoItem** (\*\*todoTable\*\*).

7. Dans la méthode **InsertTodoItem**, supprimez la ligne de code définissant la propriété **TodoItem**.\*\*Id\*\*, ajoutez le modificateur **async** à la méthode, puis annulez les marques de commentaire dans la ligne de code suivante :

        await todoTable.InsertAsync(todoItem);

  	Ce code permet d'insérer un nouvel élément dans la table.

8. Dans la méthode **RefreshTodoItems**, ajoutez le modificateur **async** à la méthode, puis annulez les marques de commentaire sur la ligne de code suivante :

        items = await todoTable.ToCollectionAsync();

   	Cela définit la liaison sur la collection d'éléments dans todoTable, qui contient tous les objets TodoItem renvoyés depuis le service mobile.

9. Dans la méthode **UpdateCheckedTodoItem**, ajoutez le modificateur **async** à la méthode, puis annulez les marques de commentaire sur la ligne de code suivante :

         await todoTable.UpdateAsync(item);

   	Cela permet d'envoyer une mise à jour de l'élément au service mobile.

Maintenant que l’application a été mise à jour pour utiliser Mobile Services pour le stockage principal, le moment est venu de tester l’application avec Mobile Services.

##<a name="test-app"></a>Test de l’application sur la base de votre nouveau service mobile

1. Dans Visual Studio, appuyez sur la touche F5 pour exécuter l’application.

2. Comme précédemment, tapez du texte dans la zone de texte, puis cliquez sur **Enregistrer**.

   	Cela permet d'envoyer un nouvel élément en tant qu'insertion au service mobile.

3. Dans le [portail de gestion], cliquez sur **Mobile Services**, puis sur le service mobile.

4. Cliquez sur l'onglet **Données**, puis sur **Parcourir**.

   	![][9]
  
   	La table **TodoItem** contient à présent des données, dont les valeurs d'ID ont été générées par Mobile Services, et les colonnes ont été automatiquement ajoutées à la table de manière à correspondre à la classe TodoItem au sein de l'application.

Cela conclut le didacticiel.

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel a présenté les bases de l'activation d'une application Windows Phone 8 pour utiliser les données dans Mobile Services. Vous pouvez ensuite consulter l’une de ces autres rubriques :

* [Ajouter l’authentification à votre application](mobile-services-windows-phone-get-started-users.md) <br/>En savoir plus sur l’authentification des utilisateurs de votre application.

* [Ajouter les notifications push à votre application](mobile-services-javascript-backend-windows-phone-get-started-push.md) <br/>En savoir plus sur l’envoi d’une notification push très basique à votre application avec Mobile Services.

* [Guide de fonctionnement Mobile Services C#](mobile-services-windows-dotnet-how-to-use-client-library.md) <br/>En savoir plus sur l’utilisation de Mobile Services avec .NET.
 
<!-- Anchors. -->
[Download the Windows Phone 8 app project]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-phone-get-started-data/mobile-quickstart-startup-wp8.png
[7]: ./media/mobile-services-windows-phone-get-started-data/mobile-add-nuget-package-wp.png
[8]: ./media/mobile-services-windows-phone-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-windows-phone-get-started-data/mobile-todoitem-data-browse.png

<!-- URLs. -->

[Azure Management Portal]: https://manage.windowsazure.com/
[portail de gestion]: https://manage.windowsazure.com/
[Kit de développement logiciel (SDK) Windows Phone 8]: http://go.microsoft.com/fwlink/p/?LinkID=268374
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268375
[Developer Code Samples site]: http://go.microsoft.com/fwlink/p/?LinkId=271146
[site d'exemples de code développeur]: http://go.microsoft.com/fwlink/p/?LinkId=271146
 

<!---HONumber=July15_HO5-->