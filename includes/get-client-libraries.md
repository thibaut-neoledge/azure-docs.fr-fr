###Installation via Composer

1. [Installez Git][install-git]. 

 
	> [AZURE.NOTE] Sous Windows, vous devez aussi ajouter l'exécutable Git à votre variable d'environnement PATH.

2. Créez un fichier nommé **composer.json** à la racine de votre projet et ajoutez-y le code suivant :

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

3. Téléchargez **[composer.phar][composer-phar]** à la racine du projet.

4. Ouvrez une invite de commandes et exécutez cette commande à la racine du projet :

		php composer.phar install

###Installation manuelle

Pour télécharger et installer manuellement les bibliothèques clientes PHP pour Azure, procédez comme suit :

1. Téléchargez une archive ZIP contenant les bibliothèques de [GitHub][php-sdk-github]. Sinon, répliquez le répertoire et clonez-le sur votre ordinateur local. (La deuxième option requiert un compte GitHub et l'installation locale de Git.)

	
	> [AZURE.NOTE] Les bibliothèques clientes PHP pour Azure ont une dépendance sur les packages PEAR [HTTP_Request2](http://pear.php.net/package/HTTP_Request2), [Mail_mime](http://pear.php.net/package/Mail_mime), et [Mail_mimeDecode](http://pear.php.net/package/Mail_mimeDecode) . La méthode recommandée pour résoudre ces dépendances consiste à installer ces packages à l'aide du [Gestionnaire de package PEAR](http://pear.php.net/manual/en/installation.php).


2. Copiez le répertoire `WindowsAzure` de l'archive téléchargée dans la structure de répertoires de votre application.

Pour plus d'informations sur l'installation des bibliothèques clientes PHP pour Azure (y compris des informations sur l'installation en tant que package PEAR), consultez la page [Télécharger le Kit de développement logiciel (SDK) Azure pour PHP][download-SDK-PHP].


[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: ../php-download-sdk/
[composer-phar]: http://getcomposer.org/composer.phar

<!--HONumber=42-->
