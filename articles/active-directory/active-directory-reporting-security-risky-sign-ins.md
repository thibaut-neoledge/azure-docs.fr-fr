---
title: "Rapport de connexions risquées dans le portail Azure Active Directory | Microsoft Docs"
description: "En savoir plus sur le rapport des connexions risquées dans le portail Azure Active Directory"
services: active-directory
author: MarkusVi
manager: femila
ms.assetid: 7728fcd7-3dd5-4b99-a0e4-949c69788c0f
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: e21b7733bc0f6b8625fbdcec3529ad603ce306c1
ms.contentlocale: fr-fr
ms.lasthandoff: 05/08/2017

---
# Rapport de connexions à risque dans le portail Azure Active Directory
<a id="risky-sign-ins-report-in-the-azure-active-directory-portal" class="xliff"></a>

Grâce aux rapports sur la sécurité dans Azure Active Directory (Azure AD), vous pouvez obtenir des informations sur les risques de compromission des comptes d’utilisateur au sein de votre environnement. 

Azure AD détecte les actions suspectes liées aux comptes des utilisateurs. Pour chaque action détectée, un enregistrement appelé *événement à risque* est créé. Pour en savoir plus, voir [Événements à risque dans Azure Active Directory](active-directory-identity-protection-risk-events.md). 

Les événements à risque détectés sont utilisés pour déterminer les valeurs suivantes :

- **Connexions risquées** : une connexion risquée est une tentative de connexion susceptible de provenir d’un utilisateur autre que le propriétaire légitime d’un compte d’utilisateur. Pour en savoir plus, voir [Connexions risquées](active-directory-identityprotection.md#risky-sign-ins). 

- **Utilisateurs avec indicateur de risque** : il s’agit d’un compte d’utilisateur susceptible d’être compromis. Pour en savoir plus, voir [Utilisateurs avec indicateur de risque](active-directory-identityprotection.md#users-flagged-for-risk).  

Dans le [portail Azure](https://portal.azure.com), vous trouverez les rapports de sécurité dans le panneau **Azure Active Directory** dans la section **Sécurité**. 

![les connexions risquées.](./media/active-directory-reporting-security-risky-sign-ins/10.png)


## Édition Azure Active Directory gratuite et de base
<a id="azure-active-directory-free-and-basic-edition" class="xliff"></a>

La version gratuite et la version de base d’Azure Active Directory proposent une fonction de création de listes des connexions risquées qui ont été détectées pour les utilisateurs. Un rapport sur les événements à risque fournit les informations suivantes :

- **Utilisateur** : nom d’utilisateur qui a été utilisé pendant l’opération de connexion.
- **IP** : adresse IP du périphérique qui a été utilisé pour la connexion à Azure Active Directory.
- **Emplacement** : emplacement utilisé pour la connexion à Azure Active Directory.
- **Heure de connexion** : heure à laquelle la connexion a été effectuée.
- **État** : état de la connexion.

Ce rapport vous offre la possibilité de télécharger les données qu’il inclut.

![Connexions risquées](./media/active-directory-reporting-security-risky-sign-ins/01.png)

Selon votre analyse de la connexion à risque, vous pouvez envoyer des commentaires à Azure Active Directory par l’intermédiaire des actions suivantes :

- Résoudre
- Marquer comme faux positif
- Ignorer
- Réactiver

![Connexions risquées](./media/active-directory-reporting-security-risky-sign-ins/21.png)

Pour en savoir plus, voir [Fermeture manuelle des événements à risque](active-directory-identityprotection.md#closing-risk-events-manually).

## Éditions Premium d’Azure Active Directory
<a id="azure-active-directory-premium-editions" class="xliff"></a>

Le rapport des connexions risquées dans les éditions Premium d’Azure Active Directory vous fournit les informations suivantes :

- Informations agrégées sur les [types d’événements à risque](active-directory-identity-protection-risk-events.md) qui ont été détectés

- Une option pour télécharger le rapport


![Connexions risquées](./media/active-directory-reporting-security-risky-sign-ins/456.png)


Lorsque vous sélectionnez un événement à risque, vous obtenez une vue de rapport détaillé pour cet événement à risque qui vous permet d’effectuer les opérations suivantes :

- Une option pour configurer une [stratégie de résolution du risque utilisateur](active-directory-identityprotection.md#user-risk-security-policy)  

- Passer en revue la chronologie de la détection de l’événement à risque  

- Passer en revue la liste des utilisateurs pour lesquels cet événement à risque a été détecté

- [Fermer manuellement les événements à risque](active-directory-identityprotection.md#closing-risk-events-manually) ou réactiver un événement à risque fermé manuellement. 


![Connexions risquées](./media/active-directory-reporting-security-risky-sign-ins/457.png)

Lorsque vous sélectionnez un utilisateur, vous obtenez une vue de rapport détaillé pour cet utilisateur qui vous permet d’effectuer les opérations suivantes :

- Ouvrir la vue Toutes les connexions

- Réinitialisez le mot de passe de l'utilisateur.

- Ignorer tous les événements

- Analysez les événements à risque signalés pour l’utilisateur. 


![Connexions risquées](./media/active-directory-reporting-security-risky-sign-ins/324.png)


Pour analyser un événement à risque, sélectionnez-en un dans la liste.  
Cette opération ouvre le panneau **Détails** pour cet événement à risque. Dans le panneau **Détails**, vous avez le choix entre [fermer manuelle un événement à risque](active-directory-identityprotection.md#closing-risk-events-manually) ou réactiver un événement à risque fermé manuellement. 


![Connexions risquées](./media/active-directory-reporting-security-risky-sign-ins/325.png)





## Étapes suivantes
<a id="next-steps" class="xliff"></a>

- Pour en savoir plus sur Azure Active Directory Identity Protection, voir [Protection de l’identité Azure Active Directory](active-directory-identityprotection.md).


