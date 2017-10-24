---
title: "Analyse des modèles d’utilisation CDN Azure | Microsoft Docs"
description: "Le client peut activer l’analyse des journaux pour Azure CDN."
services: cdn
documentationcenter: 
author: smcevoy
manager: erikre
editor: 
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2017
ms.author: v-semcev
ms.openlocfilehash: af396e9f8847421d529c32956216cfc47294edb2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Analyse des modèles d’utilisation CDN Azure

Après avoir activé le CDN pour votre application, vous pouvez surveiller l’utilisation du CDN, vérifier l’intégrité de votre application et corriger les éventuels problèmes. Azure CDN fournit ces fonctionnalités sous les formes suivantes : 

## <a name="verizon-core-reports"></a>Rapports principaux de Verizon

En tant qu’utilisateur d’Azure CDN avec un profil Standard ou Premium Verizon, vous pouvez afficher les rapports principaux de Verizon dans le portail supplémentaire Verizon. Le portail supplémentaire Verizon offre toute une variété de graphiques et de vues, et il est accessible via l’option **Gérer** du portail Azure. Pour plus d’informations, consultez [Rapports principaux de Verizon](cdn-analyze-usage-patterns.md).

## <a name="core-analytics-via-azure-diagnostic-logs"></a>Analytique principale via les journaux de diagnostic Azure

L’analytique principale est disponible pour tous les points de terminaison CDN appartenant à des profils CDN Verizon (Standard et Premium) et Akamai (Standard). Les journaux de diagnostics Azure permettent d’exporter l’analytique principale vers un stockage Azure, des hubs d’événements ou Operations Management Suite (OMS) Log Analytics. OMS Log Analytics offre une solution avec des graphiques configurables par l’utilisateur et personnalisables. Pour plus d’informations, consultez [Journaux de diagnostic Azure](cdn-azure-diagnostic-logs.md).

