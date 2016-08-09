<properties
    pageTitle="Configurer un compte d’authentification Azure | Microsoft Azure"
    description="Ce didacticiel vous guide tout au long des procédures de création et de test d’une authentification de principal de sécurité dans Azure Automation. Il est complété par un exemple d’utilisation."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""
	keywords="nom du principal du service, setspn, authentification azure"/>
<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/29/2016"
    ms.author="magoedte"/>

# Authentifier des Runbooks avec un compte d’identification Azure

Cette rubrique vous décrit la configuration d’un compte Automation à partir du portail Azure à l’aide de la fonctionnalité Compte d’identification qui permet d’authentifier les Runbooks gérant les ressources dans Azure Resource Manager ou dans la gestion des services Azure.

Lorsque vous créez un compte Automation dans le portail Azure, il crée automatiquement :

- Un compte d’identification, qui crée un principal du service dans Azure Active Directory, un certificat, et attribue le contrôle d’accès en fonction du rôle (RBAC) Collaborateur, qui sera utilisé pour gérer les ressources Resource Manager à l’aide de Runbooks.
- Un compte d’identification Classic en chargeant un certificat de gestion, qui sera utilisé pour gérer les ressources de gestion des services Azure ou les ressources classiques à l’aide de Runbooks.

Le processus s’en trouve ainsi simplifié, et vous êtes en mesure de commencer rapidement à générer et déployer des Runbooks pour répondre à vos besoins d’automatisation.

À l’aide d’un compte d’identification standard ou Classic, vous pouvez :

- Bénéficier d’une méthode d’authentification Azure standardisée lorsque vous gérez des ressources Azure Resource Manager ou des ressources des services de gestion Azure à partir de Runbooks dans le portail Azure.
- Automatiser l’utilisation de Runbooks globaux configurés dans les alertes Azure


>[AZURE.NOTE] La [fonctionnalité d’intégration d’alertes](../azure-portal/insights-receive-alert-notifications.md) Azure associée aux Runbooks globaux requiert un compte Automation configuré avec un compte d’identification standard ou Classic. Vous pouvez sélectionner un compte Automation pour lequel un compte d’identification standard ou Classic est déjà défini ou choisir d’en créer un.

Nous allons vous montrer comment créer un compte Automation à partir du portail Azure, le mettre à jour à l’aide de PowerShell et vous authentifier dans vos Runbooks.

Avant de poursuivre, voici quelques points que vous devez comprendre et prendre en considération.

1. Cela n’affecte pas les comptes Automation existants déjà créés dans le modèle de déploiement classique ou Resource Manager.
2. Cela fonctionne uniquement pour les comptes Automation créés via le portail Azure. Si vous tentez de créer un compte à partir du portail Classic, la configuration du compte d’identification ne sera pas répliquée.
3. Si vous disposez actuellement de Runbooks et de ressources (c’est-à-dire des planifications, des variables, etc.) créés précédemment pour gérer les ressources classiques, et que vous souhaitez que ces Runbooks soient authentifiés avec le nouveau compte d’identification Classic, vous devez les migrer vers le nouveau compte Automation ou mettre à jour votre compte existant à l’aide du script PowerShell indiqué ci-après.
4. Pour vous authentifier à l’aide du nouveau compte d’identification ou du compte Automation d’identification Classic, vous devez modifier les Runbooks existants avec l’exemple de code indiqué ci-dessous. **Notez** que le compte d’identification est destiné à une authentification auprès des ressources Resource Manager à l’aide du principal du service basé sur les certificats, et que le compte d’identification Classic est destiné à une authentification auprès des ressources de gestion des services avec le certificat de gestion.


## Créer un compte Automation à partir du portail Azure

Dans cette section, vous allez suivre une procédure qui vous permettra de créer un compte Azure Automation à partir du portail Azure. Cette procédure permet de créer aussi bien un compte d’identification standard qu’un compte d’identification Classic.

>[AZURE.NOTE] Pour effectuer cette procédure, vous *devez* être membre du rôle Administrateurs des abonnements et coadministrateur de l’abonnement, ce qui permet d’accéder à l’abonnement de l’utilisateur. Vous devez également ajouter l’utilisateur en tant qu’utilisateur au domaine Active Directory par défaut de cet abonnement. Il est inutile d’attribuer le compte à un rôle privilégié.

1. Connectez-vous au portail Azure avec un compte membre du rôle Administrateurs des abonnements et coadministrateur de l’abonnement.
2. Sélectionnez **Comptes Automation**.
3. Dans le panneau Comptes Automation, cliquez sur **Ajouter**.<br>![Ajouter un compte Automation](media/automation-sec-configure-azure-runas-account/create-automation-account-properties.png)

    >[AZURE.NOTE] Si l’avertissement suivant s’affiche dans le panneau **Ajouter un compte Automation**, cela signifie que votre compte n’est pas membre du rôle Administrateurs des abonnements ni coadministrateur de l’abonnement.<br>![Avertissement Ajouter un compte Automation](media/automation-sec-configure-azure-runas-account/create-account-without-perms.png)

4. Dans le panneau **Ajouter un compte Automation**, entrez le nom de votre nouveau compte Automation dans la zone **Nom**.
5. Si vous disposez de plusieurs abonnements, spécifiez-en un pour le nouveau compte, ainsi qu’un **groupe de ressources** nouveau ou existant et un **emplacement** de centre de données Azure.
6. Vérifiez que l’option **Créer un compte d’identification Azure** est bien définie sur la valeur **Oui**, puis cliquez sur le bouton **Créer**.

    >[AZURE.NOTE] Si vous choisissez de ne pas créer de compte d’identification en sélectionnant l’option **Non**, un message d’avertissement s’affiche dans le panneau **Ajouter un compte Automation**. Même si le compte est créé dans le portail Azure, il n’aura pas d’identité d’authentification correspondante au sein de votre service d’annuaire des abonnements classique ou Resource Manager et n’aura donc pas accès aux ressources de votre abonnement. Cela empêche tous les Runbooks faisant référence à ce compte de pouvoir s’authentifier et d’effectuer des tâches sur les ressources de ces modèles de déploiement.
    
    >![Avertissement Ajouter un compte Automation](media/automation-sec-configure-azure-runas-account/create-account-decline-create-runas-msg.png)<br> Si le principal du service n’est pas créé, le rôle Collaborateur n’est pas attribué.


7. Pour suivre la progression de la création du compte Automation, accédez à l’onglet **Notifications** du menu.

### Ressources incluses

Une fois le compte Automation créé, plusieurs ressources vous sont automatiquement créées. Le tableau ci-dessous récapitule les ressources du compte d’identification.<br>

Ressource|Description 
----|----
Runbook AzureAutomationTutorial|Exemple de Runbook PowerShell qui illustre l’authentification à l’aide du compte d’identification et l’accès à l’ensemble des ressources Resource Manager.
Runbook AzureAutomationTutorialScript|Exemple de Runbook PowerShell qui illustre l’authentification à l’aide du compte d’identification et l’accès à l’ensemble des ressources Resource Manager. 
AzureRunAsCertificate|Ressource de certificat créée automatiquement lors de la création du compte Automation ou à l’aide du script PowerShell ci-dessous pour un compte existant. Elle vous permet de vous authentifier auprès d’Azure afin de pouvoir gérer les ressources Azure Resource Manager des Runbooks. Ce certificat a une durée de vie d’un an. 
AzureRunAsConnection|Ressource de connexion créée automatiquement lors de la création du compte Automation ou à l’aide du script PowerShell ci-dessous pour un compte existant.

Le tableau ci-dessous récapitule les ressources du compte d’identification Classic.<br>

Ressource|Description 
----|----
Runbook AzureClassicAutomationTutorial|Exemple de Runbook qui accède à toutes les machines virtuelles Classic d’un abonnement à l’aide du compte d’identification Classic (certificat), puis indique leur nom et état.
Runbook AzureClassicAutomationTutorialScript|Exemple de Runbook qui accède à toutes les machines virtuelles Classic d’un abonnement à l’aide du compte d’identification Classic (certificat), puis indique leur nom et état.
AzureClassicRunAsCertificate|Ressource de certificat créée automatiquement, qui est utilisée pour l’authentification auprès d’Azure afin que vous puissiez gérer les ressources Azure Classic des Runbooks. Ce certificat a une durée de vie d’un an. 
AzureClassicRunAsConnection|Ressource de connexion créée automatiquement, qui est utilisée pour l’authentification auprès d’Azure afin que vous puissiez gérer les ressources Azure Classic des Runbooks.  

## Vérifier l’authentification d’identification

Nous allons à présent effectuer un test rapide afin de vérifier que vous êtes en mesure de vous authentifier à l’aide du nouveau compte d’identification.

1. Dans le portail Azure, ouvrez le compte Automation que vous avez créé.
2. Cliquez sur la vignette **Runbooks** pour ouvrir la liste des runbooks.
3. Sélectionnez le Runbook **AzureAutomationTutorialScript**, puis cliquez sur **Démarrer** pour le lancer. Vous allez recevoir une invite de confirmation de démarrage du Runbook.
4. Un [travail du Runbook](automation-runbook-execution.md) est créé. Le panneau Travail s’affiche alors et l’état du travail est affiché dans la mosaïque **Résumé du travail**.
5. L’état initial du travail est *Mis en file d’attente* pour indiquer qu’il attend la disponibilité d’un Runbook Worker dans le cloud. La tâche prend ensuite l’état *Démarrage en cours* lorsqu’un Worker sélectionne la tâche, puis l’état *En cours d’exécution* lorsque le Runbook commence à s’exécuter.
6. À l’issue du travail du Runbook, l’état **Terminé** doit s’afficher.<br> ![Test de Runbook du principal de sécurité](media/automation-sec-configure-azure-runas-account/job-summary-automationtutorialscript.png)<br>
7. Pour afficher les résultats détaillés du Runbook, cliquez sur la mosaïque **Sortie**.
8. Le panneau **Sortie** doit indiquer qu’il a authentifié et retourné la liste de toutes les ressources disponibles dans le groupe de ressources.
9. Fermez le panneau **Sortie** pour revenir au panneau **Résumé du travail**.
13. Fermez le panneau **Résumé du travail** et le panneau du Runbook **AzureAutomationTutorialScript** correspondant.

## Vérifier l’authentification d’identification Classic

Nous allons à présent effectuer un test rapide afin de vérifier que vous êtes en mesure de vous authentifier à l’aide du nouveau compte d’identification Classic.

1. Dans le portail Azure, ouvrez le compte Automation que vous avez créé.
2. Cliquez sur la vignette **Runbooks** pour ouvrir la liste des runbooks.
3. Sélectionnez le Runbook **AzureClassicAutomationTutorialScript**, puis cliquez sur **Démarrer** pour le lancer. Vous allez recevoir une invite de confirmation de démarrage du Runbook.
4. Un [travail du Runbook](automation-runbook-execution.md) est créé. Le panneau Travail s’affiche alors et l’état du travail est affiché dans la mosaïque **Résumé du travail**.
5. L’état initial du travail est *Mis en file d’attente* pour indiquer qu’il attend la disponibilité d’un Runbook Worker dans le cloud. La tâche prend ensuite l’état *Démarrage en cours* lorsqu’un Worker sélectionne la tâche, puis l’état *En cours d’exécution* lorsque le Runbook commence à s’exécuter.
6. À l’issue du travail du Runbook, l’état **Terminé** doit s’afficher.<br> ![Test de Runbook du principal de sécurité](media/automation-sec-configure-azure-runas-account/job-summary-automationclassictutorialscript.png)<br>
7. Pour afficher les résultats détaillés du Runbook, cliquez sur la mosaïque **Sortie**.
8. Le panneau **Sortie** doit indiquer qu’il a authentifié et retourné la liste de toutes les machines virtuelles Classic de l’abonnement.
9. Fermez le panneau **Sortie** pour revenir au panneau **Résumé du travail**.
13. Fermez le panneau **Résumé du travail** et le panneau du Runbook **AzureClassicAutomationTutorialScript** correspondant.

## Mise à jour d’un compte Automation à l’aide de PowerShell

Nous vous permettons ici d’utiliser PowerShell pour mettre à jour votre compte Automation existant si :

1. Vous avez créé un compte Automation sans compte d’identification.
2. Vous possédez déjà un compte Automation pour gérer les ressources Resource Manager et vous voulez le mettre à jour pour inclure le compte d’identification pour l’authentification du Runbook.
2. Vous possédez déjà un compte Automation pour gérer les ressources classiques et souhaitez le mettre à jour pour utiliser le compte d’identification Classic au lieu de créer un compte et d’y migrer vos Runbooks et ressources.

Avant de continuer, vérifiez que :

1. Vous avez téléchargé et installé [Windows Management Framework 4.0 (WMF)](https://www.microsoft.com/download/details.aspx?id=40855) si vous utilisez Windows 7. Si vous utilisez Windows Server 2012 R2, Windows Server 2012, Windows 2008 R2, Windows 8.1 et Windows 7 SP1, [Windows Management Framework 5.0](https://www.microsoft.com/download/details.aspx?id=50395) peut être installé.
2. Azure PowerShell 1.0. Pour plus d’informations sur cette version et sur la méthode d’installation, voir l’article [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).
3. Vous avez créé un compte Automation. Ce compte sera référencé en tant que valeur des paramètres –AutomationAccountName et –ApplicationDisplayName dans les deux scripts ci-dessous.

Pour obtenir les valeurs des paramètres *SubscriptionID*, *ResourceGroup* et *AutomationAccountName*, qui sont requis pour les scripts, dans le portail Azure, sélectionnez votre compte Automation dans le panneau **Compte Automation** et sélectionnez **Tous les paramètres**. Dans le panneau **Tous les paramètres**, sous **Paramètres du compte**, sélectionnez **Propriétés**. Dans le panneau **Propriétés**, vous pouvez observer ces valeurs.<br> ![Propriétés du compte Automation](media/automation-sec-configure-azure-runas-account/automation-account-properties.png)

### Créer le script PowerShell d’un compte d’identification

Le script PowerShell indiqué ci-dessous configure les éléments suivants :

- Une application Azure AD authentifiée avec un certificat auto-signé, la création d’un compte de principal du service pour cette application dans Azure AD et l’affectation du rôle de contributeur (que vous pouvez remplacer par un rôle de propriétaire ou par tout autre rôle) pour ce compte dans votre abonnement actuel. Pour plus d’informations, consultez l’article [Contrôle d’accès en fonction du rôle dans Azure Automation](../automation/automation-role-based-access-control.md).
- Une ressource de certificat Automation dans le compte Automation spécifié nommée **AzureRunAsCertificate**, qui contient le certificat utilisé dans le principal du service.
- Une ressource de connexion Automation dans le compte Automation spécifié nommée **AzureRunAsConnection**, qui contient l’ID d’application, l’ID de locataire, l’ID d’abonnement et l’empreinte du certificat.

Les étapes ci-dessous vous guident tout au long de l’exécution du script.

1. Enregistrez le script suivant sur votre ordinateur. Dans cet exemple, enregistrez-le sous le nom de fichier **New-AzureServicePrincipal.ps1**.

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
        Select-AzureRmSubscription -SubscriptionId $SubscriptionId

        $CurrentDate = Get-Date
        $EndDate = $CurrentDate.AddMonths($NoOfMonthsUntilExpired)
        $KeyId = (New-Guid).Guid
        $CertPath = Join-Path $env:TEMP ($ApplicationDisplayName + ".pfx")

        $Cert = New-SelfSignedCertificate -DnsName $ApplicationDisplayName -CertStoreLocation cert:\LocalMachine\My -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider"

        $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my" + $Cert.Thumbprint) -FilePath $CertPath -Password $CertPassword -Force | Write-Verbose

        $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate -ArgumentList @($CertPath, $CertPlainPassword)
        $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

        $KeyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
        $KeyCredential.StartDate = $CurrentDate
        $KeyCredential.EndDate= $EndDate
        $KeyCredential.KeyId = $KeyId
        $KeyCredential.Type = "AsymmetricX509Cert"
        $KeyCredential.Usage = "Verify"
        $KeyCredential.Value = $KeyValue

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
3. À partir de l’interpréteur de commandes à ligne de commande PowerShell avec élévation de privilèges, accédez au dossier qui contient le script créé à l’étape 1 et exécutez-le en modifiant les valeurs des paramètres *–ResourceGroup*, *-AutomationAccountName*, *-ApplicationDisplayName*, *-SubscriptionId* et *-CertPlainPassword*.<br>

    >[AZURE.NOTE] Une fois le script exécuté, vous êtes invité à vous authentifier auprès d’Azure. Vous devez vous connecter avec un compte membre du rôle Administrateurs des abonnements et coadministrateur de l’abonnement.
    
        .\New-AzureServicePrincipal.ps1 -ResourceGroup <ResourceGroupName> 
        -AutomationAccountName <NameofAutomationAccount> `
        -ApplicationDisplayName <DisplayNameofAutomationAccount> `
        -SubscriptionId <SubscriptionId> `
        -CertPlainPassword "<StrongPassword>"  
<br>

Une fois le script exécuté, reportez-vous à [l’exemple de code](#sample-code-to-authenticate-with-resource-manager-resources) ci-dessous pour vous authentifier auprès des ressources Resource Manager et valider la configuration des informations d’identification.

### Créer le script PowerShell d’un compte d’identification Classic

Le script PowerShell indiqué ci-dessous configure les éléments suivants :

- Une ressource de certificat Automation dans le compte Automation spécifié nommée **AzureClassicRunAsCertificate**, qui contient le certificat utilisé pour l’authentification de vos Runbooks.
- Une ressource de connexion Automation dans le compte Automation spécifié nommée **AzureClassicRunAsConnection**, qui contient le nom de l’abonnement, l’ID d’abonnement et le nom de la ressource de certificat.

Le script crée un certificat de gestion auto-signé et l’enregistre dans le dossier de fichiers temporaires sur votre ordinateur, sous le profil utilisateur utilisé pour exécuter la session PowerShell : *%USERPROFILE%\\AppData\\Local\\Temp*. Après l’exécution du script, vous devez charger le certificat de gestion Azure dans le magasin de gestion de l’abonnement dans lequel le compte Automation a été créé. Les étapes ci-dessous vous guident tout au long de l’exécution du script et du chargement du certificat.

1. Enregistrez le script suivant sur votre ordinateur. Dans cet exemple, enregistrez-le sous le nom de fichier **New-AzureClassicRunAsAccount.ps1**.

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
        Export-PfxCertificate -Cert ("Cert:\localmachine\my" + $Cert.Thumbprint) -FilePath $CertPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my" + $Cert.Thumbprint) -FilePath $CertPathCer -Type CERT | Write-Verbose

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
3. À partir de l’interpréteur de commandes à ligne de commande PowerShell avec élévation de privilèges, accédez au dossier qui contient le script créé à l’étape 1 et exécutez-le en modifiant les valeurs des paramètres *–ResourceGroup*, *-AutomationAccountName*, *-ApplicationDisplayName*, *-SubscriptionId* et *-CertPlainPassword*.<br>

    >[AZURE.NOTE] Une fois le script exécuté, vous êtes invité à vous authentifier auprès d’Azure. Vous devez vous connecter avec un compte membre du rôle Administrateurs des abonnements et coadministrateur de l’abonnement.
   
        .\New-AzureClassicRunAsAccount.ps1 -ResourceGroup <ResourceGroupName> 
        -AutomationAccountName <NameofAutomationAccount> `
        -ApplicationDisplayName <DisplayNameofAutomationAccount> `
        -SubscriptionId <SubscriptionId> `
        -CertPlainPassword "<StrongPassword>" 

Une fois le script exécuté, vous devez copier le certificat créé dans le dossier **Temp** de votre profil utilisateur. Suivez les étapes pour [charger un certificat d’API de gestion](../azure-api-management-certs.md) vers le portail Azure Classic, puis reportez-vous à [l’exemple de code](#sample-code-to-authenticate-with-service-management-resources) pour valider la configuration des informations d’identification avec les ressources de gestion des services.

## Exemple de code pour l’authentification avec des ressources Azure Resource Manager

Vous pouvez utiliser l’exemple de code mis à jour ci-dessous, extrait de l’exemple de Runbook **AzureAutomationTutorialScript**, pour procéder à une authentification avec le compte d’identification pour gérer les ressources Azure Resource Manager avec vos Runbooks.

    $connectionName = "AzureRunAsConnection"
    $SubId = Get-AutomationVariable -Name 'SubscriptionId'
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

Notez que l’applet de commande utilisée pour l’authentification (**Add-AzureRmAccount**) dans le Runbook, utilise le jeu de paramètres *ServicePrincipalCertificate*. Elle effectue l’authentification à l’aide du certificat du principal du service et non des informations d’identification.

## Exemple de code pour l’authentification avec les ressources de gestion des services

Vous pouvez utiliser l’exemple de code mis à jour ci-dessous, extrait de l’exemple de Runbook **AzureClassicAutomationTutorialScript**, pour procéder à une authentification avec le compte d’identification Classic pour gérer les ressources classiques avec vos Runbooks.
    
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


## Étapes suivantes

- Pour plus d’informations sur les principaux de service, consultez l’article [Objets Principal du service et Application](../active-directory/active-directory-application-objects.md).
- Pour plus d’informations sur le contrôle d’accès en fonction du rôle dans Azure Automation, reportez-vous à l’article [Contrôle d’accès en fonction du rôle dans Azure Automation](../automation/automation-role-based-access-control.md).
- Pour plus d’informations sur les certificats et les services Azure, consultez la page [Vue d’ensemble des certificats pour Azure Cloud Services](../cloud-services/cloud-services-certs-create.md).

<!---HONumber=AcomDC_0803_2016-->