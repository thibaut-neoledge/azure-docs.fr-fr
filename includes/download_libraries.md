# Téléchargement du Kit de développement logiciel (SDK) Azure pour Java

## Bibliothèques clientes Azure pour Java - Téléchargement manuel

Les bibliothèques Azure pour Java sont distribuées sous la [Licence Apache, Version 2.0][Licence Apache, Version 2.0]. Cliquez [ici][ici] pour obtenir un fichier .zip contenant les bibliothèques et toutes les dépendances. Cela est proposé par Microsoft Open Technologies, Inc. Pour plus d’informations sur la licence, consultez les fichiers license.txt et ThirdPartyNotices.txt fournis dans le fichier .zip.

## Bibliothèques Azure pour Java - Maven

Si votre projet est déjà configuré pour être assemblé avec Maven, ajoutez la dépendance suivante au fichier pom.xml.

    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-management</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-management-compute</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-management-network</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-management-sql</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-management-storage</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-management-websites</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-media</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-servicebus</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-serviceruntime</artifactId>
        <version>n.n.n</version>
    </dependency>

Dans l'élément `<version>`, remplacez *n.n.n* par un numéro de version valide, que vous pouvez obtenir dans le [référentiel des bibliothèques Azure sur Maven][référentiel des bibliothèques Azure sur Maven].

  [Licence Apache, Version 2.0]: http://www.apache.org/licenses/LICENSE-2.0.html
  [ici]: http://go.microsoft.com/fwlink/?LinkId=253887
  [référentiel des bibliothèques Azure sur Maven]: http://go.microsoft.com/fwlink/?LinkID=286274
