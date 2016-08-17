VNet Peering est un mécanisme permettant de connecter deux réseaux virtuels situés dans la même région via le réseau principal Azure. Une fois homologués, les deux réseaux virtuels apparaissent comme un seul et même réseau virtuel pour tous les besoins de connectivité. Si vous ne connaissez pas VNet Peering, consultez l’article [VNet Peering](../articles/virtual-network/virtual-network-peering-overview.md).

VNet Peering est disponible en version préliminaire publique. Pour pouvoir l’utiliser, vous devez vous inscrire à l’aide des commandes ci-dessous :

    Register-AzureRmProviderFeature -FeatureName AllowVnetPeering -ProviderNamespace Microsoft.Network

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
 

<!---HONumber=AcomDC_0810_2016-->