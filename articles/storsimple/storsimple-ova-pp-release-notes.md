---
title: Notes de publication pour StorSimple Virtual Array | Microsoft Docs
description: Décrit les problèmes critiques non résolus et les solutions possibles pour StorSimple Virtual Array.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''

ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/13/2016
ms.author: alkohli

---
# Notes de publication pour StorSimple Virtual Array
## Vue d’ensemble
Les notes de publication suivantes identifient les problèmes critiques non résolus pour la version de disponibilité générale de mars 2016 de Microsoft Azure StorSimple Virtual Array (également appelé appareil virtuel local StorSimple ou appareil virtuel StorSimple). Cette version correspond à la version logicielle n° 10.0.10271.0.

Les notes de publication sont régulièrement mises à jour ; les problèmes critiques nécessitant une solution de contournement sont ajoutés au fur et à mesure de leur découverte. Lisez les informations contenues dans les notes de publication avant de déployer votre appareil virtuel StorSimple.

Le tableau suivant récapitule les problèmes connus de cette version.

| N° | Fonctionnalité | Problème | Solution de contournement/commentaires |
| --- | --- | --- | --- |
| **1.** |Mises à jour |Les appareils virtuels créés dans la version préliminaire ne peuvent pas être mis à jour vers une version à disponibilité générale prise en charge. |Ces appareils virtuels doivent être basculés vers la version à disponibilité générale à l'aide d'un flux de travail de récupération d'urgence (DR). |
| **2.** |Disque de données configuré |Une fois que vous avez configuré un disque de données d'une certaine spécifiée et créé l'appareil virtuel StorSimple correspondant, vous ne devez pas développer, ni réduire le disque de données. Toute tentative de ce type entraînera une perte de toutes les données au niveau local de l’appareil. | |
| **3.** |Stratégie de groupe |Lorsqu’un appareil est joint à un domaine, le fait d’appliquer une stratégie de groupe peut avoir un impact négatif sur son fonctionnement. |Assurez-vous que votre tableau virtuel est dans sa propre unité organisationnelle (UO) pour Active Directory et qu'aucun objet de stratégie de groupe (GPO) ne lui est appliqué. |
| **4.** |Interface utilisateur web locale |Si les fonctionnalités de sécurité améliorées sont activées dans Internet Explorer (IE ESC), certaines pages de l’interface utilisateur web locale, comme Dépannage ou Maintenance, peuvent ne pas fonctionner correctement. Les boutons sur ces pages peuvent également ne pas fonctionner. |Désactivez les fonctionnalités de sécurité améliorées d'Internet Explorer. |
| **5.** |Interface utilisateur web locale |Sur une machine virtuelle Hyper-V, les interfaces réseau de l'interface utilisateur web sont affichées sous forme d'interfaces 10 Gbits/s. |Ce comportement est le reflet de Hyper-V. Hyper-V affiche toujours 10 Gbits/s pour les cartes de réseau virtuel. |
| **6.** |Partages ou volumes à plusieurs niveaux |Le verrouillage de la plage d'octets pour les applications qui fonctionnent avec les volumes à plusieurs niveaux StorSimple n'est pas pris en charge. Si le verrouillage de la plage d'octets est activé, la hiérarchisation StorSimple ne fonctionnera pas. |Les mesures recommandées incluent : <br></br>Désactiver le verrouillage de la plage d’octets dans la logique de votre application.<br></br>Choisir de placer les données de cette application dans des volumes localement épinglés par opposition à des volumes à plusieurs niveaux.<br></br>*Inconvénient* : si le verrouillage de la plage d’octets est activé avec l’utilisation de volumes localement épinglés, n’oubliez pas que le volume localement épinglé peut être en ligne avant même que la restauration ne soit terminée. Dans ce cas, si une restauration est en cours, vous devez attendre que l'opération se termine. |
| **7.** |Partages à plusieurs niveaux |L'utilisation de fichiers volumineux peut entraîner montée en charge de niveau lente. |Lorsque vous utilisez des fichiers volumineux, vérifiez que la taille du plus grand fichier est inférieure à 3 % de la taille du partage. |
| **8.** |Capacité utilisée pour les partages |Vous pouvez constater la consommation du partage en l'absence de données sur le partage. Ceci est dû au fait que la capacité utilisée pour les partages inclut des métadonnées. | |
| **9.** |Récupération d'urgence |La récupération d'urgence d'un serveur de fichiers ne peut s'effectuer que dans le même domaine que celui de l'appareil source. La récupération d'urgence vers un appareil cible dans un autre domaine n'est pas prise en charge dans cette version. |Ceci sera implémenté dans une version ultérieure. |
| **10.** |Azure PowerShell |Les appareils virtuels StorSimple ne peuvent pas être gérés via Azure PowerShell dans cette version. |La gestion des appareils virtuels doit être effectuée par le biais du portail Azure Classic et l'interface utilisateur web locale. |
| **11.** |Modification de mot de passe |La console de l’appareil exécutant le tableau virtuel accepte uniquement les entrées au format de clavier en-US. | |
| **12.** |CHAP |Il est impossible de supprimer les informations d’identification CHAP une fois qu’elles ont été créées. En outre, si vous modifiez les informations d’identification CHAP, vous devrez mettre les volumes hors connexion, puis les remettre en ligne pour que la modification prenne effet. |Ceci sera résolu dans une version ultérieure. |
| **13.** |Serveur iSCSI |Le « stockage utilisé » affiché pour un volume iSCSI peut différer entre le service StorSimple Manager et l’hôte iSCSI. |L’hôte iSCSI dispose de la vue du système de fichiers.<br></br>L’appareil voit les blocs alloués lorsque le volume était à sa taille maximale. |

<!---HONumber=AcomDC_0518_2016-->