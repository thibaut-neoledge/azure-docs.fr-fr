---
title: "Test des requêtes Azure Stream Analytics | Microsoft Docs"
description: "Test des requêtes dans les travaux Stream Analytics"
keywords: "test des requêtes, dépannage des requêtes"
documentation center: 
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeffstok
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: 039ea499f3bfc594472ddc6de59220c4600769f1
ms.contentlocale: fr-fr
ms.lasthandoff: 05/01/2017


---
# <a name="test-azure-stream-analytics-queries-in-the-azure-portal"></a>Tester les requêtes Azure Stream Analytics dans le portail Azure

Avec Azure Stream Analytics, vous pouvez tester des requêtes dans le portail sans avoir à démarrer ou arrêter un travail.

## <a name="test-the-input"></a>Tester l’entrée

1. Pour tester les exemples de données d’entrée, cliquez sur une de vos entrées, puis sélectionnez **Charger un exemple de données du fichier**.

    ![Test des requêtes dans l’éditeur de requête Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

2. Une fois que le téléchargement est terminé, cliquez sur **Tester** pour tester cette requête dans les exemples de données que vous avez fournis.

    ![Exemple de données de test des requêtes dans l’éditeur de requête Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

Si vous souhaitez enregistrer la sortie test pour une utilisation ultérieure, la sortie de votre requête s’affiche dans le navigateur avec un lien vers les résultats du téléchargement. Vous pouvez maintenant facilement et de manière itérative modifier votre requête et la tester à plusieurs reprises pour voir comment la sortie change.

![Exemple de sortie de l’éditeur de requête Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

Avec plusieurs sorties utilisées dans une requête, vous pouvez voir les résultats des deux sorties séparément et basculer facilement entre elles.

Une fois que vous êtes satisfait des résultats affichés dans le navigateur, vous pouvez enregistrer votre requête, lancer votre travail et le regarder traiter les événements sans erreur.

## <a name="get-help"></a>Obtenir de l'aide

Pour obtenir une assistance, consultez le [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes

* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d’Azure Stream Analytics](stream-analytics-get-started.md)
* [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

