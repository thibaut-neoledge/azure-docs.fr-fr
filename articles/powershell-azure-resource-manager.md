<properties 
	pageTitle="Azure PowerShell avec Azure Resource Manager | Microsoft Azure" 
	description="Introduction à l’utilisation d’Azure PowerShell pour le déploiement de plusieurs ressources sous la forme d’un groupe de ressources dans Azure." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="powershell" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="02/17/2016" 
	ms.author="tomfitz"/>

# Utilisation d’Azure PowerShell avec Azure Resource Manager

> [AZURE.SELECTOR]
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [Interface de ligne de commande Azure](xplat-cli-azure-resource-manager.md)

Azure Resource Manager contribue à initier une réflexion entièrement nouvelle concernant vos ressources Azure. Au lieu de créer et gérer des ressources à un niveau individuel, commencez à imaginer une solution globale, telle qu’un blog, une galerie de photos, un portail SharePoint ou un wiki. Vous utilisez un modèle, c’est-à-dire une représentation déclarative de la solution, pour créer un groupe de ressources contenant toutes les ressources dont vous avez besoin pour prendre en charge de la solution. Vous pouvez ensuite gérer et déployer ce groupe de ressources sous la forme d’une unité logique.

Ce didacticiel vous apprend à utiliser Azure PowerShell avec Azure Resource Manager. Il vous familiarise avec le processus de création et de déploiement d’un groupe de ressources pour une application web hébergée sur Azure avec une base de données SQL, ainsi qu’avec l’ensemble des ressources nécessaires.

## Configuration requise

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

- Un compte Azure
  + Vous pouvez [ouvrir un compte Azure gratuitement](/pricing/free-trial/?WT.mc_id=A261C142F) : vous obtenez alors des crédits dont vous pouvez vous servir pour tester les services Azure payants, et même lorsqu’ils sont épuisés, vous pouvez conserver le compte et utiliser les services Azure gratuits, notamment Sites Web. Votre carte de crédit ne sera pas débitée tant que vous n'aurez pas explicitement modifié vos paramètres pour demander à l'être.
  
  + Vous pouvez [activer les avantages de l’abonnement MSDN](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) : votre abonnement MSDN vous donne droit chaque mois à des crédits dont vous pouvez vous servir pour les services Azure payants.
- Azure PowerShell 1.0. Pour plus d’informations sur cette version et la méthode d’installation, voir [Installation et configuration d’Azure PowerShell](powershell-install-configure.md).

Ce didacticiel s’adresse aux utilisateurs novices de PowerShell, mais il repose sur l’hypothèse que vous comprenez les concepts fondamentaux (modules, applets de commande et sessions).

## Ce que vous allez déployer

Dans ce didacticiel, vous allez utiliser Azure PowerShell pour déployer une application Web et une base de données SQL. Cette application Web et cette base de données SQL sont toutefois constituées de plusieurs types de ressources conçues pour fonctionner de concert. Vous allez déployer les ressources suivantes :

- un serveur SQL, pour héberger la base de données ;
- une base de données SQL, pour stocker les données ;
- des règles de pare-feu, pour permettre à l’application Web de se connecter à la base de données ;
- un plan App Service, pour définir les fonctionnalités et le coût de l’application Web ;
- un site Web, pour l’exécution de l’application Web ;
- une configuration Web, pour le stockage de la chaîne de connexion à la base de données. 

## Aide à l’utilisation des applets de commande

Pour accéder à l'aide détaillée de toute applet de commande présentée dans ce didacticiel, utilisez l'applet de commande Get-Help.

	Get-Help <cmdlet-name> -Detailed

Par exemple, pour obtenir de l’aide sur l’applet de commande Get-AzureRmResource, entrez :

	Get-Help Get-AzureRmResource -Detailed

Pour obtenir une liste des applets de commande dans le module Ressources avec un résumé de la rubrique d’aide, entrez :

    PS C:\> Get-Command -Module AzureRM.Resources | Get-Help | Format-Table Name, Synopsis

Le résultat ressemble à l’extrait qui suit :

	Name                                   Synopsis
	----                                   --------
	Find-AzureRmResource                   Searches for resources using the specified parameters.
	Find-AzureRmResourceGroup              Searches for resource group using the specified parameters.
	Get-AzureRmADGroup                     Filters active directory groups.
	Get-AzureRmADGroupMember               Get a group members.
	...

Pour obtenir une aide complète sur une applet de commande, entrez une commande avec la syntaxe suivante :

	Get-Help <cmdlet-name> -Full
  
## Connexion à votre compte Azure

Avant de travailler sur votre solution, vous devez vous connecter à votre compte.

Pour vous connecter à votre compte Azure, utilisez l’applet de commande **Login-AzureRmAccount**.

    PS C:\> Login-AzureRmAccount

Les applets de commande vous invitent à entrer les informations d’identification de connexion pour votre compte Azure. Une fois que vous êtes connecté, l’applet de commande télécharge vos paramètres de compte pour qu’ils soient reconnus par Azure PowerShell.

Un délai d'expiration est associé à ces paramètres ; il est donc nécessaire de les actualiser ponctuellement. Pour actualiser les paramètres de compte, réexécutez **Login-AzureRmAccount**.

>[AZURE.NOTE] Les modules Gestionnaire de ressources requièrent l’utilisation de l’applet Login-AzureRmAccount. L'utilisation d'un fichier de paramètres de publication est insuffisante.

## récupération des emplacements correspondant aux types de ressources

Lors du déploiement d’une ressource, vous devez spécifier l’emplacement dans lequel vous souhaitez héberger la ressource. Toutes les régions ne prennent pas en charge chaque type de ressource. Avant de déployer votre application Web et votre base de données SQL, vous devez identifier les régions qui les prennent en charge. Un groupe de ressources peut contenir des ressources situées dans différentes régions ; vous devez cependant, dans la mesure du possible, créer des ressources au même emplacement afin d’en optimiser les performances. Vous devez notamment vous assurer que votre base de données se trouve au même emplacement que l’application qui y accède.

Pour obtenir les emplacements compatibles avec chaque type de ressource, utilisez l’applet de commande **Get-AzureRmResourceProvider**. Voyons tout d’abord le résultat de cette commande :

    PS C:\> Get-AzureRmResourceProvider -ListAvailable

    ProviderNamespace               RegistrationState ResourceTypes
    -----------------               ----------------- -------------
    Microsoft.ApiManagement         Unregistered      {service, validateServiceName, checkServiceNameAvailability}
    Microsoft.AppService            Registered        {apiapps, appIdentities, gateways, deploymenttemplates...}
    Microsoft.Batch                 Registered        {batchAccounts}
    ...

La propriété ProviderNamespace représente un ensemble de types de ressources connexes. Ces espaces de noms concordent généralement avec les services que vous souhaitez créer dans Azure. Si vous souhaitez utiliser un fournisseur de ressources répertorié comme étant **Non inscrit**, vous pouvez inscrire ce fournisseur de ressources en exécutant l’applet de commande **Register-AzureRmResourceProvider** et en spécifiant l’espace de noms du fournisseur à inscrire. Il est probable que le fournisseur de ressources que vous utiliserez dans ce didacticiel soit déjà enregistré pour votre abonnement.

Pour obtenir plus d’informations sur un fournisseur, vous pouvez spécifier cet espace de noms :

    PS C:\> Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Sql

    ProviderNamespace RegistrationState ResourceTypes                                 Locations
    ----------------- ----------------- -------------                                 ---------
    Microsoft.Sql     Registered        {operations}                                  {East US 2, South Central US, Cent...
    Microsoft.Sql     Registered        {locations}                                   {East US 2, South Central US, Cent...
    Microsoft.Sql     Registered        {locations/capabilities}                      {East US 2, South Central US, Cent...
    ...

Pour limiter le résultat aux emplacements pris en charge pour un type spécifique de ressource, tel que les sites Web, utilisez la commande suivante :

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
    
La sortie doit ressembler à ceci :

    Brazil South
    East Asia
    East US
    Japan East
    Japan West
    North Central US
    North Europe
    South Central US
    West Europe
    West US
    Southeast Asia
    Central US
    East US 2

Les emplacements qui s’affichent peuvent être légèrement différents de ceux obtenus dans les résultats précédents. Les résultats peuvent varier car un administrateur de votre organisation a créé une stratégie qui limite les régions pouvant être utilisées dans votre abonnement ou parce que votre pays de résidence impose des restrictions liées aux politiques fiscales.

Nous allons exécuter la même commande pour la base de données :

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Sql).ResourceTypes | Where-Object ResourceTypeName -eq servers).Locations
    East US 2
    South Central US
    Central US
    North Central US
    West US
    East US
    East Asia
    Southeast Asia
    Japan West
    Japan East
    North Europe
    West Europe
    Brazil South

Il semble que ces ressources soient disponibles dans plusieurs régions. Pour cette rubrique, nous utiliserons l’**ouest des États-Unis**, mais vous pouvez spécifier n’importe quelle région prise en charge.

## Créer un groupe de ressources

Cette section du didacticiel vous accompagne tout au long du processus de création d’un groupe de ressources. Le groupe de ressources servira de conteneur pour toutes les ressources de votre solution qui partagent le même cycle de vie. Dans la suite de ce didacticiel, vous allez déployer l’application Web et la base de données SQL associées à ce groupe de ressources.

Pour créer un groupe de ressources, utilisez l’applet de commande **New-AzureRmResourceGroup**.

La commande utilise le paramètre **Name** pour attribuer un nom au groupe de ressources et le paramètre **Location** pour indiquer son emplacement. Compte tenu de ce que nous avons effectué dans la section précédente, nous allons utiliser « l’ouest des États-Unis » comme emplacement.

    PS C:\> New-AzureRmResourceGroup -Name TestRG1 -Location "West US"
    
    ResourceGroupName : TestRG1
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *

    ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1

Votre groupe de ressources a bien été créé.


## Obtenir les versions disponibles de l’API pour les ressources

Lorsque vous déployez un modèle, vous devez spécifier une version de l’API à utiliser pour créer la ressource. Les versions disponibles de l’API correspondent aux versions des opérations de l’API REST publiées par le fournisseur de ressources. À mesure que les fournisseurs de ressources activent de nouvelles fonctionnalités, de nouvelles versions de l’API REST sont publiées. Par conséquent, la version de l’API que vous spécifiez dans votre modèle affecte les propriétés disponibles lorsque vous créez le modèle. En général, vous devez sélectionner la version de l’API la plus récente lorsque vous créez de nouveaux modèles. Pour les modèles existants, vous pouvez choisir soit de continuer d’utiliser une version de l’API dont vous savez qu’elle n’affectera pas votre déploiement, soit de mettre à jour votre modèle à la dernière version afin de tirer parti des nouvelles fonctionnalités.

Si cette étape peut vous sembler déroutante, l’identification des versions de l’API compatibles avec votre ressource ne présente aucune difficulté. Vous devez utiliser à nouveau la commande **Get-AzureRmResourceProvider**.

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
    2015-08-01
    2015-07-01
    2015-06-01
    2015-05-01
    2015-04-01
    2015-02-01
    2014-11-01
    2014-06-01
    2014-04-01-preview
    2014-04-01

Comme vous pouvez le voir, cette API a été fréquemment mise à jour. En règle générale, les mêmes numéros de version de l’API sont disponibles pour toutes les ressources associées à un fournisseur de ressources, sauf si une ressource a été ajoutée ou supprimée à un moment donné. Nous partirons ici du principe que les mêmes versions de l’API sont disponibles pour la ressource serverFarms. Vous pouvez toutefois effectuer une vérification pour n’importe quelle ressource qui pourrait, selon vous, être associée à une autre liste de versions disponibles de l’API.

Pour la base de données, vous obtiendrez le résultat suivant :

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Sql).ResourceTypes | Where-Object ResourceTypeName -eq servers/databases).ApiVersions
    2014-04-01-preview
    2014-04-01 

## Créer votre modèle

Cette rubrique n’explique pas comment créer votre modèle, de même qu’elle n’aborde pas la structure du modèle. Pour obtenir ces informations, consultez l’article [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md). Vous trouverez ci-dessous le modèle que vous allez déployer. Notez que le modèle utilise les versions de l’API que vous avez récupérées dans la section précédente. Pour vérifier que toutes les ressources se trouvent dans la même région, nous utilisons l’expression de modèle **resourceGroup().location** pour utiliser l’emplacement du groupe de ressources.

Notez également la section des paramètres. Cette section définit les valeurs que vous pouvez renseigner lors du déploiement des ressources. Vous aurez besoin de ces valeurs dans la suite de ce didacticiel.

Vous pouvez copier le modèle et l’enregistrer en local en tant que fichier .json. Dans ce didacticiel, nous supposerons qu’il a été enregistré dans c:\\Azure\\Templates\\azuredeploy.json, mais vous pouvez l’enregistrer à n’importe quel emplacement et lui affecter le nom de votre choix.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "hostingPlanName": {
                "type": "string"
            },
            "serverName": {
                "type": "string"
            },
            "databaseName": {
                "type": "string"
            },
            "administratorLogin": {
                "type": "string"
            },
            "administratorLoginPassword": {
                "type": "securestring"
            }
        },
        "variables": {
            "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
        },
        "resources": [
            {
                "name": "[parameters('serverName')]",
                "type": "Microsoft.Sql/servers",
                "location": "[resourceGroup().location]",
                "apiVersion": "2014-04-01",
                "properties": {
                    "administratorLogin": "[parameters('administratorLogin')]",
                    "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
                    "version": "12.0"
                },
                "resources": [
                    {
                        "name": "[parameters('databaseName')]",
                        "type": "databases",
                        "location": "[resourceGroup().location]",
                        "apiVersion": "2014-04-01",
                        "dependsOn": [
                            "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
                        ],
                        "properties": {
                            "edition": "Basic",
                            "collation": "SQL_Latin1_General_CP1_CI_AS",
                            "maxSizeBytes": "1073741824",
                            "requestedServiceObjectiveName": "Basic"
                        }
                    },
                    {
                        "name": "AllowAllWindowsAzureIps",
                        "type": "firewallrules",
                        "location": "[resourceGroup().location]",
                        "apiVersion": "2014-04-01",
                        "dependsOn": [
                            "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
                        ],
                        "properties": {
                            "endIpAddress": "0.0.0.0",
                            "startIpAddress": "0.0.0.0"
                        }
                    }
                ]
            },
            {
                "apiVersion": "2015-08-01",
                "type": "Microsoft.Web/serverfarms",
                "name": "[parameters('hostingPlanName')]",
                "location": "[resourceGroup().location]",
                "sku": {
                    "tier": "Free",
                    "name": "f1",
                    "capacity": 0
                },
                "properties": {
                    "numberOfWorkers": 1
                }
            },
            {
                "apiVersion": "2015-08-01",
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
                "tags": {
                    "team": "webdev"
                },
                "dependsOn": [
                    "[concat('Microsoft.Web/serverFarms/', parameters('hostingPlanName'))]"
                ],
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                },
                "resources": [
                    {
                        "name": "web",
                        "type": "config",
                        "apiVersion": "2015-08-01",
                        "dependsOn": [
                            "[concat('Microsoft.Web/Sites/', variables('siteName'))]"
                        ],
                        "properties": {
                            "connectionStrings": [
                                {
                                    "ConnectionString": "[concat('Data Source=tcp:', reference(concat('Microsoft.Sql/servers/', parameters('serverName'))).fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('databaseName'), ';User Id=', parameters('administratorLogin'), '@', parameters('serverName'), ';Password=', parameters('administratorLoginPassword'), ';')]",
                                    "Name": "DefaultConnection",
                                    "Type": 2
                                }
                            ]
                        }
                    }
                ]
            }
        ]
    }


## Déployer le modèle

À présent que vous disposez de votre groupe de ressources et de votre modèle, vous êtes maintenant prêt à déployer sur le groupe de ressources l’infrastructure définie dans votre modèle. Pour déployer les ressources, utilisez l’applet de commande **New-AzureRmResourceGroupDeployment**. La syntaxe de base ressemble à ce qui suit :

    PS C:\> New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -TemplateFile c:\Azure\Templates\azuredeploy.json

Vous devez spécifier le groupe de ressources et l’emplacement du modèle. Si votre modèle n’est pas local, vous pouvez utiliser le paramètre **-TemplateUri** et spécifier un URI pour le modèle. Vous pouvez affecter au paramètre **-Mode** la valeur **Incremental** ou **Complete**. Par défaut, Resource Manager effectue une mise à jour incrémentielle pendant le déploiement. Il n’est donc pas obligatoire de définir **-Mode** avec la valeur **Incremental** si vous souhaitez effectuer une mise à jour incrémentielle. Pour comprendre les différences entre ces modes de déploiement, consultez [Déployer une application avec le modèle Azure Resource Manager](resource-group-template-deploy.md).

###Paramètres de modèle dynamiques

Si vous êtes familiarisé avec PowerShell, vous savez que vous pouvez parcourir les paramètres disponibles pour une applet de commande en utilisant le signe moins (-) puis en appuyant sur la touche TAB. Cette fonctionnalité fonctionne également avec les paramètres que vous définissez dans votre modèle. Dès que vous entrez le nom du modèle, l’applet de commande récupère le modèle, l’analyse et ajoute les paramètres du modèle à la commande de façon dynamique. Il est ainsi beaucoup plus facile de spécifier les valeurs des paramètres du modèle. En outre, si vous oubliez une valeur obligatoire, PowerShell vous invite à spécifier cette valeur.

Voici l’intégralité de la commande contenant les paramètres. Vous pouvez renseigner vos propres valeurs pour les noms des ressources.

    PS C:\> New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -TemplateFile c:\Azure\Templates\azuredeploy.json -hostingPlanName freeplanwest -serverName exampleserver -databaseName exampledata -administratorLogin exampleadmin

Au moment d'entrer la commande, vous êtes invité à saisir le paramètre obligatoire manquant, **administratorLoginPassword**. Puis, lorsque vous tapez le mot de passe, la valeur de la chaîne sécurisée est masquée. Cette stratégie évite le risque de fournir un mot de passe en clair.

    cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
    Supply values for the following parameters:
    (Type !? for Help.)
    administratorLoginPassword: ********

Si le modèle inclut un paramètre avec un nom qui correspondant à l’un des paramètres de la commande pour déployer le modèle (par exemple, en incluant un paramètre nommé **ResourceGroupName** dans votre modèle, qui est le même que le paramètre **ResourceGroupName** dans l’applet de commande [New-AzureRmResourceGroupDeployment](https://msdn.microsoft.com/library/azure/mt679003.aspx)), vous serez invité à fournir une valeur pour un paramètre avec le suffixe **FromTemplate** (tel que **ResourceGroupNameFromTemplate**). En général, vous devez éviter cette confusion en ne nommant pas les paramètres avec le même nom que les paramètres utilisés pour les opérations de déploiement.

La commande s’exécute et renvoie des messages au fur et à mesure que les ressources sont créées. Vous obtenez à la fin le résultat de votre déploiement.

    DeploymentName    : azuredeploy
    ResourceGroupName : TestRG1
    ProvisioningState : Succeeded
    Timestamp         : 10/16/2015 12:55:50 AM
    Mode              : Incremental
    TemplateLink      :
    Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    hostingPlanName  String                     freeplanwest
                    serverName       String                     exampleserver
                    databaseName     String                     exampledata
                    administratorLogin  String                  exampleadmin
                    administratorLoginPassword  SecureString

    Outputs           :

Il nous a suffi de quelques étapes seulement pour créer et déployer les ressources requises pour un site web complexe.

## Obtenir des informations sur vos groupes de ressources

Après avoir créé un groupe de ressources, vous pouvez utiliser les applets de commande du module Gestionnaire de ressources pour gérer vos groupes de ressources.

- Pour obtenir tous les groupes de ressources de votre abonnement, utilisez l’applet de commande **Get-AzureRmResourceGroup** :

		PS C:\> Get-AzureRmResourceGroup

		ResourceGroupName : TestRG1
		Location          : westus
		ProvisioningState : Succeeded
		Tags              :
		ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG
		
		...

      Si vous souhaitez simplement obtenir un groupe de ressources particulier, fournissez le paramètre **Name**.
      
          PS C:\> Get-AzureRmResourceGroup -Name TestRG1

- Pour obtenir les ressources du groupe, utilisez l’applet de commande **Find-AzureRmResource** et son paramètre **ResourceGroupNameContains**. Si vous ne spécifiez pas de paramètres, Find-AzureRmResource récupère l’ensemble des ressources de votre abonnement Azure.

        PS C:\> Find-AzureRmResource -ResourceGroupNameContains TestRG1
		
        Name              : exampleserver
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Sql/servers/tfserver10
        ResourceName      : exampleserver
        ResourceType      : Microsoft.Sql/servers
        Kind              : v12.0
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
                
        ...
	        
- Le modèle ci-dessus inclut une balise sur une ressource. Vous pouvez utiliser des balises pour organiser logiquement les ressources dans votre abonnement. Utilisez les commandes **Find-AzureRmResource** et **Find-AzureRmResourceGroup** pour interroger vos ressources selon les balises.

        PS C:\> Find-AzureRmResource -TagName team

        Name              : ExampleSiteuxq53xiz5etmq
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Web/sites/ExampleSiteuxq53xiz5etmq
        ResourceName      : ExampleSiteuxq53xiz5etmq
        ResourceType      : Microsoft.Web/sites
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
                
      Vous pouvez faire bien d’autres choses avec les balises. Pour plus d’informations, voir [Organisation des ressources Azure à l’aide de balises](resource-group-using-tags.md).

## Ajouter une ressource à un groupe de ressources

Pour ajouter une ressource au groupe de ressources, utilisez l’applet de commande **New-AzureRmResource**. Mais une telle méthode risque de créer une confusion par la suite, car la nouvelle ressource n’existe pas dans votre modèle. Si vous avez redéployé l’ancien modèle, vous allez déployer une solution incomplète. Si vous effectuez fréquemment des déploiements, il vous semblera à la fois plus facile et plus fiable d’ajouter la nouvelle ressource à votre modèle avant de redéployer ce dernier.

## Déplacer une ressource

Vous pouvez déplacer des ressources existantes vers un nouveau groupe de ressources. Pour obtenir des exemples, consultez [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](resource-group-move-resources.md).

## Supprimer un groupe de ressources

- Pour supprimer une ressource du groupe de ressources, utilisez l’applet de commande **Remove-AzureRmResource**. Cette applet de commande supprime la ressource, mais pas le groupe de ressources.

	Cette commande supprime le site web TestSite du groupe de ressources TestRG1.

		Remove-AzureRmResource -Name TestSite -ResourceGroupName TestRG1 -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01

- Pour supprimer un groupe de ressources, utilisez l’applet de commande **Remove-AzureRmResourceGroup**. Cette applet de commande supprime le groupe de ressources et ses ressources.

		PS C:\> Remove-AzureRmResourceGroup -Name TestRG1
		
		Confirm
		Are you sure you want to remove resource group 'TestRG1'
		[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y



## Étapes suivantes

- Pour en savoir plus sur la création de modèles Resource Manager, consultez [Création de modèles Azure Resource Manager](./resource-group-authoring-templates.md).
- Pour savoir comment déployer des modèles, consultez [Déployer une application avec un modèle Azure Resource Manager](./resource-group-template-deploy.md).
- Pour obtenir un exemple détaillé de déploiement d’un projet, consultez [Déployer des microservices de manière prévisible dans Azure](app-service-web/app-service-deploy-complex-application-predictably.md).
- Pour résoudre les problèmes liés à l’échec d’un déploiement, consultez [Résolution des problèmes liés aux déploiements de groupes de ressources dans Azure](./resource-manager-troubleshoot-deployments-powershell.md).

<!---HONumber=AcomDC_0330_2016-->