---
title: "Dépendances dans les modèles Resource Manager | Microsoft Docs"
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
ms.date: 09/12/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e841c21a15c47108cbea356172bffe766003a145
ms.openlocfilehash: 1499ddf06dda6740ccfe1e7b6832998bb33cb1c4


---
# <a name="defining-dependencies-in-azure-resource-manager-templates"></a>Définition de dépendances dans les modèles Azure Resource Manager
Une ressource donnée peut comporter d'autres ressources qui doivent exister avant son déploiement. Par exemple, un serveur SQL doit exister avant une tentative de déploiement d'une base de données SQL. Vous définissez cette relation en marquant une seule ressource comme dépendante de l'autre ressource. En général, vous définissez une dépendance avec l'élément **dependsOn**, mais vous pouvez également la définir à l'aide de la fonction **reference**. 

Resource Manager évalue les dépendances entre les ressources et les déploie dans leur ordre dépendant. Quand les ressources ne dépendent pas les unes des autres, Resource Manager les déploie en parallèle.

## <a name="dependson"></a>dependsOn
Dans votre modèle, l’élément dependsOn vous permet de définir une ressource comme une dépendance sur une ou plusieurs ressources. Sa valeur peut être une liste séparée par des virgules de noms de ressources. 

L’exemple suivant montre un groupe identique de machines virtuelles dépendant d’un équilibreur de charge, un réseau virtuel et une boucle qui crée plusieurs comptes de stockage. Ces autres ressources ne figurent pas dans l’exemple suivant, mais ont besoin d’exister ailleurs dans le modèle.

    {
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "name": "[variables('namingInfix')]",
      "location": "[variables('location')]",
      "apiVersion": "2016-03-30",
      "tags": {
        "displayName": "VMScaleSet"
      },
      "dependsOn": [
        "storageLoop",
        "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
      ],
      ...
    }

Pour définir une dépendance entre une ressource et les ressources qui sont créées par une boucle de copie, affectez l’élément dependsOn au nom de la boucle. Pour obtenir un exemple, consultez [Création de plusieurs instances de ressources dans Azure Resource Manager](resource-group-create-multiple.md).

Vous pouvez être tenté d’utiliser dependsOn pour mapper les relations entre vos ressources. Il est toutefois important de comprendre pourquoi vous le faites, car cette opération peut avoir un impact sur les performances de votre déploiement. Par exemple, pour documenter la manière dont les ressources sont liées entre elles, dependsOn n’est pas la bonne approche. Vous ne pourrez pas lancer de requête pour savoir quelles ressources ont été définies dans l’élément dependsOn après le déploiement. En utilisant dependsOn, vous risquez d’avoir un impact sur le temps de déploiement, car Resource Manager ne déploie pas en parallèle deux ressources qui ont une dépendance. Pour documenter les relations entre les ressources, utilisez plutôt la [liaison de ressources](resource-group-link-resources.md).

## <a name="child-resources"></a>Ressources enfants
La propriété de ressources vous permet de vous permet de spécifier les ressources enfants associées à la ressource en cours de définition. Les ressources enfants peuvent uniquement être définies sur cinq niveaux. Il est important de noter qu’aucune dépendance implicite n’est créée entre une ressources enfant et la ressource parent. Si vous avez besoin de déployer la ressource enfant après la ressource parent, vous devez déclarer explicitement cette dépendance avec la propriété dependsOn. 

Chaque ressource parente accepte uniquement certains types de ressources comme ressources enfants. Les types de ressource acceptés sont spécifiés dans le [schéma de modèle](https://github.com/Azure/azure-resource-manager-schemas) de la ressource parente. Le nom du type de ressource enfant inclut le nom du type de ressource parente. Par exemple, **Microsoft.Web/sites/config** et **Microsoft.Web/sites/extensions** sont deux ressources enfants de **Microsoft.Web/sites**.

L'exemple suivant montre un serveur SQL et une base de données SQL. Notez qu'une dépendance explicite est définie entre la base de données SQL et le serveur SQL, même si la base de données est un enfant du serveur.

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


## <a name="reference-function"></a>fonction de référence
La [fonction de référence](resource-group-template-functions.md#reference) permet à une expression de tirer sa valeur d’un autre nom JSON et de paires de valeurs ou de ressources runtime. Les expressions de référence déclarent implicitement qu’une ressource dépend d’une autre. 

    reference('resourceName').propertyPath

Vous pouvez utiliser cet élément ou l’élément dependsOn pour spécifier les dépendances, mais il est inutile d’utiliser les deux pour la même ressource dépendante. Si possible, utilisez une référence implicite pour éviter d’ajouter par inadvertance une dépendance inutile.

Pour plus d’informations, consultez la [fonction de référence](resource-group-template-functions.md#reference).

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur la création de modèles Azure Resource Manager, consultez [Création de modèles](resource-group-authoring-templates.md). 
* Pour obtenir la liste des fonctions disponibles dans un modèle, consultez [Fonctions de modèle](resource-group-template-functions.md).




<!--HONumber=Nov16_HO3-->


