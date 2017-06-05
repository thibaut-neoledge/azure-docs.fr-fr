---
title: "Gérer une application web dans Azure App Service"
description: "Liens vers des ressources permettant de gérer une application web dans Azure App Service."
services: app-service\web
documentationcenter: 
author: erikre
manager: erikre
editor: 
ms.assetid: d5e2887a-84f9-4747-a573-867635cb8b39
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: rachelap
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: 08a84ace63465e7c058add69a25687fe46c7ea07
ms.contentlocale: fr-fr
ms.lasthandoff: 05/26/2017


---
# <a name="manage-a-web-app-in-azure-app-service"></a>Gérer une application web dans Azure App Service
Cette rubrique contient des liens vers des ressources permettant de gérer une application web dans [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714). La gestion englobe toutes les tâches nécessaires au bon fonctionnement de votre application web. 

Pendant la durée de vie d’une application web, vous effectuez différentes tâches de gestion, depuis le déploiement initial jusqu’au fonctionnement normal, en passant par la maintenance et les mises à jour.

De nombreuses tâches de gestion d’application web peuvent être effectuées dans le portail Azure.

## <a name="before-you-deploy-your-web-app-to-production"></a>Avant de déployer votre application web en production
### <a name="choose-a-tier"></a>Choisir un niveau
Azure App Service est disponibles en cinq niveaux : Gratuit, Partagé, De base, Standard et Premium. Pour plus d'informations sur les fonctionnalités et la tarification de chaque niveau, consultez les [détails de tarification](https://azure.microsoft.com/pricing/details/app-service/). 

* [plans App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) vous permettent de regrouper plusieurs applications web sous le même niveau.
* Vous avez toujours la possibilité de [changer de niveau](web-sites-scale.md) après avoir créé l’application web.

### <a name="configuration"></a>Configuration
Utilisez le [portail Azure](https://portal.azure.com/) pour définir différentes options de configuration. Pour plus d’informations, consultez la page [Configurer des applications web dans Azure App Service](web-sites-configure.md). Voici une liste de vérification rapide :

* Sélectionnez **Versions exécutables** pour .NET, PHP, Java ou Python, si nécessaire.
* Activez **WebSockets** si votre site web utilise ce protocole. (Ceci inclut les applications qui utilisent [ASP.NET SignalR](http://www.asp.net/signalr) ou [socket.io](web-sites-nodejs-chat-app-socketio.md).)
* Exécutez-vous des tâches Web continues ? Si tel est le cas, activez l'option **Toujours activé**.
* Définissez le **document par défaut**, par exemple index.html.

Outre ces paramètres de configuration de base, vous pouvez configurer ce qui suit :

* **SSL (Secure Socket Layer)** . Pour utiliser le chiffrement SSL avec un nom de domaine personnalisé, vous devez obtenir un certificat SSL et configurer votre application Web pour l’utiliser. Consultez la page [Activer le protocole HTTPS pour une application web dans Azure App Service](app-service-web-tutorial-custom-ssl.md).
* **Nom de domaine personnalisé.** Votre application web a automatiquement un sous-domaine sous azurewebsites.net. Vous pouvez lui associer un nom de domaine personnalisé, par exemple contoso.com. Consultez la page [Configurer un nom de domaine personnalisé dans Azure App Service](app-service-web-tutorial-custom-domain.md).

Configuration spécifique à la langue :

* **PHP**: [configurez PHP dans Azure App Service Web Apps](web-sites-php-configure.md).
* **Python**: [configurez Python dans Azure App Service Web Apps](web-sites-python-configure.md)

## <a name="while-your-web-app-is-running"></a>Pendant l’exécution de votre application web
Lorsque votre application web est en cours d’exécution, vérifiez qu’elle est disponible et qu’elle s’adapte au volume du trafic utilisateur. Vous pouvez également résoudre les erreurs.

### <a name="monitoring"></a>Analyse
* Via le portail Azure, vous pouvez [ajouter des mesures de performances](web-sites-monitor.md) comme l’utilisation du processeur et le nombre de demandes de clients.
* [Mettez votre application web à l’échelle](web-sites-scale.md) en fonction du trafic. Suivant l'option choisie, vous pouvez mettre à l'échelle le nombre de machines virtuelles et/ou la taille des instances de celles-ci. Dans les niveaux Standard et Premium, vous pouvez également configurer la mise à l’échelle automatique, qui fait évoluer votre application automatiquement soit à une planification définie, soit en fonction de la charge.  

### <a name="backups"></a>Sauvegardes
* Configurez les [sauvegardes automatiques](web-sites-backup.md) de votre application web. Pour en savoir plus sur les sauvegardes, regardez [cette vidéo](https://azure.microsoft.com/documentation/videos/azure-websites-automatic-and-easy-backup/).
* Découvrez les options de [récupération de base de données](../sql-database/sql-database-business-continuity.md) dans la base de données Azure SQL.

### <a name="troubleshooting"></a>Résolution de problèmes
* Si une erreur survient, vous pouvez [résoudre les problèmes dans Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug)en utilisant des journaux de diagnostic et le débogage réel dans le cloud. 
* En dehors de Visual Studio, il existe différentes manières de recueillir les journaux de diagnostic. Consultez la page [Activer la journalisation des diagnostics pour les applications web dans Azure App Service](web-sites-enable-diagnostic-log.md).
* Pour les applications Node.js, consultez la page [Débogage d’une application Node.js dans Azure Web Sites](web-sites-nodejs-debug.md).

### <a name="restoring-data"></a>Restauration de données
* [Restaurez](web-sites-restore.md) une application Web qui a été précédemment sauvegardée.

## <a name="when-you-update-your-web-app"></a>Lors de la mise à jour de votre application Web
Si vous n'avez pas activé les sauvegardes automatiques, vous pouvez créer une [sauvegarde manuelle](web-sites-backup.md).

Envisagez d'utiliser le [déploiement intermédiaire](web-sites-staged-publishing.md). Cette option vous permet de publier des mises à jour pour un déploiement intermédiaire qui s'exécute parallèlement à votre déploiement de production. 

Si vous utilisez Visual Studio Team Services, vous pouvez configurer le déploiement continu depuis le contrôle de la source :

* [Utilisation de TFVC (Team Foundation Version Control)](../cloud-services/cloud-services-continuous-delivery-use-vso.md) 
* [Utilisation de Git](../cloud-services/cloud-services-continuous-delivery-use-vso-git.md)

<!-- Anchors. -->

[Before you deploy your site to production]: #before-you-deploy-your-site-to-production
[While your website is running]: #while-your-website-is-running
[When you update your website]: #when-you-update-your-website



