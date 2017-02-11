---
title: "Procédure pas à pas d’exemple d’infrastructure Azure | Microsoft Docs"
description: "Découvrez-en plus sur les principales instructions de conception et d’implémentation pour le déploiement d’un exemple d’infrastructure dans Azure."
documentationcenter: 
services: virtual-machines-linux
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 281fc2c0-b533-45fa-81a3-728c0049c73d
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 70e52cefccee4b963e512724b96525bcbacf330c
ms.openlocfilehash: e8ef1e52dec358fae00096df26b16fb9e3f46c2b


---
# <a name="example-azure-infrastructure-walkthrough"></a>Procédure pas à pas d’exemple d’infrastructure Azure
[!INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)]

Cet article vous guide à travers la création d’un exemple d’infrastructure d’application. Nous détaillons la conception d’une infrastructure pour un magasin en ligne simple qui réunit toutes les instructions et les décisions concernant les conventions de dénomination, les groupes à haute disponibilité, les réseaux virtuels et équilibreurs de charge, ainsi que le déploiement de vos machines virtuelles.

## <a name="example-workload"></a>Exemple de charge de travail
Adventure Works Cycles souhaite créer une application de magasin en ligne dans Azure, avec :

* Deux serveurs nginx exécutant le client frontal dans une couche web
* Deux serveurs nginx pour le traitement des données et des commandes dans une couche d’application
* Deux serveurs MongoDB faisant partie d’un cluster partitionné pour le stockage des données sur les produits et des commandes dans une couche de base de données
* Deux contrôleurs de domaine Active Directory pour les comptes clients et les fournisseurs dans une couche d’authentification
* Tous les serveurs se trouvent dans deux sous-réseaux :
  * un sous-réseau frontal pour les serveurs web 
  * un sous-réseau principal pour les serveurs d’applications, le cluster MongoDB et les contrôleurs de domaine

![Diagramme de différentes couches pour l’infrastructure d’applications](./media/virtual-machines-common-infrastructure-service-guidelines/example-tiers.png)

La charge du trafic web entrant sécurisé doit être répartie sur les serveurs web lorsque les clients parcourent le magasin en ligne. Le trafic de traitement des commandes sous la forme de requêtes HTTP provenant des serveurs web doit être à charge équilibrée sur les serveurs d’applications. En outre, l’infrastructure doit être conçue pour la haute disponibilité.

La conception qui en résulte doit comprendre :

* Un compte et un abonnement Azure
* un seul groupe de ressources.
* Comptes de stockage
* un réseau virtuel avec deux sous-réseaux ;
* Des groupes à haute disponibilité pour machines virtuelles avec un rôle similaire
* Machines virtuelles

Tous les éléments ci-dessus sont conformes aux conventions de dénomination :

* Adventure Works Cycles utilise **[Charge de travail informatique]-[Emplacement]-[Ressources Azure]** comme préfixe
  * Pour cet exemple, « **azos** » (Azure On-line Store) est le nom de la charge de travail informatique et « **use** » (États-Unis de l’Est 2) est l’emplacement
* Les comptes de stockage utilisent adventureazosusesa**[description]**
  * Le mot « adventure » a été ajouté au préfixe pour garantir l’unicité et les noms de compte de stockage ne prennent pas en charge l’utilisation de traits d’union.
* Les réseaux virtuels utilisent AZOS-USE-VN**[numéro]**
* Les groupes à haute disponibilité utilisent azos-use-as-**[rôle]**
* Les noms de machine virtuelle utilisent azos-use-vm-**[nom de machine virtuelle]**

## <a name="azure-subscriptions-and-accounts"></a>Abonnements et comptes Azure
Adventure Works Cycles utilise son abonnement d’entreprise, nommé Adventure Works Enterprise Subscription, pour fournir des informations de facturation pour cette charge de travail informatique.

## <a name="storage-accounts"></a>Comptes de stockage
Adventure Works Cycles a déterminé que deux comptes de stockage sont nécessaires :

* **adventureazosusesawebapp** pour le stockage standard de serveurs Web, de serveurs d’applications et de contrôleurs de domaine avec leurs disques de données.
* **adventureazosusesadbclust** pour le stockage Premium des serveurs de cluster partitionné MongoDB et leurs disques de données.

## <a name="virtual-network-and-subnets"></a>Réseau virtuel et sous-réseaux
Étant donné que le réseau virtuel n’a pas besoin d’une connectivité continue au réseau Adventure Work Cycles local, il a été décidé d’adopter un réseau virtuel cloud.

Un réseau virtuel cloud a été créé avec les paramètres suivants via le portail Azure :

* Nom : AZOS-USE-VN01
* Emplacement : East US 2
* Espace d’adressage du réseau virtuel : 10.0.0.0/8
* Premier sous-réseau :
  * Nom : FrontEnd
  * Espace d’adressage : 10.0.1.0/24
* Second sous-réseau :
  * Nom : BackEnd
  * Espace d’adressage : 10.0.2.0/24

## <a name="availability-sets"></a>Groupes à haute disponibilité
Pour assurer une haute disponibilité sur les quatre couches de son magasin en ligne, Adventure Work Cycles a adopté quatre groupes à haute disponibilité :

* **azos-use-as-web** pour les serveurs web
* **azos-use-as-app** pour les serveurs d’application
* **azos-use-as-db** pour les serveurs du cluster partitionné MongoDB
* **azos-use-as-dc** pour les contrôleurs de domaine

## <a name="virtual-machines"></a>Machines virtuelles
Adventure Works Cycles a donné les noms suivants à ses machines virtuelles Azure :

* **azos-use-vm-web01** pour le premier serveur web
* **azfae-vm-vm-web02** pour le second serveur web
* **azos-use-vm-app01** pour le premier serveur d’applications
* **azos-use-vm-app02** pour le second serveur d’applications
* **azos-use-vm-db01** pour le premier serveur MongoDB dans le cluster
* **azos-use-vm-db02** pour le second serveur MongoDB dans le cluster
* **azos-use-vm-dc01** pour le premier contrôleur de domaine
* **azos-use-vm-dc02** pour le second contrôleur de domaine

Voici la configuration obtenue.

![Infrastructure d’applications finale déployée dans Azure](./media/virtual-machines-common-infrastructure-service-guidelines/example-config.png)

Cette configuration comprend :

* un réseau virtuel cloud avec deux sous-réseaux (FrontEnd et BackEnd) ;
* deux comptes de stockage ;
* quatre groupes à haute disponibilité, un pour chaque niveau du magasin en ligne
* les machines virtuelles pour les quatre niveaux ;
* un jeu d’équilibrage de charge externe pour le trafic Web basé sur HTTPS depuis Internet vers les serveurs web ;
* un jeu d’équilibrage de charge interne pour le trafic Web non crypté depuis les serveurs Web vers les serveurs d’applications.
* un seul groupe de ressources.

## <a name="next-steps"></a>Étapes suivantes
[!INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)]




<!--HONumber=Feb17_HO2-->


