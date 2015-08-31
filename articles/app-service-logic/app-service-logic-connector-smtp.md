<properties
   pageTitle="Application API du connecteur SMTP"
	description="Utilisation du connecteur SMTP"
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
	ms.date="08/19/2015"
	ms.author="andalmia"/>


# Connecteur SMTP

Les applications logiques peuvent se déclencher selon diverses sources de données et proposent des connecteurs pour obtenir et traiter les données dans le cadre d’un flux de travail.

Le connecteur SMTP vous permet de vous connecter à un serveur SMTP et d’exécuter une action pour envoyer un message électronique avec des pièces jointes. Avec l’action « Envoyer le message électronique » du connecteur SMTP, vous pouvez envoyer un message électronique à une ou plusieurs adresses électroniques.

## Déclencheurs et actions
Les *déclencheurs* sont des événements qui se produisent. Par exemple, lorsqu'une commande est mise à jour ou lorsqu'un nouveau client est ajouté. Une *action* est le résultat du déclencheur. Par exemple, lorsqu’une commande est mise à jour ou si un nouveau client est ajouté, un courrier électronique est envoyé au nouveau client.

Le connecteur SMTP peut être utilisé comme une action dans une application logique et prend en charge les données aux formats JSON et XML. Actuellement, il n’y a aucun déclencheur pour ce connecteur.

Le connecteur SMTP propose les déclencheurs et les actions suivants :

Déclencheurs | Actions
--- | ---
Aucun | Envoi de courrier électronique


## Créer le connecteur SMTP
Un connecteur peut être créé dans une application logique ou directement à partir d'Azure Marketplace. Pour créer un connecteur à partir de Marketplace :

1. Dans le tableau d'accueil Azure, sélectionnez **Marketplace**.
2. Sélectionnez **API Apps** et recherchez « Connecteur SMTP ».
3. **Créez** le connecteur.
4. Entrez le nom, le plan App Service et d'autres propriétés.
5. Entrez les paramètres de package suivants :

	Nom | Requis | Description
	--- | --- | ---
	Nom d’utilisateur | Oui | Entrez un nom d’utilisateur pouvant se connecter au serveur SMTP.
	Mot de passe | Oui | Entrez le mot de passe utilisateur.
	Adresse du serveur | Oui | Entrez le nom du serveur SMTP ou l’adresse IP.
	Port du serveur | Oui | Entrez le numéro de port du serveur SMTP.
	Activer SSL | Non | Spécifiez *true* pour utiliser SMTP sur un canal SSL/TLS sécurisé.

6. Sélectionnez **Créer**.

## Utilisation du connecteur SMTP dans votre application logique
Une fois votre connecteur créé, vous pouvez utiliser le connecteur SMTP comme action pour votre application logique. Pour ce faire :

1.	Création d’une application logique :

	![][2]
2.	Ouvrez **Déclencheurs et actions** pour ouvrir le concepteur d’applications logiques et configurer votre flux de travail :

	![][3]
3.	Le connecteur SMTP apparaît dans la section « Applications API dans ce groupe de ressources » de la galerie située à droite. Sélectionnez-le :

	![][4]
4.	Sélectionnez le connecteur SMTP pour l’ajouter automatiquement au Concepteur de flux de travail.

Vous pouvez maintenant configurer le connecteur SMTP à utiliser dans votre flux de travail. Sélectionnez l’action **Envoyer le message électronique** et configurez les propriétés d’entrée :

	Property | Description
	--- | ---
	To | Enter the email address of recipient(s). Separate multiple email addresses using a semicolon (;). For example, enter: *recipient1@domain.com;recipient2@domain.com*.
	Cc | Enter the email address of the carbon copy recipient(s). Separate multiple email addresses using a semicolon (;). For example, enter: *recipient1@domain.com;recipient2@domain.com*.
	Subject | Enter the subject of the email.
	Body | Enter body of the email.
	Is HTML | When this property is set to true, the contents of the body are sent as HTML.
	Bcc | Enter the email address of recipient(s) for blind carbon copy. Separate multiple email addresses using a semicolon (;). For example, enter: *recipient1@domain.com;recipient2@domain.com*.
	Importance | Enter the Importance of the email. The options are Normal, Low, and High.
	Attachments | Attachments to be sent along with the email. It contains the following fields: <ul><li>Content (String)</li><li>Content transfer Encoding (Enum) (“none”|”base64”)</li><li>Content Type (String)</li><li>Content ID (String)</li><li>File Name (String)</li></ul>

	![][5]
	![][6]

## En faire plus avec votre connecteur
Maintenant que le connecteur est créé, vous pouvez l'ajouter à un flux d'entreprise à l'aide d'une application logique. Voir [Que sont les applications logiques ?](app-service-logic-what-are-logic-apps.md).

Affichez la référence d’API REST Swagger sur [Référence de connecteurs et d’applications API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Vous pouvez également consulter les statistiques de performances et contrôler la sécurité du connecteur. Consultez la page [Gestion et contrôle de vos connecteurs et applications API intégrés](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-smtp/img1.PNG
[2]: ./media/app-service-logic-connector-smtp/img2.PNG
[3]: ./media/app-service-logic-connector-smtp/img3.png
[4]: ./media/app-service-logic-connector-smtp/img4.PNG
[5]: ./media/app-service-logic-connector-smtp/img5.PNG
[6]: ./media/app-service-logic-connector-smtp/img6.PNG

<!---HONumber=August15_HO8-->