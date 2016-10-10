<properties
    pageTitle="Ajouter le connecteur DB2 dans vos applications logiques | Microsoft Azure"
    description="Vue d’ensemble du connecteur DB2 avec les paramètres d’API REST"
    services=""
    documentationCenter="" 
    authors="gplarsen"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="09/26/2016"
   ms.author="plarsen"/>


# Prise en main du connecteur DB2
Le connecteur Microsoft pour DB2 connecte Logic Apps aux ressources stockées dans une base de données IBM DB2. Ce connecteur inclut un client Microsoft permettant de communiquer avec les ordinateurs serveurs DB2 distants via un réseau TCP/IP. Cela inclut les bases de données cloud, telles qu’IBM Bluemix dashDB ou IBM DB2 pour Windows en cours d’exécution dans la virtualisation Azure et les bases de données locales utilisant la passerelle de données locale. Consultez la [liste de prise en charge](connectors-create-api-db2.md#supported-db2-platforms-and-versions) des plates-formes et versions IBM DB2 (dans cette rubrique).

>[AZURE.NOTE] Cette version de l’article s’applique à la disponibilité générale des applications logiques.

Le connecteur DB2 prend en charge les opérations de bases de données suivantes :

- énumération des tables de base de données ;
- lecture d’une ligne à l’aide de l’instruction SELECT ;
- lecture de toutes les lignes à l’aide de l’instruction SELECT ;
- ajout d’une ligne à l’aide de l’instruction INSERT ;
- modification d’une ligne à l’aide de l’instruction UPDATE ;
- suppression d’une ligne à l’aide de l’instruction DELETE.

Cette rubrique décrit comment utiliser le connecteur dans une application logique pour traiter les opérations de bases de données.

Pour plus d’informations sur Logic Apps, voir [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Actions disponibles
Le connecteur DB2 prend en charge les actions d’applications logiques suivantes :

- Getables
- GetRow
- GetRows
- InsertRow
- UpdateRow
- DeleteRow


## Affichage de la liste des tables
La création d’une application logique pour une opération quelconque implique de nombreuses étapes exécutées par le biais du Portail Microsoft Azure.

Dans l’application logique, vous pouvez ajouter une action pour répertorier les tables dans une base de données DB2. L’action indique au connecteur de traiter une instruction de schéma DB2, telle que `CALL SYSIBM.SQLTABLES`.

### Créer une application logique
1.	Dans le **Panneau de démarrage Azure**, sélectionnez **+** (signe plus), **Web + mobile**, puis **Application logique**.
2.	Entrez le **Nom**, tel que `Db2getTables`, **Abonnement**, **Groupe de ressources**, **Emplacement** et **Plan App Service**. Sélectionnez **Épingler au tableau de bord**, puis **Créer**.

### Ajouter un déclencheur et une action
1.	Dans **Concepteur d’applications logiques**, sélectionnez **Blank LogicApp** (Application logique vide) dans la liste **Modèles**.
2.	Dans la liste **déclencheurs**, sélectionnez **Périodicité**.
3.	Dans le déclencheur **Périodicité**, sélectionnez **Modifier**, sélectionnez la liste déroulante **Fréquence** et sélectionnez **Jour**, puis sélectionnez **Intervalle** et tapez **7**.
4.	Sélectionnez **+ Nouvelle étape**, puis **Ajouter une action**.
5.	Dans la liste **actions**, tapez `db2` dans la zone de modification **Search for more actions** (Rechercher d’autres actions), puis sélectionnez **DB2 - Get tables (Preview)** (DB2 - Obtenir les tables (version préliminaire)).

	![](./media/connectors-create-api-db2/Db2connectorActions.png)

6.	Dans le volet de configuration **DB2 - Get tables** (DB2 - Obtenir les tables), cochez la **case** pour activer l’option **Connect via on-premises data gateway** (Connecter par le biais de la passerelle de données locale). Vous remarquerez que les paramètres de cloud sont remplacés par les paramètres locaux.
	- Renseignez la zone **Serveur** sous la forme d’une adresse ou d’un alias suivis d’un caractère deux-points et d’un numéro de port. Par exemple, tapez `ibmserver01:50000`.
	- Renseignez la zone **Base de données**. Par exemple, tapez `nwind`.
	- Renseignez la zone **Authentification**. Par exemple, sélectionnez **De base**.
	- Renseignez la zone **Nom d’utilisateur**. Par exemple, tapez `db2admin`.
	- Renseignez la zone **Mot de passe**. Par exemple, tapez `Password1`.
	- Renseignez la zone **Passerelle**. Par exemple, sélectionnez **datagateway01**.
7. Sélectionnez **Créer**, puis **Enregistrer**.

	![](./media/connectors-create-api-db2/Db2connectorOnPremisesDataGatewayConnection.png)

8.	Au niveau du panneau **Db2getTables**, dans la liste **Toutes les exécutions** figurant sous **Résumé**, sélectionnez le premier élément répertorié (dernière exécution).
9.	Dans le panneau **Exécution d’application logique**, sélectionnez **Détails de l’exécution**. Dans la liste **Action**, sélectionnez **Get\_tables**. Examinez la zone **État**, qui doit présenter la valeur **Opération réussie**. Sélectionnez le **lien Entrées** pour afficher les entrées. Sélectionnez le **lien Sorties**, et visualisez les sorties, qui doivent inclure une liste de tables.

	![](./media/connectors-create-api-db2/Db2connectorGetTablesLogicAppRunOutputs.png)

## Créer les connexions
Ce connecteur prend en charge les connexions aux bases de données hébergées en local et dans le cloud à l’aide des propriétés de connexion ci-après.

Propriété | Description
--- | ---
server | Obligatoire. Accepte une valeur de chaîne qui représente une adresse ou un alias TCP/IP, au format IPv4 ou IPv6, suivis d’un caractère deux-points et d’un numéro de port TCP/IP. 
database | Obligatoire. Accepte une valeur de chaîne qui représente un nom de base de données relationnelle DRDA (RDBNAM). DB2 pour z/OS accepte une chaîne de 16 octets (la propriété de base de données correspond à un emplacement IBM DB2 pour z/OS). DB2 pour i5/OS accepte une chaîne de 18 octets (la propriété de base de données correspond à une base de données relationnelle IBM DB2 pour i). DB2 pour LUW accepte une chaîne de 8 octets.
authentication | facultatif. Accepte la valeur d’élément de liste De base ou Windows (Kerberos). 
username | Obligatoire. Accepte une valeur de chaîne. DB2 pour z/OS accepte une chaîne de 8 octets. DB2 pour i accepte une chaîne de 10 octets. DB2 pour Linux ou UNIX accepte une chaîne de 8 octets. DB2 pour Windows accepte une chaîne de 30 octets.
password | Obligatoire. Accepte une valeur de chaîne.
gateway | Obligatoire. Accepte une valeur d’élément de liste représentant la passerelle de données locale définie pour Logic Apps dans le groupe de stockage.  

## Créer la connexion de passerelle locale
Ce connecteur peut accéder à une base de données DB2 locale par le biais de la passerelle locale. Pour plus d’informations, voir les rubriques consacrées aux passerelles.

1. Dans le volet de configuration **Passerelles**, sélectionnez la **case** pour activer l’option **Connect via on-premises data gateway** (Connecter par le biais de la passerelle de données locale). Vous remarquerez que les paramètres de cloud sont remplacés par les paramètres locaux.
2. Renseignez la zone **Serveur** sous la forme d’une adresse ou d’un alias suivis d’un caractère deux-points et d’un numéro de port. Par exemple, tapez `ibmserver01:50000`.
3. Renseignez la zone **Base de données**. Par exemple, tapez `nwind`.
4. Renseignez la zone **Authentification**. Par exemple, sélectionnez **De base**.
5. Renseignez la zone **Nom d’utilisateur**. Par exemple, tapez `db2admin`.
6. Renseignez la zone **Mot de passe**. Par exemple, tapez `Password1`.
7. Renseignez la zone **Passerelle**. Par exemple, sélectionnez **datagateway01**.
8. Sélectionnez **Créer** pour continuer.

	![](./media/connectors-create-api-db2/Db2connectorOnPremisesDataGatewayConnection.png)

## Créer la connexion cloud
Ce connecteur peut accéder à une base de données DB2 cloud.

1. Dans le volet de configuration **Passerelles**, laissez la **case** décochée pour désactiver l’option **Connect via on-premises data gateway** (Connecter par le biais de la passerelle de données locale).
2. Renseignez la zone **Nom de la connexion**. Par exemple, tapez `hisdemo2`.
3. Renseignez la zone **DB2 server name** (Nom du serveur DB2) sous la forme d’une adresse ou d’un alias suivis d’un caractère deux-points et d’un numéro de port. Par exemple, tapez `hisdemo2.cloudapp.net:50000`.
3. Renseignez la zone **DB2 database name** (Nom de la base de données DB2). Par exemple, tapez `nwind`.
4. Renseignez la zone **Nom d’utilisateur**. Par exemple, tapez `db2admin`.
5. Renseignez la zone **Mot de passe**. Par exemple, tapez `Password1`.
6. Sélectionnez **Créer** pour continuer.

	![](./media/connectors-create-api-db2/Db2connectorCloudConnection.png)

## Extraire toutes les lignes à l’aide de l’instruction SELECT
Vous pouvez définir une action d’application logique pour qu’elle extraie toutes les lignes d’une table DB2. Cela indique au connecteur de traiter une instruction SELECT DB2, telle que `SELECT * FROM AREA`.

### Créer une application logique
1.	Dans le **Panneau de démarrage Azure**, sélectionnez **+** (signe plus), **Web + mobile**, puis **Application logique**.
2.	Entrez le **Nom**, tel que `Db2getRows`, **Abonnement**, **Groupe de ressources**, **Emplacement** et **Plan App Service**. Sélectionnez **Épingler au tableau de bord**, puis **Créer**.

### Ajouter un déclencheur et une action
1.	Dans **Concepteur d’applications logiques**, sélectionnez **Blank LogicApp** (Application logique vide) dans la liste **Modèles**.
2.	Dans la liste **déclencheurs**, sélectionnez **Périodicité**.
3.	Dans le déclencheur **Périodicité**, sélectionnez **Modifier**, sélectionnez la liste déroulante **Fréquence** et sélectionnez **Jour**, puis sélectionnez **Intervalle** et tapez **7**.
4.	Sélectionnez **+ Nouvelle étape**, puis **Ajouter une action**.
5.	Dans la liste **actions**, tapez `db2` dans la zone de modification **Search for more actions** (Rechercher d’autres actions), puis sélectionnez **DB2 - Get tables (Preview)** (DB2 - Obtenir les lignes (version préliminaire)).
6. Dans l’action **Get rows (Preview)** (Obtenir les lignes (version préliminaire)), sélectionnez **Modifier la connexion**.
7. Dans le volet de configuration **Connexions**, sélectionnez **Créer**.

	![](./media/connectors-create-api-db2/Db2connectorNewConnection.png)
  
8. Dans le volet de configuration **Passerelles**, laissez la **case** décochée pour désactiver l’option **Connect via on-premises data gateway** (Connecter par le biais de la passerelle de données locale).
	- Renseignez la zone **Nom de la connexion**. Par exemple, tapez `HISDEMO2`.
	- Renseignez la zone **DB2 server name** (Nom du serveur DB2) sous la forme d’une adresse ou d’un alias suivis d’un caractère deux-points et d’un numéro de port. Par exemple, tapez `HISDEMO2.cloudapp.net:50000`.
	- Renseignez la zone **DB2 database name** (Nom de la base de données DB2). Par exemple, tapez `NWIND`.
	- Renseignez la zone **Nom d’utilisateur**. Par exemple, tapez `db2admin`.
	- Renseignez la zone **Mot de passe**. Par exemple, tapez `Password1`.
9. Sélectionnez **Créer** pour continuer.

	![](./media/connectors-create-api-db2/Db2connectorCloudConnection.png)

10. Dans la liste **Nom de la table**, sélectionnez la **flèche vers le bas**, puis sélectionnez **AREA**.
11. Si vous le souhaitez, sélectionnez **Afficher les options avancées** pour spécifier les options de requête.
12. Sélectionnez **Enregistrer**.

	![](./media/connectors-create-api-db2/Db2connectorGetRowsTableName.png)

13.	Au niveau du panneau **Db2getRows**, dans la liste **Toutes les exécutions** figurant sous **Résumé**, sélectionnez le premier élément répertorié (dernière exécution).
14.	Dans le panneau **Exécution d’application logique**, sélectionnez **Détails de l’exécution**. Dans la liste **Action**, sélectionnez **Get\_rows**. Examinez la zone **État**, qui doit présenter la valeur **Opération réussie**. Sélectionnez le **lien Entrées** pour afficher les entrées. Sélectionnez le **lien Sorties**, et visualisez les sorties, qui doivent inclure une liste de lignes.

	![](./media/connectors-create-api-db2/Db2connectorGetRowsOutputs.png)

## ajout d’une ligne à l’aide de l’instruction INSERT ;
Vous pouvez définir une action d’application logique pour qu’elle ajoute une ligne à une table DB2. Cette action indique au connecteur de traiter une instruction INSERT DB2, telle que `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`.

### Créer une application logique
1.	Dans le **Panneau de démarrage Azure**, sélectionnez **+** (signe plus), **Web + mobile**, puis **Application logique**.
2.	Entrez le **Nom**, tel que `Db2insertRow`, **Abonnement**, **Groupe de ressources**, **Emplacement** et **Plan App Service**. Sélectionnez **Épingler au tableau de bord**, puis **Créer**.

### Ajouter un déclencheur et une action
1.	Dans **Concepteur d’applications logiques**, sélectionnez **Blank LogicApp** (Application logique vide) dans la liste **Modèles**.
2.	Dans la liste **déclencheurs**, sélectionnez **Périodicité**.
3.	Dans le déclencheur **Périodicité**, sélectionnez **Modifier**, sélectionnez la liste déroulante **Fréquence** et sélectionnez **Jour**, puis sélectionnez **Intervalle** et tapez **7**.
4.	Sélectionnez **+ Nouvelle étape**, puis **Ajouter une action**.
5.	Dans la liste **actions**, tapez **db2** dans la zone de modification **Search for more actions** (Rechercher d’autres actions), puis sélectionnez **DB2 - Insert row (Preview)** (DB2 - Insérer une ligne (version préliminaire)).
6. Dans l’action **Get rows (Preview)** (Obtenir les lignes (version préliminaire)), sélectionnez **Modifier la connexion**.
7. Dans le volet de configuration **Connexions**, sélectionnez une connexion. Par exemple, sélectionnez **hisdemo2**.

	![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)

8. Dans la liste **Nom de la table**, sélectionnez la **flèche vers le bas**, puis sélectionnez **AREA**.
9. Entrez des valeurs pour toutes les colonnes requises (signalées par un astérisque rouge). Par exemple, tapez `99999` pour **AREAID**, tapez `Area 99999`, puis tapez la valeur `102` pour **REGIONID**.
10. Sélectionnez **Enregistrer**.

	![](./media/connectors-create-api-db2/Db2connectorInsertRowValues.png)
 
11.	Au niveau du panneau **Db2insertRow**, dans la liste **Toutes les exécutions** figurant sous **Résumé**, sélectionnez le premier élément répertorié (dernière exécution).
12.	Dans le panneau **Exécution d’application logique**, sélectionnez **Détails de l’exécution**. Dans la liste **Action**, sélectionnez **Get\_rows**. Examinez la zone **État**, qui doit présenter la valeur **Opération réussie**. Sélectionnez le **lien Entrées** pour afficher les entrées. Sélectionnez le **lien Sorties**, et visualisez les sorties, qui doivent inclure la nouvelle ligne.

	![](./media/connectors-create-api-db2/Db2connectorInsertRowOutputs.png)

## Extraire une ligne à l’aide de l’instruction SELECT
Vous pouvez définir une action d’application logique pour qu’elle extraie une ligne d’une table DB2. Cette action indique au connecteur de traiter une instruction SELECT WHERE DB2, telle que `SELECT FROM AREA WHERE AREAID = '99999'`.

### Créer une application logique
1.	Dans le **Panneau de démarrage Azure**, sélectionnez **+** (signe plus), **Web + mobile**, puis **Application logique**.
2.	Renseignez les zones **Nom** (par exemple, « **Db2getRow** »), **Abonnement**, **Groupe de ressources**, **Emplacement** et **Plan App Service**. Sélectionnez **Épingler au tableau de bord**, puis **Créer**.

### Ajouter un déclencheur et une action
1.	Dans **Concepteur d’applications logiques**, sélectionnez **Blank LogicApp** (Application logique vide) dans la liste **Modèles**.
2.	Dans la liste **déclencheurs**, sélectionnez **Périodicité**.
3.	Dans le déclencheur **Périodicité**, sélectionnez **Modifier**, sélectionnez la liste déroulante **Fréquence** et sélectionnez **Jour**, puis sélectionnez **Intervalle** et tapez **7**.
4.	Sélectionnez **+ Nouvelle étape**, puis **Ajouter une action**.
5.	Dans la liste **actions**, tapez **db2** dans la zone de modification **Search for more actions** (Rechercher d’autres actions), puis sélectionnez **db2 - Get tables (Preview)** (DB2 - Obtenir les lignes (version préliminaire)).
6. Dans l’action **Get rows (Preview)** (Obtenir les lignes (version préliminaire)), sélectionnez **Modifier la connexion**.
7. Dans le volet de configuration **Connexions**, sélectionnez une connexion existante. Par exemple, sélectionnez **hisdemo2**.

	![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)

8. Dans la liste **Nom de la table**, sélectionnez la **flèche vers le bas**, puis sélectionnez **AREA**.
9. Entrez des valeurs pour toutes les colonnes requises (signalées par un astérisque rouge). Par exemple, tapez `99999` pour **AREAID**.
10. Si vous le souhaitez, sélectionnez **Afficher les options avancées** pour spécifier les options de requête.
11. Sélectionnez **Enregistrer**.

	![](./media/connectors-create-api-db2/Db2connectorGetRowValues.png)

12.	Au niveau du panneau **Db2getRow**, dans la liste **Toutes les exécutions** figurant sous **Résumé**, sélectionnez le premier élément répertorié (dernière exécution).
13.	Dans le panneau **Exécution d’application logique**, sélectionnez **Détails de l’exécution**. Dans la liste **Action**, sélectionnez **Get\_rows**. Examinez la zone **État**, qui doit présenter la valeur **Opération réussie**. Sélectionnez le **lien Entrées** pour afficher les entrées. Sélectionnez le **lien Sorties**, et visualisez les sorties, qui doivent inclure une ligne.

	![](./media/connectors-create-api-db2/Db2connectorGetRowOutputs.png)

## Modifier une ligne à l’aide de l’instruction UPDATE
Vous pouvez définir une action d’application logique pour qu’elle modifie une ligne d’une table DB2. Cette action indique au connecteur de traiter une instruction UPDATE DB2, telle que `UPDATE AREA SET AREAID = '99999', AREADESC = 'Area 99999', REGIONID = 102)`.

### Créer une application logique
1.	Dans le **Panneau de démarrage Azure**, sélectionnez **+** (signe plus), **Web + mobile**, puis **Application logique**.
2.	Entrez le **Nom**, tel que `Db2updateRow`, **Abonnement**, **Groupe de ressources**, **Emplacement** et **Plan App Service**. Sélectionnez **Épingler au tableau de bord**, puis **Créer**.

### Ajouter un déclencheur et une action
1.	Dans **Concepteur d’applications logiques**, sélectionnez **Blank LogicApp** (Application logique vide) dans la liste **Modèles**.
2.	Dans la liste **déclencheurs**, sélectionnez **Périodicité**.
3.	Dans le déclencheur **Périodicité**, sélectionnez **Modifier**, sélectionnez la liste déroulante **Fréquence** et sélectionnez **Jour**, puis sélectionnez **Intervalle** et tapez **7**.
4.	Sélectionnez **+ Nouvelle étape**, puis **Ajouter une action**.
5.	Dans la liste **actions**, tapez **db2** dans la zone de modification **Search for more actions** (Rechercher d’autres actions), puis sélectionnez **DB2 - Update row (Preview)** (DB2 - Mettre à jour une ligne (version préliminaire)).
6. Dans l’action **Get rows (Preview)** (Obtenir les lignes (version préliminaire)), sélectionnez **Modifier la connexion**.
7. Dans le volet de configuration **Connexions**, sélectionnez une connexion existante. Par exemple, sélectionnez **hisdemo2**.

	![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)

8. Dans la liste **Nom de la table**, sélectionnez la **flèche vers le bas**, puis sélectionnez **AREA**.
9. Entrez des valeurs pour toutes les colonnes requises (signalées par un astérisque rouge). Par exemple, tapez `99999` pour **AREAID**, tapez `Updated 99999`, puis tapez la valeur `102` pour **REGIONID**.
10. Sélectionnez **Enregistrer**.

	![](./media/connectors-create-api-db2/Db2connectorUpdateRowValues.png)

11.	Au niveau du panneau **Db2updateRow**, dans la liste **Toutes les exécutions** figurant sous **Résumé**, sélectionnez le premier élément répertorié (dernière exécution).
12.	Dans le panneau **Exécution d’application logique**, sélectionnez **Détails de l’exécution**. Dans la liste **Action**, sélectionnez **Get\_rows**. Examinez la zone **État**, qui doit présenter la valeur **Opération réussie**. Sélectionnez le **lien Entrées** pour afficher les entrées. Sélectionnez le **lien Sorties**, et visualisez les sorties, qui doivent inclure la nouvelle ligne.

	![](./media/connectors-create-api-db2/Db2connectorUpdateRowOutputs.png)

## suppression d’une ligne à l’aide de l’instruction DELETE.
Vous pouvez définir une action d’application logique pour qu’elle supprime une ligne d’une table DB2. Cette action indique au connecteur de traiter une instruction DELETE DB2, telle que `DELETE FROM AREA WHERE AREAID = '99999'`.

### Créer une application logique
1.	Dans le **Panneau de démarrage Azure**, sélectionnez **+** (signe plus), **Web + mobile**, puis **Application logique**.
2.	Entrez le **Nom**, tel que `Db2deleteRow`, **Abonnement**, **Groupe de ressources**, **Emplacement** et **Plan App Service**. Sélectionnez **Épingler au tableau de bord**, puis **Créer**.

### Ajouter un déclencheur et une action
1.	Dans **Concepteur d’applications logiques**, sélectionnez **Blank LogicApp** (Application logique vide) dans la liste **Modèles**.
2.	Dans la liste **déclencheurs**, sélectionnez **Périodicité**.
3.	Dans le déclencheur **Périodicité**, sélectionnez **Modifier**, sélectionnez la liste déroulante **Fréquence** et sélectionnez **Jour**, puis sélectionnez **Intervalle** et tapez **7**.
4.	Sélectionnez **+ Nouvelle étape**, puis **Ajouter une action**.
5.	Dans la liste **actions**, sélectionnez **db2** dans la zone de modification **Search for more actions** (Rechercher d’autres actions), puis sélectionnez **DB2 - Delete row (Preview)** (DB2 - Supprimer une ligne (version préliminaire)).
6. Dans l’action **Get rows (Preview)** (Obtenir les lignes (version préliminaire)), sélectionnez **Modifier la connexion**.
7. Dans le volet de configuration **Connexions**, sélectionnez une connexion existante. Par exemple, sélectionnez **hisdemo2**.

	![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)

8. Dans la liste **Nom de la table**, sélectionnez la **flèche vers le bas**, puis sélectionnez **AREA**.
9. Entrez des valeurs pour toutes les colonnes requises (signalées par un astérisque rouge). Par exemple, tapez `99999` pour **AREAID**.
10. Sélectionnez **Enregistrer**.

	![](./media/connectors-create-api-db2/Db2connectorDeleteRowValues.png)

11.	Au niveau du panneau **Db2deleteRow**, dans la liste **Toutes les exécutions** figurant sous **Résumé**, sélectionnez le premier élément répertorié (dernière exécution).
12.	Dans le panneau **Exécution d’application logique**, sélectionnez **Détails de l’exécution**. Dans la liste **Action**, sélectionnez **Get\_rows**. Examinez la zone **État**, qui doit présenter la valeur **Opération réussie**. Sélectionnez le **lien Entrées** pour afficher les entrées. Sélectionnez le **lien Sorties**, et visualisez les sorties, qui doivent inclure la ligne supprimée.

	![](./media/connectors-create-api-db2/Db2connectorDeleteRowOutputs.png)

## Détails techniques

## Actions
Une action est une opération effectuée par le flux de travail défini dans une application logique. Le connecteur de base de données DB2 inclut les actions ci-après.

|Action|Description|
|--- | ---|
|[GetRow](connectors-create-api-db2.md#get-row)|Récupère une ligne unique d’une table DB2|
|[GetRows](connectors-create-api-db2.md#get-rows)|Récupère les lignes d’une table DB2|
|[InsertRow](connectors-create-api-db2.md#insert-row)|Insère une nouvelle ligne dans une table DB2|
|[DeleteRow](connectors-create-api-db2.md#delete-row)|Supprime une ligne d’une table DB2|
|[GetTables](connectors-create-api-db2.md#get-tables)|Récupère les tables d’une base de données DB2|
|[UpdateRow](connectors-create-api-db2.md#update-row)|Met à jour une ligne existante d’une table DB2|

### Détails de l’action

Dans cette section, consultez les détails relatifs à chaque action, y compris toutes les propriétés d’entrée requises ou facultatives et toute sortie correspondante associée au connecteur.

#### Obtenir une ligne 
Récupère une ligne unique d’une table DB2.

| Nom de la propriété| Display Name |Description|
| ---|---|---|
|table * | Nom de la table |Nom de la table DB2|
|id * | ID de la ligne |Identificateur unique de la ligne à récupérer|

Un astérisque (*) signifie que la propriété est requise.

##### Détails des résultats
Item

| Nom de la propriété | Type de données |
|---|---|
|ItemInternalId|string|


#### Obtenir des lignes 
Récupère les lignes d’une table DB2.

|Nom de la propriété| Display Name|Description|
| ---|---|---|
|table*|Nom de la table|Nom de la table DB2|
|$skip|Nombre à ignorer|Nombre d’entrées à ignorer (valeur par défaut : 0)|
|$top|Nombre maximal à récupérer|Nombre maximal d’entrées à récupérer (valeur par défaut : 256)|
|$filter|Requête de filtre|Requête filter ODATA pour limiter le nombre d’entrées|
|$orderby|Trier par|Requête orderBy ODATA pour spécifier l’ordre des entrées|

Un astérisque (*) signifie que la propriété est requise.

##### Détails des résultats
ItemsList

| Nom de la propriété | Type de données |
|---|---|
|value|array|


#### Insérer une ligne 
Insère une nouvelle ligne dans une table DB2.

|Nom de la propriété| Display Name|Description|
| ---|---|---|
|table*|Nom de la table|Nom de la table DB2|
|item*|Ligne|Ligne à insérer dans la table DB2 spécifiée|

Un astérisque (*) signifie que la propriété est requise.

##### Détails des résultats
Item

| Nom de la propriété | Type de données |
|---|---|
|ItemInternalId|string|


#### Supprimer la ligne 
Supprime une ligne d’une table DB2.

|Nom de la propriété| Display Name|Description|
| ---|---|---|
|table*|Nom de la table|Nom de la table DB2|
|id*|ID de la ligne|Identificateur unique de la ligne à supprimer|

Un astérisque (*) signifie que la propriété est requise.

##### Détails des résultats
Aucune.

#### Obtenir des tables 
Récupère les tables d’une base de données DB2.

Il n'existe aucun paramètre pour cet appel.

##### Détails des résultats 
TablesList

| Nom de la propriété | Type de données |
|---|---|
|value|array|

#### Mettre à jour une ligne 
Met à jour une ligne existante d’une table DB2.

|Nom de la propriété| Display Name|Description|
| ---|---|---|
|table*|Nom de la table|Nom de la table DB2|
|id*|ID de la ligne|Identificateur unique de la ligne à mettre à jour|
|item*|Ligne|Ligne avec valeurs mises à jour|

Un astérisque (*) signifie que la propriété est requise.

##### Détails des résultats  
Item

| Nom de la propriété | Type de données |
|---|---|
|ItemInternalId|string|


### Réponses HTTP

Lorsque vous exécutez des appels de diverses actions, vous pouvez obtenir certaines réponses. Le tableau suivant présente les réponses et leurs descriptions :

|Nom|Description|
|---|---|
|200|OK|
|202|Acceptée|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Introuvable|
|500|Erreur interne du serveur. Une erreur inconnue s'est produite|
|default|L’opération a échoué.|


## Plateformes et versions DB2 prises en charge
Ce connecteur prend en charge les plateformes et versions IBM DB2 suivantes, ainsi que les produits IBM DB2 compatibles (par exemple, IBM Bluemix dashDB) qui prennent en charge les versions 10 et 11 de SQL Access Manager (SQLAM) DRDA (Distributed Relational Database Architecture) :

- IBM DB2 pour z/OS 11.1
- IBM DB2 pour z/OS 10.1
- IBM DB2 pour i 7.3
- IBM DB2 pour i 7.2
- IBM DB2 pour i 7.1
- IBM DB2 pour LUW 11
- IBM DB2 pour LUW 10.5


## Étapes suivantes

[Créez une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md). Explorez les autres connecteurs disponibles dans les applications logiques en consultant notre [liste d’API](apis-list.md).

<!---HONumber=AcomDC_0928_2016-->