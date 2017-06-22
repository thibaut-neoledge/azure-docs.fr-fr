---
title: Utiliser un client Office 365 avec un abonnement Azure | Microsoft Docs
description: "Découvrez comment ajouter un annuaire (client) Office 365 à un abonnement Azure."
services: 
documentationcenter: 
author: JiangChen79
manager: vikdesai
editor: 
tags: billing,top-support-issue
ms.assetid: cc9c57c6-7bfd-4dea-9027-c75ef3737589
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: cjiang
ms.translationtype: Human Translation
ms.sourcegitcommit: 1cb57e5156dab976599ddfa9a58f26ca8ef1ee0e
ms.openlocfilehash: 42db903244e5642dbf273e71994d402f8aeda1e9
ms.contentlocale: fr-fr
ms.lasthandoff: 02/22/2017


---
# <a name="associate-an-office-365-tenant-to-an-azure-subscription"></a>Association d’un client Office 365 à un abonnement Azure
Liez vos abonnements Azure et Office 365 distincts afin que le client Office 365 soit accessible depuis votre abonnement Azure. Pour lier vos abonnements, connectez-vous à Azure avec le compte d’administrateur de service Azure, ajoutez un annuaire et ajoutez les comptes professionnels Office 365 au client Azure Active Directory.

Si vous souhaitez un abonnement Office 365 pour les utilisateurs de votre instance d’Azure Active Directory ou que vous possédez un compte Office 365, mais pas un compte Azure, consultez [Inscription à Azure avec un compte Office 365](billing-use-existing-office-365-account-azure-subscription.md). 

## <a name="before-you-begin"></a>Avant de commencer
* Vous devez disposer des informations d’identification de l’administrateur de service de l’abonnement Azure. Les comptes de coadministrateurs ne peuvent pas effectuer certaines des étapes de cet article. Pour changer votre administrateur de service, consultez [Ajout ou modification de rôles d’administrateur Azure](billing-add-change-azure-subscription-administrator.md#change-service-administrator-for-a-subscription).
* Vous devez disposer des informations d’identification d’un administrateur général du client Office 365.
* L’adresse électronique de l’administrateur de service ne doit pas se trouver dans le client Office 365.
* L’adresse électronique de l’administrateur de service ne doit pas correspondre à celle de l’administrateur général du client Office 365.
* Si vous utilisez actuellement une adresse électronique qui est à la fois un compte Microsoft et un compte professionnel, modifiez temporairement l’administrateur de service de votre abonnement Azure afin d’utiliser un autre compte Microsoft. Vous pouvez créer un compte Microsoft à la [page d’inscription de compte Microsoft](https://signup.live.com/).

## <a name="link-office-365-tenant-to-azure-subscription"></a>Liaison d’un client Office 365 à un abonnement Azure
Pour associer le client Office 365 à l’abonnement Azure, procédez comme suit :

### <a name="step-1-add-office-365-tenant-to-your-azure-subscription"></a>Étape 1 : ajouter le client Office 365 à votre abonnement Azure

1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com/) à l’aide des informations d’identification d’administrateur de service.

    ![Capture d’écran de la connexion à Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)

2. Dans le volet gauche, sélectionnez **ACTIVE DIRECTORY**. Vous ne devriez pas voir le client Office 365. Si vous le voyez, passez à [Étape 2 : Commencez par modifier l’annuaire associé à votre abonnement Azure](#Step2).
   
   ![Capture d’écran de l’entrée Active Directory](./media/billing-add-office-365-tenant-to-azure-subscription/s35-classic-portal-active-directory-entry.png)

3. Sélectionnez **NOUVEAU** > **ANNUAIRE** > **CRÉATION PERSONNALISÉE**.
   
    ![Capture d’écran de la création personnalisée Azure Active Directory](./media/billing-add-office-365-tenant-to-azure-subscription/s37-aad-custom-create.png)
   
4. Sur la page **Ajout d’un annuaire**, sous **ANNUAIRE**, sélectionnez **Utiliser un annuaire existant**. Puis sélectionnez **Je suis prêt à me déconnecter**, puis sélectionnez **Terminer** ![icône Terminer](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).
   
    ![Capture d’écran de « Utiliser un annuaire existant »](./media/billing-add-office-365-tenant-to-azure-subscription/s39_add-directory-use-existing.png)
   
5. Une fois que vous êtes déconnecté, connectez-vous à l’aide des informations d’identification d’administrateur global de votre client Office 365.
   
    ![Capture d’écran de la connexion d’administrateur général Office 365](./media/billing-add-office-365-tenant-to-azure-subscription/s310_sign-in-global-admin-office-365.png)
   
6. Sélectionnez **Continuer**.
   
    ![Capture d’écran de vérification](./media/billing-add-office-365-tenant-to-azure-subscription/s311_use-contoso-directory-azure-verify.png)
   
7. Sélectionnez **Se déconnecter maintenant**.
   
    ![Capture d’écran de la déconnexion](./media/billing-add-office-365-tenant-to-azure-subscription/s312_use-contoso-directory-azure-confirm-and-sign-out.png)
   
8. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com/) à l’aide des informations d’identification d’administrateur de service.
   
    ![Capture d’écran de la connexion à Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)
   
9. Votre client Office 365 doit s’afficher dans le tableau de bord.
   
    ![Capture d’écran du tableau de bord](./media/billing-add-office-365-tenant-to-azure-subscription/s314_office-365-tenant-appear-in-azure.png)

### <a name="Step2"></a>Étape 2 : modifier l’annuaire associé à votre abonnement Azure
   
1. Sélectionnez **Paramètres**.
   
    ![Capture d’écran de l’icône des paramètres du portail Azure Classic](./media/billing-add-office-365-tenant-to-azure-subscription/s315_azure-classic-portal-settings-icon.png)
   
2. Sélectionnez votre abonnement Azure, puis sélectionnez **MODIFIER L’ANNUAIRE**.

    ![Capture d’écran Modifier l’annuaire de l’abonnement Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s316_azure-subscription-edit-directory.png)
   
3. Sélectionnez **Suivant** ![Icône Suivant](./media/billing-add-office-365-tenant-to-azure-subscription/s317_next-icon.png).
   
    ![Capture d’écran « Modifier l’annuaire associé »](./media/billing-add-office-365-tenant-to-azure-subscription/s318_azure-change-associated-directory.png)
   
4. Passez en revue les comptes affectés. Tous les coadministrateurs et utilisateurs du [contrôle d’accès en fonction du rôle (RBAC)](../active-directory/role-based-access-control-configure.md) avec accès affecté dans les groupes de ressources existants seront également supprimés. L’avertissement mentionne uniquement la suppression des coadministrateurs.
      
    ![Capture d’écran montrant les comptes de coadministrateurs à supprimer.](./media/billing-add-office-365-tenant-to-azure-subscription/s322_azure-confirm-directory-mapping.png)
   
    ![Capture d’écran montrant un exemple de compte d’utilisateur à supprimer.](./media/billing-add-office-365-tenant-to-azure-subscription/s325_assigned-users-removed-resource-groups.png)
   
5. Sélectionnez **Terminer** ![icône Terminer](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).

### <a name="step-3-add-your-office-365-organizational-accounts-as-co-administrators-to-the-azure-active-directory-tenant"></a>Étape 3 : Ajoutez vos comptes professionnels Office 365 en tant que coadministrateurs du client Azure Active Directory
   
1. Sélectionnez l’onglet **ADMINISTRATEURS**, puis **AJOUTER**.
   
    ![Capture d’écran de l’onglet Administrateurs dans les paramètres du portail Azure Classic](./media/billing-add-office-365-tenant-to-azure-subscription/s319_azure-classic-portal-settings-administrators.png)
   
2. Entrez un compte professionnel de votre client Office 365, sélectionnez l’abonnement Azure, puis sélectionnez **Terminer** ![icône Terminer](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).
   
    ![Capture d’écran de la boîte de dialogue d’ajout de coadministrateur Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s320_azure-add-co-administrator.png)
   
3. Revenez à l’onglet **ADMINISTRATEURS**. Le compte professionnel doit être affiché en tant que coadministrateur.
   
    ![Capture d’écran de l’onglet Administrateurs](./media/billing-add-office-365-tenant-to-azure-subscription/s321_azure-co-administrator-added.png)
4.  Testez l’accès avec le compte du coadministrateur.
   
    a. Déconnectez-vous du portail Azure Classic.
   
    b. Ouvrez le [portail Azure](https://portal.azure.com/).
   
    c. Entrez les informations d’identification du coadministrateur, puis sélectionnez **Se connecter**.
   
    ![Capture d’écran de la page de connexion à Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s324_azure-sign-in-with-co-admin.png)

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contactez le support technique.
Si vous avez besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.



