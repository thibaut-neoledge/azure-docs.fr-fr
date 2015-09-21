<properties 
   pageTitle="Utilisation d’adresses IP publiques dans un réseau virtuel"
   description="Apprenez à configurer un réseau virtuel pour utiliser des adresses IP publiques"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/04/2015"
   ms.author="telmos" />

# Espace d’adressage IP public dans un réseau virtuel

Vous pouvez maintenant ajouter un espace d'adressage IP public à vos réseaux virtuels. Auparavant, vous ne pouviez ajouter que des blocs d'adresses RFC 1918 (espace privé) à vos réseaux virtuels. Lorsque vous ajoutez une plage d'adresses IP publique, celle-ci est considérée comme faisant partie de l'espace d'adressage IP du réseau virtuel privé qui est uniquement accessible dans le réseau virtuel, les réseaux virtuels interconnectés et depuis votre emplacement local.

En principe, l’ajout d'un espace d'adressage IP public fonctionne comme suit :

![Adresse IP publique conceptuelle](./media/virtual-networks-public-ip-within-vnet/IC775683.jpg)

## Comment puis-je ajouter une plage d'adresses IP publique ?

Vous ajoutez une plage d'adresses IP publique de la même façon que lorsque vous ajoutez une plage d'adresses IP privées : en utilisant un fichier *netcfg* ou en effectuant la configuration dans le portail. Vous pouvez ajouter une plage d'adresses IP publique lorsque vous créez votre réseau virtuel, ou vous pouvez revenir en arrière et l’ajouter par la suite. L'exemple ci-dessous montre l’espace d'adresses IP public et privé configuré dans le même réseau virtuel.

![Adresse IP publique dans le Portail](./media/virtual-networks-public-ip-within-vnet/IC775684.png)

## Existe-t-il des restrictions ?

Certaines plages d'adresses IP ne sont pas autorisées :

- 224\.0.0.0/4 (multidiffusion)

- 255\.255.255.255/32 (diffusion)

- 127\.0.0.0/8 (bouclage)

- 169\.254.0.0/16 (lien-local)

- 68\.63.129.16/32 (DNS interne)

## Étapes suivantes

[Gestion des propriétés du réseau virtuel](../virtual-networks-settings)

[Gestion des serveurs DNS utilisés par un réseau virtuel](../virtual-networks-manage-dns-in-vnet)

[Suppression d'un réseau virtuelle](../virtual-networks-delete-vnet)

<!---HONumber=Sept15_HO2-->