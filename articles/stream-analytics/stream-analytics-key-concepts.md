<properties 
	pageTitle="En savoir plus sur les concepts clés de Stream Analytics | Microsoft Azure" 
	description="Découvrez les principaux concepts de Stream Analytics : composants d’une tâche Stream Analytics, notamment les entrées et sorties prises en charge, la configuration de la tâche et les mesures associées." 
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="07/01/2015" 
	ms.author="jeffstok" />


# Concepts fondamentaux de Stream Analytics : présentation des principes de base d’une tâche Stream Analytics 

Microsoft Azure Stream Analytics est un service entièrement géré permettant de traiter des événements avec une latence faible, une haute disponibilité et de façon évolutive via un flux de données dans le cloud. Stream Analytics permet aux clients de configurer des tâches de diffusion en continu pour analyser des flux de données et de mener à bien des analyses en quasi-temps réel. Cet article présente les concepts clés d’un travail Stream Analytics.

## Quelles actions peuvent être exécutées dans Stream Analytics ?
Avec Stream Analytics, vous pouvez :

- Effectuer un traitement complexe des événements sur des flux de données volumineux et à haut débit.   
- Collecter des données d’événements à partir de ressources ou d'équipements distribués, comme des voitures connectées ou des réseaux publics. 
- Traiter des données de télémétrie pour effectuer une surveillance et un diagnostic en quasi-temps réel. 
- Récupérer et archiver des événements en temps réel en vue d'un traitement ultérieur

Pour plus d’informations, consultez [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md).

Un travail Stream Analytics comprend l’ensemble des éléments suivants : - Une ou plusieurs sources d’entrée - Une requête sur un flux de données entrantes - Une cible de sortie.


## Entrées

### Flux de données

Chaque définition de travail Stream Analytics doit contenir au moins une source d'entrée de flux qui sera traitée et transformée par le travail. Le [stockage Azure Blob](http://azure.microsoft.com/documentation/services/storage/) et [Azure Event Hubs](http://azure.microsoft.com/services/event-hubs/) sont pris en charge en tant que sources d’entrées de flux de données. Les sources d'entrée de concentrateurs d'événements permettent de collecter des flux d'événements à partir de plusieurs périphériques et services différents, tandis que le stockage d'objets blob peut servir de source d'entrée pour la réception de grandes quantités de données. Étant donné que les objets blob ne diffusent pas de données, les travaux Stream Analytics rattachés aux objets blob ne dureront pas dans le temps, sauf si les enregistrements des objets blob contiennent des horodatages.

### Données de référence
Stream Analytics prend également en charge un deuxième type de source d'entrée : les données de référence. Il s'agit de données auxiliaires permettant d'effectuer des corrélations et des recherches. Ces données sont généralement statiques ou rarement modifiées. Le [stockage d'objets blob Azure](http://azure.microsoft.com/documentation/services/storage/) est la seule source d'entrée prise en charge pour les données de référence. Les objets blob de source de données de référence sont limités à une taille de 50 Mo.

Pour prendre en charge l'actualisation des données de référence, l'utilisateur doit spécifier une liste d'objets blob dans la configuration d'entrée à l'aide des jetons {date} et {heure} dans le modèle de chemin d'accès. Le travail charge l'objet blob correspondant en fonction de la date et de l'heure codées dans les noms d'objets blob selon le fuseau horaire UTC.

Par exemple, si le travail a une entrée de référence configurée dans le portail avec le modèle de chemin d'accès suivant : /sample/{date}/{heure}/products.csv où le format de date est « AAAA-MM-JJ » et le format de l'heure est « HH:mm », alors le travail récupère un fichier nommé /sample/2015-04-16/17:30/products.csv à 17h30 le 16 avril 2015 (UTC) (ce qui équivaut à 10h30 le 16 avril 2015 dans le fuseau horaire PST).


### Sérialisation
Pour garantir un comportement correct des requêtes, Stream Analytics doit respecter le format de sérialisation utilisé sur le flux de données entrantes. Les formats de sérialisation actuellement pris en charge sont JSON, CSV et Avro pour les flux de données, et CSV ou JSON pour les données de référence.

### Propriétés générées
Selon le type d'entrée utilisé dans le travail, des champs supplémentaires seront générés. Ils contiennent des métadonnées d'événements. Ces champs peuvent être interrogées comme d'autres colonnes d'entrée. Si le champ d'un événement porte le même nom que l'une des propriétés ci-dessous, il sera remplacé par les métadonnées d'entrée.

<table border="1">
	<tr>
		<th></th>
		<th>Propriété</th>
		<th>Description</th>
	</tr>
	<tr>
		<td rowspan="4" valign="top"><strong>Objet blob</strong></td>
		<td>BlobName</td>
		<td>Nom de l'objet blob d'entrée d'où provient l'événement.</td>
	</tr>
	<tr>
		<td>EventProcessedUtcTime</td>
		<td>Date et heure du traitement de l'enregistrement de l'objet blob.</td>
	</tr>
	<tr>
		<td>BlobLastModifiedUtcTime</td>
		<td>Date et heure de la dernière modification apportée à l'objet blob.</td>
	</tr>
	<tr>
		<td>PartitionId</td>
		<td>ID de partition de base zéro de l'adaptateur d'entrée.</td>
	</tr>
	<tr>
		<td rowspan="3" valign="top"><b>Concentrateur d'événements</b></td>
		<td>EventProcessedUtcTime</td>
		<td>Date et heure du traitement de l'événement.</td>
	</tr>
	<tr>
		<td>EventEnqueuedUtcTime</td>
		<td>Date et heure de la réception de l'événement par le concentrateur d'événements.</td>
	</tr>
	<tr>
		<td>PartitionId</td>
		<td>ID de partition de base zéro de l'adaptateur d'entrée.</td>
	</tr>
</table>

###Partitions avec des données d'entrée lentes ou inexistantes
En cas de lecture à partir de sources d'entrée qui ont plusieurs partitions et quand une ou plusieurs partitions sont à la traîne ou ne comportent pas de données, le travail de diffusion en continu doit déterminer comment gérer cette situation pour que les événements continuent de circuler dans le système. Le paramètre d'entrée « Retard d'arrivée maximal autorisé » contrôle ce comportement. Par défaut, il est défini de sorte à attendre les données indéfiniment, ce qui signifie que les horodatages des événements ne sont pas modifiés, mais également que les événements circulent en fonction de la partition d'entrée la plus lente. Ils cessent de circuler si une ou plusieurs partitions d'entrée ne comportent pas de données. Cette définition s'avère utile si les données sont réparties uniformément sur les partitions d'entrée et que la cohérence temporelle entre les événements est essentielle.

Vous pouvez également décider de n'attendre que pendant une période limitée. Le paramètre « Retard d'arrivée maximal autorisé » détermine le délai à l’issue duquel le travail décide d'avancer, en laissant les partitions d'entrée à la traîne et en agissant sur les événements en fonction du paramètre « Action pour les événements en retard », c'est-à-dire en les annulant ou en réglant leurs horodatages si des données arrivent plus tard. Cette définition s'avère utile si la latence est essentielle et le décalage d'horodatage toléré, mais les entrées risquent de ne pas être distribuées uniformément.

###Partitions avec des événements en désordre
Quand la requête du travail de diffusion en continu utilise le mot clé TIMESTAMP BY, il n'y a aucune garantie quant à l'ordre dans lequel les événements arrivent à l'entrée. Certains événements dans la même partition d'entrée peuvent être à la traîne, le paramètre « Désordre maximal autorisé au sein d'une entrée » entraîne le travail de diffusion en continu à agir sur les événements qui sont trouvent en dehors de la tolérance de l'ordre, en fonction du paramètre « Action pour les événements en retard », en les annulant ou en réglant leurs horodatages.

### Ressources supplémentaires
Pour plus d'informations sur la création de sources d'entrée, consultez le [Guide de développement de concentrateurs d'événements Azure](http://msdn.microsoft.com/library/azure/dn789972.aspx) et [Utilisation du stockage d'objets Blob Azure](../storage/storage-dotnet-how-to-use-blobs.md).



## Interroger
La logique permettant de filtrer, manipuler et traiter les données entrantes est définie la requête des travaux Stream Analytics. Les requêtes sont écrites dans un langage de requête propre à Stream Analytics. Il s'agit d'un langage de type SQL qui est principalement un sous-ensemble de la syntaxe Transact-SQL standard avec quelques extensions spécifiques aux requêtes temporelles.

### Fenêtrage
Les extensions de fenêtrage permettent d'effectuer des agrégations et des calculs sur des sous-ensembles d'événements inclus dans une période de temps définie. Les fonctions de fenêtrage sont appelées via l'instruction **GROUP BY**. Par exemple, la requête suivante compte les événements reçus par seconde :

	SELECT Count(*) 
	FROM Input1 
	GROUP BY TumblingWindow(second, 1) 

### Étapes d'exécution
Pour les requêtes plus complexes, la clause SQL standard **WITH** permet de spécifier un jeu de résultats temporaire nommé. Par exemple, cette requête utilise **WITH** pour effectuer une transformation à deux étapes d'exécution :
 
	WITH step1 AS ( 
		SELECT Avg(Reading) as avr 
		FROM temperatureInput1 
		GROUP BY Building, TumblingWindow(hour, 1) 
	) 

	SELECT Avg(avr) AS campus_Avg 
	FROM step1 
	GROUP BY TumblingWindow (day, 1) 

Pour en savoir plus sur le langage des requêtes, consultez les [Références sur le langage des requêtes d'Azure Stream Analytics](http://go.microsoft.com/fwlink/?LinkID=513299).

## Sortie
La cible de sortie correspond à l'emplacement d'écriture des résultats du travail Stream Analytics. Les résultats sont écrits en continu dans la cible de sortie pendant que le travail traite les événements d'entrée. Les cibles de sortie suivantes sont prises en charge :

- Concentrateurs d'événements Azure : sélectionnez le concentrateur d'événements comme cible de sortie pour les scénarios où vous devez utiliser simultanément plusieurs pipelines de diffusion, comme lorsque des commandes sont renvoyées vers les périphériques.
- Stockage d'objets blob : utilisez ces objets blob pour archiver la sortie sur le long terme ou pour stocker des données en vue d'un traitement ultérieur.
- Stockage sur les tables Azure : il s'agit d'un magasin de données structurées avec moins de contraintes sur le schéma. Les entités avec un schéma et des types différents peuvent être stockées dans la même table Azure. Le stockage des données sur les tables Azure permet d’assurer leur persistance et une récupération efficace. Pour plus d'informations, consultez [Introduction à Azure Storage](../storage/storage-introduction.md) et [Conception d'une stratégie de partition extensible pour le stockage de tables Azure](https://msdn.microsoft.com/library/azure/hh508997.aspx).
- Base de données SQL Azure : cette cible de sortie convient aux données de nature relationnelle ou aux applications qui dépendent de contenus hébergés dans une base de données.

## Unités de diffusion en continu ##
Pour fournir des performances plus prévisibles aux clients, Azure Stream Analytics utilise des unités de diffusion en continu pour représenter les ressources et la puissance afin d’exécuter un travail. Ces unités permettent de décrire la capacité relative de traitement des événements basée sur une mesure mixte du processeur, de la mémoire et des taux de lecture et d’écriture. Chaque unité de diffusion en continu correspond à un débit d'environ 1 Mo/s. Chaque travail Azure Stream Analytics doit avoir au moins une unité de diffusion en continu, qui correspond à la valeur par défaut de tous les travaux. Pour en savoir plus sur la sélection du nombre approprié d’unités de diffusion en continu pour un travail, consultez [Mise à l’échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md).

## Mise à l'échelle des travaux

La métrique Utilisation de % d’unités de diffusion en continu définie ci-dessous, est un indicateur de la nécessité de mise à l’échelle d’un travail Azure Stream Analytics. Une utilisation élevée de % d’unités de diffusion peut provenir d’une grande fenêtre dans une requête, d’événements volumineux dans la plage de tolérance pour les événements d’entrée en désordre ou d’une combinaison des éléments ci-dessus. Le partitionnement de la requête, ou son fractionnement en plusieurs étapes et l’ajout d’unités de diffusion en continu à partir de l’onglet Échelle sont les deux stratégies visant à éviter cette condition.

Vous pouvez observer une utilisation des ressources de référence même sans événements d’entrée, car le système consomme une certaine quantité de ressources. La quantité des ressources consommées par le système peut également varier au fil du temps.

Pour plus d'informations, consultez [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md).


## Travaux de surveillance et de résolution des problèmes

### Compte de stockage de surveillance régional

Pour activer la surveillance des travaux, vous devez désigner un compte Azure Storage à Stream Analytics pour surveiller les données de chaque région contenant des travaux Stream Analytics. Ceci est configuré au moment de la création d'un travail.

### Mesures
Les mesures suivantes sont disponibles pour surveiller l'utilisation et les performances des travaux Stream Analytics :

- Utilisation de % d’unités de diffusion en continu : indicateur de la capacité relative de traitement des événements pour une ou plusieurs des étapes de la requête. Si cet indicateur atteint 80 % ou plus, il est fort probable que le traitement des événements soit retardé ou arrêté.
- Erreurs : nombre de messages d'erreur générés par un travail Stream Analytics.
- Événements d'entrée : quantité de données reçues par le travail Stream Analytics, en termes de nombre d'événements.
- Événements de sortie : quantité de données envoyées par le travail Stream Analytics à la cible de sortie, en termes de nombre d'événements.
- Événements non ordonnés : nombre d'événements reçus dans le désordre qui ont été supprimés ou dont l'horodatage a été réglé, en fonction de la stratégie d'arrivée en désordre.
- Erreurs de conversion de données : nombre d'erreurs de conversion de données générées par un travail Stream Analytics.

### Journaux des opérations
La meilleure approche pour déboguer un travail Stream Analytics ou résoudre ses problèmes consiste à utiliser les journaux des opérations Azure. Ces journaux sont accessibles dans la section **Services de gestion** du portail. Pour examiner les journaux de votre travail, définissez le champ **Type de service** sur **Stream Analytics** et le champ **Nom du service** sur le nom de votre travail.


## Gestion des travaux 

### Démarrage et arrêt des travaux
Quand vous démarrez un travail, vous êtes invité à spécifier une valeur **Démarrer la sortie**, qui détermine quand ce travail commence à produire la sortie obtenue. Si la requête associée est comprise dans un intervalle de temps, le travail commence à récupérer des données d'entrée à partir des sources d'entrée au début de cet intervalle, afin de produire le premier événement de sortie au moment défini. Il existe trois options : **Heure de début du travail**, **Personnalisé** et **Heure du dernier arrêt**. Le paramètre par défaut est **Heure de début du travail**. Quand un travail est temporairement arrêté, il est préférable de choisir **Heure du dernier arrêt** pour la valeur **Démarrer la sortie** afin de reprendre le travail à l'heure de la dernière sortie et d'éviter de perdre des données. Pour l'option **Personnalisé**, vous devez indiquer une date et une heure. Ce paramètre est utile pour indiquer la quantité de données d'historique des sources d'entrée à traiter ou pour récupérer des données de traitement à un moment précis.

### Configuration des travaux
Voici les paramètres généraux d'un travail Stream Analytics que vous pouvez régler :

- **Démarrer la sortie** : utilisez ce paramètre pour spécifier quand ce travail commence à produire la sortie obtenue. Si la requête associée est comprise dans un intervalle de temps, le travail commence à récupérer des données d'entrée à partir des sources d'entrée au début de cet intervalle, afin de produire le premier événement de sortie au moment défini. Il existe deux options : **Heure de début du travail** et **Personnalisé**. Le paramètre par défaut est **Heure de début du travail**. Pour l'option **Personnalisé**, vous devez indiquer une date et une heure. Ce paramètre est utile pour indiquer la quantité de données d'historique des sources d'entrée à traiter ou pour récupérer des données de traitement à un moment précis, comme par exemple, lors du dernier arrêt d'un travail. 
- **Stratégie d'arrivée en désordre** : paramètres permettant de gérer des événements qui n'arrivent pas jusqu'au travail Stream Analytics les uns à la suite des autres. Vous pouvez désigner un seuil pour réorganiser les événements en spécifiant une fenêtre de tolérance et en déterminant une action à effectuer sur les événements en dehors de cette fenêtre : **Annuler** ou **Régler**. **Annuler** supprime l’ensemble des événements reçus en désordre, tandis que **Régler** modifie le système. Horodatage des événements en désordre suivant l’horodatage de l’événement le plus récent reçu dans l’ordre. 
- **Stratégie d'arrivée en désordre** : en cas de lecture à partir de sources d'entrée qui ont plusieurs partitions et quand une ou plusieurs partitions sont à la traîne ou ne comportent pas de données, le travail de diffusion en continu doit déterminer comment gérer cette situation pour que les événements continuent de circuler dans le système. Le paramètre d'entrée « Retard d'arrivée maximal autorisé » contrôle ce comportement. Par défaut, il est défini de sorte à attendre les données indéfiniment, ce qui signifie que les horodatages des événements ne sont pas modifiés, mais également que les événements circulent en fonction de la partition d'entrée la plus lente. Ils cessent de circuler si une ou plusieurs partitions d'entrée ne comportent pas de données. Cette définition s'avère utile si les données sont réparties uniformément sur les partitions d'entrée et que la cohérence temporelle entre les événements est essentielle. L'utilisateur peut également décider d'attendre uniquement pendant une période limitée. Le paramètre « Retard d'arrivée maximal autorisé » détermine le délai après lequel le travail décide d'avancer, en laissant les partitions d'entrée à la traîne et en agissant sur les événements en fonction du paramètre « Action pour les événements en retard », c'est-à-dire en les annulant ou en réglant leurs horodatages si des données arrivent plus tard. Cette définition s'avère utile si la latence est essentielle et le décalage d'horodatage toléré, mais les entrées risquent de ne pas être distribuées uniformément.
- **Paramètres régionaux** : utilisez ce paramètre pour indiquer les préférences internationales pour le travail Stream Analytics. Si les horodatages des données sont des paramètres régionaux neutre, ces paramètres-ci peuvent affecter l'analyser, la comparaison et les données de tri du travail. Pour la version préliminaire, seul le paramètre **fr-fr** est pris en charge.

### État

L'état des travaux Stream Analytics peut être examiné dans le portail Azure. Les travaux en cours d’exécution peuvent être dans l’un de ces deux états : **En cours d’exécution** ou **Détérioré**. La définition de chacun de ces états est indiquée ci-dessous :

- **En cours d’exécution** : le travail est alloué, l’entrée est en cours de traitement ou en attente. Si le travail est bloqué à l’état En cours d’exécution sans produire de résultat, soit la fenêtre temporelle de traitement de données est grande, soit la logique de la requête est compliquée. Une autre raison peut être qu’il n’y a pas de données envoyées actuellement au travail.
- **Détérioré** : cet état indique qu'un travail Stream Analytics rencontre une des erreurs suivantes : erreurs de communication entrée/sortie, erreurs de requête ou erreurs d'exécution pouvant être retentée. Pour identifier le type d'erreur que rencontre le travail, consultez les journaux des opérations.


## Obtenir de l'aide
Pour obtenir une assistance, consultez le [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/fr-fr/home?forum=AzureStreamAnalytics)


## Étapes suivantes

Maintenant que vous connaissez les concepts clés de Stream Analytics, consultez :

- [Présentation d'Azure Stream Analytics](stream-analytics-introduction.md)
- [Prise en main d'Azure Stream Analytics](stream-analytics-get-started.md)
- [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
- [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Références sur l'API REST de gestion d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 

<!---HONumber=July15_HO4-->