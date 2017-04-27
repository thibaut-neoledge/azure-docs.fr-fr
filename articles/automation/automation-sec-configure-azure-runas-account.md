---
title: "Configurer un compte d’authentification Azure | Microsoft Docs"
description: "Ce didacticiel vous guide tout au long des procédures de création et de test d’une authentification de principal de sécurité dans Azure Automation. Il est complété par un exemple d’utilisation."
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
keywords: nom du principal du service, setspn, authentification azure
ms.assetid: 2f783441-15c7-4ea0-ba27-d7daa39b1dd3
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/06/2017
ms.author: magoedte
ROBOTS: NOINDEX
redirect_url: /azure/automation/automation-offering-get-started
redirect_document_id: TRUE
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: fbca3d195290551d37606e231b997a40a602351f
ms.lasthandoff: 03/29/2017


---

# <a name="authenticate-runbooks-with-an-azure-run-as-account"></a>Authentifier des Runbooks avec un compte d’identification Azure
Cet article vous montre comment configurer un compte Azure Automation dans le portail Azure. Pour ce faire, utilisez la fonctionnalité Compte d’identification qui permet d’authentifier les Runbooks gérant les ressources dans Azure Resource Manager ou dans Azure Service Management.

Lorsque vous créez un compte Automation dans le portail Azure, il crée automatiquement 2 comptes :

* Compte d’identification Azure. Ce compte crée un principal de service dans Azure Active Directory (Azure AD) et un certificat. Il affecte également le rôle RBAC (contrôle d’accès en fonction du rôle) de contributeur qui gère les ressources Resource Manager à l’aide de Runbooks.
* Compte d’identification Classic. Ce compte charge un certificat de gestion, qui est utilisé pour gérer les ressources de gestion des services ou les ressources classiques à l’aide de Runbooks.

Le processus de création d’un compte Automation s’en trouve ainsi simplifié, et vous êtes en mesure de commencer rapidement à générer et déployer des Runbooks pour répondre à vos besoins d’automatisation.

À l’aide de comptes d’identification standard ou Classic, vous pouvez :

* Bénéficier d’une méthode d’authentification Azure standardisée lorsque vous gérez des ressources Resource Manager ou des ressources des services de gestion Azure à partir de Runbooks dans le portail Azure.
* Automatiser l’utilisation de Runbooks globaux que vous pouvez configurer dans les alertes Azure.

> [!NOTE]
> La [fonctionnalité d’intégration d’alertes](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) Azure associée aux Runbooks globaux requiert un compte Automation configuré avec un compte d’identification standard et un compte d’identification Classic. Vous pouvez sélectionner un compte Automation pour lequel des comptes d’identification standard et Classic sont déjà définis ou choisir d’en créer un.
>  

Cet article vous montre comment créer un compte Automation à partir du portail Azure, le mettre à jour à l’aide de PowerShell, gérer la configuration du compte et vous authentifier dans vos Runbooks.

Avant de créer un compte Automation, il est judicieux de comprendre et de prendre en compte les éléments suivants :

* La création d’un compte Automation n’affecte pas les comptes Automation existants déjà créés dans le modèle de déploiement classique ou Resource Manager.
* Cela fonctionne uniquement pour les comptes Automation créés via le portail Azure. Si vous tentez de créer un compte à partir du portail Azure Classic, la configuration du compte d’identification n’est pas répliquée.
* Si vous avez déjà des Runbooks et des ressources (telles que des planifications ou des variables) en place pour gérer les ressources classiques, et que vous souhaitez que les Runbooks s’authentifient auprès du nouveau compte d’identification Classic, effectuez l’une des actions suivantes :

  * Pour créer un compte d’identification Classic, suivez les instructions de la section « Gestion de votre compte d’identification ». 
  * Pour mettre à jour votre compte existant, utilisez le script PowerShell de la section « Mise à jour de votre compte Automation à l’aide de PowerShell ».
* Pour vous authentifier à l’aide du nouveau compte d’identification ou du compte Automation d’identification Classic, vous devez modifier les Runbooks existants avec l’exemple de code indiqué dans la section [Exemples de code d’authentification](#authentication-code-examples).

    >[!NOTE]
    >Le compte d’identification sert à l’authentification des ressources Resource Manager à l’aide du principal de service basé sur les certificats. Le compte d’identification Classic sert à l’authentification des ressources de gestion des services avec un certificat de gestion.

## <a name="create-an-automation-account-from-the-azure-portal"></a>Créer un compte Automation à partir du portail Azure
Dans cette section, vous créez un compte Azure Automation à partir du portail Azure, qui à son tour crée un compte d’identification standard et un compte d’identification Classic.

>[!NOTE]
>Pour créer un compte Automation, vous devez posséder le rôle Administrateurs de services ou être coadministrateur de l’abonnement, ce qui permet d’accéder à l’abonnement. Vous devez également être ajouté en tant qu’utilisateur à l’instance Active Directory par défaut de cet abonnement. Il n’est pas nécessaire d’attribuer au compte un rôle doté de privilèges.
>
>Si vous n’êtes pas membre de l’instance d’Active Directory de l’abonnement au moment d’être ajouté au rôle Coadministrateur de l’abonnement, vous serez ajouté à Active Directory en tant qu’invité. Dans ce cas, vous recevrez le message d’avertissement « Vous n’avez pas les autorisations pour créer... » dans le panneau **Ajouter un compte Automation**.
>
>Les utilisateurs qui ont d’abord reçu le rôle Coadministrateur peuvent être supprimés de l’instance Active Directory de l’abonnement, puis rajoutés pour en faire des utilisateurs complets dans Active Directory. Pour vérifier si tel est le cas, dans le volet **Azure Active Directory** du portail Azure, sélectionnez **Utilisateurs et groupes** et **Tous les utilisateurs**, choisissez l’utilisateur concerné, puis sélectionnez **Profil**. La valeur de l’attribut **Type d’utilisateur** sous le profil de l’utilisateur ne doit pas être **Invité**.
>

1. Connectez-vous au portail Azure avec un compte membre du rôle Administrateurs des abonnements et coadministrateur de l’abonnement.

2. Sélectionnez **Comptes Automation**.

3. Dans le panneau **Comptes Automation**, cliquez sur **Ajouter**.
Le panneau **Ajouter un compte Automation** s’ouvre.

 ![Panneau « Ajouter un compte Automation »](media/automation-sec-configure-azure-runas-account/create-automation-account-properties-b.png)

   > [!NOTE]
   > Si votre compte n’est ni membre du rôle Administrateurs des abonnements ni coadministrateur de l’abonnement, l’avertissement suivant s’affiche dans le panneau **Ajouter un compte Automation** :
   >
   >![Avertissement Ajouter un compte Automation](media/automation-sec-configure-azure-runas-account/create-account-without-perms.png)
   >
   >

4. Dans le panneau **Ajouter un compte Automation**, entrez le nom de votre nouveau compte Automation dans la zone **Nom**.

5. Si vous avez plusieurs abonnements, procédez comme suit :

    a. Sous **Abonnement**, spécifiez-en un pour le nouveau compte.

    b. Sous **Groupe de ressources**, cliquez sur **Créer** ou **Utiliser l’existant**.

    c. Sous **Emplacement**, indiquez un centre de données Azure.

6. Sous **Créer un compte d’identification Azure**, sélectionnez **Oui**, puis cliquez sur **Créer**.

   > [!NOTE]
   > Si vous choisissez de ne pas créer de compte d’identification en sélectionnant l’option **Non**, un message d’avertissement s’affiche dans le panneau **Ajouter un compte Automation**. Même si le compte est créé dans le portail Azure, il n’aura pas d’identité d’authentification correspondante au sein de votre service de répertoire des abonnements classique ou Resource Manager. Par conséquent, le compte n’a aucun accès aux ressources de votre abonnement. Ce scénario empêche tous les Runbooks faisant référence à ce compte de pouvoir s’authentifier et d’effectuer des tâches sur les ressources de ces modèles de déploiement.
   >
   > ![Message d’avertissement dans le panneau « Ajouter un compte Automation »](media/automation-sec-configure-azure-runas-account/create-account-decline-create-runas-msg.png)
   >
   > En outre, étant donné que le principal du service n’est pas créé, le rôle Collaborateur n’est pas attribué.
   >

7. Pour suivre la progression de la création du compte Automation, accédez à l’onglet **Notifications** du menu.

### <a name="resources"></a>les ressources
Une fois le compte Automation créé, plusieurs ressources vous sont automatiquement créées. Les ressources sont récapitulées dans les 2 tableaux ci-dessous :

#### <a name="run-as-account-resources"></a>Ressources de compte d’identification

| Ressource | Description |
| --- | --- |
| Runbook AzureAutomationTutorial | Exemple de Runbook Graphical qui illustre l’authentification à l’aide du compte d’identification et l’accès à l’ensemble des ressources Resource Manager. |
| Runbook AzureAutomationTutorialScript | Exemple de Runbook PowerShell qui illustre l’authentification à l’aide du compte d’identification et l’accès à l’ensemble des ressources Resource Manager. |
| AzureRunAsCertificate | La ressource de certificat créée automatiquement lors de la création du compte Automation ou à l’aide du script PowerShell ci-dessous pour un compte existant. Le certificat permet de vous authentifier auprès d’Azure afin de pouvoir gérer les ressources Azure Resource Manager des Runbooks. Ce certificat a une durée de vie d’1 an. |
| AzureRunAsConnection | La ressource de connexion créée automatiquement lors de la création du compte Automation ou à l’aide du script PowerShell pour un compte existant. |

#### <a name="classic-run-as-account-resources"></a>Ressources de compte d’identification Classic

| Ressource | Description |
| --- | --- |
| Runbook AzureClassicAutomationTutorial | Exemple de Runbook Graphical qui accède à toutes les machines virtuelles créées sur le modèle de déploiement classique dans un abonnement à l’aide du compte d’identification Classic (certificat), puis indique leur nom et état. |
| Runbook AzureClassicAutomationTutorialScript | Exemple de Runbook PowerShell qui accède à toutes les machines virtuelles Classic d’un abonnement à l’aide du compte d’identification Classic (certificat), puis écrit leur nom et leur état. |
| AzureClassicRunAsCertificate | Ressource de certificat créée automatiquement et utilisée pour l’authentification auprès d’Azure afin que vous puissiez gérer les ressources Azure Classic des Runbooks. Ce certificat a une durée de vie d’1 an. |
| AzureClassicRunAsConnection | Ressource de connexion créée automatiquement et utilisée pour l’authentification auprès d’Azure afin que vous puissiez gérer les ressources Azure Classic des Runbooks. |

## <a name="verify-run-as-authentication"></a>Vérifier l’authentification d’identification
Effectuez un test rapide afin de vérifier que vous êtes en mesure de vous authentifier à l’aide du nouveau compte d’identification.

1. Dans le portail Azure, ouvrez le compte Automation que vous avez créé.

2. Cliquez sur la vignette **Runbooks** pour ouvrir la liste des runbooks.

3. Sélectionnez le Runbook **AzureAutomationTutorialScript**, puis cliquez sur **Démarrer** pour le lancer. Les événements suivants se produisent :
 * Un [travail du Runbook](automation-runbook-execution.md) est créé. Le panneau **Travail** s’affiche alors et l’état du travail est affiché dans la mosaïque **Résumé du travail**.
 * L’état initial du travail est **Mis en file d’attente** pour indiquer qu’il attend la disponibilité d’un Runbook Worker dans le cloud.
 * L’état passe ensuite à **Démarrage en cours** lorsqu’un Worker sélectionne le travail.
 * L’état passe à **En cours d’exécution** lorsque le Runbook commence à s’exécuter.
 * Lorsque le travail de Runbook a fini de s’exécuter,l’état est **Terminé**.

       ![Security Principal Runbook Test](media/automation-sec-configure-azure-runas-account/job-summary-automationtutorialscript.png)
4. Pour afficher les résultats détaillés du Runbook, cliquez sur la mosaïque **Sortie**.  
Le panneau **Sortie** est affiché, indiquant que le Runbook a authentifié et retourné la liste de toutes les ressources disponibles dans le groupe de ressources.

5. Fermez le panneau **Sortie** pour revenir au panneau **Résumé du travail**.

6. Fermez le panneau **Résumé du travail** et le panneau du Runbook **AzureAutomationTutorialScript** correspondant.

## <a name="verify-classic-run-as-authentication"></a>Vérifier l’authentification d’identification Classic
Effectuez un test rapide similaire afin de vérifier que vous êtes en mesure de vous authentifier à l’aide du nouveau compte d’identification Classic.

1. Dans le portail Azure, ouvrez le compte Automation que vous avez créé.

2. Cliquez sur la vignette **Runbooks** pour ouvrir la liste des runbooks.

3. Sélectionnez le Runbook **AzureClassicAutomationTutorialScript**, puis cliquez sur **Démarrer** pour le lancer. Les événements suivants se produisent :

 * Un [travail du Runbook](automation-runbook-execution.md) est créé. Le panneau **Travail** s’affiche alors et l’état du travail est affiché dans la mosaïque **Résumé du travail**.
 * L’état initial du travail est **Mis en file d’attente** pour indiquer qu’il attend la disponibilité d’un Runbook Worker dans le cloud.
 * L’état passe ensuite à **Démarrage en cours** lorsqu’un Worker sélectionne le travail.
 * L’état passe à **En cours d’exécution** lorsque le Runbook commence à s’exécuter.
 * Lorsque le travail de Runbook a fini de s’exécuter, l’état est **Terminé**.

    ![Test de Runbook du principal de sécurité](media/automation-sec-configure-azure-runas-account/job-summary-automationclassictutorialscript.png)<br>
4. Pour afficher les résultats détaillés du Runbook, cliquez sur la mosaïque **Sortie**.  
Le panneau **Sortie** est affiché, indiquant que le Runbook a authentifié et retourné la liste de toutes les machines virtuelles Classic de l’abonnement.

5. Fermez le panneau **Sortie** pour revenir au panneau **Résumé du travail**.

6. Fermez le panneau **Résumé du travail** et le panneau du Runbook **AzureAutomationTutorialScript** correspondant.

## <a name="managing-your-run-as-account"></a>Gestion de votre compte d’identification
Avant que votre compte Automation n’expire, vous devez renouveler le certificat. Si vous pensez que le compte d’identification a été compromis, vous pouvez le supprimer et le recréer. Cette section décrit comment effectuer ces opérations.

### <a name="self-signed-certificate-renewal"></a>Renouvellement de certificat auto-signé
Le certificat auto-signé que vous avez créé pour le compte d’identification expire 1 an après la date de création. Vous pouvez le renouveler à tout moment avant qu’il n’expire. Lorsque vous le renouvelez, le certificat valide en cours est conservé afin de garantir que les Runbooks en file d’attente ou en cours d’exécution, qui s’authentifient avec le compte d’identification, ne sont pas affectés. Le certificat reste valide jusqu’à sa date d’expiration.

> [!NOTE]
> Si vous avez configuré votre compte d’identification Automation pour utiliser un certificat émis par votre autorité de certification d’entreprise et que vous utilisez cette option, ce certificat sera remplacé par un certificat auto-signé.

Pour renouveler le certificat, procédez comme suit :

1. Dans le portail Azure, ouvrez le compte Automation.

2. Dans le panneau **Compte Automation**, dans le volet **Propriétés du compte**, sous **Paramètres du compte**, sélectionnez **Comptes d’identification**.

    ![Panneau des propriétés du compte Automation](media/automation-sec-configure-azure-runas-account/automation-account-properties-pane.png)
3. Dans le panneau des propriétés des **Comptes d’identification**, sélectionnez le compte d’identification standard ou le compte d’identification Classic pour lequel vous souhaitez renouveler le certificat.

4. Sur le panneau **Propriétés** du compte sélectionné, cliquez sur **Renouveler le certificat**.

    ![Renouveler le certificat pour le compte d’identification](media/automation-sec-configure-azure-runas-account/automation-account-renew-runas-certificate.png)

5. Pour suivre la progression du renouvellement du certificat, accédez à l’onglet **Notifications** du menu.

### <a name="delete-a-run-as-or-classic-run-as-account"></a>Supprimer un compte d’identification standard ou Classic
Cette section décrit comment supprimer et recréer votre compte d’identification standard ou Classic. Lorsque vous effectuez cette opération, le compte Automation est conservé. Après avoir supprimé le compte d’identification standard ou le compte d’identification Classic, vous pouvez le recréer dans le portail Azure.

1. Dans le portail Azure, ouvrez le compte Automation.

2. Dans le panneau **Compte Automation**, dans le volet des propriétés du compte, sélectionnez **Comptes d’identification**.

3. Dans le panneau des propriétés des **Comptes d’identification**, sélectionnez le compte d’identification standard ou le compte d’identification Classic que vous voulez supprimer. Ensuite, dans le panneau **Propriétés** du compte sélectionné, cliquez sur **Supprimer**.

 ![Supprimer un compte d’identification](media/automation-sec-configure-azure-runas-account/automation-account-delete-runas.png)

4. Pour suivre la progression de la suppression du compte, accédez à l’onglet **Notifications** du menu.

5. Une fois le compte supprimé, vous pouvez le recréer sur le panneau des propriétés **Comptes d’identification** en sélectionnant l’option de création **Compte d’identification Azure**.

 ![Recréer le compte d’identification Automation](media/automation-sec-configure-azure-runas-account/automation-account-create-runas.png)

### <a name="misconfiguration"></a>Configuration incorrecte
Certains éléments de configuration nécessaires pour que le compte d’identification standard ou le compte d’identification Classic fonctionne correctement ont peut-être été supprimés ou n’ont pas été créés correctement lors de l’installation initiale. Par exemple :

* Ressource de certificat
* Ressource de connexion
* Compte d’identification supprimé du rôle Contributeur
* Principal du service ou application dans Azure AD

Dans les cas précédents et dans d’autres instances de configuration incorrecte, le compte Automation détecte les modifications et affiche l’état *Incomplet* dans le panneau des propriétés **Comptes d’identification** du compte.

![État Incomplet pour la configuration du compte d’identification](media/automation-sec-configure-azure-runas-account/automation-account-runas-incomplete-config.png)

Lorsque vous sélectionnez le compte d’identification, le volet **Propriétés** du compte affiche le message d’erreur suivant :

![Message d’avertissement Incomplet pour la configuration du compte d’identification](media/automation-sec-configure-azure-runas-account/automation-account-runas-incomplete-config-msg.png).

Vous pouvez rapidement résoudre ces problèmes liés au compte d’identification en supprimant et en recréant le compte.

## <a name="update-your-automation-account-by-using-powershell"></a>Mise à jour de votre compte Automation à l’aide de PowerShell
Vous pouvez utiliser PowerShell pour mettre à jour votre compte Automation existant si :

* Vous créez un compte Automation sans compte d’identification.
* Vous possédez déjà un compte Automation pour gérer les ressources Resource Manager et vous voulez le mettre à jour pour inclure le compte d’identification pour l’authentification du Runbook.
* Vous possédez déjà un compte Automation pour gérer les ressources classiques et souhaitez le mettre à jour pour utiliser le compte d’identification Classic au lieu de créer un compte et d’y migrer vos Runbooks et ressources.   
* Vous souhaitez créer un compte d’identification standard et un compte d’identification Classic à l’aide d’un certificat émis par votre AC d’entreprise.

Le script nécessite les éléments suivants :

* Ce script peut uniquement être exécuté sur Windows 10 et sur Windows Server 2016 avec les modules Azure Resource Manager 2.01 et versions ultérieures. Il n’est pas pris en charge dans les versions antérieures de Windows.
* Azure PowerShell 1.0 et versions ultérieures. Pour plus d’informations sur la version PowerShell 1.0, voir [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs).
* Un compte Automation, référencé en tant que valeur des paramètres *–AutomationAccountName* et *-ApplicationDisplayName* dans le script PowerShell suivant.

Pour obtenir les valeurs des paramètres *SubscriptionID*, *ResourceGroup* et *AutomationAccountName*, qui sont des paramètres requis pour les scripts, procédez comme suit :
1. Sélectionnez votre compte Automation à partir du panneau **Compte Automation** du portail Azure, puis sélectionnez **Tous les paramètres**. 
2. Dans le panneau **Tous les paramètres**, sous **Paramètres du compte**, sélectionnez **Propriétés**. 
3. Notez les valeurs dans le panneau **Propriétés**.

![Panneau Propriétés du compte Automation](media/automation-sec-configure-azure-runas-account/automation-account-properties.png)  

### <a name="create-a-run-as-account-powershell-script"></a>Créer le script PowerShell d’un compte d’identification
Ce script PowerShell prend en charge les configurations suivantes :

* Créez un compte d’identification à l’aide d’un certificat auto-signé.
* Créez un compte d’identification standard et un compte d’identification Classic à l’aide d’un certificat auto-signé.
* Créez un compte d’identification standard et un compte d’identification Classic à l’aide d’un certificat d’entreprise.
* Créez un compte d’identification standard et un compte d’identification Classic à l’aide d’un certificat auto-signé dans le cloud Azure Government.

Selon l’option de configuration que vous sélectionnez, le script crée les éléments suivants.

**Pour les comptes d’identification :**

* Crée une application Azure AD qui sera exportée avec la clé publique du certificat auto-signé ou du certificat d’entreprise, crée un compte de principal de service pour cette application dans Azure AD et affecte le rôle Collaborateur pour le compte dans votre abonnement actuel. Vous pouvez remplacer ce paramètre par un rôle de propriétaire ou par tout autre rôle. Pour plus d’informations, voir [Contrôle d’accès en fonction du rôle dans Azure Automation](automation-role-based-access-control.md).
* Crée une ressource de certificat Automation nommée *AzureRunAsCertificate* dans le compte Automation spécifié. La ressource de certificat conserve la clé privée du certificat utilisée par l’application Azure AD.
* Crée une ressource de connexion Automation nommée *AzureRunAsConnection* dans le compte Automation spécifié. La ressource de connexion conserve les ID applicationId, tenantId et subscriptionId, et l’empreinte de certificat.

**Pour les comptes d’identification Classic :**

* Crée une ressource de certificat Automation nommée *AzureClassicRunAsCertificate*dans le compte Automation spécifié. La ressource de certificat conserve la clé privée du certificat utilisée par le certificat de gestion.
* Crée une ressource de connexion Automation nommée *AzureClassicRunAsConnection* dans le compte Automation spécifié. La ressource de connexion conserve le nom de l’abonnement, l’ID subscriptionId et le nom de ressource de certificat.

>[!NOTE]
> Si vous sélectionnez l’option permettant de créer un compte d’identification Classic, après l’exécution du script, vous devez charger le certificat public (extension de nom de fichier .cer) dans le magasin de gestion de l’abonnement dans lequel le compte Automation a été créé.
> 

Pour exécuter le script et télécharger le certificat, procédez comme suit :

1. Enregistrez le script suivant sur votre ordinateur. Dans cet exemple, enregistrez-le sous le nom de fichier *New-RunAsAccount.ps1*.

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
        [Boolean] $CreateClassicRunAsAccount,

        [Parameter(Mandatory=$true)]
        [String] $SelfSignedCertPlainPassword,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPathForRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPlainPasswordForRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPathForClassicRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPlainPasswordForClassicRunAsAccount,

        [Parameter(Mandatory=$false)]
        [ValidateSet("AzureCloud","AzureUSGovernment")]
        [string]$EnvironmentName="AzureCloud",

        [Parameter(Mandatory=$false)]
        [int] $SelfSignedCertNoOfMonthsUntilExpired = 12
        )

        function CreateSelfSignedCertificate([string] $keyVaultName, [string] $certificateName, [string] $selfSignedCertPlainPassword,
                                      [string] $certPath, [string] $certPathCer, [string] $selfSignedCertNoOfMonthsUntilExpired ) {
        $Cert = New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation cert:\LocalMachine\My `
           -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
           -NotAfter (Get-Date).AddMonths($selfSignedCertNoOfMonthsUntilExpired)

        $CertPassword = ConvertTo-SecureString $selfSignedCertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPathCer -Type CERT | Write-Verbose
        }

        function CreateServicePrincipal([System.Security.Cryptography.X509Certificates.X509Certificate2] $PfxCert, [string] $applicationDisplayName) {  
        $CurrentDate = Get-Date
        $keyValue = [System.Convert]::ToBase64String($PfxCert.GetRawCertData())
        $KeyId = (New-Guid).Guid

        $KeyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
        $KeyCredential.StartDate = $CurrentDate
        $KeyCredential.EndDate= [DateTime]$PfxCert.GetExpirationDateString()
        $KeyCredential.EndDate = $KeyCredential.EndDate.AddDays(-1)
        $KeyCredential.KeyId = $KeyId
        $KeyCredential.CertValue  = $keyValue

        # Use key credentials and create an Azure AD application
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $KeyId) -KeyCredentials $KeyCredential
        $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId
        $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id

        # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
        Sleep -s 15
        $NewRole = New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6)
        {
           Sleep -s 10
           New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
           $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
           $Retries++;
        }
           return $Application.ApplicationId.ToString();
        }

        function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName,[string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
        $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force   
        Remove-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
        New-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
        }

        function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
        Remove-AzureRmAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
        }

        Import-Module AzureRM.Profile
        Import-Module AzureRM.Resources

        $AzureRMProfileVersion= (Get-Module AzureRM.Profile).Version
        if (!(($AzureRMProfileVersion.Major -ge 2 -and $AzureRMProfileVersion.Minor -ge 1) -or ($AzureRMProfileVersion.Major -gt 2)))
        {
           Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
           return
        }

        Login-AzureRmAccount -EnvironmentName $EnvironmentName
        $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId

        # Create a Run As account by using a service principal
        $CertifcateAssetName = "AzureRunAsCertificate"
        $ConnectionAssetName = "AzureRunAsConnection"
        $ConnectionTypeName = "AzureServicePrincipal"

        if ($EnterpriseCertPathForRunAsAccount -and $EnterpriseCertPlainPasswordForRunAsAccount) {
        $PfxCertPathForRunAsAccount = $EnterpriseCertPathForRunAsAccount
        $PfxCertPlainPasswordForRunAsAccount = $EnterpriseCertPlainPasswordForRunAsAccount
        } else {
          $CertificateName = $AutomationAccountName+$CertifcateAssetName
          $PfxCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".pfx")
          $PfxCertPlainPasswordForRunAsAccount = $SelfSignedCertPlainPassword
          $CerCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".cer")
          CreateSelfSignedCertificate $KeyVaultName $CertificateName $PfxCertPlainPasswordForRunAsAccount $PfxCertPathForRunAsAccount $CerCertPathForRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
        }

        # Create a service principal
        $PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
        $ApplicationId=CreateServicePrincipal $PfxCert $ApplicationDisplayName

        # Create the Automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $CertifcateAssetName $PfxCertPathForRunAsAccount $PfxCertPlainPasswordForRunAsAccount $true

        # Populate the ConnectionFieldValues
        $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
        $TenantID = $SubscriptionInfo | Select TenantId -First 1
        $Thumbprint = $PfxCert.Thumbprint
        $ConnectionFieldValues = @{"ApplicationId" = $ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Thumbprint; "SubscriptionId" = $SubscriptionId}

        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ConnectionAssetName $ConnectionTypeName $ConnectionFieldValues

        if ($CreateClassicRunAsAccount) {
            # Create a Run As account by using a service principal
            $ClassicRunAsAccountCertifcateAssetName = "AzureClassicRunAsCertificate"
            $ClassicRunAsAccountConnectionAssetName = "AzureClassicRunAsConnection"
            $ClassicRunAsAccountConnectionTypeName = "AzureClassicCertificate "
            $UploadMessage = "Please upload the .cer format of #CERT# to the Management store by following the steps below." + [Environment]::NewLine +
                    "Log in to the Microsoft Azure Management portal (https://manage.windowsazure.com) and select Settings -> Management Certificates." + [Environment]::NewLine +
                    "Then click Upload and upload the .cer format of #CERT#"

             if ($EnterpriseCertPathForClassicRunAsAccount -and $EnterpriseCertPlainPasswordForClassicRunAsAccount ) {
             $PfxCertPathForClassicRunAsAccount = $EnterpriseCertPathForClassicRunAsAccount
             $PfxCertPlainPasswordForClassicRunAsAccount = $EnterpriseCertPlainPasswordForClassicRunAsAccount
             $UploadMessage = $UploadMessage.Replace("#CERT#", $PfxCertPathForClassicRunAsAccount)
        } else {
             $ClassicRunAsAccountCertificateName = $AutomationAccountName+$ClassicRunAsAccountCertifcateAssetName
             $PfxCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".pfx")
             $PfxCertPlainPasswordForClassicRunAsAccount = $SelfSignedCertPlainPassword
             $CerCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".cer")
             $UploadMessage = $UploadMessage.Replace("#CERT#", $CerCertPathForClassicRunAsAccount)
             CreateSelfSignedCertificate $KeyVaultName $ClassicRunAsAccountCertificateName $PfxCertPlainPasswordForClassicRunAsAccount $PfxCertPathForClassicRunAsAccount $CerCertPathForClassicRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
        }

        # Create the Automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false

        # Populate the ConnectionFieldValues
        $SubscriptionName = $subscription.Subscription.SubscriptionName
        $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName $ClassicRunAsAccountConnectionFieldValues

        Write-Host -ForegroundColor red $UploadMessage
        }

2. Sur votre ordinateur, cliquez sur **Démarrer**, puis démarrez **Windows PowerShell** avec des droits de l’utilisateur élevés.

3. À partir de l’interface de ligne de commande PowerShell avec élévation de privilèges, accédez au dossier contenant le script que vous avez créé à l’étape 1.

4. Exécutez le script en utilisant les valeurs de paramètre pour la configuration dont vous avez besoin.

    **Créer un compte d’identification à l’aide d’un certificat auto-signé**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false`

    **Créer un compte d’identification standard et un compte d’identification Classic à l’aide d’un certificat auto-signé**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true`

    **Créer un compte d’identification standard et un compte d’identification Classic à l’aide d’un certificat d’entreprise**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>`

    **Créer un compte d’identification standard et un compte d’identification Classic à l’aide d’un certificat auto-signé dans le cloud Azure Government**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment`

    > [!NOTE]
    > Une fois le script exécuté, vous êtes invité à vous authentifier auprès d’Azure. Connectez-vous avec un compte membre du rôle Administrateurs des abonnements et coadministrateur de l’abonnement.
    >
    >

Une fois le script exécuté, notez les points suivants :
* Si vous avez créé un compte d’identification Classic avec un certificat public auto-signé (fichier .cer), le script le crée et l’enregistre dans le dossier de fichiers temporaires sur votre ordinateur, sous le profil d’utilisateur *%USERPROFILE%\AppData\Local\Temp* utilisé pour exécuter la session PowerShell.
* Si vous avez créé un compte d’identification Classic avec un certificat public d’entreprise (fichier .cer), utilisez ce certificat. Suivez les instructions pour [charger un certificat d’API de gestion vers le portail Azure Classic](../azure-api-management-certs.md), puis validez la configuration des informations d’identification avec les ressources de gestion des services à l’aide de [l’exemple de code pour l’authentification avec les ressources de gestion des services](#sample-code-to-authenticate-with-service-management-resources). 
* Si vous n’avez *pas* créé de compte d’identification Classic, authentifiez-vous avec des ressources Resource Manager et validez la configuration des informations d’identification à l’aide [l’exemple de code pour l’authentification avec les ressources de gestion des services](#sample-code-to-authenticate-with-resource-manager-resources).

## <a name="sample-code-to-authenticate-with-resource-manager-resources"></a>Exemple de code pour l’authentification avec des ressources Azure Resource Manager
Vous pouvez utiliser l’exemple de code mis à jour suivant, extrait de l’exemple de Runbook *AzureAutomationTutorialScript*, pour procéder à une authentification avec le compte d’identification pour gérer les ressources Azure Resource Manager avec vos Runbooks.

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

Afin de faciliter le travail entre plusieurs abonnements, le script inclut deux lignes de code supplémentaires pour prendre en charge le référencement d’un contexte d’abonnement. La ressource variable *SubscriptionId* contient l’ID de l’abonnement. Après l’instruction d’applet de commande `Add-AzureRmAccount`, l’applet de commande [`Set-AzureRmContext`](https://msdn.microsoft.com/library/mt619263.aspx) est indiqué avec le jeu de paramètres *-SubscriptionId*. Si le nom de la variable est trop générique, vous pouvez le modifier afin qu’il comprenne un préfixe ou utiliser une autre convention d’affectation de noms pour faciliter son identification. Vous pouvez également utiliser le jeu de paramètres *-SubscriptionName* au lieu de *-SubscriptionId* avec la ressource de variable correspondante.

L’applet de commande utilisée pour l’authentification dans le Runbook, `Add-AzureRmAccount`, utilise le jeu de paramètres *ServicePrincipalCertificate*. Elle effectue l’authentification à l’aide du certificat du principal du service, et non des informations d’identification de l’utilisateur.

## <a name="sample-code-to-authenticate-with-service-management-resources"></a>Exemple de code pour l’authentification avec les ressources de gestion des services
Vous pouvez utiliser l’exemple de code mis à jour suivant, extrait de l’exemple de Runbook *AzureClassicAutomationTutorialScript*, pour procéder à une authentification avec le compte d’identification Classic pour gérer les ressources classiques avec vos Runbooks.

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
* [Objets application et principal du service dans Azure AD](../active-directory/active-directory-application-objects.md)
* [Contrôle d’accès en fonction du rôle dans Azure Automation](automation-role-based-access-control.md)
* [Vue d’ensemble des certificats pour Azure Cloud Services](../cloud-services/cloud-services-certs-create.md)

