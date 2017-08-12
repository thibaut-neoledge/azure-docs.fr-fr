---
title: "Ajouter des Runbooks Azure Automation à des plans de récupération dans le portail Classic | Microsoft Docs"
description: "Cet article explique comment Microsoft Azure Site Recovery vous permet désormais d’étendre des plans de récupération à l’aide de Microsoft Azure Automation, afin d’effectuer des tâches complexes lors de la récupération vers Microsoft Azure"
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: f24eaa62-9dea-4fce-92e1-a72513ca0289
ms.service: site-recovery
ms.devlang: powershell
ms.tgt_pltfrm: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 08/11/2017
ms.author: ruturajd
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: c91a1210e84d215449b98aae14bda490aa2cb156
ms.contentlocale: fr-fr
ms.lasthandoff: 04/27/2017

---

# <a name="add-azure-automation-runbooks-to-recovery-plans-in-the-classic-portal"></a>Ajouter des Runbooks Azure Automation à des plans de récupération dans le portail Classic
Ce didacticiel explique comment Microsoft Azure Site Recovery s’intègre dans Microsoft Azure Automation pour permettre l’extensibilité des plans de récupération. Les plans de récupération peuvent orchestrer la récupération des machines virtuelles protégées via Microsoft Azure Site Recovery, pour la réplication vers un cloud secondaire et la réplication vers Microsoft Azure. Ils garantissent que le récupération est **précise et cohérente**, **répétable** et **automatisée**. Si vous effectuez le basculement de vos machines virtuelles vers Microsoft Azure, l’intégration avec Microsoft Azure Automation étend les plans de récupération et vous offre la possibilité d’exécuter des Runbooks, ce qui vous donne accès à des tâches d’automatisation très puissantes.

Si vous n’avez pas encore entendu parler de Microsoft Azure Automation, connectez-vous [ici](https://azure.microsoft.com/services/automation/) et téléchargez [ici](https://azure.microsoft.com/documentation/scripts/) des exemples de scripts. Lisez plus d’informations sur [Microsoft Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) et découvrez [ici](https://azure.microsoft.com/blog/?p=166264) comment orchestrer la récupération vers Microsoft Azure à l’aide de plans de récupération.

Dans ce bref didacticiel, nous allons voir comment intégrer des Runbooks Microsoft Azure dans des plans de récupération. Nous allons automatiser des tâches simples, qui nécessitaient auparavant une intervention manuelle, et voir comment convertir une opération de récupération en plusieurs étapes en une seule action, impliquant un seul clic. Nous verrons également comment dépanner un script simple en cas de problème.

## <a name="protect-the-application-to-azure"></a>Protéger l’application dans Microsoft Azure
Commençons par une application simple, constituée de deux machines virtuelles. Voici l’application HRweb de Fabrikam. « Fabrikam-HRweb-frontend » et « Fabrikam-Hrweb-backend » sont deux machines virtuelles protégées sur Microsoft Azure, via Microsoft Azure Site Recovery. Pour protéger les machines virtuelles via Microsoft Azure Site Recovery, procédez comme suit :

1. Activez la protection de vos machines virtuelles.
2. Assurez-vous que les machines virtuelles ont terminé la réplication initiale et que leur réplication est en cours.
3. Attendez que la réplication initiale soit terminée et que l’état de la réplication soit Protégé.

## ![](media/site-recovery-runbook-automation/01.png)
Dans ce didacticiel, nous allons créer un plan de récupération pour l’application Fabrikam HRweb, afin de la faire basculer vers Microsoft Azure. Ensuite, nous l’intégrerons dans un Runbook destiné à créer un point de terminaison sur la machine virtuelle Microsoft, afin de fournir des pages web au niveau du port 80.

Commençons par créer un plan de récupération pour notre application.

## <a name="create-the-recovery-plan"></a>Créer un plan de récupération
Pour récupérer l’application dans Microsoft Azure, vous devez créer un plan de récupération.
Au moyen de ce plan, nous pouvons spécifier l’ordre de récupération des machines virtuelles. La machine virtuelle placée dans le groupe 1 récupère suite à l’erreur et démarre en premier. Cela fait, celle du groupe 2 prend la relève.

Créez un plan de récupération ressemblant à ce qui suit.

![](media/site-recovery-runbook-automation/12.png)

Pour en savoir plus sur les plans de récupération, lisez la documentation [ici](https://msdn.microsoft.com/library/azure/dn788799.aspx "ici").

Ensuite, nous allons créer les artefacts nécessaires dans Microsoft  Azure Automation.

## <a name="create-the-automation-account-and-its-assets"></a>Créer le compte Automatisation et ses ressources
Vous avez besoin d’un compte Microsoft Azure Automation pour créer des Runbooks. Si vous ne disposez pas déjà d’un compte, accédez à l’onglet Azure Automation indiqué par l’élément ![](media/site-recovery-runbook-automation/02.png)et créez un compte.

1. Donnez à ce compte un nom permettant de l’identifier.
2. Spécifiez la région géographique dans laquelle placer le compte.

Il est recommandé de placer le compte dans la même région que le coffre ASR.

![](media/site-recovery-runbook-automation/03.png)

Ensuite, créez les ressources ci-après dans le compte.

### <a name="add-a-subscription-name-as-asset"></a>Ajouter un nom d’abonnement en tant que ressource
1. Ajoutez un nouveau paramètre (![](media/site-recovery-runbook-automation/04.png)) dans la zone des ressources Microsoft Azure Automatisation et choisissez l’option ![](media/site-recovery-runbook-automation/05.png).
2. Sélectionnez le type de variable **String**
3. Spécifiez le nom de variable **AzureSubscriptionName**

   ![](media/site-recovery-runbook-automation/06.png)
4. Comme valeur de la variable, spécifiez votre nom d’abonnement Azure réel.

   ![](media/site-recovery-runbook-automation/07_1.png)

Vous pouvez identifier le nom de votre abonnement à partir de la page des paramètres de votre compte, sur le Portail Azure.

### <a name="add-an-azure-login-credential-as-asset"></a>Ajouter des informations d’identification de connexion Microsoft Azure en tant que ressources
Azure Automation utilise Azure PowerShell pour se connecter à l’abonnement et agit sur les artefacts à ce niveau. Pour cela, vous devez vous authentifier à l’aide de votre compte Microsoft ou d’un compte professionnel ou scolaire.
Vous pouvez stocker les informations d’identification de compte dans une ressource, afin qu’elles soient utilisées par le Runbook en toute sécurité.

1. Ajoutez un nouveau paramètre (![](media/site-recovery-runbook-automation/04.png)) dans la zone des ressources Microsoft Azure Automatisation et choisissez l’option ![](media/site-recovery-runbook-automation/09.png).
2. Sélectionnez le type d’information d’identification suivant : **Information d’identification Windows PowerShell**
3. Spécifiez le nom ci-après : **AzureCredential**

   ![](media/site-recovery-runbook-automation/10.png)
4. Indiquez le nom d’utilisateur et le mot de passe avec lesquels vous connecter.

Désormais, ces deux paramètres sont disponibles dans vos ressources.

![](media/site-recovery-runbook-automation/11.png)

Pour en savoir plus sur la connexion à votre abonnement avec PowerShell, cliquez [ici](/powershell/azure/overview).

Ensuite, vous allez créer un Runbook dans Microsoft Azure Automation, capable d’ajouter un point de terminaison pour la machine virtuelle frontale après le basculement.

## <a name="azure-automation-context"></a>Contexte Microsoft Automation
ASR passe une variable de contexte au Runbook, afin de vous aider à écrire des scripts déterministes. On pourrait arguer que les noms du service cloud et de la machine virtuelle sont assez prévisibles. Or, ce n’est pas toujours le cas, à cause de certains scénarios (comme celui selon lequel le nom de la machine virtuelle a pu être modifié suite à la présence de caractères non pris en charge par Microsoft Azure). Par conséquent, ces informations sont passées au plan de récupération ASR en tant qu’éléments du *contexte*.

Voici à quoi ressemble la variable de contexte.

        {"RecoveryPlanName":"hrweb-recovery",

        "FailoverType":"Test",

        "FailoverDirection":"PrimaryToSecondary",

        "GroupId":"1",

        "VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":

                {"CloudServiceName":"pod02hrweb-Chicago-test",

                "RoleName":"Fabrikam-Hrweb-frontend-test"}

                }

        }


Le tableau ci-dessous contient le nom et la description de chaque variable dans le contexte.

| **Nom de la variable** | **Description** |
| --- | --- |
| RecoveryPlanName |Nom du plan en cours d'exécution. Vous permet d'entreprendre une action basée sur le nom à l'aide du même script |
| FailoverType |Indique si le basculement est un test, planifié ou non planifié. |
| FailoverDirection |Indique si la récupération est principale ou secondaire |
| GroupID |Identifie le numéro de groupe dans le plan de récupération lorsque le plan est en cours d'exécution. |
| VmMap |Tableau de toutes les machines virtuelles du groupe. |
| Clé VMMap |Clé unique (GUID) pour chaque machine virtuelle. Ce GUID est identique à l’ID VMM de la machine virtuelle, le cas échéant. |
| RoleName |Nom de la machine virtuelle Azure qui est en cours de récupération |
| CloudServiceName |Nom Azure Cloud Service sous lequel la machine virtuelle est créée. |

Pour identifier la valeur du paramètre « VmMap Key » dans le contexte, vous pouvez également accéder à la page des propriétés de la machine virtuelle dans ASR et examiner la propriété GUID VM.

![](media/site-recovery-runbook-automation/13.png)

## <a name="author-an-automation-runbook"></a>Créer un Runbook Automation
À présent, créez le Runbook pour ouvrir le port 80 sur la machine virtuelle frontale.

1. Créez un Runbook dans le compte Microsoft Azure Automation, en lui donnant le nom **OpenPort80**

   ![](media/site-recovery-runbook-automation/14.png)
2. Accédez à la vue Auteur du Runbook et optez pour le mode brouillon.
3. Commencez par spécifier la variable à utiliser en tant que contexte du plan de récupération.

   ```
       param (
           [Object]$RecoveryPlanContext
       )

   ```
4. Ensuite, connectez-vous à l’abonnement avec le nom d’abonnement et les informations d’identification adéquats.

   ```
       $Cred = Get-AutomationPSCredential -Name 'AzureCredential'

       # Connect to Azure
       $AzureAccount = Add-AzureAccount -Credential $Cred
       $AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
       Select-AzureSubscription -SubscriptionName $AzureSubscriptionName
   ```

   Remarque : vous pouvez utiliser ici les ressources Microsoft Azure intitulées **AzureCredential** et **AzureSubscriptionName**.
5. À présent, indiquez les informations relatives au point de terminaison et au GUID de la machine virtuelle pour laquelle vous souhaitez exposer le point de terminaison. Dans ce cas, la machine virtuelle frontale.

   ```
       # Specify the parameters to be used by the script
       $AEProtocol = "TCP"
       $AELocalPort = 80
       $AEPublicPort = 80
       $AEName = "Port 80 for HTTP"
       $VMGUID = "7a1069c6-c1d6-49c5-8c5d-33bfce8dd183"
   ```

   Cela permet de spécifier le protocole du point de terminaison Microsoft Azure, le port local sur la machine virtuelle et le port public mappé qui lui est associé. Ces variables sont les paramètres requis par les commandes Microsoft Azure qui ajoutent des points de terminaison aux machines virtuelles. La valeur VMGUID conserve le GUID de la machine virtuelle sur laquelle vous devez agir.
6. Le script va maintenant extraire le contexte de la valeur VMGUID donnée et créer un point de terminaison sur la machine virtuelle qu’il référence.

   ```
       #Read the VM GUID from the context
       $VM = $RecoveryPlanContext.VmMap.$VMGUID

       if ($VM -ne $null)
       {
           # Invoke pipeline commands within an InlineScript

           $EndpointStatus = InlineScript {
               # Invoke the necessary pipeline commands to add a Azure Endpoint to a specified Virtual Machine
               # Commands include: Get-AzureVM | Add-AzureEndpoint | Update-AzureVM (including parameters)

               $Status = Get-AzureVM -ServiceName $Using:VM.CloudServiceName -Name $Using:VM.RoleName | `
                   Add-AzureEndpoint -Name $Using:AEName -Protocol $Using:AEProtocol -PublicPort $Using:AEPublicPort -LocalPort $Using:AELocalPort | `
                   Update-AzureVM
               Write-Output $Status
           }
       }
   ```
7. Une fois cette opération terminée, appuyez sur l’option Publier ( ![](media/site-recovery-runbook-automation/20.png) ) pour autoriser la mise à disponibilité de votre script pour l’exécution.

Le script complet est indiqué ci-dessous, à titre de référence.

```
  workflow OpenPort80
  {
    param (
        [Object]$RecoveryPlanContext
    )

    $Cred = Get-AutomationPSCredential -Name 'AzureCredential'

    # Connect to Azure
    $AzureAccount = Add-AzureAccount -Credential $Cred
    $AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
    Select-AzureSubscription -SubscriptionName $AzureSubscriptionName

    # Specify the parameters to be used by the script
    $AEProtocol = "TCP"
    $AELocalPort = 80
    $AEPublicPort = 80
    $AEName = "Port 80 for HTTP"
    $VMGUID = "7a1069c6-c1d6-49c5-8c5d-33bfce8dd183"

    #Read the VM GUID from the context
    $VM = $RecoveryPlanContext.VmMap.$VMGUID

    if ($VM -ne $null)
    {
        # Invoke pipeline commands within an InlineScript

        $EndpointStatus = InlineScript {
            # Invoke the necessary pipeline commands to add an Azure Endpoint to a specified Virtual Machine
            # This set of commands includes: Get-AzureVM | Add-AzureEndpoint | Update-AzureVM (including necessary parameters)

            $Status = Get-AzureVM -ServiceName $Using:VM.CloudServiceName -Name $Using:VM.RoleName | `
                Add-AzureEndpoint -Name $Using:AEName -Protocol $Using:AEProtocol -PublicPort $Using:AEPublicPort -LocalPort $Using:AELocalPort | `
                Update-AzureVM
            Write-Output $Status
        }
    }
  }
```

## <a name="add-the-script-to-the-recovery-plan"></a>Ajouter le script au plan de récupération
Une fois que le script est prêt, vous pouvez l’ajouter au plan de récupération créé précédemment.

1. Dans le plan de récupération que vous avez créé, optez pour l’ajout d’un script après le groupe 2. ![](media/site-recovery-runbook-automation/15.png)
2. Spécifiez un nom de script. Il s’agit simplement d’un nom convivial pour ce script, qui doit s’afficher dans le plan de récupération.
3. Lors du basculement vers le script Microsoft Azure, sélectionnez le nom de compte Microsoft Azure Automation.
4. Dans la liste des Runbooks Microsoft Azure, sélectionnez le Runbook que vous avez créé.

![](media/site-recovery-runbook-automation/16.png)

## <a name="primary-side-scripts"></a>Scripts côté principal
Lorsque vous exécutez un basculement vers Azure, vous pouvez également choisir d'exécuter des scripts côté principal. Ces scripts seront exécutés sur le serveur VMM pendant le basculement.
Les scripts côté principal ne sont disponibles que pour les phases précédant et suivant l’arrêt. En effet, nous prévoyons que le site principal ne sera généralement pas disponible lors des incidents.
Pendant un basculement non planifié, et uniquement si vous optez pour les opérations de site principal, il tentera d'exécuter les scripts côté principal. S’ils ne sont pas accessibles ou si le délai d'attente est dépassé, le basculement continuera de récupérer les machines virtuelles.
Les scripts côté principal ne sont pas disponibles pour les sites VMware/physiques/Hyper-V sans VMM protégé vers Azure - lors du basculement vers Azure.
Toutefois, lors de la restauration à partir d’Azure vers un serveur local, les scripts côté principal (Runbook) peuvent être utilisés pour toutes les cibles à l’exception de VMware.

## <a name="test-the-recovery-plan"></a>Tester le plan de récupération
Une fois que vous avez ajouté le Runbook au plan, vous pouvez lancer un test de basculement et le voir s’exécuter. Nous vous recommandons d’exécuter systématiquement un basculement de test pour tester l’application et le plan de récupération, afin de vérifier qu’ils ne présentent aucune erreur.

1. Sélectionnez le plan de récupération et démarrez un test de basculement.
2. Pendant l’exécution du plan, vous pouvez voir si le Runbook s’est exécuté ou non, en consultant son état.

   ![](media/site-recovery-runbook-automation/17.png)
3. Vous pouvez également voir le détail de l’état d’exécution du Runbook sur la page des tâches Microsoft Azure Automation pour le Runbook.

   ![](media/site-recovery-runbook-automation/18.png)
4. Une fois le basculement terminé, mis à part le résultat de l’exécution du Runbook, vous pouvez voir si l’exécution a abouti en consultant la page relative à la machine virtuelle Microsoft Azure et en examinant les points de terminaison.

![](media/site-recovery-runbook-automation/19.png)

## <a name="sample-scripts"></a>Exemples de scripts
Dans ce didacticiel, nous avons passé en revue la procédure d’automatisation d’une tâche fréquemment exécutée, qui inclut l’ajout d’un point de terminaison à une machine virtuelle Microsoft Azure. Cependant, Microsoft Azure Automation vous permet d’effectuer d’autres tâches d’automatisation très puissantes. Microsoft et la communauté Microsoft Azure Automation proposent des exemples de Runbooks qui peuvent vous aider à créer vos propres solutions, ainsi que des Runbooks utilitaires, que vous pouvez ensuite utiliser en tant que composantes pour des tâches d’automatisation plus importantes. Vous pouvez les utiliser à partir de la galerie et créer des plans de récupération puissants pour vos applications, en un seul clic à l’aide d’Azure Site Recovery.

## <a name="additional-resources"></a>Ressources supplémentaires
[Vue d’ensemble de Microsoft Azure Automation](http://msdn.microsoft.com/library/azure/dn643629.aspx "Vue d’ensemble de Microsoft Azure Automation")

[Exemples de scripts Microsoft Azure Automation](http://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=User&f\[0\].Value=SC%20Automation%20Product%20Team&f\[0\].Text=SC%20Automation%20Product%20Team "Exemples de scripts Microsoft Azure Automation")

