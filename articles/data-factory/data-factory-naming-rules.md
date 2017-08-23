---
title: "Règles d’affectation des noms des entités Azure Data Factory | Microsoft Docs"
description: "Décrit les règles d'affectation de noms pour les entités Data Factory."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: bc5e801d-0b3b-48ec-9501-bb4146ea17f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: shlo
ms.translationtype: Human Translation
ms.sourcegitcommit: 1e6f2b9de47d1ce84c4043f5f6e73d462e0c1271
ms.openlocfilehash: e8f190ec8f6cffdcc5748454ab7ae765648f2e30
ms.contentlocale: fr-fr
ms.lasthandoff: 06/21/2017

---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory - Règles d’affectation des noms
Le tableau suivant fournit des règles d'affectation de noms pour les artefacts Data Factory.

| Nom | Unicité du nom | Contrôles de validation |
|:--- |:--- |:--- |
| Data Factory |Unique sur Microsoft Azure. Les noms ne respectent pas la casse, c’est-à-dire que `MyDF` et `mydf` font référence à la même fabrique de données. |<ul><li>Chaque fabrique de données est liée à un seul abonnement Azure.</li><li>Les noms d’objet doivent commencer par une lettre ou un chiffre, et peuvent comporter uniquement des lettres, des chiffres et des tirets (-).</li><li>Chaque tiret (-) doit être immédiatement précédé et suivi par une lettre ou un chiffre. Les tirets consécutifs ne sont pas autorisés dans les noms de conteneurs.</li><li>Le nom doit contenir entre 3 et 63 caractères.</li></ul> |
| Services/tableaux/pipelines liés |Unique dans une fabrique de données. Les noms sont sensibles à la casse. |<ul><li>Un tableau peut contenir un maximum de 260 caractères.</li><li>Les noms d’objet doivent commencer par une lettre, un chiffre ou un trait de soulignement (_).</li><li>Les caractères suivants ne sont pas autorisés : « . », « + », « ? », « / », « < », « > », « * », « % », « & », « : », « \\ »</li></ul> |
| Groupe de ressources |Unique sur Microsoft Azure. Les noms sont sensibles à la casse. |<ul><li>Nombre maximal de caractères : 1 000.</li><li>Un nom peut contenir des lettres, des chiffres et les caractères suivants : « - », « _ », « , » et « . ».</li></ul> |


