<properties 
   pageTitle="Présentation des itinéraires définis par l’utilisateur et du transfert IP"
   description="Présentation des itinéraires définis par l’utilisateur et du transfert IP"
   services="virtual-networks"
   documentationCenter="na"
   authors="telmosampaio"
   manager="adinah"
   editor="tysonn" /> 
<tags 
   ms.service="virtual-networks"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/22/2015"
   ms.author="telmos" />

# Itinéraires définis par l’utilisateur et transfert IP
Azure utilise une table d’itinéraires pour déterminer comment transférer le trafic IP en fonction de la destination de chaque paquet. Bien qu’Azure fournisse une table d’itinéraires par défaut en fonction de vos paramètres de réseau virtuel, vous devrez peut-être ajouter des itinéraires personnalisés à cette table. Le besoin le plus courant pour une entrée personnalisée de la table d’itinéraires est l’utilisation d’une appliance virtuelle dans votre environnement Azure. Étudiez le scénario illustré dans la figure ci-dessous. Supposons que vous souhaitez vous assurer que tout le trafic dirigé vers les sous-réseaux intermédiaires et principaux initiés à partir du sous-réseau frontal passe par une appliance de pare-feu virtuelle. L’ajout de l’appliance à votre réseau virtuel et sa connexion aux différents sous-réseaux n’est pas la solution. Vous devez également modifier la table d’itinéraires appliquée à votre sous-réseau pour garantir que les paquets sont transférés vers l’appliance de pare-feu virtuelle.

Il en est de même si vous avez implémenté une appliance virtuelle NAT pour contrôler le trafic entre votre réseau virtuel Azure et Internet. Pour garantir que l’appliance virtuelle est utilisée, vous devez créer un itinéraire spécifiant que tout le trafic destiné à Internet doit être transféré vers l’appliance virtuelle.

## Routage
Les paquets sont acheminés via un réseau TCP/IP basé sur une table d’itinéraires définie sur chaque nœud du réseau physique. Une table d’itinéraires est une collection d’itinéraires individuels permettant de déterminer où transférer les paquets en fonction de l’adresse IP de destination. Un itinéraire se compose des éléments suivants :

- **Préfixe d’adresse**. CIDR de destination auquel s’applique l’itinéraire, par exemple 10.1.0.0/16.
- **Type de tronçon suivant**. Type de tronçon Azure vers lequel le paquet doit être envoyé. Les valeurs possibles sont les suivantes :
	- **Local**. Représente le réseau virtuel local. Par exemple, si vous avez deux sous-réseaux, 10.1.0.0/16 et 10.2.0.0/16 qui sont situés dans le même réseau virtuel, l’itinéraire de chaque sous-réseau de la table d’itinéraires a la valeur de tronçon suivant *Local*.
	- **Passerelle VPN**. Représente une passerelle VPN de site à site Azure. 
	- **Internet**. Représente la passerelle Internet par défaut fournie par l’infrastructure Azure 
	- **Appliance virtuelle**. Représente une appliance virtuelle que vous avez ajoutée à votre réseau virtuel Azure.
	- **NULL**. Représente un trou noir. Les paquets transmis à un trou noir ne sont pas du tout transférés.
- **Valeur de tronçon suivant**. La valeur de tronçon suivant contient l’adresse IP vers laquelle les paquets doivent être transférés. Les valeurs de tronçon suivant sont autorisées uniquement dans les itinéraires où le type de tronçon suivant est *Appliance virtuelle*.

## Itinéraires par défaut
Chaque sous-réseau créé dans un réseau virtuel est automatiquement associé à une table d’itinéraires qui contient les règles d’itinéraire par défaut suivantes :
 - **Règle de réseau virtuel locale** : cette règle est automatiquement créée pour chaque sous-réseau d’un réseau virtuel. Elle indique qu’il existe un lien direct entre les machines virtuelles du réseau virtuel et qu’il n’existe aucun tronçon suivant intermédiaire.
 - **Règle locale** : cette règle s’applique à tout le trafic destiné à la plage d’adresses locale et utilise la passerelle VPN comme tronçon suivant de destination. 
 - **Règle Internet** : cette règle gère tout le trafic destiné à Internet public et utilise la passerelle Internet d’infrastructure en tant que tronçon suivant pour tout le trafic destiné à Internet.

## Itinéraires BGP
Si vous avez une connexion ExpressRoute entre votre réseau local et Azure, vous pouvez activer BGP pour propager les itinéraires de votre réseau local vers Azure. Ces itinéraires BGP sont utilisés de la même façon que les itinéraires par défaut et les itinéraires définis par l’utilisateur dans chaque sous-réseau Azure. Pour plus d’informations, consultez la page [Présentation d’ExpressRoute](../expressroute-introduction)

[AZURE.IMPORTANT]Vous pouvez configurer votre environnement Azure de manière à ce qu’il utilise le tunneling forcé via votre réseau local en créant un itinéraire défini par l’utilisateur pour le sous-réseau 0.0.0.0/0 qui utilise la passerelle VPN comme tronçon suivant. Toutefois, cela ne fonctionne que si vous utilisez une passerelle VPN, et non ExpressRoute. Pour ExpressRoute, le tunneling forcé est configuré via BGP.

## Itinéraires définis par l’utilisateur
Vous ne pouvez pas afficher les itinéraires par défaut spécifiés ci-dessus dans votre environnement Azure, et pour la plupart des environnements, ce sont les seuls itinéraires dont vous avez besoin. Toutefois, vous pouvez créer une table d’itinéraires et ajouter un ou plusieurs itinéraires dans des cas spécifiques, par exemple:

- Forcer le tunneling vers Internet via votre réseau local.
- Utiliser des appliances virtuelles dans votre environnement Azure.
- 
Dans les scénarios ci-dessus, vous devez créer une table d’itinéraires et lui ajouter des itinéraires définis par l’utilisateur. Vous pouvez avoir plusieurs tables d’itinéraires et celles-ci peuvent être associées à un ou plusieurs sous-réseaux. Chaque sous-réseau ne peut être associé qu’à une seule table d’itinéraires. L’ensemble des machines virtuelles et services cloud d’un sous-réseau utilisent la table d’itinéraires associée à ce sous-réseau.

Les sous-réseaux s’appuient sur les itinéraires par défaut jusqu’à ce qu’une table d’itinéraires soit associée au sous-réseau. Une fois que l’association existe, le routage se base sur la correspondance de préfixe la plus longue parmi les itinéraires définis par l’utilisateur et les itinéraires par défaut. S’il existe plusieurs itinéraires avec la même correspondance de préfixe la plus longue, un itinéraire est sélectionné en fonction de son origine dans l’ordre suivant :

1. Itinéraire défini par l’utilisateur
1. Itinéraire BGP \(lorsque ExpressRoute est utilisé\)
1. Itinéraire par défaut

[AZURE.IMPORTANT]Les itinéraires définis par l’utilisateur sont appliqués uniquement aux machines virtuelles et services cloud Azure. Par exemple, si vous souhaitez ajouter une appliance virtuelle de pare-feu entre votre réseau local et Azure, vous devez créer un itinéraire défini par l’utilisateur pour vos tables d’itinéraires Azure qui transmettent tout le trafic allant vers l’espace d’adressage local à l’appliance virtuelle. Toutefois, le trafic entrant à partir de l’espace d’adressage local transite de votre passerelle VPN ou circuit ExpressRoute directement vers l’environnement Azure, en ignorant l’appliance virtuelle.

## Transfert IP
Comme décrit ci-dessus, une des raisons principales pour créer un itinéraire défini par l’utilisateur est de transférer le trafic vers une appliance virtuelle. Une appliance virtuelle n’est rien de plus qu’une machine virtuelle qui exécute une application permettant de gérer le trafic réseau d’une certaine façon, comme un pare-feu ou un périphérique NAT.

La machine virtuelle d’appliance virtuelle doit être capable de recevoir le trafic entrant qui ne lui est pas adressé. Pour permettre à une machine virtuelle de recevoir le trafic adressé à d’autres destinations, vous devez activer le transfert IP dans la machine virtuelle.

## Voir aussi

[Création d’itinéraires et activation du transfert IP dans Azure](../virtual-networks-udr-how-to)

[Adresses IP publiques de niveau d’instance](../virtual-networks-instance-level-public-ip)

[Présentation du réseau virtuel](https://msdn.microsoft.com/library/azure/jj156007.aspx).

<!--HONumber=52-->
