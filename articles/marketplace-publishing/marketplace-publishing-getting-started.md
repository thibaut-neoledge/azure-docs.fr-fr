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
   ms.date="01/25/2016"
   ms.author="hascipio" />

# Publier une offre dans Azure Marketplace
Cet article a pour but d’aider les développeurs à créer et déployer une solution sur Azure Marketplace afin de la proposer à d’autres clients et partenaires Azure.

La première étape en tant qu’éditeur est de définir le type de solution offert par votre entreprise. Azure Marketplace prend en charge plusieurs solutions. Leur publication dans le Marketplace demande des étapes légèrement différentes.

Types de solutions :

- Image de machine virtuelle
- Service de développement
- Service de données
- Modèle de solution

Certaines étapes sont communes aux différents types de solutions. Cet article présente brièvement la procédure que vous devez suivre pour tous les types de solutions.

> [AZURE.NOTE] Avant de commencer à publier dans Azure Marketplace, vous devez être pré-approuvé. Ce n’est pas le cas pour les éditeurs de service de données.

|Image de machine virtuelle |Service de développement | Service de données | Modèle de solution |
|----|----|----|----|----|
|**Obtenir une pré-approbation** | [Microsoft Azure Certified][link-certification] | [Microsoft Azure Certified][link-certification] | n/a | [Microsoft Azure Certified][link-certification] |
|**Étape 1 : Enregistrer votre compte développeur** | [Compte Microsoft Developer : création et enregistrement][link-accts] | [Compte Microsoft Developer : création et enregistrement][link-accts] | [Compte Microsoft Developer : création et enregistrement][link-accts] | [Compte Microsoft Developer : création et enregistrement][link-accts] |
|**Étape 2 : Créer votre offre**| [Conditions préalables non techniques générales](marketplace-publishing-pre-requisites.md)| [Conditions préalables non techniques générales](marketplace-publishing-pre-requisites.md)| [Conditions préalables non techniques générales](marketplace-publishing-pre-requisites.md)| [Conditions préalables non techniques générales](marketplace-publishing-pre-requisites.md)|
|[Conditions préalables techniques des machines virtuelles][link-single-vm-prereq] | Conditions préalables techniques des services de développement | [Conditions préalables techniques des services de données](marketplace-publishing-data-service-creation-prerequisites.md) | [Conditions préalables techniques des modèles de solution](marketplace-publishing-solution-template-creation-prerequisites.md) |
|[Guide de publication de l’image de machine virtuelle][link-single-vm] | Guide de publication de service de développeur | [Guide de publication de service de données](marketplace-publishing-data-service-creation.md) | [Guide de publication de modèles de solution](marketplace-publishing-solution-template-creation.md) |
|[Guide de contenu marketing Azure Marketplace][link-pushstaging] | [Guide de contenu marketing Azure Marketplace][link-pushstaging] | [Guide de contenu marketing Azure Marketplace][link-pushstaging] | [Guide de contenu marketing Azure Marketplace][link-pushstaging] |
|**Étape 3 : Déployer votre offre dans un environnement intermédiaire** | [Tester votre offre de machine virtuelle dans un environnement intermédiaire](marketplace-publishing-vm-image-test-in-staging.md) | Tester votre offre de service de développement dans un environnement intermédiaire | [Tester votre offre de service de données dans un environnement intermédiaire](marketplace-publishing-data-service-test-in-staging.md) | [Tester votre modèle de solution dans un environnement intermédiaire](marketplace-publishing-solution-template-test-in-staging.md) |
|**Étape 4 :Déployer votre offre sur Marketplace** | [Déployer votre offre sur Marketplace][link-pushprod] | [Déployer votre offre sur Marketplace][link-pushprod] | [Déployer votre offre sur Marketplace][link-pushprod] | [Déployer votre offre sur Marketplace][link-pushprod] |

## Support
- [Obtenir de l’aide en tant qu’éditeur][suppt-general]
- [Présentation du rapport Informations du vendeur][suppt-rpt-insights]
- [Présentation du rapport Revenus][suppt-rpt-payouts]
- [Résolution des problèmes de publication courants dans le Marketplace][suppt-common]

## Ressources supplémentaires
- Pour en savoir plus sur les portails utilisés, voir [Portails dont vous avez besoin](marketplace-publishing-portals.md).

**Machines virtuelles**

- [Configuration d’Azure PowerShell](marketplace-publishing-powershell-setup.md)
- [Création d’une image de machine virtuelle locale](marketplace-publishing-vm-image-creation-on-premise.md)
- [Création d’une machine virtuelle exécutant Windows dans le portail Azure en version préliminaire](../virtual-machines-windows-tutorial/)

**Services de données**

- [Mappage OData de service de données](marketplace-publishing-data-service-creation-odata-mapping.md)
- [Nœuds de mappage OData de service de données](marketplace-publishing-data-service-creation-odata-mapping-nodes.md)
- [Exemples de mappage OData de service de données](marketplace-publishing-data-service-creation-odata-mapping-examples.md)

[suppt-general]: marketplace-publishing-get-publisher-support.md
[suppt-rpt-insights]: marketplace-publishing-report-seller-insights.md
[suppt-rpt-payouts]: marketplace-publishing-report-payout.md
[suppt-common]: marketplace-publishing-support-common-issues.md
[link-certification]: marketplace-publishing-azure-certification.md
[link-accts]: marketplace-publishing-accounts-creation-registration.md
[link-single-vm]: marketplace-publishing-vm-image-creation.md
[link-single-vm-prereq]: marketplace-publishing-vm-image-creation-prerequisites.md
[link-multi-vm]: marketplace-publishing-solution-template-creation.md
[link-multi-vm-prereq]: marketplace-publishing-solution-template-creation-prerequisites.md
[link-datasvc]: marketplace-publishing-data-service-creation.md
[link-datasvc-prereq]: marketplace-publishing-data-service-creation-prerequisites.md
[link-devsvc]: marketplace-publishing-dev-service-creation.md
[link-devsvc-prereq]: marketplace-publishing-dev-service-creation-prerequisites.md
[link-pushstaging]: marketplace-publishing-push-to-staging.md
[link-pushprod]: marketplace-publishing-push-to-production.md

<!---HONumber=AcomDC_0204_2016-->