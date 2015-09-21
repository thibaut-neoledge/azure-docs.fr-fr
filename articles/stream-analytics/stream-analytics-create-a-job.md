<properties 
	pageTitle="Création d'une tâche | Microsoft Azure" 
	description="Segment du parcours d'apprentissage Création d'une tâche."
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
	ms.date="09/09/2015" 
	ms.author="jeffstok"/>

# Création d'une tâche

La ressource de niveau supérieur dans Azure Stream Analytics est une tâche Stream Analytics. Elle se compose d'une ou plusieurs sources de données d'entrée, une requête exprimant la transformation de données et une ou plusieurs cibles de sortie où les résultats sont écrits.

Pour commencer à utiliser Stream Analytics, créez une tâche Stream Analytics. Notez que cette action n'a aucune incidence de facturation tant que la tâche n'a pas démarré.

1.  Connectez-vous au [portail Microsoft Azure](http://manage.windowsazure.com) en ligne.
2.  **Cliquez sur Nouveau**, puis cliquez sur **Data Services**, puis cliquez sur **Stream Analytics** et **Création rapide**.

    ![Assistant Création rapide](./media/stream-analytics-create-a-job/1-stream-analytics-create-a-job.png)

3.  Spécifiez la configuration souhaitée pour la tâche Stream Analytics.
	- Dans la zone **Nom de la tâche**, entrez un nom pour identifier la tâche Stream Analytics. Lorsque le **nom de la tâche** est validé, une coche verte s'affiche dans la zone Nom de la tâche. Le **Nom de la tâche** ne peut contenir que des caractères alphanumériques et le caractère « - », et doit compter entre 3 et 63 caractères.
	- Utilisez la **Région** pour spécifier l'emplacement géographique où vous souhaitez exécuter la tâche.
	- Dans **Compte de stockage de surveillance régionale**, sélectionnez ou créez un compte de stockage. Ce compte de stockage est utilisé pour stocker les données de surveillance de toutes les tâches Stream Analytics en cours d'exécution dans cette région.

4.  Une fois les options de la nouvelle tâche Stream Analytics configurées, cliquez sur **Créer une tâche Stream Analytics**. La création de la tâche Stream Analytics peut prendre plusieurs minutes. Pour vérifier l'état, vous pouvez suivre l'avancement dans le concentrateur de notifications.

    ![Concentrateur de notifications](./media/stream-analytics-create-a-job/2-stream-analytics-create-a-job.png)

5.  Le nouveau travail est affiché avec l’état **Créé**. Notez que le bouton **Démarrer** est désactivé. Avant de pouvoir démarrer la tâche, vous devez configurer son entrée, sa requête et sa sortie.

    ![Statut de tâche](./media/stream-analytics-create-a-job/3-stream-analytics-create-a-job.png)

## Obtenir de l'aide
Pour obtenir une assistance, essayez notre [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/fr-FR/home?forum=AzureStreamAnalytics)

## Étapes suivantes

- [Présentation d'Azure Stream Analytics](stream-analytics-introduction.md)
- [Prise en main d'Azure Stream Analytics](stream-analytics-get-started.md)
- [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
- [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=Sept15_HO2-->