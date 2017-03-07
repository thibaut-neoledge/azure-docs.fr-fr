---
title: "Définir l’ordre de déploiement des ressources Azure | Microsoft Docs"
description: "Décrit la procédure permettant de définir une ressource comme dépendante d’une autre ressource au cours du déploiement afin de garantir le déploiement des ressources dans l&quot;ordre adéquat."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: 34ebaf1e-480c-4b4d-9bf6-251bd3f8f2cf
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 006d8e10acd6b4b756c0b78988176f71c3802080
ms.lasthandoff: 03/06/2017


---
# <a name="define-the-order-for-deploying-resources-in-azure-resource-manager-templates"></a>Définir l’ordre de déploiement des ressources dans les modèles Azure Resource Manager
Une ressource donnée peut comporter d'autres ressources qui doivent exister avant son déploiement. Par exemple, un serveur SQL doit exister avant une tentative de déploiement d'une base de données SQL. Vous définissez cette relation en marquant une seule ressource comme dépendante de l'autre ressource. Pour définir une dépendance, vous devez utiliser l’élément **dependsOn** ou la fonction **reference**. 

Resource Manager évalue les dépendances entre les ressources et les déploie dans leur ordre dépendant. Quand les ressources ne dépendent pas les unes des autres, Resource Manager les déploie en parallèle. Vous devez uniquement définir des dépendances pour les ressources qui sont déployées dans le même modèle. 

## <a name="dependson"></a>dependsOn
Dans votre modèle, l’élément dependsOn vous permet de définir une ressource comme une dépendance sur une ou plusieurs ressources. Sa valeur peut être une liste séparée par des virgules de noms de ressources. 

L’exemple suivant montre un groupe identique de machines virtuelles dépendant d’un équilibreur de charge, un réseau virtuel et une boucle qui crée plusieurs comptes de stockage. Ces autres ressources ne figurent pas dans l’exemple suivant, mais ont besoin d’exister ailleurs dans le modèle.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "[variables('namingInfix')]",
  "location": "[variables('location')]",
  "apiVersion": "2016-03-30",
  "tags": {
    "displayName": "VMScaleSet"
  },
  "dependsOn": [
    "[variables('loadBalancerName')]",
    "[variables('virtualNetworkName')]",
    "storageLoop",
  ],
  ...
}
```

Dans l’exemple précédent, une dépendance est incluse sur les ressources créées par le biais d’une boucle de copie nommée **storageLoop**. Pour obtenir un exemple, consultez [Création de plusieurs instances de ressources dans Azure Resource Manager](resource-group-create-multiple.md).

Lors de la définition des dépendances, vous pouvez inclure l’espace de noms du fournisseur de ressources et le type de ressource pour éviter toute ambiguïté. Par exemple, pour distinguer un équilibrage de charge et un réseau virtuel qui peuvent avoir les mêmes noms que d’autres ressources, utilisez le format suivant :

```json
"dependsOn": [
  "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
  "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
]
``` 

Vous pouvez être tenté d’utiliser dependsOn pour mapper les relations entre vos ressources. Il est toutefois important de comprendre pourquoi vous le faites. Par exemple, pour documenter la manière dont les ressources sont liées entre elles, dependsOn n’est pas la bonne approche. Vous ne pourrez pas lancer de requête pour savoir quelles ressources ont été définies dans l’élément dependsOn après le déploiement. En utilisant dependsOn, vous risquez d’avoir un impact sur le temps de déploiement, car Resource Manager ne déploie pas en parallèle deux ressources qui ont une dépendance. Pour documenter les relations entre les ressources, utilisez plutôt la [liaison de ressources](/rest/api/resources/resourcelinks).

## <a name="child-resources"></a>Ressources enfants
La propriété de ressources vous permet de vous permet de spécifier les ressources enfants associées à la ressource en cours de définition. Les ressources enfants peuvent uniquement être définies sur cinq niveaux. Il est important de noter qu’aucune dépendance implicite n’est créée entre une ressources enfant et la ressource parent. Si vous avez besoin de déployer la ressource enfant après la ressource parent, vous devez déclarer explicitement cette dépendance avec la propriété dependsOn. 

Chaque ressource parente accepte uniquement certains types de ressources comme ressources enfants. Les types de ressource acceptés sont spécifiés dans le [schéma de modèle](https://github.com/Azure/azure-resource-manager-schemas) de la ressource parente. Le nom du type de ressource enfant inclut le nom du type de ressource parente. Par exemple, **Microsoft.Web/sites/config** et **Microsoft.Web/sites/extensions** sont deux ressources enfants de **Microsoft.Web/sites**.

L'exemple suivant montre un serveur SQL et une base de données SQL. Notez qu'une dépendance explicite est définie entre la base de données SQL et le serveur SQL, même si la base de données est un enfant du serveur.

```json
"resources": [
  {
    "name": "[variables('sqlserverName')]",
    "type": "Microsoft.Sql/servers",
    "location": "[resourceGroup().location]",
    "tags": {
      "displayName": "SqlServer"
    },
    "apiVersion": "2014-04-01-preview",
    "properties": {
      "administratorLogin": "[parameters('administratorLogin')]",
      "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
    },
    "resources": [
      {
        "name": "[parameters('databaseName')]",
        "type": "databases",
        "location": "[resourceGroup().location]",
        "tags": {
          "displayName": "Database"
        },
        "apiVersion": "2014-04-01-preview",
        "dependsOn": [
          "[variables('sqlserverName')]"
        ],
        "properties": {
          "edition": "[parameters('edition')]",
          "collation": "[parameters('collation')]",
          "maxSizeBytes": "[parameters('maxSizeBytes')]",
          "requestedServiceObjectiveName": "[parameters('requestedServiceObjectiveName')]"
        }
      }
    ]
  }
]
```

## <a name="reference-function"></a>fonction de référence
La [fonction de référence](resource-group-template-functions.md#reference) permet à une expression de tirer sa valeur d’un autre nom JSON et de paires de valeurs ou de ressources runtime. Les expressions de référence déclarent implicitement qu’une ressource dépend d’une autre. Le format général est le suivant :

```json
reference('resourceName').propertyPath
```

Dans l’exemple suivant, un point de terminaison CDN dépend explicitement du profil CDN et implicitement d’une application web.

```json
{
    "name": "[variables('endpointName')]",
    "type": "endpoints",
    "location": "[resourceGroup().location]",
    "apiVersion": "2016-04-02",
    "dependsOn": [
            "[variables('profileName')]"
    ],
    "properties": {
        "originHostHeader": "[reference(variables('webAppName')).hostNames[0]]",
        ...
    }
```

Vous pouvez utiliser cet élément ou l’élément dependsOn pour spécifier les dépendances, mais il est inutile d’utiliser les deux pour la même ressource dépendante. Si possible, utilisez une référence implicite pour éviter d’ajouter une dépendance inutile.

Pour plus d’informations, consultez la [fonction de référence](resource-group-template-functions.md#reference).

## <a name="recommendations-for-setting-dependencies"></a>Recommandations pour la définition des dépendances

Lorsque vous décidez des dépendances à définir, appliquez les recommandations suivantes :

* Définissez le moins de dépendances possible.
* Définissez une ressource enfant comme dépendante de sa ressource parent.
* Utilisez la fonction **reference** pour définir les dépendances implicites entre les ressources qui doivent partager une propriété. N’ajoutez pas de dépendance explicite (**dependsOn**) lorsque vous avez déjà défini une dépendance implicite. Cette approche permet de réduire le risque d’avoir des dépendances inutiles. 
* Définissez une dépendance lorsqu’une ressource ne peut pas être **créée** sans la fonctionnalité d’une autre ressource. Ne définissez pas de dépendance si les ressources interagissent uniquement après le déploiement.
* Ajoutez les dépendances l’une après l’autre sans les définir explicitement. Par exemple, votre machine virtuelle dépend d’une interface de réseau virtuel, et l’interface de réseau virtuelle dépend d’un réseau virtuel et d’adresses IP publiques. Par conséquent, la machine virtuelle est déployée après les trois ressources. Cependant, ne définissez pas explicitement la machine virtuelle comme dépendante de ces trois ressources. Cette approche permet de clarifier l’ordre des dépendances et de simplifier les modifications ultérieures du modèle.
* Si une valeur peut être déterminée avant le déploiement, essayez de déployer la ressource sans dépendance. Par exemple, si une valeur de configuration a besoin du nom d’une autre ressource, vous n’avez pas forcément besoin d’une dépendance. Cette recommandation n’est pas toujours applicable, car certaines ressources vérifient l’existence de l’autre ressource. Si vous recevez une erreur, ajoutez une dépendance. 

Resource Manager identifie les dépendances circulaires lors de la validation du modèle. Si vous recevez une erreur indiquant qu’il existe une dépendance circulaire, évaluez votre modèle pour déterminer si certaines dépendances sont inutiles et peuvent être supprimées. Si la suppression de ces dépendances n’a aucun effet, vous pouvez éliminer les dépendances circulaires en déplaçant certaines opérations de déploiement dans les ressources enfants qui sont déployées après les ressources présentant la dépendance circulaire. Par exemple, supposons que vous déployiez deux machines virtuelles, mais que vous deviez définir sur chacune d’elles des propriétés faisant référence les unes aux autres. Vous pouvez les déployer dans l’ordre suivant :

1. Machine virtuelle 1
2. Machine virtuelle 2
3. L’extension sur la machine virtuelle 1 dépend des machines virtuelles 1 et&2;. L’extension définit sur la machine virtuelle 1 des valeurs qu’elle obtient de la machine virtuelle 2.
4. L’extension sur la machine virtuelle 2 dépend des machines virtuelles 1 et&2;. L’extension définit sur la machine virtuelle 2 des valeurs qu’elle obtient de la machine virtuelle 1.

Pour plus d’informations sur l’évaluation de l’ordre de déploiement et la résolution des erreurs de dépendance, consultez [Vérifier la séquence de déploiement](resource-manager-common-deployment-errors.md#check-deployment-sequence).

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur la résolution des problèmes liés aux dépendances lors du déploiement, consultez [Résolution des erreurs courantes dans des déploiements Azure avec Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Pour en savoir plus sur la création de modèles Azure Resource Manager, consultez [Création de modèles](resource-group-authoring-templates.md). 
* Pour obtenir la liste des fonctions disponibles dans un modèle, consultez [Fonctions de modèle](resource-group-template-functions.md).


