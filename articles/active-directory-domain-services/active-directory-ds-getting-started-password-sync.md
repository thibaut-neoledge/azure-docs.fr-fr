<properties
	pageTitle="Version préliminaire des services de domaine Azure Active Directory : prise en main | Microsoft Azure"
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
	ms.date="01/26/2016"
	ms.author="maheshu"/>

# Services de domaine Azure AD *(version préliminaire)* : prise en main

## Étape 5 : activer la synchronisation de mot de passe
Une fois que vous avez activé les services de domaine Azure AD pour votre client Azure AD, l’étape suivante consiste à activer la synchronisation de mot de passe. Cette opération permet aux utilisateurs de se connecter au domaine à l’aide de leurs informations d’identification d’entreprise.

Les étapes sont différentes selon que votre organisation est un client Azure AD uniquement dans le cloud ou est paramétrée de manière à se synchroniser avec votre annuaire local à l’aide d’Azure AD Connect.

### Clients uniquement dans le cloud - Génération du hachage des informations d'identification NTLM et Kerberos dans Azure AD
Si votre organisation est un client Azure AD uniquement dans le cloud, les utilisateurs ayant besoin d’utiliser les services de domaine Azure AD doivent modifier leur mot de passe. Cette étape entraîne la génération, dans Azure AD, des hachages des informations d’identification dont ont besoin les services de domaine Azure AD pour l’authentification Kerberos et NTLM. Vous pouvez faire expirer les mots de passe de tous les utilisateurs dans le locataire qui doivent recourir aux services de domaine Azure AD, ou demander à ces utilisateurs de modifier leur mot de passe.

Voici les instructions que vous devez fournir aux utilisateurs pour qu’ils modifient leur mot de passe :

1. Accédez à la page du volet d’accès Azure AD pour votre organisation. Cette page est généralement disponible à l’adresse [http://myapps.microsoft.com](http://myapps.microsoft.com).
2. Dans cette page, sélectionnez l’onglet **profil**.
3. Cliquez sur la vignette **Modifier le mot de passe** dans cette page pour initier un changement de mot de passe.

    ![Créer un réseau virtuel pour les services de domaine Azure AD.](./media/active-directory-domain-services-getting-started/user-change-password.png)

4. La page **Modifier le mot de passe** apparaît alors. Les utilisateurs peuvent entrer leur mot de passe existant (ancien), puis poursuivre la modification de leur mot de passe.

    ![Créer un réseau virtuel pour les services de domaine Azure AD.](./media/active-directory-domain-services-getting-started/user-change-password2.png)

Une fois que les utilisateurs ont modifié leur mot de passe, le nouveau mot de passe est rapidement utilisable dans les services de domaine Azure AD. Après quelques minutes, les utilisateurs peuvent se connecter aux ordinateurs joints au domaine géré à l'aide de leur nouveau mot de passe.


### Clients synchronisés - Activer la synchronisation des hachages des informations d'identification NTLM et Kerberos avec Azure AD
Si le client Azure AD pour votre organisation est défini de manière à se synchroniser avec votre annuaire local à l’aide d’Azure AD Connect, vous devez configurer Azure AD Connect pour synchroniser les hachages d’informations d’identification nécessaires pour l’authentification NTLM et Kerberos. Ces hachages ne sont pas synchronisés avec Azure AD par défaut ; les étapes suivantes vous indiquent comment synchroniser les hachages avec votre client Azure AD.

#### Installer ou mettre à jour Azure AD Connect

Vous devez installer la dernière version recommandée d’Azure AD Connect sur un ordinateur joint au domaine. Si une instance d’Azure AD Connect est déjà configurée, vous devez la mettre à jour pour utiliser la build de disponibilité générale d’Azure AD Connect. Veillez à utiliser la dernière version d’Azure AD Connect afin d’éviter les problèmes/bogues connus.

**[Télécharger Azure AD Connect](http://www.microsoft.com/download/details.aspx?id=47594)**

Version minimale recommandée : **1.0.9131** (publiée le 3 décembre 2015).

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

<!---HONumber=AcomDC_0211_2016-->