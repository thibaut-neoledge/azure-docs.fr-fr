---
title: "Gérer plusieurs environnements dans Fabric Service | Microsoft Docs"
description: "Les applications Fabric Service peuvent être exécutées sur des clusters comportant entre une machine et des milliers. Dans certains cas, vous devez configurer votre application différemment pour les différents environnements. Cet article explique comment définir des paramètres d’application par l’environnement."
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: 
ms.assetid: f406eac9-7271-4c37-a0d3-0a2957b60537
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/06/2017
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: b57655c8041fa366d0aeb13e744e30e834ec85fa
ms.openlocfilehash: 7432e45ef33bd4d51fca8e8db8ec880e8beaf3ab


---
# <a name="manage-application-parameters-for-multiple-environments"></a>Gestion des paramètres d’application pour plusieurs environnements
Vous pouvez créer des clusters Service Fabric comportant n’importe quel nombre d’ordinateurs compris entre un et plusieurs milliers. Bien que les fichiers binaires d’application puissent s’exécuter sans modification dans ce large éventail d’environnements, il se peut que vous souhaitiez configurer l’application différemment selon le nombre d’ordinateurs sur lesquels vous la déployez.

À titre d’exemple, envisagez d’utiliser `InstanceCount` pour un service sans état. Lorsque vous exécutez des applications dans Azure, vous souhaitez généralement définir ce paramètre à la valeur spéciale « -1 ». Vous êtes ainsi certain que votre service s’exécute sur chaque nœud du cluster (ou tous les nœuds du type de nœud, si vous avez défini une contrainte de placement). Cependant, cette configuration ne convient pas à un cluster à une seule machine, car vous ne disposez pas de plusieurs processus à l’écoute du même point de terminaison sur une machine. Au lieu de cela, vous allez définir `InstanceCount` sur « 1 ».

## <a name="specifying-environment-specific-parameters"></a>Spécification des paramètres spécifiques à l’environnement
La solution à ce problème de configuration est un ensemble de services paramétrables par défaut et des fichiers de paramètres d’application qui remplissent ces valeurs de paramètre pour un environnement donné. Les services par défaut et paramètres d’application sont configurés dans les manifestes d’applications et de services. La définition de schéma pour les fichiers ServiceManifest.xml et ApplicationManifest.xml est installée avec le Kit de développement logiciel (SDK) Service Fabric et les outils sous *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

### <a name="default-services"></a>Services par défaut
Les applications Service Fabric sont constituées d’une collection d’instances de service. Bien qu’il vous soit possible de créer une application vide, puis toutes les instances de service de manière dynamique, la plupart des applications possèdent un ensemble de services de base qui doivent être créés systématiquement lors de l’instanciation de l’application. Ils sont nommés « services par défaut » et sont spécifiés dans le manifeste d’application avec des espaces réservés à la configuration par environnement inclus entre crochets :

```xml
    <DefaultServices>
        <Service Name="Stateful1">
            <StatefulService
                ServiceTypeName="Stateful1Type"
                TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]"
                MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">

                <UniformInt64Partition
                    PartitionCount="[Stateful1_PartitionCount]"
                    LowKey="-9223372036854775808"
                    HighKey="9223372036854775807"
                />
        </StatefulService>
    </Service>
  </DefaultServices>
```

Chacun des paramètres nommés doit être défini dans l’élément Paramètres du manifeste d’application :

```xml
    <Parameters>
        <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="2" />
        <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
        <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    </Parameters>
```

L’attribut DefaultValue spécifie la valeur à utiliser en l’absence d’un paramètre plus spécifique pour un environnement donné.

> [!NOTE]
> Tous les paramètres d’instance de service conviennent à la configuration par environnement. Dans l’exemple ci-dessus, les valeurs LowKey et HighKey du schéma de partitionnement du service sont explicitement définies pour toutes les instances du service dans la mesure où la plage de partition est une fonction du domaine de données et non de l’environnement.
> 
> 

### <a name="per-environment-service-configuration-settings"></a>Paramètres de configuration de service par environnement
Le [modèle d’application Service Fabric](service-fabric-application-model.md) permet aux services d’inclure des packages de configuration contenant des paires clé-valeur personnalisées lisibles lors de l’exécution. Les valeurs de ces paramètres peuvent également être différenciées par l’environnement en spécifiant un `ConfigOverride` dans le manifeste d’application.

Supposons que vous utilisiez le paramètre suivant dans le fichier Config\Settings.xml pour le service `Stateful1` :

```xml
    <Section Name="MyConfigSection">
      <Parameter Name="MaxQueueSize" Value="25" />
    </Section>
```
Pour remplacer cette valeur par une paire application/environnement spécifique, créez un `ConfigOverride` lors de l’importation du manifeste de service dans le manifeste d’application.

```xml
    <ConfigOverrides>
     <ConfigOverride Name="Config">
        <Settings>
           <Section Name="MyConfigSection">
              <Parameter Name="MaxQueueSize" Value="[Stateful1_MaxQueueSize]" />
           </Section>
        </Settings>
     </ConfigOverride>
  </ConfigOverrides>
```
Ce paramètre peut ensuite être configuré par environnement, comme indiqué ci-dessus. Vous pouvez réaliser cette opération par le biais d’une déclaration dans la section Paramètres du manifeste d’application et la spécification des valeurs spécifiques à l’environnement dans les fichiers de paramètres d’application.

> [!NOTE]
> Dans le cas de paramètres de configuration de service, il existe trois emplacements où la valeur d’une clé peut être définie : le package de configuration du service, le manifeste d’application et le fichier de paramètre d’application. Service Fabric choisira toujours dans le fichier de paramètres d’application en premier (s’il est spécifié), puis le manifeste d’application et enfin, le package de configuration.
> 
> 

### <a name="setting-and-using-environment-variables"></a>Définition et utilisation des variables d’environnement 
Vous pouvez spécifier et définir des variables d’environnement dans le fichier ServiceManifest.xml, puis les remplacer dans le fichier ApplicationManifest.xml instance par instance.
L’exemple suivant montre deux variables d’environnement, une avec un ensemble de valeurs et une autre qui sera ignorée. Vous pouvez utiliser les paramètres d’application pour définir les valeurs des variables d’environnement de la même manière que pour les substitutions de configuration.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="MyCode" Version="CodeVersion1">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
    <EnvironmentVariables>
      <EnvironmentVariable Name="MyEnvVariable" Value=""/>
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
    </EnvironmentVariables>
  </CodePackage>
  <ConfigPackage Name="MyConfig" Version="ConfigVersion1" />
  <DataPackage Name="MyData" Version="DataVersion1" />
</ServiceManifest>
```
Pour remplacer les variables d’environnement dans le fichier ApplicationManifest.xml, faites référence au package de code dans ServiceManifest avec l’élément `EnvironmentOverrides`.

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="FrontEndServicePkg" ServiceManifestVersion="1.0.0" />
    <EnvironmentOverrides CodePackageRef="MyCode">
      <EnvironmentVariable Name="MyEnvVariable" Value="mydata"/>
    </EnvironmentOverrides>
  </ServiceManifestImport>
 ``` 
 Une fois que l’instance de service nommé est créée, vous pouvez accéder aux variables d’environnement à partir du code. Par exemple, en C#, vous pouvez effectuer les opérations suivantes :

```csharp
    string EnvVariable = Environment.GetEnvironmentVariable("MyEnvVariable");
```

### <a name="service-fabric-environment-variables"></a>Variables d’environnement de Service Fabric
Des variables d’environnement définies pour chaque instance de service sont intégrées à Service Fabric. Vous trouverez ci-dessous la liste complète des variables d’environnement. Vous pourrez utiliser celles en gras dans votre service, tandis que les autres seront utilisées par le runtime Service Fabric. 

* Fabric_ApplicationHostId
* Fabric_ApplicationHostType
* Fabric_ApplicationId
* **Fabric_ApplicationName**
* Fabric_CodePackageInstanceId
* **Fabric_CodePackageName**
* **Fabric_Endpoint_[YourServiceName]TypeEndpoint**
* **Fabric_Folder_App_Log**
* **Fabric_Folder_App_Temp**
* **Fabric_Folder_App_Work**
* **Fabric_Folder_Application**
* Fabric_NodeId
* **Fabric_NodeIPOrFQDN**
* **Fabric_NodeName**
* Fabric_RuntimeConnectionAddress
* Fabric_ServicePackageInstanceId
* Fabric_ServicePackageName
* Fabric_ServicePackageVersionInstance
* FabricPackageFileName

Le code ci-dessous montre comment répertorier les variables d’environnement de Service Fabric.
 ```csharp
    foreach (DictionaryEntry de in Environment.GetEnvironmentVariables())
    {
        if (de.Key.ToString().StartsWith("Fabric"))
        {
            Console.WriteLine(" Environment variable {0} = {1}", de.Key, de.Value);
        }
    }
```
Vous trouverez ci-après un exemple de variables d’environnement pour un type d’application nommé `GuestExe.Application` avec un type de service appelé `FrontEndService` lorsque vous travaillez sur votre ordinateur de développement local.

* **Fabric_ApplicationName = fabric:/GuestExe.Application**
* **Fabric_CodePackageName = Code**
* **Fabric_Endpoint_FrontEndServiceTypeEndpoint = 80**
* **Fabric_NodeIPOrFQDN = localhost**
* **Fabric_NodeName = _Node_2**

### <a name="application-parameter-files"></a>Fichiers de paramètre d’application
Le projet d’application Service Fabric peut inclure un ou plusieurs fichiers de paramètres d’application. Chacun d’eux définit les valeurs spécifiques pour les paramètres qui sont définis dans le manifeste d’application :

```xml
    <!-- ApplicationParameters\Local.xml -->

    <Application Name="fabric:/Application1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <Parameters>
            <Parameter Name ="Stateful1_MinReplicaSetSize" Value="2" />
            <Parameter Name="Stateful1_PartitionCount" Value="1" />
            <Parameter Name="Stateful1_TargetReplicaSetSize" Value="3" />
        </Parameters>
    </Application>
```
Par défaut, une nouvelle application contient trois fichiers de paramètres, nommés Local.1Node.xml, Local.5Node.xml et Cloud.xml :

![Fichiers de paramètres d’application dans l’Explorateur de solutions][app-parameters-solution-explorer]

Pour créer un nouveau fichier de paramètres, il suffit de copier et coller fichier de paramètres existant et de lui donner un nouveau nom.

## <a name="identifying-environment-specific-parameters-during-deployment"></a>Identification des paramètres spécifiques à l’environnement au cours du déploiement
Au moment du déploiement, vous devez choisir le fichier de paramètres approprié à utiliser avec votre application. Vous pouvez le faire via la boîte de dialogue Publier de Visual Studio ou via PowerShell.

### <a name="deploy-from-visual-studio"></a>Déploiement depuis Visual Studio
Vous pouvez choisir dans la liste des fichiers de paramètres disponibles lors de la publication de votre application dans Visual Studio.

![Choisir un fichier de paramètres dans la boîte de dialogue Publier][publishdialog]

### <a name="deploy-from-powershell"></a>Déploiement depuis PowerShell
Le script PowerShell `Deploy-FabricApplication.ps1` inclus dans le modèle de projet d’application accepte un profil de publication en tant que paramètre et le profil PublishProfile contient une référence au fichier de paramètres d’application.

  ```PowerShell
    ./Deploy-FabricApplication -ApplicationPackagePath <app_package_path> -PublishProfileFile <publishprofile_path>
  ```

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur certains des principaux concepts abordés dans cette rubrique, consultez la [Présentation technique de Fabric Service](service-fabric-technical-overview.md). Pour plus d’informations sur les autres fonctionnalités de gestion d’application disponibles dans Visual Studio, consultez la section [Gestion de vos applications de Service Fabric dans Visual Studio](service-fabric-manage-application-in-visual-studio.md).

<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png



<!--HONumber=Feb17_HO2-->


