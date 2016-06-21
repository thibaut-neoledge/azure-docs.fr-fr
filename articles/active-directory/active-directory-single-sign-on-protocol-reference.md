<properties
	pageTitle="Protocole SAML d’authentification unique Azure | Microsoft Azure"
	description="Cet article décrit le protocole SAML d’authentification unique dans Azure Active Directory"
	services="active-directory"
	documentationCenter=".net"
	authors="priyamohanram"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/31/2016"
	ms.author="priyamo"/>

# Protocole SAML d’authentification unique

[AZURE.INCLUDE [active-directory-protocols](../../includes/active-directory-protocols.md)]

Cet article vous permet de vous familiariser avec les demandes et réponses d’authentification SAML 2.0 prises en charge par Azure Active Directory (Azure AD) dans le cadre de l’authentification unique.

Le schéma de protocole ci-dessous décrit la séquence d’authentification unique. Le service cloud (le fournisseur de services) utilise une liaison de redirection HTTP pour transmettre un élément `AuthnRequest` (demande d’authentification) à Azure AD (le fournisseur d’identité). Azure AD utilise ensuite une liaison HTTP POST pour valider un élément `Response` auprès du service cloud.

![Workflow d’authentification unique](media/active-directory-single-sign-on-protocol-reference/active-directory-saml-single-sign-on-workflow.png)

## AuthnRequest

Pour demander une authentification utilisateur, les services cloud envoient un élément `AuthnRequest` à Azure AD. Voici un exemple d’élément `AuthnRequest` SAML 2.0 :

```
<samlp:AuthnRequest
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="id6c1c178c166d486687be4aaf5e482730"
Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
</samlp:AuthnRequest>
```


| Paramètre | | Description |
| ----------------------- | ------------------------------- | --------------- |
| ID | required | Azure AD utilise cet attribut pour compléter l’attribut `InResponseTo` de la réponse retournée. L’ID ne doit pas commencer par un nombre ; vous pouvez donc suivre la stratégie courante qui consiste à ajouter une chaîne de type « id » devant la représentation sous forme de chaîne d’un GUID. Par exemple, `id6c1c178c166d486687be4aaf5e482730` est un ID valide. |
| Version | required | Il doit s’agir de **2.0**.|
| IssueInstant | required | Chaîne DateTime associée à une valeur UTC et comportant le [format aller-retour (« o »)](https://msdn.microsoft.com/library/az4se3k1.aspx). Azure AD attend une valeur DataTime de ce type, mais n’évalue ni n’utilise cette valeur. |
| AssertionConsumerServiceUrl | facultatif | Si ce paramètre fourni, il doit correspondre à l’élément `RedirectUri` du service cloud dans Azure AD. |
| ForceAuthn | facultatif | S’il est fourni, il doit être défini sur false. Toute autre valeur générera une erreur.|
| IsPassive | facultatif | S’il est fourni, il doit être défini sur false. Toute autre valeur générera une erreur. |  

Tous les autres attributs `AuthnRequest`, tels que Consent, Destination, AssertionConsumerServiceIndex, AttributeConsumerServiceIndex and ProviderName, sont **ignorés**.

Azure AD ignore également l’élément `Conditions` dans `AuthnRequest`.

### Émetteur

L’élément `Issuer` dans `AuthnRequest` doit correspondre exactement à l’un des **ServicePrincipalNames** du service cloud dans Azure AD. En règle générale, il est défini sur **l’URI de l’ID d’application** spécifié au moment de l’inscription de l’application.

Voici un exemple d’extrait de code SAML contenant l’élément `Issuer` :

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
```

### NameIDPolicy

Cet élément demande un format d’ID de nom particulier dans la réponse et est facultatif dans les éléments `AuthnRequest` envoyés à Azure AD.

Exemple d’élément `NameIdPolicy` :

```
<NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
```

Si `NameIDPolicy` est fourni, vous pouvez inclure son attribut `Format` facultatif. L’attribut `Format` ne peut comporter qu’une des valeurs suivantes ; toute autre valeur produira une erreur.

-  `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent` : Azure Active Directory émet la revendication NameID sous la forme d’un identificateur par paire.
- `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress` : Azure Active Directory émet la revendication NameID sous la forme d’une adresse e-mail.
- `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` : cette valeur permet à Azure Active Directory de sélectionner le format de revendication. Azure Active Directory émet la revendication NameID sous la forme d’un identificateur par paire.

N’incluez pas l’attribut `SPNameQualifer`. Azure AD ignore l’attribut `AllowCreate`.

### RequestAuthnContext

L’élément `RequestedAuthnContext` spécifie les méthodes d’authentification souhaitées. Il est facultatif dans les éléments `AuthnRequest` envoyés à Azure AD. Azure AD prend en charge une seule valeur `AuthnContextClassRef` : `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`.

### Scoping

L’élément `Scoping`, qui comprend une liste de fournisseurs d’identité, est facultatif dans les éléments `AuthnRequest` envoyés à Azure AD.

S’il est fourni, n’incluez ni l’attribut `ProxyCount` ni l’élément `IDPListOption` ou `RequesterID`, car ils ne sont pas pris en charge.

### Signature

N’incluez pas d’élément `Signature` dans les éléments `AuthnRequest`, car Azure AD ne prend pas en charge les demandes d’authentification signées.

### Objet

Azure AD ignore l’élément `Subject` des éléments `AuthnRequest`.

## Response

Lorsqu’une demande d’authentification aboutit, Azure AD publie une réponse au service cloud. Exemple de réponse à une tentative réussie d’authentification :

```
<samlp:Response ID="_a4958bfd-e107-4e67-b06d-0d85ade2e76a" Version="2.0" IssueInstant="2013-03-18T07:38:15.144Z" Destination="https://contoso.com/identity/inboundsso.aspx" InResponseTo="id758d0ef385634593a77bdf7e632984b6" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
    ...
  </ds:Signature>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
  <Assertion ID="_bf9c623d-cc20-407a-9a59-c2d0aee84d12" IssueInstant="2013-03-18T07:38:15.144Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
    <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
      ...
    </ds:Signature>
    <Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
  </Assertion>
</samlp:Response>
```

### Response

L’élément `Response` inclut le résultat de la demande d’autorisation. Azure AD définit les valeurs `ID`, `Version` et `IssueInstant` dans l’élément `Response`. Il définit également les attributs suivants :

- `Destination` : lorsque l’authentification aboutit, il est défini sur l’élément `RedirectUri` du fournisseur de services (service cloud).
- `InResponseTo` : cet attribut est définit sur l’attribut `ID` de l’élément `AuthnRequest` qui a émis la réponse.

### Émetteur

Azure AD définit l’élément `Issuer` sur `https://login.microsoftonline.com/<TenantIDGUID>/`, où <TenantIDGUID> correspond à l’ID client du client Azure AD.

Exemple de réponse contenant l’élément Issuer :

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

### Signature

Azure AD signe l’élément `Response` lorsque l’authentification aboutit. L’élément `Signature` contient une signature numérique que l’application peut utiliser pour authentifier la source et vérifier l’intégrité de la réponse.

Azure AD utilise la clé de signature spécifiée dans l’élément `IDPSSODescriptor` de son document de métadonnées. Pour plus d’informations, consultez le [document de métadonnées de fédération](active-directory-federation-metadata.md).

Azure AD signe également l’élément `Assertion`, mais les deux éléments Signature sont indépendants.

Exemple d’élément `Signature` contenu dans la réponse :

```
<ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
    ...
  </ds:Signature>
```

### Statut

L’élément `Status` indique si l’authentification a abouti ou échoué. Il inclut l’élément `StatusCode`, qui lui-même contient un code ou un ensemble de codes imbriqués représentant l’état de la demande. Il inclut également l’élément `StatusMessage`, qui contient des messages d’erreur personnalisés générés pendant le processus d’authentification.

<!-- TODO: Add a authentication protocol error reference -->

Voici une réponse SAML à une tentative d’ouverture de session infructueuse.

```
<samlp:Response ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Requester">
      <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:RequestUnsupported" />
    </samlp:StatusCode>
    <samlp:StatusMessage>AADSTS75006: An error occurred while processing a SAML2 Authentication request. AADSTS90011: The SAML authentication request property 'NameIdentifierPolicy/SPNameQualifier' is not supported.
Trace ID: 66febed4-e737-49ff-ac23-464ba090d57c
Timestamp: 2013-03-18 08:49:24Z</samlp:StatusMessage>
  </samlp:Status>
```

### Assertion

Outre les éléments `ID`, `IssueInstant` et `Version`, Azure Active Directory définit les éléments suivants dans l’élément `Assertion` de la réponse.

#### Émetteur

Cet élément est défini sur `https://sts.windows.net/<TenantIDGUID>/`, où <TenantIDGUID> correspond à l’ID client du client Azure AD.

```
<Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

#### Signature

Azure AD signe l’assertion en réponse à une ouverture de session réussie. L’élément `Signature` contient une signature numérique que le service cloud peut utiliser pour authentifier la source afin de vérifier l’intégrité de l’assertion.

Pour générer cette signature numérique, Azure AD utilise la clé de signature spécifiée dans l’élément `IDPSSODescriptor` de son document de métadonnées.

```
<ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
      digital_signature_here
    </ds:Signature>
```

#### Objet

Spécifie le principal qui fait l’objet des instructions contenues dans l’assertion. Il contient un élément `NameID` qui représente l’utilisateur authentifié. La valeur `NameID` est un identificateur ciblé qui est dirigé uniquement vers le fournisseur de services visé pour le jeton. Elle est persistante : elle peut être révoquée, mais n’est jamais réaffectée. Elle est également opaque, car elle ne révèle rien sur l’utilisateur et ne peut pas être utilisée comme identificateur pour les requêtes d’attribut.

L’attribut `Method` de l’élément `SubjectConfirmation` est toujours défini sur `urn:oasis:names:tc:SAML:2.0:cm:bearer`.

```
<Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
</Subject>
```

#### Conditions

Cet élément spécifie les conditions qui définissent l’usage acceptable des assertions SAML.

```
<Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
</Conditions>
```

Les attributs `NotBefore` et `NotOnOrAfter` spécifient l’intervalle pendant lequel l’assertion est valide.

- La valeur de l’attribut `NotBefore` est égale à la valeur de l’attribut `IssueInstant` de l’élément `Assertion`, ou légèrement supérieure (moins d’une seconde). Azure AD ne tient pas compte des différences de temps entre sa propre horloge et celle du service cloud (fournisseur de services) et n’ajoute pas de mémoire tampon à cette durée.
- La valeur de l’attribut `NotOnOrAfter` est de 70 minutes de plus que la valeur de l’attribut `NotBefore`.

#### Audience

Contient un URI qui identifie une audience visée. Azure AD définit la valeur de cet élément sur la valeur de l’élément `Issuer` de l’attribut `AuthnRequest` qui a lancé la session. Pour évaluer la valeur `Audience`, utilisez la valeur de `App ID URI` spécifiée lors de l’inscription de l’application.

```
<AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
</AudienceRestriction>
```

Comme la valeur `Issuer`, la valeur `Audience` doit correspondre exactement à un des noms de principaux du service qui représentent le service cloud dans Azure AD. Toutefois, si la valeur de l’élément `Issuer` n’est pas une valeur d’URI, la valeur `Audience` contenue dans la réponse correspond à la valeur `Issuer` précédée de `spn:`.

#### AttributeStatement

Contient les revendications sur l’objet ou l’utilisateur. L’extrait suivant contient un exemple d’élément `AttributeStatement`. Les points de suspension indiquent que l’élément peut contenir plusieurs attributs et valeurs d’attribut.

```
<AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
</AttributeStatement>
```		

- **Revendication Name** : la valeur de l’attribut `Name` (`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`) est le nom d’utilisateur principal de l’utilisateur authentifié, tel que `testuser@managedtenant.com`.
- **Réclamation ObjectIdentifier** : la valeur de l’attribut `ObjectIdentifier` (`http://schemas.microsoft.com/identity/claims/objectidentifier`) est l’élément `ObjectId` de l’objet de répertoire représentant l’utilisateur authentifié dans Azure AD. `ObjectId` est un identifiant sûr, immuable, globalement unique et réutilisable de l’utilisateur authentifié.

#### AuthnStatement

Cet élément déclare que le sujet de l’assertion a été authentifié par un moyen précis à un moment donné.

- L’attribut `AuthnInstant` spécifie l’heure à laquelle l’utilisateur s’est authentifié auprès d’Azure AD.
- L’élément `AuthnContext` spécifie le contexte d’authentification utilisé pour authentifier l’utilisateur.

```
<AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
</AuthnStatement>
```

<!---HONumber=AcomDC_0608_2016-->