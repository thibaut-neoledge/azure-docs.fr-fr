<properties 
   pageTitle="Connecteur Office 365" 
   description="Utilisation du connecteur Office 365" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="sutalasi" 
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


# Utilisation du connecteur Office 365 dans votre application logique#

Les applications logiques peuvent se déclencher selon diverses sources de données et proposent des connecteurs pour obtenir et traiter les données dans le cadre du flux. Le connecteur Office 365 vous permet d'envoyer et recevoir des messages électroniques, de gérer un calendrier et des contacts dans votre compte Office 365. Vous pouvez effectuer diverses actions comme envoyer, recevoir et obtenir des messages messages électroniques, créer et supprimer des événements dans votre calendrier et créer, mettre à jour, obtenir et supprimer vos contacts.

**Actions de base**

- Nouveau message (déclencheur)
- Envoyer un message
- Répondre à un message
- Envoyer un événement
- Ajouter un contact


## Créer une application logique##
Pour utiliser un connecteur Office 365 dans votre application logique, l'application API du connecteur doit déjà avoir été créée ou vous pouvez en créer une à la volée dans l'application logique. Créons une application logique simple qui se déclenche quand un message électronique est reçu (à votre id de messagerie pour les demandes de renseignements commerciaux, par exemple sales@contoso.com). Elle crée un événement, ajoute un contact avec les détails de l'expéditeur, envoie un message électronique à votre compte personnel et enfin envoie une réponse avec un accusé de réception.

1.	Connectez-vous au portail Azure et cliquez sur Nouveau -> Web + mobile -> Application logique.

	![][1]

2.	Dans la page Créer une application logique, fournissez les informations requises, telles que le nom, le plan de service de l'application et l'emplacement.

	![][2]

3.	Cliquez sur Déclencheurs et actions. L'écran de l'éditeur d'application logique s'affiche.

	![][3]

4.	Développez Créer dans la galerie pour afficher toutes les applications API disponibles. Sélectionnez Office 365 dans la galerie et le déclencheur Office 365 s'ajoute au flux.
	![][4]

5.	Une nouvelle application API de connecteur Office 365 est alors créée dans le même groupe de ressources que l'application logique. Environ 30 secondes sont nécessaires pour créer l'application API.

6.	Une fois l'application créée, cliquez sur Autoriser pour indiquer les informations d'identification Office 365.

	![][5]

7.	Vous êtes redirigé vers la page de connexion Office 365, puis vous pouvez vous authentifier avec vos informations d'identification Office 365.

	![][6]

	![][7]

8.	Une fois que l'autorisation est terminée, les déclencheurs Office 365 s'affichent.

	![][8]

9.	Sélectionnez le déclencheur Nouveau message et les paramètres d'entrée s'affichent.


10.	Remplacez la fréquence de déclenchement par  'Minutes' et cliquez sur ✓. 

	![][9]

11. Le déclencheur  'New Email' Office 365 est configuré et vous pouvez également voir les paramètres de sortie.
	
	![][10]

12.	Sélectionnez Connecteur Office 365 dans la section des éléments récemment utilisés dans la galerie et une nouvelle action Office 365 est ajoutée.

13.	Sélectionnez Envoyer un événement dans la liste des actions et les paramètres d'entrée de cette action s'affichent.

	![][11]

14.	Spécifiez les détails de l'événement, puis cliquez sur ✓.

	![][12]

15.	Sélectionnez Connecteur Office 365 dans la section des éléments récemment utilisés dans la galerie et une nouvelle action Office 365 est ajoutée.

16.	Sélectionnez Ajouter un contact dans la liste des actions et les paramètres d'entrée de cette action s'affichent.

	![][13]

17.	Cliquez sur + en regard du champ  'Email Address', puis sélectionnez la valeur du champ de sortie  'From' à partir du déclencheur.

	![][14]

18. Cliquez sur ✓. La configuration de l'action est terminée.

	![][15]

19.	Sélectionnez Connecteur Office 365 dans la section des éléments récemment utilisés dans la galerie et une nouvelle action Office 365 est ajoutée.


20.	Sélectionnez Envoyer un message dans la liste des actions et les paramètres d'entrée de cette action s'affichent.

	![][19]

21.	Indiquez les détails requis pour envoyer le message électronique. Vous pouvez rédiger un message en tapant quelque chose comme ci-dessous. Une fois l'action  'Send Email' configurée, cliquez sur ✓.

		Body - @concat('You got a new sales enquiry from',triggers().output.body.From)

	![][20]
22.	Sélectionnez Connecteur Office 365 dans la section des éléments récemment utilisés dans la galerie et une nouvelle action Office 365 est ajoutée.


23.	Sélectionnez Répondre dans la liste des actions et les paramètres d'entrée de cette action s'affichent.

	![][16]

24.	Cliquez sur + en regard du champ  'From' et sélectionnez la valeur de l'id de message de sortie du déclencheur, puis cliquez sur ✓.

	![][17]

25. Cliquez sur OK dans l'écran de l'éditeur d'application logique, puis sur  'Create'. Environ 30 secondes sont nécessaires pour terminer la création.

26. Envoyez un message électronique au compte avec lequel vous avez configuré votre déclencheur. Un message électronique doit apparaître dans votre compte de messagerie personnel et un événement de calendrier ainsi qu'un contact doivent apparaître dans votre compte de messagerie professionnel. De plus, vous devez obtenir une réponse confirmant que la demande de renseignements commerciaux sera traitée dans les meilleurs délais.

<!--Image references-->
[1]: ./media/app-service-logic-connector-office365/1_New_Logic_App.png
[2]: ./media/app-service-logic-connector-office365/2_Logic_App_Settings.png
[3]: ./media/app-service-logic-connector-office365/3_Logic_App_Editor.png
[4]: ./media/app-service-logic-connector-office365/4_Select_Office365_Gallery.png
[5]: ./media/app-service-logic-connector-office365/5_Office365_Authorize.png
[6]: ./media/app-service-logic-connector-office365/6_Office365_Login.png
[7]: ./media/app-service-logic-connector-office365/7_Office365_User_Consent.png
[8]: ./media/app-service-logic-connector-office365/8_Office365_Trigger.png
[9]: ./media/app-service-logic-connector-office365/9_Office365_Trigger_Settings.png
[10]: ./media/app-service-logic-connector-office365/10_Office365_Trigger_Configured.png
[11]: ./media/app-service-logic-connector-office365/11_Office365_Actions_List.png
[12]: ./media/app-service-logic-connector-office365/12_Office365_Create_Event_Inputs.png
[13]: ./media/app-service-logic-connector-office365/13_Office365_Add_Contact_Inputs.png
[14]: ./media/app-service-logic-connector-office365/14_Office365_Add_Contact_Email_FromTrigger.png
[15]: ./media/app-service-logic-connector-office365/15_Office365_Add_Contacts_Configured.png
[16]: ./media/app-service-logic-connector-office365/16_Office365_Reply_To_Inputs.png
[17]: ./media/app-service-logic-connector-office365/17_Office365_Reply_To_MessageId.png
[18]: ./media/app-service-logic-connector-office365/18_Office365_Reply_To_Configured.png
[19]: ./media/app-service-logic-connector-office365/19_Office365_Send_Inputs.png
[20]: ./media/app-service-logic-connector-office365/20_Office365_Send_Configured.png


<!--HONumber=49-->