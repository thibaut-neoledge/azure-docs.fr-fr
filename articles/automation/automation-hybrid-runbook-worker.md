---
title: "Runbooks Workers hybrides d’Azure Automation | Microsoft Docs"
description: "Cet article fournit des informations sur l'installation et l'utilisation de la fonctionnalité Runbook Worker hybride d'Azure Automation qui vous permet d'exécuter des Runbooks sur les machines de votre centre de données local ou de votre fournisseur de cloud."
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 06227cda-f3d1-47fe-b3f8-436d2b9d81ee
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/05/2017
ms.author: magoedte;bwren
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 783fb22b0154915f2e3d8574ab95538dbd646705
ms.contentlocale: fr-fr
ms.lasthandoff: 08/09/2017

---

# <a name="automate-resources-in-your-data-center-or-cloud-with-hybrid-runbook-worker"></a>Automatiser des ressources dans votre centre de données ou votre cloud à l’aide d’un Runbook Worker hybride
Dans Azure Automation, les Runbooks ne peuvent pas accéder aux ressources d’autres clouds ou dans votre environnement local car ils s'exécutent dans le cloud Azure.  La fonctionnalité de Runbook Worker hybride d’Azure Automation vous permet d’exécuter des Runbooks directement sur l’ordinateur qui héberge le rôle et par rapport aux ressources de l’environnement afin de gérer ces ressources locales. Les Runbooks sont stockés et gérés dans Azure Automation, puis remis à un ou plusieurs ordinateurs désignés.  

Cette fonctionnalité est illustrée dans l’image suivante :<br>  

![Vue d'ensemble des Runbooks Workers hybrides](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

Pour obtenir un aperçu technique des considérations de rôle et de déploiement d’un Runbook Worker hybride, consultez [Présentation de l’architecture Automation](automation-offering-get-started.md#automation-architecture-overview).    

## <a name="hybrid-runbook-worker-groups"></a>Groupes de Runbooks Workers hybrides
Chaque Runbook Worker hybride est membre d'un groupe de Runbooks Workers hybrides que vous spécifiez lorsque vous installez l'agent.  Un groupe peut inclure un seul agent, mais vous pouvez installer plusieurs agents dans un groupe pour une haute disponibilité.

Lorsque vous démarrez un Runbook sur un Runbook Worker hybride, vous spécifiez le groupe sur lequel il s’exécute.  Les membres du groupe déterminent quel Worker traite la demande.  Vous ne pouvez pas spécifier un Worker particulier.

## <a name="relationship-to-service-management-automation"></a>Relation avec Service Management Automation
[Service Management Automation (SMA)](https://technet.microsoft.com/library/dn469260.aspx) vous permet d’exécuter les mêmes Runbooks que ceux pris en charge par Azure Automation dans votre centre de données local. SMA est généralement déployé avec Windows Azure Pack, qui contient une interface graphique de gestion de SMA. Contrairement à Azure Automation, SMA nécessite une installation locale incluant les serveurs web qui hébergent l’API, une base de données qui contient les runbooks et la configuration SMA, et les Runbook Workers qui exécutent les travaux des runbooks. Azure Automation fournit ces services dans le cloud. Il ne vous reste plus qu'à tenir à jour les Runbooks Workers hybrides dans votre environnement local.

Si vous êtes un utilisateur SMA existant, vous pouvez déplacer vos Runbooks vers Azure Automation pour qu'ils soient utilisés avec la fonctionnalité Runbook Worker hybride sans subir aucune modification, en supposant qu'ils effectuent leur propre authentification auprès des ressources, comme décrit dans la section [exécuter des Runbooks sur un Runbook Worker hybride](automation-hrw-run-runbooks.md).  Dans SMA, les Runbooks s'exécutent dans le contexte du compte de service sur le serveur de Workers qui peut fournir cette authentification pour les Runbooks.

Vous pouvez utiliser les critères suivants pour déterminer si Azure Automation avec la fonctionnalité Runbook Worker hybride ou Service Management Automation est plus adapté à vos besoins.

* SMA nécessite l’installation locale de ses composants sous-jacents connectés à Windows Azure Pack si une interface de gestion graphique est requise. Des ressources locales sont nécessaires avec des coûts de maintenance plus élevés qu’Azure Automation, qui nécessite uniquement l’installation d’un agent sur des Runbooks Workers locaux. Les agents sont gérés par Operations Management Suite, ce qui réduit encore davantage vos coûts de maintenance.
* Azure Automation stocke ses Runbooks dans le cloud et les remet à des Runbook Workers hybrides locaux. Si votre stratégie de sécurité n’autorise pas ce comportement, vous devez utiliser SMA.
* SMA est fourni avec System Center ; par conséquent, il requiert une licence System Center 2012 R2. Azure Automation est basé sur un modèle d’abonnement à plusieurs niveaux.
* Azure Automation dispose de fonctionnalités avancées, comme les runbooks graphiques, qui ne sont pas disponibles dans SMA.

## <a name="installing-the-windows-hybrid-runbook-worker"></a>Installation de la fonctionnalité Windows Runbook Worker hybride 

Il existe deux méthodes pour installer et configurer un Runbook Worker hybride Windows.  La méthode recommandée utilise un Runbook Automation afin d’automatiser totalement le processus requis pour configurer un ordinateur Windows.  La deuxième méthode consiste à suivre la procédure pas à pas pour installer et configurer le rôle manuellement.  

> [!NOTE]
> Pour gérer la configuration de vos serveurs prenant en charge le rôle Runbook Worker hybride avec configuration de l’état souhaité (DSC), vous devez les ajouter en tant que nœuds DSC.  Pour plus d’informations sur leur intégration pour les gérer avec DSC, consultez la page [Gestion de machines avec Azure Automation DSC](automation-dsc-onboarding.md).           
><br>
>Si vous activez la [solution de gestion des mises à jour](../operations-management-suite/oms-solution-update-management.md), tout ordinateur Windows connecté à votre espace de travail OMS est automatiquement configuré comme un Runbook Worker hybride pour prendre en charge les Runbooks inclus dans cette solution.  Toutefois, il n’est enregistré auprès d’aucun des groupes de Workers hybrides déjà définis dans votre compte Automation.  L’ordinateur peut être ajouté à un groupe de Runbooks Workers hybrides dans votre compte Automation pour prendre en charge des runbooks Automation à condition d’utiliser le même compte à la fois pour la solution et pour l’appartenance au groupe de Runbooks Workers hybrides.  Cette fonctionnalité a été ajoutée à la version 7.2.12024.0 du groupe de Runbooks Workers hybrides.  

Consultez les informations suivantes concernant les [exigences matérielles et logicielles requise](automation-offering-get-started.md#hybrid-runbook-worker) et les [informations pour préparer votre réseau](automation-offering-get-started.md#network-planning) avant de commencer le déploiement d’un Runbook Worker hybride.  Une fois que vous avez déployé un runbook worker avec succès, consultez [exécuter des runbooks sur un Runbook Worker hybride](automation-hrw-run-runbooks.md) pour apprendre comment configurer vos runbooks afin d’automatiser les processus dans votre centre de données local ou un autre environnement cloud.  
 
### <a name="automated-deployment"></a>Déploiement automatisé

Effectuez les étapes suivantes pour automatiser l’installation et la configuration du rôle Worker hybride Windows.  

1. Téléchargez le script *New-OnPremiseHybridWorker.ps1* à partir de [PowerShell Gallery](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/1.0/DisplayScript) directement depuis l’ordinateur qui exécute le rôle Runbook Worker hybride ou depuis un autre ordinateur dans votre environnement, et copiez-le dans le Worker.  

    Le script *New-OnPremiseHybridWorker.ps1* requiert les paramètres suivants lors de l’exécution :

  * *AutomationAccountName* (obligatoire) : nom de votre compte Automation.  
  * *ResourceGroupName* (obligatoire) - le nom du groupe de ressources associé à votre compte Automation.  
  * *HybridGroupName* (obligatoire) - le nom d’un groupe de Runbooks Workers hybrides que vous spécifiez comme cible pour les Runbooks prenant en charge ce scénario. 
  *  *SubscriptionID* (obligatoire) - l’ID de l’abonnement Azure dans lequel se trouve votre compte Automation.
  *  *WorkspaceName* (facultatif) : le nom d’espace de travail OMS.  Si vous n’avez pas d’espace de travail OMS, le script en crée un et le configure.  

     > [!NOTE]
     > Actuellement, les seules régions Automation prises en charge pour l’intégration avec OMS sont : **Sud-Est de l’Australie**, **Est des États-Unis 2**, **Sud-Est asiatique** et **Europe de l’Ouest**.  Si votre compte Automation ne se trouve pas dans une de ces régions, le script crée un espace de travail OMS, mais vous avertit qu’il ne peut pas lier les deux.
     > 
2. Sur votre ordinateur, démarrez **Windows PowerShell** à partir de l’écran **Démarrer** en mode Administrateur.  
3. Dans l’interface de ligne de commande PowerShell, accédez au dossier qui contient le script que vous avez téléchargé, puis exécutez-le en modifiant les valeurs des paramètres *-AutomationAccountName*, *-ResourceGroupName*, *-HybridGroupName*, *-SubscriptionId* et *-WorkspaceName*.

     > [!NOTE] 
     > Une fois le script exécuté, vous êtes invité à vous authentifier auprès d’Azure.  Vous **devez** vous connecter avec un compte membre du rôle Administrateurs des abonnements et coadministrateur de l’abonnement.  
     >  
    
        .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> `
        -ResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
        -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfOMSWorkspace>

4. Vous êtes invité à accepter d’installer **NuGet** et à vous authentifier avec vos informations d’identification Azure.<br><br> ![Exécution du script New-OnPremiseHybridWorker](media/automation-hybrid-runbook-worker/new-onpremisehybridworker-scriptoutput.png)

5. Une fois le script terminé, le panneau Groupes de Workers hybrides affiche le nouveau groupe et le nombre de membres, ou, si le groupe existe déjà, incrémente le nombre de membres.  Vous pouvez sélectionner le groupe dans la liste du panneau **Groupes de Workers hybrides** et sélectionner la vignette **Workers hybrides**.  Le panneau **Workers hybrides** affiche chaque membre du groupe listé.  

### <a name="manual-deployment"></a>Déploiement manuel 
Effectuez les deux premières étapes une fois pour votre environnement Automation, puis répétez les étapes restantes pour chaque ordinateur Worker.

#### <a name="1-create-operations-management-suite-workspace"></a>1. Création d’un espace de travail Operations Management Suite
Si vous ne disposez pas déjà d’un espace de travail Operations Management Suite, créez-en un en suivant les instructions mentionnées à la page [Gérer votre espace de travail](../log-analytics/log-analytics-manage-access.md). Vous pouvez utiliser un espace de travail existant si vous en avez déjà un.

#### <a name="2-add-automation-solution-to-operations-management-suite-workspace"></a>2. Ajout de la solution Automation à l’espace de travail Operations Management Suite
Les solutions ajoutent des fonctionnalités à Operations Management Suite.  La solution Automation ajoute des fonctionnalités à Azure Automation, notamment la prise en charge des Runbooks Workers hybrides.  Lorsque vous ajoutez la solution à votre espace de travail, les composants Worker sont automatiquement transférés à l’ordinateur agent que vous installerez à l’étape suivante.

Suivez les instructions de la page [Pour ajouter une solution à l’aide de la galerie de solutions](../log-analytics/log-analytics-add-solutions.md) pour ajouter la solution **Automation** à votre espace de travail Operations Management Suite.

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3. Installation de Microsoft Monitoring Agent
Microsoft Monitoring Agent connecte les ordinateurs à Operations Management Suite.  Lorsque vous installez l’agent sur votre ordinateur local et que vous le connectez à votre espace de travail, il télécharge automatiquement les composants requis pour le Runbook Worker hybride.

Suivez les instructions de la page [Connecter des ordinateurs Windows à Log Analytics](../log-analytics/log-analytics-windows-agents.md) pour installer l’agent sur l’ordinateur local.  Vous pouvez répéter ce processus pour plusieurs ordinateurs afin d’ajouter plusieurs Workers à votre environnement.

Lorsque l’agent parvient à se connecter à Operations Management Suite, il est répertorié dans l’onglet **Sources connectées** du volet **Paramètres** d’Operations Management Suite.  Vous pouvez vérifier que l’agent a correctement téléchargé la solution Automation lorsqu’un dossier appelé **AzureAutomationFiles** figure dans C:\Program Files\Microsoft Monitoring Agent\Agent.  Pour valider la version du Runbook Worker hybride, vous pouvez accéder à C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\ et consulter le sous-dossier \\*version*.   

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Installer l'environnement de Runbook et se connecter à Azure Automation
Lorsque vous ajoutez un agent à Operations Management Suite, la solution Automation lance le module PowerShell **HybridRegistration**, qui contient l’applet de commande **Add-HybridRunbookWorker**.  Vous utilisez cette applet de commande pour installer l'environnement de Runbook sur la machine et l'inscrire auprès d'Azure Automation.

Ouvrez une session PowerShell en mode administrateur et exécutez les commandes suivantes pour importer le module.

    cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
    Import-Module HybridRegistration.psd1

Exécutez ensuite l'applet de commande **Add-HybridRunbookWorker** en utilisant la syntaxe suivante :

    Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>

Vous pouvez obtenir les informations requises pour cette applet de commande dans le panneau **Gérer les clés** du portail Azure.  Ouvrez ce panneau en sélectionnant l’option **Clés** sur le panneau **Paramètres** de votre compte Automation.

![Vue d'ensemble des Runbooks Workers hybrides](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **GroupName** est le nom du groupe de Runbooks Workers hybrides. Si ce groupe existe déjà dans le compte Automation, la machine actuelle est ajoutée à ce dernier.  S'il n'existe pas encore, il est alors ajouté.
* **EndPoint** est le champ **URL** dans le panneau **Gérer les clés**.
* **Token** est la **clé d’accès primaire** dans le panneau **Gérer les clés**.  

Utilisez le commutateur **-Verbose** avec **Add-HybridRunbookWorker** pour recevoir des informations détaillées concernant l’installation.

#### <a name="5-install-powershell-modules"></a>5. Installer des modules PowerShell
Les Runbooks peuvent utiliser toutes les activités et applets de commande définies dans les modules installés dans votre environnement Azure Automation.  Toutefois, comme ces modules ne sont pas automatiquement déployés sur les ordinateurs locaux, vous devez les installer manuellement.  L’exception est le module Azure, qui est installé par défaut et qui permet d’accéder aux applets de commande de l’ensemble des services et activités Azure pour Azure Automation.

Étant donné que l’objectif principal de la fonctionnalité Runbook Worker hybride est de gérer les ressources locales, vous devez probablement installer les modules qui prennent en charge ces ressources.  Vous pouvez vous reporter à la section [Installation de modules](http://msdn.microsoft.com/library/dd878350.aspx) pour obtenir des informations sur l'installation de modules Windows PowerShell.  Les modules installés doivent se trouver dans un emplacement référencé par la variable d’environnement PSModulePath afin d’être importés automatiquement par le Worker hybride.  Pour plus d’informations, consultez la page [Modifier le chemin d’Installation de PSModulePath](https://msdn.microsoft.com/library/dd878326%28v=vs.85%29.aspx). 

## <a name="installing-linux-hybrid-runbook-worker"></a>Installation de la fonctionnalité Runbook Worker hybride Linux
L’installation et la configuration d’un Runbook Worker hybride sur Linux est une procédure très simple qui permet d’installer et de configurer le rôle manuellement.  Cela nécessite l’activation de la solution **Automation Hybrid Worker** dans votre espace de travail OMS, puis l’exécution d’un ensemble de commandes pour inscrire l’ordinateur en tant que Worker et l’ajouter à un groupe nouveau ou existant. 

1.  Activez la solution Automation Hybrid Worker dans OMS. Pour ce faire, vous pouvez :

   1. À partir de la Galerie de solutions du [portail OMS](https://mms.microsoft.com), activer la solution **Automation Hybrid Worker**
   2. Exécutez l’applet de commande suivante :

        ```$null = Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```
2.  Run the following command with the proper parameters (endpoint and key can be taken from the portal from the automation account linked to the workspace used in the steps above):
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <OMSworkspaceId> -k <automationsharedkey> --groupname <hybridgroupname> -e <automationendpoint>


## Removing Hybrid Runbook Worker 
You can remove one or more Hybrid Runbook Workers from a group or you can remove the group, depending on your requirements.  To remove a Hybrid Runbook Worker from an on-premises computer, perform the following steps.

1. In the Azure portal, navigate to your Automation account.  
2. From the **Settings** blade, select **Keys** and note the values for field **URL** and **Primary Access Key**.  You need this information for the next step.
3. Open a PowerShell session in Administrator mode and run the following command - `Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>`.  Use the **-Verbose** switch for a detailed log of the removal process.

> [!NOTE]
> This does not remove the Microsoft Monitoring Agent from the computer, only the functionality and configuration of the Hybrid Runbook Worker role.  

## Remove Hybrid Worker groups
To remove a group, you first need to remove the Hybrid Runbook Worker from every computer that is a member of the group using the procedure shown earlier, and then you perform the following steps to remove the group.  

1. Open the Automation account in the Azure portal.
2. Select the **Hybrid Worker Groups** tile and in the **Hybrid Worker Groups** blade, select the group you wish to delete.  After selecting the specific group, the **Hybrid worker group** properties blade is displayed.<br> ![Hybrid Runbook Worker Group Blade](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)   
3. On the properties blade for the selected group, click **Delete**.  A message appears asking you to confirm this action, select **Yes** if you are sure you want to proceed.<br> ![Delete Group Confirmation Dialog](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)<br> This process can take several seconds to complete and you can track its progress under **Notifications** from the menu.  

## Troubleshooting 
The Hybrid Runbook Worker depends on the Microsoft Monitoring Agent to communicate with your Automation account to register the worker, receive runbook jobs, and report status. If  registration of the worker fails, here are some possible causes for the error:  

1. The hybrid worker is behind a proxy or firewall.  
    Verify the computer has outbound access to *.azure-automation.net on port 443.  

2. The computer the hybrid worker is running on has less than the minimum hardware [requirements](automation-offering-get-started.md#hybrid-runbook-worker).  
    Computers running the Hybrid Runbook Worker should meet the minimum hardware requirements before designating it to host this feature. Otherwise, depending on the resource utilization of other background processes and contention caused by runbooks during execution, the computer will become over utilized and cause runbook job delays or timeouts.
    Confirm the computer designated to run the Hybrid Runbook Worker feature meets the minimum hardware requirements.  If it does, monitor CPU and memory utilization to determine any correlation between the performance of Hybrid Runbook Worker processes and Windows.  If there is memory or CPU pressure, this may indicate the need to upgrade or add additional processors, or increase memory to address the resource bottleneck and resolve the error. Alternatively, select a different compute resource that can support the minimum requirements and scale when workload demands indicate an increase is necessary.
    
3. The Microsoft Monitoring Agent service is not running.  
    If the Microsoft Monitoring Agent Windows service is not running, this prevents the Hybrid Runbook Worker from communicating with Azure Automation.  Verify the agent is running by entering the following command in PowerShell: `get-service healthservice`.  If the service is stopped, enter the following command in PowerShell to start the service: `start-service healthservice`.  

4. In the **Application and Services Logs\Operations Manager** event log, you see event 4502  and EventMessage containing **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent** with the following description:  *The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication. The article KB3126513 has additional troubleshooting information for connectivity issues.*
    This can be caused by your proxy or network firewall blockking communication to Microsoft Azure.  Verify the computer has outbound access to *.azure-automation.net on ports 443.

Logs are stored locally on each hybrid worker at C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes.  You can check if there are any warning or error events written to the **Application and Services Logs\Microsoft-SMA\Operations** and **Application and Services Logs\Operations Manager** event log that would indicate a connectivity or other issue affecting onboarding of the role to Azure Automation or issue while performing normal operations.  

## Next steps
Review [run runbooks on a Hybrid Runbook Worker](automation-hrw-run-runbooks.md) to learn how to configure your runbooks to automate processes in your on-premises datacenter or other cloud environment.

