<properties
	pageTitle="Configurer PHP dans Azure App Service Web Apps "
	description="Apprenez à configurer l'installation de PHP par défaut ou à ajouter une installation de PHP personnalisée pour Web Apps dans Azure App Service."
	services="app-service\web"
	documentationCenter="php"
	authors="tfitzmac"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="PHP"
	ms.topic="article"
	ms.date="03/24/2015"
	ms.author="tomfitz"/>

#Configurer PHP dans Azure App Service Web Apps 

## Introduction

Ce guide vous explique comment configurer le runtime PHP intégré dans Web Apps pour [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714), fournir un runtime PHP personnalisé et activer des extensions. Pour utiliser App Service, souscrivez à la [version d'évaluation gratuite]. Pour tirer le meilleur parti de ce guide, commencez par créer une application web PHP dans App Service.

##  Modification de la configuration PHP par défaut
Par défaut, PHP 5.4 est installé et immédiatement utilisable lorsque vous créez une application web App Service. Pour visualiser la révision de version disponible, sa configuration par défaut et les extensions activées, la meilleure solution consiste à déployer un script qui appelle la fonction [phpinfo()].

Les versions PHP 5.5 et PHP 5.6 sont également disponibles, mais ne sont pas activées par défaut. Pour mettre à jour la version de PHP, procédez comme suit :

1. Accédez à votre application web dans le [portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715), puis cliquez sur le bouton **Paramètres**.

	![Paramètres d'application web][settings-button]

2. Dans le panneau **Paramètres**, sélectionnez **Paramètres de l'application**, puis choisissez la nouvelle version PHP.

    ![Paramètres de l'application][application-settings]

3. Cliquez sur le bouton **Enregistrer** dans la partie supérieure du panneau **Paramètres d'application web**.

	![Enregistrer les paramètres de configuration][save-button]

Quel que soit le runtime PHP intégré, vous pouvez modifier toute option de configuration qui n'est pas une directive de niveau système uniquement en procédant comme suit. (Pour plus d'informations sur les directives de niveau système uniquement, voir [Liste des directives du php.ini].)

1. Ajoutez un fichier [.user.ini] à votre répertoire racine.
2. Ajoutez des paramètres de configuration au fichier `.user.ini` en utilisant la même syntaxe que pour le fichier `php.ini`. Par exemple, si vous souhaitez activer le paramètre `display_errors` et définir le paramètre `upload_max_filesize` sur 10M, votre fichier `.user.ini` doit contenir le texte suivant :

		; Example Settings
		display_errors=On
		upload_max_filesize=10M

3. Déployez votre application web.
4. Redémarrez l'application web. (Le redémarrage est nécessaire, car la fréquence à laquelle PHP lit les fichiers `.user.ini` est déterminée par le paramètre `user_ini.cache_ttl`, un paramètre de niveau système défini par défaut sur 300 secondes (5 minutes). Le redémarrage de l'application web force PHP à lire les nouveaux paramètres du fichier `.user.ini`.)

Au lieu d'utiliser un fichier `.user.ini`, vous pouvez utiliser la fonction [ini_set()] dans des scripts afin de définir des options de configuration qui ne sont pas des directives de niveau système.

##  Activation des extensions dans l'exécution PHP par défaut
Comme indiqué à la section précédente, la méthode idéale pour visualiser la version PHP par défaut, sa configuration par défaut et les extensions activées consiste à déployer un script qui appelle [phpinfo()]. Pour activer des extensions supplémentaires, procédez comme suit.

1. Ajoutez un répertoire `bin` au répertoire racine.
2. Placez les fichiers d'extension `.dll` dans le répertoire `bin` (par exemple,  `php_mongo.dll`). Assurez-vous que les extensions sont compatibles avec la version par défaut de PHP (qui, au moment où nous écrivons ces lignes, est PHP 5.4), ainsi qu'avec VC9 et NTS (Non-Thread Safe).
3. Déployez votre application web.
4. Accédez à votre application web dans le portail Azure, puis cliquez sur le bouton **Paramètres**.

	![Web App Settings][settings-button]

5. Dans le panneau **Paramètres**, sélectionnez **Paramètres de l'application**, puis accédez à la section **Paramètres de l'application**.
6. Dans la section **Paramètres de l'application**, créez une clé **PHP_EXTENSIONS**. La valeur de cette clé est un chemin d'accès relatif à la racine du site web : **bin\votre-fichier-d'extension**.

	![Enable extension in app settings][php-extensions]

7. Cliquez sur le bouton **Enregistrer** dans la partie supérieure du panneau **Paramètres d'application web**.

	![Save configuration settings][save-button]

Les extensions Zend sont également prises en charge via l'utilisation d'une clé **PHP_ZENDEXTENSIONS**. Pour activer plusieurs extensions, insérez une liste séparée par des virgules de fichiers `.dll` pour la valeur de paramètre de l'application.

##  Utilisation d'un runtime PHP personnalisé
Au lieu du runtime PHP par défaut, App Service Web Apps peut utiliser un runtime PHP que vous fournissez pour exécuter des scripts PHP. Le runtime en question peut être configuré par un fichier `php.ini` également fourni par vos soins. Pour utiliser un runtime PHP personnalisé avec Web Apps, suivez la procédure ci-après.

1. Obtenez une version de PHP compatible avec VC9 et NTS pour Windows. Les versions récentes de PHP pour Windows sont disponibles à l'adresse suivante : [http://windows.php.net/download/]. Les versions plus anciennes sont disponibles dans l'archive accessible à cette adresse : [http://windows.php.net/downloads/releases/archives/].
2. Modifiez le fichier `php.ini` pour votre runtime. Notez que tout paramètre de configuration correspondant à une directive de niveau système uniquement est ignoré par Web Apps. (Pour plus d'informations sur les directives de niveau système uniquement, voir [Liste des directives du php.ini]).
3. Vous pouvez éventuellement ajouter des extensions à votre runtime PHP et les activer dans le fichier `php.ini`.
4. Ajoutez un répertoire `bin` à votre répertoire racine, puis placez-y le répertoire contenant votre runtime PHP (par exemple, `bin\php`).
5. Déployez votre application web.
4. Accédez à votre application web dans le portail Azure, puis cliquez sur le bouton **Paramètres**.

	![Web App Settings][settings-button]

7. Dans le panneau **Paramètres**, sélectionnez **Paramètres de l'application**, puis accédez à la section **Mappages de gestionnaires**. Ajoutez " *.php " au champ Extension, puis ajoutez le chemin d'accès à l'exécutable `php-cgi.exe`. Si vous avez placé votre runtime PHP dans le répertoire `bin` situé à la racine de votre application, le chemin d'accès sera `D:\home\site\wwwroot\bin\php\php-cgi.exe`.

	![Specify handler in hander mappings][handler-mappings]

8. Cliquez sur le bouton **Enregistrer** dans la partie supérieure du panneau **Paramètres d'application web**.

	![Save configuration settings][save-button]

>[AZURE.NOTE] Si vous souhaitez commencer à utiliser Azure App Service avant d'ouvrir un compte Azure, accédez au site permettant d'[essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pourrez créer immédiatement une application web de départ de courte durée dans App Service. Aucune carte de crédit n'est requise, et vous ne prenez aucun engagement.

## Nouveautés
* Pour plus d'informations sur le remplacement de Sites Web par App Service, voir : [Azure App Service et son impact sur les services Azure existants (en anglais)](http://go.microsoft.com/fwlink/?LinkId=529714)
* Pour plus d'informations sur le remplacement de l'ancien portail par le nouveau portail, voir : [Référence en matière de navigation dans le portail en version préliminaire (en anglais)](http://go.microsoft.com/fwlink/?LinkId=529715)

[version d'évaluation gratuite]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo()]: http://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[Liste des directives du php.ini]: http://www.php.net/manual/en/ini.list.php
[.user.ini]: http://www.php.net/manual/en/configuration.file.per-user.php
[ini_set()]: http://www.php.net/manual/en/function.ini-set.php
[application-settings]: ./media/web-sites-php-configure/application-settings.png
[settings-button]: ./media/web-sites-php-configure/settings-button.png
[save-button]: ./media/web-sites-php-configure/save-button.png
[php-extensions]: ./media/web-sites-php-configure/php-extensions.png
[handler-mappings]: ./media/web-sites-php-configure/handler-mappings.png
[http://windows.php.net/download/]: http://windows.php.net/download/
[http://windows.php.net/downloads/releases/archives/]: http://windows.php.net/downloads/releases/archives/

<!--HONumber=49-->