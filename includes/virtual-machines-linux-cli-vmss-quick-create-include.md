Si ce n’est pas déjà fait, vous pouvez vous inscrire gratuitement à un [abonnement Azure](https://azure.microsoft.com/pricing/free-trial/) et [connecter l’interface de ligne de commande Azure](../articles/xplat-cli-install.md) [à votre compte Azure](../articles/xplat-cli-connect.md). Vous pouvez ensuite exécuter les commandes suivantes pour créer rapidement un jeu de mise à l’échelle :

```bash
# make sure we are in Resource Manager mode (https://azure.microsoft.com/documentation/articles/resource-manager-deployment-model/)
azure config mode arm

# quick-create a scale set
#
# generic syntax:
# azure vmss quick-create -n SCALE-SET-NAME -g RESOURCE-GROUP-NAME -l LOCATION -u USERNAME -p PASSWORD -C INSTANCE-COUNT -Q IMAGE-URN
#
# example:
azure vmss quick-create -n negatvmss -g negatvmssrg -l westus -u negat -p P4ssw0rd -C 5 -Q Canonical:UbuntuServer:14.04.4-LTS:latest
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

# now that we have the name of the load balancer, we can show the details to find which Public IP (PIP) is associated to it
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

<!---HONumber=AcomDC_0413_2016-->