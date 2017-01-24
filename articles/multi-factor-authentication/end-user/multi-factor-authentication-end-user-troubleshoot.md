---
title: "Vérification en deux étapes pour résoudre les problèmes | Microsoft Docs"
description: "Ce document fournit aux utilisateurs des informations sur la procédure à suivre en cas de problème avec l&quot;Azure Multi-Factor Authentication."
services: multi-factor-authentication
keywords: "client de l&quot;authentification multifacteur, problème d&quot;authentification, ID de corrélation"
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 4ff028f88e984f28bc0f4a228aabed1fabc90560
ms.openlocfilehash: 2eab76ba42955616ae4d6909c1568f03da0c1819


---
# <a name="having-trouble-with-two-step-verification"></a>Difficultés avec la vérification en deux étapes
Cet article décrit certains des problèmes que vous pourriez rencontrer avec la vérification en deux étapes. Si celui auquel vous êtes confronté n’en fait pas partie, veuillez envoyer des commentaires détaillés dans la section Commentaires afin que nous puissions apporter les améliorations nécessaires.

## <a name="i-lost-my-phone-or-it-was-stolen"></a>Mon téléphone a été perdu ou volé
Il existe deux moyens d’accéder de nouveau à votre compte. Le premier consiste à se connecter avec votre autre numéro de téléphone d’authentification, si vous l’avez défini. Le deuxième consiste à demander à votre administrateur d’effacer vos paramètres.

En cas de perte ou de vol de votre téléphone, nous vous recommandons également de demander à votre administrateur de réinitialiser vos mots de passe d’application et de désactiver les appareils mémorisés. Si votre administrateur ne sait pas comment réaliser ces opérations, renvoyez-le à cet article : [Gérer les utilisateurs et les appareils](../multi-factor-authentication-manage-users-and-devices.md).

### <a name="use-an-alternate-phone-number"></a>Utiliser un autre numéro de téléphone
Si vous avez défini plusieurs options de vérification, notamment un numéro de téléphone secondaire ou une application d’authentification sur un autre appareil, vous pouvez utiliser l’une d’entre elles pour vous connecter.

Pour vous connecter à l’aide d’un autre numéro de téléphone, procédez comme suit :

1. Ouvrez une session comme à l’ordinaire.
2. Lorsque vous êtes invité à vérifier votre compte, choisissez **Utiliser une autre option de vérification**.
   
    ![Vérification différente](./media/multi-factor-authentication-end-user-manage/differentverification.png)
3. Sélectionnez le numéro de téléphone auquel vous avez accès.
   
    ![Autre téléphone](./media/multi-factor-authentication-end-user-manage/altphone2.png)
4. Une fois de retour dans votre compte, [gérez vos paramètres](multi-factor-authentication-end-user-manage-settings.md) pour modifier votre numéro de téléphone d’authentification.

> [!IMPORTANT]
> Il est important de configurer un numéro de téléphone d’authentification secondaire. Si votre numéro de téléphone principal et votre application mobile se trouvent sur le même téléphone, vous aurez besoin d’une troisième option en cas de perte ou de vol de votre téléphone.   

### <a name="clear-your-settings"></a>Effacer vos paramètres
Si vous n’avez pas configuré de numéro de téléphone secondaire d’authentification, vous devrez contacter votre administrateur. Demandez-lui d’effacer vos paramètres : de cette façon, la prochaine fois que vous vous connecterez, vous serez invité à [reconfigurer votre compte](multi-factor-authentication-end-user-first-time.md).

## <a name="i-am-not-receiving-a-text-or-call-on-my-phone"></a>Je ne reçois pas le SMS ou l’appel sur mon téléphone
Plusieurs raisons peuvent expliquer que vous ne receviez pas le SMS ou l’appel téléphonique malgré vos tentatives de connexion. S’il vous est déjà arrivé par le passé de recevoir des SMS ou des appels téléphoniques sur votre téléphone sans difficulté, il s’agit probablement d’un problème avec le fournisseur de téléphonie, et non avec votre compte. Assurez-vous de recevoir un signal de bonne qualité ; dans le cas d’un SMS, vérifiez que votre forfait et votre téléphone prennent en charge les messages texte.

Si vous avez attendu un SMS ou un appel pendant plusieurs minutes, le moyen le plus rapide d’accéder à votre compte est d’essayer une autre option.

1. Sélectionnez **Utiliser une autre option de vérification** sur la page en attente de vérification.
   
    ![Vérification différente](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)
2. Sélectionnez la méthode à utiliser (numéro de téléphone ou envoi).
   
    Si vous avez reçu plusieurs codes de vérification, seul le plus récent fonctionne.

Si vous n’avez pas configuré d’autres méthodes, contactez votre administrateur et demandez-lui d’effacer vos paramètres. La prochaine fois que vous vous connecterez, vous devrez [reconfigurer l’authentification multifacteur](multi-factor-authentication-end-user-first-time.md).

Si vous subissez souvent des retards en raison d’un mauvais signal téléphonique, nous vous recommandons d’utiliser [l’application Microsoft Authenticator](microsoft-authenticator-app-how-to.md) sur votre smartphone. L’application peut générer des codes de sécurité aléatoires qui vous permettent de vous connecter et qui ne nécessitent pas de signal téléphonique ou de connexion Internet.

## <a name="app-passwords-are-not-working"></a>Les mots de passe d’application ne fonctionnent pas
Tout d’abord, assurez-vous que vous avez saisi correctement votre mot de passe.  S’il ne fonctionne toujours pas, essayez de vous connecter et de [créer un mot de passe d’application](multi-factor-authentication-end-user-app-passwords.md).  Si le problème persiste, contactez votre administrateur et demandez-lui de [supprimer vos mots de passe d’application existants](../multi-factor-authentication-manage-users-and-devices.md). Vous pourrez alors créer un mot de passe.

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Je n’ai pas trouvé de réponse à mon problème.
Si vous avez essayé de suivre ces étapes de dépannage mais que vous rencontrez toujours des problèmes, contactez votre administrateur ou la personne qui a configuré l’authentification multifacteur pour vous. Ils doivent être en mesure de vous aider.

Vous pouvez également publier une question sur les [Forums Azure AD](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD) ou [contacter le support technique](https://support.microsoft.com/contactus) : nous répondrons à votre problème dès que possible.

Si vous contactez le support technique, fournissez les informations suivantes :

* **ID utilisateur** : avec quelle adresse de messagerie avez-vous essayé de vous connecter ?
* **Description générale de l’erreur** : quel message d’erreur avez-vous précisément reçu ?  Si aucun message d’erreur n’est apparu, décrivez en détail le comportement inattendu remarqué.
* **Page** : sur quelle page vous trouviez-vous lorsque l’erreur est apparue (incluez l’URL) ?
* **ErrorCode** : le code d’erreur spécifique que vous recevez.
* **SessionId** : l’ID de session spécifique que vous recevez.
* **ID de corrélation** : quel était l’ID de corrélation généré lorsque l’utilisateur a remarqué l’erreur ?
* **Horodatage** : quelles étaient la date et l’heure précises auxquelles l’erreur s’est produite (incluez le fuseau horaire) ?

La plupart de ces informations sont accessibles sur votre page de connexion. Lorsque vous ne vérifiez pas votre connexion dans les temps, sélectionnez **Afficher les détails**.

![Détails de l’erreur de connexion](./media/multi-factor-authentication-end-user-troubleshoot/view_details.png)

Ces informations nous aident à résoudre votre problème aussi rapidement que possible.

## <a name="related-topics"></a>Rubriques connexes
* [Gérer les paramètres de la vérification en deux étapes](multi-factor-authentication-end-user-manage-settings.md)  
* [FAQ sur l’application Microsoft Authenticator](microsoft-authenticator-app-faq.md)




<!--HONumber=Dec16_HO4-->


