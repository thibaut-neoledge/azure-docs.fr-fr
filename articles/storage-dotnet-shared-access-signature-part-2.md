<properties linkid="manage-services-storage-net-shared-access-signature-part-2" urlDisplayName="" pageTitle="Create and use a SAS with the Blob Service | Microsoft Azure" metaKeywords="Azure blob, shared access signatures, stored access policy" description="Explore generating and using shared access signatures with the Blob service" metaCanonical="" services="storage" documentationCenter="" title="Part 2: Create and Use a SAS with the Blob Service" authors="tamram" solutions="" manager="mbaldwin" editor="cgronlun" />

Signatures d'accès partagé, partie 2 : création et utilisation d'une signature d'accès partagé avec le service BLOB
===================================================================================================================

La [partie 1](../storage-dotnet-shared-access-signature-part-1/) de ce didacticiel traitait des signatures d'accès partagé et indiquait les meilleures pratiques les concernant. La partie 2 indique comment générer et utiliser les signatures d'accès partagé avec le service BLOB Azure. Les exemples ont été écrits en C\# et utilisent la bibliothèque du client Azure Storage pour .NET. Les scénarios traités incluent les points suivants de l'utilisation des signatures d'accès partagé :

-   Génération d'une signature d'accès partagé sur un conteneur
-   Génération d'une signature d'accès partagé sur un objet blob
-   Création d'une stratégie d'accès stockée pour gérer les signatures sur les ressources d'un conteneur
-   Test des signatures d'accès partagé à partir d'une application cliente

À propos de ce didacticiel
==========================

Ce didacticiel traite spécifiquement de la création des signatures d'accès partagé pour les conteneurs et les objets blob en créant deux applications console. La première application console génère des signatures d'accès partagé sur un conteneur et un objet blob. Cette application connaît les clés de compte de stockage. La seconde application console, qui se comporte comme une application cliente, accède aux ressources du conteneur et de l'objet blob en utilisant les signatures d'accès partagé créées avec la première application. Cette application utilise les signatures d'accès partagé uniquement pour authentifier son accès aux ressources du conteneur et de l'objet blob. Elle ne connaît pas les clés de compte.

Partie 1 : création d'une application console pour générer des signatures d'accès partagé
=========================================================================================

Commencez par vérifier que la bibliothèque du client Azure Storage pour .NET (version 2.0) est installée. Vous pouvez installer le [package NuGet](http://nuget.org/packages/WindowsAzure.Storage/ "package NuGet") qui contient les assemblys les plus à jour pour la bibliothèque du client. Cette méthode est recommandée pour garantir que les correctifs les plus récents sont installés. Vous pouvez également télécharger la bibliothèque du client avec la version la plus récente du [Kit de développement logiciel (SDK) Azure pour .NET](http://www.windowsazure.com/en-us/downloads/).

Dans Visual Studio, créez une application console Windows et nommez-la **GenerateSharedAccessSignatures**. Ajoutez des références à **Microsoft.WindowsAzure.Configuration.dll** et **Microsoft.WindowsAzure.Storage.dll**, en utilisant l'une des méthodes suivantes :

-   Si vous souhaitez installer le package NuGet, commencez par installer [NuGet Package Manager Extension pour Visual Studio](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c). Dans Visual Studio, sélectionnez **Project | Manage NuGet Packages**, effectuez une recherche en ligne sur **Azure Storage** et suivez les instructions d'installation.
-   Vous pouvez également rechercher les assemblys dans votre installation du Kit de développement logiciel (SDK) Azure et y ajouter des références.

Au début du fichier Program.cs, ajoutez les instructions **using** suivantes :

    using System.IO;    
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;

Modifiez le fichier app.config pour qu'il contienne un paramètre de configuration avec une chaîne de connexion qui pointe vers votre compte de stockage. Votre fichier app.config ressemble à ceci :

    <configuration>
      <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
      </startup>
      <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey"/>
      </appSettings> 
    </configuration>

Génération d'un URI de signature d'accès partagé pour un conteneur
------------------------------------------------------------------

Pour commencer, ajoutons une méthode pour générer une signature d'accès partagé sur un nouveau conteneur. Dans ce cas, la signature n'est pas associée à une stratégie d'accès stockée et véhicule sur l'URI les informations concernant son heure d'expiration et les autorisations qu'elle accorde.

Ajoutons du code à la méthode **Main()** pour authentifier l'accès à votre compte de stockage et créer un conteneur :

    static void Main(string[] args)
    {
        //Analysez la chaîne de connexion et renvoyez une référence au compte de stockage.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));
        
        //Créez le client d'objet blob.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        
        //Obtenez une référence vers un conteneur à utiliser pour l'exemple de code ou créez-en une si elle n'existe pas déjà.
        CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
        container.CreateIfNotExists();
        
        //Insérez des appels aux méthodes créées ci-après...
        
        //Demandez une entrée utilisateur avant la fermeture de la fenêtre de la console.
        Console.ReadLine();
    }

Ajoutez ensuite une nouvelle méthode qui génère la signature d'accès partagé pour le conteneur et renvoie l'URI de la signature :

    static string GetContainerSasUri(CloudBlobContainer container)
    {
        //Définissez l'heure d'expiration et les autorisations pour le conteneur.
        //Ici, l'heure de début n'est pas spécifiée et la signature d'accès partagé est valide immédiatement.
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
        sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4);
        sasConstraints.Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List;
        
        //Générez la signature d'accès partagé sur le conteneur en définissant les contraintes directement sur la signature.
        string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);
        
        //Renvoyez la chaîne de l'URI pour le conteneur avec le jeton de signature d'accès partagé.
        return container.Uri + sasContainerToken;
    }

Ajoutez les lignes suivantes à la fin de la méthode **Main()**, avant l'appel à **Console.ReadLine()**, pour appeler **GetContainerSasUri()** et écrire l'URI de la signature sur la fenêtre de la console :

    //Générez un URI de signature d'accès partagé pour le conteneur, sans stratégie d'accès stockée.
    Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
    Console.WriteLine();

Compilez et exécutez pour renvoyer l'URI de la signature d'accès partagé pour le nouveau conteneur. L'URI ressemble à cela :

https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2013-04-13T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3D

Une fois que l'exécution du code est lancée, la signature d'accès partagé que vous avez créée sur le conteneur est valide pendant quatre heures. La signature accorde une autorisation client pour créer une liste d'objets blob dans le conteneur et écrire un nouvel objet blob dans le conteneur.

Génération d'un URI de signature d'accès partagé pour un objet blob
-------------------------------------------------------------------

Nous allons ensuite écrire un code similaire pour créer un objet blob dans le conteneur et générer une signature d'accès partagé pour lui. Cette signature d'accès partagé n'est pas associée à une stratégie d'accès stockée et inclut l'heure de début, l'heure d'expiration et les informations d'autorisation sur l'URI.

Ajoutez une nouvelle méthode qui crée un objet blob et y écrit du texte, puis génère une signature d'accès partagé et renvoie l'URI de la signature :

    static string GetBlobSasUri(CloudBlobContainer container)
    {
        //Obtenez une référence à un objet blob dans le conteneur.
        CloudBlockBlob blob = container.GetBlockBlobReference("sasblob.txt");
        
        //Téléchargez du texte dans l'objet blob. Si l'objet blob n'existe pas encore, il est créé. 
        //Si l'objet blob existe, son contenu est remplacé.
        string blobContent = "This blob will be accessible to clients via a Shared Access Signature.";
        MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        ms.Position = 0;
        using (ms)
        {
            blob.UploadFromStream(ms);
        }
        
        //Définissez l'heure d'expiration et les autorisations pour l'objet blob.
        //Ici, l'heure de début est spécifiée sur quelques minutes plus tôt pour résoudre les problèmes de variation d'horloges.
        //La signature d'accès partagé est valide immédiatement.
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
        sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
        sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4);
        sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;
        
        //Générez la signature d'accès partagé sur l'objet blob en définissant les contraintes directement sur la signature.
        string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);
        
        //Renvoyez la chaîne de l'URI pour le conteneur avec le jeton de signature d'accès partagé.
        return blob.Uri + sasBlobToken;
    }

Ajoutez les lignes suivantes à la fin de la méthode **Main()**, avant l'appel à **Console.ReadLine()**, pour appeler **GetBlobSasUri()** et écrire l'URI de la signature d'accès partagé sur la fenêtre de la console :

    //Générez un URI de signature d'accès partagé pour un objet blob du conteneur, sans stratégie d'accès stockée.
    Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
    Console.WriteLine();

Compilez et exécutez pour renvoyer l'URI de la signature d'accès partagé pour le nouvel objet blob. L'URI ressemble à cela :

https://storageaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-12T23%3A37%3A08Z&se=2013-04-13T00%3A12%3A08Z&sr=b&sp=rw&sig=dF2064yHtc8RusQLvkQFPItYdeOz3zR8zHsDMBi4S30%3D

Création d'une stratégie d'accès stockée sur le conteneur
---------------------------------------------------------

Créons une stratégie d'accès stockée sur le conteneur pour définir les contraintes des signatures d'accès partagé qui y sont associées.

Dans les exemples précédents, nous avons spécifié l'heure de début (implicitement ou explicitement), l'heure d'expiration et les autorisations sur l'URI de la signature d'accès partagé lui-même. Dans les exemples suivants, nous allons spécifier cela sur la stratégie d'accès stockée, pas sur la signature. Cela nous permettra de modifier ces contraintes sans devoir émettre de nouveau la signature d'accès partagé.

Notez qu'il est possible de définir un certain nombre de contraintes sur la signature et le reste sur la stratégie d'accès stockée. Toutefois, l'heure de début, l'heure d'expiration et les autorisations doivent être définies sur l'un ou l'autre emplacement. Ainsi, vous ne pouvez pas spécifier des autorisations à la fois sur la signature d'accès partagé et sur la stratégie d'accès stockée.

Ajoutez une nouvelle méthode qui crée une stratégie d'accès stockée et renvoie le nom de la stratégie :

    static void CreateSharedAccessPolicy(CloudBlobClient blobClient, CloudBlobContainer container, string policyName)
    {
        //Créez une stratégie d'accès stockée et définissez ses contraintes.
        SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
            Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List
        };
        
        //Obtenez les autorisations du conteneur.
        BlobContainerPermissions permissions = new BlobContainerPermissions();
        
        //Ajoutez la nouvelle stratégie aux autorisations du conteneur.
        permissions.SharedAccessPolicies.Clear();
        permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
        container.SetPermissions(permissions);
    }

Ajoutez les lignes suivantes à la fin de la méthode **Main()**, avant l'appel à **Console.ReadLine()**, pour appeler la méthode **CreateSharedAccessPolicy()** :

    //Créez une stratégie d'accès sur le conteneur, qui peut éventuellement servir à fournir des contraintes pour 
    //les signatures d'accès partagé sur le conteneur et l'objet blob.
    string sharedAccessPolicyName = "tutorialpolicy";
    CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);

Génération d'un URI de signature d'accès partagé sur le conteneur qui utilise une stratégie d'accès
---------------------------------------------------------------------------------------------------

Créons maintenant une autre signature d'accès partagé sur le conteneur que nous avons créé précédemment, mais en associant la signature à la stratégie d'accès que nous avons créée dans l'exemple précédent.

Ajoutez une nouvelle méthode pour générer une autre signature d'accès partagé sur le conteneur :

    static string GetContainerSasUriWithPolicy(CloudBlobContainer container, string policyName)
    {
        //Générez la signature d'accès partagé sur le conteneur. Ici, toutes les contraintes pour la 
        //signature d'accès partagé sont spécifiées sur la stratégie d'accès stockée.
        string sasContainerToken = container.GetSharedAccessSignature(null, policyName);
        
        //Renvoyez la chaîne de l'URI pour le conteneur avec le jeton de signature d'accès partagé.
        return container.Uri + sasContainerToken;
    }

Ajoutez les lignes suivantes à la fin de la méthode **Main()**, avant l'appel à **Console.ReadLine()**, pour appeler la méthode **GetContainerSasUriWithPolicy** :

    //Générez un URI de signature d'accès partagé pour le conteneur, en utilisant une stratégie d'accès stockée pour définir les contraintes sur la signature.
    Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

Génération d'un URI de signature d'accès partagé sur l'objet blob qui utilise une stratégie d'accès
---------------------------------------------------------------------------------------------------

Pour terminer, ajoutons une méthode similaire pour créer un autre objet blob et générer une signature d'accès partagé associée à une stratégie d'accès.

Ajoutez une nouvelle méthode pour créer un objet blob et générer une signature d'accès partagé :

    static string GetBlobSasUriWithPolicy(CloudBlobContainer container, string policyName)
    {
        //Obtenez une référence à un objet blob dans le conteneur.
        CloudBlockBlob blob = container.GetBlockBlobReference("sasblobpolicy.txt");
        
        //Téléchargez du texte dans l'objet blob. Si l'objet blob n'existe pas encore, il est créé. 
        //Si l'objet blob existe, son contenu est remplacé.
        string blobContent = "This blob will be accessible to clients via a shared access signature. " + 
        "A stored access policy defines the constraints for the signature.";
        MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        ms.Position = 0;
        using (ms)
        {
            blob.UploadFromStream(ms);
        }
        
        //Générez la signature d'accès partagé sur l'objet blob.
        string sasBlobToken = blob.GetSharedAccessSignature(null, policyName);
        
        //Renvoyez la chaîne de l'URI pour le conteneur avec le jeton de signature d'accès partagé.
        return blob.Uri + sasBlobToken;
    }

Ajoutez les lignes suivantes à la fin de la méthode **Main()**, avant l'appel à **Console.ReadLine()**, pour appeler la méthode **GetBlobSasUriWithPolicy** :

    //Générez un URI de signature d'accès partagé pour un objet blob du conteneur, en utilisant une stratégie d'accès stockée pour définir les contraintes sur la signature.
    Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

La méthode **Main()** doit maintenant ressembler à ceci. Lancez-la pour écrire les URI de signature d'accès partagé sur la fenêtre de la console, puis copiez et collez-les dans un fichier texte pour les utiliser dans la deuxième partie de ce didacticiel.

    static void Main(string[] args)
    {
        //Analysez la chaîne de connexion et renvoyez une référence au compte de stockage.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));
        
        //Créez le client d'objet blob.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        
        //Obtenez une référence vers un conteneur à utiliser pour l'exemple de code ou créez-en une si elle n'existe pas déjà.
        CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
        container.CreateIfNotExists();
        
        //Générez un URI de signature d'accès partagé pour le conteneur, sans stratégie d'accès stockée.
        Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
        Console.WriteLine();
        
        //Générez un URI de signature d'accès partagé pour un objet blob du conteneur, sans stratégie d'accès stockée.
        Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
        Console.WriteLine();
        
        //Créez une stratégie d'accès sur le conteneur, qui peut éventuellement servir à fournir des contraintes pour 
        //les signatures d'accès partagé sur le conteneur et l'objet blob.
        string sharedAccessPolicyName = "tutorialpolicy";
        CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);
        
        //Générez un URI de signature d'accès partagé pour le conteneur, en utilisant une stratégie d'accès stockée pour définir les contraintes sur la signature.
        Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
        Console.WriteLine();
        
        //Générez un URI de signature d'accès partagé pour un objet blob du conteneur, en utilisant une stratégie d'accès stockée pour définir les contraintes sur la signature.
        Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
        Console.WriteLine();
        
        Console.ReadLine();
    }

Lorsque vous exécutez l'application console GenerateSharedAccessSignatures, la sortie est semblable à ce qui suit dans la fenêtre de la console. Il s'agit des signatures d'accès partagé que vous allez utiliser dans la partie 2 du didacticiel.

![sas-console-output-1](./media/storage-dotnet-shared-access-signature-part-2/sas-console-output-1.PNG)

Partie 2 : création d'une application console pour tester les signatures d'accès partagé
========================================================================================

Pour tester les signatures d'accès partagé créées dans les exemples précédents, nous allons créer une deuxième application console qui utilise les signatures afin d'exécuter les opérations sur le conteneur et un objet blob.

Notez que si plus de quatre heures se sont écoulées depuis la fin de la première partie de ce didacticiel, les signatures que vous avez générées avec une heure d'expiration fixée sur quatre heures ne sont plus valides. De même, les signatures associées à la stratégie d'accès stockée expirent après 10 heures. Si l'un ou l'autre de ces intervalles s'est écoulé, exécutez le code dans la première application console pour générer de nouvelles signatures d'accès partagé pour la seconde partie du didacticiel.

Dans Visual Studio, créez une application console Windows et nommez-la **ConsumeSharedAccessSignatures**. Ajoutez des références à **Microsoft.WindowsAzure.Configuration.dll** et **Microsoft.WindowsAzure.Storage.dll**, comme précédemment.

Au début du fichier Program.cs, ajoutez les instructions **using** suivantes :

    using System.IO;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;

Dans le corps de la méthode **Main()**, ajoutez les constantes suivantes et mettez leurs valeurs à jour dans les signatures d'accès partagé générées dans la partie 1 du didacticiel.

    static void Main(string[] args)
    {
        string containerSAS = "<your container SAS>";
        string blobSAS = "<your blob SAS>";
        string containerSASWithAccessPolicy = "<your container SAS with access policy>";
        string blobSASWithAccessPolicy = "<your blob SAS with access policy>";
    }

Ajout d'une méthode pour tester les opérations sur le conteneur avec une signature d'accès partagé
--------------------------------------------------------------------------------------------------

Nous allons ensuite ajouter une méthode qui teste des opérations représentatives sur le conteneur avec une signature d'accès partagé sur le conteneur. Notez que la signature d'accès partagé permet de renvoyer une référence au conteneur pour authentifier l'accès au conteneur sur la base de la signature uniquement.

Ajoutez la méthode suivante au fichier Program.cs :

    static void UseContainerSAS(string sas)
    {
        //Essayez d'exécuter les opérations sur le conteneur avec la signature d'accès partagé fournie.

        //Renvoyez une référence au conteneur avec l'URI de la signature d'accès partagé.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(sas));

        //Créez une liste pour stocker les URI des objets blob renvoyés par une opération de création de liste sur le conteneur.
        List<Uri> blobUris = new List<Uri>();

        try
        {
            //Opération d'écriture : écrivez un nouvel objet blob sur le conteneur. 
            CloudBlockBlob blob = container.GetBlockBlobReference("blobCreatedViaSAS.txt");
            string blobContent = "This blob was created with a shared access signature granting write permissions to the container. ";
            MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
            msWrite.Position = 0;
            using (msWrite)
            {
                blob.UploadFromStream(msWrite);
            }
            Console.WriteLine("Write operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Write operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        try
        {
            //Opération de création de liste : Répertorie les objets blob dans le conteneur, y compris celui qui vient d'être ajouté.
            foreach (ICloudBlob blobListing in container.ListBlobs())
            {
                blobUris.Add(blobListing.Uri);
            }
            Console.WriteLine("List operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("List operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        try
        {
            //Opération de lecture : Obtenez une référence pointant vers l'un des objets blob du conteneur et lisez-la. 
            CloudBlockBlob blob = container.GetBlockBlobReference(blobUris[0].ToString());
            MemoryStream msRead = new MemoryStream();
            msRead.Position = 0;
            using (msRead)
            {
                blob.DownloadToStream(msRead);
                Console.WriteLine(msRead.Length);
            }
            Console.WriteLine("Read operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Read operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }
        Console.WriteLine();

        try
        {
            //Opération de suppression : Supprimez un objet blob du conteneur.
            CloudBlockBlob blob = container.GetBlockBlobReference(blobUris[0].ToString());
            blob.Delete();
            Console.WriteLine("Delete operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Delete operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }        
    }

Mettez à jour la méthode **Main()** pour appeler **UseContainerSAS()** avec les deux signatures d'accès partagé que vous avez créées sur le conteneur :

    static void Main(string[] args)
    {
        string containerSAS = "<your container SAS>";
        string blobSAS = "<your blob SAS>";
        string containerSASWithAccessPolicy = "<your container SAS with access policy>";
        string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

        //Appelez les méthodes de test avec les signatures d'accès partagé créées sur le conteneur, avec et sans la stratégie d'accès.
        UseContainerSAS(containerSAS);
        UseContainerSAS(containerSASWithAccessPolicy); 
        
        //Appelez les méthodes de test avec les signatures d'accès partagé créées sur l'objet blob, avec et sans la stratégie d'accès.
        UseBlobSAS(blobSAS);
        UseBlobSAS(blobSASWithAccessPolicy);

        Console.ReadLine();
    }

Ajout d'une méthode pour tester les opérations sur l'objet blob avec une signature d'accès partagé
--------------------------------------------------------------------------------------------------

Pour terminer, nous allons ajouter une méthode qui teste des opérations représentatives sur l'objet blob avec une signature d'accès partagé sur l'objet blob. Ici, nous utilisons le constructeur **CloudBlockBlob(String)**, en passant la signature d'accès partagé, pour renvoyer une référence vers l'objet blob. Aucune autre authentification n'est nécessaire, car l'opération est basée uniquement sur la signature.

Ajoutez la méthode suivante au fichier Program.cs :

    static void UseBlobSAS(string sas)
    {
        //Essayez d'exécuter les opérations sur l'objet blob avec la signature d'accès partagé fournie.

        //Renvoyez une référence à l'objet blob avec l'URI de la signature d'accès partagé.
        CloudBlockBlob blob = new CloudBlockBlob(new Uri(sas));

        try
        {
            //Opération d'écriture : écrivez un nouvel objet blob sur le conteneur. 
            string blobContent = "This blob was created with a shared access signature granting write permissions to the blob. ";
            MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
            msWrite.Position = 0;
            using (msWrite)
            {
                blob.UploadFromStream(msWrite);
            }
            Console.WriteLine("Write operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Write operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        try
        {
            //Opération de lecture : Lisez le contenu de l'objet blob.
            MemoryStream msRead = new MemoryStream();
            using (msRead)
            {
                blob.DownloadToStream(msRead);
                msRead.Position = 0;
                using (StreamReader reader = new StreamReader(msRead, true))
                {
                    string line;
                    while ((line = reader.ReadLine()) != null)
                    {
                        Console.WriteLine(line);
                    }
                }
            }
            Console.WriteLine("Read operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Read operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        try
        {
            //Opération de suppression : Supprimez l'objet blob.
            blob.Delete();
            Console.WriteLine("Delete operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Delete operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }        
    }

Mettez à jour la méthode **Main()** pour appeler **UseBlobSAS()** avec les deux signatures d'accès partagé que vous avez créées sur l'objet blob :

    static void Main(string[] args)
    {
        string containerSAS = "<your container SAS>";
        string blobSAS = "<your blob SAS>";
        string containerSASWithAccessPolicy = "<your container SAS with access policy>";
        string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

        //Appelez les méthodes de test avec les signatures d'accès partagé créées sur le conteneur, avec et sans la stratégie d'accès.
        UseContainerSAS(containerSAS);
        UseContainerSAS(containerSASWithAccessPolicy); 
        
        //Appelez les méthodes de test avec les signatures d'accès partagé créées sur l'objet blob, avec et sans la stratégie d'accès.
        UseBlobSAS(blobSAS);
        UseBlobSAS(blobSASWithAccessPolicy);

        Console.ReadLine();
    }

Exécutez l'application console et observez la sortie pour connaître les opérations autorisées en fonction des signatures. La sortie dans la fenêtre de la console ressemble à ceci :

![sas-console-output-2](./media/storage-dotnet-shared-access-signature-part-2/sas-console-output-2.PNG)

Étapes suivantes
================

[Signatures d'accès partagé, partie 1 : présentation du modèle SAP](../storage-dotnet-shared-access-signature-part-1/)

[Gestion de l'accès aux ressources de stockage Azure](http://msdn.microsoft.com/en-us/library/windowsazure/ee393343.aspx)

[Délégation de l'accès avec une signature d'accès partagé (API REST)](http://msdn.microsoft.com/en-us/library/windowsazure/ee395415.aspx)

[Présentation des signatures d'accès partagé de table et de file d'attente](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)

