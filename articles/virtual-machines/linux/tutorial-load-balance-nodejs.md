---
title: "Didacticiel : Création d’une application hautement disponible sur des machines virtuelles Azure | Microsoft Docs"
description: "Apprenez à créer une application hautement disponible et sécurisée entre trois machines virtuelles Linux avec un équilibrage de charge dans Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/27/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 65ff0e03af6b43478f645274adfeb46d28418894
ms.lasthandoff: 04/07/2017

---

# <a name="build-a-load-balanced-highly-available-application-on-linux-virtual-machines-in-azure"></a>Création d’une application à haute disponibilité avec équilibrage de charge sur des machines virtuelles Linux dans Azure
Dans ce didacticiel, vous créez une application hautement disponible qui résiste aux événements de maintenance. L’application utilise trois machines virtuelles Linux, un groupe à haute disponibilité et un équilibrage de charge. Ce didacticiel développe une application Node.js, mais vous pouvez l’utiliser pour déployer une infrastructure d’application utilisant les mêmes instructions et composants de haute disponibilité.

## <a name="step-1---azure-prerequisites"></a>Étape 1 : Conditions préalables pour Azure
Pour suivre ce didacticiel, ouvrez une fenêtre de terminal et assurez-vous que vous avez installé la dernière version [d’Azure CLI 2.0](/cli/azure/install-azure-cli). Si vous n’êtes pas encore connecté à votre abonnement Azure, connectez-vous avec la commande [az login](/cli/azure/#login) et suivez les instructions à l’écran :

```azurecli
az login
```

Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Avant de pouvoir créer d’autres ressources Azure, vous devez créer un groupe de ressources avec [az group create](/cli/azure/group#create). L’exemple suivant crée un groupe de ressources nommé `myResourceGroup` à l’emplacement `westus` :

```azurecli
az group create --name myResourceGroup --location westus
```


## <a name="step-2---create-availability-set"></a>Étape 2 : Créer un groupe à haute disponibilité
Les machines virtuelles peuvent être créées sur les domaines de mise à jour et d’erreur logiques. Chaque domaine logique représente une partie du matériel dans le centre de données Azure sous-jacent. Lorsque vous créez deux ou plusieurs machines virtuelles, vos ressources de calcul et de stockage sont réparties sur ces domaines. Cette distribution assure la disponibilité de votre application si un composant matériel devait subir une maintenance. Les groupes à haute disponibilité permettent de définir ces domaines d’erreur et de mise à jour logiques.

Créez un groupe à haute disponibilité avec la commande [az vm availability-set create](/cli/azure/vm/availability-set#create). L’exemple suivant permet de créer un groupe à haute disponibilité nommé `myAvailabilitySet` :

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet \
    --platform-fault-domain-count 3 \
    --platform-update-domain-count 2
```


## <a name="step-3---create-load-balancer"></a>Étape 3 : Créer un équilibreur de charge
Un équilibrage de charge Azure répartit le trafic sur un ensemble de machines virtuelles définies à l’aide de règles d’équilibrage de charge. Une sonde d’intégrité surveille un port donné sur chaque machine virtuelle et ne distribue le trafic que vers une machine virtuelle opérationnelle.

### <a name="create-a-public-ip-address"></a>Créer une adresse IP publique
Pour accéder à votre application sur Internet, affectez une adresse IP publique à l’équilibrage de charge. Créez une adresse IP publique avec la commande [az network public-ip create](/cli/azure/public-ip#create). L’exemple suivant crée une adresse IP publique nommée `myPublicIP` :

```azurecli
az network public-ip create --resource-group myResourceGroup --name myPublicIP
```

### <a name="create-a-load-balancer"></a>Créer un équilibrage de charge
Créez un équilibrage de charge avec la commande [az network lb create](/cli/azure/network/lb#create). L’exemple suivant permet de créer un équilibrage de charge nommé `myLoadBalancer` avec l’adresse `myPublicIP` :

```azurecli
az network lb create \
    --resource-group myResourceGroup \
    --name myLoadBalancer \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --public-ip-address myPublicIP
```

### <a name="create-a-health-probe"></a>Créer une sonde d’intégrité
Pour permettre à l’équilibrage de charge de surveiller l’état de votre application, vous utilisez une sonde d’intégrité. La sonde d’intégrité ajoute ou supprime dynamiquement des machines virtuelles de la rotation d’équilibrage de charge en fonction de leur réponse aux vérifications d’intégrité. Par défaut, une machine virtuelle est supprimée de la distribution d’équilibrage de charge après deux échecs consécutifs à des intervalles de 15 secondes. Vous créez une sonde d’intégrité selon un protocole ou une page de vérification d’intégrité spécifique pour votre application. L’exemple suivant crée une sonde TCP, bien que vous puissiez développer cet exemple et ajouter `--path healthcheck.js`, par exemple. Le fichier `healthcheck.js` doit être créé et retourner la réponse **HTTP 200 OK** pour l’équilibrage de charge pour assurer la rotation de l’hôte.

Créez la sonde d’intégrité avec la commande [az network lb probe create](/cli/azure/network/lb/probe#create). L’exemple suivant permet de créer une sonde d’intégrité nommée `myHealthProbe` qui surveille chaque machine virtuelle :

```azurecli
az network lb probe create \
    --resource-group myResourceGroup \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-a-load-balancer-rule"></a>Créer une règle d’équilibreur de charge
Une règle d’équilibrage de charge est utilisée pour définir la distribution du trafic vers les machines virtuelles.

Utilisez [az network lb rule create](/cli/azure/network/lb/rule#create) pour créer une règle d’équilibrage de charge. L’exemple suivant crée une règle nommée `myLoadBalancerRule`, utilise la sonde d’intégrité `myHealthProbe` et équilibre le trafic sur le port `80` :

```azurecli
az network lb rule create \
    --resource-group myResourceGroup \
    --lb-name myLoadBalancer \
    --name myLoadBalancerRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe
```


## <a name="step-4---configure-networking"></a>Étape 4 - Configuration de la mise en réseau
Chaque machine virtuelle possède une ou plusieurs cartes d’interface réseau (NIC) virtuelles qui se connectent à un réseau virtuel. Ce réseau virtuel est sécurisé pour filtrer le trafic en fonction des règles d’accès définies.

### <a name="create-a-virtual-network"></a>Créez un réseau virtuel
Pour fournir la connectivité réseau à vos machines virtuelles, créez un réseau virtuel avec [az network vnet create](/cli/azure/vnet#create). L’exemple suivant permet de créer un réseau virtuel nommé `myVnet` avec un sous-réseau nommé `mySubnet` :

```azurecli
az network vnet create --resource-group myResourceGroup --name myVnet --subnet-name mySubnet
```

### <a name="configure-network-security"></a>Configurer la sécurité réseau
Les groupes de sécurité réseau utilisent des règles pour contrôler le flux du trafic vers et à partir de machines virtuelles. Vous définissez ces règles, notamment pour autoriser le trafic sur le port 80. Même si l’équilibrage de charge répartit le trafic entre les machines virtuelles, les règles de groupe de sécurité réseau garantissent que seul le trafic autorisé est admis.

Créez un groupe de sécurité réseau avec la commande [az network nsg create](/cli/azure/network/nsg#create). L’exemple suivant permet de créer un groupe de sécurité réseau nommé `myNetworkSecurityGroup` :

```azurecli
az network nsg create --resource-group myResourceGroup --name myNetworkSecurityGroup
```

Pour permettre au trafic web d’atteindre votre application, créez une règle de groupe de sécurité réseau avec [az network nsg rule create](/cli/azure/network/nsg/rule#create). L’exemple suivant permet de créer une règle de groupe de sécurité réseau nommée `myNetworkSecurityGroupRule` :

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --priority 1001 \
    --protocol tcp \
    --destination-port-range 80
```

### <a name="create-virtual-network-interface-cards"></a>Création de cartes d’interface réseau virtuelles 
Les équilibrages de charge fonctionnent avec la ressource de carte réseau virtuelle plutôt que la machine virtuelle elle-même. La carte réseau virtuelle est connectée à l’équilibrage de charge, puis attachée à une machine virtuelle.

Créez une carte réseau virtuelle avec [az network nic create](/cli/azure/network/nic#create). L’exemple suivant crée trois cartes réseau virtuelles. (Une carte réseau virtuelle pour chaque machine virtuelle que vous créez pour votre application dans les étapes suivantes) :

```bash
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroup \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```


## <a name="step-5---build-your-app"></a>Étape 5 : Générer votre application
**cloud-init** est une approche largement utilisée pour la personnalisation d’une machine virtuelle. Vous pouvez utiliser **cloud-init** pour installer des packages et écrire des fichiers. Comme **cloud_init** s’exécute lors du déploiement initial, il n’y a aucune étape supplémentaire pour lancer votre application. L’équilibrage de charge commence à distribuer le trafic une fois que la machine virtuelle a terminé le déploiement et que l’application est en cours d’exécution. Pour plus d’informations sur l’utilisation de **cloud-init**, consultez [Utiliser cloud-init pour personnaliser une machine virtuelle Linux lors de la création](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

La configuration **cloud-init** suivante installe **nodejs** et **npm**, puis installe et configure **nginx** comme proxy web pour votre application. La configuration crée également une application « Hello World » Node.js simple, puis initialise et démarre l’application avec **Express**. Si vous souhaitez utiliser un framework d’application différent, ajustez les packages et l’application déployée en conséquence.

Créez un fichier nommé `cloud-init.txt` et collez la configuration suivante :

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - nginx -s reload
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```


## <a name="step-6---create-virtual-machines"></a>Étape 6 : Créer les machines virtuelles
Tous les composants sous-jacents étant en place, vous pouvez désormais créer des machines virtuelles hautement disponibles pour exécuter votre application.

### <a name="create-vms"></a>Créer des machines virtuelles
Créez les machines virtuelles avec [az vm create](/cli/azure/vm#create). L’exemple suivant crée trois machines virtuelles et génère des clés SSH si elles n’existent pas déjà :

```bash
for i in `seq 1 3`; do
    az vm create \
        --resource-group myResourceGroup \
        --name myVM$i \
        --availability-set myAvailabilitySet \
        --nics myNic$i \
        --image Canonical:UbuntuServer:14.04.4-LTS:latest \
        --admin-username azureuser \
        --generate-ssh-keys \
        --custom-data cloud-init.txt \
        --no-wait
done
```

La création et la configuration des trois machines virtuelles prennent quelques minutes. La sonde d’intégrité de l’équilibrage de charge détecte automatiquement lorsque l’application est en cours d’exécution sur chaque machine virtuelle. Une fois que l’application est en cours d’exécution, la règle d’équilibrage de charge commence à distribuer le trafic.

### <a name="test-your-app"></a>Test de l'application
Obtenez l’adresse IP publique de votre équilibrage de charge avec [az network public-ip show](/cli/azure/network/public-ip#show). L’exemple suivant obtient l’adresse IP de `myPublicIP` créée précédemment :

```azurecli
az network public-ip show \
    --resource-group myResourceGroup \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
```

Entrez l’adresse IP publique dans un navigateur web. L’application s’affiche, avec notamment le nom d’hôte de la machine virtuelle sur laquelle l’équilibrage de charge a distribué le trafic :

![Exécution de l’application Node.js](./media/tutorial-load-balance-nodejs/running-nodejs-app.png)

Forcez l’actualisation de votre navigateur web pour visualiser la distribution de trafic par l’équilibrage de charge sur les trois machines virtuelles exécutant votre application.


## <a name="step-7---management-tasks"></a>Étape 7 : Tâches de gestion
Vous devrez peut-être effectuer la maintenance sur la machine virtuelle exécutant votre application, avec par exemple l’installation des mises à jour du système d’exploitation. Pour faire face à une augmentation du trafic vers votre application, vous devrez peut-être ajouter des machines virtuelles supplémentaires. Cette section vous indique comment supprimer ou ajouter une machine virtuelle pour l’équilibrage de charge.

### <a name="remove-a-vm-from-the-load-balancer"></a>Supprimer une machine virtuelle de l’équilibrage de charge
Supprimez une machine virtuelle à partir du pool d’adresses principal avec [az network nic ip-config address-pool remove](/cli/azure/network/nic/ip-config/address-pool#remove). L’exemple suivant supprime la carte réseau virtuelle pour **myVM2** de `myLoadBalancer` :

```azurecli
az network nic ip-config address-pool remove \
    --resource-group myResourceGroup \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool 
```

Forcez l’actualisation de votre navigateur web pour visualiser la distribution de trafic par l’équilibrage de charge sur les deux machines virtuelles restantes exécutant votre application. Vous pouvez maintenant effectuer la maintenance sur la machine virtuelle, avec par exemple l’installation des mises à jour du système d’exploitation ou le redémarrage de la machine virtuelle.

### <a name="add-a-vm-to-the-load-balancer"></a>Ajouter une machine virtuelle à l’équilibrage de charge
Après avoir effectué les opérations de maintenance de la machine virtuelle, ou si vous avez besoin de développer la capacité, ajoutez une machine virtuelle pour le pool d’adresses principal avec [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#add). L’exemple suivant ajoute la carte réseau virtuelle pour **myVM2** à `myLoadBalancer` :

```azurecli
az network nic ip-config address-pool add \
    --resource-group myResourceGroup \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool
```


## <a name="next-steps"></a>Étapes suivantes
Ce didacticiel crée une infrastructure d’application à haute disponibilité en utilisant des ressources Azure individuelles. Vous pouvez également utiliser des groupes identiques de machines virtuelles pour augmenter ou diminuer le nombre de machines virtuelles exécutant votre application. Continuez avec le didacticiel suivant : [Créer une application à haute disponibilité sur Linux avec des groupes identiques de machines virtuelles](tutorial-convert-to-vmss.md).

Pour en savoir plus sur certaines des fonctionnalités de haute disponibilité présentées dans ce didacticiel, consultez les informations suivantes :

- [Gestion de la disponibilité des machines virtuelles Linux](../windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Vue d’ensemble d’Azure Load Balancer](../../load-balancer/load-balancer-overview.md)
- [Contrôler le flux de trafic réseau avec les groupes de sécurité réseau](../../virtual-network/virtual-networks-nsg.md)
- [Exemples de scripts CLI Azure](../windows/cli-samples.md)
