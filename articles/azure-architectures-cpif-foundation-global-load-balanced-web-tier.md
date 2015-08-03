<properties 
   pageTitle="Niveau global du web à charge équilibrée (modèles d'architecture Azure)" 
   description="Le modèle du niveau global du web à charge équilibrée fait partie de la Fondation, qui est décrite en détail dans le document de l'architecture CPIF" 
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

# Niveau global du web à charge équilibrée (modèles d'architecture Azure)

Le [Framework d’intégration de plateforme dans le cloud (CPIF)](azure-architectures-cpif-overview.md) fournit des conseils d’intégration de charge de travail pour les applications d’accueil dans une solution Microsoft Cloud.

CPIF décrit la manière dont les organisations, les clients et les partenaires doivent concevoir et déployer des charges de travail ciblées dans le cloud en utilisant les fonctionnalités hybrides de gestion et de plateforme dans le cloud d'Azure, de System Center et de Windows Server.

Le modèle du **niveau global du web à charge équilibrée** fait partie de la **Fondation**, qui est décrite en détail dans le document sur l’architecture CPIF.

##  Niveau global du web à charge équilibrée

Le modèle de conception du niveau global du web à charge équilibrée présente les fonctionnalités et les services Azure requis pour fournir des services de niveau du web qui peuvent proposer des performances prévisibles, ainsi qu'une haute disponibilité au-delà des limites géographiques.

Aux fins de ce modèle de conception, un niveau du web est défini en tant que niveau de service fournissant du contenu HTTP/HTTPS ou des services d'applications traditionnels, de manière isolée ou dans le cadre d'une application web multi-niveaux. Dans ce modèle, l'équilibrage de charge du niveau du web est proposé localement, au sein de la région, mais également entre différentes régions. D'un point de vue informatique, ces services peuvent être fournis par des machines virtuelles Microsoft Azure, des sites web ou une combinaison des deux. Les machines virtuelles fournissant des services web peuvent héberger du contenu via n'importe quel système d'exploitation « invité » Microsoft Windows ou Linux, pris en charge dans Microsoft Azure.


## Vue d'ensemble du modèle architectural 

Ce document présente un modèle permettant de fournir un accès à des services web ou au contenu du serveur web dans plusieurs zones géographiques, à des fins de disponibilité et de redondance. Les services essentiels sont illustrés ci-dessous, mais ne présentent pas les contraintes de la plateforme web ou la méthodologie de développement du service web. Il existe deux variations à ce modèle - celle qui héberge le contenu ou les services web sur des machines virtuelles (via les systèmes d'exploitation pris en charge par Azure et les familles) et celle qui utilise Azure Websites. Le schéma ci-dessous vous présente les services pertinents et la manière dont ils sont utilisés dans le cadre de ce modèle, en utilisant l'exemple des machines virtuelles.

##  Ressources supplémentaires
[Niveau global du web à charge équilibrée (pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a) (en anglais)

## Voir aussi
[Architecture CPIF](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a) (en anglais)

[Niveau de données à charge équilibrée](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41) (en anglais)

[Mise en réseau hybride](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38) (en anglais)

[Niveau d’Azure Search](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d) (en anglais)

[Niveau de traitement par lots](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1) (en anglais)

<!---HONumber=July15_HO4-->