<properties linkid="dev-nodejs-getting-started" urlDisplayName="Cloud Service" pageTitle="Node.js Getting Started Guide - Azure Tutorial" metaKeywords="Azure node.js getting started, Azure Node.js tutorial, Azure Node.js tutorial" description="An end-to-end tutorial that helps you develop a simple Node.js web application and deploy it to Azure." metaCanonical="" services="cloud-services" documentationCenter="Node.js" title="Build and deploy a Node.js application to an Azure Cloud Service" authors="larryfr" solutions="" manager="" editor="" />

Création et déploiement d'une application Node.js dans Azure Cloud Services
===========================================================================

À la fin de ce guide, vous disposerez d'une application Node.js simple qui s'exécute dans Azure Cloud Services. Les services de cloud computing sont les blocs de construction des applications cloud extensibles dans Azure. Ils permettent de séparer, de gérer et d'étendre de façon indépendante le composant frontal et le composant principal de votre application. Cloud Services héberge de façon fiable chaque rôle sur une machine virtuelle dédiée.

Pour plus d'informations sur Cloud Services et pour connaître les différences avec Sites Web Azure et Azure Virtual Machines, consultez la page [Sites Web, services cloud et machines virtuelles Azure : que choisir ?](http://msdn.microsoft.com/fr-fr/library/windowsazure/jj218759.aspx)

**Vous voulez juste un site Web ?**

Si votre scénario ne comporte qu'un simple composant frontal Web, envisagez l'utilisation d'un [site Web Azure léger.](../create-a-website-(mac)) Vous pouvez facilement passer à un service cloud en fonction de la croissance de votre site et de vos besoins.

Dans ce didacticiel, vous allez créer une application Web simple, hébergée dans un rôle Web. Vous allez utiliser l'émulateur de calcul pour tester votre application en local, puis vous allez la déployer à l'aide d'outils en ligne de commande PowerShell.

Voici une capture d'écran de l'application terminée :

![Une fenêtre de navigateur affichant la page hello world. L'URL indique que la page est hébergée sur Azure.](https://wacomdpsstablestorage.blob.core.windows.net/articlesmedia/demo-ppe.windowsazure.com/fr-fr/documentation/articles/cloud-services-nodejs-develop-deploy-app/20140107035927/node21.png)

Création d'une application Node
-------------------------------

Effectuez les tâches suivantes pour créer un projet Azure Cloud Services, avec la structure Node.js de base :

1.  À partir du menu **Démarrer** ou de l'**écran d'accueil**, recherchez **Azure PowerShell**. Enfin, cliquez avec le bouton droit sur **Azure PowerShell** et sélectionnez **Exécuter en tant qu'administrateur**.

    ![Icône Azure PowerShell](./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-start.png)

    [WACOM.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]

2.  Créez un répertoire **node** sur votre disque C et accédez au répertoire c:\\node :

    ![Une invite de commandes s'affiche, avec les commandes mkdir c:\\\\node et cd node.](./media/cloud-services-nodejs-develop-deploy-app/getting-started-6.png)

3.  Entrez la cmdlet suivante pour créer une solution :

        PS C:\node> New-AzureServiceProject helloworld

        Vous obtenez la réponse suivante :

    ![Le résultat de la nouvelle commande helloworld New-AzureService](./media/cloud-services-nodejs-develop-deploy-app/node9.png)

    La cmdlet **New-AzureServiceProject** génère une structure de base pour créer une application Azure Node qui sera publiée sur Azure Cloud Services. Elle contient les fichiers de configuration nécessaires à la publication sur Azure. La cmdlet change aussi votre répertoire de travail et le remplace par le répertoire du service.

    Les fichiers créés par la cmdlet **New-AzureServiceProject** sont :

    -   **ServiceConfiguration.Cloud.cscfg**, **ServiceConfiguration.Local.cscfg** et **ServiceDefinition.csdef** sont des fichiers propres à Azure, nécessaires à la publication de votre application.

    Pour plus d'informations sur ces fichiers, consultez la page [Présentation de la création d'un service hébergé pour Azure](http://msdn.microsoft.com/fr-fr/library/windowsazure/jj155995.aspx).

    -   **deploymentSettings.json** stocke les paramètres locaux utilisés par les cmdlets de déploiement Azure PowerShell.

4.  Entrez la commande suivante pour ajouter un nouveau rôle Web à l'aide de la cmdlet **Add-AzureNodeWebRole** :

        PS C:\node\helloworld> Add-AzureNodeWebRole

    Vous obtenez la réponse suivante :

    ![Résultat de la commande Add-AzureNodeWebRole.](./media/cloud-services-nodejs-develop-deploy-app/node11.png)

    La cmdlet **Add-AzureNodeWebRole** crée un répertoire pour votre application et génère la structure d'une application Node.js de base. Elle modifie également les fichiers **ServiceConfiguration.Cloud.csfg**, **ServiceConfiguration.Local.csfg** et **ServiceDefinition.csdef** créés à l'étape précédente afin d'ajouter des entrées de configuration pour le nouveau rôle.

    **Remarque**

    Par défaut, si vous n'indiquez pas de nom de rôle, un nom est créé. Vous pouvez indiquer un nom comme premier paramètre de **Add-AzureNodeWebRole**. Par exemple, `Add-AzureNodeWebRole MyRole`

5.  Utilisez les commandes suivantes pour accéder au répertoire **WebRole1**, puis ouvrez le fichier **server.js** dans le Bloc-notes.

    PS C:\\node\\helloworld\> cd WebRole1 
	PS C:\\node\\helloworld\\WebRole1\> notepad server.js

    Le fichier **server.js** a été créé par la cmdlet **Add-AzureNodeWebRole** et il contient le code de démarrage suivant. Ce code est similaire à l'exemple « Hello World » du site Web [nodejs.org](http://nodejs.org/), à l'exception des points suivants :

	-	Le port a été modifié pour que l'application trouve le port qui lui est attribué par l'environnement cloud.

	-	La journalisation de la console a été supprimée.

    ![Notepad displaying the contents of server.js](./media/cloud-services-nodejs-develop-deploy-app/node13.png)

Exécution de votre application en local dans l'émulateur
--------------------------------------------------------

Un des outils installés par le Kit de développement logiciel (SDK) Azure est l'émulateur de calcul, qui vous permet de tester votre application en local. Il simule l'environnement dans lequel votre application sera exécutée lors de son déploiement dans le cloud. Effectuez les étapes suivantes pour tester l'application dans l'émulateur.

1.  Fermez le Bloc-notes et revenez à la fenêtre Windows PowerShell. Entrez la cmdlet suivante pour exécuter votre service dans l'émulateur :

        PS C:\node\helloworld\WebRole1> Start-AzureEmulator -Launch

    Le paramètre **-Launch** spécifie que les outils doivent automatiquement ouvrir une fenêtre de navigateur et afficher l'application une fois qu'elle s'exécute dans l'émulateur. Un navigateur s'ouvre et affiche « Hello World », comme dans la capture d'écran ci-dessous. Cela indique que le service est en cours d'exécution dans l'émulateur et fonctionne correctement.

    ![Navigateur Web avec la page Web Hello World](./media/cloud-services-nodejs-develop-deploy-app/node14.png)

2.  Pour arrêter l'émulateur de calcul, utilisez la commande **Stop-AzureEmulator** suivante :

    PS C:\\node\\helloworld\\WebRole1\> Stop-AzureEmulator

Déploiement de l'application dans Azure
---------------------------------------

    [WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

### Téléchargement des paramètres de publication Azure

Afin de déployer votre application sur Azure, vous devez télécharger les paramètres de publication de votre abonnement Azure. Les étapes suivantes vous présentent ce processus :

1.  Dans la fenêtre Windows PowerShell, lancez la page de téléchargement en exécutant la cmdlet suivante :

        PS C:\node\helloworld\WebRole1> Get-AzurePublishSettingsFile

    Ceci utilise votre navigateur pour accéder à la page de téléchargement des paramètres de publication. Il est possible que vous soyez invité à vous connecter avec un compte Microsoft. Si c'est le cas, utilisez le compte associé à votre abonnement Azure.

    Enregistrez le profil téléchargé dans un emplacement auquel vous pouvez accéder facilement.

2.  Dans la fenêtre Azure Windows PowerShell, utilisez la cmdlet suivante pour configurer les cmdlets Windows PowerShell pour Node.js afin qu'elles utilisent le profil de publication Azure que vous avez téléchargé :

        PS C:\node\helloworld\WebRole1> Import-AzurePublishSettingsFile [chemin d'accès du fichier]

    **Remarque**

    Après l'importation des paramètres de publication, pensez à supprimer le fichier .publishSettings téléchargé, car il contient des informations pouvant être utilisées par d'autres personnes pour accéder à votre compte.

### Publication de l'application

1.  Publiez l'application à l'aide de la cmdlet **Publish-AzureServiceProject**, comme illustré ci-dessous.

        PS C:\node\helloworld\WebRole1> Publish-AzureServiceProject -ServiceName NodeHelloWorld -Location "East US" -Launch

    -   Le paramètre **servicename** spécifie le nom utilisé pour ce déploiement. Ce nom doit être unique, sans quoi le processus de publication échoue.

    -   Le paramètre **location** spécifie le centre de données dans lequel votre application sera hébergée. Pour voir la liste des centres de données disponibles, utilisez la cmdlet **Get-AzureLocation**.

    -   Le paramètre **launch** lance votre navigateur et accède aux services hébergés une fois le déploiement terminé.

    Une fois la publication effectuée, vous devez obtenir une réponse semblable à celle-ci :

    ![Résultat de la commande Publish-AzureService.](./media/cloud-services-nodejs-develop-deploy-app/node19.png)

    La cmdlet **Publish-AzureServiceProject** effectue les opérations suivantes :

2.  Elle crée le package à déployer sur Azure. Ce package contient tous les fichiers de votre dossier d'application node.js.

3.  Elle crée un **compte de stockage**, si celui-ci n'existe pas. Le compte de stockage Azure permet de stocker le package de l'application au cours du déploiement. Vous pouvez supprimer en toute sécurité le compte de stockage une fois le déploiement terminé.

4.  Elle crée un **service cloud**, si celui-ci n'existe pas. Le **service cloud** est le conteneur dans lequel votre application est hébergée lorsqu'elle est déployée sur Azure. Pour plus d'informations, consultez la page [Présentation de la création d'un service hébergé pour Azure](http://msdn.microsoft.com/fr-fr/library/windowsazure/jj155995.aspx).

5.  Elle publie le package de déploiement sur Azure.

    > [WACOM.NOTE] 
    > Il peut s'écouler de 5 à 7 minutes avant que l'application ne soit déployée et disponible lors de la première publication.

    Une fois le déploiement terminé, une fenêtre de navigateur s'ouvre et accède au service cloud.

    ![Une fenêtre de navigateur affichant la page hello world. L'URL indique que la page est hébergée sur Azure.](./media/cloud-services-nodejs-develop-deploy-app/node21.png)

    Votre application s'exécute maintenant sur Azure.

Arrêt et suppression de votre application
-----------------------------------------

Après avoir déployé votre application, vous pouvez la désactiver afin de vous éviter des coûts supplémentaires. Azure facture les instances de rôle Web par heure de serveur consommée. Une fois votre application déployée, elle consomme du temps de serveur, même si les instances ne sont pas exécutées et sont arrêtées.

1.  Dans la fenêtre Windows PowerShell, arrêtez le déploiement du service créé dans la section précédente à l'aide de la cmdlet suivante :

        PS C:\node\helloworld\WebRole1> Stop-AzureService

    L'arrêt du service peut prendre plusieurs minutes. Une fois le service arrêté, vous recevez un message confirmant l'arrêt du service.

    ![État de la commande Stop-AzureService.](./media/cloud-services-nodejs-develop-deploy-app/node48.png)

2.  Pour supprimer le service, utilisez la cmdlet suivante :

        PS C:\node\helloworld\WebRole1> Remove-AzureService

    Lorsque vous y êtes invité, entrez **Y** pour supprimer le service.

    La suppression du service peut prendre plusieurs minutes. Une fois le service supprimé, vous recevez un message confirmant la suppression du service.

    ![État de la commande Remove-AzureService](./media/cloud-services-nodejs-develop-deploy-app/node49.png)

    **Remarque**

    La suppression du service ne supprime pas le compte de stockage qui a été créé lors de la publication initiale du service. Le stockage utilisé continuera à vous être facturé. Pour plus d'informations sur la suppression d'un compte de stockage, consultez la page [Suppression d'un compte de stockage d'un abonnement Azure](http://msdn.microsoft.com/fr-fr/library/windowsazure/hh531562.aspx).

[The Windows Start menu with the Azure SDK Node.js entry expanded]: ./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-menu.png
[mkdir]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-6.png
[nodejs.org]: http://nodejs.org/
[A directory listing of the helloworld folder.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-7.png
[Overview of Creating a Hosted Service for Azure]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj155995.aspx
[A directory listing of the WebRole1 folder]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-8.png
[The menu displayed when right-clicking the Azure emulator from the task bar.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-11.png
[A browser window displaying http://www.windowsazure.com/ with the Free Trial link highlighted]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-12.png
[A browser window displaying the liveID sign in page]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-13.png
[Internet Explorer displaying the save as dialog for the publishSettings file.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-14.png

[The full status output of the Publish-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node20.png
[How to Delete a Storage Account from an Azure Subscription]: https://www.windowsazure.com/en-us/manage/services/storage/how-to-manage-a-storage-account/
[powershell-menu]: ./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-start.png
