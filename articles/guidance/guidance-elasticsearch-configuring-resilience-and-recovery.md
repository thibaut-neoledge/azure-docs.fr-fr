<properties
   pageTitle="Configuration de la résilience et de la récupération d’Elasticsearch sur Azure"
   description="Considérations relatives à la résilience et à la récupération pour Elasticsearch."
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
   
# Configuration de la résilience et de la récupération d’Elasticsearch sur Azure

Cet article fait [partie d’une série](guidance-elasticsearch.md).

L’une des fonctionnalités clés d’Elasticsearch est la prise en charge fournie pour assurer la résilience en cas de défaillance des nœuds et/ou d’événements de partition réseau. La réplication est la manière la plus évidente d’améliorer la résilience de n’importe quel cluster. Elle permet à Elasticsearch de s’assurer que plusieurs copies d’un élément de données sont disponibles sur différents nœuds dans le cas où un nœud deviendrait inaccessible. Si un nœud est temporairement indisponible, d’autres nœuds contenant des réplicas des données du nœud manquant peuvent délivrer les données manquantes jusqu’à ce que le problème soit résolu. En cas de problème à plus long terme, le nœud manquant peut être remplacé par un nouveau nœud, et Elasticsearch peut restaurer les données sur le nouveau nœud à partir des réplicas.

Ici, nous résumons les options de résilience et de récupération disponibles avec Elasticsearch lorsqu’il est hébergé dans Azure, et nous décrivons certains aspects importants d’un cluster Elasticsearch dont vous devriez tenir compte pour réduire les risques de perte de données et les temps étendus de récupération de données.

Cet article illustre également quelques exemples de tests qui ont été effectués pour montrer les effets des différents types de défaillances sur un cluster Elasticsearch, et la manière dont le système réagit lors de la récupération.

Un cluster Elasticsearch utilise des réplicas pour garantir la disponibilité et améliorer les performances de lecture. Les réplicas doivent être stockés sur différentes machines virtuelles à partir des partitions principales qu’ils répliquent. L’objectif : si la machine virtuelle qui héberge un nœud de données échoue ou devient indisponible, le système peut continuer à fonctionner à l’aide des machines virtuelles contenant des réplicas.

## Utilisation de nœuds principaux dédiés

Un nœud dans un cluster Elasticsearch est choisi en tant que nœud principal. L’objectif de ce nœud est d’effectuer des opérations de gestion de cluster telles que :

- Détection de nœuds défaillants et basculement sur les réplicas

- Relocalisation de partitions afin d’équilibrer la charge de travail des nœuds

- Récupération des partitions lorsqu’un nœud est remis en ligne

Vous devez envisager d’utiliser les nœuds principaux dédiés dans les clusters critiques, et vérifier qu’il existe 3 nœuds dédiés dont le seul rôle est d’être les nœuds principaux. Cette configuration réduit la quantité de travail exigeant d’importantes ressources que ces nœuds doivent exécuter (ils ne stockent pas de données et ne gèrent pas de requêtes) et contribue à améliorer la stabilité du cluster. Un seul de ces nœuds sera élu, mais les autres contiendront une copie de l’état du système et pourront prendre le relais en cas de défaillance du nœud principal élu.

## Contrôle de la haute disponibilité avec Azure : domaines de mise à jour et domaines d’erreur 

Des machines virtuelles différentes peuvent partager le même matériel physique. Dans un centre de données Azure, un seul rack peut héberger plusieurs machines virtuelles, et l’ensemble de ces machines virtuelles partagent une source d’alimentation et un commutateur réseau communs. Une seule défaillance au niveau du rack peut donc avoir un impact sur plusieurs machines virtuelles. Azure utilise le concept de domaines d’erreur (FD) pour essayer de répartir ce risque. Un domaine d’erreur correspond plus ou moins à un groupe de machines virtuelles qui partagent le même rack. Pour vous assurer qu’une défaillance au niveau du rack ne bloque pas un nœud ainsi que les nœuds contenant tous ses réplicas simultanément, vous devez vous assurer que les machines virtuelles sont réparties entre les domaines d’erreur.

De même, les machines virtuelles peuvent être retirées par le [contrôleur de structure Azure](https://azure.microsoft.com/documentation/videos/fabric-controller-internals-building-and-updating-high-availability-apps/) pour exécuter la maintenance planifiée et les mises à niveau du système d’exploitation. Azure alloue des machines virtuelles à des domaines de mise à jour (UD). Lorsqu’un événement de maintenance planifiée se produit, seules les machines virtuelles dans un domaine de mise à jour unique sont affectées à tout moment. Les machines virtuelles dans d’autres domaines de mise à jour continuent d’être exécutées jusqu’à ce que les machines virtuelles dans le domaine de mise à jour soient remises en ligne. Par conséquent, vous devez également vous assurer que les machines virtuelles hébergeant les nœuds et leurs réplicas appartiennent à différents domaines de mise à jour autant que possible.

> [AZURE.NOTE] Pour plus d’informations sur les domaines d’erreur et les domaines de mise à jour, consultez [Gestion de la disponibilité des machines virtuelles][].

Vous ne pouvez allouer explicitement une machine virtuelle à un domaine de mise à jour et à un domaine d’erreur. Cette allocation est contrôlée par Azure lors de la création de machines virtuelles. Pour plus d’informations, consultez le document [Gestion de la disponibilité des machines virtuelles][]. Toutefois, vous pouvez spécifier que les machines virtuelles doivent être créées dans le cadre d’un groupe à haute disponibilité. Les machines virtuelles figurant dans le même groupe à haute disponibilité seront réparties sur les domaines de mise à jour d’erreur et les domaines d’erreur. Si vous créez des machines virtuelles manuellement, Azure associe chaque groupe à haute disponibilité à deux domaines d’erreur et à cinq domaines de mise à jour, et les machines seront allouées à ces domaines d’erreur et domaines de mise à jour de manière cyclique au fur et à mesure de la configuration de machines virtuelles supplémentaires, comme suit :

- La première machine virtuelle configurée dans le groupe à haute disponibilité sera placée dans le premier domaine d’erreur (FD 0) et dans le premier domaine de mise à jour (UD 0).
- La deuxième machine virtuelle configurée dans le groupe à haute disponibilité sera placée dans FD 1 et UD 1.
- La troisième machine virtuelle configurée dans le groupe à haute disponibilité sera placée dans FD 0 et UD 2. La quatrième machine virtuelle configurée dans le groupe à haute disponibilité sera placée dans FD 1 et UD 3.
- La cinquième machine virtuelle configurée dans le groupe à haute disponibilité sera placée FD 0 et UD 4.
- La sixième machine virtuelle configurée dans le groupe à haute disponibilité sera placée FD 1 et UD 0.
- La septième machine virtuelle configurée dans le groupe à haute disponibilité sera placée FD 0 et UD 1.

> [AZURE.IMPORTANT] Si vous créez des machines virtuelles à l’aide d’Azure Resource Manager (ARM), il est possible d’allouer à chaque groupe à haute disponibilité jusqu’à 3 domaines d’erreur et 20 domaines de mise à jour. Il s’agit d’une bonne raison d’utiliser ARM.

En règle générale, placez toutes les machines virtuelles qui ont la même fonction dans le même groupe à haute disponibilité, mais créez des groupes à haute disponibilité distincts pour les machines virtuelles qui exécutent des fonctions différentes. Avec Elasticsearch, cela signifie que vous devez envisager de créer des groupes à haute disponibilité distincts au moins pour :

- Les machines virtuelles hébergeant des nœuds de données.
- Les machines virtuelles hébergeant des nœuds de client (si vous en utilisez).
- Les machines virtuelles hébergeant des nœuds principaux.

En outre, vous devez vous assurer que chaque nœud dans un cluster est conscient du domaine de mise à jour et du domaine d’erreur auxquels il appartient. Ces informations permettent de garantir qu’Elasticsearch ne crée pas les partitions et leurs réplicas dans les mêmes domaines d’erreur et de mise à jour, réduisant ainsi le risque qu’une partition et ses réplicas soient retirés en même temps. Vous pouvez configurer un nœud Elasticsearch pour refléter la distribution du matériel du cluster en configurant [Shard Allocation Awareness](https://www.elastic.co/guide/en/elasticsearch/reference/current/allocation-awareness.html#allocation-awareness) (Connaissance de l’allocation de partitions). Par exemple, vous pouvez définir une paire d’attributs personnalisés de nœud appelés *faultDomain* et *updateDomain* dans le fichier elasticsearch.yml, comme suit :

```yaml
node.faultDomain: \${FAULTDOMAIN}
node.updateDomain: \${UPDATEDOMAIN}
```

Dans ce cas, les attributs sont définis à l’aide des valeurs contenues dans les variables d’environnement *\\${FAULTDOMAIN}* et *\\${UPDATEDOMAIN}* au démarrage d’Elasticsearch. Vous devez également ajouter les entrées suivantes au fichier Elasticsearch.yml pour indiquer que *faultDomain* et *updateDomain* sont des attributs de connaissance de l’allocation et spécifient les ensembles de valeurs acceptables pour ces attributs :

```yaml
cluster.routing.allocation.awareness.force.updateDomain.values: 0,1,2,3,4
cluster.routing.allocation.awareness.force.faultDomain.values: 0,1
cluster.routing.allocation.awareness.attributes: updateDomain, faultDomain
```

Vous pouvez utiliser la connaissance de l’allocation de partitions conjointement avec [Shard Allocation Filtering](https://www.elastic.co/guide/en/elasticsearch/reference/2.0/shard-allocation-filtering.html#shard-allocation-filtering) (Filtrage de l’allocation de partitions) afin de spécifier explicitement les nœuds qui peuvent héberger des partitions d’un index donné.

Si vous avez besoin d’effectuer une mise à l’échelle au-delà du nombre de domaines d’erreur et de domaines de mise à jour dans un groupe à haute disponibilité, vous pouvez créer des machines virtuelles dans des groupes à haute disponibilité supplémentaires. Toutefois, vous devez comprendre que les nœuds se trouvant dans différents groupes à haute disponibilité peuvent être retirés simultanément pour des raisons de maintenance. Essayez de vous assurer que chaque partition et au moins l’un de ses réplicas sont contenus dans le même groupe à haute disponibilité.

> [AZURE.NOTE] Il existe actuellement une limite de 100 machines virtuelles par groupe à haute disponibilité. Pour plus d’informations, consultez [Abonnement Azure et limites, quotas et contraintes du service](../azure-subscription-service-limits.md).

### Sauvegarde et restauration

L’utilisation de réplicas ne fournit pas une protection complète contre les défaillances graves (par exemple, la suppression accidentelle de l’ensemble du cluster). Vous devez vous assurer que vous sauvegardez régulièrement les données dans un cluster, et que vous disposez d’une stratégie éprouvée et testée pour la restauration du système à partir de ces sauvegardes.

Utilisez les [API d’instantané et de restauration](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-snapshots.html) pour sauvegarder et restaurer les index. Les instantanés peuvent être enregistrés dans un système de fichiers partagé. Autre possibilité, des plug-ins capables d’écrire des instantanés sur HDFS (le [plug-in HDFS](https://github.com/elasticsearch/elasticsearch-hadoop/tree/master/repository-hdfs)) ou sur le stockage Azure (le [plug-in Azure Cloud](https://github.com/elasticsearch/elasticsearch-cloud-azure#azure-repository)) sont disponibles.

Vous devez tenir compte des points suivants lorsque vous sélectionnez le mécanisme de stockage des instantanés :

- Vous pouvez utiliser [Azure File Storage](https://azure.microsoft.com/services/storage/files/) pour implémenter un système de fichiers partagé, accessible à partir de tous les nœuds.

- Utilisez uniquement le plug-in HDFS si vous exécutez Elasticsearch conjointement avec Hadoop.

- Le plug-in HDFS exige que vous désactiviez le Gestionnaire de sécurité Java exécuté à l’intérieur de l’instance Elasticsearch de la machine virtuelle Java.

- Le plug-in HDFS prend en charge tout système de fichiers compatible avec HDFS sous réserve que la configuration de Hadoop correcte soit utilisée avec Elasticsearch.

  
## Gestion d’une connectivité intermittente entre les nœuds

Des problèmes réseau intermittents, des redémarrages de machine virtuelle après une maintenance de routine du centre de données, et d’autres événements similaires peuvent donner lieu à des nœuds temporairement inaccessibles. Dans ces situations, où l’événement est susceptible d’être éphémère, la surcharge consistant à rééquilibrer les partitions se produit deux fois en succession rapide (une fois lorsque la défaillance est détectée, et à nouveau lorsque le nœud devient visible pour le nœud principal), et peut devenir significative et avoir des répercussions sur les performances. Vous pouvez empêcher l’inaccessibilité temporaire du nœud en poussant le nœud principal à rééquilibrer le cluster. Pour ce faire, définissez la propriété *delayed\_timeout* d’un index ou de tous les index. L’exemple ci-dessous définit le délai à 5 minutes :

```http
PUT /_all/settings
{
	"settings": {
    "index.unassigned.node_left.delayed_timeout": "5m"
	}
}
```

Pour plus d’informations, consultez [Delaying allocation when a node leaves](https://www.elastic.co/guide/en/elasticsearch/reference/current/delayed-allocation.html) (Retarder l’allocation lors du départ d’un nœud).

Dans un réseau sujet aux interruptions, vous pouvez également modifier les paramètres qui configurent un nœud principal pour détecter lorsqu’un autre nœud n’est plus accessible. Ces paramètres font partie du module de [découverte Zen](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-discovery-zen.html#modules-discovery-zen) fourni avec Elasticsearch, et vous pouvez les définir dans le fichier Elasticsearch.yml. Par exemple, le paramètre *discovery.zen.fd.ping.retries* spécifie le nombre de fois où un nœud principal va essayer d’effectuer un test Ping sur un autre nœud dans le cluster avant de décider qu’il a échoué. Ce paramètre est défini par défaut sur la valeur 3, mais vous pouvez le modifier comme suit :

```yaml
discovery.zen.fd.ping_retries: 6
```

## Contrôle de la récupération

Lorsque la connectivité à un nœud est rétablie après une défaillance, les partitions de ce nœud devront être récupérées pour être mises à jour. Par défaut, Elasticsearch récupère les partitions dans l’ordre suivant :

- Par ordre chronologique inverse de création d’index. Les index les plus récents sont restaurés avant les index les plus anciens.

- Par ordre alphanumérique inverse de nom d’index. Les index dont les noms sont supérieurs dans l’ordre alphanumérique seront restaurés en premier.

Si certains index sont plus stratégiques que d’autres, mais ne répondent pas à ces critères, vous pouvez remplacer la priorité des index en définissant la propriété *index.priority*. Les index avec une valeur plus élevée pour cette propriété seront récupérés avant les index présentant une valeur inférieure :

```http
PUT low_priority_index
{
	"settings": {
		"index.priority": 1
	}
}

PUT high_priority_index
{
	"settings": {
		"index.priority": 10
	}
}
```

Pour plus d’informations, consultez [Index Recovery Prioritization](https://www.elastic.co/guide/en/elasticsearch/reference/2.0/recovery-prioritization.html#recovery-prioritization) (Hiérarchisation de la récupération des index).

Vous pouvez surveiller le processus de récupération d’un ou de plusieurs index à l’aide de l’API *\_recovery*:

```http
GET /high_priority_index/_recovery?pretty=true
```

Pour plus d’informations, consultez [Indices Recovery](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-recovery.html#indices-recovery) (Récupération d’index).

> [AZURE.NOTE] Un cluster comptant des partitions nécessitant une récupération présente un statut *jaune* pour indiquer que toutes les partitions ne sont pas disponibles actuellement. Lorsque toutes les partitions sont disponibles, le statut du cluster doit redevenir *vert*. Un cluster avec le statut *rouge* indique qu’une ou plusieurs partitions sont physiquement absentes. Il peut être nécessaire de restaurer les données à partir d’une sauvegarde.

## Empêcher le Split-Brain 

Un Split-Brain peut se produire si les connexions entre les nœuds échouent. Si un nœud principal devient inaccessible pour une partie du cluster, une élection aura lieu dans le segment de réseau qui reste joignable et un autre nœud deviendra le nœud principal. Dans un cluster mal configuré, il est possible que chaque partie du cluster ait un nœud principal différent en raison d’incohérences ou d’altération des données. Ce phénomène est appelé *Split-Brain*.

Vous pouvez réduire les risques de Split-Brain en configurant la propriété *minimum\_master\_nodes* du module de détection dans le fichier elasticsearch.yml. Cette propriété spécifie le nombre de nœuds qui doit être disponible pour permettre l’élection d’un nœud principal. L’exemple suivant définit la valeur de cette propriété sur 2 :

```yaml
discovery.zen.minimum_master_nodes: 2
```

Cette valeur doit être définie sur la majorité la plus basse du nombre de nœuds qui sont en mesure de remplir le rôle de nœud principal. Par exemple, si votre cluster compte 3 nœuds principaux, la propriété *minimum\_master\_nodes* doit être définie sur 2. Si vous avez 5 nœuds principaux, la propriété *minimum\_master\_nodes* doit être définie sur 3. Idéalement, vous devez disposer d’un nombre impair de nœuds principaux.

**Remarque :** il est possible qu’un Split-Brain se produise si plusieurs nœuds principaux dans le même cluster sont démarrés simultanément. Bien que cet événement soit rare, vous pouvez l’éviter en démarrant des nœuds en série avec un léger retard (5 secondes) entre chacun d’entre eux.

## Gestion du déploiement de mises à jour

Si vous effectuez une mise à niveau logicielle des nœuds (par exemple, la migration vers une version plus récente ou l’exécution d’un correctif), vous devrez peut-être travailler sur des nœuds individuels qui devront être mis hors connexion tout en maintenant le reste du cluster disponible. Dans ce cas, envisagez de mettre en œuvre le processus suivant :

Assurez-vous que la réallocation des partitions est suffisamment retardée pour empêcher le nœud principal élu de rééquilibrer les partitions à partir d’un nœud manquant dans le reste du cluster. Par défaut, la réallocation des partitions est retardée de 1 minute, mais vous pouvez augmenter la durée si un nœud est susceptible d’être indisponible pendant une période plus longue. L’exemple suivant augmente le retard à 5 minutes :

```http
PUT /_all/_settings
{
	"settings": {
		"index.unassigned.node_left.delayed_timeout": "5m"
	}
}
```

> [AZURE.IMPORTANT] Vous pouvez également désactiver complètement la réallocation des partitions en définissant la propriété *cluster.routing.allocation.enable* du cluster sur *aucun*. Toutefois, évitez d’utiliser cette approche si de nouveaux index sont susceptibles d’être créés pendant que le nœud est hors connexion. Cela peut entraîner l’échec de l’allocation d’index et donner lieu à un cluster affichant un statut rouge.

Arrêtez Elasticsearch sur le nœud devant faire l’objet d’une maintenance. Si Elasticsearch est exécuté en tant que service, vous pouvez peut-être interrompre le processus de façon contrôlée à l’aide d’une commande du système d’exploitation. L’exemple suivant montre comment interrompre le service Elasticsearch sur un nœud unique exécuté sur Ubuntu :

```bash
service elasticsearch stop
```

Vous pouvez également utiliser l’API d’arrêt directement sur le nœud :

```http
POST /_cluster/nodes/_local/_shutdown
```

Effectuez la maintenance nécessaire sur le nœud, puis redémarrez-le et attendez qu’il rejoigne le cluster.

Réactivez l’allocation de partitions :

```http
PUT /_cluster/settings
{
	"transient": {
		"cluster.routing.allocation.enable": "all"
	}
}
```

> [AZURE.NOTE] Si vous devez effectuer la maintenance de plusieurs nœuds, répétez les étapes 2, 3 et 4 sur chaque nœud avant de réactiver l’allocation de partitions.

Si possible, arrêtez l’indexation de nouvelles données pendant ce processus. Cela vous aidera à réduire le temps de récupération lorsque les nœuds seront remis en ligne et rejoindront le cluster.

Méfiez-vous des mises à jour automatisées des éléments tels que la machine virtuelle Java (dans l’idéal, désactivez les mises à jour automatiques pour ces éléments), en particulier lorsque vous exécutez Elasticsearch sous Windows. L’agent de mise à jour de Java permet de télécharger automatiquement la version la plus récente de Java, mais peut exiger le redémarrage d’Elasticsearch pour que la mise à jour prenne effet. Cela peut entraîner une perte de nœuds temporaire non coordonnée, selon la configuration de l’agent de mise à jour de Java. Cela peut également donner lieu à la présence dans le même cluster de différentes instances d’Elasticsearch, exécutant des versions différentes de la machine virtuelle Java, ce qui peut entraîner des problèmes de compatibilité.

## Test et analyse de la résilience et de la récupération d’Elasticsearch

Cette section décrit une série de tests qui ont été effectués pour évaluer la résilience et la récupération d’un cluster Elasticsearch comprenant trois nœuds de données et trois nœuds principaux.

Quatre scénarios ont été testés :

1.  Défaillance de nœud et redémarrage sans perte de données. Un nœud de données est arrêté, puis redémarré après 5 minutes. Elasticsearch a été configuré pour ne pas réaffecter les partitions manquantes dans cet intervalle, donc aucune Entrée/Sortie supplémentaire n’est engagée dans le déplacement de partitions. Lorsque le nœud redémarre, le processus de récupération réactualise les partitions de ce nœud.

2.  Défaillance de nœud avec perte de données grave. Un nœud de données est arrêté et les données qu’il contient sont effacées pour simuler la défaillance de disque grave. Le nœud est ensuite redémarré (après 5 minutes), et agit en fait en tant que remplacement du nœud d’origine. Le processus de récupération exige la reconstruction des données manquantes pour ce nœud, et peut impliquer la relocalisation des partitions présentes sur d’autres nœuds.

3.  Défaillance de nœud et redémarrage sans perte de données, mais avec réallocation des partitions. Un nœud de données est arrêté et les partitions qu’il contient sont réaffectées à d’autres nœuds. Le nœud est redémarré et d’autres réaffectations ont lieu pour rééquilibrer le cluster.

4.  Déploiement de mises à jour Chaque nœud du cluster est arrêté et redémarré après un court intervalle pour simuler le redémarrage de machines après une mise à jour logicielle. Un seul nœud est arrêté à chaque fois. Les partitions ne sont pas réaffectées pendant l’arrêt d’un nœud.

Chaque scénario a été soumis à la même charge de travail comprenant un mélange de tâches d’intégration de données, d’agrégations et de requêtes de filtre tandis que les nœuds ont été mis hors connexion et récupérés. Les opérations d’insertion en bloc dans la charge de travail ont stocké 1 000 documents chacune, et ont été effectuées sur un index, tandis que les agrégations et les requêtes de filtre ont utilisé un index séparé contenant plusieurs millions de documents. Il s’agissait de permettre l’évaluation séparée des performances des requêtes par rapport aux insertions en bloc. Chaque index comprenait cinq partitions et un réplica.

Les sections suivantes résument les résultats de ces tests, et soulignent toute dégradation de performances lorsqu’un nœud est hors connexion ou en cours de récupération, ainsi que toute erreur signalée. Les résultats sont présentés sous forme de graphique : ils mettent en évidence les points où un ou plusieurs nœuds sont manquants et estiment le temps nécessaire au système pour récupérer entièrement et atteindre un niveau de performances similaire à celui qui existait avant la mise hors connexion des nœuds.

> [AZURE.NOTE] Les ateliers de test utilisés pour exécuter ces tests sont disponibles en ligne. Vous pouvez adapter et utiliser ces ateliers pour vérifier la résilience et la capacité de récupération de vos propres configurations de cluster. Pour plus d’informations, consultez [Running the Automated Elasticsearch Resiliency Tests][] (Exécution de tests de résilience automatisés Elasticsearch).

## Défaillance de nœud et redémarrage sans perte de données : résultats

<!-- TODO; reformat this pdf for display inline -->

Les résultats de ce test s’affichent dans le fichier [ElasticsearchRecoveryScenario1.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticsearchRecoveryScenario1.pdf). Les graphiques indiquent le profil de performances de la charge de travail et les ressources physiques pour chaque nœud dans le cluster. La partie initiale des graphiques montre une exécution normale du système pendant 20 minutes environ, point à partir duquel le nœud 0 est arrêté pendant 5 minutes avant d’être redémarré. Les statistiques pour 20 minutes supplémentaires sont illustrées. Le système prend environ 10 minutes pour récupérer et se stabiliser. Ceci est illustré par les taux de transaction et les temps de réponse des différentes charges de travail.

Notez les points suivants :

- Pendant le test, aucune erreur n’a été signalée. Aucune donnée n’a été perdue, et toutes les opérations se sont terminées avec succès.

- Les taux de transaction des trois types d’opération (insertion en bloc, requête d’agrégation et requête de filtre) ont été supprimés et les temps de réponse moyens ont augmenté tandis que le nœud 0 était hors connexion.

- Pendant la période de récupération, les taux de transaction et les temps de réponse pour la requête d’agrégation et les opérations de requête de filtre ont progressivement été restaurés. Les performances de l’insertion en bloc ont récupéré pendant une courte période avant de diminuer. Toutefois, cela est probablement dû au volume de données à l’origine de la croissance de l’index utilisé par l’insertion en bloc. Les taux de transaction pour cette opération peuvent subir un ralentissement avant même que le nœud 0 soit mis hors connexion.

- Le graphique d’utilisation du processeur pour le nœud 0 indique une activité réduite pendant la phase de récupération. Cela est dû à l’augmentation de l’activité du disque et du réseau provoquée par le mécanisme de récupération. Le nœud doit rattraper toutes les données qu’il a manquées pendant qu’il était hors ligne et mettre à jour les partitions qu’il contient.

- Les partitions pour les index ne sont pas distribuées équitablement entre tous les nœuds. Il existe deux index comprenant 5 partitions et 1 réplica chacun, pour un total de 20 partitions. Deux nœuds contiendront donc 6 partitions tandis que les deux autres en contiendront 7 chacun. Ceci est évident dans les graphiques de l’utilisation du processeur pendant la période initiale de 20 minutes ; le nœud 0 est moins occupé que les deux autres. Une fois la récupération terminée, une commutation semble se produire car le nœud 2 est devenu le moins encombré.

    
## Défaillance de nœud avec perte de données grave : résultats

<!-- TODO; reformat this pdf for display inline -->

Les résultats de ce test sont présentés dans le fichier [ElasticsearchRecoveryScenario2.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticsearchRecoveryScenario2.pdf). Comme pour le premier test, la partie initiale des graphiques montre une exécution normale du système pendant 20 minutes environ, point à partir duquel le nœud 0 est arrêté pendant 5 minutes. Pendant cette période, les données Elasticsearch sur ce nœud sont supprimées, simulant une perte de données grave, avant le redémarrage. La récupération complète semble prendre 12 à 15 minutes avant la restauration des niveaux de performances constatés avant le test.

Notez les points suivants :

- Pendant le test, aucune erreur n’a été signalée. Aucune donnée n’a été perdue, et toutes les opérations se sont terminées avec succès.

- Les taux de transaction des trois types d’opération (insertion en bloc, requête d’agrégation et requête de filtre) ont été supprimés et les temps de réponse moyens ont augmenté tandis que le nœud 0 était hors connexion. À ce stade, le profil de performances du test est similaire au premier scénario. Ce n’est pas surprenant car, à ce stade, les scénarios sont identiques.

- Pendant la période de récupération, les taux de transaction et les temps de réponse ont été restaurés, bien que les chiffres aient connu une volatilité plus importante. Ceci est probablement dû au travail supplémentaire que les nœuds du cluster effectuent, en fournissant les données pour restaurer les partitions manquantes. Ce travail supplémentaire se note clairement dans les graphiques d’utilisation du processeur, d’activité du disque et d’activité réseau.

- Le graphique d’utilisation du processeur pour les nœuds 0 et 1 montre une activité réduite pendant la phase de récupération. Cela est dû à l’augmentation de l’activité du disque et du réseau causée par le processus de récupération. Dans le premier scénario, seul le nœud en cours de récupération affichait ce comportement, mais dans le scénario présent, il est probable que la plupart des données manquantes pour le nœud 0 soient en cours de restauration à partir du nœud 1.

- En fait, l’activité d’E/S du nœud 0 est réduite par rapport au premier scénario. Cela pourrait être dû à l’efficacité des E/S permettant de simplement copier les données d’une partition entière plutôt qu’une série de plus petites demandes d’E/S nécessaires pour mettre à jour une partition existante.

- L’activité réseau des trois nœuds indique des pics d’activité car des données sont transmises et reçues entre les nœuds. Dans le scénario 1, seul le nœud 0 affichait autant d’activité réseau, mais cette activité semblait être soutenue pendant une période plus longue. Là encore, cette différence peut provenir de la pratique efficace qui consiste à transmettre l’ensemble des données d’une partition en une seule requête, plutôt que la série de plus petites requêtes reçues lors de la récupération d’une partition.

## Défaillance de nœud et redémarrage avec réallocation des partitions : résultats

<!-- TODO; reformat this pdf for display inline -->

Le fichier [ElasticsearchRecoveryScenario3.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticsearchRecoveryScenario3.pdf) illustre les résultats de ce test. Comme pour le premier test, la partie initiale des graphiques montre une exécution normale du système pendant 20 minutes environ, point à partir duquel le nœud 0 est arrêté pendant 5 minutes. À ce stade, le cluster Elasticsearch tente de recréer les partitions manquantes et de rééquilibrer les partitions entre les nœuds restants. Après 5 minutes, le nœud 0 est remis en ligne et le cluster doit à nouveau rééquilibrer les partitions. Les performances sont restaurées après 12 à 15 minutes.

Notez les points suivants :

- Pendant le test, aucune erreur n’a été signalée. Aucune donnée n’a été perdue, et toutes les opérations se sont terminées avec succès.

- Les taux de transaction des trois types d’opération (insertion en bloc, requête d’agrégation et requête de filtre) ont été supprimés et les temps de réponse moyens ont considérablement augmenté tandis que le nœud 0 était hors connexion, par rapport aux deux tests précédents. Cela est dû à une activité accrue du cluster, qui recrée les partitions manquantes et rééquilibre le cluster, comme le montrent les chiffres mis en évidence pour l’activité du disque et du réseau des nœuds 1 et 2 au cours de cette période.

- Pendant la période après que le nœud 0 est remis en ligne, les taux de transaction et les temps de réponse restent volatils.

- Les graphiques d’utilisation du processeur et d’activité du disque du nœud 0 présentent une action initiale très réduite pendant la phase de récupération. En effet, à ce stade, le nœud 0 ne délivre pas de données. Après une période d’environ 5 minutes, le nœud passe subitement à l’action ce qui se manifeste par l’augmentation soudaine de l’activité du réseau, du disque et du processeur. Cela est probablement dû à la redistribution des partitions entre les nœuds par le cluster. Le nœud 0 affiche ensuite une activité normale.
  
## Déploiement de mises à jour : Résultats

<!-- TODO; reformat this pdf for display inline -->

Les résultats de ce test, dans le fichier [ElasticsearchRecoveryScenario4.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticsearchRecoveryScenario4.pdf), montrent comment chaque nœud est successivement mis hors connexion, puis remis en ligne. Chaque nœud est arrêté pendant 5 minutes avant d’être redémarré, moment auquel le nœud suivant dans la séquence est arrêté.

Notez les points suivants :

- Bien que chaque nœud soit cyclique, les performances en termes de débit et de temps de réponse restent sensiblement identiques.

- L’activité du disque augmente car chaque nœud augmente pendant une courte période pendant qu’il est remis en ligne. Cela est très probablement dû au processus de récupération de restauration par progression de toutes les modifications survenues pendant que le nœud était arrêté.

- Lorsqu’un nœud est mis hors connexion, les pics d’activité réseau se produisent dans les nœuds restants. Des pics peuvent également se produire lorsqu’un nœud est redémarré.

- Une fois que le nœud final est recyclé, le système entre dans une période de volatilité importante. Cela est probablement causé par le processus de récupération qui doit synchroniser les modifications sur tous les nœuds et s’assurer que tous les réplicas et leurs partitions correspondantes sont cohérents. À un moment, cette tâche entraîne l’expiration et l’échec des opérations d’insertion. Dans chaque cas, les erreurs signalées ont été :

```
Failure -- BulkDataInsertTest17(org.apache.jmeter.protocol.java.sampler.JUnitSampler$AnnotatedTestCase): java.lang.AssertionError: failure in bulk execution:
[1]: index [systwo], type [logs], id [AVEg0JwjRKxX_sVoNrte], message [UnavailableShardsException[[systwo][2] Primary shard is not active or isn't assigned to a known node. Timeout: [1m], request: org.elasticsearch.action.bulk.BulkShardRequest@787cc3cd]]

```

Les expérimentations suivantes ont montré que l’introduction d’un retard de quelques minutes entre les cycles de chaque nœud éliminait cette erreur. Par conséquent, elle était probablement causée par la contention entre le processus de récupération essayant de restaurer simultanément plusieurs nœuds et les opérations d’insertion en bloc essayant de stocker des milliers de nouveaux documents.


## Résumé

Les tests effectués ont indiqué que :

- Elasticsearch était hautement résilient aux modes de défaillance courants susceptibles de se produire dans un cluster.

- Elasticsearch peut récupérer rapidement si un cluster bien conçu est soumis à une perte de données grave sur un nœud. Cela peut se produire si vous configurez Elasticsearch pour enregistrer les données dans le stockage éphémère et que le nœud est ensuite reconfiguré après un redémarrage. Ces résultats montrent que même dans ce cas, les risques liés à l’utilisation du stockage éphémère sont probablement compensés par les avantages de performances que fournit cette classe de stockage.

- Dans les trois premiers scénarios, aucune erreur n’est survenue lors des charges de travail simultanées d’insertion en bloc, d’agrégation et de requête de filtre pendant la mise hors connexion et la récupération du nœud.

- Seul le scénario 4 a indiqué une perte de données potentielle, et cette perte a uniquement affecté les nouvelles données ajoutées. Dans les applications effectuant l’intégration des données, il est recommandé d’atténuer ce risque en renouvelant les opérations d’insertion qui ont échoué, car l’erreur signalée est très probablement de type temporaire.

- Les résultats du test 4 montrent également que si vous effectuez une maintenance planifiée des nœuds dans un cluster, les performances en seront améliorées si vous laissez plusieurs minutes entre les cycles des nœuds. Dans une situation non planifiée (par exemple, le centre de données recyclant les nœuds après avoir effectué une mise à jour du système d’exploitation), vous avez moins de contrôle sur la manière dont les nœuds sont retirés et redémarrés et le moment où cela se produit. La contention qui survient lorsqu’Elasticsearch essaie de récupérer l’état du cluster après les pannes de nœud séquentiel peut entraîner des expirations et des erreurs.

[Gestion de la disponibilité des machines virtuelles]: ../articles/virtual-machines/virtual-machines-manage-availability.md
[Running the Automated Elasticsearch Resiliency Tests]: guidance-elasticsearch-running-automated-resilience-tests.md

<!---HONumber=AcomDC_0224_2016-->