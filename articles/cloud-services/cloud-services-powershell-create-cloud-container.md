<properties
   pageTitle="Utilisation d’une commande Azure PowerShell pour créer un conteneur de service cloud vide | Microsoft Azure"
   description="Cet article explique comment créer un conteneur de service cloud et exécuter des opérations de gestion liées au service cloud à l’aide d’un script PowerShell."
   services="cloud-services"
   documentationCenter=".net"
   authors="cawaMS"
   manager="paulyuk"
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="na"
   ms.date="01/13/2015"
   ms.author="cawa"/>

# Utiliser une commande Azure PowerShell pour créer un conteneur de service cloud vide
Cet article explique comment créer rapidement un conteneur Cloud Services à l’aide d’applets de commande Azure PowerShell. Procédez comme suit :

1. Installez l’applet de commande Microsoft Azure PowerShell à partir de la page [Téléchargements d’Azure PowerShell](http://aka.ms/webpi-azps).
2. Ouvrez l’invite de commandes PowerShell.
3. Utilisez l’applet de commande [Add-AzureAccount](https://msdn.microsoft.com/library/dn495128.aspx) pour vous connecter.

    > [AZURE.NOTE] Pour obtenir des instructions complémentaires sur l’installation de l’applet de commande Azure PowerShell et sur la connexion à votre abonnement Azure, consultez l’article [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).

4. Créez un conteneur de service cloud Azure vide à l’aide de l’applet de commande **New-AzureService**.

    ```
    New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
    New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
```

5. Suivez cet exemple pour appeler l’applet de commande : ```
New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"
```

Pour plus d’informations sur la création du service cloud Azure, exécutez : ```
Get-help New-AzureService
```

### Étapes suivantes

 * Pour gérer le déploiement du service cloud, reportez-vous aux rubriques relatives aux commandes [Get-AzureService](https://msdn.microsoft.com/library/azure/dn495131.aspx), [Remove-AzureService](https://msdn.microsoft.com/library/azure/dn495120.aspx) et [Set-AzureService](https://msdn.microsoft.com/library/azure/dn495242.aspx). Pour plus d’informations, vous pouvez également consulter l’article [Configuration des services cloud](cloud-services-how-to-configure.md).

 * Pour publier votre projet de service cloud dans Azure, consultez l’exemple de code **PublishCloudService.ps1** dans l’article [Livraison continue du service cloud dans Azure](cloud-services-dotnet-continuous-delivery.md).

<!---HONumber=AcomDC_0204_2016-->