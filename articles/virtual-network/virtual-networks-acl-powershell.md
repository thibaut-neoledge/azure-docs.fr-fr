---
title: Comment gérer les listes de contrôle d’accès (ACL) pour les points de terminaison à l’aide de PowerShell
description: En savoir plus sur la gestion des listes ACL avec PowerShell
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn

ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial

---
# Comment gérer les listes de contrôle d’accès (ACL) pour les points de terminaison à l’aide de PowerShell
Vous pouvez créer et gérer des listes de contrôle d’accès réseau pour les points de terminaison à l’aide d’Azure PowerShell ou du portail de gestion. Dans cette rubrique, vous trouverez des procédures pour les tâches courantes des listes de contrôle d’accès que vous pouvez effectuer à l’aide de PowerShell. Pour obtenir la liste des applets de commande Azure PowerShell, consultez [Applets de commande de gestion Azure](http://go.microsoft.com/fwlink/?LinkId=317721). Pour plus d’informations sur les listes de contrôle d’accès, consultez [Qu’est-ce qu’une liste de contrôle d’accès (ACL) réseau ?](virtual-networks-acl.md). Si vous voulez gérer vos listes de contrôle d’accès à l’aide du portail de gestion, consultez [Configuration des points de terminaison sur une machine virtuelle](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md).

## Gérer les listes de contrôle d’accès réseau à l’aide d’Azure PowerShell
Vous pouvez utiliser les applets de commande Azure PowerShell pour créer, supprimer et configurer (définir) des listes de contrôle d’accès réseau. Nous avons inclus quelques exemples qui illustrent des méthodes de configuration de listes de contrôle d’accès à l’aide de PowerShell.

Pour récupérer la liste complète des applets de commande PowerShell des listes ACL, utilisez l’une des opérations suivantes :

    Get-Help *AzureACL*
    Get-Command -Noun AzureACLConfig

### Créer une liste de contrôle d’accès réseau avec des règles qui autorisent l’accès à un sous-réseau distant
L’exemple suivant illustre un moyen de créer une liste ACL contenant des règles. Cette liste ACL est ensuite appliquée à un point de terminaison de machine virtuelle. Les règles ACL dans l’exemple ci-dessous autorisent l’accès à un sous-réseau distant. Pour créer une liste de contrôle d’accès réseau avec des règles Permit pour un sous-réseau distant, ouvrez un environnement d’écriture de scripts intégré d’Azure PowerShell. Copiez et collez le script ci-dessous, en configurant le script avec vos propres valeurs, puis exécutez le script.

1. Créez l’objet de liste de contrôle d’accès réseau.
   
        $acl1 = New-AzureAclConfig
2. Définissez une règle qui autorise l’accès depuis un sous-réseau distant. Dans l’exemple ci-dessous, vous définissez la règle *100* (qui est prioritaire sur la règle 200 et supérieure) pour autoriser le sous-réseau distant *10.0.0.0/8* à accéder au point de terminaison de machine virtuelle. Remplacez les valeurs en fonction de votre propre configuration. Le nom « SharePoint ACL config » doit être remplacé par le nom convivial que vous attribuez à cette règle.
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "SharePoint ACL config"
3. Pour les règles supplémentaires, répétez l’applet de commande, en remplaçant les valeurs en fonction de votre propre configuration. Veillez à modifier l’ordre des numéros de règle pour qu’il reflète l’ordre dans lequel vous souhaitez que les règles soient appliquées. Le numéro de règle le plus bas est prioritaire sur le numéro supérieur.
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
4. Ensuite, vous pouvez créer un point de terminaison (Add) ou définir la liste ACL d’un point de terminaison existant (Set). Dans cet exemple, nous ajoutons un nouveau point de terminaison de machine virtuelle appelé « web » et nous mettons à jour le point de terminaison de machine virtuelle avec les paramètres ACL.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        | Update-AzureVM
5. Ensuite, combinez les applets de commande et exécutez le script. Pour cet exemple, les applets de commande combinées ressemblent à ce qui suit :
   
        $acl1 = New-AzureAclConfig
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "Sharepoint ACL config"
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        |Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        |Update-AzureVM

### Supprimer une règle de liste de contrôle d’accès réseau qui autorise l’accès depuis un sous-réseau distant
L’exemple ci-dessous illustre une méthode de suppression d’une règle de liste de contrôle d’accès réseau. Pour supprimer une liste de contrôle d’accès réseau avec des règles Permit pour un sous-réseau distant, ouvrez un environnement d’écriture de scripts intégré d’Azure PowerShell. Copiez et collez le script ci-dessous, en configurant le script avec vos propres valeurs, puis exécutez le script.

1. La première étape consiste à obtenir l’objet de liste de contrôle d’accès réseau du point de terminaison de machine virtuelle. Ensuite, vous supprimez la règle ACL. Dans ce cas, nous la supprimons par son ID de règle. Seul l’ID de règle 0 est supprimé de la liste ACL. L’objet ACL n’est pas supprimé du point de terminaison de machine virtuelle.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Get-AzureAclConfig –EndpointName "web" `
        | Set-AzureAclConfig –RemoveRule –ID 0 –ACL $acl1
2. Ensuite, vous devez appliquer l’objet de liste de contrôle d’accès réseau au point de terminaison de machine virtuelle et mettre à jour la machine virtuelle.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Set-AzureEndpoint –ACL $acl1 –Name "web" `
        | Update-AzureVM

### Supprimer une liste de contrôle d’accès réseau d’un point de terminaison de machine virtuelle
Dans certains scénarios, vous voudrez peut-être supprimer un objet de liste de contrôle d’accès réseau d’un point de terminaison de machine virtuelle. Pour ce faire, ouvrez un environnement d’écriture de scripts intégré d’Azure PowerShell. Copiez et collez le script ci-dessous, en configurant le script avec vos propres valeurs, puis exécutez le script.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Remove-AzureAclConfig –EndpointName "web" `
        | Update-AzureVM

## Étapes suivantes
[Qu’est-ce qu’une liste de contrôle d’accès (ACL) réseau ?](virtual-networks-acl.md)

<!---HONumber=AcomDC_0810_2016-->