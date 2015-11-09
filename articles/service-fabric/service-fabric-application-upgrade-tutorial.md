<properties
   pageTitle="Didacticiel sur la mise à niveau d'une application Service Fabric"
   description="Cet article décrit pas à pas une expérience de mise à niveau d'une application Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/17/2015"
   ms.author="subramar"/>



# Didacticiel de mise à niveau d'application

L'approche de la mise à niveau la plus fréquemment utilisée et recommandée est la mise à niveau propagée surveillée. Service Fabric surveille l'intégrité de l'application en cours de mise à niveau sur la base d'un ensemble de stratégies de contrôle d'intégrité. Une fois que les applications figurant dans un domaine de mise à niveau ont été mises à niveau, Service Fabric évalue leur intégrité et détermine sur la base des stratégies de contrôle d'intégrité s'il convient de passer au domaine de mise à niveau suivant ou s'il est préférable de faire échouer la mise à niveau. Une mise à niveau surveillée des applications peut être effectuée à l'aide des API managées ou natives, PowerShell ou REST.

La mise à niveau propagée surveillée de Service Fabric permet à l'administrateur d'une application de configurer la stratégie d'évaluation d'intégrité que Service Fabric utilise pour déterminer si l'application est saine. En outre, elle permet également à l'administrateur de configurer l'action à entreprendre en cas d'échec de l'évaluation d'intégrité, telle qu'une restauration automatique. Cette section présente une procédure pas à pas de mise à niveau surveillée pour l'un des exemples du Kit de développement logiciel (SDK).

## Étape 1 : Créer et déployer l'exemple d'objets visuels

Pour effectuer cette procédure, ouvrez le projet dans Visual Studio, cliquez avec le bouton droit sur la solution et sélectionnez la commande Déployer dans l'élément de menu Service Fabric. Consultez l'article [Gestion de votre application Service Fabric dans Visual Studio](service-fabric-manage-application-in-visual-studio.md) pour plus d'informations. Il est également possible d'utiliser PowerShell.

> [AZURE.NOTE]Avant d'utiliser les commandes Service Fabric dans PowerShell, il convient de se connecter au cluster en utilisant l'applet de commande `Connect-ServiceFabricCluster`. De même, cela suppose que le cluster a déjà été configuré sur votre ordinateur local. Consultez l'article sur la [configuration de votre environnement de développement Service Fabric](service-fabric-get-started.md).

Après avoir créé le projet dans Visual Studio, il est possible d'utiliser la commande PowerShell **Copy-ServiceFabricApplicationPackage** pour copier le package d'application dans le magasin d'images, puis d'inscrire l'application dans le runtime de Service Fabric à l'aide de l'applet de commande **Register-ServiceFabricApplicationPackage** et enfin de démarrer une instance de l'application à l'aide de l'applet de commande **New-ServiceFabricApplication**. Ces trois étapes sont analogues à l'utilisation de l'élément de menu Déployer dans Visual Studio.

À présent, vous pouvez utiliser l'[Explorateur de Service Fabric pour afficher le cluster et l'application](service-fabric-visualizing-your-cluster.md). L'application possède un service web accessible dans Internet Explorer en tapant [http://localhost:80](http://localhost:80) dans la barre d'adresse. Vous devez normalement voir des objets visuels flottants en rotation à l'écran. En outre, vous pouvez utiliser **Get-ServiceFabricApplication** pour vérifier l'état de l'application.

## Étape 2 : Mettre à jour l'exemple d'objets visuels

Vous pouvez remarquer qu'avec la version qui a été déployée à l'étape 1, les objets visuels ne sont pas en rotation. Mettons à niveau cette application vers une application dans laquelle les objets visuels sont en rotation.

Sélectionnez le projet VisualObjects.DataService dans la solution VisualObjects et ouvrez le fichier VisualObjects.cs dans le projet. Dans ce fichier, accédez à la méthode `CreateMovedObject` et recherchez la ligne où la rotation des objets visuels est définie (recherchez les lignes suivantes dans la méthode `CreateMovedObject`). Commentez la ligne `nextObject.Rotation = 1` et supprimez les marques de commentaire de la ligne suivante. Votre code doit ressembler à ce qui suit après les modifications :

```c#
            //nextObject.Rotation = 1;

            // Once that's deployed, uncomment this line and upgrade the application:

             nextObject.Rotation = (nextObject.Rotation + 10) % 360;
```

Vous devez également mettre à jour le fichier *ServiceManifest.xml* (sous PackageRoot) du projet **VisualObjects.DataService**. Mettez à jour *CodePackage* et la version du service vers 2.0, et les lignes correspondantes dans le fichier *ServiceManifest.xml* doivent normalement ressembler à ce qui suit (les parties en surbrillance indiquent les modifications) :

```xml
<ServiceManifestName="VisualObjects.DataService"Version="2.0"xmlns="http://schemas.microsoft.com/2011/01/fabric"xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code"Version="2.0">
```

À présent, nous allons mettre à jour le fichier *ApplicationManifest.xml* (situé sous le projet **VisualObjects**, sous la solution **VisualObjects**) pour utiliser la version 2.0 du projet **VisualObjects.DataService** et également mettre à jour la version de l'application de 1.0.0.0 vers 2.0.0.0. Désormais, les lignes correspondantes dans le fichier *ApplicationManifest.xml* doivent être comme suit :

```xml
<ApplicationManifestxmlns:xsd="http://www.w3.org/2001/XMLSchema"xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"ApplicationTypeName="VisualObjects"ApplicationTypeVersion="2.0.0.0"xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.DataService"ServiceManifestVersion="2.0" />
```

À présent, générez le projet en sélectionnant le projet **VisualObjects**, en effectuant un clic droit et en sélectionnant Générer dans Visual Studio (si vous sélectionnez Régénérer tout, vous devrez peut-être mettre à jour les versions pour le projet **VisualObjects.WebService** aussi bien dans *ServiceManifest.xml* que dans *ApplicationManifest.xml*, étant donné que le code aura changé). À présent, nous allons empaqueter l'application mise à jour en cliquant avec le bouton droit sur le projet *VisualObjects*, en sélectionnant le menu Fabric Service et en choisissant Package. Cette action doit créer un package d'application susceptible d'être déployé. Votre application mise à jour est prête à être déployée dès maintenant.

## Étape 3 : Décider des stratégies de contrôle d'intégrité et des paramètres de mise à niveau

Vous devez vous familiariser avec les [paramètres de mise à niveau d'application](service-fabric-application-upgrade-parameters.md) et le [processus de mise à niveau](service-fabric-application-upgrade.md) pour bien comprendre les différents paramètres de mise à niveau, délais d'attente et critères d'intégrité appliqués. Pour cette procédure pas à pas, nous allons conserver le critère d'évaluation d'intégrité de service par défaut (et les valeurs recommandées) qui signifie que tous les services et instances doivent être _sains_ après la mise à niveau. Nous allons augmenter toutefois le paramètre *HealthCheckStableDuration* pour spécifier 60 secondes (de sorte que les services soient sains pendant au moins 20 secondes avant que la mise à niveau passe au domaine de mise à niveau suivant). Nous allons également définir *UpgradeDomainTimeout* sur 1 200 secondes et *UpgradeTimeout* sur 3 000 secondes. Enfin, définissons aussi *UpgradeFailureAction* de manière à restaurer, pour demander ainsi à Service Fabric de restaurer l'application à la version précédente en cas de problème quelconque pendant la mise à niveau. Ainsi, les paramètres de mise à niveau que nous spécifierons au démarrage de la mise à niveau (à l'étape 4) seront les suivants :

FailureAction = Rollback

HealthCheckStableDurationSec = 60

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000

## Étape 4 : Préparer l'application pour la mise à niveau

À présent, l'application est créée et prête à être mise à niveau. Si vous ouvrez une fenêtre PowerShell en tant qu'administrateur et tapez **Get-ServiceFabricApplication**, la commande doit vous informer que le Type d'application 1.0.0.0 de **VisualObjects** est en cours de déploiement. Le package d'application est stocké sous le chemin d'accès relatif suivant où vous avez décompressé le Kit de développement logiciel (SDK) Fabric Service : *Samples\\Services\\Stateful\\VisualObjects\\VisualObjects\\obj\\x64\\Debug*. Un dossier « Package » doit figurer dans ce répertoire. Il contient le package d'application. Vérifiez les horodatages pour vous assurer qu'il s'agit de la dernière version (et vous devrez peut-être également modifier les chemins d'accès en conséquence).

À présent, nous allons copier le package d'application mis à jour dans le magasin d'images de Service Fabric (où les packages d'application sont stockés par Service Fabric). Le paramètre *ApplicationPackagePathInImageStore* indique à Service Fabric où il peut trouver le package d'application. Nous avons placé l'application mise à jour dans « VisualObjects\_V2 » à l'aide de la commande suivante (vous devrez peut-être modifier de nouveau les chemins d'accès en conséquence).

```powershell
Copy-ServiceFabricApplicationPackage  -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package
-ImageStoreConnectionString fabric:ImageStore   -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

L'étape suivante consiste à inscrire l'application auprès de Service Fabric, ce qui peut être effectué à l'aide de la commande suivante :

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

Si la commande ci-dessus ne réussit pas, il est probable que vous ayez besoin d'une régénération de tous les services. Comme cela est mentionné à l'étape 2, vous devrez peut-être mettre à jour également votre version de WebService.

## Étape 5 : Démarrer la mise à niveau de l'application

À présent, nous sommes prêts à démarrer la mise à niveau de l'application en utilisant la commande suivante :

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


Notez que le nom de l'application est tel qu'il a été décrit dans le fichier *ApplicationManifest.xml*. Service Fabric utilise ce nom pour identifier l'application à mettre à niveau. Si vous définissez des délais d'attente trop courts, vous pouvez recevoir un message d'échec stipulant le problème. Reportez-vous à la section de résolution des problèmes ou augmentez les délais d'attente.

Vous pouvez désormais surveiller la progression de la mise à niveau de l'application à l'aide de l'Explorateur de Service Fabric ou à l'aide de la commande PowerShell suivante : **Get-ServiceFabricApplicationUpgrade fabric:/VisualObjects**.

Après quelques minutes, l'état utilisant la commande PowerShell ci-dessus doit indiquer que tous les domaines de mise à niveau ont été mis à niveau (terminés). De plus, vous pouvez normalement noter que les objets visuels sont désormais en rotation dans la fenêtre de votre navigateur.

Vous souhaiterez peut-être essayer de modifier les versions et de passer de la version 2 à la version 3 en guise d'exercice, voire même de revenir de la version 2 à la version 1 (oui, vous pouvez effectuer une mise à niveau de v2 à v1). Amusez-vous avec les délais d'attente et les stratégies de contrôle d'intégrité pour vous y familiariser. Quand vous effectuez un déploiement vers un cluster Azure, les paramètres utilisés sont différents de ceux qui fonctionnaient lors du déploiement vers un cluster local. Il est recommandé de définir les délais d'attente de manière prudente.


## Étapes suivantes

[Paramètres de mise à niveau](service-fabric-application-upgrade-parameters.md)

[Sérialisation des données](service-fabric-application-upgrade-data-serialization.md)

[Rubriques avancées](service-fabric-application-upgrade-advanced.md)

[Résolution des problèmes de mise à niveau d'une application](service-fabric-application-upgrade-troubleshooting.md)
 

<!---HONumber=Nov15_HO1-->