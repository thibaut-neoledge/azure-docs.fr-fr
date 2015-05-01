<properties 
   pageTitle="Utilisation d'une commande Azure PowerShell pour créer un conteneur de service cloud vide" 
   description="Cet article explique comment créer un conteneur de service cloud et exécuter des opérations de gestion liées au service cloud à l'aide d'un script PowerShell" 
   services="cloud-services" 
   documentationCenter=".net" 
   authors="cawaMS" 
   manager="" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="na" 
   ms.date="02/20/2015"
   ms.author="cawa"/>

# Utilisation d'une commande Azure PowerShell pour créer un conteneur de service cloud vide
1. Installez l'applet de commande Microsoft Azure PowerShell [télécharger Azure PowerShell](http://go.microsoft.com/?linkid=9811175&clcid=0x409). Pour obtenir des instructions complémentaires sur l'installation de l'applet de commande Azure PowerShell et sur la connexion à votre abonnement Azure, reportez-vous à l'article [Installation et configuration d'Azure PowerShell Azure PowerShell](install-configurer-powershell.md).

2. Vous pouvez créer un conteneur de service cloud vide grâce à l'applet de commande **New-AzureService**. 

    ```
    New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>] 
    New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
```

   Voici un exemple d'appel de l'applet de commande : 
```
New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"
```

3. Pour plus d'informations sur la création d'un service cloud Azure, exécutez : 
```
Get-help New-AzureService
```

4. Étapes suivantes :

   - Pour gérer le déploiement du service cloud, reportez-vous aux rubriques qui traitent des commandes [Get-AzureService](https://msdn.microsoft.com/library/azure/dn495131.aspx), [Remove-AzureService](https://msdn.microsoft.com/library/azure/dn495120.aspx) et [Set-AzureService](https://msdn.microsoft.com/library/azure/dn495242.aspx). Consultez également l'article [Configuration des services cloud](cloud-services-how-to-configure.md)

    - Pour publier votre projet de service cloud dans Azure, reportez-vous à l'exemple de code **PublishCloudService.ps1** disponible dans l'article [Remise continue pour Cloud Services dans Azure](cloud-services-dotnet-continuous-delivery.md)


    



<!--HONumber=52-->