<properties 
   pageTitle="Limites du système StorSimple | Microsoft Azure"
   description="Décrit les limites du système et les tailles recommandées pour les connexions et les composants StorSimple série 8000."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="02/17/2016"
   ms.author="alkohli" />

# Limites du système StorSimple

## Vue d'ensemble

StorSimple fournit un stockage évolutif et flexible pour votre centre de données. Toutefois, il existe certaines limites dont vous devez tenir compte lorsque vous planifiez, déployez et exploitez votre solution StorSimple. Le tableau suivant décrit ces limites et fournit quelques recommandations afin que vous puissiez tirer parti de votre solution StorSimple.

| Identificateur de la limite | Limite | Commentaires |
|----------------- | ------|--------- |
| Nombre maximal d'informations d'identification du compte de stockage | 64 | |
| Nombre maximal de conteneurs de volumes | 64 | |
| Nombre maximal de volumes | 255 | |
| Nombre maximal de planifications par modèle de bande passante | 168 | Une planification par heure, tous les jours de la semaine (24*7). |
| Taille maximale d'un volume à plusieurs niveaux sur les appareils physiques | 64 To pour 8100 et 8600 | 8100 et 8600 sont des appareils physiques. |
| Taille maximale d'un volume à plusieurs niveaux sur les appareils virtuels dans Azure | 30 To pour 8010 <br></br> 64 To pour 8020 | 8010 et 8020 sont des appareils virtuels dans Azure qui utilisent le stockage Standard et Premium respectivement. |
| Taille maximale d'un volume épinglé localement sur les appareils physiques | 9 To pour 8100 <br></br> 24 To pour 8600 | 8100 et 8600 sont des appareils physiques. |
| Nombre maximal de connexions iSCSI | 512 | |
| Nombre maximal de connexions iSCSI à partir d'initiateurs | 512 | |
| Nombre maximal d'enregistrements de contrôle d'accès par appareil | 64 | |
| Nombre maximal de volumes par stratégie de sauvegarde | 24 | |
| Nombre maximal de sauvegardes conservées par planification (dans une stratégie de sauvegarde) | 64 | |
| Nombre maximal de planifications par stratégie de sauvegarde | 10 | |
| Nombre maximal d'instantanés de n'importe quel type pouvant être conservés par volume | 256 | Cela inclut les instantanés locaux et les instantanés cloud. |
| Nombre maximal d'instantanés pouvant être présents dans n'importe quel appareil | 10 000 | |
| Nombre maximal de volumes pouvant être traités en parallèle pour sauvegarder, restaurer ou cloner | 16 |<ul><li>S'il existe plus de 16 volumes, ils seront traités séquentiellement au fur et à mesure que des emplacements de traitement se libèrent.</li><li>Il n'est pas possible de générer de nouvelles sauvegardes d'un volume à plusieurs niveaux cloné ou restauré tant que l'opération n'est pas terminée. Toutefois, pour un volume local, les sauvegardes sont autorisées une fois que le volume est en ligne.</li></ul>|
| Temps de récupération d'un clone et d'une restauration pour les volumes à plusieurs niveaux | < 2 minutes | <ul><li>Le volume sera disponible dans les 2 minutes suivant l’opération de restauration ou de clonage, quelle que soit la taille du volume.</li><li>Les performances du volume peuvent initialement être inférieures à la normale, car la plupart des données et des métadonnées se trouvent toujours dans le cloud. Les performances vont augmenter au fur et à mesure que les données provenant du cloud se dirigent vers l’appareil StorSimple.</li><li>La durée totale de téléchargement des métadonnées dépend de la taille du volume alloué. Les métadonnées sont automatiquement intégrées à l’appareil en arrière-plan, à une vitesse de 5 minutes par To de données du volume alloué. Cette vitesse peut être affectée par la bande passante Internet vers le cloud.</li><li>L'opération de restauration ou de clonage est terminée lorsque toutes les métadonnées se trouvent dans l’appareil.</li><li>Impossible d’effectuer des opérations de sauvegarde tant que la restauration ou le clonage n’est pas totalement terminé(e).|
| Temps de récupération d'une restauration pour les volumes épinglés localement | < 2 minutes | <ul><li>Le volume sera disponible dans les 2 minutes suivant l'opération de restauration, quelle que soit la taille du volume.</li><li>Les performances du volume peuvent initialement être inférieures à la normale, car la plupart des données et des métadonnées se trouvent toujours dans le cloud. Les performances vont augmenter au fur et à mesure que les données provenant du cloud se dirigent vers l’appareil StorSimple.</li><li>La durée totale de téléchargement des métadonnées dépend de la taille du volume alloué. Les métadonnées sont automatiquement intégrées à l’appareil en arrière-plan, à une vitesse de 5 minutes par To de données du volume alloué. Cette vitesse peut être affectée par la bande passante Internet vers le cloud.</li><li>Contrairement aux volumes à plusieurs niveaux, dans le cas des volumes épinglés localement, les données du volume sont également téléchargées localement sur l'appareil. L'opération de restauration est terminée lorsque toutes les données du volume ont été transférées vers l'appareil.</li><li>Les opérations de restauration peuvent être longues et la durée totale de restauration dépend de la taille du volume local configuré, de votre bande passante Internet et des données existantes sur l'appareil. Les opérations de sauvegarde sur le volume épinglé localement sont autorisées pendant que l'opération de restauration est en cours.|
|Vitesse de traitement des instantanés cloud| 15 minutes/To | <ul><li>Durée minimale nécessaire pour que l’instantané cloud soit prêt pour le chargement (par téraoctet de données d’un volume alloué dans une sauvegarde). </li><li> La durée totale d’un instantané cloud est calculée en ajoutant cette durée à la durée de chargement de l’instantané, qui est affectée par la bande passante Internet vers le cloud.
| Débit maximal de lecture/d’écriture du client (si traité à partir du niveau SSD)* | 920/720 Mo/s avec une seule interface réseau de 10 Gigabit Ethernet | Jusqu'à 2x avec MPIO et deux interfaces réseau. |
| Débit maximal de lecture/d’écriture du client (si traité à partir du niveau HDD)* | 120/250 Mo/s |
| Débit maximal de lecture/d’écriture du client (si traité à partir du niveau cloud)* | 11/41 Mo/s | Le débit de lecture dépend de si les clients génèrent et conservent une profondeur de file d’attente d’E/S suffisante. |

&#42; Le débit maximal par type d’E/S a été mesuré avec des scénarios de lecture et d’écriture de 100 pour cent. Le débit réel peut être inférieur et varie en fonction du mélange d’E/S et des conditions réseau.

## Étapes suivantes

Relisez les [Conditions requises pour le système StorSimple](storsimple-system-requirements.md).

<!---HONumber=AcomDC_0218_2016-->