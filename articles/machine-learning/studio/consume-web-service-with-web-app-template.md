---
title: "Utilisation d’un service web Machine Learning à l’aide d’un modèle d’application web | Microsoft Docs"
description: "Utilisez un modèle d’application Web dans Azure Marketplace pour exploiter un service Web prédictif dans Azure Machine Learning."
keywords: "service Web, opérationnalisation, API REST, apprentissage automatique"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: e0d71683-61b9-4675-8df5-09ddc2f0d92d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: garye;raymondl
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 1c182403409966923440f359cb2514af7b7df9f3
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---
# <a name="consume-an-azure-machine-learning-web-service-with-a-web-app-template"></a>Utilisation d’un service Web Microsoft Azure Machine Learning à l’aide d’un modèle d’application Web

Une fois vous avez développé votre modèle de prévision et l’avez déployé en tant que service web Azure à l’aide de Machine Learning Studio ou à l’aide d’outils comme R ou Python, vous pouvez accéder au modèle opérationnalisé à l’aide d’une API REST.

Il existe plusieurs moyens d’utiliser l’API REST et d’accéder au service Web. Vous pouvez par exemple écrire une application en C#, R ou Python à l’aide de l’exemple de code généré lors du déploiement du service web (disponible dans le [portail des services web Machine Learning](https://services.azureml.net/quickstart) ou dans Machine Learning Studio, dans le tableau de bord du service web). Vous pouvez également utiliser l’exemple de classeur Microsoft Excel créé en même temps.

Mais le moyen le plus rapide et le plus simple d’accéder à votre service web consiste à utiliser les modèles d’application web disponibles dans [Azure Web App Marketplace](https://azure.microsoft.com/marketplace/web-applications/all/).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="the-azure-machine-learning-web-app-templates"></a>Modèles d’applications Web de Microsoft Azure Machine Learning
Les modèles d’applications Web disponibles dans Azure Marketplace peuvent générer une application Web personnalisée qui connaît les données d’entrée et les résultats attendus de votre service Web. Il vous suffit de donner à l’application Web l’accès à votre service Web et aux données associées, et le modèle fait le reste.

Il existe deux modèles :

* [Modèle d’application Web Azure ML Request-Response Service](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/)
* [Modèle d’application Web Azure ML Batch Execution Service](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)

Chaque modèle crée un exemple d’application ASP.NET, en utilisant l’URI et la clé de l’API correspondant à votre service Web, et le déploie en tant que site Web dans Azure. Le modèle Request-Response (RRS) crée une application Web qui vous permet d’envoyer une seule ligne de données au service Web afin d’obtenir un résultat unique. Le modèle Batch Execution Service (BES) crée une application Web qui vous permet d’envoyer un grand nombre de lignes de données de manière à obtenir plusieurs résultats.

Aucun code n’est nécessaire pour utiliser ces modèles. Vous devez simplement spécifier la clé API et l’URI pour permettre au modèle de générer automatiquement l’application.

Pour obtenir la clé API et l’URI de requête pour un service web :

1. Dans le [portail de services web](https://services.azureml.net/quickstart), pour un nouveau service web, cliquez sur **Services web** en haut. Pour un service web classique, cliquez sur **Services web classiques**.
2. Cliquez sur le service web auquel vous souhaitez accéder.
3. Pour un service web classique, cliquez sur le point de terminaison auquel vous souhaitez accéder.
4. Cliquez sur **Utiliser** en haut.
5. Copiez la clé **primaire** ou **secondaire** et enregistrez-la.
6. Si vous créez un modèle de service de requête-réponse (RRS, Request-Response Service), copiez l’URI de la **requête-réponse** et enregistrez-le. Si vous créez un modèle de service d’exécution de lot (BES, Batch Execution Service), copiez l’URI des **requêtes de lots** et enregistrez-le.


## <a name="how-to-use-the-request-response-service-rrs-template"></a>Comment utiliser le modèle Request-Response Service (RRS)
Procédez comme suit pour utiliser le modèle d’application web RRS (voir schéma ci-dessous).

![Procédure d’utilisation du modèle Web RSS][image1]


<!--    ![API Key][image3] -->

<!-- This value will look like this:
   
        https://ussouthcentral.services.azureml.net/workspaces/<workspace-id>/services/<service-id>/execute?api-version=2.0&details=true
   
    ![Request URI][image4] -->

1. Accédez au [Portail Azure](https://portal.azure.com), **Connexion**, cliquez sur **Nouveau**, recherchez et sélectionnez **Azure ML Request-Response Service Web App**, puis cliquez sur **Créer**. 
   
   * Donnez un nom unique à votre application Web. L’URL de l’application web sera ce nom suivi de `.azurewebsites.net.` Par exemple, `http://carprediction.azurewebsites.net.`
   * Sélectionnez l’abonnement Azure et les services sous lesquels est exécuté votre service Web.
   * Cliquez sur **Create**.
     
     ![Créer une application web][image5]

4. Une fois le déploiement de l’application web terminé, cliquez sur l’ **URL** sur la page des paramètres de l’application web dans Azure, ou entrez l’URL dans un navigateur web. Par exemple, `http://carprediction.azurewebsites.net.`
5. À la première exécution de l’application web, vous êtes invité à renseigner **l’URL de publication de l’API** et la **clé API**.
   Entrez les valeurs que vous avez enregistrées précédemment (**URI de requête** et **Clé API** respectivement).
     
     Cliquez sur **Envoyer**.
     
     ![Entrer l’URI de publication et la clé de l’API][image6]

6. L’application web affiche la page **Configuration de l’application web** avec les paramètres du service web actif. Vous pouvez ici modifier les paramètres utilisés par l’application Web.
   
   > [!NOTE]
   > Une modification des paramètres à ce stade n’affecte que l’application Web concernée. Les paramètres par défaut de votre service Web ne seront pas modifiés. Par exemple, si vous modifiez ici la **Description** , l’opération n’aura aucun effet sur la description affichée sur le tableau de bord du service web dans Machine Learning Studio.
   > 
   > 
   
    Quand vous avez terminé, cliquez sur **Enregistrer les modifications**, puis cliquez sur **Atteindre la page de démarrage**.

7. Vous pouvez entrer les valeurs à envoyer à votre service web dans la page d’accueil. Cliquez sur **Envoyer** lorsque vous avez terminé. Le résultat est renvoyé.

Si vous souhaitez revenir à la page **Configuration**, accédez à la page `setting.aspx` de l’application web. Par exemple : `http://carprediction.azurewebsites.net/setting.aspx.`. Vous serez invité à saisir de nouveau la clé de l’API pour pouvoir accéder à la page et mettre à jour les paramètres.

Vous pouvez arrêter, redémarrer ou supprimer l’application web dans le portail Azure comme n’importe quelle autre application web. Tant qu’elle est en cours d’exécution, vous pouvez accéder à l’adresse Web de base et saisir les nouvelles valeurs.

## <a name="how-to-use-the-batch-execution-service-bes-template"></a>Comment utiliser le modèle Batch Execution Service (BES)
Vous pouvez utiliser le modèle d’application Web BES de la même manière que le modèle RRS, à ceci près que l’application Web créée vous permettra d’envoyer plusieurs lignes de données et de recevoir plusieurs résultats.

Les valeurs d’entrée d’un service web d’exécution de lot peuvent provenir du stockage Azure ou d’un fichier local. Les résultats sont stockés dans un conteneur de stockage Azure.
Vous aurez donc besoin d’un conteneur de stockage Azure pour stocker les résultats renvoyés par l’application Web. Vous devrez également préparer vos données d’entrée.

![Procédure d’utilisation du modèle Web BES][image2]

1. Pour créer l’application web BES, suivez la même procédure que celle utilisée pour le modèle RRS. Cependant, vous devrez cette fois accéder à [Azure ML Batch Execution Service Web App Template](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/) pour ouvrir le modèle BES sur la Place de marché Azure, puis cliquer sur **Créer une application web**.

2. Pour spécifier l’emplacement de stockage des résultats, indiquez les informations du conteneur de destination sur la page d’accueil de l’application Web. Indiquez également l’emplacement d’où l’application Web pourra extraire ses valeurs d’entrée, à savoir dans un fichier local ou dans un conteneur de stockage Azure.
   Cliquez sur **Envoyer**.
   
    ![Informations sur le stockage][image7]

L’application Web affiche une page avec l’état de la tâche.
Une fois la tâche terminée, vous obtiendrez l’emplacement des résultats dans le stockage d’objets blob Azure. Vous avez également la possibilité de télécharger les résultats dans un fichier local.

## <a name="for-more-information"></a>Pour plus d’informations
Pour en savoir plus sur...

* la création d’une expérience d’apprentissage automatique avec Machine Learning Studio, consultez [Création de votre première expérience dans Azure Machine Learning Studio](create-experiment.md)
* le déploiement de votre expérience d’apprentissage automatique sous la forme d’un service web, consultez [Déploiement d’un service web Azure Machine Learning](publish-a-machine-learning-web-service.md)
* d’autres manières d’accéder à votre service web, consultez [Utilisation d’un service web Azure Machine Learning](consume-web-services.md)

[image1]: media/consume-web-service-with-web-app-template/rrs-web-template-flow.png
[image2]: media/consume-web-service-with-web-app-template/bes-web-template-flow.png
[image3]: media/consume-web-service-with-web-app-template/api-key.png
[image4]: media/consume-web-service-with-web-app-template/post-uri.png
[image5]: media/consume-web-service-with-web-app-template/create-web-app.png
[image6]: media/consume-web-service-with-web-app-template/web-service-info.png
[image7]: media/consume-web-service-with-web-app-template/storage.png

