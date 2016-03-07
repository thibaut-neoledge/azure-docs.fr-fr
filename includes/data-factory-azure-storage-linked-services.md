## Service lié Azure Storage

Le **service lié Azure Storage** vous permet de lier un compte de stockage Azure à une fabrique de données Azure à l’aide de la **clé de compte**. Ainsi, la fabrique de données dispose d’un accès global à Azure Storage. Le tableau suivant fournit la description des éléments JSON spécifiques au service lié Azure Storage.

| Propriété | Description | Requis |
| :-------- | :----------- | :-------- |
| type | La propriété de type doit être définie sur : **AzureStorage** | Oui |
| connectionString | Spécifier les informations requises pour la connexion au stockage Azure pour la propriété connectionString. | Oui |

Consultez l’article suivant pour savoir comment afficher ou copier la clé de compte pour un stockage Azure : [Affichage, copie et régénération de clés d’accès de stockage](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

**Exemple :**
  
	{  
		"name": "StorageLinkedService",  
		"properties": {  
			"type": "AzureStorage",  
			"typeProperties": {  
				"connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"  
			}  
		}  
	}  


## Service lié SAP Azure Storage  
Une signature d’accès partagé (SAP) fournit un accès délégué aux ressources de votre compte de stockage. Cela vous permet d’octroyer à un client des autorisations d’accès limité à des objets de votre compte de stockage pendant une période donnée et avec un ensemble défini d’autorisations, sans partager les clés d’accès de votre compte. La signature d'accès partagé est un URI qui englobe dans ses paramètres de requête toutes les informations nécessaires pour un accès authentifié à une ressource de stockage. Pour accéder aux ressources de stockage avec la signature d'accès partagé, il suffit au client de transmettre cette dernière à la méthode ou au constructeur approprié. Pour plus d’informations sur SAP, consultez [Signatures d’accès partagé : présentation du modèle SAP](../articles/storage/storage-dotnet-shared-access-signature-part-1.md).
  
Le service lié Azure Storage SAS vous permet de lier un compte de stockage Azure à une fabrique de données Azure à l’aide d’une signature d’accès partagé (SAP). Ainsi, la fabrique de données dispose d’un accès restreint ou limité dans le temps à tout ou partie des ressources (objet blob/conteneur) dans le stockage. Le tableau suivant fournit la description des éléments JSON spécifiques au service lié Azure Storage SAS.

| Propriété | Description | Requis |
| :-------- | :----------- | :-------- |
| type | La propriété de type doit être définie sur : **AzureStorageSas**. | Oui |
| sasUri | Spécifiez l’URI de signature d’accès partagé des ressources Azure Storage, telles qu’un objet blob, un conteneur ou une table. Pour plus d’informations, consultez les remarques ci-dessous. | Oui | 


**Exemple :**
  
	{  
		"name": "StorageSasLinkedService",  
		"properties": {  
			"type": "AzureStorageSas",  
			"typeProperties": {  
				"sasUri": "<storageUri>?<sasToken>"   
			}  
		}  
	}  

Quand vous créez un **URI SAP**, prenez en compte les points suivants :

- Azure Data Factory prend uniquement en charge les **SAP de service**, pas les SAP de compte. Pour plus d’informations sur ces deux types, consultez [Types de signatures d’accès partagé](../articles/storage/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures).
- Des **autorisations** de lecture/écriture appropriées doivent être définies sur les objets en fonction de l’utilisation du service lié (lecture, écriture, lecture/écriture) dans votre fabrique de données.
- Une **heure d’expiration** doit être définie de manière appropriée. Assurez-vous que l’accès aux objets Azure Storage n’expire pas pendant la période active du pipeline.
- L’URI doit être créé au niveau table ou objet blob/conteneur approprié en fonction des besoins. Un URI SAP vers un objet blob Azure rend ce dernier accessible au service Data Factory. Un URI SAP vers un conteneur d’objets blob Azure permet au service Data Factory de parcourir les objets blob dans ce conteneur. Si vous êtes amené à accorder l’accès à plus ou moins d’objets ou à mettre à jour l’URI SAP, mettez à jour le service lié avec le nouvel URI.   

<!---HONumber=AcomDC_0224_2016-->