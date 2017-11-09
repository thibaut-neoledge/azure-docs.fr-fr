---
title: "Mise à niveau vers la version 2016-09-01 de l’API REST du service Recherche Azure | Microsoft Docs"
description: "Mise à niveau vers la version 2016-09-01 de l’API REST du service Recherche Azure"
services: search
documentationcenter: 
author: brjohnstmsft
manager: pablocas
editor: 
ms.assetid: 6183fa6c-48bb-4af7-adae-4be3bc43c3ed
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/27/2016
ms.author: brjohnst
ms.openlocfilehash: f6a189c2e314b91c490583a86d8bacca8ec78a0f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="upgrading-to-the-azure-search-service-rest-api-version-2016-09-01"></a>Mise à niveau vers la version 2016-09-01 de l’API REST du service Recherche Azure
Si vous utilisez la version 2015-02-28 ou 2015-02-28-Preview de [l’API REST du service Recherche Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx), cet article vous aidera à mettre à niveau votre application pour utiliser la nouvelle version de l’API (2016-09-01).

La version 2016-09-01 de l’API REST contient des modifications des versions antérieures. Ces modifications sont, pour la plupart, à compatibilité descendante. La modification de votre code est donc facilitée, selon la version que vous utilisiez précédemment. Consultez la page [Procédure de mise à niveau](#UpgradeSteps) pour obtenir des instructions sur la façon de modifier votre code pour qu’il utilise la nouvelle version de l’API.

> [!NOTE]
> Votre instance de service Recherche Azure prend en charge plusieurs versions de l’API REST, y compris la plus récente. Vous pouvez continuer à utiliser une version lorsqu’elle n’est pas la plus récente, mais nous vous recommandons de migrer votre code pour utiliser la dernière version.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2016-09-01"></a>Nouveautés de la version 2016-09-01
La version 2016-09-01 est la deuxième version généralement disponible de l’API REST du service Recherche Azure. Cette version de l’API comprend les nouvelles fonctionnalités suivantes :

* Des [analyseurs personnalisés](https://aka.ms/customanalyzers), qui vous permettent de contrôler le processus de conversion de texte en jetons d’indexation et de recherche.
* Des indexeurs [Stockage Blob Azure](search-howto-indexing-azure-blob-storage.md) et [Stockage Table Azure](search-howto-indexing-azure-tables.md), qui vous permettent d’importer facilement des données depuis le stockage Azure dans la Recherche Azure, selon une planification ou à la demande.
* Des [mappages de champs](search-indexer-field-mappings.md), qui vous permettent de personnaliser la façon dont les indexeurs importent des données dans la Recherche Azure.
* ETag, qui vous permet de mettre à jour les définitions d’index, les indexeurs et les sources de données avec accès concurrentiel sécurisé. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Procédure de mise à niveau
Si vous effectuez une mise à niveau à partir de la version 2015-02-28, vous n’aurez probablement pas à modifier votre code, en dehors du numéro de version. Les seules situations dans lesquelles vous pouvez avoir à modifier votre code sont les suivantes :

* Lorsque votre code échoue, car des propriétés non reconnues sont renvoyées dans une réponse de l’API. Par défaut, votre application doit ignorer les propriétés qu’elle ne comprend pas.
* Votre code conserve des demandes d’API et tente de les renvoyer à la nouvelle version de l’API. Par exemple, cela peut se produire si votre application conserve les jetons de continuation renvoyés par l’API Recherche (pour plus d’informations, recherchez `@search.nextPageParameters` dans les [références sur l’API Recherche](https://msdn.microsoft.com/library/azure/dn798927.aspx#Anchor_1)).

Si vous êtes concerné par l’une de ces situations, vous aurez peut-être à modifier votre code en conséquence. Dans le cas contraire, aucune modification n’est nécessaire, sauf si vous souhaitez commencer à utiliser les [nouvelles fonctionnalités](#WhatsNew) de la version 2016-09-01.

Cela s’applique également si vous effectuez une mise à niveau à partir de la version 2015-02-28-Preview. Cependant, certaines fonctionnalités ne sont pas disponibles dans la version 2016-09-01 :

* Prise en charge des indexeurs Stockage Blob Azure pour les fichiers CSV et les objets Blob contenant des tableaux JSON.
* Synonymes
* Requêtes « More like this »

Si votre code utilise ces fonctionnalités, vous ne pourrez pas effectuer une mise à niveau vers la version 2016-09-01 sans supprimer votre utilisation de ces fonctionnalités.

> [!IMPORTANT]
> N’oubliez pas que les API d’évaluation sont destinées à être utilisées à des fins de test et d’évaluation, et non dans les environnements de production.
> 
> 

## <a name="conclusion"></a>Conclusion
Pour plus d’informations sur l’utilisation de l’API REST du service Recherche Azure, consultez les [références d’API](https://msdn.microsoft.com/library/azure/dn798935.aspx) récemment mises à jour sur MSDN.

Vos commentaires sur la Recherche Azure sont les bienvenus. Si vous rencontrez des problèmes, n’hésitez pas à nous demander de l’aide sur le [forum MSDN de la Recherche Azure ](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch) ou sur [StackOverflow](http://stackoverflow.com/). Si vous souhaitez poser une question sur la Recherche Azure sur StackOverflow, veillez à utiliser le mot clé `azure-search`.

Merci d’utiliser Azure Search !

