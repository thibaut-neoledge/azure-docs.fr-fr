---
title: "Comprendre la limite de dépense d’Azure | Microsoft Docs"
description: "Décrit le fonctionnement de la limite de dépense d’Azure et comment la désactiver"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: genli
ms.translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: 39ae134d8927f5123667b64bbd0c659cd5f62ffc
ms.contentlocale: fr-fr
ms.lasthandoff: 04/20/2017


---
# <a name="understand-azure-spending-limit-and-how-to-remove-it"></a>Comprendre la limite de dépense d’Azure et comment la désactiver

La limite de dépense d’Azure est une limite sur l’utilisation de votre abonnement Azure. Pour tout nouveau client adoptant une version d’évaluation incluant des crédits s’étalant sur plusieurs mois, la limite de dépense est activée par défaut. La limite de dépense est de 0 $. Elle ne peut pas être modifiée. La limite de dépense n’est pas disponible pour les abonnements faisant l’objet d’un paiement à l’utilisation et les plans d’engagement. Pour plus d’informations, consultez la [liste complète des offres Azure et la disponibilité de la limite de dépense](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="what-happens-when-i-reach-the-spending-limit"></a>Que se passe-t-il lorsque j’atteins la limite de dépense ?

Si votre utilisation entraîne des frais dépassant les montants mensuels inclus dans votre offre, les services que vous avez déployés sont désactivés jusqu’à la fin de ce mois de facturation. Par exemple, si vous avez déployé des services cloud, ces services sont retirés de la production et vos machines virtuelles Azure sont arrêtées et désallouées. Pour empêcher la désactivation de vos services, vous pouvez décider de supprimer votre limite de dépense. Lorsque vos services sont désactivés, les administrateurs peuvent accéder en lecture seule aux informations de vos comptes et bases de données de stockage. Au début du mois de facturation suivant, si votre offre inclut des crédits s’étalant sur plusieurs mois, votre abonnement est réactivé. Vous pouvez redéployer vos services cloud et disposer d’un accès complet à vos comptes de stockage et bases de données.

Lorsque l’abonnement d’essai gratuit atteint la limite de dépense, vous pouvez réactiver l’abonnement et le [mettre automatiquement à niveau vers notre offre de paiement à l’utilisation standard](billing-upgrade-azure-subscription.md) dans les 90 jours.

Vous recevez des notifications lorsque vous atteignez la limite de dépense correspondant à votre offre. Connectez-vous au [Centre des comptes Azure](https://account.windowsazure.com), cliquez sur **COMPTE**, puis sélectionnez **Abonnements**. Des notifications relatives aux abonnements qui ont atteint la limite de dépense s’affichent.

## <a name="things-you-are-charged-for-even-if-you-have-a-spending-limit-enabled"></a>Éléments faisant l’objet d’une facturation même si une limite de dépense est fixée

Certains services Azure et [achats sur la Place de marché](https://azure.microsoft.com/marketplace/) peuvent occasionner des frais prélevés selon le mode de paiement (CC), même si une limite de dépense a été fixée. C’est le cas, par exemple, des licences Visual Studio, d’Azure Active Directory Premium, des abonnements au support technique et de la plupart des services vendus sous marque de tiers via la Place de marché.


## <a name="when-not-to-use-the-spending-limit"></a>Quand ne pas utiliser la limite de dépense

La limite de dépense peut vous empêcher de déployer ou d’utiliser certains services de la Place de marché et de Microsoft. Voici les situations dans lesquelles vous devez lever la limite de dépense liée à votre abonnement.

- Vous prévoyez de déployer des images internes telles qu’Oracle et des services tels que Visual Studio Team Services. Cette situation entraîne presque immédiatement un dépassement de votre limite de dépense, qui a pour effet de désactiver votre abonnement.

- Vous avez des services dont le fonctionnement ne peut pas être interrompu.

- Vous avez des services et ressources utilisant des paramètres tels que des adresses IP virtuelles que vous ne voulez pas perdre. Ces paramètres sont perdus en cas de désallocation des services et ressources.


## <a name="remove-the-spending-limit"></a>Supprimer la limite de dépense

Vous pouvez supprimer la limite de dépense à tout moment, pour autant qu’un mode de paiement valide soit associé à votre abonnement. Pour les offres bénéficiant d’un crédit s’étalant sur plusieurs mois, vous pouvez également réactiver la limite de dépense au début de votre cycle de facturation suivant.

Pour supprimer votre limite de dépense, procédez comme suit :

1. Connectez-vous au [Centre des comptes Azure](https://account.windowsazure.com).

2. Sélectionnez un abonnement.

3. Si l’abonnement est désactivé parce que la limite de dépense a été atteinte, cliquez sur la notification suivante : « L’abonnement a atteint la limite de dépense et a été désactivé pour empêcher les frais. » Autrement, cliquez sur **Supprimer la limite de dépense** dans la zone **ÉTAT DE L’ABONNEMENT**.

4. Sélectionnez une option adaptée à votre situation.

|Option|Résultat|
|-------|-----|
|Supprimer la limite de dépense pour une durée indéterminée|Supprime la limite de dépense sans l'activer automatiquement au début de la période de facturation suivante.|
|Supprimer la limite de dépense pour la période de facturation en cours|Supprime la limite de dépense et l’active automatiquement au début de la période de facturation suivante.|

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contactez le support technique.
Si vous avez besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.
