
<properties 
    pageTitle="Modification du locataire Azure Active Directory dans RemoteApp"
    description="Apprenez à modifier le locataire Azure Active Directory associé à RemoteApp" 
    services="remoteapp" 
    solutions="" documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/19/2015" 
    ms.author="elizapo" />



# Modification du locataire Azure Active Directory dans RemoteApp

RemoteApp utilise Azure Active Directory (Azure AD) pour autoriser l'accès utilisateur. Le seul locataire Azure AD que vous pouvez utiliser est celui associé à l'abonnement Azure. Vous pouvez afficher l'abonnement associé dans la page Paramètres du portail. Examinez la colonne Annuaire dans l'onglet Abonnements. 

Si vous souhaitez utiliser un autre locataire, suivez ces étapes pour modifier l'association avec votre abonnement :

1. Dans le portail, supprimez les utilisateurs Azure AD auxquels vous avez donné accès aux services RemoteApp.


2. Définissez un compte Microsoft (anciennement appelé Live ID) comme administrateur du service. (Consultez **Paramètres -> Administrateurs**.)
	1. Cliquez sur l'utilisateur actuellement connecté dans le coin supérieur droit, puis cliquez sur **Afficher ma facture**.
	2. Sélectionnez votre abonnement, puis cliquez sur **Modifiez les données d'abonnement**.
	3. Apportez les modifications nécessaires.



3. Déconnectez-vous du portail, puis reconnectez-vous avec le compte Microsoft que vous avez spécifié à l'étape précédente.


4. Cliquez sur **Nouveau -> App Services -> Active Directory -> Création personnalisée -> Utiliser un annuaire existant**. Ajoutez le locataire Azure AD que vous souhaitez associer à cet abonnement.


5. Sous **Paramètres -> Abonnements**, sélectionnez votre abonnement, puis cliquez sur **Modifier l'annuaire**. Sélectionnez le locataire Azure AD à utiliser.



Vous pouvez à présent utiliser le locataire Azure AD pour contrôler l'accès à l'abonnement Azure et configurer l'accès utilisateur dans Azure RemoteApp.


<!--HONumber=52-->