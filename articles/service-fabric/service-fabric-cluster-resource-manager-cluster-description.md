<properties
   pageTitle="Description du cluster de l'équilibreur de ressources | Microsoft Azure"
   description="Description d'un cluster Service Fabric en spécifiant les domaines d'erreur, domaines de mise à niveau, propriétés du nœud et capacités du nœud à l'équilibreur de ressources."
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/03/2016"
   ms.author="masnider"/>

# Description d'un cluster Service Fabric

Le Gestionnaire de ressources de cluster Service Fabric fournit plusieurs mécanismes permettant de décrire un cluster. Pendant l’exécution, le Gestionnaire de ressources utilise ces informations pour garantir une haute disponibilité des services en cours d’exécution dans le cluster, tout en faisant en sorte que les ressources du cluster soient convenablement utilisées.

## Concepts clés
Les fonctionnalités du Gestionnaire de ressources de cluster qui décrivent un cluster sont les suivantes :
- Domaines d'erreur
- Domaines de mise à niveau
- Propriétés du nœud
- Capacités du nœud

### Domaines d'erreur

Un domaine d’erreur est une zone d’échec coordonné. Une machine unique constitue un domaine d’erreur (puisqu’elle peut cesser de fonctionner de manière indépendante pour de nombreuses raisons : coupure électrique, défaillance de disque ou erreur de microprogramme de carte d’interface réseau). Si plusieurs machines sont connectées au même commutateur Ethernet, elles se trouvent dans le même domaine d’erreur, tout comme celles connectées à une source d’alimentation unique.

Si vous avez défini votre propre cluster, vous devez réfléchir à ces différents domaines d’erreur et vous assurer que vos domaines d’erreur ont été configurés correctement pour que Service Fabric sache où placer des services de manière sûre. Par « sûre », nous voulons dire « intelligente » : nous ne voulons pas placer des services à un emplacement où la perte d’un domaine d’erreur entraîne leur panne. Dans l’environnement Azure, nous exploitons les informations de domaine d’erreur fournies par le contrôleur de structure/gestionnaire de ressources Azure pour configurer correctement les nœuds du cluster en votre nom. Pour donner un exemple simple, dans l’illustration ci-dessous (Figure 7), nous avons appliqué une couleur à toutes les entités qui correspondent raisonnablement à un domaine d’erreur et répertorié les différents domaines d’erreur correspondants. Dans cet exemple, nous avons des centres de données (DC), des racks (R) et des panneaux (B). En théorie, si chaque panneau contient plusieurs machines virtuelles, il peut exister une autre couche dans la hiérarchie de domaine d’erreur.

![Nœuds organisés par domaines d'erreur][Image1]

 Lors de l’exécution, le Gestionnaire de ressources de cluster Service Fabric considère les domaines d’erreur dans le cluster et tente de répartir les réplicas d’un service donné afin qu’ils soient tous dans des domaines d’erreur distincts. Ce processus permet de s’assurer qu’en cas d’échec d’un domaine d’erreur, la disponibilité de ce service n’est pas compromise.

 Le Gestionnaire de ressources de cluster Service Fabric ne tient pas véritablement compte du nombre de couches de la hiérarchie. Cependant, dans la mesure où il cherche à s’assurer que la perte d’une partie de la hiérarchie n’influe pas sur le cluster ou les services exécutés sur celui-ci, il est généralement préférable que chaque niveau de profondeur du domaine d’erreur inclue le même nombre de machines. Cela évite qu’une partie de la hiérarchie doive contenir plus de services que d’autres à la fin de la journée.

 Si vous configurez votre cluster d’une manière qui crée un déséquilibre dans l’« arborescence » de domaines d’erreur, le Gestionnaire de ressources aura du mal à déterminer l’allocation optimale des réplicas, en particulier car cela signifie que la perte d’un domaine donné peut grandement affecter la disponibilité du cluster. Le Gestionnaire de ressources est alors partagé entre l’utilisation efficace des machines dans ce domaine « lourd » et le placement des services de façon à ce que la perte du domaine n’entraîne aucun problème.

 Dans le diagramme ci-dessous, nous avons configuré deux clusters différents : un où les nœuds sont bien distribués sur les domaines d’erreur et un autre où un domaine d’erreur finit avec beaucoup plus de nœuds que les autres. Notez que dans Azure, vous n’avez pas à gérer la répartition des nœuds dans les domaines d’erreur et de mise à niveau. De ce fait, vous ne devriez jamais constater ce type de déséquilibre. Cependant, si vous créez votre propre cluster sur site ou dans un autre environnement, c’est une chose à laquelle vous devez penser.

 ![Deux dispositions de cluster différentes][Image2]

### Domaines de mise à niveau

Les domaines de mise à niveau correspondent à une autre fonctionnalité qui permet au Gestionnaire de ressources Service Fabric de comprendre la disposition du cluster afin de planifier les échecs à l’avance. Les domaines de mise à niveau définissent des zones (des ensembles de nœuds, en réalité) qui s’arrêteront simultanément au cours d’une mise à niveau.

Les domaines de mise à niveau sont très semblables aux domaines d’erreur, avec cependant quelques différences clés. Tout d’abord, les domaines de mise à niveau sont généralement définis par la stratégie, alors que les domaines d’erreur sont rigoureusement définis par les zones d’échec coordonné (et donc généralement par la disposition matérielle de l’environnement). Toutefois, dans le cas des domaines de mise à niveau, vous devez décider du nombre de domaines. Une autre différence est que les domaines de mise à niveau (en tout cas, à l’heure actuelle) ne sont pas hiérarchiques. Ils ressemblent davantage à une simple balise qu’à une hiérarchie.

L’illustration ci-dessous montre une configuration fictive, dans laquelle nous avons trois domaines de mise à niveau répartis sur trois domaines d’erreur. Elle montre également un emplacement possible pour trois réplicas différents d’un service avec état. Notez qu’ils se trouvent tous dans des domaines d’erreur et de mise à niveau distincts. Cela signifie que nous pouvons perdre un domaine d’erreur au milieu d’une mise à niveau de service et qu’il y aurait toujours une copie du code et des données en cours d’exécution dans le cluster. Selon vos besoins, cela peut être suffisant. Cependant, vous remarquerez peut-être que cette copie est ancienne (dans la mesure où Service Fabric utilise une réplication basée sur quorum). Pour véritablement faire face à deux défaillances, vous avez besoin de davantage de réplicas (cinq au minimum).

![Positionnement avec des domaines d’erreur et de mise à niveau][Image3]

Il existe des avantages et des inconvénients au fait de disposer de nombreux domaines de mise à niveau : l’avantage est que chaque étape de la mise à niveau est plus granulaire et affecte donc un plus petit nombre de nœuds ou de services. Cela signifie que le nombre de services à déplacer simultanément est moindre, ce qui limite les mouvements sur le système et améliore globalement la fiabilité (dans la mesure où un nombre inférieur de services est affecté par les problèmes). L’inconvénient d’avoir de nombreux domaines de mise à niveau est que Service Fabric vérifie l’intégrité de chaque domaine de mise à niveau lors des mises à niveau, et garantit que le domaine de mise à niveau est sain avant de passer au domaine de mise à niveau suivant. L’objectif de cette vérification est de s’assurer que les services ont la possibilité de se stabiliser et que leur intégrité est validée avant la poursuite de la mise à niveau, afin que les problèmes soient détectés. Le compromis est acceptable, car il empêche les modifications incorrectes d’affecter une trop grande partie du service à la fois.

Une quantité trop faible de domaines de mise à niveau a également des effets secondaires : lorsque l’un des domaines de mise à niveau est arrêté et en cours de mise à niveau, une grande partie de votre capacité globale n’est pas disponible. Par exemple, si vous avez seulement trois domaines de mise à niveau, vous vous défaites d’environ 1/3 de votre service global ou de votre capacité de cluster à la fois. Ce n’est pas souhaitable, dans la mesure où vous devez disposer d’une capacité suffisante dans le reste de votre cluster afin d’assurer la charge de travail, ce qui signifie qu’en situation normale, ces nœuds sont moins chargés qu’ils ne devraient l’être, augmentant ainsi le coût des produits vendus.

Il n’existe aucune limite réelle au nombre total de domaines d’erreur ou de mise à niveau dans un environnement, ni de contraintes sur la façon dont ils se chevauchent. Les structures les plus fréquemment rencontrées sont les structures 1:1 (où chaque domaine d’erreur unique est mappé à son propre domaine de mise à niveau), les structures avec un domaine de mise à niveau par nœud (instance de système d’exploitation physique ou virtuel) et un modèle « agrégé par bandes » ou de « matrice » dans lequel les domaines d’erreur et les domaines de mise à niveau forment une matrice où les machines s’exécutent généralement en suivant la matrice diagonale.

![Dispositions de domaines d’erreur et de mise à niveau][Image4]

Il n’existe pas de disposition idéale, chacune ayant des avantages et des inconvénients. Par exemple, le modèle 1:1 est relativement simple à configurer, tandis que le modèle avec un domaine de mise à niveau par nœud correspond davantage à ce à quoi les utilisateurs étaient habitués par le passé pour la gestion de petits ensembles de machines, où chacune pouvait être arrêtée de manière indépendante.

Le modèle le plus courant (et celui que nous utilisons pour les clusters Azure Service Fabric hébergés) est la matrice domaine d’erreur/domaine de mise à niveau, où les domaines d’erreur et les domaines de mise à niveau forment une table et où les nœuds sont placés le long de la diagonale. En fonction du nombre total de nœuds par rapport au nombre de domaines d’erreur et de mise à niveau, les nœuds seront partiellement alloués ou compressés (autrement dit, si le cluster est suffisamment grand, presque tout finit par se présenter comme le modèle de matrice dense, représenté dans l’option en bas à droite de la Figure 10).

## Configuration des domaines d’erreur et de mise à niveau
La définition des domaines d’erreur et de mise à niveau s’effectue automatiquement dans les déploiements Service Fabric hébergés sur Azure ; Service Fabric récupère simplement les informations d’environnement d’Azure. De votre côté, en tant qu’utilisateur, vous pouvez choisir le nombre de domaines que vous souhaitez. Dans Azure, les informations sur les domaines d’erreur et de mise à niveau semblent être composées d’un « niveau unique », mais en réalité, elles englobent les informations des couches inférieures d’Azure Stack et présentent les domaines logiques d’erreur et de mise à niveau du point de vue de l’utilisateur.

Si vous créez votre propre cluster (ou que vous voulez seulement tester une topologie donnée sur votre ordinateur de développement), vous devez fournir les informations de domaine d’erreur et de domaine de mise à niveau par défaut vous-même. Dans cet exemple, nous définissons un cluster à 9 nœuds qui s’étend sur trois « centres de données » (chacun avec trois racks) et trois domaines de mise à niveau répartis sur ces trois centres de données. Dans votre manifeste de cluster figure quelque chose comme ceci :

ClusterManifest.xml

```xml
  <Infrastructure>
    <!-- IsScaleMin indicates that this cluster runs on one-box /one single server -->
    <WindowsServer IsScaleMin="true">
      <NodeList>
        <Node NodeName="Node01" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType01" FaultDomain="fd:/DC01/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node02" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType02" FaultDomain="fd:/DC01/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node03" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType03" FaultDomain="fd:/DC01/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node04" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType04" FaultDomain="fd:/DC02/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node05" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType05" FaultDomain="fd:/DC02/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node06" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType06" FaultDomain="fd:/DC02/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node07" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType07" FaultDomain="fd:/DC03/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node08" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType08" FaultDomain="fd:/DC03/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node09" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType09" FaultDomain="fd:/DC03/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
      </NodeList>
    </WindowsServer>
  </Infrastructure>
```
> [AZURE.NOTE] Dans les déploiements Azure, les domaines d'erreur et de mise à niveau affectés par Azure. Par conséquent, la définition des nœuds et des rôles au sein de l'option Infrastructure pour Azure n'inclut pas les informations sur les domaines d'erreur ou de mise à niveau.

## Contraintes de placement et propriétés de nœud
Parfois (en réalité, la plupart du temps), vous voudrez vous assurer que certaines charges de travail s’exécutent uniquement sur certains nœuds ou certains ensembles de nœuds. Par exemple, certaines charges de travail peuvent nécessiter des GPU ou des SSD, tandis que d’autres n’en ont pas besoin. Presque toutes les architectures multiniveau en sont un bon exemple : certaines machines servent de système frontal/d’interface desservant une partie de l’application, pendant qu’un autre ensemble (souvent avec des ressources matérielles différentes) gère le travail des couches de calcul ou de stockage. Service Fabric s’attend à ce que, même dans un monde dominé par les microservices, il y ait des cas où des charges de travail particulières doivent s’exécuter sur des configurations matérielles données, par exemple :

- une application multiniveau existante a été « augmentée et déplacée » dans un environnement Service Fabric
- une charge de travail veut s’exécuter sur un matériel spécifique pour des raisons d’isolation de sécurité, de performance ou de mise à l’échelle
-	une charge de travail doit être isolée des autres charges de travail pour des raisons de stratégie ou de consommation de ressources

Pour prendre en charge ces types de configurations, Service Fabric dispose d’une notion de premier ordre de ce que nous appelons les contraintes de placement. Les contraintes peuvent être utilisées pour indiquer où certains services doivent s’exécuter. Les utilisateurs peuvent étendre l’ensemble des contraintes, ce qui signifie qu’il est possible d’appliquer une balise aux nœuds avec des propriétés personnalisées, puis de les sélectionner.

![Disposition du cluster avec différentes charges de travail][Image5]

Les différentes balises clé/valeur sur les nœuds sont appelées propriétés de positionnement de nœud (ou simplement propriétés de nœud), tandis que l’instruction au niveau du service s’appelle une contrainte de placement. La valeur spécifiée dans la propriété de nœud peut être une chaîne, une valeur booléenne ou une valeur signée longue. La contrainte peut être n’importe quelle instruction booléenne qui opère sur les différentes propriétés de nœud dans le cluster, et le service peut uniquement être placé sur les nœuds où l’instruction prend la valeur « True ». Les nœuds sans une propriété définie ne correspondent à aucune contrainte de placement contenant cette propriété. Service Fabric définit également certaines propriétés par défaut qui peuvent être utilisées automatiquement sans que l’utilisateur ait à les définir. À ce jour, les propriétés par défaut définies sur chaque nœud sont NodeType et NodeName. En général, NodeType fait partie des propriétés les plus couramment utilisées, dans la mesure où elle correspond généralement parfaitement à un type de machine, qui à son tour correspond à un type de charge de travail dans une architecture d’application multiniveau classique.

![Contraintes de placement et propriétés de nœud][Image6]

Supposons que les propriétés de nœud suivantes aient été définies pour un type de nœud donné : ClusterManifest.xml

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasDisk" Value="true"/>
        <Property Name="Value" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

Vous pouvez créer des contraintes de placement de service pour un service comme suit :

C#

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasDisk == true && Value >= 4)";
// add other required servicedescription fields
//...
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

PowerShell :

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasDisk == true && Value >= 4"
```

Si vous êtes sûr que tous les nœuds de NodeType01 sont valides, vous pouvez également simplement sélectionner ce type de nœud, en utilisant les contraintes de placement comme indiqué dans les images ci-dessus.

Un des avantages offerts par les contraintes de placement d’un service est qu’elles peuvent être mises à jour dynamiquement pendant l’exécution. Par conséquent, si vous le souhaitez, vous pouvez déplacer un service dans le cluster, ajouter et supprimer des exigences, etc. Service Fabric s’occupe de vous assurer que le service reste opérationnel et disponible, même lorsque ces types de modifications sont en cours.

C# :

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

PowerShell :

```posh
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

Les contraintes de placement (ainsi que de nombreuses autres propriétés dont nous allons parler) sont spécifiées pour chaque instance de service individuelle. Les mises à jour prennent toujours la place (remplacent) de ce qui a été précédemment spécifié.

### Capacité
L’une des principales tâches de n’importe quel orchestrateur consiste à vous aider à gérer la consommation de ressources du cluster. Si vous tentez d’exécuter efficacement des services, vous ne voudrez certainement pas qu’un ensemble de nœuds soit actif (entraînant des conflits de ressources et des performances médiocres) tandis que d’autres sont froids (gaspillage de ressources). Mais prenons un exemple encore plus simple que l’équilibrage de charge (que nous examinerons dans une minute) : et si vous vous assuriez en premier lieu que les nœuds ne soient pas à cours de ressources ?

Il s’avère que Service Fabric représente les ressources sous la forme d’éléments qu’il appelle « métriques ». Les métriques correspondent à n’importe quelle ressource logique ou physique que vous souhaitez décrire pour Service Fabric. Par exemple, « WorkQueueDepth » et « MemoryInMb » sont des métriques. Les métriques sont différentes des contraintes et des propriétés de nœud, dans la mesure où les propriétés de nœud sont généralement des descripteurs statiques des nœuds eux-mêmes, tandis que les métriques concernent les ressources physiques que les services consomment lorsqu’ils s’exécutent sur un nœud. Une propriété pourrait donc être par exemple HasSSD et pourrait être définie sur true ou false, tandis que la quantité d’espace disponible sur ce disque SSD (et consommée par les services) serait une métrique comme « DriveSpaceInMb ». La capacité du nœud définit « DriveSpaceInMb » sur la quantité totale d’espace non réservé sur le disque, et les services indiquent la quantité de la métrique qu’ils ont utilisée pendant leur exécution.

Si vous avez désactivé toutes les fonctions d’équilibrage des ressources de Service Fabric, le Gestionnaire de ressources est tout de même en mesure de garantir qu’aucun nœud ne dépasse sa capacité (à moins que le cluster dans son ensemble ne soit trop plein). Les capacités correspondent au mécanisme que Service Fabric utilise pour déterminer la quantité d’une ressource dont dispose un nœud. Il faut alors déduire la consommation des différents services (nous reviendrons sur ce point plus tard), afin de savoir quelle capacité il reste. La capacité et la consommation au niveau du service sont toutes deux exprimées en termes de métriques.

Lors de l’exécution, le Gestionnaire de ressources suit la quantité de chaque ressource présente sur chaque nœud (définie par sa capacité) et la quantité restante (en soustrayant toute utilisation déclarée par chaque service). Grâce à ces informations, le Gestionnaire de ressources de Service Fabric peut déterminer où placer ou déplacer les réplicas afin que les nœuds ne dépassent pas la capacité.

C# :

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceLoadMetricDescription metric = new ServiceLoadMetricDescription();
metric.Name = "Memory";
metric.PrimaryDefaultLoad = 1024;
metric.SecondaryDefaultLoad = 1024;
metric.Weight = ServiceLoadMetricWeight.High;
serviceDescription.Metrics.Add(metric);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

PowerShell :

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("Memory,High,1024,1024”)
```

![Nœuds de cluster et capacité][Image7]

Ces informations figurent dans le manifeste de cluster :

ClusterManifest.xml

```xml
    <NodeType Name="NodeType02">
      <Capacities>
        <Capacity Name="Memory" Value="10"/>
        <Capacity Name="Disk" Value="10000"/>
      </Capacities>
    </NodeType>
```

Il est également possible que la charge d’un service évolue de manière dynamique. Dans ce cas, il est possible que l’emplacement actuel d’un réplica ou d’une instance devienne non valide, étant donné que l’utilisation combinée de tous les réplicas et de toutes les instances de ce nœud dépasse les capacités du nœud. Nous étudierons ultérieurement ce scénario, dans lequel la charge peut être modifiée de manière dynamique, mais où, en termes de capacité, elle est gérée de la même façon : la gestion des ressources de Service Fabric intervient automatiquement et ramène le nœud au-dessous de la capacité en déplaçant un ou plusieurs réplicas ou instances de ce nœud vers des nœuds différents. Ce faisant, le Gestionnaire de ressources essaie de réduire le coût de tous les mouvements (nous reviendrons sur la notion de coût ultérieurement).

###Capacité de cluster

Par conséquent, comment faire en sorte que l’ensemble du cluster ne soit pas saturé ? Avec la charge dynamique, nous ne pouvons en réalité pas faire grand-chose (étant donné que les services peuvent avoir un pic de charge indépendamment des actions effectuées par le Gestionnaire de ressources : un cluster ayant beaucoup de marge aujourd’hui pourra manquer de puissance demain), mais il existe certains contrôles conçus pour éviter les erreurs de base. La première chose que nous pouvons faire est d’empêcher la création de nouvelles charges de travail qui risquent de saturer le cluster.

Supposons que vous créez un service sans état simple et qu’une charge y est associée (nous reviendrons plus tard sur les rapports de charge par défaut et dynamique). Supposons que ce service ait besoin de certaines ressources (par exemple l’espace disque) et qu’il consomme par défaut 5 unités d’espace disque pour chaque instance du service. Vous souhaitez créer 3 instances du service. Parfait ! Cela signifie que nous avons besoin de 15 unités d’espace disque dans le cluster pour être en mesure de créer ces instances de service. Service Fabric calcule en continu la capacité globale et la consommation de chaque métrique, pour que nous puissions facilement effectuer cette détermination et rejeter l’appel de création de service si l’espace est insuffisant.

Notez que dans la mesure où la spécification est uniquement que 15 unités soient disponibles, cet espace peut être alloué de différentes façons. Il peut s’agir d’une seule unité restante sur 15 nœuds différents, par exemple, ou de trois unités restantes sur 5 nœuds différents, etc. S’il ne trouve pas la capacité suffisante sur trois nœuds différents, Service Fabric réorganise les services déjà dans le cluster afin de libérer de l’espace sur les trois nœuds nécessaires. Cette réorganisation est presque toujours possible à moins que le cluster dans son ensemble soit presque entièrement complet.

Pour aider les utilisateurs à gérer la capacité globale du cluster, nous avons ajouté un système de mémoire tampon réservée à la capacité spécifiée sur chaque nœud. Ce paramètre est facultatif, mais il permet aux utilisateurs de réserver une partie de la capacité globale du nœud afin qu’elle soit uniquement utilisée pour placer des services au cours des mises à niveau et des échecs, c’est-à-dire dans les cas où la capacité du cluster est réduite. Aujourd’hui, la mémoire tampon est indiquée de manière globale par métrique pour tous les nœuds via le manifeste de cluster. La valeur que vous choisissez pour la capacité de réserve sera une fonction des ressources sur lesquelles vos services sont soumis au plus grand nombre de contraintes, ainsi que du nombre de domaines d’erreur et de mise à niveau dont vous disposez dans le cluster. Généralement, un plus grand nombre de domaines d’erreur et de mise à niveau signifie que vous pouvez choisir une capacité de mémoire tampon inférieure, dans la mesure où vous pouvez vous attendre à ce qu’une plus petite portion de votre cluster soit indisponible lors des défaillances et des mises à niveau. Notez que l’indication du pourcentage de mémoire tampon est pertinente uniquement si vous avez également spécifié la capacité du nœud pour une métrique.

ClusterManifest.xml

```xml
        <Section Name=" NodeBufferPercentage">
            <Parameter Name="DiskSpace" Value="10" />
            <Parameter Name="Memory" Value="15" />
            <Parameter Name="SomeOtherMetric" Value="20" />
        </Section>
```
Les appels de création générant de nouveaux services échouent lorsque le cluster est hors de la capacité de mise en mémoire tampon, garantissant ainsi que le cluster conserve suffisamment d’espace de secours pour que les défaillances et les mises à niveau n’entraînent pas un dépassement de la capacité par les nœuds. Le Gestionnaire de ressources expose un grand nombre de ces informations via PowerShell et les API de requête, ce qui vous permet de voir les paramètres de capacité de mise en mémoire tampon, la capacité totale et la consommation actuelle de chaque métrique donnée. Voici un exemple de cette sortie :

```posh
PS C:\Users\user> Get-ServiceFabricClusterLoadInformation
LastBalancingStartTimeUtc : 9/1/2015 12:54:59 AM
LastBalancingEndTimeUtc   : 9/1/2015 12:54:59 AM
LoadMetricInformation     :
                            LoadMetricName        : Metric1
                            IsBalancedBefore      : False
                            IsBalancedAfter       : False
                            DeviationBefore       : 0.192450089729875
                            DeviationAfter        : 0.192450089729875
                            BalancingThreshold    : 1
                            Action                : NoActionNeeded
                            ActivityThreshold     : 0
                            ClusterCapacity       : 189
                            ClusterLoad           : 45
                            ClusterRemainingCapacity : 144
                            NodeBufferPercentage  : 10
                            ClusterBufferedCapacity : 170
                            ClusterRemainingBufferedCapacity : 125
                            ClusterCapacityViolation : False
                            MinNodeLoadValue      : 0
                            MinNodeLoadNodeId     : 3ea71e8e01f4b0999b121abcbf27d74d
                            MaxNodeLoadValue      : 15
                            MaxNodeLoadNodeId     : 2cc648b6770be1bc9824fa995d5b68b1
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes
- [En savoir plus sur l’architecture du Gestionnaire de ressources de cluster](service-fabric-cluster-resource-manager-architecture.md)
- [En savoir plus sur les mesures de défragmentation](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
- [Obtenir une présentation du Gestionnaire de ressources de cluster Service Fabric](service-fabric-cluster-resource-manager-introduction.md)

[Image1]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png

<!---------HONumber=AcomDC_0309_2016-->