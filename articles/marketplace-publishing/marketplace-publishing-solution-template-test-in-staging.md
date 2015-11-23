<properties
   pageTitle="Test de votre offre de modèle de solution pour Marketplace | Microsoft Azure"
   description="Découvrez comment tester votre offre de modèle de solution pour Azure Marketplace."
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
   ms.date="10/08/2015"
   ms.author="hascipio; v-divte" />

# Test de votre offre de modèle de solution en mode Intermédiaire
Dans le cadre du déploiement dans un environnement intermédiaire, vous déployez votre offre dans un « bac à sable » (sandbox) privé dans lequel vous pouvez tester et vérifier ses fonctionnalités avant le lancement de sa production. L'offre apparaît avec le statut Intermédiaire, comme pour un client qui l'aurait déployée. Votre offre **doit être certifiée pour passer en mode Intermédiaire**.

Une fois que votre offre a le statut Intermédiaire, vous pouvez l'afficher et la tester dans le [portail Azure en version préliminaire](https://ms.portal.azure.com/).

Veuillez suivre les étapes ci-dessous pour faire passer votre offre en mode Intermédiaire et réaliser des tests dans le [portail Azure en version préliminaire](https://ms.portal.azure.com/).

1.	Accédez au [portail de publication](https://publish.windowsazure.com)-> sélectionnez l'onglet **Solution Templates (Modèles de solution)** -> votre offre -> **Publish (Publier)** ->Cliquez sur **« Push to Staging (Déployer dans un environnement intermédiaire) »**
2.	Fournissez la liste des abonnements Azure que vous utiliserez pour l'aperçu / le test de votre offre.
3.	Connectez-vous au portail Azure en version préliminaire à l'aide de l'ID d'abonnement avec lequel vous avez déployé votre offre en mode Intermédiaire à l'étape précédente.
4.	Effectuez au moins une série de tests des points mentionnés ci-dessous dans le portail Azure en version préliminaire.
  -	Assurez-vous de l'affichage correct du contenu marketing dans la galerie
  -	Réalisez un déploiement de bout en bout de la topologie
  -	Réalisez des tests de performances et de contrainte
  -	Assurez-vous que votre topologie respecte les meilleures pratiques.

## Étape suivante
Si vous êtes satisfait des résultats, vous pouvez passer à la phase finale de publication de l'offre, **Étape 4**, [Déploiement de votre offre dans Marketplace](marketplace-publishing-push-to-production.md). Sinon, apportez les modifications nécessaires à votre offre et effectuez de nouveau une demande de certification.

> [AZURE.NOTE]En cas de modifications de contenus marketing, la certification n'est pas nécessaire.

## Voir aussi
- [Mise en route : publication d'une offre sur Azure Marketplace](marketplace-publishing-getting-started.md)

<!---HONumber=Nov15_HO3-->