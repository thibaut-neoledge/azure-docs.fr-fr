<properties
   pageTitle="Analyse des données des serveurs dans Microsoft Azure"
   description="Rechercher des journaux des événements et IIS pour les services de cloud et les machines virtuelles en activant les diagnostics Azure."
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor=""/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/02/2015"
   ms.author="banders"/>
# Analyse des données des serveurs dans Microsoft Azure

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Operational Insights utilise des données des serveurs dans votre infrastructure locale ou dans le cloud. Vous pouvez collecter des données de la machine à partir du stockage Azure lorsqu’elles sont générées par les diagnostics Azure.

En utilisant les données que vous collectez depuis le stockage Azure, vous pouvez rechercher rapidement les journaux des événements et les journaux IIS pour les services cloud et les machines virtuelles en activant les [diagnostics Azure](operational-insights-log-collection.md) Vous pouvez également obtenir plus d’informations relatives à vos machines virtuelles en installant Microsoft Monitoring Agent.

Les solutions d’évaluation des mises à jour, de suivi des modifications et d’évaluation SQL sont toutes compatibles avec Microsoft Monitoring Agent pour fournir des informations plus détaillées sur vos machines virtuelles. Si vous ne l’avez pas encore fait, vous pouvez [ajouter des solutions](operational-insights-add-solution.md) lorsque vous êtes connecté au [portail Operational Insights](https://preview.opinsights.azure.com/).

Pour les machines virtuelles Azure, il existe deux moyens simples d’activer la collecte de données basée sur les agents :

- Dans le portail de gestion Microsoft Azure

- Utiliser PowerShell

Lorsque vous utilisez la collecte basée sur les agents pour les données de journal, vous devez configurer les journaux à collecter dans la page de configuration de gestion des journaux du [portail Operational Insights](https://preview.opinsights.azure.com/)

 >[AZURE.NOTE]Si vous avez configuré Operational Insights pour indexer les données de journal à l’aide des diagnostics Azure et que vous configurez l’agent pour collecter des journaux, les mêmes journaux seront indexés deux fois. Vous serez facturé à un tarif de données normal pour les deux sources de données. Si vous avez installé l’agent, vous pouvez recueillir des données de journal à l’aide de l’agent et ne pas indexer les journaux collectés par les diagnostics Azure.

## Portail de gestion Microsoft Azure

Vous pouvez installer l'agent pour Operational Insights à partir du [portail Azure](https://manage.windowsazure.com/#Workspaces/OperationalInsightExtension/Workspaces).

### Installation de l'agent pour Operational Insights
1. Dans le portail Azure, accédez à votre espace de travail Operational Insights et sélectionnez l’onglet **Serveurs**.
2. Sous l’onglet, vous verrez une liste de vos machines virtuelles. Sélectionnez la machine virtuelle sur laquelle vous souhaitez installer l’agent, puis cliquez sur **Activer Op Insights**.

L’agent est automatiquement installé et configuré pour votre espace de travail Operational Insights.

![Image de la page Serveurs d’Operational Insights](./media/operational-insights-analyze-data-azure/servers.png)

 >[AZURE.NOTE]L’[agent de la machine virtuelle Azure](https://msdn.microsoft.com/library/azure/dn832621.aspx) doit être installé pour installer automatiquement l'agent pour Operational Insights.



## PowerShell

Si vous préférez écrire un script pour apporter des modifications à vos machines virtuelles Azure, vous pouvez activer Microsoft Monitoring Agent à l’aide de PowerShell.

Microsoft Monitoring Agent est une [extension de machine virtuelle Azure](https://msdn.microsoft.com/library/azure/dn832621.aspx) et vous pouvez la gérer à l'aide de PowerShell, comme illustré dans l'exemple ci-dessous.

```powershell
Add-AzureAccount

$workspaceId="enter workspace here"
$workspaceKey="enter workspace key here"
$hostedService="enter hosted service here"

$vm = Get-AzureVM –ServiceName $hostedService
Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'MicrosoftMonitoringAgent' -Version '1.*' -PublicConfiguration "{'workspaceId':  '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose
```

Lors de la configuration à l’aide de PowerShell, vous devez fournir l’ID et la clé primaire de l’espace de travail. L'ID et la clé primaire de votre espace de travail se trouvent sur la page **Paramètres** du portail Operational Insights.

![sources](./media/operational-insights-analyze-data-azure/sources01.png)

## Collecter des données à l'aide des diagnostics Azure

Operational Insights peut analyser les données écrites par les diagnostics Azure dans le stockage Azure. Vous devez effectuer 2 étapes principales :

- Configurer la collecte de données de diagnostic dans le stockage Azure
- Configurer Operational Insights pour analyser les données du compte de stockage

Les rubriques ci-dessous décrivent comment activer la collecte des données de diagnostic dans le stockage Azure, puis comment configurer Operational Insights pour analyser les données dans le stockage Azure.

Les diagnostics Azure sont des extensions vous permettant de collecter des données de diagnostic à partir d’un rôle de travail, d’un rôle Web ou d’une machine virtuelle exécuté dans Azure. Les données sont stockées dans un compte de stockage Azure et peuvent ensuite être utilisées par Operational Insights.

>[AZURE.NOTE]Vous serez facturé à un tarif de données normal pour le stockage et les transactions lorsque vous envoyez des diagnostics à un compte de stockage et lorsque Operational Insights opérationnelle lit les données de votre compte de stockage.

Azure Diagnostics peut collecter les types de données télémétriques suivants :

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>Source de données</b></td>
		<td><b>Description</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>Journaux IIS</td>
		<td>Informations au sujet des sites Web IIS.</td>
    </tr>
    <tr align="left" valign="top">
		<td>Journaux d'infrastructure Azure Diagnostic</td>
		<td>Informations au sujet de Diagnostics lui-même.</td>
    </tr>
	<tr align="left" valign="top">
		<td>Journaux d'échecs de requête IIS </td>
		<td>Informations au sujet des échecs de requête à un site ou à une application IIS.</td>
    </tr>
	    <tr align="left" valign="top">
		<td>Journaux d'événements Windows</td>
		<td>Informations envoyées au système de journalisation des événements Windows.</td>
    </tr>
    <tr align="left" valign="top">
		<td>Compteurs de performances</td>
		<td>Compteur du système d'exploitation et compteurs de performances personnalisés.</td>
    </tr>
    <tr align="left" valign="top">
		<td>Vidages sur incident</td>
		<td>Informations au sujet de l'état du processus en cas d'incident d'application.</td>
    </tr>
    <tr align="left" valign="top">
		<td>Journaux d'erreurs personnalisés</td>
		<td>Journaux créés par votre application ou votre service.</td>
    </tr>
    <tr align="left" valign="top">
		<td>NET EventSource</td>
		<td>Événements générés par votre code à l'aide de la classe <a href="https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx">EventSource .NET.</td>
    </tr>
    <tr align="left" valign="top">
		<td>ETW basé sur les manifestes</td>
		<td>Événements ETW générés par n'importe quel processus.</td>
    &lt;/tr
    <tr align="left" valign="top">
		<td>Syslog</td>
		<td>Événements envoyés aux démons Syslog ou Rsyslog</td>
    </tr>
    </tbody>
    </table>


Actuellement, Operational Insights est en mesure d'analyser :

- Journaux IIS des rôles Web et des machines virtuelles
- Journaux des événements Windows à partir de rôles Web, des rôles de travail et des machines virtuelles Azure exécutant un système d'exploitation Windows
- Syslog à partir des machines virtuelles Azure exécutant un système d'exploitation Linux

Les journaux doivent se trouver dans les emplacements suivants :

- WADWindowsEventLogsTable (Stockage de tables) : contient les informations des journaux des événements Windows.
- wad-iis-logfiles (Stockage d’objets blob) : contient des informations sur les journaux IIS.
- LinuxsyslogVer2v0 (Stockage de tables) : contient les événements syslog de Linux.

 >[AZURE.NOTE]Les journaux IIS des sites Web Azure ne sont actuellement pas pris en charge.

Pour les machines virtuelles, vous pouvez également installer [Microsoft Monitoring Agent](http://go.microsoft.com/fwlink/?LinkId=517269) sur votre machine virtuelle pour activer des informations supplémentaires. Cela vous permet d’analyser les journaux IIS et les journaux des événements, mais également d'effectuer des analyses supplémentaires, notamment le suivi des modifications de configuration, l’évaluation SQL et l’évaluation de la mise à jour.

Vous pouvez nous aider à hiérarchiser les journaux supplémentaires devant être analysés par Operational Insights en votant sur notre [page de commentaires](http://feedback.azure.com/forums/267889-azure-operational-insights/category/88086-log-management-and-log-collection-policy)

## Activation des diagnostics Azure dans un rôle Web pour la collecte de journaux IIS et des événements

Consultez [Activation des diagnostics dans un service cloud](https://msdn.microsoft.com/library/azure/dn482131.aspx). Vous en utiliserez les informations de base et personnaliserez les étapes pour une utilisation avec Microsoft Azure Operational Insights.

Avec les diagnostics Azure activés :

- Les journaux IIS sont stockés par défaut et les données de journal sont transférées selon l’intervalle de transfert scheduledTransferPeriod.

- Les journaux des événements Windows ne sont pas transférés par défaut.

### Activation des diagnostics

Pour activer les journaux d'événements Windows, ou pour modifier scheduledTransferPeriod, configurez les diagnostics Azure à l'aide du fichier de configuration XML (diagnostics.wadcfg), comme indiqué dans l’[étape 2 : ajoutez le fichier diagnostics.wadcfg à votre solution Visual Studio](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step2) et l’[étape 3: configurez les diagnostics pour votre application](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step3) dans la rubrique Procédure d’activation des diagnostics dans un service cloud. L’exemple de fichier de configuration suivant collecte des journaux IIS et tous les événements des journaux de l’application et du système :

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


Dans l’[étape 4 : configuration du stockage de vos données de diagnostic](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step4) de la rubrique Procédure d’activation des diagnostics dans un service cloud, assurez-vous que votre élément ConfigurationSettings spécifie un compte de stockage, comme dans l’exemple suivant :


    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>


Les valeurs de **AccountName** et **AccountKey** se trouvent dans le portail de gestion Microsoft Azure dans le tableau de bord du compte de stockage, sous Gérer les clés d’accès. Le protocole pour la chaîne de connexion doit être **https**.

Une fois que la configuration de diagnostic mise à jour est appliquée à votre service cloud et écrit des diagnostics dans Azure Storage, vous êtes prêt à configurer Operational Insights.

## Activation des diagnostics Azure dans une machine virtuelle pour la collecte des journaux IIS et des journaux des événements

Utilisez la procédure suivante pour activer les diagnostics Azure dans une machine virtuelle pour la collecte de journaux IIS et de journaux des événements à l’aide du portail de gestion Microsoft Azure.

### Activation des diagnostics Azure dans une machine virtuelle à l’aide du portail de gestion Azure

1. Installez l’agent de machine virtuelle lorsque vous créez une machine virtuelle. Si la machine virtuelle existe déjà, vérifiez que l’agent de machine virtuelle est déjà installé.
	- Si vous utilisez le portail de gestion Azure par défaut pour créer la machine virtuelle, effectuez une **Création personnalisée** et sélectionnez **Installer l’agent de machine virtuelle**.
	- Si vous utilisez le nouveau portail de gestion Azure pour créer une machine virtuelle, sélectionnez **Configuration facultative**, puis **Diagnostics** et définissez l’**État** sur **Activé**.

	Ensuite, la machine virtuelle installe et exécute automatiquement l’extension Azure Diagnostics. Cette dernière est chargée de collecter vos données de diagnostic.

2. Activation de l’analyse et configuration de la journalisation des événements sur une machine virtuelle existante. Vous pouvez activer les diagnostics au niveau de la machine virtuelle. Pour activer les diagnostics et configurer la journalisation des événements par la suite, procédez comme suit :
	1. Sélectionnez la machine virtuelle.
	2. Cliquez sur **Analyse**.
	3. Cliquez sur **Diagnostics**.
	4. Définissez l’**État** sur **ACTIVÉ**.
	5. Cliquez sur les métriques de diagnostic que vous souhaitez utiliser. Operational Insights peut analyser les journaux des événements système Windows, les journaux des événements d’application de Windows et les journaux IIS.
	7. Cliquez sur **OK**.

Vous pouvez spécifier les événements écrits dans Azure Storage plus précisément à l’aide d’Azure PowerShell. Consultez le schéma de configuration d’Azure Diagnostics 1.2 pour un exemple de fichier de configuration et une documentation détaillée sur son schéma. Veillez à installer et à configurer Azure PowerShell version 0.8.7 ou ultérieure à partir de la page [Installation et configuration d’Azure PowerShell](powershell-install-configure) Si vous disposez d’une version de Microsoft Azure Diagnostics antérieure à la version 1.2, vous ne pouvez pas utiliser le nouveau portail pour activer ou configurer les diagnostics.

Vous pouvez activer et mettre à jour l’Agent en utilisant le script PowerShell suivant. Vous pouvez également utiliser ce script avec la configuration de journalisation personnalisée. Vous devez modifier le script pour définir le compte de stockage, le nom du service et le nom de la machine virtuelle.

### Activation des diagnostics sur une machine virtuelle avec Azure PowerShell

Examinez l’exemple de script suivant, copiez-le et modifiez-le si nécessaire, enregistrez l’exemple dans un fichier de script PowerShell, puis exécutez le script.


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


## Activation de l’analyse d’Azure Storage par Operational Insights

Utilisez la procédure suivante pour activer l’analyse et configurer Operational Insights pour la lecture à partir du compte de stockage Azure avec Azure Diagnostics.

### Activation de l’analyse par Operational Insights

1. Dans le portail Azure par défaut, accédez à votre espace de travail Operational Insights et sélectionnez l’onglet **Stockage**. ![onglet de stockage de l'espace de travail](./media/operational-insights-analyze-data-azure/workspace-storage-tab.png)
2. Cliquez sur **Ajouter un compte de stockage** pour ouvrir la boîte **Ajouter un compte de stockage**.
3. Sélectionnez le compte de stockage que vous voulez utiliser.
4. Dans la liste **Type de données**, sélectionnez un type de données : **Événements**, **Journaux IIS** ou **Syslog (Linux)**.
5. Cliquez sur l’image **OK**. ![boîte du compte de stockage](./media/operational-insights-analyze-data-azure/storage-account.png)
6. Répétez les étapes ci-dessus pour chaque combinaison de type de données et de compte de stockage à partir de laquelle vous souhaitez collecter des données.

Après environ une heure, les données du compte de stockage sont disponibles pour analyse dans Operational Insights.

## Contenu connexe

- [Connexion directe des ordinateurs à Operational Insights](operational-insights-direct-agent)
- [Billet de blog : activation d’Operational Insights pour les machines virtuelles Azure](http://azure.microsoft.com/updates/easily-enable-operational-insights-for-azure-virtual-machines/)

## Étapes suivantes

[Configuration des paramètres de pare-feu et de proxy (facultatif)](../operational-insights-proxy-filewall.md)

<!---HONumber=July15_HO2-->