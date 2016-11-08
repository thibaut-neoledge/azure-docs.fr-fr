---
title: Comment migrer des groupes d’affinités vers un réseau virtuel régional
description: En savoir plus sur la migration des groupes d’affinités vers les réseaux virtuels régionaux
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn

ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial

---
# Comment migrer des groupes d’affinités vers un réseau virtuel régional
Vous pouvez utiliser un groupe d’affinités pour vous assurer que les ressources créées dans le même groupe d’affinités sont physiquement hébergées par des serveurs proches les uns des autres, ce qui leur permet de communiquer plus rapidement. Auparavant, les groupes d’affinités étaient requis pour la création des réseaux virtuels. À cette époque, le service de gestionnaire réseau qui gérait les réseaux virtuels ne fonctionnait qu’au sein d’un ensemble de serveurs physiques ou d’une unité d’échelle. Les améliorations architecturales ont augmenté la portée de la gestion réseau à une région.

Suite à ces améliorations architecturales, les groupes d’affinités ne sont plus recommandés ou requis pour les réseaux virtuels. L’utilisation des groupes d’affinités pour les réseaux virtuels est remplacée par les régions. Les réseaux virtuels associés à des régions sont appelés réseaux virtuels régionaux.

En outre, nous vous recommandons de ne pas utiliser les groupes d’affinités en général. Outre la spécification du réseau virtuel, les groupes d’affinités étaient également importants pour garantir que des ressources, telles que le calcul et le stockage, étaient placées proches les unes des autres. Toutefois, avec l’architecture actuelle du réseau Azure, ces exigences de placement ne sont plus nécessaires. Consultez [Groupes d’affinités et machines virtuelles](#Affinity-groups-and-VMs) pour les quelques rares cas restants pour lesquels vous pouvez avoir besoin d’utiliser un groupe d’affinités.

## Création de réseaux virtuels régionaux et migration
À partir de maintenant, quand vous créez des réseaux virtuels, utilisez une *Région*. L’option s’affiche dans le portail de gestion. Notez que dans le fichier de configuration réseau, cette option s’appelle *Emplacement*.

> [!IMPORTANT]
> Bien qu’il soit techniquement possible de créer un réseau virtuel associé à un groupe d’affinités, il n’existe aucune raison valable de le faire. De nombreuses nouvelles fonctionnalités, telles que les groupes de sécurité réseau, sont disponibles uniquement quand vous utilisez un réseau virtuel régional et ne sont pas disponibles pour les réseaux virtuels associés à des groupes d’affinités.
> 
> 

### À propos des réseaux virtuels actuellement associés à des groupes d’affinités
Les réseaux virtuels qui sont actuellement associés à des groupes d’affinités peuvent être migrés vers des réseaux virtuels régionaux. Pour la migration vers un réseau virtuel régional, procédez comme suit :

1. Exportez le fichier de configuration réseau. Vous pouvez utiliser PowerShell ou le portail de gestion. Pour obtenir des instructions sur l’utilisation du portail de gestion, consultez [Configuration de votre réseau virtuel à l’aide d’un fichier de configuration réseau](virtual-networks-using-network-configuration-file.md).
2. Modifiez votre fichier de configuration réseau, en remplaçant les anciennes valeurs par les nouvelles.
   
   > [!NOTE]
   > **Emplacement** correspond à la région que vous avez spécifiée pour le groupe d’affinités associé à votre réseau virtuel. Par exemple, si votre réseau virtuel est associé à un groupe d’affinités qui se trouve dans l’Ouest des États-Unis, votre emplacement doit pointer sur Ouest des États-Unis pendant la migration.
   > 
   > 
   
    Modifiez les lignes suivantes dans votre fichier de configuration réseau, en remplaçant les valeurs avec les vôtres :
   
    **Ancienne valeur :** \<VirtualNetworkSitename="VNetUSWest" AffinityGroup="VNetDemoAG"\>
   
    **Nouvelle valeur :** \<VirtualNetworkSitename="VNetUSWest" Location="West US"\>
3. Enregistrez vos modifications et [importez](virtual-networks-using-network-configuration-file.md) la configuration réseau dans Azure.

> [!NOTE]
> Cette migration n'entraîne PAS de temps d’arrêt de vos services.
> 
> 

## Groupe d’affinités et machines virtuelles
Comme mentionné précédemment, les groupes d’affinités ne sont plus recommandés pour les machines virtuelles. Vous devez utiliser un groupe d’affinités uniquement si un ensemble de machines virtuelles doit avoir la latence réseau la plus basse possible entre les machines virtuelles. En plaçant les machines virtuelles dans un groupe d'affinités, celles-ci sont placées dans le même cluster de calcul ou unité d'échelle.

Il est important de noter que l’utilisation d’un groupe d’affinités peut avoir deux conséquences potentiellement négatives :

* La taille de l’ensemble des machines virtuelles sera limitée à celle proposée par l’unité d’échelle de calcul.
* Il est fort probable que vous ne puissiez pas allouer de nouvelle machine virtuelle. C’est le cas quand l’unité d’échelle spécifique du groupe d’affinités se trouve hors capacité.

### Marche à suivre en cas de machine virtuelle dans un groupe d’affinités
Les machines virtuelles qui sont actuellement dans un groupe d’affinités n’ont pas besoin d’être supprimées du groupe d’affinités.

Une fois qu’une machine virtuelle est déployée, elle l’est sur une seule unité d’échelle. Les groupes d’affinités peuvent restreindre la taille disponible de l’ensemble des machines virtuelles pour un nouveau déploiement de machines virtuelles, mais les machines virtuelles existantes déployées sont déjà limitées à la taille disponible de l’ensemble des machines virtuelles dans l’unité d’échelle où elles sont déployées. Pour cette raison, la suppression d’une machine virtuelle du groupe d’affinités n’a aucun effet.

<!---HONumber=AcomDC_0810_2016-->