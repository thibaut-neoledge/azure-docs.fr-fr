---
title: "Décoder des messages AS2 dans Azure Logic Apps | Microsoft Docs"
description: "Découvrez comment utiliser le décodeur AS2 inclus dans Enterprise Integration Pack et Logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 2f407a428aa176cc5c2a3b6bb236b522bda5ab64
ms.openlocfilehash: 37562ff385305088590c793147b8ad268148c40b


---
# <a name="get-started-with-decoding-as2-message-in-your-logic-apps"></a>Prise en main du décodage de messages AS2 dans vos applications logiques
Connectez-vous à Decode AS2 Message pour créer des conditions sûres et fiables de transmission de messages. Decode AS2 Message fournit des fonctionnalités de signature numérique, de cryptage et d’accusés de réception par vérification des notifications de messages (Message Disposition Notifications, MDN).

## <a name="prereqs"></a>Conditions préalables
* Un compte Azure (que vous pouvez [créer gratuitement)](https://azure.microsoft.com/free)
* Un compte d’intégration est nécessaire pour utiliser le connecteur Decode AS2 Message. Plus d’informations sur la création d’un [compte d’intégration](logic-apps-enterprise-integration-create-integration-account.md), de [partenaires](logic-apps-enterprise-integration-partners.md) et d’un [contrat AS2](../logic-apps/logic-apps-enterprise-integration-as2.md)

## <a name="decode-as2-messages"></a>Décoder des messages AS2
1. [Créez une application logique](../logic-apps/logic-apps-create-a-logic-app.md).
2. Ce connecteur ne possède aucun déclencheur. Utilisez d’autres déclencheurs pour démarrer l’application logique, tel qu’un déclencheur de demande.  Dans le concepteur d’applications logiques, ajoutez un déclencheur et une action.  Sélectionnez Afficher les API gérées par Microsoft dans la liste déroulante, puis saisissez « AS2 » dans la zone de recherche.  Sélectionnez AS2 - Decode AS2 Message :
   
    ![Recherche AS2](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage1.png)
3. Si vous n’avez pas encore créé de connexion aux comptes d’intégration, vous êtes invité à saisir les informations de connexion :
   
    ![Créer une connexion d’intégration](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage2.png)
4. Entrez les détails du compte d’intégration.  Les propriétés marquées d’un astérisque sont obligatoires :
   
   | Propriété | Détails |
   | --- | --- |
   | Nom de connexion * |Entrez un nom pour votre connexion |
   | Compte d’intégration * |Entrez le nom du compte d’intégration. Vérifiez que votre compte d’intégration et votre application logique se trouvent dans le même emplacement Azure |
   
      Une fois complets, les détails de votre connexion se présentent comme suit
   
      ![connexion d’intégration](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage3.png)
5. Sélectionnez **Créer**.
6. Vous pouvez voir que la connexion a été créée.  Maintenant, effectuez les autres étapes dans votre application logique :
   
    ![connexion d’intégration créée](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage4.png) 
7. Sélectionnez le corps et les en-têtes des sorties de requête :
   
    ![remplir les champs obligatoires](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage5.png) 

## <a name="as2-decoder-details"></a>Détails sur le décodeur AS2
Le connecteur Decode AS2 effectue les opérations suivantes : 

* Traite les en-têtes AS2/HTTP
* Vérifie la signature (si configuré)
* Déchiffre les messages (si configuré)
* Compresse le message (si configuré)
* Rapproche un MDN reçu avec le message sortant d’origine
* Met à jour et met en corrélation les enregistrements dans la base de données de non-répudiation
* Écrit les enregistrements pour le rapport d’état AS2
* Les contenus de charge utile de sortie sont codés en base64
* Détermine si un MDN est requis et si le MDN doit être synchrone ou asynchrone d’après la configuration dans l’accord AS2
* Génère un MDN synchrone ou asynchrone (basé sur les configurations de l’accord)
* Définit les propriétés et les jetons de corrélation sur le MDN

## <a name="try-it-yourself"></a>Essayez-le
Faites un essai. Utilisez le [scénario et le modèle d’application logique AS2](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/) pour déployer une application logique totalement opérationnelle.

## <a name="next-steps"></a>Étapes suivantes
[En savoir plus sur Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md) 




<!--HONumber=Jan17_HO5-->


