<properties 
   pageTitle="Runbooks Workers hybrides Azure Automation"
   description="Cet article fournit des informations sur l'installation et l'utilisation de la fonctionnalité Runbook Worker hybride d'Azure Automation qui vous permet d'exécuter des Runbooks sur les machines de votre centre de données local."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/11/2015"
   ms.author="bwren" />

# Runbooks Workers hybrides Azure Automation

Dans Azure Automation, les Runbooks ne peuvent pas accéder aux ressources de votre centre de données local, car ils s'exécutent dans le cloud Azure. La fonctionnalité Runbook Worker hybride d'Azure Automation vous permet d'exécuter des Runbooks sur des machines situées dans votre centre de données afin de gérer les ressources locales. Les Runbooks sont stockés et gérés dans Azure Automation, puis remis à une ou plusieurs ordinateurs locaux où ils sont exécutés.

Cette fonctionnalité est illustrée dans l'image suivante.

![Vue d'ensemble des Runbooks Workers hybrides](./media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-overview.png)

Vous pouvez désigner un ou plusieurs ordinateurs de votre centre de données pour qu'elles jouent le rôle de Runbook Worker hybride et exécutent les Runbooks à partir d'Azure Automation. Chaque Worker nécessite Microsoft Management Agent avec une connexion à Azure Operational Insights et l'environnement de Runbook Azure Automation. Operational Insights est utilisé uniquement pour installer et mettre à jour l'agent de gestion, ainsi que pour surveiller la fonctionnalité du Worker. La remise de Runbooks et l'instruction visant à les exécuter sont effectuées par Azure Automation.

![Composants des Runbooks Workers hybrides](./media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-components.png)

Il n'existe aucune exigence relative aux pare-feu entrants pour prendre en charge les Runbooks Workers hybrides. L'agent sur la machine locale établit toutes les communications avec Azure Automation dans le cloud. Lorsqu'un Runbook est démarré, Azure Automation crée une instruction qui est récupérée par l'agent. L'agent extrait alors le Runbook et tous les paramètres avant de l'exécuter. Il récupère également toutes les [ressources](http://msdn.microsoft.com/library/dn939988.aspx) qui sont utilisées par le Runbook dans Azure Automation.

## Groupes de Runbooks Workers hybrides

Chaque Runbook Worker hybride est membre d'un groupe de Runbooks Workers hybrides que vous spécifiez lorsque vous installez l'agent. Un groupe peut inclure un seul agent, mais vous pouvez installer plusieurs agents dans un groupe pour une haute disponibilité.

Lorsque vous démarrez un Runbook sur un Runbook Worker hybride, vous spécifiez le groupe sur lequel il s'exécutera. Les membres du groupe déterminent quel Worker traitera la demande. Vous ne pouvez pas spécifier un Worker particulier.

## Installation de la fonctionnalité Runbook Worker hybride

### Préparer l'environnement Azure Automation

Exécutez la procédure suivante pour préparer votre environnement Azure Automation pour les Runbooks Workers hybrides.

#### 1. Créer un espace de travail Azure Operational Insights
Si vous ne disposez pas déjà d'un espace de travail Operational Insights dans votre compte Azure, créez-en un à l'aide des instructions mentionnées dans la section [Configuration de votre espace de travail Operational Insights](../operational-insights-setup-workspace). Vous pouvez utiliser un espace de travail existant si vous en avez déjà un.

#### 2. Déployer la solution Automation
Dans Operational Insights, la solution Automation lance les composants requis pour configurer et prendre en charge l'environnement du Runbook. Suivez les instructions fournies dans la section [Solutions Operational Insights](../operational-insights-add-solution) pour installer le pack **Azure Automation**.

### Configurer des machines locales
Exécutez la procédure suivante pour chacune des machines locales qui jouera le rôle de Runbook Worker hybride.


#### 1. Installer Microsoft Management Agent
Microsoft Management Agent connecte l'ordinateur à Operational Insights et lui permet d'exécuter la logique de solutions. Suivez les instructions fournies dans la section [Connexion directe des ordinateurs à Operational Insights](../operational-insights-direct-agent) pour installer l'agent sur la machine locale et le connecter à Operational Insights.

#### 2. Installer l'environnement de Runbook et se connecter à Azure Automation
Lorsque vous ajoutez un ordinateur à Operational Insights, la solution Automation lance le module PowerShell **HybridRegistration** qui contient l'applet de commande **Add-HybridRunbookWorker**. Vous utilisez cette applet de commande pour installer l'environnement de Runbook sur la machine et l'inscrire auprès d'Azure Automation.

Ouvrez une session PowerShell en mode administrateur et exécutez la commande suivante pour importer le module.

	Import-Module HybridRegistration 

Si un message d'erreur indiquant que le fichier de module est introuvable s'affiche, vous devrez peut-être exécuter la commande suivante qui utilise le chemin d'accès complet au fichier de module.

	Import-Module "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomationFiles\HybridRegistration\HybridRegistration.psd1"

Exécutez ensuite l'applet de commande **Add-HybridRunbookWorker** en utilisant la syntaxe suivante :

	Add-HybridRunbookWorker –Name <String> -EndPoint <Url> -Token <String>


- **Name** est le nom du groupe de Runbooks Workers hybrides. Si ce groupe existe déjà dans le compte Automation, la machine actuelle est ajoutée à ce dernier. S'il n'existe pas encore, il est alors ajouté.
- **EndPoint** est l'URL du service de l'agent. Vous pouvez l'obtenir à partir du portail Azure en version préliminaire dans le panneau **Gérer les clés**.  
- **Token** est la **clé d'accès primaire** dans le panneau **Gérer les clés**. Vous pouvez ouvrir le panneau Gérer les clés en cliquant sur l'icône de clé dans le panneau Éléments du compte Automation.<br><br>![Vue d'ensemble des Runbooks Workers hybrides](./media/automation-hybrid-runbook-worker/elements-panel-keys.png)


#### 3. Installer des modules PowerShell 
Les Runbooks peuvent utiliser toutes les activités et applets de commande définies dans les modules installés dans votre environnement Azure Automation. Toutefois, comme ces modules ne sont pas automatiquement déployés sur les machines locales, vous devez les installer manuellement. L'exception est le module Azure qui est installé par défaut et qui permet d'accéder aux applets de commande pour l'ensemble des services et activités Azure pour Azure Automation.

Étant donné que l'objectif principal de la fonctionnalité Runbook Worker hybride est de gérer les ressources locales, vous devrez probablement installer les modules qui prennent en charge ces ressources. Vous pouvez vous reporter à la section [Installation de modules](http://msdn.microsoft.com/library/dd878350.aspx) pour obtenir des informations sur l'installation de modules Windows PowerShell.

## Démarrage de Runbooks sur un Runbook Worker hybride

La section [Démarrage d'un Runbook dans Azure Automation](../automation-starting-a-runbook) décrit différentes méthodes de démarrage d'un Runbook. La fonctionnalité Runbook Worker hybride ajoute une option **RunOn** qui vous permet de spécifier le nom d'un groupe de Runbooks Workers hybrides. Si un groupe est spécifié, le Runbook est récupéré et exécuté par les Workers de ce groupe. Si cette option n'est pas spécifiée, il est exécuté dans Azure Automation comme habituellement.

Lorsque vous démarrez un Runbook dans le portail Azure en version préliminaire, une option **Exécuter sur** qui vous permet de sélectionner **Azure** ou **Worker hybride** apparaît. Si vous sélectionnez **Worker hybride**, vous pouvez sélectionner le groupe souhaité dans une liste déroulante.

Utilisez le paramètre **RunOn**. Vous pouvez utiliser la commande suivante pour démarrer un Runbook nommé Test-Runbook sur un groupe de Runbooks Workers hybrides nommé MyHybridGroup à l'aide de Windows PowerShell.

	Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"

>[AZURE.NOTE]Le paramètre **RunOn** a été ajouté à l'applet de commande **Start-AzureAutomationRunbook** dans la version 0.9.1 de Microsoft Azure PowerShell. Vous devez [télécharger la version la plus récente](http://azure.microsoft.com/downloads) si une version antérieure est installée. Il vous suffit d'installer cette version sur une station de travail sur laquelle vous démarrez le Runbook à partir de Windows PowerShell. Il n'est pas nécessaire de l'installer sur l'ordinateur de travail, sauf si vous avez l'intention de démarrer les Runbooks dessus. Actuellement, vous ne pouvez pas démarrer un Runbook sur un Runbook Worker hybride à partir d'un autre Runbook, car cela nécessiterait que la version la plus récente d'Azure PowerShell soit installée dans votre compte Automation. Bientôt, la version la plus récente sera automatiquement mise à jour dans Azure Automation et lancée automatiquement dans les Workers.


## Création de Runbooks pour un Runbook Worker hybride

Il n'existe aucune différence entre la structure des Runbooks qui s'exécutent dans Azure Automation et celle des Runbooks qui s'exécutent sur un Runbook Worker hybride. Toutefois, il est très possible que les Runbooks que vous utilisez avec chaque structure diffèrent considérablement, car les Runbooks Workers hybrides gèrent généralement les ressources locales de votre centre de données, tandis que les Runbooks dans Azure Automation gèrent généralement les ressources dans le cloud Azure.

### Autorisations de Runbook

Les Runbooks s'exécutent dans le contexte du compte Système local sur le Runbook Worker hybride. De ce fait, ils doivent fournir leur propre authentification auprès des ressources auxquelles ils accèdent. Ils ne peuvent pas utiliser la même [méthode que celle généralement utilisée pour l'authentification des Runbooks auprès des ressources Azure](automation-configuring.md/#configuring-authentication-to-azure-resources), car ils accèdent à des ressources en dehors d'Azure.

Vous pouvez utiliser les ressources [Informations d'identification](http://msdn.microsoft.com/library/dn940015.aspx) et [Certificat](http://msdn.microsoft.com/library/dn940013.aspx) dans votre Runbook avec des applets de commande qui vous permettent de spécifier des informations d'identification pour que vous puissiez vous authentifier auprès de différentes ressources. L'exemple suivant présente une partie d'un Runbook qui redémarre un ordinateur. Il récupère des informations d'identification à partir d'une ressource d'informations d'identification et le nom de l'ordinateur à partir d'une ressource de variable, puis il utilise ces valeurs avec l'applet de commande Restart-Computer.

	$Cred = Get-AutomationCredential "MyCredential"
	$Computer = Get-AutomationVariable "ComputerName"

	Restart-Computer -ComputerName $Computer  -Credential $Cred

Vous pouvez également exploiter [InlineScript](automation-runbook-concepts.md/#inline-script) qui vous permettra d'exécuter des blocs de code sur une autre machine avec les informations d'identification spécifiées par le [paramètre commun PSCredential](http://technet.microsoft.com/library/jj129719.aspx).


### Création et test de Runbooks

Vous pouvez modifier un Runbook pour le Runbook Worker hybride dans Azure Automation, mais vous pouvez rencontrer des difficultés si vous essayez de tester le Runbook dans l'éditeur. Les modules PowerShell qui accèdent aux ressources locales ne peuvent pas être installés dans votre environnement Azure Automation, car sinon, le test échoue. Si vous installez les modules requis, le Runbook s'exécute, mais il ne peut pas accéder aux ressources locales pour un test complet.

## Relation avec Service Management Automation

[Service Management Automation (SMA)](http://aka.ms/runbookauthor/sma) est un composant de Windows Azure Pack (WAP) qui vous permet d'exécuter les mêmes Runbooks que ceux pris en charge par Azure Automation dans votre centre de données local. Contrairement à Azure Automation, SMA nécessite une installation locale qui inclut le portail de gestion Windows Azure Pack et une base de données pour contenir les Runbooks et la configuration de SMA. Azure Automation fournit ces services dans le cloud. Il ne vous reste plus qu'à tenir à jour les Runbooks Workers hybrides dans votre environnement local.

Si vous êtes un utilisateur SMA existant, vous pouvez déplacer vos Runbooks vers Azure Automation pour qu'ils soient utilisés avec la fonctionnalité Runbook Worker hybride sans subir aucune modification, en supposant qu'ils effectuent leur propre authentification auprès des ressources, comme décrit dans la section [Création de Runbooks pour un Runbook Worker hybride](#creating-runbooks-for-hybrid-runbook-worker). Dans SMA, les Runbooks s'exécutent dans le contexte du compte de service sur le serveur de Workers qui peut fournir cette authentification pour les Runbooks.

Vous pouvez utiliser les critères suivants pour déterminer si Azure Automation avec la fonctionnalité Runbook Worker hybride ou Service Management Automation est plus adapté à vos besoins.

- SMA requiert une installation locale de Windows Azure Pack qui a des ressources plus locales et des coûts de maintenance plus élevés qu'Azure Automation qui nécessite uniquement qu'un agent soit installé sur des Runbooks Workers locaux. Les agents sont gérés par Operational Insights, ce qui favorise la réduction des coûts de maintenance. 
- Azure Automation stocke ses Runbooks dans le cloud et les remet à des Runbooks Workers hybrides locaux. Si votre stratégie de sécurité n'autorise pas ce comportement, vous devez utiliser SMA.
- Windows Azure Pack est un téléchargement gratuit tandis qu'Azure Automation peut entraîner des frais d'abonnement. Azure. Plusieurs bases de données doivent être tenues à jour pour SMA.
- Azure Automation avec la fonctionnalité Runbook Worker hybride vous permet de gérer les Runbooks pour les ressources cloud et les ressources locales dans un seul et même emplacement, plutôt que de gérer à la fois Azure Automation et SMA.
- Azure Automation dispose de fonctionnalités avancées, comme la création de graphiques qui ne sont pas disponibles dans SMA. 


## Articles connexes

- [Démarrage d'un Runbook dans Azure Automation](../automation-starting-a-runbook)
- [Modification d'un Runbook dans Azure Automation](https://msdn.microsoft.com/library/dn879137.aspx)

<!---HONumber=58-->