---
title: "Lancement de la réinitialisation du mot de passe libre-service Azure AD | Microsoft Docs"
description: "Conseils pour réussir le lancement de la réinitialisation du mot de passe libre-service Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: gahug
ms.assetid: f8cd7e68-2c8e-4f30-b326-b22b16de9787
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/17/2017
ms.author: joflore
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 5bd17fc68447b8387dfaad33fe2d0055596ec086
ms.contentlocale: fr-fr
ms.lasthandoff: 05/26/2017

---
# <a name="roll-out-password-reset-for-users"></a>Lancer la réinitialisation du mot de passe des utilisateurs

De nombreux clients exécutent les étapes qui suivent pour garantir un lancement sans problèmes de la fonctionnalité de réinitialisation du mot de passe libre-service.

1. [Activez la réinitialisation du mot de passe dans votre répertoire](active-directory-passwords-getting-started.md).
2. [Configurez les autorisations Active Directory locales pour l’écriture différée du mot de passe](active-directory-passwords-how-it-works.md#active-directory-permissions).
3. [Configurez l’écriture différée du mot de passe](active-directory-passwords-writeback.md#configuring-password-writeback) pour écrire des mots de passe en différé à partir d’Azure AD dans votre répertoire local
4. [Affectez et vérifiez les licences requises](active-directory-passwords-licensing.md).
5. Si vous souhaitez effectuer un lancement progressif, vous pouvez éventuellement limiter la réinitialisation du mot de passe à un groupe d’utilisateurs pour déployer la fonctionnalité lentement au fil du temps. Pour ce faire, définissez **Réinitialisation du mot de passe en libre-service activée** en le faisant basculer de **Tout le monde** à **Un groupe**, puis sélectionnez un groupe de sécurité pour l’activer pour la réinitialisation du mot de passe. Des licences doivent être affectées à tous les membres de ce groupe, et c’est un excellent moyen pour activer [Gestion des licences par groupe](active-directory-passwords-licensing.md#enable-group-or-user-based-licensing).
6. Renseignez l’ensemble minimal de [Données d’authentification](active-directory-passwords-data.md) en fonction de votre stratégie.
7. Apprenez à vos utilisateurs à utiliser la réinitialisation du mot de passe libre-service, en leur envoyant les instructions à suivre pour s’inscrire et réinitialiser le mot de passe.
    > [!NOTE]
    > Testez la réinitialisation de mot de passe en libre-service avec un utilisateur et non un administrateur, car Microsoft applique des spécifications d’authentification forte pour les comptes Administrateur Azure. Pour plus d’informations sur la stratégie de mot de passe administrateur, consultez notre [article détaillé](active-directory-passwords-how-it-works.md).

8. Vous pouvez choisir d’appliquer l’inscription à tout moment et demander aux utilisateurs de reconfirmer leurs informations d’authentification après un certain laps de temps. Si vous ne souhaitez pas que les utilisateurs s’inscrivent, vous pouvez [déployer une réinitialisation du mot de passe sans nécessiter d’inscription de l’utilisateur final](active-directory-passwords-data.md).
9. Au fil du temps, passez en revue l’inscription et l’utilisation des utilisateurs en affichant les [rapports fournis par Azure AD](active-directory-passwords-reporting.md).

## <a name="email-based-rollout"></a>Déploiement par courrier électronique

De nombreux clients estiment qu’une campagne par courrier électronique, accompagnée d’instructions simples d’utilisation, constitue le moyen le plus simple pour que les utilisateurs aient recours à la réinitialisation du mot de passe libre-service. [Nous avons créé trois courriers électroniques simples que vous pouvez utiliser comme modèles pour vous aider dans votre lancement.](https://onedrive.live.com/?authkey=%21AD5ZP%2D8RyJ2Cc6M&id=A0B59A91C740AB16%2125063&cid=A0B59A91C740AB16)

* **Bientôt disponible** : modèle de courrier électronique à utiliser dans les semaines ou les jours précédant le lancement pour informer les utilisateurs s’ils ont besoin d’effectuer une action.
* **Disponible dès maintenant** : modèle de courrier électronique à utiliser le jour du lancement pour inciter les utilisateurs à s’inscrire et à confirmer leurs données d’authentification afin de pouvoir utiliser la réinitialisation du mot de passe libre-service lorsqu’ils en auront besoin.
* **Rappel d’inscription** : modèle de courrier électronique à utiliser dans les jours ou les semaines qui suivent le déploiement pour rappeler aux utilisateurs de s’inscrire et de confirmer leurs données d’authentification.

## <a name="creating-your-own-password-portal"></a>Création de votre propre portail de mot de passe

Nombre de nos clients plus importants choisissent d’héberger une page web et de créer une entrée DNS racine, comme https://passwords.contoso.com. Ils renseignent cette page avec des liens vers les portails de réinitialisation de mot de passe Azure AD, d’inscription à la réinitialisation de mot de passe et de modification de mot de passe, et y indiquent d’autres informations propres à leur organisation. Dans toutes vos communications par courrier électronique ou prospectus, vous pouvez inclure une URL de marque, facile à retenir que les utilisateurs peuvent suivre quand ils doivent utiliser les services.

* Portail de réinitialisation de mot de passe : https://passwordreset.microsoftonline.com/
* Portail d’inscription à la réinitialisation de mot de passe : http://aka.ms/ssprsetup
* Portail de modification de mot de passe : https://account.activedirectory.windowsazure.com/ChangePassword.aspx

## <a name="using-enforced-registration"></a>Utilisation de l’inscription forcée

Si vous souhaitez que vos utilisateurs s’inscrivent pour la réinitialisation du mot de passe, vous pouvez les forcer à s’inscrire lorsqu’ils se connectent à l’aide d’Azure AD. Vous pouvez activer cette option dans le panneau **Réinitialisation de mot de passe** de votre répertoire en activant l’option **Demander aux utilisateurs de s’inscrire lorsqu’ils se connectent** dans l’onglet **Inscription**.

Les administrateurs peuvent demander aux utilisateurs de se réinscrire après un laps de temps en définissant l’option **Nombre de jours avant que les utilisateurs ne soient invités à reconfirmer leurs informations d’authentification** sur une valeur comprise entre 0 et 730 jours.

Une fois que vous avez activé cette option, lorsque les utilisateurs se connectent, un message les informe que leur administrateur leur a demandé de vérifier leurs informations d’authentification.

## <a name="populate-authentication-data"></a>Renseigner les données d’authentification

Si vous [renseignez les données d’authentification pour vos utilisateurs](active-directory-passwords-data.md), ceux-ci n’ont alors pas besoin de s’inscrire pour la réinitialisation de mot de passe pour pouvoir utiliser la réinitialisation du mot de passe libre-service. Tant que leurs données d’authentification définies sont conformes à la stratégie de réinitialisation de mot de passe que vous avez définie, les utilisateurs sont en mesure de réinitialiser leurs mots de passe.

## <a name="disabling-self-service-password-reset"></a>Désactivation de la réinitialisation de mot de passe en libre-service

Pour désactiver la réinitialisation de mot de passe en libre-service, il vous suffit d’ouvrir votre locataire Azure AD, d’accéder à **Réinitialisation de mot de passe**, **Propriétés**, et de sélectionner **Personne** sous **Réinitialisation de mot de passe en libre-service activée**

## <a name="next-steps"></a>Étapes suivantes

Les liens suivants fournissent des informations supplémentaires sur la réinitialisation de mot de passe à l’aide d’Azure AD.

* [**Démarrage rapide**](active-directory-passwords-getting-started.md) : soyez rapidement opérationnel avec la gestion des mots de passe en libre-service Azure AD. 
* [**Licences**](active-directory-passwords-licensing.md) : configurez vos licences Azure AD.
* [**Données**](active-directory-passwords-data.md) : comprenez mieux les données requises et leur utilisation dans la gestion des mots de passe.
* [**Personnalisation**](active-directory-passwords-customize.md) : personnalisez l’apparence de l’interface de réinitialisation de mot de passe en libre-service de votre société.
* [**Stratégie**](active-directory-passwords-policy.md) : comprenez mieux et définissez les stratégies de mot de passe d’Azure AD.
* [**Écriture différée du mot de passe**](active-directory-passwords-writeback.md) : fonctionnement de l’écriture différée du mot de passe avec votre répertoire local.
* [**Rapports**](active-directory-passwords-reporting.md) : découvrez si, quand et où vos utilisateurs accèdent aux fonctionnalités de réinitialisation de mot de passe en libre-service.
* [**Présentation technique approfondie**](active-directory-passwords-how-it-works.md) : découvrez ce qui se passe sous le capot pour mieux comprendre le fonctionnement.
* [**Forum Aux Questions (FAQ)**](active-directory-passwords-faq.md) : Comment ? Pourquoi ? Quoi ? Où ? Qui ? Quand ? - Les réponses aux questions que vous vouliez poser depuis toujours.
* [**Résolution des problèmes**](active-directory-passwords-troubleshoot.md) : découvrez comment résoudre les problèmes courants susceptibles de survenir avec la réinitialisation de mot de passe en libre-service.
