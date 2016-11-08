---
title: Configuration du coffre de clés avec une rotation des clés et un audit de bout en bout | Microsoft Docs
description: Utilisez cette procédure pour configurer la rotation des clés et le contrôle des journaux de coffre de clés
services: key-vault
documentationcenter: ''
author: swgriffith
manager: ''
tags: ''

ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2016
ms.author: jodehavi;stgriffi

---
# Configuration du coffre de clés avec une rotation des clés et un audit de bout en bout
## Introduction
Une fois votre Azure Key Vault créé, vous pouvez commencer à l’utiliser pour stocker vos clés et secrets. Vos applications ne doivent plus nécessairement conserver vos clés et secrets, mais les demanderont au coffre de clés en cas de besoin. Cela vous permet de mettre à jour les clés et les secrets sans affecter le comportement de votre application, vous offrant ainsi toute une multitude de possibilités de gestion des clés et secrets.

Cet article présente un exemple d’utilisation d’Azure Key Vault pour stocker un secret, dans notre exemple une clé de compte de stockage Azure à laquelle une application accède. Il vous propose également une démonstration de mise en œuvre d’une rotation planifiée de cette clé de compte de stockage. Pour finir, il vous présente, par le biais d’une démonstration, comment contrôler les journaux d’audit du coffre de clés et déclencher des alertes en cas de requêtes inattendues.

> [REMARQUE AZURE] Ce didacticiel n’est pas conçu pour expliquer en détail la configuration initiale de votre Azure Key Vault. Pour plus d’informations, consultez [Prise en main d’Azure Key Vault](key-vault-get-started.md). Ou, pour obtenir des instructions de l'interface de ligne de commande interplateforme, consultez [ce didacticiel équivalent](key-vault-manage-with-cli.md).
> 
> 

## Configuration du coffre de clés
Pour qu’une application puisse récupérer un secret dans Azure Key Vault, vous devez tout d’abord créer le secret et le télécharger dans votre coffre. Cela peut être effectué en toute simplicité via PowerShell comme indiqué ci-dessous.

Démarrez une session Azure PowerShell et connectez-vous à votre compte Azure avec la commande suivante :

```powershell
Login-AzureRmAccount
```

Dans la fenêtre contextuelle de votre navigateur, entrez votre nom d’utilisateur et votre mot de passe Azure. Azure PowerShell obtient alors tous les abonnements associés à ce compte et utilise par défaut le premier.

Si vous disposez de plusieurs abonnements, vous devrez peut-être en spécifier un en particulier, celui qui a été utilisé pour créer votre Azure Key Vault. Tapez la commande suivante pour afficher les abonnements de votre compte :

```powershell
Get-AzureRmSubscription
```

Ensuite, pour spécifier l’abonnement associé au coffre de clés que vous allez consigner, tapez :

```powershell
Set-AzureRmContext -SubscriptionId <subscriptionID> 
```

Cet article présentant le stockage d’une clé de compte de stockage sous la forme d’un secret, vous devez obtenir cette clé de compte de stockage.

```powershell
Get-AzureRmStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

Une fois votre secret récupéré, votre clé de compte de stockage dans notre exemple, vous devez le convertir en une chaîne sécurisée, puis créer un secret avec cette valeur dans votre coffre de clés.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzureKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```
Vous devez ensuite obtenir l’URI du secret que vous venez de créer. Il sera utilisé dans une étape ultérieure lorsque vous appellerez le coffre de clés pour récupérer votre secret. Exécutez la commande PowerShell suivante et notez la valeur « Id », qui correspond à l’URI de secret.

```powershell
Get-AzureKeyVaultSecret –VaultName <vaultName>
```

## Configuration de l’application
Maintenant qu’un secret est stocké, vous souhaiterez le récupérer et l’utiliser à partir du code. Quelques étapes sont nécessaires pour y parvenir, la première et la plus importante d’entre elles consistant à enregistrer votre application avec Azure Active Directory et à fournir à Azure Key Vault des informations sur votre application afin qu’il autorise les requêtes provenant de votre application.

> [REMARQUE AZURE] Votre application doit être créée sur le même client Azure Active Directory que votre coffre de clés.
> 
> 

Ouvrez tout d’abord l’onglet des applications d’Azure Active Directory

![Ouvrir des applications dans Azure AD](./media/keyvault-keyrotation/AzureAD_Header.png)

Choisissez « Ajouter » pour ajouter une nouvelle application à votre Azure AD

![Choisir Ajouter une application](./media/keyvault-keyrotation/Azure_AD_AddApp.png)

Conservez le type d’application « Application web et/ou API web » et donnez un nom à votre application.

![Nommer l’application](./media/keyvault-keyrotation/AzureAD_NewApp1.png)

Attribuez à votre application une « URL de connexion » et un « URI ID d’application ». Pour cette démonstration, il peut s’agir de n’importe quel URL/URI, vous pouvez les modifier ultérieurement si nécessaire.

![Fournir les URI requis](./media/keyvault-keyrotation/AzureAD_NewApp2.png)

Une fois l’application ajoutée à Azure AD, vous accédez à la page de l’application. À ce stade, cliquez sur l’onglet « Configurer », puis recherchez et copiez la valeur « ID client ». Notez l’ID client pour les étapes ultérieures.

Vous générez ensuite une clé pour que votre application puisse interagir avec votre Azure AD. Vous pouvez la créer dans la section « Clés » de l’onglet « Configuration ». Notez la clé générée par votre application Azure AD pour pouvoir l’utiliser dans une étape ultérieure.

![Clés d’application Azure AD](./media/keyvault-keyrotation/Azure_AD_AppKeys.png)

Avant de créer des appels entre votre application et le coffre de clés, vous devez fournir au coffre des clés des informations sur votre application et ses autorisations. La commande suivante récupère le nom du coffre et l’ID client dans votre application Azure AD et accorde à l’application un accès « Get » à votre coffre de clés.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

À ce stade, vous êtes prêt à créer vos appels d’application. Dans votre application, vous devez tout d’abord installer les packages NuGet nécessaires pour interagir avec Azure Key Vault et Azure Active Directory. À partir de la console du gestionnaire de package Visual Studio, entrez les commandes suivantes. Notez qu’au moment de la rédaction de cet article, la version du package ActiveDirectory est la version 3.10.305231913. Nous vous conseillons donc de vérifier que vous disposez de la dernière version et de mettre à jour si nécessaire.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

Dans votre code d’application, créez une classe qui contiendra la méthode d’authentification de votre Active Directory. Dans cet exemple, la classe est appelée « Utils ». Vous devez ensuite ajouter l’instruction « using » suivante.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Ajoutez ensuite la méthode suivante pour récupérer le jeton JWT dans Azure AD. Pour faciliter la maintenance, vous souhaiterez peut-être déplacer les valeurs de chaîne codées en dur dans votre configuration web ou d’application.

```csharp
public async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);

    ClientCredential clientCred = new ClientCredential("<AzureADApplicationClientID>","<AzureADApplicationClientKey>");

    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)

    throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

Enfin, vous pouvez ajouter le code nécessaire pour appeler le coffre de clés et récupérer votre valeur secrète. Vous devez tout d’abord ajouter l’instruction « using » suivante.

```csharp
using Microsoft.Azure.KeyVault;
```

Vous ajoutez alors les appels de méthode pour appeler le coffre de clés et récupérer votre secret. Dans cette méthode, vous fournissez l’URI de secret que vous avez enregistré dans une étape précédente. Notez l’utilisation de la méthode GetToken à partir de la classe Utils créée ci-dessus.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Lorsque vous exécutez votre application, vous devez désormais vous authentifier auprès d’Azure Active Directory et récupérer votre valeur secrète dans votre Azure Key Vault.

## Rotation des clés à l’aide d’Azure Automation
Diverses options de mise en œuvre d’une stratégie de rotation sont possibles pour les valeurs que vous stockez sous forme de secrets Azure Key Vault. La rotation des secrets peut être manuelle, elle peut également être effectuée par programmation à l’aide d’appels d’API ou à l’aide d’un script d’automatisation. Dans cet article, nous utiliserons Azure PowerShell et Azure Automation pour modifier une clé d’accès de compte de stockage Azure, puis nous mettrons à jour un secret de coffre de clés avec cette nouvelle clé.

Pour qu’Azure Automation puisse définir des valeurs secrètes dans votre coffre de clés, vous devez obtenir l’ID client pour la connexion nommée « AzureRunAsConnection » créée pendant la définition de votre instance Azure Automation. Vous pouvez obtenir cet ID en choisissant « Éléments multimédias » dans votre instance Azure Automation. Sélectionnez ensuite « Connexions », puis le principal du service « AzureRunAsConnection ». Vous noterez alors l’ID d’application.

![ID client Azure Automation](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

Toujours dans la fenêtre des éléments multimédias, vous souhaiterez peut-être également sélectionner « Modules ». Vous sélectionnez ici « Galerie », puis recherchez et importez les versions à jour de chacun des modules suivants.

    Azure
    Azure.Storage    
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [REMARQUE AZURE] Au moment de la rédaction de cet article, seuls les modules indiqués ci-dessus devaient être mis à jour pour le script partagé ci-dessous. Si vous constatez un échec de votre tâche d’automatisation, vérifiez que vous disposez de tous les modules nécessaires et que leurs dépendances ont été importées.
> 
> 

Une fois que vous avez récupéré l’ID d’application pour votre connexion Azure Automation, vous devez indiquer à votre Azure Key Vault que cette application est autorisée à accéder afin de mettre à jour les secrets dans votre coffre. Cela peut être effectué avec la commande PowerShell suivante.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Vous sélectionnez ensuite la ressource « Runbooks » sous votre instance Azure Automation, puis « Ajouter un Runbook ». Sélectionnez « Création rapide ». Donnez un nom à votre runbook et sélectionnez « PowerShell » comme type de runbook. Vous pouvez éventuellement ajouter une description. Pour finir, cliquez sur « Créer ».

![Créer un Runbook](./media/keyvault-keyrotation/Create_Runbook.png)

Dans le volet de l’éditeur de votre nouveau runbook, collez le script PowerShell suivant.

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection "
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Add-AzureRmAccount `
        -ServicePrincipal `
        -TenantId $servicePrincipalConnection.TenantId `
        -ApplicationId $servicePrincipalConnection.ApplicationId `
        -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    "Login complete."
}
catch {
    if (!$servicePrincipalConnection)
    {
        $ErrorMessage = "Connection $connectionName not found."
        throw $ErrorMessage
    } else{
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

#Optionally you may set the following as parameters
$StorageAccountName = <storageAccountName>
$RGName = <storageAccountResourceGroupName>
$VaultName = <keyVaultName>
$SecretName = <keyVaultSecretName>

#Key name. For example key1 or key2 for the storage account
New-AzureRmStorageAccountKey -ResourceGroupName $RGName -StorageAccountName $StorageAccountName -KeyName "key2" -Verbose
$SAKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName

$secretvalue = ConvertTo-SecureString $SAKeys[1].Value -AsPlainText -Force

$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

Dans le volet de l’éditeur, vous pouvez choisir le volet « Test » pour tester votre script. Une fois que le script s’exécute sans erreur, vous pouvez sélectionner l’option « Publier », puis appliquer une planification du runbook dans le volet de configuration du runbook.

## Pipeline d’audit du coffre de clés
Lorsque vous configurez un Azure Key Vault, vous pouvez activer la fonction d’audit afin de collecter des journaux de demandes d’accès au coffre de clés. Ces journaux sont stockés dans un compte de stockage Azure spécifié et peuvent ensuite être récupérés, contrôlés et analysés. Le scénario ci-dessous vous guide dans l’utilisation d’Azure Functions, d’Azure Logic Apps et des journaux d’audit du coffre de clés afin de créer un pipeline pour envoyer un message électronique lorsque des secrets du coffre sont récupérés par une application qui ne correspond pas à l’ID de l’application web.

Vous devez tout d’abord activer la journalisation sur votre coffre de clés. Cela peut être effectué à l’aide des commandes PowerShell suivantes (tous les détails peuvent être consultés [ici](key-vault-logging.md)) :

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzureRmKeyVault -VaultName '<vaultName>' 
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent
```

Une fois cette option est activée, les journaux d’audit commencent la collecte dans le compte de stockage spécifié. Ces journaux contiendront des événements sur la méthode et l’heure d’accès de vos coffres de clés, et par qui.

> [REMARQUE AZURE] Vous pouvez accéder aux informations de journalisation au plus 10 minutes après l’opération sur le coffre de clés. Dans la plupart des cas, ce sera plus rapide.
> 
> 

L’étape suivante consiste à [créer une file d’attente Azure Service Bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). C’est dans celle-ci que les journaux d’audit de coffre de clés sont envoyés. Une fois placée dans la file d’attente, l’application logique récupère et agit sur ces journaux. La création d’un Service Bus est relativement simple et en voici les étapes principales :

1. Créez un espace de noms Service Bus (si vous avez déjà un et que vous souhaitez l’utiliser ici, passez à l’étape 2).
2. Recherchez le Service Bus dans le portail, puis sélectionnez l’espace de noms dans lequel vous souhaitez créer la file d’attente.
3. Sélectionnez Nouveau et choisissez Service Bus -> File d’attente, puis entrez les informations demandées.
4. Récupérez les informations de connexion Service Bus en choisissant l’espace de noms et en cliquant sur *Informations de connexion*. Vous aurez besoin de ces informations dans la partie suivante.

Vous allez ensuite [créer une fonction Azure](../azure-functions/functions-create-first-azure-function.md) pour interroger les journaux de coffre de clés dans le compte de stockage et récupérer les nouveaux événements. Il s’agit d’une fonction déclenchée de manière planifiée.

Créez une fonction Azure (choisissez Nouveau -> Function App dans le portail). Lors de la création, vous pouvez utiliser un plan d’hébergement existant ou en créer un nouveau. Vous pouvez également opter pour un hébergement dynamique. Vous trouverez plus d’informations sur les options d’hébergement de fonction [ici](../azure-functions/functions-scale.md).

Lorsque la fonction Azure est créée, accédez à celle-ci et choisissez une fonction de minuteur et C#, puis cliquez sur **Créer** dans l’écran d’accueil.

![Panneau d’accueil d’Azure Functions](./media/keyvault-keyrotation/Azure_Functions_Start.png)

Dans l’onglet *Développement*, remplacez le code run.csx par le code suivant :

```csharp
#r "Newtonsoft.Json"

using System;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.ServiceBus.Messaging; 
using System.Text;

public static void Run(TimerInfo myTimer, TextReader inputBlob, TextWriter outputBlob, TraceWriter log) 
{ 
    log.Info("Starting");

    CloudStorageAccount sourceStorageAccount = new CloudStorageAccount(new StorageCredentials("<STORAGE_ACCOUNT_NAME>", "<STORAGE_ACCOUNT_KEY>"), true);

    CloudBlobClient sourceCloudBlobClient = sourceStorageAccount.CreateCloudBlobClient();

    var connectionString = "<SERVICE_BUS_CONNECTION_STRING>";
    var queueName = "<SERVICE_BUS_QUEUE_NAME>";

    var sbClient = QueueClient.CreateFromConnectionString(connectionString, queueName);

    DateTime dtPrev = DateTime.UtcNow;
    if(inputBlob != null)
    {
        var txt = inputBlob.ReadToEnd();

        if(!string.IsNullOrEmpty(txt))
        {
            dtPrev = DateTime.Parse(txt);
            log.Verbose($"SyncPoint: {dtPrev.ToString("O")}");
        }
        else
        {
            dtPrev = DateTime.UtcNow;
            log.Verbose($"Sync point file didnt have a date. Setting to now.");
        }
    }

    var now = DateTime.UtcNow;

    string blobPrefix = "insights-logs-auditevent/resourceId=/SUBSCRIPTIONS/<SUBSCRIPTION_ID>/RESOURCEGROUPS/<RESOURCE_GROUP_NAME>/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/<KEY_VAULT_NAME>/y=" + now.Year +"/m="+now.Month.ToString("D2")+"/d="+ (now.Day).ToString("D2")+"/h="+(now.Hour).ToString("D2")+"/m=00/";

    log.Info($"Scanning:  {blobPrefix}");

    IEnumerable<IListBlobItem> blobs = sourceCloudBlobClient.ListBlobs(blobPrefix, true);

    log.Info($"found {blobs.Count()} blobs");

    foreach(var item in blobs)
    {
        if (item is CloudBlockBlob)
        {
            CloudBlockBlob blockBlob = (CloudBlockBlob)item;

            log.Info($"Syncing: {item.Uri}");

            string sharedAccessUri = GetContainerSasUri(blockBlob);

            CloudBlockBlob sourceBlob = new CloudBlockBlob(new Uri(sharedAccessUri));

            string text;
            using (var memoryStream = new MemoryStream())
            {
                sourceBlob.DownloadToStream(memoryStream);
                text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
            }

            dynamic dynJson = JsonConvert.DeserializeObject(text);

            //required to order by time as they may not be in the file
            var results = ((IEnumerable<dynamic>) dynJson.records).OrderBy(p => p.time);

            foreach (var jsonItem in results)
            {
                DateTime dt = Convert.ToDateTime(jsonItem.time);

                if(dt>dtPrev){
                    log.Info($"{jsonItem.ToString()}");

                    var payloadStream = new MemoryStream(Encoding.UTF8.GetBytes(jsonItem.ToString()));
                    //When sending to ServiceBus, use the payloadStream and set keeporiginal to true
                    var message = new BrokeredMessage(payloadStream, true);
                    sbClient.Send(message);
                    dtPrev = dt;
                }
            }
        }
    }
    outputBlob.Write(dtPrev.ToString("o"));
}

static string GetContainerSasUri(CloudBlockBlob blob) 
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```
> [REMARQUE AZURE] Veillez à remplacer les variables dans le code ci-dessus, qui pointent vers votre compte de stockage dans lequel les journaux de coffre de clés sont écrits, par le Service Bus que vous avez créé et par le chemin d’accès spécifique aux journaux de stockage du coffre de clés.
> 
> 

La fonction récupère le dernier fichier journal du compte de stockage dans lequel les journaux de coffre de clés sont écrits, extrait les événements les plus récents dans ce fichier et les place dans une file d’attente Service Bus. Étant donné qu’un même fichier peut comporter plusieurs événements, sur une heure par exemple, nous créons ensuite un fichier *sync.txt* que la fonction examine également pour déterminer l’horodatage du dernier événement récupéré. Cela permet de s’assurer que le même événement n’est pas envoyé plusieurs fois. Ce fichier *sync.txt* contient simplement l’horodatage du dernier événement rencontré. Les journaux, une fois chargés, doivent être triés en fonction de l’horodatage pour s’assurer qu’ils sont classés correctement.

Pour cette fonction, nous faisons référence à deux bibliothèques supplémentaires qui ne sont pas disponibles dans Azure Functions. Pour pouvoir les inclure, Azure Functions doit le faire à l’aide de nuget. Choisissez l’option *Afficher les fichiers*

![Option Afficher les fichiers](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

et ajoutez un nouveau fichier appelé *project.json* avec le contenu suivant :

```json
    {
      "frameworks": {
        "net46":{
          "dependencies": {
                "WindowsAzure.Storage": "7.0.0",
                "WindowsAzure.ServiceBus":"3.2.2"
          }
        }
       }
    }
```
Cliquez sur *Enregistrer*. Azure Functions télécharge alors les fichiers binaires requis.

Basculez vers l’onglet **Intégration** et donnez un nom explicite au paramètre du minuteur à utiliser dans la fonction. Dans le code ci-dessus, le minuteur est appelé *myTimer*. Spécifiez une [expression CRON](../app-service-web/web-sites-create-web-jobs.md#CreateScheduledCRON) comme suit : 0 ***** pour le minuteur qui activera l’exécution de la fonction une fois par minute.

Dans ce même onglet **Intégration**, ajoutez une entrée qui sera de type *Azure Blob Storage*. Elle pointera vers le fichier *sync.txt* contenant l’horodatage du dernier événement examiné par la fonction. Elle sera disponible dans la fonction en tant que nom de paramètre. Dans le code ci-dessus, Azure Blob Storage attend le nom de paramètre *inputBlob*. Choisissez le compte de stockage dans lequel se trouve le fichier *sync.txt* (il peut s’agir du même compte de stockage ou d’un autre) puis, dans le champ du chemin d’accès, indiquez le chemin d’accès à l’emplacement du fichier sous la forme {container-name}/path/to/sync.txt.

Ajoutez une sortie qui sera de type *Azure Blob Storage*. Celle-ci pointera également vers le fichier *sync.txt* que vous venez de définir dans l’entrée. Elle sera utilisée par la fonction pour écrire l’horodatage du dernier événement examiné. Dans le code ci-dessus, le paramètre est appelé *outputBlob*.

À ce stade, la fonction est prête. N’oubliez pas de revenir à l’onglet **Développement** et de cliquer sur *Enregistrer* pour enregistrer le code. Dans la fenêtre de résultat, recherchez d’éventuelles erreurs de compilation et corrigez-les. S’il est compilé, le code doit maintenant s’exécuter, contrôle les journaux du coffre de clés chaque minute, et place les nouveaux événements dans la file d’attente Service Bus définie. Vous devez voir des informations de journalisation dans la fenêtre du journal chaque fois que la fonction est déclenchée.

### Application logique Azure
Nous devons ensuite créer une application logique Azure qui sélectionne les événements que la fonction place dans la file d’attente Service Bus, analyse le contenu et envoie un message électronique lorsqu’une condition est remplie.

[Créez une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md) en accédant à Nouveau -> Application logique.

Une fois l’application logique créée, accédez à celle-ci et choisissez *Modifier*. Dans l’éditeur de l’application logique, choisissez l’API gérée de la *File d’attente Service Bus* et entrez vos informations d’identification Service Bus pour le connecter à la file d’attente.

![Service Bus d’application logique Azure](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Choisissez ensuite *Ajouter une condition*. Dans la condition, basculez vers l’*éditeur avancé* et entrez les informations suivantes, en remplaçant l’APP\_ID par l’APP\_ID réel de votre application web :

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Cette expression retourne essentiellement la valeur **false** si la propriété **appid** de l’événement entrant (à savoir le corps du message de Service Bus) ne correspond pas à l’**appid** de l’application.

Créez maintenant une action sous l’option *Si non, ne rien faire...*.

![Choisir une action d’application logique Azure](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

Pour l’action, choisissez *Office 365 - Envoyer un message électronique*. Renseignez les champs pour créer un message électronique à envoyer lorsque la condition définie retourne false. Si vous n’avez pas Office 365, vous pouvez rechercher des alternatives pour parvenir au même résultat.

À ce stade, vous disposez d’un pipeline de bout en bout qui, une fois par minute, recherche les nouveaux journaux d’audit de coffre de clés. Lorsqu’un nouveau journal est trouvé, il le place dans une file d’attente Service Bus. L’application logique est déclenchée dès qu’un nouveau message arrive dans la file d’attente et, si l’appid dans l’événement ne correspond à l’ID de l’application appelante, elle envoie un message électronique.

<!---HONumber=AcomDC_0928_2016-->