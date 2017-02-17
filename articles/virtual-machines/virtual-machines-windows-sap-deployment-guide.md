---
title: "SAP NetWeaver sur machines virtuelles Azure – Guide de déploiement | Microsoft Docs"
description: "SAP NetWeaver sur machines virtuelles Azure – Guide de déploiement"
services: virtual-machines-windows
documentationcenter: 
author: MSSedusch
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 22aaa98c-bb9f-472f-b546-0b294e033cda
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/08/2016
ms.author: sedusch
translationtype: Human Translation
ms.sourcegitcommit: 3b3250c17f0086ee0fd25b5c7fb8822eb52a26f8
ms.openlocfilehash: 25865776748ae0605310eb86aefca2afebac2799


---
# <a name="sap-netweaver-on-azure-virtual-machines-vms--deployment-guide"></a>SAP NetWeaver sur machines virtuelles Azure – Guide de déploiement
[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]:https://launchpad.support.sap.com/#/notes/1619967
[1750510]:https://launchpad.support.sap.com/#/notes/1750510
[1752266]:https://launchpad.support.sap.com/#/notes/1752266
[1757924]:https://launchpad.support.sap.com/#/notes/1757924
[1757928]:https://launchpad.support.sap.com/#/notes/1757928
[1758182]:https://launchpad.support.sap.com/#/notes/1758182
[1758496]:https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2367194]:https://launchpad.support.sap.com/#/notes/2367194

[azure-cli]:../xplat-cli-install.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:virtual-machines-windows-sap-dbms-guide.md (SAP NetWeaver sur les machines virtuelles Azure – Guide de déploiement SGBD) [dbms-guide-2.1]:virtual-machines-windows-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (Mise en cache pour les machines virtuelles et les disques durs virtuels) [dbms-guide-2.2]:virtual-machines-windows-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (RAID logiciel) [dbms-guide-2.3]:virtual-machines-windows-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Stockage Microsoft Azure) [dbms-guide-2]:virtual-machines-windows-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (Structure d’un déploiement SGBDR) [dbms-guide-3]:virtual-machines-windows-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (Haute disponibilité et récupération d’urgence avec les machines virtuelles Azure) [dbms-guide-5.5.1]:virtual-machines-windows-sap-dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 et versions ultérieures) [dbms-guide-5.5.2]:virtual-machines-windows-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 et versions antérieures) [dbms-guide-5.6]:virtual-machines-windows-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (Utilisation d’images SQL Server issues de la Place de marché Microsoft Azure) [dbms-guide-5.8]:virtual-machines-windows-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (Résumé - SQL Server général pour SAP sur Azure) [dbms-guide-5]:virtual-machines-windows-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (Caractéristiques de SGBDR SQL Server) [dbms-guide-8.4.1]:virtual-machines-windows-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (Configuration du stockage) [dbms-guide-8.4.2]:virtual-machines-windows-sap-dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (Sauvegarde et restauration) [dbms-guide-8.4.3]:virtual-machines-windows-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (Considérations sur les performances de sauvegarde et de restauration) [dbms-guide-8.4.4]:virtual-machines-windows-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (Autres) [dbms-guide-900-sap-cache-server-on-premises]:virtual-machines-windows-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:./media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:./media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:./media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:./media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:./media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:./media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:./media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:./media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:./media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:virtual-machines-windows-sap-deployment-guide.md (SAP NetWeaver sur machines virtuelles Azure – Guide de déploiement) [deployment-guide-2.2]:virtual-machines-windows-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (Ressources SAP) [deployment-guide-3.1.2]:virtual-machines-windows-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (Déploiement d’une machine virtuelle avec une image personnalisée) [deployment-guide-3.2]:virtual-machines-windows-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (Scénario 1 : Déploiement d’une machine virtuelle provenant d’Azure Marketplace pour SAP) [deployment-guide-3.3]:virtual-machines-windows-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (Scénario 2 : Déploiement d’une machine virtuelle avec une image personnalisée pour SAP) [deployment-guide-3.4]:virtual-machines-windows-sap-deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (Scénario 3 : Déplacement d’une machine virtuelle locale à l’aide d’un disque dur virtuel Azure non généralisé avec SAP) [deployment-guide-3]:virtual-machines-windows-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Scénarios de déploiement de machines virtuelles pour SAP sur Microsoft Azure) [deployment-guide-4.1]:virtual-machines-windows-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Déploiement d’applets de commande Azure PowerShell) [deployment-guide-4.2]:virtual-machines-windows-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (Téléchargement et importation d’applets de commande PowerShell appropriées pour SAP) [deployment-guide-4.3]:virtual-machines-windows-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (Joindre une machine virtuelle au domaine local – Windows uniquement) [deployment-guide-4.4.2]:virtual-machines-windows-sap-deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux) [deployment-guide-4.4]:virtual-machines-windows-sap-deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Télécharger, installer et activer l’agent de machine virtuelle Azure) [deployment-guide-4.5.1]:virtual-machines-windows-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell) [deployment-guide-4.5.2]:virtual-machines-windows-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Interface de ligne de commande Azure) [deployment-guide-4.5]:virtual-machines-windows-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (Configurer l’extension d’analyse Azure améliorée pour SAP) [deployment-guide-5.1]:virtual-machines-windows-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Vérification de la disponibilité de l’analyse Azure améliorée pour SAP) [deployment-guide-5.2]:virtual-machines-windows-sap-deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Contrôle d’intégrité de la configuration de l’infrastructure d’analyse Azure) [deployment-guide-5.3]:virtual-machines-windows-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (Étapes supplémentaires de la résolution des problèmes de l’infrastructure d’analyse Azure pour SAP)

[deployment-guide-configure-monitoring-scenario-1]:virtual-machines-windows-sap-deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (Configure Monitoring)
[deployment-guide-configure-proxy]:virtual-machines-windows-sap-deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (Configure Proxy)
[deployment-guide-figure-100]:./media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:./media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:virtual-machines-windows-sap-deployment-guide.md#figure-11
[deployment-guide-figure-1100]:./media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:./media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:./media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:virtual-machines-windows-sap-deployment-guide.md#figure-14
[deployment-guide-figure-1400]:./media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:./media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:./media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:virtual-machines-windows-sap-deployment-guide.md#figure-5
[deployment-guide-figure-50]:./media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:./media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:virtual-machines-windows-sap-deployment-guide.md#figure-6
[deployment-guide-figure-600]:./media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:virtual-machines-windows-sap-deployment-guide.md#figure-7
[deployment-guide-figure-700]:./media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:./media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:./media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:virtual-machines-windows-sap-deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:virtual-machines-windows-sap-deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:virtual-machines-windows-sap-deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:virtual-machines-windows-sap-deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Checks and Troubleshooting for End-to-End Monitoring Setup for SAP on Azure)

[deploy-template-cli]:../azure-resource-manager/resource-group-template-deploy-cli.md
[deploy-template-portal]:../azure-resource-manager/resource-group-template-deploy-portal.md
[deploy-template-powershell]:../azure-resource-manager/resource-group-template-deploy.md#deploy-with-powershell

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:virtual-machines-windows-sap-get-started.md
[getting-started-dbms]:virtual-machines-windows-sap-get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:virtual-machines-windows-sap-get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:virtual-machines-windows-sap-get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:http://go.microsoft.com/fwlink/?LinkId=613056

[ha-guide]:virtual-machines-windows-sap-high-availability-guide.md

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:./media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:./media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:virtual-machines-windows-sap-planning-guide.md (SAP NetWeaver sur les machines virtuelles Azure – Guide de planification et d’implémentation) [planning-guide-1.2]:virtual-machines-windows-sap-planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (Ressources) [planning-guide-11]:virtual-machines-windows-sap-planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (Haute disponibilité (HA) et récupération d’urgence (DR)pour SAP NetWeaver s’exécutant sur des machines virtuelles Azure) [planning-guide-11.4.1]:virtual-machines-windows-sap-planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (Haute disponibilité pour les serveurs d’applications SAP) [planning-guide-11.5]:virtual-machines-windows-sap-planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (Utilisation du démarrage automatique pour les instances SAP) [planning-guide-2.1]:virtual-machines-windows-sap-planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (Cloud uniquement : déploiement de machines virtuelles dans Azure sans dépendances du réseau local du client) [planning-guide-2.2]:virtual-machines-windows-sap-planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (Intersite : déploiement d’une ou plusieurs machines virtuelles SAP dans Azure, dans lequel une intégration complète au réseau local est nécessaire) [planning-guide-3.1]:virtual-machines-windows-sap-planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Régions Azure) [planning-guide-3.2.1]:virtual-machines-windows-sap-planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (Domaines d'erreur) [planning-guide-3.2.2]:virtual-machines-windows-sap-planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (Domaines de mise à niveau) [planning-guide-3.2.3]:virtual-machines-windows-sap-planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Groupes à haute disponibilité Azure) [planning-guide-3.2]:virtual-machines-windows-sap-planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (Concept de la machine virtuelle Microsoft Azure) [planning-guide-3.3.2]:virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Stockage Premium Azure) [planning-guide-5.1.1]:virtual-machines-windows-sap-planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (Déplacement d’une machine virtuelle locale vers Azure avec un disque non généralisé) [planning-guide-5.1.2]:virtual-machines-windows-sap-planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (Déploiement d’une machine virtuelle avec une image spécifique du client) [planning-guide-5.2.1]:virtual-machines-windows-sap-planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (Préparation du déplacement d’une machine virtuelle locale vers Azure avec un disque non généralisé) [planning-guide-5.2.2]:virtual-machines-windows-sap-planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (Préparation du déploiement d’une machine virtuelle avec une image spécifique du client pour SAP) [planning-guide-5.2]:virtual-machines-windows-sap-planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Préparation de machines virtuelles avec SAP pour Azure) [planning-guide-5.3.1]:virtual-machines-windows-sap-planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Différence entre un disque Azure et une image Azure) [planning-guide-5.3.2]:virtual-machines-windows-sap-planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (Chargement d’un disque dur virtuel local vers Azure) [planning-guide-5.4.2]:virtual-machines-windows-sap-planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Copie de disques entre comptes de stockage Azure) [planning-guide-5.5.1]:virtual-machines-windows-sap-planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (Structure de machine virtuelle/disque dur virtuel pour les déploiements SAP) [planning-guide-5.5.3]:virtual-machines-windows-sap-planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (Paramétrage du montage automatique pour les disques attachés) [planning-guide-7.1]:virtual-machines-windows-sap-planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (Machine virtuelle unique avec scénario de démonstration/formation SAP NetWeaver) [planning-guide-7]:virtual-machines-windows-sap-planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (Concepts de déploiement cloud uniquement d’instances SAP) [planning-guide-9.1]:virtual-machines-windows-sap-planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Solution de surveillance Azure pour SAP) [planning-guide-azure-premium-storage]:virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Stockage Premium Azure)

[planning-guide-figure-100]:./media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:./media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:./media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:./media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:./media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:./media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:./media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:./media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:./media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:./media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:./media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:./media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:./media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:./media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:./media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:./media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:./media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:./media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:./media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:./media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:./media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:./media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:./media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:virtual-machines-windows-sap-planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Microsoft Azure Networking)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:virtual-machines-windows-sap-planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Storage: Microsoft Azure Storage and Data Disks)

[powershell-install-configure]:/powershell/azureps-cmdlets-docs
[resource-group-authoring-templates]:../azure-resource-manager/resource-group-authoring-templates.md
[resource-group-overview]:../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../virtual-network/resource-groups-networking.md
[sap-pam]:https://support.sap.com/pam (SAP Product Availability Matrix)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../storage/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../storage/storage-azure-cli.md#copy-blobs
[storage-introduction]:../storage/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../storage/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../storage/storage-premium-storage.md
[storage-redundancy]:../storage/storage-redundancy.md
[storage-scalability-targets]:../storage/storage-scalability-targets.md
[storage-use-azcopy]:../storage/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:virtual-machines-linux-attach-disk-portal.md
[virtual-machines-windows-attach-disk-portal]:virtual-machines-windows-attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../azure-resource-manager/resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-windows-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:virtual-machines-linux-cli-deploy-templates.md (Deploy and manage virtual machines by using Azure Resource Manager templates and the Azure CLI)
[virtual-machines-deploy-rmtemplates-powershell]:virtual-machines-windows-ps-manage.md (Manage virtual machines using Azure Resource Manager and PowerShell)
[virtual-machines-linux-agent-user-guide]:virtual-machines-linux-agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:virtual-machines-linux-agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-capture]:virtual-machines-linux-capture-image.md#capture-the-vm
[virtual-machines-windows-capture-image]:virtual-machines-windows-capture-image.md
[virtual-machines-windows-capture-image-capture]:virtual-machines-windows-capture-image.md#capture-the-vm
[virtual-machines-linux-configure-lvm]:virtual-machines-linux-configure-lvm.md
[virtual-machines-linux-configure-raid]:virtual-machines-linux-configure-raid.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:virtual-machines-linux-suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:virtual-machines-linux-redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:virtual-machines-linux-add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:virtual-machines-linux-add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:virtual-machines-linux-quick-create-cli.md
[virtual-machines-linux-update-agent]:virtual-machines-linux-update-agent.md
[virtual-machines-manage-availability]:virtual-machines-windows-manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-ps-create.md
[virtual-machines-sizes]:virtual-machines-windows-sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:./windows/sql/virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:./windows/sql/virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:./windows/sql/virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:virtual-machines-windows-hero-tutorial.md
[virtual-machines-windows-create-vm-specialized]:virtual-machines-windows-create-vm-specialized.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../virtual-network/virtual-network-deploy-multinic-arm-cli.md
[virtual-network-deploy-multinic-arm-ps]:../virtual-network/virtual-network-deploy-multinic-arm-ps.md
[virtual-network-deploy-multinic-arm-template]:../virtual-network/virtual-network-deploy-multinic-arm-template.md
[virtual-networks-configure-vnet-to-vnet-connection]:../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../virtual-network/virtual-networks-create-vnet-arm-pportal.md
[virtual-networks-manage-dns-in-vnet]:../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../virtual-network/virtual-networks-multiple-nics.md
[virtual-networks-nsg]:../virtual-network/virtual-networks-nsg.md
[virtual-networks-reserved-private-ip]:../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md
[vpn-gateway-vpn-faq]:../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../xplat-cli-install.md
[xplat-cli-azure-resource-manager]:../azure-resource-manager/xplat-cli-azure-resource-manager.md

Microsoft Azure permet aux entreprises d’acquérir des ressources de calcul et de stockage rapidement, sans longs cycles d’acquisition. Azure Virtual Machines permet aux entreprises de déployer dans Azure des applications classiques, comme les applications basées sur SAP NetWeaver, et d’étendre leur fiabilité et leur disponibilité même en l’absence des ressources supplémentaires disponibles en local. Microsoft Azure prend également en charge la connectivité sur différents sites, ce qui permet aux entreprises d’intégrer de manière active Azure Virtual Machines dans leurs domaines locaux, leurs Clouds privés et leur paysage SAP.

Ce livre blanc décrit étape par étape comment préparer une machine virtuelle Azure pour le déploiement d’applications basées sur SAP NetWeaver. Il part du principe que les informations contenues dans le [Guide de planification et d’implémentation][planification-guide] sont connues. Si ce n’est pas le cas, ce document doit être lu au préalable.

Ce document vient compléter la documentation sur l’installation SAP et des notes SAP, qui représentent les ressources incontournables en matière d’installation et de déploiement de logiciels SAP sur des plateformes données.

## <a name="introduction"></a>Introduction
Un grand nombre d’entreprises du monde entier utilisent des applications basées sur SAP NetWeaver, plus particulièrement SAP Business Suite, pour exécuter leurs processus d’entreprise stratégiques. Par conséquent, l’intégrité du système est une ressource cruciale, et la possibilité de fournir un support à l’entreprise en cas de dysfonctionnement, y compris des incidents liés aux performances, devient un impératif vital.
Microsoft Azure fournit une instrumentation de plate-forme supérieure pour prendre en compte les exigences de prise en charge de toutes les applications d’entreprise stratégiques. Ce guide s’assure qu’une machine virtuelle Microsoft Azure ciblée pour le déploiement de logiciels SAP est configurée de manière à pouvoir fournir un support à l’entreprise, indépendamment du mode de création de la machine virtuelle, qu’elle vienne d’Azure Marketplace ou d’une image spécifique à un client.
Dans la configuration ci-dessous, toutes les tâches nécessaires sont décrites en détail.

## <a name="prerequisites-and-resources"></a>Configuration requise et ressources
### <a name="prerequisites"></a>Composants requis
Avant de commencer, assurez-vous que les conditions préalables qui sont décrites dans les chapitres suivants sont remplies.

#### <a name="local-personal-computer"></a>Ordinateur personnel local
La configuration d’une machine virtuelle Azure pour le déploiement de logiciels SAP comprend plusieurs étapes. Pour gérer les machines virtuelles Windows ou Linux, vous devez utiliser un script PowerShell et le portail Microsoft Azure. Pour ce faire, vous avez besoin d’un ordinateur personnel local exécutant Windows 7 ou version ultérieure. Si vous voulez uniquement gérer des machines virtuelles Linux et voulez utiliser une machine Linux pour cette tâche, vous pouvez également utiliser l’interface de ligne de commande Azure.

#### <a name="internet-connection"></a>Connexion Internet
Pour télécharger et exécuter les outils et scripts requis, une connexion Internet est requise. En outre, la machine virtuelle Azure Microsoft exécutant l’extension d’analyse améliorée Azure pour SAP doit être connectée à Internet. Si cette machine virtuelle Azure fait partie d’un réseau virtuel Azure ou du domaine local, assurez-vous que les paramètres de proxy appropriés sont configurés comme décrit dans le chapitre [Configurer le proxy][deployment-guide-configure-proxy] de ce document.

#### <a name="microsoft-azure-subscription"></a>Abonnement Microsoft Azure
Vous avez déjà un compte Azure et vous connaissez les informations de connexion associées.

#### <a name="topology-consideration-and-networking"></a>Prise en compte de la topologie et mise en réseau
La topologie et l’architecture du déploiement SAP dans Azure doivent être définies. Architecture par rapport aux éléments suivants :

* Comptes Microsoft Azure Storage à utiliser
* Réseau virtuel dans lequel déployer le système SAP
* Groupe de ressources dans lequel déployer le système SAP
* Région Azure dans laquelle déployer le système SAP
* Configuration SAP (2 ou 3 niveaux)
* Tailles des machines virtuelles et nombre de disques durs virtuels supplémentaires à monter sur les machines virtuelles
* Configuration système de transport et correction SAP

Des comptes Azure Storage ou réseaux virtuels Azure à cet effet doivent avoir été créé et configurés. Leur création et configuration sont expliquées dans le [Guide de planification et d’implémentation][planification-guide].

#### <a name="sap-sizing"></a>Dimensionnement SAP
* La charge de travail SAP prévue a été définie, par exemple à l’aide de l’outil SAP Quicksizer, et le numéro SAP correspondant est connu. 
* La consommation de mémoire et de ressources de processeur requise du système SAP doit être connue.
* Le nombre d’opérations d’E/S requises par seconde doit être connu.
* La bande passante réseau requise pour la communication éventuelle entre les différentes machines virtuelles dans Azure est connue.
* La bande passante réseau requise entre les ressources locales et les systèmes SAP Azure déployés est connue.

#### <a name="resource-groups"></a>Groupes de ressources
Les groupes de ressources font partie d’un nouveau concept de déploiement. Toutes les ressources d’un groupe de ressources ont le même cycle de vie. Par exemple, elles sont créées et supprimées en même temps. Lisez [cet article][resource-group-overview] pour plus d’informations sur les groupes de ressources. 

### <a name="a-name42ee2bdb-1efc-4ec7-ab31-fe4c22769b94asap-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Ressources SAP
Pendant la configuration, les ressources suivantes sont nécessaires :

* La note SAP [1928533] contient :
  * liste des tailles de machines virtuelles Azure prises en charge pour le déploiement de logiciels SAP 
  * informations importantes sur la capacité par taille de machine virtuelle Azure
  * logiciels SAP pris en charge et combinaison système d’exploitation/base de données
  * version du noyau SAP requise pour Windows et Linux sur Microsoft Azure

* La note SAP [2015553] établit les conditions préalables nécessaires pour garantir la prise en charge par SAP lors du déploiement de logiciels SAP sur Microsoft Azure.
* La note SAP [2178632] contient des informations détaillées sur toutes les métriques d’analyse signalées pour SAP sur Microsoft Azure. 
* La note SAP [1409604] contient la version requise de l’agent hôte SAP pour Windows sur Microsoft Azure.
* La note SAP [2191498] contient la version requise de l’agent hôte SAP pour Linux sur Microsoft Azure.
* La note SAP [2243692] contient des informations sur les licences SAP sur Linux dans Azure
* La note SAP [1984787] contient des informations sur SUSE LINUX Enterprise Server 12
* La note SAP [2002167] contient des informations sur Red Hat Enterprise Linux 7.x
* La note SAP [1999351] contient des informations de dépannage supplémentaires pour l’analyse Azure améliorée pour SAP.
* [SAP WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) qui contient toutes les notes SAP requises pour Linux
* Applets de commande PowerShell spécifiques à SAP qui font partie [d’Azure PowerShell][azure-ps]
* Interface de ligne de commande Azure spécifique à SAP faisant partie de [l’interface de ligne de commande Azure][azure-cli]
* [Portail Microsoft Azure][azure-portal]

[comment]: <> (MSSedusch TODO Add ARM patch level for SAP Host Agent in SAP Note 1409604)

Les guides suivants traitent également de SAP sur Microsoft Azure :

* [SAP NetWeaver sur machines virtuelles Azure – Guide de planification et d’implémentation][planning-guide]
* [SAP NetWeaver sur machines virtuelles Azure – Guide de déploiement (ce document)][deployment-guide]
* [SAP NetWeaver sur les machines virtuelles Azure – Guide de déploiement SGBD][dbms-guide]
* [SAP NetWeaver sur machines virtuelles Azure – Guide de déploiement haute disponibilité][ha-guide]

## <a name="a-nameb3253ee3-d63b-4d74-a49b-185e76c4088eadeployment-scenarios-of-vms-for-sap-on-microsoft-azure"></a><a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Scénarios de déploiement de machines virtuelles pour SAP sur Microsoft Azure
Dans ce chapitre, vous allez apprendre les différents modes de déploiement et les étapes individuelles pour chaque type de déploiement.

### <a name="deployment-of-vms-for-sap"></a>Déploiement de machines virtuelles pour SAP
Microsoft Azure offre plusieurs modes de déploiement de machines virtuelles et des disques associés. Ainsi, il est très important de comprendre les différences dans la mesure où les préparations des machines virtuelles peuvent différer en fonction du déploiement. En règle générale, nous examinerons les scénarios suivants :

#### <a name="deploying-a-vm-out-of-the-azure-marketplace"></a>Déploiement d’une machine virtuelle provenant d’Azure Marketplace
Vous voulez prendre une image fournie par Microsoft ou par un tiers provenant d’Azure Marketplace pour déployer votre machine virtuelle. Une fois que vous avez déployé votre machine virtuelle sur Microsoft Azure, vous utilisez les mêmes instructions et outils pour installer les logiciels SAP au sein de votre machine virtuelle comme vous le feriez dans un environnement local. Pour installer les logiciels SAP au sein de la machine virtuelle Azure, SAP et Microsoft recommandent de télécharger et stocker le support d’installation SAP sur des disques durs virtuels Azure, ou de créer une machine virtuelle Azure faisant office de serveur de fichiers qui contient tous les supports d’installation SAP nécessaires.

[comment]: <> (MSSedusch TODO why do we need to recommend a file management e.g. File Server or VHD? Is that so different from on-premises?)

Pour plus d’informations, consultez le chapitre [Scénario 1 : Déploiement d’une machine virtuelle provenant d’Azure Marketplace][déploiement-guide-3.2].

#### <a name="a-name3688666f-281f-425b-a312-a77e7db2dfabadeploying-a-vm-with-a-custom-image"></a><a name="3688666f-281f-425b-a312-a77e7db2dfab"></a>Déploiement d’une machine virtuelle avec une image personnalisée
En raison des exigences spécifiques de correctif par rapport aux versions de votre système d’exploitation ou SGBD, les images fournies provenant d’Azure Marketplace peuvent ne pas répondre à vos besoins. Par conséquent, vous devrez peut-être créer une machine virtuelle à l’aide de votre propre image privée de machine virtuelle du système d’exploitation/base de données privé pouvant être déployée plusieurs fois par la suite.
Les étapes pour créer une image privée diffèrent en fonction du type d’image (Windows ou Linux).

- - -
> ![Windows][Logo_Windows] Windows
> 
> Pour préparer une image Windows pouvant être utilisée pour déployer plusieurs machines virtuelles, les paramètres Windows (comme le nom d’hôte et le SID Windows) doivent être abstraits/généralisés sur la machine virtuelle locale. Cette opération peut être effectuée à l’aide de sysprep comme décrit sur le site <https://msdn.microsoft.com/library/hh825084.aspx>.
> 
> ![Linux][Logo_Linux] Linux
> 
> Pour préparer une image Linux pouvant être utilisée pour déployer plusieurs machines virtuelles, certains paramètres Linux doivent être abstraits/généralisés sur la machine virtuelle locale. Cela peut être effectué à l’aide de la commande waagent -deprovision, comme décrit dans [cet article][virtual-machines-linux-capture-image] ou dans [cet article][virtual-machines-linux-agent-user-guide-command-line-options].
> 
> 

- - -
Vous pouvez configurer le contenu de votre base de données en utilisant le gestionnaire de déploiement de logiciels SAP pour installer un nouveau système SAP, restaurer une sauvegarde de base de données à partir d’un disque dur virtuel connecté à la machine virtuelle ou restaurer directement une sauvegarde de base de données à partir du Stockage Azure si le SGBD le prend en charge. (Voir le [Guide de déploiement DBMS][dbms-guide]). Si vous avez déjà installé un système SAP sur votre machine virtuelle locale (en particulier pour les systèmes à 2 niveaux), vous pouvez adapter les paramètres du système SAP après le déploiement de la machine virtuelle Azure à l’aide la procédure de renommage système prise en charge par le gestionnaire de déploiement de logiciels SAP (Note SAP [1619720]). Sinon, vous pouvez installer les logiciels SAP après le déploiement de la machine virtuelle Azure.

Pour plus d’informations, consultez le chapitre [Scénario 2 : Déploiement avec une machine virtuelle provenant d’une image personnalisée pour SAP][déploiement-guide-3.3].

#### <a name="moving-a-vm-from-on-premises-to-microsoft-azure-with-a-non-generalized-disk"></a>Déplacement d’une machine virtuelle locale vers Microsoft Azure avec un disque non généralisé
Vous envisagez de déplacer un système SAP spécifique local vers Microsoft Azure. Pour ce faire, vous pouvez charger le disque dur virtuel qui contient le système d’exploitation, les fichiers binaires SAP et les éventuels fichiers binaires SGBD, ainsi que les disques durs virtuels contenant les données et les fichiers journaux du SGBD pour Microsoft Azure. Contrairement au scénario décrit dans le chapitre [Déploiement d’une machine virtuelle avec une image personnalisée][déploiement-guide-3.1.2] ci-dessus, vous conservez le nom d’hôte, le SID SAP et les comptes d’utilisateur SAP dans la machine virtuelle Azure, conformément à leur configuration dans l’environnement local. Par conséquent, il n’est pas nécessaire de généraliser le système d’exploitation. Ce cas s’applique tout particulièrement pour les scénarios intersites dans lesquels une partie du paysage SAP est exécutée en local et une autre sur Microsoft Azure.

Pour plus d’informations, consultez le chapitre [Scénario 3 : Déplacement d’une machine virtuelle locale à l’aide d’un disque dur virtuel Azure non généralisé avec SAP][déploiement-guide-3.4].

### <a name="a-namedb477013-9060-4602-9ad4-b0316f8bb281ascenario-1-deploying-a-vm-out-of-the-azure-marketplace-for-sap"></a><a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>Scénario 1 : Déploiement d’une machine virtuelle provenant d’Azure Marketplace pour SAP
Microsoft Azure offre la possibilité de déployer une instance de machine virtuelle provenant d’Azure Marketplace, qui offre des images standard du système d’exploitation Windows Server et différentes distributions Linux. Il est également possible de déployer une image comprenant des références SGBD, par exemple Microsoft SQL Server. Pour plus d’informations sur l’utilisation de ces images avec des références SGBD, consultez le [Guide de déploiement SGBD][dbms-guide].

Les étapes spécifiques à SAP pour déployer une machine virtuelle provenant d’Azure Marketplace seraient comme suit :

![Organigramme de déploiement de machines virtuelles pour les systèmes SAP à l’aide d’une image de machine virtuelle provenant d’Azure Marketplace][deployment-guide-figure-100]

Les étapes suivantes doivent être exécutées d’après l’organigramme :

#### <a name="create-virtual-machine-using-the-azure-portal"></a>Création d’une machine virtuelle à l’aide du portail Azure
Le moyen le plus simple de créer une machine virtuelle à l’aide d’une image provenant d’Azure Marketplace est d’utiliser le portail Azure. Accédez à <https://portal.azure.com/#create> ou cliquez sur « + » sur le côté gauche du portail Azure. Entrez le type de système d’exploitation que vous voulez déployer, par exemple, Windows, SLES or RHEL, puis sélectionnez la version. Veillez à sélectionner le modèle de déploiement « Resource Manager », puis cliquez sur Créer.

L’assistant vous guide pour configurer les paramètres requis pour créer la machine virtuelle, ainsi que toutes les ressources requises, telles que les interfaces réseau ou les comptes de stockage. Voici certains exemples de paramètres :

1. Concepts de base
   1. Nom : nom de la ressource, c’est-à-dire le nom de la machine virtuelle
   1. Nom utilisateur et mot de passe/clé publique SSH : entrez le nom d’utilisateur et le mot de passe de l’utilisateur créé lors de la configuration. Pour une machine virtuelle Linux, vous pouvez également entrer la clé publique SSH que vous voulez utiliser pour vous connecter à la machine via SSH.
   1. Abonnement : sélectionnez l’abonnement à utiliser pour configurer la nouvelle machine virtuelle.
   1. Groupe de ressources : entrez le nom du groupe de ressources. Vous pouvez insérer le nom d’un nouveau groupe de ressources ou celui d’un groupe de ressources existant.
   1. Emplacement : sélectionnez l’emplacement où la nouvelle machine virtuelle doit être déployée. Si vous voulez connecter la machine virtuelle à votre réseau local, veillez à sélectionner l’emplacement du réseau virtuel qui connecte Azure à votre réseau local. Pour plus d’informations, consultez le chapitre [Mise en réseau Microsoft Azure][planning-guide-microsoft-azure-networking] dans le [Guide de planification][planning-guide].
1. Taille

   Veuillez lire la note SAP [1928533] pour obtenir la liste des types de machine virtuelle pris en charge. Veillez également à sélectionner le type approprié si vous voulez utiliser le stockage Premium. Tous les types de machine virtuelle ne prennent pas en charge le stockage Premium. Consultez le chapitre [Stockage : Stockage Microsoft Azure et disques de données][planning-guide-storage-microsoft-azure-storage-and-data-disks] et [Stockage Premium Azure][planning-guide-azure-premium-storage] dans le [Guide de planification][planning-guide] pour plus de détails.

1. Settings
   1. Compte de stockage : sélectionnez un compte de stockage existant ou créez un nouveau compte de stockage. Lisez le chapitre [Microsoft Azure Storage][dbms-guide-2.3] du [Guide SGBD][dbms-guide] pour plus d’informations sur les différents types de stockage. Notez que tous les types de stockage ne sont pas pris en charge pour l’exécution d’applications SAP.
   1. Réseau virtuel et sous-réseau : sélectionnez le réseau virtuel connecté à votre réseau local si vous voulez intégrer la machine virtuelle à votre intranet.
   1. Adresse IP publique : sélectionnez l’adresse IP publique que vous voulez utiliser ou entrez les paramètres nécessaires pour en créer une. Vous pouvez utiliser une adresse IP publique pour accéder à votre machine virtuelle via Internet. Assurez-vous également de créer un groupe de sécurité réseau pour filtrer l’accès à votre machine virtuelle.
   1. Groupe de sécurité réseau : consultez [Présentation du groupe de sécurité réseau][virtual-networks-nsg] pour plus de détails.
   1. Disponibilité : sélectionnez un groupe à haute disponibilité ou entrez les paramètres permettant d’en créer un. Pour plus d’informations, consultez chapitre [Groupes à haute disponibilité Azure][planification-guide-3.2.3].
   1. Analyse : vous pouvez désactiver le paramètre de diagnostic. Il est activé automatiquement lorsque vous exécutez les commandes permettant d’activer l’analyse Azure améliorée comme décrit dans le chapitre [Configuration de l’analyse][deployment-guide-configure-monitoring-scenario-1].
   
1. Résumé : validez les informations fournies sur la page de résumé et cliquez sur OK.

Une fois l’assistant terminé, votre machine virtuelle sera déployée dans le groupe de ressources que vous avez sélectionné.

#### <a name="create-virtual-machine-using-a-template"></a>Création d’une machine virtuelle à l’aide d’un modèle
Vous pouvez également créer un déploiement à l’aide de l’un des modèles SAP publiés dans le [référentiel github azure-quickstart-templates][azure-quickstart-templates-github]. Vous pouvez également créer une machine virtuelle manuellement à l’aide du [Portail Azure][virtual-machines-windows-tutorial], de [PowerShell][virtual-machines-ps-create-preconfigure-windows-resource-manager-vms] ou de [l’interface de ligne de commande Azure][virtual-machines-linux-tutorial].

* [Modèle de configuration à 2 niveaux (une seule machine virtuelle) (sap-2-tier-marketplace-image)][sap-templates-2-tier-marketplace-image] Utilisez ce modèle si vous voulez créer un système à 2 niveaux avec une seule machine virtuelle.
* [Modèle de configuration à 3 niveaux (plusieurs machines virtuelles) (sap-3-tier-marketplace-image)][sap-templates-3-tier-marketplace-image] Utilisez ce modèle si vous voulez créer un système à 3 niveaux avec plusieurs machines virtuelles.

Une fois que vous avez ouvert un des modèles ci-dessus, le portail Azure accède à un écran sur lequel vous pouvez entrer les paramètres pour le modèle. Entrez les informations suivantes :

1. Concepts de base
  * **Abonnement** : l’abonnement pour lequel vous souhaitez déployer le modèle
  * **Groupe de ressources** : le groupe de ressources pour lequel vous souhaitez déployer le modèle. Vous pouvez créer un groupe de ressources ou sélectionner un groupe de ressources existant dans l’abonnement sélectionné.
  * **Emplacement** : l’emplacement pour lequel vous souhaitez déployer le modèle. Si vous avez sélectionné un groupe de ressources existant, l’emplacement du groupe de ressources est utilisé.
1. Settings
  * **Sap System Id** : identifiant du système SAP
  * **Os Type** : système d’exploitation que vous voulez déployer, par exemple, Windows Server 2012 R2, SLES 12 ou RHEL 7.2
    * La liste ne contient pas tous les systèmes d’exploitation. Par exemple, la liste ne contient pas Windows Server 2008 R2 bien qu’il est pris en charge par SAP. Veuillez lire la note SAP [1928533] pour obtenir la liste des systèmes d’exploitation pris en charge.
  * **Sap System Size** : taille du système SAP
    * Le nombre de SAP fournis par le nouveau système. Si vous ne savez pas combien de SAP sont requis par le système, demandez à votre partenaire technologique SAP ou un intégrateur système
  * **System Availability** : (modèle à&3; niveaux uniquement) disponibilité du système
    * Sélectionnez haute disponibilité si la configuration est adaptée à une installation haute disponibilité. Deux serveurs de base de données et deux serveurs pour l’ASCS seront créés.
  * **Storage Type** : (modèle à&2; niveaux uniquement) type de stockage à utiliser
    * Pour les systèmes plus importants, il est fortement recommandé d’utiliser le stockage Premium. Pour plus d’informations sur les différents types de stockage, lisez la section
      * [Utilisation du stockage SSD Azure Premium pour l’instance de SGBD SAP][2367194]
      * [Microsoft Azure Storage][dbms-guide-2.3] du [Guide DBMS][dbms-guide]
      * [Premium Storage : stockage hautes performances pour les charges de travail des machines virtuelles Azure][storage-premium-storage-preview-portal]
      * [Présentation du stockage Microsoft Azure][storage-introduction]
  * **Admin Username** et **Admin Password** : nom d’utilisateur et mot de passe
    * Un utilisateur pouvant être utilisé pour ouvrir une session sur la machine est créé.
  * **New Or Existing Subnet** : détermine si un réseau virtuel et un sous-réseau doivent être créés ou si un sous-réseau existant doit être utilisé. Si vous disposez déjà d’un réseau virtuel connecté à votre réseau local, sélectionnez existant.
  * **Subnet Id** : identifiant du sous-réseau auquel les machines virtuelles doivent être connectées. Sélectionnez le sous-réseau de votre VPN ou réseau virtuel ExpressRoute pour connecter la machine virtuelle à votre réseau local. L’identifiant se présente généralement comme suit : /abonnements/`<subscription id`>/groupesderessources/`<resource group name`>/fournisseurs/Réseau.Microsoft/réseauxVirtuels/`<virtual network name`>/sous-réseaux/`<subnet name`>

1. Conditions  
    Lisez et acceptez les conditions juridiques.

Après avoir entré tous les paramètres, confirmez l’écran en cliquant sur Purchase (Achat). 

Veuillez noter que l’agent de la machine virtuelle Azure est déployé par défaut lorsque vous utilisez une image provenant d’Azure Marketplace.

#### <a name="configure-proxy-settings"></a>Configurer les paramètres de proxy
Selon votre configuration de réseau local, vous devrez peut-être configurer le proxy sur votre machine virtuelle si elle est connectée à votre réseau local via VPN ou ExpressRoute. Si vous ne le faites pas, la machine virtuelle peut ne pas être en mesure d’accéder à Internet et par conséquent ne pas pouvoir télécharger les extensions requises ni collecter des données d’analyse. Consultez le chapitre [Configurer le proxy][deployment-guide-configure-proxy] de ce document.

#### <a name="join-domain-windows-only"></a>Joindre le domaine (Windows uniquement)
Si le déploiement dans Azure est connecté à AD/DNS en local par le biais d’Azure site à site ou d’ExpressRoute (également appelé intersite dans le [Guide de planification et d’implémentation][planification-guide]), la machine virtuelle doit être associée à un domaine local. Les points à prendre en compte pour cette étape sont décrits dans le chapitre [Joindre une machine virtuelle à un domaine local (Windows uniquement)][déploiement-guide-4.3] de ce document.

#### <a name="a-nameec323ac3-1de9-4c3a-b770-4ff701def65baconfigure-monitoring"></a><a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>Configuration de l’analyse
Pour garantir un environnement pris en charge par SAP, configurez l’extension d’analyse Azure améliorée pour SAP, comme décrit dans le chapitre [Configurer l’extension d’analyse Azure améliorée pour SAP][deployment-guide-4.5] de ce document.

Vérifiez la configuration requise pour l’analyse SAP pour connaître les versions minimales requises pour le noyau SAP et l’agent hôte SAP dans les ressources répertoriées dans le chapitre [Ressources SAP][déploiement-guide-2.2] de ce document.

#### <a name="monitoring-check"></a>Vérification de l’analyse
Vérifiez si l’analyse fonctionne comme décrit dans le chapitre [Vérifications et résolution des problèmes pour la configuration de l’analyse de bout en bout pour SAP sur Azure][deployment-guide-troubleshooting-chapter].

#### <a name="post-deployment-steps"></a>Étapes suivant le déploiement
Une fois la machine virtuelle créée, elle est déployée et vous devez y installer tous les composants logiciels requis. Par conséquent, pour ce type de déploiement de machine virtuelle, vous devez disposer des logiciels nécessaires soit dans Microsoft Azure, soit sur une autre machine virtuelle ou sur un disque pouvant être connecté. Sinon, il s’agit de scénarios intersites dans lesquels la connectivité aux ressources locales (partages d’installation) est acquise.

### <a name="a-name54a1fc6d-24fd-4feb-9c57-ac588a55dff2ascenario-2-deploying-a-vm-with-a-custom-image-for-sap"></a><a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>Scénario 2 : Déploiement d’une machine virtuelle avec une image personnalisée pour SAP
Comme décrit dans le [Guide de planification et d’implémentation][planning-guide], vous pouvez préparer et créer une image personnalisée et l’utiliser pour créer plusieurs machines virtuelles. La séquence d’étapes dans l’organigramme serait comme suit :

![Organigramme de déploiement de machines virtuelles pour les systèmes SAP à l’aide d’une image de machine virtuelle provenant du Marketplace privé][deployment-guide-figure-300]

Les étapes suivantes doivent être exécutées d’après l’organigramme :

#### <a name="create-the-virtual-machine"></a>Créer la machine virtuelle
Pour créer un déploiement à l’aide d’une image privée de système d’exploitation par l’intermédiaire du portail Azure, utilisez l’un des modèles SAP répertoriés ci-dessous. Ces modèles sont publiés dans le [référentiel GitHub azure-quickstart-templates][azure-quickstart-templates-github].
Vous pouvez également créer une machine virtuelle en utilisant [PowerShell][virtual-machines-upload-image-windows-resource-manager] manuellement. 

* [Modèle de configuration à 2 niveaux (une seule machine virtuelle) (sap-2-tier-user-image)][sap-templates-2-tier-user-image] Utilisez ce modèle si vous voulez créer un système à 2 niveaux avec une seule machine virtuelle et votre propre image de système d’exploitation.
* [Modèle de configuration à 3 niveaux (plusieurs machines virtuelles) (sap-3-tier-user-image)][sap-templates-3-tier-user-image] Utilisez ce modèle si vous voulez créer un système à 3 niveaux avec plusieurs machines virtuelles et votre propre image de système d’exploitation.

Une fois que vous avez ouvert un des modèles ci-dessus, le portail Azure accède à un écran sur lequel vous pouvez entrer les paramètres pour le modèle. Entrez les informations suivantes :

1. Concepts de base
  * **Abonnement** : l’abonnement pour lequel vous souhaitez déployer le modèle
  * **Groupe de ressources** : le groupe de ressources pour lequel vous souhaitez déployer le modèle. Vous pouvez créer un groupe de ressources ou sélectionner un groupe de ressources existant dans l’abonnement sélectionné.
  * **Emplacement** : l’emplacement pour lequel vous souhaitez déployer le modèle. Si vous avez sélectionné un groupe de ressources existant, l’emplacement du groupe de ressources est utilisé.
1. Settings
  * **Sap System Id** : identifiant du système SAP
  * **Os Type** : type de système d’exploitation à déployer, Windows ou Linux
  * **Sap System Size** : taille du système SAP
    * Le nombre de SAP fournis par le nouveau système. Si vous ne savez pas combien de SAP sont requis par le système, demandez à votre partenaire technologique SAP ou un intégrateur système
  * **System Availability** : (modèle à&3; niveaux uniquement) disponibilité du système 
    * Sélectionnez haute disponibilité si la configuration est adaptée à une installation haute disponibilité. Deux serveurs de base de données et deux serveurs pour l’ASCS seront créés.
  * **Storage Type** : (modèle à&2; niveaux uniquement) type de stockage à utiliser 
    * Pour les systèmes plus importants, il est fortement recommandé d’utiliser le stockage Premium. Pour plus d’informations sur les différents types de stockage, lisez la section 
      * [Utilisation du stockage SSD Azure Premium pour l’instance de SGBD SAP][2367194]
      * [Microsoft Azure Storage][dbms-guide-2.3] du [Guide DBMS][dbms-guide]
      * [Premium Storage : stockage hautes performances pour les charges de travail des machines virtuelles Azure][storage-premium-storage-preview-portal]
      * [Présentation du stockage Microsoft Azure][storage-introduction]
  * **User Image Vhd Uri** : URI du disque dur virtuel de l’image privée du système d’exploitation, par exemple https://`<accountname`>.blob.core.windows.net/vhds/userimage.vhd
  * **User Image Storage Account** : nom du compte de stockage où l’image privée du système d’exploitation est stockée, par exemple, `<accountname`> dans l’exemple d’URI ci-dessus
  * **Admin Username** et **Admin Password** : nom d’utilisateur et mot de passe
    * Un utilisateur pouvant être utilisé pour ouvrir une session sur la machine est créé.
  * **New Or Existing Subnet** : détermine si un réseau virtuel et un sous-réseau doivent être créés ou si un sous-réseau existant doit être utilisé. Si vous disposez déjà d’un réseau virtuel connecté à votre réseau local, sélectionnez existant.
  * **Subnet Id** : identifiant du sous-réseau auquel les machines virtuelles doivent être connectées. Sélectionnez le sous-réseau de votre VPN ou réseau virtuel ExpressRoute pour connecter la machine virtuelle à votre réseau local. L’identifiant se présente généralement comme suit : /abonnements/`<subscription id`>/groupesderessources/`<resource group name`>/fournisseurs/Réseau.Microsoft/réseauxVirtuels/`<virtual network name`>/sous-réseaux/`<subnet name`>

1. Conditions  
    Lisez et acceptez les conditions juridiques.

Après avoir entré tous les paramètres, confirmez l’écran en cliquant sur Purchase (Achat).

#### <a name="install-vm-agent-linux-only"></a>Installer l’agent de machine virtuelle (Linux uniquement)
L’agent Linux doit déjà être installé sur l’image utilisateur si vous voulez utiliser les modèles ci-dessus. Si ce n'est pas le cas, le déploiement échoue. Téléchargez et installez l’agent de machine virtuelle dans l’image utilisateur, comme décrit dans le chapitre [Télécharger, installer et activer l’agent de machine virtuelle Azure][déploiement-guide-4.4] de ce document.
Si vous n’utilisez pas les modèles ci-dessus, vous pouvez également installer l’agent de machine virtuelle plus tard.

#### <a name="join-domain-windows-only"></a>Joindre le domaine (Windows uniquement)
Si le déploiement dans Azure est connecté à AD/DNS en local par le biais d’Azure site à site ou d’ExpressRoute (également appelé intersite dans le [Guide de planification et d’implémentation][planification-guide]), la machine virtuelle doit être associée à un domaine local. Les points à prendre en compte pour cette étape sont décrits dans le chapitre [Joindre une machine virtuelle à un domaine local (Windows uniquement)][deployment-guide-4.3] de ce document.

#### <a name="configure-proxy-settings"></a>Configurer les paramètres de proxy
Selon votre configuration de réseau local, vous devrez peut-être configurer le proxy sur votre machine virtuelle si elle est connectée à votre réseau local via VPN ou ExpressRoute. Si vous ne le faites pas, la machine virtuelle peut ne pas être en mesure d’accéder à Internet et par conséquent ne pas pouvoir télécharger les extensions requises ni collecter des données d’analyse. Consultez le chapitre [Configurer le proxy][deployment-guide-configure-proxy] de ce document.

#### <a name="configure-monitoring"></a>Configuration de l’analyse
Pour garantir un environnement pris en charge par SAP, configurez l’extension d’analyse Azure pour SAP, comme décrit dans le chapitre [Configurer l’extension d’analyse Azure améliorée pour SAP][deployment-guide-4.5] de ce document.
Vérifiez la configuration requise pour l’analyse SAP pour connaître les versions minimales requises pour le noyau SAP et l’agent hôte SAP dans les ressources répertoriées dans le chapitre [Ressources SAP][déploiement-guide-2.2] de ce document.

#### <a name="monitoring-check"></a>Vérification de l’analyse
Vérifiez si l’analyse fonctionne comme décrit dans le chapitre [Vérifications et résolution des problèmes pour la configuration de l’analyse de bout en bout pour SAP sur Azure][deployment-guide-troubleshooting-chapter].

### <a name="a-namea9a60133-a763-4de8-8986-ac0fa33aa8c1ascenario-3-moving-a-vm-from-on-premises-using-a-non-generalized-azure-vhd-with-sap"></a><a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>Scénario 3 : Déplacement d’une machine virtuelle locale à l’aide d’un disque dur virtuel Azure non généralisé avec SAP
Si vous envisagez de déplacer votre système SAP tel quel (même nom d’hôte et SID SAP) de l’environnement local vers Azure, le disque dur virtuel est directement utilisé comme disque de système d’exploitation et n’est pas référencé en tant qu’image lors du déploiement. Dans ce cas, l’agent de machine virtuelle ne sera pas automatiquement installé lors du déploiement. Comme l’agent de machine virtuelle et l’extension d’analyse Azure améliorée pour SAP sont nécessaires pour la prise en charge par SAP, vous devez télécharger, installer et activer ces deux composants manuellement après avoir créé la machine virtuelle. 

Pour plus d’informations sur l’agent de machine virtuelle Azure, consultez cet article :

[comment]: <> (MSSedusch TODO Update Windows Link below) 

- - -
> ![Windows][Logo_Windows] Windows
> 
> <http://blogs.msdn.com/b/wats/archive/2014/02/17/bginfo-guest-agent-extension-for-azure-vms.aspx>
> 
> ![Linux][Logo_Linux] Linux
> 
> [Guide d'utilisateur de l'agent Linux Azure][virtual-machines-linux-agent-user-guide]
> 
> 

- - -
Le flux de travail des différentes étapes ressemble à ceci :

![Organigramme de déploiement de machines virtuelles pour les systèmes SAP à l’aide d’un disque de machine virtuelle][deployment-guide-figure-400]

En supposant que le disque est déjà chargé et défini dans Azure (voir le [Guide de planification et d’implémentation][planning-guide]), procédez comme suit :

#### <a name="create-virtual-machine"></a>Créer une machine virtuelle
Pour créer un déploiement à l’aide d’un disque privé de système d’exploitation par l’intermédiaire du portail Azure, utilisez le modèle SAP publié dans le [référentiel github azure-quickstart-templates][azure-quickstart-templates-github]. Vous pouvez également créer une machine virtuelle en utilisant [PowerShell][virtual-machines-windows-create-vm-specialized] manuellement.

* [Modèle de configuration à&2; niveaux (une seule machine virtuelle) (sap-2-tier-user-disk)][sap-templates-2-tier-os-disk]
  * Utilisez ce modèle si vous voulez créer un système à 2 niveaux avec une seule machine virtuelle.

Une fois que vous avez ouvert un des modèles ci-dessus, le portail Azure accède à un écran sur lequel vous pouvez entrer les paramètres pour le modèle. Entrez les informations suivantes :

1. Concepts de base
  * **Abonnement** : l’abonnement pour lequel vous souhaitez déployer le modèle
  * **Groupe de ressources** : le groupe de ressources pour lequel vous souhaitez déployer le modèle. Vous pouvez créer un groupe de ressources ou sélectionner un groupe de ressources existant dans l’abonnement sélectionné.
  * **Emplacement** : l’emplacement pour lequel vous souhaitez déployer le modèle. Si vous avez sélectionné un groupe de ressources existant, l’emplacement du groupe de ressources est utilisé.
1. Settings
  * **Sap System Id** : identifiant du système SAP
  * **Os Type** : type de système d’exploitation à déployer, Windows ou Linux
  * **Sap System Size** : taille du système SAP
    * Le nombre de SAP fournis par le nouveau système. Si vous ne savez pas combien de SAP sont requis par le système, demandez à votre partenaire technologique SAP ou un intégrateur système
  * **Storage Type** : (modèle à&2; niveaux uniquement) type de stockage à utiliser 
    * Pour les systèmes plus importants, il est fortement recommandé d’utiliser le stockage Premium. Pour plus d’informations sur les différents types de stockage, lisez la section
      * [Utilisation du stockage SSD Azure Premium pour l’instance de SGBD SAP][2367194] 
      * [Microsoft Azure Storage][dbms-guide-2.3] du [Guide DBMS][dbms-guide]
      * [Premium Storage : stockage hautes performances pour les charges de travail des machines virtuelles Azure][storage-premium-storage-preview-portal]
      * [Présentation du stockage Microsoft Azure][storage-introduction]
  * **Os Disk Vhd Uri** : URI du disque de système d’exploitation privé, par exemple https://`<accountname`>.blob.core.windows.net/vhds/osdisk.vhd
  * **New Or Existing Subnet** : détermine si un réseau virtuel et un sous-réseau doivent être créés ou si un sous-réseau existant doit être utilisé. Si vous disposez déjà d’un réseau virtuel connecté à votre réseau local, sélectionnez existant.
  * **Subnet Id** : identifiant du sous-réseau auquel les machines virtuelles doivent être connectées. Sélectionnez le sous-réseau de votre VPN ou réseau virtuel ExpressRoute pour connecter la machine virtuelle à votre réseau local. L’identifiant se présente généralement comme suit : /abonnements/`<subscription id`>/groupesderessources/`<resource group name`>/fournisseurs/Réseau.Microsoft/réseauxVirtuels/`<virtual network name`>/sous-réseaux/`<subnet name`>

1. Conditions  
    Lisez et acceptez les conditions juridiques.

Après avoir entré tous les paramètres, confirmez l’écran en cliquant sur Purchase (Achat).

#### <a name="install-vm-agent"></a>Installer l’agent de machine virtuelle
L’agent de machine virtuelle doit être installé sur le disque du système d’exploitation si vous voulez utiliser les modèles ci-dessus. Si ce n'est pas le cas, le déploiement échoue. Téléchargez et installez l’agent de machine virtuelle dans la machine virtuelle, comme décrit dans le chapitre [Télécharger, installer et activer l’agent de machine virtuelle Azure][déployment-guide-4.4] de ce document.

Si vous n’utilisez pas les modèles ci-dessus, vous pouvez également installer l’agent de machine virtuelle plus tard.

#### <a name="join-domain-windows-only"></a>Joindre le domaine (Windows uniquement)
Si le déploiement dans Azure est connecté à AD/DNS en local par le biais d’Azure site à site ou d’ExpressRoute (également appelé intersite dans le [Guide de planification et d’implémentation][planification-guide]), la machine virtuelle doit être associée à un domaine local. Les points à prendre en compte pour cette étape sont décrits dans le chapitre [Joindre une machine virtuelle à un domaine local (Windows uniquement)][déploiement-guide-4.3] de ce document.

#### <a name="configure-proxy-settings"></a>Configurer les paramètres de proxy
Selon votre configuration de réseau local, vous devrez peut-être configurer le proxy sur votre machine virtuelle si elle est connectée à votre réseau local via VPN ou ExpressRoute. Si vous ne le faites pas, la machine virtuelle peut ne pas être en mesure d’accéder à Internet et par conséquent ne pas pouvoir télécharger les extensions requises ni collecter des données d’analyse. Consultez le chapitre [Configurer le proxy][deployment-guide-configure-proxy] de ce document.

#### <a name="configure-monitoring"></a>Configuration de l’analyse
Pour garantir un environnement pris en charge par SAP, configurez l’extension d’analyse Azure améliorée pour SAP, comme décrit dans le chapitre [Configurer l’extension d’analyse Azure améliorée pour SAP][deployment-guide-4.5] de ce document.

Vérifiez la configuration requise pour l’analyse SAP pour connaître les versions minimales requises pour le noyau SAP et l’agent hôte SAP dans les ressources répertoriées dans le chapitre [Ressources SAP][déploiement-guide-2.2] de ce document.

#### <a name="monitoring-check"></a>Vérification de l’analyse
Vérifiez si l’analyse fonctionne comme décrit dans le chapitre [Vérifications et résolution des problèmes pour la configuration de l’analyse de bout en bout pour SAP sur Azure][deployment-guide-troubleshooting-chapter].

### <a name="scenario-4-updating-the-monitoring-configuration-for-sap"></a>Scénario 4 : Mise à jour de la configuration de l’analyse pour SAP
Dans les cas suivants, vous devez mettre à jour la configuration de l’analyse SAP :

* L’équipe MS/SAP a étendu les capacités d’analyse et a décidé d’ajouter ou de supprimer des compteurs. 
* Microsoft introduit une nouvelle version de l’infrastructure Azure sous-jacente générant les données d’analyse, et l’extension d’analyse Azure améliorée pour SAP doit s’adapter à ces modifications.
* Vous montez des disques durs virtuels supplémentaires sur votre machine virtuelle Azure ou vous supprimez un disque dur virtuel. Dans ce cas, vous devez mettre à jour la collection de données liées au stockage. Si vous ajoutez ou supprimez des points de terminaison, ou si vous affectez des adresses IP à une machine virtuelle, cela n’affecte pas la configuration de l’analyse.
* Vous modifiez la taille de votre machine virtuelle Azure, en passant par exemple de la taille A5 à une autre taille.
* Vous ajoutez de nouvelles interfaces de réseau à votre machine virtuelle Azure.

Pour mettre à jour la configuration de l’analyse, procédez comme suit :

* Mettez à jour l’infrastructure d’analyse en suivant les étapes décrites dans le chapitre [Configurer l’extension d’analyse Azure améliorée pour SAP][déploiement-guide-4.5] de ce document. La réexécution du script décrit dans ce chapitre permet de détecter si une configuration est déployée, et d’effectuer les modifications nécessaires à la configuration de l’analyse. 

## <a name="detailed-single-deployment-steps"></a>Étapes de déploiement détaillées
### <a name="a-name604bcec2-8b6e-48d2-a944-61b0f5dee2f7adeploying-azure-powershell-cmdlets"></a><a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Déploiement d’applets de commande Azure PowerShell
* Accédez à <https://azure.microsoft.com/downloads/>
* Dans la section « Outils de ligne de commande », vous trouverez une section intitulée « PowerShell ». Suivez le lien « Installation Windows ».
* Le gestionnaire de téléchargement Microsoft affiche un élément de ligne se terminant par .exe. Sélectionnez l’option « Exécuter ».
* Une fenêtre contextuelle vous demande si vous voulez exécuter le programme d’installation de la plate-forme Web Microsoft. Choisissez OUI.
* Un écran semblable à celui-ci s’affiche :

![Écran d’installation pour les applets de commande PowerShell d’Azure][deployment-guide-figure-500]
<a name="figure-5"></a>

* Appuyez sur Installer et acceptez les CLUF.

Vérifier régulièrement si les applets de commande PowerShell ont été mis à jour. Il sont généralement des mis à jour tous les mois. Le moyen le plus simple pour ce faire consiste à suivre les étapes d’installation décrites plus haut jusqu’à l’écran d’installation illustré dans [cette][deployment-guide-figure-5] figure. Sur cet écran, la date de publication des applets de commande est affichée, ainsi que le numéro de version effectif. Sauf indication contraire dans les notes SAP [1928533] ou [2015553], il est recommandé d’utiliser la dernière version des applets de commande Azure PowerShell.

La version actuellement installée des applets de commande Azure sur l’ordinateur de bureau/portable peut être vérifiée à l’aide de la commande PS :

```powershell
Import-Module Azure
(Get-Module Azure).Version
```

Le résultat doit se présenter comme indiqué ci-dessous dans [cette][deployment-guide-figure-6] figure.

![Résultat du contrôle de la version de l’applet de commande Azure PS][deployment-guide-figure-600]
<a name="figure-6"></a>

Si la version de l’applet de commande Azure installée sur l’ordinateur de bureau/portable est la version actuelle, le premier écran après le démarrage du programme d’installation de la plateforme Web Microsoft sera légèrement différent de celui illustré dans [cette][deployment-guide-figure-5] figure.

Veuillez noter le cercle rouge dans la [figure][deployment-guide-figure-7] ci-dessous.

![Écran d’installation pour les applets de commande Azure PowerShell indiquant que les versions les plus récentes des applets de commande Azure PS sont installées.][deployment-guide-figure-700]
<a name="figure-7"></a>

Si l’écran se présente comme [ci-dessus][deployment-guide-figure-7] et indique que la dernière version la plus récente de l’applet de commande Azure est déjà installée, il est inutile de poursuivre l’installation. Dans ce cas vous pouvez quitter l’installation à ce stade.

### <a name="a-name1ded9453-1330-442a-86ea-e0fd8ae8cab3adeploying-azure-cli"></a><a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Déploiement de l’interface de ligne de commande Azure
* Accédez à <https://azure.microsoft.com/downloads/>
* Dans la section « Outils de ligne de commande », vous trouverez une section intitulée « Interface de ligne de commande Azure ». Suivez le lien d’installation pour votre système d’exploitation.

Vérifiez régulièrement si l’interface de ligne de commande Azure a été mise à jour. Il sont généralement des mis à jour tous les mois. Le moyen le plus simple pour ce faire consiste à suivre les étapes d’installation détaillées ci-dessus.

La version actuellement installée de l’interface de ligne de commande Azure sur l’ordinateur de bureau/portable peut être vérifiée à l’aide de la commande :

```
azure --version
```

Le résultat doit se présenter comme indiqué ci-dessous dans [cette][deployment-guide-figure-azure-cli-version] figure.

![Résultat du contrôle de la version de l’interface de ligne de commande Azure][deployment-guide-figure-760]
<a name="0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda"></a>

### <a name="a-name31d9ecd6-b136-4c73-b61e-da4a29bbc9ccajoin-vm-into-on-premises-domain-windows-only"></a><a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>Joindre une machine virtuelle à un domaine local (Windows uniquement)
Si vous déployez des machines virtuelles SAP dans le cadre d’un scénario intersite où les instances AD et DNS locales sont étendues dans Azure, les machines virtuelles doivent être jointes à un domaine local. Les étapes détaillées de connexion d’une machine virtuelle à un domaine local et les logiciels supplémentaires requis pour les membres d’un domaine local dépendent du client. Généralement, la connexion d’une machine virtuelle à un domaine local consiste à installer des logiciels supplémentaires tels que des logiciels de protection contre les programmes malveillants ou divers agents de sauvegarde ou d’analyse.

En outre, pour les cas où les paramètres de proxy Internet sont imposés pour la connexion à un domaine, vous devez vous assurer que le compte LocalSystem Windows (S-1-5-18) de la machine virtuelle invitée a également ces paramètres. Le plus simple est d’imposer le proxy avec la stratégie de groupe de domaine qui s’applique aux systèmes du domaine.

### <a name="a-namec7cbb0dc-52a4-49db-8e03-83e7edc2927dadownload-install-and-enable-azure-vm-agent"></a><a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>Télécharger, installer et activer l’agent de machine virtuelle Azure
Pour les machines virtuelles déployées à partir d’une image de système d’exploitation qui n’est pas généralisée (par exemple, pas préparée avec Sysprep pour Windows), vous devez télécharger, installer et activer l’agent de machine virtuelle Azure manuellement. 

Pour les machines virtuelles déployées à partir d’Azure Marketplace, cette étape n’est pas nécessaire, car ces images contiennent déjà l’agent de machine virtuelle Azure.

#### <a name="a-nameb2db5c9a-a076-42c6-9835-16945868e866awindows"></a><a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows
* Téléchargez l’agent de machine virtuelle Azure :
  * Téléchargez le package d’installation de l’agent de machine virtuelle Azure à partir de : <https://go.microsoft.com/fwlink/?LinkId=394789>
  * Stockez le package MSI de l’agent de machine virtuelle localement sur l’ordinateur portable ou sur un serveur.
* Installez l’agent de machine virtuelle Azure :
  * Connectez-vous à la machine virtuelle Azure déployée avec le Bureau à distance (RDP)
  * Ouvrez une fenêtre de l’explorateur Windows sur la machine virtuelle et choisissez un répertoire cible pour le fichier MSI de l’agent de machine virtuelle
  * Glissez et déposez le fichier MSI du programme d’installation de l’agent de machine virtuelle Azure de votre ordinateur portable/serveur local au répertoire cible de l’agent de machine virtuelle dans la machine virtuelle.
  * Double-cliquez sur le fichier MSI dans la machine virtuelle
  * Pour les machines virtuelles associées à des domaines locaux, veillez à ce que les paramètres de proxy Internet s’appliquent au compte système local Windows (S-1-5-18) de la machine virtuelle, comme décrit dans le chapitre [Configurer le proxy][deployment-guide-configure-proxy]. L’agent de machine virtuelle s’exécute dans ce contexte et doit être en mesure de se connecter à Azure.

La mise à jour de l’agent de machine virtuelle Azure ne nécessite aucune intervention de la part de l’utilisateur. L’agent de machine virtuelle se met à jour automatiquement et ne nécessite pas un redémarrage de la machine virtuelle.

#### <a name="a-name6889ff12-eaaf-4f3c-97e1-7c9edc7f7542alinux"></a><a name="6889ff12-eaaf-4f3c-97e1-7c9edc7f7542"></a>Linux
Installez l’agent de machine virtuelle pour Linux à l’aide de la commande suivante.

* **SLES**

```
sudo zypper install WALinuxAgent
```
* **RHEL**

```
sudo yum install WALinuxAgent
```

Suivez les étapes de [cet article][virtual-machines-linux-update-agent] pour mettre à jour l’agent Linux Azure s’il est déjà installé. 

### <a name="a-namebaccae00-6f79-4307-ade4-40292ce4e02daconfigure-proxy"></a><a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>Configurer le proxy
Les étapes de configuration du proxy varient entre Windows et Linux.

#### <a name="windows"></a>Windows
Ces paramètres doivent également être valides pour que le compte LocalSystem puisse accéder à Internet. Si vos paramètres de proxy ne sont pas définis par la stratégie de groupe, vous pouvez les configurer pour le compte LocalSystem. Procédez comme suit :

1. Ouvrez gpedit.msc.
2. Accédez à Configuration ordinateur > Modèles d’administration > Composants Windows > Internet Explorer. Assurez-vous que le paramètre de stratégie « Paramètres machine du serveur proxy (plutôt que les paramètres individualisés) » est activé.
3. Ouvrez le panneau de configuration et accédez à Réseau et Internet -> Options Internet.
4. Ouvrez l’onglet Connexions, cliquez sur Paramètres réseau.
5. Désactivez « Détecter automatiquement les paramètres de connexion ».
6. Activez « Utiliser un serveur proxy pour votre réseau local » et entrez l’adresse et le port du proxy
7. Cliquez sur Avancé
8. Ajoutez l’adresse IP 168.63.129.16 à la liste des exceptions

#### <a name="linux"></a>Linux
Configurer le proxy approprié dans le fichier de configuration de l’agent invité Microsoft Azure, qui se trouve dans/etc/waagent.conf. Les paramètres suivants doivent être définis :

```
HttpProxy.Host=<proxy host e.g. proxy.corp.local>
HttpProxy.Port=<port of the proxy host e.g. 80>
```

Redémarrez l’agent après avoir modifié sa configuration avec

```
sudo service waagent restart
```

Les paramètres de proxy dans /etc/waagent.conf s’appliquent également pour les extensions de machine virtuelle requises. Si vous voulez utiliser les référentiels Azure, assurez-vous que le trafic vers ces référentiels ne passe pas par l’intranet local. Si vous avez créé des itinéraires définis par les utilisateurs pour activer le tunneling forcé, veillez à ajouter un itinéraire qui achemine le trafic vers les référentiels directement vers Internet, et non par le biais de votre connexion de site à site.

* **SLES** Vous devez également ajouter des itinéraires pour les adresses IP répertoriées dans /etc/regionserverclnt.cfg. Cet exemple est présenté dans la capture d’écran ci-dessous. 
* **RHEL** Vous devez également ajouter des itinéraires pour les adresses IP des hôtes répertoriés dans /etc/yum.repos.d/rhui-load-balancers. Cet exemple est présenté dans la capture d’écran ci-dessous.

Pour plus d’informations sur les itinéraires définis par les utilisateurs, consultez [cet article][virtual-networks-udr-overview].

![Tunneling forcé][deployment-guide-figure-50]

### <a name="a-named98edcd3-f2a1-49f7-b26a-07448ceb60caaconfigure-azure-enhanced-monitoring-extension-for-sap"></a><a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>Configurer l’extension d’analyse Azure améliorée pour SAP
Une fois que la machine virtuelle est préparée comme décrit dans le chapitre [Scénarios de déploiement de machines virtuelles pour SAP sur Microsoft Azure][deployment-guide-3], l’agent de machine virtuelle Azure est installé sur la machine virtuelle. L’étape importante suivante consiste à déployer l’extension d’analyse Azure améliorée pour SAP, qui est disponible dans le référentiel d’extensions Azure dans les centres de données mondiaux de Microsoft Azure. Pour plus d’informations, veuillez consulter le [Guide de planification et d’implémentation][planification-guide-9.1]. 

Vous pouvez utiliser Azure PowerShell ou l’interface de ligne de commande Azure pour installer et configurer l’extension d’analyse Azure améliorée pour SAP. Lisez le chapitre [Azure PowerShell][déploiement-guide-4.5.1] si vous voulez installer l’extension sur une machine virtuelle Windows ou Linux à l’aide d’une machine Windows. Pour installer l’extension sur une machine virtuelle Linux à l’aide d’un ordinateur de bureau Linux, lisez le chapitre [Interface de ligne de commande Azure][déploiement-guide-4.5.2].

#### <a name="a-name987cf279-d713-4b4c-8143-6b11589bb9d4aazure-powershell-for-linux-and-windows-vms"></a><a name="987cf279-d713-4b4c-8143-6b11589bb9d4"></a>Azure PowerShell pour les machines virtuelles Linux et Windows
Pour installer l’extension d’analyse Azure améliorée pour SAP, procédez comme suit :

* Vérifiez que vous avez installé la dernière version de l’applet de commande Microsoft Azure PowerShell. Consultez le chapitre [Déploiement d’applets de commande Azure PowerShell][déploiement-guide-4.1] de ce document.  
* Exécutez l’applet de commande PowerShell suivant.
    Pour obtenir la liste des environnements disponibles, exécutez l’applet de commande Get-AzureRmEnvironment. Si vous voulez utiliser la version publique d’Azure, votre environnement est AzureCloud. Pour Azure en Chine, sélectionnez AzureChinaCloud.

```powershell
    $env = Get-AzureRmEnvironment -Name <name of the environment>
    Login-AzureRmAccount -Environment $env
    Set-AzureRmContext -SubscriptionName <subscription name>

    Set-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
```

Une fois que vous avez fourni des données de votre compte et de la machine virtuelle Azure, le script déploie les extensions requises et active les fonctionnalités requises. Ceci peut prendre plusieurs minutes.
Veuillez lire [cet article MSDN][msdn-set-azurermvmaemextension] pour plus d’informations sur l’extension Set-AzureRmVMAEMExtension.

![Écran de résultat de l’exécution réussie de l’applet de commande Azure spécifique à SAP Set-AzureRmVMAEMExtension][deployment-guide-figure-900]

Une exécution réussie de Set-AzureRmVMAEMExtension effectue toutes les étapes nécessaires à la configuration de la fonctionnalité d’analyse de l’hôte pour SAP. 

Le résultat renvoyé par le script doit ressembler à cela :

* Confirmation que la configuration de l’analyse pour le disque dur virtuel de base (contenant le système d’exploitation) ainsi que tous les disques durs virtuels supplémentaires montés sur la machine virtuelle ont été configurés.
* Les deux messages suivants confirment la configuration des métriques de stockage pour un compte de stockage spécifique. 
* Une des lignes de sortie fournit l’état de la mise à jour réelle de la configuration de l’analyse.
* Une autre confirme que la configuration a été déployée ou mise à jour.
* La dernière ligne de la sortie est informative et indique la possibilité de tester la configuration de l’analyse.
* Pour vérifier que toutes les étapes de l’analyse Azure améliorée ont été exécutées correctement et que l’infrastructure Azure fournit les données nécessaires, vérifiez la disponibilité de l’extension d’analyse Azure améliorée pour SAP, comme décrit dans le chapitre [Vérification de la disponibilité de l’analyse Azure améliorée pour SAP][deployment-guide-5.1] de ce document. 
* Pour ce faire, patientez 15 à 30 minutes, jusqu’à ce que les diagnostics Azure aient collecté les données appropriées.

#### <a name="a-name408f3779-f422-4413-82f8-c57a23b4fc2faazure-cli-for-linux-vms"></a><a name="408f3779-f422-4413-82f8-c57a23b4fc2f"></a>Interface de ligne de commande Azure pour machines virtuelles Linux
Pour installer l’extension d’analyse Azure améliorée pour SAP, procédez comme suit :

1. Installez l’interface de ligne de commande Azure comme décrit dans [cet][azure-cli] article
2. Connectez-vous avec votre compte Azure
   
    ```
    azure login
    ```
3. Basculez en mode Azure Resource Manager
   
    ```
    azure config mode arm
    ```
4. Activez la surveillance Azure améliorée pour SAP
   
    ```
    azure vm enable-aem <resource-group-name> <vm-name>
    ```  
5. Vérifiez que la surveillance améliorée Azure est active sur la machine virtuelle Azure Linux. Vérifiez que le fichier /var/lib/AzureEnhancedMonitor/PerfCounters existe. S’il existe, il affiche des informations collectées par AEM avec :
   
    ```
    cat /var/lib/AzureEnhancedMonitor/PerfCounters
    ```
    Vous obtiendrez un résultat tel que celui-ci :
   
    ```
    2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
    2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
    …
    …
    ```

## <a name="a-name564adb4f-5c95-4041-9616-6635e83a810bachecks-and-troubleshooting-for-end-to-end-monitoring-setup-for-sap-on-azure"></a><a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>Vérifications et résolution des problèmes pour la configuration de l’analyse de bout en bout pour SAP sur Azure
Une fois que vous avez déployé votre machine virtuelle Azure et configuré l’infrastructure d’analyse Azure appropriée, vérifiez si tous les composants de l’analyse Azure améliorée fonctionnent correctement. 

Par conséquent, vérifiez la disponibilité de l’extension d’analyse Azure améliorée pour SAP, comme décrit dans le chapitre [Vérification de la disponibilité de l’analyse Azure améliorée pour SAP][deployment-guide-5.1]. Si le résultat de cette vérification est positif et que vous obtenez tous les compteurs de performances pertinents, cela signifie que l’analyse Azure a été correctement configurée. Dans ce cas, passez à l’installation de l’agent hôte SAP comme décrit dans les notes SAP répertoriées dans le chapitre [Ressources SAP][déploiement-guide-2.2] de ce document. Si le résultat de la vérification de disponibilité signale des compteurs manquants, passez au contrôle d’intégrité de l’infrastructure d’analyse Azure décrite dans le chapitre [Contrôle d’intégrité de la configuration de l’infrastructure d’analyse Azure][deployment-guide-5.2]. En cas de problème avec la configuration de l’analyse Azure, consultez le chapitre [Étapes supplémentaires de la résolution des problèmes de l’infrastructure d’analyse Azure pour SAP][deployment-guide-5.3] pour plus d’informations sur la résolution des problèmes.

### <a name="a-namebb61ce92-8c5c-461f-8c53-39f5e5ed91f2areadiness-check-for-azure-enhanced-monitoring-for-sap"></a><a name="bb61ce92-8c5c-461f-8c53-39f5e5ed91f2"></a>Vérification de la disponibilité de l’analyse Azure améliorée pour SAP
Cette vérification vous assure que les métriques affichées au sein de votre application SAP sont entièrement fournies par l’infrastructure d’analyse Azure sous-jacente. 

#### <a name="execute-the-readiness-check-on-a-windows-vm"></a>Vérifier la disponibilité d’une machine virtuelle Windows
Pour vérifier la disponibilité, connectez-vous à la machine virtuelle Azure (pas besoin d’utiliser un compte d’administrateur) et exécutez les étapes suivantes :

* Ouvrez une invite de commandes Windows et accédez au dossier d’installation de l’extension d’analyse Azure pour SAP : C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\\`<version`> \drop

La version figurant dans le chemin d’accès à l’extension d’analyse ci-dessus peut varier. Si vous voyez plusieurs dossiers de la version de l’extension d’analyse dans le dossier d’installation, vérifiez la configuration du service Windows « AzureEnhancedMonitoring » et accédez au dossier indiqué en tant que « Chemin d’accès des fichiers exécutables ».

![Propriétés du service exécutant l’extension d’analyse Azure améliorée pour SAP][deployment-guide-figure-1000]

* Exécutez azperflib.exe dans la fenêtre de commande sans aucun paramètre.

> [!NOTE]
> Le fichier exécutable azperflib.exe fonctionne en boucle et met à jour les compteurs collectés toutes les 60 secondes. Pour mettre fin à la boucle, fermez la fenêtre de commande.
> 
> 

Si l’extension d’analyse Azure améliorée n’est pas installée ou que le service « AzureEnhancedMonitoring » n’est pas en cours d’exécution, cela signifie que l’extension n’a pas été configurée correctement. Dans ce cas, consultez le chapitre [Étapes supplémentaires de la résolution des problèmes de l’infrastructure d’analyse Azure pour SAP][déploiement-guide-5.3] pour obtenir des instructions détaillées sur le redéploiement de l’extension.

##### <a name="check-the-output-of-azperflibexe"></a>Vérifiez la sortie renvoyée par azperflib.exe
La sortie de azperflib.exe indique tous les compteurs de performances Azure remplis pour SAP. En bas de la liste des compteurs collectés, vous trouverez un résumé et un indicateur d’intégrité, qui indique l’état de l’analyse Azure. 

![Sortie du contrôle d’intégrité effectué avec azperflib.exe indiquant l’absence de problèmes][deployment-guide-figure-1100]
<a name="figure-11"></a>

Vérifiez le résultat renvoyé pour la sortie du « Nombre de compteurs » signalés comme vides et pour le contrôle d’intégrité, comme illustré dans la figure [ci-dessus][deployment-guide-figure-11].

Vous pouvez interpréter les valeurs comme suit :

| Résultats du fichier exécutable azperflib.exe | Surveillance de l’état d’intégrité Azure |
| --- | --- |
| **Appels de l’API - non disponibles** | Les compteurs qui ne sont pas disponibles peuvent ne pas être concernés par la configuration de la machine virtuelle ou les erreurs - voir l’état d’intégrité | 
| **Nombre total de compteurs : vide** |Les 2 compteurs suivants de stockage Azure peuvent être vides :  <ul><li>Stockage Lecture Op Latence Serveur msec</li><li>Stockage Lecture Op Latence E2E msec</li></ul>Tous les autres compteurs doivent contenir des valeurs. |
| **Contrôle d’intégrité** |Uniquement OK si l’état renvoyé est OK. |
| **Diagnostics** |Informations détaillées sur l’état d’intégrité |

Si la valeur de contrôle d’intégrité n’est pas correcte, suivez les instructions du contrôle d’intégrité pour la configuration de l’infrastructure d’analyse Azure comme décrit dans le chapitre [Contrôle d’intégrité de la configuration de l’infrastructure d’analyse Azure][deployment-guide-5.2] ci-dessous.

#### <a name="execute-the-readiness-check-on-a-linux-vm"></a>Vérifier la disponibilité d’une machine virtuelle Linux
Pour vérifier la disponibilité, connectez-vous à la machine virtuelle Azure avec SSH et exécutez les étapes suivantes :

* Vérifier le résultat de l’extension d’analyse Azure améliorée
  * more /var/lib/AzureEnhancedMonitor/PerfCounters
    * Doit vous fournir une liste des compteurs de performances. Le fichier ne doit pas être vide.
  * cat /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error
    * Doit retourner une ligne où l’erreur est « none », par exemple 3;config;Error;;0;0;**none**;0;1456416792;tst-servercs;
  * more /var/lib/AzureEnhancedMonitor/LatestErrorRecord
    * Doit être vide ou ne doit pas exister.
* Si la première vérification ci-dessus n’a pas réussi, effectuez ces tests supplémentaires :
  * Assurez-vous que le waagent est installé et démarré.
    * sudo ls -al /var/lib/waagent/
      * Doit répertorier le contenu du répertoire waagent.
    * ps -ax | grep waagent
      * Doit afficher une entrée similaire à « python /usr/sbin/waagent-daemon »
  * Assurez-vous que l’extension de diagnostic Linux est installée et démarrée.
    * sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.LinuxDiagnostic-*'
      * Doit répertorier le contenu du répertoire de l’extension de diagnostic Linux.
    * ps -ax | grep diagnostic
      * Doit afficher une entrée similaire à « python /var/lib/waagent/Microsoft.OSTCExtensions.LinuxDiagnostic-2.0.92/diagnostic.py -daemon ».
  * Assurez-vous que l’extension d’analyse Azure améliorée est installée et démarrée.
    * sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/'
      * Doit répertorier le contenu du répertoire de l’extension d’analyse Azure améliorée.
    * ps -ax | grep AzureEnhanced
      * Doit afficher une entrée similaire à « python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon ».
* Installez l’agent hôte SAP comme décrit dans la note SAP [1031096] et vérifiez le résultat de saposcol
  * Exécutez /usr/sap/hostctrl/exe/saposcol -d.
  * Exécutez dump ccm.
  * Vérifiez si la métrique « Virtualization_Configuration\Enhanced Monitoring Access » est vraie.
* Si vous avez déjà installé un serveur d’applications ABAP NetWeaver SAP, ouvrez la transaction ST06 et regardez si l’analyse améliorée est activée.

Si une ou plusieurs des vérifications ci-dessus échouent, consultez le chapitre [Étapes supplémentaires de la résolution des problèmes de l’infrastructure d’analyse Azure pour SAP][déploiement-guide-5.3] pour obtenir des instructions détaillées sur le redéploiement de l’extension.

### <a name="a-namee2d592ff-b4ea-4a53-a91a-e5521edb6cd1ahealth-check-for-azure-monitoring-infrastructure-configuration"></a><a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>Contrôle d’intégrité de la configuration de l’infrastructure d’analyse Azure
Si certaines des données d’analyse ne sont pas fournies correctement, comme indiqué par le test décrit dans le chapitre [Vérification de la disponibilité de l’analyse Azure améliorée pour SAP][déploiement-guide-5.1] ci-dessus, exécutez l’applet de commande Test-AzureRmVMAEMExtension pour tester si l’infrastructure d’analyse Azure et l’extension d’analyse pour SAP sont correctes.

Pour tester la configuration de l’analyse, exécutez la séquence suivante :

* Assurez-vous d’avoir installé la dernière version de l’applet de commande Microsoft Azure PowerShell comme, décrit dans le chapitre [Déploiement d’applets de commande Azure PowerShell][déploiement-guide-4.1] de ce document.
* Exécutez l’applet de commande PowerShell suivant. Pour obtenir la liste des environnements disponibles, exécutez l’applet de commande Get-AzureRmEnvironment. Si vous voulez utiliser la version publique d’Azure, votre environnement est AzureCloud. Pour Azure en Chine, sélectionnez AzureChinaCloud.

```powershell
$env = Get-AzureRmEnvironment -Name <name of the environment>
Login-AzureRmAccount -Environment $env
Set-AzureRmContext -SubscriptionName <subscription name>
Test-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
```

* Une fois que vous avez fourni les données de votre compte et la machine virtuelle Azure, le script teste la configuration de la machine virtuelle que vous choisissez.

![Écran d’entrée de l’applet de commande Azure spécifique à SAP Test-VMConfigForSAP_GUI][deployment-guide-figure-1200]

Une fois que vous avez saisi les informations relatives à votre compte et à la machine virtuelle Azure, le script teste la configuration de la machine virtuelle que vous choisissez.

![Sortie du test réussi de l’infrastructure d’analyse Azure pour SAP][deployment-guide-figure-1300]

Assurez-vous que chaque contrôle renvoie le résultat OK. Si certains des contrôles ne renvoient pas OK, exécutez l’applet de commande mise à jour comme décrit dans le chapitre [Configurer l’extension d’analyse Azure améliorée pour SAP][déploiement-guide-4.5] de ce document. Attendez 15 minutes supplémentaires et effectuez les vérifications décrites dans les chapitres [Vérification de la disponibilité de l’analyse Azure améliorée pour SAP][déploiement-guide-5.1] et [Contrôle d’intégrité de la configuration de l’infrastructure d’analyse Azure][déploiement-guide-5.2] à nouveau. Si les vérifications indiquent encore un problème avec certains ou tous les compteurs, passez au chapitre [Étapes supplémentaires de résolution des problèmes de l’infrastructure d’analyse Azure pour SAP][déploiement-guide-5.3].

### <a name="a-namefe25a7da-4e4e-4388-8907-8abc2d33cfd8afurther-troubleshooting-of-azure-monitoring-infrastructure-for-sap"></a><a name="fe25a7da-4e4e-4388-8907-8abc2d33cfd8"></a>Étapes supplémentaires de la résolution des problèmes de l’infrastructure d’analyse Azure pour SAP
#### <a name="windowslogowindows-azure-performance-counters-do-not-show-up-at-all"></a>![Windows][Logo_Windows] Les compteurs de performances Azure ne s’affichent pas
Les métriques de performances sur Azure sont collectées par le service Windows « AzureEnhancedMonitoring ». Si le service n’a pas été installé correctement ou s’il n’est pas exécuté sur votre machine virtuelle, aucune métrique de performance ne peut être collectée.

##### <a name="the-installation-directory-of-the-azure-enhanced-monitoring-extension-is-empty"></a>Le répertoire d’installation de l’extension d’analyse Azure améliorée est vide.
###### <a name="issue"></a>Problème
Le répertoire d’installation C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\\`<version`>\drop est vide.

###### <a name="solution"></a>Solution
L’extension n’est pas installée. Vérifiez s’il s’agit d’un problème de proxy (comme décrit précédemment). Vous devrez peut-être redémarrer la machine et/ou réexécuter le script de configuration Set-AzureRmVMAEMExtension.

##### <a name="service-for-azure-enhanced-monitoring-does-not-exist"></a>Le service d’analyse Azure améliorée n’existe pas.
###### <a name="issue"></a>Problème
Le service WindowsAzureEnhancedMonitoring n’existe pas. Azperflib.exe : la sortie du fichier exécutable azperlib.exe génère une erreur, comme indiqué dans la [figure ci-dessous][deployment-guide-figure-14].

![L’exécution d’azperflib.exe indique que le service de l’extension d’analyse Azure améliorée pour SAP n’est pas en cours d’exécution][deployment-guide-figure-1400]
<a name="figure-14"></a>

###### <a name="solution"></a>Solution
Si le service n’existe pas comme indiqué dans la [figure ci-dessus][deployment-guide-figure-14], cela signifie que l’extension d’analyse Azure pour SAP n’a pas été installée correctement. Redéployez l’extension selon la procédure décrite pour votre scénario de déploiement dans le chapitre [Scénarios de déploiement de machines virtuelles pour SAP sur Microsoft Azure][déploiement-guide-3]. 

Après le déploiement de l’extension, vérifiez à nouveau si les compteurs de performances Azure sont fournis dans la machine virtuelle Azure après 1 heure.

##### <a name="service-for-azure-enhanced-monitoring-exists-but-fails-to-start"></a>Le service d’analyse Azure améliorée Azure existe, mais ne parvient pas à démarrer
###### <a name="issue"></a>Problème
Le service Windows « AzureEnhancedMonitoring » existe et est activé, mais ne parvient pas à démarrer. Consultez le journal des événements de l’application pour plus d’informations.

###### <a name="solution"></a>Solution
Configuration incorrecte. Activez à nouveau l’extension d’analyse pour la machine virtuelle, comme décrit dans le chapitre [Configurer l’extension d’analyse Azure améliorée pour SAP][déploiement-guide-4.5].

#### <a name="windowslogowindows-some-azure-performance-counters-are-missing"></a>![Windows][Logo_Windows] Certains compteurs de performances Azure sont manquants
Les métriques de performances sur Azure sont collectées par le service Windows « AzureEnhancedMonitoring », qui obtient des données de plusieurs sources. Certaines données de configuration sont collectées localement, les métriques de performances sont lues à partir des diagnostics Azure, et les compteurs de stockage sont utilisés à partir de votre niveau d’abonnement de stockage de connexion.

Si la note SAP [1999351] n’a pas permis de résoudre le problème, exécutez à nouveau le script de configuration Set-AzureRmVMAEMExtension. Vous devrez peut-être attendre une heure, car il est possible que les compteurs d’analyse ou de diagnostics de stockage ne soient pas créés immédiatement après avoir été activés. Si le problème persiste, ouvrez un message de support client SAP sur le composant BC-OP-NT-AZR pour Windows ou BC-OP-LNX-AZR pour une machine virtuelle Linux.

#### <a name="linuxlogolinux-azure-performance-counters-do-not-show-up-at-all"></a>![Linux][Logo_Linux] Les compteurs de performances Azure ne s’affichent pas
Les métriques de performances sur Azure sont collectés par un démon. Si le démon ne fonctionne pas, aucune métrique de performance ne peut être collectée.

##### <a name="the-installation-directory-of-the-azure-enhanced-monitoring-extension-is-empty"></a>Le répertoire d’installation de l’extension d’analyse Azure améliorée est vide.
###### <a name="issue"></a>Problème
Le répertoire/var/lib/waagent/ne contient pas de sous-répertoire pour l’extension d’analyse Azure améliorée.

###### <a name="solution"></a>Solution
L’extension n’est pas installée. Vérifiez s’il s’agit d’un problème de proxy (comme décrit précédemment). Vous devrez peut-être redémarrer la machine et/ou réexécuter le script de configuration Set-AzureRmVMAEMExtension.

#### <a name="linuxlogolinux-some-azure-performance-counters-are-missing"></a>![Linux][Logo_Linux] Certains compteurs de performances Azure sont manquants
Les métriques de performances sur Azure sont collectées par un démon, qui obtient des données de plusieurs sources. Certaines données de configuration sont collectées localement, les métriques de performances sont lues à partir des diagnostics Azure, et les compteurs de stockage sont utilisés à partir de votre niveau d’abonnement de stockage de connexion.

Pour obtenir une liste complète et à jour des problèmes connus, consultez la note SAP [1999351] contenant des informations de dépannage supplémentaires pour l’analyse Azure améliorée pour SAP.

Si la note SAP [1999351] ne permet pas de résoudre les problèmes, exécutez à nouveau le script de configuration Set-AzureRmVMAEMExtension comme décrit dans le chapitre [Configurer l’extension d’analyse Azure améliorée pour SAP][déploiement-guide-4.5]. Vous devrez peut-être attendre une heure, car il est possible que les compteurs d’analyse ou de diagnostics de stockage ne soient pas créés immédiatement après avoir été activés. Si le problème persiste, ouvrez un message de support client SAP sur le composant BC-OP-NT-AZR pour Windows ou BC-OP-LNX-AZR pour une machine virtuelle Linux.




<!--HONumber=Jan17_HO4-->


