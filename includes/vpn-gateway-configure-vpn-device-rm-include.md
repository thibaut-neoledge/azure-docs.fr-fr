Les connexions site à site vers un réseau local nécessitent un périphérique VPN. Nous ne fournissons pas les étapes de configuration de tous les périphériques VPN, mais les informations des liens ci-dessous pourront vous être utiles :

- Pour plus d’informations sur les périphériques VPN compatibles, consultez la page [Périphériques VPN](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md). 
- Vous trouverez des liens vers les paramètres de configuration des périphériques sur la page [Périphériques VPN validés](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable). Les liens de configuration des périphériques sont fournis dans la mesure du possible. Il est toujours préférable de vérifier les dernières informations de configuration auprès du fabricant du périphérique.
- Pour plus d’informations sur la modification des exemples de configuration des périphériques, consultez la page [Modifier les exemples](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#editing).
- En ce qui concerne les paramètres IPsec/IKE, consultez la page [Paramètres](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec).
- Avant de configurer votre périphérique VPN, identifiez également les éventuels [Problèmes de compatibilité connus avec le matériel](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#known) pour le périphérique VPN que vous souhaitez utiliser.

Pour configurer votre périphérique VPN, vous avez besoin des éléments suivants :

- L’adresse IP publique de votre passerelle de réseau virtuel.

    -  Pour rechercher l’adresse IP publique à l’aide du portail Azure, accédez à **Passerelles de réseau virtuel**, puis cliquez sur le nom de votre passerelle. 
    - Pour trouver l’adresse IP publique de votre passerelle de réseau virtuel à l’aide de PowerShell, utilisez l’exemple suivant en remplaçant les valeurs par les vôtres.

            Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG
- Une clé partagée. Il s’agit de la clé partagée spécifiée lors de la création de la connexion VPN de site à site. Dans nos exemples, nous utilisons une clé partagée très basique. Vous devez générer une clé plus complexe à utiliser.




