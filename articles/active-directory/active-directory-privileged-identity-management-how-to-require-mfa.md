<properties
   pageTitle="Azure Privileged Identity Management : exigence de l’application de la solution MFA"
   description="Découvrez comment exiger l’application de la solution MFA pour les identités dotées de privilèges avec l’extension Azure Privileged Identity Management."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="01/21/2016"
   ms.author="kgremban"/>

# Azure Privileged Identity Management : Comment exiger l’authentification MFA

Il est vivement recommandé d’exiger l’application de la solution Multi-Factor Authentication (MFA) pour tous vos administrateurs.

##Exigence d’application de la solution MFA dans Azure Privileged Identity Management
Lorsque vous vous connectez en tant qu’administrateur Azure Privileged Identity Management, vous recevez des alertes suggérant que vos comptes dotés de privilèges doivent exiger l’application de la solution Multi-Factor Authentication (MFA). Cliquez sur l’alerte de sécurité dans le tableau de bord Privileged Identity Management. Un nouveau panneau s’ouvre, affichant la liste des comptes administrateur qui doivent exiger l’application de la solution MFA. Vous pouvez exiger l’authentification MFA en sélectionnant plusieurs rôles, puis en cliquant sur le bouton **Corriger**. Vous pouvez également cliquer sur les points de suspension en regard d’un rôle, puis cliquer sur le bouton **Corriger**.

Vous pouvez en outre modifier l’exigence d’authentification MFA pour un rôle spécifique en cliquant dessus dans la section Rôles du tableau de bord, en activant MFA pour ce rôle en cliquant sur **Paramètres** dans le panneau du rôle, puis en sélectionnant **Activer** sous l’authentification multifacteur.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0128_2016-->