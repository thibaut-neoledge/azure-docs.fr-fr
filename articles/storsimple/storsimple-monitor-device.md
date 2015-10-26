<properties 
   pageTitle="Analyser votre appareil StorSimple | Microsoft Azure"
   description="Décrit comment utiliser le service StorSimple Manager pour surveiller les performances E/S, l'utilisation de la capacité, le débit du réseau et les performances de l'appareil."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="09/15/2015"
   ms.author="alkohli" />

# Utilisation du service StorSimple Manager pour surveiller votre appareil StorSimple 

## Vue d'ensemble

Vous pouvez utiliser le service StorSimple Manager pour surveiller des appareils spécifiques dans votre solution StorSimple. Vous pouvez créer des graphiques personnalisés basés sur les performances E/S, l'utilisation de la capacité, le débit du réseau et les mesures de performances de l'appareil.

Pour afficher les informations de surveillance pour un appareil spécifique, dans le portail Azure, sélectionnez le service StorSimple Manager, cliquez sur l'onglet **Surveiller** et effectuez votre sélection dans la liste des appareils. La page **Surveiller** contient les informations suivantes :

## Performances E/S 

**Performances E/S** : cette option assure le suivi des mesures relatives au nombre d'opérations de lecture et d'écriture entre les interfaces de l'initiateur iSCSI sur le serveur hôte et l'appareil ou entre l'appareil et le cloud. Ces performances peuvent être mesurées pour un volume spécifique, un conteneur de volumes spécifique ou tous les conteneurs de volumes.

Le tableau ci-dessous montre les E/S pour l'initiateur vers votre appareil pour tous les volumes d'un appareil de production. Les mesures de tracé sont des octets en lecture et écriture par seconde, des E/S de lecture et écriture par seconde, ainsi que des latences de lecture et écriture.

![Performances d'E/S de l'initiateur à l’appareil](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_InitiatorTODevice_For_AllVolumesM.png)

Pour le même appareil, les E/S sont tracées pour les données de l’appareil vers le cloud pour tous les conteneurs de volume. Sur cet appareil, les données sont uniquement dans la couche linéaire et aucune n’est dispersée dans le cloud. Aucune opération en lecture seule n’émane de l’appareil vers le nuage. Par conséquent, les pics du graphique sont à un intervalle de 5 minutes, ce qui correspond à la fréquence à laquelle la pulsation est vérifiée entre le périphérique et le service.

![Performances d'E/S de l’appareil vers le cloud](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_DeviceTOCloud_For_AllVolumeContainersM.png)


Pour le même appareil, un instantané de cloud a été pris pour les données de volume, avec une heure de début à 14 h 00. Cela a entraîné un flux de données de l’appareil vers le cloud. Des opérations de lecture-écriture ont été prises en charge pour le cloud dans cette durée. Le graphique d'E/S affiche un pic dans les différentes mesures, qui correspond à l'heure où l'instantané a été pris.

![Performances d'E/S de l’appareil vers le cloud après l'instantané cloud](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_DeviceTOCloud_For_AllVolumeContainers2M.png)


## Utilisation de la capacité 

**Utilisation de la capacité** : cette option assure le suivi des mesures relatives à la quantité d'espace de stockage des données qui est utilisé par les volumes, les conteneurs de volumes ou l'appareil. Vous pouvez créer des rapports basés sur l'utilisation de la capacité du stockage principal, du stockage cloud ou du stockage de l'appareil. L'utilisation de la capacité peut être mesurée sur un volume spécifique, un conteneur de volumes spécifique ou tous les conteneurs de volumes.

La capacité de stockage principale, de cloud et de l’appareil peut être décrite comme suit :

- **Utilisation de la capacité du stockage principal** : cette option affiche la quantité de données écrites sur les volumes StorSimple avant que les données ne soient dédupliquées et compressées. Les graphiques suivants montrent l'utilisation de la capacité de stockage principale d'un appareil StorSimple avant et après un instantané cloud. Étant donné qu'il s'agit uniquement des données de volume, un instantané cloud ne doit pas modifier le stockage principal. Comme vous pouvez le voir, le graphique n'affiche aucune différence dans l'utilisation de la capacité principale suite à un instantané cloud. Notez que l'instantané cloud a démarré à environ 14 h 00 sur l'appareil.

	![Utilisation de la capacité principale avant l'instantané cloud](./media/storsimple-monitor-device/StorSimple_PrimaryCapacityUtil_For_AllVolumes2M.png)
	
	![Utilisation de la capacité principale après l'instantané cloud](./media/storsimple-monitor-device/StorSimple_PrimaryCapacityUtil_For_AllVolumes1M.png)


- **Utilisation de la capacité du stockage cloud** : cette option affiche la quantité de stockage cloud utilisé. Les données sont dédupliquées et compressées. Cette quantité inclut les instantanés cloud qui peuvent contenir des données qui ne sont pas reflétées dans un volume principal et qui sont conservées à des fins de rétention obligatoire ou héritée. Vous pouvez comparer les chiffres de la consommation du stockage principal et du stockage cloud pour obtenir une idée du taux de réduction des données, même si le chiffre ne sera pas exact. Les graphiques suivants montrent l'utilisation de la capacité de stockage cloud d'un appareil StorSimple avant et après un instantané cloud. L'instantané cloud a démarré à environ 14 h 00 sur l'appareil. Vous pouvez constater que la capacité du cloud a augmenté au même moment, passant de 5,73 Mo à 4,04 Go.

	![Utilisation de la capacité cloud avant l'instantané cloud](./media/storsimple-monitor-device/StorSimple_CloudCapacityUtil_For_AllVolumeContainers2M.png)

	![Utilisation de la capacité cloud après l'instantané cloud](./media/storsimple-monitor-device/StorSimple_CloudCapacityUtil_For_AllVolumeContainers1M.png)


- **Utilisation de la capacité de stockage de l'appareil** : cette option affiche l'utilisation totale pour l'appareil, qui sera supérieure à l'utilisation du stockage principal car elle inclut le niveau linéaire SSD. Ce niveau contient une quantité de données qui existe également sur les autres niveaux de l’appareil. La capacité du niveau linéaire SSD est cyclique, de sorte que lors de la réception de nouvelles données, les anciennes données sont déplacées vers le niveau HDD (elles sont alors dédupliquées et compressées).

	Au fil du temps, l'utilisation de la capacité principale et l'utilisation de la capacité de l'appareil augmenteront en parallèle jusqu'à ce que les données commencent à être transférées vers le cloud. À ce stade, l'utilisation de la capacité de l'appareil commencera sans doute à stagner, mais l'utilisation de la capacité principale augmentera à mesure que davantage de données sont écrites.

	Les graphiques suivants montrent l'utilisation de la capacité de stockage principale d'un appareil StorSimple avant et après un instantané cloud. L'instantané cloud a démarré à 14 h 00 et la capacité du périphérique a commencé à diminuer à ce moment-là. L'utilisation de capacité de stockage de l’appareil a diminué, passant de 11,58 Go à 7,48 Go. Cela indique très probablement que des données non compressées au niveau linéaire SSD ont été dédupliquées, compressées et déplacées vers le niveau de disque dur. Notez que si l’appareil possède déjà une grande quantité de données dans les niveaux de SSD et de disque dur, vous ne verrez pas cette diminution. Dans cet exemple, l’appareil a une petite quantité de données.

	![Utilisation de la capacité de l’appareil avant l'instantané cloud](./media/storsimple-monitor-device/StorSimple_DeviceCapacityUtil2M.png)

	![Utilisation de la capacité de l’appareil après l'instantané cloud](./media/storsimple-monitor-device/StorSimple_DeviceCapacityUtil1M.png)


## Débit du réseau

**Débit du réseau** : cette option assure le suivi des mesures relatives à la quantité de données transférées entre les interfaces réseau de l'initiateur iSCSI sur le serveur hôte et l'appareil, et entre l'appareil et le cloud. Vous pouvez surveiller cette mesure pour chacune des interfaces réseau iSCSI sur votre appareil.

Les graphiques ci-dessous montrent le débit du réseau pour Data 0 et Data 4, deux interfaces de réseau 1 Gigabit Ethernet sur votre appareil. Dans le cas présent, le cloud a été activé pour Data 0, et iSCSI a été activé pour Data 4. Vous pouvez voir le trafic entrant et sortant pour votre périphérique StorSimple. Notez que la ligne plate dans le graphique à partir de 15 h 24 est due au fait que nous ne collectons des données que toutes les 5 minutes et que celle-ci doit être ignorée.

![Débit du réseau pour Data 4](./media/storsimple-monitor-device/StorSimple_NetworkThroughput_Data0M.png)

![Débit du réseau pour Data 4](./media/storsimple-monitor-device/StorSimple_NetworkThroughput_Data4M.png)


## Performances de l'appareil 

**Performances de l'appareil** : cette option assure le suivi des mesures relatives aux performances de votre appareil. Le tableau ci-dessous affiche les statistiques d'utilisation du processeur pour un appareil en production.

![Utilisation du processeur pour l’appareil](./media/storsimple-monitor-device/StorSimple_DeviceMonitor_DevicePerformance1M.png)

## Étapes suivantes

- Découvrez comment [utiliser le tableau de bord de l’appareil du service StorSimple Manager](storsimple-device-dashboard.md).

- Découvrez comment [utiliser le service StorSimple Manager pour gérer votre appareil StorSimple](storsimple-manager-service-administration.md).

<!---HONumber=Oct15_HO3-->