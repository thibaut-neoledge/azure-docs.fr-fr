---
title: "À propos des exigences de chiffrement et des passerelles VPN Azure | Microsoft Docs"
description: Cet article aborde les exigences de chiffrement et les passerelles VPN Azure
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: azure-resource-manager
ms.assetid: 238cd9b3-f1ce-4341-b18e-7390935604fa
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/22/2017
ms.author: yushwang
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: c789e6c278fc0c58c64f5d96e57f94aee5a6cefc
ms.contentlocale: fr-fr
ms.lasthandoff: 07/28/2017

---
# <a name="about-cryptographic-requirements-and-azure-vpn-gateways"></a>À propos des exigences de chiffrement et des passerelles VPN Azure

Cet article explique comment configurer des passerelles VPN Azure pour satisfaire vos exigences de chiffrement pour les tunnels VPN S2S entre différents locaux et les connexions de réseau virtuel à réseau virtuel dans Azure. 

## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a>À propos des paramètres de stratégie IPsec et IKE pour les passerelles VPN Azure
La norme de protocole IPsec et IKE standard prend en charge un vaste éventail d’algorithmes de chiffrement dans différentes combinaisons. Si les clients ne demandent pas une combinaison spécifique de paramètres et d’algorithmes de chiffrement, les passerelles VPN Azure utilisent un ensemble de propositions par défaut. Les jeux de stratégies par défaut ont été choisis pour optimiser l’interopérabilité avec un large éventail d’appareils VPN tiers dans des configurations par défaut. Par conséquent, les stratégies et le nombre de propositions ne peuvent pas couvrir toutes les combinaisons d’algorithmes de chiffrement disponibles et de forces de clé disponibles.

La stratégie par défaut définie pour la passerelle VPN Azure est répertoriée dans le document : [À propos des appareils VPN et des paramètres IPsec/IKE pour les connexions de passerelle VPN site à site](vpn-gateway-about-vpn-devices.md).

## <a name="cryptographic-requirements"></a>Exigences de chiffrement
Pour les communications qui exigent des paramètres ou des algorithmes de chiffrement spécifiques, généralement en raison des exigences de conformité ou de sécurité, les clients peuvent désormais configurer leurs passerelles VPN Azure pour utiliser une stratégie IPsec/IKE personnalisée avec des algorithmes de chiffrement et des forces de clé spécifiques, plutôt que les jeux de stratégies Azure par défaut.

Par exemple, les stratégies de mode principal IKEv2 pour les passerelles VPN Azure utilisent uniquement Diffie-Hellman groupe 2 (1 024 bits), tandis que les clients doivent peut-être spécifier des groupes plus puissants à utiliser dans IKE, tels que le groupe 14(2 048 bits), le groupe 24 (groupe MODP de 2 048 bits) ou des groupes ECP (groupes à courbe elliptique) 256 ou 384 bits (groupe 19 et groupe 20, respectivement). Des exigences similaires s’appliquent également aux stratégies de mode rapide IPsec.

## <a name="custom-ipsecike-policy-with-azure-vpn-gateways"></a>Stratégie IPsec/IKE personnalisée avec des passerelles VPN Azure
Les passerelles VPN Azure prennent désormais en charge la stratégie IPsec/IKE personnalisée par connexion. Vous pouvez choisir une combinaison spécifique d’algorithmes de chiffrement pour IPsec et IKE avec la force de clé souhaitée pour une connexion S2S ou de réseau virtuel à réseau virtuel, comme illustré dans l’exemple ci-dessous :

![stratégie IPSec/IKE](./media/vpn-gateway-about-compliance-crypto/ipsecikepolicy.png)

Vous pouvez créer une stratégie IPsec/IKE et l’appliquer à une connexion nouvelle ou existante. 

### <a name="workflow"></a>Workflow

1. Créer des réseaux virtuels, des passerelles VPN ou des passerelles de réseau local pour votre topologie de connectivité, comme décrit dans d’autres documents de guide pratique
2. Créer une stratégie IPsec/IKE
3. Vous pouvez appliquer la stratégie quand vous créez une connexion S2S ou de réseau virtuel à réseau virtuel
4. Si la connexion est déjà créée, vous pouvez appliquer ou mettre à jour la stratégie sur une connexion existante


## <a name="ipsecike-policy-faq"></a>Questions fréquentes (FAQ) relatives à la stratégie IPsec/IKE

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-ipsecikepolicy-faq-include.md)]


## <a name="next-steps"></a>Étapes suivantes
Consultez [Configurer la stratégie IPsec/IKE](vpn-gateway-ipsecikepolicy-rm-powershell.md) pour obtenir des instructions détaillées sur la configuration d’une stratégie IPsec/IKE personnalisée sur une connexion.

Consultez aussi [Connecter plusieurs appareils VPN en fonction de stratégies](vpn-gateway-connect-multiple-policybased-rm-ps.md) pour en savoir plus sur l’option UsePolicyBasedTrafficSelectors.
