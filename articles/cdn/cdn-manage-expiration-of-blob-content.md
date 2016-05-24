<properties
 pageTitle="Comment gérer l'expiration des contenus d'objets blob dans le réseau de distribution de contenu Azure (CDN)"
 description="Découvrez les options de contrôle de la durée de vie des objets blob dans la mise en cache Azure CDN."
 services="cdn"
 documentationCenter=".NET"
 authors="camsoper"
 manager="erikre"
 editor=""/>
<tags
 ms.service="cdn"
 ms.workload="media"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="05/11/2016"
 ms.author="casoper"/>


#Comment gérer l'expiration des contenus d'objets blob dans le réseau de distribution de contenu Azure (CDN)  

Les objets blob qui bénéficient le plus de la mise en cache Azure CDN sont ceux qui sont fréquemment sollicités pendant leur durée de vie. Un objet blob reste dans le cache pour la durée de vie, puis est actualisé par le service blob une fois cette période écoulée. Ensuite, le processus se répète.

Vous avez deux options pour contrôler la durée de vie.

1.	Ne pas définir de valeurs de cache, donc utiliser la durée de vie par défaut de 7 jours.
2.	Définissez explicitement la propriété *x-ms-blob-cache-control* sur une demande **Put Blob**, **Put Block List** ou **Set Blob Properties** ou utilisez la bibliothèque managée Azure pour définir la propriété [BlobProperties.CacheControl](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol.aspx). Le fait de définir cette propriété définit la valeur de l’en-tête *Cache-Control* pour l’objet blob. La valeur de l'en-tête ou de la propriété doit spécifier la valeur appropriée en secondes. Par exemple, pour définir la période maximale de mise en cache sur un an, vous pouvez spécifier l’en-tête de demande sous la forme `x-ms-blob-cache-control: public, max-age=31556926`. Pour plus d’informations sur la configuration des en-têtes de mise en cache, consultez la [spécification HTTP/1.1](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html).  

Tout contenu que vous voulez mettre en cache via le CDN doit être stocké dans votre compte de stockage Azure en tant qu'objet blob accessible publiquement. Pour plus d’informations sur le service BLOB Azure, consultez la page **Concepts du service BLOB**.

Il existe différentes manières d'utiliser du contenu dans le service BLOB :

-	À l’aide de l’API managée fournie par la **référence de bibliothèque managée Azure**.
-	À l'aide d'un outil de gestion de stockage tiers
-	À l'aide de l'API REST des services de stockage Azure  

L'exemple de code suivant est une application console qui utilise la bibliothèque managée Azure pour créer un conteneur, définir ses autorisations pour l'accès public et créer un objet blob dans le conteneur. En outre, il spécifie explicitement un intervalle d'actualisation en définissant l'en-tête Cache-Control sur l'objet blob.

En supposant que vous avez activé le CDN comme indiqué ci-dessus, l'objet blob créé est mis en cache par le CDN. Veillez à spécifier vos informations d'identification de compte à l'aide de vos propres clé d'accès et compte de stockage :

	using System;
	using Microsoft.WindowsAzure;
	using Microsoft.WindowsAzure.StorageClient;

	namespace BlobsInCDN
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            //Specify storage credentials.
	            StorageCredentialsAccountAndKey credentials = new StorageCredentialsAccountAndKey("storagesample",
	                "m4AHAkXjfhlt2rE2BN/hcUR4U2lkGdCmj2/1ISutZKl+OqlrZN98Mhzq/U2AHYJT992tLmrkFW+mQgw9loIVCg==");

	            //Create a reference to your storage account, passing in your credentials.
	            CloudStorageAccount storageAccount = new CloudStorageAccount(credentials, true);

	            //Create a new client object, which will provide access to Blob service resources.
	            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	            //Create a new container.
	            CloudBlobContainer container = blobClient.GetContainerReference("cdncontent");
	            container.CreateIfNotExist();

	            //Specify that the container is publicly accessible.
	            BlobContainerPermissions containerAccess = new BlobContainerPermissions();
	            containerAccess.PublicAccess = BlobContainerPublicAccessType.Container;
	            container.SetPermissions(containerAccess);

	            //Create a new blob and write some text to it.
	            CloudBlob blob = blobClient.GetBlobReference("cdncontent/testblob.txt");
	            blob.UploadText("This is a test blob.");

	            //Set the Cache-Control header on the blob to specify your desired refresh interval.
	            blob.SetCacheControl("public, max-age=31536000");
	        }
	    }

	    public static class BlobExtensions
	    {
	        //A convenience method to set the Cache-Control header.
	        public static void SetCacheControl(this CloudBlob blob, string value)
	        {
	            blob.Properties.CacheControl = value;
	            blob.SetProperties();
	        }
	    }
	}

Vérifiez que votre objet blob est disponible via l'URL du CDN. Pour l'objet blob indiqué ci-dessus, l'URL ressemblerait à ceci :

	http://az1234.vo.msecnd.net/cdncontent/testblob.txt  

Si vous le souhaitez, vous pouvez utiliser un outil comme **wget** ou Fiddler pour examiner les détails de la demande et de la réponse.

##Voir aussi

[Comment gérer l’expiration des contenus de service cloud dans le réseau de distribution de contenu Azure (CDN)](./cdn-manage-expiration-of-cloud-service-content.md)

<!---HONumber=AcomDC_0511_2016-->