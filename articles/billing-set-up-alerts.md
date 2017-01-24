---
title: Configurer des alertes de facturation pour vos abonnements Microsoft Azure | Microsoft Docs
description: Describes how you can set up alerts on your Azure bill so you can avoid billing surprises.
services: 
documentationcenter: 
author: vikdesai
manager: vikdesai
editor: 
tags: billing
ms.assetid: 9b7b3eeb-cd9d-4690-86a3-51b1e2a8974f
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: vikdesai
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: ce21f881d95b369073168d205c315d74de9f24ef


---
# <a name="set-up-billing-alerts-for-your-microsoft-azure-subscriptions"></a>Configurer des alertes de facturation pour vos abonnements Microsoft Azure
Souhaitez-vous connaître le montant de vos dépenses mensuelles pour votre abonnement Azure ? Si vous êtes l'administrateur de compte d'un abonnement Azure, vous pouvez utiliser le service d'alerte de facturation Azure pour créer des alertes de facturation personnalisées qui vous aident à surveiller et à gérer l'activité de facturation de vos comptes Azure.

Dans la mesure où ce service est en version préliminaire, la première chose à faire est de vous y inscrire. Pour activer cette fonctionnalité, visitez [la page Fonctionnalités préliminaires](https://account.windowsazure.com/PreviewFeatures) dans le portail de gestion des comptes Azure.

## <a name="set-the-alert-threshold-and-email-recipients"></a>Définir le seuil d'alerte et les destinataires des messages électroniques
Lorsque vous aurez reçu par courrier électronique la confirmation que le service de facturation a été activé pour votre abonnement, rendez-vous sur la [page Abonnements](https://account.windowsazure.com/Subscriptions) du portail des comptes. Cliquez sur l’abonnement à surveiller, puis sur **Alertes**.

![][Image1]

Cliquez ensuite sur **Ajouter une alerte** pour créer votre première alerte. Vous pouvez configurer un total de cinq alertes de facturation par abonnement, avec un seuil distinct, et jusqu’à deux destinataires de courrier électroniques pour chaque alerte.

![][Image2]

Quand vous ajoutez une alerte, vous lui donnez un nom unique, vous choisissez un seuil de dépenses, et vous choisissez également les adresses électroniques auxquelles les alertes sont envoyées. Quand vous configurez le seuil, vous pouvez choisir un **Total facturé** ou un **Crédit monétaire** dans la liste **Alerte pour**. Pour un total facturé, une alerte est envoyée quand les dépenses d'abonnement dépassent le seuil. Pour un crédit monétaire, une alerte est envoyée quand les crédits monétaires passent sous la limite. Les crédits monétaires s'appliquent habituellement aux essais et abonnements gratuits associés à des comptes MSDN.

![][Image3]

Azure prend en charge toutes les adresses de messagerie mais ne vérifie pas si elles sont valides. Vous devez donc vous assurer qu'il n'y a aucune faute de frappe.

## <a name="check-on-your-alerts"></a>Vérifier vos alertes
Une fois que vous avez configuré des alertes, le Centre des comptes les répertorie et vous montre le nombre d'alertes que vous pouvez encore configurer. Pour chaque alerte, vous voyez la date et l'heure d'envoi, s'il s'agit d'une alerte relative au total facturé ou au crédit monétaire, ainsi que la limite que vous avez configurée. L'heure est au format 24 heures UTC (Universal Time Coordinate) et la date est au format aaaa-mm-jj. Cliquez sur le signe plus pour modifier une alerte listée, ou cliquez sur la poubelle pour la supprimer.

[Image1]: ./media/azure-billing-set-up-alerts/billingalert1.png
[Image2]: ./media/azure-billing-set-up-alerts/billingalert2.png
[Image3]: ./media/azure-billing-set-up-alerts/billingalerts3.png



<!--HONumber=Dec16_HO2-->


