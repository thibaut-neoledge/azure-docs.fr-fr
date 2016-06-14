<properties
   pageTitle="Comprendre le flux d’octroi implicite OAuth2 dans Azure Active Directory | Microsoft Azure"
   description="Apprenez-en plus sur l’implémentation du flux d’octroi implicite OAuth2 d’Azure Active Directory, et déterminez si elle est adaptée à votre application."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="vibronet"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/01/2016"
   ms.author="vittorib;bryanla"/>

# Comprendre le flux d’octroi implicite OAuth2 dans Azure Active Directory (AD)

L’octroi implicite OAuth2 est connu pour présenter le plus grand nombre de problèmes de sécurité de la spécification OAuth2. Pourtant, c’est l’approche implémentée par ADAL JS et celle que nous recommandons pour l’écriture d’applications à page unique. Quelle en est la raison ? Tout est une question de compromis : il s’avère que l’octroi implicite est la meilleure approche vous pouvez adopter pour les applications qui utilisent une API web via JavaScript à partir d’un navigateur.

## Qu’est-ce que l’octroi implicite OAuth2 ?

Fondamentalement, [l’octroi de code d’autorisation OAuth2](https://tools.ietf.org/html/rfc6749#section-1.3.1) est l’octroi d’autorisation qui utilise deux points de terminaison distincts. Le point de terminaison d’autorisation est utilisé pour la phase d’interaction utilisateur, ce qui génère un code d’autorisation ; le point de terminaison de jeton est ensuite utilisé par le client pour échanger le code contre un jeton d’accès et, souvent, un jeton d’actualisation. Les applications web sont tenues de présenter leurs propres informations d’identification d’application au point de terminaison de jeton, de sorte que le serveur d’autorisation puisse authentifier le client.

[L’octroi implicite OAuth2](https://tools.ietf.org/html/rfc6749#section-1.3.2) est défini en tant que variante dans laquelle un client peut obtenir un jeton d’accès (et, dans le cas [d’OpenId Connect](http://openid.net/specs/openid-connect-core-1_0.html), un jeton id\_token) directement à partir du point de terminaison d’autorisation, sans avoir à contacter le point de terminaison de jeton ni à authentifier l’application cliente. Cette variante a été spécialement conçue pour les applications basées sur JavaScript, qui s’exécutent dans un navigateur web : dans la spécification OAuth2 d’origine, les jetons sont retournés dans un fragment d’URI. Ceci met les bits de jeton à la disposition du code JavaScript dans le client, mais garantit qu’ils ne seront pas inclus dans les redirections vers le serveur. Le retour des jetons par le biais de redirections de navigateur directement à partir de points de terminaison d’autorisation présente également l’avantage d’éliminer toutes les exigences liées aux appels cross-origin, ce qui serait nécessaire si l’application JavaScript devait contacter le point de terminaison de jeton.

Le fait que ces flux ne retournent jamais de jetons d’actualisation au client représente une caractéristique importante de l’octroi implicite OAuth2. Comme nous allons le voir dans la section suivante, ceci n’est pas vraiment nécessaire et représenterait en fait un problème de sécurité.

## Scénarios adaptés à l’octroi implicite OAuth2

Comme la spécification OAuth2 le déclare elle-même, l’octroi implicite a été conçu pour autoriser les applications agent utilisateur – autrement dit, les applications JavaScript exécutées dans un navigateur. La caractéristique essentielle de ces applications réside dans le fait que le code JavaScript est utilisé pour accéder aux ressources du serveur (généralement une API web) et mettre à jour l’expérience utilisateur de l’application en conséquence. Pensez à des applications comme Gmail ou Outlook Web Access : lorsque vous sélectionnez un message dans votre boîte de réception, seul le volet de visualisation du message change pour afficher la nouvelle sélection, tandis que le reste de la page reste inchangé. La situation est totalement différente avec les applications web basées sur une redirection classique : chaque interaction utilisateur entraîne une publication de la page entière et un rendu de page complet de la nouvelle réponse du serveur.

Les applications qui poussent l’approche JavaScript à son extrême sont appelées « applications à page unique » : l’idée est que ces applications traitent uniquement une page HTML initiale et le code JavaScript associé. Toutes les interactions résultantes sont basées sur les appels d’API web effectués avec JavaScript. Toutefois, les approches hybrides (l’application est principalement basée sur la publication, mais effectue des appels JS occasionnels pour implémenter certaines expériences) ne sont pas rares. La discussion sur l’utilisation de flux implicites s’applique également à ces approches.

Généralement, les applications basées sur la redirection sécurisent leurs demandes à l’aide de cookies ; toutefois, cette approche ne fonctionne pas aussi bien pour les applications JavaScript, étant donné que les cookies ne fonctionnent que sur le domaine pour lequel ils ont été générés, tandis que les appels JavaScript peuvent être dirigés vers d’autres domaines. En fait, ce sera très souvent le cas : pensez aux applications appelant une API Graph Microsoft, une API Office, une API Azure – résidant toutes en dehors du domaine à partir duquel l’application est traitée. Une tendance croissante pour les applications JavaScript consiste à n’avoir aucun principal et à reposer à 100 % sur des API web tierces pour implémenter leur fonction métier.

Actuellement, la méthode recommandée pour protéger les appels aux API web consiste à utiliser l’approche de jeton du porteur OAuth2 : chaque appel est accompagné d’un jeton d’accès OAuth2 ; l’API web examine le jeton d’accès entrant et, si elle y trouve les étendues nécessaires, accorde l’accès à l’opération demandée. Le flux implicite fournit un mécanisme pratique permettant aux applications JavaScript d’obtenir des jetons d’accès pour une API web et offre ainsi de nombreux avantages en ce qui concerne les cookies :

- Les jetons peuvent être obtenus de façon fiable sans avoir recours aux appels cross-origin – l’inscription obligatoire de l’URI de redirection vers laquelle les jetons sont retournés garantit que les jetons ne sont pas déplacés.
- Les applications JavaScript peuvent obtenir autant de jetons d’accès que nécessaire, pour un nombre illimité d’API web ciblées, sans aucune restriction sur les domaines.
- Les fonctionnalités HTML5 comme le stockage de session ou local accordent un contrôle total sur la gestion de la mise en cache et de la durée de vie des jetons, tandis que la gestion des cookies est opaque pour l’application.
- Les jetons d’accès ne sont pas vulnérables aux falsifications de requête intersites (CSRF, Cross Site Request Forgery).

Le flux d’octroi implicite n’émet pas de jetons d’actualisation, principalement pour des raisons de sécurité. L’étendue d’un jeton d’actualisation n’est pas aussi étroite que celle des jetons d’accès. Ainsi, un jeton d’actualisation offre une puissance bien supérieure et peut causer des dommages beaucoup plus lourds en cas de fuite. Dans le flux implicite, les jetons sont remis à l’URL. Par conséquent, le risque d’interception est plus élevé qu’avec l’octroi de code d’autorisation.

Toutefois, notez qu’une application JavaScript a un autre mécanisme à sa disposition pour renouveler les jetons d’accès sans inviter l’utilisateur à entrer ses informations d’identification à plusieurs reprises. L’application peut utiliser un IFrame masqué pour effectuer de nouvelles demandes de jeton sur le point de terminaison d’autorisation d’Azure AD : tant que le navigateur a une session active (c’est-à-dire qu’il dispose d’un cookie de session) sur le domaine Azure AD, la demande d’authentification peut s’effectuer correctement sans aucune intervention de l’utilisateur.

Ce modèle permet à l’application JavaScript de renouveler indépendamment des jetons d’accès, voire d’en acquérir de nouveaux pour une nouvelle API (à condition que l’utilisateur ait déjà accordé son consentement) sans avoir, en plus, à acquérir, gérer et protéger un artefact de valeur élevée comme un jeton d’actualisation. L’artefact qui permet un renouvellement silencieux, le cookie de session Azure AD, est géré en dehors de l’application. Cette approche offre un autre avantage : lorsqu’un utilisateur se déconnecte d’Azure AD à partir de toute application en utilisant Azure AD dans l’un des onglets du navigateur (ce qui entraîne la suppression du cookie de session Azure AD), l’application JavaScript perd automatiquement la capacité de renouveler les jetons pour cet utilisateur.

## L’octroi implicite est-il adapté à mon application ?

L’octroi implicite présente plus de risques que d’autres octrois. Les aspects auxquels vous devez prêter attention sont bien documentés. Par exemple, consultez [Misuse of Access Token to Impersonate Resource Owner in Implicit Flow][OAuth2-Spec-Implicit-Misuse] (Utilisation incorrecte du jeton d’accès pour emprunter l’identité d’un propriétaire des ressources dans un flux implicite) et [OAuth 2.0 Threat Model and Security Considerations][OAuth2-Threat-Model-And-Security-Implications] (Modèle de menace OAuth 2.0 et considérations de sécurité). Toutefois, le profil de risque supérieur est principalement dû au fait qu’il est destiné à autoriser les applications qui exécutent du code actif, envoyé à un navigateur par une ressource distante. Si vous optez pour une architecture d’application à page unique, que vous n’avez aucun composant principal ou que, d’une façon générale, vous envisagez d’appeler une API web à l’aide de JavaScript, l’utilisation du flux implicite pour l’acquisition de jeton est recommandée.

Si votre application est un client natif, ce flux implicite n’est pas très adapté : l’absence du cookie de session Azure AD dans le contexte d’un client natif prive votre application de tout moyen de maintenir une session durable et d’obtenir des jetons d’accès pour les nouvelles ressources sans solliciter l’utilisateur de façon répétée.

Si vous développez une application web qui inclut un composant principal et qui est destinée à utiliser une API à partir de son code principal, le flux implicite ne représente pas non plus une solution adaptée. Vous obtiendrez de meilleures performances avec d’autres types d’octroi : par exemple, l’octroi des informations d’identification des clients OAuth2 permet d’obtenir des jetons qui reflètent les autorisations attribuées à l’application elle-même (contrairement aux délégations d’utilisateur), de gérer l’accès aux ressources par programme même lorsqu’un utilisateur n’est pas activement engagé dans une session, etc. En outre, cet octroi offre des garanties de sécurité supérieures : les jetons d’accès ne transitent jamais par le navigateur de l’utilisateur, ne risquent pas d’être enregistrés dans l’historique du navigateur, etc. L’application cliente peut effectuer une authentification forte lorsqu’un jeton est demandé, etc.

## Étapes suivantes

- Pour obtenir la liste complète des ressources de développement, y compris des informations de référence sur l’ensemble des protocoles et la prise en charge des flux d’octroi d’autorisation OAuth2 par Azure AD, reportez-vous au [Guide du développeur Azure AD][AAD-Developers-Guide].
- Consultez la page [Intégration avec Azure Active Directory][ACOM-How-To-Integrate] pour des informations plus détaillées sur le processus d’intégration d’applications.

<!--Image references-->
[Scenario-Topology]: ./media/active-directory-devhowto-auth-using-any-aad/multi-tenant-aad-components.png

<!--Reference style links in use-->
[AAD-Developers-Guide]: active-directory-developers-guide.md
[ACOM-How-And-Why-Apps-Added-To-AAD]: active-directory-how-applications-are-added.md
[ACOM-How-To-Integrate]: active-directory-how-to-integrate.md
[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819

<!---HONumber=AcomDC_0601_2016-->