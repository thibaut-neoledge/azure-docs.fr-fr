<properties
   pageTitle="Azure AD Connect : principes de conception | Microsoft Azure"
   description="Cette rubrique détaille certains aspects de la conception de l’implémentation"
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="Identity"
   ms.date="06/27/2016"
   ms.author="andkjell"/>

# Principes de conception Azure AD Connect
L’objectif de cette rubrique est de décrire les principes qui doivent présider à la conception de l’implémentation d’Azure AD Connect. Il s’agit d’une exploration approfondie de certains aspects. Ces concepts sont également décrits brièvement dans d’autres rubriques.

## sourceAnchor
L’attribut sourceAnchor est défini en tant qu’*attribut immuable pendant la durée de vie d’un objet*. Il identifie de façon univoque un objet comme étant le même objet local et dans Azure AD. L’attribut est également appelé **immutableId** et les deux noms sont interchangeables.

Le mot « immuable », signifiant « qui ne peut pas être changé », est important dans cette rubrique. Étant donné que la valeur de cet attribut ne peut pas être changée une fois qu’elle a été définie, il est important de choisir une conception qui prend en charge votre scénario.

L’attribut est utilisé pour les scénarios suivants :

- Quand un nouveau serveur de moteur de synchronisation est créé ou recréé après un scénario de récupération d’urgence, cet attribut lie les objets existants dans Azure AD à des objets locaux.
- Si vous passez d’une identité de cloud uniquement à un modèle d’identité synchronisé, cet attribut permet une correspondance exacte et concrète des objets existants dans Azure AD avec des objets locaux.
- Si vous utilisez la fédération, cet attribut est utilisé avec **userPrincipalName** dans la revendication pour identifier un utilisateur de façon univoque.

Cette rubrique va traiter de sourceAnchor seulement par rapport aux utilisateurs. Les mêmes règles s’appliquent à tous les types d’objets, mais le problème ne se pose généralement que pour des utilisateurs.

### Sélection d’un attribut sourceAnchor approprié
La valeur de l’attribut doit respecter les règles suivantes :

- sa longueur doit être inférieure à 60 caractères
    - Les caractères autres que a-z, A-Z ou 0-9 seront codés et comptabilisées comme 3 caractères.
- elle ne doit pas contenir les caractères spéciaux suivants : &#92; ! # $ % & * + / = ? ^ &#96; { }| ~ < > ( ) ' ; : , [ ] " @ _
- elle doit être globalement unique
- elle doit être une chaîne, un entier ou une valeur binaire
- elle ne doit pas être basée sur le nom de l'utilisateur, celui-ci est susceptible de changer
- elle ne doit pas respecter la casse et doit éviter les valeurs qui peuvent varier selon la casse
- elle doit être assignée lorsque l'objet est créé.

Si le sourceAnchor sélectionné n’est pas de type chaîne, Azure AD Connect encode la valeur de l’attribut en base 64 pour garantir qu’aucun caractère spécial n’apparaît. Si vous utilisez un serveur de fédération autre qu’AD FS, assurez-vous que votre serveur a également la capacité d’encoder la valeur de l’attribut en base 64.

L’attribut sourceAnchor respecte la casse. La valeur « JohnDoe » n’est pas le même que « johndoe ».

Si vous avez une seule forêt locale, l'attribut que vous devez utiliser est **objectGUID**. C’est également l’attribut utilisé quand vous utilisez la configuration rapide dans Azure AD Connect, ainsi que l’attribut utilisé par DirSync.

Si vous avez plusieurs forêts et que vous ne déplacez pas des utilisateurs entre les forêts et entre les domaines d'une même forêt, **objectGUID** est un attribut approprié à utiliser même dans ce cas.

Si vous déplacez des utilisateurs entre des forêts et domaines, vous devez trouver un attribut qui ne changera pas ou qui peut être déplacé en même temps que les utilisateurs. Une approche recommandée est d'introduire un attribut synthétique. Un attribut qui peut contenir quelque chose qui ressemble à un GUID serait approprié. Lors de la création d’un objet, un nouveau GUID est créé et affecté à l’utilisateur. Une règle personnalisée peut être créée dans le serveur du moteur de synchronisation pour créer cette valeur selon **objectGUID** et mettre à jour l'attribut sélectionné dans ADDS. Quand vous déplacez l’objet, veillez à copier également le contenu de cette valeur.

Une autre solution consiste à choisir un attribut existant, dont vous êtes sûr qu'il ne changera pas. **employeeID** est un des attributs couramment utilisés. Si vous envisagez un attribut contenant des lettres, assurez-vous de l'absence de risque que la casse (majuscule ou minuscule) change pour la valeur de l’attribut. Des attributs inappropriés qui ne doivent pas être utilisés sont notamment ceux qui ont le nom de l’utilisateur. En cas de mariage ou de divorce, le nom risque de changer, ce qui n’est pas autorisé pour cet attribut. C'est également une raison pour laquelle il n'est même pas possible de sélectionner des attributs comme **userPrincipalName**, **mail** et **targetAddress** dans l'Assistant Installation d'Azure AD Connect. Ces attributs contiennent également le caractère « @ », qui n’est pas autorisé dans sourceAnchor.

### Changement de l’attribut sourceAnchor
La valeur de l’attribut sourceAnchor ne peut pas être changée une fois que l’objet a été créé dans Azure AD et que l’identité est synchronisée.

Pour cette raison, les restrictions suivantes s’appliquent à Azure AD Connect :

- L’attribut sourceAnchor peut être défini seulement lors de l’installation initiale. Si vous réexécutez l’Assistant Installation, cette option est en lecture seule. Si vous devez changer cette valeur, vous devez désinstaller et réinstaller.
- Si vous installez un autre serveur Azure AD Connect, vous devez sélectionner le même attribut sourceAnchor que celui qui a été précédemment utilisé. Si vous avez précédemment utilisé DirSync et que vous passez à Azure AD Connect, vous devez utiliser **objectGUID**, car il s'agit de l’attribut utilisé par DirSync.
- Si la valeur de sourceAnchor est changée après que l’objet a été exporté vers Azure AD, Azure AD Connect Sync génère une erreur et n’autorise plus de modifications sur cet objet avant que le problème ait été résolu et que sourceAnchor soit replacé à sa valeur précédente dans l’annuaire source.

## Authentification dans Azure AD

Lorsque vous intégrez votre annuaire local à Azure AD, il est important de bien comprendre comment les paramètres de synchronisation peuvent affecter la manière dont les utilisateurs s’authentifient. Azure AD utilise userPrincipalName ou UPN pour authentifier l’utilisateur. Toutefois, lorsque vous synchronisez vos utilisateurs, vous devez choisir avec soin l’attribut à utiliser pour la valeur de userPrincipalName.

### Choix de l’attribut de userPrincipalName

Lorsque vous sélectionnez l’attribut fournissant la valeur d’UPN à utiliser dans Azure, vous devez vous assurer que :

* Les valeurs de l’attribut sont conformes à la syntaxe UPN (RFC 822), c’est-à-dire au format username@domain.
* Le suffixe des valeurs correspond à l’un des domaines personnalisés vérifiés dans Azure AD

Dans la configuration rapide, le choix supposé de l’attribut est userPrincipalName. Toutefois, si vous pensez que l’attribut userPrincipalName ne contient pas la valeur que vous souhaiteriez que vos utilisateurs utilisent pour se connecter à Azure, vous devez choisir **Installation personnalisée** et fournir l’attribut approprié.

### État du domaine personnalisé et UPN
Il est important de s’assurer qu’il existe un domaine vérifié pour le suffixe UPN.

John est un utilisateur de contoso.com. Vous souhaitez que John utilise l’UPN local john@contoso.com pour se connecter à Azure une fois que vous avez synchronisé les utilisateurs sur votre annuaire Azure AD azurecontoso.onmicrosoft.com. Pour ce faire, vous devez ajouter et vérifier contoso.com comme domaine personnalisé dans Azure AD avant de commencer la synchronisation des utilisateurs. Si le suffixe UPN de John, contoso.com, ne correspond pas à un domaine vérifié dans Azure AD, Azure AD remplace le suffixe UPN par azurecontoso.onmicrosoft.com, et John devra utiliser john@azurecontoso.onmicrosoft.com pour se connecter à Azure.

### Domaines locaux non routables et UPN pour Azure AD
Certaines organisations ont des domaines non routables, comme contoso.local, ou simplement des domaines à étiquette unique, comme contoso. Dans Azure AD, vous ne pourrez pas vérifier un domaine non routable. Azure AD Connect peut uniquement se synchroniser sur un domaine vérifié dans Azure AD. Lorsque vous créez un annuaire Azure AD, il crée un domaine routable qui devient le domaine par défaut de votre Azure AD, par exemple contoso.onmicrosoft.com. Par conséquent, il devient nécessaire de vérifier tous les autres domaines routables dans un scénario de ce type, si vous ne souhaitez pas effectuer de synchronisation avec le domaine par défaut .onmicrosoft.com.

Pour plus d’informations sur l’ajout et la vérification de domaines, lisez [Ajout de votre nom de domaine personnalisé à Azure Active Directory](active-directory-add-domain.md).

Azure AD Connect détecte si vous exécutez un environnement de domaine non routable et vous avertit en temps utile si vous tentez de poursuivre la configuration rapide. Si votre domaine n’est pas routable, il est probable que l’UPN des utilisateurs ait également un suffixe non routable. Par exemple, si votre domaine est contoso.local, Azure AD Connect vous propose d’utiliser des paramètres personnalisés plutôt que la configuration rapide. Avec les paramètres personnalisés, vous serez en mesure de spécifier l’attribut à utiliser comme UPN pour la connexion à Azure une fois les utilisateurs synchronisés avec Azure AD. Pour plus d’informations, consultez **Sélection d’un attribut pour le nom d’utilisateur principal dans Azure AD**.

## Étapes suivantes
En savoir plus sur l’[intégration de vos identités locales à Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0629_2016-->