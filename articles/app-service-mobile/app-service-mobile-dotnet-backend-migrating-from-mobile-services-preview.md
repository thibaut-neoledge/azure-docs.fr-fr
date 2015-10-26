<properties 
	pageTitle="Migration à partir de Mobile Services vers une application App Service Mobile App" 
	description="Découvrez comment migrer facilement votre application Mobile Services vers une application App Service Mobile App." 
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
	ms.date="08/11/2015" 
	ms.author="mahender"/>

# Migration d'une application Azure Mobile Service existante vers une application Azure App Service Mobile App

Cette rubrique montre comment migrer une application Azure Mobile Services existante vers une nouvelle application App Service Mobile App. Toutes les applications Mobile Services existantes peuvent être facilement migrées vers une nouvelle application App Service Mobile App. Pendant une migration, votre application Mobile Services existante peut continuer à fonctionner. Au fil du temps, le processus de migration deviendra encore plus facile, mais pour ceux qui souhaitent migrer dès aujourd'hui, la procédure suivante peut être utilisée.

>[AZURE.NOTE]Les migrations sont actuellement prises en charge uniquement pour les clients qui utilisent le backend .NET de Mobile Services. Les applications qui utilisent le backend Node.JS devront rester sur Mobile Services.

##<a name="understand"></a>Présentation d’App Service Mobile Apps

App Service Mobile Apps représente une nouvelle façon de générer des applications mobiles à l'aide de Microsoft Azure. Vous obtiendrez plus d’informations sur Mobile Apps dans la rubrique [Que sont les applications Mobile Apps ?].

Dans une migration vers Mobile Apps, toutes les fonctionnalités existantes de l'application (et le code) peuvent être conservées. En outre, les nouvelles fonctionnalités sont disponibles pour l'application. Dans le modèle Mobile Apps, votre code s'exécute en fait sur une application web (la nouvelle version d'Azure Web Sites). Vous avez le contrôle total sur l'application web et la façon dont elle fonctionne. En outre, les fonctionnalités des applications web qui n'étaient précédemment pas disponibles aux clients Mobile Services, telles que Traffic Manager et les emplacements de développement, peuvent désormais être utilisées.

Le nouveau modèle résout également l'une des principales difficultés liées à l'utilisation de Mobile Services. Désormais, il est possible de déployer n'importe quelle version d'un package NuGet quelconque sans se soucier des conflits de dépendances. Plus d’informations sur les avantages de la migration sont disponibles dans la rubrique [J’utilise déjà les sites web et des services mobiles – comment App Service peut-il m’aider ?].

##<a name="overview"></a>Vue d’ensemble de la migration de base
La méthode la plus simple d'effectuer une migration consiste à créer une nouvelle instance d'une application App Service Mobile App. Dans de nombreux cas, la migration consiste simplement à basculer vers le nouveau SDK de serveur et à republier votre code dans une nouvelle application Mobile App. Il existe toutefois des scénarios qui nécessitent une configuration supplémentaire, tels que les scénarios d'authentification avancée et l'utilisation de tâches planifiées. Ils sont couverts individuellement dans les sections suivantes.

>[AZURE.NOTE]Il est conseillé de lire et de comprendre entièrement le reste de cette rubrique avant de commencer une migration. Prenez note de toutes les fonctionnalités répertoriées ci-dessous que vous utilisez.

Vous pouvez déplacer et tester votre code à votre rythme. Quand le backend Mobile App est prêt, vous pouvez lancer une nouvelle version de votre application cliente. À ce stade, vous aurez deux copies du backend de votre application en cours d'exécution côte à côte. Vous devez vous assurer que l'ensemble des résolutions de bogue que vous apportez sont appliquées aux deux copies. Enfin, une fois que vos utilisateurs ont effectué la mise à jour vers la version la plus récente, vous pouvez supprimer le service mobile d'origine.

La procédure complète à suivre pour effectuer cette migration est la suivante :

1. Créer et configurer une application Mobile App
2. Résoudre les éventuels problèmes d'authentification
3. Publier une nouvelle version de votre application cliente
4. Supprimer l'instance Mobile Services d'origine


##<a name="mobile-app-version"></a>Configuration d’une version Mobile App de votre application
La première étape de la migration consiste à créer la ressource Mobile App qui hébergera la nouvelle version de votre application. Vous pouvez créer une application Mobile App dans la [version préliminaire du portail de gestion Azure]. Vous pouvez consulter la rubrique [Création d’une application Mobile App] pour plus d’informations.

Vous souhaiterez probablement utiliser les mêmes base de données et hub de notifications que dans Mobile Services. Vous pouvez copier ces valeurs à partir de l’onglet **Configurer** de la section Mobile Services du [portail de gestion Azure]. Sous **Chaînes de connexion**, copiez `MS_NotificationHubConnectionString` et `MS_TableConnectionString`. Accédez au site Mobile App et sélectionnez **Paramètres** et **Paramètres de l’application**, puis ajoutez-les à la section des **Chaînes de connexion**, en remplaçant les valeurs existantes.

Mobile Apps fournit une nouveau [Kit de développement logiciel (SDK) Mobile App Server] qui fournit de nombreuses fonctionnalités identiques à celles du runtime Mobile Services. Vous devez d’abord supprimer le site NuGet Mobile Services de votre projet existant et inclure à la place le kit de développement logiciel de serveur. Pour cette migration, la plupart des développeurs souhaiteront télécharger et installer le package `Microsoft.Azure.Mobile.Server.Quickstart`, car cela permet de récupérer l'ensemble requis. Ensuite, dans WebApiConfig.cs, vous pouvez remplacer

    // Use this class to set configuration options for your mobile service
    ConfigOptions options = new ConfigOptions();
    
    // Use this class to set WebAPI configuration options
    HttpConfiguration config = ServiceConfig.Initialize(new ConfigBuilder(options));

par

    HttpConfiguration config = new HttpConfiguration();

    new MobileAppConfiguration()
	    .UseDefaultConfiguration()
	    .ApplyTo(config);


>[AZURE.NOTE]Si vous souhaitez en savoir plus sur le nouveau kit de développement logiciel du serveur et sur la façon d’ajouter/supprimer des fonctionnalités de votre application, consultez la rubrique [Comment utiliser le kit de développement logiciel du serveur .NET].

Il existe quelques autres changements entre les kits de développement logiciel Mobile Services et Mobile Apps, mais ils sont faciles à gérer. Au fil de votre projet, vous devrez peut-être modifier certaines instructions using, avec l'aide de Visual Studio.

Vous devez ajouter l’attribut `[MobileAppController]` à tous vos ApiController, en plaçant simplement ce décorateur juste avant la définition de classe.

Il n'y a plus d’attribut `[AuthorizeLevel]`. Vous devez plutôt décorer vos contrôleurs et vos méthodes avec l’attribut `[Authorize]` ASP.NET standard. Notez également que les contrôleurs qui n'ont pas de `[AuthorizeLevel]` ne sont plus protégés par une clé d'application : ils sont traités comme publics. Le nouveau kit de développement logiciel n'utilise plus de clés d'application et de clés principales.

En matière de notifications Push, l'élément principal qui vous semblera peut-être manquant dans le kit de développement logiciel de serveur est la classe PushRegistrationHandler. Les inscriptions sont traitées légèrement différemment dans Mobile Apps et les inscriptions sans balise sont activées par défaut. La gestion des balises peut être accomplie à l'aide d'API personnalisées. Reportez-vous à la rubrique [Ajout de notifications push à votre application mobile] pour plus d’informations.

Les tâches planifiées ne sont pas intégrées dans Mobile Apps, si bien que toutes les tâches existantes dont vous disposez dans votre backend .NET doivent être migrées individuellement. Une option consiste à créer une [tâche web] planifiée sur le site de code Mobile App. Vous pouvez également configurer un contrôleur contenant le code de votre tâche et configurer [Azure Scheduler] pour traiter ce point de terminaison au moment prévu.

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

>[AZURE.NOTE]Certains fournisseurs, tels que Twitter et Compte Microsoft, ne vous permettent pas de spécifier plusieurs URI de redirection sur des domaines différents. Si votre application utilise l'un de ces fournisseurs et prend une dépendance sur les ID utilisateur, il est conseillé de ne pas essayer de migrer à ce moment.

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

Une fois que vos clients ont eu l'occasion de recevoir ces mises à jour, vous pouvez supprimer la version Mobile Services de votre application. À ce stade, vous avez terminé la migration vers une application App Service Mobile App.

<!-- URLs. -->

[version préliminaire du portail de gestion Azure]: https://portal.azure.com/
[portail de gestion Azure]: https://manage.windowsazure.com/
[Que sont les applications Mobile Apps ?]: app-service-mobile-value-prop-preview.md
[J’utilise déjà les sites web et des services mobiles – comment App Service peut-il m’aider ?]: /fr-FR/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services-preview
[Kit de développement logiciel (SDK) Mobile App Server]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Création d’une application Mobile App]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview.md
[Ajout de notifications push à votre application mobile]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview.md
[Ajout de l’authentification à votre application mobile]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-users-preview.md
[Azure Scheduler]: /fr-FR/documentation/services/scheduler/
[tâche web]: ../app-service-web/websites-webjobs-resources.md
[Envoi de notifications push interplateformes]: app-service-mobile-dotnet-backend-xamarin-ios-push-notifications-to-user-preview.md
[Comment utiliser le kit de développement logiciel du serveur .NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md

<!---HONumber=Oct15_HO3-->