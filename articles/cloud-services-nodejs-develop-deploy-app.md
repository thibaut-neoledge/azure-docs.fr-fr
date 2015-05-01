<properties 
	pageTitle="Guide de démarrage Node.js - Didacticiel Azure" 
	description="Apprenez à créer une application web Node.js simple et à la déployer vers un service cloud Azure." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="hero-article" 
	ms.date="02/24/2015" 
	ms.author="mwasson"/>


# Création et déploiement d'une application Node.js dans Azure Cloud Services

> [AZURE.SELECTOR]
- [Node.js](cloud-services-nodejs-develop-deploy-app.md)
- [.NET](cloud-services-dotnet-get-started.md)

Ce didacticiel explique comment créer une application Node.js simple s'exécutant dans Azure Cloud Services. Les services de cloud computing sont les blocs de construction des applications cloud extensibles dans Azure. Ils permettent de séparer, de gérer et d'étendre de façon indépendante le composant frontal et le composant principal de votre application.  Cloud Services héberge de façon fiable chaque rôle sur une machine virtuelle dédiée.

Pour plus d'informations sur les services cloud, et pour connaître les différences avec les sites Web Azure et les machines virtuelles, consultez [Comparaison des sites Web Azure, des services cloud et des machines virtuelles](http://azure.microsoft.com/documentation/articles/choose-web-site-cloud-service-vm/).

>[AZURE.TIP] Vous voulez créer un simple site Web ? Si votre scénario ne comporte qu'un simple composant frontal Web, envisagez d'<a href="/documentation/articles/web-sites-nodejs-develop-deploy-mac/">utiliser un site Web Azure léger</a>. Vous pouvez facilement passer à un service cloud en fonction de l'évolution de votre site et de vos besoins.


Dans ce didacticiel, vous allez créer une application Web simple, hébergée dans un rôle Web. Vous utiliserez l'émulateur de calcul pour tester votre application localement, puis déploierez cette dernière à l'aide d'outils en ligne de commande PowerShell.

Il s'agit d'une application " hello world " simple :

<p><img src="https://wacomdpsstablestorage.blob.core.windows.net/articlesmedia/demo-ppe.windowsazure.com/documentation/articles/cloud-services-nodejs-develop-deploy-app/20140107035927/node21.png" alt="Fenêtre du navigateur affichant la page hello world. L'URL indique que la page est hébergée sur Azure.">
</p>

## Conditions préalables

> [AZURE.NOTE] Ce didacticiel utilise Azure PowerShell, qui nécessite Windows.

- Installez le Kit de développement logiciel (SDK) Azure pour Node.js : <a href="http://go.microsoft.com/fwlink/?LinkId=254279">programme d'installation Windows</a>. 

- Installez et configurez [Azure Powershell](install-configure-powershell.md).


## Créer un projet Azure Cloud Services

Effectuez les tâches suivantes pour créer un projet Azure Cloud Services, avec la structure Node.js de base :


1. Exécutez **Azure PowerShell** en tant qu'administrateur. (À partir du menu **Démarrer** ou de l'**écran d'accueil**, recherchez **Azure PowerShell**.)

2.  Entrez l'applet de commande PowerShell suivante pour créer le projet :

        New-AzureServiceProject helloworld

	![The result of the New-AzureService helloworld command](./media/cloud-services-nodejs-develop-deploy-app/node9.png)

	L'applet de commande **New-AzureServiceProject** génère une structure de base pour publier une application Node.js dans un service cloud. Elle contient les fichiers de configuration nécessaires à la publication sur Azure. L'applet de commande change aussi votre répertoire de travail et le remplace par le répertoire du service.

	L'applet de commande crée les fichiers suivants :

	-   **ServiceConfiguration.Cloud.cscfg**,
        **ServiceConfiguration.Local.cscfg** and **ServiceDefinition.csdef**: 
        Azure-specific files necessary for publishing your
        application. Pour plus d'informations, consultez la [vue d'ensemble de la création d'un service hébergé pour Azure][].

	-   **deploymentSettings.json**: stocke les paramètres locaux utilisés par les cmdlets de déploiement Azure PowerShell.

4.  Entrez la commande suivante pour ajouter un nouveau rôle Web :

        Add-AzureNodeWebRole
	
	![The output of the Add-AzureNodeWebRole command.](./media/cloud-services-nodejs-develop-deploy-app/node11.png)

	L'applet de commande **Add-AzureNodeWebRole** crée une application Node.js de base. Elle modifie également les fichiers **.csfg** et **.csdef** afin d'ajouter des entrées de configuration pour le nouveau rôle.

	> [AZURE.NOTE] Si vous ne spécifiez pas de nom de rôle, un nom par défaut est utilisé. Vous pouvez indiquer un nom comme premier paramètre de l'applet de commande : `Add-AzureNodeWebRole MyRole`


L'application Node.js est définie dans le fichier **server.js**, situé dans le répertoire du rôle Web (**WebRole1** par défaut). Voici le code :

	var http = require('http');
	var port = process.env.port || 1337;
	http.createServer(function (req, res) {
	    res.writeHead(200, { 'Content-Type': 'text/plain' });
	    res.end('Hello World\n');
	}).listen(port);

Ce code est essentiellement identique à l'exemple " Hello World " sur le site web [nodejs.org][], sauf qu'il utilise le numéro de port attribué par l'environnement de cloud.


## Exécuter l'application localement dans l'émulateur

L'émulateur de calcul Azure constitue l'un des outils installés par le Kit de développement logiciel (SDK) Azure.
Il vous permet de tester votre application localement. The
émulateur de calcul simule l'environnement dans lequel votre application s'exécute
lorsqu'elle est déployée sur le cloud. 

1.  Entrez l'applet de commande Azure PowerShell suivante pour exécuter votre service dans l'émulateur :

        Start-AzureEmulator -Launch

	Utilisez le paramètre **-Launch** pour ouvrir automatiquement une fenêtre de navigateur lorsque le rôle Web s'exécute dans l'émulateur. La fenêtre du navigateur doit afficher " Hello World ", comme dans la capture d'écran ci-dessous. 

	![A web browser displaying the Hello World web page](./media/cloud-services-nodejs-develop-deploy-app/node14.png)

2.  Pour arrêter l'émulateur de calcul, utilisez l'applet de commande **Stop-AzureEmulator** suivante :
	
		Stop-AzureEmulator

## Déploiement de l'application dans Azure

	[AZURE.INCLUDE [create-account-note](../includes/create-account-note.md)]


### Télécharger les paramètres de publication Azure

Pour déployer votre application sur Azure, vous devez télécharger les paramètres de publication de votre abonnement Azure. 

1.  Exécutez l'applet de commande Azure PowerShell suivante :

        Get-AzurePublishSettingsFile

	Ceci utilise votre navigateur pour accéder à la page de téléchargement des paramètres de publication. Il est possible que vous soyez invité à vous connecter avec un compte Microsoft. Si c'est le cas, utilisez le compte associé à votre abonnement Azure.

	Enregistrez le profil téléchargé dans un emplacement auquel vous pouvez accéder facilement.

2.  Exécutez l'applet de commande suivante pour importer le profil de publication que vous avez téléchargé :

        Import-AzurePublishSettingsFile [chemin d'accès au fichier]


	> [AZURE.NOTE] Après l'importation des paramètres de publication, pensez à supprimer le fichier .publishSettings téléchargé, car il contient des informations qui pourraient permettre à d'autres personnes d'accéder à votre compte.
    

### Publication de l'application

Pour procéder à la publication, exécutez l'applet de commande **Publish-AzureServiceProject** comme suit :

    Publish-AzureServiceProject -ServiceName NodeHelloWorld -Location "East US" -Launch

- **-ServiceName** spécifie le nom du déploiement. Ce nom doit être unique, sans quoi le processus de publication échouera.

- Le paramètre **-Location** spécifie le centre de données dans lequel votre application sera hébergée. Pour voir la liste des centres de données disponibles, utilisez l'applet de commande **Get-AzureLocation**.

- Le paramètre **-Launch** ouvre une fenêtre de navigateur et accède au service hébergé une fois le déploiement terminé.

Une fois la publication effectuée, vous devez obtenir une réponse semblable à celle-ci :

![The output of the Publish-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node19.png)

> [AZURE.NOTE]
> Il peut s'écouler entre 5 et 7 minutes avant que l'application soit déployée et disponible lors de la première publication.

Une fois le déploiement terminé, une fenêtre de navigateur s'ouvre et accède au service cloud.


![A browser window displaying the hello world page. The URL indicates the page is hosted on Azure.](./media/cloud-services-nodejs-develop-deploy-app/node21.png)

Votre application s'exécute maintenant sur Azure.

L'applet de commande **Publish-AzureServiceProject** effectue les opérations suivantes :

1.  Elle crée un package à déployer. Ce package contient tous les fichiers de votre dossier d'application.

2.  Elle crée un **compte de stockage**, si celui-ci n'existe pas. Le compte de stockage Azure permet de stocker le package de l'application au cours du déploiement. Vous pouvez supprimer en toute sécurité le compte de stockage une fois le déploiement terminé.

3.  Elle crée un **service cloud**, si celui-ci n'existe pas. Le **service cloud** est le conteneur dans lequel votre application est hébergée quand elle est déployée sur Azure. Pour plus d'informations, consultez la page [Vue d'ensemble de la création d'un service hébergé pour Azure][].

4.  Elle publie le package de déploiement sur Azure.



## Arrêt et suppression de votre application

Après avoir déployé votre application, vous pouvez la désactiver afin de vous éviter des coûts supplémentaires. Azure facture les instances de rôle Web par heure de serveur consommée. Une fois votre application déployée, elle consomme du temps de serveur, même si les instances ne sont pas exécutées et sont arrêtées.

1.  Dans la fenêtre Windows PowerShell, arrêtez le déploiement du service créé dans la section précédente à l'aide de l'applet de commande suivante :

        Stop-AzureService

	L'arrêt du service peut prendre plusieurs minutes. Une fois le service arrêté, vous recevez un message confirmant l'arrêt du service.

	![The status of the Stop-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node48.png)

2.  Pour supprimer le service, utilisez l'applet de commande suivante :

        Remove-AzureService

	Quand vous y êtes invité, entrez **Y** pour supprimer le service.

	La suppression du service peut prendre plusieurs minutes. Une fois le service supprimé, vous recevez un message confirmant la suppression du service.

	![The status of the Remove-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node49.png)

	> [AZURE.NOTE] La suppression du service ne supprime pas le compte de stockage qui a été créé lors de la publication initiale du service. Le stockage utilisé continuera à vous être facturé. Pour en savoir plus sur la suppression d'un compte de stockage, consultez la page [Suppression d'un compte de stockage d'un abonnement Azure](http://msdn.microsoft.com/library/windowsazure/hh531562.aspx).


[Menu Démarrer de Windows avec l'entrée Node.js développée du Kit de développement logiciel (SDK) Azure]: ./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-menu.png
[mkdir]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-6.png
[nodejs.org]: http://nodejs.org/
[Liste des répertoires du dossier helloworld.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-7.png
[Overview of Creating a Hosted Service for Azure]: http://msdn.microsoft.com/library/windowsazure/jj155995.aspx
[Vue d'ensemble de la création d'un service hébergé pour Azure]: http://msdn.microsoft.com/library/windowsazure/jj155995.aspx
[Liste des répertoires du dossier WebRole1]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-8.png
[Menu affiché quand vous cliquez avec le bouton droit sur l'émulateur Azure dans la barre des tâches.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-11.png
[Fenêtre du navigateur affichant http://www.windowsazure.com/ avec le lien vers la version d'évaluation gratuite en surbrillance]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-12.png
[Fenêtre du navigateur affichant la page de connexion liveID]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-13.png
[Internet Explorer affichant la boîte de dialogue Enregistrer sous pour le fichier publishSettings.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-14.png

[Sortie d'état complète de la commande Publish-AzureService]: ./media/cloud-services-nodejs-develop-deploy-app/node20.png
[Comment supprimer un compte de stockage d'un abonnement Azure]: https://www.windowsazure.com/manage/services/storage/how-to-manage-a-storage-account/
[powershell-menu]: ./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-start.png


<!--HONumber=52-->