<properties 
	pageTitle="Créer une présence sur le web à l’international sur Azure App Service Web Apps" 
	description="Ce guide offre un aperçu technique de l’hébergement du site (.COM) de votre organisation sur Microsoft Azure App Service Web Apps. Il aborde notamment le déploiement, les domaines personnalisés, SSL et l’analyse." 
	editor="jimbe" 
	manager="wpickett" 
	authors="cephalin" 
	services="app-service\web" 
	documentationCenter=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/26/2016" 
	ms.author="cephalin"/>


# Créer une présence sur le web à l’international sur Azure App Service Web Apps

[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps inclut toutes les fonctions requises pour établir une présence sur le web à l’international pour votre site .COM. Quelle que soit la taille de votre organisation, il vous faut une plateforme robuste, sécurisée et évolutive pour favoriser votre activité, la connaissance de votre marque et vos communications client. App Service Web Apps peut vous aider à assurer la bonne réputation de votre marque et de votre identité, grâce au soutien de votre continuité d’activité par Microsoft.

>[AZURE.NOTE] Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement et gratuitement une application de départ temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

Voici un exemple de site web .COM exécutant App Service Web Apps. Il indique les actions possibles lorsque vous vous contentez de combiner des applications web avec d’autres services, pour un investissement technique minime. **Cliquez sur un élément de la topographie pour en savoir plus.**

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/corp-website-visio.svg" width="100%" height="100%"></object>

> [AZURE.NOTE]
Ce guide présente certaines des zones et tâches les plus courantes, alignées avec l’exécution d’un site .COM public dans Azure App Service Web Apps. Toutefois, il existe d’autres solutions courantes, que vous pouvez implémenter dans Azure App Service Web Apps. Pour les découvrir, consultez les autres guides concernant les [campagnes marketing numériques](web-sites-digital-marketing-application-solution-overview.md) et les [applications métier](web-sites-business-application-solution-overview.md).

## Créer des ressources ou utiliser des ressources existantes

Vous pouvez créer rapidement des sites à partir d’un système de gestion de contenu (CMS) bien connu de la galerie, ou exploiter les ressources web existantes dans App Service Web Apps, qui utilisent différents langages et structures.

Microsoft Azure Marketplace fournit des modèles proposés par des systèmes CMS bien connus, tel que Orchard, Umbraco, Drupal et [WordPress]. Vous pouvez créer une application web en utilisant votre version de CMS préférée. Différents systèmes de bases de données principaux permettent de répondre à vos besoins, y compris la [base de données SQL Azure] et [MySQL].

Vos ressources web existantes peuvent s’exécuter sur App Service Web Apps, qu’il s’agisse de ressources .NET, PHP, Java, Node.js ou Python. Vous pouvez les déplacer vers Web Apps à l’aide de vos outils [FTP] habituels ou de votre système de gestion de contrôle de code source. Web Apps prend en charge la publication directe à partir des options de contrôle de code source populaires, comme [Visual Studio], [Visual Studio Team Services] et [Git] (local, GitHub, BitBucket, DropBox, Mercurial, etc.).

## Publication fiable

Publiez votre site web de manière fiable en optant pour la publication en continu et ce, directement à partir de votre système de contrôle de code source existant, et en testant en direct votre contenu.

Lors de la planification, du prototypage et du développement initial d’un site, vous pouvez consulter les versions fonctionnelles réelles du site web avant sa mise en service via le [déploiement vers un module de transfert] de votre site sur l’application vers App Service Web Apps. En intégrant le contrôle de code source dans Web Apps, vous pouvez effectuer une [publication en continu] vers un module de transfert, puis basculer en mode de production sans temps d’arrêt, le moment venu. En cas de défaillance sur le site de production, vous pouvez également rebasculer vers une version précédente de votre site et ce, immédiatement.

De plus, lorsque vous envisagez d’apporter des modifications à un site web en ligne, vous pouvez facilement [exécuter des tests A/B] sur les mises à jour prévues, en utilisant la fonction de test en production, puis analyser le comportement réel de l’utilisateur, afin de prendre des décisions éclairées sur la conception de votre site.

## Marque et sécurité

Vous pouvez utiliser gratuitement le domaine App Service Web Apps, ou effectuer un mappage vers le nom de domaine enregistrer, puis sécuriser votre marque au moyen du certificat SSL signé par une autorité de certification.

Le domaine ***.azurewebsites.net** est gratuit lorsque vous exécutez votre site web sur Web Apps. Vous pouvez également mapper votre site web sur un [domaine personnalisé] (par exemple : contoso.com), obtenu à partir d'un registre DNS tel que GoDaddy.

Si vous collectez des informations sur les utilisateurs, effectuez des transactions de type e-commerce ou gérez des données sensibles, vous pouvez protéger la réputation de votre marque et vos clients avec le protocole [HTTPS]. Le nom de domaine ***.azurewebsites.net** propose déjà un certificat SSL. Si vous utilisez votre domaine personnalisé, vous pouvez intégrer le certificat SSL qui lui est associé dans Web Apps. Une facturation mensuelle (au prorata des heures) est associée à chaque certificat SSL. Pour en savoir plus, consultez [Détails de la tarification d’App Service].

## Optez pour l’international !

Optez pour l’international, en desservant des sites régionaux avec Microsoft Azure Traffic Manager et en fournissant du contenu très rapidement, grâce à Microsoft Azure Content Delivery Network (CDN).

Pour desservir des clients dans leurs régions respectives, recourez à [Microsoft Azure Traffic Manager] pour rediriger les visiteurs vers un site régional offrant des performances optimales. Vous pouvez également répartir la charge du site entre plusieurs copies de votre site web hébergé dans plusieurs régions et ce, de manière équitable.

Fournissez très rapidement un contenu statique aux utilisateurs dans le monde entier via l’[intégration de votre application web avec Microsoft Azure CDN]. CDN met en cache le contenu statique dans le [nœud CDN] le plus proche de l’utilisateur, ce qui réduit la latence et les connexions à votre site web.

## Optimisation

Optimisez votre site .COM en le mettant automatiquement à l’échelle avec la fonction Mise à l’échelle automatique, en le mettant en cache avec le cache Redis Azure, en exécutant des tâches en arrière-plan avec WebJobs et assurant une haute disponibilité avec Azure Traffic Manager.

La capacité de l’application App Service Web Apps à [adapter la taille des sites] répond aux besoins de votre site .COM, quelle que soit la taille de votre charge de travail. Augmentez la taille de votre site manuellement via la [portail Azure](https://portal.azure.com) ou par programme, via l’[API Service Management] ou des [scripts PowerShell], ou encore automatiquement, via la fonction Mise à l’échelle automatique. Dans le plan d’hébergement **standard**, la fonction Mise à l’échelle automatique vous permet d’augmenter automatiquement la taille d’un site web, selon l’utilisation de l’UC. Pour connaître les bonnes pratiques, consultez [10 choses que j’ai apprises sur la mise à l’échelle rapide d’applications web avec Azure] sur le site de [Troy Hunt].

Optimisez la réactivité de votre site web grâce au [cache Redis Azure]. Utilisez-le pour mettre en cache les données des bases de données principales et d’autres éléments, par exemple l’[état de session ASP.NET] et le [cache de sortie].

Assurez la haute disponibilité de votre site web à l’aide [Microsoft Azure Traffic Manager]. À l’aide de la méthode **Basculement**, Traffic Manager achemine automatiquement le trafic vers un site secondaire en cas de problème sur le site primaire.

## Surveillance et analyse

Tenez-vous informé sur les performances de votre site web au moyen d’outils Microsoft Azure ou tiers. Recevez des alertes sur des événements critiques de votre site web. Grâce à Application Insight ou à la fonction d’analyse de journaux web du logiciel HDInsight, vous pouvez mieux connaître les utilisateurs.

Bénéficiez d’un [aperçu rapide] sur les mesures de performance et les quotas de ressources actuels de votre site web, en consultant le panneau de l’application web dans le [portail Azure](https://portal.azure.com). Pour une évaluation complète de la disponibilité, des performances et de l’utilisation de votre application web, utilisez [Azure Application Insights] et ses puissantes fonctionnalités d’utilisation, de diagnostic et de dépannage. Vous pouvez aussi recourir à un outil tiers comme [New Relic], qui fournit des données de surveillance avancées pour tous vos sites web.

Dans le plan d’hébergement **Standard**, vous pouvez surveiller la réactivité de votre site, en recevant des notifications par messagerie électronique chaque fois que votre site ne répond pas. Pour plus d’informations, consultez la page [Réception de notifications d’alerte et gestion des règles d’alerte dans Azure].

## Utilisation de contenus multimédia enrichis et atteindre tous les périphériques

Rendez votre site .COM attrayant grâce à des contenus multimédia enrichis, comme ce qui suit :

-  Chargement et diffusion en continu de vidéos dans le monde entier avec [Azure Media Services]
-  Envoi de courriers électroniques aux utilisateurs grâce au [service SendGrid de Microsoft Azure Marketplace]

## Autres ressources

- [Documentation d’App Service Web Apps](/services/app-service/web/)
- [Plan d’apprentissage pour Azure App Service Web Apps](/documentation/learning-paths/appservice-webapps/)
- [Blog web sur Microsoft Azure](/blog/topics/web/)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]


[Azure App Service]: /services/app-service/web/

[WordPress]: web-sites-php-web-site-gallery.md
[MySQL]: web-sites-php-mysql-deploy-use-git.md
[base de données SQL Azure]: web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md
[FTP]: web-sites-deploy.md#ftp
[Visual Studio]: web-sites-dotnet-get-started.md
[Visual Studio Team Services]: ../cloud-services-continuous-delivery-use-vso.md
[Git]: web-sites-publish-source-control.md

[déploiement vers un module de transfert]: web-sites-staged-publishing.md
[publication en continu]: http://rickrainey.com/2014/01/21/continuous-deployment-github-with-azure-web-sites-and-staged-publishing/
[exécuter des tests A/B]: http://blogs.msdn.com/b/tomholl/archive/2014/11/10/a-b-testing-with-azure-websites.aspx

[domaine personnalisé]: web-sites-custom-domain-name.md
[HTTPS]: web-sites-configure-ssl-certificate.md
[Détails de la tarification d’App Service]: /pricing/details/app-service/#ssl-connections

[Microsoft Azure Traffic Manager]: http://www.hanselman.com/blog/CloudPowerHowToScaleAzureWebsitesGloballyWithTrafficManager.aspx
[Microsoft Azure Traffic Manager]: http://www.hanselman.com/blog/CloudPowerHowToScaleAzureWebsitesGloballyWithTrafficManager.aspx
[intégration de votre application web avec Microsoft Azure CDN]: cdn-websites-with-cdn.md
[nœud CDN]: https://msdn.microsoft.com/library/azure/gg680302.aspx

[adapter la taille des sites]: web-sites-scale.md
[Azure Management Portal]: http://manage.windowsazure.com/
[API Service Management]: https://msdn.microsoft.com/library/azure/ee460799.aspx
[scripts PowerShell]: https://msdn.microsoft.com/library/azure/jj152841.aspx
[Troy Hunt]: https://twitter.com/troyhunt
[10 choses que j’ai apprises sur la mise à l’échelle rapide d’applications web avec Azure]: http://www.troyhunt.com/2014/09/10-things-i-learned-about-rapidly.html
[cache Redis Azure]: /blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
[état de session ASP.NET]: https://msdn.microsoft.com/library/azure/dn690522.aspx
[cache de sortie]: https://msdn.microsoft.com/library/azure/dn798898.aspx

[aperçu rapide]: web-sites-monitor.md
[Azure Application Insights]: http://blogs.msdn.com/b/visualstudioalm/archive/2015/01/07/application-insights-and-azure-websites.aspx
[New Relic]: ../store-new-relic-cloud-services-dotnet-application-performance-management.md
[Réception de notifications d’alerte et gestion des règles d’alerte dans Azure]: http://msdn.microsoft.com/library/windowsazure/dn306638.aspx

[Azure Media Services]: http://blogs.technet.com/b/cbernier/archive/2013/09/03/windows-azure-media-services-and-web-sites.aspx
[service SendGrid de Microsoft Azure Marketplace]: sendgrid-dotnet-how-to-send-email.md

 

<!---HONumber=AcomDC_0302_2016-->