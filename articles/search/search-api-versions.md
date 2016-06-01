<properties
   pageTitle="Versions d’API Azure Search | Microsoft Azure | API Search"
   description="Stratégie de version pour les API REST Azure Search et la bibliothèque cliente dans le Kit de développement logiciel (SDK) .NET."
   services="search"
   documentationCenter=""
   authors="brjohnstmsft"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="dotnet"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="03/08/2016"
   ms.author="brjohnst"/>

# Versions d’API dans Azure Search

Azure Search déploie régulièrement des mises à jour de fonctionnalités. Parfois, ces mises à jour nous obligent à publier une nouvelle version de notre API pour maintenir la compatibilité descendante. La publication d’une nouvelle version vous permet de contrôler quand et comment intégrer les mises à jour du service de recherche dans votre code.

En règle générale, nous tentons de publier de nouvelles versions uniquement lorsque cela est nécessaire, car certains efforts peuvent être requis pour mettre à niveau votre code afin d’utiliser une nouvelle version de l’API. Nous publions uniquement une nouvelle version si nous devons modifier certains aspects de l’API d’une manière qui interrompt la compatibilité descendante. Cela peut se produire en raison de correctifs de fonctionnalités existantes ou en raison de nouvelles fonctionnalités qui modifient la surface d’exposition des API existantes.

Nous suivons la même règle pour les mises à jour du Kit de développement logiciel (SDK). Le Kit de développement logiciel (SDK) Azure Search suit les règles de [contrôle de version sémantique](http://semver.org/), ce qui signifie que sa version comprend trois parties : majeure, mineure et numéro de version (par exemple, 1.1.0). Nous publions une nouvelle version du Kit de développement logiciel (SDK) uniquement en cas de modifications qui interrompent la compatibilité descendante. Pour les mises à jour de fonctionnalités sans rupture, nous incrémenterons la version mineure. Pour corriger les bogues, nous augmentons uniquement le numéro de version.

##Instantané des versions actuelles 

Vous trouverez ci-dessous un instantané des versions actuelles de toutes les interfaces de programmation d’Azure Search. Les sections suivantes de ce document incluent un calendrier et d’autres informations.

Interfaces|Version majeure la plus récente|Statut
----------|-------------------------|------
[KIT DE DÉVELOPPEMENT LOGICIEL (SDK) .NET](https://msdn.microsoft.com/library/azure/dn951165.aspx)|1\.1|Mise à la disposition générale en février 2016
[API REST du service](https://msdn.microsoft.com/library/azure/dn798935.aspx)|2015-02-28|Mise à la disposition générale
[Version préliminaire de l’API REST Service](search-api-2015-02-28-preview.md)|2015-02-28-Preview|VERSION PRÉLIMINAIRE
[l’API REST de gestion ;](https://msdn.microsoft.com/library/azure/dn832684.aspx)|2015-08-19|Mise à la disposition générale

Pour les API REST, il est nécessaire d’inclure la version d’API (`api-version`) sur chaque appel. Cela permet de cibler facilement une version spécifique, par exemple une API en version préliminaire. L’exemple suivant montre la manière dont le paramètre `api-version` est spécifié :

    GET https://adventure-works.search.windows.net/indexes/bikes?api-version=2015-02-28

> [AZURE.NOTE] Bien que chaque demande ait une version d’API (`api-version`), nous vous recommandons d’utiliser la même version pour toutes les demandes d’API. Cela est particulièrement vrai lorsque de nouvelles versions d’API introduisent des attributs ou des opérations qui ne sont pas reconnus par les versions précédentes. La combinaison de versions d’API peut avoir des conséquences inattendues et doit être évitée.
> 
L’API REST Service et l’API REST Gestion sont contrôlées indépendamment l’une de l’autre. Toute ressemblance dans les numéros de version est une coïncidence.

Les API mises à la disposition générale peuvent être utilisées en production et sont soumises aux contrats SLA Azure. Les versions préliminaires offrent les fonctionnalités expérimentales qui ne sont pas toujours migrées vers la version mise à la disposition générale. **Nous vous conseillons vivement d’utiliser des API en version préliminaire dans les applications de production.**

##Calendrier des versions du Kit de développement logiciel (SDK)

Chaque version du Kit de développement logiciel (SDK) .NET cible une version particulière de l’API REST Service. Les fonctionnalités sont transférées dans l’API REST, puis implémentées dans le Kit de développement logiciel (SDK).

Le Kit de développement logiciel (SDK) .NET est désormais mis à la disposition générale et nous travaillons déjà sur la prochaine version. Le tableau suivant présente les futures versions du Kit de développement logiciel (SDK) afin que vous ayez une idée de ce que nous allons bientôt vous proposer.

Version du Kit de développement logiciel (SDK) .NET|Version de l’API REST|Caractéristiques|ETA
----------------|----------------|--------|---
1\.1|2015-02-28|Syntaxe de requête Lucene|Février 2016
2\.x-preview|2015-02-28-Preview|Analyseurs personnalisés, indexeur d’objet blob Azure, mappages de champs, ETags|Les fonctionnalités seront disponibles au cours du 1er trimestre 2016
2\.x|Nouvelle version de l’API mise à la disposition générale|Identique à la version 2.x-preview|Disponible peu après la version 2.x-preview

##À propos des versions préliminaires et mises à la disposition générale

Azure Search publie toujours les fonctionnalités expérimentales par le biais de l’API REST, puis par le biais des versions préliminaires du Kit de développement logiciel (SDK) .NET. Vous trouverez la liste des fonctionnalités en version préliminaire dans [Nouveautés dans Azure Search](search-latest-updates.md).

Il se peut que les fonctionnalités en version préliminaire ne soient pas migrées vers une version mise à la disposition générale. Alors que les fonctionnalités d’une version mise à la disposition générale sont considérées comme stables et peu susceptibles de changer (à l’exception de légères améliorations et de petits correctifs avec une compatibilité descendante), les fonctionnalités en version préliminaire sont disponibles pour les tests et l’expérimentation, le but étant de recueillir des commentaires sur la conception et l’implémentation.

Toutefois, comme les fonctionnalités en version préliminaire sont susceptibles d’être modifiées, nous vous recommandons de ne pas écrire un code de production dépendant de ces versions. Si vous utilisez une version préliminaire antérieure, nous vous recommandons de migrer vers la version mise à la disposition générale.

Pour le Kit de développement logiciel (SDK) .NET, des conseils sur la migration du code sont disponibles à la page [Mise à niveau du Kit de développement logiciel (SDK) .NET](search-dotnet-sdk-migration.md).

La mise à la disposition générale signifie qu’Azure Search est désormais associé à un contrat SLA. Ce contrat SLA est disponible à la page sur les [contrats SLA Azure Search](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

<!---HONumber=AcomDC_0518_2016-->