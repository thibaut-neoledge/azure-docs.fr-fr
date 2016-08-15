<properties
   pageTitle="Utilisation du connecteur DB2 dans Microsoft Azure App Service | Microsoft Azure"
   description="Comment utiliser le connecteur DB2 avec les déclencheurs et actions d’application logique"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="gplarsen"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/31/2016"
   ms.author="plarsen"/>

# Connecteur DB2
>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma 2014-12-01-preview des applications logiques.

Microsoft Connector for DB2 est une application API qui permet de connecter des applications via Azure App Service aux ressources stockées dans une base de données IBM DB2. L’application Connector utilise un client Microsoft pour se connecter aux ordinateurs du serveur DB2 distant via une connexion réseau TCP/IP, y compris des connexions hybrides Azure sur les serveurs DB2 locaux à l’aide d’Azure Service Bus Relay. Connector prend en charge les opérations de base de données suivantes :

- Lecture des lignes à l’aide de l’instruction SELECT
- Interrogation pour la lecture de lignes à l’aide de l’instruction SELECT COUNT suivie de l’instruction SELECT
- Ajout d’une ou plusieurs lignes (en bloc) à l’aide de l’instruction INSERT
- Modification d’une ou plusieurs lignes (en bloc) à l’aide de l’instruction UPDATE
- Suppression d’une ou plusieurs lignes (en bloc) à l’aide de l’instruction DELETE
- Lecture pour la modification de lignes à l’aide de l’instruction SELECT CURSOR, suivie de l’instruction UPDATE WHERE CURRENT OF CURSOR
- Lecture pour la suppression de lignes à l’aide de l’instruction SELECT CURSOR, suivie de l’instruction DELETE WHERE CURRENT OF CURSOR
- Exécution de la procédure avec des paramètres d’entrée et de sortie, une valeur de retour et un jeu de résultats à l’aide de l’instruction CALL
- Personnalisation des commandes et des opérations composites à l’aide des instructions SELECT, INSERT, UPDATE, DELETE

## Déclencheurs et actions
Connector prend en charge les déclencheurs et actions d’application logique suivants :

Déclencheurs | Actions
--- | ---
<ul><li>Interrogation des données</li></ul> | <ul><li>Bulk Insert</li><li>Insert</li><li>Bulk Update</li><li>Update</li><li>Call</li><li>Bulk Delete</li><li>Delete</li><li>Select</li><li>Conditional update</li><li>Post to EntitySet</li><li>Conditional delete</li><li>Select single entity</li><li>Delete</li><li>Upsert to EntitySet</li><li>Commandes personnalisées</li><li>Opérations composites</li></ul>


## Créer le connecteur DB2
Vous pouvez définir un connecteur dans une application logique ou à partir d’Azure Marketplace, comme dans l’exemple suivant :

1. Dans le tableau d'accueil Azure, sélectionnez **Marketplace**.
2. Dans le panneau **Tout**, entrez **db2** dans la zone **Tout rechercher**, puis appuyez sur la touche Entrée.
3. Dans les résultats de la recherche, sélectionnez **Connecteur DB2**.
4. Dans le panneau de description du connecteur DB2, sélectionnez **Créer**.
5. Dans le panneau de package du connecteur DB2, entrez le nom (par exemple, « Db2ConnectorNewOrders »), le plan App Service ainsi que les autres propriétés.
6. Sélectionnez **Paramètres de package**, puis entrez les paramètres de package suivants :

	Nom | Requis | Description
--- | --- | ---
ConnectionString | Oui | Chaîne de connexion du client DB2 (par exemple, « Network Address=servername;Network Port=50000;User ID=username;Password=password;Initial Catalog=SAMPLE;Package Collection=NWIND;Default Schema=NWIND »).
Tables | Oui | Liste de noms de table, de vue et d’alias séparés par des virgules nécessaires pour les opérations OData et pour la génération de la documentation swagger avec des exemples (par exemple, « *NEWORDERS* »).
Procédures | Oui | Liste de noms de procédure et de fonction séparés par des virgules (par exemple, « SPORDERID »).
OnPremise | Non | Déployer en local à l’aide d’Azure Service Bus Relay.
ServiceBusConnectionString | Non | Chaîne de connexion d’Azure Service Bus Relay.
PollToCheckData | Non | Instruction SELECT COUNT à utiliser avec un déclencheur d’application logique (par exemple, « SELECT COUNT(*) FROM NEWORDERS WHERE SHIPDATE IS NULL »).
PollToReadData | Non | Instruction SELECT à utiliser avec un déclencheur d’application logique (par exemple, « SELECT * FROM NEWORDERS WHERE SHIPDATE IS NULL FOR UPDATE »).
PollToAlterData | Non | Instruction UPDATE ou SELECT à utiliser avec un déclencheur d’application logique (par exemple, « UPDATE NEWORDERS SET SHIPDATE = CURRENT DATE WHERE CURRENT OF &lt;CURSOR&gt; »).

7. Sélectionnez **OK**, puis cliquez sur **Créer**.
8. Lorsque vous avez terminé, les paramètres du package se présentent comme suit : ![][1]


## Action d’ajout de données d’une application logique avec le connecteur DB2 ##
Vous pouvez définir une action d’application logique pour ajouter des données à une table DB2 à l’aide d’une opération OData Insert ou Post to Entity. Par exemple, vous pouvez insérer un nouvel enregistrement de commande client en exécutant une instruction SQL INSERT sur une table définie avec une colonne d’identité, qui renvoie la valeur d’identité ou les lignes affectées à l’application logique (SELECT ORDID FROM FINAL TABLE (INSERT INTO NWIND.NEWORDERS (CUSTID,SHIPNAME,SHIPADDR,SHIPCITY,SHIPREG,SHIPZIP) VALUES (?,?,?,?,?,?))).

> [AZURE.TIP] L’instruction « *Post to EntitySet* » de DB2 Connection renvoie la valeur de la colonne d’identité et l’instruction « *API Insert* » renvoie les lignes affectées.

1. Dans le tableau d’accueil Azure, sélectionnez **+** (signe plus), **Web + Mobile**, puis **Application logique**.
2. Entrez le nom (par exemple, « NewOrdersDb2 »), le plan App Service ainsi que d’autres propriétés, puis sélectionnez **Créer**.
3. Dans le tableau d’accueil Azure, sélectionnez l’application logique que vous venez de créer, puis cliquez sur **Paramètres** et **Déclencheurs et actions**.
4. Dans le panneau Déclencheurs et actions, sélectionnez **Créer intégralement** dans les modèles d’application logique.
5. Dans le panneau Applications d’API, sélectionnez **Périodicité**, définissez une fréquence et un intervalle, puis cliquez sur la **coche**.
6. Dans le panneau Applications d’API, sélectionnez **Connecteur DB2** et développez la liste des opérations pour sélectionner **Insert into NEWORDER**.
7. Développez la liste de paramètres pour entrer les valeurs suivantes :

	Nom | Valeur
--- | --- 
CUSTID | 10042
SHIPNAME | Lazy K Kountry Store 
SHIPADDR | 12 Orchestra Terrace
SHIPCITY | Walla Walla 
SHIPREG | WA
SHIPZIP | 99362 

8. Sélectionnez la **coche** pour enregistrer les paramètres d’action, puis cliquez sur **Enregistrer**.
9. Les paramètres doivent se présenter comme suit : ![][3]

10. Dans la liste **Toutes les exécutions** sous **Opérations**, sélectionnez le premier élément répertorié (la dernière exécution).
11. Dans le panneau **Exécution d’application logique**, sélectionnez l’élément **ACTION** **db2connectorneworders**.
12. Dans le panneau **Action d’application logique**, sélectionnez l’élément **INPUTS LINK**. Le connecteur DB2 utilise ces entrées pour traiter une instruction INSERT paramétrable.
13. Dans le panneau **Action d’application logique**, sélectionnez l’élément **OUTPUTS LINK**. Les entrées doivent se présenter comme suit : ![][4]

#### Bon à savoir

- Le connecteur tronque les noms de table DB2 lors de la constitution des noms d’action d’application logique. Par exemple, l’opération **Insert into NEWORDERS** est tronquée en **Insert into NEWORDER**.
- Après avoir enregistré les **déclencheurs et actions** d’application logique, l’application logique traite l’opération. Il peut y avoir un délai d’attente de quelques secondes (par exemple, 3 à 5 secondes) avant que l’application logique ne traite l’opération. Si vous le souhaitez, vous pouvez cliquer sur **Exécuter maintenant** pour traiter l’opération.
- Le connecteur DB2 définit les membres EntitySet avec des attributs, notamment pour indiquer si le membre correspond à une colonne DB2 avec une valeur par défaut ou bien à des colonnes générées (par exemple, colonne d’identité). L’application logique affiche un astérisque rouge en regard du nom de code du membre EntitySet, pour indiquer les colonnes DB2 qui requièrent des valeurs. Vous ne devez pas saisir de valeur pour le membre ORDID, qui correspond à la colonne d’identité DB2. Vous pouvez entrer des valeurs pour d’autres membres facultatifs (ITEMS, ORDDATE, REQDATE, SHIPID, FREIGHT, SHIPCTRY) qui correspondent aux colonnes DB2 avec des valeurs par défaut.
- Le connecteur DB2 renvoie à l’application logique la réponse sur l’élément Post to EntitySet qui inclut les valeurs des colonnes d’identité, laquelle est dérivée de l’élément SQLDARD DRDA (données de la réponse de la zone de données SQL) sur l’instruction SQL INSERT préparée. Le serveur DB2 ne renvoie pas les valeurs insérées pour les colonnes avec des valeurs par défaut.


## Action d’ajout de données en bloc d’une application logique avec le connecteur DB2 ##
Vous pouvez définir une action d’application logique pour ajouter des données à une table DB2 à l’aide d’une opération Bulk Insert d’API. Par exemple, vous pouvez insérer deux nouveaux enregistrements de commande client en exécutant une instruction SQL INSERT à l’aide d’un tableau de valeurs de ligne sur une table définie avec une colonne d’identité, qui renvoie les lignes affectées à l’application logique (SELECT ORDID FROM FINAL TABLE (INSERT INTO NWIND.NEWORDERS (CUSTID,SHIPNAME,SHIPADDR,SHIPCITY,SHIPREG,SHIPZIP) VALUES (?,?,?,?,?,?))).

1. Dans le tableau d’accueil Azure, sélectionnez **+** (signe plus), **Web + Mobile**, puis **Application logique**.
2. Entrez le nom (par exemple, « NewOrdersBulkDb2 »), le plan App Service ainsi que d’autres propriétés, puis sélectionnez **Créer**.
3. Dans le tableau d’accueil Azure, sélectionnez l’application logique que vous venez de créer, puis cliquez sur **Paramètres** et **Déclencheurs et actions**.
4. Dans le panneau Déclencheurs et actions, sélectionnez **Créer intégralement** dans les modèles d’application logique.
5. Dans le panneau Applications d’API, sélectionnez **Périodicité**, définissez une fréquence et un intervalle, puis cliquez sur la **coche**.
6. Dans le panneau Applications d’API, sélectionnez **Connecteur DB2** et développez la liste des opérations pour sélectionner **Bulk Insert into NEW**.
7. Entrez la valeur de **lignes** sous forme de tableau. Par exemple, copiez et collez le code suivant :

	```
    [{"CUSTID":10081,"SHIPNAME":"Trail's Head Gourmet Provisioners","SHIPADDR":"722 DaVinci Blvd.","SHIPCITY":"Kirkland","SHIPREG":"WA","SHIPZIP":"98034"},{"CUSTID":10088,"SHIPNAME":"White Clover Markets","SHIPADDR":"305 14th Ave. S. Suite 3B","SHIPCITY":"Seattle","SHIPREG":"WA","SHIPZIP":"98128","SHIPCTRY":"USA"}]
	```

8. Sélectionnez la **coche** pour enregistrer les paramètres d’action, puis cliquez sur **Enregistrer**. Les paramètres doivent se présenter comme suit : ![][6]

9. Dans la liste **Toutes les exécutions** sous **Opérations**, cliquez sur le premier élément répertorié (la dernière exécution).
10. Dans le panneau **Exécution d’application logique**, cliquez sur l’élément **ACTION**.
11. Dans le panneau **Action d’application logique**, cliquez sur l’élément **INPUTS LINK**. Les sorties doivent se présenter comme suit : [][7]
12. Dans le panneau **Action d’application logique**, cliquez sur l’élément **OUTPUTS LINK**. Les sorties doivent se présenter comme suit : ![][8]

#### Bon à savoir

- Le connecteur tronque les noms de table DB2 lors de la constitution des noms d’action d’application logique. Par exemple, l’opération **Bulk Insert into NEWORDERS** est tronquée en **Bulk Insert into NEW**.
- En omettant les colonnes d’identité (par exemple, ORDID), les colonnes de type nullable (par exemple, SHIPDATE) et les colonnes avec des valeurs par défaut (par exemple, ORDDATE, REQDATE, SHIPID, FREIGHT, SHIPCTRY), la base de données DB2 génère des valeurs.
- En spécifiant « today » et « tomorrow », le connecteur DB2 génère les fonctions « CURRENT DATE » et « CURRENT DATE + 1 DAY » (par exemple, REQDATE).


## Déclencheur d’application logique avec connecteur DB2 pour la lecture, la modification ou la suppression de données ##
Vous pouvez définir un déclencheur d’application logique pour interroger et lire des données à partir d’une table DB2 en utilisant une opération composite Poll Data d’API. Par exemple, vous pouvez lire un ou plusieurs nouveaux enregistrements de commande client pour renvoyer les enregistrements à l’application logique. Les paramètres de package/application de DB2 Connection doivent se présenter comme suit :

	App Setting | Value
--- | --- | ---
PollToCheckData | SELECT COUNT(*) FROM NEWORDERS WHERE SHIPDATE IS NULL
PollToReadData | SELECT * FROM NEWORDERS WHERE SHIPDATE IS NULL FOR UPDATE
PollToAlterData | <aucune valeur spécifiée>


Vous pouvez également définir un déclencheur d’application logique pour interroger, lire et modifier des données dans une table DB2 en utilisant une opération composite Poll Data d’API. Par exemple, vous pouvez lire un ou plusieurs nouveaux enregistrements de commande client et mettre à jour les valeurs de ligne pour renvoyer les enregistrements sélectionnés (avant la mise à jour) à l’application logique. Les paramètres de package/application de DB2 Connection doivent se présenter comme suit :

	App Setting | Value
--- | --- | ---
PollToCheckData | SELECT COUNT(*) FROM NEWORDERS WHERE SHIPDATE IS NULL
PollToReadData | SELECT * FROM NEWORDERS WHERE SHIPDATE IS NULL FOR UPDATE
PollToAlterData | UPDATE NEWORDERS SET SHIPDATE = CURRENT DATE WHERE CURRENT OF &lt;CURSOR&gt;


Vous pouvez aussi définir un déclencheur d’application logique pour interroger, lire et supprimer des données d’une table DB2 en utilisant une opération composite Poll Data d’API. Par exemple, vous pouvez lire un ou plusieurs nouveaux enregistrements de commande client et supprimer les lignes pour renvoyer les enregistrements sélectionnés (avant la suppression) à l’application logique. Les paramètres de package/application de DB2 Connection doivent se présenter comme suit :

	App Setting | Value
--- | --- | ---
PollToCheckData | SELECT COUNT(*) FROM NEWORDERS WHERE SHIPDATE IS NULL
PollToReadData | SELECT * FROM NEWORDERS WHERE SHIPDATE IS NULL FOR UPDATE
PollToAlterData | DELETE NEWORDERS WHERE CURRENT OF &lt;CURSOR&gt;

Dans cet exemple, l’application logique va interroger, lire, mettre à jour, puis relire les données de la table DB2.

1. Dans le tableau d’accueil Azure, sélectionnez **+** (signe plus), **Web + Mobile**, puis **Application logique**.
2. Entrez le nom (par exemple, « ShipOrdersDb2 »), le plan App Service ainsi que d’autres propriétés, puis sélectionnez **Créer**.
3. Dans le tableau d’accueil Azure, sélectionnez l’application logique que vous venez de créer, puis cliquez sur **Paramètres** et **Déclencheurs et actions**.
4. Dans le panneau Déclencheurs et actions, sélectionnez **Créer intégralement** dans les modèles d’application logique.
5. Dans le panneau Applications d’API, sélectionnez **Connecteur DB2**, définissez une fréquence et un intervalle, puis cliquez sur la **coche**.
6. Dans le panneau Applications d’API, sélectionnez **Connecteur DB2** et développez la liste des opérations pour sélectionner **Select from NEWORDERS**.
7. Sélectionnez la **coche** pour enregistrer les paramètres d’action, puis cliquez sur **Enregistrer**. Les paramètres doivent se présenter comme suit : ![][10]
8. Cliquez pour fermer le panneau **Déclencheurs et actions**, puis cliquez pour fermer le panneau **Paramètres**.
9. Dans la liste **Toutes les exécutions** sous **Opérations**, cliquez sur le premier élément répertorié (la dernière exécution).
10. Dans le panneau **Exécution d’application logique**, cliquez sur l’élément **ACTION**.
11. Dans le panneau **Action d’application logique**, cliquez sur l’élément **OUTPUTS LINK**. Les sorties doivent se présenter comme suit : ![][11]


## Action de suppression de données d’une application logique avec le connecteur DB2 ##
Vous pouvez définir une action d’application logique pour supprimer des données d’une table DB2 à l’aide d’une opération OData Delete ou Post to Entity. Par exemple, vous pouvez insérer un nouvel enregistrement de commande client en exécutant une instruction SQL INSERT sur une table définie avec une colonne d’identité, qui renvoie la valeur d’identité ou les lignes affectées à l’application logique (SELECT ORDID FROM FINAL TABLE (INSERT INTO NWIND.NEWORDERS (CUSTID,SHIPNAME,SHIPADDR,SHIPCITY,SHIPREG,SHIPZIP) VALUES (?,?,?,?,?,?))).

## Création d’une application logique utilisant le connecteur DB2 pour supprimer des données ##
Vous pouvez créer une application logique dans Azure Marketplace et utiliser ensuite le connecteur DB2 en tant qu’action pour supprimer des commandes client. Par exemple, vous pouvez utiliser l’opération Conditional Delete du connecteur DB2 pour traiter une instruction SQL DELETE (DELETE FROM NEWORDERS WHERE ORDID >= 10000).

1. Dans le menu hub du panneau **Démarrer** d’Azure, cliquez sur **+** (signe plus) et cliquez sur **Web + Mobile**, puis sur **Application logique**.
2. Dans le panneau **Créer une application logique**, entrez un **nom**, par exemple **RemoveOrdersDb2**.
3. Sélectionnez ou définissez des valeurs pour les autres paramètres (par exemple, le plan de service et le groupe de ressources).
4. Les paramètres doivent se présenter comme suit. Cliquez sur **Créer** : ![][12]
5. Dans le panneau **Paramètres**, cliquez sur **Déclencheurs et actions**.
6. Dans la liste **Modèles d’application logique** du panneau **Déclencheurs et actions**, sélectionnez **Créer intégralement**.
7. Dans le panneau **Déclencheurs et actions**, sous **Applications d’API**, cliquez sur **Périodicité** au niveau du groupe de ressources.
8. Sur la surface de conception de l’application logique, cliquez sur l’élément **Périodicité**, définissez une **Fréquence** et un **Intervalle**, par exemple **Jours** et **1**, puis cliquez sur la **coche** pour enregistrer les paramètres de l’élément de périodicité.
9. Dans le panneau **Déclencheurs et actions**, sous **Applications d’API**, cliquez sur **Connecteur DB2** au niveau du groupe de ressources.
10. Sur la surface de conception de l’application logique, cliquez sur l’élément d’action **Connecteur DB2**, sur les points de suspension (**...**) pour développer la liste des opérations, puis sur **Conditional delete from N**.
11. Sur l’élément d’action du connecteur DB2, tapez **ORDID ge 10000** pour une **expression qui identifie un sous-ensemble d’entrées**.
12. Sélectionnez la **coche** pour enregistrer les paramètres d’action, puis cliquez sur **Enregistrer**. Les paramètres doivent se présenter comme suit : ![][13]
13. Cliquez pour fermer le panneau **Déclencheurs et actions**, puis cliquez pour fermer le panneau **Paramètres**.
14. Dans la liste **Toutes les exécutions** sous **Opérations**, cliquez sur le premier élément répertorié (la dernière exécution).
15. Dans le panneau **Exécution d’application logique**, cliquez sur l’élément **ACTION**.
16. Dans le panneau **Action d’application logique**, cliquez sur l’élément **OUTPUTS LINK**. Les sorties doivent se présenter comme suit : ![][14]

**Remarque :** le concepteur d’application logique tronque les noms de table. Par exemple, l’opération **Conditional Delete from NEWORDERS** est tronqué en **Conditional delete from N**.


> [AZURE.TIP] Utilisez les instructions SQL suivantes pour créer les exemples de tables et de procédures stockées.

Vous pouvez créer l’exemple de table NEWORDERS à l’aide des instructions DDL SQL DB2 suivantes :
 
 	CREATE TABLE ORDERS (  
 		ORDID INT NOT NULL GENERATED BY DEFAULT AS IDENTITY (START WITH 10000, INCREMENT BY 1) ,  
 		CUSTID INT NOT NULL ,  
 		EMPID INT NOT NULL DEFAULT 10000 ,  
 		ORDDATE DATE NOT NULL DEFAULT CURRENT DATE ,  
 		REQDATE DATE DEFAULT CURRENT DATE ,  
 		SHIPDATE DATE ,  
 		SHIPID INT NOT NULL DEFAULT 10000,  
 		FREIGHT DECIMAL (9,2) NOT NULL DEFAULT 0.00 ,  
 		SHIPNAME CHAR (40) NOT NULL ,  
 		SHIPADDR CHAR (60) NOT NULL ,  
 		SHIPCITY CHAR (20) NOT NULL ,  
 		SHIPREG CHAR (15) NOT NULL ,  
 		SHIPZIP CHAR (10) NOT NULL ,  
 		SHIPCTRY CHAR (15) NOT NULL DEFAULT 'USA' ,  
 		PRIMARY KEY(ORDID)  
 		)  
 
 	CREATE UNIQUE INDEX XORDID ON ORDERS (ORDID ASC)  



Vous pouvez créer l’exemple de procédure stockée SPORDERID à l’aide de l’instruction DDL DB2 suivante :
 
 	CREATE OR REPLACE PROCEDURE NWIND.SPORDERID (IN ORDERID VARCHAR(128))  
 		DYNAMIC RESULT SETS 1  
 	P1: BEGIN  
 		DECLARE CURSOR1 CURSOR WITH RETURN FOR  
 			SELECT * FROM NWIND.NEWORDERS  
 				WHERE ORDID = ORDERID;  
 		OPEN CURSOR1;  
 	END P1  
 	') 


## Configuration hybride (facultatif)

> [AZURE.NOTE] Cette étape n’est requise que si vous utilisez le connecteur DB2 en local derrière votre pare-feu.

App Service utilise le Gestionnaire de configuration hybride pour se connecter en toute sécurité à votre système local. Si le connecteur utilise un serveur SQL IBM DB2 local pour Windows, le Gestionnaire de connexion hybride est requis.

Consultez la rubrique [Utilisation du Gestionnaire de connexion hybride](app-service-logic-hybrid-connection-manager.md).


## En faire plus avec votre connecteur
Maintenant que le connecteur est créé, vous pouvez l’ajouter à un flux d’entreprise à l’aide d’une application logique. Voir [Qu’est-ce qu’une application logique ?](app-service-logic-what-are-logic-apps.md).

Créez les applications API à l’aide des API REST. Pour plus d'informations, consultez [Référence de connecteurs et d'applications API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Vous pouvez également consulter les statistiques de performances et contrôler la sécurité du connecteur. Consultez la page [Gestion et contrôle de vos connecteurs et applications API intégrés](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-db2/ApiApp_Db2Connector_Create.png
[2]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersDb2_Create.png
[3]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersDb2_TriggersActions.png
[4]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersDb2_Outputs.png
[5]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_Create.png
[6]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_TriggersActions.png
[7]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_Inputs.png
[8]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_Outputs.png
[9]: ./media/app-service-logic-connector-db2/LogicApp_UpdateOrdersDb2_Create.png
[10]: ./media/app-service-logic-connector-db2/LogicApp_UpdateOrdersDb2_TriggersActions.png
[11]: ./media/app-service-logic-connector-db2/LogicApp_UpdateOrdersDb2_Outputs.png
[12]: ./media/app-service-logic-connector-db2/LogicApp_RemoveOrdersDb2_Create.png
[13]: ./media/app-service-logic-connector-db2/LogicApp_RemoveOrdersDb2_TriggersActions.png
[14]: ./media/app-service-logic-connector-db2/LogicApp_RemoveOrdersDb2_Outputs.png

<!---HONumber=AcomDC_0803_2016-->