<properties linkid="dev-nodejs-enablesslworker" urlDisplayName="Enable SSL worker role" pageTitle="Configure SSL for a cloud service (Node.js) worker role" metaKeywords="Node.js Azure SSL, Node.js Azure, SSL worker role" description="" metaCanonical="" services="cloud-services" documentationCenter="Node.js" title="Configuring SSL for a Node.js Application in an Azure Worker Role" authors="" solutions="" manager="" editor="" />

Configuration de SSL pour une application Node.js dans un rôle de travail Azure
===============================================================================

Le chiffrement SSL (Secure Socket Layer) est la méthode de sécurisation la plus couramment utilisée pour envoyer des données sécurisées sur Internet. Cette tâche présente la spécification d'un point de terminaison HTTPS pour une application Node.js hébergée en tant que service cloud Azure dans un rôle de travail.

**Remarque**

Les étapes figurant dans cet article s'appliquent uniquement aux applications Node hébergées en tant que service cloud Azure dans un rôle de travail.

Cette procédure comprend les étapes suivantes :

-   [Étape 1 : création d'un service Node.js et publication de ce service dans le cloud](#step1)
-   [Étape 2 : obtention d'un certificat SSL](#step2)
-   [Étape 3 : modification de l'application pour utiliser le certificat SSL](#step3)
-   [Étape 4 : modification du fichier de définition de service](#step4)
-   [Étape 5 : connexion à l'instance de rôle à l'aide de HTTPS](#step5)

Étape 1 : création d'un service Node.js et publication de ce service dans le cloud
----------------------------------------------------------------------------------

Vous pouvez créer un simple service « hello world » Node.js en utilisant Azure PowerShell, en procédant ainsi :

1.  À partir du menu **Démarrer** ou de l'**écran d'accueil**, recherchez **Azure PowerShell**. Enfin, cliquez avec le bouton droit sur **Azure PowerShell** et sélectionnez **Exécuter en tant qu'administrateur**.

    ![Icône Azure PowerShell](./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/azure-powershell-start.png)

2.  Créez un service en utilisant la cmdlet **New-AzureServiceProject**.

    ![](./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-01.png)

3.  Ajoutez un rôle de travail à votre service en utilisant la cmdlet **Add-AzureNodeWorkerRole** :

    ![](./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-02-worker.png)

4.  Publiez vos services sur le cloud en utilisant la cmdlet **Publish-AzureServiceProject** :

    ![](./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-03-worker.png)

    **Remarque**

    Si vous n'avez pas déjà importé les paramètres de publication pour votre abonnement Azure, vous recevez une erreur lors de la publication. Pour plus d'informations sur le téléchargement et l'importation des paramètres de publication pour votre abonnement, consultez la page [Utilisation d'Azure PowerShell pour Node.js](https://www.windowsazure.com/en-us/develop/nodejs/how-to-guides/powershell-cmdlets/#ImportPubSettings)

La valeur **URL du site Web créé** renvoyée par la cmdlet **Publish-AzureServiceProject** contient le nom de domaine complet (FQDN) de votre application hébergée. Vous devrez obtenir un certificat SSL pour ce nom de domaine complet et le déployer sur Azure.

Étape 2 : obtention d'un certificat SSL
---------------------------------------

Pour configurer le chiffrement SSL pour une application, vous devez d'abord obtenir un certificat SSL signé par une autorité de certification, un tiers approuvé qui émet des certificats à cet effet. Si vous n'en possédez pas, vous devez en obtenir un auprès de la société qui vend des certificats SSL.

Le certificat SSL doit répondre aux prérequis suivants dans Azure :

-   Le certificat doit contenir une clé privée.
-   Il doit être créé pour un échange de clés (fichier **.pfx**).
-   Le nom d'objet du certificat doit correspondre au domaine servant à accéder au service cloud. Vous ne pouvez pas acquérir un certificat SSL pour le domaine cloudapp.net. Le nom d'objet du certificat doit donc correspondre au nom de domaine personnalisé utilisé pour accéder à votre application. Par exemple, **mysecuresite.cloudapp.net**.
-   Le certificat doit utiliser au minimum un chiffrement à 2048 bits.

Le fichier **.pfx** contenant le certificat est ajouté à votre projet de service et déployé dans Azure durant les étapes suivantes.

Étape 3 : modification de l'application pour utiliser le certificat SSL
-----------------------------------------------------------------------

Lorsqu'une application Node.js est déployée dans un rôle de travail, le certificat du serveur et la connexion SSL sont gérés par Node.exe. Pour gérer le trafic SSL, vous devez utiliser le module « https » au lieu du « http ». Procédez comme suit pour ajouter le certificat SSL à votre projet, puis modifiez l'application pour utiliser le certificat.

1.  Enregistrez le fichier **.pfx** fourni par votre autorité de certification dans le répertoire contenant votre application. Par exemple, **c:\\node\\securesite\\workerrole1** est le répertoire contenant l'application utilisée dans cet article.

2.  Ouvrez le fichier **c:\\node\\securesite\\workerrole1\\server.js** en utilisant le Bloc-notes, puis remplacez le contenu du fichier par le code suivant :

		var https = require('https');
		var fs = require('fs');

		var options = { 
            pfx: fs.readFileSync('certificate.pfx'),
            passphrase: "password" 
        };
        var port = process.env.PORT || 8000; 
        https.createServer(options, function (req, res) {
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            res.end('Hello World\\n'); 
        }).listen(port);

    <div class="dev-callout">
    <strong>Remarque :</strong>
    <p>vous devez remplacer « certificate.pfx » par le nom du fichier de certificat et « password » par le mot de passe (le cas échéant) du fichier de certificat.</p>
    </div>

1.  Enregistrez le fichier **server.js**.

Une fois le fichier **server.js** modifié, l'application écoute les communications du port 443 (port standard des communications SSL) lors de son déploiement vers Azure. Le fichier **.pfx** est utilisé pour implémenter les communications SSL sur ce transport.

Étape 4 : modification du fichier de définition de service
----------------------------------------------------------

Comme votre application écoute à présent le port 443, vous devez également modifier la définition du service pour autoriser les communications sur ce port.

1.  Dans le répertoire du service, ouvrez le fichier de définition du service (**ServiceDefinition.csdef**), mettez à jour l'élément http **InputEndpoint** dans la section **Points de terminaison** pour autoriser la communication sur le port 443 :

        <WorkerRole name="WorkerRole1" vmsize="Small">
        ...
            <Endpoints>
                <InputEndpoint name="HttpIn" protocol="tcp" 
                    port="443" />
            </Endpoints>
        ...
        </WorkerRole>

    Une fois ces modifications effectuées, enregistrez le fichier **ServiceDefinition.csdef**.

2.  Republiez votre service dans le cloud pour actualiser votre configuration mise à jour. À l'invite Azure PowerShell, tapez **Publish-AzureServiceProject** dans le répertoire du service.

Étape 5 : connexion à l'instance de rôle à l'aide de HTTPS
----------------------------------------------------------

Maintenant que votre déploiement est opérationnel dans Azure, vous pouvez vous y connecter via HTTPS.

1.  Dans le portail de gestion Azure, sélectionnez votre service cloud, puis cliquez sur **Tableau de bord**.

2.  Faites défiler l'écran vers le bas, puis cliquez sur le lien affiché en tant qu'**URL du site** :

    ![URL du site](./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/site-url.png)

    **Remarque**

    Si l'URL du site affichée dans le portail ne commence pas par HTTPS, vous devez alors l'entrer manuellement dans le navigateur en utilisant HTTPS au lieu de HTTP.

3.  Une nouvelle fenêtre de navigateur s'ouvre et affiche votre site Web.

    Votre navigateur affiche une icône de cadenas pour indiquer que la connexion HTTPS est active. Ceci indique également que votre application est correctement configurée pour SSL.

    ![](./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-08.png)

Ressources supplémentaires
--------------------------

[Association d'un certificat à un service](http://msdn.microsoft.com/fr-fr/library/windowsazure/gg465718.aspx)

[Configuration de SSL pour une application Node.js dans un rôle Web Azure](/en-us/develop/nodejs/common-tasks/enable-ssl/)

[Configuration d'un certificat SSL sur un point de terminaison HTTPS](http://msdn.microsoft.com/fr-fr/library/windowsazure/ff795779.aspx)

