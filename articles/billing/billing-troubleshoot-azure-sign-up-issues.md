---
title: "Résolution de problèmes d’inscription à Azure | Microsoft Docs"
description: "Fournit des informations sur la résolution de certains problèmes d’inscription à Azure."
services: 
documentationcenter: 
author: JiangChen79
manager: adpick
editor: 
tags: billing,top-support-issue
ms.assetid: a0907da1-cb2d-41d1-a97f-43841fabe355
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/01/2017
ms.author: cjiang
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 459d0526961d03b564a35178f9ff4ff2e343e08c
ms.openlocfilehash: 70a9d96e36f5e386e8b1c0036ef8e2f770924930
ms.lasthandoff: 03/02/2017

---
# <a name="troubleshoot-sign-up-issues-for-azure"></a>Résoudre les problèmes d’inscription pour Azure
Si vous ne parvenez pas à vous inscrire à Azure, il existe plusieurs choses que vous pouvez vérifier pour résoudre le problème.

## <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>La barre de progression se bloque dans la section « Vérification d’identité par carte »

Lors de l’inscription à Azure, une section s’intitule « Vérification d’identité par carte ». Si la barre de progression se bloque :

![Capture d’écran du blocage dans la section Vérification d’identité par carte pendant l’inscription](./media/billing-troubleshoot-azure-sign-up-issues/identity-verification-hangs.PNG)

Ce problème se produit lorsque les cookies tiers sont bloqués pour votre navigateur.

### <a name="suggestion"></a>Suggestion

1. Autorisez les cookies tiers dans les paramètres de votre navigateur.
  * Dans Edge, accédez à Paramètres -> Afficher les paramètres avancés -> Cookies, sélectionnez « Ne pas bloquer les cookies ».
  * Dans Chrome, accédez à Paramètres-> Afficher les paramètres avancés -> Confidentialité-> Paramètres de contenu, décochez la case « Bloquer les cookies et les données de site tiers ».
2. Actualisez la page d’inscription à Azure, puis vérifiez si le problème est résolu.
3. Si l’actualisation n’a pas résolu le problème, quittez et redémarrez votre navigateur, puis réessayez.

## <a name="no-text-messages-or-calls-during-sign-up-account-verification"></a>Aucun message texte ni appel lors de la vérification du compte à l’inscription
* Le transfert de votre code texte peut prendre jusqu’à quatre minutes..
* Vérifiez que votre numéro de téléphone peut recevoir des SMS.
* Vérifiez par deux fois le numéro de téléphone saisi, y compris l’indicatif du pays sélectionné dans le menu déroulant.
* Assurez-vous que votre téléphone peut recevoir des messages texte (SMS) si vous utilisez l’option « Envoyer un message texte » ou s’il peut recevoir des appels téléphoniques si vous choisissez l’option « M’appeler ».
* Dès que vous recevez le message texte, insérez le code dans la zone de texte et cliquez sur le bouton de vérification pour continuer.

### <a name="suggestions"></a>Suggestions
* Si vous ne recevez pas de message texte (SMS) sur votre téléphone, utilisez la méthode de vérification « m’appeler ».
* Si la vérification par téléphone à l’aide des méthodes SMS et « m’appeler » échoue, utilisez un autre numéro de téléphone.
* Vous ne pouvez pas utiliser un numéro de téléphone VoIP pour le processus de vérification par téléphone.

## <a name="credit-card-declined-or-not-accepted"></a>Carte de crédit refusée
Assurez-vous que le mode de paiement utilisé lors de l’inscription est pris en charge. Vous pouvez également en savoir plus sur les raisons pour lesquelles [votre carte bancaire est refusée lors de l’inscription à Azure](billing-credit-card-fails-during-azure-sign-up.md).

## <a name="free-trial-is-not-available"></a>« L'essai gratuit n'est pas disponible »
Avez-vous utilisé un abonnement Azure auparavant ? Les conditions d’utilisation d’Azure n’autorisent l’activation que d’une seule évaluation gratuite pour tout utilisateur n’ayant jamais utilisé Azure. Si vous avez déjà souscrit un autre type d’abonnement Azure, vous ne pouvez pas activer une version d'évaluation gratuite.

### <a name="suggestion"></a>Suggestion
* Inscrivez-vous à un [abonnement de type paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/).

## <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>Je n’arrive pas à activer un plan d’avantages Azure de type MSDN, BizSpark, BizSparkPlus ou MPN
Assurez-vous d’utiliser les informations d’identification de connexion correctes et vérifiez dans votre programme d’avantages si vous êtes éligible au plan choisi :

* MSDN
  * Vérifiez l’état de votre éligibilité dans votre [page de compte MSDN](https://msdn.microsoft.com/subscriptions/manage/default.aspx).
  * Si vous ne pouvez pas vérifier votre état, contactez l’un des [Centres de service client des abonnements MSDN](https://msdn.microsoft.com/subscriptions/contactus.aspx)
* MPN
  * Connectez-vous au [portail MPN](https://mspartner.microsoft.com/en/us/Pages/Locale.aspx) et vérifiez l’état de votre éligibilité. Vous pouvez bénéficier d’avantages supplémentaires si vous disposez des [compétences de plateforme cloud](https://mspartner.microsoft.com/en/us/pages/membership/cloud-platform-competency.aspx) appropriées.
  * Si vous ne pouvez pas vérifier votre état, contactez le [support MPN](https://mspartner.microsoft.com/en/us/Pages/Support/Premium/contact-support.aspx).
* BizSpark
  * Connectez-vous au [portail BizSpark](https://www.microsoft.com/bizspark/default.aspx#start-two) et vérifiez l’état de votre éligibilité pour BizSpark et BizSpark Plus.
  * Si vous ne pouvez pas vérifier votre éligibilité, vous pouvez [contacter l’équipe BizSpark](mailto:bizspark@microsoft.com?subject=BizSpark%20Support&body=Thank%20you%20for%20contacting%20BizSpark.%20Please%20provide%20as%20much%20of%20the%20following%20information%20as%20possible,%20as%20it%20will%20help%20expedite%20our%20response%20to%20you.%0aContact%20name:%0aStartup%20name:%0aMicrosoft%20Account/Live%20ID:%0aSpecific%20description%20of%20issue%20experienced%20or%20question:%0a%0aThank%20you,%0a%0aThe%20BizSpark%20Team)

## <a name="cant-activate-new-azure-in-open-subscription"></a>Je n’arrive pas à activer un nouvel abonnement Azure dans Open
Vous devez disposer d’une clé OSA valide associée à au moins un jeton Azure dans Open pour pouvoir créer un nouvel abonnement Azure dans Open.

### <a name="suggestion"></a>Suggestion
Si vous ne disposez d’aucune clé OSA, contactez l’un des partenaires Microsoft répertoriés dans [Microsoft Pinpoint](http://pinpoint.microsoft.com/).

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contactez le support technique.
Si vous avez besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.

