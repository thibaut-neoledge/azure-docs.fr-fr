---
title: Extension de machine virtuelle OMS Azure pour Linux | Microsoft Docs
description: "Déployez l’Agent OMS sur une machine virtuelle Linux avec une extension de machine virtuelle."
services: virtual-machines-linux
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c7bbf210-7d71-4a37-ba47-9c74567a9ea6
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 9f27890e52cb7a9a0d46f2bb84bfe92f7c6fff37
ms.openlocfilehash: 9864314956d79317785c1c2a4bc87621bc6a3e2d


---
# <a name="oms-virtual-machine-extension-for-linux"></a>Extension de machine virtuelle OMS pour Linux

## <a name="overview"></a>Vue d'ensemble

Operations Management Suite (OMS) fournit des fonctionnalités de surveillance, d’alerte et de correction des alertes sur le ressources cloud et locales. L’extension de machine virtuelle de l’agent OMS pour Linux est publiée et prise en charge par Microsoft. L’extension installe l’agent OMS sur les machines virtuelles Azure et inscrit les machines virtuelles dans un espace de travail OMS existant. Ce document présente les plateformes, configurations et options de déploiement prises en charge pour l’extension de machine virtuelle OMS pour Linux.

Pour des informations générales sur les extensions de machines virtuelles Azure, consultez la page [Présentation des extensions de machines virtuelles](./virtual-machines-linux-extensions-features.md).

Pour plus d’informations sur Operations Management Suite, consultez [Présentation d’Operations Management Suite](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite).

## <a name="prerequisites"></a>Composants requis

### <a name="operating-system"></a>Système d’exploitation

L’extension de l’agent OMS peut être exécutée sur ces distributions de Linux.

| Distribution | Version |
|---|---|
| CentOS Linux | 5, 6 et 7 |
| Oracle Linux | 5, 6 et 7 |
| Red Hat Enterprise Linux Server | 5, 6 et 7 |
| Debian GNU/Linux | 6, 7 et 8 |
| Ubuntu | 12.04 LTS, 14.04 LTS, 15.04 |
| SUSE Linux Enterprise Server | 11 et 12 |

### <a name="connectivity"></a>Connectivité

L’extension de l’agent OMS pour Linux nécessite que la machine virtuelle cible soit connectée à Internet. 

## <a name="extension-configuration"></a>Configuration de l’extension

L’extension de machine virtuelle de l’agent OMS pour Linux requiert l’ID et la clé de l’espace de travail OMS cible. La clé de l’espace de travail devant être traitée comme une donnée sensible, elle est stockée dans une configuration protégée. Les données de configuration protégées de l’extension de machine virtuelle Azure sont chiffrées et déchiffrées uniquement sur la machine virtuelle cible. Les configurations publiques et privées sont spécifiées au moment du déploiement, ce qui est détaillé dans les sections suivantes de ce document.

### <a name="public-configuration"></a>Configuration publique

Schéma pour la configuration publique :

- workspaceId : (obligatoire, chaîne) l’ID de l’espace de travail OMS dans lequel intégrer la machine virtuelle.

```json
{
  "workspaceId": "myWorkspaceId"
}
```

### <a name="private-configuration"></a>Configuration privée

Schéma pour la configuration publique :

- workspaceKey : (obligatoire, chaîne) la clé partagée principale/secondaire de l’espace de travail.

```json
{
  "workspaceKey": "myWorkSpaceKey"
}
```

## <a name="template-deployment"></a>Déploiement de modèle

Les extensions de machines virtuelles Azure peuvent être déployées avec des modèles Azure Resource Manager. Les modèles sont idéaux lorsque vous déployez une ou plusieurs machines virtuelles nécessitant une configuration post-déploiement, comme l’intégration dans OMS. Vous trouverez un exemple de modèle Resource Manager qui inclut l’extension de machine virtuelle d’agent OMS dans la [galerie de démarrage rapide Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm). 

Cet exemple peut être déployé à partir de ce document à l’aide du bouton suivant :

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-oms-extension-ubuntu-vm%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

Le JSON utilisé pour déployer l’extension de machine virtuelle Agent OMS est semblable à l’exemple suivant :

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "<extension-deployment-name>",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.0",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

## <a name="azure-cli-deployment"></a>Déploiement de l’interface de ligne de commande Azure

Vous pouvez utiliser l’interface de ligne de commande Azure pour déployer l’extension de machine virtuelle d’agent OMS sur une machine virtuelle existante. Avant de déployer l’extension d’agent OMS, créez un fichier public.json et un fichier protected.json. Le schéma pour ces fichiers est détaillé plus haut dans ce document.

```azurecli
azure vm extension set <resource-group> <vm-name> \
  OmsAgentForLinux Microsoft.EnterpriseCloud.Monitoring 1.0 \
  --public-config-path public.json  \
  --private-config-path protected.json
```

## <a name="troubleshoot-and-support"></a>Dépannage et support technique

### <a name="troubleshoot"></a>Résolution des problèmes

Vous pouvez récupérer les données sur l’état des déploiements d’extension à partir du portail Azure et à l’aide de l’interface de ligne de commande Azure. Pour afficher l’état du déploiement des extensions pour une machine virtuelle donnée, exécutez la commande suivante à l’aide de l’interface de ligne de commande Azure.

```azurecli
azure vm extension get myResourceGroup myVM
```

La sortie de l’exécution de l’extension est enregistrée dans le fichier suivant :

`
/opt/microsoft/omsagent/bin/stdout
`

### <a name="support"></a>Support

Si vous avez besoin d’une aide supplémentaire à quelque étape que ce soit dans cet article, vous pouvez contacter les experts Azure sur les [forums MSDN Azure et Stack Overflow](https://azure.microsoft.com/en-us/support/forums/). Vous pouvez également signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/en-us/support/options/) , puis cliquez sur Obtenir un support. Pour plus d’informations sur l’utilisation du support Azure, lisez le [FAQ du support Microsoft Azure](https://azure.microsoft.com/en-us/support/faq/).



<!--HONumber=Dec16_HO1-->


