---
title: "Considérations de sécurité pour le proxy d’application Azure AD | Microsoft Docs"
description: "Couvre les considérations de sécurité lors de l’utilisation du proxy d’application Azure AD"
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
ms.date: 04/12/2017
ms.author: kgremban
ms.translationtype: Human Translation
ms.sourcegitcommit: 2c33e75a7d2cb28f8dc6b314e663a530b7b7fdb4
ms.openlocfilehash: 52704b3259ddad85db12b8bb81c4992ece2f37a4
ms.contentlocale: fr-fr
ms.lasthandoff: 04/21/2017


---

# <a name="security-considerations-for-accessing-apps-remotely-with-azure-ad-application-proxy"></a>Considérations de sécurité pour l’accès aux applications à distance avec le proxy d’application Azure AD

Cet article explique comment le proxy d’application Azure Active Directory (Azure AD) fournit un service sécurisé pour la publication et l’accès à distance de vos applications.

Le diagramme ci-dessous illustre la façon dont Azure AD permet un accès à distance sécurisé à vos applications locales.

 ![Diagramme illustrant l’accès sécurisé à distance via le proxy d’application Azure AD](./media/application-proxy-security-considerations/secure-remote-access.png)

Le proxy d’application Azure AD offre les avantages de sécurité suivants :

**Accès authentifié :** seules les connexions authentifiées peuvent accéder à votre réseau.

* Le proxy d’application Azure AD s’appuie sur le service d’émission de jeton de sécurité (STS) Azure AD pour toutes les authentifications.  L’authentification préalable, par sa nature, bloque un nombre conséquent d’attaques anonymes, car seules les identités authentifiées sont autorisées à accéder à l’application principale.

**Accès conditionnel :** appliquez des contrôles de stratégie plus riches avant que les connexions à votre réseau soient établies.

* Avec [l’accès conditionnel](active-directory-conditional-access-azuread-connected-apps.md), vous pouvez définir des restrictions concernant le trafic autorisé à accéder à vos applications back-end, en fonction de l’emplacement, de la force de l’authentification et du profil de risque de l’utilisateur.

**Arrêt du trafic :** tout le trafic est arrêté dans le cloud.

* Étant donné que le proxy d’application Azure AD est un proxy inversé, tout le trafic vers les applications principales est arrêté au niveau du service. La session peut être réétablie uniquement avec le serveur principal, ce qui signifie que vos serveurs principaux ne sont pas exposés au trafic HTTP direct. Par exemple, vous pouvez arrêter plus facilement les attaques ciblées.

**Tous les accès sont sortants :** vous n’avez pas besoin de connexion ouverte sur le réseau d’entreprise.

* Les connecteurs Azure AD conservent les connexions sortantes vers le service de proxy d’application Azure AD, ce qui signifie qu’il n’est pas nécessaire d’ouvrir des ports de pare-feu pour les connexions entrantes. Les proxys traditionnels exigeaient un réseau de périmètre (également appelé *DMZ*, *zone démilitarisée* et *sous-réseau filtré*) et autorisaient l’accès à des connexions non authentifiées sur le périmètre du réseau. Il était ainsi nécessaire d’investir davantage dans des produits de pare-feu d’application web pour analyser le trafic et offrir des protections supplémentaires à l’environnement. Avec le proxy d’application, vous pouvez même vous passer de réseau de périmètre, car toutes les connexions sont sortantes et sur un canal sécurisé.

**Analyse de sécurité et mécanisme intelligent basé sur le langage machine :** bénéficiez d’une protection de pointe.

* [Azure AD Identity Protection](active-directory-identityprotection.md) avec un mécanisme intelligent basé sur l’apprentissage de flux de données de notre département Digital Crimes Unit et du Centre de réponse aux problèmes de sécurité Microsoft. Ensemble, nous identifions de façon proactive les comptes compromis et offrons une protection en temps réel contre les connexions à haut risque. Nous prenons en compte divers facteurs, dont l’accès à partir d’appareils infectés et à travers des réseaux anonymes, ainsi qu’à partir d’emplacements atypiques et peu probables.
* Nombre de ces rapports et événements sont déjà disponibles via une API pour l’intégration avec vos systèmes de gestion des événements et des informations de sécurité.

**Accès à distance en tant que service :** vous n’avez pas à vous soucier de la maintenance et de l’application de correctifs sur vos serveurs locaux.

* Les logiciels sans correctifs sont toujours responsables d’un grand nombre d’attaques. Le proxy d’application Azure AD est un service Internet contrôlé par Microsoft ; vous êtes donc assuré d’obtenir les derniers correctifs de sécurité et mises à niveau.
* Pour améliorer la sécurité des applications publiées par le proxy d’application Azure AD, nous empêchons les robots web d’indexer et d’archiver vos applications. Chaque fois qu’un robot web tente de récupérer les paramètres de robots d’une application publiée, le proxy d’application répond avec un fichier robots.txt qui inclut `User-agent: * Disallow: /`.

## <a name="under-the-hood"></a>Sous le capot

Le proxy d’application Azure Active Directory se compose de deux parties :

* Le service basé sur le cloud : c’est là que sont effectuées les connexions client/utilisateur externes.
* Le connecteur de proxy d’application Azure AD : un composant local, le connecteur écoute les demandes du service de proxy d’application Azure AD et gère les connexions vers les applications internes. 

Un flux entre le connecteur et le service de proxy d’application est établi lorsque :

* Le connecteur est configuré pour la première fois.
* Le connecteur extrait les informations de configuration à partir du service de proxy d’application, y compris le groupe auquel chaque connecteur appartient.
* Un utilisateur accède à une application publiée.

>[!NOTE]
>Toutes les communications se produisent sur le protocole SSL et proviennent du connecteur vers le service de proxy d’application. Le service est uniquement sortant.

Le connecteur utilise un certificat client pour authentifier le service de proxy d’application pour presque tous les appels. La seule exception est l’étape initiale d’installation, au cours de laquelle le certificat client est établi.

### <a name="installing-the-connector"></a>Installation du connecteur

Le flux suivant se déroule lorsque le connecteur est configuré pour la première fois :

1. L’inscription du connecteur sur le service se produit dans le cadre de l’installation du connecteur. À ce stade, les utilisateurs sont invités à saisir leurs informations d’identification administrateur Azure AD. Le jeton obtenu est ensuite présenté au service de proxy d’application Azure AD.
2. Le proxy d’application évalue le jeton pour garantir que l’utilisateur est membre du rôle d’administrateur d’entreprise au sein du client pour lequel le jeton a été émis. Si l’utilisateur n’est pas membre du rôle d’administrateur, le processus est arrêté.
3. Le connecteur génère une demande de certificat client et la transmet avec le jeton au service de proxy d’application, qui à son tour vérifie le jeton et signe la demande de certificat client.
4. Le connecteur utilise le certificat client pour les communications ultérieures avec le service de proxy d’application.
5. Le connecteur effectue une extraction initiale des données de configuration système à partir du service à l’aide de son certificat client et est désormais prêt à recevoir des demandes.

### <a name="updating-the-configuration-settings"></a>Mise à jour des paramètres de configuration

Chaque fois que le service de proxy d’application met à jour les paramètres de configuration, les événements de flux suivants ont lieu :

1. Le connecteur se connecte au point de terminaison de configuration dans le service de proxy d’application à l’aide de son certificat client.
2. Une fois que le certificat client a été validé, le service de proxy d’application renvoie les données de configuration au connecteur (par exemple, le groupe auquel le connecteur appartient).
3. Le connecteur génère une nouvelle demande de certificat si le certificat actuel date de plus de 30 jours. Ainsi, dans les faits, le certificat client est mis à jour tous les 30 jours.

### <a name="accessing-published-applications"></a>Accès aux applications publiées

Le flux d’événements suivant se déroule lorsque des utilisateurs accèdent à une application publiée :

#### <a name="the-application-proxy-service-checks-the-configuration-settings-for-the-app"></a>Le service de proxy d’application vérifie les paramètres de configuration de l’application. 

Si l’application est configurée pour utiliser l’authentification préalable avec Azure AD, les utilisateurs sont redirigés vers Azure AD STS pour s’authentifier. Cette étape est ignorée si vous configurez l’application pour utiliser l’accès direct.

1. Le proxy d’application vérifie les éventuelles exigences de stratégie d’accès conditionnel pour l’application spécifique. Cette étape permet de vérifier que l’utilisateur a été attribué à l’application. Si une vérification en deux étapes est requise, la séquence d’authentification invite l’utilisateur à s’authentifier avec un second facteur.

2. Une fois toutes les vérifications réussies, Azure AD STS émet un jeton signé pour l’application et redirige l’utilisateur vers le service de proxy d’application.

3. Le proxy d’application vérifie que le jeton a été émis pour corriger l’application. Il effectue également d’autres vérifications : il vérifie par exemple que le jeton a bien été signé par Azure AD et qu’il est toujours dans la plage d’utilisation valide.

4. Le proxy d’application définit un cookie d’authentification chiffré pour indiquer que l’authentification à l’application a eu lieu. Le cookie contient un horodatage d’expiration basé sur le jeton d’Azure AD et d’autres données, comme le nom d’utilisateur sur lequel l’authentification est basée. Le cookie est chiffré à l’aide d’une clé privée connue uniquement du service de proxy d’application.

5. Le proxy d’application redirige l’utilisateur vers l’URL demandée à l’origine.

Si une partie de la procédure d’authentification préalable échoue, la demande de l’utilisateur est refusée et l’utilisateur voit un message indiquant la source du problème.


#### <a name="application-proxy-places-a-request-in-the-appropriate-queue-for-an-on-premises-connector-to-handle"></a>Le proxy d’application place une demande dans la file d’attente appropriée pour que la gestion soit effectuée par un connecteur local. 

Toutes les demandes du connecteur sont émises vers le service du proxy d’application. Les connecteurs gardent une connexion sortante ouverte vers le proxy d’application. Lorsqu’une demande arrive, le proxy d’application met en file d’attente la demande sur une des connexions ouvertes pour récupération par le connecteur.

La demande comprend des éléments de l’application, comme les en-têtes de la demande et des données du cookie chiffré, l’utilisateur qui effectue la demande et l’ID de la demande. Cependant, le cookie d’authentification chiffré n’est pas envoyé au connecteur.

#### <a name="the-connector-receives-the-request-from-the-queue"></a>Le connecteur reçoit la demande provenant de la file d’attente. 

En fonction de la demande, le proxy d’application effectue l’une des actions suivantes :

* Le connecteur confirme s’il peut identifier l’application. S’il ne parvient pas à identifier l’application, le connecteur établit une connexion au service de proxy d’application pour collecter des informations détaillées sur l’application et la met en cache localement.

* Si la demande est une opération simple (par exemple s’il n’y a aucune donnée dans le corps comme pour une requête RESTful *GET*), le connecteur établit une connexion à la ressource interne cible et attend une réponse.

* Si la demande possède des données associées dans le corps (par exemple une opération RESTful *POST*), le connecteur établit une connexion sortante à l’aide du certificat client vers l’instance du proxy d’application. Il établit cette connexion pour demander les données et ouvrir une connexion vers la ressource interne. Après réception de la demande à partir du connecteur, le service de proxy d’application commence à accepter le contenu de l’utilisateur et transfère les données au connecteur. Le connecteur transfère à son tour les données vers la ressource interne.

#### <a name="after-the-request-and-transmission-of-all-content-to-the-back-end-is-complete-the-connector-waits-for-a-response"></a>Une fois la demande et la transmission de tout le contenu vers le serveur principal terminées, le connecteur attend une réponse.

Après obtention d’une réponse, le connecteur établit une connexion sortante vers le service de proxy d’application, afin de renvoyer les détails de l’en-tête et commencer la diffusion des données renvoyées.

#### <a name="application-proxy-streams-the-data-to-the-user"></a>Le proxy d’application « diffuse » les données vers l’utilisateur. 

Un traitement des en-têtes peut se produire ici, en fonction des besoins et comme défini par l’application.


## <a name="next-steps"></a>Étapes suivantes

[Considérations sur la topologie du réseau lors de l’utilisation du proxy d’application Azure AD](application-proxy-network-topology-considerations.md)

[Présentation des connecteurs de proxy d’application Azure AD](application-proxy-understand-connectors.md)

