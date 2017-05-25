---
title: "Personnalisation des revendications émises dans le jeton SAML pour les applications pré-intégrées dans Azure Active Directory | Microsoft Docs"
description: "En savoir plus sur la personnalisation des revendications émises dans le jeton SAML pour les applications pré-intégrées dans Azure Active Directory"
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: femila
editor: 
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2016
ms.author: asmalser
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: e89a06de6232afef579c32d51137ddf577917436
ms.contentlocale: fr-fr
ms.lasthandoff: 04/27/2017


---
# <a name="customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps-in-azure-active-directory"></a>Personnalisation des revendications émises dans le jeton SAML pour les applications pré-intégrées dans Azure Active Directory
Aujourd’hui, Azure Active Directory prend en charge des milliers d’applications pré-intégrées dans la galerie d’applications Azure AD, notamment plus de 150 applications qui gèrent l’authentification unique à l’aide du protocole SAML 2.0. Quand un utilisateur s’authentifie auprès d’une application par l’intermédiaire d’Azure AD en utilisant SAML, Azure AD envoie un jeton à l’application (via une redirection HTTP 302). Après quoi, l’application valide et utilise ce jeton pour connecter l’utilisateur au lieu de lui demander un nom d’utilisateur et un mot de passe. Ces jetons SAML contiennent des informations sur l’utilisateur appelées « revendications ».

En jargon d’identité, une « revendication » concerne ce qu’un fournisseur d’identité déclare sur un utilisateur dans le jeton qu’il émet sur cet utilisateur. Dans un [jeton SAML](http://en.wikipedia.org/wiki/SAML_2.0), ces données sont généralement contenues dans l’instruction d’attribut SAML et l’ID unique de l’utilisateur unique est généralement représenté dans l’objet SAML.

Par défaut, Azure AD émet un jeton SAML à destination de votre application qui contient une revendication NameIdentifier, dont la valeur est le nom de l’utilisateur dans Azure AD. Cette valeur peut identifier l’utilisateur de manière unique. Le jeton SAML contient également des revendications supplémentaires contenant l’adresse de messagerie de l’utilisateur, son prénom et son nom.

Pour afficher ou modifier les revendications émises dans le jeton SAML à destination de l’application, ouvrez l’enregistrement de l’application dans le portail Azure Classic et sélectionnez l’onglet **Attributs** sous l’application.

![Onglet Attributs][1]

Vous pouvez être amené à modifier les revendications émises dans le jeton SAML pour les deux raisons suivantes :
* L’application a été écrite pour exiger un ensemble différent d’URI de revendication ou de valeurs de revendication 
* Votre application a été déployée d’une manière qui nécessite que la revendication NameIdentifier soit différente du nom d’utilisateur (nom d’utilisateur principal) stocké dans Azure Active Directory. 

Vous pouvez modifier n’importe quelle valeur de revendication par défaut. Sélectionnez l’icône en forme de crayon qui apparaît à droite chaque fois que vous placez la souris sur l’une des lignes du tableau d’attributs de jeton SAML. Vous pouvez également supprimer les revendications (autres que NameIdentifier) à l’aide de l’icône **X** et ajouter de nouvelles revendications à l’aide du bouton **Ajouter un attribut utilisateur**.

## <a name="editing-the-nameidentifier-claim"></a>Modification de la revendication NameIdentifier
Pour résoudre le problème dans lequel l’application a été déployée à l’aide d’un nom d’utilisateur différent, cliquez sur l’icône en forme de crayon en regard de la revendication NameIdentifier. Cette action donne accès à une boîte de dialogue qui contient différentes options :

![Modifier un attribut utilisateur][2]

Dans le menu **Valeur d’attribut**, sélectionnez **user.mail** pour faire correspondre la revendication NameIdentifier à l’adresse de messagerie de l’utilisateur contenue dans l’annuaire. Sinon, sélectionnez **user.onpremisessamaccountname** pour affecter le nom de compte SAM de l’utilisateur qui a été synchronisé à partir d’Azure AD en local.

Vous pouvez aussi utiliser la fonction spéciale ExtractMailPrefix() pour supprimer le suffixe de domaine de l’adresse de courrier ou du nom d’utilisateur principal. Seule la première partie du nom d’utilisateur est alors transmise (par exemple, « joe_smith » au lieu de joe_smith@contoso.com).

![Modifier un attribut utilisateur][3]

## <a name="adding-claims"></a>Ajout de revendications
Au moment d’ajouter une revendication, vous pouvez spécifier le nom d’attribut (qui ne doit pas nécessairement correspondre de manière stricte à un modèle d’URI, conformément à la spécification SAML). Faites correspondre la valeur à n’importe quel attribut utilisateur stocké dans l’annuaire.

![Ajouter un attribut utilisateur][4]

Par exemple, vous devez envoyer le service auquel appartient l’utilisateur au sein de son entreprise en tant que revendication (par exemple, Ventes). Vous pouvez entrer la valeur de revendication qui est attendue par l’application, puis sélectionner **user.department** comme valeur.

Si pour un utilisateur donné, aucune valeur n’est stockée pour un attribut sélectionné, la revendication n’est pas émise dans le jeton.

**Remarque :** **user.onpremisesecurityidentifier** et **user.onpremisesamaccountname** sont uniquement pris en charge lors de la synchronisation des données utilisateur issues de l’annuaire Active Directory local en utilisant [l’outil Azure AD Connect](../active-directory-aadconnect.md).

## <a name="next-steps"></a>Étapes suivantes
* [Index d’articles pour la gestion des applications dans Azure Active Directory](../active-directory-apps-index.md)
* [Configuration de l'authentification unique pour les applications ne faisant pas partie de la galerie d'applications Azure Active Directory.](../active-directory-saas-custom-apps.md)
* [Dépannage de l’authentification unique basée sur SAML](active-directory-saml-debugging.md)

<!--Image references-->
[1]: ../media/active-directory-saml-claims-customization/claimscustomization1.png
[2]: ../media/active-directory-saml-claims-customization/claimscustomization2.png
[3]: ../media/active-directory-saml-claims-customization/claimscustomization3.png
[4]: ../media/active-directory-saml-claims-customization/claimscustomization4.png
