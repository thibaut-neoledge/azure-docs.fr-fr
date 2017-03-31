---
title: "Mise à l’échelle automatique d’un service cloud dans le portail Classic | Microsoft Docs"
description: "(Classic) Découvrez comment utiliser le portail Classic pour configurer des règles de mise à l’échelle automatique pour le rôle web ou de travail d’un service cloud dans Azure."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: eb167d70-4eba-42a4-b157-d8d0688abf4b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 488b16623300d80a53a61badee420de106c418b0
ms.lasthandoff: 03/25/2017

---

# <a name="how-to-configure-auto-scaling-for-a-cloud-service-in-the-classic-portal"></a>Configuration de la mise à l’échelle automatique d’un service cloud dans le portail Classic
> [!div class="op_single_selector"]
> * [portail Azure](cloud-services-how-to-scale-portal.md)
> * [Portail Azure Classic](cloud-services-how-to-scale.md)

Sur la page de mise à l’échelle du portail Azure Classic, vous pouvez configurer les paramètres de mise à l’échelle automatique pour votre rôle web ou rôle de travail. Vous pouvez également configurer une mise à l’échelle manuelle au lieu d’une mise à l’échelle automatique basée sur des règles.

> [!NOTE]
> Cet article porte essentiellement sur les rôles web et de travail d’un service cloud. Lorsque vous créez directement une machine virtuelle (Classic), elle est hébergée dans un service cloud. Certaines de ces informations s’appliquent à ces types de machines virtuelles. La mise à l’échelle d’un groupe à haute disponibilité de machines virtuelles consiste simplement à les démarrer et à les arrêter selon les règles de mise à l’échelle que vous configurez. Pour plus d’informations sur les machines virtuelles et les groupes à haute disponibilité, consultez [Gérer la disponibilité des machines virtuelles](../virtual-machines/windows/classic/configure-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

Vous devez tenir compte des informations suivantes avant de configurer la mise à l'échelle de votre application :

* L'utilisation des cœurs a une incidence sur la mise à l'échelle.

    Le nombre de cœurs utilisés varie en fonction de la taille des instances de rôle. Vous pouvez mettre à l’échelle une application uniquement dans la limite des cœurs de votre abonnement. Par exemple, si la limite de votre abonnement est de 20 cœurs. Si vous exécutez une application avec deux services cloud de taille moyenne (4 cœurs au total), vous pouvez seulement faire monter en charge les autres déploiements de service cloud de votre abonnement des 16 cœurs restants. Pour plus d’informations sur les tailles, consultez [Tailles de services cloud](cloud-services-sizes-specs.md).

* Vous devez créer une file d’attente et l’associer à un rôle avant de pouvoir mettre à l’échelle une application en fonction d’un seuil de messages. Pour plus d'informations, consultez la page [Utilisation du service de stockage de file d'attente](../storage/storage-dotnet-how-to-use-queues.md).

* Vous pouvez mettre à l'échelle des ressources qui sont liées à votre service cloud. Pour plus d’informations sur la liaison des ressources, consultez la rubrique [Liaison d’une ressource à un service cloud](cloud-services-how-to-manage.md#how-to-link-a-resource-to-a-cloud-service).

* Pour activer la fonction de haute disponibilité de votre application, vous devez vous assurer qu’elle est déployée avec plusieurs instances de rôle. Pour plus d'informations, consultez la page [Contrats de niveau de service](https://azure.microsoft.com/support/legal/sla/).

> [!WARNING]
> La mise à l’échelle automatique ne fonctionne qu’avec les comptes de stockage Azure Classic. Elle ne fonctionne pas avec les comptes de stockage Azure Resource Manager.

## <a name="schedule-scaling"></a>Planifier la mise à l'échelle
Par défaut, tous les rôles ne suivent pas une planification spécifique. Par conséquent, les paramètres modifiés s’appliquent à toutes les heures et à tous les jours de l’année. Si vous le souhaitez, vous pouvez configurer une mise à l’échelle manuelle ou automatique pour un des modes suivants :

* Les jours de la semaine
* Les week-ends
* Les nuits de la semaine
* Les matinées de la semaine
* Des dates spécifiques
* Des plages de dates spécifiques

Le paramètre de planification est configuré dans le [Portail Azure Classic](https://manage.windowsazure.com/) à la page  
**Services cloud** > **\[Votre service cloud\]** > **Mise à l’échelle** > **\[Production ou intermédiaire\]**.

Cliquez sur le bouton **Configurer des heures de planification** pour chaque rôle à modifier.

![Mise à l’échelle automatique d’un service cloud en fonction d’une planification][scale_schedules]

## <a name="manual-scale"></a>Mise à l’échelle manuelle
Dans la page **Mettre à l’échelle** , vous pouvez augmenter ou diminuer manuellement le nombre d’instances s’exécutant dans un service cloud. Cette configuration s’applique à chaque planification que vous avez créée ou tout le temps si vous n’en avez pas créé.

1. Dans le [portail Azure Classic](https://manage.windowsazure.com/), cliquez sur **Cloud Services**, puis sur le nom du service cloud pour ouvrir le tableau de bord.
   
   > [!TIP]
   > Si votre service cloud n’y figure pas, vous devrez peut-être passer de **Production** à **Intermédiaire** ou vice versa.

2. Cliquez sur **Scale**.
3. Sélectionnez la planification dont vous voulez modifier les options de mise à l’échelle. La valeur par défaut est *Aucune heure planifiée* si vous n’avez pas défini de planification.
4. Recherchez la section **Mettre à l’échelle par métrique**, puis sélectionnez **AUCUN**. Il s’agit du paramètre par défaut pour tous les rôles.
5. Chaque rôle dans le service cloud est doté d'un curseur permettant de modifier le nombre d'instances à utiliser.
   
    ![Mise à l’échelle manuelle d’un rôle de service cloud][manual_scale]
   
    Si vous avez besoin d’instances supplémentaires, vous devrez peut-être modifier la [taille des machines virtuelles du service cloud](cloud-services-sizes-specs.md).
6. Cliquez sur **Save**.  
   Les instances de rôle sont ajoutées ou supprimées en fonction de vos sélections.

> [!TIP]
> Chaque fois que l’icône ![][tip_icon] apparaît, pointez dessus avec le curseur de la souris pour obtenir de l’aide concernant la fonction d’un paramètre spécifique.

## <a name="automatic-scale---cpu"></a>Mise à l’échelle automatique - UC
Une mise à l’échelle se produit pour ce mode si le pourcentage moyen d’utilisation de l’unité centrale est supérieur ou inférieur aux seuils spécifiés. Dans ce cas, des instances de rôle sont créées ou supprimées.

1. Dans le [portail Azure Classic](https://manage.windowsazure.com/), cliquez sur **Cloud Services**, puis sur le nom du service cloud pour ouvrir le tableau de bord.
   
   > [!TIP]
   > Si votre service cloud n’y figure pas, vous devrez peut-être passer de **Production** à **Intermédiaire** ou vice versa.

2. Cliquez sur **Scale**.
3. Sélectionnez la planification dont vous voulez modifier les options de mise à l’échelle. La valeur par défaut est *Aucune heure planifiée* si vous n’avez pas défini de planification.
4. Recherchez la section **Mettre à l’échelle par métrique**, puis sélectionnez **UC**.
5. Vous pouvez maintenant configurer une plage minimale et maximale d’instances de rôles, l’utilisation d’unité centrale cible (pour déclencher une montée en charge) et le nombre d’instances devant faire l’objet d’une montée ou d’une descente en charge.

![Mise à l’échelle d’un rôle de service cloud par charge d’UC][cpu_scale]

> [!TIP]
> Chaque fois que l’icône ![][tip_icon] apparaît, pointez dessus avec le curseur de la souris pour obtenir de l’aide concernant la fonction d’un paramètre spécifique.

## <a name="automatic-scale---queue"></a>Mise à l’échelle automatique - File d’attente
Une mise à l’échelle automatique se produit dans ce mode si le nombre de messages dans une file d’attente monte ou descend au-delà d’un seuil spécifié. Dans ce cas, des instances de rôle sont créées ou supprimées.

1. Dans le [portail Azure Classic](https://manage.windowsazure.com/), cliquez sur **Cloud Services**, puis sur le nom du service cloud pour ouvrir le tableau de bord.
   
   > [!TIP]
   > Si votre service cloud n’y figure pas, vous devrez peut-être passer de **Production** à **Intermédiaire** ou vice versa.

2. Cliquez sur **Scale**.
3. Recherchez la section **Mettre à l’échelle par métrique**, puis sélectionnez **QUEUE**.
4. Vous pouvez maintenant configurer une plage minimale et maximale d’instances de rôles, la file d’attente et le nombre de messages de file d’attente à traiter pour chaque instance, ainsi que le nombre d’instances devant faire l’objet d’une montée ou d’une descente en charge.

![Mise à l’échelle d’un rôle de service cloud par une file d’attente de messages][queue_scale]

> [!TIP]
> Chaque fois que l’icône ![][tip_icon] apparaît, pointez dessus avec le curseur de la souris pour obtenir de l’aide concernant la fonction d’un paramètre spécifique.

## <a name="scale-linked-resources"></a>Mise à l'échelle des ressources liées
Lorsque vous mettez à l'échelle un rôle, il est souvent avantageux de mettre également à l'échelle la base de données qui est utilisée par l'application. Si vous liez la base de données au service cloud, vous pouvez accéder aux paramètres de mise à l’échelle de cette ressource en cliquant sur le lien approprié.

1. Dans le [portail Azure Classic](https://manage.windowsazure.com/), cliquez sur **Cloud Services**, puis sur le nom du service cloud pour ouvrir le tableau de bord.
   
   > [!TIP]
   > Si votre service cloud n’y figure pas, vous devrez peut-être passer de **Production** à **Intermédiaire** ou vice versa.

2. Cliquez sur **Scale**.
3. Recherchez la section **Ressources liées** et cliquez sur **Gérer la mise à l’échelle pour cette base de données**.
   
   > [!NOTE]
   > Si vous ne voyez pas de section **Ressources liées** , c’est que vous n’avez probablement pas de ressources liées.

![][linked_resource]

[manual_scale]: ./media/cloud-services-how-to-scale/manual-scale.png
[queue_scale]: ./media/cloud-services-how-to-scale/queue-scale.png
[cpu_scale]: ./media/cloud-services-how-to-scale/cpu-scale.png
[tip_icon]: ./media/cloud-services-how-to-scale/tip.png
[scale_schedules]: ./media/cloud-services-how-to-scale/schedules.png
[scale_popup]: ./media/cloud-services-how-to-scale/schedules-dialog.png
[linked_resource]: ./media/cloud-services-how-to-scale/linked-resources.png

