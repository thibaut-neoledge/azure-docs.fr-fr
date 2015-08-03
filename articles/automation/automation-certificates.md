<properties 
   pageTitle="Ressources de certificats dans Azure Automation"
   description="Les certificats peuvent être stockés en toute sécurité dans Azure Automation afin d’y accéder par Runbooks pour s’authentifier auprès des ressources Azure et tierces. Cet article présente les certificats et leur utilisation dans la création textuelle et graphique."
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
   ms.date="06/14/2015"
   ms.author="bwren" />

# Ressources de certificats dans Azure Automation

Les certificats peuvent être stockés en toute sécurité dans Azure Automation afin d’y accéder par Runbooks à l’aide de l’activité **Get-AutomationCertificate**. Cela vous permet de créer des Runbooks qui utilisent des certificats pour l’authentification ou les ajoute aux ressources Azure ou tierces que votre Runbook crée ou configure.

>[AZURE.NOTE]Les ressources sécurisées dans Azure Automation incluent les informations d’identification, les certificats, les connexions et les variables chiffrées. Ces ressources sont chiffrées et stockées dans Azure Automation à l'aide d'une clé unique, générée pour chaque compte Automation. Cette clé est chiffrée par un certificat principal et stockée dans Azure Automation. Avant de stocker une ressource sécurisée, la clé pour le compte Automation est déchiffrée à l’aide du certificat principal, puis utilisée pour chiffrer la ressource.

## Applets de commande Windows PowerShell

Les applets de commande du tableau suivant sont utilisées pour créer et gérer les ressources de certificats Automation avec Windows PowerShell. Elles font partie du [module Azure PowerShell](../powershell-install-configure.md) qui est disponible dans les Runbooks Automation.

|Applets de commande|Description|
|:---|:---|
|[Get-AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913765.aspx)|Récupère des informations sur un certificat. Vous pouvez uniquement récupérer le certificat lui-même à partir de l’activité Get-AutomationCertificate.|
|[New- AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913764.aspx)|Importe un nouveau certificat dans Azure Automation.|
|[Remove- AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913773.aspx)|Supprime un certificat dans Azure Automation.|
|[Set- AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913763.aspx)|Définit les propriétés d’un certificat existant, y compris le téléchargement du fichier de certificat et le mot de passe d’un fichier .pfx.|

## Activités de Runbook

Les activités dans le tableau suivant sont utilisées pour accéder aux certificats dans un Runbook.

|Activités|Description|
|:---|:---|
|Get-AutomationCertificate|Obtient un certificat à utiliser dans un Runbook.|

>[AZURE.NOTE]Évitez d’utiliser des variables dans le paramètre – Name de GetAutomationCertificate, car cela complique la découverte des dépendances entre les Runbooks et les ressources de certificats au moment de la conception.

## Création d’un certificat

Lorsque vous créez un certificat, vous téléchargez un fichier cer ou pfx dans Azure Automation. Si vous marquez le certificat comme exportable, vous pouvez également le transférer du magasin de certificats Azure Automation. S’il n’est pas exportable, il peut uniquement être utilisé pour la signature dans le Runbook.

### Pour créer un certificat avec le portail Azure

1. À partir de votre compte Automation, cliquez sur **Ressources** en haut de la fenêtre.
1. En bas de la fenêtre, cliquez sur **Ajouter un paramètre**.
1. Cliquez sur **Ajouter les informations d’identification**.
2. Dans la liste déroulante **Type d’informations d’identification**, sélectionnez **Certificat**.
3. Tapez un nom pour le certificat dans la zone **Nom**, puis cliquez sur la flèche droite.
4. Recherchez un fichier .cer ou .pfx. Si vous sélectionnez un fichier .pfx, spécifiez un mot de passe et s’il est autorisé à être exporté.
1. Cliquez sur la coche pour télécharger le fichier de certificat et enregistrer la nouvelle ressource de certificat.


### Pour créer un certificat avec le portail Azure en version préliminaire

1. À partir de votre compte Automation, cliquez sur la partie **Ressources** afin d’ouvrir le panneau **Ressources**.
1. Cliquez sur la partie **Certificats** afin d’ouvrir le panneau **Certificats**.
1. Cliquez sur **Ajouter un certificat** en haut du panneau.
2. Tapez un nom pour le certificat dans la zone **Nom**.
2. Cliquez sur **Sélectionner un fichier** sous **Télécharger un fichier de certificat** pour rechercher un fichier .cer ou .pfx. Si vous sélectionnez un fichier .pfx, spécifiez un mot de passe et s’il est autorisé à être exporté.
1. Cliquez sur **Créer** pour enregistrer la nouvelle ressource de certificat.


### Pour créer un certificat avec Windows PowerShell

Les exemples de commandes suivants montrent comment créer un certificat Automation et le marquer comme exportable. Cette opération importe un fichier pfx existant.

	$certName = 'MyCertificate'
	$certPath = '.\MyCert.pfx'
	$certPwd = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
	
	New-AzureAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certName -Path $certPath –Password $certPwd -Exportable

## Utilisation d’un certificat dans un Runbook

Vous devez utiliser l’activité **Get-AutomationCertificate** pour utiliser un certificat dans un Runbook. Vous ne pouvez pas utiliser l’applet de commande [Get-AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913765.aspx) dans la mesure où elle retourne des informations sur la ressource de certificat, mais pas le certificat lui-même.

### Exemple de Runbook textuel

L’exemple de code suivant montre comment ajouter un certificat à un service cloud dans un Runbook. Dans cet exemple, le mot de passe est récupéré à partir d’une variable Automation chiffrée.

	$serviceName = 'MyCloudService'
	$cert = Get-AutomationCertificate -Name 'MyCertificate'
	$certPwd = Get-AutomationVariable –Name 'MyCertPassword'
	Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert

### Exemple de Runbook graphique

Vous ajoutez un **Get-AutomationCerticiate** à un Runbook graphique en cliquant avec le bouton droit sur le certificat dans le volet Bibliothèque de l’éditeur graphique et en sélectionnant **Ajouter à la zone de dessin**.

![](media/automation-certificates/certificate-add-canvas.png)

L’image suivante montre un exemple d’utilisation d’un certificat dans un Runbook graphique. Il s’agit du même exemple que celui affiché plus haut pour ajouter un certificat à un service cloud à partir d’un Runbook textuel.

Cet exemple utilise le paramètre **UseConnectionObject** défini pour l’activité Send-**TwilioSMS**, qui utilise un objet de connexion pour l’authentification auprès du service. Un [lien pipeline](automation-graphical-authoring-intro.md#links-and-workflow) doit être utilisé ici dans la mesure où un lien de séquence retournerait une collection contenant un seul objet que le paramètre Connexion n’attend pas.

![](media/automation-certificates/add-certificate.png)


## Voir aussi

- [Liens de création graphique](automation-graphical-authoring-intro.md#links-and-workflow) 

<!---HONumber=July15_HO4-->