<properties
	pageTitle="Gérer l’inscription et la protection des systèmes" 
	description="Microsoft Azure Site Recovery coordonne la réplication, le basculement et la récupération des machines virtuelles hébergées, sur des serveurs locaux, vers Microsoft Azure ou un centre de données secondaire. Utilisez cet article pour annuler l’inscription des serveurs à partir d’un coffre Site Recovery et pour désactiver la protection des machines virtuelles et serveurs physiques." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="10/07/2015" 
	ms.author="raynew"/>

# Gérer l’inscription et la protection des systèmes

Cet article explique comment annuler l’inscription des serveurs du coffre Microsoft Site Recovery et comment désactiver la protection des machines virtuelles protégées par ce logiciel. Après avoir lu cet article, peut-être aurez-vous des questions. Le cas échéant, publiez-les sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Annuler l’inscription d’un serveur VMM

Vous pouvez annuler l’inscription d’un serveur VMM dans un coffre en supprimant le serveur via l’onglet **Serveurs** du portail Microsoft Azure Site Recovery. Notez les points suivants :

-  **Serveur VMM connecté** : nous vous recommandons d’annuler l’inscription du serveur VMM lorsqu’il est connecté à Microsoft Azure. Cette opération garantit le nettoyage correct des paramètres sur le serveur VMM local, ainsi que sur les serveurs VMM associés (qui contiennent des clouds mappés sur ceux du serveur à supprimer). En cas de problème de connectivité permanent, nous vous recommandons de supprimer uniquement un serveur non connecté.
- **Serveur VMM non connecté**: si le serveur VMM n’est pas connecté lorsque vous le supprimez, vous devez exécuter un script manuellement afin d’effectuer le nettoyage. Ce script est disponible dans la [galerie Microsoft](https://gallery.technet.microsoft.com/scriptcenter/Cleanup-Script-for-Windows-95101439). Notez l’ID VMM du serveur, afin d’effectuer le processus de nettoyage manuel.
- **Serveur VMM dans un cluster** : si vous devez annuler l’inscription d’un serveur VMM déployé au sein d’un cluster, procédez comme suit :

	- Si le serveur est connecté, supprimez le serveur VMM connecté sur l’onglet **Serveurs**. Pour désinstaller le fournisseur sur le serveur, connectez-vous sur chaque nœud du cluster et désinstallez-le dans le Panneau de configuration. Exécutez le script de nettoyage indiqué dans la section précédente sur tous les nœuds passifs du cluster, afin de supprimer les entrées d’inscription.
	- Si le serveur n’est pas connecté, vous devez exécuter le script de nettoyage sur tous les nœuds du cluster.

### Annuler l’inscription d’un serveur VMM non connecté

Sur le serveur VMM que vous souhaitez supprimer, procédez comme suit :

1. Annulez l’inscription du serveur VMM à partir du portail Microsoft Azure.
2. Sur le serveur VMM, téléchargez le script de nettoyage.
3. Ouvrez PowerShell avec l’option Exécuter en tant qu’administrateur pour modifier la stratégie d’exécution associée à l’étendue par défaut (LocalMachine).
4. Suivez les instructions du script. 

Sur les serveurs VMM dotés de clouds couplés à ceux du serveur que vous supprimez, procédez comme suit :

1. Exécutez le script de nettoyage et suivez les étapes 2 à 4.
2. Spécifiez l’ID VMM du serveur VMM dont l’inscription a été annulée. 
3. Ce script supprime les informations d’inscription relatives au serveur VMM et au couplage des clouds.


## Annuler l’inscription d’un serveur Hyper-V dans un site Hyper-V

Lors du déploiement de Microsoft Azure Site Recovery pour protéger les machines virtuelles situées sur un serveur Hyper-V, au sein d’un site Hyper-V (sans aucun serveur VMM), vous pouvez annuler l’inscription d’un serveur Hyper-V à partir d’un coffre, comme suit :

1. Désactivez la protection des machines virtuelles se trouvant sur le serveur Hyper-V.
2. Sur l’onglet **Serveurs** du portail Microsoft Azure Site Recovery, sélectionnez le serveur, puis cliquez sur Supprimer. Lorsque vous effectuez cette opération, il n’est pas nécessaire que le serveur soit connecté à Microsoft Azure.
3. Exécutez le script suivant pour nettoyer les paramètres sur le serveur et annuler son inscription dans le coffre. 

	    pushd .
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
	    popd


## Arrêter la protection d’une machine virtuelle Hyper-V

Si vous souhaitez arrêter la protection d'une machine virtuelle Hyper-V, vous devez en supprimer la protection. Selon la façon dont vous supprimez la protection, vous devrez ou non nettoyer manuellement les paramètres de protection sur la machine.

### Supprimer la protection

1. Dans l’onglet **Machines virtuelles** de la page de propriétés du cloud, sélectionnez la machine virtuelle et cliquez sur **Supprimer**.
2. Sur la page **Confirmer la suppression de la machine virtuelle**, vous pouvez choisir différentes options :

	- **Désactiver la protection** : si vous activez cette option et l’enregistrez, la machine virtuelle n’est plus protégée par Site Recovery. Les paramètres de protection de la machine virtuelle seront automatiquement nettoyés.
	- **Supprimer du coffre** : si vous sélectionnez cette option, la machine virtuelle est supprimée du coffre Site Recovery uniquement. Les paramètres de protection locale de la machine virtuelle ne seront pas affectés. Vous devez nettoyer manuellement les paramètres en suivant les instructions ci-dessous pour supprimer les paramètres de protection et supprimer la machine virtuelle de l'abonnement Azure.

Si vous choisissez de supprimer la machine virtuelle et ses disques durs, ces derniers sont supprimés de l’emplacement cible.

### Nettoyer les paramètres de protection manuellement (entre des sites VMM)

Si vous choisissez l’option **Arrêt de la gestion de la machine virtuelle**, nettoyez manuellement les paramètres, comme suit :

1. Sur le serveur principal, exécutez ce script à partir de la console VMM pour nettoyer les paramètres de la machine virtuelle principale. Dans la console VMM, cliquez sur le bouton PowerShell pour ouvrir la console VMM PowerShell. Remplacez l’élément SQLVM1 par le nom de votre machine virtuelle.

	     $vm = get-scvirtualmachine -Name "SQLVM1"
	     Set-SCVirtualMachine -VM $vm -ClearDRProtection

2. Sur le serveur VMM secondaire, exécutez ce script pour nettoyer les paramètres de la machine virtuelle secondaire :

	    $vm = get-scvirtualmachine -Name "SQLVM1"
	    Remove-SCVirtualMachine -VM $vm -Force

3. Sur le serveur secondaire VMM, après avoir exécuté le script, actualisez les machines virtuelles sur le serveur hôte Hyper-V pour que la machine virtuelle secondaire soit redétectée dans la console VMM.
4. Les étapes ci-dessus effacent les paramètres de réplication du serveur VMM uniquement. Si vous souhaitez supprimer la réplication de la machine virtuelle, vous devez effectuer les étapes ci-dessous sur les machines virtuelles principale et secondaire. Exécutez le script ci-dessous pour supprimer la réplication et remplacez SQLVM1 par le nom de votre machine virtuelle.

	    Remove-VMReplication –VMName “SQLVM1”


### Nettoyer les paramètres de protection manuellement (entre des sites VMM locaux et Azure)

1. Sur le serveur VMM source, exécutez ce script pour nettoyer les paramètres de la machine virtuelle principale :

	    $vm = get-scvirtualmachine -Name "SQLVM1"
	    Set-SCVirtualMachine -VM $vm -ClearDRProtection

2. Les étapes ci-dessus effacent les paramètres de réplication du serveur VMM uniquement. Une fois que vous avez supprimé la réplication du serveur VMM, assurez-vous de supprimer la réplication pour la machine virtuelle s'exécutant sur le serveur hôte Hyper-V avec ce script. Remplacez l’élément SQLVM1 par le nom de votre machine virtuelle et l’élément host01.contoso.com, par le nom du serveur hôte Hyper-V.

	    $vmName = "SQLVM1"
	    $hostName  = "host01.contoso.com"
	    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
	    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
	    $replicationService.RemoveReplicationRelationship($vm.__PATH)

### Nettoyer les paramètres de protection manuellement (entre des sites Hyper-V et Azure)

1. Sur le serveur hôte Hyper-V source, pour supprimer la réplication de la machine virtuelle, utilisez ce script. Remplacez l’élément SQLVM1 par le nom de votre machine virtuelle.

	    $vmName = "SQLVM1"
	    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
	    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
	    $replicationService.RemoveReplicationRelationship($vm.__PATH)

## Arrêter la protection d'une machine virtuelle VMware ou d’un serveur physique

Si vous souhaitez arrêter la protection d'une machine virtuelle VMware ou d’un serveur physique, vous devez en supprimer la protection. Selon la façon dont vous supprimez la protection, vous devrez ou non nettoyer manuellement les paramètres de protection sur la machine.

### Supprimer la protection

1. Dans l’onglet **Machines virtuelles** de la page de propriétés du cloud, sélectionnez la machine virtuelle et cliquez sur **Supprimer**.
2. Dans **Supprimer une machine virtuelle** sélectionnez l’une des options suivantes :

	- **Désactiver la protection (à utiliser pour les exercices de récupération et les redimensionnements de volumes)**. Vous ne pourrez voir et activer cette option que si vous avez :
		- **Redimensionné le volume de la machine virtuelle** : lorsque vous redimensionnez un volume, la machine virtuelle passe en état critique. Dans ce cas, sélectionnez cette option. Elle désactive la protection tout en conservant des points de récupération dans Azure. Lorsque vous réactiverez la protection de la machine, les données du volume redimensionné seront transférées vers Azure.
		- **Exécuté un basculement** : une fois que vous avez testé votre environnement en exécutant un basculement à partir des machines virtuelles VMware locales ou des serveurs physiques vers Azure, sélectionnez cette option pour protéger de nouveau vos machines virtuelles locales. Cette option désactive chaque machine virtuelle. Ensuite, vous devrez réactiver la protection. Notez les points suivants :
			- La désactivation de la machine virtuelle avec ce paramètre n'affecte pas la machine virtuelle de réplication dans Azure.
			- Vous ne devez pas désinstaller le service de mobilité de la machine virtuelle.
	
	- **Désactiver la protection** : si vous activez cette option et l’enregistrez, la machine n’est plus protégée par Site Recovery. Les paramètres de protection de la machine seront automatiquement nettoyés.
	- **Supprimer du coffre** : si vous sélectionnez cette option, la machine est supprimée du coffre Site Recovery uniquement. Les paramètres de protection locale de la machine ne seront pas affectés. Pour supprimer les paramètres de la machine et supprimer la machine virtuelle de l’abonnement Azure, vous devrez nettoyer les paramètres en désinstallant le service de mobilité. ![Supprimer les options](./media/site-recovery-manage-registration-and-protection/RegistrationProtection_RemoveVM.png)

<!---HONumber=Oct15_HO3-->