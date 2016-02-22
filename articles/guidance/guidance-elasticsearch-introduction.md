
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
   ms.date="02/05/2016"
   ms.author="mabsimms"/>

# Conseils Elasticsearch sur Azure

Elasticsearch est un moteur de recherche open source hautement évolutif utilisant une base de données. Il convient aux situations nécessitant une analyse et une découverte rapides des informations contenues dans des jeux de données volumineux. Ce guide aborde certains aspects essentiels à prendre en compte lors de la création d'un cluster Elasticsearch :

- **[Conseils généraux][]** présente brièvement la structure générale d’Elasticsearch, puis explique comment implémenter un cluster Elasticsearch à l’aide d’Azure.
- **[Conseils sur l’ingestion de données][]** décrit les options de déploiement et de configuration à prendre en compte pour le cluster Elasticsearch qui attend un taux élevé d’ingestion de données.
- **[Conseils sur les tests de performances][]** décrit comment configurer un environnement permettant de tester les performances d'un cluster Elasticsearch.
- **[Conseils sur JMeter][]** décrit comment créer et utiliser un échantillonneur JUnit qui peut générer des données et les charger sur un cluster Elasticsearch.
- **[Considérations sur JMeter][]** résume l'expérience acquise lors de la construction et de l’exécution de l’ingestion de données et de plans de test. 

  > [AZURE.NOTE] Pour suivre ces instructions, vous devez déjà avoir des connaissances de base d’Elasticsearch. Pour plus d'informations, visitez le [site Web Elasticsearch](https://www.elastic.co/products/elasticsearch).

[Conseils généraux]: guidance-elasticsearch.md
[Conseils sur l’ingestion de données]: guidance-elasticsearch-data-ingestion.md
[Conseils sur les tests de performances]: guidance-elasticsearch-performance-testing-environment.md
[Conseils sur JMeter]: guidance-elasticsearch-implementing-jmeter.md
[Considérations sur JMeter]: guidance-elasticsearch-deploy-jmeter-junit-sampler.md

<!---HONumber=AcomDC_0211_2016-->