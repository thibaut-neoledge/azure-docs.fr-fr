---
title: "(obsolète) Publication du service web Machine Learning sur Azure Marketplace | Microsoft Docs"
description: "(obsolète) Publication du service web Machine Learning sur Azure Marketplace"
services: machine-learning
documentationcenter: 
author: BharathS
manager: jhubbard
editor: cgronlun
ms.assetid: 68e908be-3a99-4cd7-9517-e2b5f2f341b8
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: deprecated
ms.date: 06/02/2017
ms.author: bharaths
ROBOTS: NOINDEX, NOFOLLOW
redirect_url: machine-learning-gallery-experiments
ms.translationtype: Human Translation
ms.sourcegitcommit: 85a58e09e05fdb50984055ab25c4c2fe520dab4d
ms.openlocfilehash: 2d62966f130f6778c9561393cc7fc338f8903f1e
ms.contentlocale: fr-fr
ms.lasthandoff: 01/10/2017


---
# <a name="deprecated-publish-azure-machine-learning-web-service-to-the-azure-marketplace"></a>(obsolète) Publication du service web Machine Learning sur Azure Marketplace

> [!NOTE]
> DataMarket et Data Services vont être mis hors service et les abonnements existants seront annulés à compter du 31/03/2017. Par conséquent, cet article deviendra obsolète. 
> 
> Vous pouvez aussi publier vos expériences d’apprentissage Machine Learning dans la [galerie Cortana Intelligence](https://gallery.cortanaintelligence.com/) pour les partager avec la communauté spécialiste des données. Pour plus d’informations, consultez [Partager et découvrir des solutions dans la galerie Cortana Intelligence](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-gallery-how-to-use-contribute-publish).

Azure Marketplace offre la possibilité de publier des services web Azure Machine Learning sous forme de services payants ou gratuits que des clients externes pourront consommer. Cet article propose une présentation du processus, avec des liens vers les consignes pour vous aider à démarrer. À l’aide de ce processus, vous pouvez mettre vos services Web à disposition d’autres développeurs, qui pourront les consommer dans leurs applications.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="overview-of-the-publishing-process"></a>Présentation du processus de publication
Les étapes suivantes vous permettront de publier un service Web Azure Machine Learning sur Azure Marketplace :

1. Créez et publiez un service Request-Response (Request-Response Service, service de requête-réponse) Machine Learning
2. Déployez le service en production et obtenez les informations du point de terminaison OData et de la clé d’API.
3. Utilisez l’URL du service Web à publier sur [Azure Marketplace (Data Market)](https://publish.windowsazure.com/workspace/) 
4. Une fois soumise, votre offre est évaluée et doit être approuvée avant que vos clients puissent commencer à l'acheter. Le processus de publication peut prendre quelques jours ouvrés. 

## <a name="walk-through"></a>Procédure
### <a name="step-1-create-and-publish-a-machine-learning-request-response-service-rrs"></a>Étape 1 : créez et publiez un RRS (Request-Response Service, service de requête-réponse) Machine Learning
 Si vous ne l’avez pas encore fait, consultez ce [guide](machine-learning-walkthrough-5-publish-web-service.md).

### <a name="step-2-deploy-the-service-to-production-and-obtain-the-api-key-and-odata-endpoint-information"></a>Étape 2 : déployez le service en production et obtenez les informations de point de terminaison OData et de la clé d’API.
1. Depuis le [portail Azure Classic](http://manage.windowsazure.com), sélectionnez l’option **MACHINE LEARNING** dans la barre de navigation de gauche, puis sélectionnez votre espace de travail. 
2. Cliquez sur l’onglet **WEB SERVICES** (services Web) et sélectionnez le service Web que vous souhaitez publier sur le marché.
   
    ![Azure Marketplace][workspace]
3. Sélectionnez le point de terminaison que vous souhaitez voir consommé par le marché. Si vous n’avez créé aucun point de terminaison supplémentaire, vous pouvez sélectionner le point de terminaison **Default** (par défaut).
4. Une fois que vous avez cliqué sur le point de terminaison, vous voyez la **API KEY** (clé API). Vous aurez besoin de cette information par la suite, lors de l’étape 3. Faites-en une copie.
   
    ![Azure Marketplace][apikey]
5. Cliquez sur la méthode **REQUEST/RESPONSE** (requête/réponse). Pour l’instant, nous ne prenons pas en charge la publication de services d’exécution par lots sur le marketplace. Vous serez dirigé sur la page d’aide API pour la méthode Request/Response.
6. Copiez l’ **OData Endpoint Address**(l’adresse du point de terminaison OData), vous aurez besoin de ces informations ultérieurement, lors de l’étape 3.
   
    ![Azure Marketplace][odata]

déployez le service en production.

### <a name="step-3-use-the-url-of-the-published-web-service-to-publish-to-azure-marketplace-datamarket"></a>Étape 3 : utilisez l’URL du service Web à publier sur Azure Marketplace (DataMarket)
1. Accédez à [Azure Marketplace (Data Market)](http://datamarket.azure.com/home) 
2. Cliquez sur le lien **Publish** (Publier) en haut de la page. Vous serez dirigé vers le [portail de publication Microsoft Azure](https://publish.windowsazure.com)
3. Cliquez sur la section **publishers** (éditeurs) pour vous inscrire en tant qu’éditeur.
4. Durant la création d’une offre, sélectionnez **Services de données**, puis cliquez sur **Créer un nouveau service de données**. 
   
   ![Azure Marketplace][image1]
   
   <br />
5. Sous **Plans** , indiquez les informations relatives à votre offre, y compris un plan de tarification. Vous pouvez choisir de proposer un service payant ou gratuit. Pour être payé, vous devez indiquer des informations de paiement, notamment vos renseignements bancaires et fiscaux.
6. Sous **Marketing** , vous devez fournir des informations relatives à votre offre, comme son titre et sa description.
7. Sous **Tarification** , vous pouvez fixer le prix de vos plans pour des pays donnés, ou laisser le système décider lui-même du prix de votre offre.
8. Sous l’onglet **Service de données**, cliquez sur **Service Web** pour la **Source de données**.
   
    ![Azure Marketplace][image2]
9. Obtenez l’URL du service web et la clé API à partir du portail Azure Classic, tel qu’expliqué dans l’étape 2 ci-dessus.
10. Dans la boîte de dialogue de configuration du service de données Marketplace, collez l’adresse du point de terminaison OData dans la zone de texte **URL du service** .
11. Pour **l’authentification**, choisissez **En-tête** comme **Schéma d’authentification**.
    
    * Saisissez « Autorisation » comme **nom d’en-tête**.
    * Pour la **valeur d’en-tête**, entrez « Titulaire » (sans les guillemets), appuyez sur la barre **d’espace** et collez la clé API.
    * Cochez la case **Ce service est OData** .
    * Cliquez sur **Tester la connexion** pour tester la connexion.
12. Sous **Catégories**, assurez-vous que l’option **Apprentissage automatique** est sélectionnée.
13. Quand vous avez terminé la saisie de toutes les métadonnées concernant votre offre, cliquez sur **Publier**, puis **Push dans l’environnement intermédiaire**. À ce stade, vous recevrez une notification pour chaque problème restant à corriger.
14. Après avoir vérifié l’achèvement de tous les problèmes en suspens, cliquez sur **Demander l’approbation pour pousser vers la Production**. Le processus de publication peut prendre quelques jours ouvrés. 

[image1]:./media/machine-learning-publish-web-service-to-azure-marketplace/image1.png
[image2]:./media/machine-learning-publish-web-service-to-azure-marketplace/image2.png
[workspace]:./media/machine-learning-publish-web-service-to-azure-marketplace/selectworkspace.png
[apikey]:./media/machine-learning-publish-web-service-to-azure-marketplace/apikey.png
[odata]:./media/machine-learning-publish-web-service-to-azure-marketplace/odata.png


