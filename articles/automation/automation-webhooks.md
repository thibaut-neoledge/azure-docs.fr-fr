<properties 
   pageTitle="Webhooks Azure Automation"
   description="Webhook qui permet à un client de démarrer un Runbook dans Azure Automation à partir d'un appel HTTP. Cet article décrit comment créer un webhook et l'appeler pour démarrer un Runbook."
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
   ms.date="07/05/2015"
   ms.author="bwren" />

# Webhooks Azure Automation

Un *webhook* vous permet de démarrer un Runbook dans Azure Automation via une simple requête HTTP. Les services externes, tels que Visual Studio Online, GitHub ou les applications personnalisées, peuvent ainsi démarrer les Runbooks sans avoir à implémenter une solution complète à l'aide de l'API d'Azure Automation.

![Webhooks](media/automation-webhooks/webhooks-overview.png)

Vous pouvez comparer les webhooks à d'autres méthodes de démarrage d'un Runbook dans [Démarrage d'un Runbook dans Azure Automation](automation-starting-a-runbook.md)

## Détails d'un webhook

Le tableau suivant décrit les propriétés que vous devez configurer pour un webhook.

| Propriété | Description |
|:---|:---|
|Nom | Vous pouvez attribuer le nom de votre choix à un webhook, car il n'apparaît pas au client. Il est uniquement utilisé pour que vous puissiez identifier le Runbook dans Azure Automation. <br> À titre de meilleure pratique, nommez le webhook d'après le client qui l'utilise. |
|URL |L'URL du webhook est l'adresse unique qu'un client appelle avec une méthode HTTP POST pour démarrer le Runbook lié au webhook. Elle est générée automatiquement lorsque vous créez le webhook. Vous ne pouvez pas spécifier d'URL personnalisée. <br> <br> L'URL contient un jeton de sécurité qui permet que le Runbook soit appelé par un système tiers sans authentification supplémentaire. Pour cette raison, elle doit être traitée comme un mot de passe. Pour des raisons de sécurité, vous pouvez uniquement afficher l'URL dans le portail Azure en version préliminaire au moment de la création du webhook. Notez l'URL dans un emplacement sécurisé en vue d'une utilisation ultérieure. |
|Date d'expiration | Comme un certificat, chaque webhook possède une date d'expiration à partir de laquelle il ne peut plus être utilisé. Cette date d'expiration ne peut pas être modifiée après la création du webhook et le webhook ne peut plus être activé à nouveau après que la date d'expiration a été atteinte. Dans ce cas, vous devez créer un autre webhook pour remplacer l'actuel et mettre à jour le client pour utiliser le nouveau webhook. |
| Activé | Un webhook est activé par défaut lorsqu'il est créé. Si vous le définissez sur Disabled, aucun client ne sera en mesure de l'utiliser. Vous pouvez définir la propriété **Enabled** lorsque vous créez le webhook ou à tout moment après qu'il a été créé. |


### Paramètres
Un webhook peut définir les valeurs des paramètres du Runbook qui sont utilisées lorsque le Runbook est démarré par ce webhook. Le webhook doit inclure les valeurs de tous les paramètres obligatoires du Runbook et peut inclure les valeurs des paramètres optionnels. Plusieurs webhooks liés à un même Runbook peuvent utiliser différentes valeurs de paramètres.

>[AZURE.NOTE]Les valeurs de paramètres définies par un webhook ne peuvent pas être modifiées après sa création. Vous devez créer un autre webhook qui utilise des valeurs de paramètres différentes.

Lorsqu'un client démarre un Runbook à l'aide d'un webhook, il ne peut pas remplacer les valeurs de paramètres définies dans le webhook. Pour recevoir les données du client, le Runbook peut accepter un paramètre unique appelé **$WebhookData** de type [object] qui contient les données que le client inclut dans la requête POST.

![Données du webhook](media/automation-webhooks/webhookdata.png)

L'objet **$WebhookData** possède les propriétés suivantes :

| Propriété | Description |
|:--- |:---|
| WebhookName | Nom du webhook. |
| RequestHeader | Table de hachage contenant les en-têtes de la requête POST entrante. |
| RequestBody | Corps de la requête POST entrante. Ceci conserve les mises en forme telles que les chaînes, JSON, XML ou les données de formulaire codées. Le Runbook doit être écrit pour fonctionner avec le format de données qui est attendu.|


Il n'existe aucune configuration du webhook requise pour prendre en charge le paramètre **$WebhookData** et le Runbook n'est pas obligé de l'accepter. Si le Runbook ne définit pas le paramètre, tous les détails de la demande envoyée depuis le client sont ignorés.

Si vous spécifiez une valeur pour $WebhookData lorsque vous créez le webhook, cette valeur est remplacée lorsque le webhook démarre le Runbook avec les données de la demande POST du client, même si le client n'inclut pas toutes les données dans le corps de la demande. Si vous démarrez un Runbook ayant $WebhookData à l'aide d'une méthode autre qu'un webhook, vous pouvez fournir une valeur pour $Webhookdata qui sera reconnue par le Runbook. Cette valeur doit être un objet avec les mêmes propriétés que $Webhookdata afin que le Runbook puisse l'utiliser correctement.

>[AZURE.NOTE]Les valeurs de tous les paramètres d'entrée sont enregistrés avec la tâche du Runbook. Cela signifie qu'une entrée fournie par le client dans la requête webhook sera enregistrée et accessible à toute personne ayant accès à la tâche Automation. Pour cette raison, soyez prudent lorsque vous incluez des informations sensibles dans les appels du webhook.

## Sécurité

La sécurité d'un webhook s'appuie sur la confidentialité de son URL, laquelle contient un jeton de sécurité lui permettant d'être appelée. Azure Automation n'effectue pas d'authentification de la demande tant qu'elle est adressée à la bonne URL. Pour cette raison, les webhooks ne doivent pas être utilisés pour les Runbooks qui exécutent des fonctions très sensibles sans recourir à un autre moyen de validation de la demande.

Vous pouvez inclure une logique dans le Runbook pour déterminer qu'il a été appelé par un webhook en vérifiant la propriété **WebhookName** du paramètre $WebhookData. Le Runbook peut effectuer une validation supplémentaire en recherchant des informations spécifiques dans les propriétés **RequestHeader** ou **RequestBody**.

Une autre stratégie consiste à ce que le Runbook effectue la validation d'une condition externe lorsqu'il reçoit une demande du webhook. Par exemple, envisagez un Runbook appelé par GitHub chaque fois qu'il est procédé à une nouvelle validation d'un référentiel GitHub. Le Runbook peut se connecter à GitHub pour s'assurer qu'une nouvelle validation vient juste de se produire avant de continuer.

## Création d'un webhook

Utilisez la procédure suivante pour créer un webhook lié à un Runbook dans le portail Azure en version préliminaire.

1. À partir du panneau **Runbooks** du portail Azure en version préliminaire, cliquez sur le Runbook que le webhook démarrera pour afficher son panneau Détails. 
3. Cliquez sur **Webhook** en haut du panneau pour ouvrir le panneau **Ajouter un webhook**. <br> ![Bouton Webhooks](media/automation-webhooks/webhooks-button.png)
4. Cliquez sur **Créer un nouveau webhook** pour ouvrir le panneau **Créer un webhook**.
5. Spécifiez un **Nom** et une **Date d'Expiration** pour le webhook, ainsi que s'il doit être activé. Pour plus d'informations sur ces propriétés, consultez [Détails d'un webhook](#details-of-a-webhook).
6. Cliquez sur l'icône de copie et appuyez sur Ctrl + C pour copier l'URL du webhook. Puis enregistrez-la dans un endroit sûr. **Une fois que vous avez créé le webhook,vous ne pouvez pas récupérer l'URL à nouveau.** <br> ![URL du webhook](media/automation-webhooks/copy-webhook-url.png)
3. Cliquez sur **Paramètres** pour fournir les valeurs des paramètres du Runbook. Si le Runbook possède des paramètres obligatoires, vous ne pourrez pas créer le webhook, sauf si des valeurs sont fournies.
1. Cliquez sur **Créer** pour créer le webhook.


## Utilisation d'un webhook

Pour utiliser un webhook après sa création, votre application cliente doit émettre une requête HTTP POST avec l'URL du webhook. La syntaxe du webhook sera au format suivant.

	http://<Webhook Server>/token?=<Token Value>

Le client reçoit l'un des codes de réponse suivants à la requête POST.

| Code | Texte | Description |
|:---|:----|:---|
| 202 | Acceptée | La requête a été acceptée et le Runbook a été mis en file d'attente avec succès. |
| 400 | Demande incorrecte | La demande a été refusée pour l'une des raisons suivantes. <ul> <li>Le webhook a expiré.</li> <li>Le webhook est désactivé.</li> <li>Le jeton de l'URL n'est pas valide.</li> </ul>|
| 404 | Introuvable | La demande a été refusée pour l'une des raisons suivantes. <ul><li>Le webhook est introuvable.</li> <li>Le Runbook est introuvable.</li> <li>Le compte est introuvable.</li></ul> |
| 500 | Erreur interne du serveur | L'URL est valide, mais une erreur s'est produite. Soumettez à nouveau la demande. |

En supposant que la requête aboutisse, la réponse webhook contient l'ID de travail au format JSON comme suit. Elle contient un ID de tâche unique, mais le format JSON permet des améliorations ultérieures potentielles.

	{"JobIds":["<JobId>"]}  

Le client ne peut pas déterminer l'issue du travail du Runbook ou de son état d'achèvement à partir du webhook. Il peut déterminer ces informations à l'aide de l'ID de travail avec une autre méthode telle que [Windows PowerShell](http://msdn.microsoft.com/library/azure/dn690263.aspx) ou [API d'Azure Automation](https://msdn.microsoft.com/library/azure/mt163826.aspx).

### Exemple

L'exemple suivant utilise Windows PowerShell pour démarrer un Runbook avec un webhook. Notez que n'importe quel langage qui peut effectuer une requête HTTP peut utiliser un webhook ; Windows PowerShell est uniquement utilisé ici à titre d'exemple.

Le Runbook s'attend à une liste de machines virtuelles au format JSON dans le corps de la requête. Nous allons également inclure des informations sur qui démarre le Runbook, ainsi que la date et l'heure de son démarrage dans l'en-tête de la requête.

	$uri = "https://s1events.azure-automation.net/webhooks?token=8ud0dSrSo%2fvHWpYbklW%3c8s0GrOKJZ9Nr7zqcS%2bIQr4c%3d"
	$headers = @{"From"="user@contoso.com";"Date"="05/28/2015 15:47:00"}
    
    $vms  = @([pscustomobject]@{Name="vm01";ServiceName="vm01"})
    $vms += @([pscustomobject]@{Name="vm02";ServiceName="vm02"})
	$body = ConvertTo-Json -InputObject $vms 

	$response = Invoke-RestMethod -Method Post -Uri $uri -Headers $headers -Body $body
	$jobid = ConvertFrom-Json $response 


L'illustration suivante montre les informations d'en-tête (à l'aide d'un suivi [Fiddler](http://www.telerik.com/fiddler)) à partir de cette requête. Cela inclut les en-têtes standard d'une requête HTTP en plus des en-têtes Date et De personnalisés que nous avons ajoutés. Chacune de ces valeurs est disponible pour le Runbook dans la propriété **RequestHeaders** de **WebhookData**.

![Bouton Webhooks](media/automation-webhooks/webhook-request-headers.png)

L'illustration suivante montre le corps de la requête (à l'aide d'un suivi [Fiddler](http://www.telerik.com/fiddler)) qui est disponible pour le Runbook dans la propriété **RequestBody** de **WebhookData**. Il est au format JSON car il s'agit du format qui a été inclus dans le corps de la requête.

![Bouton Webhooks](media/automation-webhooks/webhook-request-body.png)

L'illustration suivante montre la requête envoyée à partir de Windows PowerShell et sa réponse. L'ID de travail est extrait de la réponse et converti en une chaîne.

![Bouton Webhooks](media/automation-webhooks/webhook-request-response.png)

L'exemple suivant de Runbook accepte la requête de l'exemple précédent et démarre les machines virtuelles spécifiées dans le corps de la requête.

	workflow Test-StartVirtualMachinesFromWebhook
	{
		param (	
			[object]$WebhookData
		)

		# If runbook was called from Webhook, WebhookData will not be null.
		if ($WebhookData -ne $null) {	
			
			# Collect properties of WebhookData
			$WebhookName 	= 	$WebhookData.WebhookName
			$WebhookHeaders = 	$WebhookData.RequestHeader
			$WebhookBody 	= 	$WebhookData.RequestBody
			
			# Collect individual headers. VMList converted from JSON.
			$From = $WebhookHeaders.From
			$VMList = (ConvertFrom-Json -InputObject $WebhookBody).VirtualMachines
			Write-Output "Runbook started from webhook $WebhookName by $From."
			
			# Authenticate to Azure resources
			$Cred = Get-AutomationPSCredential -Name 'MyAzureCredential'
			Add-AzureAccount -Credential $Cred
			
            # Start each virtual machine
			foreach ($VM in $VMList)
			{
				Write-Output "Starting $VM.Name."
				Start-AzureVM -Name $VM.Name -ServiceName $VM.ServiceName
			}
		}
		else {
			Write-Error "Runbook mean to be started only from webhook." 
		} 
	}

	

## Articles connexes

- [Démarrage d'un Runbook](automation-starting-a-runbook.md)
- [Affichage de l'état d'une tâche du Runbook](automation-viewing-the-status-of-a-runbook-job.md) 

<!---HONumber=July15_HO3-->