<properties 
	pageTitle="Créer un package d&#39;application API" 
	description="Découvrez comment créer un package d&#39;application API" 
	services="app-service\api" 
	documentationCenter=".net"
	authors="guangyang"
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="guayan"/>

# Créer un package d'application API

## Vue d'ensemble

Cet article montre comment créer un package d'application API que vous pourrez publier sur [Azure Marketplace](http://azure.microsoft.com/marketplace/api-apps/).

- Pour savoir comment créer une application API, consultez [Créer une application API à l'aide de Visual Studio](app-service-dotnet-create-api-app.md).
- Pour savoir comment publier un package d'application API sur Azure Marketplace, consultez [Publier un package d'application API sur Azure Marketplace](app-service-api-publish-package).

## Structure des dossiers

Un package NuGet \(fichier \*.nupkg\*\) pour une application API a les fichiers et dossiers suivants sous le dossier *Content* :

    apiapp.json
    Metadata
        apiDefinition.swagger.json
        icons
            <icon files>
        screenshots
            <screenshot files>
        deploymentTemplates
            <template files>
        UIDefinition.json
    <other artifacts like source code, binary, etc.>

Un fichier *.nupkg* est créé avec cette structure de dossiers quand vous créez un package pour un projet Visual Studio qui a *apiapp.json* et *Metadata* dans le dossier du projet, comme dans l'illustration suivante :

![apiapp.JSON et les métadonnées dans l'Explorateur de solutions](./media/app-service-api-create-package/metadatainse.png)

Les sections suivantes décrivent chaque fichier et chaque dossier de la structure des dossiers de l'application API, avant de montrer comment créer un package du projet et comment le déployer.

## apiapp.json

Il s'agit du fichier manifeste de votre application API.

|Nom \(en gras = requis\)|Type|Format|Description|
|:---------------------|:-----|:-------|:------------|
|**id**|string|[a-zA-Z0-9\_.]|ID de ce package. Doit être unique dans un espace de noms et peut contenir seulement des caractères alphanumériques, « \_ » et « . ». Doit commencer par un caractère alphanumérique.|
|**namespace**|string|nom de domaine|Espace de noms qui, avec la propriété **id**, identifie de façon univoque l'application API. Doit être un des noms de domaine du client AAD de l'éditeur.|
|**version**|string|[semver](http://docs.nuget.org/Create/Versioning)|Version de ce package. Quand la mise à niveau automatique est activée par les utilisateurs pour ce package, elle s'applique seulement aux nouvelles versions de la même version principale.|
|**gateway**|string|2015-01-14|Version de l'API de passerelle utilisée par ce package. Une passerelle est une application web spéciale à travers laquelle sont routées toutes les demandes adressées aux applications API d'un groupe de ressources. Une de ses fonctions principales consiste à gérer l'authentification. Actuellement, la seule version de passerelle est 2015-01-14. À l'avenir, quand de nouvelles versions de passerelles seront disponibles, cette propriété vous donnera la possibilité d'éviter les modifications avec rupture et de continuer à utiliser l'API de passerelle précédente.| 
|**title**|string||Nom complet affiché de l'application API.|
|**summary**|string|100 caractères au maximum|Bref résumé de l'application API.
|description|string|1 500 caractères au maximum|Description complète de l'application API. Peut contenir du HTML. Les éléments et les attributs autorisés sont « h1 », « h2 », « h3 », « h4 », « h5 », « p », « ol », « ul », « li », « a[target\|href] », « br », « strong », « em », « b », « i ».|
|**author**|string|256 caractères au maximum|Le ou les auteurs de l'application API.|
|homepage|string|URL|Page d'accueil de l'application API.|
|endpoints|objet||Une liste de points de terminaison que la plateforme de l'application API peut interroger pour obtenir des informations sur les méthodes et les états de l'application API.|
|endpoints.apiDefinition|string|Chemin d'accès de l'URL|URL relative d'une API exposée par l'application API, qui retourne une définition d'API Swagger 2.0 sur une demande GET \(par exemple, « /swagger/docs/v1 »\). Si ce paramètre est défini, il sera utilisé à la place du fichier apiDefinition.swagger.json statique dans le package, s'il en existe un.|
|endpoints.status|string|Chemin d'accès de l'URL|URL relative d'une API exposée par l'application API, qui retourne des informations d'état de l'exécution à propos de l'application API sur une demande GET.|
|categories|string[]|community, social, enterprise, integration, protocol, app-datasvc, other|Catégorie Azure Marketplace dans laquelle apparaît ce package d'application API. Par défaut, l'application API apparaît toujours dans la catégorie community. Une fois l'application API approuvée, elle apparaît dans la catégorie spécifiée.|
|license|objet||Licence de l'application API.|
|**license.type**|string||Identificateur de licence SPDX, par exemple MIT.|
|license.url|string|URL|URL absolue pointant vers le texte complet de la licence.|
|license.requireAcceptance|valeur booléenne|true, false|Indique si une licence doit être acceptée avant l'installation. Valeur par défaut : false.|
|links|object[]||Tableau de liens à ajouter à la page Marketplace.|
|**links.text**|string||Texte du lien.|
|**links.url**|string|URL|URL absolue du lien.|
|authentication|object[]||Tableau qui indique le type d'authentification nécessaire à cette application API pour effectuer des appels d'API sortants. Actuellement, une seule authentification est prise en charge par package d'application API.|
|**authentication.type**|string|Box, DropBox, Facebook, Google, Office365, OneDrive, Quickbooks, Salesforce, SharePointOnline, Twitter, Yammer|Authentification requise par l'application API. Actuellement, 11 types d'authentification sont pris en charge. D'autres seront ajoutés par la suite.| 
|authentication.scopes|string[]||Tableau des portées qui sont spécifiques au type d'authentification.|
|copyright|string|Mention de copyright de l'application API.
|brandColor|string|N'importe quel format compatible avec CSS|Couleur personnalisée facultative pour l'expérience de l'interface utilisateur. Par exemple, le concepteur d'applications logiques utilise cette propriété pour dessiner la couleur d'arrière-plan de l'en-tête de la carte.|

## metadata/apiDefinition.swagger.json

Si vous le souhaitez, vous pouvez fournir ici un fichier JSON Swagger 2.0 pour exposer la définition de l'API de votre application API. La plateforme vérifie d'abord si la propriété **endpoints.apiDefinition** est configurée dans **apiapp.json**. Si oui, elle obtient la définition de l'API à partir de l'URL spécifiée dans la propriété. Si ce n'est pas le cas, elle tente de trouver ce fichier.

- Pour plus d'informations sur la norme Swagger 2.0, consultez [http://swagger.io/](http://swagger.io/). \<!--todo fournir des URL
- Pour plus d'informations sur la façon de personnaliser la définition de l'API pour l'optimiser pour des applications logiques, consultez [titre du document]().
- Pour plus d'informations sur la façon d'exposer une définition d'API dynamique, consultez [titre du document](). --\>

## metadata/icons

Si vous le souhaitez, vous pouvez fournir votre propre jeu d'icônes pour votre package d'application API. Si les fichiers d'icône requis ne sont pas fournis, une icône par défaut est utilisée, comme illustré ci-dessous.

![Grande icône d'application API par défaut](./media/app-service-api-create-package/api-app-default-large-icon.png)

|Fichier |Largeur|Hauteur|Description|
|:--------------------|:----|:-----|:----------|
|metadata/icons/small.png|40 px|40 px|Obligatoire si vous voulez utiliser vos propres icônes.|
|metadata/icons/medium.png|90 px|90 px|Obligatoire si vous voulez utiliser vos propres icônes.|
|metadata/icons/large.png|115 px|115 px|Obligatoire si vous voulez utiliser vos propres icônes.|
|metadata/icons/wide.png|255 px|115 px|Obligatoire si vous voulez utiliser vos propres icônes.|
|metadata/icons/hero.png|815 px|290 px|Facultatif si vous voulez utiliser vos propres icônes.|

## metadata/screenshots

Si vous le souhaitez, vous pouvez fournir jusqu'à 5 captures d'écran pour votre package d'application API.

|Fichier|Largeur|Hauteur|Description|
|:--------------------|:----|:-----|:----------|
|metadata/screenshots/\*.png|533 px|324 px|Captures d'écran de votre package d'application API.|

## metadata/deploymentTemplates

Parfois, un package d'application API nécessite une configuration personnalisée lors du déploiement. Par exemple, le [connecteur d'objets blob Azure Storage](http://azure.microsoft.com/marketplace/partners/microsoft_com/azurestorageblobconnector/) requiert l'URI du conteneur d'objets blob Azure Storage. Si vous le souhaitez, vous pouvez également configurer une clé d'accès.

Pour prendre en charge ce scénario, vous pouvez ajouter une liste de fichiers JSON de modèles ARM \(Azure Resource Manager\) dans ce dossier pour personnaliser le déploiement d'applications API. La plateforme d'applications API fusionne vos modèles ARM avec notre modèle système pour produire un modèle final pour le déploiement. Tous les paramètres définis dans vos modèles ARM personnalisés \(attendus pour **$system**\) seront également demandés automatiquement dans le panneau **Créer** du portail Azure en version préliminaire, pour que les utilisateurs de votre application API puissent entrer les valeurs.

Voici un exemple de modèle ARM montrant comment demander une URI de conteneur d'objets blob et une clé d'accès lors du déploiement d'une application API.

    {
      "$schema": "http://schemas.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "$system": {
          "type": "object"
        },
        "BlobConnector_ContainerUrl": {
          "type": "string"
        },
        "BlobConnector_AccessKey": {
          "type": "securestring"
        }
      },
      "resources": [
        {
          "apiVersion": "2015-04-01",
          "type": "Microsoft.Web/sites",
          "name": "[parameters('$system').siteName]",
          "location": "[parameters('$system').location]",
          "properties": {
            "siteConfig": {
              "appSettings": [
                {
                  "name": "BlobConnector_ContainerUrl",
                  "value": "[parameters('BlobConnector_ContainerUrl')]"
                },
                {
                  "name": "BlobConnector_AccessKey",
                  "value": "[parameters('BlobConnector_AccessKey')]"
                }
              ]
            }
          }
        }
      ]
    }

Le panneau Créer du portail Azure en version préliminaire correspondant est illustré dans la capture d'écran ci-dessous. \(La capture d'écran montre un package d'application API utilisant UIDefinition.json pour améliorer le panneau de création.\) Pour plus d'informations, consultez [metadata/UIDefinition.json](#metadata-uidefinition-json).

![Exemple de panneau de création de modèle ARM personnalisé](./media/app-service-api-create-package/custom-arm-template-create-blade-example.png)

Pour plus d'informations, consultez [Langage des modèles du gestionnaire de ressources Azure](https://msdn.microsoft.com/library/dn835138).

## metadata/UIDefinition.json

Quand vous avez besoin d'utiliser des modèles ARM personnalisés pour spécifier une configuration personnalisée nécessaire au déploiement d'une application API, le panneau **Créer** du portail Azure en version préliminaire demande automatiquement les paramètres du modèle ARM, afin que les utilisateurs de votre application API puissent entrer les valeurs. Vous pouvez utiliser le fichier *UIDefinition.json* pour améliorer cette interface utilisateur du panneau **Créer** en fournissant des valeurs par défaut, des info-bulles, des validations, etc.

Pour fournir un *fichier UIDefinition.json*, démarrez avec la structure suivante, puis suivez le tableau ci-dessous pour configurer chaque paramètre.


    {
      "parameters": {
        "<your API app package id>": {
            "<name of the ARM template parameter>": { },
            "<name of the ARM template parameter>": { }
        }
      }
    }

|Nom |Type|Description|
|:---------------------|:-------|:------------|
|defaultValue|string|Valeur par défaut du contrôle d'entrée dans le panneau Créer du portail Azure en version préliminaire.|
|displayName|string|Libellé du contrôle d'entrée dans le panneau Créer du portail Azure en version préliminaire. Ce libellé doit être court.|
|description|string|Description du contrôle d'entrée.|
|tooltip|string|Info-bulle du contrôle d'entrée dans le panneau Créer du portail Azure en version préliminaire. Elle apparaît quand les utilisateurs cliquent sur la bulle d'informations à droite du libellé. Ce texte peut être long et complet.|
|constraints|Object|Contraintes qui sont vérifiées pour le paramètre.|
|constraints.required|valeur booléenne|Indique si le paramètre est obligatoire ou non. La valeur par défaut est false.|

Par exemple, voici le fichier *UIDefinition.json* pour le [connecteur d'objets blob Azure Storage](http://azure.microsoft.com/marketplace/partners/microsoft_com/azurestorageblobconnector/) :

    {
      "parameters": {
        "AzureStorageBlobConnector": {
          "BlobConnector_ContainerUrl": {
            "defaultValue": "",
            "displayName": "Container/SAS URI",
            "description": "Specify the URI of the Blob Container. The URI may also include the SAS token. Example http://storageaccountname.blob.core.windows.net/containername or http://storageaccountname.blob.core.windows.net/containername?sr=c&si=mypolicy&sig=signatureblah",
            "tooltip": "Specify the URI of the Blob Container. The URI may also include the SAS token. Example http://storageaccountname.blob.core.windows.net/containername or http://storageaccountname.blob.core.windows.net/containername?sr=c&si=mypolicy&sig=signatureblah",
            "constraints": {
              "required": "true"
            }
          },
          "BlobConnector_AccessKey": {
            "defaultValue": "",
            "displayName": "Access Key",
            "description": "Specify a valid primary/secondary storage account access key. Leave this field empty if you are using SAS token for authentication.",
            "tooltip": "Specify a valid primary/secondary storage account access key. Leave this field empty if you are using SAS token for authentication.",
            "constraints": {
              "required": "false"
            }
          }
        }
      }
    }

Ceci configure le panneau **Créer** du portail Azure en version préliminaire pour qu'il affiche le panneau **Paramètres du package** suivant.

![Exemple de panneau de création de UIDefinition](./media/app-service-api-create-package/uidefinition-create-blade-example.png)

<!--todo add when ready to document status URI
A URI to a web service Get method that returns a value that indicates the API app's current status. If you provide this URI, the portal will show the API app's current operational status along with other information about the API app, for example:  running, nearing quota, SSL certificate expiring, etc. The format of the JSON the portal expects to receive is shown below, following the end of this table. If you don't provide an endpoints.status URI, the portal shows the Azure platform status as the API app's status.
Here is an example that shows the expected format of the JSON response from the Get method that `endpoints.status` points to:
		{
		  "status":[{ 
		    "name":"Nearing Quota", 
		    "message":"One or more quotas is nearing their limit", 
		    "level":"Warning" 
		  }, { 
		    "name":"Expiring Certificate", 
		    "message":"The SSL Certificate associated with this website is about to expire", 
		    "level":"Error"
		  }, {
		    "name":"Running", 
		    "message":"The website is handling traffic", 
		    "level":"Info"
		  }]
		}
The `name` property is a short description of the status, `message` is a longer description, and `level` can be "Error", "Warning", or "Info" for normal status.
-->

## Créer un package d'application API

Pour créer un package d'application API

1. [Télécharger et installer l'interface de ligne de commande multiplateforme Azure](xplat-cli)
2. Passez en mode Gestionnaire de ressources Azure en exécutant la commande suivante

        azure config mode arm

3. Créez un package NuGet pour votre application API en exécutant la commande suivante

        azure apiapp package create -p <package folder> -o <destination folder>

    Par exemple :

        azure apiapp package create -p c:\ContactList\ContactList -o c:\ContactListPackage

Cela permet de valider le contenu du package pour s'assurer que :

- Il suit le format décrit ci-dessus.
- Metadata\\apiDefinition.swagger.json \(s'il est fourni\) contient une définition d'API Swagger 2.0 valide.

S'il y a un problème, il en affiche les détails pour vous permettre de le résoudre et de créer un package d'application API valide.

## Étapes suivantes

Votre package d'application API est maintenant prêt à être publié sur Azure Marketplace. Pour savoir comment, consultez le didacticiel [Publier un package d'application API sur Azure Marketplace](app-service-api-publish-package).

<!--HONumber=52-->
