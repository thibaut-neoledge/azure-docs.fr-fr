<properties
   pageTitle="Application API du connecteur Facebook"
   description="Utilisation du connecteur Facebook"
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
   ms.author="andalmia"/>


# Utilisation du connecteur Facebook dans votre application logique #

Les applications logiques peuvent se déclencher selon diverses sources de données et proposent des connecteurs pour obtenir et traiter les données dans le cadre du flux.

Le connecteur Facebook vous permet de récupérer une nouvelle publication sur le fil d'actualité d'un utilisateur, une nouvelle publication sur une page, de publier un commentaire, une photo, etc. à partir de votre compte Facebook.

- Le déclencheur du connecteur Facebook récupère la nouvelle publication sur le fil d'actualité d'un utilisateur ou la nouvelle publication sur une page. Quand un nouveau tweet est récupéré, il déclenche une nouvelle instance du flux et transfère les données reçues dans la requête au flux à des fins de traitement.
- Les actions du connecteur Facebook vous permettent de publier un commentaire, une photo, etc. Ces actions obtiennent à leur tour une réponse et la rendent disponible pour les actions dans le flux à consommer.

## Création d'un connecteur Facebook pour votre application logique ##
Pour utiliser le connecteur Facebook, vous devez d'abord créer une instance de l'application API du connecteur Facebook. Pour cela, procédez comme suit :

1.	Ouvrez Azure Marketplace à l’aide de l’option + NOUVEAU en bas à gauche du portail Azure.
2.	Accédez à « Web et mobile > API Apps » et recherchez « Connecteur Facebook ».
3.	Configurez le connecteur Facebook comme suit :

	![][1]
4.	Cliquez sur OK pour créer.
5.	Une fois l'instance de l'application API créée, vous pouvez créer une application logique dans le même groupe de ressources pour utiliser le connecteur Facebook.
	- L'instance de l'application API du connecteur Facebook peut également être créée à partir de l'application logique.
	- Ouvrez l'éditeur d'application logique et cliquez sur le connecteur Facebook disponible dans la galerie située sur le côté droit.
	- Une instance de l'application API du connecteur Facebook est alors créée dans le même groupe de ressources que celui dans lequel l'application logique a été créée.


## Utilisation du connecteur Facebook dans votre application logique ##
Une fois votre application API créée, vous pouvez utiliser le connecteur Facebook comme déclencheur/action pour votre application logique. Pour cela, vous devez procéder comme suit :

1.	Créez une application logique et choisissez le même groupe de ressources qui comporte le connecteur Facebook.

	![][2]
2.	Ouvrez « Déclencheurs et actions » pour ouvrir le concepteur d’applications logiques et configurer votre flux.

	![][3]
3.	Le connecteur Facebook apparaît dans la section des éléments récemment utilisés dans la galerie située du côté droit. Sélectionnez-le.

	![][4]
4.	Vous pouvez déposer l'application API du connecteur Facebook dans l'éditeur en cliquant sur le connecteur Facebook dans les éléments récemment utilisés à partir de la galerie située du côté droit. Cliquez sur le bouton Autoriser. Indiquez vos informations d'identification Facebook.

	![][5]
5.	Autoriser les « applications logiques Azure AppService »

	![][6] ![][7] ![][8]
6.	Sélectionnez un déclencheur.

	![][9]
7.	Vous pouvez maintenant utiliser les publications récupérées à partir du déclencheur Facebook dans d'autres actions. Dans le flux ci-dessous, chaque fois qu'une nouvelle publication est disponible sur le fil d'actualité Facebook de l'utilisateur, la même publication est tweetée dans le fil d'actualité Twitter de l'utilisateur.

	![][10]
8.	De la même façon, vous pouvez créer des flux à l'aide des actions du connecteur Facebook. Le flux ci-dessous récupère le nouveau message publié sur le groupe Yammer et publie la même publication sur la page Facebook gérée par l'utilisateur.

	![][11]

**CONSEIL** : pour obtenir l’ID de la page Facebook ou l’ID du groupe Yammer, recherchez le code numérique dans l’URL.

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

<!---HONumber=July15_HO4-->