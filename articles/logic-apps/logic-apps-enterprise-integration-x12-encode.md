---
title: Encoder des messages X12 dans Azure Logic Apps | Microsoft Docs
description: "Comment utiliser l’encodeur X12 dans Enterprise Integration Pack, dans vos applications logiques"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: a01e9ca9-816b-479e-ab11-4a984f10f62d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: bd339e60aff1edca7f86e0ae82746f55eb67b296
ms.openlocfilehash: 9f7b53cfafa8c14ab46cf80015afc8da4e0fda2b


---
# <a name="get-started-with-encode-x12-message"></a>Prise en main d’Encode X12 Message
Valide l’EDI et les propriétés spécifiques au partenaire, convertit les messages codés au format XML en documents informatisés EDI au sein de l’échange et demande un accusé de réception technique et/ou fonctionnel

## <a name="prereqs"></a>Conditions préalables
* Un compte Azure (que vous pouvez [créer gratuitement)](https://azure.microsoft.com/free)
* Un compte d’intégration est nécessaire pour utiliser le connecteur Encode X12 Message. Plus d’informations sur la création d’un [compte d’intégration](logic-apps-enterprise-integration-create-integration-account.md), de [partenaires](logic-apps-enterprise-integration-partners.md) et d’un [contrat X12](logic-apps-enterprise-integration-x12.md)

## <a name="encode-x12-messages"></a>Encoder des messages X12

1. [Créez une application logique](logic-apps-create-a-logic-app.md).
2. Ce connecteur ne possède aucun déclencheur. Utilisez d’autres déclencheurs pour démarrer l’application logique, tel qu’un déclencheur de demande.  Dans le concepteur d’applications logiques, ajoutez un déclencheur et une action.  Sélectionnez Afficher les API gérées par Microsoft dans la liste déroulante, puis saisissez « x12 » dans la zone de recherche.  Sélectionnez X12 - Encode X12 Message par nom de contrat ou X12 - Encode to X 12 message par identités :  
   
    ![recherche x12](./media/logic-apps-enterprise-integration-x12-encode/x12decodeimage1.png) 
3. Si vous n’avez pas encore créé de connexion aux comptes d’intégration, vous êtes invité à saisir les informations de connexion :
   
    ![connexion de compte d’intégration](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage1.png) 
4. Entrez les détails du compte d’intégration.  Les propriétés marquées d’un astérisque sont obligatoires :
   
   | Propriété | Détails |
   | --- | --- |
   | Nom de connexion * |Entrez un nom pour votre connexion |
   | Compte d’intégration * |Entrez le nom du compte d’intégration. Vérifiez que votre compte d’intégration et votre application logique se trouvent dans le même emplacement Azure |
   
    Une fois complets, les détails de votre connexion se présentent comme suit :
   
    ![connexion de compte d’intégration créée](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage2.png) 
5. Sélectionnez **Créer**.
6. Vous pouvez voir que la connexion a été créée.
   
    ![détails de connexion de compte d’intégration](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage3.png) 

#### <a name="encode-x12-messages-by-agreement-name"></a>Encode X12 message par nom de contrat
Sélectionnez le contrat X12 dans la liste déroulante et le message XML à encoder :     
    ![remplir les champs obligatoires](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage4.png) 

#### <a name="encode-x12-messages-by-identities"></a>Encode X12 message par identités
Indiquez l’identificateur et le qualificateur de l’expéditeur ainsi que l’identificateur et le qualificateur du récepteur tels que configurés dans l’accord X12.  Sélectionnez le message XML à encoder :
   
   ![remplir les champs obligatoires](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage5.png) 

## <a name="x12-encode-details"></a>Détails sur X12 Encode

Le connecteur X12 Encode exécute ce qui suit :

* Résolution du contrat en faisant correspondre les propriétés de contexte de l’expéditeur et du récepteur.
* Sérialise l’échange EDI en convertissant les messages codés au format XML en documents informatisés EDI au sein de l’échange.
* Applique les segments d’en-tête et de code de fin du document informatisé
* Génère un numéro de contrôle d’échange, un numéro de contrôle de groupe et un numéro de contrôle de document informatisé pour chaque échange sortant
* Remplace les séparateurs dans les données de charge utile
* Valide l’EDI et les propriétés spécifiques au partenaire
  * Validation de schéma des éléments de données du document informatisé par rapport au schéma de message
  * Validation EDI effectuée sur les éléments de données du document informatisé.
  * Validation étendue effectuée sur les éléments de données du document informatisé
* Demande un accusé de réception fonctionnel et/ou technique (si configuré).
  * Suite à la validation de l’en-tête, un accusé de réception technique est généré. L’accusé de réception technique renvoie l’état du traitement de l’en-tête et du code de fin d’un échange par le récepteur de l’adresse
  * Suite à la validation du corps, un accusé de réception fonctionnel est généré. L’accusé de réception fonctionnel signale chaque erreur rencontrée lors du traitement du document reçu

## <a name="next-steps"></a>Étapes suivantes
[En savoir plus sur Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Découvrez Enterprise Integration Pack") 




<!--HONumber=Jan17_HO5-->


