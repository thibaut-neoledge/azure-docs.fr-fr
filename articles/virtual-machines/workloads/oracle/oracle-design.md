---
title: "Concevoir et implémenter une base de données Oracle sur Azure | Microsoft Docs"
description: "Concevez et implémentez une base de données Oracle dans votre environnement Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: v-shiuma
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/22/2017
ms.author: rclaus
ms.translationtype: HT
ms.sourcegitcommit: d941879aee6042b38b7f5569cd4e31cb78b4ad33
ms.openlocfilehash: e13e61a2d055ce4f9777cea8bf6199f2acf9e7bf
ms.contentlocale: fr-fr
ms.lasthandoff: 07/10/2017

---


# <a name="design-and-implement-of-oracle-database-on-azure"></a>Concevoir et implémenter une base de données Oracle sur Azure

## <a name="assumptions"></a>Hypothèses

- Planification de la migration d’une base de données Oracle locale vers Azure
- Présentation des diverses mesures de rapports Oracle AWR
- Définition d’un planning de référence pour les performances d’application et l’utilisation de la plateforme

## <a name="goals"></a>Objectifs

- Présentation de l’optimisation de votre déploiement Oracle sur Azure
- Explorer les options de réglage des performances pour une base de données Oracle dans un environnement Azure

### <a name="on-premises-vs-on-azure"></a>Ressources locales et ressources Azure

Voici quelques considérations importantes à prendre en compte lors de la migration d’applications locales vers Azure. Contrairement aux ressources locales, les ressources (machines virtuelles, disques, réseau virtuel, etc.) dans Azure sont partagées avec les autres clients. En outre, la ressource peut être limitée en fonction d’exigences définies. Au lieu de se concentrer sur ce qui permet d’éviter l’échec (temps moyen entre défaillances), Azure se concentre sur ce qui permet de survivre à un échec (temps moyen de récupération).

Le tableau suivant répertorie quelques-unes de ces différences.

> 
> |  | **Local** | **Microsoft Azure** |
> | --- | --- | --- |
> | **Mise en réseau** |LAN/WAN  |SDN (Software-Defined Networking)|
> | **Groupe de sécurité** |Outils de restriction d’adresse IP/de port |[Groupe de sécurité réseau](https://azure.microsoft.com/blog/network-security-groups) |
> | **Résilience** |MTBF (temps moyen entre défaillances) |MTTR (temps moyen de récupération)|
> | **Maintenance planifiée** |Correctifs/mises à niveau|[Groupes à haute disponibilité](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) (correctifs/mises à niveau gérés par Azure) |
> | **Ressource** |Dédié  |Partagée avec d’autres clients|
> | **Régions** |Centres de données |[Paires de régions](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability)|
> | **Stockage** |SAN/disques physiques |[Stockage géré par Azure](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
> | **Mettre à l'échelle** |Verticale |Horizontale|


### <a name="requirements"></a>Configuration requise

- Déterminer le taux de croissance et la taille de la base de données
- Déterminer les exigences en termes d’E/S par seconde ; une estimation peut être réalisée à partir d’un rapport AWR Oracle ou d’autres outils de surveillance réseau

## <a name="configuration-options"></a>Options de configuration

Quatre zones potentielles peuvent être paramétrées pour améliorer les performances dans un environnement Azure.

- Taille de la machine virtuelle
- Débit du réseau
- Types de disque et configurations
- Paramètres de cache des disques

### <a name="generate-awr-report"></a>Générer un rapport AWR

Si vous disposez d’une base de données Oracle existante et si vous planifiez une migration vers Azure. Vous pouvez exécuter le rapport Oracle AWR pour obtenir les mesures (E/S par seconde, Mbits/s, GiO/s, etc.), puis sélectionner la machine virtuelle à partir des mesures que vous avez collectées. Vous pouvez également contacter votre équipe d’infrastructure pour obtenir des informations similaires.

Vous pouvez envisager d’exécuter votre rapport AWR lors de charges de travail régulières et maximales, pour comparer la différence. En fonction de ces rapports, vous pouvez dimensionner les machines virtuelles selon la charge de travail moyenne ou maximale.

L’exemple suivant montre comment générer un rapport AWR.

```bash
$ sqlplus / as sysdba
SQL> EXEC DBMS_WORKLOAD_REPOSITORY.CREATE_SNAPSHOT;
SQL> @?/rdbms/admin/awrrpt.sql
```

### <a name="key-metrics"></a>Mesures clés

Voici les mesures qui peuvent être tirées du rapport AWR.

- Nombre total de cœurs
- Vitesse d’horloge du processeur
- Quantité totale de mémoire en Go
- Utilisation du processeur
- Taux maximal de transfert de données
- Taux de modification d’E/S (lecture/écriture)
- Taux d’enregistrement de la restauration par progression (Mbits/s)
- Débit du réseau
- Taux de latence réseau (élevée/basse)
- Taille de la base de données en Go
- Octets reçus par le biais de SQL*Net en provenance/à destination du client

### <a name="virtual-machine-size"></a>Taille de la machine virtuelle

#### <a name="1-initial-estimate-of-vm-size-is-based-on-cpumemoryio-usage-from-the-awr-report"></a>1. L’estimation initiale de la taille de la machine virtuelle est basée sur l’utilisation du processeur/de la mémoire/des E/S selon le rapport AWR.

Vous pouvez examiner les cinq principaux événements en premier plan expirés, qui indiquent l’emplacement des goulots d’étranglement.

Par exemple : dans le diagramme ci-dessous, la synchronisation des fichiers journaux est en haut, et elle indique le nombre d’attentes d’écriture de LGWR sur la mémoire tampon pour le fichier journal de restauration par progression. Cela indique qu’un stockage/des disques de meilleure qualité sont requis. En outre, le nombre de processeurs (cœurs) et la mémoire apparaissent dans le diagramme.

![Capture d’écran de la page Rapport AWR](./media/oracle-design/cpu_memory_info.png)

Le diagramme ci-dessous montre le nombre total d’E/S en lecture et écriture. 59 Go sont en lecture et 247,3 Go sont en écriture pendant le rapport.

![Capture d’écran de la page Rapport AWR](./media/oracle-design/io_info.png)

#### <a name="2-estimate-the-vm-size"></a>2. Estimer la taille de la machine virtuelle

À partir des informations collectées dans le rapport AWR, l’étape suivante consiste à choisir une machine virtuelle de taille similaire et qui répond à vos besoins. Les machines virtuelles disponibles sont répertoriées dans ce lien suivant : [tailles des machines virtuelles](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-sizes-memory).

#### <a name="3-fine-tune-the-vm-sizing-with-similar-vm-series-based-on-the-acu"></a>3. Ajustez la taille de la machine virtuelle aux séries de machines virtuelles similaires en fonction de l’ACU

Une fois que vous avez choisi les machines virtuelles, faites attention à l’ACU pour les machines virtuelles. Vous pouvez choisir une machine virtuelle similaire, basée sur la valeur ACU, afin de mieux répondre à vos propres besoins. Pour plus d’informations, consultez [Unité de calcul Azure (ACU)](https://docs.microsoft.com/azure/virtual-machines/windows/acu).

![Capture d’écran de la page Unités ACU](./media/oracle-design/acu_units.png)

### <a name="network-throughput"></a>Débit du réseau

Voici la relation entre le débit et le nombre d’E/S par seconde.

![Capture d’écran du débit](./media/oracle-design/throughput.png)

Le débit réseau total est une estimation basée sur ce qui suit :
- Trafic SQL*Net
- Mbits/s x nombre de serveurs (flux sortant tel que Data Guard)
- Autres facteurs, comme la réplication de l’application

![Capture d’écran du débit SQL*Net](./media/oracle-design/sqlnet_info.png)

Selon vos besoins en bande passante réseau, il existe différents types de passerelle (Basic, VpnGw, ExpressRoute). Pour en savoir plus, consultez [Tarification de Passerelle VPN](https://azure.microsoft.com/en-us/pricing/details/vpn-gateway/?v=17.23h)

Recommandations

- La latence réseau est supérieure par rapport à un déploiement local. La diminution des allers-retours réseau peut considérablement améliorer les performances.
- Consolidez les applications disposant de transactions élevées ou les applications bavardes sur la même machine virtuelle pour réduire les allers-retours.

### <a name="disk-types-and-configurations"></a>Types de disque et configurations

- Disque de système d’exploitation par défaut : avec données persistantes et mise en cache. Ce disque est optimisé pour un accès au système d’exploitation à l’heure de démarrage, mais il n’est pas conçu pour les charges de travail transactionnelles ou Data Warehouse (analyse)

- Disques non gérés : vous gérez les comptes de stockage que vous utilisez pour stocker les fichiers de disque dur virtuel (VHD) correspondant à vos disques de machine virtuelle. Les fichiers VHD sont stockés en tant qu’objets blob de pages dans les comptes de stockage Azure.

- Disques gérés : Azure gère les comptes de stockage que vous utilisez pour vos disques de machine virtuelle. Vous spécifiez le type de disque (Premium ou Standard) et la taille de disque dont vous avez besoin. Azure crée et gère le disque pour vous.

- Disques de stockage Premium (les mieux adaptés aux charges de travail de production) : le stockage Premium prend en charge les disques de machines virtuelles pouvant être joints à des machines virtuelles de taille spécifique, notamment les séries DS, DSv2, GS et F. Le disque Premium est fourni dans différentes tailles. Vous pouvez choisir un disque de 32 Go à 4 096 Go. Chaque taille de disque a ses propres spécifications en matière de performances. Selon les besoins de votre application, vous pouvez associer un ou plusieurs disques à votre machine virtuelle.

Lorsque vous créez un disque géré à partir du portail, vous pouvez choisir le type de compte afin de spécifier le type de disque que vous souhaitez utiliser. Tous les disques disponibles n’apparaissent pas dans la zone de liste déroulante. En effet, le stockage Azure est intégré dans les machines virtuelles, et les limites sont définies en fonction des références (SKU) (par exemple, nombre maximal de disques, E/S par seconde maximales). Par conséquent, une fois que vous avez choisi une taille de machine virtuelle, seules les références de stockage Premium disponibles pour cette taille de machine virtuelle sont affichées.

![Capture d’écran de la page de disque géré](./media/oracle-design/premium_disk01.png)

Pour plus d’informations, consultez l’article portant sur [le stockage Premium Azure](https://docs.microsoft.com/azure/storage/storage-premium-storage).

Une fois que vous avez configuré votre stockage sur une machine virtuelle, vous pouvez tester la charge des disques avant de créer une base de données. Le fait de connaître la latence et le débit du taux d’E/S peut vous aider à déterminer si les machines virtuelles prennent en charge le débit attendu avec les cibles de latence.

Divers outils sont dédiés au test de charge d’application : Oracle Orion, Sysbench, Fio, etc.

Exécutez de nouveau le test de charge après avoir déployé la base de données Oracle et lancez vos charges de travail régulières et maximales. Les résultats vous permettent d’afficher la ligne de base de votre environnement.

Il peut être plus important de dimensionner le stockage en fonction du taux d’E/S par seconde que le stockage lui-même. Par exemple, si les E/S par seconde requises sont égales à 5 000, mais si vous n’avez besoin que de 200 Go. Vous pouvez tout de même obtenir le disque Premium de classe P30, même s’il offre plus de 200 Go de stockage.

Le taux d’E/S par seconde peut être obtenu à partir du rapport AWR. Il est déterminé par le journal de restauration par progression, les lectures physiques et les taux d’écriture.

![Capture d’écran de la page Rapport AWR](./media/oracle-design/awr_report.png)

Par exemple : la restauration par progression fait 12 200 000 octets par seconde, ce qui est égal à 11,63 Mbits/s. Le nombre d’E/S par seconde est égal à 12 200 000/2 358 = 5 174

Une fois que vous avez une vision claire des exigences en termes d’E/S, vous pouvez choisir une combinaison de disques mieux adaptée à ces exigences

Recommandations

- Pour l’espace disque de données, répartition de la charge de travail d’E/S sur plusieurs disques à l’aide du stockage géré ou d’Oracle ASM
- Lorsque la taille du bloc d’E/S augmente, pour les opérations de lecture et d’écriture intensives, ajout de disques de données
- Augmenter la taille de bloc pour les processus séquentiels volumineux
- Utiliser la compression de données pour réduire les E/S (données et index)
- Séparer les journaux de restauration par progression, système, temporaire et annuler le flux de transport sur des disques de données distincts
- Il est recommandé de ne pas placer de fichiers d’application sur le disque du système d’exploitation par défaut (/dev/sda). Ce disque est optimisé pour les démarrages rapides de machine virtuelle. Il peut ne pas fournir de performances optimales pour votre application.

### <a name="disk-cache-settings"></a>Paramètres de cache des disques

Il existe trois solutions pour la mise en cache de l’hôte.

- Lecture seule : toutes les demandes mises en cache pour les lectures ultérieures. Toutes les écritures conservées directement dans les objets blob de stockage Windows Azure

- Lecture-écriture : algorithme de type lecture anticipée. Les lectures et les écritures sont mises en cache pour les lectures ultérieures. Les écritures qui ne sont pas de type double écriture sont d’abord conservées dans le cache local. Pour SQL Server, les écritures sont conservées dans le stockage WA, car il utilise la double écriture. Il fournit la latence de disque la plus faible pour les charges de travail légères

- Aucun (désactivé) : cela vous permet de contourner le cache. Toutes les données sont transférées sur le disque et conservées dans le stockage Windows Azure. Cette méthode vous donne le taux d’E/S le plus élevé pour les charges de travail intensives d’E/S. Vous devez également tenir compte du coût de transaction.

Recommandations

Pour optimiser le débit, il est recommandé de commencer par Aucun pour la mise en cache de l’hôte. Pour le stockage Premium, vous devez désactiver les barrières lorsque vous montez le système de fichiers conformément aux options Lecture seule ou Aucun. Mettez à jour le fichier/etc/fstab avec l’UUID sur les disques.

Pour plus d’informations, consultez [Stockage Premium pour les machines virtuelles Linux](https://docs.microsoft.com/azure/storage/storage-premium-storage#premium-storage-for-linux-vms).

![Capture d’écran de la page de disque géré](./media/oracle-design/premium_disk02.png)

- Pour les disques du système d’exploitation, utilisez la mise en cache par défaut Lecture/écriture
- Pour SYSTEM, TEMP et UNDO, utilisez Aucun
- Pour DATA, utilisez Aucun, mais si votre base de données est en lecture seule ou en lecture intensive, utilisez la mise en cache en lecture seule

Une fois que votre configuration de disque de données est enregistrée, le paramètre de mise en cache de l’hôte (AFAIK) ne peut pas être modifié, sauf si vous démontez le disque au niveau du système d’exploitation, puis le remontez après modification.


## <a name="security"></a>Sécurité

Une fois que vous avez configuré votre environnement Azure. L’étape suivante consiste à sécuriser votre réseau. Voici quelques recommandations :

- Stratégie de groupe de sécurité réseau

Le groupe de sécurité réseau peut être défini par un sous-réseau ou une carte réseau.  Il est plus simple de contrôler la sécurité de l’accès au niveau du sous-réseau et de forcer le routage pour les éléments tels que les pare-feu d’application.

- Jumpbox ![Capture d’écran de la page relative à la topologie Jumpbox](./media/oracle-design/jumpbox.png)

Pour un accès plus sécurisé, les administrateurs ne doivent pas se connecter directement au service d’application ou à la base de données. Une jumpbox sert de média entre la machine de l’administrateur et les ressources Azure.

L’accès à la machine de l’administrateur doit être limité à l’adresse IP de la jumpbox. La jumpbox a alors accès à l’application/la base de données.

- Réseau privé (sous-réseaux)

Il est recommandé de disposer du service d’application et de la base de données sur des sous-réseaux séparés. La stratégie de groupe de sécurité réseau peut ainsi définir un meilleur contrôle.


## <a name="additional-readings"></a>Documentation supplémentaire :

- [Configurer Oracle ASM](configure-oracle-asm.md)
- [Implémenter Oracle Data Guard sur une machine virtuelle Linux Azure](configure-oracle-dataguard.md)
- [Implémenter Oracle Golden Gate sur une machine virtuelle Linux Azure](configure-oracle-golden-gate.md)
- [Sauvegarder et récupérer une base de données de la base de données Oracle Database 12c sur une machine virtuelle Linux Azure](oracle-backup-recovery.md)

## <a name="next-steps"></a>Étapes suivantes

[Didacticiel : créer des machines virtuelles hautement disponibles](../../linux/create-cli-complete.md)

[Explorer des exemples Azure CLI de déploiement de machines virtuelles](../../linux/cli-samples.md)

