<properties
pageTitle="Personnaliser votre page de connexion dans la version préliminaire d’Azure Active Directory | Microsoft Azure"
description="Découvrir comment ajouter une société à la page de connexion Azure"
services="active-directory"
documentationCenter=""
authors="curtand"
manager="femila"
editor=""/>

<tags
ms.service="active-directory"
ms.workload="identity"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="09/12/2016"
ms.author="curtand"/>

# Ajouter la personnalisation de votre société à votre page de connexion dans la version préliminaire d’Azure Active Directory

Pour éviter toute confusion, de nombreuses entreprises veulent que tous les sites Web et services qu’elles gèrent aient un aspect similaire. La version préliminaire d’Azure Active Directory offre cette possibilité en vous permettant de personnaliser l’apparence la page de connexion pour qu’elle affiche le logo de votre société et sa palette de couleurs personnalisée. [Nouveautés de la version préliminaire](active-directory-preview-explainer.md) La page de connexion s’affiche lorsque vous vous connectez à Office 365 ou à d’autres applications Web qui utilisent Azure AD comme fournisseur d’identité. Vous interagissez avec cette page pour saisir vos informations d’identification.

Si vous souhaitez afficher la marque, la palette de couleurs de votre société ou tout autre élément personnalisable sur cette page, reportez-vous aux images qui suivent pour comprendre la différence entre les deux.

La capture d’écran suivante montre un exemple de la page de connexion Office 365 sur un ordinateur de bureau **avant** une personnalisation :

![Page de connexion Office 365 avant la personnalisation](./media/active-directory-branding-custom-signon-azure-portal/sign-in-page-before-customization.png)

La capture d’écran suivante affiche l’exemple de la page de connexion Office 365 sur un ordinateur de bureau **après** une personnalisation :

![Page de connexion d’Office 365 après la personnalisation](./media/active-directory-branding-custom-signon-azure-portal/sign-in-page-after-customization.png)


## Personnalisation de la page de connexion

En règle générale, vous utilisez la page de connexion pour accéder à l’aide d’un navigateur à vos applications et services cloud auxquels votre organisation est abonnée.

Lorsque vous apportez des modifications à votre page de connexion, un délai pouvant atteindre une heure peut être nécessaire pour qu’elles apparaissent.

Une page de connexion personnalisée s’affiche uniquement lorsque vous accédez à un service avec une URL spécifique au client, telle que https://outlook.com/**contoso**.com ou https://mail.**contoso**.com.

Lorsque vous accédez à un service avec une URL indépendante du client (par exemple : https://mail.office365.com), une page de connexion non personnalisée s’affiche. Dans ce cas, votre marque apparaît une fois que vous avez entré votre ID d’utilisateur ou que vous avez sélectionné une vignette utilisateur.

> [AZURE.NOTE]
>
- Votre nom de domaine doit apparaître comme étant « Actif » dans la partie **Domaines** du portail Azure dans lequel vous avez effectué la personnalisation de société. Pour plus d’informations, consultez [Ajouter des noms de domaine personnalisés](active-directory-domains-add-azure-portal.md).
- La personnalisation de la page de connexion ne s’étend pas à la page de connexion client de Microsoft. Si vous vous connectez avec un compte Microsoft, vous pouvez voir une liste personnalisée de vignettes utilisateur générées par Azure AD, mais la marque de votre organisation ne s’applique pas à la page de connexion du compte Microsoft.

**Pour ajouter votre marque à l’annuaire :**

1.  Connectez-vous au [portail Azure](https://portal.azure.com) en utilisant un compte d’administrateur général pour le répertoire.

2.  Sélectionnez **Plus de services**, saisissez **Utilisateurs et groupes** dans la zone de texte, puis sélectionnez **Entrée**.

    ![Ouvrir la gestion des utilisateurs](./media/active-directory-branding-custom-signon-azure-portal/user-management.png)

3. Sur le panneau **Utilisateurs et groupes** panneau, sélectionnez **Personnalisation de société**.

4. Sur le panneau **Utilisateurs et groupes - Personnalisation de la société**, sélectionnez la commande **Modifier**.

    ![Modifier la personnalisation de société](./media/active-directory-branding-custom-signon-azure-portal/edit-branding.png)

5. Modifiez les éléments que vous voulez personnaliser. Tous ces éléments sont facultatifs.

6. Cliquez sur **Save**.

Il peut s’écouler jusqu’à une heure avant que les modifications que vous avez apportées à la page de connexion soient visibles.

## Étapes suivantes

[Ajouter la marque de l’entreprise spécifique à une langue](active-directory-branding-localize-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->