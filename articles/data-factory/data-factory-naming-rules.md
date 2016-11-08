---
title: Data Factory - Règles d'affectation des noms | Microsoft Docs
description: Décrit les règles d'affectation de noms pour les entités Data Factory.
services: data-factory
documentationcenter: ''
author: spelluru
manager: jhubbard
editor: monicar

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2016
ms.author: spelluru

---
# Azure Data Factory - Règles d'affectation des noms
Le tableau suivant fournit des règles d'affectation de noms pour les artefacts Data Factory.

| Nom | Unicité du nom | Contrôles de validation |
|:--- |:--- |:--- |
| Data Factory |Unique sur Microsoft Azure. Les noms ne respectent pas la casse, c'est-à-dire que MyDF et mydf font référence à la même fabrique de données. |<ul><li>Chaque fabrique de données est liée à exactement un abonnement Azure.</li><li>Les noms d’objets doivent commencer par une lettre ou un nombre, et peuvent contenir uniquement des lettres, des chiffres et le tiret (-).</li><li>Chaque tiret (-) doit être immédiatement précédé et suivi par une lettre ou un chiffre. Les tirets consécutifs ne sont pas autorisés dans les noms de conteneurs.</li><li>Un nom peut comporter entre 3 et 63 caractères.</li></ul> |
| Services/tableaux/pipelines liés |Unique dans une fabrique de données. Les noms sont sensibles à la casse. |<ul><li>Nombre maximal de caractères dans un nom de tableau : 260.</li><li>Les noms d’objets doivent commencer par un numéro, une lettre ou un trait de soulignement (\_).</li><li>Les caractères suivants ne sont pas autorisés : « . », « + », « ? », « / », « < », « > », « * », « % », « & », « : », « \\ »</li></ul> |
| Groupe de ressources |Unique sur Microsoft Azure. Les noms sont sensibles à la casse. |<ul><li>Nombre maximal de caractères : 1000.</li><li>Un nom peut contenir des lettres, des chiffres et les caractères suivants : « - », « \_ », « , » et « . ».</li></ul> |

<!---HONumber=AcomDC_0914_2016-->