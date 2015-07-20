## Configurer une chaîne de connexion de stockage

La bibliothèque du client de stockage Azure pour .NET prend en charge l’utilisation d’une chaîne de connexion de stockage pour la configuration de points de terminaison et d’informations d’identification permettant d’accéder aux services de stockage. Nous vous recommandons de conserver votre chaîne de connexion de stockage dans un fichier de configuration au lieu de la coder en dur dans votre application. Deux options sont disponibles pour enregistrer votre chaîne de connexion :

- Si votre application s’exécute dans un service cloud Azure, enregistrez votre chaîne de connexion à l’aide du système de configuration de service Azure (fichiers `*.csdef` et `*.cscfg`). Pour plus d'informations sur la configuration de service cloud Azure, consultez [Création et déploiement d’un service cloud](../articles/cloud-services/cloud-services-how-to-create-deploy.md).
- Si votre application s’exécute sur des ordinateurs virtuels Azure, ou si vous développez des applications .NET qui s’exécuteront en dehors d’Azure, enregistrez votre chaîne de connexion à l’aide du système de configuration .NET (par exemple, le fichier `web.config` ou `app.config`).

Plus loin dans ce guide, nous vous montrerons comment extraire votre chaîne de connexion de votre code.

### Configuration de votre chaîne de connexion à partir d'un service cloud Azure

Le mécanisme de configuration de service propre à Azure Cloud Services vous permet de modifier les paramètres de configuration de façon dynamique à partir du portail de gestion Azure sans avoir à redéployer votre application.

Pour configurer votre chaîne de connexion dans la configuration de service Azure :

1.  Dans l'Explorateur de solutions de Visual Studio, dans le dossier **Rôles** de votre projet de déploiement Azure, cliquez avec le bouton droit sur votre rôle Web ou de travail, puis cliquez sur **Propriétés**. ![Sélectionner les propriétés sur un rôle de service cloud dans Visual Studio][connection-string1]

2.  Cliquez sur l’onglet **Paramètres** et appuyez sur le bouton **Ajouter un paramètre**. ![Ajouter un paramètre de service cloud dans Visual Studio][connection-string2]

    Une nouvelle entrée nommée **Setting1** est ensuite affichée dans la grille des paramètres.

3.  Dans la liste déroulante **Type** de la nouvelle entrée **Setting1**, choisissez **Chaîne de connexion**. ![Définir le type de chaîne de connexion][connection-string3]

4.  Cliquez sur le bouton **...** situé à l’extrême droite de l’entrée **Setting1**. La boîte de dialogue **Storage Account Connection String** s'ouvre.

5.  Indiquez si vous voulez cibler l’émulateur de stockage (stockage Windows Azure simulé sur votre ordinateur local) ou un compte de stockage dans le cloud. Le code de ce guide fonctionne dans les deux cas.

	> [AZURE.NOTE]Vous pouvez cibler l’émulateur de stockage pour éviter les frais liés à l’utilisation des services de stockage Azure. Toutefois, si vous choisissez de cibler un compte de stockage Azure situé dans le cloud, les frais associés à l’utilisation de ce didacticiel seront négligeables.

	Si vous ciblez un compte de stockage dans le cloud, entrez la clé d’accès primaire de ce compte de stockage. Pour découvrir comment copier votre clé d’accès primaire via le portail de gestion Azure, consultez [Afficher, copier et régénérer des clés d’accès de stockage](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

	> [AZURE.NOTE]Votre clé de compte de stockage est similaire au mot de passe racine pour votre compte de stockage. Veillez à protéger votre clé. Évitez de la communiquer à d'autres utilisateurs ou de l'enregistrer dans un fichier texte brut accessible à d'autres personnes. Régénérez votre clé à l'aide du portail de gestion si vous pensez que sa confidentialité est compromise.
	
    ![Select target environment][connection-string4]

6.  Remplacez la valeur **Setting1** de l’entrée **Name** par un nom plus convivial tel que **ChaineConnexionStockage**. Le référencement de la chaîne de connexion dans le code sera effectué plus loin dans ce guide. ![Modifier le nom de la chaîne de connexion][connection-string5]
	
### Configuration de votre chaîne de connexion en utilisant la configuration .NET

Si vous développez une application autre qu’un service cloud Azure (consultez la section précédente), nous vous recommandons d’utiliser le système de configuration .NET (par exemple `web.config` ou `app.config`). Ceci inclut les sites web Azure ou les machines virtuelles Azure, ainsi que les applications conçues pour être exécutées en dehors d’Azure. Vous stockez la chaîne de connexion à l’aide de l’élément `<appSettings>` comme suit. Remplacez `account-name` par le nom de votre compte de stockage et `account-key` par votre clé d’accès au compte :

	<configuration>
  		<appSettings>
    		<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
  		</appSettings>
	</configuration>

Par exemple, le paramètre de configuration de votre fichier de configuration peut être semblable à ce qui suit :

	<configuration>
    	<appSettings>
      		<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=nYV0gln9fT7bvY+rxu2iWAEyzPNITGkhM88J8HUoyofpK7C8fHcZc2kIZp6cKgYRUM74lHI84L50Iau1+9hPjB==" />
    	</appSettings>
	</configuration>

Vous pouvez à présent effectuer les tâches présentées dans ce guide.

[connection-string1]: ./media/storage-configure-connection-string-include/connection-string1.png
[connection-string2]: ./media/storage-configure-connection-string-include/connection-string2.png
[connection-string3]: ./media/storage-configure-connection-string-include/connection-string3.png
[connection-string4]: ./media/storage-configure-connection-string-include/connection-string4.png
[connection-string5]: ./media/storage-configure-connection-string-include/connection-string5.png

[Configuring Connection Strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx

<!---HONumber=July15_HO2-->