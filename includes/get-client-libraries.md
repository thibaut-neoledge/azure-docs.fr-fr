### Installation via Composer

1. [Installez Git][install-git]. Notez que sous Windows, vous devez aussi ajouter l’exécutable Git à votre variable d’environnement PATH. 

2. Créez un fichier nommé **composer.json** à la racine de votre projet et ajoutez-y le code suivant :

	```
	{
      "require": {
        "microsoft/windowsazure": "^0.4"
      }
    }
	```

3. Téléchargez **[composer.phar][composer-phar]** à la racine du projet.

4. Ouvrez une invite de commande et exécutez la commande suivante à la racine du projet

	```
	php composer.phar install
	```

[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: ../articles/php-download-sdk.md
[composer-phar]: http://getcomposer.org/composer.phar

<!---HONumber=AcomDC_0601_2016-->