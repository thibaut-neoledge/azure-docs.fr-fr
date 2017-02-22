---
title: "Durées de vie des jetons configurables dans Azure Active Directory | Microsoft Docs"
description: "Les administrateurs et les développeurs utilisent cette fonctionnalité pour spécifier les durées de vie des jetons émis par Azure AD."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 06f5b317-053e-44c3-aaaa-cf07d8692735
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2016
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: b520b4672dd403981d218c9855c3beb09ef55021
ms.openlocfilehash: 6da28e6273d92445e4b14ea22752a6e59b1dd93a


---
# <a name="configurable-token-lifetimes-in-azure-active-directory-public-preview"></a>Durées de vie des jetons configurables dans Azure Active Directory (version préliminaire publique)
> [!NOTE]
> Pour le moment, cette fonctionnalité est disponible dans la version préliminaire publique.  Vous devez être préparé à rétablir ou à supprimer les modifications.  Nous étendons cette fonctionnalité pour que tout le monde puisse l’essayer dans la version préliminaire publique, mais certains aspects peuvent nécessiter un [abonnement Azure AD Premium](active-directory-get-started-premium.md) une fois mis à la disposition générale.
> 
> 

## <a name="introduction"></a>Introduction
Les administrateurs et les développeurs utilisent cette fonctionnalité pour spécifier les durées de vie des jetons émis par Azure AD. Les durées de vie des jetons peuvent être configurées pour toutes les applications d’une organisation, pour une application mutualisée (plusieurs organisations) ou pour un principal du service spécifique d’une organisation.

Dans Azure AD, un objet de stratégie représente un ensemble de règles appliquées sur des applications individuelles ou sur toutes les applications d’une organisation.  Chaque type de stratégie comporte une structure unique avec un ensemble de propriétés qui sont ensuite appliquées aux objets auxquels elles sont affectées.

Une stratégie peut être désignée par défaut pour une organisation. Cette stratégie est alors appliquée sur les applications qui résident dans cette organisation tant qu’elle n’est pas remplacée par une stratégie pourvue d’une priorité plus élevée. Les stratégies peuvent être également affectées à des applications spécifiques. L’ordre de priorité varie par type de stratégie.

Les stratégies de durée de vie des jetons peuvent être configurées pour les jetons d’actualisation, les jetons d’accès, les jetons de session et les jetons d’ID.

### <a name="access-tokens"></a>Jetons d’accès
Un jeton d’accès est utilisé par un client pour accéder à une ressource protégée. Il ne peut être utilisé que pour une combinaison spécifique d’utilisateur, de client et de ressource. Les jetons d’accès ne peuvent pas être révoqués et sont valides jusqu’à leur expiration. Un acteur malveillant qui a obtenu un jeton d’accès peut l’utiliser pour prolonger sa durée de vie.  L’ajustement de la durée de vie des jetons d’accès représente un compromis entre l’amélioration des performances du système et l’augmentation de la durée pendant laquelle le client conserve un accès une fois son compte désactivé.  Les performances du système sont améliorées en réduisant le nombre de fois où un client doit acquérir un nouveau jeton d’accès. 

### <a name="refresh-tokens"></a>Jetons d’actualisation
Lorsqu’un client acquiert un jeton d’accès pour accéder à une ressource protégée, il reçoit un jeton d’actualisation et un jeton d’accès. Le jeton d’actualisation permet d’obtenir de nouvelles paires de jetons d’accès/actualisation à l’expiration du jeton d’accès actuel. Les jetons d’actualisation sont liés à des combinaisons d’utilisateur et de client. Ils peuvent être révoqués, et leur validité est vérifiée à chaque fois qu’ils sont utilisés.

Il est important de distinguer les clients confidentiels des clients publics. Pour plus d’informations sur les différents types de client, consultez [RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1).

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>Durées de vie des jetons avec des jetons d’actualisation de client confidentiel
Les clients confidentiels sont des applications qui peuvent stocker un mot de passe client en toute sécurité (secret), ce qui leur permet de prouver que les demandes proviennent de l’application cliente et non d’un acteur malveillant. Par exemple, une application web est un client confidentiel, car elle peut stocker un secret de client sur le serveur web et n’est donc pas exposée. Comme ces flux sont plus sécurisés, les durées de vie par défaut des jetons d’actualisation émis pour ces flux sont supérieures et ne peuvent pas être modifiées à l’aide de la stratégie.

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>Durées de vie des jetons avec des jetons d’actualisation de client public 

Les clients publics ne peuvent pas stocker en toute sécurité un mot de passe de client (secret). Par exemple, une application iOS/Android ne peut pas masquer un secret au propriétaire de la ressource et est donc considérée comme un client public.  Des stratégies peuvent être définies sur des ressources pour empêcher les jetons d’actualisation des clients publics antérieurs à une période spécifiée d’obtenir une nouvelle paire de jetons d’accès/actualisation (délai d’inactivité max de jeton d’actualisation).  En outre, les stratégies peuvent être utilisées pour définir une période au-delà de laquelle les jetons d’actualisation ne sont plus acceptés (âge maximal de jeton d’actualisation).  L’ajustement de la durée de vie des jetons d’actualisation vous permet de contrôler le moment et la fréquence auxquels l’utilisateur doit entrer de nouveau les informations d’identification au lieu d’être authentifié de nouveau en mode silencieux lorsqu’il utilise une application cliente publique.

### <a name="id-tokens"></a>Jetons d’ID
Les jetons d’ID sont transmis aux sites web et aux clients natifs, et contiennent les informations de profil d’un utilisateur. Un jeton d’ID est lié à une combinaison spécifique d’utilisateur et de client. Les jetons d’ID sont considérés comme valides jusqu’à leur expiration.  En règle générale, une application web fait correspondre la durée de vie de session d’un utilisateur de l’application à la durée de vie du jeton d’ID émis pour l’utilisateur.  L’ajustement de la durée de vie du jeton d’ID vous permet de contrôler la fréquence à laquelle l’application web arrête la session de l’application et demande à l’utilisateur de s’authentifier à nouveau auprès d’Azure AD (en mode silencieux ou interactif).

### <a name="single-sign-on-session-token"></a>Jeton de session d’authentification unique
Lorsqu’un utilisateur s’authentifie auprès d’Azure AD et coche la case Maintenir la connexion, une session d’authentification unique est établie avec le navigateur de l’utilisateur et Azure AD.  Le jeton de session d’authentification unique représente cette session sous la forme d’un cookie. Il est important de noter que le jeton de session d’authentification unique n’est pas lié à une application cliente/ressource spécifique. Les jetons de session d’authentification unique peuvent être révoqués, et leur validité est vérifiée à chaque fois qu’ils sont utilisés.

Il existe deux types de jeton de session d’authentification unique. Les jetons de session persistants sont stockés en tant que cookies persistants par le navigateur tandis que les jetons de session non persistants sont stockés en tant que cookies de session (ils sont détruits à la fermeture du navigateur).

Les jetons de session non persistants ont une durée de vie de 24 heures alors qu’elle est de 180 jours pour les jetons persistants. À chaque fois que le jeton de session d’authentification unique est utilisé au cours de sa période de validité, celle-ci est prolongée à nouveau de 24 heures ou de 180 jours. Si le jeton de session d’authentification unique n’est pas utilisé au cours de sa période de validité, il est considéré comme arrivé à expiration et n’est plus accepté. 

Des stratégies peuvent être utilisées pour définir une période après l’émission du premier jeton de session au-delà de laquelle les jetons de session ne sont plus acceptés (âge maximal de jeton de session).  L’ajustement de la durée de vie des jetons de session vous permet de contrôler le moment et la fréquence auxquels l’utilisateur doit entrer de nouveau les informations d’identification au lieu d’être authentifié en mode silencieux lorsqu’il utilise une application web.

### <a name="token-lifetime-policy-properties"></a>Propriétés des stratégies de durée de vie des jetons
Une stratégie de durée de vie des jetons est un type d’objet de stratégie qui contient des règles de durée de vie des jetons.  Les propriétés de la stratégie permettent de contrôler les durées de vie des jetons spécifiés.  Si aucune stratégie n’est définie, le système applique la valeur de durée de vie par défaut.

### <a name="configurable-token-lifetime-properties"></a>Propriétés des durées de vie des jetons configurables
| Propriété | Chaîne de propriété de stratégie | Éléments affectés | Default | Minimale | Maximale |
| --- | --- | --- | --- | --- | --- |
| Durée de vie de jeton d’accès |AccessTokenLifetime |Jetons d’accès, jetons d’ID, jetons SAML2 |1 heure |10 minutes |1 jour |
| Délai d’inactivité maximale de jeton d’actualisation |MaxInactiveTime |Jetons d’actualisation |14 jours |10 minutes |90 jours |
| Âge maximal de jeton d’actualisation à facteur unique |MaxAgeSingleFactor |Jetons d’actualisation (pour tous les utilisateurs) |90 jours |10 minutes |Jusqu’à révocation* |
| Âge maximal de jeton d’actualisation multifacteur |MaxAgeMultiFactor |Jetons d’actualisation (pour tous les utilisateurs) |90 jours |10 minutes |Jusqu’à révocation* |
| Âge maximal de jeton de session à facteur unique |MaxAgeSessionSingleFactor** |Jetons de session (persistants et non persistants) |Jusqu’à révocation |10 minutes |Jusqu’à révocation* |
| Âge maximal de jeton de session multifacteur |MaxAgeSessionMultiFactor*** |Jetons de session (persistants et non persistants) |Jusqu’à révocation |10 minutes |Jusqu’à révocation* |

* *Une durée explicite maximale de&365; jours peut être définie pour ces attributs.
* **Si la chaîne MaxAgeSessionSingleFactor n’est pas définie, cette valeur prend la valeur MaxAgeSingleFactor. Si aucun de ces paramètres n’est défini, la propriété prend la valeur par défaut (Jusqu’à révocation).
* ***Si la chaîne MaxAgeSessionMultiFactor n’est pas définie, cette valeur prend la valeur MaxAgeMultiFactor. Si aucun de ces paramètres n’est défini, la propriété prend la valeur par défaut (Jusqu’à révocation).

### <a name="exceptions"></a>Exceptions
| Propriété | Éléments affectés | Default |
| --- | --- | --- |
| Délai d’inactivité maximale de jeton d’actualisation (utilisateurs fédérés disposant d’informations de révocation insuffisantes) |Jetons d’actualisation (émis pour les utilisateurs fédérés disposant d’informations de révocation insuffisantes) |12 heures |
| Délai d’inactivité maximale de jeton d’actualisation (clients confidentiels) |Jetons d’actualisation (émis pour les clients confidentiels) |90 jours |
| Âge maximal de jeton d’actualisation (émis pour les clients confidentiels) |Jetons d’actualisation (émis pour les clients confidentiels) |Jusqu’à révocation |

### <a name="priority-and-evaluation-of-policies"></a>Priorité et évaluation des stratégies
Les stratégies de durée de vie des jetons peuvent être créées et affectées à des applications, organisations et principaux du service spécifiques. Il est donc possible d’appliquer plusieurs stratégies à une application spécifique. La stratégie de durée de vie du jeton appliquée suit les règles ci-dessous :

* Si une stratégie est explicitement affectée au principal du service, elle est appliquée. 
* Si aucune stratégie n’est explicitement affectée au principal du service, une stratégie explicitement affectée à l’organisation parente du principal du service est appliquée. 
* Si aucune stratégie n’est explicitement affectée au principal du service ou à l’organisation, la stratégie affectée à l’application est appliquée. 
* Si aucune stratégie n’a été affectée au principal du service, à l’organisation ou à l’objet d’application, les valeurs par défaut sont appliquées (voir le tableau ci-dessus).

Pour plus d’informations sur la relation existant entre les objets de principal du service et d’application dans Azure AD, voir [Objets application et principal du service dans Azure Active Directory](active-directory-application-objects.md).

La validité d’un jeton est évaluée lors de son utilisation. C’est la stratégie pourvue de la priorité la plus élevée sur l’application ouverte qui est appliquée.

> [!NOTE]
> Exemple
> 
> Un utilisateur souhaite accéder aux 2 applications web 2, A et B. 
> 
> * Les deux applications résident dans la même organisation parente. 
> * La stratégie 1 de durée de vie des jetons pourvue d’une propriété Âge maximal de jeton de session de 8 heures est définie comme valeur par défaut de l’organisation parente.
> * L’application web A est une application web d’utilisation régulière, qui n’est liée à aucune stratégie. 
> * L’application web B est utilisée pour les processus très sensibles, et son principal du service est lié à la stratégie 2 de durée de vie des jetons pourvue d’une propriété Âge maximal de jeton de session de 30 minutes.
> 
> À 12 h 00, l’utilisateur ouvre une nouvelle session de navigateur et tente d’accéder à l’application web A. Il est redirigé vers Azure AD et doit se connecter. Cette action dépose un cookie avec un jeton de session dans le navigateur. L’utilisateur est redirigé vers l’application A avec un jeton d’ID qui lui permet d’accéder à l’application.
> 
> À 12 h 15, l’utilisateur essaie alors d’accéder à l’application web B. Le navigateur le redirige vers Azure AD qui détecte le cookie de session. Le principal du service de l’application web B est lié à la stratégie 2, mais fait également partie de l’organisation parente avec la stratégie par défaut 1. La stratégie 2 est appliquée puisque les stratégies liées aux principaux du service ont une priorité supérieure à celle des stratégies par défaut de l’organisation. Comme le jeton de session a été initialement émis au cours des 30 dernières minutes, il est considéré comme valide. L’utilisateur est redirigé vers l’application web B avec un jeton d’ID qui lui octroie un accès.
> 
> À 13 h 00 l’utilisateur tente d’accéder à l’application web A. Il est redirigé vers Azure AD. L’application web A n’est liée à aucune stratégie, mais comme elle réside dans une organisation avec la stratégie 1 par défaut, cette stratégie est appliquée. Le cookie de session qui a été initialement émis au cours des 8 dernières heures est détecté, et l’utilisateur est redirigé en mode silencieux vers application web A avec un nouveau jeton d’ID sans devoir s’authentifier.
> 
> L’utilisateur essaie immédiatement d’accéder à l’application web B. Il est redirigé vers Azure AD. Comme auparavant, la stratégie 2 est appliquée. Comme le jeton a été émis il y a plus de 30 minutes, l’utilisateur est invité à entrer de nouveau ses informations d’identification, et une nouvelle session ainsi qu’un nouveau jeton d’ID sont émis. L’utilisateur peut ensuite accéder à l’application web B.
> 
> 

## <a name="configurable-policy-properties-in-depth"></a>Propriétés de stratégie configurables : présentation détaillée
### <a name="access-token-lifetime"></a>Durée de vie de jeton d’accès
**Chaîne :** AccessTokenLifetime

**Éléments affectés :** jetons d’accès, jetons d’ID

**Résumé :** cette stratégie détermine la durée pendant laquelle les jetons d’accès et d’ID sont considérés comme valides. La réduction de la durée de vie des jetons d’accès atténue le risque qu’un jeton d’accès ou d’ID soient utilisés pendant une période prolongée par un acteur malveillant (car ils ne peuvent pas être révoqués), mais nuit également aux performances, car les jetons devront être remplacés plus souvent.

### <a name="refresh-token-max-inactive-time"></a>Délai d’inactivité maximale de jeton d’actualisation
**Chaîne :** MaxInactiveTime

**Éléments affectés :** jetons d’actualisation

**Résumé :** cette stratégie détermine l’âge qu’un jeton d’actualisation peut avoir avant qu’un client ne puisse plus l’utiliser pour récupérer une nouvelle paire de jetons d’actualisation/d’accès lors de la tentative d’accès à cette ressource. Comme un nouveau jeton d’actualisation est généralement retourné lorsqu’un jeton d’actualisation est utilisé, le client ne doit pas avoir accédé à une ressource à l’aide du jeton d’actualisation en cours pour la période spécifiée avant que cette stratégie n’en empêche l’accès. 

Cette stratégie force les utilisateurs qui n’ont pas été actifs sur leur client à s’authentifier de nouveau pour récupérer un nouveau jeton d’actualisation. 

Il est important de noter que la propriété Délai d’inactivité maximale de jeton d’actualisation doit être définie sur une valeur inférieure à celles des propriétés Âge maximal de jeton d’actualisation à facteur unique et Âge maximal de jeton d’actualisation multifacteur.

### <a name="single-factor-refresh-token-max-age"></a>Âge maximal de jeton d’actualisation à facteur unique
**Chaîne :** MaxAgeSingleFactor

**Éléments affectés :** jetons d’actualisation

**Résumé :** cette stratégie détermine la durée pendant laquelle un utilisateur peut continuer à utiliser des jetons d’actualisation pour obtenir de nouvelles paires de jetons d’accès/actualisation après sa dernière authentification réussie avec un seul facteur. Une fois qu’un utilisateur est authentifié et a reçu un nouveau jeton d’actualisation, il est en mesure d’utiliser le flux de jetons d’actualisation (tant que le jeton d’actualisation en cours n’est pas révoqué et qu’il ne reste pas inutilisé pendant une durée supérieure à celle du délai d’inactivité) pour la période spécifiée. À ce stade, les utilisateurs sont contraints de s’authentifier de nouveau pour recevoir un nouveau jeton d’actualisation. 

En réduisant l’âge maximal, vous obligez les utilisateurs à s’authentifier plus souvent. Comme l’authentification à facteur unique est considérée comme moins sécurisée qu’une authentification multifacteur, il est recommandé de définir cette stratégie sur une valeur inférieure ou égale à celle de la stratégie Âge maximal de jeton d’actualisation multifacteur.

### <a name="multi-factor-refresh-token-max-age"></a>Âge maximal de jeton d’actualisation multifacteur
**Chaîne :** MaxAgeMultiFactor

**Éléments affectés :** jetons d’actualisation

**Résumé :** cette stratégie détermine la durée pendant laquelle un utilisateur peut continuer à utiliser des jetons d’actualisation pour obtenir de nouvelles paires de jetons d’accès/actualisation après sa dernière authentification réussie avec plusieurs facteurs. Une fois qu’un utilisateur est authentifié et a reçu un nouveau jeton d’actualisation, il est en mesure d’utiliser le flux de jetons d’actualisation (tant que le jeton d’actualisation en cours n’est pas révoqué et qu’il ne reste pas inutilisé pendant une durée supérieure à celle du délai d’inactivité) pour la période spécifiée. À ce stade, les utilisateurs sont contraints de s’authentifier de nouveau pour recevoir un nouveau jeton d’actualisation. 

En réduisant l’âge maximal, vous obligez les utilisateurs à s’authentifier plus souvent. Étant donné que l’authentification à facteur unique est considérée comme moins sécurisée qu’une authentification multifacteur, il est recommandé de définir cette stratégie sur une valeur supérieure ou égale à celle de la stratégie Âge maximal de jeton d’actualisation à facteur unique.

### <a name="single-factor-session-token-max-age"></a>Âge maximal de jeton de session à facteur unique
**Chaîne :** MaxAgeSessionSingleFactor

**Éléments affectés :** jetons de session (persistants et non persistants)

**Résumé :** cette stratégie détermine la durée pendant laquelle un utilisateur peut continuer à utiliser des jetons de session pour obtenir de nouveaux jetons d’ID et de session après sa dernière authentification réussie avec un seul facteur. Une fois qu’un utilisateur s’est authentifié et a reçu un nouveau jeton de session, il peut utiliser le flux de jetons de session (tant que le jeton de session en cours n’a pas été révoqué ou n’est pas arrivé à expiration) pendant la période spécifiée. À ce stade, les utilisateurs sont contraints de s’authentifier de nouveau pour recevoir un nouveau jeton de session. 

En réduisant l’âge maximal, vous obligez les utilisateurs à s’authentifier plus souvent. Comme l’authentification à facteur unique est considérée comme moins sécurisée qu’une authentification multifacteur, il est recommandé de définir cette stratégie sur une valeur inférieure ou égale à celle de la stratégie Âge maximal de jeton de session multifacteur.

### <a name="multi-factor-session-token-max-age"></a>Âge maximal de jeton de session multifacteur
**Chaîne :** MaxAgeSessionMultiFactor

**Éléments affectés :** jetons de session (persistants et non persistants)

**Résumé :** cette stratégie détermine la durée pendant laquelle un utilisateur peut continuer à utiliser des jetons de session pour obtenir de nouveaux jetons d’ID et de session après sa dernière authentification réussie avec plusieurs facteurs. Une fois qu’un utilisateur s’est authentifié et a reçu un nouveau jeton de session, il peut utiliser le flux de jetons de session (tant que le jeton de session en cours n’a pas été révoqué ou n’est pas arrivé à expiration) pendant la période spécifiée. À ce stade, les utilisateurs sont contraints de s’authentifier de nouveau pour recevoir un nouveau jeton de session. 

En réduisant l’âge maximal, vous obligez les utilisateurs à s’authentifier plus souvent. Étant donné que l’authentification à facteur unique est considérée comme moins sécurisée qu’une authentification multifacteur, il est recommandé de définir cette stratégie sur une valeur supérieure ou égale à celle de la stratégie Âge maximal de jeton de session à facteur unique.

## <a name="sample-token-lifetime-policies"></a>Exemples de stratégie de durée de vie des jetons
La possibilité de créer et de gérer les durées de vie des jetons pour les applications, les principaux du service et votre organisation globale expose toutes sortes de nouveaux scénarios possibles dans Azure AD.  Nous allons vous guider dans quelques scénarios courants de stratégie qui vous aideront à imposer de nouvelles règles pour les éléments suivants :

* Durées de vie de jeton
* Délais d’inactivité maximale des jetons
* Âge maximal des jetons

Nous allons explorer quelques scénarios :

* Gestion de la stratégie par défaut d’une organisation
* Création d’une stratégie de connexion web
* Création d’une stratégie pour les applications natives appelant une API web
* Gestion d’une stratégie avancée 

### <a name="prerequisites"></a>Composants requis
Dans les exemples de scénarios, nous allons créer, mettre à jour, lier et supprimer des stratégies sur les applications, les principaux du service et votre organisation globale.  Si vous débutez avec Azure AD, découvrez [cet article](active-directory-howto-tenant.md) pour vous aider à vous familiariser avant de passer à ces exemples.  

1. Pour commencer, téléchargez la dernière [version d’évaluation des applets de commande Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview). 
2. Une fois que vous disposez des applets de commande Azure AD PowerShell, exécutez la commande Connect pour vous connecter à votre compte d’administrateur Azure AD. Vous devez procéder ainsi à chaque fois que vous démarrez une nouvelle session.
   
     Connect-AzureAD -Confirm
3. Exécutez la commande suivante pour afficher toutes les stratégies qui ont été créées dans votre organisation.  Cette commande doit être utilisée après la plupart des opérations dans les scénarios suivants.  Elle vous aide également à obtenir **l’ID d’objet** de vos stratégies. 
   
     Get-AzureADPolicy

### <a name="sample-managing-a-organizations-default-policy"></a>Exemple : gestion de la stratégie par défaut d’une organisation
Dans cet exemple, nous allons créer une stratégie qui permet aux utilisateurs de se connecter moins fréquemment dans votre organisation entière. 

Pour ce faire, nous allons créer une stratégie de durée de vie des jetons pour les jetons d’actualisation à facteur unique, qui sera appliquée à votre organisation. Cette stratégie est appliquée à toutes les applications de votre organisation et à chaque principal du service pour lequel aucune stratégie n’est déjà définie. 

1. **Créez une stratégie de durée de vie des jetons.** 

Définissez le jeton d’actualisation à facteur unique sur Jusqu’à révocation (il n’arrivera pas à expiration tant que l’accès ne sera pas révoqué).  La définition de stratégie ci-dessous correspond à ce que nous allons créer :

        @("{
          `"TokenLifetimePolicy`":
              {
                 `"Version`":1, 
                 `"MaxAgeSingleFactor`":`"until-revoked`"
              }
        }")

Exécutez ensuite la commande ci-dessous pour créer cette stratégie. 

    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1, `"MaxAgeSingleFactor`":`"until-revoked`"}}") -DisplayName OrganizationDefaultPolicyScenario -IsOrganizationDefault $true -Type TokenLifetimePolicy

Pour afficher votre nouvelle stratégie et obtenir son ID d’objet, exécutez la commande ci-après.

    Get-AzureADPolicy
&nbsp;&nbsp;2.    **Mettez à jour la stratégie.**

Vous pensez que la première stratégie n’est pas aussi stricte qu’il le faut pour votre service, et souhaitez que vos jetons d’actualisation à facteur unique arrivent à expiration dans 2 jours. Exécutez la commande ci-dessous. 

    Set-AzureADPolicy -ObjectId <ObjectID FROM GET COMMAND> -DisplayName OrganizationDefaultPolicyUpdatedScenario -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxAgeSingleFactor`":`"2.00:00:00`"}}")

&nbsp;&nbsp;3. **Vous avez terminé !** 

### <a name="sample-creating-a-policy-for-web-sign-in"></a>Exemple : création d’une stratégie de connexion web
Dans cet exemple, nous allons créer une stratégie qui nécessitera que vos utilisateurs s’authentifient plus fréquemment dans votre application web. Cette stratégie définit la durée de vie des jetons d’accès/ID et l’âge maximal d’un jeton de session multifacteur pour le principal du service de votre application web.

1. **Créez une stratégie de durée de vie des jetons.**

Cette stratégie de connexion web définit la durée de vie des jetons d’accès/ID et l’âge maximal de jeton de session à facteur unique sur 2 heures.

    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"AccessTokenLifetime`":`"02:00:00`",`"MaxAgeSessionSingleFactor`":`"02:00:00`"}}") -DisplayName WebPolicyScenario -IsOrganizationDefault $false -Type TokenLifetimePolicy

Pour afficher votre nouvelle stratégie et obtenir son ID d’objet, exécutez la commande ci-après.

    Get-AzureADPolicy
&nbsp;&nbsp;2.    **Affectez la stratégie au principal du service.**

Nous allons lier cette nouvelle stratégie à un principal du service.  Vous devrez également accéder à **l’ID d’objet** du principal du service. Vous pouvez interroger [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) ou accéder à notre [outil Afficheur Graph](https://graphexplorer.cloudapp.net/) et vous connecter à votre compte Azure AD pour voir tous les principaux du service de votre organisation. 

Une fois que vous disposez de **l’ID d’objet**, exécutez la commande ci-dessous.

    Add-AzureADServicePrincipalPolicy -ObjectId <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
&nbsp;&nbsp;3.    **Vous avez terminé.** 

 

### <a name="sample-creating-a-policy-for-native-apps-calling-a-web-api"></a>Exemple : création d’une stratégie pour les applications natives appelant une API web
Dans cet exemple, nous allons créer une stratégie qui nécessitera que les utilisateurs s’authentifient moins souvent et prolonger la période pendant laquelle ils peuvent être inactifs sans devoir s’authentifier de nouveau. La stratégie sera appliquée à l’API web. De cette façon, lorsque l’application native la demandera en tant que ressource, cette stratégie sera appliquée.

1. **Créez une stratégie de durée de vie des jetons.** 

Cette commande crée une stratégie stricte pour une API web. 

    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxInactiveTime`":`"30.00:00:00`",`"MaxAgeMultiFactor`":`"until-revoked`",`"MaxAgeSingleFactor`":`"180.00:00:00`"}}") -DisplayName WebApiDefaultPolicyScenario -IsOrganizationDefault $false -Type TokenLifetimePolicy

Pour afficher votre nouvelle stratégie et obtenir son ID d’objet, exécutez la commande ci-après.

    Get-AzureADPolicy

&nbsp;&nbsp;2.    **Affectez la stratégie à votre API web.**

Nous allons lier cette nouvelle stratégie à une application.  Vous devrez également accéder à **l’ID d’objet** de votre application. La meilleure façon de trouver **l’ID d’objet** de votre application consiste à utiliser le [portail Azure](https://portal.azure.com/). 

Une fois que vous disposez de **l’ID d’objet**, exécutez la commande ci-dessous.

    Add-AzureADApplicationPolicy -ObjectId <ObjectID of the App> -RefObjectId <ObjectId of the Policy>

&nbsp;&nbsp;3.    **Vous avez terminé.** 

### <a name="sample-managing-an-advanced-policy"></a>Exemple : gestion d’une stratégie avancée
Dans cet exemple, nous allons créer quelques stratégies pour montrer le fonctionnement du système de priorité et la façon dont vous pouvez gérer plusieurs stratégies appliquées à plusieurs objets. Cela vous donne une idée de la priorité des stratégies expliquée ci-dessus et vous permet de gérer des scénarios plus complexes. 

1. **Créez une stratégie de durée de vie des jetons.**

Pour l’instant, c’est plutôt simple. Nous avons créé une stratégie par défaut d’organisation qui définit la durée de vie des jetons d’actualisation à facteur unique sur 30 jours. 

    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxAgeSingleFactor`":`"30.00:00:00`"}}") -DisplayName ComplexPolicyScenario -IsOrganizationDefault $true -Type TokenLifetimePolicy
Pour afficher votre nouvelle stratégie et obtenir son ID d’objet, exécutez la commande ci-après.

    Get-AzureADPolicy

&nbsp;&nbsp;2.    **Affectez la stratégie à un principal du service.**

À présent, nous disposons d’une stratégie sur toute l’organisation.  Supposons que nous souhaitions conserver cette stratégie de 30 jours pour un principal du service spécifique, mais que nous changions la stratégie par défaut d’organisation pour qu’elle soit la limite supérieure du paramètre « Jusqu’à révocation ». 

Nous allons commencer par lier cette nouvelle stratégie à notre principal du service.  Vous devrez également accéder à **l’ID d’objet** du principal du service. Vous pouvez interroger [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) ou accéder à notre [outil Afficheur Graph](https://graphexplorer.cloudapp.net/) et vous connecter à votre compte Azure AD pour voir tous les principaux du service de votre organisation. 

Une fois que vous disposez de **l’ID d’objet**, exécutez la commande ci-dessous.

    Add-AzureADServicePrincipalPolicy -ObjectId <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>

&nbsp;&nbsp;3.    **Définissez l’indicateur IsOrganizationDefault sur false à l’aide de la commande ci-dessous**. 

    Set-AzureADPolicy -ObjectId <ObjectId of Policy> -DisplayName ComplexPolicyScenario -IsOrganizationDefault $false
&nbsp;&nbsp;4.    **Créer une stratégie par défaut d’organisation**

    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxAgeSingleFactor`":`"until-revoked`"}}") -DisplayName ComplexPolicyScenarioTwo -IsOrganizationDefault $true -Type TokenLifetimePolicy

&nbsp;&nbsp;5.     **Vous avez terminé.** 

À présent, la stratégie d’origine est liée à votre principal du service, et la nouvelle stratégie est définie comme stratégie par défaut de votre organisation.  Il est important de se rappeler que les stratégies appliquées aux principaux de service ont priorité sur les stratégies par défaut d’organisation. 

## <a name="cmdlet-reference"></a>Informations de référence sur les applets de commande
### <a name="manage-policies"></a>Gérer les stratégies
Les applets de commande suivantes permettent de gérer les stratégies.</br></br>

#### <a name="new-azureadpolicy"></a>New-AzureADPolicy
Permet de créer une stratégie.

    New-AzureADPolicy -Definition <Array of Rules> -DisplayName <Name of Policy> -IsOrganizationDefault <boolean> -Type <Policy Type> 

| Paramètres | Description | Exemple |
| --- | --- | --- |
| -Definition |Tableau de champs de chaîne JSON qui contient toutes les règles de la stratégie. |-Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxInactiveTime`":`"20:00:00`"}}") |
| -DisplayName |Chaîne du nom de la stratégie |-DisplayName MyTokenPolicy |
| -IsOrganizationDefault |Si la valeur true définit la stratégie comme stratégie par défaut de l’organisation la valeur false n’a aucun effet |-IsOrganizationDefault $true |
| -Type |Type de stratégie. Pour les durées de vie des jetons, utilisez toujours « TokenLifetimePolicy ». |-Type TokenLifetimePolicy |
| -AlternativeIdentifier [Optional] |Définit un autre ID pour la stratégie. |-AlternativeIdentifier myAltId |

</br></br>

#### <a name="get-azureadpolicy"></a>Get-AzureADPolicy
Permet d’obtenir toutes les stratégies d’Azure AD ou une stratégie spécifiée. 

    Get-AzureADPolicy 

| Paramètres | Description | Exemple |
| --- | --- | --- |
| -ObjectId [Optional] |ID d’objet de la stratégie que vous souhaitez obtenir. |-ObjectId &lt;ObjectID of Policy&gt; |

</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Get-AzureADPolicyAppliedObject
Permet d’obtenir toutes les applications et tous les principaux de service liés à une stratégie.

    Get-AzureADPolicyAppliedObject -ObjectId <object id of policy> 

| Paramètres | Description | Exemple |
| --- | --- | --- |
| -ObjectId |ID d’objet de la stratégie que vous souhaitez obtenir. |-ObjectId &lt;ObjectID of Policy&gt; |

</br></br>

#### <a name="set-azureadpolicy"></a>Set-AzureADPolicy
Met à jour une stratégie existante.

    Set-AzureADPolicy -ObjectId <object id of policy> -DisplayName <string> 

| Paramètres | Description | Exemple |
| --- | --- | --- |
| -ObjectId |ID d’objet de la stratégie que vous souhaitez obtenir. |-ObjectId &lt;ObjectID of Policy&gt; |
| -DisplayName |Chaîne du nom de la stratégie |-DisplayName MyTokenPolicy |
| -Definition [Optional] |Tableau de champs de chaîne JSON qui contient toutes les règles de la stratégie. |-Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxInactiveTime`":`"20:00:00`"}}") |
| -IsOrganizationDefault [en option] |Si la valeur true définit la stratégie comme stratégie par défaut de l’organisation la valeur false n’a aucun effet |-IsOrganizationDefault $true |
| -Type [Optional] |Type de stratégie. Pour les durées de vie des jetons, utilisez toujours « TokenLifetimePolicy ». |-Type TokenLifetimePolicy |
| -AlternativeIdentifier [Optional] |Définit un autre ID pour la stratégie. |-AlternativeIdentifier myAltId |

</br></br>

#### <a name="remove-azureadpolicy"></a>Remove-AzureADPolicy
Supprime la stratégie spécifiée.

     Remove-AzureADPolicy -ObjectId <object id of policy>

| Paramètres | Description | Exemple |
| --- | --- | --- |
| -ObjectId |ID d’objet de la stratégie que vous souhaitez obtenir. |-ObjectId &lt;ObjectID of Policy&gt; |

</br></br>

### <a name="application-policies"></a>Stratégies d’application
Les applets de commande suivantes peuvent être utilisées pour les stratégies d’application.</br></br>

#### <a name="add-azureadapplicationpolicy"></a>Add-AzureADApplicationPolicy
Lie la stratégie spécifiée à une application.

    Add-AzureADApplicationPolicy -ObjectId <object id of application> -RefObjectId <object id of policy>

| Paramètres | Description | Exemple |
| --- | --- | --- |
| -ObjectId |ID d’objet de l’application. |-ObjectId &lt;ObjectID of Application&gt; |
| -RefObjectId |ID d’objet de la stratégie. |-RefObjectId &lt;ObjectID of Policy&gt; |

</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Get-AzureADApplicationPolicy
Permet d’obtenir la stratégie affectée à une application.

    Get-AzureADApplicationPolicy -ObjectId <object id of application>

| Paramètres | Description | Exemple |
| --- | --- | --- |
| -ObjectId |ID d’objet de l’application. |-ObjectId &lt;ObjectID of Application&gt; |

</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>Remove-AzureADApplicationPolicy
Supprime une stratégie d’une application.

    Remove-AzureADApplicationPolicy -ObjectId <object id of application> -PolicyId <object id of policy>

| Paramètres | Description | Exemple |
| --- | --- | --- |
| -ObjectId |ID d’objet de l’application. |-ObjectId &lt;ObjectID of Application&gt; |
| -PolicyId |ID d’objet de stratégie. |-PolicyId &lt;ObjectID of Policy&gt; |

</br></br>

### <a name="service-principal-policies"></a>Stratégies de principal du service
Les applets de commande suivantes peuvent être utilisées pour les stratégies de principal du service.</br></br>

#### <a name="add-azureadserviceprincipalpolicy"></a>Add-AzureADServicePrincipalPolicy
Lie la stratégie spécifiée à un principal du service.

    Add-AzureADServicePrincipalPolicy -ObjectId <object id of service principal> -RefObjectId <object id of policy>

| Paramètres | Description | Exemple |
| --- | --- | --- |
| -ObjectId |ID d’objet de l’application. |-ObjectId &lt;ObjectID of Application&gt; |
| -RefObjectId |ID d’objet de la stratégie. |-RefObjectId &lt;ObjectID of Policy&gt; |

</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Get-AzureADServicePrincipalPolicy
Permet d’obtenir une stratégie liée au principal du service spécifié.

    Get-AzureADServicePrincipalPolicy -ObjectId <object id of service principal>

| Paramètres | Description | Exemple |
| --- | --- | --- |
| -ObjectId |ID d’objet de l’application. |-ObjectId &lt;ObjectID of Application&gt; |

</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Remove-AzureADServicePrincipalPolicy
Supprime la stratégie du principal du service spécifié.

    Remove-AzureADServicePrincipalPolicy -ObjectId <object id of service principal>  -PolicyId <object id of policy>

| Paramètres | Description | Exemple |
| --- | --- | --- |
| -ObjectId |ID d’objet de l’application. |-ObjectId &lt;ObjectID of Application&gt; |
| -PolicyId |ID d’objet de stratégie. |-PolicyId &lt;ObjectID of Policy&gt; |




<!--HONumber=Jan17_HO3-->


