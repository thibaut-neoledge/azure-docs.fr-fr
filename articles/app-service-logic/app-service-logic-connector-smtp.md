<properties
   pageTitle="Utilisation du connecteur SMTP dans des applications logiques | Microsoft Azure App Service"
   description="Comment créer et configurer le connecteur SMTP ou une application API et l'utiliser dans une application logique d’Azure App Service"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="rajeshramabathiran"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="11/11/2015"
   ms.author="rajram"/>


# Prise en main du connecteur SMTP et ajout de celui-ci à votre application logique
Connexion à un serveur SMTP et envoie d’e-mails avec des pièces jointes. Avec l’action « Envoyer le message électronique » du connecteur SMTP, vous pouvez envoyer un message électronique à une ou plusieurs adresses électroniques.

Les applications logiques peuvent se déclencher selon diverses sources de données et proposent des connecteurs pour obtenir et traiter les données dans le cadre d’un flux de travail. Vous pouvez ajouter le connecteur SMTP à votre flux de travail professionnel et traiter les données dans le cadre de ce flux de travail dans une application logique.


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

> [AZURE.IMPORTANT]Le fonctionnement de ce connecteur (SendGrid et Gmail) peut occasionner des problèmes sur certains serveurs SMTP. Si vous voulez envoyer du courrier à partir de SendGrid, notre [référentiel GitHub](https://github.com/logicappsio/SendGridAPI) contient une API personnalisée qui communique directement avec les API SendGrid.

## Utilisation du connecteur SMTP dans votre application logique
Une fois votre connecteur créé, vous pouvez utiliser le connecteur SMTP comme action pour votre application logique. Pour ce faire :

1.	Créez une application logique :
![][2]
2.	Ouvrez **Déclencheurs et actions** pour ouvrir le concepteur Logic Apps et configurer votre flux de travail :
![][3]
3.	Le connecteur SMTP apparaît dans la section « Applications API dans ce groupe de ressources » de la galerie située à droite. Sélectionnez-le :
![][4]
4.	Sélectionnez le connecteur SMTP pour l’ajouter automatiquement au Concepteur de flux de travail.

Vous pouvez maintenant configurer le connecteur SMTP à utiliser dans votre flux de travail. Sélectionnez l’action **Envoyer l’e-mail** et configurez les propriétés d’entrée :

	Propriété | Description
	--- | ---
	À | Entrez l'adresse e-mail du ou des destinataire(s). Séparez les adresses e-mail par un point-virgule (;). Par exemple, entrez: *recipient1@domain.com;recipient2@domain.com*.
	Cc | Entrez l'adresse e-mail du ou des destinataire(s) de la copie carbone. Séparez les adresses e-mail par un point-virgule (;). Par exemple, entrez: *recipient1@domain.com;recipient2@domain.com*.
	Objet | Entrez l'objet de l'e-mail.
	Corps | Entrez le corps de l'e-mail.
	Is HTML | Lorsque cette proriété est définie sur true, le contenu du corps est envoyé en HTML.
	Cci | Entrez l'adresse e-mail du ou des destintaire(s) pour la copie carbone invisible. Séparez les adresses e-mail par un point-virgule (;). Par exemple, entrez: *recipient1@domain.com;recipient2@domain.com*.
	Importance | Entrez l'importance de l'e-mail. Les options sont Normale, Faible et Élevée.
	Pièces jointes | Les pièces jointes à envoyer avec l'e-mail. Cette section contient les champs suivants: <ul><li>Contenu (Chaîne)</li><li>Codage du transfert du contenu (Enum) (“none”|”base64”)</li><li>Type de contenu (Chaîne)</li><li>ID du contenu (Chaîne)</li><li>Nom de fichier (Chaîne)</li></ul>

![][5]
![][6]

## En faire plus avec votre connecteur
Maintenant que le connecteur est créé, vous pouvez l'ajouter à un flux d'entreprise à l'aide d'une application logique. Voir [Que sont les applications logiques ?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure Logic Apps avant d'ouvrir un compte Azure, accédez à la page [Essayer Logic App](https://tryappservice.azure.com/?appservice=logic), où vous pourrez créer immédiatement une application logique temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

Affichez la référence d’API REST Swagger sur [Référence de connecteurs et d’applications API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Vous pouvez également consulter les statistiques de performances et contrôler la sécurité du connecteur. Consultez la page [Gestion et contrôle de vos connecteurs et applications API intégrés](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-smtp/img1.PNG
[2]: ./media/app-service-logic-connector-smtp/img2.PNG
[3]: ./media/app-service-logic-connector-smtp/img3.png
[4]: ./media/app-service-logic-connector-smtp/img4.PNG
[5]: ./media/app-service-logic-connector-smtp/img5.PNG
[6]: ./media/app-service-logic-connector-smtp/img6.PNG

<!---HONumber=Nov15_HO3-->