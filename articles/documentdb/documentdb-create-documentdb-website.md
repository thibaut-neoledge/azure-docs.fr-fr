<properties 
	pageTitle="Déploiement de DocumentDB et d’Azure App Service Web Apps avec un modèle Azure Resource Manager | Azure" 
	description="Découvrez comment déployer un compte DocumentDB, Azure App Service Web Apps et un exemple d’application web avec un modèle Azure Resource Manager." 
	services="documentdb, app-service\web" 
	authors="stephbaron" 
	manager="johnmac" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="stbaro"/>

# Déploiement de DocumentDB et d’Azure App Service Web Apps avec un modèle Azure Resource Manager #

Ce didacticiel vous montre comment utiliser un modèle Azure Resource Manager pour déployer et intégrer [Microsoft Azure DocumentDB](http://azure.microsoft.com/services/documentdb/), une application web [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) et un exemple d’application web.

Après avoir terminé ce didacticiel, vous serez en mesure de répondre aux questions suivantes :

-	Comment puis-je utiliser un modèle Azure Resource Manager pour déployer et intégrer un compte DocumentDB et une application web dans Azure App Service ?
-	Comment puis-je utiliser un modèle Azure Resource Manager pour déployer et intégrer un compte DocumentDB, une application web dans App Service Web Apps et une application Webdeploy ?

<a id="Prerequisites"></a>
## Configuration requise ##
> [AZURE.TIP]Ce didacticiel ne suppose pas d'être familiarisé avec les modèles de gestionnaire des ressources Azure, avec JSON ou avec Azure PowerShell, sauf si vous envisagez de modifier les modèles référencés ou les options de déploiement.

Avant de suivre les instructions de ce didacticiel, assurez-vous de disposer des éléments suivants :

- Un abonnement Azure. Azure est une plateforme disponible par abonnement. Pour plus d'informations sur la façon de se procurer un abonnement, consultez les [formules d'abonnement](http://azure.microsoft.com/pricing/purchase-options/), les [offres spéciales membres](http://azure.microsoft.com/pricing/member-offers/) ou la [version d'évaluation gratuite](http://azure.microsoft.com/pricing/free-trial/).
- Un compte de stockage Azure. Pour obtenir des instructions, consultez [À propos des comptes de stockage Azure](../storage-whatis-account.md).
- Un poste de travail sur lequel est installé Azure PowerShell. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell](../install-configure-powershell.md).

##<a id="CreateDB"></a>Étape 1 : téléchargement et extraction des fichiers d’exemple ##
Commençons par télécharger les fichiers d'exemple que nous utiliserons dans ce didacticiel.

1. Téléchargez [l’exemple de création d’un compte DocumentDB, de Web Apps et de déploiement d’une application de démonstration](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebsiteTodo.zip) dans un dossier local (par exemple, C:\\DocumentDBTemplates), puis extrayez les fichiers. Cet exemple déploie un compte DocumentDB, une application web App Service et une application web. Il configure également automatiquement l'application web pour se connecter au compte DocumentDB.

2. Téléchargez [l’exemple de création d’un compte DocumentDB et de Web Apps](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebSite.zip) dans un dossier local (par exemple, C:\\DocumentDBTemplates), puis extrayez les fichiers. Cet exemple déploie un compte DocumentDB et une application web App Service, ainsi qu’il modifie la configuration de l’application web pour faire facilement apparaître les informations de connexion DocumentDB. Toutefois, il n’inclut pas d’application web.

> [AZURE.TIP]Notez qu’en fonction des paramètres de sécurité de votre ordinateur, vous devrez débloquer les fichiers extraits en cliquant avec le bouton droit sur **Propriétés**, puis sur **Débloquer**.

![Capture d’écran de la fenêtre Propriétés avec le bouton Débloquer mis en surbrillance](./media/documentdb-create-documentdb-website/image1.png)

<a id="Build"></a>
##Étape 2 : déploiement de l’exemple de compte DocumentDB, d’une application web App Service et d’une application de démonstration ##

Maintenant, nous allons déployer notre premier modèle.

> [AZURE.TIP]Ce modèle ne valide pas que le nom de l’application web et le nom du compte DocumentDB entrés ci-dessous sont a) corrects et b) disponibles. Il est vivement recommandé de vérifier la disponibilité des noms que vous souhaitez fournir avant d'exécuter le script de déploiement PowerShell.

1. Ouvrez Microsoft Azure PowerShell et accédez au dossier dans lequel vous avez téléchargé et extrait [l’exemple de création d’un compte DocumentDB, d’une application web App Service et de déploiement d’une application de démonstration](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebsiteTodo.zip) (par exemple, C:\\DocumentDBTemplates\\CreateDocDBWebsiteTodo).


2. Nous allons exécuter le script PowerShell CreateDocDBWebsiteTodo.ps1. Le script accepte les paramètres obligatoires suivants :
	- WebsiteName : spécifie le nom de l’application web App Service utilisée pour former l’URL d’accès à l’application web (par exemple, si vous spécifiez « mydemodocdbwebsite », l’URL par laquelle vous accéderez à l’application web sera mydemodocdbwebsite.azurewebsites.net).

	- ResourceGroupName : spécifie le nom du groupe de ressources Azure à déployer. Si le groupe de ressources spécifié n'existe pas, il sera créé.

	- docDBAccountName : spécifie le nom du compte DocumentDB à créer.

	- location : spécifie l’emplacement Azure dans lequel créer les ressources DocumentDB et d’application web. Les valeurs valides sont Asie orientale, Asie du Sud-est, Est des États-Unis, Ouest des États-Unis, Europe du Nord, Europe de l'Ouest (notez que la valeur de l'emplacement fournie est sensible à la casse).


3. Voici un exemple de commande pour exécuter le script :

    	PS C:\DocumentDBTemplates\CreateDocDBWebAppTodo> .\CreateDocDBWebsiteTodo.ps1 -WebSiteName "mydemodocdbwebapp" -ResourceGroupName "myDemoResourceGroup" -docDBAccountName "mydemodocdbaccount" -location "West US"

	> [AZURE.TIP]Notez que vous serez invité à entrer vos nom d'utilisateur et mot de passe de compte Azure dans le cadre de l'exécution du script. L'exécution complète du déploiement prendra entre 10 et 15 minutes.

4. Voici un exemple de la sortie obtenue :

		VERBOSE: 1:06:00 PM - Created resource group 'myDemoResourceGroup' in location westus'
		VERBOSE: 1:06:01 PM - Template is valid.
		VERBOSE: 1:06:01 PM - Create template deployment 'Microsoft.DocumentDBWebSiteTodo'.
		VERBOSE: 1:06:08 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is running
		VERBOSE: 1:06:10 PM - Resource Microsoft.Web/serverFarms 'mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:14 PM - Resource microsoft.insights/alertrules 'CPUHigh mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:16 PM - Resource microsoft.insights/autoscalesettings 'mydemodocdbwebapp-myDemoResourceGroup' provisioning status is succeeded
		VERBOSE: 1:06:16 PM - Resource microsoft.insights/alertrules 'LongHttpQueue mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:21 PM - Resource Microsoft.Web/Sites 'mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:23 PM - Resource microsoft.insights/alertrules 'ForbiddenRequests mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:25 PM - Resource microsoft.insights/alertrules 'ServerErrors mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:25 PM - Resource microsoft.insights/components 'mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:16:22 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is succeeded
		VERBOSE: 1:16:22 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is succeeded
		VERBOSE: 1:16:24 PM - Resource Microsoft.Web/Sites/config 'mydemodocdbwebapp/web' provisioning status is succeeded
		VERBOSE: 1:16:27 PM - Resource Microsoft.Web/Sites/Extensions 'mydemodocdbwebapp/MSDeploy' provisioning status is running
		VERBOSE: 1:16:35 PM - Resource Microsoft.Web/Sites/Extensions 'mydemodocdbwebapp/MSDeploy' provisioning status is succeeded

		ResourceGroupName : myDemoResourceGroup
		Location          : westus
		Resources         : {mydemodocdbaccount, CPUHigh mydemodocdbwebapp, ForbiddenRequests mydemodocdbwebapp, LongHttpQueue mydemodocdbwebapp...}
		ResourcesTable    :
                    Name                                    Type                                   Location
                    ======================================  =====================================  =========
                    mydemodocdbaccount                      Microsoft.DocumentDb/databaseAccounts  westus
                    CPUHigh mydemodocdbwebapp              microsoft.insights/alertrules          eastus
                    ForbiddenRequests mydemodocdbwebapp    microsoft.insights/alertrules          eastus
                    LongHttpQueue mydemodocdbwebapp        microsoft.insights/alertrules          eastus
                    ServerErrors mydemodocdbwebapp         microsoft.insights/alertrules          eastus
                    mydemodocdbwebapp-myDemoResourceGroup  microsoft.insights/autoscalesettings   eastus
                    mydemodocdbwebapp                      microsoft.insights/components          centralus
                    mydemodocdbwebapp                      Microsoft.Web/serverFarms              westus
                    mydemodocdbwebapp                      Microsoft.Web/sites                    westus

		ProvisioningState : Succeeded


5. Avant d'examiner notre exemple d'application, essayons de comprendre ce que le déploiement du modèle a effectué :

	- Une application web App Service a été créée.

	- Un compte DocumentDB a été créé.

	- Un package de déploiement web a été déployé sur l’application web App Service.

	- La configuration de l’application web a été modifiée de telle sorte que le point de terminaison DocumentDB et la clé principale primaire ont été signalés en tant que paramètres de l’application.

	- Une série de règles de surveillance par défaut a été créée.

	
6. Pour utiliser l’application, accédez simplement à l’URL de l’application web (dans l'exemple ci-dessus, l’URL serait http://mydemodocdbwebapp.azurewebsites.net)). Vous verrez l'application web suivante :

	![Exemple d’application Todo](./media/documentdb-create-documentdb-website/image2.png)

7. Continuez et créez deux tâches, puis ouvrez le [portail Azure en version préliminaire](https://portal.azure.com).

8. Choisissez de parcourir les groupes de ressources et de sélectionner le groupe de ressources que nous avons créé au cours du déploiement (dans l'exemple ci-dessus, myDemoResourceGroup).

	![Capture d’écran du portail Azure avec l’application web myDemoResourceGroup mise en surbrillance](./media/documentdb-create-documentdb-website/image3.png)
9.  Notez que la carte des ressources de l’objectif Résumé affiche toutes nos ressources connexes (compte DocumentDB, application web App Service, surveillance).

	![Capture d’écran de l’objectif Résumé](./media/documentdb-create-documentdb-website/image4.png)
10.  Cliquez sur votre compte DocumentDB et lancez l'Explorateur de requête (au bas du panneau du compte).

	![Capture d’écran des panneaux Groupe de ressources et Compte avec vignette de l’Explorateur de requête mise en surbrillance](./media/documentdb-create-documentdb-website/image8.png)

11. Exécutez la requête par défaut, « SELECT * FROM c », et examinez les résultats. Notez que la requête a récupéré la représentation JSON des éléments de tâche que vous avez créés à l'étape 7 ci-dessus. N'hésitez pas à faire des essais avec des requêtes ; par exemple, essayez d'exécuter SELECT * FROM c WHERE c.isComplete = true pour retourner tous les éléments de tâche qui ont été marqués comme terminés.


	![Capture d’écran des panneaux Explorateur de requête et Résultats affichant les résultats de requête](./media/documentdb-create-documentdb-website/image5.png)
12. N'hésitez pas à explorer l'utilisation du portail DocumentDB ou modifier l'exemple d'application Todo. Lorsque vous êtes prêt, nous allons déployer un autre modèle.
	
<a id="Build"></a>
## Étape 3 : déploiement de l’exemple de compte DocumentDB et d’application web ##

Maintenant nous allons déployer notre deuxième modèle.

> [AZURE.TIP]Ce modèle ne valide pas que le nom de l’application web et le nom du compte DocumentDB entrés ci-dessous sont a) corrects et b) disponibles. Il est vivement recommandé de vérifier la disponibilité des noms que vous souhaitez fournir avant d'exécuter le script de déploiement PowerShell.

1. Ouvrez Microsoft Azure PowerShell et accédez au dossier dans lequel vous avez téléchargé et extrait [l’exemple de création d’un compte DocumentDB et d’application web](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebSite.zip) (par exemple, C:\\DocumentDBTemplates\\CreateDocDBWebsite).


2. Nous allons exécuter le script PowerShell CreateDocDBWebsite.ps1. Le script accepte les mêmes paramètres que le premier modèle que nous avons déployé, à savoir :
	- WebsiteName : spécifie le nom de l’application web App Service utilisée pour former l’URL d’accès à l’application web (par exemple, si vous spécifiez « myotherdocumentdbwebapp », l’URL par laquelle vous accéderez à l’application web sera myotherdocumentdbwebapp.azurewebsites.net).

	- ResourceGroupName : spécifie le nom du groupe de ressources Azure à déployer. Si le groupe de ressources spécifié n'existe pas, il sera créé.

	- docDBAccountName : spécifie le nom du compte DocumentDB à créer.

	- 	location : spécifie l’emplacement Azure dans lequel créer les ressources DocumentDB et d’application web. Les valeurs valides sont Asie orientale, Asie du Sud-est, Est des États-Unis, Ouest des États-Unis, Europe du Nord, Europe de l'Ouest (notez que la valeur de l'emplacement fournie est sensible à la casse).

3. Voici un exemple de commande pour exécuter le script :

    	PS C:\DocumentDBTemplates\CreateDocDBWebSite> .\CreateDocDBWebSite.ps1 -WebSiteName "myotherdocumentdbwebapp" -ResourceGroupName "myOtherDemoResourceGroup" -docDBAccountName "myotherdocumentdbdemoaccount" -location "East US"

	> [AZURE.TIP]Notez que vous serez invité à entrer vos nom d'utilisateur et mot de passe de compte Azure dans le cadre de l'exécution du script. L'exécution complète du déploiement prendra entre 10 et 15 minutes.

4. La sortie du déploiement sera très similaire au premier exemple de modèle.
5. Avant d’ouvrir le portail Azure en version préliminaire, essayons de comprendre ce que ce déploiement de modèle a effectué :

	- Une application web App Service a été créée.

	- Un compte DocumentDB a été créé.

	- 	La configuration de l’application web a été modifiée de telle sorte que le point de terminaison Azure DocumentDB, la clé principale primaire et la clé principale secondaire ont été signalés en tant que paramètres de l’application.

	- 	Une série de règles de surveillance par défaut a été créée.

6. Nous allons ouvrir le [portail Azure en version préliminaire](https://portal.azure.com), parcourir les groupes de ressources et sélectionner le groupe de ressources que nous avons créé au cours du déploiement (dans l’exemple ci-dessus, myOtherDemoResourceGroup).
7. Dans l’objectif Résumé, cliquez sur l’application web que vous venez de déployer.

	![Capture d’écran de l’objectif Résumé avec l’application web myotherdocumentdbwebapp mise en surbrillance](./media/documentdb-create-documentdb-website/image6.png)
8. Dans le panneau de l’application web, cliquez sur **Tous les paramètres**, puis sur **Paramètres de l’application** et remarquez l’existence de paramètres d’application pour le point de terminaison DocumentDB et chacune des clés principales DocumentDB.

	![Capture d’écran des panneaux de l’application web, des paramètres et des paramètres de l’application](./media/documentdb-create-documentdb-website/image7.png)
9. N’hésitez pas à poursuivre l’exploration du portail Azure en version préliminaire ou à suivre l’un de nos [exemples](http://go.microsoft.com/fwlink/?LinkID=402386) DocumentDB pour créer votre propre application DocumentDB.

	
	
<a name="NextSteps"></a>
## Étapes suivantes

Félicitations ! Vous avez déployé DocumentDB, une application web App Service et un exemple d’application web avec les modèles Azure Resource Manager.

- Pour en savoir plus sur DocumentDB, cliquez [ici](http://azure.com/docdb).
- Pour en savoir plus sur les applications web Azure App Service, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=325362).
- Pour en savoir plus sur les modèles Azure Resource Manager, cliquez [ici](https://msdn.microsoft.com/library/azure/dn790549.aspx).


## Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714).
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre l’ancien et le nouveau portail, consultez [Références sur la navigation dans le portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715).

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement et gratuitement une application de départ temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.
 

<!---HONumber=July15_HO3-->