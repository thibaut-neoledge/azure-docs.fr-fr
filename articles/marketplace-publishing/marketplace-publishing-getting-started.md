<properties
   pageTitle="Présentation de la procédure de création et de déploiement d’une offre dans le Marketplace | Microsoft Azure"
   description="Découvrez les étapes nécessaires pour devenir un développeur Microsoft approuvé, et pour créer et déployer une image de machine virtuelle, un modèle, un service de données ou un service de développement dans Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/05/2016"
   ms.author="hascipio" />

# Comment publier et gérer une offre dans Azure Marketplace
Cet article a pour but d’aider les développeurs à créer, déployer et gérer leurs solutions répertoriées sur Azure Marketplace afin de la proposer à d’autres clients et partenaires Azure.

La première étape en tant qu’éditeur est de définir le type de solution offert par votre entreprise. Azure Marketplace prend en charge plusieurs solutions. Leur publication dans le Marketplace demande des étapes légèrement différentes.

## Types d’offres
|Type de l’offre| Définition |
|---|---|
|Image de machine virtuelle | Image de machine virtuelle préconfigurée dans laquelle un système d’exploitation et une ou plusieurs applications sont entièrement installés. Les offres relatives à l’image de machine virtuelle peuvent comporter une seule ou plusieurs images de machine virtuelle reliées les unes aux autres par un modèle de solution. Une image de machine virtuelle (« Image ») fournit les informations nécessaires pour créer et déployer des machines virtuelles dans le service Azure Virtual Machines. Une Image se compose d’un disque dur virtuel contenant un système d’exploitation et éventuellement de disques durs virtuels de disque de données. Les Clients peuvent déployer un nombre quelconque de machines virtuelles à partir d’une seule Image.|
|Service de développement| Services intégralement gérés destinés à aider les travailleurs de l’information, les analystes d’entreprise, les développeurs et les professionnels de l’informatique dans leurs tâches de gestion système ou de développement d’applications client. Les fonctions des services de développement permettent aux clients de développer rapidement des applications à l’échelle du cloud sur Azure. Les clients doivent disposer d’un abonnement Azure pour pouvoir acheter des services de développement. Il est de la responsabilité des éditeurs de mesurer l’utilisation des services de développement par les clients et d’adresser les rapports connexes à Microsoft, tel que mentionné dans le contrat d’éditeur de Microsoft Azure Marketplace.|
|Modèle de solution|Un « Modèle de solution Azure Resource Manager (ARM) » est une structure de données capable de référencer plusieurs offres distinctes, y compris des offres publiées par d’autres éditeurs, afin de permettre aux clients Azure de déployer une ou plusieurs offres de manière unique et coordonnée.|

Certaines étapes sont communes aux différents types de solutions. Cet article présente brièvement la procédure que vous devez suivre pour tous les types de solutions.

## 1\. Conditions préalables

> [AZURE.NOTE] Avant de commencer à publier dans Azure Marketplace, vous devez être pré-approuvé. Ce n’est pas le cas pour les éditeurs de service de données.

1. [Demander l’approbation préalable certifiée de Microsoft Azure](marketplace-publishing-azure-certification.md)
2. [Créer et inscrire un compte de développeur Microsoft](marketplace-publishing-accounts-creation-registration.md)
3. [Remplir les conditions préalables non techniques](marketplace-publishing-pre-requisites.md)

## 2\. Publication de votre offre
### 2\.1 Remplir les conditions préalables techniques propres à l’offre
- [Conditions préalables techniques des machines virtuelles](marketplace-publishing-vm-image-creation-prerequisites.md)
- [Conditions préalables techniques des modèles de solution](marketplace-publishing-solution-template-creation-prerequisites.md)

### 2\.2 : Créer votre offre
1. Créez votre offre à l’aide des guides ci-dessous.
    - [Créer votre offre de machine virtuelle](marketplace-publishing-vm-image-creation.md)
    - [Créer votre offre de modèle de solution](marketplace-publishing-solution-template-creation.md)
2. [Créer votre offre de contenu marketing](marketplace-publishing-push-to-staging.md)

### 2\.3 Tester votre offre dans un environnement intermédiaire
- [Tester votre offre de machine virtuelle dans un environnement intermédiaire](marketplace-publishing-vm-image-test-in-staging.md)
- [Tester votre offre de modèle de solution en mode intermédiaire](marketplace-publishing-solution-template-test-in-staging.md)

### 2\.4 Déployer votre offre sur Marketplace
- [Déploiement de votre offre sur Azure Marketplace](marketplace-publishing-push-to-production.md)

### Propre à l’image de machine virtuelle ###
- [Création d’une image de machine virtuelle locale](marketplace-publishing-vm-image-creation-on-premise.md)
- [Création d’une machine virtuelle exécutant Windows dans le portail Azure en version préliminaire](../virtual-machines/virtual-machines-windows-hero-tutorial.md)


- [Résolution des problèmes de publication courants dans le Marketplace](marketplace-publishing-support-common-issues.md)
- Pour en savoir plus sur les portails utilisés, voir [Portails dont vous avez besoin](marketplace-publishing-portals.md).


## 3\. Gestion postérieure à la publication de votre offre
- [Guide de post-production pour les offres de machine virtuelle](marketplace-publishing-vm-image-post-publishing.md)
- [Comment mettre à jour les détails non techniques d’une offre ou d’une référence (SKU)](marketplace-publishing-vm-image-post-publishing.md#2-how-to-update-the-non-technical-details-of-an-offer-or-a-sku)
- [Comment supprimer une offre ou une référence (SKU) d’Azure Marketplace](marketplace-publishing-vm-image-post-publishing.md#4-how-to-delete-a-live-offer-or-sku-from-the-azure-marketplace)
- [Modification de « l'offre incitative revendeurs » des fournisseurs de solutions Cloud](marketplace-publishing-csp-incentive.md)
- [Présentation des rapports Informations du vendeur](marketplace-publishing-report-seller-insights.md)
- [Présentation de votre rapport Revenus](marketplace-publishing-report-payout.md)
- [Obtenir de l’aide en tant qu’éditeur](marketplace-publishing-get-publisher-support.md)

## Ressources supplémentaires
- [Configuration d’Azure PowerShell](marketplace-publishing-powershell-setup.md)

<!---HONumber=AcomDC_0706_2016-->