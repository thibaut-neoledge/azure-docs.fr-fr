<properties 
   pageTitle="Limites du système StorSimple"
   description="Décrit les limites du système et les tailles recommandées pour les connexions et les composants de StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="06/18/2015"
   ms.author="alkohli" />

# Limites du système StorSimple

## Vue d'ensemble

StorSimple fournit un stockage évolutif et flexible pour votre centre de données. Toutefois, il existe certaines limites dont vous devez tenir compte lorsque vous planifiez, déployez et exploitez votre solution StorSimple. Le tableau suivant décrit ces limites et fournit quelques recommandations afin que vous puissiez tirer parti de votre solution StorSimple.

| Identificateur de la limite | Limite | Commentaires |
|----------------- | ------|--------- |
| Nombre maximal d'informations d'identification du compte de stockage | 64 | |
| Nombre maximal de conteneurs de volumes | 64 | |
| Nombre maximal de volumes | 255 | |
| Nombre maximal de modèles de bande passante | 25 | |
| Nombre maximal de planifications par modèle de bande passante | 168 | Une planification par heure, tous les jours de la semaine (24*7). |
| Taille maximale d'un volume | 64 To | La taille recommandée pour un volume NTFS est de 64 To. |
| Nombre maximal de connexions iSCSI | 512 | |
| Nombre maximal de connexions iSCSI à partir d'initiateurs | 512 | |
| Nombre maximal d'enregistrements de contrôle d'accès par appareil | 64 | |
| Nombre maximal de volumes par stratégie de sauvegarde | 24 | |
| Nombre maximal de sauvegardes conservées par stratégie de sauvegarde | 64 | |
| Nombre maximal de planifications par stratégie de sauvegarde | 10 | |
| Nombre maximal d'instantanés de n'importe quel type pouvant être conservés par volume | 256 | Cela inclut les instantanés locaux et les instantanés cloud. |
| Nombre maximal d'instantanés pouvant être présents dans n'importe quel appareil | 10 000 | |
| Nombre maximal de volumes pouvant être traités en parallèle pour sauvegarder, restaurer ou cloner | 16 |<ul><li>S'il existe plus de 16 volumes, ils seront traités séquentiellement au fur et à mesure que des emplacements de traitement se libèrent.</li><li>Il n’est pas possible de générer de nouvelles sauvegardes d’un volume cloné ou restauré tant que l’opération n’est pas terminée.</li></ul>|
| Temps de récupération d’un clone et d’une restauration | < 2 minutes | <ul><li>Le volume sera disponible dans les 2 minutes suivant l’opération de restauration ou de clonage, quelle que soit la taille du volume.</li><li>Les performances du volume peuvent initialement être inférieures à la normale, car la plupart des données et des métadonnées se trouvent toujours dans le cloud. Les performances vont augmenter au fur et à mesure que les données provenant du cloud se dirigent vers l’appareil StorSimple.</li><li>La durée totale de téléchargement des métadonnées dépend de la taille du volume alloué. Les métadonnées sont automatiquement intégrées à l’appareil en arrière-plan, à une vitesse de 5 minutes par To de données du volume alloué. Cette vitesse peut être affectée par la bande passante Internet vers le cloud.</li><li>L'opération de restauration ou de clonage est terminée lorsque toutes les métadonnées se trouvent dans l’appareil.</li><li>Impossible d’effectuer des opérations de sauvegarde tant que la restauration ou le clonage n’est pas totalement terminé(e).|
| Disponibilité de la restauration légère | Dernier basculement | |
| Débit maximal de lecture/d’écriture du client (si traité à partir du niveau SSD)* | 920/720 Mo/s avec une seule interface réseau de 10 Gigabit Ethernet | Jusqu'à 2x avec MPIO et deux interfaces réseau. |
| Débit maximal de lecture/d’écriture du client (si traité à partir du niveau HDD)* | 120/250 Mo/s |
| Débit maximal de lecture/d’écriture du client (si traité à partir du niveau cloud)* | 11/41 Mo/s | Le débit de lecture dépend de si les clients génèrent et conservent une profondeur de file d’attente d’E/S suffisante. |

* Le débit maximal par type d’E/S a été mesuré avec des scénarios de lecture et d’écriture de 100 pour cent. Le débit réel peut être inférieur et varie en fonction du mélange d’E/S et des conditions réseau.

## Étapes suivantes

Relisez les [Conditions requises pour le système StorSimple](storsimple-system-requirements.md).

<!---HONumber=62-->