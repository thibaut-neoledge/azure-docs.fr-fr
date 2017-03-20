---
title: "Runbooks Workers hybrides d’Azure Automation | Microsoft Docs"
description: "Cet article fournit des informations sur l&quot;installation et l&quot;utilisation de la fonctionnalité Runbook Worker hybride d&quot;Azure Automation qui vous permet d&quot;exécuter des Runbooks sur les machines de votre centre de données local."
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: tysonn
ms.assetid: 06227cda-f3d1-47fe-b3f8-436d2b9d81ee
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: ca145339ab14ef29851b53deca9be9ad137317c9
ms.openlocfilehash: 77d3a4114b23114c0b4bf97a461cee2356d66a4a
ms.lasthandoff: 03/01/2017

---

# <a name="automate-resources-in-your-data-center-with-hybrid-runbook-worker"></a>Automatiser des ressources dans votre centre de données à l’aide d’un Runbook Worker hybride
Dans Azure Automation, les Runbooks ne peuvent pas accéder aux ressources de votre centre de données local, car ils s'exécutent dans le cloud Azure.  La fonctionnalité Runbook Worker hybride d'Azure Automation vous permet d'exécuter des Runbooks sur des machines situées dans votre centre de données afin de gérer les ressources locales. Les Runbooks sont stockés et gérés dans Azure Automation, puis remis à un ou plusieurs ordinateurs locaux.  

Cette fonctionnalité est illustrée dans l’image suivante.<br>  

![Vue d'ensemble des Runbooks Workers hybrides](media/automation-hybrid-runbook-worker/automation.png)

Vous pouvez désigner un ou plusieurs ordinateurs de votre centre de données pour qu'elles jouent le rôle de Runbook Worker hybride et exécutent les Runbooks à partir d'Azure Automation.  Chaque Worker nécessite Microsoft Management Agent avec une connexion à Microsoft Operations Management Suite et l’environnement de Runbook Azure Automation.  Operations Management Suite est utilisé uniquement pour installer et gérer l’agent de gestion, ainsi que pour surveiller la fonctionnalité du Worker.  La remise de Runbooks et l'instruction visant à les exécuter sont effectuées par Azure Automation.

Il n'existe aucune exigence relative aux pare-feu entrants pour prendre en charge les Runbooks Workers hybrides. L'agent sur la machine locale établit toutes les communications avec Azure Automation dans le cloud. Lorsqu'un Runbook est démarré, Azure Automation crée une instruction qui est récupérée par l'agent. L'agent extrait alors le Runbook et tous les paramètres avant de l'exécuter.  Il récupère également toutes les [ressources](http://msdn.microsoft.com/library/dn939988.aspx) qui sont utilisées par le Runbook dans Azure Automation.

> [!NOTE]
> Pour gérer la configuration de vos serveurs prenant en charge le rôle Runbook Worker hybride avec configuration de l’état souhaité (DSC), vous devez les ajouter en tant que nœuds DSC.  Pour obtenir plus d’informations sur leur intégration pour les gérer avec DSC, consultez [Gestion de machines avec Azure Automation DSC](automation-dsc-onboarding.md).           
><br>
>Si vous activez la [solution Gestion des mises à jour](../operations-management-suite/oms-solution-update-management.md), tout ordinateur Windows connecté à votre espace de travail OMS sera automatiquement configuré comme un Runbook Worker hybride pour prendre en charge les runbooks inclus dans cette solution.  Toutefois, il n’est inscrit avec aucun groupe de Workers hybrides que vous avez déjà défini dans votre compte Automation.  L’ordinateur peut être ajouté à un groupe de Runbooks Workers hybrides dans votre compte Automation pour prendre en charge des runbooks Automation à condition d’utiliser le même compte à la fois pour la solution et pour l’appartenance au groupe de Runbooks Workers hybrides.  Cette fonctionnalité a été ajoutée à la version 7.2.12024.0 du groupe de Runbooks Workers hybrides.  


## <a name="hybrid-runbook-worker-groups"></a>Groupes de Runbooks Workers hybrides
Chaque Runbook Worker hybride est membre d'un groupe de Runbooks Workers hybrides que vous spécifiez lorsque vous installez l'agent.  Un groupe peut inclure un seul agent, mais vous pouvez installer plusieurs agents dans un groupe pour une haute disponibilité.

Lorsque vous démarrez un Runbook sur un Runbook Worker hybride, vous spécifiez le groupe sur lequel il s'exécutera.  Les membres du groupe déterminent quel Worker traitera la demande.  Vous ne pouvez pas spécifier un Worker particulier.

## <a name="hybrid-runbook-worker-requirements"></a>Conditions requises des Runbooks Workers hybrides
Vous devez désigner au moins un ordinateur local pour exécuter les tâches de runbooks hybrides.  Cet ordinateur doit disposer des éléments suivants :

* Windows Server 2012 ou version ultérieure
* Windows PowerShell 4.0 ou version ultérieure.  Nous vous recommandons d’installer Windows PowerShell 5.0 sur les ordinateurs pour renforcer la fiabilité. Vous pouvez la télécharger la nouvelle version à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=50395).
* Deux cœurs et 4 Go de RAM minimum

Tenez compte des recommandations suivantes pour les Workers hybrides :

* Désignez plusieurs Workers hybrides dans chaque groupe pour la haute disponibilité.  
* Des Workers hybrides peuvent coexister avec les serveurs runbooks Service Management Automation ou System Center Orchestrator.
* Envisagez d’utiliser un ordinateur physiquement situé au sein ou à proximité de la région de votre compte Automation, car les données des travaux sont renvoyées à Azure Automation lorsqu’un travail se termine.

### <a name="configure-proxy-and-firewall-settings"></a>Configuration des paramètres de proxy et de pare-feu
Pour que le Runbook Worker hybride local se connecte à Microsoft Operations Management Suite (OMS) et soit enregistré, il doit avoir accès au numéro de port et aux URL décrits ci-dessous.  Il s’agit d’un ajout aux [ports et URL requis pour que Microsoft Monitoring Agent](../log-analytics/log-analytics-proxy-firewall.md#configure-settings-with-the-microsoft-monitoring-agent) se connecte à OMS. Si vous utilisez un serveur proxy pour la communication entre l'agent et le service OMS, vous devez vous assurer que les ressources appropriées sont accessibles. Si vous utilisez un pare-feu pour restreindre l’accès à Internet, vous devez configurer votre pare-feu pour autoriser l’accès.

Les informations ci-dessous répertorient le port et les URL requis pour que le Runbook Worker hybride communique avec Automation.

* Port : seul TCP 443 est nécessaire pour l’accès Internet sortant
* URL globale : *.azure-automation.net

Si vous avez un compte Automation défini pour une région spécifique et que vous souhaitez limiter la communication avec ce centre de données régional, le tableau suivant fournit l’enregistrement DNS pour chaque région.

| **Région** | **Enregistrement DNS** |
| --- | --- |
| Centre-Sud des États-Unis |scus-jobruntimedata-prod-su1.azure-automation.net |
| Est des États-Unis 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| Europe de l'Ouest |we-jobruntimedata-prod-su1.azure-automation.net |
| Europe du Nord |ne-jobruntimedata-prod-su1.azure-automation.net |
| Centre du Canada |cc-jobruntimedata-prod-su1.azure-automation.net |
| Asie du Sud-Est |sea-jobruntimedata-prod-su1.azure-automation.net |
| Inde centrale |cid-jobruntimedata-prod-su1.azure-automation.net |
| Est du Japon |jpe-jobruntimedata-prod-su1.azure-automation.net |
| Sud-Est de l’Australie |ase-jobruntimedata-prod-su1.azure-automation.net |

## <a name="installing-hybrid-runbook-worker"></a>Installation de la fonctionnalité Runbook Worker hybride

Il existe deux méthodes, décrites ci-dessous, pour installer et configurer un Runbook Worker hybride.  La première méthode est un script PowerShell qui automatise toutes les étapes nécessaires pour configurer l’ordinateur Windows, et il s’agit de l’approche conseillée, car elle simplifie le processus de déploiement complet pour vous.  La deuxième méthode consiste à suivre la procédure pas à pas pour installer et configurer le rôle manuellement.   

### <a name="automated-deployment"></a>Déploiement automatisé

Effectuez les étapes suivantes pour automatiser l’installation et la configuration du rôle Worker hybride.  

1. Téléchargez le script *New-OnPremiseHybridWorker.ps1* à partir de [PowerShell Gallery](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/1.0/DisplayScript) directement depuis l’ordinateur qui exécute le rôle Runbook Worker hybride ou depuis un autre ordinateur dans votre environnement, et copiez-le dans le Worker.  

    Le script *New-OnPremiseHybridWorker.ps1* requiert les paramètres suivants lors de l’exécution :

  * *AutomationAccountName* (obligatoire) : nom de votre compte Automation.  
  * *ResourceGroupName* (obligatoire) - le nom du groupe de ressources associé à votre compte Automation.  
  * *HybridGroupName* (obligatoire) - le nom d’un groupe de Runbook Workers hybrides que vous spécifiez comme cible pour les runbooks prenant en charge ce scénario 
  *  *SubscriptionID* (obligatoire) - l’ID de l’abonnement Azure dans lequel se trouve votre compte Automation
  *  *WorkspaceName* (facultatif) : le nom d’espace de travail OMS.  Si vous n’avez pas d’espace de travail OMS, le script en crée et configure un.

    > [!NOTE]
    > Actuellement, les seules régions Automation prises en charge pour l’intégration avec OMS sont : **Sud-Est de l’Australie**, **Est des États-Unis 2**, **Sud-Est asiatique** et **Europe de l’Ouest**.  Si votre compte Automation ne se trouve pas dans une de ces régions, le script crée l’espace de travail OMS, mais vous avertit qu’il ne peut pas lier les deux.  

2. Sur votre ordinateur, démarrez **Windows PowerShell** à partir de l’écran **Démarrer** en mode Administrateur.  
3. À partir de l’interface de ligne de commande PowerShell, accédez au dossier qui contient le script que vous avez téléchargé, puis exécutez-le en modifiant les valeurs des paramètres *-AutomationAccountName*, *-ResourceGroupName*, *-HybridGroupName*, *-SubscriptionId* et *-WorkspaceName*.

    > [!NOTE] 
    > Une fois le script exécuté, vous êtes invité à vous authentifier auprès d’Azure.  Vous **devez** vous connecter avec un compte membre du rôle Administrateurs des abonnements et coadministrateur de l’abonnement.   
    
        .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> `
        -ResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
        -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfOMSWorkspace>

4. Il vous sera demandé d’accepter d’installer **NuGet** et vous serez invité à vous authentifier avec vos informations d’identification Azure.<br><br> ![Exécution du script New-OnPremiseHybridWorker](media/automation-hybrid-runbook-worker/new-onpremisehybridworker-scriptoutput.png)

5. Une fois le script terminé, le panneau Groupes de Workers hybrides affiche le nouveau groupe et le nombre de membres, ou, si le groupe existe déjà, incrémente le nombre de membres en conséquence.  Vous pouvez sélectionner le groupe dans la liste du panneau **Groupes de Workers hybrides** et sélectionner la mosaïque **Workers hybrides**.  Dans le panneau **Workers hybrides**, vous verrez chaque membre du groupe répertorié.  

### <a name="manual-deployment"></a>Déploiement manuel 
Effectuez les deux premières étapes une fois pour votre environnement Automation, puis répétez les étapes restantes pour chaque ordinateur Worker.

#### <a name="1-create-operations-management-suite-workspace"></a>1. Création d’un espace de travail Operations Management Suite
Si vous ne disposez pas déjà d’un espace de travail Operations Management Suite, créez-en un en suivant les instructions mentionnées à la page [Gestion de l’accès à Log Analytics](https://technet.microsoft.com/library/mt484119.aspx). Vous pouvez utiliser un espace de travail existant si vous en avez déjà un.

#### <a name="2-add-automation-solution-to-operations-management-suite-workspace"></a>2. Ajout de la solution Automation à l’espace de travail Operations Management Suite
Les solutions ajoutent des fonctionnalités à Operations Management Suite.  La solution Automation ajoute des fonctionnalités à Azure Automation, notamment la prise en charge des Runbooks Workers hybrides.  Lorsque vous ajoutez la solution à votre espace de travail, les composants Worker sont automatiquement transférés à l’ordinateur agent que vous installerez à l’étape suivante.

Suivez les instructions de la page [Pour ajouter une solution à l’aide de la galerie de solutions](../log-analytics/log-analytics-add-solutions.md) pour ajouter la solution **Automation** à votre espace de travail Operations Management Suite.

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3. Installation de Microsoft Monitoring Agent
Microsoft Monitoring Agent connecte les ordinateurs à Operations Management Suite.  Lorsque vous installez l’agent sur votre ordinateur local et que vous le connectez à votre espace de travail, il télécharge automatiquement les composants requis pour le Runbook Worker hybride.

Suivez les instructions de la page [Connecter des ordinateurs Windows à Log Analytics](../log-analytics/log-analytics-windows-agents.md) pour installer l’agent sur l’ordinateur local.  Vous pouvez répéter ce processus pour plusieurs ordinateurs afin d’ajouter plusieurs Workers à votre environnement.

Lorsque l’agent parvient à se connecter à Operations Management Suite, il est répertorié dans l’onglet **Sources connectées** du volet **Paramètres** d’Operations Management Suite.  Vous pouvez vérifier que l’agent a correctement téléchargé la solution Automation lorsqu’un dossier appelé **AzureAutomationFiles** figure dans C:\Program Files\Microsoft Monitoring Agent\Agent.  Pour valider la version du Runbook Worker hybride, vous pouvez accéder à C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\ et consulter le sous-dossier \\*version*.   

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Installer l'environnement de Runbook et se connecter à Azure Automation
Lorsque vous ajoutez un agent à Operations Management Suite, la solution Automation lance le module PowerShell **HybridRegistration** qui contient l’applet de commande **Add-HybridRunbookWorker**.  Vous utilisez cette applet de commande pour installer l'environnement de Runbook sur la machine et l'inscrire auprès d'Azure Automation.

Ouvrez une session PowerShell en mode administrateur et exécutez les commandes suivantes pour importer le module.

    cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
    Import-Module HybridRegistration.psd1

Exécutez ensuite l'applet de commande **Add-HybridRunbookWorker** en utilisant la syntaxe suivante :

    Add-HybridRunbookWorker –Name <String> -EndPoint <Url> -Token <String>

Vous pouvez obtenir les informations requises pour cette applet de commande dans le panneau **Gérer les clés** du portail Azure.  Ouvrez ce panneau en sélectionnant l’option **Clés** à partir du panneau **Paramètres** de votre compte Automation.

![Vue d'ensemble des Runbooks Workers hybrides](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **Name** est le nom du groupe de Runbooks Workers hybrides. Si ce groupe existe déjà dans le compte Automation, la machine actuelle est ajoutée à ce dernier.  S'il n'existe pas encore, il est alors ajouté.
* **EndPoint** est le champ **URL** dans le panneau **Gérer les clés**.
* **Token** est la **clé d’accès primaire** dans le panneau **Gérer les clés**.  

Utilisez le commutateur **-Verbose** avec **Add-HybridRunbookWorker** pour recevoir des informations détaillées concernant l’installation.

#### <a name="5-install-powershell-modules"></a>5. Installer des modules PowerShell
Les Runbooks peuvent utiliser toutes les activités et applets de commande définies dans les modules installés dans votre environnement Azure Automation.  Toutefois, comme ces modules ne sont pas automatiquement déployés sur les ordinateurs locaux, vous devez les installer manuellement.  L'exception est le module Azure qui est installé par défaut et qui permet d'accéder aux applets de commande pour l'ensemble des services et activités Azure pour Azure Automation.

Étant donné que l'objectif principal de la fonctionnalité Runbook Worker hybride est de gérer les ressources locales, vous devrez probablement installer les modules qui prennent en charge ces ressources.  Vous pouvez vous reporter à la section [Installation de modules](http://msdn.microsoft.com/library/dd878350.aspx) pour obtenir des informations sur l’installation de modules Windows PowerShell.

## <a name="removing-hybrid-runbook-worker"></a>Suppression de la fonctionnalité Runbook Worker hybride 
Vous pouvez supprimer un ou plusieurs Runbook Workers hybrides d’un groupe ou supprimer le groupe, selon vos besoins.  Pour supprimer un Runbook Worker hybride à partir d’un ordinateur local, procédez comme suit.

1. Dans le portail Azure, accédez à votre compte Automation.  
2. À partir du panneau **Paramètres**, sélectionnez **Clés** et notez les valeurs des champs **URL** et **Clé d’accès primaire**.  Vous aurez besoin de ces informations dans l’étape suivante.
3. Ouvrez une session PowerShell en mode administrateur et exécutez la commande suivante - `Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>`.  Utilisez le commutateur **-Verbose** pour afficher un journal détaillé du processus de suppression.

> [!NOTE]
> Cela ne supprime pas Microsoft Monitoring Agent de l’ordinateur, mais seulement les fonctionnalités et la configuration du rôle Runbook Worker hybride.  

## <a name="remove-hybrid-worker-groups"></a>Suppression de groupes de Workers hybrides
Pour supprimer un groupe, vous devez tout d’abord supprimer les Runbook Workers hybrides de chaque ordinateur membre du groupe à l’aide de la procédure indiquée plus haut, puis effectuer les opérations suivantes pour supprimer le groupe.  

1. Dans le portail Azure, ouvrez le compte Automation.
2. Sélectionnez la vignette **Groupes de Workers hybrides** et sélectionnez le groupe que vous souhaitez supprimer dans le panneau **Groupes de Workers hybrides**.  Une fois que vous avez sélectionné le groupe, le panneau de propriétés du **groupe de Workers hybrides** s’affiche.<br> ![Panneau Groupes de Runbook Workers hybrides](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)   
3. Dans le panneau des propriétés du groupe sélectionné, cliquez sur **Supprimer**.  Un message apparaît, vous invitant à confirmer cette action : sélectionnez **Oui** Si vous êtes sûr de vouloir continuer.<br> ![Boîte de dialogue de confirmation de suppression du groupe](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)<br> Ce processus peut prendre plusieurs secondes. Vous pouvez suivre la progression sous **Notifications** dans le menu.  

## <a name="starting-runbooks-on-hybrid-runbook-worker"></a>Démarrage de Runbooks sur un Runbook Worker hybride
[Démarrage d'un Runbook dans Azure Automation](automation-starting-a-runbook.md) décrit différentes méthodes de démarrage d'un Runbook.  La fonctionnalité Runbook Worker hybride ajoute une option **RunOn** qui vous permet de spécifier le nom d'un groupe de Runbooks Workers hybrides.  Si un groupe est spécifié, le Runbook est récupéré et exécuté par les Workers de ce groupe.  Si cette option n'est pas spécifiée, il est exécuté dans Azure Automation comme habituellement.

Quand vous démarrez un Runbook dans le portail Azure, vous obtenez une option **Exécuter sur** qui vous permet de sélectionner **Azure** ou **Worker hybride**.  Si vous sélectionnez **Worker hybride**, vous pouvez sélectionner le groupe souhaité dans une liste déroulante.

Utilisez le paramètre **RunOn**. Vous pouvez utiliser la commande suivante pour démarrer un Runbook nommé Test-Runbook sur un groupe de Runbook Workers hybrides nommé MyHybridGroup à l’aide de Windows PowerShell.

    Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"

> [!NOTE]
> Le paramètre **RunOn** a été ajouté à l’applet de commande **Start-AzureAutomationRunbook** dans la version 0.9.1 de Microsoft Azure PowerShell.  Vous devez [télécharger la version la plus récente](https://azure.microsoft.com/downloads/) si une version antérieure est installée.  Il vous suffit d'installer cette version sur une station de travail sur laquelle vous démarrez le Runbook à partir de Windows PowerShell.  Il n'est pas nécessaire de l'installer sur l'ordinateur de travail, sauf si vous avez l'intention de démarrer les Runbooks dessus.  Actuellement, vous ne pouvez pas démarrer un Runbook sur un Runbook Worker hybride à partir d'un autre Runbook, car cela nécessiterait que la version la plus récente d'Azure PowerShell soit installée dans votre compte Automation.  Bientôt, la version la plus récente sera automatiquement mise à jour dans Azure Automation et lancée automatiquement dans les Workers.
>
>

## <a name="runbook-permissions"></a>Autorisations de Runbook
Les Runbooks exécutés sur un Runbook Worker hybride ne peuvent pas utiliser la même méthode que celle généralement utilisée pour l’authentification des Runbooks auprès des ressources Azure, car ils accèdent à des ressources extérieures à Azure.  Le Runbook peut fournir sa propre authentification aux ressources locales, mais vous pouvez également spécifier un compte RunAs pour fournir un contexte utilisateur pour l’ensemble des Runbooks.

### <a name="runbook-authentication"></a>Authentification des Runbooks
Par défaut, les Runbooks s’exécutent dans le contexte du compte Système local de l’ordinateur local. De ce fait, ils doivent fournir leur propre authentification auprès des ressources auxquelles ils accèdent.  

Vous pouvez utiliser les ressources [Informations d’identification](http://msdn.microsoft.com/library/dn940015.aspx) et [Certificat](http://msdn.microsoft.com/library/dn940013.aspx) dans votre Runbook avec des applets de commande qui vous permettent de spécifier des informations d’identification pour que vous puissiez vous authentifier auprès de différentes ressources.  L'exemple suivant présente une partie d'un Runbook qui redémarre un ordinateur.  Il récupère des informations d'identification à partir d'une ressource d'informations d'identification et le nom de l'ordinateur à partir d'une ressource de variable, puis il utilise ces valeurs avec l'applet de commande Restart-Computer.

    $Cred = Get-AzureRmAutomationCredential -ResourceGroupName "ResourceGroup01" -Name "MyCredential"
    $Computer = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" -Name  "ComputerName"

    Restart-Computer -ComputerName $Computer -Credential $Cred

Vous pouvez également exploiter [InlineScript](automation-powershell-workflow.md#inlinescript) qui vous permettra d’exécuter des blocs de code sur une autre machine avec les informations d’identification spécifiées par le [paramètre commun PSCredential](http://technet.microsoft.com/library/jj129719.aspx).

### <a name="runas-account"></a>Compte RunAs
Plutôt que de laisser les Runbooks fournir leur propre authentification aux ressources locales, vous pouvez spécifier un compte **RunAs** pour un groupe de Workers hybrides.  Vous spécifiez une [ressource d’information d’identification](automation-credentials.md) ayant accès aux ressources locales, après quoi tous les Runbooks utiliseront ces informations d’identification au moment de leur exécution sur un Runbook Worker hybride du groupe.  

Le nom d’utilisateur doit utiliser l’un des formats suivants :

* domaine\nom d’utilisateur
* username@domain
* nom d’utilisateur (pour les comptes locaux sur l’ordinateur local)

Utilisez la procédure suivante pour spécifier un compte RunAs pour un groupe de Workers hybrides :

1. Créez une [ressource d’information d’identification](automation-credentials.md) ayant accès aux ressources locales.
2. Dans le portail Azure, ouvrez le compte Automation.
3. Sélectionnez la vignette **Groupes de Workers hybrides** , puis sélectionnez le groupe.
4. Sélectionnez **Tous les paramètres**, puis **Paramètres du groupe Worker hybride**.
5. Sous **Exécuter en tant que**, remplacez **Par défaut** par **Personnalisé**.
6. Sélectionnez les informations d’identification et cliquez sur **Enregistrer**.

## <a name="creating-runbooks-for-hybrid-runbook-worker"></a>Création de Runbooks pour un Runbook Worker hybride
Il n'existe aucune différence entre la structure des Runbooks qui s'exécutent dans Azure Automation et celle des Runbooks qui s'exécutent sur un Runbook Worker hybride. Toutefois, il est très possible que les Runbooks que vous utilisez avec chaque structure diffèrent considérablement, car les Runbooks Workers hybrides gèrent généralement les ressources locales de votre centre de données, tandis que les Runbooks dans Azure Automation gèrent généralement les ressources dans le cloud Azure.

Vous pouvez modifier un Runbook pour le Runbook Worker hybride dans Azure Automation, mais vous pouvez rencontrer des difficultés si vous essayez de tester le Runbook dans l'éditeur.  Les modules PowerShell qui accèdent aux ressources locales ne peuvent pas être installés dans votre environnement Azure Automation, car sinon, le test échoue.  Si vous installez les modules requis, le Runbook s'exécute, mais il ne peut pas accéder aux ressources locales pour un test complet.

## <a name="troubleshooting-runbooks-on-hybrid-runbook-worker"></a>Résolution de problèmes de runbooks sur un Runbook Worker hybride
[Sortie et messages de Runbooks](automation-runbook-output-and-messages.md) : ils sont envoyés à Azure Automation à partir de Workers hybrides tout comme les tâches de Runbook exécutées dans le cloud.  Vous pouvez également activer les flux Détaillé et Progression comme vous le feriez pour d’autres runbooks.  

Les journaux sont stockés localement sur chaque Worker hybride à l’emplacement C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes.

Si vos Runbooks ne se terminent pas correctement et que le résumé de la tâche affiche l’état **Interrompu**, consultez l’article de dépannage [Runbook Worker hybride : une tâche de Runbook se termine avec l’état suspendu](automation-troubleshooting-hrw-runbook-terminates-suspended.md).   

## <a name="relationship-to-service-management-automation"></a>Relation avec Service Management Automation
[Service Management Automation (SMA)](https://technet.microsoft.com/library/dn469260.aspx) vous permet d’exécuter les mêmes Runbooks que ceux pris en charge par Azure Automation dans votre centre de données local. SMA est généralement déployé avec Windows Azure Pack, qui contient une interface graphique de gestion de SMA. Contrairement à Azure Automation, SMA nécessite une installation locale incluant les serveurs web qui hébergent l’API, une base de données qui contient les runbooks et la configuration SMA, et les Runbook Workers qui exécutent les travaux des runbooks. Azure Automation fournit ces services dans le cloud. Il ne vous reste plus qu'à tenir à jour les Runbooks Workers hybrides dans votre environnement local.

Si vous êtes un utilisateur SMA existant, vous pouvez déplacer vos Runbooks vers Azure Automation pour qu'ils soient utilisés avec la fonctionnalité Runbook Worker hybride sans subir aucune modification, en supposant qu'ils effectuent leur propre authentification auprès des ressources, comme décrit dans la section [Création de Runbooks pour un Runbook Worker hybride](#creating-runbooks-for-hybrid-runbook-worker).  Dans SMA, les Runbooks s'exécutent dans le contexte du compte de service sur le serveur de Workers qui peut fournir cette authentification pour les Runbooks.

Vous pouvez utiliser les critères suivants pour déterminer si Azure Automation avec la fonctionnalité Runbook Worker hybride ou Service Management Automation est plus adapté à vos besoins.

* SMA nécessite l’installation locale de ses composants sous-jacents connectés à Windows Azure Pack si une interface de gestion graphique est requise. Des ressources locales sont nécessaires avec des coûts de maintenance plus élevés qu’Azure Automation, qui nécessite uniquement l’installation d’un agent sur des Runbook Workers locaux. Les agents sont gérés par Operations Management Suite, ce qui réduit encore davantage vos coûts de maintenance.
* Azure Automation stocke ses Runbooks dans le cloud et les remet à des Runbook Workers hybrides locaux. Si votre stratégie de sécurité n’autorise pas ce comportement, vous devez utiliser SMA.
* SMA est fourni avec System Center ; par conséquent, il requiert une licence System Center 2012 R2. Azure Automation est basé sur un modèle d’abonnement à plusieurs niveaux.
* Azure Automation dispose de fonctionnalités avancées, comme les runbooks graphiques, qui ne sont pas disponibles dans SMA.

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur les différentes méthodes qui peuvent être utilisées pour démarrer un Runbook, consultez [Démarrage d’un Runbook dans Azure Automation](automation-starting-a-runbook.md).  
* Pour comprendre les différentes procédures pour utiliser des Runbooks PowerShell et Workflow PowerShell dans Azure Automation à l’aide de l’éditeur de texte, consultez [Modifier des Runbooks textuels dans Azure Automation](automation-edit-textual-runbook.md)

