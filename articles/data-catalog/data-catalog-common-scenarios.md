---
title: "Scénarios courants avec Azure Data Catalog | Microsoft Docs"
description: "Vue d’ensemble des scénarios courants pour Azure Data Catalog, y compris l’inscription et la découverte des sources de données de grande valeur, l’activation du décisionnel libre-service et la capture de connaissances tribales existantes sur les sources de données et les processus."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 60930d78-d2d4-4d5d-9651-bdda50b0da0e
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 05/15/2017
ms.author: maroche
ms.translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 975084c19909b0829b6f93cab05373fb71b10cf3
ms.contentlocale: fr-fr
ms.lasthandoff: 11/17/2016


---
# <a name="azure-data-catalog-common-scenarios"></a>Scénarios courants avec Azure Data Catalog
Cet article présente des scénarios courants dans lesquels Azure Data Catalog peut aider les organisations à mieux exploiter leurs sources de données existantes.

## <a name="scenario-1---registration-of-central-data-sources"></a>Scénario no1 : enregistrement des sources de données centrales
Les organisations ont souvent de nombreuses sources de données de grande valeur. Celles-ci incluent les systèmes d’entreprise OLTP, les entrepôts de données et le décisionnel / les bases de données d’analyse. Souvent, le nombre de systèmes augmente et le chevauchement entre les systèmes s’accentue au fur et à mesure de l’évolution des besoins de l’entreprise ou de l’évolution de l’entreprise elle-même par le biais de fusions et d’acquisitions.

Il est souvent difficile pour les utilisateurs de savoir où rechercher les données parmi ces sources de données. Les questions se posent régulièrement :

* Parmi les trois systèmes de RH utilisés par l’entreprise, lequel dois-je utiliser pour créer ce type de rapport ?
* Où puis-je obtenir les chiffres des ventes certifiés pour l’exercice qui vient de se clôturer ?
* À qui dois-je m’adresser ou quel processus dois-je suivre pour accéder à l’entrepôt de données ?
* Je ne sais pas si ces chiffres sont corrects ; qui pourrait m’expliquer comment ces données sont censées être utilisées avant de partager ce tableau de bord avec mon équipe ?

Dans ce scénario, Azure Data Catalog peut vous aider. Les sources de données centrales, de grande valeur, gérées par informatique et utilisées dans l’ensemble de l’organisation sont souvent le point de départ logique pour remplir le catalogue. Bien que n’importe quel utilisateur puisse inscrire une source de données, il sera plus facile d’encourager l’adoption et l’utilisation du système si vous commencez par remplir le catalogue avec les sources de données qui sont susceptibles d’être utiles au plus grand nombre d’utilisateurs. Pour les clients n’ayant jamais utilisé Azure Data Catalog auparavant, l’identification et l’enregistrement des principales sources de données utilisées par de nombreuses équipes consommatrices de données peuvent représenter un premier pas vers la réussite.

Ce scénario permet également d’annoter les sources de données à valeur élevée pour les rendre plus faciles à comprendre et plus faciles d’accès. Un aspect clé de cet effort consiste à inclure des informations sur la façon dont les utilisateurs peuvent demander l'accès à la source de données. L’Azure Data Catalog permet à des utilisateurs de fournir l'adresse électronique de l'équipe ou de l'utilisateur chargé de contrôler l'accès aux sources de données, les liens vers des outils existants ou une documentation, ou du texte libre qui décrit le processus de demande d'accès. Avec ces informations incluses dans le catalogue, les utilisateurs qui découvrent des sources de données enregistrées, mais qui n'ont pas encore les autorisations pour accéder aux données, peuvent facilement demander l'accès à l'aide de processus définis et contrôlés par les propriétaires des sources de données.

## <a name="scenario-2---self-service-business-intelligence"></a>Scénario no2 : décisionnel libre-service
Bien que les solutions traditionnelles d’analyse décisionnelle des entreprises continuent d’être un élément important du paysage de données de nombreuses organisations, le décisionnel libre-service gagne chaque jour en importance du fait de l’intensification du rythme des opérations. Le décisionnel libre-service permet aux professionnels de l’information et aux analystes de créer leurs propres rapports, classeurs et tableaux de bord sans dépendre d’une équipe informatique centrale, et sans être limités par l’emploi du temps ou la disponibilité de l’équipe informatique.

Dans les scénarios de décisionnel libre-service, les utilisateurs combinent fréquemment des données provenant de plusieurs sources, dont un grand nombre n’ont peut-être jamais été utilisées auparavant pour le décisionnel et l’analyse. Bien qu’il arrive que certaines de ces sources soient déjà connues, il est souvent nécessaire de suivre un processus visant à identifier les étapes permettant de localiser et d’évaluer les sources potentielles de données pour une tâche donnée.

Généralement, ce processus est exécuté manuellement : les analystes utilisent leurs connexions réseau homologues pour identifier d’autres personnes travaillant avec les données recherchées. Une source de données peut être utilisée dès qu’elle a été trouvée, mais il faut ensuite répéter le processus pour chaque initiative ultérieure de décisionnel libre-service, avec plusieurs utilisateurs effectuant le même processus manuel de découverte.

Grâce à Azure Data Catalog, les utilisateurs peuvent rompre ce cycle d’efforts redondants. Une fois qu’une source de données a été découverte selon un processus traditionnel, un analyste peut enregistrer la source de données pour la rendre plus facilement identifiable à l’avenir. L’utilisateur peut annoter les ressources de données enregistrées pour leur conférer davantage de valeur ; il n’est pas nécessaire que cette opération soit effectuée au moment de l’enregistrement. Les utilisateurs peuvent apporter leur contribution au fil du temps, en fonction de leur emploi du temps, renforçant ainsi progressivement la valeur des sources de données enregistrées dans le catalogue.

Cette croissance organique du contenu du catalogue est un complément naturel de l’enregistrement initial des sources de données centrales. Le préremplissage du catalogue avec des données utiles à un grand nombre d’utilisateurs pourra initialement inciter ces derniers à découvrir et utiliser le catalogue. De plus, si les utilisateurs sont autorisés à enregistrer et annoter des sources supplémentaires, cela contribuera à consolider leur engagement et celui de leurs homologues.

Il est également important de noter que, bien que ce scénario se concentre spécifiquement sur le décisionnel libre-service, les mêmes schémas et les mêmes défis s’appliquent également aux projets décisionnels d’entreprise de grande échelle. Tout effort impliquant un processus manuel de découverte de sources de données est un effort dont peut tirer profit l’entreprise grâce à l’utilisation d’Azure Data Catalog.

## <a name="scenario-3---capturing-tribal-knowledge"></a>Scénario no3 : capturer les connaissances tribales
Comment savoir quelles données sont nécessaires pour effectuer votre travail et où les trouver ?

Si vous occupez le même poste depuis un certain temps, vous connaissez probablement la réponse. Vous avez suivi un processus d’apprentissage progressif, et au fil du temps vous avez appris quelles sources de données sont essentielles à la réalisation de vos tâches quotidiennes.

Mais lorsqu’un nouvel employé intègre votre équipe, comment sait-il de quelles données il aura besoin pour effectuer son travail et où les trouver ?

Il est fort probable qu’il se tourne vers vous pour le savoir.

Ce transfert continu de connaissances tribales fait partie du processus de découverte des sources de données dans toutes les entreprises, quelle que soit leur taille. Les membres les plus anciens et les plus expérimentés de l’équipe ont accumulé des connaissances au fil des années, tandis que les membres ayant rejoint l’équipe plus récemment ont appris à s’adresser à eux lorsqu’ils ont des questions. Souvent, seules quelques personnes connaissent les informations essentielles, et lorsque ces personnes sont en vacances ou quittent l’équipe, l’organisation en pâtit.

Parfois ces experts en données font l’effort de documenter leurs connaissances en les partageant par courrier électronique ou via des documents Word sur un site d’équipe SharePoint. Bien que ce système puisse se révéler utile, il introduit également un nouveau problème de découverte : comment savoir quelles documentations existent et où les trouver...

Azure Data Catalog agit comme une plateforme permettant de partager ces connaissances tribales et de les rendre facilement identifiables. Les experts en données peuvent annoter directement les ressources de données et même inclure des liens vers la documentation existante. Cela permet non seulement de capturer les connaissances, mais également d’inscrire l’accès aux connaissances dans le même processus que celui utilisé pour découvrir les sources de données. Ainsi, lorsqu’une personne utilise le catalogue pour découvrir une source de données, elle trouvera non seulement la source elle-même, mais également les connaissances de l’expert qui auparavant existaient uniquement dans la tête de ce dernier.

