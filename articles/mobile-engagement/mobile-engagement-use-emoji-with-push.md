<properties 
	pageTitle="Utiliser l’émoticône Emoji dans les notifications push" 
	description="Comment utiliser des émoticônes Emoji dans vos notifications push"					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/06/2015" 
	ms.author="piyushjo" />

#Utiliser l’émoticône Emoji dans les notifications push

Vous pouvez inclure des émoticônes Emoji dans votre notification push. Actuellement, Azure Mobile Engagement ne prend en charge que les émoticônes 3 octets Emoji pour les notifications texte dans et en dehors de l'application. Procédez comme suit :

1.  Vous devez commencer par rechercher une bibliothèque d'émoticônes Emoji 3 octets. Vous trouverez toutes les émoticônes Emoji utilisables en cliquant sur le [lien](http://stackoverflow.com/questions/10153529/emoji-on-mysql-and-php-why-some-symbol-yes-other-not) suivant.

	![][1]

2. Accédez à l'onglet Reach du portail Azure Mobile Engagement.

3. Sélectionnez le type de votre notification push (annonce, interrogation, etc.). Pour cet exemple, nous choisissons une annonce push.

4. Spécifiez les différents champs de la notification jusqu'à ce que vous atteigniez le texte de la notification. C’est là que vous allez ajouter votre émoticône Emoji. Vous pouvez la placer dans le titre, dans le message ou les deux.

	![][2]

5. Coupez l'émoticône Emoji que vous souhaitez utiliser à partir du lien précédent. Collez-la directement dans le titre et/ou le message, à l'emplacement de votre choix.

6. Renseignez les autres champs de la notification et enregistrez-la.

7. Lorsque vous exécutez un test ou activez l'annonce, une notification s’affiche avec l'émoticône spécifiée.

	![][3] ![][4]

<!-- Images. -->
[1]: ./media/mobile-engagement-use-emoji-with-push/emoji.png
[2]: ./media/mobile-engagement-use-emoji-with-push/notification_input.png
[3]: ./media/mobile-engagement-use-emoji-with-push/notification_android.png
[4]: ./media/mobile-engagement-use-emoji-with-push/notification_ios.png
 

<!---HONumber=July15_HO2-->