<properties urlDisplayName="Add an application to your Java website" pageTitle="Ajout d'une application à votre site web Java" metaKeywords="" description="This tutorial shows you how to add a page or application to your Java website on Microsoft Azure." metaCanonical="" services="web-sites" documentationCenter="Java" title="Add an application to your Java website" videoId="" scriptId="" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="09/25/2014" ms.author="robmcm" />

# Ajout d'une application à votre site web Java sur Azure

Une fois que vous avez initialisé votre site web Java comme indiqué dans [Prise en main de Sites Web Microsoft Azure et de Java](../web-sites-java-get-started), vous pouvez télécharger votre application en plaçant votre fichier WAR dans le dossier **webapps**.

Le chemin d'accès au dossier **webapps** varie en fonction de la configuration de votre site web.

- Si vous configurez votre site web à l'aide de la galerie d'applications Azure, le chemin d'accès du dossier **webapps** se présente sous la forme **d:\home\site\wwwroot\bin\application\_server\webapps**, où **application\_server** est le nom du serveur d'applications de votre site web. 
- Si vous configurez votre site web à l'aide de l'interface utilisateur de configuration Azure, le chemin d'accès du dossier **webapps** se présente sous la forme **d:\home\site\wwwroot\webapps**. 

Notez que vous pouvez utiliser le contrôle de code source pour télécharger votre application ou vos pages web, y compris dans des scénarios d'intégration continue. Pour obtenir les instructions d'utilisation du contrôle de code source avec votre site web, consultez [Publication à partir du contrôle de code source dans Azure Web Sites](../web-sites-publish-source-control). Le protocole FTP permet également de télécharger votre application ou des pages web.

Remarque pour les sites web Tomcat : une fois que vous avez téléchargé votre fichier WAR vers le dossier **webapps**, le serveur d'applications Tomcat détecte que vous l'avez ajouté et le charge automatiquement. Notez que si vous copiez des fichiers (autres que des fichiers WAR) dans le répertoire ROOT, vous devez redémarrer le serveur d'applications avant d'utiliser ces fichiers. La fonctionnalité de chargement automatique des sites web Java Tomcat exécutés sur Azure est basée sur l'ajout d'un fichier WAR, ou sur les fichiers ou répertoires ajoutés au dossier **webapps**. 
