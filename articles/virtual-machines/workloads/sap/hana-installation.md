---
title: Installation de SAP HANA sur SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Comment installer SAP HANA sur SAP HANA sur Azure (grandes instances).
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: fa842efd99718be7fa9eaf8aac8030c32cbceeec
ms.openlocfilehash: 0d998b5347a9da6dd209b0ba106792ab5c34164d
ms.lasthandoff: 03/01/2017


---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Guide pratique d’installation et de configuration de SAP HANA (grandes instances) sur Azure

L’installation de SAP HANA relève de votre responsabilité. Vous pouvez l’effectuer immédiatement après la remise d’un nouveau serveur SAP HANA sur Azure (grandes instances). Veuillez noter que, conformément à la stratégie de SAP, l’installation de SAP HANA doit être effectuée par un installateur SAP HANA certifié, c’est-à-dire par une personne qui a passé la certification Certified SAP Technology Associate – SAP HANA Installation, ou par un intégrateur système certifié SAP.

Certaines considérations de connectivité liées à SAP HANA (côté serveur) et SAP HANA (côté client) doivent être prises en compte. Dans de nombreux cas, le serveur SAP HANA envoie son adresse IP au client, qui la met alors en cache et l’utilise pour les tentatives de connexion ultérieures. Dans la mesure où SAP HANA sur Azure (grandes instances) transforme l’adresse IP du serveur interne utilisée dans le réseau locataire en une plage d’adresses IP fournie pour les réseaux virtuels Azure spécifiés, le serveur de base de données SAP HANA renvoie la plage d’adresses IP &quot;interne&quot;. Par exemple, pour la résolution de nom d’hôte, au lieu que SAP HANA fournisse l’adresse IP transformée, l’adresse IP interne mise en cache est utilisée. Par conséquent, les applications utilisant un client SAP HANA (ODBC, JDBC, etc.) ne seront pas en mesure de se connecter avec cette adresse IP. Pour indiquer au serveur SAP HANA qu’il doit propager l’adresse IP transformée au client, le fichier de configuration global du système SAP HANA (global.ini) doit être modifié.

Modifiez le fichier global.ini comme suit (directement ou via SAP HANA Studio) :
```
[public\_hostname\_resolution]
use\_default\_route = no
map\_<hostname of SAP HANA on Azure (Large Instances) tenant> = <NAT IP Address (IP address that can be accessed in Azure)>
```
Consultez la section _Mappage de noms d’hôte pour l’accès client à la base de données_ dans le [guide d’administration SAP HANA](http://help.sap.com/hana/sap_hana_administration_guide_en.pdf) pour plus d’exemples.

Côté client (pour les serveurs d’applications SAP dans Azure), modifiez le fichier /etc/hosts (sur Linux) ou /system32/drivers/etc/hosts (sur Windows Server) et incluez une entrée pour le nom d’hôte du locataire SAP HANA sur Azure (grandes instances) et son adresse IP transformée.

>[!NOTE] 
>Si des erreurs se produisent lors de la connexion à SAP HANA sur Azure (grandes instances) lors de l’installation du client de base de données SAP HANA, utilisez implicitement l’adresse IP NAT du locataire des grandes instances HANA au lieu du nom d’hôte.

## <a name="storage"></a>Storage

La disposition du stockage de SAP HANA sur Azure (grandes instances) est configurée par SAP HANA sur Azure Service Management en suivant les meilleures pratiques recommandées par SAP. Voir le livre blanc [SAP HANA Storage Requirements](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) (Conditions de stockage SAP HANA).

Les configurations exactes de stockage pour les différentes grandes instances HANA sont :

| Taille de la mémoire | HANA/data | HANA/log | HANA/shared | HANA/log/backups |
| --- | --- | --- | --- | --- |
| 768 Go | 1280 Go | 512 Go | 768 Go | 512 Go |
| 1536 Go | 3456 Go | 768 Go | 1024 Go | 768 Go |
| 3072 Go | 7680 Go | 1536 Go | 1024 Go | 1536 Go |

En outre, les clients peuvent acheter de l’espace de stockage supplémentaire par incréments de 1 To. Cet espace supplémentaire peut être ajouté en tant que nouveaux volumes à des grandes instances HANA.

Le contrôleur de stockage et les nœuds dans les tampons de grandes instances sont synchronisés avec les serveurs NTP. Dans la mesure où vous synchronisez les unités SAP HANA sur Azure (grandes instances) et les machines virtuelles Azure avec un serveur NTP, vous ne devriez pas rencontrer d’écart de temps entre l’infrastructure et les unités de calcul dans Azure ou les tampons de grandes instances.

## <a name="operating-system"></a>Système d’exploitation

[SUSE Linux Enterprise Server 12 SP1 for SAP Applications](https://www.suse.com/products/sles-for-sap/hana) est la distribution de Linux installée pour SAP HANA sur Azure (grandes instances). Cette distribution spéciale fournit des fonctionnalités spécifiques à SAP &quot;prêtes à l’emploi&quot; (y compris des paramètres prédéfinis pour une exécution efficace de SAP sur SLES).

Consultez [la bibliothèque de ressources/les livres blancs](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) sur le site Web SUSE et [SAP sur SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) sur SCN (SAP Community Network) pour bénéficier de ressources utiles relatives au déploiement de SAP HANA sur SLES (notamment sur la configuration de la haute disponibilité, le renforcement de la sécurité spécifique aux opérations SAP, etc.).

Autres liens utiles relatifs à SLES :

- [SAP HANA sur le site SUSE Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [Best Practice for SAP: Enqueue Replication – SAP NetWeaver on SUSE Linux Enterprise 12](https://www.suse.com/docrepcontent/container.jsp?containerId=9113) (Meilleure pratique pour SAP : réplication de file d’attente – SAP NetWeaver sur SUSE Linux Enterprise 12)
- [ClamSAP – SLES Virus Protection for SAP](http://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (ClamSAP : protection antivirus SLES pour SAP) (y compris SLES 12 for SAP Applications)

Notes de support SAP applicables à l’implémentation de SAP HANA sur SLES 12 SP1 :

- [Note de support SAP #1944799 – SAP HANA Guidelines for SLES Operating System Installation](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html) (Instructions SAP HANA pour l’installation du système d’exploitation SLES)
- [Note de support SAP #2205917 – SAP HANA DB Recommended OS Settings for SLES 12 for SAP Applications](https://launchpad.support.sap.com/#/notes/2205917/E) (Paramètres SAP HANA DB de système d’exploitation recommandés pour SLES 12 for SAP Applications)
- [Note de support SAP #1984787 – SUSE Linux Enterprise Server 12:  Installation Notes](https://launchpad.support.sap.com/#/notes/1984787) (SUSE Linux Enterprise Server 12 : notes d’installation)
- [Note de support SAP #171356 – SAP Software on Linux:  General Information](https://launchpad.support.sap.com/#/notes/1984787) (Logiciel SAP sur Linux : informations générales)
- [Note de support SAP #1391070 – Linux UUID Solutions](https://launchpad.support.sap.com/#/notes/1391070) (Solutions Linux UUID)

## <a name="time-synchronization"></a>Synchronisation temporelle

SAP est très sensible aux écarts temporels entre les différents composants qui constituent le système SAP. Vous connaissez certainement les vidages courts SAP ABAP avec le titre d’erreur ZDATE\_LARGE\_TIME\_DIFF si vous avez travaillé avec SAP (De base) pendant une longue période, dans la mesure où ces vidages courts surviennent lorsque l’heure système des différents serveurs ou machines virtuelles s’éloigne trop.

Pour SAP HANA sur Azure (grandes instances), la synchronisation de l’heure effectuée dans Azure ne s’applique pas aux unités de calcul dans les tampons de grandes instances. Cela ne concerne pas les applications SAP qui s’exécutent en mode natif dans Azure (sur des machines virtuelles), car Azure garantit la bonne synchronisation de l’heure des systèmes. Par conséquent, un serveur temporel distinct doit être configuré pour être utilisé par les serveurs d’applications SAP s’exécutant sur des machines virtuelles Azure et les instances de base de données SAP HANA s’exécutant sur des grandes instances HANA. L’heure de l’infrastructure de stockage dans les tampons de grandes instances est synchronisée avec les serveurs NTP.



