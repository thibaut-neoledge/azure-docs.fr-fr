---
title: "Notes de publication de StorSimple 8000 Update 0.3 | Microsoft Docs"
description: "Décrit les nouvelles fonctionnalités, les correctifs, les problèmes non résolus et les solutions de contournement disponibles pour la version de février 2015 de Microsoft Azure StorSimple (Update 0.3)."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: b01bfd04-f9f8-45f4-ade8-95ac2b979e6a
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/18/2016
ms.author: v-sharos
ms.translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: c059fd74854813615754e67547497b7ededbe4dd
ms.contentlocale: fr-fr
ms.lasthandoff: 07/06/2017


---
# <a name="storsimple-8000-series-update-03-release-notes---february-2015"></a>Notes de publication de StorSimple série 8000 Update 0.3 - Février 2015
## <a name="overview"></a>Vue d'ensemble
Les notes de publication suivantes identifient les problèmes critiques non résolus relatifs à la version StorSimple série 8000 Update 0.3, publiée en février 2015. Elles contiennent également une liste des mises à jour du logiciel et du microprogramme StorSimple incluses dans cette version. Il s’agit de la troisième version postérieure à la mise à disposition générale de la version StorSimple série 8000, en juillet 2014.

Cette mise à jour ne modifie pas la version logicielle de l’appareil, qui a été mise à jour en janvier. La version est toujours 6.3.9600.17312. Vous pouvez confirmer que la mise à jour a bien été installée en vérifiant la date de **dernière mise à jour** . Si la date est le 10/02/2015 ou une date ultérieure, la mise à jour a bien été installée.  

Nous vous recommandons de rechercher les mises à jour éventuelles et de les appliquer immédiatement après avoir installé l’appareil StorSimple. Vous pouvez également activer les mises à jour automatiques pour télécharger et installer les mises à jour de haute priorité à partir de Microsoft dès qu’elles sont mises à disposition. Consultez la rubrique [Mise à jour de votre appareil StorSimple](storsimple-update-device.md)pour plus d'informations.  

Lisez les informations contenues dans les notes de publication avant de déployer la mise à jour dans votre solution StorSimple.  

> [!IMPORTANT]
> * Utilisez le service StorSimple Manager, pas Windows PowerShell pour StorSimple, pour installer la mise à jour de février.   
> * L’installation de cette mise à jour prend environ une heure. Toutefois, si vous installez des mises à jour cumulatives, la procédure peut prendre environ 3 heures.  
> * La version de février de StorSimple ne contient aucune mise à jour pour l’appareil virtuel StorSimple. Vous pouvez toujours appliquer les mises à jour Windows disponibles à l’appareil virtuel, y compris les correctifs de sécurité récents, mais celles-ci ne modifieront pas la version de l’appareil virtuel.  
> 
> 

Assurez-vous que les conditions préalables suivantes sont remplies avant de mettre l’appareil StorSimple à jour.  

* Vérifiez que les deux contrôleurs de l’appareil sont actifs avant de rechercher des mises à jour. Si un des contrôleurs n’est pas actif, la recherche échoue. Pour vérifier que les contrôleurs fonctionnent correctement, accédez à **État du matériel** dans la page **Maintenance**. Si des composants **nécessitent une attention**, contactez le support technique de Microsoft avant de continuer.
* Vérifiez que les adresses IP fixes des contrôleurs 0 et 1 prennent le routage en charge et permettent la connexion à Internet, car elles sont utilisées pour installer les mises à jour sur l’appareil. Vous pouvez utiliser l’ [applet de commande Test-Connection](https://technet.microsoft.com/library/hh849808.aspx) pour effectuer un test ping sur une adresse connue en dehors du réseau, telle que outlook.com, afin de vérifier que le contrôleur peut se connecter au réseau externe.
* Vérifiez que les ports 80 et 443 sont disponibles pour la communication sortante sur l’appareil StorSimple. Pour plus d’informations, consultez la rubrique [Configuration réseau requise pour votre appareil StorSimple](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).
* Si la version logicielle de l’appareil est supérieure à 6.3.9600.17312 (mise à jour d’octobre 2014), désactivez les ports de données 2 et 3, le cas échéant, avant de commencer la mise à jour. Si vous laissez le port de données 2 ou 3 activé lors de la mise à jour, le contrôleur de l’appareil risque de passer en mode de récupération. Veuillez noter que lorsque vous désactivez les interfaces réseau, tous les volumes associés sont mis hors connexion, et les E/S sont interrompues pendant toute la durée de la mise à jour.  

## <a name="whats-new-in-the-february-release"></a>Nouveautés dans la version de février
Cette mise à jour contient un correctif pour le problème de réinitialisation aux paramètres d’usine rencontré sur les appareils qui ont été mis à niveau vers la version d’octobre 2014 à partir de la version mise à la disposition générale. Pour plus d’informations, reportez-vous à [Problèmes résolus dans cette version](#issues-fixed-in-the-february-release).   

Cette mise à jour ne contient pas de nouvelles fonctions ou fonctionnalités.  

## <a name="issues-fixed-in-the-february-release"></a>Problèmes résolus dans la version de février
Le tableau suivant décrit le problème résolu dans cette mise à jour.  

| N° | Fonctionnalité | Problème | S’applique à un appareil physique | S’applique à un appareil virtuel |
| --- | --- | --- | --- | --- |
| 1 |Réinitialisation aux paramètres d’usine |Vous essayez de restaurer les paramètres d’usine sur un appareil sur lequel la version mise à la disposition générale (version 6.3.9600.17215) était initialement installée et qui a été mis à jour vers la version d’octobre (version 6.3.9600.17312). La réinitialisation aux paramètres d’usine échoue et l’appareil devient instable. |Oui |Non |

## <a name="known-issues-in-the-february-release"></a>Problèmes connus dans la version de février
Le tableau suivant récapitule les problèmes connus dans cette version.

| Non. | Fonctionnalité | Problème | Commentaires/solution de contournement | S’applique à un appareil physique | S’applique à un appareil virtuel |
| --- | --- | --- | --- | --- | --- |
| 1 |Réinitialisation aux paramètres d’usine |Dans certains cas, lorsque vous effectuez une réinitialisation aux paramètres d’usine, l’appareil StorSimple peut se bloquer et afficher le message suivant : **La réinitialisation aux paramètres d’usine est en cours (phase 8)**. Cela se produit si vous appuyez sur Ctrl+C alors que l’applet de commande est en cours. |N’appuyez pas sur Ctrl+C après avoir lancé une réinitialisation aux paramètres d’usine. Si vous avez déjà effectué cette opération, contactez le support technique Microsoft. |Oui |Non |
| 2 |Disque quorum |Dans de rares cas, si la majorité des disques du boîtier EBOD d’un appareil 8600 sont déconnectés, ce qui signifie qu’il n’y a pas de disque quorum, le pool de stockage est hors connexion. Il reste hors connexion même si les disques sont reconnectés. |Vous devez redémarrer l’appareil. Si le problème persiste, contactez le support technique Microsoft. |Oui |Non |
| 3 |Échec des instantanés cloud |Dans de rares cas, un instantané cloud peut échouer et renvoyer l’erreur **Limite de sauvegarde maximale atteinte**. Cela se produit si vous avez plus de 255 clones en ligne sur un même appareil, provenant du volume d’origine qui a été supprimé. | |Oui |Oui |
| 4 |ID de contrôleur incorrect |Lorsqu’un contrôleur est remplacé, le contrôleur 0 peut apparaître comme contrôleur 1. Pendant le remplacement du contrôleur, lorsque l’image est chargée à partir du nœud homologue, l’ID du contrôleur peut s’afficher initialement comme l’ID du contrôleur homologue. Dans de rares cas, ce comportement peut également se produire après un redémarrage du système. |Aucune action utilisateur n’est requise. Cette situation se résout automatiquement une fois le contrôleur remplacé. |Oui |Non |
| 5 |Graphiques d’analyse de l’appareil |Dans le service StorSimple Manager, les graphiques d’analyse de l’appareil ne fonctionnent pas lorsque l’authentification de base ou NTLM est activée dans la configuration du serveur proxy pour l’appareil. |Modifiez la configuration du proxy web pour l’appareil inscrit auprès du service StorSimple Manager afin que l’authentification soit définie sur AUCUNE. Pour cela, exécutez l’applet de commande Set-HcsWebProxy de Windows PowerShell pour StorSimple. |Oui |Oui |
| 6 |Comptes de stockage |La suppression du compte de stockage à l’aide du service de stockage n’est pas prise en charge. En effet, cette opération donnerait lieu à une situation dans laquelle il serait impossible de récupérer les données utilisateur. | |Oui |Oui |
| 7 |Basculement de l’appareil |Le basculement multiple d’un conteneur de volumes d’un même appareil source vers différents appareils cibles n’est pas pris en charge.    Si un appareil inactif est basculé vers plusieurs appareils, les conteneurs de volumes du premier appareil basculé perdent la propriété des données. Après un basculement de ce type, les conteneurs de volumes concernés apparaissent ou se comportent différemment lorsque vous les affichez dans le portail Azure Classic. | |Oui |Non |
| 8 |Installation |Lors de l’installation de l’adaptateur StorSimple pour SharePoint, vous devez fournir une adresse IP d’appareil pour que l’installation s’effectue correctement. | |Oui |Non |
| 9 |Proxy web |Si HTTPS est défini comme protocole dans la configuration du proxy web, la communication appareil-service est altérée et l’appareil se met hors connexion. Des packages de prise en charge sont également générés, ce qui consomme de nombreuses ressources de l’appareil. |Vérifiez que le protocole défini pour l’URL du proxy web est bien HTTP. Découvrez comment [configurer le proxy web pour votre appareil](storsimple-configure-web-proxy.md). |Oui |Non |
| 10 |Proxy web |Si vous configurez et activez le proxy web sur un appareil inscrit, vous devez redémarrer le contrôleur actif sur votre appareil. | |Oui |Non |
| 11 |Latence de cloud élevée et charge de travail d’E/S élevée |Lorsque l’appareil StorSimple doit gérer à la fois des latences de cloud très élevées (de l’ordre de quelques secondes) et une charge de travail d’E/S élevée, ses volumes se détériorent et une défaillance peut se produire au niveau des E/S, avec l’erreur « appareil non prêt ». |Vous devez redémarrer les contrôleurs de l’appareil manuellement ou effectuer un basculement d’appareil pour résoudre ce problème. |Oui |Non |

## <a name="physical-device-updates-in-the-february-release"></a>Mises à jour de l’appareil physique dans la version de février
Cette mise à jour contient un correctif pour le problème de réinitialisation aux paramètres d’usine rencontré sur les appareils qui ont été mis à niveau vers la version d’octobre 2014 à partir de la version mise à la disposition générale. Elle ne contient aucune autre mise à jour pour l’appareil StorSimple.  

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-the-february-release"></a>Mises à jour pour le microprogramme et le contrôleur SAS (Serial Attached SCSI) dans la version de février
Cette version ne contient aucune mise à jour pour le microprogramme ou le contrôleur série SAS (Serial Attached SCSI). Le pilote a été mis à jour dans la version d’octobre 2014.  

## <a name="virtual-device-updates-in-the-february-release"></a>Mises à jour pour l’appareil virtuel dans la version de février
Cette version ne contient aucune mise à jour pour l’appareil virtuel. L’installation de cette mise à jour ne modifie pas la version logicielle de l’appareil virtuel.


