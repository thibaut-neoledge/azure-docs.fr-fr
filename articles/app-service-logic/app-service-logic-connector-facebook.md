<properties
   pageTitle="Utilisation du connecteur Facebook dans votre application logique dans Azure App Service"
	description="Utilisation du connecteur Facebook dans une application logique"
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


# Connecteur Facebook

Les applications logiques peuvent se déclencher selon diverses sources de données et proposent des connecteurs pour obtenir et traiter les données dans le cadre du flux.

- Le déclencheur du connecteur Facebook récupère la nouvelle publication sur le fil d'actualité d'un utilisateur ou la nouvelle publication sur une page. Quand un nouveau tweet est récupéré, il déclenche une nouvelle instance du flux et transfère les données reçues dans la requête au flux à des fins de traitement.
- Les actions du connecteur Facebook vous permettent de publier un commentaire, une photo, etc. Ces actions obtiennent à leur tour une réponse et la rendent disponible pour les actions dans le flux à consommer.

## Déclencheurs et actions

Déclencheurs | Actions
--- | ---
<ul><li>Nouvelle publication sur le fil d'actualité d'un utilisateur</li><li>Nouvelle publication sur une page</li></ul> | <ul><li>Publier le message</li><li>Publier une photo</li></ul>



## Création d'un connecteur Facebook pour votre application logique
Pour utiliser le connecteur Facebook, vous devez d'abord créer une instance de l'application API du connecteur Facebook. Pour cela, procédez comme suit :

1. Dans le tableau d'accueil Azure, sélectionnez **Marketplace**.
2. Recherchez « Connecteur Facebook », sélectionnez-le et sélectionnez **Créer**.
3. Entrez le nom, le plan App Service et d'autres propriétés : ![][1]
4.	Sélectionnez **Créer**.


## Utilisation du connecteur Facebook dans votre application logique
Une fois votre application API créée, vous pouvez utiliser le connecteur Facebook comme déclencheur/action pour votre application logique. Pour cela, vous devez procéder comme suit :

1.	Dans votre application logique, ouvrez **Déclencheurs et actions** pour ouvrir le concepteur d'applications logiques et configurer votre flux : ![][3]
2.	Le connecteur Facebook est répertorié dans la galerie : ![][4]
3. Sélectionnez le connecteur Facebook pour l'ajouter automatiquement dans le concepteur. Sélectionnez **Autoriser**, entrez vos informations d'identification, puis sélectionnez **Autoriser** : ![][5] ![][6] ![][7] ![][8]
4.	Sélectionnez un déclencheur : ![][9]

Vous pouvez maintenant utiliser les publications récupérées à partir du déclencheur Facebook dans d'autres actions. Dans le flux ci-dessous, chaque fois qu'une nouvelle publication est disponible sur le fil d'actualité Facebook de l'utilisateur, la même publication est tweetée dans le fil d'actualité Twitter de l'utilisateur : ![][10]

De la même façon, vous pouvez créer des flux à l'aide des actions du connecteur Facebook. Le flux ci-dessous récupère le nouveau message publié sur le groupe Yammer et publie la même publication sur la page Facebook gérée par l'utilisateur : ![][11]

> [AZURE.TIP]Pour obtenir l'ID de la page Facebook ou l'ID du groupe Yammer, recherchez le code numérique dans l'URL.

## En faire plus avec votre connecteur
Maintenant que le connecteur est créé, vous pouvez l'ajouter à un flux d'entreprise à l'aide d'une application logique. Voir [Que sont les applications logiques ?](app-service-logic-what-are-logic-apps.md).

Affichez la référence d’API REST Swagger sur [Référence de connecteurs et d’applications API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Vous pouvez également consulter les statistiques de performances et contrôler la sécurité du connecteur. Consultez la page [Gestion et contrôle de vos connecteurs et applications API intégrés](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-facebook/img1.png
[2]: ./media/app-service-logic-connector-facebook/img2.png
[3]: ./media/app-service-logic-connector-facebook/img3.png
[4]: ./media/app-service-logic-connector-facebook/img4.png
[5]: ./media/app-service-logic-connector-facebook/img5.png
[6]: ./media/app-service-logic-connector-facebook/img6.png
[7]: ./media/app-service-logic-connector-facebook/img7.png
[8]: ./media/app-service-logic-connector-facebook/img8.png
[9]: ./media/app-service-logic-connector-facebook/img9.png
[10]: ./media/app-service-logic-connector-facebook/img10.png
[11]: ./media/app-service-logic-connector-facebook/img11.png

<!---HONumber=August15_HO8-->