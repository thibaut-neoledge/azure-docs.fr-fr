<properties 
	pageTitle="Migration à partir de Mobile Services vers une application App Service Mobile App" 
	description="Découvrez comment migrer facilement votre application Mobile Services vers une application App Service Mobile App." 
	services="app-service\mobile" 
	documentationCenter="" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/23/2015" 
	ms.author="mahender"/>

# Migration d'une application Azure Mobile Service existante vers une application Azure App Service Mobile App

Cette rubrique montre comment migrer une application Azure Mobile Services existante vers une nouvelle application App Service Mobile App. Toutes les applications Mobile Services existantes peuvent être facilement migrées vers une nouvelle application App Service Mobile App. Pendant une migration, votre application Mobile Services existante peut continuer à fonctionner. Au fil du temps, le processus de migration deviendra encore plus facile, mais pour ceux qui souhaitent migrer dès aujourd'hui, la procédure suivante peut être utilisée.

>[AZURE.NOTE]Les migrations sont actuellement prises en charge uniquement pour les clients qui utilisent le backend .NET de Mobile Services. Les applications qui utilisent le backend Node.JS devront rester sur Mobile Services.

##<a name="understand"></a>Présentation d’App Service Mobile Apps

App Service Mobile Apps représente une nouvelle façon de générer des applications mobiles à l'aide de Microsoft Azure. Vous obtiendrez plus d’informations sur Mobile Apps dans la rubrique [Que sont les applications Mobile Apps ?].

Dans une migration vers Mobile Apps, toutes les fonctionnalités existantes de l'application (et le code) peuvent être conservées. En outre, les nouvelles fonctionnalités sont disponibles pour l'application. Dans le modèle Mobile Apps, votre code s'exécute en fait sur une application web (la nouvelle version d'Azure Web Sites). Vous avez le contrôle total sur l'application web et la façon dont elle fonctionne. En outre, les fonctionnalités des applications web qui n'étaient précédemment pas disponibles aux clients Mobile Services, telles que Traffic Manager et les emplacements de développement, peuvent désormais être utilisées.

Le nouveau modèle résout également l'une des principales difficultés liées à l'utilisation de Mobile Services. Désormais, il est possible de déployer n'importe quelle version d'un package NuGet quelconque sans se soucier des conflits de dépendances. Plus d’informations sur les avantages de la migration sont disponibles dans la rubrique [J’utilise déjà les sites web et des services mobiles – comment App Service peut-il m’aider ?].

Quand vous créez une application App Service Mobile App, vous obtenez :

- une ressource Mobile App, qui contient de nouvelles fonctionnalités ; 
- un site Code Mobile App, qui exécute votre projet d'API web à l'aide du Kit de développement logiciel (SDK) Mobile App Server ;
- une passerelle App Service, qui gère la connexion et vous permet d'ajouter des applications API App Service à votre application.

##<a name="overview"></a>Vue d’ensemble de la migration de base
La méthode la plus simple d'effectuer une migration consiste à créer une nouvelle instance d'une application App Service Mobile App. Dans de nombreux cas, la migration consiste simplement à basculer vers le nouveau SDK de serveur et à republier votre code dans une nouvelle application Mobile App. Il existe toutefois des scénarios qui nécessitent une configuration supplémentaire, tels que les scénarios d'authentification avancée et l'utilisation de tâches planifiées. Ils sont couverts individuellement dans les sections suivantes.

>[AZURE.NOTE]Il est conseillé de lire et de comprendre entièrement le reste de cette rubrique avant de commencer une migration. Prenez note de toutes les fonctionnalités répertoriées ci-dessous que vous utilisez.

Vous pouvez déplacer et tester votre code à votre rythme. Quand le backend Mobile App est prêt, vous pouvez lancer une nouvelle version de votre application cliente. À ce stade, vous aurez deux copies de l'application en cours d'exécution côte à côte. Vous devez vous assurer que l'ensemble des résolutions de bogue que vous apportez sont appliquées aux deux copies. Enfin, une fois que vos utilisateurs ont effectué la mise à jour vers la version la plus récente, vous pouvez supprimer le service mobile d'origine.

La procédure complète à suivre pour effectuer cette migration est la suivante :

1. Créer et configurer une application Mobile App
2. Résoudre les éventuels problèmes d'authentification
3. Publier une nouvelle version de votre application cliente
4. Supprimer l'instance Mobile Services d'origine


##<a name="mobile-app-version"></a>Configuration d’une version Mobile App de votre application
La première étape de la migration consiste à créer le service App Service qui hébergera la nouvelle version de votre application. Vous pouvez créer une application Mobile App dans la [version préliminaire du portail de gestion Azure]. Vous pouvez consulter la rubrique [Création d’une application Mobile App] pour plus d’informations.

Vous souhaiterez probablement utiliser les mêmes base de données et hub de notifications que dans Mobile Services. Vous pouvez copier ces valeurs à partir de l’onglet **Configurer** de la section Mobile Services du [portail de gestion Azure]. Sous **Chaînes de connexion**, copiez `MS_NotificationHubConnectionString` et `MS_TableConnectionString`. Accédez au site Code Mobile App et sélectionnez **Paramètres** et **Paramètres de l’application**, puis ajoutez les chaînes de connexion, en remplaçant les valeurs existantes. Vous devez également ajouter ces valeurs à la ressource Mobile App. Pour ce faire, accédez au panneau Mobile App et sélectionnez **Paramètres** puis **Propriétés**. Cliquez sur le lien **Hôte d’application API** pour afficher le site qui héberge la ressource Mobile App. Accédez à **Paramètres** et **Paramètres de l’application**, puis collez les chaînes de connexion comme dans le site de code. Ne modifiez pas les autres valeurs car cela pourrait nuire aux fonctionnalités Mobile App. Notez qu'actuellement le panneau Mobile App continue d'afficher les connexions existantes, même après cette étape de configuration. Une action supplémentaire peut être nécessaire après la mise à jour de l'expérience Mobile Apps.

Mobile Apps fournit une nouveau [Kit de développement logiciel (SDK) Mobile App Server] qui fournit de nombreuses fonctionnalités identiques à celles du runtime Mobile Services. Vous devez supprimer le site NuGet Mobile Services de votre projet existant et inclure à la place le Kit de développement logiciel (SDK) de serveur. Vous devrez peut-être modifier certaines instructions using, avec l'aide de Visual Studio. L'élément principal qui vous semblera peut-être manquant dans le Kit de développement logiciel (SDK) de serveur est la classe PushRegistrationHandler. Les inscriptions sont traitées légèrement différemment dans Mobile Apps et les inscriptions sans balise sont activées par défaut. La gestion des balises peut être accomplie à l'aide d'API personnalisées. Reportez-vous à la rubrique [Ajout de notifications push à votre application mobile] pour plus d’informations.

Les tâches planifiées ne sont pas intégrées dans Mobile Apps, si bien que toutes les tâches existantes dont vous disposez dans votre backend .NET doivent être migrées individuellement. Une option consiste à créer une [tâche web] planifiée sur le site de code Mobile App. Vous pouvez également configurer un contrôleur contenant le code de votre tâche et configurer [Azure Scheduler] pour traiter ce point de terminaison au moment prévu.


##<a name="authentication"></a>Considérations relatives à l’authentification
L'une des principales différences entre Mobile Apps et Mobile Services tient au fait que la connexion est gérée par la passerelle App Service dans le cas de Mobile Apps, et non pas le site de code. Pour la plupart des applications, cela ne nécessitera aucune action supplémentaire, mais il convient de noter quelques scénarios avancés.

Pour la plupart des applications, il convient d’utiliser simplement une nouvelle inscription avec le fournisseur d’identité cible. Vous pouvez obtenir des informations sur l’ajout d’une identité à une application App Service en suivant le didacticiel [Ajout de l’authentification à votre application mobile].

Si votre application prend des dépendances sur les ID utilisateur, en les stockant par exemple dans une base de données, il est important de noter que les ID utilisateur sont différents entre Mobile Services et App Service Mobile Apps. Toutefois, il est possible d'obtenir l'ID utilisateur Mobile Services dans votre application App Service Mobile App en utilisant les éléments suivants (avec Facebook par exemple) :

    ServiceUser user = (ServiceUser) this.User;
    FacebookCredentials creds = (await user.GetIdentitiesAsync()).OfType< FacebookCredentials >().FirstOrDefault();
    string mobileServicesUserId = creds.Provider + ":" + creds.UserId;

En outre, si vous prenez des dépendances sur les ID utilisateur, il est important de tirer parti de la même inscription avec un fournisseur d'identité si possible. Les ID utilisateur sont généralement limités à l'inscription de l'application qui a été utilisée, si bien que l'introduction d'une nouvelle inscription peut créer des problèmes de mise en correspondance des utilisateurs avec leurs données. Si votre application a besoin d'utiliser la même inscription de fournisseur d'identité pour une raison quelconque, vous pouvez procéder comme suit :

1. Copiez les informations de connexion ID client et Clé secrète client pour chaque fournisseur utilisé par votre application.
2. Ajoutez les points de terminaison /signin-* de la passerelle comme URI de redirection supplémentaire pour chaque fournisseur. 

>[AZURE.NOTE]Certains fournisseurs, tels que Twitter et Compte Microsoft, ne vous permettent pas de spécifier plusieurs URI de redirection sur des domaines différents. Si votre application utilise l'un de ces fournisseurs et prend une dépendance sur les ID utilisateur, il est conseillé de ne pas essayer de migrer à ce moment.

##<a name="updating clients"></a>Mise à jour des clients
Une fois que vous avez un backend Mobile App opérationnel, vous pouvez mettre à jour votre application cliente pour utiliser la nouvelle application Mobile App. Mobile Apps inclut également une nouvelle version des Kits de développement logiciel (SDK) de client Mobile Services, ce qui permettra aux développeurs de tirer parti des nouvelles fonctionnalités App Service. Une fois que vous disposez d'une version Mobile App de votre backend, vous pouvez lancer une nouvelle version de votre application cliente qui tire parti de la nouvelle version du Kit de développement logiciel (SDK).

Le changement principal requis de votre code client se trouve dans le constructeur. Outre l'URL de votre site Code Mobile App et la clé d'application, vous devez fournir l'URL de la passerelle App Service qui héberge vos paramètres d'authentification :

    public static MobileServiceClient MobileService = new MobileServiceClient(
        "https://contoso-code.azurewebsites.net", //URL of the Mobile App Code
        "https://contosogateway.azurewebsites.net", //URL of the App Service Gateway
        "983682c4-f043-483e-a75b-8a8545fc1846"
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
[J’utilise déjà les sites web et des services mobiles – comment App Service peut-il m’aider ?]: /fr-fr/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services-preview
[Kit de développement logiciel (SDK) Mobile App Server]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Création d’une application Mobile App]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview.md
[Ajout de notifications push à votre application mobile]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview.md
[Ajout de l’authentification à votre application mobile]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-users-preview.md
[Azure Scheduler]: /fr-fr/documentation/services/scheduler/
[tâche web]: ../app-service-web/websites-webjobs-resources.md
[Envoi de notifications push interplateformes]: app-service-mobile-dotnet-backend-xamarin-ios-push-notifications-to-user-preview.md

<!---HONumber=July15_HO4-->