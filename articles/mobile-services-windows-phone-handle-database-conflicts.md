<properties 
	pageTitle="Gestion des conflits d'écriture dans une base de données à l'aide du contrôle d'accès concurrentiel optimiste (Windows Store) | Centre de développement mobile" 
	description="Découvrez comment gérer les conflits d'écriture de base de données sur le serveur et dans votre application Windows Store." 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/23/2014" 
	ms.author="wesmc"/>

# Gestion des conflits d'écriture dans une base de données

<div class="dev-center-tutorial-selector sublanding">
<a href="/fr-fr/develop/mobile/tutorials/handle-database-write-conflicts-dotnet/" title="Windows Store C#">Windows Store C#</a>
<a href="/fr-fr/documentation/articles/mobile-services-windows-store-javascript-handle-database-conflicts/" title="Windows Store JavaScript">Windows Store JavaScript</a>
<a href="/fr-fr/develop/mobile/tutorials/handle-database-write-conflicts-wp8/" title="Windows Phone" class="current">Windows Phone</a>
</div>

Ce didacticiel vise à mieux vous faire comprendre comment gérer les conflits qui se produisent lorsque plusieurs clients écrivent dans un même enregistrement de base de données d'une application Windows Phone 8. Dans certains scénarios, plusieurs clients peuvent écrire à un même moment des modifications dans un même élément. En l'absence de détection de conflits, la dernière écriture remplace les mises à jour précédentes, même si cela n'était pas le but recherché. Mobile Services prend en charge la détection et la résolution de ces conflits. Cette rubrique vous accompagne tout au long de la procédure destinée à gérer les conflits d'écriture dans une base de données sur le serveur et dans votre application.

Dans le cadre de ce didacticiel, vous allez ajouter une fonctionnalité à l'application de démarrage rapide afin de gérer les contentions qui se produisent lors de la mise à jour de la base de données TodoItem. Ce didacticiel vous familiarise avec ces étapes de base :

1. [Mise à jour de l'application pour autoriser les mises à jour]
2. [Activation de la détection de conflits dans votre application]
3. [Test des conflits d'écriture dans la base de données de l'application]
4. [Gestion automatique de la résolution des conflits dans les scripts serveur]


Ce didacticiel requiert les éléments suivants :

+ Microsoft Visual Studio 2012 Express pour Windows 8 ou version ultérieure
+ [Le Kit de développement logiciel (SDK) Windows Phone 8] sur Windows 8. 
+ [Compte Azure]
+ Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de commencer, vous devez effectuer le didacticiel [Prise en main de Mobile Services].
+ Package NuGet Azure Mobile Services version 1.1.0 ou ultérieure. Pour obtenir la dernière version, procédez comme suit :
	1. Dans Visual Studio, ouvrez le projet, cliquez dessus avec le bouton droit dans l'Explorateur de solutions, puis cliquez sur **Gérer les packages Nuget**. 

		![][13]

	2. Développez **En ligne**, puis cliquez sur **Microsoft et .NET**. Dans la zone de texte de recherche, entrez **Azure Mobile Services**. Cliquez sur **Installer** dans le package NuGet **Azure Mobile Services**.

		![][14]


 

<h2><a name="uiupdate"></a>Mise à jour de l'application pour autoriser les mises à jour</h2>

Dans cette section, vous allez mettre à jour l'interface utilisateur TodoList pour autoriser la mise à jour du texte de chaque élément composant un contrôle ListBox. Ce dernier contiendra un contrôle CheckBox et TextBox pour chaque élément de la table de base de données. Vous pourrez mettre à jour le champ texte de TodoItem. L'application gérera l'événement  `LostFocus` depuis ce TextBox pour mettre à jour l'élément dans la base de données.


1. Dans Visual Studio, ouvrez le projet TodoList que vous avez téléchargé dans le didacticiel [Prise en main de Mobile Services].
2. Dans l'Explorateur de solutions Visual Studio, ouvrez MainPage.xaml et remplacez la définition  `phone:LongListSelector` par le contrôle ListBox ci-après, puis enregistrez la modification.

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


2. Dans l'Explorateur de solutions Visual Studio, ouvrez MainPage.xaml.cs et ajoutez la directive  `using` suivante.

		using System.Threading.Tasks;


3. Dans l'Explorateur de solutions de Visual Studio, ouvrez MainPage.xaml.cs. Ajoutez le gestionnaire d'événements dans MainPage pour l'événement TextBox  `LostFocus` comme indiqué ci-dessous.


        private async void ToDoText_LostFocus(object sender, RoutedEventArgs e)
        {
            TextBox tb = (TextBox)sender;
            TodoItem item = tb.DataContext as TodoItem;
            //let's see if the text changed
            if (item.Text != tb.Text)
            {
                item.Text = tb.Text;
                await UpdateToDoItem(item);
            }
        }

4. Dans MainPage.xaml.cs, ajoutez la définition pour la méthode  `UpdateToDoItem()` de MainPage référencée dans le gestionnaire d'événements, comme indiqué ci-dessous.

        private async Task UpdateToDoItem(TodoItem item)
        {
            try
            {
                //update at the remote table
                await todoTable.UpdateAsync(item);
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message, "Update Failed", MessageBoxButton.OK);
            }
        }

À présent, l'application écrit les modifications apportées au texte dans chaque élément de la base de données lorsque le TextBox perd le focus.

<h2><a name="enableOC"></a>Activation de la détection de conflits dans votre application</h2>

Dans certains scénarios, plusieurs clients peuvent écrire à un même moment des modifications dans un même élément. En l'absence de détection de conflits, la dernière écriture remplace les mises à jour précédentes, même si cela n'était pas le but recherché. [Le contrôle d'accès concurrentiel optimiste] considère que chaque transaction peut être validée et qu'à ce titre, elle ne fait appel à aucun verrouillage de ressources. Avant de valider une transaction, le contrôle d'accès concurrentiel optimiste vérifie qu'aucune autre transaction n'a modifié les données. Si les données ont été modifiées, la transaction de validation est annulée. Azure Mobile Services prend en charge le contrôle d'accès concurrentiel optimiste en suivant les modifications apportées à chaque élément en utilisant la colonne de propriété système " __version " ajoutée à chaque table. Dans cette section, nous allons permettre à l'application de détecter ces conflits d'écriture via la propriété système " __version ". L'application sera notifiée par une exception  `MobileServicePreconditionFailedException` lors d'une tentative de mise à jour si l'enregistrement a été modifié depuis la dernière requête. Il sera ensuite possible de valider la modification dans la base de données ou de laisser intacte la dernière modification apportée à la base de données. Pour plus d'informations sur les propriétés système pour Mobile Services, consultez la page [Propriétés système].

1. Dans MainPage.xaml.cs, mettez à jour la définition de la classe **TodoItem** avec le code suivant pour inclure la propriété système **__version** autorisant la prise en charge de la détection de conflits d'écriture.

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

	> [AZURE.NOTE[ Lorsque des tables non typées sont utilisées, l'activation du contrôle d'accès concurrentiel optimiste s'effectue en ajoutant l'indicateur Version aux propriétés système (SystemProperties) de la table.  
	>
	>````` 
	//Enable optimistic concurrency by retrieving __version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
`````


2. En ajoutant la propriété  `Version` à la classe  `TodoItem`, l'application est notifiée par une exception  `MobileServicePreconditionFailedException` lors d'une mise à jour si l'enregistrement a été modifié depuis la dernière requête. Cette exception inclut la dernière version de l'élément en provenance du serveur. Dans MainPage.xaml.cs, ajoutez le code suivant pour gérer l'exception dans la méthode  `UpdateToDoItem()`.

        private async Task UpdateToDoItem(TodoItem item)
        {
            Exception exception = null;
            try
            {
                //update at the remote table
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
                    //conflict detected, the item has changed since the last query
                    await ResolveConflict(item, ((MobileServicePreconditionFailedException<TodoItem>)exception).Item);
                }
                else
                    MessageBox.Show(exception.Message, "Update Failed", MessageBoxButton.OK);
            }
        }


3. Dans MainPage.xaml.cs, ajoutez la définition de la méthode  `ResolveConflict()` référencée dans  `UpdateToDoItem()`. Notez que pour résoudre le conflit, vous devez définir la version de l'élément local sur la version mise à jour du serveur avant de valider la décision de l'utilisateur. À défaut, le conflit se répètera sans cesse.


        private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)		
        {
            // Ask user to choose between the local text value or leaving the 
			// server's updated text value
            MessageBoxResult mbRes = MessageBox.Show(String.Format("The item has already been updated on the server.\n\n" +
                                                                   "Server value: {0} \n" +
                                                                   "Local value: {1}\n\n" +
                                                                   "Press OK to update the server with the local value.\n\n" +
                                                                   "Press CANCEL to keep the server value.", serverItem.Text, localItem.Text), 
                                                                   "CONFLICT DETECTED ", MessageBoxButton.OKCancel);
            // OK : After examining the updated text from the server, overwrite it
            //      with the changes made in this client.
            if (mbRes == MessageBoxResult.OK)
            {
                // Update the version of the item to the latest version
                // to resolve the conflict. Otherwise the exception
                // will be thrown again for the attempted update.
                localItem.Version = serverItem.Version;
                // Recursively updating just in case another conflict 
				// occurs while the user is deciding.
                await this.UpdateToDoItem(localItem);
            }
            // CANCEL : After examining the updated text from the server, leave 
			// the server item intact and refresh this client's query discarding 
			// the proposed changes.
            if (mbRes == MessageBoxResult.Cancel)
            {
                RefreshTodoItems();
            }
        }



<h2><a name="test-app"></a>Test des conflits d'écriture dans la base de données de l'application</h2>

Dans cette section, vous allez tester le code qui gère les conflits d'écriture en exécutant l'application dans deux émulateurs Windows Phone 8 différents (WVGA et WVGA 512M). Les deux applications clientes vont tenter de mettre à jour la même propriété  `text` de l'élément, obligeant l'utilisateur à résoudre le conflit.


1. Dans Visual Studio, vérifiez que **Emulator WVGA 512MB** est sélectionné dans la zone de liste déroulante comme cible de déploiement, comme illustré dans la capture d'écran ci-dessous.

	![][0]

2. Dans Visual Studio, dans le menu, cliquez sur **BUILD**, puis sur **Déployer la solution**. Si l'émulateur ne fonctionnait pas, il lui faudra quelques minutes pour charger le système d'exploitation Windows Phone 8. Dans la fenêtre de sortie en bas, vérifiez que le développement et le déploiement dans l'émulateur Windows Phone 8 ont réussi.

	![][2]

3. Dans Visual Studio, paramétrez la zone de liste déroulante de la cible de déploiement sur **Emulator WVGA**.

	![][1]

4. Dans Visual Studio, dans le menu, cliquez sur **BUILD**, puis sur **Déployer la solution**. Dans la fenêtre de sortie en bas, vérifiez que le développement et le déploiement dans l'émulateur Windows Phone 8 ont réussi.

   	![][2]
  
5. Placez les deux émulateurs côte à côte. Il est possible de simuler des conflits d'écriture entre les applications clientes sur ces émulateurs. Dans les deux émulateurs, balayez l'écran vers la gauche pour afficher la liste des applications installées. Dans chaque liste, faites défiler jusqu'en bas et cliquez sur l'application **todolist**.

	![][3]

6. Dans l'émulateur à gauche, mettez à jour le  `text` du dernier TodoItem sur **Test Write 1**, puis cliquez sur une autre zone de texte pour que le gestionnaire d'événements  `LostFocus` mette à jour la base de données. La capture d'écran ci-dessous montre un exemple. 

	![][4]

7. À ce stade, l'élément correspondant dans l'émulateur à droite a une version ancienne et une valeur de texte ancienne. Dans l'émulateur de droite, entrez **Test Write 2** comme propriété de texte. Cliquez sur une autre zone de texte pour que le gestionnaire d'événements  `LostFocus` dans l'émulateur à droite tente de mettre à jour la base de données avec l'ancienne version.

	![][5]

8. Comme la version utilisée pour la tentative de mise à jour ne correspond pas à celle du serveur, le Kit de développement logiciel (SDK) Mobile Services lève l'exception  `MobileServicePreconditionFailedException`, qui permet à l'application de résoudre ce conflit. Pour résoudre le conflit, vous pouvez cliquer sur **ok** pour valider les valeurs de l'application de droite. Vous pouvez aussi cliquer sur **annuler** pour ignorer les valeurs de l'application de droite et conserver celles de l'application de gauche. 

	![][6]



<h2><a name="scriptsexample"></a>Gestion automatique de la résolution des conflits dans les scripts serveur</h2>

Vous pouvez détecter et résoudre les conflits d'écriture dans les scripts serveur. Cela est judicieux lorsque vous pouvez résoudre le conflit en utilisant une logique à base de script au lieu d'une interaction utilisateur. Dans cette section, vous allez ajouter un script côté serveur à la table TodoItem pour l'application. Voici la logique que ce script utilisera pour résoudre les conflits :

+  Si la valeur du champ " complete " de TodoItem est définie sur true, il est considéré comme terminé et  `text` ne peut plus être modifié.
+  Si le champ " complete " de TodoItem a toujours la valeur false, les tentatives de mise à jour de  `text` sont validées.

Les étapes suivantes vous accompagnent tout au long des procédures d'ajout et de test du script de mise à jour serveur.

1. Connectez-vous au [portail de gestion Azure], cliquez sur **Mobile Services**, puis sur l'application. 

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
5. Modifiez le texte TodoItem du dernier élément dans l'application de l'émulateur à gauche. Cliquez ensuite sur une autre zone de texte pour que le gestionnaire d'événements  `LostFocus` mette à jour la base de données.

	![][4]

6. Dans l'émulateur à droite, entrez une autre valeur pour la propriété de texte du dernier TodoItem. Cliquez sur une autre zone de texte pour que le gestionnaire d'événements  `LostFocus` dans l'émulateur à droite tente de mettre à jour la base de données avec l'ancienne version.

	![][5]

7. À noter qu'aucune exception n'a été rencontrée dans l'application, car le script serveur a résolu le conflit. Et comme l'élément n'était pas marqué comme terminé, la mise à jour a abouti. Pour confirmer la réussite de la mise à jour, cliquez sur **Actualiser** dans l'application dans l'émulateur de gauche pour envoyer une nouvelle requête à la base de données.

	![][10]

8. Dans l'application de l'émulateur à gauche, cliquez sur la case à cocher pour terminer le dernier TodoItem.

	![][11]

9. Dans l'application dans l'émulateur à droite, essayez de mettre à jour le même texte TodoItem et de déclencher l'événement  `LostFocus`. Le script a résolu le conflit ainsi obtenu en refusant la mise à jour, car l'élément était déjà terminé. 

	![][12]


## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel a présenté l'activation d'une application Windows Phone 8 pour gérer les conflits d'écriture lors de l'utilisation de données dans Mobile Services. Nous vous invitons ensuite à suivre l'un des didacticiels suivants de notre série sur les données :

* [Validation et modification de données à l'aide de scripts]
  <br/>Découvrez comment utiliser des scripts serveur dans Mobile Services pour valider et modifier les données envoyées à partir de votre application.

* [Affinage des requêtes au moyen de la pagination]
  <br/>Découvrez comment utiliser la pagination dans les requêtes pour contrôler la quantité de données traitées dans une seule requête.

Une fois que vous avez terminé la série de données, vous pouvez essayer l'un des didacticiels Windows Phone 8 suivants :

* [Prise en main de l'authentification] 
  <br/>Découvrez comment authentifier les utilisateurs de votre application.

* [Prise en main des notifications Push] 
  <br/>Apprenez à envoyer une notification Push très basique à votre application avec Mobile Services.
 
<!-- Anchors. -->
[Mise à jour de l'application pour autoriser les mises à jour]: #uiupdate
[Activation de la détection de conflits dans votre application]: #enableOC
[Test des conflits d'écriture dans la base de données de l'application]: #test-app
[Gestion automatique de la résolution des conflits dans les scripts serveur]: #scriptsexample
[Étapes suivantes]:#next-steps

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
[Contrôle d'accès concurrentiel optimiste]: http://go.microsoft.com/fwlink/?LinkId=330935
[Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started/#create-new-service
[Compte Azure]: http://azure.microsoft.com/pricing/free-trial/
[Validation et modification de données à l'aide de scripts]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-wp8
[Affinage des requêtes au moyen de la pagination]: /fr-fr/develop/mobile/tutorials/add-paging-to-data-wp8
[Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started-wp8
[Prise en main des données] : ./mobile-services-get-started-with-data-wp8.md
[Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-wp8
[Prise en main des notifications Push]: /fr-fr/develop/mobile/tutorials/get-started-with-push-wp8

[Portail de gestion Azure]: https://manage.windowsazure.com/
[Portail de gestion]: https://manage.windowsazure.com/
[Kit de développement logiciel (SDK) Windows Phone 8]: http://go.microsoft.com/fwlink/p/?LinkID=268374
[Kit de développement logiciel (SDK) Mobile Services]: http://go.microsoft.com/fwlink/p/?LinkID=268375
[Site d'exemples de code développeur]:  http://go.microsoft.com/fwlink/p/?LinkId=271146
[Propriétés système]: http://go.microsoft.com/fwlink/?LinkId=331143



<!--HONumber=42-->
