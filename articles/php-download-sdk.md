<properties title="Download the Azure SDK for PHP" pageTitle="Download the Azure SDK for PHP" metaKeywords="" description="Learn how to download and install the Azure SDK for PHP." documentationCenter="PHP" services="" solutions="web" authors="" />

Téléchargement du Kit de développement logiciel (SDK) Azure pour PHP
====================================================================

Le Kit de développement logiciel (SDK) Azure pour PHP inclut des composants qui vous permettent de développer, de déployer et de gérer des applications PHP pour Azure. Il inclut plus précisément les éléments suivants :

-   **Bibliothèques clientes PHP pour Azure**. L'interface de ces bibliothèques de classes permet d'accéder aux fonctionnalités Azure, telles que les services de gestion des données et les services cloud.
-   **Outils en ligne de commande Azure pour Mac et Linux**. Ces outils en ligne de commande permettent de déployer et de gérer des services Azure, tels que Sites Web Azure et Azure Virtual Machines. Ces outils fonctionnent sur toutes les plateformes, dont Mac, Linux et Windows.
-   **Azure PowerShell (Windows uniquement)**. Cet ensemble de cmdlets PowerShell permet de déployer et de gérer les services Azure, tels que Cloud Services et Virtual Machines.
-   **Émulateurs Azure (Windows uniquement)**. Les émulateurs de stockage et de calcul sont des émulateurs locaux des services cloud et de gestion des données qui vous permettent de tester une application localement. Les émulateurs Azure fonctionnent uniquement sur Windows.

Les sections ci-dessous présentent les procédures de téléchargement et d'installation des composants décrits plus haut.

Les instructions de cette rubrique partent du principe que [PHP](http://www.php.net/manual/en/install.php) est installé.

> [WACOM.NOTE] 
> Vous devez posséder PHP 5.3 ou une version ultérieure pour utiliser les bibliothèques clientes PHP pour Azure.

Bibliothèques clientes PHP pour Azure
-------------------------------------

Les bibliothèques clientes PHP pour Azure fournissent une interface permettant d'accéder aux fonctionnalités Azure, telles que les services cloud et de gestion des données à partir d'un système d'exploitation. Vous pouvez les installer manuellement, ou via les gestionnaires de package Composer ou PEAR.

Pour plus d'informations sur l'utilisation des bibliothèques clientes PHP pour Azure, consultez les pages [Utilisation du service BLOB](http://go.microsoft.com/fwlink/?LinkId=252714), [Utilisation du service de Table](http://go.microsoft.com/fwlink/?LinkId=252715) et [Utilisation du service de File d'attente](http://go.microsoft.com/fwlink/?LinkId=252716).

### Installation via Composer

1.  [Installez Git](http://git-scm.com/book/en/Getting-Started-Installing-Git).

    > [WACOM.NOTE] 
    > Sur Windows, vous devez également ajouter l'exécutable Git à votre variable d'environnement PATH.

2.  Créez un fichier nommé **composer.json** à la racine de votre projet et ajoutez-y le code suivant :

         {
             "require": {
                 "microsoft/windowsazure": "*"
             },         
             "repositories": [
                 {
                     "type": "pear",
                     "url": "http://pear.php.net"
                 }
             ],
             "minimum-stability": "dev"
         }

3.  Téléchargez **[composer.phar](http://getcomposer.org/composer.phar)** à la racine du projet.

4.  Ouvrez une invite de commandes et exécutez cette commande à la racine du projet

         php composer.phar install

### Installation d'un package PEAR

Pour installer les bibliothèques clientes PHP pour Azure en tant que package PEAR, procédez comme suit :

1.  [Installez PEAR](http://pear.php.net/manual/en/installation.getting.php).
2.  Configurez le canal PEAR Azure :

         pear channel-discover pear.windowsazure.com

3.  Installez le package PEAR :

         pear install pear.windowsazure.com/WindowsAzure-0.3.1

À la fin de l'installation, vous pouvez référencer les bibliothèques de classes à partir de votre application.

### Installation manuelle

Pour télécharger et installer manuellement les bibliothèques clientes PHP pour Azure, procédez comme suit :

1.  Téléchargez une archive ZIP qui contient les bibliothèques de [GitHub](http://go.microsoft.com/fwlink/?LinkId=252719). Sinon, répliquez le répertoire et clonez-le sur votre ordinateur local. (La deuxième option requiert un compte GitHub et l’installation locale de Git.)

    > [WACOM.NOTE] 
    > Les bibliothèques clientes PHP pour Azure ont une dépendance sur les packages PEAR [HTTP\_Request2](http://pear.php.net/package/HTTP_Request2), [Mail\_mime](http://pear.php.net/package/Mail_mime) et [Mail\_mimeDecode](http://pear.php.net/package/Mail_mimeDecode). La méthode recommandée pour résoudre ces dépendances consiste à installer ces packages à l'aide du [Gestionnaire de package PEAR](http://pear.php.net/manual/en/installation.php).

2.  Copiez le répertoire `WindowsAzure` de l'archive téléchargée dans la structure de répertoires de votre application et référencez les classes à partir de votre application.

Azure PowerShell et émulateurs Azure
------------------------------------

Azure PowerShell est un ensemble de cmdlets PowerShell permettant de déployer et de gérer les services Azure, tels que Cloud Services et Virtual Machines. Les émulateurs de stockage Azure sont des émulateurs des services cloud et de gestion des données qui vous permettent de tester une application localement. Ces composants sont pris en charge uniquement par Windows.

Pour installer Azure PowerShell et les émulateurs Azure, il est recommandé d'utiliser [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/?LinkId=253447). Notez que vous pouvez également installer d'autres composants de développement, tels que PHP, SQL Server, les pilotes Microsoft pour SQL Server pour PHP et WebMatrix.

Pour plus d'informations sur l'utilisation d'Azure PowerShell, consultez la page [Utilisation d'Azure PowerShell](http://go.microsoft.com/fwlink/?LinkId=252718).

Outils en ligne de commande Azure pour Mac et Linux
---------------------------------------------------

L'ensemble d'outils en ligne de commande Azure pour Mac et Linux permet de déployer et de gérer les services Azure, tels que Sites Web Azure et Azure Virtual Machines. La liste suivante décrit comment installer les outils en fonction de votre système d'exploitation :

-   **Mac** : téléchargez le programme d'installation du Kit de développement logiciel (SDK) Azure ici : <http://go.microsoft.com/fwlink/?LinkId=252249>. Ouvrez le fichier .pkg téléchargé et suivez les étapes d'installation indiquées.

-   **Linux** : installez la dernière version de [Node.js](http://nodejs.org/) (voir [Install Node.js via Package Manager](https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager) (en anglais)), puis exécutez la commande suivante :

        npm install azure-cli -g

    > [WACOM.NOTE] 
    > Vous devrez peut-être exécuter cette commande avec des privilèges élevés : `sudo npm install azure-cli -g`

Pour plus d'informations sur l'utilisation des outils en ligne de commande Azure pour Mac et Linux, consultez la page [Utilisation des outils en ligne de commande Azure pour Mac et Linux](http://go.microsoft.com/fwlink/?LinkId=252717).

