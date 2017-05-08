---
title: "Azure Active Directory Domain Services : guide d’administration | Microsoft Docs"
description: "Joignez une machine virtuelle Windows à un domaine géré avec Azure PowerShell et le modèle de déploiement classique."
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 9143b843-7327-43c3-baab-6e24a18db25e
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 9fc3b38b22c3a092dc2d932d2d7471872f5ba66d
ms.lasthandoff: 04/27/2017


---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain-using-powershell"></a>Joindre une machine virtuelle Windows Server à un domaine géré à l’aide de PowerShell
> [!div class="op_single_selector"]
> * [Portail Azure Classic - Windows](active-directory-ds-admin-guide-join-windows-vm.md)
> * [PowerShell - Windows](active-directory-ds-admin-guide-join-windows-vm-classic-powershell.md)
>
>

<br>

> [!IMPORTANT]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../azure-resource-manager/resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Actuellement, les services de domaine Azure AD ne prennent pas en charge le modèle de gestionnaire de ressources.
>
>

Ces étapes vous montrent comment personnaliser un jeu de commandes Azure PowerShell en vue de créer et de préconfigurer une machine virtuelle Azure basée sur Windows à l'aide d'une approche modulaire. Ces étapes peuvent vous aider à créer une machine virtuelle Azure Windows et à la joindre à un domaine géré de services de domaine Azure AD.

Ces étapes utilisent une méthode de cases à remplir pour créer des jeux de commandes Azure PowerShell. Cette méthode peut être utile si vous découvrez PowerShell ou souhaitez connaître les valeurs à indiquer pour une configuration réussie. Les utilisateurs avancés de PowerShell peuvent prendre les commandes et indiquer leurs propres valeurs pour les variables (lignes commençant par « $ »).

Si ce n’est pas encore fait, installez Azure PowerShell sur votre ordinateur local à l’aide des instructions décrites dans [Installation et configuration d’Azure PowerShell](/powershell/azure/overview) . Puis ouvrez une invite de commandes Windows PowerShell.

## <a name="step-1-add-your-account"></a>Étape 1 : Ajouter votre compte
1. À l’invite PowerShell, tapez **Add-AzureAccount**, puis cliquez sur **Entrée**.
2. Tapez l’adresse de messagerie associée à votre abonnement Azure, puis cliquez sur **Continuer**.
3. Tapez le mot de passe de votre compte.
4. Cliquez sur **Se connecter**.

## <a name="step-2-set-your-subscription-and-storage-account"></a>Étape 2 : Configurer votre abonnement et votre compte de stockage
Pour configurer votre abonnement et votre compte de stockage Azure, exécutez ces commandes à l’invite de commandes Windows PowerShell. Remplacez tous les éléments entre guillemets, y compris les caractères < et >, par les noms appropriés.

    $subscr="<subscription name>"
    $staccount="<storage account name>"
    Select-AzureSubscription -SubscriptionName $subscr –Current
    Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

Le nom de l’abonnement apparaît dans la propriété SubscriptionName du résultat de la commande **Get-AzureSubscription** . Le nom du compte de stockage correct apparaît dans la propriété Label de la sortie de la commande **Get-AzureStorageAccount** une fois que vous avez exécuté la commande **Select-AzureSubscription**.

## <a name="step-3-step-by-step-walkthrough---provision-the-virtual-machine-and-join-it-to-the-managed-domain"></a>Étape 3: Procédure pas à pas – approvisionnez la machine virtuelle et joignez-la au domaine géré
Voici le jeu de commandes Azure PowerShell correspondant qui permet de créer cette machine virtuelle. Les lignes vides entre chaque bloc offrent une meilleure lisibilité.

Spécifiez des informations sur la machine virtuelle Windows à approvisionner.

    $family="Windows Server 2012 R2 Datacenter"
    $vmname="Contoso100-test"
    $vmsize="ExtraSmall"

Pour plus d’informations sur les valeurs InstanceSize des machines virtuelles des séries D, DS et G, voir l’article [Tailles de machines virtuelles et de services cloud pour Microsoft Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx).

Fournissent des informations sur le domaine géré.

    $domaindns="contoso100.com"
    $domacctdomain="contoso100"

Spécifiez le nom du service cloud.

    $svcname="Contoso100-test"

Spécifiez le nom du réseau virtuel auquel la machine virtuelle doit être jointe. Assurez-vous que le domaine géré AAD-DS est disponible dans ce réseau virtuel.

    $vnetname="MyPreviewVnet"

Sélectionnez l'image de machine virtuelle à utiliser pour approvisionner la machine virtuelle.

    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

Configurez la machine virtuelle - définissez le nom de la machine virtuelle, la taille d'instance et l’image à utiliser.

    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

Obtenez les informations d’identification du compte d’administrateur local pour la machine virtuelle. Choisissez un mot de passe administrateur fort.

    $localadmincred=Get-Credential –Message "Type the name and password of the local administrator account."

Obtenez des informations d'identification pour un compte d'utilisateur appartenant au groupe « Administrateurs de contrôleur de domaine AAD » pour joindre la machine virtuelle au domaine géré. Ne spécifiez pas le nom de domaine – par exemple, dans notre exemple, nous spécifions « bob » comme nom d'utilisateur.

    $domainadmincred=Get-Credential –Message "Now type the name (DO NOT INCLUDE THE DOMAIN) and password of an account in the AAD DC Administrators group, that has permission to add the machine to the domain."

Configurez la machine virtuelle - spécifiez la nécessité de jonction de domaine et les informations d'identification requises.

    $vm1 | Add-AzureProvisioningConfig -AdminUsername $localadmincred.Username -Password $localadmincred.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $domainadmincred.Username -DomainPassword $domainadmincred.GetNetworkCredential().Password -JoinDomain $domaindns

Définissez un sous-réseau pour la machine virtuelle.

    $vm1 | Set-AzureSubnet -SubnetNames "Subnet-1"

Facultatif : point vers l'adresse IP du domaine. Si vous définissez les adresses IP du domaine des services de domaine Azure AD géré pour être les serveurs DNS du réseau virtuel, cette étape n'est pas requise.

    $dns = New-AzureDns -Name 'contoso100-dc1' -IPAddress '10.0.0.4'

Maintenant, approvisionnez la machine virtuelle Windows jointe à un domaine.

    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname -Location "Central US" -DnsSettings $dns

<br>

## <a name="script-to-provision-a-windows-vm-and-automatically-join-it-to-an-aad-domain-services-managed-domain"></a>Script pour approvisionner une machine virtuelle Windows et la joindre automatiquement à un domaine géré de services de domaine AAD
Cette commande PowerShell crée une machine virtuelle pour un serveur métier qui :

* utilise l'image Windows Server 2012 R2 Datacenter
* est une machine virtuelle très petite
* porte le nom Contoso100-test
* est automatiquement joint au domaine géré contoso100
* est ajouté au même réseau virtuel en tant que le domaine géré.

Voici l'exemple de script complet pour créer la machine virtuelle Windows et la joindre automatiquement aux services de domaine Azure AD gérés.

    $family="Windows Server 2012 R2 Datacenter"
    $vmname="Contoso100-test"
    $vmsize="ExtraSmall"

    $domaindns="contoso100.com"
    $domacctdomain="contoso100"

    $svcname="Contoso100-test"
    $vnetname="MyPreviewVnet"

    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    $localadmincred=Get-Credential –Message "Type the name and password of the local administrator account."

    $domainadmincred=Get-Credential –Message "Now type the name (not including the domain) and password of an account in the AAD DC Administrators group, that has permission to add the machine to the domain."

    $vm1 | Add-AzureProvisioningConfig -AdminUsername $localadmincred.Username -Password $localadmincred.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $domainadmincred.Username -DomainPassword $domainadmincred.GetNetworkCredential().Password -JoinDomain $domaindns

    $vm1 | Set-AzureSubnet -SubnetNames "Subnet-1"

    $dns = New-AzureDns -Name 'contoso100-dc1' -IPAddress '10.0.0.4'

    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname -Location "Central US" -DnsSettings $dns

<br>

## <a name="related-content"></a>Contenu connexe
* [Services de domaine Azure AD : guide de prise en main](active-directory-ds-getting-started.md)
* [Administrer un domaine géré par les services de domaine Azure Active Directory](active-directory-ds-admin-guide-administer-domain.md)

