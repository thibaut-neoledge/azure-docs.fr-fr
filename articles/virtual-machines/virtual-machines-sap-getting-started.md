<properties
   pageTitle="Utilisation de SAP sur des machines virtuelles Azure | Microsoft Azure"
   description="Utilisation de SAP sur des machines virtuelles Azure"
   services="virtual-machines,virtual-network,storage"
   documentationCenter="saponazure"
   authors="MSSedusch"
   manager="juergent"
   editor=""
   tags="azure-service-management"
   keywords=""/>
<tags
   ms.service="virtual-machines"
   ms.devlang="NA"
   ms.topic="campaign-page"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="na"
   ms.date="02/12/2016"
   ms.author="sedusch"/>
   
# Utilisation de SAP sur des machines virtuelles Azure

Cloud Computing est un terme largement utilisé et il l’est de plus en plus dans le secteur de l’informatique, des petites entreprises jusqu’aux grandes multinationales. Microsoft Azure est la plateforme de Cloud Services de Microsoft qui offre un large éventail de nouvelles possibilités. Désormais, les clients peuvent configurer et annuler rapidement des applications dans Cloud-Services, en échappant ainsi aux éventuelles restrictions techniques ou budgétaires. Au lieu de consacrer du temps et de l’argent à l’infrastructure matérielle, les entreprises peuvent se concentrer sur l’application, les processus de travail et leurs avantages pour les clients et les utilisateurs.

Avec Microsoft Azure Virtual Machine Services, Microsoft propose une infrastructure complète sous forme d’infrastructure en tant que services (IaaS). Les applications basées sur SAP NetWeaver sont prises en charge sur Azure Virtual Machines (IaaS). Les livres blancs ci-dessous décrivent comment planifier et mettre en œuvre des applications basées sur SAP NetWeaver dans Microsoft Azure en tant que plateforme de prédilection.

## Planification et implémentation

Titre : SAP NetWeaver sur Azure Virtual Machines – Guide de planification et d’implémentation

Résumé : commencez par étudier ce document si vous envisagez d’exécuter SAP NetWeaver sur Azure Virtual Machines. Ce guide de planification et d’implémentation vous aidera à évaluer si un système existant ou planifié, basé sur SAP NetWeaver peut être déployé dans un environnement Azure Virtual Machines. Il comprend plusieurs scénarios de déploiement de SAP NetWeaver et inclut des configurations SAP spécifiques d’Azure. Il répertorie et décrit toutes les informations de configuration de SAP/Azure dont vous avez besoin pour exécuter dans un environnement SAP hybride. Il traite décrit également les mesures à prendre pour assurer une haute disponibilité des systèmes basés sur SAP NetWeaver déployés dans une infrastructure IaaS.

Mise à jour : août 2015

[Télécharger ce guide maintenant](http://go.microsoft.com/fwlink/?LinkId=397963)
## Déploiement
Titre : SAP NetWeaver sur Machines virtuelles Azure – Guide de déploiement

Résumé : ce document décrit la procédure de déploiement du logiciel SAP NetWeaver sur des machines virtuelles dans Azure. Ce document aborde trois scénarios de déploiement spécifiques, en mettant l’accent sur l’activation des extensions d’analyse Azure pour SAP, et inclut des conseils de dépannage concernant ces dernières. Ce document suppose que vous avez lu le guide de planification et d’implémentation.

Mise à jour : septembre 2015

[Télécharger ce guide maintenant](http://go.microsoft.com/fwlink/?LinkId=397964)

## Système de gestion de base de données (SGBD) pour SAP dans Azure
Titre : guide de déploiement de SGBD pour SAP dans Azure

Résumé : ce document porte sur les éléments à prendre en compte pour la planification et l’implémentation des systèmes de gestion de base de données qui doivent s’exécuter conjointement avec SAP. La première partie répertorie et présente les considérations générales. Les parties suivantes du document concernent des déploiements de SGBD différents dans Azure pris en charge par SAP. Les autres SGBD présentés sont : SQL Server, SAP ASE, Oracle, SAP MaxDB et IBM DB2 pour Linux, Unix et Windows. Ces sections présentent les éléments à prendre en compte lors de l’exécution de systèmes SAP sur Azure avec ces systèmes de gestion de base de données. D’autres sections présentent les méthodes de sauvegarde et de haute disponibilité prises en charge par les différents systèmes de gestion de base de données sur Azure dans le cadre d’une utilisation avec des applications SAP.

Mise à jour : décembre 2015

[Télécharger ce guide maintenant](http://go.microsoft.com/fwlink/?LinkId=397965)

## SAP NetWeaver sur Azure

Titre : SAP NetWeaver - Création d’une solution de récupération d’urgence Azure

Résumé : ce document décrit de façon détaillée la création d’une solution de récupération d’urgence Azure pour SAP NetWeaver. La solution décrite est fondée sur l’hypothèse selon laquelle le paysage SAP s’exécute localement de façon virtualisée sur Hyper-V. La première partie du document présente les composants des services Azure Site Recovery (ASR). La deuxième partie du document décrit les environnements des paysages basés sur SAP NetWeaver. Les possibilités d’utiliser ASR avec des instances de l’application SAP NetWeaver et SAP Central Services y sont décrites. Une section de la deuxième partie indique comment exploiter les services ASR pour SAP Central Services protégés par des configurations de cluster de basculement Windows Server.

Mise à jour : septembre 2015

[Télécharger ce guide maintenant](http://go.microsoft.com/fwlink/?LinkID=521971)

## SAP NetWeaver sur Azure : haute disponibilité

Titre : SAP NetWeaver sur Azure – clustering d’instances ASCS/SCS SAP utilisant un Cluster de basculement Windows Server sur Azure avec SIOS DataKeeper

Résumé : ce document explique comment utiliser SIOS DataKeeper pour définir une configuration à haute disponibilité SAP ASCS/SCS sur Azure. SAP protège les composants à point unique de défaillance tels que SAP ASCS/SCS ou les services de réplication de file d’attente avec des configurations de cluster de basculement Windows Server qui nécessitent des disques partagés. Ces composants SAP sont essentiels pour le fonctionnement d’un système SAP. Par conséquent des fonctionnalités de haute disponibilité doivent donc être mises en place pour s’assurer que ces composants peuvent supporter une défaillance de serveur ou de machine virtuelle, comme le font les configurations de cluster Windows pour les environnements complets et Hyper-V. Depuis août 2015, Azure seul ne peut pas fournir les disques partagés qui seraient nécessaires pour les configurations Windows hautement disponibles requises pour ces composants SAP critiques. Toutefois, des configurations de cluster de basculement Windows Server nécessaires à SAP ASCS/SCS peuvent être créées sur la plateforme Azure IaaS à l’aide du produit DataKeeper de SIOS. Ce document décrit étape par étape la procédure d’installation d’une configuration de Cluster de basculement Windows Server avec disque partagé fourni par SIOS Datakeeper dans Azure. Il détaille les configurations pour Azure d’une part et Windows et SAP d’autre part qui font fonctionner de manière optimale la configuration à haute disponibilité. Ce document vient compléter la documentation sur l’installation SAP et des notes SAP, qui représentent les ressources incontournables en matière d’installation et de déploiement de logiciels SAP sur des plateformes données.

Mise à jour : août 2015

[Télécharger ce guide maintenant](http://go.microsoft.com/fwlink/?LinkId=613056)

## SAP NetWeaver sur les machines virtuelles Azure SUSE Linux

Titre : Test de SAP NetWeaver sur les machines virtuelles Microsoft Azure SUSE Linux

Résumé : à ce stade, il n’existe aucune prise en charge SAP officielle pour l’exécution de SAP NetWeaver sur les machines virtuelles Linux Azure. Néanmoins, les clients peuvent effectuer des tests ou envisager d’exécuter une version de démonstration de SAP ou des systèmes d’apprentissage sur les machines virtuelles Linux Azure, tant qu’il n’est pas nécessaire de contacter le service d’assistance SAP. Cet article doit vous aider à configurer les machines virtuelles Azure SUSE Linux pour l’exécution de SAP et donne quelques conseils de base afin d’éviter les pièges potentiels courants.

Mise à jour : décembre 2015

[Cet article est disponible ici](virtual-machines-sap-on-linux-suse-quickstart.md)

<!---HONumber=AcomDC_0218_2016-->