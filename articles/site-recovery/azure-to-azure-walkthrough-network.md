---
title: "Planifier une mise en réseau pour la réplication de VMware vers Azure avec Site Recovery | Microsoft Docs"
description: "Cet article décrit la planification réseau requise pour la réplication de machines virtuelles Azure avec Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/01/2017
ms.author: sujayt
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: 7b37e853f6b97ba313111f9201f877846a28fae9
ms.contentlocale: fr-fr
ms.lasthandoff: 08/02/2017

---
# <a name="step-3-plan-networking-for-azure-vm-replication"></a>Étape 3 : planifier la mise en réseau pour la réplication de machines virtuelles Azure

Après avoir vérifié les [conditions préalables au déploiement](azure-to-azure-walkthrough-prerequisites.md), lisez cet article pour comprendre les considérations de mise en réseau impliquées dans la réplication et la récupération des machines virtuelles Azure d’une région Azure à l’autre, à l’aide du service Azure Site Recovery. 

- À la fin de cet article, vous devriez avoir compris comment configurer l’accès sortant des machines virtuelles Azure à répliquer, et comment vous connecter à partir de votre site local à ces machines virtuelles.
- Publiez des commentaires au bas de cet article ou posez des questions sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

>[!NOTE]
> La réplication de machines virtuelles Azure avec Site Recovery est actuellement en préversion.



## <a name="network-overview"></a>Présentation du réseau

En général, vos machines virtuelles Azure se trouvent dans un réseau/sous-réseau virtuel Azure, et votre site local est connecté à Azure à l’aide d’Azure ExpressRoute, ou d’une connexion VPN.

Les réseaux sont généralement protégés à l’aide de pare-feu et/ou de groupes de sécurité réseau. Les pare-feu peuvent utiliser des listes d’adresses IP basées sur des URL pour contrôler la connectivité réseau. Les groupes de sécurité réseau utilisent des règles basées sur des plages d’adresses IP. 


Pour que Site Recovery fonctionne comme prévu, vous devez apporter des modifications à la connectivité réseau sortante des machines virtuelles à répliquer. Site Recovery ne prend pas en charge l’utilisation d’un proxy d’authentification pour contrôler la connectivité réseau. Si vous utilisez un proxy d’authentification, la réplication ne peut pas être activée. 


Le diagramme suivant illustre un environnement classique pour une application qui s’exécute sur des machines virtuelles Azure.

![environnement client](./media/azure-to-azure-walkthrough-network/source-environment.png)

**Environnement de machines virtuelles Azure**

Vous pouvez également avoir configuré une connexion à Azure depuis votre site local à l’aide d’ExpressRoute ou d’une connexion VPN. 

![environnement client](./media/azure-to-azure-walkthrough-network/source-environment-expressroute.png)

**Connexion locale à Azure**



## <a name="outbound-connectivity-for-urls"></a>Connectivité sortante pour les URL

Si vous utilisez un proxy de pare-feu basé sur des URL pour contrôler la connectivité sortante, veillez à autoriser les URL utilisées par Site Recovery

**URL** | **Détails**  
--- | ---
*.blob.core.windows.net | Permet d’écrire les données dans le compte de stockage de cache dans la région source à partir de la machine virtuelle.
login.microsoftonline.com | Fournit l’autorisation et l’authentification aux URL du service Site Recovery.
*.hypervrecoverymanager.windowsazure.com | Permet la communication avec le service Site Recovery à partir de la machine virtuelle.
*.servicebus.windows.net | Nécessaire pour que les données de surveillance et de diagnostic Site Recovery puissent être écrites à partir de la machine virtuelle.

## <a name="outbound-connectivity-for-ip-address-ranges"></a>Connectivité sortante pour les plages d’adresses IP

- Vous pouvez créer automatiquement les règles requises sur le groupe de sécurité réseau en téléchargeant et en exécutant [ce script](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702).
- Nous vous recommandons de créer les règles de groupe de sécurité réseau requises sur un groupe de sécurité réseau de test, et de vérifier qu’il n’y a aucun problème avant de créer les règles sur un groupe de sécurité réseau de production.
- Pour créer le nombre nécessaire de règles de groupe de sécurité réseau, vérifiez que votre abonnement figure dans la liste verte. Contactez le support pour augmenter la limite du nombre de règles de groupe de sécurité réseau dans votre abonnement.
Si vous utilisez un proxy de pare-feu quelconque basé sur l’adresse IP ou des règles de groupe de sécurité réseau pour contrôler la connectivité sortante, les plages d’adresses IP suivantes doivent figurer dans la liste verte, en fonction des emplacements source et cible des machines virtuelles :

    - Toutes les plages d’adresses IP qui correspondent à l’emplacement source. Téléchargez les [plages](https://www.microsoft.com/download/confirmation.aspx?id=41653).) L’inclusion dans la liste verte est nécessaire pour que les données puissent être écrites dans le compte de stockage de cache à partir de la machine virtuelle.
    - Toutes les plages d’adresses IP qui correspondent aux [points de terminaison IP V4 d’authentification et d’identité](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity) Office 365. Si de nouvelles adresses IP sont ajoutées aux plages d’adresses IP Office 365, vous devez créer des règles de groupe de sécurité réseau.
-     Adresses IP des points de terminaison du service Site Recovery ([disponibles dans un fichier XML](https://aka.ms/site-recovery-public-ips)), qui dépendent de votre emplacement cible : 

   **Emplacement cible** | **Adresses IP du service Site Recovery** |  **Adresse IP de surveillance Site Recovery**
   --- | --- | ---
   Est de l'Asie | 52.175.17.132</br>40.83.121.61 | 13.94.47.61
   Asie du Sud-Est | 52.187.58.193</br>52.187.169.104 | 13.76.179.223
   Inde centrale | 52.172.187.37</br>52.172.157.193 | 104.211.98.185
   Inde du Sud | 52.172.46.220</br>52.172.13.124 | 104.211.224.190
   États-Unis - partie centrale septentrionale | 23.96.195.247</br>23.96.217.22 | 168.62.249.226
   Europe du Nord | 40.69.212.238</br>13.74.36.46 | 52.169.18.8
   Europe de l’Ouest | 52.166.13.64</br>52.166.6.245 | 40.68.93.145
   Est des États-Unis | 13.82.88.226</br>40.71.38.173 | 104.45.147.24
   Ouest des États-Unis | 40.83.179.48</br>13.91.45.163 | 104.40.26.199
   États-Unis - partie centrale méridionale | 13.84.148.14</br>13.84.172.239 | 104.210.146.250
   Centre des États-Unis | 40.69.144.231</br>40.69.167.116 | 52.165.34.144
   Est des États-Unis 2 | 52.184.158.163</br>52.225.216.31 | 40.79.44.59
   Est du Japon | 52.185.150.140</br>13.78.87.185 | 138.91.1.105
   Ouest du Japon | 52.175.146.69</br>52.175.145.200 | 138.91.17.38
   Sud du Brésil | 191.234.185.172</br>104.41.62.15 | 23.97.97.36
   Est de l’Australie | 104.210.113.114</br>40.126.226.199 | 191.239.64.144
   Sud-est de l’Australie | 13.70.159.158</br>13.73.114.68 | 191.239.160.45
   Centre du Canada | 52.228.36.192</br>52.228.39.52 | 40.85.226.62
   Est du Canada | 52.229.125.98</br>52.229.126.170 | 40.86.225.142
   Centre-Ouest des États-Unis | 52.161.20.168</br>13.78.230.131 | 13.78.149.209
   Ouest des États-Unis 2 | 52.183.45.166</br>52.175.207.234 | 13.66.228.204
   Ouest du Royaume-Uni | 51.141.3.203</br>51.140.226.176 | 51.141.14.113
   Sud du Royaume-Uni | 51.140.43.158</br>51.140.29.146 | 51.140.189.52

## <a name="example-nsg-configuration"></a>Exemple de configuration de groupe de sécurité réseau

Cette section explique comment configurer les règles de groupe de sécurité réseau afin que les réplications fonctionnent pour une machine virtuelle donnée. Si vous utilisez des règles de groupe de sécurité réseau pour contrôler la connectivité sortante, utilisez des règles « Autoriser le trafic HTTPS sortant » pour toutes les plages d’adresses IP requises.

Dans cet exemple, l’emplacement de la machine virtuelle source est « Est des États-Unis ». L’emplacement de réplication cible est « Centre des États-Unis ».


### <a name="example"></a>Exemple

#### <a name="east-us"></a>Est des États-Unis

1. Créez des règles qui correspondent aux [plages d’adresses IP d’Est des États-Unis](https://www.microsoft.com/download/confirmation.aspx?id=41653). Ceci est nécessaire pour que les données puissent être écrites dans le compte de stockage de cache à partir de la machine virtuelle.
2. Créez des règles pour toutes les plages d’adresses IP qui correspondent aux [points de terminaison IP V4 d’authentification et d’identité](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity) Office 365.
3. Créez des règles qui correspondent à l’emplacement cible :

   **Emplacement** | **Adresses IP du service Site Recovery** |  **Adresse IP de surveillance Site Recovery**
    --- | --- | ---
   Centre des États-Unis | 40.69.144.231</br>40.69.167.116 | 52.165.34.144

#### <a name="central-us"></a>Centre des États-Unis

Ces règles sont nécessaires pour que la réplication puisse être activée de la région cible vers la région source après le basculement.

1. Créez des règles qui correspondent aux [plages d’adresses IP de Centre des États-Unis](https://www.microsoft.com/download/confirmation.aspx?id=41653).
2. Créez des règles pour toutes les plages d’adresses IP qui correspondent aux [points de terminaison IP V4 d’authentification et d’identité](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity) Office 365.
3. Créez des règles qui correspondent à l’emplacement source :

   **Emplacement** | **Adresses IP du service Site Recovery** |  **Adresse IP de surveillance Site Recovery**
    --- | --- | ---
   Est des États-Unis | 13.82.88.226</br>40.71.38.173 | 104.45.147.24


## <a name="existing-on-premises-connection"></a>Connexion locale existante

Si vous avez une connexion ExpressRoute ou VPN entre votre site local et l’emplacement source dans Azure, suivez les instructions suivantes :

**Configuration** | **Détails**
--- | ---
**Tunneling forcé** | En règle générale, un itinéraire par défaut (0.0.0.0/0) force le trafic Internet sortant à circuler via l’emplacement local. Nous ne recommandons pas cette approche. Le trafic de réplication et les communications de Site Recovery doivent rester dans Azure.<br/><br/> La solution consiste à ajouter des itinéraires définis par l’utilisateur pour [ces plages d’adresses IP](#outbound-connectivity-for-azure-site-recovery-ip-ranges) afin que le trafic n’accède pas à l’infrastructure locale.
**Connectivité** | Si les applications doivent se connecter à des machines locales ou si des clients locaux se connectent à l’application à partir de l’infrastructure locale par le biais d’un VPN/ExpressRoute, vérifiez que vous avez au moins une [connexion site à site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) entre la région Azure cible et le site local.<br/><br/> Si les volumes de trafic sont élevés entre la région Azure cible et le site local, créez une autre [connexion ExpressRoute](../expressroute/expressroute-introduction.md) entre la région cible et le site local.<br/><br/> Si vous souhaitez conserver les adresses IP des machines virtuelles après le basculement, laissez la connexion site à site/ExpressRoute de la région cible dans un état déconnecté. Ainsi, il n’y a aucun conflit de plage entre les plages d’adresses IP sources et cibles.
**ExpressRoute** | Créez un circuit ExpressRoute dans les régions source et cible.<br/><br/> Créez une connexion entre le réseau source et le circuit ExpressRoute, et entre le réseau cible et le circuit.<br/><br/> Nous vous recommandons d’utiliser des plages d’adresses IP différentes dans les régions source et cible. Le circuit ne pourra pas se connecter en même temps à plusieurs réseaux virtuels Azure ayant les mêmes plages d’adresses IP.<br/><br/> Vous pouvez créer des réseaux virtuels avec les mêmes plages d’adresses IP dans les deux régions, puis créer des circuits ExpressRoute dans les deux régions. En cas de basculement, déconnectez le circuit du réseau virtuel source et connectez le circuit sur le réseau virtuel cible.<br/><br/> Si la région principale est complètement en panne, l’opération de déconnexion peut échouer. Dans ce cas, le réseau virtuel cible ne bénéficiera pas de la connectivité ExpressRoute.
**ExpressRoute Premium** | Vous pouvez créer des circuits dans la même région géopolitique.<br/><br/> Pour créer des circuits dans différentes régions géopolitiques, vous avez besoin d’Azure ExpressRoute Premium.<br/><br/> Avec Premium, le coût est incrémentiel. Si vous l’utilisez déjà, cela ne représente aucun coût supplémentaire.<br/><br/> En savoir plus sur les [emplacements](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) et la [tarification](https://azure.microsoft.com/pricing/details/expressroute/).



## <a name="next-steps"></a>Étapes suivantes

Aller à l’[Étape 4 : créer un coffre](azure-to-azure-walkthrough-vault.md)


