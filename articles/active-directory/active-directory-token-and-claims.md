<properties 
   pageTitle="Types de jeton et de revendication pris en charge"
   description="Un guide pour la compréhension et l'évaluation des revendications dans les jetons SAML 2.0 et les jetons web JSON (JWT) émis par Azure Active Directory (AAD)"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   services="active-directory" 
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/01/2015"
   ms.author="mbaldwin"/>

# Types de jeton et de revendication pris en charge

Cette rubrique est conçue pour vous aider à comprendre et évaluer les revendications dans les jetons SAML 2.0 et les jetons web JSON (JWT) émis par Azure Active Directory (Azure AD).

La rubrique commence par une description de chaque revendication de jeton et montre un exemple de la revendication dans un jeton SAML et un jeton JWT, le cas échéant. Les revendications qui ont le statut d'aperçu sont répertoriées séparément. La rubrique se termine avec des exemples de jetons pour vous permettre de voir les revendications en contexte.

Azure ajoute des revendications aux jetons au fil du temps pour permettre de nouveaux scénarios. Généralement, nous présentons ces revendications avec le statut d'aperçu puis nous les convertissons au statut de prise en charge complète après une période d'essai. Pour préparer les modifications de revendication, les applications qui acceptent les jetons d'Azure AD doivent ignorer les revendications de jeton inconnues afin que les nouvelles revendications ne provoquent pas une interruption de l'application. Les applications qui utilisent les revendications qui ont un statut d'aperçu ne doivent pas dépendre de ces revendications et ne doivent pas lever des exceptions si la revendication n'apparaît pas dans le jeton. Si votre application a besoin de revendications qui ne sont pas disponibles dans les jetons SAML ou JWT émis par Azure AD, utilisez la section Ajouts de la communauté au bas de cette page pour faire des suggestions et discuter des nouveaux types de revendication.

## Référence des revendications de jeton

Cette section répertorie et décrit les revendications dans des jetons retournés par Azure AD. Elle inclut la version SAML et la version JWT de la revendication ainsi qu’une description de la revendication et de son utilisation. Les revendications sont répertoriées par ordre alphabétique.

### ID de l'application

La revendication de l’ID de l'application identifie l'application qui utilise le jeton pour accéder à une ressource. L'application peut agir pour elle-même ou pour le compte d'un utilisateur. L'ID d'application représente généralement un objet d’application, mais elle peut également représenter un objet du principal du service dans Azure AD.

Azure AD ne prend pas en charge une revendication de l’ID d'application dans un jeton SAML.

Dans un jeton JWT, l'ID d'application s'affiche dans une revendication appid.

    "appid":"15CB020F-3984-482A-864D-1D92265E8268"

### Public ciblé
L’audience d'un jeton est le destinataire du jeton. L'application qui reçoit le jeton doit vérifier que la valeur de l'audience est correcte et rejeter les jetons destinés à une autre audience.

La valeur de l'audience est une chaîne, généralement l'adresse de base de la ressource à laquelle on accède, telle que « https://contoso.com ». Dans les jetons Azure AD, l'audience est l'URI d’ID de l'application qui a demandé le jeton. Lorsque l'application (c’est-à-dire l’audience) possède plusieurs URI d’ID de l’application, l'URI d’ID de l’application dans la revendication d’audience du jeton correspond à l'URI d’ID de l’application dans la demande de jeton. Dans un jeton SAML, la revendication d’audience est définie dans l'élément Audience de l'élément AudienceRestriction.

    <AudienceRestriction>
    <Audience>https://contoso.com</Audience>
    </AudienceRestriction>

Dans un jeton JWT, l’audience apparaît dans une revendication aud.

    "aud":"https://contoso.com"

### Référence de classe du contexte d'authentification de l'application

La revendication de référence de classe du contexte de l'authentification de l’application indique comment le client a été authentifié. Pour un client public, la valeur est 0. Si l'ID client et la clé secrète client sont utilisés, la valeur est 1.

Dans un jeton JWT, la valeur de référence de classe du contexte de l'authentification s'affiche dans une revendication appidacr (valeur ACR spécifique à l'application).

    "appidacr": "0"

### Référence de classe du contexte d'authentification
La revendication de référence de classe du contexte de l'authentification indique comment le sujet a été authentifié, et non pas le client comme dans la revendication de référence de classe du contexte de l'authentification de l’application. La valeur « 0 » indique que l'authentification de l'utilisateur final ne répondait pas aux exigences de la norme ISO/IEC 29115.

- Dans un jeton JWT, la revendication de référence de classe du contexte de l'authentification s'affiche dans une revendication appidacr (valeur ACR spécifique à l'utilisateur).

    « acr » : « 0 »

### Moment d'authentification

La revendication du moment d'authentification enregistre la date et l'heure de l'authentification.

Dans un jeton SAML, le moment d'authentification s'affiche dans l'attribut AuthnInstant de l'élément AuthnStatement. Il représente une valeur DateHeure en temps UTC (Z).

    <AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">

Azure AD n'a pas de revendication équivalente dans les jetons JWT.

### Méthode d'authentification

La revendication de la méthode d'authentification indique comment le sujet du jeton a été authentifié. Dans cet exemple, le fournisseur d'identité a utilisé un mot de passe pour authentifier l'utilisateur. http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password

Dans un jeton SAML, la valeur de la méthode d'authentification s'affiche dans l'élément AuthnContextClassRef.

    <AuthnContextClassRef>http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password</AuthnContextClassRef>

Dans un jeton JWT, la valeur de la méthode d'authentification s'affiche dans la revendication amr.

    “amr”: ["pwd"]

###Prénom

La revendication du prénom fournit le prénom de l'utilisateur tel que défini dans l'objet utilisateur Azure AD.

Dans un jeton SAML, le prénom s'affiche dans une revendication dans l'élément d'attribut SAML givenname.

    <Attribute Name=” http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname”>
    <AttributeValue>Frank<AttributeValue>

Dans un jeton JWT, le prénom apparaît dans la revendication given_name.

    "given_name": "Frank"

### Groupes

La revendication des groupes fournit les ID d'objet qui représentent les appartenances aux groupes du sujet. Ces valeurs sont uniques (voir l'ID objet) et peuvent être utilisées en toute sécurité pour la gestion des accès, telle que l'autorisation d'accéder à une ressource. Les groupes inclus dans la revendication des groupes sont configurés pour chaque application, via la propriété « groupMembershipClaims » du manifeste d'application. Une valeur Null exclut tous les groupes, une valeur « SecurityGroup » inclut uniquement les appartenances aux groupes de sécurité Active Directory et une valeur « All » inclut les groupes de sécurité et les listes de Distribution Office 365. Dans toutes les configurations, la revendication des groupes représente les appartenances aux groupes transitifs du sujet.

Dans un jeton SAML, la revendication des groupes s'affiche dans l'attribut des groupes.

    <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
    <AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>

Dans un jeton JWT, la revendication des groupes s'affiche dans l'attribut des groupes.

    “groups”: ["0e129f5b-6b0a-4944-982d-f776045632af", … ]

### Fournisseur d’identité :

La revendication du fournisseur d'identité enregistre le fournisseur d’identité qui a authentifié le sujet du jeton. Cette valeur est identique à la valeur de la revendication de l’émetteur sauf si le compte d'utilisateur est dans un autre client que l'émetteur.

Dans un jeton SAML, le fournisseur d'identité apparaît dans une revendication dans l'élément d'attribut SAML identityprovider.

    <Attribute Name=” http://schemas.microsoft.com/identity/claims/identityprovider”>
    <AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>

Dans un jeton JWT, le fournisseur d'identité s'affiche dans une revendication idp.

    "idp":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”

### IssuedAt

La revendication IssuedAt enregistre l'heure à laquelle le jeton a été émis. Il est souvent utilisé pour mesurer l’actualisation du jeton. Dans un jeton SAML, la valeur IssuedAt s'affiche dans l'assertion IssueInstant.

    <Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">

Dans un jeton JWT, la valeur IssuedAt apparaît dans la revendication iat. La valeur est exprimée en nombre de secondes depuis le 01/01/1970 0:0:0Z en temps universel coordonné (UTC).

    "iat": 1390234181

### Émetteur

La revendication de l’émetteur identifie le service d’émission de jeton de sécurité (STS) qui construit et retourne le jeton et le client Azure AD Directory. Dans les jetons retournés par Azure AD, l'émetteur est sts.windows.net. Le GUID dans la valeur de revendication de l'émetteur est l'ID client de Azure AD Directory. L'ID client est un identificateur non modifiable et fiable du répertoire.

Dans un jeton SAML, la revendication de l'émetteur apparaît dans un élément Émetteur.

    <Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>

Dans un jeton JWT, l'émetteur s'affiche dans une revendication iss.

    "iss":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”

### Nom

La revendication du nom fournit le nom de famille de l'utilisateur tel que défini dans l'objet utilisateur Azure AD. Dans un jeton SAML, le nom de famille s'affiche dans une revendication dans l'élément d'attribut SAML nom.

    <Attribute Name=” http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname”>
    <AttributeValue>Miller<AttributeValue>

Dans un jeton JWT, le nom de famille apparaît dans la revendication family_name.

    "family_name": "Miller"

### Nom

La revendication du nom fournit une valeur humaine lisible qui identifie le sujet du jeton. Il n’est pas certain que cette valeur soit unique au sein d'un client. Elle est conçue pour être utilisée uniquement à des fins d'affichage. Dans un jeton SAML, le nom apparaît dans l'attribut de nom.

    <Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name”>
    <AttributeValue>frankm@contoso.com<AttributeValue>

Dans une revendication JWT, le nom apparaît dans la revendication unique_name.

    "unique_name": "frankm@contoso.com"

### ID objet

La revendication de l'ID objet contient un identificateur unique d'un objet dans Azure AD. Cette valeur n’est pas modifiable et ne peut pas être réassignée ou réutilisée. Vous pouvez donc l’utiliser pour effectuer des vérifications d'autorisation en toute sécurité, comme lorsque le jeton est utilisé pour accéder à une ressource. Utilisez l'ID objet pour identifier un objet dans les requêtes à Azure AD. Dans un jeton SAML, l'ID objet s'affiche dans l'attribut objectidentifier.

    <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
    <AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>

Dans un jeton JWT, l’ID objet s'affiche dans une revendication oid.

    "oid":"528b2ac2-aa9c-45e1-88d4-959b53bc7dd0"

### contrôleur

La revendication des rôles fournit des chaînes conviviales qui représentent des attributions de rôle de l'application du sujet dans Azure AD. Elles peuvent être utilisées pour appliquer un contrôle d'accès basé sur les rôles. Les rôles d'application sont définis pour chaque application, via la propriété « appRoles » du manifeste d'application. La propriété « valeur » de chaque rôle d'application est la valeur qui s’affiche dans la revendication des rôles. Les rôles inclus dans la revendication des rôles représentent tous les rôles d'application qui ont été accordés au sujet directement et indirectement via l'appartenance au groupe. Dans un jeton SAML, la revendication des rôles s'affiche dans l'attribut des rôles.

    <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">
    <AttributeValue>Admin</AttributeValue>

Dans un jeton JWT, la revendication des rôles s'affiche dans l'attribut des rôles.

    “roles”: ["Admin", … ]

### Scope

L'étendue du jeton indique les autorisations d’emprunt d’identité accordées à l’application cliente. L'autorisation par défaut est user_impersonation. Le propriétaire de la ressource sécurisée peut enregistrer des valeurs supplémentaires dans Azure AD.

Dans un jeton JWT, l'étendue du jeton est spécifiée dans une revendication scp.

    "scp": "user_impersonation"

### Objet

Le sujet du jeton est le principal pour lequel le jeton fournit des informations telles que l'utilisateur d'une application. Cette valeur n’est pas modifiable et ne peut pas être réassignée ou réutilisée. Vous pouvez donc l’utiliser pour effectuer des vérifications d'autorisation en toute sécurité, comme lorsque le jeton est utilisé pour accéder à une ressource. Étant donné que le sujet est toujours présent dans les jetons émis par Azure AD, vous êtes invité à utiliser cette valeur dans un but général de système d'autorisation.

Dans un jeton SAML, le sujet du jeton est spécifié dans l'élément NameID de l'élément Subject. L’élément NameID est un identificateur unique, non réutilisé du sujet, qui peut être un utilisateur, une application ou un service.

SubjectConfirmation n'est pas une revendication. Il décrit comment le sujet du jeton est vérifié. « Support » indique que le sujet est confirmé par leur possession du jeton.

    <Subject>
    <NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>
    <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
    </Subject>

Dans un jeton JWT, l'objet s'affiche dans une revendication sub.

    "sub":"92d0312b-26b9-4887-a338-7b00fb3c5eab"

### ID client
L'ID client est un identificateur non modifiable et non réutilisable qui identifie le client du répertoire qui a émis le jeton. Vous pouvez utiliser cette valeur pour accéder à des ressources de répertoire spécifiques au client dans une application mutualisée. Par exemple, vous pouvez utiliser cette valeur pour identifier le client dans un appel à l'API Graph.

Dans un jeton SAML, l’ID client s'affiche dans une revendication dans l'élément d'attribut SAML tenantid.

    <Attribute Name=”http://schemas.microsoft.com/identity/claims/tenantid”>
    <AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>

Dans un jeton JWT, l'ID client apparaît dans une revendication tid.

    "tid":"cbb1a5ac-f33b-45fa-9bf5-f37db0fed422"

### Durée de vie du jeton
La revendication de durée de vie du jeton définit l'intervalle de temps pendant lequel un jeton est valide. Le service qui valide le jeton doit vérifier que la date actuelle est dans sa durée de vie. Dans le cas contraire, le jeton sera rejeté. Le service peut accorder une marge de cinq minutes au-delà de la plage de durée de vie du jeton pour prendre en compte tout écart de temps (« décalage horaire ») entre Azure AD et le service.

Dans un jeton SAML, la revendication de durée de vie du jeton est définie dans l'élément Conditions à l’aide des attributs NotBefore et NotOnOrAfter.

    <Conditions
    NotBefore="2013-03-18T21:32:51.261Z"
    NotOnOrAfter="2013-03-18T22:32:51.261Z"
    >

Dans un jeton JWT, la durée de vie du jeton est définie par les revendications nbf (pas avant) et exp (délai d'expiration). La valeur de ces revendications est exprimée en nombre de secondes depuis le 01/01/1970 0:0:0Z en temps universel coordonné (UTC). Pour plus d'informations, consultez RFC 3339.

    "nbf":1363289634,
    "exp":1363293234

### Nom d'utilisateur principal
La revendication de nom d'utilisateur principal enregistre le nom de l'utilisateur principal.

Dans un jeton JWT, le nom d'utilisateur principal apparaît dans une revendication upn.

    "upn": frankm@contoso.com

### Version
La revendication de version enregistre le numéro de version du jeton. Dans un jeton JWT, le nom d'utilisateur principal apparaît dans une revendication ver.

    "ver": "1.0"

## Exemples de jeton

Cette section affiche des exemples de jetons SAML et JWT retournés par Azure AD. Ces exemples vous permettent de voir les revendications en contexte. Jeton SAML

Il s'agit d'un exemple de jeton SAML classique.

	<?xml version="1.0" encoding="UTF-8"?>
	<t:RequestSecurityTokenResponse xmlns:t="http://schemas.xmlsoap.org/ws/2005/02/trust">
	  <t:Lifetime>
		<wsu:Created xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T05:15:47.060Z</wsu:Created>
		<wsu:Expires xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T06:15:47.060Z</wsu:Expires>
	  </t:Lifetime>
	  <wsp:AppliesTo xmlns:wsp="http://schemas.xmlsoap.org/ws/2004/09/policy">
		<EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
		  <Address>https://contoso.onmicrosoft.com/MyWebApp</Address>
		</EndpointReference>
	  </wsp:AppliesTo>
	  <t:RequestedSecurityToken>
		<Assertion xmlns="urn:oasis:names:tc:SAML:2.0:assertion" ID="_3ef08993-846b-41de-99df-b7f3ff77671b" IssueInstant="2014-12-24T05:20:47.060Z" Version="2.0">
		  <Issuer>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</Issuer>
		  <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
			<ds:SignedInfo>
			  <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
			  <ds:SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha256" />
			  <ds:Reference URI="#_3ef08993-846b-41de-99df-b7f3ff77671b">
				<ds:Transforms>
				  <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature" />
				  <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
				</ds:Transforms>
				<ds:DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha256" />
				<ds:DigestValue>cV1J580U1pD24hEyGuAxrbtgROVyghCqI32UkER/nDY=</ds:DigestValue>
			  </ds:Reference>
			</ds:SignedInfo>
			<ds:SignatureValue>j+zPf6mti8Rq4Kyw2NU2nnu0pbJU1z5bR/zDaKaO7FCTdmjUzAvIVfF8pspVR6CbzcYM3HOAmLhuWmBkAAk6qQUBmKsw+XlmF/pB/ivJFdgZSLrtlBs1P/WBV3t04x6fRW4FcIDzh8KhctzJZfS5wGCfYw95er7WJxJi0nU41d7j5HRDidBoXgP755jQu2ZER7wOYZr6ff+ha+/Aj3UMw+8ZtC+WCJC3yyENHDAnp2RfgdElJal68enn668fk8pBDjKDGzbNBO6qBgFPaBT65YvE/tkEmrUxdWkmUKv3y7JWzUYNMD9oUlut93UTyTAIGOs5fvP9ZfK2vNeMVJW7Xg==</ds:SignatureValue>
			<KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
			  <X509Data>
				<X509Certificate>MIIDPjCCAabcAwIBAgIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTQwMTAxMDcwMDAwWhcNMTYwMTAxMDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAkSCWg6q9iYxvJE2NIhSyOiKvqoWCO2GFipgH0sTSAs5FalHQosk9ZNTztX0ywS/AHsBeQPqYygfYVJL6/EgzVuwRk5txr9e3n1uml94fLyq/AXbwo9yAduf4dCHTP8CWR1dnDR+Qnz/4PYlWVEuuHHONOw/blbfdMjhY+C/BYM2E3pRxbohBb3x//CfueV7ddz2LYiH3wjz0QS/7kjPiNCsXcNyKQEOTkbHFi3mu0u13SQwNddhcynd/GTgWN8A+6SN1r4hzpjFKFLbZnBt77ACSiYx+IHK4Mp+NaVEi5wQtSsjQtI++XsokxRDqYLwus1I1SihgbV/STTg5enufuwIDAQABo2IwYDBeBgNVHQEEVzBVgBDLebM6bK3BjWGqIBrBNFeNoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAA4IBAQCJ4JApryF77EKC4zF5bUaBLQHQ1PNtA1uMDbdNVGKCmSp8M65b8h0NwlIjGGGy/unK8P6jWFdm5IlZ0YPTOgzcRZguXDPj7ajyvlVEQ2K2ICvTYiRQqrOhEhZMSSZsTKXFVwNfW6ADDkN3bvVOVbtpty+nBY5UqnI7xbcoHLZ4wYD251uj5+lo13YLnsVrmQ16NCBYq2nQFNPuNJw6t3XUbwBHXpF46aLT1/eGf/7Xx6iy8yPJX4DyrpFTutDz882RWofGEO5t4Cw+zZg70dJ/hH/ODYRMorfXEW+8uKmXMKmX2wyxMKvfiPbTy5LmAU8Jvjs2tLg4rOBcXWLAIarZ</X509Certificate>
			  </X509Data>
			</KeyInfo>
		  </ds:Signature>
		  <Subject>
			<NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">m_H3naDei2LNxUmEcWd0BZlNi_jVET1pMLR6iQSuYmo</NameID>
			<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
		  </Subject>
		  <Conditions NotBefore="2014-12-24T05:15:47.060Z" NotOnOrAfter="2014-12-24T06:15:47.060Z">
			<AudienceRestriction>
			  <Audience>https://contoso.onmicrosoft.com/MyWebApp</Audience>
			</AudienceRestriction>
		  </Conditions>
		  <AttributeStatement>
			<Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
			  <AttributeValue>a1addde8-e4f9-4571-ad93-3059e3750d23</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">
			  <AttributeValue>b9411234-09af-49c2-b0c3-653adc1f376e</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
			  <AttributeValue>sample.admin@contoso.onmicrosoft.com</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">
			  <AttributeValue>Admin</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">
			  <AttributeValue>Sample</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
			  <AttributeValue>5581e43f-6096-41d4-8ffa-04e560bab39d</AttributeValue>
			  <AttributeValue>07dd8a89-bf6d-4e81-8844-230b77145381</AttributeValue>
			  <AttributeValue>0e129f4g-6b0a-4944-982d-f776000632af</AttributeValue>
			  <AttributeValue>3ee07328-52ef-4739-a89b-109708c22fb5</AttributeValue>
			  <AttributeValue>329k14b3-1851-4b94-947f-9a4dacb595f4</AttributeValue>
			  <AttributeValue>6e32c650-9b0a-4491-b429-6c60d2ca9a42</AttributeValue>
			  <AttributeValue>f3a169a7-9a58-4e8f-9d47-b70029v07424</AttributeValue>
			  <AttributeValue>8e2c86b2-b1ad-476d-9574-544d155aa6ff</AttributeValue>
			  <AttributeValue>1bf80264-ff24-4866-b22c-6212e5b9a847</AttributeValue>
			  <AttributeValue>4075f9c3-072d-4c32-b542-03e6bc678f3e</AttributeValue>
			  <AttributeValue>76f80527-f2cd-46f4-8c52-8jvd8bc749b1</AttributeValue>
			  <AttributeValue>0ba31460-44d0-42b5-b90c-47b3fcc48e35</AttributeValue>
			  <AttributeValue>edd41703-8652-4948-94a7-2d917bba7667</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.microsoft.com/identity/claims/identityprovider">
			  <AttributeValue>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</AttributeValue>
			</Attribute>
		  </AttributeStatement>
		  <AuthnStatement AuthnInstant="2014-12-23T18:51:11.000Z">
			<AuthnContext>
			  <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
			</AuthnContext>
		  </AuthnStatement>
		</Assertion>
	  </t:RequestedSecurityToken>
	  <t:RequestedAttachedReference>
		<SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
		  <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
		</SecurityTokenReference>
	  </t:RequestedAttachedReference>
	  <t:RequestedUnattachedReference>
		<SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
		  <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
		</SecurityTokenReference>
	  </t:RequestedUnattachedReference>
	  <t:TokenType>http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0</t:TokenType>
	  <t:RequestType>http://schemas.xmlsoap.org/ws/2005/02/trust/Issue</t:RequestType>
	  <t:KeyType>http://schemas.xmlsoap.org/ws/2005/05/identity/NoProofKey</t:KeyType>
	</t:RequestSecurityTokenResponse>

### Jeton JWT - emprunt d'identité de l'utilisateur

Il s'agit d'un exemple de jeton web JSON (JWT) classique utilisé dans un flux web d'emprunt d'identité de l’utilisateur. Outre les revendications, le jeton inclut un numéro de version dans **ver** et **appidacr** et la référence de classe du contexte de l'authentification qui indique comment le client a été authentifié. Pour un client public, la valeur est 0. Si un ID client ou une clé secrète client est utilisé, la valeur est 1.

    {
     typ: "JWT",
     alg: "RS256",
     x5t: "kriMPdmBvx68skT8-mPAB3BseeA"
    }.
    {
     aud: "https://contoso.onmicrosoft.com/scratchservice",
     iss: "https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/",
     iat: 1416968588,
     nbf: 1416968588,
     exp: 1416972488,
     ver: "1.0",
     tid: "b9411234-09af-49c2-b0c3-653adc1f376e",
     amr: [
      "pwd"
     ],
     roles: [
      "Admin"
     ],
     oid: "6526e123-0ff9-4fec-ae64-a8d5a77cf287",
     upn: "sample.user@contoso.onmicrosoft.com",
     unique_name: "sample.user@contoso.onmicrosoft.com",
     sub: "yf8C5e_VRkR1egGxJSDt5_olDFay6L5ilBA81hZhQEI",
     family_name: "User",
     given_name: "Sample",
     groups: [
      "0e129f6b-6b0a-4944-982d-f776000632af",
      "323b13b3-1851-4b94-947f-9a4dacb595f4",
      "6e32c250-9b0a-4491-b429-6c60d2ca9a42",
      "f3a161a7-9a58-4e8f-9d47-b70022a07424",
      "8d4c81b2-b1ad-476d-9574-544d155aa6ff",
      "1bf80164-ff24-4866-b19c-6212e5b9a847",
      "76f80127-f2cd-46f4-8c52-8edd8bc749b1",
      "0ba27160-44d0-42b5-b90c-47b3fcc48e35"
     ],
     appid: "b075ddef-0efa-123b-997b-de1337c29185",
     appidacr: "1",
     scp: "user_impersonation",
     acr: "1"
    }.

##Voir aussi

[Protocoles d’authentification d’Azure Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx)
 

<!---HONumber=July15_HO4-->