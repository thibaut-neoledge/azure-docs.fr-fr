<properties
	pageTitle="Création d’une application web Java dans Azure App Service | Microsoft Azure"
	description="Ce didacticiel vous explique comment déployer une application web Java dans Azure App Service."
	services="app-service\web"
	documentationCenter="java"
	authors="rmcmurray"
	manager="wpickett"
	editor="jimbe"/>
<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="hero-article"
	ms.date="08/31/2015"
	ms.author="robmcm"/>

# Créer une application web Java dans Azure App Service

Ce didacticiel vous explique comment créer une application web sur Microsoft Azure à l’aide de Java, soit par le biais d’Azure Marketplace, soit via l’interface utilisateur de configuration dans la [fonctionnalité Web Apps d’Azure App Service][].

Si vous ne souhaitez pas utiliser l’une de ces techniques (par exemple, si vous préférez personnaliser votre conteneur d’applications), consultez la page [Charger une application web Java personnalisée dans Azure](web-sites-java-custom-upload.md).

> [AZURE.NOTE]Pour effectuer ce didacticiel, vous avez besoin d’un compte Microsoft Azure. Si vous ne possédez pas de compte, vous pouvez [activer les avantages de votre abonnement MSDN][] ou [obtenir une évaluation gratuite][].

Si vous souhaitez commencer à utiliser Azure App Service avant d’ouvrir un compte Azure, accédez à [Essayer App Service][]. Là, vous pouvez créer immédiatement une application de départ temporaire dans App Service. Aucune carte de crédit n’est requise ni aucun engagement.

## Créer une application web Java à l’aide d’Azure Marketplace

Ces informations vous indiquent comment utiliser Azure Marketplace pour sélectionner un conteneur d’applications Java (Apache Tomcat ou Jetty) pour votre application web.

L’exemple suivant illustre l’aspect d’une application web conçue avec Tomcat à partir d’Azure Marketplace :

<!--todo:![Web app using Apache Tomcat](./media/web-sites-java-get-started/tomcat.png)-->

L’exemple suivant illustre l’aspect d’une application web conçue avec Jetty à partir d’Azure Marketplace :

<!--todo:![Web app using Jetty](./media/web-sites-java-get-started/jetty.png)-->

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715).
2. Cliquez sur **Nouveau** dans la partie inférieure gauche de la page.
3. Cliquez sur le panneau **Web et mobilité**.
4. Cliquez sur **Azure Marketplace** en bas du panneau **Web et mobilité**.
5. Cliquez sur **Web**.
6. La partie supérieure de la page **Web** contient une zone de texte de recherche. Dans cette zone, indiquez le serveur d’applications Java souhaité, par exemple **Apache Tomcat** ou **Jetty**.
4. Cliquez sur le serveur d’applications Java souhaité.
5. Cliquez sur **Create**.
6. Spécifiez un nom d'URL
6. Sélectionnez une région. Par exemple, sélectionnez **Ouest des États-Unis**.
7. Cliquez sur **Create**.

Votre application web est créée en quelques instants. Pour visualiser l’application web, dans le panneau **Web Apps** du portail Azure, cliquez sur l’application web, puis sur son URL.

Maintenant que vous avez créé l’application web avec un conteneur d’applications, consultez la section **Étapes suivantes** pour accéder aux informations concernant le chargement de votre application dans l’application web.

## Créer une application web Java avec l’interface utilisateur de configuration Azure

Ces informations vous indiquent comment utiliser l’interface utilisateur de configuration Azure pour sélectionner un conteneur d’applications Java (Apache Tomcat ou Jetty) pour votre application web.

1. Connectez-vous au portail Azure.
2. Cliquez sur **Nouveau** dans la partie inférieure gauche de la page.
3. Cliquez sur le panneau **Web et mobilité**.
4. Cliquez sur **Azure Marketplace** en bas du panneau **Web et mobilité**.
5. Cliquez sur **Web**.
6. Cliquez sur **Application web**.
7. Cliquez sur **Create**.
8. Spécifiez un nom d'URL
9. Sélectionnez une région. Par exemple, sélectionnez **Ouest des États-Unis**.
10. Cliquez sur **Create**.
11. Une fois l’application web créée, cliquez sur **Tous les paramètres**.
12. Cliquez sur **Paramètres de l’application**.
13. Cliquez sur la version Java souhaitée.
14. Les options pour le conteneur Web s'affichent (par exemple Tomcat et Jetty). Sélectionnez le **conteneur web** souhaité.
15. Cliquez sur **Save**.

Votre application web passe sous Java en quelques instants. Pour confirmer qu'il est basé sur Java, cliquez sur son URL. Notez que la page affiche un message indiquant que la nouvelle application web est basée sur Java.

Maintenant que vous avez créé l’application web avec un conteneur d’applications, consultez la section **Étapes suivantes** pour accéder aux informations concernant le chargement de votre application dans l’application web.

## Étapes suivantes

Vous disposez à présent d’un serveur d’applications Java exécuté comme votre application web Java sur Azure. Pour y ajouter votre propre application ou page web, consultez la page [Ajout d’une application à votre site Web Java sur Azure](web-sites-java-add-app.md).

## Changements apportés

* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants][].
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre l’ancien et le nouveau portail, consultez [Références sur la navigation dans le portail Azure][].

<!-- External Links -->
[activer les avantages de votre abonnement MSDN]: http://go.microsoft.com/fwlink/?LinkId=623901
[obtenir une évaluation gratuite]: http://go.microsoft.com/fwlink/?LinkId=623901
[fonctionnalité Web Apps d’Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Essayer App Service]: http://go.microsoft.com/fwlink/?LinkId=523751
[Azure App Service et les services Azure existants]: http://go.microsoft.com/fwlink/?LinkId=529714
[Références sur la navigation dans le portail Azure]: http://go.microsoft.com/fwlink/?LinkId=529715

<!---HONumber=September15_HO1-->