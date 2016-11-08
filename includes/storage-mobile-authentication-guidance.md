## Configuration de votre application pour accéder à Azure Storage
Il existe deux manières d’authentifier votre application pour accéder aux services de stockage :

* Clé partagée : utilisez la clé partagée aux fins de test uniquement
* Signature d’accès partagé (SAP) : utilisez la signature d’accès partagé pour les applications de production

### Clé partagée
L’authentification par clé partagée signifie que votre application utilisera votre nom de compte et votre clé de compte pour accéder aux services de stockage. Afin de vous montrer rapidement comment utiliser cette bibliothèque, nous allons utiliser l’authentification par clé partagée dans cet article de prise en main.

> [AZURE.WARNING (Only use Shared Key authentication for testing purposes!) ] Votre nom de compte et votre clé de compte, qui donnent un accès complet en lecture/écriture au compte de stockage associé, seront distribués à chaque personne qui télécharge votre application. Ce n’est **pas** une bonne pratique car votre clé risque d’être compromise par des clients non approuvés.
> 
> 

Lorsque vous utilisez une authentification par clé partagée, vous créez une [chaîne de connexion](../articles/storage/storage-configure-connection-string.md). La chaîne de connexion comporte les éléments suivants :

* **DefaultEndpointsProtocol** : vous avez le choix entre les protocoles HTTP et HTTPS. Toutefois, l’utilisation du protocole HTTPS est fortement recommandée.
* **Nom du compte** : nom de votre compte de stockage.
* **Clé de compte** : dans le [portail Azure](https://portal.azure.com), accédez à votre compte de stockage, puis cliquez sur l’icône **Clés** pour rechercher ces informations.
* (Facultatif) **EndpointSuffix** : utilisé pour les services de stockage dans les régions avec des suffixes de point de terminaison différents, comme Azure China ou Azure Governance.

Voici un exemple de chaîne de connexion utilisant l’authentification par clé partagée :

`"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"`

### Signatures d’accès partagé (SAP)
Pour une application mobile, la méthode recommandée pour authentifier une demande d’un client par rapport au service de stockage Azure consiste à utiliser une signature d’accès partagé (SAP). Une SAP vous permet d’accorder un accès client à une ressource pour une période spécifiée, avec un jeu d’autorisations spécifié. En tant que propriétaire du compte de stockage, vous devez générer une SAP que vos clients mobiles pourront consommer. Pour ce faire, vous souhaiterez probablement écrire un service séparé qui génère les SAP à distribuer à vos clients. À des fins de test, vous pouvez utiliser [l’Explorateur de stockage Microsoft Azure](http://storageexplorer.com) ou le [portail Azure](https://portal.azure.com) pour générer une SAP. Lorsque vous créez la SAP, vous pouvez spécifier l’intervalle de temps pendant lequel elle est valide et les autorisations qu’elle accorde au client.

L’exemple suivant montre comment utiliser l’Explorateur de stockage Microsoft Azure pour générer une SAP.

1. Si ce n’est déjà fait, [installez l’Explorateur de stockage Microsoft Azure](http://storageexplorer.com)
2. Connectez-vous à votre abonnement.
3. Cliquez sur votre compte de stockage puis sur l’onglet « Actions » en bas à gauche. Cliquez sur « Obtenir une signature d’accès partagé » afin de générer une « chaîne de connexion » pour votre SAS.
4. Voici un exemple de chaîne de connexion SAS qui accorde des autorisations en lecture et écriture au niveau du service, du conteneur et de l’objet pour le service blob du compte de stockage.
   
   `"SharedAccessSignature=sv=2015-04-05&ss=b&srt=sco&sp=rw&se=2016-07-21T18%3A00%3A00Z&sig=3ABdLOJZosCp0o491T%2BqZGKIhafF1nlM3MzESDDD3Gg%3D;BlobEndpoint=https://youraccount.blob.core.windows.net"`

Comme vous pouvez le voir, lorsque vous utilisez une SAP, vous n’exposez pas votre clé de compte dans votre application. Vous pouvez en savoir plus sur les SAP et les meilleures pratiques en matière d’utilisation de SAP en consultant [Signatures d’accès partagé : présentation du modèle SAP](../articles/storage/storage-dotnet-shared-access-signature-part-1.md).

<!---HONumber=AcomDC_0907_2016-->