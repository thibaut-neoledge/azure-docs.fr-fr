<properties 
	pageTitle="Utiliser des émoticônes Emoji dans Azure Mobile Engagement" 
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
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/19/2016" 
	ms.author="piyushjo" />

#Utiliser l’émoticône Emoji dans les notifications push

Vous pouvez inclure des émoticônes Emoji dans votre notification Push en quelques étapes simples :

1. Recherchez d’abord l’Emoji à insérer dans le message. Veillez à ce qu’elle soit prise en charge par l’appareil cible. En effet, l’ajout d’émoticônes Emoji nouvellement approuvées aux plateformes d’appareils peut prendre un certain temps.

2. Sur **Windows** : vous pouvez accéder à ce [lien](http://apps.timwhitlock.info/emoji/tables/unicode) et copier l’icône « Native ».

	![][7]

3. Sur **Mac** : les émoticônes Emoji se trouvent dans l’application Dictionnaire sous Modifier -> Emoji et symboles.

	![][6]

4. Accédez à l’onglet **Reach** du portail Azure Mobile Engagement. Sélectionnez le type de votre notification push (annonce, interrogation, etc.). Pour cet exemple, nous choisissons une annonce push.

5. Spécifiez les différents champs de la notification jusqu'à ce que vous atteigniez le texte de la notification. C’est là que vous allez ajouter votre émoticône Emoji. Vous pouvez la placer dans le titre, dans le message ou les deux. Faites glisser-déplacer ou copiez les émoticônes Emoji figurant dans les emplacements ci-dessus.

	![][1]

6. Renseignez les autres champs de la notification et enregistrez-la.

7. Lorsque vous exécutez un test ou activez l'annonce, une notification s’affiche avec l'émoticône spécifiée.

	![][3] ![][4] ![][5]

<!-- Images. -->
[1]: ./media/mobile-engagement-use-emoji-with-push/notification_input.png
[3]: ./media/mobile-engagement-use-emoji-with-push/iOS_Emoji.png
[4]: ./media/mobile-engagement-use-emoji-with-push/Android_Emoji.png
[5]: ./media/mobile-engagement-use-emoji-with-push/WindowsPhone_Emoji.png
[6]: ./media/mobile-engagement-use-emoji-with-push/Mac_SelectEmoji.png
[7]: ./media/mobile-engagement-use-emoji-with-push/Windows_SelectEmoji.png

<!---HONumber=AcomDC_0824_2016-->