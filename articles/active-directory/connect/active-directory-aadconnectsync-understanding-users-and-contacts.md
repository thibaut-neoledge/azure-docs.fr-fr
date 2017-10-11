---
title: "Azure AD Connect Sync : Présentation des utilisateurs et des contacts | Microsoft Docs"
description: "Décrit les utilisateurs et les contacts dans Azure AD Connect Sync."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 8d204647-213a-4519-bd62-49563c421602
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: markvi;andkjell
ms.openlocfilehash: 495e926a1975128707212ba571d47cfc7c9507b3
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2017
---
# <a name="azure-ad-connect-sync-understanding-users-and-contacts"></a>Azure AD Connect Sync : Présentation des utilisateurs et des contacts
Il existe plusieurs raisons pour lesquelles vous pouvez avoir plusieurs forêts Active Directory et il existe plusieurs topologies de déploiement différentes. Parmi les modèles courants, citons les déploiements de ressources de comptes et les forêts avec liste d’adresses globale synchronisées après fusion et acquisition. Mais même s’il existe des modèles pures, les modèles hybrides sont également courants. La configuration par défaut du service de synchronisation Azure AD Connect ne suppose pas l’existence d’un modèle particulier, mais des comportements différents peuvent être observés en fonction de la façon dont la correspondance utilisateur a été sélectionnée dans le guide d’installation.

Dans cette rubrique, nous allons voir comment se comporte la configuration par défaut dans certaines topologies. Nous allons examiner la configuration et voir comment utiliser l’éditeur de règles de synchronisation pour vérifier la configuration.

Il existe quelques règles générales que la configuration considère comme étant respectées :

* Quel que soit l’ordre dans lequel nous importons des données depuis les annuaires Active Directory sources, le résultat final doit toujours être le même.
* Un compte actif fournira toujours des informations de connexion, y compris **userPrincipalName** et **sourceAnchor**.
* Un compte désactivé fournira userPrincipalName et sourceAnchor, sauf s’il s’agit d’une boîte aux lettres liée si aucun compte actif n’est trouvé.
* Un compte avec une boîte aux lettres liée ne sera jamais utilisé pour userPrincipalName et sourceAnchor. On part du principe qu’un compte actif sera trouvé ultérieurement.
* Un objet de contact peut être approvisionné pour Azure AD en tant que contact ou utilisateur. Vous ne saurez s’il s’agit de l’un ou de l’autre qu’une fois que toutes les forêts Active Directory sources auront été traitées.

## <a name="contacts"></a>Contacts
Avoir des contacts représentant un utilisateur dans une autre forêt est courant après une fusion et acquisition où une solution GALSync joue le rôle de pont entre plusieurs forêts Exchange. L’objet de contact est toujours joint de l’espace de connecteur au métaverse à l’aide de l’attribut de messagerie. S’il existe déjà un objet contact ou un objet utilisateur avec la même adresse de messagerie, les objets sont joints. Ce comportement est configuré dans la règle **In from AD – Contact Join**. Il existe également une règle nommée **In from AD – Contact Common** avec un flux d’attribut vers l’attribut de métaverse **sourceObjectType** avec la constante **Contact**. Cette règle a une priorité très faible. Ainsi, si un objet utilisateur est joint au même objet Metaverse, la règle **In from AD – User Common** fournit la valeur User à cet attribut. Avec cette règle, cet attribut a la valeur Contact si aucun utilisateur n’a été joint et la valeur User si au moins un utilisateur a été trouvé.

Pour l’approvisionnement d’un objet dans Azure AD, la règle sortante **Out to AAD – Contact Join** crée un objet contact si l’attribut de métaverse **sourceObjectType** a la valeur **Contact**. Si cet attribut a la valeur **User**, la règle **Out to AAD – User Join** crée plutôt un objet utilisateur.
Il est possible qu’un objet soit promu de Contact en User quand davantage d’annuaires Active Directory sources sont importés et synchronisés.

Par exemple, dans une topologie GALSync, nous trouverons des objets contacts pour tous les membres de la deuxième forêt quand nous importerons la première forêt. De nouveaux objets contacts seront ainsi créés dans le connecteur AAD. Quand plus tard nous importerons et synchroniserons la deuxième forêt, nous trouverons les utilisateurs réels et nous les joindrons aux objets de métaverse existants. Nous supprimerons ensuite l’objet contact dans AAD et nous créerons un objet utilisateur à la place.

Si vous disposez d’une topologie dans laquelle les utilisateurs sont représentés en tant que contacts, veillez à sélectionner la mise en correspondance des utilisateurs sur l’attribut de messagerie dans le guide d’installation. Si vous sélectionnez une autre option, vous aurez une configuration dépendant de l’ordre. Les objets contacts seront toujours joints sur l’attribut de messagerie, mais les objets utilisateurs seront joints sur l’attribut de messagerie uniquement si cette option a été sélectionnée dans le guide d’installation. Vous pourriez alors vous retrouver avec deux objets différents dans le métaverse avec le même attribut de messagerie si l’objet contact a été importé avant l’objet utilisateur. Lors de l’exportation vers Azure AD, une erreur sera levée. Ce comportement est normal et indique que des données sont incorrectes ou que la topologie n’a pas été identifiée correctement lors de l’installation.

## <a name="disabled-accounts"></a>Comptes désactivés
Les comptes désactivés sont aussi synchronisés vers Azure AD. Les comptes désactivés représentent couramment des ressources dans Exchange, par exemple des salles de conférence. L’exception concerne les utilisateurs avec une boîte aux lettres liée ; comme mentionné précédemment, ces utilisateurs ne configureront jamais un compte dans Azure AD.

L’hypothèse est que si un compte d’utilisateur désactivé est détecté, nous ne trouverons pas ultérieurement d’autre compte actif et l’objet est approvisionné dans Azure AD avec les attributs userPrincipalName et sourceAnchor trouvés. Dans le cas où un autre compte actif joindrait le même objet de métaverse, ses attributs userPrincipalName et sourceAnchor seraient utilisés.

## <a name="changing-sourceanchor"></a>Changement de l’attribut sourceAnchor
Quand un objet a été exporté vers Azure AD, il n’est plus autorisé à modifier l’attribut sourceAnchor. Quand l’objet a été exporté, l’attribut de métaverse **cloudSourceAnchor** est défini avec la valeur **sourceAnchor** acceptée par Azure AD. Si **sourceAnchor** est modifié et ne correspond pas à **cloudSourceAnchor**, la règle **Out to AAD – User Join** génère l’erreur **L’attribut sourceAnchor a changé**. Dans ce cas, la configuration ou les données doivent être corrigées pour que le même sourceAnchor soit présent dans le métaverse et que l’objet puisse être de nouveau synchronisé.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Azure AD Connect Sync : personnalisation des options de synchronisation](active-directory-aadconnectsync-whatis.md)
* [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)

