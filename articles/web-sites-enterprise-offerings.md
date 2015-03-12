<properties 
	pageTitle="Migration de vos sites Web IIS sur Sites Web Azure avec l'Assistant Migration" 
	description="Montre comment utiliser Sites Web Azure pour créer des solutions de sites Web professionnelles pour votre entreprise" 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	writer="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/20/2014" 
	ms.author="cephalin"/>

# Livre blanc sur les offres Sites Web Azure pour les entreprises #

La nécessité de réduire les coûts et de fournir des solutions informatiques plus rapides dans un environnement en rapide évolution crée de nouveaux défis pour les développeurs, les professionnels de l'informatique et les gestionnaires. Les utilisateurs attendent de leurs applications Web métier qu'elles soient rapides, réactives et disponibles à partir de n'importe quel appareil. De la même façon, les entreprises essaient de tirer profit de la productivité et de l'efficacité accrues provenant de l'intégration au cloud et des services mobiles. Cela peut aller de la simple authentification unique sur plusieurs appareils à l'aide d'Active Directory jusqu'à la collaboration dans Office 365 avec des données extraites d'une application métier interne, qui à son tour extrait les données émanant directement de la force de vente. [Sites Web Microsoft Azure](http://azure.microsoft.com/services/websites/) est un service cloud d'entreprise pour le développement, le test et l'exécution des applications Web et mobiles, des API Web et des sites Web génériques. Ce service peut être utilisé pour exécuter des sites Web d'entreprise, des applications professionnelles et des campagnes marketing numériques sur un réseau mondial de centres de données optimisé pour la mise à l'échelle et la disponibilité, ainsi que la prise en charge de l'intégration continue et de pratiques liées aux opérations de développement modernes. 

Ce livre blanc met en évidence les fonctionnalités du service Sites Web Microsoft Azure spécialement destiné à l'exécution des applications Web métier. Il porte sur la migration d'applications Web existantes et le déploiement de nouvelles applications Web métier sur la plateforme. 

## Public ciblé ##

Professionnels de l'informatique, architectes et gestionnaires cherchant à migrer vers le cloud les charges de travail Web en cours d'exécution localement. Ces charges de travail Web peuvent couvrir les activités entreprise-employé ou entreprise-applications Web de partenaires.

## Introduction ##

Sites Web Microsoft Azure est une plateforme idéale sur laquelle héberger les applications Web et des services internes et externes. Elle offre une solution gérée, économique et hautement évolutive qui vous permet de vous concentrer sur la fourniture de valeur ajoutée pour vos utilisateurs au lieu de consacrer beaucoup de temps et d'argent à la maintenance et la prise en charge d'environnements séparés. Le service Sites Web Microsoft Azure offre une plateforme flexible sur laquelle déployer vos applications Web d'entreprise et permet de conserver l'authentification via Active Directory en local grâce à l'intégration à Microsoft Azure Active Directory. Il prend en charge des déploiements faciles et rapides en implémentant vos pratiques de déploiement et d'intégration continues internes, tout en procédant à la mise à l'échelle automatique en fonction de la croissance des besoins de l'entreprise. Le tout sur une plateforme gérée qui vous permet de vous concentrer sur votre application et non sur votre infrastructure. 

## Définition du problème ##

Le paysage informatique évolue rapidement en s'écartant du mode d'hébergement traditionnel sur des serveurs qui implique des coûts élevés d'immobilisation sur de longues périodes et tend vers une utilisation des services à la demande qui s'adaptent automatiquement à la charge. Les services informatiques sont tenus de réduire le coût et l'encombrement de l'infrastructure ainsi que les dépenses de maintenance en mettant l'accent sur la réduction de l'investissement tout en augmentant l'agilité. La fin de vie de plateformes plus anciennes, comme Windows Server 2003, conduit les services informatiques à considérer la migration vers le cloud comme un moyen permettant d'éviter de nouveaux coûts en termes d'investissement à long terme. Auparavant, les directeurs informatiques décidaient des achats pour d'autres services, mais les directeurs marketing et autres responsables de départements suivent maintenant de plus près leur budget et le retour sur investissement. Le personnel des entreprises est de plus en plus mobile, travaille à distance, passe davantage de temps en clientèle et nécessite un accès fluide aux systèmes.

L'évolution des besoins est mensuelle, hebdomadaire, quotidienne ; les entreprises recherchent une mise à l'échelle globale instantanée avec des services mis à jour régulièrement à l'aide de nouvelles fonctionnalités, fournies par un tiers ou en interne. Les utilisateurs ont des attentes plus élevées. Bon nombre d'entre eux utilisent déjà des services tels qu'Office 365 dans leur vie personnelle et s'attendent donc à bénéficier des mêmes services riches en fonctionnalités, à jour, dans leur vie professionnelle. Pour répondre à cette demande, le service informatique doit faciliter l'implémentation via la sélection et l'intégration de services tiers, un choix rigoureux de plateformes qui peuvent s'adapter aux besoins de l'entreprise, tout en étant aussi fiables avec un coût de possession total réduit.

Les équipes de développement s'emploient à procurer des bénéfices immédiats, en offrant fréquemment de nouvelles fonctionnalités. Elles recherchent une plateforme fiable et économique compatible avec leurs pratiques et leurs outils existants (développement, test et version). Elles collaborent avec les services informatiques pour automatiser le déploiement, la gestion et les alertes, leur principal objectif étant l'absence d'interruption.

<a href="highlevel" />
## Solution de niveau élevé ##

Les infrastructures et les plateformes Web sont de plus en plus utilisées pour développer, tester et héberger des applications métier.  Prenons le cas d'une application métier classique, telle qu'un système interne de notes de frais des employés, souvent uniquement constitué d'un site Web avec une base de données de sauvegarde pour stocker les données connectées à l'application.

Le service Sites Web Microsoft Azure est une bonne option pour héberger ces applications, car il offre une infrastructure évolutive et fiable qui est gérée et corrigée sans quasiment aucune intervention manuelle ni interruption. La plateforme Microsoft Azure offre de nombreuses options de stockage de données pour prendre en charge des applications Web hébergées sur Sites Web Microsoft Azure, de la base de données SQL Microsoft Azure, qui est une base de données (en tant que service) relationnelle, évolutive et gérée, aux services populaires de nos partenaires comme les bases de données MySQL, ClearDB et MongoDB.

Une autre approche consiste à utiliser votre investissement existant en local. Dans cet exemple, mettant en scène un système de notes de frais des employés, vous voulez gérer votre banque de données dans votre propre infrastructure interne, que cela soit motivé par l'intégration avec les systèmes internes (rapports, salaires, facturation, etc.) ou pour respecter une exigence de gouvernance informatique.  Sites Web Microsoft Azure fournit deux méthodes de connexion à votre infrastructure locale :

- [Connexions hybrides](http://aka.ms/hybridconnections) : fonctionnalité de Microsoft Azure BizTalk Services qui permet à Sites Web Azure de se connecter localement à des ressources en toute sécurité, par exemple SQL Server, MySQL, des API Web et des services Web personnalisés.
- [Intégration au réseau virtuel](http://aka.ms/websitesvnet) : grâce à cette fonctionnalité, Sites Web Azure permet de connecter votre site Web à un réseau virtuel Azure qui à son tour peut être connecté à votre infrastructure locale via un réseau privé virtuel de site à site.

Le diagramme suivant est un exemple de solution puissante dotée d'options de connectivité pour des ressources locales.

![](./media/web-sites-enterprise-offerings/on-premise-connectivity-solutions.png)

## Avantages pour l'entreprise ##

Sites Web Microsoft Azure fournit un ensemble d'avantages pour l'entreprise qui vous permettent de couvrir les besoins de l'entreprise de manière beaucoup plus économique et agile. 

### Modèle PaaS ###

Sites Web Azure repose sur un modèle PaaS (plateforme en tant que service) permettant un certain nombre d'économies en termes de coût et d'efficacité.  Vous n'avez plus à consacrer de longues heures à la gestion de machines virtuelles, à l'application de correctifs des systèmes d'exploitation et des infrastructures. Sites Web Azure est un environnement corrigé automatiquement qui permet de vous concentrer sur la gestion de vos applications Web et pas sur les machines virtuelles, en laissant les équipes libres de fournir des touches supplémentaires de valeur ajoutée.

Le modèle PaaS à la base de Sites Web Microsoft Azure permet aux praticiens de la méthodologie relative aux opérations de développement de répondre à leurs objectifs. Pour l'entreprise, cela se traduit par la gestion et l'intégration complètes pendant tout le cycle de vie de l'application, notamment le développement, le test, la publication, la surveillance, la gestion et le support. 

Pour les équipes de développement, l'intégration et les flux de travail de déploiement en continu peuvent être configurés à partir de Visual Studio Online, GitHub, TeamCity, Hudson ou BitBucket. En fait, la génération, le test et le déploiement automatisés permettent des cycles de versions plus rapides tout en limitant les problèmes de version de l'infrastructure existante. Sites Web Azure prend également en charge la création de plusieurs environnements de test et intermédiaires pour votre flux de travail de versions. Vous ne devez plus réserver ou allouer du matériel à cette fin, vous pouvez créer autant d'environnements que nécessaire et définir votre propre promotion pour le flux de travail de versions. En tant qu'entreprise, vous pouvez décider d'engager une version dans une fenêtre de test à partir du contrôle de code source, d'effectuer une série de tests et en cas de réussite, de promouvoir la version dans un emplacement intermédiaire, avant son lancement en production, sans interruption de service. L'avantage des applications Web hébergées sur Sites Web Azure, c'est qu'elles sont préchargées et prêtes à offrir la meilleure expérience utilisateur qui soit.

Les équipes opérationnelles peuvent ainsi réagir aux problèmes concernant n'importe laquelle de leurs applications Web hébergées sur Sites Web Azure avec les fonctionnalités de surveillance et d'alertes intégrées. Les équipes d'exploitation ont déjà investi dans les solutions d'analyse et de surveillance, telles que Microsoft Visual Studio Application Insights, New Relic et AppDynamics. Elles sont également entièrement prises en charge sur Sites Web Azure en permettant la continuité et représentent des environnements familiers à partir desquels surveiller vos applications Web.

Enfin, Sites Web Azure offre une fonctionnalité pour sauvegarder automatiquement vos sites et bases de données hébergés directement dans un conteneur de stockage d'objets blob Azure. Vous bénéficiez d'une méthode simple et efficace avec laquelle effectuer la récupération d'urgence, réduisant la nécessité de posséder du matériel et des logiciels complexes en local.

### Facilité de migration ###

La rotation et la maintenance matérielles sont un élément essentiel pour les entreprises étant donné l'accélération des cycles de version du matériel et des systèmes d'exploitation. Vous disposez peut-être d'un nombre de serveurs Windows Server 2003 R2 qui ne bénéficieront plus du support technique en 2015, mais ils continuent d'héberger des applications Web clés pour votre entreprise. Sites Web Azure est une excellente façon d'héberger ces applications Web et vous permet de rationaliser votre capital matériel professionnel. Sites Web Azure vous donne accès à une plage de spécifications matérielles qui sont gérées et mises à jour dans le cadre du service. Vous n'avez plus besoin de prendre en compte des coûts de gestion et de remplacement dans votre budget d'infrastructure.  La migration peut être aussi simple qu'un copier-coller de votre déploiement existant vers Sites Web Azure ou plus complexe à l'aide de l'Assistant Migration Sites Web Azure mais avec une réelle valeur ajoutée. Les applications Web migrées bénéficient de la gamme complète des services Azure, en intégrant des services supplémentaires pour les applications Web. Par exemple, vous pouvez envisager d'ajouter Azure Active Directory pour contrôler l'accès à votre application en fonction de l'association des utilisateurs aux groupes de sécurité. Un autre exemple peut être l'ajout des services de cache pour améliorer les performances et réduire la latence, qui permet d'offrir globalement une meilleure expérience utilisateur. 

### Hébergement de classe entreprise ###

Sites Web Azure fournit une plateforme fiable et stable qui a fait ses preuves pour gérer un large éventail de besoins d'entreprise, des petites charges de travail de développement et de test internes jusqu'au trafic élevé de sites Web hautement évolutifs. Avec Sites Web Azure, vous utilisez la même plateforme d'hébergement d'entreprise que Microsoft utilise (pour sa société) pour les charges de travail Web de valeur élevée. Sites Web Azure, ainsi que tous les services sur la plateforme Azure, sont conçus en conformité avec les exigences de la réglementation, en termes de sécurité et de normes, telles qu'ISO (ISO/IEC 27001:2005), les attestations SSAE 16/ISAE 3402 SOC1 et SOC2, HIPAA BAA, PCI et Fedramp, pour chaque élément et fonctionnalité. Pour plus d'informations, consultez la page [http://aka.ms/azurecompliance](http://aka.ms/azurecompliance). 

La plateforme Microsoft Azure permet le contrôle d'accès en fonction du rôle (RBAC, Role Based Authorization Controls), en activant des niveaux de contrôle des ressources spécifiques à l'entreprise dans Sites Web Azure. Avec RBAC, les entreprises peuvent implémenter leurs propres stratégies de gestion des accès pour tous leurs actifs dans l'environnement Azure, en affectant des utilisateurs aux groupes et des autorisations nécessaires à ces groupes en fonction des actifs, comme un site Web Azure. Pour plus d'informations sur RBAC dans Azure, consultez la page [http://aka.ms/azurerbac](http://aka.ms/azurerbac). En utilisant Sites Web Azure, vous avez l'assurance que vos applications Web sont déployées dans un environnement sûr et sécurisé et vous avez un contrôle total sur le territoire où vos actifs sont déployés. 

De plus, Sites Web Azure peut également tirer pleinement parti de vos investissements locaux en vous permettant de vous reconnecter à vos ressources internes, telles que votre entrepôt de données ou votre environnement SharePoint. Comme indiqué dans [Solution de niveau élevé](#highlevel) , vous utilisez des connexions hybrides et la connectivité au réseau virtuel pour établir des connexions à l'infrastructure et aux services locaux.

### Mise à l'échelle globale ###

Sites Web Azure est une plateforme globale et évolutive, qui permet à vos applications Web de croître et de s'adapter rapidement aux besoins d'une entreprise en pleine croissance et avec une planification à long terme minimale et économique. Dans les scénarios classiques d'infrastructure locale, l'expansion et l'augmentation de la demande au niveau local et étendu nécessiteraient une grande quantité de gestion, de planification et de dépenses pour approvisionner et gérer une infrastructure supplémentaire. Sites Web Azure offre la possibilité de mettre à l'échelle vos applications Web en fonction de vos besoins. Prenons par exemple l'application de notes de frais : pendant la majeure partie du mois, vos utilisateurs ne sont pas assidus, mais chaque mois, à mesure que la date d'échéance approche et qu'ils doivent entrer leurs notes de frais, l'application est davantage sollicitée. Sites Web Azure peut approvisionner automatiquement plus d'infrastructure pour votre application puis, une fois que l'activité a cessé à nouveau, il peut rétablir l'infrastructure de base que vous définissez.

Sites Web Azure est disponible globalement dans 17 centres de données à travers le monde et est en pleine croissance. Pour obtenir la liste la plus à jour des régions et des emplacements, consultez la page [http://aka.ms/azlocations](http://aka.ms/azlocations). Avec Sites Web Azure, votre entreprise peut facilement atteindre une échelle et une portée mondiales. À mesure que votre entreprise s'implante dans de nouvelles régions, les tableaux de bord de l'application de création de rapports que vous utilisez peuvent facilement être déployés dans des centres de données supplémentaires et servir aux utilisateurs locaux beaucoup plus rapidement grâce à la combinaison de Sites Web Azure et d'Azure Traffic Manager. L'infrastructure évolutive présente l'avantage de pouvoir se contracter ou s'étendre pour s'adapter aux modifications des bureaux régionaux.
 
## Détails de la solution ##

Examinons un exemple de scénario de migration d'applications. Ce document présente de manière détaillée l'association des fonctionnalités de Sites Web Azure permettant de fournir la solution idéale et de générer de la valeur pour l'entreprise.
 
Tout au long de cet exemple, l'application métier que nous allons aborder est une application de présentation de notes de frais qui permet aux employés de soumettre leurs notes de frais en vue de leur remboursement. L'application est hébergée sur un serveur Windows Server 2003 R2 exécutant IIS6 et la base de données est une base de données SQL Server 2005. Nous choisissons un ancien serveur, car Windows Server 2003 R2 et SQL Server 2005 arrivent bientôt en fin de service et nous proposons des [outils](http://aka.ms/websitesmigration) et des [conseils](http://aka.ms/websitesmigrationresources) pour migrer automatiquement les charges de travail dans Azure. Dans cette optique, le modèle utilisé dans cet exemple s'applique à un large éventail de scénarios de migration. 

### Migrer une application existante ###

La première étape de la solution globale pour migrer une application métier vers Sites Web Azure consiste à identifier l'architecture et les ressources d'application existantes. L'exemple dans ce document est une application Web ASP.NET hébergée sur un seul serveur IIS avec la base de données hébergée sur un serveur SQL Server distinct, comme illustré dans la figure ci-dessous. Les employés se connectent au système à l'aide d'un nom d'utilisateur et d'un mot de passe, ils entrent les détails des dépenses et téléchargent les copies numérisées des reçus dans la base de données, pour chaque élément de frais. 
 
![](./media/web-sites-enterprise-offerings/on-premise-app-example.png)

#### Éléments à prendre en considération ###

Pendant la migration d'une application depuis un environnement local, vous devez garder à l'esprit quelques contraintes de Sites Web Azure. Voici certains points clés à connaître pendant la migration des applications Web vers Sites Web Azure ([http://aka.ms/websitesmigrationresources](http://aka.ms/websitesmigrationresources)) :

-	Liaisons de port : Sites Web Azure prend uniquement en charge le port 80 pour HTTP et le port 443 pour le trafic HTTPS. Si votre application ou votre site utilise un autre port, une fois migré, l'application ou le site utilisera le port 80 pour HTTP et le port 443 pour le trafic HTTPS. C'est souvent un problème sans conséquence, car il est courant d'utiliser différents ports dans les déploiements pour éviter l'utilisation de noms de domaine, en particulier dans les environnements de développement et de test
-	Authentification : Sites Web Azure prend en charge l'authentification anonyme par défaut et l'authentification par formulaire selon l'identification d'une application. Sites Web Azure peut offrir l'authentification Windows quand l'application ou le site est intégré à Azure Active Directory et ADFS uniquement. Cette fonctionnalité est abordée plus en détail [ici](http://aka.ms/azurebizapp) 
-	Assemblys basés sur GAC : Sites Web Azure n'autorise pas le déploiement des assemblys vers le Global Assembly Cache (GAC). Par conséquent, si l'application ou le site en cours de migration utilise cette fonction locale, pensez à déplacer les assemblys dans le dossier bin de l'application ou du site
-	Mode de compatibilité IIS5 : Sites Web Azure ne prend pas en charge le mode de compatibilité IIS5 et par conséquent, chaque site et toutes les applications Web sous le site parent s'exécutent dans le même processus de travail dans un pool d'applications unique
-	Utilisation des bibliothèques COM : Sites Web Azure n'autorise pas l'inscription des composants COM sur la plateforme. Ainsi, si le site ou l'application utilise des composants COM, ils doivent être réécrits dans du code géré et déployés avec le site ou l'application
-	Filtres ISAPI : les filtres ISAPI peuvent être pris en charge sur Sites Web Azure. Ils doivent être déployés dans le cadre du site et inscrits sur le site ou dans le fichier web.config des applications Web. Pour plus d'informations, consultez la page [http://aka.ms/azurewebsitesxdt](http://aka.ms/azurewebsitesxdt). 

Une fois que ces éléments ont été pris en compte, votre application Web doit être prête pour le cloud. Et ne vous inquiétez pas si certains critères ne sont pas entièrement remplis, l'outil de migration s'attachera à procéder à la meilleure migration possible. 

Les prochaines étapes du processus de migration visent à créer un site Web Azure et une base de données SQL Azure. Il existe plusieurs tailles d'instances de site Web Microsoft Azure avec un nombre variable de cœurs d'UC et des quantités de mémoire vive disponibles que vous pouvez sélectionner en fonction des besoins de vos applications Web. Pour plus d'informations, consultez la page [http://aka.ms/azurewebsitesskus](http://aka.ms/azurewebsitesskus). De même, la base de données SQL Microsoft Azure répond à tous les besoins d'une entreprise avec différents niveaux de service et de performance pour satisfaire les exigences. Pour plus d'informations, consultez la page [http://aka.ms/azuresqldbskus](http://aka.ms/azuresqldbskus). Une fois créé, le site ou l'application est téléchargé vers le site Web Azure, soit via FTP soit via WebDeploy, puis déplacé dans la base de données.

Dans cette migration, la solution utilise la base de données SQL Azure, mais ce n'est pas la seule base de données prise en charge sur Azure. Les entreprises peuvent également tirer parti de MySQL, MongoDB, Azure DocumentDB et beaucoup d'autres via des composants additionnels qui peuvent être achetés dans le [Magasin Azure](http://aka.ms/azurestore). 

Lors de la création d'une base de données SQL Azure, un certain nombre d'options est disponible pour importer une base de données existante à partir d'un serveur local, depuis la génération d'un script d'une base de données existante jusqu'à l'utilisation de l'[importation et de l'exportation d'application de couche données](http://aka.ms/dacpac). 

La base de données de l'application de notes de frais a été générée en créant une base de données SQL Azure, en se connectant à la base de données à l'aide de SQL Server Management Studio, puis en exécutant un script pour générer le schéma de base de données et le remplir avec les données de la base de données locale.

La dernière étape de cette première phase de la migration nécessite la mise à jour des chaînes de connexion à la base de données pour l'application. Cette opération peut être effectuée via le portail de gestion Azure. Chaque application Sites Web Azure a son propre tableau de bord avec un écran de configuration qui permet la modification des paramètres spécifiques des applications, notamment des chaînes de connexion servant à se connecter à une base de données utilisée par l'application.

### Alternative à l'utilisation de la base de données SQL Azure ###

La plateforme Azure offre un nombre d'alternatives à l'utilisation de la base de données SQL Azure en tant que base de données principale des applications Web. Elle permet d'activer les différentes charges de travail par exemple, en utilisant une solution NoSQL ou en activant la plateforme pour répondre aux besoins en termes de données d'une entreprise. Par exemple, une entreprise peut détenir des données qui ne doivent pas être stockées hors site ou dans un environnement cloud public et chercher à maintenir l'utilisation d'une base de données locale.

#### Connectivité aux ressources locales ###
Sites Web Azure offre deux méthodes de connexion aux ressources locales, telles que des bases de données, permettant de réutiliser l'infrastructure existante à forte valeur ajoutée. Les deux méthodes sont l'intégration au réseau virtuel de Sites Web Azure et les connexions hybrides :

- L'intégration au réseau virtuel de Sites Web Azure prend en charge l'intégration entre Sites Web Azure et un réseau virtuel Azure, qui vous permet d'accéder aux ressources de votre réseau virtuel. En fait, si ce dernier est connecté à votre réseau local avec un réseau privé virtuel site à site, vous êtes directement connecté à vos systèmes locaux.
- Les connexions hybrides sont une fonctionnalité Azure BizTalk Services et fournissent un moyen simple de se connecter à des ressources locales telles que SQL Server, MySQL, API Web HTTP et la plupart des services Web personnalisés.

#### Mise à l'échelle et résilience ###

Quand le personnel d'une entreprise augmente, via des acquisitions ou une croissance organique naturelle, les applications Web doivent être mises à l'échelle pour répondre à ces nouvelles demandes. Aujourd'hui en effet, il est courant de voir une augmentation d'équipes colocalisées et d'employés distants, notamment dans les entreprises possédant des bureaux aux États-Unis, en Europe et en Asie, avec une force de vente mobile présente dans de nombreuses régions. Sites Web Azure a la capacité de gérer des variations élastiques de l'échelle confortablement et automatiquement.

Avec Sites Web Microsoft Azure, les applications Web peuvent être configurées pour se mettre automatiquement à l'échelle via le portail de gestion selon deux modes : planification horaire ou par l'utilisation du processeur. La mise à l'échelle automatique de Sites Web Microsoft Azure fournit un moyen extrêmement souple et rentable de s'adapter aux évolutions de l'utilisation de toute application d'entreprise, qu'il s'agisse d'applications Web telles que notre système de notes de frais ou de sites Web marketing qui sont confrontés à une augmentation élevée de trafic sur une courte durée. Pour plus d'informations et pour obtenir des conseils sur la mise à l'échelle de vos applications Web à l'aide de Sites Web Microsoft Azure, consultez la rubrique [Mise à l'échelle des sites Web](http://azure.microsoft.com/documentation/articles/web-sites-scale/).

Outre la possibilité de mise à l'échelle de Sites Web Microsoft Azure, l'ensemble de la plateforme permet la continuité d'activité et la résilience via la répartition des applications Web et de leurs ressources sur plusieurs centres de données et régions géographiques.

## Résumé ##
Sites Web Microsoft Azure offre une solution souple, rentable et réactive qui s'adapte aux besoins dynamiques de l'entreprise dans un environnement à évolution rapide. Sites Web Azure s'emploie à augmenter la productivité et l'efficacité en utilisant une plateforme gérée avec des fonctionnalités modernes pour les opérations de développement et une gestion pratique réduite, tout en permettant la mise à l'échelle, la résilience, la sécurité et l'intégration aux ressources locales des fonctionnalités d'entreprise.

## Appel à l'action ##
Pour plus d'informations sur le service Sites Web Microsoft Azure, visitez [http://aka.ms/enterprisewebsites](http://aka.ms/enterprisewebsites). Vous pouvez vous inscrire pour un essai aujourd'hui sur [http://aka.ms/azuretrial](http://aka.ms/azuretrial) pour évaluer le service et découvrir les avantages qui en découlent pour votre entreprise.


<!--HONumber=42-->
