---
title: "Recommandations de coûts du conseiller Azure | Microsoft Docs"
description: "Le conseiller Azure permet d’optimiser le coût de vos déploiements Azure."
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 7b9c7037271fabd67c1ada80420ad72c340e46bb
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2017
---
# <a name="advisor-cost-recommendations"></a>Recommandations en matière de coûts du conseiller

Le conseiller vous aide à optimiser et à réduire votre dépense Azure globale en identifiant les ressources inactives et sous-utilisées. Vous pouvez obtenir des recommandations en matière de coûts dans l’onglet **Coût** du tableau de bord Advisor.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Optimiser le coût de la machine virtuelle en redimensionnant ou en arrêtant les instances sous-utilisées 
Alors que certains scénarios d’application peuvent par définition entraîner une faible utilisation, vous pouvez souvent faire des économies grâce à la gestion de la taille de vos machines virtuelles et de leur nombre. Advisor surveille l’utilisation de votre machine virtuelle pendant 14 jours et identifie les machines virtuelles faiblement utilisées. Les machines virtuelles dont l’utilisation du processeur est inférieure ou égale à 5 % et celle du réseau est inférieure ou égale à 7 Mo pendant quatre jours ou plus sont considérées comme des machines virtuelles faiblement utilisées.

Advisor vous présente une estimation du coût de l’exécution de votre machine virtuelle, pour que vous puissiez choisir de l’arrêter ou de la redimensionner.

Si vous souhaitez être plus « agressif » dans l’identification des machines virtuelles sous-utilisées, vous pouvez ajuster la règle d’utilisation moyenne du processeur par abonnement.

## <a name="use-a-cost-effective-solution-to-manage-performance-goals-of-multiple-sql-databases"></a>Utiliser une solution économique pour gérer les objectifs de performances de plusieurs bases de données SQL
Le conseiller identifie les instances de serveur SQL pouvant bénéficier de la création de pools de bases de données élastiques. Les pools de bases de données élastiques offrent une solution simple et économique pour gérer les objectifs de performances de plusieurs bases de données ayant des modèles d’utilisation variables. Pour plus d’informations sur les pools élastiques Azure, consultez la page [Qu’est-ce qu’un pool élastique Azure ?](https://azure.microsoft.com/en-us/documentation/articles/sql-database-elastic-pool/).

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Réduire les coûts en éliminant les circuits ExpressRoute non approvisionnés
Advisor identifie les circuits ExpressRoute qui présentent l’état de fournisseur *Non approvisionné* depuis plus d’un mois et recommande la suppression de ces circuits si vous ne prévoyez pas de les approvisionner avec votre fournisseur de connectivité.

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Accès aux recommandations de coût dans Azure Advisor

1. Connectez-vous au [portail Azure](https://portal.azure.com), puis ouvrez [Advisor](https://aka.ms/azureadvisordashboard).

2.  Dans le tableau de bord Advisor, cliquez sur l’onglet **Coût**.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les recommandations d’Advisor, consultez les ressources suivantes :
* [Présentation d’Azure](advisor-overview.md)
* [Prise en main](advisor-get-started.md)
* [Recommandations du conseiller en matière de performances](advisor-cost-recommendations.md)
* [Recommandations du conseiller en matière de haute disponibilité](advisor-cost-recommendations.md)
* [Recommandations du conseiller en matière de sécurité](advisor-cost-recommendations.md)
