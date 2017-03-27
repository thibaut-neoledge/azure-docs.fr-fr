---
title: "Analyse des sentiments Twitter en temps réel avec Stream Analytics | Microsoft Docs"
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
ms.date: 03/09/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 09860b34bf4b1664e8d82af0e049cfd1a2d8defa
ms.lasthandoff: 03/10/2017

---

# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Analyse de sentiments Twitter en temps réel dans Azure Stream Analytics

Apprenez à créer une solution d’analyse de sentiments pour l’analyse des réseaux sociaux en intégrant des événements Twitter dans Azure Event Hubs. Vous allez écrire une requête Azure Stream Analytics pour analyser les données. Ensuite, vous allez soit stocker les résultats en vue d’une consultation ultérieure, soit utiliser un tableau de bord et [Power BI](https://powerbi.com/) pour fournir des informations en temps réel.

Les outils d’analyse de réseaux sociaux aident les organisations à comprendre les sujets populaires, les sujets significatifs et les avis apparaissant dans un grand nombre de billets sur les réseaux sociaux. L’analyse de sentiments, aussi appelée *exploration d’opinions*, utilise des outils d’analyse de réseaux sociaux pour déterminer les attitudes envers un produit, une idée, etc. L’analyse de tendances Twitter en temps réel constitue un excellent exemple, car le modèle d’abonnement hashtag vous permet de suivre des mots clés spécifiques et de développer l’analyse de sentiments sur le flux.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Scénario : analyse de sentiments sur les réseaux sociaux en temps réel

Une entreprise qui dispose d’un site web de médias souhaite obtenir un avantage sur ses concurrents en présentant des contenus immédiatement pertinents pour ses lecteurs. Elle utilise l’analyse des réseaux sociaux sur des sujets pertinents pour ses lecteurs en effectuant une analyse de sentiments en temps réel des données de Twitter. Pour identifier les tendances en temps réel sur Twitter, l’entreprise doit analyser en temps réel le volume et les sentiments des tweets relatifs aux principaux sujets. Elle a donc essentiellement besoin d’un moteur d’analyse de sentiments basé sur le flux de ce réseau social.

## <a name="prerequisites"></a>Composants requis

* Un abonnement Azure
* Compte Twitter et [jeton d’accès OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)
* Le fichier [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) de GitHub.
* Facultatif : code source pour le client Twitter dans [GitHub](https://aka.ms/azure-stream-analytics-twitterclient)

## <a name="create-an-event-hub-input"></a>Création d’une entrée de hub d’événements

L’exemple d’application génère des événements et les transmet vers une instance de hub d’événements (ou simplement « hub d’événement » pour faire plus court). Les hubs d’événements Service Bus constituent la méthode la plus efficace pour la réception d’événements dans Stream Analytics. Pour plus d’informations, consultez la documentation d’Event Hubs sur [Documentation d’Azure Service Bus](/azure/service-bus/).

### <a name="use-the-following-steps-to-create-an-event-hub"></a>Procédez comme suit pour créer un hub d’événements.

1. Dans le [Portail Azure](https://portal.azure.com), cliquez sur **NOUVEAU** > et saisissez *Event Hubs* , puis sélectionnez **Event Hubs** à partir de la recherche qui en résulte. Cliquez ensuite sur **Créer**.
2. Fournissez un nom pour Event Hub et créez un groupe de ressources. J’ai spécifié `socialtwitter-eh` et `socialtwitter-rg` respectivement. Cochez la case pour épingler le compte au tableau de bord, puis cliquez sur le bouton **Créer**.
3. Une fois le déploiement terminé, cliquez sur l’instance d’Event Hubs, puis sur **Event Hubs** sous **entités**.
4. Cliquez sur le bouton **+ Event Hub** pour créer votre Event Hub. Indiquez à nouveau votre nom (le mien était `socialtwitter-eh`) et cliquez sur **Créer**.
5. Pour accorder l’accès au hub d’événements, vous devez créer une stratégie d’accès partagé. Cliquez sur l’Event Hub et cliquez sur **Stratégies d’accès partagé** sous **Paramètres**.
6. Sous **STRATÉGIES D’ACCÈS PARTAGÉ**, créez une stratégie ayant les autorisations **GÉRER** en cliquant sur **+ Ajouter**. Attribuez un nom à la stratégie et cochez **Gérer**, puis cliquez sur **Créer**.
7. Cliquez sur votre nouvelle stratégie une fois qu’elle a été créée, puis cliquez sur le bouton Copier pour l’entité **CHAÎNE DE CONNEXION - CLÉ PRIMAIRE**. Nous en aurons besoin plus loin dans l’exercice. Ensuite, revenez au tableau de bord.

![points de terminaison de la stratégie d’accès partagé](./media/stream-analytics-twitter-sentiment-analysis-trends/keysandendpoints.png)

## <a name="configure-and-start-the-twitter-client-application"></a>Configurer et démarrer l’application client Twitter

Nous vous proposons une application client capable de se connecter aux données de Twitter par le biais des [API Streaming de Twitter](https://dev.twitter.com/streaming/overview) pour collecter les événements Tweet sur un ensemble de sujets paramétrable. L’outil open source [Sentiment140](http://help.sentiment140.com/) est utilisé pour affecter une valeur de sentiment à chaque tweet.

* 0 = négatif
* 2 = neutre
* 4 = positif

Les événements Tweet sont ensuite transmis au hub d’événements.  

### <a name="follow-these-steps-to-set-up-the-application"></a>Procédez comme suit pour configurer l’application :

1. [Téléchargez TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) et décompressez le fichier.
2. Exécutez l’application `TwitterWPFClient.exe` et entrez vos données pour la clé d’API Twitter et la clé secrète, le jeton d’accès Twitter et le secret de jeton, ainsi que les informations d’Azure Event Hub. Enfin, définissez les mots clés pour lesquels vous souhaitez suivre les sentiments. Remarque : si vous spécifiez plusieurs mots (en utilisant des virgules pour séparer les valeurs) et que vous souhaitez utiliser n’importe lequel, vous devez définir le contrôle sur « Toute correspondance ».

   [Procédure de génération d’un jeton d’accès OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)  

   Vous devez créer une application vide pour générer un jeton.  

3. Remplacez les valeurs EventHubConnectionString et EventHubName dans le fichier TwitterWpfClient.exe.config par la chaîne de connexion et le nom de votre hub d’événements. La chaîne de connexion copiée précédemment vous donne la chaîne de connexion et le nom de votre hub d’événements. Par conséquent, pensez à les séparer et à les placer dans le champ approprié. Prenons par exemple la chaîne de connexion suivante :  
   
   `Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey;EntityPath=yourhub`
   
   Le fichier TwitterWpfClient.exe.config doit contenir vos paramètres comme dans l’exemple ci-dessous :
   
   ```
     add key="EventHubConnectionString" value="Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey"
     add key="EventHubName" value="yourhub"
   ```
   
   Il est important de noter que le texte « EntityPath= » n’apparaît **pas** dans la valeur EventHubName.
   
   Vous pouvez également entrer les valeurs de vos informations de connexion à Twitter et Azure directement dans le client. La même logique s’applique là où « EntityPath= » n’est pas utilisé.
   
   ![wpfclient](./media/stream-analytics-twitter-sentiment-analysis-trends/wpfclientlines.png)

4. *Facultatif :* définissez les mots clés à rechercher.  Par défaut, cette application recherche des mots clés de jeu.  Si vous le souhaitez, vous pouvez modifier ces mots clés en changeant les valeurs de **twitter_keywords** dans le fichier TwitterWpfClient.exe.config.
5. Exécutez TwitterWpfClient.exe et cliquez sur le bouton vert de démarrage pour recueillir les sentiments sur les réseaux sociaux. Vous voyez s’afficher les événements Tweet tandis que les valeurs **CreatedAt**, **Topic** et **SentimentScore** sont transmises à votre hub d’événements.

## <a name="create-a-stream-analytics-job"></a>Création d’un travail Stream Analytics

Maintenant que nous avons un flux d’événements Tweet diffusé en temps réel depuis Twitter, nous pouvons configurer un travail Stream Analytics pour analyser ces événements en temps réel.

### <a name="provision-a-stream-analytics-job"></a>Configuration d’un travail Stream Analytics

Dans le [portail Azure](https://portal.azure.com/), cliquez sur **NOUVEAU** > saisissez **STREAM ANALYTICS**, puis cliquez sur la vignette Azure Stream Analytics. Spécifiez les valeurs suivantes, puis cliquez sur **CRÉER**.

   * **NOM DU TRAVAIL**: entrez un nom pour le travail.
   * **Groupe de ressources** : sélectionnez le groupe de ressources créé précédemment dans cet exercice à partir de l’option « Utiliser existant ».
   * **COMPTE DE STOCKAGE** : choisissez le compte de stockage que vous souhaitez utiliser pour stocker les données de surveillance de tous les travaux Stream Analytics en cours d’exécution dans cette région. Vous pouvez choisir un compte de stockage existant ou en créer un.   

![Nouvelle tâche](./media/stream-analytics-twitter-sentiment-analysis-trends/newjob.png)

Une fois que le travail est créé, ce dernier s’ouvre dans le portail.

## <a name="specify-the-job-input"></a>Spécification de l’entrée de travail

Dans votre travail Stream Analytics, cliquez sur **ENTRÉES** au milieu du volet de travail, dans Topologie du travail, puis cliquez sur **Ajouter**. Le portail vous présente ensuite les informations répertoriées ci-dessous. La plupart des valeurs par défaut sont suffisantes, mais elles sont définies ci-dessous à titre d’information.
  
   * **ALIAS D’ENTRÉE** - entrez un nom convivial pour cette entrée de travail, comme `TwitterStream`. Vous utiliserez ce nom dans la requête par la suite.
   * **NOM DU HUB D’ÉVÉNEMENTS** : sélectionnez le nom du hub d’événements.
   * **NOM DE LA STRATÉGIE DU HUB D’﻿﻿﻿ÉVÉNEMENTS** : sélectionnez la stratégie de hub d’événements créée précédemment dans ce didacticiel.

Cliquez sur le bouton **Créer** .

## <a name="specify-job-query"></a>Spécification de la requête du travail

Stream Analytics prend en charge un modèle de requête simple et déclaratif pour la description des transformations. Pour plus d’informations sur ce langage, consultez la page [Références sur le langage des requêtes d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx).  Ce didacticiel aborde la création et le test de plusieurs requêtes sur des données Twitter.

Pour comparer le nombre de mentions entre les sujets, nous allons utiliser une [fenêtre bascule (TumblingWindow)](https://msdn.microsoft.com/library/azure/dn835055.aspx) pour obtenir le nombre de mentions par sujet toutes les cinq secondes.

Modifiez la requête dans l’éditeur de code avec le code ci-dessous, puis cliquez sur **Enregistrer** :

```
SELECT System.Timestamp as Time, Topic, COUNT(*)
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY TUMBLINGWINDOW(s, 5), Topic
```   

Cette requête utilise le mot clé **TIMESTAMP BY** pour spécifier un champ d’horodatage dans la charge utile à utiliser dans le calcul temporel. Si ce champ n’est pas spécifié, l’opération de fenêtrage est réalisée en utilisant l’heure d’arrivée de chaque événement dans le hub d’événements.  Pour en savoir plus, consultez la section « Heure d’arrivée par rapport à l’heure de l’application » de la page [Référence du langage de requête d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx).

Cette requête permet également d’accéder à un horodatage pour la fin de chaque fenêtre à l’aide de la propriété **System.Timestamp**.

![Case de requête](./media/stream-analytics-twitter-sentiment-analysis-trends/querybox.png)

## <a name="create-output-sink"></a>Création du récepteur de sortie

Maintenant que nous avons défini un flux d’événements, un hub d’événements d’entrée pour la réception des événements et une requête pour effectuer une transformation sur le flux, la dernière étape consiste à définir un récepteur de sortie pour le travail.  Nous allons écrire les événements tweet agrégés à partir de notre requête de travail dans un stockage d’objets blob Azure.  Selon les besoins spécifiques de votre application, vous pouvez également transmettre vos résultats à une base de données SQL Azure, un stockage de tables Azure ou un hub d’événements.

## <a name="specify-job-output"></a>Spécification de la sortie du travail

Dans votre tâche Stream Analytics, cliquez sur **SORTIE** dans **Topologie de travail**, puis sur **AJOUTER**. Puis saisissez ou sélectionnez les valeurs suivantes sur le panneau :
   
   * **ALIAS DE SORTIE**: entrez un nom convivial pour cette sortie de travail. Cette démonstration utilise `Output`.
   * **Sink** : sélectionnez Stockage d’objets blob.
   * **COMPTE DE STOCKAGE** : sélectionnez « Créer un nouveau compte de stockage ».
    * **Compte de STOCKAGE** : nommez le nouveau compte de stockage (`socialtwitter` dans cet exemple)
    * **CONTENEUR** : nommez le nouveau conteneur (`socialtwitter` dans cet exemple)

Laissez le reste des entrées sur les valeurs par défaut. Cliquez enfin sur **Créer**.

## <a name="start-the-job"></a>Démarrage du travail

Maintenant que nous avons spécifié une entrée, une requête et une sortie pour le travail Stream Analytics, nous pouvons le démarrer.

Dans le volet Vue d’ensemble du travail, cliquez simplement sur **DÉMARRER** en haut de la page.

Dans la boîte de dialogue qui s’affiche, cliquez sur **HEURE DE DÉBUT DU TRAVAIL**, puis sur la **coche** en bas de la boîte de dialogue. L’état du travail passe à **Démarrage**, puis à **En cours d’exécution**.

![Travail en cours d’exécution](./media/stream-analytics-twitter-sentiment-analysis-trends/jobrunning.png)

## <a name="view-output-for-sentiment-analysis"></a>Afficher la sortie de l’analyse de sentiments

Une fois que votre travail en cours d’exécution traite le flux Twitter en temps réel, choisissez la façon dont vous souhaitez afficher la sortie de l’analyse de sentiments. Pour afficher la sortie de votre travail en temps réel, utilisez un outil tel que [l’explorateur de stockage Azure](https://http://storageexplorer.com/) ou [Azure Explorer](http://www.cerebrata.com/products/azure-explorer/introduction). À ce stade, vous pouvez utiliser [Power BI](https://powerbi.com/) pour étendre les fonctionnalités de votre application afin d’ajouter un tableau de bord personnalisé comme celui de la capture d’écran suivante.

![powerbi](./media/stream-analytics-twitter-sentiment-analysis-trends/power-bi.png)

## <a name="another-query-of-interest-in-this-scenario"></a>Une autre requête d’intérêt dans ce scénario

Un autre exemple de requête que nous avons créé pour ce scénario est basé sur les [Fenêtres glissantes](https://msdn.microsoft.com/library/azure/dn835051.aspx). Pour identifier les tendances, nous allons rechercher des sujets dépassant une valeur de seuil de mention dans un laps de temps donné. Pour les besoins de ce didacticiel, nous allons consulter les rubriques mentionnées plus de 20 fois pendant les 5 dernières secondes.

```
SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY SLIDINGWINDOW(s, 5), topic
HAVING COUNT(*) > 20
```

## <a name="table-of-the-field-headers"></a>Tableau des en-têtes de champ

Pour une divulgation complète, les étiquettes des champs que vous pouvez utiliser dans cet exercice sont répertoriées dans cette table. N’hésitez pas à faire des essais dans l’éditeur de requête.

Propriété JSON | Définition
--- | ---
CreatedAt | heure de création du tweet
Rubrique | Rubrique qui correspond au mot clé spécifié
SentimentScore | score des sentiments à partir de Sentiment140
Auteur | Nom d’utilisateur Twitter qui a envoyé le tweet
Texte | corps complet du tweet


## <a name="get-support"></a>Obtenir de l'aide
Pour obtenir une assistance, consultez le [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes
* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d’Azure Stream Analytics](stream-analytics-get-started.md)
* [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)


