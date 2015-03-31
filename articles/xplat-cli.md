<properties 
	pageTitle="Interface de ligne de commande interplateforme Azure" 
	description="Installation et configuration de l'interface en ligne de commande interplateforme Azure pour gérer Azure Services" 
	editor="tysonn" 
	manager="timlt" 
	documentationCenter="" 
	authors="squillace" 
	services=""/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="command-line-interface" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="1/16/2015" 
	ms.author="rasquill"/>

# Installation et configuration de l'interface de ligne de commande interplateforme Azure

<div class="dev-center-tutorial-selector sublanding"><a href="/manage/install-and-configure-windows-powershell/" title="PowerShell">Interface de ligne de commande interplateforme PowerShell</a><a href="/manage/install-and-configure-cli/" title="Cross-Platform CLI" class="current"></a></div>

L'interface de ligne de commande interplateforme Azure (xplat-cli) fournit un ensemble de commandes interplateformes open source à utiliser avec la plateforme Azure. Elle offre presque les mêmes fonctionnalités que le portail de gestion Azure, notamment la possibilité de gérer des sites Web, des machines virtuelles, des services mobiles, la base de données SQL et d'autres services fournis par la plateforme Azure.

L'interface xplat-cli est écrite en JavaScript et requiert Node.js. Elle est implémentée à l'aide du Kit de développement logiciel (SDK) Azure pour Node.js et publiée sous licence Apache 2.0. Le référentiel du projet se trouve à la page [https://github.com/WindowsAzure/azure-sdk-tools-xplat](https://github.com/WindowsAzure/azure-sdk-tools-xplat).

Ce document décrit l'installation et la configuration de l'interface de ligne de commande interplateforme Azure et explique comment l'utiliser pour exécuter les tâches de base avec la plateforme Azure.

##Dans ce document

* [Installation et configuration de l'interface de ligne de commande interplateforme Azure](#install)
* [Connexion à votre abonnement Azure](#configure)
* [Utilisation de l'interface de ligne de commande interplateforme Azure](#use)
* [Création de scripts dans l'interface de ligne de commande interplateforme Azure](#script)
* [Ressources supplémentaires](#additional-resources)

## <a id="install">Installation et configuration de l'interface de ligne de commande interplateforme Azure</a>

Il existe deux façons d'installer l'interface xplat-cli : avec les packages d'installation pour Windows et OS X, et avec la commande **npm** si Node.js est installé sur votre système. Une fois l'interface xplat-cli installée, vous pouvez utiliser la commande **azure** à partir de votre interface de ligne de commande (interpréteur de commandes, terminal, invite de commandes) pour accéder aux commandes associées.

###Utilisation d'un programme d'installation

Les packages d'installation suivants sont disponibles :

* [Windows Installer][windows-installer]

* [Programme d'installation OS X][mac-installer]

###Utilisation de npm

Si Node.js est installé sur votre système, utilisez la commande suivante pour installer l'interface xplat-cli :

	npm install azure-cli -g

>[AZURE.NOTE] Vous devrez peut-être utiliser  `sudo` pour parvenir à exécuter la commande __npm__.

Si node.js n'est pas installé, vous devrez d'abord l'installer. Pour les systèmes Linux, vous devez avoir installé Node.js et soit utiliser **npm** pour installer l'interface xplat-cli comme décrit ci-après, soit la générer à partir de la source. Celle-ci est disponible à la page [http://go.microsoft.com/fwlink/?linkid=253472&clcid=0x409](http://go.microsoft.com/fwlink/?linkid=253472&clcid=0x409). Pour plus d'informations sur la création à partir de la source, consultez le fichier INSTALL inclus dans l'archive.

####Installation de node.js et npm sur des distributions qui utilisent la gestion des packages [dpkg](http://en.wikipedia.org/wiki/Dpkg) 
La plus courante de ces distributions utilisent soit l'[outil de packaging avancé (apt)](http://en.wikipedia.org/wiki/Advanced_Packaging_Tool), soit d'autres outils basés sur le format de package  `.deb`. Ubuntu et Debian en sont des exemples, mais il en existe beaucoup d'autres. 

La plupart des plus récentes de ces distributions requièrent l'installation de **nodejs-legacy** afin d'obtenir un outil **npm** correctement configuré pour installer azure-cli. Le code suivant illustre les commandes qui installent **npm** correctement sur Ubuntu 14.04.
	
	sudo apt-get install nodejs-legacy
	sudo apt-get install npm
	sudo npm install -g azure-cli

Certaines des distributions plus anciennes, comme Ubuntu 12.04, requièrent l'installation de la distribution binaire actuelle de node.js. Le code suivant montre comment procéder en installant et en utilisant **curl**. 

>[AZURE.NOTE] Ces commandes proviennent des instructions d'installation Joyent disponibles [ici](https://github.com/joyent/node/wiki/installing-node.js-via-package-manager). Toutefois, quand vous utilisez **sudo** comme cible de canal, vous devez toujours veiller à vérifier les scripts que vous installez et confirmer qu'ils font exactement ce que vous attendez avant de les exécuter via **sudo**.
	
	sudo apt-get install curl
	curl -sL https://deb.nodesource.com/setup | sudo bash -
	sudo apt-get install -y nodejs
	sudo npm install -g azure-cli

####Installation de node.js et npm sur des distributions qui utilisent la gestion des packages [rpm](http://en.wikipedia.org/wiki/RPM_Package_Manager)

L'installation de node.js sur des distributions RPM requiert l'activation du référentiel EPEL. Le code suivant illustre les méthodes conseillées pour l'installation sur CentOS 7. (Notez que dans la première ligne, ci-dessous, le " - " (tiret) est important !)

	su -     
	yum update [enter]
	yum upgrade -y [enter] 
	yum install epel-release [enter]
	yum install nodejs [enter] 
	yum install npm [enter] 
	npm install -g azure-cli  [enter]


Une fois l'interface xplat-cli installée, vous pouvez utiliser la commande **azure** à partir de votre interface de ligne de commande (interpréteur de commandes, terminal, invite de commandes) pour accéder aux commandes associées. À la fin de l'installation, des informations similaires aux suivantes s'affichent normalement :

	azure-cli@0.8.0 ..\node_modules\azure-cli
	|-- easy-table@0.0.1
	|-- eyes@0.1.8
	|-- xmlbuilder@0.4.2
	|-- colors@0.6.1
	|-- node-uuid@1.2.0
	|-- async@0.2.7
	|-- underscore@1.4.4
	|-- tunnel@0.0.2
	|-- omelette@0.1.0
	|-- github@0.1.6
	|-- commander@1.0.4 (keypress@0.1.0)
	|-- xml2js@0.1.14 (sax@0.5.4)
	|-- streamline@0.4.5
	|-- winston@0.6.2 (cycle@1.0.2, stack-trace@0.0.7, async@0.1.22, pkginfo@0.2.3, request@2.9.203)
	|-- kuduscript@0.1.2 (commander@1.1.1, streamline@0.4.11)
	|-- azure@0.7.13 (dateformat@1.0.2-1.2.3, envconf@0.0.4, mpns@2.0.1, mime@1.2.10, validator@1.4.0, xml2js@0.2.8, wns@0.5.3, request@2.25.0)

> [AZURE.NOTE] Vous pouvez également installer Node.js et npm sur Windows à partir de <a href="http://nodejs.org/">http://nodejs.org/</a>.

## <a id="configure">Connexion à votre abonnement Azure</a>

Certaines commandes fournies par l'interface xplat-cli fonctionnent sans abonnement Azure, mais la plupart exigent un abonnement. Pour configurer l'interface xplat-cli de manière à utiliser votre abonnement, vous pouvez :

* Télécharger et utiliser un fichier de paramètres de publication.

OU

* Vous connecter à Azure en utilisant un compte professionnel. Lorsque vous vous connectez, Azure Active Directory est utilisé pour authentifier les informations d'identification.

Pour vous aider à choisir la méthode d'authentification adaptée à vos besoins, tenez compte des informations suivantes :

*  La méthode par connexion peut faciliter la gestion de l'accès à un abonnement, mais risque d'interrompre l'automatisation, car les informations d'identification peuvent dépasser le délai d'expiration et vous obliger à vous reconnecter.

	> [AZURE.NOTE] La méthode par connexion fonctionne uniquement avec un compte professionnel.  Il s'agit d'un utilisateur géré par votre organisation et défini dans le client Azure Active Directory de vos organisations. Si vous ne possédez pas de compte professionnel et que vous utilisez un compte Microsoft pour vous connecter à votre abonnement Azure, vous pouvez en créer un facilement en procédant comme suit.
	> 
	> 1. Connectez-vous au [portail de gestion Azure][portal] et cliquez sur **Active Directory**.
	> 
	> 2. S'il n'existe aucun annuaire, sélectionnez **Create your directory** et fournissez les informations demandées.
	> 
	> 3. Sélectionnez votre annuaire et ajoutez un nouvel utilisateur. Il s'agit d'un compte professionnel.
	> 
	>     Pendant la création de l'utilisateur, une adresse de messagerie est fournie pour l'utilisateur, ainsi qu'un mot de passe temporaire. Conservez ces informations, car elles vous serviront à une étape ultérieure.
	> 
	> 4. Dans le portail de gestion, sélectionnez **Paramètres**, puis **Administrateurs**. Select **Add**, and add the new user as a co-administrator. Cela permet au compte professionnel de gérer votre abonnement Azure.
	> 
	> 5. Pour finir, déconnectez-vous du portail Azure et reconnectez-vous en utilisant le nouveau compte professionnel. Si vous vous connectez pour la première fois avec ce compte, vous êtes invité à changer le mot de passe.
	>
	>Pour plus d'informations sur les comptes professionnels avec Microsoft Azure, consultez la rubrique [Inscription à Microsoft Azure en tant qu'organisation][signuporg].

*  La méthode basée sur un fichier de paramètres de publication installe un certificat qui vous permet d'exécuter les tâches de gestion pendant toute la durée de validité de l'abonnement et du certificat. Cette méthode facilite l'automatisation des tâches de longue durée. Une fois les informations téléchargées et importées, vous n'avez plus besoin de les fournir de nouveau. Elle complique cependant la gestion de l'accès à un abonnement, car quiconque a accès au certificat peut gérer l'abonnement.

Pour plus d'informations sur la gestion de l'authentification et de l'abonnement, consultez la rubrique [" Quelle est la différence entre l'authentification basée sur les comptes et l'authentification basée sur les certificats ? "][authandsub].

Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la rubrique [Version d'évaluation gratuite d'Azure][free-trial].

###Utilisation de la méthode par connexion

Pour vous connecter avec un compte professionnel, utilisez la commande suivante :

	azure login -u username -p password

Si vous voulez vous connecter en tapant de manière interactive (par exemple, si vous souhaitez que votre mot de passe soit masqué à l'écran), utilisez la commande suivante :

	azure login

et vous serez invité à entrer vos informations d'identification et les caractères de votre mot de passe seront masqués.

> [AZURE.NOTE] Si vous vous connectez pour la première fois avec ces informations d'identification, vous recevez un message vous invitant à vérifier que vous souhaitez bien mettre en cache un jeton d'authentification. Ce message s'affiche également si vous avez préalablement utilisé la commande  `azure logout` décrite plus loin. Pour ignorer cette invite pour les scénarios d'automatisation, utilisez le paramètre `-q` avec la commande  `azure login`.
>
> Quand vous vous authentifiez avec un compte professionnel, les informations d'accès à votre abonnement Azure sont stockées dans un annuaire  `.azure` situé dans votre répertoire  `user`. Votre répertoire  `user` est protégé par votre système d'exploitation ; toutefois, il est recommandé de prendre des mesures supplémentaires pour chiffrer votre répertoire  `user`. Pour ce faire, procédez comme suit :

Pour vous déconnecter, utilisez la commande suivante :

	azure logout -u <username>

> [AZURE.NOTE] Si les abonnements associés au compte étaient authentifiés uniquement avec Active Directory, la déconnexion supprime les informations d'abonnement du profil local. Cependant, si un fichier de paramètres de publication a également été importé pour les abonnements, la déconnexion supprime uniquement les informations Active Directory du profil local.

> [AZURE.NOTE] Les commandes suivantes ne fonctionnent pas correctement quand un compte est utilisé pour l'authentification :
> 
> * `azure vm`
> * `azure network`
> * `azure mobile`
> 
> Si vous avez besoin de recourir à ces commandes, utilisez un fichier de paramètres de publication pour vous authentifier auprès d'Azure, comme décrit dans la section suivante.

### Utilisation de la méthode basée sur un fichier de paramètres de publication

Pour télécharger les paramètres de publication de votre compte, utilisez la commande suivante :

	azure account download

Cette commande ouvre votre navigateur par défaut et vous invite à vous connecter au portail de gestion Azure. Une fois que vous êtes connecté, un fichier  `.publishsettings` est téléchargé. Prenez note de son emplacement.

> [AZURE.NOTE] Si votre compte est associé à plusieurs clients Azure Active Directory, vous pouvez être invité à sélectionner l'annuaire Active Directory pour lequel vous voulez télécharger un fichier de paramètres de publication.
> 
> Une fois que vous avez sélectionné l'annuaire Active Directory à partir de la page de téléchargement ou en accédant au portail de gestion Azure, il est utilisé par défaut par le portail et la page de téléchargement. Une fois qu'un paramètre par défaut a été défini, le texte " cliquez ici pour revenir à la page de sélection " apparaît en haut de la page de téléchargement. Utilisez le lien affiché pour revenir à la page de sélection.

Importez ensuite le fichier  `.publishsettings` en exécutant la commande suivante, en remplaçant `[chemin d'accès du fichier .publishsettings]` par le chemin d'accès de votre fichier  `.publishsettings` :

	azure account import [path to .publishsettings file]

> [AZURE.NOTE] Quand vous importez des paramètres de publication, les informations d'accès à votre abonnement Azure sont stockées dans un annuaire  `.azure` situé dans votre répertoire  `user`. Votre répertoire  `user` est protégé par votre système d'exploitation ; toutefois, il est recommandé de prendre des mesures supplémentaires pour chiffrer votre répertoire  `user`. Pour ce faire, procédez comme suit :
>
> * Sur Windows, modifiez les propriétés d'annuaire ou utilisez BitLocker.
> * Sur Mac, activez FileVault pour l'annuaire.
> * Sous Ubuntu, utilisez la fonctionnalité d'annuaire Encrypted Home. Les autres versions de Linux fournissent des fonctionnalités équivalentes.

Après l'importation de vos paramètres de publication, vous devez supprimer le fichier  `.publishsettings`, car il n'est plus nécessaire aux outils en ligne de commande et présente un risque de sécurité dans la mesure où il peut être utilisé pour accéder à votre abonnement.

###Abonnements multiples

Si vous possédez plusieurs abonnements Azure, la connexion donne l'accès à tous les abonnements associés à vos informations d'identification. Si vous utilisez un fichier de paramètres de publication  `.publishsettings`, celui-ci contient les informations concernant tous les abonnements. Avec l'une ou l'autre méthode, un abonnement spécifique est sélectionné comme abonnement par défaut et utilisé par l'interface xplat-cli lors de l'exécution des opérations. Vous pouvez afficher les abonnements et identifier l'abonnement par défaut, mais en utilisant la commande  `azure account list`. Cette commande renvoie des informations similaires aux suivantes :

	info:    Executing command account list
	data:    Name              Id                                    Current
	data:    ----------------  ------------------------------------  -------
	data:    Azure-sub-1       ####################################  true
	data:    Azure-sub-2       ####################################  false

Dans la liste ci-dessus, la colonne **Current** indique l'abonnement par défaut actuel comme étant Azure-sub-1. Pour modifier l'abonnement par défaut, utilisez la commande  `azure account set` et spécifiez l'abonnement que vous souhaitez utiliser par défaut. Par exemple :

	azure account set Azure-sub-2

Cette commande sélectionne Azure-sub-2 comme abonnement par défaut. 

> [AZURE.NOTE] Le changement de l'abonnement par défaut prend effet immédiatement et il s'agit d'un changement global. Les nouvelles commandes xplat, qu'elles soient exécutées à partir de la même instance de ligne de commande ou d'une autre instance, utiliseront le nouvel abonnement par défaut.

Si vous souhaitez utiliser avec l'interface xplat-cli un autre abonnement que l'abonnement par défaut actuel, mais sans changer ce dernier, vous pouvez utiliser l'option " --subscription " et fournir le nom de l'abonnement à utiliser pour l'opération.

<h2><a id="use"></a>Utilisation de l'interface de ligne de commande interplateforme Azure</h2>

L'interface xplat-cli est accessible avec la commande  `azure`. Pour afficher la liste des commandes disponibles, utilisez la commande  `azure` sans paramètres. Des informations d'aide similaires aux suivantes s'affichent normalement :

	info:             _    _____   _ ___ ___
	info:            /_\  |_  / | | | _ \ __|
	info:      _ ___/ _ \__/ /| |_| |   / _|___ _ _
	info:    (___  /_/ \_\/___|\___/|_|_\___| _____)
	info:       (_______ _ _)         _ ______ _)_ _
	info:              (______________ _ )   (___ _ _)
	info:
	info:    Windows Azure: Microsoft's Cloud Platform
	info:
	info:    Tool version 0.8.0
	help:
	help:    Display help for a given command
	help:      help [options] [command]
	help:
	help:    Opens the portal in a browser
	help:      portal [options]
	help:
	help:    Commands:
	help:      account        Commands to manage your account information and publish settings
	help:      config         Commands to manage your local settings
	help:      hdinsight      Commands to manage your HDInsight accounts
	help:      mobile         Commands to manage your Mobile Services
	help:      network        Commands to manage your Networks
	help:      sb             Commands to manage your Service Bus configuration
	help:      service        Commands to manage your Cloud Services
	help:      site           Commands to manage your Web Sites
	help:      sql            Commands to manage your SQL Server accounts
	help:      storage        Commands to manage your Storage objects
	help:      vm             Commands to manage your Virtual Machines
	help:
	help:    Options:
	help:      -h, --help     output usage information
	help:      -v, --version  output the application version

Les commandes du premier niveau répertoriées ci-dessus contiennent des commandes utilisées dans un domaine particulier d'Azure. Par exemple, la commande  `azure account` contient des commandes en rapport avec votre abonnement Azure, comme les paramètres  `download` et  `import` utilisés préalablement.

La plupart des commandes utilisent le format `azure <commande> <opération> [paramètres]` et exécutent une opération sur un service ou un objet comme la configuration de votre compte. D'autres commandes proposent des sous-commandes et respectent le format `azure <commande> <sous-commande> <opération> [paramètres]`. Voici des exemples de commandes qui fonctionnent avec la configuration de votre compte :

* Pour afficher les abonnements que vous avez importés, utilisez :

		azure account list

* Si vous avez importé des abonnements, utilisez la commande suivante pour en définir un comme abonnement par défaut :

		azure account set <subscription>

Le paramètre " --help " ou " -h " peut être utilisé pour afficher l'aide relative à des commandes particulières. Le format `azure help [commande] [options]` permet également de renvoyer les mêmes informations. Les commandes suivantes, par exemple, renvoient les mêmes informations :

	azure account set --help

	azure account set -h

	azure help account set

Si vous avez des doutes sur les paramètres exigés par une commande, reportez-vous à l'aide en utilisant `--help`, `-h` ou `azure help [commande]`.

###Définition du mode de configuration

L'interface xplat-cli vous permet d'exécuter des opérations de gestion sur des _ressources_ individuelles, qui correspondent à des entités gérées par l'utilisateur, telles qu'un serveur de base de données, une base de données ou un site web. Il s'agit du mode de fonctionnement par défaut de l'interface xplat-cli qui est désigné sous le nom d'**Azure Service Management**. Toutefois, dans le cas d'une solution complexe comportant plusieurs ressources, il est utile de pouvoir gérer la solution entière comme une seule et même unité.

Pour prendre en charge la gestion d'un groupe de ressources comme une seule unité logique, ou groupe de ressources, nous avons introduit une version préliminaire du **Gestionnaire de ressources** comme nouveau moyen de gestion des ressources Azure. 

>[AZURE.NOTE] Le Gestionnaire de ressources en est actuellement au stade préliminaire et ne fournit pas des capacités de gestion du même niveau qu'Azure Service Management.

Pour prendre en charge le nouveau gestionnaire de ressources, l'interface xplat-cli vous permet de basculer entre ces  'modes' de gestion avec la commande  `azure config mode`.

L'interface xplat-cli utilise par défaut le mode Azure Service Management. Pour basculer en mode Gestionnaire de ressources, utilisez la commande suivante :

	azure config mode arm

Pour repasser en mode Azure Service Management, utilisez la commande suivante :

	azure config mode asm 

>[AZURE.NOTE] Le mode Gestionnaire de ressources et le mode Azure Service Management s'excluent mutuellement. En d'autres termes, les ressources créées dans un mode ne peuvent pas être gérées dans l'autre mode.

Pour plus d'informations sur l'utilisation du Gestionnaire de ressources avec l'interface xplat-cli, consultez la page [Utilisation de l'interface de ligne de commande interplateforme Azure avec le Gestionnaire de ressources][xplatarm].

###Utilisation des services en mode Azure Service Management

L'interface xplat-cli vous permet de gérer facilement des services Azure. Dans cet exemple, vous allez apprendre à l'utiliser pour gérer un site Web Azure.

1. Pour créer un site web Azure, utilisez la commande suivante. Remplacez **mywebsite** par un nom unique.

		azure site create mywebsite

	Vous êtes invité à indiquer la région dans laquelle sera créé le site web. Sélectionnez une région qui est géographiquement proche. Une fois l'exécution de la commande terminée, le site web est disponible à l'adresse http://mywebsite.azurewebsites.net (remplacez **mywebsite** par le nom que vous avez indiqué).

	> [AZURE.NOTE] Si vous utilisez Git pour le contrôle du code source du projet, vous pouvez spécifier le paramètre `--git` pour créer un référentiel Git sur Azure pour ce site web. Ce paramètre initialise également un référentiel Git dans l'annuaire à partir duquel la commande a été exécutée, si celui-ci n'existe pas déjà. Il crée par ailleurs un Git distant nommé __azure__, qui peut être utilisé pour transmettre les déploiements au site Web Azure avec la commande  `git push azure master`.

	> [AZURE.NOTE] Si vous recevez une erreur indiquant que  'site' n'est pas une commande Azure, l'interface xplat-cli est très probablement en mode groupe de ressources. Pour repasser en mode ressource, utilisez la commande  `azure config mode asm`.

2. Utilisez la commande suivante pour répertorier les sites Web pour votre abonnement :

		azure site list

	La liste doit contenir le site créé à l'étape précédente.

2. Pour arrêter le site Web, utilisez la commande suivante : Remplacez **mywebsite** par le nom du site.

		azure site stop mywebsite

	Une fois l'exécution de la commande terminée, vous pouvez actualiser le navigateur pour vérifier que le site a été arrêté.

3. Pour démarrer le site Web, utilisez la commande suivante : Remplacez **mywebsite** par le nom du site.

		azure site start mywebsite

	Une fois l'exécution de la commande terminée, vous pouvez actualiser le navigateur pour vérifier que le site a été démarré.

4. Pour supprimer le site Web, utilisez la commande suivante : Remplacez **mywebsite** par le nom du site.

		azure site delete mywebsite

	Une fois l'exécution de la commande terminée, utilisez la commande  `azure site list` pour vérifier que le site web n'existe plus.

<h2><a id="script"></a>Création de scripts dans l'interface de ligne de commande interplateforme Azure</h2>

Avec l'interface xplat-cli, vous pouvez émettre des commandes manuellement, mais également créer des flux de travail d'automatisation complexes en exploitant les fonctionnalités de votre interpréteur de ligne de commande ou des autres utilitaires de ligne de commande disponibles sur votre système. La commande suivante, par exemple, permet d'arrêter tous les sites Web Azure en cours d'exécution :

	azure site list | grep 'Running' | awk '{system("azure site stop "$2)}'

Cet exemple envoie une liste de sites Web à la commande  `grep`, qui examine chaque ligne en recherchant la chaîne  'Running'. Toutes les lignes avec une correspondance sont ensuite envoyées à la commande  `awk`, qui appelle  `azure site stop` et utilise la seconde colonne transmise (le nom du site en cours d'exécution) comme nom du site à arrêter.

Comme nous venons de le voir, vous avez la possibilité d'enchaîner des commandes, mais vous pouvez également créer des scripts plus élaborés à l'aide des fonctions de création de scripts fournies par votre interpréteur de ligne de commande. Les différents interpréteurs de ligne de commande n'ont pas les mêmes fonctions de création de scripts ni la même syntaxe. Bash est probablement l'interpréteur de ligne de commande le plus largement utilisé pour les systèmes UNIX, y compris Linux et OS X.

Pour obtenir des informations sur la création de scripts avec Bash, consultez la page [Guide avancé sur la création de scripts Bash][advanced-bash].

Pour obtenir des informations plus générales sur la création de scripts pour des systèmes OS X ou Linux, consultez la page [Script Shell][script].

Pour obtenir des informations sur la création de scripts pour des systèmes Windows avec des fichiers de commandes, consultez la page [Référence de ligne de commande de A à Z][batch].

### Compréhension des résultats

Quand vous créez des scripts, vous devez souvent capturer la sortie d'une commande et soit la transmettre à une autre commande, soit exécuter une opération sur la sortie, par exemple, la recherche d'une valeur particulière. L'interface xplat-cli génère la sortie dans STDOUT et STDERR. Chaque ligne est précédée de la chaîne  `info:` for informational status messages, or `data:` for data returned about a service; however, you can instruct the xplat-cli to return more verbose information by using the `--verbose` ou le paramètre `-v`. Des informations complémentaires, précédées de la chaîne  `verbose:`., sont alors renvoyées.

Commande For example, the following output is returned from the `azure site list` :

	info:    Executing command site list
	+ Enumerating sites
	data:    Name           Status   Mode  Host names
	data:    -------------  -------  ----  -------------------------------
	data:    myawesomesite  Running  Free  myawesomesite.azurewebsites.net
	info:    site list command OK

Si le paramètre " --verbose " ou " -v " est spécifié, des informations similaires aux suivantes sont renvoyées :

	info:    Executing command site list
	verbose: Subscription ####################################
	verbose: Enumerating sites
	verbose: [
	verbose:     {
	verbose:         ComputeMode: 'Shared',
	verbose:         HostNameSslStates: {
	verbose:             HostNameSslState: [
	verbose:                 {
	verbose:                     IpBasedSslResult: {
	verbose:                         $: { i:nil: 'true' }
	verbose:                     },
	verbose:                     SslState: 'Disabled',
	verbose:                     ToUpdateIpBasedSsl: {
	verbose:                         $: { i:nil: 'true' }
	verbose:                     },
	verbose:                     VirtualIP: {
	verbose:                         $: { i:nil: 'true' }
	verbose:                     },
	verbose:                     Thumbprint: {
	verbose:                         $: { i:nil: 'true' }
	verbose:                     },
	verbose:                     ToUpdate: {
	verbose:                         $: { i:nil: 'true' }
	verbose:                     },
	verbose:                     Name: 'myawesomesite.azurewebsites.net'
	verbose:                 },
	...
	verbose:     }
	verbose: ]
	data:    Name           Status   Mode  Host names
	data:    -------------  -------  ----  -------------------------------
	data:    myawesomesite  Running  Free  myawesomesite.azurewebsites.net
	info:    site list command OK

Notez que vous pouvez utiliser le paramètre  `verbose:` information appears to be JSON formatted data. You can use the `--json` pour renvoyer les informations au format JSON si vous travaillez avec des utilitaires qui comprennent JSON de manière native, tels que [jsawk](https://github.com/micha/jsawk) ou [jq](http://stedolan.github.io/jq/). Par exemple :

	azure site list --json | jsawk -n 'out(this.Name)' | xargs -L 1 azure site delete -q 

La commande ci-dessus récupère une liste de sites Web au format JSON, puis utilise jsawk pour récupérer les noms des sites, et utilise finalement xargs pour exécuter une commande de suppression de site pour chaque site, en transmettant le nom du site comme paramètre.

>[AZURE.NOTE] Le paramètre `--json` bloque la génération d'informations d'état ou de données (les chaînes préfixées par le paramètre  `info:` and `data:`). For example, if the `--json` sont utilisées avec  `azure site create`), aucune sortie n'est renvoyée car cette commande ne retourne pas de données autres que `info:`.

###Traitement des erreurs

Bien que l'interface xplat-cli ne consigne pas les informations sur les erreurs dans STDERR, des informations supplémentaires peuvent également être enregistrées dans un fichier **azure.err** du répertoire dans lequel le script a été exécuté. Si les informations sont enregistrées dans ce fichier, le texte suivant est écrit dans STDOUT :

	info:    Error information has been recorded to azure.err

###État de sortie

Certaines commandes de l'interface xplat-cli ne renvoient pas un état de sortie différent de zéro si des paramètres obligatoires sont manquants, mais demandent une entrée d'utilisateur. Par exemple, lorsque vous utilisez la commande  `azure site create` pour créer un site web, si aucun nom de site ou paramètre `--location` n'est spécifié, vous êtes invité à fournir ces valeurs.

Si vous écrivez un script qui dépend de l'état de sortie, vérifiez que les commandes xplat-cli utilisées ne demandent pas d'entrée utilisateur.

<h2><a id="additional-resources"></a>Ressources supplémentaires</h2>

* Pour plus d'informations sur l'interface xplat-cli, pour télécharger un code source, signaler des problèmes ou contribuer au projet, consultez la page [Référentiel GitHub pour l'interface de ligne de commande interplateforme Azure](https://github.com/WindowsAzure/azure-sdk-tools-xplat).

* Si vous rencontrez des problèmes lors de l'utilisation de l'interface xplat-cli ou d'Azure, consultez les [forums Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home).

* Pour plus d'informations sur Azure, consultez le site [http://azure.microsoft.com/fr-fr/](http://azure.microsoft.com).




[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/?linkid=9828653&clcid=0x409
[authandsub]: http://msdn.microsoft.com/library/windowsazure/hh531793.aspx#BKMK_AccountVCert

[Site web Azure]: ../media/freetrial.png
[sélectionner une fonctionnalité préliminaire]: ../media/antares-iaas-preview-02.png
[sélectionner l'abonnement]: ../media/antares-iaas-preview-03.png
[free-trial]: http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A7171371E
[advanced-bash]: http://tldp.org/LDP/abs/html/
[script]: http://en.wikipedia.org/wiki/Shell_script
[batch]: http://technet.microsoft.com/library/bb490890.aspx
[xplatarm]: /documentation/articles/xplat-cli-azure-resource-manager/
[portal]: https://manage.windowsazure.com
[signuporg]: http://www.windowsazure.com/documentation/articles/sign-up-organization/

<!--HONumber=47-->
