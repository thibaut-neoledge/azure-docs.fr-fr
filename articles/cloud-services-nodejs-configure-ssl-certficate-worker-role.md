<properties 
	pageTitle="Configuration de SSL pour un rôle de travail de service cloud (Node.js)" 
	description="" 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="wpickett"/>





# Configuration de SSL pour une application Node.js dans un rôle de travail Azure

Le chiffrement Secure Socket Layer (SSL) est la méthode la plus couramment utilisée
pour sécuriser des données envoyées via Internet. Cette tâche explique comment
spécifier un point de terminaison HTTPS pour une application Node.js hébergée en tant qu'Azure Cloud Service dans un rôle de travail.

> [AZURE.NOTE] Les étapes figurant dans cet article s'appliquent uniquement aux applications Node hébergées en tant que service cloud Azure dans un rôle de travail.

Cette procédure comprend les étapes suivantes :

-   [Étape 1 : création d'un service Node.js et publication de ce service dans le cloud]
-   [Étape 2 : Obtention d'un certificat SSL]
-   [Étape 3 : modification de l'application pour utiliser le certificat SSL]
-   [Étape 4 : modification du fichier de définition de service]
-   [Étape 5 : connexion à l'instance de rôle à l'aide de HTTPS]

## <a name="step1"> </a>Étape 1 : création d'un service Node.js et publication de ce service dans le cloud

Vous pouvez créer un simple service Node.js 'hello
world' à l'aide d'Azure PowerShell en procédant comme suit :

1. À partir du menu **Démarrer** ou de l'**écran d'accueil**, recherchez **Azure PowerShell**. Enfin, cliquez avec le bouton droit sur **Azure PowerShell** et sélectionnez **Exécuter en tant qu'administrateur**.

	![Azure PowerShell icon][powershell-menu]

	

2.  Créez un nouveau service en utilisant l'applet de commande **New-AzureServiceProject**.

	![][1]

3.  Ajoutez un rôle de travail à votre service en utilisant l'applet de commande **Add-AzureNodeWorkerRole** :

    ![][2]

4.  Publiez vos services sur le cloud en utilisant l'applet de commande **Publish-AzureServiceProject** :

    ![][3]

	> [AZURE.NOTE] Si vous n'avez pas déjà importé les paramètres de publication pour votre abonnement Azure, vous recevez une erreur lors de la publication. Pour plus d'informations sur le téléchargement et l'importation des paramètres de publication pour votre abonnement, consultez la page [Utilisation d'Azure PowerShell pour Node.js](https://www.windowsazure.com/fr-fr/develop/nodejs/how-to-guides/powershell-cmdlets/#ImportPubSettings)

La valeur **URL du site Web créé** renvoyée par l'applet de commande **Publish-AzureServiceProject** contient le nom de domaine complet (FQDN) de votre application hébergée. Vous devrez obtenir un certificat SSL pour ce nom de domaine complet et le déployer sur Azure.

## <a name="step2"> </a>Étape 2 : obtention d'un certificat SSL

Pour configurer SSL pour une application, vous devez d'abord obtenir un certificat SSL
qui a été signé par une autorité de certification (CA), un
tiers approuvé qui émet des certificats à cet effet. Si vous n'en
possédez pas déjà un, vous devez en obtenir un auprès d'une société qui
vend des certificats SSL.

Le certificat doit répondre aux exigences suivantes relatives aux
certificats SSL dans Azure :

-   Le certificat doit contenir une clé privée.
-   Il doit être créé pour un échange de clés (fichier **.pfx**).
-   Le nom d'objet du certificat doit correspondre au domaine utilisé pour accéder au
    service cloud. Vous ne pouvez pas acquérir un certificat SSL pour le
    domaine cloudapp.net, donc le nom du sujet du certificat doit correspondre au
    nom de domaine personnalisé utilisé pour accéder à votre application. Par exemple, __mysecuresite.cloudapp.net__.
-   Le certificat doit utiliser au minimum un chiffrement à 2048 bits.

Le fichier **.pfx** contenant le certificat est ajouté à votre projet de service et déployé dans Azure durant les étapes suivantes.

## <a name="step3"> </a>Étape 3 : modification de l'application pour utiliser le certificat SSL

Lorsqu'une application Node.js est déployée sur un rôle de travail, le certificat de serveur et de la connexion SSL sont gérés par Node.exe. Pour gérer le trafic SSL, vous devez utiliser le module  'https' au lieu de  'http'. Procédez comme suit pour ajouter le certificat SSL à votre projet, puis modifiez l'application pour utiliser le certificat.

1.   Enregistrez le fichier **.pfx** fourni par votre autorité de certification dans le répertoire contenant votre application. Par exemple, **c:\\node\\securesite\\workerrole1** est le répertoire contenant l'application utilisée dans cet article.

2.   Ouvrez le fichier **c:\\node\\securesite\\workerrole1\server.js** en utilisant le Bloc-notes, puis remplacez le contenu du fichier par le code suivant :

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

	> [AZURE.IMPORTANT] vous devez remplacer  'certificate.pfx' par le nom du fichier de certificat et " password " par le mot de passe (le cas échéant) du fichier de certificat.

3.   Enregistrez le fichier **server.js**.

Une fois le fichier **server.js** modifié, l'application écoute les communications du port 443 (port standard des communications SSL) lors de son déploiement vers Azure. Le fichier **.pfx** sera utilisé pour implémenter les communications SSL sur ce transport.

## <a name="step4"> </a>Étape 4 : modification du fichier de définition de service

Comme votre application écoute à présent le port 443, vous devez également modifier la définition du service pour autoriser les communications sur ce port.

1.  Dans le service d'annuaire, ouvrez le fichier de définition de service
    (**ServiceDefinition.csdef**) et mettez à jour l'élément http **InputEndpoint** dans la section **Points de terminaison** pour activer la communication sur le port 443 :

        <WorkerRole name="WorkerRole1" vmsize="Small">
        ...
            <Endpoints>
                <InputEndpoint name="HttpIn" protocol="tcp" 
                    port="443" />
            </Endpoints>
        ...
        </WorkerRole>

	Une fois ces modifications effectuées, enregistrez le fichier **ServiceDefinition.csdef**.

4.  Pour actualiser votre configuration mise à jour dans le cloud, publiez
    à nouveau votre service. Au moment de l'invite Azure PowerShell,
    saisissez **Publish-AzureServiceProject** à partir du répertoire de service.

## <a name="step5"> </a>Étape 5 : connexion à l'instance de rôle à l'aide de HTTPS

Maintenant que votre déploiement est opérationnel dans Azure, vous pouvez
vous y connecter via HTTPS.

1.  Dans le portail de gestion, sélectionnez votre service cloud, puis cliquez sur **Tableau de bord**.

2. Faites défiler l'écran vers le bas, puis cliquez sur le lien affiché en tant qu'**URL du site** :

    ![the site url][site-url]

	> [AZURE.IMPORTANT] Si l'URL du site affichée dans le portail ne commence pas par HTTPS, vous devez alors l'entrer manuellement dans le navigateur en utilisant HTTPS au lieu de HTTP.

3.  Une nouvelle fenêtre de navigateur s'ouvre et affiche votre site Web.

    Votre navigateur affiche une icône de verrou pour indiquer qu'il
    utilise une connexion HTTPS. Cela indique également que votre application
    a été correctement configurée pour SSL.

    ![][8]

## Ressources supplémentaires

[Comment associer un certificat à un Service]

[Configuration de SSL pour une Application Node.js dans un rôle Web Azure]

[Comment configurer un certificat SSL sur un point de terminaison HTTPS]

  [Étape 1 : création d'un service Node.js et publication de ce service dans le cloud]: #step1
  [Étape 2 : obtention d'un certificat SSL]: #step2
  [Étape 3 : modification de l'application pour utiliser le certificat SSL]: #step3
  [Étape 4 : modification du fichier de définition de service]: #step4
  [Étape 5 : connexion à l'instance de rôle à l'aide de HTTPS]: #step5
  [**Azure PowerShell**]: http://go.microsoft.com/?linkid=9790229&clcid=0x409
  
  
  
  
  [1]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-01.png
  [2]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-02-worker.png
  [3]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-03-worker.png
  [Portail de gestion Azure]: http://manage.windowsazure.com
  
  
  [Comment associer un certificat à un Service]: http://msdn.microsoft.com/library/windowsazure/gg465718.aspx
  
  [site-url]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/site-url.png
  [8]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-08.png
  [Comment configurer un certificat SSL sur un point de terminaison HTTPS]: http://msdn.microsoft.com/library/windowsazure/ff795779.aspx
  [powershell-menu]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/azure-powershell-start.png
  
  
  [Configuration de SSL pour une Application Node.js dans un rôle Web Azure]: /fr-fr/develop/nodejs/common-tasks/enable-ssl/
  

<!--HONumber=45--> 
