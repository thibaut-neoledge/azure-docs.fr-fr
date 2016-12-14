---
title: "Liaison de certificats SSL à l’aide de PowerShell"
description: "Découvrez comment lier des certificats SSL à votre application web à l’aide de PowerShell."
services: app-service\web
documentationcenter: 
author: ahmedelnably
manager: stefsch
editor: 
ms.assetid: 8ce32508-e982-48d3-b878-0e526afda537
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/13/2016
ms.author: aelnably
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e0302c4e4e63383a2b19bd6f02f3be8d83f5ff98


---
# <a name="azure-app-service-ssl-certificate-binding-using-powershell"></a>Liaison de certificat SSL d’Azure App Service à l’aide de PowerShell
Avec la publication de Microsoft Azure PowerShell version 1.1.0, une nouvelle applet de commande a été ajoutée. Elle permet à l’utilisateur de lier des certificats SSL nouveaux ou existants à une application web existante.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

Pour savoir comment utiliser les applets de commande Azure PowerShell basées sur Azure Resource Manager pour gérer vos applications web, consultez [Commandes PowerShell basées sur Azure Resource Manager pour Application web Azure](app-service-web-app-azure-resource-manager-powershell.md)

## <a name="uploading-and-binding-a-new-ssl-certificate"></a>Chargement et liaison d’un nouveau certificat SSL
Scénario : l’utilisateur souhaite lier un certificat SSL à l’une de ses applications web.

Si nous connaissons le nom du groupe de ressources qui contient l’application web, le nom de l’application web, le chemin d’accès du fichier .pfx du certificat sur l’ordinateur de l’utilisateur, le mot de passe pour le certificat et le nom d’hôte personnalisé, nous pouvons utiliser la commande PowerShell suivante pour créer cette liaison SSL :

    New-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -CertificateFilePath PathToPfxFile -CertificatePassword PlainTextPwd -Name www.contoso.com

Notez qu’avant d’ajouter une liaison SSL à une application web, vous devez déjà avoir configuré un nom d’hôte (domaine personnalisé). Si le nom d’hôte n’est pas configuré, vous recevrez un message d’erreur indiquant que « hostname » n’existe pas lors de l’exécution de New-AzureRmWebAppSSLBinding. Vous pouvez ajouter un nom d’hôte directement depuis le portail ou à l’aide d’Azure PowerShell. L’extrait de code PowerShell suivant peut vous permettre de configurer le nom d’hôte avant d’exécuter New-AzureRmWebAppSSLBinding.   

    $webApp = Get-AzureRmWebApp -Name mytestapp -ResourceGroupName myresourcegroup  
    $hostNames = $webApp.HostNames  
    $HostNames.Add("www.contoso.com")  
    Set-AzureRmWebApp -Name mytestapp -ResourceGroupName myresourcegroup -HostNames $HostNames   

Il est important de comprendre que l’applet de commande Set-AzureRmWebApp remplace les noms d’hôte de l’application web. Par conséquent, l’extrait de code PowerShell ci-dessus vient s’ajouter à la liste existante des noms d’hôte de l’application web.  

## <a name="uploading-and-binding-an-existing-ssl-certificate"></a>Chargement et liaison d’un certificat SSL existant
Scénario : l’utilisateur souhaite lier un certificat SSL préalablement chargé à l’une de ses applications web.

Nous pouvons obtenir la liste des certificats déjà chargés vers un groupe de ressources spécifique en utilisant la commande suivante :

    Get-AzureRmWebAppCertificate -ResourceGroupName myresourcegroup

Comme les certificats sont locaux dans un emplacement spécifique et un groupe de ressources, notez que l’utilisateur doit recharger le certificat si l’application web configurée se trouve à un emplacement différent et dans un groupe de ressources autre que celui du certificat nécessaire. 

Si nous connaissons le nom du groupe de ressources qui contient l’application web, le nom de l’application web, l’empreinte de certificat et le nom d’hôte personnalisé, nous pouvons utiliser la commande PowerShell suivante pour créer cette liaison SSL :

    New-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Thumbprint <certificate thumbprint> -Name www.contoso.com

## <a name="deleting-an-existing-ssl-binding"></a>Suppression d’une liaison SSL existante
Scénario : l’utilisateur souhaite supprimer une liaison SSL existante.

Si nous connaissons le nom du groupe de ressources qui contient l’application web, le nom de l’application web et le nom d’hôte personnalisé, nous pouvons utiliser la commande PowerShell suivante pour supprimer cette liaison SSL :

    Remove-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Name www.contoso.com

Notez que si la liaison SSL supprimée était la dernière liaison utilisant ce certificat à cet emplacement, le certificat est supprimé par défaut. Si l’utilisateur souhaite le conserver, il peut utiliser l’option DeleteCertificate.

    Remove-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Name www.contoso.com -DeleteCertificate $false

### <a name="references"></a>Références
* [Commandes PowerShell basées sur Azure Resource Manager pour Application web Azure](app-service-web-app-azure-resource-manager-powershell.md)
* [Présentation de l'environnement App Service](app-service-app-service-environment-intro.md)
* [Utilisation d’Azure PowerShell avec Azure Resource Manager](../powershell-azure-resource-manager.md)




<!--HONumber=Nov16_HO3-->


