<properties
   pageTitle="Extension de machine virtuelle AzureLogCollector | Microsoft Azure"
   description="Décrit l’extension de machine virtuelle AzureLogCollector VM, qui recueille tous les fichiers journaux et les rassemble dans un emplacement de stockage Azure."
   services="virtual-machines"
   documentationCenter="virtual-machines"
   authors="squillace"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="powershell"
   ms.topic="article"
   ms.tgt_pltfrm="nad"
   ms.workload="infrastructure"
   ms.date="11/12/2015"
   ms.author="rasquill"/>


# AzureLogCollector Extension

Les problèmes de diagnostic avec un service cloud Microsoft Azure nécessite la collecte des fichiers journaux de service sur les machines virtuelles lorsque les problèmes surviennent. Vous pouvez utiliser l’extension AzureLogCollector à la demande pour exécuter une collecte unique de journaux depuis une ou plusieurs machines virtuelles de Service Cloud (à partir des rôles web et de travail) et transférer les fichiers collectés dans un compte de stockage Azure, le tout sans connexion à distance à une machine virtuelle quelconque.
> [AZURE.NOTE]Vous trouverez des descriptions de la plupart des informations consignées sur http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.asp.

Il existe deux modes de collecte dépendant des types de fichiers à recueillir. - Journaux d’agent d’invité Azure uniquement (GA). Ce mode de collecte inclut tous les journaux liés aux agents invités d’Azure et d’autres composants Azure. -Tous les journaux (complète). Ce mode de collecte recueille tous les fichiers en mode GA plus :

  - journaux des événements système et d’application
  
  - Journaux d’erreurs HTTP
  
  - Journaux IIS
  
  - Fichiers journaux d’installation
  
  - autres journaux système.

Dans les deux modes de collecte, les dossiers de collecte de données supplémentaires peuvent être spécifiés à l’aide d’une collection de la structure suivante :

- **Nom** : nom de la collection qui sera utilisé comme nom de sous-dossier dans le fichier zip à collecter.

- **Emplacement** : chemin d’accès au dossier sur l’ordinateur virtuel dans lequel les fichiers seront collectés.

- **Modèle de recherche** : modèle des noms de fichiers à collecter. « * » constitue la valeur par défaut.

- **Récursive** : si les fichiers sont collectés de façon récursive sous le dossier.

## Composants requis

- Vous devez disposer d’un compte de stockage pour une extension pour enregistrer les fichiers zip générés.
- Vous devez vous assurer que vous utilisez des applets de commande Azure PowerShell V0.8.0 ou version ultérieure. Pour plus d’informations, consultez la rubrique [Téléchargements Azure](http://azure.microsoft.com/downloads/).

## Ajouter l’extension

Vous pouvez utiliser des applets de commande [Microsoft Azure PowerShell](https://msdn.microsoft.com/library/dn495240.aspx) ou des [API REST de gestion des services](https://msdn.microsoft.com/library/ee460799.aspx) pour ajouter l’extension AzureLogCollector.

Pour les services cloud, l’applet de commande Powershell de Azure existante **Set-AzureServiceExtension**, peut être utilisée pour activer l’extension sur les instances de rôle de service cloud. Chaque fois que cette extension est activée via cette applet de commande, la collecte des journaux est déclenchée sur les instances de rôle choisies pour les rôles sélectionnés.

Pour les machines virtuelles, l’applet de commande Azure Powershell existante **Set-AzureVMExtension** peut être utilisée pour activer l’extension sur des machines virtuelles. À chaque fois que cette extension est activée via les applets de commande, la collecte des journaux est déclenchée sur chaque instance.

En interne, cette extension utilise les configurations PublicConfiguration et PrivateConfiguration basées sur JSON. Voici la disposition d’un exemple de JSON pour la configuration publique et privée.

### PublicConfiguration

    {
        "Instances":  "*",
        "Mode":  "Full",
        "SasUri":  "SasUri to your storage account with sp=wl",
        "AdditionalData":  
        [
          {
                  "Name":  "StorageData",
                  "Location":  "%roleroot%storage",
                  "SearchPattern":  "*.*",
                  "Recursive":  "true"
          },
          {
                "Name":  "CustomDataFolder2",
                "Location":  "c:\customFolder",
                "SearchPattern":  "*.log",
                "Recursive":  "false"
          },
        ]
    }

### PrivateConfiguration

    {
    
    }

> [AZURE.NOTE]Cette extension n’a pas besoin de **privateConfiguration**. Vous pouvez simplement fournir une structure vide pour l’argument **–PrivateConfiguration**.

Vous pouvez exécuter l’une des deux étapes suivantes pour ajouter AzureLogCollector à une ou plusieurs instances d’un Service Cloud ou d’une machine virtuelle des rôles sélectionnés, ce qui déclenche les collectes sur chaque machine virtuelle à exécuter et envoyer aux fichiers recueillis à un compte Azure spécifié.

## Ajout d’une Extension de service

1. Suivez les instructions pour connecter Azure PowerShell à votre abonnement.

2. Spécifiez le nom de service, l’emplacement, les rôles et les instances de rôle auxquels vous souhaitez ajouter et activer l’extension AzureLogCollector.

        #Specify your cloud service name
        $ServiceName = 'extensiontest2'
        
        #Specify the slot. 'Production' or 'Staging'   
        $slot = 'Production'
        
        #Specified the roles on which the extension will be installed and enabled
        $roles = @("WorkerRole1","WebRole1")
        
        #Specify the instances on which extension will be installed and enabled.  Use wildcard * for all instances
        $instances = @("*")
        
        #Specify the collection mode, "Full" or "GA"
        $mode = "GA"

3. Spécifiez le dossier de données supplémentaires pour lequel les fichiers seront collectés (cette étape est facultative).

        #add one location
        $a1 = New-Object PSObject 
        
        $a1 | Add-Member -MemberType NoteProperty -Name "Name" -Value "StorageData"
        $a1 | Add-Member -MemberType NoteProperty -Name "SearchPattern" -Value "*"  
        $a1 | Add-Member -MemberType NoteProperty -Name "Location" -Value "%roleroot%storage"  #%roleroot% is normally E: or F: drive
        $a1 | Add-Member -MemberType NoteProperty -Name "Recursive" -Value "true"
        
        $AdditionalDataList+= $a1
              #more locations can be added....
  
    > [AZURE.NOTE]Vous pouvez utiliser le jeton `%roleroot%` pour spécifier le lecteur racine de rôle, car il n’utilise pas un lecteur fixe.

4. Fournissez le nom du compte de stockage Azure et la clé vers laquelle les fichiers recueillis seront téléchargés.

        $StorageAccountName = 'YourStorageAccountName'
        $StorageAccountKey  = ‘YouStorageAccountKey'

5. Appelez le SetAzureServiceLogCollector.ps1 (inclus à la fin de l’article) comme suit pour activer l’extension AzureLogCollector pour un Service Cloud. Une fois l’exécution achevée, vous trouverez le fichier téléchargé sous `https://YouareStorageAccountName.blob.core.windows.net/vmlogs`

        .\SetAzureServiceLogCollector.ps1 -ServiceName YourCloudServiceName  -Roles $roles  -Instances $instances –Mode $mode -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -AdditionDataLocationList $AdditionalDataList

Voici la définition des paramètres transmis au script. (Également copié ci-dessous.)

    [CmdletBinding(SupportsShouldProcess = $true)]
    
    param (
      [Parameter(Mandatory=$true)]  
    [string]   $ServiceName,
      
    [Parameter(Mandatory=$false)] 
    [string[]] $Roles ,
      
    [Parameter(Mandatory=$false)] 
    [string[]] $Instances,
      
    [Parameter(Mandatory=$false)] 
    [string]   $Slot = 'Production',
      
    [Parameter(Mandatory=$false)] 
    [string]   $Mode = 'Full',
      
    [Parameter(Mandatory=$false)] 
    [string]   $StorageAccountName,
      
    [Parameter(Mandatory=$false)] 
    [string]   $StorageAccountKey,
      
    [Parameter(Mandatory=$false)] 
    [PSObject[]] $AdditionDataLocationList = $null
    )

- *ServiceName*: nom de votre service cloud.

- *Rôles*: une liste de rôles, tels que « WebRole1 » ou « WorkerRole1 ».

- *Instances*: liste de noms des instances de rôle séparés par des virgules, utilisez la chaîne de caractères génériques (« * ») pour toutes les instances de rôle.

- *Emplacement*: nom de l’emplacement. « Production » ou « Intermédiaire ».

- *Mode*: mode de collecte. « Complet » ou « GA ».

- *StorageAccountName*: nom du compte de stockage Azure pour le stockage des données recueillies.

- *StorageAccountKey: nom de clé de compte de stockage Azure.

- *AdditionalDataLocationList*: liste obéissant à la structure suivante :

      { Nom de chaîne, Emplacement de chaîne, schéma de recherche, Récursive Bool }
             
            
## Ajout d’une extension de machine virtuelle

Suivez les instructions pour connecter Azure PowerShell à votre abonnement.

1. Spécifiez le nom du service, la machine virtuelle et le mode de collecte.

        #Specify your cloud service name
        $ServiceName = 'YourCloudServiceName'
        
        #Specify the VM name
        $VMName = "'YourVMName'"
        
        #Specify the collection mode, "Full" or "GA"
        $mode = "GA"
        
        Specify the additional data folder for which files will be collected (this step is optional).
        
        #add one location
        $a1 = New-Object PSObject 
        
        $a1 | Add-Member -MemberType NoteProperty -Name "Name" -Value "StorageData"
        $a1 | Add-Member -MemberType NoteProperty -Name "SearchPattern" -Value "*"  
        $a1 | Add-Member -MemberType NoteProperty -Name "Location" -Value "%roleroot%storage"  #%roleroot% is normally E: or F: drive
        $a1 | Add-Member -MemberType NoteProperty -Name "Recursive" -Value "true"
        
        $AdditionalDataList+= $a1
              #more locations can be added....

2. Fournissez le nom du compte de stockage Azure et la clé vers laquelle les fichiers recueillis seront téléchargés.

        $StorageAccountName = 'YourStorageAccountName'
        $StorageAccountKey  = ‘YouStorageAccountKey'
        
3. Appelez le SetAzureVMLogCollector.ps1 (inclus à la fin de l’article) comme suit pour activer l’extension AzureLogCollector pour Service Cloud. Une fois l’exécution achevée, vous trouverez le fichier téléchargé sous https://YouareStorageAccountName.blob.core.windows.net/vmlogs

  
Voici la définition des paramètres transmis au script. (Également copié ci-dessous.)

    [CmdletBinding(SupportsShouldProcess = $true)]
    
    param (
        [Parameter(Mandatory=$true)]  
      [string]   $ServiceName,
        
      [Parameter(Mandatory=$false)] 
      [string] $VMName ,
        
        [Parameter(Mandatory=$false)] 
      [string]   $Mode = 'Full',
        
      [Parameter(Mandatory=$false)] 
      [string]   $StorageAccountName,
        
      [Parameter(Mandatory=$false)] 
      [string]   $StorageAccountKey,
        
      [Parameter(Mandatory=$false)] 
      [PSObject[]] $AdditionDataLocationList = $null
      )

- ServiceName : est le nom de votre service cloud.

- VMName Nom de la machine virtuelle.

- Mode : mode de collecte. « Complète » ou « GA ».

- StorageAccountName : nom du compte de stockage Azure pour le stockage des données recueillies.

- StorageAccountKey : nom de clé de compte de stockage Azure.

- AdditionalDataLocationList : liste de la structure suivante :
      
```
      {
        String Name,
        String Location,
        String SearchPattern,
        Bool   Recursive  
      }
```

## Fichiers script PowerShell d’extension

SetAzureServiceLogCollector.ps1

    [CmdletBinding(SupportsShouldProcess = $true)]
    
    param (
                  [Parameter(Mandatory=$true)]  
                  [string]   $ServiceName,
                  
                  [Parameter(Mandatory=$false)] 
                  [string[]] $Roles ,
                  
                  [Parameter(Mandatory=$false)] 
                  [string[]] $Instances = '*',
                  
                  [Parameter(Mandatory=$false)] 
                  [string]   $Slot = 'Production',
                  
                  [Parameter(Mandatory=$false)] 
                  [string]   $Mode = 'Full',
                  
                  [Parameter(Mandatory=$false)] 
                  [string]   $StorageAccountName,
                  
                  [Parameter(Mandatory=$false)] 
                  [string]   $StorageAccountKey,
                  
                  [Parameter(Mandatory=$false)] 
                  [PSObject[]] $AdditionDataLocationList = $null
            )
    
    $publicConfig = New-Object PSObject
      
    if ($Instances -ne $null -and $Instances.Count -gt 0)  #Instances should be seperated by ,
    {
        $instanceText = $Instances[0] 
        for ($i = 1;$i -lt $Instances.Count;$i++)
        { 
              $instanceText = $instanceText+ "," + $Instances[$i]  
          }
        $publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value $instanceText
    }
    else  #For all instances if not specified.  The value should be a space or *
    {
        $publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value " "
    }
    
    if ($Mode -ne $null ) 
    {
        $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value $Mode
    }
    else
    {
        $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value "Full"
    }
    
    #
    #we need to get the Sasuri from StorageAccount and containers
    #
    $context = New-AzureStorageContext -Protocol https -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey 
      
    $ContainerName = "azurelogcollectordata"
    $existingContainer = Get-AzureStorageContainer -Context $context |  Where-Object { $_.Name -like $ContainerName} 
    if ($existingContainer -eq $null)
    {
        "Container ($ContainerName) doesn't exist. Creating it now.."  
        New-AzureStorageContainer -Context $context -Name $ContainerName -Permission off
    }
      
    $ExpiryTime =  [DateTime]::Now.AddMinutes(120).ToString("o")
    $SasUri = New-AzureStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rwl -Context $context
    $publicConfig | Add-Member -MemberType NoteProperty -Name "SasUri" -Value $SasUri
    
    #
    #Add AdditionalData to collect data from additional folders
    #
    if ($AdditionDataLocationList -ne $null )  
    {
      $publicConfig | Add-Member -MemberType NoteProperty -Name "AdditionalData" -Value $AdditionDataLocationList
    }
    
    #
    # Convert it to JSON format
    #
    $publicConfigJSON = $publicConfig | ConvertTo-Json
    "publicConfig is:  $publicConfigJSON"
      
    #we just provide a empty privateConfig object
    $privateconfig = "{
    }"
    
    if ($Roles -ne $null)
    {
          Set-AzureServiceExtension -Service $ServiceName -Slot $Slot -Role $Roles -ExtensionName 'AzureLogCollector' -ProviderNamespace Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.0 -Verbose 
    }
    else
    {
          Set-AzureServiceExtension -Service $ServiceName -Slot $Slot  -ExtensionName 'AzureLogCollector' -ProviderNamespace Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.0 -Verbose 
    }
    
    #
    #This is an optional step: generate a sasUri to the container so it can be shared with other people if nened
    #
    $SasExpireTime = [DateTime]::Now.AddMinutes(120).ToString("o")
    $SasUri = New-AzureStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rl -Context $context
    $SasUri = $SasUri + "&restype=container&comp=list"
    Write-Output "The container for uploaded file can be accessed using this link:`r`n$sasuri"


SetAzureVMLogCollector.ps1


    [CmdletBinding(SupportsShouldProcess = $true)]
    
    param (
                  [Parameter(Mandatory=$true)]  
                  [string]   $ServiceName,
                  
                  [Parameter(Mandatory=$false)] 
                  [string] $VMName ,
                  
                  [Parameter(Mandatory=$false)] 
                  [string]   $Mode = 'Full',
                  
                  [Parameter(Mandatory=$false)] 
                  [string]   $StorageAccountName,
                  
                  [Parameter(Mandatory=$false)] 
                  [string]   $StorageAccountKey,
                  
                  [Parameter(Mandatory=$false)] 
                  [PSObject[]] $AdditionDataLocationList = $null
            )
    
    $publicConfig = New-Object PSObject
    $publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value "*"
    
    if ($Mode -ne $null ) 
    {
        $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value $Mode
    }
    else
    {
        $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value "Full"
    }
    
    #
    #we need to get the Sasuri from StorageAccount and containers
    #
    $context = New-AzureStorageContext -Protocol https -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey 
      
    $ContainerName = "azurelogcollectordata"
    $existingContainer = Get-AzureStorageContainer -Context $context |  Where-Object { $_.Name -like $ContainerName} 
    if ($existingContainer -eq $null)
    {
        "Container ($ContainerName) doesn't exist. Creating it now.."  
        New-AzureStorageContainer -Context $context -Name $ContainerName -Permission off
    }
      
    $ExpiryTime =  [DateTime]::Now.AddMinutes(90).ToString("o")
    $SasUri = New-AzureStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rwl -Context $context
    $publicConfig | Add-Member -MemberType NoteProperty -Name "SasUri" -Value $SasUri
    
    #
    #Add AdditionalData to collect data from additional folders
    #
    if ($AdditionDataLocationList -ne $null )  
    {
      $publicConfig | Add-Member -MemberType NoteProperty -Name "AdditionalData" -Value $AdditionDataLocationList
    }
    
    #
    # Convert it to JSON format
    #
    $publicConfigJSON = $publicConfig | ConvertTo-Json
      
    Write-Output "PublicConfigurtion is: \r\n$publicConfigJSON"
    
    #
    #we just provide a empty privateConfig object
    #
    $privateconfig = "{
    }"
    
    if ($VMName -ne $null ) 
    {
          $VM = Get-AzureVM -ServiceName $ServiceName -Name $VMName
          $VM.VM.OSVirtualHardDisk.OS
          
          if ($VM.VM.OSVirtualHardDisk.OS -like '*Windows*')
          {
                Set-AzureVMExtension -VM $VM -ExtensionName "AzureLogCollector" -Publisher Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.* | Update-AzureVM -Verbose 
                
                #
                #We will check the VM status to find if operation by extension has been completed or not. The completion of the operation,either succeed or fail, can be indicated by
                #the presence of SubstatusList field.   
                #
                $Completed = $false
                while ($Completed -ne $true)
                {
                        $VM = Get-AzureVM -ServiceName $ServiceName -Name $VMName
                        $status = $VM.ResourceExtensionStatusList | Where-Object {$_.HandlerName -eq "Microsoft.WindowsAzure.Compute.AzureLogCollector"}
                        
                        if ( ($status.Code -ne 0) -and ($status.Status -like '*error*'))
                        {
                            Write-Output "Error status is returned: $($Status.ExtensionSettingStatus.FormattedMessage.Message)."
                              $Completed = $true
                        }
                        elseif (($status.ExtensionSettingStatus.SubstatusList -eq $null -or $status.ExtensionSettingStatus.SubstatusList.Count -lt 1))
                        {
                              $Completed = $false
                              Write-Output "Waiting for operation to complete..."
                        }
                        else
                        {
                              $Completed = $true
                              Write-Output "Operation completed."
            
                        $UploadedFileUri = $Status.ExtensionSettingStatus.SubStatusList[0].FormattedMessage.Message
                              $blob = New-Object Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob($UploadedFileUri)
    
                      # 
                            # This is an optional step:  For easier access to the file, we can generate a read-only SasUri directly to the file
                              #
                              $ExpiryTimeRead =  [DateTime]::Now.AddMinutes(120).ToString("o")
                              $ReadSasUri = New-AzureStorageBlobSASToken -ExpiryTime $ExpiryTimeRead  -FullUri  -Blob  $blob.name -Container $blob.Container.Name -Permission r -Context $context
    
                            Write-Output "The uploaded file can be accessed using this link: $ReadSasUri"
                              
                              #
                              #This is an optional step:  Remove the extension after we are done
                              #
                              Get-AzureVM -ServiceName $ServiceName -Name $VMName | Set-AzureVMExtension -Publisher Microsoft.WindowsAzure.Compute -ExtensionName "AzureLogCollector" -Version 1.* -Uninstall | Update-AzureVM -Verbose  
                              
                        }
                        Start-Sleep -s 5
                }
          }
          else
          {
              Write-Output "VM OS Type is not Windows, the extension cannot be enabled"
          }
          
    }
    else
    {
      Write-Output "VM name is not specified, the extension cannot be enabled"
    }
    
## Étapes suivantes

Vous pouvez maintenant examiner ou copier vos journaux depuis un emplacement très simple.

<!----HONumber=AcomDC_1203_2015-->