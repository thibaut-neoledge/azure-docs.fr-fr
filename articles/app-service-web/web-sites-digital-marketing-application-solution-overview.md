<properties 
	pageTitle="Créer une campagne marketing numérique sur Azure App Service Web Apps" 
	description="Ce guide fournit un aperçu technique de l’utilisation d’Azure App Service Web Apps en vue de créer des campagnes marketing numériques. Il aborde notamment le déploiement, l’intégration aux médias sociaux, les stratégies de mise à l’échelle et l’analyse." 
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
	ms.date="07/06/2015" 
	ms.author="cephalin"/>

# Créer une campagne marketing numérique sur Azure App Service Web Apps
[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps est l’outil de prédilection des campagnes marketing numériques. Les campagnes marketing numériques sont des entités à durée de vie limitée, conçues pour promouvoir des objectifs marketing à court terme. Il y a deux scénarios principaux à envisager. Dans le premier scénario, une agence de marketing crée et gère une campagne pour un client pendant toute la durée de la promotion. Dans le second scénario, l’agence de marketing crée une campagne marketing numérique, puis transfère les droits de propriété sur les ressources associées au client. Ce dernier lance et gère ensuite seul la campagne. est approprié dans les deux cas.

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement et gratuitement une application de départ temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

Vous trouverez ci-dessous un exemple de campagne marketing numérique multi-canal globale utilisant App Service Web Apps. Il montre les possibilités offertes par l’utilisation d’App Service Web Apps avec d’autres services pour un investissement technique minimal. **Cliquez sur un élément de la topographie pour en savoir plus.**

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/digital-marketing-notitle.svg" width="100%" height="100%"></object>

> [AZURE.NOTE]Ce guide présente certaines des zones et des tâches les plus courantes correspondant à l’exécution d’une campagne marketing numérique dans Azure App Service Web Apps. Toutefois, il existe d’autres solutions courantes que vous pouvez implémenter dans App Service Web Apps. Pour les découvrir, consultez les guides relatifs à la [Présence sur le Web à l’international](web-sites-global-web-presence-solution-overview.md) et aux [Applications métier](web-sites-business-application-solution-overview.md).

## Créer des ressources ou utiliser des ressources existantes

Créez rapidement de nouvelles applications web à partir d’un CMS populaire dans la galerie ou intégrez vos ressources web existantes à App Service Web Apps à partir d’une variété de langues et d’infrastructures.

Microsoft Azure Marketplace fournit des modèles proposés par des systèmes CMS bien connus, tel que [Orchard], [Umbraco], [Drupal] et [WordPress]. Vous pouvez créer une application web en utilisant votre version de CMS préférée. Différents systèmes de bases de données principaux permettent de répondre à vos besoins, y compris la [base de données SQL Azure] et [MySQL].

Vos ressources web existantes peuvent s’exécuter sur des applications web, même s’il s’agit d’applications .NET, PHP, Java, Node.js ou Python. Vous pouvez les déplacer vers Web Apps à l’aide des outils [FTP] avec lesquels vous êtes familiarisé. Si vous créez souvent des campagnes marketing numériques, il se peut que vous disposiez de ressources web existantes au sein d’un système de gestion de contrôle de code source. Vous pouvez effectuer un déploiement vers Web Apps directement à partir d’options de contrôle de code source populaires, telles que [Visual Studio], [Visual Studio Online] et [Git] : local, GitHub, BitBucket, DropBox, Mercurial, etc.

## Restez flexible

Préservez votre flexibilité par une publication en continu directement à partir de votre contrôle de code source existant et exécutez des tests A/B dans App Service Web Apps.

Lors des phases de planification, de prototypage et de développement initial d’une application web, l’agence et le client peuvent travailler sur des versions fonctionnelles réelles de l’application de campagne avant sa mise en service par [déploiement vers un emplacement intermédiaire] de votre application web. L’intégration du contrôle de code source à App Service Web Apps vous permet d’effectuer la [publication en continu] vers un emplacement intermédiaire et de mettre l’application en production sans temps d’interruption dès que celle-ci est prête.

Lorsque vous planifiez des modifications à apporter à une application web active, vous pouvez également facilement [exécuter des tests A/B] sur les mises à jour proposées à l’aide de la fonctionnalité de test en production intégrée et analyser le comportement des utilisateurs réels pour vous permettre de prendre des décisions avisées sur la conception de l’application.


## Pensez réseaux sociaux

Votre campagne marketing numérique dans App Service Web Apps peut être intégrée aux réseaux sociaux par une authentification auprès des réseaux les plus largement plébiscités, tels que Facebook et Twitter. Pour voir un exemple de cette approche avec une application ASP.NET, consultez [Créer une application ASP.NET MVC avec authentification et base de données SQL et la déployer dans Azure App Service].

Par ailleurs, chaque site de réseau social fournit habituellement des informations relatives aux autres modes d’intégration à partir de .NET et de nombreuses autres infrastructures.

## Utilisation de contenus multimédia enrichis et atteindre tous les périphériques

Enrichissez votre campagne marketing numérique avec d’autres services Azure :

-  Chargement et diffusion en continu de vidéos dans le monde entier avec [Azure Media Services]
-  Envoi de courriers électroniques aux utilisateurs grâce au [service SendGrid de Microsoft Azure Marketplace]
-  Établissement d’une présence sur les appareils Windows, iOS et Android grâce à [Mobile Services]
-  Envoi de notifications Push à des millions d’appareils avec [Notification Hub]

## Optez pour l’international !

Optez pour l’international, en desservant des sites régionaux avec Microsoft Azure Traffic Manager et en fournissant du contenu très rapidement, grâce à Microsoft Azure Content Delivery Network (CDN).

Pour desservir des clients dans leurs régions respectives, recourez à [Microsoft Azure Traffic Manager] pour rediriger les visiteurs vers un site régional offrant des performances optimales. Vous pouvez également répartir uniformément la charge du site entre plusieurs copies de votre application web hébergée dans plusieurs régions.

Fournissez très rapidement un contenu statique aux utilisateurs dans le monde entier via l’[intégration de votre application web avec Azure CDN]. Azure CDN met en cache le contenu statique dans le [nœud CDN] le plus proche de l’utilisateur, ce qui réduit la latence et les connexions à votre application web.

## Optimisation

Optimisez votre application web en procédant à une mise à l’échelle automatique à l’aide de la fonctionnalité Échelle automatique, à la mise en cache avec le Cache Redis Azure, à l’exécution de tâches en arrière-plan avec WebJobs et au maintien d’une haute disponibilité avec Azure Traffic Manager.

La capacité de [mise à l’échelle] d’App Service Web Apps est idéale pour faire face aux charges de travail imprévisibles des campagnes marketing numériques. Mettez votre application web à l’échelle manuellement, via le [portail Azure en version préliminaire](http://go.microsoft.com/fwlink/?LinkId=529715), par programme, via l’[API de gestion de service] ou des [scripts PowerShell], ou encore automatiquement, via la fonctionnalité de mise à l’échelle automatique. Au niveau **Standard**, la fonctionnalité de mise à l’échelle automatique vous permet de dimensionner une application web automatiquement selon l’utilisation du processeur. Elle vous permet dans le même temps d’optimiser la flexibilité et de réduire les coûts en mettant à l’échelle l’application web uniquement lorsque cela est nécessaire en fonction de l’activité des utilisateurs. Pour connaître les bonnes pratiques, consultez [10 choses que j’ai apprises sur la mise à l’échelle rapide d’applications web avec Azure] sur le site de [Troy Hunt].

Améliorez la réactivité de votre application web avec le [cache Redis Azure]. Utilisez-le pour mettre en cache les données des bases de données principales et d’autres éléments, par exemple l’[état de session ASP.NET] et le [cache de sortie].

Maintenez la haute disponibilité de votre application web à l’aide d’[Azure Traffic Manager]. À l’aide de la méthode **Basculement**, Traffic Manager achemine automatiquement le trafic vers un site secondaire en cas de problème sur le site primaire.

## Surveillance et analyse

Tenez-vous informé des performances de votre application web avec Azure ou des outils tiers. Recevez des alertes concernant les événements critiques liés à l’application web. Grâce à Application Insight ou à la fonction d’analyse de journaux web du logiciel HDInsight, vous pouvez mieux connaître les utilisateurs.

Consultez un [bref aperçu] des métriques de performance et des quotas de ressources de l’application web dans le panneau de cette dernière sur le [portail Azure en version préliminaire](http://go.microsoft.com/fwlink/?LinkId=529715). Pour une évaluation complète de la disponibilité, des performances et de l’utilisation de votre application web, utilisez [Azure Application Insights] et ses puissantes fonctionnalités d’utilisation, de diagnostic et de dépannage. Vous pouvez également utiliser un outil tiers comme [New Relic] pour obtenir des données de surveillance avancées sur vos applications web.

Au niveau **Standard**, surveillez la réactivité de l’application et recevez des notifications par courrier électronique dès que votre application web ne répond plus. Pour plus d’informations, consultez la page [Réception de notifications d’alerte et gestion des règles d’alerte dans Azure].

## Autres ressources

- [Documentation d’App Service Web Apps](/services/app-service/web/)
- [Plan d’apprentissage pour Azure App Service Web Apps](websites-learning-map.md)
- [Blog web sur Microsoft Azure](/blog/topics/web/)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[Azure App Service]: /services/app-service/web/

[Orchard]: web-sites-dotnet-orchard-cms-gallery.md
[Umbraco]: web-sites-gallery-umbraco.md
[Drupal]: web-sites-php-migrate-drupal.md
[WordPress]: web-sites-php-web-site-gallery.md
  
[MySQL]: web-sites-php-mysql-deploy-use-git.md
[base de données SQL Azure]: web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md
[FTP]: web-sites-deploy.md#ftp
[Visual Studio]: web-sites-dotnet-get-started.md
[Visual Studio Online]: ../cloud-services-continuous-delivery-use-vso.md
[Git]: web-sites-publish-source-control.md

[déploiement vers un emplacement intermédiaire]: web-sites-staged-publishing.md
[publication en continu]: http://rickrainey.com/2014/01/21/continuous-deployment-github-with-azure-web-sites-and-staged-publishing/
[exécuter des tests A/B]: http://blogs.msdn.com/b/tomholl/archive/2014/11/10/a-b-testing-with-azure-websites.aspx

[Créer une application ASP.NET MVC avec authentification et base de données SQL et la déployer dans Azure App Service]: web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md

[Azure Media Services]: http://blogs.technet.com/b/cbernier/archive/2013/09/03/windows-azure-media-services-and-web-sites.aspx
[service SendGrid de Microsoft Azure Marketplace]: sendgrid-dotnet-how-to-send-email.md
[Mobile Services]: ../mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users.md
[Notification Hub]: ../mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users.md

[Azure Traffic Manager]: http://www.hanselman.com/blog/CloudPowerHowToScaleAzureWebsitesGloballyWithTrafficManager.aspx
[Microsoft Azure Traffic Manager]: http://www.hanselman.com/blog/CloudPowerHowToScaleAzureWebsitesGloballyWithTrafficManager.aspx
[intégration de votre application web avec Azure CDN]: cdn-websites-with-cdn.md
[nœud CDN]: https://msdn.microsoft.com/library/azure/gg680302.aspx

[mise à l’échelle]: /manage/services/web-sites/how-to-scale-websites/
[Azure Management Portal]: http://manage.windowsazure.com/
[API de gestion de service]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx
[scripts PowerShell]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx
[Troy Hunt]: https://twitter.com/troyhunt
[10 choses que j’ai apprises sur la mise à l’échelle rapide d’applications web avec Azure]: http://www.troyhunt.com/2014/09/10-things-i-learned-about-rapidly.html
[cache Redis Azure]: /blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
[état de session ASP.NET]: https://msdn.microsoft.com/library/azure/dn690522.aspx
[cache de sortie]: https://msdn.microsoft.com/library/azure/dn798898.aspx

[bref aperçu]: /manage/services/web-sites/how-to-monitor-websites/
[Azure Application Insights]: http://blogs.msdn.com/b/visualstudioalm/archive/2015/01/07/application-insights-and-azure-websites.aspx
[New Relic]: /develop/net/how-to-guides/new-relic/
[Réception de notifications d’alerte et gestion des règles d’alerte dans Azure]: http://msdn.microsoft.com/library/windowsazure/dn306638.aspx

  
  [gitstaging]: http://www.bradygaster.com/post/multiple-environments-with-windows-azure-web-sites
 

<!---HONumber=July15_HO4-->