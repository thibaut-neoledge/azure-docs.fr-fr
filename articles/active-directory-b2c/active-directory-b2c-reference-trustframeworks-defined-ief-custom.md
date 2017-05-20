---
title: "Azure Active Directory B2C : informations de référence sur les infrastructures de confiance | Microsoft Docs"
description: "Une rubrique sur les stratégies personnalisées Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/25/2017
ms.author: joroja
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 019dd66d5abe4242ffb6cdc3276929dcd7f04466
ms.contentlocale: fr-fr
ms.lasthandoff: 05/08/2017


---

# <a name="defining-trust-frameworks-with-azure-ad-b2c-identity-experience-framework"></a>Définition des infrastructures de confiance avec l’infrastructure d’expérience d’identité Azure AD B2C

Les stratégies personnalisées Azure AD B2C reposant sur l’infrastructure d’expérience d’identité (IEF) fournissent à votre organisation un service centralisé qui simplifie la fédération des identités dans une grande communauté d’intérêt sous la forme d’une relation d’approbation unique et d’un échange de métadonnées unique.

Vous aurez besoin de stratégies personnalisées Azure AD B2C reposant sur l’infrastructure d’expérience d’identité pour répondre aux questions suivantes.

- *Quelles stratégies de conformité, de sécurité, de confidentialité et de protection de données doivent être appliquées ?*
- *Quels sont les contacts et les processus pour devenir un participant agréé ?*
- *Quels sont les fournisseurs d’informations d’identité agréés (également appelés fournisseurs de revendications) et que proposent-ils ?*
- *Quelles sont les parties de confiance agréées [et que nécessitent-elles éventuellement] ?*
- *Quelles sont les exigences techniques d’interopérabilité réseau pour les participants ?*
- *Quelles sont les règles de « runtime » opérationnel à appliquer pour échanger des informations d’identité numérique ?*

Pour répondre à toutes ces questions, les stratégies personnalisées Azure AD B2C reposant sur l’IEF utilisent la structure Trust Framework (TF). Décrivons cette structure et les fonctionnalités offertes dans ce cadre.

## <a name="understanding-the-trust-framework-and-federation-management-foundation"></a>Présentation de l’infrastructure de confiance (TF) et de la gestion de la fédération

Une structure de ce type doit être interprétée comme une spécification écrite des stratégies d’identité, de sécurité, de confidentialité et de protection des données auxquelles doivent se conformer les membres d’une communauté d’intérêt.

L’identité fédérée fournit une base pour la protection de l’identité des utilisateurs sur Internet.  En déléguant la gestion des identités à des tierces parties, l’identité numérique unique d’un utilisateur peut être réutilisée avec plusieurs parties de confiance.  

La protection de l’identité exige en effet que les fournisseurs d’identité et d’attributs respectent certaines pratiques et stratégies opérationnelles, de sécurité et de confidentialité.  Ne pouvant effectuer d’inspections directes, les parties de confiance doivent développer des relations d’approbation avec les fournisseurs d’identité et d’attributs avec lesquels elles choisissent de travailler.  Alors que le nombre de consommateurs et de fournisseurs d’informations d’identité numérique augmente à toute vitesse, il n’est plus possible de continuer à gérer ces relations d’approbation par paire, ou même à procéder à un échange par paire des métadonnées techniques requises pour la connectivité réseau.  Les concentrateurs de fédération n’ont pas réussi à résoudre totalement ces problèmes.

Les infrastructures de confiance (TF) constituent l’élément central du modèle d’infrastructure de confiance Open Identity Exchange (OIX) où chaque communauté d’intérêt est régie par une spécification TF particulière. Ce type de spécification TF définit :

- Les mesures de sécurité et de confidentialité pour la communauté d’intérêt avec la définition de plusieurs éléments :
    - Les niveaux de garantie proposés/requis par les participants, c’est-à-dire un ensemble ordonné d’évaluations de confiance relatives à l’authenticité des informations d’identité numérique.
    - Les niveaux de protection proposés/requis par les participants, c’est-à-dire un ensemble ordonné d’évaluations de confiance relatives à la protection des informations d’identité numérique gérées par les participants de la communauté d’intérêt.
- Les informations d’identité numérique proposées/requises par les participants.
- Les stratégies techniques pour la production et la consommation des informations d’identité numérique, qui serviront à calculer les niveaux de garantie et de protection. Ces stratégies écrites incluent généralement les catégories de stratégies suivantes :
    - Stratégies de vérification de l’identité : *quel est le niveau de vérification des informations d’identité d’une personne ?*
    - Stratégies de sécurité : *quel est le niveau de protection de la confidentialité et de l’intégrité des informations ?*
    - Stratégies de confidentialité : *quel contrôle un utilisateur a-t-il sur ses données personnelles ?*
    - Stratégies de survie : continuité et protection des données personnelles si un fournisseur cesse son activité.
- Les profils techniques pour la production et la consommation des informations d’identité numérique. Ces profils :
    - Définissent l’étendue des interfaces ayant accès aux informations d’identité numérique pour chaque niveau de garantie ;
    - Décrivent les exigences techniques d’interopérabilité réseau.
- Les descriptions des différents rôles pouvant être attribués aux participants de la communauté, ainsi que des qualifications requises pour exécuter ces rôles.

Par conséquent, une spécification TF détermine comment les informations d’identité sont échangées entre les participants de la communauté d’intérêt : les parties de confiance, les fournisseurs d’identité et d’attributs, et les vérificateurs d’attributs.

Dans le jargon de ce modèle TF OIX, une spécification TF comprend un ou plusieurs documents qui servent de référence pour la gouvernance de la communauté d’intérêt qui régule la revendication et la consommation des informations d’identité numérique au sein de la communauté d’intérêt. Cela implique un ensemble documenté de stratégies et de procédures, conçues pour établir l’approbation des identités numériques utilisées pour les transactions en ligne entre les différents membres d’une communauté d’intérêt.  **Une spécification TF définit les règles pour la création d’un écosystème d’identité fédérée viable pour une communauté donnée.**

Les avantages de cette approche sont aujourd’hui globalement reconnus et il ne fait aucun doute que les spécifications TF faciliteront le développement d’écosystèmes d’identité numérique avec des caractéristiques de sécurité, de garantie et de confidentialité vérifiables, qui pourront ainsi être réutilisés pour d’autres communautés d’intérêt.

Pour cette raison, les stratégies personnalisées Azure AD B2C reposant sur l’IEF utilisent cette spécification comme base pour la représentation des données afin de faciliter l’interopérabilité.  

Les stratégies personnalisées Azure AD B2C reposant sur l’IEF représentent une spécification TF contenant à la fois des données humaines et machine : certaines sections de ce modèle (celles faisant généralement référence à la gouvernance) sont représentées en tant que références à la documentation de sécurité et de confidentialité disponible, ainsi qu’aux procédures associées, le cas échéant, tandis que les autres sections décrivent en détail les métadonnées de configuration et les règles de runtime pour faciliter l’automatisation des opérations.

## <a name="understanding-trust-framework-policies"></a>Présentation des stratégies Trust Framework

En termes d’implémentation, la spécification TF ci-dessus se compose de stratégies personnalisées Azure AD B2C reposant sur l’IEF dans un ensemble de stratégies qui permettent de contrôler totalement les comportements et expériences en matière d’identité.  Les stratégies personnalisées Azure AD B2C reposant sur l’IEF vous permettent en effet de créer votre propre spécification TF via ces stratégies déclaratives qui peuvent définir et configurer :

- Les références de documents définissant l’écosystème d’identité fédérée de la communauté associé à la spécification TF. Il s’agit de liens vers la documentation TF. Les règles de « runtime » opérationnel (prédéfinies), ou les parcours utilisateur qui automatisent et/ou contrôlent l’échange et l’utilisation de revendications. Ces parcours utilisateur sont associés à un niveau de garantie (et de protection). Une stratégie peut donc contenir des parcours utilisateur avec différents niveaux de garantie (et de protection).
- Les fournisseurs d’identité et d’attributs, ou fournisseurs de revendications, de la communauté d’intérêt et les profils techniques qu’ils prennent en charge, ainsi que leur agrément de niveau de garantie/protection (hors bande) respectif.
- L’intégration avec les vérificateurs d’attributs, ou les fournisseurs de revendications.
- Les parties de confiance de la communauté (par inférence).
- Les métadonnées pour établir des communications réseau entre les participants. Ces métadonnées et les profils techniques seront utilisés au cours d’une transaction pour analyser l’interopérabilité réseau entre la partie de confiance et les autres participants de la communauté.
- La conversion de protocole, le cas échéant (SAML, OAuth2, WS-Federation et OpenID Connect).
- Les exigences d’authentification.
- L’orchestration multifacteur, le cas échéant.
- Un schéma partagé pour toutes les revendications disponibles et des mappages aux participants d’une communauté d’intérêt.
- Toutes les transformations de revendications, ainsi que la réduction possible des données dans ce contexte, pour maintenir l’échange et l’utilisation des revendications.
- La liaison et le chiffrement.
- Le stockage des revendications.

> [!NOTE]
> Nous faisons référence à tous les types d’informations d’identité pouvant être échangés en tant que revendications : revendications concernant les informations d’authentification d’un utilisateur, la vérification de l’identité, le périphérique de communication, l’emplacement physique, les attributs d’identification personnelle, etc.  
>
> Nous utilisons le terme *revendications* plutôt que le terme « attributs » qui en est un sous-ensemble. En effet, dans le cas de transactions en ligne, ce ne sont pas des faits qui peuvent être vérifiés directement par la partie de confiance ; ce sont des assertions ou des revendications concernant des faits pour lesquels la partie de confiance doit développer une confiance suffisante pour autoriser la transaction demandée par l’utilisateur.  
>
> C’est également dû au fait que les stratégies personnalisées Azure AD B2C reposant sur l’IEF sont conçues pour simplifier l’échange de tous les types d’informations d’identité numériques de manière cohérente, peu importe si le protocole sous-jacent a été défini pour l’authentification des utilisateurs ou la récupération des attributs.  De même, nous utiliserons le terme « fournisseurs de revendications » pour désigner collectivement les fournisseurs d’identité, les fournisseurs d’attributs et les vérificateurs d’attributs lorsque nous ne souhaitons pas faire la distinction entre leurs fonctions respectives.   

Par conséquent, elles déterminent comment les informations d’identité sont échangées entre une partie de confiance, les fournisseurs d’identité et d’attributs, et les vérificateurs d’attributs. Elles spécifient les fournisseurs d’identité et d’attributs requis pour l’authentification d’une partie de confiance. Elles doivent être considérées comme un langage spécifique à un domaine (DSL), autrement dit, un langage informatique dédié à un domaine d’application spécifique avec un héritage, des instructions *IF* et du polymorphisme.

Ces stratégies constituent la partie de données machine de la structure TF dans les stratégies personnalisées Azure AD B2C reposant sur l’IEF avec tous les détails opérationnels, notamment les métadonnées et les profils techniques des fournisseurs de revendications, la définition du schéma de revendications, les fonctions de transformation des revendications, ainsi que les parcours utilisateur renseignés, ce dans le but de faciliter l’automatisation et l’orchestration des opérations.  

Elles sont supposées être des *documents dynamiques* dans les stratégies personnalisées Azure AD B2C reposant sur l’IEF car il y a de fortes chances que leur contenu change au fil du temps, notamment concernant les participants actifs déclarés dans les stratégies et dans certaines situations, les conditions requises pour devenir participant.  
La configuration et la gestion de la fédération sont grandement simplifiées en épargnant aux parties de confiance la reconfiguration permanente de la connectivité et de l’approbation lors de l’arrivée ou du départ des différents fournisseurs/vérificateurs de revendications de la communauté représentée par l’ensemble de stratégies.

L’interopérabilité constitue un autre défi au moment d’intégrer des fournisseurs/vérificateurs de revendications supplémentaires dans la mesure où les parties de confiance sont peu susceptibles de prendre en charge tous les protocoles nécessaires. Les stratégies personnalisées Azure AD B2C reposant sur l’IEF répondent à ce problème en prenant en charge les protocoles standard et en appliquant des parcours utilisateur spécifiques pour transposer les demandes lorsque les parties de confiance et les fournisseurs d’attributs ne prennent pas en charge le même protocole.  

Les parcours utilisateur incluent les profils et les métadonnées des protocoles qui seront utilisés pour analyser l’interopérabilité réseau entre la partie de confiance et les autres participants.  Il existe également des règles de runtime opérationnel qui seront appliquées aux messages de réponse/demande d’échange d’informations d’identité afin de garantir la conformité avec les stratégies publiées dans le cadre de la spécification TF. L’idée de parcours utilisateur est essentielle pour la personnalisation de l’expérience client et dévoile le fonctionnement du système au niveau du protocole.

Sur cette base, les portails et applications des parties de confiance peuvent, selon leur contexte, appeler des stratégies personnalisées Azure AD B2C reposant sur l’IEF en transmettant le nom d’une stratégie spécifique afin d’obtenir le comportement et l’échange d’informations attendus sans risque ni complication.

