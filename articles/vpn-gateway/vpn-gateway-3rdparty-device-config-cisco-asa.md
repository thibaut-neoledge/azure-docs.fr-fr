---
title: "Exemple de configuration - Appareil Cisco ASA se connectant à des passerelles VPN Azure | Microsoft Docs"
description: "Cet article fournit un exemple de configuration d’appareil Cisco ASA se connectant à des passerelles VPN Azure."
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: 
ms.assetid: a8bfc955-de49-4172-95ac-5257e262d7ea
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: yushwang
ms.translationtype: HT
ms.sourcegitcommit: d941879aee6042b38b7f5569cd4e31cb78b4ad33
ms.openlocfilehash: 10466b8928e2cd687f7961a2956b6d60823b82be
ms.contentlocale: fr-fr
ms.lasthandoff: 07/10/2017


---
# <a name="sample-configuration-cisco-asa-device-ikev2no-bgp"></a>Exemple de configuration : appareil Cisco ASA (IKEv2/sans BGP)
Cet article fournit des exemples de configuration d’appareil Cisco ASA se connectant à des passerelles VPN Azure.

## <a name="device-at-a-glance"></a>Aperçu de l’appareil

|                        |                                   |
| ---                    | ---                               |
| Fournisseur de l’appareil          | Cisco                             |
| Modèle de l'appareil           | ASA (Adaptive Security Appliance) |
| Version cible         | 8.4+                              |
| Modèle testé           | ASA 5505                          |
| Version testée         | 9.2                               |
| Version IKE            | **IKEv2**                         |
| BGP                    | **Non**                            |
| Type de passerelle VPN Azure | Passerelle VPN **basée sur le routage**       |
|                        |                                   |

> [!NOTE]
> 1. La configuration ci-dessous connecte un appareil Cisco ASA à une passerelle VPN Azure **basée sur le routage** à l’aide d’une stratégie IPsec/IKE personnalisée avec l’option « UserPolicyBasedTrafficSelectors », comme décrit dans [cet article](vpn-gateway-connect-multiple-policybased-rm-ps.md).
> 2. Les appareils ASA doivent utiliser la version **IKEv2** avec les configurations basées sur des listes d’accès, pas celles basées sur une interface virtuelle de tunnel (VTI).
> 3. Veuillez consulter les spécifications de votre fournisseur de périphérique VPN pour vous assurer que la stratégie est prise en charge sur vos périphériques VPN locaux.

## <a name="vpn-device-requirements"></a>Configuration requise du périphérique VPN
Les passerelles VPN Azure utilisent des suites de protocoles IPsec/IKE standard pour établir des tunnels VPN site à site (S2S). Reportez-vous à la page [À propos des périphériques VPN](vpn-gateway-about-vpn-devices.md) pour connaître les paramètres détaillés du protocole IPsec/IKE et les algorithmes de chiffrement par défaut des passerelles VPN Azure. Vous pouvez éventuellement spécifier la combinaison exacte d’algorithmes de chiffrement et les forces de clé souhaitée d’une connexion spécifique, comme décrit dans la rubrique [À propos des exigences de chiffrement](vpn-gateway-about-compliance-crypto.md). Si vous sélectionnez une combinaison spécifique d’algorithmes de chiffrement et de forces de clé, assurez-vous que vous utilisez les spécifications correspondantes sur vos périphériques VPN.

## <a name="single-vpn-tunnel"></a>Tunnel VPN unique
Cette topologie présente un tunnel VPN S2S unique entre une passerelle VPN Azure et votre périphérique VPN local. Vous pouvez éventuellement configurer le protocole BGP sur le tunnel VPN.

![tunnel unique](./media/vpn-gateway-3rdparty-device-config-cisco-asa/singletunnel.png)

Reportez-vous à la section [Tunnel VPN unique](vpn-gateway-3rdparty-device-config-overview.md#singletunnel) afin de connaître les instructions étape par étape pour créer les configurations Azure.

### <a name="network-and-vpn-gateway-information"></a>Informations sur le réseau et la passerelle VPN
Cette section répertorie les paramètres de cet exemple.

| **Paramètre**                | **Valeur**                    |
| ---                          | ---                          |
| Préfixes d’adresse du réseau virtuel        | 10.11.0.0/16<br>10.12.0.0/16 |
| IP de la passerelle VPN Azure         | Azure_Gateway_Public_IP      |
| Préfixes d’adresse locale | 10.51.0.0/16<br>10.52.0.0/16 |
| IP du périphérique VPN local    | OnPrem_Device_Public_IP     |
| * ASN BGP du réseau virtuel                | 65010                        |
| * IP d’homologue BGP Azure           | 10.12.255.30                 |
| * ASN BGP local         | 65050                        |
| * IP d’homologue BGP local     | 10.52.255.254                |
|                              |                              |

* (*) Paramètres facultatifs pour BGP uniquement.

### <a name="ipsecike-policy--parameters"></a>Stratégie et paramètres IPsec/IKE

Le tableau ci-dessous répertorie les algorithmes et paramètres IPsec/IKE utilisés dans l’exemple. Consultez les spécifications de votre périphérique VPN pour vous assurer que tous les algorithmes répertoriés ci-dessus sont pris en charge par vos modèles de périphérique VPN et les versions du microprogramme.

| **IPsec/IKEv2**  | **Valeur**                            |
| ---              | ---                                  |
| Chiffrement IKEv2 | AES256                               |
| Intégrité IKEv2  | SHA384                               |
| Groupe DH         | DHGroup24                            |
| Chiffrement IPsec | AES256                               |
| Intégrité IPsec  | SHA1                                 |
| Groupe PFS        | PFS24                                |
| Durée de vie de l’AS en mode rapide   | 7200 seconds                         |
| Sélecteur de trafic | UsePolicyBasedTrafficSelectors $True |
| Clé prépartagée   | PreSharedKey                         |
|                  |                                      |

- (*) Sur certains appareils, l’intégrité IPsec doit être « null » si GCM-AES est utilisé comme algorithme de chiffrement IPsec.

### <a name="device-notes"></a>Remarques sur l’appareil

>[!NOTE]
>
> 1. La prise en charge du protocole IKEv2 fonctionne avec les versions 8.4 et ultérieures d’ASA.
> 2. La prise en charge des groupes Diffie-Hellman et PFS supérieurs (au-delà du groupe 5) nécessitent la version 9.x d’ASA.
> 3. La prise en charge du chiffrement IPsec avec AES-GCM et de l’intégrité IPsec avec SHA-256, SHA-384, SHA-512 nécessite ASA 9.x sur le matériel ASA récent ; les modèles ASA 5505, 5510, 5520, 5540, 5550, 5580 ne sont **pas** pris en charge. (Consultez les spécifications du fournisseur pour confirmer ce point.)
>


### <a name="sample-device-configurations"></a>Exemples de configurations de périphérique
Le script ci-dessous fournit un exemple de configuration basé sur la topologie et les paramètres répertoriés ci-dessus. La configuration du tunnel VPN S2S comprend les éléments suivants :

1. Interfaces et itinéraires
2. Listes d’accès
3. Stratégie et paramètres IKE (Phase 1 ou Mode principal)
4. Stratégie et paramètres IPsec (Phase 2 ou Mode rapide)
5. Autres paramètres (clamping du MSS TCP, etc.)

>[!IMPORTANT] 
>Veillez à bien effectuer la configuration des éléments supplémentaires ci-dessous et à remplacer les espaces réservés par les valeurs réelles :
> 
> - Configuration d’interfaces internes et externes
> - Itinéraires pour vos réseaux internes/privés et externes/publics
> - Veiller à ce que tous les noms et les numéros de stratégie soient uniques sur l’appareil
> - Vérifier que les algorithmes de chiffrement sont pris en charge sur votre appareil
> - Remplacer les espaces réservés suivants avec les valeurs réelles
>   - Nom de l’interface externe : « outside »
>   - Azure_Gateway_Public_IP
>   - OnPrem_Device_Public_IP
>   - Pre_Shared_Key IKE
>   - Noms du réseau virtuel et de la passerelle de réseau local (VNetName, LNGName)
>   - Préfixes d’adresse du réseau virtuel et du réseau local
>   - Masques de réseau appropriés

#### <a name="sample-configuration"></a>Exemple de configuration

```
! Sample ASA configuration for connecting to Azure VPN gateway
!
! Tested hardware: ASA 5505
! Tested version:  ASA version 9.2(4)
!
! Replace the following place holders with your actual values:
!   - Interface names - default are "outside" and "inside"
!   - <Azure_Gateway_Public_IP>
!   - <OnPrem_Device_Public_IP>
!   - <Pre_Shared_Key>
!   - <VNetName>*
!   - <LNGName>* ==> LocalNetworkGateway - the Azure resource that represents the
!     on-premises network, specifies network prefixes, device public IP, BGP info, etc.
!   - <PrivateIPAddress> ==> Replace it with a private IP address if applicable
!   - <Netmask> ==> Replace it with appropriate netmasks
!   - <Nexthop> ==> Replace it with the actual nexthop IP address
!
! (*) Must be unique names in the device configuration
!
! ==> Interface & route configurations
!
!     > <OnPrem_Device_Public_IP> address on the outside interface or vlan
!     > <PrivateIPAddress> on the inside interface or vlan; e.g., 10.51.0.1/24
!     > Route to connect to <Azure_Gateway_Public_IP> address
!
!     > Example:
!
!       interface Ethernet0/0
!        switchport access vlan 2
!       exit
!
!       interface vlan 1
!        nameif inside
!        security-level 100
!        ip address <PrivateIPAddress> <Netmask>
!       exit
!
!       interface vlan 2
!        nameif outside
!        security-level 0
!        ip address <OnPrem_Device_Public_IP> <Netmask>
!       exit
!
!       route outside 0.0.0.0 0.0.0.0 <NextHop IP> 1
!
! ==> Access lists
!
!     > Most firewall devices deny all traffic by default. Create access lists to
!       (1) Allow S2S VPN tunnels between the ASA and the Azure gateway public IP address
!       (2) Construct traffic selectors as part of IPsec policy or proposal
!
access-list outside_access_in extended permit ip host <Azure_Gateway_Public_IP> host <OnPrem_Device_Public_IP>
!
!     > Object group that consists of all VNet prefixes (e.g., 10.11.0.0/16 &
!       10.12.0.0/16)
!
object-group network Azure-<VNetName>
 description Azure virtual network <VNetName> prefixes
 network-object 10.11.0.0 255.255.0.0
 network-object 10.12.0.0 255.255.0.0
exit
!
!     > Object group that corresponding to the <LNGName> prefixes.
!       E.g., 10.51.0.0/16 and 10.52.0.0/16. Note that LNG = "local network gateway".
!       In Azure network resource, a local network gateway defines the on-premises
!       network properties (address prefixes, VPN device IP, BGP ASN, etc.)
!
object-group network <LNGName>
 description On-Premises network <LNGName> prefixes
 network-object 10.51.0.0 255.255.0.0
 network-object 10.52.0.0 255.255.0.0
exit
!
!     > Specify the access-list between the Azure VNet and your on-premises network.
!       This access list defines the IPsec SA traffic selectors.
!
access-list Azure-<VNetName>-acl extended permit ip object-group <LNGName> object-group Azure-<VNetName>
!
!     > No NAT required between the on-premises network and Azure VNet
!
nat (inside,outside) source static <LNGName> <LNGName> destination static Azure-<VNetName> Azure-<VNetName>
!
! ==> IKEv2 configuration
!
!     > General IKEv2 configuration - enable IKEv2 for VPN
!
group-policy DfltGrpPolicy attributes
 vpn-tunnel-protocol ikev1 ikev2
exit
!
crypto isakmp identity address
crypto ikev2 enable outside
!
!     > Define IKEv2 Phase 1/Main Mode policy
!       - Make sure the policy number is not used
!       - integrity and prf must be the same
!       - DH group 14 and above require ASA version 9.x.
!
crypto ikev2 policy 1
 encryption       aes-256
 integrity        sha384
 prf              sha384
 group            24
 lifetime seconds 86400
exit
!
!     > Set connection type and pre-shared key
!
tunnel-group <Azure_Gateway_Public_IP> type ipsec-l2l
tunnel-group <Azure_Gateway_Public_IP> ipsec-attributes
 ikev2 remote-authentication pre-shared-key <Pre_Shared_Key> 
 ikev2 local-authentication  pre-shared-key <Pre_Shared_Key> 
exit
!
! ==> IPsec configuration
!
!     > IKEv2 Phase 2/Quick Mode proposal
!       - AES-GCM and SHA-2 requires ASA version 9.x on newer ASA models. ASA
!         5505, 5510, 5520, 5540, 5550, 5580 are not supported.
!       - ESP integrity must be null if AES-GCM is configured as ESP encryption
!
crypto ipsec ikev2 ipsec-proposal AES-256
 protocol esp encryption aes-256
 protocol esp integrity  sha-1
exit
!
!     > Set access list & traffic selectors, PFS, IPsec protposal, SA lifetime
!       - This sample uses "Azure-<VNetName>-map" as the crypto map name
!       - ASA supports only one crypto map per interface, if you already have
!         an existing crypto map assigned to your outside interface, you must use
!         the same crypto map name, but with a different sequence number for
!         this policy
!       - "match address" policy uses the access-list "Azure-<VNetName>-acl" defined 
!         previously
!       - "ipsec-proposal" uses the proposal "AES-256" defined previously 
!       - PFS groups 14 and beyond requires ASA version 9.x.
!
crypto map Azure-<VNetName>-map 1 match address Azure-<VNetName>-acl
crypto map Azure-<VNetName>-map 1 set pfs group24
crypto map Azure-<VNetName>-map 1 set peer <Azure_Gateway_Public_IP>
crypto map Azure-<VNetName>-map 1 set ikev2 ipsec-proposal AES-256
crypto map Azure-<VNetName>-map 1 set security-association lifetime seconds 7200
crypto map Azure-<VNetName>-map interface outside
!
! ==> Set TCP MSS to 1350
!
sysopt connection tcpmss 1350
!
```

## <a name="simple-debugging-commands"></a>Commandes de débogage simples

Voici quelques commandes ASA de débogage :

1. Afficher les associations de sécurité IPsec et IKE
    - « show crypto ipsec sa »
    - « show crypto ikev2 sa »
2. Passer en mode de débogage - ce qui peut provoquer beaucoup de bruit au niveau de la console
    - « debug crypto ikev2 platform <level> »
    - « debug crypto ikev2 protocol <level> »
3. Répertorier les configurations actuelles
    - « show run » - Affiche les configurations actuelles sur l’appareil ; vous pouvez utiliser les diverses sous-commandes pour répertorier des parties spécifiques de la configuration. Par exemple, « show run crypto », « show run access-list », « show run tunnel-group », etc.


## <a name="next-steps"></a>Étapes suivantes
Pour connaître les étapes de configuration des connexions en mode actif-actif entre des réseaux locaux ou entre deux réseaux virtuels, consultez la page [Configuring Active-Active VPN Gateways for Cross-Premises and VNet-to-VNet Connections](vpn-gateway-activeactive-rm-powershell.md) (Configuration des passerelles VPN actif-actif pour des connexions entre des réseaux locaux et entre deux réseaux virtuels).


