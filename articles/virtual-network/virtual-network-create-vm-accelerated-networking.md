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
ms.translationtype: Human Translation
ms.sourcegitcommit: 17c4dc6a72328b613f31407aff8b6c9eacd70d9a
ms.openlocfilehash: 43663ed8becfa69c06699709a18623652df28ed6
ms.contentlocale: fr-fr
ms.lasthandoff: 05/16/2017


---
# <a name="create-a-virtual-machine-with-accelerated-networking"></a>Créer une machine virtuelle avec mise en réseau accélérée

Ce didacticiel explique comment créer une machine virtuelle Azure (VM) avec mise en réseau accélérée. Une mise en réseau accélérée permet d’opérer une virtualisation d’E/S d’une racine unique (SR-IOV) sur une machine virtuelle, ce qui améliore considérablement les performances de mise en réseau. Cette voie hautement performante court-circuite l’hôte du chemin d’accès aux données, réduisant ainsi la latence, l’instabilité et l’utilisation du processeur pour servir les charges de travail réseau les plus exigeantes sur les types de machines virtuelles pris en charge. L’illustration suivante montre la communication entre deux machines virtuelles avec ou sans mise en réseau accélérée :

![Opérateurs de comparaison](./media/virtual-network-create-vm-accelerated-networking/image1.png)

Sans mise en réseau accélérée, tout le trafic réseau en direction et en provenance de la machine virtuelle doit transiter par l’hôte et le commutateur virtuel. Le commutateur virtuel fournit au trafic réseau toutes les stratégies, telles que les groupes de sécurité réseau, les listes de contrôle d’accès, l’isolation et d’autres services de réseau virtualisé. Pour plus d’informations sur les commutateurs virtuels, voir [Vue d’ensemble de la virtualisation de réseau Hyper-V](https://technet.microsoft.com/library/jj945275.aspx).

Dans le cas d’une mise en réseau accélérée, le trafic réseau parvient à la carte réseau de la machine virtuelle avant d’être transféré vers celle-ci. Toutes les stratégies réseau que le commutateur virtuel applique sans mise en réseau accélérée sont déchargées et appliquées dans le matériel. L’application de la stratégie au niveau du matériel permet à la carte réseau de transférer le trafic directement à la machine virtuelle, en ignorant l’hôte et le commutateur virtuel, tout en conservant toutes les stratégies qu’il a appliquées dans l’hôte.

Les avantages d’une mise en réseau accélérée s’appliquent uniquement à la machine virtuelle activée. Pour des résultats optimaux, il convient d’activer cette fonctionnalité sur au moins deux machines virtuelles connectées au même réseau virtuel Azure Virtual Network. Lors de la communication entre réseaux virtuels ou d’une connexion locale, cette fonctionnalité a une incidence minimale sur la latence globale.

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

## <a name="benefits"></a>Avantages
* **Latence plus faible/Nombre supérieur de paquets par seconde (pps) :** la suppression du commutateur virtuel du chemin de données évite que les paquets liés au traitement des stratégies séjournent sur l’hôte, ce qui augmente le nombre de paquets pouvant être traités dans la machine virtuelle.
* **Instabilité réduite :** le traitement du commutateur virtuel dépend de la quantité de stratégie qui doit être appliquée et de la charge de travail du processeur qui effectue le traitement. Le déchargement des stratégies sur le matériel supprime cette variabilité en fournissant des paquets directement à la machine virtuelle, en supprimant l’hôte dans la communication de la machine virtuelle et toutes les interruptions de logiciel et les changements de contexte.
* **Utilisation réduite du processeur :** ignorer le commutateur virtuel dans l’hôte réduit l’utilisation du processeur pour le traitement du trafic réseau.

## <a name="Limitations"></a>Limitations
Les limitations suivantes existent lors de l’utilisation de cette fonctionnalité :

* **Création d’interface réseau :** la mise en réseau accélérée ne peut être activée que pour une nouvelle carte réseau. Elle ne peut pas être activée pour une carte réseau existante.
* **Création de machine virtuelle :** une carte réseau avec mise en réseau accélérée activée ne peut être attachée à une machine virtuelle que lors de la création de celle-ci. Une carte réseau ne peut pas être attachée à une machine virtuelle existante.
* **Régions :** des machines virtuelles Windows avec mise en réseau accélérée sont proposées dans la plupart des régions Azure. Des machines virtuelles Linux avec mise en réseau accélérée ne sont proposées que dans deux régions : Sud du centre des États-Unis et États-Unis de l’Ouest 2. Les régions dans lesquelles cette fonctionnalité est disponible seront étendues à l’avenir.
* **Systèmes d’exploitation pris en charge :** Windows : Microsoft Windows Server 2012 R2 Datacenter et Windows Server 2016. Linux : Ubuntu Server 16.04 LTS avec noyau 4.4.0-77 ou version supérieure. Des distributions supplémentaires seront bientôt ajoutées.
* **Taille de machine virtuelle :** tailles d’instance à usage général et de calcul optimisé avec au moins huit cœurs. Pour plus d’informations, voir les articles sur les tailles de machine virtuelle [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) et [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Le nombre de tailles d’instance de machine virtuelle augmentera dans le futur.

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
6. Cliquez sur **DS4_V2 Standard**, puis sur le bouton **Sélectionner**.
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
8. Sur la machine virtuelle MyVm, cliquez sur le bouton Démarrer de Windows, puis sur **Internet Explorer**.
9. Dans la boîte de dialogue **Internet Explorer 11** qui s’affiche, cliquez sur **Utiliser les paramètres de sécurité, de confidentialité et de compatibilité recommandés**, puis sur **OK**.
10. Dans la barre d’adresse d’Internet Explorer, tapez https://gallery.technet.microsoft.com/Azure-Accelerated-471b5d84, puis appuyez sur Entrée.
11. Dans la boîte de dialogue **Alerte de sécurité** qui s’affiche, cliquez sur **OK**.
12. Dans la boîte de dialogue **Internet Explorer** qui s’affiche, cliquez sur **Ajouter**, cliquez sur le bouton **Ajouter** dans la boîte de dialogue **Sites de confiance**, puis cliquez sur le bouton **Fermer**. Procédez de la sorte pour toute autre boîte de dialogue qui s’affiche.
13. Pour télécharger le fichier, cliquez dessus.
14. Lorsque la boîte de dialogue **Termes du contrat de licence** s’affiche, cliquez sur **J’accepte**.
15. Autorisez Internet Explorer à enregistrer le fichier en cliquant sur le bouton **Enregistrer** dans la boîte de dialogue qui s’affiche en bas de l’écran, puis cliquez sur le bouton **Ouvrir le dossier**.
16. Pour installer le pilote de mise en réseau accélérée, double-cliquez sur le fichier. Pendant l’exécution de l’Assistant Installation, acceptez toutes les valeurs par défaut, et cliquez sur le bouton **Oui** à la fin pour redémarrer la machine virtuelle.
17. Quand la machine virtuelle redémarre, suivez les étapes 9 à 12 pour vous connecter à la machine virtuelle.
18. Cliquez avec le bouton droit sur le bouton Démarrer de Windows, puis cliquez sur **Gestionnaire de périphériques**. Développez le nœud **Cartes réseau**. Vérifiez que la mention **Carte Ethernet Mellanox ConnectX-3 fonction virtuelle** s’affiche comme dans l’image suivante :
   
    ![Gestionnaire de périphériques](./media/virtual-network-create-vm-accelerated-networking/image2.png)

## <a name="create-a-linux-vm"></a>Créer une machine virtuelle Linux
Pour créer la machine virtuelle, vous pouvez utiliser le Portail Azure ou Azure [PowerShell](#linux-powershell).

### <a name="linux-portal"></a>Portail
1. Inscrivez-vous à la mise en réseau accélérée pour Linux en version préliminaire en procédant de la manière décrite dans les étapes 1 à 5 de la section [Créer une machine virtuelle Linux - PowerShell](#linux-powershell) de cet article.  Vous ne pouvez pas vous inscrire à la version préliminaire via le portail.
2. Procédez de la manière décrite dans les étapes 1 à 8 dans la section [Créer une machine virtuelle Windows - Portail](#windows-portal) de cet article. À l’étape 2, cliquez sur **Ubuntu Server 16.04 LTS** au lieu de **Windows Server Datacenter 2016**. Dans le cadre de ce didacticiel, choisissez d’utiliser un mot de passe plutôt qu’une clé SSH. Cependant, pour des déploiements de production, vous pouvez utiliser celle-ci. Si la mention **Mise en réseau accélérée** n’apparaît pas lorsque vous procédez de la manière décrite à l’étape 7 dans la section [Créer une machine virtuelle Windows - Portail](#windows-portal) de cet article, c’est probablement pour l’une des raisons suivantes :
    - Vous n’êtes pas encore inscrit pour la version préliminaire. Vérifiez que votre état d’enregistrement est **Inscrit**, comme décrit à l’étape 4 dans la section [Créer une machine virtuelle Linux - PowerShell](#linux-powershell) de cet article. **Remarque :** si vous avez participé à la mise en réseau accélérée pour la version préliminaire de machines virtuelles Windows (il n’est plus nécessaire de vous inscrire pour la mise en réseau accélérée pour les machines virtuelles), vous n’êtes pas automatiquement inscrit pour la mise en réseau accélérée pour machines virtuelles Linux en version préliminaire. Pour y participer, vous devez vous inscrire à la mise en réseau accélérée pour machines virtuelles Linux en version préliminaire.
    - Vous n’avez pas sélectionné de taille de machine virtuelle, de système d’exploitation ou d’emplacement répertorié dans la section [Limitations](#simitations) de cet article.
3. Pour installer le pilote de mise en réseau accélérée pour Linux, procédez de la manière décrite dans la section [Configurer Linux](#configure-linux) de cet article.

### <a name="linux-powershell"></a>PowerShell

>[!WARNING]
>Si vous créez des machines virtuelles Linux avec mise en réseau accélérée, puis tentez de créer une machine virtuelle Windows avec mise en réseau accélérée dans le même abonnement, la création de la machine virtuelle Windows peut échouer. Dans cette version préliminaire, il est recommandé de tester les machines virtuelles Linux et Windows avec une mise en réseau accélérée dans des abonnements distincts.
>

1. Installez la dernière version du module Azure PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/). Si vous débutez dans l’utilisation d’Azure PowerShell, voir [Vue d’ensemble d’Azure PowerShell](/powershell/azure/get-started-azureps?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Démarrez une session PowerShell en cliquant sur le bouton Démarrer de Windows, en tapant **powershell**, puis en cliquant sur **PowerShell** dans les résultats de la recherche.
3. Dans la fenêtre PowerShell, entrez la commande `login-azurermaccount` pour vous connecter avec votre [compte](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) Azure. Si vous n’avez pas de compte, vous pouvez vous inscrire pour bénéficier d’un [essai gratuit](https://azure.microsoft.com/offers/ms-azr-0044p).
4. Inscrivez-vous à la mise en réseau accélérée pour Azure en version préliminaire en procédant comme suit :
    - Entrez les commandes suivantes :

        ```powershell
        Register-AzureRmProviderFeature -FeatureName AllowAcceleratedNetworkingFeature -ProviderNamespace Microsoft.Network
        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
        ```
    - Envoyez un e-mail à [axnpreview@microsoft.com](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) en indiquant votre ID d’abonnement Azure et l’utilisation prévue. 
    - Entrez la commande suivante pour confirmer que vous êtes inscrit à la version préliminaire :
    
        `Get-AzureRmProviderFeature -FeatureName AllowAcceleratedNetworkingFeature -ProviderNamespace Microsoft.Network`

        Ne passez pas à l’étape 5 tant que la mention **Inscrit** n’apparaît pas dans la sortie après que vous avez entré la commande précédente. Pour que vous puissiez continuer, la sortie doit ressembler ceci :
    
        ```
        FeatureName                       ProviderName      RegistrationState
        -----------                       ------------      -----------------
        AllowAcceleratedNetworkingFeature Microsoft.Network Registered
        ```
      >[!NOTE]
      >Si vous avez participé à la mise en réseau accélérée pour la version préliminaire de machines virtuelles Windows (il n’est plus nécessaire de vous inscrire pour la mise en réseau accélérée pour les machines virtuelles), vous n’êtes pas automatiquement inscrit pour la mise en réseau accélérée pour machines virtuelles Linux en version préliminaire. Pour y participer, vous devez vous inscrire à la mise en réseau accélérée pour machines virtuelles Linux en version préliminaire.
      >
5. Dans votre navigateur, copiez le script suivant :
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
      -Linux `
      -ComputerName myVM `
      -Credential $Cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName Canonical `
      -Offer UbuntuServer `
      -Skus 16.04-LTS `
      -Version latest | `
    Add-AzureRmVMNetworkInterface -Id $Nic.Id 

    # Create the virtual machine.    
    New-AzureRmVM `
      -ResourceGroupName $RgName `
      -Location $Location `
      -VM $VmConfig
    #
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
8. À l’invite, entrez `uname -r`, puis vérifiez que la sortie est la suivante : « 4.4.0-77-generic ».
9.    Créez une liaison entre la carte réseau virtuelle de mise en réseau standard et la carte réseau virtuelle de mise en réseau accélérée en exécutant les commandes suivantes. Le trafic réseau utilise la carte réseau virtuelle de mise en réseau accélérée la plus performante, tandis que la liaison garantit que le trafic réseau ne soit pas interrompu lors de certains changements de configuration. 
    - `wget https://git.kernel.org/pub/scm/linux/kernel/git/next/linux-next.git/plain/tools/hv/bondvf.sh`
    - `chmod +x ./bondvf.sh`
    - `sudo ./bondvf.sh`
    - `sudo mv ~/bondvf.sh /etc/init.d`
    - `sudo update-rc.d bondvf.sh defaults` Remarque : si le message d’erreur *insserv: warning: script ’bondvf.sh’ missing LSB tags and overrides*, s’affiche, vos pouvez l’ignorer.
    - `sudo nano /etc/network/interfaces.d/50-cloud-init.cfg` Ouvre l’éditeur nano de GNU pour vous permettre de modifier le fichier.
    - Dans l’éditeur, commentez les lignes *auto etho0* et *iface eth0 inet dhcp* en ajoutant *#*  au début de celles-ci. Après que vous avez ajouté  *#* aux lignes, celles-ci ressemblent à l’exemple suivant :
        - #<a name="auto-eth0"></a>auto eth0
        - #<a name="iface-eth0-inet-dhcp"></a>iface eth0 inet dhcp
10. Maintenez les touches **Ctrl+X** enfoncées, tapez **Y**, puis appuyez sur **Entrée** pour enregistrer le fichier.
11. Redémarrez la machine virtuelle en entrant la commande `sudo shutdown -r now`.
12. Une fois que la machine virtuelle a redémarré, reconnectez-vous à celle-ci en procédant de la manière décrite aux étapes 5 à 7.
13.    Exécutez la commande `ifconfig`, en vérifiant que bond0 apparaît et que l’interface s’affiche comme étant en service. **Remarque :** une application utilisant une mise en réseau accélérée doit communiquer via l’interface *bond0*, et non *eth0*.  Le nom de l’interface peut changer avant que la mise en réseau accélérée soit en disponibilité générale.

