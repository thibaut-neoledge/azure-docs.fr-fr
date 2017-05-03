Pour modifier l’adresse IP de la passerelle, utilisez l’applet de commande « New-AzureRmVirtualNetworkGatewayConnection ». À l’heure actuelle, l’applet de commande « Set » ne permet pas de modifier l’adresse IP de la passerelle.

### <a name="gwipnoconnection"></a>Modifier l’adresse IP de la passerelle - sans connexion de passerelle
Pour modifier l’adresse IP de votre passerelle de réseau local si celle-ci ne dispose pas encore de connexion, utilisez l’exemple ci-dessous. Vous pouvez également modifier les préfixes d’adresse en même temps. Veillez à utiliser le nom existant de votre passerelle de réseau local pour remplacer les paramètres actuels. Si vous ne le faites pas, vous créerez une nouvelle passerelle de réseau local au lieu de remplacer la passerelle existante.

Utilisez l’exemple suivant en remplaçant les valeurs par les vôtres :

```powershell
New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
-Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
-GatewayIpAddress "5.4.3.2" -ResourceGroupName MyRGName
```

### <a name="gwipwithconnection"></a>Modifier l’adresse IP de la passerelle - avec une connexion de passerelle existante
S’il existe déjà une connexion de passerelle, vous devez tout d’abord supprimer la connexion. Une fois la connexion supprimée, vous pouvez modifier l’adresse IP de la passerelle et recréer une connexion. Vous pouvez également modifier les préfixes d’adresse en même temps. Cela entraînera une interruption de votre connexion VPN.

> [!IMPORTANT]
> Ne supprimez pas la passerelle VPN. Si vous procédez ainsi, vous devrez revenir en arrière pour la créer à nouveau. En outre, vous devez mettre à jour votre périphérique VPN local avec la nouvelle adresse IP de passerelle VPN.
> 
> 

1. Supprimez la connexion. Vous trouverez le nom de votre connexion en utilisant l’applet de commande « Get-AzureRmVirtualNetworkGatewayConnection ».

  ```powershell
  Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
  -ResourceGroupName MyRGName
  ```
2. Modifiez la valeur « GatewayIpAddress ». Vous pouvez également modifier les préfixes d’adresse en même temps. Veillez à utiliser le nom existant de votre passerelle de réseau local pour remplacer les paramètres actuels. Si vous ne le faites pas, vous créerez une nouvelle passerelle de réseau local au lieu de remplacer la passerelle existante.

  ```powershell
  New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
  -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
  -GatewayIpAddress "104.40.81.124" -ResourceGroupName MyRGName
  ```
3. Créez la connexion. Dans cet exemple, nous allons configurer un type de connexion IPsec. Lorsque vous recréez votre connexion, utilisez le type de connexion spécifié pour votre configuration. Pour les autres types de connexion, consultez la page sur [les applets de commande PowerShell](https://msdn.microsoft.com/library/mt603611.aspx) .  Pour obtenir le nom de VirtualNetworkGateway, vous pouvez exécuter l’applet de commande « Get-AzureRmVirtualNetworkGateway ».
   
    Définissez les variables.

  ```powershell
  $local = Get-AzureRMLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
  $vnetgw = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName MyRGName
  ```
   
    Créez la connexion.

  ```powershell 
  New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName `
  -Location "West US" `
  -VirtualNetworkGateway1 $vnetgw `
  -LocalNetworkGateway2 $local `
  -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
  ```