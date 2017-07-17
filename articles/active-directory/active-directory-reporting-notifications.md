---
title: Notifications de rapports Azure Active Directory
description: Comment utiliser les notifications de rapports Azure Active Directory pour signaler les connexions suspectes.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: ae6d4b0e-5931-4cb3-98bf-9be91b381c92
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/15/2017
ms.author: dhanyahk;markvi
ms.custom: oldportal
ms.reviewer: dhanyahk
ms.translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 90ba006f25c27e1dc24c85fdc4ef1d5e2a8d0e3d
ms.contentlocale: fr-fr
ms.lasthandoff: 02/11/2017

---
# Notifications de rapports Azure Active Directory
<a id="azure-active-directory-reporting-notifications" class="xliff"></a>
## Quels rapports génèrent les notifications par courrier électronique
<a id="what-reports-generate-email-notifications" class="xliff"></a>
À ce stade, seul le rapport Activité de connexion anormale déclenche des notifications par courrier électronique.

## Qu'est-ce qu'une « connexion anormale » ?
<a id="what-is-an-irregular-sign-in" class="xliff"></a>
Les connexions irrégulières sont celles qui ont été identifiées par nos algorithmes d’apprentissage automatique, sur la base d'une condition de « déplacement impossible » associée à un emplacement et un périphérique de connexion anormaux. Cela peut signifier qu'un pirate a essayé de se connecter à l'aide de ce compte.

## Qui reçoit les notifications par courrier électronique ?
<a id="who-receives-the-email-notifications" class="xliff"></a>
Le courrier électronique est envoyé à tous les administrateurs généraux titulaires d'une licence Active Directory Premium. Par mesure de précaution, ce courrier électronique est également envoyé sur leur adresse de messagerie de secours. Les administrateurs doivent inclure aad-alerts-noreply@mail.windowsazure.com dans leur liste d'expéditeurs approuvés pour ne pas rater le courrier électronique.

## Quelle est la fréquence d’envoi de ces courriers électroniques ?
<a id="how-often-are-these-emails-sent" class="xliff"></a>
Le courrier électronique est envoyé si 10 nouvelles activités de connexion anormale se produisent au cours des 30 derniers jours, ou depuis que le dernier courrier électronique a été envoyé, selon la valeur qui est inférieure.

## Comment puis-je accéder au rapport mentionné dans le courrier électronique ?
<a id="how-do-i-access-the-report-mentioned-in-the-email" class="xliff"></a>
Lorsque vous cliquerez sur le lien, vous serez redirigé vers la page du rapport du portail Azure Classic. Pour accéder au rapport, vous devez être à la fois :

* Un administrateur ou un coadministrateur de votre abonnement Azure
* Un administrateur général du répertoire, titulaire d’une licence Active Directory Premium. Pour plus d’informations, consultez la page [Éditions d’Azure Active Directory](active-directory-editions.md).

## Puis-je désactiver ces courriers électroniques ?
<a id="can-i-turn-off-these-emails" class="xliff"></a>
Oui, pour désactiver les notifications liées à des connexions anormales dans le portail Azure Classic, cliquez sur **Configurer**, puis sélectionnez **Désactivé** sous la rubrique **Notifications**.

## Étapes suivantes
<a id="whats-next" class="xliff"></a>
* Curieux de savoir quels rapports de sécurité, d'audit et d'activité sont disponibles ? Découvrez [Rapports de sécurité, d'audit et d'activité d'Azure AD](active-directory-view-access-usage-reports.md)
* [Prise en main d’Azure Active Directory Premium (AD)](active-directory-get-started-premium.md)
* [Ajout d’une marque de société aux pages de connexion et du volet d’accès](active-directory-add-company-branding.md)


