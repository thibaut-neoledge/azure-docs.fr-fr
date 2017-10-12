---
title: "Afficher la consommation d’adresses IP publiques dans Azure Stack | Microsoft Docs"
description: "Les administrateurs peuvent afficher la consommation d’adresses IP publiques dans une région"
services: azure-stack
documentationcenter: 
author: ScottNapolitan
manager: darmour
editor: 
ms.assetid: 0f77be49-eafe-4886-8c58-a17061e8120f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: scottnap
ms.openlocfilehash: 7651565eebf6272f307a4ce4790ca19b41bfa826
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="view-public-ip-address-consumption-in-azure-stack"></a>Afficher la consommation d’adresses IP publiques dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Si vous êtes un administrateur cloud, vous pouvez afficher le nombre d’adresses IP publiques qui ont été allouées aux locataires, le nombre d’adresses IP publiques qui sont encore disponibles pour l’allocation et le pourcentage d’adresses IP publiques qui ont été allouées à cet emplacement.

La vignette **Utilisation des pools d’adresses IP publiques** affiche le nombre total d’adresses IP publiques qui ont été consommées dans tous les pools d’adresses IP publiques de la structure fabric, si elles ont été utilisées pour des instances de machine virtuelle IaaS du locataire, des services d’infrastructure fabric ou des ressources d’adresse IP publique créées explicitement par les locataires.

L’objectif de cette vignette est de donner aux administrateurs Azure Stack une idée du nombre total d’adresses IP publiques qui ont été consommées dans cet emplacement. Cela leur permet de déterminer si cette ressource est insuffisante.

Dans le panneau **Fournisseurs de ressources**, **Réseau**, l’élément de menu **Adresses IP publiques** sous **Ressources de locataire** répertorie uniquement les adresses IP publiques qui ont été *explicitement créées par les locataires*. Par conséquent, le nombre d’adresses IP publiques **Utilisées** dans la vignette **Utilisation des pools d’adresses IP publiques** est toujours différent (supérieur) par rapport au nombre de la vignette **Adresses IP publiques** sous **Ressources de locataire**.

## <a name="view-the-public-ip-address-usage-information"></a>Afficher les informations d’utilisation d’adresses IP publiques
Pour afficher le nombre total d’adresses IP publiques qui ont été consommées dans la région :

1. Dans le portail d’administration Azure Stack, cliquez sur **Autres services**, sous **Ressources administratives**, cliquez sur **Fournisseurs de ressources**.
2. Dans la liste des **Fournisseurs de ressources**, sélectionnez **Réseau**.
3. Le panneau **Réseau** affiche la vignette **Utilisation des pools d’adresses IP publiques** dans la section **Vue d’ensemble**.

![Panneau Fournisseur de ressources réseau](media/azure-stack-viewing-public-ip-address-consumption/image01.png)

N’oubliez pas que le nombre sous **Utilisées** représente le nombre d’adresses IP publiques de tous les pools d’adresses IP publiques de cet emplacement qui sont affectées. Le nombre sous **Libres** représente le nombre d’adresses IP publiques de tous les pools d’adresses IP publiques qui n’ont pas été affectées et sont encore disponibles. Le nombre sous **% utilisé** représente le nombre d’adresses utilisées ou affectées en pourcentage du nombre total d’adresses IP publiques dans tous les pools d’adresses IP publiques de cet emplacement.

## <a name="view-the-public-ip-addresses-that-were-created-by-tenant-subscriptions"></a>Afficher les adresses IP publiques créées par des abonnements de locataire
Pour afficher la liste des adresses IP publiques créées explicitement par des abonnements de locataire dans une région spécifique, cliquez sur **Adresses IP publiques** sous **Ressources de locataire**.

![Adresses IP publiques de locataire](media/azure-stack-viewing-public-ip-address-consumption/image02.png)

Notez que certaines adresses IP publiques qui ont été allouées dynamiquement apparaissent dans la liste, mais n’ont pas encore d’adresse associée. C’est parce que la ressource d’adresse a été créée dans le Fournisseur de ressources réseau, mais pas encore dans le contrôleur de réseau.

Le contrôleur de réseau n’affecte pas d’adresse à cette ressource tant qu’elle n’est pas réellement liée à une interface, une carte d’interface réseau, un équilibreur de charge ou une passerelle de réseau virtuel. Quand l’adresse IP publique est liée à une interface, le contrôleur de réseau lui alloue une adresse IP qui apparaît alors dans le champ **Adresse**.

## <a name="view-the-public-ip-address-information-summary-table"></a>Afficher le tableau récapitulatif des informations d’adresses IP publiques
Quand une adresse IP publique est affectée, elle apparaît dans une liste ou dans l’autre selon les cas.

| **Cas d’affectation d’adresses IP publiques** | **Apparaît dans le récapitulatif d’utilisation** | **Apparaît dans la liste d’adresses IP publiques du locataire** |
| --- | --- | --- |
| Adresse IP publique dynamique non encore affectée à une carte réseau ou un équilibreur de charge (temporaire) |Non |Oui |
| Adresse IP publique dynamique affectée à une carte réseau ou un équilibreur de charge. |Oui |Oui |
| Adresse IP publique statique affectée à une carte réseau ou un équilibreur de charge du locataire. |Oui |Oui |
| Adresse IP publique statique affectée à un point de terminaison de service d’infrastructure fabric. |Oui |Non |
| Adresse IP publique implicitement créée pour des instances de machine virtuelle IaaS et utilisée pour les règles NAT de trafic sortant sur le réseau virtuel. Ces adresses IP sont créées en arrière-plan chaque fois qu’un locataire crée une instance de machine virtuelle pour que les machines virtuelles puissent envoyer des informations sur Internet. |Oui |Non |

## <a name="next-steps"></a>Étapes suivantes
[Gérer les comptes de stockage dans Azure Stack](azure-stack-manage-storage-accounts.md)