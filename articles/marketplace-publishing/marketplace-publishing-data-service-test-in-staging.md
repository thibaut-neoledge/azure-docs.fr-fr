---
title: "Tester votre offre de service de données pour Marketplace | Microsoft Docs"
description: "Découvrez comment tester votre offre de service de données pour Azure Marketplace."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: e861bd11-f74d-4d77-b4b5-23fb463644ad
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2016
ms.author: hascipio; avikova
ms.openlocfilehash: 56a8aad7484fed18b74200ffa7acf22363625a15
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2017
---
# <a name="testing-your-data-service-offer-in-staging"></a>Tester votre offre de service de données dans l'emplacement intermédiaire
> [!IMPORTANT]
> **À ce stade, nous n’intégrons plus de nouveaux éditeurs de services de données. Le listing de nouveaux services de données ne sera pas approuvé.** Si vous avez une application SaaS professionnelle à publier sur AppSource, vous trouverez plus d’informations [ici](https://appsource.microsoft.com/partners). Si vous avez une application IaaS ou un service de développement à publier sur Azure Marketplace, vous trouverez plus d’informations [ici](https://azure.microsoft.com/marketplace/programs/certified/).
> 
> 

Après avoir effectué les deux premières étapes ([Création de votre compte de développeur Microsoft](marketplace-publishing-accounts-creation-registration.md) et [Création de votre offre de service de données dans le portail de publication](marketplace-publishing-data-service-creation.md)), vous êtes prêt à proposer votre offre dans Azure Marketplace. Cette rubrique vous guidera dans la première étape appelée « préproduction »

Dans le cadre du déploiement dans un environnement intermédiaire, vous déployez votre offre dans un « bac à sable » (sandbox) privé dans lequel vous pouvez tester et vérifier ses fonctionnalités avant le lancement de sa production. L'offre apparaît avec le statut Intermédiaire, comme pour un client qui l'aurait déployée.

## <a name="step-1-pushing-your-offer-to-staging"></a>Étape 1. Déployer votre offre dans un environnement intermédiaire
Le placement de votre offre dans l'environnement intermédiaire vous permet de tester cette offre avant de la proposer aux futurs abonnés.  Vous pouvez ainsi voir comment votre offre apparaît et comment elle est utilisée par les abonnés à vos données.  

  ![dessin](media/marketplace-publishing-data-service-test-in-staging/step-1.1.png)

1. Connexion au [portail de publication](https://publish.windowsazure.com)
2. Sélectionnez **DATA SERVICES** dans la fenêtre de navigation de gauche
3. Sélectionnez l’offre que vous voulez placer dans l'environnement intermédiaire. L'écran ci-dessus apparaît.
4. Cliquez sur le bouton **PUSH TO STAGING** .  
5. Si des problèmes liés à l'offre doivent être résolus avant que l’offre ne passe dans l'environnement intermédiaire, la liste de ces problèmes s’affichera.  Corrigez-les en cliquant sur chaque élément de la liste. Une fois toutes les corrections effectuées, cliquez de nouveau sur le bouton **PUSH TO STAGING** .

Si votre offre ne présente aucun problème, vous verrez la fenêtre contextuelle ci-dessous.  

Si vous n’avez pas l’intention ou n’être pas habilité à publier votre offre dans le portail Azure (capacité limitée), fermez la fenêtre contextuelle.

Pour tester votre service de données dans le Portail Azure (en plus du portail DataMarket), vous devez utiliser un ID d’abonnement Azure.  Cet ID d’abonnement identifiera le compte autorisé à tester votre offre.  

Coupez et collez votre ID d’abonnement, puis cliquez sur la coche pour continuer.

  ![dessin](media/marketplace-publishing-data-service-test-in-staging/step-1.2.png)

> [!NOTE]
> Ces ID d’abonnement Azure sont uniquement requis pour le test et la gestion intermédiaire dans le [Portail de gestion Azure](https://manage.windowsazure.com). Ils ne sont pas nécessaires pour le test dans Azure Marketplace.
> 
> 

L'écran suivant montre que la publication a lieu en affichant l'icône « En cours » en surbrillance jaune ci-dessous. Le transfert de l’offre vers l’environnement intermédiaire prend entre 10 à 15 minutes.  Si l’opération prend plus de temps, actualisez d’abord votre navigateur (appuyez sur F5 dans Internet Explorer).  Dans les rares cas où votre offre est toujours en cours de transfert vers l'environnement intermédiaire après une heure, cliquez sur le lien de contact pour nous avertir du problème.

  ![dessin](media/marketplace-publishing-data-service-test-in-staging/step-1.3.png)

Une fois le passage vers l’environnement intermédiaire effectué, l'icône « En cours » s’immobilise et passe à l’état « Intermédiaire ».  Vous êtes maintenant prêt à tester votre offre.  

## <a name="step-2-test-your-staged-offer-in-datamarket"></a>Étape 2. Tester votre offre intermédiaire dans DataMarket
Cliquez sur le lien après le texte **« See Your service offer at... »** pour afficher l'écran que verra l'abonné lorsque votre offre est mise en production et visible dans DataMarket.

  ![dessin](media/marketplace-publishing-data-service-test-in-staging/step-2.2.png)

Testez ou vérifiez chacun des 12 éléments marqués ci-dessus pour vous assurer que tous les logos, prix/transactions, textes, images, documentation et liens sont corrects et fonctionnent.  Il s'agit d'un bon moment pour vérifier que toutes les valeurs de test que vous avez entrées lors de la création de votre offre ont bien été remplacées par les valeurs réelles.

1. Logo de l'offre
2. Nom de l’offre
3. Nom de l’éditeur/lien vers le site web de votre entreprise
4. Parcourir les catégories de votre offre
5. Lien vers le support de votre offre pour aider les abonnés
6. Description contextuelle de votre offre
7. Plan de l’offre précisant les détails de la facturation
8. Lien vers le code d'implémentation
9. Exemples d'images illustrant l’utilisation des données de l'offre
10. Métadonnées d'entrée/sortie pour chaque service au sein de l'offre
11. Conditions d'utilisation de l’offre
12. Aperçu des données de l'offre

Enfin, vérifiez que le service fonctionnera dans Datamarket en cliquant sur le lien « Explorer ce jeu de données ».  Une nouvelle fenêtre s'ouvrira dans l'outil que nous appelons « Explorateur de service », vous permettant de prévisualiser les résultats d'une requête sur votre service.  Dans cette fenêtre, vous pouvez entrer les paramètres nécessaires et afficher les résultats d'une requête sur votre service.   Vous y verrez également l'URL de votre requête.  

> [!NOTE]
> Veillez à consulter la description textuelle du service affichée en haut.  Si votre offre se compose de plusieurs appels de services, cliquez sur les onglets en bas pour basculer vers le service suivant afin de le vérifier et de le tester.
> 
> 

## <a name="next-step"></a>Étape suivante
Si vous rencontrez des problèmes et avez besoin d'aide pour les résoudre, veuillez contacter l’ [assistance pour éditeurs Azure](http://go.microsoft.com/fwlink/?LinkId=272975).

Si vous êtes satisfait et prêt à publier votre offre, consultez la documentation [Request Approval to Push To Production](marketplace-publishing-push-to-production.md) (Demander une approbation pour lancer la production).

## <a name="see-also"></a>Voir aussi
* [Mise en route : publication d’une offre dans Azure Marketplace](marketplace-publishing-getting-started.md)

