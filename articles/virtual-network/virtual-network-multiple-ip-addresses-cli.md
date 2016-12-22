---
title: "Plusieurs adresses IP pour les machines virtuelles - Azure CLI | Microsoft Docs"
description: "Apprenez à affecter plusieurs adresses IP à une machine virtuelle avec Azure CLI | Resource Manager."
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: annahar
translationtype: Human Translation
ms.sourcegitcommit: bf92cdc3f6adf1bfaffbcae4e8a0257d0682e33d
ms.openlocfilehash: 0bb3e5f74cb6d013a2b14b4e4c81f0936f90004a


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-azure-cli"></a>Affecter plusieurs adresses IP aux machines virtuelles avec Azure CLI

> [!div class="op_single_selector"]
> * [Portail Azure](virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
> * [INTERFACE DE LIGNE DE COMMANDE](virtual-network-multiple-ip-addresses-cli.md)

Une ou plusieurs interfaces réseau sont attachées à une machine virtuelle Azure. Une ou plusieurs adresses IP publiques ou privées statiques ou dynamiques peuvent être affectées à chaque carte réseau. L’affectation de plusieurs adresses IP à une machine virtuelle permet :

* D’héberger plusieurs sites web ou services avec des adresses IP différentes et des certificats SSL sur un serveur unique.
* de jouer le rôle d’une appliance virtuelle réseau, telle qu’un pare-feu ou un équilibreur de charge.
* La possibilité d’ajouter l’une des adresses IP privées pour toutes les cartes réseau à un pool principal Azure Load Balancer. Auparavant, seule l’adresse IP principale de la carte réseau principale pouvait être ajoutée à un pool principal. Pour en savoir plus sur la façon d’équilibrer plusieurs configurations IP, lisez l’article [Équilibrage de la charge de plusieurs configurations IP](../load-balancer/load-balancer-multiple-ip.md).

Chaque carte réseau connectée à une machine virtuelle a une ou plusieurs configurations IP associées. Une adresse IP privée statique ou dynamique est affectée à chaque configuration. Une seule ressource d’adresse IP publique peut également être associée à chaque configuration. Une adresse IP statique ou dynamique peut être affectée à une ressource d’adresse IP publique. Si vous n’êtes pas familiarisé avec les adresses IP dans Azure, lisez l’article [Adresses IP dans Azure](virtual-network-ip-addresses-overview-arm.md) pour en savoir plus.

Cet article explique comment utiliser PowerShell pour affecter plusieurs adresses IP à une machine virtuelle créée dans le modèle de déploiement Azure Resource Manager. Il n’est pas possible d’affecter plusieurs adresses IP à des ressources créées dans le modèle de déploiement classique. Pour en savoir plus sur les modèles de déploiement Azure, lisez l’article [Comprendre les modèles de déploiement](../resource-manager-deployment-model.md).

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

## <a name="scenario"></a>Scénario
Une machine virtuelle avec une seule carte réseau est créée et connectée à un réseau virtuel. La machine virtuelle nécessite trois différentes adresses IP *privées* et deux adresses IP *publiques*. Les adresses IP sont affectées aux configurations IP suivantes :

* **IPConfig-1 :** attribue une adresse IP privée *dynamique* (par défaut) et une adresse IP publique *statique*.
* **IPConfig-2 :** attribue une adresse IP privée *statique* et une adresse IP publique *statique*.
* **IPConfig-3 :** attribue une adresse IP privée *dynamique* et aucune adresse IP publique.
  
    ![Plusieurs adresses IP](./media/virtual-network-multiple-ip-addresses-powershell/OneNIC-3IP.png)

Les configurations IP sont associées à la carte réseau lors de sa création et la carte réseau est attachée à la machine virtuelle lors de la création de la machine virtuelle. Les types d’adresses IP utilisées pour le scénario sont indiqués à titre d’illustration. Vous pouvez affecter les types d’adresses IP et d’attribution que vous souhaitez.

## <a name="a-name--createacreate-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Créer une machine virtuelle avec plusieurs adresses IP

Les étapes qui suivent expliquent comment créer une machine virtuelle d’exemple avec plusieurs adresses IP, comme décrit dans le scénario. Modifiez les noms des variables et les types d’adresses IP en fonction des besoins de votre implémentation.

1. Installez et configurez Azure CLI en suivant les étapes décrites dans l’article [Installer et configurer l’interface de ligne de commande Azure](../xplat-cli-install.md) et connectez-vous à votre compte Azure.

2. Obtenez la version préliminaire en envoyant un e-mail à [Plusieurs adresses IP](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) en indiquant votre ID d’abonnement et l’utilisation prévue. N’essayez pas d’effectuer les étapes restantes :
    - Tant que vous n’avez pas reçu d’e-mail vous informant que vous avez été accepté dans la version préliminaire
    - Sans suivre les instructions dans l’e-mail que vous recevez
3. [Créez un groupe de ressources](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-resource-groups-and-choose-deployment-locations), ainsi qu’un [réseau virtuel et un sous-réseau](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-network-and-subnet). Modifiez les valeurs des champs ``` --address-prefixes ``` et ```--address-prefix``` sur les valeurs suivantes pour qu’elles correspondent au scénario présenté cet article :

    ```azurecli
    --address-prefixes 10.0.0.0/16
    --address-prefix 10.0.0.0/24
    ```
    >[!NOTE] 
    >L’article référencé ci-dessus utilise Europe de l’Ouest comme emplacement pour créer des ressources, mais cet article utilise Ouest des États-Unis. Modifiez l’emplacement en conséquence.

4. [Créez un compte de stockage](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-storage-account) pour votre machine virtuelle.


5. Créez la carte réseau et les configurations IP à affecter à la carte réseau. Vous pouvez ajouter, supprimer ou modifier les configurations selon les besoins. Les configurations suivantes sont décrites dans le scénario :

    **IPConfig-1**

    Entrez les commandes suivantes pour créer :

    - Une ressource d’adresse IP publique avec une adresse IP publique statique
    - Une configuration IP avec la ressource d’adresse IP publique et une adresse IP privée dynamique

    ```azurecli
    azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP --domain-name-label mypublicdns --allocation-method Static
    ```
    > [!NOTE]
    > Les adresses IP publiques ont un coût nominal. Pour en savoir plus, lisez la page [Tarification des adresses IP](https://azure.microsoft.com/pricing/details/ip-addresses) . Il existe une limite au nombre d’adresses IP publiques qui peuvent être utilisées dans un abonnement. Pour plus d’informations sur les limites, consultez l’article [Limites d’Azure](../azure-subscription-service-limits.md#networking-limits) .

    ```azurecli
    azure network nic create --resource-group myResourceGroup --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic1 --public-ip-name myPublicIP
    ```

    **IPConfig-2**

     Entrez les commandes suivantes pour créer une nouvelle ressource d’adresse IP publique et une nouvelle configuration IP avec une adresse IP publique statique et une adresse IP privée statique :
    
    ```azurecli
    azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP2 --domain-name-label mypublicdns2 --allocation-method Static

    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-2 --private-ip-address 10.0.0.5 --public-ip-name myPublicIP2
    ```

    **IPConfig-3**

    Entrez les commandes suivantes pour créer une configuration IP avec une adresse IP privée dynamique et aucune adresse IP publique :

    ```azurecli
    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-3
    ```

    >[!NOTE] 
    >Bien que cet article affecte toutes les configurations IP à une seule carte réseau, vous pouvez également affecter plusieurs configurations IP à une carte réseau dans une machine virtuelle. Pour apprendre à créer une machine virtuelle avec plusieurs cartes d’interface réseau, lisez l’article Créer une machine virtuelle avec plusieurs cartes d’interface réseau.

6. Article [Créer une machine virtuelle Linux](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-the-linux-vms). Veillez à supprimer la propriété ```  --availset-name myAvailabilitySet \ ``` dans la mesure où elle n’est pas requise pour ce scénario. Utilisez l’emplacement approprié en fonction de votre scénario. 

    >[!WARNING] 
    > L’étape 6 dans l’article Créer une machine virtuelle échoue si la taille de la machine virtuelle n’est pas prise en charge dans l’emplacement sélectionné. Exécutez la commande suivante pour obtenir une liste complète des ordinateurs virtuels dans la région Centre-Ouest des États-Unis, par exemple. Ce nom d’emplacement peut être modifié en fonction de votre scénario.
    > 
    >       azure vm sizes --location westcentralus

    Pour modifier la taille de la machine virtuelle sur Standard DS2 v2, par exemple, ajoutez simplement la propriété suivante ```  --vm-size Standard_DS3_v2``` à la commande ``` azure vm create ``` à l’étape 6.

7. Entrez la commande suivante pour afficher la carte d’interface réseau et les configurations IP associées :

    ```azurecli
    azure network nic show --resource-group myResourceGroup --name myNic1
    ```

## <a name="a-nameosconfigaadd-ip-addresses-to-a-vm-operating-system"></a><a name="OsConfig"></a>Ajouter des adresses IP à un système d’exploitation de machine virtuelle

Connectez-vous à une machine virtuelle que vous avez créée à l’aide de plusieurs adresses IP privées. Vous devez ajouter manuellement toutes les adresses IP privées (y compris l’adresse principale) que vous avez ajoutées à la machine virtuelle. Effectuez les étapes suivantes dans le système d’exploitation de votre machine virtuelle :

### <a name="windows"></a> Windows

1. Tapez *ipconfig /all*à l’invite de commandes.  Seule l’adresse IP privée *principale* est visible (via DHCP).
2. Saisissez *ncpa.cpl* dans l’invite de commandes pour ouvrir la fenêtre **Connexions réseau**.
3. Ouvrez les propriétés de **Connexion au réseau local**.
4. Double-cliquez sur Internet Protocol version 4 (IPv4).
5. Cliquez sur **Utiliser l’adresse IP suivante** et entrez les valeurs suivantes :

    * **Adresse IP**: entrez l’adresse IP privée *principale* .
    * **Masque de sous-réseau**: définissez cette option en fonction de votre sous-réseau. Par exemple, si le sous-réseau est un sous-réseau /24, le masque de sous-réseau est 255.255.255.0.
    * **Passerelle par défaut**: première adresse IP du sous-réseau. Si votre sous-réseau est 10.0.0.0/24, l’adresse IP de la passerelle est 10.0.0.1.
    * Cliquez sur **Utiliser l’adresse de serveur DNS suivante** et saisissez les valeurs ci-dessous :
        * **Serveur DNS préféré** : saisissez 168.63.129.16 si vous n’utilisez pas votre propre serveur DNS.  Si vous utilisez votre propre serveur DNS, entrez l’adresse IP de votre serveur.
    * Cliquez sur le bouton **Avancé** et ajoutez des adresses IP supplémentaires. Ajoutez chacune des adresses IP privées secondaires de l’étape 8 à l’interface réseau avec le même sous-réseau que celui de l’adresse IP principale.
    * Cliquez sur **OK** pour fermer les paramètres TCP/IP, puis sur **OK** à nouveau pour fermer les paramètres de la carte réseau. Votre connexion RDP est rétablie.
6. Tapez *ipconfig /all*à l’invite de commandes. Toutes les adresses IP que vous avez ajoutées sont affichées et le protocole DHCP est désactivé.
    
### <a name="linux-ubuntu"></a>Linux (Ubuntu)

1. Ouvrez une fenêtre de terminal.
2. Assurez-vous d’être l’utilisateur root. Si ce n’est pas le cas, saisissez la commande suivante :

    ```bash
    sudo -i
    ```

3. Mettez à jour le fichier de configuration de l’interface réseau (en supposant que « eth0 » est utilisé).

    * Conservez l’élément de ligne existant pour dhcp. L’adresse IP principale reste configurée telle qu’elle était précédemment.
    * Ajoutez une configuration pour une adresse IP statique supplémentaire à l’aide des commandes suivantes :

        ```bash
        cd /etc/network/interfaces.d/
        ls
        ```

    Un fichier .cfg doit s’afficher.
4. Ouvrez le fichier : vi *nom_de_fichier*.

    Les lignes suivantes doivent figurer à la fin du fichier :

    ```bash
    auto eth0
    iface eth0 inet dhcp
    ```

5. Ajoutez les lignes suivantes après les lignes qui existent dans ce fichier :

    ```bash
    iface eth0 inet static
    address <your private IP address here>
    ```

6. Enregistrez le fichier à l’aide de la commande suivante :

    ```bash
    :wq
    ```

7. Réinitialisez l’interface réseau à l’aide de la commande suivante :

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

    > [!IMPORTANT]
    > Exécutez les scripts ifup et ifdown sur la même ligne si vous utilisez une connexion à distance.
    >

8. Vérifiez que l’adresse IP est ajoutée à l’interface réseau à l’aide de la commande suivante :

    ```bash
    Ip addr list eth0
    ```

    Vous devez voir l’adresse IP que vous avez ajoutée à la liste.
    
### <a name="linux-redhat-centos-and-others"></a>Linux (Redhat, CentOS, etc.)

1. Ouvrez une fenêtre de terminal.
2. Assurez-vous d’être l’utilisateur root. Si ce n’est pas le cas, saisissez la commande suivante :

    ```bash
    sudo -i
    ```

3. Entrez votre mot de passe et suivez les instructions qui s’affichent. Une fois que vous êtes l’utilisateur root, accédez au dossier Scripts réseau avec la commande suivante :

    ```bash
    cd /etc/sysconfig/network-scripts
    ```

4. Répertoriez les fichiers ifcfg connexes à l’aide de la commande suivante :

    ```bash
    ls ifcfg-*
    ```

    Vous devez voir *ifcfg-eth0* dans la liste de fichiers.

5. Copiez le fichier *ifcfg-eth0* et nommez-le *ifcfg-eth0:0* à l’aide de la commande suivante :

    ```bash
    cp ifcfg-eth0 ifcfg-eth0:0
    ```

6. Modifiez le fichier *ifcfg-eth0:0* à l’aide de la commande suivante :

    ```bash
    vi ifcfg-eth1
    ```

7. Donnez à l’appareil le nom approprié figurant dans le fichier, en l’occurrence *eth0:0*, avec la commande suivante :

    ```bash
    DEVICE=eth0:0
    ```

8. Modifiez la ligne *IPADDR = YourPrivateIPAddress* pour y indiquer l’adresse IP.
9. Enregistrez le fichier à l’aide de la commande suivante :

    ```bash
    :wq
    ```

10. Redémarrez les services réseau et vérifiez que les modifications ont été appliquées en exécutant les commandes suivantes :

    ```bash
    /etc/init.d/network restart
    Ipconfig
    ```

    Vous devez voir l’adresse IP que vous avez ajoutée à la liste, dans le cas présent *eth0:0*.

## <a name="a-nameaddaadd-ip-addresses-to-a-vm"></a><a name="add"></a>Ajouter des adresses IP à une machine virtuelle

Vous pouvez ajouter des adresses IP privées et publiques supplémentaires à une carte d’interface réseau en effectuant les étapes qui suivent. Les exemples reposent sur le [scénario](#Scenario) décrit dans cet article.

1. Ouvrez l’interface de ligne de commande Azure et effectuez les étapes restantes de cette section en une seule session CLI. Si l’interface de ligne de commande Azure n’est pas encore installée et configurée, suivez les étapes décrites dans l’article [Installer et configurer l’interface de ligne de commande Azure](../xplat-cli-install.md) et connectez-vous à votre compte Azure.

2. Obtenez la version préliminaire en envoyant un e-mail à [Plusieurs adresses IP](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) en indiquant votre ID d’abonnement et l’utilisation prévue. N’essayez pas d’effectuer les étapes restantes :
    - Tant que vous n’avez pas reçu d’e-mail vous informant que vous avez été accepté dans la version préliminaire
    - Sans suivre les instructions dans l’e-mail que vous recevez


3. Suivez les étapes dans l’une des sections suivantes, selon vos besoins :

    **Ajouter une adresse IP privée**
    
    Pour ajouter une adresse IP privée à une carte d’interface réseau, vous devez créer une configuration IP à l’aide de la commande ci-dessous.  Si vous souhaitez ajouter une adresse IP privée dynamique, supprimez ```-PrivateIpAddress 10.0.0.7``` avant la saisie de la commande. Lorsque vous spécifiez une adresse IP statique, il doit s’agir d’une adresse non utilisée pour le sous-réseau.

    ```azurecli
    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 --private-ip-address 10.0.0.7 --name IPConfig-4
    ```
    Créez autant de configurations que nécessaire, à l’aide de noms de configuration unique et d’adresses IP privées (pour les configurations avec des adresses IP statiques).

    **Ajouter une adresse IP publique**
    
    Une adresse IP publique est ajoutée en l’associant à une nouvelle configuration IP ou une configuration IP existante. Suivez les étapes dans les sections ci-après, selon les besoins.

    > [!NOTE]
    > Les adresses IP publiques ont un coût nominal. Pour en savoir plus, lisez la page [Tarification des adresses IP](https://azure.microsoft.com/pricing/details/ip-addresses) . Il existe une limite au nombre d’adresses IP publiques qui peuvent être utilisées dans un abonnement. Pour plus d’informations sur les limites, consultez l’article [Limites d’Azure](../azure-subscription-service-limits.md#networking-limits) .
    >

    **Associer la ressource à une nouvelle configuration IP**
    
    Lorsque vous ajoutez une adresse IP publique dans une nouvelle configuration IP, vous devez également ajouter une adresse IP privée, car toutes les configurations IP doivent avoir une adresse IP privée. Vous pouvez ajouter une ressource d’adresse IP publique existante ou en créer une nouvelle. Pour en créer une nouvelle, saisissez la commande suivante :
    
    ```azurecli
    azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP3 --domain-name-label mypublicdns3
    ```

    Pour créer une nouvelle configuration IP avec une adresse IP privée dynamique et la ressource d’adresse IP publique *myPublicIP3*, saisissez la commande suivante :

    ```azurecli
    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic --name IPConfig-4 --public-ip-name myPublicIP3
    ```

    **Associer la ressource à une configuration IP existante**
    Une ressource d’adresse IP publique peut uniquement être associée à une configuration IP n’ayant pas encore de ressource associée. Vous pouvez déterminer si une configuration IP a une adresse IP publique associée en saisissant la commande suivante :

    ```azurecli
    azure network nic ip-config list --resource-group myResourceGroup --nic-name myNic1
    ```

    Recherchez une ligne similaire à celle qui suit dans la sortie retournée :
    
        Name               Provisioning state  Primary  Private IP allocation  Private IP version  Private IP address  Subnet    Public IP
        -----------------  ------------------  -------  ---------------------  ------------------  ------------------  --------  -----------
        default-ip-config  Succeeded           true     Dynamic                IPv4                10.0.0.4            mySubnet  myPublicIP
        IPConfig-2         Succeeded           false    Static                 IPv4                10.0.0.5            mySubnet  myPublicIP2
        IPConfig-3         Succeeded           false    Dynamic                IPv4                10.0.0.6            mySubnet
     
    Étant donné que l’**adresse IP publique** pour *IpConfig-3* est vide, aucune ressource d’adresse IP publique n’y est actuellement associée. Vous pouvez ajouter une ressource d’adresse IP publique existante sur IpConfig-3, ou entrer la commande suivante pour en créer une :

    ```azurecli
    azure network public-ip create --resource-group  myResourceGroup --location westcentralus --name myPublicIP3 --domain-name-label mypublicdns3 --allocation-method Static
    ```
    
    Entrez la commande suivante pour associer la ressource d’adresse IP publique à la configuration IP existante nommée *IPConfig-3* :
    
    ```azurecli
    azure network nic ip-config set --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-3 --public-ip-name myPublicIP3
    ```


7. Affichez les ressources d’adresses IP privées et l’adresse IP publique affectées à la carte d’interface réseau en saisissant la commande suivante :

    ```azurecli
    azure network nic ip-config list --resource-group myResourceGroup --nic-name myNic1
    ```
    Le résultat ressemble à ce qui suit : 
    
        Name               Provisioning state  Primary  Private IP allocation  Private IP version  Private IP address  Subnet    Public IP
        -----------------  ------------------  -------  ---------------------  ------------------  ------------------  --------  -----------
        default-ip-config  Succeeded           true     Dynamic                IPv4                10.0.0.4            mySubnet  myPublicIP
        IPConfig-2         Succeeded           false    Static                 IPv4                10.0.0.5            mySubnet  myPublicIP2
        IPConfig-3         Succeeded           false    Dynamic                IPv4                10.0.0.6            mySubnet  myPublicIP3
     
9. Ajoutez les adresses IP que vous avez ajoutées à l’interface réseau pour le système d’exploitation de la machine virtuelle en suivant les instructions dans la section [Ajouter des adresses IP dans un système d’exploitation de machine virtuelle](#OsConfig) de cet article.


<!--HONumber=Nov16_HO3-->


