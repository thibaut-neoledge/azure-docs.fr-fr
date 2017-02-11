---
title: Ajouter le connecteur Informix dans vos applications logiques | Microsoft Docs
description: "Vue d’ensemble du connecteur Informix avec les paramètres d’API REST"
services: 
documentationcenter: 
author: gplarsen
manager: erikre
editor: 
tags: connectors
ms.assetid: ca2393f0-3073-4dc2-8438-747f5bc59689
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/26/2016
ms.author: plarsen
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 614400a8787fdd2081fa8e981c0fc6b6dd794a58


---
# <a name="get-started-with-the-informix-connector"></a>Prise en main du connecteur Informix
Microsoft Connector for Informix connecte Logic Apps aux ressources stockées dans une base de données IBM Informix. Le connecteur Informix inclut un client Microsoft permettant de communiquer avec les ordinateurs serveur Informix distants via un réseau TCP/IP. Cela inclut les bases de données cloud, telles que IBM Informix pour Windows en cours d’exécution dans la virtualisation Azure et les bases de données locales utilisant la passerelle de données locale. Consultez la [liste prise en charge](connectors-create-api-informix.md#supported-informix-platforms-and-versions) des plates-formes et versions IBM Informix (dans cette rubrique).

> [!NOTE]
> Cette version de l’article s’applique à la disponibilité générale des applications logiques. 
> 
> 

Le connecteur prend en charge les opérations de base de données suivantes :

* énumération des tables de base de données ;
* lecture d’une ligne à l’aide de l’instruction SELECT ;
* lecture de toutes les lignes à l’aide de l’instruction SELECT ;
* ajout d’une ligne à l’aide de l’instruction INSERT ;
* modification d’une ligne à l’aide de l’instruction UPDATE ;
* suppression d’une ligne à l’aide de l’instruction DELETE.

Cette rubrique décrit comment utiliser le connecteur dans une application logique pour traiter les opérations de base de données.

Pour plus d’informations sur Logic Apps, voir [Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="available-actions"></a>Actions disponibles
Ce connecteur prend en charge les actions d’application logique suivantes :

* Getables
* GetRow
* GetRows
* InsertRow
* UpdateRow
* DeleteRow

## <a name="list-tables"></a>Affichage de la liste des tables
La création d’une application logique pour une opération quelconque implique de nombreuses étapes exécutées par le biais du Portail Microsoft Azure.

Dans l’application logique, vous pouvez ajouter une action pour répertorier les tables dans une base de données Informix. Cette action indique au connecteur de traiter une instruction de schéma Informix, telle que `CALL SYSIBM.SQLTABLES`.

### <a name="create-a-logic-app"></a>Créer une application logique
1. Dans le **Panneau de démarrage Azure**, sélectionnez **+** (signe plus), **Web + mobile**, puis **Application logique**.
2. Entrez le **Nom**, tel que `InformixgetTables`, **Abonnement**, **Groupe de ressources**, **Emplacement** et **Plan App Service**. Sélectionnez **Épingler au tableau de bord**, puis sélectionnez **Créer**.

### <a name="add-a-trigger-and-action"></a>Ajouter un déclencheur et une action
1. Dans **Concepteur d’applications logiques**, sélectionnez **Blank LogicApp** (Application logique vide) dans la liste **Modèles**.
2. Dans la liste **déclencheurs**, sélectionnez **Périodicité**. 
3. Dans le déclencheur **Périodicité**, sélectionnez **Modifier**, sélectionnez la liste déroulante **Fréquence** et sélectionnez **Jour**, puis sélectionnez **Intervalle** et tapez **7**.  
4. Sélectionnez **+ Nouvelle étape**, puis sélectionnez **Ajouter une action**.
5. Dans la liste **actions**, tapez **informix** dans la zone de modification **Search for more actions** (Rechercher d’autres actions), puis sélectionnez **Informix - Get tables (Preview)** (Informix - Obtenir les tables (version préliminaire)).
   
   ![](./media/connectors-create-api-informix/InformixconnectorActions.png)  
6. Dans le volet de configuration **Informix - Get tables** (Informix - Obtenir les tables), sélectionnez la **case** pour activer l’option **Connect via on-premises data gateway** (Connecter par le biais de la passerelle de données locale). Vous remarquerez que les paramètres de cloud sont remplacés par les paramètres locaux.
   
   * Renseignez la zone **Serveur**sous la forme d’une adresse ou d’un alias suivis d’un caractère deux-points et d’un numéro de port. Par exemple, tapez `ibmserver01:9089`.
   * Renseignez la zone **Base de données**. Par exemple, tapez `nwind`.
   * Renseignez la zone **Authentification**. Par exemple, sélectionnez **De base**.
   * Renseignez la zone **Nom d’utilisateur**. Par exemple, tapez `informix`.
   * Renseignez la zone **Mot de passe**. Par exemple, tapez `Password1`.
   * Renseignez la zone **Passerelle**. Par exemple, sélectionnez **datagateway01**.
7. Sélectionnez **Créer**, puis sélectionnez **Enregistrer**. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)
8. Au niveau du panneau **InformixgetTables**, dans la liste **Toutes les exécutions** figurant sous **Résumé**, sélectionnez le premier élément répertorié (dernière exécution).
9. Dans le panneau **Exécution d’application logique**, sélectionnez **Détails de l’exécution**. Dans la liste **Action**, sélectionnez **Get_tables**. Examinez la zone **État**, qui doit présenter la valeur **Opération réussie**. Sélectionnez le **lien Entrées** pour afficher les entrées. Sélectionnez le **lien Sorties**, et visualisez les sorties, qui doivent inclure une liste de tables.
   
   ![](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

## <a name="create-the-connections"></a>Créer les connexions
Ce connecteur prend en charge les connexions aux bases de données locales et dans le cloud à l’aide des propriétés de connexion ci-après. 

| Propriété | Description |
| --- | --- |
| Serveur |Obligatoire. Accepte une valeur de chaîne représentant une adresse ou un alias TCP/IP, au format IPv4 ou IPv6, suivis d’un caractère deux-points et d’un numéro de port TCP/IP. |
| Base de données |Obligatoire. Accepte une valeur de chaîne représentant un nom de base de données relationnelle DRDA (RDBNAM). Informix accepte une chaîne de 128 octets (la propriété de base de données correspond à un nom de base de données (dbname) IBM Informix). |
| Authentification |facultatif. Accepte la valeur d’élément de liste De base ou Windows (Kerberos). |
| Nom d’utilisateur |Obligatoire. Accepte une valeur de chaîne. |
| Mot de passe |Obligatoire. Accepte une valeur de chaîne. |
| Passerelle |Obligatoire. Accepte une valeur d’élément de liste représentant la passerelle de données locale définie pour Logic Apps dans le groupe de stockage. |

## <a name="create-the-on-premises-gateway-connection"></a>Créer la connexion de passerelle locale
Ce connecteur peut accéder à une base de données Informix locale à l’aide de la passerelle de données locale. Pour plus d’informations, voir les rubriques consacrées aux passerelles. 

1. Dans le volet de configuration **Passerelles**, sélectionnez la **case** pour activer l’option **Connect via on-premises data gateway** (Connecter par le biais de la passerelle de données locale). Vous pouvez remarquer que les paramètres de cloud sont remplacés par les paramètres locaux.
2. Renseignez la zone **Serveur**sous la forme d’une adresse ou d’un alias suivis d’un caractère deux-points et d’un numéro de port. Par exemple, tapez `ibmserver01:9089`.
3. Renseignez la zone **Base de données**. Par exemple, tapez `nwind`.
4. Renseignez la zone **Authentification**. Par exemple, sélectionnez **De base**.
5. Renseignez la zone **Nom d’utilisateur**. Par exemple, tapez `informix`.
6. Renseignez la zone **Mot de passe**. Par exemple, tapez `Password1`.
7. Renseignez la zone **Passerelle**. Par exemple, sélectionnez **datagateway01**.
8. Sélectionnez **Créer** pour continuer. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)

## <a name="create-the-cloud-connection"></a>Créer la connexion cloud
Ce connecteur peut accéder à une base de données Informix cloud. 

1. Dans le volet de configuration **Passerelles**, laissez la **case** décochée pour désactiver l’option **Connect via on-premises data gateway** (Connecter par le biais de la passerelle de données locale). 
2. Renseignez la zone **Nom de la connexion**. Par exemple, tapez `hisdemo2`.
3. Renseignez la zone **Informix server name**(Nom du serveur Informix) sous la forme d’une adresse ou d’un alias suivis d’un caractère deux-points et d’un numéro de port. Par exemple, tapez `hisdemo2.cloudapp.net:9089`.
4. Renseignez la zone **Informix database name**(Nom de la base de données Informix). Par exemple, tapez `nwind`.
5. Renseignez la zone **Nom d’utilisateur**. Par exemple, tapez `informix`.
6. Renseignez la zone **Mot de passe**. Par exemple, tapez `Password1`.
7. Sélectionnez **Créer** pour continuer. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)

## <a name="fetch-all-rows-using-select"></a>Extraire toutes les lignes à l’aide de l’instruction SELECT
Vous pouvez créer une action d’application logique pour extraire toutes les lignes dans la table Informix. Cette action indique au connecteur de traiter une instruction SELECT Informix, telle que `SELECT * FROM AREA`.

### <a name="create-a-logic-app"></a>Créer une application logique
1. Dans le **Panneau de démarrage Azure**, sélectionnez **+** (signe plus), **Web + mobile**, puis **Application logique**.
2. Renseignez les zones **Nom** (par exemple, « **InformixgetRows** »), **Abonnement**, **Groupe de ressources**, **Emplacement** et **Plan App Service**. Sélectionnez **Épingler au tableau de bord**, puis sélectionnez **Créer**.

### <a name="add-a-trigger-and-action"></a>Ajouter un déclencheur et une action
1. Dans **Concepteur d’applications logiques**, sélectionnez **Blank LogicApp** (Application logique vide) dans la liste **Modèles**.
2. Dans la liste **déclencheurs**, sélectionnez **Périodicité**. 
3. Dans le déclencheur **Périodicité**, sélectionnez **Modifier**, sélectionnez la liste déroulante **Fréquence** et sélectionnez **Jour**, puis sélectionnez **Intervalle** et tapez **7**. 
4. Sélectionnez **+ Nouvelle étape**, puis sélectionnez **Ajouter une action**.
5. Dans la liste **actions**, tapez **informix** dans la zone de modification **Search for more actions** (Rechercher d’autres actions), puis sélectionnez **Informix - Get rows (Preview)** (Informix - Obtenir les lignes (version préliminaire)).
6. Dans l’action **Get rows (Preview)** (Obtenir les lignes (version préliminaire)), sélectionnez **Modifier la connexion**.
7. Dans le volet de configuration **Connexions**, sélectionnez **Créer**. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorNewConnection.png)
8. Dans le volet de configuration **Passerelles**, laissez la **case** décochée pour désactiver l’option **Connect via on-premises data gateway** (Connecter par le biais de la passerelle de données locale).
   
   * Renseignez la zone **Nom de la connexion**. Par exemple, tapez `HISDEMO2`.
   * Renseignez la zone **Informix server name**(Nom du serveur Informix) sous la forme d’une adresse ou d’un alias suivis d’un caractère deux-points et d’un numéro de port. Par exemple, tapez `HISDEMO2.cloudapp.net:9089`.
   * Renseignez la zone **Informix database name**(Nom de la base de données Informix). Par exemple, tapez `NWIND`.
   * Renseignez la zone **Nom d’utilisateur**. Par exemple, tapez `informix`.
   * Renseignez la zone **Mot de passe**. Par exemple, tapez `Password1`.
9. Sélectionnez **Créer** pour continuer.
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)
10. Dans la liste **Nom de la table**, sélectionnez la **flèche vers le bas**, puis sélectionnez **AREA**.
11. Si vous le souhaitez, sélectionnez **Afficher les options avancées** pour spécifier les options de requête.
12. Sélectionnez **Enregistrer**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsTableName.png)
13. Au niveau du panneau **InformixgetRows**, dans la liste **Toutes les exécutions** figurant sous **Résumé**, sélectionnez le premier élément répertorié (dernière exécution).
14. Dans le panneau **Exécution d’application logique**, sélectionnez **Détails de l’exécution**. Dans la liste **Action**, sélectionnez **Get_rows**. Examinez la zone **État**, qui doit présenter la valeur **Opération réussie**. Sélectionnez le **lien Entrées** pour afficher les entrées. Sélectionnez le **lien Sorties**, et visualisez les sorties, qui doivent inclure une liste de lignes.
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

## <a name="add-one-row-using-insert"></a>ajout d’une ligne à l’aide de l’instruction INSERT ;
Vous pouvez créer une action d’application logique pour ajouter une ligne dans une table Informix. Cette action indique au connecteur de traiter une instruction INSERT Informix, telle que `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`.

### <a name="create-a-logic-app"></a>Créer une application logique
1. Dans le **Panneau de démarrage Azure**, sélectionnez **+** (signe plus), **Web + mobile**, puis **Application logique**.
2. Entrez le **Nom**, tel que `InformixinsertRow`, **Abonnement**, **Groupe de ressources**, **Emplacement** et **Plan App Service**. Sélectionnez **Épingler au tableau de bord**, puis sélectionnez **Créer**.

### <a name="add-a-trigger-and-action"></a>Ajouter un déclencheur et une action
1. Dans **Concepteur d’applications logiques**, sélectionnez **Blank LogicApp** (Application logique vide) dans la liste **Modèles**.
2. Dans la liste **déclencheurs**, sélectionnez **Périodicité**. 
3. Dans le déclencheur **Périodicité**, sélectionnez **Modifier**, sélectionnez la liste déroulante **Fréquence** et sélectionnez **Jour**, puis sélectionnez **Intervalle** et tapez **7**. 
4. Sélectionnez **+ Nouvelle étape**, puis sélectionnez **Ajouter une action**.
5. Dans la liste **actions**, tapez **informix** dans la zone de modification **Search for more actions** (Rechercher d’autres actions), puis sélectionnez **Informix - Insert row (Preview)** (Informix - Insérer une ligne (version préliminaire)).
6. Dans l’action **Get rows (Preview)** (Obtenir les lignes (version préliminaire)), sélectionnez **Modifier la connexion**. 
7. Dans le volet de configuration **Connexions** , sélectionnez une connexion. Par exemple, sélectionnez **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. Dans la liste **Nom de la table**, sélectionnez la **flèche vers le bas**, puis sélectionnez **AREA**.
9. Entrez des valeurs pour toutes les colonnes requises (signalées par un astérisque rouge). Par exemple, tapez `99999` pour **AREAID**, tapez `Area 99999`, puis tapez la valeur `102` pour **REGIONID**. 
10. Sélectionnez **Enregistrer**.
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowValues.png)
11. Au niveau du panneau **InformixinsertRow**, dans la liste **Toutes les exécutions** figurant sous **Résumé**, sélectionnez le premier élément répertorié (dernière exécution).
12. Dans le panneau **Exécution d’application logique**, sélectionnez **Détails de l’exécution**. Dans la liste **Action**, sélectionnez **Get_rows**. Examinez la zone **État**, qui doit présenter la valeur **Opération réussie**. Sélectionnez le **lien Entrées** pour afficher les entrées. Sélectionnez le **lien Sorties**, et visualisez les sorties, qui doivent inclure la nouvelle ligne.
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

## <a name="fetch-one-row-using-select"></a>Extraire une ligne à l’aide de l’instruction SELECT
Vous pouvez créer une action d’application logique pour extraire une ligne dans une table Informix. Cette action indique au connecteur de traiter une instruction SELECT WHERE Informix, telle que `SELECT FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Créer une application logique
1. Dans le **Panneau de démarrage Azure**, sélectionnez **+** (signe plus), **Web + mobile**, puis **Application logique**.
2. Entrez le **Nom**, tel que `InformixgetRow`, **Abonnement**, **Groupe de ressources**, **Emplacement** et **Plan App Service**. Sélectionnez **Épingler au tableau de bord**, puis sélectionnez **Créer**.

### <a name="add-a-trigger-and-action"></a>Ajouter un déclencheur et une action
1. Dans **Concepteur d’applications logiques**, sélectionnez **Blank LogicApp** (Application logique vide) dans la liste **Modèles**.
2. Dans la liste **déclencheurs**, sélectionnez **Périodicité**. 
3. Dans le déclencheur **Périodicité**, sélectionnez **Modifier**, sélectionnez la liste déroulante **Fréquence** et sélectionnez **Jour**, puis sélectionnez **Intervalle** et tapez **7**. 
4. Sélectionnez **+ Nouvelle étape**, puis sélectionnez **Ajouter une action**.
5. Dans la liste **actions**, tapez **informix** dans la zone de modification **Search for more actions** (Rechercher d’autres actions), puis sélectionnez **Informix - Get rows (Preview)** (Informix - Obtenir les lignes (version préliminaire)).
6. Dans l’action **Get rows (Preview)** (Obtenir les lignes (version préliminaire)), sélectionnez **Modifier la connexion**. 
7. Dans le volet de configuration **Connexions** , sélectionnez une connexion existante. Par exemple, sélectionnez **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. Dans la liste **Nom de la table**, sélectionnez la **flèche vers le bas**, puis sélectionnez **AREA**.
9. Entrez des valeurs pour toutes les colonnes requises (signalées par un astérisque rouge). Par exemple, tapez `99999` pour **AREAID**. 
10. Si vous le souhaitez, sélectionnez **Afficher les options avancées** pour spécifier les options de requête.
11. Sélectionnez **Enregistrer**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowValues.png)
12. Au niveau du panneau **InformixgetRow**, dans la liste **Toutes les exécutions** figurant sous **Résumé**, sélectionnez le premier élément répertorié (dernière exécution).
13. Dans le panneau **Exécution d’application logique**, sélectionnez **Détails de l’exécution**. Dans la liste **Action**, sélectionnez **Get_rows**. Examinez la zone **État**, qui doit présenter la valeur **Opération réussie**. Sélectionnez le **lien Entrées** pour afficher les entrées. Sélectionnez le **lien Sorties**, et visualisez les sorties, qui doivent inclure une ligne.
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

## <a name="change-one-row-using-update"></a>Modifier une ligne à l’aide de l’instruction UPDATE
Vous pouvez créer une action d’application logique pour modifier une ligne dans une table Informix. Cette action indique au connecteur de traiter une instruction UPDATE Informix, telle que `UPDATE AREA SET AREAID = '99999', AREADESC = 'Area 99999', REGIONID = 102)`.

### <a name="create-a-logic-app"></a>Créer une application logique
1. Dans le **Panneau de démarrage Azure**, sélectionnez **+** (signe plus), **Web + mobile**, puis **Application logique**.
2. Entrez le **Nom**, tel que `InformixupdateRow`, **Abonnement**, **Groupe de ressources**, **Emplacement** et **Plan App Service**. Sélectionnez **Épingler au tableau de bord**, puis sélectionnez **Créer**.

### <a name="add-a-trigger-and-action"></a>Ajouter un déclencheur et une action
1. Dans **Concepteur d’applications logiques**, sélectionnez **Blank LogicApp** (Application logique vide) dans la liste **Modèles**.
2. Dans la liste **déclencheurs**, sélectionnez **Périodicité**. 
3. Dans le déclencheur **Périodicité**, sélectionnez **Modifier**, sélectionnez la liste déroulante **Fréquence** et sélectionnez **Jour**, puis sélectionnez **Intervalle** et tapez **7**. 
4. Sélectionnez **+ Nouvelle étape**, puis sélectionnez **Ajouter une action**.
5. Dans la liste **actions**, tapez **informix** dans la zone de modification **Search for more actions** (Rechercher d’autres actions), puis sélectionnez **Informix - Update row (Preview)** (Informix - Mettre à jour une ligne (version préliminaire)).
6. Dans l’action **Get rows (Preview)** (Obtenir les lignes (version préliminaire)), sélectionnez **Modifier la connexion**. 
7. Dans le volet de configuration **Connexions** , sélectionnez une connexion existante. Par exemple, sélectionnez **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. Dans la liste **Nom de la table**, sélectionnez la **flèche vers le bas**, puis sélectionnez **AREA**.
9. Entrez des valeurs pour toutes les colonnes requises (signalées par un astérisque rouge). Par exemple, tapez `99999` pour **AREAID**, tapez `Updated 99999`, puis tapez la valeur `102` pour **REGIONID**. 
10. Sélectionnez **Enregistrer**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowValues.png)
11. Au niveau du panneau **InformixupdateRow**, dans la liste **Toutes les exécutions** figurant sous **Résumé**, sélectionnez le premier élément répertorié (dernière exécution).
12. Dans le panneau **Exécution d’application logique**, sélectionnez **Détails de l’exécution**. Dans la liste **Action**, sélectionnez **Get_rows**. Examinez la zone **État**, qui doit présenter la valeur **Opération réussie**. Sélectionnez le **lien Entrées** pour afficher les entrées. Sélectionnez le **lien Sorties**, et visualisez les sorties, qui doivent inclure la nouvelle ligne.
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

## <a name="remove-one-row-using-delete"></a>suppression d’une ligne à l’aide de l’instruction DELETE.
Vous pouvez créer une action d’application logique pour supprimer une ligne dans une table Informix. Cette action indique au connecteur de traiter une instruction DELETE Informix, telle que `DELETE FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Créer une application logique
1. Dans le **Panneau de démarrage Azure**, sélectionnez **+** (signe plus), **Web + mobile**, puis **Application logique**.
2. Entrez le **Nom**, tel que `InformixdeleteRow`, **Abonnement**, **Groupe de ressources**, **Emplacement** et **Plan App Service**. Sélectionnez **Épingler au tableau de bord**, puis sélectionnez **Créer**.

### <a name="add-a-trigger-and-action"></a>Ajouter un déclencheur et une action
1. Dans **Concepteur d’applications logiques**, sélectionnez **Blank LogicApp** (Application logique vide) dans la liste **Modèles**.
2. Dans la liste **déclencheurs**, sélectionnez **Périodicité**. 
3. Dans le déclencheur **Périodicité**, sélectionnez **Modifier**, sélectionnez la liste déroulante **Fréquence** et sélectionnez **Jour**, puis sélectionnez **Intervalle** et tapez **7**. 
4. Sélectionnez **+ Nouvelle étape**, puis sélectionnez **Ajouter une action**.
5. Dans la liste **actions**, tapez **informix** dans la zone de modification **Search for more actions** (Rechercher d’autres actions), puis sélectionnez **Informix - Delete row (Preview)** (Informix - Supprimer une ligne (version préliminaire)).
6. Dans l’action **Get rows (Preview)** (Obtenir les lignes (version préliminaire)), sélectionnez **Modifier la connexion**. 
7. Dans le volet de configuration **Connexions** , sélectionnez une connexion existante. Par exemple, sélectionnez **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. Dans la liste **Nom de la table**, sélectionnez la **flèche vers le bas**, puis sélectionnez **AREA**.
9. Entrez des valeurs pour toutes les colonnes requises (signalées par un astérisque rouge). Par exemple, tapez `99999` pour **AREAID**. 
10. Sélectionnez **Enregistrer**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowValues.png)
11. Au niveau du panneau **InformixdeleteRow**, dans la liste **Toutes les exécutions** figurant sous **Résumé**, sélectionnez le premier élément répertorié (dernière exécution).
12. Dans le panneau **Exécution d’application logique**, sélectionnez **Détails de l’exécution**. Dans la liste **Action**, sélectionnez **Get_rows**. Examinez la zone **État**, qui doit présenter la valeur **Opération réussie**. Sélectionnez le **lien Entrées** pour afficher les entrées. Sélectionnez le **lien Sorties**, et visualisez les sorties, qui doivent inclure la ligne supprimée.
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="technical-details"></a>Détails techniques
## <a name="actions"></a>Actions
Une action est une opération effectuée par le flux de travail défini dans une application logique. Le connecteur de base de données Informix inclut les actions suivantes. 

| Action | Description |
| --- | --- |
| [GetRow](connectors-create-api-informix.md#get-row) |Récupère une ligne unique d’une table Informix |
| [GetRows](connectors-create-api-informix.md#get-rows) |Récupère les lignes d’une table Informix |
| [InsertRow](connectors-create-api-informix.md#insert-row) |Insère une nouvelle ligne dans une table Informix |
| [DeleteRow](connectors-create-api-informix.md#delete-row) |Supprime une ligne d’une table Informix |
| [GetTables](connectors-create-api-informix.md#get-tables) |Récupère les tables d’une base de données Informix |
| [UpdateRow](connectors-create-api-informix.md#update-row) |Met à jour une ligne existante dans une table Informix |

### <a name="action-details"></a>Détails de l’action
Dans cette section, consultez les détails relatifs à chaque action, y compris toutes les propriétés d’entrée requises ou facultatives et toute sortie correspondante associée au connecteur.

#### <a name="get-row"></a>Obtenir une ligne
Récupère une ligne unique d’une table Informix.  

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| table * |Nom de la table |Nom de la table Informix |
| id * |ID de la ligne |Identificateur unique de la ligne à récupérer |

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails des résultats
Item

| Nom de la propriété | Type de données |
| --- | --- |
| ItemInternalId |string |

#### <a name="get-rows"></a>Obtenir des lignes
Récupère les lignes d’une table Informix.  

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| table* |Nom de la table |Nom de la table Informix |
| $skip |Nombre à ignorer |Nombre d’entrées à ignorer (valeur par défaut : 0) |
| $top |Nombre maximal à récupérer |Nombre maximal d’entrées à récupérer (valeur par défaut : 256) |
| $filter |Requête de filtre |Requête filter ODATA pour limiter le nombre d’entrées |
| $orderby |Trier par |Requête orderBy ODATA pour spécifier l’ordre des entrées |

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails des résultats
ItemsList

| Nom de la propriété | Type de données |
| --- | --- |
| value |array |

#### <a name="insert-row"></a>Insérer une ligne
Insère une nouvelle ligne dans une table Informix.  

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| table* |Nom de la table |Nom de la table Informix |
| item* |Ligne |Ligne à insérer dans la table Informix spécifiée |

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails des résultats
Item

| Nom de la propriété | Type de données |
| --- | --- |
| ItemInternalId |string |

#### <a name="delete-row"></a>Supprimer la ligne
Supprime une ligne d’une table Informix.  

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| table* |Nom de la table |Nom de la table Informix |
| id* |ID de la ligne |Identificateur unique de la ligne à supprimer |

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails des résultats
Aucune.

#### <a name="get-tables"></a>Obtenir des tables
Récupère les tables d’une base de données Informix.  

Il n'existe aucun paramètre pour cet appel. 

##### <a name="output-details"></a>Détails des résultats
TablesList

| Nom de la propriété | Type de données |
| --- | --- |
| value |array |

#### <a name="update-row"></a>Mettre à jour une ligne
Met à jour une ligne existante dans une table Informix.  

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| table* |Nom de la table |Nom de la table Informix |
| id* |ID de la ligne |Identificateur unique de la ligne à mettre à jour |
| item* |Ligne |Ligne avec valeurs mises à jour |

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails des résultats
Item

| Nom de la propriété | Type de données |
| --- | --- |
| ItemInternalId |string |

### <a name="http-responses"></a>Réponses HTTP
Lorsque vous exécutez des appels de diverses actions, vous pouvez obtenir certaines réponses. Le tableau suivant présente les réponses et leurs descriptions :  

| Nom | Description |
| --- | --- |
| 200 |OK |
| 202 |Acceptée |
| 400 |Demande incorrecte |
| 401 |Non autorisé |
| 403 |Interdit |
| 404 |Introuvable |
| 500 |Erreur interne du serveur. Une erreur inconnue s'est produite |
| default |L’opération a échoué. |

## <a name="supported-informix-platforms-and-versions"></a>Plateformes et versions Informix prises en charge
Ce connecteur prend en charge les versions IBM Informix ci-après en cas de configuration pour la prise en charge des connexions client DRDA (Distributed Relational Database Architecture).

* IBM Informix 12.1
* IBM Informix 11.7

## <a name="next-steps"></a>Étapes suivantes
[Créez une application logique](../logic-apps/logic-apps-create-a-logic-app.md). Explorez les autres connecteurs disponibles dans les applications logiques en consultant notre [liste d’API](apis-list.md).




<!--HONumber=Feb17_HO2-->


