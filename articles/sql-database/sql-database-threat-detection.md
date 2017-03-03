---
title: "Détection de menaces - Azure SQL Database | Microsoft Docs"
description: "Threat Detection permet de détecter les activités base de données anormales indiquant la présence potentielle de menaces de sécurité pour la base de données."
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: v-romcal
ms.assetid: b50d232a-4225-46ed-91e7-75288f55ee84
ms.service: sql-database
ms.custom: secure and protect
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 07/10/2016
ms.author: ronmat; ronitr
translationtype: Human Translation
ms.sourcegitcommit: 5d51a5ef3387b4c00079547b0f44ffe1f96bd77c
ms.openlocfilehash: eadaa3e7a279b6b92da1d0c026c3002297dfd298
ms.lasthandoff: 02/17/2017


---
# <a name="sql-database-threat-detection"></a>Détection de menaces pour les bases de données SQL

Threat Detection permet de détecter les activités base de données anormales indiquant la présence potentielle de menaces de sécurité pour la base de données.  La détection de menaces est actuellement en version préliminaire.

## <a name="overview"></a>Vue d'ensemble

Threat Detection fournit une nouvelle couche de sécurité qui permet aux clients de détecter les menaces potentielles et d’y répondre à mesure qu’elles se présentent en générant des alertes de sécurité sur les activités anormales.  Les utilisateurs peuvent explorer les événements suspects à l’aide de l’ [audit de base de données SQL](sql-database-auditing.md) pour déterminer s’ils sont le résultat d’une tentative d’accès, d’une violation ou d’une exploitation des données dans la base de données.
Threat Detection vous permet de réagir facilement aux menaces potentielles à la base de données sans avoir à acquérir une expertise de la sécurité ou à gérer des systèmes de surveillance de la sécurité avancés.

Par exemple, il détecte certaines activités de base de données anormales indiquant des tentatives d’injection SQL potentielles. L’injection SQL représente l’un des problèmes de sécurité auxquels sont le plus exposées les applications web, et est utilisée pour cibler les applications pilotées par des données. Les pirates exploitent les vulnérabilités des applications pour injecter des instructions SQL nuisibles dans les champs de saisie d’application afin de violer ou modifier les données contenues dans la base de données.

## <a name="next-steps"></a>Étapes suivantes

* Pour configurer et gérer la détection des menaces, consultez [Configuration et gestion de la détection de menaces dans le portail Azure](sql-database-threat-detection-portal.md).

