<properties 
   pageTitle="Paramètres d’entrée de Runbook"
   description="Les paramètres d’entrée de Runbook augmentent la flexibilité des Runbook en vous permettant de transmettre des données à un Runbook lors de son démarrage. Cet article décrit différents cas où des paramètres d’entrée sont utilisés dans des Runbook."
   services="automation"
   documentationCenter=""
   authors="SnehaGunda"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/16/2015"
   ms.author="sngun"/>

# Paramètres d’entrée de Runbook

Les paramètres d’entrée de Runbook augmentent la flexibilité des Runbook en vous permettant de transmettre des données à un Runbook lors de son démarrage. Les paramètres permettent que les actions du Runbook soient ciblées pour des scénarios et environnements spécifiques. Cet article vous guide dans différents scénarios où des paramètres d’entrée sont utilisés dans des Runbook.

## Configuration des paramètres d’entrée

Des paramètres d’entrée peuvent être configurés dans des Runbook PowerShell, de flux de travail PowerShell et graphiques. Un Runbook peut avoir plusieurs paramètres avec différents types de données, ou aucun paramètre. Des paramètres d’entrée peuvent être obligatoires ou facultatifs, et vous pouvez affecter une valeur par défaut à des paramètres facultatifs. Les paramètres d’entrée configurés pour un Runbook peuvent avoir des valeurs affectées quand vous démarrez un Runbook via l’une des méthodes disponibles, par exemple, via l’interface utilisateur, des WebHooks, un service web ou un Runbook enfant appelé en ligne dans un autre Runbook.

## Configuration de paramètres d’entrée dans des Runbook PowerShell et de flux de travail PowerShell

Les Runbook PowerShell et [de flux de travail PowerShell](automation-first-runbook-textual.md) dans Azure Automation prennent en charge les paramètres d’entrée définis à l’aide des attributs suivants :

| **Propriété** | **Description** |
|:--- |:---|
| Type | Obligatoire. Type de données attendu pour la valeur du paramètre. Tout type .net est valide. |
| Nom | Obligatoire. Le nom du paramètre. Il doit être unique dans un Runbook, ne peut contenir que des lettres, des chiffres ou des caractères de soulignement, et doit commencer par une lettre. |
| Obligatoire | facultatif. Spécifie si une valeur doit être fournie pour le paramètre. Si vous définissez cette valeur sur **$true**, une valeur doit être fournie lors du démarrage du Runbook. Si vous définissez cette valeur sur **$false**, une valeur est facultative. |
| Valeur par défaut | facultatif. Spécifie une valeur à utiliser pour le paramètre si aucune valeur n’est transmise lors du démarrage du Runbook. Une valeur par défaut peut être définie pour tout paramètre, qui rend automatiquement le paramètre facultatif, indépendamment du paramètre Mandatory. |

Windows PowerShell prend en charge d’autres attributs de paramètres d’entrée que ceux répertoriés ici, tels que la validation, les alias et les jeux de paramètres. Toutefois, Azure Automation ne prend actuellement en charge que la liste ci-dessus.

Une définition de paramètre dans des Runbook de flux de travail PowerShell a la forme générale suivante, où plusieurs paramètres sont séparés par des virgules.

```
     Param
     (
         [Parameter (Mandatory= $true/$false)]
         [Type] Name1 = <Default value>,

         [Parameter (Mandatory= $true/$false)]
         [Type] Name2 = <Default value>
     ) 
```

>[AZURE.NOTE]Lors de la définition de paramètres, si vous ne spécifiez pas l’attribut **Mandatory**, par défaut, le paramètre est considéré comme facultatif. De même, si vous définissez une valeur par défaut pour un paramètre dans des Runbook de flux de travail PowerShell, il est traité par PowerShell comme un paramètre facultatif, indépendamment de la valeur de l’attribut **Mandatory**.

En guise d’exemple, nous allons configurer les paramètres d’entrée pour un Runbook de flux de travail PowerShell qui renvoie des détails sur des machines virtuelles, soit une machine virtuelle unique, soit toutes les machines virtuelles au sein d’un service. Ce Runbook comporte deux paramètres : le nom de la machine virtuelle et le nom du service, comme illustré dans la capture d’écran ci-dessous.

![Flux de travail PowerShell d’Automation](media/automation-runbook-input-parameters/automation_01_PowerShellWorkflow.png)

Dans cette définition de paramètre, les paramètres **$VMName** et **$ServiceName** sont des paramètres simples de type chaîne. Toutefois, les Runbook PowerShell et de flux de travail PowerShell prennent en charge tous les types, simples et complexes, tels que **object** ou **PSCredential**, pour les paramètres d’entrée.

Si votre Runbook comprend un paramètre d’entrée de type [object], pour transmettre une valeur, utilisez une table de hachage PowerShell comportant des paires (nom,valeur). Par exemple, si vous avez le paramètre suivant dans un Runbook,

     [Parameter (Mandatory = $true)]
     [object] $FullName

Vous pouvez transmettre la valeur suivante au paramètre :

    @{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}


## Configuration des paramètres d’entrée dans des Runbook graphiques

Pour configurer un Runbook graphique avec des paramètres d’entrée, nous allons créer un [Runbook graphique](automation-first-runbook-graphical.md) qui renvoie des détails sur des machines virtuelles, soit une machine virtuelle unique, soit toutes les machines virtuelles au sein d’un service. Le Runbook se compose de deux activités principales :

* [**Add-AzureAccount**](https://msdn.microsoft.com/library/dn495128.aspx) pour s’authentifier auprès d’Azure ;
* [**Get-AzureVM**](https://msdn.microsoft.com/library/azure/dn495236.aspx) pour obtenir toutes les machines virtuelles.

Vous pouvez utiliser l’activité [**Write-Output**](https://technet.microsoft.com/library/hh849921.aspx) pour renvoyer les noms de machines virtuelles. L’activité **Get-AzureVM** accepte deux paramètres : le **nom de machine virtuelle** et le **nom de compte de service**. Étant donné que ces paramètres peuvent nécessiter des valeurs différentes chaque fois que vous démarrez le Runbook, vous pouvez ajouter des paramètres d’entrée à votre Runbook. Voici les étapes pour ajouter des paramètres d’entrée :

1. Sélectionnez le Runbook graphique dans le panneau **Runbooks**, puis [modifiez](automation-graphical-authoring-intro.md)-le. 

2. Dans le panneau **Modifier**, cliquez sur **Entrée et sortie** pour ouvrir le panneau **Entrée et sortie**.

    ![Runbook graphique d’Automation](media/automation-runbook-input-parameters/automation_02_GraphicalRunbook.png)

 
3. Le panneau **Entrée et sortie** affiche la liste des paramètres d’entrée définis pour le Runbook. Dans ce panneau, vous pouvez ajouter un nouveau paramètre d’entrée ou modifier la configuration d’un paramètre d’entrée existant. Pour ajouter un nouveau paramètre pour le Runbook, cliquez sur **Ajouter une entrée** pour ouvrir le panneau « Paramètre d’entrée de Runbook » dans lequel vous pouvez configurer les paramètres suivants :

    | **Propriété** | **Description** |
    |:--- |:---|
    | Nom | Obligatoire. Le nom du paramètre. Il doit être unique dans un Runbook, ne peut contenir que des lettres, des chiffres ou des caractères de soulignement, et doit commencer par une lettre. |
    | Description | facultatif. Description de l’objectif du paramètre d’entrée. |
    | Type | facultatif. Type de données attendu pour la valeur du paramètre. Les types de paramètres pris en charge sont String, Int32, Int64, Decimal, Boolean, DateTime et Object. Si un type de données n’est pas sélectionné, le type par défaut est String. |
    | Obligatoire | facultatif. Spécifie si une valeur doit être fournie pour le paramètre. Si vous choisissez **yes**, une valeur doit être fournie lors du démarrage du Runbook. Si vous choisissez **no**, aucune valeur n’est requise lors du démarrage du Runbook, et une valeur par défaut peut être définie. |
    | Valeur par défaut | facultatif. Spécifie une valeur à utiliser pour le paramètre si aucune valeur n’est transmise lors du démarrage du Runbook. Une valeur par défaut peut être définie pour un paramètre qui n’est pas obligatoire. Vous pouvez choisir **Custom** pour définir une valeur par défaut. Cette valeur est utilisée, sauf si une autre valeur est fournie lorsque du démarrage du Runbook. Choisissez **None** si vous ne souhaitez pas fournir de valeur par défaut. |  

    ![AddNewInput](media/automation-runbook-input-parameters/automation_03_AddNewInput.png)

4. Créez deux paramètres avec les propriétés suivantes qui seront utilisées par l’activité **Get-AzureVM** :

    * **Parameter1:** Name – VMName, Type – String, Mandatory – No
	
    * **Parameter2:** Name – ServiceName, Type – String, Mandatory – No, Default Value – Custom, Custom default value – <Nom du service par défaut qui contient les machines virtuelles>

5. Une fois les paramètres ajoutés, cliquez sur **OK**. Vous pouvez maintenant les voir dans le panneau **Entrée et sortie**. Cliquez de nouveau sur **OK**, puis sur **Enregistrer** et **Publier** pour publier votre Runbook.

## Affectation de valeurs aux paramètres d’entrée dans des Runbook

Vous pouvez transmettre des valeurs aux paramètres d’entrée dans des Runbook dans les cas suivants :

### Démarrer un Runbook et affecter des paramètres

Un Runbook peut être démarré de plusieurs façons : via l’interface utilisateur du portail Azure, avec un WebHook, avec des applets de commande PowerShell, avec l’API REST et avec un Kit de développement logiciel (SDK). Nous abordons ci-dessous différentes méthodes pour démarrer un Runbook et affecter des paramètres.

* **Démarrer un Runbook publié via le portail Azure et affecter des paramètres**

Lorsque vous [démarrez le Runbook](automation-starting-a-runbook#starting-a-runbook-with-the-azure-portal.md), le panneau **Démarrer le Runbook** s’ouvre, dans lequel vous pouvez configurer des valeurs pour les paramètres que vous venez de créer.

![Démarrer à l’aide du portail](media/automation-runbook-input-parameters/automation_04_StartRunbookUsingPortal.png)

Dans l’étiquette située sous la zone de texte d’entrée, vous pouvez voir les attributs définis pour le paramètre : obligatoires ou facultatifs, le type et une valeur par défaut. Dans la bulle d’aide en regard du nom de paramètre, vous pouvez voir toutes les informations clés dont vous avez besoin pour prendre des décisions sur les valeurs d’entrée du paramètre. Ces informations indiquent si un paramètre est obligatoire ou facultatif, son type, une valeur par défaut et d’autres notes utiles.

![Bulle d’aide](media/automation-runbook-input-parameters/automation_05_HelpBaloon.png)


>[AZURE.NOTE]Les paramètres de type String prennent en charge les valeurs de chaîne **vides**. La valeur **[EmptyString]** dans la zone de texte du paramètre d’entrée a pour effet de transmettre une chaîne vide au paramètre. De même, les paramètres de type String ne prennent en charge la transmission de valeurs **Null**. Si vous ne transmettez pas de valeur au paramètre de type String, PowerShell interprète sa valeur comme null.

* **Démarrer un Runbook publié à l’aide d’applets de commande PowerShell et affecter des paramètres**

    * **Applets de commande de gestion des services Azure :** vous pouvez démarrer un Runbook Automation créé dans un groupe de ressources par défaut à l’aide de l’applet de commande [Start-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690259.aspx)

    **Exemple :**

      ``` $params = @{“VMName”=”WSVMClassic”; ”ServiceName”=”WSVMClassicSG”}

        Start-AzureAutomationRunbook -AutomationAccountName “TestAutomation” -Name “Get-AzureVMGraphical” -Parameters $params
      ```


    * **Applets de commande d’Azure Resource Manager :** vous pouvez démarrer un Runbook Automation créé dans un groupe de ressources à l’aide de l’applet de commande [Start-AzureRMAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx)

    **Exemple :**

      ``` $params = @{“VMName”=”WSVMClassic”;”ServiceName”=”WSVMClassicSG”}

        Start-AzureRMAutomationRunbook -AutomationAccountName “TestAutomationRG” -Name “Get-AzureVMGraphical” –ResourceGroupName “RG1” -Parameters $params
      ```

>[AZURE.NOTE]Lorsque vous démarrez un Runbook à l’aide d’applets de commande PowerShell, ainsi qu’avec les paramètres d’entrée que vous avez transmis, un paramètre par défaut, **MicrosoftApplicationManagementStartedBy**, est créée avec la valeur **PowerShell**. Vous pouvez afficher ce paramètre dans le panneau de détails du travail.

* **Démarrer un Runbook à l’aide du Kit de développement logiciel (SDK) et affecter des paramètres**

    * **Méthode de gestion des services Azure :** vous pouvez démarrer un Runbook en utilisant le Kit de développement logiciel (SDK) d’un langage de programmation. Voici un extrait de code C# permettant de démarrer un Runbook dans votre compte Automation. Pour le code complet, voir notre [dépôt GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ServiceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).  

    ```      
        public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
        {
            var response = AutomationClient.Jobs.Create(automationAccount, new JobCreateParameters
            {
                Properties = new JobCreateProperties 
                {
                    Runbook = new RunbookAssociationProperty
                    {
                        Name = runbookName
                    },
                        Parameters = parameters
                }
            });
            return response.Job;
        }
    ```
      
    * **Méthode d’Azure Resource Manager :** vous pouvez démarrer un Runbook en utilisant le Kit de développement logiciel (SDK) d’un langage de programmation. Voici un extrait de code C# permettant de démarrer un Runbook dans votre compte Automation. Pour le code complet, voir notre [dépôt GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ResourceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).  

    ```
        public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
        {
           var response = AutomationClient.Jobs.Create(resourceGroup, automationAccount, new JobCreateParameters
           {
               Properties = new JobCreateProperties 
               {
                   Runbook = new RunbookAssociationProperty
                   {
                       Name = runbookName
                   },
                       Parameters = parameters
               }
           });
        return response.Job;
        }
    ```

Pour lancer cette méthode, créez un dictionnaire pour stocker les paramètres de Runbook VMName et ServiceName ainsi que leurs valeurs, puis démarrez le Runbook. Voici l’extrait de code C# permettant d’appeler la méthode définie ci-dessus.

```
    IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();

    // Add parameters to the dictionary.
    RunbookParameters.Add("VMName", "WSVMClassic");
    RunbookParameters.Add("ServiceName", "WSVMClassicSG");

    //Call the StartRunbook method with parameters
    StartRunbook(“Get-AzureVMGraphical”, RunbookParameters);
```

* **Démarrer un Runbook à l’aide de l’API REST et affecter des paramètres**

Vous pouvez créer et démarrer une tâche de Runbook avec l’API REST d’Azure Automation en utilisant la méthode **PUT** avec l’URI de demande suivant.

    https://management.core.windows.net/<subscription-id>/cloudServices/<cloud-service-name>/resources/automation/~/automationAccounts/<automation-account-name>/jobs/<job-id>?api-version=2014-12-08

Dans l’URI de demande, remplacez les paramètres suivants :
 
* **subscription-id** : ID de votre abonnement Azure.  
* **cloud-service-name :** nom du service cloud auquel la demande doit être envoyée.  
* **automation-account-name :** nom de votre compte Automation hébergé dans le service cloud spécifié.  
* **job-id :** GUID de la tâche. Vous pouvez créer un GUID dans PowerShell à l’aide de applet de commande **[GUID]::NewGuid(). ToString()**.
	
Pour transmettre des paramètres à la tâche du Runbook, utilisez le corps de la demande, qui prend deux propriétés fournies au format JSON :

* **Runbook Name** : obligatoire. Nom du Runbook pour le démarrage de la tâche.  
* **Runbook Parameters** : facultatif. Dictionnaire de la liste de paramètres au format (name, value) où name doit être un nom de type String, et valeur peut être toute valeur JSON valide. 

Si vous souhaitez démarrer le Runbook **Get-AzureVMTextual** créé précédemment avec VMName et ServiceName en tant que paramètres, utilisez le format JSON suivant pour le corps de la demande.

```
        {
           "properties":{
           "runbook":{
               "name":"Get-AzureVMTextual"
           },
           "parameters":{
               "VMName":"WSVMClassic",
               "ServiceName":”WSCS1”
           }
          }
       }
```

Un code d’état HTTP 201 est retourné si la tâche est correctement créée. Pour plus d’informations sur les en-têtes et corps de réponse, voir [Création d’un travail](https://msdn.microsoft.com/library/azure/mt163849.aspx).

### Tester un Runbook et affecter des paramètres

Lorsque vous [testez](automation-testing-runbook.md) le brouillon de votre Runbook à l’aide de l’option de test, le panneau **Test** s’ouvre, dans lequel vous pouvez configurer des valeurs pour les paramètres que vous venez de créer.

![Tester et affecter des paramètres](media/automation-runbook-input-parameters/automation_06_TestAndAssignParameters.png)

### Lier une planification à un Runbook et affecter des paramètres

Vous pouvez [lier une planification](automation-scheduling-a-runbook.md) à votre Runbook afin de démarrer celui-ci à un moment spécifique. Des paramètres d’entrée sont affectés lors de la création de la planification. Le Runbook utilise ces valeurs quand la planification le démarre. Vous ne pouvez pas enregistrer la planification tant que toutes les valeurs de paramètres obligatoires n’ont pas été fournies.

![Planifier et affecter des paramètres](media/automation-runbook-input-parameters/automation_07_ScheduleAndAssignParameters.png)

### Créer un WebHook pour un Runbook et affecter des paramètres

Vous pouvez créer un [WebHook](automation-webhooks.md) pour votre Runbook, puis configurer les paramètres d’entrée de celui-ci. Vous ne pouvez pas enregistrer le WebHook tant que toutes les valeurs de paramètres obligatoires n’ont pas été fournies.

![Créer un WebHook et affecter des paramètres](media/automation-runbook-input-parameters/automation_08_CreateWebhookAndAssignParameters.png)

Lorsque vous exécutez un Runbook à l’aide d’un WebHook, un paramètre d’entrée prédéfini, **[Webhookdata](automation-webhooks.md#details-of-a-webhook)**, est envoyé avec les paramètres d’entrée que vous avez définis. Pour plus de détails, vous pouvez cliquer sur le paramètre WebhookData pour le développer.

![Paramètre WebhookData](media/automation-runbook-input-parameters/automation_09_WebhookDataParameter.png)


## Étapes suivantes

- Pour plus d’informations sur l’entrée et la sortie du Runbook, voir [Azure Automation : entrée et sortie de Runbook, et Runbook imbriqués](https://azure.microsoft.com/blog/azure-automation-runbook-input-output-and-nested-runbooks/)
- Pour plus d’informations sur les différentes façons de démarrer un Runbook, voir [Démarrage d’un Runbook](automation-starting-a-runbook.md)
- Pour modifier un Runbook textuel, voir [Modification des Runbook textuels](automation-edit-textual-runbook.md)
- Pour modifier un Runbook graphique, voir [Création graphique dans Azure Automation](automation-graphical-authoring-intro.md)

<!---HONumber=AcomDC_1223_2015-->