---
title: "Certifier des connecteurs personnalisés - Azure Logic Apps | Microsoft Docs"
description: "Mettre les connecteurs à la disposition de tous les utilisateurs dans Azure Logic Apps, Microsoft Flow et Microsoft PowerApps"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 9bcb138c181df0c12548cc5dc721542fd9c9ba68
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="submit-your-connectors-for-microsoft-certification"></a>Soumettre des connecteurs à la certification Microsoft

Pour rendre les connecteurs personnalisés accessibles à tous les utilisateurs d’Azure Logic Apps, Microsoft Flow et Microsoft PowerApps, soumettez votre connecteur à la révision, à la validation et à l’approbation de Microsoft en vue de sa publication. 

## <a name="certification-criteria"></a>Critères de certification

| Fonctionnalité | Détails | Requis ou recommandé |
|------------|---------|-------------------------|
| Application SaaS (software as a service) | Répond à un scénario d’utilisateur qui s’adapte bien à Logic Apps, Flow et PowerApps. | Requis |
| Type d'authentification | Votre API doit prendre en charge l’authentification de base, OAuth2, par clé API. | Requis | 
| Support | Vous devez fournir un contact de support pour permettre aux clients d’obtenir de l’aide. | Requis | 
| Disponibilité et durée de fonctionnement | Votre application a une durée de fonctionnement d’au moins 99,9 %. | Recommandé | 
|||| 

De même, si vous n’êtes ni partenaire Microsoft, ni éditeur de logiciels indépendant (ISV) et que vous souhaitez certifier et publier un connecteur, vous devez soit posséder le service sous-jacent, soit présenter des droits explicites d’utilisation de l’API.

## <a name="validation-phases"></a>Phases de validation

Microsoft utilise ces phases de validation pour évaluer votre connecteur :

| Phase de validation | Description | 
| ----- | ----------- |
| Fonctionnalités | Microsoft teste votre connecteur avec Logic Apps, Flow et PowerApps pour vérifier l’absence des erreurs suivantes : <p>- erreurs d’OpenAPI (Swagger) ou JSON non valide qui s’affichent dans la section Définition de l’Assistant Connecteur personnalisé ; <p>- erreurs de validation du runtime ou du schéma qui s’affichent dans la section Test de l’Assistant Connecteur personnalisé. | 
| Contenu | Microsoft vérifie que votre connecteur utilise des noms et des descriptions conviviaux pour chaque entité. Assurez-vous que chacune des opérations, chacun des paramètres d’entrée et chacun des attributs de réponse du Swagger de votre connecteur comportent ces éléments : <p>- [Résumé](../logic-apps/custom-connector-openapi-extensions.md#summary) <br>- [Description](../logic-apps/custom-connector-openapi-extensions.md#description) </br>- [Informations sur la visibilité](../logic-apps/custom-connector-openapi-extensions.md#visibility) | 
||| 

## <a name="nominate-and-submit-your-connector-to-microsoft-for-certification"></a>Proposer et soumettre votre connecteur à Microsoft en vue de sa certification

Pour demander une certification, suivez les étapes ci-dessous :

1. **Proposer**

   1. [Soumettez votre proposition](https://go.microsoft.com/fwlink/?linkid=848754).

      1. En bas de la page, cliquez sur **Nous contacter**.
      2. Remplissez le formulaire et sélectionnez **Questions sur le programme ISV Connector et le co-marketing**.
      3. Cliquez sur **Envoyer**.

   2. Signez l’accord de confidentialité et l’accord de partenariat mutuels que vous avez reçus. 

      Microsoft exige la signature de ces contrats pour continuer. 
      Nous pouvons alors vérifier si votre connecteur répond aux critères de certification. 
   
   3. Si votre connecteur est approuvé, Microsoft vous informe des instructions d’intégration.
    
2. **Révision**

   1. Envoyez ces informations à votre contact de candidature en vue de la révision :

      * Le fichier OpenAPI décrivant votre API.
      * Le fichier d’icône (.png ou .jpg) qui représente votre connecteur. 
      
        L’icône doit avoir un logo d’environ 160 pixels à l’intérieur d’un carré de 230 pixels. 
        Un logo blanc sur fond de couleur est préférable.
      
      * La couleur de la marque de votre icône au format hexadécimal, qui doit correspondre à l’arrière-plan de couleur du fichier d’icône.

      * Un compte de test pour la validation.
      * Un contact de support.

   2. Si nous avons besoin d’autres informations, nous vous contacterons.

3. **Publier**

    Une fois que nous avons validé les fonctionnalités et le contenu de votre connecteur, nous préparons le connecteur au déploiement sur tous les produits et toutes les régions.
    
    Par défaut, tous les connecteurs sont publiés en tant que connecteurs « Premium ». 
    Si vous avez créé votre application avec Azure, vous pouvez demander à faire figurer votre connecteur comme connecteur « Standard », accessible à tous les utilisateurs disposant d’un plan Office 365 Entreprise. 
    Pour plus d’informations, demandez à votre contact de candidature.

## <a name="next-steps"></a>Étapes suivantes

* [FAQ sur les connecteurs personnalisés](../logic-apps/custom-connector-faq.md)
* [Vue d’ensemble des connecteurs personnalisés](../logic-apps/custom-connector-overview.md)