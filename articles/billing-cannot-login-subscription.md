---
title: Impossible de me connecter pour gérer mon abonnement Azure | Microsoft Docs
description: Fournit des informations sur la résolution de certains problèmes de connexion à l’abonnement Azure
services: ''
documentationcenter: ''
author: genlin
manager: msmbaldwin
editor: ''
tags: billing

ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/09/2016
ms.author: genli

---
# Impossible de me connecter pour gérer mon abonnement Azure
Cet article vous présente certaines des méthodes plus courantes pour résoudre les problèmes de connexion.

> [!NOTE]
> Si à un moment donné dans cet article, vous avez besoin d’aide, veuillez [contacter le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.
> 
> 

## La page se bloque à l’état de chargement
Ce problème peut être dû à un dysfonctionnement qui affecte votre navigateur Internet.

Pour résoudre ce problème, essayez les méthodes suivantes, dans l’ordre indiqué. Après chaque méthode, essayez de vous reconnecter à la page de connexion sur le portail.

* Actualisez la page.
* Utilisez un autre navigateur Internet.
* Si vous utilisez Microsoft Internet Explorer, accédez au portail Azure en utilisant le mode de navigation InPrivate. Pour ce faire, procédez comme suit :
  
  A. Cliquez sur **Outils** ![bouton Outils](./media/billing-cannot-login-subscription/Toolsbutton.png) > **Sécurité** > **Navigation InPrivate**.
  
  B. Accédez au [Portail Azure](https://portal.azure.com), puis connectez-vous.

## Message d’erreur « Aucun abonnement trouvé »
Ce problème peut se produire si le compte ne dispose pas des droits d’utilisateur suffisants. Un administrateur de compte ne peut accéder qu’au [Centre des comptes](https://account.windowsazure.com/), tandis que les administrateurs de services (SA) et les coadministrateurs (CA) bénéficient uniquement d’un accès au [Portail Azure](https://portal.azure.com).

**Scénario 1 : Le message d’erreur s’affiche sur le [Portail Azure](https://portal.azure.com)**

Pour résoudre ce problème, [ajoutez le rôle de co-administrateur ou de propriétaire](billing-add-change-azure-subscription-administrator.md) pour le compte.

**Scénario 2 : Le message d’erreur s’affiche dans le [Centre des comptes Azure](https://account.windowsazure.com/Subscriptions)**

Vérifiez si le compte utilisé correspond à l’administrateur du compte. Pour vérifier qui est l’administrateur du compte, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le menu Hub, sélectionnez **Abonnement**.
3. Sélectionnez l’abonnement que vous souhaitez vérifier, puis choisissez **Paramètres**.
4. Sélectionner **Propriétés**. L’administrateur de compte de l’abonnement s’affiche dans la zone **Administrateur de compte**.

## Vous êtes automatiquement connecté comme un autre utilisateur
Ce problème peut se produire si vous utilisez plusieurs comptes utilisateur dans un navigateur Internet.

Pour résoudre le problème, essayez l’une des méthodes suivantes :

* Déconnectez-vous du portail, puis reconnectez-vous avec le compte que vous souhaitez utiliser.
* Effacez la mémoire cache et supprimez les cookies Internet. Pour ce faire, dans Internet Explorer, cliquez sur **Outils** ![bouton Outils](./media/billing-cannot-login-subscription/Toolsbutton.png) > **Options Internet** > **Supprimer**, vérifiez que les cases à cocher pour les fichiers temporaires, les cookies, les mots de passe et l’historique de navigation sont sélectionnées, puis cliquez sur Supprimer.
* Réinitialisez les paramètres Internet Explorer pour rétablir les paramètres personnels que vous avez définis. Pour ce faire, cliquez sur **Outils** ![bouton Outils](./media/billing-cannot-login-subscription/Toolsbutton.png)> **Options Internet** > **Avancé** > cochez la case **Supprimer les paramètres personnels** > **Réinitialiser**.
* Accédez au portail Azure en mode de navigation InPrivate. Pour ce faire, cliquez sur **Outils** ![bouton Outils](./media/billing-cannot-login-subscription/Toolsbutton.png) > **Sécurité** > **Navigation InPrivate**.

## Vous devez vous connecter avec un compte professionnel
Votre compte Microsoft correspond à l’adresse de messagerie et au mot de passe que vous utilisez pour vous connecter à n’importe quel programme ou service Windows Live (par exemple, Outlook, Hotmail, MSN ou OneDrive). Vous pouvez configurer un compte Microsoft en utilisant l’une de vos adresses de messagerie personnelles, y compris la messagerie de votre entreprise. Pour plus d’informations, consultez [www.microsoft.com/account](http://www.microsoft.com/account).

La page de connexion par défaut du Portail Azure est pour le compte Microsoft. Si votre compte est associé à un compte professionnel, sélectionnez l’option de connexion appropriée, comme indiqué ci-dessous. Pour plus d’informations sur l’utilisation des comptes professionnels, consultez la page [Inscription à Azure en tant qu’organisation](active-directory/sign-up-organization.md).

![page de connexion](./media/billing-cannot-login-subscription/signin.png)

> [!NOTE]
> Si vous avez d’autres questions, veuillez [contacter le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.
> 
> 

<!---HONumber=AcomDC_0928_2016-->