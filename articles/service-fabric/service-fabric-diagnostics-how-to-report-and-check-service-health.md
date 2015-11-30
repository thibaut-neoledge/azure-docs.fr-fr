<properties
   pageTitle="Service Fabric - Comment signaler et vérifier l’intégrité | Microsoft Azure"
   description="Cet article décrit comment envoyer des rapports d’intégrité à partir du code de votre service et comment vérifier l’intégrité de votre service avec les outils d’analyse du fonctionnement fournis avec Azure Service Fabric."
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


# Création de rapports et vérification de l’intégrité du service
Lorsque vos services rencontrent des problèmes, votre capacité de réaction et de correction des incidents et des pannes dépend de votre capacité à détecter rapidement les problèmes. En signalant les problèmes et les échecs au gestionnaire de contrôle d’intégrité Service Fabric à partir du code de votre service, vous pouvez utiliser les outils d’analyse de fonctionnement standard fournis par Service Fabric pour vérifier l’intégrité. Ce document vous guide dans un exemple d’ajout d’un rapport d’intégrité à un service et vous montre comment l’intégrité peut être vérifiée à l’aide des outils fournis par Service Fabric. Cet article est une courte introduction aux fonctions d’analyse du fonctionnement dans Service Fabric. Pour des informations plus détaillées, consultez la série d’articles détaillés sur l’intégrité, en commençant par le lien à la fin de ce document.

## Composants requis
Les éléments suivants doivent être installés : * Visual Studio 2015 * Kit de développement logiciel (SDK) Service Fabric

## Déployer une application et vérifier son intégrité
Pour déployer une application et vérifier son intégrité, procédez comme suit :

1. Lancez Visual Studio en tant qu'administrateur.

2. Créez un projet pour un service avec état.

  ![Créer une application Service Fabric avec des services avec état](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)

3. Appuyez sur F5 pour exécuter l'application en mode débogage. L’application est déployée sur le cluster local.

4. Une fois que l’application s’exécute, lancez l’Explorateur Service Fabric en cliquant avec le bouton droit sur l’application du gestionnaire du cluster local dans la barre d’état système. Choisissez ensuite Gérer le cluster local dans le menu contextuel. ![Lancer l’Explorateur Service Fabric à partir de la barre d’état système]()

5. L’intégrité de l’application doit s’afficher comme dans l’image ci-dessous. À ce stade, l’application doit être saine, sans erreurs.

  ![Application saine dans l’Explorateur Service Fabric](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)

6. Vous pouvez également vérifier l’intégrité avec PowerShell. Vous pouvez vérifier l’intégrité d’une application avec ```Get-ServiceFabricApplicationHealth``` et interroger l’intégrité du service avec ```Get-ServiceFabricServiceHealth```. Voici à quoi ressemble le rapport d’intégrité pour la même application dans PowerShell. ![Application saine dans PowerShell](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## Ajouter des événements d’intégrité personnalisés à votre code de service
Les modèles de projet Visual Studio de Service Fabric contiennent des exemples de code. Les étapes ci-dessous montrent comment vous pouvez créer des rapports sur des événements d’intégrité personnalisés à partir de votre code de service. Ces rapports s’afficheront automatiquement dans les outils standard d’analyse du fonctionnement fournis par Service Fabric, comme l’Explorateur Service Fabric, la vue d’intégrité du portail Azure et PowerShell.

1. Rouvrez l’application que vous avez créée précédemment dans Visual Studio ou créez une application avec un service avec état à partir des modèles Visual Studio.
2. Ouvrez le fichier **Stateful1.cs**. Recherchez la déclaration de `var myDictionary` et ajoutez le code ci-dessous après la déclaration `var myDictionary`. L’objet `fabricClient` créé ici sera utilisé ultérieurement pour créer le rapport d’intégrité.

    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```

    Ajoutez également cet espace de noms au fichier **Stateful1.cs**.

    ```csharp
    using System.Fabric;
    ```

4. Recherchez ensuite l’appel `myDictionary.TryGetValueAsync` dans la méthode *RunAsync*. Vous pouvez voir que ceci retourne un `result` qui contient la valeur actuelle du compteur, car la logique principale de cette application est de tenir un décompte. S’il s’agissait d’une application réelle et si l’absence de résultat représentait un échec, il faudrait le signaler au gestionnaire de contrôle d’intégrité.
5. Pour signaler comme un échec l’absence de résultat pour un événement d’intégrité, ajoutez le code ci-dessous après l’appel `myDictionary.TryGetValueAsync`. Nous signalons l’événement comme un `StatefulServiceReplicaHealthReport`, dans la mesure où il vient d’un service avec état. Les PartitionId et ReplicaId qui sont passés à l’événement de rapport permettent d’identifier la source de ce rapport lorsque vous le voyez dans un des outils d’analyse du fonctionnement, dans la mesure où un service avec état déployé peut avoir plusieurs partitions et chaque partition plusieurs réplicas. Le paramètre `HealthInformation` stocke les informations relatives au problème d’intégrité signalé. Ajoutez cet espace de noms au fichier **Stateful1.cs**.

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

5. Simulons cette panne et voyons comment elle s’affiche dans les outils d’analyse du fonctionnement. Pour simuler l’échec, ajoutez des marques de commentaires à la première ligne du code de déclaration d’intégrité ajouté précédemment. La première ligne de code devrait ressembler à ceci. Ceci déclenchera le rapport d’intégrité à chaque exécution de RunAsync. Après avoir apporté la modification, appuyez sur F5 pour exécuter l’application.

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

6. Une fois que l’application est en cours d’exécution, ouvrez l’Explorateur Service Fabric pour vérifier l’intégrité de l’application. Cette fois-ci, l’Explorateur Service Fabric affiche un problème d’intégrité de l’application. Ceci est dû à l’erreur qui a été signalée à partir du code que nous avons ajouté ci-dessus. ![Application non saine dans l’Explorateur Service Fabric](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)

7. Si vous sélectionnez le réplica principal dans l’arborescence de l’Explorateur Service Fabric, vous constatez qu’il indique que l’intégrité est mauvaise. Il affiche également les détails du rapport d’intégrité ajoutés au paramètre `HealthInformation` dans le code. Vous pouvez voir les mêmes rapports d’intégrité dans PowerShell, ainsi que dans le portail Azure. ![Intégrité du réplica dans l’Explorateur Service Fabric](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

Ce rapport restera dans le gestionnaire de contrôle d’intégrité jusqu’à ce qu’il soit remplacé par un autre rapport ou que ce réplica soit supprimé (*puisque nous n’avons pas défini de TimeToLive pour ce rapport d’intégrité dans l’objet HealthInformation afin qu’il n’expire jamais*). Pour pouvoir vérifier l’intégrité et créer des rapports, FabricClient doit se trouver dans un processus avec privilèges d’administrateur.

## Étapes suivantes
[Présentation approfondie de l’intégrité de Service Fabric](service-fabric-health-introduction.md)

<!---HONumber=Nov15_HO4-->