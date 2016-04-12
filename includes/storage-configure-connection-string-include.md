## Configurer une chaîne de connexion de stockage

La bibliothèque du client de stockage Azure pour .NET prend en charge l’utilisation d’une chaîne de connexion de stockage pour la configuration de points de terminaison et d’informations d’identification permettant d’accéder aux services de stockage. La meilleure façon de conserver votre chaîne de connexion de stockage est dans un fichier de configuration.

Pour plus d’informations sur les chaînes de connexion, voir [Configuration d’une chaîne de connexion dans Azure Storage](../articles/storage/storage-configure-connection-string.md).

> [AZURE.NOTE] Votre clé de compte de stockage est similaire au mot de passe racine pour votre compte de stockage. Veillez toujours à protéger votre clé de compte de stockage. Évitez de la communiquer à d’autres utilisateurs, de la coder en dur ou de l’enregistrer dans un fichier texte brut accessible à d’autres personnes. Régénérez votre clé à l’aide du portail Azure si vous pensez que sa confidentialité est compromise.

### Déterminer votre environnement cible

Vous avez le choix entre deux environnements pour exécuter les exemples de ce guide :

- Vous pouvez exécuter votre code sur l’émulateur de stockage Azure. L’émulateur de stockage est un environnement local qui émule un compte Azure Storage dans le cloud. L’émulateur est une option gratuite permettant de tester et déboguer votre code lors du développement de votre application. L’émulateur utilise un compte et une clé connus. Pour plus d’informations, voir [Utilisation de l’émulateur de stockage Azure pour le développement et le test](../articles/storage/storage-use-emulator.md).
- Vous pouvez exécuter votre code sur un compte Azure Storage dans le cloud. 

Si vous ciblez un compte de stockage dans le cloud, copiez la clé d’accès primaire de votre compte de stockage à partir du portail Azure. Pour plus d’informations, voir [Affichage et copie de clés d’accès de stockage](../articles/storage/storage-create-storage-account.md#view-and-copy-storage-access-keys).

> [AZURE.NOTE] Vous pouvez cibler l’émulateur de stockage pour éviter les frais liés à l’utilisation des services de stockage Azure. Toutefois, si vous choisissez de cibler un compte de stockage Azure situé dans le cloud, les frais associés à l’utilisation de ce didacticiel seront négligeables.
	
### Configurer votre chaîne de connexion en utilisant la configuration .NET

Si votre application s’exécute sur un appareil mobile ou de bureau, sur une machine virtuelle Azure, ou dans une application web Azure, enregistrez votre chaîne de connexion à l’aide de la configuration .NET (*par exemple,* dans le fichier `web.config` ou `app.config` de l’application). Stockez votre chaîne de connexion à l’aide de l’élément `<appSettings>` comme suit. Remplacez `account-name` par le nom de votre compte de stockage et `account-key` par votre clé d’accès au compte :

	<configuration>
  		<appSettings>
    		<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
  		</appSettings>
	</configuration>

Par exemple, votre paramètre de configuration sera semblable à :

	<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=account-key" />

Pour cibler l’émulateur de stockage, vous pouvez utiliser un raccourci qui correspond à la clé et au nom de compte connus. Dans ce cas, le paramètre de votre chaîne de connexion sera :

	<add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />

### Configurer votre chaîne de connexion pour un service cloud Azure

Si votre application s’exécute dans un service cloud Azure, enregistrez votre chaîne de connexion à l’aide du système des fichiers de configuration de service Azure (fichiers `*.csdef` et `*.cscfg`). Voir [Création et déploiement d’un service cloud](../articles/cloud-services/cloud-services-how-to-create-deploy.md) pour plus d’informations sur la configuration de service cloud Azure.

<!---HONumber=AcomDC_0406_2016-->