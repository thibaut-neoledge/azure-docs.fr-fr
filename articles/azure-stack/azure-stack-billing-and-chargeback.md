---
title: "Facturation des clients et rétrofacturation dans Azure Stack | Microsoft Docs"
description: "Découvrez comment récupérer les informations sur l’utilisation des ressources à partir d’Azure Stack."
services: azure-stack
documentationcenter: 
author: AlfredoPizzirani
manager: byronr
editor: 
ms.assetid: a9afc7b6-43da-437b-a853-aab73ff14113
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: alfredop
ms.openlocfilehash: ea7510c239ee07a9a27f3e682e61a6b08eb5694d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="usage-and-billing-in-azure-stack"></a>Utilisation et facturation dans Azure Stack

L’utilisation représente la quantité de ressources consommées par un utilisateur. Azure Stack collecte des informations d’utilisation pour chaque utilisateur et les utilise pour la facturation. Cet article décrit la façon dont les utilisateurs Azure Stack sont facturés pour l’utilisation de ressources, et l’utilisation des informations de facturation pour l’analytique, la rétrofacturation, etc.

Azure Stack contient l’infrastructure pour collecter et regrouper les données d’utilisation pour toutes les ressources, et pour transférer ces données à Azure Commerce. Vous pouvez accéder à ces données et les exporter vers un système de facturation à l’aide d’un adaptateur de facturation ou vous pouvez les exporter vers un outil décisionnel comme Microsoft Power BI. Après l’exportation, ces informations de facturation sont utilisées pour l’analytique ou elles sont transférées à un système de rétrofacturation.

![Modèle conceptuel d’un adaptateur de facturation connectant Azure Stack à une application de facturation](media/azure-stack-billing-and-chargeback/image1.png)

## <a name="usage-pipeline"></a>Pipeline d’utilisation

Chaque fournisseur de ressources dans Azure Stack émet des données d’utilisation correspondant à l’utilisation des ressources. Le service d’utilisation regroupe régulièrement (toutes les heures ou tous les jours) ces données d’utilisation et les stocke dans la base de données d’utilisation. Les données d’utilisation stockées sont accessibles localement par les utilisateurs et opérateurs Azure Stack à l’aide d’API d’utilisation. 

Si vous avez [inscrit votre instance Azure Stack auprès d’Azure](azure-stack-register.md), Usage Bridge est configuré pour envoyer les données d’utilisation à Azure Commerce. Une fois les données disponibles dans Azure, vous pouvez y accéder à travers le portail de facturation ou les API d’utilisation Azure. Consultez la rubrique [Rapports des données d’utilisation](azure-stack-usage-reporting.md) pour en savoir plus sur les données d’utilisation transmises à Azure. 

L’illustration suivante montre les principaux composants du pipeline d’utilisation :

![Pipeline d’utilisation](media/azure-stack-billing-and-chargeback/usagepipeline.png)

## <a name="what-usage-information-can-i-find-and-how"></a>Quelles informations d’utilisation puis-je trouver et comment ?

Les fournisseurs de ressources Azure Stack, comme Calcul, Stockage et Réseau, génèrent des données d’utilisation toutes les heures pour chaque abonnement. Les données d’utilisation contiennent des informations sur la ressource utilisée, comme le nom de la ressource, l’abonnement utilisé, la quantité utilisée, etc. Pour en savoir plus sur les ressources d’ID de compteurs, consultez l’article [FAQ sur l’API d’utilisation](azure-stack-usage-related-faq.md). 

Une fois les données d’utilisation collectées, elles sont [transmises à Azure](azure-stack-usage-reporting.md) pour générer une facture, qui peut être affichée via le portail de facturation Azure. 

> [!NOTE]
> Les rapports des données d’utilisation ne sont pas nécessaires pour le Kit de développement Azure Stack ni pour les utilisateurs de systèmes intégrés Azure Stack qui gèrent leur licence dans le modèle de capacité. Pour en savoir plus sur la gestion des licences dans Azure Stack, consultez la feuille de données [Packages et prix](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf).

Le portail de facturation Azure affiche les données d’utilisation uniquement pour les ressources facturables. Outre les ressources facturables, Azure Stack capture les données d’utilisation pour un ensemble plus large de ressources, auquel vous pouvez accéder dans votre environnement Azure Stack via des API REST ou PowerShell. Les opérateurs Azure Stack peuvent récupérer les données d’utilisation pour tous les abonnements alors qu’un utilisateur ne peut obtenir que les détails concernant son utilisation.

## <a name="retrieve-usage-information"></a>Récupérer les informations sur l’utilisation

Pour générer les données d’utilisation, vous devez disposer de ressources en cours d’exécution qui utilisent activement le système, comme une machine virtuelle active ou un compte de stockage contenant des données, etc. Si vous ne savez pas si vous avez des ressources en cours d’exécution dans la Place de marché Azure Stack, déployez une machine virtuelle et consultez le panneau d’analyse de la machine virtuelle pour vous assurer qu’elle est en cours d’exécution. Pour afficher les données d’utilisation, utilisez les cmdlets PowerShell suivantes :

1. [Installer PowerShell pour Azure Stack.](azure-stack-powershell-install.md)
2. [Configurer l’environnement PowerShell de l’utilisateur Azure Stack](user/azure-stack-powershell-configure-user.md) ou [Configurer l’environnement PowerShell de l’opérateur Azure Stack](azure-stack-powershell-configure-admin.md) 

3. Pour récupérer les données d’utilisation, utilisez la cmdlet PowerShell [Get-UsageAggregates](/powershell/module/azurerm.usageaggregates/get-usageaggregates) :

   ```powershell
   Get-UsageAggregates -ReportedStartTime "<Start time for usage reporting>" -ReportedEndTime "<end time for usage reporting>" -AggregationGranularity <Hourly or Daily>
   ```

## <a name="next-steps"></a>Étapes suivantes

[Signaler les données d’utilisation Azure Stack à Azure](azure-stack-usage-reporting.md)

[API Utilisation des ressources de fournisseur](azure-stack-provider-resource-api.md)

[API Utilisation des ressources de client](azure-stack-tenant-resource-usage-api.md)

[Questions fréquentes (FAQ) relatives à l’utilisation](azure-stack-usage-related-faq.md)

