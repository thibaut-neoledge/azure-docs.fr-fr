---
title: En savoir plus sur le connecteur Enterprise Integration Pack Encode AS2 Message | Microsoft Docs
description: Découvrez comment utiliser les partenaires avec Enterprise Integration Pack et vos applications logiques
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: erikre
editor: ''

ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: padmavc

---
# Prise en main d’Encode AS2 Message
Connectez-vous à Encode AS2 Message pour créer des conditions sûres et fiables de transmission de messages. Encode AS2 Message fournit des fonctionnalités de signature numérique, de cryptage et d’accusés de réception par vérification des notifications de messages (Message Disposition Notifications, MDN), ce qui renforce par ailleurs la non-répudiation.

## Créer la connexion
### Composants requis
* Un compte Azure (que vous pouvez [créer gratuitement)](https://azure.microsoft.com/free)
* Un compte d’intégration est nécessaire pour utiliser le connecteur Encode AS2 Message. Plus d’informations sur la création d’un [compte d’intégration](app-service-logic-enterprise-integration-create-integration-account.md), de [partenaires](app-service-logic-enterprise-integration-partners.md) et d’un [contrat AS2](app-service-logic-enterprise-integration-as2.md)

### Connectez-vous à Encode AS2 Message en procédant comme suit :
1. La page [Créer une application logique](app-service-logic-create-a-logic-app.md) vous fournit un exemple.
2. Ce connecteur ne possède aucun déclencheur. Utilisez d’autres déclencheurs pour démarrer l’application logique, tel qu’un déclencheur de demande. Dans le concepteur d’applications logiques, ajoutez un déclencheur et une action. Sélectionnez Afficher les API gérées par Microsoft dans la liste déroulante, puis saisissez « AS2 » dans la zone de recherche. Sélectionnez AS2 - Encode AS2 Message.
   
    ![Rechercher AS2](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage1.png)
3. Si vous n’avez pas encore créé de connexion aux comptes d’intégration, vous êtes invité à saisir les informations de connexion.
   
    ![Créer une connexion de compte d’intégration](./media/app-service-logic-enterprise-integration-AS2connector/as2encodeimage1.png)
4. Entrez les détails du compte d’intégration. Les propriétés marquées d’un astérisque sont obligatoires.
   
   | Propriété | Détails |
   | --- | --- |
   | Nom de connexion * |Entrez un nom pour votre connexion |
   | Compte d’intégration * |Entrez le nom du compte d’intégration. Vérifiez que votre compte d’intégration et votre application logique se trouvent dans le même emplacement Azure |
   
      Une fois complets, les détails de votre connexion se présentent comme suit
   
      ![Connexion d’intégration créée](./media/app-service-logic-enterprise-integration-AS2connector/as2encodeimage2.png)
5. Sélectionnez **Créer**
6. Vous pouvez voir que la connexion a été créée. Renseignez les informations relatives aux identificateurs AS2-From et AS2-To (tels que configurés dans le contrat), ainsi qu’au corps (ou charge utile) du message.
   
    ![Remplir les champs obligatoires](./media/app-service-logic-enterprise-integration-AS2connector/as2encodeimage3.png)

## Le codage AS2 effectue les opérations suivantes :
* Applique les en-têtes AS2/HTTP
* Signe les messages sortants (si configuré)
* Chiffre les messages sortants (si configuré)
* Compresse le message (si configuré)

## Essayez par vous-même
Prêt à vous lancer ? Cliquez [ici](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/) pour déployer votre propre application logique entièrement fonctionnelle à l’aide des fonctionnalités AS2 de Logic Apps

## Étapes suivantes
[En savoir plus sur Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "En savoir plus sur Enterprise Integration Pack")

<!---HONumber=AcomDC_0914_2016-->