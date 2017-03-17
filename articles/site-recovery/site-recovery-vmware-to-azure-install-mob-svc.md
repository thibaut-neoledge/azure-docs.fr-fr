---
title: "Installation du service Mobilité (VMware/serveur physique vers Azure) | Microsoft Docs"
description: "Cet article décrit comment installer l’agent du service Mobilité sur vos ordinateurs locaux pour commencer à les protéger."
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 2/20/2017
ms.author: anoopkv
translationtype: Human Translation
ms.sourcegitcommit: 1f4075d6a3ab81bdbde614bbee400bd23f6cea20
ms.openlocfilehash: 1fd481d06f355547fd15200999c4bca3a503ec31
ms.lasthandoff: 02/23/2017

---

# <a name="installing-mobility-service-vmwarephysical-to-azure"></a>Installation du service Mobilité (VMware/serveur physique vers Azure)
Le service Mobilité doit être déployé sur chaque machine (machine virtuelle VMware ou serveur physique) que vous souhaitez répliquer vers Azure. Il enregistre les écritures de données sur la machine et les transmet au serveur de processus. Le service Mobilité peut être déployé sur les serveurs qui nécessitent une protection à l’aide des méthodes suivantes :


1. [Installation du service Mobilité à l’aide d’outils de déploiement de logiciels tels que System Center Configuration Manager](site-recovery-install-mobility-service-using-sccm.md)
2. [Installation du Service Mobilité à l’aide d’Azure Automation et de la Configuration de l’état souhaité](site-recovery-automate-mobility-service-install.md)
3. [Installation du service Mobilité manuellement à l’aide de l’interface utilisateur graphique](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-using-the-graphical-user-interface)
4. [Installation manuelle du service Mobilité à l’aide d’une ligne de commande](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-using-command-line)
5. [Installation du service Mobilité à l’aide de l’installation de transmission (push) à partir d’Azure Site Recovery](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-using-push-install-from-azure-site-recovery)


>[!IMPORTANT]
> Depuis la version 9.7.0.0, sur les machines virtuelles Windows, le programme d’installation du service Mobilité installe également le dernier [agent Azure VM](../virtual-machines/virtual-machines-windows-extensions-features.md#azure-vm-agent). De cette façon, lorsque la machine bascule sur Azure, elle répond à cette condition préalable requise pour utiliser n’importe quelle extension de machine virtuelle.

## <a name="prerequisites"></a>Composants requis
Procédez comme suit avant de commencer à installer manuellement le service Mobilité sur vos serveurs.
1. Ouvrez une session sur votre serveur de configuration et ouvrez une invite de commandes avec des privilèges d’administration.
2. Indiquez le dossier bin et créez un fichier de phrase secrète.

  ```
  cd %ProgramData%\ASR\home\svsystems\bin
  genpassphrase.exe -v > MobSvc.passphrase
  ```
3. Stockez ce fichier dans un emplacement sécurisé, car nous allons l’utiliser lors de l’installation du service Mobilité.
4. Les programmes d’installation du service Mobilité de tous les systèmes d’exploitation pris en charge se trouvent sous le répertoire suivant :     

  `%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository`

#### <a name="mobility-service-installer-to-operating-system-mapping"></a>Correspondance entre le programme d’installation du service Mobilité et le système d’exploitation

| Nom du modèle de fichier de programme d’installation| Système d’exploitation |
|---|--|
|Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2008 R2 (64 bits) SP1</br> Windows Server 2012 (64 bits) </br> Windows Server 2012 R2 (64 bits) |
|Microsoft-ASR\_UA\*RHEL6-64*release.tar.gz| RHEL 6.4, 6.5, 6.6, 6.7, 6.8 (64 bits uniquement) </br> CentOS 6.4, 6.5, 6.6, 6.7. 6.8 (64 bits uniquement) |
|Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3 (64 bits uniquement)|
|Microsoft-ASR_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4, 6.5 (64 bits uniquement)|


## <a name="install-mobility-service-manually-using-the-graphical-user-interface"></a>Installation manuelle du service Mobilité à l’aide de l’interface utilisateur graphique

>[!NOTE]
> L’installation à l’aide de l’interface utilisateur graphique est prise en charge uniquement sur les systèmes d’exploitation Microsoft Windows.

[!INCLUDE [site-recovery-install-mob-svc-gui](../../includes/site-recovery-install-mob-svc-gui.md)]

## <a name="install-mobility-service-manually-using-command-line"></a>Installation manuelle du service Mobilité à l’aide d’une ligne de commande
### <a name="command-line-based-install-on-windows-computers"></a>Installation avec une ligne de commande sur les ordinateurs Windows
[!INCLUDE [site-recovery-install-mob-svc-win-cmd](../../includes/site-recovery-install-mob-svc-win-cmd.md)]

### <a name="command-line-based-install-on-linux-computers"></a>Installation avec une ligne de commande sur les ordinateurs Linux
[!INCLUDE [site-recovery-install-mob-svc-lin-cmd](../../includes/site-recovery-install-mob-svc-lin-cmd.md)]


## <a name="install-mobility-service-using-push-install-from-azure-site-recovery"></a>Installation du service Mobilité à l’aide de l’installation de transmission (push) à partir d’Azure Site Recovery
Pour pouvoir effectuer l’installation de transmission (push) du service Mobilité à l’aide d’Azure Site Recovery, vous devez vérifier que les conditions préalables suivantes sont remplies sur tous les ordinateurs cibles.

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-win](../../includes/site-recovery-prepare-push-install-mob-svc-win.md)]

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-lin](../../includes/site-recovery-prepare-push-install-mob-svc-lin.md)]


## <a name="next-steps"></a>Étapes suivantes
Une fois que le service Mobilité est installé, vous pouvez utiliser le bouton **+Répliquer** dans le portail Azure pour démarrer l’activation de la protection de ces machines virtuelles.

