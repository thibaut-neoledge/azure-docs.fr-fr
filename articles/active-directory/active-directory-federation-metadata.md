<properties
   pageTitle="Métadonnées de fédération"
   description="Description des points de terminaison de métadonnées et du contenu du document de métadonnées que les services qui acceptent des jetons Azure AD doivent utiliser."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="05/30/2015"
   ms.author="mbaldwin"/>

# Métadonnées de fédération
Azure Active Directory (Azure AD) publie un document de métadonnées de fédération pour les services qui sont configurés pour accepter les jetons de sécurité émis par Azure Active Directory. Le format de document des métadonnées de fédération est décrit en [langage WS-Federation (Web Services Federation Language) version 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html), qui étend les [métadonnées pour la spécification SAML (Security Assertion Markup Language) OASIS V2.0](http://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf).

Cette rubrique répertorie les points de terminaison de métadonnées et explique le contenu du document de métadonnées que les services qui acceptent des jetons Azure AD doivent utiliser.

##Points de terminaison de métadonnées spécifiques ou indépendants du client

Azure AD publie des points de terminaison spécifiques et indépendants du client. Les points de terminaison spécifiques du client sont conçus pour un client donné. Les métadonnées de fédération spécifiques du client contiennent des informations sur le client, y compris des informations sur l’émetteur et le point de terminaison propres au client. Les applications qui limitent l’accès à un seul client utilisent des points de terminaison spécifiques du client.

Les points de terminaison indépendants du client fournissent des informations qui sont communes à tous les clients Azure AD. Ces informations s’appliquent aux clients hébergés sur *login.windows.net*. Elles sont partagées entre les clients. Les points de terminaison indépendants du client sont recommandés pour les applications mutualisées, car ils ne sont pas associés à un client particulier.

## Points de terminaison de métadonnées de fédération

Azure AD publie des métadonnées de fédération dans *https://login.windows.net/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml*,où la valeur de <TenantDomainName> peut être « commun » ou une valeur spécifique du client. Les points de terminaison étant adressables, vous pouvez accéder à cette adresse pour afficher les métadonnées de fédération d’un client.

Pour les **points de terminaison spécifiques du client**, le <TenantDomainName> peut être de l’un des types suivants :

- Un nom de domaine enregistré d’un client Azure AD, par exemple : contoso.onmicrosoft.com.

- L’ID client non modifiable du domaine, tel que 72f988bf-86f1-41af-91ab-2d7cd011db45.

Pour les **points de terminaison indépendants du client**, le <TenantDomainName> est **commun**. Ce nom indique que seuls les éléments de métadonnées de fédération communs à tous les clients Azure AD sont hébergés à l’adresse login.windows.net.

Par exemple, un point de terminaison spécifique d’un client peut être *https://login.windows.net/contoso.onmicrosoft.comFederationMetadata/2007-06/FederationMetadata.xml*. Le point de terminaison indépendant du client est *https://login.windows.net/common/FederationMetadata/2007-06/FederationMetadata.xml*.

## Contenu des métadonnées de fédération

La section suivante fournit les informations nécessaires aux services qui utilisent des jetons émis par Azure AD.

### EntityID

L’élément **EntityDescriptor** contient un attribut **EntityID**. La valeur de l’attribut **EntityID** représente l’émetteur, autrement dit, le service de jeton de sécurité (STS) qui a émis le jeton. Il est important de valider l’émetteur pour confirmer quel client a émis le jeton.

Les métadonnées suivantes montrent un exemple d’élément spécifique du client **EntityDescriptor** avec un élément **EntityID**.

    <EntityDescriptor 
    xmlns="urn:oasis:names:tc:SAML:2.0:metadata" 
    ID="_b827a749-cfcb-46b3-ab8b-9f6d14a1294b" 
    entityID="https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/">

L’élément **EntityID** du point de terminaison indépendant du client fournit un modèle qui peut servir à générer une valeur **EntityID** propre au client. Remplacez le littéral « {client} » du point de terminaison indépendant du client par la valeur de la revendication **TenantID** à partir d’un jeton. La valeur qui en résulte est identique à celle de l’émetteur du jeton. Cette stratégie permet à une application mutualisée de valider l’émetteur pour un client donné.

Les métadonnées suivantes montrent un exemple d’élément **EntityID** indépendant du client. Dans cet élément, {client} est un littéral, et non un espace réservé.

    <EntityDescriptor 
    xmlns="urn:oasis:names:tc:SAML:2.0:metadata" 
    ID="="_0e5bd9d0-49ef-4258-bc15-21ce143b61bd" 
    entityID="https://sts.windows.net/{tenant}/">

### Certificats de signature de jeton
Lorsqu’un service reçoit un jeton émis par un client Azure AD, la signature du jeton doit être validée avec une clé de signature qui est publiée dans le document des métadonnées de fédération.

Les métadonnées de fédération incluent la partie publique des certificats utilisés par les clients pour la signature de jetons. Les octets bruts du certificat s’affichent dans l’élément **KeyDescriptor**. Le certificat de signature de jeton est valide pour la signature uniquement lorsque la valeur de l’attribut **use** est **signing**.

Un document des métadonnées de fédération publié par Azure AD peut avoir plusieurs clés de signature, par exemple lorsqu’Azure AD se prépare à mettre à jour le certificat de signature. Lorsqu’un document des métadonnées de fédération comprend plusieurs certificats, un service qui valide les jetons doit prendre en charge tous les certificats du document.

Les métadonnées suivantes montrent un exemple d’élément **KeyDescriptor** avec une clé de signature.

    <KeyDescriptor use="signing">
    <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
    <X509Data>
    <X509Certificate>
    MIIDPjCCAiqgAwIBAgIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTIwNjA3MDcwMDAwWhcNMTQwNjA3MDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEArCz8Sn3GGXmikH2MdTeGY1D711EORX/lVXpr+ecGgqfUWF8MPB07XkYuJ54DAuYT318+2XrzMjOtqkT94VkXmxv6dFGhG8YZ8vNMPd4tdj9c0lpvWQdqXtL1TlFRpD/P6UMEigfN0c9oWDg9U7Ilymgei0UXtf1gtcQbc5sSQU0S4vr9YJp2gLFIGK11Iqg4XSGdcI0QWLLkkC6cBukhVnd6BCYbLjTYy3fNs4DzNdemJlxGl8sLexFytBF6YApvSdus3nFXaMCtBGx16HzkK9ne3lobAwL2o79bP4imEGqg+ibvyNmbrwFGnQrBc1jTF9LyQX9q+louxVfHs6ZiVwIDAQABo2IwYDBeBgNVHQEEVzBVgBCxDDsLd8xkfOLKm4Q/SzjtoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAA4IBAQAkJtxxm/ErgySlNk69+1odTMP8Oy6L0H17z7XGG3w4TqvTUSWaxD4hSFJ0e7mHLQLQD7oV/erACXwSZn2pMoZ89MBDjOMQA+e6QzGB7jmSzPTNmQgMLA8fWCfqPrz6zgH+1F1gNp8hJY57kfeVPBiyjuBmlTEBsBlzolY9dd/55qqfQk6cgSeCbHCy/RU/iep0+UsRMlSgPNNmqhj5gmN2AFVCN96zF694LwuPae5CeR2ZcVknexOWHYjFM0MgUSw0ubnGl0h9AJgGyhvNGcjQqu9vd1xkupFgaN+f7P3p3EVN5csBg5H94jEcQZT7EKeTiZ6bTrpDAnrr8tDCy8ng
    </X509Certificate>
    </X509Data>
    </KeyInfo>
    </KeyDescriptor>

L’élément **KeyDescriptor** apparaît dans deux emplacements du document des métadonnées de fédération : dans la section spécifique de WS-Federation et dans la section propre à SAML. Les certificats publiés dans les deux sections sont identiques.

Dans la section WS-Federation, un lecteur de métadonnées WS-Federation lit les certificats d’un élément **RoleDescriptor** avec le type **SecurityTokenServiceType**.

Les métadonnées suivantes montrent un exemple d’élément **RoleDescriptor**.

    <RoleDescriptor xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:fed="http://docs.oasis-open.org/wsfed/federation/200706"
    xsi:type="fed:SecurityTokenServiceType"protocolSupportEnumeration="http://docs.oasis-open.org/wsfed/federation/200706">`

Dans la section SAML, un lecteur de métadonnées WS-Federation lit les certificats d’un élément **IDPSSODescriptor**.

Les métadonnées suivantes montrent un exemple d’élément **IDPSSODescriptor**.

    <IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">

Il n’existe aucune différence de format entre les certificats spécifiques du client et ceux indépendants du client.

### URL de point de terminaison WS-Federation
Les métadonnées de fédération incluent l’URL qu’utilise Azure AD pour la connexion et la déconnexion dans le protocole WS-Federation. Ce point de terminaison s’affiche dans l’élément **PassiveRequestorEndpoint**.

Les métadonnées suivantes montrent un exemple d’élément **PassiveRequestorEndpoint** pour un point de terminaison spécifique du client.

    <fed:PassiveRequestorEndpoint>
    <EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
    <Address>
    https://login.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/wsfed
    </Address>
    </EndpointReference>
    </fed:PassiveRequestorEndpoint>

Pour le point de terminaison indépendant du client, l’URL WS-Federation apparaît dans le point de terminaison WS-Federation, comme illustré dans l’exemple suivant.

    <fed:PassiveRequestorEndpoint>
    <EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
    <Address>
    https://login.windows.net/common/wsfed
    </Address>
    </EndpointReference>
    </fed:PassiveRequestorEndpoint>

### URL de point de terminaison de protocole SAML

Les métadonnées de fédération incluent l’URL qu’utilise Azure AD pour la connexion et la déconnexion dans le protocole SAML 2.0. Ces points de terminaison s’affichent dans l’élément **IDPSSODescriptor**.

Les URL de connexion et de déconnexion s’affichent dans les éléments **SingleSignOnService** et **SingleLogoutService**. Les métadonnées suivantes montrent un exemple d’élément **PassiveResistorEndpoint** pour un point de terminaison spécifique du client.

    <IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    …
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.windows.net/contoso.onmicrosoft.com/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https:// login.windows.net/contoso.onmicrosoft.com /saml2" />
    </IDPSSODescriptor>

De la même façon, les points de terminaison communs du protocole SAML 2.0 sont publiés dans les métadonnées de fédération indépendantes du client, comme illustré dans l’exemple suivant.

    <IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    …
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.windows.net/common/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.windows.net/common/saml2" />
    </IDPSSODescriptor>

## Voir aussi
[Protocoles d’authentification d’Azure Active Directory](active-directory-authentication-protocols.md)

[Guide du développeur Azure Active Directory](active-directory-developers-guide.md)

<!---HONumber=July15_HO3-->