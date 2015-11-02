<properties
   pageTitle="Définition de dépendances dans les modèles Azure Resource Manager"
   description="Décrit la procédure permettant de définir une ressource comme dépendante d’une autre ressource au cours du déploiement."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="mmercuri"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/20/2015"
   ms.author="mmercuri"/>

# Définition de dépendances dans les modèles Azure Resource Manager

Une ressource donnée peut avoir plusieurs dépendances en amont et dépendances enfants essentielles au succès de votre topologie. Vous pouvez définir des dépendances sur d’autres ressources à l’aide des propriétés **dependsOn** et **ressources** d’une ressource. Une dépendance peut également être spécifiée à l’aide de la fonction **référence**.

    {
        "name": "<name-of-the-resource>",
        "type": "<resource-provider-namespace/resource-type-name>",
        "apiVersion": "<supported-api-version-of-resource>",
        "location": "<location-of-resource>",
        "tags": { <name-value-pairs-for-resource-tagging> },
        "dependsOn": [ <array-of-related-resource-names> ],
        "properties": { <settings-for-the-resource> },
        "resources": { <dependent-resources> }
    }

 Il existe également des liens pouvant définir des relations entre les ressources et prendre en charge la définition de ces relations entre les groupes de ressources.

## dependsOn

Pour une machine virtuelle donnée, vous pouvez être dépendant de la présence d’une ressource de base de données correctement configurée. Dans d’autre cas, vous pouvez dépendre de l’installation de plusieurs nœuds de votre cluster avant le déploiement de machine virtuelle avec l’outil de gestion de cluster.

Dans votre modèle, la propriété dependsOn offre la possibilité de définir cette dépendance pour une ressource. Sa valeur peut être une liste séparée par des virgules de noms de ressources. Les dépendances entre les ressources sont évaluées et les ressources sont déployées dans leur ordre dépendant. Quand les ressources ne dépendent les unes des autres, leur déploiement en parallèle est tenté.

Vous pouvez être tenté d’utiliser dependsOn pour mapper les dépendances entre vos ressources. Il est toutefois important de comprendre pourquoi vous le faites, car cette opération peut avoir un impact sur les performances de votre déploiement. Par exemple, si vous faites cela parce que vous souhaitez documenter la manière dont les ressources liées entre elles, dependsOn n’est pas la bonne approche. Le cycle de vie de dependsOn se consacre uniquement au déploiement, et n’est pas disponible après. Après le déploiemnet, il n’existe aucun moyen d’interroger ces dépendances. Avec dependsOn, vous risquez dégrader les performances en détournant par inadvertance le moteur de déploiement de l’utilisation du parallélisme, alors que cela pourrait se passer autrement. Pour documenter et fournir la fonctionnalité d’interrogation sur les relations entre les ressources, il est préférable d’utiliser la [liaison des ressources](resource-group-link-resources.md).

Cet élément n’est pas nécessaire si la fonction de référence est utilisée pour obtenir la représentation d’une ressource, car un objet de référence implique une dépendance sur la ressource. En fait, s’il existe une possibilité d’utiliser une référence plutôt que dependsOn, le conseil est d’utiliser la fonction de référence et des références implicites. Le raisonnement ici concerne à nouveau les performances. Les références définissent des dépendances implicites dont on sait qu’elles sont obligatoires au moment où elles sont référencées au sein du modèle. Par leur présence, elles sont pertinentes, et évitent une nouvelle optimisation des performances, ainsi que le risque de nuire au moteur de déploiement pour éviter un parallélisme intule.

Si vous devez définir une dépendance entre une ressource et les ressources qui sont créées via une boucle de copie, vous pouvez affecter l’élément dependsOn au nom de la boucle. Pour obtenir un exemple, consultez [Création de plusieurs instances de ressources dans Azure Resource Manager](resource-group-create-multiple.md).

## les ressources

La propriété de ressources vous permet de vous permet de spécifier les ressources enfants associées à la ressource en cours de définition. Les ressources enfants peuvent uniquement être définies sur 5 niveaux. Il est important de noter qu’aucune dépendance implicite n’est créée entre une ressources enfant et la ressource parent. Si vous avez besoin de déployer la ressource enfant après la ressource parent, vous devez déclarer explicitement cette dépendance avec la propriété dependsOn.

## fonction de référence

La fonction de référence permet à une expression de tirer sa valeur d’un autre nom JSON et de paires de valeurs ou de ressources runtime. Les expressions de référence déclarent implicitement qu’une ressource dépend d’une autre. La propriété représentée par **propertyPath** ci-dessous est facultative ; si elle n’est pas spécifiée, la référence est à la ressource.

    reference('resourceName').propertyPath

Vous pouvez utiliser cet élément ou l’élément dependsOn pour spécifier les dépendances, mais il est inutile d’utiliser les deux pour la même ressource dépendante. Le guide consiste à utiliser la référence implicite pour éviter le risque qu’un élément dependsOn arrête par inadvertance le moteur de déploiement et l’empêche de réaliser certains aspects du déploiement en parallèle.

Pour plus d’informations, consultez la section [fonction de référence](../resource-group-template-functions/#reference).

## Étapes suivantes

- Pour en savoir plus sur la création de modèles Azure Resource Manager, consultez la section [Création de modèles](resource-group-authoring-templates.md). 
- Pour obtenir la liste des fonctions disponibles dans un modèle, consultez la section [Fonctions de modèle](resource-group-template-functions.md).

<!---HONumber=Oct15_HO4-->