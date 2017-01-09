Lorsque vous créez une passerelle de réseau virtuel, vous devez spécifier la référence SKU de passerelle que vous voulez utiliser. Si vous sélectionnez une référence SKU de passerelle supérieure, la quantité de ressources processeur et de bande passante réseau allouées à la passerelle augmente. Par conséquent, la passerelle peut prendre en charge un débit réseau plus élevé sur le réseau virtuel.

La passerelle VPN peut utiliser les références SKU suivantes :

* De base
* Standard
* HighPerformance

La passerelle VPN n’utilise pas la référence de la passerelle UltraPerformance. Pour plus d’informations sur la référence UltraPerformance, consultez la documentation [ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md).

Lorsque vous sélectionnez une référence SKU, tenez compte des éléments suivants :

* Si vous souhaitez utiliser un type de VPN basé sur uns stratégie, vous devez utiliser la référence SKU De base. Les VPN basés sur une stratégie (précédemment appelés « routage statique ») ne sont pas pris en charge sur une autre SKU.
* Le protocole BGP n’est pas pris en charge pour la référence SKU De base.
* Les configurations de passerelle VPN et ExpressRoute coexistants ne sont pas prises en charge par la référence SKU De base.
* Les connexions VPN S2S en mode actif-actif avec des passerelles peuvent uniquement être configurées sur la référence SKU HighPerformance.



<!--HONumber=Jan17_HO1-->


