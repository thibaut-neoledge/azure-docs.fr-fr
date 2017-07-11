<a id="is-custom-ipsecike-policy-supported-on-all-azure-vpn-gateway-skus" class="xliff"></a>

### La stratégie personnalisée IPsec/IKE est-elle prise en charge sur toutes les références de passerelle VPN Azure ?
La stratégie personnalisée IPsec/IKE est prise en charge sur les passerelles VPN Azure **VpnGw1, VpnGw2, VpnGw3, Standard** et **HighPerformance**. La référence **De base** N’EST PAS prise en charge.

<a id="how-many-policies-can-i-specify-on-a-connection" class="xliff"></a>

### Combien de stratégies puis-je spécifier pour une connexion ?
Vous pouvez uniquement spécifier ***une*** combinaison de stratégie pour une connexion donnée.

<a id="can-i-specify-a-partial-policy-on-a-connection-eg-only-ike-algorithms-but-not-ipsec" class="xliff"></a>

### Puis-je spécifier une stratégie partielle pour une connexion ? (Par exemple, uniquement les algorithmes IKE mais pas IPsec)
Non, vous devez spécifier tous les algorithmes et paramètres pour IKE (mode principal) et IPsec (mode rapide). Vous n’êtes pas en droit de spécifier de stratégie partielle.

<a id="what-are-the-algorithms-and-key-strengths-supported-in-the-custom-policy" class="xliff"></a>

### Quels sont les algorithmes et les forces de clé pris en charge dans la stratégie personnalisée ?
Le tableau ci-dessous répertorie les algorithmes de chiffrement et les forces de clé pris en charge et configurables par les clients. Vous devez sélectionner une option pour chaque champ.

| **IPsec/IKEv2**  | **Options**                                                                 |
| ---              | ---                                                                         |
| Chiffrement IKEv2 | AES256, AES192, AES128, DES3, DES                                           |
| Intégrité IKEv2  | SHA384, SHA256, SHA1, MD5                                                   |
| Groupe DH         | ECP384, ECP256, DHGroup24, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, Aucun |
| Chiffrement IPsec | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, Aucun    |
| Intégrité IPsec  | GCMAES256, GCMAES192, GCMAES128, SHA256, SHA1, MD5                          |
| Groupe PFS        | ECP384, ECP256, PFS24, PFS2048, PFS14, PFS2, PFS1, Aucun                     |
| Durée de vie de l’AS en mode rapide*  | Secondes (entier ; **min. 300**) et Ko (entier ; **min. 1024**)                                      |
| Sélecteur de trafic | UsePolicyBasedTrafficSelectors** ($True/$False; default $False)                             |
|                  |                                                                             |

* (*) La durée de vie de l’AS en mode principal IKEv2 est fixée à 28 800 secondes pour les passerelles VPN Azure
* (**) Consultez le point suivant du FAQ pour « UsePolicyBasedTrafficSelectors »

<a id="does-everything-need-to-match-between-the-azure-vpn-gateway-policy-and-my-on-premises-vpn-device-configurations" class="xliff"></a>

### Tous les éléments entre la stratégie de passerelle VPN Azure et mes configurations de périphérique VPN local doivent-ils correspondre ?
La configuration de votre périphérique VPN local doit correspondre aux algorithmes et paramètres suivants, spécifiés dans la stratégie IPsec/IKE Azure ou les contenir :

* Algorithme de chiffrement IKE
* Algorithme d’intégrité IKE
* Groupe DH
* Algorithme de chiffrement IPsec
* Algorithme d’intégrité IPsec
* Groupe PFS
* Sélecteur de trafic (*)

Les durées de vie de l’AS sont uniquement des spécifications locales, elles n’ont pas besoin de correspondre.

Si vous activez **UsePolicyBasedTrafficSelectors**, vous devez vous assurer que votre périphérique VPN dispose des sélecteurs de trafic correspondant définis avec toutes les combinaisons de préfixes (passerelle réseau locale) de votre réseau local vers et depuis les préfixes Azure, plutôt que de manière individuelle. Par exemple, si les préfixes de votre réseau local sont 10.1.0.0/16 et 10.2.0.0/16 et si les préfixes de votre réseau virtuel sont 192.168.0.0/16 et 172.16.0.0/16, vous devez spécifier les sélecteurs de trafic suivants :
* 10.1.0.0/16 <====> 192.168.0.0/16
* 10.1.0.0/16 <====> 172.16.0.0/16
* 10.2.0.0/16 <====> 192.168.0.0/16
* 10.2.0.0/16 <====> 172.16.0.0/16

Reportez-vous à [Connect Azure VPN gateways to multiple on-premises policy-based VPN devices using PowerShell](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md) (Connecter des passerelles VPN Azure à plusieurs périphériques VPN basés sur une stratégie locale à l’aide de PowerShell) pour plus d’informations sur l’utilisation de cette option.

<a id="does-the-custom-policy-replace-the-default-ipsecike-policy-sets-for-azure-vpn-gateways" class="xliff"></a>

### La stratégie personnalisée remplace-t-elle les jeux de stratégie IPsec/IKE par défaut pour les passerelles VPN Azure ?
Oui, une fois qu’une stratégie personnalisée est spécifiée pour une connexion, la passerelle VPN Azure utilisera uniquement la stratégie pour la connexion, à la fois en tant qu’initiateur IKE et que répondeur IKE.

<a id="if-i-remove-a-custom-ipsecike-policy-does-the-connection-become-unprotected" class="xliff"></a>

### Si je supprime une stratégie IPsec/IKE personnalisée, la connexion devient-elle non protégée ?
Non, la connexion restera protégée par IPsec/IKE. Après avoir supprimé la stratégie personnalisée d’une connexion, la passerelle VPN Azure revient à la [liste par défaut de propositions IPsec/IKE](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md) et relance la négociation IKE avec votre périphérique VPN local.

<a id="would-adding-or-updating-an-ipsecike-policy-disrupt-my-vpn-connection" class="xliff"></a>

### L’ajout ou la mise à jour d’une stratégie IPsec/IKE risquent-ils de perturber ma connexion VPN ?
Oui, ces opérations peuvent légèrement perturber votre connexion VPN (pendant quelques secondes), puisque la passerelle VPN Azure va éliminer la connexion existante et relancer la négociation IKE pour rétablir le tunnel IPsec avec les nouveaux algorithmes de chiffrement et paramètres. Vérifiez que votre périphérique VPN local est également configuré avec les algorithmes et les forces de clé correspondant pour minimiser les perturbations.

<a id="can-i-use-different-policies-on-different-connections" class="xliff"></a>

### Puis-je utiliser des stratégies différentes pour des connexions distinctes ?
Oui. Une stratégie personnalisée est appliquée sur une base par connexion. Vous pouvez créer et appliquer des stratégies IPsec/IKE différentes pour des connexions distinctes. Vous pouvez également choisir d’appliquer des stratégies personnalisées pour un sous-ensemble de connexions. Les autres connexions utiliseront les jeux de stratégie IPsec/IKE Azure par défaut.

<a id="can-i-use-the-custom-policy-on-vnet-to-vnet-connection-as-well" class="xliff"></a>

### Puis-je également utiliser la stratégie personnalisée pour une connexion entre des réseaux virtuels ?
Oui, vous pouvez appliquer la stratégie personnalisée pour des connexions IPsec entre différents sites locaux ou des connexions entre des réseaux virtuels.

<a id="do-i-need-to-specify-the-same-policy-on-both-vnet-to-vnet-connection-resources" class="xliff"></a>

### Dois-je spécifier la même stratégie pour les ressources de connexion entre des réseaux virtuels ?
Oui. Un tunnel entre des réseaux virtuels se compose de deux ressources de connexion dans Azure, une pour chaque direction. Vous devez vous assurer que les ressources de connexion ont la même stratégie, sinon la connexion entre les réseaux virtuels ne pourra pas être établie.

<a id="does-custom-ipsecike-policy-work-on-expressroute-connection" class="xliff"></a>

### La stratégie IPsec/IKE est-elle compatible avec une connexion ExpressRoute ?
Non. La stratégie IPsec/IKE est uniquement compatible avec les connexions S2S VPN et entre des réseaux virtuels via les passerelles VPN Azure.
