<properties linkid="develop-mobile-tutorials-get-started-with-data-xamarin-android" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Xamarin.Android) - Azure Mobile Services" metaKeywords="Azure Xamarin.Android data, Azure mobile services data" description="Learn how to store and access data from your Azure Mobile Services Xamarin.Android app." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Get started with data in Mobile Services" documentationCenter="Mobile" authors="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author></tags>

# Prise en main des données dans Mobile Services

<div class="dev-center-tutorial-selector sublanding">    
    <a href="/en-us/develop/mobile/tutorials/get-started-with-data-dotnet" title="Windows Store C#">Windows Store C#</a><a href="/en-us/develop/mobile/tutorials/get-started-with-data-js" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/en-us/develop/mobile/tutorials/get-started-with-data-wp8" title="Windows Phone">Windows Phone</a><a href="/en-us/develop/mobile/tutorials/get-started-with-data-ios" title="iOS">iOS</a><a href="/en-us/develop/mobile/tutorials/get-started-with-data-android" title="Android">Android</a><a href="/en-us/develop/mobile/tutorials/get-started-with-data-html" title="HTML">HTML</a><a href="/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a>
</div>

Cette rubrique montre comment utiliser Azure Mobile Services pour tirer parti des données dans une application Xamarin.Android. Dans ce didacticiel, vous allez télécharger une application qui stocke les données en mémoire, créer un service mobile, intégrer le service mobile à l'application, puis vous connecter au portail de gestion Azure pour afficher les modifications apportées aux données lors de l'exécution de l'application.

<div class="dev-callout"><br><b>Remarque</b>
<p>Ce didacticiel vise &agrave; mieux vous faire comprendre en quoi Mobile Services vous permet de stocker et de r&eacute;cup&eacute;rer des donn&eacute;es &agrave; partir d'une application Xamarin.Android &agrave; l'aide d'Azure. &Agrave; cet effet, il vous guide tout au long des nombreuses &eacute;tapes ex&eacute;cut&eacute;es automatiquement dans le d&eacute;marrage rapide Mobile Services. Si vous n'avez aucune exp&eacute;rience de Mobile Services, nous vous invitons &agrave; suivre d'abord le didacticiel <a href="/en-us/develop/mobile/tutorials/get-started-xamarin-android">Prise en main de Mobile Services</a>.</p>
</div>

Ce didacticiel vous familiarise avec ces étapes de base :

1.  [Téléchargement du projet d'application Xamarin.Android][]
2.  [Création du service mobile][]
3.  [Ajout d'une table de données pour le stockage][]
4.  [Mise à jour de l'application pour utiliser Mobile Services][]
5.  [Test de l'application avec Mobile Services][]

<div class="dev-callout"><br><strong>Remarque</strong> <p>Pour effectuer ce didacticiel, vous avez besoin d&rsquo;un compte Azure. Si vous ne poss&eacute;dez pas de compte, vous pouvez cr&eacute;er un compte d'&eacute;valuation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A9C9624B5" target="_blank">Version d'&eacute;valuation gratuite d'Azure</a>.</p></div>

Ce didacticiel requiert le [composant Azure Mobile Services][], [Xamarin.Android] et le Kit de développement logiciel (SDK) Android 4.2 ou ultérieur.

<div class="dev-callout"><br><b>Remarque</b>
<p>Le projet GetStartedWithData t&eacute;l&eacute;charg&eacute; requiert Android&nbsp;4.2 ou une version ult&eacute;rieure. Toutefois, le Kit de d&eacute;veloppement logiciel (SDK) Mobile Services requiert uniquement Android&nbsp;2.2 ou une version ult&eacute;rieure.</p>
</div>

## <a name="download-app"></a><span class="short-header">Téléchargement du projet</span>Téléchargement du projet GetStartedWithData

Ce didacticiel est basé sur l'[application GetStartedWithData][Téléchargement du projet d'application Xamarin.Android], qui est une application Xamarin.Android. L'interface utilisateur de cette application est identique à l'application générée par le démarrage rapide Android de Mobile Services, sauf que les éléments ajoutés sont stockés en local dans la mémoire.

1.  Téléchargez l'exemple d'application `GetStartedWithData`, puis décompressez les fichiers sur votre ordinateur.

2.  Dans Xamarin Studio, cliquez sur **File**, puis **Open**, accédez au dossier où vous avez décompressé l'exemple de projet GetStartedWithData, puis sélectionnez **XamarinTodoQuickStart.Android.sln** et ouvrez-le.

3.  Recherchez et ouvrez la classe **TodoActivity**.

    Des commentaires `// TODO::` indiquent les étapes à suivre pour que cette application fonctionne avec votre service mobile.

4.  À partir du menu **Exécuter**, cliquez sur **Exécuter sans débogage**. Vous êtes invité à choisir un émulateur ou un appareil USB Android attaché.

    <div class="dev-callout"><strong>Remarque</strong> <p>Vous pouvez ex&eacute;cuter ce projet &agrave; l'aide d'un t&eacute;l&eacute;phone Android ou de l'&eacute;mulateur Android. L'ex&eacute;cution avec un t&eacute;l&eacute;phone Android requiert le t&eacute;l&eacute;chargement d'un pilote USB propre au t&eacute;l&eacute;phone.</p> <p>Pour ex&eacute;cuter le projet dans l'&eacute;mulateur Android, vous devez d&eacute;finir au moins un AVD (appareil virtuel Android). Utilisez le gestionnaire AVD pour cr&eacute;er et g&eacute;rer ces appareils.</p></div>

5.  Dans l'application, tapez un texte explicite, comme *Suivre le didacticiel*, puis cliquez sur **Ajouter**.

    ![][]

    Notez que le texte enregistré est stocké dans une collection en mémoire et affiché dans la liste ci-dessous.

## <a name="create-service"></a><span class="short-header">Création d'un service mobile</span>Création d'un service mobile dans le portail de gestion

[WACOM.INCLUDE [mobile-services-create-new-service-data][]]

## <a name="add-table"></a><span class="short-header">Ajout d'une nouvelle table</span>Ajout d'une nouvelle table au service mobile

Avant de pouvoir stocker des données d'application dans le nouveau service mobile, vous devez créer une table.

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

    Le composant du Kit de développement logiciel (SDK) Mobile Services est ajouté au projet.

2.  Ouvrez le fichier **AndroidManifest.xml** et assurez-vous que la ligne d'autorisation suivante existe :

        <uses-permission android:name="android.permission.INTERNET" />

    L'application peut ainsi accéder à Mobile Services dans Azure.

3.  Dans la fenêtre **Solution**, ouvrez la classe **TodoActivity**, puis annulez les marques de commentaire sur la ligne de code suivante :

        using Microsoft.WindowsAzure.MobileServices;

4.  Nous allons supprimer la liste en mémoire actuellement utilisée par l'application pour la remplacer par un service mobile. Dans la classe **TodoActivity**, placez la ligne de code suivante en commentaire, qui définit la liste **todoItemList** existante.

        public List<TodoItem> todoItemList = new ArrayList<TodoItem>();

5.  Une fois l'étape précédente effectuée, le projet indique des erreurs de build. Recherchez les trois emplacements restants où la variable `todoItemList` est utilisée et placez les sections indiquées en commentaire.

6.  Nous ajoutons maintenant notre service mobile. Annulez les marques de commentaire sur les lignes de code suivantes :

        private MobileServiceClient client; // Mobile Service Client references
        private IMobileServiceTable<TodoItem> todoTable; // Mobile Service Table used to access data   

7.  Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

8.  Cliquez sur l'onglet **Tableau de bord** et notez la valeur **URL du site**, puis cliquez sur **Gérer les clés** et notez la valeur de **Clé de l'application**.

    ![][3]

    Ces valeurs sont nécessaires pour accéder au service mobile à partir de votre code d'application.

9.  Dans la classe **Constants**, annulez les marques de commentaire au niveau des variables de membres suivantes :

        public const string ApplicationURL = @"AppUrl";
        public const string ApplicationKey = @"AppKey";

10. Remplacez **AppUrl** et **AppKey** dans les variables ci-dessus par les valeurs extraites du portail de gestion plus haut.

11. Dans la méthode **OnCreate**, annulez les marques de commentaire sur les lignes de code suivantes qui définissent la variable **MobileServiceClient** :

        // Create the Mobile Service Client instance, using the provided
        // Mobile Service URL and key
        client = new MobileServiceClient(
            Constants.ApplicationURL,
            Constants.ApplicationKey).WithFilter(filter);

        // Get the Mobile Service Table instance to use
        todoTable = client.GetTable<TodoItem>();    

    Cela crée une instance de MobileServiceClient utilisée pour accéder au service mobile. Cela crée également l'instance de MobileServiceTable utilisée pour traiter par proxy le stockage des données dans le service mobile.

12. Recherchez la classe ProgressFilter en bas du fichier et annulez les marques de commentaire sur cette dernière. Cette classe affiche un indicateur « chargement » lorsque MobileServiceClient exécute des opérations réseau.

13. Annulez les marques de commentaire sur ces lignes de la méthode **CheckItem** :

        try {
            await todoTable.UpdateAsync(item);
            if (item.Complete)
                adapter.Remove(item);
        } catch (Exception e) {
            CreateAndShowDialog(e, "Error");
        }

    Cela permet d'envoyer une mise à jour de l'élément au service mobile et de supprimer les éléments cochés de l'adaptateur.

14. Annulez les marques de commentaire sur ces lignes de la méthode **AddItem** :

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

15. Annulez les marques de commentaire sur ces lignes de la méthode **RefreshItemsFromTableAsync** :

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

## <a name="test-app"></a><span class="short-header">Test de l'application</span>Test de l'application avec votre nouveau service mobile

1.  Dans le menu **Exécuter**, cliquez sur **Exécuter sans débogage** pour démarrer le projet. Vous êtes invité à choisir une image d'émulateur existante ou un appareil USB Android attaché.

    Cela permet d'exécuter votre application, créée avec Xamarin.Android, qui utilise la bibliothèque cliente pour envoyer une requête renvoyant des éléments à partir de votre service mobile.

2.  Comme auparavant, tapez un texte explicite, puis cliquez sur **Ajouter**.

    Cela permet d'envoyer un nouvel élément en tant qu'insertion au service mobile.

3.  Dans le [portail de gestion][], cliquez sur **Mobile Services**, puis sur le service mobile.

4.  Cliquez sur l'onglet **Données**, puis sur **Parcourir**.

    ![][4]

    La table **TodoItem** contient à présent des données, dont les valeurs d'ID ont été générées par Mobile Services, et les colonnes ont été automatiquement ajoutées à la table de manière à correspondre à la classe TodoItem au sein de l'application.

Cela conclut le didacticiel **Prise en main des données** pour Xamarin.Android.

## Téléchargement de l'exemple terminé

Téléchargez le [projet d'exemple terminé][Téléchargement du projet d'application Xamarin.Android]. Veillez à mettre à jour les variables **applicationURL** et **applicationKey** avec vos propres paramètres Azure.

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel a présenté les bases de l'activation d'une application Xamarin.Android pour utiliser les données dans Mobile Services.

Ensuite, pensez à suivre l'un des didacticiels suivants basés sur l'application GetStartedWithData que vous avez créée dans ce didacticiel :

-   [Validation et modification des données avec des scripts][]
    En savoir plus sur l'utilisation des scripts serveur dans Mobile Services pour valider et modifier les données envoyées à partir de votre application.

-   [Affinage des requêtes à la pagination][]
    En savoir plus sur l'utilisation de la pagination dans les requêtes pour contrôler la quantité de données traitées dans une seule requête.

Une fois que vous avez terminé les séries de données, essayez ces autres didacticiels Xamarin.Android :

-   [Prise en main de l'authentification][]
    En savoir plus sur l'authentification des utilisateurs de votre application.

-   [Prise en main des notifications Push][]
    En savoir plus sur l'envoi d'une notification Push très basique sur votre application avec Mobile Services.

<!-- Anchors. --> 

<!-- Images. --> 

<!-- URLs. TODO:: update 'Download the Android app project' download link, 'GitHub', completed project, etc. -->

  [Windows Store C\#]: /en-us/develop/mobile/tutorials/get-started-with-data-dotnet "Windows Store C#"
  [Windows Store JavaScript]: /en-us/develop/mobile/tutorials/get-started-with-data-js "Windows Store JavaScript"
  [Windows Phone]: /en-us/develop/mobile/tutorials/get-started-with-data-wp8 "Windows Phone"
  [iOS]: /en-us/develop/mobile/tutorials/get-started-with-data-ios "iOS"
  [Android]: /en-us/develop/mobile/tutorials/get-started-with-data-android "Android"
  [HTML]: /en-us/develop/mobile/tutorials/get-started-with-data-html "HTML"
  [Xamarin.iOS]: /en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios "Xamarin.iOS"
  [Xamarin.Android]: /en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android "Xamarin.Android"
  [Prise en main de Mobile Services]: /en-us/develop/mobile/tutorials/get-started-xamarin-android
  [Téléchargement du projet d'application Xamarin.Android]: http://go.microsoft.com/fwlink/p/?LinkId=331302
  [Création du service mobile]: #create-service
  [Ajout d'une table de données pour le stockage]: #add-table
  [Mise à jour de l'application pour utiliser Mobile Services]: #update-app
  [Test de l'application avec Mobile Services]: #test-app
  [Version d'évaluation gratuite d'Azure]: http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A9C9624B5
  [composant Azure Mobile Services]: http://components.xamarin.com/view/azure-mobile-services/
  []: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-quickstart-startup-android.png
  [mobile-services-create-new-service-data]: ../includes/mobile-services-create-new-service-data.md
  [1]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-data-tab-empty.png
  [2]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-create-todoitem-table.png
  [3]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-dashboard-tab.png
  [portail de gestion]: https://manage.windowsazure.com/
  [4]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-todoitem-data-browse.png
  [Validation et modification des données avec des scripts]: /en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android
  [Affinage des requêtes à la pagination]: /en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-android
  [Prise en main de l'authentification]: /en-us/develop/mobile/tutorials/get-started-with-users-xamarin-android
  [Prise en main des notifications Push]: /en-us/develop/mobile/tutorials/get-started-with-push-xamarin-android
