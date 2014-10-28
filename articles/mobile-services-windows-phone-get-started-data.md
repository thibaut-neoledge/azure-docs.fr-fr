<properties linkid="develop-mobile-tutorials-get-started-with-data-wp8" urlDisplayName="Get Started with Data" pageTitle="Get started with data (WP8) - Azure Mobile Services" metaKeywords="" description="Learn how to get started using data from your Azure Mobile Services Windows Phone 8 app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/19/2014" ms.author="glenga"></tags>

# Prise en main des données dans Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-data-legacy][]]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>Cette rubrique montre comment utiliser Azure Mobile Services pour exploiter des donn&eacute;es dans une application Windows Phone&nbsp;8. Dans ce didacticiel, vous allez t&eacute;l&eacute;charger une application qui stocke les donn&eacute;es en m&eacute;moire, cr&eacute;er un service mobile, int&eacute;grer le service mobile &agrave; l'application, puis vous connecter au portail de gestion Azure pour afficher les modifications apport&eacute;es aux donn&eacute;es lors de l'ex&eacute;cution de l'application.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkID=298628" target="_blank" class="label">regarder le didacticiel</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-get-started-data-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkID=298628" target="_blank" class="dev-onpage-video"><span class="icon">Lire la vid&eacute;o</span></a> <span class="time">12:54:00</span></div>

</div>

<div class="dev-callout"><b>Remarque</b>
<p>Ce didacticiel vise &agrave; mieux vous faire comprendre en quoi Mobile Services vous permet de stocker et de r&eacute;cup&eacute;rer des donn&eacute;es dans une application Windows Phone&nbsp;8 &agrave; l'aide d'Azure. &Agrave; cet effet, il vous guide tout au long des nombreuses &eacute;tapes ex&eacute;cut&eacute;es automatiquement dans le d&eacute;marrage rapide Mobile Services. Si vous n'avez aucune exp&eacute;rience de Mobile Services, nous vous invitons &agrave; suivre d'abord le didacticiel <a href="/fr-fr/develop/mobile/tutorials/get-started-wp8">Prise en main de Mobile Services</a>.</p>
</div>

Ce didacticiel vous familiarise avec ces étapes de base :

1.  [Téléchargement du projet d'application Windows Phone 8][]
2.  [Création du service mobile][]
3.  [Ajout d'une table de données pour le stockage][]
4.  [Mise à jour de l'application pour utiliser Mobile Services][]
5.  [Test de l'application avec Mobile Services][]

Ce didacticiel requiert Visual Studio 2012 Express pour Windows Phone 8 et le [Kit de développement logiciel (SDK) Windows Phone 8][] s'exécutant sous Windows 8. Pour effectuer ce didacticiel et créer une application Windows Phone 8.1, vous devez utiliser Visual Studio 2013 Update 2 ou une version ultérieure.

> [WACOM.NOTE] Pour effectuer ce didacticiel, vous avez besoin d’un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure][].

## <a name="download-app"></a>Téléchargement du projet GetStartedWithData

Ce didacticiel est basé sur [GetStartedWithData][], qui est une application Silverlight pour Windows Phone 8. L'interface utilisateur de cette application est identique à l'application générée par le démarrage rapide de Mobile Services, sauf que les éléments ajoutés sont stockés en local dans la mémoire.

1.  Téléchargez le projet de l'exemple d'application GetStartedWithData sur le [site d'exemples de code développeur][GetStartedWithData].

    > [WACOM.NOTE]Pour créer une application Silverlight pour Windows Phone 8.1, il vous suffit de remplacer par Windows Phone 8.1 le système d'exploitation cible dans le projet d'application Silverlight pour Windows Phone 8 téléchargé. Pour créer une application Windows Phone Store, téléchargez la [version Windows Phone Store de l'application][] du projet de l'exemple d'application GetStartedWithData.

2.  Dans Visual Studio, ouvrez le projet téléchargé et examinez le fichier MainPage.xaml.cs.

    Notez que les objets **TodoItem** ajoutés sont stockés dans un **ObservableCollection\<TodoItem\>** en mémoire.

3.  Appuyez sur la touche **F5** pour régénérer le projet et démarrer l'application.

4.  Dans l'application, tapez du texte dans la zone de texte, puis cliquez sur le bouton **Enregistrer**.

    ![][]

    Le texte enregistré est affiché dans la liste ci-dessous.

## <a name="create-service"></a><span class="short-header">Création d'un service mobile</span>Création d'un service mobile dans le portail de gestion

[WACOM.INCLUDE [mobile-services-create-new-service-data][]]

## <a name="add-table"></a><span class="short-header">Ajout d'une nouvelle table</span>Ajout d'une nouvelle table au service mobile

[WACOM.INCLUDE [mobile-services-create-new-service-data-2][]]

## <a name="update-app"></a><span class="short-header">Mise à jour de l'application</span>Mise à jour de l'application afin d'utiliser le service mobile pour l'accès aux données

Votre service mobile étant prêt, vous pouvez mettre à jour l'application pour stocker les éléments dans Mobile Services et non dans la collection locale.

1.  Dans l'**Explorateur de solutions** de Visual Studio, cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **Manage NuGet Packages**.

2.  Dans le volet gauche, sélectionnez la catégorie **Online**, recherchez `WindowsAzure.MobileServices`, cliquez sur **Install**au niveau du package**Azure Mobile Services**, puis acceptez le contrat de licence.

    ![][1]

    La bibliothèque cliente Mobile Services est ajoutée au projet.

3.  Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

4.  Cliquez sur l'onglet **Tableau de bord** et notez la valeur **URL du site**, puis cliquez sur **Gérer les clés** et notez la valeur de **Clé de l'application**.

    ![][2]

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

2.  Comme précédemment, tapez du texte dans la zone de texte, puis cliquez sur **Enregistrer**.

    Cela permet d'envoyer un nouvel élément en tant qu'insertion au service mobile.

3.  Dans le [portail de gestion][], cliquez sur **Mobile Services**, puis sur le service mobile.

4.  Cliquez sur l'onglet **Données**, puis sur **Parcourir**.

    ![][3]

    La table **TodoItem** contient à présent des données, dont les valeurs d'ID ont été générées par Mobile Services, et les colonnes ont été automatiquement ajoutées à la table de manière à correspondre à la classe TodoItem au sein de l'application.

Cela conclut le didacticiel **Prise en main des données** pour Windows Phone 8.

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel a présenté les bases de l'activation d'une application Windows Phone 8 pour utiliser les données dans Mobile Services. Ensuite, nous vous invitons à suivre le didacticiel suivant, qui est basé sur l'application GetStartedWithData que vous avez créée dans ce didacticiel :

-   [Validation et modification des données avec des scripts][]

    En savoir plus sur l'utilisation des scripts serveur dans Mobile Services pour valider et modifier les données envoyées à partir de votre application.

-   [Affinage des requêtes à la pagination][]

    En savoir plus sur l'utilisation de la pagination dans les requêtes pour contrôler la quantité de données traitées dans une seule requête.

Une fois que vous avez terminé la série de données, vous pouvez essayer l'un des didacticiels Windows Phone 8 suivants :

-   [Prise en main de l'authentification][]

    En savoir plus sur l'authentification des utilisateurs de votre application.

-   [Prise en main des notifications Push][]

    En savoir plus sur l'envoi d'une notification Push très basique sur votre application avec Mobile Services.

  [mobile-services-selector-get-started-data-legacy]: ../includes/mobile-services-selector-get-started-data-legacy.md
  [regarder le didacticiel]: http://go.microsoft.com/fwlink/?LinkID=298628
  [Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started-wp8
  [Téléchargement du projet d'application Windows Phone 8]: #download-app
  [Création du service mobile]: #create-service
  [Ajout d'une table de données pour le stockage]: #add-table
  [Mise à jour de l'application pour utiliser Mobile Services]: #update-app
  [Test de l'application avec Mobile Services]: #test-app
  [Kit de développement logiciel (SDK) Windows Phone 8]: http://go.microsoft.com/fwlink/p/?LinkID=268374
  [Version d'évaluation gratuite d'Azure]: http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-wp8%2F
  [GetStartedWithData]: http://go.microsoft.com/fwlink/p/?LinkId=271146
  [version Windows Phone Store de l'application]: http://go.microsoft.com/fwlink/p/?LinkId=397372
  []: ./media/mobile-services-windows-phone-get-started-data/mobile-quickstart-startup-wp8.png
  [mobile-services-create-new-service-data]: ../includes/mobile-services-create-new-service-data.md
  [mobile-services-create-new-service-data-2]: ../includes/mobile-services-create-new-service-data-2.md
  [1]: ./media/mobile-services-windows-phone-get-started-data/mobile-add-nuget-package-wp.png
  [2]: ./media/mobile-services-windows-phone-get-started-data/mobile-dashboard-tab.png
  [portail de gestion]: https://manage.windowsazure.com/
  [3]: ./media/mobile-services-windows-phone-get-started-data/mobile-todoitem-data-browse.png
  [Validation et modification des données avec des scripts]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-wp8
  [Affinage des requêtes à la pagination]: /fr-fr/develop/mobile/tutorials/add-paging-to-data-wp8
  [Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-wp8
  [Prise en main des notifications Push]: /fr-fr/develop/mobile/tutorials/get-started-with-push-wp8
