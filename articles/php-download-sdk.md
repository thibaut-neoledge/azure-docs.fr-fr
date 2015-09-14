<properties
	pageTitle="Téléchargement du Kit de développement logiciel (SDK) Azure pour PHP"
	description="Découvrez comment télécharger et installer le Kit de développement logiciel (SDK) Azure pour PHP."
	documentationCenter="php"
	services=""
	authors="tfitzmac"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="PHP"
	ms.topic="article"
	ms.date="08/31/2015"
	ms.author="tomfitz"/>

#Téléchargement du Kit de développement logiciel (SDK) Azure pour PHP

## Vue d'ensemble

Le Kit de développement logiciel (SDK) Azure pour PHP inclut des composants qui vous permettent de développer, de déployer et de gérer des applications PHP pour Azure. Il inclut plus précisément les éléments suivants :

* **Bibliothèques clientes PHP pour Azure**. L'interface de ces bibliothèques de classes permet d'accéder aux fonctionnalités Azure, telles que les services de gestion des données et les services cloud.  
* **Interface de ligne de commande Azure pour Mac, Linux et Windows (Azure CLI)**. Cet ensemble de commandes permet de déployer et de gérer des services Azure, tels que Sites Web Azure et Azure Virtual Machines. L’interface de ligne de commande Azure fonctionne sur toutes les plateformes, dont Mac, Linux et Windows.
* **Azure PowerShell (Windows uniquement)**. Cet ensemble de cmdlets PowerShell permet de déployer et de gérer les services Azure, tels que Cloud Services et Virtual Machines.
* **Émulateurs Azure (Windows uniquement)**. Les émulateurs de stockage et de calcul sont des émulateurs locaux des services cloud et de gestion des données qui vous permettent de tester une application localement. Les émulateurs Azure fonctionnent uniquement sur Windows.

Les sections ci-dessous présentent les procédures de téléchargement et d'installation des composants décrits plus haut.

Les instructions de cette rubrique partent du principe que [PHP][install-php] est installé.

> [AZURE.NOTE]Vous devez disposer de PHP 5.3 ou d'une version ultérieure pour utiliser les bibliothèques clientes PHP pour Azure.

##Bibliothèques clientes PHP pour Azure

Les bibliothèques clientes PHP pour Azure fournissent une interface permettant d'accéder aux fonctionnalités Azure, telles que les services cloud et de gestion des données à partir d'un système d'exploitation. Vous pouvez les installer manuellement, ou via les gestionnaires de package Composer ou PEAR.

Pour plus d'informations sur l'utilisation des bibliothèques clientes PHP pour Azure, consultez les pages [Utilisation du service BLOB][blob-service], [Utilisation du service de Table][table-service] et [Utilisation du service de File d'attente][queue-service].

###Installation via Composer

1. [Installez Git][install-git].


	> [AZURE.NOTE]Sous Windows, vous devez aussi ajouter l’exécutable Git à votre variable d’environnement PATH.

2. Créez un fichier nommé **composer.json** à la racine de votre projet et ajoutez-y le code suivant :

        {
            "repositories": [
                {
                    "type": "pear",
                    "url": "http://pear.php.net"
                }
            ],
            "require": {
                "pear-pear.php.net/mail_mime" : "*",
                "pear-pear.php.net/http_request2" : "*",
                "pear-pear.php.net/mail_mimedecode" : "*",
                "microsoft/windowsazure": "*"
            }
        }

3. Téléchargez **[composer.phar][composer-phar]** à la racine du projet.

4. Ouvrez une invite de commandes et exécutez cette commande à la racine du projet :

		php composer.phar install

###Installation d'un package PEAR

Pour installer les bibliothèques clientes PHP pour Azure en tant que package PEAR, procédez comme suit :

1. [Installez PEAR][install-pear].
2. Configurez le canal PEAR Azure :

		pear channel-discover pear.windowsazure.com
3. Installez le package PEAR :

		pear install pear.windowsazure.com/WindowsAzure-0.4.0

À la fin de l'installation, vous pouvez référencer les bibliothèques de classes à partir de votre application.

###Installation manuelle

Pour télécharger et installer manuellement les bibliothèques clientes PHP pour Azure, procédez comme suit :

1. Téléchargez une archive ZIP qui contient les bibliothèques de [GitHub][php-sdk-github]. Sinon, répliquez le répertoire et clonez-le sur votre ordinateur local. (La deuxième option requiert un compte GitHub et l’installation locale de Git.)

	> [AZURE.NOTE]Les bibliothèques clientes PHP pour Azure ont une dépendance sur les packages PEAR [HTTP\_Request2](http://pear.php.net/package/HTTP_Request2), [Mail\_mime](http://pear.php.net/package/Mail_mime) et [Mail\_mimeDecode](http://pear.php.net/package/Mail_mimeDecode). La méthode recommandée pour résoudre ces dépendances consiste à installer ces packages à l'aide du [Gestionnaire de package PEAR](http://pear.php.net/manual/en/installation.php).

2. Copiez le répertoire `WindowsAzure` de l’archive téléchargée dans la structure de répertoires de votre application et référencez les classes à partir de votre application.

##Azure PowerShell et émulateurs Azure

Azure PowerShell est un ensemble de cmdlets PowerShell permettant de déployer et de gérer les services Azure, tels que Cloud Services et Virtual Machines. Les émulateurs de stockage Azure sont des émulateurs des services cloud et de gestion des données qui vous permettent de tester une application localement. Ces composants sont pris en charge uniquement par Windows.

Pour installer Azure PowerShell et les émulateurs Azure, il est recommandé d'utiliser [Microsoft Web Platform Installer][download-wpi]. Notez que vous pouvez également installer d'autres composants de développement, tels que PHP, SQL Server, les pilotes Microsoft pour SQL Server pour PHP et WebMatrix.

Pour plus d'informations sur l'utilisation d'Azure PowerShell, consultez la page [Utilisation d'Azure PowerShell][powershell-tools].

##Interface de ligne de commande Azure

L’interface de ligne de commande Azure est un ensemble de commandes permettant de déployer et de gérer des services Azure, tels que Sites Web Azure et Azure Virtual Machines. Pour plus d'informations sur l'installation de l’interface de ligne de commande Azure, consultez [Installer l’interface de ligne de commande Azure](xplat-cli-install.md).




[install-php]: http://www.php.net/manual/en/install.php
[composer-github]: https://github.com/composer/composer
[composer-phar]: http://getcomposer.org/composer.phar
[pear-net]: http://pear.php.net/
[http-request2-package]: http://pear.php.net/package/HTTP_Request2
[mail-mimedecode-package]: http://pear.php.net/package/Mail_mimeDecode
[mail-mime-package]: http://pear.php.net/package/Mail_mime
[install-pear]: http://pear.php.net/manual/en/installation.getting.php
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[download-wpi]: http://go.microsoft.com/fwlink/?LinkId=253447
[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[blob-service]: http://go.microsoft.com/fwlink/?LinkId=252714
[table-service]: http://go.microsoft.com/fwlink/?LinkId=252715
[queue-service]: http://go.microsoft.com/fwlink/?LinkId=252716
[azure cli]: http://go.microsoft.com/fwlink/?LinkId=252717
[powershell-tools]: http://go.microsoft.com/fwlink/?LinkId=252718
[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git

<!---HONumber=September15_HO1-->