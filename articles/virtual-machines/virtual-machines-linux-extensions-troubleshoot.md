<properties
   pageTitle="Résolution des défaillances des extensions de machine virtuelle Linux | Microsoft Azure"
   description="En savoir plus sur la résolution des défaillances des extensions de machine virtuelle Azure Linux"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="top-support-issue,azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="support-article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# Dépannage des échecs d’extension de machine virtuelle Azure Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modèle de déploiement classique.

[AZURE.INCLUDE [virtual-machines-common-extensions-troubleshoot](../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## Affichage de l’état de l’extension
Les modèles Azure Resource Manager peuvent être exécutés à partir de l’interface de ligne de commande Azure. Une fois que le modèle est exécuté, l'état de l'extension peut être affiché à partir d'Azure Resource Explorer ou des outils de ligne de commande.

Voici un exemple :

Interface de ligne de commande Azure :

      azure vm get-instance-view


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

### Réexécution de l’extension sur la machine virtuelle

Si vous exécutez des scripts sur la machine virtuelle à l’aide de l’extension de script personnalisé, cela peut générer une erreur indiquant que la machine virtuelle a été créée avec succès mais que le script a échoué. Dans ces conditions, la méthode recommandée pour corriger cette erreur consiste à supprimer l'extension et exécuter le modèle à nouveau. Remarque : à l'avenir, cette fonctionnalité sera améliorée pour supprimer le besoin de désinstaller l'extension.

#### Suppression de l’extension de l’interface de ligne de commande Azure

      azure vm extension set --resource-group "KPRG1" --vm-name "kundanapdemo" --publisher-name "Microsoft.Compute.CustomScriptExtension" --name "myCustomScriptExtension" --version 1.4 --uninstall

Où « publsher-name » correspond au type d'extension à partir de la sortie de « azure vm get-instance-view » et name est le nom de la ressource d'extension à partir du modèle

Une fois que l'extension a été supprimée, le modèle peut être réexécuté pour exécuter les scripts sur la machine virtuelle.

<!---HONumber=AcomDC_0420_2016-->