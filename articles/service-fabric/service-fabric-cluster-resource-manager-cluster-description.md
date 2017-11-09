---
title: "Description d’un cluster Cluster Resource Manager | Microsoft Docs"
description: "Description d’un cluster Service Fabric en spécifiant des domaines d’erreur, des domaines de mise à niveau, les propriétés des nœuds et les capacités des nœuds pour Cluster Resource Manager."
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
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 26ce9e96dd4df170e80c2c61dcc08c70357eec22
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/27/2017
---
# <a name="describing-a-service-fabric-cluster"></a>Description d’un cluster Service Fabric
Le Gestionnaire de ressources de cluster Service Fabric fournit plusieurs mécanismes permettant de décrire un cluster. Pendant l’exécution, Cluster Resource Manager utilise ces informations pour garantir une haute disponibilité des services en cours d’exécution dans le cluster. Tout en appliquant ces règles importantes, il essaie aussi d’optimiser la consommation de ressources au sein du cluster.

## <a name="key-concepts"></a>Concepts clés
Le Gestionnaire de ressources de cluster prend en charge plusieurs fonctionnalités qui décrivent un cluster :

* Domaines d'erreur
* Domaines de mise à niveau
* Propriétés du nœud
* Capacités du nœud

## <a name="fault-domains"></a>Domaines d'erreur
Un domaine d’erreur est une zone d’échec coordonné. Une machine unique constitue un domaine d’erreur (puisqu’elle peut cesser de fonctionner de manière indépendante pour de nombreuses raisons : coupure électrique, défaillance de disque ou erreur de microprogramme de carte d’interface réseau). Les machines connectées à un même commutateur Ethernet se trouvent dans le même domaine d’erreur, tout comme les machines qui partagent une même source d’alimentation ou qui se trouvent à un même emplacement. Dans la mesure où le chevauchement des défaillances matérielles est naturel, les domaines d’erreur sont hiérarchiques par nature et sont représentés en tant qu’URI dans Service Fabric.

Il est important que les domaines d’erreur soient configurés correctement, car Service Fabric utilise ces informations pour placer des services en toute sécurité. Service Fabric ne souhaite pas placer des services si la perte d’un domaine d’erreur (provoquée par la défaillance d’un composant) doit entraîner l’arrêt d’un service. Dans l’environnement Azure, Service Fabric utilise les informations de domaine d’erreur fournies par l’environnement pour configurer correctement les nœuds du cluster en votre nom. Pour Service Fabric autonome, les domaines d’erreur sont définis au moment où le cluster est configuré. 

> [!WARNING]
> Il est important que les informations de domaine d’erreur fournies à Service Fabric soient précises. Par exemple, supposons que les nœuds de votre cluster Service Fabric s’exécutent à l’intérieur de 10 machines virtuelles, s’exécutant sur cinq hôtes physiques. Dans ce cas, même s’il y a 10 ordinateurs virtuels, il y a seulement 5 domaines d’erreur (de niveau supérieur) différents. Le partage d’un même hôte physique amène les machines virtuelles à partager le même domaine d’erreur racine, car les machines virtuelles subissent une défaillance coordonnée si leur hôte physique subit une défaillance.  
>
> Service Fabric s’attend à ce que le domaine d’erreur d’un nœud ne change pas. Les autres mécanismes visant à assurer une haute disponibilité des machines virtuelles, tels que [HA-VM](https://technet.microsoft.com/en-us/library/cc967323.aspx), peuvent entraîner des conflits avec Service Fabric car ils utilisent une migration transparente des machines virtuelles d’un hôte vers un autre. Ces mécanismes ne reconfigurent ni ne notifient le code s’exécutant dans la machine virtuelle. À ce titre, ils ne sont **pas prises en charge** en tant qu’environnements d’exécution de clusters Service Fabric. Service Fabric doit être la seule technologie de haute disponibilité employée. La migration dynamique de machines virtuelles, les SAN et autres mécanismes ne sont pas nécessaires. S’ils sont utilisés avec Service Fabric, ces mécanismes _réduisent_ la disponibilité et la fiabilité des applications, car ils introduisent une complexité supplémentaire, ajoutent des sources centralisées de défaillance et utilisent des stratégies de fiabilité et de disponibilité qui sont en conflit avec celles de Service Fabric. 
>
>

Dans le graphique ci-dessous, nous avons indiqué en couleur toutes les entités qui contribuent aux domaines d’erreur et nous avons répertorié tous les domaines d’erreur différents qui en résultent. Dans cet exemple, nous avons des centres de données (« DC »), des racks (« R ») et des panneaux (« B »). En théorie, si chaque panneau contient plusieurs machines virtuelles, il peut exister une autre couche dans la hiérarchie de domaine d’erreur.

<center>
![Nœuds organisés par domaines d’erreur][Image1]
</center>

Pendant l’exécution, Service Fabric Cluster Resource Manager prend en compte les domaines d’erreur du cluster et planifie des dispositions. Les réplicas avec état ou les instances sans état d’un service donné sont répartis de sorte qu’ils se trouvent dans des domaines d’erreur distincts. La répartition du service entre les domaines d’erreur est l’assurance que la disponibilité du service n’est pas mise à mal quand un domaine d’erreur connaît une défaillance à n’importe quel niveau de la hiérarchie.

Service Fabric Cluster Resource Manager ne se soucie pas du nombre de couches dans la hiérarchie du domaine d’erreur. Cependant, il essaie de faire en sorte que la perte d’une partie de la hiérarchie n’influe pas sur les services qui s’y exécutent. 

Il est préférable d’avoir le même nombre de nœuds à chaque niveau de profondeur dans la hiérarchie du domaine d’erreur. Si « l’arborescence » des domaines d’erreur est déséquilibrée dans votre cluster, il est plus difficile pour Cluster Resource Manager de déterminer la meilleure allocation de services. Des dispositions déséquilibrées de domaines d’erreur signifient que la perte de quelques domaines a un impact plus important sur la disponibilité des services que d’autres domaines. Par conséquent, Cluster Resource Manager est partagé entre deux objectifs : utiliser les machines dans ce domaine « chargé » en y plaçant des services et placer des services dans d’autres domaines pour éviter que la perte d’un domaine n’entraîne des problèmes. 

À quoi ressemblent des domaines déséquilibrés ? Le schéma ci-dessous montre deux dispositions de cluster différentes. Dans le premier exemple, les nœuds sont répartis uniformément entre les domaines d’erreur. Dans le deuxième exemple, un domaine d’erreur contient beaucoup plus de nœuds que les autres domaines d’erreur. 

<center>
![Deux dispositions de cluster différentes][Image2]
</center>

Dans Azure, le choix du domaine d’erreur qui contient un nœud est géré automatiquement. Toutefois, en fonction du nombre de nœuds que vous configurez, vous pouvez malgré tout vous retrouver avec des domaines d’erreur contenant plus de nœuds que d’autres. Supposons, par exemple, que vous avez cinq domaines d’erreur dans le cluster, mais que vous approvisionnez sept nœuds pour un NodeType donné. Dans ce cas, les deux premiers domaines d’erreur se retrouvent avec davantage de nœuds. Si vous continuez à déployer plus de NodeTypes avec seulement deux instances, le problème s’aggrave. C’est pourquoi, le nombre de nœuds dans chaque type de nœud doit de préférence être un multiple du nombre de domaines d’erreur.

## <a name="upgrade-domains"></a>Domaines de mise à niveau
Les domaines de mise à niveau correspondent à une autre fonctionnalité qui permet à Service Fabric Cluster Resource Manager de comprendre la disposition du cluster. Les domaines de mise à niveau définissent des ensembles de nœuds qui sont mis à niveau en même temps. Les domaines de mise à niveau aident Cluster Resource Manager à comprendre et à orchestrer les opérations de gestion telles que les mises à niveau.

Les domaines de mise à niveau sont très semblables aux domaines d’erreur, avec cependant quelques différences clés. Tout d’abord, les zones de défaillances matérielles coordonnées définissent les domaines d’erreur. D’un autre côté, les domaines de mise à niveau sont définis par une stratégie. Vous pouvez décider du nombre que vous en voulez au lieu que ce chiffre soit dicté par l’environnement. Vous pouvez disposer d’autant de domaines mise à niveau que de nœuds. Une autre différence entre les domaines d’erreur et les domaines de mise à niveau est que les domaines de mise à niveau ne sont pas hiérarchiques. En effet, ils s’apparentent plus à une balise simple. 

Le diagramme suivant illustre trois domaines de mise à niveau répartis sur trois domaines d’erreur. Il montre également un emplacement possible pour trois réplicas différents d’un service avec état, où chaque réplica est attribué à des domaines d’erreur et de mise à niveau différents. Ce positionnement autorise la perte d’un domaine d’erreur au cours de la mise à niveau d’un service tout en conservant une copie du code et des données.  

<center>
![Positionnement avec des domaines d’erreur et de mise à niveau][Image3]
</center>

Il existe des avantages et des inconvénients au fait de disposer de nombreux domaines de mise à niveau. Davantage de domaines de mise à niveau signifie que chaque étape de la mise à niveau est plus précise et qu’elle affecte ainsi un plus petit nombre de nœuds ou de services. De ce fait, il y a moins de services à déplacer simultanément, ce qui limite l’activité au sein du système. Cela tend à améliorer la fiabilité, car un pan moins important du service est affecté dans le cas de l’introduction d’un problème pendant la mise à niveau. Davantage de domaines de mise à niveau signifie aussi que vos besoins en mémoire tampon sur les autres nœuds sont moindres pour gérer l’impact de la mise à niveau. Par exemple, si vous avez cinq domaines de mise à niveau, les nœuds présents dans chacun gèrent environ 20 % du trafic. Si vous avez besoin d’arrêter un domaine de mise à niveau pour effectuer une mise à niveau, cette charge doit en principe être affectée autre part. Comme il vous reste quatre domaines de mise à niveau, chacun d’eux doit pouvoir prendre en charge environ 5 % du trafic total. Davantage de domaines de mise à niveau signifie moins de besoins en mémoire tampon sur les nœuds du cluster. Par exemple, imaginons que vous disposez de 10 domaines de mise à niveau. Dans ce cas, chaque domaine de mise à niveau ne gèrerait qu’environ 10 % du trafic total. Quand une mise à jour parcourt le cluster, chaque domaine n’a besoin de prendre en charge qu’environ 1,1 % du trafic total. Le fait de disposer d’un plus grand nombre domaines de mise à niveau vous permet généralement d’exécuter vos nœuds à un taux d’utilisation plus élevé, car vous avez moins besoin de capacité réservée. Cela vaut aussi pour les domaines d’erreur.  

L’inconvénient dans le fait d’avoir un grand nombre de domaines de mise à niveau est que les mises à niveau ont tendance à prendre plus de temps. À la fin de l’opération sur le domaine de mise à niveau, Service Fabric attend un bref instant avant d’effectuer des vérifications et commencer à mettre à niveau le suivant. Ces laps de temps permettent de détecter les problèmes introduits par la mise à niveau avant son exécution. Le compromis est acceptable, car il empêche les modifications incorrectes d’affecter une trop grande partie du service à la fois.

Un nombre insuffisant de domaines de mise à niveau a de nombreux effets secondaires négatifs : quand chaque domaine de mise à niveau est hors service au cours d’une mise à niveau, une grande partie de votre capacité globale n’est pas disponible. Par exemple, si vous avez seulement trois domaines de mise à niveau, vous vous défaites d’environ 1/3 de votre service global ou de votre capacité de cluster à la fois. Le fait qu’une grande partie de votre service soit arrêtée simultanément n’est pas souhaitable car la capacité doit être suffisante dans le reste de votre cluster pour gérer la charge de travail. En conservant cette mémoire tampon, dans des conditions normales de fonctionnement, ces nœuds subissent une moindre charge. Cela a pour effet d’augmenter le coût d’exécution de votre service.

Il n’existe aucune limite réelle au nombre total de domaines d’erreur ou de mise à niveau dans un environnement, ni de contraintes sur la façon dont ils se chevauchent. Ceci dit, il existe plusieurs modèles courants :

- Correspondance parfaite des domaines d’erreur et des domaines de mise à niveau
- Un domaine de mise à niveau par nœud (instance de système d’exploitation physique ou virtuel)
- Un modèle « agrégé par bandes » ou de « matrice » dans lequel les domaines d’erreur et les domaines de mise à niveau forment une matrice où les machines s’exécutent généralement en suivant la matrice diagonale

<center>
![Dispositions de domaines d’erreur et de mise à niveau][Image4]
</center>

Il n’existe pas de disposition idéale, chacune ayant des avantages et des inconvénients. Par exemple, le modèle à un domaine d’erreur pour un domaine de mise à niveau est simple à mettre en place. Le modèle à un domaine de mise à niveau par nœud ressemble davantage au modèle généralement adopté. Lors des mises à niveau, chaque nœud est mis à jour indépendamment. Il s’agit d’un processus analogue à celui qui consistait par le passé à mettre à jour manuellement des petits groupes d’ordinateurs. 

Le modèle le plus courant est la matrice Domaine d’erreur/Domaine de mise à niveau, où les domaines d’erreur et les domaines de mise à niveau forment une table et où les nœuds sont placés le long de la diagonale. Il s’agit du modèle utilisé par défaut dans les clusters Service Fabric dans Azure. Les clusters constitués d’un grand nombre de nœuds finissent par ressembler au modèle de matrice dense présenté ci-dessus.

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>Contraintes des domaines d’erreur et de mise à niveau, et comportement résultant
Cluster Resource Manager traite le souhait de conserver un service équilibré entre les domaines d’erreur et de mise à niveau en tant que contrainte. Vous trouverez plus d’informations sur les contraintes dans [cet article](service-fabric-cluster-resource-manager-management-integration.md). Les contraintes des domaines d’erreur et de mise à niveau indiquent : « pour une partition de service donnée, il ne doit jamais y avoir une différence *supérieure à un* dans le nombre d’objets de service (instances de service sans état ou réplicas de service avec état) entre deux domaines. » Cela empêche certains déplacements ou organisations qui enfreignent cette contrainte.

Examinons un exemple. Supposons que nous avons un cluster avec six nœuds, configuré avec cinq domaines d’erreur et cinq de mise à niveau.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

Maintenant, supposons que nous créons un service en attribuant à TargetReplicaSetSize (ou, pour un service sans état, à InstanceCount) la valeur 5. Les réplicas se trouvent sur N1-N5. En fait, N6 n’est jamais utilisé, quel que soit le nombre de services équivalents que vous créez. Mais pourquoi ? Examinons la différence entre la disposition actuelle et ce qui se passerait si N6 était choisi.

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

Cette disposition ne respecte pas notre définition de la contrainte du domaine d’erreur. FD0 a deux réplicas, alors que FD1 en a zéro. La différence entre FD0 et FD1 est donc égale à deux. Cluster Resource Manager n’autorise pas cette organisation. De même, si nous avions choisi N2 et N6 (au lieu de N1 et N2) nous aurions obtenu :

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Cette disposition est équilibrée du point de vue des domaines d’erreur. En revanche, elle enfreint à présent la contrainte de domaine de mise à niveau. Cela est dû au fait que UD0 n’a aucun réplica, alors que UD1 en a deux. Par conséquent, cette disposition n’est pas non plus valide et ne sera pas choisie par Cluster Resource Manager. 

## <a name="configuring-fault-and-upgrade-domains"></a>Configuration des domaines d’erreur et de mise à niveau
La définition des domaines d’erreur et de mise à niveau s’effectue automatiquement dans les déploiements Service Fabric hébergés sur Azure. Service Fabric récupère et utilise les informations d’environnement d’Azure.

Si vous créez votre propre cluster (ou si vous voulez exécuter une topologie particulière pendant son développement), vous pouvez vous-même fournir les informations de domaine d’erreur et de domaine de mise à niveau. Dans cet exemple, nous définissons un cluster de développement local à neuf nœuds qui s’étend sur trois « centres de données » (chacun avec trois racks). Ce cluster a également trois domaines de mise à niveau répartis sur ces trois centres de données. Voici un exemple de cette configuration : 

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
> Quand les clusters sont définis via Azure Resource Manager, les domaines d’erreur et les domaines de mise à niveau sont assignés par Azure. Par conséquent, la définition de vos types de nœud et Virtual Machine Scale Sets dans votre modèle Azure Resource Manager n’inclut pas les informations de domaine d’erreur ou de domaine de mise à niveau.
>

## <a name="node-properties-and-placement-constraints"></a>Propriétés de nœud et contraintes de placement
Parfois (en réalité, la plupart du temps), vous voudrez faire en sorte que certaines charges de travail s’exécutent uniquement sur certains types de nœud du cluster. Par exemple, certaines charges de travail peuvent nécessiter des GPU ou des SSD, tandis que d’autres n’en ont pas besoin. Presque toutes les architectures multiniveau sont un bon exemple de ciblage du matériel sur des charges de travail spécifiques. Certaines machines font office de système frontal ou remplissent le rôle de service d’API de l’application et sont exposées aux clients ou à Internet. D’autres machines, souvent dotées d’autres ressources matérielles, gèrent le travail des couches de calcul ou de stockage. Celles-ci ne sont généralement _pas_ directement exposées aux clients ou à Internet. Service Fabric s’attend dans certains cas à ce que des charges de travail particulières aient besoin de s’exécuter sur des configurations matérielles particulières. Par exemple :

* une application multiniveau existante a été « augmentée et déplacée » dans un environnement Service Fabric
* une charge de travail veut s’exécuter sur un matériel spécifique pour des raisons d’isolation de sécurité, de performance ou de mise à l’échelle
* une charge de travail doit être isolée des autres charges de travail pour des raisons de stratégie ou de consommation de ressources

Pour prendre en charge ces types de configurations, Service Fabric dispose d’une notion de premier ordre des balises qui peuvent être appliquées aux nœuds. Ces balises sont appelés **propriétés de nœud**. Les **contraintes de placement** sont les instructions associées aux différents services que vous sélectionnez pour une ou plusieurs propriétés de nœud. Les contraintes de placement définissent là où les services doivent s’exécuter. L’ensemble de contraintes est extensible ; n’importe quelle paire clé/valeur peut fonctionner. 

<center>
![Disposition du cluster avec différentes charges de travail][Image5]
</center>

### <a name="built-in-node-properties"></a>Propriétés de nœud intégrées
Service Fabric définit certaines propriétés de nœud par défaut qui peuvent être utilisées automatiquement sans que l’utilisateur ait à les définir. Les propriétés par défaut définies sur chaque nœud sont **NodeType** et **NodeName**. Par exemple, vous pouvez écrire une contrainte de placement ainsi : `"(NodeType == NodeType03)"`. En règle générale, NodeType est l’une des propriétés les plus couramment utilisées. Elle est utile, car elle correspond parfaitement à un type de machine. Chaque type de machine correspond à un type de charge de travail dans une application multicouche classique.

<center>
![Contraintes de placement et propriétés de nœud][Image6]
</center>

## <a name="placement-constraint-and-node-property-syntax"></a>Syntaxe des contraintes de placement et des propriétés de nœud 
La valeur spécifiée dans la propriété de nœud peut être une chaîne, une valeur booléenne ou une valeur signée longue. L’instruction au niveau du service est appelée une *contrainte* de placement, car elle contraint l’exécution du service à un emplacement spécifique dans le cluster. La contrainte peut être toute déclaration booléenne qui opère sur les différentes propriétés de nœud du cluster. Les sélecteurs valides dans ces déclarations booléennes sont :

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

Le service peut être placé sur les nœuds dont l’instruction de contrainte de placement globale prend la valeur « True » uniquement. Les nœuds sans une propriété définie ne correspondent à aucune contrainte de placement contenant cette propriété.

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

via ClusterConfig.json pour les déploiements autonomes ou Template.json pour les clusters hébergés sur Azure. 

> [!NOTE]
> Dans votre modèle Azure Resource Manager, le type de nœud est généralement paramétré. Il ressemble à « [parameters('vmNodeType1Name')] » plutôt qu’à « NodeType01 ».
>

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
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasSSD == true && SomeProperty >= 4"
```

Si tous les nœuds de NodeType01 sont valides, vous pouvez aussi sélectionner ce type de nœud avec la contrainte « (NodeType == NodeType01) ».

Un des avantages offerts par les contraintes de placement d’un service est qu’elles peuvent être mises à jour dynamiquement pendant l’exécution. Par conséquent, si vous le souhaitez, vous pouvez déplacer un service dans le cluster, ajouter et supprimer des exigences, etc. Service Fabric se charge de vérifier que le service reste opérationnel et disponible, même quand ces types de modifications sont apportées.

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

Les contraintes de placement sont spécifiées pour chaque différente instance de service nommée. Les mises à jour prennent toujours la place de (remplacent) ce qui a été précédemment spécifié.

La définition du cluster définit les propriétés d’un nœud. La modification des propriétés d’un nœud nécessite une mise à niveau de la configuration du cluster. Après avoir mis à niveau les propriétés d’un nœud, chaque nœud affecté doit redémarrer pour faire état de ses nouvelles propriétés. Ces mises à niveau propagées sont gérées par Service Fabric.

## <a name="describing-and-managing-cluster-resources"></a>Description et gestion des ressources de cluster
L’une des principales tâches de n’importe quel orchestrateur consiste à vous aider à gérer la consommation de ressources du cluster. La gestion des ressources de cluster peut signifier plusieurs choses. En premier lieu, il peut s’agir de veiller à ce que les machines ne soient pas en surcharge. Cela implique de vérifier que les machines n’exécutent pas plus de services qu’elles ne peuvent en gérer. En second lieu, il peut s’agir d’équilibrer et d’optimiser ce qui est essentiel au bon fonctionnement des services. Les offres de services sensibles aux coûts ou aux performances ne peuvent pas s’accommoder de la présence simultanée de nœuds à chaud et de nœuds à froid. Les nœuds à chaud entraînent des conflits de ressources et des performances médiocres, tandis que les nœuds à froid représentent un gaspillage de ressources et des coûts accrus. 

Service Fabric représente les ressources en tant que `Metrics`. Les métriques correspondent à n’importe quelle ressource logique ou physique que vous souhaitez décrire pour Service Fabric. Par exemple, « WorkQueueDepth » et « MemoryInMb » sont des métriques. Pour plus d’informations sur les ressources physiques que Service Fabric peut régir sur les nœuds, consultez [gouvernance des ressources](service-fabric-resource-governance.md). Pour plus d’informations sur la configuration de métriques personnalisées et sur leur utilisation, consultez [cet article](service-fabric-cluster-resource-manager-metrics.md)

Les mesures sont différentes des contraintes de placement et des propriétés de nœud. Les propriétés de nœud sont des descripteurs statiques des nœuds proprement dits. Les métriques décrivent les ressources à la disposition des nœuds et que les services consomment quand ils s’exécutent sur un nœud. La propriété d’un nœud peut être équivalente à « HasSSD » et définie sur true ou false. La quantité d’espace disponible sur ce disque SSD et la quantité consommée par les services pourrait correspondre à une métrique nommée « DriveSpaceInMb ». 

Il est important de noter que, comme pour les contraintes de placement et les propriétés de nœud, Service Fabric Cluster Resource Manager ne comprend pas ce que signifient les noms des mesures. Les noms des mesures ne sont que des chaînes. Il est recommandé de déclarer les unités dans le cadre des noms des mesures que vous créez lorsque cela peut être ambigu.

## <a name="capacity"></a>Capacity
Si vous avez désactivé toutes les fonctions d’*équilibrage* des ressources, Cluster Resource Manager de Service Fabric est tout de même en mesure de garantir qu’aucun nœud ne dépasse sa capacité. Il est possible de gérer les dépassements de capacité, sauf si le cluster est saturé ou si la charge de travail dépasse la capacité d’un nœud. La capacité est une autre *contrainte* que Cluster Resource Manager utilise pour comprendre la quantité d’une ressource dont un nœud dispose. La capacité restante est également suivie pour le cluster dans son ensemble. La capacité et la consommation au niveau du service sont toutes deux exprimées en termes de métriques. Par exemple, pour une métrique appelée « ClientConnections », un nœud donné peut avoir une capacité de 32768. Les autres nœuds peuvent avoir d’autres limites. Un service s’exécutant sur ce nœud peut indiquer qu’il consomme actuellement 32256 de la métrique « ClientConnections ».

Pendant l’exécution, Cluster Resource Manager assure le suivi de la capacité restante dans le cluster et sur les nœuds. Pour ce faire, Cluster Resource Manager soustrait la consommation de chaque service à la capacité du nœud sur lequel le service s’exécute. Grâce à ces informations, Cluster Resource Manager de Service Fabric peut déterminer où placer ou déplacer les réplicas afin que les nœuds ne dépassent pas la capacité.

<center>
![Nœuds de cluster et capacité][Image7]
</center>

C# :

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceLoadMetricDescription metric = new ServiceLoadMetricDescription();
metric.Name = "ClientConnections";
metric.PrimaryDefaultLoad = 1024;
metric.SecondaryDefaultLoad = 0;
metric.Weight = ServiceLoadMetricWeight.High;
serviceDescription.Metrics.Add(metric);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

PowerShell :

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ClientConnections,High,1024,0)
```

Vous pouvez voir les capacités définies dans le manifeste de cluster :

ClusterManifest.xml

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

via ClusterConfig.json pour les déploiements autonomes ou Template.json pour les clusters hébergés sur Azure. 

```json
"nodeTypes": [
    {
        "name": "NodeType03",
        "capacities": {
            "ClientConnections": "65536",
        }
    }
],
```

En règle générale, la charge d’un service évolue de manière dynamique. Supposons que la charge d’un réplica de « ClientConnections » est passée de 1024 à 2048, mais que le nœud sur lequel il s’exécutait alors n’avait que 512 de capacité restante pour cette métrique. À présent, le placement de ce réplica ou de cette instance n’est pas valide, car il n’y a pas suffisamment d’espace sur ce nœud. Cluster Resource Manager doit intervenir et ramener le nœud en dessous de la capacité. Il réduit la charge sur le nœud en surcapacité en déplaçant un ou plusieurs réplicas ou instances de ce nœud vers d’autres nœuds. Lors du déplacement des réplicas, Cluster Resource Manager essaie de réduire le coût de ces mouvements. La question du coût de déplacement est abordée dans [cet article](service-fabric-cluster-resource-manager-movement-cost.md) et les stratégies et règles de rééquilibrage de Cluster Resource Manager sont décrites plus en détail [ici](service-fabric-cluster-resource-manager-metrics.md).

## <a name="cluster-capacity"></a>Capacité de cluster
Comment Service Fabric Cluster Resource Manager fait-il pour éviter la saturation du cluster dans son ensemble ? En fait, du fait du chargement dynamique, il ne peut pas faire grand chose. La charge des services peut augmenter indépendamment des actions entreprises par Cluster Resource Manager. Par conséquent, votre cluster qui dispose de suffisamment d’espace aujourd’hui pourrait ne pas être suffisamment puissant demain une fois que vous serez devenu célèbre. Ceci dit, des moyens existent pour éviter les problèmes. La première chose que nous pouvons faire est d’empêcher la création de nouvelles charges de travail qui risquent de saturer le cluster.

Supposons que vous créez un service sans état et qu’il a une charge associée. Supposons que le service respecte la mesure « DiskSpaceInMb ». Supposons également qu’il va utiliser cinq unités de « DiskSpaceInMb » pour chaque instance du service. Vous souhaitez créer trois instances du service. Parfait ! Cela signifie que nous avons besoin de 15 unités « DiskSpaceInMb » dans le cluster pour être en mesure de créer ces instances de service. The Cluster Resource Manager calcule en permanence la capacité et la consommation de chaque métrique pour déterminer la capacité restante au niveau du cluster. Si l’espace est insuffisant, Cluster Resource Manager rejette l’appel de création du service.

Étant donné que l’exigence est seulement de 15 unités disponibles, cet espace peut être affecté de différentes façons. Par exemple, il peut y avoir une unité restante de capacité sur 15 nœuds différents ou trois unités restantes de capacité sur cinq nœuds différents. Si Cluster Resource Manager peut changer l’organisation de façon à avoir cinq unités disponibles sur trois nœuds, il place le service. Il est généralement possible de réorganiser le cluster, à moins que le cluster soit proche de la saturation ou que les services existants ne puissent pas être consolidés pour une raison quelconque.

## <a name="buffered-capacity"></a>Capacité de mise en mémoire tampon
La capacité mise en mémoire tampon est une autre fonctionnalité de Cluster Resource Manager. Elle permet de réserver une partie de la capacité globale des nœuds. Cette mémoire tampon de capacité sert uniquement à placer les services pendant les mises à niveau et les défaillances de nœuds. La capacité mise en mémoire tampon est spécifiée de manière globale par métrique pour tous les nœuds. La valeur de capacité réservée que vous choisissez est fonction du nombre de domaines d’erreur et de mise à niveau dont vous disposez dans le cluster. Un nombre de domaines d’erreur et de mise à niveau plus important signifie que vous pouvez choisir un nombre inférieur pour la capacité de mise en mémoire tampon. Si vous avez davantage de domaines, une portion moins importante de votre cluster sera indisponible lors de mises à niveau et en cas de défaillance. Spécifier la capacité mise en mémoire tampon n’a de sens que si vous avez aussi spécifié la capacité des nœuds pour une métrique.

Voici un exemple montrant comment spécifier la capacité de mise en mémoire tampon :

ClusterManifest.xml

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="SomeMetric" Value="0.15" />
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
          "name": "SomeMetric",
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

La création de nouveaux services échoue lorsque la capacité de mise en mémoire tampon du cluster est épuisée pour une mesure. Empêcher la création de services pour préserver la mémoire tampon est l’assurance que les mises à niveau et les défaillances ne provoqueront pas un dépassement de la capacité des nœuds. La capacité de mise en mémoire tampon est facultative mais recommandée dans tout cluster qui définit une capacité pour une mesure.

Cluster Resource Manager expose ces informations de charge. Pour chaque métrique, ces informations incluent : 
  - les paramètres de capacité mise en mémoire tampon ;
  - la capacité totale ;
  - la consommation actuelle ;
  - une indication de l’état équilibré ou non de chaque métrique ;
  - des statistiques sur l’écart type ;
  - les nœuds qui ont la charge la plus importante et ceux qui ont la charge la moins importante.  
  
Voici un exemple de cette sortie :

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
