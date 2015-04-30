<properties 
   pageTitle="Collecte des données de l'ordinateur" 
   description="Collecte des données de l'ordinateur à partir des serveurs dans votre infrastructure cloud ou sur site" 
   services="operational-insights" 
   documentationCenter="" 
   authors="bandersmsft" 
   manager="jwhit" 
   editor="bandersmsft"/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/20/2015"
   ms.author="banders"/>
#Collecte des données de l'ordinateur

Operational Insights utilise des données des serveurs dans votre infrastructure cloud ou sur site. Vous pouvez collecter des données de l'ordinateur à partir des sources suivantes :

- Agents Operations Manager
- Ordinateurs directement connectés à Operational Insights
- Données de diagnostic de machine virtuelle dans les services Azure Storage

Une fois que les données sont collectées, elles sont envoyées au service Operational Insights.

## Connexion à Operational Insights à partir de System Center Operations Manager 

Vous pouvez connecter Operational Insights à un environnement System Center Operations Manager existant. Cela vous permet d'utiliser les agents d'Operations Manager existants en tant qu'agents Operational Insights. Pour plus d'informations sur l'utilisation d'Operations Manager avec Operational Insights, consultez [Considérations relatives à Operations Manager avec Operational Insights](operational-insights-operations-manager.md).

Si vous utilisez Operations Manager pour surveiller les charges de travail suivantes, vous devez définir des comptes d'identification Operations Manager. Consultez [comptes d'identification Operations Manager pour Operational Insights](operational-insights-run-as.mdpour obtenir plus d'informations sur la configuration des comptes.

- SQL Assessment
- Virtual Machine Manager
- Lync Server
- SharePoint

 >[AZURE.NOTE] La prise en charge d'Operational Insights est disponible à partir d'Operations Manager 2012 SP1 UR6 et Operations Manager 2012 R2 UR2. La prise en charge du proxy a été ajoutée dans System Center Operations Manager 2012 SP1 UR7 et System Center Operations Manager 2012 R2 UR3.

#### Connexion d'Operations Manager à Operational Insights et ajout d'agents

1. Dans la console Operations Manager, cliquez sur **Administration**.

2. Développez le nœud **Operational Insights** et cliquez sur **Connexion à Operational Insights**.

3. Cliquez sur le lien **Inscription à Operational Insights** et suivez les instructions qui s'affichent. 

4. Après avoir terminé l'Assistant Inscription, cliquez sur **Ajouter un ordinateur/groupe**.

5. Vous pouvez rechercher des ordinateurs ou des groupes surveillés par Operations Manager dans la boîte de dialogue **Recherche d'ordinateurs**. Sélectionnez les ordinateurs ou les groupes à intégrer à Operational Insights, cliquez sur **Ajouter**, puis sur **OK**.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## Connexion directe des ordinateurs à Operational Insights 

Vous pouvez connecter les ordinateurs directement à Operational Insights en installant l'agent Operational Insights sur chaque ordinateur à connecter. 


###Téléchargement et installation de l'agent

####Téléchargement du fichier de configuration de l'agent
1. Dans le **portail Operational Insights**, sur la page **Présentation**, cliquez sur **Serveurs et utilisation**.
1. Sous **Serveurs directement connectés**, cliquez sur **Configurer**.
1. À côté de **Ajouter des agents**, cliquez sur le lien de l'agent pour télécharger le fichier d'installation.
1. Dans la zone **Clé de l'espace de travail primaire**, sélectionnez la clé et copiez-la (CTRL + C).


#### Installation de l'agent à l'aide du programme d'installation
1. Exécutez le programme d'installation pour installer l'agent sur un ordinateur que vous souhaitez gérer.
1. Sélectionnez **Connecter l'agent à Microsoft Azure Operational Insights**, puis cliquez sur **Suivant**.
1. Lorsque vous y êtes invité, entrez les informations que vous avez copiées à l'étape 4.
1. Lorsque vous avez terminé, l'**agent de gestion Microsoft** apparaît dans le **Panneau de configuration**.

#### Installation de l'agent à l'aide de la ligne de commande
Modifiez, puis utilisez l'exemple suivant pour installer l'agent à l'aide de la ligne de commande.

    MMASetup-AMD64.exe /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"

### Configuration du Microsoft Monitoring Agent (facultatif)
Utilisez les informations suivantes pour permettre à un agent de communiquer directement avec le service Microsoft Azure Operational Insights. Une fois que vous avez configuré l'agent, il s'inscrira auprès du service de l'agent et obtiendra les informations de configuration nécessaires, ainsi que les packs d'administration contenant les informations du pack d'analyse décisionnelle.

Une fois que les données des ordinateurs analysés sont collectées par l'agent, le nombre d'ordinateurs analysés apparaîtra dans le portail Operational Insights sur la page Utilisation, sous **Agents directement connectés**. Vous pouvez afficher les données et les informations d'évaluation de tout ordinateur envoyant des données dans le portail Operational Insights.

Vous pouvez également désactiver l'agent, si nécessaire, ou l'autoriser à l'aide de la ligne de commande ou du script.

#### Configuration de l'agent
- Après avoir installé l'agent **Microsoft Monitoring Agent**, ouvrez le **Panneau de configuration**.
- Ouvrez l'agent Microsoft Monitoring Agent, puis cliquez sur l'onglet Azure Operational Insights.
- Sélectionnez **Connexion à Azure Operational Insights**.
- Dans la zone **ID de l'espace de travail**, tapez l'ID de l'espace de travail fourni dans le portail Operational Insights.
- Dans la zone de Clé de compte, collez la **Clé de l'espace de travail primaire** que vous avez copiée lors de l'installation de l'agent, puis cliquez sur **OK**.

#### Désactivation d'un agent
- Après avoir installé l'agent, ouvrez le **Panneau de configuration**.
- Ouvrez l'agent Microsoft Monitoring Agent, puis cliquez sur l'onglet **Azure Operational Insights**.
- Supprimez la connexion à **Azure Operational Insights**.

#### Activation de l'agent à l'aide de la ligne de commande ou du script
Vous pouvez utiliser Windows PowerShell ou un script VB avec l'exemple suivant.

    $healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
    $healthServiceSettings.EnableOnlineMonitoring('workspacename', 'workspacekey')
    $healthServiceSettings.ReloadConfiguration()
    



### Configuration des paramètres de pare-feu et de proxy (facultatif)
Si vous avez des serveurs proxy ou un pare-feu qui limitent l'accès à Internet dans votre environnement, vous devez suivre les procédures suivantes pour permettre à Operations Manager et/ou aux agents de communiquer avec le service Operational Insights 



- [Configuration des paramètres de pare-feu et de proxy (facultatif)](operational-insights-proxy-filewall.md) 

## Analyse des données des serveurs dans Microsoft Azure

Avec Operational Insights, vous pouvez rechercher rapidement les journaux des événements et les journaux IIS pour les services cloud et les machines virtuelles en activant [Diagnostics dans Azure Cloud Services](operational-insights-log-collection.md). Vous pouvez également obtenir plus d'informations relatives à vos machines virtuelles en installant Microsoft Monitoring Agent.

Les packs d'analyse décisionnelle d'évaluation des mises à jour, de suivi des modifications et d'évaluation SQL sont tous compatibles avec Microsoft Monitoring Agent pour fournir des informations plus poussées sur vos machines virtuelles. Si vous ne l'avez pas encore fait, vous pouvez activer ces packs d'analyse décisionnelle lorsque vous êtes connecté au [portail Operational Insights](https://preview.opinsights.azure.com/).

Pour les machines virtuelles Azure, il existe deux moyens simples d'activer la collecte de données basée sur les agents :

- Dans le portail de gestion Microsoft Azure

- Utiliser PowerShell

Lorsque vous utilisez la collecte basée sur les agents pour les données de journal, vous devez configurer les journaux à collecter dans la page de configuration de gestion des journaux du [portail Operational Insights](https://preview.opinsights.azure.com/).

Si vous avez configuré Operational Insights pour indexer les données de journal à l'aide des diagnostics dans Azure Cloud Services et que vous configurez l'agent pour collecter des journaux, les mêmes journaux des événements auront deux copies des données indexées. Si vous avez installé l'agent, vous devez recueillir des données à l'aide de l'agent et ne pas indexer les journaux collectés par les diagnostics dans Azure Cloud Services.

### Portail de gestion Microsoft Azure

Dans le [portail Operational Insights](https://preview.opinsights.azure.com/), accédez à votre espace de travail Operational Insights et sélectionnez l'onglet Serveurs. Sous l'onglet, vous verrez une liste de vos machines virtuelles. Sélectionnez la machine virtuelle sur laquelle vous souhaitez installer l'agent, puis sur **Activer Op Insights**.

L'agent est installé et configuré pour votre espace de travail Operational Insights.

![Image de la page Serveurs d'Operational Insights](./media/operational-insights-collect-data/servers.png)

### PowerShell

Si vous préférez écrire un script pour apporter des modifications à vos machines virtuelles Azure, vous pouvez activer Microsoft Monitoring Agent à l'aide de PowerShell.

Microsoft Monitoring Agent est une [extension de machine virtuelle Azure](https://msdn.microsoft.com/library/azure/dn832621.aspx) et peut la gérer à l'aide de PowerShell, comme illustré dans l'exemple ci-dessous.

    Add-AzureAccount
    
    $workspaceId="enter workspace here"
    $workspaceKey="enter workspace key here"
    $hostedService="enter hosted service here"
    
    $vm = Get-AzureVM -ServiceName $hostedService
    Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'MicrosoftMonitoringAgent' -Version '1.*' -PublicConfiguration "{'workspaceId':  '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose

Lors de la configuration à l'aide de PowerShell, vous devez fournir l'ID et la clé de l'espace de travail. L'ID et la clé de votre espace de travail se trouvent sur la page Serveurs et Utilisation du portail Operational Insights.

![Image de la configuration d'agent direct d'Operational Insights](./media/operational-insights-collect-data/direct-agent-cfg.png)

### Collecte de données à l'aide des diagnostics dans Azure Cloud Services

Operational Insights peut analyser les données écrites par les diagnostics pour Azure Cloud Services et les services Azure Storage. Vous devez effectuer 2 étapes principales :

- Configurer la collecte de données de diagnostic dans le stockage Azure
- Configurer Operational Insights pour analyser les données du compte de stockage

Les rubriques ci-dessous décrivent comment activer la collecte des données de diagnostic dans le stockage Azure, puis comment configurer Operational Insights pour analyser les données dans le stockage Azure.

Azure Diagnostics sont des extensions vous permettant de collecter des données télémétriques de diagnostic à partir d'un rôle de travail, d'un rôle Web ou d'une machine virtuelle exécuté dans Azure. Les données de télémétrie sont stockées dans un compte de stockage Azure et peuvent ensuite être utilisées par Operational Insights.

>[AZURE.NOTE] Des frais de données normaux vous sont facturés pour le stockage et les transactions lorsque vous envoyez des diagnostics à un compte de stockage.

Azure Diagnostics peut collecter les types de données télémétriques suivants :

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>Source de données</b></td>
		<td><b>

Description </b></td>
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
		<td>Événements générés par votre code à l'aide de la <a href="https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx">classe EventSource/a> .NET.</td>
    </tr>
    <tr align="left" valign="top">
		<td>ETW basé sur les manifestes</td>
		<td>Événements ETW générés par n'importe quel processus.</td>
    </tr>
    </tbody>
    </table>


Actuellement, Operational Insights peut analyser les journaux IIS des rôles Web et les journaux des événements Windows à partir des machines virtuelles et des rôles Web et de travail. Les journaux doivent se trouver dans les emplacements suivants :

- WADWindowsEventLogsTable (Stockage de tables) : contient les informations des journaux des événements Windows.

- wad-iis-logfiles (Stockage d'objets blob) : contient des informations sur les journaux IIS.

Pour les machines virtuelles, vous pouvez également installer [Microsoft Monitoring Agent](http://go.microsoft.com/fwlink/?LinkId=517269) dans votre machine virtuelle. Cela vous permet d'analyser les journaux IIS et les journaux des événements, mais également d'effectuer des analyses supplémentaires, notamment le suivi des modifications de configuration et l'évaluation des mises à jour. Activation des analyses supplémentaires en installant [Microsoft Monitoring Agent](http://go.microsoft.com/fwlink/?LinkId=517269).

>[AZURE.NOTE] Les journaux IIS des sites Web Azure ne sont actuellement pas pris en charge.

Vous pouvez nous aider à hiérarchiser les journaux supplémentaires à analyser par Operational Insights, en votant sur notre [page de commentaires](http://feedback.azure.com/forums/267889-azure-operational-insights/category/88086-log-management-and-log-collection-policy).

### Activation des diagnostics Azure dans un rôle Web pour la collecte de journaux IIS et des événements

Consultez [Activation de Diagnostics dans un service cloud](https://msdn.microsoft.com/library/azure/dn482131.aspx). Vous en utiliserez les informations de base et personnaliserez les étapes pour une utilisation avec Microsoft Azure Operational Insights.

Avec les diagnostics Azure activés :

- Les journaux IIS sont stockés par défaut et les données de journal sont transférées selon l'intervalle de transfert scheduledTransferPeriod.

- Les journaux des événements Windows ne sont pas transférés par défaut.

#### Activation des diagnostics

Pour activer les journaux d'événements Windows, ou pour modifier l'intervalle scheduledTransferPeriod, configurez Azure Diagnostics à l'aide du fichier de configuration XML (diagnostics.wadcfg), comme indiqué dans l'[étape 2 : ajout du fichier diagnostics.wadcfg à votre solution Visual Studio](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step2) et dans l'[étape 3 : configuration des diagnostics pour votre application](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step3) dans la rubrique Activation de Diagnostics dans un service cloud. L'exemple de fichier de configuration suivant collecte les journaux IIS et tous les événements des journaux système et d'application :

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


Dans l'[Étape 4 : configuration du stockage de vos données de diagnostic](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step4) de la rubrique Activation des diagnostics dans un service cloud, assurez-vous que votre élément ConfigurationSettings spécifie un compte de stockage, comme dans l'exemple suivant :


    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>


Les valeurs de **AccountName** et **AccountKey** se trouvent dans le portail de gestion Microsoft Azure dans le tableau de bord du compte de stockage, sous Gérer les clés d'accès. Le protocole pour la chaîne de connexion doit être **https**.

Une fois que la configuration de diagnostic mise à jour est appliquée à votre service cloud et écrit des diagnostics dans Azure Storage, vous êtes prêt à configurer Operational Insights.

### Activation des diagnostics Azure Cloud Services dans une machine virtuelle pour la collecte de journaux IIS et des événements

Utilisez la procédure suivante pour activer les diagnostics Azure Cloud Services dans une machine virtuelle pour la collecte de journaux IIS et des événements à l'aide du portail de gestion Microsoft Azure.

#### Activation des diagnostics Azure Cloud Services dans une machine virtuelle via le portail de gestion Azure

1. Installez l'agent de machine virtuelle lorsque vous créez une machine virtuelle. Si la machine virtuelle existe déjà, vérifiez que l'agent de machine virtuelle est déjà installé.
	- Si vous utilisez le portail de gestion Azure par défaut pour créer la machine virtuelle, effectuez une **Création personnalisée** et sélectionnez **Installer l'agent de machine virtuelle**.
	- Si vous utilisez le nouveau portail de gestion Azure pour créer une machine virtuelle, sélectionnez **Configuration facultative**, puis **Diagnostics** et définissez l'**État** sur **Activé**.
	
	Ensuite, la machine virtuelle installe et exécute automatiquement l'extension Azure Diagnostics. Cette dernière est chargée de collecter vos données de diagnostic.

2. Activation de l'analyse et configuration de la journalisation des événements sur une machine virtuelle existante. Vous pouvez activer les diagnostics au niveau de la machine virtuelle. Pour activer les diagnostics et configurer la journalisation des événements par la suite, procédez comme suit :
	1. Sélectionnez la machine virtuelle.
	2. Cliquez sur **Analyse**.
	3. Cliquez sur **Diagnostics**.
	4. Définissez l'**État** comme **ACTIVÉ**.
	5. Cliquez sur les métriques de diagnostic que vous souhaitez utiliser. Operational Insights peut analyser les journaux des événements système Windows, les journaux des événements d'application de Windows et les journaux IIS.
	7. Cliquez sur **OK**.

Vous pouvez spécifier les événements écrits dans Azure Storage plus précisément à l'aide d'Azure PowerShell. Consultez le schéma de configuration d'Azure Diagnostics 1.2 pour un exemple de fichier de configuration et une documentation détaillée sur son schéma. Veillez à installer et à configurer Azure PowerShell version 0.8.7 ou ultérieure à partir de la page [Installation et configuration d'Azure PowerShell](install-configure-powershell/). Si vous disposez d'une version de Microsoft Azure Diagnostics antérieure à la version 1.2, vous ne pouvez pas utiliser le nouveau portail pour activer ou configurer les diagnostics.

Vous pouvez activer et mettre à jour l'Agent en utilisant le script PowerShell suivant. Vous pouvez également utiliser ce script avec la configuration de journalisation personnalisée. Vous devez modifier le script pour définir le compte de stockage, le nom du service et le nom de la machine virtuelle.

#### Activation des diagnostics sur une machine virtuelle avec Azure PowerShell

Examinez l'exemple de script suivant, copiez-le et modifiez-le si nécessaire, enregistrez l'exemple dans un fichier de script PowerShell, puis exécutez le script.

    # Connexion à Azure
    Add-AzureAccount 
     
    # paramètres à modifier :
    $wad_storage_account_name = "myStorageAccount"
    $service_name = "myService"
    $vm_name = "myVM"
     
    #Construction de la configuration publique d'Azure Diagnostics et conversion au format config 
    
    # Collecter uniquement les événements d'erreur système :
    $wad_xml_config = "<WadCfg><DiagnosticMonitorConfiguration><WindowsEventLog scheduledTransferPeriod=""PT1M""><DataSource name=""System!* "" /></WindowsEventLog></DiagnosticMonitorConfiguration></WadCfg>"
    
    $wad_b64_config = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($wad_xml_config))
    $wad_public_config = [string]::Format("{{""xmlCfg"":""{0}""}}",$wad_b64_config)
    
    #Construction de la configuration privée des diagnostics Azure
    
    $wad_storage_account_key = (Get-AzureStorageKey $wad_storage_account_name).Primary
    $wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)
    
    #Activation de l'extension Diagnostics pour la machine virtuelle
    
    $wad_extension_name = "IaaSDiagnostics"
    $wad_publisher = "Microsoft.Azure.Diagnostics"
    $wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Version # Obtient la dernière version de l'extension
    
    (Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM
    

### Activation de l'analyse d'Azure Storage par Operational Insights

Dans le portail Azure par défaut, accédez à votre espace de travail Operational Insights et sélectionnez l'onglet **Stockage**. Utilisez les informations suivantes pour configurer Operational Insights pour la lecture à partir du compte Azure Storage avec Azure Diagnostics.

#### Activation de l'analyse par Operational Insights

1. Cliquez sur **Ajouter** pour ouvrir la zone **Ajouter un compte de stockage**.

2. Sélectionnez le **Compte de stockage**.

3. Sélectionnez un type de données, soit **Journaux d'événements**, soit **Journaux IIS**.

4. Cliquez sur **OK**.

5. Répétez les étapes ci-dessus pour chaque combinaison de type de données et de compte de stockage à partir de laquelle vous souhaitez collecter des données.

Après environ une heure, les données du compte de stockage sont disponibles pour analyse dans Operational Insights.




## Contenu connexe

- [Billet de blog : connexion directe des serveurs à Operational Insights](http://blogs.technet.com/b/momteam/archive/2015/01/20/connect-servers-directly-to-operational-insights.aspx)
- [Billet de blog : activation d'Operational Insights pour les machines virtuelles Azure](http://azure.microsoft.com/updates/easily-enable-operational-insights-for-azure-virtual-machines/)

## Étapes suivantes

[Configuration des paramètres de pare-feu et de proxy (facultatif)](operational-insights-proxy-filewall.md)



<!--HONumber=52-->