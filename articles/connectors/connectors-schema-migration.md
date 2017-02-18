---
title: "Comment migrer des applications logiques vers la version préliminaire du schéma du 01/08/2015 | Microsoft Docs"
description: "Vous pouvez facilement migrer vos applications logiques vers la dernière version du schéma. Suivez simplement cette procédure."
services: logic-apps
documentationcenter: 
author: MSFTMAN
manager: erikre
editor: 
tags: connectors
ms.assetid: 3e177e49-fd69-43e9-9b9b-218abb250c31
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/23/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 30abc823a72431dfd12f1051434191ea904533f6
ms.openlocfilehash: a5a73a9f124e5339b61dbc49021444a208a471f0


---
# <a name="how-to-migrate-logic-apps-to-schema-version-2015-08-01-preview"></a>Comment migrer des applications logiques vers la version préliminaire du schéma du 01/08/2015
Pour migrer vos applications logiques existantes vers le nouveau schéma, procédez comme suit :  

1. Ouvrez votre application logique dans le portail Azure  
2. Cliquez sur Mettre à jour le schéma.
   
   ![Icône API][step1]   
   La page Mettre à jour le schéma fournit un lien vers un document contenant des informations sur les améliorations apportées au nouveau schéma : ![Icône API][step2]

> [!NOTE]
> Lorsque vous sélectionnez **Mettre à jour le schéma**, les étapes de migration sont automatiquement exécutées avec le code de sortie correspondant. Vous pouvez utiliser ce code pour mettre à jour la définition. Veillez cependant à respecter les bonnes pratiques de codage, telles que celles décrites dans la section **Meilleures pratiques** ci-dessous.
> 
> 

## <a name="best-practices-when-migrating-your-logic-apps-to-the-latest-schema-version"></a>Meilleures pratiques lors de la migration de vos applications logiques vers la dernière version du schéma :
* Copiez le script migré vers une nouvelle application logique : n’écrasez pas l’ancienne version tant que le test n’est pas terminé et que vous n’avez pas vérifié que l’application migrée fonctionne comme prévu.
* Testez votre application logique **avant** sa mise en production
* Une fois la migration terminée, commencez à mettre à jour vos applications logiques pour utiliser les [API gérées](apis-list.md) dès que possible. Par exemple, vous pouvez commencer à utiliser DropBox v2 partout où vous utilisez DropBox v1.

## <a name="whats-next"></a>Étapes suivantes
* [Apprenez à migrer manuellement vos applications logiques](../logic-apps/logic-apps-schema-2015-08-01.md)

<!--Icon references-->
[step1]: ./media/connectors-schema-migration/migrateschema1.png
[step2]: ./media/connectors-schema-migration/migrateschema2.png









<!--HONumber=Jan17_HO3-->


