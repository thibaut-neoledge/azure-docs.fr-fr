---
title: "Utiliser le Kit de développement logiciel (SDK) Java Data Lake Store pour développer des applications | Microsoft Docs"
description: "Utiliser le Kit de développement logiciel (SDK) Java Azure Data Lake Store pour développer des applications"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: d10e09db-5232-4e84-bb50-52efc2c21887
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/17/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 255e8def5179e544a4fb919774f3b18148566898


---
# <a name="get-started-with-azure-data-lake-store-using-java"></a>Prise en main d’Azure Data Lake Store à l’aide de Java
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

Découvrez comment utiliser le Kit de développement logiciel (SDK) Java Azure Data Lake Store pour effectuer des opérations de base comme créer des dossiers ou charger et télécharger des fichiers de données. Pour plus d’informations sur Data Lake, consultez [Azure Data Lake Store](data-lake-store-overview.md).

Vous pouvez accéder à la documentation de l’API du Kit de développement logiciel (SDK) Java pour Azure Data Lake Store dans la section [Documentation de l’API Java Azure Data Lake Store](https://azure.github.io/azure-data-lake-store-java/javadoc/).

## <a name="prerequisites"></a>Conditions préalables
* Kit de développement Java (JDK 7 ou version ultérieure, utilisant Java version 1.7 ou ultérieure)
* Compte Azure Data Lake Store. Suivez les instructions de [Prise en main d'Azure Data Lake Store avec le portail Azure](data-lake-store-get-started-portal.md).
* [Maven](https://maven.apache.org/install.html). Ce didacticiel utilise Maven pour les dépendances de build et de projet. Bien qu’il soit possible de créer sans utiliser un système de build comme Maven ou Gradle, ces systèmes facilitent considérablement la gestion des dépendances.
* (Facultatif) Et un environnement IDE comme [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) ou [Eclipse](https://www.eclipse.org/downloads/) ou similaire.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Comment s’authentifier à l’aide d’Azure Active Directory ?
Dans ce didacticiel, nous utilisons une clé secrète client d’application Azure AD pour récupérer un jeton Azure Active Directory (authentification de service à service). Nous utilisons ce jeton pour créer un objet client Data Lake Store afin d’effectuer des opérations sur les fichiers et les répertoires. Pour obtenir des instructions sur la façon de s’authentifier auprès d’Azure Data Lake Store à l’aide de la clé secrète client, nous allons effectuer les étapes générales suivantes :

1. Créer une application web Azure AD
2. Récupérez l’ID client, la clé secrète client et le point de terminaison du jeton pour l’application web Azure AD.
3. Configurez l’accès pour l’application web Azure AD sur le fichier/dossier Data Lake Store auquel vous souhaitez accéder à partir de l’application Java que vous créez.

Pour obtenir des instructions sur la façon d’effectuer ces étapes, consultez [Créer une application Active Directory](data-lake-store-authenticate-using-active-directory.md#create-an-active-directory-application).

Azure Active Directory offre d’autres options pour récupérer un jeton. Vous pouvez choisir parmi différents mécanismes d’authentification en fonction de votre scénario, par exemple, une application s’exécutant dans un navigateur, une application distribuée en tant qu’application de bureau, ou une application serveur exécutée localement ou dans une machine virtuelle Azure. Vous pouvez également choisir parmi différents types d’informations d’identification comme des mots de passe, des certificats, l’authentification à 2 facteurs, etc. En outre, Azure Active Directory vous permet de synchroniser vos utilisateurs d’Active Directory locaux avec le cloud. Pour plus de détails, consultez [Scénarios d’authentification pour Azure Active Directory](../active-directory/active-directory-authentication-scenarios.md). 

## <a name="create-a-java-application"></a>Création d’une application Java
L’exemple de code disponible [sur GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) vous guide tout au long du processus de création de fichiers dans le magasin, de concaténation de fichiers, de téléchargement d’un fichier et de suppression de certains fichiers du compte. Cette section de l’article vous guide tout au long des principales parties du code.

1. Créez un projet Maven en utilisant [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) à partir de la ligne de commande ou à l’aide d’un environnement IDE. Vous trouverez [ici](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html) des instructions sur la création d’un projet Java à l’aide d’IntelliJ. Vous trouverez [ici](http://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm) des instructions sur la création d’un projet à l’aide d’Eclipse. 
2. Ajoutez les dépendances suivantes à votre fichier Maven **pom.xml**. Ajoutez l’extrait de texte suivant entre la balise **\</version>** et la balise **\</project>** :
   
        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.0.4-SNAPSHOT</version>
          </dependency>
          <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>1.7.21</version>
          </dependency>
        </dependencies>
   
    La première dépendance consiste à utiliser le SDK Data Lake Store (`azure-datalake-store`) à partir du référentiel maven. La seconde dépendance (`slf4j-nop`) consiste à spécifier le framework de journalisation à utiliser pour cette application. Le SDK Data Lake Store utilise la façade de journalisation [slf4j](http://www.slf4j.org/), ce qui vous permet de choisir parmi plusieurs frameworks de journalisation populaires tels que log4j, la journalisation Java, Logback, etc., ou aucune journalisation. Pour cet exemple, nous allons désactiver la journalisation et, par conséquent, nous utilisons la liaison **slf4j-nop**. Vous trouverez [ici](http://www.slf4j.org/manual.html#projectDep) des informations pour utiliser d’autres options de journalisation.

### <a name="add-the-application-code"></a>Ajouter le code de l’application
Le code se compose de trois parties principales.

1. Obtenir le jeton Azure Active Directory
2. Utilisez le jeton pour créer un client Data Lake Store.
3. Utilisez le client Data Lake Store pour effectuer des opérations.

#### <a name="step-1-obtain-an-azure-active-directory-token"></a>Étape 1 : Obtenir un jeton Azure Active Directory.
Le SDK Data Lake Store propose des méthodes pratiques pour obtenir les jetons de sécurité nécessaires pour communiquer avec le compte Data Lake Store. Toutefois, le Kit de développement (SDK) n’impose pas d’utiliser ces seules méthodes. Vous pouvez utiliser tout autre moyen pour obtenir le jeton, par exemple à l’aide du [SDK Azure Active Directory](https://github.com/AzureAD/azure-activedirectory-library-for-java), ou de votre propre code personnalisé.

Pour utiliser le SDK Data Lake Store afin d’obtenir le jeton de l’application web Active Directory que vous avez créée précédemment, utilisez les méthodes statiques dans la classe `AzureADAuthenticator`. Remplacez **FILL-IN-HERE** (à remplir) par les valeurs réelles de l’application web Azure Active Directory.

    private static String clientId = "FILL-IN-HERE";
    private static String authTokenEndpoint = "FILL-IN-HERE";
    private static String clientKey = "FILL-IN-HERE";

    AzureADToken token = AzureADAuthenticator.getTokenUsingClientCreds(authTokenEndpoint, clientId, clientKey);

#### <a name="step-2-create-an-azure-data-lake-store-client-adlstoreclient-object"></a>Étape 2 : Créer un objet (ADLStoreClient) de client Azure Data Lake Store
La création d’un objet [ADLStoreClient](https://azure.github.io/azure-data-lake-store-java/javadoc/) vous oblige à spécifier le nom du compte Data Lake Store et du jeton Azure Active Directory que vous avez générés à l’étape précédente. Notez que le nom du compte Data Lake Store doit être un nom de domaine complet. Par exemple, remplacez **FILL-IN-HERE** (à remplir) par quelque chose comme **mydatalakestore.azuredatalakestore.net**.

    private static String accountFQDN = "FILL-IN-HERE";  // full account FQDN, not just the account name
    ADLStoreClient client = ADLStoreClient.createClient(accountFQDN, token);

### <a name="step-3-use-the-adlstoreclient-to-perform-file-and-directory-operations"></a>Étape 3 : Utiliser l’objet ADLStoreClient pour effectuer des opérations sur les fichiers et les répertoires
Le code ci-dessous contient des exemples d’extraits de certaines opérations courantes. Vous pouvez consulter toute la [documentation sur l’API SDK Java Data Lake Store](https://azure.github.io/azure-data-lake-store-java/javadoc/) de l’objet **ADLStoreClient** pour afficher d’autres opérations.

Notez que les fichiers sont lus et écrits en utilisant des flux Java standard. Cela signifie que vous pouvez superposer des flux Java sur des flux Data Lake Store pour bénéficier des fonctionnalités Java standard (par exemple, imprimer des flux pour une sortie mise en forme, ou n’importe quel flux de compression ou de chiffrement pour une fonctionnalité supplémentaire, etc.).

    // set file permission
    client.setPermission(filename, "744");

    // append to file
    stream = client.getAppendStream(filename);
    stream.write(getSampleContent());
    stream.close();

    // Read File
    InputStream in = client.getReadStream(filename);
    byte[] b = new byte[64000];
    while (in.read(b) != -1) {
        System.out.write(b);
    }
    in.close();

    // concatenate the two files into one
    List<String> fileList = Arrays.asList("/a/b/c.txt", "/a/b/d.txt");
    client.concatenateFiles("/a/b/f.txt", fileList);

    //rename the file
    client.rename("/a/b/f.txt", "/a/b/g.txt");

    // list directory contents
    List<DirectoryEntry> list = client.enumerateDirectory("/a/b", 2000);
    System.out.println("Directory listing for directory /a/b:");
    for (DirectoryEntry entry : list) {
        printDirectoryInfo(entry);
    }

    // delete directory along with all the subdirectories and files in it
    client.deleteRecursive("/a");

#### <a name="step-4-build-and-run-the-application"></a>Étape 4 : Générer et exécuter l’application
1. Pour une exécution au sein d’un environnement IDE, recherchez et appuyez sur le bouton **Run** (Exécuter). Pour une exécution depuis Maven, utilisez [exec:exec](http://www.mojohaus.org/exec-maven-plugin/exec-mojo.html).
2. Pour produire un fichier jar autonome que vous pouvez exécuter à partir de la ligne de commande, créez le fichier jar en incluant toutes les dépendances à l’aide du [plug-in assembly Maven](http://maven.apache.org/plugins/maven-assembly-plugin/usage.html). Le fichier pom.xml dans l[’exemple de code source sur github](https://github.com/Azure-Samples/data-lake-store-java-upload-download-get-started/blob/master/pom.xml) contient un exemple expliquant comment effectuer cette opération.

## <a name="next-steps"></a>Étapes suivantes
* [Sécuriser les données dans Data Lake Store](data-lake-store-secure-data.md)
* [Utiliser Azure Data Lake Analytics avec Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Utiliser Azure HDInsight avec Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)




<!--HONumber=Nov16_HO2-->


