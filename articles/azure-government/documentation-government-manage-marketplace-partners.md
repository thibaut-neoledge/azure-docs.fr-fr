---
title: "Place de marché Azure pour les partenaires Azure Government | Microsoft Docs"
description: "Cet article propose une comparaison de fonctionnalités et des conseils pour le développement d’applications pour Azure Government."
services: azure-government
cloud: gov
documentationcenter: 
author: tsingh
manager: asimm
ms.assetid: 7790d3c5-d0fa-4662-b4f0-a174cb7d6c9f
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 11/14/2016
ms.author: zakramer;tsingh;divacc
translationtype: Human Translation
ms.sourcegitcommit: 0839e8e57b2149e50d4512d28b1e57e6592be458
ms.openlocfilehash: 70821864520ff18ba8c64be0ea66376bccee7148


---
# <a name="azure-marketplace-for-azure-government"></a>Place de marché Azure pour Azure Government
Si vous êtes un partenaire Azure Government et que vous voulez publier des offres sur la Place de marché Azure, vous trouverez les détails dans cet article.

Actuellement, les images de machines virtuelles BYOL et les modèles de solutions sont pris en charge dans la Place de marché Azure Government. La meilleure pratique est de vérifier avant publication sur Azure que les modèles de solutions Government fonctionnent bien sur le cloud public Azure et le cloud Azure Government. Si vous publiez uniquement une Image de machine virtuelle, vous pouvez passer aux étapes de publication plus bas.

## <a name="pre-publishing-validation-for-solution-templates"></a>Validation avant la publication de modèles de solutions

Avant de publier votre modèle de solution dans Azure Government, nous vous recommandons de vérifier quelques meilleures pratiques courantes afin de garantir que votre modèle fonctionnera dans le cloud public Azure et dans Azure Government.

1.  Vérifiez les points de terminaison non codés en durs dans votre modèle de solution pour le cloud public Azure. Ils ne sont pas valides pour les autres environnements Azure. Modifiez plutôt le modèle de solution de façon à demander un appel d’API afin d’extraire le point de terminaison valide :  

  Exemple :

  URI de disque dur virtuel (codé en dur) "uri": "[concat(’https://’, variables(’nomCompteStockage’), ’.blob.core.windows.net/’,  ’/osdisk.vhd’)]",

  URI de disque dur virtuel correct (référencé)

  "uri": "[concat(reference(resourceId(’Microsoft.Storage/storageAccounts/’, variables(’nomCompteStockage’))).primaryEndpoints.blob, ’osdisk.vhd’)]",

  La syntaxe corrigée garantit que le modèle fonctionne sur n’importe quel cloud (Gov, public Azure, AzureStack, Chine, etc.).

2.  Vérifiez que les ressources utilisées dans votre modèle de solution sont disponibles dans le cloud souverain sur lequel vous effectuez la publication.
RP dans Azure et version d’API

    Identifiez la disponibilité Azure Government à l’aide de l’Explorateur de ressources dans le portail :

  1.    Connectez-vous au Portail Azure Government
  2.    Lancez l’Explorateur de ressources en suivant les étapes listées ici : https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-supported-services#supported-regions.

  Extensions les plus fréquemment utilisées ; Disponibilité dans Fairfax  

  | Serveur de publication / Type | Versions disponibles dans Fairfax |
  | --- | --- |
  | Microsoft.OSTCExtensions/CustomScriptForLinux | 1; 1.1; 1.2.2.0; 1.3.0.2; 1.4.1.0; 1.5.2.0 |
  | Microsoft.Compute/CustomScriptExtension | 1.2; 1.3; 1.4; 1.7; 1.8 |
  | Microsoft.Azure.Extensions/DockerExtension | Non disponible |
  | Microsoft.Azure.Extensions/CustomScript | 2.0.2 |
  | Microsoft.OSTCExtensions/LinuxDiagnostic | 2.0.9005; 2.1.9005; 2.2.9005; 2.3.9011 |
  | Microsoft.Powershell/DSC | 2.19.0.0 |

> [!NOTE]
> Si vous insérez des emplacements pour obtenir la liste des valeurs autorisées, vous devrez la mettre à jour régulièrement, au fur et à mesure de l’ajout de nouvelles régions.  


## <a name="publishing"></a>Publication
> [!NOTE]
> Si vous n’êtes pas déjà partenaire certifié de la Place de marché Azure, suivez les étapes qui indiquent comment [publier et gérer une offre](../marketplace-publishing/marketplace-publishing-getting-started.md) avant de continuer.
>
>

### <a name="step-1"></a>Étape 1 :
Connectez-vous à la [Publication Azure](https://publish.windowsazure.com).

### <a name="step-2"></a>Étape 2 :
Cliquez sur l’offre que vous voulez publier.

### <a name="step-3"></a>Étape 3 :
Cliquez sur **SKU**, puis sélectionnez la zone **Azure Government Cloud**.

> [!NOTE]
> Seules les SKU BYOL (apportez votre propre licence) sont prises en charge.  Cette option n’est pas disponible pour les SKU à paiement à l’utilisation (retenues à la source).
>
>

![Page de l’offre à laquelle se trouvent les options Références SKU et Cloud Azure Government](./media/government-manage-marketplace-partner-1.png)

### <a name="step-4"></a>Étape 4
Cliquez sur le lien **+ Add Certification** (Ajouter une certification) pour ajouter des liens vers d’éventuelles certifications de votre offre.

![Page de l’offre avec lien Ajouter une certification](./media/government-manage-marketplace-partner-2.png)

### <a name="step-5"></a>Étape 5
Pour tester votre image dans le portail de publication, demandez un [Compte d’essai](https://azuregov.microsoft.com/trial/azuregovtrial) de Microsoft Azure Government.

Après une vérification de votre éligibilité en tant que partenaire servant des entités fédérales, d’État ou locales aux États-Unis, une confirmation est envoyée par e-mail.  Votre compte d’évaluation sera disponible dans un délai compris entre trois et cinq jours ouvrés.

### <a name="step-6"></a>Étape 6
Cliquez sur **Publish** (Publier), puis sur **Push to Staging** (Déployer dans un environnement intermédiaire).

![Options Publier et Déployer dans un environnement intermédiaire](./media/government-manage-marketplace-partner-3.png)

Vous êtes invité entrer un abonnement figurant dans la liste approuvée qui a accès à l’offre intermédiaire. Entrez l’ID d’abonnement à partir de votre nouveau compte d’évaluation Azure Government.

![Invite où vous spécifiez les ID d’abonnements qui peuvent accéder à l’offre](./media/government-manage-marketplace-partner-4.png)

### <a name="step-7"></a>Étape 7
Une fois l’offre transférée avec succès, vous pouvez tester votre image en vous connectant au [Portail Azure](https://portal.azure.us) à l’aide de votre compte d’essai Azure Government.

### <a name="step-8"></a>Étape 8
Une fois que vous avez validé votre image à l’aide de l’abonnement d’évaluation, vous pouvez activer l’offre en cliquant sur **Publish** (Publier) et en demandant une approbation pour passer en mode de production.

![Commande Demander l’approbation pour passer en mode de production](./media/government-manage-marketplace-partner-5.png)

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des informations supplémentaires et des mises à jour, inscrivez-vous au [blog Microsoft Azure Government](https://blogs.msdn.microsoft.com/azuregov/).



<!--HONumber=Dec16_HO3-->


