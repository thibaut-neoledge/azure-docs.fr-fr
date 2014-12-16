<properties urlDisplayName="Get Started with Data" pageTitle="Prise en main des données (JavaScript Windows Store) | Centre de développement mobile" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store JavaScript app." metaCanonical="https://www.windowsazure.com/fr-fr/develop/mobile/tutorials/get-started-with-data-dotnet/" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/19/2014" ms.author="glenga" />


# Ajout de services mobiles à une application existante

[WACOM.INCLUDE [mobile-services-selector-get-started-data-legacy](../includes/mobile-services-selector-get-started-data-legacy.md)]

<div class="dev-center-tutorial-subselector">
	<a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/" title=".NET backend">Service principal .NET</a> | 
	<a href="/fr-fr/documentation/articles/mobile-services-windows-store-javascript-get-started-data/" title="JavaScript backend" class="current">Service principal JavaScript</a>
</div>


Cette rubrique vous présente l'utilisation d'Azure Mobile Services pour utiliser les données dans une application Windows Store. Dans ce didacticiel, vous allez télécharger un projet Visual Studio 2013 pour une application qui stocke les données en mémoire, créer un service mobile, intégrer le service mobile avec l'application, puis vous connecter au portail de gestion Azure pour afficher les modifications effectuées sur les données lors de l'exécution de l'application.

>[WACOM.NOTE]Cette rubrique vous présente l'utilisation de Visual Studio 2013 pour ajouter Azure Mobile Services à un projet Windows Store. Vous pouvez ajouter le même service mobile principal JavaScript à un projet d'application Windows universelle. Pour plus d'informations, consultez la page version [application Windows universelle](/fr-fr/documentation/articles/mobile-services-javascript-backend-windows-universal-javascript-get-started-data) de ce didacticiel.

Ce didacticiel vous familiarise avec ces étapes de base :

1. [Téléchargement d'un projet d'application Windows Store][Get the Windows Store app] 
2. [Création du service mobile]
3. [Ajout d'une table de données pour le stockage]
4. [Mise à jour de l'application pour utiliser Mobile Services]
5. [Test de l'application avec Mobile Services]

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/fr-fr/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Ffr-fr%2Fdocumentation%2Farticles%2Fmobile-services-windows-store-javascript-get-started-data%2F).
* Visual Studio 2013, qui facilite la connexion de votre application Windows Store à Mobile Services. Pour effectuer la même procédure de base à l'aide de Visual Studio 2012, suivez la procédure de la rubrique <a href="/fr-fr/documentation/articles/mobile-services-windows-store-javascript-get-started-data-vs2012">Prise en main des données dans Mobile Services à l'aide de Visual Studio 2012</a>. 

<h2><a name="download-app"></a>Téléchargement du projet GetStartedWithData</h2>

Ce didacticiel est basé sur l'application [GetStartedWithMobileServices][Developer Code Samples site], qui est un projet d'application Windows Store dans Visual Studio 2013. L'interface utilisateur de cette application est identique à l'application générée par le démarrage rapide de Mobile Services, sauf que les éléments ajoutés sont stockés en local dans la mémoire.  

1. Téléchargez la version JavaScript de l'exemple d'application GetStartedWithData sur le [site d'exemples de code développeur]. 

2. Dans Visual Studio 2012 Express pour Windows 8, ouvrez le projet téléchargé, développez le dossier **js** et examinez le fichier default.js.

   	Notez que les objets **TodoItem** ajoutés sont stockés dans un objet **List** en mémoire.

3. Appuyez sur la touche **F5** pour régénérer le projet et démarrer l'application.

4. Dans l'application, tapez du texte dans **Insert a TodoItem**, puis cliquez sur **Enregistrer**.

   	![][0]  

   	Notez que le texte enregistré est affiché dans la deuxième colonne sous **Query and update data**.

##<a name="create-service"></a>Création d'un service mobile à partir de Visual Studio

[WACOM.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

<ol start="7">
<li><p>Dans l'Explorateur de solutions, développez les dossiers **services**, **mobile services**, **&lt;your_service&gt;**, ouvrez le fichier de script service.js, et notez la nouvelle variable globale, similaire à celle présentée dans l'exemple suivant :</p> 

		<pre><code>var todolistClient = new WindowsAzure.MobileServiceClient(
                "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");</code></pre>

	<p>Ce code permet d'accéder à votre nouveau service mobile dans votre application en utilisant une variable globale. Le client est créé en fournissant l'URI et la clé de l'application du nouveau service mobile. Dans la mesure où une référence à ce script a été ajoutée dans le fichier default.html, cette variable est disponible pour tous les fichiers de script qui sont également référencés sur cette page.</p>
</li>
</ol>

##<a name="add-table"></a>Ajout d'une table pour le stockage des données

[WACOM.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

>[WACOM.NOTE]Des tables sont créées avec les colonnes Id, __createdAt, __updatedAt et __version. Lorsqu'un schéma dynamique est activé, Mobile Services génère automatiquement de nouvelles colonnes basées sur l'objet JSON dans la requête d'insertion ou de mise à jour. Pour plus d'informations, consultez la page [Schéma dynamique](http://msdn.microsoft.com/fr-fr/library/windowsazure/jj193175.aspx).

#<a name="update-app"></a>Mise à jour de l'application pour utiliser le service mobile

[WACOM.INCLUDE [mobile-services-windows-javascript-update-data-app](../includes/mobile-services-windows-javascript-update-data-app.md)]

##<a name="test-app"></a>Test de l'application sur base de votre nouveau service mobile

1. Dans Visual Studio, appuyez sur la touche F5 pour exécuter l'application.

2. Comme précédemment, tapez du texte dans **Insert a TodoItem**, puis cliquez sur **Enregistrer**.

   	Cela permet d'envoyer un nouvel élément en tant qu'insertion au service mobile.

3. Dans le [portail de gestion], cliquez sur **Mobile Services**, puis sur le service mobile.

4. Cliquez sur l'onglet **Données**, puis sur **Parcourir**.

   	![][9]
  
   	La table **TodoItem** contient à présent des données, dont les valeurs d'ID ont été générées par Mobile Services, et les colonnes ont été automatiquement ajoutées à la table de manière à correspondre à la classe TodoItem au sein de l'application.

5. Dans l'application, cochez un des éléments dans la liste, puis revenez à l'onglet Parcourir dans le portail et cliquez sur **Actualiser**. 

  	Notez que la valeur terminée est passée de **false** à **true**.

6. Dans le fichier projet default.js, remplacez la fonction **RefreshTodoItems** existante par le code suivant qui filtre les éléments terminés :

        var refreshTodoItems = function () {                     
            // More advanced query that filters out completed items. 
            todoTable.where({ complete: false })
               .read()
               .done(function (results) {
                   todoItems = new WinJS.Binding.List(results);
                   listItems.winControl.itemDataSource = todoItems.dataSource;
               });            
        };

7. Dans l'application, cochez un autre élément dans la liste, puis cliquez sur le bouton **Actualiser**.

   	Notez que l'élément coché n'est plus affiché dans la liste. Chaque actualisation provoque un aller-retour vers le serveur mobile, qui renvoie les données filtrées.

Cela conclut le didacticiel **Prise en main des données**.

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel a présenté les bases de l'activation d'une application Windows Store pour utiliser les données dans Mobile Services. Ensuite, pensez à suivre l'un des didacticiels suivants basés sur l'application GetStartedWithData que vous avez créée dans ce didacticiel :

* [Validation et modification des données avec des scripts]
  <br/>En savoir plus sur l'utilisation des scripts serveur dans Mobile Services pour valider et modifier les données envoyées à partir de votre application.

* [Affinage des requêtes à la pagination.]
  <br/>En savoir plus sur l'utilisation de la pagination dans les requêtes pour contrôler la quantité de données traitées dans une seule requête.

Une fois que vous avez terminé les séries de données, essayez l'un de ces autres didacticiels :

* [Prise en main de l'authentification]
  <br/>En savoir plus sur l'authentification des utilisateurs de votre application.

* [Prise en main des notifications Push] 
  <br/>En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

* [Guide de fonctionnement de Mobile Services avec HTML/JavaScript]
  <br/>En savoir plus sur l'utilisation de Mobile Services avec HTML et JavaScript

<!-- Anchors. -->

[Obtenir l'application Windows Store]: #download-app
[Création du service mobile]: #create-service
[Ajout d'une table de données pour le stockage]: #add-table
[Mise à jour de l'application pour utiliser Mobile Services]: #update-app
[Test de l'application avec Mobile Services]: #test-app
[Étapes suivantes]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-quickstart-startup.png

[9]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-todoitem-data-browse.png
[10]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-data-sample-download-js-vs12.png


<!-- URLs. -->
[Validation et modification des données avec des scripts]: /fr-fr/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/
[Affinage des requêtes à la pagination.]: /fr-fr/documentation/articles/mobile-services-windows-store-javascript-add-paging-data/
[Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started/
[Prise en main des données]: /fr-fr/documentation/articles/mobile-services-windows-store-javascript-get-started-data/
[Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
[Prise en main des notifications Push]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push/

[Portail de gestion Azure]: https://manage.windowsazure.com/
[Portail de gestion]: https://manage.windowsazure.com/
[Kit de développement logiciel (SDK) Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=257545
[Site d'exemples de code développeur]:  http://go.microsoft.com/fwlink/p/?LinkId=328660
[Guide de fonctionnement de Mobile Services avec HTML/JavaScript]: /fr-fr/documentation/articles/mobile-services-html-how-to-use-client-library/
