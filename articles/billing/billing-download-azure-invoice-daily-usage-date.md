---
title: "Téléchargement de votre facture Azure et de vos données d’utilisation quotidienne | Microsoft Docs"
description: "Décrit comment télécharger ou afficher votre facture Azure et vos données d’utilisation quotidienne."
keywords: "facturation, facture, téléchargement de facture, facture Azure, utilisation d’Azure"
services: 
documentationcenter: 
author: genlin
manager: ruchic
editor: 
tags: billing
ms.assetid: 6d568d1d-3bd6-4348-97d0-1098b5fe0661
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: genli
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: c920627e11e801bde88b47fc7c7e128e06f59ea8
ms.lasthandoff: 03/22/2017


---
# <a name="download-or-view-your-azure-billing-invoice-and-daily-usage-data"></a>Télécharger ou afficher votre facture Azure et vos données d’utilisation quotidienne
Vous pouvez télécharger votre facture à partir du [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) ou la faire envoyer par courrier électronique. Pour télécharger votre utilisation quotidienne, accédez au [Centre des comptes Azure](https://account.windowsazure.com). Seul l’administrateur de compte est autorisé à obtenir les informations de facturation et d’utilisation. Pour savoir qui est l’administrateur de compte de l’abonnement, consultez la page [Transfert de propriété d’un abonnement Azure - FAQ](billing-subscription-transfer.md#faq).

## <a name="get-your-invoice-in-email-pdf"></a>Obtenir votre facture par e-mail (.pdf)
Vous pouvez choisir de configurer des destinataires supplémentaires qui recevront votre facture Azure par e-mail. Cette fonctionnalité n’est peut-être pas disponible pour certains abonnements tels que les offres de support, les contrats Entreprise ou Azure dans Open.

1. Sélectionnez votre abonnement dans le [panneau des abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Choisissez cette option pour chacun de vos abonnements. Cliquez sur **Factures** pour sélectionner **l’envoi de votre facture par E-mail**. 

    ![Capture d’écran montrant le processus d’abonnement](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)
    
2. Cliquez sur **Activation** et acceptez les termes du contrat :

    ![Capture d’écran montrant le processus d’abonnement](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)
 
3. Une fois que vous avez accepté le contrat, vous pouvez configurer des destinataires supplémentaires :

    ![Capture d’écran montrant le processus d’abonnement](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)
    
Si vous n’obtenez pas de courrier électronique une fois ces étapes terminées, assurez-vous que votre adresse de messagerie est correcte dans les [préférences de communication sur votre profil](https://account.windowsazure.com/profile).


## <a name="download-invoice-from-azure-portal-pdf"></a>Télécharger la facture à partir du portail Azure (.pdf)

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur de compte. 
2. Dans le menu Hub, sélectionnez **Abonnements**. 

    ![Capture d’écran qui montre l’option Abonnement](./media/billing-download-azure-invoice-daily-usage-date/submenu.png) 

3. Dans le panneau **Abonnements**, sélectionnez l’abonnement que vous souhaitez consulter, puis sélectionnez **Facturation et utilisation**. 

    ![Capture d’écran qui montre l’option Facturation et utilisation](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png) 

4. Dans le panneau **Facturation et utilisation**, cliquez sur **Télécharger la facture** afin de consulter une copie de votre facture PDF. 

    ![Capture d’écran qui montre les périodes de facturation, l’option de téléchargement et le total des frais pour chaque période de facturation](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

5. Vous pouvez afficher votre utilisation quotidienne en cliquant sur la période de facturation. 

Pour plus d’informations sur votre facture, consultez la page [Comprendre votre facture Microsoft Azure](billing-understand-your-bill.md). Pour obtenir de l’aide sur la gestion des coûts, consultez [Éviter les coûts inattendus avec la gestion de la facturation et des coûts dans Azure](billing-getting-started.md).

## <a name="download-usage-from-the-account-center-csv"></a>Télécharger l’utilisation à partir du centre de compte (.csv)
1. Connectez-vous au [Centre des comptes Azure](https://account.windowsazure.com/subscriptions) en tant qu’administrateur de compte.
2. Sélectionnez l’abonnement pour lequel vous voulez obtenir les informations de facturation et d’utilisation.
3. Sélectionnez **HISTORIQUE DE FACTURATION**. 

    ![Capture d’écran qui montre l’option d’historique de facturation](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. Vous pouvez consulter vos relevés des six dernières périodes de facturation, ainsi que la période non facturée en cours. 

    ![Capture d’écran qui montre les périodes de facturation, les options de téléchargement des factures et de l’utilisation quotidienne et le total des frais pour chaque période de facturation](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. Sélectionnez **Afficher la déclaration actuelle** pour voir une estimation de vos frais au moment où l’estimation a été générée. Cette information est uniquement mise à jour quotidiennement et peut ne pas inclure l’ensemble de votre utilisation. Votre facture mensuelle peut différer de l’estimation.

    ![Capture d’écran qui montre l’option Afficher la déclaration actuelle](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![Capture d’écran qui montre l’estimation des frais actuels](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. Sélectionnez **Télécharger l’utilisation** pour télécharger les données d’utilisation quotidienne dans un fichier CSV. Si deux versions sont disponibles, téléchargez la version 2.

    ![Capture d’écran qui montre l’option Télécharger les informations d’utilisation](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

Pour plus d’informations sur votre utilisation quotidienne, consultez la page [Comprendre votre facture Microsoft Azure](billing-understand-your-bill.md). Pour obtenir de l’aide sur la gestion des coûts, consultez [Éviter les coûts inattendus avec la gestion de la facturation et des coûts dans Azure](billing-getting-started.md).

## <a name="noinvoice"></a> Pourquoi ne puis-je pas voir de facture pour la dernière période de facturation ?
Si vous ne voyez pas de facture, cela peut être pour plusieurs raisons :
- Vous disposez d’un crédit mensuel pour votre abonnement qui n’est pas dépassé ou vous disposez d’un essai gratuit. Une facture est générée uniquement lorsque vous avez quelque chose à payer.
- Cela fait moins de 30 jours que vous êtes inscrit à Azure.
- La facture n’est pas encore générée. Patientez jusqu’à la fin de la période de facturation.

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contactez le support technique.
Si vous avez d’autres questions, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.


