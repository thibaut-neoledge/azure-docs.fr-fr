<properties title="Scale Azure Stream Analytics jobs" pageTitle="Mise à l'échelle des travaux Azure Stream Analytics | Azure" description="Découvrez comment mettre à l'échelle les tâches Stream Analytics" metaKeywords="" services="stream-analytics" solutions="" documentationCenter="" authors="jgao" videoId="" scriptId="" manager="paulettm" editor="cgronlun"/>

<tags ms.service="stream-analytics" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="data-services" ms.date="10/28/2014" ms.author="jgao" />

# Mise à l'échelle des travaux Azure Stream Analytics 

Découvrez comment calculer des *unités de diffusion en continu* pour un travail Stream Analytics et comment mettre à l'échelle des travaux Stream Analytics en configurant des partitions d'entrée, en réglant la définition de requête et en configurant les unités de diffusion en continue d'un travail.

La définition d'un travail Stream Analytics se compose d'entrées, de requêtes et d'une sortie. Les entrées correspondent à l'emplacement à partir duquel le travail lit les données de flux, la sortie correspond à l'emplacement où le travail envoie ses résultats et la requête permet de transformer le flux d'entrée.  Un travail nécessite au moins une source d'entrée de flux de données. La source d'entrée de flux de données peut être un concentrateur d'événements Service Bus Azure ou un objet blob de stockage Azure. Pour plus d'informations, consultez [Présentation d'Azure Stream Analytics][stream.analytics.introduction], [Prise en main d'Azure Stream Analytics][stream.analytics.get.started] et [Guide de développement pour Azure Stream Analytics][stream.analytics.developer.guide]. 

Les ressources disponibles pour le traitement des travaux Stream Analytics sont mesurées par une unité de diffusion en continu. Chaque unité de diffusion en continu peut fournir un débit d'environ 1 Mo/s. Chaque travail doit avoir au moins une unité de diffusion en continu, qui correspond à la valeur par défaut pour tous les travaux. Vous pouvez définir jusqu'à 12 unités de diffusion en continu pour travail Stream Analytics à l'aide du portail de gestion Azure. Chaque abonnement Azure peut avoir jusqu'à 12 unités de diffusion en continu pour tous les travaux d'une région spécifique. Pour augmenter les unités de diffusion en continu de votre abonnement jusqu'à 100 unités, contactez le [Support Microsoft][microsoft.support].

Le nombre d'unités de diffusion en continu qu'un travail peut utiliser dépend de la configuration de partition des entrées, et la requête définie pour le travail. Cet article va vous montrer comment calculer et régler la requête pour augmenter le débit.

##Dans cet article
+ [Calcul du nombre maximum d'unités de diffusion en continu pour un travail](#calculate)
+ [Configuration d'une partition de travail Stream Analytics](#configure)
+ [Surveillance des performances d'un travail Stream Analytics](#monitor)
+ [Étapes suivantes](#nextstep)


##<a name="calculate"></a>Calcul du nombre maximum d'unités de diffusion en continu pour un travail
Le nombre total d'unités de diffusion en continu qui peut être utilisé par un travail Stream Analytics varie selon le nombre d'étapes de la requête définie pour le travail et le nombre de partitions pour chaque étape.

### Étapes d'une requête
Une requête peut avoir une ou plusieurs étapes. Chaque étape constitue une requête secondaire définie à l'aide du mot clé WITH. Si une requête se trouve en dehors du mot clé WITH, elle est également comptabilisée comme une étape. Par exemple, l'instruction SELECT dans la requête suivante :

	WITH Step1 (
		SELECT COUNT(*) AS Count, TollBoothId
		FROM Input1 Partition By PartitionId 
		GROUP BY TumblingWindow(minute, 3), TollBoothId
	) 

	SELECT SUM(Count) AS Count, TollBoothId
	FROM Step1 
	GROUP BY TumblingWindow(minute,3), TollBoothId

La requête précédente a 2 étapes. 

> [WACOM.NOTE] Cet exemple de requête est détaillé plus loin dans cet article.

### Partitionnement d'une étape

Les conditions suivantes doivent être respectées pour procéder au partitionnement d'une étape :

- La source d'entrée doit être partitionnée. Consultez le [Guide de développement pour Azure Stream Analytics][stream.analytics.developer.guide] et le [Guide de développement de Concentrateurs d'événements Azure][azure.event.hubs.developer.guide].
- L'instruction SELECT de la requête doit lire à partir d'une source d'entrée partitionnée. 
- La requête de l'étape doit avoir le mot clé Partition By 

Lorsqu'une requête est partitionnée, les événements d'entrée sont traités et agrégées dans des groupes de partition distincts et les événements de sorties sont générés pour chacun des groupes. Si vous devez procéder à un agrégat combiné, vous devez créer une deuxième étape non partitionnée à agréger.

La version préliminaire d'Azure Stream Analytics ne prend pas en charge le partitionnement par noms de colonnes. Vous pouvez uniquement partitionner par champ PartitionId. Ce type de champ est intégré dans votre requête. Il indique la partition d'origine du flux de données source.  Pour plus d'informations, consultez [Limitations et problèmes connus d'Azure Stream Analytics][stream.analytics.limitations].

### Calcul des unités de diffusion en continu maximum pour un travail

Toutes les étapes non partitionnées ensemble peuvent être mises à l'échelle jusqu'à atteindre 6 unités de diffusion en continu par travail Stream Analytics. Pour ajouter d'autres unités de diffusion en continu, vous devez partitionner une étape. Chaque partition peut avoir 6 unités de diffusion en continu.

<table border="1">
<tr><th>Requête d'un travail</th><th>Nombre maximum d'unités de diffusion continue pour ce travail</th></td>

<tr><td>
<ul>
<li>La requête contient une étape.</li>
<li>L'étape n'est pas partitionnée.</li>
</ul>
</td>
<td>6</td></tr>

<tr><td>
<ul>
<li>Le flux de données d'entrée est partitionné par 3.</li>
<li>La requête contient une étape.</li>
<li>L'étape est partitionnée.</li>
</ul>
</td>
<td>18</td></tr>

<tr><td>
<ul>
<li>La requête contient 2 étapes.</li>
<li>Aucune des étapes n'est partitionnée.</li>
</ul>
</td>
<td>6</td></tr>



<tr><td>
<ul>
<li>Le flux de données d'entrée est partitionné par 3.</li>
<li>La requête contient 2 étapes.</li>
<li>L'instruction SELECT lit à partir de l'entrée partitionnée.</li>
</ul>
</td>
<td>24 (18 par étape partitionnée + 6 par étape non partitionnée)</td></tr>
</table>

###Exemple de mise à l'échelle
La requête suivante calcule le nombre de voitures traversant une gare de péage avec 3 péages dans un intervalle de temps de 3 minutes. Cette requête peut être mise à l'échelle jusqu'à 6 unités de diffusion en continu.

	SELECT COUNT(*) AS Count, TollBoothId
	FROM Input1 
	GROUP BY TumblingWindow(minute, 3), TollBoothId

Pour utiliser plusieurs unités de diffusion en continu pour la requête, le flux de données d'entrée et la requête doivent être partitionnés. Étant donné que la partition de flux de données est définie sur 3, la requête modifiée suivante peut être mise à l'échelle jusqu'à compter 18 unités de diffusion en continu.

	SELECT COUNT(*) AS Count, TollBoothId
	FROM Input1 Partition By PartitionId
	GROUP BY TumblingWindow(minute, 3), TollBoothId

Lorsqu'une requête est partitionnée, les événements d'entrée sont traités et agrégées dans des groupes de partition distincts et les événements de sorties sont générés pour chacun des groupes. Le partitionnement peut provoquer des résultats inattendus lorsque le champ group-by n'est pas la clé de partition dans l'entrée du flux de données. Par exemple, le champ TollBoothId dans l'exemple de requête précédent n'est pas la clé de partition d'Input1. Les données du péage " tollbooth #1 " peuvent être réparties dans plusieurs partitions. Chacune des partitions d'Input1 sera traitée séparément par Stream Analytics et plusieurs enregistrements du nombre " car-pass-through " (nombre de voitures qui traversent) seront créés pour le même péage au cours du même intervalle de temps. Au cas où il serait impossible de modifier la clé de partition d'entrée, ce problème peut être résolu en ajoutant une étape non partitionnée supplémentaire. Par exemple :

	WITH Step1 (
		SELECT COUNT(*) AS Count, TollBoothId
		FROM Input1 Partition By PartitionId
		GROUP BY TumblingWindow(minute, 3), TollBoothId
	) 

	SELECT SUM(Count) AS Count, TollBoothId
	FROM Step1 
	GROUP BY TumblingWindow(minute, 3), TollBoothId

Cette requête peut être mise à l'échelle jusqu'à comporter 24 unités de diffusion en continu. 

>[WACOM.NOTE] Si vous joignez deux flux de données, assurez-vous que les flux de données sont partitionnées par la clé de partition de la colonne de jointure, et que chaque flux a le même nombre de partitions.


##<a name="configure"></a>Configuration d'une partition de travail Stream Analytics

**Réglage de l'unité de diffusion en continu d'un travail**

1. Connectez-vous au [portail de gestion][azure.management.portal].
2. Sur la gauche, cliquez sur **Stream Analytics**.
3. Cliquez sur le travail Stream Analytics que vous souhaitez mettre à l'échelle.
4. En haut de la page, cliquez sur **METTRE ﻿﻿À L'ÉCHELLE**.

![Azure Stream Analytics configure job scale][img.stream.analytics.configure.scale]


##<a name="monitor"></a>Surveillance des performances du travail

À l'aide du portail de gestion, vous pouvez suivre le débit d'un travail dans Événements par seconde :

![Azure Stream Analytics monitor jobs][img.stream.analytics.monitor.job]
 
Calculez le débit prévu pour la charge de travail en événements par seconde. Au cas où le débit est plus faible que prévu, réglez la partition d'entrée ainsi que la requête, puis ajoutez d'autres unités de diffusion en continu à votre travail.

##<a name="nextstep"></a> Étapes suivantes
Dans cet article, vous avez appris comment calculer le nombre d'unités de diffusion en continu et comment mettre à l'échelle un travail Stream Analytics. Pour en savoir plus sur Stream Analytics, consultez :

- [Présentation d'Azure Stream Analytics][stream.analytics.introduction]
- [Prise en main d'Azure Stream Analytics][stream.analytics.get.started]
- [Guide de développement pour Azure Stream Analytics][stream.analytics.developer.guide]
- [Limitations et problèmes connus d'Azure Stream Analytics][stream.analytics.limitations]
- [Références sur le langage des requêtes d'Azure Stream Analytics][stream.analytics.query.language.reference]
- [Références sur l'API REST de gestion d'Azure Stream Analytics][stream.analytics.rest.api.reference]

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png

<!--Link references-->

[microsoft.support]: http://support.microsoft.com
[azure.management.portal]: http://manage.windowsazure.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/fr-fr/library/azure/dn789972.aspx

[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.limitations]: ../stream-analytics-limitations/
[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.get.started]: ../stream-analytics-get-started/
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301


