<properties 
	pageTitle="Prise en main des données (Xamarin.iOS) - Azure Mobile Services" 
	description="Découvrez comment stocker des données et y accéder à partir de votre application Azure Mobile Services Xamarin.iOS." 
	documentationCenter="xamarin" 
	authors="lindydonna" 
	manager="dwrede" 
	services="mobile-services" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/24/2014" 
	ms.author="donnam"/>

# Ajout de Mobile Services à une application existante

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

Cette rubrique vous présente l'utilisation d'Azure Mobile Services pour tirer parti des données dans une application Xamarin.iOS. Dans ce didacticiel, vous allez télécharger une application qui stocke les données en mémoire, créer un service mobile, intégrer le service mobile à l'application, puis vous connecter au portail de gestion Azure pour afficher les modifications apportées aux données lors de l'exécution de l'application.

> [AZURE.NOTE] Ce didacticiel vise à mieux vous faire comprendre en quoi Mobile Services vous permet de stocker et de récupérer des données à partir d'une application Xamarin.iOS à l'aide d'Azure. À cet effet, il vous guide tout au long des nombreuses étapes exécutées automatiquement dans le démarrage rapide Mobile Services. Si vous n'avez aucune expérience avec Mobile Services, nous vous invitons à suivre d'abord le didacticiel [Prise en main de Mobile Services](/fr-fr/develop/mobile/tutorials/get-started-xamarin-ios).

Ce didacticiel vous familiarise avec ces étapes de base :

1. [Téléchargement du projet d'application Xamarin.iOS][GitHub] 
2. [Création du service mobile]
3. [Ajout d'une table de données pour le stockage]
4. [Mise à jour de l'application pour utiliser Mobile Services]
5. [Test de l'application par rapport à Mobile Services]

Ce didacticiel nécessite le [Composants Azure Mobile Services], [XCode 5.0][Installation de Xcode], [Xamarin.iOS], et iOS 5.0 ou une version ultérieure.

> [AZURE.IMPORTANT] Pour effectuer ce didacticiel, vous avez besoin d'un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite Azure](http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-xamarin-ios%2F"%20target="_blank).

<h2><a name="download-app"></a>Téléchargement du projet GetStartedWithData</h2>

Ce didacticiel est basé sur [l'application GetStartedWithData][GitHub], qui est une application Xamarin.iOS app. L'interface utilisateur de cette application est identique à l'application générée par le démarrage rapide Xamarin.IOS de Mobile Services, sauf que les éléments ajoutés sont stockés en local dans la mémoire. 

1. Téléchargez [l'application GetStartedWithData][GitHub]. 

2. Dans Xamarin Studio, ouvrez le projet téléchargé et examinez la classe **TodoService**.

   	Plusieurs commentaires **// TODO** indiquent les étapes à suivre pour que cette application fonctionne avec votre service mobile.

3. Accédez au menu **Exécuter** et choisissez **Exécuter sans débogage** pour démarrer l'application.

4. Dans l'application, tapez du texte dans la zone de texte, puis cliquez sur le bouton **+**.

   	![][0]  

   	Le texte enregistré est affiché dans la liste ci-dessous.

<h2><a name="create-service"></a>Création d'un service mobile dans le portail de gestion</h2>

[AZURE.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a>Ajout d'une nouvelle table au service mobile</h2>

Pour pouvoir stocker des données d'application dans le nouveau service mobile, vous devez d'abord créer une table dans l'instance de base de données SQL associée.

1. Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

2. Cliquez sur l'onglet **Données**, puis sur **+Créer**.

   	![][5]

   	La boîte de dialogue **Créer une table** s'affiche.

3. Dans **Nom de la table**, tapez _TodoItem_, puis cliquez sur le bouton de vérification.

  	![][6]

  	Cela crée une table de stockage **TodoItem** avec les autorisations par défaut définies, ce qui signifie que n'importe quel utilisateur de l'application peut accéder aux données de la table et les modifier. 

    > [AZURE.NOTE] Le même nom de table est utilisé dans le démarrage rapide avec Mobile Services. Toutefois, chaque table est créée dans un schéma spécifique pour un service mobile donné. Cela vise à éviter les collisions de données lorsque plusieurs services mobiles utilisent la même base de données.

4. Cliquez sur la nouvelle table **TodoItem** et vérifiez qu'aucune ligne de données n'est présente.

5. Cliquez sur l'onglet **Columns** et vérifiez qu'il existe une seule colonne **id**, qui est créée automatiquement.

	  Cela correspond à la configuration minimale requise pour une table dans Mobile Services. 

    > [AZURE.NOTE] Lorsque le schéma dynamique est activé sur votre service mobile, de nouvelles colonnes sont créées automatiquement lorsque des objets JSON sont envoyés au service mobile par une opération d'insertion ou de mise à jour.

Vous pouvez maintenant utiliser le nouveau service mobile en tant que stockage des données pour l'application.

<h2><a name="update-app"></a>Mise à jour de l'application afin d'utiliser le service mobile pour l'accès aux données</h2>

Votre service mobile étant prêt, vous pouvez mettre à jour l'application pour stocker les éléments dans Mobile Services et non dans la collection locale. 

1. Si **Azure Mobile Services** n'est pas déjà répertorié dans le dossier Components, vous pouvez l'obtenir en cliquant avec le bouton droit sur **Components**, en choisissant **Get More Components** et en recherchant **Azure Mobile Services**.

2. In the Solution view in Xamarin Studio, open the **TodoService** class and uncomment the following `using` statement:

        using Microsoft.WindowsAzure.MobileServices;
               
3. Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

4. Cliquez sur l'onglet **Tableau de bord** et notez la valeur **URL du site**, puis cliquez sur **Gérer les clés** et notez la valeur de **Clé de l'application**.

   	![][8]

5. Dans la classe **Constants**, annulez les marques de commentaire au niveau des constantes suivantes :

        public const string ApplicationURL = @"AppUrl";
        public const string ApplicationKey = @"AppKey";

6. Remplacez **AppUrl** et **AppKey** dans les constantes ci-dessus par les valeurs que vous avez trouvées précédemment dans le portail de gestion.

7. Dans la classe **TodoService**, annulez les marques de commentaire sur la ligne de code suivante :

        private MobileServiceClient client;

   	Cela permet de créer une propriété qui représente le client MobileServiceClient qui se connecte au service

8. Dans la classe **TodoService**, annulez les marques de commentaire sur la ligne de code suivante :

        private IMobileServiceTable<TodoItem> todoTable;  

   	Cela permet de créer une représentation de propriété pour la table de votre service mobile.

9. Annulez les marques de commentaire sur les lignes suivantes du constructeur **TodoService** :

		// TODO:: Uncomment these lines to use Mobile Services
		client = new MobileServiceClient(Constants.ApplicationURL, Constants.ApplicationKey).WithFilter(this); 
		todoTable = client.GetTable<TodoItem>(); 

    Cela crée une instance du client Mobile Services, ainsi que l'instance de la table TodoItem.

10. Annulez les marques de commentaire sur les lignes suivantes de la méthode **RefreshDataAsync** dans **TodoService**

		// TODO:: Uncomment these lines to use Mobile Services
    try 
        {
			// This code refreshes the entries in the list view by querying the TodoItems table.
			// The query excludes completed TodoItems
			Items = await todoTable
				.Where (todoItem => todoItem.Complete == false).ToListAsync();
		} 
        catch (MobileServiceInvalidOperationException e) 
        {
			Console.Error.WriteLine (@"ERROR {0}", e.Message);
			return null;
		}
        
    Cela permet de créer une requête qui renvoie toutes les tâches non terminées.

11. Recherchez la méthode **InsertTodoItemAsync**, puis annulez les marques de commentaire sur la ligne suivante :

		await todoTable.InsertAsync(todoItem);
		
    Ce code envoie une requête d'insertion au service mobile.

13. Recherchez la méthode **CompleteItemAsync**, puis annulez les marques de commentaire sur la ligne suivante :

		await todoTable.UpdateAsync(item);
		
   	Ce code supprime les éléments TodoItems une fois ceux-ci terminés. 

Maintenant que l'application a été mise à jour pour utiliser Mobile Services pour le stockage principal, le moment est venu de tester l'application avec Mobile Services.

<h2><a name="test-app"></a>Test de l'application sur la base de votre nouveau service mobile</h2>

1. Dans Xamarin Studio, sélectionnez l'émulateur ou l'appareil vers lequel le déploiement sera effectué dans l'une des principales zones de liste déroulante, puis accédez au menu **Exécuter** et sélectionnez **Exécuter sans débogage** pour démarrer l'application.

   	Cela permet d'exécuter votre client Azure Mobile Services, généré avec Xamarin.iOS, qui effectue des requêtes sur les éléments auprès de votre service mobile.

2. Comme précédemment, tapez du texte dans la zone de texte, puis cliquez sur le bouton **+**.

   	Cela permet d'envoyer un nouvel élément en tant qu'insertion au service mobile.

3. Dans le [portail de gestion], cliquez sur **Mobile Services**, puis sur le service mobile.

4. Cliquez sur l'onglet **Données**, puis sur **Parcourir**.

   	![][9]
  
   	Notez que la table **TodoItem** contient à présent des données, dont les valeurs d'ID ont été générées par Mobile Services, et que des colonnes ont été automatiquement ajoutées à la table de manière à correspondre à la classe TodoItem au sein de l'application.

Cela conclut le didacticiel **Prise en main des données** pour Xamarin.OS.

## Téléchargement de l'exemple terminé
Téléchargez le [Exemple de projet terminé]. Veillez à mettre à jour les variables **applicationURL** et **applicationKey** avec vos paramètres Azure. 

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel a présenté les bases de l'activation d'une application iOS pour utiliser les données dans Mobile Services. 

Ensuite, pensez à suivre l'un des didacticiels suivants basés sur l'application GetStartedWithData que vous avez créée dans ce didacticiel :

* [Validation et modification de données à l'aide de scripts]
  <br/>Obtenez plus d'informations sur l'utilisation des scripts serveur dans Mobile Services pour valider et modifier les données envoyées à partir de votre application.

* [Affinage des requêtes au moyen de la pagination]
  <br/>Apprenez à utiliser la pagination dans les requêtes pour contrôler la quantité de données traitées dans une seule requête.

Une fois que vous avez terminé les séries de données, essayez ces autres didacticiels iOS :

* [Prise en main de l'authentification] 
	<br/>Découvrez comment authentifier les utilisateurs de votre application.

* [Prise en main des notifications Push] 
  <br/>Apprenez à envoyer une notification Push très basique à votre application avec Mobile Services.

<!-- Anchors. -->

[Obtention de l'application Windows Store]: #download-app
[Création du service mobile]: #create-service
[Ajout d'une table de données pour le stockage]: #add-table
[Mise à jour de l'application pour utiliser Mobile Services]: #update-app
[Test de l'application par rapport à Mobile Services]: #test-app
[Étapes suivantes]:#next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png




[5]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-data-tab-empty.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-create-todoitem-table.png
[8]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-dashboard-tab.png
[9]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png

<!-- URLs. TODO:: update download link, github link, and completed example project with new Xamarin.iOs projects -->
[Validation et modification de données à l'aide de scripts]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios
[Affinage des requêtes au moyen de la pagination]: /fr-fr/develop/mobile/tutorials/add-paging-to-data-xamarin-ios
[Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started-xamarin-ios
[Prise en main des données]: /fr-fr/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Prise en main des notifications Push]: /fr-fr/develop/mobile/tutorials/get-started-with-push-xamarin-ios

[Portail de gestion Azure]: https://manage.windowsazure.com/
[Portail de gestion]: https://manage.windowsazure.com/
[Installation de Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Kit de développement logiciel (SDK) Mobile Services pour iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331302
[Référentiel GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=268784
[Composants Azure Mobile Services]: http://components.xamarin.com/view/azure-mobile-services/

[Exemple de projet terminé]: http://go.microsoft.com/fwlink/p/?LinkId=331302


<!--HONumber=42-->
