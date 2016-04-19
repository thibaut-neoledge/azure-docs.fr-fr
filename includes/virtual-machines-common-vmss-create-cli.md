Les jeux de mise à l’échelle de machine virtuelle vous permettent de gérer plusieurs machines virtuelles sous forme d’un ensemble. À un niveau élevé, les jeux de mise à l’échelle ont les avantages et inconvénients suivants :

Avantages :

1. Haute disponibilité : Chaque ensemble de mise à l’échelle place ses machines virtuelles dans un groupe à haute disponibilité avec 5 domaines d’erreur et 5 domaines de mise à jour pour garantir la disponibilité (pour plus d’informations sur les domaines d’erreur et les domaines de mise à jour, consultez la rubrique sur la [disponibilité des machines virtuelles](../articles/virtual-machines/virtual-machines-linux-manage-availability.md)).
2. Intégration facile dans l’équilibreur de charge Azure et App Gateway.
3. Intégration facile dans Azure Autoscale.
4. Déploiement, gestion et nettoyage de machines virtuelles simplifiés.
5. Prise en charge des versions courantes de Windows et Linux, ainsi que d’images personnalisées.

Inconvénients :

1. Impossible d’attacher des disques de données à des instances de machine virtuelle dans un jeu de mise à l’échelle. Au lieu de cela, vous devez utiliser le stockage d’objets Blob (Blob Storage), des fichiers Azure, des tables Azure ou une autre solution de stockage.

## Création rapide de machines virtuelles à l’aide de l’interface de ligne de commande

Si ce n’est pas déjà fait, vous pouvez vous procurer une [version d’essai gratuite d’abonnement Azure](https://azure.microsoft.com/pricing/free-trial/) et [l’interface de ligne de commande Azure](../articles/xplat-cli-install.md) et [les connecter à votre compte Azure](../articles/xplat-cli-connect.md). Vous pouvez ensuite exécuter les commandes suivantes pour créer rapidement un jeu de mise à l’échelle de machine virtuelle :

```bash
# make sure we are in resource manager mode 
azure config mode arm

# quick-create a VM scale set
#
# generic syntax:
# azure vmss quick-create -n VMSS-NAME -g RESOURCE-GROUP-NAME -l LOCATION -u USERNAME -p PASSWORD -C INSTANCE-COUNT -Q IMAGE-URN
#
# example:
azure vmss quick-create -n negatvmss -g negatvmssrg -l westus -u negat -p P4$$w0rd -C 5 -Q Canonical:UbuntuServer:14.04.4-LTS:latest
```

Si vous souhaitez personnaliser l’emplacement ou l’URN de l’image, veuillez examiner les commandes `azure location list` et `azure vm image {list-publishers|list-offers|list-skus|list|show}`.

Une fois la commande exécutée, le jeu de la mise à l’échelle est créé. Ce jeu de mise à l’échelle a un équilibreur de charge avec des règles NAT qui mappe le port 50,000+i sur l’équilibreur de charge sur le port 22 sur la machine virtuelle i. Par conséquent, une fois que nous aurons déterminé le nom de domaine complet de l’équilibreur de charge, nous serons en mesure d’activer SSH dans nos ordinateurs virtuels :

```bash
# list load balancers in the resource group we created
#
# generic syntax:
# azure network lb list -g RESOURCE-GROUP-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network lb list -g nsgvmsrg | grep nsgvmssrg)
split_line=( $line )
lb_name=${split_line[1]}

# now that we have the name of the load balancer, we can show the details to find which Public IP (PIP) is associated with it
#
# generic syntax:
# azure network lb show -g RESOURCE-GROUP-NAME -n LB-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network lb show -g nsgvmssrg -n $lb_name | grep loadBalancerFrontEnd)
split_line=( $line )
pip_name=${split_line[4]}

# now that we have the name of the public IP address, we can show the details to find the FQDN
#
# generic syntax:
# azure network public-ip show -g RESOURCE-GROUP-NAME -n PIP-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network public-ip show -g nsgvmssrg -n $pip_name | grep FQDN)
split_line=( $line )
FQDN=${split_line[3]}

# now that we have the FQDN, we can ssh on port 50,000+i to ssh into VM i (where i is 0-indexed)
#
# example to ssh into VM "0":
ssh -p 50000 $FQDN
```

## Étapes suivantes

Pour plus d’informations, consultez la [page d’accueil principale pour les jeux de mise à l’échelle de machine virtuelle](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

Pour rechercher de la documentation, consultez le [page de documentation principale pour les jeux de mise à l’échelle de machine virtuelle](https://azure.microsoft.com/documentation/services/virtual-machines-scale-sets/).

Pour les modèles Azure Resource Manager utilisant des jeux de mise à l’échelle de machine virtuelle, recherchez « vmss » dans le [référentiel github de modèles de démarrage rapide Azure](https://github.com/Azure/azure-quickstart-templates).

<!---HONumber=AcomDC_0406_2016-->