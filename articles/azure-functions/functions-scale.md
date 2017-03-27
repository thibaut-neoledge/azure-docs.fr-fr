---
title: "Choisir un plan d’hébergement Azure Functions | Microsoft Docs"
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
ms.date: 03/14/2017
ms.author: dariagrigoriu, glenga
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 9b5dabe5e27e68a4a9f140d4f07131caf7306e32
ms.lasthandoff: 03/15/2017


---
# <a name="choose-the-correct-service-plan-for-azure-functions"></a>Choisir le plan de service approprié pour Azure Functions

## <a name="introduction"></a>Introduction

Azure Functions contient deux plans de service différents : le plan de consommation et le plan App Service. Le plan de consommation alloue automatiquement la puissance de calcul lors de l’exécution du code, augmente la taille des instances quand c’est nécessaire pour gérer la charge, puis diminue leur taille lorsque le code n’est pas en cours d’exécution. Cela signifie que vous ne payez rien pour les machines virtuelles inactives et qu’il n’est pas nécessaire de leur réserver de la capacité au cas où vous en auriez besoin. Cet article est consacré au plan de service de consommation. Pour plus d’informations sur le fonctionnement du plan App Service, consultez l’article [Présentation détaillée des plans d’Azure App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). 

Si vous n’êtes pas encore familiarisé avec Azure Functions, consultez l’article [Vue d’ensemble d’Azure Functions](functions-overview.md).

## <a name="service-plan-options"></a>Options de plan de service

Lorsque vous créez une Function App, vous devez configurer un plan d’hébergement pour les fonctions contenues dans l’application. Les plans d’hébergement disponibles sont : le **Plan de consommation** et le [Plan App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). Actuellement, ce choix doit être effectué lors de la création de l’application de fonction. Vous ne pouvez pas basculer entre ces deux options après la création. Vous pouvez faire évoluer le [plan App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) entre les différents niveaux.. Aucune modification n’est actuellement prise en charge pour le plan de consommation, car la mise à l’échelle est dynamique.

### <a name="consumption-plan"></a>Plan de consommation

Dans le **plan de consommation**, vos applications de fonctions sont attribuées à une instance de traitement de calcul. Si nécessaire, des instances supplémentaires sont ajoutées ou supprimées dynamiquement. En outre, les fonctions sont exécutées en parallèle, réduisant ainsi le temps total nécessaire pour traiter les demandes. Le temps d’exécution de chaque fonction est agrégé par l’application de fonction conteneur. Le coût est déterminé par la taille de la mémoire et la durée totale d’exécution de toutes les fonctions dans une fonction d’application, et il est exprimé en gigaoctet-secondes. Cette option est donc idéale si vos besoins de calcul sont intermittents ou si les durées de vos tâches ont tendance à être très courtes. Elle vous permet en effet de payer pour les ressources de calcul uniquement lorsqu’elles sont réellement utilisées. La section suivante fournit plus d’informations sur le fonctionnement du plan de consommation.

### <a name="app-service-plan"></a>Plan App Service

Dans le **plan App Service**, vos applications de fonctions sont exécutées sur des machines virtuelles dédiées, tout comme des applications web aujourd’hui (pour les références de base, Standard ou Premium). Les machines virtuelles dédiées sont allouées à vos applications App Service et à vos applications de fonctions, et sont toujours disponibles que le code soit en cours d’exécution ou non. C’est une bonne option si vous possédez des machines virtuelles qui exécutent déjà du code mais ne sont pas complètement utilisées ou si vous envisagez d’exécuter les fonctions en continu ou presque. L’utilisation d’une machine virtuelle dissocie le coût de l’exécution et de la taille de mémoire. Vous pouvez donc limiter le coût d’un grand nombre de fonctions longues au coût des machines virtuelles sur lesquelles elles s’exécutent. Pour plus d’informations sur le fonctionnement du plan App Service, consultez l’article [Présentation détaillée des plans d’Azure App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). 

## <a name="how-the-consumption-plan-works"></a>Fonctionnement du plan de consommation

Le plan de consommation met automatiquement à l’échelle les ressources processeur et mémoire en ajoutant des instances de traitement supplémentaires selon les besoins d’exécution des fonctions dans une application de fonction. Chaque instance de traitement des applications de fonctions se voit affecter jusqu'à 1,5 Go de ressources mémoire.

Lors de l’exécution sur un plan de consommation, si une application de fonction est inactive, il peut y avoir jusqu’à 10 minutes par jour dans le traitement des nouveaux objets blob. Une fois la Function App en cours d’exécution, les objets blob sont traités plus rapidement. Pour éviter ce délai initial, utilisez un plan App Service régulier avec Toujours actif activé ou un autre mécanisme pour déclencher le traitement des objets blob, par exemple un message de file d’attente contenant le nom de l’objet blob. 

Lorsque vous créez une application de fonction, vous devez créer ou lier un compte de stockage Azure à usage général qui prend en charge le stockage Blob, File d’attente et Table. En interne, les fonctions Azure utilise le stockage Azure pour les opérations telles que la gestion des déclencheurs et la journalisation des exécutions de fonctions. Certains comptes de stockage ne prennent pas en charge les files d’attente et les tables, comme les comptes de stockage Blob uniquement (notamment le stockage Premium) et les comptes de stockage à usage général avec la réplication ZRS. Ces comptes sont filtrés à partir du panneau du compte de stockage lors de la création d’une nouvelle application de fonction.

Lorsque vous utilisez le plan d’hébergement de la consommation, le contenu de l’application de fonction (par exemple, les fichiers de code de fonction et la configuration de liaison) est stocké sur des partages de fichiers Azure dans le compte de stockage principal. Si vous supprimez le compte de stockage principal, ce contenu sera supprimé et ne peut pas être récupéré.

Pour en savoir plus sur les types de compte de stockage, consultez la page [Présentation des services de stockage Azure] (.. / storage/storage-introduction.md#introducing-the-azure-storage-services).

### <a name="runtime-scaling"></a>Mise à l’échelle du runtime

Functions utilise un contrôleur de mise à l’échelle pour évaluer les besoins en calcul sur la base des déclencheurs configurés et décider quand augmenter ou diminuer la taille des instances. Le contrôleur de mise à l’échelle traite en continu des indications pour les besoins en mémoire et des points de données propres aux déclencheurs. Par exemple, dans le cas d’un déclencheur Stockage File d’attente Azure, les points de données comprennent la longueur de la file d’attente et le temps d’attente pour l’entrée la plus ancienne.

![](./media/functions-scale/central-listener.png)

L’unité de mise à l’échelle est l’application de fonction. Dans ce cas, l’augmentation de la taille des instances correspond à l’ajout d’instances d’une application de fonction. À l’inverse, lorsque la demande de calcul est réduite, des instances d’application de fonction sont supprimées. Le nombre d’instances est finalement réduit à zéro lorsque aucune instance n’est exécutée. 

### <a name="billing-model"></a>Modèle de facturation

La facturation du plan de consommation est décrite en détail dans la page [Tarification d’Azure Functions](https://azure.microsoft.com/pricing/details/functions). L’utilisation est rapportée par l’application de fonction uniquement lorsque le code est exécuté. Les unités de facturation sont les suivantes : 
* **La consommation des ressources en Go-s (gigaoctet-secondes)** calculée comme une combinaison de la taille de la mémoire et le temps d’exécution pour toutes les fonctions exécutées dans une Function App. 
* **Exécutions** comptées chaque fois qu’une fonction est exécutée en réponse à un événement déclenché par une liaison.

