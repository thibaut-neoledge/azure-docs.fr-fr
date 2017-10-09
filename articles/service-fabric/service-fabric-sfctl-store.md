---
title: "CLI Azure Service Fabric : sfctl store | Microsoft Docs"
description: "Décrit les commandes sfctl store de l’interface de ligne de commande (CLI) Service Fabric."
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 09/22/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: cb9130243bdc94ce58d6dfec3b96eb963cdaafb0
ms.openlocfilehash: 2af6dff4ffcdf295731f2d61b5f9e35af40615e5
ms.contentlocale: fr-fr
ms.lasthandoff: 09/26/2017

---
# <a name="sfctl-store"></a>sfctl store
Effectue des opérations élémentaire au niveau des fichiers dans le magasin d’images de cluster.

## <a name="commands"></a>Commandes

|Commande|Description|
| --- | --- |
|    delete| Supprime le contenu du magasin d’images.|
|    root-info| Permet d’obtenir les informations de contenu à la racine du magasin d’images.|
|    stat  | Permet d’obtenir les informations de contenu du magasin d’images.|


## <a name="sfctl-store-delete"></a>sfctl store delete
Supprime le contenu du magasin d’images.

Supprime le contenu du magasin d’images trouvé dans le chemin d’accès relatif du magasin d’images donné. Cette commande permet de supprimer les packages d’application chargés une fois qu’ils ont été approvisionnés.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --content-path [Requis]| Chemin d’accès relatif au fichier ou au dossier dans le magasin d’images à partir de sa racine.|
| --timeout -t          | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug               | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h             | Affiche ce message d’aide et quitte.|
| --output -o           | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut : json.|
| --query               | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose             | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-store-stat"></a>sfctl store stat
Permet d’obtenir les informations de contenu du magasin d’images.

Retourne les informations sur le contenu du magasin d’images dans le chemin d’accès de contenu spécifié par rapport à la racine du magasin d’images.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --content-path [Requis]| Chemin d’accès relatif au fichier ou au dossier dans le magasin d’images à partir de sa racine.|
| --timeout -t          | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug               | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h             | Affiche ce message d’aide et quitte.|
| --output -o           | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut : json.|
| --query               | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose             | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="next-steps"></a>Étapes suivantes
- [Configurez](service-fabric-cli.md) l’interface de ligne de commande Service Fabric.
- Découvrez comment utiliser l’interface de ligne de commande (CLI) Service Fabric à l’aide d’[exemples de scripts](/azure/service-fabric/scripts/sfctl-upgrade-application).
