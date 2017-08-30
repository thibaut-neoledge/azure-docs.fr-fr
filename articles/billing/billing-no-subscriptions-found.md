---
title: "Erreur Aucun abonnement trouvé lorsque vous tentez de vous connecter au portail Azure ou au centre des comptes Azure | Documents Microsoft"
description: "Fournit la solution pour un problème dans lequel le message d’erreur Aucun abonnement trouvé s’affiche lorsque vous vous connectez au portail Azure ou au centre des comptes Azure."
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
ms.date: 08/21/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: a4ce9b219c05f8469379c2aac5241fcfffd16033
ms.contentlocale: fr-fr
ms.lasthandoff: 08/24/2017

---

# <a name="no-subscriptions-found-error-in-azure-portal-or-azure-account-center"></a>Erreur Aucun abonnement trouvé dans le portail Azure ou le centre des comptes Azure
Vous pouvez recevoir un message d’erreur « Aucun abonnement trouvé » lorsque vous essayez de vous connecter au [portail Azure](https://portal.azure.com/) ou au [centre des comptes Azure](https://account.windowsazure.com/Subscriptions). Cet article fournit une solution à ce problème.

## <a name="symptom"></a>Symptôme

Lorsque vous essayez de vous connecter au [portail Azure](https://portal.azure.com/) ou au [centre des comptes Azure](https://account.windowsazure.com/Subscriptions), vous recevez le message d’erreur suivant : « Aucun abonnement trouvé ».

## <a name="cause"></a>Cause :

Ce problème se produit si votre compte ne dispose pas des autorisations suffisantes. 

## <a name="solution"></a>Solution

Assurez-vous de vous connecter avec l’administrateur approprié. L’administrateur du compte peut uniquement accéder au Centre des comptes. Les administrateurs de service (SA) et les coadministrateurs (CA) ont uniquement accès au portail Azure ou au portail classique Azure.

### <a name="scenario-1-error-message-is-received-in-the-azure-portalhttpsportalazurecom"></a>Scénario 1 : le message d’erreur est reçu dans le [portail Azure](https://portal.azure.com)

Pour résoudre ce problème :

* Vérifiez que le répertoire Azure correct soit sélectionné en cliquant sur votre compte en haut à droite.

  ![Sélectionnez l’annuaire en haut à droite du portail Azure.](./media/billing-no-subscriptions-found/directory-switch.png)

* Si le répertoire Azure correct est sélectionné, mais que vous recevez néanmoins le messager d’erreur, [faites ajouter votre compte en tant que Propriétaire](billing-add-change-azure-subscription-administrator.md).

### <a name="scenario-2-error-message-is-received-in-the-azure-account-centerhttpsaccountwindowsazurecomsubscriptions"></a>Scénario 2 : le message d’erreur est reçu dans le [Centre des comptes Azure](https://account.windowsazure.com/Subscriptions)

Vérifiez que le compte utilisé correspond à l’administrateur du compte. Pour vérifier qui est l’administrateur du compte, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le menu Hub, sélectionnez **Abonnement**.
3. Sélectionnez l’abonnement que vous souhaitez vérifier, puis choisissez **Paramètres**.
4. Sélectionner **Propriétés**. L’administrateur de compte de l’abonnement s’affiche dans la zone **Administrateur de compte** .

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contactez le support technique.
Si vous avez besoin d’aide, [contactez le support technique](http://go.microsoft.com/fwlink/?linkid=544831&clcid=0x409) pour obtenir une prise en charge rapide de votre problème. 

