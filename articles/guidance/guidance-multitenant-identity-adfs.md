<properties
   pageTitle="Fédération avec les services AD FS d’un client | Microsoft Azure"
   description="Comment effectuer la fédération avec les services AD FS d’un client dans une application mutualisée"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# Fédération avec les services AD FS d’un client

Cet article fait [partie d’une série] qui s’accompagne d’un [exemple d’application].

Il décrit comment une application SaaS mutualisée peut prendre en charge l’authentification via Active Directory Federation Services (AD FS), afin de se fédérer avec les services AD FS d’un client.

## Vue d'ensemble

Azure Active Directory (Azure AD) vous permet de connecter des utilisateurs à partir de clients Azure AD, y compris de clients Office 365 et Dynamics CRM Online. Mais qu’en est-il des clients qui utilisent Active Directory localement sur un intranet d’entreprise ?

Pour eux, l’une des possibilités consiste à synchroniser leur Active Directory local avec Azure AD, à l’aide d’[Azure AD Connect]. Cependant, certains clients sont parfois dans l’incapacité d’utiliser cette approche, à cause d’une stratégie informatique de l’entreprise ou d’autres raisons. Dans ce cas, l’autre possibilité consiste à assurer une fédération à l’aide d’Active Directory Federation Services (AD FS).

Pour ce faire :

-	Le client doit disposer d’une batterie de serveurs AD FS sur Internet.
-	Le fournisseur SaaS déploie sa propre batterie de serveurs AD FS.
-	Le client et le fournisseur SaaS doivent configurer l’[approbation de fédération]. Ce processus est manuel.

La relation d’approbation comprend trois rôles principaux :

-	Les services AD FS du client sont le [partenaire du compte], responsable de l’authentification des utilisateurs à partir de l’AD du client, et de la création de jetons de sécurité en fonction des revendications des utilisateurs.
-	Les services AD FS du fournisseur SaaS sont le [partenaire de ressources], qui approuve le partenaire du compte et reçoit les revendications des utilisateurs.
-	L’application est configurée comme une partie de confiance (RP) dans les services AD FS du fournisseur SaaS.

	![Approbation de fédération](media/guidance-multitenant-identity/federation-trust.png)

> [AZURE.NOTE] Dans cet article, nous partons du principe que l’application utilise OpenID Connect comme protocole d’authentification. L’autre possibilité consiste à utiliser WS-Federation.

> Avec OpenID Connect, le fournisseur SaaS doit utiliser AD FS 4.0 dans Windows Server 2016, qui est actuellement en version bêta. AD FS 3.0 ne prend pas en charge OpenID Connect.

> ASP.NET Core 1.0 n’assure pas une prise en charge immédiate de WS Federation.

Pour obtenir un exemple d’utilisation de WS-Federation avec ASP.NET 4, consultez https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation.

## Flux d’authentification

1.	Lorsque l’utilisateur clique sur « Connexion », l’application le redirige vers un point de terminaison OpenID Connect sur les services AD FS du fournisseur SaaS.
2.	L’utilisateur entre son nom d’utilisateur (« `alice@corp.contoso.com` »). Les services AD FS utilisent la découverte du domaine d’accueil pour pointer vers les services AD FS du client, où l’utilisateur entre ses informations d’identification.
3.	Les services AD FS du client envoient les revendications des utilisateurs aux services AD FS du fournisseur SaaS, à l’aide de WS-Federation (ou de SAML).
4.	Les revendications transitent des services AD FS vers l’application, à l’aide d’OpenID Connect. Cela nécessite un changement de protocole (WS-Federation).

## Limites

Au moment de la rédaction de cet article, l’application obtient un ensemble limité de revendications dans l’id\_token OpenID. AD FS 4.0 étant encore en version préliminaire, cette liste est susceptible de changer. Mais si votre application requiert des revendications supplémentaires, cela n’est actuellement pas possible et il est important de le noter avant de continuer.

Actuellement, les revendications suivantes sont envoyées dans l’id\_token :

Revendication | Description
------|-------------
aud | Public, c’est-à-dire l’application pour laquelle les revendications ont été émises.
AuthenticationInstant | [Moment d’authentification]
c\_hash | Valeur de hachage de code
exp | [Heure d’expiration]
iat | [Émis à]
iss | Émetteur. La valeur de cette revendication est toujours les services AD FS du partenaire de ressources, et non les services AD FS du client. (En d’autres termes, cette revendication identifie le fournisseur SaaS comme l’émetteur plutôt que le client.)
name | Nom d’utilisateur. Exemple : `john@corp.fabrikam.com`.
nameidentifier | [Identificateur de nom]
nonce | Valeur à usage unique de session
upn | Nom d’utilisateur principal. Exemple : john@corp.fabrikam.com

En particulier, notez que la revendication « iss » ne spécifie pas les services AD FS du client. Pour connaître le domaine du client, vous devez examiner l’UPN. Le reste de cet article décrit comment définir la relation d’approbation entre la partie de confiance (l’application) et le partenaire du compte (le client).

## Déploiement des services AD FS

Le fournisseur SaaS peut déployer les services AD FS sur site ou sur des machines virtuelles Azure. Pour des raisons de sécurité et de disponibilité, les instructions suivantes sont importantes :

-	Déployez au moins deux serveurs AD FS et deux serveurs proxy AD FS pour optimiser la disponibilité des services AD FS.
-	Les contrôleurs de domaine et les serveurs AD FS ne doivent jamais être exposés directement à Internet et doivent être accessibles directement dans un réseau virtuel.
-	Les proxys d’application web (les anciens proxys AD FS) doivent être utilisés pour publier des serveurs AD FS sur Internet.

La configuration d’une topologie similaire dans Azure nécessite l’utilisation de réseaux virtuels, de groupes de sécurité réseau, de machines virtuelles Azure et de groupes à haute disponibilité. Pour plus d’informations, consultez la page [Recommandations en matière de déploiement de Windows Server Active Directory sur des machines virtuelles Azure](MSDN).

## Configurer l’application pour utiliser l’authentification OpenID Connect avec les services AD FS

Le fournisseur SaaS doit activer OpenID Connect entre l’application et les services AD FS. Pour ce faire, ajoutez un groupe d’applications dans les services AD FS. Vous trouverez des instructions détaillées dans ce [billet de blog], sous « Setting up a Web App for OpenId Connect sign in AD FS » (Configuration d’une application web pour une connexion via Open ID Connect dans AD FS). Ensuite, configurez l’intergiciel OpenID Connect. Le point de terminaison des métadonnées est `https://domain/adfs/.well-known/openid-configuration`, où « domain » est le domaine des services AD FS du fournisseur SaaS.

En général, vous pouvez l’associer à d’autres points de terminaison OpenID Connect (comme AAD). Vous aurez besoin de deux boutons de connexion différents (ou identiques mais différenciés par un moyen ou un autre), afin que l’utilisateur soit redirigé vers le point de terminaison d’authentification approprié.

## Configurer le partenaire de ressources des services AD FS

Le fournisseur SaaS doit effectuer les opérations suivantes pour chaque client qui souhaite se connecter via les services AD FS :

1.	Ajouter une approbation de fournisseur de revendications.
2.	Ajouter des règles de revendications.
3.	Activer la découverte du domaine d’accueil.

Voyons ces étapes plus en détail.

### Ajouter une approbation de fournisseur de revendications

1.	Dans Gestionnaire de serveur, cliquez sur **Outils** et sélectionnez **Gestion AD FS**.
2.	Dans l’arborescence de la console, sous **AD FS**, cliquez avec le bouton droit **Approbations de fournisseur de revendications**. Sélectionnez **Ajouter une approbation de fournisseur de revendications**.
3.	Cliquez sur **Démarrer** pour démarrer l’assistant.
4.	Sélectionnez l’option « Importer des données sur le fournisseur de revendications publiées en ligne ou sur un réseau local ». Entrez l’URI du point de terminaison des métadonnées de fédération du client. (Exemple : `https://contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`.) C’est ce que vous devez obtenir du client.
5.	Parcourez le reste des étapes de l’Assistant en acceptant les valeurs par défaut.

### Modifier des règles de revendications

1.	Cliquez sur l’approbation de fournisseur de revendications ajoutée, puis sélectionnez **Modifier des règles de revendications**.
2.	Cliquez sur **Ajouter une règle**.
3.	Sélectionnez « Transférer ou filtrer une revendication entrante » et cliquez sur **Suivant**. ![Assistant Ajouter une règle de revendication de transformation](media/guidance-multitenant-identity/edit-claims-rule.png)
4.	Entrez le nom de la règle.
5.	Sous « Type de revendication entrante », sélectionnez **UPN**.
6.	Sélectionnez « Transférer toutes les valeurs de revendication ». ![Assistant Ajouter une règle de revendication de transformation](media/guidance-multitenant-identity/edit-claims-rule2.png)
7.	Cliquez sur **Terminer**.
8.	Répétez les étapes 2 à 7 et spécifiez **Type de revendication d’ancrage** comme type de revendication entrante.
9.	Cliquez sur **OK** pour terminer l’assistant.

### Activer la découverte du domaine d’accueil
Exécutez le script PowerShell suivant :

```
Set-ADFSClaimsProviderTrust -TargetName "name" -OrganizationalAccountSuffix @("suffix")
```

où « name » est le nom convivial de l’approbation de fournisseur de revendications, et « suffix » est le suffixe UPN de l’Active Directory du client (par exemple, « corp.fabrikam.com »).

Avec cette configuration, les utilisateurs peuvent saisir leur compte professionnel et AD FS sélectionne automatiquement le fournisseur de revendications correspondant. Consultez la page [Personnalisation des pages de connexion AD FS], dans la section « Configurer le fournisseur d’identité pour utiliser certains suffixes d’adresse de messagerie ».

## Configuration du partenaire de compte AD FS

Le client doit effectuer les opérations suivantes :

1.	Ajouter une approbation de partie de confiance.
2.	Ajouter des règles de revendications.

### Ajouter l’approbation de la partie de confiance

1.	Dans Gestionnaire de serveur, cliquez sur **Outils** et sélectionnez **Gestion AD FS**.
2.	Dans l’arborescence de la console, sous **AD FS**, cliquez avec le bouton droit **Approbations de la partie de confiance**. Sélectionnez **Ajouter une approbation de partie de confiance**.
3.	Sélectionnez **Prenant en charge les revendications**, puis cliquez sur **Démarrer**.
4.	Dans la page **Sélectionner la source de données** page, sélectionnez l’option « Importer des données sur le fournisseur de revendications publiées en ligne ou sur un réseau local ». Entrez l’URI du point de terminaison des métadonnées de fédération du fournisseur SaaS. ![Assistant Ajouter une approbation de partie de confiance](media/guidance-multitenant-identity/add-rp-trust.png)
5.	Dans la page **Indiquer le nom complet**, entrez le nom de votre choix.
6.	Dans la page **Choisir la stratégie de contrôle d’accès**, choisissez une stratégie. Vous pouvez autoriser tout le monde dans l’organisation ou choisir un groupe de sécurité. ![Assistant Ajouter une approbation de partie de confiance](media/guidance-multitenant-identity/add-rp-trust2.png)
7.	Cliquez sur **Suivant** pour terminer l’assistant.

### Ajouter des règles de revendications

1.	Cliquez avec le bouton droit de la souris sur l’approbation de partie de confiance ajoutée, puis sélectionnez **Modifier la stratégie d’émission de revendications**.
2.	Cliquez sur **Ajouter une règle**.
3.	Sélectionnez « Envoyer les attributs LDAP comme des revendications » et cliquez sur **Suivant**. ![Assistant Ajouter une règle de revendication de transformation](media/guidance-multitenant-identity/add-claims-rules.png)
4.	Entrez le nom de la règle (par exemple, « UPN »).
5.	Sous **Magasin d’attributs**, sélectionnez **Active Directory**.
6.	Dans la section **Mappage des attributs LDAP** :
  -	Sous **Attribut LDAP**, sélectionnez **User-Principal-Name**.
  - Sous **Type de revendication sortante**, sélectionnez **UPN**. ![Assistant Ajouter une règle de revendication de transformation](media/guidance-multitenant-identity/add-claims-rules2.png)
7.	Cliquez sur **Terminer**.
8.	Cliquez de nouveau sur **Ajouter une règle**.
9.	Sélectionnez « Envoyer les revendications à l’aide d’une règle personnalisée », puis cliquez sur **Suivant**.
10.	Entrez le nom de la règle (par exemple, « Revendication d’ancrage »).
11.	Sous **Règle personnalisée**, ajoutez le code suivant :

	```
    EXISTS([Type == "http://schemas.microsoft.com/ws/2014/01/identity/claims/anchorclaimtype"])=>
      issue (Type = "http://schemas.microsoft.com/ws/2014/01/identity/claims/anchorclaimtype",
             Value = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn");
	```

    Cette règle mappe la revendication UPN à la revendication d’ancrage.

12.	Cliquez sur **Terminer**.
13.	Cliquez sur **OK** pour terminer l’assistant.

<!-- Links -->
[partie d’une série]: guidance-multitenant-identity.md
[Azure AD Connect]: ../active-directory/active-directory-aadconnect.md
[approbation de fédération]: https://technet.microsoft.com/library/cc738707.aspx
[partenaire du compte]: https://technet.microsoft.com/library/cc758463(v=ws.10).aspx
[partenaire de ressources]: https://technet.microsoft.com/library/cc758463(v=ws.10).aspx
[Moment d’authentification]: https://msdn.microsoft.com/library/system.security.claims.claimtypes.authenticationinstant%28v=vs.110%29.aspx
[Heure d’expiration]: http://tools.ietf.org/html/draft-ietf-oauth-json-web-token-25#section-4.1.4
[Émis à]: http://tools.ietf.org/html/draft-ietf-oauth-json-web-token-25#section-4.1.6
[Identificateur de nom]: https://technet.microsoft.com/library/ee913589.aspx
[Guidelines for Deploying Windows Server Active Directory on Azure Virtual Machines]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[billet de blog]: http://www.cloudidentity.com/blog/2015/08/21/OPENID-CONNECT-WEB-SIGN-ON-WITH-ADFS-IN-WINDOWS-SERVER-2016-TP3/
[Personnalisation des pages de connexion AD FS]: https://technet.microsoft.com/library/dn280950.aspx
[exemple d’application]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->