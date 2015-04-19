<properties 
	pageTitle="Créer des campagnes marketing numériques dans Sites Web Azure" 
	description="Ce guide fournit un aperçu technique de l'utilisation de Sites Web Azure pour créer des campagnes marketing numériques. Il aborde notamment le déploiement, l'intégration aux médias sociaux, les stratégies de mise à l'échelle et l'analyse." 
	editor="jimbe" 
	manager="wpickett" 
	authors="cephalin" 
	services="web-sites" 
	documentationCenter=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/02/2014" 
	ms.author="cephalin"/>

# Campagnes marketing numériques dans Sites Web Azure
[Sites Web Azure] est un excellent choix pour les campagnes marketing numériques. Les campagnes marketing numériques sont généralement éphémères et sont destinées à servir les objectifs marketing à court terme. Il y a deux scénarios principaux à envisager. Dans le premier scénario, une agence de marketing crée et gère une campagne pour un client pendant toute la durée de la promotion. Dans le second scénario, l'agence de marketing crée une campagne marketing numérique, puis transfère les droits de propriété sur les ressources associées au client. Ensuite, le client mène et gère lui-même la campagne marketing. est une bonne solution pour les deux scénarios. 

> [AZURE.NOTE] Si vous voulez prendre en main Sites Web Azure avant de créer un compte, accédez à <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>, où vous pouvez immédiatement et gratuitement créer un site de départ ASP.NET de courte durée dans Azure Web Sites. Aucune carte de crédit n'est requise, vous ne prenez aucun engagement.

Ci-dessous, vous trouverez l'exemple d'une campagne marketing numérique multicanal internationale, utilisant Sites Web Azure. Il montre ce que vous pouvez faire en associant simplement Sites Web Azure avec d'autres services, et ce avec un investissement technique minimal. **Cliquez sur un élément du schéma pour en savoir plus.** 

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/digital-marketing-notitle.svg" width="100%" height="100%"></object>

> [WACOM.NOTE]
> Ce guide présente les domaines et les tâches les plus courants s'adaptant à la gestion d'une campagne marketing numérique dans Sites Web Azure. Toutefois, il existe d'autres solutions courantes que vous pouvez implémenter dans Sites Web Azure. Pour les découvrir, consultez les guides relatifs à la <a href="http://azure.microsoft.com/manage/services/web-sites/global-web-presence-solution-overview/">Présence sur le Web à l'international</a> et aux <a href="http://azure.microsoft.com/manage/services/web-sites/business-application-solution-overview">Applications métier</a>.

### Création intégrale ou utilisation de ressources existantes

Créez rapidement des sites à partir d'un CMS populaire de la galerie ou utilisez vos ressources Web existantes sur Sites Web Azure à partir de divers langages et infrastructures.

La galerie Azure fournit des modèles à partir des systèmes de gestion de contenu (CMS) populaires, tels qu'[Orchard], [Umbraco], [Drupal] et [WordPress]. Vous pouvez créer un site Web en utilisant votre style de CMS préféré. Vous pouvez choisir parmi les différents serveurs principaux de base de données pour répondre à vos besoins, y compris la [base de données SQL Azure] et [MySQL].

Vos ressources Web existantes peuvent s'exécuter sur Sites Web Azure, qu'ils soient .NET, PHP, Java, Node.js ou Python. Vous pouvez les transférer vers Sites Web Azure à l'aide de vos outils [FTP] habituels. Si vous créez fréquemment des campagnes marketing numériques, vous disposez peut-être de composants Web dans un système de gestion de contrôle de code source. Vous pouvez directement déployer sur Sites Web Azure à partir d'options de contrôle du code source populaires, telles que [Visual Studio], [Visual Studio Online], et [Git] (local, GitHub, BitBucket, DropBox, Mercurial, etc.).

### Flexibilité

Restez flexible en publiant en continu directement à partir de votre contrôle de code source existant et exécutez des tests A/B dans Sites Web Azure. 

Lors des phases de planification, de prototypage et de développement initial d'un site, votre client et vous pouvez travailler sur des versions fonctionnelles réelles du site de campagne avant de le mettre en ligne en [déployant sur un module de transfert] de votre site Web Azure. En intégrant le contrôle de code source à Sites Web Azure, vous pouvez [publier en continu] sur un module de transfert pour le basculer en production dès qu'il est prêt et sans délai. 

Lorsque vous planifiez les modifications apportées à un site Web actif, vous pouvez facilement [exécuter des tests A/B] sur les mises à jour proposées avec la fonctionnalité de test en production et analyser le comportement réel des utilisateurs pour éclairer vos décisions sur la conception de votre site.


### Intégration des médias sociaux

Votre campagne marketing numérique dans Sites Web Azure peut intégrer des médias sociaux par le biais de l'authentification auprès de fournisseurs populaires comme Facebook et Twitter. Pour découvrir un exemple de cette approche avec une application ASP.NET, consultez la rubrique [Déploiement d'une application ASP.NET MVC sécurisée avec une fonctionnalité d'appartenance, OAuth et une base de données SQL vers un site Web Azure]. 

En outre, chaque site de média social fournit généralement des informations sur d'autres façons de l'intégrer à partir de .NET et beaucoup d'autres infrastructures.

### Utilisation de contenus multimédias riches et présence sur tous les périphériques

Enrichissez votre campagne marketing numérique avec d'autres services Azure. Vous pouvez notamment :

-  télécharger et diffuser des vidéos à l'international avec [Azure Media Services] ;
-  envoyer des e-mails aux utilisateurs avec le [service SendGrid dans Azure Marketplace] ;
-  assurer une présence sur les périphériques Windows, iOS et Android avec [Mobile Services] ;
-  envoyer des notifications Push à des millions d'appareils avec [Notification Hub].

### Présence mondiale

Pensez mondial en proposant des sites régionaux avec Azure Traffic Manager et en offrant du contenu ultrarapide grâce à Azure CDN.

Pour atteindre les clients dans leurs régions respectives, utilisez [Azure Traffic Manager] pour acheminer vos visiteurs vers un site régional qui fournit de meilleures performances. Vous pouvez également répartir uniformément la charge du site entre plusieurs copies de votre site Web hébergé dans plusieurs régions.

Proposez un contenu statique ultrarapide aux utilisateurs du monde entier en [intégrant votre site Web à Azure CDN]. Azure CDN met en cache le contenu statique dans le [nœud CDN] le plus proche de l'utilisateur, ce qui réduit la latence et les connexions sur votre site Web.

### Optimisation

Optimisez votre site grâce à la mise à l'échelle automatique, à la mise en cache avec le Cache Redis Azure, à l'exécution des tâches en arrière-plan avec WebJobs et au maintien d'une haute disponibilité avec Azure Traffic Manager.

Avec sa capacité de [mise à l'échelle], Sites Web Azure est idéal pour les charges de travail imprévisibles, comme avec les campagnes marketing numériques. Mettez votre site Web à l'échelle manuellement via le [portail de gestion Azure], par programmation via l'[API de gestion de service] ou [scripts PowerShell], ou automatiquement via la fonctionnalité de mise à l'échelle automatique. Dans le plan d'hébergement **Standard**, la mise à l'échelle automatique vous permet de faire évoluer un site Web automatiquement en fonction de l'utilisation du processeur. Cette fonctionnalité vous permet d'optimiser la souplesse et de réduire les coûts tout en faisant monter en charge le site Web uniquement si nécessaire, selon l'activité des utilisateurs. Pour une utilisation optimale, consultez l'article de [Troy Hunt] [10 choses que j'ai apprises rapidement grâce à l'évolution des sites Web avec Azure].

Rendez votre site Web plus réactif avec le [Cache Redis Azure]. Utilisez-le pour mettre en cache des données stockées sur les bases de données principales et d'autres supports tels que [l'état de session ASP.NET] et le [cache de sortie].

Maintenez une haute disponibilité pour votre site Web à l'aide d'[Azure Traffic Manager]. À l'aide de la méthode de **basculement**, Traffic Manager achemine automatiquement le trafic vers un site secondaire en cas de problèmes sur le site principal.

### Surveillance et analyse

Suivez les performances de votre site Web avec Azure ou des outils tiers. Recevez des alertes sur les événements critiques de votre site Web. Apprenez-en plus sur l'utilisation avec Application Insights ou avec l'analyse de journal Web HDInsight. 

Obtenez un [aperçu rapide] des mesures de performances en temps réel du site Web et des quotas de ressource dans le tableau de bord des sites Web Azure. Pour une vue complète sur la disponibilité, les performances et l'utilisation de votre application, utilisez [Azure Application Insights], un outil rapide et performant pour la résolution des problèmes, le diagnostic et l'analyse de l'utilisation de votre application. Sinon, utilisez un outil tiers comme [New Relic] afin d'obtenir des données avancées pour l'analyse de vos sites Web.

Dans le plan d'hébergement **Standard**, vous pourrez surveiller la réactivité de votre site grâce à des notifications e-mail dès que votre site ne répond plus. Pour plus d'informations, consultez a page [Procédure : recevoir des notifications d'alerte et gérer des règles d'alerte dans Azure].

## Plus de ressources

- [Documentation sur les sites Web Azure](/fr-fr/documentation/services/websites/)
- [Plan de formation pour les sites Web Azure](websites-learning-map.md)
- [Blog Azure Web](/blog/topics/web/)


[Sites Web Azure]:/fr-fr/services/websites/

[Orchard]:/fr-fr/documentation/articles/web-sites-dotnet-orchard-cms-gallery/
[Umbraco]:/fr-fr/documentation/articles/web-sites-gallery-umbraco/
[Drupal]:/fr-fr/documentation/articles/web-sites-php-migrate-drupal/
[WordPress]:/fr-fr/documentation/articles/web-sites-php-web-site-gallery/
  
[MySQL]:/fr-fr/documentation/articles/web-sites-php-mysql-deploy-use-git/
[base de données SQL Azure]:/fr-fr/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/
[FTP]:/fr-fr/documentation/articles/web-sites-deploy/#ftp
[Visual Studio]:/fr-fr/documentation/articles/web-sites-dotnet-get-started/
[Visual Studio Online]:/fr-fr/documentation/articles/cloud-services-continuous-delivery-use-vso/
[Git]:/fr-fr/documentation/articles/web-sites-publish-source-control/

[déployant sur un module de transfert]:/fr-fr/documentation/articles/web-sites-staged-publishing/ 
[publier en continu]:http://rickrainey.com/2014/01/21/continuous-deployment-github-with-azure-web-sites-and-staged-publishing/
[exécuter des tests A/B]:http://blogs.msdn.com/b/tomholl/archive/2014/11/10/a-b-testing-with-azure-websites.aspx

[Déploiement d'une application ASP.NET MVC sécurisée avec une fonctionnalité d'appartenance, OAuth et une base de données SQL vers un site Web Azure]:/fr-fr/develop/net/tutorials/web-site-with-sql-database/

[Azure Media Services]:http://blogs.technet.com/b/cbernier/archive/2013/09/03/windows-azure-media-services-and-web-sites.aspx
[service SendGrid dans Azure Marketplace]:/fr-fr/documentation/articles/sendgrid-dotnet-how-to-send-email/
[Mobile Services]:/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users/
[Notification Hub]:/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users/

[Azure Traffic Manager]:http://www.hanselman.com/blog/CloudPowerHowToScaleAzureWebsitesGloballyWithTrafficManager.aspx
[intégrant votre site Web à Azure CDN]:/fr-fr/documentation/articles/cdn-websites-with-cdn/ 
[nœud CDN]:https://msdn.microsoft.com/library/azure/gg680302.aspx

[mise à l'échelle]:/fr-fr/manage/services/web-sites/how-to-scale-websites/
[portail de gestion Azure]:http://manage.windowsazure.com/
[API de gestion de service]:http://msdn.microsoft.com/library/windowsazure/ee460799.aspx
[scripts PowerShell]:http://msdn.microsoft.com/library/windowsazure/jj152841.aspx
[Troy Hunt]:https://twitter.com/troyhunt
[10 choses que j'ai apprises rapidement grâce à l'évolution des sites Web avec Azure]:http://www.troyhunt.com/2014/09/10-things-i-learned-about-rapidly.html
[Cache Redis Azure]:/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
[l'état de session ASP.NET]:https://msdn.microsoft.com/fr-fr/library/azure/dn690522.aspx
[cache de sortie]:https://msdn.microsoft.com/fr-fr/library/azure/dn798898.aspx

[aperçu rapide]:/fr-fr/manage/services/web-sites/how-to-monitor-websites/
[Azure Application Insights]:http://blogs.msdn.com/b/visualstudioalm/archive/2015/01/07/application-insights-and-azure-websites.aspx
[New Relic]:/fr-fr/develop/net/how-to-guides/new-relic/
[Procédure : recevoir des notifications d'alerte et gérer des règles d'alerte dans Azure]:http://msdn.microsoft.com/library/windowsazure/dn306638.aspx

  
  [gitstaging]:http://www.bradygaster.com/post/multiple-environments-with-windows-azure-web-sites  


<!--HONumber=42-->
