<properties
   pageTitle="Présentation de la procédure de création et de déploiement d’une offre dans le Marketplace | Microsoft Azure"
   description="Découvrez les étapes nécessaires pour devenir un vendeur Microsoft approuvé, et créer et déployer une image de machine virtuelle, un modèle, un service de données ou un service de développement dans Azure Marketplace"
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
   ms.date="11/17/2015"
   ms.author="hascipio" />

# Publier une offre dans Azure Marketplace
Cet article est fourni pour aider les vendeurs à créer et déployer une solution sur Azure Marketplace afin de la proposer à d’autres clients et partenaires Azure.

La première étape en tant qu’éditeur est de définir le type de solution offert par votre entreprise. Azure Marketplace prend en charge plusieurs solutions. Leur publication dans le Marketplace demande des étapes légèrement différentes.

Types de solutions :

- Image de machine virtuelle
- Service de développement
- Service de données
- Modèle de solution

Certaines étapes sont communes aux différents types de solutions. Cet article présente brièvement la procédure que vous devez suivre pour tous les types de solutions.

> [AZURE.NOTE]Avant de commencer à publier dans Azure Marketplace, vous devez être pré-approuvé. Ce n’est pas le cas pour les éditeurs de service de données.

|| Image de machine virtuelle | Service de développement | Service de données | Modèle de solution |
|---|---|---|---|---|
| **Obtenir une préapprobation** | [Microsoft Azure Certified][link-certification] | [Microsoft Azure Certified][link-certification] | n/a | [Microsoft Azure Certified][link-certification] |
| **Étape 1. Inscrire le compte du vendeur** | [Compte de vendeur Microsoft : création et inscription][link-accts] | [Compte de vendeur Microsoft : création et inscription][link-accts] | [Compte de vendeur Microsoft : création et inscription][link-accts] | [Compte de vendeur Microsoft : création et inscription][link-accts] |
|**Étape 2. Créer votre offre**| [Conditions préalables non techniques générales](marketplace-publishing-pre-requisites.md)| [Conditions préalables non techniques générales](marketplace-publishing-pre-requisites.md)| [Conditions préalables non techniques générales](marketplace-publishing-pre-requisites.md)| [Conditions préalables non techniques générales](marketplace-publishing-pre-requisites.md)|
|| [Conditions préalables techniques des machines virtuelles][link-single-vm-prereq] | Conditions préalables techniques des services de développement | Conditions préalables techniques des services de données | [Conditions préalables techniques des modèles de solution](marketplace-publishing-solution-template-creation-prerequisites.md) |
||[Guide de publication des images de machine virtuelle][link-single-vm] | Guide de publication des services de développement | Guide de publication des services de données | [Guide de publication des modèles de solution](marketplace-publishing-solution-template-creation.md) |
|| [Guide du contenu marketing d’Azure Marketplace][link-pushstaging] | [Guide du contenu marketing d’Azure Marketplace][link-pushstaging] | [Guide du contenu marketing d’Azure Marketplace][link-pushstaging] | [Guide du contenu marketing d’Azure Marketplace][link-pushstaging] |
| **Étape 3. Envoyer votre offre dans l’environnement intermédiaire** | [Tester votre offre de machine virtuelle dans l’environnement intermédiaire](marketplace-publishing-vm-image-test-in-staging.md) | Tester votre offre de service de développement dans l’environnement intermédiaire | Tester votre offre de service de données dans l’environnement intermédiaire | [Tester votre modèle de solution dans l’environnement intermédiaire](marketplace-publishing-solution-template-test-in-staging.md) |
| **Étape 4. Déployer votre offre dans le Marketplace** | [Déployer votre offre dans le Marketplace][link-pushprod] | [Déployer votre offre dans le Marketplace][link-pushprod] | [Déployer votre offre dans le Marketplace][link-pushprod] | [Déployer votre offre dans le Marketplace][link-pushprod] |

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

**Service de données** - [Mappage OData de service de données](marketplace-publishing-data-service-creation-odata-mapping.md) - [Nœuds de mappage OData de service de données](marketplace-publishing-data-service-creation-odata-mapping-nodes.md) - [Exemples de mappage OData de service de données](marketplace-publishing-data-service-creation-odata-mapping-examples.md)

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

<!---HONumber=AcomDC_1203_2015-->