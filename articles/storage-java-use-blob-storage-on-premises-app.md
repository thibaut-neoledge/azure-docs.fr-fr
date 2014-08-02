<properties linkid="dev-java-how-to-on-premise-application-with-blob-storage" urlDisplayName="Image Gallery w/ Storage" pageTitle="On-premises application with blob storage (Java) | Microsoft Azure" metaKeywords="Azure blob storage, Azure blob Java, Azure blob example, Azure blob tutorial" description="Learn how to create a console application that uploads an image to Azure, and then displays the image in your browser. Code samples in Java." metaCanonical="" services="storage" documentationCenter="Java" title="On-Premises Application with Blob Storage" authors="waltpo" solutions="" manager="bjsmith" editor="mollybos" />

Application locale avec stockage d'objets blob
==============================================

L'exemple suivant montre comment utiliser le stockage Azure pour stocker des images dans Azure. Le code ci-dessous s'applique à une application console qui charge une image dans Azure, puis crée un fichier HTML affichant l'image dans votre navigateur.

Sommaire
--------

-   [Configuration requise](#bkmk_prerequisites)
-   [Utilisation du stockage d'objets blob Azure pour charger un fichier](#bkmk_uploadfile)
-   [Suppression d'un conteneur](#bkmk_deletecontainer)

Configuration requise
---------------------

1.  La version 1.6 ou ultérieure du Kit de développement logiciel Java (JDK) est installée.
2.  Le Kit de développement logiciel (SDK) Azure est installé.
3.  L'archive Java (JAR) des bibliothèques Azure pour Java et les dépendances applicables JAR sont installées et se trouvent dans le chemin d'accès de build utilisé par votre compilateur Java. Pour plus d'informations sur l'installation des bibliothèques Azure pour Java, consultez la page [Téléchargement du Kit de développement logiciel (SDK) Azure pour Java](http://www.windowsazure.com/en-us/develop/java/).
4.  Un compte de stockage Azure a été configuré. Le nom et la clé du compte de stockage sont utilisés par le code ci-dessous. Consultez la page [Création d'un compte de stockage](http://www.windowsazure.com/en-us/manage/services/storage/how-to-create-a-storage-account/) pour des informations sur la création d'un compte de stockage et la page [Gestion des comptes de stockage](http://www.windowsazure.com/en-us/manage/services/storage/how-to-manage-a-storage-account/) pour des informations sur la récupération de la clé de compte.
5.  Vous avez créé un fichier image local nommé et stocké sous le chemin d'accès c:\\myimages\\image1.jpg. Vous pouvez également modifier le constructeur **FileInputStream** dans l'exemple pour utiliser un chemin d'accès à l'image et un nom de fichier différents.

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

Utilisation du stockage d'objets blob Azure pour charger un fichier
-------------------------------------------------------------------

Nous présentons ici une procédure étape par étape ; si vous voulez passer à la suite, le code est présenté dans son intégralité plus loin dans cette rubrique.

Commencez le code en incluant les imports pour les classes de stockage de base Azure, les classes du client d'objet blob Azure, les classes des E/S Java et la classe **URISyntaxException** :

    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.windowsazure.services.blob.client.*;
    import java.io.*;
    import java.net.URISyntaxException;

Déclarez une classe nommée **StorageSample**, puis incluez le crochet ouvrant 
**{**.

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

-   **CloudStorageAccount** : permet d'initialiser l'objet account avec vos nom et clé de compte de stockage Azure, ainsi que de créer l'objet client d'objet blob.
-   **CloudBlobClient** : permet d'accéder au service BLOB.
-   **CloudBlobContainer** : permet de créer un conteneur d'objets blob, de répertorier les objets blob dans le conteneur et de supprimer ce dernier.
-   **CloudBlockBlob** : permet de charger un fichier image local vers le conteneur.

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

    // Le nom du conteneur doit être en minuscules.
    container = serviceClient.getContainerReference("gettingstarted");

Création du conteneur. Cette méthode crée le conteneur s'il n'existe pas (et renvoie **true**). Si le conteneur existe, elle renvoie **false**. La méthode **create** (qui renvoie une erreur si le conteneur existe déjà) offre une alternative à **createIfNotExist**.

    container.createIfNotExist();

Définissez un accès anonyme pour le conteneur.

    // Définissez un accès anonyme au conteneur.
    BlobContainerPermissions containerPermissions;
    containerPermissions = new BlobContainerPermissions();
    containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
    container.uploadPermissions(containerPermissions);

Obtenez une référence pointant vers l'objet blob de blocs qui représente l'objet blob dans le stockage Azure.

    blob = container.getBlockBlobReference("image1.jpg");

Utilisez le constructeur **File** pour obtenir une référence pointant vers le fichier créé en local que vous allez charger. (Assurez-vous d'avoir créé ce fichier avant d'exécuter le code.)

    File fileReference = new File ("c:\myimages\image1.jpg");

Chargez le fichier local en appelant la méthode **CloudBlockBlob.upload**. Le premier paramètre de la méthode **CloudBlockBlob.upload** est un objet **FileInputStream** qui représente le fichier local à charger sur le stockage Azure. Le deuxième paramètre est la taille du fichier en octets.

    blob.upload(new FileInputStream(fileReference), fileReference.length());

Appelez une fonction d'assistance nommée **MakeHTMLPage** pour créer une page HTML de base contenant un élément **&lt;image\>** dont la source est définie sur l'objet blob qui est désormais dans votre compte de stockage Azure. (Le code pour **MakeHTMLPage** est abordé plus loin dans cette rubrique.)

    MakeHTMLPage(container);

Imprimez un message d'état et des informations sur la page HTML créée.

    System.out.println("Processing complete.");
    System.out.println("Open index.html to see the images stored in your storage account.");

Fermez le bloc **try** en insérant un crochet fermant : **}**

Gérez les exceptions suivantes :

-   **FileNotFoundException** : peut être levée par les constructeurs **FileInputStream** ou **FileOutputStream**.
-   **StorageException** : peut être levée par la bibliothèque de stockage cliente Azure.
-   **URISyntaxException** : peut être levée par la méthode **ListBlobItem.getUri**.
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

Pour créer une page HTML de base, créez une méthode nommée **MakeHTMLPage**. Cette méthode dispose d'un paramètre du type **CloudBlobContainer** qui est utilisé pour effectuer une itération dans la liste des objets blob chargés. Cette méthode lève des exceptions du type **FileNotFoundException** pouvant être levées par le constructeur **FileOutputStream** et du type **URISyntaxException** pouvant être levées par la méthode **ListBlobItem.getUri**. Incluez le crochet ouvrant **{**.

    public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
    {

Créez un fichier local nommé **index.html**.

    PrintStream stream;
    stream = new PrintStream(new FileOutputStream("index.html"));

Écrivez dans le fichier local en y ajoutant les éléments **&lt;html\>**, **&lt;header\>** et **&lt;body\>**.

    stream.println("<html>");
    stream.println("<header/>");
    stream.println("<body>");

Effectuez une itération dans la liste des objets blob chargés. Pour chaque objet blob, créez dans la page HTML un élément **&lt;img\>** dont l'attribut **src** est envoyé à l'URI de l'objet blob tel qu'il existe dans votre compte de stockage Azure. Dans cet exemple, vous avez ajouté une seule image, mais si vous en ajoutiez plus, ce code effectuerait une itération pour chacune d'entre elles.

Par souci de simplification, cet exemple part du principe que chaque objet blob chargé est une image. Si vous avez chargé des objets blob qui ne sont pas des images ou des objets blob de pages au lieu d'objets blob de blocs, modifiez le code en conséquence.

    // Énumérez les objets blob chargés.
    for (ListBlobItem blobItem : container.listBlobs()) {
    // Listez chaque objet blob comme élément <img> dans le corps HTML.
    stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
    }

Fermez les éléments **&lt;body\>** et **&lt;html\>**.

    stream.println("</body>");
    stream.println("</html>");

Fermez le fichier local.

    stream.close();

Fermez **MakeHTMLPage** en insérant un crochet fermant : **}**

Fermez **StorageSample** en insérant un crochet fermant : **}**

Voici le code complet pour cet exemple. N'oubliez pas de modifier les valeurs des espaces réservés **your\_account\_name** et **your\_account\_key** pour utiliser respectivement votre nom et votre clé de compte.

    import com.microsoft.windowsazure.services.core.storage.*;
                import com.microsoft.windowsazure.services.blob.client.*;
                import java.io.*;
                import java.net.URISyntaxException;
                
                // Créez une image c:\myimages\image1.jpg avant d'exécuter cet exemple.
    // Vous pouvez également modifier la valeur utilisée par le constructeur FileInputStream 
                // pour utiliser un chemin d'accès et un fichier image différents de ceux précédemment créés.
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
                            // Le nom du conteneur doit être en minuscules.
                container = serviceClient.getContainerReference("gettingstarted");
                            container.createIfNotExist();
                            
                            // Définissez un accès anonyme au conteneur.
                BlobContainerPermissions containerPermissions;
                            containerPermissions = new BlobContainerPermissions();
                            containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
                            container.uploadPermissions(containerPermissions);
                
                            // Chargez un fichier image.
                blob = container.getBlockBlobReference("image1.jpg");
                            File fileReference = new File ("c:\myimages\image1.jpg");
                            blob.upload(new FileInputStream(fileReference), fileReference.length());
                
                            // L'image est désormais chargée.
                // Créez ensuite une page HTML qui répertorie toutes les images chargées.
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
                
                    // Créez une page HTML pouvant être utilisée pour afficher les images chargées.
        // Cet exemple part du principe que tous les objets blob sont des images.
        public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
                {
                        PrintStream stream;
                        stream = new PrintStream(new FileOutputStream("index.html"));
                
                        // Créez les éléments d'ouverture <html>, <header> et <body>.
            stream.println("<html>");
                        stream.println("<header/>");
                        stream.println("<body>");
                
                        // Énumérez les objets blob chargés.
            for (ListBlobItem blobItem : container.listBlobs()) {
                            // Énumérez chaque objet blob comme élément <img> dans le corps HTML.
                stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
                        }
                
                        stream.println("</body>");
                
                        // Complétez l'élément <html> et fermez le fichier.
            stream.println("</html>");
                        stream.close();
                    }
                }

En plus de charger votre fichier image local dans le stockage Azure, l'exemple de code crée un fichier local nommé index.html que vous pouvez ouvrir dans votre navigateur pour voir votre image chargée.

Comme le code contient votre nom et votre clé de compte, veillez à la sécurité de votre code source.

Suppression d'un conteneur
--------------------------

Étant donné que le stockage vous est facturé, vous pouvez supprimer le conteneur **gettingstarted** une fois que vous avez fini d'utiliser cet exemple. Pour supprimer un conteneur, utilisez la méthode **CloudBlobContainer.delete** :

    container = serviceClient.getContainerReference("gettingstarted");
    container.delete();

Pour appeler la méthode **CloudBlobContainer.delete**, le processus d'initialisation des objets **CloudStorageAccount**, **CloudBlobClient** et **CloudBlobContainer** est le même que celui montré pour la méthode **createIfNotExist**. Voici un exemple complet permettant de supprimer le conteneur nommé **gettingstarted**.

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
                            // Le nom du conteneur doit être en minuscules.
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

Pour une présentation d'autres classes et méthodes de stockage d'objets blob, consultez la page [Utilisation du service de stockage d'objets blob à partir de Java](http://www.windowsazure.com/en-us/develop/java/how-to-guides/blob-storage/).

