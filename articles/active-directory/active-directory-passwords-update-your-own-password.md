---
title: "Réinitialisation de votre mot de passe Azure AD | Microsoft Docs"
description: "Utilisez la fonction de réinitialisation de mot de passe en libre-service pour récupérer l’accès à votre compte"
services: active-directory
keywords: "Gestion des mots de passe Active Directory, gestion des mots de passe, réinitialisation de mot de passe en libre-service Azure AD, SSPR"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 7ba69b18-317a-4a62-afa3-924c4ea8fb49
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/11/2017
ms.author: joflore
ms.custom: end-user
translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: bb62d6973dff00932e72ddb4b2c344f070537828
ms.lasthandoff: 04/14/2017


---
# <a name="help-i-forgot-my-password"></a>J’ai oublié mon mot de passe

Ne vous affolez pas !

Si les scénarios suivants s’appliquent à vous, nous pouvons vous aider.

* Vous ne savez pas comment accéder à votre compte et avez oublié votre mot de passe.
* Aucun mot de passe ne vous a été affecté et votre administrateur vous a invité à consulter cette page.

## <a name="unlock-my-account"></a>Déverrouillage de mon compte

Si vous consultez cette page pour déverrouiller votre compte, suivez les étapes ci-dessous. Si vous voyez apparaître la mention **Choisir un nouveau mot de passe** à l’étape 6 ci-dessous, cela signifie que vous pouvez déverrouiller votre mot de passe, ou le modifier afin de le déverrouiller.

## <a name="reset-my-password"></a>Réinitialiser mon mot de passe

Pour pouvoir accéder à nouveau à votre compte, procédez comme suit.
1. Depuis une page de connexion professionnelle ou scolaire, cliquez sur le **lien Vous ne pouvez pas accéder à votre compte ?** et sur **Compte professionnel ou scolaire**, ou accédez directement à la [page de réinitialisation du mot de passe](https://passwordreset.microsoftonline.com/).

    ![Vous ne pouvez pas accéder à votre compte ?][Login]

2. Saisissez **l’ID utilisateur** associé à votre compte professionnel ou scolaire, prouvez que vous n’êtes pas un robot en répondant au test CAPTCHA et en saisissant le texte qui vous est présenté, puis cliquez sur **Suivant**.

   > [!NOTE]
   > Si l’administrateur n’a pas activé cette fonctionnalité, le lien « Contactez l’administrateur » s’affiche à cette étape, afin de permettre à l’administrateur de vous aider par e-mail, ou via son portail web.
   >

3. Selon la façon dont l’administrateur a configuré le système, vous verrez apparaître une ou plusieurs des options suivantes :
    * **Envoyer un courrier électronique sur mon adresse de messagerie secondaire** : envoie un e-mail avec un code à 6 chiffres à votre autre adresse e-mail ou à votre adresse e-mail d’authentification (au choix).
    * **Envoyer un SMS à mon téléphone mobile** : envoie un code à 6 chiffres à votre téléphone mobile ou à votre téléphone d’authentification (au choix).
    * **Appeler mon téléphone mobile** : appelle votre téléphone mobile ou votre téléphone d’authentification (au choix). Appuyez sur la touche # pour vérifier l’appel.
    * **Appeler mon téléphone de bureau** : appelle votre téléphone de bureau. Appuyez sur la touche # pour vérifier l’appel.
    * **Répondre à mes questions de sécurité** : affiche vos questions de sécurité préenregistrées pour vous permettre de répondre.
4. Remplissez les champs requis en indiquant les réponses au test et cliquez sur **Suivant**.

    ![Vérifier vos données d’authentification][Verification]

5. Votre administrateur peut avoir besoin d’une étape de vérification supplémentaire ; vous devrez peut-être répéter l’étape 4 en sélectionnant une autre option.
6. Sur la page **Choisir un nouveau mot de passe**, saisissez un nouveau mot de passe répondant aux exigences de l’entreprise et confirmez votre choix, puis cliquez sur **Terminer**.

    ![Modifier votre mot de passe][Change]

7. Lorsque vous voyez apparaître le message **Votre mot de passe a été réinitialisé**, cela signifie que vous pouvez vous connecter avec votre nouveau mot de passe.

    ![Votre mot de passe a été réinitialisé][Complete]

Après avoir utilisé cette méthode pour déverrouiller ou réinitialiser votre mot de passe, vous pouvez recevoir un e-mail signalant que la procédure est terminée, qui peut provenir d’un compte tel que « Microsoft pour le compte de votre organisation ici ». Si vous recevez ce type de message et n’avez pas utilisé la fonction de réinitialisation du mot de passe en libre-service pour récupérer l’accès à votre compte, contactez l’administrateur.

## <a name="change-my-password"></a>Modifier mon mot de passe

Si vous connaissez déjà votre mot de passe et devez le modifier, essayez les étapes qui suivent.

### <a name="change-your-password-from-the-office-365-portal"></a>Modifier votre mot de passe à partir du portail Office 365

1. Cliquez sur votre profil, dans le coin supérieur droit de la fenêtre, puis sélectionnez **Afficher le compte**.
2. **Sécurité et confidentialité**
3. **Mot de passe**
4. Saisissez votre ancien mot de passe, puis indiquez un nouveau mot de passe et confirmez-le.
5. **Envoyer**

### <a name="change-your-password-from-the-azure-access-panel"></a>Modifier votre mot de passe à partir du volet d’accès Azure

1. Connectez-vous au [portail d’accès Azure](https://myapps.microsoft.com/) en utilisant votre mot de passe existant.
2. Cliquez sur votre profil, dans le coin supérieur droit de la fenêtre, puis sélectionnez **Profil**.
3. **Modifier le mot de passe**
4. Saisissez votre ancien mot de passe, puis indiquez un nouveau mot de passe et confirmez-le.
5. **Envoyer**

## <a name="next-steps"></a>Étapes suivantes

* [Page relative à la procédure d’inscription pour utiliser la réinitialisation du mot de passe en libre-service](active-directory-passwords-reset-register.md)
* [Page relative à l’inscription à la réinitialisation du mot de passe](http://aka.ms/ssprsetup)
* [Portail de réinitialisation du mot de passe](https://passwordreset.microsoftonline.com/)

[Login]: ./media/active-directory-passwords-update-your-own-password/reset-1-login.png "Page de connexion Vous ne pouvez pas accéder à votre compte ?"
[Verification]: ./media/active-directory-passwords-update-your-own-password/reset-2-verification.png "Vérifier vos données d’authentification"
[Change]: ./media/active-directory-passwords-update-your-own-password/reset-3-change.png "Modifier votre mot de passe"
[Complete]: ./media/active-directory-passwords-update-your-own-password/reset-4-complete.png "Votre mot de passe a été réinitialisé"

