<properties
	pageTitle="Mise à l’échelle des travaux Stream Analytics pour augmenter le débit | Microsoft Azure"
	description="Découvrez comment mettre à l’échelle des travaux Stream Analytics en configurant des partitions d’entrée, en réglant la définition de requête et en configurant les unités de diffusion en continu d’un travail."
	keywords="analytics jobs,data stream,data streaming"
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
	ms.date="09/29/2015"
	ms.author="jeffstok"/>

# Mise à l’échelle des travaux Azure Stream Analytics pour augmenter le débit #

Découvrez comment calculer des *unités de diffusion en continu* pour des travaux Stream Analytics et comment mettre à l’échelle des travaux Stream Analytics en configurant des partitions d’entrée, en réglant la définition de requête et en définissant les unités de diffusion en continu d’un travail.

## Quelles sont les parties d’un travail Stream Analytics ? ##
La définition d'un travail Stream Analytics se compose d'entrées, d'une requête et d'une sortie. Les entrées correspondent à l’emplacement à partir duquel le travail lit le flux de données, la requête permet de transformer le flux d’entrée de données, et la sortie correspond à l’emplacement où le travail envoie ses résultats.

Un travail nécessite au moins une source d’entrée pour la diffusion de données en continu. La source d'entrée de flux de données peut être stockée sur un concentrateur d'événements Service Bus Azure ou un objet blob de stockage Azure. Pour plus d'informations, consultez [Présentation d'Azure Stream Analytics](stream-analytics-introduction.md), [Prise en main d'Azure Stream Analytics](stream-analytics-get-started.md) et [Guide de développement pour Azure Stream Analytics](../stream-analytics-developer-guide.md).

## Configuration des unités de diffusion en continu ##
Les unités de diffusion en continu représentent les ressources et la puissance pour exécuter un travail Azure Stream Analytics. Ces unités permettent de décrire la capacité relative de traitement des événements basée sur une mesure mixte du processeur, de la mémoire et des taux de lecture et d’écriture. Chaque unité de diffusion en continu correspond à un débit d'environ 1 Mo/s.

Le choix du nombre d’unités de diffusion en continu requises pour un travail particulier dépend de la configuration de la partition pour les entrées et de la requête définie pour le travail. Vous pouvez sélectionner des unités de diffusion en continu (jusqu’aux limites de votre quota) pour un travail à l’aide du portail Azure. Par défaut, chaque abonnement Azure peut avoir jusqu’à 50 unités de diffusion en continu pour tous les travaux Stream Analytics d’une région spécifique. Pour augmenter les unités de diffusion en continu de vos abonnements, contactez le [Support Microsoft](http://support.microsoft.com).

Le nombre d'unités de diffusion en continu qu'un travail peut utiliser dépend de la configuration de la partition pour les entrées et de la requête définie pour le travail. Notez également qu’une valeur valide pour les unités de diffusion en continu doit être utilisée. Les valeurs valides commencent à 1, 3, 6, puis vers le haut par incréments de 6, comme indiqué ci-dessous.

![Mise à l’échelle des unités de diffusion en continu Azure Stream Analytics][img.stream.analytics.streaming.units.scale]

Cet article vous montre comment calculer et régler la requête pour augmenter le débit des travaux Stream Analytics.

## Calcul du nombre maximum d'unités de diffusion en continu pour un travail ##
Le nombre total d'unités de diffusion en continu qui peut être utilisé par un travail Stream Analytics varie selon le nombre d'étapes de la requête définie pour le travail et le nombre de partitions pour chaque étape.

### Étapes dans une requête ###
Une requête peut avoir une ou plusieurs étapes. Chaque étape constitue une requête secondaire définie à l'aide du mot clé WITH. La seule requête qui se trouve en dehors du mot clé WITH est également comptabilisée comme une étape, par exemple, l'instruction SELECT de la requête suivante :

	WITH Step1 AS (
		SELECT COUNT(*) AS Count, TollBoothId
		FROM Input1 Partition By PartitionId
		GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
	)

	SELECT SUM(Count) AS Count, TollBoothId
	FROM Step1
	GROUP BY TumblingWindow(minute,3), TollBoothId

La requête précédente a deux étapes.

> [AZURE.NOTE]Cet exemple de requête est détaillé plus loin dans cet article.

### Partitionnement d'une étape ###

Les conditions suivantes doivent être respectées pour procéder au partitionnement d'une étape :

- La source d'entrée doit être partitionnée. Pour plus d'informations, consultez le [Guide de développement pour Azure Analytics](../stream-analytics-developer-guide.md) et le [Guide de programmation des concentrateurs d'événements](../azure-event-hubs-developer-guide.md).
- L'instruction SELECT de la requête doit lire à partir d'une source d'entrée partitionnée.
- La requête de l'étape doit contenir le mot clé **Partition By**

Lorsqu'une requête est partitionnée, les événements d'entrée sont traités et agrégées dans des groupes de partition distincts et les événements de sorties sont générés pour chacun des groupes. Si vous devez procéder à un agrégat combiné, vous devez créer une deuxième étape non partitionnée à agréger.

### Calcul des unités de diffusion en continu maximum pour un travail ###

Toutes les étapes non partitionnées ensemble peuvent être mises à l'échelle jusqu'à atteindre six unités de diffusion en continu par travail Stream Analytics. Pour ajouter d'autres unités de diffusion en continu, vous devez partitionner une étape. Chaque partition peut avoir six unités de diffusion en continu.

<table border="1">
<tr><th>Requête d'un travail</th><th>Nombre maximum d'unités de diffusion en continu pour le travail</th></td>

<tr><td>
<ul>
<li>La requête contient une étape.</li>
<li>L'étape n'est pas partitionnée.</li>
</ul>
</td>
<td>6</td></tr>

<tr><td>
<ul>
<li>Le flux de données d'entrée est partitionné par&#160;3.</li>
<li>La requête contient une étape.</li>
<li>L'étape est partitionnée.</li>
</ul>
</td>
<td>18</td></tr>

<tr><td>
<ul>
<li>La requête contient 2&#160;étapes.</li>
<li>Aucune des étapes n'est partitionnée.</li>
</ul>
</td>
<td>6</td></tr>



<tr><td>
<ul>
<li>Le flux de données d'entrée est partitionné par&#160;3.</li>
<li>La requête contient 2&#160;étapes. L'étape d'entrée est partitionnée et la deuxième étape ne l'est pas.</li>
<li>L'instruction SELECT lit à partir de l'entrée partitionnée.</li>
</ul>
</td>
<td>24&#160;(18&#160;pour les étapes partitionnées +&#160;6&#160;pour les étapes non partitionnées)</td></tr>
</table>

### Exemple de mise à l'échelle ###
La requête suivante calcule le nombre de voitures traversant une gare de péage avec trois péages dans un intervalle de temps de trois minutes. Cette requête peut être mise à l'échelle jusqu'à six unités de diffusion en continu.

	SELECT COUNT(*) AS Count, TollBoothId
	FROM Input1
	GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Pour utiliser plusieurs unités de diffusion en continu pour la requête, le flux de données d'entrée et la requête doivent être partitionnés. Étant donné que la partition de flux de données est définie sur 3, la requête modifiée suivante peut être mise à l'échelle jusqu'à compter 18 unités de diffusion en continu :

	SELECT COUNT(*) AS Count, TollBoothId
	FROM Input1 Partition By PartitionId
	GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Lorsqu'une requête est partitionnée, les événements d'entrée sont traités et agrégées dans des groupes de partition distincts. Les événements de sortie sont également générés pour chacun des groupes. Le partitionnement peut provoquer des résultats inattendus lorsque le champ **Group-by** n'est pas la clé de partition dans l'entrée du flux de données. Par exemple, le champ TollBoothId dans l'exemple de requête précédent n'est pas la clé de partition d'Input1. Les données du péage « TollBooth #1 » peuvent être réparties dans plusieurs partitions.

Chacune des partitions Input1 sera traitée séparément par Stream Analytics et plusieurs enregistrements du nombre de voitures qui traversent (« car-pass-through ») seront créés pour le même péage au cours du même intervalle de temps. Au cas où il serait impossible de modifier la clé de partition d'entrée, ce problème peut être résolu en ajoutant une étape non partitionnée supplémentaire, par exemple :

	WITH Step1 AS (
		SELECT COUNT(*) AS Count, TollBoothId
		FROM Input1 Partition By PartitionId
		GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
	)

	SELECT SUM(Count) AS Count, TollBoothId
	FROM Step1
	GROUP BY TumblingWindow(minute, 3), TollBoothId

Cette requête peut être mise à l'échelle jusqu'à comporter 24 unités de diffusion en continu.

>[AZURE.NOTE]Si vous joignez deux flux de données, assurez-vous qu’ils sont partitionnés par la clé de partition de la colonne de jointure, et que chaque flux a le même nombre de partitions.


## Configuration d'une partition de travail Stream Analytics ##

**Réglage de l'unité de diffusion en continu d'un travail**

1. Connectez-vous au [portail de gestion](https://manage.windowsazure.com).
2. Cliquez sur **Stream Analytics** sur la gauche.
3. Cliquez sur le travail Stream Analytics que vous souhaitez mettre à l'échelle.
4. Cliquez sur **METTRE À L'ÉCHELLE** en haut de la page.

![Mise à l’échelle des unités de diffusion en continu Azure Stream Analytics][img.stream.analytics.streaming.units.scale]

Dans le portail Azure en version préliminaire, les paramètres de mise à l'échelle sont accessibles sous Paramètres :

![Configuration d’une tâche Stream Analytics sur le portail Azure en version préliminaire][img.stream.analytics.preview.portal.settings.scale]

## Surveillance des performances du travail ##

À l'aide du portail de gestion, vous pouvez suivre le débit d'un travail dans Événements par seconde :

![Surveillance des travaux Azure Stream Analytics][img.stream.analytics.monitor.job]

Calculez le débit prévu pour la charge de travail en événements par seconde. Au cas où le débit est plus faible que prévu, réglez la partition d'entrée ainsi que la requête, puis ajoutez d'autres unités de diffusion en continu à votre travail.

## Débit ASA à l'échelle - Scénario Raspberry Pi ##


Pour comprendre comment ASA évolue dans un scénario classique en termes de débit de traitement sur plusieurs unités de diffusion en continu, voici une expérience qui envoie des données de capteur (clients) dans le concentrateur d'événements, ASA le traite et envoie une alerte ou des statistiques sous forme de sortie à un autre concentrateur d'événements.

Le client envoie des données de capteur synthétisées aux concentrateurs d'événements au format JSON vers ASA et la sortie des données est également au format JSON. Voici à quoi ressemblerait l'exemple de données :

    {"devicetime":"2014-12-11T02:24:56.8850110Z","hmdt":42.7,"temp":72.6,"prss":98187.75,"lght":0.38,"dspl":"R-PI Olivier's Office"}

Requête : « Envoyer une alerte lorsque la lumière est éteinte »

    SELECT AVG(lght),
	 “LightOff” as AlertText
	FROM input TIMESTAMP
	BY devicetime
	 WHERE
		lght< 0.05 GROUP BY TumblingWindow(second, 1)

Mesure de débit : dans ce contexte, le débit est la quantité de données d'entrée traitées par ASA au cours d'une durée fixe (10 minutes). Pour obtenir un meilleur débit de traitement pour les données d'entrée, le flux de données d'entrée et la requête doivent être partitionnés. De même, « COUNT() » est inclus dans la requête pour mesurer le nombre d'événements d'entrée traités. Pour vous assurer qu'ASA n'attend pas simplement les événements d'entrée, chaque partition du concentrateur d'événements d'entrée a été préchargée avec suffisamment de données d'entrée (environ 300 Mo).

Voici les résultats avec l'augmentation du nombre d'unités de diffusion en continu et le nombre de partitions correspondant dans les concentrateurs d'événements.

<table border="1">
<tr><th>Partitions d'entrée</th><th>Partitions de sortie</th><th>Unités de diffusion en continu</th><th>Débit soutenu
</th></td>

<tr><td>12</td>
<td>12</td>
<td>6</td>
<td>4,06 Mo/s</td>
</tr>

<tr><td>12</td>
<td>12</td>
<td>12</td>
<td>8,06&#160;Mo/s</td>
</tr>

<tr><td>48</td>
<td>48</td>
<td>48</td>
<td>38,32&#160;Mo/s</td>
</tr>

<tr><td>192</td>
<td>192</td>
<td>192</td>
<td>172,67&#160;Mo/s</td>
</tr>

<tr><td>480</td>
<td>480</td>
<td>480</td>
<td>454,27&#160;Mo/s</td>
</tr>

<tr><td>720</td>
<td>720</td>
<td>720</td>
<td>609,69&#160;Mo/s</td>
</tr>
</table>

![img.stream.analytics.perfgraph][img.stream.analytics.perfgraph]

## Obtenir de l'aide ##
Pour obtenir une assistance, consultez le [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/fr-FR/home?forum=AzureStreamAnalytics)


## Étapes suivantes ##

- [Présentation d'Azure Stream Analytics](stream-analytics-introduction.md)
- [Prise en main d'Azure Stream Analytics](stream-analytics-get-started.md)
- [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
- [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Références sur l'API REST de gestion d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)


<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings.png

<!--Link references-->

[microsoft.support]: http://support.microsoft.com
[azure.management.portal]: http://manage.windowsazure.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
 

<!---HONumber=Oct15_HO3-->