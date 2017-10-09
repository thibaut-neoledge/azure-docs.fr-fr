---
title: "Signaler les données d’utilisation Azure Stack à Azure | Microsoft Docs"
description: "Découvrez comment configurer les la génération de rapports de données d’utilisation dans Azure Stack."
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: sngun;AlfredoPizzirani
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 695b93a818d3c0e615bb79e0a2861e134b2f5c89
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---

# <a name="report-azure-stack-usage-data-to-azure"></a>Signaler les données d’utilisation Azure Stack à Azure 

Les données d’utilisation, également appelées « données de consommation », représentent la quantité de ressources utilisées. Les systèmes intégrés Azure Stack qui utilisent un modèle de facturation basé sur la consommation doivent signaler les données d’utilisation à Azure. Ces données d’utilisation sont utilisées à des fins de facturation. Il incombe à l’opérateur Azure Stack de configurer son environnement Azure Stack avec la génération de rapports de données d’utilisation.


> [!NOTE]
> Les rapports d’utilisation des données sont nécessaires pour les utilisateurs des systèmes intégrés Azure Stack qui attribuent des licences sous le modèle de paiement à l’utilisation. En revanche, ils sont facultatifs pour les clients qui attribuent des licences sous le modèle de capacité. Pour le Kit de développement Azure Stack, les opérateurs cloud peuvent signaler les données d’utilisation et tester la fonctionnalité, mais les utilisateurs ne sont pas facturés pour l’utilisation. Pour en savoir plus sur la tarification dans la Azure Stack, consultez la page [Comment acheter Azure Stack](https://azure.microsoft.com/overview/azure-stack/how-to-buy/).

## <a name="usage-data-reporting-flow"></a>Flux de génération de rapport des données d’utilisation

Les données d’utilisation sont envoyées d’Azure Stack à Azure via le pont Azure. Dans Azure, le système de commerce traite les données d’utilisation et génère la facture. Une fois la facture générée, le propriétaire de l’abonnement Azure peut l’afficher et la télécharger à partir du [Centre des comptes Azure](https://account.windowsazure.com/Subscriptions). Pour savoir comment Azure Stack est concédé sous licence, reportez-vous au [document d’empaquetage et de tarification d’Azure Stack](https://go.microsoft.com/fwlink/?LinkId=842847&clcid=0x409). 

L’illustration suivante est une représentation graphique du flux de données d’utilisation dans Azure Stack :

![flux de facturation](media/azure-stack-usage-reporting/billing-flow.png)

## <a name="set-up-usage-data-reporting"></a>Configurer la génération de rapports de données d’utilisation

Pour configurer la génération de rapports de données d’utilisation dans Azure Stack, vous devez [inscrire votre instance Azure Stack auprès d’Azure](azure-stack-register.md). Dans le cadre du processus d’inscription, le composant pont Azure d’Azure Stack est configuré pour connecter Azure Stack à Azure. Une fois le pont Azure configuré, il envoie les données d’utilisation suivantes à Azure : 

* **ID du compteur** : ID unique de la ressource consommée.
* **Quantité** : quantité de données d’utilisation de ressources.
* **Emplacement** : emplacement où la ressource Azure Stack actuelle est déployée.
* **URI de ressource** : URI complet de la ressource pour laquelle l’utilisation est signalée. 
* **ID d’abonnement** : ID d’abonnement de l’utilisateur Azure Stack.
* **Heure** : heure de début et de fin des données d’utilisation. Il existe un décalage entre le moment où ces ressources sont consommées dans Azure Stack et celui où les données d’utilisation sont signalées au commerce. Azure Stack agrège les données d’utilisation toutes les 24 heures et le signalement de données d’utilisation au pipeline Commerce dans Azure prend plusieurs heures. Par conséquent, l’utilisation qui se produit peu de temps avant minuit peut apparaître dans Azure le jour suivant.

## <a name="test-usage-data-reporting"></a>Tester la génération de rapports de données d’utilisation 

1. Pour tester la génération de rapports de données d’utilisation, créez quelques ressources dans Azure Stack. Par exemple, vous pouvez créer un [compte de stockage](azure-stack-provision-storage-account.md), une [machine virtuelle Windows Server](azure-stack-provision-vm.md) et une machine virtuelle Linux avec des références (SKU) de base et standard pour voir comment l’utilisation des cœurs est signalée. Les données d’utilisation de différents types de ressources sont signalées sous différents compteurs.  

2. Laissez vos ressources s’exécuter pendant quelques heures. Les informations d’utilisation sont collectées environ une fois par heure. Après avoir été collectées, ces données sont transmises à Azure et traitées dans le système de commerce Azure. Ce processus peut prendre plusieurs heures.  

3. Connectez-vous au [Centre des comptes Azure](https://account.windowsazure.com/Subscriptions) en tant qu’administrateur de compte Azure, puis sélectionnez l’abonnement Azure que vous avez utilisé pour inscrire Azure Stack. Vous pouvez afficher les données d’utilisation Azure Stack et le montant facturé pour chacune des ressources utilisées, comme illustré dans l’image suivante :  

   ![flux de facturation](media/azure-stack-usage-reporting/pricing-details.png)

Pour le Kit de développement Azure Stack, les ressources ne sont pas facturées. Ainsi, le prix affiché est 0,00 $. Pour les systèmes intégrés Azure Stack, le coût réel de chacune de ces ressources est affiché.

## <a name="are-users-charged-for-the-infrastructure-virtual-machines"></a>Les utilisateurs sont-ils facturés pour les machines virtuelles d’infrastructure ?
Non, les données d’utilisation pour certaines machines virtuelles de fournisseur de ressources Azure Stack et d’infrastructure créées pendant le déploiement sont signalées à Azure, mais elles ne sont pas facturées. 

Les utilisateurs sont facturés uniquement pour les machines virtuelles qui sont créées sous les abonnements utilisateur. Ainsi, toutes les charges de travail doivent être déployées sous des abonnements utilisateur pour être conformes au contrat de licence Azure Stack.

## <a name="how-do-i-use-my-existing-windows-server-licenses-in-azure-stack"></a>Comment utiliser mes licences Windows Server existantes dans Azure Stack ? 
Vous pouvez utiliser des licences Windows Server existantes dans Azure Stack. Ce modèle est appelé BYOL (apportez votre propre licence). Le fait d’utiliser les licences que vous possédez déjà évite de générer des compteurs d’utilisation supplémentaires. Pour utiliser vos licences existantes, vous devez déployer les machines virtuelles Windows Server comme décrit dans la rubrique [Avantage hybride pour la licence Windows Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md). 

## <a name="what-azure-meters-are-used-when-reporting-usage-data-in-integrated-systems"></a>Quels sont les compteurs Azure utilisés lors du signalement de données d’utilisation dans les systèmes intégrés ?
* **Compteurs du prix total** : utilisés pour les ressources associées à des charges de travail utilisateur.  
* **Compteurs d’administration** : utilisés pour les ressources d’infrastructure. Ces compteurs ont un prix de zéro dollar.

## <a name="which-subscription-is-charged-for-the-resources-consumed"></a>Quel abonnement est facturé pour la consommation de ressources ?
L’abonnement qui est fourni lors de [l’inscription d’Azure Stack auprès d’Azure](azure-stack-register.md) est facturé.

## <a name="what-types-of-subscriptions-are-supported-for-usage-data-reporting"></a>Quels types d’abonnements sont pris en charge pour la génération de rapports de données d’utilisation ?
Pour les systèmes intégrés Azure Stack, les abonnements Accord Entreprise et CSP sont pris en charge. 

Pour le Kit de développement Azure Stack, les abonnements Accord Entreprise, Paiement à l’utilisation, CSP et MSDN prennent en charge les rapports de données d’utilisation.

## <a name="which-sovereign-clouds-support-usage-data-reporting"></a>Quels sont les clouds souverains qui prennent en charge les rapports de données d’utilisation ?
Dans le Kit de développement Azure Stack, la génération de rapports d’utilisation des données exige que les abonnements soient créés dans le système Azure global. Les abonnements créés dans l’un des clouds souverains (clouds Azure Government, Azure Germany et Azure China) ne peuvent pas être inscrits auprès d’Azure. Ils ne prennent donc pas en charge les rapports de données d’utilisation. 

## <a name="how-can-users-identify-azure-stack-usage-data-in-the-azure-billing-portal"></a>Comment les utilisateurs peuvent-ils identifier les données d’utilisation Azure Stack dans le portail de facturation Azure ?
Les utilisateurs peuvent voir les données d’utilisation Azure Stack dans le fichier des détails d’utilisation. Pour savoir comment obtenir le fichier des détails d’utilisation, reportez-vous à l’article indiquant comment [télécharger le fichier d’utilisation à partir du Centre des comptes Azure](../billing/billing-download-azure-invoice-daily-usage-date.md#download-usage-from-the-account-center-csv). Le fichier des détails d’utilisation contient les compteurs Azure Stack qui identifient le stockage et les machines virtuelles Azure Stack. Toutes les ressources utilisées dans Azure Stack sont signalées dans la zone nommée « Azure Stack ».

## <a name="why-doesnt-the-usage-reported-in-azure-stack-match-the-report-generated-from-azure-account-center"></a>Pourquoi l’utilisation signalée dans Azure Stack ne correspond-il pas au rapport généré à partir du Centre des comptes Azure ?
Il existe un délai entre le moment où les données d’utilisation sont signalées par les API d’utilisation d’Azure Stack et celui où elles sont signalées par le Centre des comptes Azure. Ce décalage est le temps nécessaire pour charger les données d’utilisation d’Azure Stack vers le commerce Azure. En raison de ce décalage, l’utilisation qui se produit peu de temps avant minuit peut apparaître dans Azure le jour suivant. Vous pouvez voir une différence si vous utilisez les [API d’utilisation d’Azure Stack](azure-stack-provider-resource-api.md) et que vous comparez les résultats à l’utilisation indiquée dans le portail de facturation Azure.

## <a name="next-steps"></a>Étapes suivantes

* [API d’utilisation du fournisseur](azure-stack-provider-resource-api.md)  
* [API d’utilisation du locataire](azure-stack-tenant-resource-usage-api.md)
* [Forum Aux Questions sur l’utilisation](azure-stack-usage-related-faq.md)
