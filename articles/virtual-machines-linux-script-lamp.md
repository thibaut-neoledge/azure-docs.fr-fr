<properties
	pageTitle="Déployer une application Linux à l'aide de l'extension CustomScript Azure"
	description="Apprenez à utiliser l'extension CustomScript Azure pour déployer des applications sur les machines virtuelles Linux."
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	services="virtual-machines"
	authors="gbowerman"/>

<tags
	ms.service="virtual-machines"
	ms.workload="multiple"
	ms.tgt_pltfrm="linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/23/2015"
	ms.author="guybo"/>

#Déployer une application LAMP à l’aide de l’extension CustomScript Azure pour Linux#

L'extension CustomScript Azure pour Linux vous permet de personnaliser vos machines virtuelles en exécutant du code arbitraire écrit dans n'importe quel langage de script pris en charge par la machine virtuelle (par exemple Python, Bash etc.). Cela fournit un moyen très souple pour automatiser le déploiement d'application sur plusieurs machines.

Vous pouvez déployer l’extension CustomScript à l’aide du portail Azure, de PowerShell ou de l’interface de ligne de commande multiplateforme Azure (xplat-cli).

Dans cet exemple, nous allons étudier le déploiement d’une application LAMP simple sur Ubuntu à l’aide de l’interface de ligne de commande Microsoft Azure.

## Composants requis

Pour cette procédure, créez deux machines virtuelles Azure exécutant Ubuntu 14.04. Nous les appellerons *script-vm* et *lamp-vm* ici. Utilisez des noms uniques quand vous effectuez cette opération. L'une servira à exécuter les commandes de l'interface de ligne de commande, tandis que l'autre accueillera l'application LAMP déployée.

Vous avez également besoin d'un compte Azure Storage et d'une clé pour accéder à celui-ci (vous pouvez l'obtenir à partir du portail Azure).

Si vous avez besoin d’aide pour créer des machines virtuelles Linux sur Azure, reportez-vous à [Création d’une machine virtuelle exécutant Linux](virtual-machines-linux-tutorial.md).

Bien que les commandes d'installation spécifiques soient conçues pour Ubuntu, vous pouvez adapter les étapes générales à toute distribution prise en charge.

La machine virtuelle *script-vm* doit disposer de l’interface de ligne de commande Microsoft Azure, ainsi que d’une connexion opérationnelle à Microsoft Azure. Pour obtenir de l’aide à ce sujet, reportez-vous à [Installation et configuration de l’interface de ligne de commande Microsoft Azure](xplat-cli.md).

## Téléchargement d'un script

Dans cet exemple, l'extension CustomScript exécute un script sur une machine virtuelle distante pour installer la pile LAMP et créer une page PHP. Pour accéder au script depuis n'importe où, nous allons le télécharger sous la forme d'un objet blob Azure.

**Le script**

Ce script installe une pile LAMP sur Ubuntu (y compris la configuration d'une installation sans assistance de MySQL), écrit un simple fichier PHP et démarre Apache :

	#!/bin/bash
	# set up a silent install of MySQL
	dbpass="mySQLPassw0rd"

	export DEBIAN_FRONTEND=noninteractive
	echo mysql-server-5.6 mysql-server/root_password password $dbpass | debconf-set-selections
	echo mysql-server-5.6 mysql-server/root_password_again password $dbpass | debconf-set-selections

	# install the LAMP stack
	apt-get -y install apache2 mysql-server php5 php5-mysql  

	# write some PHP
	echo <center><h1>My Demo App</h1><br/></center> > /var/www/html/phpinfo.php
	echo <\?php phpinfo()\; \?> > /var/www/html/phpinfo.php

	# restart Apache
	apachectl restart

**Télécharger**

Enregistrez le script dans un fichier texte, par exemple *lamp_install.sh*, puis téléchargez-le vers le stockage Azure. Vous pouvez le faire facilement avec l’interface de ligne de commande Microsoft Azure. L'exemple suivant télécharge le fichier dans un conteneur de stockage nommé « scripts ». Remarque : si le conteneur n’existe pas, vous devez d’abord le créer.

    azure storage blob upload -a <yourStorageAccountName> -k <yourStorageKey> --container scripts ./install_lamp.sh

Créez également un fichier JSON qui décrit comment télécharger le script à partir du stockage Azure. Enregistrez-le sous la forme *public_config.json* (en remplaçant « mystorage » par le nom de votre compte de stockage) :

    {"fileUris":["https://mystorage.blob.core.windows.net/scripts/install_lamp.sh"], "commandToExecute":"sh install_lamp.sh" }


## Déploiement de l'extension

Nous sommes maintenant prêts à déployer l’extension CustomScript Linux sur la machine virtuelle distante à l’aide de l’interface de ligne de commande Microsoft Azure :

    azure vm extension set -c "./public_config.json" lamp-vm CustomScriptForLinux Microsoft.OSTCExtensions 1.*

Cette commande téléchargera et exécutera le script *lamp_install.sh* sur la machine virtuelle appelée *lamp-vm*.

Étant donné que l'application inclut un serveur web, n'oubliez pas d'ouvrir un port d'écoute HTTP sur la machine virtuelle distante :

    azure vm endpoint create -n Apache -o tcp lamp-vm 80 80

## Surveillance et dépannage

Vous pouvez vérifier la progression de l'exécution du script personnalisé en examinant le fichier journal sur la machine virtuelle distante. Établissez une connexion SSH à *lamp-vm*, puis affichez la dernière partie du fichier journal :

    cd /var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.3.0.0/
    tail -f extension.log

Une fois que l’exécution de l’extension CustomScript est terminée, vous pouvez accéder à la page PHP que vous avez créée, en l’occurrence : *http://lamp-vm.cloudapp.net/phpinfo.php*.

## Ressources supplémentaires

Vous pouvez utiliser les mêmes étapes de base pour déployer des applications plus complexes. Dans cet exemple, le script d'installation a été enregistré en tant qu'objet blob public dans Azure Storage. Une option plus sécurisée consisterait à stocker le script d’installation sous la forme d’un objet blob sécurisé avec une [signature d’accès sécurisé](https://msdn.microsoft.com/library/azure/ee395415.aspx) (SAS).

Voici quelques ressources supplémentaires pour l’interface de ligne de commande Microsoft Azure, Linux et l’extension CustomScript :

[Automatiser les tâches de personnalisation de machine virtuelle Linux à l’aide de l’extension CustomScript](http://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/)

[Extensions Linux Azure (GitHub)](https://github.com/Azure/azure-linux-extensions)

[Linux et informatique open-source sur Microsoft Azure](virtual-machines-linux-opensource.md)

<!---HONumber=58-->