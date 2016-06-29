<properties 
   pageTitle="Ressources d’informations d’identification dans Azure Automation | Microsoft Azure"
   description="Les ressources d’informations d’identification dans Azure Automation contiennent des informations d’identification de sécurité qui peuvent être utilisées pour s’authentifier auprès des ressources auxquelles le Runbook ou la configuration DSC a accès. Cet article décrit comment créer des ressources d’informations d’identification et les utiliser dans un Runbook ou une configuration DSC."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/09/2016"
   ms.author="bwren" />

# Ressources d’informations d’identification dans Azure Automation

Une ressource d’informations d’identification Automation conserve un objet [PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential) qui contient les informations d’identification de sécurité comme un nom d’utilisateur et un mot de passe. Les Runbooks et les configurations DSC peuvent utiliser les applets de commande qui acceptent un objet PSCredential pour l’authentification, ou ils peuvent extraire le nom d’utilisateur et le mot de passe de l’objet PSCredential pour les fournir à l’application ou au service nécessitant l’authentification. Les propriétés d’informations d’identification sont stockées de manière sécurisée dans Azure Automation et sont accessibles dans le Runbook ou la configuration DSC avec l’activité [Get-AutomationPSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential.aspx).

>[AZURE.NOTE] Les ressources sécurisées dans Azure Automation incluent les informations d'identification, les certificats, les connexions et les variables chiffrées. Ces ressources sont chiffrées et stockées dans Azure Automation à l'aide d'une clé unique, générée pour chaque compte Automation. Cette clé est chiffrée par un certificat principal et stockée dans Azure Automation. Avant de stocker une ressource sécurisée, la clé pour le compte Automation est déchiffrée à l’aide du certificat principal, puis utilisée pour chiffrer la ressource.

## Applets de commande Windows PowerShell

Les applets de commande du tableau suivant sont utilisées pour créer et gérer les ressources d’informations d’identification Automation avec Windows PowerShell. Elles sont fournies dans le cadre du [module Azure PowerShell](../powershell-install-configure.md), utilisable dans les Runbooks Automation et les configurations DSC.

|Applets de commande|Description|
|:---|:---|
|[Get-AzureAutomationCredential](http://msdn.microsoft.com/library/dn913781.aspx)|Récupère des informations sur une ressource d’informations d’identification. Vous pouvez uniquement récupérer les informations d’identification elles-mêmes à partir de l’activité **Get-AutomationPSCredential**.|
|[New-AzureAutomationCredential](http://msdn.microsoft.com/library/azure/jj554330.aspx)|Crée de nouvelles informations d’identification Automation.|
|[Remove- AzureAutomationCredential](http://msdn.microsoft.com/library/azure/jj554330.aspx)|Supprime des informations d’identification Automation.|
|[Set- AzureAutomationCredential](http://msdn.microsoft.com/library/azure/jj554330.aspx)|Définit les propriétés d’informations d’identification Automation existantes.|

## Activités de Runbook

Les activités dans le tableau suivant sont utilisées pour accéder aux informations d’identification dans un Runbook ou dans des configurations DSC.

|Activités|Description|
|:---|:---|
|Get-AutomationPSCredential|Obtient les informations d’identification à utiliser dans un Runbook ou dans une configuration DSC. Renvoie un objet [System.Management.Automation.PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential).|

>[AZURE.NOTE] Évitez d’utiliser des variables dans le paramètre –Name de Get-AutomationPSCredential, car cela complique la découverte des dépendances entre les Runbooks ou configurations DSC et les ressources d’informations d’identification au moment de la conception.

## Création d’une ressource d’informations d’identification


### Pour créer une ressource d’informations d’identification avec le portail Azure Classic

1. À partir de votre compte Automation, cliquez sur **Ressources** en haut de la fenêtre.
1. En bas de la fenêtre, cliquez sur **Ajouter un paramètre**.
1. Cliquez sur **Ajouter les informations d’identification**.
2. Dans la liste déroulante **Type d’informations d’identification**, sélectionnez **Informations d’identification PowerShell**.
1. Terminez l’Assistant et activez la case à cocher pour enregistrer les nouvelles informations d’identification.


### Pour créer une ressource d’informations d’identification avec le portail Azure

1. À partir de votre compte Automation, cliquez sur la partie **Ressources** afin d’ouvrir le panneau **Ressources**.
1. Cliquez sur la partie **Informations d’identification** afin d’ouvrir le panneau **Informations d’identification**.
1. Cliquez sur **Ajouter des informations d’identification** en haut du panneau.
1. Remplissez le formulaire, puis cliquez sur **Créer** pour enregistrer les nouvelles informations d’identification.


### Pour créer une ressource d’informations d’identification PowerShell avec Windows PowerShell

Les exemples de commandes suivants montrent comment créer des informations d’identification Automation. Un objet PSCredential est d’abord créé avec le nom et le mot de passe, puis il est utilisé pour créer la ressource d’informations d’identification. Vous pouvez également utiliser l’applet de commande **Get-Credential** pour être invité à saisir un nom et un mot de passe.

	$user = "MyDomain\MyUser"
	$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
	$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
	New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred

## Utilisation des informations d’identification PowerShell

Vous récupérez une ressource d’informations d’identification dans un Runbook ou une configuration DSC avec l’activité **Get-AutomationPSCredential**. Cette propriété renvoie un [objet PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential.aspx) que vous pouvez utiliser avec une activité ou une applet de commande nécessitant un paramètre PSCredential. Vous pouvez également récupérer les propriétés de l’objet d’informations d’identification pour les utiliser individuellement. L’objet possède une propriété pour le nom d’utilisateur et le mot de passe sécurisé. Vous pouvez également utiliser la méthode **GetNetworkCredential** pour retourner un objet [NetworkCredential](http://msdn.microsoft.com/library/system.net.networkcredential.aspx) fournissant une version non sécurisée du mot de passe.

### Exemple de Runbook textuel

Les exemples de commandes suivants montrent comment utiliser les informations d’identification PowerShell dans un Runbook. Dans cet exemple, les informations d’identification sont récupérées et le nom d’utilisateur et le mot de passe affectés à des variables.

	$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
	$userName = $myCredential.UserName
	$securePassword = $myCredential.Password
	$password = $myCredential.GetNetworkCredential().Password


### Exemple de Runbook graphique

Vous ajoutez une activité **Get-AutomationPSCredential** à un Runbook graphique en cliquant avec le bouton droit sur les informations d’identification dans le volet Bibliothèque de l’éditeur graphique et en sélectionnant **Ajouter à la zone de dessin**.


![Ajout d’informations d’identification à la zone de dessin](media/automation-credentials/credential-add-canvas.png)

L’image suivante montre un exemple d’utilisation d’informations d’identification dans un Runbook graphique. Dans ce cas, il est utilisé pour l’authentification d’un Runbook auprès des ressources Azure, comme décrit dans [Authentifier des Runbooks avec un compte d’utilisateur Azure AD](automation-sec-configure-aduser-account.md). La première activité récupère les informations d’identification ayant accès à l’abonnement Azure. L’activité **Add-AzureAccount** utilise ensuite ces informations d’identification pour fournir l’authentification pour toutes les activités qui la suivent. C’est un [lien pipeline](automation-graphical-authoring-intro.md#links-and-workflow), étant donné que **Get-AutomationPSCredential** attend un objet unique.

![Ajout d’informations d’identification à la zone de dessin](media/automation-credentials/get-credential.png)

## Utilisation des informations d’identification PowerShell dans une configuration DSC
Bien que les configurations DSC dans Azure Automation puissent se rapporter à des ressources d’informations d’identification utilisant **Get-AutomationPSCredential**, les ressources d’information d’identification peuvent également transmises via des paramètres, si vous le souhaitez. Pour plus d’informations, consultez [Compilation de configurations dans Azure Automation DSC](automation-dsc-compile.md#credential-assets).

## Étapes suivantes

- Pour en savoir plus sur les liens lors de la création graphique, consultez [Liens lors de la création graphique](automation-graphical-authoring-intro.md#links-and-workflow)
- Pour comprendre les différentes méthodes d’authentification avec Automation, consultez [Sécurité dans Azure Automation](automation-security-overview.md)
- Pour une prise en main des Runbooks graphiques, consultez [Mon premier Runbook graphique](automation-first-runbook-graphical.md)
- Pour une prise en main des Runbooks de workflow PowerShell, consultez [Mon premier Runbook PowerShell Workflow](automation-first-runbook-textual.md) 

 

<!---HONumber=AcomDC_0615_2016-->