<properties
   pageTitle="Gérer vos machines virtuelles à l’aide d’Azure PowerShell | Microsoft Azure"
   description="Découvrez les commandes que vous pouvez utiliser pour automatiser les tâches de gestion des machines virtuelles."
   services="virtual-machines-windows"
   documentationCenter="windows"
   authors="singhkay"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

   <tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="03/31/2016"
   ms.author="kasing"/>

# Gérer vos machines virtuelles à l’aide d’Azure PowerShell

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modèle Resource Manager


Il est possible d’automatiser les nombreuses tâches quotidiennes liées à la gestion de vos machines virtuelles en utilisant les applets de commande Azure PowerShell. Cet article donne des exemples de commandes pour réaliser des tâches simples et contient des liens vers des articles indiquant les commandes à utiliser pour des tâches plus complexes.

>[AZURE.NOTE] Si vous n’avez pas installé et configuré Azure PowerShell, vous pouvez obtenir des instructions dans l’article [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).

## Utilisation des exemples de commandes
Vous devrez remplacer une partie du texte des commandes par un texte approprié à votre environnement. Les symboles < and > indiquent le texte à remplacer. Lorsque vous remplacez le texte, supprimez les symboles, mais laissez les guillemets en place.

## Obtenir une machine virtuelle
Il s’agit d’une tâche de base que vous utiliserez souvent. Utilisez-la pour obtenir des informations sur une machine virtuelle, effectuer des tâches sur cette dernière ou pour obtenir un résultat à stocker dans une variable.

Pour obtenir des informations sur la machine virtuelle, exécutez cette commande en remplaçant tous les éléments entre guillemets notamment les caractères < and > :

     Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

Pour stocker le résultat dans une variable $vm, exécutez :

    $vm = Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## Ouvrir une session sur une machine virtuelle Windows

Exécutez ces commandes :

>[AZURE.NOTE] Vous pouvez obtenir le nom du service cloud et de la machine virtuelle à partir de l’affichage de la commande **Get-AzureVM**.
>
	$svcName="<cloud service name>"
	$vmName="<virtual machine name>"
	$localPath="<drive and folder location to store the downloaded RDP file, example: c:\temp >"
	$localFile=$localPath + "" + $vmname + ".rdp"
	Get-AzureRemoteDesktopFile -ServiceName $svcName -Name $vmName -LocalPath $localFile -Launch

## Arrêter une machine virtuelle

Exécutez cette commande :

    Stop-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

>[AZURE.IMPORTANT] Utilisez ce paramètre pour conserver l’adresse IP virtuelle du service cloud s’il s’agit de la dernière machine virtuelle de ce service. <br><br> Si vous utilisez le paramètre StayProvisioned, vous êtes toujours facturé pour cette machine virtuelle.

## Démarrer une machine virtuelle

Exécutez cette commande :

    Start-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## Association d’un disque de données
Cette tâche nécessite de réaliser quelques étapes. Tout d’abord, utilisez l’applet de commande ****Add-AzureDataDisk**** pour ajouter le disque à l’objet $vm. Utilisez ensuite l’applet de commande **Update-AzureVM** pour mettre à jour la configuration de la machine virtuelle.

Vous devez également décider d’associer un nouveau disque ou un disque existant, qui contient des données. Dans le cas d’un nouveau disque, la commande entraîne la création du fichier .vhd et son association.

Pour associer un nouveau disque, exécutez cette commande :

    Add-AzureDataDisk -CreateNew -DiskSizeInGB 128 -DiskLabel "<main>" -LUN <0> -VM <$vm> `
              | Update-AzureVM

Pour associer un disque existant, exécutez cette commande :

    Add-AzureDataDisk -Import -DiskName "<MyExistingDisk>" -LUN <0> `
              | Update-AzureVM

Pour attacher des disques de données à partir d’un fichier .vhd existant dans le stockage d’objets blob, exécutez la commande suivante :

    Add-AzureDataDisk -ImportFrom -MediaLocation `
              "<https://mystorage.blob.core.windows.net/mycontainer/MyExistingDisk.vhd>" `
              -DiskLabel "<main>" -LUN <0> `
              | Update-AzureVM

## Créer une machine virtuelle Windows

Pour créer une nouvelle machine virtuelle Windows dans Azure, consultez [Utilisation d’Azure PowerShell pour créer et préconfigurer des machines virtuelles Windows](virtual-machines-windows-classic-create-powershell.md). Cette rubrique vous guide lors de la création d’un jeu de commandes Azure PowerShell permettant de créer une machine virtuelle Windows pouvant être préconfigurée avec :

- une appartenance au domaine Active Directory ;
- des disques supplémentaires ;
- une appartenance à un jeu d’équilibrage de la charge ;
- une adresse IP statique.

## Créer une machine virtuelle basée sur Linux

Utilisez les instructions contenues dans [Création et préconfiguration d’une machine virtuelle Linux avec Azure Powershell](virtual-machines-linux-classic-createpowershell.md) pour créer une nouvelle machine virtuelle préconfigurée basée sur Linux dans Azure :

- des disques supplémentaires ;
- une appartenance à un jeu d’équilibrage de la charge ;
- une adresse IP statique.

<!---HONumber=AcomDC_0406_2016-->