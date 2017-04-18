L’émulateur de stockage prend en charge uniquement un compte fixe et une clé d’authentification connue pour l’authentification par clé partagée. Ce compte et cette clé sont les seules informations d’identification par clé partagée autorisées pour une utilisation avec l’émulateur de stockage. Il s'agit de :

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> La clé d'authentification prise en charge par l'émulateur de stockage est destinée uniquement au test de la fonctionnalité de votre code d'authentification du client. Elle n'offre aucune fonction de sécurité. Vous ne pouvez pas utiliser votre compte et votre clé de stockage de production avec l'émulateur de stockage. Vous ne devez pas utiliser le compte de développement avec des données de production.
> 
> L’émulateur de stockage prend uniquement en charge la connexion via le protocole HTTP. Toutefois, HTTPS est le protocole recommandé pour l’accès aux ressources dans un compte de Stockage Azure de production.
> 

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>Se connecter au compte de l’émulateur à l’aide d’un raccourci
Le moyen le plus simple de vous connecter à l’émulateur de stockage à partir de votre application est de configurer une chaîne de connexion dans le fichier de configuration de votre application qui référence le raccourci `UseDevelopmentStorage=true`. Voici un exemple de chaîne de connexion à l’émulateur de stockage dans un fichier *app.config* : 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="connect-to-the-emulator-account-using-the-well-known-account-name-and-key"></a>Se connecter au compte de l’émulateur à l’aide d’un nom de compte connu et d’une clé
Pour créer une chaîne de connexion qui référence le nom et la clé du compte de l’émulateur, vous devez définir les points de terminaison associés aux services que vous souhaitez utiliser à partir de l’émulateur dans la chaîne de connexion. Cela est nécessaire pour que la chaîne de connexion puisse référencer les points de terminaison de l’émulateur, qui sont différents de ceux associés à un compte de stockage de production. Par exemple, la valeur de votre chaîne de connexion ressemblera à ceci :

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
TableEndpoint=http://127.0.0.1:10002/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

Cette valeur est identique au raccourci présenté plus haut, `UseDevelopmentStorage=true`.

#### <a name="specify-an-http-proxy"></a>Spécifier un proxy HTTP
Vous pouvez aussi spécifier un proxy HTTP à utiliser lorsque vous testez votre service sur l’émulateur de stockage. Cela peut être utile pour observer les demandes et les réponses HTTP pendant que vous déboguez des opérations sur les services de stockage. Pour spécifier un proxy, ajoutez l’option `DevelopmentStorageProxyUri` à la chaîne de connexion, puis définissez sa valeur sur l’URI du proxy. Voici par exemple une chaîne de connexion qui pointe vers l’émulateur de stockage et configure un proxy HTTP :

```
UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://myProxyUri
```

