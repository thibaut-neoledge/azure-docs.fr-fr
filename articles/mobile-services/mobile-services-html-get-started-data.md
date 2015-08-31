<properties 
	pageTitle="Ajout de Mobile Services à une application existante (HTML 5) | Microsoft Azure" 
	description="Découvrez comment commencer à utiliser Mobile Services dans une application HTML existante." 
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
	ms.date="08/16/2015" 
	ms.author="glenga"/>

# Ajout de services mobiles à une application existante

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

##Vue d'ensemble 

Cette rubrique montre comment utiliser Azure Mobile Services pour tirer parti des données dans une application HTML. Dans ce didacticiel, vous allez télécharger une application qui stocke les données en mémoire, créer un service mobile, intégrer le service mobile à l'application, puis vous connecter au portail de gestion Azure pour afficher les modifications apportées aux données pendant l'exécution de l'application.

>[AZURE.NOTE]Ce didacticiel vise à mieux vous faire comprendre en quoi Mobile Services vous permet de stocker et de récupérer des données à partir d'une application HTML à l'aide d'Azure. À cet effet, il vous guide tout au long des nombreuses étapes exécutées automatiquement dans le démarrage rapide Mobile Services. Si vous n'avez aucune expérience de Mobile Services, nous vous invitons à suivre d'abord le didacticiel [Prise en main de Mobile Services].

> [AZURE.IMPORTANT]Pour suivre ce didacticiel, vous avez besoin d'un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2documentation%2Farticles%2Fmobile-services-html-get-started-data).

###Conditions supplémentaires

Vous pouvez héberger l'application GetStartedWithData sur n'importe quel serveur Web. Cependant, des scripts sont fournis afin de vous permettre d'exécuter l'application sur `http://localhost:8000`.
 
+ Pour utiliser localhost, l'un des serveurs Web suivants doit être exécuté sur votre ordinateur local pour pouvoir suivre ce didacticiel :

	+  **Sur Windows** : IIS Express. IIS Express est installé par [Microsoft Web Platform Installer].   
	+  **Sur MacOS X** : Python, qui doit déjà être installé.
	+  **Sur Linux** : Python. Vous devez installer [la dernière version de Python]. 
	
	Vous pouvez utiliser n'importe quel serveur Web pour héberger l'application, mais les serveurs précédents sont les seuls pris en charge par les scripts téléchargés.

+ Un navigateur Web qui prend en charge le HTML5

##<a name="download-app"></a>Téléchargement du projet GetStartedWithData

Ce didacticiel est basé sur l'application [GetStartedWithData], qui est une application HTML5. L'interface utilisateur de cette application est identique à l'application générée par le démarrage rapide de Mobile Services, sauf que les éléments ajoutés sont stockés en local dans la mémoire.

1. [Téléchargement des fichiers du projet d'application HTML][GetStartedWithData app].

2. Dans un éditeur HTML, ouvrez le projet téléchargé et étudiez le fichier app.js.

   	Les éléments ajoutés sont stockés dans un objet **Array** en mémoire (**staticItems**). Actualisez la page pour afficher les données. Elles ne persistent pas.

3. Lancez un des fichiers de commande depuis le sous-dossier **server**.

	+ **launch-windows** (pour les ordinateurs Windows) 
	+ **launch-mac.command** (pour les ordinateurs Mac OS X)
	+ **launch-linux.sh** (pour les ordinateurs Linux)

	> [AZURE.NOTE]Sur un ordinateur Windows, appuyez sur la touche `R` quand PowerShell vous demande de confirmer l'exécution du script. Vous pouvez recevoir un avertissement de votre navigateur Web vous recommandant de ne pas exécuter le script, car il a été téléchargé depuis Internet. Dans ce cas, vous devez demander au navigateur de continuer à charger le script
	
	Ceci démarre un serveur Web sur votre ordinateur local pour pouvoir héberger la nouvelle application.

4. Ouvrez l'URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> dans un navigateur web pour démarrer l'application.

5. Dans l'application, tapez un texte explicite, comme _Suivre le didacticiel_, dans **Entrer une nouvelle tâche**, puis cliquez sur **Ajouter**.

   	![][0]

   	Le texte enregistré est ajouté au tableau **staticItems** et la page est mise à jour pour afficher le nouvel élément.

##<a name="create-service"></a>Création d'un service mobile dans le portail de gestion

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

##<a name="add-table"></a>Ajouter une nouvelle table au service mobile

Pour pouvoir stocker des données d'application dans le nouveau service mobile, vous devez d'abord créer une table dans l'instance de base de données SQL associée.

1. Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

2. Cliquez sur l'onglet **Données**, puis sur **+Create**.

   	La boîte de dialogue **Create new table** s’affiche.

3. Dans **Nom de la table**, tapez _TodoItem_, puis cliquez sur le bouton de vérification.

  	Une nouvelle table de stockage **TodoItem** est créée avec les autorisations par défaut. Cela signifie que quiconque possédant la clé de l’application, qui est distribuée avec l’application, peut accéder aux données de la table et les modifier. La table est créée dans un schéma spécifique pour un service mobile donné. Cela vise à éviter les collisions de données lorsque plusieurs services mobiles utilisent la même base de données.

4. Cliquez sur la nouvelle table **TodoItem** et vérifiez qu'aucune ligne de données n'est présente.

	>[AZURE.NOTE]Des tables sont créées avec les colonnes Id, \_\_createdAt, \_\_updatedAt et \_\_version. Lorsqu'un schéma dynamique est activé, Mobile Services génère automatiquement de nouvelles colonnes basées sur l'objet JSON dans la requête d'insertion ou de mise à jour. Pour plus d'informations, consultez la page [Schéma dynamique](http://msdn.microsoft.com/library/windowsazure/jj193175.aspx).

6. Sous l'onglet **Configurer** vérifiez que `localhost` figure déjà dans la liste **Autoriser les demandes à partir des noms d'hôte** sous **Partage des ressources cross-origin (CORS)**. Si cela n'est pas le cas, entrez `localhost` dans le champ **Nom d'hôte**, puis cliquez sur **Enregistrer**.


	> [AZURE.IMPORTANT]Si vous déployez l'application de démarrage rapide sur un autre serveur que localhost, vous devez ajouter le nom d'hôte du serveur Web à la liste **Autoriser les demandes à partir des noms d'hôte**. Pour plus d'informations, consultez la page [Partage des ressources cross-origin](http://msdn.microsoft.com/library/windowsazure/dn155871.aspx"%20target="_blank).

Vous pouvez maintenant utiliser le nouveau service mobile en tant que stockage des données pour l’application.

##<a name="update-app"></a>Mettre à jour l'application afin d'utiliser le service mobile pour l'accès aux données

Votre service mobile étant prêt, vous pouvez mettre à jour l'application pour stocker les éléments dans Mobile Services et non dans la collection locale.

3. Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

4. Cliquez sur l'onglet **Tableau de bord** et notez la valeur **URL du service mobile**, puis cliquez sur **Gérer les clés** et notez la valeur de **Clé de l'application**.

  	Ces valeurs sont nécessaires pour accéder au service mobile à partir de votre code d'application.

1. Dans un éditeur Web, ouvrez le fichier projet index.html et ajoutez ce qui suit aux références de script de la page :

        <script src="http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.5.min.js"></script>

5. Dans l'éditeur, ouvrez le fichier JavaScript, supprimez les marques de commentaire du code suivant qui définit la variable **MobileServiceClient** et fournissez l'URL et la clé d'application du service mobile dans le constructeur **MobileServiceClient**, dans cet ordre :

	    var MobileServiceClient = WindowsAzure.MobileServiceClient,
			client = new MobileServiceClient('AppUrl', 'AppKey'),   		    

  	Cela crée une instance de MobileServiceClient utilisée pour accéder au service mobile.

6. Supprimez les marques de commentaire sur les lignes de code suivantes :

		var itemCount = 0;
		var staticItems = [];

	Les données seront stockées dans le service mobile et non dans le tableau en mémoire.

6. Supprimez les marques de commentaire sur la ligne de code suivante :

        todoItemTable = client.getTable('todoitem');

   	Ce code crée un objet proxy (**todoItemTable**) pour la base de données SQL **TodoItem**.

7. Remplacez le gestionnaire d'événements **$('#add-item').submit** par le code suivant :

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

8. Remplacez la méthode **refreshTodoItems** par le code suivant :

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
					   
				$('#todo-items').empty().append(listItems).toggle(listItems.length > 0);
				$('#summary').html('<strong>' + todoItems.length + '</strong> item(s)');
			});
		}
	   

   Ce code envoie une requête au service mobile, qui renvoie tous les éléments. Le résultat est retraité et les données s'affichent sur la page.

9. Remplacez les gestionnaires d'événement **$(document.body).on('change', '.item-text')** et **$(document.body).on('change', '.item-complete')** par le code suivant :
        
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

10. Remplacez le gestionnaire d'événements **$(document.body).on('click', '.item-delete')** par le code suivant :

		$(document.body).on('click', '.item-delete', function () {
			todoItemTable.del({ id: getTodoItemId(this) }).then(refreshTodoItems);
		});

	Ce code envoie une demande de suppression au service mobile lorsque l'utilisateur clique sur le bouton **Delete**.

Maintenant que l'application a été mise à jour pour utiliser Mobile Services pour le stockage principal, le moment est venu de tester l'application avec Mobile Services.

##<a name="test-app"></a>Test de l'application avec votre nouveau service mobile

4. Rechargez l'URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> dans un navigateur web pour démarrez l'application.

    > [AZURE.NOTE]Si vous devez redémarrer le serveur Web, répétez les étapes de la première section.

2. Comme précédemment, tapez du texte dans **Entrer une nouvelle tâche**, puis cliquez sur **Ajouter**.

   	Cela permet d'envoyer un nouvel élément en tant qu'insertion au service mobile.

3. Dans le [portail de gestion], cliquez sur **Mobile Services**, puis sur le service mobile.

4. Cliquez sur l'onglet **Données**, puis sur **Parcourir**.
  
   	La table **TodoItem** contient à présent des données, dont les valeurs d'ID ont été générées par Mobile Services, et les colonnes ont été automatiquement ajoutées à la table de manière à correspondre à la classe TodoItem au sein de l'application.

5. Dans l'application, cochez un des éléments dans la liste, puis revenez à l'onglet Parcourir dans le portail et cliquez sur **Actualiser**.

  	Notez que la valeur terminée est passée de **false** à **true**.

6. Dans le fichier projet app.js, recherchez la méthode **RefreshTodoItems** et remplacez la ligne de code qui définit `query` par ce qui suit :

   		var query = todoItemTable.where({ complete: false });

7. Rechargez la page, vérifiez un des autres éléments de la liste.

   	Notez que l'élément coché n'est plus affiché dans la liste. Chaque mise à jour provoque un aller-retour vers le serveur mobile, qui renvoie les données filtrées.

Vous voici parvenu à la fin du didacticiel **Prise en main des données**.

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel a présenté les bases de l'activation d'une application HTML pour utiliser les données dans Mobile Services. Ensuite, découvrez comment authentifier les utilisateurs de votre application en suivant le didacticiel [Ajout de l'authentification à votre application]. Si vous utilisez un projet d'application Cordova ou PhoneGap, consultez [Notifications Push aux applications Cordova avec Microsoft Azure](https://msdn.microsoft.com/magazine/dn879353.aspx) pour en savoir plus sur les notifications Push.

<!-- Anchors. -->
[Download the HTML app project]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-get-started-data/mobile-quickstart-startup-html.png

<!-- URLs. -->
[Prise en main de Mobile Services]: mobile-services-html-get-started.md
[Ajout de l'authentification à votre application]: mobile-services-html-get-started-users.md

[Azure Management Portal]: https://manage.windowsazure.com/
[portail de gestion]: https://manage.windowsazure.com/
[GetStartedWithData app]: http://go.microsoft.com/fwlink/?LinkID=286345
[GetStartedWithData]: http://go.microsoft.com/fwlink/?LinkID=286345

[Mobile Services HTML/JavaScript How-to Conceptual Reference]: mobile-services-html-how-to-use-client-library.md

[Cross-origin resource sharing]: http://msdn.microsoft.com/library/azure/dn155871.aspx

 

<!---HONumber=August15_HO8-->