---
title: "Lancement de la réinitialisation du mot de passe libre-service Azure AD | Microsoft Docs"
description: "Conseils pour réussir le lancement de la réinitialisation du mot de passe libre-service Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: f8cd7e68-2c8e-4f30-b326-b22b16de9787
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 0be1616f5df915e566dc73c15dbea2e53177aa1c
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2017
---
# <a name="how-to-successfully-rollout-self-service-password-reset"></a>Comment réussir le lancement de la réinitialisation de mot de passe en libre-service

De nombreux clients exécutent ces étapes pour garantir un lancement sans problème de la fonctionnalité de réinitialisation de mot de passe en libre-service.

1. [Activez la réinitialisation du mot de passe dans votre répertoire](active-directory-passwords-getting-started.md).
2. [Configurez les autorisations Active Directory locales pour l’écriture différée du mot de passe](active-directory-passwords-writeback.md#active-directory-permissions).
3. [Configurez l’écriture différée du mot de passe](active-directory-passwords-writeback.md#configuring-password-writeback) pour écrire des mots de passe en différé à partir d’Azure AD dans votre répertoire local.
4. [Affectez et vérifiez les licences requises](active-directory-passwords-licensing.md).
5. Si vous souhaitez lancer progressivement la réinitialisation de mot de passe en libre-service, vous pouvez limiter l’accès à un groupe d’utilisateurs afin que vous puissiez piloter un groupe spécifique. Pour ce faire, faites basculer **Réinitialisation du mot de passe en libre-service activée** à **Sélectionné**, puis sélectionnez le groupe de sécurité à activer pour la réinitialisation du mot de passe. 
6. Remplissez les [Données d’authentification](active-directory-passwords-data.md) pour vos utilisateurs telles que leur téléphone de bureau, téléphone mobile et adresse de messagerie.
7. [Personnalisez l’expérience de connexion Azure AD pour inclure la marque de votre société](active-directory-passwords-customize.md).
8. Apprenez à vos utilisateurs à utiliser la réinitialisation du mot de passe libre-service, en leur envoyant les instructions à suivre pour s’inscrire et réinitialiser le mot de passe.
9. Vous pouvez choisir d’appliquer l’inscription à tout moment et demander aux utilisateurs de reconfirmer leurs informations d’authentification après un certain laps de temps.
10. Au fil du temps, passez en revue l’inscription et l’utilisation des utilisateurs en affichant les [rapports fournis par Azure AD](active-directory-passwords-reporting.md).
11. Lorsque vous êtes prêt, activez la réinitialisation du mot de passe pour tous les utilisateurs, définissez **Réinitialisation du mot de passe en libre-service activée** en le faisant basculer en **Tout le monde**. 

   > [!IMPORTANT]
   > Testez la réinitialisation de mot de passe en libre-service avec un utilisateur et non un administrateur, car Microsoft applique des spécifications d’authentification forte pour les comptes Administrateur Azure. Pour plus d’informations sur la stratégie de mot de passe administrateur, consultez notre [article](active-directory-passwords-policy.md#administrator-password-policy-differences).

## <a name="email-based-rollout"></a>Déploiement par courrier électronique

De nombreux clients estiment qu’une campagne par courrier électronique, accompagnée d’instructions simples d’utilisation, constitue le moyen le plus simple pour que les utilisateurs aient recours à la réinitialisation du mot de passe libre-service. [Nous avons créé trois courriers électroniques simples que vous pouvez utiliser comme modèles pour vous aider dans votre lancement.](https://onedrive.live.com/?authkey=%21AD5ZP%2D8RyJ2Cc6M&id=A0B59A91C740AB16%2125063&cid=A0B59A91C740AB16)

* **Bientôt disponible** : modèle de courrier électronique à utiliser dans les semaines ou les jours précédant le lancement pour informer les utilisateurs s’ils ont besoin d’effectuer une action.
* **Disponible dès maintenant** : modèle de courrier électronique à utiliser le jour du lancement pour inciter les utilisateurs à s’inscrire et à confirmer leurs données d’authentification afin de pouvoir utiliser la réinitialisation du mot de passe libre-service lorsqu’ils en auront besoin.
* **Rappel d’inscription** : modèle de courrier électronique à utiliser dans les jours ou les semaines qui suivent le déploiement pour rappeler aux utilisateurs de s’inscrire et de confirmer leurs données d’authentification.

![E-mail][Email]

## <a name="creating-your-own-password-portal"></a>Création de votre propre portail de mot de passe

Nombre de nos clients plus importants choisissent d’héberger une page web et de créer une entrée DNS racine, comme https://passwords.contoso.com. Ils renseignent cette page avec des liens vers les portails de réinitialisation de mot de passe Azure AD, d’inscription à la réinitialisation de mot de passe et de modification de mot de passe, et y indiquent d’autres informations propres à leur organisation. Dans toutes vos communications par courrier électronique ou prospectus, vous pouvez inclure une URL de marque, facile à retenir que les utilisateurs peuvent suivre quand ils doivent utiliser les services.

* Portail de réinitialisation de mot de passe : https://aka.ms/sspr
* Portail d’inscription à la réinitialisation de mot de passe : http://aka.ms/ssprsetup
* Portail de modification de mot de passe : https://account.activedirectory.windowsazure.com/ChangePassword.aspx

Pour votre bénéfice, nous avons créé une page d’exemple que vous pouvez utiliser et personnaliser pour les besoins de votre organisation, et qui est téléchargeable sur [GitHub](https://github.com/ajamess/password-reset-page).

## <a name="using-enforced-registration"></a>Utilisation de l’inscription forcée

Si vous souhaitez que vos utilisateurs s’inscrivent pour la réinitialisation du mot de passe, vous pouvez les forcer à s’inscrire lorsqu’ils se connectent à l’aide d’Azure AD. Vous pouvez activer cette option dans le panneau **Réinitialisation de mot de passe** de votre répertoire en activant l’option **Demander aux utilisateurs de s’inscrire lorsqu’ils se connectent** dans l’onglet **Inscription**.

Les administrateurs peuvent demander aux utilisateurs de se réinscrire après un laps de temps en définissant l’option **Nombre de jours avant que les utilisateurs ne soient invités à reconfirmer leurs informations d’authentification** sur une valeur comprise entre 0 et 730 jours.

Une fois que vous avez activé cette option, lorsque les utilisateurs se connectent, un message les informe que leur administrateur leur a demandé de vérifier leurs informations d’authentification.

## <a name="populate-authentication-data"></a>Renseigner les données d’authentification

Si vous [renseignez les données d’authentification pour vos utilisateurs](active-directory-passwords-data.md), ceux-ci n’ont alors pas besoin de s’inscrire pour la réinitialisation de mot de passe pour pouvoir utiliser la réinitialisation du mot de passe libre-service. Tant que leurs données d’authentification définies sont conformes à la stratégie de réinitialisation de mot de passe que vous avez définie, les utilisateurs sont en mesure de réinitialiser leurs mots de passe.

## <a name="disabling-self-service-password-reset"></a>Désactivation de la réinitialisation de mot de passe en libre-service

Pour désactiver la réinitialisation de mot de passe en libre-service, il vous suffit d’ouvrir votre locataire Azure AD, d’accéder à **Réinitialisation de mot de passe**, **Propriétés**, et de sélectionner **Personne** sous **Réinitialisation de mot de passe en libre-service activée**

## <a name="next-steps"></a>Étapes suivantes

* [Réinitialisez ou modifiez votre mot de passe](active-directory-passwords-update-your-own-password.md).
* [Inscrivez-vous pour la réinitialisation du mot de passe en libre-service](active-directory-passwords-reset-register.md).
* [Vous avez une question relative à la licence ?](active-directory-passwords-licensing.md)
* [Quelles données sont utilisées par la réinitialisation de mot de passe en libre-service et quelles données vous devez renseigner pour vos utilisateurs ?](active-directory-passwords-data.md)
* [Quelles sont les options de stratégie disponibles avec la réinitialisation de mot de passe en libre-service ?](active-directory-passwords-policy.md)
* [Quelle est l’écriture différée de mot de passe et pourquoi dois-je m’y intéresser ?](active-directory-passwords-writeback.md)
* [Comment puis-je générer des rapports sur l’activité dans la réinitialisation de mot de passe en libre-service ?](active-directory-passwords-reporting.md)
* [Quelles sont toutes les options disponibles dans la réinitialisation de mot de passe en libre-service et que signifient-elles ?](active-directory-passwords-how-it-works.md)
* [Je pense qu’il y a une panne quelque part. Comment puis-je résoudre les problèmes de la réinitialisation de mot de passe en libre-service ?](active-directory-passwords-troubleshoot.md)
* [J’ai une question à laquelle je n’ai pas trouvé de réponse ailleurs](active-directory-passwords-faq.md)

[Email]: ./media/active-directory-passwords-best-practices/sspr-emailtemplates.png "Personnalisez ces modèles de messages électroniques en fonction des exigences de votre organisation"