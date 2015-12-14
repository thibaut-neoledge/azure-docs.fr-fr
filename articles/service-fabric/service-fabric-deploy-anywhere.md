<properties
   pageTitle="Déploiement universel avec Service Fabric (Windows Server et Linux) | Microsoft Azure"
   description="Les clusters Service Fabric peuvent être exécutés sur Windows Server et Linux permettant aux applications Service Fabric d'être déployées et hébergées partout où vous pouvez exécuter Windows Server et Linux."
   services="service-fabric"
   documentationCenter=".net"
   authors="kunalds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/19/2015"
   ms.author="kunalds"/>

# Déploiement universel sur Windows Server ou Linux avec Service Fabric
Service Fabric autorise la création de clusters Service Fabric sur les machines virtuelles ou les ordinateurs exécutant Windows Server ou Linux. Cela permet aux applications Service Fabric d'être déployées et exécutées dans n'importe quel environnement, que ce soit en local ou avec n'importe quel fournisseur cloud où vous avez un ensemble d'ordinateurs Windows Server ou Linux qui sont interconnectés. Service Fabric fournit un package d'installation pour créer des clusters Service Fabric. L’avantage de la fonctionnalité de déploiement universel est qu’il n’existe aucune obligation en termes de fournisseur lorsque vous générez une application à l’aide de Service Fabric dans la mesure où vous choisissez où ces applications s’exécutent. Cela augmente également votre chance d'atteindre un ensemble plus large de clients étant donné que des clients différents peuvent avoir des exigences différentes pour les environnements où ils souhaitent exécuter vos applications. Par exemple, les clients du secteur financier et du secteur de la santé peuvent avoir des besoins différents de ceux d'un constructeur automobile ou d'une agence de voyages. La version d'évaluation technique de cette fonctionnalité devrait être publiée au premier trimestre 2016.

## Systèmes d'exploitation pris en charge
Vous serez en mesure de créer des clusters sur des machines virtuelles ou des ordinateurs qui exécutent ces systèmes d'exploitation : * Windows Server 2012 R2 * Windows Server 2016 * Linux

## Langages de programmation pris en charge
Vous serez en mesure d'écrire des applications Service Fabric avec l'un de ces langages de programmation : * C# * Java

## Création et configuration du cluster
Service Fabric fournit un package d’installation que vous pouvez télécharger à partir du Centre de téléchargement Microsoft. Une fois que vous avez téléchargé ce package, vous devrez apporter des modifications à un fichier de configuration pour spécifier les paramètres de votre cluster. Une fois que les paramètres du cluster ont été modifiés, vous exécuterez un script d'installation qui créera le cluster couvrant les ordinateurs que vous avez spécifiés dans les paramètres de votre cluster. Les détails exacts du processus d’installation seront communiqués lors du lancement de la version préliminaire de cette fonctionnalité au premier trimestre 2016.

## Déploiements cloud et Déploiements en local
Le processus de création d'un cluster Service Fabric local est similaire au processus de création d'un cluster sur un cloud de votre choix où vous avez un ensemble de machines virtuelles. Les étapes initiales d'approvisionnement des machines virtuelles sont gouvernées par l'environnement cloud ou local que vous utilisez, mais une fois que vous avez un ensemble de machines virtuelles avec une connectivité réseau activée entre elles, alors les étapes suivantes pour configurer le package Service Fabric, modifier les paramètres du cluster et exécuter les scripts de création et de gestion du cluster sont similaires. Cela garantit que vos connaissances et votre expérience en matière d'exploitation et de gestion des clusters Service Fabric est transférable lorsque vous choisissez de cibler de nouveaux environnements d'hébergement.

## Avantages de la fonctionnalité de déploiement universel
* Aucune obligation envers un fournisseur.
* Une fois écrites, les applications Service Fabric peuvent être exécutées dans plusieurs environnements d'hébergement sans modification ou avec des modifications minimales.
* Les connaissances en matière de génération d'applications Service Fabric s'appliquent d'une plateforme d'hébergement à d'autres.
* L'expérience opérationnelle d'exécution de clusters Service Fabric s'applique d'un environnement de déploiement à d'autres.
* Ciblage étendu en termes de clientèle sans contrainte d'environnement d'hébergement.
* Couche supplémentaire de fiabilité et de protection contre les pannes répandues en déplaçant les services vers un autre environnement de déploiement si un centre de données ou le cloud fourni est confronté à une panne d'électricité.

## Différences entre les clusters Service Fabric hébergés par Azure et les clusters dotés de l'option de déploiement universel
L'exécution de clusters Service Fabric à l'aide du service hébergé Azure fournit quelques avantages par rapport à l'utilisation de l'option Déploiement universel. Donc, si vous n'avez d'exigences spécifiques en ce qui concerne l'emplacement où vous exécutez vos clusters, alors nous vous suggérons de les exécuter en tant que clusters hébergés par Azure. Dans Azure, nous intégrons d’autres fonctionnalités et services Azure qui simplifient l’exploitation et la gestion du cluster :

* Portail Azure : facilite la création et la gestion de clusters.
* Azure Resource Manager : l’utilisation d’ARM permet de faciliter la gestion de toutes les ressources utilisées par le cluster en tant qu’unité et de simplifier le suivi des coûts et la facturation. 
* Diagnostics : dans Azure, nous proposons l’intégration de WAD (Windows Azure Diagnostics) et d’Operational Insights pour les diagnostics.
* Mise à l’échelle automatique : nous fournissons une fonctionnalité de mise à l’échelle automatique intégrée pour les clusters hébergés par Azure. Dans d'autres environnements utilisant la fonctionnalité de déploiement universel, vous devrez créer votre propre fonctionnalité de mise à l'échelle automatique ou vous devrez mettre à l'échelle manuellement à l'aide des API que Service Fabric expose pour la mise à l'échelle des clusters.

<!---HONumber=AcomDC_1203_2015-->