---
title: "Notifications d’Azure Active Directory Identity Protection | Microsoft Docs"
description: "Découvrez comment les notifications prennent en charge vos activités d’examen."
services: active-directory
keywords: "azure active directory identity protection, cloud app discovery, gestion d’applications, sécurité, risque, niveau de risque, vulnérabilité, stratégie de sécurité"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 65ca79b9-4da1-4d5b-bebd-eda776cc32c7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: ce3379d5b5e883c6601c40aca191e8b84e3ad8d3
ms.openlocfilehash: 0170b5d2435f6e856478ee9e55ae26c626288f75
ms.lasthandoff: 12/22/2016


---
# <a name="azure-active-directory-identity-protection-notifications"></a>Notifications d’Azure Active Directory Identity Protection
Azure AD Identity Protection envoie deux types d’e-mails de notification automatisés pour vous aider à gérer le risque des utilisateurs et les événements à risque :

* E-mail d’alerte en cas d’utilisateur compromis
* E-mail de synthèse hebdomadaire

## <a name="user-compromised-alert-email"></a>E-mail d’alerte en cas d’utilisateur compromis
Un e-mail d’alerte en cas d’utilisateur compromis est généré lorsqu’Azure AD Identity Protection identifie un compte compromis. Cet e-mail inclut un lien vers le rapport Utilisateurs associés à un indicateur de risque dans le tableau de bord d’Identity Protection. Nous vous recommandons d’examiner immédiatement les notifications des comptes compromis.

## <a name="weekly-digest-email"></a>E-mail de synthèse hebdomadaire
L’e-mail de synthèse hebdomadaire contient un récapitulatif des nouveaux événements à risque.<br>
Il inclut :

* Les utilisateurs à risque
* Activités suspectes
* Les vulnérabilités détectées
* Des liens vers les rapports connexes dans Identity Protection

<br>
![Correction](./media/active-directory-identityprotection-notifications/400.png "Remediation")
<br>

Vous pouvez désactiver l’envoi de l’e-mail de synthèse hebdomadaire.
<br><br>
![Risques des utilisateurs](./media/active-directory-identityprotection-notifications/62.png "User risks")
<br>

**Pour ouvrir la boîte de dialogue de configuration connexe**:

1. Dans le panneau **Azure AD Identity Protection**, cliquez sur **Paramètres**.
   <br><br>
   ![Stratégie de risque d’utilisateur](./media/active-directory-identityprotection-notifications/401.png "User risk policy")
   <br>
2. Dans la section **Général**, cliquez sur **Notifications**.
   <br><br>
   ![Stratégie de risque d’utilisateur](./media/active-directory-identityprotection-notifications/405.png "User risk policy")
   <br>

## <a name="see-also"></a>Voir aussi
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md)

