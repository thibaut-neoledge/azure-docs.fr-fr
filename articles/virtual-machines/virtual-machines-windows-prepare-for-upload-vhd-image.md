---
title: "Préparer un disque dur virtuel Windows à charger sur Azure | Microsoft Docs"
description: "Préparer un disque dur virtuel Windows ou VHDX avant de charger sur Azure"
services: virtual-machines-windows
documentationcenter: 
author: genlin
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 1/11/2017
ms.author: glimoli;genli
translationtype: Human Translation
ms.sourcegitcommit: 5d8274f61c3de178c9d418adb9be1efe0fe62bc1
ms.openlocfilehash: 6fbfc74cb1cce744b51345c0732b40b95be21c94


---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Préparer un disque dur virtuel Windows à charger sur Azure
Pour charger une machine virtuelle Windows locale sur Azure, vous devez préparer le disque dur virtuel (VHD ou VHDX). Azure prend en charge uniquement les machines virtuelles de génération 1 au format de fichier de disque dur virtuel (VHD) avec une taille fixe. La taille maximale autorisée pour le disque dur virtuel s’élève à 1 023 Go. Vous pouvez convertir une machine virtuelle de génération 1 du format VHDX au format VHD et d'une taille à expansion dynamique en taille fixe. Mais vous ne pouvez pas modifier la génération d’une machine virtuelle. Pour plus d’informations, consultez [Dois-je créer une machine virtuelle de génération 1 ou 2 dans Hyper-V ?](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)

## <a name="convert-the-virtual-disk-to-vhd-and-fixed-size-disk"></a>Convertir le disque virtuel en disque VHD de taille fixe 
Pour convertir votre disque virtuel au format exigé par Azure, utilisez une des méthodes décrites dans cette section. Sauvegardez la machine virtuelle avant d’exécuter le processus de conversion de disque virtuel puis assurez-vous que le disque dur virtuel Windows fonctionne correctement sur le serveur local. Résolvez toute erreur dans la machine virtuelle elle-même avant de tenter une conversion ou un chargement sur Azure.

Après avoir converti le disque, créez une machine virtuelle qui utilise le disque converti. Démarrez et connectez-vous à la machine virtuelle pour terminer la préparation de la machine virtuelle en vue du chargement.

### <a name="convert-disk-using-hyper-v-manager"></a>Convertir un disque à l’aide du Gestionnaire Hyper-V
1. Ouvrez le Gestionnaire Hyper-V et sélectionnez votre ordinateur local sur la gauche. Dans le menu du haut, cliquez sur **Action** > **Modifier le disque**.
2. Dans l’écran **Rechercher un disque dur virtuel** , recherchez et sélectionnez le disque virtuel.
3. Sur l'écran **Choisir une action**, sélectionnez **Convertir** puis **suivant**.
4. Pour convertir un disque VHDX, sélectionnez **VHD**, puis cliquez sur **Suivant**.
5. Pour convertir un disque à expansion dynamique, sélectionnez **Taille fixe**, puis cliquez sur **Suivant**
6. Recherchez et sélectionnez le chemin d'enregistrement du nouveau fichier de disque dur virtuel.
7. Cliquez sur **Terminer** pour fermer la fenêtre.

### <a name="convert-disk-using-powershell"></a>Convertir un disque à l’aide de PowerShell
Vous pouvez convertir un disque virtuel à l’aide de l’applet de commande [Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx) dans Windows PowerShell. Sélectionnez **Exécuter en tant qu’administrateur** lorsque vous démarrez PowerShell. L’exemple suivant montre comment convertir un disque VHDX en disque VHD et un disque à expansion dynamique en disque à taille fixe :

```powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```
Remplacez les valeurs -Path par le chemin d’accès au disque dur virtuel que vous souhaitez convertir, et -DestinationPath par le nouveau chemin d’accès et le nom du disque converti.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Convertir à partir du format de disque VMDK VMware
Si vous disposez d’une image de machine virtuelle Windows au [format de fichier VMDK](https://en.wikipedia.org/wiki/VMDK), convertissez-la au format VHD à l’aide de [Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497). Pour plus d'informations, consultez le blog [How to Convert a VMWare VMDK to Hyper-V VHD](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx) (en anglais).

## <a name="set-windows-configurations-for-azure"></a>Définir les configurations Windows pour Azure

Sur la machine virtuelle que vous souhaitez charger sur Azure, exécutez toutes les commandes suivantes à partir de la fenêtre d’invite de commandes avec [des privilèges d’administrateur](https://technet.microsoft.com/library/cc947813.aspx).

1. Supprimez tout itinéraire statique persistant de la table de routage :
   
   * Pour afficher la table d’itinéraires, exécutez `route print` à partir de la fenêtre d’invite de commandes.
   * Vérifiez les sections **Persistence Routes** . S’il existe un itinéraire persistant, utilisez [route delete](https://technet.microsoft.com/library/cc739598.apx) pour le supprimer.
2. Supprimez le proxy WinHTTP :
   
    ```CMD
    netsh winhttp reset proxy
    ```
3. Définissez la stratégie SAN pour les disques sur [Onlineall](https://technet.microsoft.com/library/gg252636.aspx). 
   
    ```CMD
    diskpart 
    san policy=onlineall
    exit
    ```
    

4. Réglez l’heure UTC (Temps universel coordonné) pour Windows et définissez le type de démarrage du service de Temps Windows (w32time) sur **automatique**:
   
    ```CMD
    REG ADD HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation /v RealTimeIsUniversal /t REG_DWORD /d 1
    ```
    ```CMD
    sc config w32time start= auto
    ```

## <a name="set-services-startup-to-windows-default-values"></a>Appliquer les valeurs Windows par défaut au démarrage des services
Vérifiez que chacun des services Windows suivants est défini sur les **valeurs par défaut Windows**. Pour réinitialiser les paramètres de démarrage, exécutez les commandes suivantes :
   
```CMD
sc config bfe start= auto
   
sc config dcomlaunch start= auto
   
sc config dhcp start= auto
   
sc config dnscache start= auto
   
sc config IKEEXT start= auto
   
sc config iphlpsvc start= auto
   
sc config PolicyAgent start= demand
   
sc config LSM start= auto
   
sc config netlogon start= demand
   
sc config netman start= demand
   
sc config NcaSvc start= demand
   
sc config netprofm start= demand
   
sc config NlaSvc start= auto
   
sc config nsi start= auto
   
sc config RpcSs start= auto
   
sc config RpcEptMapper start= auto
   
sc config termService start= demand
   
sc config MpsSvc start= auto
   
sc config WinHttpAutoProxySvc start= demand
   
sc config LanmanWorkstation start= auto
   
sc config RemoteRegistry start= auto
```

## <a name="update-remote-desktop-registry-settings"></a>Mettre à jour les paramètres de registre du Bureau à distance
1. Si des certificats auto-signés sont liés à l’écouteur RDP (Remote Desktop Protocol), supprimez-les :
   
    ```CMD
    REG DELETE "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp\SSLCertificateSHA1Hash”
    ```
   
    Pour plus d’informations sur la configuration des certificats pour l’écouteur RDP, consultez [Listener Certificate Configurations in Windows Server ](https://blogs.technet.microsoft.com/askperf/2014/05/28/listener-certificate-configurations-in-windows-server-2012-2012-r2/)
2. Configurez les valeurs [KeepAlive](https://technet.microsoft.com/library/cc957549.aspx) pour le service RDP :
   
    ```CMD
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v KeepAliveEnable /t REG_DWORD  /d 1 /f
    ```
    ```CMD
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v KeepAliveInterval /t REG_DWORD  /d 1 /f
    ```
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v KeepAliveTimeout /t REG_DWORD /d 1 /f
    ```
3. Configurez le mode d’authentification pour le service RDP :
   
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD  /d 1 /f
   ```
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD  /d 1 /f
   ```
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD  /d 1 /f
    ```
4. Activez le service RDP en ajoutant les sous-clés suivantes au Registre :
   
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD  /d 0 /f
    ```

## <a name="configure-windows-firewall-rules"></a>Configurer les règles du Pare-feu Windows
1. Exécutez la commande suivante dans PowerShell pour autoriser WinRM sur les trois profils de pare-feu (domaine, privé et public) et pour activer le service à distance PowerShell :
   
   ```powershell
   Enable-PSRemoting -force
   ```
2. Exécution les commandes suivantes dans la fenêtre d'invite de commandes pour vérifier que les règles de pare-feu du système d’exploitation invité suivantes sont en place :
   
   * Trafic entrant
   
   ```CMD
   netsh advfirewall firewall set rule dir=in name="File and Printer Sharing (Echo Request - ICMPv4-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (LLMNR-UDP-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (NB-Datagram-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (NB-Name-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (Pub-WSD-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (SSDP-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (UPnP-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (WSD EventsSecure-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
   ```
   
   * Trafic entrant et sortant
   
   ```CMD
   netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
   
   netsh advfirewall firewall set rule group="Core Networking" new enable=yes
   ```
   
   * Règle de trafic sortant
   
   ```CMD
   netsh advfirewall firewall set rule dir=out name="Network Discovery (LLMNR-UDP-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (NB-Datagram-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (NB-Name-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (Pub-WSD-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (SSDP-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (UPnPHost-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (UPnP-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD Events-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD EventsSecure-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD-Out)" new enable=yes
   ```

## <a name="verify-vm-is-healthy-secure-and-accessible-with-rdp"></a>Vérifier que la machine virtuelle est saine, sécurisée et accessible via une connexion RDP 
1. Dans la fenêtre d'invite de commandes, exécutez `winmgmt /verifyrepository` pour confirmer que le référentiel Windows Management Instrumentation (WMI) est cohérent. Si le référentiel est endommagé, consultez le billet de blog [WMI: Repository Corruption, or Not?](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not) (en anglais)
2. Définissez les paramètres de données de configuration de démarrage (BCD) :
   
   ```CMD
   bcdedit /set {bootmgr} integrityservices enable
   
   bcdedit /set {default} device partition=C:
   
   bcdedit /set {default} integrityservices enable
   
   bcdedit /set {default} recoveryenabled Off
   
   bcdedit /set {default} osdevice partition=C:
   
   bcdedit /set {default} bootstatuspolicy IgnoreAllFailures
   ```
3. Supprimez tous les filtres de TDI (Transport Driver Interface) supplémentaires, tels que les logiciels qui analysent les paquets TCP.
4. Pour vérifier que le disque est sain et cohérent, exécutez la commande `CHKDSK /f` dans la fenêtre d'invite de commandes. Tapez « O » pour planifier la vérification et redémarrer la machine virtuelle.
5. Désinstallez tous les autres logiciels et pilotes tiers liés aux composants physiques ou toute autre technologie de virtualisation.
6. Vérifiez qu’aucune application tierce n’utilise le port 3389. Ce port est utilisé pour le service RDP dans Azure. Vous pouvez exécuter `netstat -anob` dans la fenêtre d’invite de commandes pour afficher les ports utilisés par les applications.
7. Si le disque dur virtuel Windows à charger est un contrôleur de domaine, suivez [ces étapes supplémentaires](https://support.microsoft.com/kb/2904015) pour le préparer.
8. Redémarrez la machine virtuelle pour vous assurer que Windows est toujours sain et qu’il est accessible par le biais de la connexion RDP.
9. Réinitialisez le mot de passe administrateur local actuel et vérifiez que vous pouvez utiliser ce compte pour vous connecter à Windows par le biais de la connexion RDP. Cette autorisation d’accès est contrôlée par l’objet de stratégie de groupe « Autoriser l’ouverture de session par les services Bureau à distance ». Vous pouvez afficher cet objet dans l'Éditeur de stratégie de groupe locale sous « Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment ».

## <a name="install-windows-updates"></a>Installer les mises à jour Windows
Installez les dernières mises à jour pour Windows. Si ce n’est pas possible, vérifiez que les mises à jour suivantes sont installées :
   
   * [KB3137061](https://support.microsoft.com/kb/3137061) Les machines virtuelles Microsoft Azure ne récupèrent pas d’une panne de réseau et des problèmes de corruption de données se produisent
   * [KB3115224](https://support.microsoft.com/kb/3115224) Améliorations de la fiabilité des machines virtuelles qui s’exécutent sur un hôte Windows Server 2012 R2 ou Windows Server 2012
   * [KB3140410](https://support.microsoft.com/kb/3140410) MS16-031 : Mise à jour de sécurité pour Microsoft Windows afin de résoudre un problème d’élévation de privilèges : 8 mars 2016
   * [KB3063075](https://support.microsoft.com/kb/3063075) De nombreux événements ID 129 sont enregistrés quand vous exécutez une machine virtuelle Windows Server 2012 R2 dans Microsoft Azure
   * [KB3137061](https://support.microsoft.com/kb/3137061) Les machines virtuelles Microsoft Azure ne récupèrent pas d’une panne de réseau et des problèmes de corruption de données se produisent
   * [KB3114025](https://support.microsoft.com/kb/3114025) Ralentissement des performances quand vous accédez au stockage de fichiers Azure depuis Windows 8.1 ou Server 2012 R2
   * [KB3033930](https://support.microsoft.com/kb/3033930) Le correctif augmente la limite de 64 Ko dans les mémoires tampons RIO par processus pour le service Microsoft Azure
   * [KB3004545](https://support.microsoft.com/kb/3004545) Impossible d’accéder aux machines virtuelles hébergées sur des services d’hébergement Azure par le biais d’une connexion VPN dans Windows
   * [KB3082343](https://support.microsoft.com/kb/3082343) La connectivité VPN intersite est perdue quand les tunnels VPN site à site Azure utilisent Windows Server 2012 R2 RRAS
   * [KB3140410](https://support.microsoft.com/kb/3140410) MS16-031 : Mise à jour de sécurité pour Microsoft Windows afin de résoudre un problème d’élévation de privilèges : 8 mars 2016
   * [KB3146723](https://support.microsoft.com/kb/3146723) MS16-048 : Description de la mise à jour de sécurité pour CSRSS : 12 avril 2016
   * [KB2904100](https://support.microsoft.com/kb/2904100) Le système se fige pendant des opérations d’E/S sur disque dans Windows
     
## <a name="run-sysprep--a-idstep23a"></a>Exécuter Sysprep  <a id="step23"></a>    
Pour créer une image permettant de déployer plusieurs machines virtuelles, vous devez [généraliser l’image en exécutant Sysprep](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) avant de charger le disque dur virtuel sur Azure. Vous n’avez pas besoin d’exécuter Sysprep pour utiliser un disque dur virtuel spécialisé. Pour plus d’informations, consultez les articles suivants :
   
   * [Généraliser une machine virtuelle Windows avec Sysprep](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
   * [Prise en charge de Sysprep pour les rôles de serveur](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

## <a name="complete-recommended-configurations"></a>Remplir les configurations recommandées
Les paramètres suivants n’affectent pas le chargement du disque dur virtuel. Toutefois, nous vous recommandons vivement de les configurer.

* Installez [l’agent Machines virtuelles Azure](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Après avoir installé l’agent, vous pouvez activer des extensions de machine virtuelle. Les extensions de machine virtuelle mettent en œuvre la plupart des fonctionnalités stratégiques à utiliser avec vos machines virtuelles, telles que la réinitialisation des mots de passe, la configuration de RDP, et bien d’autres encore.
* Le journal de vidage peut être utile pour résoudre les problèmes de blocage de Windows. Activez la collecte des journaux de vidage :
  
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f`
  
    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpFolder /t REG_EXPAND_SZ /d "c:\CrashDumps" /f
  
    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpCount /t REG_DWORD /d 10 /f
  
    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpType /t REG_DWORD /d 2 /f
  
    sc config wer start= auto
    ```
* Une fois la machine virtuelle créée dans Azure, configurez le fichier d’échange de taille définie par le système sur le lecteur D :
  
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management" /t REG_MULTI_SZ /v PagingFiles /d "D:\pagefile.sys 0 0" /f
    ```

## <a name="next-steps"></a>Étapes suivantes
* [Télécharger une image de machine virtuelle Windows dans Azure pour des déploiements Resource Manager](virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




<!--HONumber=Jan17_HO2-->


