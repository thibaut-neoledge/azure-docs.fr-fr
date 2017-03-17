---
title: "Diagnostiquer la connectivité locale via une passerelle VPN avec Azure Network Watcher | Microsoft Docs"
description: "Cet article décrit comment diagnostiquer la connectivité locale via une passerelle VPN avec la résolution des problèmes des ressources d’Azure Network Watcher."
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: aeffbf3d-fd19-4d61-831d-a7114f7534f9
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 78f367de862e4fa9203cc794549abb935f117848
ms.openlocfilehash: f27a98894e3414479ce10adabbabe0f32a8cae54
ms.lasthandoff: 02/22/2017

---

# <a name="diagnose-on-premise-connectivity-via-vpn-gateways"></a>Diagnostiquer la connectivité locale via des passerelles VPN

La passerelle VPN Azure vous permet de créer des solutions hybrides qui répondent aux besoins d’une connexion sécurisée entre votre réseau local et votre réseau virtuel Azure. Vos besoins étant uniques, le choix du périphérique VPN local l’est également. Azure prend actuellement en charge [plusieurs périphériques VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md#a-namedevicetableavalidated-vpn-devices) qui sont validés en permanence en partenariat avec les fournisseurs de périphériques. Passez en revue les paramètres de configuration spécifiques au périphérique avant de configurer votre périphérique VPN local. De même, la passerelle VPN Azure est configurée avec un ensemble de [paramètres IPsec pris en charge](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec-parameters) qui sont utilisés pour établir des connexions. Actuellement, il n’existe aucun moyen de spécifier ou de sélectionner une combinaison spécifique de paramètres IPsec à partir de la passerelle VPN Azure. Pour établir une connexion correcte entre le site et Azure, les paramètres du périphérique VPN local doivent être conformes aux paramètres IPsec prescrits par la passerelle VPN Azure. Dans le cas contraire, vous perdez la connectivité et, jusqu’à maintenant, la résolution de ces problèmes n’est pas simple et plusieurs heures sont généralement nécessaires pour identifier et corriger le problème.

Avec la fonctionnalité de résolution des problèmes d’Azure Network Watcher, vous êtes en mesure de diagnostiquer les problèmes en lien avec vos connexions et votre passerelle. En quelques minutes, vous avez suffisamment d’informations pour prendre une décision éclairée et corriger le problème.

## <a name="scenario"></a>Scénario

Vous souhaitez configurer une connexion de site à site entre Azure et votre site à l’aide de Cisco ASA comme passerelle VPN locale. Pour réaliser ce scénario, la configuration suivante est requise :

1. Passerelle de réseau virtuel - La passerelle VPN sur Azure
1. Passerelle de réseau local - La représentation de la [passerelle VPN (CISCO ASA) locale](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway) dans le cloud Azure
1. Connexion de site à site (basée sur la stratégie) - [Connexion entre la passerelle VPN et CISCO ASA local](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#a-namecreateconnectiona8-create-a-site-to-site-vpn-connection)
1. [Configuration de CISCO ASA](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASA)

Vous trouverez des instructions étape par étape détaillées sur la configuration d’une connexion de site à site en consultant [Création d’un réseau virtuel avec une connexion de site à site à l’aide du portail Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). 

L’une des étapes critiques de configuration consiste à configurer les paramètres de communication IPsec. Une mauvaise configuration entraîne une perte de connectivité entre le réseau local et Azure. Les passerelles VPN Azure sont actuellement configurées pour prendre en charge les paramètres IPsec suivants pour la phase 1. Notez, comme mentionné précédemment, que ces paramètres ne peuvent pas être modifiés.  Comme vous pouvez le voir dans le tableau ci-dessous, les algorithmes de chiffrement pris en charge par la passerelle VPN Azure sont AES256 AES128 et 3DES.

### <a name="ike-phase-1-setup"></a>Configuration IKE phase 1

| **Propriété** | **PolicyBased** | **Basé sur un itinéraire et passerelle VPN standard ou hautes performances** |
| --- | --- | --- |
| Version IKE |IKEv1 |IKEv2 |
| Groupe Diffie-Hellman |Groupe 2 (1 024 bits) |Groupe 2 (1 024 bits) |
| Méthode d'authentification |Clé prépartagée |Clé prépartagée |
| Algorithmes de chiffrement |AES256 AES128 3DES |AES256 3DES |
| Algorithme de hachage |SHA1(SHA128) |SHA1(SHA128), SHA2(SHA256) |
| Durée de vie d’association de sécurité de phase 1 (temps) |28 800 secondes |10&800; secondes |
 
En tant qu’utilisateur, vous êtes obligé de configurer votre Cisco ASA. Vous trouverez un exemple de configuration sur [Github](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Cisco/Current/ASA/ASA_9.1_and_above_Show_running-config.txt). Entre autres configurations, vous devez également spécifier l’algorithme de hachage. Cisco ASA prend en charge plus [d’algorithmes de hachage et de chiffrement](http://www.cisco.com/c/en/us/about/security-center/next-generation-cryptography.html) que la passerelle VPN Azure. Sans le savoir, vous avez configuré votre Cisco ASA pour qu’il utilise SHA-512 comme algorithme de hachage. Comme cet algorithme n’est pas un algorithme pris en charge pour les connexions basées sur une stratégie, votre connexion VPN ne fonctionne pas.

Ces problèmes sont difficiles à résoudre et les causes premières sont souvent complexes à déterminer. Dans ce cas, vous pouvez ouvrir un ticket de support pour obtenir de l’aide sur la résolution du problème. Toutefois, l’API de résolution des problèmes d’Azure Network Watcher vous permet d’identifier ces problèmes vous-même. 

## <a name="troubleshooting-using-azure-network-watcher"></a>Résolution des problèmes à l’aide d’Azure Network Watcher

Pour diagnostiquer votre connexion, connectez-vous à Azure PowerShell et lancez l’applet de commande `Start-AzureRmNetworkWatcherResourceTroubleshooting`. Vous trouverez des détails sur l’utilisation de cette applet de commande à la page Troubleshoot Virtual Network Gateway and connections - PowerShell (Résoudre les problèmes de connexions et de passerelle de réseau virtuel - PowerShell). L’exécution de cette applet de commande peut prendre plusieurs minutes. 

Suite à son exécution, vous pouvez accéder à l’emplacement de stockage spécifié dans l’applet de commande pour obtenir des informations détaillées sur le problème et les journaux. Azure Network Watcher crée un dossier zip qui contient les fichiers journaux suivants :

![1][1]

Ouvrez le fichier appelé IKEErrors.txt. Celui-ci affiche l’erreur suivante, qui indique un problème de mauvaise configuration du paramètre IKE local. 

```
Error: On-premises device rejected Quick Mode settings. Check values. 
     based on log : Peer sent NO_PROPOSAL_CHOSEN notify
```

Vous pouvez obtenir des informations détaillées sur l’erreur à partir de Scrubbed-wfpdiag.txt. Ici, il est mentionné qu’une erreur `ERROR_IPSEC_IKE_POLICY_MATCH` s’est produite et a entraîné un dysfonctionnement de la connexion.

Un autre problème de configuration courant est la spécification de clés partagées incorrectes. Si dans l’exemple précédent, vous aviez spécifié différentes clés partagées, le fichier IKEErrors.txt affiche l’erreur suivante : `Error: Authentication failed. Check shared key`.

La fonctionnalité de résolution des problèmes d’Azure Network Watcher vous permet de diagnostiquer et de résoudre les problèmes liés à votre passerelle VPN et à votre connexion grâce à une simple applet de commande PowerShell. Actuellement, nous prenons en charge le diagnostic des états suivants et travaillons pour en prendre en charge davantage. 

### <a name="gateway"></a>Passerelle

| Type d’erreur | Motif | Journal|
|---|---|---|
| NoFault | Quand aucune erreur n’est détectée. |Oui|
| GatewayNotFound | Impossible de trouver la passerelle ou la passerelle n’est pas approvisionnée. |Non|
| PlannedMaintenance |  L’instance de passerelle est en maintenance.  |Non|
| UserDrivenUpdate | Quand une mise à jour utilisateur est en cours. Il peut s’agir d’une opération de redimensionnement. | Non |
| VipUnResponsive | Impossible d’atteindre l’instance principale de la passerelle. Cela se produit en cas d’échec de la sonde d’intégrité. | Non |
| PlatformInActive | Il existe un problème avec la plate-forme. | Non|
| ServiceNotRunning | Le service sous-jacent ne fonctionne pas. | Non|
| NoConnectionsFoundForGateway | Aucune connexion n’existe sur la passerelle. Il s’agit simplement d’un avertissement.| Non|
| ConnectionsNotConnected | Aucune des connexions n’est connectée. Il s’agit simplement d’un avertissement.| Oui|
| GatewayCPUUsageExceeded | L’utilisation du processeur par la passerelle est actuellement supérieure à 95 %. | Oui |

### <a name="connection"></a>Connexion

| Type d’erreur | Motif | Journal|
|---|---|---|
| NoFault | Quand aucune erreur n’est détectée. |Oui|
| GatewayNotFound | Impossible de trouver la passerelle ou la passerelle n’est pas approvisionnée. |Non|
| PlannedMaintenance | L’instance de passerelle est en maintenance.  |Non|
| UserDrivenUpdate | Quand une mise à jour utilisateur est en cours. Il peut s’agir d’une opération de redimensionnement.  | Non |
| VipUnResponsive | Impossible d’atteindre l’instance principale de la passerelle. Cela se produit en cas d’échec de la sonde d’intégrité. | Non |
| ConnectionEntityNotFound | La configuration de la connexion est manquante. | Non |
| ConnectionIsMarkedDisconnected | La connexion est identifiée comme étant « déconnectée ». |Non|
| ConnectionNotConfiguredOnGateway | Le service sous-jacent n’a pas la connexion configurée. | Oui |
| ConnectionMarkedStandy | Le service sous-jacent est identifié comme étant en veille.| Oui|
| Authentification | Incohérence des clés prépartagées. | Oui|
| PeerReachability | La passerelle homologue n’est pas accessible. | Oui|
| IkePolicyMismatch | La passerelle homologue a des stratégies IKE qui ne sont pas prises en charge par Azure. | Oui|
| WfpParse Error | Une erreur s’est produite lors de l’analyse du journal WFP. |Oui|

## <a name="next-steps"></a>Étapes suivantes

Apprenez à vérifier la connectivité de la passerelle VPN avec PowerShell et Azure Automation en consultant [Monitor VPN gateways with Network Watcher troubleshooting](network-watcher-monitor-with-azure-automation.md) (Surveiller les passerelles VPN avec la résolution des problèmes Network Watcher).

[1]: ./media/network-watcher-diagnose-on-premises-connectivity/figure1.png
