---
title: Création de modèles Azure Resource Manager | Microsoft Docs
description: Créez des modèles Azure Resource Manager à l'aide de la syntaxe JSON déclarative pour déployer des applications sur Azure.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn

ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/19/2016
ms.author: tomfitz

---
# Création de modèles Azure Resource Manager
Cette rubrique décrit la structure d’un modèle Azure Resource Manager. Elle présente les différentes sections d’un modèle et les propriétés disponibles dans ces sections. Le modèle se compose de JSON et d'expressions que vous pouvez utiliser pour construire des valeurs pour votre déploiement.

Pour obtenir des conseils sur la création d'un modèle, consultez [Guide de création d’un modèle Resource Manager](resource-manager-template-walkthrough.md). Pour des recommandations sur la création de modèles, voir [Bonnes pratiques relatives à la création de modèles Azure Resource Manager](resource-manager-template-best-practices.md).

Un bon éditeur JSON peut simplifier la tâche de création de modèles. Pour plus d’informations sur l’utilisation de Visual Studio avec vos modèles, consultez [Création et déploiement des groupes de ressources Azure via Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md). Pour plus d’informations sur l’utilisation de VS Code, consultez [Utiliser des modèles Azure Resource Manager dans Visual Studio Code](resource-manager-vs-code.md).

Vous devez limiter la taille de votre modèle à 1 Mo et celle de chaque fichier de paramètres à 64 Ko. La limite de 1 Mo s'applique à l'état final du modèle une fois développé avec les définitions des ressources itératives et les valeurs des variables et des paramètres.

## Format de modèle
Dans sa structure la plus simple, un modèle contient les éléments suivants.

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

| Nom de l'élément | Requis | Description |
|:---:|:---:|:--- |
| $schema |Oui |Emplacement du fichier de schéma JSON qui décrit la version du langage du modèle. Vous devez utiliser l’URL affichée ci-dessus. |
| contentVersion |Oui |Version du modèle (par exemple, 1.0.0.0). Vous pouvez fournir n’importe quelle valeur pour cet élément. Quand vous déployez des ressources à l'aide du modèle, cette valeur permet de vous assurer que le bon modèle est utilisé. |
| parameters |Non |Valeurs fournies lors de l'exécution du déploiement pour personnaliser le déploiement des ressources. |
| variables |Non |Valeurs utilisées en tant que fragments JSON dans le modèle pour simplifier les expressions du langage du modèle. |
| les ressources |Oui |Types de ressource déployés ou mis à jour dans un groupe de ressources. |
| outputs |Non |Valeurs retournées après le déploiement. |

Nous allons examiner les sections du modèle de manière plus approfondie plus loin dans cette rubrique. Pour l'instant, nous allons nous intéresser à la syntaxe qui constitue le modèle.

## Expressions et fonctions
La syntaxe de base du modèle est JSON. Toutefois, des expressions et fonctions étendent la syntaxe JSON disponible dans le modèle et vous permettent de créer des valeurs qui ne sont pas des valeurs littérales strictes. Les expressions sont placées entre crochets [ et ] et sont évaluées au moment où le modèle est déployé. Les expressions peuvent apparaître n'importe où dans une valeur de chaîne JSON et retournent toujours une autre valeur JSON. Si vous avez besoin d'utiliser une chaîne littérale qui commence par un crochet [, vous devez utiliser deux crochets [[.

En général, vous utilisez des expressions avec des fonctions pour effectuer des opérations de configuration du déploiement. Comme en JavaScript, les appels de fonctions sont formatés ainsi : **functionName(arg1,arg2,arg3)**. Pour référencer des propriétés, vous utilisez les opérateurs point et [index].

L'exemple suivant vous indique comment utiliser plusieurs fonctions lors de la construction de valeurs :

    "variables": {
       "location": "[resourceGroup().location]",
       "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
       "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
    }

Pour obtenir la liste complète des fonctions de modèle, consultez [Fonctions des modèles Azure Resource Manager](resource-group-template-functions.md).

## Paramètres
C’est dans la section des paramètres du modèle que vous pouvez spécifier les valeurs que vous pouvez saisir lors du déploiement des ressources. Ces valeurs de paramètre vous permettent de personnaliser le déploiement grâce à des valeurs adaptées à un environnement particulier (par exemple développement, test et production). Il est inutile de fournir des paramètres dans votre modèle, mais sans les paramètres, votre modèle déploie toujours les mêmes ressources avec les mêmes noms, emplacements et propriétés.

Vous pouvez utiliser ces valeurs de paramètre dans tout le modèle pour définir les valeurs des ressources déployées. Seuls les paramètres déclarés dans la section des paramètres peuvent être utilisés dans d'autres sections du modèle.

Vous définissez des paramètres avec la structure suivante :

    "parameters": {
       "<parameterName>" : {
         "type" : "<type-of-parameter-value>",
         "defaultValue": "<optional-default-value-of-parameter>",
         "allowedValues": [ "<optional-array-of-allowed-values>" ],
         "minValue": <optional-minimum-value-for-int-parameters>,
         "maxValue": <optional-maximum-value-for-int-parameters>,
         "minLength": <optional-minimum-length-for-string-secureString-array-parameters>,
         "maxLength": <optional-maximum-length-for-string-secureString-array-parameters>,
         "metadata": {
             "description": "<optional-description-of-the parameter>" 
         }
       }
    }

| Nom de l'élément | Requis | Description |
|:---:|:---:|:--- |
| nom\_paramètre |Oui |Nom du paramètre. Doit être un identificateur JavaScript valide. |
| type |Oui |Type de la valeur du paramètre. Consultez la liste ci-dessous pour découvrir les types autorisés. |
| defaultValue |Non |Valeur par défaut du paramètre, si aucune valeur n'est fournie pour le paramètre. |
| allowedValues |Non |Tableau des valeurs autorisées pour le paramètre afin de vous assurer que la bonne valeur a bien été fournie. |
| minValue |Non |Valeur minimale pour les paramètres de type int, cette valeur est inclusive. |
| maxValue |Non |Valeur maximale pour les paramètres de type int. Cette valeur est inclusive. |
| minLength |Non |Valeur minimale pour les paramètres de type string, secureString et array. Cette valeur est inclusive. |
| maxLength |Non |Valeur maximale pour les paramètres de type string, secureString et array. Cette valeur est inclusive. |
| description |Non |Description du paramètre qui sera affiché pour les utilisateurs du modèle par le biais de l'interface de modèle personnalisé du portail. |

Les valeurs et types autorisés sont :

* string ou secureString - n'importe quelle chaîne JSON valide
* int - n’importe quel integer JSON valide
* bool - n’importe quel booléen JSON valide
* object ou secureObject - n'importe quel objet JSON valide
* array - n'importe quel tableau JSON valide

Pour spécifier un paramètre comme facultatif, fournissez une valeur defaultValue (peut être une chaîne vide).

Si vous indiquez un nom de paramètre correspondant à l’un des paramètres de la commande pour déployer le modèle (par exemple, en incluant un paramètre nommé **ResourceGroupName** dans votre modèle, qui est le même que le paramètre **ResourceGroupName** dans l’applet de commande [New-AzureRmResourceGroupDeployment][deployment2cmdlet]), vous serez invité à fournir une valeur de paramètre avec le suffixe **FromTemplate** (par exemple, **ResourceGroupNameFromTemplate**). En général, vous devez éviter cette confusion en ne nommant pas les paramètres avec un nom identique à celui des paramètres utilisés pour les opérations de déploiement.

> [!NOTE]
> Tous les mots de passe, clés et autres secrets doivent utiliser le type **secureString**. Il est impossible de lire les paramètres du modèle dont le type est secureString après le déploiement de la ressource.
> 
> 

L’exemple suivant vous indique comment définir les paramètres :

    "parameters": {
      "siteName": {
        "type": "string",
        "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]"
      },
      "hostingPlanName": {
        "type": "string",
        "defaultValue": "[concat(parameters('siteName'),'-plan')]"
      },
      "skuName": {
        "type": "string",
        "defaultValue": "F1",
        "allowedValues": [
          "F1",
          "D1",
          "B1",
          "B2",
          "B3",
          "S1",
          "S2",
          "S3",
          "P1",
          "P2",
          "P3",
          "P4"
        ]
      },
      "skuCapacity": {
        "type": "int",
        "defaultValue": 1,
        "minValue": 1
      }
    }

Pour plus d’informations sur la saisie des valeurs de paramètre au cours du déploiement, consultez [Déployer une application avec un modèle Azure Resource Manager](resource-group-template-deploy.md#parameter-file).

## Variables
Dans la section des variables, vous définissez des valeurs pouvant être utilisées dans votre modèle. En règle générale, ces variables se basent sur des valeurs fournies à partir des paramètres. Il est inutile de définir des variables, mais elles simplifient souvent votre modèle en réduisant les expressions complexes.

Vous définissez des variables avec la structure suivante :

    "variables": {
       "<variable-name>": "<variable-value>",
       "<variable-name>": { 
           <variable-complex-type-value> 
       }
    }

L'exemple suivant vous indique comment définir une variable qui est construite à partir de deux valeurs de paramètre :

     "variables": {
       "connectionString": "[concat('Name=', parameters('username'), ';Password=', parameters('password'))]"
    }

L'exemple suivant vous indique une variable qui est un type complexe de JSON et des variables qui sont construites à partir d'autres variables :

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
       "instancesSize": "[variables('currentEnvironmentSettings').instancesSize]",
       "instancesCount": "[variables('currentEnvironmentSettings').instancesCount]"
    }

## Ressources
Dans la section des ressources, vous définissez les ressources déployées ou mises à jour. C’est à ce stade que votre modèle peut gagner en complexité, car vous devez connaître les types que vous déployez pour fournir les valeurs adéquates. Pour apprendre ce que vous devez savoir sur les fournisseurs de ressources, consultez [Fournisseurs, régions, schémas et versions d’API Resource Manager](resource-manager-supported-services.md).

Vous définissez des ressources avec la structure suivante :

    "resources": [
       {
         "apiVersion": "<api-version-of-resource>",
         "type": "<resource-provider-namespace/resource-type-name>",
         "name": "<name-of-the-resource>",
         "location": "<location-of-resource>",
         "tags": "<name-value-pairs-for-resource-tagging>",
         "comments": "<your-reference-notes>",
         "dependsOn": [
           "<array-of-related-resource-names>"
         ],
         "properties": "<settings-for-the-resource>",
         "resources": [
           "<array-of-child-resources>"
         ]
       }
    ]

| Nom de l'élément | Requis | Description |
|:---:|:---:|:--- |
| apiVersion |Oui |La version de l'API REST à utiliser pour la création de la ressource. Pour déterminer les numéros de version disponibles pour un type de ressources particulier, consultez les [versions d’API prises en charge](resource-manager-supported-services.md#supported-api-versions). |
| type |Oui |Type de la ressource. Cette valeur est une combinaison de l'espace de noms du fournisseur de ressources et du type de ressource qu'il prend en charge. |
| name |Oui |Nom de la ressource. Le nom doit respecter les restrictions de composant d'URI définies dans le document RFC3986. En outre, les services Azure qui exposent le nom de la ressource à des parties externes valident du nom pour s’assurer qu’il ne s’agit pas d’une tentative d’usurpation d’identité. Consultez [Vérifiez le nom de ressource](https://msdn.microsoft.com/library/azure/mt219035.aspx). |
| location |Varie |Emplacements géographiques de la ressource fournie pris en charge. Pour déterminer les emplacements disponibles, consultez [Régions prises en charge](resource-manager-supported-services.md#supported-regions). La plupart des types de ressources nécessitent un emplacement, mais certains types (par exemple, une affectation de rôle) ne nécessitent aucun emplacement. |
| tags |Non |Balises associées à la ressource. |
| commentaires |Non |Vos commentaires pour documenter les ressources dans votre modèle |
| dependsOn |Non |Ressources desquelles dépend la ressource qui est définie. Les dépendances entre les ressources sont évaluées et les ressources sont déployées dans leur ordre dépendant. Quand les ressources ne dépendent les unes des autres, leur déploiement en parallèle est tenté. La valeur peut être une liste séparée par des virgules de noms de ressource ou d'identificateurs de ressource uniques. |
| properties |Non |Paramètres de configuration spécifiques aux ressources. Les valeurs de propriétés sont identiques à celles que vous fournissez dans le corps de la requête pour l’opération d’API REST (méthode PUT) pour créer la ressource. Pour accéder à la documentation du schéma de ressources ou l’API REST, consultez [Fournisseurs du Gestionnaire de ressources, régions, versions d’API et schémas](resource-manager-supported-services.md). |
| les ressources |Non |Ressources enfants qui dépendent de la ressource qui est définie. Vous pouvez fournir uniquement des types de ressources qui sont autorisés par le schéma de la ressource parente. Le nom qualifié complet du type de ressource enfant inclut le type de ressource parente, tel que **Microsoft.Web/sites/extensions**. La dépendance à la ressource parent n’est pas induite ; vous devez la définir explicitement cette dépendance. |

Si le nom de la ressource n'est pas unique, vous pouvez utiliser la fonction d'assistance **resourceId** (décrite ci-dessous) pour obtenir l'identificateur unique de la ressource.

La section des ressources contient un tableau des ressources à déployer. Au sein de chaque ressource, vous pouvez également définir un tableau de ressources enfant correspondant à ces ressources. Par conséquent, la structure de la section de ressources peut ressembler à ce qui suit :

    "resources": [
       {
           "name": "resourceA",
           ...
       },
       {
           "name": "resourceB",
           ...
           "resources": [
               {
                   "name": "firstChildResourceB",
                   ...
               },
               {   
                   "name": "secondChildResourceB",
                   ...
               }
           ]
       },
       {
           "name": "resourceC",
           ...
       }
    ]



L’exemple suivant représente une ressource **Microsoft.Web/serverfarms** et une ressource **Microsoft.Web/sites** avec une ressource enfant **Extensions**. Notez que le site est marqué comme dépendant de la batterie de serveurs, car cette dernière doit être en place avant que le site puisse être déployé. Notez également que la ressource **Extensions** est une ressource enfant du site.

    "resources": [
      {
        "apiVersion": "2015-08-01",
        "name": "[parameters('hostingPlanName')]",
        "type": "Microsoft.Web/serverfarms",
        "location": "[resourceGroup().location]",
        "tags": {
          "displayName": "HostingPlan"
        },
        "sku": {
          "name": "[parameters('skuName')]",
          "capacity": "[parameters('skuCapacity')]"
        },
        "properties": {
          "name": "[parameters('hostingPlanName')]",
          "numberOfWorkers": 1
        }
      },
      {
        "apiVersion": "2015-08-01",
        "type": "Microsoft.Web/sites",
        "name": "[parameters('siteName')]",
        "location": "[resourceGroup().location]",
        "tags": {
          "environment": "test",
          "team": "ARM"
        },
        "dependsOn": [
          "[concat('Microsoft.Web/serverFarms/', parameters('hostingPlanName'))]"
        ],
        "properties": {
          "name": "[parameters('siteName')]",
          "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
        },
        "resources": [
          {
            "apiVersion": "2015-08-01",
            "type": "extensions",
            "name": "MSDeploy",
            "dependsOn": [
              "[concat('Microsoft.Web/sites/', parameters('siteName'))]"
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
    ]


## Sorties
Dans la section des sorties, vous spécifiez des valeurs retournées à partir du déploiement. Par exemple, vous pouvez retourner l'URI d'accès à une ressource déployée.

L'exemple suivant illustre la structure de la définition d'une sortie :

    "outputs": {
       "<outputName>" : {
         "type" : "<type-of-output-value>",
         "value": "<output-value-expression>"
       }
    }

| Nom de l'élément | Requis | Description |
|:---:|:---:|:--- |
| outputName |Oui |Nom de la valeur de sortie. Doit être un identificateur JavaScript valide. |
| type |Oui |Type de la valeur de sortie. Les valeurs de sortie prennent en charge les mêmes types que les paramètres d'entrée du modèle. |
| value |Oui |Expression du langage du modèle évaluée et retournée sous forme de valeur de sortie. |

L'exemple suivant montre une valeur retournée dans la section des sorties.

    "outputs": {
       "siteUri" : {
         "type" : "string",
         "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
       }
    }

Pour plus d’informations sur le fonctionnement de la sortie, consultez [Partage d’état dans les modèles Azure Resource Manager](best-practices-resource-manager-state.md).

## Étapes suivantes
* Pour afficher des modèles complets pour de nombreux types de solutions, consultez [Modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/).
* Pour plus d’informations sur les fonctions que vous pouvez utiliser dans un modèle, consultez [Fonctions des modèles Azure Resource Manager](resource-group-template-functions.md).
* Pour combiner plusieurs modèles lors du déploiement, consultez [Utilisation de modèles liés avec Azure Resource Manager](resource-group-linked-templates.md).
* Pour itérer un nombre de fois spécifié lors de la création d'un type de ressource, consultez [Création de plusieurs instances de ressources dans Azure Resource Manager](resource-group-create-multiple.md).
* Vous devrez peut-être utiliser des ressources qui existent au sein d'un groupe de ressources différent. Cette situation est classique quand vous utilisez des comptes de stockage ou des réseaux virtuels qui sont partagés entre plusieurs groupes de ressources. Pour plus d'informations, consultez la [fonction resourceId](resource-group-template-functions.md#resourceid).

[deployment2cmdlet]: https://msdn.microsoft.com/library/mt740620(v=azure.200).aspx

<!---HONumber=AcomDC_0928_2016-->