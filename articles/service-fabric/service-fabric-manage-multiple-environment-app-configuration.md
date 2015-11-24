<properties
   pageTitle="Gérer plusieurs environnements dans Fabric Service | Microsoft Azure"
   description="Les applications Fabric Service peuvent être exécutées sur des clusters allant d’un ordinateur à des milliers. Dans certains cas, vous devez configurer votre application différemment pour les différents environnements. Cet article explique comment définir des paramètres d’application par l’environnement."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="coreysa"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/26/2015"
   ms.author="seanmck"/>

# Gestion des paramètres d’application pour plusieurs environnements

Les clusters de Service Fabric peuvent être formés à l’aide de n’importe quel nombre d’ordinateurs compris entre un et plusieurs milliers. Bien que les fichiers binaires d’application puissent s’exécuter sans modification dans ce large éventail d’environnements, il se peut que vous souhaitiez configurer l’application différemment selon le nombre d’ordinateurs que vous déployez.

À titre d’exemple, envisagez d’utiliser `InstanceCount` pour un service sans état. Lors de l’exécution dans Azure, vous devez généralement définir ce paramètre la valeur spéciale de -1, en veillant à ce que votre service s’exécute sur chaque nœud de cluster. Cette configuration ne convient pas à un cluster à un boîtier, cependant, comme vous ne disposez pas de plusieurs processus à l’écoute du même point de terminaison sur une machine. Au lieu de cela, vous allez définir le `InstanceCount` sur 1.

## Spécification des paramètres spécifiques à l’environnement

La solution est un ensemble de services paramétrables par défaut et des fichiers de paramètres d’application qui remplissent ces valeurs de paramètre pour un environnement donné.

### Services par défaut

Les applications Service Fabric sont constituées d’une collection d’instances de service. Bien qu’il soit possible de créer une application vide puis de créer toutes les instances de service de manière dynamique, la plupart des applications possèdent un ensemble de services de base qui doivent toujours être créés lorsque l’application est instanciée. Ceux-ci sont appelés « services par défaut » et sont spécifiés dans le manifeste d’application avec des espaces réservés pour la configuration par environnement inclus entre crochets :

    <DefaultServices>
        <Service Name="Stateful1">
            <StatefulService
                ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">

                <UniformInt64Partition
                    PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808"
                    HighKey="9223372036854775807"
                />
        </StatefulService>
    </Service>
  </DefaultServices>

Chacun des paramètres nommés doit être défini dans l’élément Paramètres du manifeste d’application :

    <Parameters>
        <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="2" />
        <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
        <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    </Parameters>

Les attributs DefaultValue spécifient la valeur à utiliser en l’absence d’un paramètre plus spécifique pour un environnement donné.

>[AZURE.NOTE]Tous les paramètres d’instance de service conviennent à la configuration par environnement. Dans l’exemple ci-dessus, les valeurs LowKey et HighKey du schéma de partitionnement du service sont explicitement définies pour toutes les instances du service dans la mesure où la plage de partition est une fonction de votre domaine de données et non de l’environnement.

### Paramètres de configuration de service par environnement

Le [modèle d’application Service Fabric](service-fabric-application-model.md) permet aux services d’inclure des packages de configuration contenant des paires clé-valeur personnalisées lisibles lors de l’exécution. Les valeurs de ces paramètres peuvent également être différenciées par l’environnement en spécifiant un `ConfigOverride` dans le manifeste d’application.

Supposez que vous utilisez le paramètre suivant dans le manifeste de service du service `Stateful1` :


    <Section Name="MyConfigSection">
      <Parameter Name="MaxQueueSize" Value="25" />
    </Section>

Pour remplacer cette valeur par une paire application/environnement spécifique, créez un `ConfigOverride` lors de l’importation du manifeste de service dans le manifeste d’application.

    <ConfigOverrides>
     <ConfigOverride Name="Config">
        <Settings>
           <Section Name="MyConfigSection">
              <Parameter Name="MaxQueueSize" Value="[Stateful1_MaxQueueSize]" />
           </Section>
        </Settings>
     </ConfigOverride>
  </ConfigOverrides>

Ce paramètre peut ensuite être configuré par l’environnement comme indiqué ci-dessus, par la déclaration dans la section Paramètres du manifeste d’application et la spécification des valeurs spécifiques à l’environnement dans les fichiers de paramètres d’application.

>[AZURE.NOTE]Dans le cas de paramètres de configuration de service, il existe trois emplacements où la valeur d’une clé peut être définie : le package de configuration du service, le manifeste d’application et le fichier de paramètres d’application. Service Fabric choisira toujours dans le fichier de paramètres d’application en premier (si spécifié), puis le manifeste d’application et enfin, le package de configuration.

### Fichiers de paramètre d’application

Le projet d’application Service Fabric peut inclure une ou plusieurs applications de fichiers de paramètres, chacune définissant les valeurs spécifiques pour les paramètres définis dans le manifeste d’application :

    <!-- ApplicationParameters\Local.xml -->

    <Application Name="fabric:/Application1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <Parameters>
            <Parameter Name ="Stateful1_MinReplicaSetSize" Value="2" />
            <Parameter Name="Stateful1_PartitionCount" Value="1" />
            <Parameter Name="Stateful1_TargetReplicaSetSize" Value="3" />
        </Parameters>
    </Application>

Par défaut, une nouvelle application comprend deux fichiers de paramètres, nommés Local.xml et Cloud.xml :

![Fichiers de paramètres d’application dans l’Explorateur de solutions][app-parameters-solution-explorer]

Pour créer un nouveau fichier de paramètres, il suffit de copier et coller fichier de paramètres existant et donnez-lui un nouveau nom.

## Identification des paramètres spécifiques à l’environnement au cours du déploiement

Au moment du déploiement, vous devez choisir le fichier de paramètres approprié à appliquer avec votre application. Vous pouvez le faire via la boîte de dialogue Publier dans Visual Studio ou dans PowerShell.

### Déploiement à partir de Visual Studio

Vous pouvez choisir dans la liste des fichiers de paramètres disponibles lors de la publication de votre application dans Visual Studio.

![Choisir un fichier de paramètres dans la boîte de dialogue Publier][publishdialog]

### Déploiement de PowerShell

Le script PowerShell `DeployCreate-FabricApplication.ps1` accepte un fichier de paramètres en tant que paramètre.

    ./DeployCreate-FabricApplication -ApplicationPackagePath <app_package_path> -ApplicationDefinitionFilePath <app_instance_definition_path>

## Étapes suivantes

Pour en savoir plus sur certains des principaux concepts abordés dans cette rubrique, consultez la [Présentation technique Fabric Service](service-fabric-technical-overview.md). Pour plus d’informations sur les autres fonctionnalités de gestion d’application disponibles dans Visual Studio, consultez la section [Gestion de vos applications de Service Fabric dans Visual Studio](service-fabric-manage-application-in-visual-studio.md).

<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]: ./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png

<!---HONumber=Nov15_HO4-->