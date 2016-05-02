<properties
   pageTitle="Exécution d’une révision de la sécurité | Microsoft Azure"
   description="Découvrez comment effectuer une révision avec l'application Azure Privileged Identity Management."
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
   ms.date="04/15/2016"
   ms.author="kgremban"/>

# Comment effectuer une révision de la sécurité dans Azure AD Privileged Identity Management

Le service Azure Active Directory (AD) Privileged Identity Management simplifie la gestion des identités privilégiées par les entreprises et l’accès aux ressources dans Azure AD et dans d’autres services en ligne Microsoft tels qu’Office 365 ou Microsoft Intune.

Si vous avez été affecté à un rôle d'administrateur, l'administrateur de la sécurité de votre organisation peut vous demander de vérifier et confirmer régulièrement que vous avez toujours besoin de ce rôle pour votre travail. Vous pouvez recevoir un e-mail contenant un lien ou accéder directement au [portail Azure](https://portal.azure.com). Suivez les étapes décrites dans cet article pour effectuer un auto-examen de vos rôles attribués.

Si vous êtes un administrateur de la sécurité intéressé par les analyses de sécurité, vous obtiendrez plus de détails sous [Comment démarrer une révision de sécurité](active-directory-privileged-identity-management-how-to-start-security-review.md).

## Ajout de l’application Privileged Identity Management

Vous pouvez utiliser l'application Azure AD Privileged Identity Management (PIM) dans le [portail Azure](https://portal.azure.com/) pour effectuer votre révision. Si vous n'avez pas l'application Azure AD Privileged Identity Management sur votre portail, procédez comme suit pour commencer.

1. Connectez-vous au [portail Azure](https://portal.azure.com/) si vous ne l’avez pas encore fait.
2. Si votre organisation possède plusieurs répertoires, cliquez sur votre nom d'utilisateur dans le coin supérieur droit du portail Azure, puis sélectionnez le répertoire que vous allez utiliser.
3. Sélectionnez l’icône **Nouveau** dans le volet de navigation à gauche.
4. Sélectionnez **Sécurité + Identité** dans le menu.
5. Sélectionnez **Azure AD Privileged Identity Management**.
6. Laissez la case **Épingler au tableau de bord** cochée, puis cliquez sur le bouton **Créer**. L’application Privileged Identity Management s’ouvre.


## Approuver ou refuser l'accès

L’accès ne sera pas modifié avant que [la révision ne soit terminée](active-directory-privileged-identity-management-how-to-complete-review.md). Ce processus crée simplement une liste de vérification pour ceux qui pourraient être amenés à modifier l’accès pour le rôle. Une fois qu’au moins un utilisateur a été sélectionné, les boutons **Approuver l’accès** et **Refuser l’accès** sont activés.

1. Dans l’application PIM, cliquez sur **Examiner l’accès des administrateurs**. Une liste de révisions de sécurité s’affiche.
2. Sélectionnez les **utilisateurs** dans la liste pour lesquels vous souhaitez modifier l’accès.
3. Cliquez sur **Approuver l’accès** ou **Refuser l’accès** pour les utilisateurs que vous avez sélectionnés. Une notification s’affiche dans le menu principal du portail Azure, et les noms sélectionnés dans la liste de vérification disparaissent (pour pouvez les afficher à nouveau en modifiant l’option de filtre). Fermez le panneau **Réviser les rôles Azure AD**.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0420_2016-->