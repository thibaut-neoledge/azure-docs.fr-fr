<properties
	pageTitle="Mise à jour d’Azure Linux Agent vers la dernière version à partir de Github"
	description="Découvrez comment mettre à jour Azure Linux Agent à partir de Github pour votre machine virtuelle Linux dans Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/16/2015"
	ms.author="mingzhan"/>


# Mise à jour d’Azure Linux Agent vers la dernière version à partir de Github

Pour mettre à jour votre [Azure Linux Agent](https://github.com/Azure/WALinuxAgent), vous devez déjà disposer

1. d’une machine virtuelle Linux en cours d’exécution dans Azure
2. Vous êtes connecté à cette machine virtuelle Linux à l’aide de SSH

> [AZURE.NOTE]Si vous exécutez cette tâche à partir d’un ordinateur Windows, vous pouvez utiliser Putty pour vous connecter au moyen du protocole SSH à votre machine Linux. Pour plus d’informations, consultez [Connexion à une machine virtuelle exécutant Linux](virtual-machines-linux-how-to-log-on.md).

Les versions Linux approuvées dans Azure ont placé le package Azure Linux Agent dans leurs référentiels, c’est pourquoi il convient de vérifier et de commencer par installer la dernière version à partir de ce référentiel de version si possible.

Pour Ubuntu, il suffit de taper :
     
    #sudo apt-get install walinuxagent

et sur CentOS, tapez :

    #sudo yum install waagent

Pour Oracle Linux, assurez-vous que le référentiel complémentaire est activé dans le fichier `/etc/yum.repo.d/public-yum-ol6.repo` ou `/etc/yum.repo.d/public-yum-ol7.repo`, puis tapez :

    #sudo yum install WALinuxAgent

Généralement, c’est tout ce qu’il vous est demandé de faire. Toutefois, si pour une raison quelconque vous devez l’installer directement à partir de https://github.com, procédez comme suit.


## Installer wget

Connectez-vous à votre machine virtuelle à l’aide du protocole SSH.

Installez wget (certaines versions ne l’installent pas par défaut, telles que Redhat, CentOS, et Oracle Linux versions 6.4 et 6.5) en tapant `#sudo yum install wget` sur la ligne de commande.


## Télécharger la version la plus récente

Ouvrez [la version d’Azure Linux Agent dans Github](https://github.com/Azure/WALinuxAgent/releases) dans une page Web et cherchez le dernier numéro de version. (Vous pouvez rechercher votre version actuelle en tapant `#waagent --version`.)

###Pour la version 2.0.x, tapez :

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-[version]/waagent  

   La ligne suivante utilise la version 2.0.14 comme exemple :

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.14/waagent  

###Pour la version 2.1.x (ou version ultérieure), tapez :
  
    #wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-[version].zip 
    #unzip WALinuxAgent-[version].zip
    #cd WALinuxAgent-[version]

   La ligne suivante utilise la version 2.1.0 comme exemple :

    #wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-2.1.0.zip
    #unzip WALinuxAgent-2.1.0.zip  
    #cd WALinuxAgent-2.1.0

##Installer Linux Agent

###Pour la version 2.0.x, utilisez :

 Rendre waagent exécutable

    #chmod +x waagent

 Copier le nouveau fichier exécutable dans/usr/sbin /
   
  Pour la plupart des versions Linux, utilisez
         
      #sudo cp waagent /usr/sbin

  Pour CoreOS, procédez comme suit :

    #sudo cp waagent /usr/share/oem/bin/
 
###Pour la version 2.1.x, utilisez :

Vous devez peut-être installer d’abord le package `setuptools`, disponible [ici](https://pypi.python.org/pypi/setuptools). Exécutez ensuite la commande suivante :

    #sudo python setup.py install

## Redémarrer le service de waagent

Pour la plupart des versions de linux :

    #sudo service waagent restart

Pour Ubantu, procédez comme suit :

    #sudo service walinuxagent restart

Pour CoreOS, procédez comme suit :

    #sudo systemctl restart waagent 

## Confirmer la version d’Azure Linux Agent
   
    #waagent -version

Pour CoreOS, la commande ci-dessus peut ne pas fonctionner.

Vous verrez que la version d’Azure Linux Agent a été mise à jour vers la nouvelle version.

Pour plus d’informations sur Azure Linux Agent, consultez le fichier [Lisezmoi d’Azure Linux Agent](https://github.com/Azure/WALinuxAgent).



 

<!---HONumber=August15_HO8-->