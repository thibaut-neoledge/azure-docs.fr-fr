<properties
   pageTitle="Exécuter Elasticsearch sur Azure | Microsoft Azure"
   description="Comment installer, configurer et exécuter Elasticsearch sur Azure."
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
   ms.date="02/05/2016"
   ms.author="mabsimms"/>

# Exécuter Elasticsearch sur Azure

Cet article fait [partie d’une série](guidance-elasticsearch-introduction.md).

## Vue d'ensemble

Elasticsearch est un moteur de recherche open source hautement évolutif utilisant une base de données. Il convient aux situations nécessitant une analyse et une découverte rapides des informations contenues dans des jeux de données volumineux. Scénarios courants :

- Recherche en texte libre à grande échelle, ce qui permet de localiser et d’extraire rapidement les documents correspondant aux combinaisons de termes de la recherche.
- Enregistrement des événements, les informations pouvant provenir de différentes sources. Les données doivent parfois être analysées pour s’assurer de la manière dont les événements ont conduit à une conclusion spécifique.
- Stockage des données capturées à partir d’appareils distants et d’autres sources. Les données peuvent contenir des informations variées, mais il est généralement préférable que ces informations soient présentées dans une série de tableaux de bord pour permettre à l’opérateur de comprendre l’état du système global. Les applications peuvent également utiliser les informations pour prendre rapidement des décisions concernant le flux de données et les opérations métiers qui doivent être effectuées en conséquence.
- Contrôle des stocks, consistant dans l’enregistrement des modifications de l’inventaire à mesure que les biens sont vendus. Les systèmes commerciaux peuvent utiliser ces informations pour signaler les niveaux de stock aux utilisateurs, et commander du stock supplémentaire si un produit est sur le point de manquer. Les analystes peuvent examiner les données afin de déterminer les tendances et d’identifier quels produits se vendent bien et dans quelles circonstances.
- Analyse financière, les informations sur le marché arrivant quasiment en temps réel. Il est possible de générer des tableaux de bord indiquant les performances des divers instruments financiers. Ces informations peuvent ensuite être utilisées pour prendre des décisions d’achat/vente.

Ce document présente brièvement la structure générale d’Elasticsearch, puis explique comment implémenter un cluster Elasticsearch à l’aide d’Azure. Le document est principalement axé sur les meilleures pratiques de déploiement d’un cluster Elasticsearch. Il se concentre en particulier sur les différentes exigences de performance fonctionnelle et de gestion de votre système, et explique en quoi ces exigences doivent influer sur la configuration et la topologie choisies.

> [AZURE.NOTE] Pour suivre ces instructions, vous devez déjà avoir des connaissances de base [d’Elasticsearch][].

## Structure d’Elasticsearch

Elasticsearch est une base de données de documents hautement optimisée pour servir de moteur de recherche. Les documents sont sérialisés au format JSON. Les données sont stockées dans des index, implémentés à l’aide de [Apache Lucene][]. Toutefois, les détails n’apparaissent pas dans la vue et il n’est pas nécessaire de maîtriser Lucene pour pouvoir utiliser Elasticsearch.

### Clusters, nœuds, index et partitions

Elasticsearch implémente une architecture en cluster qui utilise le partitionnement pour répartir les données sur plusieurs nœuds et la réplication pour assurer la haute disponibilité.

Les documents sont stockés dans des index. L’utilisateur peut spécifier les champs utilisés dans un document pour l’identifier de manière unique dans un index, ou bien le système peut générer automatiquement un champ et des valeurs clés. L’index permet d’organiser les documents physiquement. Il est le principal moyen utilisé pour rechercher des documents. En outre, Elasticsearch crée automatiquement un ensemble de structures supplémentaires qui sont des index inversés des champs restants et permettent d’effectuer des recherches rapides et des analyses au sein d’une collection.

Un index comprend un ensemble de partitions. Les documents sont répartis uniformément au sein des partitions à l’aide d’un mécanisme de hachage basé sur les valeurs de clé d’index et le nombre de partitions dans l’index. Une fois qu’un document est attribué à une partition, il n’est plus déplacé, sauf si sa clé d’index change. Elasticsearch répartit les partitions sur l’ensemble des nœuds de données disponibles dans un cluster ; un seul nœud peut contenir initialement une ou plusieurs partitions appartenant au même index, mais à mesure que des nœuds supplémentaires sont ajoutés au cluster, Elasticsearch déplace les partitions pour équilibrer la charge sur le système. Le même rééquilibrage est effectué lorsque des nœuds sont supprimés.

Les index peuvent être répliqués. Dans ce cas, chaque partition d’un index est copiée. Elasticsearch fait en sorte que chaque partition d’origine d’un index (appelée « partition principale ») et son réplica résident sur des nœuds différents.

> [AZURE.NOTE] Le nombre de partitions dans un index ne peut pas être facilement modifié une fois l’index créé, mais il est possible d’ajouter des réplicas.

Lorsqu’un document est ajouté ou modifié, toutes les opérations d’écriture sont effectuées sur la partition principale, puis au niveau de chaque réplica. Par défaut, ce processus est exécuté de façon synchrone afin de garantir la cohérence. Lors de l’écriture des données, Elasticsearch utilise l’accès concurrentiel optimiste avec contrôle de version. Les opérations de lecture peuvent effectuées à l’aide de la partition principale ou de l’un de ses réplicas.

La figure 1 illustre les aspects essentiels d’un cluster Elasticsearch composé de trois nœuds. Un index comprenant deux partitions principales avec deux réplicas de chaque partition (six partitions en tout) a été créé.

![](media/guidance-elasticsearch-general-cluster1.png)

**Figure 1 :**
Cluster Elasticsearch simple comprenant deux nœuds principaux et deux jeux de réplicas

Dans ce cluster, la partition principale 1 et la partition principale 2 se trouvent sur des nœuds distincts pour équilibrer la charge. Les réplicas sont répartis de la même façon. Si un nœud est défaillant, les nœuds restants ont suffisamment d’informations pour permettre au système de continuer à fonctionner ; si nécessaire, Elasticsearch promeut une partition de réplica pour qu’elle devienne partition principale, dans la mesure où la partition principale correspondante n’est pas disponible.
Lorsqu’un nœud commence à s’exécuter, il peut amorcer un nouveau cluster (s’il s’agit du premier nœud du cluster) ou rejoindre un cluster existant. Le cluster auquel le nœud appartient est déterminé par le paramètre *cluster.name* dans le fichier elasticsearch.yml.

### Rôles des nœuds

Dans un cluster Elasticsearch, les nœuds peuvent avoir les rôles suivants :

- Nœud de données pouvant contenir une ou plusieurs partitions remplies de données d’index.
- Nœud client ne contenant pas de données d’index, mais gérant les demandes entrantes adressées au nœud de données approprié par les applications clientes.
- Nœud principal ne contenant pas de données d’index, mais exécutant les opérations de gestion du cluster, telles que la maintenance et la distribution des informations de routage dans le cluster (la liste des nœuds et des partitions qu’ils contiennent), qui la détermination des nœuds disponibles, le déplacement des partitions à mesure que des nœuds apparaissent et disparaissent, la coordination de la récupération après la défaillance d’un nœud. Il est possible de configurer plusieurs nœuds comme nœuds principaux, mais un seul sera effectivement choisi pour exécuter les fonctions principales. Si ce nœud est défaillant, une autre sélection est effectuée et l’un des autres nœuds principaux est choisi pour prendre la relève.

Par défaut, les nœuds Elasticsearch endossent les trois rôles (pour vous permettre de créer un cluster opérationnel complet sur un seul ordinateur à des fins de développement et de démonstration), mais vous pouvez modifier leurs opérations via les paramètres *node.data* et *node.master* dans le fichier *elasticsearch.yml*, comme suit :

```yaml
node.data: true
node.master: false
```

**Configuration pour un nœud de données**

```yaml
node.data: false
node.master: false
```

**Configuration pour un nœud client**

```yaml
node.data: false
node.master: true
```

**Configuration pour un nœud principal**

> [AZURE.NOTE] Le nœud principal choisi est indispensable au bon fonctionnement du cluster. Les autres nœuds lui envoient régulièrement des commandes ping pour s’assurer qu’il est toujours disponible. Si le nœud principal choisi est également un nœud de données, il y a de fortes chances pour qu’il soit occupé et ne puisse pas répondre aux commandes ping. Dans ce cas, le nœud principal est supposé défaillant et l’un des autres nœuds principaux est choisi à sa place. S’il s’avère que le nœud principal est en fait toujours disponible, le cluster se retrouve avec deux nœuds principaux choisis, provoquant un syndrome « Split-Brain » et potentiellement une corruption des données ou d’autres problèmes. Le document Configuring, Testing, and Analyzing Elasticsearch Resilience and Recovery (Configurer, tester et analyser la résilience et la récupération d’Elasticsearch) décrit comment configurer le cluster pour réduire les risques que cela se produise. Toutefois, dans un cluster d’envergure modérée à grande, la meilleure stratégie consiste à utiliser des nœuds principaux dédiés qui ne prennent pas en charge la gestion des données.

Les nœuds d’un cluster partagent des informations sur les autres nœuds du cluster (par le biais d’un [protocole de bavardage (gossip protocol)][] (en anglais)) et sur les partitions qu’ils contiennent. Les applications clientes de stockage et d’extraction des données peuvent se connecter à n’importe quel nœud d’un cluster et les demandes sont acheminées de façon transparente vers le nœud approprié. Lorsqu’une application cliente demande les données d’un cluster, le nœud qui reçoit la demande en premier est chargé de prendre en charge l’opération. Il communique avec chaque nœud pour extraire les données, puis il agrège les résultats avant de les renvoyer vers l’application cliente. L’utilisation des nœuds client pour gérer les demandes évite aux nœuds de données d’avoir à effectuer cette tâche de ventilation/regroupement. Ils peuvent ainsi être entièrement dédiés à la transmission des données. Vous pouvez empêcher les applications clientes de communiquer par inadvertance avec des nœuds de données (pour qu’ils agissent comme des nœuds client) en désactivant le transport HTTP pour les nœuds de données :

```yaml
http.enabled: false
```

Les nœuds de données peuvent toujours communiquer avec d’autres nœuds de données, des nœuds client et des nœuds principaux dédiés sur le même réseau en utilisant le module de transport Elasticsearch (qui utilise des sockets TCP pour se connecter directement entre les nœuds), mais les applications clientes peuvent seulement se connecter aux nœuds client sur HTTP. La figure 2 illustre une topologie composée d’une combinaison de nœuds dédiés (principal, client et données) dans un cluster Elasticsearch.

![](media/guidance-elasticsearch-general-cluster2.png)

**Figure 2 :**
Cluster Elasticsearch composé de différents types de nœuds

### Coûts et avantages de l’utilisation de nœuds client

Lorsqu’une application envoie une requête vers un cluster Elasticsearch, il incombe au nœud auquel l’application se connecte de prendre en charge le processus de requête. Le nœud transfère la requête à chaque nœud de données et rassemble les résultats, en renvoyant les informations accumulées vers l’application. Si une requête implique des agrégations et d’autres calculs, le nœud auquel l’application se connecte effectue les opérations nécessaires après avoir récupéré les données sur chacun des autres nœuds. Ce processus de ventilation-regroupement peut consommer une quantité considérable de ressources de traitement et de mémoire.

L’utilisation de nœuds client dédiés pour effectuer ces tâches permet aux nœuds de données de se concentrer sur la gestion et le stockage des données. De nombreux scénarios impliquant des agrégations et des requêtes complexes peuvent donc tirer parti de l’utilisation de nœuds client dédiés. Toutefois, l’impact de l’utilisation de nœuds client dédiés peut varier en fonction de votre scénario, charge de travail et taille de cluster. Par exemple, les charges de travail d’ingestion des données risquent d’être moins efficaces en utilisant des nœuds client en raison du « saut » supplémentaire sur le réseau requis lors du stockage de données. Dans un cluster à 3 nœuds avec 6 partitions, si le système n’est pas configuré avec des nœuds client dédiés, avec des facteurs environnementaux et des charges de nœud identiques, il existe une probabilité de 1 sur 3 qu’une application stockant ou modifiant les données se connecte directement à la partition la plus appropriée, éliminant le besoin d’effectuer un saut réseau supplémentaire dans 1 cas sur 3. En revanche, les charges de travail qui effectuent des agrégations complexes peuvent tirer parti de l’utilisation de clients dédiés, car un seul nœud se charge de chaque jeu d’opérations de ventilation-regroupement exécuté. Dans un environnement mixte, il se peut que vous deviez exécuter des tests de performance pour évaluer l’impact de l’utilisation de nœuds client sur vos charges de travail spécifiques.

> [AZURE.NOTE] Le document Maximizing Data Aggregation and Query Performance with Elasticsearch on Azure (Maximiser les performances d’agrégation et de requête avec Elasticsearch sur Azure) rappelle l’ensemble des évaluations comparatives effectuées par l’équipe de développement des modèles et des pratiques de Microsoft, en partie dans cet objectif.

### Connexion à un cluster

Elasticsearch expose une série d’API REST pour créer des applications clientes et envoyer des requêtes vers un cluster. Si vous développez des applications à l’aide de .NET Framework, deux API de niveaux supérieurs sont disponibles : [Elasticsearch.Net et NEST][].

Si vous développez des applications clientes à l’aide de Java, vous pouvez utiliser [l’API de nœud client][] pour créer dynamiquement des nœuds client et les ajouter au cluster. La création dynamique de nœuds client est pratique si votre système utilise un nombre relativement restreint de connexions à long terme. Les nœuds client créés à l’aide de l’API de nœud sont fournies avec la carte de routage du cluster (qui détaille quels nœuds contiennent quelles partitions) par le nœud principal. Ces informations permettent à l’application Java de se connecter directement aux nœuds appropriés lors de l’indexation ou de l’interrogation de données, ce qui réduit le nombre de sauts parfois nécessaires lorsque vous utilisez d’autres API. Le coût de cette approche équivaut aux frais d’inscription du nœud client dans le cluster. Si un grand nombre de nœuds client apparaissent et disparaissent rapidement, l’impact de la maintenance et de la distribution de la carte de routage du cluster peut devenir significatif.

**Équilibrage de la charge de connexion**

Elasticsearch permet d’utiliser plusieurs mécanismes pour implémenter l’équilibrage de la charge de connexion. La liste suivante répertorie certaines approches courantes :

**Équilibrage de la charge basé sur le client** : si vous créez des applications clientes à l’aide des API Elasticsearch.Net ou NEST, vous pouvez utiliser un pool de connexions pour les demandes de connexion de type Tourniquet (round-robin) entre les nœuds, ce qui permet d’équilibrer la charge des demandes sans recourir à un équilibreur de charge externe. L’extrait de code suivant montre comment créer un objet *ElasticsearchClient* configuré avec les adresses de trois nœuds. Les demandes de l’application cliente sont réparties entre ces nœuds :

```csharp
// C#
var node1 = new Uri("http://node1.example.com:9200");
var node2 = new Uri("http://node2.example.com:9200");
var node3 = new Uri("http://node3.example.com:9200");

var connectionPool = new SniffingConnectionPool(new[] {node1, node2, node3});
var config = new ConnectionConfiguration(connectionPool);
var client = new ElasticsearchClient(config);
```

> [AZURE.NOTE] Une fonctionnalité similaire est disponible pour les applications Java via [l’API de client de transport][].

**Équilibrage de la charge basé sur le serveur** : vous pouvez utiliser un équilibreur de charge distinct pour distribuer les demandes aux nœuds. Cette approche présente l’avantage de la transparence d’adresse ; il est inutile de configurer les applications clientes avec les détails de chaque nœud, ce qui facilite l’ajout, la suppression ou le déplacement des nœuds sans modifier le code client. La figure 3 illustre une configuration qui utilise un équilibreur de charge pour acheminer les demandes vers un ensemble de nœuds client, bien que la même stratégie puisse être utilisée pour se connecter directement aux nœuds de données si les nœuds client ne sont pas utilisés.

> [AZURE.NOTE] Vous pouvez utiliser [l’équilibreur de charge Azure][] pour exposer le cluster sur le réseau Internet public, ou vous pouvez utiliser un [équilibreur de charge interne][] si le cluster et les applications clientes sont contenus entièrement au sein du même réseau virtuel (VNET) privé.

![](media/guidance-elasticsearch-general-clientappinstances.png)

**Figure 3.**
Instances de l’application cliente se connectant à un cluster Elasticsearch via l’équilibreur de charge Azure

**Équilibrage de charge personnalisé** : vous pouvez utiliser [nginx][] comme serveur proxy inverse à la place de l’équilibreur de charge Azure. Nginx fournit plusieurs méthodes d’équilibrage de la charge, notamment le tourniquet (round robin), le choix du moins connecté (une requête est acheminée vers la destination ayant le moins de connexions en cours) et le hachage basé sur l’adresse IP du client.

> [AZURE.NOTE] Vous pouvez déployer un serveur nginx comme une machine virtuelle Azure. Pour préserver la disponibilité, vous pouvez créer au moins deux serveurs nginx dans le même groupe à haute disponibilité Azure.

Vous devez prendre en compte les points suivants lorsque vous décidez d’utiliser ou non l’équilibrage de charge, et quelle implémentation utiliser :

- La connexion à un nœud identique pour gérer toutes les requêtes de toutes les instances d’une application peut créer un goulot d’étranglement au niveau de ce nœud. Comme le nombre de threads disponibles dans le nœud est épuisé, les demandes sont mises en attente et peuvent être rejetées si la longueur de la file d’attente devient excessive (ne codez pas en dur les informations de connexion d’un seul nœud dans le code de l’application qui risque d’être déployée vers nombreux utilisateurs).
- Le mécanisme de tourniquet (round robin) des API Elasticsearch.Net, NEST et de client de transport gèrent les échecs de demande de connexion en retentant la connexion sur le prochain nœud disponible dans le pool de connexions. Une connexion à un nœud qui ne répond pas dans le pool peut être temporairement marquée comme *inactive* ; elle peut redevenir active ultérieurement. Le pool peut envoyer une commande ping au nœud pour le vérifier.
- L’équilibreur de charge Azure peut rediriger de manière transparente les demandes vers les nœuds en fonction de différents facteurs (adresse IP du client, port du client, adresse IP de destination, port de destination, type de protocole). Avec cette stratégie, une instance d’application cliente exécutée sur un ordinateur donné a de fortes chances d’être dirigée vers le même nœud Elasticsearch. Selon la configuration de la sonde de l’équilibreur de charge, si le service Elasticsearch est défaillant sur ce nœud, mais que la machine virtuelle continue à s’exécuter, toutes les connexions à ce nœud expirent tandis que les connexions d’autres instances clientes à d’autres nœuds peuvent continuer à fonctionner.
- L’équilibreur de charge Azure peut être configuré pour sortir un nœud du tourniquet si celui-ci ne répond pas de manière appropriée aux demandes d’analyse d’intégrité exécutées par l’équilibreur de charge.

### Découverte des nœuds

Elasticsearch est basé sur des communications pair à pair. La découverte d’autres nœuds dans un cluster est donc une partie essentielle du cycle de vie d’un nœud. La découverte des nœuds permet d’ajouter dynamiquement de nouveaux nœuds de données à un cluster, et par conséquence d’augmenter la taille du cluster en toute transparence. Par ailleurs, si un nœud de données ne parvient pas à répondre aux demandes de communications d’autres nœuds, un nœud principal peut décider que le nœud de données est défaillant et prendre les mesures nécessaires pour réallouer les partitions qu’il contient à d’autres nœuds de données opérationnels.

La découverte des nœuds Elasticsearch est gérée à l’aide d’un module de découverte. Le module de découverte est un plug-in qui peut être basculé pour utiliser un autre mécanisme de découverte. Avec le module de découverte par défaut ([Zen][]), un nœud émet des demandes ping pour rechercher d’autres nœuds sur le même réseau. Si d’autres nœuds répondent, ils bavardent pour échanger des informations. Un nœud principal peut ensuite distribuer les partitions sur le nouveau nœud (s’il s’agit d’un nœud de données) et rééquilibrer le cluster.

Le module de découverte Zen gère également le processus de sélection du nœud principal et le protocole de détection de défaillance de nœud.

Avant Elasticsearch version 2.0, le module de découverte Zen utilisait les communications multidiffusion pour permettre aux nœuds de se contacter mutuellement. Cela permet d’introduire très facilement un nouveau nœud dans un cluster, mais peut également provoquer des problèmes de sécurité si une autre installation d’Elasticsearch sur le même réseau utilise le même nom de cluster. La nouvelle installation est considérée comme faisant partie du même cluster et les partitions peuvent être dirigées vers des nœuds de cette installation. En outre, si vous exécutez des nœuds Elasticsearch en tant que machines virtuelles Azure, la messagerie multidiffusion n’est pas prise en charge. Pour ces raisons, vous devez configurer la découverte Zen pour qu’elle utilise la messagerie monodiffusion et fournisse une liste de nœuds de contact valides dans le fichier de configuration elasticsearch.yml.

> [AZURE.NOTE] Avec Elasticsearch version 2.0 et ultérieures, la multidiffusion n’est plus le mécanisme de découverte par défaut.

Si vous hébergez un cluster Elasticsearch au sein d’un réseau virtuel Azure, vous pouvez spécifier que l’adresse IP privée attribuée par DHCP donnée à chaque machine virtuelle dans le cluster doit rester allouée (statique). Vous pouvez configurer la messagerie monodiffusion de la découverte Zen à l’aide de ces adresses IP statiques. Si vous utilisez des machines virtuelles avec des adresses IP dynamiques, n’oubliez pas que si une machine virtuelle s’arrête et redémarre, une nouvelle adresse IP peut lui être allouée, rendant la détection plus difficile. Pour gérer ce scénario, vous pouvez remplacer le module de découverte Zen par le [plug-in Azure Cloud][]. Ce plug-in utilise l’API Azure pour implémenter le mécanisme de découverte, qui est basé sur les informations d’abonnement Azure.

> [AZURE.NOTE] Avec la version actuelle du plug-in Azure Cloud, vous devez installer le certificat de gestion de votre abonnement Azure dans le keystore Java sur le nœud Elasticsearch, et fournir l’emplacement et les informations d’identification pour accéder au keystore dans le fichier elasticsearch.yml. Ce fichier est rédigé en texte clair. Il est donc essentiel que vous vérifiez que ce fichier est uniquement accessible par le compte exécutant le service Elasticsearch. En outre, cette approche peut ne pas être compatible avec les déploiements Azure Resource Manager (ARM). Pour ces raisons, nous vous recommandons d’utiliser des adresses IP statiques pour les nœuds principaux, et d’utiliser ces nœuds pour implémenter la messagerie monodiffusion de la découverte Zen au sein du cluster. Dans la configuration ci-après (issue du fichier elasticsearch.yml pour un exemple de nœud de données), les adresses IP des hôtes référencent les nœuds principaux dans le cluster :

>`discovery.zen.ping.multicast.enabled: false`  
`discovery.zen.ping.unicast.hosts: ["10.0.0.10","10.0.0.11","10.0.0.12"]`

## Instructions générales relatives au système

Elasticsearch peut s’exécuter sur différents ordinateurs, d’un simple ordinateur portable à un cluster de serveurs haut de gamme. Cependant, plus il y a de ressources disponibles (mémoire, puissance de calcul et disques rapides), plus les performances sont excellentes. Les sections suivantes résument la configuration matérielle et logicielle requise pour exécuter Elasticsearch.

### Configuration mémoire requise
Elasticsearch tente de stocker les données en mémoire pour aller plus vite. Un serveur de production hébergeant un nœud pour un déploiement dans une entreprise standard ou dans des locaux commerciaux de taille modérée sur Azure doit avoir entre 14 et 28 Go de RAM (machines virtuelles D3 ou D4). Répartissez la charge sur un plus grand nombre de nœuds au lieu de créer des nœuds avec plus de mémoire (les expériences menées ont montré que l’utilisation de nœuds plus grands ayant plus de mémoire peut entraîner des temps de récupération plus longs en cas de défaillance). Toutefois, même si la création de clusters avec un très grand nombre de nœuds de petite taille peut augmenter la disponibilité et le débit, elle accroît également les efforts nécessaires pour gérer et entretenir un tel système.

Allouez 50 % de la mémoire disponible sur un serveur au segment de mémoire Elasticsearch. Si vous utilisez Linux, définissez la variable d’environnement ES\_HEAP\_SIZE avant d’exécuter Elasticsearch. Ou bien, si vous utilisez Windows ou Linux, spécifiez la taille de la mémoire dans les paramètres *Xmx* et *Xms* lorsque vous démarrez Elasticseach ; définissez ces deux paramètres sur la même valeur pour éviter que la machine virtuelle Java (JVM) redimensionne le segment de mémoire lors de l’exécution. Toutefois, n’allouez pas plus de 30 Go. Utilisez le reste de la mémoire pour le cache de fichiers du système d’exploitation.

> [AZURE.NOTE] Elasticsearch utilise la bibliothèque Lucene pour créer et gérer des index. Les structures de Lucene utilisent un format basé sur le disque, et le fait de mettre en cache ces structures dans le cache du système de fichiers améliore considérablement les performances.

> En outre, Elasticsearch est écrit en Java. La taille maximale de segment de mémoire optimal pour Java sur un ordinateur 64 bits est légèrement supérieure à 30 Go. Au-dessus de cette taille, Java bascule vers l’utilisation d’un mécanisme étendu pour référencer les objets sur le segment de mémoire, ce qui augmente les besoins en mémoire pour chaque objet et réduit les performances. Le récupérateur de mémoire Java par défaut (marquage et balayage simultanés) peut également fonctionner de manière peu satisfaisante si la taille du segment de mémoire est supérieure à 30 Go. Actuellement, il n’est pas recommandé de basculer vers un autre récupérateur de mémoire, car Elasticsearch et Lucene ont uniquement été testés avec celui utilisé par défaut.

Ne surexploitez pas la mémoire car le fait de remplacer la mémoire principale sur le disque a un impact très négatif sur les performances. Si possible, désactivez complètement le remplacement (les détails varient selon le système d’exploitation). Si cela n’est pas possible, activez le paramètre *mlockall* dans le fichier de configuration Elasticsearch (elasticsearch.yml) comme suit :

```yaml
bootstrap.mlockall: true
```

Ce paramètre de configuration pousse la machine virtuelle Java à verrouiller sa mémoire et empêche que celle-ci soit remplacée par le système d’exploitation.

### Configuration requise pour les disques et le système de fichiers

Utilisez des disques SSD pour stocker les partitions. Les disques doivent être dimensionnés pour contenir la quantité maximale de données prévues dans vos partitions. Toutefois, il est possible d’ajouter des disques supplémentaires ultérieurement. Vous pouvez étendre une partition sur plusieurs disques au sein d’un nœud.

> [AZURE.NOTE] Elasticsearch compresse les données des champs stockés en utilisant l’algorithme LZ4. Dans Elasticsearch version 2.0 et ultérieures, vous pouvez modifier le type de compression. Vous pouvez remplacer l’algorithme de compression par DEFLATE, qui est utilisé par les utilitaires *zip* et *gzip*. Cette technique de compression peut être plus gourmande en ressources, mais vous pouvez envisager de l’utiliser pour les index inactifs tels que les données des journaux archivés. Cette approche peut contribuer à réduire la taille de l’index.

Il n’est pas essentiel que tous les nœuds d’un cluster aient la même disposition et capacité de disque. Toutefois, un nœud ayant une très grande capacité de disque par rapport aux autres nœuds d’un cluster attire davantage de données et exige une puissance de traitement accrue pour gérer ces données. Par conséquent, le nœud peut rapidement être surchargé par rapport aux autres nœuds et cela peut, à son tour, affecter les performances.

> [AZURE.NOTE] Azure propose plusieurs options de stockage sur disque, notamment le stockage standard, premium et éphémère. Le stockage standard est basé sur des disques rotatifs, alors que le stockage premium utilise des disques SSD. En fonction de la référence utilisée pour implémenter une machine virtuelle, le stockage éphémère peut être implémenté en tant que disques rotatifs (machines virtuelles de série A) ou disques SSD (machines virtuelles de série D et supérieures). Vous devez penser aux compromis financiers et techniques lorsque vous réfléchissez aux options de stockage. Ils sont décrits plus en détail dans le document Maximizing Data Ingestion Performance with Elasticsearch on Azure (Maximiser les performances d’ingestion des données avec Elasticsearch sur Azure).

Si possible, utilisez RAID 0 (entrelacement). Les autres formes de RAID qui implémentent la parité et la mise en miroir sont inutiles car Elasticsearch fournit sa propre solution de haute disponibilité sous la forme de réplicas, et les disques Azure enregistrent de toute façon trois copies des données de disque.

> [AZURE.NOTE] Avant Elasticsearch 2.0.0, il était également possible d’implémenter l’entrelacement au niveau du logiciel en spécifiant plusieurs répertoires dans le paramètre de configuration *path.data*. Dans Elasticsearch 2.0.0, cette forme d’entrelacement n’est plus prise en charge. Au lieu de cela, il est possible d’allouer différentes partitions à différents chemins d’accès. Toutefois, tous les fichiers d’une partition sont écrits dans le même chemin d’accès. Si l’entrelacement est requis, nous vous recommandons d’entrelacer les données au niveau du matériel ou du système d’exploitation.

Vous devez également savoir que le débit total d’E/S des disques Azure attachés à une machine virtuelle est limité par le groupe de stockage auquel ils appartiennent ; un compte de stockage standard unique peut gérer un débit de demandes jusqu’à 20 000 IOPS (cette restriction ne s’applique pas au stockage premium). Si ce chiffre est inférieur au trafic prévu d’E/S des disques, répartissez les disques des machines virtuelles dans votre cluster sur plusieurs comptes de stockage, en tenant compte du fait qu’un seul abonnement peut créer jusqu’à 100 comptes de stockage.

La bibliothèque Lucene peut utiliser un grand nombre de fichiers pour stocker des données d’index, et Elasticsearch peut ouvrir un nombre considérable de sockets pour la communication entre les nœuds et avec les clients. Assurez-vous que le système d’exploitation est configuré pour prendre en charge un nombre suffisant de descripteurs de fichiers ouverts (jusqu’à 64 000 si une mémoire suffisante est disponible). Notez que la configuration par défaut pour de nombreuses distributions Linux limite le nombre de descripteurs de fichiers ouverts à 1 024, ce qui est bien trop peu.

Elasticsearch utilise une combinaison d’E/S mappées en mémoire (mmap) et Java New IO (NIO) pour optimiser l’accès simultané aux fichiers de données et aux index. Si vous utilisez Linux, vous devez configurer le système d’exploitation pour garantir que la mémoire virtuelle disponible est suffisante et qu’un espace pour les zones mmap de 256 Ko est prévu.

> [AZURE.NOTE] Par défaut, un grand nombre de distributions Linux utilisent le planificateur CFQ (Completely Fair Queuing) lors de l’organisation de l’écriture des données sur le disque. Ce planificateur n’est pas optimisé pour les disque SSD. Reconfigurez le système d’exploitation pour utiliser le planificateur NOOP ou le planificateur Deadline, qui sont plus efficaces pour les disques SSD.

### Configuration requise pour les processeurs

Utilisez des processeurs avec plusieurs cœurs. Il est préférable d’utiliser plus de cœurs plutôt que des processeurs plus rapides avec moins de cœurs. En effet, la taille par défaut du pool de threads d’Elasticsearch est configurée en fonction du nombre de cœurs de processeurs disponibles. Les algorithmes utilisés par Elasticsearch sont optimisés en fonction de ces calculs, et nous vous recommandons de ne pas changer les paramètres par défaut du pool de threads d’Elasticsearch.

> [AZURE.NOTE] Les machines virtuelles Azure sont disponibles dans diverses configurations de processeurs prenant en charge 1 à 32 cœurs. Pour un nœud de données, il convient de commencer par une machine virtuelle standard de série D, puis de choisir les références D3 (4 cœurs) ou D4 (8 cœurs). La machine virtuelle D3 fournit également 14 Go de RAM, tandis que la D4 inclut 28 Go. Pour un stockage sur disque premium, vous pouvez utiliser une machine virtuelle de série DS (par exemple une DS3 ou DS4), alors que la série Dv2 fournit des processeurs Intel Xeon à 2,4 GHz plus puissants, ce qui convient mieux si vous pensez que les performances des processeurs vont jouer un rôle critique dans la limitation du débit. La série G (pour le stockage standard) et la série GS (pour le stockage premium) utilisent des processeurs Xeon E5 V3 qui peuvent être utiles pour les charges de travail très intensives, comme les agrégations à grande échelle. Pour des informations récentes, visitez la page [Tailles de machines virtuelles][].

### Configuration requise pour le réseau

Elasticsearch nécessite une bande passante réseau entre 1 et 10 Gbit/s, selon la taille et la volatilité des clusters qu’il implémente. Elasticsearch migre les partitions entre les nœuds à mesure que des nœuds supplémentaires sont ajoutés à un cluster. Elasticsearch suppose que la durée de communication entre tous les nœuds est à peu près équivalente et ne prend pas en compte les emplacements relatifs des partitions détenues sur ces nœuds. En outre, la réplication peut engendrer un nombre considérable d’E/S réseau entre les partitions. Pour ces raisons, évitez de créer des clusters sur des nœuds géographiquement dispersés.

Notez que, dans les machines virtuelles Azure, la bande passante du réseau est limitée non seulement par la référence, mais également par l’utilisation totale du réseau du matériel physique sur lequel la machine virtuelle est exécutée. Les machines virtuelles qui partagent du matériel ont moins de bande passante que celles qui sont exécutées sur des machines dédiées, mais leur déploiement est plus économique. Cela signifie qu’en dehors du fait que vous pouvez sélectionner la série et la référence de vos machines virtuelles, vous n’avez que peu de contrôle sur la bande passante réseau disponible. Par exemple, même si vous pouvez créer des machines virtuelles avec plusieurs cartes réseau virtuelles, cela n’augmente pas la bande passante globale disponible pour vos machines virtuelles. Cela signifie seulement qu’un plus grand nombre de cartes réseau virtuelles est en concurrence pour les mêmes ressources du réseau physique.

### Configuration logicielle requise

Vous pouvez exécuter Elasticsearch sur Windows ou Linux. Le service Elasticsearch est déployé en tant que bibliothèque jar Java et a des dépendances sur d’autres bibliothèques Java incluses dans le package Elasticsearch. Vous devez installer la machine virtuelle Java 7 (mise à jour 55 ou ultérieure) ou Java 8 (mise à jour 20 ou ultérieure) pour exécuter Elasticsearch.

> [AZURE.NOTE] Ne modifiez pas les paramètres de configuration des machines virtuelles Java, en dehors des paramètres de mémoire *Xmx* et *Xms* (spécifiés en tant qu’options de ligne de commande au moteur Elasticsearch – voir [Configuration mémoire requise][]). Elasticsearch a été conçu en utilisant les paramètres par défaut ; leur modification peut entraîner un déréglage et un mauvais fonctionnement d’Elasticsearch.

### Déployer Elasticsearch sur Azure

Bien que le déploiement d’une seule instance d’Elasticsearch ne soit pas difficile, la création d’un certain nombre de nœuds et l’installation et la configuration d’Elasticsearch sur chacun d’eux peut être un processus fastidieux et source d’erreurs. Si vous envisagez d’exécuter Elasticsearch sur des machines virtuelles Azure, vous avez deux options qui peuvent contribuer à réduire les risques d’erreurs.
- Utiliser le [modèle Azure Resource Manager (ARM)](http://azure.microsoft.com/documentation/templates/elasticsearch/) pour créer le cluster. Ce modèle est entièrement paramétrable pour vous permettre de spécifier le niveau de performance et la taille des machines virtuelles qui implémentent les nœuds, le nombre de disques à utiliser et d’autres facteurs courants. Le modèle peut créer un cluster basé sur Windows Server 2012 ou Ubuntu Linux 14.0.4.
- Utiliser des scripts qui peuvent être automatisés ou s’exécuter sans assistance. Les scripts qui peuvent créer et déployer un cluster Elasticsearch sont disponibles sur le site [Modèles de démarrage rapide Azure][].

## Considérations relatives au dimensionnement et à l’évolutivité du cluster et des nœuds

Elasticsearch permet un certain nombre de topologies de déploiement, conçues pour prendre en charge des exigences et des niveaux d’échelle différents. Cette section présente certaines topologies courantes et décrit les éléments à prendre en compte pour l’implémentation de clusters basés sur ces topologies.

### Topologies d’Elasticsearch

La figure 4 illustre un point de départ pour la conception d’une topologie Elasticsearch pour le cloud basée sur des machines virtuelles Azure.

![](media/guidance-elasticsearch-general-startingpoint.png)

**Figure 4.**
Suggestion d’un point de départ pour la création d’un cluster Elasticsearch avec Azure

Cette topologie comprend six nœuds de données avec trois nœuds client et trois nœuds principaux (un seul nœud principal est sélectionné, les deux autres sont disponibles pour la sélection si le nœud principal sélectionné est défaillant.) Chaque nœud est implémenté en tant que machine virtuelle distincte. Les applications web Azure sont dirigées vers les nœuds client via un équilibreur de charge. Dans cet exemple, tous les nœuds et les applications web se trouvent sur le même réseau virtuel Azure qui les isole efficacement du monde extérieur. Si le cluster doit être disponible à l’extérieur (éventuellement dans le cadre d’une solution hybride comprenant des clients sur site), vous pouvez utiliser l’équilibreur de charge Azure pour fournir une adresse IP publique, mais vous devez prendre des précautions de sécurité supplémentaires afin d’empêcher tout accès non autorisé au cluster. La « Jump Box » facultative est une machine virtuelle uniquement disponible pour les administrateurs. Elle dispose d’une connexion réseau avec le réseau virtuel Azure, mais aussi d’une connexion réseau vers l’extérieur pour permettre à l’administrateur d’ouvrir une session à partir d’un réseau externe (cette ouverture de session doit être protégée par un mot de passe fort ou un certificat). Un administrateur peut se connecter à la Jump Box, et de là se connecter directement à tous les nœuds du cluster. Les autres approches possibles incluent l’utilisation d’un VPN de site à site entre l’organisation et le réseau virtuel, ou l’utilisation de circuits [ExpressRoute][] pour se connecter au réseau virtuel. Ces mécanismes permettent un accès administratif au cluster sans exposer le cluster à l’Internet public.

Pour préserver la disponibilité de la machine virtuelle, les nœuds de données sont regroupés dans le même groupe à haute disponibilité Azure. De même, les nœuds client sont détenus dans un autre groupe à haute disponibilité et les nœuds principaux sont stockés dans un troisième groupe à haute disponibilité.

Cette topologie est relativement facile à faire évoluer ; il suffit d’ajouter des nœuds supplémentaires du type approprié et de s’assurer qu’ils sont configurés avec le même nom de cluster dans le fichier elasticsearch.yml. Les nœuds client doivent également être ajoutés au pool principal pour l’équilibreur de charge Azure.

**Géolocalisation des clusters**

Ne répartissez pas les nœuds d’un cluster sur les régions car cela peut affecter les performances de communication entre les nœuds (voir [Configuration réseau requise][]). La géolocalisation des données à proximité des utilisateurs dans différentes régions nécessite la création de plusieurs clusters. Dans ce cas, vous devez réfléchir à la manière dont vous allez (le cas échéant) synchroniser les clusters. Voici les solutions possibles :

**Connexion au cluster à l’aide des [nœuds tribu][].** Un nœud tribu est similaire à un nœud client, excepté qu’il peut participer à plusieurs clusters Elasticsearch et voir ces derniers comme un seul grand cluster. Les données sont toujours gérées en local par chaque cluster (les mises à jour ne sont pas propagées au-delà des limites du cluster), mais toutes les données sont visibles. Un nœud tribu peut interroger, créer et gérer des documents dans tous les clusters. Les principales restrictions sont qu’un nœud tribu ne peut pas être utilisé pour créer un nouvel index, et les noms des index doivent être uniques sur tous les clusters. Par conséquent, lorsque vous concevez des clusters accessibles à partir de nœuds tribu, il est important de réfléchir à la manière dont les index seront nommés.

À l’aide de ce mécanisme, chaque cluster peut contenir les données qui sont le plus susceptibles d’être utilisées par les applications clientes locales. Toutefois, ces clients peuvent toujours accéder aux données à distance et les modifier, avec une possible latence supplémentaire. La figure 5 illustre un exemple de cette topologie. Le nœud tribu dans le Cluster 1 est sélectionné ; les autres clusters peuvent également avoir des nœuds tribu même s’ils ne sont pas visibles sur ce diagramme :

![](media/guidance-elasticsearch-general-tribenode.png)

**Figure 5.**
Application cliente accédant à plusieurs clusters via un nœud tribu

Dans cet exemple, l’application cliente se connecte au nœud tribu dans le Cluster 1 (colocalisé dans la même région), mais ce nœud est configuré pour être en mesure d’accéder au Cluster 2 et au Cluster 3 qui peuvent se trouver dans des régions différentes. L’application cliente peut envoyer des demandes qui extraient ou modifient des données dans les clusters choisis.

> [AZURE.NOTE] Les nœuds tribu nécessitent une découverte multidiffusion pour se connecter aux clusters, ce qui peut présenter un problème de sécurité. Pour plus de détails, voir la section [Découverte des nœuds][].

*	Implémentation de la géo-réplication entre les clusters. Dans cette approche, les modifications apportées à chaque cluster sont propagées quasiment en temps réel vers les clusters situés dans d’autres centres de données. Des plug-ins tiers sont disponibles pour Elasticsearch. Ils prennent en charge cette fonctionnalité, comme le [plug-in de modifications PubNub][] (en anglais).
*	Utilisation du [Module Snapshot and Restore d’Elasticsearch ][]. Si les données se déplacent très lentement et sont modifiées uniquement par un seul cluster, vous pouvez envisager d’utiliser des instantanés pour effectuer une copie périodique des données, puis de restaurer ces instantanés dans d’autres clusters (les instantanés peuvent être stockés dans le Blob Storage Azure si vous avez installé le [plug-in Azure Cloud][]). Toutefois, cette solution ne fonctionne pas très bien avec les données qui changent rapidement ou les données qui sont modifiables dans plusieurs clusters.

**Topologies d’échelle identique**

Les topologies de grande échelle comprenant des clusters de nœuds de données, principal et client dédiés peuvent ne pas convenir à chaque scénario. Si vous créez un système de développement ou de production d’échelle identique, envisagez le cluster à 3 nœuds illustré dans la figure 6. Les applications clientes se connectent directement à n’importe quel nœud de données disponible dans le cluster. Le cluster contient trois partitions libellées P1-P3 (pour permettre la croissance), plus des réplicas libellés R1-R3. L’utilisation de trois nœuds permet à Elasticsearch de répartir les partitions et les réplicas de manière à ce qu’en cas de défaillance d’un nœud, aucune donnée ne soit perdue.

![](media/guidance-elasticsearch-general-threenodecluster.png)

**Figure 6.**
Cluster à 3 nœuds avec 3 partitions et réplicas

Si vous exécutez une installation de développement sur un ordinateur autonome, vous pouvez configurer un cluster avec un nœud unique qui sert de nœud principal, de nœud client et de nœud de stockage des données. Vous pouvez également démarrer plusieurs nœuds exécutés en tant que cluster sur le même ordinateur en démarrant plusieurs instances d’Elasticsearch. La figure 7 illustre un exemple.

![](media/guidance-elasticsearch-general-developmentconfiguration.png)

**Figure 7.**
Configuration de développement exécutant plusieurs nœuds Elasticsearch sur le même ordinateur

Notez qu’aucune de ces configurations autonomes n’est recommandée pour un environnement de production, car elles peuvent provoquer des conflits, sauf si votre ordinateur de développement a une quantité massive de mémoire et plusieurs disques rapides. En outre, elles ne fournissent pas les garanties de haute disponibilité. Si l’ordinateur est défaillant, tous les nœuds sont perdus.

### Considérations relatives à la mise à l’échelle d’un cluster et des nœuds de données

Elasticsearch peut effectuer une mise à l’échelle en deux dimensions : verticale (à l’aide d’ordinateurs plus puissants et plus performants) et horizontale (en répartissant la charge sur plusieurs ordinateurs).

**Mise à l’échelle verticale des nœuds de données Elasticsearch**

Si vous hébergez un cluster Elasticsearch à l’aide de machines virtuelles Azure, chaque nœud peut correspondre à une machine virtuelle. La limite de mise à l’échelle verticale d’un nœud est régie en grande partie par la référence de la machine virtuelle et par les restrictions globales appliquées aux comptes de stockage individuels et aux abonnements Azure. La page [Abonnement Azure et limites, quotas et contraintes du service](azure-subscription-service-limits/) décrit ces limites en détail, mais en ce qui concerne la création d’un cluster Elasticsearch, les éléments de la liste suivante sont les plus pertinents. En outre, nous vous conseillons d’éviter d’utiliser des machines virtuelles avec plus de 64 Go de mémoire sans vrai motif. Comme décrit dans la section [Configuration mémoire requise][], vous ne devez pas allouer plus de 30 Go de RAM sur chaque machine virtuelle JVM et laisser le système d’exploitation utiliser la mémoire restante pour la mise en mémoire tampon des E/S :
- Chaque compte de stockage est limité à 20 000 IOPS maximum ; l’utilisation d’un même compte de stockage pour stocker un certain nombre de disques durs virtuels peut limiter les performances de ces disques durs virtuels
- Le nombre de nœuds de données dans un réseau virtuel. Si vous n’utilisez pas Azure Resource Manager (ARM), il existe une limite de 2 048 instances de machine virtuelle par réseau virtuel. Cela devrait suffire dans la plupart des cas, sauf si vous avez une configuration très volumineuse comportant des milliers de nœuds.
- Nombre de comptes de stockage par abonnement par région. Vous pouvez créer jusqu’à 100 comptes de stockage par abonnement Azure dans chaque région. Les comptes de stockage sont utilisés pour stocker les disques virtuels, et chaque compte de stockage est limité à 500 To d’espace.
- Nombre de cœurs par abonnement. La limite par défaut est de 20 cœurs par abonnement, mais elle peut être augmentée par Microsoft jusqu’à 10 000 cœurs. N’oubliez pas que certaines tailles de machines virtuelles (A9, A11, D14 et DS14) peuvent contenir 16 cœurs, alors qu’une machine virtuelle G5 comporte 32 cœurs.
- La quantité de mémoire par taille de machine virtuelle. Les machines virtuelles de plus petite taille ont une quantité de mémoire disponible limitée (3,5 Go pour les machines D1 et 7 Go pour les machines D2). Ces machines peuvent ne pas convenir dans des scénarios nécessitant qu’Elasticsearch mette en cache des quantités importantes de données pour atteindre de bonnes performances (agrégation des données ou analyse d’un grand nombre de documents au cours de l’ingestion des données, par exemple).
- Le nombre maximal de disques par taille de machine virtuelle. Cette restriction peut limiter la taille et les performances d’un cluster. Un moins grand nombre de disques permet de contenir moins de données, et les performances peuvent pâtir du manque de disques disponibles pour l’entrelacement.
- Le nombre de domaines de mise à jour/domaines de défaillance par groupe à haute disponibilité. Si vous créez des machines virtuelles à l’aide de ARM, il est possible d’allouer à chaque groupe à haute disponibilité jusqu’à 3 domaines de défaillance et 20 domaines de mise à jour. Cette limitation peut affecter la résilience d’un grand cluster sujet à des mises à jour fréquentes.

En raison de ces restrictions, nous vous conseillons de toujours répartir les disques virtuels des machines virtuelles d’un cluster sur les comptes de stockage pour réduire les risques de limitation d’E/S. Dans un très grand cluster, il se peut que vous deviez repenser votre infrastructure logique et la fractionner en partitions fonctionnelles distinctes. Par exemple, il se peut que vous deviez fractionner le cluster sur les abonnements, bien que ce processus puisse entraîner d’autres complications en raison de la nécessité de connecter des réseaux virtuels.

>	[AZURE.NOTE] Sachez qu'avec Azure, les comptes de stockage sont épinglés à un tampon de stockage spécifique. Il s'agit d'un mécanisme interne qui permet d'assurer cohérence et disponibilité. L'article [A Highly Available Cloud Storage Service with Strong Consistency][] (en anglais) décrit son fonctionnement plus en détail. Si une indisponibilité du stockage est localisée sur un tampon particulier, des erreurs se produisent sur tous les lecteurs créés à l'aide de ce compte. Cela peut entraîner l'interruption des machines virtuelles qui utilisent ces lecteurs. Par conséquent, l'utilisation de plusieurs comptes de stockage pour héberger les différents lecteurs d'une machine virtuelle peut augmenter le risque de défaillance de celle-ci. Il est donc recommandé d'utiliser un seul compte de stockage par nœud, et de stocker le lecteur système et l'ensemble des lecteurs de données dans ce compte.

**Mise à l’échelle horizontale d’un cluster Elasticsearch**

Au sein d’Elasticsearch, en interne, la limite de mise à l’échelle horizontale est déterminée par le nombre de partitions définies pour chaque index. Initialement, vous pouvez allouer un grand nombre de partitions au même nœud d’un cluster, mais à mesure que le volume de données augmente, vous pouvez ajouter des nœuds supplémentaires et de répartir des partitions sur ces nœuds. En théorie, la mise à l’échelle horizontale du système cesse quand le nombre de nœuds atteint le nombre de partitions.

Comme pour la mise à l’échelle verticale, lorsque vous envisagez d’implémenter une mise à l’échelle horizontale vous devez prendre en compte certains problèmes, notamment :

- Le nombre maximal de machines virtuelles que vous pouvez vous connecter dans un réseau virtuel Azure. Cela peut limiter la mise à l’échelle horizontale d’un très grand cluster. Pour contourner cette limite, vous pouvez créer un cluster de nœuds couvrant plusieurs réseaux virtuels, mais cette approche peut entraîner une dégradation des performances en raison du manque de la proximité de chaque nœud avec ses pairs.
- Le nombre de disques par taille de machine virtuelle. Les différentes séries et références prennent en charge des quantités différentes de disques attachés. En outre, vous pouvez envisager d’utiliser le stockage éphémère inclus avec la machine virtuelle pour fournir une quantité limitée de stockage de données plus rapide, sans pour autant ignorer les conséquences à prendre en compte en termes de résilience et de récupération (Pour plus d’informations, voir le document Configuring, Testing, and Analyzing Elasticsearch Resilience and Recovery (Configurer, tester et analyser la résilience et la récupération d’Elasticsearch)). Les machines virtuelles de série D, DS, Dv2 et GS utilisent des disques SSD pour le stockage éphémère.
- Le maximum d’IOPS par disque virtuel. Les disques attachés standard créés à l’aide du stockage standard sont limités à 500 IOPS par disque. Les disques SSD attachés peuvent prendre en charge jusqu’à 5 000 IOPS par disque. Pour utiliser des disques SSD pour les disques attachés (stockage non éphémère), vous devez créer une machine virtuelle qui prend en charge le stockage premium (ordinateurs de série DS ou GS).

Vous pouvez envisager d’utiliser les groupes à échelle identique d’Azure pour démarrer et arrêter des machines virtuelles en fonction des demandes (pour plus d’informations, voir [Mise à l’échelle automatique des machines dans un groupe de machines virtuelles à échelle identique][]). Toutefois, cette approche peut ne pas être appropriée pour un cluster Elasticsearch. En voici les raisons :

- Cette approche convient mieux aux machines virtuelles sans état. Chaque fois que vous ajoutez ou supprimez un nœud d’un cluster Elasticsearch, les partitions sont réallouées afin d’équilibrer la charge, et ce processus peut générer des volumes considérables de trafic réseau et d’E/S de disque, sans compter l’impact négatif sur les taux d’ingestion de données. Vous devez évaluer si les ressources de traitement et de mémoire supplémentaires rendues disponibles en démarrant dynamiquement plusieurs machines virtuelles valent la peine de cette surcharge.
- Le démarrage de la machine virtuelle n’a pas lieu instantanément et plusieurs minutes peuvent s’écouler avant que des machines virtuelles supplémentaires soient disponibles ou s’arrêtent. Cette méthode de mise à l’échelle doit seulement être utilisée pour gérer les changements réguliers de la demande.
- Après avoir effectué une mise à l’échelle, est-il vraiment nécessaire d’envisager un retour en arrière ? La suppression d’une machine virtuelle d’un cluster Elasticsearch est un processus qui peut consommer beaucoup de ressources. Il nécessite qu’Elasticsearch récupère les partitions et les réplicas situés sur cette machine virtuelle et les recrée sur au moins un des nœuds restants. La suppression simultanée de plusieurs machines virtuelles risque de compromettre l’intégrité du cluster, rendant toute récupération très difficile. En outre, un grand nombre d’implémentations Elasticsearch croissent au fil du temps, mais la nature des données est telle qu’elles ne diminuent pas en volume. Il est possible de supprimer des documents manuellement et aussi de les configurer avec une durée de vie (TTL) après laquelle ils expirent et sont supprimés, mais généralement, l’espace précédemment alloué est rapidement réutilisé par des documents nouveaux ou modifiés. La fragmentation au sein d’un index peut se produire lorsque des documents sont supprimés ou modifiés, auquel cas vous pouvez utiliser l’API [Optimize][] HTTP Elasticsearch (Elasticsearch version 2.0.0 et antérieures) ou l’API [Force Merge][] (Elasticsearch version 2.1.0 et ultérieures) pour procéder à une défragmentation.

> [AZURE.NOTE] L’optimisation est une opération très coûteuse, et vous ne devez pas l’exécuter sur un index très actif. Toutefois, il est judicieux d’optimiser un index qui est inactif, car cela permet de diminuer les ressources nécessaires pour effectuer une recherche.

### Détermination du nombre de partitions pour un index

Le nombre de nœuds dans un cluster peut varier au fil du temps, mais le nombre de partitions dans un index est fixe une fois que l’index a été créé. Pour ajouter ou supprimer des partitions, vous devez réindexer les données : ce processus consiste à créer un nouvel index avec le nombre de partitions requis, puis à copier les données de l’ancien index vers le nouveau (vous pouvez utiliser des alias pour cacher aux utilisateurs le fait que les données ont été réindexées – pour plus d’informations, consultez le document Maximizing Data Aggregation and Query Performance with Elasticsearch on Azure (Maximiser les performances d’agrégation et de requête avec Elasticsearch sur Azure)). Par conséquent, il est important de déterminer le nombre de partitions dont vous aurez besoin avant de créer le premier index de votre cluster. Pour définir ce nombre, procédez de la manière suivante :

1. Créez un cluster à un seul nœud en utilisant la même configuration matérielle que celle que vous prévoyez de déployer en production.
2. Créez un index qui correspond à la structure que vous prévoyez d’utiliser en production. Donnez à cet index une seule partition et aucun réplica.
3. Ajoutez une quantité spécifique de données de production réalistes à l’index.
4. Exécutez des requêtes, agrégations et autres charges de travail standard dans l’index et mesurez le débit et le temps de réponse.
5. Si le débit et le temps de réponse se situent dans les limites acceptables, répétez le processus à partir de l’étape 3 (ajoutez des données supplémentaires).
6. Lorsque vous semblez avoir atteint la capacité de la partition (le temps de réponse et le débit commencent à devenir inacceptables), notez le volume de documents.
7. Extrapolez à partir de la capacité d’une seule partition et du nombre de documents prévus en production pour calculer le nombre de partitions requis (vous devez inclure une marge d’erreur dans ces calculs car l’extrapolation n’est pas une science précise).

> [AZURE.NOTE] N’oubliez pas que chaque partition est implémentée comme un index Lucene qui consomme des ressources (mémoire, processeurs et descripteurs de fichiers). Plus vous avez de partitions, plus vous aurez besoin de ressources comme celles-ci.

En outre, la création de partitions supplémentaires peut certes augmenter la capacité de mise à l’échelle (en fonction des charges de travail et du scénario) et le débit d’ingestion des données, mais elle peut aussi réduire l’efficacité de nombreuses requêtes. Par défaut, une requête interroge chaque partition utilisée par un index (vous pouvez utiliser le [routage personnalisé][] pour modifier ce comportement si vous savez sur quelles partitions se trouvent les données dont vous avec besoin).

Cette procédure permet seulement de générer une estimation du nombre de partitions, et le volume de documents attendu en production peut ne pas être connu. Dans ce cas, vous devez déterminer le volume initial (voir ci-dessus) et le taux de croissance prévu. Créez un nombre approprié de partitions capable de gérer la croissance des données pendant une certaine période jusqu’à ce que vous souhaitiez réindexer la base de données. Les autres stratégies utilisées pour des scénarios tels que la gestion des événements et la journalisation incluent le recours à la rotation d’index ; créez un index pour les données ingérées chaque jour et accédez à cet index via un alias modifié quotidiennement pour pointer vers l’index le plus récent. Cette approche vous permet de rendre plus facilement obsolètes les anciennes données (vous pouvez supprimer des index contenant des informations qui ne sont plus nécessaires) et de conserver un volume de données facile à gérer.

N’oubliez pas que le nombre de nœuds n’a pas besoin de correspondre au nombre de partitions. Par exemple, si vous créez 50 partitions, vous pouvez les répartir sur 10 nœuds initialement, puis ajouter des nœuds supplémentaires pour mettre le système à l’échelle à mesure que le volume de travail augmente. Toutefois, évitez de créer un nombre exceptionnellement élevé de partitions sur un petit nombre de nœuds (1 000 partitions réparties sur 2 nœuds, par exemple). Même si le système peut théoriquement être mis à l’échelle jusqu’à 1 000 nœuds avec cette configuration, l’exécution de 500 partitions sur un seul nœud risque d’entraver les performances du nœud.

> [AZURE.NOTE] Pour les systèmes qui ingèrent de grandes quantités de données, envisagez d’utiliser un nombre premier de partitions. L’algorithme par défaut utilisé par Elasticsearch pour le routage des documents vers les partitions réalise une répartition plus homogène dans ce cas.

### Sécurité

Par défaut, Elasticsearch implémente une sécurité minimale et ne fournit aucun moyen d’authentification et d’autorisation. Ces aspects nécessitent la configuration du système d’exploitation et du réseau sous-jacents, et l’utilisation de plug-ins et d’utilitaires tiers. Il peut s’agir de [Shield][] et [Search Guard][] par exemple.

> [AZURE.NOTE] Shield est un plug-in fourni par Elastic pour l’authentification des utilisateurs, le chiffrement des données, le contrôle d’accès basé sur les rôles, le filtrage IP et l’audit. Il peut être nécessaire de configurer le système d’exploitation sous-jacent pour implémenter des mesures de sécurité supplémentaires, telles que le chiffrement de disque.

Dans un système de production, vous devez réfléchir aux méthodes pour :

- Empêcher l’accès non autorisé au cluster.
- Identifier et authentifier les utilisateurs.
- Autoriser les opérations que les utilisateurs authentifiés peuvent effectuer.
- Protéger le cluster contre les opérations dangereuses ou non fiables.
- Protéger les données contre tout accès non autorisé.
- Satisfaire les exigences réglementaires pour la sécurité des données commerciales (le cas échéant).

### Sécurisation de l’accès au cluster

Elasticsearch est un service réseau. Les nœuds dans un cluster Elasticsearch écoutent les demandes entrantes des clients à l’aide du protocole HTTP et communiquent entre eux à l’aide d’un canal TCP. Vous devez prendre des mesures pour empêcher les clients ou services non autorisés d’être en mesure d’envoyer des requêtes sur les chemins d’accès HTTP et TCP. Envisagez ce qui suit :

- Définissez des groupes de sécurité réseau pour limiter le trafic réseau entrant et sortant d’une machine virtuelle ou d’un réseau virtuel à des ports spécifiques.
- Modifiez les ports par défaut utilisés pour l’accès au web du client (9200) et l’accès au réseau par programmation (9300). Utilisez un pare-feu pour protéger chaque nœud du trafic Internet malveillant.
- Selon l’emplacement et la connectivité des clients, placez le cluster sur un sous-réseau privé sans accès direct à Internet. Si le cluster doit être exposé à l’extérieur du sous-réseau, acheminez toutes les demandes via un serveur bastion ou un proxy suffisamment renforcé pour protéger le cluster.
- Si vous devez fournir un accès direct aux nœuds, utilisez le plug-in Jetty d’Elasticsearch pour procurer la connectivité SSL, l’authentification et la journalisation des connexions. Vous pouvez également configurer un serveur proxy nginx et configurer l’authentification HTTPS.

> [AZURE.NOTE] À l’aide d’un serveur proxy comme nginx, vous pouvez également restreindre l’accès aux fonctionnalités. Par exemple, vous pouvez configurer nginx pour autoriser uniquement les demandes vers le point de terminaison \_search si vous devez empêcher les clients d’exécuter d’autres opérations.

- Si vous avez besoin d’une sécurité d’accès au réseau plus complète, utilisez les plug-ins Shield ou Search Guard.

### Identification et authentification des utilisateurs

Toutes les demandes adressées par les clients au cluster doivent être authentifiées. En outre, vous devez empêcher les nœuds non autorisés de rejoindre le cluster car ils peuvent fournir une porte dérobée d’accès au système qui contourne l’authentification.

Certains plug-ins Elasticsearch disponibles permettent d’effectuer différents types d’authentification, notamment :
- Authentification HTTP de base. Des noms d’utilisateur et mots de passe sont inclus dans chacun. Toutes les demandes doivent être chiffrées à l’aide de SSL/TLS ou d’un niveau équivalent de protection.
- Intégration de LDAP et Active Directory. Cette approche nécessite d’assigner des rôles aux clients dans les groupes LDAP ou Active Directory.
- Authentification native à l’aide des identités définies au sein du cluster Elasticsearch lui-même. 
- Authentification TLS au sein d’un cluster pour authentifier tous les nœuds.
- Filtrage IP, pour empêcher les clients de sous-réseaux non autorisés de se connecter et empêcher les nœuds de ces sous-réseaux de rejoindre le cluster.

### Autorisation des demandes des clients

L’autorisation dépend du plug-in Elasticsearch utilisé pour fournir ce service. Par exemple, un plug-in qui fournit une authentification de base fournit généralement des fonctionnalités qui définissent le niveau d’authentification, tandis qu’un plug-in qui utilise LDAP ou AD associe généralement les clients aux rôles, puis attribue des droits d’accès à ces rôles. Lorsque vous utilisez un plug-in donné, vous devez tenir compte des points suivants :
- Devez-vous restreindre les opérations qu’un client peut effectuer ? Par exemple, un client doit-il être en mesure de surveiller l’état du cluster, ou de créer et supprimer des index ?
- Le client doit-il être limité à des index spécifiques ? Cela est utile dans un environnement multiclient où des jeux d’index spécifiques sont assignés à un client et où les autres clients ne doivent pas avoir accès à ces index.
- Le client doit-il être capable de lire et d’écrire des données dans un index ? Un client peut être en mesure d’effectuer des recherches pour extraire des données à l’aide d’un index, mais peut ne pas être autorisé à ajouter ou supprimer des données dans cet index, par exemple.

Actuellement, la plupart des plug-ins limitent le périmètre des opérations au cluster ou à un index et non à des sous-ensembles de documents dans les index. Cela s’explique par un besoin d’efficacité. Il n’est donc pas facile de limiter les demandes à des documents spécifiques au sein d’un seul index. Si vous avez besoin de ce niveau de granularité, enregistrez les documents dans des index séparés et utilisez des alias qui regroupent les index. Par exemple, dans un système personnel, si un utilisateur A doit accéder à tous les documents qui contiennent des informations sur les employés du département X, un utilisateur B doit accéder à tous les documents qui contiennent des informations sur les employés du département Y et un utilisateur C doit accéder à tous les documents qui contiennent des informations sur les employés dans les deux départements, créez deux index (pour le département X et le département Y) et un alias qui référence les deux index. Accordez à l’utilisateur A un accès en lecture au premier index, accordez à l’utilisateur B un accès en lecture au second index et accordez à l’utilisateur C un accès en lecture aux deux index via l’alias. Pour plus d’informations, voir [Faking Index per User with Aliases][] (Simuler un index par utilisateur avec alias).

### Protection du cluster

Le cluster peut devenir vulnérable à une utilisation malveillante s’il n’est pas protégé avec précaution, comme expliqué dans les points suivants :

- Désactivez les scripts de requête dynamique dans les requêtes Elasticsearch car ils peuvent entraîner des failles de sécurité. Utilisez des scripts natifs plutôt que des scripts de requête ; un script natif est un plug-in Elasticsearch écrit en Java et compilé dans un fichier JAR.

> [AZURE.NOTE] Les scripts de requête dynamique sont maintenant désactivés par défaut. Ne les réactivez pas, sauf si vous avez une très bonne raison de le faire.

- Évitez d’exposer des recherches de chaîne de requête aux utilisateurs. La recherche de chaîne de requête permet aux utilisateurs d’effectuer sans encombre des requêtes gourmandes en ressources. Ces recherches peuvent sérieusement affecter les performances du cluster et peuvent rendre le système vulnérable aux attaques par déni de service. En outre, la recherche de chaîne de requête peut exposer des informations potentiellement confidentielles.
- Empêchez les opérations de consommer une trop grande quantité de mémoire, car cela peut entraîner des exceptions de mémoire insuffisante aboutissant à un échec d’Elasticsearch sur un nœud. Les opérations longues et gourmandes en ressources peuvent également être utilisées pour implémenter des attaques par déni de service. Voici quelques exemples :
- Demandes de recherche qui tentent de charger des champs de très grande taille en mémoire (si une requête exécute un tri, un script ou des facettes sur ces champs).
  
> [AZURE.NOTE] L’utilisation des [valeurs Doc][] (en anglais) peut réduire les besoins en mémoire des index en enregistrant les données des champs sur le disque plutôt que de les charger dans la mémoire. Cela peut contribuer à réduire le risque d’insuffisance de mémoire sur un nœud, mais avec une réduction de la vitesse.

- Les recherches qui interrogent plusieurs index en même temps.
- Recherches qui extraient un grand nombre de champs. Ces recherches peuvent épuiser la mémoire en provoquant la mise en cache d’une grande quantité de données de champs. Par défaut, le cache des données de champ a une taille illimitée, mais vous pouvez définir les propriétés [indices.fielddata.cache.*] (https://www.elastic.co/guide/en/elasticsearch/reference/current/index-modules-fielddata.html) dans le fichier de configuration elasticsearch.yml pour limiter les ressources disponibles. Vous pouvez également configurer le [champ données disjoncteur][] pour aider à empêcher que les données mises en cache dans un seul champ d’épuiser la mémoire et le [disjoncteur de demande][](en anglais) pour que les requêtes individuelles cessent de monopoliser la mémoire. La configuration de ces paramètres peut se solder par une probabilité accrue d’échec ou d’expiration du délai des requêtes.
  
> [AZURE.NOTE] Elasticsearch suppose toujours qu’il a suffisamment de mémoire pour exécuter sa charge de travail en cours. Si ce n’est pas le cas, le service Elasticsearch peut se bloquer. Elasticsearch fournit des points de terminaison qui renvoient des informations sur l’utilisation des ressources ([API cat][] HTTP), et vous devez surveiller attentivement ces informations.

- Attendre trop longtemps pour vider un segment de mémoire en cours d’exécution. Cela peut épuiser l’espace tampon en mémoire. Si nécessaire, [configurez le translog][] afin de réduire les seuils auxquels les données sont évacuées du disque.

- Créer des index avec de grandes quantités de métadonnées. Un index qui contient des documents avec de grandes variations dans les noms des champs peut consommer beaucoup de mémoire. Pour plus d’informations, voir [Mapping Explosion][] (Explosion des mappages).
  
> [AZURE.NOTE] La définition d’une opération longue ou comportant de nombreuses requêtes dépend fortement du scénario. La charge de travail généralement attendue par un cluster peut avoir un profil complètement différent de la charge de travail d’un autre. Pour déterminer quelles opérations sont inacceptables, vous devez effectuer beaucoup de recherches et de tests sur vos applications.

Adoptez une attitude proactive. Détectez et arrêtez les activités malveillantes avant qu’elles provoquent des dommages importants ou des pertes de données. Vous pouvez envisager d’utiliser un système de notification et de surveillance de la sécurité capable de détecter rapidement les schémas inhabituels d’accès aux données et de déclencher des alertes lorsque, par exemple, les demandes de connexion d’un utilisateur échouent, des nœuds inattendus rejoignent ou quittent le cluster, ou des opérations durent plus longtemps que prévu. [Watcher][] d’Elasticsearch est l’un des outils capable d’effectuer cette tâche.

### Protection des données

Vous pouvez protéger les données en vol à l’aide de SSL/TLS, mais Elasticsearch ne fournit pas de forme intégrée de chiffrement des données pour les informations stockées sur le disque. N’oubliez pas que ces informations résident dans les fichiers de disque, et que tout utilisateur ayant accès à ces fichiers peut être en mesure de compromettre les données qu’ils contiennent, par exemple en les copiant dans son propre cluster. Observez les points suivants :
- Protégez les fichiers utilisés par Elasticsearch pour contenir les données. N’autorisez pas d’accès arbitraire en lecture ou en écriture aux identités autre que le service Elasticsearch.

- Chiffrez les données contenues dans ces fichiers à l’aide d’un système de fichiers EFS.

> [AZURE.NOTE] Azure prend désormais en charge le chiffrement de disque pour les machines virtuelles Linux et Windows. Pour plus d’informations, voir [Azure Disk Encryption pour machines virtuelles Windows et Linux IaaS - Version préliminaire][].

### Satisfaction des exigences réglementaires

Les exigences réglementaires concernent principalement l’audit des opérations en vue de conserver un historique des événements, et la préservation de la confidentialité de ces opérations afin d’empêcher qu’elles soient surveillées (et relues) par un organisme externe. Vous devez en particulier penser aux méthodes pour :

- Effectuer le suivi de toutes les demandes (réussies ou non) et de toutes les tentatives d’accès au système.
- Chiffrer les communications des clients avec le cluster, ainsi que les communications de nœud à nœud effectuées par le cluster. Vous devez implémenter SSL/TLS pour toutes les communications du cluster. Elasticsearch prend également en charge les chiffrements enfichables si votre organisation a des exigences autres que celles disponibles via SSL/TLS.
- Stocker toutes les données d’audit. Le volume d’informations d’audit peut augmenter très rapidement et doit être protégé efficacement pour empêcher la falsification des informations d’audit.
- Archiver de façon sécurisée les données d’audit.

### Considérations relatives à la surveillance

La surveillance est aussi importante au niveau du système d’exploitation qu’au niveau d’Elasticsearch.

Vous pouvez appliquer une surveillance au niveau du système d’exploitation à l’aide d’outils spécifiques du système d’exploitation. Sous Windows, cela inclut des éléments tels que l’Analyseur de performances avec les compteurs de performance appropriés, tandis que sous Linux vous pouvez utiliser des outils tels que *vmstat*, *iostat* et *top*. Les éléments clés à surveiller au niveau du système d’exploitation sont l’utilisation du processeur, les volumes d’E/S du disque, les temps d’attente d’E/S du disque, le trafic réseau. Dans un cluster Elasticsearch bien réglé, l’utilisation du processeur par le processus Elasticsearch doit être élevée et les temps d’attente d’E/S du disque doivent être minimes.

Au niveau du logiciel, vous devez surveiller le débit et les temps de réponse des demandes, ainsi que les détails des demandes qui ont échoué. Elasticsearch fournit plusieurs API qui permettent d’examiner les performances de différents aspects d’un cluster. Les deux API les plus importantes sont *\_cluster/health* et *\_nodes/stats*. L’API *\_cluster/health* peut être utilisée pour fournir un instantané de l’intégrité globale du cluster, tout en fournissant des informations détaillées pour chaque index, comme indiqué dans l’exemple suivant :

`GET _cluster/health?level=indices`

L’exemple de sortie ci-dessous a été généré à l’aide de cette API :

```json
{
    "cluster_name": "elasticsearch",
    "status": "green",
    "timed_out": false,
    "number_of_nodes": 6,
    "number_of_data_nodes": 3,
    "active_primary_shards": 10,
    "active_shards": 20,
    "relocating_shards": 0,
    "initializing_shards": 0,
    "unassigned_shards": 0,
    "delayed_unassigned_shards": 0,
    "number_of_pending_tasks": 0,
    "number_of_in_flight_fetch": 0,
    "indices": {
        "systwo": {
            "status": "green",
            "number_of_shards": 5,
            "number_of_replicas": 1,
            "active_primary_shards": 5,
            "active_shards": 10,
            "relocating_shards": 0,
            "initializing_shards": 0,
            "unassigned_shards": 0
        },
        "sysfour": {
            "status": "green",
            "number_of_shards": 5,
            "number_of_replicas": 1,
            "active_primary_shards": 5,
            "active_shards": 10,
            "relocating_shards": 0,
            "initializing_shards": 0,
            "unassigned_shards": 0
        }
    }
}
```

Ce cluster contient deux index nommés *systwo* et *sysfour*. Les statistiques clés à surveiller pour chaque index sont status état), active\_shards (partitions actives) et unassigned\_shards (partitions non attribuées). Status doit valoir green (vert), le nombre d’active\_shards doit correspondre au number\_of\_shards (nombre de partitions) et au number\_of\_replicas (nombre de réplicas), et unassigned\_shards doit être égal à zéro. Si status vaut red (rouge), une partie de l’index est manquante ou a été endommagée. Vous pouvez le vérifier si le paramètre *active\_shards* est inférieur à *number\_of\_shards* - (*number\_of\_replicas* + 1) et si unassigned\_shards n’est pas égal à zéro. Notez que si status vaut yellow (jaune), cela qu’un index est en cours de transition, soit en raison de l’ajout de réplicas supplémentaires, soit en raison du déplacement de partitions. Status doit basculer vers green une fois la transition terminée. S’il vaut yellow pendant une période prolongée ou passe à red, vérifiez si des événements d’E/S majeurs (par exemple, une erreur disque ou réseau) se sont produits au niveau du système d’exploitation.
L’API \_nodes/stats émet des informations détaillées concernant chaque nœud du cluster :

`GET _nodes/stats`

La sortie générée inclut des détails sur la façon dont les index sont stockés sur chaque nœud (y compris la taille et le nombre de documents), le temps passé à effectuer l’indexation, l’interrogation, la recherche et la fusion, la mise en cache, des informations sur le système d’exploitation et les processus, les statistiques sur la machine virtuelle Java (y compris les performances de récupération de mémoire) et les pools de threads. Pour plus d’informations, voir [Monitoring Individual Nodes][] (Surveillances des nœuds individuels).

Si une partie significative des demandes d’Elasticsearch échouent avec des messages d’erreur *EsRejectedExecutionException*, cela signifie qu’Elasticsearch ne parvient pas à suivre le rythme du travail qui lui est envoyé. Dans ce cas, vous devez identifier le goulot d’étranglement qui fait prendre du retard à Elasticsearch. Envisagez ce qui suit :

- Si le goulot d’étranglement est dû à une insuffisance de ressources, telle qu’une mémoire insuffisante allouée à la machine virtuelle Java provoquant des récupérations excessives de mémoire, vous pouvez allouer des ressources supplémentaires (dans ce cas, configurez la machine virtuelle Java pour utiliser plus de mémoire, jusqu’à 50 % de la capacité de stockage disponible sur le nœud - voir [Configuration mémoire requise][]).
- Si le cluster présente des temps d’attente d’E/S très longs et que les statistiques de fusion collectées pour un index à l’aide de l’API \_node/stats contiennent des valeurs élevées, cela signifie que les écritures dans l’index sont trop intensives. Réétudiez les points soulevés dans la section [Optimisation des ressources pour les opérations d’indexation](Optimisation des ressources pour les opérations d’indexation) pour ajuster les performances d’indexation.
- Limitez les applications clientes qui effectuent des opérations d’ingestion de données et déterminez l’effet que cela a sur les performances. Si cette approche révèle une amélioration importante, envisagez de conserver la limitation, ou d’effectuer une mise à l’échelle en répartissant la charge des index aux écritures intensives sur plus de nœuds. Pour plus d’informations, voir le document Maximizing Data Ingestion Performance with Elasticsearch on Azure (Maximisation des performances d’ingestion des données avec Elasticsearch sur Azure).
- Si les statistiques de recherche d’un index indiquent que les requêtes prennent beaucoup de temps, envisagez d’optimiser les requêtes. Pour plus d’informations, voir la section [Ajustement des requêtes][]. Notez que vous pouvez utiliser les valeurs *query\_time\_in\_millis* et *query\_total* indiquées par les statistiques de recherche pour calculer une estimation générale de l’efficacité des requêtes ; l’équation *query\_time\_in\_millis* / *query\_total* vous indiquera une durée moyenne pour chaque requête.

### Outils de surveillance d’Elasticsearch

Divers outils sont disponibles pour effectuer une surveillance quotidienne d’Elasticsearch en production. Ces outils utilisent généralement les API Elasticsearch sous-jacentes pour recueillir des informations et présenter les détails d’une manière plus facile à lire que les données brutes. Les exemples courants sont [Elasticsearch-Head][], [Bigdesk][], [Kopf][] et [Marvel][].

Elasticsearch-Head, Bigdesk et Kopf s’exécutent comme des plug-ins du logiciel Elasticsearch. Les versions plus récentes de Marvel peuvent s’exécuter indépendamment, mais nécessitent [Kibana][] pour fournir un environnement de capture et d’hébergement des données. L’avantage de l’utilisation de Marvel avec Kibana est que vous pouvez implémenter la surveillance dans un environnement séparé du cluster Elasticsearch, ce qui vous permet d’explorer les problèmes d’Elasticsearch d’une manière qui n’aurait pas été possible si les outils de surveillance s’exécutaient au sein du logiciel Elasticsearch. Par exemple, si Elasticsearch échoue à plusieurs reprises ou s’exécute très lentement, les outils qui s’exécutent en tant que plug-ins Elasticsearch sont également concernés, ce qui rend la surveillance et le diagnostic plus difficiles.

Au niveau du système d’exploitation, vous pouvez utiliser des outils tels que la fonctionnalité d’analyse des journaux de [Azure Operations Management Suite][] ou des [Diagnostics Azure avec le portail Azure][] pour capturer les données de performance des machines virtuelles hébergeant des nœuds Elasticsearch. Une autre approche consiste à utiliser [Logstash][] pour capturer les données des performances et des journaux, stocker ces informations dans un cluster Elasticsearch distinct (n’utilisez pas le même cluster que celui utilisé pour votre application), puis utiliser Kibana pour visualiser les données. Pour plus d’informations, voir [Microsoft Azure Diagnostics with ELK][] (Diagnostics Microsoft Azure avec ELK).

### Outils de test de performances d’Elasticsearch

D’autres outils sont disponibles si vous évaluez Elasticsearch ou soumettez un cluster à des tests de performance. Ces outils sont conçus pour être utilisés dans un environnement de développement ou de test plutôt que dans un environnement de production. L’exemple fréquemment utilisé est [Apache JMeter][].

JMeter a été utilisé pour effectuer des évaluations et d’autres tests de charge décrits dans les documents associés à ces instructions. Le document Running Performance Tests on Elasticsearch Using JMeter (Exécuter des tests de performance sur Elasticsearch à l’aide de JMeter) explique en détail comment JMeter a été configuré et utilisé.

## Étapes suivantes
- [Elasticsearch: The Definitive Guide](https://www.elastic.co/guide/en/elasticsearch/guide/master/index.html) (Elasticsearch : le guide de référence)

[Apache JMeter]: http://jmeter.apache.org/
[Apache Lucene]: https://lucene.apache.org/
[Mise à l’échelle automatique des machines dans un groupe de machines virtuelles à échelle identique]: virtual-machines-vmss-walkthrough/
[Azure Disk Encryption pour machines virtuelles Windows et Linux IaaS - Version préliminaire]: azure-security-disk-encryption/
[l’équilibreur de charge Azure]: load-balancer-overview/
[ExpressRoute]: expressroute-introduction/
[équilibreur de charge interne]: load-balancer-internal-overview/
[Tailles de machines virtuelles]: virtual-machines-size-specs/

[Configuration mémoire requise]: #memory-requirements
[Configuration réseau requise]: #network-requirements
[Découverte des nœuds]: #node-discovery
[Ajustement des requêtes]: #query-tuning

[A Highly Available Cloud Storage Service with Strong Consistency]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx
[plug-in Azure Cloud]: https://www.elastic.co/blog/azure-cloud-plugin-for-elasticsearch
[Diagnostics Azure avec le portail Azure]: https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/
[Azure Operations Management Suite]: https://www.microsoft.com/server-cloud/operations-management-suite/overview.aspx
[Modèles de démarrage rapide Azure]: https://azure.microsoft.com/documentation/templates/
[Bigdesk]: http://bigdesk.org/
[API cat]: https://www.elastic.co/guide/en/elasticsearch/reference/1.7/cat.html
[configurez le translog]: https://www.elastic.co/guide/en/elasticsearch/reference/current/index-modules-translog.html
[routage personnalisé]: https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-routing-field.html
[valeurs Doc]: https://www.elastic.co/guide/en/elasticsearch/guide/current/doc-values.html
[d’Elasticsearch]: https://www.elastic.co/products/elasticsearch
[Elasticsearch-Head]: https://mobz.github.io/elasticsearch-head/
[Elasticsearch.Net et NEST]: http://nest.azurewebsites.net/
[Module Snapshot and Restore d’Elasticsearch ]: https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-snapshots.html
[Faking Index per User with Aliases]: https://www.elastic.co/guide/en/elasticsearch/guide/current/faking-it.html
[champ données disjoncteur]: https://www.elastic.co/guide/en/elasticsearch/reference/current/index-modules-fielddata.html#fielddata-circuit-breaker
[Force Merge]: https://www.elastic.co/guide/en/elasticsearch/reference/2.1/indices-forcemerge.html
[protocole de bavardage (gossip protocol)]: https://en.wikipedia.org/wiki/Gossip_protocol
[Kibana]: https://www.elastic.co/downloads/kibana
[Kopf]: https://github.com/lmenezes/elasticsearch-kopf
[Logstash]: https://www.elastic.co/products/logstash
[Mapping Explosion]: https://www.elastic.co/blog/found-crash-elasticsearch#mapping-explosion
[Marvel]: https://www.elastic.co/products/marvel
[Microsoft Azure Diagnostics with ELK]: https://github.com/mspnp/semantic-logging/tree/elk
[Monitoring Individual Nodes]: https://www.elastic.co/guide/en/elasticsearch/guide/current/_monitoring_individual_nodes.html#_monitoring_individual_nodes
[nginx]: http://nginx.org/en/
[l’API de nœud client]: https://www.elastic.co/guide/en/elasticsearch/client/java-api/current/node-client.html
[Optimize]: https://www.elastic.co/guide/en/elasticsearch/reference/1.7/indices-optimize.html
[plug-in de modifications PubNub]: http://www.pubnub.com/blog/quick-start-realtime-geo-replication-for-elasticsearch/
[disjoncteur de demande]: https://www.elastic.co/guide/en/elasticsearch/reference/current/index-modules-fielddata.html#request-circuit-breaker
[Search Guard]: https://github.com/floragunncom/search-guard
[Shield]: https://www.elastic.co/products/shield
[l’API de client de transport]: https://www.elastic.co/guide/en/elasticsearch/client/java-api/current/transport-client.html
[nœuds tribu]: https://www.elastic.co/blog/tribe-node
[Watcher]: https://www.elastic.co/products/watcher
[Zen]: https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-discovery-zen.html

<!----HONumber=AcomDC_0211_2016-->
