---
title: En savoir plus sur le connecteur Enterprise Integration Pack Decode AS2 Message | Microsoft Docs
description: "Découvrez comment utiliser les partenaires avec Enterprise Integration Pack et vos applications logiques"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: erikre
editor: 
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 5836e0d2bb7d1d97e9f175396f51047af0f84ca5


---
# <a name="get-started-with-decode-as2-message"></a>Prise en main de Decode AS2 Message
Connectez-vous à Decode AS2 Message pour créer des conditions sûres et fiables de transmission de messages. Decode AS2 Message fournit des fonctionnalités de signature numérique, de cryptage et d’accusés de réception par vérification des notifications de messages (Message Disposition Notifications, MDN).

## <a name="create-the-connection"></a>Créer la connexion
### <a name="prerequisites"></a>Composants requis
* Un compte Azure (que vous pouvez [créer gratuitement)](https://azure.microsoft.com/free)
* Un compte d’intégration est nécessaire pour utiliser le connecteur Decode AS2 Message. Plus d’informations sur la création d’un [compte d’intégration](app-service-logic-enterprise-integration-create-integration-account.md), de [partenaires](app-service-logic-enterprise-integration-partners.md) et d’un [contrat AS2](app-service-logic-enterprise-integration-as2.md)

### <a name="connect-to-decode-as2-message-using-the-following-steps"></a>Connectez-vous à Decode AS2 Message en procédant comme suit :
1. La page [Créer une application logique](app-service-logic-create-a-logic-app.md) vous fournit un exemple.
2. Ce connecteur ne possède aucun déclencheur. Utilisez d’autres déclencheurs pour démarrer l’application logique, tel qu’un déclencheur de demande.  Dans le concepteur d’applications logiques, ajoutez un déclencheur et une action.  Sélectionnez Afficher les API gérées par Microsoft dans la liste déroulante, puis saisissez « AS2 » dans la zone de recherche.  Sélectionnez AS2 - Decode AS2 Message.
   
    ![Recherche AS2](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage1.png)
3. Si vous n’avez pas encore créé de connexion aux comptes d’intégration, vous êtes invité à saisir les informations de connexion.
   
    ![Créer une connexion d’intégration](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage2.png)
4. Entrez les détails du compte d’intégration.  Les propriétés marquées d’un astérisque sont obligatoires
   
   | Propriété | Détails |
   | --- | --- |
   | Nom de connexion * |Entrez un nom pour votre connexion |
   | Compte d’intégration * |Entrez le nom du compte d’intégration. Vérifiez que votre compte d’intégration et votre application logique se trouvent dans le même emplacement Azure |
   
      Une fois complets, les détails de votre connexion se présentent comme suit
   
      ![connexion d’intégration](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage3.png)
5. Sélectionnez **Créer**
6. Vous pouvez voir que la connexion a été créée.  Maintenant, effectuez les autres étapes dans votre application logique.
   
    ![connexion d’intégration créée](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage4.png) 
7. Sélectionnez le corps et les en-têtes à partir des sorties de requête.
   
    ![remplir les champs obligatoires](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage5.png) 

## <a name="the-as2-decode-does-the-following"></a>Le décodage AS2 effectue les opérations suivantes :
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

## <a name="try-it-for-yourself"></a>Essayez par vous-même
Prêt à vous lancer ? Cliquez [ici](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/) pour déployer votre propre application logique entièrement fonctionnelle à l’aide des fonctionnalités AS2 de Logic Apps 

## <a name="next-steps"></a>Étapes suivantes
[En savoir plus sur Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "Découvrez Enterprise Integration Pack") 




<!--HONumber=Nov16_HO3-->


