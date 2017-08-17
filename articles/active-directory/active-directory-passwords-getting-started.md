---
title: "Démarrage rapide : Azure AD SSPR | Microsoft Docs"
description: "Déployer rapidement la réinitialisation de mot de passe en libre-service Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: gahug
ms.assetid: bde8799f-0b42-446a-ad95-7ebb374c3bec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/07/2017
ms.author: joflore
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: 07c7f3ad066c735054cb339f6e09aa4d7d23f23a
ms.contentlocale: fr-fr
ms.lasthandoff: 08/11/2017

---
# <a name="quickstart-azure-ad-self-service-password-reset"></a>Démarrage rapide : Réinitialisation de mot de passe en libre-service Azure AD

> [!IMPORTANT]
> **Rencontrez-vous des problèmes de connexion ?** Dans ce cas, [voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md).

## <a name="rapidly-deploy-self-service-password-reset"></a>Déployer rapidement la réinitialisation de mot de passe en libre-service

Grâce à la réinitialisation de mot de passe en libre-service, les administrateurs informatiques disposent d’un moyen simple pour permettre aux utilisateurs de réinitialiser ou de déverrouiller leurs comptes ou leurs mots de passe. Le système inclut des rapports détaillés de suivi d’utilisation ainsi que des notifications pour vous prévenir de toute utilisation malveillante ou de tout abus.

Ce guide part du principe que vous avez déjà une version d’essai opérationnelle ou une licence de locataire Azure AD. Si vous avez besoin d’aide pour configurer Azure AD, consultez l’article [Prise en main d’Azure AD](https://azure.microsoft.com/trial/get-started-active-directory/).

1. À partir de votre locataire Azure AD existant, sélectionnez **Réinitialisation de mot de passe**

2. Dans l’écran **Propriétés**, sous Réinitialisation de mot de passe en libre-service activée, choisissez l’une des options suivantes
    * Personne : personne n’est en mesure d’utiliser la fonctionnalité de réinitialisation de mot de passe en libre-service
    * Un groupe : seuls les membres d’un groupe Azure AD que vous choisissez sont en mesure d’utiliser la fonctionnalité de réinitialisation de mot de passe en libre-service
    * Tout le monde : tous les utilisateurs disposant d’un compte dans votre locataire Azure AD peuvent se servir de la fonctionnalité de réinitialisation de mot de passe en libre-service

3. Dans l’écran **Méthodes d’authentification**, sélectionnez :
    * Nombre de méthodes requis pour la réinitialisation : nous prenons en charge une méthode minimum ou deux méthodes maximum
    * Méthodes disponibles pour les utilisateurs : nous avons besoin d’au moins une méthode, mais il peut être judicieux de disposer d’une méthode supplémentaire
        * **Messagerie** envoie un message électronique avec un code à l’adresse de messagerie d’authentification configurée pour l’utilisateur
        * **Téléphone mobile** offre à l’utilisateur la possibilité de recevoir un appel ou un SMS contenant un code sur son numéro de téléphone mobile configuré
        * **Téléphone de bureau** appelle l’utilisateur avec un code sur son numéro de téléphone configuré
        * **Questions de sécurité** nécessite que vous choisissiez les paramètres suivants :
            * Nombre de questions requises pour l’inscription désigne la valeur minimale pour une inscription réussie : un utilisateur peut choisir de répondre à davantage de questions afin de créer un pool de questions à partir desquelles extraire du contenu. Ce nombre doit être compris entre 3 et 5, et il doit être supérieur ou égal au nombre de questions requises pour la réinitialisation.
                * Vous pouvez ajouter des questions personnalisées en cliquant sur le bouton Personnalisée lors de la sélection des questions de sécurité
            * L’option Nombre de questions requises pour la réinitialisation peut indiquer de 3 à 5 questions auxquelles il faut répondre correctement avant que la réinitialisation et le déverrouillage des mots de passe utilisateur ne soient autorisés.

4. RECOMMANDÉ : **Personnalisation** vous permet de modifier le lien Contactez votre administrateur afin qu’il redirige l’utilisateur vers une page ou une adresse de messagerie que vous avez définie

5. FACULTATIF : L’écran **Inscription** présente aux administrateurs les options suivantes :
    * Obliger les utilisateurs à s’inscrire durant la connexion ?
    * Nombre de jours avant que les utilisateurs ne soient invités à reconfirmer leurs informations d’authentification

6. FACULTATIF : L’écran **« Notification »** présente aux administrateurs les options suivantes :
    * Notifier les utilisateurs lors des réinitialisations de mot de passe ?
    * Notifier tous les administrateurs quand d’autres administrateurs réinitialisent leur mot de passe ?

**À ce stade, vous avez configuré la réinitialisation de mot de passe en libre-service pour votre locataire Azure AD**. Vous pouvez vous arrêter ici ou configurer la synchronisation des mots de passe avec un domaine Active Directory local.

> [!NOTE]
> Testez la réinitialisation de mot de passe en libre-service avec un utilisateur et non un administrateur, car Microsoft applique des spécifications d’authentification forte pour les comptes Administrateur Azure. Pour plus d’informations sur la stratégie de mot de passe administrateur, consultez notre [article](active-directory-passwords-policy.md#administrator-password-policy-differences).

## <a name="configure-synchronization-to-existing-identity-source"></a>Configurer la synchronisation avec la source d’identité existante

Pour activer la synchronisation d’identité locale avec Azure AD, vous devez installer et configurer [Azure AD Connect](./connect/active-directory-aadconnect.md) sur un serveur de votre organisation. Cette application gère la synchronisation des utilisateurs et des groupes à partir de votre source d’identité existante avec votre client Azure AD.

* [Mise à niveau à partir de DirSync ou d’Azure AD Sync vers Azure AD Connect](./connect/active-directory-aadconnect-dirsync-deprecated.md)
* [Prise en main d’Azure AD Connect à l’aide de paramètres express](./connect/active-directory-aadconnect-get-started-express.md)
* [Configurez l’écriture différée du mot de passe](active-directory-passwords-writeback.md#configuring-password-writeback) pour écrire des mots de passe en différé à partir d’Azure AD dans votre répertoire local.

## <a name="disabling-self-service-password-reset"></a>Désactivation de la réinitialisation de mot de passe en libre-service

Pour désactiver la réinitialisation de mot de passe en libre-service, il vous suffit d’ouvrir votre client Azure AD, d’accéder à **Réinitialisation de mot de passe > Propriétés** > et de sélectionner **Aucune** sous **Réinitialisation de mot de passe en libre-service activée**

### <a name="learn-more"></a>En savoir plus
Les liens suivants fournissent des informations supplémentaires sur la réinitialisation de mot de passe à l’aide d’Azure AD.

* [**Licences**](active-directory-passwords-licensing.md) : configurez vos licences Azure AD.
* [**Données**](active-directory-passwords-data.md) : comprenez mieux les données requises et leur utilisation dans la gestion des mots de passe.
* [**Déploiement**](active-directory-passwords-best-practices.md) : planifiez et déployez la réinitialisation de mot de passe en libre-service pour vos utilisateurs grâce aux conseils figurant ici.
* [**Personnalisation**](active-directory-passwords-customize.md) : personnalisez l’apparence de l’interface de réinitialisation de mot de passe en libre-service de votre société.
* [**Stratégie**](active-directory-passwords-policy.md) : comprenez mieux et définissez les stratégies de mot de passe d’Azure AD.
* [**Rapports**](active-directory-passwords-reporting.md) : découvrez si, quand et où vos utilisateurs accèdent aux fonctionnalités de réinitialisation de mot de passe en libre-service.
* [**Présentation technique approfondie**](active-directory-passwords-how-it-works.md) : découvrez ce qui se passe sous le capot pour mieux comprendre le fonctionnement.
* [**Forum Aux Questions (FAQ)**](active-directory-passwords-faq.md) : Comment ? Pourquoi ? Quoi ? Où ? Qui ? Quand ? - Les réponses aux questions que vous vouliez poser depuis toujours.
* [**Résolution des problèmes**](active-directory-passwords-troubleshoot.md) : découvrez comment résoudre les problèmes courants susceptibles de survenir avec la réinitialisation de mot de passe en libre-service.

## <a name="next-steps"></a>Étapes suivantes

Avec ce guide de démarrage rapide, vous avez appris à configurer la réinitialisation de mot de passe en libre-service pour vos utilisateurs. Pour continuer sur le portail Azure afin d’effectuer ces étapes, suivez le lien ci-dessous pour accéder au portail.

> [!div class="nextstepaction"]
> [Activer la réinitialisation du mot de passe libre-service](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset)


