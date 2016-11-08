## Installation du client de stockage pour les applications du Windows Store
Avant de pouvoir utiliser une signature d'accès partagé en vue de télécharger des images vers un stockage d'objets blob, vous devez ajouter le package NuGet qui installe la bibliothèque du client de stockage pour les applications du Windows Store.

1. Dans l'**Explorateur de solutions** de Visual Studio, cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **Manage NuGet Packages**.
2. Dans le volet gauche, sélectionnez la catégorie **En ligne**, recherchez `WindowsAzure.Storage`, cliquez sur **Installer** au niveau du package **Azure Storage**, puis acceptez les contrats de licence.
   
      ![](./media/mobile-services-windows-universal-dotnet-upload-to-blob-storage/mobile-add-storage-nuget-package-dotnet.png)
   
      La bibliothèque du client des services de stockage Azure est alors ajoutée au projet.

Dans la prochaine étape, vous allez mettre à jour l'application de démarrage rapide pour capturer et télécharger des images.

## Mise à jour de l'application cliente de démarrage rapide pour capturer et télécharger des images
1. Dans Visual Studio, ouvrez le fichier Package.appxmanifest pour le projet d’application Windows et dans l’onglet **Capacités**, activez les capacités **Webcam** et **Microphone**.
   
       ![](./media/mobile-services-windows-universal-dotnet-upload-to-blob-storage/mobile-app-manifest-camera.png)
   
       Votre application pourra ainsi utiliser un appareil photo associé à l'ordinateur. Les utilisateurs seront invités à autoriser l'accès à l'appareil photo lors de la première exécution de l'application.
2. Répétez l’étape précédente pour le projet d’application Windows Phone.
3. Dans le projet d’application Windows, ouvrez le fichier MainPage.xaml et remplacez l’élément **StackPanel** juste après le premier élément **QuickStartTask** par le code suivant :
   
        <StackPanel Orientation="Horizontal" Margin="72,0,0,0">
            <TextBox Name="TextInput" Margin="5" MaxHeight="40" MinWidth="300"></TextBox>
            <AppBarButton Label="Photo" Icon="Camera" Name="ButtonCapture" Click="ButtonCapture_Click" />
            <AppBarButton Label="Upload" Icon="Upload" Name="ButtonSave" Click="ButtonSave_Click"/>
        </StackPanel>
        <Grid Name="captureGrid" Margin="62,0,0,0" Visibility="Collapsed" >
            <Grid.RowDefinitions>
                <RowDefinition Height="*" />
                <RowDefinition Height="Auto" />
            </Grid.RowDefinitions>
            <CaptureElement x:Name="previewElement" Grid.Row="0" Tapped="previewElement_Tapped" />
            <Image Name="imagePreview" Grid.Row="0"  />
            <StackPanel Name="captureButtons" Orientation="Horizontal" Grid.Row="1">
                <TextBlock Name="TextCapture" />
                <AppBarButton Label="Retake" Icon="Redo" Name="ButtonRetake" Click="ButtonCapture_Click" />
                <AppBarButton Label="Cancel" Icon="Cancel" Name="ButtonCancel" Click="ButtonCancel_Click" />
            </StackPanel>
        </Grid>
4. Remplacez l'élément **StackPanel** de **DataTemplate** par le code suivant :
   
        <StackPanel Orientation="Vertical">
            <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" 
                        Checked="CheckBoxComplete_Checked" Content="{Binding Text}" 
                        Margin="10,5" VerticalAlignment="Center"/>
            <Image Name="ImageUpload" Source="{Binding ImageUri, Mode=OneWay}"
                    MaxHeight="250"/>
        </StackPanel> 
   
       Une image est ajoutée au modèle **ItemTemplate** et sa source de liaison est définie en tant qu'URI de l'image téléchargée dans le service de stockage d'objets blob.
5. Dans le projet d’application Windows Phone, ouvrez le fichier MainPage.xaml et remplacez l’élément **ButtonSave** par le code suivant :
   
        <StackPanel Grid.Row ="1" Grid.Column="1"  Orientation="Horizontal">
            <AppBarButton Label="Photo" Icon="Camera" Name="ButtonCapture" 
                          Click="ButtonCapture_Click" Height="78" Width="62" />
            <AppBarButton Label="Upload" Icon="Upload" Name="ButtonSave" 
                          Click="ButtonSave_Click"/>
        </StackPanel>
        <Grid Grid.Row="2" Name="captureGrid" Grid.RowSpan="3" Grid.ColumnSpan="2" 
              Canvas.ZIndex="99" Background="{ThemeResource ApplicationPageBackgroundThemeBrush}" 
              Visibility="Collapsed">
            <Grid.RowDefinitions>
                <RowDefinition Height="*" />
                <RowDefinition Height="Auto" />
            </Grid.RowDefinitions>
            <CaptureElement Grid.Row="0" x:Name="previewElement" Tapped="previewElement_Tapped" />                    
            <Image Grid.Row="0" Name="imagePreview" Visibility="Collapsed" />
            <StackPanel Grid.Row="1" Name="captureButtons" 
                        Orientation="Horizontal" Visibility="Collapsed">
                <TextBlock Name="TextCapture" VerticalAlignment="Bottom" />
                <AppBarButton Label="Retake" Icon="Redo" Name="ButtonRetake" 
                              Click="ButtonRetake_Click" />
                <AppBarButton Label="Cancel" Icon="Cancel" Name="ButtonCancel" 
                              Click="ButtonCancel_Click" />
            </StackPanel>
        </Grid>
6. Remplacez l'élément **StackPanel** de **DataTemplate** par le code suivant :
   
        <StackPanel Orientation="Vertical">
            <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" 
                      Checked="CheckBoxComplete_Checked" Content="{Binding Text}" 
                      Margin="10,5" VerticalAlignment="Center"/>
            <Image Name="ImageUpload" Source="{Binding ImageUri, Mode=OneWay}" 
                   MaxHeight="150"/>
        </StackPanel>
7. Dans le dossier partagé DataModel, ouvrez le fichier de projet TodoItem.cs et ajoutez les propriétés suivantes à la classe TodoItem :
   
        [JsonProperty(PropertyName = "containerName")]
        public string ContainerName { get; set; }
   
        [JsonProperty(PropertyName = "resourceName")]
        public string ResourceName { get; set; }
   
        [JsonProperty(PropertyName = "sasQueryString")]
        public string SasQueryString { get; set; }
   
        [JsonProperty(PropertyName = "imageUri")]
        public string ImageUri { get; set; } 
8. Ouvrez le fichier de projet partagé MainPage.cs et ajoutez les instructions **using** suivantes :
   
        using Windows.Media.Capture;
        using Windows.Media.MediaProperties;
        using Windows.Storage;
        using Windows.UI.Xaml.Input;
        using Microsoft.WindowsAzure.Storage.Auth;
        using Microsoft.WindowsAzure.Storage.Blob;
        using Windows.UI.Xaml.Media.Imaging;
9. Dans la classe MainPage, ajoutez le code suivant :
   
        // Use a StorageFile to hold the captured image for upload.
        StorageFile media = null;
        MediaCapture cameraCapture;
        bool IsCaptureInProgress;
   
        private async Task CaptureImage()
        {
            // Capture a new photo or video from the device.
            cameraCapture = new MediaCapture();
            cameraCapture.Failed += cameraCapture_Failed;
   
            // Initialize the camera for capture.
            await cameraCapture.InitializeAsync();
   
            #if WINDOWS_PHONE_APP
            cameraCapture.SetPreviewRotation(VideoRotation.Clockwise90Degrees);
            cameraCapture.SetRecordRotation(VideoRotation.Clockwise90Degrees);
            #endif
   
            captureGrid.Visibility = Windows.UI.Xaml.Visibility.Visible;
            previewElement.Visibility = Windows.UI.Xaml.Visibility.Visible;
            previewElement.Source = cameraCapture;
            await cameraCapture.StartPreviewAsync();
        }
   
        private async void previewElement_Tapped(object sender, TappedRoutedEventArgs e)
        {
            // Block multiple taps.
            if (!IsCaptureInProgress)
            {                
                IsCaptureInProgress = true;
   
                // Create the temporary storage file.
                media = await ApplicationData.Current.LocalFolder
                    .CreateFileAsync("capture_file.jpg", CreationCollisionOption.ReplaceExisting);
   
                // Take the picture and store it locally as a JPEG.
                await cameraCapture.CapturePhotoToStorageFileAsync(
                    ImageEncodingProperties.CreateJpeg(), media);
   
                captureButtons.Visibility = Visibility.Visible;
   
                // Use the stored image as the preview source.
                BitmapImage tempBitmap = new BitmapImage(new Uri(media.Path));
                imagePreview.Source = tempBitmap;
                imagePreview.Visibility = Visibility.Visible;
                previewElement.Visibility = Visibility.Collapsed;
                IsCaptureInProgress = false;
            }
        }
   
        private async void cameraCapture_Failed(MediaCapture sender, MediaCaptureFailedEventArgs errorEventArgs)
        {
            // It's safest to return this back onto the UI thread to show the message dialog.
            MessageDialog dialog = new MessageDialog(errorEventArgs.Message);
            await this.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, 
                async () => { await dialog.ShowAsync(); });            
        }
   
        private async void ButtonCapture_Click(object sender, RoutedEventArgs e)
        {
            // Prevent multiple initializations.
            ButtonCapture.IsEnabled = false;
   
            await CaptureImage();
        }
   
        private async void ButtonRetake_Click(object sender, Windows.UI.Xaml.RoutedEventArgs e)
        {
            // Reset the capture and then start again.
            await ResetCaptureAsync();
            await CaptureImage();
        }
   
        private async void ButtonCancel_Click(object sender, Windows.UI.Xaml.RoutedEventArgs e)
        {
            await ResetCaptureAsync();
        }
   
        private async Task ResetCaptureAsync()
        {
            captureGrid.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
            imagePreview.Visibility = Visibility.Collapsed;
            captureButtons.Visibility = Visibility.Collapsed;
            previewElement.Source = null;
            ButtonCapture.IsEnabled = true;
   
            // Make sure we stop the preview and release resources.
            await cameraCapture.StopPreviewAsync();
            cameraCapture.Dispose();
            media = null;
        }
   
      Ce code affiche l’interface utilisateur utilisée pour capturer une image et enregistre cette image dans un fichier de stockage.
10. Remplacez la méthode `InsertTodoItem` existante par le code suivant :
    
         private async Task InsertTodoItem(TodoItem todoItem)
         {
             string errorString = string.Empty;
    
             if (media != null)
             {
                 // Set blob properties of TodoItem.
                 todoItem.ContainerName = "todoitemimages";
    
                 // Use a unigue GUID to avoid collisions.
                 todoItem.ResourceName = Guid.NewGuid().ToString();
             }
    
             // Send the item to be inserted. When blob properties are set this
             // generates an SAS in the response.
             await todoTable.InsertAsync(todoItem);
    
             // If we have a returned SAS, then upload the blob.
             if (!string.IsNullOrEmpty(todoItem.SasQueryString))
             {
                 // Get the URI generated that contains the SAS 
                 // and extract the storage credentials.
                 StorageCredentials cred = new StorageCredentials(todoItem.SasQueryString);
                 var imageUri = new Uri(todoItem.ImageUri);
    
                 // Instantiate a Blob store container based on the info in the returned item.
                 CloudBlobContainer container = new CloudBlobContainer(
                     new Uri(string.Format("https://{0}/{1}",
                         imageUri.Host, todoItem.ContainerName)), cred);
    
                 // Get the new image as a stream.
                 using (var inputStream = await media.OpenReadAsync())
                 {
                     // Upload the new image as a BLOB from the stream.
                     CloudBlockBlob blobFromSASCredential =
                         container.GetBlockBlobReference(todoItem.ResourceName);
                     await blobFromSASCredential.UploadFromStreamAsync(inputStream);
                 }
    
                 // When you request an SAS at the container-level instead of the blob-level,
                 // you are able to upload multiple streams using the same container credentials.
    
                 await ResetCaptureAsync();
             }
    
             // Add the new item to the collection.
             items.Add(todoItem);
         }
    
     Ce code envoie une demande au service mobile pour insérer un nouvel élément TodoItem. La réponse contient les SAP, qui sont ensuite utilisées pour télécharger l’image à partir du stockage local vers le stockage des objets blob Azure. L’URL de l’image chargée est utilisée dans la liaison de données.

La dernière étape consiste à tester les deux versions de l’application et à confirmer que les téléchargements ont abouti à partir des deux appareils.

## <a name="test"></a>Tester le téléchargement des images dans votre application
1. Dans Visual Studio, assurez-vous que le projet Windows est défini comme le projet par défaut, puis appuyez sur la touche F5 pour exécuter l’application.
2. Entrez le texte dans la zone de texte située sous **Insert a TodoItem**, puis cliquez sur **Photo**.
3. Cliquez ou appuyez sur la version préliminaire pour prendre une photo, puis cliquez sur **Télécharger** pour insérer le nouvel élément et télécharger l’image.
   
    ![](./media/mobile-services-windows-universal-dotnet-upload-to-blob-storage/mobile-quickstart-blob-appbar2.png)
4. Le nouvel élément et l'image téléchargée apparaissent dans la vue liste.
   
    ![](./media/mobile-services-windows-universal-dotnet-upload-to-blob-storage/mobile-quickstart-blob-ie.png)
   
       >[AZURE.NOTE]L’image est automatiquement téléchargée depuis le service de stockage d’objets blob lorsque la propriété *imageUri* du nouvel élément est liée au contrôle **Image**.
5. Arrêtez l’application et redémarrez la version du projet Windows Phone de l’application.
   
    L’image téléchargée précédemment s’affiche.
6. Comme auparavant, entrez du texte dans la zone de texte, puis cliquez sur **Photo**.
   
       ![](./media/mobile-services-windows-universal-dotnet-upload-to-blob-storage/mobile-upload-blob-app-view-wp8.png)
7. Cliquez sur la version préliminaire pour prendre une photo, puis cliquez sur **Télécharger** pour insérer le nouvel élément et télécharger l’image.
   
    ![](./media/mobile-services-windows-universal-dotnet-upload-to-blob-storage/mobile-upload-blob-app-view-final-wp8.png)

Vous avez terminé le didacticiel sur le téléchargement des images.

<!---HONumber=Oct15_HO3-->