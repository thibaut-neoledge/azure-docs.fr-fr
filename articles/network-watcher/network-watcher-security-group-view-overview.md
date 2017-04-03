---
title: "Présentation de l’affichage des groupes de sécurité dans Azure Network Watcher | Microsoft Docs"
description: "Cette page fournit une vue d’ensemble de la fonctionnalité d’affichage des groupes de sécurité de Network Watcher."
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: ad27ab85-9d84-4759-b2b9-e861ef8ea8d8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: de48da2c77c65f3e806474ae37ffd00772622b02
ms.lasthandoff: 03/04/2017

---

# <a name="introduction-to-network-security-group-view-in-azure-network-watcher"></a>Présentation de l’affichage des groupes de sécurité réseau dans Azure Network Watcher

Le groupe de sécurité réseau peut être associé à un niveau de sous-réseau ou à un niveau de carte réseau. Lorsqu’il est associé à un niveau de sous-réseau, il s’applique à toutes les instances de machine virtuelle du sous-réseau. L’affichage des groupes de sécurité réseau renvoie toutes les règles et tous les groupes de sécurité réseau configurés qui sont associés à un niveau de sous-réseau et de carte réseau. En outre, les règles de sécurité effectives sont renvoyées pour chacune des cartes réseau d’une machine virtuelle. L’affichage du groupe de sécurité réseau vous permet de déterminer les vulnérabilités réseau d’une machine virtuelle, telles que les ports ouverts. Vous pouvez également valider si votre groupe de sécurité réseau fonctionne comme prévu en comparant les règles de sécurité effectives avec celles configurées.

Un cas d’utilisation plus poussée concerne l’audit et la conformité de la sécurité. Vous pouvez définir un ensemble normatif de règles de sécurité comme modèle pour la gouvernance de la sécurité de votre organisation. Un audit de conformité périodique peut être implémenté de façon programmatique en comparant les règles normatives avec les règles effectives pour toutes les machines virtuelles de votre réseau.

Dans le portail, les règles sont divisées en catégories : Effectives, Sous-réseau, Interface réseau et Par défaut. Cela permet de savoir facilement quelles sont les règles appliquées à une machine virtuelle. Un bouton de téléchargement est fourni pour télécharger simplement toutes les règles de sécurité, quel que soit l’onglet, dans un fichier CSV.

![affichage des groupes de sécurité][1]

Les règles peuvent être sélectionnées et un nouveau panneau s’ouvre pour afficher le groupe de sécurité réseau et les préfixes source et de destination. Dans ce panneau, vous pouvez naviguer directement vers la ressource de groupe de sécurité réseau.

![exploration][2]

### <a name="next-steps"></a>Étapes suivantes

Découvrez comment effectuer un audit de vos paramètres de groupe de sécurité réseau en consultant [Automate NSG auditing with Azure Network Watcher Security group view](network-watcher-nsg-auditing-powershell.md) (Automatiser l’audit des groupes de sécurité réseau avec l’affichage des groupes de sécurité réseau Azure Network Watcher)

[1]: ./media/network-watcher-security-group-view-overview/securitygroupview.png
[2]: ./media/network-watcher-security-group-view-overview/figure1.png










