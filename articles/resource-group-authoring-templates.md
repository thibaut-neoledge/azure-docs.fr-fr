<properties
   pageTitle="Création de modèles Azure Resource Manager"
	description="Créez des modèles Azure Resource Manager à l'aide de la syntaxe JSON déclarative pour déployer des applications sur Azure."
	services="azure-resource-manager"
	documentationCenter="na"
	authors="tfitzmac"
	manager="wpickett"
	editor=""/>

<tags
   ms.service="azure-resource-manager"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="08/20/2015"
	ms.author="tomfitz"/>

# Création de modèles Azure Resource Manager

Les applications Azure requièrent généralement une combinaison de ressources (comme un serveur de base de données, une base de données ou un site Web) pour atteindre les objectifs voulus. Au lieu de déployer et gérer chaque ressource séparément, vous pouvez créer un modèle Azure Resource Manager qui déploie et approvisionne toutes les ressources de votre application en une seule opération coordonnée. Dans le modèle, vous définissez les ressources nécessaires à l'application et vous spécifiez les paramètres de déploiement pour entrer des valeurs pour différents environnements. Le modèle se compose de JSON et d'expressions que vous pouvez utiliser pour construire des valeurs pour votre déploiement.

Cette rubrique décrit les sections du modèle. Pour obtenir les schémas réels, consultez la page [Schémas Azure Resource Manager](https://github.com/Azure/azure-resource-manager-schemas).

Vous devez limiter la taille de votre modèle à 1 Mo et celle de chaque fichier de paramètres à 64 Ko. La limite de 1 Mo s'applique à l'état final du modèle une fois développé avec les définitions des ressources itératives et les valeurs des variables et des paramètres.

## Format de modèle

L'exemple suivant montre les sections qui composent la structure de base d'un modèle.

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

| Nom de l'élément | Requis | Description
| :------------: | :------: | :----------
| $schema | Oui | Emplacement du fichier de schéma JSON qui décrit la version du langage du modèle.
| contentVersion | Oui | Version du modèle (par exemple, 1.0.0.0). Quand vous déployez des ressources à l'aide du modèle, cette valeur permet de vous assurer que le bon modèle est utilisé.
| parameters | Non | Valeurs fournies lors de l'exécution du déploiement pour personnaliser le déploiement des ressources.
| variables | Non | Valeurs utilisées en tant que fragments JSON dans le modèle pour simplifier les expressions du langage du modèle.
| les ressources | Oui | Types de services déployés ou mis à jour dans un groupe de ressources.
| outputs | Non | Valeurs retournées après le déploiement.

Nous allons examiner les sections du modèle de manière plus approfondie plus loin dans cette rubrique. Pour l'instant, nous allons nous intéresser à la syntaxe qui constitue le modèle.

## Expressions et fonctions

La syntaxe de base du modèle est JSON. Toutefois, des expressions et fonctions étendent la syntaxe JSON disponible dans le modèle et vous permettent de créer des valeurs qui ne sont pas des valeurs littérales strictes. Les expressions sont placées entre crochets ([ et ]) et sont évaluées au moment où le modèle est déployé. Les expressions peuvent apparaître n'importe où dans une valeur de chaîne JSON et retournent toujours une autre valeur JSON. Si vous avez besoin d'utiliser une chaîne littérale qui commence par un crochet [, vous devez utiliser deux crochets [[.

En général, vous utilisez des expressions avec des fonctions pour effectuer des opérations de configuration du déploiement. Comme en JavaScript, les appels de fonctions sont formatés ainsi : **functionName(arg1,arg2,arg3)**. Pour référencer des propriétés, vous utilisez les opérateurs point et [index].

La liste suivante vous indique les fonctions courantes.

- **parameters(parameterName)**

    Retourne une valeur de paramètre qui est fournie lors de l’exécution du déploiement.

- **variables(variableName)**

    Retourne une variable qui est définie dans le modèle.

- **concat(arg1,arg2,arg3,...)**

    Combine plusieurs valeurs de chaîne. Cette fonction peut prendre n'importe quel nombre d'arguments.

- **base64(inputString)**

    Retourne la représentation en base 64 de la chaîne d'entrée.

- **resourceGroup()**

    Retourne un objet structuré (avec ID, nom et propriétés de l’emplacement) qui représente le groupe de ressources actuel.

- **resourceId([nom\_groupe\_ressources], type\_ressource, nom\_ressource1, [nom\_ressource2]...)**

    Retourne l'identificateur unique d'une ressource. Peut être utilisé pour récupérer une ressource d'un autre groupe de ressources.

L'exemple suivant vous indique comment utiliser plusieurs fonctions lors de la construction de valeurs :
 
    "variables": {
       "location": "[resourceGroup().location]",
       "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
       "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
    }

À ce stade, vous en savez suffisamment sur les expressions et les fonctions pour comprendre les sections du modèle. Pour plus d'informations sur toutes les fonctions des modèles, y compris les paramètres et le format des valeurs renvoyées, consultez la rubrique [Fonctions des modèles Azure Resource Manager](./resource-group-template-functions.md).


## Paramètres

Dans la section des paramètres du modèle, vous spécifiez les valeurs qu'un utilisateur peut entrer lors du déploiement des ressources. Vous pouvez utiliser ces valeurs de paramètre dans tout le modèle pour définir les valeurs des ressources déployées. Seuls les paramètres déclarés dans la section des paramètres peuvent être utilisés dans d'autres sections du modèle.

Au sein de la section des paramètres, vous ne pouvez pas utiliser une valeur de paramètre pour construire une autre valeur de paramètre. Ce type d'opération se produit généralement dans la section des variables.

Vous définissez des paramètres avec la structure suivante :

    "parameters": {
       "<parameterName>" : {
         "type" : "<type-of-parameter-value>",
         "defaultValue": "<optional-default-value-of-parameter>",
         "allowedValues": [ "<optional-array-of-allowed-values>" ]
       }
    }

| Nom de l'élément | Requis | Description
| :------------: | :------: | :----------
| nom\_paramètre | Oui | Nom du paramètre. Doit être un identificateur JavaScript valide.
| type | Oui | Type de la valeur du paramètre. Consultez la liste ci-dessous pour découvrir les types autorisés.
| defaultValue | Non | Valeur par défaut du paramètre, si aucune valeur n'est fournie pour le paramètre.
| allowedValues | Non | Tableau des valeurs autorisées pour le paramètre afin de vous assurer que la bonne valeur a bien été fournie.

Les valeurs et types autorisés sont :

- string ou secureString - n'importe quelle chaîne JSON valide
- int - n’importe quel integer JSON valide
- bool - n’importe quel booléen JSON valide
- object ou secureObject - n'importe quel objet JSON valide
- array - n'importe quel tableau JSON valide


>[AZURE.NOTE]Tous les mots de passe, clés et autres secrets doivent utiliser le type **secureString**. Il est impossible de lire les paramètres du modèle dont le type est secureString après le déploiement de la ressource.

L’exemple suivant vous indique comment définir les paramètres :

    "parameters": {
       "siteName": {
          "type": "string"
       },
       "siteLocation": {
          "type": "string"
       },
       "hostingPlanName": {
          "type": "string"
       },  
       "hostingPlanSku": {
          "type": "string",
          "allowedValues": [
            "Free",
            "Shared",
            "Basic",
            "Standard",
            "Premium"
          ],
          "defaultValue": "Free"
       }
    }

## Variables

Dans la section des variables, vous construisez des valeurs qui peuvent être utilisées pour simplifier les expressions du langage du modèle. En règle générale, ces variables se basent sur des valeurs fournies à partir des paramètres.

L'exemple suivant vous indique comment définir une variable qui est construite à partir de deux valeurs de paramètre :

    "parameters": {
       "username": {
         "type": "string"
       },
       "password": {
         "type": "secureString"
       }
     },
     "variables": {
       "connectionString": "[concat('Name=', parameters('username'), ';Password=', parameters('password'))]"
    }

L'exemple suivant vous indique une variable qui est un type complexe de JSON et des variables qui sont construites à partir d'autres variables :

    "parameters": {
       "environmentName": {
         "type": "string",
         "allowedValues": [
           "test",
           "prod"
         ]
       }
    },
    "variables": {
       "environmentSettings": {
         "test": {
           "instancesSize": "Small",
           "instancesCount": 1
         },
         "prod": {
           "instancesSize": "Large",
           "instancesCount": 4
         }
       },
       "currentEnvironmentSettings": "[variables('environmentSettings')[parameters('environmentName')]]",
       "instancesSize": "[variables('currentEnvironmentSettings').instancesSize",
       "instancesCount": "[variables('currentEnvironmentSettings').instancesCount"
    }

## Ressources

Dans la section des ressources, vous définissez les ressources déployées ou mises à jour.

Vous définissez des ressources avec la structure suivante :

    "resources": [
       {
         "apiVersion": "<api-version-of-resource>",
         "type": "<resource-provider-namespace/resource-type-name>",
         "name": "<name-of-the-resource>",
         "location": "<location-of-resource>",
         "tags": "<name-value-pairs-for-resource-tagging>",
         "dependsOn": [
           "<array-of-related-resource-names>"
         ],
         "properties": "<settings-for-the-resource>",
         "resources": [
           "<array-of-dependent-resources>"
         ]
       }
    ]

| Nom de l'élément | Requis | Description
| :----------------------: | :------: | :----------
| apiVersion | Oui | Version de l'API qui prend en charge la ressource. Pour connaître les versions et les schémas disponibles pour les ressources, consultez [Schémas Azure Resource Manager](https://github.com/Azure/azure-resource-manager-schemas).
| type | Oui | Type de la ressource. Cette valeur est une combinaison de l'espace de noms du fournisseur de ressources et du type de ressource qu'il prend en charge.
| name | Oui | Nom de la ressource. Le nom doit respecter les restrictions de composant d'URI définies dans le document RFC3986.
| location | Non | Emplacements géographiques de la ressource fournie pris en charge.
| tags | Non | Balises associées à la ressource.
| dependsOn | Non | Ressources desquelles dépend la ressource qui est définie. Les dépendances entre les ressources sont évaluées et les ressources sont déployées dans leur ordre dépendant. Quand les ressources ne dépendent les unes des autres, leur déploiement en parallèle est tenté. La valeur peut être une liste séparée par des virgules de noms de ressource ou d'identificateurs de ressource uniques.
| properties | Non | Paramètres de configuration spécifiques aux ressources.
| les ressources | Non | Ressources enfants qui dépendent de la ressource qui est définie.

Si le nom de la ressource n'est pas unique, vous pouvez utiliser la fonction d'assistance **resourceId** (décrite ci-dessous) pour obtenir l'identificateur unique de la ressource.

L'exemple suivant illustre une ressource **Microsoft.Web/serverfarms** et une ressource **Microsoft.Web/sites** avec une ressource imbriquée **Extensions** :

    "resources": [
        {
          "apiVersion": "2014-06-01",
          "type": "Microsoft.Web/serverfarms",
          "name": "[parameters('hostingPlanName')]",
          "location": "[resourceGroup().location]",
          "properties": {
              "name": "[parameters('hostingPlanName')]",
              "sku": "[parameters('hostingPlanSku')]",
              "workerSize": "0",
              "numberOfWorkers": 1
          }
        },
        {
          "apiVersion": "2014-06-01",
          "type": "Microsoft.Web/sites",
          "name": "[parameters('siteName')]",
          "location": "[resourceGroup().location]",
          "tags": {
              "environment": "test",
              "team": "ARM"
          },
          "dependsOn": [
              "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
          ],
          "properties": {
              "name": "[parameters('siteName')]",
              "serverFarm": "[parameters('hostingPlanName')]"
          },
          "resources": [
              {
                  "apiVersion": "2014-06-01",
                  "type": "Extensions",
                  "name": "MSDeploy",
                  "properties": {
                    "packageUri": "https://auxmktplceprod.blob.core.windows.net/packages/StarterSite-modified.zip",
                    "dbType": "None",
                    "connectionString": "",
                    "setParameters": {
                      "Application Path": "[parameters('siteName')]"
                    }
                  }
              }
          ]
        }
    ]


## Sorties

Dans la section des sorties, vous spécifiez des valeurs retournées à partir du déploiement. Par exemple, vous pouvez retourner l'URI d'accès à une ressource déployée.

L'exemple suivant illustre la structure de la définition d'une sortie :

    "outputs": {
       "<outputName>" : {
         "type" : "<type-of-output-value>",
         "value": "<output-value-expression>",
       }
    }

| Nom de l'élément | Requis | Description
| :------------: | :------: | :----------
| outputName | Oui | Nom de la valeur de sortie. Doit être un identificateur JavaScript valide.
| type | Oui | Type de la valeur de sortie. Les valeurs de sortie prennent en charge les mêmes types que les paramètres d'entrée du modèle.
| value | Oui | Expression du langage du modèle évaluée et retournée sous forme de valeur de sortie.


L'exemple suivant montre une valeur retournée dans la section des sorties.

    "outputs": {
       "siteUri" : {
         "type" : "string",
         "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
       }
    }

## Autres scénarios avancés
Cette rubrique donne un premier aperçu du modèle. Toutefois, votre scénario peut requérir des tâches plus avancées.

Vous devrez peut-être fusionner deux modèles ou utiliser un modèle enfant au sein d'un modèle parent. Pour en savoir plus, consultez [Utilisation de modèles liés avec Azure Resource Manager](resource-group-linked-templates.md).

Pour itérer un nombre de fois spécifié lors de la création d'un type de ressource, consultez [Création de plusieurs instances de ressources dans Azure Resource Manager](resource-group-create-multiple.md).

Vous devrez peut-être utiliser des ressources qui existent au sein d'un groupe de ressources différent. Cette situation est classique quand vous utilisez des comptes de stockage ou des réseaux virtuels qui sont partagés entre plusieurs groupes de ressources. Pour plus d'informations, consultez la [fonction resourceId](../resource-group-template-functions#resourceid).

## Modèle complet
Le modèle suivant déploie une application web et l'approvisionne avec le code d'un fichier .zip.

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "siteName": {
           "type": "string"
         },
         "hostingPlanName": {
           "type": "string"
         },
         "hostingPlanSku": {
           "type": "string",
           "allowedValues": [
             "Free",
             "Shared",
             "Basic",
             "Standard",
             "Premium"
           ],
           "defaultValue": "Free"
         }
       },
       "resources": [
         {
           "apiVersion": "2014-06-01",
           "type": "Microsoft.Web/serverfarms",
           "name": "[parameters('hostingPlanName')]",
           "location": "[resourceGroup().location]",
           "properties": {
             "name": "[parameters('hostingPlanName')]",
             "sku": "[parameters('hostingPlanSku')]",
             "workerSize": "0",
             "numberOfWorkers": 1
           }
         },
         {
           "apiVersion": "2014-06-01",
           "type": "Microsoft.Web/sites",
           "name": "[parameters('siteName')]",
           "location": "[resourceGroup().location]",
           "tags": {
             "environment": "test",
             "team": "ARM"
           },
           "dependsOn": [
             "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
           ],
           "properties": {
             "name": "[parameters('siteName')]",
             "serverFarm": "[parameters('hostingPlanName')]"
           },
           "resources": [
             {
               "apiVersion": "2014-06-01",
               "type": "Extensions",
               "name": "MSDeploy",
               "dependsOn": [
                 "[resourceId('Microsoft.Web/sites', parameters('siteName'))]"
               ],
               "properties": {
                 "packageUri": "https://auxmktplceprod.blob.core.windows.net/packages/StarterSite-modified.zip",
                 "dbType": "None",
                 "connectionString": "",
                 "setParameters": {
                   "Application Path": "[parameters('siteName')]"
                 }
               }
             }
           ]
         }
       ],
       "outputs": {
         "siteUri": {
           "type": "string",
           "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
         }
       }
    }

## Étapes suivantes
- Pour plus d'informations sur les fonctions que vous pouvez utiliser dans un modèle, consultez [Fonctions des modèles Azure Resource Manager](resource-group-template-functions.md)
- Pour savoir comment déployer le modèle que vous avez créé, consultez [Déploiement d'une application avec un modèle Azure Resource Manager](azure-portal/resource-group-template-deploy.md)
- Pour obtenir un exemple détaillé de déploiement d’une application, consultez [Mise en service et déploiement de microservices de manière prévisible dans Azure](app-service-web/app-service-deploy-complex-application-predictably.md)
- Pour voir les schémas disponibles, consultez [Schémas Azure Resource Manager](https://github.com/Azure/azure-resource-manager-schemas)

<!---HONumber=August15_HO9-->