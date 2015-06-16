<properties 
	pageTitle="Créer une application web métier sur Azure App Service" 
	description="Ce guide fournit un aperçu technique de l'utilisation d'Azure App Service Web Apps pour créer des applications métier intranet. Il aborde notamment les stratégies d'authentification, le relais de bus de services et l'analyse." 
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
	ms.date="04/08/2015" 
	ms.author="cephalin"/>



# Créer une application web métier sur Azure App Service

[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps représente un choix idéal pour les applications métier. Il s'agit d'applications intranet qui doivent être sécurisées pour une utilisation professionnelle interne. Elles nécessitent généralement une authentification, le plus souvent par rapport à un annuaire d'entreprise, et une forme d'accès ou d'intégration aux données et services locaux. 

Voici les principaux avantages du déplacement des applications métier vers App Service Web Apps :

-  Montée et descente en puissance selon les charges de travail dynamiques, par exemple avec une application qui gère les analyses de performances annuelles. Dans le cas d'une grande entreprise, le trafic connaît une très forte augmentation pendant la période d'analyse. Azure offre des options de mise à l'échelle permettant à une entreprise d'accroître ses capacités pendant la période de fort trafic tout en réalisant des économies avec des capacités réduites pendant tout le reste de l'année. 
-  Intérêt porté sur le développement d'applications, plutôt que sur l'acquisition et la gestion d'infrastructures
-  Meilleure prise en charge des employés et partenaires pour une utilisation de l'application à partir de n'importe quel endroit. Les utilisateurs n'ont en effet pas besoin d'être connectés au réseau de l'entreprise pour pouvoir utiliser l'application, et cela évite au service informatique de devoir mettre en place des solutions de proxy inverse complexes. Plusieurs options d'authentification sont disponibles pour protéger l'accès aux applications ;

Voici un exemple d'une application métier exécutée sur App Service Web Apps. Il montre ce que vous pouvez faire en associant simplement des applications web à d'autres services, moyennant des investissements techniques minimes. **Pour en savoir plus sur un élément, cliquez dessus dans la topographie.** 

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/web-app-notitle.svg" width="100%" height="100%"></object>

> [AZURE.NOTE]
> Ce guide présente les domaines et les tâches parmi les plus courants s'adaptant aux applications métier. Cependant, Azure App Service Web Apps offre encore d'autres fonctionnalités, que vous pouvez utiliser pour votre implémentation. Pour les découvrir, consultez les guides relatifs à la [Présence sur le Web à l'international](web-sites-global-web-presence-solution-overview.md) et aux [Campagnes de marketing numérique](web-sites-digital-marketing-application-solution-overview.md).

## Apporter des ressources existantes

Apportez vos ressources web existantes dans App Service Web Apps à partir d'une variété de langages et d'infrastructures.

Vos ressources web existantes peuvent s'exécuter sur App Service Web Apps, qu'il s'agisse de ressources .NET, PHP, Java, Node.js ou Python. Vous pouvez les déplacer vers Web Apps à l'aide de vos outils [FTP] favoris ou de votre système de gestion de contrôle de code source. Web Apps prend en charge la publication directe à partir d'options de contrôle de code source populaires, tels que [Visual Studio], [Visual Studio Online] et [Git] (local, GitHub, BitBucket, DropBox, Mercurial, etc.).

## Sécuriser vos ressources

Sécurisez les ressources par chiffrement, authentifiez les utilisateurs d'entreprise locaux et distants, et autorisez l'utilisation des ressources pour ces utilisateurs. 

Protégez les ressources internes contre les écoutes clandestines avec [HTTPS]. Le nom de domaine ***.azurewebsites.net** est déjà fourni avec un certificat SSL, et si vous utilisez votre domaine personnalisé, vous pouvez apporter votre certificat SSL pour celui-ci dans App Service Web Apps. Une facturation mensuelle (au prorata des heures) est appliquée pour chaque certificat SSL. Pour plus d'informations, consultez la page [Détails de la tarification - App Service].

[Authentifiez les utilisateurs] par rapport à l'annuaire d'entreprise. App Service Web Apps peut authentifier les utilisateurs avec des fournisseurs d'identité locaux tels que les services AD FS (Active Directory Federation Services) ou avec un client Azure Active Directory qui a été synchronisé avec votre déploiement Active Directory d'entreprise. Les utilisateurs peuvent accéder à vos propriétés web dans Web Apps via l'authentification unique lorsqu'ils sont sur site ou sur le terrain. Les services existants comme Office 365 ou Windows Intune utilisent déjà Azure Active Directory. La fonctionnalité d'[authentification simple] permet d'activer aisément l'authentification avec le même client Azure Active Directory pour votre application web. 

[Autorisez les utilisateurs] à utiliser les propriétés web. Avec un minimum de code supplémentaire, vous pouvez apporter le même modèle de codage ASP.NET local dans App Service Web Apps en utilisant la décoration `[Authorize]`, par exemple. Vous conservez un contrôle d'accès précis d'une grande souplesse, équivalent à celui des applications que vous gérez sur site.

## Se connecter aux ressources locales ##

Connectez-vous aux données et ressources de votre application web, qu'elles soient dans le cloud pour les performances ou sur site pour la conformité. Pour plus d'informations sur la conservation des données dans Azure, consultez la rubrique [Centre de gestion de la confidentialité Azure]. 

Selon les exigences de votre application web, vous pouvez choisir parmi différents serveurs principaux de base de données dans Azure, y compris une [base de données SQL Azure] et [MySQL]. Lorsque vos données sont conservées en toute sécurité dans Azure, elles sont géographiquement proches de votre application web, ce qui optimise ses performances.

Toutefois, votre entreprise peut avoir besoin de conserver ses données localement. App Service Web Apps vous permet de configurer facilement une [connexion hybride] vers votre ressource locale, par exemple un serveur principal de base de données. Si vous souhaitez une gestion unifiée de vos connexions locales, vous devez intégrer de nombreuses applications web à un réseau [Azure Virtual Network] disposant d'un VPN de site à site. Vous pouvez ensuite accéder aux ressources locales comme si vos applications web étaient présentes sur site.

## Optimiser

Optimisez votre application métier via la mise à l'échelle automatique, la mise en cache avec le Cache Redis Azure, l'exécution de tâches en arrière-plan avec WebJobs et l'assurance d'une haute disponibilité avec Azure Traffic Manager.

La capacité d'App Service Web Apps à [monter en puissance] répond aux besoins de votre application métier, quelle que soit l'envergure de votre charge de travail. Faites monter en puissance votre application web manuellement via le [portail de gestion Azure], par programme via l'[API de gestion de service] ou l'exécution de [scripts PowerShell], ou automatiquement via la fonctionnalité de mise à l'échelle automatique. Au niveau **Standard**, la mise à l'échelle automatique vous permet de faire monter en puissance une application web automatiquement selon l'utilisation de l'UC. Pour connaître les meilleures pratiques, consultez le document de [Troy Hunt]:[10 choses que j'ai apprises pour mettre à l'échelle rapidement des applications web avec Azure] (en anglais).

Renforcez la réactivité de votre application web avec le [Cache Redis Azure]. Utilisez-le pour mettre en cache les données de vos bases de données principales et d'autres éléments, notamment l'[état de session ASP.NET] et le [cache de sortie].

Assurez une haute disponibilité de votre application web à l'aide d'[Azure Traffic Manager]. Grâce à la méthode de **basculement**, Traffic Manager achemine automatiquement le trafic vers un site secondaire en cas de problème sur le site principal.

## Surveiller et analyser

Restez informé des performances de votre application web avec Azure ou des outils tiers. Recevez des alertes sur les événements critiques concernant les applications web. Obtenez facilement des commentaires des utilisateurs grâce à Application Insights ou à l'analyse des journaux web de HDInsight. 

Obtenez un [aperçu] des métriques de performance en cours de l'application web et des quotas de ressources dans le panneau de l'application web du [portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715). Pour une vue complète de la disponibilité, des performances et de l'utilisation de votre application, utilisez [Azure Application Insights] qui vous fournit rapidement de précieuses informations de dépannage, de diagnostic et d'utilisation. Vous pouvez également utiliser un outil tiers comme [New Relic], qui fournit des données de surveillance avancées pour vos applications web.

Au niveau **Standard**, surveillez la réactivité de l'application et recevez des notifications par courrier électronique dès lors que votre application cesse de répondre. Pour plus d'informations, consultez la page [Réception recevoir des notifications d'alerte et gérer des règles d'alerte dans Azure].

## Autres ressources

- [App Service Web Apps Documentation](/services/app-service/web/)
- [Plan d'apprentissage pour Azure App Service Web Apps](websites-learning-map.md)
- [Azure Web Blog](/blog/topics/web/)

>[AZURE.NOTE] Si vous voulez vous familiariser avec Azure App Service avant d'ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement et gratuitement une application de départ temporaire dans App Service. Aucune carte de crédit n'est requise ; vous ne prenez aucun engagement.

## Changements apportés
* Pour obtenir des informations détaillées sur le passage de Sites Web à App Service, consultez : [Azure App Service et son impact sur les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)
* Pour obtenir des informations détaillées sur le passage de l'ancien portail au nouveau portail, consultez : [Référence pour la navigation dans le portail en version préliminaire](http://go.microsoft.com/fwlink/?LinkId=529715)


[Azure App Service]: /services/app-service/web/

[FTP]:web-sites-deploy.md#ftp
[Visual Studio]:web-sites-dotnet-get-started.md
[Visual Studio Online]:../cloud-services-continuous-delivery-use-vso.md
[Git]:web-sites-publish-source-control.md

[HTTPS]:web-sites-configure-ssl-certificate.md
[Détails de la tarification - App Service]: /pricing/details/app-service/#ssl-connections
[Authentifiez les utilisateurs]:web-sites-authentication-authorization.md
[Authentification simple]:/blog/2014/11/13/azure-websites-authentication-authorization/
[Autorisez les utilisateurs]:web-sites-authentication-authorization.md

[Centre de gestion de la confidentialité Azure]:/support/trust-center/
[MySQL]:web-sites-php-mysql-deploy-use-git.md

[Connexion hybride]:web-sites-hybrid-connection-get-started.md
[Azure Virtual Network]:web-sites-integrate-with-vnet.md
[base de données SQL Azure]:web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md
[monter en puissance]:web-sites-scale.md
[Portail de gestion Azure]:http://manage.windowsazure.com/
[API de gestion de service]:http://msdn.microsoft.com/library/windowsazure/ee460799.aspx
[Scripts PowerShell]:http://msdn.microsoft.com/library/windowsazure/jj152841.aspx
[Troy Hunt]:https://twitter.com/troyhunt
[10 choses que j'ai apprises pour mettre à l'échelle rapidement des applications web avec Azure]:http://www.troyhunt.com/2014/09/10-things-i-learned-about-rapidly.html
[Cache Redis Azure]:/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
[État de session ASP.NET]:https://msdn.microsoft.com/library/azure/dn690522.aspx
[Cache de sortie]:https://msdn.microsoft.com/library/azure/dn798898.aspx

[Aperçu]:web-sites-monitor.md
[Azure Application Insights]:http://blogs.msdn.com/b/visualstudioalm/archive/2015/01/07/application-insights-and-azure-websites.aspx
[New Relic]:../store-new-relic-cloud-services-dotnet-application-performance-management.md
[Réception recevoir des notifications d'alerte et gérer des règles d'alerte dans Azure]:http://msdn.microsoft.com/library/windowsazure/dn306638.aspx



<!--HONumber=52--> 