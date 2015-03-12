<properties 
	pageTitle="Création d'applications métier dans Sites Web Azure" 
	description="Ce guide fournit un aperçu technique de l'utilisation de Sites Web Azure pour créer des applications métier intranet. Il aborde notamment les stratégies d'authentification, le relais de bus de services et l'analyse." 
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



# Création d'applications métier dans Sites Web Azure

[Sites Web Azure] est un excellent choix pour les applications métier. Ces applications sont des applications intranet devant être sécurisées pour un usage professionnel interne. Généralement, elles nécessitent une authentification via un annuaire d'entreprise, ainsi qu'un accès ou une intégration aux données et services locaux. 

Les applications métier mobiles des sites Web Azure présentent d'énormes avantages, comme :

-  la mise à l'échelle des charges de travail dynamiques telles qu'une application qui gère des bilans annuels de performance. Mais, dans le cas d'une grande entreprise, ce trafic connaît une très forte augmentation pendant la période des bilans. Azure offre des options de mise à l'échelle permettant à une entreprise d'accroître ses capacités pendant la période de trafic important tout en réalisant des économies avec des capacités réduites durant tout le reste de l'année ; 
-  la possibilité de se concentrer davantage sur le développement de l'application et moins sur l'acquisition et la gestion de l'infrastructure ;
-  une meilleure prise en charge pour que les employés et les partenaires puissent utiliser l'application partout. Les utilisateurs n'ont en effet pas besoin d'être connectés au réseau de l'entreprise pour pouvoir utiliser l'application, et cela évite au service informatique de devoir mettre en place des solutions de proxy inverse complexes. Plusieurs options d'authentification sont disponibles pour protéger l'accès aux applications.

Ci-dessous, vous trouverez un exemple d'application métier fonctionnant sur Sites Web Azure. Il montre ce que vous pouvez faire en associant simplement Sites Web Azure avec d'autres services, et ce avec un investissement technique minimal. **Cliquez sur un élément du schéma pour en savoir plus.** 

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/web-app-notitle.svg" width="100%" height="100%"></object>

<div class="dev-callout">
<strong>Remarque</strong>
<p>Ce guide présente les domaines et les tâches les plus courants s'adaptant aux applications métier. Cependant, Sites Web Azure offre encore d'autres fonctionnalités, que vous pouvez utiliser pour votre implémentation spécifique. Pour les découvrir, consultez également les autres guides concernant la <a href="http://azure.microsoft.com/manage/services/web-sites/global-web-presence-solution-overview/">Présence sur le Web à l'international</a> et les <a href="http://azure.microsoft.com/manage/services/web-sites/digital-marketing-campaign-solution-overview">Campagnes marketing numériques</a>.</p>
</div>

### Utilisation de vos ressources existantes

Utilisez vos ressources Web existantes sur Sites Web Azure grâce à une variété de langages et d'infrastructure.

Vos ressources Web existantes peuvent s'exécuter sur Sites Web Azure, qu'ils soient .NET, PHP, Java, Node.js ou Python. Vous pouvez les transférer vers Sites Web Azure à l'aide de vos outils [FTP] habituels ou votre système de gestion de contrôle de code source. Sites Web Azure prend en charge la publication directe à partir d'options de contrôle du code source populaires, telles que [Visual Studio], [Visual Studio Online], et [Git] (local, GitHub, BitBucket, DropBox, Mercurial, etc.).

### Sécurisation de vos ressources

Sécurisez vos ressources par chiffrement, permettez aux utilisateurs de l'entreprise de s'authentifier sur site ou à distance et autorisez-les à utiliser les ressources. 

Protégez les ressources internes contre les intrusions avec [HTTPS]. Le nom de domaine **\*.azurewebsites.net** est fourni avec un certificat SSL. Si vous utilisez votre domaine personnalisé, vous pouvez appliquer votre certificat SSL à Sites Web Azure. Des frais mensuels (au prorata des heures) seront à régler pour chaque certificat SSL. Pour plus d'informations, consultez la page [Tarification - Sites Web].

[Authentifiez les utilisateurs] via l'annuaire d'entreprise. Sites Web Azure peut authentifier les utilisateurs avec des fournisseurs d'identité sur site, comme qu'Active Directory Federation Services (ADFS), ou avec un locataire Azure Active Directory synchronisé avec le déploiement Active Directory de votre entreprise. Les utilisateurs peuvent accéder à vos propriétés Web dans Sites Web Azure via l'authentification unique lorsqu'ils sont sur site et en déplacement. Les services existants, comme Office 365 ou Windows Intune, utilisent déjà Active Directory Azure. Avec [l'authentification simple], il est très facile d'activer l'authentification avec le même locataire Azure Active Directory pour votre site Web. 

[Permettez aux utilisateurs] d'utiliser les propriétés Web. Avec un minimum de code supplémentaire, vous pouvez transférer votre modèle de codage ASP.NET sur les sites Web Azure à l'aide de l'attribut `[Authorize]`, par exemple. Vous conservez la même souplesse pour le contrôle d'accès précis que pour les applications sur site.

### Connexion à des ressources locales ###

Connectez-vous aux données ou ressources de votre site Web, qu'elles soient sur le cloud par souci de performances ou sur site par souci de conformité. Pour plus d'informations sur la conservation des données dans Azure, consultez le [centre de gestion de la confidentialité Azure]. 

Vous pouvez choisir parmi les différents serveurs principaux de base de données dans Azure pour répondre aux besoins de votre site Web, y compris la [base de données SQL Azure] et [MySQL]. La protection de vos données dans Azure rapproche géographiquement vos données de votre site Web et optimise ses performances.

Toutefois, il se peut que votre entreprise ait besoin que ses données soient conservées sur site. Sites Web Azure vous permet de configurer facilement une [connexion hybride] pour vos ressources sur site comme une base de données principale. Si vous souhaitez une gestion unifiée de vos connexions locales, vous pouvez intégrer de nombreux sites Web Azure sur un réseau [Azure Virtual Network] disposant d'un VPN de site à site. Vous pouvez ensuite accéder aux ressources locales comme si vos sites Web Azure étaient sur site. [Pizzeria - Connexion de sites Web à un réseau local via Service Bus][enterprisepizza]

### Optimisation

Optimisez votre application métier grâce à la mise à l'échelle automatique, à la mise en cache avec le Cache Redis Azure, à l'exécution des tâches en arrière-plan avec WebJobs et au maintien d'une haute disponibilité avec Azure Traffic Manager.

Grâce à leur capacité de [mise à l'échelle], les sites Web Azure peuvent répondre aux besoins de votre application métier, quelle que soit la taille de votre charge de travail. Mettez votre site Web à l'échelle manuellement via le [portail de gestion Azure], par programmation via l'[API de gestion de service] ou [scripts PowerShell], ou automatiquement via la fonctionnalité de mise à l'échelle automatique. Dans le plan d'hébergement **Standard**, la mise à l'échelle automatique vous permet de faire évoluer un site Web automatiquement en fonction de l'utilisation du processeur. Pour une utilisation optimale, consultez l'article de [Troy Hunt] [10 choses que j'ai apprises rapidement grâce à l'évolution des sites Web avec Azure].

Rendez votre site Web plus réactif avec le [Cache Redis Azure]. Utilisez-le pour mettre en cache des données stockées sur les bases de données principales et d'autres supports tels que [l'état de session ASP.NET] et le [cache de sortie].

Maintenez une haute disponibilité pour votre site Web à l'aide d'[Azure Traffic Manager]. À l'aide de la méthode de **basculement**, Traffic Manager achemine automatiquement le trafic vers un site secondaire en cas de problèmes sur le site principal.

### Surveillance et analyse

Suivez les performances de votre site Web avec Azure ou des outils tiers. Recevez des alertes sur les événements critiques de votre site Web. Apprenez-en plus sur l'utilisation avec Application Insights ou avec l'analyse de journal Web HDInsight. 

Obtenez un [aperçu rapide] des mesures de performances en temps réel du site Web et des quotas de ressource dans le tableau de bord des sites Web Azure. Pour une vue complète sur la disponibilité, les performances et l'utilisation de votre application, utilisez [Azure Application Insights], un outil rapide et performant pour la résolution des problèmes, le diagnostic et l'analyse de l'utilisation de votre application. Sinon, utilisez un outil tiers comme [New Relic] afin d'obtenir des données avancées pour l'analyse de vos sites Web.

Dans le plan d'hébergement **Standard**, vous pourrez surveiller la réactivité de votre site grâce à des notifications e-mail dès que votre site ne répond plus. Pour plus d'informations, consultez a page [Procédure : recevoir des notifications d'alerte et gérer des règles d'alerte dans Azure].

## Plus de ressources

- [Documentation sur les sites Web Azure](/fr-fr/documentation/services/websites/)
- [Plan de formation pour les sites Web Azure](/fr-fr/documentation/articles/websites-learning-map/)
- [Blog Azure Web](/blog/topics/web/)



[Sites Web Azure]:/fr-fr/services/websites/

[FTP]:/fr-fr/documentation/articles/web-sites-deploy/#ftp
[Visual Studio]:/fr-fr/documentation/articles/web-sites-dotnet-get-started/
[Visual Studio Online]:/fr-fr/documentation/articles/cloud-services-continuous-delivery-use-vso/
[Git]:/fr-fr/documentation/articles/web-sites-publish-source-control/

[HTTPS]:/fr-fr/documentation/articles/web-sites-configure-ssl-certificate/
[Tarification - Sites Web]:/fr-fr/pricing/details/web-sites/#service-ssl
[Authentifiez les utilisateurs]:/fr-fr/documentation/articles/web-sites-authentication-authorization/
[l'authentification simple]:/blog/2014/11/13/azure-websites-authentication-authorization/
[Permettez aux utilisateurs]:/fr-fr/documentation/articles/web-sites-authentication-authorization/

[centre de gestion de la confidentialité Azure]:/fr-fr/support/trust-center/
[MySQL]:/fr-fr/documentation/articles/web-sites-php-mysql-deploy-use-git/
[base de données SQL Azure]:/fr-fr/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/
[connexion hybride]:/fr-fr/documentation/articles/web-sites-hybrid-connection-get-started/
[Azure Virtual Network]:/fr-fr/documentation/articles/web-sites-integrate-with-vnet/

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



<!--HONumber=42-->
