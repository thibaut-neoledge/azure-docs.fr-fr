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
ms.date: 12/15/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 4ff028f88e984f28bc0f4a228aabed1fabc90560
ms.openlocfilehash: b20dd0fa6812c3125c3e2afe1dc9266613191baf


---
# <a name="microsoft-authenticator"></a>Microsoft Authenticator
L’application Microsoft Authenticator fournit un niveau supplémentaire de sécurité dans votre compte Azure, par exemple bsimon@contoso.onmicrosoft.com), votre compte professionnel local (par exemple, bsimon@contoso.com), ou votre compte Microsoft (par exemple, bsimon@outlook.com).

L’application fonctionne de l’une des deux façons suivantes :

* **Notification**. L’application peut aider à empêcher tout accès non autorisé aux comptes et à arrêter les transactions frauduleuses en envoyant une notification à votre smartphone ou tablette. Affichez simplement la notification et si elle est légitime, sélectionnez **Vérifier**. Sinon, vous pouvez sélectionner **Refuser**. Pour plus d’informations sur les notifications de refus, consultez la rubrique Utilisation de la fonctionnalité de refus et signalement d’une fraude pour Multi-Factor Authentication.
* **Mot de passe avec code de vérification**. L’application peut être utilisée comme jeton logiciel pour générer un code de vérification OAuth. Vous entrez le code fourni par l’application dans l’écran de connexion, ainsi que le nom d’utilisateur et le mot de passe, lorsque vous y êtes invité. Le code de vérification fournit un deuxième formulaire d’authentification.

La publication de l’application Microsoft Authenticator remplace l’ancienne application Azure Authenticator.  L’application Azure Authenticator continue de fonctionner, mais cet article peut vous aider si vous décidez d’adopter la nouvelle application Azure Authenticator.  

## <a name="install-the-app"></a>Installer l’application
L’application Microsoft Authenticator est disponible pour [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) et [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

## <a name="add-accounts-to-the-app"></a>Ajouter des comptes à l’application
Pour chaque compte que vous souhaitez ajouter à l’application Microsoft Authenticator, utilisez l’une des procédures suivantes.

### <a name="add-an-account-to-the-app-by-using-the-qr-code-scanner"></a>Ajouter un compte à l’application à l’aide du scanneur de code QR
1. Accédez à l’écran des paramètres de vérification de la sécurité.  Pour savoir comment accéder à cet écran, consultez [Problèmes avec Azure Multi-Factor Authentication](multi-factor-authentication-end-user-manage-settings.md).
2. Sélectionnez **Configurer**.

    ![Le bouton Configurer sur l’écran des paramètres de vérification de la sécurité.](./media/authenticator-app-how-to/azureauthe.png)

    Cela fait apparaître un écran avec un code QR dessus.

    ![Écran qui fournit le code QR](./media/authenticator-app-how-to/barcode2.png)
3. Ouvrez l’application Microsoft Authenticator Sur l’écran des **comptes**, sélectionnez **+**, puis indiquez que vous souhaitez ajouter un compte professionnel ou scolaire.

    ![L’écran des comptes avec un signe plus (+)](./media/authenticator-app-how-to/addaccount3.png)

    ![Écran pour spécifier un compte professionnel ou scolaire](./media/authenticator-app-how-to/scan.png)
4. Utilisez l’appareil photo pour scanner le code QR, puis sélectionnez **Terminé** pour fermer l’écran de code QR.

    ![Écran pour scanner un code QR](./media/multi-factor-authentication-end-user-first-time/scan2.png)

    Si votre appareil photo ne fonctionne pas correctement, vous pouvez entrer manuellement le code QR et l’URL. Pour plus d’informations, consultez [Ajouter manuellement un compte à l’application](#add-an-account-to-the-app-manually).
5. Attendez que le compte soit activé. Une fois l’activation terminée, sélectionnez **Me contacter**.  Cette étape envoie une notification ou un code de vérification sur votre téléphone.  Sélectionnez **Vérifier**.

    ![Écran où vous sélectionnez Vérifier pour vous connecter](./media/authenticator-app-how-to/verify.png)
6. Si votre entreprise requiert un code confidentiel pour approuver la vérification de l’identification, entrez-le.

    ![Zone permettant d’entrer un code confidentiel](./media/multi-factor-authentication-end-user-first-time/scan3.png)
7. Une fois le code confidentiel entré, sélectionnez **Fermer**. À ce stade, la vérification doit réussir.
8. Nous vous recommandons d’entrer votre numéro de téléphone mobile en cas de perte d’accès à votre application. Spécifiez votre pays dans la liste déroulante et entrez votre numéro de téléphone mobile dans la zone située en regard du nom de pays. Sélectionnez **Suivant**.
9. À ce stade, vous avez configuré votre méthode de contact. Il est maintenant temps de configurer les mots de passe des applications hors navigateur, comme Outlook 2010 ou les versions antérieures. Si vous n’utilisez pas ces applications, sélectionnez **Terminé**. Sinon, passez à l’étape suivante.

10. Si vous utilisez des applications hors navigateur, copiez le mot de passe d’application fourni et collez-le dans vos applications. Pour connaître les étapes relatives à des applications données, comme Outlook et Lync, consultez Modification du mot de passe dans votre messagerie électronique par le mot de passe d’application et Modification du mot de passe dans votre application par le mot de passe d’application.
11. Sélectionnez **Terminé**.

Le nouveau compte doit désormais s’afficher sur l’écran des **comptes** .

![Écran de comptes](./media/authenticator-app-how-to/accounts.png)

### <a name="add-an-account-to-the-app-manually"></a>Ajouter manuellement un compte à l’application
1. Accédez à l’écran des paramètres de vérification de la sécurité.  Pour savoir comment accéder à cet écran, consultez [Problèmes avec Azure Multi-Factor Authentication](multi-factor-authentication-end-user-manage-settings.md).
2. Sélectionnez **Configurer**.

    ![Le bouton Configurer sur l’écran des paramètres de vérification de la sécurité.](./media/authenticator-app-how-to/azureauthe.png)

    Cela fait apparaître un écran avec un code QR dessus.  Notez le code et l’URL.

    ![Écran qui fournit le code QR et l’URL](./media/authenticator-app-how-to/barcode2.png)
3. Ouvrez l’application Microsoft Authenticator Sur l’écran des **comptes**, sélectionnez **+**, puis indiquez que vous souhaitez ajouter un compte professionnel ou scolaire.

    ![L’écran des comptes avec un signe plus (+)](./media/authenticator-app-how-to/addaccount3.png)

    ![Écran pour spécifier un compte professionnel ou scolaire](./media/authenticator-app-how-to/scan.png)
4. Dans le scanner, sélectionnez **enter code manually**(entrer le code manuellement).

    ![Écran pour scanner un code QR](./media/multi-factor-authentication-end-user-first-time/scan2.png)
5. Entrez le code et l’URL dans les zones appropriées dans l’application.

    ![Écran pour saisir le code et de l’URL](./media/authenticator-app-how-to/manual.png)

    ![Écran pour saisir le code et de l’URL](./media/authenticator-app-how-to/addaccount2.png)
6. Attendez que le compte soit activé. Une fois l’activation terminée, sélectionnez **Me contacter**. Cette étape envoie une notification ou un code de vérification sur votre téléphone. Sélectionnez **Vérifier**.

Le nouveau compte doit désormais s’afficher sur l’écran des **comptes** .

![Écran de comptes](./media/authenticator-app-how-to/accounts.png)

### <a name="add-an-account-to-the-app-by-using-touch-id"></a>Ajouter un compte à l’application à l’aide de Touch ID
L’application Microsoft Authenticator sur iOS prend en charge Touch ID.  Azure Multi-Factor Authentication permet aux organisations de demander un code confidentiel pour les appareils. Avec Touch ID, les utilisateurs iOS n’ont pas besoin d’entrer de code confidentiel. Au lieu de cela, ils peuvent analyser leur empreinte digitale et sélectionner **Approuver**.

La configuration de Touch ID avec Microsoft Authenticator est simple. Vous effectuez une vérification normale avec un code confidentiel. Si votre appareil prend en charge Touch ID, Microsoft Authenticator le configurera automatiquement pour ce compte.

![Vérification de la configuration de Touch ID](./media/authenticator-app-how-to/touchid1.png)

À partir de là, lorsque vous êtes invité à vérifier votre connexion, vous sélectionnez la notification Push reçue et vous scannez votre empreinte digitale au lieu d’entrer votre code confidentiel.

![Notification Push](./media/authenticator-app-how-to/touchid2.png)

## <a name="uninstall-the-old-azure-authentication-app"></a>Désinstaller l’ancienne application d’authentification Azure
Une fois que vous avez ajouté tous les comptes à la nouvelle application, vous pouvez désinstaller l’ancienne application de votre téléphone.

## <a name="delete-an-account"></a>Supprimer un compte
Pour supprimer un compte de l’application Microsoft Authenticator, sélectionnez le compte, puis **Supprimer**.

![Bouton Supprimer](./media/authenticator-app-how-to/remove.png)



<!--HONumber=Dec16_HO4-->


