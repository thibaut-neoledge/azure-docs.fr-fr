

## Interface de ligne de commande Azure
> [!NOTE]
> Cet article explique comment parcourir et sélectionner des images de machine virtuelle, à l’aide d’une installation récente d’Azure PowerShell ou de l’interface de ligne de commande Azure. Vous devez au préalable passer en mode Resource Manager. Avec l'interface CLI Azure, entrez ce mode en tapant `azure config mode arm`.
> 
> 

La façon la plus simple et la plus rapide de rechercher une image à utiliser avec `azure vm quick-create` ou de créer un fichier de modèle de groupe de ressources consiste à appeler la commande `azure vm image list` et de passer l'emplacement, le nom de l'éditeur (qui ne respecte pas la casse) et une offre (si vous connaissez l'offre). La liste suivante en est un petit exemple (de nombreuses listes sont assez longues) si vous savez que « Canonical » est un éditeur pour l'offre « UbuntuServer ».

    azure vm image list westus canonical ubuntuserver
    info:    Executing command vm image list
    warn:    The parameter --sku if specified will be ignored
    + Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
    data:    Publisher  Offer         Sku                OS     Version          Location  Urn
    data:    ---------  ------------  -----------------  -----  ---------------  --------  --------------------------------------------------------
    data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201604203  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201604203
    data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201605161  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201605161
    data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201606100  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606100
    data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201606270  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606270
    data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201607210  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201607210
    data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201608150  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201608150
    data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607220  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607220
    data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607230  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607230
    data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607240  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607240

La colonne **Urn** aura le même format que celui que vous passez à `azure vm quick-create`.

Toutefois, il est fréquent que vous ignoriez encore ce qui est disponible. Dans ce cas, vous pouvez parcourir les images en recherchant d’abord les éditeurs à l’aide de la commande `azure vm image list-publishers` et en répondant à l’invite de localisation avec l’emplacement de centre de données que vous envisagez d’utiliser pour votre groupe de ressources. Par exemple, la liste suivante répertorie tous les éditeurs d'images présents dans l'emplacement « West US » (passez l'argument location en utilisant des minuscules et en supprimant les espaces des emplacements standard).

    azure vm image list-publishers
    info:    Executing command vm image list-publishers
    Location: westus
    + Getting virtual machine and/or extension image publishers (Location: "westus")
    data:    Publisher                                       Location
    data:    ----------------------------------------------  --------
    data:    a10networks                                     westus  
    data:    aiscaler-cache-control-ddos-and-url-rewriting-  westus  
    data:    alertlogic                                      westus  
    data:    AlertLogic.Extension                            westus  


Ces listes peuvent être assez longues. C’est pourquoi l’exemple de liste ci-dessus est simplement un extrait de code. Supposons que vous ayez remarqué que « Canonical » est, en fait, un éditeur d'image présent dans l'emplacement « West US ». Vous pouvez maintenant rechercher ses offres en appelant `azure vm image list-offers` et en indiquant l’emplacement et l’éditeur aux invites, comme dans l’exemple suivant :

    azure vm image list-offers
    info:    Executing command vm image list-offers
    Location: westus
    Publisher: canonical
    + Getting virtual machine image offers (Publisher: "canonical" Location:"westus")
    data:    Publisher  Offer                      Location
    data:    ---------  -------------------------  --------
    data:    canonical  Ubuntu15.04Snappy          westus
    data:    canonical  Ubuntu15.04SnappyDocker    westus
    data:    canonical  UbunturollingSnappy        westus
    data:    canonical  UbuntuServer               westus
    data:    canonical  Ubuntu_Snappy_Core         westus
    data:    canonical  Ubuntu_Snappy_Core_Docker  westus
    info:    vm image list-offers command OK

Nous savons maintenant que dans la région « West US », « Canonical »publie l'offre **UbuntuServer** sur Azure. Mais quelles sont les références SKU ? Pour les obtenir, vous appelez `azure vm image list-skus` et répondez à l’invite en indiquant l’emplacement, l’éditeur et l’offre que vous avez détectée.

    azure vm image list-skus
    info:    Executing command vm image list-skus
    Location: westus
    Publisher: canonical
    Offer: ubuntuserver
    + Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
    data:    Publisher  Offer         sku                Location
    data:    ---------  ------------  -----------------  --------
    data:    canonical  ubuntuserver  12.04.2-LTS        westus
    data:    canonical  ubuntuserver  12.04.3-LTS        westus
    data:    canonical  ubuntuserver  12.04.4-LTS        westus
    data:    canonical  ubuntuserver  12.04.5-DAILY-LTS  westus
    data:    canonical  ubuntuserver  12.04.5-LTS        westus
    data:    canonical  ubuntuserver  12.10              westus
    data:    canonical  ubuntuserver  14.04-beta         westus
    data:    canonical  ubuntuserver  14.04.0-LTS        westus
    data:    canonical  ubuntuserver  14.04.1-LTS        westus
    data:    canonical  ubuntuserver  14.04.2-LTS        westus
    data:    canonical  ubuntuserver  14.04.3-LTS        westus
    data:    canonical  ubuntuserver  14.04.4-DAILY-LTS  westus
    data:    canonical  ubuntuserver  14.04.4-LTS        westus
    data:    canonical  ubuntuserver  14.04.5-DAILY-LTS  westus
    data:    canonical  ubuntuserver  14.04.5-LTS        westus
    data:    canonical  ubuntuserver  14.10              westus
    data:    canonical  ubuntuserver  14.10-beta         westus
    data:    canonical  ubuntuserver  14.10-DAILY        westus
    data:    canonical  ubuntuserver  15.04              westus
    data:    canonical  ubuntuserver  15.04-beta         westus
    data:    canonical  ubuntuserver  15.04-DAILY        westus
    data:    canonical  ubuntuserver  15.10              westus
    data:    canonical  ubuntuserver  15.10-alpha        westus
    data:    canonical  ubuntuserver  15.10-beta         westus
    data:    canonical  ubuntuserver  15.10-DAILY        westus
    data:    canonical  ubuntuserver  16.04-alpha        westus
    data:    canonical  ubuntuserver  16.04-beta         westus
    data:    canonical  ubuntuserver  16.04.0-DAILY-LTS  westus
    data:    canonical  ubuntuserver  16.04.0-LTS        westus
    data:    canonical  ubuntuserver  16.10-DAILY        westus
    info:    vm image list-skus command OK

Avec ces informations, vous pouvez maintenant trouver exactement l’image souhaitée en effectuant l’appel initial affiché en haut.

    azure vm image list westus canonical ubuntuserver 16.04.0-LTS
    info:    Executing command vm image list
    + Getting virtual machine images (Publisher:"canonical" Offer:"ubuntuserver" Sku: "16.04.0-LTS" Location:"westus")
    data:    Publisher  Offer         Sku          OS     Version          Location  Urn
    data:    ---------  ------------  -----------  -----  ---------------  --------  --------------------------------------------------
    data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201604203  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201604203
    data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201605161  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201605161
    data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201606100  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606100
    data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201606270  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606270
    data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201607210  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201607210
    data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201608150  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201608150
    info:    vm image list command OK

Vous pouvez maintenant choisir précisément l'image que vous voulez utiliser. Pour créer rapidement une machine virtuelle en utilisant les informations d’URN que vous venez de trouver, ou pour utiliser un modèle avec ces informations d’URN, consultez [Utilisation de l’interface de ligne de commande Azure pour Mac, Linux et Windows avec Azure Resource Management](../articles/xplat-cli-azure-resource-manager.md).

## PowerShell
> [!NOTE]
> Installez et configurez la [dernière version d’Azure PowerShell](../articles/powershell-install-configure.md). Si vous utilisez des modules Azure PowerShell antérieurs à la version 1.0, vous vous servez des commandes suivantes, mais devez d’abord `Switch-AzureMode AzureResourceManager`.
> 
> 

Lorsque vous créez une machine virtuelle avec le gestionnaire des ressources Azure, vous devez, dans certains cas, spécifier une image en combinant les propriétés d'image suivantes :

* Éditeur
* Offer
* SKU

Par exemple, ces valeurs sont nécessaires pour l’applet de commande PowerShell `Set-AzureRMVMSourceImage` ou avec un fichier de modèle de groupe de ressources dans lequel vous devez spécifier le type de machine virtuelle à créer.

Si vous devez déterminer ces valeurs, vous pouvez parcourir les images à cette fin en procédant ainsi :

1. en répertoriant les éditeurs d’images ;
2. pour un éditeur donné, en répertoriant ses offres ;
3. pour une offre donnée, en répertoriant ses références SKU.

Tout d’abord, répertoriez les serveurs de publication avec les commandes suivantes :

```powershell
$locName="<Azure location, such as West US>"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName
```

Indiquez le nom d’éditeur de publication choisi et exécutez les commandes suivantes :

```powershell
$pubName="<publisher>"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

Indiquez le nom de l’offre choisi et exécutez les commandes suivantes :

```powershell
$offerName="<offer>"
Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
```

L’affichage de la commande `Get-AzureRMVMImageSku` comprend toutes les informations dont vous avez besoin pour spécifier l’image pour une nouvelle machine virtuelle.

Vous trouverez ci-dessous un exemple complet :

```powershell
PS C:\> $locName="West US"
PS C:\> Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName

PublisherName
-------------
a10networks
aiscaler-cache-control-ddos-and-url-rewriting-
alertlogic
AlertLogic.Extension
Barracuda.Azure.ConnectivityAgent
barracudanetworks
basho
boxless
bssw
Canonical
...
```

Pour l'éditeur « MicrosoftWindowsServer » :

```powershell
PS C:\> $pubName="MicrosoftWindowsServer"
PS C:\> Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer

Offer
-----
WindowsServer
```

Pour l'offre « WindowsServer » :

```powershell
PS C:\> $offerName="WindowsServer"
PS C:\> Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus

Skus
----
2008-R2-SP1
2012-Datacenter
2012-R2-Datacenter
2016-Nano-Server-Technical-Previe
2016-Technical-Preview-with-Conta
Windows-Server-Technical-Preview
```

Dans cette liste, copiez le nom de référence SKU choisi. Vous disposez de toutes les informations pour l’applet de commande PowerShell `Set-AzureRMVMSourceImage` ou pour un modèle de groupe de ressources.

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png
[8]: ./media/markdown-template-for-new-articles/copytemplate.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[gog]: http://google.com/
[yah]: http://search.yahoo.com/
[msn]: http://search.msn.com/

<!---HONumber=AcomDC_0824_2016-->