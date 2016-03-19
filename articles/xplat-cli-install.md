<properties
	pageTitle="Installation de l’interface de ligne de commande Azure | Microsoft Azure"
	description="Installation de l’interface de ligne de commande Azure pour Mac, Linux et Windows afin d’utiliser les services Azure"
	editor=""
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/08/2016"
	ms.author="danlep"/>

# Installer l’interface de ligne de commande Microsoft Azure

Installez rapidement l’interface de ligne de commande Azure (Azure CLI) pour bénéficier d’un ensemble de commandes shell open source permettant de créer et gérer les ressources dans Microsoft Azure. Utilisez un des packages de programme d’installation fournis pour installer cette interface sur votre système d’exploitation, installez l’interface CLI à l’aide de Node.js et **npm**, ou installez Azure CLI en tant que conteneur dans un hôte Docker. Pour obtenir davantage d’options générales et de contexte, consultez le référentiel du projet sur [GitHub](https://github.com/azure/azure-xplat-cli).


Une fois la CLI Azure installée, vous êtes à même de [vous connecter à l’aide de votre abonnement Azure](xplat-cli-connect.md) et d’exécuter les commandes **azure** depuis votre interface de ligne de commande (Bash, Terminal, invite de ligne de commande, etc.) pour travailler avec vos ressources Microsoft Azure.




## Utilisation d’un programme d’installation

Les packages d'installation suivants sont disponibles :

* [Programme d’installation Windows][windows-installer]

* [Programme d’installation OS X](http://go.microsoft.com/fwlink/?LinkId=252249)

* [Programme d’installation Linux][linux-installer]


## Installation et utilisation de Node.js et npm

Si Node.js est installé sur votre système, vous pouvez également utiliser la commande ci-après pour installer Azure CLI :

	npm install azure-cli -g

> [AZURE.NOTE] Sur les distributions Linux, vous devrez peut-être utiliser `sudo` pour parvenir à exécuter la commande __npm__.

### Installez Node.js et npm sur des distributions Linux utilisant la gestion des packages [dpkg](http://en.wikipedia.org/wiki/Dpkg)

La plus courante de ces distributions utilise soit l’[outil d’empaquetage avancé (apt)](http://en.wikipedia.org/wiki/Advanced_Packaging_Tool), soit d’autres outils basés sur le format de package `.deb`. Ubuntu et Debian en sont des exemples.

La plupart des plus récentes de ces distributions nécessitent l’installation de **nodejs-legacy** afin d’obtenir un outil **npm** adéquatement configuré pour installer Azure CLI. Le code ci-après illustre les commandes qui installent correctement **npm** sur Ubuntu 14.04.

	sudo apt-get install nodejs-legacy
	sudo apt-get install npm
	sudo npm install -g azure-cli

Certaines des distributions plus anciennes, comme Ubuntu 12.04, requièrent l’installation de la distribution binaire actuelle de Node.js. Le code ci-dessous montre comment effectuer cette opération en installant et en utilisant **curl**.

>[AZURE.NOTE] Ces commandes sont prises parmi les instructions d’installation disponibles [ici](https://github.com/joyent/node/wiki/installing-node.js-via-package-manager). Toutefois, quand vous utilisez **sudo** comme cible de canal, vous devez toujours vérifier les scripts que vous installez et vous assurer qu’ils font exactement ce que vous attendez avant de les exécuter par le biais de **sudo**. Qui dit possibilités étendues dit responsabilités accrues.

	sudo apt-get install curl
	curl -sL https://deb.nodesource.com/setup | sudo bash -
	sudo apt-get install -y nodejs
	sudo npm install -g azure-cli

### Installez Node.js et npm sur des distributions Linux utilisant la gestion de package [rpm](http://en.wikipedia.org/wiki/RPM_Package_Manager)

L’installation de Node.js sur des distributions RPM requiert l’activation du référentiel EPEL. Le code suivant illustre les méthodes conseillées pour l'installation sur CentOS 7. (Notez que dans la première ligne ci-dessous, le caractère « - » (tiret) est important !)

	su -
	yum update [enter]
	yum upgrade –y [enter]
	yum install epel-release [enter]
	yum install nodejs [enter]
	yum install npm [enter]
	npm install -g azure-cli  [enter]

### Installation de Node.js et de npm sur Windows et Mac OS X

Vous pouvez installer Node.js et npm sur Windows et sur OS X à l’aide des programmes d’installation accessibles depuis [Nodejs.org](https://nodejs.org/en/download/). Vous devrez peut-être redémarrer l’ordinateur pour terminer l’installation. Vérifiez sur node et npm ont été correctement installés en ouvrant une fenêtre Commande ou Terminal, et en effectuant une saisie

	npm -v

Si la version de npm installée s’affiche, vous pouvez poursuivre et installer l’interface Azure CLI avec :

	npm install -g azure-cli

À la fin de l'installation, des informations similaires aux suivantes s'affichent normalement :

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

>[AZURE.NOTE] Pour les systèmes Linux, vous pouvez également installer l’interface Azure CLI en la générant à partir de la [source](http://go.microsoft.com/fwlink/?linkid=253472). Pour plus d’informations sur la génération de l’interface à partir de la source, voir le fichier INSTALL inclus dans l’archive.

## Utiliser un conteneur Docker

Dans un hôte de Docker, exécutez la commande :

```
docker run -it microsoft/azure-cli
```

## Exécution des commandes Azure CLI
Une fois l’interface Azure CLI installée, vous pouvez exécuter la commande **azure** depuis l’interface de ligne de commande utilisateur (Bash, Terminal, invite de ligne de commande, etc.) Par exemple, pour exécuter la commande d’aide, saisissez ce qui suit :

```
azure help
```

Pour afficher la version de l’interface CLI que vous avez installée, saisissez la commande suivante :

```
azure --version
```

Vous avez terminé l’installation. Pour accéder à toutes les commandes CLI et travailler avec vos propres ressources, [connectez-vous à votre abonnement Azure à partir de l’interface CLI Azure](xplat-cli-connect.md).

## Mise à jour de l’interface CLI

Microsoft publie fréquemment des versions mises à jour de l’interface CLI Azure. Réinstallez l’interface CLI à l’aide du programme d’installation de votre système d’exploitation ou, si Node.js et npm sont installés, procédez à la mise à jour en saisissant ce qui suit (dans les distributions Linux, vous devrez peut-être utiliser **sudo**) :

```
npm update -g azure-cli
```

## Ressources supplémentaires

* [Utilisation de l’interface de ligne de commande Azure (CLI) avec Azure Resource Manager (ARM)][cliarm]

* [Utilisation de l’interface de ligne de commande Azure avec la gestion des services Azure][cliasm]

* Pour plus d'informations sur l'interface de ligne de commande Azure, télécharger un code source, signaler des problèmes ou contribuer au projet, voir [Référentiel GitHub pour l'interface de ligne de commande Azure](https://github.com/azure/azure-xplat-cli) (en anglais).

* Si vous avez des questions sur l’utilisation de l’interface CLI Azure, ou de Microsoft Azure, consultez les [forums Microsoft Azure](https://social.msdn.microsoft.com/Forums/fr-FR/home?forum=azurescripting).



[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/?linkid=9828653&clcid=0x409
[linux-installer]: http://go.microsoft.com/fwlink/?linkid=253472
[cliasm]: virtual-machines/virtual-machines-command-line-tools.md
[cliarm]: virtual-machines/xplat-cli-azure-resource-manager.md

<!---HONumber=AcomDC_0218_2016-->