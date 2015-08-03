
<properties
    pageTitle="Modification du client Azure Active Directory dans Azure RemoteApp"
    description="Apprenez à modifier le client Azure Active Directory associé à Azure RemoteApp"
    services="remoteapp"
	documentationCenter="" 
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/24/2015"
    ms.author="elizapo" />



# Modification du client Azure Active Directory dans Azure RemoteApp

Azure RemoteApp utilise Azure Active Directory (Azure AD) pour autoriser l’accès utilisateur. Le seul locataire Azure AD que vous pouvez utiliser est celui associé à l’abonnement Azure. Vous pouvez afficher l’abonnement associé dans la page Paramètres du portail. Examinez la colonne Annuaire dans l’onglet Abonnements.

> [AZURE.NOTE]Pour que la modification d’Azure Active Directory réussisse, vous devez tout d’abord supprimer tous les utilisateurs du client Azure Active Directory existant de toutes les collections Azure RemoteApp. Pour cela, accédez au portail Azure puis à l’onglet Azure RemoteApp et ouvrez chaque collection Azure RemoteApp. Accédez à l’onglet **Utilisateurs** et supprimez les utilisateurs qui appartiennent à votre client Azure Active Directory actuel. Répétez l’opération pour toutes les collections Azure RemoteApp. Sans cela, vous ne serez pas en mesure de créer des collections ni de les corriger.

Si vous souhaitez utiliser un autre locataire, suivez ces étapes pour modifier l’association avec votre abonnement :

1. Dans le portail, supprimez les utilisateurs Azure AD auxquels vous avez donné accès aux collections Azure RemoteApp.


2. Définissez un compte Microsoft (anciennement appelé Live ID) comme administrateur du service. (Consultez **Paramètres -> Administrateurs**.)
	1. Cliquez sur l’utilisateur actuellement connecté dans le coin supérieur droit, puis cliquez sur **Afficher ma facture**.
	2. Sélectionnez votre abonnement, puis cliquez sur **Modifier les données d’abonnement**.
	3. Apportez les modifications nécessaires.



3. Déconnectez-vous du portail, puis reconnectez-vous avec le compte Microsoft que vous avez spécifié à l’étape précédente.


4. Cliquez sur **Nouveau -> App Services -> Active Directory -> Création personnalisée -> Utiliser un annuaire existant**. Ajoutez le locataire Azure AD que vous souhaitez associer à cet abonnement.


5. Sous **Paramètres -> Abonnements**, sélectionnez votre abonnement, puis cliquez sur **Modifier l’annuaire**. Sélectionnez le locataire Azure AD à utiliser.



Vous pouvez à présent utiliser le client Azure AD pour contrôler l’accès à l’abonnement Azure et configurer l’accès utilisateur dans Azure RemoteApp.

<!---HONumber=July15_HO4-->