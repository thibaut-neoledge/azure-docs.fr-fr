---
title: Utiliser le Kit de développement logiciel (SDK) Java Data Lake Store pour développer des applications | Microsoft Docs
description: Utiliser le Kit de développement logiciel (SDK) Java Azure Data Lake Store pour développer des applications
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun

ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/13/2016
ms.author: nitinme

---
# Prise en main d’Azure Data Lake Store à l’aide de Java
> [!div class="op_single_selector"]
> * [Portail](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Kit SDK .NET](data-lake-store-get-started-net-sdk.md)
> * [Kit SDK Java](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [Interface de ligne de commande Azure](data-lake-store-get-started-cli.md)
> * [Node.JS](data-lake-store-manage-use-nodejs.md)
> 
> 

Apprenez à utiliser le Kit de développement logiciel (SDK) Java Azure Data Lake Store pour créer un compte Azure Data Lake et effectuer des opérations de base comme créer des dossiers, télécharger des fichiers de données, supprimer votre compte, etc. Pour plus d’informations sur Data Lake, consultez [Azure Data Lake Store](data-lake-store-overview.md).

## Kit de développement logiciel (SDK) Java Azure Data Lake Store
Les liens suivants permettent d’accéder à l’emplacement de téléchargement du Kit de développement logiciel (SDK) Java pour le Data Lake Store et de la référence du Kit de développement logiciel (SDK) Java. Pour ce didacticiel, il est inutile de télécharger le Kit de développement logiciel (SDK) ou de suivre le document de référence. Ces liens sont uniquement fournis à titre informatif.

* Le code source du Kit de développement logiciel (SDK) Java pour le Data Lake Store est disponible sur [GitHub](https://github.com/Azure/azure-sdk-for-java).
* La référence du Kit de développement logiciel (SDK) Java pour le Data Lake Store est disponible à l’adresse [https://azure.github.io/azure-sdk-for-java/](https://azure.github.io/azure-sdk-for-java/).

## Composants requis
* Kit de développement Java (JDK) 8 (avec Java version 1.8).
* IntelliJ ou un autre environnement de développement Java approprié. Ceci étape est facultatif mais recommandé. Les instructions ci-dessous utilisent IntelliJ.
* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Créez une application Azure Active Directory**. Il existe deux modes d’authentification à l’aide d’Azure Active Directory : **interactif** et **non interactif**. Les conditions préalables requises varient selon le mode d’authentification.
  
  * **Pour l’authentification interactive** - Dans Azure Active Directory, vous devez créer une **application cliente native**. Une fois que vous avez créé l’application, récupérez les valeurs suivantes liées à l’application.
    
    * Obtenez l’**ID client** et l’**URI de redirection** associés à l’application.
    * Définir des autorisations déléguées
  * **Pour l’authentification non interactive** (utilisée dans cet article) - Dans Azure Active Directory, vous devez créer une **application web**. Une fois que vous avez créé l’application, récupérez les valeurs suivantes liées à l’application.
    
    * Obtenez l’**ID client**, la **clé secrète client** et l’**URI de redirection** associés à l’application.
    * Définir des autorisations déléguées
    * Attribuez l’application Azure Active Directory à un rôle. Le rôle détermine le niveau de l’étendue pour laquelle vous souhaitez accorder des autorisations à l’application Azure Active Directory. Par exemple, vous pouvez affecter l’application au niveau de l’abonnement ou au niveau d’un groupe de ressources.
    
    Pour obtenir des instructions sur la récupération de ces valeurs, la définition des autorisations et l’attribution de rôles, consultez [Création de l’application Active Directory et du principal du service à l’aide du portail](../resource-group-create-service-principal-portal.md).

## Comment s’authentifier à l’aide d’Azure Active Directory ?
L’extrait de code ci-dessous fournit le code pour une authentification **non interactive**, où l’application fournit ses propres informations d’identification.

Vous devez donner à votre application l’autorisation de créer des ressources dans Azure pour que ce didacticiel fonctionne. Dans le cadre de ce didacticiel, il est **recommandé** de n’accorder à cette application que des autorisations de type Collaborateur pour un nouveau groupe de ressources, inutilisé et vide, dans votre abonnement Azure.

## Création d’une application Java
1. Ouvrez IntelliJ et créez un nouveau projet Java à l’aide du modèle **Application de ligne de commande**. Terminez l’Assistant pour créer le projet.
2. Ouvrez **Fichier** -> **Structure de projet** -> **Modules** (sous Paramètres du projet) -> **Dépendances** -> **+** -> **Bibliothèque** -> **From Maven (De Maven)**.
3. Recherchez les packages Maven suivants et ajoutez-les à votre projet :
   
   * com.microsoft.azure:azure-mgmt-datalake-store:1.0.0-beta1.2
   * com.microsoft.azure:azure-mgmt-datalake-store-uploader:1.0.0-beta1.2
   * com.microsoft.azure:azure-client-authentication:1.0.0-beta2
4. Dans le volet de gauche, accédez à **src**, **principal**, **java**, **<nom du package>**, puis ouvrez **Main.java** et remplacez le bloc de code existant par le code suivant. De même, indiquez les valeurs des paramètres inclus dans l’extrait de code, tels que **localFolderPath**, **DATA-LAKE-STORE-NAME** et **RESOURCE-GROUP-NAME**, et remplacez les espaces réservés pour les valeurs **CLIENT-ID**, **CLIENT-SECRET**, **TENANT-ID** et **SUBSCRIPTION-ID** par les informations relatives à votre abonnement et à son Azure Active Directory. Pour découvrir comment trouver ces informations, consultez le [guide Azure de création de principes de service (Azure guide to creating service principals)](../resource-group-authenticate-service-principal.md).
   
    Ce code déroule le processus de création d’un compte Data Lake Store, de création de fichiers dans le magasin, de concaténation de fichiers, de téléchargement d’un fichier et enfin de suppression du compte.
   
        package com.company;
   
        import com.microsoft.azure.CloudException;
        import com.microsoft.azure.credentials.ApplicationTokenCredentials;
        import com.microsoft.azure.management.datalake.store.implementation.DataLakeStoreAccountManagementClientImpl;
        import com.microsoft.azure.management.datalake.store.implementation.DataLakeStoreFileSystemManagementClientImpl;
        import com.microsoft.azure.management.datalake.store.models.*;
        import com.microsoft.azure.management.datalake.store.uploader.*;
        import com.microsoft.rest.credentials.ServiceClientCredentials;
        import java.io.*;
        import java.nio.charset.Charset;
        import java.util.ArrayList;
        import java.util.List;
   
        public class Main {
            final static String ADLS_ACCOUNT_NAME = <DATA-LAKE-STORE-NAME>;
            final static String RESOURCE_GROUP_NAME = "<RESOURCE-GROUP-NAME>";
            final static String LOCATION = "East US 2";
            final static String TENANT_ID = "<TENANT-ID>";
            final static String SUBSCRIPTION_ID =  "<SUBSCRIPTION-ID>";
            final static String CLIENT_ID = "<CLIENT-ID>";
            final static String CLIENT_SECRET = "<CLIENT-SECRET>"; // TODO: For production scenarios, we recommend that you replace this line with a more secure way of acquiring the application client secret, rather than hard-coding it in the source code.
   
            private static DataLakeStoreAccountManagementClientImpl _adlsClient;
            private static DataLakeStoreFileSystemManagementClientImpl _adlsFileSystemClient;
   
            public static void main(String[] args) throws Exception {
                String localFolderPath = "C:\\local_path\"; // TODO: Change this to any unused, new, empty folder on your local machine.
   
                // Authenticate
                ApplicationTokenCredentials creds = new ApplicationTokenCredentials(CLIENT_ID, TENANT_ID, CLIENT_SECRET, null);
                SetupClients(creds);
   
                // Create Data Lake Store account
                WaitForNewline("Authenticated.", "Creating NEW account.");
                CreateAccount();
                WaitForNewline("Account created.", "Displaying account(s).");
   
                // List Data Lake Store accounts that this app can access
                System.out.println(String.format("All ADL Store accounts that this app can access in subscription %s:", SUBSCRIPTION_ID));
                List<DataLakeStoreAccount> adlsListResult = _adlsClient.accounts().list().getBody();
                for (DataLakeStoreAccount acct : adlsListResult) {
                    System.out.println(acct.name());
                }
                WaitForNewline("Account(s) displayed.", "Uploading file.");
   
                // Upload a file to Data Lake Store: file1.csv
                UploadFile(localFolderPath + "file1.csv", "/file1.csv");
                WaitForNewline("File uploaded.", "Appending newline.");
   
                // Append newline to file1.csv
                AppendToFile("/file1.csv", "\r\n");
                WaitForNewline("Newline appended.", "Creating file.");
   
                // Create a new file in Data Lake Store: file2.csv
                CreateFile("/file2.csv", "456,def", true);
                WaitForNewline("File created.", "Concatenating files.");
   
                // Concatenate two files in Data Lake Store
                List<String> srcFilePaths = new ArrayList<String>();
                srcFilePaths.add("/file1.csv");
                srcFilePaths.add("/file2.csv");
                ConcatenateFiles(srcFilePaths, "/input.csv");
                WaitForNewline("Files concatenated.", "Downloading file.");
   
                // Download file from Data Lake Store
                DownloadFile("/input.csv", localFolderPath + "input.csv");
                WaitForNewline("File downloaded.", "Deleting file.");
   
                // Delete file from Data Lake Store
                DeleteFile("/input.csv");
                WaitForNewline("File deleted.", "Deleting account.");
   
                // Delete account
                DeleteAccount();
                WaitForNewline("Account deleted.", "DONE.");
            }
   
            //Set up clients
            public static void SetupClients(ServiceClientCredentials creds)
            {
                _adlsClient = new DataLakeStoreAccountManagementClientImpl(creds);
                _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClientImpl(creds);
                _adlsClient.withSubscriptionId(SUBSCRIPTION_ID);
            }
   
            // Helper function to show status and wait for user input
            public static void WaitForNewline(String reason, String nextAction)
            {
                if (nextAction == null)
                    nextAction = "";
                if (!nextAction.isEmpty())
                {
                    System.out.println(reason + "\r\nPress ENTER to continue...");
                    try{System.in.read();}
                    catch(Exception e){}
                    System.out.println(nextAction);
                }
                else
                {
                    System.out.println(reason + "\r\nPress ENTER to continue...");
                    try{System.in.read();}
                    catch(Exception e){}
                }
            }
   
            // Create Data Lake Store account
            public static void CreateAccount() throws InterruptedException, CloudException, IOException {
                DataLakeStoreAccount adlsParameters = new DataLakeStoreAccount();
                adlsParameters.withLocation(LOCATION);
   
                _adlsClient.accounts().create(RESOURCE_GROUP_NAME, ADLS_ACCOUNT_NAME, adlsParameters);
            }
   
            // Create file
            public static void CreateFile(String path) throws IOException, AdlsErrorException {
                _adlsFileSystemClient.fileSystems().create(ADLS_ACCOUNT_NAME, path);
            }
   
            // Create file with contents
            public static void CreateFile(String path, String contents, boolean force) throws IOException, AdlsErrorException {
                byte[] bytesContents = contents.getBytes();
   
                _adlsFileSystemClient.fileSystems().create(ADLS_ACCOUNT_NAME, path, bytesContents, force);
            }
   
            // Append to file
            public static void AppendToFile(String path, String contents) throws IOException, AdlsErrorException {
                byte[] bytesContents = contents.getBytes();
   
                _adlsFileSystemClient.fileSystems().append(ADLS_ACCOUNT_NAME, path, bytesContents);
            }
   
            // Concatenate files
            public static void ConcatenateFiles(List<String> srcFilePaths, String destFilePath) throws IOException, AdlsErrorException {
                _adlsFileSystemClient.fileSystems().concat(ADLS_ACCOUNT_NAME, destFilePath, srcFilePaths);
            }
   
            // Delete concatenated file
            public static void DeleteFile(String filePath) throws IOException, AdlsErrorException {
                _adlsFileSystemClient.fileSystems().delete(ADLS_ACCOUNT_NAME, filePath);
            }
   
            // Get file or directory info
            public static FileStatusProperties GetItemInfo(String path) throws IOException, AdlsErrorException {
                return _adlsFileSystemClient.fileSystems().getFileStatus(ADLS_ACCOUNT_NAME, path).getBody().fileStatus();
            }
   
            // List files and directories
            public static List<FileStatusProperties> ListItems(String directoryPath) throws IOException, AdlsErrorException {
                return _adlsFileSystemClient.fileSystems().listFileStatus(ADLS_ACCOUNT_NAME, directoryPath).getBody().fileStatuses().fileStatus();
            }
   
            // Upload file
            public static void UploadFile(String srcPath, String destPath) throws Exception {
                UploadParameters parameters = new UploadParameters(srcPath, destPath, ADLS_ACCOUNT_NAME);
                FrontEndAdapter frontend = new DataLakeStoreFrontEndAdapterImpl(ADLS_ACCOUNT_NAME, _adlsFileSystemClient);
                DataLakeStoreUploader uploader = new DataLakeStoreUploader(parameters, frontend);
                uploader.execute();
            }
   
            // Download file
            public static void DownloadFile(String srcPath, String destPath) throws IOException, AdlsErrorException {
                InputStream stream = _adlsFileSystemClient.fileSystems().open(ADLS_ACCOUNT_NAME, srcPath).getBody();
   
                PrintWriter pWriter = new PrintWriter(destPath, Charset.defaultCharset().name());
   
                String fileContents = "";
                if (stream != null) {
                    Writer writer = new StringWriter();
   
                    char[] buffer = new char[1024];
                    try {
                        Reader reader = new BufferedReader(
                                new InputStreamReader(stream, "UTF-8"));
                        int n;
                        while ((n = reader.read(buffer)) != -1) {
                            writer.write(buffer, 0, n);
                        }
                    } finally {
                        stream.close();
                    }
                    fileContents =  writer.toString();
                }
   
                pWriter.println(fileContents);
                pWriter.close();
            }
   
            // Delete account
            public static void DeleteAccount() throws InterruptedException, CloudException, IOException {
                _adlsClient.accounts().delete(RESOURCE_GROUP_NAME, ADLS_ACCOUNT_NAME);
            }
        }
5. Exécutez l'application. Suivez les invites pour exécuter et terminer l’application.

## Étapes suivantes
* [Sécuriser les données dans Data Lake Store](data-lake-store-secure-data.md)
* [Utiliser Azure Data Lake Analytics avec Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Utiliser Azure HDInsight avec Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0914_2016-->