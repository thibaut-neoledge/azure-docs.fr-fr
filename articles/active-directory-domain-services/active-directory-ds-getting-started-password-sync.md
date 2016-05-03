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

## Tâche 5 : activer la synchronisation de mot de passe pour les services de domaine AAD pour un répertoire Azure AD uniquement dans le cloud
Une fois que vous avez activé les services de domaine Azure AD pour votre client Azure AD, la tâche suivante consiste à activer la synchronisation des informations d’identification pour les services de domaine Azure AD. Cette opération permet aux utilisateurs de se connecter au domaine géré à l’aide de leurs informations d’identification d’entreprise.

Les étapes sont différentes selon que votre organisation dispose d’un répertoire Azure AD uniquement dans le cloud ou est paramétrée de manière à se synchroniser avec votre répertoire local à l’aide d’Azure AD Connect.

<br>

> [AZURE.SELECTOR]
- [Répertoire Azure AD uniquement dans le cloud](active-directory-ds-getting-started-password-sync.md)
- [Répertoire Azure AD synchronisé](active-directory-ds-getting-started-password-sync-synced-tenant.md)

<br>

### Activer la génération du hachage des informations d’identification NTLM et Kerberos dans un répertoire Azure AD uniquement dans le cloud
Si votre organisation dispose d’un répertoire Azure AD uniquement dans le cloud, les utilisateurs ayant besoin d’utiliser les services de domaine Azure AD doivent modifier leur mot de passe. Ce processus de modification du mot de passe entraîne la génération, dans Azure AD, des hachages des informations d’identification dont ont besoin les services de domaine Azure AD pour l’authentification Kerberos et NTLM. Vous pouvez faire expirer les mots de passe de tous les utilisateurs dans le locataire qui doivent recourir aux services de domaine Azure AD, ou demander à ces utilisateurs de modifier leur mot de passe.

Voici les instructions que vous devez fournir aux utilisateurs finaux pour qu’ils modifient leur mot de passe :

1. Accédez à la page du volet d’accès Azure AD pour votre organisation. Cette page est généralement disponible à l’adresse [http://myapps.microsoft.com](http://myapps.microsoft.com).

2. Dans cette page, sélectionnez l’onglet **profil**.

3. Cliquez sur la vignette **Modifier le mot de passe** dans cette page pour initier un changement de mot de passe.

    ![Créer un réseau virtuel pour les services de domaine Azure AD.](./media/active-directory-domain-services-getting-started/user-change-password.png)

4. La page **Modifier le mot de passe** apparaît alors. Les utilisateurs peuvent entrer leur mot de passe existant (ancien), puis poursuivre la modification de leur mot de passe.

    ![Créer un réseau virtuel pour les services de domaine Azure AD.](./media/active-directory-domain-services-getting-started/user-change-password2.png)

Une fois que les utilisateurs ont modifié leur mot de passe, le nouveau mot de passe est rapidement utilisable dans les services de domaine Azure AD. Après quelques minutes, les utilisateurs peuvent se connecter aux ordinateurs joints au domaine géré à l'aide de leur nouveau mot de passe.


<br>

## Contenu connexe

- [Activer la synchronisation de mot de passe pour les services de domaine AAD pour un répertoire Azure AD synchronisé](active-directory-ds-getting-started-password-sync-synced-tenant.md)

- [Administrer un domaine géré par les services de domaine Azure Active Directory](active-directory-ds-admin-guide-administer-domain.md)

- [Joindre une machine virtuelle Windows à un domaine géré par les services de domaine Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)

- [Joindre une machine virtuelle Linux Red Hat Enterprise à un domaine géré par les services de domaine Azure AD](active-directory-ds-admin-guide-join-rhel-linux-vm.md)

<!---HONumber=AcomDC_0427_2016-->