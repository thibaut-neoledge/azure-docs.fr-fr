<properties
   pageTitle="Configuration de plusieurs cartes réseau sur une machine virtuelle Linux | Microsoft Azure"
   description="Découvrez comment créer une machine virtuelle dotée de plusieurs cartes réseau à l’aide de l’interface de ligne de commande Azure ou de modèles Resource Manager."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="08/02/2016"
   ms.author="iainfou"/>

# Création d’une machine virtuelle avec plusieurs cartes d’interface réseau (NIC)
Vous pouvez créer une machine virtuelle dans Azure, à laquelle sont attachées plusieurs interfaces réseau virtuelles (NIC). Un scénario courant consisterait à avoir des sous-réseaux différents pour les connectivité frontale et principale, ou un réseau dédié à une solution de surveillance ou de sauvegarde. Cet article fournit des commandes rapides pour créer une machine virtuelle avec plusieurs cartes d’interface réseau. Pour plus d’informations, notamment sur la création de plusieurs cartes réseau dans vos propres scripts Bash, consultez la page consacrée au [déploiement de machines virtuelles avec plusieurs cartes d’interface réseau](../virtual-network/virtual-network-deploy-multinic-arm-cli.md). Comme le nombre de cartes réseau prises en charge varie suivant la [taille des machines virtuelles](virtual-machines-linux-sizes.md), pensez à dimensionner la vôtre en conséquence.

>[AZURE.WARNING] Vous devez attacher plusieurs cartes réseau quand vous créez une machine virtuelle. Vous ne pouvez pas ajouter de cartes réseau à une machine virtuelle existante. Vous pouvez [créer une machine virtuelle basée sur les disques virtuels d’origine](virtual-machines-linux-copy-vm.md) et créer plusieurs cartes réseau quand vous déployez la machine virtuelle.

## Commandes rapides
Veillez à ce que [la CLI Azure](../xplat-cli-install.md) soit connectée et utilise le mode Resource Manager (`azure config mode arm`).

Créez d’abord un groupe de ressources :

```bash
azure group create TestRG --location WestUS
```

Créez un compte de stockage qui contiendra vos machines virtuelles :

```bash
azure storage account create teststorage --resource-group TestRG \
    --location WestUS --kind Storage --sku-name PLRS
```

Créez un réseau virtuel auquel connecter vos machines virtuelles :

```bash
azure network vnet create --resource-group TestRG --location WestUS \
    --name TestVNet --address-prefixes 192.168.0.0/16 
```

Créez deux sous-réseaux de réseau virtuel : l’un pour le trafic front-end, l’autre pour le trafic back-end.

```bash
azure network vnet subnet create --resource-group TestRG --vnet-name TestVNet \
    --name FrontEnd --address-prefix 192.168.1.0/24
azure network vnet subnet create --resource-group TestRG --vnet-name TestVNet \
    --name BackEnd --address-prefix 192.168.2.0/24
```

Créez deux cartes réseau, en attachant l’une au sous-réseau front-end et l’autre au sous-réseau back-end :

```bash
azure network nic create --resource-group TestRG --location WestUS \
    -n NIC1 --subnet-vnet-name TestVNet --subnet-name FrontEnd
azure network nic create --resource-group TestRG --location WestUS \
    -n NIC2 --subnet-vnet-name TestVNet --subnet-name BackEnd
```

Enfin, créez votre machine virtuelle en attachant les deux cartes réseau que vous avez créées précédemment :

```bash
azure vm create \            
    --resource-group TestRG \
    --name TestVM1 \
    --location WestUS \
    --os-type linux \
    --nic-names NIC1,NIC2 \
    --vm-size Standard_DS2_v2
    --storage-account-name teststorage \
    --image-urn UbuntuLTS \
    --admin-username ops \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
```

## Création de plusieurs cartes réseau à l’aide de l’interface de ligne de commande Azure
Si vous avez déjà créé une machine virtuelle à l’aide de l’interface de ligne de commande Azure, les commandes rapides doivent vous être familières. Le processus est le même pour créer une carte réseau ou plusieurs cartes réseau. Vous trouverez plus d’informations sur le [déploiement de plusieurs cartes réseau à l’aide de l’interface de ligne de commande Azure](../virtual-network/virtual-network-deploy-multinic-arm-cli.md), notamment les scripts pour le processus de bouclage pour créer toutes les cartes réseau.

L’exemple suivant crée deux cartes réseau, avec une carte réseau se connectant à chaque sous-réseau :

```bash
azure network nic create --resource-group TestRG --location WestUS \
    -n NIC1 --subnet-vnet-name TestVNet --subnet-name FrontEnd
azure network nic create --resource-group TestRG --location WestUS \
    -n NIC2 --subnet-vnet-name TestVNet --subnet-name BackEnd
```

En général, vous devez également créer un [groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md) ou un [équilibreur de charge](../load-balancer/load-balancer-overview.md) pour faciliter la gestion et la répartition du trafic entre vos machines virtuelles. Là encore, les commandes sont les mêmes lorsque vous travaillez avec plusieurs cartes réseau. Les cartes réseau que vous créez sont attachées à un groupe de sécurité réseau ou à l’équilibreur de charge `azure network nic set`, comme dans l’exemple suivant :

```bash
azure network nic set --resource-group TestRG --name NIC1 \
    --network-security-group-name TestNSG
```

Lorsque vous créez la machine virtuelle, vous spécifiez maintenant plusieurs cartes réseau. Au lieu d’utiliser `--nic-name` pour fournir une seule carte réseau, vous utilisez `--nic-names` et spécifiez une liste de cartes réseau séparée par des virgules. Vous devez également faire attention en définissant la taille de la machine virtuelle. Il existe des limites pour le nombre maximal de cartes réseau que vous pouvez ajouter à une machine virtuelle. En savoir plus sur les [tailles des machines virtuelles Linux](virtual-machines-linux-sizes.md). L’exemple suivant montre comment spécifier plusieurs cartes réseau, puis sur une taille de machine virtuelle permettant l’utilisation de plusieurs cartes réseau (`Standard_DS2_v2`) :

```bash
azure vm create \            
    --resource-group TestRG \
    --name TestVM1 \
    --location WestUS \
    --os-type linux \
    --nic-names NIC1,NIC2 \
    --vm-size Standard_DS2_v2
    --storage-account-name teststorage \
    --image-urn UbuntuLTS \
    --admin-username ops \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
```

## Création de plusieurs cartes réseau à l’aide de modèles Resource Manager
Les modèles Azure Resource Manager utilisent des fichiers JSON déclaratifs pour définir votre environnement. Vous pouvez consulter une [vue d’ensemble d’Azure Resource Manager](../resource-group-overview.md). Les modèles Resource Manager fournissent un moyen de créer plusieurs instances d’une ressource pendant le déploiement, à l’image de la création de plusieurs cartes réseau. Vous utilisez *copy* pour spécifier le nombre d’instances à créer :

```bash
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

En savoir plus sur la [création de plusieurs instances à l’aide de *copy*](../resource-group-create-multiple.md).

Vous pouvez également utiliser `copyIndex()` pour ajouter ensuite un numéro à un nom de ressource afin de créer `NIC1`, `NIC2`, etc. Voici un exemple d’ajout de la valeur d’index :

```bash
"name": "[concat('NIC-', copyIndex())]", 
```

Vous pouvez consulter un exemple complet de [création de plusieurs cartes réseau à l’aide de modèles Resource Manager](../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## Étapes suivantes
Veillez à consulter les [tailles des machines virtuelles Linux](virtual-machines-linux-sizes.md) si vous créez une machine virtuelle avec plusieurs cartes réseau. Faites attention au nombre maximal de cartes réseau pris en charge par chaque taille de machine virtuelle.

N’oubliez pas que vous ne pouvez pas ajouter de carte réseau à une machine virtuelle existante. Vous devez créer toutes les cartes réseau quand vous déployez la machine virtuelle. Quand vous planifiez vos déploiements, vérifiez que vous disposez de toute la connectivité réseau nécessaire dès le départ.

<!---HONumber=AcomDC_0817_2016-->