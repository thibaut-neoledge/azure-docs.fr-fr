

Mise à jour de l'application cliente de démarrage rapide pour capturer et télécharger des images
------------------------------------------------------------------------------------------------

Dans cette section, vous allez mettre à jour le projet du didacticiel [Prise en main de Mobile Services] pour prendre des photos et les télécharger dans le stockage d'objets blob Azure. Pour capturer l'image, ce didacticiel utilise la tâche [CameraCaptureTask] de l'espace de noms `Microsoft.Phone.Tasks`. Cette classe lance l'interface utilisateur de l'appareil photo sur l'appareil Windows Phone afin de capturer la photo, puis enregistre automatiquement l'image dans la Pellicule de l'appareil Windows Phone. Si vous ne souhaitez pas enregistrer les images dans la Pellicule, utilisez la classe [PhotoCamera] de l'espace de noms `Microsoft.Devices`.

1.  Dans l'Explorateur de solutions de Visual Studio, sous le projet, développez **Propriétés**. Ouvrez ensuite le fichier WMAppManifest.xml et, dans l'onglet **Capacités**, activez l'appareil photo en cliquant sur **ID\_CAP\_ISV\_CAMERA**. Fermez le fichier pour enregistrer vos modifications.

	![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-WMAppmanifest-wp8.png)

	Votre application pourra ainsi utiliser un appareil photo associé à l'ordinateur. Les utilisateurs seront invités à autoriser l'accès à l'appareil photo lors de la première exécution de l'application.

2.  Ouvrez le fichier MainPage.xaml et remplacez l'élément **Grid** intitulé **ContentPanel** par le code suivant :

         <!--ContentPanel - placez ici le contenu supplémentaire-->
         <Grid x:Name="ContentPanel" Grid.Row="1" Margin="12,0,12,0">
             <Grid.RowDefinitions>
                 <RowDefinition Height="Auto" />
                 <RowDefinition Height="Auto" />
                 <RowDefinition Height="Auto" />
                 <RowDefinition Height="Auto" />
                 <RowDefinition Height="Auto" />
                 <RowDefinition Height="*" />
             </Grid.RowDefinitions>
             <Grid.ColumnDefinitions>
                 <ColumnDefinition Width="2*" />
                 <ColumnDefinition Width="2*" />
             </Grid.ColumnDefinitions>
             <TextBlock Grid.Row="0" Grid.ColumnSpan="2" Text="Entrez du texte ci-dessous et cliquez sur Capturer l'image pour ajouter une image capturée. Cliquez ensuite sur Enregistrer pour insérer un nouvel élément TodoItem dans votre base de données" TextWrapping="Wrap" Margin="12"/>
             <TextBox Grid.Row="1" Grid.ColumnSpan="2" Name="TodoInput" Text="" />
             <Button Name="ButtonCaptureImage" Grid.Row="2" Click="ButtonCaptureImage_Click">Capture Image</Button>
             <Button Grid.Row ="2" Grid.Column="1" Name="ButtonSave" Click="ButtonSave_Click">Save</Button>
             <TextBlock Grid.Row="3" Grid.ColumnSpan="2" Text="Cliquez sur Actualiser ci-dessous pour charger les éléments TodoItem non terminés à partir de votre base de données. Utilisez la case à cocher pour terminer et mettre à jour vos éléments TodoItem" TextWrapping="Wrap" Margin="12" />
             <Button Grid.Row="4" Grid.ColumnSpan="2" Name="ButtonRefresh" Click="ButtonRefresh_Click">Refresh</Button>
             <phone:LongListSelector Grid.Row="5" Grid.ColumnSpan="2" Name="ListItems">
                 <phone:LongListSelector.ItemTemplate>
                     <DataTemplate>
                         <StackPanel Orientation="Vertical">
                             <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" Checked="CheckBoxComplete_Checked" Content="{Binding Text}" Margin="10,5" VerticalAlignment="Center"/>
                             <Image Name="ImageUpload" Source="{Binding ImageUri, Mode=OneWay}" MaxHeight="150"/>
                         </StackPanel>
                     </DataTemplate>
                 </phone:LongListSelector.ItemTemplate>
             </phone:LongListSelector>
         </Grid>


	Un nouveau bouton est ajouté pour lancer la tâche [CameraCaptureTask], une image est ajoutée au modèle **ItemTemplate** et sa source de liaison est définie en tant qu'URI de l'image téléchargée dans le service de stockage d'objets blob.

3.  Ouvrez le fichier projet MainPage.xaml.cs et ajoutez les instructions **using** suivantes :

         using Microsoft.Phone.Tasks;
         using System.IO;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Blob;

4.  Dans le fichier projet MainPage.xaml.cs, mettez à jour la classe TodoItem en ajoutant les propriétés suivantes :

         [JsonProperty(PropertyName = "containerName")]
         public string ContainerName { get; set; }
            
         [JsonProperty(PropertyName = "resourceName")]
         public string ResourceName { get; set; }
            
         [JsonProperty(PropertyName = "sasQueryString")]
         public string SasQueryString { get; set; }
            
         [JsonProperty(PropertyName = "imageUri")]
         public string ImageUri { get; set; } 

	<div class="dev-callout"><b>Note</b>
	<p>Pour ajouter de nouvelles propriétés à l'objet TodoItem, le schéma dynamique doit être activé dans votre service mobile. Lorsque le schéma dynamique est activé, de nouvelles colonnes sont automatiquement ajoutées à la table TodoItem. Celles-ci sont mappées vers ces nouvelles propriétés.</p>
	</div>

5.  Dans le fichier projet MainPage.xaml.cs, mettez à jour la classe MainPage. Ajoutez le code suivant pour déclarer la tâche [CameraCaptureTask] et un objet de flux qui référencera l'image capturée :

         // Utilisation de CameraCaptureTask pour autoriser l'utilisateur à capturer l'image d'un élément todo //
         CameraCaptureTask cameraCaptureTask;
            
         // Utilisation d'une référence de flux pour télécharger l'image dans le stockage d'objets blob.
         Stream imageStream = null;

6.  Dans le fichier projet MainPage.xaml.cs, mettez à jour la classe MainPage. Ajoutez le code suivant pour mettre à jour le constructeur, créer la tâche CameraCaptureTask et ajouter un gestionnaire d'événements pour l'événement Completed :

         // Constructeur
         public MainPage()
         {
             InitializeComponent();
                
             cameraCaptureTask = new CameraCaptureTask();
             cameraCaptureTask.Completed += cameraCaptureTask_Completed;
         }
            
         void cameraCaptureTask_Completed(object sender, PhotoResult e)
         {
             imageStream = e.ChosenPhoto;
         }

7.  Dans le fichier projet MainPage.xaml.cs, mettez à jour la classe MainPage. Ajoutez le code suivant pour afficher l'interface utilisateur de l'appareil photo et autoriser l'utilisateur à capturer une image lorsque le bouton **Capturer l'image** est utilisé :

         private void ButtonCaptureImage_Click(object sender, RoutedEventArgs e)
         {
             cameraCaptureTask.Show();
         }

8.  Dans le fichier projet MainPage.xaml.cs, mettez à jour la classe MainPage. Remplacez la méthode `InsertTodoItem` existante par le code suivant :

         private async void InsertTodoItem(TodoItem todoItem)
         {
             string errorString = string.Empty;            
                
             if (imageStream != null)
             {
                 // Définissez les propriétés blob de l'élément TodoItem.
                 todoItem.ContainerName = "todoitemimages";
                 todoItem.ResourceName = Guid.NewGuid().ToString() + ".jpg";
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
                    
                 // Téléchargez la nouvelle image en tant qu'objet BLOB à partir du flux.
                 CloudBlockBlob blobFromSASCredential =
                     container.GetBlockBlobReference(todoItem.ResourceName);
                 await blobFromSASCredential.UploadFromStreamAsync(imageStream);
                    
                 // Lorsque vous demandez une signature d'accès partagé au niveau du conteneur et non au niveau de l'objet blob,
                 // vous pouvez télécharger plusieurs flux en utilisant les mêmes informations d'identification de conteneur.

                 imageStream = null;
             }              
                
             // Ajoutez le nouvel élément à la collection.
             items.Add(todoItem);
             TodoInput.Text = "";
         }

    Ce code envoie une requête au service mobile pour insérer un nouvel élément TodoItem, avec le nom du fichier image. La réponse contient la signature d'accès partagé, qui est ensuite utilisée pour insérer l'image dans le stockage d'objets BLOB, ainsi que l'URI de l'image pour la liaison des données.

La dernière étape consiste à tester l'application et à confirmer que les téléchargements ont abouti.

Test du téléchargement des images dans votre application
--------------------------------------------------------

1.  Dans Visual Studio, vous pouvez appuyer sur la touche F5 pour tester l'application dans l'émulateur ou avec un appareil ciblé.

2.  Entrez du texte dans la zone de texte, puis cliquez sur **Capturer l'image**.

	![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-wp8.png)

	L'interface utilisateur de capture de l'appareil photo s'affiche.

1.  Cliquez sur l'image ou sur le bouton de capture instantanée du téléphone pour prendre une photo.

	![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-camera-wp8.png)

2.  Cliquez sur **Accepter** pour accepter l'image et quitter l'interface utilisateur de l'appareil photo.

    ![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-camera-accept-wp8.png)

3.  Cliquez sur **Enregistrer** pour insérer le nouvel élément et télécharger l'image.

    ![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-save-wp8.png)

4.  Le nouvel élément et l'image téléchargée apparaissent dans la vue liste.

    ![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-final-wp8.png)

    > [WACOM.NOTE] L'image est automatiquement téléchargée depuis le service de stockage d'objets blob lorsque la propriété `imageUri` du nouvel élément est liée au contrôle **Image**.


