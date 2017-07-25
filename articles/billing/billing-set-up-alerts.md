---
title: "Configurer des alertes de facturation ou de crédit pour vos abonnements Azure | Microsoft Docs"
description: Describes how you can set up alerts on your Azure bill so you can avoid billing surprises.
keywords: "alerte de crédit, alerte de facturation"
services: 
documentationcenter: 
author: vikdesai
manager: tonguyen
editor: 
tags: billing
ms.assetid: 9b7b3eeb-cd9d-4690-86a3-51b1e2a8974f
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: vikdesai
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 32a2508997d7f50b0b2989d76d0574951d81a73f
ms.openlocfilehash: a2eb813bc96cd58762ce077d2c0710ed4bc7bde6
ms.contentlocale: fr-fr
ms.lasthandoff: 03/02/2017

---
<a id="set-up-billing-or-credit-alerts-for-your-microsoft-azure-subscriptions" class="xliff"></a>

# Configurer des alertes de facturation ou de crédit pour vos abonnements Microsoft Azure
Si vous êtes l'administrateur de compte d'un abonnement Azure, vous pouvez utiliser le service d'alerte de facturation Azure pour créer des alertes de facturation personnalisées qui vous aident à surveiller et à gérer l'activité de facturation de vos comptes Azure.

Ce service est disponible en version préliminaire. Vous devez l’activer d’abord dans la page Fonctionnalités préliminaires.

<a id="set-the-alert-threshold-and-email-recipients" class="xliff"></a>

## Définir le seuil d'alerte et les destinataires des messages électroniques
1. Visitez [la page fonctionnalités préliminaires](https://account.windowsazure.com/PreviewFeatures) et activez le **service d’alerte de facturation**.

1. Lorsque vous aurez reçu par courrier électronique la confirmation que le service de facturation a été activé pour votre abonnement, rendez-vous sur la [page Abonnements](https://account.windowsazure.com/Subscriptions) du portail des comptes. Cliquez sur l’abonnement à surveiller, puis sur **Alertes**.

    ![Capture d’écran de l’affichage des abonnements dans le Centre de comptes Azure, avec les alertes mises en surbrillance][Image1]

2. Ensuite, cliquez sur **Ajouter une alerte** pour créer votre première alerte. Vous pouvez configurer un total de cinq alertes de facturation par abonnement avec un seuil distinct, et jusqu’à deux destinataires d’e-mail pour chaque alerte.

    ![Capture d’écran de l’affichage des alertes dans lequel pouvez ajouter une alerte][Image2]

3. Quand vous ajoutez une alerte, vous lui donnez un nom unique, vous choisissez un seuil de dépenses, et vous choisissez également les e-mails auxquelles les alertes sont envoyées. Quand vous configurez le seuil, vous pouvez choisir un **Total facturé** ou un **Crédit monétaire** dans la liste **Alerte pour**. Pour un total facturé, une alerte est envoyée quand les dépenses d'abonnement dépassent le seuil. Pour un crédit monétaire, une alerte est envoyée quand les crédits monétaires passent sous la limite. Les crédits monétaires s’appliquent habituellement aux abonnements d’essai gratuit et Visual Studio.

    ![Capture d’écran de la vue d’ajout d’une alerte dans laquelle vous pouvez configurer les destinataires][Image3]

Azure prend en charge toutes les adresses e-mail mais ne vérifie pas si elles sont valides. Vous devez donc vous assurer qu'il n'y a aucune faute de frappe.

<a id="check-on-your-alerts" class="xliff"></a>

## Vérifier vos alertes
Une fois que vous avez configuré des alertes, le Centre des comptes les répertorie et vous montre le nombre d'alertes que vous pouvez encore configurer. Pour chaque alerte, vous voyez la date et l'heure d'envoi, s'il s'agit d'une alerte relative au total facturé ou au crédit monétaire, ainsi que la limite que vous avez configurée. L'heure est au format 24 heures UTC (Universal Time Coordinate) et la date est au format aaaa-mm-jj. Cliquez sur le signe plus pour modifier une alerte listée, ou cliquez sur la poubelle pour la supprimer.

<a id="billing-alerts-for-enterprise-agreement-ea-customers" class="xliff"></a>

## Alertes de facturation pour les clients Contrat Entreprise
Les clients Contrat Entreprise (EA) peuvent recevoir des alertes pour chaque département pour une inscription en définissant des quotas de dépense. Consultez les [quotas de dépense des départements](https://ea.azure.com/helpdocs/departmentSpendingQuotas) dans le portail EA pour commencer.

<a id="learn-more-about-azure-cost-management" class="xliff"></a>

## En savoir plus sur la gestion des coûts dans Azure
- Estimez les coûts à l’aide de la [calculatrice de prix](https://azure.microsoft.com/pricing/calculator/) et de la [calculatrice du coût total de possession](https://aka.ms/azure-tco-calculator), également lorsque vous ajoutez un service.
- [Passez en revue régulièrement votre utilisation et vos coûts dans le portail Azure](billing-getting-started.md#costs).
- Activez les [Recommandations de coûts du conseiller Azure](../advisor/advisor-cost-recommendations.md).

Pour plus d’informations, consultez [le guide de gestion des coûts Azure](billing-getting-started.md).

[Image1]: ./media/azure-billing-set-up-alerts/billingalert1.png 
[Image2]: ./media/azure-billing-set-up-alerts/billingalert2.png
[Image3]: ./media/azure-billing-set-up-alerts/billingalerts3.png 

