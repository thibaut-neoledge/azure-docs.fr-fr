---
title: "Migrer un réseau virtuel Azure (classique) depuis un groupe d’affinités vers une région | Microsoft Docs"
description: "Découvrez comment migrer un réseau virtuel Azure (classique) depuis un groupe d’affinités vers une région."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 84febcb9-bb8b-4e79-ab91-865ad9de41cb
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.openlocfilehash: b9b3bd0f2184ac85261166d5fe2ab67e1bf319d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-a-virtual-network-classic-from-an-affinity-group-to-a-region"></a>Migrer un réseau virtuel Azure (classique) depuis un groupe d’affinités vers une région

> [!IMPORTANT]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle de déploiement Resource Manager.

Les groupes d’affinités vous permettent de vous assurer que les ressources créées dans le même groupe d’affinités sont physiquement hébergées par des serveurs proches les uns des autres, ce qui leur permet de communiquer plus rapidement. Auparavant, les groupes d’affinités étaient requis pour la création des réseaux virtuels (classiques). À cette époque, le service de gestionnaire réseau qui gérait les réseaux virtuels (classiques) ne fonctionnait qu’au sein d’un ensemble de serveurs physiques ou d’une unité d’échelle. Les améliorations architecturales ont augmenté la portée de la gestion réseau à une région.

Suite à ces améliorations architecturales, les groupes d’affinités ne sont plus recommandés ou requis pour les réseaux virtuels (classiques). L’utilisation de groupes d’affinités pour les réseaux virtuels (classiques) est remplacée par les régions. Les réseaux virtuels (classiques) qui sont associés à des régions sont appelés réseaux virtuels régionaux.

Nous vous recommandons de ne pas utiliser les groupes d’affinités en général. Outre la spécification du réseau virtuel, les groupes d’affinités étaient également importants pour garantir que des ressources, telles que le calcul (classique) et le stockage (classique), étaient placées proches les unes des autres. Toutefois, avec l’architecture actuelle du réseau Azure, ces exigences de placement ne sont plus nécessaires.

> [!IMPORTANT]
> Bien qu’il soit techniquement possible de créer un réseau virtuel associé à un groupe d’affinités, il n’existe aucune raison valable de le faire. De nombreuses fonctionnalités de réseau virtuel, telles que les groupes de sécurité réseau, sont disponibles uniquement quand vous utilisez un réseau virtuel régional et ne sont pas disponibles pour les réseaux virtuels associés à des groupes d’affinités.
> 
> 

## <a name="edit-the-network-configuration-file"></a>Modifier le fichier de configuration réseau

1. Exportez le fichier de configuration réseau. Pour savoir comment exporter un fichier de configuration réseau à l’aide de PowerShell ou de l’interface de ligne de commande (CLI 1.0) Azure, consultez la page [Configurer un réseau virtuel (Classic) à l’aide d’un fichier config réseau](virtual-networks-using-network-configuration-file.md#export).
2. Modifiez le fichier de configuration réseau, en remplaçant le paramètre **AffinityGroup** par **Location**. Spécifiez une [région](https://azure.microsoft.com/regions) Azure pour le paramètre **Location**.
   
   > [!NOTE]
   > Le paramètre **Location** correspond à la région que vous avez spécifiée pour le groupe d’affinités associé à votre réseau virtuel (classique). Par exemple, si votre réseau virtuel (classique) est associé à un groupe d’affinités qui se trouve dans l’Ouest des États-Unis, votre paramètre **Location** doit pointer sur Ouest des États-Unis pendant la migration. 
   > 
   > 
   
    Modifiez les lignes suivantes dans votre fichier de configuration réseau, en remplaçant les valeurs avec les vôtres : 
   
    **Ancienne valeur :**\<VirtualNetworkSitename="VNetUSWest" AffinityGroup="VNetDemoAG"\> 
   
    **Nouvelle valeur :**\<VirtualNetworkSitename="VNetUSWest" Location="West US"\>
3. Enregistrez vos modifications et [importez](virtual-networks-using-network-configuration-file.md#import) la configuration réseau dans Azure.

> [!NOTE]
> Cette migration n'entraîne PAS de temps d’arrêt de vos services.
> 
> 

## <a name="what-to-do-if-you-have-a-vm-classic-in-an-affinity-group"></a>Marche à suivre en cas de machine virtuelle (classique) dans un groupe d’affinités
Les machines virtuelles (classiques) qui sont actuellement dans un groupe d’affinités n’ont pas besoin d’être supprimées du groupe d’affinités. Une fois qu’une machine virtuelle est déployée, elle l’est sur une seule unité d’échelle. Les groupes d’affinités peuvent restreindre la taille disponible de l’ensemble des machines virtuelles pour un nouveau déploiement de machines virtuelles, mais les machines virtuelles existantes déployées sont déjà limitées à la taille disponible de l’ensemble des machines virtuelles dans l’unité d’échelle où elles sont déployées. Étant donné que la machine virtuelle est déjà déployée dans une unité d’échelle, la suppression d’une machine virtuelle d’un groupe d’affinités n’a aucun effet sur cette dernière.
