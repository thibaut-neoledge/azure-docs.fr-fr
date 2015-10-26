<properties
	pageTitle="Interface de ligne de commande Azure et API Apps | Microsoft Azure"
	description="Comment utiliser l’interface de ligne de commande Microsoft Azure pour Mac, Linux et Windows avec Azure API Apps."
	editor="jimbe"
	manager="wpickett"
	documentationCenter=""
	authors="tdykstra"
	services="app-service\api"/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/13/2015"
	ms.author="tdykstra"/>

# Interface de ligne de commande (CLI) Azure et applications API

Cet article vous indique comment créer, gérer et supprimer des applications API dans Azure App Service, à l'aide de l'Interface de ligne de commande Azure (CLI) pour Mac, Linux et Windows.

## Composants requis

Cet article part du principe que vous avez installé l’interface de ligne de commande Azure et que vous savez comment effectuer des tâches de base. Pour une présentation de l'interface de ligne de commande, consultez la page [Installation et configuration de l'interface de ligne de commande Azure](../xplat-cli-install.md).

> [AZURE.NOTE]Les instructions décrites dans la rubrique [Connexion à un abonnement Azure](../xplat-cli-connect.md) vous offrent deux possibilités : vous connecter à l'aide d'un compte professionnel ou scolaire ou télécharger un fichier *.publishsettings*. Pour les applications API, la méthode d'authentification de fichiers *.publishsettings* ne fonctionnera pas. Vous devez utiliser le mode Resource Management (présenté dans la section suivante) pour exécuter des applications API, et la méthode d’authentification de fichiers *.publishsettings* ne fonctionne pas avec le Gestionnaire de ressources.

## Activer le mode Resource Management

L’interface de ligne de commande peut être utilisée dans le mode [Service Management (asm)](../virtual-machines/virtual-machines-command-line-tools.md) ou le mode [Resource Management (arm)](../xplat-cli-azure-resource-manager.md). Pour les applications API, vous devez utiliser le mode Resource Management. Le mode `arm` n’étant pas activé par défaut, utilisez la commande `config mode arm` pour l'activer.

	azure config mode arm

## Répertorier les commandes disponibles pour l'utilisation d’applications API

Pour afficher toutes les commandes actuellement disponibles pour l'utilisation d’applications API, exécutez la commande `apiapp`.

	azure apiapp

## Répertorier toutes les applications API dans un abonnement ou un groupe de ressources

Pour répertorier toutes les applications API dans votre abonnement, exécutez la commande `apiapp list` sans paramètres.

	azure apiapp list

La liste affiche le groupe de ressources, le nom de l'application API, l’ID du package et l’URL de chaque application API.

	info:    Executing command apiapp list
	info:    Listing ApiApps
	data:    Resource Group  Name           Package Id        Url                                                                       
	data:    --------------  -------------  ----------------  --------------------------------------------------------------------------
	data:    mygroup         SimpleDropbox  Microsoft.ApiApp  https://microsoft-apiappf1bbba377c6d4aa1f03146cadd6.azurewebsites.net
	info:    apiapp list command OK

Pour limiter la liste à un groupe de ressources spécifié, ajoutez le paramètre de groupe (`-g`).

	azure apiapp list -g <resource group name>

Par exemple :

	azure apiapp list -g mygroup

Pour ajouter à la liste des informations sur le niveau d’accès et la version d’application API, ajoutez les paramètres de détails (`-d`).

	azure apiapp list -d

Le résultat `list` avec les champs supplémentaires ressemble à ceci :

	info:    Executing command apiapp list
	info:    Listing ApiApps
	data:    Resource Group  Name           Package Id     Version  Auth                 Url                                                                       
	data:    --------------  -------------  -------------  -------  -------------------  --------------------------------------------------------------------------
	data:    mygroup         SimpleDropbox  SimpleDropbox  1.0.0    PublicAuthenticated  https://microsoft-apiappf1bbba377cbd2a3aa1f03146c6.azurewebsites.net
	info:    apiapp list command OK

### Répertorier les détails relatifs à une application API

Pour répertorier les détails d’une application API, utilisez la commande `apiapp show` avec les paramètres de groupe (`-g`) et de nom d'application API (`-n`).

	azure apiapp show -g <resource group name> -n <API app name>

Par exemple :

	azure apiapp show -g mygroup -n SimpleDropbox

Le résultat se présente ainsi :

	info:    Executing command apiapp show
	info:    Getting ApiApp
	data:    Name:              SimpleDropbox
	data:    Location:          West US
	data:    Resource Group:    mygroup
	data:    Package Id:        SimpleDropbox
	data:    Package Version:   1.0.0
	data:    Update Policy:     Disabled
	data:    Access Level:      PublicAuthenticated
	data:    Hosting site name: microsoft-apiappf1bbba377c6d4bd2a36cadd6
	data:    Gateway name:      SD1aeb4ae60b7cb4f3d966dfa43b6607f30
	info:    apiapp show command OK

## Créer une application API

Il existe deux façons de créer une application API. Vous pouvez utiliser des commandes de l’interface de ligne de commande pour créer des ressources Azure de manière individuelle, ou bien utiliser la syntaxe déclarative dans un modèle pour définir toutes les ressources requises et déployer ce modèle avec une commande de l’interface de ligne de commande. Pour l'approche déclarative, consultez les [étapes suivantes](#next-steps).

Pour créer une application API à l'aide de l'approche impérative, procédez comme suit :

1. Choisir un emplacement valide
1. Créer ou rechercher un groupe de ressources à utiliser
2. Créer ou rechercher un plan App Service à utiliser
4. Créer l’application API

### Choisir un emplacement valide

Lorsque vous créez un groupe de ressources, vous devez spécifier un emplacement. Voici quelques emplacements valides pour les applications API.

* Est des États-Unis
* Ouest des États-Unis
* Sud du centre des États-Unis
* Europe du Nord
* Asie de l'Est
* Est du Japon
* Europe de l'Ouest
* Asie du Sud-Est
* Ouest du Japon
* Nord du centre des États-Unis
* Centre des États-Unis
* Sud du Brésil
* Est des États-Unis 2

Pour obtenir une liste complète et actualisée des emplacements, utilisez la commande `location list` et consultez la ligne de fournisseur de ressources `Microsoft.AppService/apiapps`.

	azure location list

Le résultat de la commande `location list` ressemble à ceci.

	info:    Executing command location list
	info:    Getting Resource Providers
	data:    Name                                                                Location                                                                                                                                                   
	data:    ------------------------------------------------------------------  -----------------------------------------------------------------------------------------------------------------------------------------------------------
	data:    Microsoft.ApiManagement/service                                     East US,North Central US,South Central US,West US,North Europe,West Europe,East Asia,Southeast Asia,Japan East,Japan West,Brazil South                     
	data:    Microsoft.AppService/apiapps                                        East US,West US,South Central US,North Europe,East Asia,Japan East,West Europe,Southeast Asia,Japan West,North Central US,Central US,Brazil South,East US 2
	data:    Microsoft.AppService/appIdentities                                  East US,West US,South Central US,North Europe,East Asia,Japan East,West Europe,Southeast Asia,Japan West,North Central US,Central US,Brazil South,East US 2
	info:    location list command OK

### Créer ou rechercher un groupe de ressources à utiliser

Pour créer un groupe de ressources, utilisez la commande `group create` avec les paramètres de nom (`n`) et d'emplacement (`l`).

	azure group create -n <name> -l <location>

Par exemple :

	azure group create -n "mygroup" -l "West US"

Pour rechercher un groupe de ressources existant, utilisez la commande `group list`, puis choisissez un groupe de ressources dans un emplacement valide pour les applications API.

	azure group list

### Créer ou rechercher un plan App Service à utiliser

Pour créer un plan App Service, utilisez la commande `resource create` et le paramètre de type de ressources (`-r`) pour spécifier que le type de ressources que vous souhaitez créer est un plan App Service.

	azure resource create -g <resource group> -n <app service plan name> -r "Microsoft.Web/serverfarms" -l <location> -o <api version> -p "{"sku": {"tier": "<pricing tier>"}, "numberOfWorkers" : <number of workers>, "workerSize": "<worker size>"}"
	
Par exemple :

	azure resource create -g mygroup -n myplan -r "Microsoft.Web/serverfarms" -l "West US" -o "2015-06-01" -p "{"sku": {"tier": "Standard"}, "numberOfWorkers" : 1, "workerSize": "Small"}"

La chaîne JSON du paramètre `properties` (`-p`) est requise, en raison de certaines modifications qui ont été apportées récemment à l'API REST. À l'avenir, le paramètre `-p` sera facultatif.

L’exemple de commande spécifie la dernière version de l’API, au moment où l’article a été écrit. Pour vérifier si une version plus récente est disponible, utilisez la commande `provider show` et consultez le tableau `apiVersions` pour l’objet `sites` dans le tableau `resourceTypes`.

	azure provider show -n Microsoft.Web --json
   
Voici un exemple de l’objet `sites` dans la sortie de commande.

	{
	  "resourceTypes": [
	    {
	      "apiVersions": [
	        "2015-06-01",
	        "2015-05-01",
	        "2015-04-01",
	        "2014-04-01"
	      ],
	      "locations": [
	        "East Asia",
	        "East US",
	        "Japan East",
	        "Japan West",
	        "North Europe",
	        "West Europe",
	        "West US",
	        "Southeast Asia",
	        "Central US",
	        "East US 2"
	      ],
	      "properties": {},
	      "name": "sites"
	    }

Pour répertorier les plans App Service existants, utilisez la commande `resource list` et spécifiez le plan App Service de l'application comme type de ressources à l'aide du paramètre `-r`.

	azure resource list -r Microsoft.Web/serverfarms

Le résultat se présente ainsi :

	info:    Executing command resource list
	info:    Listing resources
	data:    Id                                                                                                                                           Name             Resource Group          Type                       Parent  Location  Tags
	data:    -------------------------------------------------------------------------------------------------------------------------------------------  ---------------  ----------------------  -------------------------  ------  --------  ----
	data:    /subscriptions/aeb4ae60-b7cb-4f3d-966d-fa43b6607f30/resourceGroups/ContosoAdsGroup/providers/Microsoft.Web/serverFarms/ContosoAdsPlan        ContosoAdsPlan   ContosoAdsGroup         Microsoft.Web/serverFarms          westus    null
	info:    resource list command OK

### Création d’une application API (personnalisée) vide

Pour créer une application API vide (pour laquelle vous devrez fournir le code vous-même), utilisez la commande `apiapp create` et spécifiez le package Nuget `Microsoft.ApiApp` (paramètre `-u`).

	azure apiapp create -g <resource group name> -n <API app name> -p <app service plan name> -u Microsoft.ApiApp

Par exemple :

	azure apiapp create -g mygroup -n newapiapp -p myplan -u Microsoft.ApiApp

Le résultat signale l’avancement de la création de l'application API.

	info:    Executing command apiapp create
	info:    Checking resource group and app service plan
	info:    Getting package metadata
	info:    Creating deployment
	info:    Waiting for deployment completion
	info:    Deployment started:
	data:    Subscription Id: aeb4ae60-b7cb-4f3d-966d-fa43b6607f30
	data:    Resource Group:  mygroup
	data:    Deployment Name: AppServiceDeployment_f67fa710-d565-43cf-8c9c-
	                          d515dd2eb903
	data:    Correlation Id:  a7894287-c585-46d5-96a4-feac4b2f48c6
	data:    Timestamp:       2015-07-21T23:20:12.8858274Z
	data:    
	data:    Operation           State         Status        Resource                                          
	data:    ------------------- ------------- ------------- --------------------------------------------------
	data:    E8D88DA720375394    Succeeded     OK            /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.Web/sites/Microsoft-ApiApp33056917d6e34238b2606d71caf73b6a
	data:    FC31834D2E73D10E    Succeeded     Created       /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.Web/sites/Microsoft-ApiApp33056917d6e34238b2606d71caf73b6a/providers/Microsoft.Resources/links/apiApp
	data:    C5B48DAF91306BB4    Succeeded     OK            /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.Web/sites/Microsoft-ApiApp33056917d6e34238b2606d71caf73b6a/siteextensions/Microsoft.ApiApp
	data:    F4B943428026A1B2    Succeeded     Created       /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.AppService/apiapps/newapiapp
	data:    4B3A935892415369    Succeeded     Created       /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.AppService/apiapps/newapiapp/providers/Microsoft.Resources/links/apiAppSite
	info:    Deployment complete with status: Succeeded
	info:    apiapp create command OK

#### Créer une application API à partir de Marketplace

Pour obtenir une liste des packages d'application API disponibles sur Marketplace, utilisez la commande `group template list` et spécifiez les applications API à l'aide du paramètre de catégorie (`-c`).

	azure group template list -c apiapps

Le résultat ressemble à ceci :

	info:    Executing command group template list
	info:    Listing gallery resource group templates
	data:    Publisher      Name                                                  
	data:    -------------  ------------------------------------------------------
	data:    Microsoft      Microsoft.Template.1.0.1                              
	data:    microsoft_com  microsoft_com.MicrosoftSharePointConnector.0.2.2      
	data:    microsoft_com  microsoft_com.FTPConnector.0.2.2                      
	info:    group template list command OK

Pour créer une application API à partir de Marketplace, utilisez la commande `apiapap create` et spécifiez le nom de l'application API que vous souhaitez créer en utilisant le paramètre du package NuGet (`-u`).

	azure apiapp create -g <resource group name> -n <API app name> -p <app service plan name> -u <marketplace name>

La valeur à utiliser pour le paramètre `-u` est la partie centrale du nom de Marketplace. Par exemple, pour microsoft\_com.MicrosoftSqlConnector.0.2.2, la commande ressemble à ceci :

	azure apiapp create -g mygroup -n mysqlconnector -p myplan -u MicrosoftSqlConnector
	
Si des paramètres de modèle d'application API sont requis, tels que les noms de serveur et les chaînes de connexion pour un connecteur SQL, vous serez invité à fournir les données requises. Vous avez la possibilité d'utiliser `--parameters` ou `--parameters-file` pour transmettre les valeurs de paramètre. Pour plus d'informations sur les fichiers de paramètres, consultez la rubrique [Provisionner une application API avec une nouvelle passerelle](app-service-api-arm-new-gateway-provision.md).

## Étapes suivantes

Cet article vous a montré comment exécuter les commandes individuelles de l’interface de ligne de commande Azure pour utiliser les applications API personnalisées ou les applications API que vous créez à partir de modèles Marketplace. Pour plus d'informations sur l'utilisation des modèles personnalisés permettant d’automatiser la création d'applications API, consultez les ressources suivantes :

* [Provisionner une application API avec une passerelle existante](app-service-api-arm-existing-gateway-provision.md)
* [Provisionner une application API avec une nouvelle passerelle](app-service-api-arm-new-gateway-provision.md)

Pour plus d'informations sur l'utilisation des utilitaires de ligne de commande Azure avec Azure Resource Manager, consultez les ressources suivantes :
 
* [Utilisation de l'interface de ligne de commande Azure pour Mac, Linux et Windows avec Azure Resource Management](../xplat-cli-azure-resource-manager.md).
* [Utilisation d’Azure PowerShell avec Azure Resource Manager](../powershell-azure-resource-manager.md)
 

<!---HONumber=Oct15_HO3-->