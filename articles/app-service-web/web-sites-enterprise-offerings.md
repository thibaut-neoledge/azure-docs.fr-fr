<properties 
	pageTitle="Offres Azure App Service Web Apps pour les entreprises" 
	description="Montre comment utiliser Azure App Service Web Apps pour créer des solutions professionnelles de sites web pour votre entreprise" 
	services="app-service\web" 
	documentationCenter="" 
	authors="apwestgarth" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/30/2015" 
	ms.author="anwestg"/>

# Livre blanc sur les offres Azure App Service Web Apps pour les entreprises #

La nécessité de réduire les coûts et de fournir des solutions informatiques plus rapides dans un environnement en rapide évolution crée de nouveaux défis pour les développeurs, les professionnels de l'informatique et les gestionnaires. Les utilisateurs attendent de leurs applications web métier qu’elles soient rapides, réactives et disponibles sur n’importe quel appareil. De la même façon, les entreprises essaient de tirer profit de la productivité et de l'efficacité accrues provenant de l'intégration au cloud et des services mobiles. Cela peut aller de la simple authentification unique sur plusieurs appareils à l'aide d'Active Directory jusqu'à la collaboration dans Office 365 avec des données extraites d'une application métier interne, qui à son tour extrait les données émanant directement de la force de vente. [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) est un service cloud d’entreprise qui permet de développer, tester et exécuter des applications web et mobiles, des API web et des sites web génériques. Ce service peut être utilisé pour exécuter des sites web d'entreprise, des sites intranet, des applications professionnelles et des campagnes marketing numériques sur un réseau mondial de centres de données optimisé pour la mise à l'échelle et la disponibilité, ainsi que la prise en charge de l'intégration continue et de pratiques liées aux opérations de développement modernes.

Ce livre blanc met en évidence les fonctionnalités du service [Web Apps](/services/app-service/web/) dédié à l’exécution d’applications web métier. Il porte sur la migration d’applications web existantes et le déploiement de nouvelles applications web métier sur la plateforme.

## Public ciblé ##

Professionnels, architectes et responsables informatiques cherchant à migrer vers le cloud des charges de travail web actuellement traitées en local. Ces charges de travail web peuvent couvrir les activités entreprise-employé ou entreprise-applications web de partenaires.

## Introduction ##

App Service Web Apps est une plateforme idéale pour héberger des applications web et des services internes et externes. Cette solution gérée, économique et hautement évolutive vous permet de vous recentrer sur la valeur ajoutée que vous apportez à vos utilisateurs au lieu de consacrer beaucoup de temps et d’argent à la maintenance et la prise en charge d’environnements séparés. Le service Web Apps constitue une plateforme flexible pour déployer vos applications web d’entreprise et conserver l’authentification via Active Directory en local grâce à l’intégration de Microsoft Azure Active Directory. Il assure des déploiements faciles et rapides grâce à vos pratiques internes de déploiement et d’intégration continus, tout en adaptant automatiquement le service à vos besoins, le tout sur une plateforme qui vous permet de vous concentrer sur votre application et non sur votre infrastructure.

## Définition du problème ##

Le paysage informatique évolue rapidement, avec l’abandon de l’hébergement sur des serveurs traditionnels qui exigent des coûts d’immobilisation importants sur de longues périodes, au profit d’un hébergement fondé sur l’utilisation à la demande de services dimensionnés automatiquement en fonction de la charge. Les services informatiques sont tenus de réduire le coût et l’encombrement de l’infrastructure, ainsi que les dépenses de maintenance, en mettant l’accent sur la réduction de l’investissement et l’accroissement de l’agilité. La fin de vie de plateformes plus anciennes, comme Windows Server 2003, conduit les services informatiques à considérer la migration vers le cloud comme un moyen permettant d'éviter de nouveaux coûts en termes d'investissement à long terme. Auparavant, les directeurs informatiques décidaient des achats pour d’autres services, mais les directeurs marketing et autres responsables de départements suivent maintenant de plus près leur budget et le retour sur investissement. Le personnel des entreprises devient de plus en plus mobile, travaille à distance, passe davantage de temps en clientèle et nécessite un accès fluide aux systèmes.

Les besoins évoluent chaque mois, chaque semaine, chaque jour. Les entreprises veulent un déploiement mondial instantané, avec des services mis à jour régulièrement à l’aide de nouvelles fonctionnalités fournies par un tiers ou en interne. Dans certains cas, les entreprises recherchent également les capacités à isoler leurs applications et l'accès aux ressources tout en utilisant des installations de cloud public. Les utilisateurs ont davantage d’attentes et beaucoup utilisent des services tels que Office 365 dans leur vie privée. Ils souhaitent avoir accès à des services similaires, actualisés et riches en fonctionnalités, dans leur vie professionnelle. Pour répondre à cette demande, le service informatique doit faciliter l’implémentation via la sélection et l’intégration de services tiers, choisir avec soin les plateformes qui peuvent s’adapter aux besoins de l’entreprise, et concilier fiabilité et un coût total de possession réduit.

Les équipes de développement cherchent à apporter des avantages métier immédiats, en fournissant régulièrement de nouvelles fonctionnalités. Elles veulent une plateforme économique et fiable, qui intègre leurs outils et pratiques : développement, test, commercialisation. Elles collaborent avec les services informatiques pour automatiser le déploiement, la gestion et les alertes, avec comme principal objectif l’absence d’interruption.

<a href="highlevel" />
## Solution de haut niveau ##

Les infrastructures et les plateformes web sont de plus en plus utilisées pour développer, tester et héberger des applications métier. Prenons le cas d’une application métier classique, telle qu’un système interne de notes de frais des employés, souvent constitué d’une application web avec une base de données de sauvegarde pour stocker les données connectées à l’application.

App Service Web Apps est une solution de choix pour héberger ces applications, car son infrastructure évolutive et fiable peut être gérée et faire l’objet de correctifs sans quasiment aucune intervention manuelle ni interruption. La plateforme Microsoft Azure offre de nombreuses options de stockage de données qui prennent en charge des applications web hébergées sur Web Apps à partir de Microsoft Azure SQL Database, une base de données (en tant que service) relationnelle, évolutive et gérée, pour fournir des services de nos partenaires tels que ClearDB MySQL Database et MongoDB.

Une autre approche consiste à utiliser votre investissement existant en local. Dans cet exemple, mettant en scène un système de notes de frais des employés, vous voulez gérer votre banque de données dans votre propre infrastructure interne, que cela soit motivé par l'intégration avec les systèmes internes (rapports, salaires, facturation, etc.) ou pour respecter une exigence de gouvernance informatique. Web Apps fournit plusieurs méthodes pour vous connecter à votre infrastructure locale :

- [App Service Environments](../app-service-app-service-environment-intro.md) : App Service Environments (ASE) constituent une nouvelle fonctionnalité Premium qui a été récemment ajoutée à l'offre App Service de Microsoft Azure. ASEs fournissent un environnement totalement isolé et dédié pour l'exécution sécurisée des applications Azure App Service à grande échelle, tout en offrant un isolement et un accès réseau sécurisé.   
- [Connexions hybrides](../integration-hybrid-connection-overview.md) : ces fonctionnalités de Microsoft Azure BizTalk Services permettent à Web Apps de se connecter à des ressources locales en toute sécurité, par exemple SQL Server, MySQL, des API web et des services web personnalisés. 
- [Intégration au réseau virtuel](http://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/) : l’intégration de Web Apps à Azure Virtual Network vous permet de vous connecter votre application web à un réseau virtuel Azure, lequel peut être connecté à votre infrastructure locale via un réseau privé virtuel de site à site. 

Le diagramme suivant représente un exemple de solution puissante dotée d'options de connectivité pour des ressources locales. Le premier exemple montre comment procéder à l'aide des fonctionnalités standard d'Azure App Service et le second exemple montre comment procéder à l'aide de l'offre Premium, App Service Environments.

Utilisation des fonctionnalités App Service standard : ![](./media/web-sites-enterprise-offerings/on-premise-connectivity-solutions.png "Utilisation des fonctionnalités App Service standard")

Utilisation d'un App Service Environment : ![](./media/web-sites-enterprise-offerings/on-premise-connectivity-solutions-ASE.png "Utilisation d'un App Service Environment")

## Avantages pour l’entreprise ##

App Service Web Apps apporte une multitude d’avantages qui permettent de couvrir les besoins de l’entreprise de manière beaucoup plus économique et agile.

### Modèle PaaS ###

App Service Web Apps repose sur un modèle PaaS (plateforme en tant que service) qui génère des économies et des gains d’efficacité. Vous n'avez plus à consacrer de longues heures à la gestion de machines virtuelles, à l'application de correctifs des systèmes d'exploitation et des infrastructures. Faisant l’objet de correctifs automatiques, Web Apps vous permet de vous concentrer sur la gestion de vos applications web et pas sur les machines virtuelles, laissant ainsi aux équipes la possibilité d’apporter encore plus de valeur ajoutée.

Grâce au modèle PaaS qui sous-tend Web Apps, les praticiens de la méthodologie des opérations de développement remplissent leurs objectifs. Pour l'entreprise, cela se traduit par la gestion et l'intégration complètes pendant tout le cycle de vie de l'application, notamment le développement, le test, la publication, la surveillance, la gestion et le support.

Pour les équipes de développement, l’intégration et les flux de travail de déploiement en continu peuvent être configurés à partir de Visual Studio Team Services, GitHub, TeamCity, Hudson ou BitBucket. En fait, la génération, le test et le déploiement automatisés permettent des cycles de versions plus rapides tout en limitant les problèmes de version de l’infrastructure existante. Web Apps prend également en charge la création de plusieurs environnements de test et intermédiaires pour le flux de travail de vos versions. Plus besoin de réserver ou d’allouer du matériel à ces tâches. Il suffit de créer autant d’environnements que vous le souhaitez et de définir votre propre promotion pour le flux de travail des versions. En tant qu’entreprise, vous pouvez décider de tester une version à partir du contrôle de code source, d’effectuer une série de tests et, en cas de réussite, de promouvoir la version à un état supérieur, avant de l’évaluer en production sans interruption de service. L’avantage des applications web hébergées sur Web Apps, c’est qu’elles sont préchargées et offrent la meilleure expérience utilisateur qui soit. En outre, les entreprises peuvent tirer parti des fonctionnalités Test en production des applications web App Service pour diriger une section du trafic vers un autre emplacement, valider les modifications, avant de diriger tout le trafic vers le nouveau déploiement ou de rétablir tout le trafic vers le déploiement précédent.

Les équipes d’exploitation peuvent ainsi réagir aux problèmes concernant n’importe laquelle de leurs applications web hébergées sur Web Apps, grâce aux fonctionnalités intégrées de surveillance et d’alertes. Les équipes d'exploitation ont déjà investi dans les solutions d'analyse et de surveillance, telles que Microsoft Visual Studio Application Insights, New Relic et AppDynamics. De plus, celles-ci sont entièrement prises en charge sur Web Apps pour assurer la continuité du service et dotées d’environnements familiers pour surveiller vos applications web.

Enfin, Web Apps permet de sauvegarder automatiquement votre ou vos applications bases de données hébergées, directement dans un conteneur de stockage d’objets blob Azure. Vous bénéficiez d'une méthode simple et efficace avec laquelle effectuer la récupération d'urgence, réduisant la nécessité de posséder du matériel et des logiciels complexes en local.

### Facilité de migration ###

La rotation et la maintenance matérielles sont un élément essentiel pour les entreprises étant donné l'accélération des cycles de version du matériel et des systèmes d'exploitation. Vous disposez peut-être d'un nombre de serveurs Windows Server 2003 R2 qui ne bénéficieront plus du support technique en 2015, mais ils continuent d'héberger des applications web clés pour votre entreprise. App Service Web Apps est une solution de choix pour héberger ces applications web et rationaliser le parc matériel de l’entreprise. Il vous donne accès à différents matériels qui sont pris en charge et mis à jour dans le cadre du service. Plus besoin de prendre en compte des coûts de gestion et de remplacement dans votre budget d’infrastructure. La migration se réduit à un simple copier-coller de votre déploiement actuel dans Web Apps, ou à une migration plus complexe dans laquelle Web Apps Migration Assistant apporte la valeur ajoutée. Les applications web migrées bénéficient de la gamme complète des services Azure, en intégrant des services supplémentaires pour les applications web. Par exemple, vous pouvez envisager d'ajouter Azure Active Directory pour contrôler l'accès à votre application en fonction de l'association des utilisateurs aux groupes de sécurité. Un autre exemple peut être l'ajout des services de cache pour améliorer les performances et réduire la latence, qui permet d'offrir globalement une meilleure expérience utilisateur.

### Hébergement de qualité professionnelle ###

App Service Web Apps fournit une plateforme fiable et stable, capable de gérer des besoins d’entreprise très différents, qu’il s’agisse de petites charges de travail de développement et de test internes ou de sites web importants et générant un trafic volumineux. Avec Web Apps, vous utilisez la même plateforme d’hébergement professionnel que Microsoft pour ses charges de travail web stratégiques. Web Apps et tous les services disponibles sur la plateforme Azure sont sécurisés et conformes aux exigences de la norme ISO (ISO/IEC 27001:2005), des attestations SSAE 16/ISAE 3402 SOC1 et SOC2, de HIPAA BAA, de PCI et de Fedramp, pour chaque élément et fonctionnalité. Pour plus d’informations, consultez la page [http://aka.ms/azurecompliance](/support/trust-center/compliance/).

La plateforme Microsoft Azure autorise les contrôles d’autorisation basés sur des rôles, qui assurent un contrôle professionnel des ressources dans Web Apps. Avec RBAC, les entreprises peuvent implémenter leurs propres stratégies de gestion des accès pour l’ensemble de leurs actifs dans l’environnement Azure, en affectant des utilisateurs aux groupes et en attribuant les autorisations nécessaires à ces groupes en fonction des actifs, tels qu’une application web. Pour plus d’informations sur RBAC dans Azure, consultez la page [http://aka.ms/azurerbac](../role-based-access-control-configure/). Avec Web Apps, vous avez l’assurance que vos applications web sont déployées dans un environnement sécurisé et vous avez un contrôle total sur le territoire où vos actifs sont déployés.

Azure App Service Environments [http://aka.ms/aseintro] constituent une nouvelle option du plan de service Premium pour les entreprises souhaitant utiliser Azure App Service et cela fournit un environnement totalement isolé et dédié. Cela permet aux entreprises de déployer des applications pouvant tirer parti d'une très grande échelle tout en ayant également un contrôle total sur le trafic réseau entrant et sortant. De plus, les ASEs permettent aux applications d'avoir des connexions sécurisées haute vitesse sur des réseaux virtuels vers des ressources locales.

Les applications web App Service peuvent également tirer pleinement parti de vos investissements locaux en vous permettant de vous reconnecter à vos ressources internes, telles que votre entrepôt de données ou votre environnement SharePoint. Comme indiqué dans [Solution de niveau élevé], vous utilisez des connexions hybrides et la connectivité au réseau virtuel pour établir des connexions à l’infrastructure et aux services locaux.

### Portée mondiale ###

App Service Web Apps est une plateforme globale et évolutive, qui permet à vos applications web de grandir et de s’adapter rapidement aux besoins d’une entreprise en pleine croissance, et ce pour un coût et une planification à long terme minimum. Dans les scénarios classiques d'infrastructure locale, l'expansion et l'augmentation de la demande au niveau local et étendu nécessiteraient une grande quantité de gestion, de planification et de dépenses pour approvisionner et gérer une infrastructure supplémentaire. Web Apps vous permet de dimensionner vos applications web en fonction de vos besoins. Reprenons, par exemple, l’application de notes de frais : pendant la plus grande partie du mois, vos utilisateurs ne sont pas assidus, mais à mesure que l’échéance approche pour les notes de frais, l’application est davantage sollicitée. Web Apps peut allouer davantage de ressources à l’application puis, une fois le pic d’activité passé, revenir à l’infrastructure de base que vous définissez.

Web Apps est disponible dans le monde entier, à partir de 24 centres de données, un nombre appelé à augmenter. Pour obtenir la liste actualisée des régions et emplacements, consultez la page [http://aka.ms/azlocations](http://aka.ms/azlocations). Avec Web Apps, votre entreprise peut facilement prendre une dimension mondiale. À mesure que votre entreprise s’implante dans de nouvelles régions, les tableaux de bord de l’application de reporting que vous utilisez sont facilement déployés dans d’autres centres de données et sont mis à la disposition des utilisateurs locaux beaucoup plus rapidement grâce à la combinaison de Web Apps et d’Azure Traffic Manager. L’infrastructure évolutive présente l’avantage de pouvoir se contracter ou de s’étendre en fonction des besoins des agences régionales.
 
## Détails de la solution ##

Examinons un exemple de scénario de migration d'applications. Il détaille comment les fonctionnalités de Web Apps constituent une solution idéale et génèrent de la valeur pour l’entreprise.
 
Tout au long de cet exemple, l'application métier que nous allons aborder est une application de présentation de notes de frais qui permet aux employés de soumettre leurs notes de frais en vue de leur remboursement. L'application est hébergée sur un serveur Windows Server 2003 R2 exécutant IIS6 et la base de données est une base de données SQL Server 2005. Nous choisissons un ancien serveur, car Windows Server 2003 R2 et SQL Server 2005 arrivent bientôt en fin de service, et nous proposons des [outils](http://aka.ms/websitesmigration) et des [conseils](http://aka.ms/websitesmigrationresources) pour migrer automatiquement les charges de travail dans Azure. Dans cette optique, le modèle utilisé dans cet exemple s'applique à un large éventail de scénarios de migration.

### Migrer une application ###

La première étape pour migrer une application métier vers Web Apps consiste à identifier l’architecture et les ressources d’application existantes. L'exemple dans ce document est une application web ASP.NET hébergée sur un seul serveur IIS avec la base de données hébergée sur un serveur SQL Server distinct, comme illustré dans la figure ci-dessous. Les employés se connectent au système à l'aide d'un nom d'utilisateur et d'un mot de passe, ils entrent les détails des dépenses et téléchargent les copies numérisées des reçus dans la base de données, pour chaque élément de frais.
 
![](./media/web-sites-enterprise-offerings/on-premise-app-example.png)

#### Éléments à prendre en compte ####

Pendant la migration de l’application depuis un environnement local, vous devez garder à l’esprit quelques contraintes de Web Apps. Voici certains points clés à connaître pour migrer des applications web vers Web Apps ([http://aka.ms/websitesmigrationresources](http://aka.ms/websitesmigrationresources)) :

-	Liaisons de port : Web Apps ne prend en charge que le port 80 pour le trafic HTTP et le port 443 pour le trafic HTTPS. Si votre application utilise un autre port, après la migration, elle utilisera le port 80 pour le trafic HTTP et le port 443 pour le trafic HTTPS. C'est souvent un problème sans conséquence, car il est courant d'utiliser différents ports dans les déploiements pour éviter l'utilisation de noms de domaine, en particulier dans les environnements de développement et de test
-	Authentification : Web Apps prend en charge l’authentification anonyme par défaut et l’authentification par formulaire lorsqu’une application le spécifie. Il ne propose l’authentification Windows que lorsque l’application est intégrée à Azure Active Directory et AD FS. Cette fonctionnalité est abordée plus en détail [ici](http://aka.ms/azurebizapp). 
-	Assemblys basés sur GAC : Web Apps n’autorise pas le déploiement d’assemblys dans le GAC (Global Assembly Cache). Par conséquent, si l’application migrée utilise cette fonctionnalité localement, placez les assemblys dans le dossier bin de l’application.
-	Mode de compatibilité IIS5 : Web Apps ne prend pas en charge le mode de compatibilité IIS5. Par conséquent, chaque instance Web Apps et toutes les applications web sous l’instance Web Apps mère s’exécutent dans le même processus de travail d’un pool d’applications unique
-	Utilisation des bibliothèques COM : Web Apps n’autorise pas l’enregistrement de composants COM sur la plateforme. Par conséquent, si l’application utilise des composants COM, ceux-ci doivent être réécrits dans du code pris en charge et déployés avec l’application.
-	Filtres ISAPI : Web Apps prend en charge les filtres ISAPI. Ils doivent être déployés dans le cadre de l’application et enregistrés dans le fichier web.config de l’application web. Pour plus d’informations, consultez la page [http://aka.ms/azurewebsitesxdt](../web-sites-transform-extend/). 

Une fois ces éléments pris en compte, votre application web est prête pour le cloud. Et ne vous inquiétez pas si certains critères ne sont pas entièrement remplis, l'outil de migration s'attachera à procéder à la meilleure migration possible.

Les prochaines étapes du processus de migration visent à créer une application web App Service et une base de données SQL Azure. Il existe plusieurs tailles d’instance Web Apps avec un nombre variable de cœurs de processeur et de quantités de mémoire vive disponibles. Faites vos choix en fonction des besoins de votre application web. Pour obtenir plus d’informations et des prix, consultez la page [http://aka.ms/azurewebsitesskus](/pricing/details/websites/). De même, la base de données SQL Microsoft Azure répond à tous les besoins d'une entreprise avec différents niveaux de service et de performance pour satisfaire les exigences. Pour plus d’informations, consultez la page [http://aka.ms/azuresqldbskus](/pricing/details/sql-database/). Une fois créée, l’application est téléchargée dans App Service Web Apps par FTP ou par WebDeploy, puis déplacez la base de données.

Dans cette migration, la solution utilise la base de données SQL Azure, mais ce n'est pas la seule base de données prise en charge sur Azure. Les entreprises peuvent également utiliser MySQL, MongoDB, Azure DocumentDB et beaucoup d’autres solutions, grâce aux modules complémentaires disponibles à l’achat dans le [Magasin Azure](/marketplace/partner-program/).

Lors de la création d’une base de données SQL Azure, plusieurs options permettent d’importer une base de données à partir d’un serveur local, que soit par la génération d’un script d’une base de données ou l’utilisation des fonctions d’[importation et d’exportation de l’application de couche de données](http://aka.ms/dacpac).

La base de données de l'application de notes de frais a été générée en créant une base de données SQL Azure, en se connectant à la base de données à l'aide de SQL Server Management Studio, puis en exécutant un script pour générer le schéma de base de données et le remplir avec les données de la base de données locale.

La dernière étape de cette première phase de la migration nécessite la mise à jour des chaînes de connexion à la base de données pour l'application. Pour ce faire, nous allons utiliser le portail Azure. Vous pouvez modifier les paramètres de chaque application web, y compris les chaînes de connexion utilisées pour se connecter à une base de données.

### Alternatives à l’utilisation de la base de données SQL Azure ###

La plateforme Azure offre plusieurs alternatives à l’utilisation de la base de données SQL Azure comme une base de données principale d’applications web. Elles consistent à activer différentes charges de travail, c’est-à-dire à utiliser une solution NoSQL ou à adapter la plateforme aux données requises par l’entreprise. Par exemple, une entreprise peut posséder des données qui ne doivent pas être stockées à l’extérieur ou dans un environnement cloud public. Dans ce cas, elle privilégie l’utilisation de sa base de données locale.

#### Connectivité aux ressources locales ####
Les applications web App Service proposent plusieurs méthodes de connexion aux ressources locales, comme les bases de données, permettant de réutiliser l'infrastructure existante. Les options sont répertoriées ci-dessous :

- Les environnements App Service Environments sont isolés et créés dans un sous-réseau d'un réseau virtuel, ce qui permet à l'environnement de communiquer avec des points de terminaison privés au sein du même réseau virtuel - [http://aka.ms/appserviceasenetworking]
- L’intégration au réseau virtuel prend en charge l’intégration entre Web Apps et un réseau virtuel Azure, ce qui vous permet d’accéder aux ressources de votre réseau virtuel. Si ce dernier est connecté à votre réseau local avec un réseau privé virtuel site à site, vous accédez directement à vos systèmes locaux.
- Les connexions hybrides sont une fonctionnalité Azure BizTalk Services et fournissent un moyen simple de se connecter à des ressources locales telles que SQL Server, MySQL, API web HTTP et la plupart des services web personnalisés.

#### Évolutivité et résilience ####

Quand le personnel d'une entreprise augmente, via des acquisitions ou une croissance organique naturelle, les applications web doivent être mises à l'échelle pour répondre à ces nouvelles demandes. Aujourd'hui en effet, il est courant de voir une augmentation d'équipes colocalisées et d'employés distants, notamment dans les entreprises possédant des bureaux aux États-Unis, en Europe et en Asie, avec une force de vente mobile présente dans de nombreuses régions. Web Apps gère les modifications d’échelle confortablement et automatiquement.

Il permet d’automatiser les modifications d’échelle des applications web via le portail Azure, en fonction de deux critères : les heures planifiées ou l’utilisation du processeur. La fonction de dimensionnement automatique de Web Apps est un outil rentable et extrêmement souple pour gérer les pics d’utilisation de toutes les applications professionnelle, qu’il s’agisse d’applications web comme notre système de notes de frais ou de sites web marketing qui subissent des poussées de trafic de durée limitée pendant les promotions. Pour plus d’informations et pour obtenir des conseils sur le dimensionnement de vos applications web à l’aide de Web Apps, consultez la page [Faire évoluer une application web dans Azure App Service](web-sites-scale.md).

Outre la souplesse d’évolution de Web Apps, la plateforme assure continuité d’activité et résilience grâce à la répartition possible des applications web et de leurs ressources sur plusieurs centres de données et régions géographiques.

## Résumé ##
App Service Web Apps est une solution souple et rentable, qui s’adapte rapidement et dynamiquement aux besoins fluctuants d’une entreprise. Il permet aux entreprises d’augmenter productivité et efficacité en utilisant une plateforme gérée, dotée de fonctions de développement modernes, de gestion automatisée et de fonctionnalités professionnelles d’évolutivité, de résilience, de sécurité et d’intégration pour les actifs locaux.

## À vous d’agir ! ##
Pour plus d’informations sur le service Azure App Service Web Apps, visitez le site [http://aka.ms/enterprisewebsites](/services/websites/enterprise/). Vous pouvez également pour vous inscrire dès aujourd’hui sur [http://aka.ms/azuretrial](/pricing/free-trial/) pour évaluer le service et découvrir les avantages qui en découlent pour votre entreprise.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 
 

<!---HONumber=AcomDC_1217_2015-->