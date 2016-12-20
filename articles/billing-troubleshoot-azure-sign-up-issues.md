---
title: "Résoudre les problèmes d’inscription à Azure | Microsoft Docs"
description: "Fournit des informations sur la résolution de certains problèmes d’inscription à Azure."
services: 
documentationcenter: 
author: JiangChen79
manager: felixwu
editor: 
tags: billing,top-support-issue
ms.assetid: a0907da1-cb2d-41d1-a97f-43841fabe355
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/25/2016
ms.author: cjiang
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: e2e22b767ce1d54e7b90158fb6adb877e3a165b8


---
# <a name="i-cant-sign-up-for-azure"></a>Je ne parviens pas à m’inscrire à Azure
Si vous ne parvenez pas à vous inscrire à Azure, il existe plusieurs choses que vous pouvez vérifier pour résoudre le problème.

## <a name="no-text-messages-or-calls-during-sign-up-account-verification"></a>Aucun message texte ni appel lors de la vérification du compte à l’inscription
* Vérifiez que votre numéro de téléphone peut recevoir des SMS.
* Vérifiez par deux fois le numéro de téléphone saisi, y compris l’indicatif du pays sélectionné dans le menu déroulant.
* Assurez-vous que votre téléphone peut recevoir des messages texte (SMS) si vous utilisez l’option « Envoyer un message texte » ou s’il peut recevoir des appels téléphoniques si vous choisissez l’option « M’appeler ».
* Si vous utilisez un téléphone mobile, vérifiez que vous disposez d’une bonne connexion téléphonique.
* Attendez jusqu’à 4 minutes pour permettre au système de messagerie de vous envoyer le code texte si vous choisissez l’option « Envoyer un message texte ».
* Dès que vous recevez le message texte, insérez le code dans la zone de texte et cliquez sur le bouton de vérification pour continuer.

### <a name="suggestions"></a>Suggestions
* Si vous ne recevez pas de message texte (SMS) sur votre téléphone, utilisez la méthode de vérification « m’appeler ».
* Si la vérification par téléphone à l’aide des méthodes SMS et « m’appeler » échoue, utilisez un autre numéro de téléphone.
* Vous ne pouvez pas utiliser un numéro de téléphone VoIP pour le processus de vérification par téléphone.

> [!NOTE]
> Vous pouvez modifier ultérieurement votre numéro de téléphone par défaut en mettant à jour [vos informations de profil](billing-how-to-change-azure-account-profile.md).
> 
> 

## <a name="credit-card-declined-or-not-accepted"></a>Carte de crédit refusée
Êtes-vous sûr que le mode de paiement utilisé lors de l’inscription est pris en charge pour les activations ou paiements Azure ?

* Les cartes de crédit / débit virtuelles et prépayées ne sont pas acceptées.
* Les fournisseurs de cartes de crédit acceptées varient selon le pays auquel est associé le compte.

### <a name="suggestion"></a>Suggestion
Consultez [Votre carte de débit ou de crédit est refusée lors de l’inscription à Azure](billing-credit-card-fails-during-azure-sign-up.md) pour connaître les raisons courantes des problèmes d’inscription à l’aide d’une carte de crédit ou de débit.

## <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>Je n’arrive pas à activer un plan d’avantages Azure de type MSDN, BizSpark, BizSparkPlus ou MPN
Par le biais du canal de votre programme d’avantages, vérifiez si vous êtes éligible au plan choisi :

* MSDN
  * Vérifiez l’état de votre éligibilité dans votre [page de compte MSDN](https://msdn.microsoft.com/subscriptions/manage/default.aspx).
  * Si vous ne pouvez pas vérifier votre état, contactez l’un des [Centres de service client des abonnements MSDN](https://msdn.microsoft.com/subscriptions/contactus.aspx)
* MPN
  * Connectez-vous au [portail MPN](https://mspartner.microsoft.com/en/us/Pages/Locale.aspx) et vérifiez l’état de votre éligibilité. Vous pouvez bénéficier d’avantages supplémentaires si vous disposez des [compétences de plateforme cloud](https://mspartner.microsoft.com/en/us/pages/membership/cloud-platform-competency.aspx) appropriées.
  * Si vous ne pouvez pas vérifier votre état, contactez le [support MPN](https://mspartner.microsoft.com/en/us/Pages/Support/Premium/contact-support.aspx).
* Bizpark
  * Connectez-vous au [portail BizSpark](https://www.microsoft.com/bizspark/default.aspx#start-two) et vérifiez l’état de votre éligibilité pour BizSpark et BizSpark Plus.
  * Si vous ne pouvez pas vérifier votre statut, contactez le support BizSpark par e-mail via le lien [Contacter l’équipe BizSpark](mailto:bizspark@microsoft.com?subject=BizSpark%20Support&body=Thank%20you%20for%20contacting%20BizSpark.%20Please%20provide%20as%20much%20of%20the%20following%20information%20as%20possible,%20as%20it%20will%20help%20expedite%20our%20response%20to%20you.%0aContact%20name:%0aStartup%20name:%0aMicrosoft%20Account/Live%20ID:%0aSpecific%20description%20of%20issue%20experienced%20or%20question:%0a%0aThank%20you,%0a%0aThe%20BizSpark%20Team)

### <a name="suggestion"></a>Suggestion
Si vous avez tenté d’activer un nouveau programme d’avantages et que vous rencontrez une erreur lors de l’inscription, vérifiez que votre abonnement a bien été configuré sur la [page d’abonnement Azure](http://account.windowsazure.com/Subscriptions). Vous devrez peut-être patienter quelques minutes avant que votre abonnement apparaisse comme actif. Une fois votre abonnement activé, vous recevez un courrier électronique. Si l’état de votre abonnement reste en attente pendant plus de quatre minutes, [contactez le support Azure](http://go.microsoft.com/fwlink/?linkid=544831&clcid=0x409) pour obtenir de l’aide.

## <a name="cant-activate-new-azure-in-open-subscription"></a>Je n’arrive pas à activer un nouvel abonnement Azure dans Open
Vous devez disposer d’une clé OSA valide associée à au moins un jeton Azure dans Open pour pouvoir activer un nouvel abonnement Azure dans Open.

### <a name="suggestion"></a>Suggestion
Si vous ne disposez d’aucune clé OSA, contactez l’un des partenaires Microsoft répertoriés dans [Microsoft Pinpoint](http://pinpoint.microsoft.com/).

## <a name="cant-activate-azure-free-trial"></a>Je n’arrive pas à activer une version d’évaluation gratuite d’Azure
Avez-vous utilisé un abonnement Azure auparavant ? Les conditions d’utilisation d’Azure n’autorisent l’activation que d’une seule évaluation gratuite pour tout utilisateur n’ayant jamais utilisé Azure. Si vous avez déjà souscrit un autre type d’abonnement Azure, vous ne pouvez pas activer une version d'évaluation gratuite.

### <a name="suggestion"></a>Suggestion
* Si vous avez activé un abonnement Azure auparavant et que l’activation de votre évaluation gratuite échoue, envisagez d’acheter un abonnement avec paiement à l’utilisation. 
* Vérifiez si vous avez droit à une offre d’avantages. Pour en savoir plus, consultez la [page Détails de l’offre Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/). Les programmes d’avantages imposent des conditions requises spécifiques.

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contactez le support technique.
Si vous avez besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème. 




<!--HONumber=Nov16_HO3-->


