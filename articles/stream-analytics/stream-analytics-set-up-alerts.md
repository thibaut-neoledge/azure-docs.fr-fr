<properties 
	pageTitle="Alertes Stream Analytics | Microsoft Azure" 
	description="Présentation des alertes Stream Analytics" 
	keywords="big data analytics,cloud service,internet of things,managed service,stream processing,streaming analytics,streaming data"
	services="stream-analytics" 
	documentationCenter="" 
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

# Configuration d'alertes

## Page de surveillance

Vous pouvez configurer une règle pour déclencher une alerte lorsqu'une mesure atteint une condition que vous spécifiez.

Par exemple, Si les événements de sortie des 15 dernières sont < 100, envoyer une notification par courrier électronique à l'ID de courrier électronique : xyz@company.com”.

Les règles peuvent être configurées sur des mesures à l'aide du portail. Elles peuvent aussi être configurées [par programme](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a) sur les données des journaux des opérations.

## Configuration d'alertes par le biais du portail Azure

Il existe deux façons de configurer des alertes :

1.	L'onglet Surveillance de votre tâche Stream Analytics  
2.	Le Journal des opérations dans les Services de gestion  

## Alertes avec l'onglet Surveillance de la tâche dans le portail

1.	Sélectionnez la mesure dans l'onglet de surveillance, cliquez sur le bouton Ajouter une règle dans la partie inférieure du tableau de bord et configurez vos règles.  

    ![Tableau de bord](./media/stream-analytics-set-up-alerts/01-stream-analytics-set-up-alerts.png)

2.	Entrez un nom et une description pour l'alerte

    ![Création de la règle](./media/stream-analytics-set-up-alerts/02-stream-analytics-set-up-alerts.png)

3.	Entrez les seuils, la fenêtre d'évaluation d'alerte et les actions de l'alerte

    ![Définition des conditions](./media/stream-analytics-set-up-alerts/03-stream-analytics-set-up-alerts.png)

## Configuration d'alertes par le biais des journaux des opérations

1.	Accédez à l'onglet **Alertes** ans les Services de gestion du [portail Azure](https://manage.windowsazure.com)  
2.	Cliquez sur **Ajouter une règle**  

    ![Critères](./media/stream-analytics-set-up-alerts/04-stream-analytics-set-up-alerts.png)

3.	Entrez un nom et une description pour l'alerte. Sélectionnez « Stream Analytics » comme Type de service et le nom de la tâche comme Nom du service.

    ![Définition de l'alerte](./media/stream-analytics-set-up-alerts/05-stream-analytics-set-up-alerts.png)

## Obtenir de l'aide
Pour obtenir une assistance, essayez notre [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/fr-FR/home?forum=AzureStreamAnalytics)

## Étapes suivantes

- [Présentation d'Azure Stream Analytics](stream-analytics-introduction.md)
- [Prise en main d'Azure Stream Analytics](stream-analytics-get-started.md)
- [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
- [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=Sept15_HO2-->