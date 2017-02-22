---
title: "Aide et support pour l’application Microsoft Authenticator | Microsoft Docs"
description: "Fournit une liste de questions fréquentes et les réponses relatives à l’application Microsoft Authentication et à l’authentification multifacteur Azure."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: pblachar, librown
ms.assetid: f04d5bce-e99e-4f75-82d1-ef6369be3402
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 45358e89561ef2265ca5d8ae522b823c4629b3fc
ms.openlocfilehash: f869e456ef40d04f7dd085d4ce7c3d654cdf2091


---
# <a name="microsoft-authenticator-app-faq"></a>Forum aux questions sur l’application Microsoft Authenticator
L’application Microsoft Authenticator a remplacé l’application Azure Authenticator et devient l’application recommandée lorsque vous utilisez l’authentification multifacteur Azure. Cette application est disponible pour Windows Phone, Android et iOS.

## <a name="frequently-asked-questions"></a>Forum Aux Questions
### <a name="what-happened-to-the-azure-authenticator-multi-factor-auth-and-microsoft-account-apps"></a>Qu’est-il arrivé aux applications de compte Microsoft, Azure Authenticator et Multi-Factor Auth ?
L’application Microsoft Authenticator remplace chacune de ces applications. Azure Authenticator a été mis à niveau vers Microsoft Authenticator. Si vous utilisez l’application Multi-Factor Auth ou le compte Microsoft, installez Microsoft Authenticator et ajoutez de nouveau vos comptes. Prenez soin de finir d’ajouter vos comptes à la nouvelle application avant de supprimer les anciens.

L’application Microsoft Authenticator est disponible pour [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) et [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

### <a name="im-already-using-the-microsoft-authenticator-application-for-verification-codes-how-do-i-switch-to-one-click-push-notifications"></a>J’utilise déjà l’application Microsoft Authenticator pour les codes de vérification. Comment puis-je activer les notifications push en un clic ?
L’approbation d’une connexion via notification push est disponible uniquement pour les comptes Microsoft personnels, professionnels ou scolaires. Les comptes tiers tels que Google ou Facebook ne bénéficient pas de cette fonctionnalité. Si vous disposez d’un compte Microsoft professionnel ou scolaire, votre organisation peut choisir de désactiver cette option.

Si vous utilisez un compte Microsoft pour votre compte personnel et souhaitez activer les notifications push, vous devez ajouter à nouveau votre compte. Réinscrivez l’appareil avec votre compte et configurez les notifications push.  

Si vous utilisez Microsoft Authenticator pour votre compte professionnel ou scolaire, votre organisation décide s’il faut autoriser ou non les notifications en un clic.

### <a name="do-one-click-push-notifications-work-for-non-microsoft-accounts"></a>Les notifications push en un clic fonctionnent-elles pour les comptes non Microsoft ?
Non, les notifications push fonctionnent uniquement avec les comptes Microsoft et Azure Active Directory. Si votre entreprise ou école utilise des comptes Azure AD, vous pouvez désactiver cette fonctionnalité.  

### <a name="i-restored-my-device-from-a-backup-and-my-account-codes-are-missing-or-not-working-what-happened"></a>J’ai restauré mon appareil à partir d’une sauvegarde, et les codes de mon compte sont manquants ou ne fonctionnent pas. Que s’est-il passé ?
Pour des raisons de sécurité, nous ne restaurons pas les comptes à partir de sauvegardes d’application. Si vous restaurez l’application iOS à partir d’une sauvegarde, vos comptes restent affichés, mais ils ne permettent pas de recevoir des vérifications de connexion ou de générer des codes de sécurité. Après la restauration de l’application, supprimez vos comptes et ajoutez-les à nouveau.

### <a name="i-got-a-new-device-how-do-i-remove-the-microsoft-authenticator-app-from-my-old-device-and-move-to-the-new-one"></a>Je possède un nouvel appareil. Comment supprimer l’application Microsoft Authenticator sur mon ancien appareil et le transférer vers un autre ?
L’ajout de l’application Microsoft Authenticator à un nouvel appareil n’a pas pour effet de la supprimer automatiquement des autres appareils. Pour gérer les périphériques configurés pour votre compte, rendez-vous sur le site web qui vous permet de gérer la vérification en deux étapes, et choisissez de supprimer les anciennes applications.

Pour les comptes Microsoft personnels, ce site web correspond à votre page [sécurité du compte](https://account.microsoft.com/security). Pour les comptes Microsoft professionnels ou scolaires, ce site web peut être [MyApps](https://myapps.microsoft.com) ou un portail personnalisé que votre organisation a configuré.

### <a name="how-do-i-remove-an-account-from-the-app"></a>Comment supprimer un compte de l’application ?
* iOS : dans l’écran principal, effectuez un balayage vers la gauche sur une vignette de compte. Sélectionnez **Supprimer**.
* Windows Phone : dans l’écran principal, sélectionnez le bouton de menu, puis **Modifier les comptes**. Cliquez sur le symbole **X** en regard du nom de compte.
* Android : dans l’écran principal, sélectionnez le bouton de menu, puis **Modifier les comptes**. Cliquez sur le symbole **X** en regard du nom de compte.

Si vous utilisez un appareil Android qui est enregistré auprès de votre organisation, vous devrez peut-être effectuer une étape supplémentaire pour supprimer votre compte. Sur ces appareils, l’application Microsoft Authenticator est automatiquement enregistrée en tant qu’administrateur de l’appareil. Si vous souhaitez désinstaller complètement l’application, vous devez tout d’abord annuler l’inscription de l’application dans les paramètres d’application.

### <a name="why-does-the-app-request-so-many-permissions"></a>Pourquoi l’application demande-t-elle autant d’autorisations ?
Voici une liste complète des autorisations que nous vous demandons, et qui décrit la manière dont elles sont utilisées dans l’application :

* **Appareil photo** : nous utilisons votre appareil photo pour analyser les codes QR lorsque vous ajoutez un compte professionnel, scolaire ou tiers.
* **Contacts and phone (Contacts et téléphone)** : lorsque vous vous connectez à votre compte Microsoft personnel, nous essayons de simplifier le processus en recherchant les comptes existants que vous utilisez sur votre téléphone.
* **SMS** : lorsque vous vous connectez à votre compte Microsoft personnel pour la première fois, nous devons nous assurer que votre numéro de téléphone correspond à celui de nos archives. Nous envoyons un SMS sur le téléphone que vous avez utilisé pour télécharger l’application. Le message contient un code de vérification composé de 6 à 8 chiffres. Au lieu de vous demander de rechercher ce code et de le saisir dans l’application, nous le faisons pour vous dans le message texte.
* **Draw over other apps (Afficher sur d’autres applications)** : lorsque vous recevez une notification visant à vérifier votre identité, nous affichons cette notification sur une autre application potentiellement en cours d’exécution.
* **Receive data from the internet (Recevoir des données à partir d’Internet)** : cette autorisation est requise pour l’envoi de notifications.
* **Prevent phone from sleeping (Empêcher la mise en veille du téléphone)** : si vous enregistrez votre appareil auprès de votre organisation, elle peut modifier cette stratégie sur votre téléphone.
* **Control vibration (Contrôler les vibrations)** : vous avez la possibilité de choisir si vous souhaitez que votre téléphone vibre chaque fois que vous recevez une notification visant à vérifier votre identité.
* **Use fingerprint hardware (Utiliser du matériel de détection de l’empreinte digitale** : certains comptes professionnels et scolaires demandent un code PIN supplémentaire chaque fois que vous vérifiez votre identité. Pour faciliter le processus, nous vous autorisons à utiliser vos empreintes digitales au lieu d’avoir à saisir votre code PIN.
* **Afficher les connexions réseau** : lorsque vous ajoutez un compte Microsoft, l’application exige une connexion réseau/Internet.
* **Read the contents of your storage (Lire le contenu de votre stockage)** : cette autorisation est utilisée uniquement lorsque vous signalez un problème technique via les paramètres de l’application. Certaines informations sont collectées à partir de votre espace de stockage afin de diagnostiquer le problème.
* **Full network access (Accès complet au réseau)** : cette autorisation est requise pour l’envoi de notifications visant à vérifier votre identité.
* **Exécuter au démarrage** : si vous redémarrez votre téléphone, cette autorisation vous permet de continuer de recevoir des notifications visant à vérifier votre identité.

### <a name="why-does-the-microsoft-authenticator-app-allow-you-to-approve-a-request-without-unlocking-the-device"></a>Pourquoi l’application Microsoft Authenticator permet-elle d’approuver une demande sans déverrouiller l’appareil ?

C’est normal. La vérification en deux étapes fait appel à deux éléments : un que vous connaissez, et un autre que vous possédez. L’élément que vous connaissez est le mot de passe. L’élément que vous possédez est votre téléphone (configuré avec l’application Microsoft Authenticator et enregistré comme une preuve MFA).  Par conséquent, le fait d’être en possession du téléphone et d’approuver la demande remplit les critères du second facteur d’authentification. 

## <a name="next-steps"></a>Étapes suivantes

### <a name="contact-us"></a>Nous contacter
Si vous n’avez pas trouvé la réponse à votre question ici, rendez-vous sur le [forum de l’application Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp) pour publier votre question et obtenir de l’aide de la communauté, laissez un commentaire sur cette page ou [contactez le support technique](https://support.microsoft.com/contactus) et nous tâcherons de vous répondre dès que possible.


### <a name="related-topics"></a>Rubriques connexes
* [À propos de la vérification en deux étapes](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) pour les comptes Microsoft
* Vous rencontrez des [difficultés avec la vérification en deux étapes](multi-factor-authentication-end-user-troubleshoot.md) pour votre compte professionnel ou scolaire ?




<!--HONumber=Jan17_HO3-->


