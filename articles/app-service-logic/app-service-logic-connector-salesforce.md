<properties
   pageTitle="Connecteur Salesforce"
   description="Utilisation du connecteur Salesforce"
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
   ms.date="07/02/2015"
   ms.author="sameerch"/>


#Utilisation du connecteur Salesforce dans votre application logique#

Les applications logiques peuvent se déclencher selon diverses sources de données et proposent des connecteurs pour obtenir et traiter les données dans le cadre du flux. Le connecteur Salesforce vous permet de créer et de modifier différentes entités, comme les comptes, les prospects, etc. Voici les scénarios d’intégration classiques qui impliquent Salesforce.

- Synchronisation des comptes entre Salesforce et les systèmes ERP tels que SAP et QuickBooks

- Flux des commandes aux encaissements de Salesforce aux systèmes ERP


Dans le cadre des paramètres de package du connecteur, l'utilisateur peut spécifier les entités que le connecteur peut gérer et les actions, les paramètres d'entrée et de sortie sont renseignés de manière dynamique. Voici les différentes actions disponibles dans le connecteur Salesforce.

- Créer une entité : utilisez cette action pour créer une entité Salesforce comme un compte, un cas ou un objet personnalisé.

- Mettre à jour une entité : utilisez cette action pour mettre à jour une entité Salesforce existante.

- Upsert d'une entité : utilisez cette action pour mettre à jour une entité Salesforce existante ou en créer une si elle n'existe pas.

- Supprimer une entité : utilisez cette action pour supprimer une entité Salesforce existante.

- Exécuter une requête : utilisez cette action pour exécuter une requête SELECT écrite en langage SOQL (Salesforce Object Query Language).


##Créer une application API du connecteur Salesforce##
1.	Ouvrez Azure Marketplace à l’aide de l’option « + NOUVEAU » en bas à droite du portail Azure.
2.	Accédez à « Web et mobilité > Applications API » et recherchez « SalesForce ».
3.	Configurez le connecteur SalesForce en indiquant les détails du plan d’hébergement, le groupe de ressources et en sélectionnant le nom de l’application API.

	![][15]
4. Configurez les entités SalesForce que vous souhaitez lire/écrire dans Paramètres du package.

Vous pouvez maintenant créer une application API du connecteur SalesForce.


##Créer une application logique##
Créons une application logique simple qui crée un compte dans Salesforce et met à jour les détails de l'adresse de facturation de ce même compte.

1.	Connectez-vous au portail Azure et cliquez sur Nouveau -> Web + mobile -> Application logique.

	![][1]

2.	Dans la page Créer une application logique, fournissez les informations requises, telles que le nom, le plan de service de l'application et l'emplacement.

	![][2]

3.	Cliquez sur Déclencheurs et actions. L'écran de l'éditeur d'application logique s'affiche.

	![][3]

4.	Sélectionnez Exécuter cette logique manuellement, ce qui signifie que cette application logique ne peut être appelée que manuellement.

    ![][4]

5.	Développez « Applications API dans ce groupe de ressources » dans la galerie pour afficher toutes les applications API disponibles. Sélectionnez Salesforce dans la galerie. Le connecteur Salesforce est ajouté au flux.


	![][5]

8.	Pour autoriser votre application logique à accéder à votre compte SalesForce, cliquez sur « Autoriser » pour fournir les informations d’identification Salesforce.

	![][6]

9.	Vous êtes redirigé vers la page de connexion Salesforce, puis vous pouvez vous authentifier avec vos informations d'identification Salesforce.

	![][7]

	![][8]

10.	Une fois que l'autorisation est terminée, toutes les actions s'affichent.

	![][9]

11.	Sélectionnez l'action Créer un compte et les paramètres d'entrée s'affichent.

	![][10]

12.	Indiquez le nom du compte et cliquez sur ✓.

	![][11]

13.	Sélectionnez Connecteur Salesforce dans la section des éléments récemment utilisés dans la galerie et une nouvelle action Salesforce est ajoutée.

14.	Sélectionnez Mettre à jour un compte dans la liste des actions et les paramètres d'entrée de cette action s'affichent.

	![][12]

15.	Cliquez sur + en regard de ID d'enregistrement pour sélectionner la valeur ID de la sortie de l'action Créer un compte.

	![][13]

16.	Indiquez des valeurs pour l'adresse de facturation (rue, ville, état et code postal) et cliquez sur ✓.

	![][14]

17. Cliquez sur OK dans l’éditeur d’application logique, puis cliquez sur Créer. Environ 30 secondes sont nécessaires pour terminer la création.

18. Accédez à l’application logique créée et cliquez sur Exécuter pour l’exécuter.

19. Vous pouvez constater qu’un nouveau compte portant le nom Contoso a été créé dans votre compte Salesforce.

<!--Image references-->
[1]: ./media/app-service-logic-connector-salesforce/1_New_Logic_App.png
[2]: ./media/app-service-logic-connector-salesforce/2_Logic_App_Settings.png
[3]: ./media/app-service-logic-connector-salesforce/3_Logic_App_Editor.png
[4]: ./media/app-service-logic-connector-salesforce/4_Manual_Logic_App.png
[5]: ./media/app-service-logic-connector-salesforce/5_Select_Salesforce_Gallery.png
[6]: ./media/app-service-logic-connector-salesforce/6_Salesforce_Authorize.png
[7]: ./media/app-service-logic-connector-salesforce/7_Salesforce_Login.png
[8]: ./media/app-service-logic-connector-salesforce/8_Salesforce_User_Consent.png
[9]: ./media/app-service-logic-connector-salesforce/9_Salesforce_Actions.png
[10]: ./media/app-service-logic-connector-salesforce/10_Salesforce_Create_Account.png
[11]: ./media/app-service-logic-connector-salesforce/11_Create_Account_OK.png
[12]: ./media/app-service-logic-connector-salesforce/12_Salesforce_Update_Account.png
[13]: ./media/app-service-logic-connector-salesforce/13_Record_ID_from_Create.png
[14]: ./media/app-service-logic-connector-salesforce/14_Update_Account_Address.png
[15]: ./media/app-service-logic-connector-salesforce/15_Create_new_salesforce_connector.png

<!---HONumber=July15_HO4-->