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
	ms.date="03/23/2015"
	ms.author="mingzhan"/>


# Mise à jour d’Azure Linux Agent vers la dernière version à partir de Github

Pour mettre à jour votre [Azure Linux Agent](https://github.com/Azure/WALinuxAgent), vous devez déjà disposer

1. d’une machine virtuelle Linux en cours d’exécution dans Azure
2. Vous êtes connecté à cette machine virtuelle Linux à l’aide de SSH

> [AZURE.NOTE]Si vous exécutez cette tâche à partir d’un ordinateur Windows, vous pouvez utiliser Putty pour vous connecter au moyen du protocole SSH à votre machine Linux. Pour plus d’informations, consultez [Connexion à une machine virtuelle exécutant Linux](virtual-machines-linux-how-to-log-on.md).

Les versions Linux approuvées dans Azure ont placé le package Azure Linux Agent dans leurs référentiels, c’est pourquoi il convient de vérifier et de commencer par installer la dernière version à partir de ce référentiel de version si possible.

Pour Ubuntu, il suffit de taper :
     
    #sudo apt-get install waagent

Sur CentOS et Oracle Linux, tapez :

    #sudo yum install waagent

Généralement, c’est tout ce qu’il vous est demandé de faire. Toutefois, si pour une raison quelconque vous devez l’installer directement à partir de https://github.com, procédez comme suit.


## Installer wget

Connectez-vous à votre machine virtuelle à l’aide du protocole SSH.

Installez wget \(certaines versions ne l’installent pas par défaut, telles que Redhat, CentOS, et Oracle Linux versions 6.4 et 6.5\) en tapant `#sudo yum install wget` sur la ligne de commande.


## Télécharger la version la plus récente

Ouvrez [la version d’Azure Linux Agent dans Github](https://github.com/Azure/WALinuxAgent/releases) dans une page web et cherchez le dernier numéro de version, tel que : 2.0.12. \(Vous pouvez rechercher votre version actuelle en tapant `#waagent --version`.\)

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-[version]/waagent  

La ligne suivante utilise la version 2.0.12 comme exemple :

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.12/waagent  

## Rendre waagent exécutable

    #chmod +x waagent

## Copier le nouveau fichier exécutable dans/usr/sbin /
    
    #sudo cp waagent /usr/sbin

Pour CoreOS, procédez comme suit :

    #sudo cp waagent /usr/share/oem/bin/
 
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




<!--HONumber=52-->
