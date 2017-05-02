---
title: "Récupération d’urgence de compte d’intégration Logic Apps B2B Integration - Azure Logic Apps | Microsoft Docs"
description: "Récupération d’urgence Azure Logic Apps B2B"
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
ms.date: 04/10/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: 0ebaa553f360cc7deb52139fe9adaaca39a2ee23
ms.lasthandoff: 04/13/2017


---

# <a name="logic-apps-b2b-cross-region-disaster-recovery"></a>Récupération d’urgence inter-régions Logic Apps B2B
Les charges de travail B2B impliquent des transactions monétaires telles que des commandes, des factures.  Pour les entreprises, une récupération rapide est essentielle pour respecter les SLA de niveau entreprise convenus avec leurs partenaires lors d’un sinistre.  Cette rubrique montre la création d’un plan de continuité des activités pour les charges de travail B2B.  Elle couvre ce qui suit

* Créer un compte d’intégration dans une région secondaire
* Établir une connexion entre la région primaire et la région secondaire 
* Basculer vers la région secondaire lors d’un sinistre
* Revenir à la région primaire après sinistre

## <a name="create-an-integration-account-in-secondary-region"></a>Créer un compte d’intégration dans une région secondaire
1. Sélectionnez une région secondaire et créez un [compte d’intégration](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).  

2. Ajoutez des partenaires, des schémas et des contrats pour les flux de messages requis où l’état d’exécution doit être répliqué sur le compte d’intégration de la région secondaire.

    > [!Tip]
    > Garantir la cohérence entre les régions dans la convention d’affectation de noms pour les artefacts de compte d’intégration 
    > 
    > 

3. Il est recommandé de déployer toutes les ressources de la région primaire (par exemple les bases de données SQL Azure ou DocumentDB, ou Service Bus / Event Hubs utilisés pour la messagerie, APIM, Logic Apps) dans la région secondaire aussi.  

## <a name="establish-a-connection-from-primary-to-secondary"></a>Établir une connexion entre primaire et secondaire 
Pour extraire l’état d’exécution de la région primaire, créez une application logique dans la région secondaire.  Il doit avoir un **déclencheur** et une **action**.  Le déclencheur doit se connecter au compte d’intégration de la région primaire tandis que l’action doit se connecter au compte d’intégration de la région secondaire.  Selon l’intervalle de temps, le déclencheur interroge la table d’état d’exécution de la région primaire, extrait les nouveaux enregistrements le cas échéant et met à jour leurs actions dans le compte d’intégration de la région secondaire. Cela permet d’obtenir un état de runtime incrémentiel depuis la région primaire vers la région secondaire.

La continuité des activités dans le compte d’intégration Logic Apps est conçue pour prendre en charge des protocoles basés sur B2B : X12, AS2 et EDIFACT.  Pour une procédure détaillée, sélectionnez les liens correspondants.

* [X12](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#x12)
* [AS2](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#as2)
* EDIFACT (prochainement)

## <a name="fail-over-to-secondary-region-during-a-disaster-event"></a>Basculer vers la région secondaire lors d’un sinistre
Lors d’un sinistre, lorsque la région primaire n’est pas disponible pour la continuité des activités, dirigez le trafic vers la région secondaire. La région secondaire permet de récupérer rapidement des fonctions métier pour répondre aux objectifs de délai de récupération ou point de récupération (RTO/RPO) convenus avec les partenaires.  Réduit en outre les efforts pour basculer d’une région à l’autre. 

Il existe une latence attendue lorsque des numéros de contrôle sont copiés depuis la région primaire vers la région secondaire.  Pour ne pas envoyer de numéros de contrôle générés en double aux partenaires lors d’un sinistre, il est recommandé d’augmenter les numéros de contrôle dans les **accords de région secondaire** au moyen d’[applets de commande PowerShell](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery).

## <a name="fall-back-to-primary-region-post-disaster-event"></a>Revenir à la région primaire après sinistre
Lorsque la région primaire est disponible, suivre la procédure suivante pour y revenir     
* Cesser d’accepter les messages des partenaires dans la **région secondaire**   
* Augmenter les numéros de contrôle générés pour tous les **accords de région primaire** au moyen d’[applets de commande PowerShell](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery)   
* Diriger le trafic de la région secondaire vers la région primaire   
* Vérifier que l’application logique créée dans la région secondaire pour extraire l’état d’exécution de la région primaire est activée    

## <a name="x12"></a>X 12 
La continuité des activités pour les documents EDI X12 documents repose sur les numéros de contrôle   
* Numéros de contrôle reçus (messages entrants) de la part des partenaires  
* Numéros de contrôle générés (messages sortants) et envoyés aux partenaires  
    
    > [!Tip]
    > Vous pouvez également utiliser un [modèle de démarrage rapide X12](https://azure.microsoft.com/documentation/templates/201-logic-app-x12-disaster-recovery-replication/) pour créer des applications logiques.  La création d’un compte d’intégration primaire et d’un compte d’intégration secondaire est nécessaire pour utiliser le modèle.  Le modèle permet de créer 2 applications logiques, une pour le numéro de contrôle reçu et une autre pour le numéro de contrôle généré.  Les déclencheurs et actions respectifs sont créés dans les applications logiques, le déclencheur est connecté au compte d’intégration primaire et l’action au compte d’intégration secondaire.
    > 
    >

### <a name="control-numbers-received-from-the-partners"></a>Numéros de contrôle reçus de la part des partenaires
1. Créer une [application logique](../logic-apps/logic-apps-create-a-logic-app.md) dans la région secondaire   

2. Rechercher **X12** et sélectionner **X12 - lorsqu’un numéro de contrôle reçu est modifié**    
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN1.png)

3. La sélection du déclencheur invite à établir une connexion au compte d’intégration. Déclencheur à connecter au compte d’intégration de la région primaire.  Donnez un nom de connexion, sélectionnez votre **compte d’intégration de la région primaire** dans la liste et cliquez sur Créer.   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN2.png)

4. Pour démarrer la synchronisation des numéros de contrôle, DateTime est facultatif.  La fréquence peut être définie sur **Jour**, **Heure**, **Minute** ou **Seconde** avec un intervalle.    
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN3.png)

5. Cliquer sur **Nouvelle étape** et **Ajouter une action**    
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN4.png)

6. Rechercher **X12** et sélectionner **X12 - Ajouter ou mettre à jour un numéro de contrôle reçu**   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN5.png)

7. Action à connecter au compte d’intégration secondaire.  Sélectionnez **Modifier la connexion** et **Ajouter une nouvelle connexion** pour répertorier les comptes d’intégration disponibles.  Donnez un nom de connexion, sélectionnez votre **compte d’intégration de la région secondaire** dans la liste et cliquez sur Créer.     
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN6.png)

8. Sélectionner le contenu dynamique et enregistrer l’application logique   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN7.png)

9. Selon l’intervalle de temps, le déclencheur interroge la table de numéro de contrôle de la région primaire, extrait les nouveaux enregistrements et met à jour leurs actions dans le compte d’intégration de la région secondaire.  S’il n’y a aucune mise à jour, le déclencheur affiche l’état ignoré.
    
    > [!Tip]
    > L’activation de la vérification des doublons sur les paramètres de réception de contrat conserve l’état d’exécution des numéros de contrôle reçus et permet de déclencher les déclencheurs à intervalles réguliers
    > 
    >

![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN8.png)


### <a name="control-numbers-generated-and-send-to-the-partners"></a>Numéros de contrôle générés et envoyés aux partenaires
1. Créer une [application logique](../logic-apps/logic-apps-create-a-logic-app.md) dans la région secondaire  

2. Rechercher **X12** et sélectionner **X12 - Lorsqu’un numéro de contrôle généré est modifié**   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN1.png)

3. La sélection du déclencheur invite à établir une connexion au compte d’intégration. Déclencheur à connecter au compte d’intégration de la région primaire.  Donnez un nom de connexion, sélectionnez votre **compte d’intégration de la région primaire** dans la liste et cliquez sur Créer.   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN2.png) 

4. Pour démarrer la synchronisation des numéros de contrôle, DateTime est facultatif.  La fréquence peut être définie sur **Jour**, **Heure**, **Minute** ou **Seconde** avec un intervalle.   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN3.png)  

5. Cliquer sur **Nouvelle étape** et **Ajouter une action**   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN4.png)

6. Rechercher **X12** et sélectionner **X12 - Ajouter ou mettre à jour un numéro de contrôle généré**   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN5.png)

7. Action à connecter au compte d’intégration secondaire.  Sélectionnez **Modifier la connexion** et **Ajouter une nouvelle connexion** pour répertorier les comptes d’intégration disponibles.  Donnez un nom de connexion, sélectionnez votre **compte d’intégration de la région secondaire** dans la liste et cliquez sur Créer.    
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN6.png)

8. Sélectionner le contenu dynamique et enregistrer l’application logique   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN7.png)

9. Selon l’intervalle de temps, le déclencheur interroge la table de numéro de contrôle de la région primaire, extrait les nouveaux enregistrements et met à jour leurs actions dans le compte d’intégration de la région secondaire.  S’il n’y a aucune mise à jour, le déclencheur affiche l’état ignoré.  
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN8.png)

En fonction de l’intervalle de temps, l’état d’exécution incrémentiel est dupliqué de la région primaire à la région secondaire.  Lors d’un sinistre, lorsque la région primaire n’est pas disponible, dirigez le trafic vers la région secondaire pour la continuité des activités. 

## <a name="as2"></a>AS2 
La continuité des activités pour les documents qui utilisent le protocole AS2 est basée sur l’ID de message et la valeur MIC

> [!Tip]
    > Vous pouvez également utiliser un [modèle de démarrage rapide AS2](https://github.com/Azure/azure-quickstart-templates/pull/3302) pour créer des applications logiques.  La création d’un compte d’intégration primaire et d’un compte d’intégration secondaire est nécessaire pour utiliser le modèle.  Ce modèle permet de créer une application logique qui a un déclencheur et une action.  Crée une connexion entre le déclencheur et le compte d’intégration primaire et entre l’action et le compte d’intégration secondaire.
    > 
    >

1. Créer une [application logique](../logic-apps/logic-apps-create-a-logic-app.md) dans la région secondaire  

2. Rechercher **AS2** et sélectionner **AS2 - Lorsqu’une valeur MIC est créée**   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid1.png)

3. La sélection du déclencheur invite à établir une connexion au compte d’intégration. Déclencheur à connecter au compte d’intégration de la région primaire.  Donnez un nom de connexion, sélectionnez votre **compte d’intégration de la région primaire** dans la liste et cliquez sur Créer.   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid2.png)

4. Pour démarrer la synchronisation des valeurs MIC, DateTime est facultatif.  La fréquence peut être définie sur **Jour**, **Heure**, **Minute** ou **Seconde** avec un intervalle.   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid3.png)

5. Cliquer sur **Nouvelle étape** et **Ajouter une action**   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid4.png)

6. Rechercher **AS2** et sélectionner **AS2 - Ajouter ou mettre à jour un MIC**   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid5.png)

7. Action à connecter au compte d’intégration secondaire.  Sélectionnez **Modifier la connexion** et **Ajouter une nouvelle connexion** pour répertorier les comptes d’intégration disponibles.  Donnez un nom de connexion, sélectionnez votre **compte d’intégration de la région secondaire** dans la liste et cliquez sur Créer.    
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid6.png)

8. Sélectionner le contenu dynamique et enregistrer l’application logique   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid7.png)

9. En fonction de l’intervalle de temps, le déclencheur interroge la table de la région primaire, extrait les nouveaux enregistrements et met à jour leurs actions dans le compte d’intégration de la région secondaire.  S’il n’y a aucune mise à jour, le déclencheur affiche l’état ignoré.  
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid8.png)

En fonction de l’intervalle de temps, l’état d’exécution incrémentiel est dupliqué de la région primaire à la région secondaire.  Lors d’un sinistre, lorsque la région primaire n’est pas disponible, dirigez le trafic vers la région secondaire pour la continuité des activités. 

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur le [suivi des messages B2B](logic-apps-monitor-b2b-message.md).   
