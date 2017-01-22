---
title: "Déploiement d’un nouveau service web"
description: "Flux de travail du déploiement d’un service web basé sur ARM"
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondl
editor: 
ms.assetid: a358b04f-0d08-4d50-820e-eeac971854cf
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: v-donglo
translationtype: Human Translation
ms.sourcegitcommit: 201b07536bcee58e2b7102379dff1c1c93c4b675
ms.openlocfilehash: 70db87762757c42790047362ded24263157e6ae7


---
# <a name="deploy-a-new-web-service"></a>Déployer comme un nouveau service web
Microsoft Azure Machine Learning fournit désormais des services web basés sur [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) permettant de choisir de nouvelles options de plan de facturation et de déployer votre service web dans plusieurs régions.

Le flux de travail général pour déployer un service web à l’aide des services web Microsoft Azure Machine Learning est le suivant :

* Créer une expérience prédictive
* la déployer
* choisir son nom
* profil de facturation
* la tester
* l’utiliser.

La figure suivante illustre ce flux de travail.

![Flux de travail du déploiement d’un service web][1]

## <a name="deploy-web-service-from-studio"></a>Déployer un service web à partir de Studio
Pour déployer une expérience en tant que nouveau service web. Connectez-vous à Machine Learning Studio et créez un nouveau service web prédictif. 

**Remarque**: si vous avez déjà déployé une expérience en tant que service web classique, vous ne pouvez pas le déployer en tant que nouveau service web.

Cliquez sur **Exécuter** en bas du canevas de l’expérience, puis sur **Déployer le service web** et **Déployer le service web [nouveau]**. La page de déploiement du Gestionnaire de service web Machine Learning s’ouvre.

## <a name="machine-learning-web-service-manager-deploy-experiment-page"></a>Page d’expérience de déploiement du Gestionnaire de service web Machine Learning
Sur la page de l’expérience de déploiement, entrez le nom du service web.
Sélectionnez un plan de tarification. Si vous disposez d’un plan de tarification existant, vous pouvez le sélectionner. Sinon, vous devez créer un nouveau plan de tarification pour le service. 

1. Dans la liste déroulante **Plan tarifaire**, sélectionnez un plan existant ou choisissez l’option **Select new plan** (Sélectionner un nouveau plan).
2. Dans **Plan Name**, (Nom du plan), tapez un nom qui identifiera le plan sur votre facture.
3. Sélectionnez un des **niveaux de plans mensuels**. Notez que les niveaux de plan s’appliquent par défaut aux plans de votre région et que votre service web est déployé dans cette région.

Cliquez sur **Déployer** pour ouvrir la page Démarrage rapide de votre service web.

## <a name="quickstart-page"></a>Page Démarrage rapide
La page Démarrage rapide du service web vous donne accès et vous fournit des conseils sur les tâches les plus courantes que vous allez effectuer après la création d’un nouveau service web. À partir de là, vous pouvez facilement accéder à la fois à la page **Test** et à la page **Utiliser**.

## <a name="testing-your-web-service"></a>Test de votre service web
Dans la page Démarrage rapide, cliquez sur Tester le service web dans la section sur les tâches courantes.   

Pour tester le service web en tant que service de requête-réponse (RRS) :

* Cliquez sur **Test** dans la barre de menus.
* Cliquez sur **Request-Response**(Requête-réponse).
* Entrez les valeurs appropriées pour les colonnes d’entrée de votre expérience.
* Cliquez sur Tester **requête-réponse**.

Vos résultats apparaîtront sur le côté droit de la page.

Pour tester un service web d’exécution de lots (BES), vous utiliserez un fichier CSV :

* Cliquez sur **Test** dans la barre de menus.
* Cliquez sur **Lot**.
* Sous votre entrée, cliquez sur Parcourir et accédez à votre fichier de données d’exemple.
* Cliquez sur **Test**.

L’état de votre test s’affiche sous **Test Batch Jobs**(Tâches de test de traitement par lots).

## <a name="consuming-your-web-service"></a>Utilisation de votre service web
Lors de leur déploiement en tant que services web, les expériences Azure Machine Learning proposent une API REST, qui peut être exploitée par divers périphériques et plateformes. En effet, l’API REST, très simple, accepte et répond au moyen de messages présentant le format JSON. Le portail Microsoft Azure Machine Learning propose du code que vous pouvez utiliser pour appeler le service web, en R, C# et Python.

Sur la page d’utilisation, vous trouverez :

* La clé API et l’URI pour utiliser le service web dans les applications.
* Des modèles d’application web et Excel pour démarrer le processus d’utilisation.
* Un exemple de code en langage C#, python et R pour commencer.

Pour plus d’informations sur l’utilisation de services web, consultez [Utilisation d’un service web Azure Machine Learning déployé à partir d’une expérience Machine Learning](machine-learning-consume-web-services.md).

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur l’utilisation des services web, consultez :

[Utilisation d’un service web Azure Machine Learning déployé à partir d’une expérience Machine Learning](machine-learning-consume-web-services.md)

[Services web Azure Machine Learning : déploiement et consommation](machine-learning-deploy-consume-web-service-guide.md)

<!--Image references-->
[1]: ./media/machine-learning-webservice-deploy-a-web-service/armdeploymentworkflow.png


<!--links-->



<!--HONumber=Jan17_HO2-->


