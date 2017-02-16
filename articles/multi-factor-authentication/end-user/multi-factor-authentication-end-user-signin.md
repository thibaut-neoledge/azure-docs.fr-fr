---
title: "Expérience de connexion Azure MFA avec la vérification en deux étapes | Microsoft Docs"
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
ms.date: 12/15/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 4ff028f88e984f28bc0f4a228aabed1fabc90560
ms.openlocfilehash: 63951136d4f7bc2eb81c24ddfd58e696fc31cf37


---
# <a name="the-sign-in-experience-with-azure-multi-factor-authentication"></a>Expérience de connexion avec Azure Multi-Factor Authentication
> [!NOTE]
> La documentation suivante fournie sur cette page montre une expérience de connexion classique.  Pour obtenir de l'aide concernant la connexion, référez-vous à [Problèmes avec Azure Multi-Factor Authentication](multi-factor-authentication-end-user-manage-settings.md)
>
>

## <a name="what-will-your-sign-in-experience-be"></a>Quelle sera votre expérience de connexion ?
Selon la méthode de connexion et l'authentification multifacteur utilisées, le processus est différent.  Dans cette section, nous fournissons des informations sur le processus de connexion.  Choisissez ce qui décrit le mieux votre méthode de connexion :

| Que faites-vous ? | Description |
|:--- |:--- |
| [Connexion avec un téléphone mobile ou de bureau](#signing-in-with-mobile-or-office-phone) |Voici le processus de connexion habituel lors d’une connexion à l'aide de votre téléphone mobile ou de bureau. |
| [Connexion avec l’application Microsoft Authenticator à l’aide de notifications](#signing-in-with-the-microsoft-authenticator-app-using-notification) |Voici ce à quoi vous pouvez vous attendre en utilisant l’application Microsoft Authenticator avec des notifications. |
| [Connexion avec l’application Microsoft Authenticator à l’aide d’un code de vérification](#signing-in-with-the-microsoft-authenticator-app-using-verification-code) |Voici ce à quoi vous pouvez vous attendre en utilisant l’application Microsoft Authenticator avec un code de vérification. |
| [Connexion avec une autre méthode](#signing-in-with-an-alternate-method) |Ceci vous présente le processus de connexion habituel si vous souhaitez utiliser une autre méthode. |

## <a name="signing-in-with-mobile-or-office-phone"></a>Connexion avec un téléphone mobile ou de bureau
Les informations suivantes décrivent l'utilisation de l'authentification multifacteur avec votre téléphone de bureau ou mobile.

### <a name="to-sign-in-with-a-call-to-your-office-or-mobile-phone"></a>Connexion avec un appel sur votre téléphone de bureau ou mobile
* Connectez-vous à une application ou un service comme Office 365 à l’aide de votre nom d’utilisateur et votre mot de passe.  
* Microsoft vous appelle.  
* Répondez au téléphone et appuyez sur la touche #.  
* Vous devez maintenant être connecté.  

## <a name="signing-in-with-the-microsoft-authenticator-app-using-notification"></a>Connexion avec l’application Microsoft Authenticator à l’aide de notifications
Les informations suivantes décrivent l'utilisation de l'authentification multifacteur avec l’application Microsoft Authenticator lorsqu'une notification vous est envoyée.

### <a name="to-sign-in-with-a-notification-sent-the-microsoft-authenticator-app"></a>Pour vous connecter avec des notifications envoyées par l’application Microsoft Authenticator
* Connectez-vous à une application ou un service comme Office 365 à l’aide de votre nom d’utilisateur et votre mot de passe.
* Microsoft vous envoie une notification.

![Microsoft envoie une notification](./media/multi-factor-authentication-end-user-signin/notify.png)

* Répondez au téléphone et appuyez sur la touche de vérification.  Si votre entreprise requiert un code PIN, il vous sera demandé ici.

![Verify](./media/multi-factor-authentication-end-user-signin/phone2.png)

![Paramétrage](./media/multi-factor-authentication-end-user-first-time/scan3.png)

* Vous devez maintenant être connecté.

## <a name="signing-in-with-the-microsoft-authenticator-app-using-verification-code"></a>Connexion avec l’application Microsoft Authenticator à l’aide d’un code de vérification
Les informations suivantes décrivent l'utilisation de l'authentification multifacteur avec l’application Microsoft Authenticator lorsque vous utilisez un code de vérification.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>Pour vous connecter à l’application Microsoft Authenticator à l’aide d’un code de vérification
* Connectez-vous à une application ou un service comme Office 365 à l’aide de votre nom d’utilisateur et votre mot de passe.
* Microsoft vous demande un code de vérification.

![Entrer le code de vérification](./media/multi-factor-authentication-end-user-signin/verify3.png)

* Ouvrez l’application Microsoft Authenticator sur votre téléphone et entrez le code dans la zone où vous vous connectez.

![Obtenir le code](./media/multi-factor-authentication-end-user-signin/phone3.png)

* Vous devez maintenant être connecté.

## <a name="signing-in-with-an-alternate-method"></a>Connexion avec une autre méthode
La section suivante vous montre comment vous connecter avec une autre méthode quand votre méthode principale n’est pas disponible.

### <a name="to-sign-in-with-an-alternate-method"></a>Pour vous connecter avec une autre méthode
* Connectez-vous à une application ou un service comme Office 365 à l’aide de votre nom d’utilisateur et votre mot de passe.
* Sélectionnez Utiliser une autre option de vérification.  Un choix de différentes options s’affiche. Le nombre qui s’affiche dépend du nombre que vous avez défini.

![Utiliser une autre méthode](./media/multi-factor-authentication-end-user-signin/alt.png)

* Choisissez une autre méthode et connectez-vous.



<!--HONumber=Dec16_HO4-->


