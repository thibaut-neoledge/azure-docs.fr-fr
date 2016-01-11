<properties
   pageTitle="Déploiement universel avec Azure Service Fabric sur Windows Server et Linux | Microsoft Azure"
   description="Les clusters Service Fabric peuvent être exécutés sur Windows Server et Linux, ce qui vous permet de déployer et d’héberger des applications Service Fabric partout où vous pouvez exécuter Windows Server et Linux."
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
Avec l’ajout du « déploiement universel », Azure Service Fabric autorise la création de clusters Service Fabric sur les machines virtuelles ou les ordinateurs exécutant Windows Server ou Linux. Cela signifie que vous pouvez déployer et exécuter des applications Service Fabric dans n’importe quel environnement, que ce soit en local ou avec un fournisseur cloud, où vous avez un ensemble d’ordinateurs Windows Server ou Linux interconnectés.

 Service Fabric fournit un package d’installation pour créer des clusters Service Fabric. L’avantage de la fonctionnalité de déploiement universel est qu’il n’existe aucune obligation en termes de fournisseur lorsque vous générez une application à l’aide de Service Fabric dans la mesure où vous choisissez où ces applications s’exécutent. Cette fonctionnalité augmente également votre chance d’atteindre un ensemble plus large de clients étant donné que des clients différents peuvent avoir des exigences différentes pour les environnements où ils souhaitent exécuter vos applications. Par exemple, les clients du secteur financier et du secteur de la santé peuvent avoir des besoins différents de ceux d’un constructeur automobile ou d’une agence de voyages.

La version d'évaluation technique de cette fonctionnalité devrait être publiée au premier trimestre 2016.

## Systèmes d’exploitation pris en charge
Vous pourrez créer des clusters sur des machines virtuelles ou des ordinateurs qui exécutent ces systèmes d’exploitation : * Windows Server 2012 R2 * Windows Server 2016 * Linux

## Langages de programmation pris en charge
Vous pourrez écrire des applications Service Fabric avec un langage de programmation : * C# * Java

## Création et configuration du cluster
Service Fabric fournit un package d’installation que vous pouvez télécharger à partir du Centre de téléchargement Microsoft. Une fois que vous avez téléchargé ce package, vous devez apporter des modifications à un fichier de configuration pour spécifier les paramètres de votre cluster. Une fois que les paramètres du cluster ont été modifiés, vous exécutez un script d’installation qui crée le cluster couvrant les ordinateurs que vous avez spécifiés dans les paramètres de votre cluster.

Les détails exacts du processus d’installation seront communiqués lors du lancement de la version préliminaire de cette fonctionnalité au premier trimestre 2016.

## Déploiements cloud ou en local
Le processus de création d’un cluster Service Fabric en local est similaire au processus de création d’un cluster sur un cloud de votre choix avec un ensemble de machines virtuelles. Les étapes initiales d’approvisionnement des machines virtuelles sont régies par l’environnement cloud ou local que vous utilisez. Une fois que vous avez un ensemble de machines virtuelles avec une connectivité réseau activée entre elles, les étapes suivantes pour configurer le package Service Fabric, modifier les paramètres du cluster et exécuter les scripts de création et de gestion du cluster sont similaires. Cela garantit que vos connaissances et votre expérience en matière d’exploitation et de gestion des clusters Service Fabric est transférable lorsque vous choisissez de cibler de nouveaux environnements d’hébergement.

## Avantages de la fonctionnalité de déploiement universel
* Étant donné qu’il n’y a aucun verrou de fournisseur, vous pouvez choisir l’endroit où les applications s’exécutent.
* Une fois écrites, les applications Service Fabric peuvent être exécutées dans plusieurs environnements d’hébergement sans modification ou avec des modifications minimales.
* Les connaissances en matière de génération d'applications Service Fabric s'appliquent d'une plateforme d'hébergement à d'autres.
* L'expérience opérationnelle d'exécution de clusters Service Fabric s'applique d'un environnement de déploiement à d'autres.
* Ciblage étendu en termes de clientèle sans contrainte d’environnement d’hébergement.
* Une couche supplémentaire de fiabilité et de protection contre les pannes répandues existe, car vous pouvez déplacer les services vers un autre environnement de déploiement si un centre de données ou le fournisseur de services cloud est confronté à une panne d’électricité.

## Avantages des clusters gérés Service Fabric sur Azure par rapport aux clusters Service Fabric créés et gérés à l’aide du déploiement universel
L’exécution de clusters Service Fabric sur Azure fournit quelques avantages par rapport à l’utilisation de l’option Déploiement universel. Par conséquent, si vous n’avez pas d’exigences spécifiques en ce qui concerne l’emplacement où vous exécutez vos clusters, nous vous suggérons de les exécuter en tant que clusters hébergés par Azure. Dans Azure, nous intégrons d’autres fonctionnalités et services Azure qui simplifient l’exploitation et la gestion du cluster :

* **Portail Azure :** facilite la création et la gestion de clusters.
* **Azure Resource Manager :** l’utilisation d’Azure Resource Manager permet de faciliter la gestion de toutes les ressources utilisées par le cluster en tant qu’unité et de simplifier le suivi des coûts et la facturation.
* **Diagnostics :** dans Azure, nous proposons l’intégration d’Azure Diagnostics et d’Operational Insights.
* **Mise à l’échelle automatique :** pour les clusters sur Azure, nous fournissons une fonctionnalité de mise à l’échelle automatique intégrée. Dans d’autres environnements utilisant la fonctionnalité de déploiement universel, vous devez créer votre propre fonctionnalité de mise à l’échelle automatique ou effectuer une mise à l’échelle manuellement à l’aide des API que Service Fabric expose pour la mise à l’échelle des clusters.

<!---HONumber=AcomDC_1223_2015-->