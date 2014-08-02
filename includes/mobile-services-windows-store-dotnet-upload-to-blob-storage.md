
Mise à jour de l'application cliente de démarrage rapide pour capturer et télécharger des images
------------------------------------------------------------------------------------------------

1.  Dans Visual Studio 2012, ouvrez le fichier Package.appxmanifest et dans l'onglet **Capacités**, activez les capacités **Webcam** et **Microphone**.

	![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-app-manifest-camera.png)
         
	Votre application pourra ainsi utiliser un appareil photo associé à l'ordinateur. Les utilisateurs seront invités à autoriser l'accès à l'appareil photo lors de la première exécution de l'application.

2.  Ouvrez le fichier MainPage.xaml et remplacez l'élément **StackPanel** situé directement après le premier élément **Task** par le code suivant :

         <StackPanel Orientation="Horizontal" Margin="72,0,0,0">
             <TextBox Name="TextInput" Margin="5" MaxHeight="40" MinWidth="300"></TextBox>
             <Button Name="btnTakePhoto" Style="{StaticResource PhotoAppBarButtonStyle}"
                     Click="OnTakePhotoClick" />
             <Button Name="ButtonSave" Style="{StaticResource UploadAppBarButtonStyle}" 
                     Click="ButtonSave_Click"/>
         </StackPanel>

3.  Remplacez l'élément **StackPanel** de **DataTemplate** par le code suivant :

         <StackPanel Orientation="Vertical">
             <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" 
                         Checked="CheckBoxComplete_Checked" Content="{Binding Text}" 
                         Margin="10,5" VerticalAlignment="Center"/>
             <Image Name="ImageUpload" Source="{Binding ImageUri, Mode=OneWay}"
                     MaxHeight="250"/>
         </StackPanel> 

	Cela ajoute une image à **ItemTemplate** et définit sa source de liaison en tant qu'URI de l'image téléchargée dans le service de stockage d'objets blob.

4.  Ouvrez le fichier projet MainPage.xaml.cs et ajoutez les instructions **using** suivantes :

         using Windows.Media.Capture;
         using Windows.Storage;
         using Windows.UI.Popups;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Blob;

5.  Dans la classe TodoItem, ajoutez les propriétés suivantes :

         [JsonProperty(PropertyName = "containerName")]
         public string ContainerName { get; set; }
            
         [JsonProperty(PropertyName = "resourceName")]
         public string ResourceName { get; set; }
            
         [JsonProperty(PropertyName = "sasQueryString")]
         public string SasQueryString { get; set; }
            
         [JsonProperty(PropertyName = "imageUri")]
         public string ImageUri { get; set; } 

	>[WACOM.NOTE]Pour ajouter de nouvelles propriétés à l'objet TodoItem, le schéma dynamique doit être activé dans votre service mobile. Lorsque le schéma dynamique est activé, de nouvelles colonnes sont automatiquement ajoutées à la table TodoItem. Celles-ci sont mappées vers ces nouvelles propriétés.

6.  Dans la classe MainPage, ajoutez le code suivant :

         // Utilisez un fichier de stockage pour contenir l'image capturée à télécharger.
         StorageFile media = null;
            
         private async void OnTakePhotoClick(object sender, RoutedEventArgs e)
         {
             // Capturez une nouvelle photo ou une nouvelle vidéo depuis l'appareil.
             CameraCaptureUI cameraCapture = new CameraCaptureUI();
             media = await cameraCapture
                 .CaptureFileAsync(CameraCaptureUIMode.PhotoOrVideo);
         }

	Ce code affiche l'interface utilisateur de l'appareil photo pour capturer une image et enregistre cette image dans un fichier de stockage.

1.  Remplacez la méthode `InsertTodoItem` existante par le code suivant :

         private async void InsertTodoItem(TodoItem todoItem)
         {
             string errorString = string.Empty;
                
             if (media != null)
             {
                 // Définissez les propriétés blob de l'élément TodoItem.
                 todoItem.ContainerName = "todoitemimages";
                 todoItem.ResourceName = media.Name;
             }
                
             // Envoyez l'élément à insérer. Lorsque les propriétés blob sont définies, 
             // une signature d'accès partagé est générée dans la réponse.
             await todoTable.InsertAsync(todoItem);
                
             // Si une signature d'accès partagé est renvoyée, téléchargez l'objet blob.
             if (!string.IsNullOrEmpty(todoItem.SasQueryString))
             {
                 // Récupérez l'URI généré contenant la signature d'accès partagé 
                 // et extrayez les informations d'identification de stockage.
                 StorageCredentials cred = new StorageCredentials(todoItem.SasQueryString);
                 var imageUri = new Uri(todoItem.ImageUri);
                    
                 // Instanciez un conteneur de stockage d'objets blob à partir des informations contenues dans l'élément renvoyé.
                 CloudBlobContainer container = new CloudBlobContainer(
                     new Uri(string.Format("https://{0}/{1}",
                         imageUri.Host, todoItem.ContainerName)), cred);

                 // Obtenez la nouvelle image en tant que flux.
                 using (var fileStream = await media.OpenStreamForReadAsync())
                 {                                      
                     // Téléchargez la nouvelle image en tant qu'objet blob depuis le flux.
                     CloudBlockBlob blobFromSASCredential =
                         container.GetBlockBlobReference(todoItem.ResourceName);
                     await blobFromSASCredential.UploadFromStreamAsync(fileStream.AsInputStream());
                 }
                    
                 // Lorsque vous demandez une signature d'accès partagé au niveau du conteneur et non au niveau de l'objet blob,
                 // vous pouvez télécharger plusieurs flux en utilisant les mêmes informations d'identification de conteneur.
             }
                
             // Ajoutez le nouvel élément à la collection.
             items.Add(todoItem);
         }

    Ce code envoie une requête au service mobile pour insérer un nouvel élément TodoItem, avec le nom du fichier image. La réponse contient la signature d'accès partagé, qui est ensuite utilisée pour insérer l'image dans le stockage d'objets BLOB, ainsi que l'URI de l'image pour la liaison des données.

La dernière étape consiste à tester l'application et à confirmer que les téléchargements ont abouti.

Test du téléchargement des images dans votre application
--------------------------------------------------------

1.  Dans Visual Studio, appuyez sur la touche F5 pour exécuter l'application.

2.  Entrez le texte dans la zone de texte située sous **Insert a TodoItem**, puis cliquez sur **Photo**.

	![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-quickstart-blob-appbar.png)

	L'interface utilisateur de capture de l'appareil photo s'affiche.

1.  Cliquez sur l'image pour prendre une photo, puis sur **OK**.

	![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-quickstart-blob-camera.png)

2.  Cliquez sur **Télécharger** pour insérer le nouvel élément et télécharger l'image.

    ![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-quickstart-blob-appbar2.png)

3.  Le nouvel élément et l'image téléchargée apparaissent dans la vue liste.

    ![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-quickstart-blob-ie.png)

	>[WACOM.NOTE]L'image est automatiquement téléchargée depuis le service de stockage d'objets blob lorsque la propriété <code>imageUri</code> pdu nouvel élément est liée au contrôle <strong>Image</strong>.


