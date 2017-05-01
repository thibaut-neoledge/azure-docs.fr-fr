---
title: "Créer une application hautement disponible dans Azure à l’aide de groupes de machines virtuelles identiques | Microsoft Docs"
description: "Créez et déployez une application hautement disponible sur les machines virtuelles Linux à l’aide d’un groupe de machines virtuelles identiques et d’Azure CLI."
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 04/05/2017
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 4c76fb202f501f671504646395b800aeb90d8e69
ms.lasthandoff: 04/07/2017

---

# <a name="create-a-highly-available-application-on-linux-with-virtual-machine-scale-sets"></a>Créer une application hautement disponible sur Linux avec des groupes de machines virtuelles identiques
Ce didacticiel vous montre comment créer une application hautement disponible dans un groupe de machines virtuelles identiques. Vous apprendrez également à automatiser la configuration des machines virtuelles dans le groupe identique. 


## <a name="step-1---create-a-resource-group"></a>Étape 1 : Création d’un groupe de ressources
Pour suivre ce didacticiel, assurez-vous que vous avez installé la dernière version [d’Azure CLI 2.0](/cli/azure/install-azure-cli). Si vous n’êtes pas encore connecté à votre abonnement Azure, connectez-vous avec la commande [az login](/cli/azure/#login) et suivez les instructions à l’écran.

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#create). L’exemple suivant crée un groupe de ressources nommé `myResourceGroupVMSS` à l’emplacement `westus` :

```azurecli
az group create --name myResourceGroupVMSS --location westus
```


## <a name="step-2---define-your-app"></a>Étape 2 : Définition de votre application
Vous utilisez la même configuration **cloud-init** à partir du didacticiel que celle utilisée pour créer une application hautement disponible à équilibrage de charge. Pour plus d’informations sur l’utilisation de **cloud-init**, consultez [Utiliser cloud-init pour personnaliser une machine virtuelle Linux lors de la création](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

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


## <a name="step-3---create-scale-set"></a>Étape 3 : Création du groupe identique
Un groupe de machines virtuelles identiques vous permet de déployer et de gérer un ensemble de machines virtuelles identiques prenant en charge la mise à l’échelle automatique. Les groupes identiques utilisent les mêmes composants que ceux que vous avez découverts dans le didacticiel permettant de [générer une application hautement disponible dans Azure](tutorial-load-balance-nodejs.md). Ces composants incluent les groupes à haute disponibilité, les domaines d’erreur et de mise à jour, et les équilibrages de charge.

Avec un groupe identique, ces ressources sont créées et gérées pour vous. Le nombre de machines virtuelles dans votre groupe identique peut augmenter ou diminuer automatiquement selon des règles définies. Vous pouvez [utiliser une image personnalisée](capture-image.md) comme source de votre machine virtuelle ou configurer les machines virtuelles pendant le déploiement avec la commande **cloud-init** comme dans ce didacticiel.

Créez un groupe de machines virtuelles identiques avec [az vmss create](/cli/azure/vmss#create). L’exemple suivant permet de créer un groupe identique nommé `myScaleSet` :

```azurecli
az vmss create \
  --resource-group myResourceGroupVMSS \
  --name myScaleSet \
  --image Canonical:UbuntuServer:14.04.4-LTS:latest \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys      
```

La création et la configuration des l’ensemble des ressources et des machines virtuelles du groupe identique prennent quelques minutes.


## <a name="step-4---configure-firewall"></a>Étape 4 : Configuration du pare-feu
Un équilibrage de charge a été créé automatiquement dans le cadre du groupe de machines virtuelles identiques. L’équilibrage de charge répartit le trafic sur un ensemble de machines virtuelles définies à l’aide de règles d’équilibrage de charge. Pour autoriser le trafic à atteindre l’application web, créez une règle avec [az network lb probe create](/cli/azure/network/lb/probe#create). L’exemple suivant permet de créer une règle nommée `myLoadBalancerRuleWeb` :

```azurecli
az network lb rule create \
  --resource-group myResourceGroupVMSS \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

## <a name="step-5---test-your-app"></a>Étape 5 : Test de l’application
Obtenez l’adresse IP publique de votre équilibrage de charge avec [az network public-ip show](/cli/azure/network/public-ip#show). L’exemple suivant obtient l’adresse IP de `myScaleSetLBPublicIP` créée dans le cadre du groupe identique :

```azurecli
az network public-ip show \
    --resource-group myResourceGroupVMSS \
    --name myScaleSetLBPublicIP \
    --query [ipAddress] \
    --output tsv
```

Entrez l’adresse IP publique dans un navigateur web. L’application s’affiche, avec notamment le nom d’hôte de la machine virtuelle sur laquelle l’équilibrage de charge a distribué le trafic :

![Exécution de l’application Node.js](./media/tutorial-load-balance-nodejs/running-nodejs-app.png)

Forcez l’actualisation de votre navigateur web pour visualiser la distribution de trafic par l’équilibrage de charge sur les machines virtuelles du groupe identique exécutant votre application.


## <a name="step-6---management-tasks"></a>Étape 6 : Tâches de gestion
Tout au long du cycle de vie du groupe identique, vous devrez peut-être exécuter une ou plusieurs tâches de gestion. En outre, vous souhaiterez peut-être créer des scripts pour automatiser les diverses tâches liées au cycle de vie. Azure CLI 2.0 offre un moyen rapide pour effectuer ces tâches. Voici quelques tâches courantes.

### <a name="increase-or-decrease-vm-instances"></a>Augmenter ou diminuer les instances de machines virtuelles
Vous pouvez augmenter ou diminuer manuellement le nombre de machines virtuelles dans le groupe identique avec [az vmss scale](/cli/azure/vmss#scale). L’exemple suivant augmente à `5` le nombre de machines virtuelles dans votre groupe identique :

```azurecli
az vmss scale --resource-group myResourceGroupVMSS --name myScaleSet --new-capacity 5
```

Les règles de mise à l’échelle automatique vous permettent de définir comment augmenter ou réduire le nombre de machines virtuelles dans votre groupe identique en réponse à la demande, notamment le trafic réseau ou l’utilisation du processeur. Actuellement, ces règles ne peuvent pas être définies dans Azure CLI 2.0. Utilisez le [portail Azure](https://portal.azure.com) pour configurer la mise à l’échelle automatique.

### <a name="get-connection-info"></a>Obtenir des informations de connexion
Pour obtenir des informations de connexion sur les machines virtuelles dans vos groupes identiques, utilisez [az vmss list-instance-connection-info](/cli/azure/vmss#list-instance-connection-info). Cette commande renvoie l’adresse IP publique et les ports pour chaque machine virtuelle pour vous permettre de vous connecter avec SSH :

```azurecli
az vmss list-instance-connection-info --resource-group myResourceGroupVMSS --name myScaleSet
```

### <a name="delete-resource-group"></a>Supprimer un groupe de ressources
La suppression d’un groupe de ressources supprime également toutes les ressources qu’il contient.

```azurecli
az group delete --name myResourceGroupVMSS
```


## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, nous avons défini l’application web avec **cloud-init** et configuré chaque machine virtuelle pendant le déploiement. Pour plus d’informations sur la capture d’une machine virtuelle à utiliser comme image source dans votre groupe identique, consultez [Généraliser et capturer une machine virtuelle Linux](capture-image.md).

Pour en savoir plus sur certaines des fonctionnalités du groupe de machines virtuelles identiques présentées dans ce didacticiel, consultez les informations suivantes :

- [Présentation des groupes de machines virtuelles identiques Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)
- [Vue d’ensemble d’Azure Load Balancer](../../load-balancer/load-balancer-overview.md)
- [Contrôler le flux de trafic réseau avec les groupes de sécurité réseau](../../virtual-network/virtual-networks-nsg.md)
