---
title: "Comment obtenir votre facture Azure et vos données d’utilisation quotidienne | Microsoft Docs"
description: "Décrit comment obtenir votre facture Azure et vos données d’utilisation quotidienne"
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
ms.date: 02/13/2017
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 4d974590ee7677a4f3fcebd9e2474c99a974f663
ms.openlocfilehash: 60ac57fd22f237b7fe62773486fe46913aca8dd0


---
# <a name="how-to-get-your-azure-billing-invoice-and-daily-usage-data"></a>Comment obtenir votre facture Azure et vos données d’utilisation quotidienne
Vous pouvez choisir de configurer des destinataires supplémentaires qui recevront votre facture Azure par e-mail. Vous pouvez également télécharger votre facture à partir du [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). En outre, vous pouvez télécharger un fichier CSV indiquant l’utilisation dans le [Centre des comptes Azure](https://account.windowsazure.com). Seul l’administrateur de compte est autorisé à obtenir les informations de facturation et d’utilisation. Pour savoir qui est l’administrateur de compte de l’abonnement, consultez la page [Transfert de propriété d’un abonnement Azure - FAQ](billing-subscription-transfer.md#faq).

## <a name="get-your-invoice-over-email-pdf"></a>Obtenir votre facture par e-mail (.pdf)
1. Sélectionnez votre abonnement dans le [panneau des abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Vous devez choisir cette option pour chacun de vos abonnements. Cliquez sur **Factures** pour sélectionner **l’envoi de votre facture par E-mail**. Si vous n’êtes pas l’administrateur de compte, vous ne verrez peut-être pas cette option.

    ![Capture d’écran montrant le processus d’abonnement](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)
    
2. Cliquez sur **Activation** et acceptez les termes du contrat :

    ![Capture d’écran montrant le processus d’abonnement](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)
 
3. Une fois que vous avez accepté le contrat, vous pouvez configurer des destinataires supplémentaires :

    ![Capture d’écran montrant le processus d’abonnement](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)
    
Vous pouvez également accéder au lien ciblé dans votre e-mail de notification mensuel

### <a name="i-cant-access-the-email-settings-blade"></a>Impossible d’accéder au panneau des paramètres des e-mails
* Vous devez être l’administrateur de compte pour configurer ce paramètre. Vous ne savez pas ce que cela signifie ? [En savoir plus ici](billing-add-change-azure-subscription-administrator.md).
* Si vous avez une facture mensuelle sans recevoir d’e-mail, vérifiez que [la communication par e-mail est correctement définie](https://account.windowsazure.com/profile).
* Cette fonctionnalité n’est peut-être pas disponible pour certains abonnements tels que les offres de support, les contrats Entreprise ou Azure dans Open.

## <a name="download-invoice-from-azure-portal-pdf"></a>Télécharger la facture à partir du portail Azure (.pdf)

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur de compte. 
2. Dans le menu Hub, sélectionnez **Abonnements**. 

    ![Capture d’écran qui montre l’option Abonnement](./media/billing-download-azure-invoice-daily-usage-date/submenu.png) 

3. Dans le panneau **Abonnements**, sélectionnez l’abonnement que vous souhaitez consulter, puis sélectionnez **Facturation et utilisation**. 

    ![Capture d’écran qui montre l’option Facturation et utilisation](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png) 

4. Dans le panneau **Facturation et utilisation**, cliquez sur **Télécharger la facture** afin de consulter une copie de votre facture PDF. 

    ![Capture d’écran qui montre les périodes de facturation, l’option de téléchargement et le total des frais pour chaque période de facturation](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

5. Vous pouvez afficher votre utilisation quotidienne en cliquant sur la période de facturation. 

Pour plus d’informations sur votre facture, consultez la page [Comprendre votre facture Microsoft Azure](billing-understand-your-bill.md).

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

Pour plus d’informations sur votre utilisation quotidienne, consultez la page [Comprendre votre facture Microsoft Azure](billing-understand-your-bill.md).

## <a name="a-namenoinvoicea-why-dont-i-see-an-invoice-for-the-last-billing-period"></a><a name="noinvoice"></a> Pourquoi ne puis-je pas voir de facture pour la dernière période de facturation ?
Si vous ne voyez pas de facture, cela peut être pour plusieurs raisons :
- Vous disposez d’un crédit mensuel pour votre abonnement qui n’est pas dépassé ou vous disposez d’un essai gratuit. Aucune facture n’est générée, sauf si avez un solde dû.
- Cela fait moins de 30 jours que vous êtes inscrit à Azure.
- La facture n’est pas encore générée. Patientez jusqu’à la fin de la période de facturation.

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contactez le support technique.
Si vous avez d’autres questions, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.




<!--HONumber=Feb17_HO3-->


