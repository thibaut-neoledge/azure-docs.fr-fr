---
title: "Créer un contrat AS2 dans Azure Logic Apps | Microsoft Docs"
description: "Créer un contrat AS2 pour Enterprise Integration Pack | Azure Logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: c9b7e1a9-4791-474c-855f-988bd7bf4b7f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 6a947ea997bbcfe1b6b28c7cbb49911836750e6a
ms.openlocfilehash: a490b89c5420aecdfb3f79289979faab9a4630e9


---
# <a name="enterprise-integration-with-as2"></a>Intégration d'entreprise avec AS2
Pour utiliser les fonctionnalités d’entreprise dans vos applications logiques, vous devez d’abord créer des contrats.

## <a name="prerequisites"></a>Composants requis
* Un [compte d’intégration](../logic-apps/logic-apps-enterprise-integration-accounts.md) doit être défini dans votre abonnement Azure.  
* Au moins deux [partenaires](logic-apps-enterprise-integration-partners.md) doivent déjà être définis dans votre compte d’intégration.  

> [!NOTE]
> Lorsque vous créez un contrat, le contenu du fichier de contrat doit correspondre au type de contrat.    

Une fois que vous avez créé un compte d’intégration et ajouté des partenaires, vous pouvez créer un contrat en suivant les étapes décrites dans les sections ci-après.  

## <a name="create-an-agreement"></a>Créer un contrat

1. Connectez-vous au [portail Azure](http://portal.azure.com "portail Azure").  
2. Sélectionnez **Plus de services**, saisissez **intégration** dans la zone de recherche de filtre et sélectionnez **Comptes d’intégration** dans la liste des résultats.

 ![Sélectionnez « Comptes d’intégration » dans la liste des résultats de la recherche](./media/logic-apps-enterprise-integration-agreements/overview-1.png)    
3. Sélectionnez le compte d’intégration auquel ajouter le certificat.

 ![Sélectionnez le compte d’intégration](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
4. Sélectionnez la mosaïque **Contrats** . Si la mosaïque ne s’affiche pas, ajoutez-la.

 ![Sélectionnez la mosaïque « Contrats »](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)   
5. Dans le panneau **Contrats**, sélectionnez **Ajouter**.

 ![Sélectionnez « Ajouter ».](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)  
6. Entrez un nom pour votre contrat, sélectionnez **AS2** dans la liste **Type de contrat** et saisissez les informations appropriées dans les listes **Partenaire hôte**, **Identité de l’hôte**, **Partenaire invité** et **Identité de l’invité**.

 ![Entrez un nom pour le contrat](./media/logic-apps-enterprise-integration-agreements/agreement-3.png)  

 Le tableau suivant décrit les propriétés dans la boîte de dialogue **Ajouter** :

    | Propriété | Description |
    | --- | --- |
    | Partenaire hôte | Un contrat nécessite un partenaire hôte et un partenaire invité. Le partenaire hôte représente l’organisation qui configure le contrat. |
    | Identité de l’hôte | Identificateur du partenaire hôte. |
    | Partenaire invité | Un contrat nécessite un partenaire hôte et un partenaire invité. Le partenaire invité représente l’organisation qui travaille avec le partenaire hôte. |
    | identité de l’invité | Identificateur du partenaire invité. |
    | Paramètres de réception | Les propriétés qui s’appliquent à tous les messages reçus dans le cadre du contrat. |
    | Paramètres d’envoi | Les propriétés qui s’appliquent à tous les messages envoyés dans le cadre du contrat. |

7. Pour configurer la façon dont les messages reçus dans le cadre de ce contrat doivent être traités, procédez comme suit :

 a. Sélectionnez **Paramètres de réception**.

 b. Si vous le souhaitez, vous pouvez remplacer les propriétés des messages entrants en cochant la case **Override message properties (Remplacer les propriétés du message)**.

 c. Cochez la case **Le message doit être signé** pour exiger que tous les messages entrants soient signés. Si vous sélectionnez cette option, vous devrez valider la signature dans les messages en sélectionnant le **certificat public du partenaire invité** dans la liste **Certificat**.

 d. Cochez la case **Le message doit être chiffré** pour exiger que tous les messages entrants soient chiffrés. Si vous sélectionnez cette option, vous devrez déchiffrer les messages entrants en sélectionnant le **certificat privé du partenaire hôte** dans la liste **Certificat**.

 e. Pour exiger que les messages soit compressés, cochez la case **Le message doit être compressé**.    

 f. Pour envoyer une notification de réception du message (MDN) synchrone pour les messages reçus, cochez la case **Send MDN** (Envoyer MDN).

 g. Pour envoyer un MDN signé pour les messages reçus, cochez la case **Send signed MDN** (Envoyer MDN signé).

 h. Pour envoyer un MDN asynchrone pour les messages reçus, cochez la case **Send asynchronous MDN** (Envoyer MDN asynchrone).

 ![Définissez les propriétés de « Paramètres de réception »](./media/logic-apps-enterprise-integration-agreements/agreement-4.png)  

 Le tableau suivant décrit les paramètres de **Paramètres de réception** :  

 | Propriété | Description |
 | --- | --- |
 | Override message properties | Indique que les propriétés dans les messages reçus peuvent être remplacées. |
 | Le message doit être signé | Exige que les messages soient signés numériquement. Configurez le certificat public du partenaire invité pour la vérification de signature.  |
 | Le message doit être chiffré | Exige que les messages soient chiffrés. Les messages non chiffrés seront rejetés. Configurez le certificat privé du partenaire hôte pour déchiffrer les messages.  |
 | Le message doit être compressé | Exige que les messages soient compressés. Les messages non compressés seront rejetés. |
 | Texte du MDN | La notification de réception du message (MDN) par défaut à envoyer à l’expéditeur du message. |
 | Send MDN (Envoyer MDN) | Exige que le MDN soit envoyé. |
 | Send signed MDN (Envoyer MDN signé) | Exige que le MDN soit signé. |
 | MIC Algorithm (Algorithme MIC) | |
 | Send asynchronous MDN (Envoyer MDN asynchrone) | Exige que les messages soient envoyés de manière asynchrone. |
 | URL | L’URL à laquelle doivent être envoyés les MDN. |

8. Pour configurer la façon dont les messages envoyés dans le cadre de ce contrat doivent être traités, procédez comme suit :

 a. Sélectionnez **Paramètres d’envoi**.  

 b. Pour envoyer des messages signés au partenaire, cochez la case **Enable message signing** (Activer la signature des messages). Si vous sélectionnez cette option, vous devrez signer les messages en sélectionnant **l’algorithme MIC de certificat privé du partenaire hôte** dans la liste **Algorithme MIC** et le **certificat privé du partenaire hôte** dans la liste **Certificat**.

 c. Pour envoyer des messages chiffrés au partenaire, cochez la case **Enable message encryption** (Activer le chiffrement des messages). Si vous sélectionnez cette option, vous devrez chiffrer les messages en sélectionnant **l’algorithme de certificat public du partenaire invité** dans la liste **Algorithme de chiffrement** et le **certificat public du partenaire invité** dans la liste **Certificat**.

 d. Pour compresser les messages, cochez la case **Enable message compression** (Activer la compression des messages).

 e. Pour dérouler l’en-tête de type de contenu HTTP sur une seule ligne, cochez la case **Dérouler les en-têtes HTTP**.

 f. Pour recevoir un MDN synchrone pour les messages envoyés, cochez la case **Exiger le MDN**.

 g. Pour recevoir un MDN signé pour les messages envoyés, cochez la case **Exiger le MDN signé**.

 h. Pour recevoir un MDN asynchrone pour les messages envoyés, cochez la case **Exiger le MDN asynchrone**. Si vous sélectionnez cette option, saisissez l’URL à laquelle doivent être envoyés les MDN.  

 i. Pour exiger la non-répudiation de réception, cochez la case **Enable NRR** (Activer NRR).

 j. Sélectionnez **OK**.

 ![Définissez les propriétés de « Paramètres d’envoi »](./media/logic-apps-enterprise-integration-agreements/agreement-5.png)  

 Le tableau suivant décrit les paramètres de **Paramètres d’envoi** :  

 | Propriété | Description |
 | --- | --- |
 | Enable message signing (Activer la signature des messages) | Exige que tous les messages envoyés à partir du contrat soient signés. |
 | MIC Algorithm (Algorithme MIC) | L’algorithme à utiliser pour signer les messages. Configurez l’algorithme MIC de certificat privé du partenaire hôte pour signer les messages. |
 | Certificat | Le certificat à utiliser pour signer les messages. Configurez le certificat privé du partenaire hôte pour signer les messages. |
 | Enable message encryption (Activer le chiffrement du message) | Exige que tous les messages envoyés à partir du contrat soient chiffrés. Configure l’algorithme de certificat public du partenaire invité pour chiffrer les messages. |
 | Algorithme de chiffrement | L’algorithme de chiffrement à utiliser pour le chiffrement des messages. Configure le certificat public du partenaire invité pour chiffrer les messages. |
 | Certificat | Le certificat à utiliser pour chiffrer les messages. Configure le certificat privé du partenaire invité pour chiffrer les messages. |
 | Enable message compression (Activer la compression des messages) | Exige que tous les messages envoyés à partir du contrat soient compressés. |
 | Dérouler les en-têtes HTTP | Déroule l’en-tête de type de contenu HTTP sur une seule ligne. |
 | Exiger le MDN | Exige l’envoi d’un MDN pour tous les messages envoyés à partir du contrat. |
 | Exiger le MDN signé | Exige que tous les MDN envoyés dans le cadre de ce contrat soient signés. |
 | Exiger le MDN asynchrone | Exige l’envoi d’un MDN asynchrone à ce contrat. |
 | URL | L’URL à laquelle doivent être envoyés les MDN. |
 | Enable NRR (Activer NRR) | Exige la non-répudiation de réception (NRR), un attribut de communication qui fournit une preuve de réception des données. |

## <a name="view-the-agreements-list"></a>Afficher la liste des contrats
Pour afficher le nouveau contrat ajouté, sélectionnez la mosaïque **Contrats** dans le panneau **Compte d’intégration**.

![Affichez la liste des contrats](./media/logic-apps-enterprise-integration-agreements/agreement-6.png)

## <a name="next-steps"></a>Étapes suivantes
* [En savoir plus sur Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Découvrez Enterprise Integration Pack")  



<!--HONumber=Feb17_HO1-->


