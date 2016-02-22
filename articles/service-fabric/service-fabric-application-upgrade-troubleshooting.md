<properties
   pageTitle="Résolution des problèmes des mises à niveau d'applications | Microsoft Azure"
   description="Cet article aborde certains problèmes courants relatifs à la mise à niveau d'une application Service Fabric et la manière de les résoudre."
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
   ms.date="02/04/2016"
   ms.author="subramar"/>

# Résoudre les problèmes de mise à niveau d'application

Cet article aborde certains des problèmes courants relatifs à la mise à niveau d’une application Azure Service Fabric et la manière de les résoudre.

## Résoudre les problèmes liés à l'échec de la mise à niveau d'une application

Quand une mise à niveau échoue, la sortie de la commande **Get-ServiceFabricApplicationUpgrade** contient des informations supplémentaires qui permettront de résoudre les problèmes à l'origine de l'échec. Ces informations peuvent servir à :

1. Identifier le type d’échec.
2. Identifier la raison de l’échec.
3. Isoler le ou les composants défectueux en vue d’un examen approfondi.

Ces informations sont disponibles dès que Service Fabric détecte l’échec, que la propriété **FailureAction** indique de restaurer ou de suspendre la mise à niveau.

### Identifier le type d'échec

Dans la sortie de **Get-ServiceFabricApplicationUpgrade**, **FailureTimestampUtc** identifie l’horodateur (au format UTC) correspondant à la détection de l’échec de la mise à niveau par Service Fabric et au déclenchement de l’opération **FailureAction**. **FailureReason** identifie l’une des trois raisons potentielles principales de l’échec :

1. UpgradeDomainTimeout : indique qu'un domaine de mise à niveau particulier a pris trop de temps et qu'**UpgradeDomainTimeout** a expiré.
2. OverallUpgradeTimeout : indique que la mise à niveau globale a pris trop de temps et qu'**UpgradeTimeout** a expiré.
3. HealthCheck : indique qu’après la mise à niveau d’un domaine de mise à jour, l’application est restée défectueuse selon les stratégies de contrôle d’intégrité spécifiées et que **HealthCheckRetryTimeout** a expiré.

Ces entrées apparaissent dans la sortie uniquement quand la mise à niveau échoue et que la restauration commence. Des informations supplémentaires s'affichent en fonction du type d'échec.

### Examiner les dépassements de délai d'attente de mise à niveau

Les échecs de délai d'attente de mise à niveau sont généralement dus à des problèmes de disponibilité de service. La sortie ci-dessous est typique des mises à niveau où des instances ou des réplicas de service ne peuvent pas démarrer dans la nouvelle version du code. Le champ **UpgradeDomainProgressAtFailure** capture un instantané de tout travail de mise à niveau en attente au moment de l'échec.

~~~
PS D:\temp> Get-ServiceFabricApplicationUpgrade fabric:/DemoApp

ApplicationName                : fabric:/DemoApp
ApplicationTypeName            : DemoAppType
TargetApplicationTypeVersion   : v2
ApplicationParameters          : {}
StartTimestampUtc              : 4/14/2015 9:26:38 PM
FailureTimestampUtc            : 4/14/2015 9:27:05 PM
FailureReason                  : UpgradeDomainTimeout
UpgradeDomainProgressAtFailure : MYUD1

                                 NodeName            : Node4
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 744c8d9f-1d26-417e-a60e-cd48f5c098f0

                                 NodeName            : Node1
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 4b43f4d8-b26b-424e-9307-7a7a62e79750
UpgradeState                   : RollingBackCompleted
UpgradeDuration                : 00:00:46
CurrentUpgradeDomainDuration   : 00:00:00
NextUpgradeDomain              :
UpgradeDomainsStatus           : { "MYUD1" = "Completed";
                                 "MYUD2" = "Completed";
                                 "MYUD3" = "Completed" }
UpgradeKind                    : Rolling
RollingUpgradeMode             : UnmonitoredAuto
ForceRestart                   : False
UpgradeReplicaSetCheckTimeout  : 00:00:00
~~~

Dans cet exemple, nous pouvons voir que la mise à niveau a échoué au niveau du domaine de mise à niveau *MYUD1* et que deux partitions (*744c8d9f-1d26-417e-a60e-cd48f5c098f0* et *4b43f4d8-b26b-424e-9307-7a7a62e79750*) se sont bloquées, incapables de placer les réplicas principaux (*WaitForPrimaryPlacement*) sur les nœuds cibles *Node1* et *Node4*.

La commande **Get-ServiceFabricNode** peut être utilisée pour vérifier que ces deux nœuds se trouvent dans le domaine de mise à niveau *MYUD1*. *UpgradePhase* indique *PostUpgradeSafetyCheck*, ce qui signifie que ces contrôles de sécurité interviennent une fois que tous les nœuds du domaine de mise à niveau ont terminé leur mise à niveau. Toutes ces informations pointent vers un problème potentiel avec la nouvelle version du code d’application. Les problèmes les plus courants sont des erreurs de service à l'ouverture ou une promotion vers les chemins de code principaux.

Un paramètre *UpgradePhase* ayant pour valeur *PreUpgradeSafetyCheck* indique des problèmes pendant la préparation du domaine de mise à niveau avant son exécution proprement dite. Dans ce cas, les problèmes les plus courants sont des erreurs de service dans le cadre de la fermeture ou de la rétrogradation à partir des chemins de code principaux.

Le paramètre **UpgradeState** a actuellement la valeur*RollingBackCompleted*, de sorte que la mise à niveau initiale doit avoir été effectuée à l'aide d'une opération **FailureAction** de restauration, laquelle a automatiquement annulé la mise à niveau lors de l'échec. Si la mise à niveau initiale avait été effectuée à l'aide d'une opération **FailureAction** manuelle, la mise à niveau serait maintenant dans un état suspendu afin d'autoriser le débogage réel de l'application.

### Examiner les échecs des contrôles d'intégrité

Des échecs de contrôle d’intégrité peuvent être déclenchés par divers problèmes supplémentaires qui peuvent se produire après la mise à niveau de tous les nœuds dans un domaine de mise à niveau et la réussite de tous les contrôles de sécurité. La sortie ci-dessous est typique d'un échec de mise à niveau dû à l'échec des contrôles d'intégrité. Le champ **UnhealthyEvaluations** capture un instantané de tous les échecs des contrôles d’intégrité au moment de l’échec de la mise à niveau en fonction de la [stratégie de contrôle d’intégrité](service-fabric-health-introduction.md) spécifiée par l’utilisateur.

~~~
PS D:\temp> Get-ServiceFabricApplicationUpgrade fabric:/DemoApp

ApplicationName                         : fabric:/DemoApp
ApplicationTypeName                     : DemoAppType
TargetApplicationTypeVersion            : v4
ApplicationParameters                   : {}
StartTimestampUtc                       : 4/24/2015 2:42:31 AM
UpgradeState                            : RollingForwardPending
UpgradeDuration                         : 00:00:27
CurrentUpgradeDomainDuration            : 00:00:27
NextUpgradeDomain                       : MYUD2
UpgradeDomainsStatus                    : { "MYUD1" = "Completed";
                                          "MYUD2" = "Pending";
                                          "MYUD3" = "Pending" }
UnhealthyEvaluations                    :
                                          Unhealthy services: 50% (2/4), ServiceType='PersistedServiceType', MaxPercentUnhealthyServices=0%.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc3', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='3a9911f6-a2e5-452d-89a8-09271e7e49a8', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc2', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='744c8d9f-1d26-417e-a60e-cd48f5c098f0', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

UpgradeKind                             : Rolling
RollingUpgradeMode                      : Monitored
FailureAction                           : Manual
ForceRestart                            : False
UpgradeReplicaSetCheckTimeout           : 49710.06:28:15
HealthCheckWaitDuration                 : 00:00:00
HealthCheckStableDuration               : 00:00:10
HealthCheckRetryTimeout                 : 00:00:10
UpgradeDomainTimeout                    : 10675199.02:48:05.4775807
UpgradeTimeout                          : 10675199.02:48:05.4775807
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
~~~

L’examen des échecs des contrôles d’intégrité nécessite tout d’abord de comprendre le modèle d’intégrité de Service Fabric. Mais même sans ces connaissances approfondies, nous pouvons voir que deux services sont défectueux : *fabric:/DemoApp/Svc3* et *fabric:/DemoApp/Svc2*, ainsi que les rapports d’erreurs d’intégrité (« InjectedFault » dans le cas présent). Dans cet exemple, deux services sur quatre sont défectueux, soit au-dessous de l’objectif par défaut de 0 % défectueux (*MaxPercentUnhealthyServices*).

La mise à niveau a été suspendue après son échec en raison de la spécification d’une opération **FailureAction** manuelle pendant le lancement de la mise à niveau, afin que vous puissiez étudier le système réel en état d’échec, si vous le souhaitez, avant d’effectuer toute autre action.

### Récupérer à partir d'une mise à niveau suspendue

Avec une opération **FailureAction** de restauration, aucune récupération n’est nécessaire étant donné que la mise à niveau est automatiquement restaurée en cas d’échec. Si une opération **FailureAction** manuelle est définie, plusieurs options de récupération existent :

1. Déclencher manuellement une restauration
2. Exécuter manuellement le reste de la mise à niveau
3. Reprendre la mise à niveau surveillée

La commande **Start-ServiceFabricApplicationRollback** peut être utilisée à tout moment pour lancer la restauration de l'application. Une fois que la commande a été retournée avec succès, la demande de restauration est enregistrée dans le système et commencera sous peu.

La commande **Resume-ServiceFabricApplicationUpgrade** peut être utilisée pour exécuter manuellement le reste de la mise à niveau, un domaine de mise à niveau après l'autre. Dans ce mode, seuls des contrôles de sécurité sont effectués par le système. Aucun contrôle d’intégrité supplémentaire n’est effectué. Cette commande peut être utilisée seulement quand *UpgradeState* indique *RollingForwardPending*, ce qui signifie que le domaine de mise à niveau en cours a terminé la mise à niveau mais que le domaine de mise à niveau suivant ne l'a pas encore commencée (en attente).

La commande **Update-ServiceFabricApplicationUpgrade** peut être utilisée pour reprendre la mise à niveau surveillée en exécutant des contrôles d'intégrité et de sécurité.

~~~
PS D:\temp> Update-ServiceFabricApplicationUpgrade fabric:/DemoApp -UpgradeMode Monitored

UpgradeMode                             : Monitored
ForceRestart                            :
UpgradeReplicaSetCheckTimeout           :
FailureAction                           :
HealthCheckWaitDuration                 :
HealthCheckStableDuration               :
HealthCheckRetryTimeout                 :
UpgradeTimeout                          :
UpgradeDomainTimeout                    :
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :

PS D:\temp>
~~~

La mise à niveau continuera à partir du domaine de mise à niveau dans lequel elle a été suspendue en dernier, et elle utilisera les mêmes paramètres de mise à niveau et les mêmes stratégies de contrôle d'intégrité qu'auparavant. Si nécessaire, les paramètres de mise à niveau et les stratégies de contrôle d’intégrité figurant dans la sortie ci-dessus peuvent être modifiés dans la même commande au moment de la reprise de la mise à niveau. Dans cet exemple, la mise à niveau a repris en mode surveillé, laissant tous les autres paramètres inchangés et utilisant les mêmes paramètres et les mêmes stratégies de contrôle d’intégrité qu’auparavant.

## Suite de la résolution des problèmes

### Service Fabric ne suit pas les stratégies de contrôle d’intégrité spécifiées

Cause possible 1 :

Service Fabric convertit tous les pourcentages en nombres réels d'entités (p. ex., de réplicas, de partitions et de services) pour l'évaluation de l'intégrité et arrondit toujours au nombre d'entités entières le plus proche. Par exemple, si la valeur maximale _MaxPercentUnhealthyReplicasPerPartition_ est 21 % et qu’il existe cinq réplicas, Service Fabric autorise jusqu’à deux réplicas (c’est-à-dire `Math.Ceiling (5*0.21)`) défectueux pendant l’évaluation de l’intégrité des partitions. Les stratégies de contrôle d'intégrité doivent être définies en conséquence pour tenir compte de cela.

Cause possible 2 :

Les stratégies d'intégrité sont spécifiées en pourcentages du nombre total de services et non en instances de service spécifiques. Par exemple, avant une mise à niveau, supposons qu'une application présente quatre instances de service A, B, C et D, et que le service D soit défectueux mais sans impact significatif sur l'application. Nous souhaitons ignorer le service défectueux connu D pendant la mise à niveau et définir le paramètre *MaxPercentUnhealthyServices* à 25 %, en supposant que seuls A, B et C doivent être sains.

Toutefois, pendant la mise à niveau, D peut devenir sain et C devenir défectueux. La mise à niveau réussirait tout de même dans ce cas puisque seulement 25 % des services sont défectueux. Des erreurs imprévues pourraient toutefois se produire en raison de l’état défectueux inattendu de C au lieu de D. Dans ce cas, D doit être modélisé sous la forme d’un autre type de service que A, B et C. Comme les stratégies de contrôle d’intégrité peuvent être spécifiées sur la base du type de service, cela permettrait d’appliquer des seuils de pourcentage de services défectueux différents à des services différents en fonction de leurs rôles dans l’application.

### Je n’ai pas spécifié une stratégie de contrôle d’intégrité pour la mise à niveau de l’application, mais la mise à niveau continue d’échouer en raison de certains délais d’attente que je n’ai jamais spécifiés

Quand les stratégies de contrôle d’intégrité ne sont pas fournies à la demande de mise à niveau, elles sont extraites du fichier *ApplicationManifest.xml* de la version de l’application actuelle. Par exemple, si vous mettez à niveau Application X de v1 à v2, les stratégies de contrôle d’intégrité de l’application spécifiées pour Application X dans v1 sont utilisées. Si une autre stratégie de contrôle d'intégrité doit être utilisée pour la mise à niveau, la stratégie doit être spécifiée dans le cadre de l'appel d'API de mise à niveau de l'application. Notez que les stratégies spécifiées dans le cadre de l'appel d'API s'appliquent uniquement pendant la durée de la mise à niveau. Une fois la mise à niveau terminée, les stratégies spécifiées dans *ApplicationManifest.xml* sont utilisées.

### Délais d’attente incorrects spécifiés

Les utilisateurs se sont peut-être demandé ce qu’il se passe si les délais d’attente sont définis de façon incohérente, par exemple, si *UpgradeTimeout* est inférieur à *UpgradeDomainTimeout*. La réponse est qu'une erreur est renvoyée. Autres cas où cela peut se produire : si *UpgradeDomainTimeout* est inférieur à la somme de *HealthCheckWaitDuration* et de *HealthCheckRetryTimeout*, ou si *UpgradeDomainTimeout* est inférieur à la somme de *HealthCheckWaitDuration* et de *HealthCheckStableDuration*.

### Mes mises à niveau prennent trop de temps

Le temps nécessaire pour terminer une mise à niveau dépend des divers contrôles d’intégrité et délais d’attente spécifiés, lesquels dépendent à leur tour du temps nécessaire à la mise à niveau de votre application (y compris la copie du package, le déploiement et la stabilisation). Se montrer trop sévère avec les délais d’attente peut induire plus d’échecs de mises à niveau ; nous vous recommandons donc de démarrer prudemment avec des délais d’attente plus longs.

Voici un rappel rapide sur la manière dont les délais d’attente interagissent avec les durées de mise à niveau :

Les mises à niveau pour un domaine de mise à niveau ne peuvent pas prendre moins de temps que *HealthCheckWaitDuration* + *HealthCheckStableDuration*.

Un échec de mise à niveau ne peut pas se produire avant *HealthCheckWaitDuration* + *HealthCheckRetryTimeout*.

La durée de mise à niveau d’un domaine de mise à niveau est limitée par *UpgradeDomainTimeout*. Si les paramètres *HealthCheckRetryTimeout* et *HealthCheckStableDuration* sont tous les deux non nuls et que l’intégrité de l’application ne cesse d’alterner, la mise à niveau finit par expirer sur *UpgradeDomainTimeout*. *UpgradeDomainTimeout* commence le compte à rebours au démarrage de la mise à niveau pour le domaine de mise à niveau actuel.

## Étapes suivantes

La [mise à niveau de votre application à l'aide de Visual Studio](service-fabric-application-upgrade-tutorial.md) vous guide à travers une mise à niveau de l'application à l'aide de Visual Studio.

La [mise à niveau de votre application à l'aide de Powershell](service-fabric-application-upgrade-tutorial-powershell.md) vous guide à travers une mise à niveau de l'application à l'aide de PowerShell.

Contrôlez les mises à niveau de votre application à l'aide des [Paramètres de mise à niveau](service-fabric-application-upgrade-parameters.md).

Rendez les mises à niveau de votre application compatibles en apprenant à utilisez la [Sérialisation des données](service-fabric-application-upgrade-data-serialization.md).

Apprenez à utiliser les fonctionnalités avancées lors de la mise à niveau de votre application en consultant les [Rubriques avancées](service-fabric-application-upgrade-advanced.md).

Résolvez les problèmes courants de mise à niveau de l'application en vous reportant aux étapes de [Résolution des problèmes de mise à niveau des applications](service-fabric-application-upgrade-troubleshooting.md).
 

<!---HONumber=AcomDC_0211_2016-->