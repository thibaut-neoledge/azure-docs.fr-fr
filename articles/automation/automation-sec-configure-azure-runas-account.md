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
    ms.date="07/06/2016"
    ms.author="magoedte"/>

# Authentifier des Runbooks avec un compte d’identification Azure
Cette rubrique vous explique la manière de configurer un compte Automation à partir du portail Azure à l’aide de la nouvelle fonctionnalité de compte d’identification (également appelée « principal du service ») afin de vous permettre d’accéder aux ressources Azure Resource Manager dans votre abonnement avec les Runbooks Automation. Lorsque vous créez un compte Automation dans le portail Azure, un nouveau principal du service est automatiquement créé et affecté par défaut au rôle RBAC (contrôle d’accès en fonction du rôle) de Contributeur dans l’abonnement. Le processus s’en trouve ainsi simplifié, et vous êtes en mesure de commencer rapidement à générer et déployer des Runbooks pour répondre à vos besoins d’automatisation.

Avantages d’un principal du service :

* Bénéficier d’une méthode d’authentification Azure standardisée lorsque vous gérez des ressources Azure Resource Manager à l’aide de Runbooks
* Automatiser l’utilisation de Runbooks globaux configurés dans les alertes Azure


>[AZURE.NOTE] La [fonctionnalité d’intégration d’alertes](../azure-portal/insights-receive-alert-notifications.md) Azure associée aux Runbooks globaux requiert un compte Automation configuré avec un principal du service. Vous pouvez sélectionner un compte Automation déjà associé à un utilisateur principal du service ou choisir d’en créer un nouveau.



Nous allons vous montrer comment créer un compte Automation à partir du portail Azure et mettre à jour un compte avec un compte d’identification à l’aide d’Azure PowerShell, et vous expliquer comment vous authentifier auprès de ce principal du service dans vos Runbooks.

## Créer un compte Automation à partir du portail Azure
Dans cette section, vous allez suivre une procédure qui vous permettra de créer un compte Azure Automation et un principal du service à partir du portail Azure.

>[AZURE.NOTE] L’utilisateur qui exécute ces étapes *doit* être membre du rôle Administrateurs des abonnements.

1. Connectez-vous au portail Azure en tant qu’administrateur de service pour l’abonnement Azure que vous souhaitez gérer.
2. Sélectionnez **Comptes Automation**.
3. Dans le panneau Comptes Automation, cliquez sur **Ajouter**.<br>![Ajouter un compte Automation](media/automation-sec-configure-azure-runas-account/add-automation-acct-properties.png)
4. Dans le panneau **Ajouter un compte Automation**, entrez le nom de votre nouveau compte Automation dans la zone **Nom**.
5. Si vous disposez de plusieurs abonnements, spécifiez celui du nouveau compte, ainsi qu’un **groupe de ressources** nouveau ou existant et un **emplacement** de centre de données Azure.
6. Vérifiez que l’option **Créer un compte d’authentification Azure** est bien définie sur la valeur **Oui**, puis cliquez sur le bouton **Créer**.

    ![Avertissement Ajouter un compte Automation](media/automation-sec-configure-azure-runas-account/add-account-decline-create-runas-msg.png)

    >[AZURE.NOTE] Si vous choisissez de ne pas créer de compte d’identification en sélectionnant l’option **Non**, un message d’avertissement s’affiche dans le panneau **Ajouter un compte Automation**. Bien que le compte soit créé avec le rôle de **contributeur** dans l’abonnement, il n’aura pas d’identité d’authentification correspondante au sein de votre service de répertoire d’abonnements et, par conséquent, il n’aura pas accès aux ressources de votre abonnement. Cela empêchera tous les Runbooks faisant référence à ce compte de pouvoir authentifier et effectuer des tâches sur les ressources Azure Resource Manager.

    ![Avertissement Ajouter un compte Automation](media/automation-sec-configure-azure-runas-account/add-automation-acct-properties-error.png)

    >[AZURE.NOTE] Si vous recevez un message d’erreur de refus de l’autorisation après avoir cliqué sur le bouton **Créer**, c’est parce que votre compte n’est pas membre du rôle Administrateurs des abonnements.

7. Pour suivre la progression de la création du compte Automation, accédez à l’onglet **Notifications** du menu.

### Ressources incluses
Une fois la création du compte Automation terminée, plusieurs ressources vous sont automatiquement créées. Celles-ci sont résumées dans le tableau ci-dessous.

Ressource|Description 
----|----
Runbook AzureAutomationTutorial|Un exemple de Runbook qui illustre l’authentification à l’aide du Compte d’identification et l’affichage des 10 premières machines virtuelles Azure dans votre abonnement.
AzureRunAsCertificate|Une ressource de certificat qui est créée si vous avez choisi de créer le Compte d’identification lors de la création du compte Automation ou à l’aide du script PowerShell ci-dessous pour un compte existant. Ce certificat a une durée de vie d’un an. 
AzureRunAsConnection|Une ressource de connexion qui est créée si vous avez choisi de créer le Compte d’identification lors de la création du compte Automation ou à l’aide du script PowerShell ci-dessous pour un compte existant.
Modules|15 modules avec des applets de commande pour Azure, PowerShell et Automation pour une utilisation immédiate dans vos Runbooks.  

## Mise à jour d’un compte Automation à l’aide de PowerShell
La procédure suivante permet de mettre à jour un compte Automation existant et de créer le principal du service à l’aide de PowerShell. Vous devez suivre cette procédure si vous avez créé un compte sans créer de compte d’identification.

Avant de continuer, vérifiez que :

1. Vous avez téléchargé et installé [Windows Management Framework 4.0 (WMF)](https://www.microsoft.com/download/details.aspx?id=40855) si vous exécutez Windows 7. Si vous exécutez Windows Server 2012 R2, Windows Server 2012, Windows 2008 R2, Windows 8.1 et Windows 7 SP1, [Windows Management Framework 5.0](https://www.microsoft.com/download/details.aspx?id=50395) est disponible pour installation.
2. Azure PowerShell 1.0. Pour plus d’informations sur cette version et la méthode d’installation, voir [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).
3. Vous avez créé un compte Automation. Ce compte sera référencé en tant que valeur des paramètres –AutomationAccountName et –ApplicationDisplayName dans le script ci-dessous.


Le script PowerShell configurera les éléments suivants :

* Une application Azure AD authentifiée avec un certificat auto-signé, la création d’un compte de principal du service pour cette application dans Azure AD et l’affectation du rôle de contributeur (que vous pouvez remplacer par un rôle de propriétaire ou par tout autre rôle) pour ce compte dans votre abonnement actuel. Pour plus d’informations, consultez l’article [Contrôle d’accès en fonction du rôle dans Azure Automation](../automation/automation-role-based-access-control.md).
* Une ressource de certificat Automation dans le compte Automation spécifié nommée **AzureRunAsCertificate**, qui contient le certificat utilisé dans le principal du service.
* Une ressource de connexion Automation dans le compte Automation spécifié nommée **AzureRunAsConnection**, qui contient l’ID d’application, l’ID de locataire, l’ID d’abonnement et le Thumbprint du certificat.


### Exécution du script PowerShell

1. Enregistrez le script suivant sur votre ordinateur. Dans cet exemple, enregistrez-le sous le nom de fichier **New-AzureServicePrincipal.ps1**.

    ```
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
    While ($NewRole -eq $null -and $Retries -le 2)
    {
      # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
      Sleep 5
      New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
      Sleep 5
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
    ```
<br>
2. Sur votre ordinateur, démarrez **Windows PowerShell** depuis l’écran **Démarrer** avec des droits élevés.
3. À partir de l’interface de ligne de commande PowerShell avec élévation de privilèges, accédez au dossier contenant le script créé à l’étape 1 et exécutez-le en modifiant les valeurs des paramètres *–ResourceGroup*, *-AutomationAccountName*, *-ApplicationDisplayName*, *-SubscriptionId* et *-CertPlainPassword*.<br>

    ```
    .\New-AzureServicePrincipal.ps1 -ResourceGroup <ResourceGroupName> 
     -AutomationAccountName <NameofAutomationAccount> `
     -ApplicationDisplayName <DisplayNameofAutomationAccount> `
     -SubscriptionId <SubscriptionId> `
     -CertPlainPassword "<StrongPassword>"
    ```   
<br>

    >[AZURE.NOTE] Une fois le script exécuté, vous êtes invité à vous authentifier auprès d’Azure. Vous *devez* vous connecter avec un compte doté du rôle Administrateur de service dans l’abonnement. <br>
4. Une fois le script terminé, passez à la section suivante pour tester et vérifier la nouvelle configuration des informations d’identification.

### Vérifier l’authentification
Nous allons ensuite effectuer un test rapide afin de vérifier que vous êtes en mesure de vous authentifier à l’aide du nouveau principal du service. Si vous ne parvenez pas à vous authentifier, revenez à l’étape 1 et confirmez de nouveau chacune des étapes précédentes.

1. Dans le portail Azure, ouvrez le compte Automation que vous avez créé.
2. Cliquez sur la vignette **Runbooks** pour ouvrir la liste des runbooks.
3. Créez un Runbook en cliquant sur le bouton **Ajouter un Runbook**, puis sur **Créer un Runbook** dans le panneau **Ajouter un Runbook**.
4. Nommez le Runbook *Test-SecPrin-Runbook* et sélectionnez PowerShell comme **type de Runbook**. Cliquez sur **Créer** pour créer le Runbook.
5. Dans le panneau **Modifier le Runbook PowerShell**, collez le code suivant dans le canevas :<br>

    ```
     $Conn = Get-AutomationConnection -Name AzureRunAsConnection 
     Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
     -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    ```  
<br>
6. Enregistrez le Runbook en cliquant sur **Enregistrer**.
7. Cliquez sur **Panneau de test** pour ouvrir le panneau **Test**.
8. Cliquez sur **Démarrer** pour démarrer le test.
9. Une [tâche de Runbook](automation-runbook-execution.md) est créée et son état apparaît dans le volet.
10. L’état initial de la tâche est *Mis en file d’attente* pour indiquer que la tâche attend qu’un Runbook Worker du cloud devienne disponible. La tâche prend ensuite l’état *Démarrage en cours* lorsqu’un Worker sélectionne la tâche, puis l’état *En cours d’exécution* lorsque le Runbook commence à s’exécuter.
11. Lorsque la tâche du Runbook est terminée, sa sortie s'affiche. Dans notre cas, nous devrions obtenir l’état **Terminé**.<br> ![Test de Runbook du principal de sécurité](media/automation-sec-configure-azure-runas-account/runbook-test-results.png)<br>
12. Fermez le panneau **Test** pour revenir au canevas.
13. Fermez le panneau **Modifier le Runbook PowerShell**.
14. Fermez le panneau **Test-SecPrin-Runbook**.

## Exemple de code pour l’authentification avec des ressources Azure Resource Manager

Vous pouvez utiliser l’exemple de code mis à jour ci-dessous, pris dans l’exemple de Runbook AzureAutomationTutorial, pour une authentification avec le Compte d’identification. Celle-ci sert à gérer les ressources Azure Resource Manager avec vos Runbooks.

   ```
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
   ```

Le script inclut deux lignes de code supplémentaires pour prendre en charge le référencement d’un contexte d’abonnement ; le travail entre plusieurs abonnements est ainsi facilité. Une ressource variable nommée SubscriptionId contient l’ID de l’abonnement et, suite à l’instruction d’applet de commande Add-AzureRmAccount, l’[applet de commande Set-AzureRmContext](https://msdn.microsoft.com/library/mt619263.aspx) est indiquée avec le jeu de paramètres *-SubscriptionId*. Si le nom de la variable est trop générique, vous pouvez modifier le nom de la variable afin que celui-ci comprenne un préfixe ou une autre convention d’affectation de noms pour faciliter son identification. Vous pouvez également utiliser le jeu de paramètres -SubscriptionName au lieu de -SubscriptionId avec la ressource de variable correspondante.

## Étapes suivantes
- Pour plus d’informations sur les principaux de service, voir [Objets Principal du service et Application](../active-directory/active-directory-application-objects.md).
- Pour plus d’informations sur le contrôle d’accès en fonction du rôle dans Azure Automation, voir [Contrôle d’accès en fonction du rôle dans Azure Automation](../automation/automation-role-based-access-control.md).

<!---HONumber=AcomDC_0713_2016-->