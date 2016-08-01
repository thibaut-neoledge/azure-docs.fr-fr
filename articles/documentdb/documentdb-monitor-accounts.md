<properties
	pageTitle="Surveiller les demandes DocumentDB et le stockage | Microsoft Azure"
	description="Découvrez comment surveiller, dans votre compte DocumentDB, les mesures de performances (notamment les demandes et les erreurs de serveur) et les mesures d'utilisation (par exemple, l'espace de stockage utilisé)."
	services="documentdb"
	documentationCenter=""
	authors="mimig1"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/10/2016"
	ms.author="mimig"/>

# Surveiller les demandes DocumentDB, l’utilisation et le stockage

Vous pouvez surveiller vos comptes Azure DocumentDB dans le [portail Microsoft Azure](https://portal.azure.com/). Pour chaque compte DocumentDB, des mesures de performances (notamment les demandes et les erreurs de serveur) et des mesures d'utilisation (par exemple l'espace de stockage utilisé) sont disponibles.

## Affichage des mesures de performances dans le portail 
1.	Dans une nouvelle fenêtre, ouvrez le [portail Azure](https://portal.azure.com/), cliquez sur **Parcourir**, puis sur **Comptes DocumentDB**. Cliquez ensuite sur le nom du compte DocumentDB dont vous souhaitez afficher les mesures de performances.
2.	Dans le filtre **Surveillance**, vous pouvez, par défaut, voir :
	*	le nombre total de demandes pour le jour en cours ;
	*	Nombre total de RU ([unités de demande](documentdb-request-units.md)) utilisées au cours de la journée.
	*	Stockage utilisé.

	Si votre table affiche **Aucune donnée disponible**, consultez la section [Dépannage](#troubleshooting).

	![Capture d'écran du filtre de suivi qui affiche le nombre total de demandes pour aujourd'hui ainsi que l’utilisation du stockage](./media/documentdb-monitor-accounts/documentdb-total-requests-and-usage.png)


3.	Cliquez sur la mosaïque **Demandes**, **Total des demandes** ou **Stockage** pour ouvrir un panneau détaillé **Métrique**.
4.	Le panneau **Métrique** affiche les détails des mesures que vous avez sélectionnées. La partie supérieure du panneau affiche un graphique des demandes représentées toutes les heures, tandis que la partie inférieure montre un tableau affichant les valeurs d’agrégation pour les demandes limitées et totales. Le volet Métrique affiche également la liste des alertes définies, filtrées en fonction des mesures apparaissant dans le volet ouvert. Ainsi, si vous avez plusieurs alertes, seules celles qui sont pertinentes sont affichées ici.

	![Capture d’écran du panneau Métrique incluant des demandes limitées](./media/documentdb-monitor-accounts/documentdb-metric-blade.png)


## Personnalisation des vues des mesures de performances dans le portail

1.	Pour personnaliser les mesures qui s'affichent dans un graphique particulier, cliquez sur le graphique pour l’ouvrir dans le panneau **Métrique**, puis cliquez sur **Modifier le graphique**.![Capture d’écran des contrôles du panneau Métrique, avec l’option Modifier le graphique mise en surbrillance](./media/documentdb-monitor-accounts/madocdb3.png)

2.	Dans le panneau **Modifier le graphique**, des options permettent de modifier les mesures qui s'affichent dans le graphique, ainsi que leur plage horaire. ![Capture d'écran du panneau Modifier le graphique](./media/documentdb-monitor-accounts/madocdb4.png)

3.	Pour modifier les mesures affichées dans cette partie, sélectionnez simplement ou désactiver les mesures de performances disponibles, puis cliquez sur **OK** en bas du panneau.
4.	Pour modifier la plage horaire, choisissez une autre plage (par exemple : **Personnalisée**), puis cliquez sur **OK** en bas du panneau.

	![Capture d'écran de la partie Plage horaire du panneau Modifier le graphique montrant comment saisir une plage horaire personnalisée](./media/documentdb-monitor-accounts/madocdb5.png)


## Création de graphiques côte à côte dans le portail
Le portail Azure vous permet de créer des graphiques côte à côte.

1.	Tout d'abord, cliquez avec le bouton droit sur le graphique à copier et sélectionnez **Personnaliser**.

	![Capture d'écran du graphique du nombre total des demandes avec l’option Personnaliser mise en surbrillance](./media/documentdb-monitor-accounts/madocdb6.png)

2.	Cliquez sur **Cloner** dans le menu pour copier la partie, puis cliquez sur **Personnalisation terminée**.

	![Capture d'écran du graphique du nombre total des demandes avec les options Cloner et Personnalisation terminée mises en surbrillance](./media/documentdb-monitor-accounts/madocdb7.png)


Vous pouvez maintenant considérer cette partie comme n'importe quelle mesure en personnalisant les mesures et la plage horaire affichées. Ainsi, vous pouvez afficher deux graphiques de mesures différents, côte à côte, en même temps. ![Capture d'écran du graphique du nombre total des demandes et le graphique nouveau nombre total des demandes pour l’heure précédente](./media/documentdb-monitor-accounts/madocdb8.png)

## Configuration d’alertes dans le portail
1.	Dans le [portail Azure](https://portal.azure.com/), cliquez sur **Parcourir**, puis sur **Comptes DocumentDB**. Cliquez ensuite sur le nom du compte DocumentDB pour lequel vous souhaitez configurer les alertes de mesures de performances.

2.	Si le panneau **Tous les paramètres** n’est pas ouvert, cliquez sur la commande **Paramètres** en haut à gauche pour ouvrir le panneau. ![Capture d’écran de la commande Paramètres dans le panneau Compte de base de données](./media/documentdb-monitor-accounts/madocdb10.png)

3.	Dans le panneau **Tous les paramètres**, cliquez sur **Règles d’alerte** pour ouvrir le panneau des règles d’alerte. ![Capture d’écran, avec la partie Règles d’alerte sélectionnée](./media/documentdb-monitor-accounts/madocdb10.5.png)

4.	Dans le panneau Règles d'alerte, cliquez sur** Ajouter une alerte**. ![Capture d'écran du panneau Règles d'alerte, avec le bouton Ajouter une alerte mis en surbrillance](./media/documentdb-monitor-accounts/madocdb11.png)

5.	Dans le volet **Ajouter une règle d'alerte**, indiquez :
	*	le nom de la règle d'alerte que vous configurez ;
	*	une description de la nouvelle règle d'alerte ;
	*	la mesure de la règle d'alerte ;
	*	la condition, le seuil et la période déterminant l'activation de l'alerte. Par exemple, un nombre d'erreurs de serveur supérieur à 5 au cours des 15 dernières minutes.
	*	l'envoi ou non d'un courrier électronique à l'administrateur de service ou aux coadministrateurs lorsque l'alerte se déclenche ;
	*	Des adresses électroniques supplémentaires pour les notifications d'alerte. ![Capture d’écran du panneau Ajouter une règle d'alerte](./media/documentdb-monitor-accounts/madocdb12.png)

## Surveillance de DocumentDB par programme
Les métriques au niveau du compte disponibles dans le portail, par exemple les demandes d'utilisation du stockage du compte et de total, ne sont pas disponibles via les API DocumentDB. Toutefois, vous pouvez extraire des données d'utilisation au niveau de la collection à l’aide des API DocumentDB. Pour extraire des données au niveau de la collection, procédez comme suit :

- Pour utiliser l’API REST, [effectuez une opération GET sur la collection](https://msdn.microsoft.com/library/mt489073.aspx). Les informations de quota et d'utilisation de la collection sont retournées dans les en-têtes x-ms-resource-quota et x-ms-resource-usage de la réponse.
- Pour utiliser le Kit de développement logiciel (SDK) .NET, utilisez la méthode [DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx), qui retourne une valeur [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx) contenant un nombre de propriétés d’utilisation, notamment **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage** et plus encore.

Pour accéder à des mesures supplémentaires, utilisez le [SDK Azure Insights](https://www.nuget.org/packages/Microsoft.Azure.Insights). Les définitions de mesures disponibles peuvent être récupérées en appelant :

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08 

Les requêtes permettant de récupérer des mesures individuelles utilisent le format suivant :

    https://management.azure.com/subscriptions/{SubecriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z

Pour plus d’informations, consultez [Récupération des mesures de ressources via l’API Azure Insights](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/02/23/retrieving-resource-metrics-via-the-azure-insights-api/).

## Résolution de problèmes
Si vos mosaïques d’analyse affichent le message **Aucune donnée disponible** et que vous avez récemment effectué des requêtes ou ajouté des données à la base de données, vous pouvez modifier la mosaïque pour refléter l’utilisation récente.

### Modification d'une mosaïque pour actualiser les données actuelles
1.	Pour personnaliser les mesures qui s'affichent dans une partie particulière, cliquez sur le graphique pour ouvrir le panneau **Métrique**, puis cliquez sur **Modifier le graphique**.![Capture d’écran des contrôles du panneau Métrique, avec l’option Modifier le graphique mise en surbrillance](./media/documentdb-monitor-accounts/madocdb3.png)

2.	Dans le panneau **Modifier le graphique**, dans la section **Intervalle de temps**, cliquez sur **Dernière heure**, puis sur **OK**. ![Capture d'écran du panneau Modifier le graphique avec la dernière heure sélectionnée](./media/documentdb-monitor-accounts/documentdb-no-available-data-past-hour.png)


3.	Votre mosaïque doit s’actualiser et afficher vos données actuelles et votre utilisation. ![Capture d'écran de la mosaïque indiquant le nombre total de requêtes, mis à jour pour la dernière heure](./media/documentdb-monitor-accounts/documentdb-no-available-data-fixed.png)

## Étapes suivantes
Pour en savoir plus sur la capacité de DocumentDB, consultez [Gestion de la capacité de DocumentDB](documentdb-manage.md).

<!---HONumber=AcomDC_0720_2016-->