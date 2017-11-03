---
title: "Installation de la haute disponibilité SAP NetWeaver sur un cluster de basculement Windows et un partage de fichiers pour une instance SAP (A)SCS sur Azure | Microsoft Docs"
description: "Installation de la haute disponibilité SAP NetWeaver sur un cluster de basculement Windows et un partage de fichiers pour une instance SAP (A)SCS"
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 71296618-673b-4093-ab17-b7a80df6e9ac
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ec7888cfb9b0d288b5b25f580c852ee32306684c
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2017
---
# <a name="sap-netweaver-ha-installation-on-windows-failover-cluster-and-file-share-for-sap-ascs-instance-on-azure"></a>Installation de la haute disponibilité SAP NetWeaver sur un cluster de basculement Windows et un partage de fichiers pour une instance SAP (A)SCS sur Azure

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1596496]:https://launchpad.support.sap.com/#/notes/1596496

[sap-installation-guides]:http://service.sap.com/instguides

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

[s2d-in-win-2016]:https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview
[sofs-overview]:https://technet.microsoft.com/library/hh831349(v=ws.11).aspx
[new-in-win-2016-storage]:https://docs.microsoft.com/windows-server/storage/whats-new-in-storage

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[sap-blog-new-sap-cluster-resource-dll]:https://blogs.sap.com/2017/08/28/new-sap-cluster-resource-dll-is-available/
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk-create-ascs-virt-host]:sap-high-availability-installation-wsfc-shared-disk.md#a97ad604-9094-44fe-a364-f89cb39bf097
[sap-high-availability-installation-wsfc-shared-disk-add-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md

[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-shared-disk-vpn]:sap-high-availability-infrastructure-wsfc-shared-disk.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-high-availability-infrastructure-wsfc-shared-disk-change-def-ilb]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-wsfc]:sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-high-availability-infrastructure-wsfc-shared-disk-add-dot-net]:sap-high-availability-infrastructure-wsfc-shared-disk.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios-both-nodes]:sap-high-availability-infrastructure-wsfc-shared-disk.md#dd41d5a2-8083-415b-9878-839652812102
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006

[sap-official-ha-file-share-document]:https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP multi-SID high-availability configuration)


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

Ce document décrit comment installer et configurer un système SAP à haute disponibilité sur Azure, avec un **cluster de basculement Windows (WSFC)** et le **partage de fichiers Scale Out** comme option pour le clustering d’instance SAP (A)SCS.

## <a name="prerequisites"></a>Composants requis

Veillez à consulter ces documents avant de commencer l’installation :

* [Guide d’architecture - Clustering d’instance SAP (A)SCS sur un **cluster de basculement Windows** à l’aide du **Partage de fichiers**][sap-high-availability-guide-wsfc-file-share]

* [Préparation d’infrastructure Azure pour la haute disponibilité SAP à l’aide de **cluster de basculement Windows** et de **partage de fichiers** pour une instance SAP (A)SCS][sap-high-availability-infrastructure-wsfc-file-share]


Vous avez besoin des exécutables / DLL suivants de SAP :
* Outil d’installation SAP **Software Provisioning Manager** (**SWPM**) version **SPS21 (ou ultérieure)**.
* Téléchargez l’archive **NTCLUST.SAR la plus récente** avec la nouvelle DLL de ressource de cluster SAP. Les nouvelles DLL de cluster SAP prennent en charge la haute disponibilité SAP (A)SCS avec partage de fichiers sur un cluster de basculement Windows Server.

  Pour plus d’informations sur la nouvelle DLL de ressource de cluster SAP, consultez ce billet de blog : [New SAP cluster resource DLL is available! (Disponibilité de la nouvelle DLL de ressource de cluster SAP)][sap-blog-new-sap-cluster-resource-dll]

Nous ne décrivons pas l’installation du système SGBD, car celle-ci varie en fonction du système utilisé. Toutefois, nous partons du principe que la haute disponibilité du SGBD est assurée par les fonctionnalités prises en charge par les différents fournisseurs de SGBD pour Azure, par exemple Always On ou la mise en miroir de base de données pour SQL Server, et Oracle Data Guard pour bases de données Oracle. Dans le scénario utilisé dans cet article, nous n’avons pas ajouté de protection supplémentaire au SGBD.

Il n’existe pas de considérations particulières lorsque différents services de SGBD interagissent avec ce type de configuration SAP ASCS/SCS en cluster dans Azure.

> [!NOTE]
> Les procédures d’installation de systèmes SAP NetWeaver ABAP, Java et ABAP+Java sont presque identiques. La différence la plus notable est qu’un système SAP ABAP comprend une instance ASCS, le système SAP Java une instance SCS, et le système SAP ABAP+Java une instance ASCS et une instance SCS exécutées dans le même groupe de cluster de basculement Microsoft. Toute différence d’installation pour chaque pile d’installation de SAP NetWeaver est mentionnée explicitement. Le reste de la procédure est identique.  
>
>

## <a name="install-ascs-instance-on-ascs-cluster"></a>Installer l’instance (A)SCS sur le cluster (A)SCS

> [!IMPORTANT]
>
>Actuellement, un paramètre de haute disponibilité avec une configuration de partage de fichiers n’est pas pris en charge par l’outil d’installation SAP Software Provisioning Manager (SWPM). Par conséquent, une adoption manuelle est nécessaire pour installer un système SAP, par exemple pour installer et mettre en cluster une instance SAP (A)SCS et configurer un SAP GLOBALHOST distinct.  
>
>Les autres étapes d’installation (et de mise en cluster) de l’instance de SGBD et des serveurs d’applications SAP restent inchangées.
>

### <a name="install-ascs-instance-on-local-drive"></a>Installer l’instance (A)SCS sur un lecteur local

Installez l’instance SAP (A)SCS sur **les deux** nœuds du cluster (A) SCS. Installez-la sur le lecteur **local**. Dans notre exemple, nous avons choisi le lecteur local C:\\. Vous pouvez choisir n’importe quel autre lecteur local.  

Pour effectuer l’installation, naviguez dans l’outil d’installation SAP SWPM vers :

&lt;Produit&gt; -> &lt;SGBD&gt; -> Installation -> Application Server ABAP (ou Java) -> Distributed System -> (A)SCS Instance

> [!IMPORTANT]
>Actuellement, le scénario de partage de fichiers n’est pas encore pris en charge par l’outil d’installation SAP SWPM. Vous **ne pouvez pas utiliser** le chemin d’installation :
>
>&lt;Produit&gt; -> &lt;SGBD&gt; -> Installation -> Application Server ABAP (or Java) -> High-Availability  System -> …
>

### <a name="remove-sapmnt-and-create-saploc-file-share"></a>Supprimer SAPMNT et créer le partage de fichiers SAPLOC

SWMP a créé un partage local SAPMNT dans le dossier C:\\usr\\sap.

Supprimez le partage de fichiers SAPMNT sur **les deux** nœuds de cluster (A)SCS :

Exécutez le script PowerShell suivant :

```PowerShell
Remove-SmbShare sapmnt -ScopeName * -Force
 ```

Si le partage SAPLOC n’existe pas, créez-le sur les deux nœuds de cluster ASC.

Exécutez le script PowerShell suivant :

```PowerShell
#Create SAPLOC share and set security
$SAPSID = "PR1"
$DomainName = "SAPCLUSTER"
$SAPSIDGlobalAdminGroupName = "$DomainName\SAP_" + $SAPSID + "_GlobalAdmin"
$HostName = $env:computername
$SAPLocalAdminGroupName = "$HostName\SAP_LocalAdmin"
$SAPDisk = "C:"
$SAPusrSapPath = "$SAPDisk\usr\sap"

New-SmbShare -Name saploc -Path c:\usr\sap -FullAccess "BUILTIN\Administrators", $SAPSIDGlobalAdminGroupName , $SAPLocalAdminGroupName  
 ```

## <a name="prepare-sap-global-host-on-sofs-cluster"></a>Préparer SAP GLOBAL HOST sur le cluster SOFS

Lors de cette étape, vous allez créer le partage de volume et de fichiers suivant sur le cluster SOFS :

* Structure C:\ClusterStorage\Volume1\usr\sap\\&lt;SID&gt;\SYS\ de fichier SAP GLOBALHOST sur le volume partagé de cluster (CSV) SOFS

* Créer le partage de fichiers SAPMNT

* Définiz la sécurité sur le partage de fichiers et de dossiers SAPMNT avec un contrôle total pour :
    * Groupe d’utilisateurs **&lt;DOMAINE&gt;\SAP_&lt;SID&gt;_GlobalAdmin**
    * **Objets ordinateur de nœud de cluster SAP (A)SCS &lt;DOMAINE&gt;\ClusterNode1$ et &lt;DOMAINE&gt;\ClusterNode2$**

Pour créer le volume partagé de cluster avec une résilience en miroir comme défini dans le chapitre **Prérequis SAP pour SOFS dans Azure - ADD LINK**, exécutez l’applet de commande PowerShell suivante sur l’un des nœuds de cluster SOFS :


```PowerShell
New-Volume -StoragePoolFriendlyName S2D* -FriendlyName SAPPR1 -FileSystem CSVFS_ReFS -Size 5GB -ResiliencySettingName Mirror
```
Pour créer SAPMNT et définir la sécurité de dossier et de partage, exécutez le script PowerShell suivant sur l’un des nœuds de cluster SOFS :

```PowerShell
# Create SAPMNT on file share
$SAPSID = "PR1"
$DomainName = "SAPCLUSTER"
$SAPSIDGlobalAdminGroupName = "$DomainName\SAP_" + $SAPSID + "_GlobalAdmin"

# SAP (A)SCS cluster nodes
$ASCSClusterNode1 = "ascs-1"
$ASCSClusterNode2 = "ascs-2"

# Define SAP (A)SCS cluster node computer objects
$ASCSClusterObjectNode1 = "$DomainName\$ASCSClusterNode1$"
$ASCSClusterObjectNode2 = "$DomainName\$ASCSClusterNode2$"

# Create usr\sap\.. folders on CSV
$SAPGlobalFolder = "C:\ClusterStorage\Volume1\usr\sap\$SAPSID\SYS"
New-Item -Path $SAPGlobalFOlder -ItemType Directory

$UsrSAPFolder = "C:\ClusterStorage\Volume1\usr\sap\"

# Create SAPMNT file share and set share security
New-SmbShare -Name sapmnt -Path $UsrSAPFolder -FullAccess "BUILTIN\Administrators", $SAPSIDGlobalAdminGroupName, $ASCSClusterObjectNode1, $ASCSClusterObjectNode2 -ContinuouslyAvailable $false -CachingMode None -Verbose

# Get SAPMNT file share security settings
Get-SmbShareAccess sapmnt

# Set files & folder security
$Acl = Get-Acl $UsrSAPFolder

# Add file security object of SAP_<sid>_GlobalAdmin group
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($SAPSIDGlobalAdminGroupName,"FullControl", 'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Add security object of clusternode1$ computer object
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($ASCSClusterObjectNode1,"FullControl",'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Add security object of clusternode2$ computer object
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($ASCSClusterObjectNode2,"FullControl",'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Set security
Set-Acl $UsrSAPFolder $Acl -Verbose
 ```
## <a name="stop-ascs-instances-and-sap-services"></a>Arrêter les instances (A)SCS et les services SAP

Effectuez les étapes suivantes :
* Arrêtez les instances SAP (A)SCS sur les deux nœuds de cluster (A)SCS.
* Arrêtez les services Windows SAP (A)SCS **SAP&lt;SID&gt;_&lt;numéro_instance&gt;** sur les deux nœuds de cluster.

## <a name="move-sys-folder-to-sofs-cluster"></a>Déplacer le dossier \SYS\.... vers le cluster SOFS

Effectuez les étapes suivantes :
* Copiez le dossier SYS (par exemple, C:\usr\sap\\&lt;SID&gt;\SYS) de l’un des nœuds de cluster (A)SCS vers le cluster SOFS (par exemple, C:\ClusterStorage\Volume1\usr\sap\\&lt;SID&gt;\SYS).
* Supprimez le dossier C:\usr\sap\\&lt;SID&gt;\SYS des deux nœuds de cluster (A)SCS.

## <a name="update-cluster-security-setting-on-sap-ascs-cluster"></a>Mettre à jour le paramètre de sécurité de cluster sur le cluster SAP (A)SCS

Exécutez le script PowerShell suivant sur l’un des nœuds de cluster SAP (A)SCS :

```PowerShell
# Grant <DOMAIN>\SAP_<SID>_GlobalAdmin group access to cluster

$SAPSID = "PR1"
$DomainName = "SAPCLUSTER"
$SAPSIDGlobalAdminGroupName = "$DomainName\SAP_" + $SAPSID + "_GlobalAdmin"

# Set full access for <DOMAIn>\SAP_<SID>_GlobalAdmin group
Grant-ClusterAccess -User $SAPSIDGlobalAdminGroupName -Full

#Check security settings
Get-ClusterAccess
```

## <a name="create-a-virtual-host-name-for-the-clustered-sap-ascs-instance"></a>Créer un nom d’hôte virtuel pour l’instance SAP (A)SCS en cluster

Comme décrit dans le chapitre [Créer un nom d’hôte virtuel pour l’instance SAP ASCS/SCS en cluster][sap-high-availability-installation-wsfc-shared-disk-create-ascs-virt-host], créez le nom réseau du cluster SAP (A)SCS (par exemple, **pr1-ASC [10.0.6.7]**).

## <a name="update-default-and-sap-ascs-instance-profile"></a>Mettre à jour le profil d’Instance SAP (A)SCS et DEFAULT

Vous devez mettre à jour le profil d’instance SAP (A)SCS et DEFAULT &lt;SID&gt;_(A)SCS<Nr>_<Host> de façon à utiliser :

* Le nouveau nom d’hôte virtuel SAP (A)SCS

* Le nouveau nom d’hôte SAP GLOBAL


| Anciennes valeurs |  |
| --- | --- |
| Nom d’hôte SAP (A)SCS = hôte global SAP | ascs-1 |
| Nom du profil d’instance SAP (A)SCS | PR1_ASCS00_ascs-1 |

| Nouvelles valeurs |  |
| --- | --- |
| Nom d’hôte SAP (A)SCS | **pr1-ascs** |
| Hôte global SAP | **sapglobal** |
| Nom du profil d’instance SAP (A)SCS | PR1\_ASCS00\_**pr1-ascs** |

### <a name="update-sap-default-profile"></a>Mettre à jour le profil SAP DEFAULT


| Nom du paramètre | Valeur du paramètre |
| --- | --- |
| SAPGLOBALHOST | **sapglobal** |
| rdisp/mshost | **pr1-ascs** |
| enque/serverhost | **pr1-ascs** |

### <a name="update-sap-ascs-instance-profile"></a>Mettre à jour le profil d’Instance SAP (A)SCS

| Nom du paramètre | Valeur du paramètre |
| --- | --- |
| SAPGLOBALHOST | **sapglobal** |
| DIR_PROFILE | \\\\**sapglobal**\sapmnt\PR1\SYS\profile |
| _PF | $(DIR_PROFILE)\PR1\_ASCS00_ pr1-ascs |
| Restart_Program_02 = local$(_MS) pf=$(_PF) | **Start**_Program_02 = local$(_MS) pf=$(_PF) |
| SAPLOCALHOST | **pr1-ascs** |
| Restart_Program_03 = local$(_EN) pf=$(_PF) | **Start**_Program_03 = local$(_EN) pf=$(_PF) |
| gw/netstat_once | **0** |
| enque/encni/set_so_keepalive  | **true** |
| service/ha_check_node | **1** |

> [!IMPORTANT]
>Vous pouvez utiliser l’applet de commande PowerShell **Update-SAPASCSSCSProfile** pour automatiser la mise à jour du profil.
>
>L’applet de commande PowerShell prend en charge les instances SAP ABAP ASCS et SAP Java SCS.
>

Copiez **SAPScripts.ps1** vers le lecteur local C:\tmp et exécutez l’applet de commande PowerShell suivante :

```PowerShell
Import-Module C:\tmp\SAPScripts.ps1

Update-SAPASCSSCSProfile -PathToAscsScsInstanceProfile \\sapglobal\sapmnt\PR1\SYS\profile\PR1_ASCS00_ascs-1 -NewASCSHostName pr1-ascs -NewSAPGlobalHostName sapglobal -Verbose  
```

![Figure 1 : Sortie de SAPScripts.ps1][sap-ha-guide-figure-8012]

_**Figure 1 :** Sortie de SAPScripts.ps1_

## <a name="update-ltsidgtadm-user-environment-variable"></a>Mettre à jour la variable d’environnement utilisateur &lt;sid&gt;adm

Mettez à jour le nouveau chemin UNC GLOBALHOST d’environnement utilisateur &lt;sid&gt;adm sur les deux nœuds de cluster (A)SCS.
Ouvrez une session en tant qu’utilisateur &lt;sid&gt;adm et démarrez l’outil Regedit.exe.
Accédez à **HKEY_CURRENT_USER** -> **Environment** et mettez à jour les variables :

| Variable | Valeur |
| --- | --- |
| RSEC_SSFS_DATAPATH | \\\\**sapglobal**\sapmnt\PR1\SYS\global\security\rsecssfs\data |
| RSEC_SSFS_KEYPATH | \\\\**sapglobal**\sapmnt\PR1\SYS\global\security\rsecssfs\key |
| SAPEXE | \\\\**sapglobal**\sapmnt\PR1\SYS\exe\uc\NTAMD64 |
| SAPLOCALHOST  | **pr1-ascs** |


## <a name="install-new-saprcdll"></a>Installer la nouvelle SAPRC.DLL

Vous devez installer une nouvelle version de la ressource de cluster SAP qui prend en charge le scénario de partage de fichiers.

Téléchargez le package **NTCLUST. SAR** le plus récent à partir de la Place de Marché SAP Service.

Décompressez NTCLUS.SAR sur l’un des nœuds de cluster (A)SCS et exécutez la commande suivante à partir de l’invite de commandes pour installer la nouvelle saprc.dll :

```
.\NTCLUST\insaprct.exe -yes -install
```

La nouvelle saprc.dll sera installée sur les deux nœuds de cluster (A)SCS.

Pour plus d’informations, consultez [SAP Note 1596496 - How to update SAP Resource Type DLLs for Cluster Resource Monitor (Guide pratique pour mettre à jour des DLL de type de ressource SAP pour le moniteur de ressource du cluster][1596496].

## <a name="create-sap-sid-cluster-group-network-name-and-ip"></a>Créer une adresse IP, un nom réseau et un groupe de clusters SAP <SID>

Vous devez créer :

* Un groupe de clusters SAP &lt;SID&gt;
* <nom_réseau(A)SCS>
* et l’adresse IP correspondante.

Exécutez l’applet de commande PowerShell suivante :

```PowerShell
# Create SAP Cluster Group
$SAPSID = "PR1"
$SAPClusterGroupName = "SAP $SAPSID"
$SAPIPClusterResourceName = "SAP $SAPSID IP"
$SAPASCSNetworkName = "pr1-ascs"
$SAPASCSIPAddress = "10.0.6.7"
$SAPASCSSubnetMask = "255.255.255.0"

# Create SAP ASCS instance Virtual IP cluster resource
Add-ClusterGroup -Name $SAPClusterGroupName -Verbose

#Create SAP ASCS Virtual IP Address
$SAPIPClusterResource = Add-ClusterResource -Name $SAPIPClusterResourceName -ResourceType "IP Address" -Group $SAPClusterGroupName -Verbose

# Set static IP Address
$param1 = New-Object Microsoft.FailoverClusters.PowerShell.ClusterParameter $SAPIPClusterResource,Address,$SAPASCSIPAddress
$param2 = New-Object Microsoft.FailoverClusters.PowerShell.ClusterParameter $SAPIPClusterResource,SubnetMask,$SAPASCSSubnetMask
$params = $param1,$param2
$params | Set-ClusterParameter

# Create corresponding network name
$SAPNetworkNameClusterResourceName = $SAPASCSNetworkName
Add-ClusterResource -Name $SAPNetworkNameClusterResourceName -ResourceType "Network Name" -Group $SAPClusterGroupName -Verbose

# Set Network DNS Name
$SAPNetworkNameClusterResource = Get-ClusterResource $SAPNetworkNameClusterResourceName
$SAPNetworkNameClusterResource | Set-ClusterParameter -Name Name -Value $SAPASCSNetworkName

#Check the updated values
$SAPNetworkNameClusterResource | Get-ClusterParameter

#Set resource dependencies
Set-ClusterResourceDependency -Resource $SAPNetworkNameClusterResourceName -Dependency "[$SAPIPClusterResourceName]" -Verbose

#Start SAP <SID> Cluster Group
Start-ClusterGroup -Name $SAPClusterGroupName -Verbose
```

## <a name="register-sap-start-service-on-both-nodes"></a>Inscrire le service de démarrage SAP sur les deux nœuds

Vous devez réinscrire le service sapstart SAP (A)SCS pour qu’il pointe vers le nouveau profil et le nouveau chemin de profil.

Vous devez exécuter cela sur les deux nœuds de cluster (A)SCS.

Exécutez la commande suivante à partir d’une invite de commandes avec élévation de privilèges :

```
C:\usr\sap\PR1\ASCS00\exe\sapstartsrv.exe -r -p \\sapglobal\sapmnt\PR1\SYS\profile\PR1_ASCS00_pr1-ascs -s PR1 -n 00 -U SAPCLUSTER\SAPServicePR1 -P mypasswd12 -e SAPCLUSTER\pr1adm
```

![Figure 2 : Réinstaller le service SAP][sap-ha-guide-figure-8013]

_**Figure 2 :** Réinstaller le service SAP_

Vérifiez que les paramètres sont corrects et choisissez **Manuel** comme Type de démarrage.

## <a name="stop-ascs-service"></a>Arrêter le service (A)SCS

Arrêtez le service SAP (A)SCS **SAP&lt;SID&gt;_&lt;numéro_instance&gt;** sur les deux nœuds de cluster (A)SCS.

## <a name="create-new-sap-service-and-sap-instance-resources"></a>Créer le nouveau service SAP et les nouvelles ressources d’instance SAP

Vous devez maintenant finaliser la création des ressources du groupe de clusters SAP SAP&lt;SID&gt;. Vous devez par exemple créer les ressources :

* **SAP &lt;SID&gt; &lt;numéro_instance&gt; Service** et
* **SAP &lt;SID&gt; &lt;numéro_instance&gt; Instance**

Exécutez l’applet de commande PowerShell suivante :

```PowerShell
$SAPSID = "PR1"
$SAPInstanceNumber = "00"
$SAPNetworkNameClusterResourceName = "pr1-ascs"

$SAPServiceName = "SAP$SAPSID"+ "_" + $SAPInstanceNumber

$SAPClusterGroupName = "SAP $SAPSID"
$SAPServiceClusterResourceName = "SAP $SAPSID $SAPInstanceNumber Service"

$SAPASCSServiceClusterResource = Add-ClusterResource -Name $SAPServiceClusterResourceName -Group $SAPClusterGroupName -ResourceType "SAP Service" -SeparateMonitor -Verbose
$SAPASCSServiceClusterResource  | Set-ClusterParameter  -Name ServiceName -Value $SAPServiceName

#Set resource dependencies
Set-ClusterResourceDependency -Resource $SAPASCSServiceClusterResource  -Dependency "[$SAPNetworkNameClusterResourceName]" -Verbose

$SAPInstanceClusterResourceName = "SAP $SAPSID $SAPInstanceNumber Instance"

# Create SAP Instance cluster resource
$SAPASCSServiceClusterResource = Add-ClusterResource -Name $SAPInstanceClusterResourceName -Group $SAPClusterGroupName -ResourceType "SAP Resource" -SeparateMonitor -Verbose

#Set SAP Instance cluster resource parameters
$SAPASCSServiceClusterResource  | Set-ClusterParameter  -Name SAPSystemName -Value $SAPSID -Verbose
$SAPASCSServiceClusterResource  | Set-ClusterParameter  -Name SAPSystem -Value $SAPInstanceNumber -Verbose

#Set resource dependencies
Set-ClusterResourceDependency -Resource $SAPASCSServiceClusterResource  -Dependency "[$SAPServiceClusterResourceName]" -Verbose
```

## <a name="add-a-probe-port"></a>Ajouter un port de sondage

Lors de cette étape, vous allez configurer le port de sonde SAP-SID-IP d’une ressource de cluster SAP à l’aide de PowerShell. Exécutez cette configuration sur un des nœuds de cluster SAP ASCS/SCS, comme décrit [ici][sap-high-availability-installation-wsfc-shared-disk-add-probe-port].

## <a name="install-ers-instance-on-both-cluster-nodes"></a>Installer l’instance ERS sur les deux nœuds de cluster

À l’étape suivante, vous devez installer l’instance ERS (Enqueue Replication Server) sur les deux nœuds du cluster (A)SCS.
L’option d’installation se trouve dans le menu SWPM :

&lt;Produit&gt; -> &lt;SGBD&gt; -> Installation -> Additional SAP System instances -> **Enqueue Replication Server Instance**

## <a name="install-dbms-instance-and-sap-application-servers"></a>Installer l’instance de SGBD et les serveurs d’applications SAP

Finalisez l’installation de votre système SAP en installant :
* L’instance de SGBD
* Le serveur d’applications SAP principal
* Le serveur d’applications SAP supplémentaire

## <a name="next-steps"></a>Étapes suivantes

* [Installation of an (A)SCS Instance on a Failover Cluster with no Shared Disks - Official SAP Guidelines for HA File Share (Installation d’une Instance (A)SCS sur un cluster de basculement sans disques partagés - Recommandations SAP officielles pour le partage de fichiers hautement disponible)][sap-official-ha-file-share-document]

* [Espaces de stockage direct dans Windows Server 2016][s2d-in-win-2016]

* [Vue d’ensemble des serveurs de fichiers avec montée en puissance parallèle pour les données d’application][sofs-overview]

* [Nouveautés du stockage dans Windows Server 2016][new-in-win-2016-storage]
