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
ms.date: 06/23/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 079d16bbf75cd2b3b94269d684e1ae1a0e6aa967
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2017
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
![Mise à jour](./media/active-directory-identityprotection-notifications/400.png "mise à jour")
<br>

Vous pouvez désactiver l’envoi de l’e-mail de synthèse hebdomadaire.
<br><br>
![Risques pour l’utilisateur](./media/active-directory-identityprotection-notifications/62.png "risques pour l’utilisateur")
<br>

**Pour ouvrir la boîte de dialogue de configuration connexe**:

1. Dans le panneau **Azure AD Identity Protection**, cliquez sur **Paramètres**.
   <br><br>
   ![Stratégie des risques utilisateur](./media/active-directory-identityprotection-notifications/401.png "stratégie risque de l’utilisateur")
   <br>
2. Dans la section **Général**, cliquez sur **Notifications**.
   <br><br>
   ![Stratégie des risques utilisateur](./media/active-directory-identityprotection-notifications/405.png "stratégie risque de l’utilisateur")
   <br>

## <a name="see-also"></a>Voir aussi
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md)
