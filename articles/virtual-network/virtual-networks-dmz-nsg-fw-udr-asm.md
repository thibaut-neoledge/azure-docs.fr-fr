---
title: "Exemple DMZ : créer une zone DMZ pour protéger les réseaux avec un pare-feu, un itinéraire défini par l’utilisateur et un groupe de sécurité réseau | Microsoft Docs"
description: "Créer un réseau de périmètre avec un pare-feu, un itinéraire défini par l’utilisateur (UDR) et des groupes de sécurité réseau (NSG)"
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: 
ms.assetid: dc01ccfb-27b0-4887-8f0b-2792f770ffff
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: jonor;sivae
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: fdb3c5cbd3acee90386352c6f180a71aa81f54fe
ms.contentlocale: fr-fr
ms.lasthandoff: 07/11/2017


---
# <a name="example-3--build-a-dmz-to-protect-networks-with-a-firewall-udr-and-nsg"></a>Exemple 3 : créer un réseau de périmètre DMZ pour protéger les réseaux avec un pare-feu, un réseau défini sur l’utilisateur et un groupe de réseau
[Revenir à la page Meilleures pratiques relatives aux frontières de sécurité][HOME]

Cet exemple va créer un réseau de périmètre avec un pare-feu, quatre serveurs Windows, Routage défini par l’utilisateur (UDR), Transfert IP et groupes de sécurité réseau. Vous y découvrirez également comment chacune des commandes concernées fournit une meilleure connaissance de chaque opération. Il comporte également une section Scénario de trafic (Traffic Scenario) qui explique en détail et étape par étape l’évolution du trafic à travers les couches de défense dans la zone DMZ. Enfin, dans la section de référence se trouve l’intégralité du code et des instructions permettant d’élaborer l’environnement destiné à tester et à expérimenter différents scénarios. 

![Zone DMZ bidirectionnelle avec NVA, NSG et UDR][1]

## <a name="environment-setup"></a>Configuration de l’environnement
Dans cet exemple, il existe un abonnement qui contient les éléments suivants :

* Trois services cloud : « SecSvc001 », « FrontEnd001 » et « BackEnd001 »
* Un réseau virtuel « CorpNetwork », avec trois sous-réseaux : « SecNet », « FrontEnd » et « BackEnd »
* Une appliance virtuelle du réseau, dans cet exemple un pare-feu, connecté au sous-réseau SecNet
* un serveur Windows Server représentant un serveur web d’application (« IIS01 »),
* Deux serveurs Windows Server qui représentent les serveurs principaux d’applications (« AppVM01 », « AppVM02 »)
* Un serveur Windows Server qui représente un serveur DNS (« DNS01 »),

Dans la section Références ci-dessous figure un script PowerShell qui générera une grande partie l’environnement décrit ci-dessus. La création de machines virtuelles et de réseaux virtuels, bien qu’effectuée par l’exemple de script, ne figure pas en détail dans ce document.

Pour créer l’environnement :

1. Enregistrer le fichier XML de configuration réseau contenu dans la section Références (mis à jour avec les noms, l’emplacement et les adresses IP correspondant à un scénario donné)
2. Mettre à jour les variables de l’utilisateur dans le script pour qu’elles correspondent à l’environnement dans lequel le script est exécuté (abonnements, noms de service, etc.)
3. Exécuter le script dans PowerShell

**Remarque**: la région indiquée dans le script PowerShell doit correspondre à la région indiquée dans le fichier xml de configuration réseau.

Une fois que le script s’exécute correctement, les opérations de post-script qui suivent doivent être exécutées :

1. Configurer les règles de pare-feu. Ce sujet est traité dans la section ci-dessous intitulée Description de règles de pare-feu.
2. Dans la section Références, il existe deux scripts pour configurer le serveur web et le serveur d’application avec une application web simple permettant le test avec cette configuration réseau de périmètre.

Une fois que le script s’exécute correctement, il faut terminer les règles de pare-feu. Ce sujet est traité dans la section intitulée : Règles de pare-feu.

## <a name="user-defined-routing-udr"></a>Itinéraire défini par l’utilisateur (UDR)
Par défaut, les itinéraires système suivants sont définis en tant que :

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.0.0/16}     VNETLocal                            Active   Default    
         {0.0.0.0/0}       Internet                             Active   Default    
         {10.0.0.0/8}      Null                                 Active   Default    
         {100.64.0.0/10}   Null                                 Active   Default    
         {172.16.0.0/12}   Null                                 Active   Default    
         {192.168.0.0/16}  Null                                 Active   Default

Le VNETLocal constitue toujours le ou les préfixes d’adresse de réseau virtuel défini(s) du réseau virtuel correspondant à ce réseau spécifique (il passera de VNet à VNet en fonction de la manière dont chaque réseau virtuel spécifique est défini. Les itinéraires système restants sont statiques et par défaut, ils prennent la valeur indiquée ci-dessus.

En matière de priorité, les itinéraires sont traités via la méthode de correspondance du plus long préfixe (LPM) et l’itinéraire le lus spécifique de la table est appliqué à une adresse de destination donnée.

Par conséquent, le trafic (par exemple pour le serveur DNS01, 10.0.2.4) pour le réseau local (10.0.0.0/16) serait acheminé via VNet vers sa destination par le biais de l’itinéraire 10.0.0.0/16. En d’autres termes, pour 10.0.2.4, l’itinéraire 10.0.0.0/16 est l’itinéraire le plus spécifique, et ce, même si 10.0.0.0/8 et 0.0.0.0/0 pourraient s’appliquer également. Comme ils sont moins spécifiques, ils n’affectent pas ce trafic. Par conséquent, le trafic vers 10.0.2.4 emprunterait un tronçon suivant du réseau virtuel et serait simplement acheminé vers la destination.

Si le trafic a été conçu pour 10.1.1.1 par exemple, l’itinéraire 10.0.0.0/16 ne s’applique pas, mais 10.0.0.0/8 serait le plus spécifique, et le trafic serait supprimé (« placé dans le trou noir ») dans la mesure où le tronçon suivant est Null. 

Si la destination ne s’applique à aucun des préfixes Null ou aux préfixes de réseaux virtuels locaux, le trafic suivra l’itinéraire spécifique, 0.0.0.0/0 et sera acheminé en externe via Internet, le tronçon suivant et donc, hors du secteur Internet d’Azure.

S’il existe deux préfixes identiques dans la table d’itinéraires, voici l’ordre de préférence basé sur l’attribut « source » de routes :

1. "VirtualAppliance" = Un itinéraire défini par l’utilisateur ajouté manuellement à la table
2. « VPNGateway » = un itinéraire dynamique ( BGP en cas d’utilisation avec des réseaux hybrides), ajouté par un protocole réseau dynamique. Ces itinéraires peuvent changer au fil du temps, le protocole dynamique reflétant automatiquement les modifications intervenues dans le réseau associé
3. « Default » = les itinéraires du système, le réseau local virtuel et les entrées statiques, comme indiqué dans la table d’itinéraires.

> [!NOTE]
> Vous pouvez maintenant utiliser le routage défini par l’utilisateur (UDR) avec ExpressRoute et les passerelles VPN pour forcer l’acheminement du trafic intersite entrant et sortant vers une appliance virtuelle de réseau (NVA).
> 
> 

#### <a name="creating-the-local-routes"></a>Création d’itinéraires locaux
Dans cet exemple, deux tables d’itinéraires sont nécessaires, une pour chacun des sous-réseaux principal et frontal. Chaque table est chargée d’itinéraires statiques appropriés au sous-réseau donné. Dans cet exemple, chaque table possède trois routes :

1. Trafic de sous-réseau local avec Tronçon suivant défini pour permettre le trafic du sous-réseau local pour contourner le pare-feu
2. Trafic du réseau virtuel avec un tronçon suivant défini comme pare-feu. Cela remplace la règle par défaut qui autorise un acheminement direct du trafic de réseau virtuel
3. Ensemble du trafic restant (0/0) avec le tronçon suivant défini comme pare-feu

Une fois que les tables de routage sont créées, ils sont liés à leurs sous-réseaux. La table d’itinéraire de sous-réseau du serveur frontal, une fois créée et liée au sous-réseau, doit ressembler à ce qui suit :

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active 
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active


Dans cet exemple, les commandes suivantes sont utilisées pour générer la table d’itinéraires, ajouter un itinéraire défini par l’utilisateur, puis lier la table d’itinéraire à un sous-réseau (Remarque : tous les éléments ci-dessous commençant par un signe dollar (par ex.: $BESubnet) sont des variables définies par l’utilisateur à partir du script de la section Références de ce document) :

1. Tout d’abord, il faut créer la table d’itinéraires de base. Cet extrait de code illustre la création de la table du sous-réseau du serveur principal. Dans le script, une table correspondante est également créée pour le sous-réseau du serveur frontal.
   
     New-AzureRouteTable -Name $BERouteTableName `
   
         -Location $DeploymentLocation `
         -Label "Route table for $BESubnet subnet"
2. Une fois la table de routage créée, les itinéraires définis par l’utilisateur spécifiques peuvent être ajoutés. Dans cet extrait, tout le trafic (0.0.0.0/0) est acheminé via l’appliance virtuelle (une variable, $VMIP [0], est utilisée pour transmettre l’adresse IP affectée au moment de la création de l’équipement dans le script). Dans le script, une table correspondante est également créée dans la table frontale.
   
     Get-AzureRouteTable $BERouteTableName | `
   
         Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
         -NextHopType VirtualAppliance `
         -NextHopIpAddress $VMIP[0]
3. La saisie d’itinéraire ci-dessus remplace l’itinéraire par défaut « 0.0.0.0/0 », mais la règle de 10.0.0.0/16 par défaut existante autoriserait le trafic au sein du réseau virtuel acheminer les éléments directement vers leur destination, et non vers l’équipement de réseau virtuel. Pour corriger ce comportement, vous devez ajouter la règle suivante.
   
        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
4. À ce stade, vous devez faire un choix. Les deux itinéraires ci-dessus acheminent tout le trafic vers le pare-feu pour évaluation, même le trafic au sein d’un sous-réseau unique. Cela peut être souhaitable, cependant, pour autoriser le trafic au sein d’un sous-réseau pour acheminer localement sans intervention du pare-feu, une troisième règle très spécifique peut être ajoutée. Cet itinéraire États déclare que n’importe quelle adresse de destination du sous-réseau local peut être acheminée directement (NextHopType = VNETLocal).
   
        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
            -NextHopType VNETLocal
5. Enfin, avec la table d’itinéraires créée et renseignée à l’aide d’itinéraires définis par l’utilisateur, le tableau doit être associé à un sous-réseau. Dans le script, la table d’itinéraires du serveur principal est également liée au sous-réseau du serveur frontal. Voici le script de liaison pour le sous-réseau du serveur principal.
   
     Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
   
        -SubnetName $BESubnet `
        -RouteTableName $BERouteTableName

## <a name="ip-forwarding"></a>transfert IP
Le transfert IP est associé à UDR. Il s’agit d’un paramètre d’appliance virtuelle qui permet de recevoir du trafic pas spécialement adressé à l’équipement, puis de transférer ce trafic vers sa destination finale.

Par exemple, si le trafic à partir d’AppVM01 fait une demande au serveur DNS01, l’UDR l’achemine vers le pare-feu. Lorsque le transfert IP est activé, le trafic de la destination de DNS01 (10.0.2.4) est accepté par l’appliance (10.0.0.4), puis transféré vers sa destination finale (10.0.2.4). Si le routage IP n’est pas activé sur le pare-feu, le trafic ne sera pas accepté par l’équipement, même si le tronçon suivant de la table d’itinéraires est le pare-feu. 

> [!IMPORTANT]
> Il est essentiel de ne pas oublier d’activer le transfert IP en conjonction avec l’utilisateur défini.
> 
> 

La configuration du transfert IP est une commande unique et peut être exécutée au moment de la création d’une machine virtuelle. Pour le flux de cet exemple, l’extrait de code se trouve vers la fin du script et regroupé avec les commandes UDR :

1. Appelez l’instance de machine virtuelle qui est votre équipement virtuel, dans ce cas, le pare-feu, et activez le transfert IP (Remarque : tout élément en rouge contenant un signe dollar (par exemple : $VMName[0]) est une variable définie par l’utilisateur issue du script dans la section de référence de ce document. Le zéro entre crochets, [0], représente la première machine virtuelle du tableau des machines virtuelles, pour que l’exemple de script fonctionne sans modification, la première machine virtuelle (VM 0) doit être le pare-feu) :
   
     Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] | `
   
        Set-AzureIPForwarding -Enable

## <a name="network-security-groups-nsg"></a>Groupes de sécurité réseau (NSG)
Dans cet exemple, un groupe NSG est créé, puis chargé avec une seule règle. Ce groupe est ensuite lié uniquement aux sous-réseaux frontaux et principaux (et pas au SecNet). La règle suivante est générée de manière déclarative :

1. Tout le trafic (tous les ports) depuis Internet vers l’ensemble du réseau virtuel entier (tous les sous-réseaux) est refusé.

Bien que dans cet exemple, on utilise des NSG, son principal objectif est celui d’une couche secondaire de défense contre les erreurs de configuration manuelle. Nous voulons bloquer tout trafic entrant en provenance d’Internet vers les sous-réseaux frontal ou principal des sous-réseaux, le trafic doit circuler uniquement via le sous-réseau SecNet vers le pare-feu (puis, le cas échéant, sur les sous-réseaux frontal ou principal). En outre, avec les règles UDR en place, tout trafic ayant atteint les sous-réseaux principal ou frontal est dirigé vers le pare-feu (grâce à l’UDR). Le pare-feu serait considéré comme un flux asymétrique et abandonnerait le trafic sortant. Par conséquent, il existe trois couches de sécurité protégeant les sous-réseaux frontaux et principaux ; (1) aucun point de terminaison n’est ouvert sur les services cloud FrontEnd001 et BackEnd001, (2) NSGs empêche le trafic provenant d’Internet, (3) le pare-feu abandonne le trafic asymétrique.

Point intéressant concernant le groupe de sécurité réseau dans cet exemple : il contient une seule règle, illustrée ci-dessous, qui consiste à refuser le trafic Internet de l’ensemble du réseau virtuel qui inclut le sous-réseau de sécurité. 

    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet `
        from the Internet" `
        -Type Inbound -Priority 100 -Action Deny `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK `
        -DestinationPortRange '*' `
        -Protocol *

Toutefois, étant donné que le NSG est associé uniquement aux sous-réseaux frontaux et principaux, la règle n’est pas exécutée sur le trafic entrant du sous-réseau de sécurité. Par conséquent, même si la règle NSG n’indique aucun trafic Internet sur une adresse de réseau virtuel, le NSG n’est jamais lié au sous-réseau de sécurité, le trafic passe au sous-réseau de sécurité.

    Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
        -SubnetName $FESubnet -VirtualNetworkName $VNetName

    Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
        -SubnetName $BESubnet -VirtualNetworkName $VNetName

## <a name="firewall-rules"></a>Règles de pare-feu
Sur le pare-feu, vous devrez créer les règles de transfert. Étant donné que le pare-feu bloque ou transfère le trafic entrant, sortant ou intra-réseau virtuel, de nombreuses règles de pare-feu. Tout trafic entrant atteindra l’adresse IP publique de service de sécurité (sur différents ports), pour être traité par le pare-feu. L’une des meilleures pratiques consiste à faire un schéma des flux logiques avant de configurer les règles de sous-réseau et de pare-feu afin d’éviter la reprise du travail par la suite. La figure qui suit est une vue logique des règles de pare-feu de cet exemple :

![Affichage logique des règles de pare-feu][2]

> [!NOTE]
> Selon l’appliance virtuelle réseau utilisée, les ports de gestion peuvent varier. Dans cet exemple, il est fait référence à un pare-feu Barracuda NextGen Firewall utilisant les ports 22, 801 et 807. Veuillez consulter la documentation du fournisseur d’appliance pour rechercher les ports exacts utilisés pour la gestion de l’appareil utilisé.
> 
> 

### <a name="logical-rule-description"></a>Description de la logique de règle
Dans le diagramme logique ci-dessus, le sous-réseau de sécurité n’est pas affiché car le pare-feu est la seule ressource de ce sous-réseau, et ce diagramme présente les règles de pare-feu et la façon dont elles autorisent ou refusent les flux et non les itinéraires réels. En outre, les ports externes sélectionnés pour le trafic RDP appartiennent à la plage supérieure de ports (8014 – 8026) et ont été sélectionnés pour s’aligner à peu près sur les deux derniers octets de l’adresse IP locale, pour faciliter la lecture (par exemple, l’adresse du serveur local 10.0.1.4 est associée à un port externe 8014), cependant, tous les ports supérieurs non conflictuels peuvent être utilisés.

Dans cet exemple, nous avons besoin de 7 types de règles, qui se présentent comme suit :

* Règles externes (pour le trafic entrant) :
  1. Règle de gestion de pare-feu : cette règle de redirection de l’application autorise le trafic à traverser les ports de gestion de l’appliance virtuelle du réseau.
  2. Règles de RDP (pour chaque serveur Windows) : ces quatre réseaux (une pour chaque serveur) permettront la gestion des serveurs individuels via RDP. Ces éléments pourraient être regroupés en une règle, en fonction de la capacité de l’appliance virtuelle réseau utilisée.
  3. Règles de trafic d’application : elles sont au nombre de deux, la première correspondant au trafic web frontal, et la seconde, pour le trafic de l’ordinateur principal (par exemple, serveur web vers couche de données). La configuration de ces règles dépend de l’architecture réseau (sur lequel sont placés vos serveurs) et les flux de trafic (direction du flux de trafic et ports utilisés).
     * La première règle permet au trafic d’application réel de parvenir au serveur d’applications. Les autres règles concernent la sécurité, la gestion, etc., les règles d’application sont celles qui permettent aux utilisateurs externes ou aux services d’accéder aux applications. Pour cet exemple, il existe un serveur web sur le port 80, et donc une seule règle d’application redirige le trafic entrant vers l’adresse IP externe, vers l’adresse IP interne des serveurs web. L’adresse réseau de la session de trafic redirigée sera traduite vers le serveur interne.
     * La seconde règle de trafic d’application est la règle du serveur principal qui permet au serveur web de communiquer avec le serveur AppVM01 (et non AppVM02) via n’importe quel port.
* Règles internes (pour le trafic réseau virtuel interne)
  1. Sortie vers règle Internet : cette règle autorise le transfert du trafic en provenance de n’importe quel réseau vers les réseaux sélectionnés. Cette règle est généralement une règle par défaut déjà présente sur le pare-feu, mais à l’état désactivé. Pour cet exemple, cette règle doit être activée.
  2. Règle DNS : cette règle autorise uniquement le trafic DNS (port 53) vers le serveur DNS. Pour cet environnement, la majeure partie du trafic du serveur frontal vers le serveur principal est bloquée. Cette règle autorise spécifiquement DNS à partir de n’importe quel sous-réseau local.
  3. Règle sous-réseau à sous-réseau : cette règle permet à n’importe quel serveur principal du sous-réseau de se connecter à n’importe quel serveur du sous-réseau du serveur frontal (mais pas l’inverse).
* Règle de prévention de défaillance (pour le trafic ne répondant à aucun des éléments ci-dessus) :
  1. Refuser toutes les règles de trafic : il doit toujours s’agir de la dernière règle (en termes de priorité) et par conséquent, si un trafic ne correspond à aucune des règles qui précèdent, il sera abandonné par cette règle. Il s’agit d’une règle par défaut qui est en général activée, et en général, aucune modification n’est nécessaire.

> [!TIP]
> Sur la deuxième règle de trafic de l’application, n’importe quel port est autorisé pour simplifier cet exemple. Dans un scénario réel, le port le plus spécifique et les plages d’adresses doivent être utilisés pour réduire la surface d’attaque de cette règle.
> 
> 

<br />

> [!IMPORTANT]
> Une fois que toutes les règles ci-dessus sont créées, il est important de revoir la priorité de chaque règle pour s’assurer que le trafic sera autorisé ou rejeté comme souhaité. Pour cet exemple, les règles sont classées par ordre de priorité. Il est facile d’être bloqué hors du pare-feu si les règles ne sont pas dans l’ordre. Vous devez au minimum vous assurer que la gestion du pare-feu lui-même est la priorité absolue.
> 
> 

### <a name="rule-prerequisites"></a>Préalable en matière de règles
Condition préalable pour que la machine virtuelle exécute le pare-feu : les points de terminaison publics. Pour que le pare-feu traite le trafic, les points de terminaison publics appropriés doivent être ouverts. Cet exemple comporte trois types de trafic : 1) Trafic de gestion pour contrôler le pare-feu et les règles de pare-feu, 2)Trafic RDP pour contrôler les serveurs Windows et 3) Trafic d’application. Voici les trois colonnes de types de trafic situées dans la partie supérieure des règles de la vue logique des règles de pare-feu ci-dessus.

> [!IMPORTANT]
> Il ne faut surtout pas oublier que **tout** le trafic passe par le pare-feu. Donc, depuis le bureau du serveur IIS01, même s’il se trouve dans le Service cloud du serveur frontal et sur le sous- réseau frontal, pour accéder à ce serveur, nous aurons besoin d’établir une connexion RDP vers le pare-feu sur le port 8014, puis nous devrons autoriser le pare-feu à acheminer la requête RDP en interne vers le port RDP IIS01. Le bouton « Se connecter » du portail Azure ne fonctionne pas car il n’existe pas d’itinéraire RDP vers IIS01 (du point de vue du portail). En d’autres termes, toutes les connexions à partir d’Internet seront établies avec le service de sécurité et un Port, par exemple, secscv001.cloudapp.net:xxxx (se reporter au schéma ci-dessus mapper le port externe et l’adresse et le port IP interne).
> 
> 

Un point de terminaison peut être ouvert au moment de la création de la machine virtuelle ou après sa création (comme dans le script d’exemple), et affiché ci-dessous dans cet extrait de code (Remarque : tout élément qui est précédé du signe dollar (par exemple, $VMName[$i]), est une variable définie par l’utilisateur à partir du script de la section Références de ce document. Le « $I » entre crochets, [$i], représente le nombre de tableaux d’une machine virtuelle dans un tableau de machines virtuelles) :

    Add-AzureEndpoint -Name "HTTP" -Protocol tcp -PublicPort 80 -LocalPort 80 `
        -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | `
        Update-AzureVM

Bien que ce ne soit pas clairement indiqué ici en raison de l’utilisation de variables, les points de terminaison sont **uniquement** ouverts sur le Service de sécurité cloud. Ainsi, on est sûr que la totalité du trafic entrant est gérée (acheminé, les adresses traduites, annulé) par le pare-feu.

Un client de gestion devra être installé sur un ordinateur pour gérer le pare-feu et créer les configurations nécessaires. Consultez la documentation du fournisseur de votre pare-feu (ou autre NVA) sur la façon de gérer l’appareil. Le reste de cette section et la section suivante, Création de règles de pare-feu, décrivent la configuration du pare-feu lui-même par le biais du client de gestion des fournisseurs (et non par le portail Azure ou PowerShell).

Les instructions pour le téléchargement client et la connexion à Barracuda utilisé dans cet exemple se trouvent ici : [Barracuda NG Admin](https://techlib.barracuda.com/NG61/NGAdmin)

Une fois connecté au pare-feu, mais avant la création des règles de pare-feu, il existe deux classes d’objets pré-requises qui peuvent faciliter la création des règles, Réseau et objets service.

Pour cet exemple, trois objets réseaux nommés doivent être définis (un pour le sous-réseau du serveur frontal et le sous-réseau principal, et un objet réseau pour l’adresse IP du serveur DNS). Pour créer un réseau nommé, à partir du tableau de bord client admin Barracuda NG, accédez à l’onglet Configuration. Dans la section de Configuration opérationnelle, cliquez sur Ensemble de règles, puis, dans le menu Objets de pare-feu, cliquez sur « Réseaux ». Ensuite, dans le menu Édition de réseau, cliquez sur Nouveau. L’objet réseau peut désormais être créé, en ajoutant le nom et le préfixe :

![Créer un objet réseau de serveur frontal][3]

Cette opération crée un réseau nommé correspondant au sous-réseau du serveur frontal. Un objet similaire doit être créé pour le sous-réseau du serveur principal. Désormais les sous-réseaux peuvent être référencés plus facilement par nom dans les règles de pare-feu.

Créer un objet de serveur DNS :

![Créer un objet de serveur DNS][4]

La référence d’adresse IP unique sera utilisée comme règle DNS plus tard dans le document.

Le deuxième objet requis sont les Services. Il représente les ports de connexion RDP de chaque serveur. Étant donné que l’objet de service RDP existant est lié au port RDP par défaut, 3389, de nouveaux services peuvent être créés pour autoriser le trafic à partir des ports externes (8014-8026). Les nouveaux ports peuvent également être ajoutés au service RDP existant, mais pour faciliter la démonstration, une règle individuelle doit être créée pour chaque serveur. Pour créer une nouvelle règle RDP pour un serveur, à partir du tableau de bord client administrateur Barracuda NG, accédez à l’onglet configuration. Dans la section Configuration opérationnelle, cliquez sur l’ensemble de règles, puis, dans le menu Objets de pare-feu, cliquez sur « Services », faites défiler la liste des services, puis sélectionnez le service « RDP ». Effectuez un clic droit et sélectionnez Copier, puis avec le bouton droit, cliquez et sélectionnez Coller. Il existe désormais un objet de Service RDP-Copie1 qui peut être modifié. Cliquez avec le bouton droit sur RDP-Coy1, sélectionnez Modifier. La fenêtre Modifier l’objet de service s’affiche s’affiche comme indiqué ici :

![Copie de la règle RDP par défaut][5]

Les valeurs peuvent ensuite être modifiées pour représenter le service RDP d’un serveur spécifique. Pour AppVM01, la règle RDP par défaut doit être modifiée pour intégrer de nouveaux nom de service, description, et port RDP externe utilisé dans le schéma de la Figure 8 (Remarque : les ports passent du port RDP par défaut 3389 au port externe utilisé pour ce serveur spécifique, dans le cas où AppVM01, le port externe, est le 8025). Le service modifié est illustré ci-dessous :

![Règle AppVM01][6]

Ce processus doit être répété pour créer des Services RDP pour les serveurs restants (AppVM02, DNS01 et IIS01). La création de ces services facilite la création de règles et la rend plus évidente dans la section suivante.

> [!NOTE]
> Un service de protocole RDP pour le pare-feu est inutile pour deux raisons. 1) tout d’abord, la machine virtuelle pare-feu est une image Linux, et SSH sera utilisé sur le port 22 de gestion de machine virtuelle et non sur RDP et 2) port 22 et deux autres ports de gestion sont autorisés dans la première règle de gestion décrite ci-dessous. Ils permettent de gérer la connectivité de gestion.
> 
> 

### <a name="firewall-rules-creation"></a>Création de règles de pare-feu
Il existe trois types de règles de pare-feu utilisées dans cet exemple, et elles sont toutes représentées par des icônes distinctes :

La règle de redirection d’application : ![Icône de redirection d’application][7]

La règle NAT de destination : ![Icône NAT de destination][8]

La règle Pass : ![Icône de réussite][9]

Vous trouverez d’autres informations sur ces règles sur le site web de Barracuda.

Pour créer les règles suivantes (ou consulter les règles par défaut existantes), sur le tableau de bord du client administrateur de Barracuda NG, accédez à l’onglet configuration. Dans la section Configuration opérationnelle, cliquez sur Ensemble de règles. Une grille nommée « Règles principales » affiche des règles actives et désactivées sur ce pare-feu. Dans le coin supérieur droit de cette grille se trouve un petit bouton « + » vert. Cliquez dessus pour créer une nouvelle règle (Remarque : votre pare-feu peut être « verrouillé » pour les modifications. Si vous voyez un bouton marqué « Verrouiller » et que vous êtes incapable de créer ou d’éditer des règles, cliquez dessus pour « déverrouiller » l’ensemble de règles et autoriser la modification). Si vous souhaitez modifier une règle existante, sélectionnez cette règle, cliquez avec le bouton droit et sélectionnez Modifier la règle.

Une fois que vos règles sont créées et/ou modifiées, elles doivent être transférées vers le pare-feu et activées. Si cette opération n’est pas effectuée, les modifications de règles ne prendront pas effet. Le processus d’activation et de transfert est décrit en dessous des descriptions de règles de détails.

Les caractéristiques de chaque règle nécessaire pour compléter cet exemple sont décrites comme suit :

* **Règle de gestion de pare-feu**: cette règle de redirection de l’application autorise le trafic à franchir les ports de gestion de l’appliance virtuelle du réseau (dans cet exemple, un pare-feu Barracuda NextGen). Les ports de gestion sont 801, 807 et éventuellement, 22. Les ports interne et externe sont identiques (pas de transfert de port). Cette règle, SETUP-MGMT-ACCESS, est une règle par défaut et elle est activée par défaut (dans la version 6.1 du pare-feu Barracuda NextGen Firewall).
  
    ![Règle de gestion de pare-feu][10]

> [!TIP]
> L’espace d’adresse de cette règle est « Tout » si les plages d’adresses IP de gestion sont connues. La réduction de la portée se traduit par la réduction de la surface d’attaque des ports de gestion.
> 
> 

* **Règles RDP** : ces règles NAT de destination permettent la gestion des serveurs individuels via RDP.
  Il existe quatre champs critiques nécessaires à la création de cette règle :
  
  1. Source : pour permettre l’exécution de RDP depuis n’importe quel endroit, la référence « Tout » est utilisée dans le champ Source.
  2. Service : utilise l’objet de Service approprié créé précédemment, dans ce cas « AppVM01 RDP ». Les ports externes redirigent le trafic vers l’adresse IP locale des serveurs et vers le port 3386 (port RDP par défaut). Cette règle spécifique sert à l’accès RDP à AppVM01.
  3. Destination : doit être le *port local sur le pare-feu*, « DCHP IP Local 1 » ou eth0 si vous utilisez des adresses IP statiques. Le nombre ordinal (eth0, eth1, etc.) peut être différent si votre équipement réseau dispose de plusieurs interfaces locales. C’est le port à partir duquel le port fait ses envois (il peut s’agir du même port que le port de réception), la destination réellement acheminée est celle qui se trouve dans le champ Liste cible.
  4. Redirection : cette section indique l’appliance virtuelle vers lequel rediriger finalement ce trafic. La redirection la plus simple consiste à placer l’adresse IP et le Port (facultatif) dans le champ Liste des cibles. Si aucun port n’est utilisé, c’est le port de destination de la demande entrante qui est utilisé (c’est-à-dire aucune traduction), si un port est désigné, le port est également traduit avec l’adresse IP.
     
     ![Règle RDP de pare-feu][11]
     
     Un total de quatre règles RDP doit être créé : 
     
     | Nom de la règle | Serveur | de diffusion en continu | Liste des cibles |
     | --- | --- | --- | --- |
     | RDP-vers-IIS01 |IIS01 |IIS01 RDP |10.0.1.4:3389 |
     | RDP-vers-DNS01 |DNS01 |DNS01 RDP |10.0.2.4:3389 |
     | RDP 0 AppVM01 |AppVM01 |RDP AppVM01 |10.0.2.5:3389 |
     | RDP-vers-AppVM02 |AppVM02 |AppVm02 RDP |10.0.2.6:3389 |

> [!TIP]
> Affiner la portée des champs Source et Service réduira la surface d’attaque. Vous devez utiliser la portée la plus limitée possible, qui permet toutefois d’utiliser la fonctionnalité.
> 
> 

* **Règles de trafic d’application**: elles sont au nombre de deux, la première correspondant au trafic web frontal, et la seconde, pour le trafic de l’ordinateur principal (par exemple, serveur web vers couche de données). La configuration de ces règles dépend de l’architecture du réseau sur lequel sont placés vos serveurs et des flux de trafic (sens du flux de trafic et les ports utilisés).
  
    Le premier concerne la règle frontale de trafic web :
  
    ![Règle web de pare-feu][12]
  
    La règle Destination NAT permet au trafic de l’application d’atteindre le serveur d’applications. Les autres règles concernent la sécurité, la gestion, etc., les règles d’application sont celles qui permettent aux utilisateurs externes ou aux services d’accéder aux applications. Pour cet exemple, il existe un seul serveur web sur le port 80, et donc, la règle d’application du pare-feu unique redirigera le trafic entrant vers l’adresse IP externe, vers l’adresse IP interne des serveurs web.
  
    **Remarque**: aucun port n’est affecté dans le champ de la liste cible est, et par conséquent le port entrant 80 (ou 443 pour le Service sélectionné) sera utilisé pour la redirection du serveur web. Si le serveur web est à l’écoute sur un autre port, par exemple, le port 8080, le champ de liste cible peut être mis à jour vers 10.0.1.4:8080 pour permettre aussi de rediriger le port.
  
    La seconde règle de trafic d’application est la règle principale qui permet au serveur web de communiquer avec le serveur AppVM01 (et non AppVM02) via le service Any.
  
    ![Règle AppVM01 de pare-feu][13]
  
    Cette règle passe permet à n’importe quel serveur IIS sur le sous-réseau du serveur frontal d’atteindre AppVM01 (adresse IP 10.0.2.5) sur le port Any, en utilisant n’importe quel protocole nécessaire à l’application web.
  
    Dans cette capture d’écran, l’élément « \<explicite-dest\> » est utilisé dans le champ Destination pour désigner 10.0.2.5 comme destination. Il peut être soit explicite, comme indiqué, ou il peut s’agir d’un objet réseau nommé (comme dans les conditions préalables requises pour le serveur DNS). C’est à l’administrateur du pare-feu qu’il appartient de choisir la méthode qui sera utilisée. Pour ajouter 10.0.2.5 comme destination explicite, double-cliquez sur la première ligne vide sous \<explicite dest\>, puis entrez l’adresse dans la fenêtre qui s’affiche.
  
    Avec cette règle Pass, aucune traduction n’est nécessaire, car il s’agit de trafic interne et donc, la méthode de connexion peut être définie sur « Non SNAT ».
  
    **Remarque**: le réseau Source de cette règle correspond à n’importe quelle ressource du sous-réseau du serveur frontal s’il n’y en a qu’un, ou s’il s’agit d’un nombre spécifique de serveurs web, une ressource d’objet réseau peut être créée pour préciser ces adresses IP exactes et non l’ensemble du sous-réseau du serveur frontal.

> [!TIP]
> Cette règle utilise le service « Any » pour faciliter l’installation et l’utilisation. Cela permet d’exécuter une commande ICMPv4 (ping) dans une seule règle. Toutefois, cela n’est pas recommandé. Les ports et protocoles (« Services ») doivent être réduits au minimum, tout en permettant le fonctionnement de l’application, et ce, afin de réduire la surface d’attaque au-delà de cette limite.
> 
> 

<br />

> [!TIP]
> Bien que cette règle indique une référence explicite-dest utilisée, une approche cohérente doit être utilisée tout au long de la configuration du pare-feu. Il est recommandé d’utiliser l’objet réseau nommé tout au long de la prise en charge pour faciliter la lisibilité et la prise en charge. L’élément explicit-dest est utilisé ici uniquement pour montrer une méthode de référence alternative et est en général déconseillé (en particulier pour des configurations complexes).
> 
> 

* **Règle de sortie vers Internet**: cette règle Pass autorise le transfert du trafic en provenance de n’importe quel réseau vers les réseaux de destination sélectionnés. Cette règle est généralement une règle par défaut déjà présente sur le pare-feu Barracuda NextGen Firewall, mais à l’état désactivé. Un clic droit sur cette règle peut permettre d’accéder à la commande Activer la règle. La règle affichée ici a été modifiée pour y ajouter les deux sous-réseaux locaux créés en tant que références dans la section Configuration requise de ce document à l’attribut Source de cette règle.
  
    ![Règle de trafic sortant de pare-feu][14]
* **Règle DNS** : cette règle autorise uniquement le transfert du trafic DNS (port 53) vers le serveur DNS. Pour cet environnement, la majeure partie du trafic du serveur frontal vers le serveur principal est bloquée. Cette règle autorise spécifiquement DNS.
  
    ![Règle DNS de pare-feu][15]
  
    **Remarque** : dans cette capture d’écran, la méthode de connexion est incluse. Cette règle concernant le trafic d’adresse IP pour le trafic des adresses IP interne, aucune traduction n’est requise. La méthode de connexion est définie sur « No SNAT » pour cette règle de test.
* **Règle sous-réseau à sous-réseau**: la règle Pass est une règle par défaut qui a été activée et modifiée pour permettre à n’importe quel serveur du sous-réseau du serveur principal de se connecter à n’importe quel sous-réseau du serveur frontal. Cette règle concerne l’ensemble du trafic interne, et la méthode de connexion peut être définie sur No SNAT.
  
    ![Règle Intra-réseau virtuel de pare-feu][16]
  
    **Remarque** : la case bidirectionnelle n’est pas cochée (et ne l’est pas dans la plupart des règles). Ceci est important dans le sens où cette règle est alors « unidirectionnelle ». Une connexion peut donc être initiée du sous-réseau principal vers le réseau frontal, mais l’opération inverse est impossible. Si cette case à cocher est activée, cette règle permet le trafic bidirectionnel, ce qui n’est pas souhaitable pour notre diagramme logique.
* **Refuser toutes les règles de trafic**: il doit toujours s’agir de la dernière règle (en termes de priorité) et par conséquent si un trafic ne correspond à aucune des règles qui précèdent, il sera abandonné par cette règle. Il s’agit d’une règle par défaut qui est en général activée, et en général, aucune modification n’est nécessaire. 
  
    ![Règle de refus de pare-feu][17]

> [!IMPORTANT]
> Une fois que toutes les règles ci-dessus sont créées, il est important de revoir la priorité de chaque règle pour s’assurer que le trafic sera autorisé ou rejeté comme souhaité. Pour cet exemple, les règles sont dans l’ordre, dans lequel elles doivent apparaître dans la grille principale des règles de transfert du Client de gestion Barracuda.
> 
> 

## <a name="rule-activation"></a>Activation d’une règle
Une fois l’ensemble de règles modifié en fonction de la spécification du schéma logique, il doit être téléchargé sur le pare-feu et ensuite activé.

![Activation de règle de pare-feu][18]

Dans le coin supérieur droit du client de gestion se trouve un ensemble de boutons. Cliquez sur le bouton « Envoyer les modifications » pour envoyer les règles modifiées au pare-feu, puis cliquez sur le bouton « Activer ».

Avec l’activation de l’ensemble de règles de pare-feu, la création de l’environnement de cet exemple est terminée.

## <a name="traffic-scenarios"></a>Scénarios de trafic
> [!IMPORTANT]
> Il ne faut pas oublier que **tout** le trafic passe par le pare-feu. Donc, depuis le bureau du serveur IIS01, même s’il se trouve dans le Service cloud du serveur frontal et sur le sous- réseau frontal, pour accéder à ce serveur, nous aurons besoin d’établir une connexion RDP vers le pare-feu sur le port 8014, puis nous devrons autoriser le pare-feu à acheminer la requête RDP en interne vers le port RDP IIS01. Le bouton « Se connecter » du portail Azure ne fonctionne pas car il n’existe pas d’itinéraire RDP vers IIS01 (du point de vue du portail). Cela signifie que toutes les connexions à partir d’Internet seront orientées vers le Service Sécurité et un port, par exemple, secscv001.cloudapp.net:xxxx.
> 
> 

Pour ces scénarios, les règles de pare-feu suivantes doivent être en place :

1. Gestion de pare-feu
2. RDP vers IIS01
3. RDP vers DNS01
4. RDP vers AppVM01
5. RDP vers AppVM02
6. Trafic d’application vers le web
7. Trafic d’application vers AppVM01
8. Sortant vers Internet
9. Serveur frontal vers DNS01
10. Trafic intra-sous-réseau (principal vers frontal uniquement)
11. Refuser tout

L’ensemble de règles de pare-feu comporte probablement bien d’autres règles. Les règles de n’importe quel pare-feu donné peuvent comporter d’autres numéros de priorité que ceux qui apparaissent ici. Cette liste et les numéros associés représentent une pertinence entre les onze règles et la priorité à laquelle elles obéissent. En d’autres termes, sur le pare-feu, la règle « RDP vers IIS01 » peut être le la règle numéro 5, mais aussi longtemps qu’elle se trouve en dessous de la règle « Gestion du pare-feu » au-dessus de la règle « RDP DNS01 » elle respecte l’esprit général de la liste. Cette liste favorise également les scénarios ci-dessous et leur rapidité, par exemple, « Règle de pare-feu 9 (DNS) ». Par souci de concision, quatre règles de RDP sont collectivement appelées « règles RDP » lorsque le scénario de trafic n’a aucun rapport avec RDP.

Rappelez-vous également que les groupes de sécurité réseau sont en place pour le trafic Internet entrant sur les sous-réseaux serveurs frontal et principal.

#### <a name="allowed-internet-to-web-server"></a>(Autorisé) Internet vers le serveur web
1. Un utilisateur Internet demande une page HTTP en provenance de SecSvc001.CloudApp.Net (Service cloud Internet)
2. Le service Cloud transfère le trafic via un point de terminaison ouvert sur le port 80 vers l’interface de pare-feu sur 10.0.0.4:80 (serveur web)
3. Aucun groupe de sécurité réseau n’est affecté au sous-réseau de sécurité, ce qui permet le trafic vers le pare-feu
4. Le trafic parvient à l’adresse IP du pare-feu (10.0.1.4).
5. Le pare-feu commence le traitement de la règle :
   1. La règle de pare-feu 1 (Gestion de pare-feu) ne s’applique pas. Passer à la règle suivante.
   2. Les règles de pare-feu 2 à 5 (règles RDP) ne s’appliquent pas, passer à la règle suivante
   3. La règle de pare-feu 6 (application: web) s’applique. Le trafic est autorisé, le pare-feu exécute la traduction NAT sur 10.0.1.4 (IIS01)
6. Le sous-réseau du serveur frontal commence le traitement des règles du trafic entrant :
   1. La règle NSG 1 (blocage Internet) ne s’applique pas (le pare-feu a exécuté la traduction d’adresse NAT sur le pare-feu, donc l’adresse source se trouve désormais le pare-feu du sous-réseau de sécurité et est considérée par le sous-réseau du serveur frontal NSG comme « locale » et est donc autorisée). Passer à la règle suivante
   2. Les règles par défaut NSG autorisent le trafic de sous-réseau vers sous-réseau. Le trafic est autorisé, arrête le traitement des règles NSG
7. IIS01 écoute le trafic web, reçoit cette requête et commence à traiter la demande.
8. IIS01 tente de lancer une session FTP vers AppVM01 sur le sous-réseau du serveur principal
9. L’itinéraire UDR sur le sous-réseau du serveur frontal définit le pare-feu comme le tronçon suivant
10. Aucune règle sur le trafic sortant sur le sous-réseau du serveur frontal. Le trafic est autorisé.
11. Le pare-feu commence le traitement de la règle :
    1. La règle de pare-feu 1 (Gestion de pare-feu) ne s’applique pas. Passer à la règle suivante.
    2. Les règles de pare-feu 2 à 5 (règles RDP) ne s’appliquent pas. Passer à la règle suivante
    3. La règle de pare-feu 6 (Aplication:Web) ne s’applique pas. Passer à la règle suivante.
    4. La règle de pare-feu 7 (application: principal) s’applique, le trafic est autorisé, le pare-feu transfère le trafic vers 10.0.2.5 (AppVM01)
12. Le sous-réseau du serveur principal commence le traitement de la règle de trafic entrant :
    1. La règle NSG 1 (Blocage Internet) ne s’applique pas. Passer à la règle suivante.
    2. Les règles par défaut NSG autorisent le trafic de sous-réseau vers sous-réseau. Le trafic est autorisé, arrête le traitement des règles NSG
13. AppVM01 reçoit la demande et lance la session et répond
14. L’itinéraire défini par l’utilisateur (UDR) du sous-réseau du serveur principal fait du pare-feu le tronçon suivant
15. Comme il n’existe aucune règle NSG sortante sur le sous-réseau du serveur principal, la réponse est autorisée.
16. Comme il s’agit d’un trafic de retour sur une session établie, le pare-feu retransfère la réponse au serveur web (IIS01)
17. Le sous-réseau du serveur frontal commence le traitement des règles de trafic entrant :
    1. La règle NSG 1 (Blocage Internet) ne s’applique pas. Passer à la règle suivante.
    2. Les règles par défaut NSG autorisent le trafic de sous-réseau vers sous-réseau. Le trafic est autorisé, arrête le traitement des règles NSG
18. Le serveur IIS reçoit la réponse, effectue la transaction avec AppVM01, puis termine la réalisation de la réponse HTT. Cette réponse HTTP est envoyée au demandeur
19. Comme il n’existe pas de règle NSG pour le trafic sortant sur le sous-réseau du serveur frontal, la réponse est autorisée
20. La réponse HTTP atteint le pare-feu et, comme il s’agit de la réponse à une session NAT établie, elle est acceptée par le pare-feu
21. Le pare-feu redirige ensuite la réponse vers l’utilisateur Internet
22. Comme il n’existe aucune règle NSR sortante ou tronçon UDR sur le sous-réseau frontal, la réponse est autorisée, et l’utilisateur Internet reçoit la page web demandée.

#### <a name="allowed-internet-rdp-to-backend"></a>(Autorisé) Internet RDP vers le serveur principal
1. Un Administrateur serveur sur Internet demande une session RDP AppVM01 via SecSvc001.CloudApp.Net:8025, 8025 étant le numéro de port utilisateur affecté à la règle de pare-feu « RDP vers AppVM01 »
2. Le service de cloud transmet le trafic via le point de terminaison ouvert sur le port 8025 vers l’interface de pare-feu sur 10.0.0.4:8025
3. Aucun groupe de sécurité réseau n’est affecté au sous-réseau de sécurité, ce qui permet le trafic vers le pare-feu
4. Le pare-feu commence le traitement de la règle :
   1. La règle de pare-feu 1 (Gestion de pare-feu) ne s’applique pas. Passer à la règle suivante.
   2. La règle de pare-feu NSG 2 (RDP IIS) ne s’applique pas. Passer à la règle suivante.
   3. La règle de pare-feu NSG 3 (RDP DNS01) ne s’applique pas. Passer à la règle suivante.
   4. La règle de pare-feu 4 (AppVM01 RDP) s’applique, le trafic est autorisé, le pare-feu exécute une traduction d’adresse NAT vers 10.0.2.5:3386 (port RDP sur AppVM01)
5. Le sous-réseau du serveur principal commence le traitement de la règle de trafic entrant :
   1. La règle NSG 1 (blocage Internet) ne s’applique pas (le pare-feu a exécuté la traduction d’adresse NAT sur le pare-feu, donc l’adresse source se trouve désormais le pare-feu du sous-réseau de sécurité et est considéré par le sous-réseau NSG principal comme « locale » et est donc autorisée). Passer à la règle suivante
   2. Les règles par défaut NSG autorisent le trafic de sous-réseau vers sous-réseau. Le trafic est autorisé, arrête le traitement des règles NSG
6. AppVM01 est à l’écoute du trafic RDP et répond
7. En l’absence de règle NSG sur le trafic sortant, les règles par défaut s’appliquent et le retour de trafic est autorisé.
8. UDR achemine le trafic sortant vers le pare-feu qui représente le tronçon suivant
9. Comme il s’agit d’un trafic de retour sur une session établie, le pare-feu retransfère la réponse au serveur Internet
10. La Session RDP est activée.
11. AppVM01 demande le mot de passe utilisateur

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Autorisé) recherche DNS du serveur web sur le serveur DNS
1. Le serveur Web Server IIS01 a besoin d’un flux de données sur www.data.gov, mais doit résoudre l’adresse.
2. La configuration réseau du réseau virtuel DNS01 définit (10.0.2.4 sur le sous-réseau du serveur principal) comme serveur DNS principal, IIS01 envoie la requête DNS pour DNS01.
3. UDR achemine le trafic sortant vers le pare-feu qui représente le tronçon suivant
4. Aucune règle NSG sortante n’est signalée sur le sous-réseau du serveur principal. Le trafic est autorisé.
5. Le pare-feu commence le traitement de la règle :
   1. La règle de pare-feu 1 (Gestion de pare-feu) ne s’applique pas. Passer à la règle suivante.
   2. Les règles de pare-feu 2 à 5 (règles RDP) ne s’appliquent pas. Passer à la règle suivante
   3. Les règles de pare-feu 6 à 7 (règles Application) ne s’appliquent pas. Passer à la règle suivante
   4. La règle de pare-feu 8 (vers Internet) ne s’applique pas. Passer à la règle suivante
   5. La règle de pare-feu 9 (DNS) s’applique, le trafic est autorisé, le pare-feu transfère le trafic vers 10.0.2.4 (DNS01)
6. Le sous-réseau du serveur principal commence le traitement de la règle de trafic entrant :
   1. La règle NSG 1 (Blocage Internet) ne s’applique pas. Passer à la règle suivante.
   2. Les règles par défaut NSG autorisent le trafic de sous-réseau vers sous-réseau. Le trafic est autorisé, arrête le traitement des règles NSG
7. Le serveur DNS reçoit la demande.
8. Le serveur DNS n’a pas d’adresse en cache et demande à un serveur DNS racine sur Internet.
9. UDR achemine le trafic sortant vers le pare-feu qui représente le tronçon suivant
10. Aucune règle NSG sur le trafic sortant sur le sous-réseau du serveur principal. Le trafic est autorisé.
11. Le pare-feu commence le traitement de la règle :
    1. La règle de pare-feu 1 (Gestion de pare-feu) ne s’applique pas. Passer à la règle suivante.
    2. Les règles de pare-feu 2 à 5 (règles RDP) ne s’appliquent pas. Passer à la règle suivante
    3. Les règles de pare-feu 6 à 7 (règles Application) ne s’appliquent pas. Passer à la règle suivante
    4. La règle de pare-feu 8 (vers Internet) s’applique, le trafic est autorisé, une opération SNAT est exécutée sur la session vers le serveur DNS racine sur Internet
12. Le serveur Internet DNS répond, car cette session a été initialisée en interne, la réponse est acceptée par le pare-feu.
13. Comme il s’agit d’une session établie, le pare-feu transmet la réponse au serveur initiateur, DNS01
14. Le sous-réseau du serveur principal commence le traitement de la règle de trafic entrant :
    1. La règle NSG 1 (Blocage Internet) ne s’applique pas. Passer à la règle suivante.
    2. Les règles par défaut NSG autorisent le trafic de sous-réseau vers sous-réseau. Le trafic est autorisé, arrête le traitement des règles NSG
15. Le serveur DNS reçoit la réponse et la met en mémoire cache, puis il répond à la demande initiale de IIS01
16. L’itinéraire défini par l’utilisateur (UDR) du sous-réseau du serveur principal fait du pare-feu le tronçon suivant 
17. Aucune règle NSG sortante sur le sous-réseau du serveur principal. Le trafic est autorisé.
18. Il s’agit d’une session établie sur le pare-feu. La réponse est transmise par le pare-feu sur le serveur IIS
19. Le sous-réseau du serveur frontal commence le traitement des règles de trafic entrant :
    1. Aucune règle NSG ne s’applique au trafic entrant en provenance du sous-réseau du serveur principal vers le sous-réseau du serveur frontal, par conséquent aucune des règles NSG ne s’applique
    2. La règle système par défaut autorisant le trafic entre sous-réseaux autorise le trafic, le trafic est donc autorisé
20. IIS01 reçoit la réponse de la part de DNS01

#### <a name="allowed-backend-server-to-frontend-server"></a>(Autorisé) Serveur principal vers Serveur frontal
1. Un administrateur connecté à AppVM02 via RDP demande un fichier directement depuis le serveur IIS01 via l’explorateur Windows
2. L’itinéraire défini par l’utilisateur (UDR) du sous-réseau du serveur principal fait du pare-feu le tronçon suivant
3. Comme il n’existe aucune règle NSG sortante sur le sous-réseau du serveur principal, la réponse est autorisée.
4. Le pare-feu commence le traitement de la règle :
   1. La règle de pare-feu 1 (Gestion de pare-feu) ne s’applique pas. Passer à la règle suivante.
   2. Les règles de pare-feu 2 à 5 (règles RDP) ne s’appliquent pas. Passer à la règle suivante
   3. Les règles de pare-feu 6 à 7 (règles Application) ne s’appliquent pas. Passer à la règle suivante
   4. La règle de pare-feu 8 (vers Internet) ne s’applique pas. Passer à la règle suivante
   5. La règle de pare-feu 9 (DNS) ne s’applique pas. Passer à la règle suivante.
   6. La règle de pare-feu 10 (Intra-sous-réseau) s’applique, le trafic est autorisé. Le pare-feu transmet le trafic à 10.0.1.4 (IIS01)
5. Le sous-réseau du serveur frontal commence le traitement des règles de trafic entrant :
   1. La règle NSG 1 (Blocage Internet) ne s’applique pas. Passer à la règle suivante.
   2. Les règles par défaut NSG autorisent le trafic de sous-réseau vers sous-réseau. Le trafic est autorisé, arrête le traitement des règles NSG
6. En supposant que l’autorisation et l’authentification sont correctes, IIS01 accepte la demande et répond
7. L’itinéraire UDR sur le sous-réseau du serveur frontal définit le pare-feu comme le tronçon suivant
8. Comme il n’existe pas de règle NSG pour le trafic sortant sur le sous-réseau du serveur frontal, la réponse est autorisée
9. Comme il s’agit d’une session existante sur le pare-feu, cette réponse est autorisée et le pare-feu renvoie la réponse à AppVM02
10. Le sous-réseau du serveur principal entame le traitement du réseau entrant :
    1. La règle NSG 1 (Blocage Internet) ne s’applique pas. Passer à la règle suivante.
    2. Les règles par défaut NSG autorisent le trafic de sous-réseau vers sous-réseau. Le trafic est autorisé, arrête le traitement des règles NSG
11. AppVM02 reçoit la réponse

#### <a name="denied-internet-direct-to-web-server"></a>(Refusé) Internet direct vers le serveur web
1. L’utilisateur Internet tente d’accéder à un fichier sur IIS01 via le service FrontEnd001.CloudApp.Net.
2. Comme aucun point de terminaison n’est ouvert pour le trafic HTTP, il ne franchit pas le service Cloud et n’atteint pas le serveur.
3. Si, pour une raison quelconque, les points de terminaison étaient ouverts, la règle NSG 5 (Bloquer Internet) bloquerait ce trafic.
4. Enfin, l’itinéraire UDR du réseau frontal envoie tout le trafic sortant à partir de IIS01 vers le pare-feu (le tronçon suivant) et le pare-feu est considéré comme un trafic asymétrique et supprime la réponse sortante. Par conséquent, il existe au moins trois couches indépendantes de défense entre Internet et IIS01 via son service de cloud empêchant l’accès non autorisé/inapproprié.

#### <a name="denied-internet-to-backend-server"></a>(Refusé) Internet vers le serveur principal
1. L’utilisateur Internet tente d’accéder à un fichier sur AppVM01 via le service BackEnd001.CloudApp.Net.
2. Comme il n’y a aucun point de terminaison ouvert pour le partage de fichiers, il ne passe pas le Service Cloud et n’atteint pas le serveur.
3. Si les points de terminaison ont été ouverts pour une raison quelconque, la règle NSG 5 (Bloquer Internet) bloquerait le trafic.
4. Enfin, l’itinéraire UDR envoie tout le trafic sortant à partir de AppVM01 vers le pare-feu (le tronçon suivant), et le pare-feu est considéré comme un trafic asymétrique et supprime la réponse sortante. Par conséquent, il existe au moins trois couches indépendantes de défense entre Internet et AppVM01 via son service de cloud empêchant l’accès non autorisé/inapproprié.

#### <a name="denied-frontend-server-to-backend-server"></a>(Autorisé) Serveur frontal vers Serveur principal
1. Supposons que IIS01 est compromis et qu’un code malveillant essaie d’analyser les serveurs du sous-réseau du serveur principal.
2. L’itinéraire UDR de sous-réseau du serveur frontal envoie tout le trafic sortant à partir de IIS01 vers le pare-feu (le tronçon suivant). Cette opération ne peut pas être affectée par la machine virtuelle compromise.
3. Le pare-feu traite le trafic si la demande est faite à AppVM01, ou au serveur DNS dans le cadre de recherches DNS. Le trafic peut potentiellement être autorisé par le pare-feu (en raison des règles de pare-feu 7 et 9). Tout autre trafic est bloqué par la règle de pare-feu 11 (Refuser tout).
4. Si une détection de menaces avancée a été activée sur le pare-feu (sujet qui n’est pas abordé dans ce document, consultez la documentation du fournisseur de fonctionnalités avancées de votre équipement réseau). Même le trafic autorisé par les règles de transfert de base abordées dans ce document pourrait être bloqué s’il contient des signatures ou des modèles connus signalant une règle de menace avancée.

#### <a name="denied-internet-dns-lookup-on-dns-server"></a>(Refusé) Recherche Internet DNS sur le serveur DNS.
1. L’utilisateur Internet tente de rechercher un enregistrement DNS interne sur DNS01 via le service BackEnd001.CloudApp.Net 
2. Comme aucun point de terminaison n’est ouvert pour le trafic DNS, il ne franchit pas le service Cloud et n’atteint pas le serveur.
3. Si, pour une raison quelconque, les points de terminaison étaient ouverts, la règle NSG (Bloquer Internet) bloquerait ce trafic.
4. Enfin, l’itinéraire UDR du réseau principal envoie tout le trafic sortant à partir de DNS01 au pare-feu (le tronçon suivant), et le pare-feu le considère comme un trafic asymétrique et supprime la réponse sortante. Par conséquent, il existe au moins trois couches indépendantes de défense entre Internet et DNS01 via son service de cloud empêchant l’accès non autorisé/inapproprié.

#### <a name="denied-internet-to-sql-access-through-firewall"></a>(Refusé) Accès Internet vers SQL via le pare-feu
1. Un utilisateur Internet demande des données SQL de SecSvc001.CloudApp.Net (Service cloud Internet)
2. Comme aucun point de terminaison n’est ouvert pour SQL, la demande ne franchit pas le service cloud et n’atteint pas le pare-feu.
3. Si , pour une raison quelconque, les points de terminaison SQL étaient ouverts, le pare-feu serait commencerait le traitement de la règle :
   1. La règle de pare-feu 1 (Gestion de pare-feu) ne s’applique pas. Passer à la règle suivante.
   2. Les règles de pare-feu 2 à 5 (règles RDP) ne s’appliquent pas, passer à la règle suivante
   3. Les règles de pare-feu 6 à 7 (règles d’application) ne s’appliquent pas. Passer à la règle suivante
   4. La règle de pare-feu 8 (vers Internet) ne s’applique pas. Passer à la règle suivante
   5. La règle de pare-feu 9 (DNS) ne s’applique pas. Passer à la règle suivante.
   6. La règle de pare-feu 10 (vers Internet) ne s’applique pas. Passer à la règle suivante
   7. La règle de pare-feu 11 (Refuser tout) s’applique, le trafic est autorisé, arrêter le traitement des règles.

## <a name="references"></a>Références
### <a name="main-script-and-network-config"></a>Script principal et configuration réseau
Enregistrez le script complet dans un fichier de script PowerShell. Enregistrez la configuration réseau dans un fichier nommé « NetworkConf2.xml ».
Modifiez les variables définies par l’utilisateur selon vos besoins. Exécutez le script, puis suivez les instructions d’installation de règle de pare-feu ci-dessus.

#### <a name="full-script"></a>Script complet
Ce script exécutera les actions suivantes en fonction des variables définies par l’utilisateur :

1. Connexion à un abonnement Azure
2. Création d’un nouveau compte de stockage
3. Créer un nouveau réseau virtuel et trois sous-réseaux, comme indiqué dans le fichier de configuration du réseau
4. Créer cinq machines virtuelles, 1 pare-feu et 4 machines virtuelles Windows Server 
5. Configurer UDR, notamment :
   1. Création de deux nouvelles tables d’itinéraire
   2. Ajouter des itinéraires aux tables
   3. Lier des tables aux sous-réseaux appropriés
6. Activer le transfert IP sur le NVA
7. Configurez un groupe de sécurité réseau, notamment :
   1. Création d’un groupe de protection réseau
   2. Ajout d’une règle
   3. La liaison du groupe de sécurité réseaux au sous-réseaux appropriés

Ce script PowerShell doit être exécuté localement sur un PC ou un serveur connecté à Internet.

> [!IMPORTANT]
> Lorsque ce script est exécuté, des avertissements ou autres messages d’information peuvent s’afficher dans PowerShell. Seuls les messages d’erreur affichés en rouge sont source de préoccupation.
> 
> 

    <# 
     .SYNOPSIS
      Example of DMZ and User Defined Routing in an isolated network (Azure only, no hybrid connections)

     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Three new cloud services
       - Three Subnets (SecNet, FrontEnd, and BackEnd subnets)
       - A Network Virtual Appliance (NVA), in this case a Barracuda NextGen Firewall
       - One server on the FrontEnd Subnet
       - Three Servers on the BackEnd Subnet
       - IP Forwading from the FireWall out to the internet
       - User Defined Routing FrontEnd and BackEnd Subnets to the NVA

      Before running script, ensure the network configuration file is created in
      the directory referenced by $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).

     .Notes
      Everyone's security requirements are different and can be addressed in a myriad of ways.
      Please be sure that any sensitive data or applications are behind the appropriate
      layer(s) of protection. This script serves as an example of some of the techniques
      that can be used, but should not be used for all scenarios. You are responsible to
      assess your security needs and the appropriate protections needed, and then effectively
      implement those protections.

      Security Service (SecNet subnet 10.0.0.0/24)
       myFirewall - 10.0.0.4

      FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
       IIS01      - 10.0.1.4

      BackEnd Service (BackEnd subnet 10.0.2.0/24)
       DNS01      - 10.0.2.4
       AppVM01    - 10.0.2.5
       AppVM02    - 10.0.2.6

    #>

    # Fixed Variables
        $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
        $VMName = @()
        $ServiceName = @()
        $VMFamily = @()
        $img = @()
        $size = @()
        $SubnetName = @()
        $VMIP = @()

    # User Defined Global Variables
      # These should be changes to reflect your subscription and services
      # Invalid options will fail in the validation section

      # Subscription Access Details
        $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

      # VM Account, Location, and Storage Details
        $LocalAdmin = "theAdmin"
        $DeploymentLocation = "Central US"
        $StorageAccountName = "vmstore02"

      # Service Details
        $SecureService = "SecSvc001"
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"

      # Network Details
        $VNetName = "CorpNetwork"
        $VNetPrefix = "10.0.0.0/16"
        $SecNet = "SecNet"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf3.xml"

      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}

      # UDR Details
        $FERouteTableName = "FrontEndSubnetRouteTable"
        $BERouteTableName = "BackEndSubnetRouteTable"

      # NSG Details
        $NSGName = "MyVNetSG"

    # User Defined VM Specific Config
        # Note: To ensure UDR and IP forwarding is setup
        # properly this script requires VM 0 be the NVA.

        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $SecureService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $SecNet
          $VMIP += "10.0.0.4"

        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"

        # VM 2 - The First Appliaction Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"

        # VM 3 - The Second Appliaction Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"

        # VM 4 - The DNS Server
          $VMName += "DNS01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.4"

    # ----------------------------- #
    # No User Defined Varibles or   #
    # Configuration past this point #
    # ----------------------------- #

      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

      # Create Storage Account
        If (Test-AzureName -Storage -Name $StorageAccountName) { 
            Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}

      # Update Subscription Pointer to New Storage Account
        Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
        Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop

    # Validation
    $FatalError = $false

    If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
         Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
         $FatalError = $true}

    If (Test-AzureName -Service -Name $SecureService) { 
        Write-Host "The SecureService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}

    If (Test-AzureName -Service -Name $FrontEndService) { 
        Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use" -ForegroundColor Green}

    If (Test-AzureName -Service -Name $BackEndService) { 
        Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}

    If (-Not (Test-Path $NetworkConfigFile)) { 
        Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The network config file was found" -ForegroundColor Green
            If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation varible is correct and the netowrk config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}

    If ($FatalError) {
        Write-Host "A fatal error has occured, please see the above messages for more information." -ForegroundColor Red
        Return}
    Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}

    # Create VNET
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop

    # Create Services
        Write-Host "Creating Services" -ForegroundColor Cyan
        New-AzureService -Location $DeploymentLocation -ServiceName $SecureService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop

    # Build VMs
        $i=0
        $VMName | Foreach {
            Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
            If ($VMFamily[$i] -eq "Firewall") 
                { 
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Linux -LinuxUser $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Set up all the EndPoints we'll need once we're up and running
                # Note: All traffic goes through the firewall, so we'll need to set up all ports here.
                #       Also, the firewall will be redirecting traffic to a new IP and Port in a forwarding
                #       rule, so all of these endpoint have the same public and local port and the firewall
                #       will do the mapping, NATing, and/or redirection as declared in the firewall rules.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPWeb"    -Protocol tcp -PublicPort 8014 -LocalPort 8014 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPApp1"   -Protocol tcp -PublicPort 8025 -LocalPort 8025 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPApp2"   -Protocol tcp -PublicPort 8026 -LocalPort 8026 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPDNS01"  -Protocol tcp -PublicPort 8024 -LocalPort 8024 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "RemoteDesktop" | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                }
            $i++
        }

    # Configure UDR and IP Forwarding
        Write-Host "Configuring UDR" -ForegroundColor Cyan

      # Create the Route Tables
        Write-Host "Creating the Route Tables" -ForegroundColor Cyan 
        New-AzureRouteTable -Name $BERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $BESubnet subnet"
        New-AzureRouteTable -Name $FERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $FESubnet subnet"

      # Add Routes to Route Tables
        Write-Host "Adding Routes to the Route Tables" -ForegroundColor Cyan 
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
            -NextHopType VNETLocal
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $FEPrefix `
            -NextHopType VNETLocal

      # Assoicate the Route Tables with the Subnets
        Write-Host "Binding Route Tables to the Subnets" -ForegroundColor Cyan 
        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
            -SubnetName $BESubnet `
            -RouteTableName $BERouteTableName
        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
            -SubnetName $FESubnet `
            -RouteTableName $FERouteTableName

     # Enable IP Forwarding on the Virtual Appliance
        Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] `
            |Set-AzureIPForwarding -Enable

    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan

      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"

      # Add NSG Rule
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 100 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
            -Protocol *

      # Assign the NSG to two Subnets
        # The NSG is *not* bound to the Security Subnet. The result
        # is that internet traffic flows only to the Security subnet
        # since the NSG bound to the Frontend and Backback subnets
        # will Deny internet traffic to those subnets.
        Write-Host "Binding the NSG to two subnets" -ForegroundColor Cyan
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

    # Optional Post-script Manual Configuration
      # Configure Firewall
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install Backend resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host


#### <a name="network-config-file"></a>Fichier de configuration réseau
Enregistrer ce fichier XML avec l’emplacement mis à jour et ajouter le lien vers ce fichier à la variable $NetworkConfigFile dans le script ci-dessus.

    <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <Dns>
          <DnsServers>
            <DnsServer name="DNS01" IPAddress="10.0.2.4" />
            <DnsServer name="Level3" IPAddress="209.244.0.3" />
          </DnsServers>
        </Dns>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="CorpNetwork" Location="Central US">
            <AddressSpace>
              <AddressPrefix>10.0.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="SecNet">
                <AddressPrefix>10.0.0.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="FrontEnd">
                <AddressPrefix>10.0.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="BackEnd">
                <AddressPrefix>10.0.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
            <DnsServersRef>
              <DnsServerRef name="DNS01" />
              <DnsServerRef name="Level3" />
            </DnsServersRef>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>

#### <a name="sample-application-scripts"></a>Exemples de scripts d’application
Si vous souhaitez installer un exemple d’application et d’autres exemples de zone DMZ, vous en trouverez à l’adresse suivante : [Exemple de script d’application][SampleApp]

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3design.png "Zone DMZ bidirectionnelle avec NVA, NSG et UDR"
[2]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3firewalllogical.png "Affichage logique des règles de pare-feu"
[3]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectfrontend.png "Créer un objet réseau FrontEnd"
[4]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectdns.png "Créer un objet de serveur DNS"
[5]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpa.png "Copie de la règle RDP par défaut"
[6]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpb.png "Règle AppVM01"
[7]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconapplicationredirect.png "Icône de redirection d’application"
[8]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/icondestinationnat.png "Icône NAT de destination"
[9]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconpass.png "Icône de réussite"
[10]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulefirewall.png "Règle de gestion de pare-feu"
[11]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulerdp.png "Règle RDP de pare-feu"
[12]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleweb.png "Règle web de pare-feu "
[13]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleappvm01.png "Règle AppVM01 de pare-feu "
[14]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleoutbound.png "Règle de trafic sortant de pare-feu"
[15]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledns.png "Règle DNS de pare-feu"
[16]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleintravnet.png "Règle Intra-réseau virtuel de pare-feu"
[17]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledeny.png "Règle de refus de pare-feu"
[18]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/firewallruleactivate.png "Activation de règle de pare-feu"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md

