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
ms.date: 03/03/2017
ms.author: yushwang;cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: bea87fce9f1b1587af5a3e0d827a75e93d7bf534
ms.lasthandoff: 03/04/2017


---
# <a name="about-vpn-devices-for-site-to-site-vpn-gateway-connections"></a>À propos des périphériques VPN pour les connexions de la passerelle VPN de site à site
Un périphérique VPN est requis pour configurer une connexion VPN site à site (S2S) entre locaux à l’aide d’une passerelle VPN. Vous pouvez utiliser des connexions site à site pour créer une solution hybride, ou chaque fois que vous souhaitez disposer d'une connexion sécurisée entre votre réseau local et votre réseau virtuel. Cet article traite des périphériques VPN compatibles et des paramètres de configuration associés.


> [!IMPORTANT]
> Si vous rencontrez des problèmes de connectivité entre vos appareils VPN locaux et les passerelles VPN Azure, voir [Problèmes de compatibilité connus avec le matériel](#known).
> 
> 


###<a name="items-to-note-when-viewing-the-tables"></a>Éléments à noter lorsque vous affichez les tables :

* Une modification de la terminologie a eu lieu pour le routage statique et dynamique. Vous rencontrerez probablement les deux termes. Seuls les noms ont été modifiés, pas la fonctionnalité.
  * Routage statique = basé sur des stratégies
  * Routage dynamique = basé sur un itinéraire
* Sauf indication contraire, les spécifications des passerelles VPN hautes performances sont identiques à celles des passerelles VPN basées sur itinéraire. Par exemple, les périphériques VPN validés qui sont compatibles avec les passerelles VPN basées sur itinéraire sont également compatibles avec la passerelle VPN hautes performances Azure.

> [!NOTE]
> Lorsque vous configurez une connexion site à site, une adresse IPv4 publique est requise pour votre périphérique VPN.                                                                                                                                                                               
>
>


## <a name="devicetable"></a>Périphériques VPN validés
Nous avons validé un ensemble de périphériques VPN standard en partenariat avec des fournisseurs de périphériques. Tous les périphériques des familles de périphériques figurant dans la liste suivante doivent fonctionner avec les passerelles VPN Azure. Consultez [À propos de la passerelle VPN](vpn-gateway-about-vpngateways.md) pour vérifier le type de passerelle dont vous avez besoin pour créer la solution que vous souhaitez configurer.

Pour configurer plus facilement votre périphérique VPN, reportez-vous aux liens qui correspondent à la famille de périphériques appropriée. Pour une prise en charge des appareils VPN, contactez le fabricant de votre appareil.

| **Fournisseur** | **Famille de périphériques** | **Version de système d’exploitation minimale** | **PolicyBased** | **RouteBased** |
| --- | --- | --- | --- | --- |
| Allied Telesis |Routeurs VPN série AR |2.9.2 |Bientôt disponible |Non compatible |
| Barracuda Networks, Inc. |Barracuda NextGen Firewall série F |PolicyBased: 5.4.3<br>RouteBased: 6.2.0 |[Instructions de configuration](https://techlib.barracuda.com/NGF/AzurePolicyBasedVPNGW) |[Instructions de configuration](https://techlib.barracuda.com/NGF/AzureRouteBasedVPNGW) |
| Barracuda Networks, Inc. |Barracuda NextGen Firewall série X |Pare-feu Barracuda 6.5 |[Pare-feu Barracuda](https://techlib.barracuda.com/BFW/ConfigAzureVPNGateway) |Non compatible |
| Brocade |Routeur virtuel Vyatta 5400 |Routeur virtuel 6.6R3 GA |[Instructions de configuration](http://www1.brocade.com/downloads/documents/html_product_manuals/vyatta/vyatta_5400_manual/wwhelp/wwhimpl/js/html/wwhelp.htm#href=VPN_Site-to-Site%20IPsec%20VPN/Preface.1.1.html) |Non compatible |
| Check Point |Passerelle de sécurité |R77.30 |[Instructions de configuration](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |[Instructions de configuration](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco |ASA |8.3 |[Exemples Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASA) |Non compatible |
| Cisco |ASR |PolicyBased: IOS 15.1<br>RouteBased: IOS 15.2 |[Exemples Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR) |[Exemples Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR) |
| Cisco |ISR |PolicyBased: IOS 15.0<br>RouteBased*: IOS 15.1 |[Exemples Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR) |[Exemples Cisco*](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR) |
| Citrix |NetScaler MPX, SDX, VPX |10.1 et versions ultérieures |[Instructions d’intégration](https://docs.citrix.com/en-us/netscaler/11-1/system/cloudbridge-connector-introduction/cloudbridge-connector-azure.html) |Non compatible |
| Dell SonicWALL |Série TZ, série NSA<br>Série SuperMassive<br>Série NSA classe E |SonicOS 5.8.x<br>[SonicOS 5.9.x](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=850)<br>[SonicOS 6.x](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=646) |[Guide de configuration pour SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646)<br>[Guide de configuration pour SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850) |[Guide de configuration pour SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646)<br>[Guide de configuration pour SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850) |
| F5 |Série BIG-IP |12.0 |[Instructions de configuration](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip) |[Instructions de configuration](https://devcentral.f5.com/articles/big-ip-to-azure-dynamic-ipsec-tunneling) |
| Fortinet |FortiGate |FortiOS 5.4.2 |[Instructions de configuration](http://cookbook.fortinet.com/ipsec-vpn-microsoft-azure-54) |[Instructions de configuration](http://cookbook.fortinet.com/ipsec-vpn-microsoft-azure-54) |
| Internet Initiative Japan (IIJ) |Série SEIL |SEIL/X 4.60<br>SEIL/B1 4.60<br>SEIL/x86 3.20 |[Instructions de configuration](http://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf) |Non compatible |
| Juniper |SRX |PolicyBased: JunOS 10.2<br>Routebased: JunOS 11.4 |[Exemples Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX) |[Exemples Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX) |
| Juniper |Série J |PolicyBased: JunOS 10.4r9<br>RouteBased: JunOS 11.4 |[Exemples Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries) |[Exemples Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries) |
| Juniper |ISG |ScreenOS 6.3 |[Exemples Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG) |[Exemples Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG) |
| Juniper |SSG |ScreenOS 6.2 |[Exemples Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG) |[Exemples Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG) |
| Microsoft |Service de routage et d’accès à distance |Windows Server 2012 |Non compatible |[Exemples Microsoft](http://go.microsoft.com/fwlink/p/?LinkId=717761) |
| Open Systems AG |Passerelle Mission Control Security |N/A |[Guide d’installation](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) |[Guide d’installation](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) |
| Openswan |Openswan |2.6.32 |(Bientôt disponible) |Non compatible |
| Palo Alto Networks |Tous les périphériques exécutant PAN-OS |PAN-OS<br>PolicyBased : 6.1.5 ou version ultérieure<br>RouteBased : 7.1.4 |[Instructions de configuration](https://live.paloaltonetworks.com/t5/Configuration-Articles/How-to-Configure-VPN-Tunnel-Between-a-Palo-Alto-Networks/ta-p/59065) |[Instructions de configuration](https://live.paloaltonetworks.com/t5/Integration-Articles/Configuring-IKEv2-VPN-for-Microsoft-Azure-Environment/ta-p/60340) |
| WatchGuard |Tout |Fireware XTM<br> PolicyBased: v11.11.x<br>RouteBased: v11.12.x |[Instructions de configuration](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA2F00000000LI7KAM&lang=en_US) |[Instructions de configuration](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA22A000000XZogSAG&lang=en_US)|

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

## <a name="IPSec"></a>Paramètres IPsec
> [!NOTE]
> Même si les valeurs répertoriées dans le tableau ci-dessous sont prises en charge par la passerelle VPN Azure, il n’existe pour le moment aucun moyen de spécifier ou de sélectionner une combinaison spécifique de la passerelle VPN Azure. Vous devez spécifier des contraintes à partir du périphérique VPN local. En outre, vous devez définir MSS sur 1350.
>
>

### <a name="ike-phase-1-setup"></a>Configuration IKE Phase 1
| **Propriété** | **PolicyBased** | **Basé sur un itinéraire et passerelle VPN standard ou hautes performances** |
| --- | --- | --- |
| Version IKE |IKEv1 |IKEv2 |
| Groupe Diffie-Hellman |Groupe 2 (1 024 bits) |Groupe 2 (1 024 bits) |
| Méthode d'authentification |Clé prépartagée |Clé prépartagée |
| Algorithmes de chiffrement |AES256 AES128 3DES |AES256 3DES |
| Algorithme de hachage |SHA1(SHA128) |SHA1(SHA128), SHA2(SHA256) |
| Durée de vie d’association de sécurité de phase 1 (temps) |28 800 secondes |10&800; secondes |

### <a name="ike-phase-2-setup"></a>Configuration IKE Phase 2
| **Propriété** | **PolicyBased** | **Basé sur un itinéraire et passerelle VPN standard ou hautes performances** |
| --- | --- | --- |
| Version IKE |IKEv1 |IKEv2 |
| Algorithme de hachage |SHA1(SHA128), SHA2(SHA256) |SHA1(SHA128), SHA2(SHA256) |
| Durée de vie d’association de sécurité de phase 2 (temps) |3&600; secondes |3&600; secondes |
| Durée de vie d’association de sécurité de phase 2 (débit) |102 400 000 Ko |- |
| Offres d’authentification et de chiffrement d’association de sécurité IPsec (par ordre de préférence) |1. ESP-AES256 2. ESP-AES128 3. ESP-3DES 4. N/A |Voir « Offres d’association de sécurité IPsec pour passerelle basée sur un itinéraire » (ci-dessous) |
| PFS (Perfect Forward Secrecy) |Non |Non (*) |
| Détection d’homologue mort |Non pris en charge |Pris en charge |

(*) La passerelle Azure en tant que répondeur IKE peut accepter le groupe DH PFS 1, 2, 5, 14, 24.

### <a name="routebased-gateway-ipsec-security-association-sa-offers"></a>Offres d’association de sécurité IPsec pour passerelle basée sur un itinéraire
Le tableau suivant répertorie les offres d’authentification et de chiffrement d’association de sécurité IPsec. Les offres sont énumérées dans l’ordre de préférence dans lequel elles sont présentées ou acceptées.

| **Offres d’authentification et de chiffrement d’association de sécurité IPsec** | **Passerelle Azure en tant qu’initiateur** | **Passerelle Azure en tant que répondeur** |
| --- | --- | --- |
| 1 |ESP AES_256 SHA |ESP AES_128 SHA |
| 2 |ESP AES_128 SHA |ESP 3_DES MD5 |
| 3 |ESP 3_DES MD5 |ESP 3_DES SHA |
| 4 |ESP 3_DES SHA |AH SHA1 avec ESP AES_128 et HMAC Null |
| 5 |AH SHA1 avec ESP AES_256 et HMAC Null |AH SHA1 avec ESP 3_DES et HMAC Null |
| 6 |AH SHA1 avec ESP AES_128 et HMAC Null |AH MD5 avec ESP 3_DES et HMAC Null, aucune durée de vie proposée |
| 7 |AH SHA1 avec ESP 3_DES et HMAC Null |AH SHA1 avec ESP 3_DES SHA1, aucune durée de vie |
| 8 |AH MD5 avec ESP 3_DES et HMAC Null, aucune durée de vie proposée |AH MD5 avec ESP 3_DES MD5, aucune durée de vie |
| 9 |AH SHA1 avec ESP 3_DES SHA1, aucune durée de vie |ESP DES MD5 |
| 10 |AH MD5 avec ESP 3_DES MD5, aucune durée de vie |ESP DES SHA1, aucune durée de vie |
| 11 |ESP DES MD5 |AH SHA1 avec ESP DES et HMAC Null, aucune durée de vie proposée |
| 12 |ESP DES SHA1, aucune durée de vie |AH MD5 avec ESP DES et HMAC Null, aucune durée de vie proposée |
| 13. |AH SHA1 avec ESP DES et HMAC Null, aucune durée de vie proposée |AH SHA1 avec ESP DES SHA1, aucune durée de vie |
| 14 |AH MD5 avec ESP DES et HMAC Null, aucune durée de vie proposée |AH MD5 avec ESP DES MD5, aucune durée de vie |
| 15 |AH SHA1 avec ESP DES SHA1, aucune durée de vie |ESP SHA, aucune durée de vie |
| 16 |AH MD5 avec ESP DES MD5, aucune durée de vie |ESP MD5, aucune durée de vie |
| 17 |- |AH SHA, aucune durée de vie |
| 18 |- |AH MD5, aucune durée de vie |

* Vous pouvez spécifier le chiffrement IPsec ESP NULL avec les passerelles VPN basées sur un itinéraire et hautes performances. Le chiffrement Null ne fournit pas de protection des données en transit. Il doit être utilisé uniquement lorsqu’un débit maximal et une latence minimale sont requis.  Les clients peuvent choisir de l’utiliser dans les scénarios de communication entre les réseaux virtuels ou lorsque le chiffrement est appliqué ailleurs dans la solution.
* Pour les connexions entre locaux par le biais d’Internet, utilisez les paramètres de passerelle VPN Azure par défaut avec les algorithmes de chiffrement et de hachage répertoriés dans les tableaux ci-dessus pour garantir la sécurité de vos communications cruciales.

## <a name="known"></a>Problèmes de compatibilité connus avec le matériel

> [!IMPORTANT]
> Il s’agit de problèmes de compatibilité connus entre les appareils VPN tiers et les passerelles VPN Azure. L’équipe Azure travaille en étroite collaboration avec les fournisseurs pour résoudre les problèmes répertoriés ici. Une fois les problèmes résolus, cette page sera mise à jour avec les informations les plus récentes. Revenez la consulter régulièrement.

###<a name="feb-16-2017"></a>16 février 2017

**Appareils Palo Alto Networks dont la version est antérieure à la version 7.1.4** pour les VPN Azure basés sur les itinéraires : si vous utilisez des appareils VPN de Palo Alto Networks avec une version de PAN-OS antérieure à la version 7.1.4 et que vous rencontrez des problèmes de connectivité pour les passerelles VPN Azure basées sur les itinéraires, procédez comme suit :

1. Vérifiez la version du microprogramme de votre appareil Palo Alto Networks. Si votre version de PAN-OS est antérieure à la version 7.1.4, mettez à niveau vers la version 7.1.4.
2. Sur l’appareil Palo Alto Networks, modifiez la durée de SA de phase 2 (ou SA mode rapide) sur 28 800 secondes (8 heures) au moment de vous connecter à la passerelle VPN Azure.
3. Si vous continuez à rencontrer des problèmes de connectivité, ouvrez une demande de support à partir du portail Azure.

