<properties
	pageTitle="Hébergement d’un projet Mobile Services dans App Service | Microsoft Azure"
	description="Découvrez comment exécuter un projet Mobile Services au sein d’App Service"
	documentationCenter=""
	authors="mattchenderson"
	manager="dwrede"
	editor="na"
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="get-started-article"
	ms.date="10/08/2015"
	ms.author="mahender"/>

# Hébergement d’un projet .NET Mobile Services dans App Service

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

Cette rubrique montre comment héberger un projet .NET Mobile Services au sein d’Azure App Service. Les applications exécutées de cette manière ont accès à toutes les fonctionnalités App Service et sont facturées selon la [tarification App Service], et non celle de Mobile Services.

Une application hébergée de cette manière peut utiliser l’ensemble des didacticiels du runtime .NET Mobile Services. Toutefois, les URL utilisant le domaine azure-mobile.net doivent être modifiées de manière à utiliser le domaine de l’instance App Service.

Un projet Mobile Services peut également être hébergé dans un [environnement App Service]. L’ensemble du contenu de cette rubrique s’applique toujours, mais le site aura la forme contoso.contosoase.p.azurewebsites.net au lieu de contoso.azurewebsites.net.

## <a name="app-settings"></a>Pourquoi héberger dans App Service ?

App Service offre un environnement d’hébergement plus complet pour votre application. En étant hébergé dans App Service, votre service peut accéder à des emplacements intermédiaires, des domaines personnalisés, au support Traffic Manager, et bien plus. Bien que vous puissiez [migrer un service mobile vers une application mobile sur App Service], vous souhaiterez peut-être profiter de ces fonctionnalités immédiatement avant d’effectuer la mise à jour du Kit de développement logiciel (SDK).

La limitation principale que présente l’hébergement dans App Service est que les travaux planifiés Mobile Services ne sont pas intégrés. Par conséquent, il faut configurer Azure Scheduler pour atteindre les API personnalisées, ou activer les fonctionnalités WebJobs.

Pour en savoir plus sur les avantages d’App Service, consultez la rubrique [Mobile Services et App Service].

## <a name="app-settings"></a>Paramètres d’application
Mobile Services requiert que plusieurs paramètres d’application soient disponibles dans l’environnement. Cette section vous guide pour ajouter chacun d’eux. Pour définir les paramètres d’application sur votre application web, connectez-vous tout d’abord au [portail de gestion Azure en version préliminaire]. Accédez à l’application API, mobile ou web que vous voulez utiliser et sélectionnez « Paramètres », puis « Paramètres d’application ». Faites défiler jusqu’à la section intitulée « Paramètres d’application ». C’est ici que vous pouvez définir les paires clé-valeur requises.
 
**MS\_MobileServiceName** doit être défini sur le nom de votre application. Par exemple, si vous devez exécuter dans une application avec l’URL contoso.azurewebsites.net, « contoso » sera la valeur correcte.
 
**MS\_MobileServiceDomainSuffix** doit être défini sur le nom de votre application web. Par exemple, si vous devez exécuter dans une application avec l’URL contoso.azurewebsites.net, « azurewebsites.net » sera la valeur correcte.
 
**MS\_ApplicationKey** peut accepter n’importe quelle valeur, mais il doit s’agir de la valeur utilisée par le Kit de développement logiciel (SDK) client. Un GUID est recommandé.
 
**MS\_MasterKey** peut accepter n’importe quelle valeur, mais il doit s’agir de la valeur utilisée par les clients/API d’administration. Un GUID est recommandé.
 
Si vous effectuez un déplacement vers une application Mobile Services existante, la clé principale et la clé d’application sont disponibles sous l’onglet « Configurer » de la section Mobile Services du [portail de gestion Azure]. Sélectionnez l’action « Gérer les clés » en bas et copiez les clés.


## <a name="client-sdk"></a>Modification du Kit de développement logiciel (SDK) client

Dans le projet d’application client, modifiez le constructeur de l’objet client Mobile Services pour accepter l’URL de l’application (par exemple, contoso.azurewebsites.net) et la clé d’application configurées précédemment. La version du Kit de développement logiciel (SDK) client doit être une version Mobile Services et ne doit **pas** être mise à niveau. Pour les clients iOS et Android, utilisez les versions 2.x et pour Windows/Xamarin, utilisez 1.3.2. Les clients JavaScript doivent utiliser 1.2.7.

## <a name="data"></a>Activation des fonctionnalités de données

Pour utiliser les classes Entity Framework par défaut dans Mobile Services, deux autres paramètres d’application sont nécessaires.
 
Les chaînes de connexion sont stockées dans la section « Chaînes de connexion » du panneau Paramètres d’application, juste en-dessous de la section « Paramètres d’application ». La chaîne de connexion pour votre base de données doit être définie sous la clé **MS\_TableConnectionString**. En vue d’un déplacement vers une application Mobile Services existante, consultez la section « Chaînes de connexion » de l’onglet Configurer du portail Mobile Services. Cliquez sur « Afficher les chaînes de connexion » et copiez la valeur.
 
Par défaut, le schéma à utiliser est **MS\_MobileServiceName**, mais cela peut être remplacé par le paramètre **MS\_TableSchema**. Sous « Paramètres d’application », définissez **MS\_TableSchema** sur le nom du schéma à utiliser. Si vous effectuez un déplacement vers une application Mobile Services existante, un schéma a déjà été créé à l’aide d’Entity Framework. Il s’agit du nom du service Mobile Services, et non de l’instance App Service qui hébergera le code.

## <a name="push"></a>Activation des fonctionnalités Push

Pour que les fonctionnalités Push fonctionnent, l’application web doit également connaître certaines informations sur le hub de notification.
 
Sous « Chaînes de connexion », attribuez à **MS\_NotificationHubConnectionString** la chaîne de connexion DefaultFullSharedAccessSignature pour le hub de notification. En vue d’un déplacement vers une application Mobile Services existante, consultez la section « Chaînes de connexion » de l’onglet Configurer du portail Mobile Services. Cliquez sur « Afficher les chaînes de connexion » et copiez la valeur.

Le paramètre d’application **MS\_NotificationHubName** doit avoir pour valeur le nom du hub. Lorsque vous déplacez une application Mobile Services existante, cette valeur est disponible sous l’onglet des fonctionnalités Push du portail Mobile Services. Les autres champs de cet onglet sont liés au hub lui-même et n’ont pas à être copiés.
 
## <a name="auth"></a>Activation des fonctionnalités d’authentification

Les fonctionnalités d’identité ont également des paramètres d’application prérequis pour les fournisseurs individuels. Si vous effectuez un déplacement à partir d’une application Mobile Services existante, chaque champ de l’onglet Identité du portail Mobile Services correspond à un paramètre d’application.
 
Compte Microsoft : * **MS\_MicrosoftClientID** * **MS\_MicrosoftClientSecret** * **MS\_MicrosoftPackageSID**
 
Facebook : * **MS\_FacebookAppID** * **MS\_FacebookAppSecret**
 
Twitter : * **MS\_TwitterConsumerKey** * **MS\_TwitterConsumerSecret**
 
Google : * **MS\_GoogleClientID** * **MS\_GoogleClientSecret**
 
AAD : * **MS\_AadClientID** * **MS\_AadTenants** - Remarque : **MS\_AadTenants** est stocké sous forme de liste de domaines de clients séparés par des virgules (le champ « Client autorisés » du portail Mobile Services).

## <a name="publish"></a>Publication du projet Mobile Services

1. Dans le portail en version préliminaire, accédez à votre application et sélectionnez « Obtenir le profil de publication » dans la barre de commandes. Enregistrez le profil téléchargé sur l’ordinateur local.
2. Dans Visual Studio, cliquez avec le bouton droit sur le projet de serveur Mobile Services, puis sélectionnez « Publier ». Sous l’onglet Profil, sélectionnez « Importer » et recherchez le profil téléchargé.
3. Cliquez sur Publier pour déployer le code vers App Service.

Les journaux sont gérés via les fonctionnalités de journalisation standard d’App Service. Pour en savoir plus sur la journalisation, consultez [Activer la journalisation des diagnostics dans Azure App Service].

<!-- URLs. -->

[portail de gestion Azure en version préliminaire]: https://portal.azure.com/
[portail de gestion Azure]: https://manage.windowsazure.com/
[Activer la journalisation des diagnostics dans Azure App Service]: web-sites-enable-diagnostic-log.md
[tarification App Service]: https://azure.microsoft.com/fr-FR/pricing/details/app-service/
[environnement App Service]: app-service-app-service-environment-intro.md
[Mobile Services et App Service]: app-service-mobile-value-prop-migration-from-mobile-services-preview.md
[migrer un service mobile vers une application mobile sur App Service]: app-service-mobile-dotnet-backend-migrating-from-mobile-services-preview.md

<!---HONumber=Nov15_HO1-->