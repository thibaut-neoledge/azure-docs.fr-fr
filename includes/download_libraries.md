#Téléchargement du Kit de développement logiciel (SDK) Azure pour Java

##Bibliothèques clientes Azure pour Java - Téléchargement manuel

Les bibliothèques Azure pour Java sont distribuées sous la [Licence Apache, Version 2.0][licence]. Cliquez [ici][téléchargement-zip] pour obtenir un fichier .zip contenant les bibliothèques et toutes les dépendances.  Cela est proposé par Microsoft Open Technologies, Inc. Pour plus d'informations sur la licence, consultez les fichiers license.txt et ThirdPartyNotices.txt fournis dans le fichier .zip.

##Bibliothèques Azure pour Java - Mavenn

Si votre projet est déjà configuré pour être assemblé avec Maven, ajoutez la dépendance suivante au fichier pom.xml.

	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-compute</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-network</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-sql</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-storage</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-websites</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-media</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-servicebus</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-serviceruntime</artifactId>
	    <version>n.n.n</version>
	</dependency>



Dans l'élément `<version>`, remplacez *n.n.n* par un numéro de version valide, que vous pouvez obtenir dans le [référentiel des bibliothèques Azure sur Maven](http://go.microsoft.com/fwlink/?LinkID=286274).

[licence]: http://www.apache.org/licenses/LICENSE-2.0.html
[téléchargement zip]:  http://go.microsoft.com/fwlink/?LinkId=253887

<!--HONumber=42-->
