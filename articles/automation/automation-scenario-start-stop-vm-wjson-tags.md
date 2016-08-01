<properties
   pageTitle="Utilisation de balises au format JSON pour créer une planification pour le démarrage et l’arrêt de la machine virtuelle Azure | Microsoft Azure"
   description="Cet article explique comment utiliser des chaînes JSON sur des balises pour automatiser la planification du démarrage et de l’arrêt d’une machine virtuelle."
   services="automation"
   documentationCenter=""
   authors="MGoedtel"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/18/2016"
   ms.author="magoedte;paulomarquesc" />

# Scénario Azure Automation : utilisation de balises au format JSON pour créer une planification pour le démarrage et l’arrêt de la machine virtuelle Azure

Généralement, les clients souhaitent planifier le démarrage et l’arrêt des machines virtuelles pour aider à réduire les frais d’abonnement ou prendre en charge des exigences commerciales et techniques.

Le scénario suivant vous permet d’automatiser le démarrage et l’arrêt de vos machines virtuelles à l’aide d’une balise appelée Schedule au niveau d’un groupe de ressources ou d’une machine virtuelle dans Azure. Cette planification peut être configurée du dimanche au samedi, avec une heure de démarrage et une heure d’arrêt. Même si nous disposons de certaines options prêtes à l’emploi, comme [Jeux de mise à l’échelle de machine virtuelle](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) avec des paramètres de mise à l’échelle automatique, et le service [DevTest laboratoires](../devtest-lab/devtest-lab-overview.md), qui intègre une fonctionnalité de planification des opérations de démarrage et d’arrêt, ces options prennent uniquement en charge des scénarios spécifiques et ne peuvent pas être appliquées aux machines virtuelles IaaS.

Lorsque la balise Schedule est appliquée à un groupe de ressources, elle s’appliquera à toutes les machines virtuelles à l’intérieur de ce groupe de ressources. Si une planification est également directement appliquée à une machine virtuelle, la dernière planification est prioritaire dans l’ordre suivant :

1.  Planification appliquée à un groupe de ressources
2.  Planification appliquée à un groupe de ressources et à une machine virtuelle dans le groupe de ressources
3.  Planification appliquée à une machine virtuelle

Ce scénario sélectionne une chaîne JSON dans un format spécifié et l’ajoute comme valeur d’une balise appelée Schedule. Un Runbook répertorie ensuite tous les groupes de ressources et toutes les machines virtuelles, puis identifie les planifications pour chaque machine virtuelle en fonction des scénarios susmentionnés, puis il parcourt ces machines virtuelles avec les planifications associées et évalue l’action à effectuer, qui peut être démarrée, arrêtée ou ignorée.

Ces runbooks s’authentifient à l’aide du [compte d’identification Azure](../automation/automation-sec-configure-azure-runas-account.md).

## Obtention du scénario

Ce scénario se compose de quatre Runbooks de workflow PowerShell que vous pouvez télécharger à partir de la [Galerie TechNet](https://gallery.technet.microsoft.com/Azure-Automation-Runbooks-84f0efc7) ou du référentiel [GitHub](https://github.com/paulomarquesdacosta/azure-automation-scheduled-shutdown-and-startup) pour ce projet.

Runbook | Description 
----------|----------
Test-ResourceSchedule | Vérifie la planification de chaque machine virtuelle (arrête ou démarre les machines virtuelles selon la planification)
Add-ResourceSchedule | Ajoute la balise Schedule à une machine virtuelle ou un groupe de ressources
Update-ResourceSchedule | Modifie une balise Schedule existante en la remplaçant par une nouvelle balise
Remove-ResourceSchedule | Supprime la balise Schedule d’une machine virtuelle ou d’un groupe de ressources 


## Installation et configuration de ce scénario

### Installer et publier des Runbook

Après avoir téléchargé les Runbooks, vous pouvez les importer à l'aide de la procédure décrite dans les [procédures d’importation de Runbooks](automation-creating-importing-runbook.md#importing-a-runbook-from-a-file-into-Azure-Automation). Publiez chaque Runbook une fois qu’il a été correctement importé dans votre compte Automation.


### Ajout d’une planification à Test-ResourceSchedule

Procédez comme suit pour activer la planification vers le Runbook Test-ResourceSchedule. Il s’agit du Runbook qui vérifie quelle machine virtuelle sera démarrée, arrêtée ou ignorée.

1. À partir du portail Azure, ouvrez votre compte Automation, puis cliquez sur la mosaïque **Runbooks**.
2. Sur le panneau **Test-ResourceSchedule**, cliquez sur la mosaïque **Planifications**.
3. Sur le panneau **Planifications**, cliquez sur **Ajouter une planification**.
4. Dans le panneau **Planifications**, sélectionnez **Associer une planification à votre runbook**, puis, dans le panneau **Planification**, sélectionnez **Créer une planification**.
5.  Dans le panneau **Nouvelle planification**, tapez le nom de cette planification. Exemple, HourlyExecution.
6. Pour la planification **Démarrer**, définissez l’heure de démarrage avec un incrément arrondi à l’heure.
7. Sélectionnez **Périodicité** et, pour l’intervalle **de reproduction**, sélectionnez **1 heure** .
8. Vérifiez que l’option **Définir l’expiration** est définie sur **Non**, puis cliquez sur **Créer** pour enregistrer votre nouvelle planification.
9. Dans le panneau des options de la **planification du Runbook**, sélectionnez **Paramètres et valeurs pour l’exécution** et, dans le panneau des **paramètres** Test-ResourceSchedule, entrez le nom de votre abonnement dans le champ **SubscriptionName**. Il s’agit du seul paramètre nécessaire au Runbook. Lorsque vous avez terminé, cliquez sur **OK**.
   

Une fois terminée, la planification du Runbook devrait ressembler à ce qui suit :<br> ![Runbook Test-ResourceSchedule configuré](./media/automation-scenario-start-stop-vm-wjson-tags/automation-schedule-config.png)<br>

## Format JSON de la planification

Cette solution sélectionne une chaîne JSON dans un format spécifié et l’ajoute comme valeur d’une balise appelée Schedule. Un Runbook répertorie ensuite tous les groupes de ressources et machines virtuelles, identifie les planifications de chaque machine virtuelle, réexamine régulièrement ces machines virtuelles avec les planifications associées puis vérifie quelle action devrait être effectuée. L’exemple suivant montre comment la planification devrait être formatée.

    {
       "TzId": "Eastern Standard Time", 
        "0": {  
           "S": "11",
           "E": "17" 
        },
        "1": {
           "S": "9",
           "E": "19"
        },
        "2": {
           "S": "9",
           "E": "19"
        },
    }

Informations détaillées sur cette structure :

1. Le format de cette structure JSON est optimisé pour contourner la limite de 256 caractères affectant une valeur de balise unique dans Azure

2. *TzId* représente le fuseau horaire de la machine virtuelle. Cet ID peut être obtenu à l’aide de la classe .NET TimeZoneInfo dans une session PowerShell - **[System.TimeZoneInfo]::GetSystemTimeZones()**.<br>

    ![GetSystemTimeZones dans PowerShell](./media/automation-scenario-start-stop-vm-wjson-tags/automation-get-timzone-powershell.png)

    - Les jours de la semaine sont représentés par une valeur numérique de 0 à 6. La valeur 0 correspond à dimanche.
    - L’heure de début est représentée par l’attribut **S** et sa valeur est exprimée dans un format 24 heures.
    - L’heure de fin ou d’arrêt est représentée par l’attribut **E** et sa valeur est exprimée dans un format 24 heures.

    Si les attributs S et E ont une valeur égale à zéro (0), la machine virtuelle restera dans son état actuel au moment de l’évaluation.

3. Si vous souhaitez ignorer l’évaluation un jour spécifique de la semaine, n’ajoutez pas la section correspondant à ce jour de la semaine. Dans l’exemple suivant, l’évaluation se limitera au lundi et les autres jours de la semaine seront ignorés.
   
        {
          "TzId": "Eastern Standard Time",
           "1": {
             "S": "11",
             "E": "17"
           }
        }

## Balisage de groupes de ressources ou de machines virtuelles

Pour arrêter des machines virtuelles, vous devez les baliser ou baliser les groupes de ressources dans lesquels elles se trouvent. Les machines virtuelles qui n’ont pas de balise Schedule ne sont pas évaluées et, par conséquent, elles sont ni démarrées ni arrêtées. Il existe deux façons de baliser des groupes de ressources ou des machines virtuelles avec cette solution, directement depuis le portail ou à l’aide des Runbooks **Add-ResourceSchedule**, **Update-ResourceSchedule** et **Remove-ResourceSchedule**.

### Balisage via le portail

Suivez ces étapes pour baliser une machine virtuelle ou un groupe de ressources dans le portail.

1. Aplatissez la chaîne JSON et vérifiez qu’elle ne contient pas d’espaces. La chaîne JSON devrait ressembler à ceci :

        {"TzId":"Eastern Standard Time","0":{"S":"11","E":"17"},"1":{"S":"9","E":"19"},"2": {"S":"9","E":"19"},"3":{"S":"9","E":"19"},"4":{"S":"9","E":"19"},"5":{"S":"9","E":"19"},"6":{"S":"11","E":"17"}}
   

2. Sélectionnez une machine virtuelle ou un groupe de ressources pour y appliquer cette planification en sélectionnant l’icône de balise.<br>![Option de balisage de machine virtuelle](./media/automation-scenario-start-stop-vm-wjson-tags/automation-vm-tag-option.png)
3. Les balises sont définies à l’aide d’une paire Clé/Valeur. Tapez **Schedule** dans le champ **Clé**, collez la chaîne JSON dans le champ **Valeur**, puis cliquez sur **Enregistrer**. Votre nouvelle balise doit maintenant apparaître dans la liste des balises pour votre ressource.<br>![Balise de planification de machine virtuelle](./media/automation-scenario-start-stop-vm-wjson-tags/automation-vm-schedule-tag.png)


### Balisage depuis PowerShell

Tous les Runbooks importés contient des informations d’aide au début du script, expliquant comment exécuter les Runbooks directement à partir de PowerShell. Les Runbooks Add-ScheduleResource et ScheduleResource peuvent être appelés à partir de PowerShell en passant les paramètres requis, ce qui vous permet de créer ou de mettre à jour la balise Schedule sur une machine virtuelle ou un groupe de ressources en dehors du portail.

Pour créer, ajouter et supprimer des balises par le biais de PowerShell, vous devez d’abord [configurer votre environnement PowerShell pour Azure](../powershell-install-configure.md). Une fois que vous avez terminé la configuration, vous pouvez passer aux étapes suivantes.

### Création d’une balise Schedule avec PowerShell

1. Ouvrez une session PowerShell et exécutez la commande suivante pour vous authentifier auprès de votre compte d’identification, puis spécifiez un abonnement :
    
        Conn = Get-AutomationConnection -Name AzureRunAsConnection
        Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
        -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
        Select-AzureRmSubscription -SubscriptionName "MySubscription"
   
2. Définissez une table de hachage de planification. Voici un exemple montrant comment cette table devrait être créée :
    
        $schedule= @{ "TzId"="Eastern Standard Time"; "0"= @{"S"="11";"E"="17"};"1"= @{"S"="9";"E"="19"};"2"= @{"S"="9";"E"="19"};"3"= @{"S"="9";"E"="19"};"4"= @{"S"="9";"E"="19"};"5"= @{"S"="9";"E"="19"};"6"= @{"S"="11";"E"="17"}}

3. Définissez les paramètres nécessaires au Runbook. Dans l’exemple suivant, nous balisons une machine virtuelle :

        $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"; `
        "VmName"="VM01";"Schedule"=$schedule}

    Si vous balisez un groupe de ressources, supprimez simplement le paramètre *VMName* de la table de hachage $params comme suit :

        $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"; `
        "Schedule"=$schedule}

4. Exécutez le Runbook **Add-ResourceSchedule** avec les paramètres suivants pour créer la balise Schedule :

        Start-AzureRmAutomationRunbook -Name "Add-ResourceSchedule" -Parameters $params `
        -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"

5. Si vous souhaitez mettre à jour la balise d’un groupe de ressources ou d’une machine virtuelle, exécutez le Runbook **Update-ResourceSchedule** avec les paramètres suivants :

        Start-AzureRmAutomationRunbook -Name "Update-ResourceSchedule" -Parameters $params `
        -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"

### Suppression d’une balise Schedule avec PowerShell

1. Ouvrez une session PowerShell et exécutez la commande suivante pour vous authentifier auprès de votre compte d’identification, puis spécifiez un abonnement :
    
        Conn = Get-AutomationConnection -Name AzureRunAsConnection
        Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
        -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
        Select-AzureRmSubscription -SubscriptionName "MySubscription"

2. Définissez les paramètres nécessaires au Runbook. Dans l’exemple suivant, nous balisons une machine virtuelle :

        $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01" ` 
        ;"VmName"="VM01"}

    Si vous supprimez une balise d’un groupe de ressources, supprimez simplement le paramètre *VMName* de la table de hachage $params comme suit :

        $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"}

3. Exécutez le Runbook **Remove-ResourceSchedule** pour supprimer la balise Schedule :

        Start-AzureRmAutomationRunbook -Name "Remove-ResourceSchedule" -Parameters $params `
        -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"

4. Si vous souhaitez mettre à jour la balise d’un groupe de ressources ou d’une machine virtuelle, exécutez le Runbook **Remove-ResourceSchedule** avec les paramètres suivants :

        Start-AzureRmAutomationRunbook -Name "Remove-ResourceSchedule" -Parameters $params `
        -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"


>[AZURE.NOTE] Il est recommandé de surveiller de façon proactive ces Runbooks (et l’état de la machine virtuelle) pour vérifier que vos machines virtuelles s’arrêtent et démarrent en conséquence.

Vous pouvez afficher les détails de la tâche du Runbook **ResourceSchedule-Test** dans le portail Azure en sélectionnant la mosaïque Travaux du Runbook. Le résumé de la tâche affiche les paramètres d’entrée et le flux de sortie en plus des informations générales sur la tâche et des exceptions, le cas échéant. L’historique inclura les messages provenant du flux de sortie ainsi que des flux d’erreurs et d’avertissements. Sélectionnez la mosaïque Sortie pour afficher les résultats détaillés de l’exécution du Runbook.<br> ![Sortie Test-ResourceSchedule](./media/automation-scenario-start-stop-vm-wjson-tags/automation-job-output.png)

## Étapes suivantes

-  Pour prendre en main des Runbooks de workflow PowerShell, consultez [Mon premier Runbook PowerShell Workflow](automation-first-runbook-textual.md)
-  Pour en savoir plus sur les types de Runbook, leurs avantages et leurs limites, consultez [Types de Runbooks Azure Automation](automation-runbook-types.md)
-  Pour plus d’informations sur la fonctionnalité de prise en charge de script PowerShell, consultez le billet [Native PowerShell script support in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/) (Prise en charge de script PowerShell natif dans Azure Automation)
-  Pour plus d’informations sur la journalisation et la sortie de Runbook, consultez [Sortie et messages de Runbook dans Azure Automation](automation-runbook-output-and-messages.md)
-  Pour plus d’informations sur le compte d’identification Azure et comment authentifier vos runbooks qui l’utilisent, consultez [Authentifier des Runbooks avec un compte d’identification Azure](../automation/automation-sec-configure-azure-runas-account.md)

<!---HONumber=AcomDC_0720_2016-->