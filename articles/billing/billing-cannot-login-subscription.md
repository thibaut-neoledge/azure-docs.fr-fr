---
title: "Connexion impossible à l’abonnement Azure | Microsoft Docs"
description: "Fournit des informations sur la résolution de certains problèmes de connexion à l’abonnement Azure."
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.assetid: d1545298-99db-4941-8e97-f24a06bb7cb6
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 09c18ca6a967c2930ddd2b16d37f0bc606712ea1
ms.openlocfilehash: fc1c8cb6b7c186e189658cacf218ec7e9261472a


---
# <a name="i-cant-sign-in-to-manage-my-azure-subscription"></a>Impossible de me connecter pour gérer mon abonnement Azure
Cet article vous présente certaines des méthodes plus courantes pour résoudre les problèmes de connexion.

## <a name="page-hangs-in-the-loading-status"></a>La page se bloque à l’état de chargement
Si la page de votre navigateur Internet se bloque, essayez chacune des étapes suivantes jusqu’à ce que vous puissiez accéder au [portail Azure](https://portal.azure.com).

* Actualisez la page.
* Utilisez un autre navigateur Internet.
* Si vous utilisez Microsoft Internet Explorer, accédez au portail Azure en utilisant le mode de navigation InPrivate. 
  
  A.    Cliquez sur **Outils** ![bouton Outils](./media/billing-cannot-login-subscription/Toolsbutton.png) > **Sécurité** > **Navigation InPrivate**.
  
  B.    Accédez au [Portail Azure](https://portal.azure.com), puis connectez-vous.

## <a name="error-message-no-subscriptions-found"></a>Message d’erreur « Aucun abonnement trouvé »
Si votre compte ne dispose pas des autorisations suffisantes, un message d’erreur **Aucun abonnement trouvé** peut s’afficher. Assurez-vous de vous connecter avec le bon identifiant administrateur. L’administrateur de compte peut uniquement accéder au [centre des comptes](https://account.windowsazure.com/Subscriptions). Les administrateurs de Service (SA) et coadministrateurs (CA) ont uniquement accès au [portail Azure](https://portal.azure.com) ou au portail Azure Classic.

**Scénario 1 : Le message d’erreur s’affiche sur le [portail Azure](https://portal.azure.com)**

Pour résoudre ce problème, [ajoutez le rôle de co-administrateur ou de propriétaire](billing-add-change-azure-subscription-administrator.md) pour le compte.

**Scénario 2 : Le message d’erreur s’affiche dans le [Centre des comptes Azure](https://account.windowsazure.com/Subscriptions)**

Vérifiez si le compte utilisé correspond à l’administrateur du compte. Pour vérifier qui est l’administrateur du compte, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le menu Hub, sélectionnez **Abonnement**.
3. Sélectionnez l’abonnement que vous souhaitez vérifier, puis choisissez **Paramètres**.
4. Sélectionner **Propriétés**. L’administrateur de compte de l’abonnement s’affiche dans la zone **Administrateur de compte** .

## <a name="you-are-automatically-signed-in-as-a-different-user"></a>Vous êtes automatiquement connecté comme un autre utilisateur
Ce problème peut se produire si vous utilisez plusieurs comptes utilisateur dans un navigateur Internet.

Pour résoudre le problème, essayez l’une des méthodes suivantes :

* Effacez la mémoire cache et supprimez les cookies Internet. Dans Internet Explorer, cliquez sur **Outils** ![bouton Outils](./media/billing-cannot-login-subscription/Toolsbutton.png) > **Options Internet** > **Supprimer**. Vérifiez que les cases à cocher pour les fichiers temporaires, les cookies, les mots de passe et l’historique de navigation sont sélectionnées, puis cliquez sur Supprimer.
* Réinitialisez les paramètres Internet Explorer pour rétablir les paramètres personnels que vous avez définis. Cliquez sur **Outils** ![bouton Outils](./media/billing-cannot-login-subscription/Toolsbutton.png)> **Options Internet** > **Avancé** > cochez la case **Supprimer les paramètres personnels** > **Réinitialiser**.
* Accédez au portail Azure en mode de navigation InPrivate. Cliquez sur **Outils** ![bouton Outils](./media/billing-cannot-login-subscription/Toolsbutton.png) > **Sécurité** > **Navigation InPrivate**.

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contactez le support technique.
Si vous avez besoin d’aide, [contactez le support technique](http://go.microsoft.com/fwlink/?linkid=544831&clcid=0x409) pour obtenir une prise en charge rapide de votre problème. 




<!--HONumber=Feb17_HO2-->


