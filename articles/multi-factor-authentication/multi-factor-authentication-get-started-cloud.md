---
title: "Prise en main d’Azure MFA dans le cloud | Microsoft Docs"
description: "Cette page dédiée à Microsoft Azure Multi-Factor Authentication décrit la prise en main d’Azure MFA dans le cloud."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 6b2e6549-1a26-4666-9c4a-cbe5d64c4e66
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/04/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: c6fe00b72d95a3eb40d91f6f7989b7163518c46f


---
# <a name="getting-started-with-azure-multi-factor-authentication-in-the-cloud"></a>Prise en main avec Azure Multi-Factor Authentication dans le cloud
Cet article vous explique comment utiliser Azure Multi-Factor Authentication dans le cloud.

> [!NOTE]
> La documentation suivante fournit des informations relatives à l’activation des utilisateurs à l’aide du **portail Azure Classic**. Si vous recherchez des informations sur la configuration d’Azure Multi-Factor Authentication pour les utilisateurs O365, consultez l’article [Configurer l’authentification multifacteur pour les utilisateurs d’Office 365.](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6?ui=en-US&rs=en-US&ad=US)

![MFA dans le cloud](./media/multi-factor-authentication-get-started-cloud/mfa_in_cloud.png)

## <a name="prerequisites"></a>Composants requis
Les conditions préalables suivantes sont requises pour pouvoir activer Azure Multi-Factor Authentication pour vos utilisateurs.

1. [Souscrivez un abonnement Azure](https://azure.microsoft.com/pricing/free-trial/) : si vous n’avez pas encore d’abonnement Azure, vous devez en souscrire un. Si vous êtes nouveau et que vous utilisez Azure MFA, vous pouvez utiliser un abonnement d’évaluation.
2. [Créez un fournisseur Multi-Factor Auth](multi-factor-authentication-get-started-auth-provider.md) et attribuez-le à votre répertoire ou [attribuez des licences à vos utilisateurs](multi-factor-authentication-get-started-assign-licenses.md)

> [!NOTE]
> Les licences sont disponibles pour les utilisateurs qui disposent d’Azure MFA, d’Azure AD Premium ou d’Enterprise Mobility Suite (EMS).  MFA est inclus dans Azure AD Premium et dans l’EMS. Si vous avez suffisamment de licences, il est inutile de créer un fournisseur d’authentification.

## <a name="turn-on-two-step-verification-for-users"></a>Activer la vérification en deux étapes pour les utilisateurs
Pour demander la vérification en deux étapes pour un utilisateur, modifiez l’état de désactivé à activé.  Pour plus d’informations sur les états des utilisateurs, consultez [User States in Azure Multi-Factor Authentication (États des utilisateurs dans Azure Multi-Factor Authentication)](multi-factor-authentication-get-started-user-states.md)

Utilisez la procédure suivante pour activer l’authentification multifacteur pour vos utilisateurs.

### <a name="to-turn-on-multi-factor-authentication"></a>Pour activer l’authentification multifacteur
1. Connectez-vous en tant qu’administrateur au [portail Azure Classic](https://manage.windowsazure.com).
2. Cliquez à gauche sur **Active Directory**.
3. Sous Répertoire, sélectionnez le répertoire de l’utilisateur que vous souhaitez activer.
   ![Cliquez sur Répertoire](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4. En haut de la page, cliquez sur **Utilisateurs**.
5. En bas de la page, cliquez sur **Gérer Multi-Factor Auth**. Un nouvel onglet de navigateur s'ouvre.
   ![Cliquez sur Répertoire](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6. Recherchez l’utilisateur pour lequel vous souhaitez activer la vérification en deux étapes. Vous devrez peut-être modifier l'affichage en haut de la page. Assurez-vous que l’état est **désactivé.**
   ![Activer un utilisateur](./media/multi-factor-authentication-get-started-cloud/enable1.png)
7. **Cochez** la case en regard du nom des utilisateurs.
8. Sur la droite, cliquez sur **Activer**.
   ![Activer un utilisateur](./media/multi-factor-authentication-get-started-cloud/user1.png)
9. Cliquez sur **Activer l’authentification multifacteur**.
   ![Activer un utilisateur](./media/multi-factor-authentication-get-started-cloud/enable2.png)
10. Vous remarquerez que l’état est passé de **désactivé** à **activé**.
    ![Activer des utilisateurs](./media/multi-factor-authentication-get-started-cloud/user.png)

Après avoir activé les utilisateurs, vous devez les en avertir par e-mail. La prochaine fois qu’ils tentent de se connecter, il leur sera demandé d’inscrire leur compte pour la vérification en deux étapes. Une fois qu’ils commencent à utiliser la vérification en deux étapes, ils devront également configurer des mots de passe d’application afin d’éviter d’être déconnectés des applications sans navigateur.

## <a name="use-powershell-to-automate-turning-on-two-step-verification"></a>Utiliser PowerShell pour automatiser l’activation de la vérification en deux étapes
Pour modifier [l’état](multi-factor-authentication-whats-next.md) à l’aide [d’Azure AD PowerShell](/powershell/azureps-cmdlets-docs), vous pouvez utiliser les éléments suivants.  Vous pouvez modifier `$st.State` pour définir l’un des états suivants :

* activé
* Appliquée
* Désactivé  

> [!IMPORTANT]
> Nous déconseillons de modifier directement l’état des utilisateurs de Désactivé à Appliqué. Les applications sans navigateur cesseront de fonctionner, car l’utilisateur n’a pas effectué l’enregistrement MFA et obtenu un [mot de passe d’application](multi-factor-authentication-whats-next.md#app-passwords). Si vous utilisez des applications sans navigateur et que vous avez besoin de mots de passe d’application, il est recommandé de passer de l’état Désactivé à l’état Activé. Cela permet aux utilisateurs de s’inscrire et d’obtenir leurs mots de passe d’application. Ensuite, vous pouvez les faire passer à l’état Appliqué.

L’utilisation de PowerShell permet une activation en bloc des utilisateurs. Il n’existe actuellement aucune fonctionnalité d’activation en bloc dans le portail Azure ; vous devez donc sélectionner chaque utilisateur un à un. Cela peut se révéler fastidieux si vous avez un grand nombre d’utilisateurs. En créant un script PowerShell à l’aide de la commande ci-dessous, vous pouvez parcourir une liste d’utilisateurs et les activer.

        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

Voici un exemple :

    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }


Pour plus d’informations, consultez [User States in Azure Multi-Factor Authentication (États des utilisateurs dans Azure Multi-Factor Authentication)](multi-factor-authentication-get-started-user-states.md)

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez configuré Azure Multi-Factor Authentication dans le cloud, vous pouvez configurer et installer votre déploiement. Pour plus d’informations, consultez [Configuration d’Azure Multi-Factor Authentication](multi-factor-authentication-whats-next.md).




<!--HONumber=Dec16_HO2-->


