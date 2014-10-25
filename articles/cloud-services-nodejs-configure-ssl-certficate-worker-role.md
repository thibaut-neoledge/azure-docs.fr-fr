<properties linkid="dev-nodejs-enablesslworker" urlDisplayName="Enable SSL worker role" pageTitle="Configure SSL for a cloud service (Node.js) worker role" metaKeywords="Node.js Azure SSL, Node.js Azure, SSL worker role" description="" metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Configuring SSL for a Node.js Application in an Azure Worker Role" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />





# Configuration de SSL pour une application Node.js dans un rôle de travail Azure

Le chiffrement SSL (Secure Socket Layer) est la méthode de sécurisation la
plus couramment utilisée pour envoyer des données sécurisées sur Internet. Cette tâche présente la spécification
d'un point de terminaison HTTPS pour une application Node.js hébergée en tant que service cloud Azure dans un rôle de travail.

<div class="dev-callout">
	<b>Remarque</b>
	<p>Les &eacute;tapes figurant dans cet article s'appliquent uniquement aux applications Node h&eacute;berg&eacute;es en tant que service cloud Azure dans un r&ocirc;le de travail.</p>
</div>

Cette procédure comprend les étapes suivantes :

-   [Étape 1 : création d'un service Node.js et publication de ce service dans le cloud]
-   [Étape 2 : obtention d'un certificat SSL]
-   [Étape 3 : modification de l'application pour utiliser le certificat SSL]
-   [Étape 4 : modification du fichier de définition de service]
-   [Étape 5 : connexion à l'instance de rôle à l'aide de HTTPS]

## <a name="step1"> </a>Étape 1 : création d'un service Node.js et publication de ce service dans le cloud

Vous pouvez créer un simple service
« hello world » Node.js en utilisant Azure PowerShell, en procédant ainsi :

1.  À partir du menu **Démarrer** ou de l'**écran d'accueil**, recherchez **Azure PowerShell**. Enfin, cliquez avec le bouton droit sur **Azure PowerShell** et sélectionnez **Exécuter en tant qu'administrateur**.

    ![Icône Azure PowerShell][]

2.  Créez un service en utilisant la cmdlet **New-AzureServiceProject**.

    ![][1]

3.  Ajoutez un rôle de travail à votre service en utilisant la cmdlet **Add-AzureNodeWorkerRole** :

    ![][2]

4.  Publiez vos services sur le cloud en utilisant la cmdlet **Publish-AzureServiceProject** :

    ![][3]

    <div class="dev-callout">
<strong>Remarque</strong>
<p>Si vous n'avez pas d&eacute;j&agrave; import&eacute; les param&egrave;tres de publication pour votre abonnement Azure, vous recevez une erreur lors de la publication. Pour plus d'informations sur le t&eacute;l&eacute;chargement et l'importation des param&egrave;tres de publication pour votre abonnement, consultez la page <a href="https://www.windowsazure.com/fr-fr/develop/nodejs/how-to-guides/powershell-cmdlets/#ImportPubSettings">Utilisation d'Azure PowerShell pour Node.js</a></p>
</div>

La valeur **URL du site web créé** renvoyée par la cmdlet **Publish-AzureServiceProject** contient le nom de domaine complet (FQDN) de votre application hébergée. Vous devrez obtenir un certificat SSL pour ce nom de domaine complet et le déployer sur Azure.

## <a name="step2"> </a>Étape 2 : obtention d'un certificat SSL

Pour configurer le chiffrement SSL pour une application, vous
devez d'abord obtenir un certificat SSL signé par une autorité de
certification, un tiers approuvé qui émet des certificats à cet effet. Si vous
n'en possédez pas, vous devez en obtenir un auprès de la société qui
vend des certificats SSL.

Le certificat SSL doit répondre aux prérequis suivants dans
Azure :

-   Le certificat doit contenir une clé privée.
-   Il doit être créé pour un échange de clés (fichier **.pfx**).
-   Le nom d'objet du certificat doit correspondre au domaine servant à
    accéder au service cloud. Vous ne pouvez pas acquérir un certificat SSL
    pour le domaine cloudapp.net. Le nom d'objet du certificat doit
    donc correspondre au nom de domaine personnalisé utilisé pour accéder à votre application. Par exemple, **mysecuresite.cloudapp.net**.
-   Le certificat doit utiliser au minimum un chiffrement à 2048 bits.

Le fichier **.pfx** contenant le certificat est ajouté à votre projet de service et déployé dans Azure durant les étapes suivantes.

## <a name="step3"> </a>Étape 3 : modification de l'application pour utiliser le certificat SSL

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
            res.end('Hello World\n');
        }).listen(port);

    <div class="dev-callout">
<strong>Remarque</strong>
<p>Vous devez remplacer &laquo;&nbsp;certificate.pfx&nbsp;&raquo; par le nom du fichier de certificat et &laquo;&nbsp;password&nbsp;&raquo; par le mot de passe (le cas &eacute;ch&eacute;ant) du fichier de certificat.</p>
</div>

3.  Enregistrez le fichier **server.js**.

Une fois le fichier **server.js** modifié, l'application écoute les communications du port 443 (port standard des communications SSL) lors de son déploiement vers Azure. Le fichier **.pfx** est utilisé pour implémenter les communications SSL sur ce transport.

## <a name="step4"> </a>Étape 4 : modification du fichier de définition de service

Comme votre application écoute à présent le port 443, vous devez également modifier la définition du service pour autoriser les communications sur ce port.

1.  Dans le répertoire du service, ouvrez le fichier de définition
    du service (**ServiceDefinition.csdef**), mettez à jour l'élément http **InputEndpoint** dans la section **Endpoints** pour autoriser la communication sur le port 443 :

        <WorkerRole name="WorkerRole1" vmsize="Small">
        ...
            <Endpoints>
                <InputEndpoint name="HttpIn" protocol="tcp" 
                    port="443" />
            </Endpoints>
        ...
        </WorkerRole>

    Une fois ces modifications effectuées, enregistrez le fichier **ServiceDefinition.csdef**.

2.  Republiez votre service dans le cloud pour actualiser votre
    configuration mise à jour. À l'invite Azure PowerShell,
    tapez **Publish-AzureServiceProject** dans le répertoire du service.

## <a name="step5"> </a>Étape 5 : connexion à l'instance de rôle à l'aide de HTTPS

Maintenant que votre déploiement est opérationnel dans Azure,
vous pouvez vous y connecter via HTTPS.

1.  Dans le portail de gestion Azure, sélectionnez votre service cloud, puis cliquez sur **Tableau de bord**.

2.  Faites défiler l'écran vers le bas, puis cliquez sur le lien affiché en tant qu'**URL du site** :

    ![URL du site][]

    <div class="dev-callout">
<strong>Remarque</strong>
<p>Si l'URL du site affich&eacute;e dans le portail ne commence pas par HTTPS, vous devez alors l'entrer manuellement dans le navigateur en utilisant HTTPS au lieu de HTTP.</p>
</div>

3.  Une nouvelle fenêtre de navigateur s'ouvre et affiche votre site web.

    Votre navigateur affiche une icône de cadenas pour
    indiquer que la connexion HTTPS est active. Ceci indique également que votre
    application est correctement configurée pour SSL.

    ![][8]

## Ressources supplémentaires

[Association d'un certificat à un service]

[Configuration de SSL pour une application Node.js dans un rôle web Azure]

[Configuration d'un certificat SSL sur un point de terminaison HTTPS]

  [Étape 1 : création d'un service Node.js et publication de ce service dans le cloud]: #step1
  [Étape 2 : obtention d'un certificat SSL]: #step2
  [Étape 3 : modification de l'application pour utiliser le certificat SSL]: #step3
  [Étape 4 : modification du fichier de définition de service]: #step4
  [Étape 5 : connexion à l'instance de rôle à l'aide de HTTPS]: #step5
  [**Icône Azure PowerShell**]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/azure-powershell-start.png
  
  
  
  
  [1]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-01.png
  [2]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-02-worker.png
  [3]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-03-worker.png
  
  
  [Utilisation d'Azure PowerShell pour Node.js]: https://www.windowsazure.com/fr-fr/develop/nodejs/how-to-guides/powershell-cmdlets/#ImportPubSettings
  
  [URL du site]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/site-url.png
  [8]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-08.png
  [Association d'un certificat à un service]: http://msdn.microsoft.com/fr-fr/library/windowsazure/gg465718.aspx
  [Configuration de SSL pour une application Node.js dans un rôle web Azure]: /fr-fr/develop/nodejs/common-tasks/enable-ssl/
  [Configuration d'un certificat SSL sur un point de terminaison HTTPS]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ff795779.aspx
