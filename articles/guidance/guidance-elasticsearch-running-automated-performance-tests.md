
<properties
   pageTitle="Exécution des tests de performances Elasticsearch automatisés | Microsoft Azure"
   description="Description de la façon dont vous pouvez exécuter les tests de performances dans votre propre environnement."
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
   
# Exécution des tests de performances Elasticsearch automatisés

Cet article fait [partie d’une série](guidance-elasticsearch.md).

Les documents [Paramétrage des performances d’ingestion de données pour Elasticsearch sur Azure] et [Paramétrage des performances d’agrégation de données et des requêtes pour Elasticsearch sur Azure] décrivent une série de tests de performances qui ont été exécutés sur un exemple de cluster Elasticsearch.

Ces tests ont été rédigés pour pouvoir être exécutés de manière automatique. Ce document explique comment vous pouvez répéter les tests dans votre propre environnement.

## Configuration requise

Les tests automatisés exigent les éléments suivants :

-  Un cluster Elasticsearch

- Une configuration de l’environnement JMeter comme décrit dans le document [Création d’un environnement de test de performance Elasticsearch sur Azure]

- Le logiciel suivant installé sur la machine virtuelle principale JMeter uniquement

    [Python 3.5.1](https://www.python.org/downloads/release/python-351/)


## Fonctionnement des tests
Les tests sont exécutés à l’aide de JMeter. Un serveur maître JMeter charge un plan de test et le transmet à un ensemble de serveurs subordonnés JMeter qui exécutent les tests. Le serveur maître JMeter coordonne les serveurs subordonnés JMeter et accumule les résultats.

Les plans de test suivants sont fournis :

* [elasticsearchautotestplan3nodes.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan3nodes.jmx). Ce plan de test exécute le test d’ingestion sur un cluster à 3 nœuds.

* [elasticsearchautotestplan6nodes.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan6nodes.jmx). Ce plan de test exécute le test d’ingestion sur un cluster à 6 nœuds.

* [elasticsearchautotestplan6qnodes.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan6qnodes.jmx). Ce plan de test exécute le test d’ingestion et de requête sur un cluster à 6 nœuds.

* [elasticsearchautotestplan6nodesqueryonly.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan6nodesqueryonly.jmx). Ce plan de test exécute le test de requête uniquement sur un cluster à 6 nœuds.


Vous pouvez utiliser ces plans de test comme base pour vos propres scénarios si vous avez besoin de plus ou moins de nœuds.

Les plans de test utilisent un échantillonneur de demandes JUnit pour générer et charger les données de test. Le plan de test JMeter crée et exécute cet échantillonneur, puis analyse les données de performance liées à chaque nœud Elasticsearch. Pour plus d’informations, consultez les annexes des documents [Paramétrage des performances d’ingestion de données pour Elasticsearch sur Azure] et [Paramétrage des performances d’agrégation de données et des requêtes pour Elasticsearch sur Azure].

## Création et déploiement du fichier JAR JUnit et des dépendances
Avant d’exécuter les tests de résilience, vous devez télécharger, compiler et déployer les tests JUnit situés sous le dossier performance/junitcode. Ces tests sont référencés par le plan de test JMeter. Pour plus d’informations, consultez la procédure Importation d’un projet de test JUnit existant dans Eclipse, dans le document [Déploiement d’un échantillonneur JUnit JMeter pour tester les performances d’Elasticsearch].

Il existe deux versions de tests JUnit :- [Elasticsearch1.73](https://github.com/mspnp/azure-guidance/tree/master/ingestion-and-query-tests/junitcode/elasticsearch1.73). Utilisez ce code pour effectuer les tests d’ingestion. Ces tests utilisent Elasticsearch 1.73 - [Elasticsearch2](https://github.com/mspnp/azure-guidance/tree/master/ingestion-and-query-tests/junitcode/elasticsearch2). Utilisez ce code pour effectuer les tests de requête. Ces tests utilisent Elasticsearch versions 2.1 et ultérieures.

Copiez le fichier JAR approprié avec le reste des dépendances sur vos machines JMeter. Le processus est décrit dans [Déploiement d’un échantillonneur JUnit JMeter pour tester les performances d’Elasticsearch][].

> **Important** Après avoir déployé un test JUnit, utilisez JMeter pour charger et configurer les plans de test qui référencent ce test JUnit et assurez-vous que le groupe de threads *BulkInsertLarge* référence le fichier JAR, le nom de classe JUnit et la méthode de test adéquats :
> 
> ![](./media/guidance-elasticsearch/performance-tests-image1.png)
> 
> Enregistrez les plans de test mis à jour avant d’exécuter les tests.

## Création des index de test
Chaque test effectue une ingestion et/ou des requêtes sur un index unique spécifié à l’exécution du test. Vous devez créer l’index en utilisant les schémas décrits dans les annexes des documents [Paramétrage des performances d’ingestion de données pour Elasticsearch sur Azure] et [Paramétrage des performances d’agrégation de données et des requêtes pour Elasticsearch sur Azure] et les configurer en fonction de votre scénario de test (valeurs de document activées ou désactivées, plusieurs réplicas, etc.) Notez que les plans de test supposent que l’index comprend un seul type nommé *ctip*.

## Configuration des paramètres de script de test
Copiez les fichiers de paramètres de script de test suivants sur l’ordinateur serveur JMeter :

* [run.properties](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/run.properties). Ce fichier spécifie le nombre de threads de test JMeter à utiliser, la durée du test (en secondes), l’adresse IP d’un nœud (ou d’un équilibreur de charge) dans le cluster Elasticsearch et le nom du cluster :

  ```ini
  nthreads=3
  duration=300
  elasticip=<IP Address or DNS Name Here>
  clustername=<Cluster Name Here>
  ```
  
  Modifiez ce fichier et spécifiez les valeurs appropriées pour vos test et cluster.

* [query-config-win.ini](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/query-config-win.ini) et [query-config-nix.ini](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/query-config-nix.ini). Ces deux fichiers contiennent les mêmes informations ; le fichier *win* est mis en forme en fonction des chemins et noms de fichiers Windows, tandis que le fichier *nix* est mis en forme en fonction des chemins et noms de fichiers Linux :

  ```ini
  [DEFAULT]
  debug=true #if true shows console logs.

  [RUN]
  pathreports=C:\\Users\\administrator1\\jmeter\\test-results\\ #path where tests results are saved.
  jmx=C:\\Users\\administrator1\\testplan.jmx #path to the JMeter test plan.
  machines=10.0.0.1,10.0.02,10.0.0.3 #IPs of the Elasticsearch data nodes separated by commas.
  reports=aggr,err,tps,waitio,cpu,network,disk,response,view #Name of the reports separated by commas.
  tests=idx1 #Elasticsearch index name to test.
  properties=run.properties #Name of the properties file.
  ```

  Modifiez ce fichier pour spécifier les emplacements des résultats des tests, le nom du plan de test JMeter à exécuter, les adresses IP des nœuds de données Elasticsearch, les rapports contenant les données de performances brutes à générer et le (ou les noms) de l’index testé. Si le fichier *run.properties* se trouve dans un autre dossier ou répertoire, spécifiez le chemin complet de ce fichier.

## Exécution des tests

* Copiez le fichier [query-test.py](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/query-test.py) sur l’ordinateur serveur JMeter, dans le même dossier que les fichiers *run.properties* et *query-config-win.ini* (*query-config-nix.ini*).

* Vérifiez que *jmeter.bat* (Windows) ou *jmeter.sh* (Linux) se trouvent sur le chemin d’exécutable de votre environnement.

* Exécutez le script *query-test.py* à partir de la ligne de commande pour lancer des tests :

  ```cmd
  py query-test.py
  ```

* Quand le test est terminé, les résultats sont stockés dans l’ensemble de fichiers CSV spécifié dans le fichier *query-config-win.ini* (*query-config-nix.ini*). Vous pouvez utiliser Excel pour analyser ces données et les présenter sous forme de graphique.


[Paramétrage des performances d’ingestion de données pour Elasticsearch sur Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[Paramétrage des performances d’agrégation de données et des requêtes pour Elasticsearch sur Azure]: guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md
[Création d’un environnement de test de performance Elasticsearch sur Azure]: guidance-elasticsearch-creating-performance-testing-environment.md
[Déploiement d’un échantillonneur JUnit JMeter pour tester les performances d’Elasticsearch]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md

<!---HONumber=AcomDC_0224_2016-->
