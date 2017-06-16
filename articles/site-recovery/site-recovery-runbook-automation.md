---
title: "Ajouter des Runbooks Azure Automation à des plans de récupération dans Site Recovery | Microsoft Docs"
description: "Cet article explique comment Microsoft Azure Site Recovery vous permet désormais d’étendre des plans de récupération à l’aide de Microsoft Azure Automation, afin d’effectuer des tâches complexes lors de la récupération vers Microsoft Azure"
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: ecece14d-5f92-4596-bbaf-5204addb95c2
ms.service: site-recovery
ms.devlang: powershell
ms.tgt_pltfrm: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 02/22/2017
ms.author: ruturajd@microsoft.com
ms.translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 198caeea693fbc48b6e0eb1c9c8ee559e0553261
ms.contentlocale: fr-fr
ms.lasthandoff: 03/31/2017


---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Ajouter des Runbooks Azure Automation à des plans de récupération
Ce didacticiel explique comment Microsoft Azure Site Recovery s’intègre dans Microsoft Azure Automation pour permettre l’extensibilité des plans de récupération. Les plans de récupération peuvent orchestrer la récupération des machines virtuelles protégées via Microsoft Azure Site Recovery, pour la réplication vers un cloud secondaire et la réplication vers Microsoft Azure. Ils garantissent que le récupération est **précise et cohérente**, **répétable** et **automatisée**. Si vous effectuez le basculement de vos machines virtuelles vers Microsoft Azure, l’intégration avec Microsoft Azure Automation étend les plans de récupération et vous offre la possibilité d’exécuter des Runbooks, ce qui vous donne accès à des tâches d’automatisation très puissantes.

Si vous n’avez pas encore entendu parler de Microsoft Azure Automation, connectez-vous [ici](https://azure.microsoft.com/services/automation/) et téléchargez [ici](https://azure.microsoft.com/documentation/scripts/) des exemples de scripts. Lisez plus d’informations sur [Microsoft Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) et découvrez [ici](https://azure.microsoft.com/blog/?p=166264) comment orchestrer la récupération vers Microsoft Azure à l’aide de plans de récupération.

Ce didacticiel montre comment intégrer des runbooks Azure Automation dans des plans de récupération. Nous allons automatiser des tâches simples, qui nécessitaient auparavant une intervention manuelle, et voir comment convertir une opération de récupération en plusieurs étapes en une seule action, impliquant un seul clic.

## <a name="customize-the-recovery-plan"></a>Personnaliser un plan de récupération
1. Commençons par ouvrir le panneau de ressources du plan de récupération. Vous pouvez voir que deux machines virtuelles ont été ajoutées au plan de récupération pour la récupération.

    ![](media/site-recovery-runbook-automation-new/essentials-rp.PNG)
- - -
1. Cliquez sur le bouton Personnaliser pour commencer l’ajout d’un runbook.

    ![](media/site-recovery-runbook-automation-new/customize-rp.PNG)


1. Cliquez avec le bouton droit sur Groupe 1 : Démarrer et sélectionnez Ajouter une action postérieure.
2. Optez pour le choix d’un script dans le nouveau panneau.
3. Nommez le script « Hello World ».
4. Choisissez un nom de compte Automation.
    >[!NOTE]
    > Le compte Automation peut se trouver dans n’importe quel emplacement géographique Azure, mais il doit se trouver dans le même abonnement que le coffre Site Recovery.

5. Sélectionnez un runbook à partir du compte Automation. Ce runbook correspond au script qui s’exécutera pendant l’exécution du plan de récupération après la récupération du premier groupe.

    ![](media/site-recovery-runbook-automation-new/update-rp.PNG)
6. Cliquez sur OK pour enregistrer le script. Le script est ajouté au groupe d’actions postérieures de Groupe 1 : Démarrer.

    ![](media/site-recovery-runbook-automation-new/addedscript-rp.PNG)


## <a name="salient-points-of-adding-a-script"></a>Points essentiels de l’ajout d’un script
1. Vous pouvez cliquer avec le bouton droit sur le script et choisir Supprimer l’étape ou Mettre à jour le script.
2. Un script peut s’exécuter sur Azure lors du basculement depuis l’emplacement local vers Azure. Il peut s’exécuter sur Azure en tant que script du côté principal avant l’arrêt, pendant la restauration d’Azure vers l’emplacement local.
3. Lorsqu’un script s’exécute, il injecte un contexte de plan de récupération.

Voici à quoi ressemble la variable de contexte.

        {"RecoveryPlanName":"hrweb-recovery",

        "FailoverType":"Test",

        "FailoverDirection":"PrimaryToSecondary",

        "GroupId":"1",

        "VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":

                { "SubscriptionId":"7a1111111-c1d6-49c5-8c5d-111ce8dd183",

                "ResourceGroupName":"ContosoRG",

                "CloudServiceName":"pod02hrweb-Chicago-test",

                "RoleName":"Fabrikam-Hrweb-frontend-test",

                "RecoveryPointId":"TimeStamp"}

                }

        }


Le tableau suivant contient le nom et la description de chaque variable dans le contexte.

| **Nom de la variable** | **Description** |
| --- | --- |
| RecoveryPlanName |Nom du plan en cours d'exécution. Cette variable vous permet d’effectuer des actions différentes en fonction du nom et en réutilisant le script. |
| FailoverType |Indique si le basculement est un test, planifié ou non planifié. |
| FailoverDirection |Indique si la récupération est principale ou secondaire |
| GroupID |Identifie le numéro de groupe dans le plan de récupération lorsque le plan est en cours d'exécution. |
| VmMap |Tableau de toutes les machines virtuelles du groupe. |
| Clé VMMap |Clé unique (GUID) pour chaque machine virtuelle. Ce GUID est identique à l’ID VMM de la machine virtuelle, le cas échéant. |
| SubscriptionId |ID de l’abonnement Azure dans lequel la machine virtuelle est créée. |
| RoleName |Nom de la machine virtuelle Azure qui est en cours de récupération |
| CloudServiceName |Nom Azure Cloud Service sous lequel la machine virtuelle est créée. |
| ResourceGroupName|Nom du groupe de ressources Azure sous lequel la machine virtuelle est créée. |
| RecoveryPointId|Heure et date auxquelles la machine virtuelle est récupérée. |

Vous devez également veiller à ce que les modules suivants soient ajoutés au compte Automation. Tous les modules doivent présenter une version compatible. Un moyen simple de s’en assurer consiste à utiliser la dernière version disponible pour l’ensemble des modules.
* AzureRM.profile
* AzureRM.Resources
* AzureRM.Automation
* AzureRM.Network
* AzureRM.Compute


### <a name="accessing-all-vms-of-the-vmmap-in-a-loop"></a>Accès à toutes les machines virtuelles du tableau VmMap dans une boucle
Utilisez l’extrait de code suivant pour effectuer une boucle sur toutes les machines virtuelles du tableau VmMap.

```
    $VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
    $vmMap = $RecoveryPlanContext.VmMap
     foreach($VMID in $VMinfo)
     {
         $VM = $vmMap.$VMID                
             if( !(($VM -eq $Null) -Or ($VM.ResourceGroupName -eq $Null) -Or ($VM.RoleName -eq $Null))) {
             #this check is to ensure that we skip when some data is not available else it will fail
         Write-output "Resource group name ", $VM.ResourceGroupName
         Write-output "Rolename " = $VM.RoleName
         }
     }

```

> [!NOTE]
> Les valeurs du nom du groupe de ressources et du nom de rôle sont vides lorsque le script est une action antérieure pour un groupe de démarrage. Ces valeurs sont uniquement remplies si le basculement de la machine virtuelle de ce groupe réussit et le script est une action postérieure du groupe de démarrage.

## <a name="using-the-same-automation-runbook-with-multiple-recovery-plans"></a>Utilisation du même runbook Automation avec plusieurs plans de récupération

Un même script peut être utilisé dans plusieurs plans de récupération à l’aide de variables externes. Vous pouvez utiliser les [variables Azure Automation](../automation/automation-variables.md) afin de stocker des paramètres que vous pouvez transmettre pour l’exécution d’un plan de récupération. En ajoutant le nom du plan de récupération en préfixe de la variable, vous pouvez créer des variables individuelles pour chaque plan de récupération et les utiliser en tant que paramètre. Vous pouvez modifier le paramètre sans modifier le script et faire fonctionner le script différemment.

### <a name="using-simple-string-variables-in-runbook-script"></a>Utilisation de variables de chaîne simples dans un script de runbook

Prenons l’exemple d’un script qui accepte l’entrée d’un groupe de sécurité réseau et l’applique aux machines virtuelles d’un plan de récupération.

Pour que le script comprenne quel plan de récupération est en cours d’exécution, vous pouvez utiliser le contexte du plan de récupération.

```
    workflow AddPublicIPAndNSG {
        param (
              [parameter(Mandatory=$false)]
              [Object]$RecoveryPlanContext
        )

        $RPName = $RecoveryPlanContext.RecoveryPlanName
```

Pour appliquer un groupe de sécurité réseau existant, vous avez besoin du nom du groupe de sécurité réseau et du groupe de ressources du groupe de sécurité réseau. Nous fournissons ces variables en tant qu’entrée pour les scripts du plan de récupération. Pour ce faire, vous devez créer deux variables dans les ressources du compte Automation et ajoutez-y en préfixe le nom du plan de récupération pour lequel vous créez les paramètres.

1. Créez une variable pour stocker le nom du groupe de sécurité réseau. Ajoutez-y le nom du plan de récupération en préfixe.
    ![Création de la variable du nom du groupe de ressources](media/site-recovery-runbook-automation-new/var1.png)

2. Créez une variable pour stocker le nom du groupe de ressources du groupe de sécurité réseau. Ajoutez-y le nom du plan de récupération en préfixe.
    ![Création du nom du groupe de ressources du groupe de sécurité réseau](media/site-recovery-runbook-automation-new/var2.png)


Dans le script, récupérez les valeurs des variables à l’aide du code de référence suivant :

```
    $NSGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSG"
    $NSGRGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSGRG"

    $NSGnameVar = Get-AutomationVariable -Name $NSGValue
    $RGnameVar = Get-AutomationVariable -Name $NSGRGValue
```

Ensuite, vous pouvez utiliser les variables dans le runbook et appliquer le groupe de sécurité réseau à l’interface réseau de la machine virtuelle basculée.

```
     InlineScript {
        if (($Using:NSGname -ne $Null) -And ($Using:NSGRGname -ne $Null)) {
            $NSG = Get-AzureRmNetworkSecurityGroup -Name $Using:NSGname -ResourceGroupName $Using:NSGRGname
            Write-output $NSG.Id
            #Apply the NSG to a network interface
            #$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
            #Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
            #  -AddressPrefix 192.168.1.0/24 -NetworkSecurityGroup $NSG
        }
    }
```

Pour chaque plan de récupération, créez des variables indépendantes afin de pouvoir réutiliser le script et ajoutez-y le nom du plan de récupération en préfixe. Un script complet pour cet exemple est [proposé ici](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee).


### <a name="using-complex-variable-to-store-more-information"></a>Utilisation d’une variable complexe pour stocker plus d’informations

Imaginez un scénario où vous souhaitez simplement qu’un script active une adresse IP publique sur des machines virtuelles spécifiques. Vous pourriez aussi vouloir appliquer des groupes de sécurité réseau différents à des machines virtuelles différentes (pas à l’ensemble d’entre elles). Ce script doit être réutilisable pour n’importe quel autre plans de récupération. Chaque plan de récupération peut présenter un nombre variable de machines virtuelles (par exemple, une récupération SharePoint présente deux serveurs frontaux et une application métier simple un seul serveur frontal). Pour parvenir à ce résultat, il n’est pas possible de créer des variables distinctes pour chaque plan de récupération. Ici, nous utilisons une nouvelle technique et créons une [variable complexe](https://msdn.microsoft.com/library/dn913767.aspx?f=255&MSPPError=-2147217396) dans les ressources du compte Azure Automation en spécifiant plusieurs valeurs. Vous avez besoin d’Azure PowerShell pour exécuter les étapes suivantes.

1. Dans Azure PowerShell, connectez-vous à votre abonnement.

    ```
        login-azurermaccount
        $sub = Get-AzureRmSubscription -Name <SubscriptionName>
        $sub | Select-AzureRmSubscription
    ```

2. Pour stocker les paramètres, créez la variable complexe en utilisant le même nom que le plan de récupération.

    ```
        $VMDetails = @{"VMGUID"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"};"VMGUID2"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"}}
        New-AzureRmAutomationVariable -ResourceGroupName <RG of Automation Account> -AutomationAccountName <AA Name> -Name <RecoveryPlanName> -Value $VMDetails -Encrypted $false
    ```

    Dans cette variable complexe, **VMDetails* est l’ID de machine virtuelle de la machine virtuelle protégée. Vous pouvez le trouver dans les propriétés de la machine virtuelle sur le portail. Ici, nous avons créé une variable pour stocker les détails de deux machines virtuelles.

    ![ID de machine virtuelle à utiliser en tant que GUID](media/site-recovery-runbook-automation-new/vmguid.png)

3. Utilisez cette variable dans votre runbook et appliquez le groupe de sécurité réseau à la machine virtuelle si l’un des VMGUID donnés est trouvé dans le contexte du plan de récupération.

    ```
        $VMDetailsObj = Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName
    ```

4. Dans votre runbook, créez une boucle sur les machines virtuelles du plan de récupération et vérifiez si la machine virtuelle existe également dans **$VMDetailsObj**. Si elle existe, appliquez le groupe de sécurité réseau en accédant aux propriétés de la variable.
    ```
        $VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
        $vmMap = $RecoveryPlanContext.VmMap

        foreach($VMID in $VMinfo) {
            Write-output $VMDetailsObj.value.$VMID

            if ($VMDetailsObj.value.$VMID -ne $Null) { #If the VM exists in the context, this will not b Null
                $VM = $vmMap.$VMID
                # Access the properties of the variable
                $NSGname = $VMDetailsObj.value.$VMID.'NSGName'
                $NSGRGname = $VMDetailsObj.value.$VMID.'NSGResourceGroupName'

                # Add code to apply the NSG properties to the VM
            }
        }
    ```

Vous pouvez utiliser le même script avec des plans de récupération différents et fournir des paramètres différents en stockant la valeur correspondant aux différents plans de récupération dans une variable distincte.

## <a name="sample-scripts"></a>Exemples de scripts
Déployez les exemples de scripts dans votre compte Automation à l’aide du bouton Déployer sur Azure ci-dessous.

[![Déploiement sur Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

Regardez aussi une courte vidéo sur la récupération d’une application WordPress à deux niveaux sur Azure.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]



## <a name="additional-resources"></a>Ressources supplémentaires
[Compte d’identification du service Azure Automation](../automation/automation-sec-configure-azure-runas-account.md)

[Vue d’ensemble de Microsoft Azure Automation](http://msdn.microsoft.com/library/azure/dn643629.aspx "Vue d’ensemble de Microsoft Azure Automation")

[Exemples de scripts Microsoft Azure Automation](http://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=User&f\[0\].Value=SC%20Automation%20Product%20Team&f\[0\].Text=SC%20Automation%20Product%20Team "Exemples de scripts Microsoft Azure Automation")

