<properties
	pageTitle="Installation de la pile LAMP sur une machine virtuelle Linux"
	description="Apprenez à installer la pile LAMP sur une machine virtuelle Linux dans Azure Vous pouvez l'installer sur Ubuntu ou sur CentOS."
	services="virtual-machines"
	documentationCenter=""
	authors="szarkos"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/08/2015"
	ms.author="szark"/>



#Installation de la pile LAMP sur une machine virtuelle Linux dans Azure

Une pile LAMP se compose des différents éléments suivants :

- **L**inux : système d'exploitation
- **A**pache : serveur Web
- **M**ySQL : serveur de base de données
- **P**HP : langage de programmation


##Installation sur Ubuntu

Les packages suivants doivent être installés :

- `apache2`
- `mysql-server`
- `php5`
- `php5-mysql`

Après avoir exécuté `apt-get update` pour mettre à jour la liste locale de packages, vous pouvez les installer avec une simple commande `apt-get install` :

	# sudo apt-get update
	# sudo apt-get install apache2 mysql-server php5 php5-mysql

Après avoir exécuté la commande ci-dessus, vous serez invité à installer ces packages et d'autres dépendances. Appuyez sur « y » puis « Entrée » pour continuer et suivez les indications des invites pour définir un mot de passe d'administration pour MySQL.

Cela installera les extensions PHP minimales requises pour utiliser PHP avec MySQL. Exécutez les commandes suivantes pour voir les autres extensions PHP disponibles sous forme de packages :

	# apt-cache search php5


##Installation sur CentOS et Oracle Linux

Les packages suivants doivent être installés :

- `httpd`
- `mysql`
- `mysql-server`
- `php`
- `php-mysql`

Vous pouvez installer ces packages avec une simple commande `yum install` :

	# sudo yum install httpd mysql mysql-server php php-mysql

Après avoir exécuté la commande ci-dessus, vous serez invité à installer ces packages et d'autres dépendances. Appuyez sur « y » puis « Entrée » pour continuer.

Cela installera les extensions PHP minimales requises pour utiliser PHP avec MySQL. Exécutez les commandes suivantes pour voir les autres extensions PHP disponibles sous forme de packages :

	# yum search php


## Installation sur SUSE Linux Enterprise Server

Les packages suivants doivent être installés :

- apache2
- mysql
- apache2-mod_php53
- php53-mysql

Vous pouvez installer ces packages avec une simple commande `zypper install` :

	# sudo zypper install apache2 mysql apache2-mod_php53 php53-mysql

Après avoir exécuté la commande ci-dessus, vous serez invité à installer ces packages et d'autres dépendances. Appuyez sur « y » puis « Entrée » pour continuer.

Cela installera les extensions PHP minimales requises pour utiliser PHP avec MySQL. Exécutez les commandes suivantes pour voir les autres extensions PHP disponibles sous forme de packages :

	# zypper search php


Configuration
----------

1. Configurez **Apache**

	- Exécutez la commande suivante pour vous assurer que le serveur Web Apache est lancé :

		- Ubuntu et SLES : `sudo service apache2 restart`

		- CentOS et Oracle : `sudo service httpd restart`

	- Par défaut, Apache écoute sur le port 80. Il est possible que vous ayez besoin d'un point de terminaison pour accéder à votre serveur Apache à distance. Pour plus d'informations, veuillez consulter la documentation sur la [configuration de points de terminaison](virtual-machines-set-up-endpoints.md).

	- Vous pouvez maintenant vérifier si Apache fonctionne et fournit du contenu. Dans votre navigateur, accédez à `http://[MYSERVICE].cloudapp.net`, où **[MYSERVICE]** est le nom du service cloud dans lequel se trouve votre machine virtuelle. Dans certaines distributions, cela fera apparaître une page Web par défaut qui affiche simplement « It works! » Dans d'autres distributions, vous pourrez voir une page Web plus complète contenant des liens vers de la documentation et du contenu supplémentaire pour la configuration du serveur Apache.

2. Configurez **MySQL**

	- Cette étape n’est pas nécessaire sur Ubuntu, qui vous invite à saisir un mot de passe `root` MySQL au cours de l’installation du package mysql-server.

	- Dans les autres distributions, définissez le mot de passe racine pour MySQL en exécutant la commande suivante :

			# mysqladmin -u root -p password yourpassword

	- Vous pouvez ensuite gérer MySQL à l’aide des utilitaires `mysql` ou `mysqladmin`.


##Pour aller plus loin

Supposons que vous souhaitiez automatiser cette procédure pour déployer les applications sur des machines virtuelles Linux distantes ? Pour ce faire, utilisez l’extension Linux CustomScript. Consultez la page [Déployer une application LAMP à l’aide de l’extension CustomScript Azure pour Linux](virtual-machines-linux-script-lamp.md).

Il existe de nombreuses autres ressources pour configurer une pile LAMP sur Ubuntu.

- [https://help.ubuntu.com/community/ApacheMySQLPHP](https://help.ubuntu.com/community/ApacheMySQLPHP)

<!---HONumber=58-->