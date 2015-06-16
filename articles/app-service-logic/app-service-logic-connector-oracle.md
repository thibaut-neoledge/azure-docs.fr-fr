<properties 
   pageTitle="Connecteur Oracle" 
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
   ms.date="03/20/2015"
   ms.author="sutalasi"/>


# Connecteur de base de données Oracle #

Les connecteurs peuvent être utilisés dans les applications logiques pour extraire, traiter ou placer des données dans le cadre d'un flux. En utilisant le connecteur Oracle dans votre flux, vous pouvez effectuer une multitude d'opérations. Quelques exemples :  

1.	Exposer une partie des données résidant dans votre base de données Oracle via une interface utilisateur web ou mobile.
2.	Insérer des données dans une table de stockage de votre base de données Oracle (par exemple : Dossiers des employés, Bons de commande, etc.)
3.	Extraire des données d'Oracle et les exploiter dans un processus métier

Pour ces scénarios, vous devez effectuer ce qui suit : 

1. Créez une instance de l'application API du connecteur Oracle.
2. Établissez une connectivité hybride permettant à l'application API de communiquer avec le serveur Oracle local.
3. Utilisez l'application API créée dans une application logique pour accomplir le processus d'entreprise souhaité.

	###Déclencheurs et actions de base
		
    - Interroger les données (déclencheur) 
    - Insérer dans la table
    - Mettre à jour la table
    - Sélectionner dans la table
    - Supprimer de la table
    - Appeler une procédure stockée

## Créer une instance de l'application API du connecteur de base de données Oracle ##

Pour utiliser le connecteur Oracle, vous devez créer une instance de l'application API de ce connecteur. Pour cela, procédez comme suit :

1. Ouvrez Azure Marketplace à l'aide de l'option '+ NOUVEAU' située en bas à gauche du portail Azure.
2. Accédez à " Web et mobilité > API Apps " et recherchez " Connecteur Oracle ".
3. Fournissez les détails génériques comme le nom, le plan de service de l'application, etc. dans le premier panneau.
4. Indiquez les paramètres de package mentionnés dans le tableau ci-dessous.

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
<tr class="tableizer-firstrow"><th>Nom</th><th>Requis</th><th>Description</th></tr>
 <tr><td>Source de données</td><td>Oui</td><td>Nom d'une source de données (service net) spécifié dans le fichier tnsnames.ora de l'ordinateur où le client Oracle est installé. Pour plus d'informations sur les noms de source de données et le fichier tnsnames.ora, consultez la rubrique [Configuration du client Oracle].</td></tr>
 <tr><td>Nom d'utilisateur</td><td>Oui</td><td>Indiquez un nom d'utilisateur valide pour vous connecter au serveur Oracle.</td></tr>
 <tr><td>Mot de passe</td><td>Oui</td><td>Indiquez un mot de passe valide pour vous connecter au serveur Oracle.</td></tr>
 <tr><td>Chaîne de connexion Service Bus</td><td>Oui</td><td>facultatif. Spécifiez ce paramètre si votre serveur Oracle est local. Il doit s'agir d'une chaîne de connexion d'espace de noms Service Bus valide. Vous devez installer un agent écouteur sur un serveur ayant accès à votre serveur Oracle. Pour installer cet agent, accédez à la page de résumé de votre application API et cliquez sur  Connexion hybride'.</td></tr>
 <tr><td>Tables</td><td>Non</td><td>facultatif. Spécifiez les tables de la base de données, que le connecteur est autorisé à modifier. Ex. : OrdersTable,EmployeeTable</td></tr>
 <tr><td>Procédures stockées</td><td>Non</td><td>facultatif. Spécifiez les procédures stockées dans la base de données, que le connecteur peut appeler. Ex : IsEmployeeEligible,CalculateOrderDiscount</td></tr>
 <tr><td>Fonctions</td><td>Non</td><td>facultatif. Spécifiez les fonctions dans la base de données, que le connecteur peut appeler. Ex : IsEmployeeEligible,CalculateOrderDiscount</td></tr>
 <tr><td>Entités de package</td><td>Non</td><td>facultatif. Spécifiez les packages dans la base de la base de données, que le connecteur peut appeler. Ex : PackageOrderProcessing.CompleteOrder,PackageOrderProcessing.GenerateBill</td></tr>
 <tr><td>Instruction de données disponibles</td><td>Non</td><td>facultatif. Spécifiez l'instruction permettant de déterminer si des données sont disponibles pour l'interrogation. Exemple : SELECT * from nom_table</td></tr>
 <tr><td>Type d'interrogation</td><td>Non</td><td>facultatif. Spécifiez le type d'interrogation. Les valeurs autorisées sont " Sélection ", " Procédure ", " Fonction " et " Package ".</td></tr>
 <tr><td>Instruction d'interrogation</td><td>Non</td><td>facultatif. Spécifiez l'instruction permettant d'interroger la base de données du serveur Oracle. Exemple : SELECT * from table_name</td></tr>
 <tr><td>Instruction de post-interrogation</td><td>Non</td><td>facultatif. Spécifiez l'instruction à exécuter après l'interrogation. Exemple : DELETE * from table_name.</td></tr>
</table>



 ![][1]  

## Configuration hybride ##

Accédez à l'application API tout juste créée via Parcourir -> API Apps -> <Nom de l'application API que vous venez de créer>. Le comportement suivant est visible. L'installation n'est pas terminée puisque la connexion hybride n'est pas encore établie.

![][2] 

Pour l'établir, procédez comme suit :

1. Copiez la chaîne de connexion principale.
2. Cliquez sur le lien  Télécharger et configurer'.
3. Suivez le processus d'installation qui se lance et fournissez la chaîne de connexion principale quand vous y êtes invité.
4. Une fois le processus d'installation terminé, une boîte de dialogue similaire à celle-ci apparaît.

![][3] 

Maintenant, quand vous accédez de nouveau à l'application API créée, vous pouvez observer que l'état de la connexion hybride est Connecté. 

![][4] 

Remarque : si vous voulez basculer vers la chaîne de connexion secondaire, il vous suffit de recommencer la configuration et de fournir la chaîne de connexion secondaire à la place de la chaîne de connexion principale.  

## Utilisation dans une application logique ##

Le connecteur peut être utilisé comme déclencheur ou action dans une application logique. Le déclencheur et toutes les actions prennent en charge les formats de données JSON et XML. Pour chaque table déclarée dans les paramètres du package, un ensemble d'actions JSON et un ensemble d'actions XML sont fournis. Si vous choisissez le déclencheur ou une action XML, vous pouvez utiliser Transformer l'application API pour convertir les données dans un autre format XML. 

Prenons une application logique simple qui interroge les données d'une table Oracle, ajoute des données dans une autre table et les met à jour.



-  Lors de la création ou de la modification d'une application logique, choisissez l'application API du connecteur Oracle créée comme déclencheur. Cette opération va répertorier les déclencheurs disponibles : Interroger les données (JSON) et Interroger les données (XML).

 ![][5] 


- Sélectionnez le déclencheur Interroger les données (JSON), spécifiez la fréquence et cliquez sur ✓.

![][6] 



- Le déclencheur s'affiche maintenant comme configuré dans l'application logique. La ou les sorties du déclencheur s'affichent et peuvent être utilisées comme entrées dans les actions suivantes. 

![][7] 


- Sélectionnez le même connecteur Oracle dans la galerie comme action. Sélectionnez l'une des actions d'insertion : Insérer dans TempEmployeeDetails (JSON).

![][8] 



- Indiquez les entrées de l'enregistrement à insérer et cliquez sur ✓. 

![][9] 



- Sélectionnez le même connecteur Oracle dans la galerie comme action. Sélectionnez l'action de mise à jour sur la même table (Ex. : Mettre à jour EmployeeDetails)

![][11] 



- Indiquez les entrées de l'action de mise à jour et cliquez sur ✓. 

![][12] 

Vous pouvez tester l'application logique en ajoutant un nouvel enregistrement dans la table interrogée.

<!--Image references-->
[1]: ./media/app-service-logic-connector-oracle/Create.jpg
[2]: ./media/app-service-logic-connector-oracle/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-connector-oracle/HybridSetup.jpg
[4]: ./media/app-service-logic-connector-oracle/BrowseSetupComplete.jpg
[5]: ./media/app-service-logic-connector-oracle/LogicApp1.jpg
[6]: ./media/app-service-logic-connector-oracle/LogicApp2.jpg
[7]: ./media/app-service-logic-connector-oracle/LogicApp3.jpg
[8]: ./media/app-service-logic-connector-oracle/LogicApp4.jpg
[9]: ./media/app-service-logic-connector-oracle/LogicApp5.jpg
[10]: ./media/app-service-logic-connector-oracle/LogicApp6.jpg
[11]: ./media/app-service-logic-connector-oracle/LogicApp7.jpg
[12]: ./media/app-service-logic-connector-oracle/LogicApp8.jpg

<!--Links-->
[Configuration du client Oracle]: https://msdn.microsoft.com/fr-fr/library/dd787872.aspx



<!--HONumber=52--> 