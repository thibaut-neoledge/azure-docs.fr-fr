
La classe **TodoItemController** est mise à jour de manière à ce que la méthode **PostTodoItem** génère une signature d'accès partagé (SAP) lorsqu'un nouvel élément Todo est inséré.

1.  Si vous n'avez pas encore créé de compte de stockage, consultez la rubrique [Création d'un compte de stockage](/en-us/manage/services/storage/how-to-create-a-storage-account).

2.  Dans le portail de gestion, cliquez sur **Stockage**, sur le compte de stockage, puis sur **Gérer les clés**.

	![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-account.png)

1.  Notez le **nom du compte de stockage** et la **clé d'accès**.

	![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-account-keys.png)

2.  Dans votre service mobile, cliquez sur l'onglet **Configurer**, faites défiler jusqu'à **Paramètres de l'application** et entrez une paire **Nom** et **Valeur** pour chacun des paramètres suivants obtenus à partir du compte de stockage, puis cliquez sur **Enregistrer**.

    -   `STORAGE_ACCOUNT_NAME`
    -   `STORAGE_ACCOUNT_ACCESS_KEY`

    ![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-app-settings.png)

    La clé d'accès du compte de stockage est chiffrée et stockée dans les paramètres de l'application. Vous pouvez y accéder à partir du script serveur lors de l'exécution. Pour plus d'informations, consultez l'article [Paramètres de l'application](http://msdn.microsoft.com/en-us/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7).

3.  Dans l'Explorateur de solutions de Visual Studio, ouvrez le fichier Web.config correspondant au projet de service mobile et ajoutez les nouveaux paramètres de l'application suivants, en remplaçant les espaces réservés par le nom du compte de stockage et la clé d'accès définis comme paramètres de l'application dans le portail :

         <add key="STORAGE_ACCOUNT_NAME" value="**your_account_name**" />
         <add key="STORAGE_ACCOUNT_ACCESS_KEY" value="**your_access_token_secret**" />

    Le service mobile utilise ces paramètres stockés lorsqu'il est exécuté sur l'ordinateur local, vous permettant ainsi de tester le code avant de le publier. Exécuté dans Azure, le service mobile utilise les valeurs des paramètres de l'application définies dans le portail et ignore ces paramètres du projet.

4.  Dans l'Explorateur de solutions, ouvrez le fichier TodoItemController.cs et ajoutez l'instruction **using** suivante :

        using System;
        using Microsoft.WindowsAzure.Storage.Auth;
        using Microsoft.WindowsAzure.Storage.Blob;

5.  Remplacez la méthode **PostTodoItem** existante par le code suivant :

        var azure = require('azure');
            var qs = require('querystring');
            var appSettings = require('mobileservice-config').appSettings;
                
            function insert(item, user, request) {
                // Obtenir les paramètres du compte de stockage à partir des paramètres de l'application. 
            var accountName = appSettings.STORAGE_ACCOUNT_NAME;
                var accountKey = appSettings.STORAGE_ACCOUNT_ACCESS_KEY;
                var host = accountName + '.blob.core.windows.net';
                
                if ((typeof item.containerName !== "undefined") && (
                item.containerName !== null)) {
                    // Définir le nom du conteneur de stockage d'objets blob sur l'élément, qui doit être en minuscules.
                item.containerName = item.containerName.toLowerCase();
                
                    // S'il n'existe pas, créer le conteneur 
                    // avec un accès en lecture public pour les objets blob.        
                var blobService = azure.createBlobService(accountName, accountKey, host);
                    blobService.createContainerIfNotExists(item.containerName, {
                        publicAccessLevel: 'blob'
                    }, function(error) {
                        if (!error) {
                
                            // Donner accès en écriture au conteneur pendant les 5 prochaines minutes.        
                        var sharedAccessPolicy = {
                                AccessPolicy: {
                                    Permissions: azure.Constants.BlobConstants.SharedAccessPermissions.WRITE,
                                    Expiry: new Date(new Date().getTime() + 5 * 60 * 1000)
                                }
                            };
                
                            // Générer l'URL de téléchargement avec SAP pour la nouvelle image.
                        var sasQueryUrl = 
                            blobService.generateSharedAccessSignature(item.containerName, 
                            item.resourceName, sharedAccessPolicy);
                
                            // Définir la chaîne de requête.
                        item.sasQueryString = qs.stringify(sasQueryUrl.queryString);
                
                            // Définir le chemin d'accès complet sur nouvel élément, 
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

	Cela remplace la fonction appelée lors d'une insertion dans la table TodoItem avec le nouveau script. Ce nouveau script génère une nouvelle SAP pour la fonction insert, valide pendant 5 minutes, et attribue la valeur de la SAP générée à la propriété `sasQueryString` de l'élément renvoyé. La propriété `imageUri` est aussi définie sur le chemin d'accès de la ressource du nouvel objet blob pour permettre l'affichage de l'image lors de la liaison dans l'interface utilisateur du client.

    >[WACOM.NOTE] Ce code créé une SAP pour un objet blob individuel. Si vous devez télécharger plusieurs objets blob sur un conteneur à l'aide de la même SAP, vous pouvez plutôt appeler la <a href="http://go.microsoft.com/fwlink/
    LinkId=390455" target="_blank">méthode generateSharedAccessSignature</a> avec un nom de ressource d'objet blob vide, comme ceci : 
    <pre><code>blobService.generateSharedAccessSignature(containerName, '', sharedAccessPolicy);</code></pre>

Ensuite, vous allez mettre à jour l'application de démarrage rapide pour ajouter la fonctionnalité de téléchargement d'image à l'aide de la SAP générée sur la fonction insert.

