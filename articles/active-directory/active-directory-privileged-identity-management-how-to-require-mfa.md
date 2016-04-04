<properties
   pageTitle="Exiger Multi-Factor Authentication (authentification multifacteur) | Microsoft Azure"
   description="Découvrez comment exiger l’application de la solution MFA pour les identités dotées de privilèges avec l’extension Azure Active Directory Privileged Identity Management."
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
   ms.date="03/17/2016"
   ms.author="kgremban"/>

# Azure AD Privileged Identity Management : Comment exiger l’authentification MFA

Il est vivement recommandé d’exiger l’application de la solution Multi-Factor Authentication (MFA) pour tous vos administrateurs.

## Exigence d’application de la solution MFA dans Azure AD Privileged Identity Management

Lorsque vous vous connectez en tant qu’administrateur Azure Privileged Identity Management, vous recevez des alertes suggérant que vos comptes dotés de privilèges doivent exiger l’application de la solution Multi-Factor Authentication (MFA). Cliquez sur l’alerte de sécurité dans le tableau de bord Privileged Identity Management. Un nouveau panneau s’ouvre, affichant la liste des comptes administrateur qui doivent exiger l’application de la solution MFA. Vous pouvez exiger l’authentification MFA en sélectionnant plusieurs rôles, puis en cliquant sur le bouton **Corriger**. Vous pouvez également cliquer sur les points de suspension en regard d’un rôle, puis cliquer sur le bouton **Corriger**.

Vous pouvez en outre modifier l’exigence d’authentification MFA pour un rôle spécifique en cliquant dessus dans la section Rôles du tableau de bord, en activant MFA pour ce rôle en cliquant sur **Paramètres** dans le panneau du rôle, puis en sélectionnant **Activer** sous l’authentification multifacteur.

## Validation de Multi-Factor Authentication (MFA) par Azure AD PIM

> [AZURE.IMPORTANT] Étant donné que les comptes Microsoft (par exemple, @outlook.com, @live.com ou @hotmail.com) ne sont actuellement pas pris en charge pour l’inscription à Azure MFA, ils ne seront pas autorisés en tant qu’administrateurs temporaires pour les rôles à privilèges élevés. Si les utilisateurs doivent continuer à gérer les charges de travail à l’aide d’un compte Microsoft, convertissez-les en administrateurs permanents pour l’instant.

Il existe deux options pour valider l'authentification multifacteur lorsqu’un utilisateur active un rôle.

La façon la plus simple consiste à s’appuyer sur Azure MFA pour les utilisateurs qui activent un rôle privilégié. Pour ce faire, vérifiez tout d’abord que ces utilisateurs bénéficient d’une licence si nécessaire et qu’il se sont enregistrés pour l’authentification multifacteur Azure. Pour plus d’informations, consultez [Prise en main avec Azure Multi-Factor Authentication dans le cloud](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md#assigning-an-azure-mfa-azure-ad-premium-or-enterprise-mobility-license-to-users). Veuillez noter qu’il est recommandé, mais pas obligatoire, de configurer Azure AD pour appliquer l’authentification multifacteur pour ces utilisateurs lorsqu’ils se connectent. En effet, les vérifications de l’authentification multifacteur sont effectuées par Azure AD PIM directement.

Si les utilisateurs s’authentifient en local, vous pouvez également faire en sorte que votre fournisseur d’identité soit responsable de l’authentification multifacteur. Par exemple, si vous avez configuré AD Federation Services pour exiger l’authentification par carte à puce avant d’accéder à Azure AD, la section [Sécurisation des ressources de cloud avec le serveur Azure Multi-Factor Authentication et AD FS](../multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud.md) inclut des instructions pour configurer AD FS afin d’envoyer les revendications à Azure AD. Lorsqu’un utilisateur tente d’activer un rôle, Azure AD PIM accepte cette authentification multifacteur qui a déjà été validée pour l’utilisateur une fois qu’il reçoit les revendications appropriées.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0323_2016-->