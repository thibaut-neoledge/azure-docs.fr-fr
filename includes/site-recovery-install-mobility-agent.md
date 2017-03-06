---
title: 
description: 
services: site-recovery
documentationcenter: 
author: 
manager: 
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 
ms.author: 
translationtype: Human Translation
ms.sourcegitcommit: 4871e5959483058aa957e3373ddc5121b398e1d9
ms.openlocfilehash: 1d6d595bf788fc3fdba0bbc98887614e17c791dc
ms.lasthandoff: 02/17/2017


---



### <a name="install-the-mobility-service"></a>Installer le service Mobilité
La première étape de l’activation de la protection des machines virtuelles et des serveurs physiques consiste à installer le service Mobilité. Vous pouvez accomplir cette opération de différentes manières :

* **Installation Push à partir du serveur de processus**: lorsque vous activez la réplication sur une machine, transmettez et installez le composant du service Mobilité à partir du serveur de processus. 
*Remarque* : L’installation Push n’a pas lieu si les machines exécutent déjà une version actualisée du composant.
* **Installation Push d’entreprise**: installez automatiquement le composant à l’aide de votre processus Push d’entreprise, comme WSUS ou System Center Configuration Manager ou [Azure Automation et Configuration d’état souhaité](site-recovery-automate-mobility-service-install.md). Au préalable, vous devez avoir configuré le serveur de configuration.
* **Installation manuelle**: installez manuellement le composant sur chaque machine à répliquer. Au préalable, vous devez avoir configuré le serveur de configuration.

#### <a name="prepare-for-automatic-push-on-windows-machines"></a>Préparez une opération Push automatique sur les machines Windows
Voici comment préparer les ordinateurs Windows afin que le service Mobilité puisse être installé automatiquement par le serveur de traitement.

1. Créez un compte pouvant être utilisé par le serveur de traitement pour accéder à la machine. Le compte doit disposer de privilèges d’administrateur (local ou domaine) et il est utilisé uniquement pour l’installation Push.

   > [!NOTE]
   > Si vous n’utilisez pas un compte de domaine, vous devez désactiver le contrôle d’accès utilisateur distant sur la machine locale. Pour ce faire, dans le registre situé sous HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System, ajoutez l’entrée de Registre DWORD LocalAccountTokenFilterPolicy avec une valeur de 1. Pour ajouter l’entrée de Registre à partir d’une interface de ligne de commande, tapez **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**.
   >
   >
2. Sur le pare-feu Windows de la machine à protéger, sélectionnez **Autoriser une application ou une fonctionnalité à franchir le pare-feu**. Activez **Partage de fichiers et d’imprimantes** et **Windows Management Instrumentation**. Pour les machines qui appartiennent à un domaine, vous pouvez configurer les paramètres de pare-feu avec un objet de stratégie de groupe.

   ![Paramètres du pare-feu](./media/site-recovery-vmware-to-azure/mobility1.png)
3. Ajoutez le compte que vous venez de créer :

   * Ouvrez **cspsconfigtool**. Il est disponible sous forme de raccourci sur le bureau et situé dans le dossier [EMPLACEMENT D’INSTALLATION]\home\svsystems\bin.
   * Dans l’onglet **Gérer les comptes**, cliquez sur **Ajouter un compte**.
   * Ajoutez le compte que vous venez de créer. Après avoir ajouté le compte, vous devez fournir les informations d’identification lorsque vous activez la réplication d’une machine.

#### <a name="prepare-for-automatic-push-on-linux-servers"></a>Préparez une opération Push automatique sur les serveurs Linux
1. Vérifiez que la machine Linux à protéger est prise en charge, comme indiqué dans la section [Configuration requise pour les machines protégées](#protected-machine-prerequisites). Vérifiez qu’il existe une connectivité réseau entre la machine Linux et le serveur de processus.
2. Créez un compte pouvant être utilisé par le serveur de traitement pour accéder à la machine. Le compte doit être un utilisateur racine sur le serveur Linux source et être utilisé uniquement pour l’installation Push.

   * Ouvrez **cspsconfigtool**. Il est disponible sous forme de raccourci sur le bureau et situé dans le dossier [EMPLACEMENT D’INSTALLATION]\home\svsystems\bin.
   * Dans l’onglet **Gérer les comptes**, cliquez sur **Ajouter un compte**.
   * Ajoutez le compte que vous venez de créer. Après avoir ajouté le compte, vous devez fournir les informations d’identification lorsque vous activez la réplication d’une machine.
3. Vérifiez que les fichiers /etc/hosts sur le serveur Linux source contiennent des entrées qui mappent le nom d’hôte local sur les adresses IP associées à toutes les cartes réseau.
4. Installez les packages openssh, openssh-server et openssl les plus récents sur la machine à répliquer.
5. Vérifiez que SSH est activé et exécuté sur le port 22.
6. Activez le sous-système SFTP et l'authentification par mot de passe dans le fichier sshd_config comme suit :

   * Connectez-vous en tant qu’utilisateur racine.
   * Dans le fichier /etc/ssh/sshd_config, recherchez la ligne commençant par **PasswordAuthentication**.
   * Supprimez les commentaires de la ligne et remplacez la valeur **no** par **yes**.
   * Recherchez la ligne commençant par **Subsystem** et supprimez la mise en commentaire de la ligne.

     ![Linux](./media/site-recovery-vmware-to-azure/mobility2.png)

### <a name="install-the-mobility-service-manually"></a>Installer le service Mobilité manuellement
Sur le serveur de configuration, les programmes d’installation sont disponibles à l’emplacement **C:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**.

| Système d’exploitation source | Fichier d’installation du service Mobilité |
| --- | --- |
| Windows Server (64 bits uniquement) |Microsoft-ASR_UA_9.*.0.0_Windows_* release.exe |
| CentOS 6.4, 6.5, 6.6 (64 bits uniquement) |Microsoft-ASR_UA_9.*.0.0_RHEL6-64_*release.tar.gz |
| SUSE Linux Enterprise Server 11 SP3 (64 bits uniquement) |Microsoft-ASR_UA_9.*.0.0_SLES11-SP3-64_*release.tar.gz |
| Oracle Enterprise Linux 6.4, 6.5 (64 bits uniquement) |Microsoft-ASR_UA_9.*.0.0_OL6-64_*release.tar.gz |

#### <a name="install-mobility-service-on-a-windows-server"></a>Installer un service Mobilité sur un serveur Windows
1. Téléchargez et exécutez le programme d’installation approprié.
2. Dans **Avant de commencer**, sélectionnez **Service Mobilité**.

    ![Service Mobilité](./media/site-recovery-vmware-to-azure/mobility3.png)
3. Dans les **Détails du serveur de configuration**, spécifiez l’adresse IP du serveur de configuration et la phrase secrète qui a été générée lors de l’exécution du programme d’installation unifiée. Pour retrouver la phrase secrète, exécutez **<SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe –v** sur le serveur de configuration.

    ![Service Mobilité](./media/site-recovery-vmware-to-azure/mobility6.png)
4. Dans la zone **Emplacement de l’installation**, conservez le paramètre par défaut, puis cliquez sur **Suivant** pour lancer l’installation.
5. Dans la zone **Progression de l’installation**, surveillez l’installation et redémarrez la machine si vous y êtes invité. Après l’installation du service, la mise à jour de l’état sur le portail peut prendre environ 15 minutes.

#### <a name="install-mobility-service-on-a-windows-server-using-the-command-prompt"></a>Installer un service Mobilité sur un serveur Windows à l’aide de l’invite de commandes
1. Copiez le programme d’installation dans un dossier local (par exemple C:\Temp) sur le serveur que vous souhaitez protéger. Le programme d’installation est disponible sur le serveur de configuration sous **[Emplacement d’installation]\home\svsystems\pushinstallsvc\repository**. Le package correspondant aux systèmes d’exploitation Windows a un nom semblable à Microsoft-ASR_UA_9.3.0.0_Windows_GA_17thAug2016_release.exe.
2. Renommez ce fichier comme suit : MobilitySvcInstaller.exe
3. Exécutez la commande suivante pour extraire le programme d’installation MSI :

    ``C:\> cd C:\Tempww
    ``C:\Temp> MobilitySvcInstaller.exe /q /xC:\Temp\Extracted``
    ``C:\Temp> cd Extracted``
    ``C:\Temp\Extracted> UnifiedAgent.exe /Role « Agent » /CSEndpoint « IP Address of Configuration Server » /PassphraseFilePath <Full path to the passphrase file>``

##### <a name="full-command-line-syntax"></a>Syntaxe de ligne de commande complète
    UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Installation Directory>] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>] [/LogFilePath <Log File Path>]<br/>

**Paramètres**

* **/Role :** Obligatoire. Indique si le service Mobilité doit être installé. Valeurs d’entrée Agent|MasterTarget
* **/InstallLocation :** Obligatoire. Spécifie l’emplacement d’installation du service.
* **/PassphraseFilePath :** Obligatoire. Phrase secrète du serveur de configuration.
* **/LogFilePath :** Obligatoire. Emplacement où les fichiers journaux d’installation doivent être créés.

#### <a name="uninstall-the-mobility-service-manually"></a>Désinstaller le service de mobilité manuellement
Le service de mobilité peut être désinstallé à l’aide de l’option Ajout/Suppression de programmes dans le panneau de configuration ou à l’aide de l’instruction de ligne de commande suivante : MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1}

#### <a name="install-the-mobility-service-on-a-linux-server"></a>Installer le service de mobilité sur un serveur Linux
1. Copiez l’archive tar appropriée en fonction du tableau ci-dessus vers la machine Linux à répliquer.
2. Ouvrez un interpréteur de commandes et extrayez l’archive tar compressée dans un emplacement local en exécutant la commande suivante : `tar -xvzf Microsoft-ASR_UA_8.5.0.0*`
3. Créez un fichier passphrase.txt dans le répertoire local. Vous y extrairez les contenus de l’archive tar. Pour cela, copiez la phrase secrète à partir de C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase sur le serveur de configuration et enregistrez-la dans le fichier passphrase.txt, en exécutant *`echo <passphrase> >passphrase.txt`* dans l’interpréteur de commandes.
4. Installez le service Mobilité en exécutant *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*.
5. Spécifiez l’adresse IP interne du serveur de configuration et assurez-vous que le port 443 est sélectionné. Après l’installation du service, la mise à jour de l’état sur le portail peut prendre environ 15 minutes.

**Vous pouvez également procéder à l’installation à partir de la ligne de commande** :

Copiez la phrase secrète de C:\Program Files (x86)\InMage Systems\private\connection sur le serveur de configuration et enregistrez-le dans le fichier passphrase.txt sur le serveur de configuration. Exécutez ensuite ces commandes. Dans notre exemple, l’adresse IP du serveur de configuration est 104.40.75.37 et le port HTTPS doit être 443 :


Pour installer sur un serveur de production :

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

Pour procéder à l’installation sur le serveur cible :

    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt




