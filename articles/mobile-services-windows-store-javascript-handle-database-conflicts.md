<properties linkid="develop-mobile-tutorials-optimistic-concurrent-data-javascript" urlDisplayName="Optimistic concurrency" pageTitle="Handle database write conflicts with optimistic concurrency (Windows Store) | Mobile Dev Center" metaKeywords="" writer="wesmc" description="Learn how to handle database write conflicts on both the server and in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Handling database write conflicts" />

Gestion des conflits d'écriture dans une base de données
========================================================

<div class="dev-center-tutorial-selector sublanding">
<a href="/en-us/develop/mobile/tutorials/handle-database-write-conflicts-dotnet/" title="Windows Store C#">Windows Store C#</a>
<a href="/en-us/develop/mobile/tutorials/handle-database-write-conflicts-javascript/" title="Windows Store JavaScript" class="current">Windows Store JavaScript</a>
<a href="/en-us/develop/mobile/tutorials/handle-database-write-conflicts-wp8/" title="Windows Phone">Windows Phone</a></div>	

Ce didacticiel vise à mieux vous faire comprendre comment gérer les conflits qui se produisent lorsque plusieurs clients écrivent dans un même enregistrement de base de données d'une application Windows Store. Dans certains scénarios, plusieurs clients peuvent écrire à un même moment des modifications dans un même élément. En l'absence de détection de conflits, la dernière écriture remplace les mises à jour précédentes, même si cela n'était pas le but recherché. Windows Azure Mobile Services permet de détecter et de résoudre ces conflits. Cette rubrique vous accompagne tout au long de la procédure destinée à gérer les conflits d'écriture dans une base de données sur le serveur et dans votre application.

Dans le cadre de ce didacticiel, vous allez ajouter une fonctionnalité à l'application de démarrage rapide afin de gérer les contentions qui se produisent lors de la mise à jour de la base de données TodoItem. Ce didacticiel vous familiarise avec ces étapes de base :

1.  [Mise à jour de l'application pour autoriser les mises à jour](#uiupdate)
2.  [Activation de la détection de conflits dans votre application](#enableOC)
3.  [Test des conflits d'écriture dans la base de données de l'application](#test-app)
4.  [Gestion automatique de la résolution des conflits dans les scripts serveur](#scriptsexample)

Ce didacticiel requiert les éléments suivants :

-   Microsoft Visual Studio 2013 Express pour Windows ou version ultérieure.
-   Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de commencer, vous devez avoir suivi le didacticiel [Prise en main de Mobile Services](/en-us/develop/mobile/tutorials/get-started) et téléchargé la version de langage JavaScript du projet de démarrage.
-   [Compte Windows Azure](http://www.windowsazure.com/en-us/pricing/free-trial/)
-   Package NuGet Windows Azure Mobile Services NuGet Package 1.1.5 ou version ultérieure. Pour obtenir la dernière version, procédez comme suit :
    1.  Dans Visual Studio, ouvrez le projet, cliquez dessus avec le bouton droit dans l'Explorateur de solutions, puis cliquez sur **Manage Nuget Packages**.

    2.  Développez **En ligne**, puis cliquez sur **Microsoft et .NET**. Dans la zone de texte de recherche, entrez **WindowsAzure.MobileServices.WinJS**. Cliquez sur **Installer** dans la package NuGet **Windows Azure Mobile Services pour WinJS**.

 		![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-manage-nuget-packages-dialog.png)

<a name="uiupdate"></a>
Mise à jour de l'interface utilisateurMise à jour de l'application pour autoriser les mises à jour
--------------------------------------------------------------------------------------------------

Dans cette section, vous allez mettre à jour l'interface utilisateur pour autoriser la mise à jour du texte de chaque élément. Le modèle de liaison comportera une case à cocher et un contrôle de classe de texte pour chaque élément dans la table de base de données. Vous pourrez mettre à jour le champ texte de TodoItem. L'application gérera l'événement `keydown` de façon à ce que l'élément puisse être mis à jour en appuyant sur la touche **Entrée**.

1.  Dans Visual Studio, ouvrez la version de langage JavaScript du projet TodoList que vous avez téléchargé dans le didacticiel [Prise en main de Mobile Services](/en-us/develop/mobile/tutorials/get-started).
2.  Dans l'Explorateur de solutions de Visual Studio, ouvrez default.html et remplacez la définition de balise div `TemplateItem` par la balise div illustrée ci-dessous, puis enregistrez la modification. Cette action ajoute un contrôle de zone de texte vous permettant de modifier le texte d'un élément TodoItem.

         <div id="TemplateItem" data-win-control="WinJS.Binding.Template">
           <div style="display: -ms-grid; -ms-grid-columns: auto 1fr">
             <input class="itemCheckbox" type="checkbox" data-win-bind="checked: complete; dataContext: this" />
               <div style="-ms-grid-column: 2; -ms-grid-row-align: center; margin-left: 5px">
                 <input id="modifytextbox" class="text win-interactive" data-win-bind="value: text; dataContext: this" />
               </div>
           </div>
         </div>

3.  Dans l'Explorateur de solutions pour Visual Studio, développez le dossier **js**. Ouvrez le fichier default.js et remplacez la fonction `updateTodoItem` par la définition suivante qui ne supprimera pas les éléments mis à jour dans l'interface utilisateur.

         var updateTodoItem = function (todoItem) {
           // Ce code prend un TodoItem qui vient d'être terminé et met à jour la base de données. 
           todoTable.update(todoItem);
           };

4.  Dans le fichier default.js, ajoutez le gestionnaire d'événements suivant pour l'événement `keydown` de façon à ce que ce dernier puisse être mis à jour en appuyant sur la touche **Entrée**.

         listItems.onkeydown = function (eventArgs) {
           if (eventArgs.key == "Enter") {
             var todoItem = eventArgs.target.dataContext.backingData;
             todoItem.text = eventArgs.target.value;
             updateTodoItem(todoItem);
             }
           };

À présent, l'application écrit les modifications apportées au texte dans chaque élément de la base de données lorsque l'utilisateur appuie sur la touche **Entrée**.

<a name="enableOC"></a>
Activation du contrôle d'accès concurrentiel optimisteActivation de la détection de conflits dans votre application
-------------------------------------------------------------------------------------------------------------------

Windows Azure Mobile Services prend en charge le contrôle d'accès concurrentiel optimiste en suivant les modifications apportées à chaque élément en utilisant la colonne de propriété système `__version` ajoutée à chaque table. Dans cette section, nous allons permettre à l'application de détecter ces conflits d'écriture via la propriété système `__version`. Une fois la propriété système activée dans la table todoTable, l'application sera notifiée par une exception `MobileServicePreconditionFailedException` lors d'une tentative de mise à jour si l'enregistrement a été modifié depuis la dernière requête. L'application aura ensuite la possibilité de valider la modification dans la base de données ou de laisser intacte la dernière modification apportée à la base de données. Pour plus d'informations sur les propriétés système pour Mobile Services, consultez la page [Propriétés système](http://go.microsoft.com/fwlink/?LinkId=331143).

1.  Dans le fichier default.js, sous la déclaration de la variable `todoTable`, ajoutez le code pour inclure la propriété système **\_\_version** permettant la prise en charge de la détection des conflits d'écriture.

         var todoTable = client.getTable('TodoItem');
         todoTable.systemProperties |= WindowsAzure.MobileServiceTable.SystemProperties.Version;

2.  En ajoutant la propriété système `Version` aux propriétés système de la table, l'application est notifiée par une exception `MobileServicePreconditionFailedException` lors d'une mise à jour si l'enregistrement a été modifié depuis la dernière requête. Cette exception sera interceptée dans JavaScript au moyen d'une fonction d'erreur. L'erreur inclut la dernière version de l'élément en provenance du serveur permettant de résoudre les conflits. Dans default.js, mettez à jour la fonction `updateTodoItem` pour intercepter l'erreur et appeler une fonction `resolveDatabaseConflict`.

         var updateTodoItem = function (todoItem) {
           // Ce code prend un TodoItem qui vient d'être terminé et met à jour la base de données. 
           // Si la version serveur de l'enregistrement a été mise à jour, nous obtenons l'enregistrement
           // mis à jour à partir de l'erreur « Échec de la condition préalable » afin de résoudre le conflit.
           var serverItem = null;
           todoTable.update(todoItem).then(null, function (error) {
             if (error.message == "Échec de la condition préalable") {
               serverItem = error.serverInstance;
             }
             else {
               var msgDialog =
                 new Windows.UI.Popups.MessageDialog(error.request.responseText,"Échec de la mise à jour");
                 msgDialog.showAsync();
             }
           }).done(function () {
             if (serverItem != null)
               resolveDatabaseConflict(todoItem, serverItem);
           });
         };

3.  Dans default.js, ajoutez la définition de la fonction `resolveDatabaseConflict()` qui est référencée dans la fonction `updateTodoItem`. Notez que pour résoudre le conflit, vous devez définir la version de l'élément local sur la version mise à jour du serveur avant de mettre à jour l'élément dans la base de données. Sinon, un conflit se répétera sans cesse.

         var resolveDatabaseConflict = function (localItem, serverItem) {
           var content = "Cet enregistrement a déjà été modifié comme suit sur le serveur..\n\n" +
               "id : " + serverItem.id + "\n" +
               "text : " + serverItem.text + "\n" +
               "complete : " + serverItem.complete + "\n\n" +
               "Voulez-vous remplacer l'instance de serveur par vos données 
         ";
           var msgDialog = new Windows.UI.Popups.MessageDialog(content, "Résoudre le conflit de base de données");
           msgDialog.commands.append(new Windows.UI.Popups.UICommand("Oui"));
           msgDialog.commands.append(new Windows.UI.Popups.UICommand("Non"));
           msgDialog.showAsync().done(function (command) {
               if (command.label == "Oui") {
                   localItem.__version = serverItem.__version;
                   updateTodoItem(localItem);
               }
           });
         }

<a name="test-app"></a>
Test de l'applicationTest des conflits d'écriture dans la base de données de l'application
------------------------------------------------------------------------------------------

Dans cette section, vous allez créer un package d'application Windows Store pour installer cette application sur un deuxième ordinateur ou une deuxième machine virtuelle. Vous exécuterez ensuite l'application sur les deux machines en générant un conflit d'écriture afin de tester le code. Les deux instances de l'application tenteront de mettre à jour la propriété `text` du même élément, ce qui contraindra l'utilisateur à résoudre le conflit.

1.  Créez un package d'application Windows Store pour l'installer sur un deuxième ordinateur ou une deuxième machine virtuelle. Pour ce faire, cliquez sur **Projet**-\>**Windows Store**-\>**Créer des packages d'application** dans Visual Studio.

 	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package1.png)

2.  Sur l'écran Créer vos packages, cliquez sur **Non**, car ce package ne sera pas téléchargé vers le Windows Store. Cliquez ensuite sur **Suivant**.

 	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package2.png)

3.  Sur l'écran Sélectionner et configurer des packages, acceptez les paramètres par défaut et cliquez sur **Créer**.

 	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package3.png)

4.  Sur l'écran Création de package terminée, cliquez sur le lien **Emplacement de sortie** pour ouvrir l'emplacement du package.

 	![][11]

5.  Copiez le dossier du package, « todolist\_1.0.0.0\_AnyCPU\_Debug\_Test », sur la deuxième machine. Sur cette dernière, ouvrez le dossier du package, cliquez avec le bouton droit sur le script PowerShell **Add-AppDevPackage.ps1**, puis cliquez sur **Exécuter avec PowerShell**, comme illustré ci-dessous. Suivez les instructions de l'invite pour installer l'application.

 	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-install-app-package.png)

6.  Exécutez l'instance 1 de l'application dans Visual Studio en cliquant sur **Déboguer**-\>**Démarrer le débogage**. Sur l'écran d'accueil de la deuxième machine, cliquez sur la flèche orientée vers le bas pour afficher « Apps by name ». Cliquez ensuite sur l'application **todolist** pour exécuter l'instance 2 de l'application.

 	Instance 1 de l'application 

 	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1.png)

 	Instance 2 de l'application 

 	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1.png)

7.  Dans l'instance 1 de l'application, mettez à jour le texte du dernier élément en le remplaçant par **Test Write 1**, puis appuyez sur la touche **Entrée** pour mettre à jour la base de données. La capture d'écran ci-dessous montre un exemple.

 	Instance 1 de l'application 

 	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write1.png)

 	Instance 2 de l'application 

 	[](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1.png)

8.  À ce stade, le dernier élément de l'instance 2 de l'application présente une ancienne version. Dans cette instance de l'application, entrez **Test Write 2** pour la propriété `text` du dernier élément et appuyez sur **Entrée** pour mettre à jour la base de données en utilisant une ancienne propriété `_version`.

 	Instance 1 de l'application 

 	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write2.png)

 	Instance 2 de l'application 

 	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app2-write2.png)

9.  Comme la valeur de `__version` utilisée lors de la tentative de mise à jour ne correspond pas à la valeur de `__version` du serveur, le Kit de développement logiciel (SDK) Mobile Services génère une exception `MobileServicePreconditionFailedException` en tant qu'erreur dans la fonction `updateTodoItem` ce qui permet à l'application de résoudre ce conflit. Pour résoudre ce conflit, vous pouvez cliquer sur **Oui** pour valider les valeurs de l'instance 2. Vous pouvez également cliquer sur **Non** pour ignorer les valeurs de l'instance 2 et conserver les valeurs validées de l'instance 1 de l'application.

 	Instance 1 de l'application 

 	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write2.png)

 	Instance 2 de l'application 

 	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app2-write2-conflict.png)

<a name="scriptsexample"></a>
Gestion des conflits au niveau des scriptsGestion automatique de la résolution des conflits dans les scripts serveur
--------------------------------------------------------------------------------------------------------------------

Vous pouvez détecter et résoudre les conflits d'écriture dans les scripts serveur. Cela est judicieux lorsque vous pouvez résoudre le conflit en utilisant une logique à base de script au lieu d'une interaction utilisateur. Dans cette section, vous allez ajouter un script côté serveur à la table TodoItem pour l'application. Voici la logique que ce script utilisera pour résoudre les conflits :

-   Si la valeur définie du champ `complete` de TodoItem est true, il est considéré comme terminé et text ne peut plus être modifié.
-   Si le champ `complete` a toujours la valeur false, les tentatives de mise à jour de text sont validées.

Les étapes suivantes vous accompagnent tout au long des procédures d'ajout et de test du script de mise à jour serveur.

1.  Connectez-vous au [portail de gestion Windows Azure](https://manage.windowsazure.com/), cliquez sur **Mobile Services**, puis sur l'application.

   	![][7]

2.  Cliquez sur l'onglet **Données**, puis sur la table **TodoItem**.

   	![][8]

3.  Cliquez sur **Script**, puis sélectionnez l'opération **Update**.

   	![][9]

4.  Remplacez le script existant par la fonction suivante, puis cliquez sur **Enregistrer**.

         function update(item, user, request) { 
             request.execute({ 
                 conflict: function (serverRecord) {
                     // Les modifications ne sont validées que si l'élément n'est pas terminé.
                     if (serverRecord.complete === false) {
                         //écriture de l'élément mis à jour dans la table
                         request.execute();
                     }
                     else
                     {
                         request.respond(statusCodes.FORBIDDEN, 'The item is already completed.');
                     }
                 }
             }); 
         }   

5.  Exécutez l'application **todolist** sur les deux machines. Modifiez la propriété `text` de TodoItem pour le dernier élément de l'instance 2 et appuyez sur **Entrée** de façon à ce que l'application mette à jour la base de données.

 	Instance 1 de l'application 

 	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write2.png)

 	Instance 2 de l'application 

 	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app2-write2.png)

6.  Dans l'instance 1 de l'application, entrez une valeur différente pour la dernière propriété text, puis appuyez sur **Entrée**. L'application essaie de mettre à jour la base de données en utilisant une propriété `__version` incorrecte.

 	Instance 1 de l'application 

 	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write3.png)

 	Instance 2 de l'application 

 	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app2-write3.png)

7.  À noter qu'aucune exception n'a été rencontrée dans l'application, car le script serveur a résolu le conflit. Et comme l'élément n'était pas marqué comme terminé, la mise à jour a abouti. Pour vérifier que la mise à jour a vraiment abouti, cliquez sur **Refresh** dans l'instance 2 pour réinterroger la base de données.

 	Instance 1 de l'application 

 	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-write3.png)

 	Instance 2 de l'application 

 	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-write3.png)

8.  Dans l'instance 1, activez la case à cocher pour terminer le dernier élément Todo.

 	Instance 1 de l'application 

 	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-checkbox.png)

 	Instance 2 de l'application 

 	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-write3.png)

9.  Dans l'instance 2, essayez de mettre à jour la propriété text du dernier TodoItem et appuyez sur **Entrée**. Ceci entraîne un conflit parce qu'il a été mis à jour en définissant le champ complete sur true. Le script a résolu le conflit ainsi obtenu en refusant la mise à jour, car l'élément était déjà terminé. Le script a fourni un message dans la réponse.

 	Instance 1 de l'application 

 	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-2-items.png)

 	Instance 2 de l'application 

 	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-already-complete.png)

<a name="next-steps"> </a>
Étapes suivantes
----------------

Ce didacticiel a montré comment permettre à une application Windows Store de gérer les conflits d'écriture lors de l'utilisation de données dans Mobile Services. Nous vous invitons ensuite à suivre l'un des didacticiels suivants de notre série sur les données :

-   [Validation et modification des données avec des scripts](/en-us/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/)
    En savoir plus sur l'utilisation des scripts serveur dans Mobile Services pour valider et modifier les données envoyées à partir de votre application.

-   [Affinage des requêtes à la pagination](/en-us/documentation/articles/mobile-services-windows-store-javascript-add-paging-data/)
    En savoir plus sur l'utilisation de la pagination dans les requêtes pour contrôler la quantité de données traitées dans une seule requête.

Une fois que vous avez terminé la série sur les données, vous pouvez également essayer l'un des didacticiels Windows Store suivants :

-   [Prise en main de l'authentification](/en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-users/)
    En savoir plus sur l'authentification des utilisateurs de votre application.

-   [Prise en main des notifications Push](/en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-push/)
    En savoir plus sur l'envoi d'une notification Push très basique sur votre application avec Mobile Services.


<!-- Anchors. -->
[Update the application to allow updates]: #uiupdate
[Enable Conflict Detection in your application]: #enableOC
[Test database write conflicts in the application]: #test-app
[Automatically handling conflict resolution in server scripts]: #scriptsexample
[Next Steps]:#next-steps

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
[Optimistic Concurrency Control]: http://go.microsoft.com/fwlink/?LinkId=330935
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started/#create-new-service
[Windows Azure Account]: http://www.windowsazure.com/en-us/pricing/free-trial/
[Validate and modify data with scripts]: /en-us/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/
[Refine queries with paging]: /en-us/documentation/articles/mobile-services-windows-store-javascript-add-paging-data/
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started
[Get started with authentication]: /en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
[Get started with push notifications]: /en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-push/

[Windows Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268374
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268375
[Developer Code Samples site]:  http://go.microsoft.com/fwlink/p/?LinkId=271146
[System Properties]: http://go.microsoft.com/fwlink/?LinkId=331143

