<properties 
   pageTitle="À propos des périphériques VPN pour les connexions des passerelles VPN site à site pour les réseaux virtuels Azure | Microsoft Azure"
   description="Découvrez les périphériques VPN et les paramètres IPsec pour les connexions de passerelle VPN site à site (S2S). Les connexions site à site peuvent être utilisées pour les configurations hybrides. Cet article contient des liens vers des instructions de configuration et des exemples pour les passerelles VPN."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor="" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/14/2015"
   ms.author="cherylmc" />

# À propos des périphériques VPN pour les connexions de la passerelle VPN de site à site

La configuration d'une connexion VPN site à site (S2S) nécessite un périphérique VPN. Vous pouvez utiliser des connexions site à site pour créer une solution hybride, ou chaque fois que vous souhaitez disposer d'une connexion sécurisée entre votre réseau local et votre réseau virtuel. Cet article traite des périphériques VPN compatibles et des paramètres de configuration associés. Veuillez noter que lorsque vous configurez une connexion site à site, une adresse IPv4 publique est requise pour votre périphérique VPN.

Si votre périphérique n'apparaît pas dans la table de périphériques VPN validés, consultez la section sur les périphériques VPN non validés de cet article. Il est possible que votre périphérique fonctionne toujours avec Azure. Pour plus d’informations sur la prise en charge des périphériques VPN, contactez le fabricant de votre périphérique.

**Éléments à noter lorsque vous affichez les tables :**

- Une modification de la terminologie a eu lieu pour le routage statique et dynamique. Vous rencontrerez probablement les deux termes. Seuls les noms ont été modifiés, pas la fonctionnalité.
	- Routage statique = basé sur des stratégies
	- Routage dynamique = basé sur un itinéraire 
- Sauf indication contraire, les spécifications des passerelles VPN hautes performances sont identiques à celles des passerelles VPN basées sur itinéraire. Par exemple, les périphériques VPN validés qui sont compatibles avec les passerelles VPN basées sur itinéraire sont également compatibles avec la nouvelle passerelle VPN hautes performances Azure. 


## Périphériques VPN validés 

Nous avons validé un ensemble de périphériques VPN standard en partenariat avec des fournisseurs de périphériques. Tous les périphériques des familles de périphériques figurant dans la liste ci-dessous doivent fonctionner avec les passerelles VPN Azure. Consultez l'article sur les [passerelles VPN](vpn-gateway-about-vpngateways.md) pour vérifier le type de passerelle dont vous avez besoin pour créer la solution que vous souhaitez configurer.

Pour configurer plus facilement votre périphérique VPN, reportez-vous aux liens qui correspondent à la famille de périphériques appropriée.



| **Fournisseur** | **Famille de périphériques** | **Version de système d’exploitation minimale** | **Basé sur des stratégies** | **Basé sur un itinéraire** |
|---------------------------------|----------------------------------------------------------|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Allied Telesis | Routeurs VPN série AR | 2\.9.2 | Bientôt disponible | Non compatible |
| Barracuda Networks, Inc. | Pare-feu Barracuda NG | Pare-feu Barracuda NG 5.4.3 | [Pare-feu Barracuda NG](https://techlib.barracuda.com/display/BNGV54/How%20to%20Configure%20an%20IPsec%20Site-to-Site%20VPN%20to%20a%20Windows%20Azure%20VPN%20Gateway)| Non compatible |
| Barracuda Networks, Inc. | Pare-feu Barracuda | Pare-feu Barracuda 6.5 | [Pare-feu Barracuda](https://techlib.barracuda.com/BFW/ConfigAzureVPNGateway) | Non compatible |
| Brocade | Routeur virtuel Vyatta 5400 | Routeur virtuel 6.6R3 GA | [Instructions de configuration](http://www1.brocade.com/downloads/documents/html_product_manuals/vyatta/vyatta_5400_manual/wwhelp/wwhimpl/js/html/wwhelp.htm#href=VPN_Site-to-Site%20IPsec%20VPN/Preface.1.1.html) | Non compatible |
| Check Point | Passerelle de sécurité | R75.40, R75.40VS | [Instructions de configuration](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) | [Instructions de configuration](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco | ASA | 8\.3 | [Exemples Cisco](http://go.microsoft.com/fwlink/p/?LinkID=717348) | Non compatible |
| Cisco | ASR | IOS 15.1 (basé sur des stratégies), IOS 15.2 (basé sur un itinéraire) | [Exemples Cisco](http://go.microsoft.com/fwlink/p/?LinkID=717348) | [Exemples Cisco](http://go.microsoft.com/fwlink/p/?LinkID=717348) |
| Cisco | ISR | IOS 15.0 (basé sur des stratégies), IOS 15.1 (basé sur un itinéraire) | [Exemples Cisco](http://go.microsoft.com/fwlink/p/?LinkID=717348) | [Exemples Cisco](http://go.microsoft.com/fwlink/p/?LinkID=717348) |
| Citrix | Appliance MPX CloudBridge ou appliance virtuelle VPX | N/A | [Instructions d’intégration](https://www.citrix.com/welcome.html?resource=%2Fdownloads%2Fcloudbridge%2Fbetas-and-tech-previews%2Fcloudbridge-azure-integration) | Non compatible |
| Dell SonicWALL | Série TZ, Série NSA, Série SuperMassive, Série NSA classe E | SonicOS 5.8.x, [SonicOS 5.9.x](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=850), [SonicOS 6.x](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=646) | [Instructions - SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646) [Instructions - SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850) | [Instructions - SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646) [Instructions - SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850) |
| F5 | Série BIG-IP | N/A | [Instructions de configuration](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip) | Non compatible |
| Fortinet | FortiGate | FortiOS 5.0.7 | [Instructions de configuration](http://docs.fortinet.com/fortigate/admin-guides) | [Instructions de configuration](http://docs.fortinet.com/fortigate/admin-guides) |
| Internet Initiative Japan (IIJ) | Série SEIL | SEIL/X 4.60, SEIL/B1 4.60, SEIL/x86 3.20 | [Instructions de configuration](http://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf) | Non compatible |
| Juniper | SRX | JunOS 10.2 (basé sur des stratégies), JunOS 11.4 (basé sur un itinéraire) | [Exemples Juniper](http://go.microsoft.com/fwlink/p/?LinkId=717760) | [Exemples Juniper](http://go.microsoft.com/fwlink/p/?LinkId=717760) |
| Juniper | Série J | JunOS 10.4r9 (basé sur des stratégies), JunOS 11.4 (basé sur un itinéraire) | [Exemples Juniper](http://go.microsoft.com/fwlink/p/?LinkId=717760) | [Exemples Juniper](http://go.microsoft.com/fwlink/p/?LinkId=717760) |
| Juniper | ISG | ScreenOS 6.3 (basé sur des stratégies et basé sur un itinéraire) | [Exemples Juniper](http://go.microsoft.com/fwlink/p/?LinkId=717760) | [Exemples Juniper](http://go.microsoft.com/fwlink/p/?LinkId=717760) |
| Juniper | SSG | ScreenOS 6.2 (basé sur des stratégies et basé sur un itinéraire) | [Exemples Juniper](http://go.microsoft.com/fwlink/p/?LinkId=717760) | [Exemples Juniper](http://go.microsoft.com/fwlink/p/?LinkId=717760) |
| Microsoft | Service de routage et d’accès à distance | Windows Server 2012 | Non compatible | [Exemples Microsoft](http://go.microsoft.com/fwlink/p/?LinkId=717761) |
| Openswan | Openswan | 2\.6.32 | (Bientôt disponible) | Non compatible |
| Palo Alto Networks | Tous les périphériques exécutant PAN-OS 5.0 ou une version ultérieure | PAN-OS 5x ou version ultérieure | [Palo Alto Networks](https://support.paloaltonetworks.com/) | Non compatible |
| Watchguard | Tout | Fireware XTM v11.x | [Instructions de configuration](http://customers.watchguard.com/articles/Article/Configure-a-VPN-connection-to-a-Windows-Azure-virtual-network/) | Non compatible |


## Périphériques VPN non validés

Si votre appareil n'est pas répertorié dans le tableau des périphériques VPN validés (ci-dessus), il peut tout de même fonctionner avec une connexion site à site. Vérifiez que votre périphérique VPN répond à la configuration minimale requise décrite dans la section de configuration de la passerelle de l'article [À propos des passerelles VPN](vpn-gateway-about-vpngateways.md#gateway-requirements). Les périphériques qui répondent à la configuration minimale requise sont censés fonctionner également avec les passerelles VPN. Contactez le fabricant de votre périphérique pour obtenir une prise en charge et des instructions de configuration supplémentaires.


## Modification des exemples de configuration de périphérique

Après avoir téléchargé l’exemple de configuration de périphérique VPN fourni, vous devrez remplacer certaines des valeurs spécifiées pour qu’elles reflètent les paramètres de votre environnement.

**Pour modifier un exemple :**

1. Ouvrez l’exemple à l’aide du Bloc-notes. 
1. Recherchez et remplacez toutes les chaînes au format <*texte*> par les valeurs qui correspondent à votre environnement. Prenez soin d'inclure < and >. Lorsque vous sélectionnez un nom, assurez-vous qu’il est unique. Si une commande ne fonctionne pas, consultez la documentation du fabricant du périphérique.

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
| &lt;SP\_AzureGatewayIpAddress&gt; | Ces informations sont propres à votre réseau virtuel et figurent dans le portail de gestion sous l'intitulé **Adresse IP de la passerelle**. |
| &lt;SP\_PresharedKey&gt; | Ces informations sont propres à votre réseau virtuel et figurent dans le Portail de gestion sous l’intitulé Gérer la clé. |



## Paramètres IPsec

### Configuration IKE Phase 1

| **Propriété** | **Basé sur des stratégies** | **Basé sur un itinéraire et passerelle VPN standard ou hautes performances** |
|----------------------------------------------------|--------------------------------|------------------------------------------------------------------|
| Version IKE | IKEv1 | IKEv2 |
| Groupe Diffie-Hellman | Groupe 2 (1 024 bits) | Groupe 2 (1 024 bits) |
| Méthode d'authentification | Clé prépartagée | Clé prépartagée |
| Algorithmes de chiffrement | AES256 AES128 3DES | AES256 3DES |
| Algorithme de hachage | SHA1(SHA128) | SHA1(SHA128) |
| Durée de vie d’association de sécurité de phase 1 (temps) | 28 800 secondes | 28 800 secondes |


### Configuration IKE Phase 2

| **Propriété** | **Basé sur des stratégies** | **Basé sur un itinéraire et passerelle VPN standard ou hautes performances** |
|--------------------------------------------------------------------------|------------------------------------------------|--------------------------------------------------------------------|
| Version IKE | IKEv1 | IKEv2 |
| Algorithme de hachage | SHA1(SHA128) | SHA1(SHA128) |
| Durée de vie d’association de sécurité de phase 2 (temps) | 3 600 secondes | - | | Durée de vie d’association de sécurité de phase 2 (débit) | 102 400 000 Ko | - | | Offres d’authentification et de chiffrement d’association de sécurité IPsec (par ordre de préférence) | 1. ESP-AES256 2. ESP-AES128 3. ESP-3DES 4. N/A | Voir la section *Offres d'association de sécurité IPsec pour passerelle basée sur un itinéraire* (ci-dessous) | | PFS (Perfect Forward Secrecy) | Non | Oui (groupe 1 DH) | | Détection d’homologue mort | Non prise en charge | Prise en charge |

### Offres d'association de sécurité IPsec pour passerelle basée sur un itinéraire

Le tableau ci-après répertorie les offres d’authentification et de chiffrement d’association de sécurité IPsec. Les offres sont énumérées dans l’ordre de préférence dans lequel elles sont présentées ou acceptées.

| **Offres d’authentification et de chiffrement d’association de sécurité IPsec** | **Passerelle Azure en tant qu’initiateur** | **Passerelle Azure en tant que répondeur** |
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


- Vous pouvez spécifier le chiffrement IPsec ESP NULL avec les passerelles VPN basées sur un itinéraire et hautes performances. Ceci est conçu pour les connexions VNet à VNet dans les réseaux Azure. 

- Pour les connexions entre locaux par le biais d’Internet, utilisez les paramètres de passerelle VPN Azure par défaut avec les algorithmes de chiffrement et de hachage répertoriés dans les tableaux ci-dessus pour garantir la sécurité de vos communications cruciales.

<!---HONumber=AcomDC_1217_2015-->