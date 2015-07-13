<properties 
   pageTitle="Niveau de traitement par lots hors site (modèles d'architecture Azure)" 
   description="Le modèle du niveau de traitement par lots hors site fait partie de l'Infrastructure, qui est décrite en détail dans le document d'architecture CPIF" 
   services="" 
   documentationCenter="" 
   authors="arynes" 
   manager="fredhar" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple" 
   ms.date="03/25/2015"
   ms.author="arynes"/>

# Niveau de traitement par lots hors site (modèles d'architecture Azure)

Le [Framework d’intégration de plateforme dans le cloud (CPIF)](azure-architectures-cpif-overview.md) fournit des conseils d’intégration de charge de travail pour les applications d’accueil dans une solution Microsoft Cloud.

CPIF décrit la manière dont les organisations, les clients et les partenaires doivent concevoir et déployer des charges de travail ciblées dans le cloud en utilisant les fonctionnalités hybrides de gestion et de plateforme dans le cloud d'Azure, de System Center et de Windows Server.

Le modèle du **niveau de traitement par lots hors site** fait partie de l’**Infrastructure**, qui est décrite en détail dans le document sur l’architecture CPIF.

##  Niveau de traitement par lots hors site

Le modèle de conception du niveau de traitement par lots hors site présente les fonctionnalités et les services Azure requis pour assurer le traitement principal de données, qui est à la fois tolérant aux pannes et évolutif. Ces services sont exécutés en tant que rôles de travail dans les services de cloud computing sur Azure, qui peuvent actuellement être déployés dans n'importe quel centre de données Azure.

Les charges de travail de traitement par lots sont particulières, car elles disposent généralement d'une interface utilisateur très limitée, voire inexistante. Nous pourrions prendre pour exemple, pour ce type de charge de travail, un service Windows s'exécutant sur Windows Server. Lorsque vous envisagez ce type de charge de travail dans un environnement de cloud computing, il est peu rentable de déployer tout un serveur pour exécuter une charge de travail, à des fins de calcul, de stockage et de connectivité réseau. Le rôle de travail représente sa mise en œuvre sur Azure.

Par définition, un travail de traitement par lots exécuté dans Azure désigne une charge de travail qui se connecte à une ressource, fournit une certaine logique métier (informatique) et un certain résultat. Les ressources d'entrée et de sortie sont définies par l'utilisateur et peuvent exister sous la forme de fichiers plats, d'objets blob dans le stockage d'objets blob Azure, d'une base de données NoSQL ou de bases de données relationnelles.

La logique métier est implémentée dans un rôle de travail Azure, en définissant généralement la logique métier requise dans une bibliothèque .NET. Bien que le déploiement d'un rôle de travail vers Azure soit assez simple, le déploiement d'un rôle de travail évolutif et tolérant aux pannes nécessite une conception qui prend en compte la manière dont ce service est exécuté et maintenu dans Azure. Ce schéma vous présente la conception qui tient compte de ces exigences et vous explique comment celles-ci peuvent être mises en œuvre.

## Vue d'ensemble du modèle architectural 

Ce document présente un modèle de traitement par lots hors site utilisant des instances de rôle de travail contenues dans un service de cloud computing dans Azure. Les éléments essentiels de cette conception sont illustrés ci-dessous. Ce schéma vous présente les instances minimales requises, qui vous permettent de bénéficier d'une tolérance aux pannes. Des instances supplémentaires peuvent être déployées pour améliorer les performances du service. En outre, la mise à l'échelle automatique peut être activée pour vous permettre de dimensionner des instances à l'heure ou via d'autres métriques de serveur.

##  Ressources supplémentaires
[Niveau de traitement par lots (pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1) (en anglais)

## Voir aussi
[Architecture CPIF](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a) (en anglais)

[Niveau global du web à charge équilibrée](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a) (en anglais)

[Niveau de données à charge équilibrée](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41) (en anglais)

[Mise en réseau hybride](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38) (en anglais)

[Niveau d’Azure Search](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d) (en anglais)

<!---HONumber=July15_HO1-->