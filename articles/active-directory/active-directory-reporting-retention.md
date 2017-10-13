---
title: "Stratégies de rétention des rapports Azure Active Directory | Microsoft Docs"
description: "Stratégies de rétention des données de rapport dans Azure Active Directory"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/05/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 047f18acf192c75ac5904d7cfe10f19ad18e2888
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-report-retention-policies"></a>Stratégies de rétention des rapports Azure Active Directory


Cette rubrique fournit des réponses aux questions plus courantes en rapport avec la conservation des données pour les différents rapports d’activité dans Azure Active Directory. 

**Q : Comment obtenir la collecte des données des activités démarrée ?**

**R :**

| Édition d’Azure AD | Début de la collection |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | Lorsque vous vous inscrivez pour un abonnement |
| Azure AD Gratuit | La première fois que vous ouvrez le [panneau Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) ou utilisez les [API de création de rapports](https://aka.ms/aadreports)  |

---
**Q: Quand vos données d’activité sont-elles disponibles dans le portail Azure ?**

**R :**

- **Immédiatement** : si vous avez déjà travaillé avec des rapports dans le portail Azure Classic
- **Dans les 2 heures** : si vous n’avez pas encore activé la création de rapports dans le portail Azure Classic

---
**Q : Comment obtenir la collecte des signaux de sécurité démarrée ?**  

**R :** Pour les signaux de sécurité, le processus de collecte démarre quand vous choisissez d’utiliser Identity Protection Center. 


---
**Q : Pendant combien de temps les données collectées sont-elles stockées ?**

**R :**

**Rapports d’activité**    

| Rapport                 | Azure AD Gratuit | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Audit de répertoire        | 7 jours        | 30 jours             | 30 jours             |
| Activité de connexion       | N/A           | 30 jours             | 30 jours             |

**Signaux de sécurité**

| Rapport         | Azure AD Gratuit | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--                 | :--                 |
| Les utilisateurs à risque  | 7 jours        | 30 jours             | 90 jours             |
| Connexions risquées | 7 jours        | 30 jours             | 90 jours             |

---
