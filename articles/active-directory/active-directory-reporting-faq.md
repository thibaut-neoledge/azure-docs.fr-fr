---
title: FAQ sur les rapports Azure Active Directory | Microsoft Docs
description: FAQ sur les rapports Azure Active Directory.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/15/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.translationtype: HT
ms.sourcegitcommit: 94d1d4c243bede354ae3deba7fbf5da0652567cb
ms.openlocfilehash: 9a9ca74b959d1940bf30a903a8eab65cad87b74a
ms.contentlocale: fr-fr
ms.lasthandoff: 07/18/2017

---
# <a name="azure-active-directory-reporting-faq"></a>FAQ sur les rapports Azure Active Directory

Cet article répond aux questions fréquemment posées (FAQ) sur les rapports Azure Active Directory.  
Pour plus d’informations, consultez la page [Rapports Azure Active Directory](active-directory-reporting-azure-portal.md). 

**Q : Quelle est la rétention des données des journaux d’activité (audit et connexions) sur le Portail Azure ?** 

**R :** Nous fournissons 7 jours de données à nos clients de la version gratuite ; en passant à une licence Azure AD Premium 1 ou Premium 2, vous pouvez accéder aux données pendant 30 jours. Pour plus d’informations sur la rétention, consultez la page [Stratégies de rétention des rapports Azure Active Directory](active-directory-reporting-retention.md).

--- 

**Q : Au bout de combien de temps peut-on voir les données d’activité une fois la tâche terminée ?**

**R :** Les journaux d’activité d’audit ont une latence comprise entre 15 minutes et 1 heure. Les journaux d’activité de connexion ont une latence comprise entre 15 minutes pour la plupart des enregistrements et 2 heures pour quelques autres.

---

**Q : Faut-il être administrateur global pour voir les journaux d’activité sur le Portail Azure ou obtenir des données par le biais de l’API ?**

**R :** Non. Vous pouvez être **Lecteur Sécurité**, **Administrateur de la sécurité** ou **Administrateur général** pour afficher les données de rapports sur le Portail Azure ou y accéder par le biais de l’API.

---

**Q : Peut-on obtenir des informations du journal d’activité d’Office 365 avec le Portail Azure ?**

**R :** Bien que les journaux d’activité d’Office 365 et d’Azure AD partagent une grande partie des ressources de l’annuaire, vous devez accéder au Centre d’administration Office 365 pour obtenir une vue complète des journaux d’activité d’Office 365.

---


**Q : Quelles API faut-il utiliser pour obtenir des informations sur les journaux d’activité d’Office 365 ?**

**R :** Utilisez les API Gestion d’Office 365 pour accéder aux [journaux d’activité d’Office 365 avec une API](https://msdn.microsoft.com/office-365/office-365-managment-apis-overview).

---

**Q : Combien d’enregistrements peut-on télécharger à partir du Portail Azure ?**

**R :** Vous pouvez télécharger jusqu’à 120 000 enregistrements à partir du Portail Azure. Les enregistrements sont triés par *date* et, par défaut, vous obtenez les 120 000 enregistrements les plus récents. 

---

**Q : Combien d’enregistrements peut-on interroger avec l’API d’activités ?**

**R :** Vous pouvez interroger jusqu’à 1 million d’enregistrements (si vous n’utilisez pas l’opérateur top, qui trie les enregistrements par date). Si vous utilisez l’opérateur « top », vous pouvez interroger jusqu’à 500 000 enregistrements. Vous trouverez [ici](active-directory-reporting-api-getting-started.md) des exemples de requêtes sur l’utilisation de l’API.

---

**Q : Comment obtenir une licence Premium ?**

**R :** Consultez [Prise en main d’Azure Active Directory Premium](active-directory-get-started-premium.md) pour connaître la réponse à cette question.

---

**Q : Au bout de combien de temps peut-on consulter les données d’activité après avoir obtenu une licence Premium ?**

**R :** Si vous avez déjà des données d’activité sous une licence gratuite, vous pouvez voir les mêmes données. Si vous n’avez pas de données, cela prendra un ou deux jours.

---

**Q : Peut-on voir les données du mois dernier après avoir obtenu une licence Azure AD Premium ?**

**R :**: Si vous êtes récemment passé à une version Premium (y compris une version d’évaluation), vous pouvez voir les données jusqu’à 7 jours au début. Au fur et à mesure de l’accumulation des données, vous verrez jusqu’à 30 jours.

---

**Q : ldentity Protection m’informe d’un événement à risque, mais je ne vois pas à quelle connexion il correspond parmi toutes les connexions répertoriées. Est-ce normal ? **
**R :**: Oui, Identity Protection évalue les risques pour tous les flux d’authentification, qu’ils soient interactifs ou non. Toutefois, seules les connexions interactives sont répertoriées.

---

**Q : Comment puis-je télécharger le rapport « Utilisateurs avec indicateur de risque » dans le portail Azure ?**

**R :** L’option permettant de télécharger le rapport « Utilisateurs avec indicateur de risque » sera bientôt disponible.

---

**Q : Comment puis-je connaître la raison pour laquelle une connexion ou un utilisateur a été signalé comme étant « à risque » dans le portail Azure ?**

**R :** Les utilisateurs de l’édition Premium peuvent en savoir plus sur les événements à risque sous-jacents en cliquant sur l’utilisateur répertorié sous « Utilisateurs avec indicateur de risque » ou en cliquant sur « Connexions à risque ». Les utilisateurs de l’édition gratuite et de l’édition De base peuvent voir les utilisateurs et les connexions à risque, mais pas les événements à risque sous-jacents.

---


