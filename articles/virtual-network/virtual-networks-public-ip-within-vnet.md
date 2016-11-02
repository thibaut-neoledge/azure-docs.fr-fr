<properties 
   pageTitle="Utilisation d’adresses IP publiques dans un réseau virtuel"
   description="Apprenez à configurer un réseau virtuel pour utiliser des adresses IP publiques"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/27/2016"
   ms.author="jdial" />

# Espace d’adressage IP public dans un réseau virtuel

Les réseaux virtuels (VNets) peuvent contenir des plages d’adresses IP publiques et privées (blocs d’adresses RFC 1918). Lorsque vous ajoutez une plage d'adresses IP publique, celle-ci est considérée comme faisant partie de l'espace d'adressage IP du réseau virtuel privé qui est uniquement accessible dans le réseau virtuel, les réseaux virtuels interconnectés et depuis votre emplacement local.

L’illustration ci-dessous montre un réseau virtuel qui inclut des plages d’adresse IP publiques et privées.

![Adresse IP publique conceptuelle](./media/virtual-networks-public-ip-within-vnet/IC775683.jpg)

## Comment puis-je ajouter une plage d'adresses IP publique ?

Vous ajoutez une plage d'adresses IP publiques de la même manière que vous ajoutez une plage d'adresses IP privées : en utilisant un fichier *netcfg* ou en ajoutant la configuration dans le [portail Azure](http://portal.azure.com). Vous pouvez ajouter une plage d'adresses IP publique lorsque vous créez votre réseau virtuel, ou vous pouvez revenir en arrière et l’ajouter par la suite. L'exemple ci-dessous montre l’espace d'adressage IP public et privé configuré dans le même réseau virtuel.

![Adresse IP publique dans le Portail](./media/virtual-networks-public-ip-within-vnet/IC775684.png)

## Existe-t-il des restrictions ?

Certaines plages d'adresses IP ne sont pas autorisées :

- 224\.0.0.0/4 (multidiffusion)

- 255\.255.255.255/32 (diffusion)

- 127\.0.0.0/8 (bouclage)

- 169\.254.0.0/16 (lien-local)

- 168\.63.129.16/32 (DNS interne)

## Étapes suivantes

[Gestion des serveurs DNS utilisés par un réseau virtuel](virtual-networks-manage-dns-in-vnet.md)

<!---HONumber=AcomDC_0810_2016-->