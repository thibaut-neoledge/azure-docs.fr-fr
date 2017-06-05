Vous pouvez vérifier que votre connexion a réussi à l’aide de la commande [az network vpn-connection show](/cli/azure/network/vpn-connection#show). Dans l’exemple, «  --name » fait référence au nom de la connexion que vous voulez tester. Tout au long de l’établissement de la connexion, l’état de la connexion indique « Connexion en cours ». Une fois la connexion établie, l’état indique « Connecté ».

```azurecli
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```

