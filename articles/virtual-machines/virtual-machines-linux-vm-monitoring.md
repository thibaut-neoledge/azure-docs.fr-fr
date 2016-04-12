<properties
   pageTitle="Activation ou désactivation de l’analyse de machine virtuelle Azure"
   description="Décrit comment activer ou désactiver l’analyse de machine virtuelle Azure"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="kmouss"
   manager="drewm"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="02/08/2016"
   ms.author="kmouss"/>
   
# Activation ou désactivation de l’analyse de machine virtuelle Azure

Cette section décrit comment activer ou désactiver l’analyse sur les machine virtuelle exécutées sur Azure. Par défaut, l’analyse est activée sur les machines virtuelles Azure si elles sont déployées à partir du [portail Azure](https://portal.azure.com). Les graphiques de surveillance sont disponibles par défaut avec un délai de 1 minute. Vous pouvez activer ou désactiver l’analyse à l’aide du portail ou de l’interface de ligne de commande Azure pour Mac, Linux et Windows (Azure CLI).

## Activation et désactivation de l’analyse via le portail Azure
 
Vous pouvez activer l’analyse de votre machine virtuelle Azure, qui fournit les données relatives à votre instance par période de 1 minute (des modifications du stockage s’appliquent). Les données de diagnostic détaillées sont ensuite disponibles pour la machine virtuelle dans les graphiques du portail ou via l’API. Par défaut, le portail Azure active l’analyse, mais vous pouvez désactiver cette option comme décrit ci-dessous. Vous pouvez activer l’analyse alors que la machine virtuelle est en cours d’exécution ou en état arrêté.

- Ouvrez le portail Azure à l’adresse **[https://portal.azure.com](https://portal.azure.com)**

- Dans la barre de navigation de gauche, cliquez sur Machines virtuelles.

- Dans la liste Machines virtuelles, sélectionnez une instance en cours d’exécution ou arrêtée. Le panneau Machine virtuelle s’ouvre.

- Cliquez sur « Tous les paramètres ».

- Cliquez sur « Diagnostics ».

- Modifiez le statut sur On (Activé) ou Off (Désactivé). Dans ce panneau, vous pouvez également choisir le niveau des détails d’analyse que vous souhaitez activer pour votre machine virtuelle.

[Azure.Note] Le commutateur Diagnostics activés constitue la valeur par défaut lorsque vous créez une machine virtuelle

![Activation et désactivation de l’analyse via le portail Azure.][1]


## Activation ou désactivation de l’analyse avec l’interface de ligne de commande (CLI) Azure
 
Pour activer l’analyse pour une machine virtuelle Azure.

- Créez un fichier nommé PrivateConfig.json avec le contenu suivant. { «storageAccountName» : le compte de stockage qui reçoit les données, «storageAccountKey» : la clé du compte }
- Exécutez la commande d’interface de ligne de commande Azure suivante.

        azure vm extension set myvm LinuxDiagnostic Microsoft.OSTCExtensions 2.0 --private-config-path PrivateConfig.json

[Azure.Note] Vous pouvez passer de la version 2.0 à une version ultérieure (le cas échéant).

Pour plus d’informations sur la configuration des mesures d’analyse et pour obtenir des exemples, consultez le document - **[Utilisation de l’extension de diagnostic Linux pour surveiller les données de performances et de diagnostic des machines virtuelles Linux](virtual-machines-linux-diagnostic-extension/).

<!--Image references-->
[1]: ./media/virtual-machines-linux-vm-monitoring/portal-enable-disable.png
 

<!---HONumber=AcomDC_0323_2016-->