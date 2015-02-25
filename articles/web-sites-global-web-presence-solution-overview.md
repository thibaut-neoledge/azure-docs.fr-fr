<properties 
	pageTitle="Création d'une présence sur le Web à l'international dans Sites Web Azure" 
	description="Ce guide offre un aperçu technique de l'hébergement du site (.COM) de votre organisation sur Sites Web Azure. Il aborde notamment le déploiement, les domaines personnalisés, SSL et l'analyse." 
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


# Création d'une présence sur le Web à l'international dans Sites Web Azure

[Sites Web Azure] a toutes les fonctionnalités dont vous avez besoin pour établir une présence Web globale de votre site .COM. Quelle que soit la taille de votre organisation, vous avez besoin d'une plateforme robuste, sécurisée et évolutive pour votre entreprise, de la conscience de votre marque et de vos communications client. Sites Web Azure peut vous aider à maintenir votre marque et votre identité avec la continuité d'activité soutenue de Microsoft.

> [AZURE.NOTE] Si vous voulez prendre en main Azure Web Sites avant de créer un compte, accédez à <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>, où vous pouvez immédiatement et gratuitement créer un site de départ ASP.NET de courte durée dans Azure Web Sites. Aucune carte de crédit n'est requise, vous ne prenez aucun engagement.

Voici un exemple de site Web .COM s'exécutant sur Sites Web Azure. Ceci montre comment cela peut se faire simplement en combinant Sites Web Azure avec d'autres services avec des investissements techniques minimes. **Cliquez sur un élément dans la topographie pour en savoir plus à ce sujet.** 

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/corp-website-visio.svg" width="100%" height="100%"></object>

> [WACOM.NOTE]
> Ce guide présente les domaines et les tâches parmi les plus courants s'adaptant à l'exécution de sites .COM publics dans Sites Web Azure. Toutefois, il existe d'autres solutions courantes que vous pouvez implémenter dans Sites Web Azure. Pour les découvrir, consultez également les autres guides concernant les <a href="http://www.windowsazure.com/fr-fr/manage/services/web-sites/digital-marketing-campaign-solution-overview">campagnes marketing numériques</a> et les <a href="http://www.windowsazure.com/fr-fr/manage/services/web-sites/business-application-solution-overview">applications métier</a>.

### Création intégrale ou apport de ressources existantes

Créez rapidement des sites à partir d'un CMS populaire dans la galerie ou mettez vos ressources Web existantes sur Sites Web Azure à partir d'une variété de langages et d'infrastructures.

La galerie Azure fournit des modèles à partir des systèmes de gestion de contenu (CMS) populaires de site Web, tel que [Orchard], [Umbraco], [Drupal], et [WordPress]. Vous pouvez créer un site Web à l'aide de la variante préférée de votre CMS. Vous pouvez choisir parmi les principaux serveurs de base de données différents pour répondre à vos besoins, y compris [Base de données SQL Azure] et [MySQL].

Vos ressources Web existantes peuvent s'exécuter sur Sites Web Azure, qu'ils soient .NET, PHP, Java, Node.js ou Python. Vous pouvez les déplacer vers Sites Web Azure à l'aide de outils [FTP] habituels ou votre système de gestion de contrôle de code source. Sites Web Azure prend en charge la publication directe à partir des options de contrôle de code source populaires, telles que [Visual Studio], [Visual Studio Online], et [Git] (Local, GitHub, BitBucket, DropBox, Mercurial, etc.).

### Publier de façon fiable

Publiez votre site Web de manière fiable en publiant directement en continu à partir de votre système de contrôle de code source existant et en testant votre contenu en direct. 

Lors des phases de planification, de prototypage et de développement initial d'un site, vous pouvez travailler sur des versions fonctionnelles réelles du site de campagne avant de le mettre en ligne en vous [déploiement vers un emplacement intermédiaire] de votre Site Web Azure. En intégrant le contrôle de code source avec Sites Web Azure, vous pouvez [publication en continu] vers un intermédiaire d'emplacement et basculer en production sans interruption lorsque vous êtes prêt pour cela. Si une erreur se produit sur le site de production, vous pouvez également immédiatement changer une version précédente de votre site de façon automatique. 

De plus, lorsque vous planifiez les modifications apportées à un site Web actif, vous pouvez facilement [exécution de tests A/B] sur les mises à jour proposées à l'aide de la fonctionnalité Test in Production et analyser le comportement réel d'utilisateurs pour vous aider à prendre des décisions avisées sur la conception de site.

### Marque et sécurité

Utilisez le domaine de sites Web gratuitement ou mappez à votre nom de domaine enregistré, puis sécurisez votre marque avec votre certificat SSL signé par une autorité de certification.

Le domaine **\*.azurewebsites.net** est gratuit lorsque vous exécutez votre site Web sur Sites Web Azure. Vous pouvez également mapper votre site Web à un [domaine personnalisé] (par exemple, contoso.com), que vous avez obtenu à partir de n'importe quel Registre DNS, tel que GoDaddy.

Si vous collectez toutes les informations utilisateur, effectuez un commerce électronique ou gérez d'autres données sensibles, vous pouvez protéger votre réputation et vos clients avec [HTTPS]. Le nom de domaine **\*.azurewebsites.net** est déjà fourni avec un certificat SSL, et si vous utilisez votre domaine personnalisé, vous pouvez utiliser votre certificat SSL pour celui-ci sur Sites Web Azure. Il existe une facturation mensuelle (au prorata des heures) associée à chaque certificat SSL. Pour plus d'informations, consultez la rubrique [Détails de la tarification des sites Web].

### Pensez à l'international

Pensez à l'international en servant des sites régionaux avec Azure Traffic Manager et en livrant des contenus ultra-rapide avec Azure CDN.

Pour servir des clients étrangers dans leurs régions respectives, utilisez [Azure Traffic Manager] pour guider les visiteurs vers un site régional qui fournit les meilleures performances. Vous pouvez également répartir uniformément la charge du site entre plusieurs copies de votre site Web hébergé dans plusieurs régions.

Délivrez votre contenu statique en un éclair aux utilisateurs internationaux en [intégration de votre site Web à Azure CDN]. Azure CDN met en cache le contenu statique dans le [nœud CDN] le plus proche de l'utilisateur, ce qui réduit la latence et les connexions pour votre site Web.

### Optimisez

Optimisez votre site .COM en le mettant à l'échelle automatiquement grâce à la Mise à l'échelle automatique, en mettant en cache avec le Cache Redis Azure, en exécutant des tâches en arrière-plan avec WebJobs et en maintenant une haute disponibilité avec Azure Traffic Manager.

Les capacités de Sites Web Azure à [multiplication et diversification] répond aux besoins de votre site .COM, quelle que soit votre charge de travail. Mise à l'échelle manuelle de votre site Web via le [portail de gestion Azure], par programmation via l'[API de gestion des services] ou les [scripts PowerShell], ou automatique via la fonctionnalité de mise à l'échelle. Dans le plan d'hébergement **Standard**, la mise à l'échelle vous permet de faire évoluer un site Web automatiquement en fonction de l'utilisation du processeur. Pour les meilleures pratiques, consultez l'article de [Troy Hunt] : [10 choses que j'ai appris rapidement sur la mise à l'échelle des sites Web avec Azure].

Rendez votre site Web plus réactif avec le [Cache Redis Azure]. Utilisez-le pour mettre en cache des données à partir de bases de données principales et d'autres choses telles que les [états de session ASP.NET] et le [cache de sortie].

Maintenez une grande disponibilité de votre site Web à l'aide d'[Azure Traffic Manager]. À l'aide de la méthode de **basculement**, Traffic Manager achemine automatiquement le trafic vers un site secondaire s'il existe un problème sur le site principal.

### Surveillance et analyse

Tenez-vous informé sur les performances de votre site Web avec Azure ou des outils tiers. Recevez des alertes sur des événements critiques de site Web. Découvrez un aperçu de vos utilisateurs avec Application Insight ou grâce aux analyses de journaux Web avec HDInsight. 

Obtenez un [aperçu rapide] de mesures de performances du site Web en cours et des quotas de ressource dans le tableau de bord de Sites Web Azure. Pour une vue 360° de votre application en termes de disponibilité, de performances et d'utilisation, utilisez [Azure Application Insights] pour vous donner un dépannage rapide et puissant, des diagnostics et des analyses d'utilisation. Sinon, utilisez un outil tiers comme [New Relic] pour fournir les données d'analyse avancées pour vos sites Web.

Dans le plan d'hébergement **Standard**, analysez la réactivité du site et recevez des notifications par courrier électronique chaque fois que votre site ne répond plus. Pour plus d'informations, consultez la rubrique [Procédures : réception de notifications d'alerte et gestion des règles d'alerte dans Azure].

### Utilisez des contenus multimédias riches et accédez à tous les périphériques

Rendez votre site .COM attrayant avec du contenu multimédia riche, tel que :

-  Téléchargez et diffusez des vidéos à l'international avec [Azure Media Services]
-  Envoyez des courriers électroniques aux utilisateurs avec le [service SendGrid d'Azure Marketplace]

## Plus de ressources

- [Documentation de Sites Web Azure](/fr-fr/documentation/services/websites/)
- [Plan de formation pour Sites Web Azure](/fr-fr/documentation/articles/websites-learning-map/)
- [Blog Web Azure](/blog/topics/web/)



[Sites Web Azure]:/fr-fr/services/websites/

[Orchard]:/fr-fr/documentation/articles/web-sites-dotnet-orchard-cms-gallery/
[Umbraco]:/fr-fr/documentation/articles/web-sites-gallery-umbraco/
[Drupal]:/fr-fr/documentation/articles/web-sites-php-migrate-drupal/
[WordPress]:/fr-fr/documentation/articles/web-sites-php-web-site-gallery/
[MySQL]:/fr-fr/documentation/articles/web-sites-php-mysql-deploy-use-git/
[Base de données SQL Azure]:/fr-fr/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/
[FTP]:/fr-fr/documentation/articles/web-sites-deploy/#ftp
[Visual Studio]:/fr-fr/documentation/articles/web-sites-dotnet-get-started/
[Visual Studio Online]:/fr-fr/documentation/articles/cloud-services-continuous-delivery-use-vso/
[Git]:/fr-fr/documentation/articles/web-sites-publish-source-control/

[déploiement vers un emplacement intermédiaire]:/fr-fr/documentation/articles/web-sites-staged-publishing/ 
[publication en continu]:http://rickrainey.com/2014/01/21/continuous-deployment-github-with-azure-web-sites-and-staged-publishing/
[exécution de tests A/B]:http://blogs.msdn.com/b/tomholl/archive/2014/11/10/a-b-testing-with-azure-websites.aspx

[domaine personnalisé]:/fr-fr/documentation/articles/web-sites-custom-domain-name/
[HTTPS]:/fr-fr/documentation/articles/web-sites-configure-ssl-certificate/
[Détails de la tarification des sites Web]:/fr-fr/pricing/details/web-sites/#service-ssl

[Azure Traffic Manager]:http://www.hanselman.com/blog/CloudPowerHowToScaleAzureWebsitesGloballyWithTrafficManager.aspx
[intégration de votre site Web à Azure CDN]:/fr-fr/documentation/articles/cdn-websites-with-cdn/ 
[Nœud CDN]:https://msdn.microsoft.com/library/azure/gg680302.aspx

[multiplication et diversification]:/fr-fr/manage/services/web-sites/how-to-scale-websites/
[Portail de gestion Azure]:http://manage.windowsazure.com/
[API de gestion de service]:http://msdn.microsoft.com/fr-fr/library/windowsazure/ee460799.aspx
[scripts PowerShell]:http://msdn.microsoft.com/fr-fr/library/windowsazure/jj152841.aspx
[Troy Hunt]:https://twitter.com/troyhunt
[10 choses que j'ai appris rapidement sur la mise à l'échelle des sites Web avec Azure]:http://www.troyhunt.com/2014/09/10-things-i-learned-about-rapidly.html
[Cache Redis Azure]:/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
[états de session ASP.NET]:https://msdn.microsoft.com/fr-fr/library/azure/dn690522.aspx
[cache de sortie]:https://msdn.microsoft.com/fr-fr/library/azure/dn798898.aspx

[aperçu rapide]:/fr-fr/manage/services/web-sites/how-to-monitor-websites/
[Azure Application Insights]:http://blogs.msdn.com/b/visualstudioalm/archive/2015/01/07/application-insights-and-azure-websites.aspx
[New Relic]:/fr-fr/develop/net/how-to-guides/new-relic/
[Procédures : réception de notifications d'alerte et gestion des règles d'alerte dans Azure]:http://msdn.microsoft.com/library/windowsazure/dn306638.aspx

[Azure Media Services]:http://blogs.technet.com/b/cbernier/archive/2013/09/03/windows-azure-media-services-and-web-sites.aspx
[service SendGrid d'Azure Marketplace]:/fr-fr/documentation/articles/sendgrid-dotnet-how-to-send-email/



<!--HONumber=42-->
