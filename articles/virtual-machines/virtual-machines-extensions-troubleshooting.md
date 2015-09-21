<properties
   pageTitle="Dépannage des échecs d'extensions de machine virtuelle Azure"
   description="En savoir plus sur le dépannage des échecs d'extension de machine virtuelle Azure"
   services="virtual-machines"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/01/2015"
   ms.author="kundanap"/>

# Dépannage des échecs d'extension de machine virtuelle Azure.

## Vue d’ensemble des modèles Azure Resource Manager

Le modèle Azure Resource Manager vous permet de spécifier de manière déclarative l’infrastructure IaaS Azure dans le langage JSON en définissant les dépendances entre ressources.

Cliquez sur l'article [Création de modèles d'extension](virtual-machines-extensions-authoring-templates.md) pour en savoir plus sur la création de modèles pour l'utilisation d'extensions.

Dans cet article, nous apprendrons à dépanner certains des échecs d'extension de machine virtuelle les plus courants.

## Affichage de l'état de l'extension :
Les modèles Azure Resource Manager peuvent être exécutés à partir d'Azure Powershell ou CLI Azure. Une fois que le modèle est exécuté, l'état de l'extension peut être affiché à partir d'Azure Resource Explorer ou des outils de ligne de commande. Voici quelques exemples :

Interface de ligne de commande Azure :

      azure vm get-instance-view

Azure Powershell :

      Get-AzureVM -ResourceGroupName $RGName -Name $vmName -Status

Voici l'exemple de sortie :

      Extensions:  {
      "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
      "Name": "myCustomScriptExtension",
      "SubStatuses": [
        {
          "Code": "ComponentStatus/StdOut/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
              \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
              test.txt                          \\n\\n",
                      "Time": null
          },
        {
          "Code": "ComponentStatus/StdErr/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "",
          "Time": null
        }
    }
  ]

## Dépannage des échecs d'extension :

### Réexécution de l'extension sur la machine virtuelle :

Si vous exécutez des scripts sur la machine virtuelle à l'aide de l'extension de Script personnalisé, vous pourriez être confronté à une erreur indiquant que la machine virtuelle a été créée avec succès mais que le script a échoué. Dans ces conditions, la méthode recommandée pour corriger cette erreur consiste à supprimer l'extension et exécuter le modèle à nouveau. Remarque : à l'avenir, cette fonctionnalité sera améliorée pour supprimer le besoin de désinstaller l'extension.

#### Suppression de l'extension de l'interface de ligne de commande Azure :

      azure vm extension set --resource-group "KPRG1" --vm-name "kundanapdemo" --publisher-name "Microsoft.Compute.CustomScriptExtension" --name "myCustomScriptExtension" --version 1.4 --uninstall

Où « publsher-name » correspond au type d'extension à partir de la sortie de « azure vm get-instance-view » et name est le nom de la ressource d'extension à partir du modèle

#### Suppression de l'extension d'Azure PowerShell :

    Remove-AzureVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"

Une fois que l'extension a été supprimée, le modèle peut être réexécuté pour exécuter les scripts sur la machine virtuelle.

<!---HONumber=Sept15_HO2-->