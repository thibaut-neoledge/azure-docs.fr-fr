<properties 
   pageTitle="À propos des périphériques VPN pour les connexions de réseau virtuel site à site | Microsoft Azure"
   description="Découvrez les périphériques VPN et les paramètres IPsec pour les connexions de passerelle VPN site à site Azure Virtual Network."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/07/2015"
   ms.author="cherylmc" />

# À propos des périphériques VPN pour les connexions de réseau virtuel site à site

La configuration d’une connexion VPN site à site nécessite un périphérique VPN. Vous pouvez utiliser des connexions site à site pour créer une solution cloud hybride, ou chaque fois que vous souhaitez disposer d’une connexion sécurisée entre votre réseau local et votre réseau virtuel. Cet article traite des périphériques VPN compatibles et des paramètres de configuration associés.

Il est important de noter que les connexions site à site nécessitent non seulement un périphérique VPN compatible, mais également une adresse IP IPv4 publique. En outre, vous devrez sélectionner le type de passerelle qui prendra en charge votre solution. Certains périphériques VPN ne prennent pas en charge tous les types de passerelle. Pour vérifier le type de passerelle dont vous avez besoin pour créer votre solution, voir l’article [À propos des passerelles VPN](vpn-gateway-about-vpngateways.md).



## Périphériques VPN

Nous avons validé un ensemble de périphériques VPN site à site (S2S) standard en partenariat avec des fournisseurs de périphériques. Pour obtenir la liste des périphériques VPN compatibles avec le réseau virtuel, voir la section [Périphériques VPN compatibles](#compatible-vpn-devices) ci-dessous. Tous les périphériques des familles de périphériques figurant dans cette liste doivent fonctionner avec les passerelles VPN Azure. Pour configurer plus facilement votre périphérique VPN, reportez-vous à l’exemple de configuration de périphérique qui correspond à la famille de périphériques appropriée.

Sauf indication contraire, les spécifications des passerelles VPN hautes performances sont identiques à celles des passerelles VPN à routage dynamique. Par exemple, les périphériques VPN validés qui sont compatibles avec les passerelles VPN à routage dynamique Azure sont également compatibles avec la nouvelle passerelle VPN hautes performances Azure.


### Périphériques VPN compatibles

Nous avons collaboré avec des fournisseurs de périphériques VPN pour identifier comme éligibles des familles spécifiques de périphériques VPN. La section ci-dessous répertorie toutes les familles de périphériques fonctionnant avec notre passerelle VPN. Tous les périphériques appartenant aux familles répertoriées sont compatibles avec notre passerelle, sauf mention contraire. Si votre périphérique n’apparaît pas dans la liste, voir la section [Périphériques absents de la liste des périphériques compatibles](#devices-not-on-the-compatible-list).



Pour plus d’informations sur la prise en charge des périphériques VPN, contactez le fabricant de votre périphérique.



| **Fournisseur** | **Famille de périphériques** | **Version de système d’exploitation minimale** | **Routage statique** | **Routage dynamique** |
|---------------------------------|----------------------------------------------------------|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Allied Telesis | Routeurs VPN série AR | 2\.9.2 | Bientôt disponible | Non compatible |
| Barracuda Networks, Inc. | Pare-feu Barracuda NG | Pare-feu Barracuda NG 5.4.3 | [Pare-feu Barracuda NG](https://techlib.barracuda.com/display/BNGV54/How%20to%20Configure%20an%20IPsec%20Site-to-Site%20VPN%20to%20a%20Windows%20Azure%20VPN%20Gateway)| Non compatible |
| Barracuda Networks, Inc. | Pare-feu Barracuda | Pare-feu Barracuda 6.5 | [Pare-feu Barracuda](https://techlib.barracuda.com/BFW/ConfigAzureVPNGateway) | Non compatible |
| Brocade | Routeur virtuel Vyatta 5400 | Routeur virtuel 6.6R3 GA | [Instructions de configuration](http://www1.brocade.com/downloads/documents/html_product_manuals/vyatta/vyatta_5400_manual/wwhelp/wwhimpl/js/html/wwhelp.htm#href=VPN_Site-to-Site%20IPsec%20VPN/Preface.1.1.html) | Non compatible |
| Check Point | Passerelle de sécurité | R75.40, R75.40VS | [Instructions de configuration](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) | [Instructions de configuration](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco | ASA | 8\.3 | [Exemples Cisco ASA](https://msdn.microsoft.com/library/azure/dn133793.aspx) | Non compatible |
| Cisco | ASR | IOS 15.1 (statique), IOS 15.2 (dynamique) | [Exemples Cisco ASR](https://msdn.microsoft.com/library/azure/dn133802.aspx) | [Exemples Cisco ASR](https://msdn.microsoft.com/library/azure/dn133802.aspx) |
| Cisco | ISR | IOS 15.0 (statique), IOS 15.1 (dynamique) | [Exemples Cisco ISR](https://msdn.microsoft.com/library/azure/dn133800.aspx) | [Exemples Cisco ISR](https://msdn.microsoft.com/library/azure/dn133800.aspx) |
| Citrix | Appliance MPX CloudBridge ou appliance virtuelle VPX | N/A | [Instructions d’intégration](https://www.citrix.com/welcome.html?resource=%2Fdownloads%2Fcloudbridge%2Fbetas-and-tech-previews%2Fcloudbridge-azure-integration) | Non compatible |
| Dell SonicWALL | Série TZ, Série NSA, Série SuperMassive, Série NSA classe E | SonicOS 5.8.x, SonicOS 5.9.x, SonicOS 6.x | [Instructions de configuration](https://www.sonicwall.com/app/projects/file_downloader/document_lib.php?t=TN&id=348) | Non compatible |
| F5 | Série BIG-IP | N/A | [Instructions de configuration](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip) | Non compatible |
| Fortinet | FortiGate | FortiOS 5.0.7 | [Instructions de configuration](http://docs.fortinet.com/fortigate/admin-guides) | [Instructions de configuration](http://docs.fortinet.com/fortigate/admin-guides) |
| Internet Initiative Japan (IIJ) | Série SEIL | SEIL/X 4.60, SEIL/B1 4.60, SEIL/x86 3.20 | [Instructions de configuration](http://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf) | Non compatible |
| Juniper | SRX | JunOS 10.2 (statique), JunOS 11.4 (dynamique) | [Exemples Juniper SRX](https://msdn.microsoft.com/library/azure/dn133794.aspx) | [Exemples Juniper SRX](https://msdn.microsoft.com/library/azure/dn133794.aspx) |
| Juniper | Série J | JunOS 10.4r9 (statique), JunOS 11.4 (dynamique) | [Exemples Juniper série J](https://msdn.microsoft.com/library/azure/dn133799.aspx) | [Exemples Juniper série J](https://msdn.microsoft.com/library/azure/dn133799.aspx) |
| Juniper | ISG | ScreenOS 6.3 (statique et dynamique) | [Exemples Juniper ISG](https://msdn.microsoft.com/library/azure/dn133797.aspx) | [Exemples Juniper ISG](https://msdn.microsoft.com/library/azure/dn133797.aspx) |
| Juniper | SSG | ScreenOS 6.2 (statique et dynamique) | [Exemples Juniper SSG](https://msdn.microsoft.com/library/azure/dn133796.aspx) | [Exemples Juniper SSG](https://msdn.microsoft.com/library/azure/dn133796.aspx) |
| Microsoft | Service de routage et d’accès à distance | Windows Server 2012 | Non compatible | [Exemples de service de routage et d’accès à distance (RRAS)](https://msdn.microsoft.com/library/azure/dn133801.aspx) |
| Openswan | Openswan | 2\.6.32 | (Bientôt disponible) | Non compatible |
| Palo Alto Networks | Tous les périphériques exécutant PAN-OS 5.0 ou une version ultérieure | PAN-OS 5x ou version ultérieure | [Palo Alto Networks](https://support.paloaltonetworks.com/) | Non compatible |
| Watchguard | Tout | Fireware XTM v11.x | [Instructions de configuration](http://customers.watchguard.com/articles/Article/Configure-a-VPN-connection-to-a-Windows-Azure-virtual-network/) | Non compatible |


### Périphériques absents de la liste des périphériques compatibles


Si votre périphérique ne figure pas dans la liste des périphériques VPN compatibles connus et que vous souhaitez utiliser ce périphérique pour votre connexion VPN, vous devez vérifier qu’il présente la configuration minimale requise décrite dans le tableau [Conditions requises de la passerelle](vpn-gateway-about-vpngateways.md#gateway-requirements). Les périphériques offrant la configuration minimale requise sont également censés fonctionner correctement avec Virtual Network. Contactez le fabricant de votre périphérique pour obtenir une prise en charge et des instructions de configuration supplémentaires.


## Modification des exemples de configuration de périphérique

Après avoir téléchargé l’exemple de configuration de périphérique VPN fourni, vous devrez remplacer certaines des valeurs spécifiées pour qu’elles reflètent les paramètres de votre environnement.

**Pour modifier l’exemple :**

1. Ouvrez l’exemple à l’aide du Bloc-notes. 
1. Recherchez et remplacez toutes les chaînes au format <*texte*> par les valeurs qui correspondent à votre environnement. Prenez soin d’inclure les caractères < and >. Lorsque vous sélectionnez un nom, assurez-vous qu’il est unique. Si une commande ne fonctionne pas, consultez la documentation du fabricant du périphérique.

| **Texte de l’exemple** | **Valeur de substitution** |
|----------------------------------|----------------------------------------------------------------------------------------------------------------------|
| &lt;RP\_OnPremisesNetwork&gt; | Nom que vous choisissez pour cet objet. Exemple : MonRéseauLocal |
| &lt;RP\_AzureNetwork&gt; | Nom que vous choisissez pour cet objet. Exemple : MonRéseauAzure |
| &lt;RP\_AccessList&gt; | Nom que vous choisissez pour cet objet. Exemple : MaListeAccèsAzure |
| &lt;RP\_IPSecTransformSet&gt; | Nom que vous choisissez pour cet objet. Exemple : MonJeuTransformationsIPSec |
| &lt;RP\_IPSecCryptoMap&gt; | Nom que vous choisissez pour cet objet. Exemple : MaCarteChiffrementIPSec |
| &lt;SP\_AzureNetworkIpRange&gt; | Spécifiez une plage. Exemple : 192.168.0.0 |
| &lt;SP\_AzureNetworkSubnetMask&gt; | Spécifiez un masque de sous-réseau. Exemple : 255.255.0.0 |
| &lt;SP\_OnPremisesNetworkIpRange&gt; | Spécifiez une plage locale. Exemple : 10.2.1.0 |
| &lt;SP\_OnPremisesNetworkSubnetMask&gt; | Spécifiez un masque de sous-réseau local. Exemple : 255.255.255.0 |
| &lt;SP\_AzureGatewayIpAddress&gt; | Ces informations sont propres à votre réseau virtuel et figurent dans le Portail de gestion sous l’intitulé **Adresse IP de la passerelle**. |
| &lt;SP\_PresharedKey&gt; | Ces informations sont propres à votre réseau virtuel et figurent dans le Portail de gestion sous l’intitulé Gérer la clé. |



## Paramètres IPsec

### Configuration IKE Phase 1

| **Propriété** | **Passerelle VPN à routage statique** | **Passerelle VPN à routage dynamique et passerelle VPN standard ou hautes performances** |
|----------------------------------------------------|--------------------------------|------------------------------------------------------------------|
| Version IKE | IKEv1 | IKEv2 |
| Groupe Diffie-Hellman | Groupe 2 (1 024 bits) | Groupe 2 (1 024 bits) |
| Méthode d'authentification | Clé prépartagée | Clé prépartagée |
| Algorithmes de chiffrement | AES256 AES128 3DES | AES256 3DES |
| Algorithme de hachage | SHA1(SHA128) | SHA1(SHA128) |
| Durée de vie d’association de sécurité de phase 1 (temps) | 28 800 secondes | 28 800 secondes |


### Configuration IKE Phase 2

| **Propriété** | **Passerelle VPN à routage statique** | **Passerelle VPN à routage dynamique et passerelle VPN standard ou hautes performances** |
|--------------------------------------------------------------------------|------------------------------------------------|--------------------------------------------------------------------|
| Version IKE | IKEv1 | IKEv2 |
| Algorithme de hachage | SHA1(SHA128) | SHA1(SHA128) |
| Durée de vie d’association de sécurité de phase 2 (temps) | 3 600 secondes | - | | Durée de vie d’association de sécurité de phase 2 (débit) | 102 400 000 Ko | - | | Offres d’authentification et de chiffrement d’association de sécurité IPsec (par ordre de préférence) | 1. ESP-AES256 2. ESP-AES128 3. ESP-3DES 4. N/A | Voir la section « Offres d’association de sécurité IPsec pour passerelle à routage dynamique » | | PFS (Perfect Forward Secrecy) | Non | Oui (groupe 1 DH) | | Détection d’homologue mort | Non prise en charge | Prise en charge |

### Offres d’association de sécurité IPsec pour passerelle à routage dynamique

Le tableau ci-après répertorie les offres d’authentification et de chiffrement d’association de sécurité IPsec. Les offres sont énumérées dans l’ordre de préférence dans lequel elles sont présentées ou acceptées.

| **Offres d’authentification et de chiffrement d’association de sécurité IPsec** | **Passerelle Azure en tant qu’initiateur** | Passerelle Azure en tant que répondeur |
|---------------------------------------------------|--------------------------------------------------------------|--------------------------------------------------------------|
| 1 | ESP AES\_256 SHA | ESP AES\_128 SHA |
| 2 | ESP AES\_128 SHA | ESP 3\_DES MD5 |
| 3 | ESP 3\_DES MD5 | ESP 3\_DES SHA |
| 4 | ESP 3\_DES SHA | AH SHA1 avec ESP AES\_128 et HMAC Null |
| 5 | AH SHA1 avec ESP AES\_256 et HMAC Null | AH SHA1 avec ESP 3\_DES et HMAC Null |
| 6 | AH SHA1 avec ESP AES\_128 et HMAC Null | AH MD5 avec ESP 3\_DES et HMAC Null, aucune durée de vie proposée |
| 7 | AH SHA1 avec ESP 3\_DES et HMAC Null | AH SHA1 avec ESP 3\_DES SHA1, aucune durée de vie |
| 8 | AH MD5 avec ESP 3\_DES et HMAC Null, aucune durée de vie proposée | AH MD5 avec ESP 3\_DES MD5, aucune durée de vie |
| 9 | AH SHA1 avec ESP 3\_DES SHA1, aucune durée de vie | ESP DES MD5 |
| 10 | AH MD5 avec ESP 3\_DES MD5, aucune durée de vie | ESP DES SHA1, aucune durée de vie |
| 11 | ESP DES MD5 | AH SHA1 avec ESP DES et HMAC Null, aucune durée de vie proposée |
| 12 | ESP DES SHA1, aucune durée de vie | AH MD5 avec ESP DES et HMAC Null, aucune durée de vie proposée |
| 13\. | AH SHA1 avec ESP DES et HMAC Null, aucune durée de vie proposée | AH SHA1 avec ESP DES SHA1, aucune durée de vie |
| 14 | AH MD5 avec ESP DES et HMAC Null, aucune durée de vie proposée | AH MD5 avec ESP DES MD5, aucune durée de vie |
| 15 | AH SHA1 avec ESP DES SHA1, aucune durée de vie | ESP SHA, aucune durée de vie |
| 16 | AH MD5 avec ESP DES MD5, aucune durée de vie | ESP MD5, aucune durée de vie |
| 17 | - | AH SHA, aucune durée de vie | | 18 | - | AH MD5, aucune durée de vie |




- Vous pouvez spécifier le chiffrement IPsec ESP NULL avec la passerelle VPN à routage dynamique et hautes performances, conçue pour les connexions de réseau virtuel à réseau virtuel dans les réseaux Azure. 

- Pour les connexions entre locaux par le biais d’Internet, utilisez les paramètres de passerelle VPN Azure par défaut avec les algorithmes de chiffrement et de hachage répertoriés dans les tableaux ci-dessus pour garantir la sécurité de vos communications cruciales.

## Étapes suivantes


Pour plus d’informations sur les passerelles VPN, voir l’article [À propos des passerelles VPN](vpn-gateway-about-vpngateways.md).

Pour configurer un VPN site à site, voir l’article [Créer un réseau virtuel avec une connexion VPN site à site](vpn-gateway-site-to-site-create.md).

<!---HONumber=Oct15_HO3-->