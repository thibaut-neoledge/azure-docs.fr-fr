<properties linkid="develop-mobile-tutorials-get-started-with-data-xamarin-ios" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Xamarin.iOS) - Azure Mobile Services" metaKeywords="Azure Xamarin.iOS data, Azure mobile services data, " description="Learn how to store and access data from your Azure Mobile Services Xamarin.iOS app." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="donnam" manager="dwrede" services="mobile-services"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="donnam" />

# Prise en main des données dans Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

Cette rubrique montre comment utiliser Azure Mobile Services pour tirer parti des données dans une application Xamarin.iOS. Dans ce didacticiel, vous allez télécharger une application qui stocke les données en mémoire, créer un service mobile, intégrer le service mobile à l'application, puis vous connecter au portail de gestion Azure pour afficher les modifications apportées aux données lors de l'exécution de l'application.

<div class="dev-callout"><b>Remarque</b>
<p>Ce didacticiel vise &agrave; mieux vous faire comprendre en quoi Mobile Services vous permet de stocker et de r&eacute;cup&eacute;rer des donn&eacute;es &agrave; partir d'une application Xamarin.iOS &agrave; l'aide d'Azure. &Agrave; cet effet, il vous guide tout au long des nombreuses &eacute;tapes ex&eacute;cut&eacute;es automatiquement dans le d&eacute;marrage rapide Mobile Services. Si vous n'avez aucune exp&eacute;rience de Mobile Services, nous vous invitons &agrave; suivre d'abord le didacticiel <a href="/fr-fr/develop/mobile/tutorials/get-started-xamarin-ios">Prise en main de Mobile Services</a>.</p>
</div>

Ce didacticiel vous familiarise avec ces étapes de base :

1.  [Téléchargement du projet d'application Xamarin.iOS][Téléchargement du projet d'application Xamarin.iOS]
2.  [Création du service mobile][Création du service mobile]
3.  [Ajout d'une table de données pour le stockage][Ajout d'une table de données pour le stockage]
4.  [Mise à jour de l'application pour utiliser Mobile Services][Mise à jour de l'application pour utiliser Mobile Services]
5.  [Test de l'application avec Mobile Services][Test de l'application avec Mobile Services]

Ce didacticiel nécessite le [composant Azure Mobile Services][composant Azure Mobile Services], [XCode 5.0][XCode 5.0], [Xamarin.iOS] et iOS 5.0 ou ultérieur.

<div class="dev-callout"><strong>Remarque</strong> <p>Pour effectuer ce didacticiel, vous avez besoin d&rsquo;un compte Azure. Si vous ne poss&eacute;dez pas de compte, vous pouvez cr&eacute;er un compte d'&eacute;valuation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-xamarin-ios%2F" target="_blank">Version d'&eacute;valuation gratuite d'Azure</a>.</p></div>

## <a name="download-app"></a><span class="short-header">Téléchargement du projet</span>Téléchargement du projet GetStartedWithData

Ce didacticiel est basé sur l'[application GetStartedWithData][Téléchargement du projet d'application Xamarin.iOS], qui est une application Xamarin.iOS. L'interface utilisateur de cette application est identique à l'application générée par le démarrage rapide Xamarin.IOS de Mobile Services, sauf que les éléments ajoutés sont stockés en local dans la mémoire.

1.  Téléchargez l'[application GetStartedWithData][Téléchargement du projet d'application Xamarin.iOS].

2.  Dans Xamarin Studio, ouvrez le projet téléchargé et examinez la classe **TodoService**.

    Plusieurs commentaires **// TODO** indiquent les étapes à suivre pour que cette application fonctionne avec votre service mobile.

3.  Accédez au menu **Exécuter** et choisissez **Exécuter sans débogage** pour démarrer l'application.

4.  Dans l'application, tapez du texte dans la zone de texte, puis cliquez sur le bouton **+**.

    ![][0]

    Le texte enregistré est affiché dans la liste ci-dessous.

## <a name="create-service"></a><span class="short-header">Création d'un service mobile</span>Création d'un service mobile dans le portail de gestion

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

## <a name="add-table"></a><span class="short-header">Ajout d'une nouvelle table</span>Ajout d'une nouvelle table au service mobile

Pour pouvoir stocker des données d'application dans le nouveau service mobile, vous devez d'abord créer une table dans l'instance de base de données SQL associée.

1.  Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

2.  Cliquez sur l'onglet **Données**, puis sur **+Create**.

    ![][1]

    La boîte de dialogue **Create new table** s’affiche.

3.  Dans **Nom de la table**, tapez *TodoItem*, puis cliquez sur le bouton de vérification.

    ![][2]

    Cela crée une table de stockage **TodoItem** avec les autorisations par défaut définies, ce qui signifie que n'importe quel utilisateur de l'application peut accéder aux données de la table et les modifier.

    <div class="dev-callout"> 
<b>Remarque</b> 
<p>Le m&ecirc;me nom de table est utilis&eacute; dans le d&eacute;marrage rapide avec Mobile Services. Toutefois, chaque table est cr&eacute;&eacute;e dans un sch&eacute;ma sp&eacute;cifique pour un service mobile donn&eacute;. Cela vise &agrave; &eacute;viter les collisions de donn&eacute;es lorsque plusieurs services mobiles utilisent la m&ecirc;me base de donn&eacute;es.</p> 
</div>

4.  Cliquez sur la nouvelle table **TodoItem** et vérifiez qu'aucune ligne de données n'est présente.

5.  Cliquez sur l'onglet **Columns** et vérifiez qu'il existe une seule colonne **id**, qui est créée automatiquement.

    Cela correspond à la configuration minimale requise pour une table dans Mobile Services.

    <div class="dev-callout"><b>Remarque</b>
<p>Lorsque le sch&eacute;ma dynamique est activ&eacute; sur votre service mobile, de nouvelles colonnes sont cr&eacute;&eacute;es automatiquement lorsque des objets JSON sont envoy&eacute;s au service mobile par une op&eacute;ration d'insertion ou de mise &agrave; jour.</p>
</div>

Vous pouvez maintenant utiliser le nouveau service mobile en tant que stockage des données pour l'application.

## <a name="update-app"></a><span class="short-header">Mise à jour de l'application</span>Mise à jour de l'application afin d'utiliser le service mobile pour l'accès aux données

Votre service mobile étant prêt, vous pouvez mettre à jour l'application pour stocker les éléments dans Mobile Services et non dans la collection locale.

1.  Si **Azure Mobile Services** n'est pas déjà répertorié dans le dossier Components, vous pouvez l'obtenir en cliquant avec le bouton droit sur **Components**, en choisissant **Get More Components** et en recherchant **Azure Mobile Services**.

2.  Dans la vue Solution de Xamarin Studio, ouvrez la classe **TodoService** et annulez les marques de commentaire au niveau de l'instruction `using` suivante :

        using Microsoft.WindowsAzure.MobileServices;

3.  Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

4.  Cliquez sur l'onglet **Tableau de bord** et notez la valeur **URL du site**, puis cliquez sur **Gérer les clés** et notez la valeur de **Clé de l'application**.

    ![][3]

5.  Dans la classe **Constants**, annulez les marques de commentaire au niveau des constantes suivantes :

        public const string ApplicationURL = @"AppUrl";
        public const string ApplicationKey = @"AppKey";

6.  Remplacez **AppUrl** et **AppKey** dans les constantes ci-dessus par les valeurs que vous avez trouvées précédemment dans le portail de gestion.

7.  Dans la classe **TodoService**, annulez les marques de commentaire sur la ligne de code suivante :

        private MobileServiceClient client;

    Cela permet de créer une propriété qui représente le client MobileServiceClient qui se connecte au service

8.  Dans la classe **TodoService**, annulez les marques de commentaire sur la ligne de code suivante :

        private IMobileServiceTable<TodoItem> todoTable;  

    Cela permet de créer une représentation de propriété pour la table de votre service mobile.

9.  Annulez les marques de commentaire sur les lignes suivantes du constructeur **TodoService** :

        // TODO:: Uncomment these lines to use Mobile Services
        client = new MobileServiceClient(Constants.ApplicationURL, Constants.ApplicationKey).WithFilter(this); 
        todoTable = client.GetTable<TodoItem>(); 

    Cela crée une instance du client Mobile Services, ainsi que l'instance de la table TodoItem.

10. Annulez les marques de commentaire sur les lignes suivantes de la méthode **RefreshDataAsync** dans **TodoService**

        // TODO:: Uncomment these lines to use Mobile Services

    try
     {
     // Ce code permet d'actualiser les entrées de l'affichage de liste en interrogeant la table TodoItems.
     // La requête exclut les éléments TodoItems terminés
     Items = await todoTable
     .Where (todoItem =\> todoItem.Complete == false).ToListAsync();
     }
     catch (MobileServiceInvalidOperationException e)
     {
     Console.Error.WriteLine (@"ERROR {0}", e.Message);
     return null;
     }

    Cela permet de créer une requête qui renvoie toutes les tâches non terminées.

11. Recherchez la méthode **InsertTodoItemAsync**, puis annulez les marques de commentaire sur la ligne suivante :

        await todoTable.InsertAsync(todoItem);

    Ce code envoie une requête d'insertion au service mobile.

12. Recherchez la méthode **CompleteItemAsync**, puis annulez les marques de commentaire sur la ligne suivante :

        await todoTable.UpdateAsync(item);

    Ce code supprime les éléments TodoItems une fois ceux-ci terminés.

Maintenant que l'application a été mise à jour pour utiliser Mobile Services pour le stockage principal, le moment est venu de tester l'application avec Mobile Services.

## <a name="test-app"></a><span class="short-header">Test de l'application</span>Test de l'application avec votre nouveau service mobile

1.  Dans Xamarin Studio, sélectionnez l'émulateur ou l'appareil vers lequel le déploiement sera effectué dans l'une des principales zones de liste déroulante, puis accédez au menu **Exécuter** et sélectionnez **Exécuter sans débogage** pour démarrer l'application.

    Cela permet d'exécuter votre client Azure Mobile Services, généré avec Xamarin.iOS, qui effectue des requêtes sur les éléments auprès de votre service mobile.

2.  Comme précédemment, tapez du texte dans la zone de texte, puis cliquez sur le bouton **+**.

    Cela permet d'envoyer un nouvel élément en tant qu'insertion au service mobile.

3.  Dans le [portail de gestion][portail de gestion], cliquez sur **Mobile Services**, puis sur le service mobile.

4.  Cliquez sur l'onglet **Données**, puis sur **Parcourir**.

    ![][4]

    La table **TodoItem** contient à présent des données, dont les valeurs d'ID ont été générées par Mobile Services, et les colonnes ont été automatiquement ajoutées à la table de manière à correspondre à la classe TodoItem au sein de l'application.

Cela conclut le didacticiel **Prise en main des données** pour Xamarin.OS.

## Téléchargement de l'exemple terminé

Téléchargez le [projet d'exemple terminé][Téléchargement du projet d'application Xamarin.iOS]. Veillez à mettre à jour les variables **applicationURL** et **applicationKey** avec vos propres paramètres Azure.

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel a présenté les bases de l'activation d'une application iOS pour utiliser les données dans Mobile Services.

Ensuite, pensez à suivre l'un des didacticiels suivants basés sur l'application GetStartedWithData que vous avez créée dans ce didacticiel :

-   [Validation et modification des données avec des scripts][Validation et modification des données avec des scripts]

    En savoir plus sur l'utilisation des scripts serveur dans Mobile Services pour valider et modifier les données envoyées à partir de votre application.

-   [Affinage des requêtes à la pagination][Affinage des requêtes à la pagination]

    En savoir plus sur l'utilisation de la pagination dans les requêtes pour contrôler la quantité de données traitées dans une seule requête.

Une fois que vous avez terminé les séries de données, essayez ces autres didacticiels iOS :

-   [Prise en main de l'authentification][Prise en main de l'authentification]

    En savoir plus sur l'authentification des utilisateurs de votre application.

-   [Prise en main des notifications Push][Prise en main des notifications Push]

    En savoir plus sur l'envoi d'une notification Push très basique sur votre application avec Mobile Services.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. TODO:: update download link, github link, and completed example project with new Xamarin.iOs projects -->

  [mobile-services-selector-get-started-data]: ../includes/mobile-services-selector-get-started-data.md
  [Téléchargement du projet d'application Xamarin.iOS]: http://go.microsoft.com/fwlink/p/?LinkId=331302
  [Création du service mobile]: #create-service
  [Ajout d'une table de données pour le stockage]: #add-table
  [Mise à jour de l'application pour utiliser Mobile Services]: #update-app
  [Test de l'application avec Mobile Services]: #test-app
  [composant Azure Mobile Services]: http://components.xamarin.com/view/azure-mobile-services/
  [XCode 5.0]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [0]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png
  [mobile-services-create-new-service-data]: ../includes/mobile-services-create-new-service-data.md
  [1]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-data-tab-empty.png
  [2]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-create-todoitem-table.png
  [3]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-dashboard-tab.png
  [portail de gestion]: https://manage.windowsazure.com/
  [4]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png
  [Validation et modification des données avec des scripts]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios
  [Affinage des requêtes à la pagination]: /fr-fr/develop/mobile/tutorials/add-paging-to-data-xamarin-ios
  [Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-xamarin-ios
  [Prise en main des notifications Push]: /fr-fr/develop/mobile/tutorials/get-started-with-push-xamarin-ios
