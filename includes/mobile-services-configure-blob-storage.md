Un nouveau script pour la fonction insert est enregistré. Il génère une signature d’accès partagé (SAP) lorsqu’un nouvel élément Todo est inséré.

0. Si vous n’avez pas encore créé de compte de stockage, consultez la rubrique [Création d’un compte de stockage](../storage/storage-create-storage-account.md).

1. Dans le portail de gestion, cliquez sur **Stockage**, sur le compte de stockage, puis sur **Gérer les clés**.

2. Notez le **nom du compte de stockage** et la **clé d'accès**.

   	![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-account-keys.png)

3. Dans votre service mobile, cliquez sur l'onglet **Configurer**, faites défiler jusqu'à **Paramètres de l'application** et entrez une paire **Nom** et **Valeur** pour chacun des paramètres suivants obtenus à partir du compte de stockage, puis cliquez sur **Enregistrer**.

	+ `STORAGE_ACCOUNT_NAME`
	+ `STORAGE_ACCOUNT_ACCESS_KEY`

	![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-app-settings.png)

	La clé d'accès du compte de stockage est chiffrée et stockée dans les paramètres de l'application. Vous pouvez y accéder à partir du script serveur lors de l'exécution. Pour plus d'informations, consultez l'article [Paramètres de l'application].

4. Dans l’onglet Configurer, assurez-vous que l’option [Schéma dynamique](http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7) est activée. Le schéma dynamique doit être activé pour pouvoir ajouter de nouvelles colonnes au tableau TodoItem. Le schéma dynamique ne doit pas être activé dans n’importe quel service de production.

4. Cliquez sur l’onglet **Données**, puis sur la table **TodoItem**.

5.  Dans **todoitem**, cliquez sur l’onglet **Script**, sélectionnez **Insérer**, remplacez la fonction insert par le code suivant, puis cliquez sur **Enregistrer** :

		var azure = require('azure');
		var qs = require('querystring');
		var appSettings = require('mobileservice-config').appSettings;
		
		function insert(item, user, request) {
		    // Get storage account settings from app settings. 
		    var accountName = appSettings.STORAGE_ACCOUNT_NAME;
		    var accountKey = appSettings.STORAGE_ACCOUNT_ACCESS_KEY;
		    var host = accountName + '.blob.core.windows.net';
		
		    if ((typeof item.containerName !== "undefined") && (
		    item.containerName !== null)) {
		        // Set the BLOB store container name on the item, which must be lowercase.
		        item.containerName = item.containerName.toLowerCase();
		
		        // If it does not already exist, create the container 
		        // with public read access for blobs.        
		        var blobService = azure.createBlobService(accountName, accountKey, host);
		        blobService.createContainerIfNotExists(item.containerName, {
		            publicAccessLevel: 'blob'
		        }, function(error) {
		            if (!error) {
		
		                // Provide write access to the container for the next 5 mins.        
		                var sharedAccessPolicy = {
		                    AccessPolicy: {
		                        Permissions: azure.Constants.BlobConstants.SharedAccessPermissions.WRITE,
		                        Expiry: new Date(new Date().getTime() + 5 * 60 * 1000)
		                    }
		                };
		
		                // Generate the upload URL with SAS for the new image.
		                var sasQueryUrl = 
		                blobService.generateSharedAccessSignature(item.containerName, 
		                item.resourceName, sharedAccessPolicy);
		
		                // Set the query string.
		                item.sasQueryString = qs.stringify(sasQueryUrl.queryString);
		
		                // Set the full path on the new new item, 
		                // which is used for data binding on the client. 
		                item.imageUri = sasQueryUrl.baseUrl + sasQueryUrl.path;
		
		            } else {
		                console.error(error);
		            }
		            request.execute();
		        });
		    } else {
		        request.execute();
		    }
		}

   	Cela remplace la fonction appelée lors d'une insertion dans la table TodoItem avec le nouveau script. Ce nouveau script génère une nouvelle SAP pour l’insertion, qui est valide 5 minutes, et affecte la valeur de la SAP générée à la propriété `sasQueryString` de l’élément renvoyé. La propriété `imageUri` est également définie sur le chemin d'accès de la ressource du nouvel objet blob pour permettre l'affichage de l'image lors de la liaison dans l'interface utilisateur du client.

	>[AZURE.NOTE]Ce code crée une SAP pour un objet blob individuel. Si vous devez télécharger plusieurs objets blob vers un conteneur à l’aide de la même SAP, vous pouvez plutôt appeler la [méthode generateSharedAccessSignature](http://go.microsoft.com/fwlink/?LinkId=390455) </a>avec un nom de ressource d’objet blob vide, comme ceci :
	>                 
	>     blobService.generateSharedAccessSignature(containerName, '', sharedAccessPolicy);

Ensuite, vous allez mettre à jour l'application de démarrage rapide pour ajouter la fonctionnalité de téléchargement d'image à l'aide de la SAP générée sur la fonction insert.
 
<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Paramètres de l'application]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7

<!---HONumber=July15_HO5-->