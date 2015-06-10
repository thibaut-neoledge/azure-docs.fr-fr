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
   ms.date="05/13/2015"
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
| Activé | Un Runbook est activé par défaut lorsqu'il est créé. Si vous le définissez sur Disabled, aucun client ne sera en mesure de l'utiliser. Vous pouvez définir la propriété **Enabled** lorsque vous créez le webhook ou à tout moment après qu'il a été créé. |


### Paramètres
Un webhook peut définir les valeurs des paramètres du Runbook qui sont utilisées lorsque le Runbook est démarré par ce webhook. Le webhook doit inclure les valeurs de tous les paramètres obligatoires du Runbook et peut inclure les valeurs des paramètres optionnels. Plusieurs webhooks liés à un même Runbook peuvent utiliser différentes valeurs de paramètres.

>[AZURE.NOTE]Les valeurs de paramètres définies par un webhook ne peuvent pas être modifiées après sa création. Vous devez créer un autre webhook qui utilise des valeurs de paramètres différentes.

Lorsqu'un client démarre un Runbook à l'aide d'un webhook, il ne peut pas remplacer les valeurs de paramètres définies dans le webhook. Pour recevoir les données du client, le Runbook peut accepter un paramètre unique appelé **$WebhookData** de type [object] qui contient les données que le client inclut dans la requête POST.

![Données du webhook](media/automation-webhooks/webhookdata.png)

L'objet **$WebhookData** possède les propriétés suivantes :

| Propriété | Description |
|:--- |:---|
| WebhookName | Nom du webhook. |
| RequestHeader | En-têtes de la requête POST entrante. |
| RequestBody | Corps de la requête POST entrante. |

Il n'existe aucune configuration du webhook requise pour prendre en charge le paramètre **$WebhookData** et le Runbook n'est pas obligé de l'accepter. Si le Runbook ne définit pas le paramètre, tous les détails de la demande envoyée depuis le client sont ignorés.

Si vous spécifiez une valeur pour $WebhookData lorsque vous créez le webhook, cette valeur est remplacée lorsque le webhook démarre le Runbook avec les données de la demande POST du client, même si le client n'inclut pas toutes les données dans le corps de la demande. Si vous démarrez un Runbook ayant $WebhookData à l'aide d'une méthode autre qu'un webhook, vous pouvez fournir une valeur pour $Webhookdata qui sera reconnue par le Runbook. Cette valeur doit être un objet avec les mêmes propriétés que $Webhookdata afin que le Runbook puisse l'utiliser correctement.

>[AZURE.NOTE]Les valeurs de tous les paramètres d'entrée sont enregistrés avec la tâche du Runbook. Cela signifie qu'une entrée fournie par le client sera enregistrée avec $WebhookData et accessible à toute personne ayant accès à la tâche Automation. Pour cette raison, soyez prudent lorsque vous incluez des informations sensibles dans les appels du webhook.

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
1. Cliquez sur **Créer** pour créer le webhook.
3. Cliquez sur **Paramètres** pour fournir les valeurs des paramètres du Runbook. <br>
1. Cliquez sur **OK** lorsque vous avez terminé la configuration du webhook.


## Utilisation d'un webhook

Pour utiliser un webhook, votre application cliente doit émettre une demande HTTP POST avec l'URL du webhook. La syntaxe du webhook sera au format suivant.

	http://<Webhook Server>/token?=<Token Value>


Le client reçoit l'un des codes suivants en réponse à la demande POST.

| Code | Texte | Description |
|:---|:----|:---|
| 202 | Acceptée | La demande a été acceptée et le Runbook a été démarré avec succès. |
| 400 | Demande incorrecte | La demande a été refusée pour l'une des raisons suivantes. <ul> <li>Le webhook a expiré.</li> <li>Le webhook est désactivé.</li> <li>Le jeton de l'URL n'est pas valide.</li> </ul>|
| 500 | Erreur interne du serveur | L'URL est valide, mais une erreur s'est produite. Soumettez à nouveau la demande. |

Le client ne parvient pas à déterminer quand la tâche du Runbook se termine ou son état d'achèvement à partir d'un webhook, car le webhook ne renvoie pas d'identificateur pour la tâche du Runbook. Vous pouvez uniquement valider que la demande a été envoyée avec succès.

### exemples

L'exemple suivant démarre un Runbook à l'aide d'un webhook avec Windows PowerShell. Cet exemple inclut des données dans l'en-tête et dans le corps qui peuvent être utilisées par le Runbook. Notez que n'importe quel langage capable d'effectuer une demande HTTP peut utiliser un webhook.

	$uri = "https://oaaswebhookcurrent.cloudapp.net/webhooks?token=8ud0dSrSo%2fvHWpYbklW%3c8s0GrOKJZ9Nr7zqcS%2bIQr4c%3d"
	$headers = @{"header1"="headerval1";"header2"="headerval2"}
	$body = "some request body"

	Invoke-RestMethod -Method Post -Uri $uri -Headers $headers -Body $body

L'exemple suivant de Runbook accepte la demande précédente et extrait les données du webhook.

	workflow Sample-Webhook
	{
		param (	
				[object]$WebhookData
		)
	
		$WebhookName 	= 	$WebhookData.WebhookName
		$WebhookHeaders = 	$WebhookData.RequestHeader
		$WebhookBody 	= 	$WebhookData.RequestBody
	} 

	

## Articles connexes

- [Démarrage d'un Runbook](automation-starting-a-runbook.md)
- [Affichage de l'état d'une tâche du Runbook](automation-viewing-the-status-of-a-runbook-job.md)

<!---HONumber=58-->