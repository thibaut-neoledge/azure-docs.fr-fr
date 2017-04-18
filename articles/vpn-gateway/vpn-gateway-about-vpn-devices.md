---
title: "À propos des périphériques VPN pour les connexions Azure entre locaux | Microsoft Docs"
description: "Cet article traite des périphériques VPN et des paramètres IPsec pour les connexions entre locaux de passerelle VPN S2S. Des liens sont fournis vers des instructions et des exemples de configuration."
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: azure-resource-manager, azure-service-management
ms.assetid: ba449333-2716-4b7f-9889-ecc521e4d616
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: yushwang;cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 03ba7f0df69da073fd876fc4c06121952e594a02
ms.lasthandoff: 04/12/2017


---
# <a name="about-vpn-devices-and-ipsecike-parameters-for-site-to-site-vpn-gateway-connections"></a>À propos des périphériques VPN et des paramètres IPsec/IKE pour les connexions de passerelle VPN site à site

Un périphérique VPN est requis pour configurer une connexion VPN site à site (S2S) entre locaux à l’aide d’une passerelle VPN. Vous pouvez utiliser des connexions site à site pour créer une solution hybride, ou chaque fois que vous souhaitez disposer de connexions sécurisées entre vos réseaux locaux et vos réseaux virtuels. Cet article fournit la liste des paramètres IPsec/IKE pour les passerelles VPN Azure et une liste de périphériques VPN validés qui se connectent à des passerelles VPN Azure.


> [!IMPORTANT]
> Si vous rencontrez des problèmes de connectivité entre vos appareils VPN locaux et les passerelles VPN Azure, voir [Problèmes de compatibilité connus avec le matériel](#known). 


###<a name="items-to-note-when-viewing-the-tables"></a>Éléments à noter lorsque vous affichez les tables :

* Une modification de la terminologie a eu lieu pour les passerelles VPN Azure. Seuls les noms ont été modifiés, pas la fonctionnalité.
  * Routage statique = basé sur des stratégies
  * Routage dynamique = basé sur un itinéraire
* Sauf indication contraire, les spécifications des passerelles VPN hautes performances sont identiques à celles des passerelles VPN basées sur itinéraire. Par exemple, les périphériques VPN validés qui sont compatibles avec les passerelles VPN basées sur itinéraire sont également compatibles avec la passerelle VPN hautes performances Azure.

> [!NOTE]
> Lorsque vous configurez une connexion site à site, une adresse IPv4 publique est requise pour votre périphérique VPN.                                                                                                                                                                               


## <a name="devicetable"></a>Périphériques VPN validés
Nous avons validé un ensemble de périphériques VPN standard en partenariat avec des fournisseurs de périphériques. Tous les périphériques des familles de périphériques figurant dans la liste suivante doivent fonctionner avec les passerelles VPN Azure. Consultez [À propos de la passerelle VPN](vpn-gateway-about-vpngateways.md) pour vérifier le type de passerelle dont vous avez besoin pour créer la solution que vous souhaitez configurer.

Pour configurer plus facilement votre périphérique VPN, reportez-vous aux liens qui correspondent à la famille de périphériques appropriée.  Les liens vers les instructions de configuration sont fournis dans la mesure du possible. Pour une prise en charge des appareils VPN, contactez le fabricant de votre appareil.

|**Fournisseur**          |**Famille de périphériques**     |**Version de système d’exploitation minimale** |**PolicyBased** |**RouteBased** |
| ---                | ---                  | ---                   | ---            | ---           |
| A10 Networks, Inc. |Thunder CFW           |ACOS 4.1.1             |Non compatible  |[Guide de configuration](https://www.a10networks.com/resources/deployment-guides/a10-thunder-cfw-ipsec-vpn-interoperability-azure-vpn-gateways)|
| Allied Telesis     |Routeurs VPN série AR |2.9.2                  |Bientôt disponible     |Non compatible  |
| Barracuda Networks, Inc. |Barracuda NextGen Firewall série F |PolicyBased: 5.4.3<br>RouteBased: 6.2.0 |[Guide de configuration](https://techlib.barracuda.com/NGF/AzurePolicyBasedVPNGW) |[Guide de configuration](https://techlib.barracuda.com/NGF/AzureRouteBasedVPNGW) |
| Barracuda Networks, Inc. |Barracuda NextGen Firewall série X |Pare-feu Barracuda 6.5 |[Guide de configuration](https://techlib.barracuda.com/BFW/ConfigAzureVPNGateway) |Non compatible |
| Brocade            |Routeur virtuel Vyatta 5400   |Routeur virtuel 6.6R3 GA|[Guide de configuration](http://www1.brocade.com/downloads/documents/html_product_manuals/vyatta/vyatta_5400_manual/wwhelp/wwhimpl/js/html/wwhelp.htm#href=VPN_Site-to-Site%20IPsec%20VPN/Preface.1.1.html) |Non compatible |
| Check Point |Passerelle de sécurité |R77.30 |[Guide de configuration](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |[Guide de configuration](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco              |ASA       |8.3 |[Exemples de configuration](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASA) |Non compatible |
| Cisco |ASR |PolicyBased: IOS 15.1<br>RouteBased: IOS 15.2 |[Exemples de configuration](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR) |[Exemples de configuration](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR) |
| Cisco |ISR |PolicyBased: IOS 15.0<br>RouteBased*: IOS 15.1 |[Exemples de configuration](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR) |[Exemples de configuration*](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR) |
| Citrix |NetScaler MPX, SDX, VPX |10.1 et versions ultérieures |[Guide de configuration](https://docs.citrix.com/en-us/netscaler/11-1/system/cloudbridge-connector-introduction/cloudbridge-connector-azure.html) |Non compatible |
| F5 |Série BIG-IP |12.0 |[Guide de configuration](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip) |[Guide de configuration](https://devcentral.f5.com/articles/big-ip-to-azure-dynamic-ipsec-tunneling) |
| Fortinet |FortiGate |FortiOS 5.4.2 |[Guide de configuration](http://cookbook.fortinet.com/ipsec-vpn-microsoft-azure-54) |[Guide de configuration](http://cookbook.fortinet.com/ipsec-vpn-microsoft-azure-54) |
| Internet Initiative Japan (IIJ) |Série SEIL |SEIL/X 4.60<br>SEIL/B1 4.60<br>SEIL/x86 3.20 |[Guide de configuration](http://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf) |Non compatible |
| Juniper |SRX |PolicyBased: JunOS 10.2<br>Routebased: JunOS 11.4 |[Exemples de configuration](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX) |[Exemples de configuration](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX) |
| Juniper |Série J |PolicyBased: JunOS 10.4r9<br>RouteBased: JunOS 11.4 |[Exemples de configuration](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries) |[Exemples de configuration](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries) |
| Juniper |ISG |ScreenOS 6.3 |[Exemples de configuration](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG) |[Exemples de configuration](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG) |
| Juniper |SSG |ScreenOS 6.2 |[Exemples de configuration](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG) |[Exemples de configuration](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG) |
| Microsoft |Service de routage et d’accès à distance |Windows Server 2012 |Non compatible |[Exemples de configuration](http://go.microsoft.com/fwlink/p/?LinkId=717761) |
| Open Systems AG |Passerelle Mission Control Security |N/A |[Guide de configuration](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) |[Guide de configuration](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) |
| Openswan |Openswan |2.6.32 |(Bientôt disponible) |Non compatible |
| Palo Alto Networks |Tous les périphériques exécutant PAN-OS |PAN-OS<br>PolicyBased : 6.1.5 ou version ultérieure<br>RouteBased : 7.1.4 |[Guide de configuration](https://live.paloaltonetworks.com/t5/Configuration-Articles/How-to-Configure-VPN-Tunnel-Between-a-Palo-Alto-Networks/ta-p/59065) |[Guide de configuration](https://live.paloaltonetworks.com/t5/Integration-Articles/Configuring-IKEv2-VPN-for-Microsoft-Azure-Environment/ta-p/60340) |
| SonicWall |Série TZ, série NSA<br>Série SuperMassive<br>Série NSA classe E |SonicOS 5.8.x<br>SonicOS 5.9.x<br>SonicOS 6.x |[Guide de configuration pour SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646)<br>[Guide de configuration pour SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850) |[Guide de configuration pour SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646)<br>[Guide de configuration pour SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850) |
| WatchGuard |Tout |Fireware XTM<br> PolicyBased: v11.11.x<br>RouteBased: v11.12.x |[Guide de configuration](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA2F00000000LI7KAM&lang=en_US) |[Guide de configuration](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA22A000000XZogSAG&lang=en_US)|

(*) Les routeurs de la série ISR 7200 prennent uniquement en charge les VPN basés sur des stratégies.

## <a name="additionaldevices"></a>Périphériques VPN non validés
Si votre appareil n’est pas répertorié dans le tableau des périphériques VPN validés, il peut tout de même fonctionner avec une connexion site à site. Contactez le fabricant de votre périphérique pour obtenir une prise en charge et des instructions de configuration supplémentaires.

## <a name="editing"></a>Modification des exemples de configuration de périphérique
Après avoir téléchargé l’exemple de configuration de périphérique VPN fourni, vous devrez remplacer certaines des valeurs spécifiées pour qu’elles reflètent les paramètres de votre environnement.

###<a name="to-edit-a-sample"></a>Pour modifier un exemple :

1. Ouvrez l’exemple à l’aide du Bloc-notes.
2. Recherchez et remplacez toutes les chaînes au format <*texte*> par les valeurs qui correspondent à votre environnement. Prenez soin d’inclure < et >. Lorsque vous sélectionnez un nom, assurez-vous qu’il est unique. Si une commande ne fonctionne pas, consultez la documentation du fabricant du périphérique.

| **Texte de l’exemple** | **Valeur de substitution** |
| --- | --- |
| &lt;RP_OnPremisesNetwork&gt; |Nom que vous choisissez pour cet objet. Exemple : MonRéseauLocal |
| &lt;RP_AzureNetwork&gt; |Nom que vous choisissez pour cet objet. Exemple : MonRéseauAzure |
| &lt;RP_AccessList&gt; |Nom que vous choisissez pour cet objet. Exemple : MaListeAccèsAzure |
| &lt;RP_IPSecTransformSet&gt; |Nom que vous choisissez pour cet objet. Exemple : MonJeuTransformationsIPSec |
| &lt;RP_IPSecCryptoMap&gt; |Nom que vous choisissez pour cet objet. Exemple : MaCarteChiffrementIPSec |
| &lt;SP_AzureNetworkIpRange&gt; |Spécifiez une plage. Exemple : 192.168.0.0 |
| &lt;SP_AzureNetworkSubnetMask&gt; |Spécifiez un masque de sous-réseau. Exemple : 255.255.0.0 |
| &lt;SP_OnPremisesNetworkIpRange&gt; |Spécifiez une plage locale. Exemple : 10.2.1.0 |
| &lt;SP_OnPremisesNetworkSubnetMask&gt; |Spécifiez un masque de sous-réseau local. Exemple : 255.255.255.0 |
| &lt;SP_AzureGatewayIpAddress&gt; |Ces informations sont propres à votre réseau virtuel et figurent dans le portail de gestion sous l’intitulé **Adresse IP de la passerelle**. |
| &lt;SP_PresharedKey&gt; |Ces informations sont propres à votre réseau virtuel et figurent dans le Portail de gestion sous l’intitulé Gérer la clé. |

## <a name="ipsec"></a>Paramètres IPsec/IKE
> [!NOTE]
> Même si les valeurs répertoriées dans le tableau ci-dessous sont prises en charge par la passerelle VPN Azure, il n’existe pour le moment aucun moyen de spécifier ou de sélectionner une combinaison d’algorithmes ou de paramètres spécifique de la passerelle VPN Azure. Vous devez spécifier des contraintes à partir du périphérique VPN local. En outre, vous devez définir **MSS** sur **1350**.
> 
>

Dans les tableaux ci-dessous :

* AS = association de sécurité
* IKE Phase 1 est également appelé « Mode principal »
* IKE Phase 2 est également appelé « Mode rapide »

### <a name="ike-phase-1-main-mode-parameters"></a>Paramètres IKE Phase 1 (Mode principal)
| **Propriété**          |**PolicyBased**    | **RouteBased**    |
| ---                   | ---               | ---               |
| Version IKE           |IKEv1              |IKEv2              |
| Groupe Diffie-Hellman  |Groupe 2 (1 024 bits) |Groupe 2 (1 024 bits) |
| Méthode d'authentification |Clé prépartagée     |Clé prépartagée     |
| Chiffrement et algorithmes de hachage |1. AES256, SHA256<br>2. AES256, SHA1<br>3. AES128, SHA1<br>4. 3DES, SHA1 |1. AES256, SHA1<br>2. AES256, SHA256<br>3. AES128, SHA1<br>4. AES128, SHA256<br>5. 3DES, SHA1<br>6. 3DES, SHA256 |
| Durée de vie de l’AS           |28 800 secondes     |28 800 secondes     |

### <a name="ike-phase-2-quick-mode-parameters"></a>Paramètres IKE Phase 2 (Mode rapide)
| **Propriété**                  |**PolicyBased**| **RouteBased**                              |
| ---                           | ---           | ---                                         |
| Version IKE                   |IKEv1          |IKEv2                                        |
| Chiffrement et algorithmes de hachage |1. AES256, SHA256<br>2. AES256, SHA1<br>3. AES128, SHA1<br>4. 3DES, SHA1 |[Offres d’AS RouteBased en mode rapide](#RouteBasedOffers) |
| Durée de vie de l’AS (durée)            |3 600 secondes  |27 000 secondes                                |
| Durée de vie de l’AS (octets)           |102 400 000 Ko | -                                           |
| PFS (Perfect Forward Secrecy) |Non             |[Offres d’AS RouteBased en mode rapide](#RouteBasedOffers) |
| Détection d’homologue mort     |Non pris en charge  |Pris en charge                                    |


### <a name ="RouteBasedOffers"></a>Offres d’association de sécurité VPN IPsec RouteBased (AS IKE en mode rapide)
Le tableau suivant répertorie les offres d’association de sécurité IPsec (IKE en mode rapide). Les offres sont énumérées dans l’ordre de préférence dans lequel elles sont présentées ou acceptées.

#### <a name="azure-gateway-as-initiator"></a>Passerelle Azure en tant qu’initiateur
|-  |**Chiffrement**|**Authentification**|**Groupe PFS**|
|---| ---          |---               |---          |
| 1 |GCM AES256    |GCM (AES256)      |Aucun         |
| 2 |AES256        |SHA1              |Aucun         |
| 3 |3DES          |SHA1              |Aucun         |
| 4 |AES256        |SHA256            |Aucun         |
| 5 |AES128        |SHA1              |Aucun         |
| 6 |3DES          |SHA256            |Aucun         |

#### <a name="azure-gateway-as-responder"></a>Passerelle Azure en tant que répondeur
|-  |**Chiffrement**|**Authentification**|**Groupe PFS**|
|---| ---          | ---              |---          |
| 1 |GCM AES256    |GCM (AES256)      |Aucun         |
| 2 |AES256        |SHA1              |Aucun         |
| 3 |3DES          |SHA1              |Aucun         |
| 4 |AES256        |SHA256            |Aucun         |
| 5 |AES128        |SHA1              |Aucun         |
| 6 |3DES          |SHA256            |Aucun         |
| 7 |DES           |SHA1              |Aucun         |
| 8 |AES256        |SHA1              |1            |
| 9 |AES256        |SHA1              |2            |
| 10|AES256        |SHA1              |14           |
| 11|AES128        |SHA1              |1            |
| 12|AES128        |SHA1              |2            |
| 13.|AES128        |SHA1              |14           |
| 14|3DES          |SHA1              |1            |
| 15|3DES          |SHA1              |2            |
| 16|3DES          |SHA256            |2            |
| 17|AES256        |SHA256            |1            |
| 18|AES256        |SHA256            |2            |
| 19|AES256        |SHA256            |14           |
| 20|AES256        |SHA1              |24           |
| 21|AES256        |SHA256            |24           |
| 22|AES128        |SHA256            |Aucun         |
| 23|AES128        |SHA256            |1            |
| 24|AES128        |SHA256            |2            |
| 25|AES128        |SHA256            |14           |
| 26|3DES          |SHA1              |14           |

* Vous pouvez spécifier le chiffrement IPsec ESP NULL avec les passerelles VPN basées sur un itinéraire et hautes performances. Le chiffrement Null ne fournit pas de protection des données en transit. Il doit être utilisé uniquement lorsqu’un débit maximal et une latence minimale sont requis.  Les clients peuvent choisir de l’utiliser dans les scénarios de communication entre les réseaux virtuels ou lorsque le chiffrement est appliqué ailleurs dans la solution.
* Pour les connexions entre locaux par le biais d’Internet, utilisez les paramètres de passerelle VPN Azure par défaut avec les algorithmes de chiffrement et de hachage répertoriés dans les tableaux ci-dessus pour garantir la sécurité de vos communications cruciales.

## <a name="known"></a>Problèmes de compatibilité connus avec le matériel

> [!IMPORTANT]
> Il s’agit de problèmes de compatibilité connus entre les appareils VPN tiers et les passerelles VPN Azure. L’équipe Azure travaille en étroite collaboration avec les fournisseurs pour résoudre les problèmes répertoriés ici. Une fois les problèmes résolus, cette page sera mise à jour avec les informations les plus récentes. Revenez la consulter régulièrement.
>
>

###<a name="feb-16-2017"></a>16 février 2017

**Appareils Palo Alto Networks dont la version est antérieure à la version 7.1.4** pour les VPN Azure basés sur les itinéraires : si vous utilisez des appareils VPN de Palo Alto Networks avec une version de PAN-OS antérieure à la version 7.1.4 et que vous rencontrez des problèmes de connectivité pour les passerelles VPN Azure basées sur les itinéraires, procédez comme suit :

1. Vérifiez la version du microprogramme de votre appareil Palo Alto Networks. Si votre version de PAN-OS est antérieure à la version 7.1.4, mettez à niveau vers la version 7.1.4.
2. Sur l’appareil Palo Alto Networks, modifiez la durée de SA de phase 2 (ou SA mode rapide) sur 28 800 secondes (8 heures) au moment de vous connecter à la passerelle VPN Azure.
3. Si vous continuez à rencontrer des problèmes de connectivité, ouvrez une demande de support sur le Portail Azure.

