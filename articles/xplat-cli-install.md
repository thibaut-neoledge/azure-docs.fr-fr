<properties
	pageTitle="Installer l’interface de ligne de commande Azure pour Mac, Linux et Windows"
	description="Installer l’interface de ligne de commande Azure pour Mac, Linux et Windows afin d’utiliser les services Azure"
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services=""/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/02/2015"
	ms.author="danlep"/>

# Installer l’interface de ligne de commande Azure

Ce document décrit la procédure d’installation de l’interface de ligne de commande Azure (Azure CLI). Azure CLI offre un ensemble de commandes shell open source permettant de gérer les ressources sur Microsoft Azure.

> [AZURE.NOTE]Si vous avez déjà installé l’interface Azure CLI, connectez-la à vos ressources Azure. Pour plus d’informations, voir l’article [Connexion à votre abonnement Azure](xplat-cli-connect.md#configure).

L’interface Azure CLI est écrite en JavaScript et nécessite [Node.js](https://nodejs.org). Elle est implémentée à l’aide du [Kit de développement logiciel (SDK) Azure pour Node](https://github.com/azure/azure-sdk-for-node) et publiée sous licence Apache 2.0. Le référentiel du projet se trouve à la page [https://github.com/azure/azure-xplat-cli](https://github.com/azure/azure-xplat-cli).

<a id="install"></a>
## Installation de l’interface de ligne de commande Azure

Vous disposez de plusieurs méthodes pour installer Azure CLI.

1. Utilisation d'un programme d'installation
2. Installation de Node.js et de npm, puis utilisation de la commande **npm install**
3. Exécution d’Azure CLI sous la forme d’un conteneur Docker

Après avoir installé l’interface Azure CLI, vous pourrez utiliser la commande **azure** à partir de votre interface de ligne de commande (interpréteur de commandes, terminal, invite de commandes) pour accéder aux commandes associées.

## Utilisation d'un programme d'installation

Les packages d'installation suivants sont disponibles :

* [Programme d’installation Windows][windows-installer]

* [Programme d’installation OS X](http://go.microsoft.com/fwlink/?LinkId=252249)

* [Programme d’installation Linux][linux-installer]


## Installation et utilisation de Node.js et de npm

Si Node.js est installé sur votre système, utilisez la commande ci-après pour installer Azure CLI :

	npm install azure-cli -g

> [AZURE.NOTE]Sur les distributions Linux, vous devrez peut-être utiliser `sudo` pour parvenir à exécuter la commande __npm__.

### Installation de node.js et de npm sur des distributions Linux utilisant la gestion des packages [dpkg](http://en.wikipedia.org/wiki/Dpkg)
La plus courante de ces distributions utilise soit l’[outil d’empaquetage avancé (apt)](http://en.wikipedia.org/wiki/Advanced_Packaging_Tool), soit d’autres outils basés sur le format de package `.deb`. Ubuntu et Debian en sont des exemples.

La plupart des plus récentes de ces distributions nécessitent l’installation de **nodejs-legacy** afin d’obtenir un outil **npm** adéquatement configuré pour installer Azure CLI. Le code ci-après illustre les commandes qui installent correctement **npm** sur Ubuntu 14.04.

	sudo apt-get install nodejs-legacy
	sudo apt-get install npm
	sudo npm install -g azure-cli

Certaines des distributions plus anciennes, comme Ubuntu 12.04, requièrent l'installation de la distribution binaire actuelle de node.js. Le code ci-dessous montre comment effectuer cette opération en installant et en utilisant **curl**.

>[AZURE.NOTE]Ces commandes proviennent des instructions d’installation Joyent disponibles [ici](https://github.com/joyent/node/wiki/installing-node.js-via-package-manager). Toutefois, quand vous utilisez **sudo** comme cible de canal, vous devez toujours vérifier les scripts que vous installez et vous assurer qu’ils font exactement ce que vous attendez avant de les exécuter par le biais de **sudo**. Qui dit possibilités étendues dit responsabilités accrues.

	sudo apt-get install curl
	curl -sL https://deb.nodesource.com/setup | sudo bash -
	sudo apt-get install -y nodejs
	sudo npm install -g azure-cli

### Installation de node.js et de npm sur des distributions Linux utilisant la gestion des packages [rpm](http://en.wikipedia.org/wiki/RPM_Package_Manager)

L'installation de node.js sur des distributions RPM requiert l'activation du référentiel EPEL. Le code suivant illustre les méthodes conseillées pour l'installation sur CentOS 7. (Notez que dans la première ligne ci-dessous, le caractère « - » (tiret) est important !)

	su -
	yum update [enter]
	yum upgrade –y [enter]
	yum install epel-release [enter]
	yum install nodejs [enter]
	yum install npm [enter]
	npm install -g azure-cli  [enter]

### Installation de node.js et de npm sur Windows et sur Mac OS X

Vous pouvez installer node.js et npm sur Windows et sur OS X à l’aide des programmes d’installation accessibles à partir de [Nodejs.org](https://nodejs.org/download/). Vous devrez peut-être redémarrer l’ordinateur pour terminer l’installation. Vérifiez que node et npm ont été correctement installés en ouvrant l’invite de commandes et en tapant :

	npm -v

Si la version de npm installée s’affiche, vous pouvez poursuivre et installer l’interface Azure CLI avec :

	npm install -g azure-cli

Après avoir installé l’interface de ligne de commande Azure, vous pourrez utiliser la commande **azure** à partir de votre interface de ligne de commande pour accéder aux commandes associées. À la fin de l'installation, des informations similaires aux suivantes s'affichent normalement :

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

>[AZURE.NOTE]Pour les systèmes Linux, vous pouvez également installer l’interface Azure CLI en la générant à partir de la [source](http://go.microsoft.com/fwlink/?linkid=253472&clcid=0x409). Pour plus d’informations sur la génération de l’interface à partir de la source, voir le fichier INSTALL inclus dans l’archive.

## Utilisation du conteneur Docker

Dans un hôte Docker, exécutez la commande suivante : ```
	docker run -it microsoft/azure-cli
```

## Exécution des commandes de l’interface de ligne de commande Azure

Après avoir installé l’interface Azure CLI, vous pourrez utiliser la commande **azure** à partir de votre interface de ligne de commande (interpréteur de commandes, terminal, cmd.exe, etc.) pour accéder aux commandes associées. Par exemple, pour exécuter la commande d’aide dans Windows, lancez une invite de commandes (cmd.exe) avec des privilèges d’administrateur : ```
	C:\> azure help
```

Vous avez terminé l’installation. Vous pouvez à présent [vous connecter à votre abonnement Azure à partir de l’interface Azure CLI](xplat-cli-connect.md) et commencer à utiliser les commandes **azure**.


<a id="additional-resources"></a>
## Ressources supplémentaires

* [Utilisation de l’interface de ligne de commande Azure avec les commandes de gestion des services (ou le mode ASM)][cliasm]

* [Utilisation de l’interface de ligne de commande Azure avec les commandes de gestion des ressources (ou le mode ARM)][cliarm]

* Pour en savoir plus sur l’interface de ligne de commande Azure, télécharger un code source, signaler des problèmes ou contribuer au projet, voir la page [Référentiel GitHub pour l’interface de ligne de commande Azure](https://github.com/azure/azure-xplat-cli) (en anglais).

* Si vous rencontrez des problèmes lors de l’utilisation d’Azure ou de l’interface de ligne de commande Azure, consultez les [forums Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home).

* Pour plus d’informations sur Azure, consultez le site [http://azure.microsoft.com/](http://azure.microsoft.com).




[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/?linkid=9828653&clcid=0x409
[linux-installer]: http://go.microsoft.com/fwlink/?linkid=253472
[cliasm]: virtual-machines/virtual-machines-command-line-tools.md
[cliarm]: virtual-machines/xplat-cli-azure-resource-manager.md

<!---HONumber=August15_HO6-->