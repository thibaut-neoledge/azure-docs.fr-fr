---
title: Encoder des messages AS2 dans Azure Logic Apps | Microsoft Docs
description: "Découvrez comment utiliser l’encodeur AS2 inclus dans Enterprise Integration Pack et Logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 332fb9e3-576c-4683-bd10-d177a0ebe9a3
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: a2ca24c1800a01e6a15af35937a654093cf224af
ms.openlocfilehash: 754ec2e829babd2e5ca3e5e5290db950ef5035e7


---
# <a name="get-started-with-encode-as2-message"></a>Prise en main d’Encode AS2 Message
Connectez-vous à Encode AS2 Message pour créer des conditions sûres et fiables de transmission de messages. Encode AS2 Message fournit des fonctionnalités de signature numérique, de cryptage et d’accusés de réception par vérification des notifications de messages (Message Disposition Notifications, MDN), ce qui renforce par ailleurs la non-répudiation.

## <a name="prereqs"></a>Conditions préalables
* Un compte Azure (que vous pouvez [créer gratuitement)](https://azure.microsoft.com/free)
* Un compte d’intégration est nécessaire pour utiliser le connecteur Encode AS2 Message. Plus d’informations sur la création d’un [compte d’intégration](logic-apps-enterprise-integration-create-integration-account.md), de [partenaires](logic-apps-enterprise-integration-partners.md) et d’un [contrat AS2](logic-apps-enterprise-integration-as2.md)

## <a name="encode-as2-messages"></a>Encoder des messages AS2
1. [Créer une application logique](logic-apps-create-a-logic-app.md).
2. Ce connecteur ne possède aucun déclencheur. Utilisez d’autres déclencheurs pour démarrer l’application logique, tel qu’un déclencheur de demande.  Dans le concepteur d’applications logiques, ajoutez un déclencheur et une action.  Sélectionnez Afficher les API gérées par Microsoft dans la liste déroulante, puis saisissez « AS2 » dans la zone de recherche.  Sélectionnez AS2 - Encode AS2 Message :
   
    ![Rechercher AS2](./media/logic-apps-enterprise-integration-as2-encode/as2decodeimage1.png)
3. Si vous n’avez pas encore créé de connexion aux comptes d’intégration, vous êtes invité à saisir les informations de connexion :
   
    ![Créer une connexion de compte d’intégration](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage1.png)  
4. Entrez les détails du compte d’intégration.  Les propriétés marquées d’un astérisque sont obligatoires :
   
   | Propriété | Détails |
   | --- | --- |
   | Nom de connexion * |Entrez un nom pour votre connexion |
   | Compte d’intégration * |Entrez le nom du compte d’intégration. Vérifiez que votre compte d’intégration et votre application logique se trouvent dans le même emplacement Azure |
   
      Une fois complets, les détails de votre connexion se présentent comme suit :
   
      ![Connexion d’intégration créée](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage2.png)  
5. Sélectionnez **Créer**.
6. Vous pouvez voir que la connexion a été créée.  Renseignez les informations relatives aux identificateurs AS2-From et AS2-To (tels qu’ils sont configurés dans le contrat), ainsi qu’au corps (ou charge utile) du message :
   
    ![remplir les champs obligatoires](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage3.png)

## <a name="as2-encoder-details"></a>Détails sur l’encodeur AS2
Le connecteur Encode AS2 effectue les opérations suivantes : 

* Applique les en-têtes AS2/HTTP
* Signe les messages sortants (si configuré)
* Chiffre les messages sortants (si configuré)
* Compresse le message (si configuré)

## <a name="try-it-yourself"></a>Essayez-le
Faites un essai. Utilisez le [scénario et le modèle d’application logique AS2](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/) pour déployer une application logique totalement opérationnelle.

## <a name="next-steps"></a>Étapes suivantes
[En savoir plus sur Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Découvrez Enterprise Integration Pack") 




<!--HONumber=Jan17_HO5-->


