<properties
	pageTitle="Utilisation rapide de Docker avec une image de machine virtuelle Ubuntu-Docker"
	description="Cette section explique et démontre comment utiliser en quelques minutes Docker sur un serveur Ubuntu, directement depuis la galerie d'images Azure"
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure"
	ms.date="10/04/2015"
	ms.author="rasquill"/>

# Prise en main rapide de Docker dans Azure Marketplace

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modèle Resource Manager
 

Le moyen le plus rapide pour se familiariser avec [Docker] consiste à accéder à Microsoft Azure Marketplace et à créer une machine virtuelle à l’aide du modèle d’image **Docker on Ubuntu Server**, créé par [Canonical] conjointement avec [MSOpenTech]. Ceci permet de créer une machine virtuelle du serveur Ubuntu et d’installer automatiquement l’[extension de machine virtuelle Docker](virtual-machines-docker-vm-extension.md), ainsi que le **dernier** moteur Docker pré-installé et exécuté sur Microsoft Azure.

Vous pouvez immédiatement vous connecter à la machine virtuelle via SSH et commencer à travailler avec Docker directement, sans procédure supplémentaire.

> [AZURE.NOTE]La machine virtuelle créée par le modèle Azure Marketplace n'héberge pas l'API distante Docker pour la gestion par un client Docker distant. Pour activer le contrôle à distance de l’hôte Docker sur cette machine virtuelle, consultez la page [Exécution de Docker avec HTTPS](https://docs.docker.com/articles/https/) ou suivez les étapes de la rubrique [Utilisation de l’extension Docker VM avec le portail Azure](virtual-machines-docker-with-portal.md) ou [Utilisation de l’extension Docker VM à partir de l’interface interplateforme Azure (xplat-cli)](virtual-machines-docker-with-xplat-cli-install.md). <!-- --> Si vous souhaitez automatiser votre machine virtuelle Azure Docker à partir de Windows, vous pouvez [installer la boîte à outils Docker](https://docs.docker.com/installation/windows/) ou obtenir Docker.exe à partir du site [Chocolatey](https://chocolatey.org/packages/docker).

## Connexion au portail

Cette partie est facile, à moins que vous ne disposiez pas d'un compte Azure. [Dans ce cas, vous pouvez facilement en obtenir un gratuitement.](http://azure.microsoft.com/pricing/free-trial/)

## Création d’une machine virtuelle avec l’image Docker fournie par Canonical et MSOpenTech

1. Maintenant que vous êtes connecté, cliquez sur **Nouveau** dans le coin inférieur gauche de l’écran pour créer une nouvelle image de machine virtuelle. Vous pouvez voir l'image appropriée immédiatement dans la bannière :

> ![Choisir l’image Docker Ubuntu dans la bannière](./media/virtual-machines-docker-ubuntu-quickstart/CreateNewDockerBanner.png)

2. Dans le cas contraire, recherchez-la dans la bibliothèque d'images :

> ![Rechercher l’image dans la galerie d’images](./media/virtual-machines-docker-ubuntu-quickstart/DockerOnUbuntuServerMSOpenTech.png)

3. Indiquez le nom d’utilisateur ou le mot de passe de l’instance, ou le contenu d’un fichier **.pub** (format ssh-rsa) pour permettre à SSH d’utiliser un certificat. (Le graphique ci-dessous indique comment définir une combinaison nom d'utilisateur/mot de passe.) Appuyez ensuite sur **Créer**, en bas de l’écran.

> ![Configurer l’instance de machine virtuelle](./media/virtual-machines-docker-ubuntu-quickstart/CreateVMDockerUbuntuPwd.png)

4. Attendez l'exécution du processus. Cela ne devrait pas mettre longtemps.

> ![Image docker en cours d’exécution dans le portail](./media/virtual-machines-docker-ubuntu-quickstart/DockerUbuntuRunning.png)

## Connexion via SSH

La partie amusante commence maintenant. Vous pouvez vous connecter immédiatement à la machine virtuelle depuis SSH :

> ![Connexion avec SSH](./media/virtual-machines-docker-ubuntu-quickstart/SSHToDockerUbuntu.png)

et commencer à émettre des commandes Docker, en vous rappelant que la configuration par défaut requiert **`sudo`** sur cette machine virtuelle Azure :

> ![Extraction d’images](./media/virtual-machines-docker-ubuntu-quickstart/DockerPullSmallImages.png)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes

L’idéal est de commencer en utilisant [Docker].

<!--Anchors-->
[Log on to the Portal]: #logon
[Create a VM with the Docker Image from Canonical and MSOpenTech]: #createvm
[Connect with SSH and Have Fun]: #havingfun
[Next steps]: #next-steps


[Docker]: https://www.docker.com/
[BusyBox]: http://en.wikipedia.org/wiki/BusyBox
[Docker scratch image]: https://docs.docker.com/articles/baseimages/#creating-a-simple-base-image-using-scratch
[Canonical]: http://www.canonical.com/
[MSOpenTech]: http://msopentech.com/
 

<!---HONumber=Oct15_HO3-->