<properties
    pageTitle="Résolution des problèmes liés au portail DocumentDB | Microsoft Azure"
    description="Découvrez pour résoudre les problèmes liés au portail Azure DocumentDB." 
    services="documentdb"
    documentationCenter=""
    authors="mimig1"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="mimig"/>

# Conseils de dépannage du portail Azure DocumentDB

Cet article décrit comment résoudre les problèmes de DocumentDB dans le portail Azure.

## Il manque des ressources

**Symptôme** : des bases de données ou collections sont absentes des panneaux du portail.

**Solution** : réduisez l’utilisation des applications pour fonctionner sous le quota de débit maximal pour la collection.

**Explication** : le portail est une application comme les autres, en cela qu’elle effectue des appels à votre base de données DocumentDB et à votre collection. Si vos demandes sont actuellement limitées en raison d’appels passés à partir d’une application distincte, le portail peut également être limité, ce qui empêchera l’affichage des ressources dans le portail. Pour résoudre le problème, corrigez la cause de l’utilisation d’un débit élevé, puis actualisez le panneau du portail. Vous trouverez plus d’informations sur la mesure et l’utilisation d’un débit inférieur dans la section [Débit](documentdb-performance-tips.md#throughput) de l’article [Conseils sur les performances](documentdb-performance-tips.md).
 
## Les pages et panneaux ne se chargent pas

**Symptôme** : les pages et panneaux ne s’affichent pas dans le portail.

**Solution** : réduisez l’utilisation des applications pour fonctionner sous le quota de débit maximal pour la collection.

**Explication** : le portail est une application comme les autres, en cela qu’elle effectue des appels à votre base de données DocumentDB et à votre collection. Si vos demandes sont actuellement limitées en raison d’appels passés à partir d’une application distincte, le portail peut également être limité, ce qui empêchera l’affichage des ressources dans le portail. Pour résoudre le problème, corrigez la cause de l’utilisation d’un débit élevé, puis actualisez le panneau du portail. Vous trouverez plus d’informations sur la mesure et l’utilisation d’un débit inférieur dans la section [Débit](documentdb-performance-tips.md#throughput) de l’article [Conseils sur les performances](documentdb-performance-tips.md).

## Le bouton Ajouter une collection est désactivé

**Symptôme** : sur le panneau Base de données, le bouton **Ajouter une collection** est désactivé.

**Explication** : si votre abonnement Azure est associé à des crédits d’avantage, comme les crédits gratuits proposés avec un abonnement MSDN et que vous avez utilisé toutes vos crédits pour le mois, vous ne pourrez pas créer de collections supplémentaires dans DocumentDB.

**Solution** : supprimez la limite de dépense de votre compte.

1. Dans le portail Azure, dans la barre de lancement, cliquez sur **Abonnements**, cliquez sur l’abonnement associé à la base de données DocumentDB, puis, dans le panneau **Abonnement**, cliquez sur **Gérer**. ![DocumentDB offre le choix entre plusieurs modèles de cohérence bien définis (souples)](./media/documentdb-portal-troubleshooting/documentdb-change-billing.png)

2. Dans la nouvelle fenêtre de navigateur, vous verrez que vous n’avez plus de crédits restants. Cliquez sur le bouton **Supprimer la limite de dépense** pour supprimer les dépenses uniquement pour la période de facturation actuelle ou indéfiniment. Puis terminez l’assistant pour ajouter ou confirmer vos informations de carte de crédit. ![DocumentDB offre le choix entre plusieurs modèles de cohérence bien définis (souples)](./media/documentdb-portal-troubleshooting/documentdb-remove-spending-limit.png)

 
## L’explorateur de requête se termine avec des erreurs

Consultez la page [Résolution des problèmes pour l’Explorateur de requêtes](documentdb-query-collections-query-explorer.md#troubleshoot).

## Aucune donnée disponible dans les vignettes d’analyse

Consultez la page [Résolution des problèmes des vignettes d’analyse](documentdb-monitor-accounts.md#troubleshooting).

## Aucun document n’est renvoyé dans l’Explorateur de documents

Consultez la page [Résolution des problèmes de l’Explorateur de documents](documentdb-view-json-document-explorer.md#troubleshoot).

## Étapes suivantes

Si vous rencontrez toujours des problèmes dans le portail, envoyez un e-mail à [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com) pour de l’aide, ou envoyez une demande d’assistance dans le portail en cliquant sur **Parcourir**, **Aide + Support**, puis en cliquant sur **Créer une demande de support**.

<!---HONumber=AcomDC_0831_2016-->