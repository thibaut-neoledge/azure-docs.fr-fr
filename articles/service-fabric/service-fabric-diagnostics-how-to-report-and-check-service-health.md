<properties
   pageTitle="Signaler et contrôler l’intégrité avec Azure Service Fabric | Microsoft Azure"
   description="Découvrez comment envoyer des rapports d’intégrité à partir de votre code de service et comment contrôler l’intégrité de votre service avec les outils de contrôle d’intégrité fournis par Azure Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="kunaldsingh"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/05/2015"
   ms.author="kunalds"/>


# Signaler et contrôler l’intégrité du service
Lorsque vos services rencontrent des problèmes, votre capacité à réagir et à résoudre les incidents et les pannes induits dépend de la possibilité de détecter les problèmes rapidement. En signalant les problèmes et les pannes au gestionnaire de contrôle d’intégrité Service Fabric à partir de votre code de service, vous pouvez utiliser les outils standard de contrôle d’intégrité fournis par Service Fabric pour contrôler l’état d’intégrité.

Cet article vous présente un exemple d’ajout d’un rapport d’intégrité à un service et indique comment l’état d’intégrité peut être contrôlé à l’aide des outils fournis par Service Fabric. Cet article constitue une présentation rapide des fonctionnalités de contrôle d’intégrité dans Service Fabric. Pour plus d’informations, vous pouvez lire la série d’articles détaillés sur l’intégrité, à commencer par le lien situé à la fin de ce document.

## Composants requis
Les éléments suivants doivent être installés : * Visual Studio 2015 * Kit de développement logiciel (SDK) Service Fabric

## Pour déployer une application et contrôler son intégrité
Pour déployer une application et contrôler son intégrité, procédez comme suit :

1. Lancez Visual Studio en tant qu'administrateur.

2. Créez un projet pour un service avec état.

  ![Créer une application Service Fabric avec des services avec état](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)

3. Appuyez sur **F5** pour exécuter l’application en mode débogage. L’application est déployée sur le cluster local.

4. Une fois l’application exécutée, démarrez l’Explorateur Service Fabric en cliquant avec le bouton droit sur l’application de barre d’état système Gestionnaire du cluster local, et sélectionnez **Gérer le cluster local** dans le menu contextuel.

![Lancer l’Explorateur Service Fabric à partir de la barre d’état système](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)

5. L’intégrité de l’application doit s’afficher comme dans l’image ci-dessous. À ce stade, l’application doit être saine et sans erreurs.

  ![Application saine dans l’Explorateur Service Fabric](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)

6. Vous pouvez également contrôler l’intégrité à l’aide de PowerShell. Vous pouvez contrôler l’intégrité d’une application à l’aide de ```Get-ServiceFabricApplicationHealth``` et l’intégrité d’un service à l’aide de ```Get-ServiceFabricServiceHealth```. Le rapport d’intégrité pour la même application dans PowerShell se présente comme suit. ![Application saine dans PowerShell](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## Pour ajouter des événements d’intégrité personnalisés à votre code de service
Les modèles de projet Visual Studio de Service Fabric contiennent des exemples de code. Les étapes ci-dessous montrent comment vous pouvez créer des rapports sur des événements d’intégrité personnalisés à partir de votre code de service. Ces rapports apparaissent automatiquement dans les outils standard de contrôle d’intégrité fournis par Service Fabric, tels que l’Explorateur Service Fabric, la vue État d’intégrité du portail Azure, et PowerShell.

1. Rouvrez l’application créée précédemment dans Visual Studio ou créez une application à l’aide d’un service avec état à partir des modèles Visual Studio.
2. Ouvrez le fichier **Stateful1.cs**. Recherchez la déclaration pour `var myDictionary` et ajoutez le code ci-dessous juste après la déclaration `var myDictionary`. L’objet `fabricClient` créé ici sera utilisé plus tard pour générer un rapport d’intégrité.

    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```

    Ajoutez également cet espace de noms au fichier **Stateful1.cs**.

    ```csharp
    using System.Fabric;
    ```

4. Recherchez ensuite l’appel `myDictionary.TryGetValueAsync` dans la méthode *RunAsync*. Un `result` contenant la valeur actuelle du compteur est renvoyé, car la logique principale de cette application est de tenir un décompte. S’il s’agissait d’une application réelle et si l’absence de résultat représentait un échec, il faudrait le signaler au gestionnaire de contrôle d’intégrité.
5. Pour signaler comme un échec l’absence de résultat pour un événement d’intégrité, ajoutez le code ci-dessous après l’appel `myDictionary.TryGetValueAsync`. Nous signalons l’événement comme un `StatefulServiceReplicaHealthReport`, dans la mesure où il provient d’un service avec état. Les PartitionId et ReplicaId qui sont passés à l’événement de rapport permettent d’identifier la source de ce rapport lorsque vous le voyez dans un des outils de contrôle d’intégrité. C’est important, dans la mesure où un service avec état déployé peut comporter plusieurs partitions et chaque partition peut comporter plusieurs réplicas. Le paramètre `HealthInformation` stocke les informations relatives au problème d’intégrité signalé. Ajoutez cet espace de noms au fichier **Stateful1.cs**.

    ```csharp
    using System.Fabric.Health;
    ```

    Ajoutez le code suivant après l’appel `myDictionary.TryGetValueAsync`.

    ```csharp
    if(!result.HasValue)
    {
        var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.ServiceInitializationParameters.PartitionId,
            this.ServiceInitializationParameters.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));

        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```

5. Simulons cette panne et voyons comment elle s’affiche dans les outils de contrôle d’intégrité. Pour simuler la panne, commentez la première ligne dans le code de rapport d’intégrité ajouté précédemment. Une fois le commentaire ajouté à la première ligne, le code se présente comme suit. Ceci déclenchera le rapport d’intégrité à chaque exécution de RunAsync. Après avoir apporté la modification, appuyez sur **F5** pour exécuter l’application.

    ```csharp
    //if(!result.HasValue)
    {
        var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.ServiceInitializationParameters.PartitionId,
            this.ServiceInitializationParameters.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));

        var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```

6. Une fois que l’application est en cours d’exécution, ouvrez l’Explorateur Service Fabric pour vérifier l’intégrité de l’application. Cette fois-ci, l’Explorateur Service Fabric affiche un problème d’intégrité de l’application. Ceci est dû à l’erreur signalée à partir du code que nous avons ajouté précédemment. ![Application non saine dans l’Explorateur Service Fabric](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)

7. Si vous sélectionnez le réplica principal dans l’arborescence de l’Explorateur Service Fabric, vous verrez qu’il signale également un état d’erreur de l’intégrité. Il affiche en outre les détails du rapport d’intégrité qui ont été ajoutés au paramètre `HealthInformation` du code. Vous pouvez voir les mêmes rapports d’intégrité dans PowerShell, ainsi que dans le portail Azure. ![Intégrité du réplica dans l’Explorateur Service Fabric](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

Ce rapport sera conservé dans le gestionnaire de contrôle d’intégrité tant qu’il ne sera pas remplacé par un autre rapport ou que ce réplica ne sera pas supprimé. Étant donné que nous n’avons pas défini de paramètre TimeToLive pour ce rapport d’intégrité dans l’objet HealthInformation, il ne peut pas arriver à expiration. Pour pouvoir interroger et signaler l’intégrité, le FabricClient doit être dans un processus avec privilèges d’administrateur.

## Étapes suivantes
[Présentation approfondie de l’intégrité de Service Fabric](service-fabric-health-introduction.md)

<!---HONumber=AcomDC_1223_2015-->