<properties
	pageTitle="Déploiement de SharePoint 2010 sur des machines virtuelles Azure"
	description="Présentation des scénarios pris en charge pour l’utilisation de SharePoint 2010 sur des machines virtuelles Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/28/2015"
	ms.author="josephd"/>


# Déploiement de SharePoint 2010 sur des machines virtuelles Azure

Microsoft SharePoint Server 2010 offre une grande flexibilité de déploiement, qui permet aux organisations de déterminer les scénarios de déploiement appropriés afin d’aligner leurs besoins et leurs objectifs. Hébergé et géré dans le cloud, Azure Virtual Machines offre une infrastructure complète, fiable et disponible afin de prendre en charge les charges de travail des différentes applications et bases de données à la demande, par exemple les déploiements Microsoft SQL Server et SharePoint.

Bien que Microsoft Azure Virtual Machines prenne en charge différentes charges de travail, cet article se concentre sur les déploiements SharePoint. Azure Virtual Machines permet aux organisations de créer et de gérer la mise en service rapide et l’accès universel à presque chaque hôte de leur infrastructure SharePoint. Azure Virtual Machines permet une gestion et un contrôle complets des processeurs, de la mémoire vive (RAM), des plages d'unité centrales et d'autres ressources des machines virtuelles SharePoint.

Azure Virtual Machines limite le besoin en matériel afin que les organisations puissent se tourner de la gestion du coût élevé initial et de la complexité vers la création et la gestion d'une infrastructure à l'échelle. Cela signifie qu’elles peuvent innover, expérimenter et itérer en quelques heures au lieu des jours et des semaines que prennent les déploiements traditionnels.

> [AZURE.NOTE]Pour en savoir plus sur le déploiement de SharePoint 2013 dans Microsoft Azure, consultez les pages [Planification pour SharePoint 2013 sur les services d’infrastructure Azure](https://msdn.microsoft.com/library/dn275958.aspx) et [Batterie de serveurs SharePoint hébergés dans des services d’infrastructure Azure](virtual-machines-sharepoint-infrastructure-services.md).

## SharePoint sur Azure Virtual Machines

La flexibilité de SharePoint 2010 prend en charge la plupart des charges de travail d'un déploiement Azure Virtual Machines. Azure Virtual Machines est optimal pour FIS (SharePoint Server pour sites Internet) et les scénarios de développement. De même, les charges de travail SharePoint principales sont également prises en charge. Si une organisation souhaite gérer et contrôler sa propre implémentation SharePoint 2010 tout en exploitant les options de la virtualisation dans le cloud, Azure Virtual Machines est idéal pour le déploiement.

L'offre Azure Virtual Machines est hébergée et gérée dans le cloud. Elle fournit une flexibilité de déploiement et réduit le coût en limitant les dépenses d'investissement dues à l'approvisionnement en matériel. Grâce à une plus grande souplesse d'infrastructure, les organisations peuvent déployer SharePoint Server en quelques heures au lieu de plusieurs jours ou semaines. Azure Virtual Machines permet également aux organisations de déployer des charges de travail SharePoint dans le cloud en utilisant le modèle du paiement à l’utilisation. Lorsque les charges de travail SharePoint s’accroissent, une organisation peut rapidement développer l’infrastructure ; puis lorsqu’elles diminuent, l’organisation peut renvoyer les ressources qui ne sont plus requises, en ne payant que ce qui a été utilisé.

### Réorientation des objectifs informatiques

De nombreuses organisations sous-traitent les composants courants de leur infrastructure informatique et de sa gestion, notamment le matériel, les systèmes d'exploitation, la sécurité, le stockage des données et la sauvegarde, tout en conservant le contrôle des applications critiques, SharePoint Server par exemple. En déléguant toutes les couches de service non critiques de leurs plateformes informatiques à un fournisseur virtuel, les organisations peuvent réorienter les objectifs informatiques sur des services SharePoint critiques et offrir de la valeur ajoutée avec les projets SharePoint au lieu de passer plus de temps à la configuration de leur infrastructure.

### Déploiement plus rapide

La prise en charge et le déploiement d'une vaste infrastructure SharePoint peut entraver la capacité du service informatique à agir rapidement pour prendre en charge les besoins de l'organisation. Le délai requis pour créer, tester et préparer les serveurs et batteries de serveurs SharePoint et les déployer dans un environnement de production peut prendre des semaines ou même des mois, selon les processus et contraintes de l'organisation. Azure Virtual Machines permet aux organisations de déployer rapidement leurs charges de travail SharePoint sans dépenses d'investissement pour l'achat de matériel. Ainsi, les organisations peuvent capitaliser sur la souplesse de l'infrastructure pour effectuer un déploiement en quelques heures au lieu de plusieurs jours ou semaines.

### Extensibilité

Sans la nécessité de déployer, de tester et de préparer les serveurs et batteries de serveurs physiques SharePoint, les organisations peuvent développer et acquérir une capacité de calcul à la demande, sans préavis. Lorsque la charge de travail SharePoint s'accroît, une organisation peut rapidement développer son infrastructure dans le cloud. De même; lorsque les besoins en calcul diminuent, l'organisation peut diminuer les ressources, en ne payant que ce qu'elle utilise. Azure Virtual Machines réduit les dépenses à l'avance et les engagements à long terme, permettant aux organisations de créer et de gérer les infrastructures à l'échelle. Répétons-le, cela signifie que ces organisations peuvent innover, expérimenter et itérer en quelques heures au lieu des jours et des semaines que prennent les déploiements traditionnels.

### Utilisation facturée à l'usage

Azure Virtual Machines fournit une puissance, une mémoire et un stockage de calcul pour les scénarios SharePoint, dont les prix sont généralement basés sur la consommation des ressources. Les organisations ne paient que ce qu'elles utilisent et le service fournit toute la capacité requise pour exécuter l'infrastructure SharePoint. Pour plus d'informations sur la tarification et la facturation, consultez la page [Tarification Azure](http://azure.microsoft.com/pricing/). Notez que des frais nominaux sont facturés pour le stockage et les données quittant le cloud Azure depuis un réseau local. Néanmoins, Azure ne facture pas le chargement des données.

### Flexibilité

Azure Virtual Machines offre aux développeurs la flexibilité de choisir leur langue ou environnement d'exécution, avec la prise en charge officielle de .NET, Node.js, Java et PHP. Les développeurs peuvent également choisir leurs outils, avec la prise en charge de Microsoft Visual Studio, WebMatrix, Eclipse et des éditeurs de texte. En outre, Microsoft fournit un chemin à faible coût et risque vers le cloud et offre une mise en service et un déploiement économes et conviviaux pour les besoins de reporting du cloud en fournissant un accès à l'aide à la décision sur l'ensemble des appareils et des sites. Enfin, avec l'offre Azure, les utilisateurs peuvent non seulement déplacer des disques durs virtuels vers le cloud, mais également copier un disque dur virtuel en sens inverse et l'exécuter localement ou par l'intermédiaire d'un autre fournisseur de cloud, du moment qu'il possède la licence appropriée.

## Processus de mise en service

La bibliothèque d’images d’Azure fournit la liste des machines virtuelles préconfigurées disponibles. Les utilisateurs peuvent publier SharePoint Server, SQL Server, Windows Server et d'autres ISO/VHD vers la bibliothèque d'images. Pour simplifier la création des machines virtuelles, des images de base sont créées et publiées dans la bibliothèque. Les utilisateurs autorisés peuvent utiliser ces images pour générer la machine virtuelle souhaitée. Pour en savoir plus, consultez la page [Création d’une machine virtuelle exécutant Windows dans la version préliminaire du portail Azure](virtual-machines-windows-tutorial.md). La figure 1 présente les étapes de base de création d’une machine virtuelle en utilisant le portail de gestion Microsoft Azure :

**Figure 1 : Vue d’ensemble des étapes de création d’une machine virtuelle**

![azure-sharepoint-wp-2](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-2.png)

Les utilisateurs peuvent charger une image préparée avec Sysprep dans le portail de gestion Azure. Pour en savoir plus, consultez la page [Création et téléchargement d’un disque dur virtuel Windows Server dans Azure](virtual-machines-create-upload-vhd-windows-server.md). La figure 2 présente les étapes de base de chargement d’une image afin de créer une machine virtuelle.

**Figure 2 : Vue d’ensemble des étapes de chargement d’une image**

![azure-sharepoint-wp-3](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-3.png)

## Déploiement de SharePoint 2010 sur Azure 

Vous pouvez déployer SharePoint 2010 sur Azure en procédant comme suit :

1. Connectez-vous au [portail de gestion Azure](http://manage.windowsazure.com/) en utilisant le compte lié à votre abonnement Azure. Si vous n'avez pas de compte Azure, [obtenez une version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/).
2. Pour créer une machine virtuelle avec le système d’exploitation de base, dans le portail de gestion Microsoft Azure, cliquez sur **Nouveau > Calcul > Machine virtuelle > À partir de la gallerie**.
3. La boîte de dialogue **Choisir une image** s'affiche. Cliquez sur l'image de la plateforme **Windows Server 2008 R2 SP1**, puis sur la flèche droite.
4. La boîte de dialogue **Virtual machine configuration** s’affiche. Fournissez les informations suivantes :
	- Entrez un **nom de machine virtuelle**.
	- Sélectionnez la taille appropriée. Pour un environnement de production (serveur et base de données d’application SharePoint), il est recommandé d’utiliser A3 (4 cœurs, mémoire 7 Go) ou une taille supérieure.
	- Dans le champ **Nouveau nom d’utilisateur**, tapez le nom d’un compte d’administrateur local.
	- Dans la zone **Nouveau mot de passe**, tapez un mot de passe fort.
	- Dans la zone **Confirmer**, tapez de nouveau le mot de passe, puis cliquez sur la flèche droite.
5. La deuxième boîte de dialogue **Configuration de la machine virtuelle** s'affiche. Fournissez les informations suivantes :
	- Dans **Service cloud**, sélectionnez **Créer un nouveau service de cloud computing**, puis saisissez un nom DNS de service de cloud computing, ou sélectionnez un service existant de cloud computing.
	- Dans la zone **Région/Groupe d’affinités/Réseau virtuel**, sélectionnez la région dans laquelle sera hébergée l’image virtuelle.
	- Dans la zone **Compte de stockage**, sélectionnez **Utiliser un compte de stockage généré automatiquement** ou sélectionnez un nom de compte de stockage existant. Un seul compte de stockage est automatiquement créé par région. Toutes les autres machines virtuelles comportant ce paramètre sont situées dans ce compte de stockage. Vous êtes limité à 20 comptes de stockage. Pour plus d'informations, consultez la page [Création d'un compte de stockage dans Azure](virtual-machines-create-upload-vhd-windows-server.md#step-2-create-a-storage-account-in-azure).
	- Dans le champ **Jeu de disponibilité**, sélectionnez **(aucun)**, puis cliquez sur la flèche droite.
6. Dans la troisième boîte de dialogue **Configuration de la machine virtuelle** cochez la case pour créer la machine virtuelle.

Pour plus d’informations sur la connexion à la machine virtuelle, consultez la page [Connexion à une machine virtuelle exécutant Windows Server](virtual-machines-log-on-windows-server.md).

Créez la machine virtuelle SQL Server à l'aide des options suivantes :

- Créez une machine virtuelle SQL Server 2012 en suivant les étapes 1 à 7 ci-dessus à l’exception de l’étape 3, utilisez l’image SQL Server 2012 au lieu de l’image Windows Server 2008 R2 SP1. Pour plus d'informations, consultez la page [Configuration d'une machine virtuelle SQL Server sur Azure](virtual-machines-provision-sql-server.md).
	- Lorsque vous sélectionnez cette option, le processus de mise en service conserve une copie des fichiers de configuration SQL Server 2012 dans le répertoire C:\SQLServer_11.0_Full afin que vous puissiez personnaliser l'installation. Par exemple, vous pouvez convertir l'installation d'évaluation de SQL Server 2012 en une version sous licence en utilisant votre clé de licence.

- Utilisez l'outil SQL Server System Preparation (SysPrep) pour installer SQL Server sur la machine virtuelle avec un système d'exploitation de base (comme indiqué ci-dessus aux étapes 1 à 7). Pour plus d'informations, consultez la page [Installation de SQL Server 2012 à l'aide de SysPrep](http://msdn.microsoft.com/library/ee210664.aspx).

- Utilisez l'invite de commandes pour installer SQL Server. Pour plus d'informations, consultez la page [Installation de SQL Server 2012 depuis une invite de commandes](http://msdn.microsoft.com/library/ms144259.aspx#SysPrep).

- Utilisez un support SQL Server pris en charge et votre clé de licence pour installer SQL Server sur la machine virtuelle avec un système d’exploitation de base (comme indiqué ci-dessus aux étapes 1 à 7).

Créez la batterie de serveurs SharePoint en procédant comme suit :

Étape 1. configurez l'abonnement Azure à l'aide de fichiers de script.

Étape 2. mettez les serveurs SharePoint en service en créant une autre machine virtuelle avec un système d’exploitation de base (comme indiqué ci-dessus aux étapes 1 à 6). Pour créer un serveur SharePoint sur cette machine virtuelle, sélectionnez l’une des options suivantes :

- Mise en service utilisant l'interface utilisateur graphique SharePoint :
	- Pour créer et mettre en service une batterie de serveurs SharePoint, consultez la page [Création d'une batterie de serveurs SharePoint](http://technet.microsoft.com/library/ee805948.aspx#CreateConfigure).
	- Pour ajouter un serveur Web ou d’applications à la batterie de serveurs, consultez la page [Ajouter un serveur Web ou d’applications à la batterie](http://technet.microsoft.com/library/cc261752.aspx).
	- Pour ajouter un serveur de base de données à une batterie de serveurs existante, consultez la page [Ajout d'un serveur de base de données à une batterie de serveurs existante](http://technet.microsoft.com/library/cc262781).
	- Pour utiliser SQL Server 2012 pour votre batterie de serveurs SharePoint, vous devez télécharger et installer le Service Pack 1 pour SharePoint Server 2010 après avoir installé l'application et choisi de ne pas configurer le serveur. Pour plus d'informations, consultez la page [Service Pack 1 pour SharePoint Server 2010](http://www.microsoft.com/download/details.aspx?id=26623).
	- Pour tirer parti des fonctionnalités de SQL Server BI, nous vous recommandons d'installer SharePoint Server en tant que batterie de serveurs plutôt qu'en tant que serveur autonome. Pour plus d'informations, consultez la page [Installation des fonctionnalités de SQL Server 2012 Business Intelligence](http://technet.microsoft.com/library/hh231681.aspx).

- Mise en service à l’aide de Microsoft Windows PowerShell : vous pouvez utiliser l’outil en ligne de commande Psconfig comme interface de remplacement pour réaliser plusieurs opérations contrôlant la manière dont les produits SharePoint 2010 sont mis en service. Pour plus d'informations, consultez la page [Informations de référence sur la ligne de commande Psconfig](http://technet.microsoft.com/library/cc263093.aspx).

Étape 3. configurez SharePoint. Dès que chaque machine virtuelle est prête, configurez SharePoint Server sur chaque serveur à l'aide de l'une des options suivantes :

- Configurez SharePoint depuis l'interface utilisateur graphique.
- Configurez SharePoint à l'aide de Windows PowerShell. Pour plus d'informations, consultez la page [Installer SharePoint Server 2010 à l'aide de Windows PowerShell](http://technet.microsoft.com/library/cc262839.aspx).
- Vous pouvez également utiliser AutoSPInstaller du projet CodePlex qui est composé de scripts Windows PowerShell, d'un fichier d'entrée XML et d'un fichier de commandes Microsoft Windows. AutoSPInstaller fournit une infrastructure pour un script d’installation SharePoint 2010 basé sur Windows PowerShell. Pour en savoir plus, consultez la page [CodePlex : AutoSPInstaller](http://autospinstaller.codeplex.com/).

Étape 4. Une fois le script terminé, connectez-vous à la machine virtuelle à l’aide du tableau de bord de cette dernière.

Pour vérifier la configuration de SharePoint, connectez-vous au serveur SharePoint, puis utilisez l’administration centrale.

> [AZURE.NOTE]assurez-vous de configurer la sécurité sur le point de terminaison du portail de gestion et de définir un port entrant sur le pare-feu Windows de la machine virtuelle. Puis, assurez-vous que vous pouvez démarrer une session Windows PowerShell à distance sur l’un des serveurs d’applications SharePoint en ouvrant une session Windows PowerShell avec les informations d’identification Administrateur.

### Création et chargement d’un disque dur virtuel

Vous pouvez également créer vos propres images et les charger dans Azure sous la forme d’un fichier de disque dur virtuel. Pour en savoir plus, consultez la page [Création et téléchargement d’un disque dur virtuel Windows Server dans Azure](virtual-machines-create-upload-vhd-windows-server.md).

## Scénarios d’utilisation

Cette section aborde quelques scénarios clients majeurs relatifs aux déploiements de SharePoint à l’aide d’Azure Virtual Machines.

### Scénario 1 : Développement SharePoint simple et environnement de test

Les organisations recherchent des moyens plus souples de créer des applications SharePoint et de configurer des environnements SharePoint pour le développement et le test à terre/extraterritorialisés. Fondamentalement, elles veulent raccourcir le délai requis pour configurer des projets de développement d'applications SharePoint et diminuer le coût en augmentant l'utilisation de leurs environnements de test. Par exemple, une organisation peut souhaiter réaliser un test de charge à la demande sur SharePoint Server et exécuter le test d'acceptation des utilisateurs (UAT) avec plus d'utilisateurs simultanés situés dans différentes régions. De même, l'intégration des équipes sur terre/extraterritorialisées est, de nos jours, un besoin de plus en plus important pour de nombreuses organisations.

Ce scénario explique comment les organisations peuvent utiliser des batteries de serveurs SharePoint préconfigurées pour le développement et les charges de test. Une topologie de déploiement SharePoint ressemble exactement à ce qu'elle serait dans un déploiement virtualisé local. Les compétences informatiques existantes sont complètement conservées dans un déploiement Azure Virtual Machines, l'avantage majeur résidant en un passage quasiment complet des dépenses d'investissement en dépenses opérationnelles, aucun achat à l'avance de serveur physique n'étant requis. Les organisations peuvent éliminer le coût d'investissement du matériel des serveurs et obtenir une souplesse en réduisant fortement le délai de mise en service requis pour créer, configurer ou étendre une batterie de serveurs SharePoint pour un environnement de test et de développement. Le service informatique peut ajouter et supprimer dynamiquement des capacités afin de prendre en charge des besoins variables en matière de test et de développement. En outre, le service informatique peut se concentrer davantage sur la fourniture de valeur ajoutée avec les projets SharePoint et moins sur la gestion de l'infrastructure.

Pour utiliser pleinement les machines de test de charge, les organisations peuvent configurer le développement virtualisé SharePoint et tester les machines sur Azure avec la prise en charge du système d'exploitation pour Windows Server 2008 R2. Ainsi, les équipes de développement peuvent créer et tester des applications et migrer facilement vers des environnements de production locaux ou cloud sans changement de code. Les mêmes infrastructures et jeux d'outils peuvent être utilisés en local et dans le cloud, permettant à l'équipe dispersée un accès au même environnement. Les utilisateurs peuvent également accéder à des données et applications locales en définissant une connexion VPN directe.

La figure 3 montre un environnement de développement et de test SharePoint dans une machine virtuelle Microsoft Azure. Pour générer ce déploiement, commencez par utiliser le même environnement de développement et de test SharePoint local utilisé pour développer les applications. Puis, chargez et déployez les applications sur la machine virtuelle Azure pour le test et le développement. Si votre organisation décide de replacer l’application en local, elle peut le faire sans avoir à modifier l’application.

**Figure 3:  Environnement de développement et de test SharePoint dans Microsoft Azure Virtual Machines**

![azure-sharepoint-wp-11](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-11.png)

Pour implémenter un environnement de développement et de test SharePoint sur Azure, procédez comme suit :

1. Mise en service : tout d’abord, mettez une connexion VPN en service entre les éléments locaux et Microsoft Azure à l’aide de Microsoft Azure Virtual Network. Active Directory n’étant pas utilisé ici, un tunnel VPN est requis. Pour en savoir plus, consultez la page [Présentation du réseau virtuel](http://msdn.microsoft.com/library/jj156007.aspx). Puis, utilisez le portail de gestion pour mettre en service une nouvelle machine virtuelle en utilisant une image stockée provenant de la bibliothèque d’images.
	- Vous pouvez charger les machines virtuelles locales de développement et de test SharePoint sur votre compte de stockage Azure et référencer ces dernières par l'intermédiaire de la bibliothèque d'images afin de générer l'environnement requis.
	- Vous pouvez utiliser l'image SQL Server 2012 au lieu de l'image Windows Server 2008 R2 SP1. Pour plus d'informations, consultez la page [Configuration d'une machine virtuelle SQL Server sur Azure](virtual-machines-provision-sql-server.md).

2. Installation : installez SharePoint Server, Visual Studio, et SQL Server sur les machines virtuelles à l’aide d’une connexion Bureau à distance.
	- Utilisez le script SharePoint 2010 Easy Setup Script pour créer un ordinateur de développeur SharePoint. Pour plus d'informations, consultez la page [SharePoint 2010 Easy Setup Script](http://www.microsoft.com/download/details.aspx?id=23415). Utilisez Windows PowerShell. Pour plus d'informations, consultez la page [Installer SharePoint Server 2010 à l'aide de Windows PowerShell](http://technet.microsoft.com/library/cc262839.aspx). Utilisez AutoSPInstaller du projet CodePlex. Pour en savoir plus, consultez la page [CodePlex : AutoSPInstaller](http://autospinstaller.codeplex.com/).
	- Installez Visual Studio. Pour plus d'informations, consultez la page [Installation de Visual Studio](http://msdn.microsoft.com/library/e2h7fzkw.aspx).
	- Installez SQL Server. Pour plus d'informations, consultez la page [Installation de SQL Server à l'aide de SysPrep](http://msdn.microsoft.com/library/ee210664.aspx).
3. Développer des packages et scripts de déploiement pour les applications et bases de données : si vous prévoyez d’utiliser une machine virtuelle provenant de la bibliothèque d’images, les applications et bases de données locales souhaitées peuvent être déployées sur Microsoft Azure Virtual Machines :
	- Créez des packages de déploiement pour les applications et bases de données locales existantes à l’aide SQL Server Data Tools et Visual Studio.
	- Utilisez ces packages pour déployer des applications et bases de données sur Azure Virtual Machines.
4. Déploiement d’applications et bases de données SharePoint :
	- Configurez la sécurité sur le point de terminaison du portail de gestion et définissez un port entrant sur le pare-feu Windows de la machine virtuelle.
	- Déployez des applications et bases de données SharePoint sur Azure Virtual Machines à l'aide des packages et scripts de déploiement créés à l'étape 3.
- Testez les applications et bases de données déployées.
5. Gérer les machines virtuelles :
	- Surveillez les machines virtuelles à l'aide du portail de gestion.
	- Surveillez les applications à l’aide de Visual Studio et SQL Server Management Studio.
	- Vous pouvez également surveiller et gérer les machines virtuelles à l’aide d’un logiciel de gestion local, tel que Microsoft System Center Operations Manager.

### Scénario 2 : Batterie de serveurs SharePoint publique avec personnalisation

Les organisations veulent créer une présence Internet hébergée dans le cloud et facilement extensible basée sur le besoin et la demande. Elles veulent également créer des sites Web extranet partenaires pour la collaboration et implémenter un processus convivial pour la création et l'approbation distribuées du contenu des sites Web. Enfin, pour traiter des charges en augmentation constante, les organisations veulent fournir une capacité à la demande sur leurs sites Web.

Dans ce scénario, SharePoint Server est utilisé comme base d'hébergement d'un site Web public. Cela permet aux organisations de déployer, de personnaliser et d'héberger rapidement leurs sites Web professionnels sur une infrastructure de cloud sécurisée et extensible. Avec les sites Web publics SharePoint sur Azure, les organisations peuvent se mettre à l'échelle lorsque le trafic augmente et ne payer que ce qu'elles utilisent. Des outils courants, similaires à ceux utilisés en local, peuvent être utilisés pour la création, le workflow et l'approbation du contenu avec SharePoint sur Azure.

En outre, à l'aide d'Azure Virtual Machines, les organisations peuvent facilement configurer des environnements intermédiaires et de production s'exécutant sur des machines virtuelles. Les machines virtuelles publiques SharePoint créées dans Azure peuvent être sauvegardées sur un stockage virtuel. En outre, à des fins de récupération d’urgence, la fonctionnalité de géo-réplication continue permet aux organisations de sauvegarder automatiquement des machines virtuelles opérant dans un centre de données vers un autre centre de données situé à des kilomètres.

Les machines virtuelles de l’infrastructure Azure sont validées et prises en charge pour fonctionner avec d’autres produits Microsoft, tels que SQL Server et SharePoint Server. Microsoft Azure et SharePoint Server fonctionnent mieux ensemble : les deux appartiennent à la famille Microsoft et sont parfaitement intégrés, pris en charge et testés ensemble pour offrir une expérience optimale. Ils ont tous deux un seul point de support pour l’application SharePoint et l’infrastructure Azure.

Dans ce scénario, des serveurs Web frontaux supplémentaires pour SharePoint Server doivent être ajoutés afin de prendre en charge un trafic supplémentaire. Ces serveurs requièrent une sécurité améliorée et des contrôleurs de domaine de services de domaine Active Directory afin de prendre en charge l’authentification et l’autorisation utilisateur. La figure 4 présente la disposition de ce scénario.

**Figure 4 : Batterie de serveurs SharePoint publique avec personnalisation**

![azure-sharepoint-wp-12](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-12.png)

Pour implémenter une batterie de serveurs SharePoint publique sur Azure, procédez comme suit :

1. Déployer Active Directory : les exigences fondamentales de déploiement d’Active Directory sur Microsoft Azure Virtual Machines sont similaires mais pas identiques au déploiement sur des machines virtuelles (et, dans une certaine mesure, sur des machines physiques) locales. Pour plus d'informations sur les différences ainsi que pour des instructions et autres considérations, consultez la page [Recommandations en matière de déploiement de Windows Server Active Directory sur des machines virtuelles Azure](http://msdn.microsoft.com/library/jj156090). Pour déployer Active Directory dans Azure :
	- Définissez et créez un réseau virtuel où les machines virtuelles peuvent être affectées à des sous-réseaux spécifiques.
	- Utilisez le portail de gestion pour créer et déployer le contrôleur de domaine sur une nouvelle machine virtuelle sur Azure. Vous pouvez également vous reporter au script Windows PowerShell pour déployer un domaine autonome dans le cloud en utilisant Azure Virtual Machines et Virtual Network. Pour plus d'informations sur la création d'une forêt Active Directory sur une machine virtuelle dans Azure Virtual Network, consultez la page [Installation d'une nouvelle forêt Active Directory dans Azure](active-directory-new-forest-virtual-machine.md).
2. Mise en service d’une machine virtuelle : utilisez le portail de gestion pour mettre en service une nouvelle machine virtuelle à partir d’une image stockée provenant de la bibliothèque d’images.
3. Déployez une batterie de serveurs SharePoint.
	- Utilisez le portail de gestion pour configurer l’équilibrage de la charge. Configurez les points de terminaison de la machine virtuelle, sélectionnez l'option permettant d'équilibrer la charge du trafic sur un point de terminaison existant, puis spécifiez le nom de la machine virtuelle à charge équilibrée.
	- Ajoutez une autre machine virtuelle Web frontale à la batterie de serveurs SharePoint existante pour un trafic supplémentaire.
3. Gérer les machines virtuelles :
	- Surveillez les machines virtuelles à l'aide du portail de gestion.
	- Surveillez la batterie de serveurs SharePoint à l’aide de l’administration centrale.

### Scénario 3 : Batterie de serveurs montée en charge pour des services décisionnels supplémentaires

L’aide à la décision est essentielle pour obtenir des idées clés et prendre des décisions rapides et correctes. Lorsque les organisations quittent une approche locale, elles ne veulent pas apporter de modifications à l'environnement décisionnel lors du déploiement des applications décisionnelles existantes dans le cloud. Elles veulent héberger des rapports provenant de SQL Server Analysis Services (SSAS) ou SQL Server Reporting Services (SSRS) dans un environnement fortement durable et disponible, tout en gardant un contrôle complet de l'application décisionnelle, le tout sans passer beaucoup de temps et dépenser beaucoup de budget en maintenance.

Ce scénario décrit comment les organisations peuvent utiliser Azure Virtual Machines pour héberger des applications décisionnelles critiques. Les organisations peuvent déployer des batteries de serveurs SharePoint dans Azure Virtual Machines et monter en charge les composants décisionnels des machines virtuelles du serveur d'applications, tels que SSRS ou Excel Services. En mettant à l'échelle les composants utilisant beaucoup de ressources dans le cloud, les machines virtuelles peuvent plus facilement et mieux prendre en charge des charges de travail spécialisées. Notez que SQL Server dans Azure Virtual Machines offre d’excellentes performances, car il est facile de mettre à l’échelle des instances de SQL Server, dans des petites installations comme des très grandes. Ainsi, les organisations jouissent d’une grande flexibilité, leur permettant de mettre en service dynamiquement (développer) ou de supprimer les privilèges d’accès (réduire) des instances décisionnelles selon les besoins immédiats en charge de travail.

La migration des applications décisionnelles existantes vers Azure fournit une meilleure mise à l’échelle. Avec la puissance de SSAS, de SSRS et de SharePoint Server, les organisations peuvent créer une aide à la décision puissante et des applications et tableaux de bord de reporting pouvant être montés en charge ou réduits. Ces derniers peuvent également être intégrés de manière plus sécurisée aux données et applications locales. Azure garantit la conformité du centre de données grâce à la prise en charge de la norme ISO 27001. Pour plus d’informations, consultez le [Centre de gestion de la confidentialité Azure](http://azure.microsoft.com/support/trust-center/compliance/).

Pour monter en charge le déploiement des composants décisionnels, un nouveau serveur d’applications avec des services tels que PowerPivot, Power View, Excel Services ou PerformancePoint Services doit être installé. Ou, des instances décisionnelles SQL Server telles que SSAS ou SSRS doivent être ajoutées à la batterie de serveurs existante afin de prendre en charge un traitement supplémentaire des requêtes. Le serveur peut être ajouté à une nouvelle machine virtuelle Azure avec SharePoint 2010 Server ou SQL Server installé. Puis, les composants décisionnels peuvent être installés, déployés et configurés sur ce serveur (figure 5).

**Figure 5 : Batterie de serveurs SharePoint montée en charge pour des services décisionnels supplémentaires**

![azure-sharepoint-wp-13](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-13.png)

Pour monter en charge un environnement décisionnel sur Azure, procédez comme suit :

1. Mettre en service :
	- Mettez une connexion VPN en service entre les éléments locaux et Azure à l’aide d’Azure Virtual Network. Pour en savoir plus, consultez la page [Présentation du réseau virtuel](http://msdn.microsoft.com/library/jj156007.aspx).
	- Utilisez le portail de gestion pour mettre en service une nouvelle machine virtuelle à partir d’une image stockée provenant de la bibliothèque d’images. Vous pouvez charger des images de charge de travail décisionnelles SharePoint Server ou SQL Server dans la bibliothèque d’images, et tout utilisateur autorisé peut sélectionner ces machines virtuelles de composants décisionnels pour créer un environnement monté en charge.
2. Installer : 
	- si votre organisation ne possède pas d'images prégénérées des composants décisionnels SharePoint Server ou SQL Server, installez SharePoint Server et SQL Server sur les machines virtuelles à l'aide d'une connexion Bureau à distance.
	- Pour plus d’informations sur l’installation de SharePoint, consultez les pages [Installation de SharePoint Server 2010 à l’aide de Windows PowerShell](http://technet.microsoft.com/library/cc262839.aspx) ou [CodePlex : AutoSPInstaller](http://autospinstaller.codeplex.com/).
	- Pour plus d'informations sur l'installation de SQL Server, consultez la page [Installation de SQL Server à l'aide de SysPrep](http://msdn.microsoft.com/library/ee210664.aspx).
3. Ajouter la machine virtuelle décisionnelle :
	- Configurez la sécurité sur le point de terminaison du portail de gestion et définissez un port entrant sur le pare-feu Windows de la machine virtuelle.
	- Ajoutez la machine virtuelle décisionnelle nouvellement créée à la batterie de serveurs SharePoint ou SQL Server existante.
4. Gérer les machines virtuelles :
	- Surveillez les machines virtuelles à l'aide du portail de gestion.
	- Surveillez la batterie de serveurs SharePoint à l'aide de l'administration centrale.
	- Surveillez et gérez les machines virtuelles à l’aide d’un logiciel de gestion local tel que Microsoft System Center - Operations Manager.

### Scénario 4 : Site Web entièrement personnalisé basé sur SharePoint

De plus en plus, les organisations souhaitent créer des sites Web SharePoint entièrement personnalisés dans le cloud. Elles ont besoin d'un environnement fortement durable et disponible qui offre un contrôle complet permettant de gérer des applications complexes s'exécutant dans le cloud. Mais, elles ne souhaitent pas y consacrer beaucoup de temps, ni de budget.

Dans ce scénario, une organisation peut déployer l’ensemble de sa batterie de serveurs SharePoint dans le cloud et mettre dynamiquement à l’échelle tous les composants afin d’obtenir une capacité supplémentaire. Ou bien, elle peut développer son déploiement local vers le cloud afin d’accroître la capacité et d’améliorer les performances, le cas échéant. Le scénario se concentre sur les organisations souhaitant une « expérience SharePoint » totale pour le développement des applications et la gestion du contenu. Les sites les plus complexes peuvent également inclure un reporting amélioré, Power View, PerformancePoint, PowerPivot, des graphiques détaillés et la plupart des autres fonctionnalités de site SharePoint pour des fonctionnalités de bout en bout et complètes.

Les organisations peuvent utiliser Azure Virtual Machines pour héberger des applications personnalisées et des composants associés sur une infrastructure de cloud économe et fortement sécurisée. Elles peuvent également utiliser Microsoft System Center local comme outil de gestion commun pour les applications locales et cloud.

Pour implémenter un site Web SharePoint entièrement personnalisé sur Azure, une organisation doit déployer un domaine Active Directory dans le cloud et mettre en service de nouvelles machines virtuelles dans ce domaine. Puis, une machine virtuelle sous SQL Server 2012 doit être créée, configurée et intégrée à la batterie de serveurs SharePoint. Enfin, la batterie de serveurs SharePoint doit être créée, équilibrée en charge et connectée à Active Directory et SQL Server (figure 6).

**Figure 6 : Site Web entièrement personnalisé basé sur SharePoint**

![azure-sharepoint-wp-14](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-14.png)

Les étapes suivantes expliquent comment créer un environnement personnalisé de batteries de serveurs SharePoint à partir d’images prégénérées disponibles dans la bibliothèque d’images. Notez, néanmoins, que vous pouvez également charger des machines virtuelles de batterie de serveurs SharePoint dans la bibliothèque d’images, et les utilisateurs autorisés peuvent choisir ces machines virtuelles pour créer la batterie de serveurs SharePoint requise sur Azure.

1. Déployer Active Directory : les exigences fondamentales de déploiement d’Active Directory sur Microsoft Azure Virtual Machines sont similaires mais pas identiques au déploiement sur des machines virtuelles (et, dans une certaine mesure, sur des machines physiques) locales. Pour plus d'informations sur les différences ainsi que pour des instructions et autres considérations, consultez la page [Recommandations en matière de déploiement de Windows Server Active Directory sur des machines virtuelles Azure](http://msdn.microsoft.com/library/jj156090). Pour déployer Active Directory dans Azure :
	- Définissez et créez un réseau virtuel où les machines virtuelles peuvent être affectées à des sous-réseaux spécifiques.
	- Utilisez le portail de gestion pour créer et déployer le contrôleur de domaine sur une nouvelle machine virtuelle sur Azure.
	- Pour plus d'informations sur la création d'une forêt Active Directory sur une machine virtuelle dans Azure Virtual Network, consultez la page [Installation d'une nouvelle forêt Active Directory dans Azure](active-directory-new-forest-virtual-machine).
2. Déployer SQL Server :
	- Utilisez le portail de gestion pour mettre en service une nouvelle machine virtuelle à partir d’une image stockée provenant de la bibliothèque d’images.
	- Configurez SQL Server sur la machine virtuelle. Pour plus d'informations, consultez la page [Installation de SQL Server à l'aide de SysPrep](http://msdn.microsoft.com/library/ee210664.aspx).
	- Liez la machine virtuelle au domaine Active Directory nouvellement créé.
3. Déployer une batterie de serveurs SharePoint multiserveur :
	- Créez un réseau virtuel. Pour en savoir plus, consultez la page [Présentation du réseau virtuel](http://msdn.microsoft.com/library/jj156007.aspx).
	- Lorsque vous déployez les machines virtuelles SharePoint, vous avez besoin des sous-réseaux fournis pour SharePoint Server afin que les adresses DNS de la zone Active Directory locale soient disponibles au cours de la mise en service.
	- Utilisez le portail de gestion pour créer une machine virtuelle.
	- Installez SharePoint Server sur cette machine virtuelle et générez une image réutilisable. Pour plus d’informations sur l’installation de SharePoint Server, consultez les pages [Installation et configuration de SharePoint Server 2010 à l’aide de Windows PowerShell](http://technet.microsoft.com/library/cc262839.aspx) ou [CodePlex : AutoSPInstaller](http://autospinstaller.codeplex.com/).
	- Configurez la machine virtuelle SharePoint afin de créer la batterie de serveurs SharePoint et de s'y connecter en utilisant la commande [Join-SharePointFarm](http://technet.microsoft.com/library/ff607979.aspx).
	- Utilisez le portail de gestion pour configurer l’équilibrage de la charge : configurez les points de terminaison de la machine virtuelle, sélectionnez l’option permettant d’équilibrer la charge du trafic sur un point de terminaison existant, puis spécifiez le nom de la machine virtuelle à charge équilibrée.
4. Gérer la batterie de serveurs SharePoint par l’intermédiaire de System Center :
	- Utilisez l’agent Operations Manager et le nouveau Azure Integration Pack pour connecter votre System Center local à Microsoft Azure Virtual Machines.
	- Utilisez l’App Controller et l’Orchestrator locaux pour les fonctions de gestion.

## Résumé

Azure Virtual Machines offre une gamme complète de déploiements SharePoint. Azure Virtual Machines est entièrement pris en charge et testé afin de fournir une expérience optimale avec les autres applications Microsoft. Ainsi, les organisations peuvent facilement configurer et déployer SharePoint Server dans Azure, soit pour mettre en service une infrastructure pour un nouveau déploiement SharePoint, soit pour en développer un existant. Lorsque la charge de travail s'accroît, les organisations peuvent rapidement développer leur infrastructure SharePoint. De même, si la charge de travail diminue, les organisations peuvent souscrire à des ressources à la demande, en ne payant que ce qu’elles utilisent. Microsoft Azure Virtual Machines fournit une infrastructure exceptionnelle pour une large gamme de besoins, comme indiqué dans les quatre scénarios basés sur SharePoint présentés dans ce document.

Un déploiement réussi de SharePoint Server sur Azure Virtual Machines requiert une planification solide, en tenant compte notamment de la gamme d’options d’architecture et de déploiement de batteries de serveurs critiques. Les informations et les meilleures pratiques présentées dans ce document permettent d’orienter les décisions d’implémentation d’un déploiement SharePoint informé.

## Ressources supplémentaires

[SharePoint sur Azure Virtual Machines](http://msdn.microsoft.com/library/dn275955.aspx)

[Batteries de serveurs SharePoint hébergés dans des services d’infrastructure Azure](virtual-machines-sharepoint-infrastructure-services.md)

[Charge de travail des services d’infrastructure Azure : batterie de serveurs SharePoint intranet](virtual-machines-workload-intranet-sharepoint-farm)

[Instructions d’implémentation des services d’infrastructure Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

 

<!---HONumber=58_postMigration-->