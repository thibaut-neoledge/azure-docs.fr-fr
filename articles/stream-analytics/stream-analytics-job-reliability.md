---
title: "Éviter les interruptions de service avec les travaux Azure Stream Analytics | Microsoft Docs"
description: "Conseils pour rendre votre mise à niveau de travaux Stream Analytics résistante."
services: stream-analytics
documentationCenter: 
authors: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/27/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: a2892343432f7dced535efb3917d915736580dfb
ms.openlocfilehash: cf8eba0f68e1e803026079f02b91f1bbaec189da
ms.lasthandoff: 03/01/2017

---

# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Garantir la fiabilité des travaux Stream Analytics lors des mises à jour de service

Un service entièrement géré se distingue notamment par sa capacité à introduire de nouvelles fonctionnalités et améliorations de service à un rythme rapide. Par conséquent, Stream Analytics peut bénéficier d’un déploiement de mise à jour de service de manière hebdomadaire (ou plus fréquemment). Quel que soit le nombre de tests réalisés, il existe toujours un risque qu’un travail existant et en cours d’exécution cesse de fonctionner en raison de l’introduction d’un bogue. Pour les clients qui exécutent des travaux de traitement critiques en continu, ces risques doivent être limités. Pour réduire ce risque, les clients peuvent avoir recours à un mécanisme basé sur le modèle de **[région jumelée](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** d’Azure. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Comment les régions jumelées d’Azure résolvent-elles ce problème ?

Stream Analytics garantit que les travaux dans les régions jumelées sont mis à jour dans des lots distincts. Ainsi, il existe un écart de temps suffisant entre les mises à jour pour identifier les bogues potentiels et y remédier.

_À l’exception du Centre de l’Inde_ (dont la région jumelée, Inde du Sud, ne prend pas en charge Stream Analytics), le déploiement d’une mise à jour sur Stream Analytics ne se produit pas en même temps dans un ensemble de régions jumelées. Les déploiements dans plusieurs régions **dans le même groupe** peuvent se produire **en même temps**.

Consultez le tableau suivant pour connaître la liste des groupes jumelés :

Régions du groupe A |  | Régions du groupe B
------- | ------- | -------
Japon de l’Est | Jumelé avec | Ouest du Japon
Europe du Nord |  | Europe de l'Ouest
Centre des États-Unis |  | Est des États-Unis&2;
Est de l'Asie |  | Asie du Sud-Est
États-Unis - partie centrale septentrionale |  | Centre-Sud des États-Unis
Est de l’Australie |  | Sud-Est de l’Australie
Est des États-Unis |  | Ouest des États-Unis
Sud du Brésil |  | Centre-Sud des États-Unis
Chine du Nord |  | Chine orientale
Nord-Est de l’Allemagne |  | Centre de l’Allemagne

Nous conseillons aux clients de déployer des travaux identiques sur les deux régions jumelées. Outre la surveillance interne de Stream Analytics, nous recommandons également aux clients de surveiller les travaux comme si **les deux** étaient des travaux de production. Si un arrêt survient suite à la mise à jour du service Stream Analytics, signalez l’incident de manière appropriée et basculez les consommateurs en aval sur la sortie de travail intègre. Le signalement à l’équipe de support permettra d’empêcher la région jumelée d’être affectée par le nouveau déploiement et de maintenir l’intégrité des travaux jumelés.

