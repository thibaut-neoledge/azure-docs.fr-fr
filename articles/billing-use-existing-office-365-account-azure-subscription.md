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

# Utiliser votre compte Office 365 existant avec votre abonnement Azure ou vice versa
Scénario : si vous avez déjà un abonnement à Office 365 et que vous êtes prêt à prendre un abonnement à Azure, mais que vous souhaitez utiliser le(s) compte(s) d’utilisateur Office 365 existants pour votre abonnement Azure. Ou bien, vous êtes un abonné Azure et vous souhaitez obtenir un abonnement à Office 365 pour les utilisateurs dans votre Azure Active Directory existant. Cet article vous montre que vous pouvez y parvenir facilement.

> [AZURE.NOTE] Cet article ne s’applique pas aux clients du Contrat Entreprise (EA).

> [AZURE.NOTE] Si à un moment donné dans cet article, vous avez besoin d’aide, veuillez [contacter le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.

## Conseils rapides

- Si vous disposez déjà d’un abonnement à Office 365 et que vous souhaitez vous inscrire à Azure, utilisez l’option **Connectez-vous à l'aide du compte de votre entreprise** et poursuivez le processus d’inscription à Azure avec votre compte Office 365. Consultez la [procédure détaillée](#s1).
- Si vous disposez déjà d’un abonnement à Azure et que vous souhaitez obtenir un abonnement à Office 365, connectez-vous à Office 365 avec votre compte Azure et suivez la procédure d’inscription. Une fois que vous avez terminé, l’abonnement à Office 365 est ajouté au même Azure Active Directory que celui auquel votre abonnement à Azure appartient. Consultez la [procédure détaillée](#s2).

>[AZURE.NOTE] Pour obtenir un abonnement à Office 365, le compte utilisé pour la connexion doit être un membre du rôle de répertoire Administrateur général ou Administrateur de facturation dans votre client Azure AD. [Apprenez à connaître le rôle de votre Azure Active Directory](#how-to-know-your-role-in-your-azure-active-directory)

Pour comprendre comment les choses fonctionnent lorsque vous ajoutez un abonnement à un compte, consultez les [Informations générales](#background-information), plus loin dans cet article.

## Procédure détaillée
<a id="s1"></a>
### Scénario 1 : Utilisateurs d’Office 365 qui envisagent de souscrire à Azure
Dans ce scénario, nous supposons que Kelley Wall est un utilisateur qui dispose d’un abonnement à Office 365 et qui envisage de s’abonner à Azure. Il existe deux autres utilisateurs actifs, Jane et Tricia. Le compte de Kelley est admin@contoso.onmicrosoft.com.

![office365-users-admin-center.png](./media/billing-use-existing-office-365-account-azure-subscription/1-office365-users-admin-center.png)

Pour vous inscrire à Azure, procédez comme suit :

1. Inscrivez-vous à Azure sur [Azure.com](https://azure.microsoft.com/). Cliquez sur **Essai gratuit**. Sur la page suivante, cliquez sur **Démarrer maintenant**.

	![azure-signup-try-free](./media/billing-use-existing-office-365-account-azure-subscription/2-azure-signup-try-free.png)

2. Cliquez sur **Connectez-vous à l’aide du compte de votre entreprise**.

	![sign-in-to-azure](./media/billing-use-existing-office-365-account-azure-subscription/3-sign-in-to-azure.png)

3. Connectez-vous avec votre compte Office 365. Dans ce cas, il s’agit du compte Office 365 de Kelley.

	![sign-in-with-org-account](./media/billing-use-existing-office-365-account-azure-subscription/4-sign-in-with-org-account.png)

4. Renseignez les informations et terminez le processus d’inscription.

	![azure-sign-up-fill-information](./media/billing-use-existing-office-365-account-azure-subscription/5-azure-sign-up-fill-information.png)

5. Cliquez sur **Commencer à gérer mon service** et vous êtes prêt à vous lancer.

	![azure-start-managing-my-service](./media/billing-use-existing-office-365-account-azure-subscription/6-azure-start-managing-my-service.png)

Vous avez terminé. Dans le portail Azure, les mêmes utilisateurs s’affichent dans le même répertoire. Pour ce faire, procédez comme suit :

1. Cliquez sur **Commencer à gérer mon service** dans la capture d’écran ci-dessus.
2. Cliquez sur **Parcourir**, puis sur **Active Directory**.

	![azure-portal-browse-ad](./media/billing-use-existing-office-365-account-azure-subscription/7-azure-portal-browse-ad.png)

3. Cliquez sur **UTILISATEURS**.

	![azure-portal-ad-users-tab](./media/billing-use-existing-office-365-account-azure-subscription/8-azure-portal-ad-users-tab.png)

4. Tous les utilisateurs, notamment Kelley, sont répertoriés comme prévu.

	![azure-portal-ad-users](./media/billing-use-existing-office-365-account-azure-subscription/9-azure-portal-ad-users.png)

<a id="s2"></a>
### Scénario 2 : Utilisateurs d’Azure qui envisagent de souscrire à Office 365

Dans ce scénario, Kelley Wall est un utilisateur qui possède un abonnement à Azure sous le compte admin@contoso.onmicrosoft.com. Kelley veut s’abonner à Office 365 et utiliser le même répertoire que dans Azure.

>[AZURE.NOTE] Pour obtenir un abonnement à Office 365, le compte utilisé pour la connexion doit être un membre du rôle de répertoire Administrateur général ou Administrateur de facturation dans votre client Azure AD. [Apprenez à connaître le rôle de votre Azure Active Directory](#how-to-know-your-role-in-your-azure-active-directory)

![azure-portal-settings-subscription](./media/billing-use-existing-office-365-account-azure-subscription/10-azure-portal-settings-subscription.png)

![azure-portal-ads-users](./media/billing-use-existing-office-365-account-azure-subscription/11-azure-portal-ads-users.png)

Pour vous abonner à Office 365, procédez comme suit :

1. Accédez à la [page produit Office 365](https://products.office.com/business), puis sélectionnez un plan qui vous convient.
2. Après avoir sélectionné un plan, la page suivante s’affiche. Ne renseignez pas le formulaire. Cliquez sur **Se connecter** dans le coin supérieur droit de la page.

	![office-365-trial-page](./media/billing-use-existing-office-365-account-azure-subscription/12-office-365-trial-page.png)

3. Connectez-vous avec les informations d’identification de votre compte. Dans ce cas, il s’agit du compte de Kelley.

	![office-365-sign-in](./media/billing-use-existing-office-365-account-azure-subscription/13-office-365-sign-in.png)

4. Cliquez sur **Essayez dès maintenant**.

	![office-365-confirm-your-order](./media/billing-use-existing-office-365-account-azure-subscription/14-office-365-confirm-your-order.png)

5. Sur la page du reçu de la commande, cliquez sur **Continuer**.

	![office-365-order-receipt](./media/billing-use-existing-office-365-account-azure-subscription/15-office-365-order-receipt.png)

Vous avez terminé. Dans le centre d’administration Office 365, les mêmes utilisateurs du répertoire Contoso s’affichent en tant qu’utilisateurs actifs. Pour ce faire, procédez comme suit :

1. Ouvrez le centre d’administration Office 365.
2. Développez la section **UTILISATEURS**, puis cliquez sur **Utilisateurs actifs**.

	![office-365-admin-center-users](./media/billing-use-existing-office-365-account-azure-subscription/16-office-365-admin-center-users.png)

### Comment connaître votre rôle dans votre Azure Active Directory ?

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Cliquez sur **Parcourir**, puis sur **Active Directory**.

	![azure-portal-browse-ad](./media/billing-use-existing-office-365-account-azure-subscription/7-azure-portal-browse-ad.png)

3. Cliquez sur **UTILISATEURS**.

	![azure-portal-default-ad-users](./media/billing-use-existing-office-365-account-azure-subscription/17-azure-portal-default-ad-users.png)

4. Cliquez sur l’utilisateur. Dans cet exemple, il s’agit de Kelley Wall.
5. Notez le champ **RÔLE ORGANISATIONNEL**.

	![azure-portal-user-identity](./media/billing-use-existing-office-365-account-azure-subscription/18-azure-portal-user-identity.png)

## Informations contextuelles
Office 365 et Azure utilisent le service Azure Active Directory (AAD) pour gérer les utilisateurs et les abonnements. Considérez un répertoire Azure comme un conteneur qui vous sert à regrouper les abonnements et les utilisateurs. Pour utiliser le même compte d’utilisateur pour vos abonnements à Microsoft Azure et à Office 365, vous devez vous assurer que les abonnements sont créés dans le même répertoire.

- Un abonnement est créé dans un répertoire, pas l’inverse.
- Les utilisateurs appartiennent à des répertoires, pas l’inverse.
- Un abonnement est créé dans le répertoire de l’utilisateur qui crée l’abonnement. Par conséquent, votre abonnement à Office 365 est lié au même compte de votre abonnement Azure lorsque vous utilisez le compte pour créer l’abonnement à Office 365.

![background-information](./media/billing-use-existing-office-365-account-azure-subscription/19-background-information.png)

Pour plus d’informations, consultez [Comment l’abonnement à Azure est associé à Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md).

**Remarques :**

- Les abonnements Azure sont détenus par des utilisateurs individuels dans le répertoire.
- Les abonnements Office 365 sont détenus par le répertoire proprement dit. Les utilisateurs dans le répertoire peuvent gérer ces abonnements s’ils ont les autorisations requises.

##Étapes suivantes
Dans un scénario connexe, vous aviez obtenu les deux abonnements Azure et Office 365 séparément et souhaitez désormais accéder au client Office 365 à partir de l’abonnement Azure. Pour ce faire, consultez [Associer un client Office 365 à un abonnement Azure](billing-add-office-365-tenant-to-azure-subscription.md).

> [AZURE.NOTE] Si vous avez d’autres questions, veuillez [contacter le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.

<!---HONumber=AcomDC_0928_2016-->