---
title: "Cas d’utilisation - Profilage des utilisateurs"
description: "Découvrez comment Azure Data Factory permet de créer un workflow piloté par les données (pipeline) pour définir le profil des clients de leurs jeux."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: e07d55cf-8051-4203-9966-bdfa1035104b
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: a3eb126aa508204becbebb3dd763bdfde5890c8a


---
# <a name="use-case---customer-profiling"></a>Cas d’utilisation - Profilage des utilisateurs
Azure Data Factory est l’un des nombreux services permettant d’implémenter la solution d’accélérateurs Cortana Intelligence Suite.  Pour plus d’informations sur Cortana Intelligence, consultez [Cortana Intelligence Suite](http://www.microsoft.com/cortanaanalytics). Dans ce document, nous décrivons un cas d'utilisation simple pour vous aider à comprendre comment Azure Data Factory permet de résoudre les problèmes d'analyse courants.

Pour sélectionner et tester ce cas d’utilisation simple, un [abonnement Azure](https://azure.microsoft.com/pricing/free-trial/)suffit.  Vous pouvez déployer un exemple qui implémente ce cas d’utilisation en suivant les étapes décrites dans l’article [Exemples](data-factory-samples.md) .

## <a name="scenario"></a>Scénario
Contoso est une société qui crée des jeux pour plusieurs plateformes : des consoles de jeux, des appareils portatifs et des ordinateurs personnels (PC). À mesure que les joueurs jouent à ces jeux, des volumes importants de données de journalisation sont générés et suivent les modèles d’utilisation, le style de jeu et les préférences de l’utilisateur.  Combiné à des données démographiques, régionales et de produits, Contoso peut effectuer une analyse pour les guider sur la façon d’améliorer l’expérience des joueurs et leur recommander des mises à niveau et des achats intégrés. 

L’objectif de Contoso est d’identifier des opportunités de vente incitative/croisée basées sur l’historique du jeu des joueurs et d’ajouter de nouvelles fonctionnalités intéressantes, pour stimuler la croissance de l’entreprise et offrir une meilleure expérience aux clients. Pour ce cas d’utilisation, nous prenons l’exemple d’un développeur de jeux. L’entreprise souhaite optimiser ses jeux en fonction du comportement des joueurs. Ces principes s’appliquent à toute entreprise cherchant à fidéliser ses clients autour de ses produits et services et à améliorer leur expérience d’achat.

## <a name="challenges"></a>Défis
## <a name="solution-overview"></a>Vue d’ensemble de la solution
Ce cas d'utilisation simple peut servir d’exemple montrant comment utiliser Azure Data Factory pour recevoir, préparer, transformer, analyser et publier des données.

![Workflow de bout en bout](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)

Cette figure montre comment les pipelines de données apparaissent dans l’interface utilisateur du portail Azure après leur déploiement.

1. Le pipeline **PartitionGameLogsPipeline** lit les événements de jeu bruts depuis un stockage d’objets blob et crée des partitions basées sur l’année, le mois et le jour.
2. Le pipeline **EnrichGameLogsPipeline** associe des événements de jeu partitionnés à des données de référence de code géographique et les enrichit en mappant les adresses IP avec les emplacements géographiques correspondants.
3. Le pipeline **AnalyzeMarketingCampaignPipeline** utilise les données enrichies et les traite avec les données de publicité pour créer la sortie finale contenant l’efficacité de la campagne marketing.

Dans cet exemple, Data Factory sert à orchestrer les activités qui copient les données d’entrée, transforment et traitent les données, et à générer les données finales dans une base de données SQL Azure.  Vous pouvez également visualiser le réseau des pipelines de données, les gérer et surveiller leur état à partir de l'interface utilisateur.

## <a name="benefits"></a>Avantages
En optimisant l’analyse du profil de ses utilisateurs et en l’alignant sur ses objectifs commerciaux, le développeur de jeux peut recueillir rapidement des modèles d’utilisation et analyser l’efficacité de ses campagnes marketing.




<!--HONumber=Nov16_HO3-->


