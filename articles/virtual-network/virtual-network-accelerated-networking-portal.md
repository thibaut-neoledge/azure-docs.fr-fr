---
title: "Mise en réseau accélérée pour une machine virtuelle - Portail | Microsoft Docs"
description: "Découvrez comment configurer la mise en réseau accélérée pour une machine virtuelle à partir du portail Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: af4515c6-4377-4d4a-a104-18fe1348252c
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: a2e8e0dc40a63c363f295149e35f9823c8e90fa8
ms.lasthandoff: 02/28/2017


---
# <a name="accelerated-networking-for-a-virtual-machine-using-the-azure-portal"></a>Mise en réseau accélérée pour une machine virtuelle à l’aide du Portail Azure
> [!div class="op_single_selector"]
> * [Portail Azure](virtual-network-accelerated-networking-portal.md)
> * [PowerShell](virtual-network-accelerated-networking-powershell.md)
> 
> 

L’accélération réseau active la virtualisation SR-IOV (Single Root I/O Virtualization) sur une machine virtuelle, ce qui améliore considérablement les performances réseau. Ce chemin d’accès hautes performances contourne l’hôte du chemin de données en réduisant la latence, l’instabilité et l’utilisation du processeur pour servir les charges de travail réseau les plus exigeantes sur les types de machine virtuelle pris en charge. Cet article explique comment utiliser le portail Azure pour configurer la mise en réseau accélérée dans le modèle de déploiement Azure Resource Manager. Vous pouvez aussi créer une machine virtuelle avec mise en réseau accélérée à l’aide d’Azure PowerShell. Pour en savoir plus, cliquez sur PowerShell dans la zone située en haut de cet article.

L’illustration suivante montre la communication entre deux machines virtuelles avec ou sans accélération réseau :

![Opérateurs de comparaison](./media/virtual-network-accelerated-networking-portal/image1.png)

Sans accélération réseau, tout le trafic réseau vers et depuis la machine virtuelle doit traverser l’hôte et le commutateur virtuel. Le commutateur virtuel fournit au trafic réseau toutes les stratégies, telles que les groupes de sécurité réseau, les listes de contrôle d’accès, l’isolation et d’autres services de réseau virtualisé. Pour plus d’informations, consultez l’article [Virtualisation réseau Hyper-V et commutateur virtuel](https://technet.microsoft.com/library/jj945275.aspx) .

Dans le cas de l’accélération réseau, le trafic réseau parvient à la carte réseau avant d’être transmis à la machine virtuelle. Toutes les stratégies réseau du commutateur virtuel s’appliquent sans que l’accélération réseau soit déchargée et appliquée dans le matériel. L’application de la stratégie au niveau du matériel permet à la carte réseau de transférer le trafic directement à la machine virtuelle, en ignorant l’hôte et le commutateur virtuel, tout en conservant toutes les stratégies qu’il a appliquées dans l’hôte.

Les avantages de l’accélération réseau s’appliquent uniquement à la machine virtuelle activée. Pour de meilleurs résultats, il convient d’activer cette fonctionnalité sur au moins deux machines virtuelles connectées au même réseau virtuel. Lors de la communication entre les réseaux virtuels ou lors de la connexion locale, cette fonctionnalité a peu d’incidence sur la latence globale.

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

## <a name="benefits"></a>Avantages
* **Latence plus faible/Nombre supérieur de paquets par seconde (pps) :** la suppression du commutateur virtuel du chemin de données évite que les paquets liés au traitement des stratégies séjournent sur l’hôte, ce qui augmente le nombre de paquets pouvant être traités dans la machine virtuelle.
* **Instabilité réduite :** le traitement du commutateur virtuel dépend de la quantité de stratégie qui doit être appliquée et de la charge de travail du processeur qui effectue le traitement. Le déchargement des stratégies sur le matériel supprime cette variabilité en fournissant des paquets directement à la machine virtuelle, en supprimant l’hôte dans la communication de la machine virtuelle et toutes les interruptions de logiciel et les changements de contexte.
* **Utilisation réduite du processeur :** ignorer le commutateur virtuel dans l’hôte réduit l’utilisation du processeur pour le traitement du trafic réseau.

## <a name="limitations"></a>Limitations
Les limitations suivantes existent lors de l’utilisation de cette fonctionnalité :

* **Création de l’interface réseau :** l’accélération réseau ne peut être activée que pour une nouvelle interface réseau.  Elle ne peut pas être activée sur une interface réseau existante.
* **Création de machines virtuelles :** une interface réseau avec accélération réseau peut être rattachée uniquement à une machine virtuelle lorsque cette dernière est créée. L’interface réseau ne peut pas être attachée à une machine virtuelle existante.
* **Régions :** option disponible uniquement dans les régions Azure Ouest-Centre des États-Unis et Europe de l’Ouest. Le nombre de régions augmentera dans le futur.
* **Systèmes d’exploitation pris en charge :** Microsoft Windows Server 2012 R2 et Windows Server 2016 Technical Preview 5. Linux et Windows Server 2012 seront pris en charge prochainement.
* **Taille de machine virtuelle :** les tailles Standard_D15_v2 et Standard_DS15_v2 sont les seules tailles d’instance de machine virtuelle prises en charge. Pour plus d’informations, consultez [Tailles des machines virtuelles Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) . Le nombre de tailles d’instance de machine virtuelle augmentera dans le futur.

Les modifications apportées à ces limites seront annoncées sur la page des [mises à jour concernant l’accélération réseau Azure](https://azure.microsoft.com/updates/accelerated-networking-in-preview).

## <a name="create-a-windows-vm-with-accelerated-networking"></a>Créer une machine virtuelle Windows avec accélération réseau
1. Demandez la version préliminaire en envoyant un e-mail à [Abonnements à la mise en réseau accélérée](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) en indiquant votre ID d’abonnement et l’utilisation prévue. N’effectuez pas les étapes restantes tant que vous n’avez pas reçu d’e-mail vous informant que vous avez été accepté dans la version préliminaire.
2. Connectez-vous au portail Azure à l’adresse http://portal.azure.com.
3. Créez une machine virtuelle en effectuant les étapes décrites dans l’article [Créer une machine virtuelle Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), en optant pour les options suivantes :
   
   * Sélectionnez un système d’exploitation répertorié dans la section Limitations de cet article.
   * Sélectionnez un emplacement (région) répertorié dans la section Limitations de cet article.
   * Sélectionnez une taille de machine virtuelle répertoriée dans la section Limitations de cet article. Si l’une des tailles prises en charge n’est pas répertoriée, cliquez sur **Afficher tout** dans le panneau **Choisir une taille** pour sélectionner une taille dans la liste développée.
   * Dans le panneau **Paramètres**, cliquez sur *Activé* pour **Mise en réseau accélérée**, comme illustré dans l’image suivante :
     
       ![Paramètres](./media/virtual-network-accelerated-networking-portal/image3.png)
     
     > [!NOTE]
     > L’option Mise en réseau accélérée n’est visible que dans les cas suivants :
     > 
     > * Vous avez été accepté dans la version préliminaire.
     > * Vous avez sélectionné un système d’exploitation, un emplacement et des tailles de machine virtuelle pris en charge figurant dans la section Limitations de cet article.
     > 
     > 
4. Une fois la machine virtuelle créée, téléchargez le [pilote Mise en réseau accélérée](https://gallery.technet.microsoft.com/Azure-Accelerated-471b5d84), connectez-vous à la machine virtuelle, puis exécutez le programme d’installation du pilote à l’intérieur de la machine virtuelle.
5. Cliquez avec le bouton droit sur l’icône **Gestionnaire de périphériques**. Vérifiez que la **carte Ethernet Mellanox ConnectX-3 Virtual Function** apparaît sous l’option **Réseau** développée, comme dans l’image suivante :
   
    ![Gestionnaire de périphériques](./media/virtual-network-accelerated-networking-portal/image2.png)


