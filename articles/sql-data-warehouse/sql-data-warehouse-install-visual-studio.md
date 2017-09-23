---
title: "Installer Visual Studio et SSDT pour SQL Data Warehouse | Microsoft Docs"
description: "Installer les outils de développement Visual Studio et SSDT pour Azure SQL Data Warehouse"
services: sql-data-warehouse
documentationcenter: NA
author: antvgski
manager: jhubbard
editor: 
ms.assetid: 0ed9b406-9b42-4fe6-b963-fe0a5b48aac1
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: connect
ms.date: 03/30/2017
ms.author: anvang;barbkess
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: f7023b78c241a7bc8014276cd0bfa455165b42cc
ms.contentlocale: fr-fr
ms.lasthandoff: 05/26/2017

---
# <a name="install-visual-studio-and-ssdt-for-sql-data-warehouse"></a>Installer Visual Studio et SSDT pour SQL Data Warehouse
Pour développer des applications pour SQL Data Warehouse, nous recommandons l’utilisation de la version la plus récente de Visual Studio avec la dernière version de SQL Server Data Tools (SSDT).  Visual Studio 2013 Update 5 avec SSDT est également pris en charge pour la compatibilité descendante.  

L’utilisation de Visual Studio avec SSDT vous permettra d’utiliser l’Explorateur d’objets SQL Server pour explorer visuellement les tables, les vues, les procédures stockées et un plus grand nombre d’objets dans SQL Data Warehouse, ainsi que pour exécuter des requêtes.

> [!NOTE]
> SQL Data Warehouse ne prend pas actuellement en charge les projets de base de données Visual Studio.  Cette fonctionnalité sera ajoutée dans une version ultérieure.
> 
> 

## <a name="step-1-install-visual-studio"></a>Étape 1 : Installer Visual Studio
Suivez ces liens pour télécharger et installer Visual Studio. Si Visual Studio 2013 ou ultérieur est déjà installé sur votre machine, passez à l’étape 2 pour installer SSDT.

1. [Téléchargez Visual Studio][].
2. Suivez le guide [d’installation de Visual Studio][Installing Visual Studio] sur MSDN et sélectionnez les configurations par défaut.

## <a name="step-2-install-ssdt"></a>Étape 2 : Installer SSDT
Pour installer SSDT pour Visual Studio, vérifiez simplement la présence d’une mise à jour SSDT dans Visual Studio en procédant comme suit.

1. Dans Visual Studio, cliquez sur **Outils** / **Extensions et mises à jour...** / **Mises à jour**
2. Sélectionnez **Mises à jour du produit** puis recherchez **Mise à jour Microsoft SQL Server pour les outils de base de données**.

Si aucune mise à jour n’est trouvée, vous devez avoir installé la version la plus récente.  Pour vérifier que SSDT est installé, cliquez sur **Aide** / **À propos de Microsoft Visual Studio** et recherchez SQL Server Data Tools dans la liste.  La dernière version de SSDT est 14.0.60525.0.  Si l’option d’installation n’est pas disponible à partir de Visual Studio, vous pouvez visiter la page [Téléchargement SSDT][SSDT Download] pour télécharger et installer SSDT manuellement.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous disposez de la dernière version de SSDT, vous êtes prêt à vous [connecter][connect] à SQL Data Warehouse.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Téléchargez Visual Studio]: https://www.visualstudio.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx

