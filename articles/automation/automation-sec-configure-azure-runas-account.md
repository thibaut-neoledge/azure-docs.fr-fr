---
title: "Configurer un compte d’authentification Azure | Microsoft Docs"
description: "Ce didacticiel vous guide tout au long des procédures de création et de test d’une authentification de principal de sécurité dans Azure Automation. Il est complété par un exemple d’utilisation."
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
keywords: nom du principal du service, setspn, authentification azure
ms.assetid: 2f783441-15c7-4ea0-ba27-d7daa39b1dd3
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/24/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 6966befa56dc6a0feff4b8a821bde4e423a2b53a
ms.openlocfilehash: 97853ce9f78078cc6bbccdfb5c5a06cae49e218c
ms.lasthandoff: 02/24/2017


---
# <a name="authenticate-runbooks-with-azure-run-as-account"></a>Authentifier des Runbooks avec un compte d’identification Azure
Cette rubrique vous décrit la configuration d’un compte Automation à partir du portail Azure à l’aide de la fonctionnalité Compte d’identification qui permet d’authentifier les Runbooks gérant les ressources dans Azure Resource Manager ou dans la gestion des services Azure.

Lorsque vous créez un compte Automation dans le portail Azure, il crée automatiquement :

* Un compte d’identification, qui crée un principal du service dans Azure Active Directory, un certificat, et attribue le contrôle d’accès en fonction du rôle (RBAC) Collaborateur, qui sera utilisé pour gérer les ressources Resource Manager à l’aide de Runbooks.   
* Un compte d’identification Classic en chargeant un certificat de gestion, qui sera utilisé pour gérer les ressources de gestion des services Azure ou les ressources classiques à l’aide de Runbooks.  

Le processus s’en trouve ainsi simplifié, et vous êtes en mesure de commencer rapidement à générer et déployer des Runbooks pour répondre à vos besoins d’automatisation.      

À l’aide d’un compte d’identification standard ou Classic, vous pouvez :

* Bénéficier d’une méthode d’authentification Azure standardisée lorsque vous gérez des ressources Azure Resource Manager ou des ressources des services de gestion Azure à partir de Runbooks dans le portail Azure.  
* Automatiser l’utilisation de Runbooks globaux configurés dans les alertes Azure.

> [!NOTE]
> La [fonctionnalité d’intégration d’alertes](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) Azure associée aux Runbooks globaux requiert un compte Automation configuré avec un compte d’identification standard ou Classic. Vous pouvez sélectionner un compte Automation pour lequel un compte d’identification standard ou Classic est déjà défini ou choisir d’en créer un.
>  

Nous allons vous montrer comment créer un compte Automation à partir du portail Azure, le mettre à jour à l’aide de PowerShell, gérer la configuration du compte et vous authentifier dans vos Runbooks.

Avant de poursuivre, voici quelques points que vous devez comprendre et prendre en considération.

1. Cela n’affecte pas les comptes Automation existants déjà créés dans le modèle de déploiement classique ou Resource Manager.  
2. Cela fonctionne uniquement pour les comptes Automation créés via le portail Azure.  Si vous tentez de créer un compte à partir du portail Classic, la configuration du compte d’identification ne sera pas répliquée.
3. Si vous disposez actuellement de Runbooks et de ressources (c’est-à-dire des planifications, des variables, etc.) créés précédemment pour gérer les ressources classiques, et que vous souhaitez que ces Runbooks soient authentifiés avec le nouveau compte d’identification Classic, vous devez créer un compte d’identification classique à l’aide de Gérer un compte d’identification classique ou mettre à jour votre compte existant à l’aide du script PowerShell indiqué ci-après.  
4. Pour vous authentifier à l’aide du nouveau compte d’identification ou du compte Automation d’identification Classic, vous devez modifier les Runbooks existants avec l’exemple de code indiqué ci-dessous.  **Notez** que le compte d’identification est destiné à une authentification auprès des ressources Resource Manager à l’aide du principal du service basé sur les certificats, et que le compte d’identification Classic est destiné à une authentification auprès des ressources de gestion des services avec le certificat de gestion.     

## <a name="create-a-new-automation-account-from-the-azure-portal"></a>Créer un compte Automation à partir du portail Azure
Dans cette section, vous allez suivre une procédure qui vous permettra de créer un compte Azure Automation à partir du portail Azure.  Cette procédure permet de créer aussi bien un compte d’identification standard qu’un compte d’identification Classic.  

> [!NOTE]
> Pour effectuer cette procédure, vous devez posséder le rôle Administrateurs de services ou être coadministrateur de l’abonnement, ce qui permet d’accéder à l’abonnement de l’utilisateur. Vous devez également ajouter l’utilisateur en tant qu’utilisateur au domaine Active Directory par défaut de cet abonnement. Il est inutile d’attribuer le compte à un rôle privilégié. Les utilisateurs qui ne sont pas membres du domaine Active Directory de l’abonnement avant de recevoir le rôle Coadministrateur de l’abonnement seront ajoutés à Active Directory en tant qu’invités et verront l’avertissement « Vous ne disposez pas des autorisations nécessaires pour créer... » dans le panneau **Ajouter un compte Automation**. Les utilisateurs qui ont d’abord reçu le rôle Coadministrateur peuvent être supprimés des abonnements Active Directory puis rajoutés pour en faire des utilisateurs complets dans Active Directory. Pour vérifier si tel est le cas, dans le volet **Azure Active Directory** du portail Azure, sélectionnez **Utilisateurs et groupes** et **Tous les utilisateurs**, choisissez l’utilisateur concerné, puis sélectionnez **Profil**.  La valeur de l’attribut **Type d’utilisateur** sous le profil de l’utilisateur ne doit pas être **Invité**.  
> 

1. Connectez-vous au portail Azure avec un compte membre du rôle Administrateurs des abonnements et coadministrateur de l’abonnement.
2. Sélectionnez **Comptes Automation**.
3. Dans le panneau Comptes Automation, cliquez sur **Ajouter**.<br>![Ajouter un compte Automation](media/automation-sec-configure-azure-runas-account/create-automation-account-properties-b.png)
   
   > [!NOTE]
   > Si l’avertissement suivant s’affiche dans le panneau **Ajouter un compte Automation**, cela signifie que votre compte n’est ni membre du rôle Administrateurs des abonnements ni coadministrateur de l’abonnement.<br>![Avertissement Ajouter un compte Automation](media/automation-sec-configure-azure-runas-account/create-account-without-perms.png)
   > 
   > 
4. Dans le panneau **Ajouter un compte Automation**, entrez le nom de votre nouveau compte Automation dans la zone **Nom**.
5. Si vous disposez de plusieurs abonnements, spécifiez-en un pour le nouveau compte, ainsi qu’un **groupe de ressources** nouveau ou existant et un **emplacement** de centre de données Azure.
6. Vérifiez que l’option **Créer un compte d’identification Azure** est bien définie sur la valeur **Oui**, puis cliquez sur le bouton **Créer**.  
   
   > [!NOTE]
   > Si vous choisissez de ne pas créer de compte d’identification en sélectionnant l’option **Non**, un message d’avertissement s’affiche dans le panneau **Ajouter un compte Automation**.  Même si le compte est créé dans le portail Azure, il n’aura pas d’identité d’authentification correspondante au sein de votre service d’annuaire des abonnements classique ou Resource Manager et n’aura donc pas accès aux ressources de votre abonnement.  Cela empêche tous les Runbooks faisant référence à ce compte de pouvoir s’authentifier et d’effectuer des tâches sur les ressources de ces modèles de déploiement.
   > 
   > ![Avertissement Ajouter un compte Automation](media/automation-sec-configure-azure-runas-account/create-account-decline-create-runas-msg.png)<br>
   > Si le principal du service n’est pas créé, le rôle Collaborateur n’est pas attribué.
   > 

7. Pour suivre la progression de la création du compte Automation, accédez à l’onglet **Notifications** du menu.

### <a name="resources-included"></a>Ressources incluses
Une fois le compte Automation créé, plusieurs ressources vous sont automatiquement créées.  Le tableau ci-dessous récapitule les ressources du compte d’identification.<br>

| Ressource | Description |
| --- | --- |
| Runbook AzureAutomationTutorial |Exemple de Runbook Graphical qui illustre l’authentification à l’aide du compte d’identification et l’accès à l’ensemble des ressources Resource Manager. |
| Runbook AzureAutomationTutorialScript |Exemple de Runbook PowerShell qui illustre l’authentification à l’aide du compte d’identification et l’accès à l’ensemble des ressources Resource Manager. |
| AzureRunAsCertificate |Ressource de certificat créée automatiquement lors de la création du compte Automation ou à l’aide du script PowerShell ci-dessous pour un compte existant.  Elle vous permet de vous authentifier auprès d’Azure afin de pouvoir gérer les ressources Azure Resource Manager des Runbooks.  Ce certificat a une durée de vie d’un an. |
| AzureRunAsConnection |Ressource de connexion créée automatiquement lors de la création du compte Automation ou à l’aide du script PowerShell ci-dessous pour un compte existant. |

Le tableau ci-dessous récapitule les ressources du compte d’identification Classic.<br>

| Ressource | Description |
| --- | --- |
| Runbook AzureClassicAutomationTutorial |Exemple de Runbook Graphical qui accède à toutes les machines virtuelles Classic d’un abonnement à l’aide du compte d’identification Classic (certificat), puis indique leur nom et état. |
| Runbook AzureClassicAutomationTutorialScript |Exemple de Runbook PowerShell qui accède à toutes les machines virtuelles Classic d’un abonnement à l’aide du compte d’identification Classic (certificat), puis indique leur nom et état. |
| AzureClassicRunAsCertificate |Ressource de certificat créée automatiquement, qui est utilisée pour l’authentification auprès d’Azure afin que vous puissiez gérer les ressources Azure Classic des Runbooks.  Ce certificat a une durée de vie d’un an. |
| AzureClassicRunAsConnection |Ressource de connexion créée automatiquement, qui est utilisée pour l’authentification auprès d’Azure afin que vous puissiez gérer les ressources Azure Classic des Runbooks. |

## <a name="verify-run-as-authentication"></a>Vérifier l’authentification d’identification
Nous allons à présent effectuer un test rapide afin de vérifier que vous êtes en mesure de vous authentifier à l’aide du nouveau compte d’identification.     

1. Dans le portail Azure, ouvrez le compte Automation que vous avez créé.  
2. Cliquez sur la vignette **Runbooks** pour ouvrir la liste des runbooks.
3. Sélectionnez le Runbook **AzureAutomationTutorialScript**, puis cliquez sur **Démarrer** pour le lancer.  Vous allez recevoir une invite de confirmation de démarrage du Runbook.
4. Un [travail du Runbook](automation-runbook-execution.md) est créé. Le panneau Travail s’affiche alors et l’état du travail est affiché dans la mosaïque **Résumé du travail**.  
5. L’état initial du travail est *Mis en file d’attente* pour indiquer qu’il attend la disponibilité d’un Runbook Worker dans le cloud. La tâche prend ensuite l’état *Démarrage en cours* lorsqu’un Worker sélectionne la tâche, puis l’état *En cours d’exécution* lorsque le Runbook commence à s’exécuter.  
6. À l’issue du travail du Runbook, l’état **Terminé** doit s’afficher.<br> ![Test de Runbook du principal de sécurité](media/automation-sec-configure-azure-runas-account/job-summary-automationtutorialscript.png)<br>
7. Pour afficher les résultats détaillés du Runbook, cliquez sur la mosaïque **Sortie** .
8. Le panneau **Sortie** doit indiquer qu’il a authentifié et retourné la liste de toutes les ressources disponibles dans le groupe de ressources.
9. Fermez le panneau **Sortie** pour revenir au panneau **Résumé du travail**.
10. Fermez le panneau **Résumé du travail** et le panneau du Runbook **AzureAutomationTutorialScript** correspondant.

## <a name="verify-classic-run-as-authentication"></a>Vérifier l’authentification d’identification Classic
Nous allons à présent effectuer un test rapide afin de vérifier que vous êtes en mesure de vous authentifier à l’aide du nouveau compte d’identification Classic.     

1. Dans le portail Azure, ouvrez le compte Automation que vous avez créé.  
2. Cliquez sur la vignette **Runbooks** pour ouvrir la liste des runbooks.
3. Sélectionnez le Runbook **AzureClassicAutomationTutorialScript**, puis cliquez sur **Démarrer** pour le lancer.  Vous allez recevoir une invite de confirmation de démarrage du Runbook.
4. Un [travail du Runbook](automation-runbook-execution.md) est créé. Le panneau Travail s’affiche alors et l’état du travail est affiché dans la mosaïque **Résumé du travail**.  
5. L’état initial du travail est *Mis en file d’attente* pour indiquer qu’il attend la disponibilité d’un Runbook Worker dans le cloud. La tâche prend ensuite l’état *Démarrage en cours* lorsqu’un Worker sélectionne la tâche, puis l’état *En cours d’exécution* lorsque le Runbook commence à s’exécuter.  
6. À l’issue du travail du Runbook, l’état **Terminé** doit s’afficher.<br> ![Test de Runbook du principal de sécurité](media/automation-sec-configure-azure-runas-account/job-summary-automationclassictutorialscript.png)<br>
7. Pour afficher les résultats détaillés du Runbook, cliquez sur la mosaïque **Sortie** .
8. Le panneau **Sortie** doit indiquer qu’il a authentifié et retourné la liste de toutes les machines virtuelles Classic de l’abonnement.
9. Fermez le panneau **Sortie** pour revenir au panneau **Résumé du travail**.
10. Fermez le panneau **Résumé du travail** et le panneau du Runbook **AzureClassicAutomationTutorialScript** correspondant.

## <a name="managing-azure-run-as-account"></a>Gestion d’un compte d’identification Azure
Pendant la durée de vie de votre compte Automation, vous devez renouveler le certificat avant qu’il n’arrive à expiration ou si vous pensez que le compte a été compromis, vous pouvez supprimer le compte d’identification et le recréer.  Cette section fournit les étapes pour effectuer ces opérations.  

### <a name="certificate-renewal"></a>Renouvellement du certificat
Le certificat créé pour le compte d’identification Azure peut être renouvelé à tout moment, jusqu'à ce qu’il arrive à expiration, ce qui correspond au délai d’une année à partir de la date de création.  Lorsque vous le renouvelez, l’ancien certificat valide est conservé afin de garantir que les Runbooks en file d’attente ou en cours d’exécution, qui s’authentifient avec le compte d’identification, ne sont pas affectés.  Le certificat continue d’exister jusqu’à son expiration.     

1. Dans le portail Azure, ouvrez le compte Automation.  
2. Dans le panneau du compte Automation, dans le panneau des propriétés du compte, sélectionnez **Comptes d’identification** sous la section **Paramètres de compte**.<br><br> ![Panneau des propriétés du compte Automation](media/automation-sec-configure-azure-runas-account/automation-account-properties-pane.png)<br><br>
3. Dans le panneau des propriétés des **Comptes d’identification**, sélectionnez le compte d’identification ou le compte d’identification classique pour lequel vous souhaitez renouveler le certificat, puis dans le panneau des propriétés du compte sélectionné, cliquez sur **Renouveler le certificat**.<br><br> ![Renouveler le certificat pour le compte d’identification](media/automation-sec-configure-azure-runas-account/automation-account-renew-runas-certificate.png)<br><br> Vous recevez une invite de confirmation de la suppression.  
4. Pour suivre la progression du renouvellement du certificat, accédez à l’onglet **Notifications** du menu.

### <a name="delete-run-as-account"></a>Supprimer un compte d’identification
Les étapes suivantes décrivent comment supprimer et recréer votre compte d’identification ou compte d’identification classique Azure.  Lorsque vous effectuez cette opération, le compte Automation est conservé.  Après avoir supprimé le compte d’identification ou le compte d’identification classique, vous pouvez le recréer dans le portail.  

1. Dans le portail Azure, ouvrez le compte Automation.  
2. Dans le panneau du compte Automation, dans le panneau des propriétés du compte, sélectionnez **Comptes d’identification** sous la section **Paramètres de compte**.
3. Dans le panneau des propriétés des **Comptes d’identification**, sélectionnez le compte d’identification ou le compte d’identification classique à supprimer, puis dans le panneau des propriétés du compte sélectionné, cliquez sur **Supprimer**.<br><br> ![Supprimer un compte d’identification](media/automation-sec-configure-azure-runas-account/automation-account-delete-runas.png)<br><br>  Vous recevez une invite de confirmation de la suppression.
4. Pour suivre la progression de la suppression du compte, accédez à l’onglet **Notifications** du menu.  Une fois la suppression terminée, vous pouvez recréer le compte à partir du panneau des propriétés **Comptes d’identification** et en sélectionnant l’option de création **Compte d’identification Azure**.<br><br> ![Recréer le compte d’identification Automation](media/automation-sec-configure-azure-runas-account/automation-account-create-runas.png)<br> 

### <a name="misconfiguration"></a>Configuration incorrecte
Si l’un des éléments de configuration nécessaires pour que le compte d’identification ou le compte d’identification classique fonctionne correctement est supprimé ou n’a pas été créé correctement lors de l’installation initiale, par exemple :

* Ressource de certificat 
* Ressource de connexion 
* Compte d’identification supprimé du rôle Contributeur
* Principal du service ou application dans Azure AD

Automation détecte ces modifications et affiche une notification avec l’état **Incomplet** dans le panneau des propriétés **Comptes d’identification** du compte.<br><br> ![Message d’état Incomplet pour la configuration du compte d’identification](media/automation-sec-configure-azure-runas-account/automation-account-runas-incomplete-config.png)<br><br>Lorsque vous sélectionnez le compte d’identification, l’avertissement suivant s’affiche dans le volet des propriétés du compte :<br><br> ![Message d’avertissement Incomplet pour la configuration du compte d’identification](media/automation-sec-configure-azure-runas-account/automation-account-runas-incomplete-config-msg.png).<br>  
Si votre compte d’identification est mal configuré, vous pouvez rapidement résoudre ce problème en supprimant et recréant le compte d’identification.   

## <a name="update-an-automation-account-using-powershell"></a>Mise à jour d’un compte Automation à l’aide de PowerShell
Nous vous permettons ici d’utiliser PowerShell pour mettre à jour votre compte Automation existant si :

1. Vous avez créé un compte Automation sans compte d’identification.
2. Vous devez créer un compte Automation dans le cloud Azure Government
3. Vous possédez déjà un compte Automation pour gérer les ressources Resource Manager et vous voulez le mettre à jour pour inclure le compte d’identification pour l’authentification du Runbook.
4. Vous possédez déjà un compte Automation pour gérer les ressources classiques et souhaitez le mettre à jour pour utiliser le compte d’identification Classic au lieu de créer un compte et d’y migrer vos Runbooks et ressources.   

Avant de continuer, vérifiez que :

1. Ce script prend en charge l’exécution uniquement sur Windows 10 et Windows Server 2016 avec les modules Azure Resource Manager 2.01 et versions ultérieures.  Il n’est pas pris en charge dans les versions antérieures de Windows.  
2. Azure PowerShell 1.0 et versions ultérieures. Pour plus d’informations sur cette version et sur la méthode d’installation, voir l’article [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs).
3. Vous avez créé un compte Automation.  Ce compte sera référencé en tant que valeur des paramètres –AutomationAccountName et –ApplicationDisplayName dans les deux scripts ci-dessous.

Pour obtenir les valeurs des paramètres *SubscriptionID*, *ResourceGroup* et *AutomationAccountName*, qui sont requis pour les scripts, dans le portail Azure, sélectionnez votre compte Automation dans le panneau **Compte Automation** et sélectionnez **Tous les paramètres**.  Dans le panneau **Tous les paramètres**, sous **Paramètres du compte**, sélectionnez **Propriétés**.  Dans le panneau **Propriétés**, vous pouvez observer ces valeurs.<br><br> ![Propriétés du compte Automation](media/automation-sec-configure-azure-runas-account/automation-account-properties.png)  

### <a name="create-run-as-account-powershell-script"></a>Créer le script PowerShell d’un compte d’identification
Le script PowerShell indiqué ci-dessous configure les éléments suivants :

* Une application Azure AD authentifiée avec un certificat auto-signé, la création d’un compte de principal du service pour cette application dans Azure AD et l’affectation du rôle de contributeur (que vous pouvez remplacer par un rôle de propriétaire ou par tout autre rôle) pour ce compte dans votre abonnement actuel.  Pour plus d’informations, consultez l’article [Contrôle d’accès en fonction du rôle dans Azure Automation](automation-role-based-access-control.md) .
* Une ressource de certificat Automation dans le compte Automation spécifié nommée **AzureRunAsCertificate**, qui contient le certificat utilisé dans le principal du service.
* Une ressource de connexion Automation dans le compte Automation spécifié nommée **AzureRunAsConnection**, qui contient l’ID d’application, l’ID de locataire, l’ID d’abonnement et l’empreinte du certificat.    

Les étapes ci-dessous vous guident tout au long de l’exécution du script.

1. Enregistrez le script suivant sur votre ordinateur.  Dans cet exemple, enregistrez-le sous le nom de fichier **New-AzureServicePrincipal.ps1**.  
   
        #Requires -RunAsAdministrator
        Param (
        [Parameter(Mandatory=$true)]
        [String] $ResourceGroup,
   
        [Parameter(Mandatory=$true)]
        [String] $AutomationAccountName,
   
        [Parameter(Mandatory=$true)]
        [String] $ApplicationDisplayName,
   
        [Parameter(Mandatory=$true)]
        [String] $SubscriptionId,
   
        [Parameter(Mandatory=$true)]
        [String] $CertPlainPassword,
   
        [Parameter(Mandatory=$false)]
        [int] $NoOfMonthsUntilExpired = 12

        [Parameter(Mandatory=$True)]
        [ValidateSet("AzureCloud","AzureUSGovernment")]
        [string]$Environment="AzureCloud"
        )
   
        #Check to see which cloud environment to sign into.
        Switch ($Environment)
        {
          "AzureCloud" {Login-AzureRmAccount}
          "AzureUSGovernment" {Login-AzureRmAccount -EnvironmentName AzureUSGovernment} 
        }
        Import-Module AzureRM.Resources
        Select-AzureRmSubscription -SubscriptionId $SubscriptionId
   
        $CurrentDate = Get-Date
        $EndDate = $CurrentDate.AddMonths($NoOfMonthsUntilExpired)
        $KeyId = (New-Guid).Guid
        $CertPath = Join-Path $env:TEMP ($ApplicationDisplayName + ".pfx")
   
        $Cert = New-SelfSignedCertificate -DnsName $ApplicationDisplayName -CertStoreLocation cert:\LocalMachine\My -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider"
   
        $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $CertPath -Password $CertPassword -Force | Write-Verbose
   
        $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate -ArgumentList @($CertPath, $CertPlainPassword)
        $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())
   
        $KeyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
        $KeyCredential.StartDate = $CurrentDate
        $KeyCredential.EndDate= $EndDate
        $KeyCredential.KeyId = $KeyId
        #$KeyCredential.Type = "AsymmetricX509Cert"
        #$KeyCredential.Usage = "Verify"
        $KeyCredential.CertValue = $KeyValue
   
        # Use Key credentials
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $ApplicationDisplayName) -IdentifierUris ("http://" + $KeyId) -KeyCredentials $keyCredential
   
        New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId | Write-Verbose
        Get-AzureRmADServicePrincipal | Where {$_.ApplicationId -eq $Application.ApplicationId} | Write-Verbose
   
        $NewRole = $null
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6)
        {
           # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
           Sleep 5
           New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
           Sleep 10
           $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
           $Retries++;
        }
   
        # Get the tenant id for this subscription
        $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
        $TenantID = $SubscriptionInfo | Select TenantId -First 1
   
        # Create the automation resources
        New-AzureRmAutomationCertificate -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Path $CertPath -Name AzureRunAsCertificate -Password $CertPassword -Exportable | write-verbose
   
        # Create a Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        $ConnectionAssetName = "AzureRunAsConnection"
        Remove-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -Force -ErrorAction SilentlyContinue
        $ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues

2. Sur votre ordinateur, démarrez **Windows PowerShell** dans l’écran **Démarrer** avec des droits de l’utilisateur élevés.
3. À partir de l’interpréteur de commandes en ligne de commande PowerShell avec élévation de privilèges, accédez au dossier qui contient le script créé à l’étape 1 et exécutez-le en modifiant les valeurs des paramètres *–ResourceGroup*, *-AutomationAccountName*, *-ApplicationDisplayName*, *-SubscriptionId*, *-CertPlainPassword* et *-Environment*.<br>
   
   > [!NOTE]
   > Une fois le script exécuté, vous êtes invité à vous authentifier auprès d’Azure. Vous devez vous connecter avec un compte membre du rôle Administrateurs des abonnements et coadministrateur de l’abonnement.
   > 
   > 
   
        .\New-AzureServicePrincipal.ps1 -ResourceGroup <ResourceGroupName>
        -AutomationAccountName <NameofAutomationAccount> `
        -ApplicationDisplayName <DisplayNameofAutomationAccount> `
        -SubscriptionId <SubscriptionId> `
        -CertPlainPassword "<StrongPassword>" -Environment <valid values are AzureCloud or AzureUSGovernment>  
   <br>

Une fois le script exécuté, reportez-vous à [l’exemple de code](#sample-code-to-authenticate-with-resource-manager-resources) ci-dessous pour vous authentifier auprès des ressources Resource Manager et valider la configuration des informations d’identification.

### <a name="create-classic-run-as-account-powershell-script"></a>Créer le script PowerShell d’un compte d’identification Classic
Le script PowerShell indiqué ci-dessous configure les éléments suivants :

* Une ressource de certificat Automation dans le compte Automation spécifié nommée **AzureClassicRunAsCertificate**, qui contient le certificat utilisé pour l’authentification de vos Runbooks.
* Une ressource de connexion Automation dans le compte Automation spécifié nommée **AzureClassicRunAsConnection**, qui contient le nom de l’abonnement, l’ID d’abonnement et le nom de la ressource de certificat.

Le script crée un certificat de gestion auto-signé et l’enregistre dans le dossier de fichiers temporaires sur votre ordinateur, sous le profil utilisateur utilisé pour exécuter la session PowerShell : *%USERPROFILE%\AppData\Local\Temp*.  Après l’exécution du script, vous devez charger le certificat de gestion Azure dans le magasin de gestion de l’abonnement dans lequel le compte Automation a été créé.  Les étapes ci-dessous vous guident tout au long de l’exécution du script et du chargement du certificat.  

1. Enregistrez le script suivant sur votre ordinateur.  Dans cet exemple, enregistrez-le sous le nom de fichier **New-AzureClassicRunAsAccount.ps1**.
   
        #Requires -RunAsAdministrator
        Param (
        [Parameter(Mandatory=$true)]
        [String] $ResourceGroup,
   
        [Parameter(Mandatory=$true)]
        [String] $AutomationAccountName,
   
        [Parameter(Mandatory=$true)]
        [String] $ApplicationDisplayName,
   
        [Parameter(Mandatory=$true)]
        [String] $SubscriptionId,
   
        [Parameter(Mandatory=$true)]
        [String] $CertPlainPassword,
   
        [Parameter(Mandatory=$false)]
        [int] $NoOfMonthsUntilExpired = 12
        )
   
        Login-AzureRmAccount
        Import-Module AzureRM.Resources
        $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId
        $SubscriptionName = $subscription.Subscription.SubscriptionName
   
        $CurrentDate = Get-Date
        $EndDate = $CurrentDate.AddMonths($NoOfMonthsUntilExpired)
        $KeyId = (New-Guid).Guid
        $CertPath = Join-Path $env:TEMP ($ApplicationDisplayName + ".pfx")
        $CertPathCer = Join-Path $env:TEMP ($ApplicationDisplayName + ".cer")
   
        $Cert = New-SelfSignedCertificate -DnsName $ApplicationDisplayName -CertStoreLocation cert:\LocalMachine\My -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider"
   
        $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $CertPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $CertPathCer -Type CERT | Write-Verbose
   
        # Create the automation resources
        $ClassicCertificateAssetName = "AzureClassicRunAsCertificate"
        New-AzureRmAutomationCertificate -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Path $CertPath -Name $ClassicCertificateAssetName  -Password $CertPassword -Exportable | write-verbose
   
        # Create a Automation connection asset named AzureClassicRunAsConnection in the Automation account. This connection uses the ClassicCertificateAssetName.
        $ConnectionAssetName = "AzureClassicRunAsConnection"
        Remove-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -Force -ErrorAction SilentlyContinue
        $ConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicCertificateAssetName}
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureClassicCertificate -ConnectionFieldValues $ConnectionFieldValues
   
        Write-Host -ForegroundColor red "Please upload the cert $CertPathCer to the Management store by following the steps below."
        Write-Host -ForegroundColor red "Log in to the Microsoft Azure Management portal (https://manage.windowsazure.com) and select Settings -> Management Certificates."
        Write-Host -ForegroundColor red "Then click Upload and upload the certificate $CertPathCer"

2. Sur votre ordinateur, démarrez **Windows PowerShell** dans l’écran **Démarrer** avec des droits de l’utilisateur élevés.  
3. À partir de l’interpréteur de commandes en ligne de commande PowerShell avec élévation de privilèges, accédez au dossier qui contient le script créé à l’étape 1 et exécutez-le en modifiant les valeurs des paramètres *–ResourceGroup*, *-AutomationAccountName*, *-ApplicationDisplayName*, *-SubscriptionId* et *-CertPlainPassword*.<br>
   
   > [!NOTE]
   > Une fois le script exécuté, vous êtes invité à vous authentifier auprès d’Azure. Vous devez vous connecter avec un compte membre du rôle Administrateurs des abonnements et coadministrateur de l’abonnement.
   > 
   > 
   
        .\New-AzureClassicRunAsAccount.ps1 -ResourceGroup <ResourceGroupName>
        -AutomationAccountName <NameofAutomationAccount> `
        -ApplicationDisplayName <DisplayNameofAutomationAccount> `
        -SubscriptionId <SubscriptionId> `
        -CertPlainPassword "<StrongPassword>"

Une fois le script exécuté, vous devez copier le certificat créé dans le dossier **Temp** de votre profil utilisateur.  Suivez les étapes pour [charger un certificat d’API de gestion](../azure-api-management-certs.md) vers le portail Azure Classic, puis reportez-vous à l’[exemple de code](#sample-code-to-authenticate-with-service-management-resources) pour valider la configuration des informations d’identification avec les ressources de gestion des services.

## <a name="sample-code-to-authenticate-with-resource-manager-resources"></a>Exemple de code pour l’authentification avec des ressources Azure Resource Manager
Vous pouvez utiliser l’exemple de code mis à jour ci-dessous, extrait de l’exemple de Runbook **AzureAutomationTutorialScript** , pour procéder à une authentification avec le compte d’identification pour gérer les ressources Azure Resource Manager avec vos Runbooks.   

    $connectionName = "AzureRunAsConnection"
    $SubId = Get-AutomationVariable -Name 'SubscriptionId'
    try
    {
       # Get the connection "AzureRunAsConnection "
       $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

       "Signing in to Azure..."
       Add-AzureRmAccount `
         -ServicePrincipal `
         -TenantId $servicePrincipalConnection.TenantId `
         -ApplicationId $servicePrincipalConnection.ApplicationId `
         -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
       "Setting context to a specific subscription"     
       Set-AzureRmContext -SubscriptionId $SubId              
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


Le script inclut deux lignes de code supplémentaires pour prendre en charge le référencement d’un contexte d’abonnement ; le travail entre plusieurs abonnements est ainsi facilité. Une ressource de variable nommée SubscriptionId contient l’ID de l’abonnement et, suite à l’instruction de l’applet de commande Add-AzureRmAccount, [l’applet de commande Set-AzureRmContext](https://msdn.microsoft.com/library/mt619263.aspx) est indiquée avec le jeu de paramètres *-SubscriptionId*. Si le nom de la variable est trop générique, vous pouvez modifier le nom de la variable afin que celui-ci comprenne un préfixe ou une autre convention d’affectation de noms pour faciliter son identification. Vous pouvez également utiliser le jeu de paramètres -SubscriptionName au lieu de -SubscriptionId avec la ressource de variable correspondante.    

Notez que l’applet de commande utilisée pour l’authentification ( **Add-AzureRmAccount**) dans le Runbook, utilise le jeu de paramètres *ServicePrincipalCertificate* .  Elle effectue l’authentification à l’aide du certificat du principal du service et non des informations d’identification.  

## <a name="sample-code-to-authenticate-with-service-management-resources"></a>Exemple de code pour l’authentification avec les ressources de gestion des services
Vous pouvez utiliser l’exemple de code mis à jour ci-dessous, extrait de l’exemple de Runbook **AzureClassicAutomationTutorialScript** , pour procéder à une authentification avec le compte d’identification Classic pour gérer les ressources classiques avec vos Runbooks.

    $ConnectionAssetName = "AzureClassicRunAsConnection"
    # Get the connection
    $connection = Get-AutomationConnection -Name $connectionAssetName        

    # Authenticate to Azure with certificate
    Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
    if ($Conn -eq $null)
    {
       throw "Could not retrieve connection asset: $ConnectionAssetName. Assure that this asset exists in the Automation account."
    }

    $CertificateAssetName = $Conn.CertificateAssetName
    Write-Verbose "Getting the certificate: $CertificateAssetName" -Verbose
    $AzureCert = Get-AutomationCertificate -Name $CertificateAssetName
    if ($AzureCert -eq $null)
    {
       throw "Could not retrieve certificate asset: $CertificateAssetName. Assure that this asset exists in the Automation account."
    }

    Write-Verbose "Authenticating to Azure with certificate." -Verbose
    Set-AzureSubscription -SubscriptionName $Conn.SubscriptionName -SubscriptionId $Conn.SubscriptionID -Certificate $AzureCert
    Select-AzureSubscription -SubscriptionId $Conn.SubscriptionID


## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur les principaux de service, consultez l’article [Objets Principal du service et Application](../active-directory/active-directory-application-objects.md).
* Pour plus d’informations sur le contrôle d’accès en fonction du rôle dans Azure Automation, reportez-vous à l’article [Contrôle d’accès en fonction du rôle dans Azure Automation](automation-role-based-access-control.md).
* Pour plus d’informations sur les certificats et les services Azure, consultez la page [Vue d’ensemble des certificats pour Azure Cloud Services](../cloud-services/cloud-services-certs-create.md)


