<properties
   pageTitle="Utiliser le Kit de développement logiciel (SDK) Java Data Lake Store pour développer des applications | Azure"
   description="Utiliser le Kit de développement logiciel (SDK) Java Azure Data Lake Store pour développer des applications"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/12/2016"
   ms.author="nitinme"/>

# Prise en main d’Azure Data Lake Store à l’aide de Java

> [AZURE.SELECTOR]
- [Portail](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [Kit SDK .NET](data-lake-store-get-started-net-sdk.md)
- [Kit SDK Java](data-lake-store-get-started-java-sdk.md)
- [API REST](data-lake-store-get-started-rest-api.md)
- [Interface de ligne de commande Azure](data-lake-store-get-started-cli.md)
- [Node.JS](data-lake-store-manage-use-nodejs.md)

Apprenez à utiliser le Kit de développement logiciel (SDK) Java Azure Data Lake Store pour créer un compte Azure Data Lake et effectuer des opérations de base comme créer des dossiers, télécharger des fichiers de données, supprimer votre compte, etc. Pour plus d’informations sur Data Lake, consultez [Azure Data Lake Store](data-lake-store-overview.md).

## Kit de développement logiciel (SDK) Java Azure Data Lake Store

Les liens suivants permettent d’accéder à l’emplacement de téléchargement du Kit de développement logiciel (SDK) Java pour le Data Lake Store et de la référence du Kit de développement logiciel (SDK) Java. Pour ce didacticiel, il est inutile de télécharger le Kit de développement logiciel (SDK) ou de suivre le document de référence. Ces liens sont uniquement fournis à titre informatif.

* Le code source du Kit de développement logiciel (SDK) Java pour le Data Lake Store est disponible sur [GitHub](https://github.com/Azure/azure-sdk-for-java).
* La référence du Kit de développement logiciel (SDK) Java pour le Data Lake Store est disponible à l’adresse [https://azure.github.io/azure-sdk-for-java/](https://azure.github.io/azure-sdk-for-java/).

## Composants requis

* Kit de développement Java (JDK) 8 (avec Java version 1.8).
* IntelliJ ou un autre environnement de développement Java approprié. Ceci étape est facultatif mais recommandé. Les instructions ci-dessous utilisent IntelliJ.
* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Activez votre abonnement Azure** pour la version d'évaluation publique de Data Lake Store. Consultez les [instructions](data-lake-store-get-started-portal.md#signup).
* **Créez une application Azure Active Directory**. Il existe deux modes d’authentification à l’aide d’Azure Active Directory : **interactif** et **non interactif**. Les conditions préalables requises varient selon le mode d’authentification.
	* **Pour l’authentification interactive** - Dans Azure Active Directory, vous devez créer une **application cliente native**. Une fois que vous avez créé l’application, récupérez les valeurs suivantes liées à l’application.
		- Obtenez l’**ID client** et l’**URI de redirection** associés à l’application.
		- Définir des autorisations déléguées

	* **Pour l’authentification non interactive** (utilisée dans cet article) - Dans Azure Active Directory, vous devez créer une **application web**. Une fois que vous avez créé l’application, récupérez les valeurs suivantes liées à l’application.
		- Obtenez l’**ID client**, la **clé secrète client** et l’**URI de redirection** associés à l’application.
		- Définir des autorisations déléguées
		- Attribuez l’application Azure Active Directory à un rôle. Le rôle détermine le niveau de l’étendue pour laquelle vous souhaitez accorder des autorisations à l’application Azure Active Directory. Par exemple, vous pouvez affecter l’application au niveau de l’abonnement ou au niveau d’un groupe de ressources. 

	Pour obtenir des instructions sur la récupération de ces valeurs, la définition des autorisations et l’attribution de rôles, consultez [Création de l’application Active Directory et du principal du service à l’aide du portail](../resource-group-create-service-principal-portal.md).

## Comment s’authentifier à l’aide d’Azure Active Directory ?

L’extrait de code ci-dessous fournit le code pour une authentification **non interactive**, où l’application fournit ses propres informations d’identification.

Vous devrez donner à votre application l’autorisation de créer des ressources dans Azure pour que ce didacticiel fonctionne. Dans le cadre de ce didacticiel, il est **recommandé** de n’accorder à cette application que des autorisations de type Collaborateur pour un nouveau groupe de ressources, inutilisé et vide, dans votre abonnement Azure.

## Création d’une application Java

1. Ouvrez IntelliJ et créez un nouveau projet Java à l’aide du modèle **Application de ligne de commande**. Terminez l’Assistant pour créer le projet.

2. Cliquez avec le bouton droit sur le projet sur le côté gauche de l’écran et cliquez sur **Ajouter la prise en charge Framework**. Choisissez **Maven** et cliquez sur **OK**.

3. Ouvrez le fichier **« pom.xml »** nouvellement créé et ajoutez l’extrait de texte suivant entre la balise **</version>** et la balise **</project>** :

    >[AZURE.NOTE] Cette étape est temporaire jusqu’à ce que le Kit de développement logiciel (SDK) Azure Data Lake Store soit disponible dans Maven. Cet article sera mis à jour une fois le Kit de développement logiciel (SDK) disponible dans Maven. Toutes les futures mises à jour ce Kit de développement logiciel (SDK) seront disponibles via Maven.

        <repositories>
        	<repository>
	            <id>adx-snapshots</id>
	            <name>Azure ADX Snapshots</name>
	            <url>http://adxsnapshots.azurewebsites.net/</url>
	            <layout>default</layout>
	            <snapshots>
                	<enabled>true</enabled>
            	</snapshots>
        	</repository>
        	<repository>
	            <id>oss-snapshots</id>
	            <name>Open Source Snapshots</name>
	            <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
	            <layout>default</layout>
	            <snapshots>
	                <enabled>true</enabled>
	                <updatePolicy>always</updatePolicy>
	            </snapshots>
        	</repository>
    	</repositories>
    	<dependencies>
	        <dependency>
	            <groupId>com.microsoft.azure</groupId>
	            <artifactId>azure-client-authentication</artifactId>
	            <version>1.0.0-20160513.000802-24</version>
	        </dependency>
	        <dependency>
	            <groupId>com.microsoft.azure</groupId>
	            <artifactId>azure-client-runtime</artifactId>
	            <version>1.0.0-20160513.000812-28</version>
	        </dependency>
	        <dependency>
	            <groupId>com.microsoft.rest</groupId>
	            <artifactId>client-runtime</artifactId>
	            <version>1.0.0-20160513.000825-29</version>
	        </dependency>
	        <dependency>
	            <groupId>com.microsoft.azure</groupId>
	            <artifactId>azure-mgmt-datalake-store</artifactId>
	            <version>1.0.0-SNAPSHOT</version>
	        </dependency>
    	</dependencies>


4. Accédez à **Fichier**, puis **Paramètres**, puis **Build, Execution, and Deployment** (Génération, exécution et déploiement). Développez **Outils de génération**, **Maven**, puis **Importation**. Cochez la case **Importer les projets Maven automatiquement**. Cliquez sur **Appliquer**, puis sur **OK**.

5. Dans le volet de gauche, accédez à **src**, **principal**, **java**, **<nom du package>**, puis ouvrez **Main.java** et remplacez le bloc de code existant par le code suivant. Renseignez également les valeurs des paramètres inclus dans l’extrait de code, tels que **localFolderPath**, **\_adlsAccountName**, **\_resourceGroupName** et remplacez les espaces réservés pour **CLIENT-ID**, **CLIENT-SECRET**, **TENANT-ID** et **SUBSCRIPTION-ID**.

    Ce code déroule le processus de création d’un compte Data Lake Store, de création de fichiers dans le magasin, de concaténation de fichiers, de téléchargement d’un fichier et enfin de suppression du compte.

        package com.company;
        
        import com.microsoft.azure.CloudException;
        import com.microsoft.azure.credentials.ApplicationTokenCredentials;
        import com.microsoft.azure.management.datalake.store.*;
        import com.microsoft.azure.management.datalake.store.models.*;
        import com.microsoft.rest.credentials.ServiceClientCredentials;
        import java.io.*;
        import java.nio.charset.Charset;
        import java.util.ArrayList;
        import java.util.List;
        
        public class Main {
            private static String _adlsAccountName;
            private static String _resourceGroupName;
            private static String _location;
        
            private static String _tenantId;
            private static String _subId;
            private static String _clientId;
            private static String _clientSecret;
        
            private static DataLakeStoreAccountManagementClient _adlsClient;
            private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
        
            public static void main(String[] args) throws Exception {
                _adlsAccountName = "<DATA-LAKE-STORE-NAME>";
                _resourceGroupName = "<RESOURCE-GROUP-NAME>";
                _location = "East US 2";
        
                _tenantId = "<TENANT-ID>";
                _subId =  "<SUBSCRIPTION-ID>";
                _clientId = "<CLIENT-ID>";
        
                _clientSecret = "<CLIENT-SECRET>"; // TODO: For production scenarios, we recommend that you replace this line with a more secure way of acquiring the application client secret, rather than hard-coding it in the source code.
        
                String localFolderPath = "C:\\local_path\"; // TODO: Change this to any unused, new, empty folder on your local machine.
        
                // Authenticate
                ApplicationTokenCredentials creds = new ApplicationTokenCredentials(_clientId, _tenantId, _clientSecret, null);
                SetupClients(creds);
        
                // Create Data Lake Store account
                WaitForNewline("Authenticated.", "Creating NEW account.");
                CreateAccount();
                WaitForNewline("Account created.", "Displaying account(s).");
        
                // List Data Lake Store accounts that this app can access
                System.out.println(String.format("All ADL Store accounts that this app can access in subscription %s:", _subId));
                List<DataLakeStoreAccount> adlsListResult = _adlsClient.getAccountOperations().list().getBody();
                for (DataLakeStoreAccount acct : adlsListResult) {
                    System.out.println(acct.getName());
                }
                WaitForNewline("Account(s) displayed.", "Creating files.");
        
                // Create two files in Data Lake Store: file1.csv and file2.csv
                CreateFile("/file1.csv", "123,abc", true);
                CreateFile("/file2.csv", "456,def", true);
                WaitForNewline("Files created.", "Concatenating files.");
        
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
        
                _adlsClient.setSubscriptionId(_subId);
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
                adlsParameters.setLocation(_location);
        
                _adlsClient.getAccountOperations().create(_resourceGroupName, _adlsAccountName, adlsParameters);
            }
        
            // Create file
            public static void CreateFile(String path) throws IOException, CloudException {
                _adlsFileSystemClient.getFileSystemOperations().create(_adlsAccountName, path);
            }
        
            // Create file with contents
            public static void CreateFile(String path, String contents, boolean force) throws IOException, CloudException {
                byte[] bytesContents = contents.getBytes();
        
                _adlsFileSystemClient.getFileSystemOperations().create(_adlsAccountName, path, bytesContents, force);
            }
        
            // Append to file
            public static void AppendToFile(String path, String contents) throws IOException, CloudException {
                byte[] bytesContents = contents.getBytes();
        
                _adlsFileSystemClient.getFileSystemOperations().append(_adlsAccountName, path, bytesContents);
            }
        
            // Concatenate files
            public static void ConcatenateFiles(List<String> srcFilePaths, String destFilePath) throws IOException, CloudException {
                _adlsFileSystemClient.getFileSystemOperations().concat(_adlsAccountName, destFilePath, srcFilePaths);
            }
        
            // Delete concatenated file
            public static void DeleteFile(String filePath) throws IOException, CloudException {
                _adlsFileSystemClient.getFileSystemOperations().delete(_adlsAccountName, filePath);
            }
        
            // Get file or directory info
            public static FileStatusProperties GetItemInfo(String path) throws IOException, CloudException {
                return _adlsFileSystemClient.getFileSystemOperations().getFileStatus(_adlsAccountName, path).getBody().getFileStatus();
            }
        
            // List files and directories
            public static List<FileStatusProperties> ListItems(String directoryPath) throws IOException, CloudException {
                return _adlsFileSystemClient.getFileSystemOperations().listFileStatus(_adlsAccountName, directoryPath).getBody().getFileStatuses().getFileStatus();
            }
        
            // Download file
            public static void DownloadFile(String srcPath, String destPath) throws IOException, CloudException {
                InputStream stream = _adlsFileSystemClient.getFileSystemOperations().open(_adlsAccountName, srcPath).getBody();
        
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
                _adlsClient.getAccountOperations().delete(_resourceGroupName, _adlsAccountName);
            }
        }


6. Exécutez l'application. Suivez les invites pour exécuter et terminer l’application.

## Étapes suivantes

- [Sécuriser les données dans Data Lake Store](data-lake-store-secure-data.md)
- [Utiliser Azure Data Lake Analytics avec Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utiliser Azure HDInsight avec Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0615_2016-->