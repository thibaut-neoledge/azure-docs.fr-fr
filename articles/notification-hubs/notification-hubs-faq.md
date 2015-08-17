<properties 
	pageTitle="Azure Notification Hubs - Forum aux Questions (FAQ)" 
	description="FAQ sur la conception et l'implémentation de solutions sur Notification Hubs" 
	services="notification-hubs" 
	documentationCenter="mobile" 
	authors="wesmc" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/24/2015" 
	ms.author="wesmc" />

#Azure Notification Hubs - Forum aux Questions (FAQ)

##Généralités
###1\. Quelle est la tarification appliquée pour le service Notification Hubs ?
Notification Hubs est proposé dans ces trois niveaux de service : *Gratuit*, *De base* et *Standard*. Pour plus d'informations, consultez [Tarification de Notification Hubs]. La tarification s'effectue dans le cadre d'un abonnement et sur la base du nombre de notifications Push, indépendamment du nombre d'espaces de noms ou de hubs de notification que vous utilisez. Le niveau Gratuit est disponible pour les environnements de développement et n'est pas couvert pas un contrat SLA. Les niveaux De base et Standard sont proposés pour les environnements de production, mais les fonctionnalités clés suivantes sont disponibles uniquement avec le niveau Standard :

- *Diffusion* - Avec le niveau De base, le nombre de balises Notification Hubs est limité à 3 Ko (pour plus de cinq appareils). Si votre audience dépasse 3 Ko, vous devez passer au niveau Standard. 
- *Télémétrie élaborée* - Avec le niveau De base, vous ne pouvez pas exporter vos données de télémétrie ni vos inscriptions. Si vous avez besoin d'exporter vos données de télémétrie pour les afficher ou les analyser en mode hors connexion, vous devez passer au niveau Standard. 
- *Architecture mutualisée* - Si vous utilisez Notification Hubs pour créer une application mobile capable de prendre en charge plusieurs locataires, vous avez tout intérêt à passer au niveau Standard. Vous pourrez alors définir les informations d'identification des services de notifications Push (PNS) au niveau de l'espace de noms du hub de notification pour l'application, puis isoler les locataires en créant pour chacun d'eux un hub distinct sous cet espace de noms commun. Cette architecture offre l'avantage de simplifier la maintenance, tout en permettant de continuer à utiliser des clés SAS pour envoyer et recevoir les notifications des hubs de notification isolés pour chaque locataire, ce qui empêche tout chevauchement entre locataires. 

###2\. Quel est le contrat de niveau de service (SLA) ?
Pour les niveaux De base et Standard du service Notification Hubs, nous garantissons qu'au moins 99,9 % du temps, les applications correctement configurées pourront envoyer des notifications ou exécuter des opérations de gestion d'inscriptions en ce qui concerne un hub de notification déployé dans le cadre d'un niveau de service Notification Hubs De base ou Standard. Pour plus d'informations sur notre contrat SLA, consultez la page [Contrat SLA pour Notification Hubs]. Notez que le contrat SLA ne couvre pas la portion entre le service de notification de la plateforme et l'appareil, car la remise finale des notifications de Notification Hubs au matériel est assurée par les fournisseurs de plateforme externe.
 
###3\. Quels sont vos clients du service Notification Hubs ?
Parmi nos clients actuels de Notification Hubs, citons en particulier les suivants :

* Sochi 2014 : des centaines de groupes d'intérêt, plus de trois millions d'appareils, plus de 150 millions de notifications transmises en deux semaines. [Étude de cas Sochi]
* Skanska : [Étude de cas Skanska]
* Seattle Times : [Étude de cas Seattle Times]
* Mural.ly : [Étude de cas Mural.ly]
* 7Digital : [Étude de cas 7Digital]
* Applications Bing : des dizaines de millions d'appareils, trois millions de notifications envoyées par jour
 
##Conception et développement
###1\. Quelles plateformes côté service prenez-vous en charge ?
Nous fournissons des Kits de développement logiciel (SDK) et des exemples pour les plateformes .NET, Java, PHP, Python et Node.js. Vous pouvez donc configurer votre serveur principal d'applications pour communiquer avec Notification Hubs à partir de toutes ces plateformes. Les API de Notification Hubs sont basées sur l'interface REST pour permettre une communication directe avec elles. Pour plus d'informations, consultez [API REST de Notification Hubs].
 
###2\. Quelles plateformes matérielles prenez-vous en charge ?
L'envoi de notifications est possible vers toutes les plateformes suivantes : Apple iOS, Android, Windows Universal et Windows Phone, Kindle, Android China (via Baidu), Xamarin (iOS et Android) et Applications Chrome. Pour obtenir des didacticiels de prise en main pour ces plateformes, consultez [Didacticiels de prise en main de Notification Hubs].
 
###3\. Prenez-vous en charge les notifications web, par courrier électronique et par SMS ?
Notification Hubs a été principalement conçu pour permettre l'envoi de notifications aux applications mobiles basées sur les plateformes citées plus haut. Nous n'avons pas prévu de fonctionnalités d'envoi par courrier électronique ou par SMS. Toutefois, les plateformes de fournisseurs tiers qui proposent ces fonctionnalités peuvent être intégrées avec Notification Hubs pour l'envoi des notifications Push natives à l'aide d'Azure Mobile Services. Par exemple, le didacticiel consacré à l'envoi de SMS avec Azure Mobile Services explique comment [envoyer des notifications par SMS] à l'aide d'Azure Mobile Services. Nous ne fournissons pas non plus de fonctionnalité de notification Push intégrée au navigateur et prête à l’emploi. Les clients peuvent choisir d'implémenter cette fonctionnalité en utilisant SignalR. Nous proposons également un didacticiel qui explique comment envoyer des notifications Push aux applications Chrome basées sur le navigateur Google Chrome. Consultez le [didacticiel sur les applications Chrome].
 
###4\. Quel est le lien entre Azure Mobile Services et Azure Notification Hubs, et dans quels cas dois-je les utiliser ? 
Si vous disposez d'un serveur principal d'applications mobiles et que vous souhaitez seulement ajouter la fonctionnalité d'envoi de notifications Push, utilisez Azure Notification Hubs. Si vous souhaitez installer un nouveau serveur principal d'applications mobiles, utilisez Azure Mobile Services. Azure Mobile Services approvisionne automatiquement un hub de notification qui permet d'envoyer facilement des notifications Push à partir du serveur principal d'applications mobiles. La tarification appliquée pour Azure Mobile Services inclut les frais de base inhérents à un hub de notification. Vous ne payez que si vous dépassez le nombre de notifications Push prévu dans l'abonnement. Pour plus d'informations, consultez [Tarification de Mobile Services].
 
###5\. Combien d'appareils prenez-vous en charge ?
Pour les niveaux De base et Standard, nous ne limitons pas le nombre d'appareils actifs autorisés à recevoir des notifications. Pour plus d'informations, consultez [Tarification de Notification Hubs].
 
###6\. Combien de notifications Push puis-je envoyer ? 
Avec Azure Notification Hubs, nos clients envoient des millions de notifications Push chaque jour. Vous n'avez pas besoin de mettre à l'échelle votre système Notifications Hubs. Nous le faisons automatiquement en fonction du nombre de notifications transitant par votre système. Notez que la tarification est basée sur le nombre de notifications Push qui sont diffusées.
 
###7\. Combien de temps faut-il pour recevoir les notifications sur mon appareil ?
Azure Notification Hubs est en mesure de traiter au moins un million d'envois par minute dans un scénario d'utilisation normale, c'est-à-dire lorsque la charge entrante est relativement homogène et régulière. Ce taux peut varier en fonction du nombre de balises, de la nature des envois entrants etc. Dans ce laps de temps, nous déterminons les cibles par plateforme et nous transmettons les messages aux services de notifications Push correspondants sur la base des balises/expressions de balise inscrites. Ensuite, ce sont les services de notifications Push (PNS) qui sont responsables de l'envoi des notifications vers l'appareil. Les services PNS ne sont pas couverts par un contrat SLA garantissant la remise des notifications. Toutefois, la plupart des messages sont remis aux appareils en moins de 10 minutes après leur envoi sur notre plateforme. Dans quelques cas particuliers, le délai de remise peut être plus long. Azure Notification Hubs applique également une stratégie visant à supprimer toutes les notifications qui ne peuvent pas être remises au PNS dans un délai de 30 minutes. Cela peut se produire pour plusieurs raisons, la plus courante étant une limitation imposée par le PNS sur votre application.
 
###8\. Y a-t-il une garantie de latence ?
En raison de la nature même des notifications Push, qui sont remises par un service de notifications Push spécifique à une plateforme externe, nous n'apportons aucune garantie de latence. En règle générale, la majorité des notifications sont remises dans un délai de quelques minutes.
 
###9\. Quels sont les points à prendre en compte lors de la conception d'une solution avec des espaces de noms et des hubs de notification ? 
*Application mobile/Environnement :*vous devez prévoir un hub de notification par application mobile et par environnement. Dans un scénario d'architecture mutualisée, chaque locataire doit disposer d'un hub distinct. Vous ne devez jamais utiliser le même hub de notification pour les environnements de test et de production, au risque de rencontrer plus tard des problèmes lors de l'envoi des notifications. Par exemple, Apple fournit des points de terminaison Push pour les environnements de bac à sable (sandbox) et de production, chaque environnement étant défini avec des informations d'identification distinctes. Si un hub initialement configuré avec le certificat de bac à sable (sandbox) d'Apple était ensuite reconfiguré pour utiliser le certificat de production d'Apple, les anciens jetons de l'appareil ne seraient plus valides avec le nouveau certificat et provoqueraient l'échec des notifications Push. C'est pour cela qu'il est préférable de séparer vos environnements de test et de production, et d'utiliser des hubs différents pour chaque environnement.

*Identifiants PNS :* quand une application mobile est inscrite sur le portail des développeurs d'une plateforme (par exemple, Apple ou Google), vous obtenez un identifiant et des jetons de sécurité pour cette application. Le serveur principal de l'application doit fournir ces informations aux services de notifications Push de la plateforme en question pour pouvoir envoyer des notifications Push aux appareils. Ces jetons de sécurité peuvent être fournis sous la forme de certificats (par exemple, pour Apple iOS ou Windows Phone) ou de clés de sécurité (pour Google Android ou Windows, notamment). Ils doivent être configurés dans Notification Hubs. Leur configuration s'effectue généralement au niveau du hub de notification, mais peut aussi être réalisée au niveau de l'espace de noms dans un scénario d'architecture mutualisée.

*Espaces de nom :* les espaces de noms s'utilisent également dans le cadre du regroupement de déploiement. Dans un scénario d'architecture mutualisée, ils permettent aussi de représenter tous les hubs de notification de tous les locataires de la même application.

*Géo-distribution :* la géo-distribution n'est pas toujours nécessaire pour les notifications Push. Notez que les divers services de notifications Push (APNS, GCM, etc.) qui sont responsables de la remise finale des notifications Push aux appareils ne sont pas distribués de manière uniforme. Toutefois, si votre application est utilisée dans le monde entier, vous pouvez créer plusieurs hubs dans différents espaces de noms afin de bénéficier du service Notification Hubs dans toutes les régions Azure du monde où il est disponible. Notez que cela augmente les coûts de gestion, en particulier en ce qui concerne les inscriptions. La géo-distribution n'est donc pas recommandée et ne doit être implémentée que si elle est vraiment nécessaire.
 
###10\. Faut-il faire les inscriptions à partir du serveur principal des applications ou directement à partir des appareils ?
Il est préférable de réaliser les inscriptions sur le serveur principal des applications si vous devez authentifier le client avant de l'inscrire ou si ce serveur doit créer ou modifier des balises selon une logique d'application particulière. Pour plus d'informations, consultez les articles [Inscription auprès du serveur principal de votre application] et [Gestion des inscriptions - 2].
 
###11\. Quel est le modèle de sécurité utilisé ?
Azure Notification Hubs utilise un modèle de sécurité par signature d'accès partagé(SAP). Vous pouvez utiliser les jetons SAP au niveau de l'espace de noms racine ou au niveau granulaire des hubs de notification. Ces jetons SAP peuvent être définis avec des règles d'autorisation différentes, par exemple, des autorisations pour envoyer les messages, des autorisations pour écouter les notifications, etc. Pour plus d’informations, consultez [Modèle de sécurité de Notification Hubs].
 
###12\. Comment gérez-vous la charge utile sensible dans les notifications ? 
Toutes les notifications sont remises aux appareils par les services de notifications Push (PNS) des plateformes. Quand un expéditeur envoie une notification à Azure Notification Hubs, nous traitons la notification, puis la transmettons au PNS correspondant. Toutes les connexions entre l'expéditeur, Azure Notifications Hubs et le PNS utilisent le protocole HTTPS. Azure Notifications Hubs n'enregistre en aucune façon la charge utile du message. Pour l'envoi des charges utiles sensibles, nous recommandons toutefois d'utiliser un modèle Push sécurisé qui fonctionne ainsi : l'expéditeur envoie à l'appareil une notification ping dotée d'un ID de message mais sans la charge utile sensible. Lorsque l'application sur l'appareil reçoit cette charge utile, elle appelle directement une API sécurisée du serveur principal de l'application pour récupérer les données du message. Pour plus d'informations sur l'implémentation du modèle, consultez le [Didacticiel sur les notifications Push sécurisées avec Notification Hubs].
 
##Opérations
###1\. En quoi consiste la récupération d'urgence ?
Nous fournissons une protection de récupération d'urgence de certaines métadonnées (nom du hub de notification, chaîne de connexion, etc.). Dans le cas d'une récupération d'urgence, les données des inscriptions sont perdues. Vous devez donc prévoir une solution pour réintégrer ces données dans votre nouveau hub.

- *Étape 1* - Créez un hub de notification secondaire dans un autre contrôleur de domaine. Vous pouvez le créer juste au moment de l'événement de récupération d'urgence ou en créer un dès le départ. Cela a peu d'importance, car l'approvisionnement dans Notification Hubs est un processus rapide de l'ordre de quelques secondes. Il est toutefois recommandé d'en créer un dès le début pour limiter l'impact de l'événement de récupération d'urgence sur nos capacités de gestion. 

- *Étape 2* - Alimentez le hub de notification secondaire avec les inscriptions du hub de notification principal. N'essayez pas de conserver les inscriptions sur les deux hubs et de les maintenir synchronisées au fur et à mesure de l'ajout de nouvelles inscriptions. En effet, cela est difficile à effectuer en raison de la nature même des inscriptions qui sont destinées à expirer sur le PNS. Les inscriptions sont supprimées de Notification Hubs lorsque le PNS nous signale qu'elles ont expiré ou qu'elles ne sont plus valides.

Nous recommandons d'utiliser un serveur principal d'applications qui :

- conserve toutes ces inscriptions de son côté pour effectuer une insertion en bloc dans le hub de notification secondaire en cas de récupération d'urgence, OU

- obtient une sauvegarde régulière des inscriptions du hub principal, puis effectue une insertion en bloc dans le hub de notification secondaire.

(La fonctionnalité d'exportation/importation des inscriptions est disponible pour le niveau Standard et est décrite dans l'article [Exporter/importer des inscriptions].)
 
Si vous n'avez pas de serveur principal, lorsque l'application démarre sur les appareils, une nouvelle inscription est créée dans le hub de notification secondaire, où seront donc inscrits tous les appareils actifs. Il y a un inconvénient à cela : pendant un laps de temps, les appareils sur lesquels les applications n'ont pas été ouvertes ne recevront pas de notifications.
 
###2\. Existe-t-il une fonctionnalité de journal d'audit ?
Toutes les opérations de gestion concernant Notification Hubs sont enregistrées dans les journaux des opérations qui sont exposés dans le portail de gestion Azure.
 
##Surveillance et dépannage
###1\. Quelles sont les fonctionnalités proposées pour faciliter la résolution des problèmes ?
Azure Notification Hubs fournit plusieurs fonctionnalités vous permettant de résoudre des problèmes courants, en particulier les problèmes de suppression de notifications, qui sont les plus fréquents. Pour plus d'informations, consultez le [Guide de dépannage de Notification Hubs].
 
###2\. Quelles sont les fonctionnalités de télémétrie proposées ?
Azure Notification Hubs permet d'afficher des données de télémétrie dans le portail de gestion Azure. Pour plus d'informations sur les mesures disponibles, consultez l'article [Mesures de Notification Hubs]. Notez que la mesure « Notifications réussies » indique seulement que les notifications ont bien été remises au service de notifications Push externe (par exemple, APNS pour Apple, GCM pour Google, etc.). Nous ne disposons pas de mesures relatives à la remise finale des notifications aux appareils par le PNS. Azure Notification Hubs offre également la possibilité d'exporter les données de télémétrie par programme (pour le niveau de service Standard). Pour plus d'informations, consultez l'[exemple de mesures de Notification Hubs].

[Tarification de Notification Hubs]: http://azure.microsoft.com/pricing/details/notification-hubs/
[Contrat SLA pour Notification Hubs]: http://azure.microsoft.com/support/legal/sla/
[Étude de cas Sochi]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=7942
[Étude de cas Skanska]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5847
[Étude de cas Seattle Times]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=8354
[Étude de cas Mural.ly]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=11592
[Étude de cas 7Digital]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=3684
[API REST de Notification Hubs]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[Didacticiels de prise en main de Notification Hubs]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[envoyer des notifications par SMS]: http://azure.microsoft.com/documentation/articles/partner-twilio-mobile-services-how-to-use-voice-sms/
[didacticiel sur les applications Chrome]: http://azure.microsoft.com/documentation/articles/notification-hubs-chrome-get-started/
[Tarification de Mobile Services]: http://azure.microsoft.com/pricing/details/mobile-services/
[Inscription auprès du serveur principal de votre application]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[Gestion des inscriptions - 2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[Modèle de sécurité de Notification Hubs]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[Didacticiel sur les notifications Push sécurisées avec Notification Hubs]: http://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[Guide de dépannage de Notification Hubs]: http://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[Mesures de Notification Hubs]: https://msdn.microsoft.com/library/dn458822.aspx
[exemple de mesures de Notification Hubs]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel
[Exporter/importer des inscriptions]: https://msdn.microsoft.com/library/dn790624.aspx
 


 

<!---HONumber=August15_HO6-->