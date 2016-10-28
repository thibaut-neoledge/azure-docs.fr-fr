<properties
   pageTitle="Utilisation de SAP sur des machines virtuelles Windows| Microsoft Azure"
   description="En savoir plus sur l’utilisation de SAP sur des machines virtuelles Windows dans Microsoft Azure"
   services="virtual-machines-windows,virtual-network,storage"
   documentationCenter="saponazure"
   authors="MSSedusch"
   manager="timlt"
   editor=""
   tags="azure-service-management"
   keywords=""/>
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="NA"
   ms.topic="campaign-page"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="07/20/2016"
   ms.author="sedusch"/>

# Utilisation de SAP sur des machines virtuelles Windows dans Azure

Cloud Computing est un terme largement utilisé et il l’est de plus en plus dans le secteur de l’informatique, des petites entreprises jusqu’aux grandes multinationales. Microsoft Azure est la plateforme de Cloud Services de Microsoft qui offre un large éventail de nouvelles possibilités. Désormais, les clients peuvent configurer et annuler rapidement des applications dans Cloud-Services, en échappant ainsi aux éventuelles restrictions techniques ou budgétaires. Au lieu de consacrer du temps et de l’argent à l’infrastructure matérielle, les entreprises peuvent se concentrer sur l’application, les processus de travail et leurs avantages pour les clients et les utilisateurs.

Avec Microsoft Azure Virtual Machines, Microsoft propose une plateforme IaaS (Infrastructure as a Service) complète. Les applications basées sur SAP NetWeaver sont prises en charge sur Azure Virtual Machines (IaaS). Les livres blancs ci-dessous décrivent comment planifier et mettre en œuvre des applications basées sur SAP NetWeaver sur des machines virtuelles Linux dans Azure. Vous pouvez également implémenter des applications basées sur SAP NetWeaver sur des [machines virtuelles Linux](virtual-machines-linux-classic-sap-get-started.md).

[AZURE.INCLUDE [virtual-machines-common-classic-sap-get-started](../../includes/virtual-machines-common-classic-sap-get-started.md)]

## SAP NetWeaver sur Azure : haute disponibilité

Titre : SAP NetWeaver sur Azure – clustering d’instances ASCS/SCS SAP utilisant un Cluster de basculement Windows Server sur Azure avec SIOS DataKeeper

Résumé : ce document explique comment utiliser SIOS DataKeeper pour définir une configuration à haute disponibilité SAP ASCS/SCS sur Azure. SAP protège les composants à point unique de défaillance tels que SAP ASCS/SCS ou les services de réplication de file d’attente avec des configurations de cluster de basculement Windows Server qui nécessitent des disques partagés. Ces composants SAP sont essentiels pour le fonctionnement d’un système SAP. Par conséquent des fonctionnalités de haute disponibilité doivent donc être mises en place pour s’assurer que ces composants peuvent supporter une défaillance de serveur ou de machine virtuelle, comme le font les configurations de cluster Windows pour les environnements complets et Hyper-V. Depuis août 2015, Azure seul ne peut pas fournir les disques partagés qui seraient nécessaires pour les configurations Windows hautement disponibles requises pour ces composants SAP critiques. Toutefois, des configurations de cluster de basculement Windows Server nécessaires à SAP ASCS/SCS peuvent être créées sur la plateforme Azure IaaS à l’aide du produit DataKeeper de SIOS. Ce document décrit étape par étape la procédure d’installation d’une configuration de Cluster de basculement Windows Server avec disque partagé fourni par SIOS Datakeeper dans Azure. Il détaille les configurations pour Azure d’une part et Windows et SAP d’autre part qui font fonctionner de manière optimale la configuration à haute disponibilité. Ce document vient compléter la documentation sur l’installation SAP et des notes SAP, qui représentent les ressources incontournables en matière d’installation et de déploiement de logiciels SAP sur des plateformes données.

Mise à jour : août 2015

[Télécharger ce guide maintenant](http://go.microsoft.com/fwlink/?LinkId=613056)

<!---HONumber=AcomDC_0824_2016-->