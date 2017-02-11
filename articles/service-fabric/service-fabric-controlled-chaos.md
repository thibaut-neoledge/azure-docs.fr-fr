---
title: Induire un chaos dans les clusters Service Fabric | Microsoft Docs
description: "Utilisation des API du service d’injection d’erreurs et d’analyse du cluster pour gérer le chaos dans le cluster."
services: service-fabric
documentationcenter: .net
author: motanv
manager: rsinha
editor: toddabel
ms.assetid: 2bd13443-3478-4382-9a5a-1f6c6b32bfc9
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/19/2017
ms.author: motanv
translationtype: Human Translation
ms.sourcegitcommit: 1a9bec270650223cd40b3b60e5bc9fc7e212a207
ms.openlocfilehash: ea8f76d146bd630cc8cb0a417d8c090b656150a4


---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Induire un chaos contrôlé dans les clusters Service Fabric
Les systèmes distribués à grande échelle, comme les infrastructures cloud, sont par définition peu fiables. Azure Service Fabric permet aux développeurs d’écrire des services fiables sur une infrastructure peu fiable. Pour écrire des services robustes, les développeurs doivent pouvoir introduire des erreurs dans une infrastructure peu fiable pour tester la stabilité des solutions qu’ils conçoivent.

Le service d’injection d’erreurs et d’analyse de cluster (ou Service d’analyse des erreurs) permet aux développeurs de provoquer des actions erronées afin de tester les services. Toutefois, les erreurs simulées ciblées présentent une efficacité limitée. Pour aller plus loin dans le test, vous pouvez utiliser la technique du chaos.

Au sein du cluster, le chaos simule des erreurs entrelacées en continu, avec et sans perte de données, sur des périodes prolongées. Une fois que vous avez configuré le chaos avec la fréquence et le type des erreurs, vous pouvez le démarrer ou l’arrêter à l’aide des API C# ou de PowerShell pour générer des erreurs au sein du cluster et de votre service.

Pendant que le chaos s’exécute, il génère différents événements qui capturent l’état de l’exécution à un moment précis. Par exemple, un ExecutingFaultsEvent contient toutes les erreurs qui sont en cours d’exécution dans cette itération. Un ValidationFailedEvent contient les détails d’une défaillance qui a été détectée lors de la validation du cluster. Vous pouvez appeler l’API GetChaosReportAsync pour obtenir un rapport sur les exécutions du chaos.

## <a name="faults-induced-in-chaos"></a>Erreurs introduites dans le chaos
Le chaos génère des erreurs dans l’ensemble du cluster Service Fabric et compresse, en quelques heures, les erreurs constatées au cours de plusieurs mois ou années. L’utilisation d’erreurs entrelacées avec un taux élevé d’erreurs permet d’identifier des dysfonctionnements qui n’auraient pu être isolés autrement. Cet exercice du chaos entraîne une amélioration significative de la qualité du code du service.

Le chaos introduit des erreurs à partir des catégories suivantes :

* Redémarrer un nœud
* Redémarrer un package de code déployé
* Supprimer un réplica
* Redémarrer un réplica
* Déplacer un réplica principal (configurable)
* Déplacer un réplica secondaire (configurable)

Le chaos s’exécute dans de nombreuses itérations. Chaque itération consiste en des validations de cluster et des erreurs pendant la période spécifiée. Vous pouvez configurer les délais de stabilisation du cluster et de validation. Si une défaillance est trouvée dans la validation du cluster, le chaos génère et fait persister un événement ValidationFailedEvent avec l’horodatage UTC et les détails de la défaillance.

Prenons, par exemple, une instance de chaos définie pour s’exécuter une heure, avec un maximum de trois erreurs simultanées. Le chaos introduit trois erreurs, puis valide l’intégrité du cluster. Il effectue une itération sur l’étape précédente jusqu'à ce qu’il soit explicitement arrêté par le biais de l’API StopChaosAsync ou jusqu’à ce qu’une heure se soit écoulée. Si le cluster présente un défaut d’intégrité dans l’une des itérations (dont, s’il n’est pas stabilisé dans le délai configuré), le chaos génère un ValidationFailedEvent. Cet événement indique qu’une erreur est survenue et qu’un examen approfondi est peut-être nécessaire.

Dans sa forme actuelle, le chaos déclenche uniquement des erreurs. Cela signifie qu’en l’absence d’erreurs externes, aucune perte de données ni de quorum ne survient.

## <a name="important-configuration-options"></a>Options de configuration importantes
* **TimeToRun** : durée totale d’exécution du chaos jusqu’à sa fin. Vous pouvez arrêter le chaos avant la fin de la période TimeToRun définie par le biais de l’API StopChaos.
* **MaxClusterStabilizationTimeout**: délai maximal nécessaire à la restauration de l’intégrité du cluster avant le test suivant. Cette attente permet de réduire la charge sur le cluster pendant qu’il récupère. Les vérifications effectuées sont les suivantes :
  * Si l’intégrité du cluster est OK
  * Si l’intégrité du service est OK
  * Si la taille du jeu de réplicas cible est atteinte pour la partition de service
  * Aucun réplica InBuild n’existe
* **MaxConcurrentFaults** : nombre maximal d’erreurs introduites simultanément dans chaque itération. Plus le nombre est élevé, plus le chaos est agressif. Cela entraîne des basculements et des combinaisons de transition plus complexes. Le chaos garantit qu’aucune perte de quorum ou de données ne sera à déplorer en l’absence d’erreurs externes, quelle que soit la valeur de la configuration.
* **EnableMoveReplicaFaults** : active ou désactive les erreurs provoquant le déplacement des réplicas primaires ou secondaires. Ces erreurs sont désactivées par défaut.
* **WaitTimeBetweenIterations** : délai d’attente entre les itérations, c’est-à-dire après une séquence d’erreurs et la validation correspondante.
* **WaitTimeBetweenFaults** : délai d’attente entre deux erreurs consécutives dans une itération.

## <a name="how-to-run-chaos"></a>Procédure d’exécution du chaos
**C# :**

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Fabric;

using System.Diagnostics;
using System.Fabric.Chaos.DataStructures;

class Program
{
    private class ChaosEventComparer : IEqualityComparer<ChaosEvent>
    {
        public bool Equals(ChaosEvent x, ChaosEvent y)
        {
            return x.TimeStampUtc.Equals(y.TimeStampUtc);
        }

        public int GetHashCode(ChaosEvent obj)
        {
            return obj.TimeStampUtc.GetHashCode();
        }
    }

    static void Main(string[] args)
    {
        var clusterConnectionString = "localhost:19000";
        using (var client = new FabricClient(clusterConnectionString))
        {
            var startTimeUtc = DateTime.UtcNow;
            var stabilizationTimeout = TimeSpan.FromSeconds(30.0);
            var timeToRun = TimeSpan.FromMinutes(60.0);
            var maxConcurrentFaults = 3;

            var parameters = new ChaosParameters(
                stabilizationTimeout,
                maxConcurrentFaults,
                true, /* EnableMoveReplicaFault */
                timeToRun);

            try
            {
                client.TestManager.StartChaosAsync(parameters).GetAwaiter().GetResult();
            }
            catch (FabricChaosAlreadyRunningException)
            {
                Console.WriteLine("An instance of Chaos is already running in the cluster.");
            }

            var filter = new ChaosReportFilter(startTimeUtc, DateTime.MaxValue);

            var eventSet = new HashSet<ChaosEvent>(new ChaosEventComparer());

            while (true)
            {
                var report = client.TestManager.GetChaosReportAsync(filter).GetAwaiter().GetResult();

                foreach (var chaosEvent in report.History)
                {
                    if (eventSet.add(chaosEvent))
                    {
                        Console.WriteLine(chaosEvent);
                    }
                }

                // When Chaos stops, a StoppedEvent is created.
                // If a StoppedEvent is found, exit the loop.
                var lastEvent = report.History.LastOrDefault();

                if (lastEvent is StoppedEvent)
                {
                    break;
                }

                Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
            }
        }
    }
}
```
**PowerShell :**

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

$events = @{}
$now = [System.DateTime]::UtcNow

Start-ServiceFabricChaos -TimeToRunMinute $timeToRun -MaxConcurrentFaults $concurrentFaults -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec

while($true)
{
    $stopped = $false
    $report = Get-ServiceFabricChaosReport -StartTimeUtc $now -EndTimeUtc ([System.DateTime]::MaxValue)

    foreach ($e in $report.History) {

        if(-Not ($events.Contains($e.TimeStampUtc.Ticks)))
        {
            $events.Add($e.TimeStampUtc.Ticks, $e)
            if($e -is [System.Fabric.Chaos.DataStructures.ValidationFailedEvent])
            {
                Write-Host -BackgroundColor White -ForegroundColor Red $e
            }
            else
            {
                if($e -is [System.Fabric.Chaos.DataStructures.StoppedEvent])
                {
                    $stopped = $true
                }

                Write-Host $e
            }
        }
    }

    if($stopped -eq $true)
    {
        break
    }

    Start-Sleep -Seconds 1
}

Stop-ServiceFabricChaos
```



<!--HONumber=Jan17_HO1-->


