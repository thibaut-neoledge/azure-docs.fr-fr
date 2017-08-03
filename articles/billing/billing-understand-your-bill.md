---
title: Comprendre votre facture Azure | Microsoft Docs
description: "Apprenez à lire et à comprendre l’utilisation et la facturation de votre abonnement Azure"
services: 
documentationcenter: 
author: tonguyen10
manager: tonguyen
editor: 
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: tonguyen
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 264078587b81d7840fe5976498a91ae4f535b6aa
ms.contentlocale: fr-fr
ms.lasthandoff: 07/08/2017


---
# <a name="understand-your-bill-for-microsoft-azure"></a>Comprendre votre facture Microsoft Azure
Pour comprendre votre facture Azure, comparez-la avec le fichier détaillé sur l’utilisation quotidienne et les rapports de gestion des coûts dans le portail Azure.

Pour télécharger la facture au format PDF et le fichier CSV détaillant l’utilisation quotidienne, consultez [Comment obtenir votre facture Azure et vos données sur l’utilisation quotidienne](billing-download-azure-invoice-daily-usage-date.md). 

Pour les termes et descriptions détaillés de votre facture et de votre fichier détaillé sur l’utilisation quotidienne, consultez [Comprendre les termes indiqués sur votre facture Microsoft Azure](billing-understand-your-invoice.md) et [Comprendre les termes indiqués sur le fichier Microsoft Azure sur l’utilisation détaillée](billing-understand-your-usage.md).

Pour plus d’informations sur les rapports de gestion des coûts, consultez [Gestion des coûts sur le portail Azure](https://docs.microsoft.com/en-us/azure/billing/billing-getting-started).

## <a name="reconcile"></a>Comment m’assurer que les frais indiqués sur ma facture sont corrects ?
Si vous voulez plus de détails sur des frais indiqués sur votre facture, vous disposez de deux options :

### <a name="review-your-invoice-and-compare-the-usage-and-costs-with-the-detailed-usage-csv-file"></a>Passez en revue votre facture et comparez l’utilisation et les coûts avec le fichier CSV sur l’utilisation détaillée.

Le fichier CSV sur l’utilisation détaillée indique vos frais par période de facturation et l’utilisation quotidienne. Pour obtenir le fichier CSV détaillant l’utilisation, consultez [Comment obtenir votre facture Azure et vos données d’utilisation quotidienne](https://docs.microsoft.com/en-us/azure/billing/billing-download-azure-invoice-daily-usage-date).

Vos frais d’utilisation sont affichés au niveau du compteur. Les termes suivants ont la même signification sur la facture et sur le fichier sur l’utilisation détaillée. Par exemple, le cycle de facturation sur la facture équivaut à la période de facturation indiquée dans le fichier sur l’utilisation détaillée.

 | Facture (PDF) | Utilisation détaillée (CSV)|
 | --- | --- |
 |Cycle de facturation | Période de facturation |
 |Nom |Catégorie du compteur |
 |Type |Sous-catégorie du compteur |
 |Ressource |Nom du compteur |
 |Région |Région du compteur |
 |Consommé |Quantité consommée |
 |Inclus |Quantité incluse |
 |Facturable |Quantité de dépassement |

La section Frais d’utilisation de votre facture affiche la valeur totale de chaque compteur consommé pendant la période de facturation. Par exemple, la capture d’écran suivante montre les frais d’utilisation du service Azure Scheduler.

![Frais d’utilisation indiqués sur la facture](./media/billing-understand-your-bill/1.png)

Les mêmes frais sont indiqués dans la section Relevé de votre fichier CSV sur l’utilisation détaillée.

![Frais d’utilisation sur le fichier CSV](./media/billing-understand-your-bill/2.png)

En outre, il existe une section Utilisation quotidienne dans le fichier CSV sur l’utilisation détaillée. Cette section contient des éléments pour chaque jour où le compteur a été consommé. Le total de ces éléments doit correspondre à la quantité consommée pour le compteur pendant la période de facturation.

Pour les sections détaillées, les termes et les descriptions de votre facture : Voir [ici](billing-understand-your-invoice.md).

Pour les sections détaillées, les termes et les descriptions du fichier sur l’utilisation quotidienne détaillée, consultez [cette page](billing-understand-your-usage.md).

### <a name="review-your-invoice-and-compare-with-the-usage-and-costs-in-the-azure-portal"></a>Passez en revue votre facture et comparez-la avec l’utilisation et les coûts indiqués dans le portail Azure.

Le portail Azure peut également vous aider à vérifier vos frais. Il fournit des graphiques sur la gestion des coûts qui offrent une vue d’ensemble rapide de votre utilisation et des frais sur votre facture.

Voici quelques exemples d’outils de gestion des coûts disponibles dans le portail Azure :

![Coût par ressource dans le portail Azure](./media/billing-understand-your-bill/3.png)

Ce graphique fournit les coûts actuels par ressource.

![Tarif des dépenses et prévisions dans le portail Azure](./media/billing-understand-your-bill/4.png)

Ce graphique montre les coûts actuels sur cet abonnement et des prévisions de coûts pour le reste de la période de facturation.

![Vue de l’analyse des coûts dans le portail Azure](./media/billing-understand-your-bill/5.png)

L’analyse des coûts indique l’utilisation au niveau des ressources et compteurs pour différentes périodes de facturation.

Pour en savoir plus, cliquez sur [Éviter les coûts inattendus avec la gestion de la facturation et des coûts dans Azure](billing-getting-started.md#costs).

## <a name="external"></a>Quels sont les frais associés aux services externes ?
Les services externes (également appelés commandes de la Place de marché) sont fournis par les fournisseurs de services indépendants et sont facturés séparément. Les frais ne s’affichent pas sur la facture Azure. Pour en savoir plus, consultez [Présentation de vos frais de service externe Azure](billing-understand-your-azure-marketplace-charges.md).

## <a name="payment"></a>Comment effectuer un paiement ?
Si vous avez configuré une carte de crédit ou une carte bancaire comme mode de paiement, le paiement est effectué automatiquement. Sur votre relevé de carte de crédit, le poste doit indiquer **MSFT Azure**.

Si vous utilisez un [mode de paiement par facture](https://azure.microsoft.com/pricing/invoicing/), envoyez votre paiement à l’endroit indiqué au bas de votre facture. Pour plus d’informations, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="how-do-i-check-the-status-of-a-payment-made-by-credit-card"></a>Comment vérifier le statut d’un paiement effectué par carte de crédit ?
[Créer un ticket de support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour demander l’état de votre paiement. 

## <a name="tips-for-cost-management"></a>Conseils pour la gestion des coûts
- Estimez les coûts à l’aide de la [calculatrice de prix](https://azure.microsoft.com/pricing/calculator/), de la [calculatrice du coût total de possession](https://aka.ms/azure-tco-calculator), et lorsque vous ajoutez un service
- [Définition des alertes de facturation](billing-set-up-alerts.md)
- [Passez en revue régulièrement votre utilisation et vos coûts dans le portail Azure](billing-getting-started.md#costs)

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contactez le support technique. 
Si vous avez besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.

