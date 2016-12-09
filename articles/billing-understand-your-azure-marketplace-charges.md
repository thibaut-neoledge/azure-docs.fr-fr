---
title: "Présentation de vos frais de service externe Azure | Microsoft Docs"
description: "En savoir plus sur la facturation des frais des services externes, anciennement appelés Marketplace, dans Azure."
services: 
documentationcenter: 
author: adpick
manager: felixwu
editor: 
tags: billing
ms.assetid: 5e0e2a3c-d111-4054-8508-0c111c1b749b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2016
ms.author: adpick
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 45e0d553179b63de2d0664314271472f1151d73f


---
# <a name="understand-your-azure-external-service-charges"></a>Présentation de vos frais de service externe Azure
Cet article explique la facturation des services externes dans Azure. Les services externes étaient auparavant appelés commandes Marketplace. Les services externes sont fournis par des fournisseurs de services indépendants, mais sont entièrement intégrés dans l’écosystème Azure. Découvrez comment :

* Identifier les services externes
* Comprendre comment la facturation diffère des autres ressources Azure
* Afficher et effectuer le suivi des coûts liés à l’utilisation des services externes
* Gérer les commandes de service externe et le mode de paiement

## <a name="what-are-azure-external-services"></a>Que sont les services externes Azure ?
Les services externes étaient auparavant appelés Azure Marketplace. En règle générale, il s’agit de services publiés par des tiers et disponibles pour Azure. Par exemple, ClearDB et SendGrid sont des services externes que vous pouvez acheter dans Azure, mais ils ne sont pas publiés par Microsoft.

### <a name="identify-external-services"></a>Identifier les services externes
Lorsque vous configurez un nouveau service externe ou une ressource, un avertissement s’affiche :

![Avertissement d’achat Marketplace](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

> [!NOTE]
> Les services externes sont publiés par des entreprises autres que Microsoft, mais parfois des produits Microsoft sont également classés en tant que services externes.
> 
> 

### <a name="external-services-are-billed-separately"></a>Les services externes sont facturés séparément
Les services externes sont traités comme des commandes individuelles au sein de votre abonnement Azure. La période de facturation pour chaque service est définie lorsque vous achetez le service. À ne pas confondre avec la période de facturation de l’abonnement sous lequel vous l’avez acheté. Vous recevez également des factures distinctes et votre carte de crédit est facturée séparément.

### <a name="each-external-service-has-a-different-billing-model"></a>Chaque service externe possède un modèle de facturation différent
Certains services sont facturés selon un mode de paiement à l’utilisation, tandis que d’autres utilisent un modèle de paiement mensuel. Les services externes Azure nécessitent une carte de crédit. Vous ne pouvez pas acheter des services externes avec un paiement par facture.

### <a name="you-cant-use-monthly-free-credits-for-external-services"></a>Vous ne pouvez pas utiliser de crédits mensuels gratuits pour les services externes
Si vous utilisez un abonnement Azure incluant des [crédits gratuits](https://azure.microsoft.com/pricing/spending-limits/), ceux-ci ne peuvent pas s’appliquer aux factures de services externes. Utilisez une carte de crédit pour acheter des services externes.

## <a name="view-external-service-spending-and-history"></a>Afficher les dépenses et l’historique du service externe
Vous pouvez afficher la liste des services externes dans chaque abonnement dans le [portail Azure](https://portal.azure.com/) : 

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur de compte.
2. Dans le menu Hub, sélectionnez **Abonnements**.
   
    ![Sélection des abonnements dans le menu Hub](./media/billing-understand-your-azure-marketplace-charges/sub-button.png) 
3. Dans le panneau **Abonnements**, sélectionnez l’abonnement que vous souhaitez consulter, puis sélectionnez **Services externes**.
   
    ![Sélectionnez un abonnement dans le panneau Facturation](./media/billing-understand-your-azure-marketplace-charges/select-sub-external-services.png)
4. Chaque commande de service externe doit s’afficher avec le nom de l’éditeur, le niveau de service acheté, le nom donné à la ressource et le statut actuel de la commande. Sélectionnez un service externe pour afficher les factures précédentes.
   
    ![Sélectionnez un service externe](./media/billing-understand-your-azure-marketplace-charges/external-service-blade2.png)
5. De là, vous pouvez afficher les montants des factures précédentes avec la répartition des taxes.
   
    ![Afficher l’historique de facturation des services externes](./media/billing-understand-your-azure-marketplace-charges/billing-overview-blade.png)

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
Si vous souhaitez annuler votre commande de service externe, vous devez supprimer la ressource dans le [portail Azure](https://portal.azure.com).

![Supprimer la ressource](./media/billing-understand-your-azure-marketplace-charges/deleteMarketplaceOrder.PNG)

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contactez le support technique.
Si vous avez d’autres questions, veuillez [contacter le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.




<!--HONumber=Nov16_HO3-->


