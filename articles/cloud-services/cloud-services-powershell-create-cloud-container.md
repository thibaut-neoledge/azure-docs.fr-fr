<properties
   pageTitle="Utilisation d'une commande Azure PowerShell pour créer un conteneur de service cloud vide"
   description="Cet article explique comment créer un conteneur de service cloud et exécuter des opérations de gestion liées au service cloud à l'aide d'un script PowerShell"
   services="cloud-services"
   documentationCenter=".net"
   authors="cawaMS"
   manager="bscholl" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="na"
   ms.date="06/19/2015"
   ms.author="cawa"/>

# Utilisation d'une commande Azure PowerShell pour créer un conteneur de service cloud vide
1. Installez la cmdlet Microsoft Azure PowerShell à partir de [Télécharger Azure PowerShell](http://go.microsoft.com/?linkid=9811175&clcid=0x409) : Pour obtenir des instructions complémentaires sur l'installation de la cmdlet Azure PowerShell et sur la connexion à votre abonnement Azure, reportez-vous à l'article [Installation et configuration d'Azure PowerShell](../powershell-install-configure.md).

2. Vous pouvez créer un conteneur de service cloud vide grâce à la cmdlet **New-AzureService**.

    ```
    New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
    New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
```

   Voici un exemple d’appel de cmdlet : ```
New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"
```

3. Pour plus d'informations sur la création d'un service cloud Azure, exécutez : ```
Get-help New-AzureService
```

4. Étapes suivantes :

   - Pour gérer le déploiement du service cloud, reportez-vous aux rubriques relatives aux commandes [Get-AzureService](https://msdn.microsoft.com/library/azure/dn495131.aspx), [Remove-AzureService](https://msdn.microsoft.com/library/azure/dn495120.aspx) et [Set-AzureService](https://msdn.microsoft.com/library/azure/dn495242.aspx). Reportez-vous également à la rubrique [Configuration de Cloud Services](cloud-services-how-to-configure.md).

    - Pour publier votre projet de service cloud dans Azure, reportez-vous à l'exemple de code **PublishCloudService.ps1** disponible dans l'article [Remise continue pour Cloud Services dans Azure](cloud-services-dotnet-continuous-delivery.md)
 

<!---HONumber=62-->