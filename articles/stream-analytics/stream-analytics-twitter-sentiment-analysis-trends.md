---
title: "Analyse des sentiments Twitter en temps réel avec Azure Stream Analytics | Microsoft Docs"
description: "Découvrez comment utiliser Stream Analytics pour l’analyse de sentiments Twitter en temps réel. Aide pas à pas allant de la génération d’événements à la gestion des données sur un tableau de bord en direct."
keywords: "analyse de tendances twitter en temps réel, analyse de sentiments, analyse des réseaux sociaux, exemple d’analyse de tendances"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 42068691-074b-4c3b-a527-acafa484fda2
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: d4fae06cb240c1687b059ae991b8d09f94e2ffc3
ms.lasthandoff: 05/01/2017

---

# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Analyse de sentiments Twitter en temps réel dans Azure Stream Analytics

Apprenez à créer une solution d’analyse de sentiments pour l’analyse des réseaux sociaux en intégrant des événements Twitter dans Azure Event Hubs. Dans ce scénario, vous allez écrire une requête Azure Stream Analytics pour analyser les données. Ensuite, vous allez soit stocker les résultats en vue d’une utilisation ultérieure, soit utiliser un tableau de bord et [Power BI](https://powerbi.com/) pour fournir des informations en temps réel.

Les outils d’analyse des réseaux sociaux aident les organisations à comprendre les tendances. Les sujets populaires sont les sujets significatifs et avis apparaissant dans un grand nombre de billets sur les réseaux sociaux. L’analyse de sentiments, aussi appelée *exploration d’opinions*, utilise des outils d’analyse de réseaux sociaux pour déterminer les attitudes envers un produit, une idée, etc.

L’analyse de tendances Twitter en temps réel constitue un excellent exemple d’outil d’analyse, car le modèle d’abonnement hashtag vous permet de suivre des mots clés spécifiques et de développer l’analyse de sentiments sur le flux.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Scénario : analyse de sentiments sur les réseaux sociaux en temps réel

Une entreprise qui dispose d’un site web de médias souhaite obtenir un avantage sur ses concurrents en présentant des contenus immédiatement pertinents pour ses lecteurs. Elle utilise l’analyse des réseaux sociaux sur des sujets pertinents pour ses lecteurs en effectuant une analyse de sentiments en temps réel des données de Twitter.

Pour identifier les tendances en temps réel sur Twitter, l’entreprise doit analyser en temps réel le volume et les sentiments des tweets relatifs aux principaux sujets. En d’autres termes, nous avons besoin d’un moteur d’analyse de sentiments basé sur le flux de ce réseau social.

## <a name="prerequisites"></a>Composants requis

* Un abonnement Azure
* Un compte Twitter et un [jeton d’accès OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)
* Le fichier [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) de GitHub
* Facultatif : code source pour le client Twitter à partir de [GitHub](https://aka.ms/azure-stream-analytics-twitterclient)

## <a name="create-an-event-hub-input"></a>Création d’une entrée de hub d’événements

L’exemple d’application génère des événements et les transmet vers une instance de hub d’événements (ou simplement « hub d’événement » pour faire plus court). Les hubs d’événements Service Bus constituent la méthode la plus efficace pour la réception d’événements dans Stream Analytics. Pour plus d’informations, consultez la documentation d’Event Hubs sur [Documentation d’Azure Service Bus](/azure/service-bus/).

### <a name="create-an-event-hub"></a>Création d'un concentrateur d'événements

Procédez comme suit pour créer un Event Hub :

1. Dans le [Portail Azure](https://portal.azure.com), sélectionnez **NOUVEAU**, saisissez **Event Hubs**, puis sélectionnez **Event Hubs** à partir de la recherche qui en résulte. Sélectionnez ensuite **Créer**.

2. Fournissez un nom pour Event Hub et créez un groupe de ressources. Nous avons spécifié `socialtwitter-eh` et `socialtwitter-rg` respectivement. Sélectionnez l’option pour épingler le compte au tableau de bord, puis sélectionnez le bouton **Créer**.

3. Une fois le déploiement terminé, sélectionnez le concentrateur d’événements. Ensuite, sous **Entités**, sélectionnez **Concentrateurs d’événements**.

4. Pour créer le concentrateur d’événements, sélectionnez le bouton **+ Concentrateur d’événements**. Fournissez à nouveau votre nom (le nôtre était `socialtwitter-eh`), puis sélectionnez **Créer**.

5. Pour accorder l’accès au hub d’événements, vous devez créer une stratégie d’accès partagé. Sélectionnez le concentrateur d’événements, puis, sous **Paramètres**, sélectionnez **Stratégies d’accès partagé**.

6. Sous **Stratégies d’accès partagé**, créez une stratégie ayant les autorisations **GÉRER** en sélectionnant **+ Ajouter**. Attribuez un nom à la stratégie et cochez **Gérer**, puis sélectionnez **Créer**.

7. Sélectionnez votre nouvelle stratégie une fois qu’elle a été créée, puis sélectionnez le bouton Copier pour l’entité **CHAÎNE DE CONNEXION - CLÉ PRIMAIRE**. Nous en aurons besoin plus tard dans l’exercice. Ensuite, revenez au tableau de bord.

![Points de terminaison de la stratégie d’accès partagé](./media/stream-analytics-twitter-sentiment-analysis-trends/keysandendpoints.png)

## <a name="configure-and-start-the-twitter-client-application"></a>Configurer et démarrer l’application client Twitter

Nous avons créé une application client capable de se connecter aux données de Twitter par le biais des [API Streaming de Twitter](https://dev.twitter.com/streaming/overview) pour collecter les événements Tweet sur un ensemble de sujets paramétrable. L’outil open source [Sentiment140](http://help.sentiment140.com/) affecte une valeur de sentiment à chaque tweet, comme suit :

* 0 = négatif
* 2 = neutre
* 4 = positif

Les événements Tweet sont ensuite transmis au hub d’événements.  

### <a name="set-up-the-application"></a>Configurer d’application
 Procédez comme suit pour configurer l’application :

1. [Téléchargez TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip), puis décompressez le fichier.

2. Exécutez l’application `TwitterWPFClient.exe`. Ensuite, entrez vos données pour la clé d’API Twitter et la clé secrète, le jeton d’accès Twitter et le secret de jeton, ainsi que les informations de votre Event Hub. Enfin, définissez les mots-clés pour lesquels vous souhaitez suivre les sentiments.

### <a name="generate-an-oauth-access-token"></a>Génération d’un jeton d’accès OAuth
Pour plus d’informations, consultez [Procédure de génération d’un jeton d’accès OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens).  

 Pour générer un jeton, vous devez créer une application vide.  

1. Remplacez les valeurs EventHubConnectionString et EventHubName dans le fichier TwitterWpfClient.exe.config par la chaîne de connexion et le nom de votre hub d’événements. La chaîne de connexion copiée précédemment vous donne la chaîne de connexion et le nom de votre hub d’événements. Pensez à les séparer et à les placer dans le champ approprié. Prenons par exemple la chaîne de connexion suivante :

 ```
    Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey;EntityPath=yourhub
 ```  

   Le fichier TwitterWpfClient.exe.config doit contenir vos paramètres comme illustré dans l’exemple ci-dessous :

 ```
      add key="EventHubConnectionString" value="Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey"
      add key="EventHubName" value="yourhub"
   ```
   > [!NOTE]
   > Le texte « EntityPath= » n’apparaît **pas** dans la valeur EventHubName.

    Vous pouvez également entrer les valeurs de vos informations de connexion à Twitter et Azure directement dans le client. La même logique s’applique là où « EntityPath= » n’est pas utilisé.

   ![wpfclient](./media/stream-analytics-twitter-sentiment-analysis-trends/wpfclientlines.png)

2. **Facultatif :** définissez les mots clés à rechercher. Par défaut, cette application recherche des mots-clés de jeu.  Si vous le souhaitez, vous pouvez modifier ces mots clés en changeant les valeurs de **twitter_keywords** dans le fichier TwitterWpfClient.exe.config.

3. Exécutez TwitterWpfClient.exe. Sélectionnez ensuite le bouton vert de démarrage pour recueillir les sentiments sur les réseaux sociaux. Vous voyez s’afficher les événements Tweet tandis que les valeurs **CreatedAt**, **Topic** et **SentimentScore** sont transmises à votre hub d’événements.

## <a name="create-a-stream-analytics-job"></a>Création d’un travail Stream Analytics

Maintenant que nous avons un flux d’événements Tweet diffusé en temps réel depuis Twitter, nous pouvons configurer un travail Stream Analytics pour analyser ces événements en temps réel.

### <a name="provision-a-stream-analytics-job"></a>Configuration d’un travail Stream Analytics

Pour configurer un travail Stream Analytics, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com/), cliquez sur **NOUVEAU** > saisissez **STREAM ANALYTICS**, puis cliquez sur la vignette de résultat Azure Stream Analytics.

2. Spécifiez les valeurs suivantes, puis sélectionnez **CRÉER**.

   * **NOM DU TRAVAIL**: entrez un nom pour le travail.

   * **Groupe de ressources** : sélectionnez le groupe de ressources créé précédemment dans cet exercice à partir de l’option « Utiliser existant ».

   * **COMPTE DE STOCKAGE** : choisissez le compte de stockage que vous souhaitez utiliser pour stocker les données de surveillance de tous les travaux Stream Analytics en cours d’exécution dans cette région. Vous pouvez choisir un compte de stockage existant ou en créer un.   

![Nouvelle tâche](./media/stream-analytics-twitter-sentiment-analysis-trends/newjob.png)

Une fois que le travail a été créé, il s’ouvre dans le portail Azure.

## <a name="specify-the-job-input"></a>Spécification de l’entrée de travail
Pour spécifier l’entrée du travail, procédez comme suit :

1. Dans votre travail Stream Analytics, dans **Topologie du travail** au milieu du volet de travail, sélectionnez **ENTRÉES**. Sélectionnez ensuite **AJOUTER**.

    Ensuite, le portail vous demande certaines des informations suivantes. La plupart des valeurs par défaut fonctionnent et sont définies ici :

   * **ALIAS D’ENTRÉE** - entrez un nom convivial pour cette entrée de travail, comme `TwitterStream`. Vous utiliserez ce nom dans la requête par la suite.  

   * **NOM DU HUB D’ÉVÉNEMENTS** : sélectionnez le nom du hub d’événements.

   * **NOM DE LA STRATÉGIE DU HUB D’﻿﻿﻿ÉVÉNEMENTS** : sélectionnez la stratégie de hub d’événements créée précédemment dans ce didacticiel.

2. Cliquez sur le bouton **Créer**.

## <a name="specify-the-job-query"></a>Spécification de la requête de travail

Stream Analytics prend en charge un modèle de requête simple et déclaratif pour la description des transformations. Pour plus d’informations sur ce langage, consultez la page [Références sur le langage des requêtes d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx).  Ce didacticiel aborde la création et le test de plusieurs requêtes sur des données Twitter.

Pour comparer le nombre de mentions entre les sujets, nous allons utiliser une [fenêtre bascule (TumblingWindow)](https://msdn.microsoft.com/library/azure/dn835055.aspx) pour obtenir le nombre de mentions par sujet toutes les cinq secondes.

Modifiez la requête dans l’éditeur de code avec ce qui suit, puis sélectionnez **Enregistrer** :

```
SELECT System.Timestamp as Time, Topic, COUNT(*)
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY TUMBLINGWINDOW(s, 5), Topic
```   

Cette requête utilise le mot clé **TIMESTAMP BY** pour spécifier un champ d’horodatage dans la charge utile à utiliser dans le calcul temporel. Si ce champ n’est pas spécifié, l’opération de fenêtrage est réalisée en utilisant l’heure d’arrivée de chaque événement dans le hub d’événements. Pour en savoir plus, consultez la section « Heure d’arrivée par rapport à l’heure de l’application » de la page [Référence du langage de requête d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx).

Cette requête permet également d’accéder à un horodatage pour la fin de chaque fenêtre à l’aide de la propriété **System.Timestamp**.

![Case de requête](./media/stream-analytics-twitter-sentiment-analysis-trends/querybox.png)

## <a name="create-an-output-sink"></a>Créer un récepteur de sortie

Maintenant que nous avons défini un flux d’événements, un hub d’événements d’entrée pour la réception des événements et une requête pour effectuer une transformation sur le flux, la dernière étape consiste à définir un récepteur de sortie pour le travail.  

Nous écrivons les événements tweet agrégés à partir de notre requête de travail dans un stockage d’objets blob Azure.  Selon les besoins spécifiques de votre application, vous pouvez également transmettre vos résultats à une base de données SQL Azure, un stockage de tables Azure ou un hub d’événements.

## <a name="specify-the-job-output"></a>Spécification de la sortie du travail
Pour spécifier la sortie du travail, procédez comme suit :

1. Dans votre tâche Stream Analytics, cliquez sur **SORTIE** dans **Topologie de travail**, puis sélectionnez **AJOUTER**.

2. Puis saisissez ou sélectionnez les valeurs suivantes sur le panneau :

   * **ALIAS DE SORTIE**: entrez un nom convivial pour cette sortie de travail. Cette démonstration utilise `Output`.

   * **Sink** : sélectionnez **Stockage d’objets blob**.

   * **COMPTE DE STOCKAGE** : sélectionnez **Créer un nouveau compte de stockage**.

    * **Compte de STOCKAGE** : nommez le nouveau compte de stockage (`socialtwitter` dans cet exemple).

    * **CONTENEUR** : nommez le nouveau conteneur (`socialtwitter` dans cet exemple).

3. Laissez le reste des entrées sur les valeurs par défaut. Pour finir, sélectionnez **Créer**.

## <a name="start-the-job"></a>Démarrage du travail

Maintenant que nous avons spécifié une entrée, une requête et une sortie pour le travail Stream Analytics, nous pouvons le démarrer.

Pour démarrer le travail, procédez comme suit :

1. Dans le volet Vue d’ensemble du travail, en haut de la page, sélectionnez **DÉMARRER**.

2. Dans la boîte de dialogue qui s’ouvre, sélectionnez **HEURE DE DÉMARRAGE DE LA TÂCHE**, puis sélectionnez le bouton **VÉRIFIER** en bas de la boîte de dialogue. L’état du travail passe tout d’abord à **Démarrage** puis à **Exécution**.

![Travail en cours d’exécution](./media/stream-analytics-twitter-sentiment-analysis-trends/jobrunning.png)

## <a name="view-output-for-sentiment-analysis"></a>Afficher la sortie de l’analyse de sentiments

Une fois que votre travail a commencé et traite le flux Twitter en temps réel, choisissez la façon dont vous souhaitez afficher la sortie de l’analyse de sentiments.

Pour afficher la sortie de votre travail en temps réel, utilisez un outil tel que l’[explorateur de stockage Azure](https://http://storageexplorer.com/) ou [Azure Explorer](http://www.cerebrata.com/products/azure-explorer/introduction). À ce stade, vous pouvez utiliser [Power BI](https://powerbi.com/) pour étendre les fonctionnalités de votre application afin d’ajouter un tableau de bord personnalisé comme celui présenté sur la capture d’écran suivante.

![Power BI](./media/stream-analytics-twitter-sentiment-analysis-trends/power-bi.png)

## <a name="create-another-query-to-identify-trending-topics"></a>Créer une autre requête pour identifier les tendances

Un autre exemple de requête que nous avons créé pour ce scénario est basé sur les [Fenêtres glissantes](https://msdn.microsoft.com/library/azure/dn835051.aspx). Pour identifier les tendances, nous allons rechercher des sujets dépassant une valeur de seuil de mention dans un laps de temps donné.

Pour les besoins de ce didacticiel, nous allons consulter les rubriques mentionnées plus de 20 fois pendant les 5 dernières secondes.

```
SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY SLIDINGWINDOW(s, 5), topic
HAVING COUNT(*) > 20
```

## <a name="use-field-headers"></a>Utilisation des en-têtes de champ

Les étiquettes des champs que vous pouvez utiliser dans cet exercice sont répertoriées dans cette table. N’hésitez pas à faire des essais dans l’éditeur de requête.

Propriété JSON | Définition
--- | ---
CreatedAt | L’heure à laquelle le tweet a été créé
Rubrique | La rubrique qui correspond au mot-clé spécifié
SentimentScore | Le score des sentiments à partir de Sentiment140
Auteur | Le nom d’utilisateur Twitter qui a envoyé le tweet
Texte | Le corps complet du tweet


## <a name="get-support"></a>Obtenir de l'aide
Pour obtenir une assistance, consultez le [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes
* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d’Azure Stream Analytics](stream-analytics-get-started.md)
* [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

