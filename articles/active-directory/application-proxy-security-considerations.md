---
title: "Considérations de sécurité lors de l’accès aux applications à distance à l’aide du proxy d’application Azure AD | Microsoft Docs"
description: "Couvre les considérations de sécurité lors de l’utilisation du proxy d’application Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 67650676117fef834ea819da2fc45ca66ed15493
ms.openlocfilehash: 1b713e015f48b044c6c9d8ff6a0d8669d4532a9f


---

# <a name="security-considerations-when-accessing-apps-remotely-using-azure-ad-application-proxy"></a>Considérations de sécurité lors de l’accès aux applications à distance à l’aide du proxy d’application Azure AD

>[!NOTE]
>Le proxy d’application est une fonctionnalité qui n’est disponible que si vous effectuez une mise à niveau vers l’édition Premium ou De base d’Azure Active Directory. Pour plus d’informations, consultez la page [Éditions d’Azure Active Directory](active-directory-editions.md).
>  

Cet article explique comment le proxy d’application Azure AD fournit un service sécurisé pour la publication et l’accès à distance de vos applications. 

Le proxy d’application Azure AD offre les avantages de sécurité suivants :

**Accès authentifié :** seules les connexions authentifiées peuvent accéder à votre réseau

* Le proxy d’application Azure AD s’appuie sur le service de jetons de sécurité Azure AD pour toutes les authentifications. Pour les applications publiées avec l’authentification préalable, aucun trafic n’est autorisé à passer par le service de proxy d’application vers environnement sans un jeton STS valide.
* L’authentification préalable, par sa nature, bloque un nombre conséquent d’attaques anonymes, car seules les identités authentifiées sont autorisées à accéder à l’application principale.

**Accès conditionnel :** appliquez des contrôles de stratégie plus riches avant que les connexions à votre réseau soient établies

* Avec l’accès conditionnel, il est possible d’affiner les restrictions sur le trafic qui est autorisé à accéder à vos applications principales. Vous pouvez définir des restrictions basées sur l’emplacement, la force de l’authentification et le profil de risque de l’utilisateur.
* Cette fonctionnalité permet d’activer des barrières supplémentaires contre les attaquants. Vous trouverez davantage d’informations sur l’accès conditionnel dans [Prise en main de l’accès conditionnel à Azure Active Directory (AD)](https://azure.microsoft.com/en-us/documentation/articles/active-directory-conditional-access-azuread-connected-apps).

**Arrêt du trafic :** tout le trafic est arrêté dans le cloud

* Le proxy d’application Azure AD est un proxy inversé, ainsi tout le trafic vers les applications du serveur principal est arrêté au niveau du service. La session peut uniquement être rétablie avec le serveur principal. Cela signifie que vos serveurs principaux ne sont pas exposés au trafic HTTP direct. Par exemple, vous pouvez arrêter plus facilement les attaques ciblées.

**Tous les accès sont sortants :** aucune connexion entrante n’a besoin d’être ouverte sur le réseau d’entreprise

* Les connecteurs Azure AD conservent des connexions sortantes vers le service de proxy d’application Azure AD. Ainsi, il n’est pas nécessaire d’ouvrir des ports de pare-feu pour les connexions entrantes. 
* Les approches traditionnelles nécessitent un DMZ et l’ouverture d’un accès à des connexions non authentifiées sur le périmètre du réseau. Il était ainsi nécessaire d’apporter des investissements supplémentaires significatifs en produits WAF pour analyser le trafic et offrir des protections supplémentaires à l’environnement. Avec le proxy d’application, vous pouvez éviter ce scénario. Vous pouvez même envisager de vous passer de votre DMZ, car toutes les connexions sont sortantes et sur un canal sécurisé.

**Analyse de sécurité et mécanisme intelligent basé sur le langage machine :** une protection de sécurité de pointe

* La protection d’identité d’Azure AD avec un mécanisme intelligent basé sur l’apprentissage de flux de données à partir de notre département Digital Crimes Unit et du Centre de réponse aux problèmes de sécurité Microsoft. Ensemble, nous identifions de façon proactive les comptes compromis et offrons une protection en temps réel contre les connexions à haut risque. Nous prenons en compte divers facteurs, dont l’accès à partir d’appareils infectés et à travers des réseaux anonymes, ainsi qu’à partir d’emplacements atypiques et peu probables.
* Nombre de ces rapports et événements sont déjà disponibles via une API pour l’intégration avec vos systèmes SIEM.
* Pour en savoir plus sur la protection d’identité Azure AD, lisez [Azure Active Directory Identity Protection](https://azure.microsoft.com/en-us/documentation/articles/active-directory-identityprotection) (Protection de l’identité Azure Active Directory).
!
**Accès à distance en tant que service :** vous n’avez pas à vous soucier de la maintenance et de l’application de correctifs sur vos serveurs locaux

* Le proxy d’application Azure AD est un service de mise à l’échelle Internet sous notre contrôle. Ainsi, vous obtenez toujours les derniers correctifs de sécurité et mises à niveau. Nous nous chargeons donc de la sécurité de votre réseau.
* Les logiciels sans correctifs sont toujours responsables d’un grand nombre d’attaques. Avec notre modèle de service, vous n’avez plus à vous charger de la gestion des serveurs de périmètre. 

Les services d’accès à distance fournis avec Azure AD fonctionnent conformément aux directives et normes décrites dans le [Centre de gestion de la confidentialité Azure](https://azure.microsoft.com/en-us/support/trust-center). 

Le diagramme ci-dessous illustre la façon dont Azure AD permet un accès à distance sécurisé à vos applications locales.

 ![Connecteurs de proxy d’application Azure AD](./media/application-proxy-security-considerations/secure-remote-access.png)

>[!NOTE] 
>Pour améliorer la sécurité des applications publiées par le proxy d’application Azure AD, nous empêchons les robots web d’indexer et d’archiver vos applications. Chaque fois qu’un robot web tente de récupérer les paramètres de robots d’une application publiée, le proxy d’application répond avec un fichier robots.txt qui inclut les éléments suivants : <br>
>   _User-agent: *_<br>
>   _Disallow: /_
>

## <a name="components-of-the-azure-ad-app-proxy-solution"></a>Composants de la solution de proxy d’application Azure AD

Le proxy d’application Azure Active Directory se compose de deux parties :

* Le service cloud (AAD AP). C’est là que sont effectuées les connexions client/utilisateur externe.
* Un composant local appelé connecteur de proxy d’application AAD.  Le connecteur écoute les requêtes du service AAD AP et gère les connexions aux applications internes, y compris en prenant soin d’éléments tels que la délégation Kerberos contrainte (KCD) pour l’authentification unique.

### <a name="traffic-flowsand-how-they-are-secured"></a>Les flux de trafic, et la façon dont ils sont sécurisés
Cette section inclut des détails sur la manière dont les flux sont sécurisés. Un flux entre le connecteur et le service de proxy d’application est établi lorsque : 

* Le connecteur est configuré pour la première fois.
* Le connecteur extrait les informations de configuration à partir du service de proxy d’application, y compris le groupe auquel chaque connecteur appartient.
* Un utilisateur accède à une application publiée.

>[!NOTE]
>Toutes les communications se produisent sur le protocole SSL et proviennent du connecteur vers le service de proxy d’application. Ce service est uniquement sortant.
>

Le connecteur utilise un certificat client pour authentifier le service de proxy d’application pour tous les appels. La seule exception est l’étape initiale d’installation, au cours de laquelle le certificat client est établi.

#### <a name="install-the-connector"></a>Installation du connecteur

Le flux suivant se déroule lorsque le connecteur est configuré pour la première fois.

1. L’inscription du connecteur sur le service se produit dans le cadre de l’installation du connecteur. À ce stade, l’utilisateur est invité à saisir ses informations d’identification administrateur Azure AD. Le jeton obtenu est ensuite présenté au service de proxy d’application Azure AD.
2. Le proxy d’application évalue le jeton pour vous assurer que l’utilisateur est membre du rôle d’administrateur d’entreprise au sein du client pour lequel le jeton a été émis. Si ce n’est pas le cas, le processus est arrêté.
3. Le connecteur génère une demande de certificat client et la transmet avec le jeton au le service de proxy d’application, qui à son tour vérifie le jeton et signe la requête de certificat client. 
4. Le connecteur utilise ce certificat client pour les communications ultérieures avec le service de proxy d’application.
4. Le connecteur effectue une extraction initiale des données de configuration système à partir du service à l’aide de son certificat client et est prêt à recevoir des requêtes.

#### <a name="periodic-configuration-updates"></a>Mises à jour périodiques de la configuration

Le flux suivant se déroule régulièrement, chaque fois que le service de proxy d’application met à jour les paramètres de configuration.

1. Le connecteur se connecte au point de terminaison de configuration dans le service de proxy d’application à l’aide de son certificat client.
2. Une fois que le certificat client a été validé, le service de proxy d’application renvoie les données de configuration au connecteur ; par exemple, le groupe auquel le connecteur appartient.
3. Le connecteur génère une nouvelle demande de certificat si le certificat actuel date de plus de 30 jours. Ainsi, dans les faits, le certificat client est déployé tous les 30 jours.

#### <a name="accesss-published-applications"></a>Accès aux applications publiées

Le flux suivant se déroule lorsque des utilisateurs accèdent à une application publiée.

1. Lorsqu’un utilisateur accède à une application publiée, le service de proxy d’application vérifie les paramètres de configuration de l’application.  Si l’application est configurée pour utiliser l’authentification préalable avec Azure AD, l’utilisateur est redirigé vers Azure AD STS pour s’authentifier.  Cette étape est ignorée si vous publiez l’application par passthrough.
 * Lors de l’authentification avec Azure AD, nous vérifions les éventuelles exigences de stratégie d’accès conditionnel pour l’application spécifique. Nous le faisons afin de garantir que l’utilisateur a été attribué à l’application ; (par exemple, si l’authentification multifacteur est requise, la séquence d’authentification invitera l’utilisateur à effectuer une authentification de second facteur).
 * Une fois toutes les vérifications réussies, Azure AD STS émet un jeton signé pour l’application et redirige l’utilisateur vers le service de proxy d’application.
 * Le proxy d’application valide ensuite le jeton pour s’assurer qu’il a été émis pour l’application pour laquelle l’utilisateur a demandé accès. Il fait cela, ainsi que d’autres contrôles, comme la vérification que le jeton a été signé par Azure AD et est toujours dans la plage d’utilisation valide, etc..
 * Le proxy d’application définit un cookie d’authentification chiffré (comme un cookie non persistant) pour indiquer que l’authentification à l’application a eu lieu.  Ce cookie contient un horodatage d’expiration basé sur le jeton d’Azure AD et d’autres données, comme le nom d’utilisateur sur lequel l’authentification est basée.  Ce cookie est chiffré à l’aide d’une clé privée connue uniquement du service de proxy d’application.
 * Le proxy d’application redirige l’utilisateur vers l’URL demandée à l’origine.
 >[!NOTE] 
 >Si une partie de la procédure de pré-authentification échoue, la demande de l’utilisateur est refusée et l’utilisateur voit un message indiquant la source du problème.
 >

2. Le proxy d’application, lors de la réception de la requête du client, valide que la condition d’authentification préalable a été satisfaite et que le cookie est toujours valide (comme requis). Il place ensuite une demande dans la file d’attente appropriée pour gestion par un connecteur local. 

 >[!NOTE]
 >Toutes les requêtes du connecteur sont émises vers le service du proxy d’application. Les connecteurs gardent une connexion sortante vers le proxy d’application. Lorsqu’une demande arrive, le proxy d’application met en file d’attente la demande sur une des connexions ouvertes pour récupération par le connecteur.
 >

 * La requête comprend des éléments de l’application, comme les en-têtes de la requête et des données du cookie crypté, l’utilisateur qui effectue la requête et l’ID de requête.  Cependant, le cookie d’authentification chiffré n’est pas envoyé au connecteur.
3. Le connecteur reçoit la requête de la file d’attente, basée sur une connexion sortante à longue durée. Le proxy d’application effectuera une des opérations suivantes en fonction de la requête :
 * Le connecteur confirme s’il peut identifier l’application.  Si ce n’est pas le cas, le connecteur établit une connexion au service de proxy d’application pour collecter des informations détaillées sur l’application et la met en cache localement.
 * Si la demande est une opération simple, par exemple s’il n’y a aucune donnée dans le corps comme pour une requête RESTful « GET », le connecteur établit une connexion à la ressource interne cible et attend une réponse.
 * Si la requête possède des données associées dans le corps, par exemple une opération RESTful « POST », le connecteur établit une connexion sortante à l’aide du certificat client vers l’instance du proxy d’application. Il procède ainsi pour demander les données et ouvrir une connexion vers la ressource interne.  Lors de la réception de la requête à partir du connecteur, le service de proxy d’application commence à accepter le contenu de l’utilisateur et le transfère vers le connecteur.  Le connecteur transfère à son tour les données vers la ressource interne.
4. Une fois la requête et la transmission de tout le contenu vers le serveur principal terminées, le connecteur attend une réponse.
5. Une fois qu’une réponse est reçue, le connecteur établit une connexion sortante vers le service de proxy d’application, afin de renvoyer les détails de l’en-tête et commencer la diffusion des données renvoyées.
6. Le proxy d’application « diffuse » les données vers l’utilisateur.  Un traitement des en-têtes peut se produire ici, en fonction des besoins et comme défini par l’application.

Si vous avez besoin d’aide pour la communication à partir d’une application web Azure au moyen d’un navigateur client vers un point de terminaison Simple Object Access Protocal (SOAP) local authentifié par Windows, consultez ce [Billet de blog sur Azure Field Notes](http://www.azurefieldnotes.com/2016/12/02/claims-to-windows-identity-translation-solutions-and-its-flaws-when-using-azure-ad-application-proxy). 

##<a name="next-steps"></a>Étapes suivantes
[Présentation des connecteurs de proxy d’application Azure AD](application-proxy-understand-connectors.md)




<!--HONumber=Feb17_HO2-->


