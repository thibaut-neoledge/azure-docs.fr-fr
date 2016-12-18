---
title: "Résolution des problèmes liés au portail DocumentDB | Microsoft Docs"
description: "Découvrez pour résoudre les problèmes liés au portail Azure DocumentDB."
services: documentdb
documentationcenter: 
author: mimig1
manager: jhubbard
editor: monicar
ms.assetid: 36ad9bf4-2617-4347-ba29-edebf62fc3ec
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2016
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d41cbded31d64525a3e04ef742091c8d87760416


---
# <a name="azure-documentdb-portal-troubleshooting-tips"></a>Conseils de dépannage du portail Azure DocumentDB
Cet article décrit comment résoudre les problèmes de DocumentDB dans le portail Azure. 

## <a name="resources-are-missing"></a>Il manque des ressources
**Symptôme**: des bases de données ou collections sont absentes des panneaux du portail.

**Solution**: réduisez l’utilisation des applications pour fonctionner sous le quota de débit maximal pour la collection. 

**Explication**: le portail est une application comme les autres, en cela qu’elle effectue des appels à votre base de données DocumentDB et à votre collection. Si vos demandes sont actuellement limitées en raison d’appels passés à partir d’une application distincte, le portail peut également être limité, ce qui empêchera l’affichage des ressources dans le portail. Pour résoudre le problème, corrigez la cause de l’utilisation d’un débit élevé, puis actualisez le panneau du portail. Vous trouverez plus d’informations sur la mesure et l’utilisation d’un débit inférieur dans la section [Débit](documentdb-performance-tips.md#throughput) de l’article [Conseils sur les performances](documentdb-performance-tips.md).

## <a name="pages-or-blades-wont-load"></a>Les pages et panneaux ne se chargent pas
**Symptôme**: les pages et panneaux ne s’affichent pas dans le portail.

**Solution**: réduisez l’utilisation des applications pour fonctionner sous le quota de débit maximal pour la collection. 

**Explication**: le portail est une application comme les autres, en cela qu’elle effectue des appels à votre base de données DocumentDB et à votre collection. Si vos demandes sont actuellement limitées en raison d’appels passés à partir d’une application distincte, le portail peut également être limité, ce qui empêchera l’affichage des ressources dans le portail. Pour résoudre le problème, corrigez la cause de l’utilisation d’un débit élevé, puis actualisez le panneau du portail. Vous trouverez plus d’informations sur la mesure et l’utilisation d’un débit inférieur dans la section [Débit](documentdb-performance-tips.md#throughput) de l’article [Conseils sur les performances](documentdb-performance-tips.md).

## <a name="add-collection-button-is-disabled"></a>Le bouton Ajouter une collection est désactivé
**Symptôme** : dans le panneau Base de données, le bouton **Ajouter une collection** est désactivé.

**Explication**: si votre abonnement Azure est associé à des crédits d’avantage, comme les crédits gratuits proposés avec un abonnement MSDN et que vous avez utilisé toutes vos crédits pour le mois, vous ne pourrez pas créer de collections supplémentaires dans DocumentDB.

**Solution**: supprimez la limite de dépense de votre compte.

1. Dans le portail Azure, dans la barre de lancement, cliquez sur **Abonnements**, cliquez sur l’abonnement associé à la base de données DocumentDB puis, dans le panneau **Abonnement**, cliquez sur **Gérer**. 
    ![DocumentDB offre le choix entre plusieurs modèles de cohérence bien définis (souples)](./media/documentdb-portal-troubleshooting/documentdb-change-billing.png)
2. Dans la nouvelle fenêtre de navigateur, vous verrez que vous n’avez plus de crédits restants. Cliquez sur le bouton **Supprimer la limite de dépense** pour supprimer les dépenses uniquement pour la période de facturation actuelle ou indéfiniment. Puis terminez l’assistant pour ajouter ou confirmer vos informations de carte de crédit. 
    ![DocumentDB offre le choix entre plusieurs modèles de cohérence bien définis (souples)](./media/documentdb-portal-troubleshooting/documentdb-remove-spending-limit.png)

## <a name="query-explorer-completes-with-errors"></a>L’explorateur de requête se termine avec des erreurs
Consultez la page [Résolution des problèmes pour l’Explorateur de requêtes](documentdb-query-collections-query-explorer.md#troubleshoot).

## <a name="no-data-available-in-monitoring-tiles"></a>Aucune donnée disponible dans les vignettes d’analyse
Consultez la page [Résolution des problèmes des vignettes d’analyse](documentdb-monitor-accounts.md#troubleshooting).

## <a name="no-documents-returned-in-document-explorer"></a>Aucun document n’est renvoyé dans l’Explorateur de documents
Consultez la page [Résolution des problèmes de l’Explorateur de documents](documentdb-view-json-document-explorer.md#troubleshoot).

## <a name="next-steps"></a>Étapes suivantes
Si vous rencontrez toujours des problèmes dans le portail, envoyez un e-mail à [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com) pour obtenir de l’aide, ou envoyez une demande d’assistance dans le portail en cliquant sur **Parcourir**, **Aide + Support**, puis en cliquant sur **Créer une demande de support**.




<!--HONumber=Nov16_HO3-->


