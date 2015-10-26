<properties
	pageTitle="Que sont les applications Mobile Apps ?"
	description="Découvrez les avantages qu'App Service apporte à vos applications mobiles d'entreprise."
	services="app-service\mobile"
	documentationCenter=""
	authors="kirillg"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="na"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/05/2015"
	ms.author="kirillg"/>

# <a name="getting-started"> </a>Qu’est ce que la fonctionnalité Mobile Apps ?

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

Azure App Service est une offre PaaS (Platform as a Service) entièrement gérée, destinée aux développeurs professionnels, qui permet de bénéficier d'un ensemble complet de fonctionnalités pour les scénarios web, mobiles et d'intégration. La fonctionnalité *Mobile Apps* dans *Azure App Service* offre une plateforme de développement d’applications mobiles hautement évolutive pour les développeurs d’entreprise et les intégrateurs système. Disponible pour tous, elle fournit un ensemble complet de fonctionnalités pour les développeurs d’applications mobiles.

![Mobile Apps](./media/app-service-mobile-value-prop-preview/overview.png)

##Pourquoi Mobile Apps ?
La fonctionnalité *Mobile Apps* dans *Azure App Service* offre une plateforme de développement d’applications mobiles hautement évolutive pour les développeurs d’entreprise et les intégrateurs système. Disponible pour tous, elle fournit un ensemble complet de fonctionnalités pour les développeurs d’applications mobiles. Mobile Apps vous permet d'effectuer les opérations suivantes :

- **Générer des applications natives et multiplateformes** : que vous génériez des applications natives iOS, Android et Windows ou des applications multiplateformes Xamarin ou Cordova (Phonegap), vous pouvez tirer parti d'App Service à l'aide de Kits de développement logiciel (SDK) natifs.  
- **Se connecter aux systèmes de votre entreprise** : la fonctionnalité Applications mobiles vous permet d'ajouter une authentification d'entreprise en quelques minutes et de vous connecter à vos ressources locales et cloud de votre entreprise.
- **Se connecter facilement aux API SaaS** : avec plus de 40 connecteurs API SaaS, vous pouvez facilement intégrer à votre application les API SaaS que votre entreprise utilise aujourd'hui. Vous souhaitez mettre à jour l'état du compte dans votre système CRM et de facturation ? Mobile Apps vous donne accès à des API SaaS d'entreprise.
- **Créer des applications disponibles hors connexion avec la synchronisation** : augmentez la productivité de votre personnel mobile en créant des applications qui fonctionnent hors connexion et qui utilisent la fonctionnalité Applications mobiles pour synchroniser les données en arrière-plan quand la connexion est établie avec l'une de vos sources de données d'entreprise ou API SaaS.
- **Notifications push pour des millions de personnes en quelques secondes** : fidélisez vos clients en leur envoyant en temps opportun des notifications push instantanées, adaptées à leurs besoins, sur n'importe quel appareil.

## Concepts propres à Mobile App
- **Authentification unique** : bénéficiez d'une liste toujours plus longue de fournisseurs d'identité, dont notamment Azure Active Directory, Facebook, Google, Twitter et le compte Microsoft, et tirez parti de la fonctionnalité Applications mobiles pour ajouter l'authentification à votre application en quelques minutes.
- **Synchronisation hors connexion** : la fonctionnalité Applications mobiles vous permet de créer facilement des applications robustes et réactives qui permettent aux employés de travailler hors connexion en l'absence de connexion et de synchroniser leurs données avec vos systèmes de backend d'entreprise lorsque la connexion des appareils est rétablie. La fonctionnalité de synchronisation hors connexion est prise en charge sur toutes les plateformes clientes et fonctionne avec n'importe quelle source de données, notamment SQL, le stockage de table, Mongo et Document DB, et toute API SaaS, notamment Office 365, Salesforce, Dynamics ou des bases de données sur site.
- **Notifications push** : la fonctionnalité Applications mobiles offre un moteur de notifications push mobiles très évolutif, Notification Hubs, capable d'envoyer en quelques secondes des millions de notifications push personnalisées à des segments dynamiques du public utilisant des appareils iOS, Android, Windows ou Kindle. Vous pouvez facilement associer Notification Hubs à n'importe quel backend d'application existant, que ce backend soit hébergé localement ou dans le cloud.
- **Mise à l'échelle automatique** : App Service vous permet d'augmenter rapidement la taille ou le nombre de vos machines virtuelles pour vous adapter à n'importe quelle charge cliente entrante. Sélectionnez manuellement le nombre et la taille des machines virtuelles, ou configurez la mise à l'échelle automatique pour dimensionner votre backend d'application mobile en fonction de la charge ou d'une planification.

## Mise en route ##
Pour prendre en main la fonctionnalité Applications mobiles, suivez le didacticiel [Prise en main].

Pour plus d’informations sur la plateforme Azure App Service, consultez [Azure App Service].

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement et gratuitement une application de départ temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

<!-- URLs. -->
[Migrate your Mobile Service to App Service]: app-service-mobile-dotnet-backend-migrating-from-mobile-services-preview.md

[Azure App Service]: ../app-service/app-service-value-prop-what-is.md
[Prise en main]: app-service-mobile-ios-get-started-offline-data-preview.md

[Management Portal]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[JavaScript backend version]: ../mobile-services-ios-get-started.md
 

<!---HONumber=Oct15_HO3-->