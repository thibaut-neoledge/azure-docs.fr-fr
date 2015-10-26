<properties 
	pageTitle="Ajout de sorties | Microsoft Azure" 
	description="Segment du parcours d'apprentissage Ajout de sorties."
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="09/29/2015" 
	ms.author="jeffstok"/>

# Ajout de sorties

Les tâches Azure Stream Analytics peuvent être connectées à une ou plusieurs sorties, qui définissent une connexion à un récepteur de données existant. Lorsque votre tâche Stream Analytics traite et transforme les données entrantes, un flux d'événements de sortie est écrit dans la sortie de votre tâche.

Les sorties Stream Analytics peuvent servir de source aux tableaux de bord ou aux alertes en temps réel, déclencher des workflows en aval ou simplement archiver les données pour le traitement par lot ultérieurement. Stream Analytics bénéficie d'une intégration de première classe avec plusieurs services Azure, qui sont expliqués en détail ici.

Pour ajouter une sortie à votre tâche Stream Analytics :

1. Dans le portail Azure, cliquez sur **Sorties**, puis sur **Ajouter une sortie** dans votre tâche Stream Analytics.

    ![Ajout de sorties](./media/stream-analytics-add-outputs/1-stream-analytics-add-outputs.png)

    Dans le portail Azure en version préliminaire, cliquez sur le titre **Sorties** de votre tâche Stream Analytics.

    ![Ajout de sorties dans le portail Azure](./media/stream-analytics-add-outputs/5-stream-analytics-add-outputs.png)

2. Spécifiez le type de la sortie :

    ![Choix du type de données](./media/stream-analytics-add-outputs/2-stream-analytics-add-outputs.png)

    ![Choix du type de données dans le portail Azure en version préliminaire](./media/stream-analytics-add-outputs/6-stream-analytics-add-outputs.png)

3. Attribuez un nom convivial à cette sortie dans la zone **Alias de sortie**. Ce nom pourra être utilisé dans la requête de votre tâche plus tard pour faire référence à la sortie.
    
    Renseignez le reste des propriétés de connexion requises pour vous connecter à votre sortie. Ces champs varient selon le type de sortie et sont définis en détail ici.

    ![Ajout de propriétés](./media/stream-analytics-add-outputs/3-stream-analytics-add-outputs.png)

4. Selon le type de sortie, vous devrez peut-être spécifier comment les données sont sérialisées ou mises en forme. Les paramètres de sérialisation spécifiques pour chaque type de sortie sont décrits ici.

    Renseignez le reste des propriétés de connexion requises pour vous connecter à votre source de données. Ces champs varient selon le type d'entrée et le type de source, et sont définis en détail [ici](stream-analytics-create-a-job.md).

    ![Ajout d'un concentrateur d'événements](./media/stream-analytics-add-outputs/4-stream-analytics-add-outputs.png)

    ![Ajout d’un concentrateur d’événements dans le portail Azure en version préliminaire](./media/stream-analytics-add-outputs/7-stream-analytics-add-outputs.png)

## Obtenir de l'aide
Pour obtenir une assistance, essayez notre [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/fr-FR/home?forum=AzureStreamAnalytics)

## Étapes suivantes

- [Présentation d'Azure Stream Analytics](stream-analytics-introduction.md)
- [Prise en main d'Azure Stream Analytics](stream-analytics-get-started.md)
- [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
- [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=Oct15_HO3-->