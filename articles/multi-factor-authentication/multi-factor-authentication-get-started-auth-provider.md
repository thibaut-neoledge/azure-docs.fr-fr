<properties
	pageTitle="Prise en main du fournisseur Microsoft Azure Multi-Factor Auth"
	description="Découvrez comment créer un fournisseur Azure Multi-Factor Auth."
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="curtand"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/04/2016"
	ms.author="kgremban"/>



# Prise en main du fournisseur Azure Multi-Factor Auth
L’authentification multifacteur est disponible par défaut pour les administrateurs généraux disposant d’Azure Active Directory et les utilisateurs Office 365. Toutefois, si vous souhaitez tirer parti des [fonctionnalités avancées](multi-factor-authentication-whats-next.md), vous devez acheter la version complète de l’authentification multifacteur Azure.

> [AZURE.NOTE]  Un fournisseur d’authentification multifacteur Azure permet de tirer parti des fonctionnalités fournies par la version complète de l’authentification multifacteur Azure. Il s’adresse aux utilisateurs **qui ne possèdent pas de licences via l’authentification multifacteur Azure (MFA), Azure AD Premium ou EMS**. L’authentification multifacteur Azure, Azure AD Premium et EMS incluent la version complète de l’authentification Multifacteur Azure par défaut. Vous n’avez pas besoin d’un fournisseur d’authentification multifacteur Azure si vous possédez des licences.

Il sera en revanche nécessaire si vous souhaitez télécharger le Kit de développement logiciel (SDK).

> [AZURE.IMPORTANT]  Si vous souhaitez télécharger le Kit de développement logiciel (SDK), vous devrez créer un fournisseur d’authentification multifacteur Azure, même si vous disposez de licences EMS, AAD Premium ou Azure MFA. Si vous créez un fournisseur d’authentification multifacteur Azure à cet effet et que vous possédez déjà des licences, vous devez créer le fournisseur à l’aide du modèle **Par utilisateur activé** et lier le fournisseur au répertoire qui contient les licences EMS, Azure AD Premium ou Azure MFA. Cela garantit que vous ne serez pas facturé à moins d’avoir plus d’utilisateurs uniques utilisant le Kit de développement logiciel (SDK) que de licences possédées.

Suivez les étapes ci-dessous pour créer un fournisseur Azure Multi-Factor Auth.

## Pour créer un fournisseur Multi-Factor Auth
--------------------------------------------------------------------------------

1. Ouvrez une session en tant qu’administrateur sur le **portail Azure Classic**.
2. Sélectionnez **Active Directory** à gauche.
3. En haut de la page Active Directory, sélectionnez **Multi-Factor Authentication Providers** (Fournisseurs Multi-Factor Authentication). ![Création d’un fournisseur d’authentification Multifacteur](./media/multi-factor-authentication-get-started-auth-provider/authprovider1.png)
4. Cliquez sur **Nouveau** au bas de la page. ![Création d’un fournisseur d’authentification Multifacteur](./media/multi-factor-authentication-get-started-auth-provider/authprovider2.png)
5. Sous **App Services**, sélectionnez **Fournisseur Multi-Factor Authentication**. ![Création d’un fournisseur d’authentification Multifacteur](./media/multi-factor-authentication-get-started-auth-provider/authprovider3.png)
6. Sélectionnez **Création rapide**. ![Création d’un fournisseur d’authentification Multifacteur](./media/multi-factor-authentication-get-started-auth-provider/authprovider4.png)
5. Renseignez les champs suivants et sélectionnez **Créer**.
	1. **Nom** : nom du fournisseur Multi-Factor Authentication.
	2. **Modèle d’utilisation** : modèle d’utilisation du fournisseur Multi-Factor Authentication.
		- Par authentification – modèle d'achat facturé par authentification. Généralement utilisé pour les scénarios qui utilisent Azure Multi-Factor Authentication dans une application orientée client.
		- Par utilisateur activé – modèle d'achat facturé par utilisateur activé. Généralement utilisé pour l’accès des employés aux applications telles qu’Office 365.
	2. **Répertoire** : locataire Azure Active Directory auquel le fournisseur Multi-Factor Authentication est associé. Soyez conscient des éléments suivants :
		- Vous n'avez pas besoin d’un répertoire Azure AD pour créer un fournisseur Multi-Factor Auth. Laissez ce champ vide si vous prévoyez uniquement d’utiliser le serveur Azure Multi-Factor Authentication ou le kit de développement logiciel (SDK).
		- Le fournisseur Multi-Factor Auth doit être associé avec un répertoire Azure AD pour tirer parti des fonctionnalités avancées.
		- Azure AD Connect, AAD Sync ou DirSync ne sont nécessaires que si vous synchronisez votre environnement Active Directory local avec un annuaire Azure AD. Si vous utilisez uniquement un annuaire Azure AD non synchronisé, la synchronisation n’est pas requise. ![Création d’un fournisseur d’authentification Multifacteur](./media/multi-factor-authentication-get-started-auth-provider/authprovider5.png)
5. Une fois que vous cliquez sur Créer, le fournisseur Multi-Factor Authentication est créé et vous devriez voir un message indiquant : **Le fournisseur Multi-Factor Authentication a été créé correctement**. Cliquez sur **OK**. ![Création d’un fournisseur d’authentification Multifacteur](./media/multi-factor-authentication-get-started-auth-provider/authprovider6.png)

<!---HONumber=AcomDC_0921_2016-->