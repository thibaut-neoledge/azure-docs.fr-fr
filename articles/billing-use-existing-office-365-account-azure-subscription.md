<properties
    pageTitle="Partager un même client Azure AD entre des abonnements Office 365 et Azure | Microsoft Azure"
    description="Découvrez comment partager votre client Azure AD Office 365 et ses utilisateurs avec votre abonnement Azure, ou inversement"
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
    ms.date="08/17/2016"
    ms.author="cjiang"/>


# <a name="use-an-existing-office-365-account-with-your-azure-subscription-or-vice-versa"></a>Utiliser un compte Office 365 existant avec votre abonnement Azure ou vice versa
Scénario : vous avez déjà un abonnement à Office 365 et vous êtes prêt à prendre un abonnement à Azure, mais vous souhaitez utiliser les comptes d’utilisateur Office 365 existants pour votre abonnement Azure. Ou bien, vous êtes un abonné Azure et vous souhaitez obtenir un abonnement à Office 365 pour les utilisateurs dans votre Azure Active Directory existant. Cet article vous montre que vous pouvez y parvenir facilement.

> [AZURE.NOTE] Cet article ne s’applique pas aux clients du Contrat Entreprise (EA). Si à un moment donné dans cet article, vous avez besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.


## <a name="quick-guidance"></a>Conseils rapides

- Si vous disposez déjà d’un abonnement à Office 365 et que vous souhaitez vous inscrire à Azure, utilisez l’option **Connectez-vous à l’aide du compte de votre entreprise**. Puis, poursuivez le processus d’inscription à Azure avec votre compte Office 365. Consultez les [étapes détaillées plus loin dans cet article](#s1).

- Si vous disposez déjà d’un abonnement à Azure et que vous souhaitez obtenir un abonnement à Office 365, connectez-vous à Office 365 avec votre compte Azure. Puis, suivez la procédure d’inscription. Une fois que l’inscription terminée, l’abonnement à Office 365 est ajouté à la même instance Azure Active Directory que celle à laquelle votre abonnement à Azure appartient. Pour plus d’informations, consultez les [étapes détaillées plus loin dans cet article](#s2).

>[AZURE.NOTE] Pour obtenir un abonnement à Office 365, le compte utilisé pour l’inscription doit être un membre du rôle de répertoire Administrateur général ou Administrateur de facturation dans votre client Azure Active Directory. [Apprenez à déterminer le rôle dans Azure Active Directory](#how-to-know-your-role-in-your-azure-active-directory).

Pour comprendre ce qui se passe lorsque vous ajoutez un abonnement à un compte, consultez les [Informations générales, plus loin dans cet article](#background-information).

## <a name="detailed-steps"></a>Procédure détaillée
<a id="s1"></a>
### <a name="scenario-1:-office-365-users-who-plan-to-buy-azure"></a>Scénario 1 : Utilisateurs d’Office 365 qui envisagent de souscrire à Azure
Dans ce scénario, nous supposons que Kelley Wall est un utilisateur qui dispose d’un abonnement à Office 365 et qui envisage de s’abonner à Azure. Il existe deux autres utilisateurs actifs, Jane et Tricia. Le compte de Kelley est admin@contoso.onmicrosoft.com.

![Centre d’administration des utilisateurs Office 365](./media/billing-use-existing-office-365-account-azure-subscription/1-office365-users-admin-center.png)

Pour vous inscrire à Azure, procédez comme suit :

1. Inscrivez-vous à Azure sur [Azure.com](https://azure.microsoft.com/). Cliquez sur **Essai gratuit**. Sur la page suivante, cliquez sur **Démarrer maintenant**.

    ![Essayez Azure gratuitement.](./media/billing-use-existing-office-365-account-azure-subscription/2-azure-signup-try-free.png)

2. Cliquez sur **Connectez-vous à l’aide du compte de votre entreprise**.

    ![Connectez-vous à Azure.](./media/billing-use-existing-office-365-account-azure-subscription/3-sign-in-to-azure.png)

3. Connectez-vous avec votre compte Office 365. Dans ce cas, il s’agit du compte Office 365 de Kelley.

    ![Connectez-vous avec votre compte Office 365.](./media/billing-use-existing-office-365-account-azure-subscription/4-sign-in-with-org-account.png)

4. Renseignez les informations et terminez le processus d’inscription.

    ![Renseignez les informations et terminez l’inscription.](./media/billing-use-existing-office-365-account-azure-subscription/5-azure-sign-up-fill-information.png)

    ![Cliquez sur Commencer à gérer mon service.](./media/billing-use-existing-office-365-account-azure-subscription/6-azure-start-managing-my-service.png)

Vous êtes prêt. Dans le portail Azure, les mêmes utilisateurs doivent s’afficher. Pour ce faire, procédez comme suit :

1. Cliquez sur **Commencer à gérer mon service** dans l’écran affiché précédemment.
2. Cliquez sur **Parcourir**, puis sur **Active Directory**.

    ![Cliquez sur Parcourir, puis sur Active Directory.](./media/billing-use-existing-office-365-account-azure-subscription/7-azure-portal-browse-ad.png)

3. Cliquez sur **UTILISATEURS**.

    ![L’onglet Utilisateurs](./media/billing-use-existing-office-365-account-azure-subscription/8-azure-portal-ad-users-tab.png)

4. Tous les utilisateurs, notamment Kelley, sont répertoriés comme prévu.

    ![Liste des utilisateurs](./media/billing-use-existing-office-365-account-azure-subscription/9-azure-portal-ad-users.png)

<a id="s2"></a>
### <a name="scenario-2:-azure-users-who-plan-to-buy-office-365"></a>Scénario 2 : Utilisateurs d’Azure qui envisagent de souscrire à Office 365

Dans ce scénario, Kelley Wall est un utilisateur qui possède un abonnement à Azure sous le compte admin@contoso.onmicrosoft.com. Kelley veut s’abonner à Office 365 et utiliser le même répertoire que dans Azure.

>[AZURE.NOTE] Pour obtenir un abonnement à Office 365, le compte utilisé pour la connexion doit être un membre du rôle de répertoire Administrateur général ou Administrateur de facturation dans votre client Azure Active Directory. [Apprenez à connaître le rôle dans Azure Active Directory](#how-to-know-your-role-in-your-azure-active-directory).

![Paramètres d’abonnement au portail Azure](./media/billing-use-existing-office-365-account-azure-subscription/10-azure-portal-settings-subscription.png)

![Utilisateurs Active Directory dans le portail Azure](./media/billing-use-existing-office-365-account-azure-subscription/11-azure-portal-ads-users.png)

Pour vous abonner à Office 365, procédez comme suit :

1. Accédez à la [page produit Office 365](https://products.office.com/business), puis sélectionnez un plan qui vous convient.
2. Après avoir sélectionné un plan, la page suivante s’affiche. Ne renseignez pas le formulaire. Cliquez sur **Se connecter** dans le coin supérieur droit de la page.

    ![Page d’évaluation d’Office 365](./media/billing-use-existing-office-365-account-azure-subscription/12-office-365-trial-page.png)

3. Connectez-vous avec les informations d’identification de votre compte. Dans cet exemple, il s’agit du compte de Kelley.

    ![Connexion à Office 365](./media/billing-use-existing-office-365-account-azure-subscription/13-office-365-sign-in.png)

4. Cliquez sur **Essayez dès maintenant**.

    ![Confirmez votre commande d’Office 365.](./media/billing-use-existing-office-365-account-azure-subscription/14-office-365-confirm-your-order.png)

5. Sur la page du reçu de la commande, cliquez sur **Continuer**.

    ![Reçu de commande d’Office 365](./media/billing-use-existing-office-365-account-azure-subscription/15-office-365-order-receipt.png)

Vous êtes prêt. Dans le centre d’administration Office 365, les utilisateurs du répertoire Contoso doivent s’afficher en tant qu’utilisateurs actifs. Pour ce faire, procédez comme suit :

1. Ouvrez le centre d’administration Office 365.
2. Développez la section **UTILISATEURS**, puis cliquez sur **Utilisateurs actifs**.

    ![Utilisateurs du centre d’administration Office 365](./media/billing-use-existing-office-365-account-azure-subscription/16-office-365-admin-center-users.png)

### <a name="how-to-know-your-role-in-your-azure-active-directory"></a>Comment connaître votre rôle dans votre Azure Active Directory ?

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Cliquez sur **Parcourir**, puis sur **Active Directory**.

    ![Active Directory dans le portail Azure](./media/billing-use-existing-office-365-account-azure-subscription/7-azure-portal-browse-ad.png)

3. Cliquez sur **UTILISATEURS**.

    ![Utilisateurs Active Directory par défaut dans le portail Azure](./media/billing-use-existing-office-365-account-azure-subscription/17-azure-portal-default-ad-users.png)

4. Cliquez sur l’utilisateur. Dans cet exemple, l’utilisateur est Kelley Wall.

    Notez le champ **RÔLE ORGANISATIONNEL**.

    ![Identité de l’utilisateur dans le portail Azure](./media/billing-use-existing-office-365-account-azure-subscription/18-azure-portal-user-identity.png)

## <a name="background-information-about-azure-and-office-365-subscriptions"></a>Informations générales sur les abonnements Azure et Office 365
Office 365 et Azure utilisent le service Azure Active Directory pour gérer les utilisateurs et les abonnements. Considérez un répertoire Azure comme un conteneur qui vous sert à regrouper les abonnements et les utilisateurs. Pour utiliser le même compte d’utilisateur pour vos abonnements à Azure et à Office 365, vous devez vous assurer que les abonnements sont créés dans le même répertoire. Gardez à l’esprit les points suivants :

- Un abonnement est créé dans un répertoire, pas l’inverse.
- Les utilisateurs appartiennent à des répertoires, pas l’inverse.
- Un abonnement est créé dans le répertoire de l’utilisateur qui crée l’abonnement. Par conséquent, votre abonnement à Office 365 est lié au même compte que votre abonnement à Azure lorsque vous utilisez ce compte pour créer l’abonnement à Office 365.

![Informations contextuelles](./media/billing-use-existing-office-365-account-azure-subscription/19-background-information.png)

Pour plus d’informations, consultez la page [Association des abonnements Azure avec Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md).

>[AZURE.NOTE] Les abonnements Azure sont détenus par des utilisateurs individuels dans le répertoire.

>[AZURE.NOTE] Les abonnements Office 365 sont détenus par le répertoire proprement dit. Les utilisateurs dans le répertoire peuvent gérer ces abonnements s’ils ont les autorisations requises.

## <a name="next-steps"></a>Étapes suivantes
Si vous avez obtenu les deux abonnements Azure et Office 365 séparément et si vous souhaitez accéder au client Office 365 à partir de l’abonnement Azure, consultez [Associer un client Office 365 à un abonnement Azure](billing-add-office-365-tenant-to-azure-subscription.md).

> [AZURE.NOTE] Si vous avez d’autres questions, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.



<!--HONumber=Oct16_HO2-->


