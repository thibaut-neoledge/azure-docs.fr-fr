Un nouveau script pour la fonction insert est enregistré. Il génère une signature d'accès partagé (SAP) lorsqu'un nouvel élément Todo est inséré.

1.  Si vous n'avez pas encore créé de compte de stockage, consultez la rubrique [Création d'un compte de stockage](/en-us/manage/services/storage/how-to-create-a-storage-account).

2.  Dans le portail de gestion, cliquez sur **Stockage**, cliquez sur le compte de stockage, puis sur **Manage Keys**.

  	![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-account.png) 

1.  Notez le **nom du compte de stockage** et la **clé d'accès**.

   	![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-account-keys.png)

2.  Dans votre service mobile, cliquez sur l'onglet **Configurer**, faites défiler jusqu'à **Paramètres de l'application**, entrez une paire **Nom**/**Valeur** pour chacun des paramètres suivants obtenus à partir du compte de stockage, puis cliquez sur **Enregistrer**.
    
    * `STORAGE_ACCOUNT_NAME`
    * `STORAGE_ACCOUNT_ACCESS_KEY`
    
    ![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-app-settings.png)
    
    La clé d'accès du compte de stockage est chiffrée et stockée dans les paramètres de l'application. Vous pouvez y accéder à partir du script serveur lors de l'exécution. Pour plus d'informations, consultez la page [Paramètres de l'application][1].

3.  Cliquez sur l'onglet **Données**, puis sur la table **TodoItem**.    
        ![](./media/mobile-services-configure-blob-storage/mobile-portal-data-tables.png)

4.  Dans **todoitem**, cliquez sur l'onglet **Script**, sélectionnez **Insérer**, remplacez la fonction insert par le code suivant, puis cliquez sur **Enregistrer** :
    
        var azure = require(’azure’);
        var qs = require(’querystring’);
        var appSettings = require(’mobileservice-config’).appSettings;
        	
        function insert(item, user, request) {
            // Obtenez les paramètres du compte de stockage à partir des paramètres de l’application. 
            var accountName = appSettings.STORAGE_ACCOUNT_NAME;
            var accountKey = appSettings.STORAGE_ACCOUNT_ACCESS_KEY;
            var host = accountName + ’.blob.core.windows.net’;
        	
            if ((typeof item.containerName !== "undefined") && (
            item.containerName !== null)) {
                // Définissez sur l’élément le nom du conteneur de stockage d’objets BLOB, qui doit être en minuscule.
                item.containerName = item.containerName.toLowerCase();
        	
                // S’il n’existe pas, créez le conteneur 
                // avec un accès en lecture public pour les objets blob.        
                var blobService = azure.createBlobService(accountName, accountKey, host);
                blobService.createContainerIfNotExists(item.containerName, {
                    publicAccessLevel: ’blob’
                }, function(error) {
                    if (!error) {
        	
                        // Donnez accès en écriture au conteneur pendant les 5 prochaines minutes.        
                        var sharedAccessPolicy = {
                            AccessPolicy: {
                                Permissions: azure.Constants.BlobConstants.SharedAccessPermissions.WRITE,
                                Expiry: new Date(new Date().getTime() + 5 * 60 * 1000)
                            }
                        };
        	
                        // Générez l’URL de téléchargement avec SAS pour la nouvelle image.
                        var sasQueryUrl = 
                        blobService.generateSharedAccessSignature(item.containerName, 
                        item.resourceName, sharedAccessPolicy);
        	
                        // Définissez la chaîne de requête.
                        item.sasQueryString = qs.stringify(sasQueryUrl.queryString);
        	
                        // Définissez le chemin d’accès complet sur nouvel élément, 
                        // qui est utilisé pour la liaison de données sur le client. 
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

	![](./media/mobile-services-configure-blob-storage/mobile-insert-script-blob.png)
	 
	Cela remplace la fonction appelée lors d’une insertion dans la table TodoItem avec le nouveau script. Ce nouveau script génère une nouvelle SAP pour la fonction insert, valide pendant 5 minutes, et attribue la valeur de la SAP générée à la propriété `sasQueryString` de l’élément renvoyé. La propriété `imageUri` est aussi définie sur le chemin d’accès à la ressource de nouvel objet blob pour permettre l’affichage de l’image lors de la liaison dans l’interface utilisateur du client.     

	>[WACOM.NOTE] Ce code créé une SAP pour un objet blob individuel. Si vous devez télécharger plusieurs objets blob sur un conteneur à l’aide de la même SAP, vous pouvez plutôt appeler la <a href="http://go.microsoft.com/fwlink/?LinkId=390455" target="_blank">méthode generateSharedAccessSignature</a> avec un nom de la ressource de l’objet blob vide, comme ceci : 
    <pre><code>blobService.generateSharedAccessSignature(containerName, ’’, sharedAccessPolicy);</code></pre>

Ensuite, vous allez mettre à jour l'application de démarrage rapide pour ajouter la fonctionnalité de téléchargement d'image à l'aide de la SAP générée sur la fonction insert.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->



[1]: http://msdn.microsoft.com/fr-fr/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7