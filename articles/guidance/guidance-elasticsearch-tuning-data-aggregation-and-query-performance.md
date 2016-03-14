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
   ms.date="02/29/2016"
   ms.author="masimms"/>
   
# Régler les performances de requête et d’agrégation des données avec Elasticsearch sur Azure


Cet article fait [partie d’une série](guidance-elasticsearch.md).

L’utilité principale d’Elasticsearch est la prise en charge des recherches parmi les données. Les utilisateurs doivent être en mesure de localiser rapidement les informations qu’ils cherchent. En outre, le système doit permettre aux utilisateurs d’envoyer des requêtes aux données, de rechercher des corrélations et de parvenir à des conclusions pouvant guider leurs décisions commerciales. Ce traitement est ce qui différencie les données des informations.

Ce document résume les options que vous pouvez envisager lorsque vous cherchez à déterminer la meilleure façon d’optimiser les performances des requêtes et de la recherche sur votre système.

Toutes les recommandations relatives aux performances dépendent largement des scénarios qui s’appliquent à votre situation, du volume de données que vous indexez et de la fréquence à laquelle les applications et les utilisateurs interrogent vos données. Vous devez soigneusement tester les résultats de toute modification de configuration ou de structure d’indexation à l’aide de vos propres données et charges de travail, afin d’évaluer les avantages pour vos scénarios spécifiques. À cette fin, ce document décrit également un certain nombre de tests qui ont été effectuées pour un scénario spécifique, implémenté à l’aide de différentes configurations. Vous pouvez adapter l’approche adoptée pour évaluer les performances de vos propres systèmes. Les détails de ces tests sont décrits dans l’[Annexe](#appendix-the-query-and-aggregation-performance-test).

## Considérations sur les performances de requête et les index

Cette section décrit certains facteurs courants que vous devez considérer lors de la conception d’index devant prendre en charge des requêtes et des recherches rapides.

### Le stockage de plusieurs types dans un index

Un index Elasticsearch peut contenir plusieurs types. Il est préférable d’éviter cette approche et de créer un index séparé pour chaque type. Observez les points suivants :

- Différents types peuvent spécifier différents analyseurs et il n’est pas toujours évident de savoir quel analyseur Elasticsearch doit utiliser si une requête est exécutée au niveau de l’index plutôt qu’au niveau du type. Pour plus d’informations, voir [Avoiding Type Gotchas](https://www.elastic.co/guide/en/elasticsearch/guide/current/mapping.html#_avoiding_type_gotchas) (Éviter les pièges de type).

- Les partitions d’index qui contiennent plusieurs types seront probablement plus volumineuses que celles des index qui contiennent un type unique. Plus une partition est volumineuse, plus l’effort requis par Elasticsearch pour filtrer les données lors de l’exécution des requêtes est important.

- S’il existe une différence significative entre les volumes de données pour les types, les informations pour un type peuvent être distribuées entre plusieurs partitions, ce qui réduit l’efficacité des recherches qui récupèrent ces données.

    ![](./media/guidance-elasticsearch/query-performance1.png)

    ***Figure 1. Les effets du partage d’un index entre les types***

    La figure 1 illustre ce scénario. Dans la partie supérieure du diagramme, le même index est partagé par des documents de type A et de type B. Il existe bien plus de documents de type A que de type B. Les recherches pour le type A impliquent l’interrogation des quatre partitions. La partie inférieure du diagramme illustre le scénario selon lequel différents index sont créés pour chaque type. Dans ce cas, les recherches pour le type A ne nécessitent un accès qu’à deux partitions.

- Les petites partitions peuvent être plus équitablement distribuées que les partitions volumineuses. Il est ainsi plus facile pour Elasticsearch de répartir la charge sur les nœuds.

- Chaque type peut avoir une période de rétention différente. Il peut être difficile d’archiver les anciennes données qui partagent des partitions avec des données actives.


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

<!-- -->

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

    However, this search will fail to return the expected results due to the way in which the string ABC-DEF is tokenized when it is indexed; it will be effectively split into two tokens, ABC and DEF, by the hyphen. This feature is designed to support full text searching, but if you want the string to be interpreted as a single atomic item you should disable tokenization when the document is added to the index. You can use a mapping such as this:

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

### Utilisation de réplicas pour réduire les conflits de requête

Une stratégie courante pour améliorer les performances des requêtes consiste à créer plusieurs réplicas de chaque index. Les opérations de récupération de données peuvent être effectuées en extrayant les données à partir d’un réplica. Toutefois, cette stratégie peut sérieusement affecter les performances des opérations d’ingestion de données. Elle doit donc être utilisée avec précaution dans les scénarios qui impliquent des charges de travail mixtes. En outre, cette stratégie n’est avantageuse que si les réplicas sont répartis entre les nœuds et ne se disputent pas les ressources avec les partitions principales qui font partie du même index. N’oubliez pas qu’il est possible d’augmenter ou de diminuer dynamiquement le nombre de réplicas d’un index.

### Utilisation du cache de demande de la partition

Elasticsearch peut mettre en cache dans la mémoire les données locales demandées par les requêtes sur chaque partition. Cela permet aux recherches qui récupèrent les mêmes données de s’exécuter plus rapidement. Les données peuvent ainsi être lues à partir de la mémoire plutôt que du disque. La mise en cache des données de cette manière permet donc d’améliorer les performances de certaines opérations de recherche, mais cela réduit également la mémoire disponible pour les autres tâches exécutées simultanément. Il existe également un risque que les données récupérées à partir du cache soient obsolètes. Les données dans le cache sont seulement invalidées lorsque la partition est actualisée et que les données ont changé. La fréquence d’actualisation est régie par la valeur du paramètre *refresh\_interval* de l’index.

Le cache de demande pour un index est désactivé par défaut, mais vous pouvez l’activer comme suit :

```http
PUT /myindex/_settings
{
  "index.requests.cache.enable": true
}
```

Le cache de demande de la partition est plus approprié pour les informations relativement statiques, telles que des données d’historique ou de journalisation.

### Utilisation de nœuds client

Toutes les requêtes sont traitées par le nœud qui reçoit la demande en premier. Ce nœud envoie ensuite des requêtes à tous les autres nœuds contenant des partitions correspondant aux index interrogés, puis regroupe les résultats pour retourner la réponse. Si une requête implique l’agrégation de données ou des calculs complexes, le nœud initial est chargé d’effectuer le traitement approprié. Si votre système ne doit prendre en charge qu’un petit nombre de requêtes complexes, pensez à créer un pool de nœuds client pour alléger la charge des nœuds de données. À l’inverse, si votre système doit gérer un grand nombre de requêtes simples, envoyez ces requêtes directement aux nœuds de données et utilisez un équilibreur de charge pour répartir les requêtes équitablement.

### Régler les requêtes

Les conseils pour optimiser les performances des requêtes Elasticsearch sont résumés ci-dessous :

- Évitez les requêtes impliquant des caractères génériques lorsque cela est possible.

- Si le même champ est soumis à une recherche de texte intégral et de correspondance exacte, envisagez de stocker les données pour le champ dans les formulaires analysés et non analysés. Effectuez des recherches de texte intégral dans le champ analysé et des recherches de correspondance exacte dans le champ non analysé.

- Retournez seulement les données nécessaires. Si les documents sont volumineux, mais qu’une application requiert seulement les informations contenues dans un sous-ensemble des champs, retournez ce sous-ensemble à partir des requêtes plutôt que des documents entiers. Cette stratégie peut réduire les besoins en bande passante réseau du cluster.

- Chaque fois que possible, utilisez des filtres plutôt que des requêtes lorsque vous recherchez des données. Un filtre détermine simplement si un document correspond à un critère donné, tandis qu’une requête calcule également dans quelle mesure un document correspond (score). En interne, les valeurs générées par un filtre sont stockées sous la forme d’un bitmap indiquant s’il existe ou non des correspondances pour chaque document. Elles peuvent ensuite être mises en cache par Elasticsearch. Si le même critère de filtre se produit par la suite, il est possible de récupérer le bitmap à partir du cache et de l’utiliser pour extraire rapidement les documents correspondants. Pour plus d’informations, voir [Internal Filter Operation](https://www.elastic.co/guide/en/elasticsearch/guide/current/_finding_exact_values.html#_internal_filter_operation) (Opération de filtre interne).

- Utilisez les filtres *bool* pour effectuer des comparaisons statiques et utilisez uniquement les filtres *and* *or* et *not* pour les filtres calculés dynamiquement, telles que ceux impliquant des scripts ou des filtres *geo-**.

- Si une requête combine des filtres *bool* avec *and*, *or* ou *not* avec des filtres *geo-**, placez les filtres *and*/*or*/*not geo-** en dernier pour qu’ils fonctionnent sur le plus petit jeu de données possible.

    De même, utilisez un filtre *post\_filter* pour exécuter les opérations de filtre coûteuses. Ces filtres sont exécutés en dernier.

- Utilisez des agrégations plutôt que des facettes. Évitez de calculer des agrégats qui sont analysés ou qui ont de nombreuses valeurs possibles.

    > **Remarque** : les facettes ont été supprimées dans Elasticsearch version 2.0.0.

- Utilisez l’agrégation *cardinality* de préférence à l’agrégation *value\_count* sauf si votre application nécessite un nombre exact d’éléments correspondants. Un nombre exact devient rapidement obsolète, et de nombreuses applications nécessitent seulement une approximation raisonnable.

- Évitez les scripts. Les scripts dans les requêtes et les filtres peuvent être coûteux. En outre, les résultats ne sont pas mis en cache. Les scripts longs peuvent consommer des threads de recherche indéfiniment, provoquant la mise en file d’attente des requêtes suivantes. Si la file d’attente est remplie, les autres requêtes sont rejetées.

## Test et analyse des performances d’agrégation et de recherche

Cette section décrit les résultats d’une série de tests effectués sur des configurations de cluster et d’index variables. Deux types de tests ont été effectués, comme suit :

- **Le test *ingestion et requête***. Ce test a démarré avec un index vide qui s’est rempli pendant le test via des opérations d’insertion en bloc (chaque opération ajoutait 1 000 documents). Pendant ce temps, plusieurs requêtes conçues pour rechercher des documents ajoutés pendant les 15 minutes précédentes et pour générer des agrégations ont été répétées toutes les 5 secondes. Ce test pouvait généralement s’exécuter pendant 24 heures, pour reproduire les effets d’une charge de travail importante impliquant l’ingestion de données à grande échelle avec des requêtes quasiment en temps réel.

- **Le test *requête seulement***. Ce test est similaire au test *ingestion et requête* sauf que la partie ingestion est omise et que l’index sur chaque nœud est prérempli avec 100 millions de documents. Un ensemble de requêtes modifié est exécuté ; l’élément de durée limitant les documents à ceux ajoutés au cours des 15 dernières minutes a été supprimé car les données sont désormais statiques. Les tests se sont déroulés pendant 90 minutes ; l’établissement d’un modèle de performance prend moins de temps en raison de la quantité fixe de données.

---

Tous les documents de l’index avaient le même schéma. Le tableau suivant résume les champs du schéma :

Nom | Type | Remarques |
  ----------------------------- | ------------ | -------------------------------------------------------- |
 Organisation | Chaîne | Le test génère 200 organisations uniques. |
 CustomField1 - CustomField5 |String |Il s’agit de cinq champs de type string qui sont définis sur la chaîne vide.|
 DateTimeRecievedUtc |Timestamp |La date et l’heure d’ajout du document.|
 Hôte |String |Ce champ est défini sur la chaîne vide.|
 HttpMethod |Chaîne |Ce champ est défini sur l’une des valeurs suivantes : « POST », « GET », « PUT ».|
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
 SourcedFrom |Chaîne |Ce champ est défini sur la chaîne « MonitoringCollector ».|
 TargetPort |Chaîne |Ce champ est rempli avec la représentation sous forme de chaîne d’un nombre aléatoire.|
 Rating |Chaîne |Ce champ est rempli avec l’une des 20 valeurs de chaîne différentes sélectionnée au hasard.|
 UseHumanReadableDateTimes |Boolean |Ce champ est défini sur false.|
 
Les requêtes suivantes ont été exécutées en lot par chaque itération des tests. Les noms en italiques sont utilisés pour faire référence à ces requêtes dans le reste de ce document. Notez que le critère de durée (documents ajoutés au cours des 15 dernières minutes) a été omis dans les tests *requête seulement* :

- Combien de documents avec chaque valeur *Rating* ont été ajoutés au cours des 15 dernières minutes (*Nombre par classement*) ?

- Combien de documents ont été ajoutés pendant chaque intervalle de 5 minutes au cours des 15 dernières minutes (*Nombre au fil du temps*) ?

- Combien de documents de chaque valeur *Rating* ont été ajoutés pour chaque pays au cours des 15 dernières minutes (*Résultats par pays*) ?

- Quelles sont les 15 organisations qui apparaissent le plus souvent dans les documents ajoutés au cours des 15 dernières minutes (*15 organisations les plus fréquentes*) ?

- Combien d’organisations différentes apparaissent dans les documents ajoutés au cours des 15 dernières minutes (*Nombre d’organisations différentes*) ?

- Combien de documents ont été ajoutés au cours des 15 dernières minutes (*Nombre total de résultats*) ?

- Combien de valeurs *SourceIp* différentes apparaissent dans les documents ajoutés au cours des 15 dernières minutes (*Nombre d’IP différentes*) ?


La définition de l’index et les détails des requêtes sont décrits dans l’[annexe](#appendix-the-query-and-aggregation-performance-test).

Les tests ont été conçus pour comprendre les effets des variables suivantes :

- **Type de disque**. Les tests ont été exécutés sur un cluster à 6 nœuds de machines virtuelles D4 utilisant un stockage standard (HDD), puis répété sur un cluster à 6 nœuds de machines virtuelles DS4 utilisant un stockage premium (SSD).

- **Mise à l’échelle de la taille des machines**. Les tests ont été effectués sur un cluster à 6 nœuds comprenant des machines virtuelles DS3 (le *petit* cluster), puis répété sur un cluster de machines virtuelles DS4 (le cluster *moyen*), puis encore répété sur un cluster de machines virtuelles DS14 (le *grand* cluster). Le tableau suivant récapitule les principales caractéristiques de chaque référence (SKU) de machine virtuelle :

 Cluster | Référence de la machine virtuelle | Nombre de cœurs | Nombre de disques de données | RAM (Go) |
---------|---------------|-----------------|----------------------|----------|
 Petite | DS3 standard | 4 | 8 | 14 |
 Moyenne | DS4 standard | 8 | 16 | 28 |
 Grande | DS14 standard | 16 | 32 | 112 |

- **Taille du cluster - Augmentation de puissance**. Les tests ont été effectués sur des clusters de machines virtuelles DS14 comprenant 1, 3 et 6 nœuds.

- **Nombre de réplicas d’index**. Les tests ont été effectués à l’aide d’index configurés avec 1 et 2 réplicas.

- **Valeurs Doc**. Initialement, les tests ont été effectués en définissant le paramètre d’index *doc\_values* sur *true* (la valeur par défaut) Les tests sélectionnés ont été répétés avec la valeur *false* pour le paramètre *doc\_values*.

- **Mise en cache**. Les tests ont été réalisés avec le cache de demande de la partition activé sur l’index.

- **Nombre de partitions**. Les tests ont été répétés avec différentes quantités de partitions pour déterminer si les requêtes s’exécutaient plus efficacement sur les index contenant des partitions plus grandes et moins nombreuses ou des partitions plus nombreuses et plus petites.


## Résultats de performance - Type de disque

La performance du disque a été évaluée en exécutant le test *ingestion et requête* sur le cluster à 6 nœuds de machines virtuelles D4 (HDD) et sur le cluster à 6 nœuds de machines virtuelles DS4 (SSD). La configuration d’Elasticsearch dans les deux clusters était la même. Les données étaient réparties sur 16 disques sur chaque nœud et chaque nœud disposait de 14 Go de mémoire RAM alloués à la machine virtuelle Java exécutant Elasticsearch. La mémoire restante (14 Go également) était dédiée au fonctionnement du système d’exploitation. Chaque test a été exécuté pendant 24 heures. Cette durée a été définie de manière à ce que la hausse du volume des données ait le temps de se faire sentir et que le système se stabilise. Le tableau ci-dessous récapitule les résultats, notamment les temps de réponse des différentes opérations constituant le test.

 Cluster | Opération/Requête | Temps de réponse moyen (en ms) |
---------|----------------------------|----------------------------|
 D4 | Ingestion | 978 |
 | Nombre par classement | 103 |
 | Nombre au fil du temps | 134 |
 | Résultats par pays | 199 |
 | 15 organisations les plus fréquentes | 137 |
 | Nombre d’organisations différentes | 139 |
 | Nombre d’IP différentes | 510 |
 | Nombre total de résultats | 89
 DS4 | Ingestion | 511 |
 | Nombre par classement | 187 |
 | Nombre au fil du temps | 411 |
 | Résultats par pays | 402 |
 | 15 organisations les plus fréquentes | 307 |
 | Nombre d’organisations différentes | 320 |
 | Nombre d’IP différentes | 841 |
 | Nombre total de résultats | 236 |

À première vue, il semblerait que le cluster DS4 effectue les requêtes de façon moins efficace que le cluster D4. Le temps de réponse est parfois deux fois supérieur (voire plus). Mais ces données ne suffisent pas à juger réellement de leurs performances respectives. Le tableau suivant indique le nombre d’opérations d’ingestion effectuées par chaque cluster (n’oubliez pas que 1 000 documents sont chargés à chaque opération) :

 Cluster | Opérations d’ingestion |
---------|-------------------------|
 D4 | 264769 |
 DS4 | 503157 |

Le cluster DS4 a pu charger presque deux fois plus de données que le cluster D4 pendant le test. Par conséquent, lorsque vous analysez le temps de réponse pour chaque opération, vous devez également prendre en compte le nombre de documents à analyser à chaque requête, ainsi que le nombre de documents retournés. Il s’agit de chiffres dynamiques, car le volume de documents dans l’index croît en continu. Vous ne pouvez pas simplement diviser 503 137 par 264 769 (le nombre d’opérations d’ingestion effectuées par chaque cluster) puis multiplier le résultat par le temps de réponse moyen pour chaque requête effectuée par le cluster D4 pour obtenir une valeur comparative, car ce serait ignorer la quantité d’opérations d’E/S exécutées simultanément par l’opération d’ingestion. Vous devez donc mesurer la quantité de données écrites sur le disque et lues à partir du disque au cours du test. Le plan de test JMeter permet de capturer ces informations pour chaque nœud. Voici un résumé des résultats :

 Cluster | Moyenne des octets écrits/lus par chaque opération |
---------|----------------------------------------------|
 D4 | 13471557 |
 DS4 | 24643470 |

Ces données montrent que le cluster DS4 a été en mesure de maintenir un taux d’E/S environ 1,8 fois supérieur à celui du cluster D4. Vu qu’en dehors des disques toutes les ressources sont identiques, la différence doit être liée à l’utilisation de disques SSD à la place des disques durs.

Pour appuyer cette conclusion, les graphiques suivants illustrent la façon dont les opérations d’E/S ont été effectuées au fil du temps par chaque cluster :

![](./media/guidance-elasticsearch/query-performance2.png)

<!-- -->

***Figure 2. Activité du disque des clusters D4 et DS4***

Le graphique du cluster D4 affiche des variations importantes, en particulier lors de la première partie du test. Ce phénomène est probablement dû à la limitation qui diminue le taux d’E/S. Lors des phases initiales du test, les requêtes s’exécutent rapidement, car il n’existe que peu de données à analyser. Les disques du cluster D4 peuvent donc fonctionner près de leur seuil maximal d’E/S par seconde, même si chaque opération d’E/S ne retourne pas beaucoup de données. Le cluster DS4 peut prendre en charge des taux d’E/S supérieurs et ne souffre donc pas du même niveau de limitation. Les taux d’E/S sont plus réguliers. Pour appuyer cette théorie, les deux graphiques qui suivent montrent comment l’unité centrale a été bloquée par les E/S de disque au fil du temps (les délais d’attente du disque indiqués dans les graphiques sont un pourcentage du temps que l’unité centrale a passé à attendre les E/S) :

![](./media/guidance-elasticsearch/query-performance3.png)

***Figure 3. Temps d’attente d’E/S du disque/de l’unité centrale des clusters D4 et DS4***

Il est important de comprendre qu’il existe deux raisons principales pour lesquelles les opérations d’E/S bloquent l’unité centrale :

- Le sous-système d’E/S peut être en train de lire des données à partir du disque ou d’écrire des données sur le disque.

- Le sous-système d’E/S peut être limité par l’environnement hôte. Les disques Azure implémentés à l’aide des disques durs ont un débit maximal de 500 E/S par seconde et les disques SSD ont un débit maximal de 5 000 E/S par seconde.


Pour le cluster D4, le temps passé à attendre les E/S durant la première moitié du test correspond de façon inversée aux données du graphique indiquant les taux d’E/S. En effet, les périodes où le taux d’E/S est bas correspondent aux périodes où l’unité centrale est le plus souvent bloquée. Cela indique que les opérations d’E/S sont limitées. La situation change à mesure que le volume de données ajoutées au cluster augmente. Durant la deuxième partie du test, les pics d’attente correspondent aux pics de débit d’E/S par seconde. À ce stade, l’unité centrale est bloquée lors de l’exécution d’opérations d’E/S réelles. Là encore, avec le cluster DS4, le temps d’attente est beaucoup plus uniforme et chaque pic correspond non pas à un creux, mais à un pic équivalent dans les performances d’E/S. Cela signifie qu’il n’y a pas (ou peu) de limitation.

Il existe un autre facteur à prendre en compte. Pendant le test, le cluster D4 a généré 10 584 erreurs d’ingestion et 21 erreurs de requête. Aucune erreur ne s’est produite lors du test sur le cluster DS4.

## Résultats de performance - Diminution de puissance

Le test de mise à l’échelle a été effectué en exécutant des tests sur les clusters 6 nœuds des machines virtuelles DS3, DS4 et DS14. Ces références ont été sélectionnées parce qu’une machine virtuelle DS4 offre deux fois plus de cœurs d’unité centrale et de mémoire qu’une machine DS3, et une machine DS14 double encore les ressources de l’unité centrale tout en fournissant quatre fois plus de mémoire. Le tableau ci-dessous compare les principaux aspects de chaque référence :

 SKU | Nb de cœurs d’unité centrale | Mémoire (Go) | E/S par seconde maximale du disque | Bande passante maximale (Mo/s)|
------|-------------|-------------|---------------|--------------|
 DS3 | 4 | 14 | 12 800| 128 |
 DS4 | 8 | 28 | 25 600| 256 |
 DS14 | 16 | 112 | 50 000| 512 |

Le tableau suivant récapitule les résultats des tests effectués sur le petit cluster (DS3), le cluster moyen (DS4) et le grand cluster (DS14). Chaque machine virtuelle utilise des disques SSD pour contenir les données. Chaque test a été exécuté pendant 24 heures :

> **Remarque** : le tableau indique le nombre de demandes réussies pour chaque type de requête (les échecs ne sont pas inclus). Le nombre de tentatives de demande pour chaque type de requête est à peu près le même pendant l’exécution du test. C’est parce que le plan de test JMeter exécute une seule occurrence de chaque requête (Nombre par classement, Nombre au fil du temps, Résultats par pays, 15 organisations les plus fréquentes, Nombre d’organisations différentes, Nombre d’IP différentes et Nombre total de résultats) dans une seule unité appelée *transaction de test* (cette transaction est indépendante de la tâche qui effectue l’opération d’ingestion, qui est exécutée par un thread distinct). Chaque itération du plan de test effectue une transaction de test unique. Le nombre de transactions de test terminées est donc une mesure du temps de réponse de la requête la plus lente dans chaque transaction.

| Cluster | Opération/Requête | Nombre de requêtes | Temps de réponse moyen (en ms) |
|--------------|----------------------------|--------------------|----------------------------|
| Petit (DS3) | Ingestion | 207284 | 3328 |
| | Nombre par classement | 18444 | 268 |
| | Nombre au fil du temps | 18444 | 340 |
| | Résultats par pays | 18445 | 404 |
| | 15 organisations les plus fréquentes | 18439 | 323 |
| | Nombre d’organisations différentes | 18437 | 338 |
| | Nombre d’IP différentes | 18442 | 468 |
| | Nombre total de résultats | 18428 | 294   
|||||
| Moyen (DS4) | Ingestion | 503157 | 511 |
| | Nombre par classement | 6958 | 187 |
| | Nombre au fil du temps | 6958 | 411 |
| | Résultats par pays | 6958 | 402 |
| | 15 organisations les plus fréquentes | 6958 | 307 |
| | Nombre d’organisations différentes | 6956 | 320 |
| | Nombre d’IP différentes | 6955 | 841 |
| | Nombre total de résultats | 6958 | 236 |
|||||
| Grand (DS14) | Ingestion | 502714 | 511 |
| | Nombre par classement | 7041 | 201 |
| | Nombre au fil du temps | 7040 | 298 |
| | Résultats par pays | 7039 | 363 |
| | 15 organisations les plus fréquentes | 7038 | 244 |
| | Nombre d’organisations différentes | 7037 | 283 |
| | Nombre d’IP différentes | 7037 | 681 |
| | Nombre total de résultats | 7038 | 200 |

Ces données montrent que, pour ce test, les performances des clusters DS4 et DS14 étaient relativement similaires. Les temps de réponse des opérations de requête du cluster DS3 semblent également être meilleurs que les autres, et le nombre d’opérations de requête exécutées est bien supérieur aux valeurs des clusters DS4 et DS14. Toutefois, il faut aussi tenir compte du taux d’ingestion et du nombre consécutif de documents recherchés. Dans le cluster DS3, l’ingestion est bien plus limitée. À la fin du test, la base de données contenait seulement 40 % environ des documents lus par chacun des deux autres clusters. Cela peut être dû aux ressources de traitement, au réseau et à la bande passante disque disponible pour une machine virtuelle DS3 par rapport à une machine virtuelle DS14 ou DS4. Étant donné qu’une machine virtuelle DS4 a deux fois plus de ressources disponibles qu’une machine virtuelle DS3 et qu’une machine virtuelle DS14 a deux fois plus (quatre fois pour la mémoire) de ressources qu’une machine virtuelle DS4, une question demeure : pourquoi la différence de taux d’ingestion des clusters DS4 et DS14 est-elle considérablement inférieure à celle constatée entre les clusters DS3 et DS4 ? Cela peut être dû à l’utilisation du réseau et aux limites de bande passante des machines virtuelles Azure. Les graphiques suivants montrent ces données pour les trois clusters :

![](./media/guidance-elasticsearch/query-performance4.png)

***Figure 4. Utilisation du réseau pour les clusters DS3, DS4 et DS14 exécutant le test *ingestion et requête****

<!-- -->

Les limites de bande passante réseau disponible avec les machines virtuelles Azure ne sont pas publiées et peuvent varier, mais le fait que l’activité réseau semble s’être stabilisée à une moyenne d’environ 2,75 Gbit/s lors des tests du DS4 et du DS14 signifie qu’une telle limite a été atteinte et est devenue le principal facteur de limitation du débit. Dans le cas du cluster DS3, l’activité réseau a été considérablement inférieure, si bien que cette diminution des performances est probablement due aux limites de disponibilité des autres ressources.

Pour identifier les effets des opérations d’ingestion et illustrer la manière dont les performances de requête évoluent à mesure que les nœuds évoluent, un ensemble de tests « requête seulement » a été effectué en utilisant les mêmes nœuds. Le tableau suivant récapitule les résultats obtenus sur chaque cluster :

> [AZURE.NOTE] Vous ne devez pas comparer les performances et le nombre de demandes exécutées par les requêtes dans le test *requête seulement* avec celles exécutées par le test *ingestion et requête*. En effet, les requêtes ont été modifiées et le volume des documents impliqués est différent.

| Cluster | Opération/Requête | Nombre de requêtes | Temps de réponse moyen (en ms) |
|--------------|----------------------------|--------------------|----------------------------|
| Petit (DS3) | Nombre par classement | 464 | 11758 |
| | Nombre au fil du temps | 464 | 14699 |
| | Résultats par pays | 463 | 14075 |
| | 15 organisations les plus fréquentes | 464 | 11856 |
| | Nombre d’organisations différentes | 462 | 12314 |
| | Nombre d’IP différentes | 461 | 19898 |
| | Nombre total de résultats | 462 | 8882  
|||||
| Moyen (DS4) | Nombre par classement | 1045 | 4489 |
| | Nombre au fil du temps | 1045 | 7292 |
| | Résultats par pays | 1053 | 7564 |
| | 15 organisations les plus fréquentes | 1055 | 5066 |
| | Nombre d’organisations différentes | 1051 | 5231 |
| | Nombre d’IP différentes | 1051 | 9228 |
| | Nombre total de résultats | 1051 | 2180 |
|||||
| Grand (DS14) | Nombre par classement | 1842 | 1927 |
| | Nombre au fil du temps | 1839 | 4483 |
| | Résultats par pays | 1838 | 4761 |
| | 15 organisations les plus fréquentes | 1842 | 2117 |
| | Nombre d’organisations différentes | 1837 | 2393 |
| | Nombre d’IP différentes | 1837 | 7159 |
| | Nombre total de résultats | 1837 | 642 |

Cette fois, les tendances des temps de réponse moyens des différents clusters sont plus claires. L’utilisation du réseau est bien inférieure aux 2,75 Gbit/s requis précédemment par les clusters DS4 et DS14 (qui ont probablement saturé le réseau dans les tests « ingestion et requête ») et au 1,5 Gbit/s pour le cluster DS3. En fait, elle est plus proche de 200 Mbit/s dans tous les cas, comme indiqué par les graphiques suivants :

![](./media/guidance-elasticsearch/query-performance5.png)

***Figure 5. Utilisation du réseau pour les clusters DS3, DS4 et DS14 exécutant le test *requête seulement****

Le facteur de limitation des clusters DS3 et DS4 semble désormais être l’utilisation de l’unité centrale, qui approche les 100 % la plupart du temps. Dans le cluster DS14, l’utilisation moyenne de l’unité centrale est légèrement supérieure à 80 %. Cela reste élevé, mais démontre clairement les avantages liés à la disponibilité de cœurs d’unité centrale supplémentaires. L’illustration suivante montre les modèles d’utilisation de l’unité centrale pour les clusters DS3, DS4 et DS14.

![](./media/guidance-elasticsearch/query-performance6.png)

***Figure 6. Utilisation de l’unité centrale pour les clusters DS3 et DS14 exécutant le test *requête seulement****

## Résultats de performance - Augmentation de puissance

Pour illustrer la façon dont le système peut évoluer avec le nombre de nœuds, les tests ont été exécutés en utilisant des clusters DS14 comprenant 1, 3 et 6 nœuds. Cette fois, seul le test *requête seulement* a été effectué, à l’aide de 100 millions de documents et pendant 90 minutes :

> [AZURE.NOTE] Pour des informations sur la manière dont le déploiement peut affecter le comportement des opérations d’ingestion des données, voir le document [Optimisation des performances d’ingestion de données grâce à Elasticsearch dans Azure](https://github.com/mspnp/azure-guidance/blob/master/Elasticsearch-Data-Ingestion-Performance.md).

| Cluster | Opération/Requête | Nombre de requêtes | Temps de réponse moyen (en ms) |
|---------|----------------------------|--------------------|----------------------------|
| 1 nœud | Nombre par classement | 288 | 6216 |
| | Nombre au fil du temps | 288 | 28933 |
| | Résultats par pays | 288 | 29455 |
| | 15 organisations les plus fréquentes | 288 | 9058 |
| | Nombre d’organisations différentes | 287 | 19916 |
| | Nombre d’IP différentes | 284 | 54203 |
| | Nombre total de résultats | 287 | 3333 |
|||||
| 3 nœuds | Nombre par classement | 1194 | 3427 |
| | Nombre au fil du temps | 1194 | 5381 |
| | Résultats par pays | 1191 | 6840 |
| | 15 organisations les plus fréquentes | 1196 | 3819 |
| | Nombre d’organisations différentes | 1190 | 2938 |
| | Nombre d’IP différentes | 1189 | 12516 |
| | Nombre total de résultats | 1191 | 1272 |
|||||
| 6 nœuds | Nombre par classement | 1842 | 1927 |
| | Nombre au fil du temps | 1839 | 4483 |
| | Résultats par pays | 1838 | 4761 |
| | 15 organisations les plus fréquentes | 1842 | 2117 |
| | Nombre d’organisations différentes | 1837 | 2393 |
| | Nombre d’IP différentes | 1837 | 7159 |
| | Nombre total de résultats | 1837 | 642 |

Le nombre de nœuds crée une différence significative dans les performances des requêtes du cluster, bien que celles-ci ne soient pas linéaires ; le cluster à 3 nœuds termine environ 4 fois plus de requêtes que le cluster à 1 nœud, tandis que le cluster à 6 nœuds en gère 6 fois plus. Pour expliquer cette non-linéarité, les graphiques suivants montrent comment l’unité centrale a été utilisée par les trois clusters :

![](./media/guidance-elasticsearch/query-performance7.png)

***Figure 7. Utilisation de l’unité centrale pour les clusters à 1, 3 et 6 nœuds exécutant le test *requête seulement****

Les clusters à 1 nœud et à 3 nœuds sont liés à l’unité centrale. Toutefois, même si l’utilisation de l’unité centrale est élevée dans le cluster à 6 nœuds, une capacité de traitement supplémentaire est disponible. Dans ce cas, d’autres facteurs sont susceptibles de limiter le débit. Cela peut être vérifié en effectuant un test avec 9 et 12 nœuds, qui révélerait probablement une capacité de traitement supplémentaire disponible.

Les données du tableau ci-dessus montrent également de quelle manière les temps de réponse moyens des requêtes varient. Ceci est l’élément qui fournit le plus d’informations lorsqu’on teste la manière dont un système évolue pour des types spécifiques de requête ; certaines recherches sont clairement beaucoup plus efficaces lorsqu’elles couvrent plus de nœuds que d’autres. Cela peut être dû au ratio entre le nombre de nœuds et le nombre de documents croissant dans le cluster ; chaque cluster contenait 100 millions de documents. Lorsque vous effectuez des recherches qui impliquent l’agrégation de données, Elasticsearch traite et met en mémoire tampon les données récupérées dans le cadre du processus d’agrégation en mémoire sur chaque nœud. S’il y a plus de nœuds, il y a moins de données à récupérer, à mettre en mémoire tampon et à traiter sur chaque nœud.

## Résultats de performances - Nombre de réplicas

Les tests *ingestion et requête* ont été exécutés sur un index avec un seul réplica. Les tests ont été répétés sur les clusters DS4 et DS14 à 6 nœuds à l’aide d’un index configuré avec deux réplicas. Tous les tests ont été exécutés pendant 24 heures. Le tableau ci-dessous montre les résultats comparatifs pour un ou deux réplicas :

| Cluster | Opération/Requête | Temps de réponse moyen (en ms) - 1 réplica | Temps de réponse moyen (en ms) - 2 réplicas | % d’écart dans les temps de réponse |
|---------|----------------------------|----------------------------------------|-----------------------------------------|-------------------------------|
| DS4 | Ingestion | 511 | 655 | +28 % |
| | Nombre par classement | 187 | 168 | -10 % |
| | Nombre au fil du temps | 411 | 309 | -25 % |
| | Résultats par pays | 402 | 562 | +40 % |
| | 15 organisations les plus fréquentes | 307 | 366 | +19 % |
| | Nombre d’organisations différentes | 320 | 378 | +18 % |
| | Nombre d’IP différentes | 841 | 987 | +17 % |
| | Nombre total de résultats | 236 | 236 | +0 % |
||||||
| DS14 | Ingestion | 511 | 618 | +21 % |
| | Nombre par classement | 201 | 275 | +37 % |
| | Nombre au fil du temps | 298 | 466 | +56 % |
| | Résultats par pays | 363 | 529 | +46 % |
| | 15 organisations les plus fréquentes | 244 | 407 | +67 % |
| | Nombre d’organisations différentes | 283 | 403 | +42 % |
| | Nombre d’IP différentes | 681 | 823 | +21 % |
| | Nombre total de résultats | 200 | 221 | +11 % |

Le taux d’ingestion a diminué alors que le nombre de réplicas a augmenté. Cela n’est pas surprenant car Elasticsearch écrit plus de copies de chaque document, générant des E/S de disque supplémentaires. On peut le constater dans les graphiques du cluster DS14 pour les index avec 1 et 2 réplicas illustrés ci-dessous. Dans le cas de l’index avec 1 réplica, le taux moyen d’E/S était 16 896 573 octets par seconde. Pour l’index avec 2 réplicas, le taux moyen d’E/S était 33 986 843 octets par seconde, soit deux fois plus.

![](./media/guidance-elasticsearch/query-performance8.png)

***Figure 8. Taux d’E/S du disque pour les nœuds avec 1 et 2 réplicas effectuant le test *ingestion et requête****

| Cluster | Interroger | Temps de réponse moyen (en ms) - 1 réplica | Temps de réponse moyen (en ms) - 2 réplicas |
|---------|----------------------------|----------------------------------------|-----------------------------------------|
| DS4 | Nombre par classement | 4489 | 4079 |
| | Nombre au fil du temps | 7292 | 6697 |
| | Résultats par pays | 7564 | 7173 |
| | 15 organisations les plus fréquentes | 5066 | 4650 |
| | Nombre d’organisations différentes | 5231 | 4691 |
| | Nombre d’IP différentes | 9228 | 8752 |
| | Nombre total de résultats | 2180 | 1909 |
|||||
| DS14 | Nombre par classement | 1927 | 2330 |
| | Nombre au fil du temps | 4483 | 4381 |
| | Résultats par pays | 4761 | 5341 |
| | 15 organisations les plus fréquentes | 2117 | 2560 |
| | Nombre d’organisations différentes | 2393 | 2546 |
| | Nombre d’IP différentes | 7159 | 7048 |
| | Nombre total de résultats | 642 | 708 |

Ces résultats indiquent une amélioration du temps de réponse moyen pour le cluster DS4, mais une augmentation pour le cluster DS14. Pour vous aider à interpréter ces résultats, vous devez également prendre en compte le nombre de requêtes exécutées par chaque test :

| Cluster | Interroger | Nombre exécuté - 1 réplica | Nombre exécuté - 2 réplicas |
|---------|----------------------------|------------------------------|-------------------------------|
| DS4 | Nombre par classement | 1054 | 1141 |
| | Nombre au fil du temps | 1054 | 1139 |
| | Résultats par pays | 1053 | 1138 |
| | 15 organisations les plus fréquentes | 1055 | 1141 |
| | Nombre d’organisations différentes | 1051 | 1136 |
| | Nombre d’IP différentes | 1051 | 1135 |
| | Nombre total de résultats | 1051 | 1136 |
|||||
| DS14 | Nombre par classement | 1842 | 1718 |
| | Nombre au fil du temps | 1839 | 1716 |
| | Résultats par pays | 1838 | 1714 |
| | 15 organisations les plus fréquentes | 1842 | 1718 |
| | Nombre d’organisations différentes | 1837 | 1712 |
| | Nombre d’IP différentes | 1837 | 1712 |
| | Nombre total de résultats | 1837 | 1712 |

Ces données montrent que le nombre de requêtes exécutées par le cluster DS4 a augmenté alors que le temps de réponse moyen a diminué, et que c’est l’inverse pour le cluster DS14. Le facteur important est que l’utilisation de l’unité centrale par le cluster DS4 dans les tests avec 1 et 2 réplicas était inégalement répartie. Certains nœuds approchaient les 100 % d’utilisation, tandis que d’autres personnes avaient une capacité de traitement supplémentaire disponible. L’amélioration des performances est plus probablement due à l’augmentation de la capacité de distribution du traitement sur les nœuds du cluster. L’illustration suivante montre la variation de traitement entre les machines virtuelles les plus intensément et les moins intensément utilisées (nœuds 4 et 3) :

![](./media/guidance-elasticsearch/query-performance9.png)

***Figure 9. Utilisation de l’unité centrale pour les nœuds les moins utilisés et les plus utilisés dans le cluster DS4 exécutant le test *requête seulement****

Pour le cluster DS14, cela n’était pas le cas. Pour les deux tests, l’utilisation de l’unité centrale était inférieure sur l’ensemble des nœuds, et la disponibilité d’un deuxième réplica est devenue bien moins avantageuse et même plutôt une surcharge :

![](./media/guidance-elasticsearch/query-performance10.png)

***Figure 10. Utilisation de l’unité centrale pour les nœuds les moins utilisés et les plus utilisés dans le cluster DS14 exécutant le test *requête seulement****

Ces résultats montrent la nécessité d’évaluer soigneusement votre système avant de décider d’utiliser plusieurs réplicas. Vous devez toujours avoir au moins un réplica de chaque index (sauf si vous êtes prêt à risquer la perte de données en cas de défaillance d’un nœud), mais des réplicas supplémentaires peuvent imposer une charge sur le système sans présenter beaucoup d’avantages, en fonction de vos charges de travail et des ressources matérielles disponibles pour le cluster.

## Résultats de performance - Valeurs Doc

Les tests *ingestion et requête* ont été effectués avec les valeurs Doc activées. Elasticsearch stockait donc sur le disque les données utilisées pour le tri des champs. Les tests ont été répétés en désactivant les valeurs Doc. Elasticsearch construisait ainsi dynamiquement les données de champ et les mettait en mémoire cache. Tous les tests ont été exécutés pendant 24 heures. Le tableau ci-dessous compare les temps de réponse pour les tests exécutés sur les clusters à 6 nœuds créés à l’aide de machines virtuelles D4, DS4 et DS14 (le cluster D4 utilise des disques durs standard, tandis que les clusters DS4 et DS14 utilisent des disques SSD).

| Cluster | Opération/Requête | Temps de réponse moyen (ms) - Valeurs Doc activées | Temps de réponse moyen (ms) - Valeurs Doc désactivées | % d’écart dans les temps de réponse |
|---------|----------------------------|-------------------------------------------------|--------------------------------------------------|-------------------------------|
| D4 | Ingestion | 978 | 835 | -15 % |
| | Nombre par classement | 103 | 132 | +28 % |
| | Nombre au fil du temps | 134 | 189 | +41 % |
| | Résultats par pays | 199 | 259 | +30 % |
| | 15 organisations les plus fréquentes | 137 | 184 | +34 % |
| | Nombre d’organisations différentes | 139 | 197 | +42 % |
| | Nombre d’IP différentes | 510 | 604 | +18 % |
| | Nombre total de résultats | 89 | 134 | +51 % |
||||||
| DS4 | Ingestion | 511 | 581 | +14 % |
| | Nombre par classement | 187 | 190 | +2 % |
| | Nombre au fil du temps | 411 | 409 | -0,5 % |
| | Résultats par pays | 402 | 414 | +3 % |
| | 15 organisations les plus fréquentes | 307 | 284 | -7 % |
| | Nombre d’organisations différentes | 320 | 313 | -2 % |
| | Nombre d’IP différentes | 841 | 955 | +14 % |
| | Nombre total de résultats | 236 | 281 | +19 % |
||||||
| DS14 | Ingestion | 511 | 571 | +12 % |
| | Nombre par classement | 201 | 232 | +15 % |
| | Nombre au fil du temps | 298 | 341 | +14 % |
| | Résultats par pays | 363 | 457 | +26 % |
| | 15 organisations les plus fréquentes | 244 | 338 | +39 % |
| | Nombre d’organisations différentes | 283 | 350 | +24 % |
| | Nombre d’IP différentes | 681 | 909 | +33 % |
| | Nombre total de résultats | 200 | 245 | +23 % |

Le tableau suivant compare le nombre d’opérations d’ingestion effectuées par les tests :

| Cluster | Nb d’opérations d’ingestion - Valeurs Doc activées | Nb d’opérations d’ingestion - Valeurs Doc désactivées | % d’écart dans le nb d’opérations d’ingestion |
|---------|----------------------------------------------|-----------------------------------------------|-----------------------------------------|
| D4 | 264769 | 408690 | +54 % |
| DS4 | 503137 | 578237 | +15 % |
| DS14 | 502714 | 586472 | +17 % |

Les taux d’ingestion s’améliorent lorsque les valeurs Doc sont désactivées car les données écrites sur le disque sont moins nombreuses lorsque les documents sont insérés. L’amélioration des performances est particulièrement significative avec la machine virtuelle D4 équipée de disques durs pour stocker des données. Dans ce cas, le temps de réponse des opérations d’ingestion a également diminué de 15 % (voir le premier tableau dans cette section). Cela peut être dû à la diminution de la pression sur les disques durs qui fonctionnaient probablement à la limite de la capacité d’E/S par seconde lors du test avec les valeurs Doc. Pour plus d’informations, voir le test du type de disque. Le graphique suivant compare les performances d’E/S des machines virtuelles D4 avec des valeurs Doc activées (valeurs stockées sur le disque) et des valeurs Doc désactivées (valeurs stockées dans la mémoire) :

![](./media/guidance-elasticsearch/query-performance11.png)

***Figure 11. Activité du disque pour le cluster D4 avec des valeurs Doc activées et désactivées***

En revanche, les valeurs de réception pour les machines virtuelles à l’aide de disques SSD indiquent une légère augmentation du nombre de documents, mais également une augmentation du temps de réponse des opérations de réception. En dehors d’une ou deux petites exceptions, les temps de réponse des requêtes étaient également moins bons. Les disques SSD sont moins susceptibles de fonctionner à la limite de leur capacité d’E/S par seconde avec des valeurs Doc activées. Dans ce cas, les modifications de performances sont probablement dues à l’augmentation de l’activité de traitement et à la surcharge liée à la gestion du segment JVM. Cela est évident lorsque vous comparez l’utilisation de l’unité centrale avec des valeurs Doc activées et désactivées. Le graphique suivant met en évidence ces données pour le cluster DS4, au sein duquel la plupart de l’utilisation de l’unité centrale est passée de la plage 30-40 % avec des valeurs Doc activées, à la plage 40-50 % avec des valeurs Doc désactivées (le cluster DS14 révélait une tendance similaire) :

![](./media/guidance-elasticsearch/query-performance12.png)

***Figure 12. Utilisation de l’unité centrale pour le cluster DS4 avec des valeurs Doc activées et désactivées***

Pour distinguer les effets des valeurs Doc sur les performances des requêtes à partir de l’ingestion de données, deux tests « requête seulement » ont été effectués pour les clusters DS4 et DS14 avec des valeurs Doc activées et désactivées. Le tableau ci-dessous récapitule les résultats de ces tests :

| Cluster | Opération/Requête | Temps de réponse moyen (ms) - Valeurs Doc activées | Temps de réponse moyen (ms) - Valeurs Doc désactivées | % d’écart dans les temps de réponse |
|---------|----------------------------|-------------------------------------------------|--------------------------------------------------|-------------------------------|
| DS4 | Nombre par classement | 4489 | 3736 | -16 % |
| | Nombre au fil du temps | 7293 | 5459 | -25 % |
| | Résultats par pays | 7564 | 5930 | -22 % |
| | 15 organisations les plus fréquentes | 5066 | 3874 | -14 % |
| | Nombre d’organisations différentes | 5231 | 4483 | -2 % |
| | Nombre d’IP différentes | 9228 | 9474 | +3 % |
| | Nombre total de résultats | 2180 | 1218 | -44 % |
||||||
| DS14 | Nombre par classement | 1927 | 2144 | +11 % |
| | Nombre au fil du temps | 4483 | 4337 | -3 % |
| | Résultats par pays | 4761 | 4840 | +2 % |
| | 15 organisations les plus fréquentes | 2117 | 2302 | +9 % |
| | Nombre d’organisations différentes | 2393 | 2497 | +4 % |
| | Nombre d’IP différentes | 7159 | 7639 | +7 % |
| | Nombre total de résultats | 642 | 633 | -1 % |

N’oubliez pas que les valeurs Doc sont activées par défaut dans Elasticsearch 2.0 et les versions ultérieures. Dans les tests portant sur le cluster DS4, la désactivation des valeurs Doc semble avoir un effet global positif, alors que c’est l’inverse pour le cluster DS14 (les deux cas dans lesquelles les performances sont meilleures avec des valeurs Doc désactivés sont très marginaux).

Pour le cluster DS4, dans les deux cas, l’utilisation de l’unité centrale était proche des 100 % pendant la durée des deux tests, indiquant que le cluster était lié à l’unité centrale. Toutefois, le nombre de requêtes traitées est passé de 7 369 à 5 894 (soit une baisse de 20 %) ; voir le tableau ci-dessous. N’oubliez pas que si les valeurs Doc sont désactivées, Elasticsearch génère dynamiquement des données de champ en mémoire, consommant une partie de la puissance de l’unité centrale. Cette configuration a réduit le taux d’E/S du disque mais a accru la pression sur les unités centrales fonctionnant déjà à leurs capacités maximales. Dans ce cas, les requêtes sont plus rapides avec des valeurs Doc désactivées, mais elles sont aussi moins nombreuses.

Dans les tests DS14 avec et sans valeurs Doc, l’activité de l’unité centrale était élevée, mais pas à 100 %. Le nombre de requêtes exécutées était légèrement plus élevé (environ 4 %) dans les tests avec des valeurs Doc activées :

| Cluster | Interroger | Nombre exécuté - Valeurs Doc activées | Nombre exécuté - Valeurs Doc désactivées |
|---------|----------------------------|---------------------------------------|----------------------------------------|
| DS4 | Nombre par classement | 1054 | 845 |
| | Nombre au fil du temps | 1054 | 844 |
| | Résultats par pays | 1053 | 842 |
| | 15 organisations les plus fréquentes | 1055 | 846 |
| | Nombre d’organisations différentes | 1051 | 839 |
| | Nombre d’IP différentes | 1051 | 839 |
| | Nombre total de résultats | 1051 | 839  
||||| |
| DS14 | Nombre par classement | 1772 | 1842 |
| | Nombre au fil du temps | 1772 | 1839 |
| | Résultats par pays | 1770 | 1838 |
| | 15 organisations les plus fréquentes | 1773 | 1842 |
| | Nombre d’organisations différentes | 1769 | 1837 |
| | Nombre d’IP différentes | 1768 | 1837 |
| | Nombre total de résultats | 1769 | 1837 |

## Résultats de performances - Cache de demande de partition

Pour illustrer la manière dont les données de mise en cache d’index dans la mémoire de chaque nœud peuvent affecter les performances, le test *de requête et d’ingestion* a été effectué sur un cluster DS4 et un cluster DS14 à 6 nœuds avec mise en cache d’index activée. Pour plus d’informations, voir la section [Utilisation du cache de demande de partition](#using-the-shard-request-cache). Les résultats ont été comparés à ceux générés par les tests précédents en utilisant le même index mais en désactivant la mise en cache d’index. Le tableau suivant récapitule les résultats. Notez que les données ont été restreintes pour couvrir uniquement les 90 minutes de test. À ce stade, la tendance était visible et continuer le test n’aurait probablement pas fourni d’informations supplémentaires :

| Cluster | Opération/Requête | Temps de réponse moyen (ms) - Cache d’index désactivé | Temps de réponse moyen (ms) - Cache d’index activé | % d’écart dans les temps de réponse |
|---------|----------------------------|---------------------------------------------------|--------------------------------------------------|-------------------------------|
| DS4 | Ingestion | 504 | 3260 | +547 % |
| | Nombre par classement | 218 | 273 | +25 % |
| | Nombre au fil du temps | 450 | 314 | -30 % |
| | Résultats par pays | 447 | 397 | -11 % |
| | 15 organisations les plus fréquentes | 342 | 317 | -7 % |
| | Nombre d’organisations différentes | 370 | 324 | -12 % |
| | Nombre d’IP différentes | 760 | 355 | -53 % |
| | Nombre total de résultats | 258 | 291 | +12 % |
||||||
| DS14 | Ingestion | 503 | 3365 | +569 % |
| | Nombre par classement | 234 | 262 | +12 % |
| | Nombre au fil du temps | 357 | 298 | -17 % |
| | Résultats par pays | 416 | 383 | -8 % |
| | 15 organisations les plus fréquentes | 272 | 324 | -7 % |
| | Nombre d’organisations différentes | 330 | 321 | -3 % |
| | Nombre d’IP différentes | 674 | 352 | -48 % |
| | Nombre total de résultats | 227 | 292 | +29 % |

Ces données révèlent deux points d’intérêt :

-  les taux d’ingestion de données semblent considérablement diminuer en activant la mise en cache d’index, et

-  la mise en cache d’index n’augmente pas nécessairement le temps de réponse de tous les types de requête, et peut avoir un effet contraire sur certaines opérations d’agrégat comme celles effectuées par les requêtes Nombre par classement et Nombre total de résultats.
 

Pour comprendre pourquoi le système a ce comportement, vous devez tenir compte du nombre de requêtes exécutées avec succès dans chaque cas, lors de l’exécution du test. Le tableau suivant récapitule ces données :

| Cluster | Opération/Requête | Nb d’opérations/requêtes - Cache d’index désactivé | Nb d’opérations/requêtes - Cache d’index activé |
|---------|----------------------------|-------------------------------------------------|------------------------------------------------|
| DS4 | Ingestion | 38611 | 13232 |
| | Nombre par classement | 524 | 18704 |
| | Nombre au fil du temps | 523 | 18703 |
| | Résultats par pays | 522 | 18702 |
| | 15 organisations les plus fréquentes | 521 | 18706 |
| | Nombre d’organisations différentes | 521 | 18700 |
| | Nombre d’IP différentes | 521 | 18699 |
| | Nombre total de résultats | 521 | 18701  
|||| |
| DS14 | Ingestion | 38769 | 12835 |
| | Nombre par classement | 528 | 19239 |
| | Nombre au fil du temps | 528 | 19239 |
| | Résultats par pays | 528 | 19238 |
| | 15 organisations les plus fréquentes | 527 | 19240 |
| | Nombre d’organisations différentes | 524 | 19234 |
| | Nombre d’IP différentes | 524 | 19234 |
| | Nombre total de résultats | 527 | 19236 |

Comme vous pouvez le voir, même si le taux d’ingestion lors de l’activation de la mise en cache était égal à environ 1/3 de celui constaté lors de la désactivation de la mise en cache, le nombre de requêtes exécutées a augmenté par un facteur de 34. Les requêtes ne génèrent plus autant d’E/S de disque et n’ont pas à se disputer les ressources du disque. Ceci est visible dans les graphiques de la figure 13 ci-dessous qui compare l’activité d’E/S pour les quatre cas :

![](./media/guidance-elasticsearch/query-performance13.png)

***Figure 13. Activité d’E/S du disque pour le test *ingestion et requête* test avec mise en cache d’index désactivée et activée***

La diminution des E/S de disque impliquait également que l’unité centrale consacrait moins de temps à attendre la fin des E/S. Cela est illustré dans la figure 14 :

![](./media/guidance-elasticsearch/query-performance14.png)

***Figure 14.Période pendant laquelle l’unité centrale a attendu la fin des E/S de disque lors du test *ingestion et requête* avec la mise en cache d’index désactivée et activée***

La réduction des E/S de disque impliquait qu’Elasticsearch pouvait consacrer plus de temps à traiter les requêtes à partir des données stockées en mémoire. Cela augmentait l’utilisation du processeur ; vous pouvez le constater en examinant l’utilisation de l’unité centrale dans les quatre cas. Les graphiques suivants montrent que l’utilisation de l’unité centrale était plus importante avec la mise en cache activée :

![](./media/guidance-elasticsearch/query-performance15.png)

***Figure 15. Utilisation de l’unité centrale pour le test *ingestion et requête* avec mise en cache d’index désactivée et activée***

Dans les deux scénarios, le volume d’E/S réseau pendant la durée des tests était globalement similaire. Les tests sans mise en cache ont montré une dégradation progressive pendant la durée du test, mais les exécutions plus longues de ces tests sur une période de 24 heures ont montré que cette statistique s’est stabilisée à environ 2,75 Gbit/s. L’illustration ci-dessous montre ces données pour les clusters DS4 (les données des clusters DS14 étaient très similaires) :

![](./media/guidance-elasticsearch/query-performance16.png)

***Figure 16. Volumes de trafic réseau pour le test *ingestion et requête* avec mise en cache d’index désactivée et activée***

Comme décrit dans le test [Mise à l’échelle](#performance-results-scaling-up), les restrictions de bande passante réseau avec les machines virtuelles Azure ne sont pas publiées et peuvent varier, mais les niveaux modérés d’activité de l’unité centrale et du disque suggèrent que l’utilisation réseau peut être un facteur de limitation dans ce scénario.

La mise en cache est plus adaptée aux scénarios dans lesquels les données changent rarement. Pour mettre en évidence l’impact de la mise en cache dans ce scénario, les tests *requête seulement* ont été effectués avec la mise en cache activée. Les résultats sont illustrés ci-dessous (ces tests ont été effectués pendant 90 minutes, et les index contenaient 100 millions de documents pendant les tests) :

| Cluster | Interroger | Temps de réponse moyen (en ms) | Nb de requêtes exécutées |
|---------|----------------------------|----------------------------|-------------------------|
| | | **Cache désactivé** | **Cache activé** |
| DS4 | Nombre par classement | 4489 | 210 |
| | Nombre au fil du temps | 7292 | 211 |
| | Résultats par pays | 7564 | 231 |
| | 15 organisations les plus fréquentes | 5066 | 211 |
| | Nombre d’organisations différentes | 5231 | 211 |
| | Nombre d’IP différentes | 9228 | 218 |
| | Nombre total de résultats | 2180 | 210  
|||| |
| DS14 | Nombre par classement | 1927 | 211 |
| | Nombre au fil du temps | 4483 | 219 |
| | Résultats par pays | 4761 | 236 |
| | 15 organisations les plus fréquentes | 2117 | 212 |
| | Nombre d’organisations différentes | 2393 | 212 |
| | Nombre d’IP différentes | 7159 | 220 |
| | Nombre total de résultats | 642 | 211 |

La variance des performances des tests sans mise en cache est due à la différence de ressources disponibles pour les machines virtuelles DS4 et DS14. Dans les deux cas de test avec mise en cache, le temps de réponse moyen a considérablement chuté à mesure que les données étaient extraites directement de la mémoire. Il est également important de souligner que les temps de réponse pour les tests avec mise en cache des clusters DS4 et DS14 étaient très similaires malgré la disparité des résultats sans mise en cache. Il y a également très peu de différence entre les temps de réponse pour chaque requête durant chaque test. Elles prennent toutes environ 220 ms. Les taux d’E/S du disque et l’utilisation de l’unité centrale pour les deux clusters étaient très bas car, une fois l’ensemble des données en mémoire, peu d’E/S ou de traitements sont requis. Le taux d’E/S réseau était similaire à celui des tests sans mise en cache, ce qui a confirmé que la bande passante réseau pouvait être un facteur de limitation durant ce test. Les graphiques suivants présentent ces informations pour le cluster DS4. Le profil du cluster DS14 était très similaire :

![](./media/guidance-elasticsearch/query-performance17.png)

***Figure 17. E/S de disque, utilisation de l’unité centrale et utilisation du réseau pour le test *requête seulement* avec mise en cache d’index activée***

Les chiffres dans le tableau ci-dessus suggèrent que l’utilisation de l’architecture DS14 présente peu d’avantages par rapport à l’utilisation du cluster DS4. En fait, le nombre d’échantillons générés par le cluster DS14 était environ 5 % inférieur à celui du cluster DS4, mais cela pouvait également être dû aux restrictions réseau pouvant varier légèrement au fil du temps.

## Résultats de performances - Nombre de partitions

L’objectif de ce test était de déterminer si le nombre de partitions créées pour un index avait une incidence sur les performances des requêtes de cet index.

Les tests distincts effectués précédemment ont montré que la configuration de la partition d’un index peut avoir un impact sur le taux d’ingestion des données. Pour plus d’informations, voir le document [Maximizing Data Ingestion Performance with Elasticsearch on Azure](https://github.com/mspnp/azure-guidance/blob/master/Elasticsearch-Data-Ingestion-Performance.md) (Maximisation des performances d’ingestion des données avec Elasticsearch sur Azure). Les tests effectués pour vérifier les performances des requêtes ont suivi une méthodologie similaire, mais se limitaient à un cluster à 6 nœuds fonctionnant sur un matériel DS14. Cette approche permet de réduire le nombre de variables, de sorte que les différences éventuelles de performance sont probablement dues au volume des partitions.

Le test *requête seulement* a été effectué sur des copies du même index configuré avec 7, 13, 23, 37 et 61 partitions principales. L’index contenait 100 millions de documents et comportait un seul réplica, doublant le nombre de partitions sur le cluster. Chaque test a été exécuté pendant 90 minutes. Le tableau suivant récapitule les résultats : Le temps de réponse moyen indiqué est le temps de réponse de la transaction de test JMeter qui couvre l’ensemble de requêtes exécutées par chaque itération du test. Pour plus d’informations, consultez la remarque dans la section [Résultats de performances - Mise à l’échelle](#performance-results-scaling-up) :

| Nombre de partitions | Disposition des partitions (partitions par nœud, y compris les réplicas) | Nombre de requêtes exécutées | Temps de réponse moyen (en ms) |
|---------------------------|----------------------------------------------------|-----------------------------|------------------------|
| 7 (14 avec les réplicas) | 3-2-2-2-2-3 | 7461 | 40524 |
| 13 (26) | 5-4-5-4-4-4 | 7369 | 41055 |
| 23 (46) | 7-8-8-7-8-8 | 14193 | 21283 |
| 37 (74) | 13-12-12-13-12-12 | 13399 | 22506 |
| 61 (122) | 20-21-20-20-21-20 | 14743 | 20445 |

Ces résultats indiquent qu’il existe une différence significative de performance entre le cluster des partitions 13(26) et le cluster des partitions 23(46) ; le débit est quasiment multiplié par deux et les temps de réponse sont divisés par deux. Cela est probablement dû à la configuration des machines virtuelles et des structures utilisées par Elasticsearch pour traiter les demandes de recherche. Les demandes de recherche sont mises en file d’attente, et chaque demande de recherche est gérée par un thread de recherche unique. Le nombre de threads de recherche créés par un nœud Elasticsearch est fonction du nombre de processeurs disponibles sur la machine hébergeant le nœud. Les résultats suggèrent qu’avec seulement 4 ou 5 partitions sur un nœud, les ressources de traitement ne sont pas entièrement utilisées. Cela se confirme si l’on observe l’utilisation de l’unité centrale pendant l’exécution de ce test. L’illustration suivante est un instantané effectué à partir de Marvel lors de l’exécution du test des partitions 13(26) :

![](./media/guidance-elasticsearch/query-performance18.png)

***Figure 18. Utilisation de l’unité centrale pour le test *requête seulement* sur le cluster des partitions 7(14)***

Comparez ces chiffres avec ceux du test des partitions 23(46) :

![](./media/guidance-elasticsearch/query-performance19.png)

***Figure 19. Utilisation de l’unité centrale pour le test *requête seulement* sur le cluster des partitions 23(46)***

Dans le test des partitions 23(46), l’utilisation de l’unité centrale était bien plus élevée. Chaque nœud contient 7 ou 8 partitions. L’architecture du DS14 fournit 16 processeurs, et Elasticsearch est davantage en mesure d’exploiter ce nombre de cœurs avec les partitions supplémentaires. Les chiffres dans le tableau ci-dessus suggèrent que l’augmentation du nombre de partitions au-delà de ce point peut améliorer légèrement les performances, mais vous devez contrebalancer ces chiffres avec la charge supplémentaire représentée par la gestion d’un volume élevé de partitions. Ces tests impliquent que le nombre optimal de partitions par nœud est deux fois inférieur au nombre de cœurs de processeur disponibles sur chaque nœud. Toutefois, n’oubliez pas que ces résultats ont été obtenus lors de l’exécution de requêtes seulement. Si votre système importe des données, vous devez également prendre en compte la manière dont le partitionnement peut affecter les performances des opérations d’ingestion des données. Pour plus d’informations sur cet aspect, voir le document [Maximizing Data Ingestion Performance with Elasticsearch on Azure](https://github.com/mspnp/azure-guidance/blob/master/Elasticsearch-Data-Ingestion-Performance.md) (Maximisation des performances d’ingestion des données avec Elasticsearch sur Azure).

## Résumé

Elasticsearch offre de nombreuses options que vous pouvez utiliser pour structurer les index et les configurer afin de prendre en charge les opérations de requête à grande échelle. Ce document vous a présenté certaines configurations et techniques communes que vous pouvez utiliser pour régler votre base de données à des fins de requête. Toutefois, vous devez reconnaître qu’il existe un compromis entre optimiser une base de données pour permettre la récupération rapide et prendre en charge l’ingestion de grands volumes de données. Parfois, ce qui est bon pour le processus de requête peut ne pas l’être pour les opérations d’insert et vice versa. Dans un système exposé à des charges de travail mixtes, vous devez évaluer où se trouve le point d’équilibre et ajuster les paramètres du système en fonction.

En outre, la capacité d’application des différentes configurations et techniques peut varier en fonction de la structure des données et des limitations (ou autre) du matériel sur lequel le système est construit. Dans ce document, beaucoup de tests montrent comment la sélection de la plate-forme matérielle peut affecter le débit, et comment certaines stratégies peuvent être utiles dans certains cas, mais nuisibles dans d’autres. Il est essentiel de comprendre les options disponibles, puis de procéder à une évaluation rigoureuse en utilisant vos propres données pour déterminer la combinaison optimale.

Enfin, n’oubliez pas qu’une base de données Elasticsearch n’est pas nécessairement un élément statique. Elle va probablement s’agrandir au fil du temps, et les stratégies utilisées pour structurer les données doivent être révisées régulièrement. Par exemple, il peut être nécessaire de monter ou baisser en puissance, ou de réindexer les données avec des partitions supplémentaires. À mesure que le système grandit en taille et en complexité, soyez prêt à tester constamment les performances pour vous assurer que vous respectez toujours les SLA que vous avez garantis à vos clients.

## Annexe : Test de performance d’agrégation et de requête

Cette annexe décrit le test de performances effectué sur le cluster Elasticsearch. Les tests ont été exécutés à l’aide de JMeter exécuté sur un ensemble distinct de machines virtuelles. Pour en savoir plus sur la configuration de l’environnement de test, consultez le document « How-To: Create a Performance Testing Environment for Elasticsearch » (Procédure : création d’un environnement de test de performances pour Elasticsearch). Pour effectuer vos propres tests, vous pouvez créer votre propre plan de test JMeter manuellement en suivant les conseils de cette annexe, ou vous pouvez utiliser les scripts de test automatisé disponibles à part. Pour en savoir plus, consultez le document « How-To: Run the Automated Elasticsearch Query Tests » (Procédure : exécution des tests de requête automatisés Elasticsearch).

La charge de travail des requêtes de données a exécuté l’ensemble de requêtes décrit ci-dessous tout en procédant simultanément au chargement de documents à grande échelle (les données ont été chargées en utilisant un test JUnit, en suivant la même approche pour les tests d’ingestion des données décrits dans le document [Maximizing Data Ingestion Performance with Elasticsearch on Azure](https://github.com/mspnp/azure-guidance/blob/master/Elasticsearch-Data-Ingestion-Performance.md) (Maximisation des performances d’ingestion des données ElasticSearch sur Azure).) L’objectif de cette charge de travail était de simuler un environnement de production dans lequel de nouvelles données sont constamment ajoutées pendant que les recherches sont effectuées. Les requêtes étaient structurées pour récupérer uniquement les données les plus récentes à partir de documents ajoutés au cours des 15 dernières minutes.

Chaque document a été stocké dans un index unique nommé *dx* et était de type *doc*. Vous pouvez utiliser la demande HTTP suivante pour créer l’index. Les paramètres *number\_of\_replicas* et *number\_of\_shards* étaient différents des valeurs affichées ci-dessous dans plusieurs des tests. En outre, pour les tests ayant utilisé les données de champ au lieu de valeurs Doc, chaque propriété a été annotée avec l’attribut *"doc\_values" : false*.

> **Important**. L’index a été supprimé, puis recréé, avant chaque série de tests.

``` http
PUT /idx
{  
    "settings" : {
        "number_of_replicas": 1,
        "refresh_interval": "30s",
        "number_of_shards": "5",
        "index.translog.durability": "async"    
    },
    "doc": {
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

Les requêtes suivantes ont été exécutées par le test :
* Combien de documents avec chaque valeur Rating ont été ajoutés au cours des 15 dernières minutes ?

  ```http
  GET /idx/doc/_search
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

* Combien de documents ont été ajoutés pendant chaque intervalle de 5 minutes au cours des 15 dernières minutes ?

  ```http
  GET /idx/doc/_search
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

* Combien de documents de chaque valeur Rating ont été ajoutés pour chaque pays au cours des 15 dernières minutes ?

  ```HTTP
  GET /idx/doc/_search
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

* Quelles sont les 15 organisations qui apparaissent le plus souvent dans les documents ajoutés au cours des 15 dernières minutes ?

  ```http
  GET /idx/doc/_search
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

* Combien d’organisations différentes apparaissent dans les documents ajoutés au cours des 15 dernières minutes ?

  ```http
  GET /idx/doc/_search
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

* Combien de documents ont été ajoutés au cours des 15 dernières minutes ?

  ```http
  GET /idx/doc/_search
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

* Combien de valeurs Sourcehelp différentes apparaissent dans les documents ajoutés au cours des 15 dernières minutes ?

  ```http
  GET /idx/doc/_search
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

<!---HONumber=AcomDC_0302_2016-->