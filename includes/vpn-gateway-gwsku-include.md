Lorsque vous créez une passerelle de réseau virtuel, vous devez spécifier la référence SKU de passerelle que vous voulez utiliser. Sélectionnez les références SKU qui répondent à vos besoins en fonction des types de charges de travail, des débits, des fonctionnalités et des contrats de niveau de service.

[!INCLUDE [classic SKU](./vpn-gateway-classic-sku-support-include.md)]

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

###  <a name="workloads"></a>Production *par rapport aux* charges de travail de développement et de test

En raison de différences des contrats de niveau de service et des ensembles de fonctionnalités, nous vous recommandons les références SKU suivantes pour la production *par rapport au* développement et au test :

| **Charge de travail**                       | **Références SKU**               |
| ---                                | ---                    |
| **Production, charges de travail critiques** | VpnGw1, VpnGw2, VpnGw3 |
| **Développement-Test ou preuve de concept**   | De base                  |
|                                    |                        |

Si vous utilisez les anciennes références SKU, les recommandations en termes de références SKU de production sont Standard et HighPerformance. Pour plus d’informations sur les anciennes références SKU, consultez [Références SKU de passerelle (anciennes)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md).

###  <a name="feature"></a>Ensembles de fonctionnalités de référence SKU de passerelle

Les nouvelles références SKU de passerelle rationalisent les ensembles de fonctionnalités proposés sur les passerelles :

| **Référence (SKU)**| **Caractéristiques**|
| ---    | ---         |
| VpnGw1<br>VpnGw2<br>VpnGw3|VPN basé sur l’itinéraire jusqu’à 30 tunnels* <br>P2S, BGP, actif/actif, stratégie IPsec/IKE personnalisée, coexistence de ExpressRoute/VPN <br><br>* Vous pouvez configurer « PolicyBasedTrafficSelectors » pour connecter une passerelle VPN basée sur l’itinéraire (VpnGw1, VpnGw2, VpnGw3) à plusieurs pare-feu locaux basés sur des stratégies. Pour en savoir plus, consultez la section [Connect VPN gateways to multiple on-premises policy-based VPN devices using PowerShell](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md) (Connecter des passerelles VPN à plusieurs appareils VPN basés sur des stratégies via PowerShell). |
|De base   | Basé sur l’itinéraire : 10 tunnels avec P2S<br>Basée sur la stratégie (IKEv1) : 1 tunnel 1 ; aucun P2S|
|        |             |

###  <a name="resize"></a>Redimensionnement des références SKU de passerelle

1. Vous pouvez effectuer un redimensionnement entre les références SKU VpnGw1, VpnGw2 et VpnGw3.
2. Lors de l’utilisation des anciennes références SKU de passerelle, vous pouvez effectuer un redimensionnement entre les références SKU Basic, Standard et HighPerformance.
2. Vous **ne pouvez pas** effectuer de redimensionnement pour passer de références SKU Basic/Standard/HighPerformance à de nouvelles références SKU VpnGw1/VpnGw2/VpnGw3. Au lieu de cela, vous devez [effectuer une migration](#migrate) vers les nouvelles références SKU.

###  <a name="migrate"></a>Effectuer une migration depuis les anciennes références SKU vers les nouvelles références SKU

[!INCLUDE [Migrate SKU](./vpn-gateway-migrate-legacy-sku-include.md)]