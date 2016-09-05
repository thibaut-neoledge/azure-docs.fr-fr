<properties 
	pageTitle="Azure Notification Hubs : instructions relatives au diagnostic" 
	description="Instructions sur la méthode de diagnostic des problèmes courants avec Azure Notification Hubs." 
	services="notification-hubs" 
	documentationCenter="Mobile" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="NA" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="08/19/2016" 
	ms.author="wesmc"/>

#Azure Notification Hubs : instructions relatives au diagnostic

##Vue d'ensemble

Une des questions les plus courantes de nos clients Azure Notification Hubs est de savoir comment déterminer pourquoi ils ne voient pas une notification envoyée à partir de leur serveur principal d’application sur le périphérique client : où et pourquoi les notifications ont-elles été supprimées et comment résoudre ce problème. Dans cet article nous allons examiner les différentes raisons pour lesquelles les notifications peuvent être perdues ou n’arrivent pas sur les périphériques. Nous allons également examiner des méthodes vous permettant d’analyser et de déterminer la cause principale du problème.

Tout d’abord, il est essentiel de comprendre comment Azure Notification Hubs envoie des notifications aux appareils.![][0]

Dans un flux de notification d’envoi par défaut, le message est envoyé à partir du **serveur principal d’application** à **Azure Notification Hub (NH)**, qui à son tour effectue un traitement sur toutes les inscriptions en prenant en compte les balises et les expressions de balises configurées pour déterminer les « cibles » : par exemple, tous les enregistrements qui ont besoin de recevoir les notifications Push. Ces inscriptions peuvent s’étendre sur tout ou partie de nos plateformes prises en charge : iOS, Google, Windows, Windows Phone, Kindle et Baidu pour Android en Chine. Une fois les cibles établies, NH transmet les notifications, réparties sur plusieurs lots d’inscriptions, au **service de notifications Push (PNS)** propre à la plateforme de l’appareil : par exemple, APNs pour Apple, GCM pour Google, etc. NH s’authentifie avec le PNS respectif, conformément aux informations d’identification que vous définissez dans le portail Azure Classic dans la page Configuration de Notification Hubs. Le PNS transmet alors les notifications aux **périphériques clients** respectifs. Il s’agit de la méthode recommandée pour la plateforme pour fournir des notifications Push. Notez que le dernier tronçon de remise des notifications s’effectue entre le PNS de la plateforme et le périphérique. Il y a donc quatre composants principaux (*client*, *serveur principal d’application*, *Azure Notification Hubs (NH)* et les *services de notifications Push [PNS]*), chacun d’eux pouvant être à l’origine de la perte de notifications. Plus de détails sur cette architecture sont disponibles sur la page [Vue d’ensemble de Notification Hubs].

L’échec de la remise de notifications peut se produire pendant la phase initiale de test/de mise en lots. Cela peut indiquer un problème de configuration. Il peut également se produire lors de la production, où l’ensemble ou une partie des notifications peut être égarée, ce qui indique un problème de modèle d’application ou de messagerie plus sérieux. Dans la section ci-dessous, nous allons examiner différents scénarios de notifications supprimées allant des plus courants aux plus rares : certains vous sembleront peut-être évidents mais d’autres moins.

##Mauvaise configuration d’Azure Notification Hubs 

Azure Notification Hubs a besoin de s’authentifier dans le contexte de l’application du développeur pour pouvoir envoyer avec succès des notifications aux PNS respectifs. Pour cela, le développeur doit créer un compte de développeur avec la plateforme correspondante (Google, Apple, Windows, etc.) et inscrire son application où il obtient des informations d’identification à configurer dans le portail sous la section de configuration de Notification Hubs. Si vous n’obtenez aucune notification, la première étape consiste à s’assurer que les informations d’identification adéquates sont configurées dans Notification Hub, en les faisant correspondre à l’application créée dans votre compte de développeur spécifique à la plate-forme. Nos [Didacticiels de prise en main] sont utiles pour traiter ce processus étape par étape. Voici certaines configurations erronées communes :

1. **Généralités**
 
	a) Assurez-vous que le nom de votre hub de notification (sans fautes de frappe) est le même :

	- à l’endroit où vous vous inscrivez sur le client
	- à l’endroit où vous envoyez des notifications depuis le serveur principal
	- à l’endroit où vous avez configuré les informations d’identification PNS
	- pour les informations d’identification SAS que vous avez configurées sur le client et le serveur principal.
		
	b) Assurez-vous que vous utilisez les chaînes de configuration SAS adéquates sur le client et le serveur principal d’application. En règle générale, vous devez utiliser **DefaultListenSharedAccessSignature** sur le client et **DefaultFullSharedAccessSignature** sur le serveur d’application principal (ce qui autorise l’envoi de notifications au NH)

2. **Configuration d’Apple Push Notification Service (APNS)**
 
	Vous devez disposer de deux hubs différents : un pour la production et un autre pour vos essais. Il convient donc de télécharger le certificat que vous utiliserez dans un environnement de bac à sable (sandbox) sur un hub et le certificat que vous utiliserez en production sur un hub distinct. N’essayez pas de télécharger différents types de certificats sur le même hub, car cela pourrait provoquer des défaillances de notification plus tard. Si vous avez téléchargé par inadvertance différents types de certificat sur le même hub, il est recommandé de supprimer le hub et de recommencer. Si vous ne pouvez pas supprimer le hub pour une raison quelconque, vous devez au moins supprimer tous les enregistrements existants du hub.

3. **Configuration de Google Cloud Messaging (GCM)**

	a) Assurez-vous que vous activez « Google Cloud Messaging pour Android » dans votre projet cloud.
	
	![][2]
	
	b) Veillez à créer une « clé serveur » lors de l’obtention des informations d’identification qu’utilisera NH pour s’authentifier avec GCM.
	
	![][3]
	
	c) Assurez-vous que vous avez configuré l’« ID de projet » sur le client. C’est une entité entièrement numérique que vous pouvez obtenir à partir du tableau de bord :
	
	![][1]

##Problèmes de l’application

1) **Balises/expressions de balises**

Si vous utilisez des balises ou des expressions de balises pour segmenter votre public, il est possible que lorsque vous envoyez la notification, aucune cible ne soit trouvée, selon les balises/expressions de balises que vous spécifiez dans votre appel d’envoi. Il est préférable de consulter vos enregistrements pour vous assurer qu’il existe des balises correspondantes lorsque vous envoyez une notification, puis de vérifier l’accusé de réception uniquement à partir des clients avec les enregistrements correspondants. Par exemple, si tous vos enregistrements avec NH ont été réalisés avec la balise « Politique » et que vous envoyez une notification avec la balise « Sports », elle ne sera envoyée à aucun périphérique. Un cas complexe peut impliquer des expressions de balises où vous n’êtes inscrit qu’avec « Balise A » OR « Balise B », mais les expressions de balises que vous ciblez lors de l’envoi de notifications sont « Balise A && Balise B ». Dans la section de conseils pour le diagnostic personnel ci-dessous, nous vous proposons des méthodes pour examiner vos inscriptions ainsi que leurs balises.

2) **Problèmes liés aux modèles**

Si vous utilisez des modèles, assurez-vous que vous suivez les instructions décrites dans nos [Conseils relatifs aux modèles].

3) **Inscriptions non valides**

En supposant que le hub de notification est correctement configuré et que les balises/expressions de balises ont été utilisées correctement afin de trouver des cibles valides auxquelles envoyer les notifications, NH déclenche plusieurs lots de traitement en parallèle, chaque lot envoyant des messages à un ensemble d’inscriptions.

> [AZURE.NOTE] Étant donné que le traitement est effectué en parallèle, l’ordre dans lequel les notifications sont remises n’est pas garanti.

Azure Notifications Hub est optimisé pour un modèle de remise de message « au plus une fois ». Cela signifie qu’il entreprend une déduplication, afin qu’aucune notification ne soit remise plusieurs fois à un même périphérique. Pour s’en assurer, il est nécessaire d’examiner les inscriptions et de vérifier qu’un seul message est envoyé par identificateur de périphérique, avant son envoi au PNS. Étant donné que chaque lot est envoyé au PNS, qui à son tour accepte et valide les inscriptions, il est possible que le PNS détecte une erreur sur une ou plusieurs inscriptions d’un lot, renvoie une erreur à Azure NH, arrête le traitement et supprime ainsi l’intégralité du lot. Cela est particulièrement vrai pour un APNS qui utilise un protocole de flux TCP. Bien que notre configuration soit optimisée pour la remise « au plus une fois », nous supprimons dans ce cas l’enregistrement défaillant de notre base de données, puis réessayons la remise des notifications pour le reste des appareils du lot.

Des messages d’erreur peuvent s’afficher lorsqu’une tentative de remise vis-à-vis d’une inscription utilisant les API REST d’Azure Notification Hubs échoue : [Per Message Telemetry: Get Notification Message Telemetry](https://msdn.microsoft.com/library/azure/mt608135.aspx) (Télémétrie par message : télémétrie d’obtention de messages de notification) et les [commentaires PNS](https://msdn.microsoft.com/library/azure/mt705560.aspx). Voir le répertoire [SendRESTExample](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/SendRestExample) pour un exemple de code.

##Problèmes de PNS

Lorsque le message de notification a été reçu par le PNS respectif, il est de sa responsabilité de remettre la notification au périphérique. Azure Notification Hubs n’intervient pas et n’a aucun contrôle sur le moment de livraison ou la livraison en elle-même à l’appareil. Étant donné que les services de notification de plateforme sont assez solides, les notifications ont tendance à atteindre les périphériques en quelques secondes à partir du PNS. Toutefois, si le PNS connaît un goulot d’étranglement, Azure Notification Hubs applique une stratégie d’interruption exponentielle : si le PNS reste inaccessible pendant 30 minutes, il existe une stratégie pour faire expirer et supprimer ces messages définitivement.

Si un PNS tente de remettre une notification, mais que le périphérique est en mode hors connexion, la notification est stockée par le PNS pendant une période limitée et remise au périphérique lorsqu’il est disponible. Seule une notification récente est stockée pour une application donnée. Si plusieurs notifications sont envoyées lorsque le périphérique est hors connexion, chaque nouvelle notification provoque la suppression de la notification préalable. Ce comportement consistant à ne conserver que la dernière notification est appelé fusion des notifications dans APN et réduction dans GCM (qui utilise une clé de réduction). Si le périphérique reste hors connexion pendant une longue période, les notifications qui ont été stockées sont ignorées. Source : [conseils APNS] et [conseils GCM]

Avec Azure Notification Hubs : vous pouvez transmettre une clé de fusion avec un en-tête HTTP avec `SendNotification` l'API générique (par exemple, pour le kit de développement logiciel .NET –`SendNotificationAsync`), qui transmet également les en-têtes HTTP tels quels au PNS respectif.

##Conseils pour le diagnostic personnel

Nous examinerons ici les différents moyens pour diagnostiquer et trouver les causes des problèmes de Notification Hub :

###Vérification des informations d’identification

1. **Portail des développeurs PNS**

	Vérifiez-les dans le portail des développeurs PNS respectif (APNS, GCM, WNS, etc.) à l’aide de nos [Didacticiels de prise en main].

2. **Portail Azure Classic**

	Accédez à l’onglet Configurer pour examiner et faire correspondre les informations d’identification avec celles obtenues sur le portail des développeurs PNS.

	![][4]

###Vérification des inscriptions

1. **Visual Studio**

	Si vous utilisez Visual Studio à des fins de développement, vous pouvez vous connecter à Microsoft Azure et afficher et gérer un ensemble de services Azure, y compris Notifications Hub depuis l’« Explorateur de serveurs ». Cela est particulièrement utile pour votre environnement de développement et de test.

	![][9]

	Vous pouvez afficher et gérer toutes les inscriptions dans votre hub, où elles sont classées par plate-forme, inscription native ou par modèle, balise, identificateur PNS, ID d’enregistrement et date d’expiration. Vous pouvez également modifier un enregistrement à la volée, ce qui s’avère utile si vous souhaitez modifier toutes les balises par exemple.

	![][8]
 
	> [AZURE.NOTE] Les fonctionnalités de Visual Studio pour modifier les enregistrements doivent uniquement servir au cours du développement/test, avec un nombre limité d’enregistrements. Si vous avez besoin de corriger vos enregistrements en bloc, envisagez d'utiliser la fonctionnalité d'exportation/importation d'enregistrements ici : [Exportation/importation d'enregistrements](https://msdn.microsoft.com/library/dn790624.aspx)

2. **Explorateur Service Bus**

	De nombreux clients utilisent l’explorateur ServiceBus, décrit ici : [Explorateur ServiceBus], pour afficher et gérer leur hub de notification. C’est un projet open source disponible sur code.microsoft.com : [Code de l’explorateur ServiceBus]

###Vérification des notifications de messages

1. **Portail Azure Classic**

	Vous pouvez accéder à l’onglet « Debug » pour envoyer des notifications de test à vos clients sans avoir besoin d’un serveur principal de service en cours d’exécution.

	![][7]

2. **Visual Studio**

	Vous pouvez également envoyer des notifications de test à partir de Visual Studio :

	![][10]

	Vous en saurez davantage sur les fonctionnalités de l’Explorateur Azure Visual Studio Notification Hub ici :
	
	- [Vue d’ensemble de l’Explorateur de serveurs Visual Studio]
	- [Billet de blog concernant l’Explorateur de serveurs de Visual Studio : 1]
	- [Billet de blog concernant l’Explorateur de serveurs de Visual Studio : 2]

###Débogage de notifications ayant échoué/analyse des résultats de notification

**Propriété EnableTestSend**

Lorsque vous envoyez une notification via Notification Hubs, elle est d’abord placée en file d’attente, pour être traitée par NH afin de déterminer toutes ses cibles. NH l’envoie ensuite au PNS. Lorsque vous utilisez l’API REST ou un Kit de développement logiciel (SDK) client, le retour réussi de votre appel d’envoi signifie donc uniquement que le message a été correctement placé dans la file d’attente de Notification Hub. Il ne permet pas de voir ce qui s’est passé lorsque NH a finalement envoyé le message à PNS. Si votre notification n’est pas arrivée sur le périphérique client, il est possible que lorsque NH a tenté de remettre le message au PNS, une erreur s’est produite, par exemple, la taille de la charge utile a dépassé le maximum autorisé par le PNS ou les informations d’identification configurées dans NH n’étaient pas valides, etc. Pour obtenir un aperçu des erreurs PNS, nous avons introduit une propriété appelée [fonctionnalité EnableTestSend]. Cette propriété est activée automatiquement lorsque vous envoyez des messages de test à partir du portail ou du client Visual Studio et vous permet de voir les informations de débogage détaillées. Vous pouvez utiliser cette fonction via les API, en prenant l’exemple du Kit de développement logiciel (SDK) .NET, dans lequel elle est disponible dès maintenant et sera ajoutée par la suite à tous les Kits de développement logiciel (SDK) clients. Pour utiliser cette option avec l’appel REST, ajoutez simplement un paramètre querystring appelé « test » à la fin de votre appel d’envoi, par exemple

	https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test

*Exemple (Kit de développement logiciel (SDK) .NET)*
 
Supposons que vous utilisiez le Kit de développement logiciel (SDK) .NET pour envoyer une notification toast native :

    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
    var result = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(result.State);
 
`result.State` affichera simplement `Enqueued` à la fin de l’exécution, sans information sur ce qui est arrivé à votre notification Push. Vous pouvez désormais utiliser la propriété booléenne `EnableTestSend` lors de l’initialisation de `NotificationHubClient` et obtenir l’état détaillé des erreurs PNS rencontrées lors de l’envoi de la notification. L’appel d’envoi prendra plus de temps, car il retourne uniquement après que NH a envoyé la notification au PNS pour en déterminer le résultat.
 
	bool enableTestSend = true;
	NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);
	
	var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
	Console.WriteLine(outcome.State);
	
	foreach (RegistrationResult result in outcome.Results)
	{
	    Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
	}

*Exemple de sortie*

    DetailedStateAvailable
    windows
    7619785862101227384-7840974832647865618-3
    The Token obtained from the Token Provider is wrong
 
Ce message indique que des informations d’identification non valides sont configurées dans le hub de notification ou qu’il existe un problème avec les inscriptions sur le hub. La marche à suivre recommandée consiste à supprimer cet enregistrement afin de permettre au client de le recréer avant d’envoyer le message.
 
> [AZURE.NOTE] Notez que l’utilisation de cette propriété est très limitée et par conséquent, vous devez uniquement l’utiliser dans un environnement de développement et de test avec un ensemble limité d’enregistrements. Nous envoyons uniquement des notifications de débogage à 10 périphériques. Nous avons également une limite de traitement des envois de débogage de 10 par minute.

###Révision de la télémétrie 

1. **Utiliser le portail Azure Classic**

	Le portail vous permet d’obtenir un aperçu rapide de toutes les activités sur votre hub de notification.
	
	a) Sous l’onglet « tableau de bord », vous pouvez afficher une vue agrégée des enregistrements, des notifications et des erreurs par plateforme.
	
	![][5]
	
	b) Vous pouvez également ajouter plusieurs autres mesures spécifiques à la plate-forme à partir de l’onglet « Analyse » pour étudier plus précisément toute erreur spécifique au PNS retournée lorsque NH tente de lui envoyer la notification.
	
	![][6]
	
	c) Vous devez commencer par examiner les **messages entrants**, les **opérations d’enregistrement** et les **notifications réussies**, puis accéder à l’onglet par plate-forme pour examiner les erreurs spécifiques au PNS.
	
	Si vous avez mal configuré les paramètres d’authentification du hub de notification, vous verrez une erreur d’authentification PNS. Il s’agit d’une bonne indication vous poussant à vérifier les informations d’identification PNS.

2) **Accès par programme**

Plus de détails ici :

- [Accès par programme à la télémétrie]
- [Exemple d’accès à la télémétrie via les API]

> [AZURE.NOTE] Plusieurs fonctionnalités liées à la télémétrie, comme l’**Exportation/importation des enregistrements**, l’**accès à la télémétrie via les API**, etc., sont uniquement disponibles en niveau Standard. Si vous essayez d’utiliser ces fonctionnalités et que vous disposez d’un niveau Libre ou De base, vous obtenez un message d’exception lors de l’utilisation du Kit de développement logiciel (SDK) et une erreur HTTP 403 (interdit) lorsque vous les utilisez directement à partir des API REST. Assurez-vous que vous n’êtes pas passé au niveau Standard par le biais du portail Azure Classic.

<!-- IMAGES -->
[0]: ./media/notification-hubs-diagnosing/Architecture.png
[1]: ./media/notification-hubs-diagnosing/GCMConfigure.png
[2]: ./media/notification-hubs-diagnosing/GCMEnable.png
[3]: ./media/notification-hubs-diagnosing/GCMServerKey.png
[4]: ./media/notification-hubs-diagnosing/PortalConfigure.png
[5]: ./media/notification-hubs-diagnosing/PortalDashboard.png
[6]: ./media/notification-hubs-diagnosing/PortalMonitoring.png
[7]: ./media/notification-hubs-diagnosing/PortalTestNotification.png
[8]: ./media/notification-hubs-diagnosing/VSRegistrations.png
[9]: ./media/notification-hubs-diagnosing/VSServerExplorer.png
[10]: ./media/notification-hubs-diagnosing/VSTestNotification.png
 
<!-- LINKS -->
[Vue d’ensemble de Notification Hubs]: notification-hubs-overview.md
[Didacticiels de prise en main]: notification-hubs-windows-store-dotnet-get-started.md
[Conseils relatifs aux modèles]: https://msdn.microsoft.com/library/dn530748.aspx
[conseils APNS]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW4
[conseils GCM]: http://developer.android.com/google/gcm/adv.html
[Export/Import Registrations]: http://msdn.microsoft.com/library/dn790624.aspx
[Explorateur ServiceBus]: http://msdn.microsoft.com/library/dn530751.aspx
[Code de l’explorateur ServiceBus]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[Vue d’ensemble de l’Explorateur de serveurs Visual Studio]: http://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx
[Billet de blog concernant l’Explorateur de serveurs de Visual Studio : 1]: http://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs
[Billet de blog concernant l’Explorateur de serveurs de Visual Studio : 2]: http://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/
[fonctionnalité EnableTestSend]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.notificationhubclient.enabletestsend.aspx
[Accès par programme à la télémétrie]: http://msdn.microsoft.com/library/azure/dn458823.aspx
[Exemple d’accès à la télémétrie via les API]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel

 

<!---HONumber=AcomDC_0824_2016-->