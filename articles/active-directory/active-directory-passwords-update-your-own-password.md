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
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: c95f52f028cdfaed35821275d816a24035ff02fb
ms.lasthandoff: 04/26/2017


---
# <a name="help-i-forgot-my-azure-ad-password"></a>J’ai oublié mon mot de passe Azure AD

Si vous avez oublié votre mot de passe, si vous ne l’avez jamais reçu du personnel informatique,si l’accès à votre compte a été bloqué, ou si vous souhaitez le modifier, nous pouvons vous aider.

## <a name="reset-or-unlock-my-password-for-a-work-or-school-account"></a>Réinitialiser ou déverrouiller mon mot de passe d’un compte professionnel ou scolaire

Pour accéder à votre compte professionnel ou scolaire, suivez les étapes suivantes afin d’accéder au service de réinitialisation de mot de passe en libre-service Azure AD, que nous appelons également SSPR.

1. Depuis n’importe quelle page de connexion professionnelle ou scolaire, cliquez sur le lien**Vous ne pouvez pas accéder à votre compte ?** puis cliquez sur **Compte professionnel ou scolaire**, ou accédez directement à la [page de réinitialisation du mot de passe](https://passwordreset.microsoftonline.com/).

   > [!NOTE]
   > Si vous tentez de récupérer un compte personnel comme hotmail.com ou outlook.com, essayez l’option [suggestions trouvées dans cet article](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
   >
    ![Vous ne pouvez pas accéder à votre compte ?][Login]

2. Entrez votre **identifiant utilisateur** professionnel ou scolaire, prouvez que vous n’êtes pas un robot en notant les caractères visibles à l’écran, puis cliquez sur **Suivant**.

   > [!NOTE]
   > Si le personnel informatique n’a pas activé cette fonctionnalité, un lien « Contactez votre administrateur » s’affiche afin que le personnel puisse vous aider, par e-mail ou via leur propre portail web.
   >

3. Selon la façon dont votre personnel informatique a configuré la fonction SSPR, vous verrez apparaître une ou plusieurs des options ci-après. Vous ou votre personnel informatique avez déjà rempli certaines de ces informations avant d’utiliser les informations renseignées [ici](active-directory-passwords-reset-register.md).
    * **Envoyer un e-mail sur mon adresse de messagerie de secours**
    * **Envoyer un SMS sur mon téléphone portable**
    * **Effectuer un appel sur mon téléphone portable**
    * **Effectuer un appel sur mon téléphone professionnel**
    * **Répondre à ma question de sécurité**

    Choisissez une option, répondez correctement et cliquez sur **Suivant**.

    ![Vérifier vos données d’authentification][Verification]

4. Votre personnel informatique pourra avoir besoin de plus d’éléments de vérification, vous devrez sans doute répéter l’étape 3 en sélectionnant une option différente.
5. Sur la page **Choisir un nouveau mot de passe**, entrez un nouveau mot de passe, confirmez votre choix, puis cliquez sur **Terminer**. Nous vous conseillons de choisir un mot de passe comprenant 8 à 16 caractères, et incluant des majuscules, des minuscules, des nombres et des caractères spéciaux.

   > [!NOTE]
   > Si vous souhaitez déverrouiller votre compte, choisissez à ce stade uniquement l’option déverrouiller, ou modifier votre mot de passe et déverrouiller.
   >

6. Lorsque vous voyez apparaître le message **Votre mot de passe a été réinitialisé**, cela signifie que vous pouvez vous connecter avec votre nouveau mot de passe.

    ![Votre mot de passe a été réinitialisé][Complete]

Vous devriez maintenant pouvoir accéder à votre compte. Le cas échéant, contactez le personnel informatique de votre organisation pour plus d’aide.

Vous pouvez recevoir un e-mail de confirmation provenant d’un compte comme « Microsoft de la part de \<votre organisation>\". Si vous recevez ce type de message et que vous n’avez pas utilisé la fonction de réinitialisation du mot de passe en libre-service pour récupérer l’accès à votre compte, contactez le personnel informatique de votre entreprise.

## <a name="change-my-password"></a>Modifier mon mot de passe

Si vous connaissez déjà votre mot de passe et souhaitez le modifier, suivez les étapes suivantes afin de modifier votre mot de passe.

### <a name="change-your-password-from-the-office-365-portal"></a>Modifier votre mot de passe à partir du portail Office 365

Utilisez cette méthode si vous accédez généralement à vos applications à l’aide du portail Office

1. Connectez-vous à votre [compte Office 365](https://www.office.com)
2. Sur votre profil, cliquez dans le coin supérieur droit de la fenêtre, puis cliquez sur **Afficher le compte**
3. Cliquez sur **Sécurité et confidentialité** > **Mot de passe**
4. Saisissez votre ancien mot de passe, indiquez un nouveau mot de passe et confirmez-le, puis cliquez sur **Soumettre**

### <a name="change-your-password-from-the-azure-access-panel"></a>Modifier votre mot de passe à partir du volet d’accès Azure

Utilisez cette méthode si vous accédez généralement à vos applications à partir du portail d’accès Azure

1. Connectez-vous au [portail d’accès Azure](https://myapps.microsoft.com/) en utilisant votre mot de passe existant.
2. Sur votre profil, cliquez dans le coin supérieur droit de la fenêtre, puis cliquez sur **Profil**
3. Cliquez sur **Modifier le mot de passe**
4. Saisissez votre ancien mot de passe, indiquez un nouveau mot de passe et confirmez-le, puis cliquez sur **Soumettre**

## <a name="next-steps"></a>Étapes suivantes

* [Page relative à la procédure d’inscription pour utiliser la réinitialisation du mot de passe en libre-service](active-directory-passwords-reset-register.md)
* [Page relative à l’inscription à la réinitialisation du mot de passe](http://aka.ms/ssprsetup)
* [Portail de réinitialisation du mot de passe](https://passwordreset.microsoftonline.com/)
* [Connexion impossible à votre compte Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Login]: ./media/active-directory-passwords-update-your-own-password/reset-1-login.png "Page de connexion Vous ne pouvez pas accéder à votre compte ?"
[Verification]: ./media/active-directory-passwords-update-your-own-password/reset-2-verification.png "Vérifier vos données d’authentification"
[Change]: ./media/active-directory-passwords-update-your-own-password/reset-3-change.png "Modifier votre mot de passe"
[Complete]: ./media/active-directory-passwords-update-your-own-password/reset-4-complete.png "Votre mot de passe a été réinitialisé"

