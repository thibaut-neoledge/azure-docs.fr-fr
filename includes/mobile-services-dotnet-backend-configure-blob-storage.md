
##Installation du client de stockage dans le projet de service mobile

Avant de pouvoir générer une signature d'accès partagé en vue de télécharger des images vers un stockage d'objets blob, vous devez ajouter le package NuGet qui installe la bibliothèque du client de stockage dans le projet de service mobile.

1. Dans l'**Explorateur de solutions** de Visual Studio, cliquez avec le bouton droit sur le projet de service mobile, puis sélectionnez **Gérer les packages NuGet**.

2. Dans le volet gauche, sélectionnez la catégorie **En ligne**, sélectionnez **Stabile Only**, recherchez **WindowsAzure.Storage**, cliquez sur **Installer** dans le package **Azure Storage**, puis acceptez les contrats de licence.

  	![](./media/mobile-services-configure-blob-storage/mobile-add-storage-nuget-package-dotnet.png)

  	La bibliothèque cliente des services de stockage Azure est alors ajoutée au projet de service mobile.

##Mise à jour de la définition TodoItem dans le modèle de données

La classe TodoItem définit l'objet de données et vous devez ajouter à cette classe les propriétés ajoutées au client.

1. Dans Visual Studio 2013, ouvrez votre projet de service mobile, développez le dossier DataObjects, puis ouvrez le fichier projet TodoItem.cs.
	
2. Ajoutez les nouvelles propriétés suivantes à la classe **TodoItem** :

        public string containerName { get; set; }
		public string resourceName { get; set; }
		public string sasQueryString { get; set; }
		public string imageUri { get; set; } 

	Ces propriétés permettent de générer la signature d'accès partagé (SAP) et de stocker les informations de l'image. Notez que la casse de ces propriétés correspond à celle de la version du backend JavaScript.

	>[AZURE.NOTE]Lors de l’utilisation de l’initialiseur de base de données par défaut, Entity Framework supprime et recrée la base de données lorsqu’il détecte une modification du modèle de données dans la définition de Code First. Pour modifier ce modèle de données et conserver les données existantes dans la base de données, vous devez utiliser les migrations Code First. L'initialiseur par défaut ne peut pas être utilisé avec une base de données SQL dans Azure. Pour plus d'informations, consultez la rubrique [Utilisation des migrations Code First pour mettre à jour le modèle de données](../articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations.md).

##Mise à jour du contrôleur TodoItem pour générer une signature d’accès partagé 

La classe **TodoItemController** existante est mise à jour de manière à ce que la méthode **PostTodoItem** génère une signature d'accès partagé (SAP) lorsqu'un nouvel élément TodoItem est inséré. De plus

0. Si vous n'avez pas encore créé de compte de stockage, consultez la rubrique [Création d'un compte de stockage].

1. Dans le [portail Azure Classic](https://manage.windowsazure.com/), cliquez sur **Stockage**, sur le compte de stockage, puis sur **Gérer les clés**.

2. Notez le **nom du compte de stockage** et la **clé d'accès**.
 
3. Dans votre service mobile, cliquez sur l'onglet **Configurer**, faites défiler jusqu'à **Paramètres de l'application** et entrez une paire **Nom** et **Valeur** pour chacun des paramètres suivants obtenus à partir du compte de stockage, puis cliquez sur **Enregistrer**.

	+ `STORAGE_ACCOUNT_NAME`
	+ `STORAGE_ACCOUNT_ACCESS_KEY`

	![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-app-settings.png)

	La clé d'accès du compte de stockage est chiffrée et stockée dans les paramètres de l'application. Vous pouvez y accéder à partir du script serveur lors de l'exécution. Pour plus d'informations, consultez l'article [Paramètres de l'application].

4. Dans l'Explorateur de solutions de Visual Studio, ouvrez le fichier Web.config correspondant au projet de service mobile et ajoutez les nouveaux paramètres d'application suivants, en remplaçant les espaces réservés par le nom du compte de stockage et la clé d'accès définis sur le portail :

		<add key="STORAGE_ACCOUNT_NAME" value="**your_account_name**" />
		<add key="STORAGE_ACCOUNT_ACCESS_KEY" value="**your_access_token_secret**" />

	Le service mobile utilise ces paramètres stockés lorsqu'il est exécuté sur l'ordinateur local, vous permettant ainsi de tester le code avant de le publier. Exécuté dans Azure, le service mobile utilise les valeurs des paramètres de l'application définies dans le portail et ignore ces paramètres du projet.

7.  Dans l'Explorateur de solutions, ouvrez le fichier TodoItemController.cs et ajoutez l'instruction **using** suivante :

		using System;
		using Microsoft.WindowsAzure.Storage.Auth;
		using Microsoft.WindowsAzure.Storage.Blob;
  
8.  Remplacez la méthode **PostTodoItem** existante par le code suivant :

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            string storageAccountName;
            string storageAccountKey;

            // Try to get the Azure storage account token from app settings.  
            if (!(Services.Settings.TryGetValue("STORAGE_ACCOUNT_NAME", out storageAccountName) |
            Services.Settings.TryGetValue("STORAGE_ACCOUNT_ACCESS_KEY", out storageAccountKey)))
            {
                Services.Log.Error("Could not retrieve storage account settings.");
            }

            // Set the URI for the Blob Storage service.
            Uri blobEndpoint = new Uri(string.Format("https://{0}.blob.core.windows.net", storageAccountName));

            // Create the BLOB service client.
            CloudBlobClient blobClient = new CloudBlobClient(blobEndpoint, 
                new StorageCredentials(storageAccountName, storageAccountKey));

            if (item.containerName != null)
            {
                // Set the BLOB store container name on the item, which must be lowercase.
                item.containerName = item.containerName.ToLower();

                // Create a container, if it doesn't already exist.
                CloudBlobContainer container = blobClient.GetContainerReference(item.containerName);
                await container.CreateIfNotExistsAsync();

                // Create a shared access permission policy. 
                BlobContainerPermissions containerPermissions = new BlobContainerPermissions();

                // Enable anonymous read access to BLOBs.
                containerPermissions.PublicAccess = BlobContainerPublicAccessType.Blob;
                container.SetPermissions(containerPermissions);

                // Define a policy that gives write access to the container for 5 minutes.                                   
                SharedAccessBlobPolicy sasPolicy = new SharedAccessBlobPolicy()
                {
                    SharedAccessStartTime = DateTime.UtcNow,
                    SharedAccessExpiryTime = DateTime.UtcNow.AddMinutes(5),
                    Permissions = SharedAccessBlobPermissions.Write
                };

                // Get the SAS as a string.
                item.sasQueryString = container.GetSharedAccessSignature(sasPolicy); 

                // Set the URL used to store the image.
                item.imageUri = string.Format("{0}{1}/{2}", blobEndpoint.ToString(), 
                    item.containerName, item.resourceName);
            }

            // Complete the insert operation.
            TodoItem current = await InsertAsync(item);
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

   	Cette méthode POST génère une nouvelle SAP (valide 5 minutes) pour l'élément inséré et attribue la valeur de la SAP générée à la propriété `sasQueryString` de l'élément renvoyé. La propriété `imageUri` est aussi définie sur le chemin d'accès de la ressource du nouvel objet blob pour permettre l'affichage de l'image pendant la liaison dans l'interface utilisateur du client.

	>[AZURE.NOTE]Ce code crée une SAP pour un objet blob individuel. Si vous devez télécharger plusieurs objets blob sur un conteneur à l'aide de la même SAP, vous pouvez plutôt appeler la <a href="http://go.microsoft.com/fwlink/?LinkId=390455" target="_blank">méthode generateSharedAccessSignature</a> avec un nom de ressource d'objet blob vide, comme ceci : <pre><code>blobService.generateSharedAccessSignature(containerName, '', sharedAccessPolicy);</code></pre>

Ensuite, vous allez mettre à jour l'application de démarrage rapide pour ajouter la fonctionnalité de téléchargement d'image à l'aide de la SAP générée sur la fonction insert.
 
<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Création d'un compte de stockage]: ../articles/storage/storage-create-storage-account.md
[Paramètres de l'application]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7

<!---HONumber=AcomDC_1203_2015-->