---
title: SAP NetWeaver sur machines virtuelles Windows - Guide de haute disponibilité | Microsoft Docs
description: Guide de haute disponibilité pour SAP NetWeaver sur machines virtuelles Windows
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''

ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/18/2016
ms.author: goraco

---
# <a name="sap-netweaver-on-windows-virtual-machines-vms-highavailability-guide"></a>SAP NetWeaver sur machines virtuelles Windows - Guide de haute disponibilité
[767598]:https://service.sap.com/sap/support/notes/767598
[773830]:https://service.sap.com/sap/support/notes/773830
[826037]:https://service.sap.com/sap/support/notes/826037
[965908]:https://service.sap.com/sap/support/notes/965908
[1031096]:https://service.sap.com/sap/support/notes/1031096
[1139904]:https://service.sap.com/sap/support/notes/1139904
[1173395]:https://service.sap.com/sap/support/notes/1173395
[1245200]:https://service.sap.com/sap/support/notes/1245200
[1409604]:https://service.sap.com/sap/support/notes/1409604
[1558958]:https://service.sap.com/sap/support/notes/1558958
[1585981]:https://service.sap.com/sap/support/notes/1585981
[1588316]:https://service.sap.com/sap/support/notes/1588316
[1590719]:https://service.sap.com/sap/support/notes/1590719
[1597355]:https://service.sap.com/sap/support/notes/1597355
[1605680]:https://service.sap.com/sap/support/notes/1605680
[1619720]:https://service.sap.com/sap/support/notes/1619720
[1619726]:https://service.sap.com/sap/support/notes/1619726
[1619967]:https://service.sap.com/sap/support/notes/1619967
[1750510]:https://service.sap.com/sap/support/notes/1750510
[1752266]:https://service.sap.com/sap/support/notes/1752266
[1757924]:https://service.sap.com/sap/support/notes/1757924
[1757928]:https://service.sap.com/sap/support/notes/1757928
[1758182]:https://service.sap.com/sap/support/notes/1758182
[1758496]:https://service.sap.com/sap/support/notes/1758496
[1772688]:https://service.sap.com/sap/support/notes/1772688
[1814258]:https://service.sap.com/sap/support/notes/1814258
[1882376]:https://service.sap.com/sap/support/notes/1882376
[1909114]:https://service.sap.com/sap/support/notes/1909114
[1922555]:https://service.sap.com/sap/support/notes/1922555
[1928533]:https://service.sap.com/sap/support/notes/1928533
[1941500]:https://service.sap.com/sap/support/notes/1941500
[1956005]:https://service.sap.com/sap/support/notes/1956005
[1973241]:https://service.sap.com/sap/support/notes/1973241
[1984787]:https://service.sap.com/sap/support/notes/1984787
[1999351]:https://service.sap.com/sap/support/notes/1999351
[2002167]:https://service.sap.com/sap/support/notes/2002167
[2015553]:https://service.sap.com/sap/support/notes/2015553
[2039619]:https://service.sap.com/sap/support/notes/2039619
[2121797]:https://service.sap.com/sap/support/notes/2121797
[2134316]:https://service.sap.com/sap/support/notes/2134316
[2178632]:https://service.sap.com/sap/support/notes/2178632
[2191498]:https://service.sap.com/sap/support/notes/2191498
[2233094]:https://service.sap.com/sap/support/notes/2233094
[2243692]:https://service.sap.com/sap/support/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-cli]:../xplat-cli-install.md
[azure-portal]:https://portal.azure.com
[azure-ps]:../powershell-install-configure.md
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../azure-subscription-service-limits.md#subscription

[dbms-guide]:virtual-machines-windows-sap-dbms-guide.md (SAP NetWeaver sur machines virtuelles Windows – Guide de déploiement SGBD)
[dbms-guide-2.1]:virtual-machines-windows-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (Mise en cache pour les machines virtuelles et les disques durs virtuels)
[dbms-guide-2.2]:virtual-machines-windows-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (RAID logiciel)
[dbms-guide-2.3]:virtual-machines-windows-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Stockage Microsoft Azure)
[dbms-guide-2]:virtual-machines-windows-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (Structure d’un déploiement SGBDR)
[dbms-guide-3]:virtual-machines-windows-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (Haute disponibilité et récupération d’urgence avec les machines virtuelles Azure)
[dbms-guide-5.5.1]:virtual-machines-windows-sap-dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 et versions ultérieures)
[dbms-guide-5.5.2]:virtual-machines-windows-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 et versions antérieures)
[dbms-guide-5.6]:virtual-machines-windows-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (Utilisation d’images SQL Server issues de Microsoft Azure Marketplace)
[dbms-guide-5.8]:virtual-machines-windows-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (Résumé – SQL Server général pour SAP sur Azure)
[dbms-guide-5]:virtual-machines-windows-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (Caractéristiques de SGBDR SQL Server)
[dbms-guide-8.4.1]:virtual-machines-windows-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (Configuration du stockage)
[dbms-guide-8.4.2]:virtual-machines-windows-sap-dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (Sauvegarde et restauration)
[dbms-guide-8.4.3]:virtual-machines-windows-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (Considérations sur les performances de sauvegarde et de restauration)
[dbms-guide-8.4.4]:virtual-machines-windows-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (Autres)
[dbms-guide-900-sap-cache-server-on-premises]:virtual-machines-windows-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:./media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:./media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:./media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:./media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:./media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:./media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:./media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:./media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:./media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:virtual-machines-windows-sap-deployment-guide.md (SAP NetWeaver sur machines virtuelles Windows – Guide de déploiement)
[deployment-guide-2.2]:virtual-machines-windows-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (Ressources SAP)
[deployment-guide-3.1.2]:virtual-machines-windows-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (Déploiement d’une machine virtuelle avec une image personnalisée)
[deployment-guide-3.2]:virtual-machines-windows-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (Scénario 1 : Déploiement d’une machine virtuelle provenant d’Azure Marketplace pour SAP)
[deployment-guide-3.3]:virtual-machines-windows-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (Scénario 2 : Déploiement d’une machine virtuelle avec une image personnalisée pour SAP)
[deployment-guide-3.4]:virtual-machines-windows-sap-deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (Scénario 3 : Déplacement d’une machine virtuelle locale à l’aide d’un disque dur virtuel Azure non généralisé avec SAP)
[deployment-guide-3]:virtual-machines-windows-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Scénarios de déploiement de machines virtuelles pour SAP sur Microsoft Azure)
[deployment-guide-4.1]:virtual-machines-windows-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Déploiement d’applets de commande Azure PowerShell)
[deployment-guide-4.2]:virtual-machines-windows-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (Téléchargement et importation d’applets de commande PowerShell appropriées pour SAP)
[deployment-guide-4.3]:virtual-machines-windows-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (Joindre une machine virtuelle au domaine local – Windows uniquement)
[deployment-guide-4.4.2]:virtual-machines-windows-sap-deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux)
[deployment-guide-4.4]:virtual-machines-windows-sap-deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Télécharger, installer et activer l’agent de machine virtuelle Azure)
[deployment-guide-4.5.1]:virtual-machines-windows-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell)
[deployment-guide-4.5.2]:virtual-machines-windows-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Interface de ligne de commande Azure)
[deployment-guide-4.5]:virtual-machines-windows-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (Configurer l’extension d’analyse Azure améliorée pour SAP)
[deployment-guide-5.1]:virtual-machines-windows-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Vérification de la disponibilité de l’analyse Azure améliorée pour SAP)
[deployment-guide-5.2]:virtual-machines-windows-sap-deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Contrôle d’intégrité de la configuration de l’infrastructure d’analyse Azure)
[deployment-guide-5.3]:virtual-machines-windows-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (Étapes supplémentaires de la résolution des problèmes de l’infrastructure d’analyse Azure pour SAP)

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

[deploy-template-cli]:../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows
[deploy-template-portal]:../resource-group-template-deploy.md#deploy-with-the-preview-portal
[deploy-template-powershell]:../resource-group-template-deploy.md#deploy-with-powershell

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

[planning-guide]:virtual-machines-windows-sap-planning-guide.md (SAP NetWeaver sur machines virtuelles Windows – Guide de planification et d’implémentation)
[planning-guide-1.2]:virtual-machines-windows-sap-planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (Ressources)
[planning-guide-11]:virtual-machines-windows-sap-planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (Haute disponibilité (HA) et récupération d’urgence (DR) pour SAP NetWeaver s’exécutant sur des machines virtuelles Azure)
[planning-guide-11.4.1]:virtual-machines-windows-sap-planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (Haute disponibilité pour les serveurs d’applications SAP)
[planning-guide-11.5]:virtual-machines-windows-sap-planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (Utilisation du démarrage automatique pour les instances SAP)
[planning-guide-2.1]:virtual-machines-windows-sap-planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (Cloud uniquement : déploiement de machines virtuelles dans Azure sans dépendances du réseau local du client)
[planning-guide-2.2]:virtual-machines-windows-sap-planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (Intersite : déploiement d’une ou plusieurs machines virtuelles SAP dans Azure, dans lequel une intégration complète au réseau local est nécessaire)
[planning-guide-3.1]:virtual-machines-windows-sap-planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Régions Azure)
[planning-guide-3.2.1]:virtual-machines-windows-sap-planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (Domaines d’erreur)
[planning-guide-3.2.2]:virtual-machines-windows-sap-planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (Domaines de mise à niveau)
[planning-guide-3.2.3]:virtual-machines-windows-sap-planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Groupes à haute disponibilité Azure)
[planning-guide-3.2]:virtual-machines-windows-sap-planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (Concept de la machine virtuelle Microsoft Azure)
[planning-guide-3.3.2]:virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Stockage Premium Azure)
[planning-guide-5.1.1]:virtual-machines-windows-sap-planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (Déplacement d’une machine virtuelle locale vers Azure avec un disque non généralisé)
[planning-guide-5.1.2]:virtual-machines-windows-sap-planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (Déploiement d’une machine virtuelle avec une image spécifique du client)
[planning-guide-5.2.1]:virtual-machines-windows-sap-planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (Préparation du déplacement d’une machine virtuelle locale vers Azure avec un disque non généralisé)
[planning-guide-5.2.2]:virtual-machines-windows-sap-planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (Préparation du déploiement d’une machine virtuelle avec une image spécifique du client pour SAP)
[planning-guide-5.2]:virtual-machines-windows-sap-planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Préparation de machines virtuelles avec SAP pour Azure)
[planning-guide-5.3.1]:virtual-machines-windows-sap-planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Différence entre un disque Azure et une image Azure)
[planning-guide-5.3.2]:virtual-machines-windows-sap-planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (Chargement d’un disque dur virtuel local vers Azure)
[planning-guide-5.4.2]:virtual-machines-windows-sap-planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Copie de disques entre comptes Azure Storage)
[planning-guide-5.5.1]:virtual-machines-windows-sap-planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (Structure de machine virtuelle/disque dur virtuel pour les déploiements SAP)
[planning-guide-5.5.3]:virtual-machines-windows-sap-planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (Paramétrage du montage automatique pour les disques attachés)
[planning-guide-7.1]:virtual-machines-windows-sap-planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (Machine virtuelle unique avec scénario de démonstration/formation SAP NetWeaver)
[planning-guide-7]:virtual-machines-windows-sap-planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (Concepts de déploiement cloud uniquement d’instances SAP)
[planning-guide-9.1]:virtual-machines-windows-sap-planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Solution de surveillance Azure pour SAP)
[planning-guide-azure-premium-storage]:virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Stockage Premium Azure)

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

[sap-ha-guide]:virtual-machines-windows-sap-high-availability-guide.md (High-availability SAP NetWeaver on Windows virtual machines)
[sap-ha-guide-1]:virtual-machines-windows-sap-high-availability-guide.md#217c5479-5595-4cd8-870d-15ab00d4f84c (Prerequisites)
[sap-ha-guide-2]:virtual-machines-windows-sap-high-availability-guide.md#42b8f600-7ba3-4606-b8a5-53c4f026da08 (Resources)
[sap-ha-guide-3]:virtual-machines-windows-sap-high-availability-guide.md#42156640c6-01cf-45a9-b225-4baa678b24f1 (High-availability SAP with Azure Resource Manager vs. the classic deployment model)
[sap-ha-guide-3.1]:virtual-machines-windows-sap-high-availability-guide.md#f76af273-1993-4d83-b12d-65deeae23686 (Resource groups)
[sap-ha-guide-3.2]:virtual-machines-windows-sap-high-availability-guide.md#3e85fbe0-84b1-4892-87af-d9b65ff91860 (Clustering with Azure Resource Manager vs. the classic deployment model)
[sap-ha-guide-4]:virtual-machines-windows-sap-high-availability-guide.md#8ecf3ba0-67c0-4495-9c14-feec1a2255b7 (Windows Server Failover Clustering)
[sap-ha-guide-4.1]:virtual-machines-windows-sap-high-availability-guide.md#1a3c5408-b168-46d6-99f5-4219ad1b1ff2 (Quorum modes)
[sap-ha-guide-5]:virtual-machines-windows-sap-high-availability-guide.md#fdfee875-6e66-483a-a343-14bbaee33275 (Windows Server Failover Clustering on-premises)
[sap-ha-guide-5.1]:virtual-machines-windows-sap-high-availability-guide.md#be21cf3e-fb01-402b-9955-54fbecf66592 (Shared storage)
[sap-ha-guide-5.2]:virtual-machines-windows-sap-high-availability-guide.md#ff7a9a06-2bc5-4b20-860a-46cdb44669cd (Networking and name resolution)
[sap-ha-guide-6]:virtual-machines-windows-sap-high-availability-guide.md#2ddba413-a7f5-4e4e-9a51-87908879c10a (Windows Server Failover Clustering in Azure)
[sap-ha-guide-6.1]:virtual-machines-windows-sap-high-availability-guide.md#1a464091-922b-48d7-9d08-7cecf757f341 (Shared disk in Azure with SIOS DataKeeper)
[sap-ha-guide-6.2]:virtual-machines-windows-sap-high-availability-guide.md#44641e18-a94e-431f-95ff-303ab65e0bcb (Name resolution in Azure)
[sap-ha-guide-7]:virtual-machines-windows-sap-high-availability-guide.md#2e3fec50-241e-441b-8708-0b1864f66dfa (Haute disponibilité pour SAP NetWeaver dans Azure IaaS (Infrastructure as a Service))
[sap-ha-guide-7.1]:virtual-machines-windows-sap-high-availability-guide.md#93faa747-907e-440a-b00a-1ae0a89b1c0e (Serveurs d’applications SAP à haute disponibilité)
[sap-ha-guide-7.2]:virtual-machines-windows-sap-high-availability-guide.md#f559c285-ee68-4eec-add1-f60fe7b978db (Instance SAP ASCS/SCS à haute disponibilité)
[sap-ha-guide-7.2.1]:virtual-machines-windows-sap-high-availability-guide.md#b5b1fd0b-1db4-4d49-9162-de07a0132a51 (Instance SAP ASCS/SCS à haute disponibilité avec le clustering de basculement Windows Server dans Azure)
[sap-ha-guide-7.3]:virtual-machines-windows-sap-high-availability-guide.md#ddd878a0-9c2f-4b8e-8968-26ce60be1027 (Instance SGBD à haute disponibilité)
[sap-ha-guide-7.4]:virtual-machines-windows-sap-high-availability-guide.md#045252ed-0277-4fc8-8f46-c5a29694a816 (Scénarios de déploiement à haute disponibilité de bout en bout)
[sap-ha-guide-8]:virtual-machines-windows-sap-high-availability-guide.md#78092dbe-165b-454c-92f5-4972bdbef9bf (Préparer l’infrastructure)
[sap-ha-guide-8.1]:virtual-machines-windows-sap-high-availability-guide.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489 (Déployer des machines virtuelles avec connectivité réseau d’entreprise (intersite) pour une utilisation en production)
[sap-ha-guide-8.2]:virtual-machines-windows-sap-high-availability-guide.md#7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310 (Déploiement cloud uniquement d’instances SAP à des fins de test et de démonstration)
[sap-ha-guide-8.3]:virtual-machines-windows-sap-high-availability-guide.md#47d5300a-a830-41d4-83dd-1a0d1ffdbe6a (Réseau virtuel Azure)
[sap-ha-guide-8.4]:virtual-machines-windows-sap-high-availability-guide.md#b22d7b3b-4343-40ff-a319-097e13f62f9e (Adresses IP DNS)
[sap-ha-guide-8.5]:virtual-machines-windows-sap-high-availability-guide.md#9fbd43c0-5850-4965-9726-2a921d85d73f (Noms d’hôtes et adresses IP statiques de l’instance en cluster SAP ASCS/SCS et l’instance en cluster SGBD)
[sap-ha-guide-8.6]:virtual-machines-windows-sap-high-availability-guide.md#84c019fe-8c58-4dac-9e54-173efd4b2c30 (Définir des adresses IP statiques pour les machines virtuelles SAP)
[sap-ha-guide-8.7]:virtual-machines-windows-sap-high-availability-guide.md#7a8f3e9b-0624-4051-9e41-b73fff816a9e (Créer une adresse IP statique pour l’équilibrage de charge interne)
[sap-ha-guide-8.8]:virtual-machines-windows-sap-high-availability-guide.md#f19bd997-154d-4583-a46e-7f5a69d0153c (Règles d’équilibrage de charge ASCS/SCS par défaut pour l’équilibrage de charge interne Azure)
[sap-ha-guide-8.9]:virtual-machines-windows-sap-high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716 (Modifier les règles d’équilibrage de charge ASCS/SCS par défaut pour l’équilibrage de charge interne Azure)
[sap-ha-guide-8.10]:virtual-machines-windows-sap-high-availability-guide.md#e69e9a34-4601-47a3-a41c-d2e11c626c0c (Ajouter des machines virtuelles Windows au domaine)
[sap-ha-guide-8.11]:virtual-machines-windows-sap-high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158 (Ajouter des entrées de Registre aux deux nœuds de cluster de l’instance SAP ASCS/SCS)
[sap-ha-guide-8.12]:virtual-machines-windows-sap-high-availability-guide.md#0d67f090-7928-43e0-8772-5ccbf8f59aab (Configurer un cluster de clustering de basculement Windows Server pour une instance SAP ASCS/SCS)
[sap-ha-guide-8.12.1]:virtual-machines-windows-sap-high-availability-guide.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79 (Collecter les nœuds de cluster dans une configuration de cluster)
[sap-ha-guide-8.12.2]:virtual-machines-windows-sap-high-availability-guide.md#e49a4529-50c9-4dcf-bde7-15a0c21d21ca (Configurer un témoin de partage de fichiers de cluster)
[sap-ha-guide-8.12.2.1]:virtual-machines-windows-sap-high-availability-guide.md#06260b30-d697-4c4d-b1c9-d22c0bd64855 (Créer un partage de fichiers)
[sap-ha-guide-8.12.2.2]:virtual-machines-windows-sap-high-availability-guide.md#4c08c387-78a0-46b1-9d27-b497b08cac3d (Définir le quorum de témoin de partage de fichiers dans le Gestionnaire du cluster de basculement)
[sap-ha-guide-8.12.3]:virtual-machines-windows-sap-high-availability-guide.md#5c8e5482-841e-45e1-a89d-a05c0907c868 (Installer SIOS DataKeeper Cluster Edition pour le disque de partage en cluster SAP ASCS/SCS)
[sap-ha-guide-8.12.3.1]:virtual-machines-windows-sap-high-availability-guide.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3 (Ajouter .NET Framework 3.5)
[sap-ha-guide-8.12.3.2]:virtual-machines-windows-sap-high-availability-guide.md#dd41d5a2-8083-415b-9878-839652812102 (Installer SIOS DataKeeper)
[sap-ha-guide-8.12.3.3]:virtual-machines-windows-sap-high-availability-guide.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006 (Configurer SIOS DataKeeper)
[sap-ha-guide-9]:virtual-machines-windows-sap-high-availability-guide.md#a06f0b49-8a7a-42bf-8b0d-c12026c5746b (Installer le système SAP NetWeaver)
[sap-ha-guide-9.1]:virtual-machines-windows-sap-high-availability-guide.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170 (Installer SAP avec une instance ASCS/SCS à haute disponibilité)
[sap-ha-guide-9.1.1]:virtual-machines-windows-sap-high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097 (Créer un nom d’hôte virtuel pour l’instance SAP ASCS/SCS en cluster)
[sap-ha-guide-9.1.2]:virtual-machines-windows-sap-high-availability-guide.md#eb5af918-b42f-4803-bb50-eff41f84b0b0 (Installer le premier nœud de cluster SAP)
[sap-ha-guide-9.1.3]:virtual-machines-windows-sap-high-availability-guide.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556 (Modifier le profil SAP de l’instance ASCS/SCS)
[sap-ha-guide-9.1.4]:virtual-machines-windows-sap-high-availability-guide.md#10822f4f-32e7-4871-b63a-9b86c76ce761 (Ajouter un port de sondage)
[sap-ha-guide-9.2]:virtual-machines-windows-sap-high-availability-guide.md#85d78414-b21d-4097-92b6-34d8bcb724b7 (Installer l’instance de base de données)
[sap-ha-guide-9.3]:virtual-machines-windows-sap-high-availability-guide.md#8a276e16-f507-4071-b829-cdc0a4d36748 (Installer le deuxième nœud de cluster)
[sap-ha-guide-9.4]:virtual-machines-windows-sap-high-availability-guide.md#094bc895-31d4-4471-91cc-1513b64e406a (Modifier le type de démarrage de l’instance de service Windows SAP ERS)
[sap-ha-guide-9.5]:virtual-machines-windows-sap-high-availability-guide.md#2477e58f-c5a7-4a5d-9ae3-7b91022cafb5 (Installer le serveur d’applications principal SAP)
[sap-ha-guide-9.6]:virtual-machines-windows-sap-high-availability-guide.md#0ba4a6c1-cc37-4bcf-a8dc-025de4263772 (Installer le serveur d’applications supplémentaire SAP)
[sap-ha-guide-10]:virtual-machines-windows-sap-high-availability-guide.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9 (Tester le basculement et la réplication SIOS de l’instance SAP ASCS/SCS)
[sap-ha-guide-10.1]:virtual-machines-windows-sap-high-availability-guide.md#65fdef0f-9f94-41f9-b314-ea45bbfea445 (L’instance SAP ASCS/SCS s’exécute sur le nœud de cluster A)
[sap-ha-guide-10.2]:virtual-machines-windows-sap-high-availability-guide.md#5e959fa9-8fcd-49e5-a12c-37f6ba07b916 (Basculement du nœud A au nœud B)
[sap-ha-guide-10.3]:virtual-machines-windows-sap-high-availability-guide.md#755a6b93-0099-4533-9f6d-5c9a613878b5 (L’instance SAP ASCS/SCS s’exécute sur le nœud de cluster B))


[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
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


[powershell-install-configure]:../powershell-install-configure.md
[resource-group-authoring-templates]:../resource-group-authoring-templates.md
[resource-group-overview]:../resource-group-overview.md
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
[virtual-machines-azure-resource-manager-architecture]:../resource-manager-deployment-model.md
[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../resource-manager-deployment-model.md#benefits-of-using-resource-manager-and-resource-groups
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
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]:virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]:virtual-machines-windows-portal-sql-alwayson-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:virtual-machines-windows-hero-tutorial.md
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
[xplat-cli-azure-resource-manager]:../xplat-cli-azure-resource-manager.md


Machines virtuelles Azure est la solution idéale pour les organisations qui ont besoin de ressources de calcul, de stockage et réseau rapidement, sans longs cycles d’acquisition. Vous pouvez utiliser le service Machines virtuelles Azure pour déployer des applications classiques, comme les applications basées sur SAP NetWeaver ABAP, Java et ABAP+Java. Élargissez la fiabilité et la disponibilité sans ressources locales supplémentaires. Comme le service Machines virtuelles Azure prend en charge la connectivité intersite, votre organisation peut intégrer les machines virtuelles Azure dans ses domaines locaux, ses clouds privés et son paysage SAP.

Cet article décrit les étapes à suivre pour déployer des systèmes SAP à haute disponibilité dans Azure à l’aide du nouveau modèle de déploiement Azure Resource Manager. Nous vous guidons à travers les tâches principales suivantes :

* Trouver les guides d’installation et notes SAP adéquats, répertoriés dans la section [essources][sap-ha-guide-2]. Cet article vient compléter la documentation d’installation de SAP et les notes SAP, principales ressources qui peuvent vous aider à installer et à déployer des logiciels SAP sur des plateformes spécifiques.
* Découvrir les différences entre le modèle de déploiement Azure Classic et le modèle de déploiement Azure Resource Manager.
* Découvrir les modes de quorum du clustering de basculement Windows Server, pour pouvoir sélectionner le modèle adapté à votre déploiement Azure.
* Découvrir le stockage partagé en cluster de basculement Windows Server dans les services Azure.
* Découvrir comment protéger les composants à point de défaillance unique tels qu’ABAP SAP Central Services (ASCS)/SAP Central Services (SCS) et les systèmes de gestion de bases de données (SGBD), ainsi que les composants redondants comme les serveurs d’applications SAP, dans Azure.
* Suivre un exemple pas à pas d’installation et de configuration d’un système SAP à haute disponibilité dans un cluster de clustering de basculement Windows Server en utilisant Azure en tant que plateforme, avec Azure Resource Manager.
* Découvrir les étapes supplémentaires requises pour utiliser le clustering de basculement Windows Server dans Azure, mais qui ne sont pas nécessaires dans le cadre d’un déploiement local.

Pour simplifier le déploiement et la configuration, dans cet article, nous utilisons les nouveaux modèles Resource Manager SAP à trois niveaux et haute disponibilité. Les modèles permettent d’automatiser le déploiement de toute l’infrastructure dont vous avez besoin pour un système SAP à haute disponibilité. L’infrastructure prend également en charge le dimensionnement de votre système SAP.

[!INCLUDE [windows-warning](../../includes/virtual-machines-linux-sap-warning.md)]

## <a name="a-name217c547955954cd8870d15ab00d4f84ca-prerequisites"></a><a name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a> Conditions préalables
Avant de commencer, assurez-vous que vous remplissez les conditions préalables décrites dans les sections suivantes. Prenez également soin de consulter toutes les ressources répertoriées dans la section [Ressources][sap-ha-guide-2].

Dans cet article, nous utilisons des modèles Azure Resource Manager pour [SAP NetWeaver à trois niveaux](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image/). Pour une vue d’ensemble utile des modèles Azure Resource Manager SAP, consultez [cet article](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/).

## <a name="a-name42b8f6007ba34606b8a553c4f026da08a-resources"></a><a name="42b8f600-7ba3-4606-b8a5-53c4f026da08"></a> Ressources
Ces guides couvrent également les déploiements SAP dans Azure :

* [SAP NetWeaver sur machines virtuelles Windows – Guide de planification et d’implémentation][planning-guide]
* [SAP NetWeaver sur machines virtuelles Windows – Guide de déploiement][deployment-guide]
* [SAP NetWeaver sur machines virtuelles Windows – Guide de déploiement SGBD][dbms-guide]
* [SAP NetWeaver sur machines virtuelles Windows – Guide de haute disponibilité (ce guide)][sap-ha-guide]

> [!NOTE]
> Autant que possible, nous vous fournissons un lien vers le guide d’installation de SAP concerné (consultez [Guides d’installation de SAP][sap-installation-guides]). Pour plus d’informations sur les conditions préalables à l’installation et la procédure d’installation, il est recommandé de lire attentivement les guides d’installation de SAP NetWeaver. Cet article traite uniquement des tâches spécifiques pour les systèmes basés sur SAP NetWeaver que vous pouvez utiliser avec le service Machines virtuelles Azure.
> 
> 

Les notes SAP suivantes sont associées à la question de SAP dans Azure :

| Numéro de la note | Intitulé |
| --- | --- |
| [1928533] |Applications SAP sur Azure : dimensionnement et produits pris en charge |
| [2015553] |SAP sur Microsoft Azure : configuration requise |
| [1999351] |Surveillance Azure améliorée pour SAP |
| [2178632] |Métriques de surveillance clés pour SAP sur Microsoft Azure |
| [1999351] |Virtualisation sur Windows : surveillance améliorée |

Prenez connaissance des [limites des abonnements Azure][azure-subscription-service-limits-subscription], y compris les limites générales par défaut et les limites maximales.

## <a name="a-name42156640c601cf45a9b2254baa678b24f1ahighavailability-sap-with-azure-resource-manager-vs-the-classic-deployment-model"></a><a name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a>Système SAP à haute disponibilité avec modèle de déploiement Azure Resource Manager ou Classic
Les modèles de déploiement Azure Resource Manager et Classic présentent deux différences principales :

* Groupes de ressources
* Exigences relatives au clustering

### <a name="a-namef76af27319934d83b12d65deeae23686a-resource-groups"></a><a name="f76af273-1993-4d83-b12d-65deeae23686"></a> Groupes de ressources
Dans Azure Resource Manager, vous pouvez utiliser des groupes de ressources pour gérer l’ensemble des ressources d’application de votre abonnement Azure. Dans une approche intégrée, toutes les ressources d’un groupe de ressources présentent le même cycle de vie. Par exemple, toutes les ressources sont créées en même temps et supprimées au même moment. Vous trouverez plus d’informations sur les groupes de ressources [ici](../resource-group-overview.md#resource-groups).

### <a name="a-name3e85fbe084b1489287afd9b65ff91860a-clustering-with-azure-resource-manager-vs-the-classic-deployment-model"></a><a name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a>Clustering avec modèle de déploiement Azure Resource Manager ou Classic
Dans le modèle Azure Resource Manager, vous n’avez pas besoin d’un service cloud pour utiliser l’équilibrage de charge interne Azure à des fins de haute disponibilité.

Pour utiliser l’ancien modèle Azure Classic, vous devez suivre la procédure décrite dans le document [SAP NetWeaver in Azure: Clustering SAP ASCS/SCS instances by using Windows Server Failover Clustering in Azure with SIOS DataKeeper](http://go.microsoft.com/fwlink/?LinkId=613056) (SAP NetWeaver sur Azure - Clustering d’instances SAP ASCS/SCS à l’aide du clustering de basculement Windows Server sur Azure avec SIOS DataKeeper).

> [!NOTE]
> Nous vous recommandons vivement d’utiliser le modèle de déploiement Azure Resource Manager pour vos installations SAP. Celui-ci offre de nombreux avantages par rapport au modèle de déploiement Classic. Vous trouverez plus d’informations sur les modèles de déploiement Azure [ici][virtual-machines-azure-resource-manager-architecture-benefits-arm].   
> 
> 

## <a name="a-name8ecf3ba067c044959c14feec1a2255b7a-windows-server-failover-clustering"></a><a name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a> Clustering de basculement Windows Server
Le clustering de basculement Windows Server constitue la base d’une installation de SGBD et de SAP ASCS/SCS à haute disponibilité dans Windows.

Un cluster de basculement est un groupe de 1 + n serveurs indépendants (nœuds) qui fonctionnent ensemble pour accroître la disponibilité des applications et des services. En cas d’échec d’un nœud, le clustering de basculement Windows Server calcule le nombre d’échecs qui peuvent se produire tout en maintenant l’intégrité du cluster afin d’être en mesure de fournir les applications et les services définis. Différents modes de quorum sont disponibles pour la réalisation de cet objectif.

### <a name="a-name1a3c5408b16846d699f54219ad1b1ff2a-quorum-modes"></a><a name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a> Modes de quorum
Lorsque vous utilisez le clustering de basculement Windows Server, vous pouvez choisir parmi quatre modes de quorum :

* **Nœud majoritaire** : chaque nœud du cluster peut voter. Le cluster fonctionne uniquement avec une majorité de voix, c’est-à-dire plus de la moitié. Nous recommandons cette option pour les clusters qui présentent un nombre impair de nœuds. Par exemple, si trois nœuds d’un cluster à sept nœuds échouent, celui-ci obtient toujours la majorité et continue à s’exécuter.  
* **Nœud et disque majoritaires** : chaque nœud et un disque désigné dans l’espace de stockage en cluster (disque témoin) peuvent voter lorsqu’ils sont disponibles et en communication. Le cluster fonctionne uniquement avec une majorité de voix, c’est-à-dire plus de la moitié. Ce mode est pertinent dans un environnement de cluster comprenant un nombre pair de nœuds. Si la moitié des nœuds et le disque sont en ligne, le cluster conserve son état d’intégrité.
* **Nœud et partage de fichiers majoritaires** : chaque nœud et un partage de fichiers désigné (témoin de partage de fichiers) créé par l’administrateur peuvent voter, qu’ils soient ou non disponibles et en communication. Le cluster fonctionne uniquement avec une majorité de voix, c’est-à-dire plus de la moitié. Ce mode est pertinent dans un environnement de cluster comprenant un nombre pair de nœuds. Il est similaire au mode Nœud et disque majoritaires, mais il utilise un témoin de partage de fichiers au lieu d’un disque témoin. Ce mode est facile à implémenter, mais si le partage de fichiers lui-même n’est pas hautement disponible, il risque de devenir un point de défaillance unique.
* **Pas de majorité : disque uniquement** : un quorum est atteint pour le cluster si un nœud est disponible et en communication avec un disque spécifique dans l’espace de stockage en cluster. Seuls les nœuds qui sont également en communication avec ce disque peuvent rejoindre le cluster. Nous vous déconseillons d’utiliser ce mode.
   
  
  ## <a name="a-namefdfee8756e66483aa34314bbaee33275a-windows-server-failover-clustering-onpremises"></a><a name="fdfee875-6e66-483a-a343-14bbaee33275"></a> Clustering de basculement Windows Server local
  L’exemple de la figure 1 illustre un cluster de deux nœuds. Si la connexion réseau entre les nœuds échoue et les deux nœuds restent opérationnels, un disque ou un partage de fichiers quorum détermine quel nœud continuera de fournir les applications et services du cluster. Le nœud qui a accès au disque ou au partage de fichiers quorum est celui qui garantit que les services restent disponibles.

Comme un cluster à deux nœuds est utilisé dans cet exemple, nous faisons appel au mode de quorum Nœud et partage de fichiers majoritaires. Le mode Nœud et disque majoritaires est également une option valide. Dans un environnement de production, nous vous recommandons d’utiliser un disque quorum. Vous pouvez utiliser une technologie de système de stockage et de réseau pour le rendre hautement disponible.

![Figure 1 : Exemple de configuration de clustering de basculement Windows Server pour SAP ASCS/SCS dans Azure][sap-ha-guide-figure-1000]

***Figure 1 :** Exemple de configuration de clustering de basculement Windows Server pour SAP ASCS/SCS dans Azure*

### <a name="a-namebe21cf3efb01402b995554fbecf66592a-shared-storage"></a><a name="be21cf3e-fb01-402b-9955-54fbecf66592"></a> Stockage partagé
La figure 1 illustre également un cluster de stockage partagé à deux nœuds. Dans un cluster de stockage partagé local, tous les nœuds du cluster détectent le stockage partagé. Un mécanisme de verrouillage protège les données contre toute altération. L’échec d’un nœud est détecté par l’ensemble des autres nœuds. Si un nœud échoue, le nœud restant s’approprie les ressources de stockage et garantit la disponibilité des services.

> [!NOTE]
> Avec certaines applications de SGBD, telles que SQL Server, vous n’avez pas besoin de disques partagés pour atteindre une haute disponibilité. SQL Server Always On assure la réplication des données et des fichiers de journaux du SGBD à partir du disque local d’un nœud du cluster vers le disque local d’un autre nœud du cluster. Dans ce cas, la configuration du cluster Windows ne nécessite pas de disque partagé.
> 
> 

### <a name="a-nameff7a9a062bc54b20860a46cdb44669cda-networking-and-name-resolution"></a><a name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a> Mise en réseau et résolution de noms
Les ordinateurs clients atteignent le cluster via une adresse IP virtuelle et un nom d’hôte virtuel fournis par le serveur DNS. Les nœuds locaux et le serveur DNS peuvent gérer plusieurs adresses IP.

Dans une installation type, vous utilisez deux ou plusieurs connexions réseau :

* Une connexion dédiée au stockage
* Une connexion de réseau interne au cluster pour la pulsation
* Un réseau public utilisé par les clients pour se connecter au cluster

## <a name="a-name2ddba413a7f54e4e9a5187908879c10aa-windows-server-failover-clustering-in-azure"></a><a name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a> Clustering de basculement Windows Server dans Azure
Par rapport aux déploiements complets ou de cloud privé, le service Machines virtuelles Azure requiert des étapes supplémentaires pour configurer le clustering de basculement Windows Server. Lorsque vous créez un disque de cluster partagé, vous devez définir plusieurs adresses IP et noms d’hôtes virtuels pour l’instance SAP ASCS/SCS.

Cet article présente les concepts clés et les étapes supplémentaires requises lors de la création d’un cluster de services centraux SAP à haute disponibilité dans Azure. Nous vous expliquons comment configurer l’outil tiers SIOS DataKeeper et l’équilibrage de charge interne Azure. Vous pouvez utiliser ces outils pour créer un cluster de basculement Windows avec un témoin de partage de fichiers dans Azure.

![Figure 2 : Configuration du clustering de basculement Windows Server dans Azure sans disque partagé][sap-ha-guide-figure-1001]

***Figure 2 :** Configuration du clustering de basculement Windows Server dans Azure sans disque partagé*

### <a name="a-name1a464091922b48d79d087cecf757f341a-shared-disk-in-azure-with-sios-datakeeper"></a><a name="1a464091-922b-48d7-9d08-7cecf757f341"></a> Disque partagé dans Azure avec SIOS DataKeeper
Vous avez besoin d’un stockage partagé en cluster pour bénéficier d’une instance SAP ASCS/SCS à haute disponibilité. Au mois de septembre 2016, Azure n’offre pas de stockage partagé pour créer un cluster de stockage partagé. Vous pouvez utiliser le logiciel tiers SIOS DataKeeper Cluster Edition pour créer un stockage en miroir qui simule un stockage partagé en cluster. La solution SIOS assure la réplication synchrone des données en temps réel. Voici comment créer une ressource de disque partagé pour un cluster :

1. Attachez un disque dur virtuel (VHD) Azure supplémentaire à chacune des machines virtuelles se trouvant dans une configuration de cluster Windows.
2. Exécutez SIOS DataKeeper Cluster Edition sur les deux nœuds de machine virtuelle.
3. Configurez SIOS DataKeeper Cluster Edition de sorte qu’il mette en miroir le contenu du volume du VHD supplémentaire de la machine virtuelle source vers le volume du VHD supplémentaire de la machine virtuelle cible. SIOS DataKeeper extrait les volumes locaux source et cible, puis les présente au clustering de basculement Windows Server comme un seul disque partagé.

Vous trouverez plus d’informations sur SIOS DataKeeper [ici](http://us.sios.com/products/datakeeper-cluster/).

 ![Figure 3 : Configuration du clustering de basculement Windows Server dans Azure à l’aide de SIOS DataKeeper][sap-ha-guide-figure-1002]

***Figure 3 :** Configuration du clustering de basculement Windows Server dans Azure à l’aide de SIOS DataKeeper*

> [!NOTE]
> Avec certains SGBD, tels que SQL Server, vous n’avez pas besoin de disques partagés pour atteindre une haute disponibilité. SQL Server Always On assure la réplication des données et des fichiers de journaux du SGBD à partir du disque local d’un nœud du cluster vers le disque local d’un autre nœud du cluster. Dans ce cas, la configuration du cluster Windows ne nécessite pas de disque partagé.
> 
> 

### <a name="a-name44641e18a94e431f95ff303ab65e0bcba-name-resolution-in-azure"></a><a name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a> Résolution de noms dans Azure
 La plateforme cloud Azure ne permet pas de configurer des adresses IP virtuelles telles que des adresses IP flottantes. Par conséquent, vous avez besoin d’une autre solution pour configurer une adresse IP virtuelle afin d’atteindre la ressource de cluster dans le cloud.
Azure offre un équilibrage de charge interne dans le cadre du service Azure Load Balancer. Avec l’équilibrage de charge interne, les clients atteignent le cluster via son adresse IP virtuelle.
Vous devez déployer l’équilibrage de charge interne dans le groupe de ressources qui contient les nœuds de cluster. Ensuite, configurez toutes les règles de réacheminement de port nécessaires avec les ports de sondage de l’équilibrage de charge interne.
Les clients peuvent se connecter avec le nom d’hôte virtuel. Le serveur DNS résout l’adresse IP du cluster et l’équilibrage de charge interne gère le réacheminement de port vers le nœud actif du cluster.

## <a name="a-name2e3fec50241e441b87080b1864f66dfaa-highavailability-sap-netweaver-in-azure-infrastructureasaservice-iaas"></a><a name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a> Haute disponibilité pour SAP NetWeaver dans Azure IaaS (Infrastructure as a Service)
Pour atteindre une haute disponibilité des applications SAP, par exemple pour les composants logiciels SAP, vous devez protéger les composants suivants. Ce sujet est traité plus en détail dans l’article [SAP NetWeaver sur machines virtuelles Windows – Guide de planification et d’implémentation][planning-guide-11].

* Serveurs d’application SharePoint
* Instance SAP ASCS/SCS
* Serveur SGBD

### <a name="a-name93faa747907e440ab00a1ae0a89b1c0ea-highavailability-sap-application-servers"></a><a name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a>Serveurs d’applications SAP à haute disponibilité
En règle générale, vous n’avez pas besoin d’une solution à haute disponibilité pour les serveurs d’applications et les instances de dialogue SAP. La haute disponibilité s’obtient par redondance, et vous configurez plusieurs instances de dialogue sur différentes instances de machines virtuelles Azure. Vous devez avoir au moins deux instances d’applications SAP installées dans deux instances de machines virtuelles Azure.

![Figure 4 : Serveurs d’applications SAP à haute disponibilité][sap-ha-guide-figure-2000]

***Figure 4 :** Serveurs d’applications SAP à haute disponibilité*

Vous devez placer toutes les machines virtuelles qui hébergent des serveurs d’applications SAP dans le même groupe à haute disponibilité Azure. Un groupe à haute disponibilité Azure garantit que :

* Toutes les machines virtuelles font partie du même domaine de mise à niveau. Un domaine de mise à niveau permet par exemple d’éviter que les machines virtuelles soient mises à jour en même temps lors d’une interruption de maintenance planifiée.
* Toutes les machines virtuelles font partie du même domaine d’erreur. Un domaine d’erreur permet par exemple de s’assurer que les machines virtuelles soient déployées de sorte qu’aucun point de défaillance unique n’affecte la disponibilité de l’ensemble des machines virtuelles.

Découvrez comment [ la disponibilité des machines virtuelles][virtual-machines-manage-availability].

Comme le compte de stockage Azure constitue un point de défaillance unique potentiel, il est important de disposer d’au moins deux comptes de stockage Azure sur lesquels au moins deux machines virtuelles sont distribuées. Dans une configuration idéale, chaque machine virtuelle exécutant une instance de dialogue SAP est déployée sur un compte de stockage différent.

### <a name="a-namef559c285ee684eecadd1f60fe7b978dba-highavailability-sap-ascsscs-instance"></a><a name="f559c285-ee68-4eec-add1-f60fe7b978db"></a> Instance SAP ASCS/SCS à haute disponibilité
![Figure 5 : Instance SAP ASCS/SCS à haute disponibilité][sap-ha-guide-figure-2001]

***Figure 5 :** Instance SAP ASCS/SCS à haute disponibilité*

#### <a name="a-nameb5b1fd0b1db44d499162de07a0132a51a-highavailability-sap-ascsscs-instance-with-windows-server-failover-clustering-in-azure"></a><a name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a> Instance SAP ASCS/SCS à haute disponibilité avec le clustering de basculement Windows Server dans Azure
Par rapport aux déploiements complets ou de cloud privé, le service Machines virtuelles Azure requiert des étapes supplémentaires pour configurer le clustering de basculement Windows Server. Afin de créer un cluster de basculement Windows, vous avez besoin d’un disque de cluster partagé, de plusieurs adresses IP et noms d’hôtes virtuels, et d’un équilibrage de charge interne Azure pour le clustering d’une instance SAP ASCS/SCS.

Nous approfondirons ce point plus loin dans cet article.

![Figure 6 : Configuration du clustering de basculement Windows Server pour une instance SAP ASCS/SCS dans Azure à l’aide de SIOS DataKeeper][sap-ha-guide-figure-1002]

***Figure 6 :** Configuration du clustering de basculement Windows Server pour une instance SAP ASCS/SCS dans Azure à l’aide de SIOS DataKeeper*

### <a name="a-nameddd878a09c2f4b8e896826ce60be1027a-highavailability-dbms-instance"></a><a name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a> Instance SGBD à haute disponibilité
Le SGBD constitue également un point de défaillance unique d’un système SAP. Vous devez le protéger à l’aide d’une solution à haute disponibilité. La figure 7 illustre une solution à haute disponibilité SQL Server Always On dans Azure utilisant le clustering de basculement Windows Server et l’équilibrage de charge interne Azure. SQL Server Always On réplique les fichiers journaux et les données du SGBD à l’aide de sa propre réplication de SGBD. Dans ce cas, vous n’avez pas besoin de disques partagés en cluster, ce qui simplifie l’ensemble de la configuration.

![Figure 7 : Exemple de SGBD SAP à haute disponibilité SQL Server Always On][sap-ha-guide-figure-2003]

***Figure 7 :** Exemple de SGBD SAP à haute disponibilité SQL Server Always On*

Pour plus d’informations sur le clustering de SQL Server dans Azure à l’aide du modèle de déploiement Azure Resource Manager, consultez les articles suivants :

* [Configuration manuelle d’un groupe de disponibilité Always On dans une machine virtuelle Azure à l’aide de Resource Manager][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]
* [Configurer un équilibrage de charge interne pour un groupe de disponibilité Always On dans Azure][virtual-machines-windows-portal-sql-alwayson-int-listener]

### <a name="a-name045252ed02774fc88f46c5a29694a816a-endtoend-highavailability-deployment-scenarios"></a><a name="045252ed-0277-4fc8-8f46-c5a29694a816"></a> Scénarios de déploiement à haute disponibilité de bout en bout
La figure 8 illustre une architecture SAP NetWeaver à haute disponibilité dans Azure. Dans ce scénario, nous utilisons un cluster dédié pour l’instance SAP ASCS/SCS et un autre pour le SGBD.

![Figure 8 : Modèle d’architecture SAP à haute disponibilité nº 1, avec un cluster dédié pour l’instance ASCS/SCS et un cluster dédié pour l’instance SGBD][sap-ha-guide-figure-2004]

***Figure 8 :** Modèle d’architecture SAP à haute disponibilité nº 1 : clusters dédiés pour les instances ASCS/SCS et SGBD*

## <a name="a-name78092dbe165b454c92f54972bdbef9bfa-prepare-the-infrastructure"></a><a name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a>réparer l’infrastructure
Les modèles Azure Resource Manager pour SAP simplifient le déploiement des ressources requises.

Les modèles à trois niveaux prennent également en charge les scénarios de haute disponibilité, comme le modèle d’architecture nº 1, qui présente deux clusters. Chaque cluster constitue un point de défaillance pour l’instance SAP ASCS/SCS et l’instance SGBD.

Vous trouverez Les modèles Azure Resource Manager du scénario nº 1 ici :

* [Image Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Image personnalisée](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)

Lorsque vous sélectionnez l’image Marketplace SAP à trois niveaux, l’écran suivant est affiché dans le portail Azure :

![Figure 9 : Spécifier les paramètres de haute disponibilité Azure Resource Manager SAP][sap-ha-guide-figure-3000]

***Figure 9 :** Spécifier les paramètres de haute disponibilité Azure Resource Manager SAP*

Dans **SYSTEMAVAILABILITY** (disponibilité du système), sélectionnez **HA** (haute disponibilité).

Les modèles créent :

* **Des machines virtuelles** :
  * Des machines virtuelles de serveur d’applications SAP : <*SIDSystèmeSAP*>-di-<*Numéro*>
  * Des machines virtuelles de cluster ASCS/SCS : <*SIDSystèmeSAP*>-ascs-<*Numéro*>
  * Un cluster SGBD : <*SIDSystèmeSAP*>-db-<*Numéro*>
* **Des cartes réseau pour toutes les machines virtuelles, avec une adresse IP associée** :
  * <*SIDSystèmeSAP*>-nic-di-<*Numéro*>
  * <*SIDSystèmeSAP*>-nic-ascs-<*Numéro*>
  * <*SIDSystèmeSAP*>-nic-db-<*Numéro*>
* **Des comptes de stockage Azure**
* **Des groupes de disponibilité** pour :
  * Les machines virtuelles de serveur d’applications SAP : <*SIDSystèmeSAP*>-avset-di
  * Les machines virtuelles de cluster SAP ASCS/SCS : <*SIDSystèmeSAP*>-avset-ascs
  * Les machines virtuelles de cluster SGBD : <*SIDSystèmeSAP*>-avset-db
* **Un équilibrage de charge interne Azure** :
  * Avec tous les ports pour l’instance ASCS/SCS et l’adresse IP <*SIDSystèmeSAP*>-lb-ascs
  * Avec tous les ports pour l’instance SGBD et l’adresse IP <*SIDSystèmeSAP*>-lb-db
* **Un groupe de sécurité réseau** : <*SIDSystèmeSAP*>-nsg-ascs-0  
  * Avec un port RDP (Remote Desktop Protocol) ouvert vers la machine virtuelle <*SAPSystemSID*>-ascs-0

> [!NOTE]
> Toutes les adresses IP des cartes réseau et des équilibrages de charge internes Azure sont **dynamiques** par défaut. Définissez-les en tant qu’adresses IP **statiques**. La marche à suivre est décrite plus loin dans cet article.
> 
> 

### <a name="a-namec87a8d3fb1dc4d2fb23cda4b72977489a-deploy-virtual-machines-with-corporate-network-connectivity-crosspremises-to-use-in-production"></a><a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> Déployer des machines virtuelles avec connectivité réseau d’entreprise (intersite) pour une utilisation en production
Pour les systèmes SAP de production, déployez des machines virtuelles Azure avec [connectivité de réseau d’entreprise (intersite)][planning-guide-2.2] à l’aide d’un VPN de site à site Azure ou d’Azure ExpressRoute.

> [!NOTE]
> Vous pouvez utiliser votre instance de réseau virtuel Azure. Le réseau virtuel et le sous-réseau ont déjà été créés et préparés.
> 
> 

Dans **NEWOREXISTINGSUBNET** (sous-réseau nouveau ou existant), sélectionnez **existing** (existant).

Dans **SUBNETID** (ID de sous-réseau), vous devez ajouter la chaîne complète de l’ID du sous-réseau Azure préparé sur lequel vous envisagez de déployer vos machines virtuelles Azure.

Pour obtenir la liste de tous les sous-réseaux du réseau Azure, exécutez la commande PowerShell suivante :

```powershell
(Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
```

Le champ **ID** indique l’ID de sous-réseau (**SUBNETID**).

Vous pouvez récupérer la liste de toutes les valeurs **SUBNETID** à l’aide de la commande PowerShell suivante :

```PowerShell
(Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
```

Le **SUBNETID** se présente ainsi :

```
/subscriptions/<SubscriptionId>/resourceGroups/<VPNName>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<SubnetName>
```

### <a name="a-name7fe9af0e3cce495ba5ecdcb4d8e0a310a-cloudonly-deployment-of-sap-instances-for-test-and-demo"></a><a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> Déploiement cloud uniquement d’instances SAP à des fins de test et de démonstration
Vous pouvez également déployer votre système SAP à haute disponibilité dans un modèle de déploiement cloud uniquement.

Ce type de déploiement est surtout utile pour les démonstrations ou les tests. Il ne convient pas à une utilisation en production.

Dans **NEWOREXISTINGSUBNET** (sous-réseau nouveau ou existant), sélectionnez **new** (nouveau). Laissez le champ **SUBNETID** vide.

Le modèle Azure Resource Manager SAP crée automatiquement le réseau virtuel et le sous-réseau Azure.

> [!NOTE]
> Vous devez également déployer au moins une machine virtuelle dédiée pour Active Directory et le service DNS dans la même instance de réseau virtuel Azure. Le modèle ne crée pas ces machines virtuelles.
> 
> 

### <a name="a-name47d5300aa83041d483dd1a0d1ffdbe6aa-azure-virtual-network"></a><a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Réseau virtuel Azure
Dans notre exemple, l’espace d’adressage du réseau virtuel Azure est 10.0.0.0/16. Il existe un sous-réseau appelé **Subnet** (Sous-réseau), avec la plage d’adresses 10.0.0.0/24. L’ensemble des machines virtuelles et des équilibrages de charge internes sont déployés au sein de ce réseau virtuel.

> [!NOTE]
> N’apportez aucune modification aux paramètres réseau dans le système d’exploitation invité (adresses IP, serveurs DNS, sous-réseau, etc.). Configurez tous vos paramètres réseau dans Azure. Ils sont propagés par le service DHCP.
> 
> 

### <a name="a-nameb22d7b3b434340ffa319097e13f62f9ea-dns-ip-addresses"></a><a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a>Adresses IP DNS
Assurez-vous que l’option **Serveurs DNS** de votre réseau virtuel est définie sur **DNS personnalisé**.
Ensuite, sélectionnez les paramètres en fonction de votre type de réseau :

* [Connectivité de réseau d’entreprise (intersite)][planning-guide-2.2] : ajoutez l’adresse IP des serveurs DNS locaux.  
  
    Vous pouvez étendre les serveurs DNS locaux aux machines virtuelles exécutées dans Azure. Dans ce scénario, vous pouvez ajouter l’adresse IP des machines virtuelles Azure sur lesquelles vous exécutez le service DNS.
* [Déploiement cloud uniquement][planning-guide-2.1] : déployez une machine virtuelle supplémentaire dans l’instance de réseau virtuel jouant le rôle de serveur DNS. Ajoutez l’adresse IP des machines virtuelles Azure que vous avez configurées pour exécuter le service DNS.

![Figure 10 : Configurer les serveurs DNS pour le réseau virtuel Azure][sap-ha-guide-figure-3001]

***Figure 10 :** Configurer les serveurs DNS pour le réseau virtuel Azure*

> [!NOTE]
> Si vous modifiez l’adresse IP des serveurs DNS, vous devez redémarrer les machines virtuelles Azure afin d’appliquer la modification et de propager les nouveaux serveurs DNS.
> 
> 

Dans notre exemple, le service DNS est installé et configuré sur les machines virtuelles Windows suivantes :

| Rôle de la machine virtuelle | Nom d’hôte de la machine virtuelle | Nom de la carte réseau | Adresse IP statique |
| --- | --- | --- | --- |
| Premier serveur DNS |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| Deuxième serveur DNS |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

### <a name="a-name9fbd43c05850496597262a921d85d73fa-host-names-and-static-ip-addresses-for-the-sap-ascsscs-clustered-instance-and-dbms-clustered-instance"></a><a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> Noms d’hôtes et adresses IP statiques de l’instance en cluster SAP ASCS/SCS et l’instance en cluster SGBD
Pour un déploiement local, vous avez besoin des noms d’hôtes et adresses IP réservés suivants :

| Rôle du nom d’hôte virtuel | Nom d’hôte virtuel | Adresse IP statique virtuelle |
| --- | --- | --- |
| Nom d’hôte virtuel du premier cluster SAP ASCS/SCS (pour la gestion du cluster) |pr1-ascs-vir |10.0.0.42 |
| Nom d’hôte virtuel de l’instance SAP ASCS/SCS |pr1-ascs-sap |10.0.0.43 |
| Nom d’hôte virtuel du deuxième cluster SAP SGBD (gestion du cluster) |pr1-dbms-vir |10.0.0.32 |

Lorsque vous créez le cluster, créez les noms d’hôtes virtuels **pr1-ASC-vir** et **pr1-SGBD-vir**, ainsi que les adresses IP associées qui gèrent le cluster lui-même. La procédure est décrite dans la section [Collecter les nœuds de cluster dans la configuration du cluster][sap-ha-guide-8.12.1].

Vous pouvez créer manuellement les deux autres noms d’hôtes virtuels, **pr1 ASC sap** et **pr1 SGBD sap**, ainsi que les adresses IP associées, sur le serveur DNS. L’instance SAP ASCS/SCS en cluster et l’instance SGBD en cluster utilisent ces ressources. Vous trouverez plus d’informations dans la section [Créer un nom d’hôte virtuel pour l’instance SAP ASCS/SCS en cluster][sap-ha-guide-9.1.1].

### <a name="a-name84c019fe8c584dac9e54173efd4b2c30a-set-static-ip-addresses-for-the-sap-virtual-machines"></a><a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a>Définir des adresses IP statiques pour les machines virtuelles SAP
Après avoir déployé les machines virtuelles à utiliser dans votre cluster, vous devez définir des adresses IP statiques pour l’ensemble des machines virtuelles. Effectuez cette opération dans la configuration du réseau virtuel Azure et non dans le système d’exploitation invité.

Pour définir une adresse IP statique, vous pouvez utiliser le portail Azure. Dans le portail Azure, accédez à **Groupe de ressources** > **Carte réseau** > **Paramètres** > **Adresse IP**.

Sous **Affectation**, sélectionnez **Statique**. Dans le champ **Adresse IP**entrez l’adresse IP à utiliser.

> [!NOTE]
> Si vous modifiez l’adresse IP de la carte réseau, vous devez redémarrer les machines virtuelles Azure pour que la modification prenne effet.  
> 
> 

![Figure 11 : Définir des adresses IP statiques pour la carte réseau de chaque machine virtuelle][sap-ha-guide-figure-3002]

***Figure 11 :** Définir des adresses IP statiques pour la carte réseau de chaque machine virtuelle*

Répétez cette étape pour toutes les interfaces réseau, c’est-à-dire pour toutes les machines virtuelles, y compris celles que vous souhaitez utiliser pour votre service Active Directory/DNS.

Dans notre exemple, nous avons les machines virtuelles et les adresses IP statiques suivantes :

| Rôle de la machine virtuelle | Nom d’hôte de la machine virtuelle | Nom de la carte réseau | Adresse IP statique |
| --- | --- | --- | --- |
| Premier serveur d’applications SAP |pr1-di-0 |pr1-nic-di-0 |10.0.0.50 |
| Deuxième serveur d’applications SAP |pr1-di-1 |pr1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| Dernier serveur d’applications SAP |pr1-di-5 |pr1-nic-di-5 |10.0.0.55 |
| Premier nœud de cluster pour l’instance ASCS/SCS |pr1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| Deuxième nœud de cluster pour l’instance ASCS/SCS |pr1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| Premier nœud de cluster pour l’instance SGBD |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| Deuxième nœud de cluster pour l’instance SGBD |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

### <a name="a-name7a8f3e9b062440519e41b73fff816a9ea-set-a-static-ip-address-for-the-internal-load-balancer"></a><a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> Définir une adresse IP statique pour l’équilibrage de charge interne
Le modèle Azure Resource Manager SAP crée un équilibrage de charge interne Azure utilisé pour le cluster de l’instance SAP ASCS/SCS et le cluster SGBD.

Le déploiement initial définit l’adresse IP de l’équilibrage de charge interne sur **Dynamique**. Il est important de définir cette adresse IP sur **Statique**.

Dans notre exemple, nous avons deux équilibrages de charge internes Azure qui présentent les adresses IP statiques suivantes :

| Rôle de l’équilibrage de charge interne Azure | Nom de l’équilibrage de charge interne Azure | Adresse IP statique |
| --- | --- | --- |
| Équilibrage de charge interne de l’instance SAP ASCS/SCS |pr1-lb-ascs |10.0.0.43 |
| Équilibrage de charge interne du SGBD SAP |pr1-lb-dbms |10.0.0.33 |

> [!NOTE]
> L’adresse IP du nom d’hôte virtuel de l’instance SAP ASCS/SCS est identique à celle de l’équilibrage de charge interne SAP ASCS/SCS pr1-lb-ascs.
> L’adresse IP du nom virtuel du SGBD est identique à celle de l’équilibrage de charge interne du SGBD SAP pr1-lb-dbms.
> 
> 

Dans notre exemple, nous définissons l’adresse IP de l’équilibrage de charge interne **pr1-lb-ascs** sur l’adresse IP du nom d’hôte virtuel de l’instance SAP ASCS/SCS (dans notre exemple, **10.0.0.43**).

![Figure 12 : Définir une adresse IP statique pour l’équilibrage de charge interne de l’instance SAP ASCS/SCS][sap-ha-guide-figure-3003]

***Figure 12 :** Définir une adresse IP statique pour l’équilibrage de charge interne de l’instance SAP ASCS/SCS*

Définissez l’adresse IP de l’équilibrage de charge interne **pr1-lb-dbms** sur l’adresse IP du nom d’hôte virtuel de l’instance SGBD (dans notre exemple, **10.0.0.33**).

### <a name="a-namef19bd997154d4583a46e7f5a69d0153ca-default-ascsscs-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Règles d’équilibrage de charge ASCS/SCS par défaut pour l’équilibrage de charge interne Azure
Le modèle Azure Resource Manager SAP crée les ports dont vous avez besoin :

* Une instance ABAP ASCS, avec le numéro d’instance par défaut **00**
* Une instance Java SCS, avec le numéro d’instance par défaut **01**

Lorsque vous installez votre instance SAP ASCS/SCS, vous devez utiliser le numéro d’instance par défaut 00 pour votre instance ABAP ASCS et le numéro d’instance par défaut 01 pour votre instance Java SCS.

Ensuite, créez ces points de terminaison d’équilibrage de charge requis pour les ports SAP NetWeaver ABAP ASC :

| Service/nom de la règle d’équilibrage de charge | Numéros de ports par défaut | Ports concrets pour (instance ASCS avec numéro d’instance 00) (ERS avec 10) |
| --- | --- | --- |
| Serveur de file d’attente / *lbrule3200* |32<*NuméroInstance*> |3200 |
| Serveur de messages ABAP / *lbrule3600* |36<*NuméroInstance*> |3600 |
| Message ABAP interne / *lbrule3900* |39<*NuméroInstance*> |3900 |
| Serveur de messages HTTP / *Lbrule8100* |81<*NuméroInstance*> |8100 |
| Service de démarrage SAP ASCS HTTP / *Lbrule50013* |5<*NuméroInstance*>13 |50013 |
| Service de démarrage SAP ASCS HTTPS / *Lbrule50014* |5<*NuméroInstance*>14 |50014 |
| Réplication de la file d’attente / *Lbrule50016* |5<*NuméroInstance*>16 |50016 |
| Service de démarrage SAP ERS HTTP / *Lbrule51013* |5<*NuméroInstance*>13 |51013 |
| Service de démarrage SAP ERS HTTP / *Lbrule51014* |5<*NuméroInstance*>14 |51014 |
| WinRM *Lbrule5985* | |5985 |
| Partage de fichiers *Lbrule445* | |445 |

***Tableau 1 :** Numéro de port des instances SAP NetWeaver ABAP ASCS*

Ensuite, créez ces points de terminaison d’équilibrage de charge requis pour les ports SAP NetWeaver Java SCS :

| Service/nom de la règle d’équilibrage de charge | Numéros de ports par défaut | Ports concrets pour (instance SCS avec numéro d’instance 01) (ERS avec 11) |
| --- | --- | --- |
| Serveur de file d’attente / *lbrule3201* |32<*NuméroInstance*> |3201 |
| Serveur de passerelle / *lbrule3301* |33<*NuméroInstance*> |3301 |
| Serveur de messages Java / *lbrule3900* |39<*NuméroInstance*> |3901 |
| Serveur de messages HTTP / *Lbrule8101* |81<*NuméroInstance*> |8101 |
| Service de démarrage SAP SCS HTTP / *Lbrule50113* |5<*NuméroInstance*>13 |50113 |
| Service de démarrage SAP SCS HTTPS / *Lbrule50114* |5<*NuméroInstance*>14 |50114 |
| Réplication de la file d’attente / *Lbrule50116* |5<*NuméroInstance*>16 |50116 |
| Service de démarrage SAP ERS HTTP / *Lbrule51113* |5<*NuméroInstance*>13 |51113 |
| Service de démarrage SAP ERS HTTP / *Lbrule51114* |5<*NuméroInstance*>14 |51114 |
| WinRM *Lbrule5985* | |5985 |
| Partage de fichiers *Lbrule445* | |445 |

***Tableau 2 :** Numéro de port des instances SAP NetWeaver Java SCS*

![Figure 13 : Règles d’équilibrage de charge ASCS/SCS par défaut pour l’équilibrage de charge interne Azure][sap-ha-guide-figure-3004]

***Figure 13 :** Règles d’équilibrage de charge ASCS/SCS par défaut pour l’équilibrage de charge interne Azure*

Définissez l’adresse IP de l’équilibrage de charge **pr1-lb-dbms** sur l’adresse IP du nom d’hôte virtuel de l’instance SGBD (dans notre exemple, **10.0.0.33**).

### <a name="a-namefe0bd8b52b4345e3829580bee5415716a-change-the-ascsscs-default-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Modifier les règles d’équilibrage de charge ASCS/SCS par défaut pour l’équilibrage de charge interne Azure
Si vous souhaitez utiliser d’autres numéros pour les instances SAP ASCS ou SCS, vous devez mettre à jour les noms et valeurs de ces ports.

Pour mettre à jour les numéros d’instance, vous pouvez utiliser le portail Azure :

Accédez à l’équilibrage de charge **<*SID*>-lb-ascs** > ****.

Pour toutes les règles d’équilibrage de charge qui appartiennent à l’instance SAP ASCS ou SCS, modifiez les valeurs suivantes :

* Nom
* Port
* Port principal

Par exemple, si vous souhaitez remplacer le numéro d’instance ASC par défaut 00 par 31, vous devez modifier tous les ports répertoriés dans le tableau 1.

Voici un exemple de mise à jour pour le port *lbrule3200*.

![Figure 14 : Modifier les règles d’équilibrage de charge ASCS/SCS par défaut pour l’équilibrage de charge interne Azure][sap-ha-guide-figure-3005]

***Figure 14 :** Modifier les règles d’équilibrage de charge ASCS/SCS par défaut pour l’équilibrage de charge interne Azure*

### <a name="a-namee69e9a34460147a3a41cd2e11c626c0ca-add-windows-virtual-machines-to-the-domain"></a><a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Ajouter des machines virtuelles Windows au domaine
Après avoir affecté une adresse IP statique aux machines virtuelles, ajoutez les machines virtuelles au domaine.

![Figure 15 : Ajouter une machine virtuelle à un domaine][sap-ha-guide-figure-3006]

***Figure 15 :** Ajouter une machine virtuelle à un domaine*

### <a name="a-name661035b24d0f4d3186f8dc0a50d78158a-add-registry-entries-on-both-cluster-nodes-of-the-sap-ascsscs-instance"></a><a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Ajouter des entrées de Registre aux deux nœuds de cluster de l’instance SAP ASCS/SCS
Azure Load Balancer offre un équilibrage de charge interne qui ferme les connexions lorsqu’elles sont inactives pendant un laps de temps défini (délai d’inactivité). Les processus de travail SAP dans les instances de dialogue ouvrent des connexions au processus de mise en file d’attente SAP dès que la première requête de mise en file d’attente/suppression de la file d’attente doit être envoyée. Ces connexions restent généralement établies jusqu’à ce que le processus de travail ou de fil d’attente redémarre. Toutefois, si la connexion est inactive pendant un laps de temps donné, l’équilibrage de charge interne Azure ferme les connexions. Ce n’est pas un problème, car le processus de travail SAP rétablit la connexion au processus de file d’attente si elle n’existe plus. Ces activités sont documentées dans les traces de développement des processus SAP, mais elles créent une quantité importante de contenu supplémentaire dans les traces. Il est conseillé de modifier les paramètres TCP/IP `KeepAliveTime` et `KeepAliveInterval` sur les deux nœuds de cluster. Associez ces modifications des paramètres TCP/IP et les paramètres de profil SAP décrits plus loin dans cet article.

Ajoutez les entrées de registre Windows suivantes aux deux nœuds de cluster Windows pour l’instance SAP ASCS/SCS :

| Chemin | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Nom de la variable |`KeepAliveTime` |
| Type de variable |REG_DWORD (décimal) |
| Valeur |120 000 |
| Lien vers la documentation |[https://technet.microsoft.com/en-us/library/cc957549.aspx](https://technet.microsoft.com/en-us/library/cc957549.aspx) |

***Tableau 3 :** Modification du premier paramètre TCP/IP*

| Chemin | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Nom de la variable |`KeepAliveInterval` |
| Type de variable |REG_DWORD (décimal) |
| Valeur |120 000 |
| Lien vers la documentation |[https://technet.microsoft.com/en-us/library/cc957548.aspx](https://technet.microsoft.com/en-us/library/cc957548.aspx) |

***Tableau 4 :** Modification du deuxième paramètre TCP/IP*

Pour appliquer les modifications, redémarrez les deux nœuds de cluster.

### <a name="a-name0d67f090792843e087725ccbf8f59aaba-set-up-a-windows-server-failover-clustering-cluster-for-an-sap-ascsscs-instance"></a><a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Configurer un cluster de clustering de basculement Windows Server pour une instance SAP ASCS/SCS
#### <a name="a-name5eecb071c7034cccba6dfe9c6ded9d79a-collect-the-cluster-nodes-in-a-cluster-configuration"></a><a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> Collecter les nœuds de cluster dans une configuration de cluster
La première étape consiste à ajouter la fonctionnalité de clustering de basculement aux deux nœuds de cluster. Pour ce faire, utilisez l’Assistant Ajout de rôles et de fonctionnalités.

La deuxième étape consiste à configurer le cluster de basculement à l’aide du Gestionnaire du cluster de basculement.

Dans le Gestionnaire du cluster de basculement, cliquez sur **Créer un cluster**, puis ajoutez uniquement le nom du premier nœud de cluster (A), par exemple **pr1-ascs-0**. N’ajoutez pas encore le second nœud. Vous l’ajouterez à une étape ultérieure.

![Figure 16 : Ajouter le nom du serveur ou de la machine virtuelle au premier nœud de cluster][sap-ha-guide-figure-3007]

***Figure 16 :** Ajouter le nom du serveur ou de la machine virtuelle au premier nœud de cluster*

Ensuite, vous êtes invité à définir le nom de réseau (nom d’hôte virtuel) du cluster.

![Figure 17 : Définir le nom du cluster][sap-ha-guide-figure-3008]

***Figure 17 :** Définir le nom du cluster*

Une fois le cluster créé, exécutez un test de validation du cluster.

![Figure 18 : Exécuter le test de validation de cluster][sap-ha-guide-figure-3009]

***Figure 18 :** Exécuter le test de validation de cluster*

![Figure 19 : Aucun disque quorum n’a été trouvé][sap-ha-guide-figure-3010]

***Figure 19 :** Aucun disque quorum n’a été trouvé*

Vous pouvez ignorer les avertissements concernant des disques à ce stade de la procédure. Vous ajouterez un témoin de partage de fichiers et les disques partagés SIOS ultérieurement. À ce stade, le fait de ne pas disposer d’un disque quorum n’est pas un problème.

![Figure 20 : Une nouvelle adresse IP est requise pour la ressource principale du cluster][sap-ha-guide-figure-3011]

***Figure 20 :** Une nouvelle adresse IP est requise pour la ressource principale du cluster*

Le cluster ne peut pas démarrer, car l’adresse IP du serveur pointe vers un des nœuds de machine virtuelle. Vous devez modifier l’adresse IP du service principal du cluster.

Par exemple, vous devez affecter une adresse IP (dans notre exemple, **10.0.0.42**) au nom d’hôte virtuel du cluster **pr1-ascs-vir**. Effectuez cette opération dans la page de propriétés de la ressource IP du service principal du cluster, comme illustré dans la figure 21.

![Figure 21 : Dans la boîte de dialogue **Propriétés**, modifier l’adresse IP][sap-ha-guide-figure-3012]

***Figure 21 :** Dans la boîte de dialogue **Propriétés**, modifier l’adresse IP*

![Figure 22 : Affecter l’adresse IP réservée pour le cluster][sap-ha-guide-figure-3013]

***Figure 22 :** Affecter l’adresse IP réservée pour le cluster*

Après avoir modifié l’adresse IP, mettez en ligne le nom d’hôte virtuel du cluster.

![Figure 23 : Le service principal du cluster est opérationnel et son adresse IP correcte][sap-ha-guide-figure-3014]

***Figure 23 :** Le service principal du cluster est opérationnel et son adresse IP correcte*

Maintenant que le service principal du cluster est opérationnel, vous pouvez ajouter le deuxième nœud du cluster.

![Figure 24 : Ajouter le deuxième nœud du cluster][sap-ha-guide-figure-3015]

***Figure 24 :** Ajouter le deuxième nœud du cluster*

![Figure 25 : Ajouter le nom d’hôte du deuxième nœud du cluster, par exemple pr1-ascs-1][sap-ha-guide-figure-3016]

***Figure 25 :** Ajouter le nom d’hôte du deuxième nœud du cluster, par exemple **pr1-ascs-1***

![Figure 26 : Ne pas sélectionner la case à cocher][sap-ha-guide-figure-3017]

***Figure 26 :** Ne *pas* sélectionner la case à cocher*

> [!IMPORTANT]
> Assurez-vous que la case à cocher **Ajouter la totalité du stockage disponible au cluster** n’est *pas* sélectionnée.  
> 
> 

![Figure 27 : Ignorer les avertissements concernant le disque quorum][sap-ha-guide-figure-3018]

***Figure 27 :** Ignorer les avertissements concernant le disque quorum*

Vous pouvez ignorer les avertissements concernant le quorum et les disques. Vous définirez le quorum et partagerez le disque ultérieurement, à l’aide de la procédure décrite dans la section [Installer SIOS DataKeeper Cluster Edition pour le disque de partage en cluster SAP ASCS/SCS][sap-ha-guide-8.12.3].

#### <a name="a-namee49a452950c94dcfbde715a0c21d21caa-configure-a-cluster-file-share-witness"></a><a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> Configurer un témoin de partage de fichiers de cluster
##### <a name="a-name06260b30d6974c4db1c9d22c0bd64855a-create-a-file-share"></a><a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> Créer un partage de fichiers
Sélectionnez un témoin de partage de fichiers plutôt qu’un disque quorum. SIOS DataKeeper prend en charge cette option.

Dans les exemples de cet article, le témoin de partage de fichiers se trouve sur le serveur Active Directory/DNS exécuté dans Azure. Le témoin de partage de fichiers porte le nom **domcontr-0**. Comme vous avez configuré une connexion de réseau privé virtuel (VPN) à Azure (via un VPN de site à site ou Azure ExpressRoute), votre service Active Directory/DNS est local et n’est pas adapté à l’exécution d’un témoin de partage de fichiers.

> [!NOTE]
> Si votre service Active Directory/DNS s’exécute uniquement en local, ne configurez pas votre témoin de partage de fichiers sur le système d’exploitation Windows du service Active Directory/DNS exécuté localement. La latence du réseau entre les nœuds de cluster exécutés dans Azure et le service Active Directory/DNS local peut être trop importante et entraîner des problèmes de connectivité. Veillez à configurer le témoin de partage de fichiers sur une machine virtuelle Azure proche du nœud de cluster.  
> 
> 

Au moins 1 024 Mo d’espace libre sont nécessaires pour le disque quorum. Nous vous recommandons d’allouer 2 048 Mo d’espace libre à ce dernier.

La première étape consiste à ajouter l’objet nom de cluster.

![Figure 28 : Affecter les autorisations sur le partage pour l’objet nom de cluster][sap-ha-guide-figure-3019]

***Figure 28 :** Affecter les autorisations sur le partage pour l’objet nom de cluster*

Assurez-vous que les autorisations incluent la possibilité de modifier des données du partage pour l’objet nom de cluster (dans notre exemple, **pr1-ascs-vir$**). Pour ajouter l’objet nom de cluster à la liste, sélectionnez **Ajouter**. Modifiez le filtre de manière à rechercher les objets ordinateurs, en plus de ceux indiqués dans la figure 29 :

![Figure 29 : Modifier le type d’objet de façon à inclure les objets ordinateurs][sap-ha-guide-figure-3020]

***Figure 29 :** Modifier le type d’objet de façon à inclure les objets ordinateurs*

![Figure 30 : Sélectionner la case à cocher des objets ordinateurs][sap-ha-guide-figure-3021]

***Figure 30 :** Sélectionner la case à cocher des objets ordinateurs*

Ensuite, entrez l’objet nom de cluster, comme illustré dans la figure 29. Comme l’enregistrement a déjà été créé, vous pouvez modifier les autorisations, comme illustré dans la figure 28.

Ensuite, sélectionnez l’onglet **Sécurité** du partage, puis définissez des autorisations plus détaillées pour l’objet nom de cluster.

![Figure 31 : Définir les attributs de sécurité de l’objet nom de cluster sur le quorum de partage de fichiers][sap-ha-guide-figure-3022]

***Figure 31 :** Définir les attributs de sécurité de l’objet nom de cluster sur le quorum de partage de fichiers*

##### <a name="a-name4c08c38778a046b19d27b497b08cac3da-set-the-file-share-witness-quorum-in-failover-cluster-manager"></a><a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> Définir le quorum de témoin de partage de fichiers dans le Gestionnaire du cluster de basculement
Dans le Gestionnaire du cluster de basculement, définissez la configuration du cluster sur un témoin de partage de fichiers.

![Figure 32 : Démarrer l’Assistant Configuration de quorum du cluster][sap-ha-guide-figure-3023]

***Figure 32 :** Démarrer l’Assistant Configuration de quorum du cluster*

![Figure 33 : Différentes configurations de quorum sélectionnables][sap-ha-guide-figure-3024]

***Figure 33 :** Différentes configurations de quorum sélectionnables*

Choisissez **Sélectionner le témoin de quorum**.

![Figure 34 : Sélectionner le témoin de partage de fichiers][sap-ha-guide-figure-3025]

***Figure 34 :** Sélectionner le témoin de partage de fichiers*

Sélectionnez **Configurer le témoin de partage de fichiers**.

![Figure 35 : Définir l’emplacement du partage de fichiers du témoin][sap-ha-guide-figure-3026]

***Figure 35 :** Définir l’emplacement du partage de fichiers du témoin*

Entrez le chemin d’accès UNC du partage de fichiers (dans notre exemple, \\domcontr-0\FSW).

Sélectionnez **Suivant** pour afficher une liste des modifications possibles. Apportez les modifications souhaitées, puis sélectionnez **Suivant**.

![Figure 36 : Confirmation de la reconfiguration du cluster][sap-ha-guide-figure-3027]

***Figure 36 :** Confirmation de la reconfiguration du cluster*

Dans cette dernière étape, vous devez reconfigurer correctement la configuration du cluster, comme illustré dans la figure 36.  

### <a name="a-name5c8e5482841e45e1a89da05c0907c868a-install-sios-datakeeper-cluster-edition-for-the-sap-ascsscs-cluster-share-disk"></a><a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> Installer SIOS DataKeeper Cluster Edition pour le disque de partage en cluster SAP ASCS/SCS
Vous disposez maintenant d’une configuration fonctionnelle de clustering de basculement Windows Server dans Azure. Cependant, pour installer une instance SAP ASCS/SCS, vous avez besoin d’une ressource de disque partagé. Il n’est pas possible de créer les ressources de disque partagé requises dans Azure. SIOS DataKeeper Cluster Edition est une solution tierce que vous pouvez utiliser pour créer des ressources de disque partagé.

#### <a name="a-name1c2788c336484e829e0de058e475e2a3a-add-the-net-framework-35"></a><a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> Ajouter .NET Framework 3.5
Microsoft .NET Framework 3.5 n’est pas automatiquement activé ou installé dans Windows Server 2012 R2. Cependant, SIOS DataKeeper requiert .NET Framework sur tous les nœuds sur lesquels vous installez DataKeeper. Par conséquent, vous devez installer .NET Framework 3.5 sur le système d’exploitation invité de toutes les machines virtuelles du cluster.

Pour ajouter .NET Framework 3.5, deux options s’offrent à vous. La première consiste à utiliser l’Assistant Ajout de rôles et de fonctionnalités, illustré dans la figure 37.

![Figure 37 : Installer .NET Framework 3.5 à l’aide de l’Assistant Ajout de rôles et de fonctionnalités][sap-ha-guide-figure-3028]

***Figure 37 :** Installer .NET Framework 3.5 à l’aide de l’Assistant Ajout de rôles et de fonctionnalités*

![Figure 38 : Barre de progression de l’installation de .NET Framework 3.5 affichée dans l’Assistant Ajout de rôles et de fonctionnalités][sap-ha-guide-figure-3029]

***Figure 38 :** Barre de progression de l’installation de .NET Framework 3.5 affichée dans l’Assistant Ajout de rôles et de fonctionnalités*

La seconde option pour activer la fonctionnalité .NET Framework 3.5 consiste à utiliser l’outil de ligne de commande dism.exe. Pour ce type d’installation, vous devez accéder au répertoire SxS du média d’installation Windows. Exécutez la commande suivante dans une invite de commandes avec élévation de privilèges :

```
Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
```

#### <a name="a-namedd41d5a28083415b9878839652812102a-install-sios-datakeeper"></a><a name="dd41d5a2-8083-415b-9878-839652812102"></a> Installer SIOS DataKeeper
Installez SIOS DataKeeper Cluster Edition sur chaque nœud du cluster. Pour créer un stockage partagé virtuel avec SIOS DataKeeper, créez un miroir synchronisé, puis simulez le stockage partagé en cluster.

Avant d’installer le logiciel SIOS, créez l’utilisateur de domaine **DataKeeperSvc**.

> [!NOTE]
> Ajoutez l’utilisateur **DataKeeperSvc** au groupe **Administrateur local** sur les deux nœuds de cluster.
> 
> 

Installez le logiciel SIOS sur les deux nœuds de cluster.

![Programme d’installation de SIOS][sap-ha-guide-figure-3030]

![Figure 39 : Premier écran d’installation de SIOS DataKeeper][sap-ha-guide-figure-3031]

***Figure 39 :** Premier écran d’installation de SIOS DataKeeper*

![Figure 40 : DataKeeper vous indique qu’un service sera désactivé][sap-ha-guide-figure-3032]

***Figure 40 :** DataKeeper vous indique qu’un service sera désactivé*

Dans la boîte de dialogue illustrée dans la figure 40, sélectionnez **Oui**.

![Figure 41 : Sélection de l’utilisateur de SIOS DataKeeper][sap-ha-guide-figure-3033]

***Figure 41 :** Sélection de l’utilisateur de SIOS DataKeeper*

Dans l’écran présenté dans la figure 41, nous vous recommandons de sélectionner **Compte de domaine ou de serveur**.

![Figure 42 : Entrer le nom et le mot de passe de l’utilisateur du domaine pour l’installation de SIOS DataKeeper][sap-ha-guide-figure-3034]

***Figure 42 :** Entrer le nom et le mot de passe de l’utilisateur du domaine pour l’installation de SIOS DataKeeper*

Entrez le nom et le mot de passe de l’utilisateur de compte de domaine que vous avez créés pour SIOS DataKeeper.

![Figure 43 : Entrer votre licence SIOS DataKeeper][sap-ha-guide-figure-3035]

***Figure 43 :** Entrer votre licence SIOS DataKeeper*

Installez la clé de licence de votre instance SIOS DataKeeper, comme illustré dans la figure 43. À la fin de l’installation, vous devrez redémarrer la machine virtuelle.

#### <a name="a-named9c1fc8e87104dffbec21f535db7b006a-set-up-sios-datakeeper"></a><a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> Configurer SIOS DataKeeper
Après avoir installé SIOS DataKeeper sur les deux nœuds, vous devez commencer la configuration. L’objectif de la configuration est de mettre en place une réplication synchrone des données entre les disques durs virtuels supplémentaires attachés à chacune des machines virtuelles. Voici la marche à suivre pour configurer les deux nœuds.

![Figure 44 : Outil de configuration et de gestion de SIOS DataKeeper][sap-ha-guide-figure-3036]

***Figure 44 :** Outil de configuration et de gestion de SIOS DataKeeper*

Démarrez l’outil de configuration et de gestion de DataKeeper, puis sélectionnez **Connect Server** (Connexion au serveur) (cette option est entourée en rouge dans la figure 44).

![Figure 45 : Insérer le nom ou l’adresse TCP/IP du premier nœud auquel l’outil de gestion et de configuration doit se connecter, puis du second nœud][sap-ha-guide-figure-3037]

***Figure 45 :** Insérer le nom ou l’adresse TCP/IP du premier nœud auquel l’outil de gestion et de configuration doit se connecter, puis du second nœud*

L’étape suivante consiste à créer le travail de réplication entre les deux nœuds.

![Figure 46 : Créer un travail de réplication][sap-ha-guide-figure-3038]

***Figure 46 :** Créer un travail de réplication*

Un assistant vous guide à travers le processus de création d’un travail de réplication.

![Figure 47 : Définir le nom du travail de réplication][sap-ha-guide-figure-3039]

***Figure 47 :** Définir le nom du travail de réplication*

![Figure 48 : Définir les données de base du nœud qui doit être le nœud source actuel][sap-ha-guide-figure-3040]

***Figure 48 :** Définir les données de base du nœud qui doit être le nœud source actuel*

Dans un premier temps, vous devez définir le nom, l’adresse TCP/IP et le volume de disque du nœud source. La deuxième étape consiste à définir le nœud cible. Comme expliqué précédemment, vous devez définir le nom, l’adresse TCP/IP et le volume de disque du nœud cible.

![Figure 49 : Définir les données de base du nœud qui doit être le nœud cible actuel][sap-ha-guide-figure-3041]

***Figure 49 :** Définir les données de base du nœud qui doit être le nœud cible actuel*

Ensuite, définissez les algorithmes de compression. Dans notre exemple, nous vous recommandons de compresser le flux de réplication. Dans les situations de resynchronisation notamment, la compression du flux de réplication accélère considérablement la resynchronisation. Notez que la compression utilise les ressources de processeur et de RAM d’une machine virtuelle. Plus le taux de compression augmente, plus le volume de ressources de processeur utilisées est important. Vous pouvez ajuster ce paramètre ultérieurement.

Vous devez également vérifier si la réplication se fait de façon asynchrone ou synchrone. *Lorsque vous protégez des configurations SAP ASCS/SCS, vous devez utiliser la réplication synchrone*.  

![Figure 50 : Définir les détails de la réplication][sap-ha-guide-figure-3042]

***Figure 50 :** Définir les détails de la réplication*

La dernière étape consiste à définir si le volume répliqué par le travail de réplication doit être représenté auprès d’une configuration de cluster de clustering de basculement Windows Server en tant que disque partagé. Pour la configuration SAP ASCS/SCS, sélectionnez **Oui** pour que le cluster Windows voie le volume répliqué en tant que disque partagé utilisable comme volume de cluster.

![Figure 51 : Sélectionner **Oui** pour activer le volume répliqué en tant que volume de cluster][sap-ha-guide-figure-3043]

***Figure 51 :** Sélectionner **Oui** pour activer le volume répliqué en tant que volume de cluster*

Une fois le volume créé, l’outil de configuration et de gestion de DataKeeper indique que le travail de réplication est actif.

![Figure 52 : La mise en miroir synchrone de DataKeeper pour le disque de partage SAP ASCS/SCS est active][sap-ha-guide-figure-3044]

***Figure 52 :** La mise en miroir synchrone de DataKeeper pour le disque de partage SAP ASCS/SCS est active*

Le Gestionnaire du cluster de basculement affiche à présent le disque en tant que disque DataKeeper, comme illustré dans la figure 53.

![Figure 53 : Le Gestionnaire du cluster de basculement affiche le disque qui a été répliqué par DataKeeper][sap-ha-guide-figure-3045]

***Figure 53 :** Le Gestionnaire du cluster de basculement affiche le disque qui a été répliqué par DataKeeper*

## <a name="a-namea06f0b498a7a42bf8b0dc12026c5746ba-install-the-sap-netweaver-system"></a><a name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a> Installer le système SAP NetWeaver
Nous ne décrirons pas l’installation du SGBD, car celle-ci varie en fonction du SGBD utilisé. Toutefois, nous partons du principe que la haute disponibilité du SGBD est assurée par les fonctionnalités prises en charge par les différents fournisseurs de SGBD pour Azure, par exemple Always On ou la mise en miroir de base de données pour SQL Server, et Oracle Data Guard pour Oracle. Dans le scénario utilisé dans cet article, nous n’avons pas ajouté de protection supplémentaire au SGBD.

Il n’existe pas de considérations particulières lorsque différents services de SGBD interagissent avec ce type de configuration SAP ASCS/SCS en cluster dans Azure.

> [!NOTE]
> Les procédures d’installation de systèmes SAP NetWeaver ABAP, Java et ABAP+Java sont presque identiques. La différence la plus notable est qu’un système SAP ABAP comprend une instance ASCS, le système SAP Java une instance SCS, et le système SAP ABAP+Java une instance ASCS et une instance SCS exécutées dans le même groupe de cluster de basculement Microsoft. Toute différence d’installation pour chaque pile d’installation de SAP NetWeaver est mentionnée explicitement. Le reste de la procédure est identique.  
> 
> 

### <a name="a-name31c6bd4f51df40579fdf3fcbc619c170a-install-sap-with-a-highavailability-ascsscs-instance"></a><a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a> Installer SAP avec une instance ASCS/SCS à haute disponibilité
> [!IMPORTANT]
> Veillez à ne pas placer votre fichier d’échange sur des volumes DataKeeper mis en miroir. DataKeeper ne prend pas en charge les volumes mis en miroir. Vous pouvez laisser votre fichier d’échange sur le lecteur temporaire D d’une machine virtuelle (valeur par défaut). S’il ne s’y trouve pas déjà, déplacez le fichier d’échange Windows sur le lecteur D de votre machine virtuelle Azure.
> 
> 

#### <a name="a-namea97ad604909444fea364f89cb39bf097a-create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a><a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a> Créer un nom d’hôte virtuel pour l’instance SAP ASCS/SCS en cluster
Tout d’abord, dans le Gestionnaire DNS Windows, créez une entrée DNS pour le nom d’hôte virtuel de l’instance ASC/SCS. Ensuite, définissez l’adresse IP affectée au nom d’hôte virtuel.

> [!NOTE]
> N’oubliez pas que l’adresse IP que vous affectez au nom d’hôte virtuel de l’instance ASC/SCS doit être identique à celle que vous avez affectée à l’équilibrage de charge Azure Load Balancer (<*SID*>-lb-ascs).  
> 
> 

L’adresse IP du nom d’hôte SAP ASCS/SCS virtuel (pr1-ascs-sap) est identique à celle de l’équilibrage de charge Azure Load Balancer (pr1-lb-ascs).

Un seul rôle de cluster de basculement SAP peut s’exécuter dans un même cluster de basculement Windows Server dans Azure (par exemple, une instance ASCS pour le système ABAP et une instance SCS pour le système Java ; pour un système ABAP+Java, il s’agirait d’une instance et une seule instance SCS).

> [!NOTE]
> Actuellement, le clustering à plusieurs SID décrit dans les guides d’installation de SAP (voir [Guides d’installation de SAP][sap-installation-guides]) ne fonctionne pas dans Azure.
> 
> 

![Figure 54 : Définir l’entrée DNS pour le nom virtuel et l’adresse TCP/IP du cluster SAP ASCS/SCS][sap-ha-guide-figure-3046]

***Figure 54 :** Définir l’entrée DNS pour le nom virtuel et l’adresse TCP/IP du cluster SAP ASCS/SCS*

L’entrée se trouve dans le Gestionnaire DNS, sous le domaine, comme illustré dans la figure 55.

![Figure 55 : Nouveau nom virtuel et nouvelle adresse TCP/IP de la configuration de cluster SAP ASCS/SCS][sap-ha-guide-figure-3047]

***Figure 55 :** Nouveau nom virtuel et nouvelle adresse TCP/IP de la configuration de cluster SAP ASCS/SCS*

#### <a name="a-nameeb5af918b42f4803bb50eff41f84b0b0a-install-the-sap-first-cluster-node"></a><a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> Installer le premier nœud de cluster SAP
Pour installer le premier cluster SAP, exécutez l’option du premier nœud de cluster sur le nœud de cluster A, par exemple sur l’hôte **pr1-ASC-0**.

Si vous souhaitez conserver les ports par défaut pour l’équilibrage de charge interne Azure, sélectionnez :

* **Système ABAP** : numéro d’instance **ASCS** **00**
* **Système Java** : numéro d’instance **SCS** **01**
* **Système ABAP+Java** : numéro d’instance **ASCS****00** et numéro d’instance **SCS** **01**

Si vous souhaitez utiliser des numéros d’instance autres que 00 pour l’instance ASCS ABAP et 01 pour l’instance SCS Java, vous devez d’abord modifier les règles d’équilibrage de charge interne Azure par défaut, comme décrit dans [Modifier les règles d’équilibrage de charge ASCS/SCS par défaut pour l’équilibrage de charge interne Azure][sap-ha-guide-8.9].

Ensuite, vous devez effectuer quelques étapes qui ne sont pas décrites dans la documentation d’installation de SAP habituelle.

> [!NOTE]
> La documentation d’installation de SAP explique comment installer le premier nœud de cluster ASC/SCS.
> 
> 

#### <a name="a-namee4caaab2e90f4f2cbc842cd2e12a9556a-modify-the-sap-profile-of-the-ascsscs-ipowershellnstance"></a><a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> Modifier le profil SAP de l’instance ASCS/SCS i```powershell
Vous devez ajouter un nouveau paramètre de profil. Ce paramètre de profil empêche les connexions entre les processus de travail SAP et le serveur de mise en file d’attente de se fermer lorsqu’elles sont inactives pendant trop longtemps. Nous avons mentionné ce scénario problématique dans la section [Ajouter des entrées de Registre aux deux nœuds de cluster de l’instance SAP ASCS/SCS][sap-ha-guide-8.11] de cet article. Dans cette section, nous avons également apporté deux modifications à certains paramètres de connexion TCP/IP de base. Dans un deuxième temps, vous devez configurer le serveur de mise en file d’attente pour qu’il envoie un signal **keep_alive** afin que les connexions n’atteignent pas le seuil d’inactivité de l’équilibrage de charge interne Azure.

Ajoutez ce paramètre de profil au profil de l’instance SAP ASCS/SCS :

```
enque/encni/set_so_keepalive = true
```
Dans notre exemple, le chemin est :

`<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

Par exemple, au profil de l’instance SAP SCS et au chemin correspondant :

`<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

#### <a name="a-name10822f4f32e74871b63a9b86c76ce761a-add-a-probe-port"></a><a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> Ajouter un port de sondage
Utilisez la fonctionnalité de sondage de l’équilibrage de charge interne pour que la configuration de cluster entière fonctionne avec Load Balancer. Généralement, l’équilibrage de charge interne Azure répartit équitablement la charge de travail entrante entre les machines virtuelles participantes. Toutefois, cela ne fonctionne pas dans certaines configurations de cluster, car une seule instance est active. L’autre instance est passive et ne peut accepter aucune partie de la charge de travail. La fonctionnalité de sondage est utile lorsque l’équilibrage de charge interne Azure affecte du travail uniquement à une instance active. Avec la fonctionnalité de sondage, l’équilibrage de charge interne peut détecter les instances actives, puis cibler uniquement ces instances avec la charge de travail.

Tout d’abord, vérifiez le paramètre **ProbePort** actuel à l’aide de cette commande PowerShell. Exécutez-la au sein de l’une des machines virtuelles de la configuration de cluster :

```PowerShell
Get-ClusterResource „SAP PR1 IP" | Get-ClusterParameter
```

![Figure 56 : Le port de sondage de la configuration de cluster est défini sur 0 par défaut][sap-ha-guide-figure-3048]

***Figure 56 :** Le port de sondage de la configuration de cluster est défini sur 0 par défaut*

Ensuite, définissez un port de sondage. Le numéro de port de sondage par défaut est 0. Dans notre exemple, nous utilisons le port de sondage **62300**.

Le numéro de port est défini dans les modèles Azure Resource Manager SAP. Vous pouvez affecter le numéro de port dans PowerShell.

Tout d’abord, récupérez l’**IP WAC SAP** de la ressource de cluster du nom d’hôte virtuel SAP.

```PowerShell
$var = Get-ClusterResource | Where-Object {  $_.name -eq "SAP PR1 IP"  }
```

Ensuite, définissez le port de sondage sur **62300**.

```PowerShell
$var | Set-ClusterParameter -Multiple @{"Address"="10.0.0.43";"ProbePort"=62300;"Subnetmask"="255.255.255.0";"Network"="Cluster Network 1";"OverrideAddressMatch"=1;"EnableDhcp"=0}  
```

Pour activer les modifications, vous devez arrêter et démarrer le rôle de cluster **SAP PR1**.

Après avoir mis en ligne le rôle de cluster **SAP PR1**, vérifiez que **ProbePort** est défini sur la nouvelle valeur :

```PowerShell
Get-ClusterResource „SAP PR1 IP" | Get-ClusterParameter
```

![Figure 57 : Sonder le port de cluster après avoir défini la nouvelle valeur][sap-ha-guide-figure-3049]

***Figure 57 :** Sonder le port de cluster après avoir défini la nouvelle valeur*

**ProbePort** est défini sur **62300**. Vous pouvez maintenant accéder au partage de fichiers **\\\ascsha-clsap\sapmnt** depuis d’autres hôtes, par exemple **ascsha-dbas**.

### <a name="a-name85d78414b21d409792b634d8bcb724b7a-install-the-database-instance"></a><a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> Installer l’instance de base de données
Pour installer l’instance de base de données, suivez la procédure décrite dans la documentation d’installation de SAP.

### <a name="a-name8a276e16f5074071b829cdc0a4d36748a-install-the-second-cluster-node"></a><a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> Installer le deuxième nœud de cluster
Pour installer le deuxième cluster, suivez les étapes du guide d’installation de SAP.

### <a name="a-name094bc89531d4447191cc1513b64e406aa-change-the-start-type-of-the-sap-ers-windows-service-instance"></a><a name="094bc895-31d4-4471-91cc-1513b64e406a"></a> Modifier le type de démarrage de l’instance de service Windows SAP ERS
Définissez le type de démarrage du service Windows SAP ERS (Enqueue Replication Server) sur **Automatique (début différé)** sur les deux nœuds de cluster.

![Figure 58 : Définir le type de service de l’instance SAP ERS sur Automatique (début différé)][sap-ha-guide-figure-3050]

***Figure 58 :** Définir le type de service de l’instance SAP ERS sur Automatique (début différé)*

### <a name="a-name2477e58fc5a74a5d9ae37b91022cafb5a-install-the-sap-primary-application-server"></a><a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> Installer le serveur d’applications principal SAP
Installez l’instance de serveur d’application principal (PAS) <*SID*>-di-0 sur la machine virtuelle désignée pour héberger le PAS. Il n’existe aucune dépendance sur des spécificités d’Azure ou DataKeeper.

### <a name="a-name0ba4a6c1cc374bcfa8dc025de4263772a-install-the-sap-additional-application-server"></a><a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> Installer le serveur d’applications supplémentaire SAP
Installez un serveur d’applications supplémentaire SAP (AAS) sur toutes les machines virtuelles désignées pour héberger un serveur d’applications SAP. Par exemple, sur <*SID*>-di-1 à <*SID*>-di-<n>.

## <a name="a-name18aa2b9d92d24c0e8ddd5acaabda99e9a-test-the-sap-ascsscs-instance-failover-and-sios-replication"></a><a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> Tester le basculement et la réplication SIOS de l’instance SAP ASCS/SCS
Vous pouvez facilement tester et surveiller le basculement et la réplication de disque SIOS d’une instance SAP ASCS/SCS à l’aide du Gestionnaire du cluster de basculement et de l’interface utilisateur SIOS DataKeeper.

### <a name="a-name65fdef0f9f9441f9b314ea45bbfea445a-sap-ascsscs-instance-is-running-on-cluster-node-a"></a><a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a> L’instance SAP ASCS/SCS s’exécute sur le nœud de cluster A
Le groupe de cluster **SAP WAC** s’exécute sur le nœud de cluster A, par exemple sur **ascsha-clna**. Affectez le lecteur de disque partagé S, qui fait partie du groupe de cluster **SAP WAC** et est utilisé par l’instance ASCS/SCS, au nœud de cluster A.

![Figure 59 : Gestionnaire du cluster de basculement : le groupe de cluster SAP <*SID*> s’exécute sur le nœud de cluster A][sap-ha-guide-figure-5000]

***Figure 59 :** Gestionnaire du cluster de basculement : le groupe de cluster SAP <*SID*> s’exécute sur le nœud de cluster A*

À l’aide de l’interface utilisateur SIOS DataKeeper, vous pouvez voir que les données du disque partagé sont répliquées de manière synchrone du lecteur du volume source S sur le nœud de cluster A au lecteur du volume cible S sur le nœud de cluster B (par exemple, de **ascsha-clna [10.0.0.41]** à **ascsha-clnb [10.0.0.42]**).

![Figure 60 : Dans SIOS DataKeeper, répliquer le volume local du nœud de cluster A au nœud de cluster B][sap-ha-guide-figure-5001]

***Figure 60 :** Dans SIOS DataKeeper, répliquer le volume local du nœud de cluster A au nœud de cluster B*

### <a name="a-name5e959fa98fcd49e5a12c37f6ba07b916a-failover-from-node-a-to-node-b"></a><a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a> Basculement du nœud A au nœud B
Vous pouvez utiliser l’une des options suivantes pour lancer le basculement du groupe de cluster SAP <*SID*> du nœud de cluster A au nœud de cluster B :

* Utiliser le Gestionnaire du cluster de basculement  
* Utiliser l’applet de commande PowerShell de cluster de basculement
  ```powershell
  Move-ClusterGroup -Name "SAP WAC"
  ```
* Redémarrer le nœud de cluster A dans le système d’exploitation invité Windows (lance le basculement automatique du groupe de cluster SAP <*SID*> du nœud A au nœud B)  
* Redémarrer le nœud de cluster A depuis le portail Azure (lance le basculement automatique du groupe de cluster SAP <*SID*> du nœud A au nœud B)  
* Redémarrer le nœud de cluster A à l’aide d’Azure PowerShell (lance le basculement automatique du groupe de cluster SAP <*SID*> du nœud A au nœud B)
  
  ```powershell
  Restart-AzureVM -Name ascsha-clna -ServiceName ascsha-cluster
  ```

Après le basculement, le groupe de cluster SAP <*SID*> s’exécute sur le nœud de cluster B, par exemple sur **ascsha-clnb**.

![Figure 61 : Dans le Gestionnaire du cluster de basculement, le groupe de cluster SAP <*SID*> s’exécute sur le nœud de cluster B][sap-ha-guide-figure-5002]

***Figure 61 :** Dans le Gestionnaire du cluster de basculement, le groupe de cluster SAP <*SID*> s’exécute sur le nœud de cluster B*

Le disque partagé est maintenant monté sur le nœud de cluster B. SIOS DataKeeper réplique les données du lecteur du volume source S sur le nœud de cluster B au lecteur du volume cible sur le nœud de cluster A (par exemple, de **ascsha-clnb [10.0.0.42]** à **ascsha-clna [10.0.0.41]**).

![Figure 62 : SIOS DataKeeper réplique le volume local du nœud B au nœud de cluster A][sap-ha-guide-figure-5003]

***Figure 62 :** SIOS DataKeeper réplique le volume local du nœud B au nœud de cluster A*

<!---HONumber=Oct16_HO2-->


