<properties 
	pageTitle="Utilisez Mobile Services pour télécharger des images dans le stockage d’objets blob (Android) | Mobile Services"
	description="Découvrez comment utiliser Mobile Services pour télécharger des images Azure Storage vers le stockage d’objets blob Azure et y accéder à partir de votre application Android."
	services="mobile-services"
	documentationCenter="android"
	authors="RickSaling"
	manager="dwrede"
	editor=""/>

<tags 
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="09/02/2015"
	ms.author="ricksal"/>

# Télécharger des images vers Azure Storage depuis un appareil Android

[AZURE.INCLUDE [mobile-services-selector-upload-data-blob-storage](../../includes/mobile-services-selector-upload-data-blob-storage.md)]

Cette rubrique montre comment permettre à votre application Azure Mobile Services Android de télécharger des images vers Azure Storage.

Mobile Services utilise une base de données SQL pour stocker les données. Toutefois, elle est plus efficace pour stocker les données de l’objet binaire volumineux (BLOB) dans l’Azure Storage. Dans ce didacticiel, vous activez l’application de démarrage rapide Mobile Services pour prendre des photos avec l’appareil photo Android, puis télécharger les images vers Azure Storage.


## Ce dont vous avez besoin pour commencer

Avant de commencer ce didacticiel, vous devez avoir terminé le démarrage rapide Mobile Services : [Prise en main de Mobile Services].

Ce didacticiel requiert les éléments suivants :

+ Un [compte de stockage Azure](../storage-create-storage-account.md).
+ Un appareil Android avec un appareil photo

## Fonctionnement de l’application

Le téléchargement de la photo est un processus en plusieurs étapes :

- Tout d’abord vous prenez une photo et insérez une ligne TodoItem dans la base de données SQL qui contient les nouveaux champs de métadonnées utilisées par Azure Storage.
- Un nouveau service mobile script SQL d’**insertion** demande à Azure Storage une Signature d’accès partagé (SAP).
- Ce script renvoie la SAP et une URI vers l’objet blob au client.
- Le client télécharge la photo, à l’aide du SAP et l’URI d’objet blob.

Alors, qu’est ce qu’une SAP ?

Il n’est pas sûr de stocker les informations d’identification nécessaires au téléchargement des données Azure à l’intérieur de votre application cliente. Au lieu de cela, vous stockez ces informations d’identification dans votre service mobile et vous en servir pour générer une signature d’accès partagé (SAP) qui octroie l’autorisation de télécharger une nouvelle image. La signature d’accès partagé (SAP), information d’identification dont le délai d’expiration est de 5 minutes est renvoyée en toute sécurité par Mobile Services à l’application cliente. L'application utilise ensuite cette information d'identification provisoire pour télécharger l'image. Pour plus d’informations sur SAP, consultez la page [Signatures d’accès partagé, partie 1 : présentation du modèle SAP](storage-dotnet-shared-access-signature-part-1.md)

>[AZURE.NOTE] [Here](https://github.com/Azure/mobile-services-samples/tree/master/UploadImages) est la partie de code source client terminée de cette application.

## Mise à jour du script de la fonction insert inscrite dans le portail de gestion

[AZURE.INCLUDE [mobile-services-configure-blob-storage](../../includes/mobile-services-configure-blob-storage.md)]


## Mise à jour de l’application cliente de démarrage rapide servant à capturer et télécharger des images

### Référence la bibliothèque cliente Azure Storage Android.

1. Pour ajouter la référence à la bibliothèque, dans le fichier d’**application** > **build.gradle**, ajouter cette ligne à la `dependencies` section :

		compile 'com.microsoft.azure.android:azure-storage-android:0.6.0@aar'


2. Modifier la valeur `minSdkVersion` 15 et attribuez-lui la valeur 15 (requis par l’API d’appareil photo).

3. Appuyez sur l’icône **Synchronisation du projet avec les fichiers Gradle**.

### Mettre à jour le fichier manifeste pour le stockage et l’appareil photo

1. Spécifier votre application implique la mise à disposition d’un appareil photo par l’ajout de cette ligne **AndroidManifest.xml** :

	    <uses-feature android:name="android.hardware.camera"
	        android:required="true" />

2. La spécification de votre application doit avoir l’autorisation d’écrire dans le support de stockage externe en ajoutant cette ligne **AndroidManifest.xml** :

	    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

	Notez que Android de stockage externe n’est pas nécessairement une carte SD : pour plus d’informations, consultez l’[enregistrement des fichiers](http://developer.android.com/training/basics/data-storage/files.html).

### Mettre à jour des fichiers de ressources pour la nouvelle interface utilisateur

1. Ajouter des intitulés des nouveaux boutons en ajoutant le code suivant au fichier **strings.xml** dans le répertoire *valeurs* :

	    <string name="preview_button_text">Take Photo</string>
	    <string name="upload_button_text">Upload</string>

2. Dans le fichier **activity\_to\_do.xml** dans le **res = > mise en page**, ajouter le code existant pour le bouton **Ajouter**.

         <Button
             android:id="@+id/buttonPreview"
             android:layout_width="120dip"
             android:layout_height="wrap_content"
             android:onClick="takePicture"
             android:text="@string/preview_button_text" />

3. Remplacez l’élément du bouton **Ajouter** avec le code suivant :

         <Button
             android:id="@+id/buttonUpload"
             android:layout_width="100dip"
             android:layout_height="wrap_content"
             android:onClick="uploadPhoto"
             android:text="@string/upload_button_text" />


### Ajouter un code pour la capture de photo

1. Dans **ToDoActivity.java** ajoutez ce code pour créer un objet **fichier** avec un nom unique.

		// Create a File object for storing the photo
	    private File createImageFile() throws IOException {
	        // Create an image file name
	        String timeStamp = new SimpleDateFormat("yyyyMMdd_HHmmss").format(new Date());
	        String imageFileName = "JPEG_" + timeStamp + "_";
	        File storageDir = Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_PICTURES);
	        File image = File.createTempFile(
	                imageFileName,  /* prefix */
	                ".jpg",         /* suffix */
	                storageDir      /* directory */
	        );
	        return image;
	    }

5. Ajoutez ce code pour démarrer l’application de caméra Android. Vous pouvez prendre des photos et lorsque l’une d’elles semble parfaite, appuyer sur **Enregistrer**, ce qui la stockera dans le fichier que vous venez de créer.

		// Run an Intent to start up the Android camera
	    static final int REQUEST_TAKE_PHOTO = 1;
	    public Uri mPhotoFileUri = null;
	    public File mPhotoFile = null;
		
	    public void takePicture(View view) {
	        Intent takePictureIntent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
	        // Ensure that there's a camera activity to handle the intent
	        if (takePictureIntent.resolveActivity(getPackageManager()) != null) {
	            // Create the File where the photo should go
	            try {
	                mPhotoFile = createImageFile();
	            } catch (IOException ex) {
	                // Error occurred while creating the File
	                //
	            }
	            // Continue only if the File was successfully created
	            if (mPhotoFile != null) {
	                mPhotoFileUri = Uri.fromFile(mPhotoFile);
	                takePictureIntent.putExtra(MediaStore.EXTRA_OUTPUT, mPhotoFileUri);
	                startActivityForResult(takePictureIntent, REQUEST_TAKE_PHOTO);
	            }
	        }
	    }

### Ajoutez du code pour télécharger le fichier photo dans le stockage d’objets blob


1. Tout d’abord, nous ajoutons de nouveaux champs de métadonnées à l’objet `ToDoItem` en ajoutant ce code à **ToDoItem.java**.

		/**
	     *  imageUri - points to location in storage where photo will go
	     */
	    @com.google.gson.annotations.SerializedName("imageUri")
	    private String mImageUri;
	
	    /**
	     * Returns the item ImageUri
	     */
	    public String getImageUri() {
	        return mImageUri;
	    }
	
	    /**
	     * Sets the item ImageUri
	     *
	     * @param ImageUri
	     *            Uri to set
	     */
	    public final void setImageUri(String ImageUri) {
	        mImageUri = ImageUri;
	    }
	
	    /**
	     * ContainerName - like a directory, holds blobs
	     */
	    @com.google.gson.annotations.SerializedName("containerName")
	    private String mContainerName;
	
	    /**
	     * Returns the item ContainerName
	     */
	    public String getContainerName() {
	        return mContainerName;
	    }
	
	    /**
	     * Sets the item ContainerName
	     *
	     * @param ContainerName
	     *            Uri to set
	     */
	    public final void setContainerName(String ContainerName) {
	        mContainerName = ContainerName;
	    }
	
	    /**
	     *  ResourceName
	     */
	    @com.google.gson.annotations.SerializedName("resourceName")
	    private String mResourceName;
	
	    /**
	     * Returns the item ResourceName
	     */
	    public String getResourceName() {
	        return mResourceName;
	    }
	
	    /**
	     * Sets the item ResourceName
	     *
	     * @param ResourceName
	     *            Uri to set
	     */
	    public final void setResourceName(String ResourceName) {
	        mResourceName = ResourceName;
	    }
	
	    /**
	     *  SasQueryString - permission to write to storage
	     */
	    @com.google.gson.annotations.SerializedName("sasQueryString")
	    private String mSasQueryString;
	
	    /**
	     * Returns the item SasQueryString
	     */
	    public String getSasQueryString() {
	        return mSasQueryString;
	    }
	
	    /**
	     * Sets the item SasQueryString
	     *
	     * @param SasQueryString
	     *            Uri to set
	     */
	    public final void setSasQueryString(String SasQueryString) {
	        mSasQueryString = SasQueryString;
	    }

2. Remplacez le constructeur paramètre 0 par ce code :

	    /**
	     * ToDoItem constructor
	     */
	    public ToDoItem() {
	        mContainerName = "";
	        mResourceName = "";
	        mImageUri = "";
	        mSasQueryString = "";
	    }

3. Remplacez le constructeur multi-paramètre par ce code :

	    /**
	     * Initializes a new ToDoItem
	     *
	     * @param text
	     *            The item text
	     * @param id
	     *            The item id
	     */
	    public ToDoItem(String text,
	                    String id,
	                    String containerName,
	                    String resourceName,
	                    String imageUri,
	                    String sasQueryString) {
	        this.setText(text);
	        this.setId(id);
	        this.setContainerName(containerName);
	        this.setResourceName(resourceName);
	        this.setImageUri(imageUri);
	        this.setSasQueryString(sasQueryString);
	    }


4. Dans le fichier **ToDoActivity.java**, remplacez la méthode **addItem** dans **ToDoActivity.java** avec le code suivant qui télécharge l’image.

	    /**
	     * Add a new item
	     *
	     * @param view
	     *            The view that originated the call
	     */
	    public void uploadPhoto(View view) {
	        if (mClient == null) {
	            return;
	        }
	
	        // Create a new item
	        final ToDoItem item = new ToDoItem();
	
	        item.setText(mTextNewToDo.getText().toString());
	        item.setComplete(false);
	        item.setContainerName("todoitemimages");
	
	        // Use a unigue GUID to avoid collisions.
	        UUID uuid = UUID.randomUUID();
	        String uuidInString = uuid.toString();
	        item.setResourceName(uuidInString);
	
	        // Send the item to be inserted. When blob properties are set this
	        // generates an SAS in the response.
	        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
	            @Override
	            protected Void doInBackground(Void... params) {
	                try {
		                    final ToDoItem entity = addItemInTable(item);
		
		                    // If we have a returned SAS, then upload the blob.
		                    if (entity.getSasQueryString() != null) {
		
	                       // Get the URI generated that contains the SAS
	                        // and extract the storage credentials.
	                        StorageCredentials cred = 
								new StorageCredentialsSharedAccessSignature(entity.getSasQueryString());
	                        URI imageUri = new URI(entity.getImageUri());
	
	                        // Upload the new image as a BLOB from a stream.
	                        CloudBlockBlob blobFromSASCredential =
	                                new CloudBlockBlob(imageUri, cred);
	
	                        blobFromSASCredential.uploadFromFile(mPhotoFileUri.getPath());
  	                    }
	
	                    runOnUiThread(new Runnable() {
	                        @Override
	                        public void run() {
	                            if(!entity.isComplete()){
	                                mAdapter.add(entity);
	                            }
	                        }
	                    });
	                } catch (final Exception e) {
	                    createAndShowDialogFromTask(e, "Error");
	                }
	                return null;
	            }
	        };
	
	        runAsyncTask(task);
	
	        mTextNewToDo.setText("");
	    }
	

Ce code envoie une demande au service mobile pour insérer un nouvel élément TodoItem. La réponse contient les SAP qui sont ensuite utilisées pour télécharger l’image à partir du stockage local vers le stockage des objets blob Azure.


## Tester le téléchargement des images 

1. Dans Android Studio, appuyez sur **Exécuter**. Dans la boîte de dialogue, choisissez le périphérique à utiliser.

2. Lorsque l’application de l’interface utilisateur s’affiche, saisissez du texte dans la zone de texte intitulée **Ajouter un élément ToDo**.

3. Appuyez sur **Prendre une Photo**. Au démarrage de l’application de caméra, prenez une photo. Appuyez sur la coche pour accepter le nom de domaine.

4. Appuyez sur **Télécharger**. Notez que le ToDoItem a été ajouté à la liste, comme d’habitude.

5. Dans le portail Microsoft Azure, accédez à votre compte de stockage, appuyez sur l’onglet **Conteneurs**, appuyez sur le nom de votre conteneur dans la liste.

6. Une liste des fichiers d’objets blob téléchargés s’affiche. Sélectionnez-en une et appuyez sur **Télécharger**.

7. L’image que vous avez téléchargée apparaît maintenant dans une fenêtre de navigateur.


## <a name="next-steps"> </a>Étapes suivantes

Maintenant que vous avez intégré votre service mobile au service BLOB et que vous êtes en mesure de télécharger des images en toute sécurité, consultez les autres rubriques ci-dessous relatives au service backend et à l'intégration :

+ [Envoyer un courrier électronique à partir de Mobile Services avec SendGrid]
 
  Découvrez comment ajouter la fonctionnalité de messagerie électronique à votre service mobile en utilisant le service de messagerie SendGrid. Cette rubrique montre comment ajouter des scripts côté serveur pour envoyer du courrier électronique à l'aide de SendGrid.

+ [Planifier les tâches du backend dans Mobile Services]

  Découvrez comment utiliser la fonctionnalité de planification de travaux Mobile Services pour définir du code de script serveur exécuté selon une planification que vous définissez.

+ [Référence de script serveur Mobile Services]

  Rubriques de référence pour l'utilisation de scripts serveur en vue d'effectuer des tâches côté serveur et une intégration à d'autres composants Azure et ressources externes.
 
+ [Guide de fonctionnement Mobile Services .NET]

  Découvrez plus en détail comment utiliser Mobile Services avec .NET
  
 
<!-- Anchors. -->
[Install the Storage Client library]: #install-storage-client
[Update the client app to capture images]: #add-select-images
[Update the insert script to generate an SAS]: #update-scripts
[Upload images to test the app]: #test
[Next Steps]: #next-steps

<!-- Images. -->

[2]: ./media/mobile-services-windows-store-dotnet-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png


<!-- URLs. -->
[Envoyer un courrier électronique à partir de Mobile Services avec SendGrid]: store-sendgrid-mobile-services-send-email-scripts.md
[Planifier les tâches du backend dans Mobile Services]: mobile-services-schedule-recurring-tasks.md
[Send push notifications to Windows Store apps using Service Bus from a .NET back-end]: http://go.microsoft.com/fwlink/?LinkId=277073&clcid=0x409
[Référence de script serveur Mobile Services]: mobile-services-how-to-use-server-scripts.md
[Prise en main de Mobile Services]: mobile-services-javascript-backend-windows-store-dotnet-get-started.md

[Azure Management Portal]: https://manage.windowsazure.com/
[How To Create a Storage Account]: ../storage-create-storage-account.md
[Azure Storage Client library for Store apps]: http://go.microsoft.com/fwlink/p/?LinkId=276866
[Guide de fonctionnement Mobile Services .NET]: mobile-services-windows-dotnet-how-to-use-client-library.md
[App settings]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7
 

<!---HONumber=September15_HO1-->