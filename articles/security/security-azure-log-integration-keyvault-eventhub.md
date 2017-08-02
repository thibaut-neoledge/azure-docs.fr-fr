---
title: "Intégrer les journaux d’Azure Key Vault à l’aide d’Event Hubs | Microsoft Docs"
description: "Didacticiel décrivant la procédure requise pour rendre accessibles les journaux Key Vault à un système SIEM (Security Information and Event Management, système de gestion des événements et des informations de sécurité) grâce à la solution d’intégration des journaux Azure"
services: security
author: barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 
ms.service: security
ms.topic: article
ms.date: 06/29/2017
ms.author: Barclayn
ms.custom: AzLog
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: a648852fadfeb5c9a4ff61c85bbe0af856e445d4
ms.contentlocale: fr-fr
ms.lasthandoff: 07/08/2017



---

# <a name="azure-log-integration-tutorial-process-azure-key-vault-events-by-using-event-hubs"></a>Didacticiel sur l’intégration des journaux Azure : traiter les événements Azure Key Vault à l’aide d’Event Hubs

Vous pouvez utiliser la solution d’intégration des journaux Azure (AzLog) pour récupérer des événements journalisés et les rendre accessibles à votre système de gestion des événements et des informations de sécurité (SIEM, Security Information and Event Management). Ce didacticiel vous guide tout au long de la procédure qui consiste à récupérer des activités Azure Key Vault journalisées dans un Event Hub et à mettre ces activités à la disposition de votre système SIEM sous la forme de fichiers JSON. Vous pouvez ensuite configurer votre système SIEM pour qu’il procède au traitement de ces fichiers JSON.

>[!NOTE]
>La plupart des étapes de ce didacticiel impliquent la configuration de coffres de clé, de comptes de stockage et d’Event Hubs. La procédure d’intégration des journaux Azure est décrite à la fin de ce didacticiel.

Les informations fournies tout au long de la procédure vous expliquent la finalité de chaque étape. Les liens d’accès à d’autres articles offrent des détails complémentaires sur certains sujets.

Pour plus d’informations sur les services mentionnés par ce didacticiel, consultez les articles suivants : 

- [Azure Key Vault](../key-vault/key-vault-whatis.md)
- [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Intégration des journaux Azure](security-azure-log-integration-overview.md)


## <a name="initial-setup"></a>Configuration initiale

Pour exécuter la procédure décrite dans cet article, vous devez disposer des éléments suivants :

1. Un abonnement Azure et un compte dans cet abonnement avec des droits d’administrateur. Si vous ne disposez d’aucun abonnement, vous pouvez créer [un compte gratuitement](https://azure.microsoft.com/free/).
 
2. Un système doté d’un accès à Internet qui présente la configuration requise pour l’installation de l’intégration des journaux Azure. Ce système peut se trouver dans un service cloud ou être hébergé localement.

3. Solution d’[intégration des journaux Azure](https://www.microsoft.com/download/details.aspx?id=53324) installée. Pour l’installer :
   1. Utilisez la fonctionnalité Bureau à distance pour vous connecter au système mentionné à l’étape 2.
   2. Copiez le programme d’installation de l’intégration des journaux Azure sur le système. Vous pouvez [télécharger les fichiers d’installation](https://www.microsoft.com/download/details.aspx?id=53324).
   3. Démarrez le programme d’installation et acceptez les Termes du contrat de licence logiciel Microsoft.
   4. Si vous souhaitez fournir des informations de télémétrie, laissez la case cochée. Si vous préférez ne pas envoyer d’informations d’utilisation à Microsoft, décochez la case.
   
   Pour plus d’informations sur l’intégration des journaux Azure et sur son installation, consultez l’article [Intégration des journaux Azure avec Azure Diagnostics Logging et Windows Event Forwarding](security-azure-log-integration-get-started.md).

4. La dernière version de PowerShell.
 
   Si vous avez installé Windows Server 2016, vous disposez au moins de PowerShell 5.0. Si vous utilisez une autre version de Windows Server, il est possible que vous possédiez une version antérieure de PowerShell. Pour vérifier la version que vous utilisez, tapez ```get-host``` dans une fenêtre PowerShell. Si vous n’avez pas installé PowerShell 5.0, vous pouvez [le télécharger](https://www.microsoft.com/download/details.aspx?id=50395).

   Une fois que vous disposez de PowerShell 5.0 ou d’une version supérieure, vous pouvez procéder à l’installation de la dernière version :
   1. Dans une fenêtre PowerShell, tapez ```Install-Module Azure```, puis appuyez sur Entrée. Suivez la procédure d’installation. 
   2. Tapez ```Install-Module AzureRM```, puis appuyez sur Entrée. Suivez la procédure d’installation.

   Pour plus d’informations, consultez l’article [Installation et configuration d’Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.0.0).


## <a name="create-supporting-infrastructure-elements"></a>Créer les éléments d’infrastructure sous-jacents

1. Ouvrez une fenêtre PowerShell avec élévation de privilèges et accédez à **C:\Program Files\Microsoft Azure Log Integration**.
2. Importez les applets de commande AzLog en exécutant le script LoadAzLogModule.ps1. (Notez la présence du caractère « .\ » dans la commande suivante.) Tapez `.\LoadAzLogModule.ps1`, puis appuyez sur Entrée.
Le résultat suivant devrait s'afficher :</br>

   ![Liste des modules chargés](./media/security-azure-log-integration-keyvault-eventhub/loaded-modules.png)

3. Tapez `Login-AzureRmAccount`, puis appuyez sur Entrée. Dans la fenêtre de connexion, entrez les informations d’identification de l’abonnement que vous allez utiliser dans le cadre de ce didacticiel.

   >[!NOTE]
   >S’il s’agit de votre première connexion à Azure à partir de cette machine, vous verrez un message destiné à autoriser Microsoft à collecter les données d’utilisation de PowerShell. Nous vous recommandons d’autoriser cette collecte de données, car elle nous permettra d’améliorer Azure PowerShell.

4. Après vous être authentifié, vous êtes connecté et voyez apparaître les informations figurant dans la capture d’écran ci-après. Notez l’ID et le nom de l’abonnement, car vous aurez besoin de ces éléments dans la suite de cette procédure.

   ![Fenêtre PowerShell](./media/security-azure-log-integration-keyvault-eventhub/login-azurermaccount.png)
5. Créez des variables pour stocker les valeurs qui seront utilisées par la suite. Tapez toutes les lignes PowerShell ci-après et appuyez sur Entrée après chacune d’elles. Vous devrez peut-être ajuster les valeurs pour les faire correspondre à votre environnement.
    - ```$subscriptionName = ‘Visual Studio Ultimate with MSDN’``` (Votre nom d’abonnement peut être différent. Vous pouvez le voir apparaître dans la sortie de la commande précédente.)
    - ```$location = 'West US'``` (Cette variable est utilisée pour transmettre l’emplacement où les ressources doivent être créées. Vous pouvez redéfinir cette variable sur tout autre emplacement de votre choix.)
    - ```$random = Get-Random```
    - ``` $name = 'azlogtest' + $random``` (Le nom peut correspondre à une chaîne quelconque, mais doit uniquement inclure des lettres minuscules et des chiffres.)
    - ``` $storageName = $name``` (Cette variable est utilisée pour le nom du compte de stockage.)
    - ```$rgname = $name ``` (Cette variable est utilisée pour le nom du groupe de ressources.)
    - ``` $eventHubNameSpaceName = $name``` (Cette variable correspond au nom de l’espace de noms Event Hub.)
6. Spécifiez l’abonnement que vous allez utiliser :
    
    ```Select-AzureRmSubscription -SubscriptionName $subscriptionName```
7. Créez un groupe de ressources :
    
    ```$rg = New-AzureRmResourceGroup -Name $rgname -Location $location```
    
   Si vous tapez `$rg` et que vous appuyez sur Entrée à ce stade, vous verrez apparaître une sortie semblable à la capture d’écran ci-après :

   ![Sortie après la création d’un groupe de ressources](./media/security-azure-log-integration-keyvault-eventhub/create-rg.png)
8. Créez un compte de stockage qui servira à effectuer le suivi des informations d’état :
    
    ```$storage = New-AzureRmStorageAccount -ResourceGroupName $rgname -Name $storagename -Location $location -SkuName Standard_LRS```
9. Créez l’espace de noms Event Hub. Cette opération est requise pour la création d’un Event Hub.
    
    ```$eventHubNameSpace = New-AzureRmEventHubNamespace -ResourceGroupName $rgname -NamespaceName $eventHubnamespaceName -Location $location```
10. Obtenez l’ID de règle qui sera utilisé avec le fournisseur d’informations :
    
    ```$sbruleid = $eventHubNameSpace.Id +'/authorizationrules/RootManageSharedAccessKey' ```
11. Obtenez tous les emplacements Azure possibles et ajoutez les noms à une variable que vous pourrez utiliser à une étape ultérieure :
    
    1. ```$locationObjects = Get-AzureRMLocation```    
    2. ```$locations = @('global') + $locationobjects.location```
    
    Si vous tapez `$locations` et que vous appuyez sur Entrée à ce stade, vous verrez apparaître les noms d’emplacement sans les informations supplémentaires renvoyées par Get-AzureRmLocation.
12. Créez un profil de journal Azure Resource Manager : 
    
    ```Add-AzureRmLogProfile -Name $name -ServiceBusRuleId $sbruleid -Locations $locations```
    
    Pour plus d’informations sur le profil de journal Azure, consultez l’article [Présentation du journal d’activité Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

>[!NOTE]
>Il est possible que vous obteniez un message d’erreur lorsque vous essayez de créer un profil de journal. Vous pouvez alors consulter la documentation des commandes Get-AzureRmLogProfile et Remove-AzureRmLogProfile. Si vous exécutez Get-AzureRmLogProfile, vous obtenez des informations sur le profil de journal. Vous pouvez supprimer le profil de journal existant en tapant ```Remove-AzureRmLogProfile -name 'Log Profile Name' ``` et en appuyant sur Entrée.
>
>![Erreur de profil Resource Manager](./media/security-azure-log-integration-keyvault-eventhub/rm-profile-error.png)

## <a name="create-a-key-vault"></a>Création d’un coffre de clés

1. Créez le coffre de clés :

   ```$kv = New-AzureRmKeyVault -VaultName $name -ResourceGroupName $rgname -Location $location ```

2. Configurez la journalisation relative au coffre de clés :

   ```Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -ServiceBusRuleId $sbruleid -Enabled $true ```

## <a name="generate-log-activity"></a>Générer l’activité de journalisation

La génération de l’activité de journalisation nécessite l’envoi de requêtes à Key Vault. Des actions telles que la génération de clés, le stockage de secrets ou la lecture de secrets à partir de Key Vault entraînent la création d’entrées de journal.

1. Affichez les clés de stockage actuelles :
    
   ```Get-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
2. Générez une nouvelle valeur **key2** :
    
   ```New-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname -KeyName key2```
3. Réaffichez les clés et notez que l’élément **key2** présente une autre valeur :
    
   ```Get-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
4. Définissez et lisez un secret pour générer des entrées de journal supplémentaires :
    
   1. ```Set-AzureKeyVaultSecret -VaultName $name -Name TestSecret -SecretValue (ConvertTo-SecureString -String 'Hi There!' -AsPlainText -Force)```    
   2. ```(Get-AzureKeyVaultSecret -VaultName $name -Name TestSecret).SecretValueText```

   ![Secret renvoyé](./media/security-azure-log-integration-keyvault-eventhub/keyvaultsecret.png)


## <a name="configure-azure-log-integration"></a>Configurer l’intégration des journaux Azure

À présent que vous avez configuré tous les éléments nécessaires pour activer la journalisation Key Vault dans un Event Hub, vous devez configurer l’intégration des journaux Azure :

1. ```$storage = Get-AzureRmStorageAccount -ResourceGroupName $rgname -Name $storagename```
2. ```$eventHubKey = Get-AzureRmEventHubNamespaceKey -ResourceGroupName $rgname -NamespaceName $eventHubNamespace.name -AuthorizationRuleName RootManageSharedAccessKey```
3. ```$storagekeys = Get-AzureRmStorageAccountKey -ResourceGroupName $rgname -Name $storagename```
4. ``` $storagekey = $storagekeys[0].Value```

Exécutez la commande AzLog pour chaque Event Hub :

1. ```$eventhubs = Get-AzureRmEventHub -ResourceGroupName $rgname -NamespaceName $eventHubNamespaceName```
2. ```$eventhubs.Name | %{Add-AzLogEventSource -Name $sub' - '$_ -StorageAccount $storage.StorageAccountName -StorageKey $storageKey -EventHubConnectionString $eventHubKey.PrimaryConnectionString -EventHubName $_}```

Au bout d’une minute environ après l’exécution des deux dernières commandes, vous devriez voir les fichiers JSON en cours de génération. Pour vérifier ce point, surveillez le répertoire **C:\users\AzLog\EventHubJson**.

## <a name="next-steps"></a>Étapes suivantes

- [Forum aux questions sur l’intégration des journaux Azure](security-azure-log-integration-faq.md)
- [Bien démarrer avec l’intégration des journaux Azure](security-azure-log-integration-get-started.md)
- [Intégrer des journaux à partir de ressources Azure dans vos systèmes SIEM](security-azure-log-integration-overview.md)

