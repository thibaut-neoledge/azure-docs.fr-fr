<properties
   pageTitle="Installer Visual Studio et SSDT pour SQL Data Warehouse | Microsoft Azure"
   description="Installer les outils de développement Visual Studio et SSDT pour Azure SQL Data Warehouse"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/16/2016"
   ms.author="sonyama;barbkess"/>

# Installer Visual Studio 2015 et SSDT pour SQL Data Warehouse

Pour développer des applications pour SQL Data Warehouse, nous recommandons l’utilisation de Visual Studio 2015 avec la dernière version de SQL Server Data Tools (SSDT). Visual Studio 2013 avec SSDT est également pris en charge.

En outre, la **mise à jour Microsoft SQL Server pour les outils de base de données** est nécessaire pour exécuter des requêtes à partir de l’environnement de développement intégré (IDE) Visual Studio. Une fois cette extension installée, vous serez ensuite en mesure d’afficher les objets de base de données dans l’arborescence de l’Explorateur d’objets et exécuter des requêtes SQL Data Warehouse.

> [AZURE.NOTE] SQL Data Warehouse ne prend pas actuellement en charge les projets de base de données Visual Studio. Cette fonctionnalité sera ajoutée dans une version ultérieure.

## Étape 1 : Installer Visual Studio 2015

Suivez ces liens pour télécharger et installer Visual Studio 2015. Si Visual Studio 2013 ou 2015 est déjà installé sur votre machine, passez à l’étape 2 pour installer SSDT.

1. [Télécharger Visual Studio 2015][] à partir de Visual Studio Team Services.
2. Suivez le guide d’[installation de Visual Studio][] sur MSDN et sélectionnez les configurations par défaut.

## Étape 2 : Installer SSDT

Pour installer SSDT pour Visual Studio, vérifiez simplement la présence d’une mise à jour SSDT dans Visual Studio en procédant comme suit.

1. Dans Visual Studio, cliquez sur **Outils** / **Extensions et mises à jour...** / **Mises à jour**
2. Sélectionnez **Mises à jour de produits** puis recherchez **Mise à jour Microsoft SQL Server pour les outils de base de données**

Si aucune mise à jour n’est trouvée, la version installée est la version la plus récente. Pour vérifier que SSDT est installé, cliquez sur **Aide** / **À propos de Microsoft Visual Studio** et recherchez SQL Server Data Tools dans la liste.

## Étapes suivantes

Maintenant que vous disposez de la dernière version de SSDT, vous êtes prêt à vous [connecter][] à votre base de données.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connecter]: ./sql-data-warehouse-get-started-connect.md

<!--Other-->
[Télécharger Visual Studio 2015]: https://www.visualstudio.com/downloads/
[installation de Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx

<!---HONumber=AcomDC_0518_2016-->