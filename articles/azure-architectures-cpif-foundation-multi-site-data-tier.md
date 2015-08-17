<properties 
   pageTitle="Niveau de données multi-sites (modèles d'architecture Azure)" 
   description="Le modèle du niveau de données fait partie de la Fondation, qui est décrite en détail dans le document de l'architecture CPIF." 
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

# Niveau de données multi-sites (modèles d'architecture Azure)

Le [Framework d’intégration de plateforme dans le cloud (CPIF)](azure-architectures-cpif-overview.md) fournit des conseils d’intégration de charge de travail pour les applications d’accueil dans une solution Microsoft Cloud.

CPIF décrit la manière dont les organisations, les clients et les partenaires doivent concevoir et déployer des charges de travail ciblées dans le cloud en utilisant les fonctionnalités hybrides de gestion et de plateforme dans le cloud d'Azure, de System Center et de Windows Server.

Le modèle du **niveau de données multi-sites** fait partie de la **Fondation**, qui est décrite en détail dans le document sur l’architecture CPIF.

## Niveau de données multi-sites

Le modèle de conception du niveau de données multi-sites présente les fonctionnalités et les services Azure requis pour fournir des services de niveau du web qui peuvent proposer des performances prévisibles, ainsi qu'une haute disponibilité au-delà des limites géographiques. Aux fins de ce modèle de conception, un niveau de données est défini en tant que niveau de service fournissant des services de plateforme de données traditionnels, de manière isolée ou dans le cadre d'une application web multi-niveaux. Dans ce modèle, l'équilibrage de charge du niveau de données est proposé localement, au sein de la région, mais également entre différentes régions.

Proposés par SQL Server 2012, les groupes de disponibilité AlwaysOn représentent une fonctionnalité de haute disponibilité et de reprise après sinistre qui est entièrement prise en charge sur les services d'infrastructure Azure. Des informations détaillées et l'annonce de la prise en charge officielle des groupes de disponibilité AlwaysOn sur Windows Azure Infrastructure Service sont mentionnées dans l'article relatif aux groupes de disponibilité AlwaysOn.

Ce document fournit un aperçu architectural d'un niveau de données multi-sites dans Azure en utilisant des groupes de disponibilité SQL AlwaysOn, ainsi qu'un nœud secondaire facultatif, défini en lecture seule dans un centre de données Azure supplémentaire permettant de bénéficier de fonctionnalités supplémentaires et de la reprise après sinistre. L'utilisation de SQL AlwaysOn dans Azure fournit un niveau de données de haute disponibilité qui peut être exploité par des niveaux du web ou des niveaux d'applications.

Bien que ce document mette l'accent sur des pratiques et des modèles architecturaux, des conseils de déploiement complets sont énoncés dans les didacticiels officiels, qui décrivent la configuration des groupes de disponibilité AlwaysOn dans Azure et la configuration de l'écouteur de groupe de disponibilité AlwaysOn.

## Vue d'ensemble du modèle architectural 

Ce document présente un modèle permettant de fournir un accès au contenu du serveur web dans plusieurs zones géographiques, à des fins de disponibilité et de redondance. Les services essentiels sont illustrés ci-dessous sans prêter attention à l'application ou au niveau du web qui aura accès aux données. Le schéma ci-dessous vous présente les services pertinents et la manière dont ils sont utilisés dans le cadre de ce modèle.

Chaque domaine principal de services est décrit plus en détail dans ce schéma.
 
![Balises des panneaux de ressources et de groupe de ressources](./media/azure-architectures-cpif-foundation-multi-site-data-tier/overview.png)

##  Ressources supplémentaires
[Niveau de données à charge équilibrée (pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41) (en anglais)

## Voir aussi
[Architecture CPIF](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a) (en anglais)

[Niveau global du web à charge équilibrée](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a) (en anglais)

[Mise en réseau hybride](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38) (en anglais)

[Niveau d’Azure Search](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d) (en anglais)

[Niveau de traitement par lots](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1) (en anglais)

<!---HONumber=August15_HO6-->