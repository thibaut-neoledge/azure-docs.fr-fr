---
title: Types de quotas dans Azure Stack | Microsoft Docs
description: "Passez en revue les différents types de quotas disponibles pour les services et les ressources dans Azure Stack."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 8/23/2017
ms.author: erikje
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: 9c65abd596b1a67175a4f91558c318f16ddbb11f
ms.contentlocale: fr-fr
ms.lasthandoff: 09/15/2017

---
# <a name="quota-types-in-azure-stack"></a>Types de quotas dans Azure Stack
Les [quotas](azure-stack-plan-offer-quota-overview.md#plans) définissent les limites de ressources qu’un abonnement utilisateur peut approvisionner ou consommer. Par exemple, un quota peut autoriser un utilisateur de créer jusqu’à cinq machines virtuelles. Chaque ressource peut avoir ses propres types de quotas.

## <a name="compute-quota-types"></a>Types de quotas de capacité de traitement (compute)
| **Type** | **Valeur par défaut** | **Description** |
| --- | --- | --- |
| Nombre maximal de machines virtuelles |50 | Nombre maximal de machines virtuelles qu’un abonnement peut créer à cet emplacement. |
| Nombre maximal de cœurs de machine virtuelle |100 | Nombre maximal de cœurs qu’un abonnement peut créer à cet emplacement (par exemple, une machine virtuelle A3 a quatre cœurs). |
| Nombre maximal de groupes à haute disponibilité |10 | Nombre maximal de groupes à haute disponibilité qui peuvent être créés à cet emplacement. |
| Nombre maximal de groupes de machines virtuelles identiques |100 | Nombre maximal de groupes de machines virtuelles identiques qui peuvent être créés à cet emplacement. |

> [!NOTE]
> Les quotas de capacité de traitement (compute) ne sont pas appliqués dans cette version Technical Preview.
> 
> 

## <a name="storage-quota-types"></a>Types de quotas de stockage
| **Item** | **Valeur par défaut** | **Description** |
| --- | --- | --- |
| Capacité maximale (Go) |500 |Capacité de stockage totale qui peut être consommée par un abonnement à cet emplacement. |
| Nombre total de comptes de stockage |20 |Nombre maximal de comptes de stockage qu’un abonnement peut créer à cet emplacement. |

## <a name="network-quota-types"></a>Types de quotas pour les réseaux
| **Item** | **Valeur par défaut** | **Description** |
| --- | --- | --- |
| Nombre maximal d’adresses IP publiques |50 |Nombre maximal d’adresses IP publiques qu’un abonnement peut créer à cet emplacement. |
| Nombre maximal de réseaux virtuels |50 |Nombre maximal de réseaux virtuels qu’un abonnement peut créer à cet emplacement. |
| Nombre maximal de passerelles de réseau virtuel |1 |Nombre maximal de passerelles de réseau virtuel qu’un abonnement peut créer à cet emplacement. |
| Nombre maximal de connexions réseau |2 |Nombre maximal de connexions réseau (point à point ou site à site) qu’un abonnement peut créer pour toutes les passerelles de réseau virtuel à cet emplacement. |
| Nombre maximal d’équilibreurs de charge |50 |Nombre maximal d’équilibreurs de charge qu’un abonnement peut créer à cet emplacement. |
| Nombre max de cartes réseau |100 |Nombre maximal d’interfaces réseau qu’un abonnement peut créer à cet emplacement. |
| Nombre maximal de groupes de sécurité réseau |50 |Nombre maximal de groupes de sécurité réseau qu’un abonnement peut créer à cet emplacement. |

## <a name="view-an-existing-quota"></a>Afficher un quota existant
1. Cliquez sur **Autres services** > **Fournisseurs de ressources**.
2. Sélectionnez le service avec le quota que vous voulez afficher.
3. Cliquez sur **Quotas**, puis sélectionnez le quota que vous voulez afficher.

## <a name="next-steps"></a>Étapes suivantes
[Découvrez plus d’informations sur les plans, les offres et les quotas.](azure-stack-plan-offer-quota-overview.md)

[Créez des quotas lors de la création d’un plan.](azure-stack-create-plan.md)

