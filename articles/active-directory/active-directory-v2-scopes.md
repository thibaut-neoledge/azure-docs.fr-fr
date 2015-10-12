<properties
	pageTitle="Modèle d'application v2.0 - Étendues, autorisations et consentement | Microsoft Azure"
	description="Une description de l’autorisation dans le modèle d’application v2.0 Azure AD, relative notamment aux étendues, aux autorisations et aux consentements."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/12/2015"
	ms.author="dastrock"/>

# Version d’évaluation du modèle d’application v2.0 : Étendues, autorisations et consentements

Les applications qui s’intègrent à Azure AD respectent un modèle particulier d’autorisation, qui permet aux utilisateurs de contrôler le mode d’accès d’une application à leurs données. Dans le modèle d’application v2.0, l’implémentation de ce modèle d’autorisation a été mise à jour ; le mode d’interaction des applications avec Azure AD est aujourd’hui différent. Cette rubrique aborde les concepts de base de ce modèle d’autorisation, notamment les étendues, les autorisations et les consentements.

> [AZURE.NOTE]Ces informations s’appliquent à la version préliminaire publique du modèle d’application v2.0. Pour obtenir des instructions sur l’intégration au service Azure AD mis à la disposition générale, consultez le [Guide du développeur Azure AD](active-directory-developers-guide.md).

## Étendues et autorisations

Le modèle d’application v2.0 implémente le protocole d’autorisation [OAuth 2.0](active-directory-v2-protocols.md), qui est une méthode permettant d’autoriser à une application tierce d’accéder aux ressources hébergées sur le Web pour le compte d’un utilisateur. L’ensemble des ressources hébergées sur le Web qui s’intègrent à Azure AD présentent un identificateur de ressource, également appelé **URI d’ID d’application**. Voici, par exemple, quelques-unes des ressources hébergées sur le Web de Microsoft :

- L’API de messagerie unifiée Office 365 : `https://outlook.office.com`
- L’API Azure Resource Manager : `https://management.azure.com`
- L’API Graph Azure AD : `https://graph.windows.net`

Ceci s’applique également aux ressources tierces intégrées à Azure AD. Ces ressources peuvent également définir un ensemble d’autorisations à utiliser pour diviser la fonctionnalité de cette ressource en fragments plus réduits. Par exemple, l’API de messagerie unifiée Office 365 a défini les autorisations basiques suivantes :

- Lecture de la boîte aux lettres d’un utilisateur
- Écriture vers la boîte aux lettres d’un utilisateur
- Envoi de messages en tant qu’utilisateur

En définissant ces autorisations, la ressource dispose d’un contrôle affiné sur ses données et sur leur exposition. Une application tierce peut ensuite demander ces autorisations à l’utilisateur ; ce dernier doit fournir son consentement avant qu’une application puisse agir en son nom. En fragmentant les fonctionnalités de ressources en ensembles plus réduits d’autorisation, il est possible de configurer les applications tierces pour qu’elles demandent uniquement les autorisations nécessaires à l’accomplissement de leur tâche. Par ailleurs, les utilisateurs connaissent avec précision la manière dont les applications vont utiliser leurs données, et s’inquiètent moins de leur éventuelle intention malveillante.

Dans Azure AD et OAuth, ces autorisations sont appelées **étendues**. Vous pouvez également les retrouver sous l’appellation **autorisations oAuth2**. Une étendue est représentée dans Azure AD en tant que valeur de chaîne. Toujours dans l’exemple d’API de messagerie unifiée Office 365, la valeur d’étendue pour chaque autorisation est la suivante :

- Lecture d’une boîte aux lettres d’utilisateurs : `Mail.Read`
- Écriture vers la boîte aux lettres d’un utilisateur : `Mail.ReadWrite`
- Envoi de messages en tant qu’utilisateur : `Mail.Send`

Une application peut demander ces autorisations en spécifiant les étendues dans les requêtes dirigées vers le point de terminaison v2.0, comme décrit ci-dessous.

## Consentement

Dans une demande d’autorisation [OpenID Connect ou OAuth 2.0](active-directory-v2-protocols.md), une application peut demander les autorisations requises à l’aide du paramètre de requête `scope`. Par exemple, lorsqu’un utilisateur se connecte à une application, cette dernière envoie une requête de ce type (avec des sauts de ligne pour une meilleure lisibilité) :

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Foutlook.office.com%2Fmail.read%20
https%3A%2F%2Foutlook.office.com%2Fmail.send
&state=12345
```

Le paramètre `scope` est une liste d’étendues séparées par des espaces, demandées par l’application. Chaque étendue est indiquée par l’ajout de la valeur correspondante à l’identificateur de la ressource (URI d’ID d’application). La requête ci-dessus indique que l’application nécessite l’autorisation de lecture de la boîte aux lettres de l’utilisateur et d’envoi de messages au nom de l’utilisateur.

Une fois que l’utilisateur a saisi des informations d’identification, le point de terminaison v2.0 cherche un enregistrement correspondant de **consentement d’utilisateur**. Si l’utilisateur n’a accepté aucune des autorisations sollicitées par le passé, le point de terminaison v2.0 demande à l’utilisateur d’octroyer les autorisations demandées.

![Capture d’écran du consentement dans le compte de travail](../media/active-directory-v2-flows/work_account_consent.png)

Lorsque l’utilisateur approuve l’autorisation, le consentement est enregistré, de manière à ce que l’utilisateur ne doive pas le fournir à nouveau lors des connexions suivantes.

## Consentement incrémentiel

Votre application ne doit pas demander l’ensemble des autorisations requises lors des inscriptions ou des connexions initiales de l’utilisateur. Dans la mesure où vous pouvez spécifier les étendues par requête, votre application peut procéder à un « consentement incrémentiel » et déterminer le moment idéal pour demander son consentement à l’utilisateur. Voici quelques raisons pour lesquelles une application ne demande pas forcément l’ensemble des autorisations simultanément :

- L’application nécessite de nombreuses autorisations. Si votre application accède à de nombreuses ressources différentes et exécute de riches fonctionnalités sur chacune d’entre elles, la liste des autorisations requises par votre application peut rapidement s’allonger. Historiquement, les longues listes d’autorisations ont découragé les utilisateurs et ont réduit l’adoption des applications. Au lieu de demander l’ensemble de ces autorisations simultanément, votre application peut requérir un sous-ensemble lors de la connexion initiale, puis demander de manière incrémentielle des autorisations supplémentaires à mesure que l’utilisateur tente d’utiliser des fonctions avancées.
- L’application évolue au fil du temps. Quasiment toutes les applications sont lancées avec un ensemble réduit de fonctionnalités, qui s’enrichit par la suite. Grâce au consentement incrémentiel, vous pouvez facilement apporter des modifications à votre application et demander sans heurt de nouvelles autorisations à l’utilisateur. Il vous suffit de mettre à jour votre code afin d’envoyer des étendues supplémentaires dans les demandes d’autorisation.
- Autorisations exclusivement dédiées aux administrateurs Certaines ressources définissent des autorisations pouvant être acceptées, ou approuvées, **uniquement** par un administrateur d’organisation. Par exemple, l’API Graph Azure AD définit l’autorisation `Directory.Write`, grâce à laquelle une application peut créer, mettre à jour et supprimer des utilisateurs et des groupes, entre autres. Vous pouvez imaginer pourquoi cette autorisation peut nécessiter l’approbation d’un administrateur disposant de hauts privilèges. En utilisant le consentement incrémentiel, votre application peut exposer un ensemble basique de fonctionnalités, auxquelles un utilisateur peut souscrire sans aucune approbation d’administrateur. Néanmoins, une fois que l’utilisateur essaie d’exécuter une action nécessitant des privilèges élevés, vous pouvez solliciter l’autorisation exclusive de l’administrateur et demander qu’un administrateur se connecte avant de libérer l’accès à cette portion de votre application.

## Utilisation des autorisations

Une fois que l’utilisateur accepte les autorisations pour votre application, cette dernière peut acquérir des jetons d’accès représentant l’autorisation de votre application à accéder, dans une certaine capacité, à une ressource. Un jeton d’accès donné peut être utilisé pour une ressource uniquement, mais l’encodage comporte les informations relatives à l’ensemble des autorisations octroyé pour cette ressource à votre application. Pour acquérir un jeton d’accès, votre application peut transmettre une requête au point de terminaison de jetons v2.0 :

```
POST common/v2.0/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "authorization_code",
	"client_id": "2d4d11a2-f814-46a7-890a-274a72a7309e",
	"scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
	"code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
	"client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

Le jeton d’accès résultant peut ensuite être utilisé dans les requêtes HTTP dirigées vers cette ressource. Il indique de manière fiable à la ressource que votre application dispose de l’autorisation appropriée pour effectuer une tâche spécifique.

Pour en savoir plus sur le protocole OAuth 2.0 et sur le mode d’acquisition des jetons d’accès, consultez la page de [référence sur les protocoles du modèle d’application v2.0](active-directory-v2-protocols.md).

## OpenId et Offline\_Access

Le modèle d’application v2.0 présente deux étendues bien définies, qui ne s’appliquent pas à une ressource particulière : `openid` et `offline_access`.

#### OpenId

Si une application exécute la connexion à l’aide d’[OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow), elle doit solliciter l’étendue `openid`. L’étendue `openid` s’affiche sur la page de consentement de l’écran de travail, en tant qu’autorisation de connexion, et sur la page de consentement du compte Microsoft personnel en tant qu’autorisation « Afficher votre profil et se connecter aux applications et aux services à l’aide de votre compte Microsoft ». Grâce à cette autorisation, une application peut accéder au point de terminaison des informations sur l’utilisateur OpenID Connect. Aussi, elle nécessite l’approbation de l’utilisateur. L’étendue `openid` peut également être utilisée sur le point de terminaison de jeton v2.0 afin d’acquérir des jetons id\_token, qui peuvent être sollicités pour sécuriser les appels HTTP entre différents composants d’une application.

#### Offline\_Access

L’étendue `offline_access` permet à votre application d’accéder aux ressources pour le compte de l’utilisateur, pendant une période prolongée. Sur l’écran de consentement du compte de travail, cette étendue apparaît en tant qu’autorisation d’« Accéder à vos données à tout moment ». Sur l’écran de consentement du compte Microsoft personnel, elle apparaît en tant qu’autorisation « Accéder à vos informations à tout moment ». Lorsqu’un utilisateur approuve l’étendue `offline_access`, votre application est configurée pour recevoir les jetons d’actualisation du point de terminaison des jetons v2.0. Les jetons d’actualisation, durables, sont mis à profit par votre application pour acquérir de nouveaux jetons d’accès lors de l’expiration des anciens.

Si votre application ne sollicite pas l’étendue `offline_access`, elle ne reçoit pas les jetons d’actualisation. Ainsi, lorsque vous échangez un code d’autorisation dans le [flux de code d’autorisation OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow), vous recevez uniquement un jeton d’accès du point de terminaison `/token`. Ce jeton d’accès demeure valide pendant une courte période (généralement une heure), avant d’arriver à expiration. À ce stade, votre application doit rediriger l’utilisateur vers le point de terminaison `/authorize` afin de récupérer un nouveau code d’autorisation. Pendant ce réacheminement, il peut être demandé à l’utilisateur d’entrer à nouveau ses informations d’identification ou d’accepter une nouvelle fois les autorisations, en fonction du type d’application.

Pour en savoir plus sur la récupération et l’utilisation des jetons d’actualisation, consultez la page de [référence sur les protocoles du modèle d’application v2.0](active-directory-v2-protocols.md).

<!---HONumber=Oct15_HO1-->