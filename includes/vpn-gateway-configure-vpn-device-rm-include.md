Les connexions site à site vers un réseau local nécessitent un périphérique VPN. De nombreux périphériques VPN fonctionnent avec Azure. Pour plus d’informations sur les périphériques VPN et les paramètres de configuration, voir [Périphériques VPN](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md). Avant de configurer votre périphérique VPN, identifiez également les éventuels [Problèmes de compatibilité connus avec le matériel](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#known) pour le périphérique VPN que vous souhaitez utiliser. Pour des informations de configuration spécifiques, consultez le fabricant de votre périphérique VPN.

Pour configurer votre périphérique VPN, vous avez besoin des éléments suivants :

- **L’adresse IP publique** de votre passerelle de réseau virtuel.

    -  Pour rechercher l’adresse IP publique à l’aide du portail Azure, accédez à **Passerelles de réseau virtuel**, puis cliquez sur le nom de votre passerelle. 

    - Pour trouver l’adresse IP publique de votre passerelle de réseau virtuel à l’aide de PowerShell, utilisez l’exemple suivant en remplaçant les valeurs par les vôtres.

            Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG
- **Une clé partagée**. Il s’agit de la même clé partagée que celle spécifiée lors de la création de votre connexion VPN de site à site. Dans nos exemples, nous utilisons une clé partagée très basique. Vous devez générer une clé plus complexe à utiliser.




