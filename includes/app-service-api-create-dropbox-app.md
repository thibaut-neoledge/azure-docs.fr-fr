Les étapes suivantes montrent le processus de création d’une application Dropbox à l’aide du site Dropbox.com. Comme le site Dropbox.com peut être modifié sans préavis, il est possible que vous constatiez des différences dans l’interface utilisateur par rapport à ce qui est présenté ici.

1. Accédez au [portail des développeurs Dropbox](https://www.dropbox.com/developers/apps), cliquez sur **App Console**, puis sur **Create App**.

	![Créer une application Dropbox](./media/app-service-api-create-dropbox-app/dbappcreate.png)

2. Choisissez **Dropbox API app** et configurez les autres paramètres.
 
	Les options d’accès aux fichiers indiquées dans la capture d’écran ci-dessous vous permettent de tester l’accès à votre compte Dropbox avec une simple demande HTTP Get, si vous avez des fichiers dans votre compte.

	Le nom de l’application API Dropbox peut être n’importe quel nom accepté par le site Dropbox.

3. Cliquez sur **Create app**.

	![Créer une application Dropbox](./media/app-service-api-create-dropbox-app/dbapiapp.png)

	La page suivante affiche les paramètres de clé et de clé secrète d’application (ID client et clé secrète client dans Azure) que vous utiliserez pour la configuration de votre connecteur Dropbox Azure.

	Elle contient également un champ dans lequel vous pouvez entrer un URI de redirection, dont vous obtenez la valeur dans la section suivante.

	![Créer une application Dropbox](./media/app-service-api-create-dropbox-app/dbappsettings.png)

<!---HONumber=August15_HO6-->