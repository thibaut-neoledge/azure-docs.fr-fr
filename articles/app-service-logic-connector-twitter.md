<properties 
	pageTitle="Application API du connecteur Twitter" 
	description="Utilisation du connecteur Twitter" 
	services="app-service\logic" 
	documentationCenter=".net,nodejs,java" 
	authors="adgoda" 
	manager="dwrede" 
	editor=""/>

<tags
	ms.service="app-service-logic"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="integration" 
	ms.date="03/20/2015"
	ms.author="adgoda"/>


# Utilisation du connecteur Twitter dans votre application logique #

Les applications logiques peuvent se déclencher selon diverses sources de données et proposent des connecteurs pour obtenir et traiter les données dans le cadre du flux. 

Le connecteur Twitter vous permet de publier des tweets et d'obtenir les tweets de votre fil d'actualité, vos amis et vos abonnés à partir de votre compte Twitter.

- Le déclencheur du connecteur Twitter récupère les nouveaux tweets associés au mot clé donné. Quand un nouveau tweet est récupéré, il déclenche une nouvelle instance du flux et transfère les données reçues dans la requête au flux à des fins de traitement. 
- Les actions du connecteur Twitter vous permettent de tweeter, rechercher des tweets, retweeter, obtenir le fil d'actualité d'un utilisateur, etc. Ces actions obtiennent à leur tour une réponse et la rendent disponible pour les actions dans le flux à consommer.

## Création d'un connecteur Twitter pour votre application logique ##
Pour utiliser le connecteur Twitter, vous devez d'abord créer une instance de l'application API du connecteur Twitter. Pour cela, procédez comme suit :

1. Ouvrez Azure Marketplace à l'aide de l'option + NOUVEAU en bas à gauche du portail Azure.
1. Accédez à " Web et mobile > API Apps " et recherchez " Connecteur Twitter ".
1. Configurez le connecteur Twitter comme suit :

	![][1]
4.	Cliquez sur OK pour créer.
5.	Une fois l'instance de l'application API créée, vous pouvez créer une application logique dans le même groupe de ressources pour utiliser le connecteur Twitter. 
	- L'instance de l'application API du connecteur Twitter peut également être créée à partir de l'application logique. 
	- Ouvrez l'éditeur d'application logique et cliquez sur le connecteur Twitter disponible dans la galerie située sur le côté droit.
	- Une instance de l'application API du connecteur Twitter est alors créée dans le même groupe de ressources que celui dans lequel l'application logique a été créée.


## Utilisation du connecteur Twitter dans votre application logique ##
Une fois votre application API créée, vous pouvez utiliser le connecteur Twitter comme déclencheur/action pour votre application logique. Pour cela, vous devez procéder comme suit :

1.	Créez une application logique et choisissez le même groupe de ressources qui comporte le connecteur Twitter.
 	
	![][2]
2.	Ouvrez " Déclencheurs et actions " pour ouvrir le concepteur d'applications logiques et configurer votre flux. 
 	
	![][3]
3.	Le connecteur Twitter apparaît dans la section des éléments récemment utilisés dans la galerie située du côté droit. Sélectionnez-le.
 
	![][4]
4.	Vous pouvez déposer l'application API du connecteur Twitter dans l'éditeur en cliquant sur le connecteur Twitter dans les éléments récemment utilisés à partir de la galerie située du côté droit. Cliquez sur le bouton Autoriser. Indiquez vos informations d'identification Twitter. Cliquez sur " Autoriser l'application ".
 
	![][5]
6.	Vous pouvez maintenant utiliser le connecteur Twitter dans le flux. Vous pouvez maintenant utiliser les tweets récupérées à partir du déclencheur Twitter dans d'autres actions du flux.
 
	![][6]
7.	De la même façon, vous pouvez utiliser les actions Twitter dans le flux. Sélectionnez une action Twitter et configurez les entrées de cette action respective.

	![][7] ![][8]

	<!--Image references-->
[1]: ./media/app-service-logic-connector-twitter/img1.png
[2]: ./media/app-service-logic-connector-twitter/img2.png
[3]: ./media/app-service-logic-connector-twitter/img3.png
[4]: ./media/app-service-logic-connector-twitter/img4.png
[5]: ./media/app-service-logic-connector-twitter/img5.png
[6]: ./media/app-service-logic-connector-twitter/img6.png
[7]: ./media/app-service-logic-connector-twitter/img7.png
[8]: ./media/app-service-logic-connector-twitter/img8.png

<!--HONumber=49-->