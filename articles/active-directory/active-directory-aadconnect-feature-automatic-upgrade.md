<properties
   pageTitle="Azure AD Connect : Mise à niveau automatique | Microsoft Azure"
   description="Cette rubrique décrit la fonctionnalité de mise à niveau automatique intégrée dans Azure AD Connect Sync."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="02/16/2016"
   ms.author="andkjell"/>

# Azure AD Connect : Mise à niveau automatique
Cette fonctionnalité a été introduite avec la version 1.1.105.0 (publiée en février 2016).

## Vue d'ensemble
Grâce à la fonctionnalité de **mise à niveau automatique**, vous pouvez facilement vous assurer que votre installation Azure AD Connect est à jour. Cette fonctionnalité est activée par défaut pour les installations rapides et garantit que lorsqu’une nouvelle version est publiée, votre installation est automatiquement mise à niveau.

La mise à niveau automatique est activée par défaut pour les éléments suivants :

- Installation à l’aide de la configuration rapide
- SQL Express LocalDB, toujours utilisée par la configuration rapide
- Le compte AD est le compte MSOL\_ par défaut créé par la configuration rapide
- Avoir moins de 100 000 objets dans le métaverse

L’état actuel de la mise à niveau automatique peut être affiché avec l’applet de commande PowerShell `Get-ADSyncAutoUpgrade`. Elle peut avoir les états suivants :

| State | Commentaire |
| ---- | ---- |
| Activé | La mise à niveau automatique est activée. |
| Interrompu | Défini par le système uniquement. Le système n’est plus autorisé à recevoir des mises à niveau automatiques. |
| Désactivé | La mise à niveau automatique est désactivée. |

Vous pouvez passer de **Activé** à **Désactivé** avec `Set-ADSyncAutoUpgrade`. Seul le système doit définir l’état **Interrompu**.

La mise à niveau automatique utilise Azure AD Connect Health comme infrastructure de mise à niveau. Pour qu’une mise à niveau automatique fonctionne correctement, assurez-vous d’avoir ouvert l’URL dans le proxy pour **Azure AD Connect Health** comme indiqué à la rubrique [URL et plages d’adresses IP Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

Si l’interface utilisateur du **Synchronization Service Manager** est en cours d’exécution sur le serveur, la mise à niveau sera suspendue jusqu’à la fermeture de l’interface utilisateur.

## Étapes suivantes
En savoir plus sur l’[intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0218_2016-->