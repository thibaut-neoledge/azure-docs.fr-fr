<properties
   pageTitle="Dépendances dans les modèles Resource Manager | Microsoft Azure"
   description="Décrit la procédure permettant de définir une ressource comme dépendante d’une autre ressource au cours du déploiement afin de garantir le déploiement des ressources dans l'ordre adéquat."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/04/2016"
   ms.author="tomfitz"/>

# Définition de dépendances dans les modèles Azure Resource Manager

Une ressource donnée peut comporter d'autres ressources qui doivent exister avant son déploiement. Par exemple, un serveur SQL doit exister avant une tentative de déploiement d'une base de données SQL. Vous définissez cette relation en marquant une seule ressource comme dépendante de l'autre ressource. En général, vous définissez une dépendance avec l'élément **dependsOn**, mais vous pouvez également la définir à l'aide de la fonction **reference**.

Resource Manager évalue les dépendances entre les ressources et les déploie dans leur ordre dépendant. Quand les ressources ne dépendent pas les unes des autres, Resource Manager les déploie en parallèle.

## dependsOn

Dans votre modèle, l'élément dependsOn offre la possibilité de définir une ressource comme une dépendance sur une ou plusieurs ressources. Sa valeur peut être une liste séparée par des virgules de noms de ressources.

L'exemple suivant montre un ensemble de mise à l'échelle de machine virtuelle dépendant d'un équilibreur de charge, un réseau virtuel et une boucle, qui crée plusieurs comptes de stockage. Ces autres ressources ne figurent pas ci-dessous mais ont besoin d'exister ailleurs dans le modèle.

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

Si vous devez définir une dépendance entre une ressource et les ressources qui sont créées via une boucle de copie (comme indiqué ci-dessus), vous pouvez affecter l’élément dependsOn au nom de la boucle. Pour obtenir un exemple, consultez [Création de plusieurs instances de ressources dans Azure Resource Manager](resource-group-create-multiple.md).

Vous pouvez être tenté d’utiliser dependsOn pour mapper les dépendances entre vos ressources. Il est toutefois important de comprendre pourquoi vous le faites, car cette opération peut avoir un impact sur les performances de votre déploiement. Par exemple, si vous faites cela parce que vous souhaitez documenter la manière dont les ressources liées entre elles, dependsOn n’est pas la bonne approche. Le cycle de vie de dependsOn se consacre uniquement au déploiement, et n’est pas disponible après. Après le déploiemnet, il n’existe aucun moyen d’interroger ces dépendances. Avec dependsOn, vous risquez dégrader les performances en détournant par inadvertance le moteur de déploiement de l’utilisation du parallélisme, alors que cela pourrait se passer autrement. Pour documenter et fournir la fonctionnalité d’interrogation sur les relations entre les ressources, il est préférable d’utiliser la [liaison des ressources](resource-group-link-resources.md).

## Ressources enfants

La propriété de ressources vous permet de vous permet de spécifier les ressources enfants associées à la ressource en cours de définition. Les ressources enfants peuvent uniquement être définies sur 5 niveaux. Il est important de noter qu’aucune dépendance implicite n’est créée entre une ressources enfant et la ressource parent. Si vous avez besoin de déployer la ressource enfant après la ressource parent, vous devez déclarer explicitement cette dépendance avec la propriété dependsOn.

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


## fonction de référence

La fonction de référence permet à une expression de tirer sa valeur d’un autre nom JSON et de paires de valeurs ou de ressources runtime. Les expressions de référence déclarent implicitement qu’une ressource dépend d’une autre. La propriété représentée par **propertyPath** ci-dessous est facultative ; si elle n’est pas spécifiée, la référence est à la ressource.

    reference('resourceName').propertyPath

Vous pouvez utiliser cet élément ou l’élément dependsOn pour spécifier les dépendances, mais il est inutile d’utiliser les deux pour la même ressource dépendante. Le guide consiste à utiliser la référence implicite pour éviter le risque qu’un élément dependsOn arrête par inadvertance le moteur de déploiement et l’empêche de réaliser certains aspects du déploiement en parallèle.

Pour plus d’informations, consultez la [fonction de référence](../resource-group-template-functions/#reference).

## Étapes suivantes

- Pour en savoir plus sur la création de modèles Azure Resource Manager, consultez [Création de modèles](resource-group-authoring-templates.md). 
- Pour obtenir la liste des fonctions disponibles dans un modèle, consultez [Fonctions de modèle](resource-group-template-functions.md).

<!---HONumber=AcomDC_0406_2016-->