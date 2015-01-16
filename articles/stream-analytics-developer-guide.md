<properties title="Azure Stream Analytics developer guide" pageTitle="Guide de développement pour Azure Stream Analytics | Azure" description="Découvrez comment développer des applications Azure Stream Analytics." metaKeywords="" services="stream analytics" solutions="" documentationCenter="" authors="jgao" videoId="" scriptId="" manager="paulettm" editor="cgronlun"/>

<tags ms.service="stream-analytics" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="data-services" ms.date="10/28/2014" ms.author="jgao" />


# Guide de développement pour Azure Stream Analytics 

[Cette documentation préliminaire est susceptible d'être modifiée dans les futures versions.] 

Azure Stream Analytics est un service entièrement géré permettant de traiter des événements avec une latence faible, une haute disponibilité et de façon évolutive via des données de diffusion dans le cloud. Dans sa version préliminaire, Stream Analytics permet aux clients de configurer des travaux de diffusion en continu pour analyser des flux de données et de mener à bien des analyses en temps réel.  

Scénarios cibles pour Stream Analytics :

- Effectuer le traitement des événements complexes sur un volume de données élevé à haute vitesse   
- Collecter des données d'événements à partir de ressources ou d'équipements distribué(e)s globalement, comme par exemple, des voitures connectées ou un réseau public 
- Traiter des données de télémétrie pour effectuer une surveillance et des diagnostic en quasi-temps réel 
- Récupérer et archiver des événements en temps réel en vue d'un traitement ultérieur

Pour plus d'informations, consultez [Présentation d'Azure Stream Analytics][stream.analytics.introduction]. 

Les travaux Stream Analytics se composent d'une ou plusieurs entrées sources, d'une requête sur les données de flux entrantes et d'une cible de sortie.  


##Dans cet article

+ [Entrées](#inputs) 
+ [Query](#query)
+ [Sortie](#output)
+ [Mise à l'échelle des travaux](#scale)
+ [Travaux de surveillance et de résolution des problèmes](#monitor)
+ [Gestion des travaux](#manage)
+ [Étapes suivantes](#nextsteps)



##<a name="inputs"></a>Entrées

### Flux de données

Chaque définition de travail Stream Analytics doit contenir au moins une source d'entrée de flux qui sera traitée et transformée par le travail.  [Les services Stockage d'objets blob][azure.blob.storage] et [Concentrateurs d'événements Service Bus Azure][azure.event.hubs] sont pris en charge en tant que sources d'entrée de flux de données.  Les sources d'entrée de concentrateur d'événements permettent de collecter des flux d'événements à partir de plusieurs périphériques et services différents, tandis que le stockage d'objets blob peut servir de source d'entrée pour la réception de grandes quantités de données.  Étant donné que les objets blob ne diffusent pas de données, les travaux Stream Analytics rattachés aux objets blob ne dureront pas dans le temps, sauf si les enregistrements des objets blob contiennent des horodatages.

### Données de référence

Stream Analytics prend également en charge un deuxième type de source d'entrée : les données de référence.  Il s'agit de données auxiliaires permettant d'effectuer des corrélations et des recherches. Ces données sont généralement statiques ou rarement modifiées.  Dans la version préliminaire, seul le type de source d'entrée fourni par le stockage d'objets blob est pris en charge pour les données de référence.

### Sérialisation
Pour garantir un comportement correct des requêtes, Stream Analytics doit respecter le format de sérialisation utilisé sur le flux de données entrantes. Les formats actuellement pris en charge sont JSON, CSV et Avro pour les données de diffusion en continu et CSV pour les données de référence.

### Propriétés générées
Selon le type d'entrée utilisé dans le travail, des champs supplémentaires seront générés. Ils contiennent des métadonnées d'événements.  Ces champs peuvent être interrogées comme d'autres colonnes d'entrée.  Si le champ d'un événement porte le même nom que l'une des propriétés ci-dessous, il sera remplacé par les métadonnées d'entrée.

<table border="1">
	<tr>
		<th></th>
		<th>Propriété</th>
		<th>Description</th>
	</tr>
	<tr>
		<td rowspan="4" valign="top"><strong>Blob</strong></td>
		<td>BlobName</td>
		<td>Nom de l'objet blob d'entrée d'où provient l'événement</td>
	</tr>
	<tr>
		<td>EventProcessedUtcTime</td>
		<td>Date et heure du traitement de l'enregistrement de l'objet blob</td>
	</tr>
	<tr>
		<td>BlobLastModifiedUtcTime</td>
		<td>Date et heure de la dernière modification apportée à l'objet blob</td>
	</tr>
	<tr>
		<td>PartitionId</td>
		<td>ID de partition de base zéro de l'adaptateur d'entrée</td>
	</tr>
	<tr>
		<td rowspan="3" valign="top"><b>Concentrateur d'événements</b></td>
		<td>EventProcessedUtcTime</td>
		<td>Date et heure du traitement de l'événement</td>
	</tr>
	<tr>
		<td>EventEnqueuedUtcTime</td>
		<td>Date et heure de la réception de l'événement par le concentrateur d'événements</td>
	</tr>
	<tr>
		<td>PartitionId</td>
		<td>ID de partition de base zéro de l'adaptateur d'entrée</td>
	</tr>
</table>



###Ressources supplémentaires
Pour plus d'informations sur la création de sources d'entrée, consultez le [Guide de développement de Concentrateurs d'événements Azure][azure.event.hubs.developer.guide] et [Stockage blob Azure][azure.blob.storage.use].  



##<a name="query"></a>Query
La logique permettant de filtrer, de manipuler et de traiter des données entrantes est définie dans la requête des travaux Stream Analytics.  Les requêtes sont écrites dans un langage de requête propre à Stream Analytics. Il s'agit d'un langage de type SQL qui est principalement un sous-ensemble de la syntaxe T-SQL standard avec quelques extensions spécifiques aux requêtes temporelles.

###Fenêtrage
Les extensions de fenêtrage permettent d'effectuer des agrégations et des calculs sur des sous-ensembles d'événements inclus dans une période de temps définie. Les fonctions de fenêtrage sont appelées à l'aide de l'instruction GROUP BY. Par exemple, la requête suivante compte les événements reçus par seconde : 

	SELECT Count(*) 
	FROM Input1 
	GROUP BY TumblingWindow(second, 1) 

###Étapes d'exécution
Pour les requêtes plus complexes, la clause SQL standard WITH permet de spécifier un jeu de résultats temporaire nommé.  Par exemple, cette requête utilise WITH pour effectuer une transformation à deux étapes d'exécution :
 
	WITH step1 AS ( 
		SELECT Avg(Reading) as avr 
		FROM temperatureInput1 
		GROUP BY Building, TumblingWindow(hour, 1) 
	) 

	SELECT Avg(avr) AS campus_Avg 
	FROM step1 
	GROUP BY TumblingWindow (day, 1) 

Pour en savoir plus sur le langage des requêtes, consultez les [Références sur le langage des requêtes d'Azure Stream Analytics][stream.analytics.query.language.reference]. 

##<a name="output"></a>Sortie
La source de la sortie correspond à l'emplacement d'écriture des résultats du travail Stream Analytics. Résultats sont écrits en continu dans la source de sortie pendant que le travail traite les événements d'entrée.  Les sources de sortie suivantes sont prises en charge :

- Concentrateurs d'événements Service Bus Azure : sélectionnez le concentrateur d'événements comme source de sortie pour les scénarios où vous devez utiliser simultanément plusieurs pipelines de diffusion, comme lorsque des commandes sont renvoyées vers les périphériques.
- Objets blob d'Azure Storage : utilisez ces objets blob pour archiver la sortie sur le long terme ou pour stocker des données en vue d'un traitement ultérieur.
- Base de données SQL Azure : cette source de sortie est appropriée aux données de nature relationnelle ou pour les applications qui dépendent de contenus hébergés dans une base de données.


##<a name="scale"></a>Mise à l'échelle des travaux

Un travail Stream Analytics peut être mis à l'échelle via la configuration d'unités de diffusion, qui définissent la quantité de puissance de traitement allouée à un travail. Chaque unité de diffusion en continu correspond à un débit d'environ 1 Mo/s. Chaque abonnement dispose d'un quota de 12 unités de diffusion en continu par région à allouer aux travaux de cette région.

Pour plus d'informations, consultez [Mise à l'échelle des travaux Azure Stream Analytics][stream.analytics.scale.jobs].


##<a name="monitor"></a>Travaux de surveillance et de résolution des problèmes

###Compte de stockage de surveillance régionale

Pour activer la surveillance des travaux, vous devez désigner un compte Azure Storage à Stream Analytics pour surveiller les données de chaque région contenant des travaux Stream Analytics.  Ceci est configuré au moment de la création d'un travail.  

###Mesures
Les mesures suivantes sont disponibles pour surveiller l'utilisation et les performances des travaux Stream Analytics :

- Débit entrant : quantité de données reçues par le travail Stream Analytics, en termes de nombre d'événements
- Débit sortant : quantité de données envoyées par le travail Stream Analytics vers la source de sortie, en termes de nombre d'événements
- Nombre d'erreurs : nombre de messages d'erreur générés par un travail Stream Analytics

###Journaux des opérations
La meilleure approche pour déboguer un travail Stream Analytics ou résoudre ses problèmes consiste à utiliser le service Journaux des opérations Azure.  Ce service est accessible sous la section Services de gestion du portail.  Pour examiner les journaux de votre travail, définissez le champ Type de service sur " Stream Analytics " et le champ Nom du service sur le nom de votre travail.


##<a name="manage"></a>Gestion des travaux 

###Démarrage et arrêt des travaux
Dans la version préliminaire de Stream Analytics, l'arrêt d'un travail n'entraîne pas la conservation d'un état pour les derniers événements traités par ce travail.  Par conséquent, lorsqu'un travail arrêté redémarre, il est possible que des événements soient ignorés, ou des données dupliquées.  Si vous devez arrêter temporairement un travail, la meilleure solution consiste à inspecter la sortie et utiliser l'heure d'insertion du dernier enregistrement pour savoir à peu près quand le travail a été arrêté.  Puis il faut indiquer cette date/heure approximative dans paramètre Démarrer la sortie sous l'onglet Configurer lors du redémarrage du travail.
Il s'agit d'une limitation temporaire et sa résolution est une priorité élevée dans l'une des versions à venir.  

###Configuration des travaux
Voici les paramètres généraux d'un travail Stream Analytics que vous pouvez régler :

- Démarrer la sortie : permet d'indiquer le moment ou ce travail va commencer à produire des résultats de sortie. Si la requête associée est comprise dans un intervalle de temps, le travail commence à récupérer des données d'entrée à partir des sources d'entrée au début de cet intervalle, afin de produire le premier événement de sortie au moment défini. Il existe deux options : Heure de début du travail et Personnalisé. Le paramètre par défaut est Heure de début du travail. Pour l'option Personnalisé, vous devez indiquer une date et une heure. Ce paramètre est utile pour indiquer la quantité de données d'historique des sources d'entrée à traiter ou pour récupérer des données de traitement à un moment précis, comme par exemple, lors du dernier arrêt d'un travail. 
- Stratégie d'arrivée en désordre : ces paramètre permette de gérer des événements qui n'arrivent jusqu'au travail Stream Analytics les uns à la suite des autres. Vous pouvez désigner un délai pour réorganiser les événements en spécifiant un intervalle de temps de tolérance et en déterminant une action à effectuer pour récupérer les événements hors de cet intervalle : Annuler ou Régler.  " Annuler " supprime tous les événements reçus dans le désordre et " Régler " remplace la valeur System.Timestamp des événements reçus dans le désordre par l'horodatage du dernier événement reçu dans l'ordre.  
- Paramètres régionaux : utilisez ce paramètre pour indiquer les préférences internationales pour le travail Stream Analytics. Si les horodatages des données sont des paramètres régionaux neutre, ces paramètres-ci peuvent affecter l'analyser, la comparaison et les données de tri du travail. Pour la version préliminaire, seul le paramètre en-US est pris en charge.


##<a name="support"></a>Obtenir de l'aide
Pour obtenir de l'aide supplémentaire, consultez le [Forum Azure Stream Analytics][stream.analytics.forum]. 


##<a name="nextsteps"></a>Étapes suivantes

- [Présentation d'Azure Stream Analytics][stream.analytics.introduction]
- [Prise en main d'Azure Stream Analytics][stream.analytics.get.started]
- [Mise à l'échelle des travaux Azure Stream Analytics][stream.analytics.scale.jobs]
- [Limitations et problèmes connus d'Azure Stream Analytics][stream.analytics.limitations]
- [Références sur le langage des requêtes d'Azure Stream Analytics][stream.analytics.query.language.reference]
- [Références sur l'API REST de gestion d'Azure Stream Analytics][stream.analytics.rest.api.reference]



<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: http://azure.microsoft.com/fr-fr/documentation/services/storage/
[azure.blob.storage.use]: http://azure.microsoft.com/fr-fr/documentation/articles/storage-dotnet-how-to-use-blobs/

[azure.event.hubs]: http://azure.microsoft.com/fr-fr/services/event-hubs/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/fr-fr/library/azure/dn789972.aspx

[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.forum]: http://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.get.started]: ../stream-analytics-get-started/
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.scale.jobs]: ../stream-analytics-scale-jobs/
[stream.analytics.limitations]: ../stream-analytics-limitations/
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
