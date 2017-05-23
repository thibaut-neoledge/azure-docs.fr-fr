---
title: Indexeurs dans le service Recherche Azure | Microsoft Docs
description: "Analysez une base de données Azure SQL, Cosmos DB ou le stockage Azure pour extraire les données pouvant faire l’objet d’une recherche et remplir un index Recherche Azure."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 34a7694c-8fd9-46b1-8900-cefdd7236323
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 05/01/2017
ms.author: heidist
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 52b154895fca9fc465a9c6cc2fb6bf2d5384b057
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017

---

# <a name="indexers-in-azure-search"></a>Indexeurs dans Azure Search
> [!div class="op_single_selector"]
>
> * [Vue d'ensemble](search-indexer-overview.md)
> * [Portail](search-import-data-portal.md)
> * [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
> * [Azure Cosmos DB](search-howto-index-documentdb.md)
> * [Stockage Blob Azure](search-howto-indexing-azure-blob-storage.md)
> * [Stockage de tables Azure](search-howto-indexing-azure-tables.md)
>
>

Dans Azure Search, un **indexeur** est un analyseur qui extrait les données et métadonnées pouvant faire l’objet d’une recherche d’une source de données externe et renseigne un index en fonction des mappages champ à champ entre l’index et votre source de données. Cette approche est parfois appelée « modèle d’extraction », car le service extrait des données sans que vous ayez à écrire un code qui envoie des données à un index.

Vous pouvez utiliser un indexeur comme seul moyen d’ingestion des données ou utiliser une combinaison de techniques qui incluent l’utilisation d’un indexeur pour charger uniquement certains champs dans l’index.

Vous pouvez exécuter des indexeurs à la demande ou en fonction d’une planification d’actualisation des données périodique qui s’exécute jusqu’à une fois toutes les quinze minutes. Des mises à jour plus fréquentes requièrent un modèle d’émission qui met à jour simultanément les données dans Azure Search et dans votre source de données externe.

## <a name="approaches-for-creating-and-managing-indexers"></a>Méthodes de création et de gestion des indexeurs
Pour les indexeurs mis à la disposition générale tels qu’Azure SQL ou Azure Cosmos DB, vous pouvez créer et gérer les indexeurs à l’aide des méthodes suivantes :

* [Portail > Assistant Importer des données](search-get-started-portal.md)
* [API REST du service](https://msdn.microsoft.com/library/azure/dn946891.aspx)
* [Kit SDK .NET](https://msdn.microsoft.com/library/azure/microsoft.azure.search.iindexersoperations.aspx)

## <a name="basic-configuration-steps"></a>Étapes de configuration de base
Les indexeurs peuvent offrir des fonctionnalités propres à la source de données. À cet égard, certains aspects de la configuration de l’indexeur ou de la source de données varient en fonction du type d’indexeur. Cependant, tous les indexeurs présentent une composition et des exigences de base identiques. Les étapes communes à tous les indexeurs sont décrites ci-dessous.

### <a name="step-1-create-an-index"></a>Étape 1 : Création d’un index
Un indexeur automatise certaines tâches liées à l’ingestion de données, mais la création d’un index n’en fait pas partie. Au préalable, vous devez disposer d’un index prédéfini présentant des champs qui correspondent à ceux de votre source de données externe. Pour plus d’informations sur la structuration d’un index, consultez l’article [Create an Index (Azure Search REST API)](https://msdn.microsoft.com/library/azure/dn798941.aspx)(Création d’un index (API REST Azure Search)).

### <a name="step-2-create-a-data-source"></a>Étape 2 : Création d’une source de données
Un indexeur extrait les données d’une **source de données** qui contient des informations telles qu’une chaîne de connexion. Actuellement, les sources de données suivantes sont prises en charge :

* [Base de données SQL Azure ou SQL Server sur une machine virtuelle Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-documentdb.md)
* [Stockage d’objets blob azure](search-howto-indexing-azure-blob-storage.md), utilisé pour extraire du texte de fichiers PDF, HTML, XML ou de documents Office
* [Stockage de tables Azure](search-howto-indexing-azure-tables.md)

Les sources de données sont configurées et gérées indépendamment des indexeurs qui les utilisent. Autrement dit, une source de données peut être utilisée par plusieurs indexeurs pour charger plusieurs index à la fois.

### <a name="step-3create-and-schedule-the-indexer"></a>Étape 3 : Création et planification de l’indexeur
La définition de l’indexeur est une structure qui spécifie l’index, la source de données et une planification. Un indexeur peut faire référence à une source de données d’un autre service, à condition que celle-ci appartienne au même abonnement. Pour plus d’informations sur la structuration d’un indexeur, consultez l’article [Create Indexer (Azure Search REST API)](https://msdn.microsoft.com/library/azure/dn946899.aspx)(Création d’un indexeur (API REST Azure Search)).

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez la structure de base, l’étape suivante consiste à passer en revue les exigences et les tâches propres à chaque type de source de données.

* [Base de données SQL Azure ou SQL Server sur une machine virtuelle Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-documentdb.md)
* [Stockage d’objets blob azure](search-howto-indexing-azure-blob-storage.md), utilisé pour extraire du texte de fichiers PDF, HTML, XML ou de documents Office
* [Stockage de tables Azure](search-howto-indexing-azure-tables.md)
* [Indexation d’objets blob CSV avec l’indexeur d’objets blob Recherche Azure](search-howto-index-csv-blobs.md)
* [Indexation d’objets blob JSON avec l’indexeur d’objets blob Recherche Azure](search-howto-index-json-blobs.md)

