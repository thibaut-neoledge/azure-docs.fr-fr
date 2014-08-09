<properties linkid="dev-nodejs-basic-web-app-with-storage" urlDisplayName="Web App with Storage" pageTitle="Web app with table storage (Node.js) | Microsoft Azure" metaKeywords="Azure Node.js hello world tutorial, Azure Node.js hello world, Azure Node.js Getting Started tutorial, Azure Node.js tutorial, Azure Node.js Express tutorial" description="A tutorial that builds on the Web App with Express tutorial by adding Azure Storage services and the Azure module." metaCanonical="" services="cloud-services,storage" documentationCenter="Node.js" title="Node.js Web Application using Storage" authors="" solutions="" manager="" editor="" />

Application Web Node.js utilisant le stockage
=============================================

Dans ce didacticiel, vous allez enrichir l'application créée dans le didacticiel [Application Web Node.js avec Express](http://www.windowsazure.com/fr-fr/develop/nodejs/tutorials/web-app-with-express/) à l'aide des bibliothèques clientes Azure pour Node.js afin qu'elle fonctionne avec les services de gestion de données. Vous allez étendre les fonctionnalités de votre application en vue de créer une application de liste de tâches Web que vous pouvez déployer sur Azure. La liste de tâches permet à un utilisateur d'extraire des tâches, d'en ajouter de nouvelles et de marquer celles qui sont terminées.

Les éléments de tâches sont stockés dans Azure Storage, qui offre le stockage de données non structurées à tolérance de panne et haute disponibilité. Azure Storage inclut plusieurs structures de données dans lesquelles vous pouvez stocker des données et y accéder. Vous pouvez également exploiter les services de stockage à partir des API incluses dans le Kit de développement logiciel (SDK) Azure pour Node.js ou via les API REST. Pour plus d'informations, consultez la page [Stockage et accessibilité des données dans Azure](http://msdn.microsoft.com/fr-fr/library/windowsazure/gg433040.aspx).

Ce didacticiel part du principe que vous avez suivi les didacticiels [Application Web Node.js](http://www.windowsazure.com/fr-fr/develop/nodejs/tutorials/getting-started/) et [Node.js avec Express](http://www.windowsazure.com/fr-fr/develop/nodejs/tutorials/web-app-with-express/).

Vous apprendrez à effectuer les opérations suivantes :

-   utiliser le moteur de modèles Jade ;
-   utiliser les services de gestion de données Azure.

Voici une capture d'écran de l'application terminée :

![Page Web terminée dans Internet Explorer](./media/storage-nodejs-use-table-storage-cloud-service-app/getting-started-1.png)

Définition des informations d'identification de stockage dans Web.Config
------------------------------------------------------------------------

Pour accéder à Azure Storage, vous devez transmettre les informations d'identification de stockage. Pour ce faire, vous utilisez les paramètres d'application web.config. Ceux-ci sont transmis en tant que variables d'environnement à Node, qui sont alors lues par le Kit de développement logiciel (SDK) Azure.

**Remarque**

Les informations d'identification de stockage sont uniquement utilisées lors du déploiement de l'application sur Azure. Lorsqu'elle est exécutée dans l'émulateur, l'application utilise l'émulateur de stockage.

Procédez comme suit pour extraire les informations d'identification de stockage et les ajouter aux paramètres web.config :

1.  Si ce n'est pas déjà le cas, ouvrez Azure PowerShell à partir du menu **Démarrer** en développant **Tous les programmes, Azure**, cliquez avec le bouton droit sur **Azure PowerShell**, puis sélectionnez **Exécuter en tant qu'administrateur**.

2.  Remplacez les répertoires par le dossier contenant votre application. Par exemple, C:\\node\\tasklist\\WebRole1.

3.  Dans la fenêtre Azure Powershell, entrez la cmdlet suivante pour extraire les informations du compte de stockage :

        PS C:\node\tasklist\WebRole1> Get-AzureStorageAccounts

    Cette action permet d'extraire la liste des comptes de stockage et les clés de compte associées à votre service hébergé.

    **Remarque**

    Dans la mesure où le Kit de développement logiciel (SDK) Azure crée un compte de stockage lorsque vous déployez un service, un compte de stockage doit déjà exister puisque vous avez déployé votre application dans les guides précédents.

4.  Ouvrez le fichier web.cloud.config contenant les paramètres d'environnement utilisés lorsque l'application est déployée sur Azure :

        PS C:\node\tasklist\WebRole1> notepad web.cloud.config

5.  Insérez le bloc suivant sous l'élément **configuration**, en remplaçant {STORAGE ACCOUNT} et {STORAGE ACCESS KEY} par le nom de compte et la clé primaire du compte de stockage que vous souhaitez utiliser pour le déploiement :

        <appSettings>
          <add key="AZURE_STORAGE_ACCOUNT" value="{STORAGE ACCOUNT}"/>
          <add key="AZURE_STORAGE_ACCESS_KEY" value="{STORAGE ACCESS KEY}"/>
        </appSettings>

    ![Contenu du fichier web.cloud.config](./media/storage-nodejs-use-table-storage-cloud-service-app/node37.png)

6.  Enregistrez le fichier et fermez le Bloc-notes.

Installation des modules
------------------------

Pour utiliser les services de gestion de données Azure, vous devez installer le module Azure pour Node, ainsi que le module node-uuid, car il doit servir à générer des identificateurs uniques universels. Pour ce faire, entrez la commande ci-dessous :

    PS C:\node\tasklist\WebRole1> npm install node-uuid azure

Avec cette commande, les modules sont ajoutés au dossier **node\_modules**. Pour utiliser ces modules dans votre application, procédez comme suit :

1.  Ouvrez le fichier server.js :

        PS C:\node\tasklist\WebRole1> notepad server.js

2.  Ajoutez le code ci-dessous après la ligne qui se termine par express.createServer() afin d'inclure les modules node-uuid, home et azure. Si le module home n'existe pas encore, vous allez rapidement le créer.

    ![Code server.js avec application de ligne de commande = ligne modules.exports mise en surbrillance](./media/storage-nodejs-use-table-storage-cloud-service-app/node38.png)

        var uuid = require('node-uuid');
        var Home = require('./home');
        var azure = require('azure');

3.  Ajoutez du code pour créer un client de table de stockage en transmettant les informations du compte de stockage et les informations de clé d'accès.

    **Remarque**

    Lorsqu'il est exécuté dans l'émulateur, le Kit de développement logiciel (SDK) utilise automatiquement l'émulateur même si les informations de compte de stockage ont été fournies via web.config.

        var client = azure.createTableService();

4.  Créez ensuite une table appelée tasks dans Azure Storage. La logique ci-dessous crée une table si elle n'existe pas, et la remplit avec des données par défaut.

        //création de table
        client.createTableIfNotExists('tasks', function(error){
            if(error){
                throw error;
            }

            var item = {
                name: 'Add readonly task list',
                category: 'Site work',
                date: '12/01/2011',
                RowKey: uuid(),
                PartitionKey: 'partition1',
                completed: false
            };

            client.insertEntity('tasks', item, function(){});

        });

5.  Remplacez le code existant dans la section d'itinéraire par le code ci-dessous, qui crée une instance de contrôleur home et y achemine toutes les requêtes vers **/** ou vers **/home**.

    ![Fichier server.js avec la section d'itinéraires mise en surbrillance](./media/storage-nodejs-use-table-storage-cloud-service-app/node39.png)

        var home = new Home(client);
        app.get('/', home.showItems.bind(home));
        app.get('/home', home.showItems.bind(home));

    Notez qu'au lieu de traiter la demande en ligne, vous déléguez désormais la commande à un objet Home. La commande **bind** est nécessaire pour garantir la résolution correcte de ces références en local dans le contrôleur home.

Création du contrôleur Home
---------------------------

Vous devez maintenant créer un contrôleur home, qui gère toutes les demandes pour le site de la liste de tâches. Procédez comme suit pour créer le contrôleur :

1.  Créez un fichier home.js dans le Bloc-notes. Ce fichier contient le code du contrôleur qui gère la logique de la liste de tâches.

        PS C:\node\tasklist\WebRole1> notepad home.js

2.  Remplacez son contenu par le code suivant, puis enregistrez le fichier. Le code ci-dessous utilise le modèle de module javascript. Il exporte une fonction Home. Le prototype Home contient les fonctions pour gérer les demandes réelles.

        var azure=require('azure');
        module.exports = Home;

        function Home (client) {
            this.client = client;
        };

        Home.prototype = {
            showItems: function (req, res) {
                var self = this;
                this.getItems(false, function (resp, tasklist) {
                    if (!tasklist) {
                        tasklist = [];
                    }           
                    self.showResults(res, tasklist);
                });
            },

            getItems: function (allItems, callback) {
                var query = azure.TableQuery
                    .select()
                    .from('tasks');
                
                if (!allItems) {
                    query = query.where('completed eq 
            ', 'false');
                }
                this.client.queryEntities(query, callback);
             },

             showResults: function (res, tasklist) {
                res.render('home', { 
                    title: 'Todo list', 
                    layout: false, 
                    tasklist: tasklist });
             },
        };

    Votre contrôleur home inclut désormais trois fonctions :

    -   *showItems* traite la demande.
    -   *getItems* utilise le client de table pour extraire des éléments de tâches ouvertes à partir de votre table tasks. Notez que des filtres supplémentaires peuvent être appliqués à la requête ; par exemple, les filtres de la requête ci-dessus affichent uniquement les tâches pour lesquelles le statut « complete » est défini sur false.
    -   *showResults* appelle la fonction de rendu Express afin de générer la page à l'aide de la vue Accueil que vous allez créer à la prochaine section.

### Modification de la vue Accueil

Le moteur de modèles Jade utilise une syntaxe de balisage qui est moins détaillée que HTML. Il est le moteur par défaut à utiliser avec Express. Procédez comme suit pour créer une vue qui prend en charge l'affichage des éléments de liste de tâches :

1.  Dans la fenêtre de commande Windows PowerShell, modifiez le fichier home.jade en utilisant la commande suivante :

        PS C:\node\tasklist\WebRole1\views> notepad home.jade

2.  Remplacez le contenu du fichier home.jade par le code suivant, puis enregistrez le fichier. Le formulaire ci-dessous contient une fonctionnalité permettant la lecture et la mise à jour des éléments de tâches. Notez qu'actuellement, le contrôleur Home prend uniquement en charge la lecture, vous modifierez ce paramètre ultérieurement. Ce formulaire contient les détails de chaque élément de la liste de tâches.

        html
        head
            title Index
        body
            h1 My ToDo List

            form
                table(border="1")
                    tr
                        td Name
                        td Category
                        td Date
                        td Complete

                        each item in tasklist
                            tr
                                td #{item.name}
                                td #{item.category} 
                                td #{item.date} 
                                td 
                                    input(type="checkbox", name="completed", value="#{item.RowKey}") 

Exécution de l'application dans l'émulateur de calcul
-----------------------------------------------------

1.  Dans la fenêtre Windows PowerShell, entrez la cmdlet suivante pour lancer votre service dans l'émulateur de calcul et afficher une page Web qui appelle votre service.

        PS C:\node\tasklist\WebRole1> Start-AzureEmulator -launch

    Votre navigateur affiche la page suivante, montrant l'élément de tâche qui a été extrait d'Azure Storage :

    ![Internet Explorer affichant une page My Tasklist avec un élément dans une table](./media/storage-nodejs-use-table-storage-cloud-service-app/node40.png)

Ajout d'une fonctionnalité Nouvelle tâche
-----------------------------------------

Dans cette section, vous mettez à jour l'application afin de prendre en charge l'ajout de nouveaux éléments de tâche.

### Ajout d'un nouvel itinéraire à Server.js

Dans le fichier server.js, ajoutez la ligne suivante après la dernière entrée d'itinéraire pour **/home**, puis enregistrez le fichier.

![Fichier server.js avec la ligne contenant l'itinéraire vers l'accueil mis en surbrillance](./media/storage-nodejs-use-table-storage-cloud-service-app/node41.png)

        app.post('/home/newitem', home.newItem.bind(home));

    La section des itinéraires doit désormais ressembler à ce qui suit :

       // Itinéraires

       var home = new Home(client);
       app.get('/', home.showItems.bind(home));
       app.get('/home', home.showItems.bind(home));
       app.post('/home/newitem', home.newItem.bind(home));

### Ajout du module Node-UUID

Pour créer un identifiant unique à l'aide du module node-uuid, ajoutez la ligne suivante au début du fichier home.js après la première ligne dans laquelle le module est exporté.

![Fichier home.js avec la ligne module.exports = Home mise en surbrillance.](./media/storage-nodejs-use-table-storage-cloud-service-app/node42.png)

       var uuid = require('node-uuid');

### Ajout de la fonction de nouvel élément au contrôleur Home

Pour mettre en œuvre la nouvelle fonctionnalité de l'élément, créez une fonction **newItem**. Dans le fichier home.js, copiez le code suivant après la dernière fonction, puis enregistrez le fichier.

![Fonction showresults mise en surbrillance](./media/storage-nodejs-use-table-storage-cloud-service-app/node43.png)

       newItem: function (req, res) {
           var self = this;
           var createItem = function (resp, tasklist) {
               if (!tasklist) {
                   tasklist = [];
               }

               var count = tasklist.length;

               var item = req.body.item;
               item.RowKey = uuid();
               item.PartitionKey = 'partition1';
               item.completed = false;

               self.client.insertEntity('tasks', item, function (error) {
                   if(error){  
                       throw error;
                   }
                   self.showItems(req, res);
               });
           };

           this.getItems(true, createItem);
       },

La fonction **newItem** effectue les tâches suivantes :

-   Extrait l'élément publié à partir du corps.
-   Définit les valeurs **RowKey** et **PartitionKey** pour le nouvel élément. Ces valeurs sont nécessaires pour insérer l'élément dans la table Azure. Un identificateur unique universel est généré pour la valeur **RowKey**.
-   Insère l'élément dans la table de tâches en appelant la fonction **insertEntity**.
-   Effectue le rendu de la page en appelant la fonction **getItems**.

### Ajout du nouveau formulaire d'élément à la vue Accueil

À présent, mettez à jour la vue en ajoutant un nouveau formulaire pour permettre à l'utilisateur d'ajouter un élément. Dans le fichier home.jade, copiez le code suivant à la fin du fichier, puis enregistrez-le.

**Remarque**

Dans Jade, les espaces blancs ont toute leur importance, il ne faut pas les enlever dans l'exemple ci-dessous.

        hr
        form(action="/home/newitem", method="post")
            table(border="1")    
                tr
                    td Item Name: 
                    td 
                        input(name="item[name]", type="textbox")
                tr
                    td Item Category: 
                    td 
                        input(name="item[category]", type="textbox")
                tr
                    td Item Date: 
                    td 
                        input(name="item[date]", type="textbox")
            input(type="submit", value="Add item")

### Exécution de l'application dans l'émulateur

1.  L'émulateur Azure est déjà en cours d'exécution, vous pouvez donc parcourir l'application mise à jour :

        PS C:\node\tasklist\WebRole1> start http://localhost:81/home

    Le navigateur s'ouvre et affiche la page suivante :

    ![Page Web intitulée My Task List avec une table contenant les tâches et les domaines pour ajouter une nouvelle tâche](./media/storage-nodejs-use-table-storage-cloud-service-app/node44.png)

2.  Entrez **Item Name:** "New task functionality". **Item Category:** "Site work"? et **Item Date:** "12/02/2011". Cliquez ensuite sur **Add item**.

    L'élément est ajouté à votre table tasks dans Azure Storage et est affiché comme indiqué dans la capture d'écran ci-dessous.

    ![Page Web intitulée My Task List avec une table contenant des tâches, une fois que vous avez ajouté une tâche à la liste](./media/storage-nodejs-use-table-storage-cloud-service-app/node45.png)

Nouvelle publication de l'application dans Azure
------------------------------------------------

À présent que l'application est terminée, publiez-la sur Azure en mettant à jour le déploiement vers le service hébergé existant.

1.  Dans la fenêtre Windows PowerShell, appelez la cmdlet suivante pour redéployer votre service hébergé vers Azure. Vos paramètres et l'emplacement de stockage ont déjà été enregistrés et n'ont pas besoin d'être entrés à nouveau.

        PS C:\node\tasklist\WebRole1> Publish-AzureServiceProject -name myuniquename -location datacentername -launch

    Une fois le déploiement terminé, une réponse similaire à celle présentée ci-dessous doit s'afficher :

    ![Messages d'état affichés au cours du déploiement](./media/storage-nodejs-use-table-storage-cloud-service-app/node35.png)

    Comme auparavant, dans la mesure où vous avez spécifié l'option **-launch**, le navigateur s'ouvre et affiche votre application qui s'exécute dans Azure une fois la publication effectuée.

    ![Fenêtre de navigateur affichant la page My Task List. L'URL indique que la page est désormais hébergée sur Azure.](./media/storage-nodejs-use-table-storage-cloud-service-app/node47.png)

Arrêt et suppression de votre application
-----------------------------------------

Après avoir déployé votre application, vous pouvez la désactiver afin d'éviter des coûts ou de générer et de déployer d'autres applications au cours de la période d'évaluation gratuite.

Azure facture les instances de rôle Web par heure de serveur consommée. Une fois votre application déployée, elle consomme du temps de serveur, même si les instances ne sont pas exécutées et sont arrêtées.

La procédure suivante présente l'arrêt et la suppression de l'application.

1.  Dans la fenêtre Windows PowerShell, arrêtez le déploiement du service créé dans la section précédente à l'aide de la cmdlet suivante :

        PS C:\node\tasklist\WebRole1> Stop-AzureService

    L'arrêt du service peut prendre plusieurs minutes. Une fois le service arrêté, vous recevez un message confirmant l'arrêt du service.

    ![Messages d'état indiquant l'arrêt du service](./media/storage-nodejs-use-table-storage-cloud-service-app/node48.png)

2.  Pour supprimer le service, utilisez la cmdlet suivante :

        PS C:\node\tasklist\WebRole1> Remove-AzureService contosotasklist

    Lorsque vous y êtes invité, entrez **Y** pour supprimer le service.

    La suppression du service peut prendre plusieurs minutes. Une fois le service supprimé, vous recevez un message confirmant la suppression du service.

    ![Messages d'état indiquant la suppression du service](./media/storage-nodejs-use-table-storage-cloud-service-app/node49.png)


