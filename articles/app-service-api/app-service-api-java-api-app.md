<properties
	pageTitle="Créer et déployer une application API Java dans Azure App Service"
	description="Découvrez comment créer un package d’application API Java et le déployer sur Azure App Service."
	services="app-service\api"
	documentationCenter="java"
	authors="pkefal"
	manager="mohisri" 
	editor="jimbe"/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="java"
	ms.topic="get-started-article"
	ms.date="08/11/2015"
	ms.author="pakefali"/>

# Créer et déployer une application API Java dans Azure App Service

> [AZURE.SELECTOR]
- [.NET - Visual Studio 2015](app-service-dotnet-create-api-app.md)
- [.NET - Visual Studio Code](app-service-create-aspnet-api-app-using-vscode.md)
- [Node.js](app-service-api-nodejs-api-app.md)
- [Java](app-service-api-java-api-app.md)

Ce didacticiel explique comment créer une application Java et comment la déployer dans des applications API Azure App Service en utilisant [Git](http://git-scm.com). Les instructions de ce didacticiel s’appliquent à tous les systèmes d’exploitation pouvant exécuter Java. Ce didacticiel utilise également [Gradle](https://gradle.org) pour activer l’automatisation du build et la résolution de dépendance de packages pour l’application Java. Enfin, [RESTEasy](http://resteasy.jboss.org/) est utilisé pour créer le Service RESTful, qui implémente complètement les spécifications [JaxRS](https://jax-rs-spec.java.net/).

Voici une capture d'écran de l'application terminée :

![][sample-api-app-page]

## Créer une application API dans le portail Azure

> [AZURE.NOTE]Pour effectuer ce didacticiel, vous avez besoin d’un compte Microsoft Azure. Si vous n'avez pas de compte, vous pouvez [activer les avantages de votre abonnement MSDN](/pricing/member-offers/msdn-benefits-details/) ou [obtenir une évaluation gratuite](/pricing/free-trial/). Vous pouvez également essayer gratuitement des [exemples d'applications App Service](http://tryappservice.azure.com).

1. Connectez-vous à la [version préliminaire du portail Azure](https://portal.azure.com).

2. Cliquez sur **NOUVEAU** dans la partie inférieure gauche du portail.

3. Cliquez sur **Web + Mobile > Application API**.

	![][portal-quick-create]

4. Entrez une valeur pour **Nom**, par exemple JavaAPIApp.

5. Sélectionnez un plan App Service ou créez-en un. Si vous créez un plan, sélectionnez le niveau de tarification, l’emplacement et d’autres options.

	![][portal-create-api]

6. Cliquez sur **Create**.

	![][api-app-blade]

	Si vous avez laissé la case **Ajouter au tableau d’accueil** cochée, le portail ouvre automatiquement le panneau de votre application API après sa création. Si vous avez décoché la case, cliquez sur **Notifications** dans la page d'accueil du portail pour voir l'état de la création de l'application API, puis cliquez sur la notification pour accéder au panneau de la nouvelle application API.

7. Cliquez sur **Paramètres > Paramètres de l'application**.

9. Définissez le niveau d'accès à **Public (anonyme)**.

11. Cliquez sur **Enregistrer**.

	![][set-api-app-access-level]

## Activer la publication Git pour la nouvelle application API

[Git](http://git-scm.com) est un système de contrôle de version distribué permettant de déployer votre site web Azure. Vous allez stocker le code que vous écrivez pour votre application API dans un référentiel Git local, et vous allez déployer votre code dans Azure par transmission de type push vers un référentiel distant. Cette méthode de déploiement est une fonctionnalité des applications web App Service que vous pouvez utiliser dans une application API, car les applications API sont basées sur des applications web : une application API dans Azure App Service est une application web avec des fonctionnalités supplémentaires pour l'hébergement de services web.

Dans le portail, vous gérez les fonctionnalités spécifiques aux applications API dans le panneau **Application API**, et vous gérez les fonctionnalités qui sont partagées avec des applications web dans le panneau **Hôte d'application API**. Dans cette section, vous accédez au panneau **Hôte d'application API** pour configurer la fonctionnalité de déploiement Git.

1. Dans le volet Application API, cliquez sur **Hôte d'application API**.

	![][api-app-host]

2. Recherchez la section **Déploiement** du panneau **Application API** et cliquez sur **Configurer le déploiement continu**. Il peut être nécessaire de faire défiler l'écran vers le bas pour visualiser cette partie du panneau.

	![][deployment-part]

3. Cliquez sur **Choisir la source > Référentiel Git local**.

5. Cliquez sur **OK**.

	![][setup-git-publishing]

6. Si vous n'avez pas précédemment configuré les informations d'identification de déploiement pour la publication d'une application API ou d'une autre application App Service, configurez-les maintenant :

	* Cliquez sur **Définir les informations d'identification de déploiement**.

	* Créez un nom d'utilisateur et un mot de passe.

	* Cliquez sur **Enregistrer**.

	![][deployment-credentials]

1. Dans le panneau **Hôte d'application API**, cliquez sur **Paramètres > Propriétés**. L'URL du référentiel Git distant vers lequel vous allez déployer se trouve sous « URL GIT ».

2. Copiez l'URL, qui sera à utiliser ultérieurement dans le didacticiel.

	![][git-url]

## Activer Java runtime sur la nouvelle application API

Pour que l’application API parvienne à héberger une application Java, il nous faut activer Java runtime et choisir un serveur d’applications. Le portail fournit un moyen simple pour cela. Nous allons activer Java 7 et Jetty pour héberger notre application.

1. Dans le volet Application API, cliquez sur **Hôte d'application API**.

	![][api-app-host]

2. Cliquez sur **Paramètres > Paramètres de l’application**. À présent, activez Java et sélectionnez Jetty comme serveur d’applications. Cliquez sur **Enregistrer**.

	![][api-app-enable-java]

Cette procédure **active Java runtime** sur votre application API et crée un dossier **WebApp/** à la racine de votre site. Ce dossier contient tous les fichiers .war de vos applications.

## Télécharger et examiner le code d’une application API Java

Dans cette section, vous allez télécharger et examiner le code fourni dans le cadre de l’exemple JavaAPIApp.

1. Téléchargez le code dans [ce référentiel GitHub](http://go.microsoft.com/fwlink/?LinkId=571009). Vous pouvez cloner le référentiel ou bien cliquer sur **Télécharger le fichier .zip** pour le télécharger sous forme de fichier .zip. Si vous téléchargez le fichier .zip, décompressez-le sur votre disque local.

2. Accédez au dossier où vous avez décompressé l’exemple, puis accédez au dossier `build\libs`.

	![][api-app-folder-browse]

3. Ouvrez le fichier **apiapp.json** dans un éditeur de texte et examinez son contenu.

	![][apiapp-json]

	Azure App Service a deux conditions préalables pour pouvoir reconnaître une application Java en tant qu’application API :

	+ Un fichier nommé *apiapp.json* doit être présent dans le répertoire racine de l'application.
	+ Un point de terminaison de métadonnées Swagger 2.0 doit être exposé par l'application. L'URL de ce point de terminaison est spécifiée dans le fichier *apiapp.json*.

	Remarquez la propriété **apiDefinition**. Le chemin d'accès pour cette URL est relatif à l'URL de votre API et il pointe vers le point de terminaison Swagger 2.0. Azure App Service utilise cette propriété pour découvrir la définition de votre API et pour activer plusieurs fonctionnalités de l'application API App Service.

4. Accédez à `src\main\java\com\microsoft\trysamples\javaapiapp`, ouvrez le fichier **App.java** et examinez le code.

	![][app-java]

	Le code utilise le package Swagger pour JaxRS afin de créer le point de terminaison Swagger 2.0.

		beanConfig.setVersion("1.0.0");
		beanConfig.setBasePath("/JavaAPIApp/api");
		beanConfig.setHost(websitehostname);
		beanConfig.setResourcePackage("com.microsoft.trysamples.javaapiapp");
		beanConfig.setSchemes(new String[]{"http", "https"});
		beanConfig.setScan(true);

	La méthode `setVersion` définit la version de l’API dans les métadonnées servies par Swagger.

	La méthode `setBasePath` définit le chemin d’accès de base que Swagger utilise pour générer des métadonnées. Cette URL est relative au chemin d’accès de base de votre application API.

	La méthode `setHost` définit l’hôte sur lequel l’API écoute. Dans ce cas, nous utilisons soit la variable `websitehostname` que dont nous avons affectée quelques lignes auparavant pour définir de façon dynamique sur `localhost` lorsqu’il est exécuté localement ou le nom d’hôte de l’application API lorsque l’application s’exécute dans Azure App Service.

	La méthode `setResourcePackage` définit le package que Swagger analysera et inclura dans le fichier Swagger.json, lequel contient les métadonnées de l’API.

	La méthode `setSchemes` définit les schémas pris en charge.

	La méthode `setScan` permet à Swagger de générer la documentation de l’application.

	Il existe d’autres méthodes qui personnalisent la sortie de Swagger lors de l’utilisation de RESTEasy. Vous les trouverez sur la [page Wiki](https://github.com/swagger-api/swagger-core/wiki/Swagger-Core-RESTEasy-2.X-Project-Setup-1.5#using-swaggers-beanconfig) de Swagger.

	> [AZURE.NOTE]Le fichier de métadonnées Swagger est disponible dans `/JavaAPIApp/api/swagger.json`.

## Exécuter le code de l'application API localement

Dans cette section, vous exécutez l'application localement pour vérifier qu'elle fonctionne, avant de la déployer.

1. Accédez au dossier où vous avez téléchargé l'exemple.

2. Ouvrez une invite de ligne de commande et entrez la commande suivante :

		gradlew.bat

3. Lorsque la commande est terminée, entrez la commande suivante :

		gradlew.bat jettyRunWar

	La sortie de la fenêtre de ligne de commande affiche :

		17:25:49 INFO  JavaAPIApp runs at:
		17:25:49 INFO    http://localhost:8080/JavaAPIApp

5. Accédez avec votre navigateur à `http://localhost:8080/JavaAPIApp/`

	Vous voyez la page suivante :

	![][sample-api-app-page]

6. Pour afficher le fichier Swagger.json, accédez à `http://localhost:8080/JavaAPIApp/api/Swagger.json`.

## Publier le code de votre application API dans Azure App Service

Dans cette section, vous créez un référentiel Git local et vous effectuez une transmission de type push depuis ce référentiel vers Azure pour déployer votre exemple d'application dans l'application API qui s'exécute dans Azure App Service.

1. Si Git n'est pas installé, installez-le à partir de [la page de téléchargement de Git](http://git-scm.com/download).

1. Depuis la ligne de commande, accédez au répertoire de l’exemple d’application, puis à `build\libs` et entrez les commandes suivantes pour initialiser un référentiel Git local.

		git init


2. Entrez les commandes suivantes pour ajouter des fichiers au référentiel :

		git add .
		git commit -m "Initial commit of the API App"

3. Créez une référence distante pour envoyer les mises à jour vers l'application web (l'hôte de l'application API) que vous avez créée précédemment, en utilisant l'URL Git que vous avez copiée plus tôt :

		git remote add azure [URL for remote repository]

4. Envoyez vos modifications dans Azure en entrant la commande suivante :

		git push azure master

	Vous êtes invité à entrer le mot de passe que vous avez créé précédemment.

	La sortie de cette commande se termine par un message indiquant que le déploiement a réussi :

		remote: Deployment successful.
		To https://user@testsite.scm.azurewebsites.net/testsite.git
	 	* [new branch]      master -> master

## Afficher la définition d’API dans le portail Azure

Maintenant que vous avez déployé une API dans votre application API, vous pouvez voir la définition d’API dans le portail Azure. Vous commencerez par redémarrer la *passerelle*, ce qui permet à Azure de reconnaître que la définition d'API d'une application API a été modifiée. La passerelle est une application web qui gère l'administration et les autorisations des API pour les applications API d'un groupe de ressources.

6. Dans le portail Azure, accédez au panneau **Application API** pour l’application API que vous avez créée précédemment, puis cliquez sur le lien **Passerelle**.

	![][click-gateway]

7. Dans le panneau **Passerelle**, cliquez sur **Redémarrer**. Vous pouvez maintenant fermer ce panneau.

	![][restart-gateway]

8. Dans le panneau **Application API**, cliquez sur **Définition d'API**.

	![][api-definition-click]

	Le panneau **Définition d’API** montre une méthode Get.

	![][api-definition-blade]

## Exécuter l'exemple d'application dans Azure

Dans le portail Azure, accédez au panneau **Hôte d’application API** pour votre application API, puis cliquez sur **Parcourir**.

![][browse-api-app-page]

Le navigateur affiche la page d'accueil que vous avez vue précédemment quand vous avez exécuté l'exemple d'application localement.

## Étapes suivantes

Vous avez déployé dans Azure une application Web Java qui utilise un backend d’application API vers Azure. Pour plus d’informations sur l’utilisation de Java dans Azure, consultez le [Centre de développement Java](/develop/java/).

Vous pouvez essayer cet exemple d'application API sur [TryApp Service](http://tryappservice.azure.com)

[portal-quick-create]: ./media/app-service-api-java-api-app/portal-quick-create.png
[portal-create-api]: ./media/app-service-api-java-api-app/portal-create-api.png
[api-app-blade]: ./media/app-service-api-java-api-app/api-app-blade.png
[api-app-folder-browse]: ./media/app-service-api-java-api-app/api-app-folder-browse.png
[api-app-host]: ./media/app-service-api-java-api-app/api-app-host.png
[deployment-part]: ./media/app-service-api-java-api-app/continuous-deployment.png
[set-api-app-access-level]: ./media/app-service-api-java-api-app/set-api-app-access.png
[setup-git-publishing]: ./media/app-service-api-java-api-app/local-git-repo.png
[deployment-credentials]: ./media/app-service-api-java-api-app/deployment-credentials.png
[git-url]: ./media/app-service-api-java-api-app/git-url.png
[apiapp-json]: ./media/app-service-api-java-api-app/apiapp-json.png
[app-java]: ./media/app-service-api-java-api-app/app-java.png
[sample-api-app-page]: ./media/app-service-api-java-api-app/sample-api-app-page.png
[browse-api-app-page]: ./media/app-service-api-java-api-app/browse-api-app-page.png
[api-app-enable-java]: ./media/app-service-api-java-api-app/api-app-enable-java.png
[click-gateway]: ./media/app-service-api-java-api-app/clickgateway.png
[restart-gateway]: ./media/app-service-api-java-api-app/gatewayrestart.png
[api-definition-click]: ./media/app-service-api-java-api-app/apidef.png
[api-definition-blade]: ./media/app-service-api-java-api-app/apidefblade.png
 

<!---HONumber=Sept15_HO4-->