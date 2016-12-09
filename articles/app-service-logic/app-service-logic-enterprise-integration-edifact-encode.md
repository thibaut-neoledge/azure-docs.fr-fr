---
title: En savoir plus sur le connecteur Enterprise Integration Pack Encode EDIFACT Message | Microsoft Docs
description: "Découvrez comment utiliser les partenaires avec Enterprise Integration Pack et vos applications logiques"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: erikre
editor: 
ms.assetid: 974ac339-d97a-4715-bc92-62d02281e900
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 35eeddd89c79b20d81c4af611598d909da38db52


---
# <a name="get-started-with-encode-edifact-message"></a>Prise en main d’Encode EDIFACT Message
Valide l’EDI et les propriétés spécifiques au partenaire 

## <a name="create-the-connection"></a>Créer la connexion
### <a name="prerequisites"></a>Composants requis
* Un compte Azure (que vous pouvez [créer gratuitement)](https://azure.microsoft.com/free)
* Un compte d’intégration est nécessaire pour utiliser le connecteur Encode EDIFACT Message. Plus d’informations sur la création d’un [compte d’intégration](app-service-logic-enterprise-integration-create-integration-account.md), de [partenaires](app-service-logic-enterprise-integration-partners.md) et d’un [contrat EDIFACT](app-service-logic-enterprise-integration-edifact.md)

### <a name="connect-to-decode-edifact-message-using-the-following-steps"></a>Connectez-vous à Decode EDIFACT Message en procédant comme suit :
1. [Créer une application logique](app-service-logic-create-a-logic-app.md) vous fournit un exemple.
2. Ce connecteur ne possède aucun déclencheur. Utilisez d’autres déclencheurs pour démarrer l’application logique, tel qu’un déclencheur de demande.  Dans le concepteur d’applications logiques, ajoutez un déclencheur et une action.  Sélectionnez Afficher les API gérées par Microsoft dans la liste déroulante, puis saisissez « EDIFACT » dans la zone de recherche.  Sélectionnez Encode EDIFACT Message par nom de contrat ou Encode to EDIFACT message par identités.
   
    ![recherche EDIFACT](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage1.png)  
3. Si vous n’avez pas encore créé de connexion aux comptes d’intégration, vous êtes invité à saisir les informations de connexion
   
    ![créer une connexion de compte d’intégration](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactencodeimage1.png)  
4. Entrez les détails du compte d’intégration.  Les propriétés marquées d’un astérisque sont obligatoires
   
   | Propriété | Détails |
   | --- | --- |
   | Nom de connexion * |Entrez un nom pour votre connexion |
   | Compte d’intégration * |Entrez le nom du compte d’intégration. Vérifiez que votre compte d’intégration et votre application logique se trouvent dans le même emplacement Azure |
   
    Une fois complets, les détails de votre connexion se présentent comme suit
   
    ![connexion de compte d’intégration](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactencodeimage2.png)
5. Sélectionnez **Créer**
6. Vous pouvez voir que la connexion a été créée
   
    ![détails de connexion de compte d’intégration](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactencodeimage4.png)

#### <a name="encode-edifact-message-by-agreement-name"></a>Encode EDIFACT Message par nom de contrat
1. Indiquez le nom du contrat EDIFACT et le nom du message xml à encoder.
   
   ![remplir les champs obligatoires](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactencodeimage6.png)

#### <a name="encode-edifact-message-by-identities"></a>Encode EDIFACT Message par identités
1. Indiquez l’identificateur et le qualificateur de l’expéditeur ainsi que l’identificateur et le qualificateur du récepteur tels que configurés dans l’accord EDIFACT.  Sélectionnez le message xml à encoder
   
    ![remplir les champs obligatoires](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactencodeimage7.png)

## <a name="edifact-encode-does-following"></a>EDIFACT Encode effectue les actions suivantes
* Résout le contrat en mettant en correspondance les identificateurs et les qualificateurs de l’expéditeur et du récepteur
* Sérialise l’échange EDI en convertissant les messages codés au format XML en documents informatisés EDI au sein de l’échange.
* Applique les segments d’en-tête et de code de fin du document informatisé
* Génère un numéro de contrôle d’échange, un numéro de contrôle de groupe et un numéro de contrôle de document informatisé pour chaque échange sortant
* Remplace les séparateurs dans les données de charge utile
* Valide l’EDI et les propriétés spécifiques au partenaire
  * Validation de schéma des éléments de données du document informatisé par rapport au schéma de message.
  * Validation EDI effectuée sur les éléments de données du document informatisé.
  * Validation étendue effectuée sur les éléments de données du document informatisé
* Génère un document XML pour chaque document informatisé.
* Demande un accusé de réception fonctionnel et/ou technique (si configuré).
  * En tant qu’accusé de réception technique, le message CONTRL indique la réception d’un échange.
  * En tant qu’accusé de réception fonctionnel, le message CONTRL indique l’acceptation ou le rejet du message, du groupe ou de l’échange reçu, en fournissant une liste des erreurs ou des fonctionnalités non prises en charge

## <a name="next-steps"></a>Étapes suivantes
[En savoir plus sur Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "En savoir plus sur Enterprise Integration Pack") 




<!--HONumber=Nov16_HO3-->


