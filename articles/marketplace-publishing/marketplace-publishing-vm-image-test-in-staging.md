<properties
   pageTitle="Testez votre offre de machine virtuelle pour Marketplace | Microsoft Azure"
   description="Découvrez comment tester votre offre de machine virtuelle pour Azure Marketplace."
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
   ms.date="10/09/2015"
   ms.author="hascipio" />

# Test de votre offre de machine virtuelle pour Azure Marketplace en mode intermédiaire

Dans le cadre du déploiement dans un environnement intermédiaire, vous déployez votre référence SKU dans un « bac à sable » (sandbox) privé dans lequel vous pouvez tester et vérifier ses fonctionnalités avant son déploiement dans Marketplace. La référence apparaît avec le statut Intermédiaire, comme pour un client l’ayant déployée. Votre image de machine virtuelle doit être certifiée pour passer en mode Intermédiaire.

## Étape 1 : déployez votre offre dans un environnement intermédiaire

1. Dans l'onglet **Publish**, cliquez sur **Push to Staging**.

  ![dessin](media/marketplace-publishing-vm-image-test-in-staging/vm-image-push-to-staging.png)

2. Si le portail de publication vous signale des erreurs, corrigez-les.
3.	Dans la boîte de dialogue **Who can access your staged offer?**, saisissez la liste des abonnements Azure que vous allez utiliser pour afficher un aperçu de votre offre dans le [portail Azure en version préliminaire](https://portal.azure.com).
4. Connectez-vous au [portail Azure en version préliminaire](https://portal.azure.com) à l'aide de l'un des abonnements Azure répertoriés dans l'étape précédente.
5. Recherchez votre offre et validez vos points d'image de machine virtuelle :
  1. Assurez-vous que le contenu marketing s’affiche correctement sur Marketplace.

      ![img-map-portal](media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg)

  2. Déploiement de bout en bout de l'image de machine virtuelle.

> [AZURE.IMPORTANT] Votre offre restera en mode Intermédiaire jusqu'à ce que vous informiez Microsoft via le portail de publication [**Publish** > **« Request Approval to Push to Production »**] que vous êtes prêt à lancer la production. À ce stade, il est souhaitable que les membres de votre équipe procèdent à des vérifications en vue de la préparation de la publication de votre offre.

> La réplication dans les centres de données peut prendre jusqu’à 48 heures. Une fois la réplication terminée, votre offre sera répertoriée dans [Azure Marketplace](https://azure.microsoft.com/marketplace/).

## Étapes suivantes
Maintenant que votre offre est en mode Intermédiaire et que vous avez testé sa fonctionnalité et son contenu marketing, vous pouvez passer à la phase de publication finale, **Étape 4** : [déploiement de votre offre sur Marketplace](marketplace-publishing-push-to-production.md)

## Voir aussi
- [Mise en route : publication d’une offre dans Azure Marketplace](marketplace-publishing-getting-started.md)

<!---HONumber=AcomDC_0128_2016-->