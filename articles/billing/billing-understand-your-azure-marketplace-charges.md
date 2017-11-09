---
title: "Présentation de vos frais de service externe Azure | Microsoft Docs"
description: "En savoir plus sur la facturation des frais des services externes, anciennement appelés Marketplace, dans Azure."
services: 
documentationcenter: 
author: adpick
manager: tonguyen
editor: 
tags: billing
ms.assetid: 5e0e2a3c-d111-4054-8508-0c111c1b749b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/9/2017
ms.author: adpick
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 64bfd8581141001aa6c11ca17ec1af681054f490
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="understand-your-azure-billing-for-external-service-charges"></a>Présentation de la facturation de vos frais de service externe Azure
Les services externes sont publiés par des éditeurs de logiciels tiers sur la Place de marché Microsoft Azure. Par exemple, ClearDB et SendGrid sont des services externes que vous pouvez acheter dans Azure, mais ils ne sont pas publiés par Microsoft.

Lorsque vous configurez un nouveau service externe ou une ressource, un avertissement s’affiche :

![Avertissement d’achat Marketplace](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

> [!NOTE]
> Les services externes sont publiés par des entreprises autres que Microsoft, mais parfois des produits Microsoft sont également classés en tant que services externes.
> 
> 

## <a name="how-external-services-are-billed"></a>Facturation des services externes
- Les services externes sont facturés séparément. Ils sont traités comme des commandes individuelles au sein de votre abonnement Azure. La période de facturation pour chaque service est définie lorsque vous achetez le service. À ne pas confondre avec la période de facturation de l’abonnement sous lequel vous l’avez acheté. Vous recevez également des factures distinctes et votre carte de crédit est facturée séparément.
- Chaque service externe possède un modèle de facturation différent. Certains services sont facturés selon un mode de paiement à l’utilisation, tandis que d’autres utilisent un modèle de paiement mensuel. Les services externes Azure nécessitent une carte de crédit. Vous ne pouvez pas acheter des services externes avec un paiement par facture.
- Vous ne pouvez pas utiliser de crédits mensuels gratuits pour les services externes. Si vous utilisez un abonnement Azure incluant des [crédits gratuits](https://azure.microsoft.com/pricing/spending-limits/), ceux-ci ne peuvent pas s’appliquer aux factures de services externes. Utilisez une carte de crédit pour acheter des services externes.

## <a name="view-external-service-spending-and-history-in-the-azure-portal"></a>Afficher les dépenses et l’historique du service externe dans le portail Azure
Vous pouvez afficher la liste des services externes dans chaque abonnement dans le [portail Azure](https://portal.azure.com/) : 

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur de compte.
2. Dans le menu Hub, sélectionnez **Abonnements**.
   
    ![Sélection des abonnements dans le menu Hub](./media/billing-understand-your-azure-marketplace-charges/sub-button.png) 
3. Dans le panneau **Abonnements**, sélectionnez l’abonnement que vous souhaitez consulter, puis sélectionnez **Services externes**.
   
    ![Sélectionnez un abonnement dans le panneau Facturation](./media/billing-understand-your-azure-marketplace-charges/select-sub-external-services.png)
4. Chaque commande de service externe doit s’afficher avec le nom de l’éditeur, le niveau de service acheté, le nom donné à la ressource et le statut actuel de la commande. Pour afficher les factures précédentes, sélectionnez un service externe.
   
    ![Sélectionnez un service externe](./media/billing-understand-your-azure-marketplace-charges/external-service-blade2.png)
5. De là, vous pouvez afficher les montants des factures précédentes avec la répartition des taxes.
   
    ![Afficher l’historique de facturation des services externes](./media/billing-understand-your-azure-marketplace-charges/billing-overview-blade.png)

## <a name="view-external-service-spending-for-enterprise-agreement-ea-customers"></a>Afficher les dépenses du service externe pour les clients de contrat Entreprise (EA)
Les clients de contrat Entreprise (EA) peuvent voir les dépenses du service externe et télécharger des rapports dans le portail EA. Consultez la page sur [Azure Marketplace pour les clients EA](https://ea.azure.com/helpdocs/azureMarketplace) pour commencer.

## <a name="manage-payment-methods-for-external-service-orders"></a>Gérer les modes de paiement pour les commandes de service externe
Mettez à jour vos modes de paiement pour les commandes de service externe à partir du [Centre des comptes](https://account.windowsazure.com/).

> [!NOTE]
> Si vous avez acheté votre abonnement avec un compte professionnel ou scolaire, vous devez [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour modifier votre mode de paiement.
> 
> 

1. Connectez-vous au [Centre des comptes](https://account.windowsazure.com/) et [accédez à l’onglet **marketplace**](https://account.windowsazure.com/Store)
   
    ![Sélectionnez marketplace dans le centre des comptes](./media/billing-understand-your-azure-marketplace-charges/select-marketplace.png)
2. Sélectionnez le service externe à gérer
   
    ![Sélectionnez le service externe à gérer](./media/billing-understand-your-azure-marketplace-charges/select-ext-service.png)
3. Dans la partie droite de la page, cliquez sur **Modifier le mode de paiement**. Ce lien vous dirige vers un autre portail pour gérer votre mode de paiement.
   
    ![Résumé de la commande](./media/billing-understand-your-azure-marketplace-charges/change-payment.PNG)
4. Cliquez sur **Modifier les informations** et suivez les instructions pour mettre à jour vos informations de paiement.
   
    ![Sélectionnez Modifier les informations](./media/billing-understand-your-azure-marketplace-charges/edit-info.png)

## <a name="cancel-an-external-service-order"></a>Annuler une commande de service externe
Si vous souhaitez annuler votre commande de service externe, supprimez la ressource dans le [portail Azure](https://portal.azure.com).

![Supprimer la ressource](./media/billing-understand-your-azure-marketplace-charges/deleteMarketplaceOrder.PNG)

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contactez le support technique.
Si vous avez d’autres questions, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.

