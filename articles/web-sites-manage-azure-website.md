<properties 
	pageTitle="Gérer un site Web Azure" 
	description="Liens vers des ressources permettant de gérer un site Web Microsoft Azure." 
	services="web-sites" 
	documentationCenter="" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/16/2014" 
	ms.author="mwasson"/>

# Gérer un site Web Azure

Cette rubrique contient des liens vers des ressources relatives à la gestion d'un site Web Azure. La gestion comprend toutes les tâches nécessaires au bon fonctionnement de votre site Web. 

Au cours de la durée de vie d'un site, vous effectuerez différentes tâches de gestion, depuis le déploiement initial au fonctionnement normal, en passant par la maintenance et les mises à jour.

- [Avant de déployer votre site en production]
- [Pendant l'exécution de votre site Web]
- [Lors de la mise à jour de votre site Web]

De nombreuses tâches de gestion de site Web peuvent être effectuées dans le portail Azure. Pour plus d'informations, reportez-vous à la page [Gérer des sites Web via le portail de gestion Azure](http://azure.microsoft.com/documentation/articles/web-sites-manage/).

## Avant de déployer votre site en production

### Choisir un niveau

Le service Sites Web Azure est disponible en quatre niveaux : Gratuit, Partagé, Basique et Standard. Pour plus d'informations sur les fonctionnalités et la tarification de chaque niveau, consultez les [détails de tarification](http://azure.microsoft.com/pricing/details/websites/). 

- [Les plans d'hébergement Web](http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview) vous permettent de regrouper plusieurs sites Web sous le même niveau.
- Vous avez toujours la possibilité de [changer de niveau](http://azure.microsoft.com/documentation/articles/web-sites-scale/) après avoir créé le site Web.

### Configuration

Utilisez le [portail de gestion Azure](https://manage.windowsazure.com/) pour définir différentes options de configuration. Pour plus d'informations, consultez la page [Configuration des sites Web](http://azure.microsoft.com/documentation/articles/web-sites-configure/). Voici une liste de vérification rapide :

- Sélectionnez **Versions exécutables** pour .NET, PHP, Java ou Python, si nécessaire.
- Activez **WebSockets** si votre site Web utilise le protocole WebSocket. (Ceci inclut les applications qui utilisent [ASP.NET SignalR](http://www.asp.net/signalr) ou [socket.io](http://azure.microsoft.com/documentation/articles/web-sites-nodejs-chat-app-socketio/).)
- Exécutez-vous des tâches Web continues ? Si tel est le cas, activez l'option **Toujours activé**.
- Définissez le **document par défaut**, par exemple index.html.

Outre ces paramètres de configuration de base, vous pouvez configurer ce qui suit :

- Chiffrement **SSL (Secure Socket Layer)**. Pour utiliser le chiffrement SSL avec un nom de domaine personnalisé, vous devez obtenir un certificat SSL et configurer votre site Web pour l'utiliser. Consultez la page [Activation du protocole HTTPS pour un site Web Azure](http://azure.microsoft.com/documentation/articles/web-sites-configure-ssl-certificate/).
- **Nom du domaine personnalisé.** Votre site Web a automatiquement un sous-domaine sous azurewebsites.net. Vous pouvez associer un nom de domaine personnalisé, tel que contoso.com. Consultez la page [Configuration d'un nom de domaine personnalisé](http://azure.microsoft.com/documentation/articles/web-sites-custom-domain-name/).

Configuration d'un langage spécifique :

- **PHP** : [configuration de PHP dans Sites Web Azure](http://azure.microsoft.com/documentation/articles/web-sites-php-configure/).
- **Python** : [configuration de Python avec Sites Web Azure](http://azure.microsoft.com/documentation/articles/web-sites-python-configure/)


## Pendant l'exécution de votre site Web

Lorsque votre site est en cours d'exécution, vous pouvez vous assurer qu'il est disponible et qu'il peut être mis à l'échelle pour répondre au trafic utilisateur. Vous pouvez également résoudre les erreurs.

### Surveillance

- Via le portail de gestion, vous pouvez [ajouter des mesures de performances](http://azure.microsoft.com/documentation/articles/web-sites-monitor) telles que l'utilisation de l'unité centrale et le nombre de demandes de clients.
- Pour des informations plus détaillées, utilisez New Relic pour analyser et gérer les performances. Consultez la page [Gestion des performances des applications New Relic sur Azure](http://azure.microsoft.com/documentation/articles/store-new-relic-web-sites-dotnet-application-performance-management/).
- [Mise à l'échelle de votre site Web](http://azure.microsoft.com/documentation/articles/web-sites-scale/) pour répondre au trafic. Suivant l'option choisie, vous pouvez mettre à l'échelle le nombre de machines virtuelles et/ou la taille des instances de celles-ci. Dans le plan Standard, vous pouvez également définir la mise à l'échelle automatique, qui met automatiquement votre site à l'échelle à une date prédéfinie ou en fonction de la charge.  
 
### Sauvegarde

- Définissez des [sauvegardes automatiques](http://azure.microsoft.com/documentation/articles/web-sites-backup/) de votre site Web. Pour en savoir plus sur les sauvegardes, regardez [cette vidéo](http://azure.microsoft.com/documentation/videos/azure-websites-automatic-and-easy-backup/).
- Découvrez les options de [récupération de base de données](http://msdn.microsoft.com/library/azure/hh852669.aspx) dans la base de données Azure SQL.

### Résolution des problèmes

- Si une erreur survient, vous pouvez [résoudre les problèmes dans Visual Studio](http://azure.microsoft.com/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio/#remotedebug) en utilisant des journaux de diagnostic et le débogage réel dans le cloud. 
- En dehors de Visual Studio, il existe différentes manières de recueillir les journaux de diagnostic. Consultez la page [Activation de la journalisation de diagnostic pour les sites Web Azure](http://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/).
- Pour les applications Node.js, consultez la page [Débogage d'une application Node.js dans les sites Web Azure](http://azure.microsoft.com/documentation/articles/web-sites-nodejs-debug/).

### Restauration de données

- [Restaurez](http://azure.microsoft.com/documentation/articles/web-sites-restore/) un site Web qui a été précédemment sauvegardé.


## Lors de la mise à jour de votre site Web

Si vous n'avez pas activé les sauvegardes automatiques, vous pouvez créer une [sauvegarde manuelle](http://azure.microsoft.com/documentation/articles/web-sites-backup/).

Envisagez d'utiliser le [déploiement intermédiaire](http://azure.microsoft.com/documentation/articles/web-sites-staged-publishing/). Cette option vous permet de publier des mises à jour pour un déploiement intermédiaire qui s'exécute parallèlement à votre déploiement de production. 

Si vous utilisez Visual Studio Online, vous pouvez configurer un déploiement continu à partir d'un contrôle de code source :

- [Utilisation de TFVC (Team Foundation Version Control)](http://azure.microsoft.com/documentation/articles/cloud-services-continuous-delivery-use-vso/) 
- [Utilisation de Git](http://azure.microsoft.com/documentation/articles/cloud-services-continuous-delivery-use-vso-git/)
 

 
<!-- Anchors. -->


[Avant de déployer votre site en production]: #before-you-deploy-your-site-to-production
[Pendant l'exécution de votre site Web]: #while-your-website-is-running
[Lors de la mise à jour de votre site Web]: #when-you-update-your-website

 


<!--HONumber=42-->
