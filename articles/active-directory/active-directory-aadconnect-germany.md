---
title: Azure AD Connect dans Microsoft Cloud Germany
description: Azure AD Connect intègre vos répertoires locaux à Azure Active Directory. Cela vous permet de fournir une identité commune pour les applications Office 365, Azure et SaaS intégrées à Azure AD.
keywords: introduction à Azure AD Connect, présentation d’Azure AD Connect, qu’est-ce qu’Azure AD Connect, installation d’active directory, Germany, Black Forest
services: active-directory
documentationcenter: ''
author: billmath
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/08/2016
ms.author: billmath

---
# Azure AD Connect dans Microsoft Cloud Germany - Version préliminaire
## Introduction
Azure AD Connect fournit la synchronisation entre Active Directory local et Azure Active Directory. Actuellement, la plupart des scénarios dans [Microsoft Cloud Germany](https://www.microsoft.com/de-de/cloud/deutschland/default.aspx) doivent être réalisés par l’opérateur. Lorsque vous utilisez Microsoft Cloud Germany, vous devez tenir compte de ce qui suit :

* Les URL suivantes doivent être ouvertes sur un serveur proxy pour que la synchronisation réussisse :
  
  * *.microsoftonline.de
  * *.windows.net
  * * Listes de révocation de certificat
* Lorsque vous vous connectez à votre annuaire Azure AD, vous devez utiliser un compte du domaine onmicrosoft.de.
* Les fonctionnalités suivantes ne sont pas disponibles :
  * Azure AD Connect Health
  * Mises à jour automatiques
  * Écriture différée du mot de passe

## Télécharger
Vous pouvez télécharger Azure AD Connect à partir du panneau Azure AD Connect dans le portail. Utilisez les instructions ci-dessous pour localiser le panneau Azure AD Connect.

### Panneau Azure AD Connect
Une fois connecté au portail Azure, procédez comme suit :

1. Accédez à parcourir
2. Sélectionnez Azure Active Directory
3. Puis sélectionnez Azure AD Connect

Les éléments suivants doivent s’afficher :

![Panneau Azure AD Connect](media\\active-directory-aadconnect-germany\\germany1.png)

Le tableau suivant décrit les fonctionnalités affichées dans le panneau.

| Intitulé | Description |
| --- | --- |
| ÉTAT DE LA SYNCHRONISATION |Vous indique si la synchronisation est activée ou désactivée. |
| DERNIÈRE SYNCHRONISATION |Dernière fois qu’une synchronisation réussie s’est terminée. |
| DOMAINES FÉDÉRÉS |Affiche le nombre de domaines fédérés actuellement configurés. |

## Installation
Pour installer Azure AD Connect, vous pouvez utiliser la documentation [ici](active-directory-aadconnect.md#install-azure-ad-connect).

## Fonctionnalités avancées et informations supplémentaires
Pour plus d’informations et des conseils sur les paramètres personnalisés ou les configurations avancées, commencez par [Intégration des identités locales avec Azure Active Directory](active-directory-aadconnect.md). Cette page fournit des informations et des liens vers des conseils supplémentaires.

<!---HONumber=AcomDC_0914_2016-->