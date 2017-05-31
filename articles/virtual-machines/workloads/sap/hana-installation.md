---
title: Installation de SAP HANA sur SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Comment installer SAP HANA sur SAP HANA sur Azure (grandes instances).
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 89356670737ed7b9adc5df51466ce71eb73b3cd4
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017


---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Guide pratique d’installation et de configuration de SAP HANA (grandes instances) sur Azure

Vous êtes responsable de l’installation de SAP HANA. Vous pouvez la lancer après réception d’un nouveau serveur SAP HANA sur Azure (grandes instances), une fois la connectivité établie entre vos réseaux virtuels Azure et les unités de grandes instances HANA. Veuillez noter que, conformément à la stratégie de SAP, l’installation de SAP HANA doit être effectuée par un installateur SAP HANA certifié, c’est-à-dire par une personne qui a passé la certification Certified SAP Technology Associate – SAP HANA Installation, ou par un intégrateur système certifié SAP.

## <a name="first-steps-after-receiving-the-hana-large-instance-units"></a>Premières étapes à suivre après la réception des unités de grandes instances HANA

Après avoir reçu l’unité de grandes instances HANA, puis établi l’accès et la connectivité aux instances, la **première étape** consiste à enregistrer le système d’exploitation de l’instance auprès de votre fournisseur de système d’exploitation. Cela inclut notamment l’enregistrement de votre système d’exploitation SUSE Linux dans une instance de SUSE SMT que vous devez avoir déployée dans une machine virtuelle Azure à laquelle l’unité Grande instance HANA peut se connecter. Ou bien l’enregistrement de votre système d’exploitation Red Hat dans le Gestionnaire d’abonnements Red Hat auquel vous devez vous connecter. Consultez également la section Remarques de ce [document](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Cette étape est également nécessaire si vous souhaitez appliquer des correctifs ultérieurs au système d’exploitation. Tâche qui incombe au client. Pour SUSE, la documentation permettant d’installer et de configurer SMT se trouve [ici](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html).

La **deuxième étape** consiste à rechercher les nouveaux correctifs applicables à la version spécifique du système d’exploitation. Vérifiez que le niveau de correctif logiciel des grandes instances HANA est à jour. En fonction de la date de publication des correctifs/versions du système d’exploitation et des modifications de l’image que Microsoft peut déployer, il peut arriver que les correctifs les plus récents ne soient pas inclus. Par conséquent, cette étape est obligatoire après réception d’une unité de grandes instances HANA et enregistrement de l’installation du système d’exploitation auprès du distributeur Linux. Il s’agit de vérifier si des correctifs indispensables pour la sécurité, le fonctionnement, la disponibilité et les performances ont été publiés entretemps par le fournisseur Linux concerné et s’ils doivent être appliqués.

La **troisième étape** consiste à consulter les notes de support SAP relatives à l’installation et à la configuration de SAP HANA sur la version spécifique du système d’exploitation. En raison des recommandations variables ou des modifications apportées aux notes de support SAP ou des configurations qui dépendent de scénarios d’installation uniques, Microsoft ne sera pas toujours en mesure de fournir une unité de grandes instances HANA configurée à la perfection. Il vous incombe donc en tant que client de lire les notes de support SAP (recommandations minimales répertoriées ci-dessous), de vérifier la configuration de la version du système d’exploitation nécessaire et d’appliquer les paramètres idoines le cas échéant.

Vérifiez en particulier les paramètres ci-dessous et effectuez les ajustements éventuels nécessaires :

- net.core.rmem_max = 16777216
- net.core.wmem_max = 16777216
- net.core.rmem_default = 16777216
- net.core.wmem_default = 16777216
- net.core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- net.ipv4.tcp_wmem = 65536 16777216 16777216

Sous SLES12 SP1 et RHEL 7.2, ces paramètres doivent être définis dans un fichier de configuration dans le répertoire /etc/sysctl.d. Il faut par exemple créer un fichier de configuration nommé 91-NetApp-HANA.conf. Pour les versions plus anciennes de SLES et RHEL, ces paramètres doivent être définis dans /etc/sysctl.conf.

Pour toutes les versions de RHEL et à partir de SLES12, le paramètre 
- sunrpc.tcp_slot_table_entries = 128

doit être défini dans /etc/modprobe.d/sunrpc-local.conf. Si le fichier n’existe pas, il doit tout d’abord être créé en ajoutant l’entrée suivante : 
- options sunrpc tcp_max_slot_table_entries=128

La **quatrième étape** consiste à vérifier l’heure système de votre unité de grandes instances HANA. Les instances sont déployées selon un fuseau horaire système qui représente l’emplacement de la région Azure correspondant au tampon de grande instance HANA. Vous êtes libre de modifier l’heure système ou le fuseau horaire des instances que vous possédez. Dans ce cas, si vous commandez d’autres instances dans votre locataire, vous devrez probablement modifier le fuseau horaire des nouvelles instances. Microsoft Operations n’a aucune visibilité sur le fuseau horaire système défini pour vos instances après leur réception. C’est pourquoi les instances nouvellement déployées peuvent ne pas être définies sur le même fuseau horaire que celui choisi précédemment. Il vous incombe donc, en tant que client, de vérifier le fuseau horaire des instances remises et de les modifier si nécessaire. 

La **cinquième étape** consiste à vérifier le fichier etc/hosts. À la réception des unités, des adresses IP spécifiques sont affectées à différentes fonctions (voir la section suivante). Vérifiez le fichier etc/hosts. Si vous ajoutez des unités à un locataire existant, le fichier etc/hosts des systèmes nouvellement déployés ne contiendra pas les adresses IP des systèmes reçus précédemment. Il vous incombe donc, en tant que client, de définir les paramètres appropriés pour que l’instance nouvellement déployée puisse interagir et résoudre les noms des unités précédemment déployées dans votre locataire. 

## <a name="networking"></a>Mise en réseau
Nous partons du principe que vous avez suivi les recommandations données dans les documents ci-après pour concevoir vos réseaux virtuels Azure et les connecter aux grandes instances HANA :

- [Vue d’ensemble et architecture de SAP HANA (grandes instances) sur Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Infrastructure et connectivité à SAP HANA (grandes instances) sur Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Certains détails concernant la mise en réseau d’unités individuelles valent la peine d’être soulignés. Chaque unité de grandes instances HANA est fournie avec deux ou trois adresses IP affectées à deux ou trois de ses ports de carte réseau. Trois adresses IP sont utilisées dans les configurations de montée en charge HANA et dans le scénario de réplication système HANA. Une des adresses IP affectées à la carte réseau de l’unité se situe hors du pool d’adresses IP du serveur décrit à la page [Vue d’ensemble et architecture de SAP HANA (grandes instances) sur Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).

Pour les unités avec deux adresses IP affectées, voici à quoi devrait ressembler la distribution :

- L’adresse IP affectée à eth0.xx doit se trouver hors de la plage du pool d’adresses IP du serveur envoyé à Microsoft. Cette adresse IP doit être utilisée pour maintenir le fichier /etc/hosts du système d’exploitation à jour.
- L’adresse IP affectée à eth1.xx doit être utilisée pour la communication avec le stockage NFS. Par conséquent, ces adresses **N’ONT PAS BESOIN** d’être mises à jour dans le fichier etc/hosts pour autoriser un trafic interinstance au sein du locataire.

Une configuration à deux adresses IP ne convient pas aux déploiements de réplication système HANA ou aux montées en charge HANA. Si seulement deux adresses IP sont affectées et si vous souhaitez déployer une telle configuration, contactez Microsoft Service Management pour obtenir une troisième adresse IP dans un 3e VLAN. Pour les unités de grandes instances HANA avec trois adresses IP affectées à trois ports de carte réseau, la distribution suivante s’applique :

- L’adresse IP affectée à eth0.xx doit se trouver hors de la plage du pool d’adresses IP du serveur envoyé à Microsoft. Par conséquent, cette adresse IP ne doit pas être utilisée pour maintenir le fichier /etc/hosts du système d’exploitation à jour.
- L’adresse IP affectée à eth1.xx doit être utilisée pour la communication avec le stockage NFS. Ce type d’adresse ne doit donc pas être mis à jour dans le fichier etc/hosts.
- L’adresse IP du port eth2.xx doit être mise à jour dans le fichier etc/hosts pour la communication entre les différentes instances exclusivement. Ces adresses IP doivent également être mises à jour dans les configurations HANA de montée en charge : ce sont les adresses IP que HANA utilise pour la communication entre les nœuds.



## <a name="storage"></a>Storage

La disposition du stockage de SAP HANA sur Azure (grandes instances) est configurée par SAP HANA sur Azure Service Management en suivant les meilleures pratiques recommandées par SAP. Voir le livre blanc [SAP HANA Storage Requirements](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) (Conditions de stockage SAP HANA). À la lecture de ce document, et en regardant une unité de plus près, vous pourrez vous rendre compte de la générosité du volume disque des unités de grandes instances HANA attribué à HANA/data, et remarquer la présence d’un volume HANA/log/backup. Si HANA/data est aussi volumineux, c’est que les captures instantanées du stockage que nous vous proposons l’exigent. En d’autres termes, plus vous effectuez de captures instantanées du stockage, plus elles prendront d’espace. Le volume HANA/log/backup est moins considéré comme l’emplacement des sauvegardes des bases de données que comme le volume de sauvegarde exploité lors de la création du journal de transactions HANA. Dans les versions ultérieures de la fonction de capture instantanée du stockage en libre-service, ce volume spécifique accueillera des captures plus fréquentes, et davantage de réplications vers le site de récupération d’urgence (si vous choisissez de vous abonner à la fonction de récupération d’urgence de l’infrastructure de grandes instances HANA). Pour en savoir plus, consultez la page [Haute disponibilité et récupération d’urgence de SAP HANA (grandes instances) sur Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

En plus de la capacité de stockage fournie, les clients peuvent acheter de l’espace de stockage supplémentaire par incréments de 1 To. Cet espace supplémentaire peut être ajouté en tant que nouveaux volumes à des grandes instances HANA.

Pendant l’intégration avec Microsoft Service Management, le client spécifie un ID d’utilisateur et un ID de groupe pour <SID>l’utilisateur ADM et le groupe sapsys (p. ex. : 1000,500). Ces mêmes valeurs doivent être utilisées lors de l’installation du système SAP HANA.

Le contrôleur de stockage et les nœuds dans les tampons de grandes instances sont synchronisés avec les serveurs NTP. Dans la mesure où vous synchronisez les unités SAP HANA sur Azure (grandes instances) et les machines virtuelles Azure avec un serveur NTP, vous ne devriez pas rencontrer d’écart de temps entre l’infrastructure et les unités de calcul dans Azure ou les tampons de grandes instances.

Pour optimiser SAP HANA pour le stockage sous-jacent, définissez également les paramètres de configuration SAP HANA suivants :

- max_parallel_io_requests 128
- async_read_submit on
- async_write_submit_active on
- async_write_submit_blocks all
 
Pour les versions de SAP HANA 1.0 jusqu’à SPS12, ces paramètres peuvent être définis au cours de l’installation de la base de données SAP HANA, comme décrit dans la [Note de support SAP #2267798 - Configuration of the SAP HANA Database](https://launchpad.support.sap.com/#/notes/2267798) (Configuration de la base de données SAP HANA)

Vous pouvez également configurer les paramètres après l’installation de la base de données SAP HANA via le framework hdbparam. 

L’utilisation du framework hdbparam est déconseillée avec SAP HANA 2.0. Il faut donc définir le paramètre à l’aide de commandes SQL. Pour plus d’informations, consultez la [Note de support SAP #2399079 : Elimination of hdbparam in HANA 2](https://launchpad.support.sap.com/#/notes/2399079) (Suppression de hbdparam dans HANA 2).


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



