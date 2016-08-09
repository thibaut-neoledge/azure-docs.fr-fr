VNet Peering est un mécanisme permettant de connecter deux réseaux virtuels situés dans la même région via le réseau principal Azure. Une fois homologués, les deux réseaux virtuels apparaissent comme un seul et même réseau virtuel pour tous les besoins de connectivité. Si vous ne connaissez pas VNet Peering, consultez l’article [VNet Peering](../articles/virtual-network/virtual-network-peering-overview.md).

VNet Peering est en version préliminaire publique. Pour pouvoir l’utiliser, vous devez vous inscrire à l’aide de la commande ci-dessous :

> [AZURE.NOTE] Register-AzureRmProviderFeature -FeatureName AllowVnetPeering -ProviderNamespace Microsoft.Network –force
 

<!---HONumber=AcomDC_0803_2016-->