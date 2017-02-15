Si vous ne l’avez pas encore fait, vous pouvez obtenir un [essai gratuit d’abonnement Azure](https://azure.microsoft.com/pricing/free-trial/) et connecter [l’interface de ligne de commande Azure](../articles/xplat-cli-install.md) [à votre compte Azure](../articles/xplat-cli-connect.md). Assurez-vous que l’interface de ligne de commande Azure est en mode Resource Manager comme indiqué ci-après :

```azurecli
azure config mode arm
```

À présent, créez maintenant votre groupe de machines virtuelles identiques à l’aide de la commande `azure vmss quick-create`. L’exemple suivant crée un groupe identique Linux nommé `myVMSS` avec 5 instances de machine virtuelle dans le groupe de ressources nommé `myResourceGroup` :

```azurecli
azure vmss quick-create -n myVMSS -g myResourceGroup -l westus \
    -u ops -p P@ssw0rd! \
    -C 5 -Q Canonical:UbuntuServer:16.04.0-LTS:latest
```

L’exemple suivant crée un groupe identique Windows avec la même configuration :

```azurecli
azure vmss quick-create -n myVMSS -g myResourceGroup -l westus \
    -u ops -p P@ssw0rd! \
    -C 5 -Q MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest
```

Si vous souhaitez personnaliser l’emplacement ou l’URN de l’image, examinez les commandes `azure location list` et `azure vm image {list-publishers|list-offers|list-skus|list|show}`.

Une fois la commande exécutée, le jeu de la mise à l’échelle est créé. Ce jeu de mise à l’échelle a un équilibreur de charge avec des règles NAT qui mappe le port 50,000+i sur l’équilibreur de charge sur le port 22 sur la machine virtuelle i. Par conséquent, une fois que nous avons déterminé le nom de domaine complet de l’équilibreur de charge, nous pouvons nous connecter au moyen de SSH à nos machines virtuelles :

```bash
# (if you decide to run this as a script, please invoke using bash)

# list load balancers in the resource group we created
#
# generic syntax:
# azure network lb list -g RESOURCE-GROUP-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network lb list -g negatvmssrg | grep negatvmssrg)
split_line=( $line )
lb_name=${split_line[1]}

# now that we have the name of the load balancer, we can show the details to find which Public IP (PIP) is 
# associated to it
#
# generic syntax:
# azure network lb show -g RESOURCE-GROUP-NAME -n LOAD-BALANCER-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network lb show -g negatvmssrg -n $lb_name | grep loadBalancerFrontEnd)
split_line=( $line )
pip_name=${split_line[4]}

# now that we have the name of the public IP address, we can show the details to find the FQDN
#
# generic syntax:
# azure network public-ip show -g RESOURCE-GROUP-NAME -n PIP-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network public-ip show -g negatvmssrg -n $pip_name | grep FQDN)
split_line=( $line )
FQDN=${split_line[3]}

# now that we have the FQDN, we can use ssh on port 50,000+i to connect to VM i (where i is 0-indexed)
#
# example to connct via ssh into VM "0":
ssh -p 50000 negat@$FQDN
```

<!--HONumber=Feb17_HO2-->


