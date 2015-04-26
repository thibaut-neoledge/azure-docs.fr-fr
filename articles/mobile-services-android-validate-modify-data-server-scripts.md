<properties 
	pageTitle="Utilisation de scripts serveur pour valider et modifier des données (Android) | Centre de développement mobile" 
	description="Découvrez comment valider et modifier les données envoyées à l'aide de scripts serveurs à partir de votre application Android." 
	services="mobile-services" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="Mobile-Android" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="09/25/2014" 
	ms.author="ricksal"/>

# Validation et modification de données dans Mobile Services à l'aide de scripts serveur

[AZURE.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

Cette rubrique vous présente l'utilisation des scripts serveur dans Azure Mobile Services. Il est possible d'utiliser les scripts serveur inscrits dans un service mobile pour effectuer diverses opérations sur les données insérées et mises à jour, qu'il s'agisse de les valider ou de les modifier. Ce didacticiel vous apprend à définir et à inscrire les scripts serveur qui valident et modifient les données. Le comportement des scripts serveur ayant souvent un impact sur le client, vous allez également mettre à jour votre application Android pour tirer profit de ces nouveaux comportements.

Ce didacticiel vous familiarise avec ces étapes de base :

1. [Ajout de la validation de longueur de chaîne]
2. [Mise à jour du client pour la prise en charge de la validation]
3. [Ajout d'un horodatage lors d'une insertion]
4. [Mise à jour du client pour l'affichage de l'horodatage]

Ce didacticiel s'appuie sur la procédure et l'exemple d'application présentés dans le didacticiel précédent intitulé [Prise en main des données]. Avant de commencer ce didacticiel, vous devez tout d'abord effectuer [Prise en main des données].  

## <a name="string-length-validation"></a>Ajout de la validation

Il est toujours souhaitable de valider la longueur des données soumises par les utilisateurs. Vous devez d'abord inscrire un script qui valide la longueur des données de chaîne envoyées au service mobile et refuse les chaînes trop longues, en l'occurrence, celles qui font plus de 10 caractères.

1. Connectez-vous au [portail de gestion Azure], cliquez sur **Mobile Services**, puis sur l'application. 

   	![][0]

2. Cliquez sur l'onglet **Données**, puis sur la table **TodoItem**.

   	![][1]

3. Cliquez sur **Script**, puis sélectionnez l'opération **Insert**.

   	![][2]

4. Remplacez le script existant par la fonction suivante, puis cliquez sur **Enregistrer**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be 10 characters or less.');
            } else {
                request.execute();
            }
        }

    Ce script vérifie la longueur de la propriété **text** et envoie une réponse indiquant une erreur lorsque la chaîne dépasse 10 caractères. Sinon, la méthode **execute** est appelée pour effectuer l'insertion.

   > [AZURE.TIP] Sous l'onglet **Script**, vous pouvez supprimer un script inscrit en cliquant sur **Effacer**, puis sur **Enregistrer**.

## <a name="update-client-validation"></a>Mise à jour du client

Maintenant que le service mobile valide les données et envoie des réponses d'erreur, vous devez vérifier que votre application traite correctement les réponses d'erreur de la validation.

1. Dans Eclipse, ouvrez le projet que vous avez créé avec le didacticiel [Prise en main des données].

2. Dans le fichier ToDoActivity.java, recherchez la méthode **addItem** et remplacez l'appel à la méthode createAndShowDialog par le code suivant :

		createAndShowDialog(exception.getCause().getMessage(), "Error");

	Ce code affiche le message d'erreur renvoyé par le service mobile. 

3. Dans le menu **Exécuter**, cliquez ensuite sur **Exécuter** pour démarrer l'application, puis tapez un texte de plus de 10 caractères dans la zone de texte et cliquez sur le bouton **Ajouter**.

  Notez que l'erreur est traitée et que le message d'erreur est affiché pour l'utilisateur.

## <a name="add-timestamp"></a>Ajout d'un horodatage

Les tâches précédentes ont permis de valider une insertion, qui est acceptée ou rejetée. Maintenant, vous allez mettre à jour les données insérées en utilisant un script serveur qui ajoute une propriété d'horodatage à l'objet avant son insertion.

> [AZURE.NOTE] La propriété d'horodatage **createdAt** illustrée ici est désormais redondante. Mobile Services crée automatiquement une propriété système **__createdAt** pour chaque table.

1. Sous l'onglet **Scripts** du [portail de gestion], remplacez le script **Insert** actuel par la fonction suivante, puis cliquez sur **Enregistrer**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                item.createdAt = new Date();
                request.execute();
            }
        }

    Cette fonction enrichit le script d'insertion précédent en ajoutant une nouvelle propriété d'horodatage **createdAt** à l'objet avant son insertion par l'appel à **request**.**execute**. 

    > [AZURE.IMPORTANT] Le schéma dynamique doit être activé la première fois que ce script d'insertion s'exécute. Avec le schéma dynamique activé, Mobile Services ajoute automatiquement la colonne **createdAt** à la table **TodoItem** lors de la première exécution. Par défaut, le schéma dynamique est activé pour un nouveau service mobile. Il doit être désactivé avant que l'application ne soit publiée.

2. Dans le menu **Exécuter**, cliquez ensuite sur **Exécuter** pour démarrer l'application, puis tapez un texte (de moins de 10 caractères) dans la zone de texte et cliquez sur le bouton **Ajouter**.

   	Notez que le nouvel horodatage n'apparaît pas dans l'interface utilisateur de l'application.

3. De retour dans le portail de gestion, cliquez sur l'onglet **Parcourir** dans la table **todoitem**.
   
   	Notez qu'une colonne **createdAt** apparaît désormais à l'écran et qu'une valeur d'horodatage est associée au nouvel élément inséré.
  
L'application Android doit ensuite être mise à jour pour afficher cette nouvelle colonne.

## <a name="update-client-timestamp"></a>Nouvelle mise à jour du client

Le client Mobile Services ignore les données d'une réponse qu'elle ne peut pas sérialiser en propriétés de type défini. L'étape finale consiste à mettre à jour le client de manière à afficher ces nouvelles données.

1. Dans l'Explorateur de package, ouvrez le fichier ToDoItem.java, puis ajoutez l'instruction **import**suivante :

		import java.util.Date;

2. Ajoutez le code suivant aux définitions de champ privé dans la classe **TodoItem** :

		/**
		 * Timestamp of the item inserted by the service.
		 */
		@com.google.gson.annotations.SerializedName("createdAt")
		private Date mCreatedAt;
  
    > [AZURE.NOTE] L'annotation  `SerializedName` demande au client de mapper la nouvelle propriété  `mCreatedAt` dans l'application vers la colonne à la colonne  `createdAt` définie dans la table TodoItem, qui a un nom différent. En utilisant cette annotation, votre application peut avoir des noms de propriétés pour les objets qui diffèrent des noms de colonnes dans la base de données SQL. Sans cette annotation, une erreur peut se produire en raison de la différence de casse.

2. Ajoutez les méthodes suivantes à la classe ToDoItem pour obtenir et définir la nouvelle propriété mCreatedAt :

		/**
		 * Sets the timestamp.
		 * 
		 * @param date
		 *            timestamp to set
		 */
		public final void setCreatedAt(Date date) {
			mCreatedAt = date;
		}
		
		/**
		 * Returns the timestamp.
		 */
		public Date getCreatedAt() {
			return mCreatedAt;
		}

5. Dans l'Explorateur de package, ouvrez le fichier ToDoItemAdapter.java, puis ajoutez l'instruction **import** suivante :

		import java.text.DateFormat;

6. Dans la méthode GetView, ajoutez le code suivant :

		String createdAtText = "";
		if (currentItem.getCreatedAt() != null){
			DateFormat formatter = DateFormat.getDateInstance(DateFormat.SHORT);
			createdAtText = formatter.format(currentItem.getCreatedAt());
		}

   	Ce code génère une chaîne de date formatée lorsqu'il existe une valeur d'horodatage. 

7. Localisez le code `checkBox.setText(currentItem.getText());` et remplacez la ligne de code par la suivante :

		checkBox.setText(currentItem.getText() + " " + createdAtText);

	Ce code ajoute la date de l'horodatage à l'élément pour l'affichage.
	
6. Dans le menu **Exécuter**, cliquez sur **Exécuter** pour démarrer l'application. 

   	Notez que l'horodatage ne s'affiche que pour les éléments insérés une fois que le script d'insertion a été mis à jour.

7. Remplacez la méthode **RefreshItemsFromTable** existante par le code suivant :

		private void refreshItemsFromTable() {
			
			mToDoTable.where().field("complete").eq(false).and().field("createdAt").ne((String)null)
					.execute(new TableQueryCallback<ToDoItem>() {
	
						public void onCompleted(List<ToDoItem> result, int count,
								Exception exception, ServiceFilterResponse response) {
							if (exception == null) {
								mAdapter.clear();
	
								for (ToDoItem item : result) {
									mAdapter.add(item);
								}
	
							} else {
								createAndShowDialog(exception, "Error");
							}
						}
					});
		}

   	Cette méthode met à jour la requête pour éliminer également les éléments non associés à une valeur d'horodatage.
	
8. Dans le menu **Exécuter**, cliquez ensuite sur **Exécuter** pour démarrer l'application.

   	Notez que tous les éléments créés sans valeur d'horodatage disparaissent de l'interface utilisateur.

Vous avez maintenant terminé ce didacticiel.

## <a name="next-steps"> </a>Étapes suivantes

Maintenant que vous avez terminé ce didacticiel, pensez à suivre le dernier didacticiel dans la série des données : [Affinage des requêtes au moyen de la pagination]

Les scripts serveur sont également utilisés dans le cadre du processus d'autorisation des utilisateurs et pour l'envoi de notifications Push. Pour plus d'informations, reportez-vous aux didacticiels suivants :

* [Autorisation des utilisateurs avec des scripts]
  <br/>En savoir plus sur le filtrage des données en fonction de l'ID d'un utilisateur authentifié.

* [Prise en main des notifications Push] 
  <br/>En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

* [Référence de script serveur Mobile Services]
  <br/>En savoir plus sur l'inscription et l'utilisation des scripts serveur.

<!-- Anchors. -->
[Ajout de la validation de longueur de chaîne]: #string-length-validation
[Mise à jour du client pour la prise en charge de la validation]: #update-client-validation
[Ajout d'un horodatage lors d'une insertion]: #add-timestamp
[Mise à jour du client pour l'affichage de l'horodatage]: #update-client-timestamp
[Étapes suivantes]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-android-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-android-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-android-validate-modify-data-server-scripts/mobile-insert-script-users.png



<!-- URLs. -->
[Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293
[Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started-android
[Autorisation des utilisateurs avec des scripts]: /fr-fr/develop/mobile/tutorials/authorize-users-in-scripts-android
[Affinage des requêtes au moyen de la pagination]: /fr-fr/develop/mobile/tutorials/add-paging-to-data-android
[Prise en main des données]: /fr-fr/develop/mobile/tutorials/get-started-with-data-android
[Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-android
[Prise en main des notifications Push]: /fr-fr/develop/mobile/tutorials/get-started-with-push-android

[Portail de gestion]: https://manage.windowsazure.com/
[Portail de gestion Azure]: https://manage.windowsazure.com/



<!--HONumber=42-->
