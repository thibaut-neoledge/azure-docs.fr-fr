### <a name="create-a-nodejs-application"></a>Création d’une application Node.js
* Créez un fichier JavaScript appelé `listener.js`.

### <a name="add-the-relay-npm-package"></a>Ajout du package NPM de relais
* Exécutez `npm install hyco-ws` à partir d’une invite de commandes de nœud dans votre dossier de projet.

### <a name="write-some-code-to-receive-messages"></a>Écriture de code pour recevoir des messages
1. Ajoutez le `constant` suivant au début du fichier `listener.js`.
   
    ```js
    const WebSocket = require('hyco-ws');
    ```
2. Ajoutez le relais suivant `constants` à `listener.js` pour les détails de la connexion hybride. Remplacez les espaces réservés entre crochets avec les valeurs appropriées obtenues lors de la création de la connexion hybride.
   
   1. `const ns` - L’espace de noms de relais (utiliser le nom de domaine complet, par exemple `{namespace}.servicebus.windows.net`)
   2. `const path` - Le nom de la connexion hybride
   3. `const keyrule` - Le nom de la clé SAS
   4. `const key` - La valeur de la clé SAS
3. Ajoutez le code suivant au fichier `listener.js` :
   
    ```js
    var wss = WebSocket.createRelayedServer(
    {
        server : WebSocket.createRelayListenUri(ns, path),
        token: WebSocket.createRelayToken('http://' + ns, keyrule, key)
    }, 
    function (ws) {
        console.log('connection accepted');
        ws.onmessage = function (event) {
            console.log(event.data);
        };
        ws.on('close', function () {
            console.log('connection closed');
        });       
    });
   
    console.log('listening');
   
    wss.on('error', function(err) {
        console.log('error' + err);
    });
    ```
    Voici à quoi doit ressembler votre fichier listener.js :
   
    ```js
    const WebSocket = require('hyco-ws');
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    var wss = WebSocket.createRelayedServer(
        {
            server : WebSocket.createRelayListenUri(ns, path),
            token: WebSocket.createRelayToken('http://' + ns, keyrule, key)
        }, 
        function (ws) {
            console.log('connection accepted');
            ws.onmessage = function (event) {
                console.log(event.data);
            };
            ws.on('close', function () {
                console.log('connection closed');
            });       
    });
   
    console.log('listening');
   
    wss.on('error', function(err) {
        console.log('error' + err);
    });
    ```

