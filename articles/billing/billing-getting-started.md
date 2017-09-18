---
title: "Éviter les coûts imprévus, gérer la facturation - Azure | Microsoft Docs"
description: "Découvrez comment éviter des frais inattendus sur votre facture Azure. Utilisez les fonctionnalités de gestion et de suivi des coûts pour un abonnement Microsoft Azure."
services: 
documentationcenter: 
author: tonguyen10
manager: tonguyen
editor: 
tags: billing
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/10/2017
ms.author: tonguyen
experimental_id: a2b2579c-cd2e-41
ms.translationtype: HT
ms.sourcegitcommit: eeed445631885093a8e1799a8a5e1bcc69214fe6
ms.openlocfilehash: b172fc1726d2a01ee68aebf8ec03cc1567012ca6
ms.contentlocale: fr-fr
ms.lasthandoff: 09/07/2017

---

# <a name="prevent-unexpected-costs-with-azure-billing-and-cost-management"></a>Éviter les coûts inattendus avec la gestion de la facturation et des coûts dans Azure

Lorsque vous vous inscrivez à Azure, il y a plusieurs choses que vous pouvez faire pour avoir une meilleure idée de vos dépenses. Dans le [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), lorsque vous sélectionnez l’abonnement, vous pouvez voir la répartition des coûts et le taux d’avancement actuels. Vous pouvez également [télécharger vos anciennes factures et vos anciens fichiers d’utilisation détaillée](billing-download-azure-invoice-daily-usage-date.md) pour la plupart des types d’offres. Si vous souhaitez regrouper les coûts associés à des ressources utilisées pour différents projets ou équipes, tournez-vous vers le [balisage des ressources](../azure-resource-manager/resource-group-using-tags.md). Si votre organisation dispose d’un système de création de rapports que vous préférez utiliser, regardez du côté des [API de facturation](billing-usage-rate-card-overview.md). 

Pour plus d’informations sur votre utilisation quotidienne, consultez la page [Comprendre votre facture Microsoft Azure](billing-understand-your-bill.md).

Si votre abonnement s’effectue par l’intermédiaire d’un fournisseur de solutions cloud (CSP) ou Azure Sponsorship, un grand nombre des fonctionnalités présentées dans cet article ne vous concernent pas. Au lieu de cela, nous avons un autre ensemble d’outils que vous pouvez utiliser pour la gestion des coûts. Consultez [Ressources supplémentaires pour les offres EA, CSP et Sponsorship](#other-offers).

Si votre abonnement est un Contrat Entreprise (EA), la préversion publique pour l’affichage de vos coûts dans le portail Azure est maintenant publiée. Vous devez [avoir connaissance de certains éléments](#EA) puisque certaines des fonctionnalités listées dans cet article ne vous concernent pas.   

Si votre abonnement est un essai gratuit, [Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), Azure dans Open (AIO) ou BizSpark, apprenez-en davantage sur les [limites de dépense](#spending-limit) pour éviter que votre abonnement soit désactivé de façon inattendue. 

## <a name="day-0-before-you-add-azure-services"></a>Jour 0 : Avant d’ajouter des services Azure

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Estimez le coût en ligne à l’aide de la calculatrice de prix

Utilisez la [calculatrice de prix](https://azure.microsoft.com/pricing/calculator/) et la [calculatrice du coût total de possession](https://aka.ms/azure-tco-calculator) pour obtenir une estimation du coût mensuel du service qui vous intéresse. Par exemple, le coût d’une machine virtuelle A1 Windows en heures de calcul est estimé à 66,96 USD par mois si vous la laissez s’exécuter en permanence :

![Capture d’écran de la calculatrice de prix montrant que le coût d’une machine virtuelle A1 Windows est estimé à 66,96 USD par mois](./media/billing-getting-started/pricing-calcVM.png)

Pour plus d’informations, consultez le [FAQ sur la tarification](https://azure.microsoft.com/pricing/faq/). Si vous voulez parler directement à quelqu’un de notre équipe, appelez le 1-800-867-1389.

### <a name="check-your-subscription-and-access"></a>Vérifiez votre abonnement et votre accès

L’affichage des coûts nécessite un [accès de niveau abonnement aux informations de facturation](billing-manage-access.md), mais seul l’administrateur de compte peut accéder au [Centre des comptes](https://account.windowsazure.com/Home/Index), modifier les informations de facturation et gérer les abonnements. L’administrateur de compte est la personne qui a effectué le processus d’inscription. Pour plus d’informations, consultez [Ajout ou modification de rôles d’administrateur Azure gérant l’abonnement ou les services](billing-add-change-azure-subscription-administrator.md).

Pour savoir si vous êtes administrateur de compte, accédez à la [page Abonnements du portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) et examinez la liste des abonnements auxquels vous avez accès. Regardez sous **Mon rôle**. S’il est indiqué *Administrateur de compte*, vous disposez bien de tous les droits associés. S’il est indiqué autre chose, par exemple *Propriétaire*, vous ne disposez pas de privilèges complets.

![Capture d’écran de votre rôle dans la vue Abonnements du portail Azure](./media/billing-getting-started/sub-blade-view.PNG)

Si vous n’êtes pas l’administrateur de compte, quelqu’un vous a sans doute octroyé un accès partiel via le [contrôle d’accès en fonction du rôle Azure Active Directory](../active-directory/role-based-access-control-configure.md) (RBAC). Pour gérer les abonnements et modifier les informations de facturation, [identifiez l’administrateur de compte](billing-subscription-transfer.md#whoisaa) et demandez-lui d’effectuer les tâches souhaitées ou de [vous transférer l’abonnement](billing-subscription-transfer.md).

Si votre administrateur de compte ne fait plus partie de votre organisation et vous avez besoin de gérer la facturation, [contactez le support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). 

### <a name="spending-limit"></a> Vérifiez si la limite de dépense est activée

Si votre abonnement utilise des crédits, la limite de dépense est activée pour vous par défaut. De cette manière, lorsque vous dépensez tous vos crédits, votre carte bancaire n’est pas facturée. Pour plus d’informations, consultez la [liste complète des offres Azure et les informations sur la disponibilité de la limite de dépense](https://azure.microsoft.com/support/legal/offer-details/).

Toutefois, si vous atteignez votre limite de dépense, vos services sont désactivés. Cela signifie que vos machines virtuelles sont libérées. Pour éviter toute interruption de service, vous devez désactiver la limite de dépense. Tout dépassement est facturé sur la carte de crédit enregistrée. 

Pour voir si la limite de dépense est activée, accédez à la [vue Abonnements du Centre des comptes](https://account.windowsazure.com/Subscriptions). Une bannière s’affiche si votre limite de dépense est activée :

![Capture d’écran montrant l’avertissement affiché dans le Centre des comptes lorsque la limite de dépense est activée](./media/billing-getting-started/spending-limit-banner.PNG)

Cliquez sur la bannière et suivez les invites pour supprimer la limite de dépense. Si vous n’avez pas entré d’informations de carte bancaire lors de votre inscription, vous devez les saisir pour supprimer la limite de dépense. Pour plus d’informations, consultez [Limite de dépense Azure : fonctionnement et activation ou désactivation](https://azure.microsoft.com/pricing/spending-limits/).

### <a name="set-up-billing-alerts"></a>Définition des alertes de facturation

Configurez des alertes de facturation pour recevoir des e-mails lorsque les coûts d’utilisation dépassent un certain montant. Si vous disposez de crédits mensuels, configurez des alertes pour être averti lorsqu’un montant spécifique a été utilisé. Pour plus d’informations, consultez [Configurer des alertes de facturation pour vos abonnements Microsoft Azure](billing-set-up-alerts.md).

![Capture d’écran d’un e-mail d’alerte de facturation](./media/billing-getting-started/billing-alert.png)

> [!NOTE]
> Cette fonctionnalité étant encore en version préliminaire, il est recommandé de vérifier régulièrement l’utilisation.

Pour configurer votre première alerte, vous pouvez vous baser sur l’estimation du coût obtenue à l’aide de la calculatrice de prix.

### <a name="understand-limits-and-quotas-for-your-subscription"></a>Soyez au fait des limites et des quotas de votre abonnement

Chaque abonnement fait l’objet de limites par défaut en ce qui concerne le nombre de cœurs de processeur et les adresses IP. Soyez attentif à ces limites. Pour plus d’informations, consultez [Abonnement Azure et limites, quotas et contraintes de service](../azure-subscription-service-limits.md). Vous pouvez demander une augmentation de votre limite ou de votre quota en [contactant le support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="day-1-as-you-add-services"></a>Jour 1 : Lorsque vous ajoutez des services

### <a name="review-the-estimated-cost-in-the-portal"></a>Vérifiez l’estimation du coût dans le portail

En général, lorsque vous ajoutez un service dans le portail Azure, une vue présentant une estimation similaire du coût par mois vous est proposée. Par exemple, lorsque vous choisissez la taille de votre machine virtuelle Windows, vous pouvez voir l’estimation du coût mensuel pour les heures de calcul :

![Exemple : le coût d’une machine virtuelle A1 Windows est estimé à 66,96 USD par mois](./media/billing-getting-started/vm-size-cost.PNG)

### <a name="tags"></a> Regrouper vos données de facturation en ajoutant des balises 

Vous pouvez utiliser des balises pour regrouper les données de facturation associées aux services pris en charge. Par exemple, si vous exécutez plusieurs machines virtuelles pour différentes équipes, vous pouvez utiliser des balises pour classer les coûts par centre de coût (RH, marketing, service financier) ou par environnement (production, préproduction, test). 

![Capture d’écran illustrant la configuration de balises dans le portail](./media/billing-getting-started/tags.PNG)

Les balises apparaissent dans les différentes vues des rapports de coûts. Par exemple, elles sont visibles dans votre [vue d’analyse des coûts](#costs) immédiatement et dans le [fichier .csv d’utilisation détaillée](#invoice-and-usage) après votre première période de facturation.

Pour plus d’informations, voir [Organisation des ressources Azure à l’aide de balises](../azure-resource-manager/resource-group-using-tags.md).

### <a name="consider-enabling-cost-cutting-features-like-auto-shutdown-for-vms"></a>Envisagez d’activer les fonctionnalités de réduction des coûts telles que l’arrêt automatique pour les machines virtuelles

Selon votre scénario, vous pouvez configurer l’arrêt automatique de vos machines virtuelles dans le portail Azure. Pour en savoir plus, consultez le billet de blog [Auto-shutdown for VMs using Azure Resource Manager](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) (Arrêt automatique des machines virtuelles à l’aide d’Azure Resource Manager).

![Capture d’écran de l’option d’arrêt automatique dans le portail](./media/billing-getting-started/auto-shutdown.PNG)

L’arrêt automatique n’a pas le même effet que l’arrêt à partir des options d’alimentation d’une machine virtuelle. L’arrêt automatique met un terme au fonctionnement de vos machines virtuelles et les libère pour éviter des frais d’utilisation supplémentaires. Pour plus d’informations, consultez les informations concernant les états des machines virtuelles dans le FAQ sur la facturation pour les [machines virtuelles Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) et les [machines Windows VMs](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).

Pour bénéficier d’autres fonctionnalités de réduction des coûts pour vos environnements de développement et de test, consultez [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

## <a name="cost-reporting"></a> Jour 2 et après : Après avoir utilisé des services, affichez l’utilisation

### <a name="costs"></a> Vérifiez régulièrement la répartition des coûts et le taux d’avancement dans le portail

Une fois que vos services sont actifs, vérifiez régulièrement combien ils vous coûtent. Vous pouvez consulter les dépenses et le taux d’avancement actuels dans le portail. 

1. Accédez à la [page Abonnements du portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Sélectionnez l’abonnement à afficher. Il se peut qu’il n’y en ait qu’un seul.

3. Vous devriez voir la répartition des coûts et le taux d’avancement dans la page contextuelle qui s’affiche. Il se peut que votre offre ne donne pas accès à ces données (un avertissement est alors affiché en haut). Après avoir ajouté un service, comptez un délai de 24 heures pour que les données soient remplies.  
    
    ![Capture d’écran du taux d’avancement et de la répartition des coûts dans le portail Azure](./media/billing-getting-started/burn-rate.PNG)

4. Si vous le souhaitez, vous pouvez épingler la vue à votre tableau de bord.

    ![Capture d’écran de l’épinglage d’une vue au tableau de bord](./media/billing-getting-started/pin.PNG)

5. Cliquez sur **Analyse des coûts** dans la liste figurant sur la gauche pour afficher la répartition des coûts par ressource.

    ![Capture d’écran de la vue de l’analyse des coûts dans le portail Azure](./media/billing-getting-started/cost-analysis.PNG)

6. Vous pouvez filtrer les données en fonction de différentes propriétés : [balises](#tags), groupe de ressources, intervalle de temps, etc. Cliquez sur **Appliquer** pour confirmer les filtres et sur **Télécharger** pour exporter la vue vers un fichier de valeurs séparées par des virgules (.csv).

7. Cliquez sur une ressource pour afficher l’historique des dépenses et savoir combien elle vous a coûté chaque jour.

    ![Capture d’écran de la vue de l’historique des dépenses dans le portail Azure](./media/billing-getting-started/costhistory.PNG)

Nous vous recommandons de comparer les coûts affichés avec les estimations qui vous ont été données lors de la sélection des services. Si vous constatez une différence importante, vérifiez le plan de tarification (machine virtuelle A1 ou A0, par exemple) que vous avez sélectionné pour vos ressources. 

#### <a name="EA"></a> Prévisualiser les vues des coûts du Contrat Enterprise dans le portail Azure 

Les vues des coûts d’entreprise sont actuellement en préversion publique. Éléments à noter :
- Les coûts d’abonnement sont basés sur l’utilisation et ne tiennent pas compte des montants prépayés, dépassements, quantités incluses, ajustements et taxes. Les frais réels sont calculés au niveau de l’inscription. 
- Les montants affichés dans le portail Azure peuvent être différés par rapport aux valeurs indiquées dans le portail d’entreprise.  
- Si vous ne voyez pas les coûts, l’une des raisons suivantes peut expliquer cette situation :
    - Vous êtes propriétaire de compte et votre administrateur en charge de l’inscription a désactivé le paramètre « d’affichage des frais pour l’administrateur de compte ».  Contactez l’administrateur en charge de votre inscription pour obtenir l’accès aux coûts. 
    - Vous êtes administrateur de service et l’administrateur de votre inscription a désactivé le paramètre « d’affichage des frais pour l’administrateur de service ».  Contactez l’administrateur en charge de votre inscription pour obtenir l’accès. 
    - Vous avez acheté Azure par le biais d’un partenaire et ce dernier n’a pas publié d’informations tarifaires.  
- Quand des paramètres liés à l’accès aux coûts sont mis à jour dans le portail d’entreprise, il existe un délai d’attente de quelques minutes avant que les modifications ne soient répercutées dans le portail Azure.
- La limite de dépense, les alertes de facturation et les conseils de facturation ne concernent pas les abonnements EA.

#### <a name="view-costs-for-all-your-subscriptions-in-the-billing-page"></a>Afficher les coûts de tous vos abonnements dans la page Facturation

Si vous gérez plusieurs abonnements en qualité d’administrateur de compte, vous pouvez consulter le montant global facturé et la répartition des coûts pour tous vos abonnements dans la [page Facturation](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade). Les vues globales EA ne figurent pas dans la page Facturation du portail Azure.  

<!-- Add screenshots of multiple subs each with billed usage -->

### <a name="turn-on-and-check-out-azure-advisor-recommendations"></a>Activez et consultez les recommandations d’Azure Advisor

[Azure Advisor](../advisor/advisor-overview.md) est une fonctionnalité en version préliminaire qui vous permet de réduire les coûts en identifiant les ressources peu utilisées. Activez-la dans le portail Azure :

![Capture d’écran du bouton Azure Advisor dans le portail Azure](./media/billing-getting-started/advisor-button.PNG)

Vous pourrez alors accéder à des recommandations exploitables à partir de l’onglet **Coût** du tableau de bord d’Advisor :

![Exemple de recommandation en matière de coûts d’Advisor](./media/billing-getting-started/advisor-action.PNG)

Pour plus d’informations, consultez [Recommandations du conseiller en matière de coût](../advisor/advisor-cost-recommendations.md).

### <a name="invoice-and-usage"></a> Obtenez votre facture et l’utilisation détaillée après votre première période de facturation

Après votre première période de facturation, vous pouvez télécharger votre facture au format PDF et votre utilisation détaillée au format CSV. Vous pouvez aussi choisir de recevoir votre facture par e-mail. Ces fichiers vous permettent de comprendre ce qui vous est finalement facturé après application des taxes, des remises et des crédits. Si vous n’avez défini aucun mode de paiement pour votre abonnement, il se peut que ces fichiers ne soient pas disponibles. Si vous avez un abonnement EA, l’administrateur en charge de l’inscription reçoit la facture par e-mail car elle n’est pas disponible dans le portail Azure. Pour plus d’informations, consultez [Comment obtenir votre facture Azure et vos données d’utilisation quotidienne](billing-download-azure-invoice-daily-usage-date.md) et [Comprendre votre facture pour Microsoft Azure](billing-understand-your-bill.md).

![Capture d’écran d’une facture .pdf](./media/billing-getting-started/invoice.png)

Les balises que vous avez définies précédemment apparaissent dans les fichiers .csv d’utilisation détaillée :

![Capture d’écran montrant les balises dans le fichier .csv d’utilisation](./media/billing-getting-started/csv.png)

### <a name="billing-api"></a>API de facturation

Utilisez nos API de facturation pour obtenir les données d’utilisation par programmation. En associant les API RateCard et Resource Usage, vous pouvez connaître l’utilisation qui vous est facturée. Pour plus d’informations, consultez [Obtenir une vue d’ensemble de votre consommation des ressources Microsoft Azure](billing-usage-rate-card-overview.md).

## <a name="other-offers"></a> Ressources supplémentaires pour les offres EA, CSP et Sponsorship

Contactez votre responsable de compte ou votre partenaire Azure pour commencer.

| Offer | les ressources |
|-------------------------------|-----------------------------------------------------------------------------------|
| Contrat Entreprise (EA) | [Portail EA](https://ea.azure.com/), [documents d’aide](https://ea.azure.com/helpdocs), et [Rapport Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Fournisseur de solutions cloud (CSP) | Contactez votre fournisseur |
| Azure Sponsorship | [Portail Sponsorship](https://www.microsoftazuresponsorships.com/) |

Si vous êtes responsable informatique d’une grande organisation, nous vous recommandons de lire l’article [Structure d’entreprise Azure](../azure-resource-manager/resource-manager-subscription-governance.md) et le document [entreprise livre blanc IT](http://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (téléchargement au format .pdf, disponible en anglais uniquement).

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique

Si vous avez toujours besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.

