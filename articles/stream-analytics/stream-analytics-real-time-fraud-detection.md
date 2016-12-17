---
title: "Stream Analytics : détection des fraudes en temps réel | Microsoft Docs"
description: "Apprenez à créer une solution de détection des fraudes en temps réel avec Stream Analytics. Utilisez un concentrateur d’événements pour le traitement des événements en temps réel."
keywords: "détection des anomalies, détection des fraudes, détection d’anomalies en temps réel"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: c10dd53f-d17a-4268-a561-cb500a8c04eb
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 11/14/2016
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 18156086ca3eaf3ee99dfeb61096bc1eee49458b
ms.openlocfilehash: 3d32518bed64ebe3dd1790bb511949624b5dcac1


---
# <a name="get-started-using-azure-stream-analytics-real-time-fraud-detection"></a>Prise en main de l’utilisation d’Azure Stream Analytics : détection des fraudes en temps réel
Apprenez à créer une solution de bout en bout pour la détection des fraudes en temps réel avec Azure Stream Analytics. Importez des événements dans des hubs d’événements Azure, écrivez des requêtes Stream Analytics à des fins d’agrégation ou d’alerte et envoyez les résultats à un récepteur de sortie pour obtenir des informations sur les données grâce au traitement en temps réel. Ce document aborde la détection en temps réel des anomalies de télécommunication, mais la technique présentée est adaptée à d’autres types de détection des fraudes, comme l’usurpation d’identité ou le vol de carte de crédit.

Stream Analytics est un service entièrement géré qui permet de traiter des événements avec une latence faible, une haute disponibilité et un traitement d’événements complexes évolutif à l’aide d’une diffusion de données dans le cloud. Pour plus d’informations, consultez [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md).

## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>Scénario : détection des fraudes de télécommunication et SIM en temps réel
Une société de télécommunication dispose d’un volume important de données pour les appels entrants. La société doit déterminer les points suivants à partir de ses données :

* Réduire ces données à une quantité gérable pour dégager des informations sur l’utilisation par les clients au fil du temps et dans différentes régions géographiques.
* Détecter les fraudes SIM (plusieurs appels provenant de la même identité quasiment au même moment mais dans des zones géographiques différentes) en temps réel afin d’y répondre facilement en avertissant ses clients ou en arrêtant le service.

Les scénarios de type Internet des objets (IoT) génèrent de gros volumes de données télémétriques ou de capteur. Les clients souhaitent agréger ces données ou être avertis en temps réel en cas d’anomalie.

## <a name="prerequisites"></a>Composants requis
* Téléchargez [TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) à partir du Centre de téléchargement Microsoft
* Facultatif : code source du générateur d’événements dans [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator)

## <a name="create-azure-event-hubs-input-and-consumer-group"></a>Création d’une entrée de hub d’événements Azure et d’un groupe de consommateurs
L’exemple d’application génère des événements et les transmet vers une instance de hub d’événements en vue d’un traitement en temps réel. Les hubs d’événements Service Bus constituent la méthode la plus efficace pour la réception d’événements dans Stream Analytics. Pour plus d’informations sur les hubs d’événements, consultez la [documentation Azure Service Bus](/azure/service-bus/).

Pour créer un hub d’événements :

1. Dans le [portail Azure](https://manage.windowsazure.com/), cliquez sur **NOUVEAU** > **APP SERVICES** > **SERVICE BUS** > **HUB D’ÉVÉNEMENTS** > **CRÉATION RAPIDE**. Spécifiez un nom, une région et un espace de noms nouveau ou existant pour créer un hub d’événements.  
2. Nous vous recommandons de faire en sorte que chaque travail Stream Analytics lise les événements à partir d’un seul groupe de consommateurs de hubs d’événements. Nous vous expliquerons comment créer un groupe de consommateurs ultérieurement. [En savoir plus sur les groupes de consommateurs](https://msdn.microsoft.com/library/azure/dn836025.aspx). Pour créer un groupe de consommateurs, accédez au hub d’événements nouvellement créé et cliquez sur l’onglet **GROUPES DE CONSOMMATEURS**, puis sur **CRÉER** en bas de la page, et entrez un nom pour votre groupe de consommateurs.
3. Pour accorder l’accès au hub d’événements, vous devez créer une stratégie d’accès partagé. Cliquez sur l’onglet **CONFIGURER** de votre hub d’événements.
4. Sous **STRATÉGIES D’ACCÈS PARTAGÉ**, créez une stratégie ayant les autorisations **GÉRER**.

   ![Stratégies d’accès partagé où vous pouvez créer une stratégie ayant les autorisations Gérer.](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-shared-access-policies.png)
5. Cliquez sur **ENREGISTRER** en bas de la page.
6. Accédez au **Tableau de bord**, cliquez sur **INFORMATIONS DE CONNEXION** en bas de la page, puis copiez et enregistrez les informations de connexion.

## <a name="configure-and-start-the-event-generator-application"></a>Configuration et démarrage de l’application de génération d’événements
Nous avons fourni une application cliente qui génère des exemples de métadonnées d’appel entrant et les envoie aux hubs d’événements. Suivez les étapes suivantes pour configurer cette application.  

1. Téléchargez le [fichier TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) et décompressez-le dans un répertoire.

   > [!NOTE]
   > Windows peut bloquer le fichier zip téléchargé. Cliquez avec le bouton droit sur le fichier et sélectionnez **Propriétés**. Si le message « Ce fichier provient d’un autre ordinateur et peut éventuellement être bloqué pour protéger cet ordinateur. » apparaît, cochez la case **Débloquer** et cliquez sur Appliquer dans le fichier zip.
   >
   > 
2. Remplacez les valeurs Microsoft.ServiceBus.ConnectionString et EventHubName dans le fichier telcodatagen.exe.config par la chaîne de connexion et le nom de votre hub d’événements.

   La chaîne de connexion copiée à partir du portail Azure place le nom de la connexion à la fin. Veillez à supprimer « ;EntityPath=<value> » du champ « add key= ».
3. Lancez l’application. Procédez comme suit :

   telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]

L’exemple suivant génèrera 1 000 événements avec une probabilité de 20 % de fraude sur une durée de deux heures.

    telcodatagen.exe 1000 .2 2

Des enregistrements seront envoyés à votre hub d’événements. Certains champs clés que nous utiliserons dans cette application de détection des fraudes en temps réel sont définis ici :

| Enregistrement      | Définition                               |
| ----------- | ---------------------------------------- |
| CallrecTime | Horodatage de l'heure de début d'appel       |
| SwitchNum   | Commutateur téléphonique utilisé pour connecter l'appel. |
| CallingNum  | Numéro de téléphone de l'appelant.              |
| CallingIMSI | Identité de l'abonné mobile international (IMSI).  Identificateur unique de l'appelant. |
| CalledNum   | Numéro de téléphone du destinataire de l'appel.      |
| CalledIMSI  | Identité de l'abonné mobile international (IMSI).  Identificateur unique du destinataire de l'appel. |

## <a name="create-a-stream-analytics-job"></a>Création d’un travail Stream Analytics
Maintenant que nous avons un flux d’événements de télécommunication, nous pouvons configurer un travail Stream Analytics pour analyser ces événements en temps réel.

### <a name="provision-a-stream-analytics-job"></a>Configuration d’un travail Stream Analytics
1. Dans le portail Azure, cliquez sur **NOUVEAU** > **DATA SERVICES** > **STREAM ANALYTICS** > **CRÉATION RAPIDE**.
2. Spécifiez les valeurs suivantes, puis cliquez sur **CRÉER UN TRAVAIL STREAM ANALYTICS**:

   * **NOM DU TRAVAIL**: entrez un nom pour le travail.
   * **RÉGION**: sélectionnez la région où vous souhaitez exécuter le travail. Envisagez de placer le travail et le hub d’événements dans la même région pour être certain d’améliorer les performances et de ne pas payer pour un transfert de données entre différentes régions.
   * **COMPTE DE STOCKAGE** : choisissez le compte de stockage que vous souhaitez utiliser pour stocker les données de surveillance de tous les travaux Stream Analytics en cours d’exécution dans cette région. Vous pouvez choisir un compte de stockage existant ou en créer un.
3. Dans le volet gauche, cliquez sur **STREAM ANALYTICS** pour afficher une liste des travaux Stream Analytics.

   ![Icône du service Stream Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-service-icon.png)

   Le nouveau travail est affiché avec l’état **CRÉÉ**. Notez que le bouton **DÉMARRER** situé en bas de la page est désactivé. Avant de pouvoir démarrer le travail, vous devez configurer son entrée, sa sortie et sa requête.

### <a name="specify-job-input"></a>Spécification d'une entrée de travail
1. En haut de la page de votre travail Stream Analytics, cliquez sur **ENTRÉES**, puis sur **AJOUTER UNE ENTRÉE**. La boîte de dialogue qui s’ouvre vous indique les étapes à suivre pour configurer votre entrée.
2. Sélectionnez **FLUX DE DONNÉES**, puis cliquez avec le bouton droit.
3. Sélectionnez **HUB D’ÉVÉNEMENTS**, puis cliquez avec le bouton droit.
4. Saisissez ou sélectionnez les valeurs suivantes sur la troisième page :

   * **ALIAS D’ENTRÉE** : entrez un nom convivial pour ce travail, tel que *CallStream*. Notez que vous utiliserez ce nom dans la requête par la suite.
   * **HUB D’ÉVÉNEMENTS** : si le hub d’événements que vous avez créé est situé dans le même abonnement que le travail Stream Analytics, sélectionnez l’espace de noms dans lequel est situé le hub d’événements.

     Si votre hub d’événements est situé dans un autre abonnement, sélectionnez **Utiliser le hub d’événements à partir d’un autre abonnement** et entrez manuellement **l’ESPACE DE NOMS SERVICE BUS**, le **NOM DU HUB D’ÉVÉNEMENTS**, le **NOM DE LA STRATÉGIE DU HUB D’ÉVÉNEMENTS**, la **CLÉ DE STRATÉGIE DU HUB D’ÉVÉNEMENTS** et le **NOMBRE DE PARTITIONS DU HUB D’ÉVÉNEMENTS**.
   * **NOM DU HUB D’ÉVÉNEMENTS** : sélectionnez le nom du hub d’événements.
   * **NOM DE LA STRATÉGIE DU HUB D’﻿﻿﻿ÉVÉNEMENTS** : sélectionnez la stratégie de hub d’événements créée précédemment dans ce didacticiel.
   * **GROUPE DE CONSOMMATEURS DU HUB D’ÉVÉNEMENTS** : entrez le nom du groupe de consommateurs créé précédemment dans ce didacticiel.
5. Cliquez avec le bouton droit.
6. Spécifiez les valeurs suivantes :

   * **FORMAT ﻿﻿﻿DU SÉRIALISEUR D’ÉVÉNEMENT**: JSON
   * **ENCODAGE**: UTF8
7. Cliquez sur la **coche** pour ajouter cette source et vérifier que Stream Analytics peut se connecter au hub d’événements.

### <a name="specify-job-query"></a>Spécification de la requête du travail
Stream Analytics prend en charge un modèle de requête simple et déclaratif pour la description des transformations dans le cadre du traitement en temps réel. Pour plus d’informations sur ce langage, consultez la page [Références sur le langage des requêtes d’Azure Stream Analytics](https://msdn.microsoft.com/library/dn834998.aspx). Ce didacticiel aborde la création et le test de plusieurs requêtes sur votre flux de données d’appel en temps réel.

#### <a name="optional-sample-input-data"></a>Facultatif : exemples de données d’entrée
Pour appliquer votre requête à des données de travail réelles, vous pouvez utiliser la fonctionnalité **EXEMPLES DE DONNÉES** pour extraire des événements à partir de votre flux de données et créer un fichier .JSON contenant les événements du test.  Les étapes suivantes montrent comment effectuer cette opération. Nous avons également fourni un exemple de fichier [telco.json](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json) à des fins de test.

1. Sélectionnez l’entrée de votre hub d’événements, puis cliquez sur **EXEMPLES DE DONNÉES** en bas de la page.
2. Dans la boîte de dialogue qui s’affiche, entrez une **HEURE DE DÉBUT** pour le démarrage de la collecte des données et une **DURÉE** afin de déterminer la quantité de données supplémentaires à traiter.
3. Cliquez sur la **coche** pour démarrer l’échantillonnage des données à partir de l’entrée.  La production du fichier de données peut prendre une minute ou deux.  Lorsque le processus est terminé, cliquez sur **DÉTAILS**, téléchargez le fichier .JSON généré, puis enregistrez-le.

   ![Téléchargement et enregistrement des données traitées dans un fichier JSON](./media/stream-analytics-real-time-fraud-detection/stream-analytics-download-save-json-file.png)

#### <a name="pass-through-query"></a>Requête directe
Si vous voulez archiver tous les événements, vous pouvez utiliser une requête pass-through pour lire tous les champs dans la charge utile de l’événement ou du message. Pour commencer, effectuez une simple requête directe qui projette tous les champs d’un événement.

1. En haut de la page du travail Stream Analytics, cliquez sur **REQUÊTE** .
2. Ajoutez le code suivant dans l'éditeur de code :

     SELECT * FROM CallStream

   > [!IMPORTANT]
   > Assurez-vous que le nom de la source d’entrée correspond à celui que vous avez spécifié précédemment.
   >
   > 
3. Cliquez sur **Test** dans l’éditeur de requête.
4. Fournissez un fichier de test. Vous pouvez utiliser un fichier que vous avez créé en suivant la procédure ci-dessus ou le fichier [telco.json](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/SampleDataFiles/Telco.json).
5. Cliquez sur la **coche** et consultez les résultats affichés sous la définition de la requête.

   ![Résultats de la définition de la requête](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sim-fraud-output.png)

### <a name="column-projection"></a>Projection de colonne
Nous allons maintenant réduire les champs renvoyés à un ensemble plus petit.

1. Modifiez la requête dans l’éditeur de code comme ceci :

     SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum   FROM CallStream
2. Cliquez sur **Réexécuter** dans l’éditeur de requête pour afficher les résultats de la requête.

   ![Sortie dans l'éditeur de requête.](./media/stream-analytics-real-time-fraud-detection/stream-analytics-query-editor-output.png)

### <a name="count-of-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Nombre d'appels entrants par région : fenêtre bascule avec agrégation
Pour comparer la quantité d’appels entrants par région, nous allons utiliser une [fenêtre bascule (TumblingWindow)](https://msdn.microsoft.com/library/azure/dn835055.aspx) pour obtenir le nombre d’appels entrants regroupés par **SwitchNum** toutes les cinq secondes.

1. Modifiez la requête dans l’éditeur de code comme ceci :

     SELECT System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount   FROM CallStream TIMESTAMP BY CallRecTime   GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum

   Cette requête utilise le mot clé **Timestamp By** pour spécifier un champ d’horodatage dans la charge utile à utiliser dans le calcul temporel. Si ce champ n’est pas spécifié, l’opération de fenêtrage est réalisée en utilisant l’heure d’arrivée de chaque événement dans le hub d’événements. Consultez [« Heure d’arrivée par rapport à l’heure de l’application » dans la page Référence du langage de requête de Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx).

   Notez que vous pouvez accéder à un horodatage pour la fin de chaque fenêtre à l’aide de la propriété **System.Timestamp** .
2. Cliquez sur **Réexécuter** dans l’éditeur de requête pour afficher les résultats de la requête.

   ![Résultats de la requête pour Timestand par](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-query-editor-rerun.png)

### <a name="sim-fraud-detection-with-a-self-join"></a>Détection des fraudes SIM avec une jointure réflexive
Pour identifier une utilisation potentiellement frauduleuse, nous examinons les appels provenant du même utilisateur mais à des endroits différents en moins de 5 secondes.  Nous [joignons](https://msdn.microsoft.com/library/azure/dn835026.aspx) le flux d’événements d’appel avec lui-même pour vérifier ces cas.

1. Modifiez la requête dans l’éditeur de code comme ceci :

     SELECT System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1,   CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2   FROM CallStream CS1 TIMESTAMP BY CallRecTime   JOIN CallStream CS2 TIMESTAMP BY CallRecTime   ON CS1.CallingIMSI = CS2.CallingIMSI   AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5   WHERE CS1.SwitchNum != CS2.SwitchNum
2. Cliquez sur **Réexécuter** dans l’éditeur de requête pour afficher les résultats de la requête.

   ![Résultats de la requête d’une jointure](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-query-editor-join.png)

### <a name="create-output-sink"></a>Création du récepteur de sortie
Maintenant que nous avons défini un flux d’événements, un hub d’événements d’entrée pour la réception des événements et une requête pour effectuer une transformation sur le flux, la dernière étape consiste à définir un récepteur de sortie pour le travail. Il s’agit d’écrire des événements pour le comportement frauduleux dans le stockage d’objets blob Azure.

Si vous n’avez pas déjà de conteneur pour le stockage des objets blob, procédez comme suit pour en créer un.

1. Utilisez un compte de stockage existant ou créez-en un en cliquant sur **NOUVEAU > DATA SERVICES > STOCKAGE > CRÉATION RAPIDE**, puis suivez les instructions qui s’affichent.
2. Sélectionnez le compte de stockage, cliquez en haut de la page sur **CONTENEURS**, puis sur **AJOUTER**.
3. Entrez un **NOM** pour votre conteneur et définissez son **ACCÈS** sur **Objet blob public**.

## <a name="specify-job-output"></a>Spécification de la sortie du travail
1. En haut de la page de votre travail Stream Analytics, cliquez sur **SORTIE**, puis sur **AJOUTER UNE SORTIE**. La boîte de dialogue qui s’ouvre vous indique les étapes à suivre pour configurer votre sortie.
2. Sélectionnez **STOCKAGE D’OBJETS BLOB**, puis cliquez avec le bouton droit.
3. Saisissez ou sélectionnez les valeurs suivantes sur la troisième page :

   * **ALIAS DE SORTIE**: entrez un nom convivial pour cette sortie de travail.
   * **ABONNEMENT** : si le stockage d’objets blob que vous avez créé est situé dans le même abonnement que le travail Stream Analytics, cliquez sur **Utiliser le compte de stockage de l’abonnement actuel**. Si votre espace de stockage appartient à un autre abonnement, cliquez sur **Utiliser le stockage associé à un autre abonnement** et entrez manuellement les informations des champs **COMPTE DE STOCKAGE**, **CLÉ DU COMPTE DE STOCKAGE** et **CONTENEUR**.
   * **COMPTE DE STOCKAGE**: sélectionnez le nom du compte de stockage.
   * **CONTENEUR**: sélectionnez le nom du conteneur.
   * **PRÉFIXE DU NOM DE FICHIER** : entrez un préfixe de fichier à utiliser lors de l’écriture de la sortie de l’objet blob.
4. Cliquez avec le bouton droit.
5. Spécifiez les valeurs suivantes :

   * **FORMAT ﻿﻿﻿DU SÉRIALISEUR D’ÉVÉNEMENT**: JSON
   * **ENCODAGE**: UTF8
6. Cliquez sur la **coche** pour ajouter cette source et vérifier que Stream Analytics peut se connecter au compte de stockage.

## <a name="start-job-for-real-time-processing"></a>Démarrer le travail de traitement en temps réel
Maintenant que nous avons spécifié une entrée, une requête et une sortie pour le travail Stream Analytics, nous pouvons lancer l’opération de détection des fraudes en temps réel.

1. Sur le **TABLEAU DE BORD** du travail, en bas de la page, cliquez sur **DÉMARRER**.
2. Dans la boîte de dialogue qui s’affiche, cliquez sur **HEURE DE DÉBUT DU TRAVAIL**, puis sur la **coche** en bas de la boîte de dialogue. L’état du travail passe à **Démarrage**, puis à **En cours d’exécution**.

## <a name="view-fraud-detection-output"></a>Afficher la sortie de détection des fraudes
Utilisez un outil tel que [l’explorateur de stockage Azure](http://storageexplorer.com/) ou [Azure Explorer](http://www.cerebrata.com/products/azure-explorer/introduction) pour afficher les événements frauduleux au fur et à mesure qu’ils s’écrivent dans la sortie en temps réel.  

![Détection des fraudes : événements frauduleux affichés en temps réel](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-view-real-time-fraudent-events.png)

## <a name="get-support"></a>Obtenir de l'aide
Pour obtenir une assistance, consultez le [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes
* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)




<!--HONumber=Nov16_HO3-->


