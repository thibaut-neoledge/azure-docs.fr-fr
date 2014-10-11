<properties linkid="dev-java-how-to-on-premise-application-with-blob-storage" urlDisplayName="Image Gallery w/ Storage" pageTitle="On-premises application with blob storage (Java) | Microsoft Azure" metaKeywords="Azure blob storage, Azure blob Java, Azure blob example, Azure blob tutorial" description="Learn how to create a console application that uploads an image to Azure, and then displays the image in your browser. Code samples in Java." metaCanonical="" services="storage" documentationCenter="Java" title="On-Premises Application with Blob Storage" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm"></tags>

# Application locale avec stockage d'objets blob

L'exemple suivant vous montre comment utiliser le stockage Azure pour
stocker des images dans Azure. Le code ci-après concerne une application
console qui télécharge une image dans Azure, puis crée un
fichier HTML qui affiche l'image dans votre navigateur.

## Sommaire

-   [Configuration requise][]
-   [Utilisation du stockage d'objets blob Azure pour charger un fichier][]
-   [Suppression d'un conteneur][]

## <a name="bkmk_prerequisites"> </a>Conditions préalables

1.  La version 1.6 ou ultérieure du Kit de développement logiciel Java (JDK) est installée.
2.  Le Kit de développement logiciel (SDK) Azure est installé.
3.  Le fichier JAR des bibliothèques Azure pour Java et tous les fichiers
    JAR de dépendance applicables sont installés et se trouvent dans le chemin d'accès de build utilisé par
    votre compilateur Java. Pour plus d'informations sur l'installation des bibliothèques Azure pour Java, consultez la rubrique [Téléchargement du
    Kit de développement logiciel (SDK) Azure pour Java][].
4.  Un compte de stockage Azure a été configuré. Le nom
    et la clé du compte de stockage sont utilisés par le code
    ci-après. Pour plus d'informations sur la création d'un compte de stockage, consultez la rubrique [Création d'un compte de stockage][],
    et pour plus d'informations sur l'extraction de la
    clé de compte, consultez la rubrique [Gestion des comptes de stockage][].
5.  Vous avez créé un fichier image local nommé, stocké sous
    c:\\myimages\\image1.jpg. Sinon, modifiez le constructeur
    **FileInputStream** dans l'exemple pour utiliser un autre
    chemin d'accès et un autre nom de fichier pour l'image.

[WACOM.INCLUDE [create-account-note][]]

## <a name="bkmk_uploadfile"> </a>Utilisation du stockage d'objets blob Azure pour télécharger un fichier

Une procédure pas à pas est présentée ici, mais, si vous préférez l'ignorer,
le code est présenté dans son intégralité plus loin dans la rubrique.

Commencez le code en incluant les importations des principales classes de stockage,
 les classes clientes d'objets blob Azure, les classes d'E/S Java et
la classe **URISyntaxException** :

    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.windowsazure.services.blob.client.*;
    import java.io.*;
    import java.net.URISyntaxException;

Déclarez une classe nommée **StorageSample**, puis incluez le crochet ouvrant
**{**.

    public class StorageSample {

Dans la classe **StorageSample**, déclarez une variable de chaîne qui
contient le protocole de point de terminaison par défaut, votre nom de compte de stockage
et votre clé d'accès au stockage, comme spécifié dans votre compte de stockage Azure.
 Remplacez les valeurs des espaces réservés **your\_account\_name** et
**your\_account\_key** respectivement par votre nom de compte et votre clé de compte.

    public static final String storageConnectionString = 
           "DefaultEndpointsProtocol=http;" + 
               "AccountName=your_account_name;" + 
               "AccountKey=your_account_name"; 

Ajoutez votre déclaration pour **main**, ajoutez un bloc **try** et
placez les crochets ouvrants nécessaires **{**.

    public static void main(String[] args) 
    {
        try
        {

Déclarez les variables des types suivants (les descriptions concernent leur
utilisation dans cet exemple) :

-   **CloudStorageAccount** : permet d'initialiser l'objet de compte avec
    vos nom et clé de compte de stockage Azure, et de créer
    l'objet client d'objet blob.
-   **CloudBlobClient** : permet d'accéder au service BLOB.
-   **CloudBlobContainer** : permet de créer un conteneur d'objets blob, de lister les
    objets blob dans le conteneur et de supprimer le conteneur.
-   **CloudBlockBlob** : permet de télécharger un fichier image local dans le
    conteneur.

<!-- -->

    CloudStorageAccount account;
    CloudBlobClient serviceClient;
    CloudBlobContainer container;
    CloudBlockBlob blob;

Attribuez une valeur à la variable **account**.

    account = CloudStorageAccount.parse(storageConnectionString);

Attribuez une valeur à la variable **serviceClient**.

    serviceClient = account.createCloudBlobClient();

Attribuez une valeur à la variable **container**. Nous obtenons une référence à un
conteneur nommé **gettingstarted**.

    // Container name must be lower case.
    container = serviceClient.getContainerReference("gettingstarted");

Création du conteneur. Cette méthode crée le conteneur s'il
n'existe pas (et renvoie **true**). Si le conteneur existe, elle renvoie
**false**. La méthode **create** (qui renvoie une erreur si le conteneur existe déjà) offre une alternative à **createIfNotExist**
.

    container.createIfNotExist();

Définissez un accès anonyme pour le conteneur.

    // Set anonymous access on the container.
    BlobContainerPermissions containerPermissions;
    containerPermissions = new BlobContainerPermissions();
    containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
    container.uploadPermissions(containerPermissions);

Obtenez une référence à l'objet blob de blocs, qui représente l'objet blob dans
le stockage Azure.

    blob = container.getBlockBlobReference("image1.jpg");

Utilisez le constructeur **File** pour obtenir une référence au fichier créé localement
que vous allez télécharger. (Vous devez avoir créé ce fichier
avant d'exécuter le code.)

    File fileReference = new File ("c:\\myimages\\image1.jpg");

Chargez le fichier local en appelant la méthode **CloudBlockBlob.upload**
. Le premier paramètre de la méthode **CloudBlockBlob.upload** est un objet
**FileInputStream** qui représente le fichier local qui sera
téléchargé dans le stockage Azure. Le deuxième paramètre est la taille,
en octets, du fichier.

    blob.upload(new FileInputStream(fileReference), fileReference.length());

Appelez une fonction d'assistance nommée **MakeHTMLPage** pour créer une page HTML de base
qui contient un élément **\<image\>** avec la source définie sur l'objet blob qui
se trouve désormais dans votre compte de stockage Azure. (Le code pour
**MakeHTMLPage** est abordé plus loin dans cette rubrique.)

    MakeHTMLPage(container);

Imprimez un message d'état et des informations sur la page HTML créée.

    System.out.println("Processing complete.");
    System.out.println("Open index.html to see the images stored in your storage account.");

Fermez le bloc **try** en insérant un crochet fermant : **}**

Gérez les exceptions suivantes :

-   **FileNotFoundException** : peut être levée par les constructeurs **FileInputStream**
     ou **FileOutputStream**
    .
-   **StorageException** : peut être levé par la bibliothèque de stockage
    cliente Azure.
-   **URISyntaxException** : peut être levée par la méthode **ListBlobItem.getUri**
    .
-   **Exception** : gestion d'une exception générique.

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

Fermez **main** en insérant un crochet fermant : **}**

Pour créer une page HTML de base, créez une méthode nommée **MakeHTMLPage**. Cette
méthode a un paramètre de type **CloudBlobContainer**, qui sera
utilisé pour itérer dans la liste des objets blob téléchargés. Cette méthode va
lever des exceptions de type **FileNotFoundException**, qui peuvent être levées
par le constructeur **FileOutputStream**, et **URISyntaxException**,
qui peut être levée par la méthode **ListBlobItem.getUri**. Incluez le
crochet ouvrant, **{**.

    public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
    {

Créez un fichier local nommé **index.html**.

    PrintStream stream;
    stream = new PrintStream(new FileOutputStream("index.html"));

Écrivez dans le fichier local en y ajoutant les éléments **\<html\>**, **\<header\>** et
**\<body\>**.

    stream.println("<html>");
    stream.println("<header/>");
    stream.println("<body>");

Effectuez une itération dans la liste des objets blob chargés. Pour chaque objet blob, dans la page HTML,
créez un élément **\<img\>** qui a son attribut **src** envoyé à
l'URI de l'objet blob qui existe dans votre compte de stockage Azure.
Vous n'avez ajouté qu'une image dans cet exemple, mais si vous en aviez ajouté plus,
ce code les itérerait tous.

Par souci de simplification, cet exemple part du principe que chaque objet blob chargé est une image. Si
vous avez mis à jour des objets blob autres que des images, ou des objets blob de pages au lieu d'objets blob de blocs,
ajustez le code.

    // Enumerate the uploaded blobs.
    for (ListBlobItem blobItem : container.listBlobs()) {
    // List each blob as an <img> element in the HTML body.
    stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
    }

Fermez les éléments **\<body\>** et **\<html\>**.

    stream.println("</body>");
    stream.println("</html>");

Fermez le fichier local.

    stream.close();

Fermez **MakeHTMLPage** en insérant un crochet fermant : **}**

Fermez **StorageSample** en insérant un crochet fermant : **}**

Voici le code complet pour cet exemple. Pensez à modifier
la valeur des espaces réservés **your\_account\_name** et
**your\_account\_key** pour utiliser votre nom de compte et votre clé de compte,
respectivement.

    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.windowsazure.services.blob.client.*;
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

        public static void main(String[] args) 
        {
            try
            {
                CloudStorageAccount account;
                CloudBlobClient serviceClient;
                CloudBlobContainer container;
                CloudBlockBlob blob;
                
                account = CloudStorageAccount.parse(storageConnectionString);
                serviceClient = account.createCloudBlobClient();
                // Container name must be lower case.
                container = serviceClient.getContainerReference("gettingstarted");
                container.createIfNotExist();
                
                // Set anonymous access on the container.
                BlobContainerPermissions containerPermissions;
                containerPermissions = new BlobContainerPermissions();
                containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
                container.uploadPermissions(containerPermissions);

                // Upload an image file.
                blob = container.getBlockBlobReference("image1.jpg");
                File fileReference = new File ("c:\\myimages\\image1.jpg");
                blob.upload(new FileInputStream(fileReference), fileReference.length());

                // At this point the image is uploaded.
                // Next, create an HTML page that lists all of the uploaded images.
                MakeHTMLPage(container);

                System.out.println("Processing complete.");
                System.out.println("Open index.html to see the images stored in your storage account.");

            }
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

En plus de télécharger votre fichier image local dans le stockage Azure,
l'exemple de code crée le fichier local namedindex.html, que vous pouvez
ouvrir dans votre navigateur pour afficher l'image téléchargée.

Comme le code contient votre nom de compte et votre clé de compte, votre
code source est sécurisé.

## <a name="bkmk_deletecontainer"> </a>Suppression d'un conteneur

Comme le stockage vous est facturé, vous pouvez supprimer le conteneur
**gettingstarted** à la fin de cet exemple. Pour supprimer un conteneur,
utilisez la méthode **CloudBlobContainer.delete**
 :

    container = serviceClient.getContainerReference("gettingstarted");
    container.delete();

Pour appeler la méthode **CloudBlobContainer.delete**, la procédure d'
initialisation des objets **CloudStorageAccount**, **ClodBlobClient**,
**CloudBlobContainer** est celle indiquée pour la méthode
**createIfNotExist**. Voici un exemple complet qui
supprime le conteneur **gettingstarted**.

    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.windowsazure.services.blob.client.*;

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

Pour obtenir une vue d'ensemble des autres classes et méthodes de stockage d'objets blob, consultez la rubrique [
Utilisation du service de stockage d'objet blob dans Java][].

  [Configuration requise]: #bkmk_prerequisites
  [Utilisation du stockage d'objets blob Azure pour charger un fichier]: #bkmk_uploadfile
  [Suppression d'un conteneur]: #bkmk_deletecontainer
  [Téléchargement du
  Kit de développement logiciel (SDK) Azure pour Java]: http://www.windowsazure.com/en-us/develop/java/
  [Création d'un compte de stockage]: http://www.windowsazure.com/en-us/manage/services/storage/how-to-create-a-storage-account/
  [Gestion des comptes de stockage]: http://www.windowsazure.com/en-us/manage/services/storage/how-to-manage-a-storage-account/
  [create-account-note]: ../includes/create-account-note.md
  [
  Utilisation du service de stockage d'objet blob dans Java]: http://www.windowsazure.com/en-us/develop/java/how-to-guides/blob-storage/
