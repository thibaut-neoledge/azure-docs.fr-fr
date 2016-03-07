<properties
   pageTitle="Paramétrage de l’agrégation de données et des performances de requête avec Elasticsearch sur Azure | Microsoft Azure"
   description="Résumé des considérations relatives à l’optimisation des performances de requête et de recherche pour Elasticsearch."
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/18/2016"
   ms.author="masimms"/>
   
# Paramétrage de l’agrégation de données et des performances de requête avec Elasticsearch sur Azure

Cet article fait [partie d’une série](guidance-elasticsearch.md).

L’utilité principale d’Elasticsearch est la prise en charge des recherches parmi les données. Les utilisateurs doivent être en mesure de localiser rapidement les informations qu’ils cherchent. En outre, le système doit permettre aux utilisateurs d’envoyer des requêtes aux données, de rechercher des corrélations et de parvenir à des conclusions pouvant guider leurs décisions commerciales. Ce traitement est ce qui différencie les données des informations.

Ce document résume les options que vous pouvez envisager lorsque vous cherchez à déterminer la meilleure façon d’optimiser les performances des requêtes et de la recherche sur votre système.

Toutes les recommandations relatives aux performances dépendent largement des scénarios qui s’appliquent à votre situation, du volume de données que vous indexez et de la fréquence à laquelle les applications et les utilisateurs interrogent vos données. Vous devez soigneusement tester les résultats de toute modification de configuration ou de structure d’indexation à l’aide de vos propres données et charges de travail, afin d’évaluer les avantages pour vos scénarios spécifiques. À cette fin, ce document décrit également un certain nombre de tests qui ont été effectuées pour un scénario spécifique, implémenté à l’aide de différentes configurations. Vous pouvez adapter l’approche adoptée pour évaluer les performances de vos propres systèmes.

## Considérations sur les performances de requête et les index

Cette section décrit certains facteurs courants que vous devez considérer lors de la conception d’index devant prendre en charge des requêtes et des recherches rapides.

### Le stockage de plusieurs types dans un index

Un index Elasticsearch peut contenir plusieurs types. Il est préférable d’éviter cette approche et de créer un index séparé pour chaque type. Observez les points suivants :

- Différents types peuvent spécifier différents analyseurs et il n’est pas toujours évident de savoir quel analyseur Elasticsearch doit utiliser si une requête est exécutée au niveau de l’index plutôt qu’au niveau du type. Consultez la page [Avoiding Type Gotchas](https://www.elastic.co/guide/en/elasticsearch/guide/current/mapping.html#_avoiding_type_gotchas) (Éviter les pièges de type) pour plus d’informations.

- Les partitions d’index qui contiennent plusieurs types seront probablement plus volumineuses que celles des index qui contiennent un type unique. Plus une partition est volumineuse, plus l’effort requis par Elasticsearch pour filtrer les données lors de l’exécution des requêtes est important.

- S’il existe une différence significative entre les volumes de données pour les types, les informations pour un type peuvent être distribuées entre plusieurs partitions, ce qui réduit l’efficacité des recherches qui récupèrent ces données.

![](./media/guidance-elasticsearch/query-performance1.png)

***Figure 1. Les effets du partage d’un index entre les types***

La figure 1 illustre ce scénario. Dans la partie supérieure du diagramme, le même index est partagé par des documents de type A et de type B. Il existe bien plus de documents de type A que de type B. Les recherches pour le type A impliquent l’interrogation des quatre partitions. La partie inférieure du diagramme illustre le scénario selon lequel différents index sont créés pour chaque type. Dans ce cas, les recherches pour le type A ne nécessitent un accès qu’à deux partitions.

Les petites partitions peuvent être plus équitablement distribuées que les partitions volumineuses. Il est ainsi plus facile pour Elasticsearch de répartir la charge sur les nœuds.

Chaque type peut avoir une période de rétention différente. Il peut être difficile d’archiver les anciennes données qui partagent des partitions avec des données actives.

Toutefois, dans certaines circonstances, le partage d’un index entre différents types peut être efficace, si :

- Les recherches touchent de manière équitable les types stockés dans le même index.

- Chaque type ne contient qu’un petit nombre de documents et conserver un ensemble de partitions différent pour chaque type n’est pas du tout avantageux dans ce cas.

### Optimisation des types d’index

Un index Elasticsearch contient une copie des documents JSON d’origine qui ont été utilisés pour le remplir. Cette information est conservée dans le champ [*\_source*](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-source-field.html#mapping-source-field) de chaque élément indexé. Ces données ne peuvent pas être recherchées, mais elles sont retournées par défaut par les requêtes *get* et *search*. Toutefois, ce champ entraîne des surcharges et occupe du stockage, augmentant ainsi le volume des partitions et des E/S effectuées. Vous pouvez désactiver le champ *\_source* pour chaque type :

```http
PUT my_index
{
    "mappings": {
		"my_type": {
			"_source": {
				"enabled": false
			}
		}
	}
}
```

La désactivation de ce champ supprime également la possibilité d’effectuer les opérations suivantes :

- Mise à jour des données dans l’index à l’aide de l’API de *mise à jour*.
- Recherches qui retournent des données en surbrillance.
- Réindexation d’un index Elasticsearch directement vers un autre index.
- Modification des paramètres de mappage ou d’analyse.
- Débogage des requêtes en affichant le document d’origine.
 
### Réindexation des données

Le nombre de partitions disponibles pour un index détermine la capacité de l’index. Vous pouvez effectuer une estimation initiale (et éclairée) du nombre de partitions nécessaires, mais vous devez toujours envisager votre stratégie de réindexation des documents à l’avance. Dans de nombreux cas, la réindexation peut être envisagée au fur et à mesure. Vous pouvez choisir de ne pas allouer un grand nombre de partitions à un index au début, par souci d’optimisation de la recherche, puis d’allouer de nouvelles partitions lorsque le volume de données augmente. Dans d’autres cas, la réindexation devra être exécutée de manière plus ponctuelle si vos estimations sur la croissance du volume de données se révèlent tout simplement incorrectes.

> [AZURE.NOTE] La réindexation n’est pas forcément nécessaire pour les données qui vieillissent rapidement. Dans ce cas, une application peut créer un nouvel index pour chaque période. Par exemple, les journaux de performances et les données d’audit peuvent être stockés dans un nouvel index tous les jours.

Pour une réindexation efficace, il faut créer un index à partir des données contenues dans un ancien index, puis supprimer cet ancien index. Si un index est volumineux, ce processus peut prendre du temps et vous devez vous assurer que les données restent consultables pendant cette période. Pour cette raison, vous devez créer un [alias pour chaque index](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-aliases.html) et les requêtes doivent passer par cet alias pour récupérer les données. Lors de la réindexation, faites en sorte que l’alias dirige la requête vers l’ancien index, puis, une fois la réindexation terminée, faites basculer l’alias sur le nouvel index. Cette approche est également utile pour accéder aux données temporelles qui créent un nouvel index chaque jour. Pour accéder aux données actuelles, il faut utiliser un alias qui parcourt le nouvel index lors de sa création.

### Gestion des mappages

Elasticsearch utilise le mappage pour interpréter les données de chaque champ dans un document. Chaque type a son propre mappage, qui définit efficacement un schéma pour ce type. Elasticsearch utilise ces informations pour générer un index inversé pour chaque champ dans les documents d’un certain type. Dans tous les documents, chaque champ a un type de données (tel que *string*, *date* ou *long*), ainsi qu’une valeur. Vous pouvez spécifier les mappages pour un index lors de la création de celui-ci ou Elasticsearch peut les déduire lorsque de nouveaux documents sont ajoutés à un type. Prenez toutefois en compte les points suivants :

- Les mappages générés de manière dynamique peuvent provoquer des erreurs en fonction de la manière dont les champs sont interprétés lorsque des documents sont ajoutés à un index. Par exemple, le document 1 peut inclure un champ A qui contient un nombre. Elasticsearch ajoute donc un mappage qui spécifie que ce champ est de type *long*. Si un prochain document ajouté comprend un champ A qui contient des données non numériques, une erreur se produit. Dans ce cas, les données du champ A auraient dû être interprétées comme des données de type string lors de l’ajout du premier document. En spécifiant ce mappage lors de la création de l’index vous pouvez empêcher de tels problèmes.

- Faites en sorte de concevoir vos documents de manière à éviter de générer des mappages excessivement volumineux, car ceux-ci peuvent être à l’origine de surcharges significatives lors des recherches, consommer une grande quantité de mémoire et également provoquer l’échec de requêtes. Adoptez une convention d’affectation de noms cohérente pour les champs des documents qui partagent le même type. Par exemple, n’utilisez pas des noms de champ tels que « nom\_de\_famille », « NomDeFamille » et « Nom » dans différents documents. Utilisez le même nom de champ dans chaque document. En outre, n’essayez pas d’utiliser des valeurs en tant que clés. Il s’agit d’une approche courante dans les bases de données de type Colonne-Famille, mais elle peut provoquer des échecs et des problèmes d’efficacité avec Elasticsearch. Pour plus d’informations, voir [Mapping Explosion](https://www.elastic.co/blog/found-crash-elasticsearch#mapping-explosion) (Explosion des mappages).

- Utilisez *not\_analyzed* pour éviter la création de jetons le cas échéant. Par exemple, si un document comprend un champ de type string nommé *data* qui contient la valeur « ABC-DEF », vous pouvez tenter d’effectuer une recherche de tous les documents qui correspondent à cette valeur comme suit :

```http
GET /myindex/mydata/_search
{
	"query" : {
		"filtered" : {
			"filter" : {
				"term" : {
					"data" : "ABC-DEF"
				}
			}
		}
	}
}
```

Toutefois, cette recherche ne retournera pas les résultats attendus en raison de la façon dont la chaîne « ABC-DEF » a été segmentée lors de son indexation. Elle a été fractionnée en deux jetons, « ABC » et « DEF », à cause du trait d’union. Cette fonctionnalité est conçue pour prendre en charge la recherche en texte intégral, mais si vous souhaitez que la chaîne soit interprétée comme un élément unique, vous devez désactiver la création de jetons lorsque le document est ajouté à l’index. Vous pouvez utiliser un mappage tel que celui-ci :

```http
PUT /myindex
{
	"mappings" : {
		"mydata" : {
			"properties" : {
				"data" : {
					"type" : "string",
					"index" : "not_analyzed"
				}
			}
		}
	}
}
```

Pour plus d’informations, consultez l’article [Finding Exact Values](https://www.elastic.co/guide/en/elasticsearch/guide/current/_finding_exact_values.html#_term_filter_with_text) (Recherche de valeurs exactes).

### Utilisation de valeurs Doc

Nombre de requêtes et d’agrégations nécessitent que les données soient triées pendant l’opération de recherche. Pour effectuer ce tri, il faut pouvoir mapper un ou plusieurs termes à une liste de documents. Pour faciliter ce processus, Elasticsearch peut charger dans la mémoire toutes les valeurs d’un champ utilisées comme clés de tri. Ces informations sont appelées les *données de champ*. La mise en cache des données de champ dans la mémoire entraîne une diminution des E/S et peut être plus rapide que la lecture à répétition des mêmes données à partir du disque. Cependant, si un champ a une cardinalité élevée, le stockage des données de champ en mémoire peut consommer beaucoup d’espace du tas et éventuellement affecter les opérations effectuées en même temps, ou même épuiser le stockage et provoquer un échec d’Elasticsearch.

C’est pour cela qu’Elasticsearch prend également en charge les *valeurs Doc*. Une valeur Doc est similaire à un élément des données de champ en mémoire, sauf qu’elle est stockée sur le disque et créée lorsque les données sont stockées dans un index (les données de champ sont construites dynamiquement lorsqu’une requête est exécutée). Les valeurs Doc ne consomment pas d’espace du tas et sont donc utiles pour les requêtes qui trient ou agrègent des données entre des champs qui peuvent contenir un grand nombre de valeurs uniques. En outre, la pression réduite sur le tas peut aider à compenser les écarts de performances entre la récupération des données sur le disque et la lecture à partir de la mémoire. Le nettoyage de la mémoire se produit alors moins fréquemment et les opérations simultanées qui utilisent la mémoire sont moins susceptibles d’être affectées.

Dans un index, vous pouvez activer ou désactiver les valeurs Doc par propriété à l’aide de l’attribut *doc\_values*, comme illustré dans l’exemple suivant :

```http
PUT /myindex
{
	"mappings" : {
		"mydata" : {
			"properties" : {
				"data" : {
					...
				"doc_values": true
				}
			}
		}
	}
}
```

> [AZURE.NOTE] Les valeurs Doc sont activées par défaut dans Elasticsearch 2.0.0 et les versions ultérieures.

L’impact de l’utilisation des valeurs Doc dépendra sûrement de vos propres scénarios de requête et de vos données. Préparez-vous donc à effectuer des tests de performance pour établir leur utilité. Notez également que les valeurs Doc ne fonctionnent pas avec les champs de type string analysés. Pour plus d’informations, consultez l’article [Doc Values](https://www.elastic.co/guide/en/elasticsearch/guide/current/doc-values.html#doc-values) (Valeurs Doc).

### Utilisation de nœuds client

Toutes les requêtes sont traitées par le nœud qui reçoit la demande en premier. Ce nœud envoie ensuite des requêtes à tous les autres nœuds contenant des partitions correspondant aux index interrogés, puis regroupe les résultats pour retourner la réponse. Si une requête implique l’agrégation de données ou des calculs complexes, le nœud initial est chargé d’effectuer le traitement approprié. Si votre système ne doit prendre en charge qu’un petit nombre de requêtes complexes, pensez à créer un pool de nœuds client pour alléger la charge des nœuds de données. À l’inverse, si votre système doit gérer un grand nombre de requêtes simples, envoyez ces requêtes directement aux nœuds de données et utilisez un équilibreur de charge pour répartir les requêtes équitablement.

### Utilisation de réplicas pour réduire les conflits de requête

Une stratégie courante pour améliorer les performances des requêtes consiste à créer plusieurs réplicas de chaque index. Les opérations de récupération de données peuvent être effectuées en extrayant les données à partir d’un réplica. Toutefois, cette stratégie peut sérieusement affecter les performances des opérations d’ingestion de données. Elle doit donc être utilisée avec précaution dans les scénarios qui impliquent des charges de travail mixtes. En outre, cette stratégie n’est avantageuse que si les réplicas sont répartis entre les nœuds et ne se disputent pas les ressources avec les partitions principales qui font partie du même index. N’oubliez pas qu’il est possible d’augmenter ou de diminuer dynamiquement le nombre de réplicas d’un index.

### Utilisation du cache de demande de la partition

Elasticsearch peut mettre en cache dans la mémoire les données locales demandées par les requêtes sur chaque partition. Cela permet aux requêtes qui accèdent aux mêmes données de s’exécuter plus rapidement. Les données peuvent ainsi être extraites de la mémoire plutôt que du disque. Les données dans le cache sont invalidées lorsque la partition est actualisée et que les données ont changé. La fréquence d’actualisation est régie par la valeur du paramètre *refresh\_interval* de l’index. Le cache de demande de la partition pour un index est désactivé par défaut, mais vous pouvez l’activer comme suit :

```http
PUT /myindex/_settings
{
	"index.requests.cache.enable": true
}
```

Le cache de demande de la partition est plus approprié pour les informations relativement statiques, telles que des données d’historique ou de journalisation.

## Test et analyse des performances d’agrégation et de recherche
Cette section décrit les résultats d’une série de tests effectués sur des configurations de cluster et d’index variables. Chaque test a démarré avec un index vide qui s’est rempli pendant le test via des opérations d’insertion en bloc (chaque opération ajoutait 1 000 documents). Pendant ce temps, plusieurs requêtes conçues pour rechercher des données spécifiques et pour générer des agrégations ont été répétées toutes les 5 secondes. L’objectif de ces tests était de déterminer dans quelle mesure les performances des requêtes étaient affectées par le volume de données.

Tous les documents de l’index avaient le même schéma. Le tableau suivant résume les champs du schéma :

 Nom | Type | Remarques |
  ----------------------------- | ------------ | -------------------------------------------------------- |
 Organisation | Chaîne | Le test génère 200 organisations uniques. |
 CustomField1 - CustomField5 |String |Il s’agit de cinq champs de type string qui sont définis sur la chaîne vide.|
 DateTimeRecievedUtc |Timestamp |La date et l’heure d’ajout du document.|
 Hôte |String |Ce champ est défini sur la chaîne vide.|
 HttpMethod |Chaîne |Ce champ est défini sur une des valeurs suivantes : « POST », « GET », « PUT ».|
 HttpReferrer |Chaîne |Ce champ est défini sur la chaîne vide.|
 HttpRequest |Chaîne |Ce champ est rempli par du texte aléatoire contenant entre 10 et 200 caractères.|
 HttpUserAgent |String |Ce champ est défini sur la chaîne vide.|
 HttpVersion |Chaîne |Ce champ est défini sur la chaîne vide.|
 OrganizationName |Chaîne |Ce champ est défini sur la même valeur que le champ Organization.|
 SourceIp |IP |Ce champ contient une adresse IP indiquant l’« origine » des données. |
 SourceIpAreaCode |Long |Ce champ est défini sur 0.|
 SourceIpAsnNr |Chaîne |Ce champ est défini sur « AS##### ».|
 SourceIpBase10 |Long |Ce champ est défini sur 500.|
 SourceIpCountryCode |String |Ce champ contient un code de pays de 2 caractères. |
 SourceIpCity |String |Ce champ contient une chaîne permettant d’identifier une ville dans un pays. |
 SourceIpLatitude |Double |Ce champ contient une valeur aléatoire.|
 SourceIpLongitude |Double |Ce champ contient une valeur aléatoire.|
 SourceIpMetroCode |Long |Ce champ est défini sur 0.|
 SourceIpPostalCode |String |Ce champ est défini sur la chaîne vide.|
 SourceLatLong |Geo point |Ce champ est défini sur un point géographique aléatoire.|
 SourcePort |Chaîne |Ce champ est rempli avec la représentation sous forme de chaîne d’un nombre aléatoire.|
 TargetIp |IP |Ce champ est rempli avec une adresse IP aléatoire entre 0.0.100.100 et 255.9.100.100.|
 SourcedFrom |String |Ce champ est défini sur la chaîne « MonitoringCollector ».|
 TargetPort |Chaîne |Ce champ est rempli avec la représentation sous forme de chaîne d’un nombre aléatoire.|
 Rating |Chaîne |Ce champ est rempli avec l’une des 20 valeurs de chaîne différentes sélectionnée au hasard.|
 UseHumanReadableDateTimes |Boolean |Ce champ est défini sur false.|

Les requêtes suivantes ont été effectuées en lot par chaque itération du test (dans le reste de ce document, les noms en italique sont utilisés pour faire référence à ces requêtes) :

- Combien de documents avec chaque valeur *classement* ont été ajoutés au cours des 15 dernières minutes (*Nombre par classement*) ?

- Combien de documents ont été ajoutés pendant chaque intervalle de 5 minutes au cours des 15 dernières minutes (*Nombre au fil du temps*) ?

- Combien de documents de chaque valeur *Rating* ont été ajoutés pour chaque pays au cours des 15 dernières minutes (*Résultats par pays*) ?

- Quelles sont les 15 organisations qui apparaissent le plus souvent dans les documents ajoutés au cours des 15 dernières minutes (*15 organisations les plus fréquentes*) ?

- Combien d’organisations différentes apparaissent dans les documents ajoutés au cours des 15 dernières minutes (*Nombre d’organisations différentes*) ?

- Combien de documents ont été ajoutés au cours des 15 dernières minutes (*Nombre total de résultats*) ?

- Combien de valeurs *SourceIp* différentes apparaissent dans les documents ajoutés au cours des 15 dernières minutes (*Nombre d’IP différentes*) ?

Les tests ont été effectués pour comprendre les effets des variables suivantes :

- **Type de disque**. Le test a été exécuté sur un cluster à 6 nœuds de machines virtuelles D4 utilisant un stockage standard (HDD), puis répété sur un cluster à 6 nœuds de machines virtuelles DS4 utilisant un stockage premium (SSD).

- **Taille de la machine - Mise à l’échelle**. Le test a été effectué sur un cluster à 6 nœuds comprenant des machines virtuelles DS3 (le *petit* cluster), puis répété sur un cluster de machines virtuelles DS4 (le cluster *moyen*), puis encore répété sur un cluster de machines virtuelles DS14 (le *grand* cluster). Le tableau suivant récapitule les principales caractéristiques de chaque référence (SKU) de machine virtuelle :

Cluster |Référence de la machine virtuelle |Nombre de cœurs |Nombre de disques de données |RAM (Go)|
--------- |--------------- |----------------- |---------------------- |--------|
Petite |DS3 standard |4 |8 |14 |
Moyenne |DS4 standard |8 |16 |28 |
Grande |DS14 standard |16 |32 |112 |

- **Valeurs Doc**. Initialement, les tests ont été effectués en définissant le paramètre d’index *doc\_values* sur *true*. Les tests sélectionnés ont été répétés avec la valeur *false* pour le paramètre *doc\_values*.

## Résultats de performance – Type de disque

Le tableau ci-dessous résume les temps de réponse de la tâche effectuée en exécutant les tests sur le cluster à 6 nœuds de machines virtuelles D4 (HDD) et sur le cluster à 6 nœuds de machines virtuelles DS4 (SSD). La configuration d’Elasticsearch dans les deux clusters était la même. Les données étaient réparties sur 16 disques sur chaque nœud et chaque nœud disposait de 14 Go de mémoire RAM alloués à la machine virtuelle Java exécutant Elasticsearch. La mémoire restante (14 Go également) était dédiée au fonctionnement du système d’exploitation. Chaque test a été exécuté pendant 24 heures. Cette durée a été définie de manière à ce que la hausse du volume des données ait le temps de se faire sentir et que le système se stabilise :

 Cluster |Opération/Requête |Temps de réponse moyen (en ms)|
 -----------|---------------------------- |--------------------------|
 D4 |Ingestion |978 |
 |Nombre par classement |103 |
 |Nombre au fil du temps |134 |
 |Résultats par pays |199 |
 |15 organisations les plus fréquentes |137 |
 |Nombre d’organisations différentes |139 |
 |Nombre d’IP différentes |510 |
 |Nombre total de résultats |89 |
 DS4 |Ingestion |511 |
 |Nombre par classement |187 |
 |Nombre au fil du temps |411 |
 |Résultats par pays |402 |
 |15 organisations les plus fréquentes |307 |
 |Nombre d’organisations différentes |320 |
 |Nombre d’IP différentes |841 |
 |Nombre total de résultats |236 |

À première vue, il semblerait que le cluster DS4 effectue les requêtes de façon moins efficace que le cluster D4. Le temps de réponse est parfois deux fois supérieur (voire plus). Mais ces données ne suffisent pas à juger réellement de leurs performances respectives. Le tableau suivant indique le nombre d’opérations d’ingestion effectuées par chaque cluster (n’oubliez pas que 1 000 documents sont chargés à chaque opération) :

 Cluster | Opérations d’ingestion
 ----------|---------------------
 D4 | 264769              
 DS4 | 503157              

Le cluster DS4 a pu charger presque deux fois plus de données que le cluster D4 pendant le test. Par conséquent, lorsque vous analysez le temps de réponse pour chaque opération, vous devez également prendre en compte le nombre de documents à analyser à chaque requête, ainsi que le nombre de documents retournés.

Il s’agit de chiffres dynamiques, car le volume de documents dans l’index croît en continu. Vous ne pouvez pas simplement diviser 503137 par 264769 (le nombre d’opérations d’ingestion effectuées par chaque cluster), puis multiplier le résultat par le temps de réponse moyen pour chaque requête effectuée par le cluster D4 pour obtenir une valeur comparative, car ce serait ignorer la quantité d’opérations d’E/S exécutées simultanément par l’opération d’ingestion.

Vous devez donc mesurer la quantité de données écrites sur le disque et lues à partir du disque au cours du test. Le plan de test JMeter permet de capturer ces informations pour chaque nœud. Voici un résumé des résultats :

 Cluster |Moyenne des octets écrits/lus par chaque opération|
  --------- |--------------------------------------------|
 D4 |13471557 |
 DS4 |24643470 |

Ces données montrent que le cluster DS4 a été en mesure de maintenir un taux d’E/S environ 1,8 fois supérieur à celui du cluster D4. Vu qu’en dehors des disques toutes les ressources sont identiques, la différence doit être liée à l’utilisation de disques SSD à la place des disques durs.

Pour appuyer cette conclusion, les graphiques suivants illustrent la façon dont les opérations d’E/S ont été effectuées au fil du temps par chaque cluster :

![](./media/guidance-elasticsearch/query-performance2.png)

Le graphique du cluster D4 affiche des variations importantes, en particulier lors de la première partie du test. Ce phénomène est probablement dû à la limitation qui diminue le taux d’E/S. Lors des phases initiales du test, les requêtes s’exécutent rapidement, car il n’existe que peu de données à analyser. Les disques du cluster D4 peuvent donc fonctionner près de leur seuil maximal d’E/S par seconde, même si chaque opération d’E/S ne retourne pas beaucoup de données. Le cluster DS4 peut prendre en charge des taux d’E/S supérieurs et ne souffre donc pas du même niveau de limitation. Les taux d’E/S sont plus réguliers.

Pour illustrer cette théorie, les deux graphiques qui suivent montrent comment l’unité centrale a été bloquée par les E/S de disque au fil du temps (les délais d’attente du disque indiqués dans les graphiques sont un pourcentage du temps que l’unité centrale a passé à attendre les E/S) :

![](./media/guidance-elasticsearch/query-performance3.png)

Il est important de comprendre que dans ce scénario de test, il existe deux raisons principales pour lesquelles les opérations d’E/S bloquent l’unité centrale :

- Le sous-système d’E/S peut être en train de lire des données à partir du disque ou d’écrire des données sur le disque.

- Le sous-système d’E/S peut être limité par l’environnement hôte. Les disques Azure disposant d’un stockage standard ont un débit maximal de 500 E/S par seconde, tandis que ceux disposant d’un stockage premium ont un débit maximal de 5 000 E/S par seconde.

Pour le cluster D4, le temps passé à attendre les E/S durant la première moitié du test correspond de façon inversée aux données du graphique indiquant les taux d’E/S. En effet, les périodes où le taux d’E/S est bas correspondent aux périodes où l’unité centrale est le plus souvent bloquée.

Cela indique que les opérations d’E/S sont limitées. La situation change à mesure que le volume de données ajoutées au cluster augmente. Durant la deuxième partie du test, les pics d’attente correspondent aux pics de débit d’E/S par seconde. À ce stade, l’unité centrale est bloquée lors de l’exécution d’opérations d’E/S réelles. Là encore, avec le cluster DS4, le temps d’attente est beaucoup plus uniforme et chaque pic correspond non pas à un creux, mais à un pic équivalent dans les performances d’E/S. Cela signifie qu’il n’y a pas (ou peu) de limitation.

Il existe un autre facteur à prendre en compte. Pendant le test, le cluster D4 a généré 10 584 erreurs d’ingestion et 21 erreurs de requête. Aucune erreur ne s’est produite lors du test sur le cluster DS4.

## Résultats de performance – Mise à l’échelle

Le tableau ci-dessous récapitule les résultats des tests effectués sur le cluster moyen (DS4) et le grand cluster (DS14). Chaque machine virtuelle utilise des disques SSD pour contenir les données. Chaque test a été exécuté pendant 24 heures :

| Cluster |Opération/Requête |Nombre de requêtes |Temps de réponse moyen (en ms)|
|  -------------- |---------------------------- |-------------------- |--------------------------|
| Moyen (DS4) |Ingestion |503157 |511 |
| |Nombre par classement |6958 |187 |
| |Nombre au fil du temps |6958 |411 |
| |Résultats par pays |6958 |402 |
| |15 organisations les plus fréquentes |6958 |307 |
| |Nombre d’organisations différentes |6956 |320 |
| |Nombre d’IP différentes |6955 |841 |
| |Nombre total de résultats |6958 |236 |
| Grand (DS14) |Ingestion |502714 |511 |
| |Nombre par classement |7041 |201 |
| |Nombre au fil du temps |7040 |298 |
| |Résultats par pays |7039 |363 |
| |15 organisations les plus fréquentes |7038 |244 |
| |Nombre d’organisations différentes |7037 |283 |
| |Nombre d’IP différentes |7037 |681 |
| |Nombre total de résultats |7038 |200 |

<!-- 
DISCUSSION POINTS:

Similar volume of data ingested – same disk configuration for each cluster, and ingestion rate is 
constrained by I/O performance?

Average response time for queries decreases with SKU.

Show CPU graphs

Show memory utilization – more data cached, fewer GCs, etc.

-->

<!--
To isolate the effects of the ingestion operations and show how query performance varies as nodes scale up, a second set of tests was performed using the same nodes. The ingestion part of the test was omitted, and the index on each node was pre-populated with 100 million documents. An amended set of queries was performed; the time element limiting documents to those added in the last 15 minutes was removed as the data was now static. The tests ran for 90 minutes; there is less need to allow the system to stabilize due to the fixed amount of data. The following table summarizes the results obtained on each cluster:

> [AZURE.NOTE] The amended version of the test that omits the data ingestion process and that uses a set of indexes containing 100 million documents is referred to as the *query-only* test in the remainder of this document. You should not compare the performance of the queries in this test with that of the tests that perform ingestion and query operations because the queries have been modified and the volume of documents involved is different.

 |Cluster        |Operation/Query              |Number of Requests   Average Response Time (ms)
 | --------------| ----------------------------| -------------------- ----------------------------
 | Small (DS3)   | Count By Rating             |                      
 |               | Count Over Time             |                      
 |               | Hits By Country             |                      
 |               | Top 15 Organizations        |                      
 |               | Unique Count Organizations  |                      
 |               | Unique IP Count             |                      
 |               | Total Hits Counts           |                      
 | Medium (DS4)  | Count By Rating             |                      
 |               | Count Over Time             |                      
 |               | Hits By Country             |                      
 |               | Top 15 Organizations        |                      
 |               | Unique Count Organizations  |                      
 |               | Unique IP Count             |                      
 |               | Total Hits Counts           |                      
 | Large (DS14)  | Count By Rating             |                      
 |               | Count Over Time             |                      
 |               | Hits By Country             |                      
 |               | Top 15 Organizations        |                      
 |               | Unique Count Organizations  |                      
 |               | Unique IP Count             |                      
 |               | Total Hits Counts           |                      

### Performance Results – Scaling Out
-->


<!--
To show the system scales out with the number of nodes, tests were run using DS14 clusters comprising 1, 3, and 6 nodes. This time, only the query-only test was performed, using 100 million documents and running for 90 minutes:

> [AZURE.NOTE] For detailed information on how scaling out can affect the behavior of data ingestion operations, see the document [Maximizing Data Ingestion Performance with Elasticsearch on Azure](https://github.com/mspnp/azure-guidance/blob/master/Elasticsearch-Data-Ingestion-Performance.md).

|  Cluster   |Operation/Query              |Number of Requests   |Average Response Time (ms)
|  --------- |---------------------------- |-------------------- |----------------------------
|  1 Node    |Count By Rating              |                     |
|            |Count Over Time              |                     |
|            |Hits By Country              |                     |
|            |Top 15 Organizations         |                     |
|            |Unique Count Organizations   |                     |
|            |Unique IP Count              |                     |
|            |Total Hits Counts            |                     |
|  3 Nodes   |Count By Rating              |                     |
|            |Count Over Time              |                     |
|            |Hits By Country              |                     |
|            |Top 15 Organizations         |                     |
|            |Unique Count Organizations   |                     |
|            |Unique IP Count              |                     |
|            |Total Hits Counts            |                     |
|  6 Nodes   |Count By Rating              |                     |
|            |Count Over Time              |                     |
|            |Hits By Country              |                     |
|            |Top 15 Organizations         |                     |
|            |Unique Count Organizations   |                     |
|            |Unique IP Count              |                     |
|            |Total Hits Counts            |                     |
-->

<!--
### Performance Results – Number of Replicas

The ingestion and query tests were run against an index with a single replica. The tests were repeated on the 6-node DS4 and DS14 clusters using an index configured with two replicas. All tests ran for 24 hours. The table below shows the comparative results for one and two replicas:

|  Cluster|  Operation/Query            | response time 1 Replica (ms)| response time 2 Replicas (ms) | % Difference in Response Time
|  -------| ----------------------------| ----------------------------|----------- -------------------|--------------------- --------
|  DS4    |   Ingestion                 |   511                       |               655             |                          +28%
|         |   Count By Rating           |   187                       |               168             |                          -10%
|         |   Count Over Time           |   411                       |               309             |                          -25%
|         |   Hits By Country           |   402                       |               562             |                          +40%
|         |   Top 15 Organizations      |   307                       |               366             |                          +19%
|         |   Unique Count Organizations|   320                       |               378             |                          +18%
|         |   Unique IP Count           |   841                       |               987             |                          +17%
|         |   Total Hits Counts         |   236                       |               236             |                          +0%
|  DS14   |   Ingestion                 |   511                       |               618             |                          +21%
|         |   Count By Rating           |   201                       |               275             |                          +37%
|         |   Count Over Time           |   298                       |               466             |                          +56%
|         |   Hits By Country           |   363                       |               529             |                          +46%
|         |   Top 15 Organizations      |   244                       |               407             |                          +67%
|         |   Unique Count Organizations|   283                       |               403             |                          +42%
|         |   Unique IP Count           |   681                       |               823             |                          +21%
|         |   Total Hits Counts         |   200                       |               221             |                          +11%


NEED ## OF DOCUMENTS RETURNED TO JUSTIFY THIS DATA, OTHERWISE PERF FOR 2 REPLICAS LOOKS OFF!

PRESENT QUERY-ONLY TEST RESULTS TO SHOW BETTER RESULTS
-->

## Résultats de performance – Valeurs Doc

Pour ces tests d’ingestion et de requête, les valeurs Doc étaient activées. Elasticsearch stockait donc sur le disque les données utilisées pour le tri des champs. Les tests ont été répétés en désactivant les valeurs Doc. Elasticsearch construisait ainsi dynamiquement les données de champ et les mettait en mémoire cache. Tous les tests ont été exécutés pendant 24 heures.
 
 Le tableau ci-dessous compare les temps de réponse des tests exécutés sur des clusters à 6 nœuds créés avec des machines virtuelles D4, DS4 et DS14.

| Cluster |Opération/Requête |Valeurs Doc activées (ms) | Valeurs Doc désactivées (ms) | Différence en % |
|  --------- |---------------------------| -----------------------|--------------------------  |--------------------|
| D4 |Ingestion | 978 | 835 | -15 % |
| |Nombre par classement | 103 | 132 | +28 % |
| |Nombre au fil du temps | 134 | 189 | +41 % |
| |Résultats par pays | 199 | 259 | +30 % |
| |15 organisations les plus fréquentes | 137 | 184 | +34 % |
| |Nombre d’organisations différentes | 139 | 197 | +42 % |
| |Nombre d’IP différentes | 510 | 604 | +18 % |
| |Nombre total de résultats | 89 | 134 | +51 % |
| DS4 |Ingestion | 511 | 581 | +14 % |
| |Nombre par classement | 187 | 190 | +2 % |
| |Nombre au fil du temps | 411 | 409 | -0,5 % |
| |Résultats par pays | 402 | 414 | +3 % |
| |15 organisations les plus fréquentes | 307 | 284 | -7 % |
| |Nombre d’organisations différentes | 320 | 313 | -2 % |
| |Nombre d’IP différentes | 841 | 955 | +14 % |
| |Nombre total de résultats | 236 | 281 | +19 % |
| DS14 |Ingestion | 511 | 571 | +12 % |
| |Nombre par classement | 201 | 232 | +15 % |
| |Nombre au fil du temps | 298 | 341 | +14 % |
| |Résultats par pays | 363 | 457 | +26 % |
| |15 organisations les plus fréquentes | 244 | 338 | +39 % |
| |Nombre d’organisations différentes | 283 | 350 | +24 % |
| |Nombre d’IP différentes | 681 | 909 | +33 % |
| |Nombre total de résultats | 200 | 245 | +23 % |


<!--
The next table compares the number of ingestion operations performed by the tests:

  Cluster   Ingestion Operations – Doc Values Enabled   ## Ingestion Operations – Doc Values Disabled   % Difference in ## Ingestion Operations
  --------- ---------------------------------------------- ----------------------------------------------- -----------------------------------------
  D4        264769                                         408690                                          +54%
  DS4       503137                                         578237                                          +15%
  DS14      502714                                         586472                                          +17%

The improved ingestion rates occur with doc values disabled as less data is being written to disk as documents are inserted. The improved performance is especially noticeable with the D4 VM using HDDs to store data. In this case, the response time for ingestion operations also decreased by 15% (see the first table in this section). This could be due to the reduced pressure on the HDDs which were likely running close to their IOPS limits in the test with doc values enabled; see the [Disk Type](#performance-results-disk-type) test for more information. The following graph compares the I/O performance of the D4 VMs with doc values enabled (values held on disk) and doc values disabled (values held in memory):

![](./media/guidance-elasticsearch/query-performance4.png)

In contrast, the ingestion figures for the VMs using SSDs show a small increase in the number of documents but also an increase in the response time of the ingestion operations. With one or two small exceptions, the query response times were also worse. The SSDs are less likely to be running close to their IOPS limits with doc values enabled, so changes in performance are more likely due to increased processing activity and the overhead of managing the JVM heap. This is evident by comparing the CPU utilization with doc values enabled and disabled. The next graph highlights this data for the DS4 cluster, where most of the CPU utilization moves from the 30%-40% band with doc values enabled, to the 40%-50% band with doc values disabled (the DS14 cluster showed a similar trend):

![](./media/guidance-elasticsearch/query-performance5.png)

To isolate the effects that doc values on query performance from data ingestion, a pair of query-only tests were performed for each cluster as follows:

In the first test, the Elasticsearch index was created with doc values enabled, the index was pre-populated with 100 million documents, and then modified set of queries used by the replica tests were performed repeatedly for 90 minutes while performance statistics were gathered.

In the second test, the Elasticsearch index was created with doc values disabled, populated, and then subjected to the same query load for the same period as the first test.
-->

<!--
## Appendix: The Query and Aggregation Performance Test

This appendix describes the performance test performed against the Elasticsearch cluster. The tests were run by using JMeter running on a separate set of VMs. Details the configuration of the test environment are described in [Creating a Performance Testing Environment for Elasticsearch on Azure][]. To perform your own testing, you can create your own JMeter test plan manually following the guidance in this appendix, or you can use the automated test scripts available separately. See [Running the Automated Elasticsearch Performance Tests][] for further information.

The data query workload performed the set of queries described below while performing a large-scale upload of documents at the same time (the data was uploaded by using a JUnit test, following the same approach for the data ingestion tests described in the document Maximizing Data Ingestion Performance with Elasticsearch on Azure.) The purpose of this workload was to simulate a production environment where new data is constantly being added while searches are performed. The queries were structured to retrieve only the most recent data from documents added in the last 15 minutes.

Each document was stored in a single index named *sample*, and had the type *ctip*. You can use the following HTTP request to create the index. The *number\_of\_replicas* and *number\_of\_shards* settings varied from the values shown below in many of the tests. Additionally, for the tests that used fielddata rather than doc values, each property was annotated with the attribute *"doc\_values" : false*.

**Important**. The index was dropped and recreated prior to each test run.

```http
PUT /sample
{  
    "settings" : {
        "number_of_replicas": 1,
        "refresh_interval": "30s",
        "number_of_shards": "5",
        "index.translog.durability": "async"    
    },
    "ctip": {
        "mappings": {
            "event": {
                "_all": {
                    "enabled": false
                },
                "_timestamp": {
                    "enabled": true,
                    "store": true,
                    "format": "date_time"
                },
                "properties": {
                    "Organization": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField1": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField2": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField3": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField4": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField5": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "DateTimeReceivedUtc": {
                        "type": "date",
                        "format": "dateOptionalTime"
                    },
                    "Host": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpMethod": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpReferrer": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpRequest": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpUserAgent": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpVersion": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "OrganizationName": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIp": {
                        "type": "ip"
                    },
                    "SourceIpAreaCode": {
                        "type": "long"
                    },
                    "SourceIpAsnNr": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIpBase10": {
                        "type": "long"
                    },
                    "SourceIpCity": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIpCountryCode": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIpLatitude": {
                        "type": "double"
                    },
                    "SourceIpLongitude": {
                        "type": "double"
                    },
                    "SourceIpMetroCode": {
                        "type": "long"
                    },
                    "SourceIpPostalCode": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIpRegion": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceLatLong": {
                        "type": "geo_point",
                        "doc_values": true,
                        "lat_lon": true,
                        "geohash": true
                    },
                    "SourcePort": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourcedFrom": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "TargetIp": {
                        "type": "ip"
                    },
                    "TargetPort": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "Rating": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "UseHumanReadableDateTimes": {
                        "type": "boolean"
                    }
                }
            }
        }
    }
}

```

The following queries were performed by the test:

- How many documents with each *Rating* value have been entered in the last 15 minutes?

```http
GET /sample/ctip/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "range": {
            "DateTimeReceivedUtc": {
              "gte": "now-15m",
              "lte": "now"
            }
          }
        }
      ],
      "must_not": [],
      "should": []
    }
  },
  "from": 0,
  "size": 0,
  "aggs": {
    "2": {
      "terms": {
        "field": "Rating",
        "size": 5,
        "order": {
          "_count": "desc"
        }
      }
    }
  }
}

```

- How many documents have been added in each 5 minute interval during the last 15 minutes?

```http
GET /sample/ctip/_search 
{
  "query": {
    "bool": {
      "must": [
        {
          "range": {
            "DateTimeReceivedUtc": {
              "gte": "now-15m",
              "lte": "now"
            }
          }
        }
      ],
      "must_not": [],
      "should": []
    }
  },
  "from": 0,
  "size": 0,
  "sort": [],
  "aggs": {
    "2": {
      "date_histogram": {
        "field": "DateTimeReceivedUtc",
        "interval": "5m",
        "time_zone": "America/Los_Angeles",
        "min_doc_count": 1,
        "extended_bounds": {
          "min": "now-15m",
          "max": "now"
        }
      }
    }
  }
}

```

- How many documents of each *Rating* value have been added for each country in the last 15 minutes?

```http
GET /sample/ctip/_search 
{
  "query": {
    "filtered": {
      "query": {
        "query_string": {
          "query": "*",
          "analyze_wildcard": true
        }
      },
      "filter": {
        "bool": {
          "must": [
            {
              "query": {
                "query_string": {
                  "query": "*",
                  "analyze_wildcard": true
                }
              }
            },
            {
              "range": {
                "DateTimeReceivedUtc": {
                  "gte": "now-15m",
                  "lte": "now"
                }
              }
            }
          ],
          "must_not": []
        }
      }
    }
  },
  "size": 0,
  "aggs": {
    "2": {
      "terms": {
        "field": "Rating",
        "size": 5,
        "order": {
          "_count": "desc"
        }
      },
      "aggs": {
        "3": {
          "terms": {
            "field": "SourceIpCountryCode",
            "size": 15,
            "order": {
              "_count": "desc"
            }
          }
        }
      }
    }
  }
}

```

- Which 15 organizations occur most frequently in documents added in the last 15 minutes?

``` http
GET /sample/ctip/_search
{
  "query": {
    "filtered": {
      "query": {
        "query_string": {
          "query": "*",
          "analyze_wildcard": true
        }
      },
      "filter": {
        "bool": {
          "must": [
            {
              "query": {
                "query_string": {
                  "query": "*",
                  "analyze_wildcard": true
                }
              }
            },
            {
              "range": {
                "DateTimeReceivedUtc": {
                  "gte": "now-15m",
                  "lte": "now"
                }
              }
            }
          ],
          "must_not": []
        }
      }
    }
  },
  "size": 0,
  "aggs": {
    "2": {
      "terms": {
        "field": "Organization",
        "size": 15,
        "order": {
          "_count": "desc"
        }
      }
    }
  }
}

```

- How many different organizations occur in documents added in the last 15 minutes?

```http
GET /sample/ctip/_search
{
  "query": {
    "filtered": {
      "query": {
        "query_string": {
          "query": "*",
          "analyze_wildcard": true
        }
      },
      "filter": {
        "bool": {
          "must": [
            {
              "query": {
                "query_string": {
                  "query": "*",
                  "analyze_wildcard": true
                }
              }
            },
            {
              "range": {
                "DateTimeReceivedUtc": {
                  "gte": "now-15m",
                  "lte": "now"
                }
              }
            }
          ],
          "must_not": []
        }
      }
    }
  },
  "size": 0,
  "aggs": {
    "2": {
      "cardinality": {
        "field": "Organization"
      }
    }
  }
}

```

- How many documents have been added in the last 15 minutes?

```http
GET /sample/ctip/_search
{
  "query": {
    "filtered": {
      "query": {
        "query_string": {
          "query": "*",
          "analyze_wildcard": true
        }
      },
      "filter": {
        "bool": {
          "must": [
            {
              "query": {
                "query_string": {
                  "analyze_wildcard": true,
                  "query": "*"
                }
              }
            },
            {
              "range": {
                "DateTimeReceivedUtc": {
                  "gte": "now-15m",
                  "lte": "now"
                }
              }
            }
          ],
          "must_not": []
        }
      }
    }
  },
  "size": 0,
  "aggs": {}
}

```

- How many different *SourceIp* values occur in documents added in the last 15 minutes?

```http
GET /sample/ctip/_search
{
  "query": {
    "filtered": {
      "query": {
        "query_string": {
          "query": "*",
          "analyze_wildcard": true
        }
      },
      "filter": {
        "bool": {
          "must": [
            {
              "query": {
                "query_string": {
                  "query": "*",
                  "analyze_wildcard": true
                }
              }
            },
            {
              "range": {
                "DateTimeReceivedUtc": {
                  "gte": "now-15m",
                  "lte": "now"
                }
              }
            }
          ],
          "must_not": []
        }
      }
    }
  },
  "size": 0,
  "aggs": {
    "2": {
      "cardinality": {
        "field": "SourceIp"
      }
    }
  }
} 

```

[Running the Automated Elasticsearch Performance Tests]: guidance-elasticsearch-running-automated-performance-tests.md
[Creating a Performance Testing Environment for Elasticsearch on Azure]: guidance-elasticsearch-creating-performance-testing-environment.md
-->

<!---HONumber=AcomDC_0224_2016-->