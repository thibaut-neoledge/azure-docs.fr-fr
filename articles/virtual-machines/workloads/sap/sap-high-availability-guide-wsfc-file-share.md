---
title: "Clustering d’instance SAP (A)SCS sur un cluster de basculement Windows à l’aide du Partage de fichiers sur Azure| Microsoft Docs"
description: "Clustering d’instance SAP (A)SCS sur un cluster de basculement Windows à l’aide du Partage de fichiers"
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 472c3f35e2ae32550be62826407689f93101041f
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2017
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[kb4025334]:https://support.microsoft.com/help/4025334/windows-10-update-kb4025334

[dv2-series]:https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dv2-series
[ds-series]:https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#ds-series

[sap-installation-guides]:http://service.sap.com/instguides

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-volumes-s2d-choosing-filesystem]:https://docs.microsoft.com/windows-server/storage/storage-spaces/plan-volumes#choosing-the-filesystem
[choosing-the-size-of-volumes-s2d]:https://docs.microsoft.com/windows-server/storage/storage-spaces/plan-volumes#choosing-the-size-of-volumes
[deploy-sofs-s2d-in-azure]:https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment
[s2d-in-win-2016]:https://docs.microsoft.com/en-us/windows-server/storage/storage-spaces/storage-spaces-direct-overview
[deep-dive-volumes-in-s2d]:https://blogs.technet.microsoft.com/filecab/2016/08/29/deep-dive-volumes-in-spaces-direct/

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP multi-SID high-availability configuration)

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png


[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png


[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


# <a name="clustering-sap-ascs-instance-on-windows-failover-cluster-using-file-share-on-azure"></a>Clustering d’instance SAP (A)SCS sur un cluster de basculement Windows à l’aide du Partage de fichiers sur Azure

> ![Windows][Logo_Windows] Windows
>

Le clustering de basculement Windows Server constitue la base d’une installation de SGBD et de SAP ASCS/SCS à haute disponibilité dans Windows.

Un cluster de basculement est un groupe de 1 + n serveurs indépendants (nœuds) qui fonctionnent ensemble pour accroître la disponibilité des applications et des services. En cas d’échec d’un nœud, le clustering de basculement Windows Server calcule le nombre d’échecs qui peuvent se produire tout en maintenant l’intégrité du cluster afin d’être en mesure de fournir les applications et les services. Différents modes de quorum sont disponibles pour obtenir un clustering de basculement.

## <a name="prerequisite"></a>Configuration requise
Veillez à consulter ces documents avant de commencer la lecture du présent article :

* [Scénarios et architecture de haute disponibilité de machines virtuelles Azure pour SAP NetWeaver][sap-high-availability-architecture-scenarios]

> [!IMPORTANT]
>Le clustering d’instances SAP (A)SCS avec le partage de fichiers est pris en charge pour les produits **SAP NetWeaver 7.40 (et version supérieure)**, avec **SAP Kernel 7.49 (et version supérieure)**.
>


## <a name="windows-server-failover-clustering-in-azure"></a>Clustering de basculement Windows Server dans Azure

Par rapport aux déploiements complets ou de cloud privé, le service Machines virtuelles Azure requiert des étapes supplémentaires pour configurer le clustering de basculement Windows Server. Quand vous créez un cluster, vous devez définir plusieurs adresses IP et noms d’hôtes virtuels pour l’instance SAP ASCS/SCS.

### <a name="name-resolution-in-azure-and-cluster-virtual-host-name"></a>Résolution de noms dans Azure et nom d’hôte de cluster virtuel

La plateforme cloud Azure ne permet pas de configurer des adresses IP virtuelles telles que des adresses IP flottantes. Vous avez besoin d’une autre solution pour configurer une adresse IP virtuelle afin d’atteindre la ressource de cluster dans le cloud. Azure offre un **équilibreur de charge interne** dans le cadre du service Azure Load Balancer. Avec l’équilibrage de charge interne, les clients atteignent le cluster via son adresse IP virtuelle. Vous devez déployer l’équilibrage de charge interne dans le groupe de ressources qui contient les nœuds de cluster. Ensuite, configurez toutes les règles de réacheminement de port nécessaires avec les ports de sondage de l’équilibrage de charge interne. Les clients peuvent se connecter avec le nom d’hôte virtuel. Le serveur DNS résout l’adresse IP du cluster et l’équilibrage de charge interne gère le réacheminement de port vers le nœud actif du cluster.

![Figure 1 : Configuration du clustering de basculement Windows Server dans Azure sans disque partagé][sap-ha-guide-figure-1001]

_**Figure 1 :** Configuration du clustering de basculement Windows Server dans Azure sans disque partagé_

## <a name="sap-ascs-ha-with-file-share"></a>Haute disponibilité SAP (A)SCS avec le Partage de fichiers

Nouvelle approche et alternative développée par SAP pour les disques partagés de cluster et les instances SAP (A)SCS de cluster sur un cluster de basculement Windows.

Nous utilisons ici un **partage de fichiers SMB**. Il s’agit d’une option de déploiement de **fichiers SAP GLOBAL HOST**.

> [!NOTE]
>Le partage de fichiers SMB est une option supplémentaire pour les disques partagés de cluster en vue d’un clustering d’instances SAP (A)SCS.  
>

Voici les spécificités de cette architecture :

* **Services centraux SAP (avec une structure, des messages et des processus d’empilement de fichiers propres) séparés des fichiers SAP GLOBAL HOST**
* **Services centraux SAP s’exécutant sous une instance SAP (A)SCS**
* Une instance SAP (A)SCS est mise en cluster et accessible via le nom de l’hôte virtuel **<(A)SCSVirtualHostName>**
* Les fichiers SAP GLOBAL sont placés sur le partage de fichiers SMB et sont accessibles via le <SAPGLOBALHost>nom d’hôte\\ \\&lt;SAPGLOBALHost&gt;\sapmnt\\&lt;SID&gt;\SYS\..
* L’instance SAP (A)SCS est installée sur un disque local sur les deux nœuds de cluster
* Le nom de réseau **<(A)SCSVirtualHostName>** est différent de **<SAPGLOBALHost>**

![Figure 2 : Nouvelle architecture à haute disponibilité SAP (A)SCS avec le partage de fichiers SMB][sap-ha-guide-figure-8004]

_**Figure 2 :** Nouvelle architecture à haute disponibilité SAP (A)SCS avec le partage de fichiers SMB_

Conditions préalables pour le partage de fichiers SMB :

* Protocole SMB 3.0 (ou version ultérieure)
* Possibilité de définir la liste de contrôle d’accès (ACL) d’Azure Active Directory (AD) pour des **groupes d’utilisateurs AD** et des **objets ordinateur**
* La haute disponibilité doit être activée pour le partage de fichiers :
    * Les disques utilisés pour stocker des fichiers ne doivent pas constituer un point de défaillance unique
    * Veillez à ce que le temps d’arrêt des serveurs et des machines virtuelles n’entraîne pas celui du partage de fichiers

Le rôle de cluster **SAP &lt;SID&gt;** ne contient aucun disque partagé de cluster ni de ressources de cluster de fichiers partagés génériques.


![Figure 3 : ressources du rôle de cluster SAP <SID> lors de l’utilisation du partage de fichiers][sap-ha-guide-figure-8005]

_**Figure 3 :** ressources du rôle de cluster **SAP &lt;SID&gt;** lors de l’utilisation du partage de fichiers_


## <a name="scale-out-file-share-sofs-with-storage-spaces-direct-s2d-on-azure-as-sapmnt-file-share"></a>Dimensionnement du partage de fichiers (SOFS) avec Storage Spaces Direct sur Azure en tant que partage de fichiers SAPMNT

Vous pouvez utiliser SOFS pour héberger et protéger des fichiers SAP GLOBAL HOST et pour offrir un service à haute disponibilité de partage de fichiers SAPMNT.

![Figure 4 : le partage de fichiers SOFS permet de protéger les fichiers SAP GLOBAL HOST][sap-ha-guide-figure-8006]

_**Figure 4 :** le partage de fichiers SOFS permet de protéger les fichiers SAP GLOBAL HOST_

> [!IMPORTANT]
>Le partage de fichiers SOFS est entièrement pris en charge sur le cloud Microsoft Azure ainsi que sur des environnements locaux.
>

**SOFS** offre un partage de fichiers SAPMNT hautement disponible et à évolution horizontale.

**Storage Spaces Direct (S2D)** sert de **disque partagé** à SOFS, et permet de générer un stockage hautement disponible et à évolution horizontale à l’aide de serveurs à stockage local. Ainsi, le stockage partagé utilisé pour SOFS (fichiers SAP GLOBAL HOST, par exemple) ne constitue pas un point de défaillance unique.

> [!IMPORTANT]
>Si vous prévoyez de configurer la récupération d’urgence, SOFS est la solution conseillée pour un partage de fichiers à haute disponibilité dans Azure.
>

### <a name="sap-prerequisites-for-sofs-in-azure"></a>Conditions préalables SAP pour SOFS dans Azure

Pour SOFS, il vous faut :

* Minimum deux nœuds de cluster pour SOFS

* Chaque nœud doit disposer d’au moins deux disques locaux

* Pour des raisons de performances, vous devez utiliser la **mise en miroir de la résilience** :
    * Mise en miroir à **deux voies** pour SOFS, avec deux nœuds de cluster
    * Mise en miroir à **trois voies** pour SOFS, avec trois nœuds de cluster (ou plus)


* Il est **recommandé d’avoir au moins 3 nœuds de cluster pour SOFS avec mise en miroir à 3 voies**.
Cette configuration offre une meilleure extensibilité et une meilleure résilience du stockage que la configuration SOFS avec deux nœuds de cluster et une mise en miroir à deux voies.

* Vous devez utiliser un **disque Azure Premium**

* Nous vous **recommandons** d’utiliser des **disques gérés Azure Premium**

* Nous vous **recommandons** de formater les volumes avec un nouveau **système de fichiers résilient (ReFS)**
    * [Note SAP 1869038 : Prise en charge SAP pour système de fichiers ReFS][1869038]
    * Consultez le chapitre [Choisir le système de fichiers][planning-volumes-s2d-choosing-filesystem] de Planifier les volumes dans Storage Spaces Direct.
    * Veillez à installer cette mise à jour cumulative [MS **KB4025334**][kb4025334].


* Vous pouvez utiliser les tailles de machines virtuelles Azure **séries DS** ou **séries DSv2**

* Pour avoir de bonnes performances entre réseau de machine virtuelle pour une synchronisation de disque Storage Spaces Direct, vous devez utiliser un type de machine virtuelle disposant d’au moins une **bande passante réseau élevée**.
Pour en savoir plus, consultez les spécifications des [Séries DSv2][dv2-series] et des [Séries DS][ds-series].

* Nous vous **recommandons** de laisser et de **réserver de la place dans le pool de stockage non alloué**. Ceci donnera un certain volume d’espace pour réparer « sur place » en cas d’échec du disque, ce qui améliore la sécurité des données et les performances.

 Pour en savoir plus, consultez [Choisir la taille des volumes][choosing-the-size-of-volumes-s2d]


* Les machines virtuelles Azure SOFS doivent être déployées dans leur **propre groupe à haute disponibilité Azure**

* Il est inutile de configurer un équilibreur de charge interne Azure pour un nom de réseau de partage de fichiers SOFS, par exemple <SAPGlobalHostName>, comme c’est le cas pour <(A)SCSVirtualHostname> de l’instance SAP (A)SCS ou pour le DBMS. SOFS répartit les charges sur tous les nœuds de cluster, afin que <SAPGlobalHostName> utilise leur adresse IP locale.


> [!IMPORTANT]
>Le partage de fichiers SAPMNT qui pointe vers SAP GLOBAL HOST ne peut être modifié. Contrairement à SAPMNT, SAP ne prend pas en charge de nom de partage différent.
>[Note SAP 2492395 : Le nom de partage SAPMNT peut-il être modifié ?][2492395]

### <a name="configuring-sap-ascs-instances-and-sofs-in-two-clusters"></a>Configuration des instances SAP (A)SCS et SOFS dans deux clusters

Vous pouvez déployer des instances SAP(A)SCSS dans un cluster, avec leur propre rôle de cluster <SID> SAP. Le partage de fichiers SOFS est configuré dans un cluster différent avec un rôle de cluster différent.

> [!IMPORTANT]
>Dans ce scénario, l’instance SAP (A)SCS est configurée pour accéder SAP GLOBAL HOST à l’aide du chemin d’accès UNC \\\\&lt;SAPGLOBALHOST&gt;\sapmnt\\&lt;SID&gt;\SYS\...
>

![Figure 5 : Instance SAP (A)SCS et SOFS déployés dans deux clusters][sap-ha-guide-figure-8007]

_**Figure 5 :** Instance SAP (A)SCS et SOFS déployés dans deux clusters_

> [!IMPORTANT]
>Sur le cloud Azure, chaque cluster utilisé pour les partages de fichiers SAP et SOFS doit être déployé dans leur groupe à haute disponibilité Azure, afin d’assurer la répartition du placement de machines virtuelles de cluster sur l’infrastructure Azure sous-jacente.
>

## <a name="generic-file-share-with-sios-as-cluster-shared-disks"></a>Partage de fichiers générique avec SIOS en tant que disques partagés de cluster


> [!IMPORTANT]
>SOFS est la solution conseillée pour un partage de fichiers à haute disponibilité.
>
>Toutefois, si vous prévoyez aussi de configurer **la récupération d’urgence** à votre partage de fichiers à haute disponibilité, vous devez utiliser un partage de fichiers générique et SIOS pour vos disques partagés de cluster.
>

Le partage de fichiers générique constitue une alternative au partage de fichiers à haute disponibilité.

Vous pouvez utiliser une solution SIOS tiers comme disque partagé de cluster.

## <a name="next-steps"></a>Étapes suivantes

* [Préparation d’infrastructure Azure pour la haute disponibilité SAP à l’aide de cluster de basculement Windows et de partage de fichiers pour une instance SAP (A)SCS][sap-high-availability-infrastructure-wsfc-file-share]

* [Installation de la haute disponibilité SAP NetWeaver sur un cluster de basculement Windows et un partage de fichiers pour une instance SAP (A)SCS][sap-high-availability-installation-wsfc-shared-disk]

* [Déployer un serveur de fichiers à deux nœuds Storage Spaces Direct réparti pour le stockage UPD dans Azure][deploy-sofs-s2d-in-azure]

* [Storage Spaces Direct dans Windows Server 2016][s2d-in-win-2016]

* [Présentation approfondie : les volumes dans Storage Spaces Direct][deep-dive-volumes-in-s2d]
