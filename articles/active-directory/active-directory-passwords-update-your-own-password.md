---
title: "Comment mettre à jour votre mot de passe avec Azure Active Directory | Microsoft Docs"
description: "Découvrez comment demander une réinitialisation du mot de passe, modifier votre mot de passe et réinitialiser votre mot de passe si vous l’avez oublié."
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: 7ba69b18-317a-4a62-afa3-924c4ea8fb49
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2017
ms.author: joflore
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: f0a27b909c4f41300e877a70760db71ca0d36270
ms.lasthandoff: 03/15/2017


---
# <a name="how-to-update-your-own-password"></a>Comment mettre à jour votre mot de passe
Si vous ne savez pas comment gérer votre mot de passe de compte professionnel ou scolaire, vous êtes au bon endroit ! Lisez la suite pour apprendre à effectuer les étapes courantes, comme modifier un mot de passe, réinitialiser un mot de passe ou s'inscrire pour une réinitialisation du mot de passe.

* [**Ne perdez pas l’accès à votre compte !**](#dont-lose-access-to-your-account)
* [**Comment modifier votre mot de passe à partir d’Office 365**](#how-to-change-your-password-from-o365)
* [**Comment modifier votre mot de passe à partir du panneau d'accès**](#how-to-change-your-password-from-the-access-panel)
* [**Comment réinitialiser votre mot de passe**](#how-to-reset-your-password)
* [**Comment déverrouiller votre compte**](#how-to-unlock-your-account)
* [**Common problems and their solutions**](#common-problems-and-their-solutions)

## <a name="dont-lose-access-to-your-account"></a>Ne perdez pas l’accès à votre compte !
> [!IMPORTANT]
> **Pourquoi est-ce que je vois cela ?** Si vous avez suivi un lien jusqu’ici, vous voyez probablement cela parce que votre administrateur exige que vous demandiez une réinitialisation du mot de passe pour pouvoir accéder à votre application. Il se peut que vous soyez invité à fournir votre numéro de téléphone ou votre adresse de messagerie, ou encore à définir des questions de sécurité.  Ne vous inquiétez pas : nous n’utilisons ces informations que pour renforcer la sécurité de votre compte. Les étapes présentées ici devraient vous aider à atteindre votre objectif.
>
>

Le moyen le plus rapide de vous inscrire pour demander une réinitialisation du mot de passe est de vous rendre sur la page [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup).  

1. Accédez à la page [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup).
2. Entrez votre nom d'utilisateur et votre mot de passe.
3. Choisissez une option pour vous inscrire en cliquant sur **Configurer maintenant**. Dans ce cas, je vous montre l’inscription de mon **téléphone d’authentification**. Il est possible que votre administrateur vous inscrive plusieurs options. 

   ![][101]
4. Sélectionnez votre pays dans la liste déroulante et entrez votre **numéro de téléphone complet, indicatif inclus**, puis choisissez l’option de **SMS** ou d’**appel** pour recevoir votre code. Dans cet exemple j’ai choisi l’option SMS.

   ![][102]
5. Une fois que le code arrive, entrez-le dans la zone d'entrée, puis cliquez sur « Vérifier ». 

   ![][103]
6. Lorsque vous voyez **Terminer**, c’est fini ! Vous pouvez maintenant utiliser ce que vous avez enregistré pour réinitialiser votre mot de passe à tout moment en vous rendant sur la page [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com).

   ![][104]

   > [!IMPORTANT]
   > Si votre administrateur vous permet de vous inscrire à plusieurs options, nous vous recommandons vivement de vous inscrire également à une option de sauvegarde, pour le cas où vous perdriez votre téléphone ou n'auriez plus accès à votre adresse de messagerie.
   >
   >

## <a name="how-to-change-your-password-from-o365"></a>Comment modifier votre mot de passe à partir d'Office&365;
Suivez les étapes ci-dessous pour modifier le mot de passe de votre compte professionnel ou scolaire dans Office 365. Si vous avez oublié votre mot de passe et souhaitez le réinitialiser, suivez les étapes [ci-dessous](#how-to-reset-your-password).

1. Connectez-vous à Office 365 avec votre compte professionnel ou scolaire.
2. Accédez à **Paramètres** > **Office 365** > **Mot de passe** > **Modifier le mot de passe**.
3. Tapez votre ancien mot de passe, puis tapez un nouveau mot de passe et confirmez-le.
4. Cliquez sur **Save**.

Vous pouvez en apprendre plus sur le [Centre de documentation Office 365](https://support.office.com/article/Change-my-password-in-Office-365-for-business-d1efbaee-63a7-4c08-ab1d-71bf932bbb5d).

## <a name="how-to-change-your-password-from-the-access-panel"></a>Comment modifier votre mot de passe à partir du panneau d'accès
Suivez les étapes ci-dessous pour modifier le mot de passe de votre compte professionnel ou scolaire dans [Panneau d'accès](https://myapps.microsoft.com). Si vous avez oublié votre mot de passe et souhaitez le réinitialiser, suivez les étapes [ci-dessous](#how-to-reset-your-password).

1. Connectez-vous à https://myapps.microsoft.com avec votre compte professionnel ou scolaire.
2. Cliquez sur l’onglet **Profil**.
3. Cliquez sur la mosaïque **Modifier mon mot de passe** sur le côté droit de l'écran.
4. Tapez votre ancien mot de passe, puis tapez un nouveau mot de passe et confirmez-le.
5. Cliquez sur **Envoyer**.

   Vous rencontrez un problème lors de la modification de votre mot de passe ?  Lisez [Problèmes courants et leurs solutions](#common-problems-and-their-solutions).

## <a name="how-to-reset-your-password"></a>Comment réinitialiser votre mot de passe
Suivez les étapes ci-dessous pour réinitialiser le mot de passe de votre compte professionnel ou scolaire depuis n'importe quel écran de connexion professionnel ou scolaire.

> [!IMPORTANT]
> Cette fonctionnalité est disponible uniquement si votre administrateur l'a activée. Si elle est désactivée, vous verrez un message indiquant que votre compte n’est pas activé pour cette fonctionnalité.  Vous pouvez utiliser le lien « Contacter votre administrateur » dans ce cas, pour demander à votre administrateur de déverrouiller votre compte.
>
> Si votre administrateur vous a autorisé à utiliser cette fonctionnalité, vous devez vous connecter avant de pouvoir l’utiliser. Vous pouvez le faire ici : http://aka.ms/ssprsetup.
>
>

1. Sur une page de connexion professionnelle ou scolaire, cliquez sur le lien « Vous ne pouvez pas accéder à votre compte ? » , ou accéder directement à https://passwordreset.microsoftonline.com.

   ![][110]
2. Sur la page « Qui êtes-vous? », entrez votre identifiant de compte professionnel ou scolaire et prouvez que vous n'êtes pas un robot en passant le test CAPTCHA.

   ![][111]
3. Cliquez sur le bouton « Suivant ».
4. Choisissez une option pour réinitialiser votre mot de passe. Selon la façon dont votre administrateur a configuré le système, vous pouvez voir une ou plusieurs des options suivantes :

   * **Envoyer un courrier électronique sur mon adresse de messagerie secondaire** - envoie un message électronique avec un code à 6 chiffres à votre **autre adresse e-mail** ou à votre **adresse e-mail d’authentification** (au choix).
   * **Envoyer un SMS à mon téléphone mobile** - envoie un code à 6 chiffres à votre **téléphone mobile** ou à votre **adresse e-mail d’authentification** (au choix).
   * **Appeler mon téléphone mobile** - appelle votre **téléphone mobile** ou votre **téléphone d’authentification** (au choix) - appuyez sur la touche *#* pour vérifier l’appel.
   * **Appeler mon téléphone de bureau** - appelle votre **téléphone de bureau** - appuyez sur la touche *#* pour vérifier l’appel.
   * **Répondre à mes questions de sécurité** - affiche vos questions de sécurité préenregistrées pour vous permettre de répondre.

   ![][109]
5. Nous allons utiliser l'option « Envoyer un SMS à mon téléphone mobile » à titre d'exemple. Si vous utilisez une option basée sur un numéro de téléphone, vous devez vérifier votre numéro de téléphone avant que nous envoyions un SMS. Entrez votre numéro de téléphone complet, puis cliquez sur **SMS** pour vérifier qu’il est correct et envoyez un SMS.

6. Lorsque vous recevez le SMS, veillez à utiliser le code de vérification dans le corps du message, pas le numéro à partir duquel le code a été envoyé. La réception du SMS peut prendre plusieurs minutes.

7. Saisissez maintenant le code que vous venez de recevoir sur votre téléphone dans la zone d’entrée sur la page, puis sélectionnez **Suivant**.

8. Votre administrateur peut avoir besoin d’une étape de vérification supplémentaire. Dans ce cas, répétez l’étape 4 en sélectionnant une autre option.
9. Dans l'écran « Choisir un nouveau mot de passe », sélectionnez un nouveau mot de passe et confirmez votre choix, puis cliquez sur **Terminer**.

   ![][107]

10. Une fois que votre mot de passe est accepté, vous pouvez vous connecter avec le nouveau mot de passe.

    ![][108]

Vous rencontrez un problème lors de la réinitialisation de votre mot de passe ? Lisez [Problèmes courants et leurs solutions](#common-problems-and-their-solutions).

## <a name="how-to-unlock-your-account"></a>Comment déverrouiller votre compte
Suivez les étapes ci-dessous pour déverrouiller votre compte local depuis n'importe quel écran de connexion professionnel ou scolaire. 

> [!NOTE]
> Vous seul pourrez déverrouiller votre compte s’il a été verrouillé localement.

> [!IMPORTANT]
> Cette fonctionnalité est disponible uniquement si votre administrateur l'a activée. Si elle est désactivée, vous verrez un message indiquant que votre compte n'est pas activé pour cette fonctionnalité. Vous pouvez utiliser le lien « Contacter votre administrateur » dans ce cas, pour demander à votre administrateur de déverrouiller votre compte.
>
> Si votre administrateur vous a autorisé à utiliser cette fonctionnalité, vous devez d’abord vous avant de pouvoir l’utiliser. Vous pouvez le faire ici : http://aka.ms/ssprsetup.
>
>

1. Sur une page de connexion professionnelle ou scolaire, cliquez sur le lien « Vous ne pouvez pas accéder à votre compte ? » , ou accéder directement à https://passwordreset.microsoftonline.com.

   ![][110]
2. Sur la page « Qui êtes-vous? », entrez votre identifiant de compte professionnel ou scolaire et prouvez que vous n'êtes pas un robot en passant le test CAPTCHA.

   ![][111]
3. Cliquez sur le bouton « Suivant ».
4. Choisissez une option pour déverrouiller votre compte. Selon la façon dont votre administrateur a configuré le système, vous pouvez voir une ou plusieurs des options suivantes :

   * **Envoyer un courrier électronique sur mon adresse de messagerie secondaire** - envoie un message électronique avec un code à 6 chiffres à votre **autre adresse e-mail** ou à votre **adresse e-mail d’authentification** (au choix).
   * **Envoyer un SMS à mon téléphone mobile** - envoie un code à 6 chiffres à votre **téléphone mobile** ou à votre **adresse e-mail d’authentification** (au choix).
   * **Appeler mon téléphone mobile** - appelle votre **téléphone mobile** ou votre **téléphone d’authentification** (au choix) - appuyez sur la touche *#* pour vérifier l’appel.
   * **Appeler mon téléphone de bureau** - appelle votre **téléphone de bureau** - appuyez sur la touche *#* pour vérifier l’appel.
   * **Répondre à mes questions de sécurité** - affiche vos questions de sécurité préenregistrées pour vous permettre de répondre.

   ![][112]
5. À titre d’exemple, nous allons utiliser l’option « Répondre à mes questions de sécurité ». Fournissez les réponses à vos questions de sécurité, puis sélectionnez **Suivant** pour vérifier votre identité.

6. Votre administrateur peut avoir besoin d’une étape de vérification supplémentaire. Dans ce cas, vous devez répéter l’étape 4 en sélectionnant une autre option.
7. Une fois que vous voyez la page de confirmation, vous êtes prêt ! Votre compte local a été déverrouillé et vous pouvez maintenant vous connecter encore une fois.

   ![][113]

   > [!IMPORTANT]
   > Veillez à mettre à jour le mot de passe sur tous vos appareils : souvent, une application non autorisée avec un mot de passe obsolète (comme votre client de messagerie de téléphone) peut être la raison pour laquelle votre compte a été verrouillé en premier lieu.
   >
   >

Rencontrez un problème pour déverrouiller votre compte ? Lisez [Problèmes courants et leurs solutions](#common-problems-and-their-solutions).

## <a name="common-problems-and-their-solutions"></a>Problèmes courants et leurs solutions
Voici quelques cas d'erreur courants et leurs solutions :

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Cas d’erreur</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Quelle erreur voyez-vous ?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Solution</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>J'obtiens une page « Veuillez contacter votre administrateur » après avoir entré mon identifiant utilisateur</p>
            </td>
            <td>
              <p>Veuillez contacter votre administrateur <br><br>Nous avons détecté que votre mot de passe de compte d'utilisateur n'est pas géré par Microsoft. Par conséquent, nous ne pouvons pas réinitialiser automatiquement votre mot de passe. <br><br>Vous devez contacter votre administrateur ou votre équipe de support technique pour obtenir de l'aide. </p>
            </td>
            <td>
              <p>Ce message s’affiche parce que votre administrateur gère votre mot de passe dans votre environnement local et ne vous autorise pas à réinitialiser votre mot de passe à partir du lien <b>Votre compte n’est pas accessible</b>. <br><br> Pour réinitialiser votre mot de passe, contactez directement votre administrateur pour obtenir de l'aide et informez-le que vous voulez réinitialiser votre mot de passe à partir d'Office 365 pour qu'il puisse activer cette fonctionnalité pour vous.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>J'obtiens une erreur « Votre compte n'est pas activé pour la réinitialisation de mot de passe » après avoir entré mon identifiant utilisateur.</p>
            </td>
            <td>
              <p>Votre compte n’est pas activé pour la réinitialisation du mot de passe<br><br>Désolé, votre administrateur n’a pas configuré votre compte pour ce service.<br><br> Si vous le souhaitez, nous pouvons contacter un administrateur de votre organisation pour réinitialiser le mot de passe à votre place.</p>
            </td>
            <td>
              <p>Vous voyez ce message parce que votre administrateur n’a pas activé la réinitialisation de mot de passe pour votre organisation à partir du lien <b>Votre compte n’est pas accessible</b> ou ne vous a pas octroyé de licence vous permettant d’utiliser la fonctionnalité. <br><br> Pour réinitialiser votre mot de passe, cliquez sur le lien <b>Contacter un administrateur</b> afin d’envoyer un e-mail à l’administrateur de votre société : informez-le que vous voulez réinitialiser votre mot de passe à partir d’Office 365 pour qu’il puisse activer cette fonctionnalité pour vous.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>J'obtiens une erreur « Impossible de vérifier votre compte » après avoir entré mon identifiant utilisateur.</p>
            </td>
            <td>
              <p>Impossible de vérifier votre compte<br><br>Si vous le souhaitez, nous pouvons contacter un administrateur de votre organisation pour réinitialiser le mot de passe à votre place. </p>
            </td>
            <td>
              <p>Vous voyez ce message parce que vous êtes autorisé à réinitialiser le mot de passe, mais que vous ne vous êtes pas inscrit pour utiliser le service.  Pour demander une réinitialisation du mot de passe, accédez à http://aka.ms/ssprsetup une fois que vous avez à nouveau accès à votre compte. <br><br> Pour réinitialiser votre mot de passe, cliquez sur le lien <b>Contacter un administrateur</b> pour envoyer un e-mail à l’administrateur de votre société.</p>
            </td>
          </tr>
        </tbody></table>

## <a name="next-steps"></a>Étapes suivantes
Pour toute autre question sur SSPR (Self Service Password Rest), contactez votre administrateur ou suivez les liens ci-dessous.

* [Besoin d’enregistrer vos informations SSPR ?](http://aka.ms/ssprsetup)
* [Vous ne pouvez pas accéder à votre compte ?](https://passwordreset.microsoftonline.com)
* [Informations de réinitialisation du mot de passe Office 365](https://support.office.com/en-us/article/Reset-user-passwords-in-Office-365-3254c031-04c9-44f1-8fda-2563847a6b31?ui=en-US&rs=en-US&ad=US)
* [Volet d’accès](https://myapps.microsoft.com)

[101]: ./media/active-directory-passwords-update-your-own-password/password-1-dont-lose-access.png "password-1-dont-lose-access.png"
[102]: ./media/active-directory-passwords-update-your-own-password/password-2-verification-response.png "password-2-verification-response.png"
[103]: ./media/active-directory-passwords-update-your-own-password/password-2-verification-text.png "password-2-verification-text.png"
[104]: ./media/active-directory-passwords-update-your-own-password/password-3-registration-complete.png "password-3-registration-complete.png"
[105]: ./media/active-directory-passwords-update-your-own-password/password-4-reset-cant-access.png "password-4-reset-cant-access.png"
[106]: ./media/active-directory-passwords-update-your-own-password/password-4-reset-captcha.png "password-4-reset-captcha.png"
[107]: ./media/active-directory-passwords-update-your-own-password/password-4-reset-change.png "password-4-reset-change.png"
[108]: ./media/active-directory-passwords-update-your-own-password/password-4-reset-finished.png "password-4-reset-finished.png"
[109]: ./media/active-directory-passwords-update-your-own-password/password-4-reset-verification.png "password-4-reset-verification.png"
[110]: ./media/active-directory-passwords-update-your-own-password/password-5-unlock-cant-access.png "password-5-unlock-cant-access.png"
[111]: ./media/active-directory-passwords-update-your-own-password/password-5-unlock-captcha.png "password-5-unlock-captcha.png"
[112]: ./media/active-directory-passwords-update-your-own-password/password-5-unlock-verification.png "password-5-unlock-verification.png"
[113]: ./media/active-directory-passwords-update-your-own-password/password-5-unlock-finished.png "password-5-unlock-finished.png"

