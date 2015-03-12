<properties 
	pageTitle="Prise en main des données (HTML 5) | Centre de développement mobile" 
	description="Découvrez comment utiliser Mobile Services pour tirer profit des données de votre application HTML." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-html" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="09/24/2014" 
	ms.author="glenga"/>

# Ajout de Mobile Services à une application existante

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

Cette rubrique montre comment utiliser Azure Mobile Services pour tirer parti des données dans une application HTML. Dans ce didacticiel, vous allez télécharger une application qui stocke les données en mémoire, créer un service mobile, intégrer le service mobile à l'application, puis vous connecter au portail de gestion Azure pour afficher les modifications apportées aux données lors de l'exécution de l'application.

>[AZURE.NOTE]Ce didacticiel vise à mieux vous faire comprendre en quoi Mobile Services vous permet de stocker et de récupérer des données à partir d'une application HTML à l'aide d'Azure. À cet effet, il vous guide tout au long des nombreuses étapes exécutées automatiquement dans le démarrage rapide Mobile Services. Si vous n'avez aucune expérience de Mobile Services, nous vous invitons à suivre d'abord le didacticiel <a href="/fr-fr/develop/mobile/tutorials/get-started-html">Prise en main de Mobile Services</a>.

Ce didacticiel vous familiarise avec ces étapes de base :

1. [Téléchargement du projet d'application HTML]
2. [Création du service mobile]
3. [Ajout d'une table de données pour le stockage]
4. [Mise à jour de l'application pour utiliser Mobile Services]
5. [Test de l'application par rapport à Mobile Services]

> [AZURE.IMPORTANT] Pour suivre ce didacticiel, vous avez besoin d'un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-html%2F"%20target="_blank).

###Conditions supplémentaires

Vous pouvez héberger l'application GetStartedWithData sur n'importe quel serveur web. Cependant, des scripts sont fournis afin de vous permettre d'exécuter l'application sur `http://localhost:8000`.
 
+ Pour utiliser localhost, l'un des serveurs Web suivants doit être exécuté sur votre ordinateur local pour pouvoir suivre ce didacticiel :

	+  **Sur Windows** : IIS Express. IIS Express est installé par [Microsoft Web Platform Installer].   
	+  **Sur MacOS X** : Python, qui doit déjà être installé.
	+  **Sur Linux** : Python. Vous devez installer [la dernière version de Python]. 
	
	Vous pouvez utiliser n'importe quel serveur web pour héberger l'application, mais les serveurs précédents sont les seuls pris en charge par les scripts téléchargés.  

+ Un navigateur web qui prend en charge le HTML5

<h2><a name="download-app"></a>Téléchargez le projet GetStartedWithData.</h2>

Ce didacticiel est basé sur [l'application GetStartedWithData], qui est une application HTML5. L'interface utilisateur de cette application est identique à l'application générée par le démarrage rapide de Mobile Services, sauf que les éléments ajoutés sont stockés en local dans la mémoire. 

1. [Téléchargez les fichiers du projet d'application HTML][Application GetStartedWithData].

2. Dans un éditeur HTML, ouvrez le projet téléchargé et étudiez le fichier app.js.

   	Notez que les éléments ajoutés sont stockés dans un objet **Array** en mémoire (**staticItems**). Actualisez la page pour afficher les données. Elles ne persistent pas.

3. Lancez un des fichiers de commande depuis le sous-dossier **server**.

	+ **launch-windows** (Pour les ordinateurs Windows) 
	+ **launch-mac.command** (Pour les ordinateurs Mac OS X)
	+ **launch-linux.sh** (Pour les ordinateurs Linux)

	> [AZURE.NOTE] Sur un ordinateur Windows, appuyez sur la touche " R " lorsque PowerShell vous demande de confirmer l'exécution du script. Vous pouvez recevoir un avertissement de votre navigateur web vous recommandant de ne pas exécuter le script, car il a été téléchargé depuis Internet. Dans ce cas, vous devez demander au navigateur de continuer à charger le script
	
	Ceci démarre un serveur web sur votre ordinateur local pour pouvoir héberger la nouvelle application.

4. Ouvrez l'URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> dans un navigateur Web pour démarrer l'application.

5. Dans l'application, tapez un texte explicite, comme _Complete the tutorial_, dans **Entrer une nouvelle tâche**, puis cliquez sur **Ajouter**.

   	![][0]  

   	Notez que le texte enregistré est ajouté au tableau **staticItems** et que la page est actualisée pour afficher le nouvel élément.

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

  	Une nouvelle table de stockage **TodoItem** est créée avec les autorisations par défaut. Cela signifie que quiconque possédant la clé de l'application, qui est distribuée avec l'application, peut accéder aux données de la table et les modifier.

    > [AZURE.NOTE] Le même nom de table est utilisé dans le démarrage rapide avec Mobile Services. Toutefois, chaque table est créée dans un schéma spécifique pour un service mobile donné. Cela vise à éviter les collisions de données lorsque plusieurs services mobiles utilisent la même base de données.

4. Cliquez sur la nouvelle table **TodoItem** et vérifiez qu'aucune ligne de données n'est présente.

5. Cliquez sur l'onglet **Colonnes**. Vérifiez si les colonnes par défaut suivantes ont été automatiquement créées pour vous : 
	
	<table border="1" cellpadding="10">
 	<tr>
 	<th>Nom de la colonne</th>
 	<th>Type</th>
 	<th>Index</th>
 	</tr>
 	<tr>
 	<td>id</td>
 	<td>string</td>
 	<td>Indexed</td>
 	</tr>
 	<tr>
 	<td>__createdAt</td>
 	<td>date</td>
 	<td>Indexed</td>
 	</tr>
 	<tr>
 	<td>__updatedAt</td>
 	<td>date</td>
 	<td><font color="transparent">-</font></td>
 	</tr>
 	<tr>
 	<td>__version</td>
 	<td>timestamp (MSSQL)</td>
 	<td><font color="transparent">-</font></td>
 	</tr> 	
 	</table> 

  	Cela correspond à la configuration minimale requise pour une table dans Mobile Services. 

    > [AZURE.NOTE] Lorsque le schéma dynamique est activé sur votre service mobile, de nouvelles colonnes sont créées automatiquement lorsque des objets JSON sont envoyés au service mobile par une opération d'insertion ou de mise à jour.

6. Sous l'onglet **Configurer**, vérifiez que `localhost` figure déjà dans la liste **Autoriser les demandes à partir des noms d'hôte**, sous **Partage des ressources cross-origin (CORS)**. Si ce n'est pas le cas, entrez `localhost` dans le champ **Nom d'hôte**, puis cliquez sur **Enregistrer**.

  	![][11]

	> [AZURE.IMPORTANT] Si vous déployez l'application de démarrage rapide sur un serveur autre que localhost, vous devez ajouter le nom d'hôte du serveur Web à la liste **Autoriser les demandes à partir des noms d'hôte**. Pour plus d'informations, consultez la page [Partage des ressources cross-origin](http://msdn.microsoft.com/library/windowsazure/dn155871.aspx"%20target="_blank).

Vous pouvez maintenant utiliser le nouveau service mobile en tant que stockage des données pour l'application.

<h2><a name="update-app"></a>Mise à jour de l'application afin d'utiliser le service mobile pour l'accès aux données</h2>

Votre service mobile étant prêt, vous pouvez mettre à jour l'application pour stocker les éléments dans Mobile Services et non dans la collection locale. 

3. Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

4. Cliquez sur l'onglet **Tableau de bord** et notez la valeur **URL du site**, puis cliquez sur **Gérer les clés** et notez la valeur de **Clé de l'application**.

   	![][8]

  	Ces valeurs sont nécessaires pour accéder au service mobile à partir de votre code d'application.

1. Dans un éditeur Web, ouvrez le fichier projet index.html et ajoutez ce qui suit aux références de script de la page :

        <script src='http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.1.2.min.js'></script>

5. Dans l'éditeur, ouvrez le fichier app.js, supprimez les marques de commentaire du code suivant qui définit la variable **MobileServiceClient** et fournissez l'URL et la clé d'application du service mobile dans le constructeur **MobileServiceClient**, dans cet ordre :

	    var MobileServiceClient = WindowsAzure.MobileServiceClient,
			client = new MobileServiceClient('AppUrl', 'AppKey'),   		    

  	Cela crée une instance de MobileServiceClient utilisée pour accéder au service mobile.

6. Supprimez les marques de commentaire sur les lignes de code suivantes :

		var itemCount = 0;
		var staticItems = [];

	Les données seront stockées dans le service mobile et non dans le tableau en mémoire.

6. Supprimez les marques de commentaire sur la ligne de code suivante :

        todoItemTable = client.getTable('todoitem');

   	Ce code crée un objet proxy (**todoItemTable**) pour la base de données SQL **TodoItem**. 

7. Remplacez le gestionnaire d'événements **$('#add-item').submit** par le code suivant :

		$('#add-item').submit(function(evt) {
			var textbox = $('#new-item-text'),
				itemText = textbox.val();
			if (itemText !== '') {
				todoItemTable.insert({ text: itemText, complete: false })
					.then(refreshTodoItems);
			}
			textbox.val('').focus();
			evt.preventDefault();
		});


  	Ce code permet d'insérer un nouvel élément dans la table.

8. Remplacez la méthode **refreshTodoItems** par le code suivant :

		function refreshTodoItems() {

			var query = todoItemTable;

			query.read().then(function(todoItems) {
				listItems = $.map(todoItems, function(item) {
					return $('<li>')
						.attr('data-todoitem-id', item.id)
						.append($('<button class="item-delete">Delete</button>'))
						.append($('<input type="checkbox" class="item-complete">').prop('checked', item.complete))
						.append($('<div>').append($('<input class="item-text">').val(item.text)));
				});
					   
				$('#todo-items').empty().append(listItems).toggle(listIte
	ms.length > 0);
				$('#summary').html('<strong>' + todoIte
	ms.length + '</strong> item(s)');
			});
		}
	   

   Ce code envoie une requête au service mobile, qui renvoie tous les éléments. Le résultat est retraité et les données s'affichent sur la page. 

9. Remplacez les gestionnaires d'événements **$(document.body).on('change', '.item-text')** et **$(document.body).on('change', '.item-complete')** par le code suivant :
        
		$(document.body).on('change', '.item-text', function() {
			var newText = $(this).val();
			todoItemTable.update({ id: getTodoItemId(this), text: newText });
		});

		$(document.body).on('change', '.item-complete', function() {
			var isComplete = $(this).prop('checked');
			todoItemTable.update({ id: getTodoItemId(this), complete: isComplete })
				.then(refreshTodoItems);
		});
 
   	Ce code envoie une mise à jour de l'élément au service mobile lorsque du texte est modifié ou lorsque la case à cocher est activée.

10. Remplacez le gestionnaire d'événements **$(document.body).on('click', '.item-delete')** par le code suivant :

		$(document.body).on('click', '.item-delete', function () {
			todoItemTable.del({ id: getTodoItemId(this) }).then(refreshTodoItems);
		});

	Ce code envoie une demande de suppression au service mobile lorsque l'utilisateur clique sur le bouton **Supprimer**.

Maintenant que l'application a été mise à jour pour utiliser Mobile Services pour le stockage principal, le moment est venu de tester l'application avec Mobile Services.

<h2><a name="test-app"></a>Test de l'application sur la base de votre nouveau service mobile</h2>

4. Rechargez l'URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> dans un navigateur Web pour démarrer l'application.

    > [AZURE.NOTE] Si vous devez redémarrer le serveur Web, répétez les étapes de la première section.

2. Comme précédemment, tapez du texte dans **Entrer une nouvelle tâche**, puis cliquez sur **Ajouter**. 

   	Cela permet d'envoyer un nouvel élément en tant qu'insertion au service mobile.

3. Dans le [portail de gestion], cliquez sur **Mobile Services**, puis sur le service mobile.

4. Cliquez sur l'onglet **Données**, puis sur **Parcourir**.

   	![][9]
  
   	Notez que la table **TodoItem** contient à présent des données, dont les valeurs d'ID ont été générées par Mobile Services, et que des colonnes ont été automatiquement ajoutées à la table de manière à correspondre à la classe TodoItem au sein de l'application.

5. Dans l'application, cochez un des éléments dans la liste, puis revenez à l'onglet Parcourir du portail et cliquez sur **Actualiser**. 

  	Notez que la valeur complète est passée de **false** à **true**.

6. Dans le fichier projet app.js, recherchez la méthode **RefreshTodoItems** et remplacez la ligne de code qui définit `query` par ce qui suit :

   		var query = todoItemTable.where({ complete: false });

7. Rechargez la page, vérifiez un des autres éléments de la liste.

   	Notez que l'élément coché n'est plus affiché dans la liste. Chaque mise à jour provoque un aller-retour vers le serveur mobile, qui renvoie les données filtrées.

Vous voici parvenu à la fin du didacticiel **Prise en main des données**.

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel a présenté les bases de l'activation d'une application HTML pour utiliser les données dans Mobile Services. Ensuite, pensez à suivre l'un des didacticiels suivants basés sur l'application GetStartedWithData que vous avez créée dans ce didacticiel :

* [Validation et modification de données à l'aide de scripts]
  <br/>En savoir plus sur l'utilisation des scripts serveur dans Mobile Services pour valider et modifier les données envoyées à partir de votre application.

* [Affinage des requêtes au moyen de la pagination]
  <br/>Apprenez à utiliser la pagination dans les requêtes pour contrôler la quantité de données traitées dans une seule requête.
 
Une fois que vous avez terminé la série de données, découvrez comment authentifier les utilisateurs de votre application en suivant le didacticiel [Prise en main de l'authentification].

<!-- Anchors. -->
[Téléchargement du projet d'application HTML]: #download-app
[Création du service mobile]: #create-service
[Ajout d'une table de données pour le stockage]: #add-table
[Mise à jour de l'application pour utiliser Mobile Services]: #update-app
[Test de l'application par rapport à Mobile Services]: #test-app
[Étapes suivantes]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-get-started-data/mobile-quickstart-startup-html.png




[5]: ./media/mobile-services-html-get-started-data/mobile-data-tab-empty.png
[6]: ./media/mobile-services-html-get-started-data/mobile-create-todoitem-table.png

[8]: ./media/mobile-services-html-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-html-get-started-data/mobile-todoitem-data-browse.png

[11]: ./media/mobile-services-html-get-started-data/mobile-services-set-cors-localhost.png

<!-- URLs. -->
[Validation et modification de données à l'aide de scripts]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-html
[Affinage des requêtes au moyen de la pagination]: /fr-fr/develop/mobile/tutorials/add-paging-to-data-html
[Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started
[Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-html

[Portail de gestion Azure]: https://manage.windowsazure.com/
[Portail de gestion]: https://manage.windowsazure.com/
[Application GetStartedWithData]:  http://go.microsoft.com/fwlink/?LinkID=286345

[Guide de fonctionnement de Mobile Services avec HTML/JavaScript]: /fr-fr/develop/mobile/how-to-guides/work-with-html-js-client

[Partage des ressources cross-origin]: http://msdn.microsoft.com/library/windowsazure/dn155871.aspx



<!--HONumber=42-->
