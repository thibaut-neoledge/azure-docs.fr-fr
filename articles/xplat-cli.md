<properties
	pageTitle="L’interface de ligne de commande Azure pour Mac, Linux et Windows"
	description="Installez et configurez l’interface de ligne de commande Azure pour Mac, Linux et Windows afin de gérer les services Azure"
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
	ms.date="03/10/2015"
	ms.author="rasquill"/>

# Installer et configurer l’interface de ligne de commande Azure

> [AZURE.SELECTOR]
- [PowerShell](powershell-install-configure.md)
- [Azure CLI](xplat-cli.md)

La CLI Microsoft Azure offre un ensemble de commandes multiplateforme open source pour l’utilisation de la plateforme Microsoft Azure. Elle offre presque les mêmes fonctionnalités que le portail de gestion Azure, notamment la possibilité de gérer des sites Web, des machines virtuelles, des services mobiles, la base de données SQL et d'autres services fournis par la plateforme Azure.

L’interface de ligne de commande Azure est écrite en JavaScript et nécessite Node.js. Elle est implémentée à l'aide du Kit de développement logiciel (SDK) Azure pour Node.js et publiée sous licence Apache 2.0. Le référentiel du projet se trouve à la page [https://github.com/azure/azure-xplat-cli](https://github.com/azure/azure-xplat-cli).

Ce document décrit l’installation et la configuration de l’interface de ligne de commande Azure pour Mac, Linux et Windows, et explique comment l’utiliser pour exécuter les tâches de base avec la plateforme Azure.

<a id="install"></a>
## Installation de l’interface de ligne de commande Azure

Pour en savoir plus sur les étapes d’installation de l’interface de ligne de commande Azure, consultez la page [Installation de l’interface Azure CLI](xplat-cli-install.md).


<a id="configure"></a>
## Connexion à votre abonnement Azure

Certaines commandes fournies par l'interface de ligne de commande Azure fonctionnent sans abonnement Azure, mais la plupart exigent un abonnement. Pour en savoir plus sur la configuration de l’interface de ligne de commande Azure avec votre abonnement, consultez la section [Se connecter à un abonnement Azure à partir de l’interface Azure CLI](xplat-cli-connect.md).


<a id="use"></a>
## Utilisation de l’interface de ligne de commande Azure

L’interface de ligne de commande Azure est accessible à l’aide de la commande `azure`. Pour afficher la liste des commandes disponibles, utilisez la commande `azure` sans paramètres. Des informations d'aide similaires aux suivantes s'affichent normalement :

	info:             _    _____   _ ___ ___
	info:            /_\  |_  / | | | _ \ __|
	info:      _ ___/ _ __/ /| |_| |   / _|___ _ _
	info:    (___  /_/ _/___|___/|_|____| _____)
	info:       (_______ _ _)         _ ______ _)_ _
	info:              (______________ _ )   (___ _ _)
	info:
	info:    Microsoft Azure: Microsoft's Cloud Platform
	info:
	info:    Tool version 0.8.10
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

Les commandes du premier niveau répertoriées ci-dessus contiennent des commandes utilisées dans un domaine particulier d'Azure. Par exemple, la commande `azure account` contient des commandes en rapport avec votre abonnement Azure, comme les paramètres `download` et `import` utilisés préalablement. Pour en savoir plus sur les commandes et les options disponibles, consultez la section [Utilisation de Microsoft Azure pour Mac, Linux et Windows].

La plupart des commandes utilisent le format `azure <command> <operation> [parameters]` et exécutent une opération sur un service ou un objet tel que la configuration de votre compte. D’autres commandes proposent des sous-commandes et utilisent le format `azure <command> <subcommand> <operation> [parameters]`. Voici des exemples de commandes qui fonctionnent avec la configuration de votre compte :

* Pour afficher les abonnements que vous avez importés, utilisez :

		azure account list

* Si vous avez importé des abonnements, utilisez la commande suivante pour en définir un comme abonnement par défaut :

		azure account set <subscription>

Le paramètre `--help` ou `-h` peut être utilisé pour afficher l'aide relative à des commandes particulières. Le format `azure help [command] [options]` permet également de renvoyer les mêmes informations. Les commandes suivantes, par exemple, renvoient les mêmes informations :

	azure account set --help

	azure account set -h

	azure help account set

Si vous avez des doutes sur les paramètres exigés par une commande, reportez-vous à l'aide en utilisant `--help`, `-h` ou `azure help [command]`.

### Définition du mode de configuration

L'interface de ligne de commande Azure vous permet d'exécuter des opérations de gestion sur des _ressources_ individuelles, qui correspondent à des entités gérées par l'utilisateur, telles qu'un serveur de base de données, une base de données ou un site Web. Il s'agit du mode de fonctionnement par défaut de l'interface de ligne de commande Azure qui est désigné sous le nom d'**Azure Service Management**. Toutefois, dans le cas d'une solution complexe comportant plusieurs ressources, il est utile de pouvoir gérer la solution entière comme une seule et même unité.

Pour prendre en charge la gestion d'un groupe de ressources comme une seule unité logique, ou _groupe de ressources_, nous avons introduit une version préliminaire du **Gestionnaire de ressources** comme nouveau moyen de gestion des ressources Azure.

>[AZURE.NOTE]Le Gestionnaire de ressources en est actuellement au stade préliminaire et ne fournit pas des capacités de gestion du même niveau qu'Azure Service Management.

Pour prendre en charge la nouvelle solution Azure Resource Manager, l'interface de ligne de commande Azure vous permet de basculer entre ces « modes » de gestion avec la commande `azure config mode`.

L'interface de ligne de commande Azure utilise par défaut le mode Azure Service Management. Pour basculer en mode Gestionnaire de ressources, utilisez la commande suivante :

	azure config mode arm

Pour repasser en mode Azure Service Management, utilisez la commande suivante :

	azure config mode asm

>[AZURE.NOTE]Le mode Gestionnaire de ressources et le mode Azure Service Management s'excluent mutuellement. En d'autres termes, les ressources créées dans un mode ne peuvent pas être gérées dans l'autre mode.

Pour plus d’informations sur l’utilisation d’Azure Manager à l’aide de l’interface de ligne de commande Azure, consultez la section [Utilisation de l'interface de ligne de commande interplateforme Azure avec le Gestionnaire de ressources][cliarm].

### Utilisation des services en mode Azure Service Management

L'interface de ligne de commande Azure vous permet de gérer facilement des services Azure. Dans cet exemple, vous allez apprendre à l'utiliser pour gérer un site Web Azure.

1. Pour créer un site web Azure, utilisez la commande suivante. Remplacez **mywebsite** par un nom unique.

		azure site create mywebsite

	Vous êtes invité à indiquer la région dans laquelle sera créé le site Web. Sélectionnez une région qui est géographiquement proche. Une fois l’exécution de la commande terminée, le site Web est disponible à l’adresse http://mywebsite.azurewebsites.net (remplacez **mywebsite** par le nom que vous avez indiqué.)

	> [AZURE.NOTE]Si vous utilisez Git pour le contrôle de la source du projet, vous pouvez spécifier le paramètre `--git` pour créer un référentiel Git sur Azure pour ce site Web. Ce paramètre initialise également un référentiel Git dans l'annuaire à partir duquel la commande a été exécutée, si celui-ci n'existe pas déjà. Il crée par ailleurs un Git distant nommé __azure__, qui peut être utilisé pour transmettre les déploiements au site Web Azure avec la commande `git push azure master`.

	> [AZURE.NOTE]Si vous recevez une erreur indiquant que « site » n'est pas une commande Azure, l'interface de ligne de commande Azure est très probablement en mode groupe de ressources. Pour repasser en mode ressource, utilisez la commande `azure config mode asm`.

2. Utilisez la commande suivante pour répertorier les sites Web pour votre abonnement :

		azure site list

	La liste doit contenir le site créé à l'étape précédente.

2. Pour arrêter le site Web, utilisez la commande suivante : Remplacez **mywebsite** par le nom du site.

		azure site stop mywebsite

	Une fois l'exécution de la commande terminée, vous pouvez actualiser le navigateur pour vérifier que le site a été arrêté.

3. Pour démarrer le site Web, utilisez la commande suivante : Remplacez **mywebsite** par le nom du site.

		azure site start mywebsite

	Une fois l'exécution de la commande terminée, vous pouvez actualiser le navigateur pour vérifier que le site a été démarré.

4. Pour supprimer le site Web, utilisez la commande suivante : Remplacez **mywebsite** par le nom du site.

		azure site delete mywebsite

	Une fois l'exécution de la commande terminée, utilisez la commande `azure site list` pour vérifier que le site Web n'existe plus.

<a id="script"></a>
## Génération du script de l’interface de ligne de commande Azure pour Mac, Linux et Windows

Avec l'interface de ligne de commande Azure, vous pouvez émettre des commandes manuellement, mais également créer des flux de travail d'automatisation complexes en exploitant les fonctionnalités de votre interpréteur de ligne de commande ou des autres utilitaires de ligne de commande disponibles sur votre système. La commande suivante, par exemple, permet d'arrêter tous les sites Web Azure en cours d'exécution :

	azure site list | grep 'Running' | awk '{system("azure site stop "$2)}'

Cet exemple envoie une liste de sites Web à la commande `grep`, qui examine chaque ligne en recherchant la chaîne « Running ». Toutes les lignes avec une correspondance sont ensuite envoyées à la commande `awk`, qui appelle `azure site stop` et utilise la seconde colonne transmise (le nom du site en cours d'exécution) comme nom du site à arrêter.

Comme nous venons de le voir, vous avez la possibilité d'enchaîner des commandes, mais vous pouvez également créer des scripts plus élaborés à l'aide des fonctions de création de scripts fournies par votre interpréteur de ligne de commande. Les différents interpréteurs de ligne de commande n'ont pas les mêmes fonctions de création de scripts ni la même syntaxe. Bash est probablement l'interpréteur de ligne de commande le plus largement utilisé pour les systèmes UNIX, y compris Linux et OS X.

Pour obtenir des informations sur la création de scripts avec Bash, consultez la page [Guide avancé sur la création de scripts Bash][advanced-bash].

Pour obtenir des informations plus générales sur la création de scripts pour des systèmes OS X ou Linux, consultez la page [Script Shell][script].

Pour obtenir des informations sur la création de scripts pour des systèmes Windows avec des fichiers de commandes, consultez la page [Référence de ligne de commande de A à Z][batch].

### Compréhension des résultats

Quand vous créez des scripts, vous devez souvent capturer la sortie d'une commande et soit la transmettre à une autre commande, soit exécuter une opération sur la sortie, par exemple, la recherche d'une valeur particulière. L'interface de ligne de commande Azure génère la sortie dans STDOUT et STDERR. Chaque ligne est précédée de la chaîne `info:` pour les messages d'état informatifs ou `data:` pour les données renvoyées à propos d'un service. Vous pouvez cependant ordonner à l'interface de ligne de commande Azure de renvoyer des informations plus détaillées en utilisant le paramètre `--verbose` ou `-v`. Des informations complémentaires, précédées de la chaîne `verbose:`, sont alors renvoyées.

Par exemple, la sortie suivante est renvoyée par la commande `azure site list` :

	info:    Executing command site list
	+ Enumerating sites
	data:    Name           Status   Mode  Host names
	data:    -------------  -------  ----  -------------------------------
	data:    myawesomesite  Running  Free  myawesomesite.azurewebsites.net
	info:    site list command OK

Si le paramètre `--verbose` ou `-v` est spécifié, des informations similaires aux suivantes sont renvoyées :

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

Notez que les informations `verbose:` apparaissent comme des données au format JSON. Vous pouvez utiliser le paramètre `--json` pour renvoyer les informations au format JSON si vous travaillez avec des utilitaires qui comprennent JSON de manière native, tels que [jsawk](https://github.com/micha/jsawk) ou [jq](http://stedolan.github.io/jq/). Par exemple :

	azure site list --json | jsawk -n 'out(this.Name)' | xargs -L 1 azure site delete -q

La commande ci-dessus récupère une liste de sites Web au format JSON, puis utilise jsawk pour récupérer les noms des sites, et utilise finalement xargs pour exécuter une commande de suppression de site pour chaque site, en transmettant le nom du site comme paramètre.

>[AZURE.NOTE]Le paramètre `--json` bloque la génération des informations d'état ou de données (chaînes précédées de `info:` et `data:`). Par exemple, si le paramètre `--json` est utilisé avec `azure site create`, aucune sortie n'est renvoyée, car cette commande ne renvoie pas de données autres que `info:`.

### Traitement des erreurs

Bien que l'interface de ligne de commande Azure ne consigne pas les informations sur les erreurs dans STDERR, des informations supplémentaires peuvent également être enregistrées dans un fichier **azure.err** du répertoire dans lequel le script a été exécuté. Si les informations sont enregistrées dans ce fichier, le texte suivant est écrit dans STDOUT :

	info:    Error information has been recorded to azure.err

### État de sortie

Certaines commandes de l'interface de ligne de commande Azure ne renvoient pas un état de sortie différent de zéro si des paramètres obligatoires sont manquants, mais demandent une entrée d'utilisateur. Par exemple, lorsque vous utilisez la commande `azure site create` pour créer un site Web, si aucun nom de site ou paramètre `--location` n'est spécifié, vous êtes invité à fournir ces valeurs.

Si vous écrivez un script qui dépend de l'état de sortie, vérifiez que les commandes de l’interface de ligne de commande Azure utilisées ne demandent pas d'entrée utilisateur.

<a id="additional-resources"></a>

## Ressources supplémentaires

* [Liste des commandes détaillées de gestion des services][Using the Azure CLI]

* [Utilisation de l’interface de ligne de commande Azure pour Mac, Linux et Windows](cli-cli-azure-resource-manager)

* [Utilisation de l’interface de ligne de commande Azure avec Azure Resource Manager][cliarm]

* Pour en savoir plus sur l’interface de ligne de commande Azure, télécharger un code source, signaler des problèmes ou contribuer au projet, voir la page [Référentiel GitHub pour l’interface de ligne de commande Azure](https://github.com/azure/azure-xplat-cli) (en anglais).

* Si vous rencontrez des problèmes lors de l’utilisation d’Azure ou de l’interface de ligne de commande Azure, consultez les [forums Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home).

* Pour plus d’informations sur Azure, consultez le site [http://azure.microsoft.com/](http://azure.microsoft.com).




[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/?linkid=9828653&clcid=0x409
[authandsub]: http://msdn.microsoft.com/library/windowsazure/hh531793.aspx#BKMK_AccountVCert

[Azure Web Site]: ../media/freetrial.png
[select a preview feature]: ../media/antares-iaas-preview-02.png
[select subscription]: ../media/antares-iaas-preview-03.png
[free-trial]: http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A7171371E
[advanced-bash]: http://tldp.org/LDP/abs/html/
[script]: http://en.wikipedia.org/wiki/Shell_script
[batch]: http://technet.microsoft.com/library/bb490890.aspx
[cliarm]: xplat-cli-azure-resource-manager.md
[portal]: https://manage.windowsazure.com
[signuporg]: http://www.windowsazure.com/documentation/articles/sign-up-organization/
[Using the Azure CLI]: virtual-machines-command-line-tools.md

<!---HONumber=July15_HO4-->