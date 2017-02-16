---
title: "Apprendre à créer un contrat AS2 pour Enterprise Integration Pack | Microsoft Docs"
description: "Apprendre à créer un contrat AS2 pour Enterprise Integration Pack | Azure logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: 
ms.assetid: c9b7e1a9-4791-474c-855f-988bd7bf4b7f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 47a1c5653d84a55224c18222bc547e1c863ea442
ms.openlocfilehash: ea256557a38b3ce6cb457d195fa4b48cfd8879b8


---
# <a name="enterprise-integration-with-as2"></a>Intégration d'entreprise avec AS2
## <a name="create-an-as2-agreement"></a>Créer un contrat AS2
Pour utiliser les fonctionnalités d’entreprise dans vos applications logiques, vous devez d’abord créer des contrats. 

### <a name="heres-what-you-need-before-you-get-started"></a>Voici ce dont vous avez besoin pour commencer
* Un [compte d’intégration](../logic-apps/logic-apps-enterprise-integration-accounts.md) défini dans votre abonnement Azure  
* Au moins deux [partenaires](logic-apps-enterprise-integration-partners.md) déjà définis dans votre compte d’intégration  

> [!NOTE]
> Lorsque vous créez un contrat, le contenu du fichier de contrat doit correspondre au type de contrat.    
> 
> 

Une fois que vous avez [créé un compte d’intégration](../logic-apps/logic-apps-enterprise-integration-accounts.md) et [ajouté des partenaires](logic-apps-enterprise-integration-partners.md), vous pouvez créer un contrat en suivant ces étapes :  

### <a name="from-the-azure-portal-home-page"></a>À partir de la page d’accueil du portail Azure
Une fois que vous êtes connecté au [portail Azure](http://portal.azure.com "portail Azure") :  

1. Sélectionnez **Plus de services** et entrez **intégration** dans la zone de recherche de filtre. Sélectionnez **Comptes d’intégration** dans la liste des résultats    
![](./media/logic-apps-enterprise-integration-agreements/overview-1.png)    
2. Sélectionnez le compte d’intégration auquel vous ajouterez le certificat. 
![](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
3. Sélectionnez la mosaïque **Contrats** . Si vous ne la voyez pas, commencez par l’ajouter.   
![](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)   
4. Sélectionnez le bouton **Ajouter** dans le panneau Contrats qui s’affiche.  
![](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)  
5. Entrez un **nom** pour votre contrat, sélectionnez **AS2** pour le **type de contrat**, le **partenaire hôte**, **l’identité de l’hôte**, le **partenaire invité** et **l’identité de l’invité** dans le panneau Contrats.  
![](./media/logic-apps-enterprise-integration-agreements/agreement-3.png)  

Voici quelques informations qui peuvent s’avérer utiles lorsque vous configurez les paramètres de votre contrat : 

| Propriété | Description |
| --- | --- |
| Partenaire hôte |Un contrat nécessite un partenaire hôte et un partenaire invité. Le partenaire hôte représente l’organisation qui configure le contrat. |
| Identité de l’hôte |Identificateur du partenaire hôte. |
| Partenaire invité |Un contrat nécessite un partenaire hôte et un partenaire invité. Le partenaire invité représente l’organisation qui travaille avec le partenaire hôte. |
| identité de l’invité |Identificateur du partenaire invité. |
| Paramètres de réception |Ces propriétés s’appliquent à tous les messages reçus par un contrat |
| Paramètres d’envoi |Ces propriétés s’appliquent à tous les messages envoyés par un contrat |

Poursuivons :  

1. Sélectionnez les **Paramètres de réception** pour configurer la façon dont les messages reçus dans le cadre de ce contrat doivent être traités.  
   
   * Si vous le souhaitez, vous pouvez remplacer les propriétés dans le message entrant. Pour ce faire, sélectionnez **Override message properties** (Remplacer les propriétés du message).
   * Sélectionnez l’option **Le message doit être signé** si vous souhaitez exiger que tous les messages entrants soient signés. Si vous sélectionnez cette option, vous devrez également sélectionner le *certificat public de partenaire invité* pour valider la signature dans les messages.
   * Sélectionnez l’option **Le message doit être chiffré** si vous souhaitez exiger que tous les messages entrants soient chiffrés.  Si vous sélectionnez cette option, vous devez sélectionner le *certificat privé du partenaire hôte* pour déchiffrer les messages entrants.
   * Vous pouvez également exiger que les messages soient compressés. Pour ce faire, sélectionnez l’option **Le message doit être compressé**.    
   * Sélectionnez **Send MDN** (Envoyer MDN) pour envoyer un MDN de synchronisation pour les messages reçus
   * Sélectionnez **Send signed MDN** (Envoyer MDN signé) pour envoyer un MDN signé pour les messages reçus
   * Sélectionnez **Send asynchronous MDN** (Envoyer MDN asynchrone) pour envoyer un MDN asynchrone pour les messages reçus     
    ![](./media/logic-apps-enterprise-integration-agreements/agreement-4.png)  

Consultez le tableau ci-dessous pour en savoir plus sur les fonctionnalités activées par les paramètres de réception.  

| Propriété | Description |
| --- | --- |
| Override message properties |Sélectionnez cette option pour indiquer que les propriétés dans les messages reçus peuvent être remplacées |
| Le message doit être signé |Activez cette option pour exiger que les messages soient signés numériquement.  Configurer le certificat public du partenaire invité pour la vérification de signature  |
| Le message doit être chiffré |Activez cette option pour exiger que les messages soient chiffrés. Les messages non chiffrés seront rejetés. Configurer le certificat privé du partenaire hôte pour déchiffrer les messages  |
| Le message doit être compressé |Activez cette option pour exiger que les messages soient compressés. Les messages non compressés seront rejetés. |
| Texte du MDN |Il s’agit d’une notification d’état du message (MDN) par défaut à envoyer à l’expéditeur du message |
| Send MDN (Envoyer MDN) |Activez cette option pour autoriser l’envoi de MDN. |
| Send signed MDN (Envoyer MDN signé) |Activez cette option pour exiger que les MDN soient signés. |
| MIC Algorithm (Algorithme MIC) | |
| Send asynchronous MDN (Envoyer MDN asynchrone) |Activez cette option pour exiger que les messages soient envoyés de manière asynchrone. |
| URL |Voici l’URL à laquelle les MDN seront envoyés. |

Poursuivons :  

1. Sélectionnez les **Paramètres d’envoi** pour configurer la façon dont les messages envoyés dans le cadre de ce contrat doivent être traités.  

   * Sélectionnez l’option **Enable message signing** (Activer la signature des messages) pour envoyer des messages signés au partenaire. Si vous sélectionnez cette option, vous devez sélectionner *l’algorithme MIC de certificat privé du partenaire hôte* et le *certificat privé du partenaire hôte* pour signer les messages.
   * Sélectionnez l’option **Enable message encryption** (Activer le chiffrement des messages) pour envoyer des messages chiffrés au partenaire. Si vous sélectionnez cette option, vous devez sélectionner *l’algorithme de certificat public du partenaire hôte* et le *certificat public du partenaire hôte* pour chiffrer les messages.
   * Sélectionner l’option **Le message doit être compressé** pour compresser le message 
   * Sélectionner l’option **Dérouler les en-têtes HTTP** pour dérouler l’en-tête de type de contenu HTTP sur une seule ligne 
   * Sélectionnez l’option **Exiger le MDN** pour recevoir le MDN de synchronisation pour les messages envoyés
   * Sélectionnez l’option **Exiger le MDN signé** pour recevoir le MDN signé pour les messages envoyés
   * Sélectionnez l’option **Exiger le MDN asynchrone** pour recevoir le MDN asynchrone pour les messages envoyés. Si vous sélectionnez cette option, vous devez fournir une URL à laquelle sont envoyés les MDN  
   * Sélectionnez l’option **Enable NRR** (Activer NRR) pour activer la non-répudiation de réception    
   ![](./media/logic-apps-enterprise-integration-agreements/agreement-5.png)  

Consultez le tableau ci-dessous pour en savoir plus sur les fonctionnalités activées par les paramètres d’envoi.  

| Propriété | Description |
| --- | --- |
| Enable message signing (Activer la signature des messages) |Sélectionnez cette option pour que tous les messages envoyés à partir du contrat soient signés. |
| MIC Algorithm (Algorithme MIC) |Sélectionnez l’algorithme à utiliser pour la signature du message. Configurer l’algorithme MIC de certificat privé du partenaire hôte pour signer les messages |
| Certificat |Sélectionnez le certificat à utiliser pour la signature du message. Configurer le certificat privé du partenaire hôte pour signer les messages |
| Enable message encryption (Activer le chiffrement du message) |Sélectionnez cette option pour chiffrer tous les messages envoyés à partir de ce contrat. Configurer l’algorithme de certificat public du partenaire invité pour chiffrer les messages |
| Algorithme de chiffrement |Sélectionnez l’algorithme de chiffrement à utiliser pour le chiffrement du message. Configurer le certificat public du partenaire invité pour chiffrer les messages |
| Dérouler les en-têtes HTTP |Sélectionnez cette option pour dérouler l’en-tête de type de contenu HTTP sur une seule ligne |
| Exiger le MDN |Activer cette option pour demander un MDN pour tous les messages envoyés dans le cadre de ce contrat |
| Exiger le MDN signé |Activez cette option pour demander que tous les MDN envoyés à ce contrat soient signés |
| Exiger le MDN asynchrone |Activez cette option pour demander l’envoi d’un MDN asynchrone à ce contrat |
| URL |L’URL à laquelle les MDN seront envoyés |
| Enable NRR (Activer NRR) |Sélectionnez cette option pour activer la non-répudiation de réception |

Sélectionnez la mosaïque **Contrats** dans le panneau du compte d’intégration pour afficher le nouveau contrat ajouté.  
![](./media/logic-apps-enterprise-integration-agreements/agreement-6.png)

## <a name="next-steps"></a>Étapes suivantes
* [En savoir plus sur Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "En savoir plus sur Enterprise Integration Pack")  


<!--HONumber=Jan17_HO4-->


