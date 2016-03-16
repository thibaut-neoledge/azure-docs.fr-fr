<properties
   pageTitle="Installer Visual Studio et/ou SSDT pour SQL Data Warehouse | Microsoft Azure"
   description="Installer les outils de développement Visual Studio et/ou SSDT pour Azure SQL Data Warehouse"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/03/2016"
   ms.author="mausher;barbkess;sonyama"/>

# Installer Visual Studio 2015 et/ou SSDT pour SQL Data Warehouse

Pour développer des applications pour SQL Data Warehouse, nous recommandons l’utilisation de Visual Studio 2013 ou une version ultérieure combinée avec la version la plus récente de SQL Server Data Tools (SSDT).

Pour exécuter des requêtes à partir de l’environnement de développement intégré (IDE) Visual Studio, vous devez seulement installer SSDT. Cette commande installera l’environnement de développement intégré de Visual Studio avec SSDT, et vous pouvez donc utiliser l’Explorateur d’objets SQL Server pour vous connecter à votre serveur SQL Azure. Vous serez en mesure d’afficher et d’exécuter des requêtes sur vos bases de données SQL Data Warehouse.


## Étape 1 : Téléchargez et installez Visual Studio

Si vous choisissez d’installer Visual Studio, vous pouvez utiliser Visual Studio 2013 ou Visual Studio 2015 avec SQL Data Warehouse. Si Visual Studio 2013 ou 2015 est déjà installé sur votre machine, passez à l’étape 2 pour installer SSDT.

Pour installer Visual Studio 2015 :

1. [Télécharger Visual Studio 2015](https://www.visualstudio.com/downloads) à partir de Visual Studio Team Services.
2. Procédez à l’installation en suivant le guide d’[installation de Visual Studio](https://msdn.microsoft.com/library/e2h7fzkw.aspx) sur MSDN et sélectionnez les configurations par défaut

## Étape 2 : Téléchargez et installez la version préliminaire de SQL Server Data Tools (SSDT)

Que vous ayez ou non installé Visual Studio, vous avez toujours besoin de la version la plus récente de SQL Server Data Tools (SSDT), qui prend en charge SQL Data Warehouse.

Pour installer la dernière version de SSDT :

1. [Télécharger la version préliminaire de SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) pour Visual Studio 2013 ou 2015.
2. Procédez à l’installation des instructions d’installation sur le site de téléchargement.

## Étapes suivantes

Maintenant que vous disposez de la dernière version de SSDT, vous êtes prêt à vous [connecter](./sql-data-warehouse-get-started-connect.md) à votre base de données.

<!--Anchors-->

<!--Image references-->

<!---HONumber=AcomDC_0309_2016-->