<properties 
   pageTitle="Ressources de connexion dans Azure Automation | Microsoft Azure"
   description="Les ressources de connexion dans Azure Automation contiennent les informations requises pour se connecter à une application ou un service externe à partir d’un Runbook. Cet article présente de façon détaillée les connexions et leur utilisation dans la création textuelle et graphique."
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
   ms.date="08/18/2015"
   ms.author="bwren" />

# Ressources de connexion dans Azure Automation

Une ressource de connexion Automation contient les informations requises pour se connecter à une application ou un service externe à partir d’un Runbook. Cela peut inclure des informations requises pour l’authentification comme un nom d’utilisateur et un mot de passe en plus des informations de connexion telles qu’une URL ou un port. La valeur d’une connexion consiste à conserver toutes les propriétés de connexion à une application spécifique dans une seule ressource plutôt que de créer plusieurs variables. L’utilisateur peut modifier les valeurs pour une connexion à un seul endroit et vous pouvez transmettre le nom d’une connexion à un Runbook dans un seul paramètre. Les propriétés d’une connexion sont accessibles dans le Runbook avec l’activité **Get-AutomationConnection**.

Lorsque vous créez une connexion, vous devez spécifier un *type de connexion*. Le type de connexion est un modèle qui définit un ensemble de propriétés. La connexion définit des valeurs pour chaque propriété définie dans son type de connexion. Des types de connexion sont ajoutés à Azure Automation dans les modules d’intégration ou créés avec l’[API Azure Automation](http://msdn.microsoft.com/library/azure/mt163818.aspx). Les seuls types de connexion disponibles lorsque vous créez une connexion sont ceux installés dans votre compte Automation.

>[AZURE.NOTE]Les ressources sécurisées dans Azure Automation incluent les informations d’identification, les certificats, les connexions et les variables chiffrées. Ces ressources sont chiffrées et stockées dans Azure Automation à l'aide d'une clé unique, générée pour chaque compte Automation. Cette clé est chiffrée par un certificat principal et stockée dans Azure Automation. Avant de stocker une ressource sécurisée, la clé pour le compte Automation est déchiffrée à l’aide du certificat principal, puis utilisée pour chiffrer la ressource.

## Applets de commande Windows PowerShell

Les applets de commande du tableau suivant sont utilisées pour créer et gérer des connexions Automation avec Windows PowerShell. Elles font partie du [module Azure PowerShell](../powershell-install-configure.md) qui est disponible dans les Runbooks Automation.

|Applet de commande|Description|
|:---|:---|
|[Get-AzureAutomationConnection](http://msdn.microsoft.com/library/dn921828.aspx)|Récupère une connexion. Inclut une table de hachage avec les valeurs des champs de la connexion.|
|[New-AzureAutomationConnection](http://msdn.microsoft.com/library/dn921825.aspx)|Crée une connexion.|
|[Remove-AzureAutomationConnection](http://msdn.microsoft.com/library/dn921827.aspx)|Supprime une connexion existante.|
|[Set-AzureAutomationConnectionFieldValue](http://msdn.microsoft.com/library/dn921826.aspx)|Définit la valeur d’un champ particulier d’une connexion existante.|

## Activités de Runbook

Les activités du tableau suivant sont utilisées pour accéder aux connexions dans un Runbook.

|Activités|Description|
|---|---|
|Get-AutomationConnection|Obtient une connexion à utiliser dans un Runbook. Retourne une table de hachage avec les propriétés de la connexion.|

>[AZURE.NOTE]Évitez d’utiliser des variables dans le paramètre – Name de **Get- AutomationConnection**, car cela complique la découverte des dépendances entre les Runbooks et les ressources de connexion au moment de la conception.

## Création d’une connexion

### Pour créer une connexion avec le portail Azure

1. À partir de votre compte Automation, cliquez sur **Ressources** en haut de la fenêtre.
1. En bas de la fenêtre, cliquez sur **Ajouter un paramètre**.
1. Cliquez sur **Ajouter une connexion**.
2. Dans la liste déroulante **Type de connexion**, sélectionnez le type de connexion que vous souhaitez créer. L’Assistant présente les propriétés de ce type.
1. Terminez l’Assistant et activez la case à cocher pour enregistrer la nouvelle connexion.


### Pour créer une connexion avec le portail Azure en version préliminaire

1. À partir de votre compte Automation, cliquez sur la partie **Ressources** afin d’ouvrir le panneau **Ressources**.
1. Cliquez sur la partie **Connexions** afin d’ouvrir le panneau **Connexions**.
1. Cliquez sur **Ajouter une connexion** en haut du panneau.
2. Dans la liste déroulante **Type**, sélectionnez le type de connexion que vous souhaitez créer. Le formulaire présente les propriétés spécifiques à ce type
1. Remplissez le formulaire, puis cliquez sur **Créer** pour enregistrer la nouvelle connexion.



### Pour créer une connexion avec Windows PowerShell

Créer une nouvelle connexion avec Windows PowerShell à l’aide de l’applet de commande [New-AzureAutomationConnection](http://msdn.microsoft.com/library/dn921825.aspx). Cette applet de commande possède un paramètre nommé **ConnectionFieldValues** qui attend une [table de hachage](http://technet.microsoft.com/library/hh847780.aspx) définissant des valeurs pour chacune des propriétés définies par le type de connexion.


Les exemples de commandes suivants créent une nouvelle connexion pour [Twilio](http://www.twilio.com), qui est un service de téléphonie permettant d’envoyer et de recevoir des messages texte. Un exemple de module d’intégration qui inclut un type de connexion Twilio est disponible dans le [Centre de scripts](http://gallery.technet.microsoft.com/scriptcenter/Twilio-PowerShell-Module-8a8bfef8). Ce type de connexion définit des propriétés pour le SID de compte et le jeton d’autorisation, qui sont requis pour valider votre compte lors de la connexion à Twilio. Vous devez [télécharger ce module](http://gallery.technet.microsoft.com/scriptcenter/Twilio-PowerShell-Module-8a8bfef8) et l’installer dans votre compte Automation pour que cet exemple de code fonctionne.

	$AccountSid = "DAf5fed830c6f8fac3235c5b9d58ed7ac5"
	$AuthToken  = "17d4dadfce74153d5853725143c52fd1"
	$FieldValues = @{"AccountSid" = $AccountSid;"AuthToken"=$AuthToken}

	New-AzureAutomationConnection -AutomationAccountName "MyAutomationAccount" -Name "TwilioConnection" -ConnectionTypeName "Twilio" -ConnectionFieldValues $FieldValues


## Utilisation d’une connexion dans un Runbook

Vous récupérez une connexion dans un Runbook avec l’applet de commande **Get-AutomationConnection**. Cette activité récupère les valeurs des différents champs dans la connexion et les retourne sous forme de [table de hachage](http://go.microsoft.com/fwlink/?LinkID=324844), qui peut ensuite être utilisée avec les commandes appropriées dans le Runbook.

### Exemple de Runbook textuel
Les exemples de commandes suivants montrent comment utiliser la connexion Twilio de l’exemple précédent pour envoyer un message texte à partir d’un Runbook. L’activité Send-TwilioSMS utilisée ici comporte deux jeux de paramètres, chacun utilisant une méthode d’authentification différente auprès du service Twilio. L’une utilise un objet de connexion et l’autre des paramètres individuels pour le SID de compte et le jeton d’autorisation. Les deux méthodes sont illustrées dans cet exemple.

	$Con = Get-AutomationConnection -Name "TwilioConnection"
	$NumTo = "14255551212"
	$NumFrom = "15625551212"
	$Body = "Text from Azure Automation."

	#Send text with connection object.
	Send-TwilioSMS -Connection $Con -From $NumFrom -To $NumTo -Body $Body

	#Send text with connection properties.
	Send-TwilioSMS -AccountSid $Con.AccountSid -AuthToken $Con.AuthToken $Con -From $NumFrom -To $NumTo -Body $Body

### Exemples de Runbook graphique

Vous ajoutez une activité **Get-AutomationConnection** à un Runbook graphique en cliquant avec le bouton droit sur la connexion dans le volet Bibliothèque de l’éditeur graphique et en sélectionnant **Ajouter à la zone de dessin**.

![](media/automation-connections/connection-add-canvas.png)

L’image suivante montre un exemple d’utilisation d’une connexion dans un Runbook graphique. Voici le même exemple que le précédent pour envoyer un message texte à l’aide de Twilio à partir d’un Runbook textuel. Cet exemple utilise le paramètre **UseConnectionObject** défini pour l’activité **Send-TwilioSMS**, qui utilise un objet de connexion pour l’authentification auprès du service. Un [lien pipeline](automation-graphical-authoring-intro.md#links-and-workflow) est utilisé ici, car le paramètre Connexion attend un seul objet.

La raison pour laquelle une expression PowerShell est utilisée pour la valeur dans le paramètre **À** plutôt qu’une valeur constante est que ce paramètre attend un type de valeur de tableau de chaînes, afin que vous puissiez envoyer des messages à plusieurs numéros. Une expression PowerShell vous permet de fournir une valeur unique ou un tableau.

![](media/automation-connections/get-connection-object.png)

L’image ci-dessous montre le même exemple que plus haut, mais utilise le jeu de paramètres **SpecifyConnectionFields**, qui attend une spécification individuelle des paramètres AccountSid et AuthToken, plutôt que l’utilisation d’un objet de connexion pour l’authentification. Dans ce cas, les champs de la connexion sont spécifiés, et non l’objet lui-même.

![](media/automation-connections/get-connection-properties.png)



## Articles connexes

- [Liens de création graphique](automation-graphical-authoring-intro.md#links-and-workflow)
 

<!---HONumber=Oct15_HO3-->