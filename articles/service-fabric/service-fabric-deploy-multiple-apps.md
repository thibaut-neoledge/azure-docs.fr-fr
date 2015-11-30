<properties
   pageTitle="Déploiement d'une application Node.js à l'aide de MongoDB | Microsoft Azure"
   description="Procédure détaillée indiquant comment empaqueter plusieurs applications à déployer sur un cluster Azure Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="bscholl"
   manager=""
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/09/2015"
   ms.author="bscholl"/>


# Déploiement de plusieurs applications existantes

Cet article explique comment empaqueter et déployer plusieurs applications Service Fabric à l'aide de la version préliminaire de l'outil d'empaquetage Service Fabric, qui est disponible à l'adresse http://aka.ms/servicefabricpacktool.

Pour créer un package Service Fabric manuellement, lisez l'article [Déploiement d'une application existante dans Azure Service Fabric](service-fabric-deploy-existing-app.md).

Bien que cette procédure montre pas à pas comment déployer une application avec un serveur frontal Node.js utilisant MongoDB comme magasin de données, vous pouvez appliquer les étapes à n'importe quelle application qui a des dépendances dans une autre application.

## Empaquetage de l'application Node.js

Cet article suppose que Node.js n'est pas installé sur les nœuds du cluster Service Fabric. Par conséquent, vous devez ajouter node.exe dans le répertoire racine de votre application de nœud avant l'empaquetage. La structure de répertoires de l'application Node.js (avec la structure web Express et le moteur de création de modèles Jade) doit ressembler à ce qui suit :

```
|-- NodeApplication
	|-- bin
        |-- www
	|-- node_modules
        |-- .bin
        |-- express
        |-- jade
        |-- etc.
	|-- public
        |-- images
        |-- etc.
	|-- routes
        |-- index.js
        |-- users.js
    |-- views
        |-- index.jade
        |-- etc.
    |-- app.js
    |-- package.json
    |-- node.exe
```

Ensuite, vous créez un package d'application pour l'application Node.js. Le code suivant crée un package d'application Service Fabric qui contient l'application Node.js.

```
.\ServiceFabricAppPackageUtil.exe /source:'[yourdirectory]\MyNodeApplication' /target:'[yourtargetdirectory] /appname:NodeService /exe:'node.exe' /ma:'bin/www' /AppType:NodeAppType
```

Voici une description des paramètres utilisés :

- **/source** : pointe vers le répertoire de l'application qui doit être empaquetée.
- **/target** : définit le répertoire dans lequel le package doit être créé. Ce répertoire doit être différent du répertoire cible.
- **/appname** : définit le nom de l'application existante. Il est important de comprendre que cela se traduit par le nom du service dans le manifeste et non le nom de l'application Service Fabric.
- **/exe** : définit l'exécutable que Service Fabric est supposé lancer, dans ce cas `node.exe`.
- **/ma** : définit l'argument qui est utilisé pour lancer l'exécutable. Comme Node.js n'est pas installé, Service Fabric doit lancer le serveur web Node.js en exécutant `node.exe bin/www`. `/ma:'bin/www'` indique à l'outil d'empaquetage d'utiliser `bin/ma` comme argument pour node.exe.
- **/AppType** : définit le nom du type d'application Service Fabric. Si

Si vous accédez au répertoire qui a été spécifié dans le paramètre /target, vous pouvez voir que l'outil a créé un package Service Fabric entièrement fonctionnel, comme illustré ci-dessous :

```
|--[yourtargetdirectory]
    |-- NodeApplication
        |-- C
		      |-- bin
              |-- data
              |-- node_modules
              |-- public
              |-- routes
              |-- views
              |-- app.js
              |-- package.json
              |-- node.exe
        |-- config
		      |--Settings.xml
	    |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```
Le fichier ServiceManifest.xml généré contient maintenant une section qui décrit la façon dont le serveur web Node.js doit être lancé comme indiqué dans l'extrait de code ci-dessous :

```xml
<CodePackage Name="C" Version="1.0">
    <EntryPoint>
        <ExeHost>
            <Program>node.exe</Program>
            <Arguments>'bin/www'</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
        </ExeHost>
    </EntryPoint>
</CodePackage>
```
Dans cet exemple, le serveur web Node.js écoute sur le port 3000. Vous devez donc mettre à jour les informations du point de terminaison dans le fichier ServiceManifest.xml comme indiqué ci-dessous.

```xml
<Resources>
      <Endpoints>
     	<Endpoint Name="NodeServiceEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
</Resources>
```
Maintenant que vous avez empaqueté l'application Node.js, vous pouvez poursuivre et empaqueter MongoDB. Comme indiqué précédemment, les étapes que vous suivez maintenant ne sont pas spécifiques à Node.js et MongoDB. En fait, elles s'appliquent à toutes les applications qui sont destinées à être empaquetées ensemble comme application Service Fabric.

Pour empaqueter MongoDB, vous devez vous assurer que vous empaquetez mongod.exe et mongo.exe. Les deux fichiers binaires se trouvent dans le répertoire `bin` du répertoire d'installation de MongoDB. La structure de répertoires est similaire à celle présentée ci-dessous.

```
|-- MongoDB
	|-- bin
        |-- mongod.exe
        |-- mongo.exe
        |-- etc.
```
Service Fabric doit démarrer MongoDB avec une commande semblable à celle ci-dessous. Vous devez donc utiliser le paramètre `/ma` lors de l'empaquetage de MongoDB.

```
mongod.exe --dbpath [path to data]
```
> [AZURE.NOTE]Les données ne sont pas conservées en cas de défaillance d'un nœud si vous placez le répertoire de données MongoDB sur le répertoire local du nœud. Vous devez utiliser un stockage durable ou implémenter un MongoDB ReplicaSet afin d'éviter la perte de données.

Dans PowerShell ou Command Shell, nous exécutons l'outil d'empaquetage avec les paramètres suivants :

```
.\ServiceFabricAppPackageUtil.exe /source: [yourdirectory]\MongoDB' /target:'[yourtargetdirectory]' /appname:MongoDB /exe:'bin\mongod.exe' /ma:'--dbpath [path to data]' /AppType:NodeAppType
```

Afin d'ajouter MongoDB à votre package d'application Service Fabric, vous devez vous assurer que le paramètre /target pointe vers le même répertoire que celui qui contient déjà le manifeste d'application, ainsi que l'application Node.js et que vous utilisez le même nom ApplicationType.

Accédons au répertoire et examinons ce que l'outil a créé.

```
|--[yourtargetdirectory]
    |-- MyNodeApplication
    |-- MongoDB
        |-- C
            |--bin
                |-- mongod.exe
                |-- mongo.exe
                |-- etc.
        |-- config
		    |--Settings.xml
	    |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```
Comme vous pouvez le voir, l'outil a ajouté un nouveau dossier MongoDB dans le répertoire qui contient les fichiers binaires de MongoDB. Si vous ouvrez le fichier `ApplicationManifest.xml`, vous pouvez voir que le package contient désormais à la fois l'application Node.js et MongoDB. Le code suivant montre le contenu du manifeste d'application.

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyNodeApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MongoDB" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeService" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MongoDBService">
         <StatelessService ServiceTypeName="MongoDB">
            <SingletonPartition />
         </StatelessService>
      </Service>
      <Service Name="NodeServiceService">
         <StatelessService ServiceTypeName="NodeService">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
</ApplicationManifest>  
```

La dernière étape consiste à publier l'application sur le cluster Service Fabric local à l'aide des scripts PowerShell ci-dessous :

```
Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath '[yourtargetdirectory]' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStore' -ApplicationPackagePathInImageStore 'Store\NodeAppType'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'Store\NodeAppType'

New-ServiceFabricApplication -ApplicationName 'fabric:/NodeApp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0  
```

Une fois que l'application est publiée avec succès sur le cluster local, vous pouvez accéder à l'application Node.js sur le port que nous avons entré dans le manifeste de service de l'application Node.js, par exemple http://localhost:3000.

Dans ce didacticiel, vous avez vu comment empaqueter facilement deux applications existantes sous la forme d'une seule application Service Fabric et comment la déployer vers Service Fabric afin qu'elle puisse bénéficier de certaines des fonctionnalités Service Fabric, telles que la haute disponibilité et l'intégration du système d'intégrité.

Pour plus d'informations, consultez les rubriques suivantes :

[Format d'empaquetage de Service Fabric](service-fabric-deploy-existing-app.md)

<!---HONumber=Nov15_HO4-->