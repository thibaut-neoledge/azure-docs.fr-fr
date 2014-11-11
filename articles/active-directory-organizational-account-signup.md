<properties umbracoNaviHide="0" pageTitle="How To Configure Cloud Services" metaKeywords="Azure cloud services, cloud service, configure cloud service" description="Learn how to configure Azure cloud services." linkid="manage-windows-how-to-guide-storage-accounts" urlDisplayName="How to: storage accounts" headerExpose="" footerExpose="" disqusComments="1" title="Sign up for Azure as an organization" authors="terrylan" manager="terrylan" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="terrylan" />

# Inscription à Azure en tant qu'organisation

[WACOM.INCLUDE [disclaimer](../includes/disclaimer.md)]

Jusqu'à récemment, vous pouviez uniquement créer un abonnement Azure à l'aide d'un compte Microsoft (Windows Live ID). Azure prend désormais en charge l'utilisation des deux méthodes suivantes pour la création d'un compte :

-   **Comptes Microsoft** (que vous avez créés pour votre utilisation personnelle) : ils donnent accès à tous les produits et services cloud Microsoft orientés utilisateur, tels qu'Outlook (Hotmail), Messenger, SkyDrive, MSN, Xbox LIVE ou Office Live. La création d'un compte Outlook crée automatiquement un compte Microsoft avec une adresse @Outlook.com. Une fois le compte Microsoft créé, il vous permet d'accéder aux services cloud Microsoft orientés utilisateur et/ou à Azure. [En savoir plus][En savoir plus]

-   **Comptes professionnels** (créés par un administrateur à des fins commerciales/de formation) : ils donnent accès à tous les services cloud Microsoft professionnels destinés aux PME et aux grandes entreprises, tels qu'Azure, Windows Intune ou Office 365. Lorsque vous vous inscrivez à l'un de ces services en tant qu'organisation, un client cloud est automatiquement configuré dans Azure Active Directory pour représenter votre organisation. [En savoir plus][1]

    Une fois le client créé, un administrateur peut octroyer des comptes professionnels à chaque employé/étudiant et leur attribuer des licences en fonction des abonnements aux services cloud dont ils ont besoin, tels qu'Azure.

    *Pour vous inscrire à Azure en tant qu'organisation, cliquez* **ici**

## Qu'est-ce qu'Azure Active Directory ?

Tout comme Active Directory est un service mis à disposition des clients via le système d'exploitation Windows Server pour la gestion locale des identités, Azure Active Directory (Azure AD) est un service disponible via Azure pour la gestion de l'annuaire cloud de votre organisation. [En savoir plus][2]

Comme il s'agit de l'annuaire cloud de votre organisation, vous décidez quels sont vos utilisateurs, quelles informations sont conservées dans le cloud, quels utilisateurs sont autorisés à utiliser et à gérer les informations et quels services ou applications sont autorisés à accéder à celles-ci. Lorsque vous utilisez Azure AD, il est de la responsabilité de Microsoft de garantir l'exécution d'Active Directory dans le cloud avec une récupération d'urgence à grande échelle, à haute disponibilité et intégrée, tout en respectant les exigences en termes de confidentialité et de sécurité des informations de votre organisation.

### Intégration à un annuaire Active Directory local

Si votre organisation utilise déjà la version locale d'Active Directory, vous pouvez utiliser les capacités d'intégration d'annuaire d'Azure, comme la synchronisation d'un annuaire et l'authentification unique, pour prolonger davantage la portée de vos identités locales existantes dans le cloud pour une administration et une expérience utilisateur optimales. [En savoir plus][3]

  [disclaimer]: ../includes/disclaimer.md
  [En savoir plus]: http://windows.microsoft.com/fr-fr/windows-live/sign-in-what-is-microsoft-account
  [1]: http://technet.microsoft.com/fr-fr/library/jj573650
  [2]: http://technet.microsoft.com/fr-fr/library/hh967619
  [3]: http://technet.microsoft.com/fr-fr/library/jj573653
