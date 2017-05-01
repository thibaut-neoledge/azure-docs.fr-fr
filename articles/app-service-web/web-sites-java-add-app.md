---
title: Ajouter une application Java dans Azure App Service Web Apps
description: "Ce didacticiel vous montre comment ajouter une page ou une application à votre instance d’Azure App Service Web Apps déjà configurée pour utiliser Java."
services: app-service\web
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 9b46528b-e2d0-4f26-b8d7-af94bd8c31ef
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: bc23bf745f0e212de8ea652c8e57e4797013e3cc
ms.lasthandoff: 04/06/2017


---
# <a name="add-a-java-application-to-azure-app-service-web-apps"></a>Ajouter une application Java dans Azure App Service Web Apps
Une fois que vous avez initialisé votre application web Java dans [Azure App Service][Azure App Service] comme indiqué dans [Créer une application web Java dans Azure App Service](web-sites-java-get-started.md), vous pouvez télécharger votre application en plaçant votre fichier WAR dans le dossier **webapps**.

Le chemin d’accès au dossier **webapps** varie en fonction de la configuration de votre instance Web Apps.

* Si vous configurez votre application web à l’aide de la Place de marché Azure, le chemin d’accès au dossier **webapps** se présente sous la forme **d:\home\site\wwwroot\bin\serveur\_applications\webapps**, où **serveur\_applications** est le nom du serveur d’applications de votre instance Web Apps. 
* Si vous configurez votre application web à l’aide de l’interface utilisateur d’Azure, le chemin d’accès au dossier **webapps** se présente sous la forme **d:\home\site\wwwroot\webapps**. 

Notez que vous pouvez utiliser le contrôle de code source pour charger votre application ou vos pages web, y compris dans des [scénarios d’intégration continue](app-service-continuous-deployment.md). Le protocole FTP permet également de télécharger votre application ou des pages web. Pour plus d’informations sur le déploiement de vos applications via FTP, voir [Déploiement de votre application dans Azure App Service].

Remarque pour les applications web Tomcat : une fois que vous avez téléchargé votre fichier WAR dans le dossier **webapps** , le serveur d’applications Tomcat détecte que vous l’avez ajouté et le charge automatiquement. Notez que si vous copiez des fichiers (autres que des fichiers WAR) dans le répertoire ROOT, vous devez redémarrer le serveur d'applications avant d'utiliser ces fichiers. La fonctionnalité de chargement automatique des applications web Java Tomcat exécutées sur Azure repose sur l’ajout d’un fichier WAR ou de nouveaux fichiers ou répertoires dans le dossier **webapps** . 

<a name="see-also"></a>

## <a name="see-also"></a>Voir aussi
Pour plus d’informations sur l’utilisation d’Azure avec Java, consultez le [Centre de développement Java pour Azure].

[application-insights-app-insights-java-get-started](../application-insights/app-insights-java-get-started.md)

<!-- URL List -->

[Centre de développement Java pour Azure]: https://azure.microsoft.com/develop/java/
[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Déploiement de votre application dans Azure App Service]: ./web-sites-deploy.md

