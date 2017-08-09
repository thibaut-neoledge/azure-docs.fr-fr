---
title: "Créer une machine virtuelle Azure avec mise en réseau accélérée| Documents Microsoft"
description: "Apprenez à créer une machine virtuelle avec mise en réseau accélérée."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/10/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 8021f8641ff3f009104082093143ec8eb087279e
ms.openlocfilehash: c852a1297261504015a3a985fe14a38957d1a64a
ms.contentlocale: fr-fr
ms.lasthandoff: 07/21/2017

---
# <a name="create-a-virtual-machine-with-accelerated-networking"></a>Créer une machine virtuelle avec mise en réseau accélérée

Ce didacticiel explique comment créer une machine virtuelle Azure (VM) avec mise en réseau accélérée. La mise en réseau accélérée a été mise à disposition générale pour Windows et est proposée en préversion publique pour certaines distributions Linux. Une mise en réseau accélérée permet d’opérer une virtualisation d’E/S d’une racine unique (SR-IOV) sur une machine virtuelle, ce qui améliore considérablement les performances de mise en réseau. Cette voie hautement performante court-circuite l’hôte du chemin d’accès aux données, réduisant ainsi la latence, l’instabilité et l’utilisation du processeur pour servir les charges de travail réseau les plus exigeantes sur les types de machines virtuelles pris en charge. L’illustration suivante montre la communication entre deux machines virtuelles avec ou sans mise en réseau accélérée :

![Opérateurs de comparaison](./media/virtual-network-create-vm-accelerated-networking/image1.png)

Sans mise en réseau accélérée, tout le trafic réseau en direction et en provenance de la machine virtuelle doit transiter par l’hôte et le commutateur virtuel. Le commutateur virtuel fournit au trafic réseau toutes les stratégies, telles que les groupes de sécurité réseau, les listes de contrôle d’accès, l’isolation et d’autres services de réseau virtualisé. Pour plus d’informations sur les commutateurs virtuels, voir [Vue d’ensemble de la virtualisation de réseau Hyper-V](https://technet.microsoft.com/library/jj945275.aspx).

Dans le cas d’une mise en réseau accélérée, le trafic réseau parvient à la carte réseau de la machine virtuelle avant d’être transféré vers celle-ci. Toutes les stratégies réseau que le commutateur virtuel applique sans mise en réseau accélérée sont déchargées et appliquées dans le matériel. L’application de la stratégie au niveau du matériel permet à la carte réseau de transférer le trafic directement à la machine virtuelle, en ignorant l’hôte et le commutateur virtuel, tout en conservant toutes les stratégies qu’il a appliquées dans l’hôte.

Les avantages d’une mise en réseau accélérée s’appliquent uniquement à la machine virtuelle activée. Pour des résultats optimaux, il convient d’activer cette fonctionnalité sur au moins deux machines virtuelles connectées au même réseau virtuel Azure Virtual Network. Lors de la communication entre réseaux virtuels ou d’une connexion locale, cette fonctionnalité a une incidence minimale sur la latence globale.

> [!WARNING]
> La préversion publique de Linux n’offre peut-être pas les mêmes niveaux de disponibilité et de fiabilité que les fonctions de la version mise à la disposition générale. Cette fonctionnalité n’est pas prise en charge, est susceptible de disposer de possibilités limitées et peut ne pas être disponible à certains emplacements Azure. Pour les notifications les plus récentes sur la disponibilité et l’état de cette fonctionnalité, consultez la page relative aux mises à jour du réseau virtuel Azure.

## <a name="benefits"></a>Avantages
* **Latence plus faible/Nombre supérieur de paquets par seconde (pps) :** la suppression du commutateur virtuel du chemin de données évite que les paquets liés au traitement des stratégies séjournent sur l’hôte, ce qui augmente le nombre de paquets pouvant être traités dans la machine virtuelle.
* **Instabilité réduite :** le traitement du commutateur virtuel dépend de la quantité de stratégie qui doit être appliquée et de la charge de travail du processeur qui effectue le traitement. Le déchargement des stratégies sur le matériel supprime cette variabilité en fournissant des paquets directement à la machine virtuelle, en supprimant l’hôte dans la communication de la machine virtuelle et toutes les interruptions de logiciel et les changements de contexte.
* **Utilisation réduite du processeur :** ignorer le commutateur virtuel dans l’hôte réduit l’utilisation du processeur pour le traitement du trafic réseau.

## <a name="Limitations"></a>Limitations
Les limitations suivantes existent lors de l’utilisation de cette fonctionnalité :

* **Création d’interface réseau :** la mise en réseau accélérée ne peut être activée que pour une nouvelle carte réseau. Elle ne peut pas être activée pour une carte réseau existante.
* **Création de machine virtuelle :** une carte réseau avec mise en réseau accélérée activée ne peut être attachée à une machine virtuelle que lors de la création de celle-ci. Une carte réseau ne peut pas être attachée à une machine virtuelle existante.
* **Régions :** des machines virtuelles Windows avec mise en réseau accélérée sont proposées dans la plupart des régions Azure. Des machines virtuelles Linux avec mise en réseau accélérée sont proposées dans plusieurs régions. La liste des régions où cette fonctionnalité est disponible ne cesse de s’allonger. Consultez le blog consacré aux mises à jour du réseau virtuel Azure ci-dessous pour plus d’informations.   
* **Systèmes d’exploitation pris en charge :** Windows : Microsoft Windows Server 2012 R2 Datacenter et Windows Server 2016. Linux : Ubuntu Server 16.04 LTS avec noyau 4.4.0-77 ou supérieur, SLES 12 SP2, RHEL 7.3 et CentOS 7.3 (publié par « Rogue Wave Software »).
* **Taille de machine virtuelle :** tailles d’instance à usage général et de calcul optimisé avec au moins huit cœurs. Pour plus d’informations, voir les articles sur les tailles de machine virtuelle [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) et [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Le nombre de tailles d’instance de machine virtuelle augmentera dans le futur.
* **Déploiement via Azure Resource Manager (ARM) uniquement :** la mise en réseau accélérée n’est pas disponible pour le déploiement via ASM/RDFE.

Les modifications de ces limites seront annoncées via la page [Mises à jour concernant la mise en réseau virtuel Azure](https://azure.microsoft.com/updates/accelerated-networking-in-preview).

## <a name="create-a-windows-vm"></a>Créer une machine virtuelle Windows
Pour créer la machine virtuelle, vous pouvez utiliser le Portail Azure ou Azure [PowerShell](#windows-powershell).

### <a name="windows-portal"></a>Portail

1. Dans un navigateur Internet, ouvrez le [portail Azure](https://portal.azure.com) et connectez-vous avec votre [compte](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) Azure. Si vous n’avez pas de compte, vous pouvez vous inscrire pour bénéficier d’un [essai gratuit](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Dans le portail, cliquez sur **+ Nouveau** > **Calcul** > **Windows Server Datacenter 2016**. 
3. Dans le panneau **Windows Server Datacenter 2016** qui s’affiche, laissez l’option *Resource Manager* activée sous **Sélectionnez un modèle de déploiement** et cliquez sur **Créer**.
4. Dans le panneau **De base** qui s’affiche, entrez les valeurs suivantes, conservez les autres options par défaut ou sélectionnez ou entrez vos propres valeurs, puis cliquez sur le bouton **OK**:

    |Paramètre|Valeur|
    |---|---|
    |Nom|MyVm|
    |Groupe de ressources|Laissez l’option **Créer un nouveau** activée et entrez *MyResourceGroup*|
    |Lieu|Ouest des États-Unis 2|

    Si vous débutez avec Azure, apprenez-en davantage sur les [groupes de ressources](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), les [abonnements](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) et les [emplacements](https://azure.microsoft.com/regions) (également appelés régions).
5. Dans le panneau **Choisir une taille** qui s’affiche, dans la zonr **Nombre minimum de cœurs**, entrez *8*, puis cliquez sur **Afficher tout**.
6. Cliquez sur **DS4_V2 Standard** ou sur toute autre machine virtuelle prise en charge, puis sur le bouton **Sélectionner**.
7. Dans le panneau **Paramètres** qui s’affiche, ne modifiez aucun paramètres, sauf l’option **Activée** sous **Mise en réseau accélérée**, puis cliquez sur le bouton **OK**. **Remarque :** si, dans les étapes précédentes, vous avez sélectionné des valeurs pour la taille de machine virtuelle, le système d’exploitation ou l’emplacement qui ne figurent pas dans la section [Limitations](#Limitations) de cet article, la boîte de dialogue **Mise en réseau accélérée** n’apparaît pas.
8. Dans le panneau **Résumé** qui s’affiche, cliquez sur le bouton **OK**. Azure commence à créer la machine virtuelle. La création de la machine virtuelle prend quelques minutes.
9. Pour installer le pilote de mise en réseau accélérée pour Windows, procédez de la manière décrite dans la section [Configurer Windows](#configure-windows) de cet article.

### <a name="windows-powershell"></a>PowerShell
1. Installez la dernière version du module Azure PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/). Si vous débutez dans l’utilisation d’Azure PowerShell, voir [Vue d’ensemble d’Azure PowerShell](/powershell/azure/get-started-azureps?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Démarrez une session PowerShell en cliquant sur le bouton Démarrer de Windows, en tapant **powershell**, puis en cliquant sur **PowerShell** dans les résultats de la recherche.
3. Dans la fenêtre PowerShell, entrez la commande `login-azurermaccount` pour vous connecter avec votre [compte](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) Azure. Si vous n’avez pas de compte, vous pouvez vous inscrire pour bénéficier d’un [essai gratuit](https://azure.microsoft.com/offers/ms-azr-0044p).
4. Dans votre navigateur, copiez le script suivant :
    ```powershell
    $RgName="MyResourceGroup"
    $Location="westus2"
    
    # Create a resource group
    New-AzureRmResourceGroup `
      -Name $RgName `
      -Location $Location
    
    # Create a subnet
    $Subnet = New-AzureRmVirtualNetworkSubnetConfig `
      -Name MySubnet `
      -AddressPrefix 10.0.0.0/24
    
    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName $RgName `
      -Location $Location `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet

    # Create a public IP address
    $Pip = New-AzureRmPublicIpAddress `
      -Name MyPublicIp `
      -ResourceGroupName $RgName `
      -Location $Location `
      -AllocationMethod Static

    # Create a virtual network interface and associate the public IP address to it
    $Nic = New-AzureRmNetworkInterface `
      -Name MyNic `
      -ResourceGroupName $RgName `
      -Location $Location `
      -SubnetId $Vnet.Subnets[0].Id `
      -PublicIpAddressId $Pip.Id `
      -EnableAcceleratedNetworking
     
    # Define a credential object for the VM. PowerShell prompts you for a username and password.
    $Cred = Get-Credential

    # Create a virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
      -VMName MyVM -VMSize Standard_DS4_v2 | `
      Set-AzureRmVMOperatingSystem `
      -Windows `
      -ComputerName myVM `
      -Credential $Cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface -Id $Nic.Id 

    # Create the virtual machine.    
    New-AzureRmVM `
      -ResourceGroupName $RgName `
      -Location $Location `
      -VM $VmConfig
    #
    ```
5. Dans la fenêtre PowerShell, cliquez avec le bouton droit pour coller le script et commencer à l’exécuter. Vous êtes invité à entrer un nom d’utilisateur et un mot de passe. Utilisez ces informations d’identification pour vous ouvrir une session sur la machine virtuelle lors de la connexion à celle-ci à l’étape suivante. Si le script échoue et que vous avez modifié des valeurs dans celui-ci avant de l’exécuter, vérifiez que les valeurs que vous avez utilisées pour la taille de machine virtuelle, le système d’exploitation et l’emplacement figurent dans la section [Limitations](#Limitations) de cet article.
6. Pour installer le pilote de mise en réseau accélérée pour Windows, procédez de la manière décrite dans la section [Configurer Windows](#configure-windows) de cet article.

### <a name="configure-windows"></a>Configurer Windows
Après avoir créé la machine virtuelle dans Azure, vous devez installer le pilote de mise en réseau accélérée pour Windows. Avant de passer aux étapes suivantes, vous devez avoir créé une machine virtuelle Windows avec une mise en réseau accélérée en suivant l’une des étapes [portail](#windows-portal) ou [PowerShell](#windows-powershell) décrites dans cet article. 

1. Dans un navigateur Internet, ouvrez le [portail](https://portal.azure.com) Azure et connectez-vous avec votre [compte](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) Azure. Si vous n’avez pas de compte, vous pouvez vous inscrire pour bénéficier d’un [essai gratuit](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Dans la boîte de dialogue contenant le texte *Rechercher des ressources* en haut du portail Azure, tapez *MyVm*. Lorsque **MyVm** apparaît dans les résultats de recherche, cliquez dessus.
3. Dans le panneau **MyVm** qui s’affiche, cliquez sur le bouton **Connexion** dans l’angle supérieur gauche du panneau. **Remarque :** si **Création** est visible sous le bouton **Connexion**, cela signifie qu’Azure n’a pas encore fini de créer la machine virtuelle. Ne cliquez sur **Connexion** que lorsque la mention **Création** disparaît sous le bouton **Connexion**.
4. Autorisez votre navigateur à télécharger le fichier **MyVm.rdp**.  Une fois le fichier téléchargé, cliquez dessus pour l’ouvrir. 
5. Dans la boîte de dialogue **Connexion Bureau à distance** qui s’affiche, indiquant que le serveur de publication de la connexion à distance ne peut pas être identifié, cliquez sur le bouton **Connexion**.
6. Dans la boîte de dialogue **Sécurité Windows** qui s’affiche, cliquez sur **Autres choix**, puis sur **Utiliser un autre compte**. Entrez le nom d’utilisateur et le mot de passe que vous avez entrés à l’étape 4, puis cliquez sur le bouton **OK**.
7. Dans la boîte de dialogue Connexion Bureau à distance qui s’affiche, vous informant que l’identité de l’ordinateur distant ne peut pas être vérifiée, cliquez sur le bouton **Oui**.
8. Cliquez avec le bouton droit sur le bouton Démarrer de Windows, puis cliquez sur **Gestionnaire de périphériques**. Développez le nœud **Cartes réseau**. Vérifiez que la mention **Carte Ethernet Mellanox ConnectX-3 fonction virtuelle** s’affiche comme dans l’image suivante :
   
    ![Gestionnaire de périphériques](./media/virtual-network-create-vm-accelerated-networking/image2.png)

9. La mise en réseau accélérée est maintenant activée pour votre machine virtuelle.

## <a name="create-a-linux-vm"></a>Créer une machine virtuelle Linux
Vous pouvez utiliser le portail Azure ou Azure [PowerShell](#linux-powershell) pour créer une machine virtuelle Ubuntu ou SLES. Le flux de travail est différent pour les machines virtuelles RHEL et CentOS.  Consultez les instructions ci-dessous.

### <a name="linux-portal"></a>Portail
1. Inscrivez-vous à la mise en réseau accélérée pour Linux en version préliminaire en procédant de la manière décrite dans les étapes 1 à 5 de la section [Créer une machine virtuelle Linux - PowerShell](#linux-powershell) de cet article.  Vous ne pouvez pas vous inscrire à la version préliminaire via le portail.
2. Procédez de la manière décrite dans les étapes 1 à 8 dans la section [Créer une machine virtuelle Windows - Portail](#windows-portal) de cet article. À l’étape 2, cliquez sur **Ubuntu Server 16.04 LTS** au lieu de **Windows Server Datacenter 2016**. Dans le cadre de ce didacticiel, choisissez d’utiliser un mot de passe plutôt qu’une clé SSH. Cependant, pour des déploiements de production, vous pouvez utiliser celle-ci. Si la mention **Mise en réseau accélérée** n’apparaît pas lorsque vous procédez de la manière décrite à l’étape 7 dans la section [Créer une machine virtuelle Windows - Portail](#windows-portal) de cet article, c’est probablement pour l’une des raisons suivantes :
    - Vous n’êtes pas encore inscrit pour la version préliminaire. Vérifiez que votre état d’enregistrement est **Inscrit**, comme décrit à l’étape 4 dans la section [Créer une machine virtuelle Linux - PowerShell](#linux-powershell) de cet article. **Remarque :** si vous avez participé à la mise en réseau accélérée pour la version préliminaire de machines virtuelles Windows (il n’est plus nécessaire de vous inscrire pour la mise en réseau accélérée pour les machines virtuelles), vous n’êtes pas automatiquement inscrit pour la mise en réseau accélérée pour machines virtuelles Linux en version préliminaire. Pour y participer, vous devez vous inscrire à la mise en réseau accélérée pour machines virtuelles Linux en version préliminaire.
    - Vous n’avez pas sélectionné de taille de machine virtuelle, de système d’exploitation ou d’emplacement répertorié dans la section [Limitations](#limitations) de cet article.
3. Pour installer le pilote de mise en réseau accélérée pour Linux, procédez de la manière décrite dans la section [Configurer Linux](#configure-linux) de cet article.

### <a name="linux-powershell"></a>PowerShell

>[!WARNING]
>Si vous créez des machines virtuelles Linux avec mise en réseau accélérée, puis tentez de créer une machine virtuelle Windows avec mise en réseau accélérée dans le même abonnement, la création de la machine virtuelle Windows peut échouer. Dans cette version préliminaire, il est recommandé de tester les machines virtuelles Linux et Windows avec une mise en réseau accélérée dans des abonnements distincts.
>

1. Installez la dernière version du module Azure PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/). Si vous débutez dans l’utilisation d’Azure PowerShell, voir [Vue d’ensemble d’Azure PowerShell](/powershell/azure/get-started-azureps?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Démarrez une session PowerShell en cliquant sur le bouton Démarrer de Windows, en tapant **powershell**, puis en cliquant sur **PowerShell** dans les résultats de la recherche.
3. Dans la fenêtre PowerShell, entrez la commande `login-azurermaccount` pour vous connecter avec votre [compte](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) Azure. Si vous n’avez pas de compte, vous pouvez vous inscrire pour bénéficier d’un [essai gratuit](https://azure.microsoft.com/offers/ms-azr-0044p).
4. Inscrivez-vous à la mise en réseau accélérée pour Azure en version préliminaire en procédant comme suit :
    - Envoyez un e-mail à [axnpreview@microsoft.com](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) en indiquant votre ID d’abonnement Azure et l’utilisation prévue. Veuillez attendre un e-mail de confirmation de la part de Microsoft concernant l’activation de votre abonnement.
    - Entrez la commande suivante pour confirmer que vous êtes inscrit à la version préliminaire :
    
        ```powershell
        Get-AzureRmProviderFeature -FeatureName AllowAcceleratedNetworkingForLinux -ProviderNamespace Microsoft.Network
        ```

        Ne passez pas à l’étape 5 tant que la mention **Inscrit** n’apparaît pas dans la sortie après que vous avez entré la commande précédente. Pour que vous puissiez continuer, la sortie doit ressembler ceci :
    
        ```powershell
        FeatureName                        ProviderName      RegistrationState
        -----------                        ------------      -----------------
        AllowAcceleratedNetworkingForLinux Microsoft.Network Registered
        ```
        
      >[!NOTE]
      >Si vous avez participé à la mise en réseau accélérée pour la version préliminaire de machines virtuelles Windows (il n’est plus nécessaire de vous inscrire pour la mise en réseau accélérée pour les machines virtuelles), vous n’êtes pas automatiquement inscrit pour la mise en réseau accélérée pour machines virtuelles Linux en version préliminaire. Pour y participer, vous devez vous inscrire à la mise en réseau accélérée pour machines virtuelles Linux en version préliminaire.
      >
5. Dans votre navigateur, copiez le script suivant en remplaçant Ubuntu ou SLES selon vos besoins.  Ici encore, Redhat et CentOS ont un flux de travail différent, décrit ci-dessous :

    ```powershell
    $RgName="MyResourceGroup"
    $Location="westus2"
    
    # Create a resource group
    New-AzureRmResourceGroup `
      -Name $RgName `
      -Location $Location
    
    # Create a subnet
    $Subnet = New-AzureRmVirtualNetworkSubnetConfig `
      -Name MySubnet `
      -AddressPrefix 10.0.0.0/24
    
    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName $RgName `
      -Location $Location `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet

    # Create a public IP address
    $Pip = New-AzureRmPublicIpAddress `
      -Name MyPublicIp `
      -ResourceGroupName $RgName `
      -Location $Location `
      -AllocationMethod Static

    # Create a virtual network interface and associate the public IP address to it
    $Nic = New-AzureRmNetworkInterface `
      -Name MyNic `
      -ResourceGroupName $RgName `
      -Location $Location `
      -SubnetId $Vnet.Subnets[0].Id `
      -PublicIpAddressId $Pip.Id `
      -EnableAcceleratedNetworking
     
    # Create a new Storage account and define the new VM’s OSDisk name and its URI
    # Must end with ".vhd" extension
    $OSDiskName = "MyOsDiskName.vhd"
    # Storage account name must be between 3 and 24 characters in length and use numbers and lower-case letters only.
    $OSDiskSAName = "thestorageaccountname"  
    $StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $RgName -Name $OSDiskSAName -Type "Standard_GRS" -Location $Location
    $OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName
 
    # Define a credential object for the VM. PowerShell prompts you for a username and password.
    $Cred = Get-Credential

    # Create a virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
      -VMName MyVM -VMSize Standard_DS4_v2 | `
      Set-AzureRmVMOperatingSystem `
      -Linux `
      -ComputerName myVM `
      -Credential $Cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName Canonical `
      -Offer UbuntuServer `
      -Skus 16.04-LTS `
      -Version latest | `
    Add-AzureRmVMNetworkInterface -Id $Nic.Id | `
    Set-AzureRmVMOSDisk -Name $OSDiskName `
      -VhdUri $OSDiskUri `
      -CreateOption FromImage 

    # Create the virtual machine.    
    New-AzureRmVM `
      -ResourceGroupName $RgName `
      -Location $Location `
      -VM $VmConfig
    ```
    
6. Dans la fenêtre PowerShell, cliquez avec le bouton droit pour coller le script et commencer à l’exécuter. Vous êtes invité à entrer un nom d’utilisateur et un mot de passe. Utilisez ces informations d’identification pour vous ouvrir une session sur la machine virtuelle lors de la connexion à celle-ci à l’étape suivante. Si le script échoue, vérifiez les points suivants :
    - Vous êtes inscrit pour la version préliminaire, comme expliqué à l’étape 4.
    - Si vous avez modifié la taille de machine virtuelle, le type de système d’exploitation ou les valeurs d’emplacement dans le script avant l’exécution de celui-ci, les valeurs sont bien celles mentionnées dans la section [Limitations](#Limitations) de cet article.
7. Pour installer le pilote de mise en réseau accélérée pour Linux, procédez de la manière décrite dans la section [Configurer Linux](#configure-linux) de cet article.

### <a name="configure-linux"></a>Configurer Linux

Une fois la machine virtuelle crée dans Azure, vous devez installer le pilote de mise en réseau accélérée pour Linux. Avant de passer aux étapes suivantes, vous devez avoir créé une machine virtuelle Linux avec une mise en réseau accélérée en suivant l’une des étapes [portail](#linux-portal) ou [PowerShell](#linux-powershell) décrites dans cet article. 

1. Dans un navigateur Internet, ouvrez le [portail](https://portal.azure.com) Azure et connectez-vous avec votre [compte](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) Azure. Si vous n’avez pas de compte, vous pouvez vous inscrire pour bénéficier d’un [essai gratuit](https://azure.microsoft.com/offers/ms-azr-0044p).
2. En haut du portail, à droite de la barre *Rechercher des ressources*, cliquez sur l’icône **>_** pour démarrer un Cloud Shell Bash (version préliminaire). Le volet Cloud Shell Bash apparaît au bas du portail et, après quelques secondes, présente une invite  **username@Azure:~$**. Bien que vous puissiez utiliser une clé SSH plutôt que le Cloud Shell pour vous connecter à la machine virtuelle à partir de votre ordinateur, les instructions de ce didacticiel supposent que vous utilisez le Cloud Shell.
3. En haut du portail, dans la boîte de dialogue contenant le texte *Rechercher des ressources*, tapez *MyVm*. Lorsque **MyVm** apparaît dans les résultats de recherche, cliquez dessus.
4. Dans le panneau **MyVm** qui s’affiche, cliquez sur le bouton **Connexion** dans l’angle supérieur gauche du panneau. **Remarque :** si **Création** est visible sous le bouton **Connexion**, cela signifie qu’Azure n’a pas encore fini de créer la machine virtuelle. Ne cliquez sur **Connexion** que lorsque la mention **Création** disparaît sous le bouton **Connexion**.
5. Azure ouvre une boîte de dialogue invitant à entrer l’`ssh adminuser@<ipaddress>`. Entrez la commande suivante dans le Cloud Shell (ou copier-la à partir de la boîte de dialogue qui s’est affichée à l’étape 4 et collez-la dans le Cloud Shell), puis appuyez sur Entrée.
6. Tapez **Oui** en réponse à la question vous demandant si vous souhaitez poursuivre l’opération de connexion, puis appuyez sur Entrée.
7. Tapez le mot de passe que vous avez entré lors de la création de la machine virtuelle. Une fois connecté à la machine virtuelle, vous voyez s’afficher l’invite adminuser@MyVm:~$. Vous êtes à présent connecté à la machine virtuelle via la session de Cloud Shell. **Remarque :** les sessions de Cloud Shell expirent après 10 minutes d’inactivité.

À ce stade, les instructions varient en fonction de la distribution que vous utilisez. 

#### <a name="ubuntusles"></a>Ubuntu/SLES

1. À l’invite, entrez `uname -r` et confirmez que la version pour :

    * Ubuntu « 4.4.0-77-generic », ou supérieur
    * SLES « 4.4.59-92.20-default » ou supérieur

2. Créez une liaison entre la carte réseau virtuelle de mise en réseau standard et la carte réseau virtuelle de mise en réseau accélérée en exécutant les commandes suivantes. Le trafic réseau utilise la carte réseau virtuelle de mise en réseau accélérée la plus performante, tandis que la liaison garantit que le trafic réseau ne soit pas interrompu lors de certains changements de configuration.
          
     ```bash
     wget https://raw.githubusercontent.com/LIS/lis-next/master/tools/sriov/configure_hv_sriov.sh
     chmod +x ./configure_hv_sriov.sh
     sudo ./configure_hv_sriov.sh
     ```
3. Après avoir exécuté le script, la machine virtuelle redémarre après une pause de 60 secondes.
4. Une fois que la machine virtuelle a redémarré, reconnectez-vous à celle-ci en procédant de la manière décrite aux étapes 5 à 7.
5. Exécutez la commande `ifconfig`, en vérifiant que bond0 apparaît et que l’interface s’affiche comme étant en service. 
 
 >[!NOTE]
      >Les applications utilisant une mise en réseau accélérée doivent communiquer via l’interface *bond0*, et non *eth0*.  Le nom de l’interface peut changer avant que la mise en réseau accélérée soit en disponibilité générale.

#### <a name="rhelcentos"></a>RHEL/CentOS

La création d’une machine virtuelle Red Hat Enterprise Linux ou CentOS 7.3 nécessite quelques étapes supplémentaires pour charger les derniers pilotes requis pour SR-IOV ainsi que le pilote Virtual Fonction (VF) pour la carte réseau. La première phase des instructions prépare une image qui peut être utilisée pour créer une ou plusieurs machines virtuelles sur lesquelles les pilotes sont préchargés.

##### <a name="phase-one-prepare-a-red-hat-enterprise-linux-or-centos-73-base-image"></a>Phase 1 : préparer une image de base Red Hat Enterprise Linux ou CentOS 7.3. 

1.  Approvisionner une machine virtuelle non-SRIOV CentOS 7.3 sur Azure

2.  Installer LIS 4.2.2 :
    
    ```bash
    wget http://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.2-2.tar.gz
    tar -xvf lis-rpms-4.2.2-2.tar.gz
    cd LISISO && sudo ./install.sh
    ```

3.  Télécharger les fichiers de configuration
    
    ```bash
    cd /etc/udev/rules.d/  
    sudo wget https://raw.githubusercontent.com/LIS/lis-next/master/tools/sriov/60-hyperv-vf-name.rules 
    cd /usr/sbin/
    sudo wget https://raw.githubusercontent.com/LIS/lis-next/master/tools/sriov/hv_vf_name 
    sudo chmod +x hv_vf_name
    cd /etc/sysconfig/network-scripts/
    sudo wget https://raw.githubusercontent.com/LIS/lis-next/master/tools/sriov/ifcfg-vf1   
    ```

4.  Annuler l’approvisionnement de cette machine virtuelle

    ```bash
    sudo waagent -deprovision+user 
    ```

5.  Depuis le portail Azure, arrêtez cette machine virtuelle, accédez aux « disques » de la machine virtuelle puis capturez l’URI du disque dur virtuel OSDisk. Cet URI contient le nom du disque dur virtuel de l’image de base et son compte de stockage. 
 
##### <a name="phase-two-provision-new-vms-on-azure"></a>Phase 2 : approvisionner de nouvelles machines virtuelles sur Azure

1.  Approvisionnez de nouvelles machines virtuelles basées sur New-AzureRMVMConfig à l’aide de l’image de base VHD capturée lors de la première phase, avec AcceleratedNetworking activé sur la carte réseau virtuelle :

    ```powershell
    $RgName="MyResourceGroup"
    $Location="westus2"
    
    # Create a resource group
    New-AzureRmResourceGroup `
     -Name $RgName `
     -Location $Location

    # Create a subnet
    $Subnet = New-AzureRmVirtualNetworkSubnetConfig `
     -Name MySubnet `
     -AddressPrefix 10.0.0.0/24

    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
     -ResourceGroupName $RgName `
     -Location $Location `
     -Name MyVnet `
     -AddressPrefix 10.0.0.0/16 `
     -Subnet $Subnet
    
    # Create a public IP address
    $Pip = New-AzureRmPublicIpAddress `
     -Name MyPublicIp `
     -ResourceGroupName $RgName `
     -Location $Location `
     -AllocationMethod Static
    
    # Create a virtual network interface and associate the public IP address to it
    $Nic = New-AzureRmNetworkInterface `
     -Name MyNic `
     -ResourceGroupName $RgName `
     -Location $Location `
     -SubnetId $Vnet.Subnets[0].Id `
     -PublicIpAddressId $Pip.Id `
     -EnableAcceleratedNetworking
    
    # Specify the base image's VHD URI (from phase one step 5). 
    # Note: The storage account of this base image vhd should have "Storage service encryption" disabled
    # See more from here: https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption
    # This is just an example URI, you will need to replace this when running this script
    $sourceUri="https://myexamplesa.blob.core.windows.net/vhds/CentOS73-Base-Test120170629111341.vhd" 

    # Specify a URI for the location from which the new image binary large object (BLOB) is copied to start the virtual machine. 
    # Must end with ".vhd" extension
    $OsDiskName = "MyOsDiskName.vhd" 
    $destOsDiskUri = "https://myexamplesa.blob.core.windows.net/vhds/" + $OsDiskName
    
    # Define a credential object for the VM. PowerShell prompts you for a username and password.
    $Cred = Get-Credential
    
    # Create a custom virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
     -VMName MyVM -VMSize Standard_DS4_v2 | `
    Set-AzureRmVMOperatingSystem `
     -Linux `
     -ComputerName myVM `
     -Credential $Cred | `
    Add-AzureRmVMNetworkInterface -Id $Nic.Id | `
    Set-AzureRmVMOSDisk `
     -Name $OsDiskName `
     -SourceImageUri $sourceUri `
     -VhdUri $destOsDiskUri `
     -CreateOption FromImage `
     -Linux
    
    # Create the virtual machine.    
    New-AzureRmVM `
     -ResourceGroupName $RgName `
     -Location $Location `
     -VM $VmConfig
    ```

2.  Une fois les machines virtuelles démarrées, vérifiez le périphérique VF à l’aide de « lspci » et vérifiez l’entrée Mellanox. Par exemple, nous devrions voir cet élément dans la sortie lspci :
    
    ```
    0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
    ```
    
3.  Exécutez le script de liaison par :

    ```bash
    sudo bondvf.sh
    ```

4.  Redémarrez les nouvelles machines virtuelles :

    ```bash
    sudo reboot
    ```

La machine virtuelle devrait commencer par bond0 configuré, avec activation du chemin de mise réseau accélérée.  Exécutez `ifconfig` pour confirmer.

