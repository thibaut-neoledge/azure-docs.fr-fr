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
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: a595a2e24b4c2582998d88f74ca13daba5fe618d

---

# <a name="describing-a-service-fabric-cluster"></a>Description d’un cluster Service Fabric
Le Gestionnaire de ressources de cluster Service Fabric fournit plusieurs mécanismes permettant de décrire un cluster. Pendant l’exécution, Cluster Resource Manager utilise ces informations pour garantir une haute disponibilité des services en cours d’exécution dans le cluster. Tout en appliquant ces règles importantes, il essaye d’optimiser la consommation des ressources du cluster.

## <a name="key-concepts"></a>Concepts clés
Le Gestionnaire de ressources de cluster prend en charge plusieurs fonctionnalités qui décrivent un cluster :

* Domaines d'erreur
* Domaines de mise à niveau
* Propriétés du nœud
* Capacités du nœud

## <a name="fault-domains"></a>Domaines d'erreur
Un domaine d’erreur est une zone d’échec coordonné. Une machine unique constitue un domaine d’erreur (puisqu’elle peut cesser de fonctionner de manière indépendante pour de nombreuses raisons : coupure électrique, défaillance de disque ou erreur de microprogramme de carte d’interface réseau). Les machines connectées au même commutateur Ethernet se trouvent dans le même domaine d’erreur, tout comme les machines partageant une source d’alimentation unique. Dans la mesure où le chevauchement des défaillances matérielles est naturel, les domaines d’erreur sont hiérarchiques par nature et sont représentés en tant qu’URI dans Service Fabric.

Lorsque vous configurez votre propre cluster, vous devez réfléchir à ces différentes zones d’échec. Il est important que les domaines d’erreur soient configurés correctement, car Service Fabric utilise ces informations pour placer des services en toute sécurité. Service Fabric ne souhaite pas placer des services si la perte d’un domaine d’erreur (provoquée par la défaillance d’un composant) risque d’entraîner leur défaillance. Dans l’environnement Azure, Service Fabric utilise les informations de domaine d’erreur fournies par l’environnement pour configurer correctement les nœuds du cluster en votre nom.

Dans le graphique ci-dessous, nous avons indiqué en couleur toutes les entités qui contribuent aux domaines d’erreur et nous avons répertorié tous les domaines d’erreur différents qui en résultent. Dans cet exemple, nous avons des centres de données (« DC »), des racks (« R ») et des panneaux (« B »). En théorie, si chaque panneau contient plusieurs machines virtuelles, il peut exister une autre couche dans la hiérarchie de domaine d’erreur.

<center>
![Nœuds organisés par domaines d’erreur][Image1]
</center>

Lors de l’exécution, Service Fabric Cluster Resource Manager prend en compte les domaines d’erreur dans le cluster et planifie une disposition.  Il tente de répartir les réplicas avec état ou les instances sans état pour un service donné afin qu’ils se trouvent dans des domaines d’erreur distincts. Ce processus permet de s’assurer qu’en cas d’échec d’un domaine d’erreur (quel que soit le niveau de la hiérarchie), la disponibilité de ce service n’est pas compromise.

Service Fabric Cluster Resource Manager ne se soucie pas du nombre de couches dans la hiérarchie du domaine d’erreur. Toutefois, il tente de s’assurer que la perte d’une partie de la hiérarchie n’influe pas sur les services qui s’y exécutent. Pour cette raison, il est préférable d’avoir le même nombre de nœuds à chaque niveau de profondeur dans la hiérarchie du domaine d’erreur. Avoir des niveaux équilibrés permet de s’assurer qu’une partie de la hiérarchie ne contient pas plus de services que d’autres. Le fait d’agir autrement pourrait contribuer à des déséquilibres dans la charge des nœuds individuels et rendre l’échec de certains domaines plus critique que d’autres.

Si « l’arborescence » des domaines d’erreur est déséquilibrée dans votre cluster, il est plus difficile pour Cluster Resource Manager de trouver la répartition optimale des services. Des dispositions déséquilibrées de domaines d’erreur signifient que la perte d’un domaine particulier peut avoir plus d’impact sur la disponibilité du cluster. Par conséquent, Cluster Resource Manager est partagé entre ses deux objectifs : il devait utiliser les machines dans ce domaine « lourd » en y plaçant des services, et il souhaite placer des services de telle sorte que la perte d’un domaine n’entraîne aucun problème. À quoi cela ressemble-t-il ?

<center>
![Deux dispositions de cluster différentes][Image2]
</center>

Le diagramme ci-dessus illustre deux exemples de dispositions de cluster. Dans le premier exemple, les nœuds sont répartis uniformément entre les domaines d’erreur. Dans l’autre exemple, un domaine d’erreur a beaucoup plus de nœuds. Si vous créez votre propre cluster sur site ou dans un autre environnement, c’est une chose à laquelle vous devez penser.

Dans Azure, le fait de choisir quel domaine d’erreur contient un nœud est géré pour vous. Toutefois, en fonction du nombre de nœuds que vous configurez, vous pouvez malgré tout vous retrouver avec des domaines d’erreur contenant plus de nœuds que d’autres. Supposons, par exemple, que vous avez cinq domaines d’erreur, mais que vous configurez sept nœuds pour un type NodeType donné. Dans ce cas, davantage de nœuds sont alloués aux deux premiers domaines d’erreur. Si vous continuez à déployer plus de NodeTypes avec seulement deux instances, le problème s’aggrave.

## <a name="upgrade-domains"></a>Domaines de mise à niveau
Les domaines de mise à niveau correspondent à une autre fonctionnalité qui permet à Service Fabric Cluster Resource Manager de comprendre la disposition du cluster afin de planifier les échecs à l’avance. Les domaines de mise à niveau définissent des ensembles de nœuds qui sont mis à niveau en même temps.

Les domaines de mise à niveau sont très semblables aux domaines d’erreur, avec cependant quelques différences clés. Tout d’abord, tandis que les domaines d’erreur sont rigoureusement définis selon des zones de défaillance matérielles coordonnées, les domaines de mise à niveau sont définis par la stratégie. Vous pouvez décider du nombre de domaines de mise à niveau que vous voulez, au lieu que le chiffre ne soit dicté par l’environnement. Une autre différence est que les domaines de mise à niveau (en tout cas, à l’heure actuelle) ne sont pas hiérarchiques. Ils ressemblent davantage à une simple balise.

Le diagramme suivant illustre trois domaines de mise à niveau répartis sur trois domaines d’erreur. Il montre également un emplacement possible pour trois réplicas différents d’un service avec état, où chaque réplica est attribué à des domaines d’erreur et de mise à niveau différents. Ce positionnement nous permet de perdre un domaine d’erreur au cours de la mise à niveau d’un service tout en conservant une copie du code et des données.

<center>
![Positionnement avec des domaines d’erreur et de mise à niveau][Image3]
</center>

Il existe des avantages et des inconvénients au fait de disposer de nombreux domaines de mise à niveau. Avec davantage de domaines de mise à niveau, chaque étape de la mise à niveau est plus granulaire et affecte donc un plus petit nombre de nœuds ou de services. Il en résulte que moins de services doivent être déplacés simultanément, et donc une attrition moins importante dans le système. Cela a également tendance à améliorer la fiabilité (car une partie moins importante du service est affectée en cas de problème lors de la mise à niveau). Davantage de domaines de mise à niveau signifie également que vous avez besoin d’une surcharge disponible moins importante sur d’autres nœuds pour gérer l’impact de la mise à niveau. Par exemple, si vous avez cinq domaines de mise à niveau, les nœuds présents dans chacun gèrent environ 20 % du trafic. Si vous avez besoin d’arrêter un domaine de mise à niveau pour effectuer une mise à niveau, cette charge doit être dirigée autre part. Avoir davantage de domaines de mise à niveau signifie qu’une surcharge moins importante doit être conservée sur les autres nœuds du cluster.

L’inconvénient dans le fait d’avoir un grand nombre de domaines de mise à niveau est que les mises à niveau ont tendance à prendre plus de temps. Avant de continuer, Service Fabric patiente pendant une courte période après qu’un domaine de mise à niveau soit terminé. Ce délai permet d’afficher et de détecter les problèmes introduits par la mise à niveau. Le compromis est acceptable, car il empêche les modifications incorrectes d’affecter une trop grande partie du service à la fois.

Une quantité trop faible de domaines de mise à niveau a également des effets secondaires : lorsque l’un des domaines de mise à niveau est arrêté et en cours de mise à niveau, une grande partie de votre capacité globale n’est pas disponible. Par exemple, si vous avez seulement trois domaines de mise à niveau, vous vous défaites d’environ 1/3 de votre service global ou de votre capacité de cluster à la fois. Le fait qu’une grande partie de votre service soit arrêtée simultanément n’est pas souhaitable car la capacité doit être suffisante dans le reste de votre cluster pour gérer la charge de travail. Le maintien de cet équilibre signifie que dans le cas normal, ces nœuds sont moins chargés qu’ils ne le seraient normalement, ce qui accroît le coût de l’exécution de votre service.

Il n’existe aucune limite réelle au nombre total de domaines d’erreur ou de mise à niveau dans un environnement, ni de contraintes sur la façon dont ils se chevauchent. Les structures les plus fréquemment rencontrées sont les suivantes :

* Correspondance parfaite des domaines d’erreur et des domaines de mise à niveau
* Un domaine de mise à niveau par nœud (instance de système d’exploitation physique ou virtuel)
* Un modèle « agrégé par bandes » ou de « matrice » dans lequel les domaines d’erreur et les domaines de mise à niveau forment une matrice où les machines s’exécutent généralement en suivant la matrice diagonale

<center>
![Dispositions de domaines d’erreur et de mise à niveau][Image4]
</center>

Il n’existe pas de disposition idéale, chacune ayant des avantages et des inconvénients. Par exemple, le modèle 1 domaine d’erreur:1 domaine de mise à niveau est relativement simple à configurer. Le modèle avec un domaine de mise à niveau par nœud correspond davantage à ce à quoi les utilisateurs étaient habitués par le passé pour la gestion de petits ensembles de machines, où chacune pouvait être arrêtée de manière indépendante.

Le modèle le plus courant (et celui utilisé dans Azure) est la matrice domaine d’erreur/domaine de mise à niveau, où les domaines d’erreur et les domaines de mise à niveau forment une table et où les nœuds sont placés le long de la diagonale. En fonction du nombre total de nœuds par rapport au nombre de domaines d’erreur et de mise à niveau, les nœuds seront partiellement alloués ou compressés. Autrement dit, si le cluster est suffisamment grand, presque tout finit par se présenter comme le modèle de matrice dense, représenté dans l’option en bas à droite de l’image ci-dessus.

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>Contraintes des domaines d’erreur et de mise à niveau, et comportement résultant
Cluster Resource Manager traite le souhait de conserver un service équilibré entre les domaines d’erreur et de mise à niveau en tant que contrainte. Vous trouverez plus d’informations sur les contraintes dans [cet article](service-fabric-cluster-resource-manager-management-integration.md). Les contraintes des domaines d’erreur et de mise à niveau indiquent : « pour une partition de service donnée, il ne doit jamais y avoir une différence *supérieure à un* dans le nombre d’objets de service (instances de service sans état ou réplicas de service avec état) entre deux domaines. »  Concrètement, cela signifie que pour un service donné, certains mouvements ou arrangements peuvent ne pas être valides, car cela enfreindrait la contrainte du domaine de mise à niveau ou d’erreur.

Examinons un exemple. Supposons que nous avons un cluster avec six nœuds, configuré avec cinq domaines d’erreur et cinq de mise à niveau.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

Supposons maintenant que nous créons un service avec TargetReplicaSetSize défini sur cinq. Les réplicas se trouvent sur N1-N5. En fait, N6 ne sera jamais utilisé, quel que soit le nombre de services que vous créez. Mais pourquoi ? Examinons la différence entre la disposition actuelle et ce qui se passerait si N6 était choisi.

Voici la disposition que nous obtenons et le nombre total de réplicas par domaine d’erreur et de mise à niveau :

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Cette disposition est équilibrée en termes de nœuds par domaine d’erreur et domaine de mise à niveau. Elle est également équilibrée en termes de nombre de réplicas par domaine d’erreur et domaine de mise à niveau. Chaque domaine possède le même nombre de nœuds et le même nombre de réplicas.

À présent, jetons un œil à ce qui se passerait si au lieu de N2, nous avions utilisé N6. Comment les réplicas auraient-ils été réparties ?

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

Vous remarquez quelque chose ? Cette disposition ne respecte pas notre définition de la contrainte du domaine d’erreur. FD0 a deux réplicas, alors que FD1 en a zéro. La différence entre FD0 et FD1 est donc égale à deux. Cluster Resource Manager n’autorise pas cette organisation. De même, si nous avions choisi N2 et N6 (au lieu de N1 et N2) nous aurions obtenu :

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Bien que cette disposition soit équilibrée en matière de domaines d’erreur, elle enfreint la contrainte du domaine de mise à niveau (car UD0 a zéro réplica contre deux pour UD1). Cette disposition est également non valide

## <a name="configuring-fault-and-upgrade-domains"></a>Configuration des domaines d’erreur et de mise à niveau
La définition des domaines d’erreur et de mise à niveau s’effectue automatiquement dans les déploiements Service Fabric hébergés sur Azure. Service Fabric récupère et utilise les informations d’environnement d’Azure.

Si vous créez votre propre cluster (ou si vous voulez exécuter une topologie donnée en développement), vous fournissez les informations relatives au domaine d’erreur et de mise à niveau vous-même. Dans cet exemple, nous définissons un cluster de développement local à neuf nœuds qui s’étend sur trois « centres de données » (chacun avec trois racks). Ce cluster a également trois domaines de mise à niveau répartis sur ces trois centres de données. Dans le modèle de manifeste de cluster figure quelque chose comme ceci :

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

via ClusterConfig.json pour les déploiements autonomes

```json
"nodes": [
  {
    "nodeName": "vm1",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm2",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm3",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm4",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm5",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm6",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm7",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm8",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm9",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD3"
  }
],
```

> [!NOTE]
> Dans les déploiements Azure, les domaines d'erreur et de mise à niveau sont affectés par Azure. Par conséquent, la définition des nœuds et des rôles au sein de l’option Infrastructure pour Azure n’inclut pas les informations sur les domaines d’erreur ou de mise à niveau.
>
>

## <a name="placement-constraints-and-node-properties"></a>Contraintes de positionnement et propriétés de nœud
Parfois (en réalité, la plupart du temps), vous voudrez vous assurer que certaines charges de travail s’exécutent uniquement sur certains nœuds ou certains ensembles de nœuds dans le cluster. Par exemple, certaines charges de travail peuvent nécessiter des GPU ou des SSD, tandis que d’autres n’en ont pas besoin. Presque toutes les architectures multiniveau sont un bon exemple de ciblage du matériel sur des charges de travail spécifiques. Dans ces architectures, certaines machines servent de système frontal/d’interface desservant une partie de l’application (et sont donc probablement exposées à Internet). D’autres ensembles de machines (souvent avec des ressources matérielles différentes) gèrent le travail des couches de calcul ou de stockage (et ne sont généralement pas exposés à internet). Service Fabric s’attend à ce que, même dans un monde dominé par les microservices, il y ait des cas où des charges de travail particulières doivent s’exécuter sur des configurations matérielles données, par exemple :

* une application multiniveau existante a été « augmentée et déplacée » dans un environnement Service Fabric
* une charge de travail veut s’exécuter sur un matériel spécifique pour des raisons d’isolation de sécurité, de performance ou de mise à l’échelle
* une charge de travail doit être isolée des autres charges de travail pour des raisons de stratégie ou de consommation de ressources

Pour prendre en charge ces types de configurations, Service Fabric dispose d’une notion de premier ordre des balises qui peuvent être appliquées aux nœuds. Il s’agit des contraintes de placement. Les contraintes de placement peuvent être utilisées pour indiquer où certains services doivent s’exécuter. L’ensemble de contraintes est extensible ; n’importe quelle paire clé/valeur peut fonctionner.

<center>
![Disposition du cluster avec différentes charges de travail][Image5]
</center>

Les différentes balises clé/valeur sur les nœuds sont appelées *propriétés* de placement (ou simplement propriétés du nœud). La valeur spécifiée dans la propriété de nœud peut être une chaîne, une valeur booléenne ou une valeur signée longue. L’instruction au niveau du service est appelée une *contrainte* de placement, car elle contraint l’exécution du service à un emplacement spécifique dans le cluster. La contrainte peut être toute déclaration booléenne qui opère sur les différentes propriétés de nœud du cluster. Les sélecteurs valides dans ces déclarations booléennes sont :

1) des vérifications conditionnelles pour la création d’instructions particulières

| Instruction | Syntaxe |
| --- |:---:|
| "égal à" | "==" |
| "non égal à" | "!=" |
| "supérieur à" | ">" |
| "supérieur ou égal à" | ">=" |
| "inférieur à" | "<" |
| "inférieur ou égal à" | "<=" |

2) instructions booléennes pour les opérations de groupage et logiques

| Instruction | Syntaxe |
| --- |:---:|
| "et" | "&&" |
| "ou" | "&#124;&#124;" |
| "non" | "!" |
| "regrouper en tant qu’instruction unique" | "()" |

Voici quelques exemples d’instructions de contrainte de base.

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

Seuls les nœuds où la déclaration globale prend la valeur « True » peuvent avoir le service placé dessus. Les nœuds sans une propriété définie ne correspondent à aucune contrainte de placement contenant cette propriété.

Service Fabric définit certaines propriétés de nœud par défaut qui peuvent être utilisées automatiquement sans que l’utilisateur ait à les définir. À ce jour, les propriétés par défaut définies sur chaque nœud sont **NodeType** et **NodeName**. Par exemple, vous pouvez écrire une contrainte de placement ainsi : `"(NodeType == NodeType03)"`. En règle générale, NodeType est l’une des propriétés les plus couramment utilisées. Elle est utile car elle correspond parfaitement à un type de machine, qui à son tour correspond à un type de charge de travail dans une architecture d’application multiniveau classique.

<center>
![Contraintes de placement et propriétés de nœud][Image6]
</center>

Supposons que les propriétés de nœud suivantes aient été définies pour un type de nœud donné :

ClusterManifest.xml

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

via ClusterConfig.json pour les déploiements autonomes ou Template.json pour les clusters hébergés sur Azure. Dans votre modèle Azure Resource Manager pour un cluster, les éléments tels que le nom de type de nœud sont probablement paramétrables et ressemblent à « [parameters('vmNodeType1Name')] » au lieu de « NodeType01 ».

```json
"nodeTypes": [
    {
        "name": "NodeType01",
        "placementProperties": {
            "HasSSD": "true",
            "NodeColor": "green",
            "SomeProperty": "5"
        },
    }
],
```

Vous pouvez créer des *contraintes* de placement de service pour un service comme suit :

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

Si vous êtes sûr que tous les nœuds NodeType01 sont valides, vous pouvez également sélectionner ce type de nœud.

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

Les contraintes de placement (ainsi que de nombreuses autres commandes Orchestrator dont nous allons parler) sont spécifiées pour chaque instance de service individuelle nommée. Les mises à jour prennent toujours la place de (remplacent) ce qui a été précédemment spécifié.

Les propriétés d’un nœud sont définies via la définition du cluster et, par conséquent, ne peuvent pas être mises à jour sans une mise à niveau du cluster. La mise à niveau des propriétés d’un nœud nécessite que chaque nœud soit arrêté, puis redémarré.

## <a name="capacity"></a>Capacité
L’une des principales tâches de n’importe quel orchestrateur consiste à vous aider à gérer la consommation de ressources du cluster. La dernière chose que vous souhaitez si vous tentez d’exécuter efficacement des services est qu’un ensemble de nœuds soit à chaud, tandis que d’autres sont à froid. Les nœuds à chaud entraînent des conflits de ressources et des performances médiocres. Les nœuds à froid représentent un gaspillage des ressources et augmentent le coût. Avant de parler de l’équilibrage, comment s’assurer en premier lieu que les ressources sur les nœuds ne s’épuisent pas ?

Service Fabric représente les ressources en tant que `Metrics`. Les métriques correspondent à n’importe quelle ressource logique ou physique que vous souhaitez décrire pour Service Fabric. Par exemple, « WorkQueueDepth » et « MemoryInMb » sont des métriques. Pour plus d’informations sur la configuration des mesures et leur utilisation, consultez [cet article](service-fabric-cluster-resource-manager-metrics.md)

Les mesures sont différentes des contraintes de placement et des propriétés de nœud. Les propriétés de nœud sont des descripteurs statiques des nœuds eux-mêmes, alors que les mesures sont des ressources que les nœuds ont et que les services consomment lors de leur exécution sur un nœud. La propriété d’un nœud peut être équivalente à « HasSSD » et définie sur true ou false. La quantité d’espace disponible sur ce disque SSD (et consommé par les services) est une mesure telle que « DriveSpaceInMb ». Le nœud définit sa capacité « DriveSpaceInMb » sur la quantité d’espace total non réservé sur le disque. Les services indiquent la quantité de la mesure qu’ils ont utilisé lors de l’exécution.

Il est important de noter que, comme pour les contraintes de placement et les propriétés de nœud, Service Fabric Cluster Resource Manager ne comprend pas ce que signifient les noms des mesures. Les noms des mesures ne sont que des chaînes. Il est recommandé de déclarer les unités dans le cadre des noms des mesures que vous créez lorsque cela peut être ambigu.

Si vous avez désactivé toutes les fonctions *d’équilibrage* des ressources, Cluster Resource Manager de Service Fabric est tout de même en mesure de garantir qu’aucun nœud ne dépasse sa capacité. En règle générale, cela est possible, sauf si le cluster dans son ensemble est trop plein. La capacité est une autre *contrainte* que Cluster Resource Manager utilise pour comprendre la quantité d’une ressource dont un nœud dispose. La capacité restante est également suivie pour le cluster dans son ensemble. La capacité et la consommation au niveau du service sont toutes deux exprimées en termes de métriques. Par exemple, la mesure peut être « MemoryInMb » et un nœud donné peut avoir une capacité de 2048 pour « MemoryInMb ». Un service en cours d’exécution sur ce nœud peut dire qu’il utilise actuellement une quantité de 64 de « MemoryInMb ».

Lors de l’exécution, Cluster Resource Manager suit la quantité de chaque ressource présente sur chaque nœud et la quantité restante. Il effectue cela en soustrayant de la capacité du nœud toute utilisation déclarée par chaque service en cours d’exécution sur ce nœud. Grâce à ces informations, Cluster Resource Manager de Service Fabric peut déterminer où placer ou déplacer les réplicas afin que les nœuds ne dépassent pas la capacité.

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
<center>
![Nœuds de cluster et capacité][Image7]
</center>

Vous pouvez voir les capacités définies dans le manifeste de cluster :

ClusterManifest.xml

```xml
    <NodeType Name="NodeType02">
      <Capacities>
        <Capacity Name="MemoryInMb" Value="2048"/>
        <Capacity Name="DiskInMb" Value="512000"/>
      </Capacities>
    </NodeType>
```

via ClusterConfig.json pour les déploiements autonomes ou Template.json pour les clusters hébergés sur Azure. Dans votre modèle Azure Resource Manager pour un cluster, les éléments tels que le nom de type de nœud sont probablement paramétrables et ressemblent à « [parameters('vmNodeType2Name')] » au lieu de « NodeType02 ».

```json
"nodeTypes": [
    {
        "name": "NodeType02",
        "capacities": {
            "MemoryInMb": "2048",
            "DiskInMb": "512000"
        }
    }
],
```

Il est également possible (et même courant) que la charge d’un service évolue de manière dynamique. Supposons que la charge d’un réplica est passée de 64 à 1024, mais que le nœud d’exécution n’en avait que 512 (de la mesure « MemoryInMb ») restants à ce moment. À présent, le placement de ce réplica ou de cette instance n’est pas valide, car il n’y a pas suffisamment d’espace sur ce nœud. Cela peut également se produire si l’utilisation combinée des réplicas et des instances sur ce nœud dépasse les capacités de celui-ci. Dans les deux cas, Cluster Resource Manager doit entrer en action et rétablir une capacité inférieure sur le nœud. Pour ce faire, il déplace un(e) ou plusieurs instances ou réplicas de ce nœud vers des nœuds différents. Lors du déplacement des réplicas, Cluster Resource Manager essaie de réduire le coût de ces mouvements. Le coût du mouvement est abordé dans [cet article](service-fabric-cluster-resource-manager-movement-cost.md).

## <a name="cluster-capacity"></a>Capacité de cluster
Par conséquent, comment faire en sorte que l’ensemble du cluster ne soit pas saturé ? Eh bien, avec une charge dynamique, Cluster Resource Manager ne peut pas faire grand chose. La charge des services peut augmenter indépendamment des actions entreprises par Cluster Resource Manager. Par conséquent, votre cluster qui dispose de suffisamment d’espace aujourd’hui pourrait ne pas être suffisamment puissant demain une fois que vous serez devenu célèbre. Cela dit, il existe des commandes intégrées pour éviter les problèmes de base. La première chose que nous pouvons faire est d’empêcher la création de nouvelles charges de travail qui risquent de saturer le cluster.

Supposons que vous créez un service sans état et qu’une charge y est associée (nous reviendrons plus tard sur les rapports de charge par défaut et dynamique). Supposons que le service respecte la mesure « DiskSpaceInMb ». Supposons également qu’il va utiliser cinq unités de « DiskSpaceInMb » pour chaque instance du service. Vous souhaitez créer trois instances du service. Parfait ! Cela signifie que nous avons besoin de 15 unités « DiskSpaceInMb » dans le cluster pour être en mesure de créer ces instances de service. Cluster Resource Manager calcule en permanence la capacité globale et la consommation de chaque mesure, afin de déterminer facilement si l’espace est suffisant dans le cluster. Si l’espace n’est pas suffisant, Cluster Resource Manager rejette l’appel du service de création.

Étant donné que l’exigence est seulement de 15 unités disponibles, cet espace peut être affecté de différentes façons. Par exemple, il peut y avoir une unité restante de capacité sur 15 nœuds différents ou trois unités restantes de capacité sur cinq nœuds différents. Tant que Cluster Resource Manager peut réorganiser les choses de manière à avoir cinq unités disponibles sur trois nœuds, il placera le service. Cette réorganisation est presque toujours possible à moins que le cluster dans son ensemble ne soit presque entièrement complet, que les services soient tous très « lourds », ou les deux.

## <a name="buffered-capacity"></a>Capacité de mise en mémoire tampon
Pour aider à gérer la capacité globale du cluster, Cluster Resource Manager dispose d’une fonctionnalité qui permet d’ajouter un système de mémoire tampon réservée à la capacité spécifiée sur chaque nœud. La capacité de mise en mémoire tampon permet de réserver une partie de la capacité globale du nœud qui n’est utilisée que pour placer des services au cours de mises à niveau et en cas de défaillance des nœuds. Aujourd’hui, la mémoire tampon est indiquée de manière globale par métrique pour tous les nœuds via la définition du cluster. La valeur que vous choisissez pour la capacité réservée est relative au nombre de domaines d’erreur et de mise à niveau dont vous disposez dans le cluster et de la quantité de surcharge souhaitée. Un nombre de domaines d’erreur et de mise à niveau plus important signifie que vous pouvez choisir un nombre inférieur pour la capacité de mise en mémoire tampon. Si vous avez davantage de domaines, une portion moins importante de votre cluster sera indisponible lors de mises à niveau et en cas de défaillance. L’indication du pourcentage de mémoire tampon est pertinente uniquement si vous avez également spécifié la capacité du nœud pour une mesure.

Voici un exemple montrant comment spécifier la capacité de mise en mémoire tampon :

ClusterManifest.xml

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="DiskSpace" Value="0.10" />
            <Parameter Name="Memory" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

via ClusterConfig.json pour les déploiements autonomes ou Template.json pour les clusters hébergés sur Azure :

```json
"fabricSettings": [
  {
    "name": "NodeBufferPercentage",
    "parameters": [
      {
          "name": "DiskSpace",
          "value": "0.10"
      },
      {
          "name": "Memory",
          "value": "0.15"
      },
      {
          "name": "SomeOtherMetric",
          "value": "0.20"
      }
    ]
  }
]
```

La création de nouveaux services échoue lorsque la capacité de mise en mémoire tampon du cluster est épuisée pour une mesure. Cela permet de s’assurer que le cluster conserve suffisamment d’espace de secours pour que les défaillances et les mises à niveau n’entraînent pas un dépassement de la capacité par les nœuds. La capacité de mise en mémoire tampon est facultative mais recommandée dans tout cluster qui définit une capacité pour une mesure.

Cluster Resource Manager affiche ces informations via PowerShell et les API de requête. Cela vous permet de voir les paramètres de la capacité de mise en mémoire tampon, la capacité totale et la consommation actuelle pour chaque mesure utilisée dans le cluster. Voici un exemple de cette sortie :

```posh
PS C:\Users\user> Get-ServiceFabricClusterLoadInformation
LastBalancingStartTimeUtc : 9/1/2016 12:54:59 AM
LastBalancingEndTimeUtc   : 9/1/2016 12:54:59 AM
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



<!--HONumber=Jan17_HO1-->


