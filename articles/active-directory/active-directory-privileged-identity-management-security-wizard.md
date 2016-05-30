<properties
   pageTitle="Assistant Sécurité d’Azure AD Privileged Identity Management"
   description="La première fois que vous utilisez l’extension Azure Active Directory Privileged Identity Management, un Assistant Sécurité s’affiche. Cet article décrit les étapes d’utilisation de l’Assistant."
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

# Assistant Sécurité d’Azure AD Privileged Identity Management

Si vous êtes la première personne à exécuter Azure Privileged Identity Management (PIM) pour votre organisation, un Assistant s’affiche. Celui-ci vous aide à comprendre les risques liés à la sécurité des identités privilégiées et comment utiliser PIM pour les limiter. Vous n’avez pas besoin d’apporter des modifications aux affectations de rôle existantes dans l’Assistant. Vous pouvez le faire ultérieurement si vous préférez.

## À quoi s’attendre

Avant le démarrage de votre organisation à l’aide de PIM, toutes les attributions de rôles sont permanentes : les utilisateurs ont toujours ces rôles, même s’ils n’ont pas actuellement besoin de leurs privilèges. La première étape de l’Assistant vous montre une liste des rôles à privilèges élevés et le nombre d’utilisateurs ayant actuellement ces rôles. Vous pouvez explorer en détail un rôle particulier pour en savoir plus sur les utilisateurs si certains vous sont inconnus.

La deuxième étape de l’Assistant vous donne la possibilité de modifier les attributions du rôle d’administrateur.

> [AZURE.WARNING] Il est important de disposer d’au moins un administrateur général et plusieurs administrateurs de sécurité avec un compte professionnel ou scolaire (et non un compte Microsoft). S’il n’existe qu’un seul administrateur de sécurité, l’organisation ne sera pas en mesure d’utiliser PIM si ce compte est supprimé. N’attribuez pas non plus des rôles de manière temporaire si un utilisateur possède un compte Microsoft. Si vous envisagez d’exiger une authentification multifacteur (MFA) pour l’activation de ce rôle, cet utilisateur est exclu de ce rôle.


Une fois que vous avez apporté les modifications, l’Assistant ne s’affiche plus. La prochaine fois que vous ou un autre administrateur de sécurité utiliserez PIM, vous verrez le tableau de bord de PIM.

- Si vous souhaitez ajouter des utilisateurs dans des rôles ou en supprimer, ou encore modifier des affectations de l’état permanent à l’état temporaire, consultez la rubrique [Comment ajouter ou supprimer un rôle d’utilisateur](active-directory-privileged-identity-management-how-to-add-role-to-user.md) pour en savoir plus.
- Si vous souhaitez autoriser plus d’utilisateurs à gérer PIM, consultez la rubrique expliquant [comment affecter un accès à PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md) pour en savoir plus.



## Étapes suivantes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0518_2016-->