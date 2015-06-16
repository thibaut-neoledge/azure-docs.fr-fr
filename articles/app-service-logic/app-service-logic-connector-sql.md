<properties 
   pageTitle="Connecteur SQL" 
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
   ms.date="03/20/2015"
   ms.author="sutalasi"/>


# Connecteur SQL Microsoft #

Les connecteurs peuvent être utilisés dans les applications logiques pour extraire, traiter ou placer des données dans le cadre d'un flux. Parfois, vous pouvez être amené à utiliser une base de données SQL sur Azure SQL ou SQL Server (installé localement et derrière le pare-feu). En utilisant le connecteur SQL dans votre flux, vous pouvez effectuer une multitude d'opérations. Quelques exemples :  

1.	Exposer une partie des données résidant dans votre base de données SQL via une interface utilisateur web ou mobile.
2.	Insérer des données dans une table de votre base de données SQL (par exemple : dossiers des employés, Bons de commande, etc.).
3.	Extraire des données de SQL pour les utiliser dans un processus métier.

Pour ces scénarios, vous devez effectuer ce qui suit : 

1. Créez une instance de l'application API du connecteur SQL.
2. Établissez une connectivité hybride pour que l'application API puisse communiquer avec le serveur SQL local. Cette étape facultative n'est requise que pour le serveur SQL local et pas pour SQL Azure.
3. Utilisez l'application API créée dans une application logique pour accomplir le processus d'entreprise souhaité.

	###Déclencheurs et actions de base
		
    - Interroger les données (déclencheur) 
    - Insérer dans la table
    - Mettre à jour la table
    - Sélectionner dans la table
    - Supprimer de la table
    - Appeler une procédure stockée

## Créez une instance de l'application API du connecteur SQL. ##

Pour utiliser le connecteur SQL, vous devez créer une instance de l'application API de ce connecteur. Pour cela, procédez comme suit :

1. Ouvrez Azure Marketplace à l'aide de l'option " + NOUVEAU " en bas à gauche du portail Azure.
2. Accédez à " Web et mobilité > API Apps " et recherchez " Connecteur SQL ".
3. Fournissez les détails génériques comme le nom, le plan de service de l'application, etc. dans le premier panneau.
4. Indiquez les paramètres du package mentionnés dans le tableau ci-dessous.	

<style type="text/css">
	table.tableizer-table {
	border: 1px solid #CCC; font-family: Arial, Helvetica, sans-serif;
	font-size: 12px;
} 
.tableizer-table td {
	padding: 4px;
	margin: 3px;
	border: 1px solid #ccc;
}
.tableizer-table th {
	background-color: #525B64; 
	color: #FFF;
	font-weight: bold;
}
</style><table class="tableizer-table">
<tr class="tableizer-firstrow"><th>Nom</th><th>Requis</th><th>Valeur par défaut</th><th>Description</th></tr>
 <tr><td>Nom du serveur</td><td>Oui</td><td>&nbsp;</td><td>Spécifiez le nom du serveur SQL. Exemple : " SQLserver ", " SQLserver/sqlexpress " ou " SQLserver.mydomain.com ".</td></tr>
 <tr><td>Port</td><td>Non</td><td> 1433</td><td>facultatif. Numéro de port où la connexion est établie. Si vous ne spécifiez aucune valeur, le connecteur utilise le port par défaut.</td></tr>
 <tr><td>Nom d'utilisateur</td><td>Oui</td><td>&nbsp;</td><td>Spécifiez un nom d'utilisateur valide pour vous connecter au serveur SQL.</td></tr>
 <tr><td>Mot de passe</td><td>Oui</td><td>&nbsp;</td><td>Spécifiez un mot de passe valide pour vous connecter au serveur SQL.</td></tr>
 <tr><td>Nom de la base de données</td><td>Oui</td><td>&nbsp;</td><td>Spécifiez un nom de base de données valide dans le serveur SQL. Exemple : " orders ", " dbo/orders " ou " myaccount/employees ".</td></tr>
 <tr><td>Local</td><td>Oui</td><td>FALSE</td><td>Spécifiez si votre serveur SQL est installé en local derrière un pare-feu ou non. Si la valeur est TRUE, vous devez installer un agent écouteur sur un serveur qui peut accéder à votre serveur SQL. Vous pouvez afficher la page de résumé de votre application API et cliquer sur  'Connexion hybride' pour installer l'agent.</td></tr>
 <tr><td>Chaîne de connexion Service Bus</td><td>Non</td><td>&nbsp;</td><td>facultatif. Spécifiez ce paramètre si votre serveur SQL est local. Il doit s'agir d'une chaîne de connexion d'espace de noms Service Bus valide. Vérifiez que vous utilisez l'édition  'Standard' d'Azure Service Bus et non l'édition  'Basic'.</td></tr>
 <tr><td>Nom du serveur partenaire</td><td>Non</td><td>&nbsp;</td><td>facultatif. Spécifiez le serveur partenaire auquel se connecter lorsque le serveur principal est indisponible.</td></tr>
 <tr><td>Tables</td><td>Non</td><td>&nbsp;</td><td>facultatif. Spécifiez les tables de la base de données que le connecteur est autorisé à modifier. Ex. : OrdersTable, EmployeeTable</td></tr>
 <tr><td>Procédures stockées</td><td>Non</td><td>&nbsp;</td><td>facultatif. Spécifiez les procédures stockées dans la base de données, que le connecteur peut appeler. Ex : IsEmployeeEligible, CalculateOrderDiscount</td></tr>
 <tr><td>Requête de données disponibles</td><td>Non</td><td>&nbsp;</td><td>facultatif. Spécifiez l'instruction SQL permettant de déterminer si une table de la base de données SQL contient des données interrogeables. Exemple : SELECT COUNT(*) from table_name.</td></tr>
 <tr><td>Requête d'interrogation de données</td><td>Non</td><td>&nbsp;</td><td>facultatif. Spécifiez l'instruction SQL d'interrogation de la table de base de données SQL Server. Vous pouvez spécifier plusieurs instructions SQL séparées par un point-virgule. Exemple : SELECT * from table_name; DELETE from table_name. REMARQUE : vous pouvez déclarer l'instruction d'interrogation de telle sorte qu'elle ne s'exécute pas en boucle à l'infini. Par exemple, l'instruction " select " doit être suivie de " delete " et l'instruction " select " basée sur un indicateur doit être suivie par une instruction de mise à jour de cet indicateur.</td></tr>
</table>


 ![][1]  

## Configuration hybride (facultatif) ##

Remarque : cette étape n'est requise que si vous utilisez SQL Server en local, derrière le pare-feu.

Accédez à l'application API tout juste créée via Parcourir -> API Apps -> <Nom de l'application API que vous venez de créer>. Le comportement suivant est visible. L'installation n'est pas terminée puisque la connexion hybride n'est pas encore établie.

![][2] 

Pour l'établir, procédez comme suit :

1. Copiez la chaîne de connexion principale.
2. Cliquez sur le lien  'Télécharger et configurer'.
3. Suivez le processus d'installation qui se lance et fournissez la chaîne de connexion principale quand vous y êtes invité.
4. Une fois le processus d'installation terminé, une boîte de dialogue similaire à celle-ci apparaît.

![][3] 

Maintenant, quand vous accédez de nouveau à l'application API créée, vous pouvez observer que l'état de la connexion hybride est Connecté. 

![][4] 

Remarque : si vous voulez basculer vers la chaîne de connexion secondaire, il vous suffit de recommencer la configuration et de fournir la chaîne de connexion secondaire à la place de la chaîne de connexion principale.  

## Utilisation dans une application logique ##

Le connecteur SQL peut être utilisé comme déclencheur ou action dans une application logique. Le déclencheur et toutes les actions prennent en charge les formats de données JSON et XML. Pour chaque table déclarée dans les paramètres du package, un ensemble d'actions JSON et un ensemble d'actions XML sont fournis. Si vous choisissez le déclencheur ou une action XML, vous pouvez utiliser Transformer l'application API pour convertir les données dans un autre format XML. 

Prenons l'exemple d'une application logique simple qui interroge les données d'une table SQL, ajoute des données dans une autre table et les met à jour.



-  Lors de la création ou de la modification d'une application logique, choisissez l'application API du connecteur SQL créée comme déclencheur. Ceci va répertorier les déclencheurs disponibles : Interroger les données (JSON) et Interroger les données (XML).

 ![][5] 


- Sélectionnez le déclencheur Interroger les données (JSON), spécifiez la fréquence et cliquez sur ✓.

![][6] 



- Le déclencheur s'affiche maintenant comme configuré dans l'application logique. La ou les sorties du déclencheur s'affichent et peuvent être utilisées comme entrées dans les actions suivantes. 

![][7] 


- Sélectionnez le même connecteur SQL dans la galerie comme action. Sélectionnez l'une des actions d'insertion : Insérer dans TempEmployeeDetails (JSON).

![][8] 



- Indiquez les entrées de l'enregistrement à insérer et cliquez sur ✓. 

![][9] 



- Sélectionnez le même connecteur SQL dans la galerie comme action. Sélectionnez l'action de mise à jour sur la même table (Ex. : Mettre à jour EmployeeDetails)

![][11] 



- Indiquez les entrées de l'action de mise à jour et cliquez sur ✓. 

![][12] 

Vous pouvez tester l'application logique en ajoutant un nouvel enregistrement dans la table interrogée.

<!--Image references-->
[1]: ./media/app-service-logic-connector-sql/Create.jpg
[2]: ./media/app-service-logic-connector-sql/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-connector-sql/HybridSetup.jpg
[4]: ./media/app-service-logic-connector-sql/BrowseSetupComplete.jpg
[5]: ./media/app-service-logic-connector-sql/LogicApp1.jpg
[6]: ./media/app-service-logic-connector-sql/LogicApp2.jpg
[7]: ./media/app-service-logic-connector-sql/LogicApp3.jpg
[8]: ./media/app-service-logic-connector-sql/LogicApp4.jpg
[9]: ./media/app-service-logic-connector-sql/LogicApp5.jpg
[10]: ./media/app-service-logic-connector-sql/LogicApp6.jpg
[11]: ./media/app-service-logic-connector-sql/LogicApp7.jpg
[12]: ./media/app-service-logic-connector-sql/LogicApp8.jpg




<!--HONumber=52--> 