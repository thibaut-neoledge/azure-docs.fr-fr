---
title: "Analyser votre appareil StorSimple | Microsoft Docs"
description: "Décrit comment utiliser le service StorSimple Manager pour surveiller les performances E/S, l’utilisation de la capacité, le débit du réseau et les performances de l’appareil."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: bd4f7704-4f6f-47d0-927a-b1c91eabc453
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/16/2016
ms.author: alkohli
ms.translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d45bb37c8417785db0ea38be4375a998b6d9f109
ms.contentlocale: fr-fr
ms.lasthandoff: 11/17/2016

---
# <a name="use-the-storsimple-manager-service-to-monitor-your-storsimple-device"></a>Utilisation du service StorSimple Manager pour surveiller votre appareil StorSimple
## <a name="overview"></a>Vue d'ensemble
Vous pouvez utiliser le service StorSimple Manager pour surveiller des appareils spécifiques dans votre solution StorSimple. Vous pouvez créer des graphiques personnalisés basés sur les performances E/S, l’utilisation de la capacité, le débit du réseau et les mesures de performances de l’appareil. 

Pour afficher les informations de surveillance d’un appareil donné, sélectionnez le service StorSimple Manager sur le Portail Azure Classic. Cliquez sur l’onglet **Surveiller** et effectuez votre sélection dans la liste des appareils. La page **Surveiller** contient les informations suivantes :

## <a name="io-performance"></a>Performances E/S
**Performances E/S** : cette option assure le suivi des mesures relatives au nombre d’opérations de lecture et d’écriture entre les interfaces de l’initiateur iSCSI sur le serveur hôte et l’appareil ou entre l’appareil et le cloud. Ces performances peuvent être mesurées pour un volume spécifique, un conteneur de volumes spécifique ou tous les conteneurs de volumes.

Le tableau ci-dessous montre les E/S pour l’initiateur vers votre appareil pour tous les volumes d’un appareil de production. Les mesures de tracé sont des octets en lecture et écriture par seconde, des E/S de lecture et écriture par seconde, ainsi que des latences de lecture et écriture.

![Performances d’E/S de l’initiateur à l’appareil](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_InitiatorTODevice_For_AllVolumesM.png)

Pour le même appareil, les opérations d’E/S sont tracées pour les données de l’appareil vers le cloud pour tous les conteneurs de volumes. Sur cet appareil, les données sont uniquement dans la couche linéaire et aucune n’est dispersée dans le cloud. Aucune opération en lecture seule n’émane de l’appareil vers le cloud. Par conséquent, les pics du graphique sont séparés par un intervalle de cinq minutes, ce qui correspond à la fréquence à laquelle la pulsation est vérifiée entre l’appareil et le service. 

![Performances d’E/S de l’appareil vers le cloud](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_DeviceTOCloud_For_AllVolumeContainersM.png)

Pour le même appareil, un instantané de cloud a été pris pour les données de volume, avec une heure de début à 14 h 00. Cela a entraîné un flux de données de l’appareil vers le cloud. Des opérations de lecture-écriture ont été prises en charge pour le cloud dans cette durée. Le graphique d’E/S affiche un pic dans les différentes mesures, qui correspond à l’heure où l’instantané a été pris. 

![Performances d’E/S de l’appareil vers le cloud après l’instantané cloud](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_DeviceTOCloud_For_AllVolumeContainers2M.png)

## <a name="capacity-utilization"></a>Utilisation de la capacité
**Utilisation de la capacité** : cette option assure le suivi des mesures relatives à la quantité d’espace de stockage des données qui est utilisé par les volumes, les conteneurs de volumes ou l’appareil. Vous pouvez créer des rapports basés sur l’utilisation de la capacité du stockage principal, du stockage cloud ou du stockage de l’appareil. L’utilisation de la capacité peut être mesurée sur un volume spécifique, un conteneur de volumes spécifique ou tous les conteneurs de volumes.

La capacité de stockage principale, de cloud et de l’appareil peut être décrite comme suit :

### <a name="primary-storage-capacity-utilization"></a>Utilisation de la capacité de stockage principal
Ces graphiques affichent la quantité de données écrites sur des volumes StorSimple avant que les données soient dédupliquées et compressées. Vous pouvez afficher l’utilisation du stockage principal pour tous les volumes ou un seul volume.

Lorsque vous affichez des graphiques d’utilisation de la capacité de volume stockage principal pour tous les volumes (et non pour des volumes individuels) et exploitez les données principales dans ces deux cas, il se peut que vous détectiez une différence entre les deux nombres. Les données principales totales sur tous les volumes ne peuvent pas être ajoutées à la somme totale des données principales des volumes individuels. Cela peut être dû à l’une des opérations suivantes :

* **Données d’instantané incluses pour tous les volumes**: ce comportement se produit uniquement si vous utilisez une version antérieure à Update 3. Les données principales affichées pour tous les volumes sont la somme des données principales de chaque volume et des données d’instantané. Les données principales indiquées pour un volume donné correspondent uniquement au volume de données alloué sur le volume (et n’incluent pas le volume correspondant de données d’instantané).
  
    Cela peut également s’expliquer par l’équation suivante :
  
    *Données principales (tous les volumes) = Somme (données principales (volume i) + volume de données d’instantané (volume i))*
  
    *avec Données principales (volume i) = Taille des données principales allouées au volume i*
  
    Si les instantanés sont supprimés par le biais du service, la suppression s’effectue de façon asynchrone en arrière-plan. La mise à jour de la taille des données du volume peut prendre un certain temps après la suppression de l’instantané. 
  
    Si vous utilisez Update 3 ou une version ultérieure, les données d’instantané ne sont pas incluses dans le volume de données. Et l’utilisation principale est calculée comme suit :
  
    Données principales (tous les volumes) = Somme (données principales (volume i)
  
    *avec Données principales (volume i) = Taille des données principales allouées au volume i*
* **Volumes avec l’analyse désactivée inclus dans tous les volumes**: si vous avez des volumes sur votre périphérique pour lequel la surveillance est désactivée, les données de surveillance pour ces volumes individuels ne seront pas disponibles dans les graphiques. Toutefois, les données de tous les volumes du graphique incluent les volumes pour lesquels la surveillance est désactivée. 
* **Volumes supprimés avec des sauvegardes associées en continu inclus pour tous les volumes**: si les volumes contenant des données d’instantané sont supprimés alors que les instantanés associés existent toujours, vous pouvez détecter une différence.
* **Volumes supprimés inclus pour tous les volumes**: dans certains cas, les anciens volumes peuvent exister même si ceux-ci ont été supprimés. L’effet de suppression n’est pas visible et l’appareil peut afficher moins de capacité disponible. Vous devez contacter le Support Microsoft pour supprimer ces volumes.

Les graphiques suivants montrent l’utilisation de la capacité de stockage principale d’un appareil StorSimple avant et après un instantané cloud. Étant donné qu’il s’agit uniquement des données de volumes, un instantané cloud ne doit pas modifier le stockage principal. Comme vous pouvez le voir, le graphique n’affiche aucune différence dans l’utilisation de la capacité principale suite à un instantané cloud. L’instantané cloud a démarré à environ 14 h 00 sur l’appareil.

![Utilisation de la capacité principale avant l’instantané cloud](./media/storsimple-monitor-device/StorSimple_PrimaryCapacityUtil_For_AllVolumes2M.png)

![Utilisation de la capacité principale après l’instantané cloud](./media/storsimple-monitor-device/StorSimple_PrimaryCapacityUtil_For_AllVolumes1M.png)

Si vous exécutez Update 2 ou une version ultérieure, vous pouvez diviser l’utilisation de la capacité de stockage principale (un seul volume, tous les volumes, tous les volumes hiérarchisés ou tous les volumes épinglés localement) comme indiqué ci-dessous. Le fait de diviser la capacité de stockage entre tous les volumes épinglés localement vous permet de déterminer rapidement le niveau d’utilisation d’une couche locale.

![Utilisation de la capacité principale pour tous les volumes épinglés localement](./media/storsimple-monitor-device/localvolumes.png)

### <a name="cloud-storage-capacity-utilization"></a>Utilisation de la capacité de stockage dans le cloud
Ces graphiques montrent la quantité de stockage cloud utilisé. Les données sont dédupliquées et compressées. Cette quantité inclut les instantanés cloud qui peuvent contenir des données qui ne sont pas reflétées dans un volume principal et qui sont conservées à des fins de rétention obligatoire ou héritée. Vous pouvez comparer les chiffres de la consommation du stockage principal et du stockage cloud pour obtenir une idée du taux de réduction des données, même si le chiffre ne sera pas exact. Les graphiques suivants montrent l’utilisation de la capacité de stockage cloud d’un appareil StorSimple avant et après un instantané cloud. L’instantané cloud a démarré à environ 14 h 00 sur l’appareil. Vous pouvez constater que la capacité du cloud a augmenté au même moment, passant de 5,73 Mo à 4,04 Go.

![Utilisation de la capacité cloud avant l’instantané cloud](./media/storsimple-monitor-device/StorSimple_CloudCapacityUtil_For_AllVolumeContainers2M.png)

![Utilisation de la capacité cloud après l’instantané cloud](./media/storsimple-monitor-device/StorSimple_CloudCapacityUtil_For_AllVolumeContainers1M.png)

### <a name="device-storage-capacity-utilization"></a>Utilisation de la capacité de stockage de l’appareil
Ces graphiques affichent l’utilisation totale pour l’appareil. Celle-ci est supérieure à l’utilisation du stockage principal, car elle inclut le niveau linéaire SSD. Ce niveau contient une quantité de données qui existe également sur les autres niveaux de l’appareil. La capacité du niveau linéaire SSD est cyclique, de sorte que lors de la réception de nouvelles données, les anciennes données sont déplacées vers le niveau HDD (elles sont alors dédupliquées et compressées).

Au fil du temps, l’utilisation de la capacité principale et l’utilisation de la capacité de l’appareil augmenteront en parallèle jusqu’à ce que les données commencent à être transférées vers le cloud. À ce stade, l’utilisation de la capacité de l’appareil commencera sans doute à stagner, mais l’utilisation de la capacité principale augmentera à mesure que davantage de données sont écrites.

Les graphiques suivants montrent l’utilisation de la capacité de stockage principale d’un appareil StorSimple avant et après un instantané cloud. L’instantané cloud a démarré à 14 h 00 et la capacité de l’appareil a commencé à diminuer à ce moment-là. L’utilisation de capacité de stockage de l’appareil a diminué, passant de 11,58 Go à 7,48 Go. Cela indique très probablement que des données non compressées au niveau linéaire SSD ont été dédupliquées, compressées et déplacées vers le niveau de disque dur. Notez que si l’appareil possède déjà une grande quantité de données dans les niveaux de SSD et de disque dur, vous ne verrez pas cette diminution. Dans cet exemple, l’appareil a une petite quantité de données.

![Utilisation de la capacité de l’appareil avant l’instantané cloud](./media/storsimple-monitor-device/StorSimple_DeviceCapacityUtil2M.png)

![Utilisation de la capacité de l’appareil après l’instantané cloud](./media/storsimple-monitor-device/StorSimple_DeviceCapacityUtil1M.png)

## <a name="network-throughput"></a>Débit du réseau
**Débit du réseau** : cette option assure le suivi des mesures relatives à la quantité de données transférées entre les interfaces réseau de l’initiateur iSCSI sur le serveur hôte et l’appareil, et entre l’appareil et le cloud. Vous pouvez surveiller cette mesure pour chacune des interfaces réseau iSCSI sur votre appareil.

Les graphiques suivants montrent le débit du réseau pour Data 0 et Data 4, deux interfaces de réseau 1GbE sur votre appareil. Dans le cas présent, le cloud a été activé pour Data 0, et iSCSI a été activé pour Data 4. Vous pouvez voir le trafic entrant et sortant pour votre appareil StorSimple. La ligne plate dans le graphique à partir de 15 h 24 est due au fait que nous ne collectons des données que toutes les cinq minutes et que celle-ci doit être ignorée. 

![Débit du réseau pour Data 4](./media/storsimple-monitor-device/StorSimple_NetworkThroughput_Data0M.png)

![Débit du réseau pour Data 4](./media/storsimple-monitor-device/StorSimple_NetworkThroughput_Data4M.png)

## <a name="device-performance"></a>Performances de l’appareil
**Performances de l’appareil** : cette option assure le suivi des métriques relatives aux performances de votre appareil. Le tableau suivant affiche les statistiques d’utilisation du processeur pour un appareil en production.

![Utilisation du processeur pour l’appareil](./media/storsimple-monitor-device/StorSimple_DeviceMonitor_DevicePerformance1M.png)

## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment [utiliser le tableau de bord de l’appareil du service StorSimple Manager](storsimple-device-dashboard.md).
* Découvrez comment [utiliser le service StorSimple Manager pour gérer votre appareil StorSimple](storsimple-manager-service-administration.md).


