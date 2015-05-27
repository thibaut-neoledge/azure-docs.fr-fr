<properties 
	pageTitle="Ajouter une application Java dans Azure App Service Web Apps" 
	description="Ce didacticiel vous montre comment ajouter une page ou une application à votre instance d’Azure App Service Web Apps déjà configurée pour utiliser Java." 
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
	ms.topic="article" 
	ms.date="04/21/2015" 
	ms.author="robmcm"/>

# Ajouter une application Java dans Azure App Service Web Apps

Une fois que vous avez initialisé votre application web Java dans [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) comme indiqué dans [Créer une application web Java dans Azure App Service](web-sites-java-get-started.md), vous pouvez télécharger votre application en plaçant votre fichier WAR dans le dossier **webapps**.

Le chemin d’accès au dossier **webapps** varie en fonction de la configuration de votre instance Web Apps.

- Si vous configurez votre application web à l’aide d’Azure Marketplace, le chemin d’accès au dossier **webapps** se présente sous la forme **d:\\home\\site\\wwwroot\\bin\\serveur_applications\\webapps**, où **serveur_applications** est le nom du serveur d’applications de votre instance Web Apps. 
- Si vous configurez votre application web à l’aide de l’interface utilisateur d’Azure, le chemin d’accès au dossier **webapps** se présente sous la forme **d:\\home\\site\\wwwroot\\webapps**. 

Notez que vous pouvez utiliser le contrôle de code source pour télécharger votre application ou vos pages Web, y compris dans des scénarios d’intégration continue. Les instructions d’utilisation du contrôle de code source avec votre application web sont disponibles dans la page [Déploiement continu à l’aide de Git dans Azure App Service](web-sites-publish-source-control.md). Le protocole FTP permet également de télécharger votre application ou des pages web.

Remarque pour les applications web Tomcat : une fois que vous avez téléchargé votre fichier WAR dans le dossier **webapps**, le serveur d’applications Tomcat détecte que vous l’avez ajouté et le charge automatiquement. Notez que si vous copiez des fichiers (autres que des fichiers WAR) dans le répertoire ROOT, vous devez redémarrer le serveur d'applications avant d'utiliser ces fichiers. La fonctionnalité de chargement automatique des applications web Java Tomcat exécutées sur Azure repose sur l’ajout d’un fichier WAR ou de nouveaux fichiers ou répertoires dans le dossier **webapps**.

[AZURE.INCLUDE [app-service-web-whats-changed](../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../includes/app-service-web-try-app-service.md)]
 

<!--HONumber=54-->