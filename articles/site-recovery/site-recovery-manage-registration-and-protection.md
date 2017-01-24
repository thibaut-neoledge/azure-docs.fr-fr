---
title: "Supprimer les serveurs et désactiver la protection | Microsoft Docs"
description: "Cet article montre comment annuler l’inscription des serveurs à partir d’un coffre Site Recovery et comment désactiver la protection des machines virtuelles et serveurs physiques."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: cfreeman
editor: 
ms.assetid: ef1f31d5-285b-4a0f-89b5-0123cd422d80
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/19/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 3710f5966889b805b3ea8d2a3fe33fa9ab86c2ec
ms.openlocfilehash: 5dff369ca32f9f4487684b27c57d2722ab9ad954


---

# <a name="remove-servers-and-disable-protection"></a>Supprimer des serveurs et désactiver la protection

Le service Azure Site Recovery participe à votre stratégie de continuité des activités et de récupération d’urgence. Il orchestre la réplication, le basculement et la récupération des machines virtuelles et serveurs physiques. Les machines peuvent être répliquées vers Azure ou vers un centre de données local secondaire. Pour avoir un rapide aperçu, consultez la section [Qu’est-ce qu’Azure Site Recovery ?](site-recovery-overview.md)

Cet article explique comment annuler l’inscription des serveurs à partir du coffre Recovery Services et désactiver la protection des machines virtuelles protégées par Site Recovery.

Publier des commentaires ou des questions au bas de cet article, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="unregister-a-configuration-server"></a>Annuler l’inscription d’un serveur de configuration

Si vous répliquez des machines virtuelles VMware ou des serveurs physiques Windows/Linux sur Azure, vous pouvez annuler l’inscription du serveur de configuration à partir d’un coffre, en procédant comme suit :

1. Désactivez la protection des ordinateurs. Dans **Éléments protégés** > **Éléments répliqués**, cliquez avec le bouton droit sur la machine et sélectionnez **Supprimer**.
2. Dissociez les stratégies. Dans **Infrastructure Site Recovery** > **For VMWare & Physical Machines (Pour les machines VMWare et physiques)** > **Stratégies de réplication**, double-cliquez sur la stratégie associée. Cliquez avec le bouton droit sur le serveur de configuration, puis cliquez sur **Dissocier**.
3. Supprimez les processus locaux supplémentaires ou les serveurs cibles maîtres. Dans **Infrastructure Site Recovery** > **For VMWare & Physical Machines (Pour les machines VMWare et physiques)** > **Serveurs de configuration**, cliquez avec le bouton droit sur le serveur et sélectionnez **Supprimer**.
4. Supprimez le serveur de configuration.
5. Désinstallez manuellement le service Mobilité en cours d’exécution sur le serveur cible maître (serveur distinct ou serveur de configuration).
6. Désinstallez le serveur de configuration.
7. Désinstallez les serveurs de traitement supplémentaires.
8. Sur le serveur de configuration, désinstallez l’instance MySQL installée par Site Recovery.
9. Dans le registre du serveur de configuration, supprimez la clé ``HKEY_LOCAL_MACHINE\Software\Microsoft\Azure Site Recovery``.

Les instructions sont identiques si le serveur de configuration est connecté à Azure ou déconnecté d’Azure.


## <a name="unregister-a-connected-vmm-server"></a>Annuler l’inscription d’un serveur VMM connecté

Nous vous recommandons d’annuler l’inscription du serveur VMM lorsqu’il est connecté à Azure. Cela garantit le nettoyage correct des paramètres sur les serveurs VMM (et sur d’autres serveurs VMM avec des clouds appariés). En cas de problème de connectivité permanent, ne supprimez qu’un serveur non connecté. Si le serveur VMM n’est pas connecté, vous devez exécuter manuellement un script pour nettoyer les paramètres.

1. Arrêtez la réplication des machines virtuelles dans les clouds sur le serveur VMM que vous souhaitez supprimer.
2. Supprimez tous les mappages réseau utilisés par les clouds sur le serveur VMM que vous souhaitez supprimer. Dans **Infrastructure Site Recovery** > **For System Center VMM (Pour System Center VMM)** > **Mappage réseau**, cliquez avec le bouton droit sur le mappage réseau et sélectionnez **Supprimer**.
3. Dissociez les stratégies de réplication des clouds sur le serveur VMM que vous souhaitez supprimer.  Dans **Infrastructure Site Recovery** > **For System Center VMM (Pour System Center VMM)** >  **Stratégies de réplication**, double-cliquez sur la stratégie associée. Cliquez avec le bouton droit sur le cloud et sélectionnez **Dissocier**.
4. Supprimez le serveur VMM ou le mode VMM actif. Dans **Infrastructure Site Recovery** > **For System Center VMM (Pour System Center VMM)** > **Serveurs VMM**, cliquez avec le bouton droit sur le serveur et sélectionnez **Supprimer**.
5. Désinstallez le fournisseur manuellement sur le serveur VMM. Si vous avez un cluster, supprimez-le de tous les nœuds.
6. Si vous répliquez vers Azure, supprimez manuellement l’agent Microsoft Recovery Services des hôtes Hyper-V dans les clouds supprimés.



### <a name="unregister-an-unconnected-vmm-server"></a>Annuler l’inscription d’un serveur VMM non connecté

1. Arrêtez la réplication des machines virtuelles dans les clouds sur le serveur VMM que vous souhaitez supprimer.
2. Supprimez tous les mappages réseau utilisés par les clouds sur le serveur VMM que vous souhaitez supprimer. Dans **Infrastructure Site Recovery** > **For System Center VMM (Pour System Center VMM)** > **Mappage réseau**, cliquez avec le bouton droit sur le mappage réseau et sélectionnez **Supprimer**.
3. Notez l’ID du serveur VMM.
4. Dissociez les stratégies de réplication des clouds sur le serveur VMM que vous souhaitez supprimer.  Dans **Infrastructure Site Recovery** > **For System Center VMM (Pour System Center VMM)** >  **Stratégies de réplication**, double-cliquez sur la stratégie associée. Cliquez avec le bouton droit sur le cloud et sélectionnez **Dissocier**.
5. Supprimez le serveur VMM ou le nœud actif. Dans **Infrastructure Site Recovery** > **For System Center VMM (Pour System Center VMM)** > **Serveurs VMM**, cliquez avec le bouton droit sur le serveur et sélectionnez **Supprimer**.
6. Téléchargez et exécutez le [script de nettoyage](http://aka.ms/asr-cleanup-script-vmm) sur le serveur VMM. Ouvrez PowerShell avec l’option **Exécuter en tant qu’administrateur** pour modifier la stratégie d’exécution associée à l’étendue par défaut (LocalMachine). Dans le script, spécifiez l’ID du serveur VMM que vous souhaitez supprimer. Le script supprime du serveur l’inscription et les informations de pairage de cloud.
5. Exécutez le script de nettoyage sur les autres serveurs VMM qui contiennent des clouds associés à des clouds sur le serveur VMM que vous souhaitez supprimer.
6. Exécutez le script de nettoyage sur les autres nœuds de cluster VMM passifs sur lesquels le fournisseur est installé.
7. Désinstallez le fournisseur manuellement sur le serveur VMM. Si vous avez un cluster, supprimez-le de tous les nœuds.
8. Si vous répliquez vers Azure, vous pouvez supprimer l’agent Microsoft Recovery Services des hôtes Hyper-V dans les clouds supprimés.

## <a name="unregister-a-hyper-v-host-in-a-hyper-v-site"></a>Annuler l’inscription d’un hôte Hyper-V dans un site Hyper-V

Les hôtes Hyper-V non gérés par VMM sont rassemblés dans un site Hyper-V. Pour supprimer un hôte d’un site Hyper-V, procédez comme suit :

1. Désactivez la réplication des machines virtuelles Hyper-V situées sur l’hôte.
2. Dissociez les stratégies du site Hyper-V. Dans **Infrastructure Site Recovery** > **For Hyper-V Sites (Pour les sites Hyper-V)** >  **Stratégies de réplication**, double-cliquez sur la stratégie associée. Cliquez avec le bouton droit sur le site et sélectionnez **Dissocier**.
3. Supprimez les hôtes Hyper-V. Dans **Infrastructure Site Recovery** > **For System Center VMM (Pour System Center VMM)** > **Hyper-V Hosts (Hôtes Hyper-V)**, cliquez avec le bouton droit sur le serveur et sélectionnez **Supprimer**.
4. Supprimez le site Hyper-V après avoir supprimé tous les hôtes de celui-ci. Dans **Infrastructure Site Recovery** > **For System Center VMM (Pour System Center VMM)** > **Hyper-V Sites (Sites Hyper-V)**, cliquez avec le bouton droit sur le site et sélectionnez **Supprimer**.
5. Exécutez le script suivant sur chaque hôte Hyper-V que vous avez supprimé. Ce script nettoie les paramètres sur le serveur et annule son inscription dans le coffre.


        `` pushd .
        try
        {
             $windowsIdentity=[System.Security.Principal.WindowsIdentity]::GetCurrent()
             $principal=new-object System.Security.Principal.WindowsPrincipal($windowsIdentity)
             $administrators=[System.Security.Principal.WindowsBuiltInRole]::Administrator
             $isAdmin=$principal.IsInRole($administrators)
             if (!$isAdmin)
             {
                "Please run the script as an administrator in elevated mode."
                $choice = Read-Host
                return;       
             }

            $error.Clear()    
            "This script will remove the old Azure Site Recovery Provider related properties. Do you want to continue (Y/N) ?"
            $choice =  Read-Host

            if (!($choice -eq 'Y' -or $choice -eq 'y'))
            {
            "Stopping cleanup."
            return;
            }

            $serviceName = "dra"
            $service = Get-Service -Name $serviceName
            if ($service.Status -eq "Running")
            {
                "Stopping the Azure Site Recovery service..."
                net stop $serviceName
            }

            $asrHivePath = "HKLM:\SOFTWARE\Microsoft\Azure Site Recovery"
            $registrationPath = $asrHivePath + '\Registration'
            $proxySettingsPath = $asrHivePath + '\ProxySettings'
            $draIdvalue = 'DraID'

            if (Test-Path $asrHivePath)
            {
                if (Test-Path $registrationPath)
                {
                    "Removing registration related registry keys."    
                    Remove-Item -Recurse -Path $registrationPath
                }

                if (Test-Path $proxySettingsPath)
            {
                    "Removing proxy settings"
                    Remove-Item -Recurse -Path $proxySettingsPath
                }

                $regNode = Get-ItemProperty -Path $asrHivePath
                if($regNode.DraID -ne $null)
                {            
                    "Removing DraId"
                    Remove-ItemProperty -Path $asrHivePath -Name $draIdValue
                }
                "Registry keys removed."
            }

            # First retrive all the certificates to be deleted
            $ASRcerts = Get-ChildItem -Path cert:\localmachine\my | where-object {$_.friendlyname.startswith('ASR_SRSAUTH_CERT_KEY_CONTAINER') -or $_.friendlyname.startswith('ASR_HYPER_V_HOST_CERT_KEY_CONTAINER')}
            # Open a cert store object
            $store = New-Object System.Security.Cryptography.X509Certificates.X509Store("My","LocalMachine")
            $store.Open('ReadWrite')
            # Delete the certs
            "Removing all related certificates"
            foreach ($cert in $ASRcerts)
            {
                $store.Remove($cert)
            }
        }catch
        {    
            [system.exception]
            Write-Host "Error occured" -ForegroundColor "Red"
            $error[0]
            Write-Host "FAILED" -ForegroundColor "Red"
        }
        popd``



## <a name="disable-protection-for-a-vmware-vm-or-physical-server"></a>Désactiver la protection d’une machine virtuelle VMware ou d’un serveur physique

1. Dans **Éléments protégés** > **Éléments répliqués**, cliquez avec le bouton droit sur la machine et sélectionnez **Supprimer**.
2. Dans **Supprimer la machine**, sélectionnez une de ces options :
    - **Désactiver la protection pour la machine (recommandé)**. Utilisez cette option pour arrêter la réplication de la machine. Les paramètres de Site Recovery seront nettoyés automatiquement. Cette option ne s’affiche que dans les circonstances suivantes :
        - **Vous avez redimensionné le volume de la machine virtuelle** : lorsque vous redimensionnez un volume, la machine virtuelle passe en état critique. Sélectionnez cette option pour désactiver la protection tout en conservant les points de récupération dans Azure. Lorsque vous réactivez la protection de la machine, les données du volume redimensionné seront transférées vers Azure.
        - **Vous avez récemment effectué un basculement** : après avoir effectué un basculement pour tester votre environnement, sélectionnez cette option pour réactiver la protection des machines locales. Elle désactive chaque machine virtuelle. Ensuite, vous devrez réactiver leur protection. La désactivation de la machine à l’aide ce paramètre est sans effet sur la machine virtuelle répliquée dans Azure. Ne désinstallez pas le service Mobilité de la machine.
    - **Arrêter la gestion de la machine**. Si vous sélectionnez cette option, la machine n’est supprimée que du coffre. Les paramètres de protection locale de la machine ne seront pas affectés. Pour supprimer les paramètres sur la machine et supprimer celle-ci de l’abonnement Azure, vous devez nettoyer les paramètres en désinstallant le service Mobilité.

## <a name="disable-protection-for-a-hyper-v-vm-in-a-vmm-cloud"></a>Désactiver la protection d’une machine virtuelle Hyper-V dans un cloud VMM

1. Dans **Éléments protégés** > **Éléments répliqués**, cliquez avec le bouton droit sur la machine et sélectionnez **Supprimer**.
2. Dans **Supprimer la machine**, sélectionnez une de ces options :

    - **Désactiver la protection pour la machine (recommandé)**. Utilisez cette option pour arrêter la réplication de la machine. Les paramètres de Site Recovery seront nettoyés automatiquement.
    - **Arrêter la gestion de la machine**. Si vous sélectionnez cette option, la machine n’est supprimée que du coffre. Les paramètres de protection locale de la machine ne seront pas affectés. Pour supprimer les paramètres sur la machine et supprimer celle-ci de l’abonnement Azure, vous devez nettoyer les paramètres manuellement en suivant les instructions ci-dessous. Notez que si vous choisissez de supprimer la machine virtuelle et ses disques durs, ces derniers sont supprimés de l’emplacement cible.

### <a name="clean-up-protection-settings---replication-to-a-secondary-vmm-site"></a>Nettoyer les paramètres de protection (réplication sur un site VMM secondaire)

Si vous avez sélectionné **Arrêter la gestion de la machine** et que vous effectuez la réplication sur un site secondaire, exécutez ce script sur le serveur principal pour nettoyer les paramètres de la machine virtuelle principale. Dans la console VMM, cliquez sur le bouton PowerShell pour ouvrir la console VMM PowerShell. Remplacez l’élément SQLVM1 par le nom de votre machine virtuelle.

         ``$vm = get-scvirtualmachine -Name "SQLVM1"
         Set-SCVirtualMachine -VM $vm -ClearDRProtection``
2. Sur le serveur VMM secondaire, exécutez ce script pour nettoyer les paramètres de la machine virtuelle secondaire :

        ``$vm = get-scvirtualmachine -Name "SQLVM1"
        Remove-SCVirtualMachine -VM $vm -Force``
3. Sur le serveur VMM secondaire, actualisez les machines virtuelles sur le serveur hôte Hyper-V pour que la machine virtuelle secondaire soit détectée à nouveau dans la console VMM.
4. Les étapes ci-dessus effacent les paramètres de réplication sur le serveur VMM. Si vous souhaitez arrêter la réplication de la machine virtuelle, exécutez le script suivant sur les machines virtuelles principales et secondaires. Remplacez l’élément SQLVM1 par le nom de votre machine virtuelle.

        ``Remove-VMReplication –VMName “SQLVM1”``

### <a name="clean-up-protection-settings---replication-to-azure"></a>Nettoyer les paramètres de protection (réplication sur Azure)

1. Si vous avez sélectionné **Arrêter la gestion de la machine** et que vous répliquez sur Azure, exécutez ce script sur le serveur VMM source, en utilisant PowerShell à partir de la console VMM.
        ``$vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection``
2. Les étapes ci-dessus effacent les paramètres de réplication sur le serveur VMM. Pour arrêter la réplication de la machine virtuelle sur le serveur hôte Hyper-V, exécutez ce script. Remplacez SQLVM1 par le nom de votre machine virtuelle et host01.contoso.com par le nom du serveur hôte Hyper-V.

        ``$vmName = "SQLVM1"
        $hostName  = "host01.contoso.com"
        $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
        $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
        $replicationService.RemoveReplicationRelationship($vm.__PATH)``


## <a name="disable-protection-for-a-hyper-v-vm-in-a-hyper-v-site"></a>Désactiver la protection d’une machine virtuelle Hyper-V dans un site Hyper-V

Utilisez cette procédure si vous répliquez des machines virtuelles Hyper-V sur Azure sans serveur VMM.

1. Dans **Éléments protégés** > **Éléments répliqués**, cliquez avec le bouton droit sur la machine et sélectionnez **Supprimer**.
2. Dans **Supprimer la machine**, vous pouvez sélectionner les options suivantes :

   - **Désactiver la protection pour la machine (recommandé)**. Utilisez cette option pour arrêter la réplication de la machine. Les paramètres de Site Recovery seront nettoyés automatiquement.
   - **Arrêter la gestion de la machine**. Si vous sélectionnez cette option, la machine n’est supprimée que du coffre. Les paramètres de protection locale de la machine ne seront pas affectés. Pour supprimer les paramètres sur la machine virtuelle et supprimer celle-ci de l’abonnement Azure, vous devez nettoyer les paramètres manuellement. Si vous choisissez de supprimer la machine virtuelle et ses disques durs, ces derniers sont supprimés de l’emplacement cible.
3. Si vous avez sélectionné **Arrêter la gestion de la machine**, exécutez ce script sur le serveur hôte Hyper-V source pour arrêter la réplication de la machine virtuelle. Remplacez l’élément SQLVM1 par le nom de votre machine virtuelle.

        $vmName = "SQLVM1"
        $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
        $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
        $replicationService.RemoveReplicationRelationship($vm.__PATH)



<!--HONumber=Dec16_HO3-->


