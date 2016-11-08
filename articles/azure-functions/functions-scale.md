---
title: Mise à l’échelle d’Azure Functions | Microsoft Docs
description: Découvrez comment Azure Functions se met à l'échelle pour répondre aux besoins de vos charges de travail liées aux événements.
services: functions
documentationcenter: na
author: dariagrigoriu
manager: erikre
editor: ''
tags: ''
keywords: azure functions, fonctions, traitement des événements, webhooks, calcul dynamique, architecture sans serveur

ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/03/2016
ms.author: dariagrigoriu

---
# Mise à l’échelle d’Azure Functions
## Introduction
L’un des avantages d’Azure Functions réside dans le fait que les ressources de calcul sont utilisées uniquement si nécessaire. Cela signifie que vous ne payez rien pour les machines virtuelles inactives et qu’il n’est pas nécessaire de leur réserver de la capacité au cas où vous en auriez besoin. Au lieu de cela, la plateforme alloue de la puissance de calcul lors de l’exécution du code, monte en puissance quand c’est nécessaire pour gérer la charge, puis descend en puissance lorsque le code n’est pas en cours d’exécution.

Le mécanisme de cette nouvelle fonctionnalité est le plan de service dynamique.

Cet article fournit un aperçu du fonctionnement du plan de service dynamique et de la mise à l’échelle à la demande de la plateforme pour exécuter votre code.

Si vous ne connaissez pas encore Azure Functions, consultez l’article [Vue d’ensemble d’Azure Functions](functions-overview.md) pour mieux comprendre ses fonctionnalités.

## Configurer Azure Functions
Il existe deux principaux paramètres liés à la mise à l’échelle :

* [Plan Azure App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) ou plan de service dynamique
* Taille de la mémoire pour l’environnement d’exécution

Le coût d’une fonction varie selon le plan de service que vous sélectionnez. Avec un plan de service dynamique, le coût est une fonction de la durée d’exécution, de la taille de la mémoire et du nombre d’exécutions. Les coûts s’accumulent uniquement lorsque votre code est effectivement en cours d’exécution.

Un plan App Service héberge vos fonctions sur des machines virtuelles existantes qui peuvent également être utilisées pour exécuter un autre code. En dehors du paiement de ces machines virtuelles chaque mois, l’exécution des fonctions sur les machines virtuelles ne représente pas de frais supplémentaires.

## Choisir un plan de service
Lors de la création d’applications, vous pouvez choisir de les exécuter sur un plan de service dynamique ou un [plan App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). Dans le plan App Service, vos fonctions seront exécutées sur une machine virtuelle dédiée, tout comme les applications web aujourd’hui (pour les références de base, Standard ou Premium). Cette machine virtuelle dédiée est allouée à vos applications et à vos fonctions et est toujours disponible que le code soit en cours d’exécution ou non. C’est une bonne option si vous possédez des machines virtuelles qui exécutent déjà du code mais ne sont pas complètement utilisées ou si vous envisagez d’exécuter les fonctions en continu ou presque. L’utilisation d’une machine virtuelle dissocie le coût de l’exécution et de la taille de mémoire. Par conséquent, vous pouvez ramener le coût d’un grand nombre de fonctions longues au coût de la ou des machines virtuelles sur lesquelles elles s’exécutent.

[!INCLUDE [Plan de service dynamique](../../includes/functions-dynamic-service-plan.md)]

<!---HONumber=AcomDC_0803_2016-->