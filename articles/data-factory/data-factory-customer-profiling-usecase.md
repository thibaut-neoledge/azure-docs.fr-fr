<properties 
	pageTitle="Cas d’utilisation - Profilage des utilisateurs" 
	description="Découvrez comment Azure Data Factory permet de créer un workflow piloté par les données (pipeline) pour définir le profil des clients de leurs jeux." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>


<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/26/2015" 
	ms.author="spelluru"/>


# Cas d’utilisation - Profilage des utilisateurs

Azure Data Factory est un des nombreux services permettent d'implémenter la solution d’accélérateurs Cortana Analytics Suite. Pour plus d'informations sur Cortana Analytics, visitez [Cortana Analytics Suite](http://www.microsoft.com/cortanaanalytics). Dans ce document, nous décrivons un cas d'utilisation simple pour vous aider à comprendre comment Azure Data Factory permet de résoudre les problèmes d'analyse courants.

Pour sélectionner et tester ce cas d'utilisation simple, un [abonnement Azure](https://azure.microsoft.com/pricing/free-trial/) suffit. Vous pouvez déployer un exemple qui implémente ce cas d’utilisation en suivant les étapes décrites dans l’article [Exemples](data-factory-samples.md).

## Scénario

Contoso est une société qui crée des jeux pour plusieurs plateformes : des consoles de jeux, des appareils portatifs et des ordinateurs personnels (PC). A mesure que les utilisateurs jouent à ces jeux, des volumes importants de données de journalisation sont générés et suivent les modèles d'utilisation, le style de jeu et les préférences de l'utilisateur. Combiné à des données démographiques, régionales et de produits, Contoso peut effectuer une analyse pour les guider sur la façon d'améliorer l'expérience de chaque utilisateur et leur recommander des mises à niveau et des achats intégrés.

L’objectif de Contoso est d’identifier des opportunités de vente incitative et croisée basées sur le profil d'historique du jeu des utilisateurs et de développer de nouvelles fonctionnalités intéressantes pour stimuler la croissance de l'entreprise et offrir une meilleure expérience aux clients. Pour ce cas d’utilisation, nous utilisons l’exemple d’un développeur de jeux qui souhaite optimiser les habitudes de ses utilisateurs, mais ces principes s'appliquent à toute entreprise cherchant à fidéliser ses clients autour de ses produits et services et à améliorer leur expérience.

## Défis

Les entreprises de jeux doivent relever de nombreux défis lorsqu’elles tentent d’implémenter ce type de cas d'utilisation. Premièrement, des données de différentes tailles et formes doivent être reçues à partir de plusieurs sources de données, à la fois en local et dans le cloud, pour capturer les données de produits, les données d’historique des habitudes des utilisateurs, et les données des utilisateurs qui jouent sur plusieurs plateformes. Deuxièmement, les modèles d’utilisation des jeux doivent être évalués de façon raisonnable et précise. Troisièmement, les développeurs de jeux doivent mesurer l'efficacité de leur approche par un suivi des opportunités globales de vente croisée et incitative, et ajuster leurs prochaines campagnes marketing.

## Vue d’ensemble de la solution

Ce cas d'utilisation simple peut servir d’exemple montrant comment utiliser Azure Data Factory pour recevoir, préparer, transformer, analyser et publier des données.

![Workflow de bout en bout](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png) La figure ci-dessus montre comment les pipelines de données apparaissent dans l'interface utilisateur du portail Azure après leur déploiement.

1.	Le pipeline **PartitionGameLogsPipeline** lit les événements de jeu bruts depuis un stockage d’objets blob et crée des partitions basées sur l’année, le mois et le jour.
2.	Le pipeline **EnrichGameLogsPipeline** associe des événements de jeu partitionnés à des données de référence de code géographique et les enrichit en mappant les adresses IP avec les emplacements géographiques correspondants.
3.	Le pipeline **AnalyzeMarketingCampaignPipeline** s’appuie sur les données enrichies et les traite avec les données de publicité pour créer la sortie finale contenant l’efficacité de la campagne marketing.

Dans cet exemple de cas d'utilisation, Azure Data Factory sert à orchestrer les activités qui copie les données d'entrée, transforment et traitent les données avec les activités HDInsight (transformations Hive et Pig) et génèrent les données finales dans une base de données SQL Azure. Vous pouvez également visualiser le réseau des pipelines de données, les gérer et surveiller leur état à partir de l'interface utilisateur.

## Avantages

En optimisant l'analyse de leur profil d’utilisateur et en l’alignant sur les objectifs commerciaux, un développeur de jeux peut recueillir rapidement des modèles d'utilisation et analyser l'efficacité de ses campagnes marketing pour l’ensemble de ses jeux.

<!---HONumber=August15_HO6-->