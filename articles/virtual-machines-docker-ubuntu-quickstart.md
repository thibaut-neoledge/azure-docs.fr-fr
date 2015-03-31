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
	ms.date="02/02/2015" 
	ms.author="rasquill"/>

<!--The next line, with one pound sign at the beginning, is the page title--> 
# Prise en main rapide de Docker dans Azure Marketplace 

Le moyen le plus rapide pour se familiariser avec [Docker] consiste à accéder à Azure Marketplace et à créer une machine virtuelle à l'aide du modèle d'image **Docker on Ubuntu Server**, créé par [Canonical] conjointement avec [MSOpenTech]. Ceci permet de créer une machine virtuelle du serveur Ubuntu et d'installer automatiquement l'[extension de machine virtuelle Docker](http://azure.microsoft.com/documentation/articles/virtual-machines-docker-vm-extension/), ainsi que le **dernier** moteur Docker pré-installé et exécuté sur Azure.  

Vous pouvez immédiatement vous connecter à la machine virtuelle via SSH et commencer à travailler avec Docker directement, sans procédure supplémentaire. 

> [AZURE.NOTE]La machine virtuelle créée par le modèle Azure Marketplace n'héberge pas l'API distante Docker pour la gestion par un client Docker distant. Pour activer le contrôle à distance de l'hôte Docker sur cette machine virtuelle, consultez la page [Exécution de Docker avec HTTPS](https://docs.docker.com/articles/https/) ou suivez les étapes de la rubrique [Utilisation de l'extension Docker VM avec le portail Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/) ou [Utilisation de l'extension Docker VM à partir de l'interface interplateforme Azure (xplat-cli)](http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/). Si vous vous sentez l'âme aventureuse, vous pouvez construire le [client Windows Docker](https://github.com/ahmetalpbalkan/Docker.DotNet) par le biais de Github puis essayer cette méthode (ou simplement le récupérer sur [nuget](https://www.nuget.org/packages/Docker.DotNet/)). 

Dans cette rubrique :

- [Connexion au portail]
- [Création d'une machine virtuelle avec l'image Docker fournie par Canonical et MSOpenTech]
- [Connexion via SSH]

## <a id='logon'></a>Connexion au portail

Cette partie est facile, à moins que vous ne disposiez pas d'un compte Azure. [Dans ce cas, vous pouvez facilement en obtenir un gratuitement](http://azure.microsoft.com/pricing/free-trial/).

## <a id='createvm'></a>Création d'une machine virtuelle avec l'image Docker fournie par Canonical et MSOpenTech

1. Maintenant que vous êtes connecté, cliquez sur **Nouveau** dans le coin inférieur gauche de l'écran pour créer une nouvelle image de machine virtuelle. Vous pouvez voir l'image appropriée immédiatement dans la bannière :

  ![Choose the Docker Ubuntu image in the banner](./media/virtual-machines-docker-ubuntu-quickstart/CreateNewDockerBanner.png)

2. Dans le cas contraire, recherchez-la dans la bibliothèque d'images : 

  ![Locate the image in the Image Gallery](./media/virtual-machines-docker-ubuntu-quickstart/DockerOnUbuntuServerMSOpenTech.png)

3. indiquez le nom d'utilisateur ou le mot de passe de l'instance, ou un fichier **.pem** pour permettre à SSH d'utiliser un certificat. (Le graphique ci-dessous indique comment définir une combinaison nom d'utilisateur/mot de passe.) Appuyez ensuite sur **Créer**, en bas de l'écran.

  ![Configure the vm instance](./media/virtual-machines-docker-ubuntu-quickstart/CreateVMDockerUbuntuPwd.png)

4. Attendez l'exécution du processus. Cela ne devrait pas mettre longtemps.

  ![Docker image running in portal](./media/virtual-machines-docker-ubuntu-quickstart/DockerUbuntuRunning.png)

## <a id='havingfun'></a>Connexion via SSH

La partie amusante commence maintenant. Vous pouvez vous connecter immédiatement à la machine virtuelle depuis SSH :

  ![Connecting with SSH](./media/virtual-machines-docker-ubuntu-quickstart/SSHToDockerUbuntu.png)

et commencer à émettre des commandes Docker, en vous rappelant que la configuration par défaut requiert **`sudo`** sur cette machine virtuelle Azure :

  ![Pulling images](./media/virtual-machines-docker-ubuntu-quickstart/DockerPullSmallImages.png)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes

L'idéal est de commencer en utilisant [Docker]. 

<!--Anchors-->
[Connexion au portail]: #logon
[Création d'une machine virtuelle avec l'image Docker fournie par Canonical et MSOpenTech]: #createvm
[Connexion via SSH]: #havingfun
[Étapes suivantes]: #next-steps


[Docker]: https://www.docker.com/
[BusyBox]: http://en.wikipedia.org/wiki/BusyBox
[Image initiale Docker]: https://docs.docker.com/articles/baseimages/#creating-a-simple-base-image-using-scratch
[Canonical]: http://www.canonical.com/
[MSOpenTech]: http://msopentech.com/


<!--HONumber=47-->
