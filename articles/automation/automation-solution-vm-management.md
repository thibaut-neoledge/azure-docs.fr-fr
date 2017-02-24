---
title: "Solution Start/Stop VMs during off-hours (Preview) (Démarrer/arrêter des machines virtuelles durant les heures creuses [version préliminaire]) | Microsoft Docs"
description: "Cette solution de gestion de machines virtuelles assure le démarrage et l’arrêt de vos machines virtuelles Azure Resource Manager selon une planification, et permet une surveillance proactive depuis Log Analytics."
services: automation
documentationCenter: 
authors: mgoedtel
manager: carmonm
editor: 
ms.assetid: 06c27f72-ac4c-4923-90a6-21f46db21883
ms.service: automation
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 5ae60cb8ba3d391d3babd1ab575b4f32e139a185
ms.openlocfilehash: f2c9a5ef2a8f517b9b2072be57f4d8c51b7694c6

---

# <a name="startstop-vms-during-off-hours-preview-solution-in-automation"></a>Solution Start/Stop VMs during off-hours (Preview) (Démarrer/arrêter des machines virtuelles durant les heures creuses [version préliminaire]) dans Automation

La solution Start/Stop VMs during off-hours (Preview) (Démarrer/arrêter des machines virtuelles durant les heures creuses [version préliminaire]) assure le démarrage et l’arrêt de vos machines virtuelles Azure Resource Manager et fournit des informations sur la réussite des tâches Automation qui démarrent et arrêtent vos machines virtuelles avec OMS Log Analytics.  

## <a name="prerequisites"></a>Composants requis

- Les runbooks fonctionnent avec un [compte d’identification Azure](automation-sec-configure-azure-runas-account.md).  Le compte d’identification est la méthode d’authentification recommandée, car elle utilise l’authentification par certificat au lieu d’un mot de passe, susceptible d’expirer ou de changer fréquemment.  

- Cette solution permet uniquement de gérer les machines virtuelles qui se trouvent dans le même abonnement et le même groupe de ressources que le compte Automation.  

- Cette solution peut seulement être déployée dans les régions Azure suivantes : Sud-Est de l’Australie, États-Unis de l’Est, Sud-Est Asiatique et Europe de l’Ouest.  Les runbooks qui gèrent la planification des machines virtuelles peuvent cibler les machines virtuelles de n’importe quelle région.  

- L’envoi de notifications par courrier électronique à l’issue de l’exécution des runbooks de démarrage et d’arrêt de machines virtuelles nécessite un abonnement Office 365 professionnel.  

## <a name="solution-components"></a>Composants de la solution

Cette solution comprend les ressources suivantes, qui sont importées et ajoutées à votre compte Automation.

### <a name="runbooks"></a>Runbooks

Runbook | Description|
--------|------------|
CleanSolution-MS-Mgmt-VM | Ce runbook supprime toutes les ressources englobées, ainsi que les planifications, lorsque vous procédez à la suppression de la solution de votre abonnement.|  
SendMailO365-MS-Mgmt | Ce runbook envoie un message électronique via Office 365 Exchange.|
StartByResourceGroup-MS-Mgmt-VM | Ce runbook sert à démarrer des machines virtuelles (classiques et ARM) qui se trouvent dans une liste donnée de groupes de ressources Azure.
StopByResourceGroup-MS-Mgmt-VM | Ce runbook sert à arrêter des machines virtuelles (classiques et ARM) qui se trouvent dans une liste donnée de groupes de ressources Azure.|
<br>

### <a name="variables"></a>Variables

Variable | Description|
---------|------------|
Runbook **SendMailO365-MS-Mgmt** ||
SendMailO365-IsSendEmail-MS-Mgmt | Indique si les runbooks StartByResourceGroup-MS-Mgmt-VM et StopByResourceGroup-MS-Mgmt-VM peuvent envoyer une notification par courrier électronique une fois l’opération associée terminée.  Sélectionnez **True** pour activer et **False** pour désactiver les alertes par courrier électronique. La valeur par défaut est **False**.| 
Runbook **StartByResourceGroup-MS-Mgmt-VM** ||
StartByResourceGroup-ExcludeList-MS-Mgmt-VM | Entrez les noms des machines virtuelles à exclure de l’opération de gestion ; séparez leurs noms à l’aide de points-virgules (;). Les valeurs respectent la casse et le caractère générique (astérisque) est pris en charge.|
StartByResourceGroup-SendMailO365-EmailBodyPreFix-MS-Mgmt | Texte qui peut être ajouté au début du corps du message électronique.|
StartByResourceGroup-SendMailO365-EmailRunBookAccount-MS-Mgmt | Spécifie le nom du compte Automation qui contient le runbook de courrier électronique.  **Ne modifiez pas cette variable.**|
StartByResourceGroup-SendMailO365-EmailRunbookName-MS-Mgmt | Spécifie le nom du runbook de courrier électronique.  Cette variable est utilisée par les runbooks StartByResourceGroup-MS-Mgmt-VM et StopByResourceGroup-MS-Mgmt-VM pour l’envoi de courrier électronique.  **Ne modifiez pas cette variable.**|
StartByResourceGroup-SendMailO365-EmailRunbookResourceGroup-MS-Mgmt | Spécifie le nom du groupe de ressources qui contient le runbook de courrier électronique.  **Ne modifiez pas cette variable.**|
StartByResourceGroup-SendMailO365-EmailSubject-MS-Mgmt | Spécifie le texte de la ligne d’objet du message électronique.|  
StartByResourceGroup-SendMailO365-EmailToAddress-MS-Mgmt | Spécifie le ou les destinataires du message électronique.  Séparez les noms à l’aide de points-virgules (;).|
StartByResourceGroup-TargetResourceGroups-MS-Mgmt-VM | Entrez les noms des machines virtuelles à exclure de l’opération de gestion ; séparez leurs noms à l’aide de points-virgules (;). Les valeurs respectent la casse et le caractère générique (astérisque) est pris en charge.  La valeur par défaut (astérisque) inclut tous les groupes de ressources de l’abonnement.|
StartByResourceGroup-TargetSubscriptionID-MS-Mgmt-VM | Spécifie l’abonnement qui contient les machines virtuelles à gérer par cette solution.  Il doit s’agir du même abonnement que celui où le compte Automation de cette solution se trouve.|
Runbook **StopByResourceGroup-MS-Mgmt-VM** ||
StopByResourceGroup-ExcludeList-MS-Mgmt-VM | Entrez les noms des machines virtuelles à exclure de l’opération de gestion ; séparez leurs noms à l’aide de points-virgules (;). Les valeurs respectent la casse et le caractère générique (astérisque) est pris en charge.|
StopByResourceGroup-SendMailO365-EmailBodyPreFix-MS-Mgmt | Texte qui peut être ajouté au début du corps du message électronique.|
StopByResourceGroup-SendMailO365-EmailRunBookAccount-MS-Mgmt | Spécifie le nom du compte Automation qui contient le runbook de courrier électronique.  **Ne modifiez pas cette variable.**|
StopByResourceGroup-SendMailO365-EmailRunbookResourceGroup-MS-Mgmt | Spécifie le nom du groupe de ressources qui contient le runbook de courrier électronique.  **Ne modifiez pas cette variable.**|
StopByResourceGroup-SendMailO365-EmailSubject-MS-Mgmt | Spécifie le texte de la ligne d’objet du message électronique.|  
StopByResourceGroup-SendMailO365-EmailToAddress-MS-Mgmt | Spécifie le ou les destinataires du message électronique.  Séparez les noms à l’aide de points-virgules (;).|
StopByResourceGroup-TargetResourceGroups-MS-Mgmt-VM | Entrez les noms des machines virtuelles à exclure de l’opération de gestion ; séparez leurs noms à l’aide de points-virgules (;). Les valeurs respectent la casse et le caractère générique (astérisque) est pris en charge.  La valeur par défaut (astérisque) inclut tous les groupes de ressources de l’abonnement.|
StopByResourceGroup-TargetSubscriptionID-MS-Mgmt-VM | Spécifie l’abonnement qui contient les machines virtuelles à gérer par cette solution.  Il doit s’agir du même abonnement que celui où le compte Automation de cette solution se trouve.|  
<br>

### <a name="schedules"></a>Planifications

Planification | Description|
---------|------------|
StartByResourceGroup-Schedule-MS-Mgmt | Planification du runbook StartByResourceGroup, qui démarre les machines virtuelles gérées par cette solution. Une fois la création terminée, la valeur par défaut est le fuseau horaire UTC.|
StopByResourceGroup-Schedule-MS-Mgmt | Planification du runbook StartByResourceGroup, qui arrête les machines virtuelles gérées par cette solution. Une fois la création terminée, la valeur par défaut est le fuseau horaire UTC.|

### <a name="credentials"></a>Informations d'identification

Informations d'identification | Description|
-----------|------------|
O365Credential | Spécifie un compte d’utilisateur Office 365 valide pour l’envoi de courrier électronique.  Requis uniquement si la variable SendMailO365-IsSendEmail-MS-Mgmt est définie sur **True**.

## <a name="configuration"></a>Configuration

Procédez comme suit pour ajouter la solution Start/Stop VMs during off-hours (Preview) (Démarrer/arrêter des machines virtuelles durant les heures creuses [version préliminaire]) à votre compte Automation, puis configurer les variables pour personnaliser la solution.

1. Dans l’écran d’accueil du portail Azure, sélectionnez la vignette **Marketplace**.  Si la vignette n’est plus épinglée à votre écran d’accueil, dans le volet de navigation de gauche, sélectionnez **Nouveau**.  
2. Dans le panneau Marketplace, tapez **démarrer machine virtuelle** dans la zone de recherche, puis sélectionnez la solution **Start/Stop VMs during off-hours [Preview]** (Démarrer/arrêter des machines virtuelles durant les heures creuses [version préliminaire]) dans les résultats de la recherche.  
3. Dans le panneau **Start/Stop VMs during off-hours [Preview]** (Démarrer/arrêter des machines virtuelles durant les heures creuses [version préliminaire]) de la solution sélectionnée, vérifiez les informations de résumé, puis cliquez sur **Créer**.  
4. Le panneau **Ajouter une solution** s’affiche, vous invitant à configurer la solution pour pouvoir l’importer dans votre abonnement Automation.<br><br> ![Panneau Ajouter une solution de VM Management (Gestion de machines virtuelles)](media/automation-solution-vm-management/vm-management-solution-add-solution-blade.png)<br><br>
5.  Dans le panneau **Ajouter une solution**, sélectionnez **Espace de travail**, puis sélectionnez un espace de travail OMS lié à l’abonnement Azure où le compte Automation se trouve ou créez un espace de travail OMS.  Si vous ne disposez pas d’espace de travail OMS, vous pouvez sélectionner **Créer un espace de travail**, puis dans le panneau **Espace de travail OMS**, procédez comme suit : 
   - Spécifiez un nom pour le nouvel **espace de travail OMS**.
   - Dans la liste déroulante **Abonnement**, sélectionnez un abonnement à lier si la valeur par défaut sélectionnée n’est pas appropriée.
   - Pour **Groupe de ressources**, vous pouvez créer un groupe de ressources ou sélectionner un groupe de ressources existant.  
   - Sélectionnez un **emplacement**.  Actuellement, les seuls emplacements sélectionnables sont **Sud-Est de l’Australie**, **États-Unis de l’Est**, **Sud-Est asiatique** et **Europe de l’Ouest**.
   - Sélectionner un **niveau de tarification**.  Deux niveaux sont proposés pour la solution : gratuit et payant OMS.  La quantité de données collectées quotidiennement, la période de rétention et la durée d’exécution (en minutes) des tâches de runbook sont limitées pour le niveau gratuit.  Le niveau payant OMS permet de collecter une quantité illimitée de données quotidiennement.  

        > [!NOTE]
        > Tandis que la couche payée autonome s’affiche comme une option, elle n’est pas applicable.  Si vous la sélectionnez et poursuivez la création de cette solution dans votre abonnement, elle échouera.  Ce problème sera résolu lors de la sortie officielle de cette solution.<br>Si vous utilisez cette solution, elle utilise uniquement les minutes du travail d’automatisation et l’ingestion du journal.  La solution n’ajoute pas de nœuds OMS à votre environnement.  

6. Après avoir entré les informations requises dans le panneau **Espace de travail OMS**, cliquez sur **Créer**.  Pendant que les informations sont vérifiées et l’espace de travail créé, vous pouvez suivre la progression sous **Notifications** dans le menu.  Vous serez redirigé vers le panneau **Ajouter une solution**.  
7. Dans le panneau **Ajouter une solution**, sélectionnez **Compte Automation**.  Si vous créez un espace de travail OMS, vous devez également créer un compte Automation qui sera associé à l’espace de travail OMS spécifié précédemment, y compris votre abonnement, votre groupe de ressources et votre région Azure.  Vous pouvez sélectionner **Créer un compte Automation** et dans le panneau **Ajouter un compte Automation**, fournir les informations suivantes : 
  - Dans le champ **Nom**, saisissez le nom du compte Automation.

    Toutes les autres options sont renseignées automatiquement en fonction de l’espace de travail OMS sélectionné et ne peuvent pas être modifiées.  Un compte d’identification Azure est la méthode d’authentification par défaut pour les runbooks inclus dans cette solution.  Lorsque vous cliquez sur **OK**, les options de configuration sont validées et le compte Automation est créé.  Vous pouvez suivre la progression sous **Notifications** dans le menu. 

    Sinon, vous pouvez sélectionner un compte d’identification Automation existant.  Notez que le compte que vous sélectionnez ne peut pas déjà être lié à un autre espace de travail OMS, ou un message s’affichera dans le panneau pour vous en informer.  Si le compte est déjà lié, vous devez sélectionner un autre compte d’identification Automation ou en créer un nouveau.<br><br> ![Compte Automation déjà lié à l’espace de travail OMS](media/automation-solution-vm-management/vm-management-solution-add-solution-blade-autoacct-warning.png)<br>

8. Enfin, dans le panneau **Ajouter une solution**, sélectionnez **Configuration** pour afficher le panneau **Paramètres**.  Dans le panneau **Paramètres**, vous êtes invité à :  
   - Spécifier le **nom du groupe de ressources cible**, c’est-à-dire le nom du groupe de ressources qui contient les machines virtuelles à gérer par cette solution.  Vous pouvez entrer plusieurs noms en les séparant à l’aide de points-virgules (les valeurs respectent la casse).  Si vous souhaitez cibler les machines virtuelles de tous les groupes de ressources de l’abonnement, l’utilisation d’un caractère générique est prise en charge.
   - Sélectionnez une **planification**, c’est-à-dire une date et une heure récurrentes pour le démarrage et l’arrêt des machines virtuelles du ou des groupes de ressources.  Par défaut, la planification est configurée sur le fuseau horaire UTC et il est impossible de sélectionner une autre région.  Si vous souhaitez configurer la planification sur votre propre fuseau horaire après la configuration de la solution, consultez [Modification de la planification de démarrage et d’arrêt](#modifying-the-startup-and-shutdown-schedule) ci-dessous.    

10. Une fois que vous avez configuré les paramètres initiaux requis pour la solution, sélectionnez **Créer**.  Tous les paramètres sont validés et une tentative de déploiement de la solution dans votre abonnement est effectuée.  Ce processus peut prendre plusieurs secondes. Vous pouvez suivre la progression sous **Notifications** dans le menu. 

## <a name="collection-frequency"></a>Fréquence de collecte

Le journal des tâches Automation et les données des flux de tâches sont ingérés dans le référentiel OMS toutes les cinq minutes.  

## <a name="using-the-solution"></a>Utilisation de la solution

Lorsque vous ajoutez cette solution de gestion de machines virtuelles à votre espace de travail, la vignette **StartStopVMView** est ajoutée à votre tableau de bord OMS.  Cette vignette affiche un compteur et une représentation graphique des tâches de runbooks qui ont démarré et qui ont abouti.<br><br> ![Vignette StartStopVMView de VM Management (Gestion de machines virtuelles)](media/automation-solution-vm-management/vm-management-solution-startstopvm-view-tile.png)  

Dans votre compte Automation, vous pouvez accéder à la solution et la gérer en sélectionnant la vignette **Solutions** dans le panneau **Solutions**, puis en sélectionnant la solution **Start-Stop-VM[Espace de travail]** dans la liste.<br><br> ![Liste Solutions d’Automation](media/automation-solution-vm-management/vm-management-solution-autoaccount-solution-list.png)  

La sélection de la solution entraîne l’ouverture du panneau de la solution **Start-Stop-VM[Espace de travail]**, où vous pouvez consulter des informations importantes telles que la vignette **StartStopVM**, qui comme dans votre espace de travail OMS affiche un compteur et une représentation graphique des tâches de runbooks qui ont démarré et qui ont abouti.<br><br> ![Panneau Solution d’Automation](media/automation-solution-vm-management/vm-management-solution-solution-blade.png)  

À ce stade, vous pouvez également ouvrir votre espace de travail OMS et analyser plus en détail les enregistrements de tâche.  Cliquez simplement sur **Tous les paramètres**, et dans le panneau **Paramètres**, sélectionnez **Démarrage rapide** puis, dans **Démarrage rapide**, sélectionnez **Portail OMS**.   Cela a pour effet d’ouvrir un nouvel onglet ou une nouvelle session de navigateur et de présenter votre espace de travail OMS associé à votre compte et votre abonnement Automation.  


### <a name="configuring-e-mail-notifications"></a>Configuration des notifications par courrier électronique

Pour activer l’envoi de notifications par courrier électronique à l’issue de l’exécution des runbooks de démarrage et d’arrêt de machines virtuelles, vous devez modifier les informations d’identification **O365Credential** et au moins les variables suivantes :

 - SendMailO365-IsSendEmail-MS-Mgmt
 - StartByResourceGroup-SendMailO365-EmailToAddress-MS-Mgmt
 - StopByResourceGroup-SendMailO365-EmailToAddress-MS-Mgmt

Pour configurer les informations d’identification **O365Credential**, procédez comme suit :

1. Dans votre compte Automation, cliquez sur **Tous les paramètres** en haut de la fenêtre. 
2. Dans le panneau **Paramètres** situé sous la section **Ressources Automation**, sélectionnez **Actifs**. 
3. Dans le panneau **Actifs**, sélectionnez la vignette **Informations d’identification**, puis dans le panneau **Informations d’identification**, sélectionnez **O365Credential**.  
4. Entrez un nom d’utilisateur et un mot de passe Office 365 valides, puis cliquez sur **Enregistrer** pour enregistrer vos modifications.  

Pour configurer les variables indiquées précédemment, procédez comme suit :

1. Dans votre compte Automation, cliquez sur **Tous les paramètres** en haut de la fenêtre. 
2. Dans le panneau **Paramètres** situé sous la section **Ressources Automation**, sélectionnez **Actifs**. 
3. Dans le panneau **Actifs**, sélectionnez la vignette **Variables**, puis dans le panneau **Variables**, sélectionnez la variable indiquée ci-dessus et modifiez sa valeur en suivant la description spécifiée dans la section [Variables](##variables) plus haut.  
4. Cliquez sur **Enregistrer** pour enregistrer les modifications apportées à la variable.   

### <a name="modifying-the-startup-and-shutdown-schedule"></a>Modification de la planification de démarrage et d’arrêt

La gestion de la planification du démarrage et de l’arrêt dans cette solution suit la procédure indiquée dans [Planification d’un Runbook dans Azure Automation](automation-schedules.md).  N’oubliez pas que vous ne pouvez pas modifier la configuration de la planification.  Vous devez désactiver la planification existante et en créer une nouvelle, puis la lier au runbook **StartByResourceGroup-MS-Mgmt-VM** ou **StopByResourceGroup-MS-Mgmt-VM** auquel vous souhaitez que la planification s’applique.   

## <a name="log-analytics-records"></a>Enregistrements Log Analytics

Automation crée deux types d’enregistrements dans le référentiel OMS.

### <a name="job-logs"></a>Journaux de tâches

Propriété | Description|
----------|----------|
Appelant |  Ce qui a initié l’opération.  Les valeurs possibles sont une adresse de messagerie ou un système pour les travaux planifiés.|
Catégorie | Classification du type de données.  Pour Automation, la valeur est JobLogs.|
CorrelationId | GUID représentant l’ID de corrélation du travail du runbook.|
JobId | GUID représentant l’ID du travail du runbook.|
operationName | Spécifie le type d’opération exécutée dans Azure.  Pour Automation, la valeur sera Job.|
resourceId | Spécifie le type de ressource dans Azure.  Pour Automation, la valeur est le compte Automation associé au runbook.|
ResourceGroup | Spécifie le nom du groupe de ressources de la tâche du runbook.|
ResourceProvider | Spécifie le service qui fournit les ressources que vous pouvez déployer et gérer.  Pour Automation, la valeur est Azure Automation.|
ResourceType | Spécifie le type de ressource dans Azure.  Pour Automation, la valeur est le compte Automation associé au runbook.|
resultType | L’état du travail du runbook.  Les valeurs possibles sont les suivantes :<br>Démarré<br>Arrêté<br>Interrompu<br>Échec<br>- Succeeded|
resultDescription | Décrit l’état résultant du travail du runbook.  Les valeurs possibles sont les suivantes :<br>- Job is started<br>- Job Failed<br>- Job Completed|
RunbookName | Spécifie le nom du runbook.|
SourceSystem | Spécifie le système source pour les données soumises.  Pour Automation, la valeur sera OpsManager.|
StreamType | Spécifie le type d’événement. Les valeurs possibles sont les suivantes :<br>- Verbose<br>Sortie<br>Error<br>Avertissement|
SubscriptionId | Spécifie l’ID d’abonnement de la tâche.
Time | Date et heure d’exécution du travail du runbook.|


### <a name="job-streams"></a>Flux de tâches

Propriété | Description|
----------|----------|
Appelant |  Ce qui a initié l’opération.  Les valeurs possibles sont une adresse de messagerie ou un système pour les travaux planifiés.|
Catégorie | Classification du type de données.  Pour Automation, la valeur est JobStreams.|
JobId | GUID représentant l’ID du travail du runbook.|
operationName | Spécifie le type d’opération exécutée dans Azure.  Pour Automation, la valeur sera Job.|
ResourceGroup | Spécifie le nom du groupe de ressources de la tâche du runbook.|
resourceId | Spécifie l’ID de ressource dans Azure.  Pour Automation, la valeur est le compte Automation associé au runbook.|
ResourceProvider | Spécifie le service qui fournit les ressources que vous pouvez déployer et gérer.  Pour Automation, la valeur est Azure Automation.|
ResourceType | Spécifie le type de ressource dans Azure.  Pour Automation, la valeur est le compte Automation associé au runbook.|
resultType | Résultat de la tâche du runbook au moment où l’événement a été généré.  Les valeurs possibles sont les suivantes :<br>- InProgress|
resultDescription | Inclut le flux de sortie du runbook.|
RunbookName | Nom du runbook.|
SourceSystem | Spécifie le système source pour les données soumises.  Pour Automation, la valeur sera OpsManager.|
StreamType | Type de flux de travail. Les valeurs possibles sont les suivantes :<br>progress<br>Sortie<br>Avertissement<br>error<br>DEBUG<br>- Verbose|
Time | Date et heure d’exécution du travail du runbook.|

Lorsque vous effectuez une recherche de journal qui retourne des enregistrements de la catégorie **JobLogs** ou **JobStreams**, vous pouvez sélectionner la vue **JobLogs** ou **JobStreams** pour afficher un ensemble de vignettes résumant les informations renvoyées par la recherche.

## <a name="sample-log-searches"></a>Exemples de recherches de journaux

Le tableau suivant fournit des exemples de recherches de journaux pour les enregistrements de tâches collectés par cette solution. 

Requête | Description|
----------|----------|
Rechercher les tâches du runbook StartVM exécutées avec succès | Category=JobLogs RunbookName_s="StartByResourceGroup-MS-Mgmt-VM" ResultType=Succeeded &#124; measure count() by JobId_g|
Rechercher les tâches du runbook StopVM exécutées avec succès | Category=JobLogs RunbookName_s="StartByResourceGroup-MS-Mgmt-VM" ResultType=Failed &#124; measure count() by JobId_g
Afficher l’état des tâches au fil du temps pour les runbooks StartVM et StopVM | Category=JobLogs RunbookName_s="StartByResourceGroup-MS-Mgmt-VM" OR "StopByResourceGroup-MS-Mgmt-VM" NOT(ResultType="started") | measure Count() by ResultType interval 1day|

## <a name="removing-the-solution"></a>Suppression de la solution

Si vous estimez que vous n’avez plus besoin d’utiliser la solution, vous pouvez la supprimer à partir du compte Automation.  La suppression de la solution supprime uniquement les Runbooks, pas les planifications ou variables créées lors de l’ajout de la solution.  Vous devrez supprimer manuellement ces ressources si vous ne les utilisez pas avec d’autres Runbooks.  

Pour supprimer la solution, procédez comme suit :

1.  À partir de votre compte Automation, sélectionnez la vignette **Solutions**.  
2.  Dans le panneau **Solutions**, sélectionnez la solution **Start-Stop-VM [Workspace]**.  Dans le panneau **VMManagementSolution [Workspace]**, cliquez sur **Supprimer** à partir du menu.<br><br> ![Supprimer la solution de gestion de machine virtuelle](media/automation-solution-vm-management/vm-management-solution-delete.png)
3.  Dans la fenêtre **Supprimer la solution**, confirmez que vous souhaitez supprimer la solution.
4.  Pendant que les informations sont vérifiées et la solution supprimée, vous pouvez suivre la progression sous **Notifications** dans le menu.  Vous serez redirigé vers le panneau **VMManagementSolution [Workspace]** après le démarrage du processus de suppression de la solution.  

Le compte Automation et l’espace de travail OMS ne sont pas supprimés au cours de ce processus.  Si vous ne souhaitez pas conserver l’espace de travail OMS, vous devrez le supprimer manuellement.  Cette opération peut se faire également à partir du portail Azure.   Dans l’écran d’accueil du Portail Azure, sélectionnez **Log Analytics** puis, dans le panneau **Log Analytics**, sélectionnez l’espace de travail et cliquez sur **Supprimer** dans le menu du panneau des paramètres de l’espace de travail.  
      
## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la façon de construire différentes requêtes de recherche et sur la manière de consulter les journaux de travaux Automation avec Log Analytics, consultez [Recherches de journal dans Log Analytics](../log-analytics/log-analytics-log-searches.md)
- Pour en savoir plus sur l’exécution d’un runbook, la manière de surveiller des tâches de runbook et d’autres détails techniques, consultez [Suivre une tâche de runbook](automation-runbook-execution.md)
- Pour en savoir plus sur OMS Log Analytics et sur les sources de collecte de données, consultez [Collecting Azure storage data in Log Analytics overview](../log-analytics/log-analytics-azure-storage.md)






   




<!--HONumber=Feb17_HO3-->


