---
title: "Expérience de connexion Azure MFA avec la vérification en deux étapes | Microsoft Docs"
description: "Cette page vous fournit des conseils pour consulter les différentes méthodes de connexion disponibles avec Azure MFA."
keywords: "authentification de l&quot;utilisateur, expérience de connexion, connexion avec un téléphone mobile, connexion avec le téléphone de bureau"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: pblachar
ms.assetid: b310b762-471b-4b26-887a-a321c9e81d46
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: c0af680c83a72a9f2a415999141f9913c8b724a4
ms.openlocfilehash: 85e0212ed9d7bde001f9b5cd4776d34ad1f01816


---
# <a name="the-sign-in-experience-with-azure-multi-factor-authentication"></a>Expérience de connexion avec Azure Multi-Factor Authentication
> [!NOTE]
> Cet article vise à présenter de manière détaillée une expérience de connexion classique. Pour obtenir de l’aide concernant la connexion ou pour résoudre des problèmes, voir [Difficultés avec la vérification en deux étapes](multi-factor-authentication-end-user-troubleshoot.md).

## <a name="what-will-your-sign-in-experience-be"></a>Quelle sera votre expérience de connexion ?
Selon la méthode de connexion et l'authentification multifacteur utilisées, le processus est différent. Choisissez l’option qui décrit le mieux votre utilisation :

| Comment vous connectez-vous ? | 
| --- |
| [Avec mon téléphone mobile ou de bureau](#signing-in-with-mobile-or-office-phone) |
| [Avec des notifications envoyées par l’application Microsoft Authenticator](#signing-in-with-the-microsoft-authenticator-app-using-notification) |
| [Avec des codes de vérification envoyés par l’application Microsoft Authenticator](#signing-in-with-the-microsoft-authenticator-app-using-verification-code) |
| [Avec une autre méthode, car je ne peux pas utiliser ma méthode préférée à l’heure actuelle](#signing-in-with-an-alternate-method) |

## <a name="signing-in-with-mobile-or-office-phone"></a>Connexion avec un téléphone mobile ou de bureau
Les informations suivantes décrivent l'utilisation de l'authentification multifacteur avec votre téléphone de bureau ou mobile.

1. Connectez-vous à une application ou un service comme Office 365 à l’aide de votre nom d’utilisateur et votre mot de passe.  
2. Microsoft vous appelle.  
3. Répondez au téléphone et appuyez sur la touche #.  
4. Vous devez maintenant être connecté.  

## <a name="signing-in-with-the-microsoft-authenticator-app"></a>Connexion avec l’application Microsoft Authenticator 
Les informations suivantes décrivent l’utilisation de l’application Microsoft Authenticator pour les vérifications en deux étapes. Il existe deux façons différentes d’utiliser l’application. Vous pouvez soit recevoir des notifications Push sur votre appareil, soit ouvrir l’application pour obtenir un code de vérification.

### <a name="to-sign-in-with-a-notification-from-the-microsoft-authenticator-app"></a>Pour vous connecter avec des notifications envoyées par l’application Microsoft Authenticator
1. Connectez-vous à une application ou un service comme Office 365 à l’aide de votre nom d’utilisateur et votre mot de passe.
2. Microsoft envoie une notification à l’application Microsoft Authenticator sur votre appareil.

  ![Microsoft envoie une notification](./media/multi-factor-authentication-end-user-signin/notify.png)

3. Ouvrez la notification sur votre téléphone, puis sélectionnez la clé **Vérifier**. Si votre entreprise requiert un code confidentiel, vous pouvez l’entrer ici.
4. Vous devez maintenant être connecté.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>Pour vous connecter à l’application Microsoft Authenticator à l’aide d’un code de vérification

Si vous utilisez l’application Microsoft Authenticator pour obtenir des codes de vérification, lorsque vous ouvrez l’application vous verrez un nombre sous le nom de votre compte. Ce nombre change toutes les trente secondes afin que vous n’utilisiez pas deux fois le même. Lorsque vous êtes invité à entrer un code de vérification, ouvrez l’application et utilisez le nombre qui est actuellement affiché. 

1. Connectez-vous à une application ou un service comme Office 365 à l’aide de votre nom d’utilisateur et votre mot de passe.
2. Microsoft vous demande un code de vérification.

  ![Entrer le code de vérification](./media/multi-factor-authentication-end-user-signin/verify3.png)

3. Ouvrez l’application Microsoft Authenticator sur votre téléphone et entrez le code dans la zone où vous vous connectez.
4. Vous devez maintenant être connecté.

## <a name="signing-in-with-an-alternate-method"></a>Connexion avec une autre méthode
Parfois, vous n’avez pas le téléphone ou l’appareil que vous avez configuré en tant que méthode de vérification par défaut avec vous. C’est la raison pour laquelle nous vous recommandons de définir des méthodes de sauvegarde pour votre compte. La section suivante vous montre comment vous connecter avec une autre méthode quand votre méthode principale n’est pas disponible.

1. Connectez-vous à une application ou un service comme Office 365 à l’aide de votre nom d’utilisateur et votre mot de passe.
2. Sélectionnez **Utiliser une autre option de vérification**. Vous verrez autant d’options de vérification que vous en avez configurées.

  ![Utiliser une autre méthode](./media/multi-factor-authentication-end-user-signin/alt.png)

3. Choisissez une autre méthode et connectez-vous.

## <a name="next-steps"></a>Étapes suivantes

Si vous rencontrez des problèmes de connexion avec la vérification en deux étapes, obtenez plus d’informations sur la page [Difficultés avec la vérification en deux étapes](multi-factor-authentication-end-user-troubleshoot.md).

Apprenez comment [gérer les paramètres de la vérification en deux étapes](multi-factor-authentication-end-user-manage-settings.md).

Découvrez comment [prendre en main l’application Microsoft Authenticator](microsoft-authenticator-app-how-to.md) afin que vous puissiez utiliser les notifications pour vous connecter, plutôt que des SMS et des appels téléphoniques. 


<!--HONumber=Feb17_HO1-->


