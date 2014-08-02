<properties title="How to Configure PHP in Azure Web Sites" pageTitle="How to Configure PHP in Azure Web Sites" metaKeywords="Azure, Azure Web Sites, configuration, PHP" description="Learn how to configure the default PHP installation or add a custom PHP installation in Azure Web Sites." services="Web Sites" documentationCenter="PHP" authors="" />

Configuration de PHP dans Sites Web Azure
=========================================

Ce guide vous explique comment configurer l'exécution PHP intégrée dans Sites Web Azure, fournir une exécution PHP personnalisée et activer des extensions dans Sites Web Azure. Pour utiliser Sites Web Azure, inscrivez-vous pour bénéficier d'une [évaluation gratuite](https://www.windowsazure.com/en-us/pricing/free-trial/). Pour tirer pleinement parti de ce guide, vous devez d'abord créer un site PHP dans Sites Web Azure (voir la page [Didacticiels du Centre de développement PHP](https://www.windowsazure.com/en-us/develop/php/tutorials/)). Pour obtenir des informations générales sur la configuration de sites dans Sites Web Azure, consultez la page [Configuration de sites Web](https://www.windowsazure.com/en-us/manage/services/web-sites/how-to-configure-websites/).

Sommaire
--------

-   [Présentation de Sites Web Azure](#WhatIs)
-   [Modification de la configuration PHP par défaut](#ChangeBuiltInPHP)
-   [Activation d'extensions dans l'exécution PHP intégrée](#EnableExtDefaultPHP)
-   [Utilisation d'une exécution PHP personnalisée](#UseCustomPHP)
-   [Étapes suivantes](#NextSteps)

Présentation de Sites Web Azure
-------------------------------

Sites Web Azure vous permet de générer des sites Web très évolutifs dans Azure. Vous pouvez déployer, en un tournemain, des sites dans un environnement cloud très évolutif qui vous permet de commencer avec une petite structure et d'évoluer à mesure que le trafic augmente. Sites Web Azure utilise les langages et les applications Open Source de votre choix et prend en charge le déploiement avec Git, FTP et TFS. Vous pouvez facilement intégrer d'autres services tels que MySQL, Base de données SQL, Mise en cache, CDN et Storage.

Modification de la configuration PHP par défaut
-----------------------------------------------

Par défaut, PHP 5.4 est installé et immédiatement disponible lorsque vous créez un site Web Azure. Pour afficher la révision de version disponible, sa configuration par défaut et les extensions activées, la méthode idéale consiste à déployer un script qui appelle la fonction [phpinfo()](http://php.net/manual/en/function.phpinfo.php).

PHP 5.5 est également disponible, mais n'est pas activé par défaut. Pour l'activer, procédez comme suit :

1.  Accédez au tableau de bord de votre site Web dans le portail Azure, puis cliquez sur **Configurer**.

    ![Onglet Configurer sur le tableau de bord des sites Web](./media/web-sites-php-configure/configure.png)

2.  Cliquez sur PHP 5.5.

    ![Sélectionner la version PHP](./media/web-sites-php-configure/select-php-version.png)

3.  Cliquez sur **Enregistrer** au bas de la page.

    ![Enregistrer les paramètres de configuration](./media/web-sites-php-configure/save-button.png)

Quelle que soit l'exécution PHP intégrée, vous pouvez modifier toute option de configuration qui n'est pas une directive de niveau système uniquement en procédant comme suit. Pour plus d'informations sur les directives de niveau système uniquement, consultez la page [Liste des directives du php.ini](http://www.php.net/manual/en/ini.list.php).

1.  Ajoutez un fichier [.user.ini](http://www.php.net/manual/en/configuration.file.per-user.php) à votre répertoire racine.
2.  Ajoutez des paramètres de configuration au fichier `.user.ini` en utilisant la même syntaxe que pour le fichier `php.ini`. Si vous souhaitez, par exemple, activer le paramètre `display_errors` et définir le paramètre `upload_max_filesize` sur 10M, votre fichier `.user.ini` doit contenir le texte suivant :

         ; Example Settings
         display_errors=On
         upload_max_filesize=10M

3.  Déployez votre application.
4.  Redémarrez votre site Web. Le redémarrage est nécessaire, car la fréquence à laquelle PHP lit le fichier `.user.ini` est déterminée par `user_ini.cache_ttl`, un paramètre de niveau système défini, par défaut, sur 300 secondes (5 minutes). Le redémarrage du site force PHP à lire les nouveaux paramètres du fichier `.user.ini`.

Au lieu d'utiliser le fichier `.user.ini`, vous pouvez utiliser la fonction [ini\_set()](http://www.php.net/manual/en/function.ini-set.php) dans des scripts afin de définir des options de configuration qui ne sont pas des directives de niveau système.

Activation d'extensions dans l'exécution PHP par défaut
-------------------------------------------------------

Comme indiqué dans la section précédente, la méthode idéale pour afficher la version PHP par défaut, sa configuration par défaut et les extensions activées consiste à déployer un script qui appelle [phpinfo()](http://php.net/manual/en/function.phpinfo.php). Pour activer des extensions supplémentaires, procédez comme suit.

1.  Ajoutez un répertoire `bin` à votre répertoire racine.
2.  Placez les fichiers avec l'extension `.dll` dans le répertoire `bin` (`php_mongo.dll`, par exemple). Assurez-vous que les extensions sont compatibles avec la version par défaut de PHP (qui, au moment où nous écrivons ces lignes, est PHP 5.4), ainsi qu'avec VC9 et NTS (Non-Thread Safe).
3.  Déployez votre application.
4.  Accédez au tableau de bord de votre site Web dans le portail Azure, puis cliquez sur **Configurer**.

    ![Onglet Configurer sur le tableau de bord des sites Web](./media/web-sites-php-configure/configure.png)

5.  Dans la section **App Settings**, créez une clé **PHP\_EXTENSIONS** et une valeur **bin\\your-ext-file**. Pour activer plusieurs extensions, insérez une liste de fichiers `.dll` séparés par des virgules.

    ![Activer une extension dans les paramètres d'application](./media/web-sites-php-configure/app-settings.png)

6.  Cliquez sur **Enregistrer** au bas de la page.

    ![Enregistrer les paramètres de configuration](./media/web-sites-php-configure/save-button.png)

Utilisation d'une exécution PHP personnalisée
---------------------------------------------

Au lieu de l'exécution PHP par défaut, Sites Web Azure peut utiliser une exécution PHP que vous fournissez pour exécuter des scripts PHP. L'exécution en question peut être configurée par un fichier `php.ini` fourni également par vos soins. Pour utiliser une exécution PHP personnalisée dans Sites Web Azure, procédez comme suit.

1.  Obtenez une version de PHP compatible avec VC9 et NTS pour Windows. Les versions récentes de PHP pour Windows sont disponibles à l'adresse suivante : <http://windows.php.net/download/>. Les versions plus anciennes sont disponibles dans l'archive accessible à cette adresse : <http://windows.php.net/downloads/releases/archives/>.
2.  Modifiez le fichier `php.ini` pour votre exécution. Notez que tout paramètre de configuration qui correspond à une directive de niveau système uniquement est ignoré par Sites Web Azure. Pour plus d'informations sur les directives de niveau système uniquement, consultez la page [Liste des directives du php.ini](http://www.php.net/manual/en/ini.list.php).
3.  Vous pouvez éventuellement ajouter des extensions pour votre exécution PHP et les activer dans le fichier `php.ini`.
4.  Ajoutez le répertoire `bin` à votre répertoire racine et placez-y le répertoire contenant votre exécution PHP (`bin\php`, par exemple).
5.  Déployez votre application.
6.  Accédez au tableau de bord de votre site Web dans le portail Azure, puis cliquez sur **Configurer**.

    ![Onglet Configurer sur le tableau de bord des sites Web](./media/web-sites-php-configure/configure.png)

7.  Dans la section **Mappages de gestionnaires**, ajoutez `*.php` à EXTENSION et ajoutez le chemin d'accès à l'exécutable `php-cgi.exe`. Si vous avez placé votre exécution PHP dans le répertoire `bin` situé à la racine de votre application, le chemin sera `D:\home\site\wwwroot\bin\php\php-cgi.exe`.

    ![Indiquer le gestionnaire dans les mappages](./media/web-sites-php-configure/handler-mappings.png)

8.  Cliquez sur **Enregistrer** au bas de la page.

    ![Enregistrer les paramètres de configuration](./media/web-sites-php-configure/save-button.png)

Étapes suivantes
----------------

Maintenant que vous avez appris à configurer PHP dans Sites Web Azure, suivez les liens ci-dessous pour découvrir d'autres tâches.

-   [Configuration, surveillance et mise à l'échelle de vos sites Web dans Azure](http://www.windowsazure.com/en-us/manage/services/web-sites/)
-   [Téléchargement du Kit de développement logiciel (SDK) Azure pour PHP](http://www.windowsazure.com/en-us/develop/php/common-tasks/download-php-sdk/)

