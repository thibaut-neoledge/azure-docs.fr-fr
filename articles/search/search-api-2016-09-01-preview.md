---
title: "API REST du service Recherche Azure : Version 2016-09-01-Preview | Microsoft Docs"
description: "L’API REST du service Recherche Azure, version 2016-09-01-Preview, comprend des fonctionnalités expérimentales, telles que les synonymes et les recherches moreLikeThis."
services: search
documentationcenter: na
author: mhko
manager: jlembicz
editor: 
ms.assetid: 3dba3bf8-9c83-42f6-82bc-04727bd11037
ms.service: search
ms.devlang: rest-api
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 10/25/2017
ms.author: nateko
ms.openlocfilehash: 082c207f892fcc277d30d66c6165dd9920d3ab27
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2017
---
# <a name="azure-search-service-rest-api-version-2016-09-01-preview"></a>API REST du service Recherche Azure : Version 2016-09-01-Preview
Cet article constitue la documentation de référence de `api-version=2016-09-01-Preview`. Cette préversion étend la version actuelle disponible, [api-version=2016-09-01](https://msdn.microsoft.com/library/dn798935.aspx), en fournissant les fonctionnalités expérimentales suivantes :

* [API Synonymes](search-synonyms.md) pour charger des mappages de synonyme et développer la recherche.
* `moreLikeThis`Paramètre de requête](search-more-like-this.md) [ pour rechercher des documents correspondant à un document spécifique.

Assurez-vous de choisir la version de l’API en préversion `api-version=2016-09-01-Preview` pour essayer ces fonctionnalités expérimentales. L’exemple suivant illustre comment la version de l’api en préversion est spécifiée dans une formulation de requête more-like-this.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?moreLikeThis=a1&api-version=2016-09-01-Preview

> [!NOTE]
> Les fonctionnalités en préversion sont disponibles pour le test et l’expérimentation en vue de recueillir des commentaires, elles sont susceptibles de changer. **Nous vous conseillons vivement d’utiliser des API en version préliminaire dans les applications de production.**

Le service Azure Search est disponible dans plusieurs versions. Pour plus d'informations, consultez [Contrôle de version de service Azure Search](http://msdn.microsoft.com/library/azure/dn864560.aspx) .
