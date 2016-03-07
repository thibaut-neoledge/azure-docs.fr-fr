<properties
   pageTitle="Utilisation du connecteur SugarCRM dans des applications logiques | Microsoft Azure App Service"
   description="Comment créer et configurer le connecteur SugarCRM ou une application API et l'utiliser dans une application logique d’Azure App Service"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="02/10/2016"
   ms.author="sameerch"/>


# Prise en main du connecteur SugarCRM et ajout de celui-ci à votre application logique
>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma 2014-12-01-preview des applications logiques.

Le connecteur SugarCRM vous permet de créer et de modifier différentes entités, comme les comptes, les prospects, les contacts, etc. Voici les scénarios d’intégration classiques qui impliquent SugarCRM :

- Synchronisation de comptes entre SugarCRM et des systèmes ERP comme SAP
- Synchronisation de comptes, contacts et prospects entre Marketo et SugarCRM
- Flux de la commande au règlement entre SugarCRM et des systèmes ERP

Dans le cadre des paramètres de package du connecteur, l'utilisateur peut spécifier les entités que le connecteur peut gérer et les actions, les paramètres d'entrée et de sortie sont renseignés de manière dynamique.

Les applications logiques peuvent se déclencher selon diverses sources de données et proposent des connecteurs pour obtenir et traiter les données dans le cadre du flux. Vous pouvez ajouter le connecteur SugarCRM à votre flux de travail professionnel et traiter les données dans le cadre de ce flux de travail dans une application logique.



## Actions du connecteur SugarCRM
Voici les différentes actions disponibles dans le connecteur SugarCRM :

- Créer le module : utilisez cette action pour créer un enregistrement du module SugarCRM tel que Comptes, Prospects ou Contacts.

- Mettre à jour le module : utilisez cette action pour mettre à jour un enregistrement du module SugarCRM.

- Supprimer le module : utilisez cette action pour supprimer un enregistrement du module SugarCRM.

- Afficher la liste du module : utilisez cette action pour afficher la liste des enregistrements filtrés. Si aucune requête n’est spécifiée, tous les enregistrements sont affichés.

- Obtenir un enregistrement du module : utilisez cette action pour extraire un enregistrement du module spécifié.

- Obtenir le nombre d’enregistrements : utilisez cette action pour obtenir le nombre d’enregistrements qui répondent à la requête dans le module. Si aucune requête n’est spécifiée, le nombre total d’enregistrements dans le module est affiché.

- Rechercher les doublons dans le module : utilisez cette action pour rechercher les enregistrements en double dans un module.

*Remarque*: pour en savoir plus sur les arguments pris en charge dans une requête, consultez la documentation des [API REST de SugarCRM](https://msdn.microsoft.com/library/dn705870).

## Créer une application API du connecteur SugarCRM
1.	Accédez à portal.azure.com. Ouvrez Azure Marketplace à l’aide de l’option + NOUVEAU dans le coin supérieur gauche du portail Azure.
2.	Accédez à « Marketplace > Tout », puis recherchez « SugarCRM ».
3.	Configurez le connecteur SugarCRM en fournissant les détails du plan de service d’application, en indiquant le groupe de ressources et en entrant le nom de l’application API.
4. Configurez les paramètres de package du connecteur SugarCRM. Voici les paramètres de package que vous devez spécifier pour créer le connecteur :

	Nom | Requis | Description
--- | --- | ---
URL du site | Oui | Entrez l’URL de votre instance SugarCRM. Par exemple, entrez https://abcde1234.sugarcrm.com.
ID de client | Oui | Entrez la clé OAUTH 2.0 du client dans SugarCRM. 
Clé secrète client | Oui | Entrez la question secrète OAUTH du client.
Nom d’utilisateur | Oui | Entrez le nom d’utilisateur de l’utilisateur SugarCRM.
Mot de passe | Oui | Entrez le mot de passe de l’utilisateur SugarCRM.
Nom des modules | Oui | Entrez les modules SugarCRM (tels que Comptes, Contacts, Produits) sur lesquels vous souhaitez effectuer une opération<br><br>Exemple : Comptes, Prospects, Contacts  
  
![][9]



## Créer une application logique
Créons une application logique simple qui crée un compte dans SugarCRM et met à jour les adresses de facturation du même compte.

1.	Connectez-vous au portail Azure et cliquez sur Nouveau -> Web + mobile -> Application logique : ![][1]

2.	Dans la page Créer une application logique, fournissez les informations requises, telles que le nom, le plan de service de l'application et l'emplacement : ![][2]

3.	Cliquez sur Déclencheurs et actions. L'écran de l'éditeur d'application logique s'affiche. Sélectionnez Exécuter cette logique manuellement, ce qui signifie que cette application logique ne peut être appelée que manuellement.

4.	Développez « Applications API dans ce groupe de ressources » dans la galerie pour afficher toutes les applications API disponibles. Sélectionnez « SugarCRM » dans la galerie. Le « connecteur SugarCRM » est ajouté au flux : ![][3]

5.	Sélectionnez l'action Créer un compte et les paramètres d'entrée s'affichent : ![][4]

6.	Indiquez le nom « Compte Microsoft » et cliquez sur ✓ : ![][5]

7.	Sélectionnez « Connecteur SugarCRM » dans la section « Récemment utilisé » de la galerie. Une action SugarCRM est ajoutée.

8.	Sélectionnez « Mettre à jour le compte » (sous les actions avancées « ... ») dans la liste des actions ; les paramètres d’entrée de cette action s’affichent : ![][6]

9.	Cliquez sur « ... » en regard de « ID d'enregistrement » pour sélectionner la valeur « ID » de la sortie de l'action « Créer un compte » : ![][7]

10.	Indiquez les éléments de l’adresse de facturation et cliquez sur ✓ : ![][8]

11. Cliquez sur OK dans l’éditeur d’application logique, puis cliquez sur Créer. Environ 30 secondes sont nécessaires pour terminer la création.

12. Accédez à l’application logique que vous venez de créer, puis cliquez sur « Exécuter maintenant » pour l’exécuter.

13. Vous pouvez vérifier qu'un compte nommé « Compte Microsoft » figure dans votre compte SugarCRM. Dans ce compte, les adresses de facturation sont mises à jour.

## En faire plus avec votre connecteur
Maintenant que le connecteur est créé, vous pouvez l'ajouter à un flux d'entreprise à l'aide d'une application logique. Voir [Que sont les applications logiques ?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Si vous voulez vous familiariser avec Azure Logic Apps avant d'ouvrir un compte Azure, accédez à la page [Essayer Logic App](https://tryappservice.azure.com/?appservice=logic), où vous pourrez créer immédiatement une application logique temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

Affichez la référence d’API REST Swagger sur [Référence de connecteurs et d’applications API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Vous pouvez également consulter les statistiques de performances et contrôler la sécurité du connecteur. Consultez la page [Gestion et contrôle de vos connecteurs et applications API intégrés](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-sugarcrm/1_New_Logic_App.png
[2]: ./media/app-service-logic-connector-sugarcrm/2_Logic_App_Settings.png
[3]: ./media/app-service-logic-connector-sugarcrm/3_Select_SugarCRM_Gallery.png
[4]: ./media/app-service-logic-connector-sugarcrm/4_SugarCRM_Create_Account.png
[5]: ./media/app-service-logic-connector-sugarcrm/5_Create_Account_OK.png
[6]: ./media/app-service-logic-connector-sugarcrm/6_SugarCRM_Update_Account.png
[7]: ./media/app-service-logic-connector-sugarcrm/7_Record_ID_from_Create.png
[8]: ./media/app-service-logic-connector-sugarcrm/8_Update_Account_Address.png
[9]: ./media/app-service-logic-connector-sugarcrm/9_Create_new_SugarCRM_connector.png

<!---HONumber=AcomDC_0224_2016-->