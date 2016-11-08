---
title: Gestion des conflits d'écriture de base de données avec l'accès concurrentiel optimiste (Windows Store) | Microsoft Docs
description: Découvrez comment gérer les conflits d'écriture de base de données sur le serveur et dans votre application Windows Store.
documentationcenter: windows
author: wesmc7777
manager: dwrede
editor: ''
services: mobile-services

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 07/21/2016
ms.author: wesmc

---
# Gestion des conflits d'écriture dans une base de données
[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;

## Vue d'ensemble
Ce didacticiel vise à mieux vous faire comprendre comment gérer les conflits qui se produisent lorsque plusieurs clients écrivent dans un même enregistrement de base de données d'une application Windows Store. Dans certains scénarios, plusieurs clients peuvent écrire à un même moment des modifications dans un même élément. En l'absence de détection de conflits, la dernière écriture remplace les mises à jour précédentes, même si cela n'était pas le but recherché. Azure Mobile Services permet de détecter et de résoudre ces conflits. Cette rubrique vous accompagne tout au long de la procédure destinée à gérer les conflits d'écriture dans une base de données sur le serveur et dans votre application.

Dans ce didacticiel, vous allez ajouter des fonctionnalités à l'application de démarrage rapide pour gérer les conflits qui peuvent se produire pendant la mise à jour de la base de données TodoItem.

## Configuration requise
Ce didacticiel requiert les éléments suivants :

* Microsoft Visual Studio 2013 ou une version ultérieure.
* Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de commencer, vous devez effectuer le didacticiel [Prise en main de Mobile Services].
* [Compte Azure]
* Package NuGet Azure Mobile Services version 1.1.0 ou ultérieure. Pour obtenir la dernière version, procédez comme suit :
  
  1. Dans Visual Studio, ouvrez le projet, cliquez dessus avec le bouton droit dans l'Explorateur de solutions, puis cliquez sur **Manage Nuget Packages**.
     
      ![][19]
  2. Développez **En ligne**, puis cliquez sur **Microsoft et .NET**. Dans la zone de texte de recherche, entrez **Azure Mobile Services**. Cliquez sur **Install** dans le package NuGet **Azure Mobile Services**.
     
      ![][20]

## Mise à jour de l'application pour autoriser les mises à jour
Dans cette section, vous allez mettre à jour l'interface utilisateur TodoList pour autoriser la mise à jour du texte de chaque élément composant un contrôle ListBox. Ce dernier contiendra un contrôle CheckBox et TextBox pour chaque élément de la table de base de données. Vous pourrez mettre à jour le champ texte de TodoItem. L'application gère l'événement `LostFocus` depuis ce TextBox pour mettre à jour l'élément dans la base de données.

1. Dans Visual Studio, ouvrez le projet TodoList que vous avez téléchargé dans le didacticiel [Prise en main de Mobile Services].
2. Dans l'Explorateur de solutions Visual Studio, ouvrez MainPage.xaml et remplacez la définition `ListView` par la définition `ListView` ci-après, puis enregistrez la modification.
   
        <ListView Name="ListItems" Margin="62,10,0,0" Grid.Row="1">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <StackPanel Orientation="Horizontal">
                        <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" Checked="CheckBoxComplete_Checked" Margin="10,5" VerticalAlignment="Center"/>
                        <TextBox x:Name="ToDoText" Height="25" Width="300" Margin="10" Text="{Binding Text, Mode=TwoWay}" AcceptsReturn="False" LostFocus="ToDoText_LostFocus"/>
                    </StackPanel>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
3. Dans l'Explorateur de solutions de Visual Studio, ouvrez le fichier MainPage.cs dans le projet partagé. Ajoutez le gestionnaire d'événements à MainPage pour l'événement `LostFocus` de TextBox comme indiqué ci-dessous.

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

1. Dans MainPage.cs pour le projet partagé, ajoutez la définition pour la méthode de `UpdateToDoItem()` MainPage référencée dans le gestionnaire d'événements, comme indiqué ci-dessous.
   
        private async Task UpdateToDoItem(TodoItem item)
        {
            Exception exception = null;
            try
            {
                //update at the remote table
                await todoTable.UpdateAsync(item);
            }
            catch (Exception ex)
            {
                exception = ex;
            }
            if (exception != null)
            {
                await new MessageDialog(exception.Message, "Update Failed").ShowAsync();
            }
        }

À présent, l'application écrit les modifications apportées au texte dans chaque élément de la base de données lorsque le TextBox perd le focus.

## Activation de la détection de conflits dans votre application
Dans certains scénarios, plusieurs clients peuvent écrire à un même moment des modifications dans un même élément. En l'absence de détection de conflits, la dernière écriture remplace les mises à jour précédentes, même si cela n'était pas le but recherché. Le [contrôle d'accès concurrentiel optimiste] considère que chaque transaction peut être validée et qu'à ce titre, elle ne fait appel à aucun verrouillage de ressources. Avant de valider une transaction, le contrôle d'accès concurrentiel optimiste vérifie qu'aucune autre transaction n'a modifié les données. Si les données ont été modifiées, la transaction de validation est annulée. Azure Mobile Services prend en charge le contrôle d'accès concurrentiel optimiste en suivant les modifications apportées à chaque élément en utilisant la colonne de propriété système `__version` ajoutée à chaque table. Dans cette section, nous allons permettre à l'application de détecter ces conflits d'écriture via la propriété système `__version`. L'application sera notifiée par une exception `MobileServicePreconditionFailedException` lors d'une tentative de mise à jour si l'enregistrement a été modifié depuis la dernière requête. Il sera ensuite possible de valider la modification dans la base de données ou de laisser intacte la dernière modification apportée à la base de données. Pour plus d'informations sur les propriétés système pour Mobile Services, consultez la page [Propriétés système].

1. Ouvrez Todoltem.cs dans le projet partagé et mettez à jour la `TodoItem` définition de la classe avec le code suivant pour inclure la `__version` propriété système autorisant la prise en charge de la détection de conflits d'écriture.
   
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
   
   > [!NOTE]
   > Lorsque des tables non typées sont utilisées, l'activation du contrôle d'accès concurrentiel optimiste s'effectue en ajoutant l'indicateur Version aux propriétés système (SystemProperties) de la table.
   > 
   > `````
   > //Enable optimistic concurrency by retrieving __version
   > todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
   > `````
   > 
   > 
2. En ajoutant la propriété `Version` à la classe `TodoItem`, l'application est notifiée par une exception `MobileServicePreconditionFailedException` lors d'une mise à jour si l'enregistrement a été modifié depuis la dernière requête. Cette exception inclut la dernière version de l'élément en provenance du serveur. Dans MainPage.cs pour le projet partagé, ajoutez le code suivant pour gérer l'exception dans la `UpdateToDoItem()` méthode.
   
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
                if (exception is MobileServicePreconditionFailedException)
                {
                    //Conflict detected, the item has changed since the last query
                    //Resolve the conflict between the local and server item
                    await ResolveConflict(item, ((MobileServicePreconditionFailedException<TodoItem>) exception).Item);
                }
                else
                {
                    await new MessageDialog(exception.Message, "Update Failed").ShowAsync();
                }
            }
        }
3. Dans MainPage.cs, ajoutez la définition pour la `ResolveConflict()` méthode référencée dans `UpdateToDoItem()`. Notez que pour résoudre le conflit, vous devez définir la version de l'élément local sur la version mise à jour du serveur avant de valider la décision de l'utilisateur. À défaut, le conflit se répètera sans cesse.

        private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
        {
            //Ask user to choose the resolution between versions
            MessageDialog msgDialog = new MessageDialog(String.Format("Server Text: "{0}" \nLocal Text: "{1}"\n",
                                                        serverItem.Text, localItem.Text),
                                                        "CONFLICT DETECTED - Select a resolution:");
            UICommand localBtn = new UICommand("Commit Local Text");
            UICommand ServerBtn = new UICommand("Leave Server Text");
            msgDialog.Commands.Add(localBtn);
            msgDialog.Commands.Add(ServerBtn);
            localBtn.Invoked = async (IUICommand command) =>
            {
                // To resolve the conflict, update the version of the
                // item being committed. Otherwise, you will keep
                // catching a MobileServicePreConditionFailedException.
                localItem.Version = serverItem.Version;
                // Updating recursively here just in case another
                // change happened while the user was making a decision
                await UpdateToDoItem(localItem);
            };
            ServerBtn.Invoked = async (IUICommand command) =>
            {
                RefreshTodoItems();
            };
            await msgDialog.ShowAsync();
        }



## Test des conflits d'écriture dans la base de données de l'application
Dans cette section, vous allez créer un package d'application Windows Store pour installer cette application sur un deuxième ordinateur ou une deuxième machine virtuelle. Vous exécuterez ensuite l'application sur les deux machines en générant un conflit d'écriture afin de tester le code. Les deux instances de l'application tenteront de mettre à jour la propriété `text` du même élément, ce qui contraindra l'utilisateur à résoudre le conflit.

1. Créez un package d'application Windows Store pour l'installer sur un deuxième ordinateur ou une deuxième machine virtuelle. Pour ce faire, cliquez sur **Projet**->**Windows Store**->**Créer des packages d'application** dans Visual Studio.
   
    ![][0]
2. Sur l'écran Créer vos packages, cliquez sur **Non**, car ce package ne sera pas téléchargé vers le Windows Store. Cliquez ensuite sur **Suivant**.
   
    ![][1]
3. Sur l'écran Sélectionner et configurer des packages, acceptez les paramètres par défaut et cliquez sur **Créer**.
   
    ![][10]
4. Sur l'écran Création de package terminée, cliquez sur le lien **Emplacement de sortie** pour ouvrir l'emplacement du package.
   
       ![][11]
5. Copiez le dossier du package, « todolist\_1.0.0.0\_AnyCPU\_Debug\_Test », sur la deuxième machine. Sur cette dernière, ouvrez le dossier du package, cliquez avec le bouton droit sur le script PowerShell **Add-AppDevPackage.ps1**, puis cliquez sur **Exécuter avec PowerShell**, comme illustré ci-dessous. Suivez les instructions de l'invite pour installer l'application.
   
    ![][12]
6. Exécutez l'instance 1 de l'application dans Visual Studio en cliquant sur **Déboguer**->**Démarrer le débogage**. Sur l'écran d'accueil de la deuxième machine, cliquez sur la flèche orientée vers le bas pour afficher « Apps by name ». Cliquez ensuite sur l'application **todolist** pour exécuter l'instance 2 de l'application.
   
    Instance 1 de l'application ![][2]
   
    Instance 2 de l'application ![][2]
7. Dans l'instance 1 de l'application, mettez à jour le texte du dernier élément en le remplaçant par **Test Write 1**, puis cliquez sur une autre zone de texte de sorte que le gestionnaire d'événements `LostFocus` mette à jour la base de données. La capture d'écran ci-dessous montre un exemple.
   
    Instance 1 de l'application ![][3]
   
    Instance 2 de l'application ![][2]
8. À ce stade, l'élément correspondant de l'instance 2 de l'application présente une ancienne version. Dans cette instance de l'application, entrez **Test Write 2** pour la propriété `text`. Cliquez ensuite sur une autre zone de texte de sorte que le gestionnaire d'événements `LostFocus` tente de mettre à jour la base de données avec l'ancienne propriété `_version`.
   
    Instance 1 de l'application ![][4]
   
    Instance 2 de l'application ![][5]
9. Comme la valeur `__version` utilisée pour la tentative de mise à jour ne correspond pas à la valeur `__version` du serveur, le Kit de développement logiciel (SDK) Mobile Services lève une exception `MobileServicePreconditionFailedException` qui permet à l'application de résoudre ce conflit. Pour résoudre le conflit, vous pouvez cliquer sur **Commit Local Text ** pour valider les valeurs de l'instance 2. Sinon, cliquez sur **Leave Server Text** pour ignorer les valeurs dans l'instance 2 et conserver les valeurs validées de l'instance 1 de l'application.
   
    Instance 1 de l'application ![][4]
   
    Instance 2 de l'application ![][6]

## Gestion automatique de la résolution des conflits dans les scripts serveur
Vous pouvez détecter et résoudre les conflits d'écriture dans les scripts serveur. Cela est judicieux lorsque vous pouvez résoudre le conflit en utilisant une logique à base de script au lieu d'une interaction utilisateur. Dans cette section, vous allez ajouter un script côté serveur à la table TodoItem pour l'application. Voici la logique que ce script utilisera pour résoudre les conflits :

* Si la valeur définie du champ ` complete` de TodoItem est true, il est considéré comme terminé et `text` ne peut plus être modifié.
* Si le champ ` complete` de TodoItem a toujours la valeur false, les tentatives de mise à jour de `text` sont validées.

Les étapes suivantes vous accompagnent tout au long des procédures d'ajout et de test du script de mise à jour serveur.

1. Connectez-vous au [portail Azure Classic], cliquez sur **Mobile Services**, puis sur votre application.
   
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
5. Exécutez l'application **todolist** sur les deux machines. Modifiez le `text` de TodoItem pour le dernier élément de l'instance 2. Cliquez ensuite sur une autre zone de texte pour que le gestionnaire d'événements `LostFocus` mette à jour la base de données.
   
    Instance 1 de l'application ![][4]
   
    Instance 2 de l'application ![][5]
6. Dans l'instance 1 de l'application, entrez une valeur différente pour la dernière propriété text. Cliquez ensuite sur une autre zone de texte de telle sorte que le gestionnaire d'événements `LostFocus` tente de mettre à jour la base de données avec une propriété `__version` incorrecte.
   
    Instance 1 de l'application ![][13]
   
    Instance 2 de l'application ![][14]
7. À noter qu'aucune exception n'a été rencontrée dans l'application, car le script serveur a résolu le conflit. Et comme l'élément n'était pas marqué comme terminé, la mise à jour a abouti. Pour vérifier que la mise à jour a vraiment abouti, cliquez sur **Refresh** dans l'instance 2 pour réinterroger la base de données.
   
    Instance 1 de l'application ![][15]
   
    Instance 2 de l'application ![][15]
8. Dans l'instance 1, activez la case à cocher pour terminer le dernier élément Todo.
   
    Instance 1 de l'application ![][16]
   
    Instance 2 de l'application ![][15]
9. Dans l'instance 2, essayez de mettre à jour la propriété text du dernier TodoItem et déclenchez l'événement `LostFocus`. Le script a résolu le conflit ainsi obtenu en refusant la mise à jour, car l'élément était déjà terminé.
   
    Instance 1 de l'application ![][17]
   
    Instance 2 de l'application ![][18]

## Étapes suivantes
Ce didacticiel a montré comment permettre à une application Windows Store de gérer les conflits d'écriture lors de l'utilisation de données dans Mobile Services. Pour la suite, envisagez de suivre l'un des didacticiels Windows Store suivants :

* [Ajouter l'authentification à votre application] <br/>En savoir plus sur l'authentification des utilisateurs de votre application.
* [Ajouter les notifications push à votre application] <br/>En savoir plus sur l'envoi d'une notification push très basique à votre application avec Mobile Services.

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package1.png
[1]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package2.png
[2]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1.png
[3]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1-write1.png
[4]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1-write2.png
[5]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app2-write2.png
[6]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app2-write2-conflict.png
[7]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-services-selection.png
[8]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-portal-data-tables.png
[9]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-insert-script-users.png
[10]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package3.png
[11]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package4.png
[12]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-install-app-package.png
[13]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1-write3.png
[14]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app2-write3.png
[15]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-write3.png
[16]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-checkbox.png
[17]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-2-items.png
[18]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-already-complete.png
[19]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-manage-nuget-packages-VS.png
[20]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-manage-nuget-packages-dialog.png

<!-- URLs. -->
[contrôle d'accès concurrentiel optimiste]: http://go.microsoft.com/fwlink/?LinkId=330935
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Compte Azure]: http://www.windowsazure.com/pricing/free-trial/
[Validate and modify data with scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Refine queries with paging]: /develop/mobile/tutorials/add-paging-to-data-dotnet
[Prise en main de Mobile Services]: /develop/mobile/tutorials/get-started
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-dotnet
[Ajouter l'authentification à votre application]: /develop/mobile/tutorials/get-started-with-users-dotnet
[Ajouter les notifications push à votre application]: /develop/mobile/tutorials/get-started-with-push-dotnet

[portail Azure Classic]: https://manage.windowsazure.com/
[Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268374
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268375
[Developer Code Samples site]: http://go.microsoft.com/fwlink/p/?LinkId=271146
[Propriétés système]: http://go.microsoft.com/fwlink/?LinkId=331143

<!---HONumber=AcomDC_0727_2016-->