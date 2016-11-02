<properties
    pageTitle="Utiliser un client Office 365 avec un abonnement Azure | Microsoft Azure"
    description="Découvrez comment ajouter un annuaire (client) Office 365 à un abonnement Azure afin d’établir l’association."
    services=""
    documentationCenter=""
    authors="JiangChen79"
    manager="mbaldwin"
    editor=""
    tags="billing,top-support-issue"/>

<tags
    ms.service="billing"
    ms.workload="na"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="cjiang"/>


# <a name="associate-an-office-365-tenant-with-an-azure-subscription"></a>Associer un client Office 365 à un abonnement Azure
Si vous avez obtenu les deux abonnements Azure et Office 365 séparément et si vous souhaitez désormais accéder au client Office 365 à partir de l’abonnement Azure, il est facile d’y parvenir. Cet article vous montre comment procéder.

> [AZURE.NOTE] Cet article ne s’applique pas aux clients du Contrat Entreprise (EA).

## <a name="quick-guidance"></a>Conseils rapides
Pour associer votre client Office 365 à l’abonnement Azure, utilisez votre compte Azure pour ajouter votre client Office 365, puis associez votre abonnement Azure à ce dernier.

## <a name="detailed-steps"></a>Procédure détaillée
Dans ce scénario, Kelley Wall est un utilisateur qui possède un abonnement à Azure sous le compte kelley.wall@outlook.com. Kelley possède également un abonnement Office 365 sous le compte kelley.wall@contoso.onmicrosoft.com. Kelley souhaite désormais accéder au client Office 365 avec l’abonnement Azure.

![Capture d’écran du statut d’Azure Active Directory](./media/billing-add-office-365-tenant-to-azure-subscription/s31_msa-aad-status.png)

![Capture d’écran des utilisateurs actifs du centre d’administration Office 365](./media/billing-add-office-365-tenant-to-azure-subscription/s32_office-365-user.png)

### <a name="prerequisites"></a>Conditions préalables
Pour que l’association fonctionne correctement, les conditions préalables suivantes sont nécessaires :

- Vous avez besoin des informations d’identification de l’administrateur de service de l’abonnement Azure. Les coadministrateurs ne peuvent pas exécuter un sous-ensemble des étapes.
- Vous avez besoin des informations d’identification d’un administrateur général du client Office 365.
- L’adresse électronique de l’administrateur de service ne doit pas figurer dans le client Office 365.
- L’adresse électronique de l’administrateur de service ne doit pas correspondre à celle de l’administrateur général du client Office 365.
- Si vous utilisez actuellement une adresse électronique qui est à la fois un compte Microsoft et un compte professionnel, modifiez temporairement l’administrateur de service de votre abonnement Azure afin d’utiliser un autre compte Microsoft. Vous pouvez créer un compte Microsoft à la [page d’inscription de compte Microsoft](https://signup.live.com/).


Pour changer votre administrateur de service, procédez comme suit :

1. Connectez-vous au [portail de gestion des comptes](https://account.windowsazure.com/subscriptions).
2. Sélectionnez l’abonnement que vous souhaitez modifier.
3. Sélectionnez **Modifier les détails de l’abonnement**.

    ![Capture d’écran des informations sur l’abonnement Azure, avec l’option « Modifier les détails de l’abonnement » mise en évidence](./media/billing-add-office-365-tenant-to-azure-subscription/s33_azure-edit-subscription-details.png)

4. Dans la zone **ADMINISTRATEUR DE SERVICES** , entrez l’adresse de messagerie du nouvel administrateur de service.

    ![Capture d’écran de la boîte de dialogue « Modifiez votre abonnement »](./media/billing-add-office-365-tenant-to-azure-subscription/s34_change-subscription-service-admin.png)

### <a name="associate-the-office-365-tenant-with-the-azure-subscription"></a>Associer le client Office 365 à l’abonnement Azure
Pour associer le client Office 365 à l’abonnement Azure, procédez comme suit :

1.  Connectez-vous au [portail de gestion des comptes](https://account.windowsazure.com/subscriptions) à l’aide des informations d’identification d’administrateur de service.
2.  Dans le volet gauche, sélectionnez **ACTIVE DIRECTORY**.

    ![Capture d’écran de l’entrée Active Directory](./media/billing-add-office-365-tenant-to-azure-subscription/s35-classic-portal-active-directory-entry.png)

    > [AZURE.NOTE] Vous ne devriez pas voir le client Office 365. Si vous le voyez, ignorez l’étape suivante.

    ![Capture d’écran du répertoire par défaut d’Azure Active Directory](./media/billing-add-office-365-tenant-to-azure-subscription/s36-aad-tenant-default.png)

3. Ajoutez le client Office 365 à votre abonnement Azure.

    a. Sélectionnez **NOUVEAU** > **ANNUAIRE** > **CRÉATION PERSONNALISÉE**.

    ![Capture d’écran de la création personnalisée Azure Active Directory](./media/billing-add-office-365-tenant-to-azure-subscription/s37-aad-custom-create.png)

    b. Sur la page **Ajout d’un annuaire**, sous **ANNUAIRE**, sélectionnez **Utiliser un annuaire existant**. Puis sélectionnez **Je suis prêt à me déconnecter**, puis sélectionnez **Terminer** ![icône Terminer](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).

    ![Capture d’écran de « Utiliser un annuaire existant »](./media/billing-add-office-365-tenant-to-azure-subscription/s39_add-directory-use-existing.png)

    c. Une fois que vous êtes déconnecté, connectez-vous à l’aide des informations d’identification d’administrateur global de votre client Office 365.

    ![Capture d’écran de la connexion d’administrateur général Office 365](./media/billing-add-office-365-tenant-to-azure-subscription/s310_sign-in-global-admin-office-365.png)

    d. Sélectionnez **Continuer**.

    ![Capture d’écran de vérification](./media/billing-add-office-365-tenant-to-azure-subscription/s311_use-contoso-directory-azure-verify.png)

    e. Sélectionnez **Se déconnecter maintenant**.

    ![Capture d’écran de la déconnexion](./media/billing-add-office-365-tenant-to-azure-subscription/s312_use-contoso-directory-azure-confirm-and-sign-out.png)

    f. Connectez-vous au [portail de gestion des comptes](https://account.windowsazure.com/subscriptions) à l’aide des informations d’identification d’administrateur de service.

    ![Capture d’écran de la connexion à Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)

    g. Votre client Office 365 doit s’afficher dans le tableau de bord.

    ![Capture d’écran du tableau de bord](./media/billing-add-office-365-tenant-to-azure-subscription/s314_office-365-tenant-appear-in-azure.png)

4. Commencez par modifier l’annuaire associé à votre abonnement Azure.

    a. Sélectionnez **Paramètres**.

    ![Capture d’écran de l’icône des paramètres du portail Azure Classic](./media/billing-add-office-365-tenant-to-azure-subscription/s315_azure-classic-portal-settings-icon.png)

    b. Sélectionnez votre abonnement Azure, puis sélectionnez **MODIFIER L’ANNUAIRE**.
    ![Capture d’écran Modifier l’annuaire de l’abonnement Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s316_azure-subscription-edit-directory.png)

    c. Sélectionnez **Suivant** ![icône Suivant](./media/billing-add-office-365-tenant-to-azure-subscription/s317_next-icon.png).

    ![Capture d’écran « Modifier l’annuaire associé »](./media/billing-add-office-365-tenant-to-azure-subscription/s318_azure-change-associated-directory.png)

    > [AZURE.WARNING] Vous recevrez un avertissement vous informant que tous les coadministrateurs seront supprimés.

    ![azure-confirm-directory-mapping](./media/billing-add-office-365-tenant-to-azure-subscription/s322_azure-confirm-directory-mapping.png)

    >[AZURE.WARNING] En outre, tous les utilisateurs disposant du [contrôle d’accès en fonction du rôle (RBAC)](./active-directory/role-based-access-control-configure.md) avec accès affecté dans les groupes de ressources existants seront également supprimés. Toutefois, l’avertissement mentionne uniquement la suppression des coadministrateurs.

    ![assigned-users-removed-resource-groups](./media/billing-add-office-365-tenant-to-azure-subscription/s325_assigned-users-removed-resource-groups.png)

    d. Sélectionnez **Terminer** ![icône Terminer](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).

5. Vous pouvez désormais ajouter vos comptes professionnels Office 365 en tant que coadministrateurs du client Azure Active Directory.

    a. Sélectionnez l’onglet **ADMINISTRATEURS**, puis **AJOUTER**.

    ![Capture d’écran de l’onglet Administrateurs dans les paramètres du portail Azure Classic](./media/billing-add-office-365-tenant-to-azure-subscription/s319_azure-classic-portal-settings-administrators.png)

    b. Entrez un compte professionnel de votre client Office 365, sélectionnez l’abonnement Azure, puis sélectionnez **Terminer![ **icône Terminer](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).

    ![Capture d’écran de la boîte de dialogue d’ajout de coadministrateur Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s320_azure-add-co-administrator.png)

    c. Revenez à l’onglet **ADMINISTRATEURS**. Le compte professionnel doit être affiché en tant que coadministrateur.

    ![Capture d’écran de l’onglet Administrateurs](./media/billing-add-office-365-tenant-to-azure-subscription/s321_azure-co-administrator-added.png)

6. Vous pouvez ensuite tester l’accès avec le coadministrateur.

    a. Déconnectez-vous du portail de gestion des comptes.

    b. Ouvrez le [portail de gestion des comptes](https://account.windowsazure.com/subscriptions) ou le [portail Azure](https://portal.azure.com/).

    c. Si la page de connexion Azure inclut un lien **Sign in with your organization account**(Se connecter à l’aide d’un compte professionnel), cliquez sur ce dernier. Sinon, ignorez cette étape.

    ![Capture d’écran de la page de connexion à Azure](./media/billing-add-office-365-tenant-to-azure-subscription/3-sign-in-to-azure.png)

    d. Entrez les informations d’identification du coadministrateur, puis sélectionnez **Se connecter**.

    ![Capture d’écran de la page de connexion à Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s324_azure-sign-in-with-co-admin.png)

## <a name="next-steps"></a>Étapes suivantes
Les scénarios connexes incluent :

- Vous avez déjà un abonnement à Office 365 et vous êtes prêt à prendre un abonnement à Azure, mais vous souhaitez utiliser les comptes d’utilisateur Office 365 existants pour votre abonnement Azure.
- Vous êtes un abonné Azure et vous souhaitez obtenir un abonnement à Office 365 pour les utilisateurs dans votre instance Azure Active Directory existante.

Pour découvrir comment effectuer ces tâches, voir [Utiliser votre compte Office 365 existant avec votre abonnement Azure ou vice versa](billing-use-existing-office-365-account-azure-subscription.md).



<!--HONumber=Oct16_HO2-->


