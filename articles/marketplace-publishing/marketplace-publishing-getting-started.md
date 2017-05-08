---
title: "Présentation de la procédure de création et de déploiement d’une offre dans le Marketplace | Microsoft Docs"
description: "Découvrez les étapes nécessaires pour devenir un développeur Microsoft approuvé, et pour créer et déployer une image de machine virtuelle, un modèle, un service de données ou un service de développement dans Azure Marketplace."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 5343bd26-c6e4-4589-85b7-4a2c00bba8ab
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2017
ms.author: hascipio
translationtype: Human Translation
ms.sourcegitcommit: db034a8151495fbb431f3f6969c08cb3677daa3e
ms.openlocfilehash: 49ffe988867ba70df5bd7757a64edc9aa813071d
ms.lasthandoff: 04/29/2017


---
# <a name="how-to-publish-and-manage-an-offer-in-the-azure-marketplace"></a>Comment publier et gérer une offre dans Azure Marketplace
Cet article a pour but d’aider les développeurs à créer, déployer et gérer leurs solutions répertoriées sur Azure Marketplace afin de la proposer à d’autres clients et partenaires Azure.

## <a name="what-is-the-azure-marketplace"></a>Qu’est-ce qu’Azure Marketplace ?
Tout comme un éditeur Azure, Azure Marketplace vous permet de distribuer et de vendre votre solution ou votre service innovant à d’autres développeurs, éditeurs de logiciels indépendants et professionnels de l’informatique qui souhaitent développer rapidement leurs applications cloud et leurs solutions mobiles. Si votre solution s'adresse aux utilisateurs professionnels, vous devriez opter pour le marketplace [AppSource](http://appsource.microsoft.com).


## <a name="supported-types-of-solutions"></a>Types de solutions pris en charge
La première étape en tant qu’éditeur est de définir le type de solution offert par votre entreprise. Azure Marketplace prend en charge les types d’offres suivantes :

|Type de solution|Machine virtuelle|Modèle de solution|
|---|---|---|
|Définition|Images préconfigurées dans lesquelles un système d’exploitation et une ou plusieurs applications sont entièrement installés. Une image de machine virtuelle fournit les informations nécessaires pour créer et déployer des machines virtuelles dans le service Azure Virtual Machines.|Structure de données capable de référencer un ou plusieurs services Azure distincts, y compris des services publiés par d’autres vendeurs, afin de permettre aux abonnés Azure de déployer une ou plusieurs offres d’une seule façon coordonnée.|
|Exemple|**Par exemple,** tout comme un éditeur Azure, vous avez créé et validé une machine virtuelle avec un service de base de données innovant suffisamment convaincant pour que d’autres abonnés Azure soient disposés à acquérir et déployer cette machine virtuelle dans leur environnement de services cloud.|**Par exemple,** tout comme un éditeur Azure, vous avez regroupé un ensemble de services sur Azure qui accélère le déploiement de services cloud avec un équilibrage de charge, une sécurité renforcée et une meilleure disponibilité. Les autres abonnés Azure peuvent gagner du temps en achetant le modèle de solution qui répond à leurs objectifs plutôt que de localiser, acheter, déployer et configurer manuellement des services Azure identiques ou similaires.|

> [!NOTE]
> Notez que certaines étapes sont partagées entre les différents types de solutions tandis que d’autres sont propres à chaque type de solution. Cet article présente brièvement la procédure que vous devez suivre pour tous les types de solutions.

## <a name="how-to-publish-a-solution"></a>Publication d'une application
![dessiner](media/marketplace-publishing-getting-started/img01.png)

### <a name="1-nominate-your-solution-for-pre-approval"></a>1. Nommer votre solution pour une pré-approbation
- Remplissez le formulaire de nomination de solution pour **Microsoft Azure Certified pour les machines virtuelles** [ici](https://createopportunity.azurewebsites.net)

>[!NOTE]
> Si vous travaillez avec un responsable de compte partenaire ou un responsable partenaire DX, demandez-lui de vous recommander pour le programme de certification d'Azure Certified OU accédez à la page Web[Microsoft Azure Certified](http://createopportunity.azurewebsites.net), remplissez le formulaire d’application et entrez l'adresse e-mail de votre responsable de compte partenaire ou responsable partenaire DX dans le champ Microsoft Sponsor Contact.

Si les critères d'éligibilité sont reconnues par les [Politiques concernant la participation à la Place de marché Azure](http://go.microsoft.com/fwlink/?LinkID=526833) et que votre application est approuvée, nous travaillerons à vos côtés pour intégrer votre solution à Azure Marketplace.

### <a name="2-register-your-account-as-a-microsoft-seller"></a>2. Inscrire votre compte en tant que vendeur Microsoft
- [Inscrire votre compte Microsoft en tant que compte Microsoft Developer](marketplace-publishing-accounts-creation-registration.md)

### <a name="3-publish-your-solution"></a>3. Publier votre solution
1. Remplir les conditions préalables non techniques
  - [Remplir les conditions préalables non techniques](marketplace-publishing-pre-requisites.md)
  - [Conditions préalables techniques des machines virtuelles](marketplace-publishing-vm-image-creation-prerequisites.md)
  - [Conditions préalables techniques des modèles de solution](marketplace-publishing-solution-template-creation-prerequisites.md)
2. Créer votre offre
  - [Ordinateur virtuel](marketplace-publishing-vm-image-creation.md)
  - [Modèle de solution](marketplace-publishing-solution-template-creation.md)
3. [Créer votre offre de contenu marketing](marketplace-publishing-push-to-staging.md)
4. Tester votre offre dans un environnement intermédiaire
  - [Tester votre offre de machine virtuelle dans un environnement intermédiaire](marketplace-publishing-vm-image-test-in-staging.md)
  - [Tester votre offre de modèle de solution en mode intermédiaire](marketplace-publishing-solution-template-test-in-staging.md)
5. [Déploiement de votre offre sur Azure Marketplace](marketplace-publishing-push-to-production.md)


### <a name="virtual-machine-image-specific"></a>Propre à l’image de machine virtuelle
* [Création d’une image de machine virtuelle locale](marketplace-publishing-vm-image-creation-on-premise.md)
* [Créer une machine virtuelle exécutant Windows dans le portail Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Création d’une machine virtuelle exécutant Windows dans le portail Azure](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Dépannage des problèmes courants rencontrés lors de la création du disque dur virtuel](marketplace-publishing-vm-image-creation-troubleshooting.md)

## <a name="how-to-manage-your-solution"></a>Gestion de votre solution
* [Guide de post-production pour les offres de machine virtuelle](marketplace-publishing-vm-image-post-publishing.md)
* [Comment mettre à jour les détails non techniques d’une offre ou d’une référence (SKU)](marketplace-publishing-vm-image-post-publishing.md#2-how-to-update-the-non-technical-details-of-an-offer-or-a-sku)
* [Comment mettre à jour les détails techniques d’une offre ou d’une référence (SKU)](marketplace-publishing-vm-image-post-publishing.md#1-how-to-update-the-technical-details-of-a-sku)
* [Comment ajouter une nouvelle référence (SKU) à une offre répertoriée](marketplace-publishing-vm-image-post-publishing.md#3-how-to-add-a-new-sku-under-a-listed-offer)
* [Comment modifier le nombre de disques de données pour une référence (SKU) répertoriée](marketplace-publishing-vm-image-post-publishing.md#4-how-to-change-the-data-disk-count-for-a-listed-sku)
* [Comment supprimer une offre répertoriée d’Azure Marketplace](marketplace-publishing-vm-image-post-publishing.md)
* [Comment supprimer une référence (SKU) répertoriée d’Azure Marketplace](marketplace-publishing-vm-image-post-publishing.md#6-how-to-delete-a-listed-sku-from-the-azure-marketplace)
* [Comment supprimer la version actuelle d’une référence (SKU) répertoriée d’Azure Marketplace](marketplace-publishing-vm-image-post-publishing.md#7-how-to-delete-the-current-version-of-a-listed-sku-from-the-azure-marketplace)
* [Comment restaurer des prix de vente aux valeurs de production](marketplace-publishing-vm-image-post-publishing.md#8-how-to-revert-listing-price-to-production-values)
* [Comment restaurer un modèle de facturation aux valeurs de production](marketplace-publishing-vm-image-post-publishing.md#9-how-to-revert-billing-model-to-production-values)
* [Comment rétablir le paramètre de visibilité d’une référence (SKU) répertoriée à la valeur de la production](marketplace-publishing-vm-image-post-publishing.md#10-how-to-revert-visibility-setting-of-a-listed-sku-to-the-production-value)
* [Modification de « l'offre incitative revendeurs » des fournisseurs de solutions Cloud](marketplace-publishing-csp-incentive.md)
* [Présentation de votre rapport Revenus](marketplace-publishing-report-payout.md)
* [Obtenir de l’aide en tant qu’éditeur](marketplace-publishing-get-publisher-support.md)

## <a name="additional-resources"></a>Ressources supplémentaires
* [Configuration d’Azure PowerShell](marketplace-publishing-powershell-setup.md)

