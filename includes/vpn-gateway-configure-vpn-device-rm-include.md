
Vous aurez besoin de l’adresse IP publique de la passerelle de réseau virtuel pour configurer votre périphérique VPN local. Consultez le fabricant de votre périphérique pour obtenir des informations de configuration spécifiques et configurer votre périphérique. Consultez [Périphériques VPN](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md) pour obtenir plus d’informations sur les périphériques VPN qui fonctionnent bien avec Azure.

Pour trouver l'adresse IP publique de votre passerelle de réseau virtuel à l'aide de PowerShell, utilisez l'exemple suivant :

	Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG

Vous pouvez également afficher l'adresse IP publique de votre passerelle de réseau virtuel à l'aide du portail Azure. Accédez à la section **Passerelles de réseau virtuel**, puis cliquez sur le nom de votre passerelle.

<!---HONumber=AcomDC_0406_2016-->