<properties
   pageTitle="Node.js et acteurs fiables | Microsoft Azure"
   description="Examen de la création d’une application express node.js qui utilise des Reliable Actors et s’exécute sur la plateforme Azure Service Fabric."
   services="service-fabric"
   documentationCenter="nodejs"
   authors="clca"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="nodejs"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/17/2015"
   ms.author="claudioc"/>


# Node.js et acteurs fiables : une combinaison gagnante
Cet article explique commet générer une application qui utilise node.js et Reliable Actors. La solution finale est un mélange de code javascript utilisé principalement pour desservir le côté serveur frontal de l’application (API Web/Res) et C# pour des calculs plus complexes. Exploitant le modèle de programmation de Fabric Service, l’application est évolutive et fiable dès le départ. Le processus est basé sur les étapes suivantes :

1. créer un nouveau projet de Service Fabric Stateless or Stateful Actor en utilisant les outils de Service Fabric pour Visual Studio
2. Créer un projet node.js avec, par exemple les [outils node.js pour Visual Studio](https://github.com/Microsoft/nodejstools/releases/tag/v1.1.RC) 
3. Ajouter un projet node.js (une application de base 4 express par exemple) à la solution d’infrastructure Service Fabric. Vous pouvez simplement utiliser la solution/ajouter un projet existant pour inclure le projet node.js. 
4. Empaqueter l’application node.js pour qu’elle puisse être déployée à l’aide des outils Visual Studio Tools pour le Service Fabric

## création d’un projet node.js
Après avoir créé le projet node.js et ajouté les dépendances, vous devez modifier la structure de répertoire du projet pour qu’elle respecte la structure que les outils de Service Fabric pour Visual Studio requis afin d’être en mesure d’empaqueter et déployer l’application au même titre qu’un autre Service Fabric. L’objectif est d’apporter les modifications à la structure de répertoires seulement après que vous puissiez bénéficier d’utiliser le processus de déploiement dans Visual Studio et donc, déployer votre application node.js, ainsi que les autres services dans la solution. Les modifications à apporter la structure de répertoire sont :

1. la création d’un répertoire PackageRoot
2. la création d’un répertoire de code sous PackageRoot
3. le déplacement de tous les fichiers et répertoires sous le répertoire de code

Une fois que vous avez terminé, la structure de projet dans Visual Studio doit se présenter comme suit :

![][8]

Comme vous pouvez le voir, l’ensemble du code node.js se trouve sous le répertoire PackageRoot/Code. Service Fabric ne fait pas d’hypothèses concenant les applications/bibliothèques installées sur le nœud où l’application va être déployée, et vous devez donc inclure toutes les dépendances. En cas de node.js, vous devez inclure node.exe afin que Service Fabric puisse exécuter le code (vous trouverez node.exe sous le répertoire program files\\nodejs).

![][3]

## Ajouter le fichier de métadonnées servicemanifest.xml
Pour déployer l’application node.js, nous devons ajouter un fichier de métadonnées nécessaire pour spécifier des propriétés qui seront utilisées par l’infrastructure de Service Fabric afin de déterminer comment déployer et lancer l’application.

Il s’agit d’un exemple de la présentation servicemanifest.xml. Veuillez vous reporter à [cet article](service-fabric-deploy-existing-app.md) pour plus d’informations sur ce que sont les éléments importants à mettre à jour, mais en général, vous devez mettre à jour :

* Nom (élément ServiceManifest)
* ServiceTypeName (élément StatelessServiceType)
* Arguments (élément ExeHost) pour spécifier le fichier js qui doit être utilisé pour lancer l’application.


```xml

<?xml version="1.0" encoding="utf-8"?>

<ServiceManifest Name="NodejsFrontendPkg"
                 Version="1.0.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="NodejsFrontendType" UseImplicitHost="true" />
  </ServiceTypes>
  <CodePackage Name="Code" Version="1.0.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>node.exe</Program>
        <Arguments>server.js</Arguments>
        <WorkingFolder>CodeBase</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="Config" Version="1.0.0.0" />
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>

```

> [AZURE.NOTE]Veuillez noter que l’une des options servicemanifest.xml ne fonctionneront avec node.js. Dans certains cas, `ConsoleRedirection` ne fonctionne pas si un module node.js (par ex. Express) si l’on suppose que fd pour stdout et stderr sont 1 et 2.

Une fois que le fichier `servicemanifest.xml` est ajouté au projet node.js, la structure du projet doit ressembler à ce qui suit :

![][4]

## Ajouter les fichiers binaires Fabric Service
L’étape suivante consiste à ajouter les fichiers binaires Fabric Service qui sont utilisés pour se connecter avec les acteurs en cours d’exécution sur Service Fabrice. Comme vous pouvez le voir dans l’exemple Edge.js, il existe des références à des assemblages. Pour mettre ces assemblages à la disposition de edge.js, ils doivent être copiés avec le code node.js. Le moyen le plus simple consiste à copier les fichiers binaires présents dans les projets existants, les fichiers à inclure dans le répertoire de code (et qui seront utilisés par edge.js) sont les suivants :

```
Microsoft.ServiceFabric.Actors.dll
Microsoft.ServiceFabric.Collections.dll
Microsoft.ServiceFabric.Data.dll
Microsoft.ServiceFabric.Data.Log.dll
Microsoft.ServiceFabric.ReplicatedStore.dll
Microsoft.ServiceFabric.Replicator.dll
Microsoft.ServiceFabric.Services.dll
ServiceFabricServiceModel.dll
System.Fabric.Common.Internal.dll
System.Fabric.Common.Internal.Strings.resources.dll
System.Fabric.dll
```

Une fois que vous avez ajouté les fichiers binaires au projet, la structure de projet doit se présenter comme suit :

![][5]

## Ajoutez une référence au projet node.js dans le fichier applicationmanifest
L’étape suivante consiste à ajouter le service node.js au manifeste d’application afin qu’il puisse être déployé avec les outils Visual Studio pour le Service Fabric. Cela est nécessaire, car il n’existe aucune intégration aux outils Service Fabric pour Visual Studio avec le projet node.js, et ils doivent être ajouté manuellement.

Dans le fichier `applicationmanifest.xml`, vous devez ajouter les éléments suivants :

* `ServiceManifestImport`
* `Service`

> [AZURE.NOTE]Assurez-vous que vous utilisez les mêmes noms que vous avez utilisés dans `servicemanifest.xml` pour spécifier `ServiceName` et `ServiceTypeName`. Le fichier `applicationmanifest.xml` doit présenter ce qui suit :

```
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeServiceFabricSampleApplication" ApplicationTypeVersion="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
    <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeServiceFabricSamplePkg" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
   <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="VisualObjectsNodejsWebServicePkg" ServiceManifestVersion="1.0.0.0" />
       </ServiceManifestImport>
   <DefaultServices>
      <Service Name="NodeServiceFabricSampleActorService">
         <StatelessService ServiceTypeName="NodeServiceFabricSampleActorServiceType">
            <UniformInt64Partition PartitionCount="9" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatelessService>
      </Service>
     <Service Name="NodejsFrontendPkg">
       <StatelessService ServiceTypeName="NodejsFrontendType">
         <SingletonPartition />
       </StatelessService>
     </Service>
   </DefaultServices>
</ApplicationManifest>

```

## Utilisation de Reliable Actors dans l’application Node.js
Maintenant que la structure de projet est définie, nous pouvons nous concentrer sur le code qui autorise l’application node.js pour se connecter à Reliable Actors dans le cluster comme vous pouvez le faire à partir d’une application .NET. Le scénario que vous souhaitez activer consiste à générer une application node.js qui agit en tant que serveur frontal/passerelle pour l’application cliente et d’exposer une application basée sur le web ou un ensemble de rest API qu’un client peut utiliser. Node.js fournit le serveur frontal de l’application, alors que Reliable Actors offrent une couche évolutive et fiable de « serveur d’applications » de l’application. Dans Service Fabric, il est possible d’appeler Reliable Actors qui peuvent être appelées en utilisant [classe ActorProxy](service-fabric-reliable-actors-introduction.md#actor-communication). Il existe également un module node.js exceptionnel qui permet d’appeler un code .NET : [Edge.js](https://github.com/tjanczuk/edge). Vous pouvez utiliser les instructions sur le référentiel github pour savoir comment installer edge sur votre machine.

 
![][2]

Après avoir installé edge avec NPM ou l’interface NPM dans Visual Studio, vous devez déplacer le nouveau module installé dans le répertoire node\_modules sous le sous-répertoire de Code (nous avons modifié la structure du projet node.js et déplacé tout le code sous le répertoire PackageRoot/Code). Vous trouverez de bons exemples dans le référentiel edge.js sur Github sur la façon dont vous pouvez faire en sorte que edge appelle le code .NET, ce qui suit est un exemple simple de la façon dont se présente le code lorsque vous devez appeler Reliable Actors à l’aide de la classe ActorProxy.

```javascript

var getActorStatus = edge.func(function () {

/*

    #r "Microsoft.ServiceFabric.Actors.dll"
    #r "System.Globalization.dll"
    #r "System.dll"
    #r  "visualobjects.interfaces.dll"
    
    using Microsoft.ServiceFabric.Actors;
    using System.Globalization;
    using VisualObjects.Interfaces;
    using System.Threading.Tasks;
    using System; 


public class Startup
    {
         public async Task<object> Invoke(dynamic input)
        {

            var objectId = (string) input.actorId;
            var serviceUri = (string) input.serviceUri;
            var actId = new ActorId(objectId);
            var serId = new Uri(serviceUri);
            var actorProxy = ActorProxy.Create<IVisualObjectActor>(actId, serId);
            var buffer = await actorProxy.GetStateAsJsonAsync();
            if (!string.IsNullOrEmpty(buffer))
                return buffer;
                else 
                return "null";
         }
  }
 
 */ 

});

```

> [AZURE.NOTE]L’ActorProxy utilise l’interface Actor afin de savoir à quel acteur il doit se connecter. Pour que le code géré s’exécutant dans le processus node.js puisse instancier cette classe, l’assemblage d’interface Actor doit être copié dans le répertoire de code au même titre que les autres dlls Service Fabric. Remarque : vous devez copier (ou définir une action post génération dans VS) la dll à chaque vois que vous modifiez les paramètres/méthodes dans l’interface.

![][6]

## Déploiement
À ce stade, le projet peut être déployé à l’aide des outils Service Fabric pour Visual Studio. La dernière étape du processus consiste à référencer le projet dans le projet d’Application de Fabric Service afin qu’il puisse être inclus dans le package d’Application et déployés dans le cluster.

![][9]
 
Vous pouvez déployer l’application (ce qui inclut l’application node.js) en utilisant, par exemple, le menu contextuel de la solution.

![][10]

## Étapes suivantes
* En savoir plus sur les Collections fiables [Reliable Actors](service-fabric-reliable-actors-introduction.md)
* En savoir plus sur le [cycle de vie d’application](service-fabric-application-lifecycle.md)Service Fabric
* En savoir plus sur la mise à niveau d’[ application Service Fabric](service-fabric-application-upgrade.md). 

<!-- images -->
[1]: ./media/service-fabric-node-and-reliable-actors-app/nodejs-project-structure.PNG
[2]: ./media/service-fabric-node-and-reliable-actors-app/edge-js.PNG
[3]: ./media/service-fabric-node-and-reliable-actors-app/node-exe.PNG
[4]: ./media/service-fabric-node-and-reliable-actors-app/project-structure-with-manifest.PNG
[5]: ./media/service-fabric-node-and-reliable-actors-app/project-structure-with-dlls.PNG
[6]: ./media/service-fabric-node-and-reliable-actors-app/project-structure-interface-dll.PNG
[7]: ./media/service-fabric-node-and-reliable-actors-app/project-structure-config.PNG
[8]: ./media/service-fabric-node-and-reliable-actors-app/nodejs-project-structure1.PNG
[9]: ./media/service-fabric-node-and-reliable-actors-app/application-project-reference.PNG
[10]: ./media/service-fabric-node-and-reliable-actors-app/solution-deploy.PNG

<!---HONumber=Oct15_HO3-->