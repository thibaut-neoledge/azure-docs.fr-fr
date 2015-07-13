<properties 
   pageTitle="Niveau d'Azure Search (modèles d'architecture Azure)" 
   description="Le modèle du niveau d'Azure Search fait partie de la Fondation, qui est décrite en détail dans le document d'architecture CPIF" 
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

# Niveau d'Azure Search (modèles d'architecture Azure)

Le [Framework d’intégration de plateforme dans le cloud (CPIF)](azure-architectures-cpif-overview.md) fournit des conseils d’intégration de charge de travail pour les applications d’accueil dans une solution Microsoft Cloud.

CPIF décrit la manière dont les organisations, les clients et les partenaires doivent concevoir et déployer des charges de travail ciblées dans le cloud en utilisant les fonctionnalités hybrides de gestion et de plateforme dans le cloud d'Azure, de System Center et de Windows Server.

Le modèle du **niveau d’Azure Search** fait partie de la **Fondation**, qui est décrite en détail dans le document sur l’architecture CPIF.

##  Niveau d'Azure Search

Le modèle de conception du niveau d'Azure Search présente les fonctionnalités et les services Azure requis pour fournir des services de recherche qui peuvent proposer des performances prévisibles, une haute disponibilité au-delà des limites géographiques, ainsi qu'un modèle architectural permettant d'utiliser Azure Search dans le but de fournir une solution de recherche. Azure Search est une « recherche en tant que service », intégrée à Microsoft Azure, qui permet aux développeurs d'intégrer des fonctionnalités de recherche dans des applications sans avoir à déployer, gérer ou maintenir des services d'infrastructure pour fournir cette fonctionnalité à des applications. Le but de ce modèle est de fournir une solution reproductible, destinée à être utilisée dans différents types de situations et de conceptions.

## Vue d'ensemble du modèle architectural 

Ce document présente un modèle de base permettant d'utiliser Azure Search à l'aide de deux variations de cœur, dans le but de démontrer la diversité architecturale du service. Le modèle de base est constitué d'Azure Search et de services voisins Azure. Il est destiné à fournir des conseils pour créer des conceptions de bout en bout. Les variations du modèle, en particulier le service partagé et les modèles de concurrence, sont également incluses dans cette section pour fournir des conseils basés sur des exigences différentes, des contrats de niveau de service et d'autres conditions spécifiques.

##  Ressources supplémentaires
[Niveau d’Azure Search (pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d) (en anglais)

## Voir aussi
[Architecture CPIF](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a) (en anglais)

[Niveau global du web à charge équilibrée](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a) (en anglais)

[Niveau de données à charge équilibrée](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41) (en anglais)

[Mise en réseau hybride](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38) (en anglais)

[Niveau de traitement par lots](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1) (en anglais)

<!---HONumber=July15_HO1-->