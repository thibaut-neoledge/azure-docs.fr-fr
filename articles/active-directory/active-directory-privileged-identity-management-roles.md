<properties
   pageTitle="Rôles dans PIM | Microsoft Azure"
   description="Découvrez les rôles utilisés pour les identités dotées de privilèges avec l’extension Azure Privileged Identity Management."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="03/17/2016"
   ms.author="kgremban"/>

# Rôles Azure AD Privileged Identity Management

<!-- **PLACEHOLDER: Need description of how this works. Azure PIM uses roles from MSODS objects.**-->

## Rôles d’Azure Active Directory, d’Office 365 et d’autres sources

Azure Privileged Identity Management (PIM) utilise les rôles suivants en tant que rôles d’administrateur par défaut :

- Administrateur général
- Administrateur de facturation
- Administrateur de services
- Administrateur d'utilisateurs
- Administrateur de mots de passe

Pour plus d’informations sur les rôles dans Office 365, Exchange Online, Sharepoint Online et Skype Entreprise, accédez à [Attribution de rôles d’administrateur dans Office 365](https://support.office.com/fr-FR/article/Assigning-admin-roles-in-Office-365-eac4d046-1afd-4f1a-85fc-8219c79e1504?ui=fr-FR&rs=fr-FR&ad=US).

<!--**PLACEHOLDER: The above article may not be the one we want since PIM gets roles from places other that Office 365**-->


<!-- ## The PIM Security Administrator Role **PLACEHOLDER: Need description of the Security Administrator role.**-->

## Rôles d’utilisateur et connexion
> [AZURE.NOTE]Un utilisateur désireux de se connecter à Azure PIM doit disposer d’une licence pour Azure.

## Attribution d’une licence à un utilisateur dans Azure AD

> [AZURE.NOTE] L’option de licence s’affiche uniquement s’il existe réellement des licences pour cet abonnement.

1. Avec un compte d’administrateur général ou un compte de coadministrateur, connectez-vous à [http://manage.windowsazure.com](http://manage.windowsazure.com).
2. Dans le menu principal, cliquez sur **Tous les éléments**.
3. Sélectionnez l’annuaire que vous souhaitez utiliser et auquel des licences sont associées.
4. Cliquez sur **Licences**. La liste des licences disponibles s’affiche.
5. Cliquez sur le plan de licence contenant les licences que vous souhaitez distribuer.
6. Cliquez sur **Affecter des utilisateurs**.
7. Sélectionnez l’utilisateur auquel vous souhaitez affecter une licence.
8. Cliquez sur le bouton **Affecter**. L’utilisateur peut à présent ouvrir une session Azure.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0323_2016-->