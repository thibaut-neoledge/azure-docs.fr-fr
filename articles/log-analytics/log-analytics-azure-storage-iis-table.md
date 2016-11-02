<properties
    pageTitle="Utilisation d’un Stockage Blob pour IIS et d’un Stockage Table pour les événements | Microsoft Azure"
    description="Log Analytics peut lire les journaux pour des services Azure qui écrivent des diagnostics dans un Stockage Table ou des journaux IIS écrits dans un Stockage Blob."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>



# <a name="using-blob-storage-for-iis-and-table-storage-for-events"></a>Utilisation d’un Stockage Blob pour IIS et d’un Stockage Table pour les événements

Log Analytics peut lire les journaux pour des services suivants qui écrivent des diagnostics dans un Stockage Table ou des journaux IIS écrits dans un Stockage Blob :

+ Clusters Service Fabric (version préliminaire)
+ Machines virtuelles
+ Rôle de travail/web

Pour que Log Analytics puisse collecter les données pour ces ressources, les diagnostics Azure doivent être activés.

Une fois les diagnostics activés, vous pouvez utiliser le portail Azure ou PowerShell pour configurer Log Analytics afin de collecter les journaux.

Les diagnostics Azure sont une extension vous permettant de collecter des données de diagnostic à partir d’un rôle de travail, d’un rôle Web ou d’une machine virtuelle exécuté dans Azure. Les données sont stockées dans un compte de stockage Azure à partir duquel Log Analytics peut les collecter.

Pour que Log Analytics collecte ces journaux de diagnostics Azure, ceux-ci doivent être dans les emplacements suivants :

| Type de journal | Type de ressource | Lieu |
| -------- | ------------- | -------- |
| Journaux IIS | Machines virtuelles <br> Rôles web <br> Rôles de travail | wad-iis-logfiles (Stockage Blob) |
| syslog | Machines virtuelles | LinuxSyslogVer2v0 (Stockage Table) |
| Événements opérationnels Service Fabric | Nœuds Service Fabric | WADServiceFabricSystemEventTable |
| Événements Reliable Actor Service Fabric | Nœuds Service Fabric | WADServiceFabricReliableActorEventTable |
| Événements de service fiable Service Fabric | Nœuds Service Fabric | WADServiceFabricReliableServiceEventTable |
| Journaux d'événements Windows | Nœuds Service Fabric <br> Machines virtuelles <br> Rôles web <br> Rôles de travail | WADWindowsEventLogsTable (Stockage Table) |
| Journaux de suivi des événements ETW Windows | Nœuds Service Fabric <br> Machines virtuelles <br> Rôles web <br> Rôles de travail | WADETWEventTable (Stockage Table) |

>[AZURE.NOTE] Les journaux IIS des sites Web Azure ne sont actuellement pas pris en charge.

Pour les machines virtuelles, vous pouvez également installer [Microsoft Monitoring Agent](http://go.microsoft.com/fwlink/?LinkId=517269) sur votre machine virtuelle pour activer des informations supplémentaires. Cela vous permet d’analyser les journaux IIS et les journaux des événements, mais également d'effectuer des analyses supplémentaires, notamment le suivi des modifications de configuration, l’évaluation SQL et l’évaluation de la mise à jour.

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection"></a>Activation des diagnostics Azure dans une machine virtuelle pour la collecte des journaux IIS et des journaux des événements

Utilisez la procédure suivante pour activer les diagnostics Azure dans une machine virtuelle pour la collecte de journaux IIS et de journaux des événements à l’aide du portail de gestion Microsoft Azure.

### <a name="to-enable-azure-diagnostics-in-a-virtual-machine-with-the-azure-management-portal"></a>Activation des diagnostics Azure dans une machine virtuelle à l’aide du portail de gestion Azure

1. Installez l’agent de machine virtuelle lorsque vous créez une machine virtuelle. Si la machine virtuelle existe déjà, vérifiez que l’agent de machine virtuelle est déjà installé.
    - Dans le portail de gestion Azure, accédez à la machine virtuelle, sélectionnez **Configuration facultative**, puis **Diagnostics**, et définissez **État** sur **Activé**.

    Ensuite, la machine virtuelle installe et exécute automatiquement l’extension Azure Diagnostics. Cette dernière est chargée de collecter vos données de diagnostic.

2. Activation de l’analyse et configuration de la journalisation des événements sur une machine virtuelle existante. Vous pouvez activer les diagnostics au niveau de la machine virtuelle. Pour activer les diagnostics et configurer la journalisation des événements par la suite, procédez comme suit :
    1. Sélectionnez la machine virtuelle.
    2. Cliquez sur **Analyse**.
    3. Cliquez sur **Diagnostics**.
    4. Définissez **État** sur **ACTIVÉ**.
    5. Cliquez sur les métriques de diagnostic que vous souhaitez utiliser.
    7. Cliquez sur **OK**.

Vous pouvez spécifier les événements écrits dans Azure Storage plus précisément à l’aide d’Azure PowerShell. Voir [Collecter des données à l’aide des diagnostics Azure écrits dans Table Storage ou de journaux IIS écrits dans un objet blob](log-analytics-azure-storage-json.md).


## <a name="enable-azure-diagnostics-in-a-web-role-for-iis-log-and-event-collection"></a>Activation des diagnostics Azure dans un rôle Web pour la collecte de journaux IIS et des événements

Consultez [Activation des diagnostics dans un service cloud](../cloud-services/cloud-services-dotnet-diagnostics.md). Vous allez utiliser les informations de base que vous y trouverez et personnaliser les étapes ici pour une utilisation avec Log Analytics.

Avec les diagnostics Azure activés :

- Les journaux IIS sont stockés par défaut et les données de journal sont transférées selon l’intervalle de transfert scheduledTransferPeriod.
- Les journaux des événements Windows ne sont pas transférés par défaut.

### <a name="to-enable-diagnostics"></a>Activation des diagnostics

Pour activer les journaux d’événements Windows, ou pour modifier l’intervalle scheduledTransferPeriod, configurez Azure Diagnostics à l’aide du fichier de configuration XML (diagnostics.wadcfg), comme indiqué dans [Étape 4 : création de votre fichier de configuration Diagnostics et installation de l’extension](../cloud-services/cloud-services-dotnet-diagnostics.md).

L’exemple de fichier de configuration suivant collecte des journaux IIS et tous les événements des journaux de l’application et du système :

```
    <?xml version="1.0" encoding="utf-8" ?>
    <DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"
          configurationChangePollInterval="PT1M"
          overallQuotaInMB="4096">

      <Directories bufferQuotaInMB="0"
         scheduledTransferPeriod="PT10M">  
        <!-- IISLogs are only relevant to Web roles -->
        <IISLogs container="wad-iis" directoryQuotaInMB="0" />
      </Directories>

      <WindowsEventLog bufferQuotaInMB="0"
         scheduledTransferLogLevelFilter="Verbose"
         scheduledTransferPeriod="PT10M">
        <DataSource name="Application!*" />
        <DataSource name="System!*" />
      </WindowsEventLog>

    </DiagnosticMonitorConfiguration>
```

Assurez-vous que votre paramètres ConfigurationSettings spécifient un compte de stockage, comme dans l’exemple suivant :

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>
```

Les valeurs **AccountName** et **AccountKey** figurent dans le portail de gestion Azure, sur le tableau de bord du compte de stockage, sous Gérer les clés d’accès. Le protocole de la chaîne de connexion doit être **https**.

Une fois que la configuration de diagnostic mise à jour est appliquée à votre service cloud et écrit des diagnostics dans Stockage Azure, vous êtes prêt à configurer Log Analytics.


## <a name="use-the-azure-portal-to-collect-logs-from-azure-storage"></a>Utiliser le portail Azure pour collecter les journaux à partir de Stockage Azure

Vous pouvez utiliser le portail Azure pour configurer Log Analytics afin de collecter les journaux pour les services Azure suivants :

+ Clusters Service Fabric
+ Machines virtuelles
+ Rôle de travail/web

Dans le portail Azure, accédez à votre espace de travail Log Analytics, puis effectuer les tâches suivantes :

1. Cliquez sur *Journaux des comptes de stockage*.
2. Cliquez sur la tâche *Ajouter*.
3. Sélectionnez le compte de stockage contenant les journaux de diagnostic.
  - Ce peut être un compte de stockage classique ou un compte de stockage Azure Resource Manager.
4. Sélectionnez le Type de données pour lequel vous souhaitez collecter des journaux.
  - Il s’agira de l’un des types suivants : Journaux IIS, Événements, Syslog (Linux), Journaux de suivi des événements ETW, Événements Service Fabric.
5. La valeur de Source est automatiquement complétée selon le type de données, et ne peut pas être modifiée.
6. Cliquez sur OK pour enregistrer la configuration.

Répétez les étapes 2 à 6 pour les autres comptes de stockage et types de données que Log Analytics doit collecter.

Après environ 30 minutes, vous pouvez voir les données du compte de stockage dans Log Analytics. Vous voyez les données écrites dans le stockage uniquement après application de la configuration. Log Analytics ne lit pas les données préexistantes du compte de stockage.

>[AZURE.NOTE] Le portail ne valide pas l’existence de la Source dans le compte de stockage ou le fait que de nouvelles données sont écrites.

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection-using-powershell"></a>Activer les diagnostics Azure dans une machine virtuelle pour la collecte des journaux IIS et des journaux des événements avec PowerShell

Vous pouvez spécifier les événements écrits dans Azure Storage plus précisément à l’aide d’Azure PowerShell.
Consultez [Activation des diagnostics dans Azure Virtual Machines](../virtual-machines-dotnet-diagnostics.md) pour plus d’informations.

Vous pouvez activer et mettre à jour l’Agent en utilisant le script PowerShell suivant.
Vous pouvez également utiliser ce script avec une configuration de journalisation personnalisée.
Vous devez modifier le script pour définir le compte de stockage, le nom du service et le nom de la machine virtuelle.
Le script utilisant des applets de commande pour les machines virtuelles classiques.

Examinez l’exemple de script suivant, copiez-le et modifiez-le si nécessaire, enregistrez l’exemple dans un fichier de script PowerShell, puis exécutez le script.

```
    #Connect to Azure
    Add-AzureAccount

    # settings to change:
    $wad_storage_account_name = "myStorageAccount"
    $service_name = "myService"
    $vm_name = "myVM"

    #Construct Azure Diagnostics public config and convert to config format

    # Collect just system error events:
    $wad_xml_config = "<WadCfg><DiagnosticMonitorConfiguration><WindowsEventLog scheduledTransferPeriod=""PT1M""><DataSource name=""System!* "" /></WindowsEventLog></DiagnosticMonitorConfiguration></WadCfg>"

    $wad_b64_config = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($wad_xml_config))
    $wad_public_config = [string]::Format("{{""xmlCfg"":""{0}""}}",$wad_b64_config)

    #Construct Azure diagnostics private config

    $wad_storage_account_key = (Get-AzureStorageKey $wad_storage_account_name).Primary
    $wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)

    #Enable Diagnostics Extension for Virtual Machine

    $wad_extension_name = "IaaSDiagnostics"
    $wad_publisher = "Microsoft.Azure.Diagnostics"
    $wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Version # Gets latest version of the extension

    (Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM
```

## <a name="next-steps"></a>Étapes suivantes

- [Utiliser des fichiers JSON dans Stockage Blob](log-analytics-azure-storage-json.md) pour lire les journaux des services Azure qui écrivent des diagnostics dans Stockage Blob au format JSON.
- [Activer les solutions](log-analytics-add-solutions.md) pour fournir des informations sur les données.
- [Utiliser les requêtes de recherche](log-analytics-log-searches.md) pour analyser les données.



<!--HONumber=Oct16_HO2-->


