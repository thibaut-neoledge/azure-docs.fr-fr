<properties
	pageTitle="Limitations de la surface d’exposition et problèmes de blocage pour Stretch Database | Microsoft Azure"
	description="Découvrez les problèmes de blocage que vous devez résoudre avant de pouvoir activer Stretch Database."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="douglasl"/>

# Limitations de la surface d’exposition et problèmes de blocage pour Stretch Database

Découvrez les problèmes de blocage que vous devez résoudre avant de pouvoir activer Stretch Database.

## <a name="Limitations"></a>Problèmes de blocage
Dans la version préliminaire actuelle de SQL Server 2016, les éléments suivants rendent une table inéligible pour Stretch.

**Propriétés des tables**
-   Plus de 1 023 colonnes

-   Plus de 998 index

-   Tables qui contiennent des données FILESTREAM

-   FileTables

-   Tables répliquées

-   Tables utilisant activement le suivi des modifications ou la capture de données modifiées

-   Tables à mémoire optimisée

**Types de données et propriétés des colonnes**
-   timestamp

-   sql\_variant

-   XML

-   geometry

-   Geography

-   hierarchyid

-   Types CLR définis par l’utilisateur (UDT)

**Types de colonnes**
-   COLUMN\_SET

-   Colonnes calculées

**Contraintes**
-   Contraintes CHECK

-   Contraintes par défaut

-   Contraintes de clés étrangères qui référencent la table

**Index**
-   Index en texte intégral

-   Index XML

-   Index spatiaux

-   Vues indexées qui référencent la table

## <a name="Caveats"></a>Limitations et inconvénients des tables Stretch
Dans la version préliminaire actuelle de SQL Server 2016, les tables compatibles Stretch ont les limitations et les inconvénients suivants.

-   L’unicité n’est pas appliquée pour les contraintes UNIQUES et de contraintes de CLÉ PRIMAIRE sur une table compatible Stretch.

-   Vous ne pouvez pas exécuter d’opérations METTRE À JOUR ou SUPPRIMER dans une table compatible Stretch.

-   Il est impossible d’INSÉRER dans la table de base de données SQL Azure distante.

-   Il est impossible de créer un index pour une vue comprenant des tables compatibles Stretch.

-   Il est impossible de mettre à jour ou de supprimer une vue comprenant des tables compatibles Stretch. Cependant, vous pouvez insérer dans une vue comprenant des tables compatibles Stretch.

-   Les filtres sur les index ne sont pas propagés à la table distante.

## Voir aussi
[Identifier des bases de données et des tables pour Stretch Database en exécutant Stretch Database Advisor](sql-server-stretch-database-identify-databases.md) [Activer Stretch Database pour une base de données](sql-server-stretch-database-enable-database.md) [Activer Stretch Database pour une table](sql-server-stretch-database-enable-table.md)

<!---------HONumber=AcomDC_0309_2016-->