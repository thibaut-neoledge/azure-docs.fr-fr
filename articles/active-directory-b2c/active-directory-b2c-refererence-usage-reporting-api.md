---
title: "Définitions et exemples de l’API Rapports d’utilisation Azure AD B2C | Microsoft Docs"
description: "Guide et exemples sur la façon d’obtenir des rapports sur les utilisateurs, les authentifications et l’authentification multifacteur pour le client B2C."
services: active-directory-b2c
documentationcenter: dev-center-name
author: rojasja
manager: mbaldwin
ms.service: active-directory-b2c
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/08/2017
ms.author: joroja
ms.translationtype: Human Translation
ms.sourcegitcommit: 4b2cbf5f62ee63bada42b2a06506b793d4349fdb
ms.openlocfilehash: 7db9a45a4c80ea8d01937837dfa7a15c171fb66b
ms.contentlocale: fr-fr
ms.lasthandoff: 02/10/2017


---
# <a name="accessing-usage-reports-in-azure-ad-b2c-via-the-reporting-api"></a>Accès aux rapports d’utilisation dans Azure AD B2C via l’API de création de rapports

Azure Active Directory B2C fournit l’authentification de la connexion et l’authentification MFA pour tous les utilisateurs finaux de votre famille d’applications entre plusieurs fournisseurs d’identité.  Le fait de connaître le nombre d’utilisateurs inscrits dans le client, les fournisseurs qu’ils ont utilisé pour s’enregistrer et le nombre d’authentifications par type, permet de répondre à des questions telles que les suivantes :
* Combien d’utilisateurs de chaque type de fournisseur d’identité (par exemple, compte Microsoft, LinkedIn) se sont inscrits au cours des 10 derniers jours ?
* Combien d’authentifications multifacteur ont réussi au cours du mois dernier ?
* Combien d’authentifications basées sur la connexion ont été effectuées ce mois-ci ? Par jour ? Par application ?
* Comment puis-je estimer le coût mensuel prévu de l’activité de mon client B2C ?

Cet article se concentre sur les rapports plus étroitement liées à l’activité de facturation, qui est basée sur le nombre d’utilisateurs, le nombre d’authentifications facturables basées sur la connexion et le nombre d’authentifications multifacteur.


## <a name="prerequisites-to-access-the-azure-ad-reporting-api"></a>Configuration requise pour accéder à l’API de création de rapports Azure AD
Avant de commencer, vous devez respecter la [configuration requise pour accéder à l’API de création de rapports Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/).  Créez une application, obtenez une clé secrète pour celle-ci et accordez-lui des autorisations d’accès aux rapports de votre client Azure AD B2C. Des exemples *Script Bash* et *Script Python* sont également fournis ici.

## <a name="powershell-script"></a>Script PowerShell
Ce script illustre les quatre rapports d’utilisation utilisant le paramètre **TimeStamp** et le filtre **- ApplicationId**.

```
# This script will require the Web Application and permissions setup in Azure Active Directory

# Constants
$ClientID      = "your-client-application-id-here"  
$ClientSecret  = "your-client-application-secret-here"
$loginURL      = "https://login.windows.net"
$tenantdomain  = "your-b2c-tenant-domain.onmicrosoft.com"  
# Get an Oauth 2 access token based on client id, secret and tenant domain
$body          = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth         = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body
if ($oauth.access_token -ne $null) {
    $headerParams  = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

    Write-host Data from the tenantUserCount report
    Write-host ====================================================
     # Returns a JSON document for the report
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/tenantUserCount?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the tenantUserCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/tenantUserCount?%24filter=TimeStamp+gt+2016-10-15&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCountSummary report
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCountSummary?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCount?%24filter=TimeStamp+gt+2016-09-20+and+TimeStamp+lt+2016-10-03&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCount report with ApplicationId filter
    Write-host ====================================================
    # Returns a JSON document for the " " report
        $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCount?%24filter=ApplicationId+eq+ada78934-a6da-4e69-b816-10de0d79db1d&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCountSummary
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCountSummary?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCount?%24filter=TimeStamp+gt+2016-09-10+and+TimeStamp+lt+2016-10-04&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCount report with ApplicationId filter
    Write-host ====================================================
       $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCountSummary?%24filter=ApplicationId+eq+ada78934-a6da-4e69-b816-10de0d79db1d&api-version=beta")
     Write-host $myReport.Content

} else {
    Write-Host "ERROR: No Access Token"
    }
```


## <a name="usage-report-definitions"></a>Définitions de rapport d’utilisation
**tenantUserCount** : le nombre d’utilisateurs dans le client par type de fournisseur d’identité par jour au cours des 30 derniers jours. (Facultatif : un filtre TimeStamp fournit le nombre d’utilisateurs à partir d’une date spécifiée jusqu’à la date actuelle). Le rapport fournit les éléments suivants :
 * TotalUserCount = nombre de tous les objets utilisateur
 * OtherUserCount = nombre d’utilisateurs AAD Directory (utilisateurs non B2C)
 * LocalUserCount = nombre de comptes utilisateur B2C créés avec les informations d’identification locales au client B2C

**AlternateIdUserCount** = nombre d’utilisateurs B2C inscrits auprès de fournisseurs d’identité externes (par exemple, Facebook, compte Microsoft, autres clients AAD - également appelé OrgId)

**b2cAuthenticationCountSummary** : additionner le nombre journalier d’authentifications facturables au cours des 30 derniers jours, par jour et par type de flux d’authentification

**b2cAuthenticationCount** : compter le nombre d’authentifications dans une période de temps définie. Valeur par défaut : 30 derniers jours.  (Facultatif : les paramètres TimeStamp de début et de fin définissent une période spécifique des décomptes souhaités) La sortie inclut StartTimeStamp (première date d’activité pour ce client) et EndTimeStamp (dernière mise à jour)

**b2cMfaRequestCountSummary** : additionner le nombre journalier d’authentifications multifacteur, par jour et par type d’authentification multifacteur (SMS ou vocale)


## <a name="limitations"></a>Limitations
* Les données du décompte d’utilisateurs sont actualisées toutes les 24 à 48 heures.  Les authentifications sont mises à jour plusieurs fois par jour.
* Lorsque vous utilisez le filtre ApplicationId, une réponse de rapport vide peut être due à l’une des conditions suivantes :
 * L’ID d’application n’existe pas dans le client. Assurez-vous qu’il est correct.
 * L’ID d’application existe, mais aucune donnée n’a été trouvée dans la période du rapport. Passez en revue vos paramètres d’heure et de date.


## <a name="next-steps"></a>Étapes suivantes
### <a name="estimating-your-azure-ad-monthly-bill"></a>Estimation de votre facture mensuelle Azure AD.
Lorsque vous utilisez également [la tarification Azure AD B2C la plus récente disponible](https://azure.microsoft.com/pricing/details/active-directory-b2c/), vous pouvez estimer la consommation Azure quotidienne, hebdomadaire et mensuelle.  Une estimation est particulièrement utile lorsque vous planifiez des changements de comportement du client qui peuvent avoir un impact sur le coût global.  Vous pouvez examiner les coûts réels dans votre [abonnement Azure lié.](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing)

### <a name="options-for-other-output-formats"></a>Options pour les autres formats de sortie
```
# to output to JSON use following line in the PowerShell sample
$myReport.Content | Out-File -FilePath b2cUserJourneySummaryEvents.json -Force

# to output the content to a name value list
($myReport.Content | ConvertFrom-Json).value | Out-File -FilePath name-your-file.txt -Force

# to output the content in XML use the following line
(($myReport.Content | ConvertFrom-Json).value | ConvertTo-Xml).InnerXml | Out-File -FilePath name-your-file.xml -Force
```


<!--Reference style links - using these makes the source content way more readable than using inline links-->
[gog]: http://google.com/        
[yah]: http://search.yahoo.com/  
[msn]: http://search.msn.com/    

