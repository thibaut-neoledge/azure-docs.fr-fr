<properties 
   pageTitle="Guides de procédures Azure Mobile Engagement" 
   description="Guides de procédures pour Azure Mobile Engagement" 
   services="mobile-engagement" 
   documentationCenter="mobile" 
   authors="v-micada" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="Java"
   ms.topic="article"
   ms.tgt_pltfrm="mobile"
   ms.workload="mobile" 
   ms.date="02/17/2015"
   ms.author="v-micada"/>

# Azure Mobile Engagement - Guides de procédures

## Introduction

Les guides de procédures suivants pour l'interface utilisateur d'Azure Mobile Engagement supposent que vous êtes familiarisé avec les [concepts] de base[Link 6] d'Azure Mobile Engagement et ne s'appliquent qu'une fois que vous avez intégré le [SDK] Azure Engagement Mobile[Link 5] à votre application. Ces procédures pas à pas expliquent davantage comment les utilisateurs standard peuvent utiliser l'[interface utilisateur][Link 1] que comment les développeurs peuvent utiliser les [APIs] HTTP REST[Link 4]. Si vous rencontrez des difficultés avec l'une de ces [procédures pas à pas],[Link 3] consultez les [guides de dépannage] d'Azure Mobile Engagement[Link 2].

## <a name="#First">Lancer votre première campagne de notification push</a>
-    Vérifiez que votre module Couverture est intégré à votre application avec le SDK. 
-    Sélectionnez votre application
 
![First1][1]

-    Accédez à la section " Couverture ", puis cliquez sur " Nouvelle annonce "
 
![First2][2]

-    Créez une campagne et donnez-lui un nom
 
 ![First3][3]

-    Sélectionnez la façon dont la notification doit être remise, par exemple, Dans l'application uniquement
 
![First4][4]

-    Créez le message à envoyer
 
![First5][5]

-    Vous pouvez donner un titre à la notification (facultatif).
-    Écrivez le contenu du message push.
-    Vous pouvez télécharger une image. N'oubliez pas que la taille du fichier ne peut pas dépasser 32 768 octets.
-    Vous avez également la possibilité de sélectionner d'autres options, mais pour les besoins de ce didacticiel, nous les aborderons plus tard.

-    Sélectionnez Notification uniquement comme type de contenu
 
![First6][6]

-    Créez votre campagne push. Elle apparaîtra dans votre liste de campagnes.
 
![First7][7]

## <a name="#Test">Tester votre campagne de notification push</a>
 
![Test1][8]

-    Enregistrez votre appareil.
-    Cliquez sur la case à cocher de l'appareil auquel vous voulez envoyer la notification push.
-    Cliquez sur le bouton " Test " pour envoyer la notification push à l'appareil.
 
![Test2][9]

-    Activez la campagne
 
![Test3][10]

-    Maintenant que vous avez créé votre campagne, vous devez l'activer pour que la notification soit envoyée à vos utilisateurs.
 
## <a name="#Personalize">Envoyer des notifications push personnalisées</a>
-    Cet exemple crée une opération push à l'endroit où un code remise personnalisé est entré dans la notification push.
 
![Personalize1][11]

La personnalisation consiste à remplacer un marqueur d'une balise d'informations de l'application. Vous devez donc d'abord vous assurer que les informations d'application appropriées sont définies pour l'utilisateur. Dans cet exemple, la balise d'informations de l'application rebate_code est définie pour les utilisateurs ciblés.
Comme vous pouvez le constater ci-dessus, le contenu de la notification push inclut le marqueur ${rebate_code}, qui indique qu'il doit être remplacé par le contenu réel de la balise d'informations de l'application.

> Avertissement : si la balise d'informations de l'application n'est pas définie pour l'utilisateur, ce dernier ne recevra pas la notification push.

-    Résultat
 
![Personalize2][12]

### Vous pouvez personnaliser le texte de votre notification
 
![Personalize3][13]

-    Notamment le titre de la notification
-    et le contenu du message.
-    Choisissez le type d'annonce (vue de texte ou vue web)
 
![Personalize4][14]

### Le corps d'une annonce peut également être personnalisé avec les éléments suivants :
-    l'URL de l'action, en cas de personnalisation de la page d'accueil ;
-    le titre ;
-    le corps du message.
 
 
## <a name="#Differentiate">Différencier votre notification push (dans ou en dehors de l'application)</a>

-    Choisissez le type de notification que vous allez envoyer, sélectionnez votre application, accédez à la section " Couverture ", sélectionnez ou créez une campagne push et accédez à la section " Notification ".
 
-    Cliquez sur le " mode de remise " souhaité.
-    Cochez la case " Restreindre les activités " si vous voulez que la notification se produise pour des activités spécifiques (écrans).

![Differentiate1][15]

### Mode de remise " Hors de l'application uniquement "
 
![Differentiate2][16]

Le mode de remise " Hors de l'application uniquement " envoie une notification push quand l'application est fermée. Il s'agit de la notification push standard.
Quand vous sélectionnez " Hors de l'application uniquement ", vous devez déjà avoir fourni les certificats de la plateforme sur laquelle est créée votre application (service APN ou GCM).

**Voir aussi :** 

-  [Apple Push Notification Service - Certificats](http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9), Google Cloud Messaging - Certificat](http://developer.android.com/google/gcm/index.html)

### Mode de remise " Dans l'application uniquement "
 
![Differentiate3][17]

Le mode de remise " Dans l'application uniquement " envoie la notification push pendant l'exécution de l'application.
Pour cette notification, il est inutile de passer par le service APN ou le système GCM.
Vous pouvez utiliser le système de remise dans l'application pour toucher vos utilisateurs finaux.
Vous pouvez entièrement personnaliser la notification et décider dans quelle activité (écran) la notification va apparaître.

### Mode de remise " À tout moment "
Vous pouvez choisir le mode de remise " À tout moment ", qui vous permet de toucher vos utilisateurs finaux, que l'application soit en cours d'exécution ou non.
Quand vous sélectionnez " À tout moment ", vous devez déjà avoir fourni les certificats de la plateforme sur laquelle est créée votre application (service APN ou GCM). 
 
## <a name="#Schedule">Planifier une campagne push</a>
 
### Planifiez le début de la campagne
 
![Shedule1][18]

Nous sommes le 21 mars et vous devez diffuser une annonce le 22 mars à minuit. 
Vous n'êtes pas obligé de rester devant l'interface pour envoyer une notification push ! Vous pouvez planifier à l'avance la minute exacte à laquelle les notifications seront envoyées.
-    Décochez la case " Aucune " et sélectionnez une heure de début 
-    Choisissez la date et l'heure auxquelles vous voulez démarrer la campagne push.
### Planifiez la fin de la campagne
 
![Shedule2][19]

Vous voulez que votre campagne s'arrête le 25 mars à 15 h 00, mais vous savez que vous ne serez pas là pour le faire.
Vous n'êtes pas obligé de rester devant l'interface pour envoyer une notification push ! Vous pouvez planifier à l'avance la minute exacte à laquelle votre campagne s'arrêtera.
-    Cochez la case " Aucune " ou sélectionnez une heure de fin
-    Choisissez la date et l'heure auxquelles vous voulez arrêter la campagne push.
### Arrêter manuellement une campagne
 
![Shedule3][20]

Par défaut, la case " Aucune " est cochée.
Cela signifie que la campagne démarrera ou s'arrêtera dès que l'activerez ou la désactiverez dans la section Couverture.
 
> Remarque : Les campagnes créées sans date de fin stockent la notification push localement sur l'appareil et l'affiche à la prochaine ouverture de l'application, même si la campagne est arrêtée manuellement.

## <a name="#TextView">Renforcer une notification push avec une vue de texte</a>

### Qu'est-ce que la vue de texte ?
 
![TextView1][21]

Une vue de texte est une fenêtre contextuelle contenant du texte. Cette fenêtre contextuelle apparaît une fois que l'utilisateur final a cliqué sur la notification push.
Une vue de texte vous permet de présenter plus de contenu à l'utilisateur final. C'est également une opportunité de présenter un appel à l'action, par exemple, l'accès à une page de votre application, la redirection vers une boutique d'applications, l'ouverture d'une page web, l'envoi d'un message électronique, le lancement d'une recherche géo-localisée, etc.

### Exemple : Vue de texte
-    Créez votre campagne de notification push dans la section " Couverture " et donnez un nom à votre campagne
 
![TextView2][22]

-    Écrivez le message qui s'affichera dans la notification.
-    Sélectionnez " Texte " comme type de contenu de l'annonce
 
![TextView3][23]

> Remarque : quand vous envoyez une vue de texte, elle est toujours précédée d'une notification. 

- Définissez le texte (une fois que vous avez sélectionné l'annonce textuelle, une sous-section apparaît dans laquelle vous pouvez définir le texte à afficher.)
 
![TextView4][24]

-    Écrivez le titre qui apparaîtra en haut du message.
-    Écrivez le contenu principal de la vue de texte.
-    Écrivez le contenu qui s'affichera sur le bouton d'action (un bouton d'action permet à l'application d'effectuer une action spécifique, comme l'ouverture d'une page de l'application, la redirection vers une boutique d'applications ou tout type de sources que vous pouvez fournir).
-    Écrivez le contenu qui s'affichera sur le bouton Quitter (en cliquant sur le bouton Quitter, la vue de texte disparaît.)
 
-    Créez votre campagne de notification push. Elle apparaîtra dans votre liste de campagnes.
 
![TextView5][25]

-    Activez votre campagne de notifications push pour envoyer la vue de texte à vos utilisateurs.
 
![TextView6][26]

-    Résultat
 
![TextView7][27]

-    L'utilisateur reçoit la notification et clique dessus.
-    La vue de texte apparaît sous forme de fenêtre contextuelle dans laquelle l'utilisateur peut interagir.

## <a name="#WebView">Renforcer une notification push avec une vue web</a>

### Qu'est-ce qu'une vue web ?
 
![WebView1][28]

Une vue web est une fenêtre contextuelle avec du contenu web. Cette fenêtre contextuelle apparaît une fois que l'utilisateur final a cliqué sur la notification push.
Une vue web favorise l'interaction avec l'utilisateur final.
C'est également une opportunité de présenter un appel à l'action, par exemple, la redirection vers une boutique d'applications, l'ouverture d'une page web, l'envoi d'un message électronique, le lancement d'une recherche géo-localisée, etc.

### Exemple : Vue web

-    Créez votre campagne de notification push dans la section " Couverture " et donnez un nom à votre campagne.
 
![WebView2][29]

-    Écrivez le message qui s'affichera dans la notification.
-    Sélectionnez " Web " comme type de contenu de l'annonce
 
![WebView3][30]

**À propos des types d'annonce :**

- Notification uniquement : il s'agit d'une simple notification standard. Ce qui signifie que si un utilisateur clique dessus, aucune vue supplémentaire ne s'affiche, et seule l'action associée se produit.
- Annonce textuelle : il s'agit d'une notification qui engage l'utilisateur à regarder une vue de texte.
- Annonce web : il s'agit d'une notification qui engage l'utilisateur à regarder une vue web.
Sélectionnez le contenu " Annonce web ".

> Remarque : quand vous envoyez une vue web, elle est toujours précédée d'une notification.

- Définissez le contenu web (une fois que vous avez sélectionné le contenu de l'annonce web, une sous-section apparaît dans laquelle vous pouvez définir le contenu de la vue web à afficher.)

 
![WebView4][31]

-    Écrivez le titre qui apparaîtra en haut du message (facultatif).
-    Écrivez ici votre code HTML.
-    Cliquez sur le bouton du mode d'édition de la source pour basculer l'affichage et découvrir à quoi ressemble votre annonce.
-    Écrivez le contenu qui s'affichera sur le bouton d'action (un bouton d'action permet à l'application d'effectuer une action spécifique, comme l'ouverture d'une page de l'application, la redirection vers une boutique d'applications ou tout type de sources que vous pouvez fournir).
-    Écrivez le contenu qui s'affichera sur le bouton Quitter (en cliquant sur le bouton Quitter, la vue web disparaît.)
 
-    Résultat
 
![WebView5][32]

-    L'utilisateur reçoit la notification et clique dessus.
-    La vue web apparaît sous forme de fenêtre contextuelle dans laquelle l'utilisateur peut interagir.

<!--Image references-->
[1]: ./media/mobile-engagement-how-tos/First1.png
[2]: ./media/mobile-engagement-how-tos/First2.png
[3]: ./media/mobile-engagement-how-tos/First3.png
[4]: ./media/mobile-engagement-how-tos/First4.png
[5]: ./media/mobile-engagement-how-tos/First5.png
[6]: ./media/mobile-engagement-how-tos/First6.png
[7]: ./media/mobile-engagement-how-tos/First7.png
[8]: ./media/mobile-engagement-how-tos/Test1.png
[9]: ./media/mobile-engagement-how-tos/Test2.png
[10]: ./media/mobile-engagement-how-tos/Test3.png
[11]: ./media/mobile-engagement-how-tos/Personalize1.png
[12]: ./media/mobile-engagement-how-tos/Personalize2.png
[13]: ./media/mobile-engagement-how-tos/Personalize3.png
[14]: ./media/mobile-engagement-how-tos/Personalize4.png
[15]: ./media/mobile-engagement-how-tos/Differentiate1.png
[16]: ./media/mobile-engagement-how-tos/Differentiate2.png
[17]: ./media/mobile-engagement-how-tos/Differentiate3.png
[18]: ./media/mobile-engagement-how-tos/Schedule1.png
[19]: ./media/mobile-engagement-how-tos/Schedule2.png
[20]: ./media/mobile-engagement-how-tos/Schedule3.png
[21]: ./media/mobile-engagement-how-tos/TextView1.png
[22]: ./media/mobile-engagement-how-tos/TextView2.png
[23]: ./media/mobile-engagement-how-tos/TextView3.png
[24]: ./media/mobile-engagement-how-tos/TextView4.png
[25]: ./media/mobile-engagement-how-tos/TextView5.png
[26]: ./media/mobile-engagement-how-tos/TextView6.png
[27]: ./media/mobile-engagement-how-tos/TextView7.png
[28]: ./media/mobile-engagement-how-tos/WebView1.png
[29]: ./media/mobile-engagement-how-tos/WebView2.png
[30]: ./media/mobile-engagement-how-tos/WebView3.png
[31]: ./media/mobile-engagement-how-tos/WebView4.png
[32]: ./media/mobile-engagement-how-tos/WebView5.png

<!--Link references-->
[Link 1]: ../mobile-engagement-user-interface/
[Link 2]: ../mobile-engagement-troubleshooting-guide/
[Link 3]: ../mobile-engagement-how-tos/
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: ../mobile-engagement-user-interface-navigation/
[Link 13]: ../mobile-engagement-user-interface-home/
[Link 14]: ../mobile-engagement-user-interface-my-account/
[Link 15]: ../mobile-engagement-user-interface-analytics/
[Link 16]: ../mobile-engagement-user-interface-monitor/
[Link 17]: ../mobile-engagement-user-interface-reach/
[Link 18]: ../mobile-engagement-user-interface-segments/
[Link 19]: ../mobile-engagement-user-interface-dashboard/
[Link 20]: ../mobile-engagement-user-interface-settings/
[Link 21]: ../mobile-engagement-troubleshooting-guide-analytics/
[Link 22]: ../mobile-engagement-troubleshooting-guide-apis/
[Link 23]: ../mobile-engagement-troubleshooting-guide-push-reach/
[Link 24]: ../mobile-engagement-troubleshooting-guide-service/
[Link 25]: ../mobile-engagement-troubleshooting-guide-sdk/
[Link 26]: ../mobile-engagement-troubleshooting-guide-sr-info/
[Link 27]: ../mobile-engagement-how-tos-first-push/
[Link 28]: ../mobile-engagement-how-tos-test-campaign/
[Link 29]: ../mobile-engagement-how-tos-personalize-push/
[Link 30]: ../mobile-engagement-how-tos-differentiate-push/
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign/
[Link 32]: ../mobile-engagement-how-tos-text-view/
[Link 33]: ../mobile-engagement-how-tos-web-view/

<!--HONumber=47-->
