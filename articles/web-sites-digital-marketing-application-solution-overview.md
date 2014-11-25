<properties linkid="websites-digital-marketing" urlDisplayName="Resources" pageTitle="Create a Digital Marketing Campaign on Azure Websites" metaKeywords="" description="This guide provides a technical overview of how to use Azure Websites to create digital marketing campaigns. This includes deployment, social media integration, scaling strategies, and monitoring." metaCanonical="" services="" documentationCenter="" title="Create a Digital Marketing Campaign on Azure Websites" authors="jroth" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jroth" />

# Créer des campagnes marketing numériques dans Sites Web Azure

Ce guide fournit un aperçu technique de l'utilisation de Sites Web Azure pour créer des campagnes marketing numériques. Une campagne marketing numérique est une entité à durée de vie limitée, conçue pour promouvoir un objectif marketing à court terme. Il y a deux scénarios principaux à envisager. Dans le premier scénario, une agence de marketing crée et gère une campagne pour un client pendant toute la durée de la promotion. Dans le second scénario, l'agence de marketing crée une campagne marketing numérique, puis transfère les droits de propriété sur les ressources associées au client. Ce dernier lance et gère ensuite seul la campagne.

[Sites Web Azure][Sites Web Azure] est approprié pour ces deux scénarios. Il permet une création rapide, prend en charge plusieurs infrastructures et langages, s'adapte aux besoins des utilisateurs et accepte un grand nombre de systèmes de déploiement et de contrôle de code source. Avec Azure, vous avez également accès à d'autres services Azure, tels que Media Services, utiles dans le cadre d'une campagne marketing.

Bien qu'il soit possible d'utiliser [Azure Cloud Services][Azure Cloud Services] ou [Azure Virtual Machines][Azure Virtual Machines] pour héberger des sites web, ces services ne constituent pas la meilleure option pour ce scénario, sauf s'ils proposent une fonctionnalité non proposée par Sites Web Azure. Pour plus d'informations sur ces options, consultez la rubrique [Comparaison entre Sites Web Azure, Azure Cloud Services et Azure Virtual Machines][Comparaison entre Sites Web Azure, Azure Cloud Services et Azure Virtual Machines]

Les questions couvertes par ce guide sont les suivantes :

-   [Déploiement de sites Web existants][Déploiement de sites Web existants]
-   [Intégration au sein de médias sociaux][Intégration au sein de médias sociaux]
-   [Évolution en fonction de la demande][Évolution en fonction de la demande]
-   [Intégration à d'autres services][Intégration à d'autres services]
-   [Surveillance de la campagne][Surveillance de la campagne]

<div class="dev-callout">
<strong>Remarque</strong>
<p>Ce guide pr&eacute;sente les domaines et les t&acirc;ches parmi les plus courants s'adaptant au d&eacute;veloppement de sites .COM publics. Cependant, Sites Web Azure offre encore d'autres fonctionnalit&eacute;s, que vous pouvez utiliser pour votre impl&eacute;mentation sp&eacute;cifique. Pour les d&eacute;couvrir, consultez les guides relatifs &agrave; la <a href="http://www.windowsazure.com/fr-fr/manage/services/web-sites/global-web-presence-solution-overview/">Pr&eacute;sence sur le Web &agrave; l'international</a> et aux <a href="http://www.windowsazure.com/fr-fr/manage/services/web-sites/business-application-solution-overview">Applications m&eacute;tier</a>.</p>
</div>

## <a name="deployexisting"></a>Déployer des sites web existants

Dans notre scénario de présence sur le web à l'international, nous avons passé en revue les différentes options de création et de déploiement d'un site web. Si Sites Web Azure ne vous est pas familier, nous vous invitons à [consulter ces informations][consulter ces informations]. Si vous créez fréquemment des campagnes marketing numériques, vous disposez peut-être de composants Web à personnaliser selon les besoins. Dans cette section, nous nous intéresserons aux options de déploiement de différents types de site web à partir du contrôle de code source.

Si vous travaillez avec des composants Web réutilisables, nous vous conseillons fortement d'envisager l'adoption d'un système de contrôle de code source, si tel n'est pas encore le cas. Cela permet de conserver des modèles de solutions Web communes pouvant être associées et personnalisées. Websites permet en outre une synchronisation avec un grand nombre de référentiels de code source. Dans l'onglet **Tableau de bord**, cliquez sur le lien **Configurer le déploiement à partir du contrôle de code source**.

![DigitalMarketingDeploy1][DigitalMarketingDeploy1]

Une boîte de dialogue s'affiche alors, proposant plusieurs options de contrôle de code source, notamment des systèmes de contrôle de code source complets, tels que TFS, ainsi que des solutions de déploiement simples, telles que Dropbox.

![DigitalMarketingDeploy2][DigitalMarketingDeploy2]

Vous pouvez utiliser différentes techniques de contrôle de code source pour gérer un nouveau projet basé sur un matériel de base existant. Vous pouvez, par exemple, copier un référentiel de base précédemment sauvegardé pour ce projet ou créer une branche, afin de garder une trace des personnalisations réalisées. La rubrique [Environnements multiples avec Sites Web Azure][Environnements multiples avec Sites Web Azure] fournit un bon exemple d'utilisation de branches pour la gestion de différents déploiements à partir d'un même référentiel de contrôle de code source. Elle explique comment utiliser les branches git à des fins de gestion des environnements intermédiaires et de production.

Une fois votre site web connecté au système de contrôle de code source, vous pouvez configurer et suivre des déploiements à partir du portail. Pour plus d'informations sur l'utilisation du contrôle de code source avec Websites, consultez [Publication à partir du contrôle de code source dans Sites Web Azure][Publication à partir du contrôle de code source dans Sites Web Azure].

En cas d'utilisation de composants web existants, il est également important de pouvoir héberger un grand nombre de types de sites web différents. Sous l'onglet **Configuration**, vous pouvez sélectionner la prise en charge .NET et PHP de votre site web.

![DigitalMarketingFrameworkVersions][DigitalMarketingFrameworkVersions]

Outre ces options de configuration, Websites prend automatiquement en charge Python 2.7 et Node.js. La version Node.js par défaut est la version 0.10.5.

Sites Web Azure présente un autre avantage : la vitesse de déploiement des sites intermédiaires sur le web. Lors des phases de planification, de prototypage et de développement initial d'un site, l'agence et le client peuvent travailler sur des versions fonctionnelles réelles du site de campagne avant de le mettre en ligne. Une fois le site prêt pour production, l'agence peut gérer le déploiement en production pour le client ou lui transmettre l'ensemble des composants Web à des fins de déploiement et de gestion.

## <a name="socialmedia"></a>Intégration au sein de médias sociaux

La plupart des campagnes marketing numériques exploitent les réseaux sociaux, tels que Facebook ou Twitter. L'une des méthodes d'intégration possibles consiste à utiliser les identités des médias sociaux à des fins d'authentification. Pour découvrir un exemple de cette approche avec une application ASP.NET, consultez la rubrique [Déploiement d'une application ASP.NET MVC sécurisée avec une fonctionnalité d'appartenance, OAuth et une base de données SQL vers un site Web Azure][Déploiement d'une application ASP.NET MVC sécurisée avec une fonctionnalité d'appartenance, OAuth et une base de données SQL vers un site Web Azure].

Cependant, nombre de campagnes marketing numériques vont au-delà de l'authentification et font de l'intégration aux médias sociaux un élément clé de leur stratégie. Les réseaux sociaux incluent généralement une section dédiée aux développeurs, qui explique comment intégrer des applications à leurs services. Les services incluant une API REST peuvent être utilisés à partir de la quasi-totalité des infrastructures Web. Toutefois, les sites intègrent souvent des informations propres aux différents langages. Par exemple, Twitter fournit [la liste des bibliothèques disponibles prenant en charge l'API Twitter][la liste des bibliothèques disponibles prenant en charge l'API Twitter], notamment pour les langages .NET, Node.js et PHP. Ce n'est qu'un exemple parmi d'autres, et vous devez rechercher ce type de directives de développement sur chaque site de réseau social ciblé.

Pour les développeurs ASP.NET ciblant Facebook, Visual Studio propose un modèle d'application Facebook MVC 4.

![DigitalMarketingFacebook][DigitalMarketingFacebook]

Pour plus d'informations sur l'utilisation de ce modèle avec Websites, consultez [Création d'une application Facebook à l'aide d'un modèle Facebook ASP.NET MVC et hébergement gratuit de l'application sur Sites Web Azure][Création d'une application Facebook à l'aide d'un modèle Facebook ASP.NET MVC et hébergement gratuit de l'application sur Sites Web Azure]. Pour plus d'informations sur la procédure à suivre et pour découvrir un exemple d'application, consultez les rubriques [Application d'anniversaire Facebook ASP.NET MVC][Application d'anniversaire Facebook ASP.NET MVC] et [Nouveau modèle d'application Facebook et nouvelle bibliothèque pour ASP.NET MVC][Nouveau modèle d'application Facebook et nouvelle bibliothèque pour ASP.NET MVC].

Si vous développez en ASP.NET, il est important de réaliser que l'intégration aux médias sociaux n'est pas limitée par les modèles fournis par Visual Studio. Ces modèles ne visent qu'à simplifier le processus. Mais, comme nous l'avons vu, chaque site de média social fournit des informations sur les différents modes de connexion .NET, ainsi que pour un grand nombre d'autres langages et infrastructures.

## <a name="scale"></a>Évolution en fonction de la demande

Le cloud computing se révèle particulièrement utile en cas de charges de travail non prévisibles. Les campagnes marketing numériques sont un exemple parfait de ce cas de figure. Il est difficile de prévoir la popularité d'un site marketing à durée de vie relativement courte, car cela dépend en grande majorité de la capacité à capter l'intérêt des utilisateurs et des interactions avec les réseaux sociaux associés, qui redirigent le public vers le site. Azure inclut plusieurs options permettant de faire évoluer les sites web et les services cloud.

-   Mise à l'échelle manuelle via le [portail de gestion Azure][portail de gestion Azure]
-   Mise à l'échelle par programmation via l'[API de gestion des services][API de gestion des services] ou des [scripts PowerShell][scripts PowerShell]
-   Mise à l'échelle automatique via la version préliminaire de la fonctionnalité Mise à l'échelle automatique.

Dans le portail de gestion, accédez à l'onglet **Mettre à l'échelle** de votre site web. Plusieurs options de mise à l'échelle sont proposées. La première détermine le mode du site web : **Gratuit**, **Partagé** ou **Standard**.

![DigitalMarketingScale][DigitalMarketingScale]

Les fonctionnalités et options de mise à l'échelle augmentent avec les niveaux. Par exemple, en mode **Gratuit**, les sites ne peuvent pas être mis à l'échelle pour plusieurs instances, tandis que ceux en mode **Partagé** peuvent l'être pour 6 instances. Il est également possible d'opter pour la mise à l'échelle en mode **Standard**. Ce mode exécute le site sur des machines virtuelles dédiées de petite, moyenne ou grande taille. Outre le choix dans la taille des machines virtuelles, il est également possible d'effectuer une montée en charge vers plusieurs instances. Le mode **Standard** permet ainsi une montée en charge vers 10 instances. Vous trouverez la liste complète des caractéristiques spécifiques à chaque mode dans les [directives de tarification relatives aux sites Web][directives de tarification relatives aux sites Web].

En mode **Standard**, il est également possible d'activer la version préliminaire de la fonctionnalité Mise à l'échelle automatique, qui permet une mise à l'échelle en fonction du processeur. Le pourcentage **UC cible** correspond à la plage d'utilisation du processeur ciblée par Azure pour les instances du site web. Si l'utilisation UC moyenne est inférieure à ce pourcentage cible, Azure réduit le nombre d'instances, car une charge identique sur un nombre inférieur d'instances entraîne une augmentation de l'utilisation des instances restantes. Notez cependant que le **nombre d'instances** ne peut être réduit au-delà de la valeur minimale définie. De même, si l'utilisation UC moyenne est supérieure au pourcentage **UC cible**, Azure augmente le nombre d'instances. La même charge est alors répartie entre davantage de machines, ce qui a pour effet de réduire l'utilisation UC de chaque machine. Notez que le **nombre d'instances** pouvant être ajoutées est limité par la valeur maximale définie.

![DigitalMarketingAutoScale][DigitalMarketingAutoScale]

La mise à l'échelle automatique permet une exploitation bien plus efficace des ressources, par exemple, dans le cas d'une campagne marketing numérique davantage active pendant la nuit et les week-ends. Cela permet également de couvrir les scénarios dans lesquels la popularité de la campagne augmente de manière inattendue. La mise à l'échelle automatique permet alors de gérer efficacement l'augmentation de charge, puis de réduire le nombre d'instances (et donc les coûts) lorsque la charge diminue.

Pour plus d'informations sur la mise à l'échelle des sites web, consultez [Mise à l'échelle de sites web][Mise à l'échelle de sites web]. Ce sujet est également étroitement lié au concept de surveillance. Pour plus d'informations à ce sujet, consultez la section [Surveillance de la campagne][Surveillance de la campagne] ci-après.

<div class="dev-callout">
<strong>Remarque</strong>
<p>Pour les applications Web utilisant les services cloud et les r&ocirc;les Web, il est &eacute;galement possible de proc&eacute;der &agrave; une mise &agrave; l'&eacute;chelle en fonction de la longueur des &eacute;l&eacute;ments plac&eacute;s dans la file d'attente. Dans un service cloud, les r&ocirc;les traitant les files d'attente de serveur principal constituent un mod&egrave;le d'architecture commun. Pour plus d'informations sur la mise &agrave; l'&eacute;chelle des services cloud, consultez la rubrique <a href="http://www.windowsazure.com/fr-fr/manage/services/cloud-services/how-to-scale-a-cloud-service/">Extension d'un service cloud</a>.</p>
</div>

## <a name="integrate"></a>Intégration à d'autres services

Un site marketing numérique intègre bien souvent du contenu multimédia enrichi, tel que la diffusion en continu de vidéos. L'hébergement de ces sites dans Azure offre une intégration étroite aux services Azure connexes. Vous pouvez, par exemple, utiliser Azure Media Services pour encoder et diffuser en continu des vidéos pour votre site web. Pour plus d'informations sur Media Services, consultez la rubrique [Concepts Azure Media Services][Concepts Azure Media Services].

D'autres services Azure peuvent être utilisés pour créer des applications plus puissantes. Par exemple, Websites peut utiliser la mise en cache distribuée fournie par le nouveau service [Azure Cache Service (Preview)][Azure Cache Service (Preview)]. Vous pouvez également utiliser les services Azure Storage pour stocker vos données et ressources d'application. Par exemple, les images, les vidéos ainsi que d'autres fichiers volumineux peuvent être stockés durablement dans des objets blob. Des services de base de données, tels que la base de données SQL Azure et MySQL, sont également disponibles pour les données relationnelles.

## <a name="monitor"></a>Surveillance de la campagne

L'onglet **Surveillance** présente des mesures pouvant vous aider à évaluer l'efficacité et les performances de votre site marketing numérique.

![DigitalMarketingMonitor][DigitalMarketingMonitor]

Vous pouvez notamment contrôler les modes et niveaux d'utilisation grâce à des mesures, telles que le **temps processeur**, les **requêtes** et les **données envoyées**. Des valeurs élevées indiquent en effet une grande popularité de la campagne marketing. Vous pouvez ainsi utiliser ces informations sur l'utilisation pour décider quand étendre ou réduire les capacités de votre site. Pour plus d'informations, consultez la page [Surveillance des sites Web][Surveillance des sites Web].

Pour les modes **Gratuit** et **Partagé**, vous devez également tenir compte des quotas de ressource. L'onglet **Tableau de bord** du portail présente les statistiques d'utilisation de plusieurs quotas, ainsi que leur date de réinitialisation. Notez que les statistiques affichées varient en fonction du mode sélectionné. La capture d'écran ci-après présente les statistiques correspondant au mode **Gratuit**. En mode **Partagé**, par exemple, il n'y a pas de quota pour les **données envoyées**. Et en mode **Standard**, cet onglet indique uniquement le **stockage de système de fichiers** et la **taille**.

![DigitalMarketingUsageOverview][DigitalMarketingUsageOverview]

Si ces quotas arrivent à saturation, envisagez de passer du mode **Gratuit** au mode **Partagé**, ou du mode **Partagé** au mode **Standard**. En mode **Standard**, les ressources sont dédiées à une ou plusieurs machines virtuelles de petite, moyenne ou grande taille.

Outre le portail de gestion, il existe un certain nombre d'outils tiers fournissant des données de surveillance avancées pour vos sites web. Vous pouvez, par exemple, installer un module complémentaire New Relic à partir du Magasin Store. Pour plus d'informations sur l'utilisation de New Relic à des fins de surveillance, consultez la rubrique [Gestion des performances des applications New Relic sur Azure][Gestion des performances des applications New Relic sur Azure].

Enfin, en mode Standard, il est possible d'activer des fonctions de surveillance des points de terminaison et d'alerte sur les sites web. Un point de terminaison surveille régulièrement les tentatives d'accès à votre site web, et contrôle le temps de réponse. La fonction d'alerte envoie des notifications par courrier électronique lorsque le temps de réponse dépasse le seuil défini. Pour plus d'informations, consultez la section dédiée à la surveillance du scénario [Présence sur le web à l'international][consulter ces informations] et la rubrique, [Procédure : Réception de notifications d'alerte et gestion des règles d'alerte dans Azure][Procédure : Réception de notifications d'alerte et gestion des règles d'alerte dans Azure].

## <a name="summary"></a>Résumé

Sites Web Azure constitue un bon choix pour les contenus web réutilisables personnalisés pour chaque campagne marketing. Il prend en charge un grand nombre de langages, d'infrastructures et de systèmes de contrôle de code source communs, facilitant ainsi la migration de ces composants et flux de travail vers le cloud. Le modèle d'application Facebook ASP.NET permet de créer simplement des applications Facebook, mais vous pouvez utiliser pratiquement n'importe quelle intégration aux médias sociaux tierce prenant en charge les interfaces Web. Azure Media Services ainsi que d'autres services Azure connexes fournissent des outils supplémentaires pour vous aider à concevoir correctement votre site de campagne. Et les nombreuses options de mise à l'échelle manuelle et automatique se révèlent utiles pour gérer la demande, qui peut être difficile à anticiper. Pour plus d'informations, consultez les articles techniques répertoriés ci-après.

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="top">Domaine</th>
   <th align="left" valign="top">Ressources</th>
</tr>
<tr>
   <td valign="middle"><strong>Planification</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/fr-fr/manage/services/web-sites/choose-web-app-service">Comparaison entre Sites Web Azure, Azure Cloud Services et Azure Virtual Machines</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Création</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/fr-fr/manage/services/web-sites/how-to-create-websites/">Création et déploiement d'un site web</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Déploiement</strong></td>
   <td valign="top">- <a href="http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-deploy/">Déploiement d'un site web Azure</a><br/>- <a href="http://www.windowsazure.com/fr-fr/develop/net/common-tasks/publishing-with-git/">Publication à partir du contrôle de code source dans Sites Web Azure</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Médias sociaux</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/fr-fr/develop/net/tutorials/web-site-with-sql-database/">Déploiement d'une application ASP.NET MVC sécurisée avec une fonctionnalité d'appartenance, OAuth et une base de données SQL</a><br/>- <a href="http://blogs.msdn.com/b/africaapps/archive/2013/02/20/creating-a-facebook-app-using-asp-net-mvc-facebook-templates-and-hosting-them-for-free-on-windows-azure-websites.aspx">Création d'une application Facebook à l'aide d'un modèle Facebook ASP.NET MVC</a><br/>- <a href="http://blogs.msdn.com/b/webdev/archive/2012/12/13/the-new-facebook-application-template-and-library-for-asp.net-mvc.aspx">Modèle d'application Facebook et bibliothèque pour ASP.NET MVC</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Mise à l’échelle</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/fr-fr/manage/services/web-sites/how-to-scale-websites/">Mise à l'échelle de sites web</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Contenu multimédia enrichi</strong></td>
   <td valign="top">- <a href="http://msdn.microsoft.com/fr-fr/library/windowsazure/dn223282.aspx">Concepts Azure Media Services</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Monitor</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/fr-fr/manage/services/web-sites/how-to-monitor-websites/">Surveillance de sites web</a><br/>- <a href="http://msdn.microsoft.com/library/windowsazure/dn306638.aspx">Procédure : Réception de notifications d'alerte et gestion des règles d'alerte dans Azure</a></td>
</tr>
</table>

  [Sites Web Azure]: /fr-fr/documentation/services/web-sites/
  [Azure Cloud Services]: /fr-fr/documentation/services/cloud-services/
  [Azure Virtual Machines]: /fr-fr/documentation/services/virtual-machines/
  [Comparaison entre Sites Web Azure, Azure Cloud Services et Azure Virtual Machines]: /fr-fr/manage/services/web-sites/choose-web-app-service
  [Déploiement de sites Web existants]: #deployexisting
  [Intégration au sein de médias sociaux]: #socialmedia
  [Évolution en fonction de la demande]: #scale
  [Intégration à d'autres services]: #integrate
  [Surveillance de la campagne]: #monitor
  [consulter ces informations]: /fr-fr/manage/services/web-sites/global-web-presence-solution-overview/
  [DigitalMarketingDeploy1]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Deploy1.png
  [DigitalMarketingDeploy2]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Deploy2.png
  [Environnements multiples avec Sites Web Azure]: http://www.bradygaster.com/post/multiple-environments-with-windows-azure-web-sites
  [Publication à partir du contrôle de code source dans Sites Web Azure]: /fr-fr/develop/net/common-tasks/publishing-with-git/
  [DigitalMarketingFrameworkVersions]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_FrameworkVersions.png
  [Déploiement d'une application ASP.NET MVC sécurisée avec une fonctionnalité d'appartenance, OAuth et une base de données SQL vers un site Web Azure]: /fr-fr/develop/net/tutorials/web-site-with-sql-database/
  [la liste des bibliothèques disponibles prenant en charge l'API Twitter]: https://dev.twitter.com/docs/twitter-libraries#dotnet
  [DigitalMarketingFacebook]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Facebook.png
  [Création d'une application Facebook à l'aide d'un modèle Facebook ASP.NET MVC et hébergement gratuit de l'application sur Sites Web Azure]: http://blogs.msdn.com/b/africaapps/archive/2013/02/20/creating-a-facebook-app-using-asp-net-mvc-facebook-templates-and-hosting-them-for-free-on-windows-azure-websites.aspx
  [Application d'anniversaire Facebook ASP.NET MVC]: http://www.asp.net/mvc/tutorials/mvc-4/aspnet-mvc-facebook-birthday-app
  [Nouveau modèle d'application Facebook et nouvelle bibliothèque pour ASP.NET MVC]: http://blogs.msdn.com/b/webdev/archive/2012/12/13/the-new-facebook-application-template-and-library-for-asp.net-mvc.aspx
  [portail de gestion Azure]: http://manage.windowsazure.com/
  [API de gestion des services]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee460799.aspx
  [scripts PowerShell]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj152841.aspx
  [DigitalMarketingScale]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Scale.png
  [directives de tarification relatives aux sites Web]: https://www.windowsazure.com/fr-fr/pricing/details/web-sites/
  [DigitalMarketingAutoScale]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_AutoScale.png
  [Mise à l'échelle de sites web]: /fr-fr/manage/services/web-sites/how-to-scale-websites/
  [Concepts Azure Media Services]: http://msdn.microsoft.com/fr-fr/library/windowsazure/dn223282.aspx
  [Azure Cache Service (Preview)]: http://msdn.microsoft.com/fr-fr/library/windowsazure/dn386094.aspx
  [DigitalMarketingMonitor]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Monitor.png
  [Surveillance des sites Web]: /fr-fr/manage/services/web-sites/how-to-monitor-websites/
  [DigitalMarketingUsageOverview]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_UsageOverview.png
  [Gestion des performances des applications New Relic sur Azure]: /fr-fr/develop/net/how-to-guides/new-relic/
  [Procédure : Réception de notifications d'alerte et gestion des règles d'alerte dans Azure]: http://msdn.microsoft.com/library/windowsazure/dn306638.aspx
