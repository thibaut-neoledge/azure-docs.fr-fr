<properties 
	pageTitle="Ajout d'une application à votre site Web Java" 
	description="Ce didacticiel vous montre comment ajouter une page ou une application sur votre site Web Java dans Microsoft Azure." 
	services="web-sites" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="09/25/2014" 
	ms.author="robmcm"/>

# Ajout d'une application à votre site Web Java sur Azure

Une fois que vous avez initialisé votre site Web Java comme indiqué dans [Prise en main de Microsoft Azure Sites Web et de Java](web-sites-java-get-started.md), vous pouvez télécharger votre application en plaçant votre fichier WAR dans le dossier **Webapps**.

Le chemin d'accès au dossier **Webapps** varie en fonction de la configuration de votre site Web.

- Si vous configurez votre site Web à l'aide de la galerie d'applications Azure, le chemin d'accès du dossier **Webapps** se présente sous la forme **d:\home\site\wwwroot\bin\application\_server\Webapps**, où **application\_server** est le nom du serveur d'applications de votre site Web. 
- Si vous configurez votre site Web à l'aide de l'interface utilisateur de configuration Azure, le chemin d'accès du dossier **Webapps** se présente sous la forme **d:\home\site\wwwroot\Webapps**. 

Notez que vous pouvez utiliser le contrôle de code source pour télécharger votre application ou vos pages Web, y compris dans des scénarios d'intégration continue. Pour obtenir les instructions d'utilisation du contrôle de code source avec votre site Web, consultez [Publication à partir du contrôle de code source dans Azure Web Sites](web-sites-publish-source-control.md). Le protocole FTP permet également de télécharger votre application ou des pages Web.

Remarque pour les sites Web Tomcat : une fois que vous avez téléchargé votre fichier WAR vers le dossier **Webapps**, le serveur d'applications Tomcat détecte que vous l'avez ajouté et le charge automatiquement. Notez que si vous copiez des fichiers (autres que des fichiers WAR) dans le répertoire ROOT, vous devez redémarrer le serveur d'applications avant d'utiliser ces fichiers. La fonctionnalité de chargement automatique des sites Web Java Tomcat exécutés sur Azure est basée sur l'ajout d'un fichier WAR, ou sur les fichiers ou répertoires ajoutés au dossier **Webapps**. 



<!--HONumber=42-->
