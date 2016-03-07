
<properties
   pageTitle="Conseils Elasticsearch sur Azure | Microsoft Azure"
   description="Conseils Elasticsearch sur Azure."
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

# Conseils Elasticsearch sur Azure

Elasticsearch est un moteur de recherche open source hautement évolutif utilisant une base de données. Il convient aux situations nécessitant une analyse et une découverte rapides des informations contenues dans des jeux de données volumineux. Ce guide aborde certains aspects essentiels à prendre en compte lors de la création d'un cluster Elasticsearch :

- **[Exécuter Elasticsearch sur Azure][]** présente brièvement la structure générale d’Elasticsearch, puis explique comment implémenter un cluster Elasticsearch à l’aide d’Azure.
- **[Paramétrage des performances d'Ingestion de données pour Elasticsearch sur Azure][]** décrit les options de déploiement et de configuration à prendre en compte pour le cluster Elasticsearch qui attend un taux élevé d'acquisition de données.
- **[Paramétrage des performances d’agrégation de données et des requêtes pour Elasticsearch sur Azure][]** résume les options que vous pouvez envisager lorsque vous cherchez à déterminer la meilleure façon d'optimiser les performances des requêtes et de la recherche sur votre système.
- **[Configuration de la résilience et de la récupération sur Elasticsearch sur Azure][]** résume les options de résilience et de récupération disponibles grâce à Elasticsearch lorsqu'elles sont hébergées dans Azure.
- **[Création d’un environnement pour le test de performance Elasticsearch sur Azure][]** décrit comment configurer un environnement permettant de tester les performances d'un cluster Elasticsearch.
- **[Implémentation d’un plan de test JMeter pour Elasticsearch][]** décrit comment créer et utiliser un échantillonneur JUnit qui peut générer des données et les charger sur un cluster Elasticsearch.
- **[Déploiement d’un échantillonneur JUnit JMeter pour tester les performances d’Elasticsearch][]** résume l'expérience acquise lors de la construction et de l’exécution de l’ingestion de données et de plans de test. 
- **[Exécution des tests de résilience Elasticsearch automatisés][]** résume l'exécution des tests de résilience utilisés dans l'article ci-dessus.
- **[Exécution des tests de performances Elasticsearch automatisés][]** résume l'exécution des tests de performances utilisés dans l'article ci-dessus.

> [AZURE.NOTE] Pour suivre ces instructions, vous devez déjà avoir des connaissances de base d’Elasticsearch. Pour plus d'informations, visitez le [site Web Elasticsearch](https://www.elastic.co/products/elasticsearch).

[Exécuter Elasticsearch sur Azure]: guidance-elasticsearch-running-on-azure.md
[Paramétrage des performances d'Ingestion de données pour Elasticsearch sur Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[Création d’un environnement pour le test de performance Elasticsearch sur Azure]: guidance-elasticsearch-creating-performance-testing-environment.md
[Implémentation d’un plan de test JMeter pour Elasticsearch]: guidance-elasticsearch-implementing-jmeter-test-plan.md
[Déploiement d’un échantillonneur JUnit JMeter pour tester les performances d’Elasticsearch]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md
[Paramétrage des performances d’agrégation de données et des requêtes pour Elasticsearch sur Azure]: guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md
[Configuration de la résilience et de la récupération sur Elasticsearch sur Azure]: guidance-elasticsearch-configuring-resilience-and-recovery.md
[Exécution des tests de résilience Elasticsearch automatisés]: guidance-elasticsearch-running-automated-resilience-tests.md
[Exécution des tests de performances Elasticsearch automatisés]: guidance-elasticsearch-running-automated-performance-tests.md

<!---HONumber=AcomDC_0224_2016-->