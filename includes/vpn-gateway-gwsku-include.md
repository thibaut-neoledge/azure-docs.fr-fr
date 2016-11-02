Lorsque vous créez une passerelle de réseau virtuel, vous devez spécifier la référence SKU de passerelle que vous voulez utiliser. Si vous sélectionnez une référence SKU de passerelle supérieure, la quantité de ressources processeur et de bande passante réseau allouées à la passerelle augmente. Par conséquent, la passerelle peut prendre en charge un débit réseau plus élevé sur le réseau virtuel.

La passerelle VPN peut utiliser les références SKU suivantes :

- De base
- Standard
- HighPerformance

Lorsque vous sélectionnez une référence SKU, tenez compte des restrictions suivantes :

- Si vous souhaitez utiliser un type de VPN basé sur uns stratégie, vous devez utiliser la référence SKU de passerelle De base. Les VPN basés sur une stratégie (précédemment appelés « routage statique ») ne sont pas pris en charge sur une autre SKU.
- Le protocole BGP n’est pas pris en charge pour la référence SKU De base.
- Les configurations de passerelle VPN et ExpressRoute coexistants ne sont pas prises en charge par la référence SKU De base.


<!--HONumber=Oct16_HO2-->


