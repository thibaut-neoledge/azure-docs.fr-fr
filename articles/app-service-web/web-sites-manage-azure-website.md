<properties 
	pageTitle="Gérer une application web dans Azure App Service" 
	description="Liens vers des ressources permettant de gérer une application web dans Azure App Service." 
	services="app-service\web" 
	documentationCenter="" 
	authors="erikre" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/31/2015" 
	ms.author="erikre"/>

# Gérer une application web dans Azure App Service

Cette rubrique contient des liens vers des ressources permettant de gérer une application web dans [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714). La gestion englobe toutes les tâches nécessaires au bon fonctionnement de votre application web.

Pendant la durée de vie d’une application web, vous effectuez différentes tâches de gestion, depuis le déploiement initial jusqu’au fonctionnement normal, en passant par la maintenance et les mises à jour.

De nombreuses tâches de gestion d’application web peuvent être effectuées dans le portail Azure.

## Avant de déployer votre application web en production

### Choisir un niveau

Azure App Service est disponibles en cinq niveaux : Gratuit, Partagé, De base, Standard et Premium. Pour plus d'informations sur les fonctionnalités et la tarification de chaque niveau, consultez les [détails de tarification](/pricing/details/app-service/).

- Les [plans App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) vous permettent de regrouper plusieurs applications web sous le même niveau.
- Vous avez toujours la possibilité de [changer de niveau](web-sites-scale.md) après avoir créé l’application web.

### Configuration

Utilisez la [version préliminaire du portail Azure](https://portal.azure.com/) pour définir différentes options de configuration. Pour plus d’informations, consultez la page [Configurer des applications web dans Azure App Service](web-sites-configure.md). Voici une liste de vérification rapide :

- Sélectionnez **Versions exécutables** pour .NET, PHP, Java ou Python, si nécessaire.
- Activez **WebSockets** si votre site web utilise ce protocole. (Ceci inclut les applications qui utilisent [ASP.NET SignalR](http://www.asp.net/signalr) ou [socket.io](web-sites-nodejs-chat-app-socketio.md).)
- Exécutez-vous des tâches Web continues ? Si tel est le cas, activez l'option **Toujours activé**.
- Définissez le **document par défaut**, par exemple index.html.

Outre ces paramètres de configuration de base, vous pouvez configurer ce qui suit :

- Le chiffrement **SSL (Secure Socket Layer)**. Pour utiliser le chiffrement SSL avec un nom de domaine personnalisé, vous devez obtenir un certificat SSL et configurer votre application Web pour l’utiliser. Consultez la page [Activer le protocole HTTPS pour une application web dans Azure App Service](web-sites-configure-ssl-certificate.md).
- **Nom de domaine personnalisé.** Votre application web a automatiquement un sous-domaine sous azurewebsites.net. Vous pouvez lui associer un nom de domaine personnalisé, par exemple contoso.com. Consultez la page [Configurer un nom de domaine personnalisé dans Azure App Service](web-sites-custom-domain-name.md).

Configuration spécifique à la langue :

- **PHP** : [configurez PHP dans Azure App Service Web Apps](web-sites-php-configure.md).
- **Python** : [configurez Python dans Azure App Service Web Apps](web-sites-python-configure.md).


## Pendant l’exécution de votre application web

Lorsque votre application web est en cours d’exécution, vérifiez qu’elle est disponible et qu’elle s’adapte au volume du trafic utilisateur. Vous pouvez également résoudre les erreurs.

### Analyse

- Via le portail de gestion Azure, vous pouvez [ajouter des mesures de performances](web-sites-monitor.md) comme l’utilisation du processeur et le nombre de demandes de clients.
- Pour des informations plus détaillées, utilisez New Relic pour analyser et gérer les performances. Consultez la page [Gestion des performances des applications New Relic dans les applications Web Azure Service](store-new-relic-web-sites-dotnet-application-performance-management.md).
- [Mettez votre application web à l’échelle](web-sites-scale.md) en fonction du trafic. Suivant l'option choisie, vous pouvez mettre à l'échelle le nombre de machines virtuelles et/ou la taille des instances de celles-ci. Dans les niveaux Standard et Premium, vous pouvez également configurer la mise à l’échelle automatique, qui fait évoluer votre application automatiquement soit à une planification définie, soit en fonction de la charge.  
 
### Sauvegarde

- Configurez les [sauvegardes automatiques](web-sites-backup.md) de votre application web. Pour en savoir plus sur les sauvegardes, regardez [cette vidéo](http://azure.microsoft.com/documentation/videos/azure-websites-automatic-and-easy-backup/).
- Découvrez les options de [récupération de base de données](http://msdn.microsoft.com/library/azure/hh852669.aspx) dans la base de données Azure SQL.

### Résolution des problèmes

- Si une erreur survient, vous pouvez [résoudre les problèmes dans Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug) en utilisant des journaux de diagnostic et le débogage réel dans le cloud. 
- En dehors de Visual Studio, il existe différentes manières de recueillir les journaux de diagnostic. Consultez la page [Activer la journalisation des diagnostics pour les applications web dans Azure App Service](web-sites-enable-diagnostic-log.md).
- Pour les applications Node.js, consultez la page [Débogage d’une application Node.js dans Azure Web Sites](web-sites-nodejs-debug.md).

### Restauration de données

- [Restaurez](web-sites-restore.md) une application Web qui a été précédemment sauvegardée.


## Lors de la mise à jour de votre application Web

Si vous n'avez pas activé les sauvegardes automatiques, vous pouvez créer une [sauvegarde manuelle](web-sites-backup.md).

Envisagez d'utiliser le [déploiement intermédiaire](web-sites-staged-publishing.md). Cette option vous permet de publier des mises à jour pour un déploiement intermédiaire qui s'exécute parallèlement à votre déploiement de production.

Si vous utilisez Visual Studio Online, vous pouvez configurer un déploiement continu à partir d'un contrôle de code source :

- [Utilisation de TFVC (Team Foundation Version Control)](../cloud-services-continuous-delivery-use-vso.md) 
- [Utilisation de Git](../cloud-services-continuous-delivery-use-vso-git.md)
 
[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 
<!-- Anchors. -->


[Before you deploy your site to production]: #before-you-deploy-your-site-to-production
[While your website is running]: #while-your-website-is-running
[When you update your website]: #when-you-update-your-website

  

<!---HONumber=Oct15_HO3-->