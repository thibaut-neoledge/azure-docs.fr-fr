<properties 
   pageTitle="Mise en réseau hybride (modèles d'architecture Azure)" 
   description="La mise en réseau hybride fait partie de l'Infrastructure, qui est décrite en détail dans le document de l'architecture CPIF." 
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

# Mise en réseau hybride (modèles d'architecture Azure)

Le [Framework d’intégration de plateforme dans le cloud (CPIF)](azure-architectures-cpif-overview.md) fournit des conseils d’intégration de charge de travail pour les applications d’accueil dans une solution Microsoft Cloud.

CPIF décrit la manière dont les organisations, les clients et les partenaires doivent concevoir et déployer des charges de travail ciblées dans le cloud en utilisant les fonctionnalités hybrides de gestion et de plateforme dans le cloud d'Azure, de System Center et de Windows Server.

La modèle de **mise en réseau hybride** fait partie de l’**Infrastructure**, qui est décrite en détail dans le document sur l’architecture CPIF.

##  Mise en réseau hybride

Le modèle de conception de la mise en réseau hybride présente les fonctionnalités et les services Azure requis pour fournir des fonctionnalités de réseau, qui peuvent proposer des performances prévisibles ainsi qu'une haute disponibilité au-delà des limites géographiques. Une liste complète des régions de Microsoft Azure et des services proposés dans chacune d'entre elles est disponible sur le site de documentation Microsoft Azure. Ce document fournit une vue d'ensemble des fonctionnalités de mise en réseau de Microsoft Azure pour les environnements hybrides. La mise en réseau virtuelle Microsoft Azure permet de créer des réseaux isolés, de manière logique, dans Azure et de les connecter en toute sécurité à votre centre de données sur site, sur Internet ou à l'aide d'une connexion de réseau privé. En outre, les machines clientes individuelles peuvent se connecter à un réseau Azure isolé à l'aide d'une connexion VPN IPsec.

Le modèle d'architecture de mise en réseau hybride inclut les centres d'intérêt suivants :

- Connexion aux réseaux locaux dans Azure 
- Extension des réseaux virtuels Azure entre les différentes régions 
- Extension des réseaux virtuels Azure entre les différents abonnements 
- Attribution d'un accès réseau à distance destiné aux développeurs 

## Vue d'ensemble du modèle architectural 

Le modèle d'architecture de mise en réseau hybride est particulièrement complexe en raison du nombre de scénarios qui peuvent être créés. Ce modèle architectural se concentrera sur les quatre scénarios suivants :

- Mise en réseau hybride de site à site avec routage de réseau virtuel sur plusieurs tronçons, dans un seul abonnement et une seule région. 
- Mise en réseau hybride de site à site avec routage de réseau virtuel sur plusieurs tronçons entre plusieurs abonnements et plusieurs régions. 
- Mise en réseau hybride ExpressRoute à l'aide de la connectivité MPLS 
- Mise en réseau hybride ExpressRoute à l'aide de la connectivité IXP 

##  Ressources supplémentaires
[Mise en réseau hybride (pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38) (en anglais)

## Voir aussi
[Architecture CPIF](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a) (en anglais)

[Niveau global du web à charge équilibrée](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a) (en anglais)

[Niveau de données à charge équilibrée](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41) (en anglais)

[Niveau d’Azure Search](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d) (en anglais)

[Niveau de traitement par lots](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1) (en anglais)

<!---HONumber=July15_HO4-->