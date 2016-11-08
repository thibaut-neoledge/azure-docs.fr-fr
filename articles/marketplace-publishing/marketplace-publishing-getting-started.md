---
title: Présentation de la procédure de création et de déploiement d’une offre dans le Marketplace | Microsoft Docs
description: Découvrez les étapes nécessaires pour devenir un développeur Microsoft approuvé, et pour créer et déployer une image de machine virtuelle, un modèle, un service de données ou un service de développement dans Azure Marketplace.
services: marketplace-publishing
documentationcenter: ''
author: HannibalSII
manager: ''
editor: ''

ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2016
ms.author: hascipio

---
# Comment publier et gérer une offre dans Azure Marketplace
Cet article a pour but d’aider les développeurs à créer, déployer et gérer leurs solutions répertoriées sur Azure Marketplace afin de la proposer à d’autres clients et partenaires Azure.

## Qu’est-ce qu’Azure Marketplace ?
Azure Marketplace est la plateforme sur laquelle un abonné Azure peut trouver des services pour faciliter le développement d’applications et de solutions cloud ou sur site. Utilisez les services [Azure Certified](http://azure.com/certified) comme blocs de construction pour développer rapidement une application ou un service novateur pour votre cœur de métier et d’autres abonnés Azure.

Tout comme un éditeur Azure, Azure Marketplace vous permet de distribuer et de vendre votre solution ou votre service innovant à d’autres développeurs, éditeurs de logiciels indépendants et professionnels de l’informatique qui souhaitent développer rapidement leurs applications cloud et leurs solutions mobiles.

## Types d’offres pris en charge
La première étape en tant qu’éditeur est de définir le type de solution offert par votre entreprise. Azure Marketplace prend en charge trois types d’offres :

* Les **images de machines virtuelles** sont des images préconfigurées dans lesquelles un système d’exploitation et une ou plusieurs applications sont entièrement installés. Une image de machine virtuelle fournit les informations nécessaires pour créer et déployer des machines virtuelles dans le service Azure Virtual Machines.
  
  > [!NOTE]
  > **Par exemple,** tout comme un éditeur Azure, vous avez créé et validé une machine virtuelle avec un service de base de données innovant suffisamment convaincant pour que d’autres abonnés Azure soient disposés à acquérir et déployer cette machine virtuelle dans leur environnement de services cloud.
  > 
  > 
* Les **services aux développeurs** sont des services entièrement gérés à utiliser dans le développement d’applications ou la gestion des systèmes. Ils fournissent des fonctionnalités qui permettent le développement rapide d’applications à l’échelle du cloud sur Azure.
  
  > [!NOTE]
  > **Par exemple,** tout comme un éditeur Azure, vous avez développé un service accessible par API (hébergé sur Azure ou ailleurs) qui fournit des prédictions basées sur des données historiques. Et c’est un service que d’autres abonnés Azure qui conçoivent des solutions peuvent avoir envie d’utiliser. Vous pouvez déployer ce service sur Azure Marketplace de façon que d’autres puissent le trouver, l’acquérir et l’utiliser dans leurs services respectifs.
  > 
  > 
* Le **modèle de solution** est une structure de données capable de référencer un ou plusieurs services Azure distincts, y compris des services publiés par d’autres vendeurs, afin de permettre aux abonnés Azure de déployer une ou plusieurs offres d’une seule façon coordonnée.
  
  > [!NOTE]
  > **Par exemple,** tout comme un éditeur Azure, vous avez regroupé un ensemble de services sur Azure qui accélère le déploiement d’un service cloud sécurisé et à haute disponibilité avec équilibrage de charge en quelques clics. D’autres abonnés Azure pourraient être intéressés par le gain de temps offert par l’acquisition de ce modèle de solution plutôt que d’identifier et de configurer manuellement des services Azure identiques ou similaires.
  > 
  > 

Certaines étapes sont communes aux différents types de solutions. Cet article présente brièvement la procédure que vous devez suivre pour tous les types de solutions.

## 1\. Conditions préalables
> [!NOTE]
> Avant de commencer à publier dans Azure Marketplace, vous devez être [pré-approuvé](http://azure.com/certified).
> 
> 

1. [Demander l’approbation préalable certifiée de Microsoft Azure](marketplace-publishing-azure-certification.md)
2. [Créer et inscrire un compte de développeur Microsoft](marketplace-publishing-accounts-creation-registration.md)
3. [Remplir les conditions préalables non techniques](marketplace-publishing-pre-requisites.md)

## 2\. Publication de votre offre
### 2\.1 Remplir les conditions préalables techniques propres à l’offre
* [Conditions préalables techniques des machines virtuelles](marketplace-publishing-vm-image-creation-prerequisites.md)
* [Conditions préalables techniques des modèles de solution](marketplace-publishing-solution-template-creation-prerequisites.md)

### 2\.2 : Créer votre offre
1. Créez votre offre à l’aide des guides ci-dessous.
   * [Créer votre offre de machine virtuelle](marketplace-publishing-vm-image-creation.md)
   * [Créer votre offre de modèle de solution](marketplace-publishing-solution-template-creation.md)
2. [Créer votre offre de contenu marketing](marketplace-publishing-push-to-staging.md)

### 2\.3 Tester votre offre dans un environnement intermédiaire
* [Tester votre offre de machine virtuelle dans un environnement intermédiaire](marketplace-publishing-vm-image-test-in-staging.md)
* [Tester votre offre de modèle de solution en mode intermédiaire](marketplace-publishing-solution-template-test-in-staging.md)

### 2\.4 Déployer votre offre sur Marketplace
* [Déploiement de votre offre sur Azure Marketplace](marketplace-publishing-push-to-production.md)

### Propre à l’image de machine virtuelle
* [Création d’une image de machine virtuelle locale](marketplace-publishing-vm-image-creation-on-premise.md)
* [Création d’une machine virtuelle exécutant Windows dans le portail Azure en version préliminaire](../virtual-machines/virtual-machines-windows-hero-tutorial.md)
* [Résolution des problèmes de publication courants dans le Marketplace](marketplace-publishing-support-common-issues.md)
* Pour en savoir plus sur les portails utilisés, voir [Portails dont vous avez besoin](marketplace-publishing-portals.md).

## 3\. Gestion postérieure à la publication de votre offre
* [Guide de post-production pour les offres de machine virtuelle](marketplace-publishing-vm-image-post-publishing.md)
* [Comment mettre à jour les détails non techniques d’une offre ou d’une référence (SKU)](marketplace-publishing-vm-image-post-publishing.md#2-how-to-update-the-non-technical-details-of-an-offer-or-a-sku)
* [Comment mettre à jour les détails techniques d’une offre ou d’une référence (SKU)](marketplace-publishing-vm-image-post-publishing.md#1-how-to-update-the-technical-details-of-a-sku)
* [Comment ajouter une nouvelle référence (SKU) à une offre répertoriée](marketplace-publishing-vm-image-post-publishing.md#3-how-to-add-a-new-sku-under-a-listed-offer)
* [Comment modifier le nombre de disques de données pour une référence (SKU) répertoriée](marketplace-publishing-vm-image-post-publishing.md#4-how-to-change-the-data-disk-count-for-a-listed-sku)
* [Comment supprimer une offre répertoriée d’Azure Marketplace](marketplace-publishing-vm-image-post-publishing.md#5-how-to-delete-a-listed-offer-from-the-azure-marketplace)
* [Comment supprimer une référence (SKU) répertoriée d’Azure Marketplace](marketplace-publishing-vm-image-post-publishing.md#6-how-to-delete-a-listed-sku-from-the-azure-marketplace)
* [Comment supprimer la version actuelle d’une référence (SKU) répertoriée d’Azure Marketplace](marketplace-publishing-vm-image-post-publishing.md#7-how-to-delete-the-current-version-of-a-listed-sku-from-the-azure-marketplace)
* [Comment restaurer des prix de vente aux valeurs de production](marketplace-publishing-vm-image-post-publishing.md#8-how-to-revert-listing-price-to-production-values)
* [Comment restaurer un modèle de facturation aux valeurs de production](marketplace-publishing-vm-image-post-publishing.md#9-how-to-revert-billing-model-to-production-values)
* [Comment rétablir le paramètre de visibilité d’une référence (SKU) répertoriée à la valeur de la production](marketplace-publishing-vm-image-post-publishing.md#10-how-to-revert-visibility-setting-of-a-listed-sku-to-the-production-value)
* [Modification de « l'offre incitative revendeurs » des fournisseurs de solutions Cloud](marketplace-publishing-csp-incentive.md)
* [Présentation des rapports Informations du vendeur](marketplace-publishing-report-seller-insights.md)
* [Présentation de votre rapport Revenus](marketplace-publishing-report-payout.md)
* [Obtenir de l’aide en tant qu’éditeur](marketplace-publishing-get-publisher-support.md)

## Ressources supplémentaires
* [Configuration d’Azure PowerShell](marketplace-publishing-powershell-setup.md)

<!---HONumber=AcomDC_0824_2016-->