<properties
   pageTitle="Comment effectuer une révision de sécurité | Microsoft Azure"
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
   ms.date="05/19/2016"
   ms.author="kgremban"/>

# Comment effectuer une révision de la sécurité dans Azure AD Privileged Identity Management


Les administrateurs de rôle privilégié peuvent examiner un accès privilégié une fois qu’une [révision de la sécurité a été démarrée](active-directory-privileged-identity-management-how-to-start-security-review.md). Azure AD Privileged Identity Management (PIM) envoie automatiquement un e-mail demandant aux utilisateurs d'examiner leur accès. Si un utilisateur n'a pas reçu d’e-mail, vous pouvez lui envoyer les instructions dans [comment effectuer une révision de sécurité](active-directory-privileged-identity-management-how-to-perform-security-review.md).

Après la période de révision de sécurité, ou après que tous les utilisateurs ont terminé leur auto-examen, suivez les étapes décrites dans cet article pour gérer la révision et afficher les résultats.

## Gérer les révisions de sécurité

1. Accédez à la [portail Azure](https://portal.azure.com/) et sélectionnez l’application **Azure AD Privileged Identity Management** sur votre tableau de bord.
2. Sélectionnez la section **Révisions de sécurité** du tableau de bord. Le panneau **Révisions de sécurité** s’affiche.
3. Sélectionnez la révision de sécurité que vous souhaitez gérer. Le panneau des détails de la révision de sécurité s’affiche.

Une fois que vous êtes sur le panneau de détails de la révision de sécurité, il y a trois options pour la gestion de cette révision. Vous pouvez effectuer la révision, exporter les détails ou la supprimer.

### Effectuer ou arrêter une révision

Si les utilisateurs se voient refuser l’accès, vous pouvez effectuer la révision pour supprimer cette attribution de rôle d'utilisateur du répertoire. Le bouton **Arrêter la révision** permet d’archiver la révision.

### Exporter une révision

Si vous souhaitez appliquer manuellement les résultats de la révision de sécurité, vous pouvez exporter la révision. Le bouton **Exporter** fait démarrer le téléchargement d'un fichier CSV. Vous pouvez gérer les résultats dans Excel ou d'autres programmes qui s'ouvrent les fichiers CSV.

### Supprimer une révision

> [AZURE.IMPORTANT] La suppression ayant lieu sans affichage préalable d’un avertissement, assurez-vous que vous voulez supprimer cette révision.

Si vous n'êtes plus intéressé par la révision, supprimez-la. Le bouton **Supprimer** permet de supprimer la révision de l'application PIM.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0525_2016-->