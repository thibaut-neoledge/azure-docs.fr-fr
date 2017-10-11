---
title: 'Azure AD Connect : Instances de service Sync | Microsoft Docs'
description: "Cette page décrit des considérations spéciales relatives aux instances d’Azure AD."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: f340ea11-8ff5-4ae6-b09d-e939c76355a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: e8321c3d16253226a5931cacbce6fa5d50b697bd
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2017
---
# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect : considérations spéciales relatives aux instances
Azure AD Connect est couramment utilisé avec l’instance mondiale d’Azure AD et Office 365. Mais il existe également d’autres instances, qui ont des exigences différentes en matière d’URL et autres considérations spéciales.

## <a name="microsoft-cloud-germany"></a>Microsoft Cloud Allemagne
[Microsoft Cloud Allemagne](http://www.microsoft.de/cloud-deutschland) est un cloud souverain géré par un client allemand approuvé dans le domaine des données.

| URL à ouvrir dans le serveur proxy |
| --- |
| \*.microsoftonline.de |
| \*.windows.net |
| + Listes de révocation de certificat |

Quand vous vous connectez à votre locataire Azure AD, vous devez utiliser un compte du domaine onmicrosoft.de.

Fonctionnalités actuellement absentes de Microsoft Cloud Allemagne :

* **Azure AD Connect Health** n’est pas disponible.
* Les **mises à jour automatiques** ne sont pas disponibles.
* L’**écriture différée de mot de passe** est disponible en préversion avec Azure AD Connect version 1.1.570.0 et ultérieures.
* Les autres services Azure AD Premium ne sont pas disponibles.

## <a name="microsoft-azure-government-cloud"></a>Cloud Microsoft Azure Government
Le [cloud Microsoft Azure Government](https://azure.microsoft.com/features/gov/) est un cloud du gouvernement des États-Unis.

Ce cloud a été pris en charge par des versions antérieures de DirSync. À partir de la build 1.1.180 d’Azure AD Connect, la nouvelle génération du cloud est prise en charge. Cette génération utilise des points de terminaison États-Unis uniquement et a sa propre liste d’URL à ouvrir dans votre serveur proxy.

| URL à ouvrir dans le serveur proxy |
| --- |
| \*.microsoftonline.com |
| \*.microsoftonline.us |
| \*.gov.us.microsoftonline.com |
| + Listes de révocation de certificat |

Azure AD Connect ne peut pas détecter automatiquement que votre locataire Azure AD se trouve dans le cloud Government. Au lieu de cela, vous devez effectuer les actions suivantes lorsque vous installez Azure AD Connect.

1. Lancez l’installation d’Azure AD Connect.
2. Quand vous voyez apparaître la première page où vous êtes censé accepter le CLUF, ne poursuivez pas, mais laissez l’Assistant Installation en cours d’exécution.
3. Lancez regedit et modifiez la clé de registre de la valeur `HKLM\SOFTWARE\Microsoft\Azure AD Connect\AzureInstance` à la valeur `2`.
4. Revenez à l’Assistant Installation d’Azure AD Connect, acceptez le CLUF et continuez. Pendant l’installation, veillez à utiliser le chemin d’accès d’installation de la **configuration personnalisée** (et non l’installation rapide). Puis continuez l’installation normalement.

Fonctionnalités actuellement absentes du cloud Microsoft Azure Government :

* **Azure AD Connect Health** n’est pas disponible.
* Les **mises à jour automatiques** ne sont pas disponibles.
* L’**écriture différée de mot de passe** est disponible en préversion avec Azure AD Connect version 1.1.570.0 et ultérieures.
* Les autres services Azure AD Premium ne sont pas disponibles.

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur l’ [intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).
