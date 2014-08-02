<properties linkid="develop-mobile-tutorials-optimistic-concurrent-data-wp8" urlDisplayName="Optimistic concurrency" pageTitle="Handle database write conflicts with optimistic concurrency (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to handle database write conflicts on both the server and in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Handling database write conlicts" authors="" />

Gestion des conflits d'écriture dans une base de données
========================================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/handle-database-write-conflicts-dotnet/ "Windows Store C#") [Windows Store JavaScript](/en-us/develop/mobile/tutorials/handle-database-write-conflicts-javascript/ "Windows Store JavaScript") [Windows Phone](/en-us/develop/mobile/tutorials/handle-database-write-conflicts-wp8/ "Windows Phone")

Ce didacticiel vise à mieux vous faire comprendre comment gérer les conflits qui se produisent lorsque plusieurs clients écrivent dans un même enregistrement de base de données d'une application Windows Phone 8. Dans certains scénarios, plusieurs clients peuvent écrire à un même moment des modifications dans un même élément. En l'absence de détection de conflits, la dernière écriture remplace les mises à jour précédentes, même si cela n'était pas le but recherché. Mobile Services prend en charge la détection et la résolution de ces conflits. Cette rubrique vous accompagne tout au long de la procédure destinée à gérer les conflits d'écriture dans une base de données sur le serveur et dans votre application.

Dans le cadre de ce didacticiel, vous allez ajouter une fonctionnalité à l'application de démarrage rapide afin de gérer les contentions qui se produisent lors de la mise à jour de la base de données TodoItem. Ce didacticiel vous familiarise avec ces étapes de base :

1.  [Mise à jour de l'application pour autoriser les mises à jour](#uiupdate)
2.  [Activation de la détection de conflits dans votre application](#enableOC)
3.  [Test des conflits d'écriture dans la base de données de l'application](#test-app)
4.  [Gestion automatique de la résolution des conflits dans les scripts serveur](#scriptsexample)

Ce didacticiel requiert les éléments suivants :

-   Microsoft Visual Studio 2012 Express pour Windows 8 ou version ultérieure
-   [Le Kit de développement logiciel (SDK) Windows Phone 8](http://go.microsoft.com/fwlink/p/?LinkID=268374) sur Windows 8
-   [Un compte Azure](http://www.windowsazure.com/en-us/pricing/free-trial/)
-   Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de commencer, vous devez effectuer le didacticiel [Prise en main de Mobile Services](/en-us/develop/mobile/tutorials/get-started-wp8).
-   Package NuGet Azure Mobile Services version 1.1.0 ou ultérieure. Pour obtenir la dernière version, procédez comme suit :
    1.  Dans Visual Studio, ouvrez le projet, cliquez dessus avec le bouton droit dans l'Explorateur de solutions, puis cliquez sur **Manage Nuget Packages**.

        ![](./media/mobile-services-windows-phone-handle-database-conflicts/mobile-manage-nuget-packages-VS.png)

    2.  Développez **En ligne**, puis cliquez sur **Microsoft et .NET**. Dans la zone de texte de recherche, entrez **Azure Mobile Services**. Cliquez sur **Install** dans le package NuGet **Azure Mobile Services**.

        ![](./media/mobile-services-windows-phone-handle-database-conflicts/mobile-manage-nuget-packages-dialog.png)

Mise à jour de l'interface utilisateurMise à jour de l'application pour autoriser les mises à jour
--------------------------------------------------------------------------------------------------

Dans cette section, vous allez mettre à jour l'interface utilisateur TodoList pour autoriser la mise à jour du texte de chaque élément composant un contrôle ListBox. Ce dernier contiendra un contrôle CheckBox et TextBox pour chaque élément de la table de base de données. Vous pourrez mettre à jour le champ texte de TodoItem. L'application gérera l'événement `LostFocus` depuis ce TextBox pour mettre à jour l'élément dans la base de données.

1.  Dans Visual Studio, ouvrez le projet TodoList que vous avez téléchargé dans le didacticiel [Prise en main de Mobile Services](/en-us/develop/mobile/tutorials/get-started-wp8).
2.  Dans l'Explorateur de solutions Visual Studio, ouvrez MainPage.xaml et remplacez la définition `phone:LongListSelector` par le contrôle ListBox ci-après, puis enregistrez la modification.

         <ListBox Grid.Row="4" Grid.ColumnSpan="2" Name="ListItems">
             <ListBox.ItemTemplate>
                 <DataTemplate>
                     <StackPanel Orientation="Horizontal">
                         <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" Checked="CheckBoxComplete_Checked" Margin="10,5" VerticalAlignment="Center"/>
                         <TextBox x:Name="ToDoText" Width="330" Text="{Binding Text, Mode=TwoWay}" AcceptsReturn="False" LostFocus="ToDoText_LostFocus"/>
                     </StackPanel>
                 </DataTemplate>
             </ListBox.ItemTemplate>
         </ListBox>

3.  Dans l'Explorateur de solutions Visual Studio, ouvrez MainPage.xaml.cs et ajoutez la directive `using` suivante.

         using System.Threading.Tasks;

4.  Dans l'Explorateur de solutions de Visual Studio, ouvrez MainPage.xaml.cs. Ajoutez le gestionnaire d'événements à MainPage pour l'événement `LostFocus` de TextBox, comme indiqué ci-dessous.

         private async void ToDoText_LostFocus(object sender, RoutedEventArgs e)
         {
             TextBox tb = (TextBox)sender;
             TodoItem item = tb.DataContext as TodoItem;
             //Vérifiez si le texte a été modifié
             if (item.Text != tb.Text)
             {
                 item.Text = tb.Text;
                 await UpdateToDoItem(item);
             }
         }

5.  Dans MainPage.xaml.cs, ajoutez la définition pour la méthode `UpdateToDoItem()` de MainPage référencée dans le gestionnaire d'événements, comme indiqué ci-dessous.

         private async Task UpdateToDoItem(TodoItem item)
         {
             try
             {
                 //mise à jour au niveau de la table distante
                 await todoTable.UpdateAsync(item);
             }
             catch (Exception ex)
             {
                 MessageBox.Show(ex.Message, "Update Failed", MessageBoxButton.OK);
             }
         }

À présent, l'application écrit les modifications apportées au texte dans chaque élément de la base de données lorsque le TextBox perd le focus.

Activation du contrôle d'accès concurrentiel optimisteActivation de la détection de conflits dans votre application
-------------------------------------------------------------------------------------------------------------------

Dans certains scénarios, plusieurs clients peuvent écrire à un même moment des modifications dans un même élément. En l'absence de détection de conflits, la dernière écriture remplace les mises à jour précédentes, même si cela n'était pas le but recherché. Le [contrôle d'accès concurrentiel optimiste](http://go.microsoft.com/fwlink/?LinkId=330935) considère que chaque transaction peut être validée et qu'à ce titre, elle ne fait appel à aucun verrouillage de ressources. Avant de valider une transaction, le contrôle d'accès concurrentiel optimiste vérifie qu'aucune autre transaction n'a modifié les données. Si les données ont été modifiées, la transaction de validation est annulée. Azure Mobile Services prend en charge le contrôle d'accès concurrentiel optimiste en suivant les modifications apportées à chaque élément en utilisant la colonne de propriété système `__version` ajoutée à chaque table. Dans cette section, nous allons permettre à l'application de détecter ces conflits d'écriture via la propriété système `__version`. L'application sera notifiée par une exception `MobileServicePreconditionFailedException` lors d'une tentative de mise à jour si l'enregistrement a été modifié depuis la dernière requête. Il sera ensuite possible de valider la modification dans la base de données ou de laisser intacte la dernière modification apportée à la base de données. Pour plus d'informations sur les propriétés système pour Mobile Services, consultez la page [Propriétés système](http://go.microsoft.com/fwlink/?LinkId=331143).

1.  Dans MainPage.xaml.cs, mettez à jour la définition de la classe **TodoItem** avec le code suivant pour inclure la propriété système **\_\_version** autorisant la prise en charge de la détection de conflits d'écriture.

         public class TodoItem
         {
             public string Id { get; set; }            
             [JsonProperty(PropertyName = "text")]
             public string Text { get; set; }            
             [JsonProperty(PropertyName = "complete")]
             public bool Complete { get; set; }            
             [JsonProperty(PropertyName = "__version")]
             public string Version { set; get; }
         }

    **Remarque**

    Lorsque des tables non typées sont utilisées, l'activation du contrôle d'accès concurrentiel optimiste s'effectue en ajoutant l'indicateur Version aux propriétés système (SystemProperties) de la table.

    <pre><code>
    //Activez l'accès concurrentiel optimiste en récupérant __version
    todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
    </code></pre>

2.  En ajoutant la propriété `Version` à la classe `TodoItem`, l'application est notifiée par une exception `MobileServicePreconditionFailedException` lors d'une mise à jour si l'enregistrement a été modifié depuis la dernière requête. Cette exception inclut la dernière version de l'élément en provenance du serveur. Dans MainPage.xaml.cs, ajoutez le code suivant pour gérer l'exception dans la méthode `UpdateToDoItem()`.

         private async Task UpdateToDoItem(TodoItem item)
         {
             Exception exception = null;
             try
             {
                 //mise à jour au niveau de la table distante
                 await todoTable.UpdateAsync(item);
             }
             catch (MobileServicePreconditionFailedException<TodoItem> writeException)
             {
                 exception = writeException;
             }
             catch (Exception ex)
             {
                 exception = ex;
             }
             if (exception != null)
             {
                 if (exception is MobileServicePreconditionFailedException<TodoItem>)
                 {
                     //Conflit détecté, l'élément a changé depuis la dernière requête
                     await ResolveConflict(item, ((MobileServicePreconditionFailedException<TodoItem>)exception).Item);
                 }
                 else
                     MessageBox.Show(exception.Message, "Update Failed", MessageBoxButton.OK);
             }
         }

3.  Dans MainPage.xaml.cs, ajoutez la définition pour la méthode `ResolveConflict()` référencée dans `UpdateToDoItem()`. Notez que pour résoudre le conflit, vous devez définir la version de l'élément local sur la version mise à jour du serveur avant de valider la décision de l'utilisateur. À défaut, le conflit se répètera sans cesse.

         private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)     
         {
             // Demandez à l'utilisateur de choisir entre la valeur du texte local et conserver 
             // la valeur du texte mis à jour du serveur
             MessageBoxResult mbRes = MessageBox.Show(String.Format("The item has already been updated on the server.\n\n" +
                                                                    "Server value: {0} \n" +
                                                                    "Local value: {1}\n\n" +
                                                                    "Press OK to update the server with the local value.\n\n" +
                                                                    "Press CANCEL to keep the server value.", serverItem.Text, localItem.Text), 
                                                                    "CONFLICT DETECTED ", MessageBoxButton.OKCancel);
             // OK : Après examen du texte mis à jour du serveur, remplacez-le
             //      par les changements apportés dans ce client.
             if (mbRes == MessageBoxResult.OK)
             {
                 // Mettez à jour l'élément avec la dernière version
                 // pour résoudre le conflit. À défaut, l'exception
                 // sera de nouveau levée pour la tentative de mise à jour.
                 localItem.Version = serverItem.Version;
                 // La mise à jour récursive en cas de nouveau conflit 
                 // se produit lorsque l'utilisateur le décide.
                 await this.UpdateToDoItem(localItem);
             }
             // ANNULER : Après examen du texte mis à jour du serveur, conservez 
             // l'élément du serveur et actualisez cette requête du client en ignorant 
             // les changements proposés.
             if (mbRes == MessageBoxResult.Cancel)
             {
                 RefreshTodoItems();
             }
         }

Test de l'applicationTest des conflits d'écriture dans la base de données de l'application
------------------------------------------------------------------------------------------

Dans cette section, vous allez tester le code qui gère les conflits d'écriture en exécutant l'application dans deux émulateurs Windows Phone 8 différents (WVGA et WVGA 512M). Les deux applications clientes vont tenter de mettre à jour la même propriété `text` de l'élément, obligeant l'utilisateur à résoudre le conflit.

1.  Dans Visual Studio, vérifiez que **Emulator WVGA 512MB** est sélectionné dans la zone de liste déroulante comme cible de déploiement, comme illustré dans la capture d'écran ci-dessous.

    ![](./media/mobile-services-windows-phone-handle-database-conflicts/mobile-EmulatorWVGA512MB.png)

2.  Dans Visual Studio, dans le menu, cliquez sur **BUILD**, puis sur **Déployer la solution**. Si l'émulateur ne fonctionnait pas, il lui faudra quelques minutes pour charger le système d'exploitation Windows Phone 8. Dans la fenêtre de sortie en bas, vérifiez que le développement et le déploiement dans l'émulateur Windows Phone 8 ont réussi.

    ![](./media/mobile-services-windows-phone-handle-database-conflicts/mobile-build-deploy-wp8.png)

3.  Dans Visual Studio, passez la zone de liste déroulante de la cible de déploiement sur **Emulator WVGA**.

    ![](./media/mobile-services-windows-phone-handle-database-conflicts/mobile-EmulatorWVGA.png)

4.  Dans Visual Studio, dans le menu, cliquez sur **BUILD**, puis sur **Déployer la solution**. Dans la fenêtre de sortie en bas, vérifiez que le développement et le déploiement dans l'émulateur Windows Phone 8 ont réussi.

    ![][2]

5.  Placez les deux émulateurs côte à côte. Il est possible de simuler des conflits d'écriture entre les applications clientes sur ces émulateurs. Dans les deux émulateurs, balayez l'écran vers la gauche pour afficher la liste des applications installées. Dans chaque liste, faites défiler jusqu'en bas et cliquez sur l'application **todolist**.

    ![](./media/mobile-services-windows-phone-handle-database-conflicts/mobile-start-apps-oc-wp8.png)

6.  Dans l'émulateur à gauche, mettez à jour le `text` du dernier TodoItem sur **Test Write 1**, puis cliquez sur une autre zone de texte pour que le gestionnaire d'événements `LostFocus` mette à jour la base de données. La capture d'écran ci-dessous montre un exemple.

    ![](./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-write1-wp8.png)

7.  À ce stade, l'élément correspondant dans l'émulateur à droite a une version ancienne et une valeur de texte ancienne. Dans l'émulateur à droite, entrez **Test Write 2** comme propriété de texte. Cliquez sur une autre zone de texte pour que le gestionnaire d'événements `LostFocus` dans l'émulateur à droite tente de mettre à jour la base de données avec l'ancienne version.

    ![](./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-write2-wp8.png)

8.  Comme la version utilisée pour la tentative de mise à jour ne correspond pas à celle du serveur, le Kit de développement logiciel (SDK) Mobile Services lève l'exception `MobileServicePreconditionFailedException`, qui permet à l'application de résoudre ce conflit. Pour résoudre le conflit, vous pouvez cliquer sur **ok** pour valider les valeurs de l'application à droite. Vous pouvez aussi cliquer sur **cancel** pour ignorer les valeurs de l'application à droite et conserver celles de l'application à gauche.

    ![](./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-exception-wp8.png)

Gestion des conflits au niveau des scriptsGestion automatique de la résolution des conflits dans les scripts serveur
--------------------------------------------------------------------------------------------------------------------

Vous pouvez détecter et résoudre les conflits d'écriture dans les scripts serveur. Cela est judicieux lorsque vous pouvez résoudre le conflit en utilisant une logique à base de script au lieu d'une interaction utilisateur. Dans cette section, vous allez ajouter un script côté serveur à la table TodoItem pour l'application. Voici la logique que ce script utilisera pour résoudre les conflits :

-   Si la valeur définie du champ `complete` de TodoItem est true, il est considéré comme terminé et `text` ne peut plus être modifié.
-   Si le champ `complete` a toujours la valeur false, les tentatives de mise à jour de `text` sont validées.

Les étapes suivantes vous accompagnent tout au long des procédures d'ajout et de test du script de mise à jour serveur.

1.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com/), cliquez sur **Mobile Services**, puis sur l'application.

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
                         //écrivez l'élément mis à jour dans la table
                         request.execute();
                     }
                     else
                     {
                         request.respond(statusCodes.FORBIDDEN, 'The item is already completed.');
                     }
                 }
             }); 
         }   
5.  Modifiez le texte TodoItem du dernier élément dans l'application de l'émulateur à gauche. Cliquez ensuite sur une autre zone de texte pour que le gestionnaire d'événements `LostFocus` mette à jour la base de données.

    ![](./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-write1-wp8.png)

6.  Dans l'émulateur à droite, entrez une autre valeur pour la propriété de texte du dernier TodoItem. Cliquez sur une autre zone de texte pour que le gestionnaire d'événements `LostFocus` dans l'émulateur à droite tente de mettre à jour la base de données avec l'ancienne version.

    ![](./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-write2-wp8.png)

7.  À noter qu'aucune exception n'a été rencontrée dans l'application, car le script serveur a résolu le conflit. Et comme l'élément n'était pas marqué comme terminé, la mise à jour a abouti. Pour confirmer la réussite de la mise à jour, cliquez sur **Refresh** dans l'application dans l'émulateur à gauche pour envoyer une nouvelle requête à la base de données.

    ![](./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-insync-wp8.png)

8.  Dans l'application de l'émulateur à gauche, cliquez sur la case à cocher pour terminer le dernier TodoItem.

    ![](./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-complete-checkbox-wp8.png)

9.  Dans l'application dans l'émulateur à droite, essayez de mettre à jour le même texte TodoItem et de déclencher l'événement `LostFocus`. Le script a résolu le conflit ainsi obtenu en refusant la mise à jour, car l'élément était déjà terminé.

    ![](./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-already-completed-wp8.png)

Étapes suivantes
----------------

Ce didacticiel a présenté l'activation d'une application Windows Phone 8 pour gérer les conflits d'écriture lors de l'utilisation de données dans Mobile Services. Nous vous invitons ensuite à suivre l'un des didacticiels suivants de notre série sur les données :

-   [Validation et modification des données avec des scripts](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-wp8)
    En savoir plus sur l'utilisation des scripts serveur dans Mobile Services pour valider et modifier les données envoyées à partir de votre application.

-   [Affinage des requêtes à la pagination](/en-us/develop/mobile/tutorials/add-paging-to-data-wp8)
    En savoir plus sur l'utilisation de la pagination dans les requêtes pour contrôler la quantité de données traitées dans une seule requête.

Une fois que vous avez terminé la série de données, vous pouvez essayer l'un des didacticiels Windows Phone 8 suivants :

-   [Prise en main de l'authentification](/en-us/develop/mobile/tutorials/get-started-with-users-wp8)
    En savoir plus sur l'authentification des utilisateurs de votre application.

-   [Prise en main des notifications Push](/en-us/develop/mobile/tutorials/get-started-with-push-wp8)
    En savoir plus sur l'envoi d'une notification Push très basique sur votre application avec Mobile Services.


<!-- Anchors. -->
[Update the application to allow updates]: #uiupdate
[Enable Conflict Detection in your application]: #enableOC
[Test database write conflicts in the application]: #test-app
[Automatically handling conflict resolution in server scripts]: #scriptsexample
[Next Steps]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-EmulatorWVGA512MB.png
[1]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-EmulatorWVGA.png
[2]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-build-deploy-wp8.png
[3]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-start-apps-oc-wp8.png
[4]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-write1-wp8.png
[5]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-write2-wp8.png
[6]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-exception-wp8.png
[7]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-services-selection.png
[8]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-portal-data-tables.png
[9]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-insert-script-users.png
[10]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-insync-wp8.png
[11]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-complete-checkbox-wp8.png
[12]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-already-completed-wp8.png
[13]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-manage-nuget-packages-VS.png
[14]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-manage-nuget-packages-dialog.png


<!-- URLs. -->
[Optimistic Concurrency Control]: http://go.microsoft.com/fwlink/?LinkId=330935
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started/#create-new-service
[Azure Account]: http://www.windowsazure.com/en-us/pricing/free-trial/
[Validate and modify data with scripts]: /en-us/develop/mobile/tutorials/validate-modify-and-augment-data-wp8
[Refine queries with paging]: /en-us/develop/mobile/tutorials/add-paging-to-data-wp8
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started-wp8
[Get started with data]: ./mobile-services-get-started-with-data-wp8.md
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-wp8
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-wp8

[Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268374
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268375
[Developer Code Samples site]:  http://go.microsoft.com/fwlink/p/?LinkId=271146
[System Properties]: http://go.microsoft.com/fwlink/?LinkId=331143
