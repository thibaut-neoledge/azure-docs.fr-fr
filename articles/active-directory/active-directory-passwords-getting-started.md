---
title: "Démarrage rapide : Azure AD SSPR | Microsoft Docs"
description: "Déployer rapidement la réinitialisation de mot de passe en libre-service Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: bde8799f-0b42-446a-ad95-7ebb374c3bec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 7d97fad04a0aa549d0e3a182282f898302e8e41a
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/01/2017
---
# <a name="azure-ad-self-service-password-reset-rapid-deployment"></a>Déploiement rapide de la réinitialisation de mot de passe en libre-service Azure AD

> [!IMPORTANT]
> **Rencontrez-vous des problèmes de connexion ?** Dans ce cas, [voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md).

Grâce à la réinitialisation de mot de passe en libre-service, les administrateurs informatiques disposent d’un moyen simple pour permettre aux utilisateurs de réinitialiser ou de déverrouiller leurs comptes ou leurs mots de passe. Le système inclut des rapports détaillés de suivi d’utilisation ainsi que des notifications pour vous prévenir de toute utilisation malveillante ou de tout abus.

Ce guide part du principe que vous avez déjà une version d’essai opérationnelle ou une licence de locataire Azure AD. Si vous avez besoin d’aide pour configurer Azure AD, consultez l’article [Prise en main d’Azure AD](https://azure.microsoft.com/trial/get-started-active-directory/).

## <a name="enable-sspr-for-your-azure-ad-tenant"></a>Activer la réinitialisation de mot de passe en libre-service (SSPR) pour votre locataire Azure AD

1. À partir de votre locataire Azure AD existant, sélectionnez **Réinitialisation de mot de passe**

2. Dans l’écran **Propriétés**, sous Réinitialisation de mot de passe en libre-service activée, choisissez l’une des options suivantes :
    * Personne : personne n’est en mesure d’utiliser la fonctionnalité de réinitialisation de mot de passe en libre-service.
    * Sélectionné : seuls les membres d’un groupe Azure AD que vous choisissez sont en mesure d’utiliser la fonctionnalité de réinitialisation de mot de passe en libre-service. Nous vous recommandons de définir un groupe d’utilisateurs et d’utiliser ce paramètre lorsque vous déployez cette fonctionnalité pour une preuve de concept.
    * Tout le monde : tous les utilisateurs disposant d’un compte dans votre locataire Azure AD peuvent se servir de la fonctionnalité de réinitialisation de mot de passe en libre-service. Nous vous recommandons de choisir cette option lorsque vous êtes prêt à déployer cette fonctionnalité pour l’ensemble de votre locataire après avoir effectué une preuve de concept.

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
            
    ![Authentification][Authentication]

4. RECOMMANDÉ : **Personnalisation** vous permet de modifier le lien « Contactez votre administrateur » afin qu’il redirige l’utilisateur vers une page ou une adresse de messagerie que vous avez définie. Nous vous recommandons de définir ce lien à une valeur telle qu’une adresse de messagerie ou un site Web que vos utilisateurs ont l’habitude de visiter pour demander de l’assistance.

5. FACULTATIF : L’écran **Inscription** présente aux administrateurs les options suivantes :
    * Obliger les utilisateurs à s’inscrire durant la connexion ?
    * Nombre de jours avant que les utilisateurs ne soient invités à reconfirmer leurs informations d’authentification

6. FACULTATIF : L’écran **« Notification »** présente aux administrateurs les options suivantes :
    * Notifier les utilisateurs lors des réinitialisations de mot de passe ?
    * Notifier tous les administrateurs quand d’autres administrateurs réinitialisent leur mot de passe ?

**À ce stade, vous avez configuré la réinitialisation de mot de passe en libre-service pour votre locataire Azure AD**. Vos utilisateurs peuvent désormais utiliser les instructions figurant dans les articles [S’inscrire à la réinitialisation de mot de passe en libre-service](active-directory-passwords-reset-register.md) et [Réinitialiser ou modifier votre mot de passe](active-directory-passwords-update-your-own-password.md) pour mettre à jour leur mot de passe sans intervention de l’administrateur. Vous pouvez vous arrêter ici si vous êtes un client cloud uniquement, ou configurer la synchronisation des mots de passe avec un domaine Active Directory local.

> [!IMPORTANT]
> Testez la réinitialisation de mot de passe en libre-service avec un utilisateur et non un administrateur, car Microsoft applique des spécifications d’authentification forte pour les comptes Administrateur Azure. Pour plus d’informations sur la stratégie de mot de passe administrateur, consultez notre [article](active-directory-passwords-policy.md#administrator-password-policy-differences).

## <a name="configure-synchronization-to-existing-identity-source"></a>Configurer la synchronisation avec la source d’identité existante

Pour activer la synchronisation d’identité locale avec Azure AD, vous devez installer et configurer [Azure AD Connect](./connect/active-directory-aadconnect.md) sur un serveur de votre organisation. Cette application gère la synchronisation des utilisateurs et des groupes à partir de votre source d’identité existante avec votre client Azure AD.

* [Mise à niveau à partir de DirSync ou d’Azure AD Sync vers Azure AD Connect](./connect/active-directory-aadconnect-dirsync-deprecated.md)
* [Prise en main d’Azure AD Connect à l’aide de paramètres express](./connect/active-directory-aadconnect-get-started-express.md)
* [Configurez l’écriture différée du mot de passe](active-directory-passwords-writeback.md#configuring-password-writeback) pour écrire des mots de passe en différé à partir d’Azure AD dans votre répertoire local.

### <a name="on-premises-policy-change"></a>Modification de la stratégie locale

Si vous synchronisez des utilisateurs à partir d’un domaine Active Directory local et que vous souhaitez autoriser les utilisateurs à réinitialiser leurs mots de passe immédiatement, apportez la modification suivante à votre stratégie de mot de passe locale :

**Configuration de l’ordinateur** > **Stratégies** > **Paramètres Windows** > **Paramètres de sécurité** > **Stratégies de compte** > **Stratégie de mot de passe**

**Durée de validité minimale du mot de passe** : 0 jours

Ce paramètre de sécurité détermine la période de temps (en jours) pendant laquelle un utilisateur doit utiliser un mot de passe avant de pouvoir le changer. La valeur **0 jour** permet aux utilisateurs d’utiliser la réinitialisation de mot de passe en libre-service si leurs mots de passe sont modifiés par leurs équipes de support technique.

![Stratégie][Policy]

## <a name="disabling-self-service-password-reset"></a>Désactivation de la réinitialisation de mot de passe en libre-service

Pour désactiver la réinitialisation de mot de passe en libre-service, il vous suffit d’ouvrir votre client Azure AD, d’accéder à **Réinitialisation de mot de passe > Propriétés** > et de sélectionner **Aucune** sous **Réinitialisation de mot de passe en libre-service activée**

### <a name="learn-more"></a>En savoir plus
Les liens suivants fournissent des informations supplémentaires sur la réinitialisation de mot de passe à l’aide d’Azure AD.

* [Comment faire pour réussir le lancement de la réinitialisation de mot de passe en libre-service ?](active-directory-passwords-best-practices.md)
* [Réinitialisez ou modifiez votre mot de passe](active-directory-passwords-update-your-own-password.md).
* [Inscrivez-vous pour la réinitialisation du mot de passe en libre-service](active-directory-passwords-reset-register.md).
* [Vous avez une question relative à la licence ?](active-directory-passwords-licensing.md)
* [Quelles données sont utilisées par la réinitialisation de mot de passe en libre-service et quelles données vous devez renseigner pour vos utilisateurs ?](active-directory-passwords-data.md)
* [Quelles méthodes d'authentification sont accessibles aux utilisateurs ?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Quelles sont les options de stratégie disponibles avec la réinitialisation de mot de passe en libre-service ?](active-directory-passwords-policy.md)
* [Quelle est l’écriture différée de mot de passe et pourquoi dois-je m’y intéresser ?](active-directory-passwords-writeback.md)
* [Comment puis-je générer des rapports sur l’activité dans la réinitialisation de mot de passe en libre-service ?](active-directory-passwords-reporting.md)
* [Quelles sont toutes les options disponibles dans la réinitialisation de mot de passe en libre-service et que signifient-elles ?](active-directory-passwords-how-it-works.md)
* [Je pense qu’il y a une panne quelque part. Comment puis-je résoudre les problèmes de la réinitialisation de mot de passe en libre-service ?](active-directory-passwords-troubleshoot.md)
* [J’ai une question à laquelle je n’ai pas trouvé de réponse ailleurs](active-directory-passwords-faq.md)

## <a name="next-steps"></a>Étapes suivantes

Avec ce guide de démarrage rapide, vous avez appris à configurer la réinitialisation de mot de passe en libre-service pour vos utilisateurs. Pour continuer sur le portail Azure afin d’effectuer ces étapes, suivez le lien ci-dessous pour accéder au portail.

> [!div class="nextstepaction"]
> [Activer la réinitialisation du mot de passe libre-service](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset)

[Authentication]: ./media/active-directory-passwords-getting-started/sspr-authentication-methods.png "Méthodes d’authentification disponibles dans Azure Active Directory et quantité requise"
[Policy]: ./media/active-directory-passwords-getting-started/password-policy.png "Stratégie de groupe de mot de passe locale définie sur 0 jour"
