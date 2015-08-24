<properties 
   pageTitle="Utilisation du service StorSimple Manager pour surveiller votre appareil StorSimple | Microsoft Azure"
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
   ms.date="08/10/2015"
   ms.author="alkohli" />

# Utilisation du service StorSimple Manager pour surveiller votre appareil StorSimple 

## Vue d'ensemble

Vous pouvez utiliser le service StorSimple Manager pour surveiller des appareils spécifiques dans votre solution StorSimple. Vous pouvez créer des graphiques personnalisés basés sur les performances E/S, l'utilisation de la capacité, le débit du réseau et les mesures de performances de l'appareil.

Pour afficher les informations de surveillance pour un appareil spécifique, dans le portail de gestion, sélectionnez le service StorSimple Manager, cliquez sur l'onglet **Surveiller** et effectuez votre sélection dans la liste des appareils. La page **Surveiller** contient les informations suivantes :

## Performances E/S 

**Performances E/S** : cette option assure le suivi des mesures relatives au nombre d'opérations de lecture et d'écriture entre les interfaces de l'initiateur iSCSI sur le serveur hôte et l'appareil ou entre l'appareil et le cloud. Ces performances peuvent être mesurées pour un volume spécifique, un conteneur de volumes spécifique ou tous les conteneurs de volumes.

## Utilisation de la capacité 

**Utilisation de la capacité** : cette option assure le suivi des mesures relatives à la quantité d'espace de stockage des données qui est utilisé par les volumes, les conteneurs de volumes ou l'appareil. Vous pouvez créer des rapports basés sur l'utilisation de la capacité du stockage principal, du stockage cloud ou du stockage de l'appareil. L'utilisation de la capacité peut être mesurée sur un volume spécifique, un conteneur de volumes spécifique ou tous les conteneurs de volumes.

- **Utilisation de la capacité du stockage principal** : cette option affiche la quantité de données écrites sur les volumes StorSimple avant que les données ne soient dédupliquées et compressées.

- **Utilisation de la capacité du stockage cloud** : cette option affiche la quantité de stockage cloud utilisé. Les données sont dédupliquées et compressées. Cette quantité inclut les instantanés cloud qui peuvent contenir des données qui ne sont pas reflétées dans un volume principal et qui sont conservées à des fins de rétention obligatoire ou héritée. Vous pouvez comparer les chiffres de la consommation du stockage principal et du stockage cloud pour obtenir une idée du taux de réduction des données, même si le chiffre ne sera pas exact.

- **Utilisation de la capacité de stockage de l'appareil** : cette option affiche l'utilisation totale pour l'appareil, qui sera supérieure à l'utilisation du stockage principal car elle inclut le niveau linéaire SSD. Ce niveau contient une quantité de données qui existe également sur les autres niveaux de l'appareil. La capacité du niveau linéaire SSD est cyclique, de sorte que lors de la réception de nouvelles données, les anciennes données sont déplacées vers le cloud (elles sont alors dédupliquées et compressées).

Au fil du temps, l'utilisation de la capacité principale et l'utilisation de la capacité de l'appareil augmenteront en parallèle jusqu'à ce que les données commencent à être transférées vers le cloud. À ce stade, l'utilisation de la capacité de l'appareil commencera sans doute à stagner, mais l'utilisation de la capacité principale augmentera à mesure que davantage de données sont écrites.

## Débit du réseau

**Débit du réseau** : cette option assure le suivi des mesures relatives à la quantité de données transférées entre les interfaces réseau de l'initiateur iSCSI sur le serveur hôte et l'appareil, et entre l'appareil et le cloud. Vous pouvez surveiller cette mesure pour chacune des interfaces réseau iSCSI sur votre appareil.

## Performances de l'appareil 

**Performances de l'appareil** : cette option assure le suivi des mesures relatives aux performances de votre appareil.

## Étapes suivantes

[Découvrez comment utiliser le tableau de bord d'appareil du service StorSimple Manager](storsimple-device-dashboard.md).

<!---HONumber=August15_HO7-->