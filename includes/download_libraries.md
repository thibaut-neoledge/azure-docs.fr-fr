# Téléchargement du Kit de développement logiciel (SDK) Azure pour Java
## Bibliothèques clientes Azure pour Java - Téléchargement manuel

Les bibliothèques Azure pour Java sont distribuées sous la [Licence
Apache, Version 2.0][1]. Cliquez [ici][2] pour obtenir un fichier .zip
contenant les bibliothèques et toutes les dépendances. Cela est proposé
par Microsoft Open Technologies, Inc. Pour plus d'informations sur la
licence, consultez les fichiers license.txt et ThirdPartyNotices.txt
fournis dans le fichier .zip.
## Bibliothèques Azure pour Java - Maven

Si votre projet est déjà configuré pour être assemblé avec Maven,
ajoutez la dépendance suivante au fichier pom.xml.

    <dependency>
    	<groupId>com.microsoft.windowsazure</groupId>
    	<artifactId>microsoft-windowsazure-api</artifactId>
    	<version>n.n.n</version>
    </dependency>

Dans l'élément `<version>`, remplacez *n.n.n* par un
numéro de version valide, que vous pouvez obtenir dans le [référentiel
des bibliothèques Azure sous Maven][3].



[1]: http://www.apache.org/licenses/LICENSE-2.0.html
[2]: http://go.microsoft.com/fwlink/?LinkId=253887
[3]: http://go.microsoft.com/fwlink/?LinkID=286274
