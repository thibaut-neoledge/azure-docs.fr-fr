---
title: "Mise à l’échelle d’Azure Functions | Microsoft Docs"
description: "Découvrez comment Azure Functions se met à l&quot;échelle pour répondre aux besoins de vos charges de travail liées aux événements."
services: functions
documentationcenter: na
author: dariagrigoriu
manager: erikre
editor: 
tags: 
keywords: "azure functions, fonctions, traitement des événements, webhooks, calcul dynamique, architecture sans serveur"
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/03/2016
ms.author: dariagrigoriu
translationtype: Human Translation
ms.sourcegitcommit: 1a8e8bdb95b1eff6feb992727d28051463710813
ms.openlocfilehash: fce766358c61494e77464a4506dcc0e466e4e1e4


---
# <a name="scaling-azure-functions"></a>Mise à l'échelle des fonctions Azure

## <a name="introduction"></a>Introduction

La plateforme Azure Functions alloue de la puissance de calcul lors de l’exécution du code, monte en puissance quand c’est nécessaire pour gérer la charge, puis descend en puissance lorsque le code n’est pas en cours d’exécution. Cela signifie que vous ne payez rien pour les machines virtuelles inactives et qu’il n’est pas nécessaire de leur réserver de la capacité au cas où vous en auriez besoin. Le mécanisme de cette fonctionnalité est le plan de service de consommation. Cet article fournit une vue d’ensemble du fonctionnement du plan de service de consommation. 

Si vous n’êtes pas encore familiarisé avec Azure Functions, consultez l’article [Vue d’ensemble d’Azure Functions](functions-overview.md).

## <a name="choose-a-service-plan"></a>Choisir un plan de service

Lorsque vous créez une Function App, vous devez configurer un plan d’hébergement pour les fonctions contenues dans l’application. Les plans d’hébergement disponibles sont : le **Plan de consommation** et le [Plan App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). Actuellement, ce choix doit être effectué lors de la création de la Function App. Vous ne pouvez pas basculer entre ces deux options après création. Vous pouvez faire évoluer le [plan App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) entre les différents niveaux.. Aucune modification n’est actuellement prise en charge pour le plan de consommation, car la mise à l’échelle est dynamique.

### <a name="consumption-plan"></a>Plan de consommation

Dans le **Plan de consommation**, vos Function Apps sont attribuées à une instance de traitement de calcul. Si nécessaire, des instances supplémentaires sont ajoutées ou supprimées dynamiquement. En outre, les fonctions sont exécutées en parallèle, réduisant ainsi le temps total nécessaire pour traiter les demandes. Le temps d’exécution de chaque fonction est agrégé par l’application de fonction conteneur. Le coût est déterminé par la taille de la mémoire et la durée totale d’exécution de toutes les fonctions dans une Function App, et est exprimé en gigaoctet-secondes. Cette option est donc idéale si vos besoins de calcul sont intermittents ou si les durées de vos tâches ont tendance à être très courtes. Elle vous permet en effet de payer pour les ressources de calcul uniquement lorsqu’elles sont réellement utilisées. 

### <a name="app-service-plan"></a>Plan App Service

Dans le **plan App Service**, vos Function Apps seront exécutées sur une machine virtuelle dédiée, tout comme les applications web aujourd’hui (pour les références de base, Standard ou Premium). Les machines virtuelles dédiées sont allouées à vos applications App Service et Function App, et sont toujours disponibles que le code soit en cours d’exécution ou non. C’est une bonne option si vous possédez des machines virtuelles qui exécutent déjà du code mais ne sont pas complètement utilisées ou si vous envisagez d’exécuter les fonctions en continu ou presque. L’utilisation d’une machine virtuelle dissocie le coût de l’exécution et de la taille de mémoire. Par conséquent, vous pouvez ramener le coût d’un grand nombre de fonctions longues au coût de la ou des machines virtuelles sur lesquelles elles s’exécutent.

## <a name="consumption-service-plan"></a>Plan de service de consommation

Le plan de service de consommation met automatiquement à l’échelle les ressources processeur et mémoire en ajoutant des instances de traitement supplémentaires selon les besoins d’exécution des fonctions dans Function App. Chaque instance de traitement de Function App se voit affecter des ressources mémoire jusqu'à 1,5 Go.

### <a name="runtime-scaling"></a>Mise à l’échelle du runtime

La plateforme Azure Functions utilise un écouteur central pour évaluer les besoins en calcul sur la base des déclencheurs configurés et décider quand augmenter ou diminuer la taille des instances. L’écouteur central traite constamment des indications pour les besoins en mémoire et des points de données spécifiques aux déclencheurs. Par exemple, dans le cas d’un déclencheur Azure Queue Storage, les points de données comprennent la longueur de la file d’attente et le temps d’attente pour l’entrée la plus ancienne.

![](./media/functions-scale/central-listener.png)

L’unité de la mise à l’échelle est la Function App. Dans ce cas, la montée en charge signifie l’ajout d’autres instances d’une application de fonction. À l’inverse, lorsque la demande en calcul diminue, les instances de Function App sont supprimées. Leur nombre atteint finalement zéro si aucune instance n’est en cours d’exécution. 

### <a name="billing-model"></a>Modèle de facturation

La facturation pour le plan de service de consommation est décrite en détail sur la page [Tarification d’Azure Functions](https://azure.microsoft.com/pricing/details/functions). L’utilisation est rapportée par Function App uniquement lorsque le code est exécuté. Les unités de facturation sont les suivantes : 
* **La consommation des ressources en Go-s (gigaoctet-secondes)** calculée comme une combinaison de la taille de la mémoire et le temps d’exécution pour toutes les fonctions exécutées dans une Function App. 
* **Exécutions** comptées chaque fois qu’une fonction est exécutée en réponse à un événement déclenché par une liaison.



<!--HONumber=Nov16_HO4-->


