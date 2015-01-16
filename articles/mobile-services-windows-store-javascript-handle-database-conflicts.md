<properties urlDisplayName="Optimistic concurrency" pageTitle="Gestion des conflits d'écriture de base de données avec l'accès concurrentiel optimiste (Windows Store) | Centre de développement mobile" metaKeywords="" writer = "wesmc" description="Découvrez comment gérer les conflits d'écriture de base de données sur le serveur et dans votre application Windows Store." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Handling database write conflicts" authors="wesmc" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Gestion des conflits d'écriture dans une base de données

<div class="dev-center-tutorial-selector sublanding">
<a href="/fr-fr/develop/mobile/tutorials/handle-database-write-conflicts-dotnet/" title="Windows Store C#">Windows Store C#</a>
<a href="/fr-fr/documentation/articles/mobile-services-windows-store-javascript-handle-database-conflicts/" title="Windows Store JavaScript" class="current">Windows Store JavaScript</a>
<a href="/fr-fr/develop/mobile/tutorials/handle-database-write-conflicts-wp8/" title="Windows Phone">Windows Phone</a></div>	

Ce didacticiel vise à mieux vous faire comprendre comment gérer les conflits qui se produisent lorsque plusieurs clients écrivent dans un même enregistrement de base de données d'une application Windows Store. Dans certains scénarios, plusieurs clients peuvent écrire à un même moment des modifications dans un même élément. En l'absence de détection de conflits, la dernière écriture remplace les mises à jour précédentes, même si cela n'était pas le but recherché. Azure Mobile Services permet de détecter et de résoudre ces conflits. Cette rubrique vous accompagne tout au long de la procédure destinée à gérer les conflits d'écriture dans une base de données sur le serveur et dans votre application.

Dans le cadre de ce didacticiel, vous allez ajouter une fonctionnalité à l'application de démarrage rapide afin de gérer les contentions qui se produisent lors de la mise à jour de la base de données TodoItem. Ce didacticiel vous familiarise avec ces étapes de base :

1. [Mise à jour de l'application pour autoriser les mises à jour]
2. [Activation de la détection de conflits dans votre application]
3. [Test des conflits d'écriture dans la base de données de l'application]
4. [Gestion automatique de la résolution des conflits dans les scripts serveur]


Ce didacticiel requiert les éléments suivants :

+ Microsoft Visual Studio 2013 Express pour Windows ou version ultérieure.
+ Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de commencer, vous devez avoir suivi le didacticiel [Prise en main de Mobile Services] et téléchargé la version de langage JavaScript du projet de démarrage. 
+ [Compte Azure.]
+ Package NuGet Microsoft Azure Mobile Services 1.1.5 ou version ultérieure. Pour obtenir la dernière version, procédez comme suit :
	1. Dans Visual Studio, ouvrez le projet, cliquez dessus avec le bouton droit dans l'Explorateur de solutions, puis cliquez sur **Gérer les packages NuGet**. 


	2. Développez **En ligne**, puis cliquez sur **Microsoft et .NET**. Dans la zone de texte de recherche, entrez **WindowsAzure.MobileServices.WinJS**. Cliquez sur **Installer** dans le package NuGet **Windows Azure Mobile Services pour WinJS**.

		![][20]


 

<h2><a name="uiupdate"></a>Mise à jour de l'application pour autoriser les mises à jour</h2>

Dans cette section, vous allez mettre à jour l'interface utilisateur pour autoriser la mise à jour du texte de chaque élément. Le modèle de liaison comportera une case à cocher et un contrôle de classe de texte pour chaque élément dans la table de base de données. Vous pourrez mettre à jour le champ texte de TodoItem. L'application gérera l'événement `keydown` de façon à ce que l'élément puisse être mis à jour en appuyant sur la touche **Entrée**.


1. Dans Visual Studio, ouvrez la version de langage JavaScript du projet TodoList que vous avez téléchargé dans le didacticiel [Prise en main de Mobile Services].
2. Dans l'Explorateur de solutions de Visual Studio, ouvrez default.html et remplacez la définition de balise div `TemplateItem` par la balise div illustrée ci-dessous, puis enregistrez la modification. Cette action ajoute un contrôle de zone de texte vous permettant de modifier le texte d'un élément TodoItem.

        <div id="TemplateItem" data-win-control="WinJS.Binding.Template">
          <div style="display: -ms-grid; -ms-grid-columns: auto 1fr">
            <input class="itemCheckbox" type="checkbox" data-win-bind="checked: complete; dataContext: this" />
              <div style="-ms-grid-column: 2; -ms-grid-row-align: center; margin-left: 5px">
                <input id="modifytextbox" class="text win-interactive" data-win-bind="value: text; dataContext: this" />
              </div>
          </div>
        </div>


3. Dans l'Explorateur de solutions pour Visual Studio, développez le dossier **js**. Ouvrez le fichier default.js et remplacez la fonction `updateTodoItem` par la définition suivante qui ne supprimera pas les éléments mis à jour dans l'interface utilisateur.

        var updateTodoItem = function (todoItem) {
          // This code takes a freshly completed TodoItem and updates the database. 
          todoTable.update(todoItem);
          };


4. Dans le fichier default.js, l'application ajoute le gestionnaire d'événement `keydown` suivant de façon à ce que l'élément puisse être mis à jour en appuyant sur la touche **Entrée**.

        listItems.onkeydown = function (eventArgs) {
          if (eventArgs.key == "Enter") {
            var todoItem = eventArgs.target.dataContext.backingData;
            todoItem.text = eventArgs.target.value;
            updateTodoItem(todoItem);
            }
          };

À présent, l'application écrit les modifications apportées au texte dans chaque élément de la base de données lorsque l'utilisateur appuie sur la touche **Entrée**.

<h2><a name="enableOC"></a>Activation de la détection de conflits dans votre application</h2>

Azure Mobile Services prend en charge le contrôle d'accès concurrentiel optimiste en suivant les modifications apportées à chaque élément en utilisant la colonne de propriété système `__version` ajoutée à chaque table. Dans cette section, nous allons permettre à l'application de détecter ces conflits d'écriture via la propriété système `__version`. Une fois la propriété système activée dans la table todoTable, l'application sera notifiée par une exception `MobileServicePreconditionFailedException` lors d'une tentative de mise à jour si l'enregistrement a été modifié depuis la dernière requête. L'application aura ensuite la possibilité de valider la modification dans la base de données ou de laisser intacte la dernière modification apportée à la base de données. Pour plus d'informations sur les propriétés système pour Mobile Services, consultez la page [Propriétés système].

1. Dans le fichier default.js, sous la déclaration de la variable `todoTable` , ajoutez le code pour inclure la propriété système **__version** permettant la prise en charge de la détection des conflits d'écriture.

        var todoTable = client.getTable('TodoItem');
        todoTable.systemProperties |= WindowsAzure.MobileServiceTable.SystemProperties.Version;


2. En ajoutant la propriété système `Version` aux propriétés système de la table, l'application est notifiée par une exception `MobileServicePreconditionFailedException` lors d'une mise à jour si l'enregistrement a été modifié depuis la dernière requête. Cette exception sera interceptée dans JavaScript au moyen d'une fonction d'erreur. L'erreur inclut la dernière version de l'élément en provenance du serveur permettant de résoudre les conflits. Dans default.js, mettez à jour la fonction `updateTodoItem` pour intercepter l'erreur et appeler une fonction `resolveDatabaseConflict`.

        var updateTodoItem = function (todoItem) {
          // This code takes a freshly completed TodoItem and updates the database. 
          // If the server version of the record has been updated, we get the updated
          // record from the Precondition Failed error in order to resolve the conflict.
          var serverItem = null;
          todoTable.update(todoItem).then(null, function (error) {
            if (error.message == "Precondition Failed") {
              serverItem = error.serverInstance;
            }
            else {
              var msgDialog =
                new Windows.UI.Popups.MessageDialog(error.request.responseText,"Update Failed");
                msgDialog.showAsync();
            }
          }).done(function () {
            if (serverItem != null)
              resolveDatabaseConflict(todoItem, serverItem);
          });
        };


3. Dans default.js, ajoutez la définition de la fonction `resolveDatabaseConflict()` qui est référencée dans la fonction `updateTodoItem`. Notez que pour résoudre le conflit, vous devez définir la version de l'élément local sur la version mise à jour du serveur avant de mettre à jour l'élément dans la base de données. Sinon, un conflit se répétera sans cesse.


        var resolveDatabaseConflict = function (localItem, serverItem) {
          var content = "This record has been changed as follows on the server already..\n\n" +
              "id : " + serverItem.id + "\n" +
              "text : " + serverItem.text + "\n" +
              "complete : " + serverItem.complete + "\n\n" +
              "Do you want to overwrite the server instance with your data?";
          var msgDialog = new Windows.UI.Popups.MessageDialog(content, "Resolve Database Conflict");
          msgDialog.commands.append(new Windows.UI.Popups.UICommand("Yes"));
          msgDialog.commands.append(new Windows.UI.Popups.UICommand("No"));
          msgDialog.showAsync().done(function (command) {
              if (command.label == "Yes") {
                  localItem.__version = serverItem.__version;
                  updateTodoItem(localItem);
              }
          });
        }


<h2><a name="test-app"></a>Test des conflits d'écriture dans la base de données de l'application</h2>

Dans cette section, vous allez créer un package d'application Windows Store pour installer cette application sur un deuxième ordinateur ou une deuxième machine virtuelle. Vous exécuterez ensuite l'application sur les deux machines en générant un conflit d'écriture afin de tester le code. Les deux instances de l'application tenteront de mettre à jour la propriété `text`  du même élément, ce qui contraindra l'utilisateur à résoudre le conflit.


1. Créez un package d'application Windows Store pour l'installer sur un deuxième ordinateur ou une deuxième machine virtuelle. Pour ce faire, cliquez sur **Projet**->**Store**->**Créer des packages d'application** dans Visual Studio.

	![][0]

2. Sur l'écran Créer vos packages, cliquez sur **Non**, car ce package ne sera pas téléchargé vers le Windows Store. Cliquez ensuite sur **Suivant**.

	![][1]

3. Sur l'écran Sélectionner et configurer des packages, acceptez les paramètres par défaut et cliquez sur **Créer**.

	![][10]

4. Sur l'écran Création de package terminée, cliquez sur le lien **Emplacement de sortie** pour ouvrir l'emplacement du package.

   	![][11]

5. Copiez le dossier du package, " todolist_1.0.0.0_AnyCPU_Debug_Test ", sur la deuxième machine. Sur cette dernière, ouvrez le dossier du package, cliquez avec le bouton droit sur le script PowerShell **Add-AppDevPackage.ps1**, puis cliquez sur **Exécuter avec PowerShell**, comme illustré ci-dessous. Suivez les instructions de l'invite pour installer l'application.

	![][12]
  
6. Exécutez l'instance 1 de l'application dans Visual Studio en cliquant sur **Déboguer**->**Démarrer le débogage**. Sur l'écran d'accueil de la deuxième machine, cliquez sur la flèche orientée vers le bas pour afficher " Apps by name ". Cliquez ensuite sur l'application **todolist** pour exécuter l'instance 2 de l'application. 

	Instance 1 de l'application	
	![][2]

	Instance 2 de l'application	
	![][2]


7. Dans l'instance 1 de l'application, mettez à jour le texte du dernier élément en le remplaçant par **Test Write 1**, puis appuyez sur la touche **Entrée** pour mettre à jour la base de données. La capture d'écran ci-dessous montre un exemple.
	
	Instance 1 de l'application	
	![][3]

	Instance 2 de l'application	
	![][2]

8. À ce stade, le dernier élément de l'instance 2 de l'application présente une ancienne version. Dans cette instance de l'application, entrez **Test Write 2** pour la propriété `text` du dernier élément et appuyez sur **Entrée** pour mettre à jour la base de données en utilisant une ancienne propriété `_version`.

	Instance 1 de l'application	
	![][4]

	Instance 2 de l'application	
	![][5]

9. Comme la valeur de `__version` utilisée lors de la tentative de mise à jour ne correspond pas à la valeur de __version` du serveur, le Kit de développement logiciel (SDK) Mobile Services génère une exception `MobileServicePreconditionFailedException` en tant qu'erreur dans la fonction `updateTodoItem` ce qui permet à l'application de résoudre ce conflit. Pour résoudre ce conflit, vous pouvez cliquer sur **Oui** pour valider les valeurs de l'instance 2. Vous pouvez également cliquer sur **Non** pour ignorer les valeurs de l'instance 2 et conserver les valeurs validées de l'instance 1 de l'application. 

	Instance 1 de l'application	
	![][4]

	Instance 2 de l'application	
	![][6]



<h2><a name="scriptsexample"></a>Gestion automatique de la résolution des conflits dans les scripts serveur</h2>

Vous pouvez détecter et résoudre les conflits d'écriture dans les scripts serveur. Cela est judicieux lorsque vous pouvez résoudre le conflit en utilisant une logique à base de script au lieu d'une interaction utilisateur. Dans cette section, vous allez ajouter un script côté serveur à la table TodoItem pour l'application. Voici la logique que ce script utilisera pour résoudre les conflits :

+  Si la valeur définie du champ ` complete` de TodoItem est true, il est considéré comme terminé et `text` ne peut plus être modifié.
+  Si le champ ` complete` de TodoItem a toujours la valeur false, les tentatives de mise à jour de `text` sont validées.

Les étapes suivantes vous accompagnent tout au long des procédures d'ajout et de test du script de mise à jour serveur.

1. Connectez-vous au [portail de gestion Azure], cliquez sur **Mobile Services**, puis sur votre application. 

   	![][7]

2. Cliquez sur l'onglet **Données**, puis sur la table **TodoItem**.

   	![][8]

3. Cliquez sur **Script**, puis sélectionnez l'opération **Update**.

   	![][9]

4. Remplacez le script existant par la fonction suivante, puis cliquez sur **Enregistrer**.

		function update(item, user, request) { 
			request.execute({ 
				conflict: function (serverRecord) {
					// Only committing changes if the item is not completed.
					if (serverRecord.complete === false) {
						//write the updated item to the table
						request.execute();
					}
					else
					{
						request.respond(statusCodes.FORBIDDEN, 'The item is already completed.');
					}
				}
			}); 
		}   
5. Exécutez l'application **todolist** sur les deux machines. Modifiez la propriété `text` de TodoItem pour le dernier élément de l'instance 2 et appuyez sur **Entrée** de façon à ce que l'application mette à jour la base de données.

	Instance 1 de l'application	
	![][4]

	Instance 2 de l'application	
	![][5]

6. Dans l'instance 1 de l'application, entrez une valeur différente pour la dernière propriété text, puis appuyez sur **Entrée**. L'application essaie de mettre à jour la base de données en utilisant une propriété `__version` incorrecte.

	Instance 1 de l'application	
	![][13]

	Instance 2 de l'application	
	![][14]

7. À noter qu'aucune exception n'a été rencontrée dans l'application, car le script serveur a résolu le conflit. Et comme l'élément n'était pas marqué comme terminé, la mise à jour a abouti. Pour vérifier que la mise à jour a vraiment abouti, cliquez sur **Refresh** dans l'instance 2 pour réinterroger la base de données.

	Instance 1 de l'application	
	![][15]

	Instance 2 de l'application	
	![][15]

8. Dans l'instance 1, activez la case à cocher pour terminer le dernier élément Todo.

	Instance 1 de l'application	
	![][16]

	Instance 2 de l'application	
	![][15]

9. Dans l'instance 2, essayez de mettre à jour la propriété text du dernier TodoItem et appuyez sur **Entrée**. Ceci entraîne un conflit parce qu'il a été mis à jour en définissant le champ complete sur true. Le script a résolu le conflit ainsi obtenu en refusant la mise à jour, car l'élément était déjà terminé. Le script a fourni un message dans la réponse.  

	Instance 1 de l'application	
	![][17]

	Instance 2 de l'application	
	![][18]

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel a montré comment permettre à une application Windows Store de gérer les conflits d'écriture lors de l'utilisation de données dans Mobile Services. Nous vous invitons ensuite à suivre l'un des didacticiels suivants de notre série sur les données :

* [Validation et modification des données avec des scripts]
  <br/>En savoir plus sur l'utilisation des scripts serveur dans Mobile Services pour valider et modifier les données envoyées à partir de votre application.

* [Affinage des requêtes à la pagination.]
  <br/>En savoir plus sur l'utilisation de la pagination dans les requêtes pour contrôler la quantité de données traitées dans une seule requête.

Une fois que vous avez terminé la série sur les données, vous pouvez également essayer l'un des didacticiels Windows Store suivants :

* [Prise en main de l'authentification] 
  <br/>En savoir plus sur l'authentification des utilisateurs de votre application.

* [Prise en main des notifications Push] 
  <br/>En savoir plus sur l'envoi d'une notification Push très basique sur votre application avec Mobile Services.
 
<!-- Anchors. -->
[Mise à jour de l'application pour autoriser les mises à jour]: #uiupdate
[Activation de la détection de conflits dans votre application]: #enableOC
[Test des conflits d'écriture dans la base de données de l'application]: #test-app
[Gestion automatique de la résolution des conflits dans les scripts serveur]: #scriptsexample
[Étapes suivantes]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package1.png
[1]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package2.png
[2]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1.png 
[3]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write1.png
[4]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write2.png
[5]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app2-write2.png
[6]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app2-write2-conflict.png
[7]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-services-selection.png
[8]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-portal-data-tables.png
[9]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-insert-script-users.png
[10]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package3.png
[11]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package4.png
[12]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-install-app-package.png
[13]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write3.png
[14]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app2-write3.png
[15]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-write3.png
[16]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-checkbox.png
[17]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-2-items.png
[18]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-already-complete.png
[19]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-manage-nuget-packages-VS.png
[20]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-manage-nuget-packages-dialog.png

<!-- URLs. -->
[Contrôle d'accès concurrentiel optimiste]: http://go.microsoft.com/fwlink/?LinkId=330935
[Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started/#create-new-service
[Compte Azure.]: http://www.windowsazure.com/fr-fr/pricing/free-trial/
[Validation et modification des données avec des scripts]: /fr-fr/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/
[Affinage des requêtes à la pagination.]: /fr-fr/documentation/articles/mobile-services-windows-store-javascript-add-paging-data/
[Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started
[Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
[Prise en main des notifications Push]: /fr-fr/documentation/articles/mobile-services-windows-store-javascript-get-started-push/

[Portail de gestion Azure]: https://manage.windowsazure.com/
[Portail de gestion]: https://manage.windowsazure.com/
[Kit de développement logiciel (SDK) Windows Phone 8]: http://go.microsoft.com/fwlink/p/?LinkID=268374
[Kit de développement logiciel (SDK) Mobile Services]: http://go.microsoft.com/fwlink/p/?LinkID=268375
[Site d'exemples de code développeur]:  http://go.microsoft.com/fwlink/p/?LinkId=271146
[Propriétés système]: http://go.microsoft.com/fwlink/?LinkId=331143
