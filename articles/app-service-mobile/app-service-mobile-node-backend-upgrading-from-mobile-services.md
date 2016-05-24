<properties
	pageTitle="Mettre à niveau à partir de Mobile Services vers Azure App Service - Node.js"
	description="Découvrez comment facilement mettre à niveau votre application Mobile Services vers App Service Mobile Apps"
	services="app-service\mobile"
	documentationCenter=""
	authors="adrianhall"
	manager="ggailey"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile"
	ms.devlang="node"
	ms.topic="article"
	ms.date="05/05/2016"
	ms.author="adrianha"/>

# Mettre à niveau votre application .NET Azure Mobile Services existante vers App Service

App Service Mobile représente une nouvelle façon de créer des applications mobiles avec Microsoft Azure. Pour en savoir plus, voir [Que sont les applications Mobile Apps ?]

Cette rubrique décrit comment mettre à niveau une application principale Node.js existante depuis Azure Mobile Services vers une nouvelle application App Service Mobile Apps. Pendant cette mise à niveau, votre application Mobile Services existante peut continuer à fonctionner.

Quand un serveur principal mobile est mis à niveau vers Azure App Service, il a accès à toutes les fonctionnalités App Service et c’est la [tarification App Service] qui est appliquée, et non celle de Mobile Services.

## Migration et mise à niveau

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

>[AZURE.TIP] Il est recommandé d’[effectuer une migration](app-service-mobile-migrating-from-mobile-services.md) avant une mise à niveau. Ainsi, vous pouvez placer les deux versions de votre application sur le même plan App Service sans générer de frais supplémentaires.

### Améliorations du Kit de développement logiciel (SDK) serveur Node.js Mobile Apps

La mise à niveau vers le nouveau [Kit de développement logiciel (SDK) Mobile Apps](https://www.npmjs.com/package/azure-mobile-apps) offre de nombreuses améliorations, notamment :

- Basé sur l’[infrastructure Express](http://expressjs.com/en/index.html), le nouveau Kit de développement logiciel (SDK) Node est léger et conçu pour s’adapter aux nouvelles versions Node au fur et à mesure de leur sortie. Vous pouvez personnaliser le comportement de l’application avec l’intergiciel Express.

- Les améliorations des performances sont significatives par rapport au Kit de développement logiciel (SDK) Mobile Services.

- Vous pouvez désormais héberger un site web avec votre serveur principal mobile. De même, il est facile d’ajouter le Kit de développement logiciel (SDK) Azure Mobile à n’importe quelle application v4 existante.

- Conçu pour le développement multiplateforme et local, le Kit de développement logiciel (SDK) Mobile Apps peut être développé et exécuté localement sur les plateformes Windows, Linux et OSX. Il est désormais facile d’utiliser des techniques de développement Node courantes comme l’exécution de tests [Mocha](https://mochajs.org/) avant le déploiement.

- Vous pouvez utiliser Redis avec les modules natifs comme [hiredis](https://www.npmjs.com/package/hiredis). Dans la mesure où App Service installe vos packages npm, il est inutile d’inclure lors du déploiement des fichiers binaires dans votre package.

## <a name="overview"></a>Présentation de la mise à niveau de base

Contrairement au Kit de développement logiciel (SDK) .NET Mobile Apps, la mise à niveau d’un serveur principal Node de Mobile Services vers Mobile Apps n’est pas aussi simple que l’échange de packages. Vous possédez maintenant votre pile de l’application dans son intégralité (elle n’est plus contrôlée par Azure) et vous devez donc créer une application Express de base pour héberger votre serveur principal mobile. En ce qui concerne la table et les contrôleurs d’API, les concepts sont similaires, mais vous devez dorénavant exporter les objets de table et les API de fonction ont été légèrement modifiées. Cet article détaille les stratégies de mise à niveau de base, mais avant de procéder à la migration, consultez la rubrique [Serveur principal Node : procédure](app-service-mobile-node-backend-how-to-use-server-sdk.md).

>[AZURE.TIP] Consultez le reste de cette rubrique avant de commencer une mise à niveau. Prenez note de toutes les fonctionnalités répertoriées ci-dessous que vous utilisez.

Les Kits de développement logiciel (SDK) clients Mobile Services ne sont **pas** compatibles avec le nouveau Kit de développement logiciel (SDK) serveur Mobile Apps. Afin d’assurer la continuité du service pour votre application, vous ne devez pas publier des modifications apportées à un site en train de desservir des clients publiés. Vous devez plutôt créer une application mobile qui sert de doublon. Vous pouvez placer cette application sur le même plan App Service pour éviter d’encourir des frais supplémentaires.

Vous avez alors deux versions de l’application : l’une qui reste la même et délivre les applications publiées et l’autre que vous pouvez mettre à niveau et cibler avec une nouvelle version du client. Vous pouvez déplacer et tester votre code à votre rythme, mais vous devez vous assurer que tous les correctifs de bogues que vous apportez sont appliqués aux deux versions. Une fois que le nombre souhaité d’applications clientes ont effectué la mise à jour vers la dernière version, vous supprimez l’application originale qui a migré si vous le souhaitez. Cela n’entraîne aucun frais supplémentaire si l’application est hébergée sur le même plan App Service que votre application Mobile App.

Le processus de mise à niveau est le suivant :

1. Créez une application mobile.
2. Mettez à jour le projet pour utiliser les nouveaux Kits de développement logiciel (SDK) serveur.
3. Publiez votre projet sur la nouvelle application mobile.
4. Publier une nouvelle version de votre application cliente qui utilise la nouvelle application mobile
5. (Facultatif) Supprimez l’application de service mobile d’origine migrée.

La suppression peut se produire lorsque vous ne voyez pas de trafic sur votre application de service mobile d’origine migrée.

## <a name="mobile-app-version"></a> Démarrage de la mise à niveau
La première étape de la mise à niveau consiste à créer la ressource Mobile Apps qui hébergera la nouvelle version de votre application. Si vous avez déjà migré un service mobile existant, vous voulez créer cette version sur le même plan d’hébergement. Ouvrez le [portail Azure] et accédez à votre application migrée. Notez le plan App Service sur lequel elle s’exécute.

### Création d’une seconde instance d’application
Ensuite, créez la seconde instance d’application. Quand vous êtes invité à sélectionner votre plan App Service ou « plan d’hébergement », choisissez celui de votre application qui a migré.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Vous souhaiterez probablement utiliser les mêmes base de données et hub de notifications que dans Mobile Services. Copiez ces valeurs en ouvrant le [portail Azure] et en accédant à l’application d’origine, puis cliquez sur **Paramètres** > **Paramètres d’application**. Sous **Chaînes de connexion**, copiez `MS_NotificationHubConnectionString` et `MS_TableConnectionString`. Accédez à votre nouveau site de mise à niveau et collez-les en remplaçant les valeurs existantes. Répétez ce processus pour tous les autres paramètres d’application dont votre application a besoin. Si vous n’utilisez pas un service qui a migré, vous pouvez lire des chaînes de connexion et des paramètres d’application sous l’onglet **Configurer** de la section Mobile Services du [portail Azure].

### Créer un serveur principal Mobile App de base avec Node

Chaque serveur principal Node.js Azure App Service Mobile Apps démarre en tant qu’application ExpressJS. Vous pouvez créer une application [Express](http://expressjs.com/en/index.html) de base de la manière suivante :

1. Dans une commande ou une fenêtre PowerShell, créez un répertoire pour votre projet.

        mkdir basicapp

2. Exécutez npm init pour initialiser la structure du package.

        cd basicapp
        npm init

    La commande npm init posera une série de questions pour initialiser le projet. Consultez l’exemple ci-dessous pour voir le résultat obtenu.

    ![La sortie npm init][0]

3. Installez les bibliothèques express et azure-mobile-apps à partir du référentiel npm.

        npm install --save express azure-mobile-apps

4. Créez un fichier app.js pour implémenter le serveur mobile de base.

        var express = require('express'),
            azureMobileApps = require('azure-mobile-apps');

        var app = express(),
            mobile = azureMobileApps();

        // Important all tables in the 'tables' directory
        mobile.tables.import('./tables');
        mobile.api.import('./api');

        // Provide initialization of any tables that are statically defined
        mobile.tables.initialize().then(function () {
           // Add the mobile API so it is accessible as a Web API
           app.use(mobile);

           // Start listening on HTTP
           var port = process.env.PORT || 3000;
           app.listen(port, function () {
               console.log('Now listening on ', port)
           });
        });

Pour plus d’exemples, reportez-vous à notre [référentiel GitHub](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples).

## Mise à jour du projet serveur

Mobile Apps fournit un nouveau [Kit de développement logiciel (SDK) de serveur Mobile App] offrant la plupart des fonctionnalités du runtime Mobile Services. Vous êtes désormais propriétaire du runtime complet et Mobile Apps ne vous impose aucune version Node ou aucune mise à jour de code. Si vous avez suivi les étapes ci-dessus, vous disposez d’une version de base du runtime mobile Node disponible. Vous pouvez maintenant commencer le déplacement des tables et de la logique de l’API de votre Mobile Service vers votre application Mobile App, la personnalisation de la configuration de votre serveur, l’activation des notifications Push, la configuration de l’authentification, etc.

### Configuration de base

Le serveur comporte un grand nombre de paramètres de configuration, mais la variété de valeurs par défaut facilite la prise en main. La plupart des paramètres sont déjà configurés dans le [portail Azure] au moyen des menus **Données**, **Authentification/autorisation** et **Notifications Push** Pour un développement local, si vous souhaitez utiliser des données, l’authentification et les notifications Push, vous devrez peut-être configurer votre environnement de développement local.

Vous pouvez définir votre configuration du serveur à l’aide de variables d’environnement qui peuvent être établies via les paramètres de l’application dans votre serveur principal Mobile App.

Vous pouvez personnaliser davantage le Kit de développement logiciel (SDK) Mobile Apps en passant un [objet de configuration](http://azure.github.io/azure-mobile-apps-node/global.html#configuration) à l’initialiseur ou en [créant un fichier nommé azureMobile.js](app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-config-localdev) à la racine du projet.

### Utilisation des données et des tables

Le Kit de développement logiciel (SDK) est fourni avec un fournisseur de données en mémoire pour permettre une mise en route rapide et facile. Vous devez utiliser une base de données SQL dès le départ pour éviter que le fournisseur en mémoire ne perde toutes les données lors du redémarrage et ne reste pas cohérent sur plusieurs instances.

Pour déplacer votre logique métier de Mobile Services vers Mobile Apps, vous devez tout d’abord créer un fichier avec le nom de votre table (suivi de « .js ») dans le répertoire `./tables`. Vous pouvez voir un exemple complet de table Mobile App sur [GitHub](https://github.com/Azure/azure-mobile-apps-node/blob/master/samples/todo/tables/TodoItem.js). La version la plus simple est la suivante :

    var azureMobileApps = require('azure-mobile-apps');

    // Create a new table definition
    var table = azureMobileApps.table();

    module.exports = table;

Pour démarrer le portage d’une partie de votre logique, pour chacune de vos `<tablename>.<operation>.js`, vous avez besoin d’une fonction pour votre table. Nous allons ajouter une fonction de lecture par exemple.

Dans un Mobile Service avec une table TodoItem et une opération de lecture qui filtre les éléments en fonction de l’ID utilisateur, comme suit :

    function(query, user, request) {
        query.where({ userId: user.userId});
        request.execute();
    }

La fonction que nous ajoutons à la table Azure Mobile Apps ressemblerait à ceci :

    table.read(function (context) {
        context.query.where({ userId: context.user.id });
        return context.execute();
    });

La requête, l’utilisateur et la demande sont combinés dans un contexte. Les champs suivants sont disponibles dans l’objet de contexte :

| Champ | Type | Description |
| :------ | :--------------------- | :---------- |
| query | queryjs/Query | Requête OData analysée |
| id | chaîne ou nombre | ID associé à la demande |
| item | objet | Élément inséré ou supprimé |
| req | express.Request | Objet de demande rapide en cours |
| res | express.Response | Objet de réponse rapide en cours |
| données | données | Fournisseur de données configuré |
| tables | function | Fonction qui accepte un nom de table au format chaîne et renvoie un objet d’accès à la table |
| user | auth/user | Objet utilisateur authentifié |
| results | objet | Résultats de l’exécution |
| push | NotificationHubService | Service Notification Hubs, si celui-ci est configuré |

Pour plus d’informations, consultez la documentation actuelle de l’[API](http://azure.github.io/azure-mobile-apps-node).

### CORS

CORS peut être activé au moyen d’un [paramètre de configuration CORS](http://azure.github.io/azure-mobile-apps-node/global.html#corsConfiguration) dans le Kit de développement logiciel (SDK).

Les principales sources de préoccupation en cas d’utilisation de CORS concernent les en-têtes `eTag` et `Location` qui doivent être autorisés pour que les Kits de développement logiciel (SDK) clients puissent fonctionner correctement.

### Notifications Push

Le Kit de développement logiciel (SDK) Azure Notification Hubs ayant bénéficié de mises à jour importantes depuis Mobile Services, certaines signatures de fonction Notification Hubs peuvent donc être différentes. Dans le cas contraire, la fonctionnalité est semblable à Mobile Services ; le Kit de développement logiciel (SDK) Mobile Azure configure une instance Notifications Hubs si le paramètre d’application pour Notifications Hubs existe et l’expose sur `context.push`. Un exemple est disponible sur [GitHub](https://github.com/Azure/azure-mobile-apps-node/blob/master/samples/push-on-insert/tables/TodoItem.js), avec la section appropriée indiquée ci-dessous :

    table.insert(function (context) {
        // For details of the Notification Hubs JavaScript SDK,
        // see https://azure.microsoft.com/documentation/articles/notification-hubs-nodejs-how-to-use-notification-hubs/
        logger.silly('Running TodoItem.insert');

        // This push uses a template mechanism, so we need a template/
        var payload = '<toast><visual><binding template="Toast01"><text id="1">INSERT</text></binding></visual></toast>';

        // Execute the insert.  The insert returns the results as a Promise,
        // Do the push as a post-execute action within the promise flow.
        return context.execute()
            .then(function (results) {
                // Only do the push if configured
                if (context.push) {
                    context.push.wns.send(null, payload, 'wns/toast', function (error) {
                        if (error) {
                            logger.error('Error while sending push notification: ', error);
                        } else {
                            logger.silly('Push notification sent successfully!');
                        }
                    });
                }
                // Don't forget to return the results from the context.execute()
                return results;
            })
            .catch(function (error) {
                logger.error('Error while running context.execute: ', error);
            });
    });


### Tâches planifiées
Les tâches planifiées ne sont pas intégrées à Mobile Apps, si bien que toutes les tâches existantes dans votre serveur principal Mobile Service doivent être mises à niveau individuellement. Une option consiste à créer une [tâche web] planifiée sur le site de code Mobile App. Vous pouvez également configurer une API contenant le code de votre tâche et configurer [Azure Scheduler] pour traiter ce point de terminaison au moment prévu.

## <a name="authentication"></a>Considérations relatives à l’authentification

Les composants d’authentification de Mobile Services sont maintenant déplacés vers la fonctionnalité d’authentification/autorisation App Service. Pour en savoir plus sur l’activation de cette fonctionnalité pour votre site, consultez la rubrique [Ajouter l’authentification à votre application mobile](app-service-mobile-ios-get-started-users.md).

Pour certains fournisseurs, comme AAD, Facebook et Google, vous devez être en mesure d’exploiter l’inscription existante à partir de l’application de votre copie. Il vous suffit simplement d’accéder au portail du fournisseur d’identité et d’ajouter une nouvelle URL de redirection à l’inscription. Ensuite, configurez l’authentification/autorisation App Service avec l’ID client et le secret.

### Autorisation des actions de contrôleur et identité de l’utilisateur

Pour limiter l’accès à votre table, vous pouvez le définir au niveau de la table avec `table.access = 'authenticated';`. Vous pouvez voir un exemple complet sur [GitHub](https://github.com/Azure/azure-mobile-apps-node/blob/master/samples/authentication/tables/TodoItem.js).

Vous pouvez accéder aux informations d’identité de l’utilisateur par le biais de la méthode `user.getIdentity` décrite [ici](http://azure.github.io/azure-mobile-apps-node/module-azure-mobile-apps_auth_user.html#~getIdentity).

## <a name="updating-clients"></a>Mise à jour des clients
Une fois que vous avez un serveur principal Mobile App opérationnel, vous pouvez travailler sur une nouvelle version de votre application cliente qui la consomme. Mobile Apps inclut également une nouvelle version des Kits de développement logiciel (SDK) clients, et comme pour la mise à niveau serveur ci-dessus, vous devez supprimer toutes les références aux Kits de développement logiciel (SDK) Mobile Services avant d’installer les versions Mobile Apps.

L’une des principales modifications entre les versions a trait aux constructeurs qui n’exigent plus de clé d’application. Désormais, vous passez simplement l’URL de votre application mobile. Par exemple, sur les clients .NET, le constructeur `MobileServiceClient` est désormais :

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net", // URL of the Mobile App
        );

Pour en savoir plus sur l’installation des nouveaux Kits de développement logiciel (SDK) et l’utilisation de la nouvelle structure, cliquez sur les liens ci-dessous :

- [iOS version 3.0.0 ou ultérieure](app-service-mobile-ios-how-to-use-client-library.md)
- [.NET (Windows/Xamarin) version 2.0.0 ou ultérieure](app-service-mobile-dotnet-how-to-use-client-library.md)

Si votre application emploie des notifications Push, notez les instructions d’inscription spécifiques de chaque plateforme, car elles ont également fait l’objet de quelques modifications.

Une fois la nouvelle version cliente prête, essayez-la par rapport à votre projet de serveur mis à niveau. Après avoir vérifié qu’elle fonctionne, vous pouvez publier une nouvelle version de votre application pour vos clients. Enfin, une fois que vos clients ont eu l’occasion de recevoir ces mises à jour, vous pouvez supprimer la version Mobile Services de votre application. À ce stade, vous avez entièrement mis à niveau votre application vers App Service Mobile Apps avec le tout dernier SDK serveur Mobile Apps.

<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png

<!-- URLs. -->

[Azure portal]: https://portal.azure.com/
[Azure classic portal]: https://manage.windowsazure.com/
[Que sont les applications Mobile Apps ?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /fr-FR/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Kit de développement logiciel (SDK) de serveur Mobile App]: https://www.npmjs.com/package/azure-mobile-apps
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /fr-FR/documentation/services/scheduler/
[tâche web]: ../app-service-web/websites-webjobs-resources.md
[How to use the .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[tarification App Service]: https://azure.microsoft.com/pricing/details/app-service/
[.NET server SDK overview]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md

[portail Azure]: https://portal.azure.com/
[OData]: http://www.odata.org
[Promise]: https://developer.mozilla.org/fr-FR/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[todo sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[samples directory on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js Tools 1.1 for Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js package]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/fr-FR/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS Middleware]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston

<!---HONumber=AcomDC_0511_2016-->