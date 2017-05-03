Vous pouvez vérifier que votre connexion a réussi à l’aide de la commande CLI suivante. Configurez vos propres valeurs. Dans l’exemple,  -n fait référence au nom de la connexion que vous avez créée et que vous voulez tester.

```azurecli
az network vpn-connection show -n VNet1toSite2 -g TestRG1
```

Tout au long de l’établissement de la connexion, l’état de la connexion indique « Connexion en cours ». Une fois la connexion établie, l’état indique « Connecté » et vous pouvez voir les octets d’entrée et de sortie.