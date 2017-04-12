---
title: "Planifier votre réseau virtuel pour une collection RemoteApp Azure | Microsoft Docs"
description: "Planifier votre réseau virtuel pour une collection Azure RemoteApp"
services: remoteapp
documentationcenter: 
author: mghosh1616
manager: mbaldwin
ms.assetid: ad9aff0e-f374-49c0-951d-4a7be1c36de0
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 1eb8115b13fb18074b4c4726b69e3d9faf387c32
ms.lasthandoff: 03/31/2017


---
# <a name="how-to-plan-your-virtual-network-for-azure-remoteapp"></a>Comment planifier votre réseau virtuel pour Azure RemoteApp
> [!IMPORTANT]
> Azure RemoteApp ne sera plus disponible à partir du 31 août 2017. Pour plus d’informations, lisez [l’annonce](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

Ce document décrit comment configurer votre réseau virtuel Azure (réseau) et le sous-réseau pour Azure RemoteApp. Si vous n’êtes pas familiarisé avec les réseaux virtuels Azure, c’est une fonctionnalité qui vous permet de virtualiser votre infrastructure réseau vers le nuage et pour créer des solutions hybrides avec Azure et vos ressources locales. Pour plus d'informations à ce sujet, cliquez [ici](../virtual-network/virtual-networks-overview.md).

Si vous souhaitez définir des stratégies de sécurité pour le trafic (entrant et sortant) du réseau virtuel dans lequel vous déployez Azure RemoteApp, nous vous recommandons vivement de créer un sous-réseau distinct pour Azure RemoteApp à partir du reste de vos déploiements dans le réseau virtuel Azure. Pour plus d’informations sur la façon de définir des stratégies de sécurité sur votre sous-réseau de réseau virtuel Azure, consultez la section [Qu’est-ce qu’un groupe de sécurité réseau (NSG) ?](../virtual-network/virtual-networks-nsg.md).

## <a name="types-of-azure-remoteapp-collections-with-azure-virtual-networks"></a>Types de collections RemoteApp Azure avec les réseaux virtuels Azure
Les graphiques suivants illustrent les deux options de collection différentes que vous pouvez choisir au moment d’utiliser un réseau virtuel.

### <a name="azure-remoteapp-cloud-collection-with-vnet"></a>Collection Cloud Azure RemoteApp avec réseau virtuel
 ![Azure RemoteApp - Collection Cloud avec réseau virtuel](./media/remoteapp-planvpn/ra-cloudvpn.png)

Cela représente une collection Azure RemoteApp dans laquelle toutes les ressources auxquelles les hôtes de session RemoteApp doivent accéder sont déployées dans Azure. Ils peuvent se trouver dans le même réseau virtuel que le réseau virtuel RemoteApp VNET ou dans un autre réseau virtuel dans Azure.

### <a name="azure-remoteapp-hybrid-collection-with-vnet"></a>Collection hybride RemoteApp avec réseau virtuel
![Azure RemoteApp - Collection hybride avec réseau virtuel](./media/remoteapp-planvpn/ra-hybridvpn.png)

Cela représente une collection Azure RemoteApp dans laquelle certaines ressources auxquelles les hôtes de session RemoteApp doivent accéder sont déployées localement. VNET RemoteApp est lié au réseau local à l’aide de technologies hybrides Azure comme VPN de site à site ou Express Route.

## <a name="how-the-system-works"></a>Fonctionnement du système
En arrière-plan, Azure RemoteApp déploie des machines virtuelles Azure (avec votre image chargée) sur le sous-réseau de réseau virtuel que vous avez choisi lors de la configuration. Si vous avez opté pour une collection hybride, nous essayons de résoudre le nom de domaine complet du contrôleur de domaine saisi lors de la configuration du flux de travail avec le serveur DNS fourni dans le réseau virtuel.  
Si vous vous connectez à un réseau virtuel existant, veillez à exposer les ports nécessaires dans des groupes de sécurité réseau dans votre sous-réseau Azure RemoteApp. 

Nous vous recommandons d’utiliser un [sous-réseau suffisamment grand pour Azure RemoteApp](remoteapp-vnetsizing.md). La prise en charge maximale par le réseau virtuel Azure est /8 (avec des définitions de sous-réseau CIDR). Votre sous-réseau doit être suffisamment grand pour contenir toutes les machines virtuelles Azure RemoteApp pendant la mise à l’échelle lorsque plusieurs utilisateurs accèdent aux applications. 

Voici les éléments à activer sur votre sous-réseau de réseau virtuel : 

1. Le trafic sortant du sous-réseau est affecté à la plage de ports 10101-10175 pour communiquer avec un des services Azure RemoteApp internes.
2. Le trafic sortant de votre sous-réseau doit être autorisé à se connecter au stockage Azure sur le port 443
3. Si Active Directory est hébergé dans Azure, assurez-vous que toutes les machines virtuelles dans le sous-réseau de réseau virtuel pour Azure RemoteApp est en mesure de se connecter à ce contrôleur de domaine. Le serveur DNS du réseau virtuel doit être en mesure de résoudre le nom de domaine complet de ce contrôleur de domaine.

## <a name="virtual-network-with-forced-tunneling"></a>Réseau virtuel avec tunneling forcé
[Tunneling forcé](../vpn-gateway/vpn-gateway-about-forced-tunneling.md) est maintenant pris en charge dans toutes les nouvelles collections Azure RemoteApp. Actuellement, nous ne gérons pas la migration d’une collection existante pour prendre en charge le tunneling forcé.  Vous devez supprimer toutes les collections existantes à l’aide du réseau virtuel que vous associez à Azure RemoteApp et en créer un nouveau pour activer le tunneling sur vos collections. 


