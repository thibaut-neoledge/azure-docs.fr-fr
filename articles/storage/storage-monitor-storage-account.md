<properties
	pageTitle="Surveillance d'un compte de stockage | Microsoft Azure"
	description="Découvrez comment surveiller un compte de stockage dans Azure en utilisant le portail Azure."
	services="storage"
	documentationCenter=""
	authors="tamram"
	manager="adinah"
	editor="cgronlun"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/03/2015"
	ms.author="tamram"/>

# Surveillance d'un compte de stockage dans le portail Azure

## Vue d'ensemble

Vous pouvez surveiller votre compte de stockage à partir du portail Azure. Lorsque vous configurez votre compte de stockage pour la surveillance via le portail, Azure Storage utilise [Storage Analytics](http://msdn.microsoft.com/library/azure/hh343270.aspx) pour effectuer le suivi des mesures pour votre compte et enregistrer les données de la demande.

> [AZURE.NOTE]L'analyse des données de surveillance dans le portail Azure occasionne des frais supplémentaires. Pour plus d'informations, consultez la page <a href="http://msdn.microsoft.com/library/azure/hh360997.aspx">Storage Analytics et facturation</a>. <br />

> Le service de fichier Azure ne prend actuellement pas en charge Storage Analytics.

> Pour obtenir un guide détaillé concernant l'utilisation de Storage Analytics et d'autres outils permettant d'analyser, de diagnostiquer et de résoudre les problèmes d'Azure Storage, consultez [Analyse, diagnostic et résolution des problèmes rencontrés sur Microsoft Azure Storage](../storage-monitoring-diagnosing-troubleshooting/).


## Configuration de la surveillance d'un compte de stockage

1. Dans le [portail Azure](https://manage.windowsazure.com/), cliquez sur **Storage**, puis sur le nom du compte de stockage pour ouvrir le tableau de bord.

2. Cliquez sur **Configure**, puis faites défiler l'écran vers le bas jusqu'aux paramètres de surveillance (**monitoring**) pour les services Blob, Table et File d'attente.

	![MonitoringOptions](./media/storage-monitor-storage-account/Storage_MonitoringOptions.png)

3. Dans **monitoring**, définissez le niveau de surveillance et la stratégie de rétention de données pour chaque service :

	-  Pour définir le niveau de surveillance, sélectionnez l'une des options suivantes :

      **Minimal** : collecte des mesures telles que l'entrée/sortie, la disponibilité, la latence et les pourcentages de réussite, qui sont agrégées pour les services Blob, Table et File d'attente.

      **Verbose** : outre les mesures minimales, collecte le même ensemble de mesures pour chaque opération de stockage de l'API Azure Storage Service. Les mesures détaillées permettent d'analyser plus précisément les problèmes survenant durant le fonctionnement d'une application.

      **Off** : désactive la surveillance. Les données de surveillance existantes sont conservées jusqu'au terme de la période de rétention.

- Pour définir la stratégie de rétention de données, dans **Rétention (en jours)**, tapez le nombre de jours durant lesquels les données sont conservées (de 1 à 365 jours). Si vous ne souhaitez pas définir de stratégie de rétention, entrez 0. Dans ce cas, il vous appartient de supprimer ou non les données de surveillance. Nous vous recommandons de définir une stratégie de rétention en fonction de la durée de conservation que vous souhaitez appliquer aux données d'analyse de votre compte, de sorte que les données d'analyse anciennes et non utilisées puissent être supprimées par le système sans frais.

4. Une fois la configuration de la surveillance terminée, cliquez sur **Save**.

Les données de surveillance doivent commencer à s'afficher dans le tableau de bord et la page **Monitor** au bout d'une heure environ.

Tant que vous n'avez pas configuré la surveillance d'un compte de stockage, aucune collecte de données de surveillance n'intervient et les graphiques de mesures du tableau de bord et la page **Monitor** sont vides.

Après avoir défini les niveaux de surveillance et les stratégies de rétention, vous pouvez choisir parmi les mesures disponibles dans le portail Azure celles à surveiller et celles à représenter sur les graphiques de mesures. Un ensemble de mesures par défaut s'affiche à chaque niveau de surveillance. Vous pouvez utiliser **Add Metrics** pour ajouter ou supprimer des mesures dans la liste de mesures.

Les mesures sont stockées dans le compte de stockage dans quatre tables nommées $MetricsTransactionsBlob, $MetricsTransactionsTable, $MetricsTransactionsQueue et $MetricsCapacityBlob. Pour plus d'informations, consultez la page [À propos des mesures Storage Analytics](http://msdn.microsoft.com/library/azure/hh343258.aspx).


## Personnalisation du tableau de bord à des fins de surveillance

Sur le tableau de bord, vous pouvez choisir jusqu'à six mesures sur les neuf disponibles pour les représenter sur le graphique de mesures. Pour chaque service (Blob, Table et File d'attente), les mesures Availability, Success Percentage et Total Requests sont disponibles. Les mesures disponibles sur le tableau de bord sont les mêmes, quel que soit le type de surveillance (minimale ou détaillée).

1. Dans le [portail Azure](https://manage.windowsazure.com/), cliquez sur **Storage**, puis sur le nom du compte de stockage pour ouvrir le tableau de bord.

2. Pour modifier les mesures représentées sur le graphique, effectuez l'une des actions suivantes :

	- Pour ajouter une nouvelle mesure au graphique, cliquez sur la case à cocher en couleur en regard de l'en-tête des mesures dans le tableau sous le graphique.

	- Pour masquer une mesure représentée sur le graphique, désactivez la case à cocher de couleur en regard de son en-tête.

		![Monitoring\_nmore](./media/storage-monitor-storage-account/storage_Monitoring_nmore.png)

3. Par défaut, le graphique indique les tendances, affichant uniquement la valeur actuelle de chaque mesure (option **Relative** dans la partie supérieure du graphique). Pour afficher un axe Y afin de voir les valeurs absolues, sélectionnez **Absolute**.

4. Pour modifier la plage horaire des mesures affichées dans le graphique, sélectionnez 6 heures, 24 heures ou 7 jours en haut du graphique.


## Personnalisation de la page Monitor

Sur la page **Monitor**, vous pouvez afficher le jeu complet de mesures associé à votre compte de stockage.

- Si vous avez configuré la surveillance minimale pour votre compte de stockage, des mesures telles que l'entrée/sortie, la disponibilité, la latence et les pourcentages de réussite sont agrégées à partir des services Blob, Table et File d'attente.

- Si vous avez configuré la surveillance détaillée, les mesures sont disponibles à une résolution plus fine des opérations de stockage individuelles, en plus des agrégations au niveau des services.

Utilisez les procédures suivantes pour choisir les mesures de stockage à afficher dans les graphiques et le tableau des mesures figurant sur la page **Monitor**. Ces paramètres n'ont aucune incidence sur la collecte, l'agrégation et le stockage de données de surveillance dans le compte de stockage.

## Ajout de mesures au tableau des mesures


1. Dans le [portail Azure](https://manage.windowsazure.com/), cliquez sur **Storage**, puis sur le nom du compte de stockage pour ouvrir le tableau de bord.

2. Cliquez sur **Monitor**.

	La page **Monitor** s'ouvre. Par défaut, le tableau des mesures affiche un sous-ensemble des mesures disponibles pour la surveillance. L'écran Monitor par défaut illustré ci-dessous représente un compte de stockage avec une surveillance détaillée configurée pour les trois services. Utilisez **Add Metrics** pour sélectionner parmi les mesures disponibles celles qui doivent faire l'objet d'une surveillance.

	![Monitoring\_VerboseDisplay](./media/storage-monitor-storage-account/Storage_Monitoring_VerboseDisplay.png)

	> [AZURE.NOTE]Tenez compte des frais au moment de sélectionner les mesures. En effet, l'actualisation des écrans de surveillance s'accompagne de frais de transaction et de sortie. Pour plus d'informations, consultez la page [Storage Analytics et facturation](http://msdn.microsoft.com/library/azure/hh360997.aspx).

3. Cliquez sur **Add Metrics**.

	Les mesures d'agrégation disponibles dans le cadre de la surveillance minimale figurent en haut de la liste. Si la case à cocher est activée, la mesure figure dans la liste des mesures.

	![AddMetricsInitialDisplay](./media/storage-monitor-storage-account/Storage_AddMetrics_InitialDisplay.png)

4. Placez le pointeur dans la partie droite de la boîte de dialogue pour afficher une barre de défilement que vous pouvez actionner pour faire défiler d'autres mesures.

	![AddMetricsScrollbar](./media/storage-monitor-storage-account/Storage_AddMetrics_Scrollbar.png)


5. Cliquez sur la flèche vers le bas en regard d'une mesure pour développer la liste des opérations qu'elle prend en charge. Sélectionnez chaque opération que vous souhaitez voir figurer dans le tableau des mesures du portail Azure.

	Dans l'illustration suivante, la mesure AUTHORIZATION ERROR PERCENTAGE a été développée.

	![ExpandCollapse](./media/storage-monitor-storage-account/Storage_AddMetrics_ExpandCollapse.png)


6. Après avoir sélectionné des mesures pour tous les services, cliquez sur OK (coche) pour mettre à jour la configuration de la surveillance. Les mesures sélectionnées sont ajoutées au tableau des mesures.

7. Pour supprimer une mesure de la table, cliquez sur la mesure pour la sélectionner, puis sur **Delete**.

	![DeleteMetric](./media/storage-monitor-storage-account/Storage_DeleteMetric.png)

## Personnalisation du graphique de mesures de la page Monitor

1. Sur la page **Monitor** du compte de stockage, dans le tableau des mesures, sélectionnez les mesures (six au maximum) à représenter sur le graphique de mesures. Pour sélectionner une mesure, cliquez sur la case à cocher à sa gauche. Pour supprimer une mesure du graphique, désactivez la case à cocher.

2. Pour basculer entre un affichage des valeurs relatives (seule la valeur finale est affichée) et un affichage des valeurs absolues (axe Y affiché) dans le graphique, sélectionnez **Relative** ou **Absolute** en haut du graphique.

3.	Pour modifier la plage horaire des mesures affichées dans le graphique, sélectionnez **6 hours**, **24 hours** ou **7 days** en haut du graphique.



## Configuration de la journalisation

Pour chaque service de stockage accessible à votre compte de stockage (Blob, Table et File d'attente), vous pouvez enregistrer des journaux de diagnostic pour les demandes de lecture, d'écriture et/ou de suppression. De même, vous pouvez définir la stratégie de rétention de données de chaque service.

1. Dans le [portail Azure](https://manage.windowsazure.com/), cliquez sur **Storage**, puis sur le nom du compte de stockage pour ouvrir le tableau de bord.

2. Cliquez sur **Configure**, puis faites défiler la page **logging** à l'aide de la touche Bas du clavier.

	![Storagelogging](./media/storage-monitor-storage-account/Storage_LoggingOptions.png)


3. Pour chaque service (Blob, Table et File d'attente), configurez les paramètres suivants :

	- Types de demande à journaliser : demandes de lecture, d'écriture et de suppression.

	- Le nombre de jours durant lesquels les données journalisées sont conservées. Entrez 0 si vous ne souhaitez pas définir de stratégie de rétention. Dans ce cas, il vous appartient de supprimer ou non les journaux.

4. Cliquez sur **Enregistrer**.

Les journaux de diagnostic sont enregistrés dans un conteneur d'objets blob nommé $logs dans votre compte de stockage. Pour plus d'informations sur l'accès au conteneur $logs, consultez la page [À propos de la journalisation Storage Analytics](http://msdn.microsoft.com/library/azure/hh343262.aspx).

<!---HONumber=Sept15_HO2-->