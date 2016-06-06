<properties
	pageTitle="Installation de l’interface de ligne de commande Azure | Microsoft Azure"
	description="Installation de l’interface de ligne de commande (CLI) Azure pour Mac, Linux et Windows afin d’utiliser les services Azure"
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
	ms.date="05/23/2016"
	ms.author="danlep"/>
    
# Installer l’interface de ligne de commande Microsoft Azure

> [AZURE.SELECTOR]
- [PowerShell](powershell-install-configure.md)
- [Interface de ligne de commande Azure](xplat-cli-install.md)

Installez rapidement l’interface de ligne de commande Azure (Azure CLI) pour bénéficier d’un ensemble de commandes shell open source permettant de créer et gérer les ressources dans Microsoft Azure. Plusieurs choix d’installation s’offrent à vous : installez à partir d’un package npm (nécessite Node.js et npm ), utilisez l’un des packages de programme d’installation fournis pour différents systèmes d’exploitation ou installez l’interface de ligne de commande Azure en tant que conteneur dans un hôte Docker. Pour obtenir davantage d’options générales et de contexte, consultez le référentiel du projet sur [GitHub](https://github.com/azure/azure-xplat-cli).


Une fois l’interface de ligne de commande Azure installée, vous êtes à même de [vous connecter à l’aide de votre abonnement Azure](xplat-cli-connect.md) et d’exécuter les commandes **azure** depuis votre interface de ligne de commande (Bash, Terminal, invite de ligne de commande, etc.) pour travailler avec vos ressources Azure.



## Installer un package npm

Pour installer l’interface CLI à partir d’un package npm, vous aurez besoin des derniers Node.js et npm installés sur votre système. Utilisez la commande suivante pour installer le package de l’interface de ligne de commande Azure. Sur les distributions Linux, vous devrez peut-être utiliser **sudo** pour parvenir à exécuter la commande __npm__.

	npm install azure-cli -g

> [AZURE.NOTE]Si vous avez besoin d’installer ou de mettre à jour Node.js et npm pour votre système d’exploitation, consultez la documentation sur [Nodejs.org](https://nodejs.org/en/download/package-manager/). Nous vous recommandons d’installer la dernière version de Node.js LTS (4.x). Si vous utilisez une version antérieure, vous pouvez obtenir des erreurs d’installation.

## Utilisation d’un programme d’installation

Les packages d’installation suivants sont également disponibles au téléchargement :


* [Programme d’installation OS X][mac-installer]

* [Programme d’installation Windows][windows-installer]

* [Fichier tar Linux][linux-installer] (nécessite Node.js et npm) - Exécutez `sudo npm install -g <path to downloaded tar file>` pour l’installer


## Utiliser un conteneur Docker

Dans un hôte de Docker, exécutez la commande :

```
docker run -it microsoft/azure-cli
```

## Exécution des commandes Azure CLI
Une fois l’interface de ligne de commande Azure installée, vous pouvez exécuter la commande **azure** depuis l’interface de ligne de commande utilisateur (Bash, Terminal, invite de ligne de commande, etc.) Par exemple, pour exécuter la commande d’aide, saisissez ce qui suit :

```
azure help
```
> [AZURE.NOTE]Sur certaines distributions Linux, vous pourriez recevoir une erreur, /usr/bin/env: ‘node’: No such file or directory. Cela est causé par les installations récentes de nodejs installées dans /usr/bin/nodejs. Pour corriger cette erreur, créez un lien symbolique vers /usr/bin/node en exécutant la commande suivante

```
sudo ln -s /usr/bin/nodejs /usr/bin/node
```

Pour afficher la version de l’interface CLI que vous avez installée, saisissez la commande suivante :

```
azure --version
```

Vous avez terminé l’installation. Pour accéder à toutes les commandes de l’interface de ligne de commande et travailler avec vos propres ressources, [connectez-vous à votre abonnement Azure à partir de l’interface de ligne de commande Azure](xplat-cli-connect.md).

>[AZURE.NOTE] Lorsque vous utilisez l’interface de ligne de commande Azure version 0.9.20 ou version ultérieure pour la première fois, vous verrez un message vous demandant si vous souhaitez autoriser Microsoft à recueillir des informations sur l’utilisation de l’interface de ligne de commande. La participation se fait sur la base du volontariat. Si vous choisissez de participer, vous pouvez arrêter à tout moment en exécutant `azure telemetry --disable`. Pour activer la participation, exécutez `azure telemetry --enable`.


## Mise à jour de l’interface CLI

Microsoft publie fréquemment des versions mises à jour de l’interface CLI Azure. Réinstallez l’interface de ligne de commande à l’aide du programme d’installation de votre système d’exploitation ou, si les dernières versions de Node.js et npm sont installées, procédez à la mise à jour en saisissant ce qui suit (dans les distributions Linux, vous devrez peut-être utiliser **sudo**).

```
npm update -g azure-cli
```

## Activer la saisie semi-automatique via la touche Tab

La saisie semi-automatique via la touche Tab des commandes CLI est prise en charge pour Mac et Linux.

Pour l’activer dans zsh, exécutez :

```
echo '. <(azure --completion)' >> .zshrc
```

Pour l’activer dans bash, exécutez :

```
azure --completion >> ~/azure.completion.sh
echo 'source ~/azure.completion.sh' >> ~/.bash_profile
```


## Étapes suivantes 

* [Se connecter à partir de l’interface de ligne de commande à votre abonnement Azure](xplat-cli-connect.md) pour créer et gérer des ressources Azure.

* Pour plus d'informations sur l'interface de ligne de commande Azure, télécharger un code source, signaler des problèmes ou contribuer au projet, voir [Référentiel GitHub pour l'interface de ligne de commande Azure](https://github.com/azure/azure-xplat-cli) (en anglais).

* Si vous avez des questions sur l’utilisation de l’interface de ligne de commande Azure ou sur l’utilisation d’Azure, consultez les [forums Azure](https://social.msdn.microsoft.com/Forums/fr-FR/home?forum=azurescripting).

* Pour les systèmes Linux, vous pouvez également installer l’interface Azure CLI en la générant à partir de la [source](http://aka.ms/linux-azure-cli). Pour plus d’informations sur la génération à partir de la source, consultez le fichier INSTALL inclus dans l’archive source.

[mac-installer]: http://aka.ms/mac-azure-cli
[windows-installer]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=windowsazurexplatcli&mode=new
[linux-installer]: http://aka.ms/linux-azure-cli
[cliasm]: virtual-machines-command-line-tools.md
[cliarm]: ./virtual-machines/azure-cli-arm-commands.md

<!---HONumber=AcomDC_0525_2016-->