---
title: "Versions de l’API Recherche Azure | Microsoft Docs"
description: "Stratégie de version pour les API REST Azure Search et la bibliothèque cliente dans le Kit de développement logiciel (SDK) .NET."
services: search
documentationcenter: 
author: brjohnstmsft
manager: pablocas
editor: 
ms.assetid: 0458053a-164e-4682-a802-00097ecde981
ms.service: search
ms.devlang: dotnet
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 01/11/2017
ms.author: brjohnst
ms.translationtype: Human Translation
ms.sourcegitcommit: 7d45759915f38ba4337b745eb2b28dcbc72dbbe0
ms.openlocfilehash: a14131455ad94cbc4b729077568b12043401c08e
ms.contentlocale: fr-fr
ms.lasthandoff: 02/17/2017

---

# <a name="api-versions-in-azure-search"></a>Versions d’API dans Azure Search
Azure Search déploie régulièrement des mises à jour de fonctionnalités. Parfois, ces mises à jour nous obligent à publier une nouvelle version de notre API pour maintenir la compatibilité descendante. La publication d’une nouvelle version vous permet de contrôler quand et comment intégrer les mises à jour du service de recherche dans votre code.

En règle générale, nous tentons de publier de nouvelles versions uniquement lorsque cela est nécessaire, car certains efforts peuvent être requis pour mettre à niveau votre code afin d’utiliser une nouvelle version de l’API. Nous publions uniquement une nouvelle version si nous devons modifier certains aspects de l’API d’une manière qui interrompt la compatibilité descendante. Cela peut se produire en raison de correctifs de fonctionnalités existantes ou en raison de nouvelles fonctionnalités qui modifient la surface d’exposition des API existantes.

Nous suivons la même règle pour les mises à jour du Kit de développement logiciel (SDK). Le Kit de développement logiciel (SDK) de la Recherche Azure suit les règles de [gestion sémantique de version](http://semver.org/), ce qui signifie que sa version comprend trois parties : majeure, mineure et numéro de version (par exemple, 1.1.0). Nous publions une nouvelle version du Kit de développement logiciel (SDK) uniquement en cas de modifications qui interrompent la compatibilité descendante. Pour les mises à jour de fonctionnalités sans rupture, nous incrémenterons la version mineure. Pour corriger les bogues, nous augmentons uniquement le numéro de version.

> [!NOTE]
> Votre instance de service Recherche Azure prend en charge plusieurs versions de l’API REST, y compris la plus récente. Vous pouvez continuer à utiliser une version lorsqu’elle n’est pas la plus récente, mais nous vous recommandons de migrer votre code pour utiliser la dernière version. Lorsque vous utilisez l’API REST, vous devez spécifier la version de l’API dans chaque requête via le paramètre « api-version » (Version de l’API). Lorsque vous utilisez le Kit de développement logiciel (SDK) .NET, la version du Kit de développement logiciel (SDK) que vous utilisez détermine la version correspondante de l’API REST. Si vous utilisez un Kit de développement logiciel (SDK) plus ancien, vous pouvez continuer à exécuter ce code sans changement, même si le service est mis à niveau pour prendre en charge une version plus récente de l’API.

## <a name="snapshot-of-current-versions"></a>Instantané des versions actuelles
Vous trouverez ci-dessous un instantané des versions actuelles de toutes les interfaces de programmation d’Azure Search.

| Interfaces | Version majeure la plus récente | État |
| --- | --- | --- |
| [Kit SDK .NET](https://aka.ms/search-sdk) |3.0 |Mise à la disposition générale en novembre 2016 |
| [Version préliminaire du Kit de développement logiciel (SDK) .NET](https://aka.ms/search-sdk-preview) |2.0-preview |Version préliminaire, publiée en août 2016 |
| [API REST du service](https://docs.microsoft.com/rest/api/searchservice/) |2016-09-01 |Mise à la disposition générale |
| [Version préliminaire de l’API REST Service](search-api-2015-02-28-preview.md) |2015-02-28-Preview |VERSION PRÉLIMINAIRE |
| [.NET Management SDK](https://aka.ms/search-mgmt-sdk) |2015-08-19 |Mise à la disposition générale |
| [l’API REST de gestion ;](https://docs.microsoft.com/rest/api/searchmanagement/) |2015-08-19 |Mise à la disposition générale |

Pour les API REST, il est nécessaire d’inclure la version d’API ( `api-version` ) sur chaque appel. Cela permet de cibler facilement une version spécifique, par exemple une API en version préliminaire. L’exemple suivant montre la manière dont le paramètre `api-version` est spécifié :

    GET https://adventure-works.search.windows.net/indexes/bikes?api-version=2016-09-01

> [!NOTE]
> Bien que chaque demande ait une version d’API (`api-version`), nous vous recommandons d’utiliser la même version pour toutes les demandes d’API. Cela est particulièrement vrai lorsque de nouvelles versions d’API introduisent des attributs ou des opérations qui ne sont pas reconnus par les versions précédentes. La combinaison de versions d’API peut avoir des conséquences inattendues et doit être évitée.
>
> L’API REST Service et l’API REST Gestion sont contrôlées indépendamment l’une de l’autre. Toute ressemblance dans les numéros de version est une coïncidence.

Les API mises à la disposition générale peuvent être utilisées en production et sont soumises aux contrats SLA Azure. Les versions préliminaires offrent les fonctionnalités expérimentales qui ne sont pas toujours migrées vers la version mise à la disposition générale. **Nous vous conseillons vivement d’utiliser des API en version préliminaire dans les applications de production.**

## <a name="about-preview-and-generally-available-versions"></a>À propos des versions préliminaires et mises à la disposition générale
Azure Search publie toujours les fonctionnalités expérimentales par le biais de l’API REST, puis par le biais des versions préliminaires du Kit de développement logiciel (SDK) .NET.

Il se peut que les fonctionnalités en version préliminaire ne soient pas migrées vers une version mise à la disposition générale. Alors que les fonctionnalités d’une version mise à la disposition générale sont considérées comme stables et peu susceptibles de changer (à l’exception de légères améliorations et de petits correctifs avec une compatibilité descendante), les fonctionnalités en version préliminaire sont disponibles pour les tests et l’expérimentation, le but étant de recueillir des commentaires sur la conception et l’implémentation.

Toutefois, comme les fonctionnalités en version préliminaire sont susceptibles d’être modifiées, nous vous recommandons de ne pas écrire un code de production dépendant de ces versions. Si vous utilisez une version préliminaire antérieure, nous vous recommandons de migrer vers la version mise à la disposition générale.

Pour le Kit de développement logiciel (SDK) .NET, des conseils sur la migration du code sont disponibles à la page [Mise à niveau du Kit de développement logiciel (SDK) .NET](search-dotnet-sdk-migration.md).

La mise à la disposition générale signifie qu’Azure Search est désormais associé à un contrat SLA. Ce contrat SLA est disponible à la page sur les [contrats SLA Azure Search](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

