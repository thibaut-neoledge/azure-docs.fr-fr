<properties 
	pageTitle="Guide de démarrage Node.js - Didacticiel Azure" 
	description="Didacticiel détaillé qui vous aide à développer une application Web Node.js simple et à la déployer sur Azure." 
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






# Création et déploiement d'une application Node.js dans Azure Cloud Services

À la fin de ce guide, vous disposerez d'une application Node.js simple en cours d'exécution 
dans un service Azure Cloud. Les services cloud constituent les blocs de construction des 
applications cloud évolutives dans Azure. Ils vous permettent de séparer, de gérer indépendamment
et de monter en charge les composants serveurs frontaux et principaux de votre application.  Les services cloud 
hébergent de façon fiable chaque rôle sur une machine virtuelle dédiée.

Pour plus d'informations sur les services cloud, et pour connaître les différences avec les sites Web Azure et les machines virtuelles, consultez [Comparaison des sites Web Azure, des services cloud et des machines virtuelles](http://azure.microsoft.com/documentation/articles/choose-web-site-cloud-service-vm/).

>[AZURE.TIP] Vous voulez créer un simple site Web ? Si votre scénario ne comporte qu'un simple composant frontal de site Web, <a href="/fr-fr/documentation/articles/web-sites-nodejs-develop-deploy-mac/">utilisez un site Web Azure léger.</a> Vous pouvez facilement passer à un service cloud en fonction de l'évolution de votre site et de vos besoins.


Dans ce didacticiel, vous allez créer une application Web simple, hébergée dans un rôle Web. Vous
utiliserez l'émulateur de calcul afin de tester votre application localement pour ensuite la déployer
à l'aide des outils de ligne de commande PowerShell.

Voici une capture d'écran de l'application terminée :

<p><img src="https://wacomdpsstablestorage.blob.core.windows.net/articlesmedia/demo-ppe.windowsazure.com/fr-fr/documentation/articles/cloud-services-nodejs-develop-deploy-app/20140107035927/node21.png" alt="A browser window displaying the hello world page. The URL indicates the page is hosted on Azure.">
</p>



## Création d'une application Node

Effectuez les tâches suivantes pour créer un projet Azure Cloud Services, avec la structure Node.js de base :

1. À partir du menu **Démarrer** ou de l'**écran d'accueil**, recherchez **Azure PowerShell**. Enfin, cliquez avec le bouton droit sur **Azure PowerShell** et sélectionnez **Exécuter en tant qu'administrateur**.

	![Azure PowerShell icon][powershell-menu]

	[AZURE.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]


2.  Créez un répertoire **node** sur votre lecteur C et accédez au répertoire c:\node :
	
	![A command prompt displaying the commands 'mkdir c:\\node' and 'cd node'.][mkdir]

3.  Entrez l'applet de commande suivante pour créer une solution :

        PS C:\node> New-AzureServiceProject helloworld

    	Vous obtenez la réponse suivante :

	![The result of the New-AzureService helloworld command](./media/cloud-services-nodejs-develop-deploy-app/node9.png)

	L'applet de commande **New-AzureServiceProject** génère une structure de base pour créer une application Azure Node qui sera publiée dans un service cloud. Elle contient les fichiers de configuration nécessaires à la publication sur Azure. L'applet de commande change aussi votre répertoire de travail et le remplace par le répertoire du service.

	Les fichiers créés par l'applet de commande **New-AzureServiceProject** sont :

	-   **ServiceConfiguration.Cloud.cscfg**,
        **ServiceConfiguration.Local.cscfg** et **ServiceDefinition.csdef** sont
        des fichiers spécifiques à Azure nécessaires pour la publication de votre
        application.
		
	Pour plus d'informations sur ces fichiers, consultez
        [Présentation de la création d'un service hébergé pour Azure][].

	-   **deploymentSettings.json** stocke les paramètres locaux utilisés par
        les applets de commande de déploiement Azure PowerShell.

4.  Entrez la commande suivante pour ajouter un nouveau rôle Web à l'aide de l'
    **applet de commande Add-AzureNodeWebRole** :

        PS C:\node\helloworld> Add-AzureNodeWebRole

	Vous obtenez la réponse suivante :

	![The output of the Add-AzureNodeWebRole command.](./media/cloud-services-nodejs-develop-deploy-app/node11.png)

	L'applet de commande **Add-AzureNodeWebRole** crée un répertoire pour votre application et génère la structure d'une application Node.js de base. Elle modifie également les fichiers **ServiceConfiguration.Cloud.csfg**, **ServiceConfiguration.Local.csfg** et **ServiceDefinition.csdef** créés à l'étape précédente afin d'ajouter des entrées de configuration pour le nouveau rôle.

	> [AZURE.NOTE] Par défaut, si vous n'indiquez pas de nom de rôle, un nom est créé. Vous pouvez indiquer un nom comme premier paramètre de **Add-AzureNodeWebRole**. Par exemple, `Add-AzureNodeWebRole MyRole`

5.  Utilisez les commandes suivantes pour accéder au répertoire **WebRole1**, puis ouvrez le fichier **server.js** dans le Bloc-notes. 

	PS C:\\node\\helloworld> cd WebRole1
        PS C:\node\helloworld\WebRole1> notepad server.js

	Le fichier **server.js** a été créé par l'applet de commande **Add-AzureNodeWebRole**. Il contient le code de démarrage suivant. Ce code est similaire à l'exemple " Hello World " du site Web [nodejs.org][], à l'exception des points suivants :

   	-   Le port a été modifié pour que l'application trouve le 
        port qui lui est attribué par l'environnement cloud.
   	-   La journalisation de la console a été supprimée.

	![Notepad displaying the contents of server.js](./media/cloud-services-nodejs-develop-deploy-app/node13.png)

## Exécution de votre application en local dans l'émulateur

L'émulateur de calcul Azure constitue l'un des outils installés par le Kit de développement logiciel (SDK) Azure.
Il vous permet de tester votre application localement. L'
émulateur de calcul simule l'environnement dans lequel votre application s'exécute
lorsqu'elle est déployée sur le cloud. Effectuez les étapes suivantes pour tester l'application dans l'émulateur.

1.  Fermez le Bloc-notes et revenez à la fenêtre Windows PowerShell.
  	Entrez l'applet de commande suivante pour exécuter votre service dans l'émulateur :

        PS C:\node\helloworld\WebRole1> Start-AzureEmulator -Launch

	Le paramètre **-Launch** spécifie que les outils doivent automatiquement ouvrir une fenêtre du navigateur et afficher l'application une fois qu'elle s'exécute dans l'émulateur. Un navigateur s'ouvre et affiche " Hello World ", comme dans la capture d'écran ci-dessous. Cela indique que le service est en cours d'exécution dans l'émulateur et fonctionne correctement.

	![A web browser displaying the Hello World web page](./media/cloud-services-nodejs-develop-deploy-app/node14.png)

2.  Pour arrêter l'émulateur de calcul, utilisez la commande **Stop-AzureEmulator** suivante :
	
	PS C:\node\helloworld\WebRole1> Stop-AzureEmulator

## Déploiement de l'application dans Azure

	[AZURE.INCLUDE [create-account-note](../includes/create-account-note.md)]



###<a id="download_publishing_settings"></a>Téléchargement des paramètres de publication Azure

Afin de déployer votre application sur Azure, vous devez télécharger les paramètres de publication de votre abonnement Azure. Les étapes suivantes vous présentent ce processus :

1.  Dans la fenêtre Windows PowerShell, lancez la page de téléchargement en exécutant l'applet de commande suivante :

        PS C:\node\helloworld\WebRole1> Get-AzurePublishSettingsFile

	Ceci utilise votre navigateur pour accéder à la page de téléchargement des paramètres de publication. Il est possible que vous soyez invité à vous connecter avec un compte Microsoft. Si c'est le cas, utilisez le compte associé à votre abonnement Azure.

	Enregistrez le profil téléchargé dans un emplacement auquel vous pouvez accéder facilement.

2.  Dans la fenêtre Azure Windows PowerShell, utilisez l'applet de commande suivante pour configurer les applets de commande Windows PowerShell pour Node.js afin qu'elles utilisent le profil de publication Azure que vous avez téléchargé :

        PS C:\node\helloworld\WebRole1> Import-AzurePublishSettingsFile [path to file]


	> [AZURE.NOTE] Après l'importation des paramètres de publication, pensez à supprimer le fichier .publishSettings téléchargé, car il contient des informations pouvant être utilisées par d'autres personnes pour accéder à votre compte.
    

### Publication de l'application

1.  Publiez l'application à l'aide de l'applet de commande **Publish-AzureServiceProject**,
    comme indiqué ci-dessous.

        PS C:\node\helloworld\WebRole1> Publish-AzureServiceProject -ServiceName NodeHelloWorld -Location "East US" -Launch

	- Le paramètre **servicename** spécifie le nom utilisé pour ce déploiement. Ce nom doit être unique, sans quoi le processus de publication échoue.

	- Le paramètre **location** spécifie le centre de données dans lequel votre application sera hébergée. Pour voir la liste des centres de données disponibles, utilisez l'applet de commande **Get-AzureLocation**.

	- Le paramètre **launch** lance votre navigateur et accède aux services hébergés une fois le déploiement terminé.

	Une fois la publication effectuée, vous devez obtenir une réponse semblable à celle-ci :

	![The output of the Publish-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node19.png)

	L'applet de commande **Publish-AzureServiceProject** effectue les opérations suivantes :

1.  Elle crée le package à déployer sur Azure. Ce package contient tous les fichiers de votre dossier d'application node.js.

2.  Elle crée un **compte de stockage**, si celui-ci n'existe pas. Le compte de stockage Azure permet de stocker le package de l'application au cours du déploiement. Vous pouvez supprimer en toute sécurité le compte de stockage une fois le déploiement terminé.

3.  Elle crée un **service cloud**, si celui-ci n'existe pas. Le **service cloud** est le conteneur dans lequel votre application est hébergée quand elle est déployée sur Azure. Pour plus d'informations, consultez la page [Présentation de la création d'un service hébergé pour Azure][].

4.  Elle publie le package de déploiement sur Azure.


	> [AZURE.NOTE]
	> Il peut s'écouler entre 5 et 7 minutes avant que l'application soit déployée et disponible durant la première publication.

	Une fois le déploiement terminé, une fenêtre de navigateur s'ouvre et accède au service cloud.


	![A browser window displaying the hello world page. The URL indicates the page is hosted on Azure.](./media/cloud-services-nodejs-develop-deploy-app/node21.png)

	Votre application s'exécute maintenant sur Azure.


## Arrêt et suppression de votre application

Après avoir déployé votre application, vous pouvez la désactiver afin de vous éviter des coûts supplémentaires. Azure facture les instances de rôle Web par heure de serveur consommée. Une fois votre application déployée, elle consomme du temps de serveur, même si les instances ne sont pas exécutées et sont arrêtées.

1.  Dans la fenêtre Windows PowerShell, arrêtez le déploiement du service créé dans la section précédente à l'aide de l'applet de commande suivante :

        PS C:\node\helloworld\WebRole1> Stop-AzureService

	L'arrêt du service peut prendre plusieurs minutes. Une fois le service arrêté, vous recevez un message confirmant l'arrêt du service.

	![The status of the Stop-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node48.png)

2.  Pour supprimer le service, utilisez l'applet de commande suivante :

        PS C:\node\helloworld\WebRole1> Remove-AzureService

	Quand vous y êtes invité, entrez **Y** pour supprimer le service.

	La suppression du service peut prendre plusieurs minutes. Une fois le service supprimé, vous recevez un message confirmant la suppression du service.

	![The status of the Remove-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node49.png)

	> [AZURE.NOTE] La suppression du service ne supprime pas le compte de stockage qui a été créé lors de la publication initiale du service. Le stockage utilisé continuera à vous être facturé. Pour en savoir plus sur la suppression d'un compte de stockage, consultez la page [Suppression d'un compte de stockage d'un abonnement Azure](http://msdn.microsoft.com/library/windowsazure/hh531562.aspx).


[The Windows Start menu with the Azure SDK Node.js entry expanded]: ./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-menu.png
[mkdir]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-6.png
[nodejs.org]: http://nodejs.org/
[A directory listing of the helloworld folder.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-7.png
[Présentation de la création d'un service hébergé pour Azure]: http://msdn.microsoft.com/library/windowsazure/jj155995.aspx
[A directory listing of the WebRole1 folder]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-8.png
[The menu displayed when right-clicking the Azure emulator from the task bar.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-11.png
[A browser window displaying http://www.windowsazure.com/ with the Free Trial link highlighted]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-12.png
[A browser window displaying the liveID sign in page]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-13.png
[Internet Explorer displaying the save as dialog for the publishSettings file.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-14.png

[The full status output of the Publish-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node20.png
[Comment supprimer un compte de stockage d'un abonnement Azure]: https://www.windowsazure.com/fr-fr/manage/services/storage/how-to-manage-a-storage-account/
[powershell-menu]: ./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-start.png

<!--HONumber=45--> 
