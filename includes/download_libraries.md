## Bibliothèques clientes Azure pour Java - Téléchargement manuel

Les bibliothèques Azure pour Java sont distribuées sous la [Licence Apache, Version 2.0][license]. Cliquez [ici][zip-download] pour obtenir un fichier .zip contenant les bibliothèques et toutes les dépendances. Cela est proposé par Microsoft Open Technologies, Inc. Pour plus d’informations sur la licence, consultez les fichiers license.txt et ThirdPartyNotices.txt fournis dans le fichier .zip.

## Bibliothèques Azure pour Java - Maven

Si votre projet est déjà configuré pour être généré avec Maven, ajoutez la dépendance suivante au fichier pom.xml.

	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management</artifactId>
	    <version>0.7.0</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-compute</artifactId>
	    <version>0.7.0</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-network</artifactId>
	    <version>0.7.0</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-sql</artifactId>
	    <version>0.7.0</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-storage</artifactId>
	    <version>0.7.0</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-websites</artifactId>
	    <version>0.7.0</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-media</artifactId>
	    <version>0.6.0</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-servicebus</artifactId>
	    <version>0.7.0</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-serviceruntime</artifactId>
	    <version>0.6.0</version>
	</dependency>


Dans l’élément `<version>`, remplacez les numéros de version de cet exemple par des numéros de version valides, que vous pouvez obtenir dans le [référentiel des bibliothèques Azure sous Maven](http://go.microsoft.com/fwlink/?LinkID=286274).

[license]: http://www.apache.org/licenses/LICENSE-2.0.html
[zip-download]: http://go.microsoft.com/fwlink/?LinkId=253887

<!---HONumber=58_postMigration-->