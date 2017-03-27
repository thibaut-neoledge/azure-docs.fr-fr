---
title: "Décoder des messages EDIFACT - Azure Logic Apps | Microsoft Docs"
description: "Validation EDI et génération du code XML des documents informatisés avec le décodeur EDIFACT Message dans Enterprise Integration Pack pour Azure Logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 0e61501d-21a2-4419-8c6c-88724d346e81
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 176963837f4f3fc8b89e31000ef8722ef3258b11
ms.lasthandoff: 03/10/2017


---

# <a name="decode-edifact-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Décodez des messages EDIFACT pour Azure Logic Apps avec Enterprise Integration Pack

Avec le connecteur Decode EDIFACT Message, vous pouvez valider l’EDI et les propriétés spécifiques au partenaire, générer un document XML pour chaque document informatisé, ainsi qu’un accusé de réception pour la transaction traitée. Pour utiliser ce connecteur, vous devez ajouter le connecteur à un déclencheur existant dans votre application logique.

## <a name="before-you-start"></a>Avant de commencer

Voici les éléments dont vous avez besoin :

* Un compte Azure (que vous pouvez [créer gratuitement)](https://azure.microsoft.com/free)
* Un [compte d’intégration](logic-apps-enterprise-integration-create-integration-account.md) déjà défini et associé à votre abonnement Azure. Vous devez disposer d’un compte d’intégration pour pouvoir utiliser le connecteur Decode EDIFACT Message. 
* Au moins deux [partenaires](logic-apps-enterprise-integration-partners.md) déjà définis dans votre compte d’intégration
* Un [contrat EDIFACT](logic-apps-enterprise-integration-edifact.md) déjà défini dans votre compte d’intégration

## <a name="decode-edifact-messages"></a>Messages Decode EDIFACT

1. [Créer une application logique](logic-apps-create-a-logic-app.md).

2. Le connecteur Decode EDIFACT Message ne possède aucun déclencheur, ce qui signifie que vous devez ajouter un déclencheur pour le démarrage de votre application logique, par exemple un déclencheur de requête. Dans le concepteur d’applications logiques, ajoutez un déclencheur, puis ajoutez une action à votre application logique.

3.    Dans la zone de recherche, entrez « EDIFACT » comme filtre. Sélectionnez **Decode EDIFACT Message**.
   
    ![recherche EDIFACT](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage1.png)

3. Si vous n’avez pas encore créé de connexions à votre compte d’intégration, vous êtes invité à le faire à cette étape. Donnez un nom à votre connexion, puis sélectionnez le compte d’intégration auquel vous souhaitez vous connecter.
   
    ![créer un compte d’intégration](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage2.png)

    Les propriétés marquées d’un astérisque sont obligatoires.

    | Propriété | Détails |
    | --- | --- |
    | Nom de connexion * |Entrez un nom pour votre connexion. |
    | Compte d’intégration * |Entrez un nom pour votre compte d’intégration. Vérifiez que votre compte d’intégration et votre application logique se trouvent dans le même emplacement Azure. |

4.    Pour terminer la création de votre connexion, sélectionnez l’option **Créer**. Les détails de votre connexion doivent apparaître tels qu’indiqués dans l’exemple suivant :

    ![détails du compte d’intégration](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage3.png)  

5. Une fois votre connexion est créée, comme indiqué dans cet exemple, sélectionnez le message de fichier plat EDIFACT à décoder.

    ![connexion de compte d’intégration créée](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage4.png)  

    Par exemple :

    ![Sélectionner le message de fichier plat EDIFACT à décoder](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage5.png)  

## <a name="edifact-decoder-details"></a>Détails sur le décodeur EDIFACT

Le connecteur Decode EDIFACT effectue les tâches suivantes : 

* Résout le contrat en mettant en correspondance les identificateurs et les qualificateurs de l’expéditeur et du récepteur
* Fractionne plusieurs échanges d’un seul message dans des messages distincts.
* Valide l’enveloppe par rapport au contrat de partenariat commercial
* Désassemble l’échange.
* Valide l’EDI et les propriétés spécifiques au partenaire, y compris
  * Validation de la structure de l’enveloppe d’échange.
  * Validation de schéma de l’enveloppe par rapport au schéma de contrôle.
  * Validation de schéma des éléments de données du document informatisé par rapport au schéma de message.
  * Validation EDI effectuée sur les éléments de données du document informatisé
* Vérifie que les numéros de contrôle de l’échange, du groupe et du document informatisé ne sont pas des doublons (si configuré) 
  * Vérifie le numéro de contrôle de l’échange par rapport aux échanges reçus précédemment. 
  * Vérifie le numéro de contrôle du groupe par rapport aux autres numéros de contrôle de groupe dans l’échange. 
  * Vérifie le numéro de contrôle du document informatisé par rapport aux autres numéros de contrôle de document informatisé dans ce groupe.
* Génère un document XML pour chaque document informatisé.
* Convertit la totalité de l’échange au format XML 
  * Fractionne l’échange en documents informatisés - suspend les documents informatisés en cas d’erreur : analyse chaque document informatisé d’un échange dans un document XML distinct. Si la validation d’un ou plusieurs documents informatisés de l’échange échoue, EDIFACT Decode suspend uniquement ces documents informatisés. 
  * Fractionne l’échange en documents informatisés - suspend l’échange en cas d’erreur : analyse chaque document informatisé d’un échange dans un document XML distinct.  Si la validation d’un ou plusieurs documents informatisés de l’échange échoue, EDIFACT Decode suspend l’intégralité de l’échange.
  * Préserve l’échange - suspend les documents informatisés en cas d’erreur : crée un document XML pour l’intégralité de l’échange par lot. EDIFACT Decode suspend uniquement les documents informatisés dont la validation échoue, tout en continuant à traiter tous les autres documents informatisés
  * Préserve l’échange - suspend l’échange en cas d’erreur : crée un document XML pour l’intégralité de l’échange par lot. Si la validation d’un ou plusieurs documents informatisés de l’échange échoue, EDIFACT Decode suspend l’intégralité de l’échange, 
* Génère un accusé de réception fonctionnel et/ou technique (contrôle) (si configuré).
  * Un accusé de réception technique ou l’ACK CONTRL renvoie les résultats d’une vérification syntaxique de tout l’échange reçu.
  * Un accusé de réception fonctionnel accuse réception de l’acceptation ou du refus d’un groupe ou d’un échange reçu

## <a name="next-steps"></a>Étapes suivantes
[En savoir plus sur Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Découvrez Enterprise Integration Pack") 


