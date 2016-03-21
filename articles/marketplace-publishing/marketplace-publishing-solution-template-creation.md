<properties
   pageTitle="Guide de création d’un modèle de solution pour Azure Marketplace | Microsoft Azure"
   description="Instructions détaillées pour créer, certifier et déployer un modèle de solution d’image multimachine virtuelle que d’autres peuvent acheter sur Azure Marketplace."
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
      ms.date="02/06/2016"
      ms.author="hascipio; v-divte" />

# Guide de création d’un modèle de solution pour Azure Marketplace
Une fois l’étape 1 [Création de comptes et inscription][link-acct-creation] exécutée, nous vous avons guidé dans la procédure de création d’un modèle de solution compatible Azure avec la section [Conditions techniques préalables pour créer un modèle de solution](marketplace-publishing-solution-template-creation-prerequisites.md). À présent, nous allons vous guider dans la procédure de création d’un modèle de solution à plusieurs machines virtuelles dans le [Portail de publication][link-pubportal] pour Azure Marketplace.

## Créer votre offre de modèle de solution dans le portail de publication
Accédez à [https://publish.windowsazure.com](http://publish.windowsazure.com). Pour votre première connexion au [portail de publication](https://publish.windowsazure.com/), utilisez le compte avec lequel le profil de vendeur de votre entreprise a été inscrit. Plus tard, vous pourrez ajouter des employés de l’entreprise comme coadministrateurs dans le portail de publication.

### 1\. Sélectionner « Modèles de solution »

  ![drawing][img-pubportal-menu-sol-templ]

### 2\. Créer un modèle de solution

  ![dessin][img-pubportal-sol-templ-new]

### 3\. Démarrer avec les topologies
Un modèle de solution est « parent » de toutes ses topologies. Vous pouvez définir plusieurs topologies dans une offre/un modèle de solution. Quand une offre est envoyée dans l’environnement intermédiaire, toutes ses topologies l’accompagnent. Suivez les étapes ci-dessous pour définir votre offre :
- Créer une topologie : « Identificateur de topologie » est généralement le nom de la topologie pour le modèle de solution. L’identificateur de topologie est utilisé dans l’URL comme indiqué ci-dessous :

  Azure Marketplace : http://azure.microsoft.com/marketplace/partners/{PublisherNamespace}/{OfferIdentifier}{TopologyIdentifier}

  Portail Azure : https://portal.azure.com/#gallery/{PublisherNamespace}.{OfferIdentifier}{TopologyIdentifier}

- Ajouter une nouvelle version.

### 4\. Obtenir la certification des versions de votre topologie
Téléchargez un fichier zip contenant tous les fichiers requis pour configurer cette version particulière de la topologie. Ce fichier zip doit contenir les fichiers suivants :
- Fichiers *mainTemplate.json* et *createUiDefinition.json* dans son répertoire racine.
- Tous les modèles liés et tous les scripts nécessaires.

Après avoir téléchargé le fichier zip, cliquez sur **Demander la certification**. L’équipe de certification Microsoft examine les fichiers et certifie la topologie.

  > [AZURE.TIP] Pendant que vos développeurs se chargent de la création des topologies du modèle de solution et obtiennent leur certification, les services commerciaux, marketing et/ou juridique de votre entreprise se chargent du contenu marketing et juridique.

## Étapes suivantes
Vous avez à présent créé votre modèle de solution et envoyé le fichier zip avec les fichiers nécessaires pour la certification. Vous pouvez donc continuer le processus en suivant les instructions du [Guide du contenu marketing d’Azure Marketplace](marketplace-publishing-push-to-staging.md) avant de préparer votre offre pour les tests dans l’environnement intermédiaire. Vous pouvez également accéder à l’ensemble des articles de publication Marketplace via l’article [Prise en main : publier une offre dans Azure Marketplace](marketplace-publishing-getting-started.md).

Les rubriques suivantes peuvent également vous intéresser :

- Images de machine virtuelle : [À propos des images de machine virtuelle dans Azure](https://msdn.microsoft.com/library/azure/dn790290.aspx)

- Extensions de machine virtuelle : [Présentation de l’agent de machine virtuelle et des extensions de machine virtuelle](https://msdn.microsoft.com/library/azure/dn832621.aspx) et [Fonctionnalités et extensions de machine virtuelle Azure](https://msdn.microsoft.com/library/azure/dn606311.aspx)

- Gestionnaire de ressources Azure : [Création de modèles Azure ARM](../resource-group-authoring-templates/) et [Exemples de modèle ARM simples](https://github.com/rjmax/ArmExamples)

- Limitations des comptes de stockage : [Surveiller les limitations des comptes de stockage](http://blogs.msdn.com/b/mast/archive/2014/08/02/how-to-monitor-for-storage-account-throttling.aspx) et [Premium Storage](../storage/storage-premium-storage.md#scalability-and-performance-targets-whfr-FRing-premium-storage)

[img-pubportal-menu-sol-templ]: media/marketplace-publishing-solution-template-creation/pubportal-menu-solution-templates.png
[img-pubportal-sol-templ-new]: media/marketplace-publishing-solution-template-creation/pubportal-solution-template-new.png
[link-acct-creation]: marketplace-publishing-microsoft-accounts-creation-registration.md
[link-pubportal]: https://publish.windowsazure.com

<!------HONumber=AcomDC_0309_2016-->