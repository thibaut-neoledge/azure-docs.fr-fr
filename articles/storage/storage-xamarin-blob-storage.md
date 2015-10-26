<properties 
	pageTitle="Utilisation du stockage d’objets blob à partir de Xamarin (version préliminaire) | Microsoft Azure" 
	description="La bibliothèque cliente de stockage Azure pour Xamarin permet aux développeurs d’utiliser un code base C# partagé pour créer des applications iOS, Android et Windows Store avec leurs interfaces utilisateur natives. Ce didacticiel montre comment utiliser Xamarin pour créer une application Android qui utilise le stockage d’objets blob Azure." 
	services="storage" 
	documentationCenter="xamarin" 
	authors="tamram" 
	manager="carolz" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/03/2015" 
	ms.author="tamram"/>

# Utilisation du stockage d’objets blob à partir de Xamarin (version préliminaire)

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Vue d'ensemble

Xamarin permet aux développeurs d’utiliser un code base C# partagé pour créer des applications iOS, Android et Windows Store avec leurs interfaces utilisateur natives. La bibliothèque cliente de stockage Azure pour Xamarin est une bibliothèque en version préliminaire. Il convient de noter qu’elle est susceptible de faire l’objet de modifications.

Ce didacticiel vous montre comment utiliser le stockage d’objets blob Azure avec une application Xamarin Android. Pour en savoir plus sur Azure Storage avant de vous plonger dans le code, consultez la section [Étapes suivantes](#next-steps) à la fin de ce document.

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Création d’une signature d’accès partagé

Lors du développement avec la bibliothèque cliente de stockage Azure pour Xamarin, vous ne pouvez pas authentifier l’accès à un compte de stockage Azure à l’aide des clés d’accès de votre compte. Cela permet d’empêcher la distribution de vos informations d’identification de compte à des utilisateurs qui risquent de télécharger votre application. Nous préférons encourager l’utilisation de signatures d’accès partagé (SAP), qui n’exposent pas les informations d’identification de votre compte.

Dans ce guide, nous allons utiliser Azure PowerShell pour créer un jeton SAP. Nous allons ensuite créer une application Xamarin qui utilise la signature d’accès partagé créée.

Tout d’abord, vous devez installer Azure PowerShell. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md#Install).

Ensuite, ouvrez Azure PowerShell et exécutez les commandes suivantes. N’oubliez pas de remplacer `ACCOUNT_NAME` et `ACCOUNT_KEY== ` par les informations d’identification de votre compte de stockage. Remplacez `CONTAINER_NAME` par un nom de votre choix.

    PS C:\> $context = New-AzureStorageContext -StorageAccountName "ACCOUNT_NAME" -StorageAccountKey "ACCOUNT_KEY=="
	PS C:\> New-AzureStorageContainer CONTAINER_NAME -Permission Off -Context $context
	PS C:\> $now = Get-Date
	PS C:\> New-AzureStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri

L’URI de la signature d’accès partagé du nouveau conteneur doit être semblable à ce qui suit :

	https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2013-04-13T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss

La signature d’accès partagé que vous avez créée sur le conteneur est valide pour le jour suivant. La signature accorde des droits d’accès complets (*par exemple*, lecture, écriture, suppression et liste) aux objets blob du conteneur.

Pour plus d’informations sur les signatures d’accès partagé, consultez le [didacticiel sur les signatures d’accès partagé](storage-dotnet-shared-access-signature-part-2.md).

## Création d’une application Xamarin

Pour ce didacticiel, nous allons créer l’application Xamarin dans Visual Studio. Procédez comme suit pour créer l’application :

1. Téléchargez et installez [Visual Studio](https://www.visualstudio.com/).
2. Téléchargez et installez [Xamarin](http://xamarin.com/platform).
3. Ouvrez Visual Studio et sélectionnez **Fichier > Nouveau > Projet > Android > Application vide (Android)**.
4. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur votre projet, puis sélectionnez **Gérer les packages NuGet**. Ensuite, recherchez **Azure Storage** et installez la **version préliminaire d’Azure Storage 4.4.0**.

Vous devez maintenant avoir une application qui vous permet de cliquer sur un bouton et d’incrémenter un compteur.

## Utilisez la signature d’accès partagé pour effectuer des opérations de conteneur.

Ensuite, ajoutez le code pour effectuer une série d’opérations de conteneur à l’aide de l’URI SAP que vous avez créé.

Tout d’abord, ajoutez les instructions **using** suivantes :

	using System.IO;
	using System.Text;
	using System.Threading.Tasks;
	using Microsoft.WindowsAzure.Storage.Blob;


Ensuite, ajoutez une ligne à votre jeton SAP. Remplacez la chaîne `"SAS_URI"` par l’URI SAP que vous avez créé dans Azure PowerShell. Ajoutez ensuite une ligne pour un appel à la méthode `UseContainerSAS` que nous allons créer ci-dessous. Notez que le mot clé **async** a été ajouté avant le délégué.


	public class MainActivity : Activity
	{
    	int count = 1;
    	string sas = "SAS_URI";
    	protected override void OnCreate(Bundle bundle)
    	{
        	base.OnCreate(bundle);

        	// Set our view from the "main" layout resource
        	SetContentView(Resource.Layout.Main);

        	// Get our button from the layout resource, and attach an event to it
        	Button button = FindViewById<Button>(Resource.Id.MyButton);

        	button.Click += async delegate	{
             	button.Text = string.Format("{0} clicks!", count++);
             	await UseContainerSAS(sas);
         	};
     }

Ajoutez la nouvelle méthode `UseContainerSAS` sous la méthode `OnCreate`.

	static async Task UseContainerSAS(string sas)
	{
    	//Try performing container operations with the SAS provided.

    	//Return a reference to the container using the SAS URI.
    	CloudBlobContainer container = new CloudBlobContainer(new Uri(sas));
    	string date = DateTime.Now.ToString();
    	try
    	{
        	//Write operation: write a new blob to the container.
        	CloudBlockBlob blob = container.GetBlockBlobReference("sasblob_" + date + ".txt");

        	string blobContent = "This blob was created with a shared access signature granting write permissions to the container. ";
        	MemoryStream msWrite = new
        	MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        	msWrite.Position = 0;
        	using (msWrite)
         	{
             	await blob.UploadFromStreamAsync(msWrite);
         	}
         	Console.WriteLine("Write operation succeeded for SAS " + sas);
         	Console.WriteLine();
     	}
     	catch (Exception e)
     	{
        	Console.WriteLine("Write operation failed for SAS " + sas);
        	Console.WriteLine("Additional error information: " + e.Message);
        	Console.WriteLine();
     	}
     	try
     	{
        	//Read operation: Get a reference to one of the blobs in the container and read it.
        	CloudBlockBlob blob = container.GetBlockBlobReference("sasblob_” + date + “.txt");
        	string data = await blob.DownloadTextAsync();

        	Console.WriteLine("Read operation succeeded for SAS " + sas);
        	Console.WriteLine("Blob contents: " + data);
     	}
     	catch (Exception e)
     	{
        	Console.WriteLine("Additional error information: " + e.Message);
       		Console.WriteLine("Read operation failed for SAS " + sas);
        	Console.WriteLine();
     	}
     	Console.WriteLine();
     	try
     	{
        	//Delete operation: Delete a blob in the container.
         	CloudBlockBlob blob = container.GetBlockBlobReference("sasblob_” + date + “.txt");
         	await blob.DeleteAsync();

         	Console.WriteLine("Delete operation succeeded for SAS " + sas);
         	Console.WriteLine();
     	}
     	catch (Exception e)
     	{
        	Console.WriteLine("Delete operation failed for SAS " + sas);
        	Console.WriteLine("Additional error information: " + e.Message);
        	Console.WriteLine();
     	}
	}

## Exécution de l'application

Vous pouvez maintenant exécuter cette application dans un émulateur ou un appareil Android.

Bien que la prise en main se concentre sur Android, vous pouvez aussi utiliser la méthode `UseContainerSAS` dans vos applications iOS ou Windows Store. Xamarin permet également aux développeurs de créer des applications Windows Phone. Néanmoins, notre bibliothèque ne prend pas encore cela en charge.

## Étapes suivantes

Dans ce didacticiel, vous avez appris à utiliser le stockage d’objets blob Azure avec une application Xamarin Android. En guise d’exercice supplémentaire, vous pouvez appliquer un modèle similaire afin de créer un jeton SAP pour une file d’attente ou table Azure.

Pour en savoir plus sur les objets blob, les tables et les files d’attente, suivez les liens ci-après :

[Introduction à Microsoft Azure Storage](storage-introduction.md)  
[Utilisation du stockage d’objets blob à partir de .NET](storage-dotnet-how-to-use-blobs.md)  
[Utilisation du stockage de tables à partir de .NET](storage-dotnet-how-to-use-tables.md)  
[Utilisation du stockage de files d’attente à partir de .NET](storage-dotnet-how-to-use-queues.md)
 

<!---HONumber=Oct15_HO3-->