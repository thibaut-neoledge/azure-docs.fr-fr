<properties 
   pageTitle="Utilisation du connecteur Oracle dans Microsoft Azure App Service" 
   description="Utilisation du connecteur Oracle" 
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
   ms.date="06/22/2015"
   ms.author="sutalasi"/>


# Connecteur de base de données Oracle

Connectez-vous à un serveur de base de données Oracle en local pour créer et modifier vos informations ou données. Les connecteurs peuvent être utilisés dans les applications logiques pour extraire, traiter ou placer des données dans le cadre d'un « flux ». En utilisant le connecteur Oracle dans votre flux, vous pouvez effectuer une multitude d'opérations. Vous pouvez, par exemple :

- Exposer une partie des données résidant dans votre base de données Oracle via une application web ou mobile.
- Insérer des données dans votre table de stockage de votre base de données Oracle. Par exemple, vous pouvez entrer des dossiers d'employés, mettre à jour des bons de commande, et ainsi de suite.
- Extraire des données d'Oracle et les exploiter dans un processus métier. Par exemple, vous pouvez obtenir les dossiers de clients et les placer dans SalesForce. 


## Déclencheurs et actions
Les *déclencheurs* sont des événements qui se produisent. Par exemple, lorsqu'une commande est mise à jour ou lorsqu'un client est ajouté. Une *action* est le résultat du déclencheur. Par exemple, lorsqu'une commande est mise à jour, envoyer une alerte au vendeur. Ou bien, lorsqu'un nouveau client est ajouté, lui envoyer un message de bienvenue.

Le connecteur de base de données Oracle peut être utilisé comme un déclencheur ou une action dans une application logique et prend en charge les données aux formats JSON et XML. Pour chaque table incluse dans les paramètres de votre package (voir plus loin dans cette rubrique), il existe un jeu d'actions JSON et un jeu d'actions XML. Si vous choisissez un déclencheur ou une action XML, vous pouvez utiliser l'[application API Transformation](app-service-logic-transform-xml-documents.md) pour convertir les données dans un autre format XML.

Le connecteur de base de données Oracle propose les déclencheurs et les actions suivants :

Déclencheurs | Actions
--- | ---
Interrogation des données | <ul><li>Insérer dans la table</li><li>Mettre à jour la table</li><li>Sélectionner dans la table</li><li>Supprimer de la table</li><li>Appeler une procédure stockée</li>


## Créer un connecteur de base de données Oracle

Un connecteur peut être créé dans une application logique ou directement à partir d'Azure Marketplace. Pour créer un connecteur à partir de Marketplace :

1. Dans le tableau d'accueil Azure, sélectionnez **Marketplace**.
2. Sélectionnez **API Apps** et recherchez « Connecteur de base de données Oracle ».
3. Entrez le nom, le plan App Service et d'autres propriétés.
4. Entrez les paramètres de package suivants :

	Nom | Requis | Description
--- | --- | ---
Source de données | Oui | Nom d'une source de données (service net) spécifié dans le fichier tnsnames.ora de l'ordinateur où le client Oracle est installé. Pour plus d'informations sur les noms de source de données et le fichier tnsnames.ora, consultez la rubrique [Configuration du client Oracle](http://msdn.microsoft.com/library/dd787872.aspx).
Nom d’utilisateur | Oui | Entrez un nom d'utilisateur pour vous connecter au serveur Oracle.
Mot de passe | Oui | Entrez le mot de passe utilisateur.
Chaîne de connexion Service Bus | Oui | Si vous vous connectez sur site, entrez la chaîne de connexion Service Bus Relay.<br/><br/>[Utilisation du Gestionnaire de connexion hybride](app-service-logic-hybrid-connection-manager.md)<br/>[Tarification Service Bus](http://azure.microsoft.com/pricing/details/service-bus/)
Tables | Non | Entrez les tables de la base de données que le connecteur est autorisé à modifier. Par exemple, entrez *OrdersTable,EmployeeTable*.
Procédures stockées | Non | Entrez les procédures stockées dans la base de données que le connecteur peut appeler. Par exemple, entrez *IsEmployeeEligible,CalculateOrderDiscount*.
Fonctions | Non | Entrez les fonctions dans la base de données que le connecteur peut appeler. Par exemple, entrez *IsEmployeeEligible,CalculateOrderDiscount*.
Entités de package | Non | Entrez les packages dans la base de la base de données que le connecteur peut appeler. Par exemple, entrez *PackageOrderProcessing.CompleteOrder,PackageOrderProcessing.GenerateBill*.
Instruction de données disponibles | Non | Entrez l'instruction permettant de déterminer si des données sont disponibles pour l'interrogation. Par exemple, entrez *SELECT * from table_name*.
Type d’interrogation | Non | Entrez le type d'interrogation. Les valeurs autorisées sont « Sélection », « Procédure », « Fonction » et « Package ».
Instruction d’interrogation | Non | Entrez l'instruction permettant d'interroger la base de données du serveur Oracle. Par exemple, entrez *SELECT * from table_name*.
Instruction de post-interrogation | Non | Entrez l'instruction à exécuter après l'interrogation. Par exemple, entrez *DELETE * from table_name*.

5. Lorsque vous avez terminé, les paramètres du package se présentent comme suit : <br/> ![][1]


## Utiliser le connecteur comme déclencheur
Prenons une application logique simple qui interroge les données d'une table Oracle, ajoute des données dans une autre table et met les données à jour.

### Ajouter le déclencheur
1. Lors de la création ou de la modification d'une application logique, choisissez le connecteur Oracle créé comme déclencheur. Ceci répertorie les déclencheurs disponibles : **Interroger les données (JSON)** et **Interroger les données (XML)** : <br/> ![][5] 

2. Sélectionnez le déclencheur **Interroger les données (JSON)**, entrez la fréquence et cliquez sur ✓ : <br/> ![][6]

3. Le déclencheur s'affiche maintenant comme configuré dans l'application logique. La ou les sorties du déclencheur s'affichent et peuvent être utilisées comme entrées dans les actions suivantes : <br/> ![][7]

## Utiliser le connecteur comme une action
Prenons notre application logique simple qui interroge les données d'une table Oracle, ajoute des données dans une autre table et les met à jour.

Pour utiliser le connecteur Oracle comme une action, entrez le nom des tables et/ou des procédures stockées que vous avez saisies lorsque vous avez créé le connecteur Oracle :

1. Sélectionnez le même connecteur Oracle dans la galerie comme action. Sélectionnez l'une des actions d'insertion : *Insérer dans TempEmployeeDetails (JSON)* : <br/> ![][8] 

2. Indiquez les entrées de l'enregistrement à insérer et cliquez sur ✓: <br/> ![][9]

3. Dans la galerie, sélectionnez le même connecteur Oracle que vous avez créé. En tant qu'action, sélectionnez l'action Update dans la même table, par exemple *Update TempEmployeeDetails* : <br/> ![][11]

4. Indiquez les entrées de l'action de mise à jour et cliquez sur ✓ : <br/> ![][12]

Vous pouvez tester l'application logique en ajoutant un nouvel enregistrement dans la table interrogée.

## Configuration hybride

> [AZURE.NOTE]Cette étape n'est requise que si vous utilisez Oracle en local, derrière votre pare-feu.

App Service utilise le Gestionnaire de configuration hybride pour se connecter en toute sécurité à votre système local. Si votre connecteur utilise Oracle en local, le Gestionnaire de connexion hybride est requis.

Consultez la rubrique [Utilisation du Gestionnaire de connexion hybride](app-service-logic-hybrid-connection-manager.md).

## En faire plus avec votre connecteur
Maintenant que le connecteur est créé, vous pouvez l'ajouter à un flux d'entreprise à l'aide d'une application logique. Voir [Que sont les applications logiques ?](app-service-logic-what-are-logic-apps.md).

Vous pouvez également consulter les statistiques de performances et contrôler la sécurité du connecteur. Voir [Gérer et surveiller les applications API et le connecteur](app-service-api-manage-in-portal.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-oracle/Create.png
[5]: ./media/app-service-logic-connector-oracle/LogicApp1.png
[6]: ./media/app-service-logic-connector-oracle/LogicApp2.png
[7]: ./media/app-service-logic-connector-oracle/LogicApp3.png
[8]: ./media/app-service-logic-connector-oracle/LogicApp4.png
[9]: ./media/app-service-logic-connector-oracle/LogicApp5.png
[10]: ./media/app-service-logic-connector-oracle/LogicApp6.png
[11]: ./media/app-service-logic-connector-oracle/LogicApp7.png
[12]: ./media/app-service-logic-connector-oracle/LogicApp8.png



 

<!---HONumber=62-->