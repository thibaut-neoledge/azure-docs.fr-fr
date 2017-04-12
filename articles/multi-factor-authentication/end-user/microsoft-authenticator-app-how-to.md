---
title: "Application Microsoft Authenticator pour les téléphones mobiles | Microsoft Docs"
description: "Découvrez comment effectuer une mise à niveau vers la dernière version d’Azure Authenticator."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: curtland
ms.assetid: 3065a1ee-f253-41f0-a68d-2bd84af5ffba
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2017
ms.author: kgremban
ms.custom: H1Hack27Feb2017, end-user
translationtype: Human Translation
ms.sourcegitcommit: 6872b216ab2bfd5823b65b1bb70d503c7628ee13
ms.openlocfilehash: 26d9f04f9d3019ab5fcad48ef1e9821a4dd6a724
ms.lasthandoff: 03/01/2017

---
# <a name="get-started-with-the-microsoft-authenticator-app"></a>Prise en main de l’application Microsoft Authenticator
L’application Microsoft Authenticator fournit un niveau supplémentaire de sécurité dans votre compte Azure (p. ex. bsimon@contoso.onmicrosoft.com), votre compte professionnel local (p. ex. bsimon@contoso.com), ou votre compte Microsoft (p. ex. bsimon@outlook.com).

L’application fonctionne de l’une des deux façons suivantes :

* **Notification**. L’application peut aider à empêcher tout accès non autorisé aux comptes et à arrêter les transactions frauduleuses en envoyant une notification à votre smartphone ou tablette. Affichez simplement la notification et si elle est légitime, sélectionnez **Vérifier**. Sinon, vous pouvez sélectionner **Refuser**. Pour plus d’informations sur les notifications de refus, consultez la rubrique Utilisation de la fonctionnalité de refus et signalement d’une fraude pour Multi-Factor Authentication.
* **Mot de passe avec code de vérification**. L’application peut être utilisée comme jeton logiciel pour générer un code de vérification OAuth. Après avoir entré votre nom d’utilisateur et votre mot de passe, vous entrez le code fourni par l’application dans l’écran de connexion. Le code de vérification fournit un deuxième formulaire d’authentification.

L’application Microsoft Authenticator remplace l’application Azure Authenticator.  L’application Azure Authenticator continue de fonctionner, mais cet article peut vous aider si vous décidez d’adopter la nouvelle application Azure Authenticator.  

## <a name="install-the-app"></a>Installer l’application
L’application Microsoft Authenticator est disponible pour [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) et [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

## <a name="add-accounts-to-the-app"></a>Ajouter des comptes à l’application
Pour chaque compte que vous souhaitez ajouter à l’application Microsoft Authenticator, utilisez l’une des procédures suivantes.

### <a name="add-a-personal-microsoft-account-to-the-app"></a>Ajouter un compte Microsoft personnel à l’application

Pour un compte Microsoft personnel (celui qui vous permet de vous connecter à Outlook.com, Xbox, Skype, etc.), il vous suffit de vous connecter à votre compte dans l’application Microsoft Authenticator.

### <a name="add-a-work-or-school-account-to-the-app-using-the-qr-code-scanner"></a>Ajouter un compte professionnel ou scolaire à l’application à l’aide du scanneur de code QR
1. Accédez à l’écran des paramètres de vérification de la sécurité.  Pour savoir comment accéder à cet écran, consultez [Problèmes avec Azure Multi-Factor Authentication](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page).
2. Cochez la case **Application Azure Authenticator**, puis sélectionnez **Configurer**.

    ![Le bouton Configurer sur l’écran des paramètres de vérification de la sécurité.](./media/authenticator-app-how-to/azureauthe.png)

    Cela fait apparaître un écran avec un code QR dessus.

    ![Écran qui fournit le code QR](./media/authenticator-app-how-to/barcode2.png)
3. Ouvrez l’application Microsoft Authenticator Sur l’écran des **comptes**, sélectionnez **+**, puis indiquez que vous souhaitez ajouter un compte professionnel ou scolaire.
4. Utilisez l’appareil photo pour scanner le code QR, puis sélectionnez **Terminé** pour fermer l’écran de code QR.

    Si votre appareil photo ne fonctionne pas correctement, vous pouvez [entrer manuellement le code QR et l’URL](#add-an-account-to-the-app-manually).

5. Lorsque l’application affiche le nom de votre compte souligné d’un code à six chiffres, c’est que vous avez terminé. 

    ![Écran de comptes](./media/authenticator-app-how-to/accounts.png)

### <a name="add-an-account-to-the-app-manually"></a>Ajouter manuellement un compte à l’application
1. Accédez à l’écran des paramètres de vérification de la sécurité.  Pour savoir comment accéder à cet écran, consultez [Problèmes avec Azure Multi-Factor Authentication](multi-factor-authentication-end-user-manage-settings.md).
2. Sélectionnez **Configurer**.

    ![Le bouton Configurer sur l’écran des paramètres de vérification de la sécurité.](./media/authenticator-app-how-to/azureauthe.png)

    Cela fait apparaître un écran avec un code QR dessus.  Notez le code et l’URL.

    ![Écran qui fournit le code QR et l’URL](./media/authenticator-app-how-to/barcode2.png)
3. Ouvrez l’application Microsoft Authenticator Sur l’écran des **comptes**, sélectionnez **+**, puis indiquez que vous souhaitez ajouter un compte professionnel ou scolaire.

4. Dans le scanner, sélectionnez **enter code manually**(entrer le code manuellement).

    ![Écran pour scanner un code QR](./media/multi-factor-authentication-end-user-first-time/scan2.png)
5. Entrez le code et l’URL dans les zones appropriées de l’application, puis sélectionnez **Terminer**.

    ![Écran pour saisir le code et de l’URL](./media/authenticator-app-how-to/manual.png)

6. Lorsque l’application affiche le nom de votre compte souligné d’un code à six chiffres, c’est que vous avez terminé.

    ![Écran de comptes](./media/authenticator-app-how-to/accounts.png)

### <a name="add-an-account-to-the-app-using-touch-id"></a>Ajouter un compte à l’application à l’aide de Touch ID
L’application Microsoft Authenticator sur iOS prend en charge Touch ID.  Azure Multi-Factor Authentication permet aux organisations de demander un code confidentiel pour les appareils. Avec Touch ID, les utilisateurs iOS n’ont pas besoin d’entrer de code confidentiel. Au lieu de cela, ils peuvent analyser leur empreinte digitale et sélectionner **Approuver**.

La configuration de Touch ID avec Microsoft Authenticator est simple. Vous effectuez une vérification normale avec un code confidentiel. Si votre appareil prend en charge Touch ID, Microsoft Authenticator le configurera automatiquement pour ce compte.

![Vérification de la configuration de Touch ID](./media/authenticator-app-how-to/touchid1.png)

À partir de là, lorsque vous êtes invité à vérifier votre connexion, vous sélectionnez la notification Push reçue et vous scannez votre empreinte digitale au lieu d’entrer votre code confidentiel.

![Notification Push](./media/authenticator-app-how-to/touchid2.png)


