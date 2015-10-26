<properties
	pageTitle="Application locale avec stockage d’objets blob (Java) | Microsoft Azure"
	description="Découvrez comment créer une application console qui charge une image dans Azure, puis l'affiche dans votre navigateur. Les exemples de code sont écrits en Java."
	services="storage"
	documentationCenter="java"
	authors="rmcmurray"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="08/31/2015" 
	ms.author="robmcm"/>

# Application locale avec stockage d’objets blob

## Vue d'ensemble

L’exemple suivant montre comment utiliser le stockage Azure pour stocker des images dans Azure. Le code figurant dans cet article concerne une application de console qui charge une image dans Azure, puis crée un fichier HTML affichant cette image dans votre navigateur.

## Composants requis

- Le Kit de développement logiciel Java (JDK) version 1.6 ou ultérieure est installé.
- Le Kit de développement logiciel (SDK) Azure est installé.
- L'archive Java (JAR) des bibliothèques Azure pour Java et les dépendances applicables JAR sont installées et se trouvent dans le chemin d'accès de build utilisé par votre compilateur Java. Pour plus d’informations sur l’installation des bibliothèques Azure pour Java, consultez la page [Téléchargement du Kit de développement logiciel (SDK) Azure pour Java][].
- Un compte de stockage Azure a été configuré. Le nom et la clé du compte de stockage sont utilisés par le code figurant dans cet article. Consultez la page [Création d'un compte de stockage] pour des informations sur la création d'un compte de stockage et la page [Gestion des comptes de stockage][] pour des informations sur la récupération de la clé de compte.
- Vous avez créé un fichier image local nommé et stocké sous le chemin d'accès c:\\myimages\\image1.jpg. Vous pouvez également modifier le constructeur **FileInputStream** dans l'exemple pour utiliser un chemin d'accès à l'image et un nom de fichier différents.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## Utilisation du stockage d’objets blob Azure pour charger un fichier

La procédure présentée ici détaille chaque étape. Si vous souhaitez la passer, le code est intégralement présenté plus avant dans cet article.

Commencez le code en important les classes de stockage de base Azure, les classes du client d’objets blob Azure, les classes d’E/S Java et la classe **URISyntaxException** :

    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;
    import java.io.*;
    import java.net.URISyntaxException;

Déclarez une classe nommée **StorageSample**, puis incluez le crochet ouvrant **{**.

    public class StorageSample {

Dans la classe **StorageSample**, déclarez une variable de chaîne qui contient le protocole du point de terminaison par défaut, le nom de votre compte de stockage et votre clé d'accès de stockage, comme spécifié dans votre compte de stockage Azure. Remplacez les valeurs des espaces réservés **your\_account\_name** et **your\_account\_key** par respectivement votre nom et votre clé de compte.

    public static final String storageConnectionString =
           "DefaultEndpointsProtocol=http;" +
               "AccountName=your_account_name;" +
               "AccountKey=your_account_name";

Ajoutez votre déclaration pour **main**, incluez un bloc **try** ainsi que les crochets ouvrants nécessaires **{**.

    public static void main(String[] args)
    {
        try
        {

Déclarez les variables du type suivant (les descriptions se rapportent à la façon dont elles sont utilisées dans cet exemple) :

-   **CloudStorageAccount** : permet d’initialiser le compte avec le nom et la clé de votre compte de stockage Azure, et de créer l’objet client du blob.
-   **CloudBlobClient** : permet d’accéder au service BLOB.
-   **CloudBlobContainer** : permet de créer un conteneur d’objets blob, de répertorier les objets blob dans le conteneur et de supprimer ce dernier.
-   **CloudBlockBlob** : permet de charger un fichier image local dans le conteneur.

<!-- -->

    CloudStorageAccount account;
    CloudBlobClient serviceClient;
    CloudBlobContainer container;
    CloudBlockBlob blob;

Attribuez une valeur à la variable **account**.

    account = CloudStorageAccount.parse(storageConnectionString);

Attribuez une valeur à la variable **serviceClient**.

    serviceClient = account.createCloudBlobClient();

Attribuez une valeur à la variable **container**. Nous obtenons une référence pointant vers un conteneur nommé **gettingstarted**.

    // Container name must be lower case.
    container = serviceClient.getContainerReference("gettingstarted");

Création du conteneur. Cette méthode crée le conteneur s’il n’existe pas (et renvoie **true**). Si le conteneur existe, elle renvoie **false**. La méthode **create** (qui renvoie une erreur si le conteneur existe déjà) constitue une alternative à **createIfNotExists**.

    container.createIfNotExists();

Définissez un accès anonyme pour le conteneur.

    // Set anonymous access on the container.
    BlobContainerPermissions containerPermissions;
    containerPermissions = new BlobContainerPermissions();
    containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
    container.uploadPermissions(containerPermissions);

Obtenez une référence pointant vers l'objet blob de blocs qui représente l'objet blob dans le stockage Azure.

    blob = container.getBlockBlobReference("image1.jpg");

Utilisez le constructeur **File** pour obtenir une référence pointant vers le fichier créé en local que vous allez charger. Assurez-vous d’avoir créé ce fichier avant d’exécuter le code.

    File fileReference = new File ("c:\\myimages\\image1.jpg");

Chargez le fichier local en appelant la méthode **CloudBlockBlob.upload**. Le premier paramètre de la méthode **CloudBlockBlob.upload** est un objet **FileInputStream** qui représente le fichier local à charger sur le stockage Azure. Le deuxième paramètre est la taille du fichier en octets.

    blob.upload(new FileInputStream(fileReference), fileReference.length());

Appelez une fonction d’assistance nommée **MakeHTMLPage** pour créer une page HTML de base contenant un élément **&lt;image&gt;** dont la source est l’objet blob situé maintenant dans votre compte de stockage Azure. Le code de **MakeHTMLPage** est abordé plus avant dans cet article.

    MakeHTMLPage(container);

Imprimez un message d’état et des informations sur la page HTML créée.

    System.out.println("Processing complete.");
    System.out.println("Open index.html to see the images stored in your storage account.");

Fermez le bloc **try** en insérant une parenthèse fermante : **}**

Gérez les exceptions suivantes :

-   **FileNotFoundException** : peut être émise par les constructeurs **FileInputStream** et **FileOutputStream**.
-   **StorageException** : peut être émise par la bibliothèque de stockage cliente Azure.
-   **URISyntaxException** : peut être émise par la méthode **ListBlobItem.getUri**.
-   **Exception** : traitement d’une exception générique.

<!-- -->

    catch (FileNotFoundException fileNotFoundException)
    {
        System.out.print("FileNotFoundException encountered: ");
        System.out.println(fileNotFoundException.getMessage());
        System.exit(-1);
    }
    catch (StorageException storageException)
    {
        System.out.print("StorageException encountered: ");
        System.out.println(storageException.getMessage());
        System.exit(-1);
    }
    catch (URISyntaxException uriSyntaxException)
    {
        System.out.print("URISyntaxException encountered: ");
        System.out.println(uriSyntaxException.getMessage());
        System.exit(-1);
    }
    catch (Exception e)
    {
        System.out.print("Exception encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

Fermez **main** en insérant une parenthèse fermante : **}**

Pour créer une page HTML de base, créez une méthode nommée **MakeHTMLPage**. Cette méthode dispose d'un paramètre du type **CloudBlobContainer** qui est utilisé pour effectuer une itération dans la liste des objets blob chargés. Cette méthode lève des exceptions du type **FileNotFoundException** pouvant être levées par le constructeur **FileOutputStream** et du type **URISyntaxException** pouvant être levées par la méthode **ListBlobItem.getUri**. Incluez le crochet ouvrant **{**.

    public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
    {

Créez un fichier local nommé **index.html**.

    PrintStream stream;
    stream = new PrintStream(new FileOutputStream("index.html"));

Dans le fichier local, ajoutez du contenu aux éléments **&lt;html&gt;**, **&lt;header&gt;** et **&lt;body&gt;**.

    stream.println("<html>");
    stream.println("<header/>");
    stream.println("<body>");

Effectuez une itération dans la liste des objets blob chargés. Pour chaque objet blob, créez dans la page HTML un élément **&lt;img&gt;** dont l’attribut **src** est envoyé à l’URI de l’objet blob tel qu’il existe dans votre compte de stockage Azure. Dans cet exemple, vous avez ajouté une seule image, mais si vous en ajoutiez plus, ce code effectuerait une itération pour chacune d’entre elles.

Par souci de simplification, cet exemple part du principe que chaque objet blob chargé est une image. Si vous avez chargé des objets blob qui ne sont pas des images ou des objets blob de pages au lieu d’objets blob de blocs, modifiez le code en conséquence.

    // Enumerate the uploaded blobs.
    for (ListBlobItem blobItem : container.listBlobs()) {
    // List each blob as an <img> element in the HTML body.
    stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
    }

Fermez les éléments **&lt;body&gt;** et **&lt;html&gt;**.

    stream.println("</body>");
    stream.println("</html>");

Fermez le fichier local.

    stream.close();

Fermez **MakeHTMLPage** en insérant une parenthèse fermante : **}**

Fermez **StorageSample** en insérant une parenthèse fermante : **}**

Voici le code complet pour cet exemple. N'oubliez pas de modifier les valeurs des espaces réservés **your\_account\_name** et **your\_account\_key** pour utiliser respectivement votre nom et votre clé de compte.

    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;
    import java.io.*;
    import java.net.URISyntaxException;

    // Create an image, c:\myimages\image1.jpg, prior to running this sample.
    // Alternatively, change the value used by the FileInputStream constructor
    // to use a different image path and file that you have already created.
    public class StorageSample {

        public static final String storageConnectionString =
                "DefaultEndpointsProtocol=http;" +
                       "AccountName=your_account_name;" +
                       "AccountKey=your_account_name";

        public static void main(String[] args) {
            try {
                CloudStorageAccount account;
                CloudBlobClient serviceClient;
                CloudBlobContainer container;
                CloudBlockBlob blob;

                account = CloudStorageAccount.parse(storageConnectionString);
                serviceClient = account.createCloudBlobClient();
                // Container name must be lower case.
                container = serviceClient.getContainerReference("gettingstarted");
                container.createIfNotExists();

                // Set anonymous access on the container.
                BlobContainerPermissions containerPermissions;
                containerPermissions = new BlobContainerPermissions();
                containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
                container.uploadPermissions(containerPermissions);

                // Upload an image file.
                blob = container.getBlockBlobReference("image1.jpg");

                File fileReference = new File("c:\\myimages\\image1.jpg");
                blob.upload(new FileInputStream(fileReference), fileReference.length());

                // At this point the image is uploaded.
                // Next, create an HTML page that lists all of the uploaded images.
                MakeHTMLPage(container);

                System.out.println("Processing complete.");
                System.out.println("Open index.html to see the images stored in your storage account.");

            } catch (FileNotFoundException fileNotFoundException) {
                System.out.print("FileNotFoundException encountered: ");
                System.out.println(fileNotFoundException.getMessage());
                System.exit(-1);
            } catch (StorageException storageException) {
                System.out.print("StorageException encountered: ");
                System.out.println(storageException.getMessage());
                System.exit(-1);
            } catch (URISyntaxException uriSyntaxException) {
                System.out.print("URISyntaxException encountered: ");
                System.out.println(uriSyntaxException.getMessage());
                System.exit(-1);
            } catch (Exception e) {
                System.out.print("Exception encountered: ");
                System.out.println(e.getMessage());
                System.exit(-1);
            }
        }

        // Create an HTML page that can be used to display the uploaded images.
        // This example assumes all of the blobs are for images.
        public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
        {
            PrintStream stream;
            stream = new PrintStream(new FileOutputStream("index.html"));

            // Create the opening <html>, <header>, and <body> elements.
            stream.println("<html>");
            stream.println("<header/>");
            stream.println("<body>");

            // Enumerate the uploaded blobs.
            for (ListBlobItem blobItem : container.listBlobs()) {
                // List each blob as an <img> element in the HTML body.
                stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
            }

            stream.println("</body>");

            // Complete the <html> element and close the file.
            stream.println("</html>");
            stream.close();
        }
    }

En plus de charger votre fichier image local dans le stockage Azure, l’exemple de code crée un fichier local nommé index.html que vous pouvez ouvrir dans votre navigateur pour voir votre image chargée.

Comme le code contient votre nom et votre clé de compte, veillez à la sécurité de votre code source.

## Suppression d'un conteneur

Étant donné que le stockage vous est facturé, vous pouvez supprimer le conteneur **gettingstarted** une fois que vous avez fini d'utiliser cet exemple. Pour supprimer un conteneur, utilisez la méthode **CloudBlobContainer.delete**.

    container = serviceClient.getContainerReference("gettingstarted");
    container.delete();

Pour appeler la méthode **CloudBlobContainer.delete**, le processus d’initialisation des objets **CloudStorageAccount**, **CloudBlobClient** et **CloudBlobContainer** est le même que celui de la méthode **createIfNotExist**. Voici un exemple complet permettant de supprimer le conteneur nommé **gettingstarted**.

    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;

    public class DeleteContainer {

        public static final String storageConnectionString =
                "DefaultEndpointsProtocol=http;" +
                   "AccountName=your_account_name;" +
                   "AccountKey=your_account_key";

        public static void main(String[] args)
        {
            try
            {
                CloudStorageAccount account;
                CloudBlobClient serviceClient;
                CloudBlobContainer container;

                account = CloudStorageAccount.parse(storageConnectionString);
                serviceClient = account.createCloudBlobClient();
                // Container name must be lower case.
                container = serviceClient.getContainerReference("gettingstarted");
                container.delete();

                System.out.println("Container deleted.");

            }
            catch (StorageException storageException)
            {
                System.out.print("StorageException encountered: ");
                System.out.println(storageException.getMessage());
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.print("Exception encountered: ");
                System.out.println(e.getMessage());
                System.exit(-1);
            }
        }
    }

Pour une présentation d’autres classes et méthodes de stockage d’objets blob, consultez la page [Utilisation du service de stockage d’objets blob à partir de Java].

## Étapes suivantes

Pour en savoir plus sur les tâches de stockage plus complexes, cliquez sur les liens ci-dessous.

- [Kit de développement logiciel (SDK) Azure Storage pour Java][]
- [Référence du Kit de développement logiciel (SDK) du client Azure Storage][]
- [API REST d’Azure Storage][]
- [Blog de l'équipe Azure Storage][]

  [Download the Azure SDK for Java]: http://go.microsoft.com/fwlink/?LinkID=525671
  [Création d'un compte de stockage]: storage-create-storage-account.md#create-a-storage-account
  [Gestion des comptes de stockage]: storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys
  [Utilisation du service de stockage d’objets blob à partir de Java]: storage-java-how-to-use-blob-storage.md
  [Kit de développement logiciel (SDK) Azure Storage pour Java]: https://github.com/azure/azure-storage-java
  [Référence du Kit de développement logiciel (SDK) du client Azure Storage]: http://dl.windowsazure.com/storage/javadoc/
  [API REST d’Azure Storage]: http://msdn.microsoft.com/library/azure/gg433040.aspx
  [Blog de l'équipe Azure Storage]: http://blogs.msdn.com/b/windowsazurestorage/

<!---HONumber=Oct15_HO3-->