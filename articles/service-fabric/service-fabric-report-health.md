<properties
   pageTitle="Ajout de rapports d'intégrité Service Fabric personnalisés"
   description="Explique comment envoyer des rapports d'intégrité personnalisés à des entités d'intégrité Azure Service Fabric. Fournit des recommandations pour la conception et la mise en œuvre de rapports d'intégrité de qualité."
   services="service-fabric"
   documentationCenter=".net"
   authors="oanapl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/16/2015"
   ms.author="oanapl"/>

# Ajout de rapports d'intégrité Service Fabric personnalisés
Service Fabric présente un [Modèle d'intégrité](service-fabric-health-introduction.md) conçu pour signaler des conditions de cluster ou d'application défectueuses sur des entités spécifiques. Ceci s'effectue grâce aux **rapporteurs d'intégrité** (composants système et agents de surveillance). L'objectif consiste en un diagnostic et une réparation simples et rapides. Les enregistreurs du service doivent penser en amont à l'intégrité. Toute condition qui peut avoir un impact sur l'intégrité doit être signalée, surtout si cela peut aider à signaler des problèmes proches de la racine. Ceci peut permettre d'économiser beaucoup de temps en termes de débogage et d'enquête lorsque le service est en cours d'exécution à l'échelle dans le cloud (privé ou Azure).

Les rapporteurs Service Fabric surveillent les conditions identifiées qui présentent un intérêt. Ils dressent un rapport sur ces conditions en fonction de leur vue locale. Le [Magasin d'intégrité](service-fabric-health-introduction.md#Health-Store) agrège les données d'intégrité envoyées par tous les rapporteurs pour déterminer si les entités sont globalement intègres. Le modèle doit être riche, flexible et facile à utiliser. La qualité des rapports d'intégrité détermine la précision de la vue de l'intégrité du cluster. Les faux positifs qui indiquent à tort des problèmes d'intégrité peuvent affecter de façon négative les mises à niveau ou d'autres services qui utilisent les données d'intégrité, comme les services de réparation ou les mécanismes d'alerte. Par conséquent, une réflexion est nécessaire pour générer des rapports qui capturent des conditions pertinentes de la meilleure façon possible.

Pour concevoir et mettre en œuvre des rapports d'intégrité, les agents de surveillance et les composants du système doivent :

- Définir la condition qui les intéresse, la façon dont elle est surveillée et l'impact sur la fonctionnalité du cluster/de l'application. Ceci permet de définir la propriété du rapport d'intégrité et l'état d'intégrité.

- Déterminer à quelle [entité](service-fabric-health-introduction.md#health-entities-and-hierarchy) le rapport s'applique.

- Déterminer à partir de quel emplacement le rapport est généré ; soit depuis le service, soit depuis l'agent de surveillance interne ou externe.

- Définir une source utilisée pour identifier le rapporteur.

- Choisir une stratégie de création de rapports, périodiquement ou selon les transitions. La méthode recommandée est la création périodique, car elle requiert un code plus simple et est par conséquent moins sujette aux erreurs.

- Déterminer la durée pendant laquelle le rapport sur les conditions défectueuses doit rester dans le magasin d'intégrité et comment il doit être effacé. Ceci permet de définir la durée de vie du rapport et le comportement de suppression à expiration.

Comme mentionné ci-dessus, la création de rapports peut être effectuée à partir des éléments suivants :

- Le réplica de service Service Fabric surveillé.

- Les agents de surveillance internes déployés en tant que service de Service Fabric. Par exemple, un service sans état Service Fabric qui surveille les conditions et crée un rapport. Les agents de surveillance peuvent être déployés sur tous les nœuds ou ils peuvent être apparentés au service surveillé.

- Les agents de surveillance internes qui s'exécutent sur les nœuds Service Fabric mais qui ne sont **pas** implémentés en tant que services de Service Fabric.

- Les agents de surveillance externes qui sondent la ressource depuis l'**extérieur** du cluster Service Fabric. Par exemple, Gomez comme service de surveillance.

> [AZURE.NOTE]Dès le départ, le cluster comprend des rapports d'intégrité envoyés par les composants système. Pour en savoir plus, consultez [Utilisation des rapports d'intégrité du système pour la résolution des problèmes](service-fabric-understand-and-troubleshoot-with-system-health-reports.md). Les rapports utilisateur doivent être envoyés sur des [entités d'intégrité](service-fabric-health-introduction.md#health-entities-and-hierarchy) déjà créées par le système.

Lorsque la conception de la création des rapports d'intégrité est claire, l'envoi de rapports d'intégrité est simple. Elle peut se faire au moyen d'une API à l'aide de FabricClient.HealthManager.ReportHealth, au moyen de Powershell ou de REST. En interne, toutes les méthodes utilisent un client d'intégrité contenu à l'intérieur d'un client d'infrastructure. Il existe des dispositifs de configuration pour regrouper les rapports afin d'améliorer les performances.

> [AZURE.NOTE]Le rapport d'intégrité est synchronisé et représente uniquement le travail de validation côté client. Le fait que le rapport est accepté par le client d'intégrité ne signifie pas qu'il est appliqué dans le magasin. Il sera envoyé de manière asynchrone, éventuellement regroupé avec d'autres rapports et le traitement sur le serveur peut échouer (par ex. numéro de séquence obsolète, l'entité sur laquelle le rapport doit être appliqué a été supprimée, etc.).

## Client de contrôle d'intégrité
Les rapports d'intégrité sont envoyés au Magasin d'intégrité à l'aide d'un client de contrôle d'intégrité, qui réside dans le client d'infrastructure. Le client de contrôle d'intégrité peut être configuré avec les éléments suivants :

- HealthReportSendInterval. Le délai entre le moment où le rapport est ajouté au client et le moment où il est envoyé au Magasin d'intégrité. Ceci permet de regrouper les rapports dans un seul message, au lieu d'envoyer un message par rapport, afin d'améliorer les performances. Par défaut : 30 secondes.

- HealthReportRetrySendInterval. L'intervalle auquel le client de contrôle d'intégrité renvoie les rapports d'intégrité cumulés au Magasin d'intégrité. Par défaut : 30 secondes.

- HealthOperationTimeout. Le délai d'expiration d'un message de rapport envoyé au Magasin d'intégrité. Si un message expire, le client de contrôle d'intégrité réessaie jusqu'à ce que le Magasin d'intégrité confirme que les rapports ont été traités. Par défaut : 2 minutes.

> [AZURE.NOTE]Lorsque les rapports sont traités par lot, le client d'infrastructure doit être maintenu actif pendant au moins la valeur de HealthReportSendInterval pour s'assurer que les rapports sont envoyés. Si le message est perdu ou si le Magasin d'intégrité n'est pas en mesure de les appliquer en raison d'erreurs transitoires, le client d'infrastructure doit être maintenu actif plus longtemps pour lui donner une chance de réessayer.

La mise en mémoire tampon sur le client prend en compte l'unicité des rapports. Par exemple, si un rapporteur incorrect spécifique génère 100 rapports par seconde sur la même propriété de la même entité, les rapports seront remplacés par la dernière version. Au plus, un rapport de ce type existe dans la file d'attente du client. Si le traitement par lot est configuré, le nombre de rapports envoyés au Magasin d'intégrité est d'un rapport par intervalle d'envoi, le dernier rapport ajouté, qui reflète l'état le plus récent de l'entité. Tous les paramètres de configuration peuvent être spécifiés lors de la création de FabricClient, en transmettant FabricClientSettings avec les valeurs souhaitées pour les entrées liées à l'intégrité.

Ce qui suit permet de créer un client d'infrastructure et d'indiquer que les rapports doivent être envoyés dès qu'ils sont ajoutés. Pour les erreurs renouvelables ou les délais d'attente, les nouvelles tentatives se produisent toutes les 40 secondes.

```csharp
var clientSettings = new FabricClientSettings()
{
    HealthOperationTimeout = TimeSpan.FromSeconds(120),
    HealthReportSendInterval = TimeSpan.FromSeconds(0),
    HealthReportRetrySendInterval = TimeSpan.FromSeconds(40),
};
var fabricClient = new FabricClient(clientSettings);
```

Il est possible de définir les mêmes paramètres lors de la création d'une connexion à un cluster par le biais de Powershell. La commande suivante démarre une connexion à un cluster local :

```powershell
PS C:\> Connect-ServiceFabricCluster -HealthOperationTimeoutInSec 120 -HealthReportSendIntervalInSec 0 -HealthReportRetrySendIntervalInSec 40
True

ConnectionEndpoint   :
FabricClientSettings : {
                       ClientFriendlyName                   : PowerShell-1944858a-4c6d-465f-89c7-9021c12ac0bb
                       PartitionLocationCacheLimit          : 100000
                       PartitionLocationCacheBucketCount    : 1024
                       ServiceChangePollInterval            : 00:02:00
                       ConnectionInitializationTimeout      : 00:00:02
                       KeepAliveInterval                    : 00:00:20
                       HealthOperationTimeout               : 00:02:00
                       HealthReportSendInterval             : 00:00:00
                       HealthReportRetrySendInterval        : 00:00:40
                       NotificationGatewayConnectionTimeout : 00:00:00
                       NotificationCacheUpdateTimeout       : 00:00:00
                       }
GatewayInformation   : {
                       NodeAddress                          : localhost:19000
                       NodeId                               : 1880ec88a3187766a6da323399721f53
                       NodeInstanceId                       : 130729063464981219
                       NodeName                             : Node.1
                       }
```

> [AZURE.NOTE]Pour vous assurer que les services non autorisés ne peuvent pas signaler l'intégrité sur les entités dans le cluster, le serveur peut être configuré pour accepter uniquement les demandes provenant de clients sécurisés. Dans la mesure où les rapports sont créés par le biais de FabricClient, cela signifie que la sécurité doit être activée sur FabricClient pour lui permettre de communiquer avec le cluster, par exemple, avec l'authentification des certificats ou Kerberos.

## Conception de rapports d'intégrité
La première étape dans la création de rapports de haute qualité consiste à identifier les conditions qui peuvent avoir un impact sur l'intégrité du service. Toute condition qui peut aider à signaler des problèmes dans le service ou le cluster lorsqu'ils commencent, ou mieux encore, avant qu'ils ne se produisent, peut permettre d'économiser des milliards de dollars. Moins de temps d'arrêt, moins de nuits passées à rechercher et à résoudre les problèmes, une meilleure satisfaction de la clientèle.

Une fois que les conditions sont identifiées, les enregistreurs de surveillance doivent déterminer la meilleure façon de les surveiller pour obtenir le meilleur compromis entre la charge et l'utilité. Prenons comme exemple un service qui effectue des calculs complexes à l'aide de fichiers temporaires sur un partage. Un agent de surveillance peut surveiller le partage pour s'assurer que l'espace disponible est suffisant. Il peut écouter les notifications des modifications du fichier/répertoire. Il peut émettre un avertissement si un seuil initial est atteint et signaler une erreur si le partage est plein. En cas d'avertissement, un système de réparation peut démarrer le nettoyage des anciens fichiers sur le partage. En cas d'erreur, un système de réparation peut déplacer le réplica de service vers un autre nœud. Notez comment les états de condition sont décrits en termes d'intégrité : quel est l'état de la condition pouvant être considéré comme sain ou défectueux (avertissement ou erreur).

Une fois les détails de surveillance définis, l'enregistreur de surveillance doit savoir comment mettre en œuvre la surveillance. Si les conditions peuvent être déterminées à partir du service, l'agent de surveillance peut faire partie du service surveillé lui-même. Par exemple, le code de service peut vérifier l'utilisation du partage et créer un rapport à l'aide d'un client d'infrastructure local à chaque fois qu'il tente d'écrire un fichier. L'avantage de cette approche est que la création de rapports est simple. Vous devez veiller à ce que les bogues de l'agent de surveillance n'affectent pas la fonctionnalité du service.

La création de rapports depuis le service surveillé n'est pas toujours une option. Un agent de surveillance au sein du service n'est peut-être pas en mesure de détecter les conditions : il n'a pas la logique ou les données pour effectuer cette détermination, ou la charge de la surveillance des conditions est élevée, ou les conditions ne sont pas spécifiques à un service, mais affectent les interactions entre les services. Une autre option consiste à placer les agents de surveillance dans le cluster en tant que processus distincts. Les agents de surveillance surveillent les conditions et créent des rapports, sans affecter les services principaux. Par exemple, il est possible de mettre en œuvre ces agents de surveillance en tant que services sans état dans la même application, déployés sur tous les nœuds ou sur les mêmes nœuds que le service.

Parfois, un agent de surveillance exécuté dans le cluster n'est pas non plus une option. Si les conditions surveillées sont la disponibilité ou la fonctionnalité du service telle qu'elle est vue par les utilisateurs, il est préférable que les agents de surveillance se trouvent au même endroit que les clients utilisateurs, pour tester les opérations de la même façon que les utilisateurs les appellent. Par exemple, il est possible d'avoir un agent de surveillance à l'extérieur du cluster et émettant des requêtes au service, vérifiant la latence et l'exactitude des résultats (pour un service de calculatrice, est-ce que l'opération 2 + 2 renvoie le résultat 4 dans un délai raisonnable ?).

Une fois que les détails de l'agent de surveillance ont été finalisés, vous devez choisir un ID source qui l'identifie de façon unique. Si plusieurs agents de surveillance du même type résident dans le cluster, ils doivent transmettre leurs rapports sur des entités différentes. S'ils transmettent leurs rapports sur la même entité, assurez-vous que l'ID source ou la propriété est différent(e), afin que les rapports puissent coexister. La propriété du rapport d'intégrité doit capturer la condition surveillée. Par exemple, dans l'exemple ci-dessus, la propriété peut être ShareSize. Si plusieurs données sont appliquées à la même condition, la propriété doit contenir des informations dynamiques pour autoriser les rapports à coexister. Par exemple, s'il existe plusieurs partages qui doivent être surveillés, le nom de la propriété peut être ShareSize-nom du partage.

> [AZURE.NOTE]Vous ne devez **pas** utiliser le magasin d'intégrité pour conserver les informations sur l'état. Seules les informations liées à l'intégrité doivent être indiquées comme telles, c'est-à-dire des informations qui ont un impact sur l'évaluation d'intégrité d'une entité. Le magasin d'intégrité n'a pas été conçu comme un magasin à usage général. Il utilise la logique d'évaluation de l'intégrité pour regrouper toutes les données dans un état d'intégrité. Le fait d'envoyer des informations non liées à l'intégrité (par ex., signaler l'état avec l'état d'intégrité Ok) n'affectera pas l'état d'intégrité agrégé, mais cela pourrait affecter négativement les performances du magasin d'intégrité.

La décision suivante consiste à savoir sur quelle entité créer le rapport. La plupart du temps, cela est évident en fonction de la condition. Choisissez l'entité avec la meilleure granularité possible. Si une condition a un impact sur tous les réplicas d'une partition, le rapport doit porter sur la partition, pas le service. Il existe des cas où une réflexion approfondie est nécessaire. Si la condition a un impact sur une entité (par ex. réplica) mais que vous souhaitez que la condition soit signalée pendant une durée supérieure à la durée de vie du réplica, alors la condition doit être signalée sur la partition. Dans le cas contraire, lorsque le réplica est supprimé, tous les rapports associés sont effacés du magasin. Cela signifie que les enregistreurs de surveillance doivent également réfléchir à la durée de vie de l'entité et du rapport. Le moment où un rapport doit être effacé d'un magasin doit être clair (par ex. lorsqu'une Erreur signalée sur une entité ne s'applique plus).

Prenons un exemple pour rassembler les points ci-dessus. Examinons une application Service Fabric composée d'un service persistant avec état maître et de services sans état esclaves déployés sur tous les nœuds (un type de service esclave par type de tâche). Le service maître a une file d'attente de traitement contenant les commandes devant être exécutées par les services esclaves. Les services esclaves exécutent les requêtes entrantes et renvoient des accusés de réception. Une condition qui peut être surveillée est la longueur de la file d'attente de traitement maître. Si la longueur de la file d'attente maître atteint un seuil, un avertissement doit être émis, car cela signifie que les services esclaves ne peuvent pas gérer la charge. Si la file d'attente a atteint la longueur maximale et que des commandes sont abandonnées, une erreur doit être signalée, car le service ne peut pas récupérer. Les rapports peuvent avoir lieu sur la propriété « QueueStatus ». L'agent de surveillance se trouve à l'intérieur du service et il est envoyé régulièrement sur le réplica principal maître. La durée de vie est de 2 minutes et il est envoyé régulièrement toutes les 30 secondes. Si le service principal tombe en panne, le rapport est effacé automatiquement du magasin. Si le réplica de service fonctionne mais qu'il est bloqué ou a d'autres problèmes, le rapport expirera dans le magasin d'intégrité et l'entité sera évaluée à l'erreur.

L'heure d'exécution de la tâche est une autre condition qui peut être surveillée. Le service maître distribue les tâches aux services esclaves selon le type de tâche. En fonction de la conception, le service maître peut interroger les services esclaves sur l'état de la tâche ou il peut attendre que les services esclaves renvoient des accusés de réception lorsque la tâche est terminée. Dans le deuxième cas, vous devez veiller à détecter les situations où des services esclaves périssent ou les messages se perdent. Une option consiste pour le service maître à envoyer une requête ping au même service esclave, ce qui en renvoie l'état. Si aucun état n'est reçu, vous pouvez considérer que la tâche a échoué et la replanifier. Ceci suppose que les tâches sont idempotentes. Nous pouvons traduire la condition surveillée en tant qu'avertissement si la tâche n'est pas effectuée dans un certain laps de temps t1 (par ex. 10 minutes) ; et en tant qu'erreur si la tâche n'est pas terminée dans le temps t2 (par ex. 20 minutes). Cette création de rapport peut se faire de plusieurs façons :

- Le réplica principal maître crée régulièrement des rapports sur lui-même. Nous pouvons avoir une propriété pour toutes les tâches en attente dans la file d'attente : si au moins une tâche prend plus de temps, un rapport est créé sur la propriété « PendingTasks » comme avertissement ou erreur, selon le cas. S'il n'y a aucune tâche en attente ou si toutes les tâches viennent de démarrer, le rapport indique Ok. Les tâches sont conservées. Donc, si le service principal tombe en panne, le nouveau service principal promu peut continuer à créer des rapports correctement.

- Un autre processus de surveillance (dans le cloud ou en externe) vérifie les tâches (à partir de l'extérieur, en fonction du résultat de la tâche souhaitée) pour voir si elles sont terminées. Si les seuils ne sont pas respectés, un rapport est créé sur le service maître. Un rapport est créé sur chaque tâche avec l'identificateur de tâche (par ex. : PendingTask + IDtâche). Un rapport est créé uniquement sur les états défectueux. La valeur TTL est définie sur quelques minutes et les rapports à supprimer sont sélectionnés lorsqu'ils ont expiré pour assurer le nettoyage.

- Le service esclave qui exécute une tâche signale si son exécution dure plus longtemps que prévu. Le rapport est créé sur l'instance de service sur la propriété « PendingTasks ». Ceci met en évidence l'instance de service qui présente des problèmes, mais cela ne capture pas la situation où l'instance meurt. Les rapports sont nettoyés à ce moment-là. Le rapport peut s'effectuer sur le service esclave. Si le service esclave termine la tâche, l'instance esclave efface le rapport du magasin. Ceci ne capture pas la situation où le message d'accusé de réception est perdu et la tâche n'est pas terminée du point de vue du service maître.

Quelle que soit la façon dont les rapports sont créés dans les cas décrits ci-dessus, ils seront capturés dans l'intégrité de l'application lors de l'évaluation de l'intégrité.

## Rapport régulier ou sur transition
À l'aide du modèle de création de rapports d'intégrité, les agents de surveillance peuvent envoyer des rapports régulièrement ou en cas de transition. La méthode régulière est recommandée, car le code est beaucoup plus simple, donc moins sujet aux erreurs. Les agents de surveillance doivent s'efforcer d'être aussi simples que possible pour éviter les bogues qui déclenchent des rapports incorrects. Un rapport d'état défectueux incorrect aura un impact sur l'évaluation de l'intégrité et les scénarios basés sur l'intégrité, comme les mises à niveau. Un rapport d'état intègre incorrect masque les problèmes dans le cluster, ce qui n'est pas souhaité.

Pour la création de rapports réguliers, l'agent de surveillance peut être implémenté avec une minuterie. Sur le rappel de la minuterie, l'agent de surveillance peut vérifier l'état et envoyer un rapport basé sur l'état actuel. Il est inutile de voir le rapport envoyé précédemment ou d'effectuer une optimisation en termes de messagerie. Pour ce faire, le client de contrôle d'intégrité dispose de la logique de traitement par lot. Tant que le client de contrôle d'intégrité est maintenu actif, il réessayera en interne jusqu'à ce que le rapport soit reçu par le magasin d'intégrité ou que l'agent de surveillance crée un rapport plus récent avec une entité, une propriété et une source identiques.

La création de rapports sur transition nécessite un traitement prudent de l'état. L'agent de surveillance surveille certaines conditions et ne crée un rapport que lorsque les conditions changent. L'avantage est que moins de rapports sont nécessaires. L'inconvénient est que la logique de l'agent de surveillance est complexe. Les conditions ou les rapports doivent être maintenus afin qu'ils puissent être inspectés pour déterminer les changements d'état. En cas de basculement, il convient d'envoyer un rapport qui peut ne pas avoir été envoyé précédemment (placé en file d'attente, mais pas encore envoyé au magasin d'intégrité). Le numéro de séquence doit toujours augmenter ou les rapports seront rejetés en raison de l'obsolescence. Dans les rares cas où la perte de données se produit, une synchronisation peut être nécessaire entre l'état du rapporteur et l'état du magasin d'intégrité.

## Mise en œuvre de la création de rapports d'intégrité
Une fois que les détails de l'entité et du rapport sont clairs, l'envoi de rapports d'intégrité peut se faire au moyen d'une API, Powershell ou REST.

### API
Pour créer un rapport au moyen de l'API, les utilisateurs doivent créer un rapport d'intégrité spécifique au type d'entité, puis transmettre celui-ci à un client de contrôle d'intégrité.

L'exemple suivant montre une création de rapports réguliers à partir de l'agent de surveillance dans le cluster. L'observateur vérifie s'il est possible d'accéder à une ressource externe à partir d'un nœud. La ressource est requise par un manifeste de service au sein de l'application. Si la ressource n'est pas disponible, les autres services au sein de l'application peuvent fonctionner correctement. Par conséquent, le rapport est envoyé sur l'entité du package du service déployé, régulièrement, toutes les 30 secondes.

```csharp
private static Uri ApplicationName = new Uri("fabric:/WordCount");
private static string ServiceManifestName = "WordCount.Service";
private static string NodeName = FabricRuntime.GetNodeContext().NodeName;
private static Timer ReportTimer = new Timer(new TimerCallback(SendReport), null, 30 * 1000, 30 * 1000);
private static FabricClient Client = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });

public static void SendReport(object obj)
{
    // Test whether the resource can be accessed from the node
    HealthState healthState = this.TestConnectivityToExternalResource();

    // Send report on deployed service package, as the connectivity is needed by the specific service manifest
    // and can be different on different nodes
    var deployedServicePackageHealthReport = new DeployedServicePackageHealthReport(
        ApplicationName,
        ServiceManifestName,
        NodeName,
        new HealthInformation("ExternalSourceWatcher", "Connectivity", healthState));

    // TODO: handle exception. Code omitted for snipet brevity.
    Client.HealthManager.ReportHealth(deployedServicePackageHealthReport);
}
```

### Powershell
Les utilisateurs peuvent envoyer des rapports d'intégrité avec Send-ServiceFabric*EntityType*HealthReport.

L'exemple suivant montre la création de rapports réguliers sur les valeurs du processeur sur un nœud. Les rapports doivent être envoyés toutes les 30 secondes, ils ont une durée de vie de 2 minutes. S'ils expirent, cela signifie que le rapporteur présente des problèmes, donc le nœud est évalué à l'erreur. Lorsque le processeur est supérieure à un seuil, le rapport contient un avertissement pour l'état d'intégrité. Si le processeur est supérieure au seuil pendant une durée supérieure à celle configurée, cela est signalé comme erreur. Dans le cas contraire, le rapporteur envoie Ok.

```powershell
PS C:\> Send-ServiceFabricNodeHealthReport -NodeName Node.1 -HealthState Warning -SourceId PowershellWatcher -HealthProperty CPU -Description "CPU is above 80% threshold" -TimeToLiveSec 120

PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='CPU', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 5
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : CPU
                        HealthState           : Warning
                        SequenceNumber        : 130741236814913394
                        SentAt                : 4/21/2015 9:01:21 PM
                        ReceivedAt            : 4/21/2015 9:01:21 PM
                        TTL                   : 00:02:00
                        Description           : CPU is above 80% threshold
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:01:21 PM
```

L'exemple suivant signale un avertissement temporaire sur un réplica. Il obtient d'abord l'ID de partition et l'ID de réplica pour le service qui l'intéresse, puis il envoie un rapport à partir de PowershellWatcher sur la propriété ResourceDependency. Le rapport n'est utile que pendant 2 minutes et il sera automatiquement supprimé du magasin.

```powershell
PS C:\> $partitionId = (Get-ServiceFabricPartition -ServiceName fabric:/WordCount/WordCount.Service).PartitionId

PS C:\> $replicaId = (Get-ServiceFabricReplica -PartitionId $partitionId | where {$_.ReplicaRole -eq "Primary"}).ReplicaId

PS C:\> Send-ServiceFabricReplicaHealthReport -PartitionId $partitionId -ReplicaId $replicaId -HealthState Warning -SourceId PowershellWatcher -HealthProperty ResourceDependency -Description "The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes." -TimeToLiveSec 120 -RemoveWhenExpired

PS C:\> Get-ServiceFabricReplicaHealth  -PartitionId $partitionId -ReplicaOrInstanceId $replicaId


PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
ReplicaId             : 130740415594605869
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='ResourceDependency', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130740768777734943
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : ResourceDependency
                        HealthState           : Warning
                        SequenceNumber        : 130741243777723555
                        SentAt                : 4/21/2015 9:12:57 PM
                        ReceivedAt            : 4/21/2015 9:12:57 PM
                        TTL                   : 00:02:00
                        Description           : The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:12:32 PM
```

## Étapes suivantes

Grâce aux données d'intégrité, les enregistreurs de service et les administrateurs de cluster/d'application peuvent réfléchir à des façons de consommer les informations. Par exemple, ils peuvent configurer des alertes basées sur l'état d'intégrité pour intercepter les problèmes graves avant que ceux-ci ne provoquent des pannes. Ils peuvent configurer les systèmes de réparation automatique pour résoudre automatiquement les problèmes.

[Présentation du contrôle d'intégrité de Service Fabric](service-fabric-health-introduction.md)

[Comment afficher les rapports d'intégrité de Service Fabric](service-fabric-view-entities-aggregated-health.md)

[Utilisation des rapports d'intégrité système pour la résolution des problèmes](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Comment analyser et diagnostiquer des services localement](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Mise à niveau des applications Service Fabric](service-fabric-application-upgrade.md)
 

<!-----HONumber=July15_HO4-->