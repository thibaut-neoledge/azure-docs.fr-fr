<properties 
	pageTitle="Mettre à niveau à partir de Mobile Services vers Azure App Service" 
	description="Découvrez comment mettre à niveau facilement votre application Mobile Services vers App Service Mobile Apps" 
	services="app-service\mobile" 
	documentationCenter="" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/03/2015" 
	ms.author="mahender"/>

# Mettre à niveau votre application .NET Azure Mobile Services existante sur App Service

App Service Mobile représente une nouvelle façon de créer des applications mobiles avec Microsoft Azure. Pour en savoir plus, consultez [Qu'est-ce que Mobile Apps ?].

Cette rubrique décrit comment mettre à niveau une application de backend .NET existante à partir d'Azure Mobile Services vers une nouvelle application App Service Mobile Apps. Pendant cette mise à niveau, votre application Mobile Services existante peut continuer à fonctionner.

Les backends mobiles mis à niveau vers Azure App Service ont accès à toutes les fonctionnalités App Service et sont facturés selon la [tarification App Service], et non celle de Mobile Services.

##Migration et mise à niveau

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

###Améliorations du Kit de développement logiciel (SDK) serveur .NET Mobile Apps

La mise à niveau vers les nouveaux [packages NuGet du SDK Mobile Apps](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) offre les avantages suivants :

- Plus de flexibilité sur les dépendances NuGet. L'environnement d'hébergement ne fournit plus ses propres versions des packages NuGet : vous pouvez donc utiliser d'autres versions compatibles. Toutefois, si de nouvelles résolutions de bogues critiques ou mises à jour de sécurité sont apportées au SDK du serveur mobile ou à ses dépendances, vous devez mettre à jour manuellement votre service.

- Prise en charge de nouvelles fonctionnalités d'authentification App Service. Cela vous permet d'utiliser une configuration d'authentification commune à vos applications mobiles et web. De plus, les fonctionnalités d'authentification sont maintenant présentes dans leur propre composant middleware OWIN, ce qui signifie qu'une même configuration d'authentification peut être utilisée pour les itinéraires mobiles et web.

- Prise en charge d'autres itinéraires et types de projets ASP.NET. Vous pouvez désormais héberger des contrôleurs d'API web et MVC dans votre projet de backend mobile.

- Plus de souplesse dans le SDK mobile. Vous pouvez contrôler explicitement les fonctionnalités et itinéraires définis, comme l'authentification, les API de table et le point de terminaison de l'enregistrement Push. Pour en savoir plus, consultez [Utilisation du Kit de développement logiciel (SDK) serveur .NET pour Azure Mobile Apps](app-service-mobile-net-upgrading-from-mobile-services.md#server-project-setup).

- Davantage d'options pour les infrastructures d'injection de dépendances. Le SDK serveur .NET Mobile Services possède une dépendance à autofac, mais le SDK du serveur Mobile Apps vous permet d'utiliser d'autres infrastructures, comme [Unity](https://unity.codeplex.com/).

>[AZURE.NOTE]Les SDK clients Mobile Services ne sont **pas** compatible avec le nouveau SDK serveur Mobile Apps. Si des applications clientes mobiles se connectent à votre service mobile existant, vous devez maintenir le service jusqu'à ce que tous les clients mobiles soient passés au **SDK client** Mobile Apps.
> 
> Le service mobile existant peut *migrer* automatiquement sur App Service sans modification des applications clientes existantes. Pour en savoir plus sur la migration, consultez [Migrer votre service Mobile Services existant sur App Service].


##<a name="overview"></a>Présentation de la mise à niveau de base
La méthode la plus simple pour effectuer une mise à niveau consiste à créer une nouvelle instance d'une application App Service Mobile Apps. Dans de nombreux cas, la mise à niveau consiste simplement à basculer vers le nouveau SDK serveur Mobile Apps et à republier votre code dans une nouvelle application Mobile Apps. Il existe toutefois des scénarios qui nécessitent une configuration supplémentaire, tels que les scénarios d'authentification avancée et l'utilisation de tâches planifiées. Ils sont couverts individuellement dans les sections suivantes.

>[AZURE.NOTE]Il est conseillé de lire et de comprendre entièrement le reste de cette rubrique avant de commencer une mise à niveau. Prenez note de toutes les fonctionnalités répertoriées ci-dessous que vous utilisez.

Vous pouvez déplacer et tester votre code à votre rythme. Quand le backend Mobile App est prêt, vous pouvez lancer une nouvelle version de votre application cliente. À ce stade, vous aurez deux copies du backend de votre application en cours d'exécution côte à côte. Vous devez vous assurer que l'ensemble des résolutions de bogue que vous apportez sont appliquées aux deux copies. Enfin, une fois que vos utilisateurs ont effectué la mise à jour vers la version la plus récente, vous pouvez supprimer le service mobile d'origine.

Voici la procédure complète à suivre pour effectuer cette mise à niveau :

1. Créer et configurer une application Mobile App
2. Résoudre les éventuels problèmes d'authentification
3. Publier une nouvelle version de votre application cliente
4. Supprimer l'instance Mobile Services d'origine


##<a name="mobile-app-version"></a>Configuration d’une version Mobile App de votre application
La première étape de la mise à niveau consiste à créer la ressource Mobile Apps qui hébergera la nouvelle version de votre application. Vous pouvez créer une application Mobile App dans la [version préliminaire du portail de gestion Azure]. Vous pouvez consulter la rubrique [Création d’une application Mobile App] pour plus d’informations.

Vous souhaiterez probablement utiliser les mêmes base de données et hub de notifications que dans Mobile Services. Vous pouvez copier ces valeurs à partir de l’onglet **Configurer** de la section Mobile Services du [portail de gestion Azure]. Sous **Chaînes de connexion**, copiez `MS_NotificationHubConnectionString` et `MS_TableConnectionString`. Accédez au site Mobile App et sélectionnez **Paramètres** et **Paramètres de l’application**, puis ajoutez-les à la section des **Chaînes de connexion**, en remplaçant les valeurs existantes.

Mobile Apps fournit une nouveau [Kit de développement logiciel (SDK) Mobile App Server] qui fournit de nombreuses fonctionnalités identiques à celles du runtime Mobile Services. Vous devez d’abord supprimer le site NuGet Mobile Services de votre projet existant et inclure à la place le kit de développement logiciel de serveur. Pour cette mise à niveau, la plupart des développeurs souhaiteront télécharger et installer le package `Microsoft.Azure.Mobile.Server.Quickstart`, car cela permet d'extraire la totalité de l'ensemble requis. Ensuite, dans WebApiConfig.cs, vous pouvez remplacer

    // Use this class to set configuration options for your mobile service
    ConfigOptions options = new ConfigOptions();
    
    // Use this class to set WebAPI configuration options
    HttpConfiguration config = ServiceConfig.Initialize(new ConfigBuilder(options));

par

    HttpConfiguration config = new HttpConfiguration();

    new MobileAppConfiguration()
	    .UseDefaultConfiguration()
	    .ApplyTo(config);


>[AZURE.NOTE]Si vous souhaitez en savoir plus sur le nouveau SDK serveur .NET et sur la façon d'ajouter / de supprimer des fonctionnalités de votre application, consultez la rubrique [Utilisation du Kit de développement logiciel (SDK) serveur .NET].

Il existe quelques autres changements entre les kits de développement logiciel Mobile Services et Mobile Apps, mais ils sont faciles à gérer. Au fil de votre projet, vous devrez peut-être modifier certaines instructions using, avec l'aide de Visual Studio.

Vous devez ajouter l’attribut `[MobileAppController]` à tous vos ApiController, en plaçant simplement ce décorateur juste avant la définition de classe.

Il n'y a plus d’attribut `[AuthorizeLevel]`. Vous devez plutôt décorer vos contrôleurs et vos méthodes avec l’attribut `[Authorize]` ASP.NET standard. Notez également que les contrôleurs qui n'ont pas de `[AuthorizeLevel]` ne sont plus protégés par une clé d'application : ils sont traités comme publics. Le nouveau kit de développement logiciel n'utilise plus de clés d'application et de clés principales.

En matière de notifications Push, l'élément principal qui vous semblera peut-être manquant dans le kit de développement logiciel de serveur est la classe PushRegistrationHandler. Les inscriptions sont traitées légèrement différemment dans Mobile Apps et les inscriptions sans balise sont activées par défaut. La gestion des balises peut être accomplie à l'aide d'API personnalisées. Reportez-vous à la rubrique [Ajout de notifications push à votre application mobile] pour plus d’informations.

Les tâches planifiées ne sont pas intégrées à Mobile Apps, si bien que toutes les tâches existantes dans votre backend .NET doivent être mises à niveau individuellement. Une option consiste à créer une [tâche web] planifiée sur le site de code Mobile App. Vous pouvez également configurer un contrôleur contenant le code de votre tâche et configurer [Azure Scheduler] pour traiter ce point de terminaison au moment prévu.

L’objet `ApiServices` ne fait plus partie du kit de développement logiciel. Pour accéder aux paramètres de Mobile Apps, vous pouvez utiliser les éléments suivants :

    MobileAppSettingsDictionary settings = this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings(); 

De même, la journalisation s’effectue désormais avec le traçage ASP.NET standard :

    ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
    traceWriter.Info("Hello, World");  

##<a name="authentication"></a>Considérations relatives à l’authentification
L'une des principales différences entre Mobile Apps et Mobile Services tient au fait que la connexion est maintenant gérée par la passerelle App Service dans le cas de Mobile Apps, et non pas par le site exécutant votre code. Si votre groupe de ressources n'en a pas encore, vous pouvez configurer une passerelle en accédant à votre application Azure Mobile App dans le portail de gestion. Puis sélectionnez **Paramètres** et **Authentification utilisateur** sous la catégorie **Mobile**. Cliquez sur **Créer** pour associer une passerelle à votre application Mobile App.

Plus généralement, la plupart des applications ne nécessitent aucune action supplémentaire, mais il convient de noter quelques scénarios avancés.

Pour la plupart des applications, il convient d’utiliser simplement une nouvelle inscription avec le fournisseur d’identité cible. Vous pouvez obtenir des informations sur l’ajout d’une identité à une application App Service en suivant le didacticiel [Ajout de l’authentification à votre application mobile].

Si votre application prend des dépendances sur les ID utilisateur, en les stockant par exemple dans une base de données, il est important de noter que les ID utilisateur sont différents entre Mobile Services et App Service Mobile Apps. Toutefois, il est possible d'obtenir l'ID utilisateur Mobile Services dans votre application App Service Mobile App en utilisant les éléments suivants (avec Facebook par exemple) :

    MobileAppUser = (MobileAppUser) this.User;
    FacebookCredentials creds = await user.GetIdentityAsync<FacebookCredentials>();
    string mobileServicesUserId = creds.Provider + ":" + creds.UserId;

En outre, si vous prenez des dépendances sur les ID utilisateur, il est important de tirer parti de la même inscription avec un fournisseur d'identité si possible. Les ID utilisateur sont généralement limités à l'inscription de l'application qui a été utilisée, si bien que l'introduction d'une nouvelle inscription peut créer des problèmes de mise en correspondance des utilisateurs avec leurs données. Si votre application a besoin d'utiliser la même inscription de fournisseur d'identité pour une raison quelconque, vous pouvez procéder comme suit :

1. Copiez les informations de connexion ID client et Clé secrète client pour chaque fournisseur utilisé par votre application.
2. Ajoutez les points de terminaison /signin-* de la passerelle comme URI de redirection supplémentaire pour chaque fournisseur. 

>[AZURE.NOTE]Certains fournisseurs, tels que Twitter et Compte Microsoft, ne vous permettent pas de spécifier plusieurs URI de redirection sur des domaines différents. Si votre application utilise l'un de ces fournisseurs et acquiert une dépendance aux ID utilisateurs, il est conseillé de ne pas tenter d'effectuer la mise à niveau pour le moment.

##<a name="updating-clients"></a>Mise à jour des clients
Une fois que vous avez un backend Mobile App opérationnel, vous pouvez mettre à jour votre application cliente pour utiliser la nouvelle application Mobile App. Mobile Apps inclut également une nouvelle version des Kits de développement logiciel (SDK) de client Mobile Services, ce qui permettra aux développeurs de tirer parti des nouvelles fonctionnalités App Service. Une fois que vous disposez d'une version Mobile App de votre backend, vous pouvez lancer une nouvelle version de votre application cliente qui tire parti de la nouvelle version du Kit de développement logiciel (SDK).

Le changement principal requis de votre code client se trouve dans le constructeur. Outre l'URL de votre site Mobile App, vous devez fournir l'URL de la passerelle App Service qui héberge vos paramètres d'authentification :

    public static MobileServiceClient MobileService = new MobileServiceClient(
        "https://contoso.azurewebsites.net", // URL of the Mobile App
        "https://contoso-gateway.azurewebsites.net", // URL of the App Service Gateway
        "" // Formerly app key. To be removed in future client SDK update
    );

Cela permettra au client de router les demandes vers les composants de votre application Mobile App. Vous trouverez plus de détails spécifiques à votre plateforme cible en utilisant la rubrique [Création d’une application Mobile App].

Dans la même mise à jour, vous devez régler les appels d'inscription aux notifications push que vous effectuez. De nouvelles API améliorent le processus d'inscription (par exemple, en utilisant Windows) :

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    await MobileService.GetPush().Register(channel.Uri); 

Reportez-vous aux rubriques [Ajout de notifications push à votre application mobile] et [Envoi de notifications push interplateformes] pour plus d’informations spécifiques à votre plateforme cible.

Une fois que vos clients ont eu l'occasion de recevoir ces mises à jour, vous pouvez supprimer la version Mobile Services de votre application. À ce stade, vous avez entièrement mis à niveau votre application vers App Service Mobile Apps avec le tout dernier SDK serveur Mobile Apps.

<!-- URLs. -->

[version préliminaire du portail de gestion Azure]: https://portal.azure.com/
[portail de gestion Azure]: https://manage.windowsazure.com/
[Qu'est-ce que Mobile Apps ?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /fr-FR/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Kit de développement logiciel (SDK) Mobile App Server]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Création d’une application Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Ajout de notifications push à votre application mobile]: app-service-mobile-xamarin-ios-get-started-push.md
[Ajout de l’authentification à votre application mobile]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /fr-FR/documentation/services/scheduler/
[tâche web]: ../app-service-web/websites-webjobs-resources.md
[Envoi de notifications push interplateformes]: app-service-mobile-xamarin-ios-push-notifications-to-user.md
[Utilisation du Kit de développement logiciel (SDK) serveur .NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-dotnet-backend-migrating-from-mobile-services.md
[Migrer votre service Mobile Services existant sur App Service]: app-service-mobile-dotnet-backend-migrating-from-mobile-services.md
[tarification App Service]: https://azure.microsoft.com/fr-FR/pricing/details/app-service/

<!---HONumber=Nov15_HO3-->