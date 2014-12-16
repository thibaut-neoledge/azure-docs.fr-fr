<properties urlDisplayName="Azure Notification Hubs" pageTitle="Azure Notification Hubs" metaKeywords="notifications Push Azure, concentrateurs de notification Azure, messagerie Azure" description="Learn how to use push notifications in Azure. Code samples written in C# using the .NET API." metaCanonical="" disqusComments="1" umbracoNaviHide="0" title="Azure Notification Hubs" authors="sethm" manager="dwrede" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="09/24/2014" ms.author="sethm" />


#Azure Notification Hubs


La prise en charge des notifications Push dans Azure vous permet d'accéder à une infrastructure Push conviviale, multi-plateforme et avec montée en charge qui simplifie fortement l'implémentation des notifications Push pour les applications consommateur et entreprise pour les plateformes mobiles.

<h2>Présentation des notifications Push</h2>

Les smartphones et les tablettes peuvent " notifier " les utilisateurs lorsqu'un événement se produit. Dans les applications Windows Store, la notification peut résulter en un _toast_ : une fenêtre non modale s'affiche, avec un son, pour signaler une nouvelle notification Push. Sur les appareils Apple iOS, de manière similaire, la notification Push affiche une boîte de dialogue, demandant à l'utilisateur d'afficher ou de fermer la notification. Le fait de cliquer sur **View** ouvre l'application qui reçoit le message.

Les notifications Push permettent aux appareils mobiles d'afficher des informations actualisées tout en restant économe en énergie. Les notifications Push constituent un composant vital des applications consommateur où elles sont utilisées pour accroître l'engagement envers l'application ainsi que son utilisation. Les notifications sont également utiles aux entreprises, des informations à jour augmentant la réactivité des employés à des événements commerciaux. 

Des exemples spécifiques de scénarios d'engagement mobile sont présentés ci-dessous :

1.  Mise à jour d'une vignette sur Windows 8 ou Windows Phone avec les informations financières actuelles.
2.  Alerte d'un utilisateur par un toast qu'un élément de travail lui a été attribué, dans une application d'entreprise basée sur un workflow.
3.  Affichage d'un badge avec le nombre de prospects actuels dans une application CRM (telle que Microsoft Dynamics CRM).

<h2>Fonctionnement des notifications Push</h2>

Les notifications Push sont diffusées par l'intermédiaire d'infrastructures spécifiques à des plateformes appelées _Platform Notification Systems_ (PNS). Un PNS fournit des fonctions dépouillées (c'est-à-dire sans prise en charge de la diffusion, personnalisation) et sans interface commune. Par exemple, pour envoyer une notification vers une application Windows Store, un développeur doit contacter les Services de notifications Push Windows (WNS). Pour envoyer une notification vers un appareil iOS, le même développeur doit contacter le service de notifications Push Apple (APNS) et envoyer le message une seconde fois.

Néanmoins, à un niveau supérieur, tous les systèmes de notification de plateforme suivent le même modèle :

.1.  L'application cliente contacte le PNS pour récupérer son _handle_. Le type de handle dépend du système. Pour WNS, il s'agit d'un URI ou " canal de notification ". Pour APNS, il s'agit d'un jeton.
2.  L'application cliente stocke ce handle dans le _back-end_ de l'application en vue d'une utilisation ultérieure. Pour WNS, le serveur principal est généralement un service cloud. Dans Apple, le système est appelé _provider_.
3.  Pour envoyer une notification Push, le serveur principal de l'application contacte le PNS à l'aide du handle afin de cibler une instance d'application cliente spécifique.
4.  Le PNS transmet la notification à l'appareil spécifié par le handle.

![][0]

<h2>Défis des notifications Push</h2>

Bien que ces systèmes soient très puissants, il reste encore beaucoup de travail au développeur d'applications pour implémenter ne serait-ce que des scénarios de notification Push courants, tels que la diffusion ou l'envoi de notifications Push à un utilisateur.

Les notifications Push sont l'une des fonctionnalités les plus demandées dans les services cloud pour les applications mobiles. La raison en est que l'infrastructure requise pour les faire fonctionner est assez complexe et principalement sans lien avec la logique commerciale principale de l'application. Vous trouverez ci-dessous quelques défis de la création d'une infrastructure Push à la demande :

- **Dépendance de la plateforme.** Afin d'envoyer des notifications à des appareils sur différentes plateformes, plusieurs interfaces doivent être encodées dans le serveur principal. Non seulement les détails de niveau inférieur sont différents mais la présentation de la notification (vignette, toast ou badge) dépend également de la plateforme. Ces différences peuvent mener à un code du serveur principal complexe et difficile à entretenir.

- **Mise à l'échelle.** La mise à l'échelle de cette infrastructure comporte deux aspects :
1. Selon les instructions de PNS, les jetons des appareils doivent être actualisés chaque fois que l'application est lancée. Cela entraîne un volume de trafic important (et des accès à la base de données consécutifs) juste pour maintenir les jetons à jour. Lorsque le nombre d'appareils s'accroît (probablement jusqu'à des millions), le coût de création et de maintenance de cette infrastructure n'est pas négligeable.
2.  La plupart des PNS ne prennent pas en charge la diffusion sur plusieurs appareils. Il s'en suit qu'une diffusion à des millions d'appareils résulte en millions d'appels aux PNS. La capacité de mise à l'échelle de ces requêtes n'est pas insignifiante car, généralement, les développeurs d'application souhaitent maintenir la latence basse (par exemple, le dernier appareil à recevoir le message ne doit pas recevoir la notification 30 minutes après son envoi, car, dans la plupart des cas, ce retard va à l'encontre du but recherché).
- **Routage.** Les PNS permettent d'envoyer un message à un appareil. Néanmoins, dans la plupart des applications, les notifications sont ciblées sur des utilisateurs et/ou des groupes d'intérêt (par exemple, tous les employés attribués à un certain compte utilisateur). Ainsi, afin de router les notifications vers les appareils appropriés, le serveur principal de l'application doit conserver un registre qui associe les groupes d'intérêt aux jetons des appareils. Ce traitement s'ajoute à la durée totale de mise sur le marché et aux coûts de maintenance d'une application.

<h2>Pourquoi utiliser Notification Hubs ?</h2>

Notification Hubs offre une infrastructure de notification Push prête à l'emploi qui prend en charge les éléments suivants :

- **Plusieurs plateformes.** Notification Hubs offre une interface commune permettant d'envoyer des notifications à tous les plateformes prises en charge. Le serveur principal de l'application peut envoyer des notifications dans des formats propres ou non à des plateformes. Notification Hubs permet d'envoyer des notifications Push aux applications Windows Store, iOS, Android et Windows Phone.
- **Routage Pub/Sub.** Chaque appareil, lors de l'envoi de son handle à un concentrateur de notification, peut indiquer un ou plusieurs _tags_ (balises). Pour plus d'informations sur les balises, consultez la section suivante. Il n'est pas nécessaire que les balises soient préapprovisionnées ou éliminées. Les balises fournissent un moyen simple d'envoyer des notifications à des utilisateurs ou des groupes d'intérêt. Les balises pouvant contenir n'importe quel identificateur propre à une application (par exemple des ID d'utilisateur ou de groupe), leur utilisation libère le serveur principal de l'application de la charge de stockage et de gestion des handle des appareils.
- **Mise à l'échelle.** Notification Hubs met à l'échelle des millions d'appareils sans la nécessité de recréer l'architecture ou de partitionner.

Notification Hubs utilise une infrastructure de notifications Push complète, multi-plateforme, avec montée en charge et réduit considérablement le code Push qui s'exécute dans le serveur principal de l'application. Notification Hubs implémente toutes les fonctionnalités d'une infrastructure Push. La seule responsabilité des appareils est d'enregistrer leur handle PNS et le serveur principal est responsable de l'envoi des messages indépendants d'une plateforme aux utilisateurs et groupes d'intérêt.

![][1]

## Étapes suivantes

Vous trouverez des informations supplémentaires sur Notification Hubs dans les rubriques suivantes :

+ **[Comment les clients utilisent Notification Hubs]**

+ **[Didacticiels et guides sur Notification Hubs]** 

+ **Didacticiels de prise en main de Notification Hubs** ([iOS], [Android], [Windows Universal], [Windows Phone], [Kindle], [Xamarin.iOS], [Xamarin.Android])

Les références d'API managées .NET pertinentes pour les notifications Push se trouvent aux emplacements suivants :

+ [Microsoft.WindowsAzure.Messaging.NotificationHub]
+ [Microsoft.ServiceBus.Notifications] 


  [0]: ./media/notification-hubs-overview/SBPushNotifications1.gif
  [1]: ./media/notification-hubs-overview/SBPushNotifications2.gif
  [Comment les clients utilisent Notification Hubs]: http://azure.microsoft.com/fr-fr/services/notification-hubs
  [Didacticiels et guides sur Notification Hubs]: http://azure.microsoft.com/fr-fr/documentation/services/notification-hubs
  [iOS]: http://azure.microsoft.com/fr-fr/documentation/articles/notification-hubs-ios-get-started
  [Android]: http://azure.microsoft.com/fr-fr/documentation/articles/notification-hubs-android-get-started
  [Windows Universal]: http://azure.microsoft.com/fr-fr/documentation/articles/notification-hubs-windows-store-dotnet-get-started
  [Windows Phone]: http://azure.microsoft.com/fr-fr/documentation/articles/notification-hubs-windows-phone-get-started
  [Kindle]: http://azure.microsoft.com/fr-fr/documentation/articles/notification-hubs-kindle-get-started
  [Xamarin.iOS]: http://azure.microsoft.com/fr-fr/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
  [Xamarin.Android]: http://azure.microsoft.com/fr-fr/documentation/articles/partner-xamarin-notification-hubs-android-get-started
  [Microsoft.WindowsAzure.Messaging.NotificationHub]: http://msdn.microsoft.com/fr-fr/library/microsoft.windowsazure.messaging.notificationhub.aspx
  [Microsoft.ServiceBus.Notifications]: http://msdn.microsoft.com/fr-fr/library/microsoft.servicebus.notifications.aspx
  
