<properties
	pageTitle="Azure Notification Hubs"
	description="Découvrez comment utiliser les notifications Push dans Azure. Exemples de code écrits en C# à l’aide de l’API .NET."
	authors="wesmc7777"
	manager="dwrede"
	editor=""
	services="notification-hubs"
	documentationCenter=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="multiple"
	ms.devlang="multiple"
	ms.topic="hero-article"
	ms.date="02/11/2016"
	ms.author="wesmc"/>


#Azure Notification Hubs

##Vue d'ensemble

Azure Notification Hubs fournit une infrastructure de notifications Push simple d’utilisation, multiplateforme et avec montée en charge, vous permettant d’envoyer des notifications Push mobiles à partir de n’importe quel serveur principal (dans le cloud ou en local) vers n’importe quelle plateforme mobile.

Avec Notification Hubs, vous pouvez aisément envoyer des notifications Push personnalisées et interplateformes, sans vous préoccuper des détails des différents systèmes de notifications de plateformes (PNS). Avec un seul appel d’API, vous pouvez cibler des utilisateurs individuels ou des segments de publics complets comprenant des millions d’utilisateurs, sur tous leurs appareils.

Vous pouvez utiliser Notification Hubs lors de scénarios d’entreprise et de clients. Par exemple :

- Envoyez des notifications de dernières nouvelles à des millions d’utilisateurs avec une latence faible (Notification Hubs alimente des applications Bing pré-installées sur tous les appareils Windows Phone et Windows).
- Envoyez des coupons basés sur la localisation aux segments d’utilisateurs.
- Envoyez des notifications d’événements à des utilisateurs ou des groupes pour des applications de sport/finance/jeux.
- Informez les utilisateurs d’événements d’entreprise tels que des nouveaux messages/courriers électroniques ou prospects.
- Envoyez les mots de passe à usage unique requis pour l’authentification multi-facteur.



##Présentation des notifications Push

Les smartphones et les tablettes peuvent « notifier » les utilisateurs lorsqu’un événement se produit. Ces notifications peuvent prendre différentes formes.

Dans les applications Windows Store et Windows Phone, la notification peut prendre la forme d’un _toast_ : une fenêtre non modale s’affiche avec un signal sonore, pour signaler une nouvelle notification. D’autres types de notification sont pris en charge, notamment _vignette_, _brute_ et _badge_. Pour plus d’informations sur les types de notifications pris en charge sur les appareils Windows, consultez la page [Vignettes, badges et notifications (applications Windows Runtime)](http://msdn.microsoft.com/library/windows/apps/hh779725.aspx).

Sur les appareils Apple iOS, de manière similaire, la notification Push notifie l’utilisateur en affichant une boîte de dialogue, demandant à l’utilisateur d’afficher ou de fermer la notification. Le fait de cliquer sur **View** ouvre l'application qui reçoit le message. Pour plus d’informations sur les notifications iOS, consultez la page [Notifications iOS](http://go.microsoft.com/fwlink/?LinkId=615245).

Les notifications Push permettent aux appareils mobiles d’afficher des informations actualisées tout en restant économe en énergie. Les notifications peuvent être envoyées par des systèmes principaux vers des appareils mobiles, même lorsque les applications correspondantes ne sont pas activées sur les appareils mobiles. Les notifications Push constituent un composant vital des applications consommateur où elles sont utilisées pour accroître l’engagement envers l’application ainsi que son utilisation. Les notifications sont également utiles aux entreprises, des informations à jour augmentant la réactivité des employés à des événements commerciaux.

Des exemples spécifiques de scénarios d'engagement mobile sont présentés ci-dessous :

1.  Mise à jour d'une vignette sur Windows 8 ou Windows Phone avec les informations financières actuelles.
2.  Alerte d'un utilisateur par un toast qu'un élément de travail lui a été attribué, dans une application d'entreprise basée sur un workflow.
3.  Affichage d'un badge avec le nombre de prospects actuels dans une application CRM (telle que Microsoft Dynamics CRM).

##Fonctionnement des notifications Push

Les notifications Push sont diffusées par l'intermédiaire d'infrastructures spécifiques à des plateformes appelées _Platform Notification Systems_ (PNS). Un PNS fournit des fonctions dépouillées (c'est-à-dire sans prise en charge de la diffusion, personnalisation) et sans interface commune. Par exemple, pour envoyer une notification vers une application Windows Store, un développeur doit contacter les Services de notifications Push Windows (WNS). Pour envoyer une notification vers un appareil iOS, le même développeur doit contacter le service de notifications Push Apple (APNS) et envoyer le message une seconde fois. Azure Notification Hubs fournit une interface commune, ainsi que d’autres fonctionnalités permettant de prendre en charge les notifications Push sur chaque plateforme.

Néanmoins, à un niveau supérieur, tous les systèmes de notification de plateforme suivent le même modèle :

1.  L'application cliente contacte le PNS pour récupérer son _handle_. Le type de handle dépend du système. Pour WNS, il s'agit d'un URI ou « canal de notification ». Pour APNS, il s'agit d'un jeton.
2.  L'application cliente stocke ce handle dans le _serveur principal_ de l'application pour une utilisation ultérieure. Pour WNS, le serveur principal est généralement un service cloud. Pour Apple, le système est appelé un _fournisseur_.
3.  Pour envoyer une notification Push, le serveur principal de l’application contacte le PNS à l’aide du handle afin de cibler une instance d’application cliente spécifique.
4.  Le PNS transmet la notification à l’appareil spécifié par le handle.

![][0]

##Défis des notifications Push

Bien que ces systèmes soient très puissants, il reste encore beaucoup de travail au développeur d’applications pour implémenter ne serait-ce que des scénarios de notification Push courants, tels que la diffusion ou l’envoi de notifications Push à un utilisateur.

Les notifications Push sont l'une des fonctionnalités les plus demandées dans les services cloud pour les applications mobiles. La raison en est que l'infrastructure requise pour les faire fonctionner est assez complexe et principalement sans lien avec la logique commerciale principale de l'application. Vous trouverez ci-dessous quelques défis de la création d’une infrastructure Push à la demande :

- **Dépendance de la plateforme** Afin d'envoyer des notifications à des appareils sur différentes plateformes, plusieurs interfaces doivent être encodées dans le serveur principal. Non seulement les détails de niveau inférieur sont différents mais la présentation de la notification (vignette, toast ou badge) dépend également de la plateforme. Ces différences peuvent mener à un code du serveur principal complexe et difficile à entretenir.

- **Mise à l’échelle** La mise à l'échelle de cette infrastructure comporte deux aspects :
	+ Selon les instructions de PNS, les jetons des appareils doivent être actualisés chaque fois que l’application est lancée. Cela entraîne un volume de trafic important (et des accès à la base de données consécutifs) juste pour maintenir les jetons à jour. Lorsque le nombre d'appareils s'accroît (probablement jusqu'à des millions), le coût de création et de maintenance de cette infrastructure n'est pas négligeable.

	+ La plupart des PNS ne prennent pas en charge la diffusion sur plusieurs appareils. Il s'en suit qu'une diffusion à des millions d'appareils résulte en millions d'appels aux PNS. La capacité de mise à l’échelle de ces requêtes n’est pas insignifiante car, généralement, les développeurs d’application souhaitent maintenir la latence basse (par exemple, le dernier appareil à recevoir le message ne doit pas recevoir la notification 30 minutes après son envoi, car, dans la plupart des cas, ce retard va à l’encontre du but recherché).
- **Routage** Les PNS permettent d'envoyer un message à un appareil. Néanmoins, dans la plupart des applications, les notifications sont ciblées sur des utilisateurs et/ou des groupes d'intérêt (par exemple, tous les employés attribués à un certain compte utilisateur). Ainsi, afin de router les notifications vers les appareils appropriés, le serveur principal de l'application doit conserver un registre qui associe les groupes d'intérêt aux jetons des appareils. Ce traitement s'ajoute à la durée totale de mise sur le marché et aux coûts de maintenance d'une application.

##Pourquoi utiliser Notification Hubs ?

Notification Hubs simplifie votre travail : vous n’avez plus besoin de gérer les problèmes relatifs aux notifications Push. Au lieu de cela, il vous suffit d’utiliser un hub de notification. Notification Hubs utilise une infrastructure de notifications Push complète, multi-plateforme, avec montée en charge et réduit considérablement le code Push qui s’exécute dans le serveur principal de l’application. Notification Hubs implémente toutes les fonctionnalités d’une infrastructure Push. La seule responsabilité des appareils est d’enregistrer le handle PNS et le serveur principal est responsable de l’envoi des messages indépendants d’une plateforme aux utilisateurs et groupes d’intérêt.

![][1]


Notification Hubs offre une infrastructure de notification Push prête à l’emploi avec les avantages suivants :

- **Multiplateforme**
	+  Prise en charge de toutes les principales plateformes mobiles. Notification Hubs permet d'envoyer des notifications Push aux applications Windows Store, iOS, Android et Windows Phone.

	+  Notification Hubs offre une interface commune permettant d'envoyer des notifications à tous les plateformes prises en charge. Il n’est plus nécessaire de suivre les protocoles propres à chaque plateforme. Le serveur principal de l'application peut envoyer des notifications dans des formats propres ou non à des plateformes. L’application communique uniquement avec Notification Hubs.

	+  Gestion du handle de l’appareil Notification Hubs gère le registre de handle et les commentaires PNS.

- **Fonctionne avec tous les serveurs principaux** : sur cloud ou sur site, .NET, PHP, Java, Node, etc.

- **Mise à l’échelle** Notification Hubs met à l'échelle des millions d'appareils sans la nécessité de recréer l'architecture ou de partitionner.


- **Ensemble complet de modèles de remise** :

	- *Diffusion* : permet une diffusion presque simultanée vers des millions d’appareils avec un seul appel d’API.

	- *Monodiffusion/multidiffusion* : transmission de notifications Push vers des balises représentant des utilisateurs individuels, y compris tous leurs appareils ; ou un groupe plus étendu, comme par exemple, des facteurs de format distincts (tablette et téléphone).

	- *Segmentation* : transmission de notifications Push vers un segment complexe défini par des expressions de balise (par exemple, des appareils situés à New York suivant les match des Yankees).

	Chaque appareil, lors de l’envoi de son handle à un hub de notification, peut indiquer une ou plusieurs _balises_. Pour plus d’informations sur les balises, consultez [cette page]. Il n'est pas nécessaire que les balises soient préapprovisionnées ou éliminées. Les balises fournissent un moyen simple d'envoyer des notifications à des utilisateurs ou des groupes d'intérêt. Les balises pouvant contenir n'importe quel identificateur propre à une application (par exemple des ID d'utilisateur ou de groupe), leur utilisation libère le serveur principal de l'application de la charge de stockage et de gestion des handle des appareils.

- **Personnalisation** : chaque appareil peut avoir un ou plusieurs modèles pour effectuer une localisation et une personnalisation en fonction des appareils sans affecter le code principal.

- **Sécurité** : secret d’accès partagé ou authentification fédérée.

- **Télémétrie complète** : disponible dans le portail et par programme.


##Intégration d’App Service Mobile Apps

En vue de permettre une expérience transparente et unifiée dans l’ensemble des services Azure, [App Service Mobile Apps] dispose d’une prise en charge intégrée des notifications Push à l’aide de Notification Hubs. [App Service Mobile Apps] offre une plateforme de développement d’applications mobiles hautement évolutive pour les développeurs d’entreprise et les intégrateurs système. Disponible pour tous, elle fournit un ensemble complet de fonctionnalités pour les développeurs d’applications mobiles.

Les développeurs Mobile Apps peuvent utiliser Notification Hubs avec le flux de travail suivant :

1. Récupération du handle PNS de l’appareil
2. Enregistrez l’appareil et les [modèles] avec Notification Hubs grâce à l’API d’enregistrement de kit de développement logiciel (SDK) client Mobile Apps adapté
    + Notez que Mobile Apps supprime tous les mots clés des inscriptions pour des raisons de sécurité. Utilisez Notification Hubs directement depuis votre serveur principal pour associer des mots clés aux appareils.
3. Envoyez des notifications de votre serveur principal d’application avec Notification Hubs

Voici certains des avantages qu’offre cette intégration aux développeurs :
- **SDK client Mobile Apps.** Ces Kits de développement logiciel (SDK) multi-plateformes fournissent des API simples pour l’inscription et la communication avec le hub de notification lié à l’application mobile de manière automatique. Les développeurs n’ont ni à rechercher les informations d’identification de Notification Hubs ni à utiliser de service supplémentaire.
    + Les kits de développement logiciel balisent automatiquement l’appareil avec l’identifiant utilisateur authentifié auprès de Mobile Apps pour activer le scénario de transmission de notifications Push à l’utilisateur.
    + Les kits de développement utilisent automatiquement l’ID d’installation de Mobile Apps en tant que GUID pour l’inscription auprès de Notification Hubs, ce qui évite aux développeurs d’avoir à gérer plusieurs GUID de service.
- **Modèle d’installation.** Mobile Apps fonctionne avec le modèle d’émission le plus récent de Notification Hubs pour représenter l’ensemble des propriétés d’émission associées à un appareil dans une installation JSON alignée avec les services de notifications Push et facile à utiliser.
- **Flexibilité.** Les développeurs peuvent toujours choisir d’utiliser Notification Hubs directement, même avec l’intégration effective.
- **Expérience intégrée dans le [portail Azure].** La fonctionnalité Notifications Push est représentée visuellement dans Mobile Apps. Les développeurs peuvent utiliser le hub de notification associé en toute simplicité via Mobile Apps.



##Étapes suivantes

Vous trouverez des informations supplémentaires sur Notification Hubs dans les rubriques suivantes :

+ **[Comment les clients utilisent Notification Hubs]**

+ **[Didacticiels et guides sur Notification Hubs]**

+ **Didacticiels de prise en main de Notification Hubs** ([iOS], [Android], [Windows Universel], [Windows Phone], [Kindle], [Xamarin.iOS], [Xamarin.Android])

Les références d’API managées .NET pertinentes pour les notifications Push se trouvent aux emplacements suivants :

+ [Microsoft.WindowsAzure.Messaging.NotificationHub]
+ [Microsoft.ServiceBus.Notifications]


  [0]: ./media/notification-hubs-overview/registration-diagram.png
  [1]: ./media/notification-hubs-overview/notification-hub-diagram.png
  [Comment les clients utilisent Notification Hubs]: http://azure.microsoft.com/services/notification-hubs
  [Didacticiels et guides sur Notification Hubs]: http://azure.microsoft.com/documentation/services/notification-hubs
  [iOS]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started
  [Android]: http://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started
  [Windows Universel]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started
  [Windows Phone]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started
  [Kindle]: http://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started
  [Xamarin.iOS]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
  [Xamarin.Android]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started
  [Microsoft.WindowsAzure.Messaging.NotificationHub]: http://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx
  [Microsoft.ServiceBus.Notifications]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx
  [App Service Mobile Apps]: https://azure.microsoft.com/fr-FR/documentation/articles/app-service-mobile-value-prop/
  [modèles]: notification-hubs-templates.md
  [portail Azure]: https://portal.azure.com
  [cette page]: (http://msdn.microsoft.com/library/azure/dn530749.aspx)

<!---HONumber=AcomDC_0309_2016-->