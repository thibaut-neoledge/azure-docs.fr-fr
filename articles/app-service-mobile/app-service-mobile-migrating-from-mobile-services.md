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
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/25/2015"
	ms.author="mahender"/>

# Migration de votre service mobile Azure existant vers App Service

Cette rubrique montre comment migrer une application Azure Mobile Services existante vers une nouvelle application App Service Mobile App. Un service mobile existant peut facilement migrer vers un serveur principal Mobile App. Pendant la migration, le service mobile existant continue à fonctionner.

Un service mobile qui migre vers App Service a accès à toutes les fonctionnalités App Service et c’est la [tarification App Service] qui est appliquée, et non celle de Mobile Services.

À noter également que tous les travaux planifiés se déplacent d’un plan Azure Scheduler géré par Microsoft vers un plan Azure Scheduler relevant de vos propres abonnement et contrôle. En plus de tirer parti des avantages de la migration vers App Service, vous pouvez exploiter toute la puissance d’Azure Scheduler.

### <a name="why-host"></a>Pourquoi héberger dans App Service ?

App Service offre un environnement d’hébergement plus complet pour votre application. En étant hébergé dans App Service, votre service peut accéder à des emplacements intermédiaires, des domaines personnalisés, au support Traffic Manager, et bien plus. Même si vous pouvez mettre à niveau un service mobile vers un serveur principal Mobile App une fois la migration vers App Service effectuée, vous faites peut-être partie de ces clients qui préfèrent profiter tout de suite de ces fonctionnalités, avant même d’effectuer la mise à jour du Kit de développement logiciel (SDK).

Pour plus d’informations sur les avantages d’App Service, consultez [Mobile Services et App Service].

## Migration et mise à niveau

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

- Pour les projets de serveurs Node.js, le nouveau [SDK Node.js Mobile Apps](https://github.com/Azure/azure-mobile-apps-node) apporte un certain nombre de nouvelles fonctionnalités. Par exemple, vous pouvez maintenant faire du développement et du débogage localement, utiliser n'importe quelle version de Node.js à partir de 0.10 et utiliser n'importe quel middleware Express.js pour personnaliser.

- Pour les projets serveurs .NET, les nouveaux [packages NuGet pour SDK Mobile Apps](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) ont plus de flexibilité sur les dépendances NuGet, prennent en charge les nouvelles fonctionnalités d’authentification App Service et s’associent avec tous les projets ASP.NET, y compris MVC. Pour en savoir plus sur la mise à niveau, consultez [Mettre à niveau votre service Mobile Services .NET existant sur App Service](app-service-mobile-net-upgrading-from-mobile-services.md).


## <a name="understand"></a>Présentation d’App Service Mobile Apps

Mobiles Apps dans App Service représente une nouvelle façon de générer des applications mobiles à l’aide d’Azure. Pour en savoir plus sur Mobile Apps, consultez [Que sont les applications Mobile Apps ?]

Pendant la migration vers Mobile Apps, toutes les fonctionnalités d’application existante, y compris votre logique métier, peuvent être conservées. En outre, les nouvelles fonctionnalités sont disponibles pour l'application. Dans le modèle Mobile Apps, votre code s’exécute en fait sur une application web dans App Service, qui correspond à la nouvelle version d’Azure Web Sites. Vous avez le contrôle total sur l'application web et la façon dont elle fonctionne. En outre, les fonctionnalités des applications web qui n’étaient précédemment pas disponibles pour les clients Mobile Services, telles que Traffic Manager et les emplacements de développement, peuvent désormais être utilisées.

La limitation principale que présente la mise à niveau est que les travaux planifiés Mobile Services ne sont pas intégrés. Par conséquent, il faut configurer Azure Scheduler pour appeler les API personnalisées, ou activer les fonctionnalités WebJobs.

## Migration de Service Mobile avec l’Assistant Migrer vers App Service

La façon la plus simple et recommandée pour migrer votre service mobile vers App Service consiste à utiliser l’Assistant Migrer vers App Service, disponible dans le portail Azure Classic. Accédez au [portail Azure Classic], parcourez vos services mobiles, puis sélectionnez un service mobile à faire migrer ; au bas de l’écran, un bouton **Migrer vers App Service** apparaît et vous guide tout au long du processus de migration de votre service mobile.

### <a name="what-gets-migrated"></a>Qu’est-ce qui migre ?

L’Assistant de migration modifie les batteries de serveurs qui hébergent votre service mobile pour les faire passer d’une gestion par Mobile Services à une gestion par App Service. Il déplace également tous les plans Scheduler que gérait Mobile Services mobiles d’un abonnement géré par Microsoft vers votre abonnement. Quand les batteries de serveurs sont transférées au contrôle de gestion d’App Service, tous les services Mobile Services qui étaient associés à cette batterie de serveurs sont simultanément déplacés.

Mobile Services organise vos services mobiles dans des batteries de serveurs selon leur région et leur SKU (gratuit, de base, standard). Tous vos services gratuits compris dans une région se trouvent sur la même batterie de serveurs et migrent ensemble. Certains services de base compris dans une région sont hébergés ensemble sur la même batterie de serveurs, mais si leur nombre est élevé, ils relèvent de plans distincts. Chaque service standard se trouve sur sa propre batterie de serveurs. Si vous voulez qu’un seul service migre ou éviter de déplacer un service particulier avec vos autres sites, vous pouvez le mettre à niveau vers un service de base pour qu’il se relève de son propre plan indépendant.

Les plans Azure Scheduler sont également ajoutés à votre abonnement, si vous utilisez des tâches planifiées. Il existe un nombre limité de tâches planifiées gratuites autorisées par abonnement, des coûts peuvent donc y être associés. Vous les passerez en revue suite à l’abonnement.

###  Utilisation de l’Assistant de migration

1. Accédez au [portail Azure Classic] et cliquez sur Mobile Services. 

2. Sélectionnez le service mobile à faire migrer, puis cliquez sur « Migrer vers App Service » dans la barre de menus inférieure. Cette option fait apparaître la liste des services Mobile Services à faire migrer. Consultez la [section précédente](#what-gets-migrated) pour plus d’informations sur les services qui apparaissent et pourquoi.

3. Tapez le nom du service mobile pour confirmer la migration, puis cliquez sur la coche pour continuer. Quand plusieurs services mobiles sont inclus dans la migration, vous devez quand même taper le nom du service d’origine qui a été sélectionné.

4. Une fois que la migration a commencé, vous pouvez en afficher l’état actuel à partir de la liste Mobile Services dans le [portail Azure Classic]. Celui-ci indique « en cours de migration » pour tous les services actuellement en cours de migration. Une fois toutes les migrations terminées, vous pouvez les consulter dans le [portail Azure] sous Mobile Apps. Pendant et après la migration, vos services mobiles continuent de fonctionner et l’URL ne change pas.

## Migration manuelle pour le backend .NET Mobile Services

>[AZURE.NOTE]Notez qu’il est recommandé d’effectuer la migration d’un service mobile par le biais de l’Assistant Migrer vers App Service, décrit dans la section précédente. Une migration manuelle doit uniquement intervenir dans les situations où l’Assistant ne peut pas être utilisé.

Cette section vous montre comment héberger un projet .NET Mobile Services au sein d’Azure App Service. Une application hébergée de cette manière peut utiliser l’ensemble des didacticiels du runtime .NET *Mobile Services*. Toutefois, les URL utilisant le domaine azure-mobile.net doivent être modifiées de manière à utiliser le domaine de l’instance App Service.

Un projet Mobile Services peut également être hébergé dans un [environnement App Service]. Tout le contenu de cette rubrique est toujours d’actualité, mais le site prendra la forme `contoso.contosoase.p.azurewebsites.net` au lieu de `contoso.azurewebsites.net`.

>[AZURE.NOTE]Si vous effectuez une migration manuelle, vous *ne pouvez pas* conserver votre URL **service.azure-mobile.net** existante. Quand des clients mobiles se connectent à cette URL, vous devez utiliser l’option de migration automatique ou bien maintenir votre service mobile jusqu’à ce que tous les clients mobiles soient passés à la nouvelle URL.


### <a name="app-settings"></a>Paramètres d’application
Mobile Services exige que plusieurs paramètres d’application soient disponibles dans l’environnement. Ceux-ci sont décrits dans cette section.

Afin de définir les paramètres d’application sur votre serveur principal Mobile App, commercez par vous connecter au [portail Azure]. Accédez à l’application App Service que vous voulez utiliser en tant que serveur principal Mobile App, cliquez sur **Paramètres** > **Paramètres d’application**, puis faites défiler la liste jusqu’à **Paramètres d’application**, où vous pouvez définir les paires clé-valeur exigées suivantes :

+ **MS\_MobileServiceName** doit être défini sur le nom de votre application. Par exemple, quand l’URL de votre serveur principal est `contoso.azurewebsites.net`, alors *contoso* est la valeur correcte.

+ **MS\_MobileServiceDomainSuffix** doit être défini sur le nom de votre application web. Par exemple, quand l’URL de votre serveur principal est `contoso.azurewebsites.net`, alors *azurewebsites.net* est la valeur correcte.

+ **MS\_ApplicationKey** peut accepter n’importe quelle valeur, mais il doit s’agir de la valeur utilisée par le Kit de développement logiciel (SDK) client. Un GUID est recommandé.

+ **MS\_MasterKey** peut accepter n’importe quelle valeur, mais il doit s’agir de la valeur utilisée par les clients/API d’administration. Un GUID est recommandé.

Quand vous faites migrer un service mobile, la clé principale et la clé d’application peuvent toutes les deux être obtenues à partir de l’onglet **Configurer** de la section Mobile Services du [portail Azure Classic]. Cliquez sur **Gérer les clés** en bas et copiez les clés.


### <a name="client-sdk"></a>Modification du Kit de développement logiciel (SDK) client

Dans le projet d’application client, modifiez le constructeur de l’objet client Mobile Services de sorte qu’il accepte la nouvelle URL de l’application (par exemple, `https://contoso.azurewebsites.net`) et la clé d’application configurées précédemment. La version du SDK client doit être une version **Mobile Services** et ne doit **pas** être mise à niveau. Pour les clients iOS et Android, utilisez les versions 2.x et pour Windows/Xamarin, utilisez 1.3.2. Les clients JavaScript doivent utiliser 1.2.7.

### <a name="data"></a>Activation des fonctionnalités de données

Pour utiliser les classes Entity Framework par défaut dans Mobile Services, deux autres paramètres d’application sont nécessaires.

Les chaînes de connexion sont stockées dans la section **Chaînes de connexion** du panneau Paramètres d’application, juste en-dessous de la section **Paramètres d’application**. La chaîne de connexion pour votre base de données doit être définie sous la clé **MS\_TableConnectionString**. Quand vous effectuez la migration d’un service mobile existant vers App Service, accédez à la section **Chaînes de connexion** de l’onglet **Configurer** du service mobile dans le [portail Azure Classic](https://manage.windowsazure.com/). Cliquez sur **Afficher les chaînes de connexion** et copiez la valeur.

Par défaut, le schéma à utiliser est **MS\_MobileServiceName**, mais il peut être remplacé par le paramètre **MS\_TableSchema**. Sous **Paramètres d’application**, définissez **MS\_TableSchema** sur le nom du schéma à utiliser. Si vous effectuez un déplacement vers une application Mobile Services existante, un schéma a déjà été créé à l’aide d’Entity Framework. Il s’agit du nom du service mobile, et non de l’instance App Service qui hébergera désormais le code.

### <a name="push"></a>Activation des fonctionnalités Push

Pour que les fonctionnalités Push fonctionnent, l’application web doit également connaître certaines informations sur le hub de notification.

Sous **Chaînes de connexion**, attribuez à **MS\_NotificationHubConnectionString** la chaîne de connexion DefaultFullSharedAccessSignature du hub de notification. Pour effectuer la migration d’un service mobile existant, accédez à la section **Chaînes de connexion** de l’onglet **Configurer** du service mobile dans le [portail Azure Classic](https://manage.windowsazure.com/). Cliquez sur **Afficher les chaînes de connexion** et copiez la valeur.

Le paramètre d’application **MS\_NotificationHubName** doit avoir pour valeur le nom du hub. Quand vous déplacez un service mobile existant, cette valeur est indiquée sous son onglet **Push** dans le [portail Azure Classic](https://manage.windowsazure.com/). Les autres champs de cet onglet sont liés au hub lui-même et n’ont pas à être copiés.

### <a name="auth"></a>Activation des fonctionnalités d’authentification

Les fonctionnalités d’identité ont également des paramètres d’application prérequis pour les fournisseurs individuels. Si vous effectuez un déplacement à partir d’un service mobile existant, chaque champ de l’onglet **Identité** du portail Azure Classic correspond à un paramètre d’application.

Compte Microsoft

* **MS\_MicrosoftClientID**

* **MS\_MicrosoftClientSecret**

* **MS\_MicrosoftPackageSID**

Facebook

* **MS\_FacebookAppID**

* **MS\_FacebookAppSecret**

Twitter

* **MS\_TwitterConsumerKey**

* **MS\_TwitterConsumerSecret**

Google

* **MS\_GoogleClientID**

* **MS\_GoogleClientSecret**

AAD

* **MS\_AadClientID**

* **MS\_AadTenants** - Remarque : **MS\_AadTenants** est stocké sous forme de liste de domaines de clients séparés par des virgules (le champ **Client autorisés** du portail Azure Classic).

### <a name="publish"></a>Publication du projet de service mobile

1. Dans le [portail Azure], accédez à votre application, cliquez sur **Obtenir le profil de publication** dans la barre de commandes, puis enregistrez le profil téléchargé sur l’ordinateur local.
2. Dans Visual Studio, cliquez avec le bouton droit sur le projet de serveur Mobile Services, puis cliquez sur **Publier**. 
3. Sous l’onglet Profil, sélectionnez **Importer** et recherchez le profil téléchargé.
3. Cliquez sur **Publier** pour déployer le code vers App Service.

Les journaux sont gérés via les fonctionnalités de journalisation standard d’App Service. Pour en savoir plus sur la journalisation, consultez [Activer la journalisation des diagnostics dans Azure App Service].



<!-- URLs. -->

[portail Azure]: https://portal.azure.com/
[portail Azure Classic]: https://manage.windowsazure.com/
[Que sont les applications Mobile Apps ?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /fr-FR/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Mobile App Server SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /fr-FR/documentation/services/scheduler/
[Web Job]: ../app-service-web/websites-webjobs-resources.md
[How to use the .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md


[Activer la journalisation des diagnostics dans Azure App Service]: web-sites-enable-diagnostic-log.md
[tarification App Service]: https://azure.microsoft.com/fr-FR/pricing/details/app-service/
[environnement App Service]: app-service-app-service-environment-intro.md
[Mobile Services et App Service]: app-service-mobile-value-prop-migration-from-mobile-services-preview.md
[migrate a mobile service to a mobile app on App Service]: app-service-mobile-migrating-from-mobile-services.md

<!---HONumber=AcomDC_1210_2015-->