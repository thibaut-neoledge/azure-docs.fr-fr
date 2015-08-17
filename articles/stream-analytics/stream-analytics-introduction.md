<properties 
	pageTitle="Introduction à Stream Analytics, l’analyse de données de diffusion en continu | Microsoft Azure" 
	description="Découvrez Azure Stream Analytics, un service cloud entièrement géré qui vous permet d’analyser les données de diffusion en continu à partir de l’Internet des objets (IoT) en temps réel." 
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
	ms.date="08/04/2015" 
	ms.author="jeffstok"/>


# Introduction à Azure Stream Analytics, un service cloud entièrement géré pour l’analyse des données de diffusion en continu

Azure Stream Analytics est un service entièrement géré permettant de traiter des événements avec une latence faible, une haute disponibilité et de façon évolutive via des données de diffusion dans le cloud. Il s’agit d’un service d’analyse de données pour l’Internet des objets (IoT) qui permet aux développeurs de combiner des flux de données avec des enregistrements d’historiques ou des données de référence pour dégager des perspectives métier rapidement, en toute simplicité.


## Présentation d'Azure Stream Analytics (vidéo)

Vidéo : Santosh Balasubramanian et Scott Hanselman présentent Azure Stream Analytics. (Durée : 11:22)

> [AZURE.VIDEO introduction-to-azure-stream-analytics-with-santosh-balasubramanian]

## Présentation et objectif

De nos jours, d'importantes quantités de données transitent à haute vitesse sur les réseaux câblés. Les organisations qui peuvent traiter et agir sur ces données de diffusion en continu en temps réel peuvent considérablement améliorer leur efficacité et se démarquer de leurs concurrents. Les scénarios d’analyse de diffusion en continu en temps réel concernent tous les secteurs : l’analyse et les alertes personnalisées relatives aux transactions d’actions en temps réel proposées par des sociétés de services financiers ; la détection des fraudes en temps réel ; les services de protection des données et des identités ; la réception et l’analyse fiables des données générées par les capteurs et mécanismes de positionnement incorporés dans des objets physiques (Internet des objets) ; les analyses de flux des clics sur le web ; et les applications de gestion des relations avec les clients (CRM) émettant des alertes lorsque l’expérience client se dégrade pendant un certain temps. Les entreprises recherchent la méthode la plus flexible, fiable et économique pour mener de telles analyses de données de flux d'événements en temps réel pour réussir dans ce monde moderne extrêmement compétitif.

La création de systèmes de traitement de flux de données est une tâche complexe. Les opérations de flux telles que les corrélations et les agrégations doivent non seulement être implémentées efficacement, mais elles doivent également être évolutives et à tolérance de panne. Des défis opérationnels supplémentaires surviennent, comme par exemple, le déploiement, le débogage et la surveillance. Les coûts de création et de maintenance d'une telle solution augmentent rapidement. De grandes entreprises se sont résignées à payer de tels coûts avec des solutions personnalisées, tandis que des petites entreprises manquent de nombreuses opportunités, car elles sont bloquées par la complexité et les coûts trop élevés. En fournissant un service géré dans le cloud en particulier pour l’analyse de la diffusion en continu, Stream Analytics s’attaque à ces défis.

En quelques clics dans le portail Azure, les clients peuvent créer un travail d’analyse de la diffusion en continu à l’aide d’un langage de type SQL pour spécifier des transformations et surveiller l’évolution et/ou la vitesse de leur pipeline de diffusion global. Ce service peut facilement évoluer pour traiter chaque seconde une quantité d'événements allant de quelques kilo-octets à un gigaoctet. Les clients de la plupart des autres solutions de diffusion disponibles aujourd’hui doivent écrire des codes personnalisés complexes, tandis que les clients de Stream Analytics peuvent utiliser un langage SQL simple, convivial et déclaratif.

Stream Analytics fournit une gamme d’opérateurs allant de simples filtres à des corrélations complexes. La définition d'opérations fenêtrées basées sur le temps telles que les agrégats fenêtrés, la corrélation de plusieurs flux pour détecter des modèles (comme des séquences) ou la comparaison de conditions actives par rapport à un historique de valeurs et de modèles, peut s'effectuer en quelques minutes à l'aide de l'ensemble accessible des opérateurs de langage de requêtes SQL de Stream Analytics. Pour spécifier un pipeline de diffusion en continu, vous n'avez qu'à configurer ses entrées et sorties et fournir une requête SQL décrivant les transformations de votre choix. Bien que cela suffise pour la plupart des cas, des scénarios plus complexes et plus évolués bénéficient des capacités de configuration de Stream Analytics. Les utilisateurs peuvent déterminer la quantité de puissance de calcul à dédier à chaque étape du pipeline pour atteindre le débit maximal souhaité.

Actuellement, Stream Analytics se connecte directement aux concentrateurs Azure Event Hubs pour l’ingestion de flux de données et au service blob Azure pour accéder aux données d’historique. La plage des interfaces d’entrée et de sortie des concentrateurs Azure Event Hubs facilite l’intégration de Stream Analytics à d’autres sources de données et moteurs de traitement sans dénaturer la capacité de diffusion des calculs. Vous pouvez écrire des données à partir de Stream Analytics vers Azure Storage, où elles peuvent ensuite être traitées à nouveau comme une série d'événements ou utilisées dans d'autres formats d'analyse par lots à l'aide d'Azure HDInsight. Stream Analytics tire parti des années de travail de l’équipe Microsoft Research dans le développement de moteurs de diffusion hautement optimisés pour le traitement urgent, ainsi que dans les intégrations de langages pour de telles spécifications intuitives. Stream Analytics tire parti de la communauté open source de Hadoop, YARN et REEF pour le traitement de la mise à l’échelle.


## Fonctionnalités clés

### Simplicité d'utilisation
Stream Analytics prend en charge un modèle de requête simple et déclaratif pour la description des transformations. Pour faciliter l'utilisation, nous avons sélectionné une variante du langage SQL comme langage spécifique au domaine (DSL) et nous évitons à nos clients les complexités techniques inhérentes à notre système de diffusion. À l'aide du langage de requête de Stream Analytics, vous pouvez implémenter rapidement et facilement des fonctions temporelles, comme les jointures temporelles, les agrégats fenêtrés, les filtres temporels, ainsi que d'autres opérations courantes telles que les jointures, les agrégats, les projections et les filtres.

Même si nous étendons la sémantique SQL selon différentes méthodes intuitives pour un utilisateur SQL, nous respectons les limites syntaxiques du standard SQL afin de faciliter l'implémentation et l'utilisation de l'outil. Pour plus d'informations sur notre langage de requête, consultez la section « Étapes suivantes ».

### Extensibilité
Stream Analytics est capable de gérer un débit d'événements élevé allant jusqu'à 1 Go/s. L'intégration aux concentrateurs d'événements Azure permet à la solution d'ingérer des millions d'événements par seconde. Ces événements peuvent provenir d'appareils connectés, de flux de clics, de fichiers journaux, etc. Pour ce faire, nous exploitons la capacité de partitionnement des concentrateurs d'événements, qui peuvent produire 1 Mo/s par partition. Stream Analytics prend en charge le partitionnement lors du traitement de ces événements ingérés horizontalement et verticalement. Un utilisateur peut partitionner le calcul en plusieurs étapes logiques au sein de la définition de requête. Chacune de ces étapes peut elle-même être partitionnée en éléments d'exécution parallèle de données. Au fil du temps, la solution Stream Analytics sera automatiquement mise à l'échelle selon le taux de réception d'événements, la complexité du traitement et les latences prévues pour permettre aux utilisateurs de personnaliser leur charge de travail de façon appropriée. Pour plus d'informations sur l'implémentation de l'extensibilité, consultez les liens de la section « Étapes suivantes ».

### Fiabilité, répétition et récupération rapide
Service géré dans le cloud, Stream Analytics aide à empêcher la perte de données et fournit une continuité d’activité en cas de défaillances de nœud grâce à ses capacités de points de contrôle et de récupération intégrées. Celles-ci sont fournies par l'architecture du modèle d'ancrage du client de Stream Analytics. Le service est conçu pour conserver l'état afin d'optimiser la reprise après défaillance de nœud, la reprise des calculs et la mise en cache des données de sortie, tout cela afin de réagir efficacement en cas de défaillances en aval.

Doté d'une capacité de conservation de l'état en interne, le service peut fournir des résultats reproductibles pour lesquels vous pouvez archiver les événements et recommencer un traitement ultérieurement, tout en obtenant les mêmes résultats. Cette fonctionnalité permet aux clients de revenir en arrière et d'examiner les calculs des scénarios tels que l'analyse des causes premières et l'analyse de simulations. Ces fonctionnalités combinées permettent d'accélérer la récupération en cas de défaillance de nœud, ainsi que le retraitement des états perdus.

### Latence faible
La latence faible est un autre avantage d’un service géré. Stream Analytics est actuellement optimisé pour les latences observables de bout en bout dans la plage secondaire. Cela permet au système de procéder en permanence au traitement, à un débit élevé. Le modèle de communication est un modèle de traitement par lots adaptatif basé sur l'extraction, qui fonctionne selon des délais d'attente et des limites de taille configurés. Par conséquent, les événements et les autres enregistrements sont traités par lot jusqu'à ce que les limites soient atteintes. Même avec un débit élevé, le système est en mesure de fournir une latence faible.

### Données de référence
Stream Analytics permet aux utilisateurs de spécifier et d'utiliser des données de référence. Il peut s'agir de données d'historique ou de données qui changent moins souvent au fil du temps. Le système simplifie l'utilisation des données de référence pour les traiter comme n'importe quel autre flux d'événement entrant à joindre avec d'autres flux d'événements reçus en temps réel pour effectuer des transformations. Pour plus d’informations sur la construction et l’utilisation des données de référence, consultez la section « Étapes suivantes ».



## Objectifs professionnels lors du choix d'Azure Stream Analytics

### Moindre coût
Le service cloud Stream Analytics est optimisé pour permettre aux utilisateurs d’exécuter et de tenir à jour des solutions d’analyse en temps réel à prix réduit. Ce service est créé pour vous permettre de payer en fonction de votre utilisation. L’utilisation est définie en fonction du volume d’événements traités et de la quantité de puissance de calcul configurée dans le cluster pour gérer les travaux d’analyse de diffusion en continu respectifs.

### Implémentation rapide
Avec Stream Analytics, vous pouvez avoir une solution de traitement d'événements en temps réel hautement évolutive sans frais de matériel ou d'autres coûts initiaux, sans installation ni configuration chronophage. Microsoft Azure, le fournisseur de service cloud, s’occupe de tout cela pour vous. Vous pouvez être opérationnel en quelques minutes en vous inscrivant simplement au service par le biais du portail Azure. L'interface utilisateur conviviale du portail vous guide tout au long de la procédure de prise en main rapide pour configurer et tester votre source d'entrée et votre magasin de sortie, mais aussi obtenir un éditeur de requête simple doté de fonctionnalités de saisie et de recommandation automatiques.

### Développement rapide
Stream Analytics vous permet de réduire les interférences et la complexité lors du développement des fonctions d'analyse pour la mise à l'échelle des systèmes distribués. Les développeurs décrivent simplement les transformations qu'ils souhaitent dans un langage de requête basé sur SQL et le système traite automatiquement la distribution de la charge de travail pour la mise à l'échelle, les performances et la résilience, éliminant l'obligation de programmation procédurale requise dans la plupart des solutions de traitement de flux. Grâce à ses capacités intégrées, ainsi que son portail utilisateur convivial, Stream Analytics s'adresse à un développeur de haut niveau pour effectuer rapidement et facilement des traitements en temps réel sans avoir à se soucier de l'approvisionnement de l'infrastructure, de la maintenance et de la mise à l'échelle.

## Scénarios et cas d'utilisation

### Analyse en temps réel pour l'Internet des objets (IoT)
Comme les appareils deviennent de plus en plus efficaces et que leurs capacités de communication augmentent, les perspectives d'exploitation des données qu'ils produisent et recueillent continuent d'évoluer, dans les espaces commerciaux et grand public. Ces perspectives consistent à rapidement combiner et traiter une telle quantité de données disponible afin d'obtenir plus d'informations sur l'environnement qui nous entoure et sur les appareils que nous utilisons régulièrement. Pour comprendre ce qu'est un scénario IoT standard, prenons l'exemple d'un distributeur automatique :

Les distributeurs automatiques envoient régulièrement des données telles que le stock, l'état et la température d'un produit à une passerelle de champ (si le distributeur ne peut pas utiliser le protocole Internet) ou à une passerelle de cloud (si le distributeur peut utiliser le protocole Internet) pour qu'elles soient saisies dans le système. Le flux de données entrant est traité et transformé pour que la sortie calculée puisse être immédiatement transmise via les passerelles vers l'appareil afin d'entreprendre l'action adéquate. Par exemple, si le distributeur surchauffe, il faut peut-être qu'il redémarre ou procède à une mise à jour de microprogramme sans intervention humaine. Le résultat traité peut également déclencher d'autres alertes et notifications pour planifier automatiquement l'intervention d'un technicien en fonction des événements.

Lorsque davantage de données sont collectées et traitées, Machine Learning peut être utilisé pour développer et découvrir des modèles dans le système. Par exemple, la possibilité existe de prévoir le moment où les machines devront être révisées en fonction des flux d'événements en temps réel saisis dans le système ou l'intégration d'une maintenance préventive au planning d'un technicien lorsque des problèmes sont sur le point de survenir.

Cet exemple d'appareil envoyant des informations à un système de traitement et pouvant réagir en fonction des résultats traités en temps réel est commun dans les cas d'usage de l'Internet des objets. D'autres scénarios portent sur des voitures connectées, des analyses de flux de clics, des gestions d'installations, etc. Afin d'optimiser cette boucle de rétroaction pour obtenir une latence faible et un débit élevé, vous pouvez utiliser Stream Analytics pour recevoir des données à partir des concentrateurs d'événements Azure, les traiter et les renvoyer aux concentrateurs d'événements pour que l'appareil concerné entreprenne l'action appropriée.


![Analyse des données volumineuses et Internet des objets : analyse en temps réel d’Azure Stream Analytics pour l’Internet des objets (IoT).][img.stream.analytics.scenario1]


### Analyse de télémétrie et de fichier journal en utilisant des tableaux de bord
Tandis que le volume d'appareils, de machines et d'applications augmente, le besoin de surveiller et de répondre aux exigences changeantes d'une entreprise par le biais d'analyses détaillées en temps quasi-réel constitue un cas d'usage professionnel répandu. Pour comprendre à quoi correspond un scénario d'analyse de télémétrie/fichier journal standard, on peut prendre l'exemple d'un service ou d'une application en ligne. Cependant, ce modèle est très répandu dans les entreprises qui collectent des données ou créent des rapports de télémétrie d'application ou d'appareil :

L'application ou le service collecte régulièrement des données de contrôle d'intégrité. Les données représentant l'état actuel de l'application ou de l'infrastructure à un moment précis, les journaux de demandes utilisateur et d'autres types de données représentant des actions ou des activités effectuées dans l'application... tout cela est collecté. Ces données sont généralement enregistrées dans un objet blob ou tout autre type de magasin de données, en vue d'un traitement ultérieur.

Avec la récente tendance des tableaux de bord en temps réel, en plus de l'enregistrement des données dans un objet blob ou tout autre type de magasin en vue d'une analyse d'historique, les clients cherchent à traiter et transformer le flux de données entrant directement pour pouvoir le transférer immédiatement aux utilisateurs finaux, sous la forme de tableaux de bord et/ou de notifications, lorsque des actions doivent être entreprises. Par exemple, si un site de service tombe en panne, le personnel opérationnel peut être averti pour commencer l'investigation et résoudre le problème rapidement. Dans plusieurs cas d'usage, un être humain analyse généralement un tableau de bord en temps réel basé sur le jeu de données mis à jour après le traitement des données saisies via l'analyse de flux.
 
![Azure Stream Analytics : analyse de télémétrie et de journal des données de diffusion en continu par le biais des tableaux de bord.][img.stream.analytics.scenario2]

### Archivage d'événement en vue d'un traitement ultérieur
Les attentes d'exécution rapide et flexible continuent de croître dans les entreprises. Les entreprises et les développeurs optent désormais pour des services cloud faciles à utiliser pour faire face à la demande d’agilité, et recherchent des plateformes qui leur permettent de recevoir et de traiter un flux continu de données généré par leurs systèmes en quasi-temps réel. Aujourd’hui, ces clients ne peuvent pas tirer parti d’un service géré optimisé pour une faible latence qui écrit vers un magasin de données et, par conséquent, finit par perdre plusieurs jeux de données cruciaux pouvant révéler des informations commerciales critiques. Un scénario d'archivage d'événement canonique se présente comme suit :

les données de plusieurs appareils et plateformes répartis dans le monde sont envoyées vers un collecteur de données centralisé. Une fois les données sur le site central, une transformation sans état est exécutée sur celles-ci, comme la purge des informations d'identification personnelle, l'ajout du balisage géographique et l'exécution de la recherche IP. Les données transformées sont alors archivées dans le stockage Blob d'une manière qui peut être facilement utilisée par HDInsight ou d'autres outils pour le traitement hors connexion.

![Archivage d’événement d’Azure Stream Analytics en vue d’un traitement de flux ultérieur.][img.stream.analytics.scenario3]
 
## Obtenir de l'aide
Pour obtenir une assistance, essayez notre [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## Étapes suivantes
Stream Analytics, un service géré d’analyse de diffusion en continu des données à partir de l’Internet des objets vous a été présenté. Pour en savoir plus sur ce service, consultez les rubriques suivantes :

- [Prise en main d'Azure Stream Analytics](stream-analytics-get-started.md)
- [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
- [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Références sur l'API REST de gestion d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)


<!--Image references-->
[img.stream.analytics.scenario1]: ./media/stream-analytics-introduction/Introduction-to-Azure-Stream-Analytics_01.png
[img.stream.analytics.scenario2]: ./media/stream-analytics-introduction/Introduction-to-Azure-Stream-Analytics_02.png
[img.stream.analytics.scenario3]: ./media/stream-analytics-introduction/Introduction-to-Azure-Stream-Analytics_03.png


<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
 

<!---HONumber=August15_HO6-->