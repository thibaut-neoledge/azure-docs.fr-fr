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
	ms.date="04/07/2016"
	ms.author="danlep"/>

# Installer l’interface de ligne de commande Microsoft Azure

Installez rapidement l’interface de ligne de commande Azure (Azure CLI) pour bénéficier d’un ensemble de commandes shell open source permettant de créer et gérer les ressources dans Microsoft Azure. Plusieurs choix d’installation s’offrent à vous : utilisez un des packages de programme d’installation fournis pour différents systèmes d’exploitation, installez à partir d’un package npm ou installez l’interface de ligne de commande Azure en tant que conteneur dans un hôte Docker. Pour obtenir davantage d’options générales et de contexte, consultez le référentiel du projet sur [GitHub](https://github.com/azure/azure-xplat-cli).


Une fois l’interface de ligne de commande Azure installée, vous êtes à même de [vous connecter à l’aide de votre abonnement Azure](xplat-cli-connect.md) et d’exécuter les commandes **azure** depuis votre interface de ligne de commande (Bash, Terminal, invite de ligne de commande, etc.) pour travailler avec vos ressources Azure.


## Utilisation d’un programme d’installation

Les packages d'installation suivants sont disponibles :

* [Programme d’installation Windows][windows-installer]

* [Programme d’installation OS X](http://go.microsoft.com/fwlink/?LinkId=252249)

* [Programme d’installation Linux][linux-installer]


## Installer un package npm

Si les versions les plus récentes de Node.js et npm sont installées sur votre système, exécutez la commande ci-après pour installer le package de l’interface de ligne de commande Azure : Sur les distributions Linux, vous devrez peut-être utiliser **sudo** pour parvenir à exécuter la commande __npm__.

	npm install azure-cli -g

> [AZURE.NOTE]Si vous avez besoin d’installer ou de mettre à jour Node.js et npm pour votre système d’exploitation, consultez la documentation sur [Nodejs.org](https://nodejs.org/en/download/package-manager/). Nous vous recommandons d’installer la dernière version de Node.js LTS (4.x). Si vous utilisez une version antérieure, vous pouvez obtenir des erreurs d’installation.


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

Pour afficher la version de l’interface CLI que vous avez installée, saisissez la commande suivante :

```
azure --version
```

Vous avez terminé l’installation. Pour accéder à toutes les commandes de l’interface de ligne de commande et travailler avec vos propres ressources, [connectez-vous à votre abonnement Azure à partir de l’interface de ligne de commande Azure](xplat-cli-connect.md).


## Mise à jour de l’interface CLI

Microsoft publie fréquemment des versions mises à jour de l’interface CLI Azure. Réinstallez l’interface de ligne de commande à l’aide du programme d’installation de votre système d’exploitation ou, si les dernières versions de Node.js et npm sont installées, procédez à la mise à jour en saisissant ce qui suit (dans les distributions Linux, vous devrez peut-être utiliser **sudo**).

```
npm update -g azure-cli
```

## Étapes suivantes 

* [Se connecter à partir de l’interface de ligne de commande à votre abonnement Azure](xplat-cli-connect.md) pour créer et gérer des ressources Azure.

* Pour plus d'informations sur l'interface de ligne de commande Azure, télécharger un code source, signaler des problèmes ou contribuer au projet, voir [Référentiel GitHub pour l'interface de ligne de commande Azure](https://github.com/azure/azure-xplat-cli) (en anglais).

* Si vous avez des questions sur l’utilisation de l’interface de ligne de commande Azure ou d’Azure, consultez les [forums Microsoft Azure](https://social.msdn.microsoft.com/Forums/fr-FR/home?forum=azurescripting).

* Pour les systèmes Linux, vous pouvez également installer l’interface Azure CLI en la générant à partir de la [source](http://aka.ms/linux-azure-cli). Pour plus d’informations sur la génération à partir de la source, consultez le fichier INSTALL inclus dans l’archive source.

[mac-installer]: http://aka.ms/mac-azure-cli
[windows-installer]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=windowsazurexplatcli&mode=new
[linux-installer]: http://aka.ms/linux-azure-cli
[cliasm]: virtual-machines-command-line-tools.md
[cliarm]: ./virtual-machines/azure-cli-arm-commands.md

<!---HONumber=AcomDC_0420_2016-->