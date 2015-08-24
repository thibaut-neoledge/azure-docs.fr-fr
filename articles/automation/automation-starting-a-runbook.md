<properties 
   pageTitle="Démarrage d'un Runbook dans Azure Automation"
   description="Résume les différentes méthodes qui peuvent être utilisées pour démarrer un Runbook dans Azure Automation et fournit des détails sur l'utilisation du portail Azure et de Windows PowerShell."
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
   ms.date="08/11/2015"
   ms.author="bwren" />

# Démarrage d'un Runbook dans Azure Automation

Le tableau suivant vous aide à déterminer la méthode de démarrage d'un Runbook dans Azure Automation la plus appropriée à votre scénario. Cet article inclut des informations détaillées sur le démarrage d'un Runbook avec le portail Azure et avec Windows PowerShell. Des informations supplémentaires sur les autres méthodes sont fournies dans différentes documentations accessibles depuis les liens ci-dessous.

<table>
 <tr>
  <td>MÉTHODE</td>
  <td>CARACTÉRISTIQUES</td>
 </tr>
 <tr>           
  <td><a href="#starting-a-runbook-with-the-azure-portal">Portail Azure</a></td>
  <td>
   <ul>
    <li>Méthode la plus simple avec une interface utilisateur interactive.</li>
    <li>Formulaire pour fournir les valeurs de paramètres simples.</li>
    <li>Suivi aisé de l'état des tâches.</li>
    <li>Accès authentifié avec ouverture de session Azure.</li>
   </ul>
  </td>
 </tr>
 <tr>
  <td><a href="https://msdn.microsoft.com/library/dn690259.aspx">Windows&#160;PowerShell</a></td>
  <td>
   <ul>
     <li>Appel à partir de la ligne de commande avec les applets de commande Windows PowerShell.</li>
     <li>Possibilité d'inclusion dans une solution automatisée à plusieurs étapes.</li>
     <li>Demande authentifiée avec un certificat ou un principal du service&#160;/&#160;principal d'utilisateur OAuth.</li>
     <li>Fourniture de valeurs de paramètres simples et complexes.</li>
     <li>Suivi de l'état des tâches.</li>
     <li>Client requis pour prendre en charge les applets de commande PowerShell.</li>
   </ul>
  </td>
 </tr>
 <tr>
 <tr>
  <td><a href="http://msdn.microsoft.com/library/azure/mt163849.aspx">API Azure Automation</a></td>
  <td>
   <ul>
    <li>Méthode la plus souple, mais également la plus complexe.</li>
    <li>Appel à partir de n'importe quel code personnalisé qui peut envoyer des requêtes HTTP.</li>
    <li>Requête authentifiée avec un certificat ou un principal du service&#160;/&#160;principal d'utilisateur Oauth.</li>
    <li>Fourniture de valeurs de paramètres simples et complexes.</li>
    <li>Suivi de l'état des tâches.</li>
   </ul>
  </td>
 </tr>
 <tr>
 <tr>
  <td><a href="http://azure.microsoft.com/documentation/articles/automation-webhooks/">Webhook</a></td>
  <td>
   <ul>
    <li>Démarrage d'un Runbook à partir d'une simple requête HTTP.</li>
    <li>Authentification avec un jeton de sécurité dans l'URL.</li>
    <li>Le client ne peut pas remplacer les valeurs de paramètre spécifiées lors de la création du Webhook. Le Runbook peut définir un paramètre unique qui est rempli avec les détails de la requête HTTP.</li>
    <li>Aucune possibilité de suivre l'état des tâches via l'URL du Webhook.</li>
   </ul>
  </td>
 </tr>
 <tr>
 <tr>
  <td><a href="http://azure.microsoft.com/documentation/articles/automation-scheduling-a-runbook">Planification</a></td>
  <td>
   <ul>
    <li>Démarrage automatique du Runbook selon une planification horaire, quotidienne ou hebdomadaire.</li>
    <li>Manipulation de la planification via le portail Azure, les applets de commande PowerShell ou les API Azure.</li>
    <li>Fourniture des valeurs de paramètres à utiliser avec la planification.</li>
   </ul>
  </td>
 </tr>
 <tr>
  <td><a href="http://msdn.microsoft.com/library/azure/dn857355.aspx">À partir d'un autre Runbook</a></td>
  <td>
   <ul>
    <li>Utilisation d'un Runbook en tant qu'activité d'un autre Runbook.</li>
    <li>Utile pour les fonctionnalités utilisées par plusieurs Runbooks.</li>
    <li>Fourniture des valeurs de paramètres au Runbook enfant et utilisation de la sortie dans le Runbook parent.</li>
   </ul>
  </td>
 </tr>
</table>
<br>



## Démarrage d'un Runbook avec le portail Azure

1. Dans le portail Azure, sélectionnez **Automation**, puis cliquez sur le nom d'un compte Automation.
1. Sélectionnez l'onglet **Runbooks**.
1. Sélectionnez un Runbook, puis cliquez sur **Démarrer**.
1. Si le Runbook possède des paramètres, vous devez fournir des valeurs avec une zone de texte pour chaque paramètre. Pour plus d'informations sur les paramètres, consultez [Paramètres du Runbook](#Runbook-parameters) ci-dessous.
1. Sélectionnez **Afficher la tâche** à côté du message du Runbook **Démarrage** ou sélectionnez l'onglet **Tâches** pour que le Runbook affiche l'état de la tâche du Runbook.

## Démarrage d'un Runbook avec le portail Azure en version préliminaire

1. À partir de votre compte Automation, cliquez sur la partie **Runbooks** afin d'ouvrir le panneau **Runbooks**.
1. Cliquez sur un Runbook pour ouvrir son panneau **Runbook**.
2. Cliquez sur **Start**.
1. Si le Runbook n'a aucun paramètre, vous devez confirmer si vous souhaitez le démarrer. Si le Runbook possède des paramètres, le panneau **Démarrer le Runbook** s'ouvre pour vous permettre de fournir les valeurs des paramètres. Pour plus d'informations sur les paramètres, consultez [Paramètres du Runbook](#Runbook-parameters) ci-dessous.
3. Le panneau **Tâche** s'ouvre afin que vous puissiez suivre l'état de la tâche.


## Démarrage d'un Runbook avec Windows PowerShell

Vous pouvez utiliser l'applet de commande [Start-AzureAutomationRunbook](http://msdn.microsoft.com/library/azure/dn690259.aspx) pour démarrer un Runbook avec Windows PowerShell. L'exemple de code suivant démarre un Runbook appelé Test-Runbook.

	Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"

Start-AzureAutomationRunbook retourne un objet de traitement que vous pouvez utiliser pour suivre son état une fois que le Runbook a démarré. Vous pouvez ensuite utiliser cet objet de traitement avec [Get-AzureAutomationJob](http://msdn.microsoft.com/library/azure/dn690263.aspx) pour déterminer l'état de la tâche et avec [Get-AzureAutomationJobOutput](http://msdn.microsoft.com/library/azure/dn690268.aspx) pour obtenir sa sortie. L'exemple de code suivant démarre un Runbook appelé Test-Runbook, attend qu'il ait terminé, puis affiche sa sortie.

	$job = Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"
	
	$doLoop = $true
	While ($doLoop) {
	   $job = Get-AzureAutomationJob –AutomationAccountName "MyAutomationAccount" -Id $job.Id
	   $status = $job.Status
	   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped") 
	}
	
	Get-AzureAutomationJobOutput –AutomationAccountName "MyAutomationAccount" -Id $job.Id –Stream Output

Si le Runbook requiert des paramètres, vous devez les fournir comme [table de hachage](http://technet.microsoft.com/library/hh847780.aspx) où la clé de la table de hachage correspond au nom de paramètre et la valeur à la valeur du paramètre. L'exemple suivant montre comment démarrer un Runbook avec deux paramètres de chaîne nommés FirstName et LastName, un entier nommé RepeatCount et un paramètre booléen nommé Show. Pour plus d'informations sur les paramètres, consultez [Paramètres du Runbook](#Runbook-parameters).

	$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
	Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" –Parameters $params

## Paramètres du Runbook

Lorsque vous démarrez un Runbook à l'aide du portail de gestion Azure ou de Windows PowerShell, l'instruction est envoyée via le service web Azure Automation. Ce service ne prend pas en charge les paramètres avec des types de données complexes. Si vous devez fournir une valeur pour un paramètre complexe, vous devez l'appeler en ligne à partir d'un autre Runbook, comme décrit dans [Démarrage d'un Runbook à partir d'un autre Runbook](http://msdn.microsoft.com/library/azure/dn857355.aspx).

Le service web Azure Automation fournit des fonctionnalités spécifiques pour les paramètres en utilisant certains types de données comme décrit dans les sections suivantes.

### Valeurs nommées

Si le paramètre est un type de données [object], vous pouvez utiliser le format JSON suivant pour lui envoyer une liste de valeurs nommées : *{"Nom1":Valeur1, "Nom2":Valeur2, "Nom3":Valeur3}*. Ces valeurs doivent avoir des types simples. Le Runbook reçoit le paramètre comme [PSCustomObject](http://msdn.microsoft.com/library/azure/system.management.automation.pscustomobject(v=vs.85).aspx) avec des propriétés correspondant à chaque valeur nommée.

Considérez le Runbook de test suivant qui accepte un paramètre nommé user.

	Workflow Test-Parameters
	{
	   param ( 
	      [Parameter(Mandatory=$true)][object]$user
	   )
	    if ($user.Show) {
	        foreach ($i in 1..$user.RepeatCount) {
	            $user.FirstName
	            $user.LastName
	        }
	    } 
	}

Le texte suivant peut être utilisé pour le paramètre user.

	{"FirstName":"Joe","LastName":"Smith","RepeatCount":2,"Show":true}

Il s'ensuit la sortie suivante.

	Joe
	Smith
	Joe
	Smith

### Tableaux

Si le paramètre est un tableau comme [array] ou [string], vous pouvez utiliser le format JSON suivant pour lui envoyer une liste de valeurs : *[Valeur1,Valeur2,Valeur3]*. Ces valeurs doivent avoir des types simples.

Considérez le Runbook de test suivant qui accepte un paramètre nommé *user*.

	Workflow Test-Parameters
	{
	   param ( 
	      [Parameter(Mandatory=$true)][array]$user
	   )
	    if ($user[3]) {
	        foreach ($i in 1..$user[2]) {
	            $ user[0]
	            $ user[1]
	        }
	    } 
	}

Le texte suivant peut être utilisé pour le paramètre user.

	["Joe","Smith",2,true]

Il s'ensuit la sortie suivante.

	Joe
	Smith
	Joe
	Smith

### Informations d'identification

Si le paramètre est un type de données **PSCredential**, vous pouvez fournir le nom d'une [ressource d'informations d'identification](http://msdn.microsoft.com/library/azure/dn940015.aspx) Azure Automation. Le Runbook récupère la ressource d'informations d'identification portant le nom que vous spécifiez.

Considérez le Runbook de test suivant qui accepte un paramètre nommé credential.

	Workflow Test-Parameters
	{
	   param ( 
	      [Parameter(Mandatory=$true)][PSCredential]$credential
	   )
	   $credential.UserName
	}

Le texte suivant peut être utilisé pour le paramètre user en supposant qu'il existe une ressource d'informations d'identification nommée *My Credential*.

	My Credential

En supposant que le nom d'utilisateur des informations d'identification soit *jsmith*, il s'ensuit le résultat suivant.

	jsmith

## Articles connexes

- [Démarrage d'un Runbook à partir d'un autre Runbook](http://msdn.microsoft.com/library/azure/dn857355.aspx) 

<!---HONumber=August15_HO7-->