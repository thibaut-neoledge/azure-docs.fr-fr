---
title: Utilisation d'outils Azure Stream Analytics pour Visual Studio | Microsoft Docs
description: "Didacticiel pour bien démarrer avec les outils Azure Stream Analytics pour Visual Studio"
keywords: visual studio
documentationcenter: 
services: stream-analytics
author: 
manager: 
editor: 
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 
ms.author: 
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 618c1055795a75e0ed71dacddba3e076f81f4946
ms.contentlocale: fr-fr
ms.lasthandoff: 07/04/2017


---
# <a name="use-azure-stream-analytics-tools-for-visual-studio"></a>Utilisation d’outils Azure Stream Analytics pour Visual Studio
## <a name="introduction"></a>Introduction
Dans ce didacticiel, vous apprenez à utiliser les outils Azure Stream Analytics pour Visual Studio afin de créer, fournir du contenu, tester localement, gérer et déboguer vos tâches Stream Analytics. 

Après avoir effectué ce didacticiel, vous pourrez :
* bien connaître les outils Stream Analytics pour Visual Studio ;
* configurer et déployer un travail Stream Analytics ;
* tester votre travail en local avec des exemples de données locaux ;
* utiliser la surveillance pour résoudre les problèmes ;
* exporter des travaux existants dans des projets.

## <a name="prerequisites"></a>Composants requis
Pour effectuer ce didacticiel, vous avez besoin de ce qui suit :
* Accomplissez les étapes qui précèdent « Créer une tâche Stream Analytics » du [didacticiel Créer une solution IoT à l’aide de Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-build-an-iot-solution-using-stream-analytics). 
* Utilisez Visual Studio 2015, Visual Studio 2013 mise à jour 4 ou Visual Studio 2012. Les éditions Enterprise (Ultimate/Premium), Professional et Community sont prises en charge. L’édition Express n’est pas prise en charge. Visual Studio 2017 n’est pas pris en charge. 
* Utilisez le Kit SDK Azure pour .NET version 2.7.1 ou ultérieure. Installez-le à l’aide de [Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx).
* Installez les [outils Stream Analytics pour Visual Studio](http://aka.ms/asatoolsvs).

## <a name="create-a-stream-analytics-project"></a>Créer un projet Stream Analytics
1. Dans Visual Studio, cliquez sur le menu **Fichier** et sélectionnez **Nouveau projet**. 

2. Dans la liste des modèles sur la gauche, sélectionnez **Stream Analytics**, puis cliquez sur **Application Azure Stream Analytics**.

3. Entrez le **Nom**, l’**Emplacement** et le **Nom de la solution** du projet, comme vous le feriez pour d’autres projets.

    ![Fenêtre Nouveau projet](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-01.png)

    Un projet **Toll** est généré dans l’**Explorateur de solutions**.

    ![Projet Toll généré dans l’Explorateur de solutions](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-02.png)

## <a name="choose-the-correct-subscription"></a>Choisir le bon abonnement
1. Dans Visual Studio, cliquez sur le menu **Affichage** et ouvrez **Explorateur de serveurs**.

2. Connectez-vous à votre compte Azure. 

## <a name="define-the-input-sources"></a>Définir les sources d’entrée
1.  Dans l’**Explorateur de solutions**, développez le nœud **Entrées** et renommez **Input.json** en **EntryStream.json**. Double-cliquez sur **EntryStream.json**.
2.  L’**Alias d’entrée** est désormais **EntryStream**. L’alias d’entrée est utilisé dans le script de requête. 
3.  Dans **Type de source**, sélectionnez **Data Stream**.
4.  Dans **Source**, sélectionnez **Event Hub**.
5.  Dans **Espace de noms Service Bus**, sélectionnez l’option **TollData**.
6.  Dans **Nom du hub d’événements**, sélectionnez **entry**.
7.  Dans **Nom de la stratégie du hub d’événements**, sélectionnez **RootManageSharedAccessKey** (la valeur par défaut).
8.  Dans **Format de sérialisation de l’événement**, sélectionnez **Json**. 
9.  Dans **Encodage**, sélectionnez **UTF8**. Vos paramètres doivent ressembler à ceux de la capture d’écran suivante :

    ![Fenêtre Entrée](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-01.png)
 
10. Pour terminer l’exécution de l’Assistant, cliquez sur **Enregistrer**. Vous pouvez maintenant ajouter une autre source d’entrée pour créer le flux de sortie. Cliquez avec le bouton droit sur le nœud **Entrées** et sélectionnez **Nouvel élément**.

    ![Nouvel élément](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-02.png)
 
11. Dans la fenêtre, sélectionnez **Entrée Azure Stream Analytics** et modifiez le **Nom** en **ExitStream.json**. Cliquez sur **Ajouter**.

    ![Fenêtre Ajouter un nouvel élément](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-03.png)
 
12. Double-cliquez sur **ExitStream.json** dans le projet et suivez la même procédure que celle que vous avez utilisée pour le flux d’entrée de données. Veillez à entrer **exit** pour le **Nom du hub d’événements**, comme indiqué sur la capture d’écran suivante :

    ![Fenêtre ExitStream](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-04.png)

    Vous avez maintenant défini deux flux d’entrée de données :

    ![Flux d’entrée et de sortie](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-05.png)
 
    À présent, ajoutons une entrée de données de référence pour le fichier blob contenant les données d’inscription des véhicules.

13. Cliquez avec le bouton droit sur le nœud **Entrées** dans le projet, puis suivez la même procédure que celle que vous avez utilisée pour les entrées de flux de données. Dans **Alias d’entrée**, entrez **Registration**, et dans **Type de Source**, sélectionnez **Reference data**.

    ![Fenêtre Inscription](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-06.png)

14. Dans **Compte de stockage**, sélectionnez l’option **tolldata**. Dans **Conteneur**, sélectionnez **tolldata**, et dans **Modèle de chemin d’accès**, entrez **registration.json**. Ce nom de fichier respecte la casse et doit être en minuscules.
15. Pour terminer l’exécution de l’Assistant, cliquez sur **Enregistrer**.

À présent, toutes les entrées sont définies.

## <a name="define-the-output"></a>Définir la sortie
1.  Dans l’**Explorateur de solutions**, développez le nœud **Entrées** et double-cliquez sur **Output.json**.

2.  Dans **Alias de sortie**, entrez **output**. 
3.  Dans **Récepteur**, sélectionnez **SQL Database**.
4.  Dans **Base de données**, sélectionnez **TollDataDB**.
5.  Dans **Nom d’utilisateur**, entrez **tolladmin**. 
6.  Dans **Mot de passe**, entrez **123toll!**.
7.  Dans **Table**, entrez **TollDataRefJoin**.
8.  Cliquez sur **Save**.

    ![Fenêtre Sortie](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-output-01.png)
 
## <a name="create-a-stream-analytics-query"></a>Créer une requête Stream Analytics
Ce didacticiel tente de répondre à différentes questions liées aux données de péage. Il élabore également des requêtes Stream Analytics utilisables dans Stream Analytics pour fournir des réponses pertinentes.
Avant de commencer votre première tâche Stream Analytics, examinons un scénario simple et la syntaxe de requête.

### <a name="introduction-to-the-stream-analytics-query-language"></a>Présentation du langage de requête de Stream Analytics
Supposons que nous devons compter le nombre de véhicules qui entrent dans un poste de péage. Comme cet exemple est un flux continu d’événements, vous devez définir une période. Modifiez la question en « Combien de véhicules entrent en poste de péage toutes les trois minutes ? ». Cette façon de comptabiliser les données s’appelle communément le « nombre bascule ».

Examinez la requête Stream Analytics répondant à cette question :

        SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count 
        FROM EntryStream TIMESTAMP BY EntryTime 
        GROUP BY TUMBLINGWINDOW(minute, 3), TollId 

Stream Analytics utilise un langage de requête similaire à SQL et ajoute quelques extensions pour spécifier les aspects de la requête liés au temps.

Pour plus d’informations, consultez les constructions [Gestion du temps](https://msdn.microsoft.com/library/azure/mt582045.aspx) et [Fenêtrage](https://msdn.microsoft.com/library/azure/dn835019.aspx) utilisées dans la requête à partir de MSDN.

Maintenant que vous avez écrit votre première requête Stream Analytics, il est temps de la tester. Utilisez les exemples de fichiers de données situés dans votre dossier TollApp via le chemin suivant :

..\TollApp\TollApp\Data

Ce dossier contient les fichiers suivants :
*   Entry.json
*   Exit.json
*   registration.json

## <a name="count-the-number-of-vehicles-entering-a-toll-booth"></a>Compter le nombre de véhicules arrivant à un poste de péage
Dans le projet, double-cliquez sur **Script.asaql** pour ouvrir le script dans le l’**Éditeur de requête**. Copiez et collez le script de la section précédente dans l’éditeur. L’Éditeur de requête prend en charge IntelliSense, la coloration syntaxique et le marquage d’erreurs.

![Éditeur de requête](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="test-stream-analytics-queries-locally"></a>Tester localement les requêtes Stream Analytics

1. Pour compiler la requête et voir si elle comporte une erreur de syntaxe, cliquez avec le bouton droit sur le projet et sélectionnez **Créer**. 

2. Pour valider cette requête sur des exemples de données, vous pouvez utiliser des exemples de données locaux. Cliquez avec le bouton droit sur l’entrée et sélectionnez **Ajouter une entrée locale**.

    ![Ajouter une entrée locale](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-01.png)
 
3. Dans la fenêtre indépendante, sélectionnez l’exemple de données à partir de votre chemin local. Cliquez sur **Save**.

    ![Fenêtre Ajouter une entrée locale](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-02.png)
 
    Un fichier nommé **local_EntryStream.json** est automatiquement ajouté à votre dossier d’entrées.

    ![Fichier ajouté au dossier des entrées](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-03.png)
 
4. Dans l’**Éditeur de requête**, cliquez sur **Exécution locale**. Ou vous pouvez appuyer sur la touche F5.

    ![Exécution locale](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-01.png)

    ![Sortie de l’exécution locale](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-02.png)

    Appuyez sur une touche quelconque pour afficher la sortie dans la fenêtre **Résultat de l’exécution locale ASA** de Visual Studio. 

    ![Fenêtre Résultats de l’exécution locale ASA](./media/stream-analytics-tools-for-vs/local-testing-output.png)

5. Cliquez sur **Ouvrir le dossier des résultats** pour vérifier les fichiers de sortie aux formats CSV et JSON.

    ![Sortie Ouvrir le dossier des résultats](./media/stream-analytics-tools-for-vs/local-testing-files.png)
 

### <a name="sample-the-input-data"></a>Échantillonner les données d’entrée
Vous pouvez également constituer des exemples de données à partir de sources d’entrée dans un fichier local. 
1. Cliquez avec le bouton droit sur le fichier de configuration d’entrées et sélectionnez **Exemple de données**. 

   ![Exemple de données](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-01.png)

    Pour l’instant, vous pouvez uniquement échantillonner le hub d’événements ou le hub IoT. Les autres sources d’entrée ne sont pas prises en charge.

2. Dans la fenêtre indépendante, entrez le chemin local utilisé pour enregistrer les exemples de données. Cliquez sur **Exemple**.

    ![Fenêtre Exemples de données](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-02.png)
 
    Vous pouvez voir la progression de l’opération dans la fenêtre **Sortie**. 

    ![Fenêtre Sortie](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-03.png)
 
### <a name="submit-a-stream-analytics-query-to-azure"></a>Envoyer une requête Stream Analytics sur Azure
1. Dans l’**Éditeur de requête**, cliquez sur **Envoyer sur Azure** dans l’éditeur de script.

    ![Envoyer sur Azure](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-01.png)
 
2. Sélectionnez **Créer une tâche Azure Stream Analytics**. Entrez le **Nom de la tâche** et sélectionnez l’**Abonnement** approprié. Cliquez sur **Envoyer**.

    ![Fenêtre Envoyer la tâche](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-02.png)

 
### <a name="start-a-job"></a>Démarrer une tâche
Dès lors que votre tâche est créée, sa vue s’ouvre automatiquement. 
1. Pour commencer cette tâche, cliquez sur le bouton à la **flèche verte**.

    ![Démarrer une tâche](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-01.png)
 
2. Sélectionnez le paramètre par défaut et cliquez sur **Démarrer**.
 
    ![Fenêtre Démarrer une tâche](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-02.png)

    L’**État** de la tâche passe à **En cours d’exécution**, et les **Événements d’entrée** ainsi que les **Événements de sortie** s’affichent.

    ![État en cours d’exécution dans Résumé de la tâche](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-03.png)

## <a name="check-the-results-in-visual-studio"></a>Vérifier les résultats dans Visual Studio
1. Dans Visual Studio, ouvrez l’**Explorateur de serveurs** et cliquez avec le bouton droit sur la table **TollDataRefJoin**.
2. Sélectionnez **Afficher les données de la table** pour voir le résultat de votre tâche.
   
    ![Sélection de l’option Afficher les données de la table dans l’Explorateur de serveurs](media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-check-results.jpg)


### <a name="view-the-job-metrics"></a>Afficher les métriques de tâche
Certaines statistiques des tâches de base se trouvent dans **Métriques de tâche**. 

![Fenêtre Métriques de tâche](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-metrics-01.png)

 
## <a name="list-the-job-in-server-explorer"></a>Répertorier la tâche dans l’Explorateur de serveurs
Dans l’**Explorateur de serveurs**, cliquez sur **Tâches Stream Analytics**, puis sur **Actualiser**. La tâche s’affiche sous **Tâches Stream Analytics**.

![Tâches Stream Analytics répertoriées dans l’Explorateur de serveurs](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-list-jobs-01.png)


## <a name="open-the-job-view"></a>Ouvrir la vue de la tâche
Pour ouvrir la vue de la tâche, développez le nœud de votre tâche et double-cliquez sur le nœud **Vue de la tâche**.

![Nœud Vue de la tâche](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-view-01.png)


## <a name="export-an-existing-job-to-a-project"></a>Exporter un travail existant dans un projet
Il existe deux méthodes pour exporter un travail existant vers un projet.

Dans l’**Explorateur de serveurs**, cliquez avec le bouton droit sur le nœud de la tâche voulue, situé dans le nœud **Tâches Stream Analytics**, et sélectionnez **Exporter vers un nouveau projet Stream Analytics**.

![Exporter vers un nouveau projet Stream Analytics](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-01.png)

Le projet est généré dans l’**Explorateur de solutions**.

![Projet généré dans l’Explorateur de solutions](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-02.png)
 
Vous pouvez aussi utiliser la vue de la tâche et cliquer sur **Générer le projet**.

![Générer le projet](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-03.png)

## <a name="known-issues-and-limitations"></a>Problèmes connus et limitations
 
- Il n’existe aucune prise en charge pour la sortie Power BI et la sortie d’Azure Date Lake Store.
- Il n’existe aucune prise en charge de l’éditeur pour l’ajout ou la modification de fonctions JavaScript définies par l’utilisateur.

## <a name="next-steps"></a>Étapes suivantes
* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Bien démarrer avec Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

