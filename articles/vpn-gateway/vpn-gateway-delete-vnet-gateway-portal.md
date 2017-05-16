---
title: "Suppression d’une passerelle de réseau virtuel : portail Azure : Resource Manager | Microsoft Docs"
description: "Supprimez une passerelle de réseau virtuel avec PowerShell dans le modèle de déploiement Resource Manager."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: b4949db62ccd31cf6ce3d19df5459367cac99b59
ms.lasthandoff: 03/31/2017


---
# <a name="delete-a-virtual-network-gateway-using-the-portal"></a>Supprimer une passerelle de réseau virtuel à l’aide du portail
> [!div class="op_single_selector"]
> * [Resource Manager - Portail Azure](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [Classic - PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

Deux approches sont possibles afin de supprimer une passerelle de réseau virtuel pour une configuration de passerelle VPN.

- Si vous souhaitez tout supprimer et recommencer, comme dans le cas d’un environnement de test, vous pouvez supprimer le groupe de ressources. Supprimer un groupe de ressources supprime toutes les ressources du groupe. Cette approche n’est recommandée que si vous ne souhaitez conserver aucune des ressources du groupe de ressources. Vous ne pouvez pas choisir de supprimer uniquement certaines ressources avec cette approche.

- Si vous souhaitez conserver certaines ressources de votre groupe de ressources, la suppression d’une passerelle de réseau virtuel devient légèrement plus complexe. Avant de supprimer la passerelle de réseau virtuel, vous devez commencer par supprimer toutes les ressources qui dépendent de la passerelle. Les étapes à suivre dépendent du type de connexions que vous avez créées et des ressources dépendantes de chaque connexion.

##<a name="deletegw"></a>Supprimer une passerelle VPN

Si vous souhaitez supprimer une passerelle de réseau virtuel, vous devez d’abord supprimer chaque ressource liée à la passerelle de réseau virtuel. Les ressources doivent être supprimées dans un certain ordre en raison des dépendances.

###<a name="step-1-navigate-to-the-virtual-network-gateway"></a>Étape 1 : Accès à la passerelle de réseau virtuel

Dans le [portail Azure](https://portal.azure.com), sous **Toutes les ressources**, cliquez sur la passerelle de réseau virtuel que vous souhaitez supprimer. Le graphique pour une passerelle est le suivant :

![Localiser la passerelle de réseau virtuel](./media/vpn-gateway-delete-vnet-gateway-portal/gw.png)

###<a name="step-2-delete-connections"></a>Étape 2 : Suppression des connexions

1. Dans le panneau de votre passerelle de réseau virtuel, cliquez sur **Connexions** pour afficher toutes les connexions à la passerelle.
2. Cliquez sur **...** sur la ligne du nom de la connexion, puis sélectionnez **Supprimer** dans la liste déroulante.
3. Cliquez sur **Oui** pour confirmer que vous souhaitez supprimer la connexion. Si vous avez plusieurs connexions, supprimez chaque connexion.

###<a name="step-3-delete-the-local-network-gateways"></a>Étape 3 : Suppression des passerelles de réseau local
1. Sous **Toutes les ressources**, recherchez les passerelles de réseau local associées à chaque connexion. Le graphique pour une passerelle de réseau local est le suivant :

    ![Localiser la passerelle de réseau local](./media/vpn-gateway-delete-vnet-gateway-portal/lng.png)
2. Dans le panneau **Vue d’ensemble** de la passerelle de réseau local, cliquez sur **Supprimer**.

###<a name="step-4-delete-the-virtual-network-gateway"></a>Étape 4 : Suppression de la passerelle de réseau virtuel
1. Sous **Toutes les ressources**, recherchez la passerelle de réseau virtuel que vous souhaitez supprimer.
2. Dans le panneau **Vue d’ensemble**, cliquez sur **Supprimer** pour supprimer la passerelle.

>[!NOTE]
> Si vous avez une configuration P2S sur ce réseau virtuel en plus de votre configuration S2S, la suppression de la passerelle de réseau virtuel déconnecte automatiquement tous les clients P2S sans avertissement.
>
>

###<a name="step-5-delete-the-public-ip-address-for-the-gateway"></a>Étape 5 : Suppression de l’adresse IP publique de la passerelle

1. Sous **Toutes les ressources**, recherchez l’adresse IP publique qui a été affectée à la passerelle. Si la passerelle de réseau virtuel était active-active, vous verrez deux adresses IP publiques. Le graphique d’une adresse IP publique est le suivant :

    ![Adresse IP publique](./media/vpn-gateway-delete-vnet-gateway-portal/pip.png)

2. Sur la page **Vue d’ensemble** de l’adresse IP publique, cliquez sur **Supprimer**, puis sur **Oui** pour confirmer.

###<a name="step-6-delete-the-gateway-subnet"></a>Étape 6 : Suppression du sous-réseau de la passerelle

1. Sous **Toutes les ressources**, recherchez le réseau virtuel. 
2. Dans le panneau **Sous-réseaux**, cliquez sur **Sous-réseau de passerelle**, puis cliquez sur **Supprimer**. 
3. Cliquez sur **Oui** pour confirmer que vous souhaitez supprimer le sous-réseau de passerelle.

##<a name="deleterg"></a>Supprimer une passerelle VPN en supprimant le groupe de ressources

Si vous n’avez pas besoin de conserver de ressources dans le groupe de ressources et que vous souhaitez simplement recommencer à zéro, vous pouvez supprimer tout un groupe de ressources. Il s’agit d’un moyen rapide de tout supprimer. Les étapes suivantes s’appliquent uniquement au modèle de déploiement Resource Manager.

1. Sous **Toutes les ressources**, recherchez le groupe de ressources et cliquez pour ouvrir le panneau.
2. Cliquez sur **Supprimer**. Dans le panneau Supprimer, affichez les ressources affectées. Vérifiez que vous voulez supprimer toutes ces ressources. Dans le cas contraire, utilisez les étapes sous [Supprimer une passerelle VPN](#deletegw) en haut de cet article.
3. Pour continuer, saisissez le nom du groupe de ressources que vous souhaitez supprimer, puis cliquez sur **Supprimer**.
