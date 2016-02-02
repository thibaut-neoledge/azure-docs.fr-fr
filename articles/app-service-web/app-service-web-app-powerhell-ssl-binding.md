<properties
	pageTitle="Liaison de certificats SSL à l’aide de PowerShell"
	description="Découvrez comment lier des certificats SSL à votre application web à l’aide de PowerShell."
	services="app-service\web"
	documentationCenter=""
	authors="ahmedelnably"
	manager="stefsch"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/13/2016"
	ms.author="ahmedelnably"/>

# Liaison de certificat SSL d’Azure App Service à l’aide de PowerShell #

Avec la publication de Microsoft Azure PowerShell version 1.1.0, une nouvelle applet de commande a été ajoutée. Elle permet à l’utilisateur de lier des certificats SSL nouveaux ou existants à une application web existante.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]


## Chargement et liaison d’un nouveau certificat SSL ##

Scénario : l’utilisateur souhaite lier un certificat SSL à l’une de ses applications web.

Si nous connaissons le nom du groupe de ressources qui contient l’application web, le nom de l’application web, le chemin d’accès du fichier .pfx du certificat sur l’ordinateur de l’utilisateur, le mot de passe pour le certificat et le nom d’hôte personnalisé, nous pouvons utiliser la commande PowerShell suivante pour créer cette liaison SSL :

    New-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -CertificateFilePath PathToPfxFile -CertificatePassword PlainTextPwd -Name www.contoso.com

## Chargement et liaison d’un certificat SSL existant ##

Scénario : l’utilisateur souhaite lier un certificat SSL préalablement chargé à l’une de ses applications web.

Nous pouvons obtenir la liste des certificats déjà chargés vers un groupe de ressources spécifique en utilisant la commande suivante :

	Get-AzureRmWebAppCertificate -ResourceGroupName myresourcegroup

Comme les certificats sont locaux dans un emplacement spécifique et un groupe de ressources, notez que l’utilisateur doit recharger le certificat si l’application web configurée se trouve à un emplacement différent et dans un groupe de ressources autre que celui du certificat nécessaire.

Si nous connaissons le nom du groupe de ressources qui contient l’application web, le nom de l’application web, l’empreinte de certificat et le nom d’hôte personnalisé, nous pouvons utiliser la commande PowerShell suivante pour créer cette liaison SSL :

    New-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Thumbprint <certificate thumbprint> -Name www.contoso.com

## Suppression d’une liaison SSL existante  ##

Scénario : l’utilisateur souhaite supprimer une liaison SSL existante.

Si nous connaissons le nom du groupe de ressources qui contient l’application web, le nom de l’application web et le nom d’hôte personnalisé, nous pouvons utiliser la commande PowerShell suivante pour supprimer cette liaison SSL :

    Remove-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Name www.contoso.com

Notez que si la liaison SSL supprimée était la dernière liaison utilisant ce certificat à cet emplacement, le certificat est supprimé par défaut. Si l’utilisateur souhaite le conserver, il peut utiliser l’option DeleteCertificate.

	Remove-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Name www.contoso.com -DeleteCertificate $false

### Références ###
- [Présentation de l'environnement App Service](app-service-app-service-environment-intro.md)
- [Utilisation d’Azure PowerShell avec Azure Resource Manager](../powershell-azure-resource-manager.md)

<!---HONumber=AcomDC_0121_2016-->