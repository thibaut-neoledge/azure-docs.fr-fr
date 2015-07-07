<properties 
   pageTitle="Utilisation du connecteur SQL dans Microsoft Azure App Service" 
   description="Utilisation du connecteur SQL" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="anuragdalmia" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="06/17/2015"
   ms.author="sutalasi"/>


# Connecteur SQL Microsoft

Connectez-vous à une base de données SQL Server ou Azure SQL en local pour créer et modifier vos informations ou données. Les connecteurs peuvent être utilisés dans les applications logiques pour extraire, traiter ou placer des données dans le cadre d’un ’flux’. En utilisant le connecteur SQL dans votre flux, vous pouvez effectuer une multitude d’opérations. Vous pouvez par exemple afficher :

- Exposer une partie des données résidant dans votre base de données SQL via une interface utilisateur web ou mobile. 
- Insérer des données dans une table de stockage de votre base de données Oracle. Par exemple, vous pouvez entrer des dossiers d'employés, mettre à jour des bons de commande, et ainsi de suite.
- Extraire des données SQL et les exploiter dans un processus métier. Par exemple, vous pouvez obtenir les dossiers de clients et les placer dans SalesForce. 

## Déclencheurs et actions
Les *déclencheurs* sont des événements qui se produisent. Par exemple, lorsqu'une commande est mise à jour ou lorsqu'un nouveau client est ajouté. Une *action* est le résultat du déclencheur. Par exemple, lorsqu'une commande est mise à jour, envoyer une alerte au vendeur. Ou bien, lorsqu’un nouveau client est ajouté, lui envoyer un message de bienvenue.

Le connecteur de base de données SQL peut être utilisé comme un déclencheur ou une action dans une application logique et prend en charge les données aux formats JSON et XML. Pour chaque table incluse dans les paramètres de votre package (voir plus loin dans cette rubrique), il existe un jeu d'actions JSON et un jeu d'actions XML.

Le connecteur de base de données SQL propose les déclencheurs et les actions suivants :

Déclencheurs | Actions
--- | ---
Interrogation des données | <ul><li>Insérer dans la table</li><li>Mettre à jour la table</li><li>Sélectionner dans la table</li><li>Supprimer de la table</li><li>Appeler une procédure stockée</li>

## Créer le connecteur SQL

Un connecteur peut être créé dans une application logique ou directement à partir d'Azure Marketplace. Pour créer un connecteur à partir de Marketplace :

1. Dans le tableau d'accueil Azure, sélectionnez **Marketplace**.
2. Sélectionnez **API Apps** et recherchez « Connecteur SQL ».
3. Entrez le nom, le plan App Service et d'autres propriétés.
4. Entrez les paramètres de package suivants :

	Nom | Requis | Description
--- | --- | ---
Nom du serveur | Oui | Entrez le nom du serveur SQL. Par exemple, entrez *SQLserver/sqlexpress* ou *SQLserver.mydomain.com*.
Port | Non | La valeur par défaut est 1433.
Nom d’utilisateur | Oui | Entrez un nom d'utilisateur pouvant se connecter au serveur SQL. Si vous vous connectez à un serveur SQL en local, entrez le domaine\nom d'utilisateur. 
Mot de passe | Oui | Entrez le mot de passe d’utilisateur.
Nom de la base de données | Oui | Entrez la base de données à laquelle vous vous connectez. Par exemple, vous pouvez entrer *Customers* ou *dbo/orders*.
Local | Oui | La valeur par défaut est false. Entrez False si vous vous connectez à une base de données SQL Azure. Entrez True si vous vous connectez à un serveur SQL en local. 
Chaîne de connexion Service Bus | Non | Si vous vous connectez sur site, entrez la chaîne de connexion Service Bus Relay.<br/><br/>[Utilisation du Gestionnaire des connexions hybrides](app-service-logic-hybrid-connection-manager.md)<br/>[Tarification Service Bus](http://azure.microsoft.com/pricing/details/service-bus/)
Nom du serveur partenaire | Non | Si le serveur principal est indisponible, vous pouvez entrer un serveur partenaire comme serveur secondaire ou de sauvegarde. 
Tables | Non | Répertoriez les tables de base de données qui peuvent être mises à jour par le connecteur. Par exemple, entrez *OrdersTable* ou *EmployeeTable*. Si aucune table n'est entrée, toutes les tables peuvent être utilisées. Des tables et/ou des procédures stockées valident sont nécessaires pour utiliser ce connecteur comme une action. 
Procédures stockées | Non | Entrez une procédure stockée existante qui peut être appelée par le connecteur. Par exemple, entrez *sp_IsEmployeeEligible* ou *sp_CalculateOrderDiscount*. Des tables et/ou des procédures stockées valident sont nécessaires pour utiliser ce connecteur comme une action. 
Requête de données disponibles | Pour la prise en charge du déclencheur | Instruction SQL permettant de déterminer si une table de la base de données SQL contient des données interrogeables. Devrait renvoyer une valeur numérique représentant le nombre de lignes de données disponibles. Exemple : SELECT COUNT(*) from table_name. 
Requête d’interrogation de données | Pour la prise en charge du déclencheur | Instruction SQL permettant d’interroger la table de base de données SQL Server. Vous pouvez entrer plusieurs instructions SQL séparées par un point-virgule. Cette instruction est exécutée de façon transactionnelle et validée uniquement lorsque les données sont stockées en toute sécurité dans votre application logique. Exemple : SELECT * FROM table_name; DELETE FROM table_name. <br/><br/>**Remarque**<br/>Vous devez fournir une instruction d'interrogation qui évite la création d'une boucle infinie en supprimant, déplaçant ou mettant à jour les données afin qu'elles ne soient pas interrogées à nouveau. 

5. Lorsque vous avez terminé, les paramètres du package se présentent comme suit : <br/> ![][1]

## Utiliser le connecteur comme un déclencheur
Prenons l’exemple d’une application logique simple qui interroge les données d’une table SQL, ajoute des données dans une autre table et les met à jour.

Pour utiliser le connecteur SQL comme un déclencheur, entrez les valeurs **Requête de données disponibles** et **Requête d’interrogation de données**. La valeur **Requête de données disponibles** est exécutée selon votre planification, et détermine si des données sont disponibles. Comme cette requête retourne uniquement un nombre scalaire, elle peut être réglée et optimisée pour une exécution fréquente.

La valeur **Requête d'interrogation de données** est exécutée uniquement lorsque la requête de données disponibles indique que des données sont disponibles. Cette instruction s'exécute au sein d'une transaction et n'est validée que lorsque les données extraites sont stockées durablement dans votre flux. Il est important d'éviter d'extraire à l'infini les mêmes données. La nature transactionnelle de cette exécution peut servir à supprimer ou à mettre à jour les données pour s'assurer qu'elles ne seront pas collectées à la prochaine interrogation de données.

> [AZURE.NOTE]Le schéma renvoyé par cette instruction identifie les propriétés disponibles dans votre connecteur. Toutes les colonnes doivent être nommées.

#### Exemple de requête de données disponibles

	SELECT COUNT(*) FROM [Order] WHERE OrderStatus = 'ProcessedForCollection'

#### Exemple de requête d'interrogation de données

	SELECT *, GetData() as 'PollTime' FROM [Order] 
		WHERE OrderStatus = 'ProcessedForCollection' 
		ORDER BY Id DESC; 
	UPDATE [Order] SET OrderStatus = 'ProcessedForFrontDesk' 
		WHERE Id = 
		(SELECT Id FROM [Order] WHERE OrderStatus = 'ProcessedForCollection' ORDER BY Id DESC)

### Ajouter le déclencheur
1. Lors de la création ou de la modification d’une application logique, choisissez le connecteur SQL créé comme déclencheur. Ceci répertorie les déclencheurs disponibles : **Interroger les données (JSON)** et **Interroger les données (XML)** : <br/> ![][5] 

2. Sélectionnez le déclencheur **Interroger les données (JSON)**, entrez la fréquence et cliquez sur ✓ : <br/> ![][6]

3. Le déclencheur s’affiche maintenant comme configuré dans l’application logique. La ou les sorties du déclencheur s’affichent et peuvent être utilisées comme entrées dans les actions suivantes : <br/> ![][7]

## Utiliser le connecteur comme une action
Prenons notre scénario dans lequel une application logique simple interroge les données d’une table SQL, ajoute des données dans une autre table et les met à jour.

Pour utiliser le connecteur SQL comme une action, entrez le nom des tables et/ou des procédures stockées que vous avez saisies lorsque vous avez créé le connecteur SQL :

1. Après votre déclencheur (ou si vous choisissez d'exécuter cette logique manuellement), ajoutez le connecteur SQL que vous avez créé à partir de la galerie. Sélectionnez l’une des actions d’insertion : *Insérer dans TempEmployeeDetails (JSON)* : <br/> ![][8] 

2. Indiquez les entrées de l’enregistrement à insérer et cliquez sur ✓: <br/> ![][9]

3. Dans la galerie, sélectionnez le même connecteur SQL que vous avez créé. En tant qu'action, sélectionnez l'action Update dans la même table, par exemple *Update EmployeeDetails* : <br/> ![][11]

4. Indiquez les entrées de l’action de mise à jour et cliquez sur ✓ : <br/> ![][12]

Vous pouvez tester l’application logique en ajoutant un nouvel enregistrement dans la table interrogée.

## Configuration hybride (facultatif)

> [AZURE.NOTE]Cette étape n’est requise que si vous utilisez SQL Server en local, derrière votre pare-feu.

App Service utilise le Gestionnaire des configurations hybrides pour se connecter en toute sécurité à votre système local. Si votre connecteur utilise un serveur SQL en local, le Gestionnaire des connexions hybrides est requis.

Consultez la rubrique [Utilisation du Gestionnaire des connexions hybrides](app-service-logic-hybrid-connection-manager.md).


## En faire plus avec votre connecteur
Maintenant que le connecteur est créé, vous pouvez l'ajouter à un flux d'entreprise à l'aide d'une application logique. Voir [Que sont les applications logiques ?](app-service-logic-what-are-logic-apps.md).

Vous pouvez également consulter les statistiques de performances et contrôler la sécurité du connecteur. Voir [Gérer et surveiller les applications API et le connecteur](../app-service-api/app-service-api-manage-in-portal.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-sql/Create.png
[5]: ./media/app-service-logic-connector-sql/LogicApp1.png
[6]: ./media/app-service-logic-connector-sql/LogicApp2.png
[7]: ./media/app-service-logic-connector-sql/LogicApp3.png
[8]: ./media/app-service-logic-connector-sql/LogicApp4.png
[9]: ./media/app-service-logic-connector-sql/LogicApp5.png
[11]: ./media/app-service-logic-connector-sql/LogicApp7.png
[12]: ./media/app-service-logic-connector-sql/LogicApp8.png


 

<!---HONumber=62-->