---
title: "Migrer vers l’API Recommendations Azure Cognitive Services à partir de l’API Recommandations DataMarket | Microsoft Docs"
description: Recommandations Azure Machine Learning -- migration vers les recommandations Cognitive Services
services: cognitive-services
documentationcenter: 
author: luiscabrer
manager: jhubbard
editor: cgronlun
ms.assetid: ec9cc302-fef5-4b68-8f9b-fa73538d0424
ms.service: cognitive-services
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/01/2016
ms.author: luisca
translationtype: Human Translation
ms.sourcegitcommit: 8329cc800f685455be113521446e691b5798c1fc
ms.openlocfilehash: 072f92b186f32813eef9b17e742081b680ef86a3


---
# <a name="migrate-to-azure-cognitive-services-recommendations-api-from-the-datamarket-recommendations-api"></a>Migrer vers l’API Recommendations Azure Cognitive Services à partir de l’API Recommandations DataMarket
Cet article vous explique comment migrer de [l’API Recommendations Microsoft DataMarket](https://datamarket.azure.com/dataset/amla/recommendations) vers [l’API Recommendations Microsoft Azure Cognitive Services](https://www.microsoft.com/cognitive-services/en-us/recommendations-api).

L’API Recommendations DataMarket n’acceptera plus de nouveaux clients à partir du 31 décembre 2016 et sera déconseillée le 28 février 2017.

## <a name="how-do-i-start-using-the-azure-cognitive-services-recommendations-api"></a>Comment commencer à utiliser l’API Recommendations Cognitive Services ?
Pour migrer vers l’API Recommendations Cognitive Services, procédez comme suit :

1. Si vous ne disposez pas d’un abonnement Azure, [souscrivez-en un](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1) . 
2. Suivez les instructions détaillées du [guide de démarrage rapide](cognitive-services-recommendations-quick-start.md) pour vous inscrire à l’API Recommendations Cognitive Services et l’utiliser à des fins de programmation. 
3. Accédez à la [page d’accueil de l’API Recommendations Cognitive Services](https://www.microsoft.com/cognitive-services/en-us/recommendations-api) pour en savoir plus sur le service et consulter la documentation.

## <a name="i-used-the-recommendations-ui-to-build-my-models-is-there-a-similar-tool-for-the-cognitive-services-recommendations-api"></a>J’ai utilisé l’interface utilisateur Recommendations pour créer mes modèles. Existe-t-il un outil similaire pour l’API Recommendations Cognitive Services ?
Absolument ! L’URL de la nouvelle [interface utilisateur Recommandations](http://recommendations-portal.azurewebsites.net/) est http://recommendations-portal.azurewebsites.net. 

> [!NOTE]
> Vos informations d’identification DataMarket ne fonctionnent pas ici. Souscrivez un abonnement dans le portail Azure et obtenez la clé de compte nécessaire pour utiliser la nouvelle [interface utilisateur Recommendations](http://recommendations-portal.azurewebsites.net/).
> Si vous n’avez pas de clé de compte, consultez la tâche 1 du [guide de démarrage rapide](cognitive-services-recommendations-quick-start.md).
> 
> 

## <a name="is-the-new-api-format-the-same-as-the-datamarket-recommendations-api"></a>Le format de la nouvelle API est-il identique à celui de l’API Recommandations DataMarket ?
L’API prend en charge les mêmes scénarios et flux de processus que ceux qui sont pris en charge dans la version DataMarket, mais l’interface de l’API réelle a été mise à jour pour être conforme aux [exigences de l’API Microsoft REST](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md). Les API sont plus cohérentes et travaillent mieux avec des outils qui prennent en charge Swagger.

Pour comprendre chacune des API, consultez l’ [explorateur d’API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3db).
Utilisez le bouton *Essayer* pour tester un appel d’API. Le format des fichiers utilisé par l’API Recommendations (fichiers catalogue et d’utilisation) n’ayant pas changé, vous pouvez continuer à utiliser les mêmes fichiers et/ou les infrastructures que celles que vous avez créées pour générer ces fichiers.

## <a name="what-are-some-new-features-in-the-cognitive-services-recommendations-api"></a>Quelles sont les nouvelles fonctionnalités de l’API Recommendations Cognitive Services ?
Au cours des deux derniers mois, nous avons publié de nouvelles fonctionnalités pour l’API Recommendations Cognitive Services :

* L’interface utilisateur Recommendations pour former et tester des modèles sans avoir à écrire une seule ligne de code
* La notation Batch pour vous fournir des milliers de recommandations à la fois
* La prise en charge des mesures de build pour interroger la qualité des modèles de recommandations
* La prise en charge des règles d’entreprise
* La possibilité d’énumérer et de télécharger les fichiers d’utilisation et de catalogue
* La prise en charge des builds de classement pour interroger la qualité des fonctions d’élément d’un modèle de recommandations
* La possibilité de rechercher un produit dans le catalogue

## <a name="when-does-microsoft-stop-supporting-the-datamarket-recommendations-api"></a>Quand l’API Recommendations DataMarket ne sera-t-elle plus prise en charge par Microsoft ?
[API Recommendations sur DataMarket](https://datamarket.azure.com/dataset/amla/recommendations) n’acceptera plus de nouveaux clients à compter du 31 décembre 2016 et sera complètement supprimée d’ici le 28 février 2017. 

## <a name="what-if-i-dont-have-the-data-that-i-need-to-recreate-my-models-in-the-cognitive-services-recommendations-api"></a>Et si je n’ai pas les données dont j’ai besoin pour recréer mes modèles dans l’API Recommendations Cognitive Services ?
Nous souhaitons que cette transition se fasse aussi aisément que possible pour vous. Nous pouvons vous aider à déplacer vos anciens modèles de votre compte DataMarket vers votre nouvel abonnement à l’API Recommendations d’Azure Cognitive Services. Contactez-nous à l’adresse [mlapi@microsoft.com](mailto://mlapi@microsoft.com). Avant d’associer les modèles à votre nouveau compte, nous vous demanderons de fournir votre ID d’abonnement DataMarket et votre ID d’abonnement Cognitive Services.




<!--HONumber=Dec16_HO2-->


