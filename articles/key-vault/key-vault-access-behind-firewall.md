<properties
	pageTitle="Accès à un coffre de clés derrière le pare-feu | Microsoft Azure"
	description="Découvrez comment accéder à un coffre de clés à partir d’une application derrière un pare-feu"
	services="key-vault"
	documentationCenter=""
	authors="amitbapat"
	manager="mbaldwin"
	tags="azure-resource-manager"/>

<tags
	ms.service="key-vault"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="09/13/2016"
	ms.author="ambapat"/>

# Accès à un coffre de clés derrière un pare-feu
### Q : mon application cliente de coffre de clés doit se trouver derrière un pare-feu, quel(e)s ports/hôtes/adresses IP dois-je ouvrir pour autoriser l’accès au coffre de clés ?

Pour accéder à un coffre de clés, votre application cliente de coffre de clés doit être en mesure d’accéder à plusieurs points de terminaison pour différentes fonctionnalités.

- Authentification (via Azure Active Directory)
- Gestion du coffre de clés (comprenant la création/lecture/mise à jour/suppression, ainsi que la définition de stratégies d’accès) par le biais d’Azure Resource Manager
- L’accès et la gestion des objets (clés et secrets) stockés dans le coffre de clés lui-même, passe par le point de terminaison spécifique au coffre de clés (par exemple, [https://yourvaultname.vault.azure.net](https://yourvaultname.vault.azure.net)).

Il existe des variantes selon votre configuration et l’environnement.

## Ports

Tout le trafic vers le coffre de clés pour chacune des 3 fonctions (authentification, gestion et accès au plan de données) passe par le protocole HTTPS: Port 443. Pour les CRL cependant, il faut compter occasionnellement sur un trafic HTTP (port 80). Les clients qui prennent en charge le protocole OCSP ne doivent pas atteindre les CRL, mais peuvent occasionnellement atteindre [http://cdp1.public-trust.com/CRL/Omniroot2025.crl](http://cdp1.public-trust.com/CRL/Omniroot2025.crl).

## Authentification

L’application cliente de coffre de clés devra accéder aux points de terminaison Azure Active Directory pour l’authentification. Le point de terminaison utilisé dépend de la configuration du client AAD et le type de principal --principal d’utilisateur, principal du service et type de compte, par exemple, compte Microsoft ou ID d’organisation.

| Type de principal | Point de terminaison:port |
|----------------|---------------|
| Utilisateur utilisant un compte Microsoft<br> (par exemple, user@hotmail.com) | **International :**<br> login.microsoftonline.com:443<br><br> **Azure Chine :**<br> login.chinacloudapi.cn:443<br><br>**Azure gouvernement américain :**<br> connexion-us.microsoftonline.com:443<br><br>**Azure Allemagne :**<br> login.microsoftonline.de:443<br><br> et <br>login.live.com:443 |
| Principal d’utilisateur/de service avec ID d’organisation avec AAD (par exemple, user@contoso.com) | **International :**<br> login.microsoftonline.com:443<br><br> **Azure Chine :**<br> login.chinacloudapi.cn:443<br><br>**Azure gouvernement américain :**<br> login-us.microsoftonline.com:443<br><br>**Azure Allemagne :**<br> login.microsoftonline.de:443 |
| Principal d’utilisateur/de service avec ID d’organisation+AD FS ou autre point de terminaison fédéré (par exemple, user@contoso.com) | Tous les points de terminaison ci-dessus pour l’ID d’organisation + AD FS ou autres points de terminaison fédérés |

D’autres scénarios complexes sont possibles. Pour plus d’informations, reportez-vous à [Azure Active Directory Authentication Flow (Flux d’authentification d’Azure Active Directory)](/documentation/articles/active-directory-authentication-scenarios/), [Intégration d’applications dans Azure Active Directory](/documentation/articles/active-directory-integrating-applications/) et [Protocoles d’authentification Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx).

## Gestion de coffre de clés

Pour la gestion d’un coffre de clés (CRUD et définition de la stratégie d’accès), l’application cliente de coffre de clés doit accéder au point de terminaison d’Azure Resource Manager.

| Type d’opération | Point de terminaison:port |
|----------------|---------------|
| Opérations du plan de contrôle de coffre de clés<br> via Azure Resource Manager | **International :**<br> management.azure.com:443<br><br> **Azure Chine :**<br> management.chinacloudapi.cn:443<br><br> **Azure gouvernement américain :**<br> management.usgovcloudapi.net:443<br><br> **Azure Allemagne :**<br> management.microsoftazure.de:443 |
| API Graph Azure Active Directory | **International :**<br> graph.windows.net:443<br><br> **Azure Chine :**<br> graph.chinacloudapi.cn:443<br><br> **Azure gouvernement américain :**<br> graph.windows.net:443<br><br> **Azure Allemagne :**<br> graph.cloudapi.de:443 |

## Opérations du coffre de clés

Pour toutes les opérations de gestion et de chiffrement d’objet (clés et secrets), le client de coffre de clés doit accéder au point de terminaison du coffre de clés. Le suffixe DNS du point de terminaison varie en fonction de l’emplacement de votre coffre de clés. Le point de terminaison du coffre de clés est au format : < nom du coffre >. < suffixe-dns-spécifique-à-la-région> comme indiqué dans le tableau ci-dessous.

| Type d’opération | Point de terminaison:port |
|----------------|---------------|
| Opérations telles que le chiffrement sur les clés, création/lecture/mise à jour/suppression de clés et de secrets, définition/obtention de mots-clés et autres attributs sur objets de coffre de clés (clés/secrets) | **International :**<br> & lt ; nom du coffre & gt ;. vault.azure .net : 443<br><br> **Azure Chine :**<br> & lt ; nom du coffre & gt ;. Vault.Azure.CN:443<br><br> **Azure gouvernement américain :**<br> & lt ; nom du coffre & gt ;. vault.usgovcloudapi .net : 443<br><br> **Azure Allemagne :**<br> & lt ; nom du coffre & gt ;. Vault.microsoftazure.de:443 |

## Plages d’adresse IP

Le service de coffre de clés utilise d’autres ressources Azure telles que l’infrastructure PaaS, de sorte qu’il n’est pas possible de fournir une plage spécifique des adresses IP qu’auront les points de terminaison du service de coffre de clés à un moment donné. Toutefois, si votre pare-feu prend en charge uniquement des plages d’adresses IP, veuillez vous reporter au document [Plages IP des centres de données Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653) document. Pour l’authentification et l’identité (Azure Active Directory), votre application doit être en mesure de se connecter aux points de terminaison décrits dans [Authentication and identity Addresses (Authentification et identité des adresses)](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

## Étapes suivantes

- Pour toute question concernant le coffre de clés, rendez-vous sur les [forums sur les coffres de clés Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)

<!---HONumber=AcomDC_0921_2016-->