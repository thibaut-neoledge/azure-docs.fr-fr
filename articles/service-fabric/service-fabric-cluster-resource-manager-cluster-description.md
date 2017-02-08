---
title: "Description du cluster de l’équilibreur de ressources | Microsoft Docs"
description: "Description d’un cluster Service Fabric en spécifiant les domaines d’erreur, domaines de mise à niveau, propriétés du nœud et capacités du nœud du Cluster Resource Manager."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 55f8ab37-9399-4c9a-9e6c-d2d859de6766
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/19/2016
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 62000440292dd12f5744bbc8ec558cd92d8c6235


---
# <a name="describing-a-service-fabric-cluster"></a>Description d’un cluster Service Fabric
Le Gestionnaire de ressources de cluster Service Fabric fournit plusieurs mécanismes permettant de décrire un cluster. Pendant l’exécution, le Gestionnaire de ressources de cluster utilise ces informations pour garantir une haute disponibilité des services en cours d’exécution dans le cluster, tout en faisant en sorte que les ressources du cluster soient convenablement utilisées.

## <a name="key-concepts"></a>Concepts clés
Le Gestionnaire de ressources de cluster prend en charge plusieurs fonctionnalités qui décrivent un cluster :

* Domaines d'erreur
* Domaines de mise à niveau
* Propriétés du nœud
* Capacités du nœud

## <a name="fault-domains"></a>Domaines d'erreur
Un domaine d’erreur est une zone d’échec coordonné. Une machine unique constitue un domaine d’erreur (puisqu’elle peut cesser de fonctionner de manière indépendante pour de nombreuses raisons : coupure électrique, défaillance de disque ou erreur de microprogramme de carte d’interface réseau). Si plusieurs machines sont connectées au même commutateur Ethernet, elles se trouvent dans le même domaine d’erreur, tout comme celles connectées à une source d’alimentation unique. Dans la mesure où leur chevauchement est naturel, les domaines d’erreur sont hiérarchiques par nature et sont représentés en tant qu’URI dans Service Fabric.

Si vous avez défini votre propre cluster, vous devez réfléchir à ces différents domaines d’erreur et vous assurer que vos domaines d’erreur ont été configurés correctement pour que Service Fabric sache où placer des services de manière sûre. Par « sûre », nous voulons dire « intelligente » : nous ne voulons pas placer des services de telle manière que la perte d’un domaine d’erreur (par exemple, la défaillance d’un des composants énumérés plus haut) entraîne leur panne.  Dans l’environnement Azure, nous exploitons les informations de domaine d’erreur fournies par l’environnement pour configurer correctement les nœuds du cluster en votre nom.
Pour donner un exemple simple, dans l’illustration ci-dessous (Figure 7), nous avons appliqué une couleur à toutes les entités qui correspondent raisonnablement à un domaine d’erreur et répertorié les différents domaines d’erreur correspondants. Dans cet exemple, nous avons des centres de données (DC), des racks (R) et des panneaux (B). En théorie, si chaque panneau contient plusieurs machines virtuelles, il peut exister une autre couche dans la hiérarchie de domaine d’erreur.

![Nœuds organisés par domaines d'erreur][Image1]

 Lors de l’exécution, le Gestionnaire de ressources de cluster Service Fabric considère les domaines d’erreur dans le cluster et tente de répartir les réplicas d’un service donné afin qu’ils soient tous dans des domaines d’erreur distincts. Ce processus permet de s’assurer qu’en cas d’échec d’un domaine d’erreur (quel que soit le niveau de la hiérarchie), la disponibilité de ce service n’est pas compromise.

 Le gestionnaire de ressources de cluster Service Fabric ne tient pas véritablement compte du nombre de couches de la hiérarchie. Cependant, dans la mesure où il cherche à s’assurer que la perte d’une partie de la hiérarchie n’influe pas sur le cluster ou les services exécutés sur celui-ci, il est généralement préférable que chaque niveau de profondeur du domaine d’erreur inclue le même nombre de machines. Cela évite qu’une partie de la hiérarchie doive contenir plus de services que d’autres à la fin de la journée.

 Si vous configurez votre cluster d’une manière qui crée un déséquilibre dans l’« arborescence » de domaines d’erreur, le Gestionnaire de ressources de cluster aura du mal à déterminer l’allocation optimale des réplicas, en particulier car cela signifie que la perte d’un domaine donné peut grandement affecter la disponibilité du cluster. Le Gestionnaire de ressources de cluster est alors partagé entre l’utilisation efficace des machines dans ce domaine « lourd » en plaçant des services dessus et le placement des services de façon à ce que la perte du domaine n’entraîne aucun problème.

 Dans le diagramme ci-dessous, nous montrons deux exemples de disposition de cluster différents : un où les nœuds sont bien distribués sur les domaines d’erreur et un autre où un domaine d’erreur finit avec beaucoup plus de nœuds que les autres.  Notez que dans Azure, vous n’avez pas à gérer la répartition des nœuds dans les domaines d’erreur et de mise à niveau. De ce fait, vous ne devriez jamais constater ce type de déséquilibre. Cependant, si vous créez votre propre cluster sur site ou dans un autre environnement, c’est une chose à laquelle vous devez penser.

 ![Deux dispositions de cluster différentes][Image2]

## <a name="upgrade-domains"></a>Domaines de mise à niveau
Les domaines de mise à niveau correspondent à une autre fonctionnalité qui permet au Gestionnaire de ressources Service Fabric de comprendre la disposition du cluster afin de planifier les échecs à l’avance. Les domaines de mise à niveau définissent des zones (des ensembles de nœuds, en réalité) qui s’arrêteront simultanément au cours d’une mise à niveau.

Les domaines de mise à niveau sont très semblables aux domaines d’erreur, avec cependant quelques différences clés. Tout d’abord, les domaines de mise à niveau sont généralement définis par la stratégie, alors que les domaines d’erreur sont rigoureusement définis par les zones d’échec coordonné (et donc généralement par la disposition matérielle de l’environnement). Toutefois, dans le cas des domaines de mise à niveau, vous devez décider du nombre de domaines. Une autre différence est que les domaines de mise à niveau (en tout cas, à l’heure actuelle) ne sont pas hiérarchiques. Ils ressemblent davantage à une simple balise qu’à une hiérarchie.

L’illustration ci-dessous montre une configuration fictive, dans laquelle nous avons trois domaines de mise à niveau répartis sur trois domaines d’erreur. Elle montre également un emplacement possible pour trois réplicas différents d’un service avec état. Notez qu’ils se trouvent tous dans des domaines d’erreur et de mise à niveau distincts. Cela signifie que nous pouvons perdre un domaine d’erreur au milieu d’une mise à niveau de service et qu’il y aurait toujours une copie du code et des données en cours d’exécution dans le cluster. Selon vos besoins, cela peut être suffisant. Cependant, vous remarquerez peut-être que cette copie est ancienne (dans la mesure où Service Fabric utilise une réplication basée sur quorum). Pour véritablement faire face à deux défaillances, vous avez besoin de davantage de réplicas (cinq au minimum).

![Positionnement avec des domaines d’erreur et de mise à niveau][Image3]

Il existe des avantages et des inconvénients au fait de disposer de nombreux domaines de mise à niveau : l’avantage est que chaque étape de la mise à niveau est plus granulaire et affecte donc un plus petit nombre de nœuds ou de services. Cela signifie que le nombre de services à déplacer simultanément est moindre, ce qui limite les mouvements sur le système et améliore globalement la fiabilité (dans la mesure où un nombre inférieur de services est affecté par les problèmes). L’inconvénient d’avoir de nombreux domaines de mise à niveau est que Service Fabric vérifie l’intégrité de chaque domaine de mise à niveau lors des mises à niveau, et garantit que le domaine de mise à niveau est sain avant de passer au domaine de mise à niveau suivant. L’objectif de cette vérification est de s’assurer que les services ont la possibilité de se stabiliser et que leur intégrité est validée avant la poursuite de la mise à niveau, afin que les problèmes soient détectés. Le compromis est acceptable, car il empêche les modifications incorrectes d’affecter une trop grande partie du service à la fois.

Une quantité trop faible de domaines de mise à niveau a également des effets secondaires : lorsque l’un des domaines de mise à niveau est arrêté et en cours de mise à niveau, une grande partie de votre capacité globale n’est pas disponible. Par exemple, si vous avez seulement trois domaines de mise à niveau, vous vous défaites d’environ 1/3 de votre service global ou de votre capacité de cluster à la fois. Ce n’est pas souhaitable, dans la mesure où vous devez disposer d’une capacité suffisante dans le reste de votre cluster afin d’assurer la charge de travail, ce qui signifie qu’en situation normale, ces nœuds sont moins chargés qu’ils ne devraient l’être, augmentant ainsi le coût des produits vendus.

Il n’existe aucune limite réelle au nombre total de domaines d’erreur ou de mise à niveau dans un environnement, ni de contraintes sur la façon dont ils se chevauchent. Les structures les plus fréquemment rencontrées sont les structures 1:1 (où chaque domaine d’erreur unique est mappé à son propre domaine de mise à niveau), les structures avec un domaine de mise à niveau par nœud (instance de système d’exploitation physique ou virtuel) et un modèle « agrégé par bandes » ou de « matrice » dans lequel les domaines d’erreur et les domaines de mise à niveau forment une matrice où les machines s’exécutent généralement en suivant la matrice diagonale.

![Dispositions de domaines d’erreur et de mise à niveau][Image4]

Il n’existe pas de disposition idéale, chacune ayant des avantages et des inconvénients. Par exemple, le modèle 1:1 est relativement simple à configurer, tandis que le modèle avec un domaine de mise à niveau par nœud correspond davantage à ce à quoi les utilisateurs étaient habitués par le passé pour la gestion de petits ensembles de machines, où chacune pouvait être arrêtée de manière indépendante.

Le modèle le plus courant (et celui que nous utilisons pour les clusters Azure Service Fabric hébergés) est la matrice domaine d’erreur/domaine de mise à niveau, où les domaines d’erreur et les domaines de mise à niveau forment une table et où les nœuds sont placés le long de la diagonale. En fonction du nombre total de nœuds par rapport au nombre de domaines d’erreur et de mise à niveau, les nœuds seront partiellement alloués ou compressés (autrement dit, si le cluster est suffisamment grand, presque tout finit par se présenter comme le modèle de matrice dense, représenté dans l’option en bas à droite de la Figure 10).

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>Contraintes des domaines d’erreur et de mise à niveau, et comportement résultant
Le Gestionnaire de ressources de cluster traite le souhait de conserver un service équilibré entre les domaines d’erreur et de mise à niveau en tant que contrainte. Vous trouverez plus d’informations sur les contraintes dans [cet article](service-fabric-cluster-resource-manager-management-integration.md). Les contraintes des domaines d’erreur et de mise à niveau sont définies comme suit : « pour une partition de service donnée, il ne doit jamais y avoir une différence *supérieure à un* dans le nombre de réplicas entre deux domaines. »  » Concrètement, cela signifie que pour un service donné, certains mouvements ou arrangements peuvent ne pas être valides dans le cluster, car cela enfreindrait la contrainte du domaine de mise à niveau ou d’erreur.

Examinons un exemple. Supposons que nous avons un cluster avec 6 nœuds, configuré avec 5 domaines d’erreur et 5 de mise à niveau.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| UD0 |N1 | | | | |
| UD1 |N6 |N2 | | | |
| UD2 | | |N3 | | |
| UD3 | | | |N4 | |
| UD4 | | | | |N5 |

Supposons maintenant que nous créons un service avec TargetReplicaSetSize défini sur 5. Les réplicas se trouvent sur N1-N5. En fait, N6 ne sera jamais utilisé. Mais pourquoi ? Observons la différence entre la disposition actuelle et ce qui surviendrait si nous avions choisi N6 à la place, et réfléchissons à la façon dont cela se rapporte à notre définition de la contrainte de domaine d’erreur et de mise à niveau.

Voici la disposition que nous obtenons et le nombre total de réplicas par domaine d’erreur et de mise à niveau.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| UD0 |R1 | | | | |1 |
| UD1 | |R2 | | | |1 |
| UD2 | | |R3 | | |1 |
| UD3 | | | |R4 | |1 |
| UD4 | | | | |R5 |1 |
| FDTotal |1 |1 |1 |1 |1 |- |

Notez que cette disposition est équilibrée en matière de nœuds par domaine d’erreur et de domaine de mise à niveau, ainsi qu’en matière de nombre de réplicas par domaine d’erreur et de domaine de mise à niveau. Chaque domaine possède le même nombre de nœuds et le même nombre de réplicas.

À présent, jetons un œil à ce qui se passerait si au lieu de N2, nous avions utilisé N6. Comment les réplicas auraient-ils été réparties ? Le résultat suivant ressemblerait à ceci :

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| UD0 |R1 | | | | |1 |
| UD1 |R5 | | | | |1 |
| UD2 | | |R2 | | |1 |
| UD3 | | | |R3 | |1 |
| UD4 | | | | |R4 |1 |
| FDTotal |2 |0 |1 |1 |1 |- |

Cela enfreint notre définition pour la contrainte de domaine d’erreur, car FD0 a 2 réplicas, tandis que FD1 en a 0, ce qui donne une différence totale de 2. Ainsi, le Gestionnaire de ressources de cluster ne permet pas cet arrangement. De la même façon, en choisissant N2-6, nous aurions obtenu :

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| UD0 | | | | | |0 |
| UD1 |R5 |R1 | | | |2 |
| UD2 | | |R2 | | |1 |
| UD3 | | | |R3 | |1 |
| UD4 | | | | |R4 |1 |
| FDTotal |1 |1 |1 |1 |1 |- |

Ce qui, bien qu’équilibré en matière de domaines d’erreur, enfreint la contrainte du domaine de mise à niveau (car UD0 a 0 réplique contre 2 pour UD1), et est donc invalide aussi.

## <a name="configuring-fault-and-upgrade-domains"></a>Configuration des domaines d’erreur et de mise à niveau
La définition des domaines d’erreur et de mise à niveau s’effectue automatiquement dans les déploiements Service Fabric hébergés sur Azure ; Service Fabric récupère simplement les informations d’environnement d’Azure. Dans Azure, les informations sur les domaines d’erreur et de mise à niveau semblent être composées d’un « niveau unique », mais en réalité, elles englobent les informations des couches inférieures d’Azure Stack et présentent les domaines logiques d’erreur et de mise à niveau du point de vue de l’utilisateur.

Si vous créez votre propre cluster (ou que vous voulez seulement tester une topologie donnée sur votre ordinateur de développement), vous devez fournir les informations de domaine d’erreur et de domaine de mise à niveau par défaut vous-même. Dans cet exemple, nous définissons un cluster de développement local à 9 nœuds qui s’étend sur trois « centres de données » (chacun avec trois racks) et trois domaines de mise à niveau répartis sur ces trois centres de données. Dans le modèle de manifeste de cluster figure quelque chose comme ceci :

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
> [!NOTE]
> Dans les déploiements Azure, les domaines d'erreur et de mise à niveau affectés par Azure. Par conséquent, la définition des nœuds et des rôles au sein de l'option Infrastructure pour Azure n'inclut pas les informations sur les domaines d'erreur ou de mise à niveau.
> 
> 

## <a name="placement-constraints-and-node-properties"></a>Contraintes de positionnement et propriétés de nœud
Parfois (en réalité, la plupart du temps), vous voudrez vous assurer que certaines charges de travail s’exécutent uniquement sur certains nœuds ou certains ensembles de nœuds dans le cluster. Par exemple, certaines charges de travail peuvent nécessiter des GPU ou des SSD, tandis que d’autres n’en ont pas besoin. Presque toutes les architectures multiniveau en sont un bon exemple : certaines machines servent de système frontal/d’interface desservant une partie de l’application (et sont donc probablement exposées à Internet), pendant qu’un autre ensemble (souvent avec des ressources matérielles différentes) gère le travail des couches de calcul ou de stockage (et n’est généralement pas exposé à Internet). Service Fabric s’attend à ce que, même dans un monde dominé par les microservices, il y ait des cas où des charges de travail particulières doivent s’exécuter sur des configurations matérielles données, par exemple :

* une application multiniveau existante a été « augmentée et déplacée » dans un environnement Service Fabric
* une charge de travail veut s’exécuter sur un matériel spécifique pour des raisons d’isolation de sécurité, de performance ou de mise à l’échelle
* une charge de travail doit être isolée des autres charges de travail pour des raisons de stratégie ou de consommation de ressources

Pour prendre en charge ces types de configurations, Service Fabric dispose d’une notion de premier ordre de ce que nous appelons les contraintes de placement. Les contraintes de placement peuvent être utilisées pour indiquer où certains services doivent s’exécuter. Les utilisateurs peuvent étendre l’ensemble des contraintes, ce qui signifie qu’il est possible d’appliquer une balise aux nœuds avec des propriétés personnalisées, puis de les sélectionner.

![Disposition du cluster avec différentes charges de travail][Image5]

Les différentes balises clé/valeur sur les nœuds sont appelées *propriétés* de positionnement de nœud (ou simplement propriétés de nœud), tandis que l’instruction au niveau du service s’appelle une *contrainte* de placement. La valeur spécifiée dans la propriété de nœud peut être une chaîne, une valeur booléenne ou une valeur signée longue. La contrainte peut être toute déclaration booléenne qui opère sur les différentes propriétés de nœud du cluster. Les sélecteurs valides dans ces déclarations booléennes (qui sont des chaînes) sont :

* des vérifications conditionnelles pour la création d’instructions particulières
  * « égal à » ==
  * « supérieur à » >
  * « inférieur à » <
  * « non égal à » !=
  * « supérieur ou égal à » >=
  * « inférieur ou égal à » <=
* instructions booléennes pour groupage et négation
  * « et » &&
  * « ou » ||
  * « non » !
* parenthèses pour les opérations de groupe
  
  * ()
  
  Voici quelques exemples d’instructions de contrainte de base qui utilisent certains des symboles ci-dessus. Notez que les propriétés d’un nœud peuvent être des valeurs de type chaîne, booléennes ou numériques.   
  
  * « Foo >= 5 »
  * « NodeColor != green »
  * « ((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100))) »

Seuls les nœuds où la déclaration globale prend la valeur « True » peuvent avoir le service placé dessus. Les nœuds sans une propriété définie ne correspondent à aucune contrainte de placement contenant cette propriété.

Service Fabric définit également certaines propriétés par défaut qui peuvent être utilisées automatiquement sans que l’utilisateur ait à les définir. À ce jour, les propriétés par défaut définies sur chaque nœud sont NodeType et NodeName. Par exemple, vous pouvez écrire une contrainte de placement ainsi : « (NodeType == NodeType03) ». En général, NodeType fait partie des propriétés les plus couramment utilisées, dans la mesure où elle correspond généralement parfaitement à un type de machine, qui à son tour correspond à un type de charge de travail dans une architecture d’application multiniveau classique.

![Contraintes de positionnement et propriétés de nœud][Image6]

Supposons que les propriétés de nœud suivantes aient été définies pour un type de nœud donné : ClusterManifest.xml

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

Vous pouvez créer des contraintes de placement de service pour un service comme suit :

C#

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasSSD == true && SomeProperty >= 4)";
// add other required servicedescription fields
//...
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

PowerShell :

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasSSD == true && SomeProperty >= 4"
```

Si vous êtes sûr que tous les nœuds de NodeType01 sont valides, vous pouvez également simplement sélectionner ce type de nœud, en utilisant les contraintes de placement comme indiqué dans les images ci-dessus.

Un des avantages offerts par les contraintes de placement d’un service est qu’elles peuvent être mises à jour dynamiquement pendant l’exécution. Par conséquent, si vous le souhaitez, vous pouvez déplacer un service dans le cluster, ajouter et supprimer des exigences, etc. Service Fabric s’occupe de vous assurer que le service reste opérationnel et disponible, même lorsque ces types de modifications sont en cours.

C# :

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

PowerShell :

```posh
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

Les contraintes de placement (ainsi que de nombreuses autres commandes Orchestrator dont nous allons parler) sont spécifiées pour chaque instance de service individuelle nommée. Les mises à jour prennent toujours la place (remplacent) de ce qui a été précédemment spécifié.

Il est également important de noter qu’à ce stade les propriétés d’un nœud sont définies via la définition du cluster et, par conséquent, ne peuvent pas être mises à jour sans une mise à niveau du cluster et nécessiteront que chaque nœud descende et remonte afin d’actualiser ses propriétés.

## <a name="capacity"></a>Capacité
L’une des principales tâches de n’importe quel orchestrateur consiste à vous aider à gérer la consommation de ressources du cluster. Si vous tentez d’exécuter efficacement des services, vous ne voudrez certainement pas qu’un ensemble de nœuds soit actif (entraînant des conflits de ressources et des performances médiocres) tandis que d’autres sont froids (gaspillage de ressources). Mais prenons un exemple encore plus simple que l’équilibrage de charge (que nous examinerons dans une minute) : et si vous vous assuriez en premier lieu que les nœuds ne soient pas à cours de ressources ?

Service Fabric représente les ressources en tant que mesures. Les métriques correspondent à n’importe quelle ressource logique ou physique que vous souhaitez décrire pour Service Fabric. Par exemple, « WorkQueueDepth » et « MemoryInMb » sont des métriques. Les mesures sont différentes des contraintes de placement et des propriétés de nœud en cela que les propriétés de nœud sont généralement des descripteurs statiques des nœuds eux-mêmes, alors que les mesures sont des ressources que les nœuds ont et que les services consomment lors de leur exécution sur un nœud. Une propriété pourrait donc être par exemple HasSSD et pourrait être définie sur true ou false, tandis que la quantité d’espace disponible sur ce disque SSD (et consommée par les services) serait une métrique comme « DriveSpaceInMb ». La capacité du nœud définit « DriveSpaceInMb » sur la quantité totale d’espace non réservé sur le disque, et les services indiquent la quantité de la métrique qu’ils ont utilisée pendant leur exécution.

Si vous avez désactivé toutes les fonctions *d’équilibrage*des ressources de Service Fabric, le Gestionnaire de ressources de cluster est tout de même en mesure de garantir qu’aucun nœud ne dépasse sa capacité (à moins que le cluster dans son ensemble ne soit trop plein). La capacité est une autre *contrainte* que le Gestionnaire de ressources de cluster utilise pour comprendre la quantité d’une ressource d’un nœud a. La capacité et la consommation au niveau du service sont toutes deux exprimées en termes de métriques. Par exemple, la mesure peut être « MemoryInMb », un nœud donné peut avoir une capacité de 2048 pour MemoryInMb, alors qu’un service donné peut dire qu’il consomme actuellement 64 de MemoryInMb.

Lors de l’exécution, le Gestionnaire de ressources de cluster suit la quantité de chaque ressource présente sur chaque nœud (définie par sa capacité) et la quantité restante (en soustrayant toute utilisation déclarée par chaque service). Grâce à ces informations, le Gestionnaire de ressources de Service Fabric peut déterminer où placer ou déplacer les réplicas afin que les nœuds ne dépassent pas la capacité.

C# :

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceLoadMetricDescription metric = new ServiceLoadMetricDescription();
metric.Name = "MemoryInMb";
metric.PrimaryDefaultLoad = 64;
metric.SecondaryDefaultLoad = 64;
metric.Weight = ServiceLoadMetricWeight.High;
serviceDescription.Metrics.Add(metric);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

PowerShell :

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("Memory,High,64,64)
```

![Nœuds de cluster et capacité][Image7]

Ces informations figurent dans le manifeste de cluster :

ClusterManifest.xml

```xml
    <NodeType Name="NodeType02">
      <Capacities>
        <Capacity Name="MemoryInMb" Value="2048"/>
        <Capacity Name="Disk" Value="10000"/>
      </Capacities>
    </NodeType>
```

Il est également possible que la charge d’un service évolue de manière dynamique. Supposons que la charge d’une réplique est passée de 64 à 1024, mais que le nœud d’exécution d’en avait que 512 (de la mesure « MemoryInMb ») restants à ce moment. Ainsi, il est possible que l’emplacement actuel d’un réplica ou d’une instance devienne non valide, étant donné que l’utilisation combinée de tous les réplicas et de toutes les instances de ce nœud dépasse les capacités du nœud. Nous étudierons ultérieurement ce scénario, dans lequel la charge peut être modifiée de manière dynamique, mais où, en termes de capacité, elle est gérée de la même façon : la gestion des ressources de cluster intervient automatiquement et ramène le nœud au-dessous de la capacité en déplaçant un ou plusieurs réplicas ou instances de ce nœud vers des nœuds différents. Ce faisant, le Gestionnaire de ressources de cluster essaie de réduire le coût de tous les mouvements (nous reviendrons sur la notion de coût ultérieurement).

## <a name="cluster-capacity"></a>Capacité de cluster
Par conséquent, comment faire en sorte que l’ensemble du cluster ne soit pas saturé ? Avec la charge dynamique, nous ne pouvons en réalité pas faire grand-chose (étant donné que les services peuvent avoir un pic de charge indépendamment des actions effectuées par le Gestionnaire de ressources de cluster : un cluster ayant beaucoup de marge aujourd’hui pourra manquer de puissance demain), mais il existe certains contrôles conçus pour éviter les problèmes de base. La première chose que nous pouvons faire est d’empêcher la création de nouvelles charges de travail qui risquent de saturer le cluster.

Supposons que vous créez un service sans état simple et qu’une charge y est associée (nous reviendrons plus tard sur les rapports de charge par défaut et dynamique). Supposons que ce service ait besoin de certaines ressources (par exemple l’espace disque) et qu’il consomme par défaut 5 unités d’espace disque pour chaque instance du service. Vous souhaitez créer 3 instances du service. Parfait ! Cela signifie que nous avons besoin de 15 unités d’espace disque dans le cluster pour être en mesure de créer ces instances de service. Service Fabric calcule en continu la capacité globale et la consommation de chaque métrique, pour que nous puissions facilement effectuer cette détermination et rejeter l’appel de création de service si l’espace est insuffisant.

Notez que dans la mesure où la spécification est uniquement que 15 unités soient disponibles, cet espace peut être alloué de différentes façons. Il peut s’agir d’une seule unité restante sur 15 nœuds différents, par exemple, ou de trois unités restantes sur 5 nœuds différents, etc. S’il ne trouve pas la capacité suffisante sur trois nœuds différents, Service Fabric réorganise les services déjà dans le cluster afin de libérer de l’espace sur les trois nœuds nécessaires. Cette réorganisation est presque toujours possible à moins que le cluster dans son ensemble soit presque entièrement complet.

## <a name="buffered-capacity"></a>Capacité de mise en mémoire tampon
Pour aider les utilisateurs à gérer la capacité globale du cluster, il est possible d’ajouter un système de mémoire tampon réservée à la capacité spécifiée sur chaque nœud. Ce paramètre est facultatif, mais il permet aux utilisateurs de réserver une partie de la capacité globale du nœud afin qu’elle soit uniquement utilisée pour placer des services au cours des mises à niveau et des échecs, c’est-à-dire dans les cas où la capacité du cluster est réduite. Aujourd’hui, la mémoire tampon est indiquée de manière globale par métrique pour tous les nœuds via le manifeste de cluster. La valeur que vous choisissez pour la capacité de réserve sera une fonction des ressources sur lesquelles vos services sont soumis au plus grand nombre de contraintes, ainsi que du nombre de domaines d’erreur et de mise à niveau dont vous disposez dans le cluster. Généralement, un plus grand nombre de domaines d’erreur et de mise à niveau signifie que vous pouvez choisir une capacité de mémoire tampon inférieure, dans la mesure où vous pouvez vous attendre à ce qu’une plus petite portion de votre cluster soit indisponible lors des défaillances et des mises à niveau. Notez que l’indication du pourcentage de mémoire tampon est pertinente uniquement si vous avez également spécifié la capacité du nœud pour une métrique.

Voici un exemple montrant comment spécifier la capacité de mise en mémoire tampon :

ClusterManifest.xml

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="DiskSpace" Value="0.10" />
            <Parameter Name="Memory" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

La création de nouveaux services échoue lorsque le cluster est hors de la capacité de mise en mémoire tampon, garantissant ainsi que le cluster conserve suffisamment d’espace de secours pour que les défaillances et les mises à niveau n’entraînent pas un dépassement de la capacité par les nœuds. Le gestionnaire de ressources de cluster expose de nombreuses informations via PowerShell et l’API de requêtes, ce qui vous permet de voir les paramètres de capacité de mise en tampon, la capacité totale et la consommation actuellement pour chaque mesure du cluster. Voici un exemple de cette sortie :

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

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur l’architecture et le flux d’informations dans Cluster Resource Manager, consultez [cet article ](service-fabric-cluster-resource-manager-architecture.md)
* La définition des mesures de défragmentation est une façon de consolider la charge sur les nœuds au lieu de la répartir. Pour savoir comment configurer la défragmentation, reportez-vous à [cet article](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
* Commencez au début pour [obtenir une présentation de Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
* Pour en savoir plus sur la façon dont Cluster Resource Manager gère et équilibre la charge du cluster, consultez l’article sur [l’équilibrage de la charge](service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png



<!--HONumber=Dec16_HO2-->


