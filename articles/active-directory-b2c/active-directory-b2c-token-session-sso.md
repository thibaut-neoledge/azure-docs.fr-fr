<properties
	pageTitle="Azure Active Directory B2C en version préliminaire : configuration du jeton, de la session et de l’authentification unique | Microsoft Azure"
	description="Configuration du jeton, de la session et de l’authentification unique dans Azure Active Directory B2C"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C en version préliminaire : configuration du jeton, de la session et de l’authentification unique

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

Cette fonctionnalité vous donne un contrôle précis, sur une [base par stratégie](active-directory-b2c-reference-policies.md), de ce qui suit :
 
1. Durées de vie des jetons de sécurité émis par Azure Active Directory (Azure AD) B2C.
2. Durées de vie des sessions d’applications web gérées par Azure AD B2C.
3. Comportement de l’authentification unique (SSO) entre plusieurs applications et stratégies dans votre client B2C.

Vous pouvez utiliser cette fonctionnalité dans votre client B2C comme suit :

1. Suivez ces étapes pour [accéder au panneau de fonctionnalités B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) sur le portail Azure.
2. Cliquez sur **Stratégies d’authentification**. *Remarque : vous pouvez utiliser cette fonctionnalité avec n’importe quel type de stratégie, pas seulement les **stratégies d’authentification***.
3. Ouvrez une stratégie en cliquant dessus. Par exemple, cliquez sur **B2C\_1\_SiIn**.
4. Cliquez sur **Modifier** dans la partie supérieure du panneau.
5. Cliquez sur **Configuration du jeton, de la session et de l’authentification unique**.
6. Apportez les modifications voulues. Apprenez-en plus sur les propriétés disponibles dans les sections suivantes.
7. Cliquez sur **OK**.
8. Cliquez sur **Enregistrer** dans la partie supérieure du panneau.

![Capture d’écran de la configuration du jeton, de la session et de l’authentification unique](./media/active-directory-b2c-token-session-sso/token-session-sso.png)

## Configuration de la durée de vie des jetons

Azure AD B2C prend en charge le [protocole d’autorisation OAuth 2.0](active-directory-b2c-reference-protocols.md) pour activer l’accès sécurisé aux ressources protégées. Pour implémenter cette prise en charge, Azure AD B2C émet divers [jetons de sécurité](active-directory-b2c-reference-tokens.md). Ce sont les propriétés que vous pouvez utiliser pour gérer les durées de vie des jetons de sécurité émis par Azure AD B2C :

- **Durées de vie du jeton d’accès et d’ID (minutes)** : la durée de vie du jeton de porteur OAuth 2.0 utilisé pour accéder à une ressource protégée. À ce stade, Azure AD B2C émet uniquement des jetons d’ID. Cette valeur peut s’appliquer aux jetons d’accès également, une fois leur prise en charge ajoutée.
   - Par défaut : 60 minutes.
   - Valeur minimale (inclusive) : 15 minutes.
   - Valeur maximale (inclusive) : 1 440 minutes.
- **Durée de vie du jeton d’actualisation (jours)** : la durée maximale avant laquelle un jeton d’actualisation peut être utilisé pour acquérir un nouveau jeton d’accès ou d’ID (et éventuellement, un nouveau jeton d’actualisation, si l’étendue `offline_access` a été accordée à votre application).
   - Par défaut : 14 jours.
   - Valeur minimale (inclusive) : 1 jour.
   - Valeur maximale (inclusive) : 90 jours.
- **Durée de vie fenêtre glissante du jeton d’actualisation (jours)** : une fois cette période écoulée, l’utilisateur est obligé de s’authentifier de nouveau, quelle que soit la période de validité de dernier jeton d’actualisation obtenu par l’application. Cette valeur ne peut être fournie que si le commutateur est défini sur **Délimité**. Elle doit être supérieure ou égale à la valeur **Durée de vie du jeton d’actualisation (jours)**. Si le commutateur est défini sur **Non délimité**, vous ne pouvez pas fournir une valeur spécifique.
   - Par défaut : 90 jours.
   - Valeur minimale (inclusive) : 1 jour.
   - Valeur maximale (inclusive) : 365 jours.

Voici quelques cas d’usage que vous pouvez activer à l’aide de ces propriétés :

- Autoriser un utilisateur à rester connecté à une application mobile indéfiniment, tant qu’il est actif en permanence sur l’application. Vous pouvez configurer cela en définissant le commutateur **Durée de vie fenêtre glissante du jeton d’actualisation (jours)** sur **Non délimité** dans votre stratégie d’authentification.
- Respectez les exigences de conformité et de sécurité de votre secteur en définissant les durées de vie correctes du jeton d’accès.

## Configuration de session

Azure AD B2C prend en charge le [protocole d’authentification OpenID Connect](active-directory-b2c-reference-oidc.md) pour activer l’authentification sécurisée dans les applications web. Voici les propriétés que vous pouvez utiliser pour gérer les sessions d’application web :

- **Durée de vie de session d’application web (minutes)** : la durée de vie du cookie de session Azure AD B2C stocké dans le navigateur de l’utilisateur après une authentification réussie.
   - Par défaut : 1 440 minutes.
   - Valeur minimale (inclusive) : 15 minutes.
   - Valeur maximale (inclusive) : 1 440 minutes.
- **Expiration de la session d’application web** : si ce commutateur est défini sur **Absolu**, l’utilisateur est obligé de s’authentifier de nouveau lorsque la période spécifiée par **Durée de vie de la session d’application web (minutes)** est écoulée. Si ce commutateur est défini sur **Cumulé** (paramètre par défaut), l’utilisateur reste connecté tant qu’il est actif en permanence dans votre application web.

Voici quelques cas d’usage que vous pouvez activer à l’aide de ces propriétés :

- Respectez les exigences de conformité et de sécurité de votre secteur en définissant les durées de vie correctes de la session d’application web.
- Forcez la réauthentification après une période donnée pendant une interaction utilisateur avec une partie haute sécurité de votre application web. 

## Configuration de l’authentification unique

Si vous avez plusieurs applications et stratégies dans votre client B2C, vous pouvez gérer les interactions utilisateur à l’aide de la propriété **Configuration de l’authentification unique**. Vous pouvez définir la propriété sur l’un des paramètres suivants :

- **Client** : il s’agit du paramètre par défaut. L’utilisation de ce paramètre permet à plusieurs applications et stratégies dans votre client B2C de partager la même session utilisateur. Par exemple, lorsqu’un utilisateur se connecte à une application, Contoso Shopping, il peut également se connecter de façon transparente à une autre application, Contoso Pharmacy, lorsqu’il y accède.
- **Application** : ceci vous permet de maintenir une session utilisateur exclusivement pour une application, indépendamment des autres applications. Par exemple, si vous souhaitez que l’utilisateur se connecte à Contoso Pharmacy (avec les mêmes informations d’identification), même s’il est déjà connecté à Contoso Shopping, une autre application sur le même client B2C. 
- **Stratégie** : ceci vous permet de maintenir une session utilisateur exclusivement pour une stratégie, indépendamment des applications qui l’utilisent. Par exemple, si l’utilisateur s’est déjà connecté et a effectué une étape d’authentification multifacteur, il peut obtenir l’accès à des parties plus sécurisées de plusieurs applications tant que la session liée à la stratégie n’expire pas.
- **Désactivé** : ceci oblige l’utilisateur à réexécuter toute la procédure pour chaque exécution de la stratégie. Par exemple, cela permet à plusieurs utilisateurs de s’inscrire à votre application (dans un scénario de bureau partagé), même lorsqu’un seul utilisateur reste connecté pendant toute la durée.

<!---HONumber=AcomDC_0518_2016-->