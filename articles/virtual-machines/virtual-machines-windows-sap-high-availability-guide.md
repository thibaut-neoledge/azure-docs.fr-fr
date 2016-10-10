<properties
   pageTitle="SAP NetWeaver sur machines virtuelles Windows – Guide de haute disponibilité | Microsoft Azure"
   description="SAP NetWeaver sur machines virtuelles Windows – Guide de haute disponibilité"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="goraco"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="08/18/2016"
   ms.author="goraco"/>

# SAP NetWeaver sur machines virtuelles Windows – Guide de haute disponibilité

[767598]: https://service.sap.com/sap/support/notes/767598
[773830]: https://service.sap.com/sap/support/notes/773830
[826037]: https://service.sap.com/sap/support/notes/826037
[965908]: https://service.sap.com/sap/support/notes/965908
[1031096]: https://service.sap.com/sap/support/notes/1031096
[1139904]: https://service.sap.com/sap/support/notes/1139904
[1173395]: https://service.sap.com/sap/support/notes/1173395
[1245200]: https://service.sap.com/sap/support/notes/1245200
[1409604]: https://service.sap.com/sap/support/notes/1409604
[1558958]: https://service.sap.com/sap/support/notes/1558958
[1585981]: https://service.sap.com/sap/support/notes/1585981
[1588316]: https://service.sap.com/sap/support/notes/1588316
[1590719]: https://service.sap.com/sap/support/notes/1590719
[1597355]: https://service.sap.com/sap/support/notes/1597355
[1605680]: https://service.sap.com/sap/support/notes/1605680
[1619720]: https://service.sap.com/sap/support/notes/1619720
[1619726]: https://service.sap.com/sap/support/notes/1619726
[1619967]: https://service.sap.com/sap/support/notes/1619967
[1750510]: https://service.sap.com/sap/support/notes/1750510
[1752266]: https://service.sap.com/sap/support/notes/1752266
[1757924]: https://service.sap.com/sap/support/notes/1757924
[1757928]: https://service.sap.com/sap/support/notes/1757928
[1758182]: https://service.sap.com/sap/support/notes/1758182
[1758496]: https://service.sap.com/sap/support/notes/1758496
[1772688]: https://service.sap.com/sap/support/notes/1772688
[1814258]: https://service.sap.com/sap/support/notes/1814258
[1882376]: https://service.sap.com/sap/support/notes/1882376
[1909114]: https://service.sap.com/sap/support/notes/1909114
[1922555]: https://service.sap.com/sap/support/notes/1922555
[1928533]: https://service.sap.com/sap/support/notes/1928533
[1941500]: https://service.sap.com/sap/support/notes/1941500
[1956005]: https://service.sap.com/sap/support/notes/1956005
[1973241]: https://service.sap.com/sap/support/notes/1973241
[1984787]: https://service.sap.com/sap/support/notes/1984787
[1999351]: https://service.sap.com/sap/support/notes/1999351
[2002167]: https://service.sap.com/sap/support/notes/2002167
[2015553]: https://service.sap.com/sap/support/notes/2015553
[2039619]: https://service.sap.com/sap/support/notes/2039619
[2121797]: https://service.sap.com/sap/support/notes/2121797
[2134316]: https://service.sap.com/sap/support/notes/2134316
[2178632]: https://service.sap.com/sap/support/notes/2178632
[2191498]: https://service.sap.com/sap/support/notes/2191498
[2233094]: https://service.sap.com/sap/support/notes/2233094
[2243692]: https://service.sap.com/sap/support/notes/2243692

[sap-installation-guides]: http://service.sap.com/instguides

[azure-cli]: ../xplat-cli-install.md
[azure-portal]: https://portal.azure.com
[azure-ps]: ../powershell-install-configure.md
[azure-quickstart-templates-github]: https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]: https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]: ../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]: ../azure-subscription-service-limits.md#subscription

[dbms-guide]: virtual-machines-windows-sap-dbms-guide.md "SAP NetWeaver sur machines virtuelles Windows – Guide de déploiement SGBD"
[dbms-guide-2.1]: virtual-machines-windows-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f "Mise en cache pour les machines virtuelles et les disques durs virtuels"
[dbms-guide-2.2]: virtual-machines-windows-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 "RAID logiciel"
[dbms-guide-2.3]: virtual-machines-windows-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 "Microsoft Azure Storage"
[dbms-guide-2]: virtual-machines-windows-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 "Structure d’un déploiement SGBDR"
[dbms-guide-3]: virtual-machines-windows-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 "Haute disponibilité et récupération d’urgence avec les machines virtuelles Azure"
[dbms-guide-5.5.1]: virtual-machines-windows-sap-dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 "SQL Server 2012 SP1 CU4 et versions ultérieures"
[dbms-guide-5.5.2]: virtual-machines-windows-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b "SQL Server 2012 SP1 CU3 et versions antérieures"
[dbms-guide-5.6]: virtual-machines-windows-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 "Utilisation d’images SQL Server issues de Microsoft Azure Marketplace"
[dbms-guide-5.8]: virtual-machines-windows-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 "Résumé – SQL Server général pour SAP sur Azure"
[dbms-guide-5]: virtual-machines-windows-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 "Caractéristiques de SGBDR SQL Server"
[dbms-guide-8.4.1]: virtual-machines-windows-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 "Configuration du stockage"
[dbms-guide-8.4.2]: virtual-machines-windows-sap-dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d "Sauvegarde et restauration"
[dbms-guide-8.4.3]: virtual-machines-windows-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c "Considérations sur les performances de sauvegarde et de restauration"
[dbms-guide-8.4.4]: virtual-machines-windows-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 "Autres"
[dbms-guide-900-sap-cache-server-on-premises]: virtual-machines-windows-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]: ./media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]: ./media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]: ./media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]: ./media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]: ./media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]: ./media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]: ./media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]: ./media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]: ./media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]: virtual-machines-windows-sap-deployment-guide.md "SAP NetWeaver sur machines virtuelles Windows – Guide de déploiement"
[deployment-guide-2.2]: virtual-machines-windows-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 "Ressources SAP"
[deployment-guide-3.1.2]: virtual-machines-windows-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab "Déploiement d’une machine virtuelle avec une image personnalisée"
[deployment-guide-3.2]: virtual-machines-windows-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 "Scénario 1 : Déploiement d’une machine virtuelle provenant d’Azure Marketplace pour SAP"
[deployment-guide-3.3]: virtual-machines-windows-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 "Scénario 2 : Déploiement d’une machine virtuelle avec une image personnalisée pour SAP"
[deployment-guide-3.4]: virtual-machines-windows-sap-deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 "Scénario 3 : Déplacement d’une machine virtuelle locale à l’aide d’un disque dur virtuel Azure non généralisé avec SAP"
[deployment-guide-3]: virtual-machines-windows-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e "Scénarios de déploiement de machines virtuelles pour SAP sur Microsoft Azure"
[deployment-guide-4.1]: virtual-machines-windows-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 "Déploiement d’applets de commande Azure PowerShell"
[deployment-guide-4.2]: virtual-machines-windows-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e "Téléchargement et importation d’applets de commande PowerShell appropriées pour SAP"
[deployment-guide-4.3]: virtual-machines-windows-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc "Joindre une machine virtuelle au domaine local – Windows uniquement"
[deployment-guide-4.4.2]: virtual-machines-windows-sap-deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 "Linux"
[deployment-guide-4.4]: virtual-machines-windows-sap-deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d "Télécharger, installer et activer l’agent de machine virtuelle Azure"
[deployment-guide-4.5.1]: virtual-machines-windows-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 "Azure PowerShell"
[deployment-guide-4.5.2]: virtual-machines-windows-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f "Interface de ligne de commande Azure"
[deployment-guide-4.5]: virtual-machines-windows-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca "Configurer l’extension d’analyse Azure améliorée pour SAP"
[deployment-guide-5.1]: virtual-machines-windows-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 "Vérification de la disponibilité de l’analyse Azure améliorée pour SAP"
[deployment-guide-5.2]: virtual-machines-windows-sap-deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 "Contrôle d’intégrité de la configuration de l’infrastructure d’analyse Azure"
[deployment-guide-5.3]: virtual-machines-windows-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 "Étapes supplémentaires de la résolution des problèmes de l’infrastructure d’analyse Azure pour SAP"

[deployment-guide-configure-monitoring-scenario-1]: virtual-machines-windows-sap-deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b "Configuration de l’analyse"
[deployment-guide-configure-proxy]: virtual-machines-windows-sap-deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d "Configurer le proxy"
[deployment-guide-figure-100]: ./media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]: ./media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]: virtual-machines-windows-sap-deployment-guide.md#figure-11
[deployment-guide-figure-1100]: ./media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]: ./media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]: ./media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]: virtual-machines-windows-sap-deployment-guide.md#figure-14
[deployment-guide-figure-1400]: ./media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]: ./media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]: ./media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]: virtual-machines-windows-sap-deployment-guide.md#figure-5
[deployment-guide-figure-50]: ./media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]: ./media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]: virtual-machines-windows-sap-deployment-guide.md#figure-6
[deployment-guide-figure-600]: ./media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]: virtual-machines-windows-sap-deployment-guide.md#figure-7
[deployment-guide-figure-700]: ./media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]: ./media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]: ./media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]: virtual-machines-windows-sap-deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]: virtual-machines-windows-sap-deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]: virtual-machines-windows-sap-deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]: virtual-machines-windows-sap-deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b "Vérifications et résolution des problèmes pour la configuration de l’analyse de bout en bout pour SAP sur Azure"

[deploy-template-cli]: ../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows
[deploy-template-portal]: ../resource-group-template-deploy.md#deploy-with-the-preview-portal
[deploy-template-powershell]: ../resource-group-template-deploy.md#deploy-with-powershell

[dr-guide-classic]: http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]: virtual-machines-windows-sap-get-started.md
[getting-started-dbms]: virtual-machines-windows-sap-get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]: virtual-machines-windows-sap-get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]: virtual-machines-windows-sap-get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]: virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]: virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]: virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]: virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]: virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]: virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]: http://go.microsoft.com/fwlink/?LinkId=613056

[ha-guide]: virtual-machines-windows-sap-high-availability-guide.md

[install-extension-cli]: virtual-machines-linux-enable-aem.md

[Logo_Linux]: ./media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]: ./media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]: https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]: virtual-machines-windows-sap-planning-guide.md "SAP NetWeaver sur machines virtuelles Windows – Guide de planification et d’implémentation"
[planning-guide-1.2]: virtual-machines-windows-sap-planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff "Ressources"
[planning-guide-11]: virtual-machines-windows-sap-planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 "Haute disponibilité (HA) et récupération d’urgence (DR)pour SAP NetWeaver s’exécutant sur des machines virtuelles Azure"
[planning-guide-11.4.1]: virtual-machines-windows-sap-planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 "Haute disponibilité pour les serveurs d’applications SAP"
[planning-guide-11.5]: virtual-machines-windows-sap-planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f "Utilisation du démarrage automatique pour les instances SAP"
[planning-guide-2.1]: virtual-machines-windows-sap-planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 "Cloud uniquement : déploiement de machines virtuelles dans Azure sans dépendances du réseau local du client"
[planning-guide-2.2]: virtual-machines-windows-sap-planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 "Intersite : déploiement d’une ou plusieurs machines virtuelles SAP dans Azure, dans lequel une intégration complète au réseau local est nécessaire"
[planning-guide-3.1]: virtual-machines-windows-sap-planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a "Régions Azure"
[planning-guide-3.2.1]: virtual-machines-windows-sap-planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 "Domaines d'erreur"
[planning-guide-3.2.2]: virtual-machines-windows-sap-planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 "Domaines de mise à niveau"
[planning-guide-3.2.3]: virtual-machines-windows-sap-planning-guide.md#18810088-f9be-4c97-958a-27996255c665 "Groupes à haute disponibilité Azure"
[planning-guide-3.2]: virtual-machines-windows-sap-planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 "Concept de la machine virtuelle Microsoft Azure"
[planning-guide-3.3.2]: virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 "Stockage Premium Azure"
[planning-guide-5.1.1]: virtual-machines-windows-sap-planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 "Déplacement d’une machine virtuelle locale vers Azure avec un disque non généralisé"
[planning-guide-5.1.2]: virtual-machines-windows-sap-planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c "Déploiement d’une machine virtuelle avec une image spécifique du client"
[planning-guide-5.2.1]: virtual-machines-windows-sap-planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef "Préparation du déplacement d’une machine virtuelle locale vers Azure avec un disque non généralisé"
[planning-guide-5.2.2]: virtual-machines-windows-sap-planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 "Préparation du déploiement d’une machine virtuelle avec une image spécifique du client pour SAP"
[planning-guide-5.2]: virtual-machines-windows-sap-planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 "Préparation de machines virtuelles avec SAP pour Azure"
[planning-guide-5.3.1]: virtual-machines-windows-sap-planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 "Différence entre un disque Azure et une image Azure"
[planning-guide-5.3.2]: virtual-machines-windows-sap-planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a "Chargement d’un disque dur virtuel local vers Azure"
[planning-guide-5.4.2]: virtual-machines-windows-sap-planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 "Copie de disques entre comptes Azure Storage"
[planning-guide-5.5.1]: virtual-machines-windows-sap-planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 "Structure de machine virtuelle/disque dur virtuel pour les déploiements SAP"
[planning-guide-5.5.3]: virtual-machines-windows-sap-planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d "Paramétrage du montage automatique pour les disques attachés"
[planning-guide-7.1]: virtual-machines-windows-sap-planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 "Machine virtuelle unique avec scénario de démonstration/formation SAP NetWeaver"
[planning-guide-7]: virtual-machines-windows-sap-planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 "Concepts de déploiement cloud uniquement d’instances SAP"
[planning-guide-9.1]: virtual-machines-windows-sap-planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 "Solution de surveillance Azure pour SAP"
[planning-guide-azure-premium-storage]: virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 "Stockage Premium Azure"

[planning-guide-figure-100]: ./media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]: ./media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]: ./media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]: ./media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]: ./media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]: ./media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]: ./media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]: ./media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]: ./media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]: ./media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]: ./media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]: ./media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]: ./media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]: ./media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]: ./media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]: ./media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]: ./media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]: ./media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]: ./media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]: ./media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]: ./media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]: ./media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]: ./media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]: virtual-machines-windows-sap-planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd "Mise en réseau Microsoft Azure"
[planning-guide-storage-microsoft-azure-storage-and-data-disks]: virtual-machines-windows-sap-planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f "Stockage : Microsoft Azure Storage et disques de données"

[sap-ha-guide]: virtual-machines-windows-sap-high-availability-guide.md "SAP NetWeaver sur machines virtuelles Windows – Guide de haute disponibilité"
[sap-ha-guide-1]: virtual-machines-windows-sap-high-availability-guide.md#217c5479-5595-4cd8-870d-15ab00d4f84c "Composants requis"
[sap-ha-guide-2]: virtual-machines-windows-sap-high-availability-guide.md#42b8f600-7ba3-4606-b8a5-53c4f026da08 "Ressources"
[sap-ha-guide-3]: virtual-machines-windows-sap-high-availability-guide.md#42156640c6-01cf-45a9-b225-4baa678b24f1 "Différences dans SAP à haute disponibilité entre les modèles de déploiement Azure Resource Manager et Classic"
[sap-ha-guide-3.1]: virtual-machines-windows-sap-high-availability-guide.md#f76af273-1993-4d83-b12d-65deeae23686 "Groupes de ressources"
[sap-ha-guide-3.2]: virtual-machines-windows-sap-high-availability-guide.md#3e85fbe0-84b1-4892-87af-d9b65ff91860 "Clustering avec Azure Resource Manager par comparaison avec le modèle de déploiement Classic"
[sap-ha-guide-4]: virtual-machines-windows-sap-high-availability-guide.md#8ecf3ba0-67c0-4495-9c14-feec1a2255b7 "Clustering de basculement Windows Server (WSFC)"
[sap-ha-guide-4.1]: virtual-machines-windows-sap-high-availability-guide.md#1a3c5408-b168-46d6-99f5-4219ad1b1ff2 "Modes de quorum"
[sap-ha-guide-5]: virtual-machines-windows-sap-high-availability-guide.md#fdfee875-6e66-483a-a343-14bbaee33275 "Cluster de basculement Windows local"
[sap-ha-guide-5.1]: virtual-machines-windows-sap-high-availability-guide.md#be21cf3e-fb01-402b-9955-54fbecf66592 "Stockage partagé"
[sap-ha-guide-5.2]: virtual-machines-windows-sap-high-availability-guide.md#ff7a9a06-2bc5-4b20-860a-46cdb44669cd "Mise en réseau / résolution de noms"
[sap-ha-guide-6]: virtual-machines-windows-sap-high-availability-guide.md#2ddba413-a7f5-4e4e-9a51-87908879c10a "Cluster de basculement Windows avec Microsoft Azure"
[sap-ha-guide-6.1]: virtual-machines-windows-sap-high-availability-guide.md#1a464091-922b-48d7-9d08-7cecf757f341 "Disque partagé sur Microsoft Azure avec SIOS DataKeeper"
[sap-ha-guide-6.2]: virtual-machines-windows-sap-high-availability-guide.md#44641e18-a94e-431f-95ff-303ab65e0bcb "Résolution de noms sur Microsoft Azure"
[sap-ha-guide-7]: virtual-machines-windows-sap-high-availability-guide.md#2e3fec50-241e-441b-8708-0b1864f66dfa "SAP NetWeaver à haute disponibilité sur Azure IaaS"
[sap-ha-guide-7.1]: virtual-machines-windows-sap-high-availability-guide.md#93faa747-907e-440a-b00a-1ae0a89b1c0e "Haute disponibilité pour les serveurs d’applications SAP"
[sap-ha-guide-7.2]: virtual-machines-windows-sap-high-availability-guide.md#f559c285-ee68-4eec-add1-f60fe7b978db "Haute disponibilité pour les instances SAP (A)SCS"
[sap-ha-guide-7.2.1]: virtual-machines-windows-sap-high-availability-guide.md#b5b1fd0b-1db4-4d49-9162-de07a0132a51 "Haute disponibilité pour l’instance SAP (A)SCS avec le cluster de basculement Windows dans Azure"
[sap-ha-guide-7.3]: virtual-machines-windows-sap-high-availability-guide.md#ddd878a0-9c2f-4b8e-8968-26ce60be1027 "Haute disponibilité pour l’instance SGBD"
[sap-ha-guide-7.4]: virtual-machines-windows-sap-high-availability-guide.md#045252ed-0277-4fc8-8f46-c5a29694a816 "Scénarios possibles de déploiement de bout en bout à haute disponibilité"
[sap-ha-guide-8]: virtual-machines-windows-sap-high-availability-guide.md#78092dbe-165b-454c-92f5-4972bdbef9bf "Préparation de l’infrastructure"
[sap-ha-guide-8.1]: virtual-machines-windows-sap-high-availability-guide.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489 "Déployer des machines virtuelles avec une connectivité réseau d’entreprise (intersite) pour une utilisation en production"
[sap-ha-guide-8.2]: virtual-machines-windows-sap-high-availability-guide.md#7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310 "Déploiement cloud uniquement d’instances SAP à des fins de test/démo"
[sap-ha-guide-8.3]: virtual-machines-windows-sap-high-availability-guide.md#47d5300a-a830-41d4-83dd-1a0d1ffdbe6a "Réseau virtuel Azure"
[sap-ha-guide-8.4]: virtual-machines-windows-sap-high-availability-guide.md#b22d7b3b-4343-40ff-a319-097e13f62f9e "Adresses IP DNS"
[sap-ha-guide-8.5]: virtual-machines-windows-sap-high-availability-guide.md#9fbd43c0-5850-4965-9726-2a921d85d73f "Noms d’hôtes et adresses IP statiques de l’instance en cluster SAP ASCS/SCS et l’instance en cluster SGBD"
[sap-ha-guide-8.6]: virtual-machines-windows-sap-high-availability-guide.md#84c019fe-8c58-4dac-9e54-173efd4b2c30 "Configurer des adresses IP statiques pour les machines virtuelles SAP"
[sap-ha-guide-8.7]: virtual-machines-windows-sap-high-availability-guide.md#7a8f3e9b-0624-4051-9e41-b73fff816a9e "Configurer l’adresse IP de l’équilibrage de charge interne (ILB)"
[sap-ha-guide-8.8]: virtual-machines-windows-sap-high-availability-guide.md#f19bd997-154d-4583-a46e-7f5a69d0153c "Règles d’équilibrage de charge ASCS/SCS par défaut pour l’équilibrage de charge interne (ILB) Azure"
[sap-ha-guide-8.9]: virtual-machines-windows-sap-high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716 "Modification des règles d’équilibrage de charge ASCS/SCS par défaut pour l’équilibrage de charge interne (ILB) Azure"
[sap-ha-guide-8.10]: virtual-machines-windows-sap-high-availability-guide.md#e69e9a34-4601-47a3-a41c-d2e11c626c0c "Ajouter des ordinateurs Windows au domaine"
[sap-ha-guide-8.11]: virtual-machines-windows-sap-high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158 "Ajouter des entrées de Registre aux deux nœuds de cluster utilisés pour l’instance SAP ASCS/SCS"
[sap-ha-guide-8.12]: virtual-machines-windows-sap-high-availability-guide.md#0d67f090-7928-43e0-8772-5ccbf8f59aab "Configuration du cluster de basculement Windows Server pour l’instance SAP ASCS/SCS"
[sap-ha-guide-8.12.1]: virtual-machines-windows-sap-high-availability-guide.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79 "Collecter les nœuds de cluster dans la configuration du cluster"
[sap-ha-guide-8.12.2]: virtual-machines-windows-sap-high-availability-guide.md#e49a4529-50c9-4dcf-bde7-15a0c21d21ca "Configurer un partage de fichiers en cluster témoin"
[sap-ha-guide-8.12.2.1]: virtual-machines-windows-sap-high-availability-guide.md#06260b30-d697-4c4d-b1c9-d22c0bd64855 "Créer un partage de fichiers"
[sap-ha-guide-8.12.2.2]: virtual-machines-windows-sap-high-availability-guide.md#4c08c387-78a0-46b1-9d27-b497b08cac3d "Configurer le quorum du partage de fichiers témoin dans le gestionnaire du cluster de basculement"
[sap-ha-guide-8.12.3]: virtual-machines-windows-sap-high-availability-guide.md#5c8e5482-841e-45e1-a89d-a05c0907c868 "Installation de SIOS DataKeeper Cluster Edition pour le disque de partage en cluster SAP ASCS/SCS"
[sap-ha-guide-8.12.3.1]: virtual-machines-windows-sap-high-availability-guide.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3 "Ajouter la fonctionnalité Microsoft .NET Framework 3.5"
[sap-ha-guide-8.12.3.2]: virtual-machines-windows-sap-high-availability-guide.md#dd41d5a2-8083-415b-9878-839652812102 "Installation de SIOS DataKeeper"
[sap-ha-guide-8.12.3.3]: virtual-machines-windows-sap-high-availability-guide.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006 "Configurer SIOS DataKeeper"
[sap-ha-guide-9]: virtual-machines-windows-sap-high-availability-guide.md#a06f0b49-8a7a-42bf-8b0d-c12026c5746b "Installation de système SAP NetWeaver"
[sap-ha-guide-9.1]: virtual-machines-windows-sap-high-availability-guide.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170 "Installation de SAP avec une instance ASCS/SCS hautement disponible"
[sap-ha-guide-9.1.1]: virtual-machines-windows-sap-high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097 "Créer un nom d’hôte virtuel pour SAP ASCS/SCS en cluster"
[sap-ha-guide-9.1.2]: virtual-machines-windows-sap-high-availability-guide.md#eb5af918-b42f-4803-bb50-eff41f84b0b0 "Installer le premier nœud de cluster SAP"
[sap-ha-guide-9.1.3]: virtual-machines-windows-sap-high-availability-guide.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556 "Modifier le profil SAP de l’instance ASCS/SCS"
[sap-ha-guide-9.1.4]: virtual-machines-windows-sap-high-availability-guide.md#10822f4f-32e7-4871-b63a-9b86c76ce761 "Ajouter un port de sondage"
[sap-ha-guide-9.2]: virtual-machines-windows-sap-high-availability-guide.md#85d78414-b21d-4097-92b6-34d8bcb724b7 "Installation de l’instance de base de données"
[sap-ha-guide-9.3]: virtual-machines-windows-sap-high-availability-guide.md#8a276e16-f507-4071-b829-cdc0a4d36748 "Installation du deuxième nœud de cluster"
[sap-ha-guide-9.4]: virtual-machines-windows-sap-high-availability-guide.md#094bc895-31d4-4471-91cc-1513b64e406a "Modifier le type de démarrage de service Windows de l’instance SAP ERS"
[sap-ha-guide-9.5]: virtual-machines-windows-sap-high-availability-guide.md#2477e58f-c5a7-4a5d-9ae3-7b91022cafb5 "Installation du serveur d’applications principal SAP (PAS)"
[sap-ha-guide-9.6]: virtual-machines-windows-sap-high-availability-guide.md#0ba4a6c1-cc37-4bcf-a8dc-025de4263772 "Installation du serveur d’applications supplémentaire SAP (AAS)"
[sap-ha-guide-10]: virtual-machines-windows-sap-high-availability-guide.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9 "Test de basculement d’instance SAP ASCS/SCS et de réplication SIOS"
[sap-ha-guide-10.1]: virtual-machines-windows-sap-high-availability-guide.md#65fdef0f-9f94-41f9-b314-ea45bbfea445 "Point de départ : l’instance SAP ASCS/SCS s’exécute sur le nœud A du cluster"
[sap-ha-guide-10.2]: virtual-machines-windows-sap-high-availability-guide.md#5e959fa9-8fcd-49e5-a12c-37f6ba07b916 "Processus de basculement du nœud A vers le nœud B"
[sap-ha-guide-10.3]: virtual-machines-windows-sap-high-availability-guide.md#755a6b93-0099-4533-9f6d-5c9a613878b5 "Résultat final : l’instance SAP ASCS/SCS s’exécute sur le nœud B du cluster"


[sap-ha-guide-figure-1000]: ./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]: ./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]: ./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]: ./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]: ./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]: ./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]: ./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-3000]: ./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]: ./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]: ./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]: ./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]: ./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]: ./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]: ./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]: ./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]: ./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]: ./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]: ./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]: ./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]: ./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]: ./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]: ./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]: ./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]: ./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]: ./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]: ./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]: ./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]: ./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]: ./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]: ./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]: ./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]: ./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]: ./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]: ./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]: ./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]: ./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]: ./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]: ./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]: ./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]: ./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]: ./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]: ./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]: ./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]: ./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]: ./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]: ./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]: ./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]: ./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]: ./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]: ./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]: ./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]: ./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]: ./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]: ./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]: ./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]: ./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]: ./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]: ./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]: ./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]: ./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]: ./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]: ./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png


[powershell-install-configure]: ../powershell-install-configure.md
[resource-group-authoring-templates]: ../resource-group-authoring-templates.md
[resource-group-overview]: ../resource-group-overview.md
[resource-groups-networking]: ../virtual-network/resource-groups-networking.md
[sap-pam]: https://support.sap.com/pam "Tableau de disponibilité des produits SAP"
[sap-templates-2-tier-marketplace-image]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]: ../storage/storage-azure-cli.md
[storage-azure-cli-copy-blobs]: ../storage/storage-azure-cli.md#copy-blobs
[storage-introduction]: ../storage/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]: ../storage/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]: ../storage/storage-premium-storage.md
[storage-redundancy]: ../storage/storage-redundancy.md
[storage-scalability-targets]: ../storage/storage-scalability-targets.md
[storage-use-azcopy]: ../storage/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]: virtual-machines-linux-attach-disk-portal.md
[virtual-machines-windows-attach-disk-portal]: virtual-machines-windows-attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]: ../resource-manager-deployment-model.md
[virtual-machines-azure-resource-manager-architecture-benefits-arm]: ../resource-manager-deployment-model.md#benefits-of-using-resource-manager-and-resource-groups
[virtual-machines-azurerm-versus-azuresm]: virtual-machines-windows-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]: virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]: virtual-machines-linux-cli-deploy-templates.md "Déploiement et gestion de machines virtuelles à l’aide des modèles Azure Resource Manager et de l’interface de ligne de commande Azure"
[virtual-machines-deploy-rmtemplates-powershell]: virtual-machines-windows-ps-manage.md "Gestion des machines virtuelles à l’aide de modèles Azure Resource Manager et de PowerShell"
[virtual-machines-linux-agent-user-guide]: virtual-machines-linux-agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]: virtual-machines-linux-agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]: virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-capture]: virtual-machines-linux-capture-image.md#capture-the-vm
[virtual-machines-windows-capture-image]: virtual-machines-windows-capture-image.md
[virtual-machines-windows-capture-image-capture]: virtual-machines-windows-capture-image.md#capture-the-vm
[virtual-machines-linux-configure-lvm]: virtual-machines-linux-configure-lvm.md
[virtual-machines-linux-configure-raid]: virtual-machines-linux-configure-raid.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]: virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]: virtual-machines-linux-suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]: virtual-machines-linux-redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]: virtual-machines-linux-add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]: virtual-machines-linux-add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]: virtual-machines-linux-quick-create-cli.md
[virtual-machines-linux-update-agent]: virtual-machines-linux-update-agent.md
[virtual-machines-manage-availability]: virtual-machines-windows-manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]: virtual-machines-windows-ps-create.md
[virtual-machines-sizes]: virtual-machines-windows-sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]: virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]: virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]: virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]: virtual-machines-windows-portal-sql-alwayson-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]: virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]: virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]: virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]: virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]: virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]: https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]: ../virtual-network/virtual-network-deploy-multinic-arm-cli.md
[virtual-network-deploy-multinic-arm-ps]: ../virtual-network/virtual-network-deploy-multinic-arm-ps.md
[virtual-network-deploy-multinic-arm-template]: ../virtual-network/virtual-network-deploy-multinic-arm-template.md
[virtual-networks-configure-vnet-to-vnet-connection]: ../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]: ../virtual-network/virtual-networks-create-vnet-arm-pportal.md
[virtual-networks-manage-dns-in-vnet]: ../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]: ../virtual-network/virtual-networks-multiple-nics.md
[virtual-networks-nsg]: ../virtual-network/virtual-networks-nsg.md
[virtual-networks-reserved-private-ip]: ../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]: ../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]: ../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]: ../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]: ../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]: ../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md
[vpn-gateway-vpn-faq]: ../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]: ../xplat-cli-install.md
[xplat-cli-azure-resource-manager]: ../xplat-cli-azure-resource-manager.md
 

Microsoft Azure permet aux entreprises d’acquérir des ressources de calcul, de stockage et de réseau rapidement, sans longs cycles d’acquisition. Les Machines Virtuelles Azure permettent aux entreprises de déployer dans Azure des applications classiques, comme les applications basées sur SAP NetWeaver (ABAP, Java et pile ABAP+Java), et d’étendre leur fiabilité et leur disponibilité même en l’absence des ressources supplémentaires disponibles en local. Les Machines Virtuelles Azure prennent également en charge la connectivité intersite, ce qui permet aux entreprises d’intégrer de manière active les Machines Virtuelles Azure dans leurs domaines locaux, leurs clouds privés et leur paysage SAP.


Ce document détaille toutes les étapes nécessaires pour déployer des systèmes SAP hautement disponibles dans Azure suivant notre nouvelle méthode avec le nouveau modèle de déploiement Azure Resource Manager. Ce guide vous guidera tout au long des étapes principales :


- Recherche des notes et des guides d’installation de SAP adaptés, listés plus bas dans la section intitulée [Ressources][sap-ha-guide-2].  
  Ce document vient compléter la documentation sur l’installation SAP et des notes SAP, qui représentent les ressources incontournables en matière d’installation et de déploiement de logiciels SAP sur des plateformes données.

- Présentation de la différence entre le modèle de déploiement Azure Classic actuel et le nouveau modèle de déploiement Azure Resource Manager.

- Présentation des modes de quorum WSFC (Windows Server Failover Cluster), pour que vous puissiez sélectionner le modèle adapté à votre déploiement Azure

- Présentation du stockage partagé WSFC (Windows Server Failover Cluster) dans Azure

- Présentation des possibilités de protection des composants de point unique de défaillance de SAP, comme SAP ASCS/SCS et SGBD, et des composants redondants, comme les serveurs d’applications SAP, dans Azure

- Approche pas à pas de l’installation et de la configuration d’un système SAP à haute disponibilité dans un cluster de basculement Windows (WSFC) avec la plateforme Microsoft Azure et du nouveau modèle Azure Resource Manager.

- Étapes supplémentaires requises pour le cluster WSFC dans Azure mais non nécessaires dans le cas de déploiements locaux


Pour simplifier le déploiement et la configuration, nous utilisons le nouveau modèle Azure Resource Manager de niveau 3 SAP, qui automatise le déploiement de la totalité de l’infrastructure requise pour le système SAP à haute disponibilité et qui prend en charge le dimensionnement souhaité de votre système SAP.

[AZURE.INCLUDE [windows-warning](../../includes/virtual-machines-linux-sap-warning.md)]

##  <a name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a> Conditions préalables

Avant de commencer, assurez-vous que les conditions préalables décrites dans les chapitres suivants sont remplies et vérifiez toutes les ressources listées dans le chapitre des ressources.

Nous utilisons des modèles Azure Resource Manager pour SAP NetWeaver de niveau 3 : [https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image/)

Une vue d’ensemble des modèles Azure Resource Manager SAP est présentée ici : [https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/)


##  <a name="42b8f600-7ba3-4606-b8a5-53c4f026da08"> </a> Ressources

Les déploiements SAP dans Azure sont décrits dans les guides supplémentaires suivants :

- [SAP NetWeaver sur machines virtuelles Windows – Guide de planification et d’implémentation][planning-guide]
- [SAP NetWeaver sur machines virtuelles Windows – Guide de déploiement][deployment-guide]
- [SAP NetWeaver sur machines virtuelles Windows – Guide de déploiement SGBD][dbms-guide]
- [SAP NetWeaver sur machines virtuelles Windows – Guide de haute disponibilité (ce guide)][sap-ha-guide]


> [AZURE.NOTE] Autant que possible, un lien pointant vers le guide d’installation de SAP concerné est utilisé (consultez [Guides d’installation de SAP][sap-installation-guides]). Lorsqu’il s’agit de la configuration requise et du processus d’installation, les guides d’installation de SAP NetWeaver doivent toujours être lus avec attention, car ce document traite uniquement des tâches spécifiques des systèmes basés sur SAP NetWeaver installés dans une machine virtuelle Microsoft Azure.

Les notes SAP suivantes sont associées à la rubrique SAP sur Azure :


| Numéro de la note | Intitulé                                                    
| ------------- |----------------------------------------------------------
| [1928533] | Applications SAP sur Azure : dimensionnement et produits pris en charge 
| [2015553] | SAP sur Microsoft Azure : configuration requise         
| [1999351] | Surveillance Azure améliorée pour SAP                        
| [2178632] | Métriques de surveillance clés pour SAP sur Microsoft Azure        
| [1999351] | Virtualisation sur Windows : surveillance améliorée           


Les limitations générales par défaut et les limitations maximales des abonnements Azure sont exposées dans [cet article][azure-subscription-service-limits-subscription].

##  <a name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a> Différences dans SAP à haute disponibilité entre les modèles de déploiement Azure Resource Manager et Classic 

> [AZURE.NOTE] Le modèle de déploiement Classic est également appelé modèle Azure Service Management (ASM).

### <a name="f76af273-1993-4d83-b12d-65deeae23686"> </a> Groupes de ressources
Les groupes de ressources sont un nouveau concept qui réunit toutes les ressources ayant le même cycle de vie, par exemple ayant été créées et supprimées en même temps. Lisez cet article pour plus d’informations sur les groupes de ressources.

### <a name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a> Clustering avec Azure Resource Manager par comparaison avec le modèle de déploiement Classic 

Le nouveau modèle Azure Resource Manager offre les évolutions suivantes par rapport au modèle de déploiement Classic pour la haute disponibilité :

- Il n’est pas nécessaire d’avoir un service cloud pour utiliser un équilibrage de charge interne (ILB) Azure

Si vous souhaitez toujours utiliser l’ancien modèle Azure Classic, vous devez suivre la procédure décrite dans le document [SAP NetWeaver sur Azure - Clustering d’instances SAP ASCS/SCS à l’aide du cluster de basculement Windows Server sur Azure avec SIOS DataKeeper](http://go.microsoft.com/fwlink/?LinkId=613056).

> [AZURE.NOTE] Il est fortement recommandé d’utiliser le nouveau modèle de déploiement Azure Resource Manager pour vos installations SAP, car il offre plusieurs avantages par rapport au modèle de déploiement Classic. Des informations supplémentaires sont disponibles dans [cet article][virtual-machines-azure-resource-manager-architecture-benefits-arm].


## <a name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a> Clustering de basculement Windows Server (WSFC) 

Un cluster WSFC Microsoft constitue la base technique d’une installation de SGBD et de SAP ASCS/SCS hautement disponible sous Windows.

Un cluster de basculement est un groupe de 1 + n serveurs indépendants (nœuds) qui fonctionnent ensemble pour accroître la disponibilité des applications et des services. En cas d’échec d’un ou plusieurs nœud, le cluster WSFC doit déterminer le nombre d’échecs qui peuvent se produire tout en maintenant l’intégrité du cluster afin d’être en mesure de fournir les applications et/ou les services définis. Différents modes de quorum sont disponibles pour y parvenir.
 

### <a name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a> Modes de quorum

Avec WSFC, quatre modes de quorum différents sont disponibles :

- **Nœud majoritaire :** chaque nœud peut voter. Le cluster fonctionne uniquement avec la majorité des voix, autrement dit plus de la moitié. Cette option est recommandée dans le cas d’un nombre impair de nœuds. Par exemple : même si 3 nœuds d’un cluster à 7 nœud échouent, le cluster conserve la majorité et continue à fonctionner.

- **Nœud et disque majoritaires :** chaque nœud et un disque désigné dans l’espace de stockage en cluster (le « disque témoin ») peuvent voter lorsqu’ils sont disponibles et en communication. Le cluster fonctionne uniquement avec la majorité des voix, autrement dit plus de la moitié. Ce mode est pertinent dans un environnement de cluster comprenant un nombre pair de nœuds. Tant que la moitié des nœuds ainsi que le disque sont en ligne, le cluster conserve son état d’intégrité.

- **Nœud et partage de fichiers majoritaires :** chaque nœud et un partage de fichiers désigné créé par l’administrateur (le partage de fichiers témoin) peuvent voter lorsqu’ils sont disponibles et en communication. Le cluster fonctionne uniquement avec la majorité des voix, autrement dit plus de la moitié. Ce mode est pertinent dans un environnement de cluster comprenant un nombre pair de nœuds ; il est similaire au mode nœud et disque majoritaires, à la différence qu’il utilise un partage de fichiers témoin au lieu d’un disque témoin. Il est facile à implémenter, mais si le partage de fichiers n’est lui-même pas hautement disponible, il risque de devenir un point de défaillance unique.

- **Pas de majorité : disque uniquement :** le cluster possède un quorum si un nœud est disponible et en communication avec un disque spécifique dans l’espace de stockage en cluster. Seuls les nœuds qui sont également en communication avec ce disque peuvent rejoindre le cluster. Ce mode n’est pas recommandé.  

## <a name="fdfee875-6e66-483a-a343-14bbaee33275"></a> Cluster de basculement Windows local

Dans cet exemple, nous avons un cluster composé de deux nœuds. Si la connexion réseau entre les nœuds échoue alors que les deux nœuds restent en cours d’exécution, il est nécessaire de préciser quel nœud est censé fournir les applications et les services du cluster. C’est le rôle que joue un partage de fichiers ou un disque quorum. Le nœud qui a accès au partage de fichiers ou au disque quorum est celui qui doit garantir l’accessibilité des services.

Dans cet exemple, nous utilisons un cluster à deux nœuds. Pour cette raison, nous avons choisi le mode de quorum nœud et partage de fichiers. Le mode nœud et disque majoritaires est également une option valide. Dans un environnement de production, il est recommandé d’utiliser un disque quorum à la place et d’utiliser une technologie de système de stockage et de réseau pour le rendre hautement disponible.

![Figure 1 : Configuration proposée du cluster de basculement Windows Server pour SAP ASCS/SCS sur Azure][sap-ha-guide-figure-1000]

_**Figure 1 :** Configuration proposée du cluster de basculement Windows Server pour SAP ASCS/SCS sur Azure_

### <a name="be21cf3e-fb01-402b-9955-54fbecf66592"> </a> Stockage partagé

La figure ci-dessus illustre un cluster de stockage partagé à deux nœuds. Dans un cluster de stockage partagé local, le stockage partagé est visible par tous les nœuds du cluster. Un mécanisme de verrouillage protège les données contre la corruption. En outre, tous les nœuds peuvent détecter si un autre nœud échoue. Si un nœud échoue, le nœud restant s’approprie les ressources de stockage et garantit la disponibilité des services.

> [AZURE.NOTE] Avec certains SGBD, comme SQL Server, les disques partagés ne sont pas nécessaires pour atteindre une haute disponibilité. SQL Server AlwaysOn effectue la réplication des données et des fichiers de journaux du SGBD à partir du disque local d’un nœud du cluster vers le disque local d’un autre nœud du cluster. Par conséquent, la configuration du cluster Windows ne nécessite pas de disque partagé.

### <a name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a> Mise en réseau / résolution de noms

Le cluster est accessible au moyen d’une adresse IP virtuelle et d’un nom d’hôte virtuel fourni par le serveur DNS. Les nœuds sur site et le serveur DNS peuvent gérer plusieurs adresses IP.

Dans une installation par défaut, deux ou plusieurs connexions réseau sont utilisées :

- une connexion dédiée au stockage ;
- une connexion de réseau interne au cluster pour la pulsation ; et
- un réseau public utilisé par les clients pour se connecter au cluster.



## <a name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a> Cluster de basculement Windows avec Microsoft Azure

Par rapport aux déploiements complets ou de cloud privé, les Machines virtuelles Microsoft Azure requièrent des étapes supplémentaires pour configurer un cluster WSFC. Pour créer un disque de cluster partagé, plusieurs adresses IP et noms d’hôtes virtuels sont requis pour l’instance SAP ASCS/SCS.

Ci-dessous, nous expliquons les concepts supplémentaires et les autres étapes requises lors de la création d’un cluster de services centraux SAP à haute disponibilité sur Microsoft Azure. Les étapes indiquent comment configurer l’outil tiers SIOS DataKeeper et l’équilibrage de charge interne Azure. Ces outils nous donneront la possibilité de créer un cluster de basculement Windows avec un partage de fichiers témoin dans Microsoft Azure.


![Figure 2 : Schéma d’une configuration de cluster de basculement Windows Server dans Azure sans disque partagé][sap-ha-guide-figure-1001]

_**Figure 2 :** Schéma d’une configuration de cluster de basculement Windows Server dans Azure sans disque partagé_


### <a name="1a464091-922b-48d7-9d08-7cecf757f341"></a> Disque partagé sur Microsoft Azure avec SIOS DataKeeper

À partir de septembre 2016, Microsoft Azure ne fournit pas de stockage partagé pour créer un cluster de stockage partagé. Toutefois, le stockage partagé en cluster est nécessaire pour une instance SAP ASCS/SCS hautement disponible.

Le logiciel tiers SIOS DataKeeper Cluster Edition permet de créer un stockage en miroir qui simule le stockage partagé en cluster. La solution SIOS assure la réplication synchrone des données en temps réel. Voici la façon dont est créée une ressource de disque partagé pour un cluster :

- Association d’un disque dur virtuel Azure supplémentaire à chacune des machines virtuelles se trouvant dans une configuration de cluster Windows.
- Exécution de SIOS DataKeeper Cluster Edition sur les deux nœuds de machines virtuelles.
- Configuration de SIOS DataKeeper Cluster Edition de sorte qu’il mette en miroir le contenu du volume attaché au disque dur virtuel supplémentaire à partir des machines virtuelles sources vers le volume attaché au disque dur virtuel supplémentaire de la machine virtuelle cible. SIOS DataKeeper extrait les volumes locaux source et cible et les présente à un cluster de basculement Windows comme un seul disque partagé.

Pour plus d’informations sur le produit SIOS DataKeeper, consultez cette source : [http://us.sios.com/products/datakeeper-cluster/](http://us.sios.com/products/datakeeper-cluster/)

 ![Figure 2 : Schéma d’une configuration de cluster de basculement Windows Server dans Azure avec SIOS DataKeeper][sap-ha-guide-figure-1002]

_**Figure 3 :** Schéma d’une configuration de cluster de basculement Windows Server dans Azure avec SIOS DataKeeper_

> [AZURE.NOTE] Avec certains SGBD, comme SQL Server, les disques partagés ne sont pas nécessaires pour atteindre une haute disponibilité. SQL Server AlwaysOn effectue la réplication des données et des fichiers de journaux du SGBD à partir du disque local d’un nœud du cluster vers le disque local d’un autre nœud du cluster. Par conséquent, la configuration du cluster Windows ne nécessite pas de disque partagé.

### <a name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a> Résolution de noms sur Microsoft Azure

La plateforme cloud Microsoft Azure ne fournit pas la possibilité de configurer des adresses IP virtuelles, par exemple des adresses IP flottantes. Pour cette raison, vous avez besoin d’une autre solution pour configurer une adresse IP virtuelle afin d’atteindre la ressource de cluster dans le cloud. Azure fournit l’équilibrage de charge interne (ILB). Avec l’équilibrage de charge interne, le cluster est atteignable avec l’adresse IP virtuelle du cluster. Par conséquent, vous devez déployer l’équilibrage de charge interne dans le groupe de ressources qui contient les nœuds de cluster. Vous devez configurer toutes les règles de réacheminement de port nécessaires avec les ports de sondage de l’équilibrage de charge interne. Les clients peuvent se connecter avec le nom d’hôte virtuel. Le serveur DNS résout l’adresse IP du cluster et l’équilibrage de charge interne gère le réacheminement vers le nœud actif du cluster.

## <a name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a> SAP NetWeaver à haute disponibilité sur Azure IaaS

Comme l’indiquait précédemment le chapitre sur la haute disponibilité, [SAP NetWeaver sur machines virtuelles Azure, du guide de planification et d’implémentation de SAP NetWeaver sur machines virtuelles Windows][planning-guide-11], nous devons protéger les composants suivants pour bénéficier de la haute disponibilité de l’application SAP (par exemple, haute disponibilité des composants logiciels de SAP) :

- Serveurs d’application SharePoint
- Instance SAP ASCS/SCS
- Serveur SGBD

### <a name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a> Haute disponibilité pour les serveurs d’applications SAP

Pour les instances de dialogue/serveurs d’applications SAP, une solution à haute disponibilité spécifique n’est en général pas nécessaire. La haute disponibilité s’obtient par redondance ; par conséquent, plusieurs instances de dialogue sont configurées sur différentes machines virtuelles Azure. Vous devez avoir au moins deux instances d’applications SAP installées dans deux machines virtuelles Azure.

![Figure 4 : Serveurs d’applications SAP à haute disponibilité][sap-ha-guide-figure-2000]

_**Figure 4 :** Serveurs d’applications SAP à haute disponibilité_


Toutes les machines virtuelles qui hébergent des serveurs d’applications SAP doivent être placées dans le même **groupe à haute disponibilité Azure**. Le groupe à haute disponibilité Azure garantit que :

- Toutes les machines virtuelles font partie des mêmes domaines de mise à niveau, par exemple en évitant que les machines virtuelles soient mises à jour en même temps pendant le temps d’arrêt pour maintenance planifiée.
- Toutes les machines virtuelles font partie des mêmes domaines d’erreur, par exemple en faisant en sorte que les machines virtuelles soient déployées de façon à éviter la présence d’un point unique de défaillance susceptible d’affecter la disponibilité de toutes les machines virtuelles.

Pour plus d’informations, consultez l’article : [Gérer la disponibilité des machines virtuelles][virtual-machines-manage-availability].

Comme le compte de stockage Azure constitue un point unique de défaillance potentiel, il est important d’avoir au moins deux comptes de stockage Azure sur lesquels au moins deux machines virtuelles seront distribuées. Dans l’idéal, les machines virtuelles exécutant des instances de dialogue SAP doivent toutes être déployées sur différents compte de stockage.

### <a name="f559c285-ee68-4eec-add1-f60fe7b978db"></a> Haute disponibilité pour les instances SAP (A)SCS 

![Figure 5 : Instance SAP ASCS/SCS à haute disponibilité][sap-ha-guide-figure-2001]

_**Figure 5 :** Instance SAP ASCS/SCS à haute disponibilité_


#### <a name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a> Haute disponibilité pour l’instance SAP (A)SCS avec le cluster de basculement Windows dans Azure

Par rapport aux déploiements complets ou de cloud privé, les Machines virtuelles Microsoft Azure requièrent des étapes supplémentaires pour configurer un cluster WSFC. Pour créer un cluster de basculement Windows, un disque de cluster partagé, plusieurs adresses IP et noms d’hôtes virtuels et un équilibrage de charge interne (ILB) Azure sont requis pour le clustering de l’instance SAP ASCS/SCS.

Cette méthode est expliquée en détails plus loin dans le document.

![Figure 6 : Schéma de la configuration d’un cluster de basculement Windows Server pour SAP ASCS/SCS dans Azure avec SIOS DataKeeper][sap-ha-guide-figure-1002]

_**Figure 6 :** Schéma de la configuration d’un cluster de basculement Windows Server pour SAP ASCS/SCS dans Azure avec SIOS DataKeeper_


### <a name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a> Haute disponibilité pour l’instance SGBD

Le SGBD est également le SPOF d’un système SAP et doit être protégé à l’aide d’une solution à haute disponibilité. Vous trouverez ci-dessous un exemple de solution SQL Server AlwaysOn à haute disponibilité dans Azure utilisant le cluster de basculement Windows Server et l’équilibrage de charge interne Azure. SQL Server AlwaysOn réplique les fichiers journaux et les données SGBD à l’aide de sa propre réplication de SGBD. Par conséquent, nous n’avons pas besoin de disques partagés en cluster, ce qui simplifie tout le réglage.


![Figure 7 : Serveurs SGBD SAP à haute disponibilité – exemple de réglage SQL Server AlwaysOn à haute disponibilité][sap-ha-guide-figure-2003]

_**Figure 7 :** Serveurs SGBD SAP à haute disponibilité – exemple de réglage SQL Server AlwaysOn à haute disponibilité_


Ce document ne couvre pas le clustering du SGBD.

Pour plus d’informations sur le clustering de SQL Server dans Azure à l’aide du modèle de déploiement Azure Resource Manager, consultez les articles suivants :

- [Configuration manuelle d’un groupe de disponibilité Always On dans une machine virtuelle Azure à l’aide de Resource Manager][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]
- [Configurer un équilibrage de charge interne pour un groupe de disponibilité AlwaysOn dans Azure][virtual-machines-windows-portal-sql-alwayson-int-listener]

### <a name="045252ed-0277-4fc8-8f46-c5a29694a816"></a> Scénarios possibles de déploiement de bout en bout à haute disponibilité

Voici un exemple d’architecture complète SAP NetWeaver à haute disponibilité dans Azure, où nous utilisons un cluster dédié pour l’instance SAP ASCS/SCS et un autre pour le SGBD.

![Figure 8 : Modèle d’architecture SAP à haute disponibilité 1 – avec un cluster dédié à ASCS/SCS et un cluster dédié à l’instance SGBD][sap-ha-guide-figure-2004]

_**Figure 8 :** Modèle d’architecture SAP à haute disponibilité 1 – avec un cluster dédié à ASCS/SCS et un cluster dédié à l’instance SGBD_

## <a name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a> Préparation de l’infrastructure

Afin de simplifier le déploiement des ressources requises pour SAP, nous avons développé des modèles Azure Resource Manager pour SAP.

Ces modèles de couche 3 prennent également en charge les scénarios de haute disponibilité, par exemple :

- **Modèle architectural 1** avec deux clusters, chacun pour les points de défaillance uniques de SAP ASCS/SCS et SGBD

Les modèles Azure Resource Manager du scénario 1 sont disponibles ici :

- Image d’Azure Marketplace : [https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)
- Image personnalisée : [https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)

Lorsque vous cliquez sur l’image Marketplace de niveau 3 de SAP, vous obtenez l’interface utilisateur suivante sur le Portail Azure :

![Figure 9 : Spécification des paramètres Azure Resource Manager SAP à haute disponibilité][sap-ha-guide-figure-3000]

_**Figure 9 :** Spécification des paramètres Azure Resource Manager SAP à haute disponibilité_


Veillez à choisir **HA** pour l’option SYSTEMAVAILABILITY.

Les modèles créeront :

- Toutes les **machines virtuelles** nécessaires pour :
    - les machines virtuelles des serveurs d’applications SAP : `<SAPSystemSID>-di-<Number>`
    - les machines virtuelles de cluster ASCS/SCS : `<SAPSystemSID>-ascs-<Number>`
    - le cluster SGBD : `<SAPSystemSID>-db-<Number>`
- **Cartes réseau de toutes les machines virtuelles ayant une adresse IP associée**:
    - `<SAPSystemSID>-nic-di-<Number>`
    - `<SAPSystemSID>-nic-ascs-<Number>`
    - `<SAPSystemSID>-nic-db-<Number>`
- **Comptes Azure Storage**
- **Groupes de disponibilité** pour :
    - les machines virtuelles des serveurs d’applications SAP : `<SAPSystemSID>-avset-di`
    - les machines virtuelles de cluster SAP ASCS/SCS : `<SAPSystemSID>-avset-ascs`
    - Les machines virtuelles de cluster SGBD : `<SAPSystemSID>-avset-db`
- **Équilibrage de charge interne (ILB) Azure** avec tous les ports pour l’instance ASCS/SCS et l’adresse IP `<SAPSystemSID>-lb-ascs`
-	**Équilibrage de charge interne (ILB) Azure** avec tous les ports pour SQL Server SGBD et l’adresse IP `<SAPSystemSID>-lb-db`
- **Groupe de sécurité réseau**: `<SAPSystemSID>-nsg-ascs-0` avec port RDP externe ouvert vers la machine virtuelle `<SAPSystemSID>-ascs-0`


> [AZURE.NOTE]  TOUTES les adresses IP des cartes réseau et TOUS les équilibrages de charge Azure sont créés initialement comme étant **dynamiques**. Vous devez les transformer en adresses IP **statiques**, comme le décrit plus loin le document.


### <a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> Déployer des machines virtuelles avec une connectivité réseau d’entreprise (intersite) pour une utilisation en production

Pour les systèmes SAP en production, vous allez déployer des machines virtuelles Azure avec [connectivité de réseau d’entreprise (intersite)][planning-guide-2.2], à l’aide d’Azure de site à site (VPN) ou d’Azure ExpressRoute.

> [AZURE.NOTE]  Dans ce cas, votre réseau virtuel et sous-réseau Azure est déjà créé et préparé.


Dans le champ **NEWOREXISTINGSUBNET** choisissez Existing (Existant).

Dans le champ de texte **SUBNETID**, vous devez ajouter la chaîne complète du SubnetID de votre réseau Azure préparé, sur lequel vous envisagez de déployer vos machines virtuelles Azure.

Vous pouvez obtenir la liste de tous les sous-réseaux du réseau Azure à l’aide de cette commande PowerShell :

```powershell
(Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
```


Le **SUBNETID** s’affiche dans le champ Id.

La liste de tous les **SUBNETID** peut être récupérée à l’aide de la commande PowerShell suivante :

```PowerShell
(Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
```

Le **SUBNETID** ressemble à ceci :

```
/subscriptions/<SubscriptionId>/resourceGroups/<VPNName>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<SubnetName>
```

### <a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a>Déploiement cloud uniquement d’instances SAP à des fins de test/démo

Vous pouvez également déployer votre système SAP à haute disponibilité dans le modèle de déploiement appelé « cloud uniquement ».

Ce déploiement convient surtout pour le cas d’usage de type démo, et non pour les cas d’usage de type production.

Dans le champ NEWOREXISTINGSUBNET choisissez _**New (Nouveau)**_. Laissez le champ SUBNETID **vide**.

Le réseau virtuel et le sous-réseau Azure seront créés automatiquement par le modèle Azure Resource Manager SAP.

> [AZURE.NOTE] En outre, vous devez déployer au moins une machine virtuelle dédiée à AD/DNS dans le même réseau virtuel. Ces machines virtuelles ne sont pas créées par le modèle.


### <a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Réseau virtuel Azure

Dans notre exemple, l’espace d’adressage du réseau virtuel Azure est 10.0.0.0/16. Il existe un sous-réseau appelé _**Subnet (Sous-réseau)**_, avec la plage d’adresses 10.0.0.0/24. Toutes les machines virtuelles et tous les équilibrages de charge internes sont déployés dans ce réseau virtuel.
  
> [AZURE.NOTE] N’apportez pas de modifications aux paramètres réseau dans l’invité (comme l’adresse IP, les serveurs DNS, le sous-réseau, etc.). Tous les paramètres réseau sont effectués avec Azure et sont propagés par le biais du service DHCP.

### <a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a> Adresses IP DNS

Assurez-vous que l’option **Serveurs DNS** de votre réseau virtuel est définie sur **DNS personnalisé**. Dans le cas de :

- **[Connectivité de réseau d’entreprise (intersite)][planning-guide-2.2]** : ajoutez l’adresse IP des serveurs DNS locaux. Les serveurs DNS locaux peuvent être étendus aux machines virtuelles exécutées sur Azure. Dans ce cas, vous pouvez ajouter l’adresse IP de ces machines virtuelles Azure configurées pour exécuter le service DNS.

-	**[Déploiement cloud uniquement][planning-guide-2.1]**: déployez une machine virtuelle supplémentaire dans le même réseau virtuel, qui fera office de serveur(s) DNS. Ajoutez l’adresse IP de ces machines virtuelles Azure configurées pour exécuter le service DNS.


![Figure 10 : Configuration de serveurs DNS pour le réseau virtuel Azure][sap-ha-guide-figure-3001]

_**Figure 10 :** Configuration de serveurs DNS pour le réseau virtuel Azure_

> [AZURE.NOTE] Si vous modifiez l’adresse IP des serveurs DNS, vous devez redémarrer les machines virtuelles Azure afin d’appliquer la modification et de propager les nouveaux serveurs DNS. Dans notre exemple, le service DNS est installé et configuré sur les machines virtuelles Windows suivantes



| Rôle de la machine virtuelle | Nom d’hôte de la machine virtuelle | Nom de la carte réseau | Adresse IP statique  
| ---------------|-------------|--------------------|-------------------
| 1er serveur DNS | domcontr-0 | pr1-nic-domcontr-0 | 10\.0.0.10         
| 2e serveur DNS | domcontr-1 | pr1-nic-domcontr-1 | 10\.0.0.11         


### <a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> Noms d’hôtes et adresses IP statiques de l’instance en cluster SAP ASCS/SCS et l’instance en cluster SGBD

Comme en local, nous avons besoin des adresses IP / noms d’hôtes réservés suivants :

| Rôle de nom d’hôte virtuel | Nom d’hôte virtuel | Adresse IP statique virtuelle 
| ----------------------------------------------------------------------------|------------------|---------------------------
| Nom d’hôte virtuel du 1er cluster SAP ASCS/SCS (utilisé pour la gestion du cluster) | pr1-ascs-vir | 10\.0.0.42                 
| Nom d’hôte virtuel de **l’INSTANCE** SAP ASCS/SCS | pr1-ascs-sap | `10.0.0.43`             
| Nom d’hôte virtuel du 2e cluster SAP SGBD (utilisé pour la gestion du cluster) | pr1-dbms-vir | 10\.0.0.32                 
 

Les noms d’hôtes virtuels _**pr1-ascs-vir**_ et _**pr1-dbms-vir**_ et les adresses IP associées, qui sont utilisés pour gérer le cluster lui-même, sont créés lors de la création du cluster, comme le décrit le chapitre [Collecter les nœuds de cluster dans la configuration du cluster][sap-ha-guide-8.12.1].

Les deux autres noms d’hôtes virtuels _**pr1-ascs-sap**_ et _**pr1-dbms-sap**_ et les adresses IP associées, qui sont utilisés par l’instance SAP ASCS/SCS en cluster et l’instance SGBD en cluster, peuvent être créés manuellement sur le serveur DNS, comme le décrit le chapitre [Créer des noms d’hôtes virtuels pour SAP ASCS/SCS en cluster][sap-ha-guide-9.1.1].

### <a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a> Configurer des adresses IP statiques pour les machines virtuelles SAP

Après avoir déployé les machines virtuelles pour le clustering, nous devons configurer une adresse IP statique pour toutes les machines virtuelles. Cela ne peut pas être effectué dans le système d’exploitation invité, mais dans la configuration de réseau virtuel Azure.

Vous pouvez pour cela utiliser le Portail Azure. Dans le Portail Azure, accédez à :

```
<Resource Group> -> <Network Card> -> Settings -> IP Address
```

Modifiez le champ Affectation de **Dynamique** à **Statique**, et entrez **l’adresse IP** souhaitée.

> [AZURE.NOTE] Si vous modifiez l’adresse IP de la carte réseau, vous devez redémarrer les machines virtuelles Azure afin d’appliquer la modification.


![Figure 11 : Configuration d’une adresse IP statique pour la carte réseau de chaque machine virtuelle][sap-ha-guide-figure-3002]

_**Figure 11 :** Configuration d’une adresse IP statique pour la carte réseau de chaque machine virtuelle_

Répétez cette étape pour toutes les interfaces réseau, c’est-à-dire pour toutes les machines virtuelles, y compris celles que vous souhaitez utiliser pour le service AD/DNS.

Dans notre exemple, nous avons les machines virtuelles et les adresses IP statiques suivantes :

| Rôle de la machine virtuelle | Nom d’hôte de la machine virtuelle | Nom de la carte réseau | Adresse IP statique  
| ----------------------------------------|--------------|--------------------|-------------------
| 1er serveur d’applications SAP | pr1-di-0 | pr1-nic-di-0 | 10\.0.0.50         
| 2e serveur d’applications SAP | pr1-di-1 | pr1-nic-di-1 | 10\.0.0.51         
| ... | ... | ... | ...               
| Dernier serveur d’applications SAP | pr1-di-5 | pr1-nic-di-5 | 10\.0.0.55         
| 1er nœud de cluster pour l’instance ASCS/SCS | pr1-ascs-0 | pr1-nic-ascs-0 | 10\.0.0.40         
| 2e nœud de cluster pour l’instance ASCS/SCS | pr1-ascs-1 | pr1-nic-ascs-1 | 10\.0.0.41         
| 1er nœud de cluster pour l’instance SGBD | pr1-db-0 | pr1-nic-db-0 | 10\.0.0.30         
| 2e nœud de cluster pour l’instance SGBD | pr1-db-1 | pr1-nic-db-1 | 10\.0.0.31         

### <a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> Configurer l’adresse IP de l’équilibrage de charge interne (ILB)

Le modèle Azure Resource Manager SAP crée un équilibrage de charge interne (ILB) Azure utilisé pour le cluster de l’instance SAP ASCS/SCS et pour le cluster SGBD.

Le déploiement initial définit l’adresse IP de l’équilibrage de charge interne sur **Dynamique**. Il est important de définir l’adresse IP comme **Statique**.

Dans notre exemple, nous avons deux équilibrages de charge internes Azure avec les adresses IP statiques suivantes :

| Rôle de l’équilibrage de charge interne Azure | Nom de l’équilibrage de charge interne Azure | Adresse IP statique 
| ---------------------------|----------------|-------------------
| Équilibrage de charge interne de l’instance SAP ASCS/SCS | pr1-lb-ascs | `10.0.0.43`         
| Équilibrage de charge interne SAP SGBD | pr1-lb-dbms | 10\.0.0.33         


> [AZURE.NOTE]  
**Adresse IP du nom d’hôte virtuel de SAP ASCS/SCS = adresse IP d’Azure Load Balancer SAP ASCS/SCS pr1-lb-ascs** **Adresse IP du nom virtuel du SGBD = adresse IP d’Azure Load Balancer SGBD pr1-lb-dbms**

Dans notre exemple, définissez l’adresse IP de l’équilibrage de charge interne _pr1-lb-ascs_ comme l’adresse IP du nom d’hôte virtuel de l’instance SAP ASCS/SCS (`10.0.0.43`)

![Figure 12 : Configuration de l’adresse IP de l’équilibrage de charge interne (ILB) pour l’instance SAP ASCS/SCS][sap-ha-guide-figure-3003]

_**Figure 12 :** Configuration de l’adresse IP de l’équilibrage de charge interne (ILB) pour l’instance SAP ASCS/SCS_

De même, définissez l’adresse IP de l’équilibreur de charge _pr1-lb-dbms_ comme l’adresse IP du nom d’hôte virtuel de l’instance SGBD (dans notre exemple, 10.0.0.33).

### <a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Règles d’équilibrage de charge ASCS/SCS par défaut pour l’équilibrage de charge interne (ILB) Azure

Par défaut, le modèle Azure Resource Manager SAP crée tous les ports requis pour :

- L’instance ABAP ASCS avec le numéro d’instance par défaut **00**
- L’instance Java SCS avec le numéro d’instance par défaut **01**

Par conséquent, lors de l’installation de votre instance SAP ASCS/SCS, vous devez utiliser ces numéros d’instance par défaut de 00 et 01 pour votre instance ABAP ASCS et/ou Java SCS.

Les points de terminaison d’équilibrage de charge interne Azure suivants sont nécessaires et créés pour les ports SAP NetWeaver ABAP ASCS :


| Service / Nom de la règle d’équilibrage de charge | Numéros de ports par défaut | Ports concrets pour (instance ASCS avec numéro d’instance 00) (ERS avec 10)  
| ---------------------------------------------|-----------------------|--------------------------------------------------------------------------
| Serveur de file d’attente / _lbrule3200_ | 32`<InstanceNumber>` | 3200                                                                     
| Serveur de messages ABAP / _lbrule3600_ | 32`<InstanceNumber>` | 3600                                                                     
| Message ABAP interne / _lbrule3900_ | 39`<InstanceNumber>` | 3900                                                                     
| Serveur de messages HTTP / _Lbrule8100_ | 81`<InstanceNumber>` | 8100                                                                     
| Service de démarrage SAP ASCS HTTP / _Lbrule50013_ | 5`<InstanceNumber>`13 | 50013                                                                    
| Service de démarrage SAP ASCS HTTPS / _Lbrule50014_ | 5`<InstanceNumber>`14 | 50014                                                                    
| Réplication de la file d’attente / _Lbrule50016_ | 5`<InstanceNumber>`16 | 50016                                                                    
| Service de démarrage SAP ERS HTTP / _Lbrule51013_ | 5`<InstanceNumber>`13 | 51013                                                                    
| Service de démarrage SAP ERS HTTP / _Lbrule51014_ | 5`<InstanceNumber>`14 | 51014                                                                    
| WinRM _Lbrule5985_ | | 5985                                                                     
| Partage de fichiers _Lbrule445_ | | 445                                                                      

_**Tableau 1 :** Numéro de port des instances de SAP NetWeaver ABAP ASCS_


Les points de terminaison d’équilibrage de charge interne Azure suivants sont nécessaires et créés pour les ports SAP NetWeaver Java SCS :

| Service / Nom de la règle d’équilibrage de charge | Numéros de ports par défaut | Ports concrets pour (instance SCS avec numéro d’instance 01) (ERS avec 11)  
| ---------------------------------------------|-----------------------|--------------------------------------------------------------------------
| Serveur de file d’attente / _lbrule3201_ | 32`<InstanceNumber>` | 3201                                                                     
| Serveur de passerelle / _lbrule3301_ | 33`<InstanceNumber>` | 3301                                                                     
| Serveur de messages Java / _lbrule3900_ | 39`<InstanceNumber>` | 3901                                                                     
| Serveur de messages HTTP / _Lbrule8101_ | 81`<InstanceNumber>` | 8101                                                                     
| Service de démarrage SAP SCS HTTP / _Lbrule50113_ | 5`<InstanceNumber>`13 | 50113                                                                    
| Service de démarrage SAP SCS HTTPS / _Lbrule50114_ | 5`<InstanceNumber>`14 | 50114                                                                    
| Réplication de la file d’attente / _Lbrule50116_ | 5`<InstanceNumber>`16 | 50116                                                                    
| Service de démarrage SAP ERS HTTP / _Lbrule51113_ | 5`<InstanceNumber>`13 | 51113                                                                    
| Service de démarrage SAP ERS HTTP / _Lbrule51114_ | 5`<InstanceNumber>`14 | 51114                                                                    
| WinRM _Lbrule5985_ | | 5985                                                                     
| Partage de fichiers _Lbrule445_ | | 445                                                                      

_**Tableau 2 :** Numéro de port des instances de SAP NetWeaver Java SCS_


![Figure 13 : Règles d’équilibrage de charge ASCS/SCS par défaut pour l’équilibrage de charge interne (ILB) Azure][sap-ha-guide-figure-3004]

_**Figure 13 :** Règles d’équilibrage de charge ASCS/SCS par défaut pour l’équilibrage de charge interne (ILB) Azure_

De même, définissez l’adresse IP de l’équilibreur de charge _**pr1-lb-dbms**_ comme l’adresse IP du nom d’hôte virtuel de l’instance SGBD (dans notre exemple, _**10.0.0.33**_).

### <a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Modification des règles d’équilibrage de charge ASCS/SCS par défaut pour l’équilibrage de charge interne (ILB) Azure

Si vous souhaitez utiliser d’autres numéros d’instance pour l’instance SAP ASCS ou SCS, vous devez mettre à jour les noms et valeurs de ces ports.

Vous pouvez pour cela utiliser le Portail Azure.

Accédez à `<SID>-lb-ascs load balancer -> Load Balancing Rules`

Pour toutes les règles d’équilibrage de charge qui appartiennent à l’instance SAP ASCS ou SCS, modifiez les éléments suivants :

- Nom
- Port
- Port principal

Par exemple, si vous souhaitez modifier le numéro d’instance ASCS par défaut (00) et choisir par exemple 31, vous devez effectuer les modifications pour tous les ports répertoriés dans _**Tableau 1 :** Numéro de port des instances de SAP NetWeaver ABAP ASCS_.

Voici un exemple de mise à jour pour le port _lbrule3200_.

![Figure 14 : Modification des règles d’équilibrage de charge ASCS/SCS par défaut pour l’équilibrage de charge interne (ILB) Azure][sap-ha-guide-figure-3005]

_**Figure 14 :** Modification des règles d’équilibrage de charge ASCS/SCS par défaut pour l’équilibrage de charge interne (ILB) Azure_


### <a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Ajouter des ordinateurs Windows au domaine

Après avoir affecté une adresse IP statique aux machines virtuelles, ajoutez-les au domaine.

![Figure 15 : Ajout d’une machine virtuelle à un domaine][sap-ha-guide-figure-3006]

_**Figure 15 :** Ajout d’une machine virtuelle à un domaine_


### <a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Ajouter des entrées de Registre aux deux nœuds de cluster utilisés pour l’instance SAP ASCS/SCS

Les équilibreurs Azure Load Balancer, y compris l’équilibrage de charge interne Azure, ferment les connexions lorsqu’elles sont inactives pendant un certain laps de temps (délai d’inactivité). De l’autre côté, les processus de travail SAP dans les instances de dialogue ouvrent des connexions au processus de file d’attente SAP dès que la première requête empiler/dépiler doit être envoyée. Ces connexions restent généralement établies jusqu’à ce que le processus de travail ou de fil d’attente redémarre. Toutefois, si la connexion est inactive pendant un certain temps, l’équilibrage de charge interne Azure les fermera. Ce n’est pas vraiment un problème étant donné que le processus de travail SAP rétablira la connexion au processus de file d’attente si elle n’existe plus. Toutefois, ces activités seront documentées dans les traces de développement des processus SAP et créeront par conséquent beaucoup de contenu dans ces traces sans véritablement de bonne raison. Par conséquent, nous vous recommandons de modifier `KeepAliveTime` et `KeepAliveInterval` du protocole TCP/IP sur les deux nœuds du cluster. La modification des paramètres TCP/IP doit être combinée avec les paramètres de profil SAP qui nous décrirons plus loin dans ce document.

Ajoutez les entrées de registre Windows suivantes aux deux nœuds de cluster Windows pour SAP ASCS/SCS :

| Chemin | HKLM\\SYSTEM\\CurrentControlSet\\Services\\Tcpip\\Parameters   
| ----------------------|-----------------------------------------------------------
| Nom de la variable | `KeepAliveTime`                                              
| Type de variable | REG\_DWORD (décimal)                                        
| Valeur | 120 000                                                     
| Lien vers la documentation | [https://technet.microsoft.com/fr-FR/library/cc957549.aspx](https://technet.microsoft.com/fr-FR/library/cc957549.aspx) 


_**Tableau 3 :** Premier paramètre TCP/IP à modifier_


| Chemin | HKLM\\SYSTEM\\CurrentControlSet\\Services\\Tcpip\\Parameters   
| ----------------------|-----------------------------------------------------------
| Nom de la variable | `KeepAliveInterval`                                          
| Type de variable | REG\_DWORD (décimal)                                        
| Valeur | 120 000                                                     
| Lien vers la documentation | [https://technet.microsoft.com/fr-FR/library/cc957548.aspx](https://technet.microsoft.com/fr-FR/library/cc957548.aspx)


_**Tableau 4 :** Deuxième paramètre TCP/IP à modifier_

Puis **redémarrez les deux nœuds du cluster** afin d’appliquer les modifications.

### <a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Configuration du cluster de basculement Windows Server pour l’instance SAP ASCS/SCS

#### <a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> Collecter les nœuds de cluster dans la configuration du cluster

La première étape consiste à ajouter la fonctionnalité de clustering avec basculement aux deux nœuds du cluster. Cette opération s’effectue avec **l’Assistant « Ajouter un rôle et des fonctionnalités »** et ne nécessite pas d’autres descriptions.

La deuxième étape consiste à configurer le cluster de basculement à l’aide du gestionnaire du cluster de basculement Windows.

Dans la console MMC du gestionnaire du cluster de basculement, cliquez sur Créer un cluster et ajoutez uniquement le nom du nœud A du cluster, par exemple, _**pr1-ascs-0**_. N’ajoutez pas encore le second nœud. Il sera ajouté dans une étape ultérieure.

![Figure 16 : Première étape pour l’ajout d’une configuration de cluster de basculement : ajout du nom de serveur/machine virtuelle du premier nœud qui doit être un nœud de cluster][sap-ha-guide-figure-3007]

_**Figure 16 :** Première étape pour l’ajout d’une configuration de cluster de basculement : ajout du nom de serveur/machine virtuelle du premier nœud qui doit être un nœud de cluster_

Dans les étapes suivantes, vous êtes invité à entrer le nom de réseau (nom d’hôte virtuel) du cluster.

![Figure 17 : Deuxième étape pour l’ajout d’une configuration de cluster de basculement : définition du nom du cluster][sap-ha-guide-figure-3008]

_**Figure 17 :** Deuxième étape pour l’ajout d’une configuration de cluster de basculement : définition du nom du cluster_


Une fois le cluster créé, un test de validation du cluster s’exécute

![Figure 18 : Dernière étape pour l’ajout d’une configuration de cluster de basculement : exécution du contrôle de validation du cluster][sap-ha-guide-figure-3009]

_**Figure 18 :** Dernière étape pour l’ajout d’une configuration de cluster de basculement : exécution du contrôle de validation du cluster_


![Figure 19 : Dernière étape pour l’ajout d’une configuration de cluster de basculement : affichage d’avertissements de disque quorum introuvable par le contrôle de validation du cluster][sap-ha-guide-figure-3010]

_**Figure 19 :** Dernière étape pour l’ajout d’une configuration de cluster de basculement : affichage d’avertissements de disque quorum introuvable par le contrôle de validation du cluster_

Les avertissements relatifs aux disques peuvent être ignorés à ce stade ; un partage de fichiers témoin sera ajouté ultérieurement, ainsi que des disques partagés SIOS. À ce stade, nous ne nous préoccupons pas du quorum.

![Figure 20 : Définition d’une ressource principale de cluster avec adresse IP – nouvelle adresse IP nécessaire toutefois][sap-ha-guide-figure-3011]

_**Figure 20 :** Définition d’une ressource principale de cluster avec adresse IP – nouvelle adresse IP nécessaire toutefois_


Étant donné que l’adresse IP du serveur pointe vers un des nœuds de machine virtuelle, le cluster ne peut pas démarrer. Nous devons maintenant modifier l’adresse IP du service de cluster principal.

Par exemple, nous devons affecter une adresse IP (dans notre exemple, _**10.0.0.42**_) au nom d’hôte virtuel du cluster _**pr1-ascs-vir**_. Cette opération est effectuée sur la page de propriétés de la ressource IP du service de cluster principal comme indiqué ci-dessous

![Figure 21 : Utiliser les « Propriétés » pour définir une adresse IP correcte][sap-ha-guide-figure-3012]

_**Figure 21 :** Utiliser les « Propriétés » pour définir une adresse IP correcte_


![Figure 22 : Affecter l’adresse IP réservée pour le cluster][sap-ha-guide-figure-3013]

_**Figure 22 :** Affecter l’adresse IP réservée pour le cluster_

Après avoir modifié l’adresse IP, mettez en ligne le nom d’hôte virtuel du cluster.

![Figure 23 : Fonctionnement du service principal du cluster avec la bonne adresse IP][sap-ha-guide-figure-3014]

_**Figure 23 :** Fonctionnement du service principal du cluster avec la bonne adresse IP_

Maintenant que le service de cluster principal est fonctionnel, vous pouvez ajouter le deuxième nœud du cluster

![Figure 24 : Ajouter le deuxième nœud du cluster][sap-ha-guide-figure-3015]

_**Figure 24 :** Ajouter le deuxième nœud du cluster_

![Figure 25 : Ajouter le deuxième nom d’hôte du nœud du cluster, par exemple, pr1-ascs-1][sap-ha-guide-figure-3016]

_**Figure 25 :** Ajouter le deuxième nom d’hôte du nœud du cluster, par exemple, pr1-ascs-1_

![Figure 26 : Ne PAS cocher la case !][sap-ha-guide-figure-3017]

_**Figure 26 :** Ne PAS cocher la case !_

> [AZURE.NOTE]  
Assurez-vous que cette case « Ajouter la totalité du stockage disponible au cluster » n’est **PAS** cochée !

![Figure 27 : Ignorer de nouveau l’avertissement autour du quorum disque][sap-ha-guide-figure-3018]

_**Figure 27 :** Ignorer de nouveau l’avertissement autour du quorum disque_

Vous pouvez ignorer les avertissements concernant le quorum et les disques. La configuration du quorum et du disque de partage sera effectuée ultérieurement, comme le décrit le chapitre **[Installation de SIOS DataKeeper Cluster Edition pour le disque de partage en cluster SAP ASCS/SCS][sap-ha-guide-8.12.3]**.

#### <a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> Configurer un partage de fichiers en cluster témoin

##### <a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> Créer un partage de fichiers

Nous choisissons un partage de fichiers témoin au lieu d’un disque quorum. Cette option est prise en charge par SIOS DataKeeper.

Dans la configuration que nous utilisons comme illustrations dans ce document, le partage de fichiers témoin est configuré sur le serveur AD/DNS qui s’exécute dans Azure et est appelé _**domcontr-0**_. Dans la mesure où vous avez configuré une connexion VPN à Azure (de site à site ou avec ExpressRoute), votre AD/DNS réside en local et par conséquent ne convient pas pour exécuter un partage de fichiers témoin.

> [AZURE.NOTE] Dans le cas où votre AD/DNS s’exécute uniquement en local, ne configurez pas votre partage de fichiers témoin sur le système d’exploitation Windows AD/DNS s’exécutant en local, car la latence du réseau entre les nœuds de cluster s’exécutant sur Azure et AD/DNS en local peut devenir trop importante et entraîner des problèmes de connectivité. Veillez à configurer le partage de fichiers témoin sur une machine virtuelle Microsoft Azure proche du nœud de cluster.

Le disque quorum a besoin d’au moins 1 024 Mo d’espace libre. Le volume recommandé est de 2 048 Mo

La première étape consiste à ajouter l’objet nom de cluster

![Figure 28 : Affecter les autorisations de partage à l’objet nom de cluster][sap-ha-guide-figure-3019]

_**Figure 28 :** Affecter les autorisations de partage à l’objet nom de cluster_

Assurez-vous que les autorisations incluent la possibilité de modifier des données du partage de l’objet nom de cluster (dans notre exemple, _**pr1-ascs-vir$**_). Pour ajouter l’objet nom de cluster à la liste ci-dessus, vous devez appuyer sur « **Ajouter** », puis modifier le filtre pour permettre la vérification des objets ordinateurs également, comme indiqué ci-dessous.

![Figure 29 : Modifier le type d’objet de façon à inclure également des objets ordinateurs][sap-ha-guide-figure-3020]

_**Figure 29 :** Modifier le type d’objet de façon à inclure également des objets ordinateurs_

![Figure 30 : Cocher la case des objets ordinateurs][sap-ha-guide-figure-3021]

_**Figure 30 :** Cocher la case des objets ordinateurs_

Après cela, entrez l’objet nom de cluster, comme l’indique la figure 29. L’enregistrement étant à présent créé, vous pouvez modifier les autorisations comme l’indique la figure 28.

L’étape suivante consiste à utiliser l’onglet « Sécurité » du partage et à définir les autorisations les plus précises possible pour l’objet nom de cluster.

![Figure 31 : Définir les attributs de sécurité de l’objet nom de cluster sur le quorum de partage de fichiers][sap-ha-guide-figure-3022]

_**Figure 31 :** Définir les attributs de sécurité de l’objet nom de cluster sur le quorum du partage de fichiers_

##### <a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> Configurer le quorum du partage de fichiers témoin dans le gestionnaire du cluster de basculement

L’étape suivante consiste à modifier la configuration du cluster sur un partage de fichiers témoin à l’aide du gestionnaire du cluster de basculement.

![Figure 32 : Appeler « Configurer l’Assistant Configuration du paramètre de quorum du cluster » comme indiqué ici][sap-ha-guide-figure-3023]

_**Figure 32 :** Appeler « Configurer l’Assistant Configuration du paramètre de quorum du cluster » comme indiqué ici_

![Figure 33 : Écran de sélection de différentes configurations de quorum][sap-ha-guide-figure-3024]

_**Figure 33 :** Écran de sélection de différentes configurations de quorum_

Dans cette sélection, vous devez choisir « _**Sélectionner le témoin de quorum**_ ».

![Figure 34 : Écran de sélection du partage de fichiers témoin][sap-ha-guide-figure-3025]

_**Figure 34 :** Écran de sélection du partage de fichiers témoin_

Dans notre cas, vous devez choisir « _**Configurer un partage de fichiers témoin**_ ».

![Figure 35 : Définir l’emplacement du partage de fichiers témoin][sap-ha-guide-figure-3026]

_**Figure 35 :** Définir l’emplacement du partage de fichiers témoin_


Entrez le chemin d’accès UNC du partage de fichiers (dans notre exemple, `\\domcontr-0\FSW` )

Appuyez sur « Suivant », ce qui renvoie la liste des modifications que vous souhaitez apporter. Vérifiez-les et appuyez de nouveau sur Suivant pour modifier la configuration du cluster.

![Figure 36 : Écran de reconfiguration réussie du cluster][sap-ha-guide-figure-3027]

_**Figure 36 :** Écran de reconfiguration réussie du cluster_

Dans cette dernière étape, la configuration du cluster doit être correctement reconfigurée.

### <a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> Installation de SIOS DataKeeper Cluster Edition pour le disque de partage en cluster SAP ASCS/SCS

Nous disposons à ce stade d’une configuration fonctionnelle de cluster de basculement Windows Server dans Azure. Toutefois, cette configuration de cluster n’a encore aucune ressource de disque partagé. Pour installer un service SAP ASCS/SCS, nous avons besoin d’une ressource de disque partagé. C’est là que SIOS DataKeeper Cluster Edition entre en jeu. Azure ne nous permet pas de créer une ressources de disque partagé avec les fonctionnalités nécessaires ; nous devons donc nous appuyer sur SIOS DataKeeper, par exemple, pour avoir accès à cette fonctionnalité.

#### <a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> Ajouter la fonctionnalité Microsoft .NET Framework 3.5

Microsoft .NET Framework 3.5 n’est pas automatiquement activé ou installé sur les versions les plus récentes de Windows Server. Toutefois, SIOS DataKeeper requiert le .NET Framework sur tous les nœuds sur lesquels le produit sera installé. Par conséquent, il est nécessaire d’installer .NET 3.5 sur le système d’exploitation invité de toutes les machines virtuelles.

Il existe deux façons d’ajouter .NET Framework 3.5. La première possibilité consiste à utiliser « **Ajouter des rôles et des fonctionnalités** » dans Windows, comme indiqué ci-dessous :

![Figure 37 : Installer .NET Framework 3.5 avec « l’Assistant Ajouter un rôle et des fonctionnalités »][sap-ha-guide-figure-3028]

_**Figure 37 :** Installer .NET Framework 3.5 avec « l’Assistant Ajouter un rôle et des fonctionnalités »_

![Figure 38 : Barre de progression de l’installation de .NET Framework 3.5 avec « l’Assistant Ajouter un rôle et des fonctionnalités »][sap-ha-guide-figure-3029]

_**Figure 38 :** Barre de progression de l’installation de .NET Framework 3.5 avec « l’Assistant Ajouter un rôle et des fonctionnalités »_

La seconde possibilité pour activer la fonctionnalité .NET Framework 3.5 consiste à utiliser l’outil de ligne de commande _**dism.exe**_. Pour ce type d’installation, le répertoire « sxs » du support d’installation Windows doit être accessible. La commande suivante doit être exécutée dans une fenêtre d’invite de commandes avec élévation de privilèges :

```
Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
```

#### <a name="dd41d5a2-8083-415b-9878-839652812102"></a> Installation de SIOS DataKeeper

Parcourons l’installation de SIOS DataKeeper Cluster Edition. Il doit être installé sur chacun de nos deux nœuds de cluster. SIOS DataKeeper permet la création d’un stockage partagé virtuel en créant un miroir synchronisé et en simulant le stockage partagé en cluster.

Avant d’installer le logiciel SIOS, vous devez créer un utilisateur de domaine _**DataKeeperSvc**_.

> [AZURE.NOTE] Ajoutez cet utilisateur _**DataKeeperSvc**_ au groupe **Administrateurs locaux** sur les deux nœuds du cluster.
  
Installez le logiciel SIOS sur les deux nœuds de cluster

![Programme d’installation de SIOS][sap-ha-guide-figure-3030]

![Figure 39 : Premier écran d’installation de SIOS DataKeeper][sap-ha-guide-figure-3031]

_**Figure 39 :** Premier écran d’installation de SIOS DataKeeper_

![Figure 40 : Fenêtre contextuelle de DataKeeper indiquant qu’un service doit être désactivé][sap-ha-guide-figure-3032]

_**Figure 40 :** Fenêtre contextuelle de DataKeeper indiquant qu’un service doit être désactivé_

Lorsque vous recevez la fenêtre contextuelle de la figure 40, choisissez « _**Oui**_ ».

![Figure 41 : Sélection de l’utilisateur de SIOS DataKeeper][sap-ha-guide-figure-3033]

_**Figure 41 :** Sélection de l’utilisateur de SIOS DataKeeper_


Dans l’écran ci-dessus, nous vous recommandons de choisir _**Compte de domaine ou de serveur**_.

![Figure 42 : Indiquer l’utilisateur de domaine et le mot de passe à l’installation de SIOS DataKeeper][sap-ha-guide-figure-3034]

_**Figure 42 :** Indiquer l’utilisateur de domaine et le mot de passe à l’installation de SIOS DataKeeper_

Spécifiez le compte de domaine que vous avez créé pour SIOS DataKeeper ainsi que les mots de passe de ce compte.

![Figure 43 : Fournir votre licence SIOS DataKeeper][sap-ha-guide-figure-3035]

_**Figure 43 :** Fournir votre licence SIOS DataKeeper_

Installez la clé de licence de votre SIOS DataKeeper, comme l’illustre la figure 43. À la fin de l’installation, vous devrez **redémarrer la machine virtuelle**.

#### <a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> Configurer SIOS DataKeeper

Après avoir installé SIOS DataKeeper sur les deux nœuds, nous devons commencer la configuration. L’objectif de la configuration est de mettre en place une réplication synchrone des données entre le disque dur virtuel supplémentaire associé et chacune des machines virtuelles. Les étapes suivantes montrent la configuration sur les deux nœuds.

![Figure 44 : Outil de configuration et de gestion de DataKeeper][sap-ha-guide-figure-3036]

_**Figure 44 :** Outil de configuration et de gestion de DataKeeper_


Lancez l’outil de gestion et de configuration de DataKeeper et appuyez sur le lien « _**Se connecter au serveur**_ » (entouré en rouge ci-dessus)

![Figure 45 : Insérer le nom ou l’adresse TCP/IP du premier nœud et, dans la deuxième étape, du second nœud ; l’outil de gestion doit se connecter à][sap-ha-guide-figure-3037]

_**Figure 45 :** Insérer le nom ou l’adresse TCP/IP du premier nœud et, dans la deuxième étape, du second nœud ; l’outil de gestion doit se connecter à_

L’étape suivante consiste à créer le travail de réplication entre les deux nœuds

![Figure 46 : Créer le travail de réplication][sap-ha-guide-figure-3038]

_**Figure 46 :** Créer le travail de réplication_

Un Assistant vous guide tout au long du processus

![Figure 47 : Définir le nom du travail de réplication][sap-ha-guide-figure-3039]

_**Figure 47 :** Définir le nom du travail de réplication_

![Figure 48 : Définir les données de base du nœud qui doit être le nœud source actuel][sap-ha-guide-figure-3040]

_**Figure 48 :** Définir les données de base du nœud qui doit être le nœud source actuel_

Dans un premier temps, le nom, l’adresse TCP/IP et le volume de disque du nœud source doivent être définis. La deuxième étape consiste à définir le nœud cible. Là encore, le nom, l’adresse TCP/IP et le volume de disque du nœud cible doivent être définis.

![Figure 49 : Définir les données de base du nœud qui doit être le nœud cible actuel][sap-ha-guide-figure-3041]

_**Figure 49 :** Définir les données de base du nœud qui doit être le nœud cible actuel_

L’étape suivante consiste à définir les algorithmes de compression à appliquer. Dans notre cas, nous recommandons de compresser le flux de réplication. Dans les situations de resynchronisation notamment, la compression du flux de réplication accélère considérablement la resynchronisation. N’oubliez pas que la compression utilise les ressources du processeur et de la RAM d’une machine virtuelle. Par conséquent, plus le taux de compression est élevé, plus le processeur est utilisé. Vous ne pouvez pas modifier ce paramètre par la suite.

Vous devez également vérifier si la réplication s’exécute en mode asynchrone ou synchrone. **Pour protéger les configurations SAP ASCS/SCS, le paramètre de réplication synchrone est requis**.

![Figure 50 : Définir les détails de la réplication][sap-ha-guide-figure-3042]

_**Figure 50 :** Définir les détails de la réplication_

La dernière étape consiste à définir si le volume répliqué par le travail de réplication doit être représenté auprès d’une configuration de cluster WSFC comme disque partagé. Pour la configuration SAP ASCS/SCS, nous devons choisir « OUI » pour que le cluster Windows voie le volume répliqué en tant que disque partagé utilisable comme volume de cluster.

![Figure 51 : Appuyer sur « Oui » pour activer le volume répliqué en tant que volume de cluster][sap-ha-guide-figure-3043]

_**Figure 51 :** Appuyer sur « Oui » pour activer le volume répliqué en tant que volume de cluster_

Une fois la création terminée, l’outil de gestion DataKeeper répertorie le travail de réplication comme actif.

![Figure 52 : Mise en miroir synchrone active de DataKeeper pour le disque de partage SAP ASCS/SCS][sap-ha-guide-figure-3044]

_**Figure 52 :** Mise en miroir synchrone active de DataKeeper pour le disque de partage SAP ASCS/SCS_

Par conséquent, le disque peut maintenant être considéré comme un disque DataKeeper dans le gestionnaire du cluster de basculement Windows, comme indiqué ci-dessous.

![Figure 53 : Affichage du disque répliqué par DataKeeper dans le gestionnaire du cluster de basculement][sap-ha-guide-figure-3045]

_**Figure 53 :** Affichage du disque répliqué par DataKeeper dans le gestionnaire du cluster de basculement_


## <a name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a> Installation de système SAP NetWeaver

Nous ne décrivons pas l’installation du système SGBD, car les configurations varient selon le SGBD utilisé. Toutefois, nous partons du principe que les problèmes de haute disponibilité du SGBD sont traités par les fonctions de support des fournisseurs de SGBD pour Azure. Par exemple, AlwaysOn ou mise en miroir de la base de données pour SQL Server et Oracle Data Guard pour Oracle. Dans notre exemple de scénario, utilisé dans cette documentation, nous n’avons pas protégé le SGBD en plus.

Il n’y a pas non plus de considérations particulières concernant l’interaction des différents SGBD avec une configuration SAP ASCS/SCS en cluster de ce type sur Azure.

> [AZURE.NOTE]  
La procédure d’installation de systèmes SAP NetWeaver ABAP, Java et ABAP+Java est presque identique. La plus grande différence est qu’un système SAP ABAP comprend une instance ASCS. Le système SAP Java possède une instance SCS et le système SAP ABAP+Java une instance ASCS et une instance SCS exécutées dans le même groupe de cluster de basculement Microsoft. Toute différence d’installation de chaque pile d’installation de SAP NetWeaver sera mentionnée explicitement. Toutes les autres parties sont supposées identiques.

### <a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a> Installation de SAP avec une instance ASCS/SCS hautement disponible

> [AZURE.NOTE]  
Ne placez PAS votre fichier d’échange sur des volumes DataKeeper mis en miroir, car il n’est pas pris en charge par DataKeeper. Vous pouvez laisser votre fichier d’échange sur le lecteur D:\\ temporaire d’une machine virtuelle Azure où il se trouve déjà lors du déploiement d’une machine virtuelle dans Azure. Si ce n’est pas le cas, veuillez le corriger et placer le fichier d’échange Windows sur le lecteur D:\\ de votre machine virtuelle Azure.

#### <a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a> Créer un nom d’hôte virtuel pour SAP ASCS/SCS en cluster

La première étape consiste à créer l’entrée DNS nécessaire pour le nom d’hôte virtuel de l’instance ASCS/SCS. L’outil utilisé pour cela est le gestionnaire de DNS Windows. Outre le nom d’hôte virtuel, l’adresse IP affectée au nom d’hôte virtuel doit également être définie.

> [AZURE.NOTE]  
**N’oubliez pas que l’adresse IP que nous affectons au nom d’hôte virtuel de l’instance ASCS/SCS doit être la même que l’adresse IP affectée à l’équilibreur Azure Load Balancer (`<sid>-lb-ascs`) Adresse IP du nom d’hôte virtuel SAP ASCS/SCS `(pr1-ascs-sap)` = Adresse IP d’Azure Load Balancer `(pr1-lb-ascs)`**

Cela signifie également qu’un seul rôle de cluster de basculement SAP, par exemple pour un système ABAP une instance ASCS, pour un système Java une instance SCS et pour un système ABAP+Java une instance ASCS et une instance SCS, peuvent s’exécuter dans un même cluster de basculement Windows Server sur Azure.

> [AZURE.NOTE] Le clustering à plusieurs SID comme le décrit le guide d’installation de SAP (consultez [Guides d’installation de SAP][sap-installation-guides]) ne fonctionne pas actuellement dans Azure.

![Figure 54 : Définir l’entrée DNS de l’adresse TCP/IP et du nom virtuel du cluster SAP ASCS/SCS][sap-ha-guide-figure-3046]

_**Figure 54 :** Définir l’entrée DNS de l’adresse TCP/IP et du nom virtuel du cluster SAP ASCS/SCS_

L’entrée est indiquée dans le gestionnaire DNS sous le domaine, comme l’indique la figure suivante.

![Figure 55 : Nouveau nom virtuel et nouvelle adresse TCP/IP répertoriés pour la configuration de cluster SAP ASCS/SCS][sap-ha-guide-figure-3047]

_**Figure 55 :** Nouveau nom virtuel et nouvelle adresse TCP/IP répertoriés pour la configuration de cluster SAP ASCS/SCS_

#### <a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> Installer le premier nœud de cluster SAP

L’installation du premier nœud de cluster ASCS/SCS ne diffère en rien de la description dans la documentation d’Installation de SAP :
- Exécutez l’option du premier nœud de cluster sur le nœud A du cluster, l’hôte _**pr1-ascs-0**_ dans notre exemple.

Si vous souhaitez conserver les ports par défaut pour l’équilibrage de charge interne Azure, choisissez :

- Pour **Système ABAP** - numéro d’instance **ASCS** **00**
- Pour **Système Java** - numéro d’instance **SCS** **01**
- Pour **Système ABAP+Java** - numéro d’instance **ASCS****00** et numéro d’instance **SCS** **01**

Si vous souhaitez utiliser d’autres numéros d’instance que 00 pour l’instance ASCS ABAP et 01 pour l’instance SCS Java, vous devez tout d’abord modifier les règles d’équilibrage de charge interne Azure par défaut, comme décrit dans : **[Modification des règles d’équilibrage de charge interne (ILB) Azure ASCS/SCS par défaut][sap-ha-guide-8.9]**.

Une fois cette étape terminée, vous devez suivre quelques étapes qui ne sont pas décrites dans la documentation d’installation de SAP habituelle.

#### <a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> Modifier le profil SAP de l’instance ASCS/SCS

Un nouveau paramètre de profil doit être ajouté. Ce paramètre de profil empêche la fermeture des connexions entre les processus de travail SAP et le serveur de file d’attente lorsqu’ils sont inactifs pendant trop longtemps. Nous avons mentionné le problème du scénario dans le chapitre **[Ajouter des entrées de registre sur les deux nœuds de cluster utilisés pour l’instance SAP ASCS/SCS][sap-ha-guide-8.11]** de ce document. Dans cette section, nous avons également introduit deux modifications dans certains paramètres de connexion TCP/IP de base. Dans un deuxième temps, nous devons configurer le serveur de file d’attente pour qu’il envoie un signal **keep\_alive** et ainsi que les connexions n’atteignent pas le seuil d’inactivité de l’équilibrage de charge interne Azure. Pour cela, ajoutez ce paramètre de profil :

```
enque/encni/set_so_keepalive = true
```

au profil d’instance SAP ASCS/SCS. Dans notre exemple, le chemin est :

`<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

par exemple, au profil d’instance SAP SCS et au chemin correspondant

`<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`


#### <a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> Ajouter un port de sondage

Pour que l’ensemble de la configuration du cluster fonctionne avec un équilibreur Azure Load Balancer, nous devons tirer parti des fonctionnalités de sondage de l’équilibrage de charge interne. Généralement, un équilibrage de charge interne Azure équilibre et distribue équitablement la charge de travail entrante entre les machines virtuelles qui participent. Toutefois, cela ne fonctionnerait pas dans une configuration de cluster de ce type, car seule une instance est active ; l’autre est passive et ne peut pas accepter de charge de travail. Pour autoriser les configurations où l’équilibrage de charge interne Azure affecte du travail à la ou les instances actives uniquement, une fonctionnalité de sondage a été instaurée. Grâce à cette fonctionnalité, l’équilibrage de charge interne a la possibilité de vérifier quelle instance est active et ensuite de cibler uniquement cette instance avec la charge de travail. Tout d’abord, nous allons vérifier le paramètre _**ProbePort**_ actuel avec cette commande PowerShell exécutée dans une des machines virtuelles engagées dans la configuration du cluster :

```PowerShell
Get-ClusterResource „SAP PR1 IP" | Get-ClusterParameter 
```

![Figure 56 : Port de sondage de la configuration du cluster de 0 par défaut][sap-ha-guide-figure-3048]

_**Figure 56 :** Port de sondage de la configuration du cluster de 0 par défaut_

Par défaut, le numéro du port de sondage est défini sur 0. Pour que la configuration fonctionne, un port doit être défini. Dans notre cas, nous devons utiliser le port de sondage _**62300**_, car ce numéro de port est défini dans les modèles Azure Resource Manager SAP. L’affectation de ce numéro de port peut s’effectuer avec les deux commandes ci-dessous :

Récupérez tout d’abord _**l’IP WAC SAP**_ de la ressource de cluster du nom d’hôte virtuel SAP

```PowerShell
$var = Get-ClusterResource | Where-Object {  $_.name -eq "SAP PR1 IP"  } 
```

Puis définissez le port de sondage sur 62300

```PowerShell
$var | Set-ClusterParameter -Multiple @{"Address"="10.0.0.43";"ProbePort"=62300;"Subnetmask"="255.255.255.0";"Network"="Cluster Network 1";"OverrideAddressMatch"=1;"EnableDhcp"=0}  
```

Vous devez arrêter et démarrer le rôle de cluster _**SAP PR1**_ pour activer les modifications.

Après avoir mis en ligne le rôle de cluster _**SAP PR1**_, vérifiez que _**ProbePort**_ est défini sur la nouvelle valeur :

```PowerShell
Get-ClusterResource „SAP PR1 IP" | Get-ClusterParameter 
```

![Figure 57 : Port de sondage du cluster après modification][sap-ha-guide-figure-3049]

_**Figure 57 :** Port de sondage du cluster après modification_

Vous pouvez voir que _**ProbePort**_ a maintenant la valeur _**62300**_. Vous pouvez maintenant accéder au partage de fichiers _**\\\ascsha-clsap\\sapmnt**_ d’autres hôtes comme _**ascsha-dbas**_.

### <a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> Installation de l’instance de base de données

L’installation de l’instance de base de données n’est en rien différente du processus décrit dans la documentation d’installation de SAP. Par conséquent, elle n’est pas décrite ici.

### <a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> Installation du deuxième nœud de cluster

Là encore, l’installation du deuxième nœud du cluster ne diffère pas de la documentation d’installation de SAP. Par conséquent, suivez les étapes du guide d’installation pour installer le deuxième nœud du cluster.

### <a name="094bc895-31d4-4471-91cc-1513b64e406a"></a> Modifier le type de démarrage de service Windows de l’instance SAP ERS

Choisir le type de démarrage du ou des services Windows SAP ERS _**Automatique (début différé)**_ sur les deux nœuds du cluster.

![Figure 58 : Choisir le type de service Automatique différé pour l’instance SAP ERS][sap-ha-guide-figure-3050]

_**Figure 58 :** Choisir le type de service Automatique différé pour l’instance SAP ERS_

### <a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> Installation du serveur d’applications principal SAP (PAS)

Exécutez l’installation de l’instance de serveur d’applications principal `<sid>-di-0` sur la machine virtuelle désignée pour héberger le PAS. Il n’existe aucune dépendance à des spécificités d’Azure ou DataKeeper.

### <a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> Installation du serveur d’applications supplémentaire SAP (AAS)

Exécutez l’installation d’un serveur d’applications SAP supplémentaire sur toutes les machines virtuelles désignées pour héberger un serveur d’applications SAP, par exemple entre `<sid>-di-1` et `<sid>-di-<n>`.

## <a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> Test de basculement d’instance SAP ASCS/SCS et de réplication SIOS

Vous pouvez facilement tester et surveiller le basculement d’une instance SAP ASCS/SCS et la réplication de disque SIOS avec le _**Gestionnaire du cluster de basculement**_ et l’interface utilisateur SIOS DataKeeper.

### <a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a> Point de départ : l’instance SAP ASCS/SCS s’exécute sur le nœud A du cluster

Le groupe de cluster _**SAP WAC**_ est en cours d’exécution sur le nœud A du cluster, par exemple _**ascsha-clna**_. Le disque partagé S:, qui fait partie du groupe de cluster _**SAP WAC**_ et est utilisé par l’instance ASCS/SCS, est affecté au nœud A du cluster.

![Figure 59 : Gestionnaire du cluster de basculement : le groupe de cluster SAP <SID> est en cours d’exécution sur le nœud A du cluster][sap-ha-guide-figure-5000]

_**Figure 59 :** Gestionnaire du cluster de basculement : le groupe de cluster SAP <SID> est en cours d’exécution sur le nœud A du cluster_

À l’aide de l’interface utilisateur SIOS DataKeeper, vous pouvez voir que les données du disque partagé sont répliquées de manière synchrone à partir du volume source S: sur le nœud A du cluster (par exemple, _**ascsha-clna [10.0.0.41]**_) vers le volume cible _**S:**_ sur le nœud B du cluster (par exemple, _**ascsha-clnb [10.0.0.42]**_)

![Figure 60 : SIOS DataKeeper : réplication du volume local du nœud A vers le nœud B du cluster][sap-ha-guide-figure-5001]

_**Figure 60 :** SIOS DataKeeper : réplication du volume local du nœud A vers le nœud B du cluster_


### <a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a> Processus de basculement du nœud A vers le nœud B

Vous pouvez initier le basculement du groupe de cluster SAP <SID> du nœud A vers le nœud B du cluster :

- avec le gestionnaire du cluster de basculement

- avec PowerShell du cluster de basculement

  ```powershell
  Move-ClusterGroup -Name "SAP WAC" 
  ```

- en redémarrant le nœud A du cluster dans le système d’exploitation invité Windows (cette opération lance le basculement automatique du groupe de cluster SAP <SID> du nœud A vers le nœud B)

- en redémarrant le nœud A du cluster sur le Portail Azure (cette opération lance le basculement automatique du groupe de cluster SAP <SID> du nœud A vers le nœud B)

- en redémarrant le nœud A du cluster avec Azure PowerShell (cette opération lance le basculement automatique du groupe de cluster SAP <SID> du nœud A vers le nœud B)

  ```powershell
  Restart-AzureVM -Name ascsha-clna -ServiceName ascsha-cluster
  ```

### <a name="755a6b93-0099-4533-9f6d-5c9a613878b5"></a> Résultat final : l’instance SAP ASCS/SCS s’exécute sur le nœud B du cluster

Après le basculement, le groupe de cluster `SAP <SID>` est en cours d’exécution sur le nœud B du cluster, par exemple _**ascsha-clna**_.

![Figure 61 : Gestionnaire du cluster de basculement : le groupe de cluster SAP <SID> est en cours d’exécution sur le nœud B du cluster][sap-ha-guide-figure-5002]

_**Figure 61 :** Gestionnaire du cluster de basculement : le groupe de cluster SAP <SID> est en cours d’exécution sur le nœud B du cluster_

Le disque partagé est maintenant monté sur le nœud B du cluster. SIOS DataKeeper réplique les données à partir du volume source S: sur le nœud B du cluster (par exemple, _**ascsha-clnb [10.0.0.42]**_) vers le volume cible S: sur le nœud A du cluster (par exemple, _**ascsha-clna [10.0.0.41]**_).

![Figure 62 : SIOS DataKeeper : réplication du volume local du nœud B vers le nœud A du cluster][sap-ha-guide-figure-5003]

_**Figure 62 :** SIOS DataKeeper : réplication du volume local du nœud B vers le nœud A du cluster_

<!---HONumber=AcomDC_0928_2016-->