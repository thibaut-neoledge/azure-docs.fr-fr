<properties urlDisplayName="Get Started with Data" pageTitle="Prise en main des données (Xamarin.Android) - Azure Mobile Services " metakeywords="données Azure Xamarin.Android, données Azure Mobile Services" description="Learn how to store and access data from your Azure Mobile Services Xamarin.Android app." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Get started with data in Mobile Services" documentationCenter="Mobile" authors="donnam" manager="dwrede" services="mobile-services" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="donnam" />

# Ajout de services mobiles à une application existante

[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]	

<p>Cette rubrique vous présente l'utilisation d'Azure Mobile Services pour tirer parti des données dans une application Xamarin.Android. Dans ce didacticiel, vous allez télécharger une application qui stocke les données en mémoire, créer un service mobile, intégrer le service mobile à l'application, puis vous connecter au portail de gestion Azure pour afficher les modifications apportées aux données lors de l'exécution de l'application.</p>

<div class="dev-callout"><b>Remarque</b>
<p>Ce didacticiel vise à mieux vous faire comprendre en quoi Mobile Services vous permet de stocker et de récupérer des données à partir d'une application Xamarin.Android à l'aide d'Azure. À cet effet, il vous guide tout au long des nombreuses étapes exécutées automatiquement dans le démarrage rapide Mobile Services. Si vous n'avez aucune expérience de Mobile Services, nous vous invitons à suivre d'abord le didacticiel <a href="/fr-fr/develop/mobile/tutorials/get-started-xamarin-android">Prise en main de Mobile Services</a>.</p>
</div>

Ce didacticiel vous familiarise avec ces étapes de base :

1. [Téléchargement du projet d'application Xamarin.Android][GitHub] 
2. [Création du service mobile]
3. [Ajout d'une table de données pour le stockage]
4. [Mise à jour de l'application pour utiliser Mobile Services]
5. [Test de l'application avec Mobile Services]

<div class="dev-callout"><strong>Remarque</strong> <p>Pour effectuer ce didacticiel, vous avez besoin d'un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=A9C9624B5" target="_blank">Version d'évaluation gratuite d'Azure</a>.</p></div> 

Ce didacticiel requiert le [composant Azure Mobile Services], [Xamarin.Android] et le Kit de développement logiciel (SDK) Android 4.2 ou version ultérieure. 

<div class="dev-callout"><b>Remarque</b>
<p>Le projet GetStartedWithData téléchargé requiert Android 4.2 ou une version ultérieure. Toutefois, le Kit de développement logiciel (SDK) Mobile Services requiert uniquement Android 2.2 ou une version ultérieure.</p>
</div>

<h2><a name="download-app"></a>Téléchargement du projet GetStartedWithData</h2>

Ce didacticiel est basé sur l'application [GetStartedWithData][GitHub], qui est une application Xamarin.Android. L'interface utilisateur de cette application est identique à l'application générée par le démarrage rapide Android de Mobile Services, sauf que les éléments ajoutés sont stockés en local dans la mémoire. 

1. Téléchargez l'exemple d'application `GetStartedWithData`, puis décompressez les fichiers sur votre ordinateur. 

2. Dans Xamarin Studio, cliquez sur **File**, puis **Open**, accédez au dossier où vous avez décompressé l'exemple de projet GetStartedWithData, puis sélectionnez **XamarinTodoQuickStart.Android.sln** et ouvrez-le.

3. Recherchez et ouvrez la classe **TodoActivity**.

   	Des commentaires `// TODO::` indiquent les étapes à suivre pour que cette application fonctionne avec votre service mobile.

5. À partir du menu **Exécuter**, cliquez sur **Exécuter sans débogage**. Vous êtes invité à choisir un émulateur ou un appareil USB Android attaché.

	<div class="dev-callout"><strong>Remarque</strong> <p>Vous pouvez exécuter ce projet à l'aide d'un téléphone Android ou de l'émulateur Android. L'exécution avec un téléphone Android requiert le téléchargement d'un pilote USB propre au téléphone.</p> <p>Pour exécuter le projet dans l'émulateur Android, vous devez définir au moins un AVD (appareil virtuel Android). Utilisez le gestionnaire AVD pour créer et gérer ces appareils.</p></div>

6. Dans l'application, tapez un texte explicite, comme _Suivre le didacticiel_, puis cliquez sur **Ajouter**.

   	![][13]

   	Notez que le texte enregistré est stocké dans une collection en mémoire et affiché dans la liste ci-dessous.

<h2><a name="create-service"></a>Création d'un service mobile dans le portail de gestion</h2>

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a>Ajout d'une nouvelle table au service mobile</h2>

Avant de pouvoir stocker des données d'application dans le nouveau service mobile, vous devez créer une table.  

1. Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

2. Cliquez sur l'onglet **Données**, puis sur **+Create**.

   	![][5]

   	La boîte de dialogue **Create new table** s'affiche.

3. Dans **Nom de la table**, tapez _TodoItem_, puis cliquez sur le bouton de vérification.

  	![][6]

  	Cela crée une table de stockage **TodoItem** avec les autorisations par défaut définies, ce qui signifie que n'importe quel utilisateur de l'application peut accéder aux données de la table et les modifier. 

    <div class="dev-callout"> 
	<b>Remarque</b> 
	<p>Le même nom de table est utilisé dans le démarrage rapide avec Mobile Services. Toutefois, chaque table est créée dans un schéma spécifique pour un service mobile donné. Cela vise à éviter les collisions de données lorsque plusieurs services mobiles utilisent la même base de données.</p> 
	</div>

4. Cliquez sur la nouvelle table **TodoItem** et vérifiez qu'aucune ligne de données n'est présente.

5. Cliquez sur l'onglet **Columns** et vérifiez qu'il existe une seule colonne **id**, qui est créée automatiquement.

  	Cela correspond à la configuration minimale requise pour une table dans Mobile Services. 

    <div class="dev-callout"><b>Remarque</b>
	<p>Lorsque le schéma dynamique est activé sur votre service mobile, de nouvelles colonnes sont créées automatiquement lorsque des objets JSON sont envoyés au service mobile par une opération d'insertion ou de mise à jour.</p>
    </div>

Vous pouvez maintenant utiliser le nouveau service mobile en tant que stockage des données pour l'application.

<h2><a name="update-app"></a>Mise à jour de l'application afin d'utiliser le service mobile pour l'accès aux données</h2>

Votre service mobile étant prêt, vous pouvez mettre à jour l'application pour stocker les éléments dans Mobile Services et non dans la collection locale. 

1. Si **Azure Mobile Services** n'est pas déjà répertorié dans le dossier Components, vous pouvez l'obtenir en cliquant avec le bouton droit sur **Components**, en choisissant **Get More Components** et en recherchant **Azure Mobile Services**.

  	Le composant du Kit de développement logiciel (SDK) Mobile Services est ajouté au projet.

2. Ouvrez le fichier **AndroidManifest.xml** et assurez-vous que la ligne d'autorisation suivante existe :

		<uses-permission android:name="android.permission.INTERNET" />

  	L'application peut ainsi accéder à Mobile Services dans Azure.

3. Dans la fenêtre **Solution**, ouvrez la classe **TodoActivity**, puis annulez les marques de commentaire sur la ligne de code suivante : 

		using Microsoft.WindowsAzure.MobileServices;
 
4. Nous allons supprimer la liste en mémoire actuellement utilisée par l'application pour la remplacer par un service mobile. Dans la classe **TodoActivity**, placez la ligne de code suivante en commentaire, qui définit la liste **todoItemList** existante.

		public List<TodoItem> todoItemList = new ArrayList<TodoItem>();

5. Une fois l'étape précédente effectuée, le projet indique des erreurs de build. Recherchez les trois emplacements restants où la variable `todoItemList` est utilisée et placez les sections indiquées en commentaire. 

6. Nous ajoutons maintenant notre service mobile. Annulez les marques de commentaire sur les lignes de code suivantes :

        private MobileServiceClient client; // Mobile Service Client references
        private IMobileServiceTable<TodoItem> todoTable; // Mobile Service Table used to access data   

7. Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

8. Cliquez sur l'onglet **Tableau de bord** et notez la valeur **URL du site**, puis cliquez sur **Gérer les clés** et notez la valeur de **Clé de l'application**.

   	![][8]

  	Ces valeurs sont nécessaires pour accéder au service mobile à partir de votre code d'application.

9. Dans la classe **Constants**, annulez les marques de commentaire au niveau des variables de membres suivantes :

        public const string ApplicationURL = @"AppUrl";
        public const string ApplicationKey = @"AppKey";
        
10. Remplacez **AppUrl** et **AppKey** dans les variables ci-dessus par les valeurs extraites du portail de gestion plus haut.

11. Dans la méthode **OnCreate**, annulez les marques de commentaire sur les lignes de code suivantes qui définissent la variable **MobileServiceClient** :

		// Create the Mobile Service Client instance, using the provided
		// Mobile Service URL and key
		client = new MobileServiceClient(
			Constants.ApplicationURL,
			Constants.ApplicationKey).WithFilter(filter);

		// Get the Mobile Service Table instance to use
		todoTable = client.GetTable<TodoItem>();    

  	Cela crée une instance de MobileServiceClient utilisée pour accéder au service mobile. Cela crée également l'instance de MobileServiceTable utilisée pour traiter par proxy le stockage des données dans le service mobile.

12. Recherchez la classe ProgressFilter en bas du fichier et annulez les marques de commentaire sur cette dernière. Cette classe affiche un indicateur " chargement " lorsque MobileServiceClient exécute des opérations réseau.

13. Annulez les marques de commentaire sur ces lignes de la méthode **CheckItem** :

		try {
			await todoTable.UpdateAsync(item);
			if (item.Complete)
				adapter.Remove(item);
		} catch (Exception e) {
			CreateAndShowDialog(e, "Error");
		}

   	Cela permet d'envoyer une mise à jour de l'élément au service mobile et de supprimer les éléments cochés de l'adaptateur.
    
14. Annulez les marques de commentaire sur ces lignes de la méthode **AddItem** :
	
		try 
		{
			// Insert the new item
			await todoTable.InsertAsync(item);

			if (!item.Complete) 
				adapter.Add(item);			
		} 
		catch (Exception e) 
		{
			CreateAndShowDialog(e, "Error");
		}   		

  	Ce code permet de créer un élément et de l'insérer dans la table du service mobile distant.

15. Annulez les marques de commentaire sur ces lignes de la méthode **RefreshItemsFromTableAsync** :

		try {
			// Get the items that weren't marked as completed and add them in the adapter
			var list = await todoTable.Where(item => item.Complete == false).ToListAsync ();

			adapter.Clear();

			foreach (TodoItem current in list)
				adapter.Add(current);
		} 
        catch (Exception e) 
        {
			CreateAndShowDialog(e, "Error");
		}

	Cela permet d'interroger le service mobile et de renvoyer tous les éléments qui ne sont pas marqués comme Complete. Les éléments sont ajoutés à l'adaptateur pour la liaison.
		

Maintenant que l'application a été mise à jour pour utiliser Mobile Services pour le stockage principal, le moment est venu de tester l'application avec Mobile Services.

<h2><a name="test-app"></a>Test de l'application sur base de votre nouveau service mobile</h2>

1. Dans le menu **Exécuter**, cliquez sur **Exécuter sans débogage** pour démarrer le projet. Vous êtes invité à choisir une image d'émulateur existante ou un appareil USB Android attaché.

	Cela permet d'exécuter votre application, créée avec Xamarin.Android, qui utilise la bibliothèque cliente pour envoyer une requête renvoyant des éléments à partir de votre service mobile.

5. Comme auparavant, tapez un texte explicite, puis cliquez sur **Ajouter**.

   	Cela permet d'envoyer un nouvel élément en tant qu'insertion au service mobile.

3. Dans le [portail de gestion], cliquez sur **Mobile Services**, puis sur le service mobile.

4. Cliquez sur l'onglet **Données**, puis sur **Parcourir**.

   	![][9]
  
   	La table **TodoItem** contient à présent des données, dont les valeurs d'ID ont été générées par Mobile Services, et les colonnes ont été automatiquement ajoutées à la table de manière à correspondre à la classe TodoItem au sein de l'application.

Cela conclut le didacticiel **Prise en main des données** pour Xamarin.Android.

## Téléchargement de l'exemple terminé
Téléchargez [l'exemple de projet terminé]. Veillez à mettre à jour les variables **applicationURL** et **applicationKey** avec vos propres paramètres Azure. 

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel a présenté les bases de l'activation d'une application Xamarin.Android pour utiliser les données dans Mobile Services. 

Ensuite, pensez à suivre l'un des didacticiels suivants basés sur l'application GetStartedWithData que vous avez créée dans ce didacticiel :

* [Validation et modification des données avec des scripts]
  <br/>En savoir plus sur l'utilisation des scripts serveur dans Mobile Services pour valider et modifier les données envoyées à partir de votre application.

* [Affinage des requêtes à la pagination.]
  <br/>En savoir plus sur l'utilisation de la pagination dans les requêtes pour contrôler la quantité de données traitées dans une seule requête.

Une fois que vous avez terminé les séries de données, essayez ces autres didacticiels Xamarin.Android :

* [Prise en main de l'authentification] 
	<br/>En savoir plus sur l'authentification des utilisateurs de votre application.

* [Prise en main des notifications Push] 
  <br/>En savoir plus sur l'envoi d'une notification Push très basique sur votre application avec Mobile Services.

<!-- Anchors. -->

[Obtenir l'application Windows Store]: #download-app
[Création du service mobile]: #create-service
[Ajout d'une table de données pour le stockage]: #add-table
[Mise à jour de l'application pour utiliser Mobile Services]: #update-app
[Test de l'application avec Mobile Services]: #test-app
[Étapes suivantes]:#next-steps

<!-- Images. -->





[5]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-data-tab-empty.png
[6]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-create-todoitem-table.png
[8]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-dashboard-tab.png
[9]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-todoitem-data-browse.png
[13]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-quickstart-startup-android.png

<!-- URLs. TODO:: update 'Download the Android app project' download link, 'GitHub', completed project, etc. -->
[Validation et modification des données avec des scripts]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android
[Affinage des requêtes à la pagination.]: /fr-fr/develop/mobile/tutorials/add-paging-to-data-xamarin-android
[Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started-xamarin-android
[Prise en main des données]: /fr-fr/develop/mobile/tutorials/get-started-with-data-xamarin-android
[Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-xamarin-android
[Prise en main des notifications Push]: /fr-fr/develop/mobile/tutorials/get-started-with-push-xamarin-android

[Portail de gestion Azure]: https://manage.windowsazure.com/
[Portail de gestion]: https://manage.windowsazure.com/
[Composant Azure Mobile Services]: http://components.xamarin.com/view/azure-mobile-services/
[Téléchargement d'un projet d'application Android]: http://www.google.com/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331302
[Kit de développement logiciel Android]: https://go.microsoft.com/fwLink/p/?LinkID=280125

[Exemple de projet terminé]: http://go.microsoft.com/fwlink/p/?LinkId=331302
