---
title: "Créer des modèles pour les déploiements Azure | Microsoft Docs"
description: "Décrit la structure et les propriétés des modèles Azure Resource Manager à l’aide de la syntaxe JSON déclarative."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 19694cb4-d9ed-499a-a2cc-bcfc4922d7f5
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: a75863878a97c1202e9b9946b0bff19261952c21
ms.lasthandoff: 03/22/2017


---
# <a name="authoring-azure-resource-manager-templates"></a>Création de modèles Azure Resource Manager
Cette rubrique décrit la structure d’un modèle Azure Resource Manager. Elle présente les différentes sections d’un modèle et les propriétés disponibles dans ces sections. Le modèle se compose d’un JSON et d’expressions que vous pouvez utiliser pour construire des valeurs pour votre déploiement. 

Pour afficher le modèle pour les ressources que vous avez déjà déployées, consultez [Exporter un modèle Azure Resource Manager à partir de ressources existantes](resource-manager-export-template.md). Pour obtenir des conseils sur la création d'un modèle, consultez [Guide de création d’un modèle Resource Manager](resource-manager-template-walkthrough.md). Pour des recommandations sur la création de modèles, voir [Bonnes pratiques relatives à la création de modèles Azure Resource Manager](resource-manager-template-best-practices.md).

Un bon éditeur JSON peut simplifier la tâche de création de modèles. Pour plus d’informations sur l’utilisation de Visual Studio avec vos modèles, consultez [Création et déploiement des groupes de ressources Azure via Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md). Pour plus d’informations sur l’utilisation de VS Code, consultez [Utiliser des modèles Azure Resource Manager dans Visual Studio Code](resource-manager-vs-code.md).

Limitez la taille de votre modèle à 1 Mo et celle de chaque fichier de paramètres à 64 Ko. La limite de 1 Mo s’applique à l’état final du modèle une fois développé avec les définitions des ressources itératives et les valeurs des variables et des paramètres. 

## <a name="template-format"></a>Format de modèle
Dans sa structure la plus simple, un modèle contient les éléments suivants :

```json
{
   "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "",
   "parameters": {  },
   "variables": {  },
   "resources": [  ],
   "outputs": {  }
}
```

| Nom de l'élément | Requis | Description |
|:--- |:--- |:--- |
| $schema |Oui |Emplacement du fichier de schéma JSON qui décrit la version du langage du modèle. Utilisez l’URL indiquée dans l’exemple précédent. |
| contentVersion |Oui |Version du modèle (par exemple, 1.0.0.0). Vous pouvez fournir n’importe quelle valeur pour cet élément. Quand vous déployez des ressources à l'aide du modèle, cette valeur permet de vous assurer que le bon modèle est utilisé. |
| parameters |Non |Valeurs fournies lors de l'exécution du déploiement pour personnaliser le déploiement des ressources. |
| variables |Non |Valeurs utilisées en tant que fragments JSON dans le modèle pour simplifier les expressions du langage du modèle. |
| les ressources |Oui |Types de ressource déployés ou mis à jour dans un groupe de ressources. |
| outputs |Non |Valeurs retournées après le déploiement. |

Nous allons examiner les sections du modèle de manière plus approfondie plus loin dans cette rubrique.

## <a name="expressions-and-functions"></a>Expressions et fonctions
La syntaxe de base du modèle est JSON. Toutefois, les expressions et fonctions étendent les valeurs JSON disponibles dans le modèle.  Les expressions sont écrites dans les littéraux de chaîne JSON dont le premier et dernier caractères sont les crochets : `[` et `]`, respectivement. La valeur de l’expression est évaluée lorsque le modèle est déployé.   Bien qu’écrit sous la forme d’un littéral de chaîne, le résultat de l’évaluation de l’expression peut être d’un type différent de JSON, tel qu’un tableau ou un entier, en fonction de l’expression réelle.  Notez que pour avoir un littéral de chaîne commençant par un crochet `[`, sans qu’il soit interprété comme une expression, ajoutez un crochet supplémentaire pour démarrer la chaîne avec `[[`.

En général, vous utilisez des expressions avec des fonctions pour effectuer des opérations de configuration du déploiement. Comme en JavaScript, les appels de fonctions sont formatés ainsi : **functionName(arg1,arg2,arg3)**. Pour référencer des propriétés, vous utilisez les opérateurs point et [index].

L'exemple suivant vous indique comment utiliser plusieurs fonctions lors de la construction de valeurs :

```json
"variables": {
   "location": "[resourceGroup().location]",
   "usernameAndPassword": "[concat(parameters('username'), ':', parameters('password'))]",
   "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
}
```

Pour obtenir la liste complète des fonctions de modèle, consultez [Fonctions des modèles Azure Resource Manager](resource-group-template-functions.md). 

## <a name="parameters"></a>parameters
C’est dans la section des paramètres du modèle que vous pouvez spécifier les valeurs que vous pouvez saisir lors du déploiement des ressources. Ces valeurs de paramètre vous permettent de personnaliser le déploiement grâce à des valeurs adaptées à un environnement particulier (par exemple développement, test et production). Il est inutile de fournir des paramètres dans votre modèle, mais sans les paramètres, votre modèle déploie toujours les mêmes ressources avec les mêmes noms, emplacements et propriétés.

Vous pouvez utiliser ces valeurs de paramètre dans tout le modèle pour définir les valeurs des ressources déployées. Seuls les paramètres déclarés dans la section des paramètres peuvent être utilisés dans d'autres sections du modèle.

Vous définissez des paramètres avec la structure suivante :

```json
"parameters": {
   "<parameter-name>" : {
     "type" : "<type-of-parameter-value>",
     "defaultValue": "<default-value-of-parameter>",
     "allowedValues": [ "<array-of-allowed-values>" ],
     "minValue": <minimum-value-for-int>,
     "maxValue": <maximum-value-for-int>,
     "minLength": <minimum-length-for-string-or-array>,
     "maxLength": <maximum-length-for-string-or-array-parameters>,
     "metadata": {
         "description": "<description-of-the parameter>" 
     }
   }
}
```

| Nom de l'élément | Requis | Description |
|:--- |:--- |:--- |
| nom_paramètre |Oui |Nom du paramètre. Doit être un identificateur JavaScript valide. |
| type |Oui |Type de la valeur du paramètre. La liste des types autorisés est présentée après ce tableau. |
| defaultValue |Non |Valeur par défaut du paramètre, si aucune valeur n'est fournie pour le paramètre. |
| allowedValues |Non |Tableau des valeurs autorisées pour le paramètre afin de vous assurer que la bonne valeur a bien été fournie. |
| minValue |Non |Valeur minimale pour les paramètres de type int, cette valeur est inclusive. |
| maxValue |Non |Valeur maximale pour les paramètres de type int. Cette valeur est inclusive. |
| minLength |Non |Valeur minimale pour les paramètres de type string, secureString et array. Cette valeur est inclusive. |
| maxLength |Non |Valeur maximale pour les paramètres de type string, secureString et array. Cette valeur est inclusive. |
| Description |Non |Description du paramètre qui est affiché pour les utilisateurs du modèle par le biais de l’interface de modèle personnalisé du portail. |

Les valeurs et types autorisés sont :

* **string**
* **secureString**
* **int**
* **bool**
* **object** 
* **secureObject**
* **array**

Pour spécifier un paramètre comme facultatif, fournissez une valeur defaultValue (peut être une chaîne vide). 

Si vous spécifiez dans votre modèle un nom de paramètre qui correspond à un paramètre de la commande servant à déployer le modèle, il existe une ambiguïté potentielle concernant les valeurs que vous fournissez. Resource Manager élimine ce risque de confusion en ajoutant le suffixe **romTemplate** au paramètre du modèle. Par exemple, si vous incluez dans votre modèle un paramètre nommé **ResourceGroupName**, celui-ci est en conflit avec le paramètre **ResourceGroupName** dans l’applet de commande [New-AzureRmResourceGroupDeployment][deployment2cmdlet]. Pendant le déploiement, vous êtes invité à fournir une valeur pour **ResourceGroupNameFromTemplate**. En général, vous devez éviter cette confusion en ne nommant pas les paramètres avec un nom identique à celui des paramètres utilisés pour les opérations de déploiement.

> [!NOTE]
> Tous les mots de passe, clés et autres secrets doivent utiliser le type **secureString** . Si vous transmettez des données sensibles dans un objet JSON, utilisez le type **secureObject**. Il est impossible de lire les paramètres du modèle dont le type est secureString ou secureObject après le déploiement de la ressource. 
> 
> Par exemple, l’entrée suivante dans l’historique de déploiement indique la valeur pour une chaîne et un objet, mais pas pour secureString et secureObject.
>
> ![afficher les valeurs de déploiement](./media/resource-group-authoring-templates/show-parameters.png)  
>

L’exemple suivant vous indique comment définir les paramètres :

```json
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
```

Pour plus d’informations sur la saisie des valeurs de paramètre au cours du déploiement, consultez [Déployer une application avec un modèle Azure Resource Manager](resource-group-template-deploy.md). 

## <a name="variables"></a>variables
Dans la section des variables, vous définissez des valeurs pouvant être utilisées dans votre modèle. En règle générale, les variables se basent sur des valeurs fournies à partir des paramètres. Il est inutile de définir des variables, mais elles simplifient souvent votre modèle en réduisant les expressions complexes.

Vous définissez des variables avec la structure suivante :

```json
"variables": {
   "<variable-name>": "<variable-value>",
   "<variable-name>": { 
       <variable-complex-type-value> 
   }
}
```

L'exemple suivant vous indique comment définir une variable qui est construite à partir de deux valeurs de paramètre :

```json
"variables": {
    "connectionString": "[concat('Name=', parameters('username'), ';Password=', parameters('password'))]"
}
```

L'exemple suivant vous indique une variable qui est un type complexe de JSON et des variables qui sont construites à partir d'autres variables :

```json
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
```

## <a name="resources"></a>les ressources
Dans la section des ressources, vous définissez les ressources déployées ou mises à jour. Cette section gagne en complexité, car vous devez connaître les types que vous déployez pour fournir les valeurs adéquates. 

Vous définissez des ressources avec la structure suivante :

```json
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
     "copy": {
       "name": "<name-of-copy-loop>",
       "count": "<number-of-iterations>"
     },
     "resources": [
       "<array-of-child-resources>"
     ]
   }
]
```

| Nom de l'élément | Requis | Description |
|:--- |:--- |:--- |
| apiVersion |Oui |La version de l'API REST à utiliser pour la création de la ressource. |
| type |Oui |Type de la ressource. Cette valeur est une combinaison de l’espace de noms du fournisseur de ressources et du type de ressource (comme **Microsoft.Storage/storageAccounts**). |
| name |Oui |Nom de la ressource. Le nom doit respecter les restrictions de composant d'URI définies dans le document RFC3986. En outre, les services Azure qui exposent le nom de la ressource à des parties externes valident du nom pour s’assurer qu’il ne s’agit pas d’une tentative d’usurpation d’identité. Consultez [Vérifiez le nom de ressource](https://msdn.microsoft.com/library/azure/mt219035.aspx). |
| location |Varie |Emplacements géographiques de la ressource fournie pris en charge. Vous pouvez sélectionner l’un des emplacements disponibles, mais en général, il est judicieux de choisir celui qui est proche de vos utilisateurs. En règle générale, il est également judicieux de placer dans la même région les ressources qui interagissent entre elles. La plupart des types de ressources nécessitent un emplacement, mais certains types (par exemple, une affectation de rôle) ne nécessitent aucun emplacement. |
| tags |Non |Balises associées à la ressource. |
| commentaires |Non |Vos commentaires pour documenter les ressources dans votre modèle |
| dependsOn |Non |Les ressources qui doivent être déployées avant le déploiement de cette ressource. Resource Manager évalue les dépendances entre les ressources et les déploie dans le bon ordre. Quand les ressources ne dépendent les unes des autres, leur déploiement se fait en parallèle. La valeur peut être une liste séparée par des virgules de noms de ressource ou d’identificateurs de ressource uniques. Répertoriez uniquement les ressources qui sont déployées dans ce modèle. Les ressources qui ne sont pas définies dans ce modèle doivent déjà exister. Évitez d’ajouter des dépendances inutiles, car cela risque de ralentir votre déploiement et de créer des dépendances circulaires. Pour savoir comment définir des dépendances, consultez [Définition de dépendances dans les modèles Azure Resource Manager](resource-group-define-dependencies.md). |
| properties |Non |Paramètres de configuration spécifiques aux ressources. Les valeurs de propriétés sont identiques à celles que vous fournissez dans le corps de la requête pour l’opération d’API REST (méthode PUT) pour créer la ressource. Pour accéder à la documentation du schéma de ressources ou l’API REST, consultez [Fournisseurs, régions, schémas et versions d’API Resource Manager](resource-manager-supported-services.md). |
| copy |Non |Si plusieurs instances sont nécessaires, le nombre de ressources à créer. Pour plus d’informations, consultez [Création de plusieurs instances de ressources dans Azure Resource Manager](resource-group-create-multiple.md). |
| les ressources |Non |Ressources enfants qui dépendent de la ressource qui est définie. Fournissez uniquement des types de ressources qui sont autorisés par le schéma de la ressource parente. Le type complet de la ressource enfant inclut le type de ressource parente, par exemple **Microsoft.Web/sites/extensions**. La dépendance envers la ressource parente n’est pas induite. Vous devez la définir explicitement. |

Connaître les valeurs à spécifier pour **apiVersion**, **type** et **location** n’est pas immédiatement évident. Heureusement, vous pouvez déterminer ces valeurs via Azure PowerShell ou l’interface de ligne de commande Azure.

Pour obtenir tous les fournisseurs de ressources avec **PowerShell**, utilisez :

```powershell
Get-AzureRmResourceProvider -ListAvailable
```

Dans la liste renvoyée, recherchez les fournisseurs de ressources qui vous intéressent. Pour obtenir les types de ressources pour un fournisseur de ressources (par exemple, Stockage), utilisez :

```powershell
(Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Storage).ResourceTypes
```

Pour obtenir les versions d’API pour un type de ressource (par exemple, comptes de stockage), utilisez :

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Storage).ResourceTypes | Where-Object ResourceTypeName -eq storageAccounts).ApiVersions
```

Pour obtenir les emplacements pris en charge pour un type de ressource, utilisez :

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Storage).ResourceTypes | Where-Object ResourceTypeName -eq storageAccounts).Locations
```

Pour obtenir tous les fournisseurs de ressources avec **l’interface de ligne de commande Azure**, utilisez :

```azurecli
azure provider list
```

Dans la liste renvoyée, recherchez les fournisseurs de ressources qui vous intéressent. Pour obtenir les types de ressources pour un fournisseur de ressources (par exemple, Stockage), utilisez :

```azurecli
azure provider show Microsoft.Storage
```

Pour obtenir les emplacements et les versions d’API pris en charge, utilisez :

```azurecli
azure provider show Microsoft.Storage --details --json
```

Pour en savoir plus sur les fournisseurs de ressources, consultez [Fournisseurs, régions, versions d’API et schémas de Resource Manager](resource-manager-supported-services.md).

La section des ressources contient un tableau des ressources à déployer. Au sein de chaque ressource, vous pouvez également définir un tableau de ressources enfant. Par conséquent, la structure de la section de ressources peut ressembler à ce qui suit :

```json
"resources": [
   {
       "name": "resourceA",
   },
   {
       "name": "resourceB",
       "resources": [
           {
               "name": "firstChildResourceB",
           },
           {   
               "name": "secondChildResourceB",
           }
       ]
   },
   {
       "name": "resourceC",
   }
]
```      

L’exemple suivant représente une ressource **Microsoft.Web/serverfarms** et une ressource **Microsoft.Web/sites** avec une ressource enfant **Extensions**. Notez que le site est marqué comme dépendant de la batterie de serveurs, car cette dernière doit être en place avant que le site puisse être déployé. Notez également que la ressource **Extensions** est une ressource enfant du site.

```json
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
      "team": "Web"
    },
    "dependsOn": [
      "[concat(parameters('hostingPlanName'))]"
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
```

## <a name="outputs"></a>outputs
Dans la section des sorties, vous spécifiez des valeurs retournées à partir du déploiement. Par exemple, vous pouvez retourner l'URI d'accès à une ressource déployée.

L'exemple suivant illustre la structure de la définition d'une sortie :

```json
"outputs": {
   "<outputName>" : {
     "type" : "<type-of-output-value>",
     "value": "<output-value-expression>"
   }
}
```

| Nom de l'élément | Requis | Description |
|:--- |:--- |:--- |
| outputName |Oui |Nom de la valeur de sortie. Doit être un identificateur JavaScript valide. |
| type |Oui |Type de la valeur de sortie. Les valeurs de sortie prennent en charge les mêmes types que les paramètres d'entrée du modèle. |
| value |Oui |Expression du langage du modèle évaluée et retournée sous forme de valeur de sortie. |

L'exemple suivant montre une valeur retournée dans la section des sorties.

```json
"outputs": {
   "siteUri" : {
     "type" : "string",
     "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
   }
}
```

Pour plus d’informations sur le fonctionnement de la sortie, consultez [Partage d’état dans les modèles Azure Resource Manager](best-practices-resource-manager-state.md).

## <a name="next-steps"></a>Étapes suivantes
* Pour afficher des modèles complets pour de nombreux types de solutions, consultez [Modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/).
* Pour plus d’informations sur les fonctions que vous pouvez utiliser dans un modèle, consultez [Fonctions des modèles Azure Resource Manager](resource-group-template-functions.md).
* Pour combiner plusieurs modèles lors du déploiement, consultez [Utilisation de modèles liés avec Azure Resource Manager](resource-group-linked-templates.md).
* Vous devrez peut-être utiliser des ressources qui existent au sein d'un groupe de ressources différent. Ce scénario est classique quand vous utilisez des comptes de stockage ou des réseaux virtuels qui sont partagés entre plusieurs groupes de ressources. Pour plus d'informations, consultez la [fonction resourceId](resource-group-template-functions.md#resourceid).

[deployment2cmdlet]: https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.2.0/new-azurermresourcegroupdeployment

