---
title: "Azure AD Connect : principes de conception | Microsoft Docs"
description: "Cette rubrique détaille certains aspects de la conception de l’implémentation"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 4114a6c0-f96a-493c-be74-1153666ce6c9
ms.service: active-directory
ms.custom: azure-ad-connect
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 09/13/2016
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 116ef0786c9c45b549311a59615d06f194093bc2


---
# <a name="azure-ad-connect-design-concepts"></a>Principes de conception Azure AD Connect
L’objectif de cette rubrique est de décrire les principes qui doivent présider à la conception de l’implémentation d’Azure AD Connect. Il s’agit d’une exploration approfondie de certains aspects. Ces concepts sont également décrits brièvement dans d’autres rubriques.

## <a name="sourceanchor"></a>sourceAnchor
L’attribut sourceAnchor est défini en tant qu’ *attribut immuable pendant la durée de vie d’un objet*. Il identifie de façon univoque un objet comme étant le même objet local et dans Azure AD. L’attribut est également appelé **immutableId** et les deux noms sont interchangeables.

Le mot « immuable », signifiant « qui ne peut pas être changé », est important dans cette rubrique. Étant donné que la valeur de cet attribut ne peut pas être changée une fois qu’elle a été définie, il est important de choisir une conception qui prend en charge votre scénario.

L’attribut est utilisé pour les scénarios suivants :

* Quand un nouveau serveur de moteur de synchronisation est créé ou recréé après un scénario de récupération d’urgence, cet attribut lie les objets existants dans Azure AD à des objets locaux.
* Si vous passez d’une identité de cloud uniquement à un modèle d’identité synchronisé, alors cet attribut permet une correspondance exacte et concrète des objets existants dans Azure AD avec des objets locaux.
* Si vous utilisez la fédération, alors cet attribut est utilisé avec **userPrincipalName** dans la revendication pour identifier un utilisateur de façon univoque.

Cette rubrique traite de sourceAnchor seulement par rapport aux utilisateurs. Les mêmes règles s’appliquent à tous les types d’objets, mais le problème ne se pose généralement que pour des utilisateurs.

### <a name="selecting-a-good-sourceanchor-attribute"></a>Sélection d’un attribut sourceAnchor approprié
La valeur de l’attribut doit respecter les règles suivantes :

* sa longueur doit être inférieure à 60 caractères
  * les caractères autres que a-z, A-Z ou 0-9 sont codés et comptabilisés comme 3 caractères
* elle ne doit pas contenir les caractères spéciaux suivants : &#92; ! # $ % & * + / = ? ^ &#96; { } | ~ < > ( ) ' ; : , [ ] " @ _
* elle doit être globalement unique
* elle doit être une chaîne, un entier ou une valeur binaire
* elle ne doit pas être basée sur le nom de l’utilisateur, celui-ci est susceptible de changer
* elle ne doit pas respecter la casse et doit éviter les valeurs qui peuvent varier selon la casse
* elle doit être assignée lorsque l’objet est créé.

Si le sourceAnchor sélectionné n’est pas de type chaîne, alors Azure AD Connect encode la valeur de l’attribut en base 64 pour garantir qu’aucun caractère spécial n’apparaît. Si vous utilisez un serveur de fédération autre qu’ADFS, assurez-vous que votre serveur a également la capacité d’encoder la valeur de l’attribut en base 64.

L’attribut sourceAnchor respecte la casse. La valeur « JohnDoe » n’est pas le même que « johndoe ». Mais vous ne devez pas avoir deux objets différents avec uniquement une différence de casse.

Si vous avez une seule forêt locale, alors l’attribut que vous devez utiliser est **objectGUID**. C’est également l’attribut utilisé quand vous utilisez la configuration rapide dans Azure AD Connect, ainsi que l’attribut utilisé par DirSync.

Si vous avez plusieurs forêts et que vous ne déplacez pas d’utilisateurs entre les forêts et les domaines , alors **objectGUID** est un attribut approprié à utiliser même dans ce cas.

Si vous déplacez des utilisateurs entre des forêts et des domaines, alors vous devez trouver un attribut qui ne change pas ou qui peut être déplacé en même temps que les utilisateurs. Une approche recommandée est d'introduire un attribut synthétique. Un attribut qui peut contenir quelque chose qui ressemble à un GUID serait approprié. Lors de la création d’un objet, un nouveau GUID est créé et affecté à l’utilisateur. Une règle de synchronisation personnalisée peut être créée dans le serveur du moteur de synchronisation pour générer cette valeur selon **objectGUID** et mettre à jour l’attribut sélectionné dans ADDS. Quand vous déplacez l’objet, veillez à copier également le contenu de cette valeur.

Une autre solution consiste à choisir un attribut existant, dont vous êtes sûr qu’il ne changera pas. **employeeID**est un des attributs couramment utilisés. Si vous envisagez d’opter pour un attribut contenant des lettres, assurez-vous qu’il n’y a aucun risque de changement de la casse (majuscule ou minuscule) pour la valeur de l’attribut. Des attributs inappropriés qui ne doivent pas être utilisés sont notamment ceux qui ont le nom de l’utilisateur. En cas de mariage ou de divorce, le nom risque de changer, ce qui n’est pas autorisé pour cet attribut. C'est également une raison pour laquelle il n'est même pas possible de sélectionner des attributs comme **userPrincipalName**, **mail** et **targetAddress** dans l'Assistant Installation d'Azure AD Connect. Ces attributs contiennent également le caractère @-character,, qui n’est pas autorisé dans sourceAnchor.

### <a name="changing-the-sourceanchor-attribute"></a>Changement de l’attribut sourceAnchor
La valeur de l’attribut sourceAnchor ne peut pas être changée une fois que l’objet a été créé dans Azure AD et que l’identité est synchronisée.

Pour cette raison, les restrictions suivantes s’appliquent à Azure AD Connect :

* L’attribut sourceAnchor peut être défini seulement lors de l’installation initiale. Si vous exécutez une nouvelle fois l’Assistant Installation, cette option est en lecture seule. Si vous devez changer ce paramètre, alors vous devez effectuer une désinstallation et une réinstallation.
* Si vous installez un autre serveur Azure AD Connect, vous devez sélectionner le même attribut sourceAnchor que celui qui a été précédemment utilisé. Si vous avez précédemment utilisé DirSync et que vous passez à Azure AD Connect, vous devez utiliser **objectGUID** , car il s'agit de l’attribut utilisé par DirSync.
* Si la valeur de sourceAnchor est changée après que l’objet a été exporté vers Azure AD, alors Azure AD Connect Sync génère une erreur et n’autorise plus de modifications sur cet objet avant que le problème ait été résolu et que sourceAnchor soit replacé à sa valeur précédente dans le répertoire source.

## <a name="azure-ad-sign-in"></a>Authentification dans Azure AD
Lorsque vous intégrez votre annuaire local à Azure AD, il est important de bien comprendre comment les paramètres de synchronisation peuvent affecter la manière dont les utilisateurs s’authentifient. Azure AD utilise userPrincipalName (UPN) pour authentifier l’utilisateur. Toutefois, lorsque vous synchronisez vos utilisateurs, vous devez choisir avec soin l’attribut à utiliser pour la valeur de userPrincipalName.

### <a name="choosing-the-attribute-for-userprincipalname"></a>Choix de l’attribut de userPrincipalName
Lorsque vous sélectionnez l’attribut fournissant la valeur d’UPN à utiliser dans Azure, vous devez vous assurer que :

* Les valeurs de l’attribut sont conformes à la syntaxe UPN (RFC 822), c’est-à-dire au format username@domain
* Le suffixe des valeurs correspond à l’un des domaines personnalisés vérifiés dans Azure AD

Dans la configuration rapide, le choix supposé de l’attribut est userPrincipalName. Si l’attribut userPrincipalName ne contient pas la valeur que vous souhaitez que vos utilisateurs utilisent pour se connecter à Azure, alors choisissez **Installation personnalisée**.

### <a name="custom-domain-state-and-upn"></a>État du domaine personnalisé et UPN
Il est important de s’assurer qu’il existe un domaine vérifié pour le suffixe UPN.

John est un utilisateur de contoso.com. Vous souhaitez que John utilise l’UPN local john@contoso.com pour se connecter à Azure une fois que vous avez synchronisé les utilisateurs sur votre répertoire Azure AD contoso.onmicrosoft.com. Pour ce faire, vous devez ajouter et vérifier contoso.com comme domaine personnalisé dans Azure AD avant de commencer la synchronisation des utilisateurs. Si le suffixe UPN de John, par exemple contoso.com, ne correspond pas à un domaine vérifié dans Azure AD, alors Azure AD remplace le suffixe UPN par contoso.onmicrosoft.com.

### <a name="non-routable-on-premises-domains-and-upn-for-azure-ad"></a>Domaines locaux non routables et UPN pour Azure AD
Certaines organisations ont des domaines non routables, comme contoso.local, ou de simples domaines à étiquette unique, comme contoso. Dans Azure AD, vous n’êtes pas en mesure de vérifier un domaine non routable. Azure AD Connect peut uniquement se synchroniser sur un domaine vérifié dans Azure AD. Lorsque vous créez un répertoire Azure AD, il crée un domaine routable qui devient le domaine par défaut de votre Azure AD, par exemple contoso.onmicrosoft.com. Par conséquent, il devient nécessaire de vérifier tous les autres domaines routables dans un scénario de ce type, si vous ne souhaitez pas effectuer de synchronisation avec le domaine par défaut onmicrosoft.com.

Pour plus d’informations sur l’ajout et la vérification de domaines, consultez [Ajouter un nom de domaine personnalisé à Azure Active Directory](active-directory-add-domain.md) .

Azure AD Connect détecte si vous exécutez un environnement de domaine non routable et vous avertit en temps utile si vous tentez de poursuivre la configuration rapide. Si votre domaine n’est pas routable, il est probable que les UPN des utilisateurs aient également des suffixes non routables. Par exemple, si votre domaine est contoso.local, Azure AD Connect vous propose d’utiliser des paramètres personnalisés plutôt que la configuration rapide. Avec les paramètres personnalisés, vous êtes en mesure de spécifier l’attribut à utiliser comme UPN pour la connexion à Azure une fois les utilisateurs synchronisés avec Azure AD.

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur l’ [intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).




<!--HONumber=Nov16_HO3-->


