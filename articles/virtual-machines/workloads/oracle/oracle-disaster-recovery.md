---
title: "Vue d’ensemble du scénario de récupération d’urgence Oracle dans votre environnement Azure | Microsoft Docs"
description: "Scénario de récupération d’urgence d’Oracle Database 12c dans votre environnement Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: v-shiuma
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/2/2017
ms.author: rclaus
ms.translationtype: HT
ms.sourcegitcommit: d941879aee6042b38b7f5569cd4e31cb78b4ad33
ms.openlocfilehash: 4f6fc6ed79606f0510bc3d6258205d1e6105de25
ms.contentlocale: fr-fr
ms.lasthandoff: 07/10/2017

---

# <a name="disaster-recovery-dr-of-oracle-12c-database-on-an-azure-environment"></a>Récupération d’urgence d’Oracle Database 12c dans un environnement Azure

## <a name="assumptions"></a>Hypothèses

- Présentation de la conception d’Oracle Data Guard et de l’environnement Azure


## <a name="goals"></a>Objectifs
- Concevoir la topologie et la configuration adaptées à vos besoins de récupération d’urgence.

## <a name="scenario-1-primary-and-dr-sites-on-azure"></a>Scénario 1 (site principal et site de récupération d’urgence sur Azure)

Un client dispose d’une configuration de base de données Oracle sur le site principal. Le site de récupération d’urgence se trouve dans une autre région. Oracle Data Guard permet une récupération rapide entre ces sites. Le site principal possède également une base de données secondaire pour la création de rapports et d’autres utilisations. 

### <a name="topology"></a>Topologie

Voici un résumé de la configuration Azure.

- Deux sites (site principal et site de récupération d’urgence)
- Deux réseaux virtuels
- Deux bases de données Oracle avec Data Guard (primaire et secondaire)
- Deux bases de données Oracle avec Golden Gate ou Data Guard (site principal uniquement)
- Deux services d’application sur le site principal et un sur le site de récupération d’urgence
- Un « groupe à haute disponibilité » est utilisé pour la base de données et le service d’application sur le site principal
- Une jumpbox sur chaque site, qui limite l’accès au réseau privé, autorise uniquement l’administrateur à se connecter
- La jumpbox, le service d’application, la base de données et la passerelle VPN se trouvent sur des sous-réseaux distincts
- Le groupe de sécurité réseau est appliqué sur les sous-réseaux d’application et de base de données

![Capture d’écran de la page de topologie de récupération d’urgence](./media/oracle-disaster-recovery/oracle_topology_01.png)

## <a name="scenario-2-primary-site-on-premises-and-dr-site-on-azure"></a>Scénario 2 (site principal local et site de récupération d’urgence sur Azure)

Un client dispose d’une configuration de base de données Oracle locale (site principal). Le site de récupération d’urgence est sur Azure. Oracle Data Guard permet une récupération rapide entre ces sites. Le site principal possède également une base de données secondaire pour la création de rapports et d’autres utilisations. 

Deux approches sont possibles pour cette configuration.

### <a name="1-direct-connections-between-on-premises-and-azure-required-open-tcp-ports-on-firewall-this-approach-is-not-recommended-as-it-expose-the-tcp-ports-to-outside-world"></a>1. Connexions directes entre site local et site sur Azure, ports TCP ouverts requis sur le pare-feu. Cette approche n’est pas recommandée, car elle expose les ports TCP aux dangers extérieurs.

### <a name="topology"></a>Topologie

Voici un résumé de la configuration Azure.

- Un site (site de récupération d’urgence)
- Un réseau virtuel
- Une base de données Oracle avec Data Guard (active)
- Un service d’application sur le site de récupération d’urgence
- Une jumpbox, qui limite l’accès au réseau privé et autorise uniquement l’administrateur à se connecter
- La jumpbox, le service d’application, la base de données et la passerelle VPN se trouvent sur des sous-réseaux distincts
- Le groupe de sécurité réseau est appliqué sur les sous-réseaux d’application et de base de données
- Ajouter la règle/stratégie de groupe de sécurité réseau pour autoriser le port TCP entrant 1521 (ou défini par l’utilisateur)
- Ajouter la stratégie/règle de groupe de sécurité réseau pour limiter l’accès du réseau virtuel à l’adresse IP/aux adresses locales (base de données ou application) uniquement.

![Capture d’écran de la page de topologie de récupération d’urgence](./media/oracle-disaster-recovery/oracle_topology_02.png)

### <a name="2-a-better-approach-is-using-site-to-site-vpn-more-information-about-setting-up-vpn-is-available-from-this-linkhttpsdocsmicrosoftcomen-usazurevpn-gatewayvpn-gateway-howto-site-to-site-resource-manager-cli"></a>2. La meilleure approche consiste à utiliser des VPN de site à site. Pour plus d’informations sur la configuration du réseau VPN, cliquez sur ce [lien](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli)

### <a name="topology"></a>Topologie

Voici un résumé de la configuration Azure.

- Un site (site de récupération d’urgence)
- Un réseau virtuel
- Une base de données Oracle avec Data Guard (active)
- Un service d’application sur le site de récupération d’urgence
- Une jumpbox, qui limite l’accès au réseau privé et autorise uniquement l’administrateur à se connecter
- La jumpbox, le service d’application, la base de données et la passerelle VPN se trouvent sur des sous-réseaux distincts
- Le groupe de sécurité réseau est appliqué sur les sous-réseaux d’application et de base de données
- Connexion VPN de site à site entre un site local et un site sur Azure

![Capture d’écran de la page de topologie de récupération d’urgence](./media/oracle-disaster-recovery/oracle_topology_03.png)

## <a name="additional-readings"></a>Documentation supplémentaire :

- [Design and implement Oracle database on Azure](oracle-design.md) (Concevoir et mettre en œuvre une base de données Oracle sur Azure)
- [Implémenter Oracle Data Guard sur une machine virtuelle Linux Azure](configure-oracle-dataguard.md)
- [Implémenter Oracle Golden Gate sur une machine virtuelle Linux Azure](configure-oracle-golden-gate.md)
- [Sauvegarder et récupérer une base de données de la base de données Oracle Database 12c sur une machine virtuelle Linux Azure](oracle-backup-recovery.md)


## <a name="next-steps"></a>Étapes suivantes

[Didacticiel : créer des machines virtuelles hautement disponibles](../../linux/create-cli-complete.md)

[Explorer des exemples Azure CLI de déploiement de machines virtuelles](../../linux/cli-samples.md)

