<properties
	pageTitle="Services de domaine : Activer la synchronisation de mot de passe | Microsoft Azure"
	description="Prise en main des services de domaine Azure Active Directory"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/25/2016"
	ms.author="maheshu"/>

# Services de domaine Azure AD *(Version préliminaire)* : Activer la synchronisation du mot de passe pour les services de domaine Azure AD

## Tâche 5 : activer la synchronisation de mot de passe pour les services de domaine AAD pour un client Azure AD synchronisé
Une fois que vous avez activé les services de domaine Azure AD pour votre répertoire Azure AD, la tâche suivante consiste à activer la synchronisation de mots de passe pour les services de domaine Azure AD. Cette opération permet aux utilisateurs de se connecter au domaine à l’aide de leurs informations d’identification d’entreprise.

Les étapes sont différentes selon que votre organisation dispose d’un répertoire Azure AD uniquement dans le cloud ou est paramétrée de manière à se synchroniser avec votre répertoire local à l’aide d’Azure AD Connect.

<br>

> [AZURE.SELECTOR]
- [Répertoire Azure AD uniquement dans le cloud](active-directory-ds-getting-started-password-sync.md)
- [Répertoire Azure AD synchronisé](active-directory-ds-getting-started-password-sync-synced-tenant.md)

<br>

### Clients synchronisés - Activer la synchronisation des hachages des informations d'identification NTLM et Kerberos avec Azure AD
Si le client Azure AD pour votre organisation est défini de manière à se synchroniser avec votre annuaire local à l’aide d’Azure AD Connect, vous devez configurer Azure AD Connect pour synchroniser les hachages d’informations d’identification nécessaires pour l’authentification NTLM et Kerberos. Ces hachages ne sont pas synchronisés avec Azure AD par défaut ; les étapes suivantes vous indiquent comment synchroniser les hachages avec votre client Azure AD.

#### Installer ou mettre à jour Azure AD Connect

Vous devez installer la dernière version recommandée d’Azure AD Connect sur un ordinateur joint au domaine. Si une instance d’Azure AD Connect est déjà configurée, vous devez la mettre à jour pour utiliser la build de disponibilité générale d’Azure AD Connect. Veillez à utiliser la dernière version d’Azure AD Connect afin d’éviter les problèmes/bogues connus déjà corrigés.

**[Télécharger Azure AD Connect](http://www.microsoft.com/download/details.aspx?id=47594)**

Version minimale recommandée : **1.1.130.0** (publiée le 12 avril 2016).

  > [AZURE.WARNING] Vous DEVEZ installer la dernière version recommandée d’Azure AD Connect pour que les informations d’identification de mot de passe héritées (nécessaires pour l’authentification NTLM et Kerberos) puissent être synchronisées avec votre locataire Azure AD. Cette fonctionnalité n’est pas disponible dans les versions antérieures d’Azure AD Connect ou avec l’outil DirSync hérité.

Les instructions d’installation d’Azure AD Connect sont disponibles dans l’article suivant : [Prise en main d’Azure AD Connect](../active-directory/active-directory-aadconnect.md).


#### Forcer la synchronisation de mot de passe complète avec Azure AD

Pour forcer la synchronisation de mot de passe complète et permettre aux hachages de mot de passe de tous les utilisateurs locaux (y compris les hachages d’informations d’identification nécessaires pour l’authentification NTLM/Kerberos) de se synchroniser avec votre client Azure AD, exécutez le script PowerShell suivant sur chaque forêt Active Directory.

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"  
$azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"  
Import-Module adsync  
$c = Get-ADSyncConnector -Name $adConnector  
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1  
$c.GlobalParameters.Remove($p.Name)  
$c.GlobalParameters.Add($p)  
$c = Add-ADSyncConnector -Connector $c  
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false   
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true  
```

En fonction de la taille de votre annuaire (nombre d’utilisateurs, de groupes, etc.), la synchronisation des informations d’identification avec Azure AD peut prendre du temps. Les mots de passe seront utilisables sur le domaine géré de Services de domaine Azure AD peu après la synchronisation des hachages d'informations d'identification avec Azure AD.


<br>

## Contenu connexe

- [Activer la synchronisation de mot de passe pour les services de domaine AAD pour un répertoire Azure AD uniquement dans le cloud](active-directory-ds-getting-started-password-sync.md)

- [Administrer un domaine géré par les services de domaine Azure Active Directory](active-directory-ds-admin-guide-administer-domain.md)

- [Joindre une machine virtuelle Windows à un domaine géré par les services de domaine Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)

- [Joindre une machine virtuelle Linux Red Hat Enterprise à un domaine géré par les services de domaine Azure AD](active-directory-ds-admin-guide-join-rhel-linux-vm.md)

<!---HONumber=AcomDC_0427_2016-->