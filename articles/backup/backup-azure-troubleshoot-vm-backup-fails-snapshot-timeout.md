---
title: "Échec de sauvegarde des machines virtuelles Azure : impossible de communiquer avec l’agent de machine virtuelle pour obtenir l’état de l’instantané ; la sous-tâche de capture de la machine virtuelle est arrivée à expiration | Microsoft Docs"
description: "Causes des symptômes et solutions possibles pour les échecs de sauvegarde de machines virtuelles Azure liés à l’impossibilité de communiquer avec l’agent de machine virtuelle pour obtenir l’état de l’instantané. Erreur de délai d’expiration de sous-tâche de capture instantanée de machine virtuelle"
services: backup
documentationcenter: 
author: genlin
manager: cfreeman
editor: 
ms.assetid: 4b02ffa4-c48e-45f6-8363-73d536be4639
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: jimpark; markgal;genli
translationtype: Human Translation
ms.sourcegitcommit: 5cd91d74ea09e4fc77437a310f39b769983bd123
ms.openlocfilehash: 005d6b220135302a85495d0840e545c13feb0705


---
# <a name="azure-vm-backup-fails-could-not-communicate-with-the-vm-agent-for-snapshot-status---snapshot-vm-sub-task-timed-out"></a>Échec de sauvegarde des machines virtuelles Azure : impossible de communiquer avec l’agent de machine virtuelle pour obtenir l’état de l’instantané ; la sous-tâche de capture instantanée de la machine virtuelle a expiré.
## <a name="summary"></a>Résumé
Après l’inscription et la planification d’une machine virtuelle pour Azure Backup, le service Azure Backup lance le travail de sauvegarde à l’heure planifiée en communiquant avec l’extension de sauvegarde de la machine virtuelle pour prendre un instantané à un moment donné. Certaines conditions peuvent empêcher la capture d’un instantané, ce qui entraîne un échec de la sauvegarde. Cet article détaille les étapes nécessaires au dépannage des problèmes liés aux échecs de sauvegarde des machines virtuelles Azure relatifs à une erreur de délai d’expiration d’un instantané.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>Symptôme
La machine virtuelle Sauvegarde Microsoft Azure utilisant l’infrastructure IaaS échoue et renvoie le message d’erreur suivant dans les détails de l’erreur de la tâche dans le [portail Azure](https://portal.azure.com/) :

**Impossible de communiquer avec l’agent de machine virtuelle pour obtenir l’état de l’instantané ; la sous-tâche de capture instantanée de la machine virtuelle a expiré.**

## <a name="cause"></a>Cause :
Quatre causes courantes peuvent être à l’origine de cette erreur :

* La machine virtuelle n’a pas accès à Internet.
* L’agent de machine virtuelle Microsoft Azure installé dans la machine virtuelle est obsolète (pour les machines virtuelles Linux).
* L’extension de sauvegarde ne peut être mise à jour ou chargée.
* Impossible de récupérer l’état des instantanés ou de capturer les instantanés.

## <a name="cause-1-the-vm-does-not-have-internet-access"></a>Cause 1 : la machine virtuelle n’a pas accès à Internet.
Selon la configuration requise pour le déploiement, la machine virtuelle n’a pas accès à Internet, ou a des restrictions en place qui empêchent l’accès à l’infrastructure Azure.

L’extension de sauvegarde nécessite le fonctionnement correct de la connectivité vers les adresses IP publiques Azure. Cela est dû à l’envoi de commandes vers un point de terminaison Azure Storage (HTTP URL) pour gérer les instantanés de la machine virtuelle. Si l’extension n’a pas accès à Internet public, la sauvegarde échoue.

### <a name="solution"></a>Solution
Dans ce scénario, utilisez l’une des méthodes suivantes pour résoudre le problème :

* Mettez sur liste approuvée les plages IP du centre de données Azure.
* Créez un chemin d'accès pour le trafic HTTP

### <a name="to-whitelist-the-azure-datacenter-ip-ranges"></a>Mettre sur liste blanche les plages IP du centre de données Azure.
1. Obtenez la liste des [adresses IP de centres de données Azure](https://www.microsoft.com/download/details.aspx?id=41653) à mettre sur liste blanche.
2. Débloquez les adresses IP à l’aide de l’applet de commande New-NetRoute. Exécutez cette applet de commande dans la machine virtuelle Azure, dans une fenêtre PowerShell avec élévation de privilèges (exécution en tant qu’administrateur).
3. Ajoutez des règles à un groupe de sécurité réseau (le cas échéant) pour autoriser l’accès aux adresses IP.

### <a name="to-create-a-path-for-http-traffic-to-flow"></a>Créer un chemin d’accès pour le trafic HTTP
1. Si des restrictions réseau ont été mises en place (un groupe de sécurité réseau, par exemple), déployez un serveur proxy HTTP pour acheminer le trafic.
2. Ajoutez des règles à un groupe de sécurité réseau (le cas échéant) pour autoriser l’accès à Internet à partir du proxy HTTP.

Apprenez l’ [utilisation d’un proxy HTTP pour les sauvegardes de machine virtuelle](backup-azure-vms-prepare.md#using-an-http-proxy-for-vm-backups).

## <a name="cause-2-the-microsoft-azure-vm-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Cause 2 : l’agent de machine virtuelle Microsoft Azure installé dans la machine virtuelle est obsolète (pour les machines virtuelles Linux).
### <a name="solution"></a>Solution
La plupart des échecs des machines virtuelles Linux liés aux agents ou aux extensions sont causés par des problèmes qui affectent un ancien agent de machine virtuelle. En règle générale, les premières étapes vers la résolution de ce problème sont les suivantes :

1. Suivez les instructions dans la rubrique [Mise à jour d’un agent de machine virtuelle Linux](../virtual-machines/virtual-machines-linux-update-agent.md).
Nous **recommandons vivement** la mise à jour de l’agent uniquement par le biais de référentiel de distribution. Nous ne recommandons pas de télécharger le code de l’agent à partir de github directement et d’effectuer la mise à jour. Si le dernier agent n’est pas disponible pour la distribution, contactez le support de distribution pour obtenir des instructions sur l’installation de l’agent le plus récent. Vous pouvez vérifier les informations sur [l’agent Windows Azure Linux](https://github.com/Azure/WALinuxAgent/releases) le plus récent dans le référentiel github. 
2. Assurez-vous que l’agent Azure s’exécute sur la machine virtuelle. Pour ce faire, exécutez la commande suivante : ```ps -e```
   
    Si ce n’est pas le cas, utilisez les commandes suivantes pour le redémarrer.
   
    Pour Ubuntu : ```service walinuxagent start```
   
    Pour les autres distributions :     ```service waagent start
   ```
3. [Configure the auto restart agent](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Run a new test backup. If the failures persist, please collect logs from the following folders for further debugging.
   
    We require the following logs from the customer’s VM:
   
   * /var/lib/waagent/*.xml
   * /var/log/waagent.log
   * /var/log/azure/*

If we require verbose logging for waagent, follow these steps to enable this:

1. In the /etc/waagent.conf file, locate the following line:
   
    Enable verbose logging (y|n)
2. Change the **Logs.Verbose** value from n to y.
3. Save the change, and then restart waagent by following the previous steps in this section.

## Cause 3: The backup extension fails to update or load
If extensions cannot be loaded, then Backup fails because a snapshot cannot be taken.

### Solution
For Windows guests:

1. Verify that the iaasvmprovider service is enabled and has a startup type of automatic.
2. If this is not the configuration, enable the service to determine whether the next backup succeeds.

For Linux guests:

The latest version of VMSnapshot Linux (extension used by backup) is 1.0.91.0

If the backup extension still fails to update or load, you can force the VMSnapshot extension to be reloaded by uninstalling the extension. The next backup attempt will reload the extension.

### To uninstall the extension
1. Go to the [Azure portal](https://portal.azure.com/).
2. Locate the particular VM that has backup problems.
3. Click **Settings**.
4. Click **Extensions**.
5. Click **Vmsnapshot Extension**.
6. Click **uninstall**.

This will cause the extension to be reinstalled during the next backup.

## Cause 4: The snapshots status cannot be retrieved or the snapshots cannot be taken
VM backup relies on issuing snapshot command to underlying storage. The backup can fail because it has no access to storage or because of a delay in snapshot task execution.

### Solution
The following conditions can cause snapshot task failure:

| Cause | Solution |
| --- | --- |
| VMs that have Microsoft SQL Server Backup configured. By default, VM Backup runs a VSS Full backup on Windows VMs. On VMs that are running SQL Server-based servers and on which SQL Server Backup is configured, snapshot execution delays may occur. |Set following registry key if you are experiencing backup failures because of snapshot issues.<br><br>[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT] "USEVSSCOPYBACKUP"="TRUE" |
| VM status is reported incorrectly because the VM is shut down in RDP. If you shut down the virtual machine in RDP, check the portal to determine whether that VM status is reflected correctly. |If it’s not, shut down the VM in the portal by using the ”Shutdown” option in the VM dashboard. |
| Many VMs from the same cloud service are configured to back up at the same time. |It’s a best practice to spread out the VMs from the same cloud service to have different backup schedules. |
| The VM is running at high CPU or memory usage. |If the VM is running at high CPU usage (more than 90 percent) or high memory usage, the snapshot task is queued and delayed and eventually times out. In this situation, try on-demand backup. |
| The VM cannot get host/fabric address from DHCP. |DHCP must be enabled inside the guest for IaaS VM Backup to work.  If the VM cannot get host/fabric address from DHCP response 245, then it cannot download ir run any extensions. If you need a static private IP, you should configure it through the platform. The DHCP option inside the VM should be left enabled. View more information about [Setting a Static Internal Private IP](../virtual-network/virtual-networks-reserved-private-ip.md). |




<!--HONumber=Jan17_HO1-->


