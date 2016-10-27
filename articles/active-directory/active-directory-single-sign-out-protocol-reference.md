<properties
    pageTitle="Protocole SAML de déconnexion unique Azure | Microsoft Azure"
    description="Cet article décrit le protocole SAML de déconnexion unique dans Azure Active Directory"
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
    ms.date="10/03/2016"
    ms.author="priyamo"/>



# <a name="single-sign-out-saml-protocol"></a>Protocole SAML de déconnexion unique

Azure Active Directory (Azure AD) prend en charge le profil de déconnexion unique du navigateur web SAML 2.0. Pour que le processus de déconnexion unique puisse fonctionner correctement, Azure AD doit enregistrer ses URL de métadonnées au cours de l’inscription de l’application. Azure AD obtient l’URL de déconnexion et la clé de signature du service cloud à partir des métadonnées. Azure AD utilise la clé de signature pour vérifier la signature sur le LogoutRequest entrant, et utilise l’élément LogoutURL pour rediriger les utilisateurs une fois qu’ils sont déconnectés.

Si le service cloud ne prend pas en charge un point de terminaison des métadonnées, le développeur doit contacter le support Microsoft pour obtenir l’URL de déconnexion et la clé de signature une fois l’application enregistrée.

Ce schéma illustre le workflow du processus de déconnexion unique Azure AD.

![Workflow de déconnexion unique](media/active-directory-single-sign-out-protocol-reference/active-directory-saml-single-sign-out-workflow.png)

## <a name="logoutrequest"></a>LogoutRequest

Le service cloud envoie un message `LogoutRequest` à Azure AD pour indiquer qu’une session a été arrêtée. L’extrait suivant illustre un exemple d’élément `LogoutRequest` .

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### <a name="logoutrequest"></a>LogoutRequest

L’élément `LogoutRequest` envoyé à Azure AD requiert les attributs suivants :

- `ID` : identifie la demande de déconnexion. La valeur `ID` ne doit pas commencer par un chiffre. La méthode classique consiste à ajouter **id** à la représentation sous forme de chaîne d’un GUID.

- `Version` : définissez la valeur de cet élément sur **2.0**. Cette valeur est obligatoire.

- `IssueInstant` : chaîne `DateTime` associée à une valeur UTC et ayant le [format aller-retour (« o »)](https://msdn.microsoft.com/library/az4se3k1.aspx). Azure Active Directory attend une valeur de ce type, mais ne l’applique pas.

- Les attributs `Consent`, `Destination`, `NotOnOrAfter` et `Reason` sont ignorés s’ils sont inclus dans un élément `LogoutRequest`.

### <a name="issuer"></a>Émetteur

L’élément `Issuer` dans `LogoutRequest` doit correspondre exactement à l’un des **ServicePrincipalNames** du service cloud dans Azure AD. En règle générale, il est défini sur **l’URI ID d’application** spécifié au moment de l’inscription de l’application.

### <a name="nameid"></a>NameID

La valeur de l’élément `NameID` doit correspondre exactement à la valeur `NameID` de l’utilisateur déconnecté.
## <a name="logoutresponse"></a>LogoutResponse

Azure AD envoie une `LogoutResponse` en réponse à un élément `LogoutRequest`. L’extrait suivant illustre un exemple d’élément `LogoutResponse`.

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>LogoutResponse

Azure AD définit les valeurs `ID`, `Version` et `IssueInstant` dans l’élément `LogoutResponse`. Il définit également l’élément `InResponseTo` sur la valeur de l’attribut `ID` de l’élément `LogoutRequest` qui a obtenu la réponse.

### <a name="issuer"></a>Émetteur

Azure AD définit cette valeur sur `https://login.microsoftonline.com/<TenantIdGUID>/`, où <TenantIdGUID> correspond à l’ID client du client Azure AD.

Pour évaluer la valeur de l’élément `Issuer` , utilisez la valeur de **l’URI ID d’application** spécifiée lors de l’inscription de l’application.

### <a name="status"></a>État

Azure AD utilise l’élément `StatusCode` dans l’élément `Status` pour indiquer la réussite ou l’échec de la déconnexion. En cas d’échec de la tentative de déconnexion, l’élément `StatusCode` peut également contenir des messages d’erreur personnalisés.



<!--HONumber=Oct16_HO2-->


