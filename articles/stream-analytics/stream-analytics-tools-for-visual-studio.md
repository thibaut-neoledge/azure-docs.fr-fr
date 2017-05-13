---
title: Utilisation d&quot;outils Azure Stream Analytics pour Visual Studio | Microsoft Docs
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
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: cbde312e1f58cb69a7c5edbd22543732a0781fdb
ms.contentlocale: fr-fr
ms.lasthandoff: 05/03/2017


---
# <a name="use-azure-stream-analytics-tool-for-visual-studio"></a>Utilisation d'outils Azure Stream Analytics pour Visual Studio
## <a name="introduction"></a>Introduction
Dans ce didacticiel, vous allez apprendre à utiliser les outils Azure Stream Analytics pour Visual Studio pour créer, tester localement, gérer et déboguer vos travaux Azure Stream Analytics. 

Après avoir effectué ce didacticiel, vous pourrez :
* bien connaître les outils Azure Stream Analytics pour Visual Studio ;
* configurer et déployer un travail Stream Analytics ;
* tester votre travail en local avec des exemples de données locaux ;
* utiliser la surveillance pour résoudre les problèmes ;
* exporter des travaux existants dans des projets.

## <a name="prerequisites"></a>Composants requis
Pour suivre ce didacticiel, vous avez besoin des composants suivants :
* Avant de **créer un travail Stream Analytics**, terminez les étapes du [didacticiel Créer une solution IoT à l’aide de Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-build-an-iot-solution-using-stream-analytics). 
* Visual Studio 2015, Visual Studio 2013 mise à jour 4 ou Visual Studio 2012. Les éditions Enterprise (Ultimate/Premium), Professional et Community sont prises en charge ; l’édition Express n’est pas prise en charge. Visual Studio 2017 n’est pas pris en charge pour l’instant. 
* Kit de développement logiciel (SDK) Microsoft Azure pour .NET version 2.7.1 ou ultérieure.  Installez-le avec [Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx).
* Installation des [Azure Stream Analytics Tools pour Visual Studio](http://aka.ms/asatoolsvs).

## <a name="create-a-stream-analytics-project"></a>Création d’une tâche Stream Analytics
Dans Visual Studio, cliquez dans le **menu Fichier** et choisissez **Nouveau projet**. Choisissez **Stream Analytics** dans la liste des modèles sur la gauche, puis cliquez sur **Application Azure Stream Analytics**.
Entrez le nom du projet, l’emplacement et le nom de la solution en bas, comme vous le feriez pour d’autres projets.

![Créer un projet Azure Stream Analytics](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-01.png)

Vous verrez un projet **Toll** généré dans **l’Explorateur de solutions**.

![Créer un projet Azure Stream Analytics](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-02.png)

## <a name="choose-the-correct-subscription"></a>Choix de l'abonnement approprié
1. Ouvrez **l’Explorateur de serveurs** dans Visual Studio dans le menu **Affichage**.
2. Connectez-vous à votre compte Azure. 

## <a name="define-input-sources"></a>Définir les sources d’entrée
1.    Dans **l’Explorateur de solutions**, développez le nœud **Entrées** et renommez **Input.json** en **EntryStream.json**. Double-cliquez sur **EntryStream.json**.
2.    Votre **alias d’entrée** doit être maintenant **EntryStream**. Veuillez noter que l’alias d’entrée est celui qui sera utilisé dans le script de requête. 
3.    Le type de source est **Flux de données**.
4.    La Source est **Event Hub**.
5.    L’espace de noms Service Bus doit être **tollData** dans la liste déroulante.
6.    Le nom de l'event hub doit être défini sur **entrée**.
7.    Le nom de la stratégie du hub d’événements est **RootManageSharedAccessKey** (valeur par défaut).
8.    Sélectionnez **JSON** pour l’option **Format de sérialisation de l’événement**, et **UTF8** pour **Encodage**.

    Vos paramètres ressemblent à ceci :

    ![Définir les sources d’entrée](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-01.png)
 
9.    Cliquez sur **Enregistrer** en bas de la page pour terminer l’exécution de l’Assistant. Vous pouvez maintenant ajouter une autre source d’entrée pour créer le flux de sortie. Cliquez sur le nœud d’entrées, puis sur **Nouvel élément**.

    ![Définir les sources d’entrée](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-02.png)
 
10.    Dans la fenêtre contextuelle en haut, choisissez **Entrée Azure Stream Analytics** et modifiez le nom en **ExitStream.json**. Cliquez sur **Ajouter**.

    ![Définir les sources d’entrée](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-03.png)
 
11.    Double-cliquez sur **ExitStream.json** dans le projet et suivez la même procédure que le flux d’entrée pour le renseigner. Veillez à entrer des valeurs pour le nom d'Event Hub comme sur la capture d’écran suivante.

    ![Définir les sources d’entrée](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-04.png)

    Vous avez maintenant défini deux flux d’entrée.

    ![Définir les sources d’entrée](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-05.png)
 
    À présent, nous allons ajouter une entrée de données de référence pour le fichier blob contenant les données d’inscription des véhicules.

12.    Cliquez avec le bouton droit sur le nœud **Entrées**, puis suivez la même procédure pour les entrées de flux de données, mais en sélectionnant **DONNÉES DE RÉFÉRENCE** au lieu de Flux de données et l’alias d’entrée pour **Inscription**.

    ![Définir les sources d’entrée](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-06.png)

13.    Sélectionnez le compte de stockage contenant **tolldata**. Le nom de conteneur doit être **tolldata** et le **Modèle de chemin d’accès** doit être **registration.json**. Ce nom de fichier respecte la casse et doit être en minuscules.
14.    Cliquez sur **Enregistrer** pour terminer l’exécution de l’Assistant.

Désormais, toutes les entrées sont définies.

## <a name="define-output"></a>Définir la sortie
1.    Dans **l’Explorateur de solutions**, développez le nœud **Entrées** et double-cliquez sur **Output.json**.
2.    Définissez l’Alias de sortie sur **sortie**, puis le Récepteur sur SQL database.
2.    Entrez le nom de la base de données : **TollDataDB**.
3.    Entrez **tolladmin** dans le champ **NOM D’UTILISATEUR**, **123toll!** dans le champ **MOT DE PASSE** et **TollDataRefJoin** dans le champ **TABLE**.
4.    Cliquez sur **Enregistrer**.

![Définir la sortie](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-output-01.png)
 
## <a name="azure-stream-analytics-query"></a>Requête Azure Stream Analytics
Ce didacticiel tente de répondre à plusieurs questions liées aux données de péage, et montre comment construire des requêtes Stream Analytics utilisables dans Azure Stream Analytics pour fournir une réponse pertinente.
Avant de commencer votre premier travail Stream Analytics, examinons quelques scénarios simples et la syntaxe de requête.

### <a name="introduction-to-azure-stream-analytics-query-language"></a>Présentation du langage de requête d’Azure Stream Analytics
Supposons que nous devons compter le nombre de véhicules qui entrent dans un poste de péage. Comme il s’agit d’un flux continu d’événements, nous devons définir une « période ». Modifions la question en « Combien de véhicules entrent en poste de péage en trois minutes ? ». C’est ce qu’on appelle communément le « nombre bascule ».

Examinons la requête d’Azure Stream Analytics répondant à cette question :

        SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count 
        FROM EntryStream TIMESTAMP BY EntryTime 
        GROUP BY TUMBLINGWINDOW(minute, 3), TollId 

Comme vous pouvez le voir, Azure Stream Analytics utilise un langage de requête similaire à SQL et ajoute quelques extensions pour spécifier les aspects de la requête liés au temps.

Pour plus de détails, consultez les constructions [Gestion du temps](https://msdn.microsoft.com/library/azure/mt582045.aspx) et [Fenêtrage](https://msdn.microsoft.com/library/azure/dn835019.aspx) utilisées dans la requête à partir de MSDN.

Notre première requête Azure Stream Analytics étant écrite, nous pouvons la tester à l’aide des exemples de fichiers de données figurant dans votre dossier TollApp au chemin suivant :

<seg>
  **..\TollApp\TollApp\Data**</seg>

Ce dossier contient les fichiers suivants : • Entry.json •   Exit.json •   Registration.json

## <a name="question-number-of-vehicles-entering-a-toll-booth"></a>Question : nombre de véhicules arrivant à un poste de péage
Dans le projet, double-cliquez sur Script.asaql pour ouvrir le script dans l’éditeur et collez le script de la section précédente dans l’éditeur. L’éditeur de requête prend en charge Intellisense, la coloration syntaxique et le marquage d’erreurs.

![Modifier la requête](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="testing-azure-stream-analytics-queries-locally"></a>Test local de requêtes Azure Stream Analytics

1.    Vous pouvez tout d’abord cliquer avec le bouton droit sur le projet et choisir **Créer** pour compiler la requête et voir si elle comporte une erreur de syntaxe. 

2.    Pour valider cette requête par rapport aux exemples de données, vous pouvez utiliser les exemples de données locaux en double-cliquant sur l’entrée et sélectionner **Ajouter une entrée locale** dans le menu contextuel.

    ![Ajouter une entrée locale](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-01.png)
 
    Dans la fenêtre contextuelle, sélectionnez l’exemple de données depuis votre chemin d’accès local. Cliquez sur **Save**.

    ![Ajouter une entrée locale](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-02.png)
 
    Un fichier nommé **local_EntryStream.json** est automatiquement ajouté à votre dossier d’entrées.

    ![Ajouter une entrée locale](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-03.png)
 
3.    Dans l’éditeur de requête, cliquez sur Exécution locale. Ou vous pouvez appuyer sur F5.

    ![Exécution locale](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-01.png)

    ![Exécution locale](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-02.png)

    Vous pouvez appuyer sur une touche quelconque pour afficher la sortie dans la fenêtre de résultat de l’exécution locale ASA dans Visual Studio. 

    ![Exécution locale](./media/stream-analytics-tools-for-vs/local-testing-output.png)

4.  Vous pouvez ouvrir le dossier de résultat pour vérifier les fichiers de sortie aux formats CSV et JSON.

    ![Exécution locale](./media/stream-analytics-tools-for-vs/local-testing-files.png)
 

### <a name="sample-input"></a>Exemple d’entrée
Vous pouvez également copier des exemples de données de sources d’entrée dans un fichier local. Cliquez avec le bouton droit sur le fichier de configuration d’entrée et sélectionnez **Exemple de données**. 

![Exemples de données](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-01.png)

Notez que vous ne pouvez utiliser que des exemples de données d'Event Hub ou IoT Hub pour l’instant. Les autres sources d’entrée ne sont pas prises en charge.  Dans la fenêtre contextuelle, renseignez le chemin d’accès local pour enregistrer les exemples de données. Cliquez sur **Exemple**.

![Exemples de données](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-02.png)
 
Vous pouvez voir la progression de l'installation dans la fenêtre de résultat. 

![Exemples de données](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-03.png)
 
### <a name="submit-azure-stream-analytics-query-to-azure"></a>Envoyer une requête Azure Stream Analytics vers Azure
Dans **l’éditeur de requête**, cliquez sur **Envoyer vers Azure dans l’éditeur de script**.

![Envoi du travail](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-01.png)
 
Choisissez Créer une tâche Azure Stream Analytics. Entrez le nom de la tâche comme indiqué ci-dessous. Choisissez l'abonnement approprié. Cliquez sur Envoyer.

![Envoi du travail](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-02.png)

 
### <a name="start-job"></a>Démarrage du travail
Maintenant, votre travail est créé et ouvert automatiquement. Cliquez sur le bouton **VERT** pour démarrer le travail.

![Démarrage du travail](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-01.png)
 
Choisissez le paramètre par défaut, puis cliquez sur **Démarrer**.
 
![Démarrage du travail](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-02.png)

Vous pouvez voir que l’état du travail est passé à **En cours d’exécution** et qu'il existe des événements d’entrée/de sortie.

![Démarrage du travail](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-03.png)

## <a name="check-results-in-visual-studio"></a>Vérifier les résultats dans Visual Studio
1. Ouvrez l’Explorateur de serveurs Visual Studio, puis cliquez avec le bouton droit sur la table **TollDataRefJoin** .
2. Cliquez sur **Afficher les données de la table** pour voir le résultat de votre tâche.
   
    ![Sélection de « Afficher les données de la table » dans l’Explorateur de serveurs](media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-check-results.jpg)


### <a name="view-job-metrics"></a>Affichage des mesures de travail
Certaines statistiques de travail de base se trouvent dans **Mesures du travail**. 

![Mesures de travail](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-metrics-01.png)

 
## <a name="list-job-in-server-explorer"></a>Répertorier le travail dans l'Explorateur de serveurs
Cliquez sur **Tâches Stream Analytics** dans **l'Explorateur de serveurs**, puis sur **Actualiser**. Vous devez voir votre travail sous **Tâches Stream Analytics**.
![Répertorier les travaux](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-list-jobs-01.png)


## <a name="open-job-view"></a>Ouvrir la vue des travaux
Développez le nœud de votre projet et double-cliquez sur le nœud **Vue des travaux** pour ouvrir la vue des travaux.

![Vue des travaux](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-view-01.png)


## <a name="export-an-existing-job-to-a-project"></a>Exporter un travail existant dans un projet
Il existe deux méthodes pour exporter un travail existant vers un projet.
1. Cliquez avec le bouton droit sur le nœud de travail sous le nœud **Tâches Stream Analytics** dans **l’Explorateur de serveurs**. Cliquez sur **Exporter vers un nouveau projet Stream Analytics** dans le menu contextuel.

    ![Exporter le travail](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-01.png)

    Vous voyez le projet généré dans **l’Explorateur de solutions**.

    ![Exporter le travail](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-02.png)
 
2. Dans la vue des travaux, cliquez sur **Générer le projet**.

    ![Exporter le travail](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-03.png)

## <a name="known-issues-and-limitations"></a>Problèmes connus et limitations
 
1. Aucune prise en charge pour les sorties Power BI et les sorties ADLS.
2. Aucune prise en charge de l’éditeur pour l’ajout ou la modification de fonctions définies par l'utilisateur JavaScript.

## <a name="next-steps"></a>Étapes suivantes
* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d’Azure Stream Analytics](stream-analytics-get-started.md)
* [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

