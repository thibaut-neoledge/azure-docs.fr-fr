---
title: "Utiliser une capture de paquets pour effectuer une surveillance proactive du réseau avec Azure Functions | Microsoft Docs"
description: "Cet article décrit la création d’une capture de paquets déclenchée par des alertes avec Azure Network Watcher"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 75e6e7c4-b3ba-4173-8815-b00d7d824e11
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 5f31b24a0d46b9d557a55b3c9d0cd7748ecb9c33
ms.lasthandoff: 03/21/2017


---
# <a name="use-packet-capture-to-do-proactive-network-monitoring-with-azure-functions"></a>Utiliser une capture de paquets pour effectuer une surveillance proactive du réseau avec Azure Functions

La fonctionnalité de capture des paquets Network Watcher crée des sessions de capture afin d’effectuer le suivi du trafic en direction et en provenance d’une machine virtuelle. Le fichier de capture peut contenir un filtre défini pour suivre uniquement le trafic que vous souhaitez analyser. Ces données sont ensuite stockées dans un objet blob de stockage ou localement sur l’ordinateur invité. Cette fonctionnalité peut être démarrée à distance à partir d’autres scénarios d’automatisation comme Azure Functions. La capture de paquets permet d’exécuter des captures proactives en fonction des anomalies définies sur le réseau. Elle permet aussi de collecter des statistiques réseau, d’obtenir des informations sur les intrusions, de déboguer des communications client-serveur, etc.

Les ressources déployées dans Azure s’exécutent 24 heures sur 24, 7 jours sur 7. Ni votre équipe ni vous ne pouvez surveiller activement l’état de toutes les ressources 24 heures sur 24, 7 jours sur 7. Que se passe-t-il si un problème se produit à 2 h ?

En utilisant Network Watcher, Alerte et Fonctions dans l’écosystème Azure, vous pouvez répondre de façon proactive aux problèmes de votre réseau avec les données et les outils permettant de résoudre le problème.

## <a name="before-you-begin"></a>Avant de commencer

Dans cet exemple, votre machine virtuelle envoie plus de segments TCP que d’habitude, et vous souhaitez être alerté. Les segments TCP sont utilisés à titre d’exemple, et vous pouvez utiliser une condition d’alerte. Une fois alerté, vous souhaitez disposer des données du niveau des paquets pour comprendre pourquoi la communication a augmenté afin de pouvoir prendre des mesures pour rétablir une communication normale sur la machine.
Ce scénario suppose que vous possédez une instance existante de Network Watcher ainsi qu’un groupe de ressources avec une machine virtuelle valide à utiliser.

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

## <a name="scenario"></a>Scénario

Pour automatiser ce processus, nous créons et connectons une alerte à notre machine virtuelle pour qu’elle se déclenche lorsque l’incident se produit et pour qu’une fonction Azure rappelle Network Watcher.

Ce scénario :

* crée une fonction Azure qui démarrera une capture de paquets ;
* crée une règle d’alerte sur une machine virtuelle ;
* configure la règle d’alerte pour appeler la fonction Azure.

## <a name="creating-an-azure-function-and-overview"></a>Création d’une fonction Azure et vue d’ensemble

La première étape consiste à créer une fonction Azure pour traiter l’alerte et créer une capture de paquets. 

La liste suivante est une vue d’ensemble du flux de travail qui a lieu.

1. Une alerte est déclenchée sur votre machine virtuelle.
1. L’alerte appelle votre fonction Azure via un webhook.
1. Votre fonction Azure traite l’alerte et démarre une session de capture de paquets Network Watcher.
1. La capture de paquets s’exécute sur la machine virtuelle et collecte le trafic. 
1. Le fichier de capture est chargé vers un compte de stockage pour la révision et le diagnostic 

Il est possible de créer une fonction Azure dans le portail en suivant l’article [Créer votre première fonction Azure](../azure-functions/functions-create-first-azure-function.md). Pour cet exemple, nous avons choisi une fonction de type HttpTrigger-PowerShell. Les personnalisations sont requises pour cet exemple et sont décrites dans les étapes suivantes :

![exemples de fonctions][functions1]

> [!NOTE]
> Le modèle PowerShell est expérimental et n’a pas de prise en charge complète.

## <a name="adding-modules"></a>Ajout de modules

Pour utiliser les applets de commande Network Watcher PowerShell, le dernier module PowerShell doit être chargé dans Function App.

1. Sur votre ordinateur local avec les derniers modules Azure PowerShell installés, exécutez la commande PowerShell suivante :

    ```powershell
    (Get-Module AzureRM.Network).Path
    ```

    Cela vous donne le chemin d’accès local de vos modules Azure PowerShell. Ces dossiers sont utilisés dans une étape ultérieure. Les modules utilisés dans ce scénario sont :

    * AzureRM.Network

    * AzureRM.Profile

    * AzureRM.Resources

    ![Dossiers PowerShell][functions5]

1. Accédez à **Paramètres Function App** > **Accéder à l’Éditeur App Service**.

    ![Fonctions kudu][functions2]

1. Cliquez avec le bouton droit sur le dossier AlertPacketCapturePowershell et créez un dossier nommé **azuremodules**. Continuez à créer des sous-dossiers pour chaque module nécessaire.

    ![Fonctions kudu][functions3]

    * AzureRM.Network

    * AzureRM.Profile

    * AzureRM.Resources

1. Cliquez avec le bouton droit sur le sous-dossier **AzureRM.Network**, puis cliquez sur **Charger les fichiers**. Accédez à l’emplacement où vos modules Azure sont installés et dans le dossier AzureRM.Network local, sélectionnez tous les fichiers et cliquez sur **Ok**.  Répétez ces étapes pour AzureRM.Profile et AzureRM.Resources.

    ![Charger des fichiers][functions6]

1. Lorsque vous avez terminé, chaque dossier doit contenir les fichiers du module PowerShell de votre ordinateur local.

    ![Fichiers PowerShell][functions7]

## <a name="authentication"></a>Authentification

Pour utiliser les applets de commande PowerShell, vous devez vous authentifier. L’authentification doit être configuré dans Function App. Pour ce faire, les variables d’environnement sont configurées et un fichier de clé chiffré doit être chargé vers Function App.

> [!note]
> Ce scénario ne fournit qu’un exemple d’implémentation de l’authentification avec Azure Functions, il existe d’autres façons de procéder.

### <a name="encrypted-credentials"></a>Informations d’identification chiffrées

Le script PowerShell suivant crée un fichier de clé appelé **PassEncryptKey.key** et fournit une version chiffrée du mot de passe fourni.  Ce mot de passe est le même que celui défini pour l’application Azure AD qui est utilisée pour l’authentification.

```powershell
#variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

Dans l’Éditeur App Service de Function App, créez un dossier nommé **keys** sous **AlertPacketCapturePowerShell** et chargez le fichier **PassEncryptKey.key** créé par l’exemple PowerShell précédent.

![Clé de fonctions][functions8]

### <a name="retrieving-values-for-environment-variables"></a>Extraction des valeurs pour les variables d’environnement

La dernière configuration requise consiste à définir les variables d’environnement nécessaires pour accéder aux valeurs pour l’authentification. Voici une liste des variables d’environnement qui sont créés.

* AzureClientID

* AzureTenant

* AzureCredPassword


#### <a name="azureclientid"></a>AzureClientID

L’ID client est l’ID d’application d’une application dans Azure Active Directory.

1. Si vous ne disposez pas d’une application à utiliser, exécutez l’exemple suivant pour créer une application.

    ```powershell
    $app = New-AzureRmADApplication -DisplayName "ExampleAutomationAccount_MF" -HomePage "https://exampleapp.com" -IdentifierUris "https://exampleapp1.com/ExampleFunctionsAccount" -Password "<same password as defined earlier>"
    New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
    Start-Sleep 15
    New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $app.ApplicationId
    ```

   > [!NOTE]
   > Le mot de passe utilisé lors de la création de l’application doit être le même que celui créé précédemment lors de l’enregistrement du fichier de clé.

1. Dans le portail Azure, accédez à **Abonnements** > Choisissez l’abonnement à utiliser > **Contrôle d’accès (IAM)**.

    ![Fonctions iam][functions9]

1. Choisissez le compte à utiliser, puis cliquez sur Propriétés. Copiez l’ID de l’application.

    ![ID de l’application de fonctions][functions10]

#### <a name="azuretenant"></a>AzureTenant

L’ID client est obtenu en exécutant l’exemple PowerShell suivant :

```powershell
(Get-AzureRmSubscription -SubscriptionName "<subscriptionName>").TenantId
```

#### <a name="azurecredpassword"></a>AzureCredPassword

La valeur de la variable d’environnement AzureCredPassword est la valeur obtenue avec l’exécution de l’exemple PowerShell suivant. Il s’agit du même exemple que celui indiqué dans la section **Informations d’identification chiffrées** précédente. La valeur requise est le résultat de la variable `$Encryptedpassword`.  Ceci est le mot de passe principal de service que nous avons chiffré à l’aide du script PowerShell.

```powershell
#variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

### <a name="storing-the-environment-variables"></a>Stockage des variables d’environnement

1. Accédez à Function App, cliquez sur **Paramètres Function App** > **Configurer les paramètres de l’application**

    ![Configurer les paramètres de l’application][functions11]

1. Ajoutez les variables d’environnement et leurs valeurs aux paramètres de l’application et cliquez sur **Enregistrer**

    ![paramètres d'application][functions12]

## <a name="processing-the-alert-and-starting-a-packet-capture-session"></a>Traitement de l’alerte et démarrage d’une session de capture de paquets

À présent, il est temps d’appeler Network Watcher à partir de la fonction Azure. Selon les besoins, l’implémentation de cette fonction est différente. Toutefois, le flux général du code est ainsi :

1. Traiter les paramètres d’entrée
2. Interroger les captures de paquets existantes, vérifier les limites et résoudre les conflits de noms
3. Créer une capture de paquets avec les paramètres appropriés
4. Sonder régulièrement la capture de paquets jusqu’à la fin
5. Informer l’utilisateur de la fin de la session de capture de paquets

L’exemple suivant est PowerShell qui peut être utilisé dans la fonction Azure. Il y a des valeurs qui doivent être remplacées pour subscriptionId, resourceGroupName et storageAccountName.

```powershell
#Import Azure PowerShell modules required to make calls to Network Watcher
Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Profile\AzureRM.Profile.psd1" -Global
Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Network\AzureRM.Network.psd1" -Global
Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Resources\AzureRM.Resources.psd1" -Global

#Process Alert Request Body
$requestBody = Get-Content $req -Raw | ConvertFrom-Json

#Storage Account Id to save captures in
$storageaccountid = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}"

#Packet Capture Vars
$packetcapturename = "PSAzureFunction"
$packetCaptureLimit = 10
$packetCaptureDuration = 10

#Credentials
$tenant = $env:AzureTenant
$pw = $env:AzureCredPassword
$clientid = $env:AzureClientId
$keypath = "D:\home\site\wwwroot\AlertPacketCapturePowerShell\keys\PassEncryptKey.key"

#Authentication
$secpassword = $pw | ConvertTo-SecureString -Key (Get-Content $keypath)
$credential = New-Object System.Management.Automation.PSCredential ($clientid, $secpassword)
Add-AzureRMAccount -ServicePrincipal -Tenant $tenant -Credential $credential #-WarningAction SilentlyContinue | out-null


#Get the VM that fired the Alert
if($requestBody.context.resourceType -eq "Microsoft.Compute/virtualMachines")
{
    Write-Output ("Subscription ID: {0}" -f $requestBody.context.subscriptionId)
    Write-Output ("Resource Group:  {0}" -f $requestBody.context.resourceGroupName)
    Write-Output ("Resource Name:  {0}" -f $requestBody.context.resourceName)
    Write-Output ("Resource Type:  {0}" -f $requestBody.context.resourceType)

    #Get the Network Watcher in the VM's Region
    $nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $requestBody.context.resourceRegion}
    $networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName

    #Get existing packetCaptures
    $packetCaptures = Get-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher

    #Remove existing packet capture created by the function if it exists
    $packetCaptures | %{if($_.Name -eq $packetCaptureName)
    { 
        Remove-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName $packetCaptureName
    }}

    #Initiate Packet Capture on the VM that fired the alert
    if ((Get-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher).Count -lt $packetCaptureLimit){
        echo "Initiating Packet Capture"
        New-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $requestBody.context.resourceId -PacketCaptureName $packetCaptureName -StorageAccountId $storageaccountid -TimeLimitInSeconds $packetCaptureDuration
        Out-File -Encoding Ascii -FilePath $res -inputObject "Packet Capture created on ${requestBody.context.resourceID}"
    }
} 
``` 

Une fois que vous avez créé votre fonction, vous devez configurer l’alerte pour appeler l’URL associée à la fonction. Pour obtenir cette valeur, cliquez sur **</> Obtenir l’URL de la fonction** 

![recherche de l’url de la fonction 1][functions13]

Copiez l’URL de la fonction pour votre Function App.

![recherche de l’url de la fonction 2][2]

Si vous avez besoin de propriétés personnalisées dans la charge utile de la demande POST du webhook, consultez l’article [Configurer un webhook sur une alerte de métrique Azure](../monitoring-and-diagnostics/insights-webhooks-alerts.md)

## <a name="configure-an-alert-on-a-vm"></a>Configurer une alerte sur une machine virtuelle

Des alertes peuvent être configurées pour informer qu’une mesure spécifique dépasse un seuil qui lui a été affecté. Dans cet exemple, l’alerte est définie sur les segments TCP envoyés, mais elle peut être déclenchée pour plusieurs autres mesures. Dans cet exemple, une alerte est configurée pour appeler un webhook afin qu’il appelle la fonction.

### <a name="create-the-alert-rule"></a>Créer la règle d’alerte

Accédez à une machine virtuelle existante et ajoutez une règle d’alerte. Une documentation plus détaillée sur la configuration des alertes se trouve dans l’article [Créer des alertes dans Azure Monitor pour les services Azure - Portail Azure](../monitoring-and-diagnostics/insights-alerts-portal.md). 

![ajouter une règle d’alerte à une machine virtuelle][1]

> [!NOTE]
> Certaines mesures ne sont pas activées par défaut. Pour en savoir plus sur l’activation de mesures supplémentaires, consultez l’article [Activation de la surveillance et des diagnostics](../monitoring-and-diagnostics/insights-how-to-use-diagnostics.md).

Pour finir, collez l’URL de l’étape précédente dans la zone de texte Webhook de votre alerte. Cliquez sur **OK** pour enregistrer la règle d’alerte.

![collage de l’url dans la règle d’alerte][3]

## <a name="downloading-and-viewing-the-capture-file"></a>Téléchargement et affichage du fichier de capture

Si vous enregistrez la capture dans un compte de stockage, le fichier de capture peut être téléchargé via le portail ou par programme. S’il est stocké localement, vous pouvez le récupérer en vous connectant à la machine virtuelle. 

Pour obtenir des instructions de téléchargement des fichiers à partir des comptes de stockage Azure, consultez [Prise en main du stockage d’objets blob Azure à l’aide de .NET](../storage/storage-dotnet-how-to-use-blobs.md). L’explorateur de stockage peut aussi être utilisé. Pour en savoir plus sur l’explorateur de stockage, cliquez sur le lien suivant : [Explorateur de stockage](http://storageexplorer.com/)

Une fois la capture téléchargée, vous pouvez l’afficher dans tout outil en mesure de lire un fichier **.cap**. Les liens de deux de ces outils sont indiqués ci-après :

[Microsoft Message Analyzer](https://technet.microsoft.com/en-us/library/jj649776.aspx)  
[WireShark](https://www.wireshark.org/)  

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment afficher vos captures de paquets en consultant l’article [Packet capture analysis with Wireshark (Analyse de la capture de paquets avec Wireshark)](network-watcher-alert-triggered-packet-capture.md).

[1]: ./media/network-watcher-alert-triggered-packet-capture/figure1.png
[2]: ./media/network-watcher-alert-triggered-packet-capture/figure2.png
[3]: ./media/network-watcher-alert-triggered-packet-capture/figure3.png
[functions1]:./media/network-watcher-alert-triggered-packet-capture/functions1.png
[functions2]:./media/network-watcher-alert-triggered-packet-capture/functions2.png
[functions3]:./media/network-watcher-alert-triggered-packet-capture/functions3.png
[functions4]:./media/network-watcher-alert-triggered-packet-capture/functions4.png
[functions5]:./media/network-watcher-alert-triggered-packet-capture/functions5.png
[functions6]:./media/network-watcher-alert-triggered-packet-capture/functions6.png
[functions7]:./media/network-watcher-alert-triggered-packet-capture/functions7.png
[functions8]:./media/network-watcher-alert-triggered-packet-capture/functions8.png
[functions9]:./media/network-watcher-alert-triggered-packet-capture/functions9.png
[functions10]:./media/network-watcher-alert-triggered-packet-capture/functions10.png
[functions11]:./media/network-watcher-alert-triggered-packet-capture/functions11.png
[functions12]:./media/network-watcher-alert-triggered-packet-capture/functions12.png
[functions13]:./media/network-watcher-alert-triggered-packet-capture/functions13.png
