---
title: "Améliorations apportées à Cloud App Discovery dans Azure Active Directory | Microsoft Docs"
description: Fournit une vue d'ensemble de la recherche et de la gestion d'applications avec Cloud App Discovery, ainsi que des informations sur ses avantages et son fonctionnement.
services: active-directory
keywords: "détection d'applications cloud, gestion d'applications"
documentationcenter: 
author: curtand
manager: femila
tags: ignite
ms.assetid: db968bf5-22ae-489f-9c3e-14df6e1fef0a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2017
ms.author: curtand
ms.reviewer: nigu
ms.translationtype: HT
ms.sourcegitcommit: 8ad98f7ef226fa94b75a8fc6b2885e7f0870483c
ms.openlocfilehash: 59af2a5de5936d15456058aaeacfc334b9b34d4c
ms.contentlocale: fr-fr
ms.lasthandoff: 09/29/2017

---
# <a name="cloud-app-discovery-enhancements-in-azure-active-directory"></a>Améliorations apportées à Cloud App Discovery dans Azure Active Directory 
Plus de 80 % des employés admettent utiliser des applications SaaS non approuvées dans le cadre de leur travail. Nous avons intégré Cloud App Discovery dans Azure AD aux fonctionnalités de données et d’analyse de Microsoft Cloud App Security pour vous permettre d’identifier plus facilement davantage d’applications cloud non gérées. Cette nouvelle intégration nécessite des étapes de configuration différentes de celles requises jusque-là.

## <a name="what-can-i-do-now-with-the-improvements-to-cloud-app-discovery-in-azure-ad"></a>Que puis-je faire maintenant avec les améliorations apportées à Cloud App Discovery dans Azure AD ?

* **Visibilité plus profonde de l’utilisation des applications cloud** Cloud App Discovery dans Azure AD découvre maintenant **plus de 15 000 applications** à partir de tout le trafic réseau de l’organisation et de n’importe quel appareil. 
* **Aucun agent requis** Cette nouvelle version de Cloud App Discovery ne nécessite pas l’installation d’agents sur les appareils utilisateur. Au lieu de cela, la découverte s’appuie sur les fichiers journaux importés à partir de vos pare-feu et proxys. Vous pouvez découvrir les applications sur la totalité du trafic réseau de l’organisation, quel que soit l’appareil ou le système d’exploitation.
* **Analyse et alertes en continu** Cloud App Discovery dans Azure AD fournit une analyse des risques en continu détaillée, ainsi que des alertes quand une nouvelle application est en cours d’utilisation. Vous pouvez désormais en savoir plus sur l’utilisation des applications cloud dans votre organisation, par exemple sur le trafic entrant, le trafic sortant et les principaux utilisateurs des applications découvertes.

Si vous n’avez pas Active Directory Premium, consultez la section [Tarification Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/) pour en savoir plus.

Utilisez ce lien pour explorer la [nouvelle expérience de Cloud App Discovery dans Azure AD](https://portal.cloudappsecurity.com).

## <a name="next-steps"></a>Étapes suivantes
Utilisez les liens suivants pour configurer Cloud App Discovery dans Azure AD.

* [Bien démarrer avec Cloud App Discovery dans Azure AD](cloudappdiscovery-get-started.md)
* [Créer des rapports d’instantanés](cloudappdiscovery-set-up-snapshots.md)
* [Configurer le reporting continu](https://docs.microsoft.com/cloud-app-security/discovery-docker)
* [Utiliser un analyseur de journal personnalisé](https://docs.microsoft.comcommit/cloud-app-security/custom-log-parser)
