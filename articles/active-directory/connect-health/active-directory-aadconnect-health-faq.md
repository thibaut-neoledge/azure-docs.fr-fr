---
title: "Azure Active Directory Connect Health : FAQ - Azure | Microsoft Docs"
description: "Ce FAQ répond aux questions que vous pouvez vous poser au sujet d’Azure AD Connect. Ce FAQ couvre les questions sur l&quot;utilisation du service, notamment le modèle de facturation, les fonctionnalités, les limitations et le support."
services: active-directory
documentationcenter: 
author: billmath
manager: samueld
editor: curtand
ms.assetid: f1b851aa-54d7-4cb4-8f5c-60680e2ce866
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 7c320a043322fefea1f58301492d4c5a0567320c
ms.openlocfilehash: aff2ced283de8282860d0aa356b12478f647d09c

---
# <a name="azure-ad-connect-health-frequently-asked-questions-faq"></a>Forum Aux Questions (FAQ) Azure AD Connect Health
Ce FAQ répond aux questions que vous pouvez vous poser au sujet d’Azure AD Connect. Ce FAQ couvre les questions sur l'utilisation du service, notamment le modèle de facturation, les fonctionnalités, les limitations et le support.

## <a name="general-questions"></a>Questions générales
**Q : Je gère plusieurs annuaires Azure AD. Comment basculer entre les différents répertoires dans le portail Azure ?**

Vous pouvez basculer entre les différents clients Azure AD en sélectionnant le nom d’utilisateur actuellement connecté dans le coin supérieur droit, puis en choisissant le compte approprié. Si le compte n’est pas répertorié ici, cliquez sur Se déconnecter, puis utilisez les informations d’identification appropriées du répertoire pour vous connecter.

**Q : Quelles versions des rôles d’identité sont prises en charge par Azure AD Connect Health ?**

Le tableau suivant répertorie les rôles et les versions de système d’exploitation prises en charge.

|Rôle| Système d’exploitation/version|
|--|--|
|AD FS| <ul> <li> Windows Server 2008R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|
|Azure AD Connect | Version 1.0.9125 ou supérieure.|
|AD DS| <ul> <li> Windows Server 2008R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|

Notez que les fonctionnalités fournies par le service peuvent différer selon le rôle et le système d’exploitation. En d’autres termes, toutes les fonctionnalités peuvent ne pas être disponibles pour toutes les versions de système d’exploitation. Consultez les descriptions des fonctionnalités pour plus d’informations.

**Q : De combien de licences ai-je besoin pour surveiller mon infrastructure ?**

* Le premier agent Connect Health nécessite au moins une licence Azure AD Premium.
* Chaque agent supplémentaire inscrit nécessite 25 licences AADP complémentaires. 
* Le nombre d’agents est équivalent au nombre total d’agents inscrits auprès de tous les rôles surveillés (AD FS, Azure AD Connect et/ou AD DS).

Les informations relatives aux licences se trouvent également sur la [page Azure Active Directory Tarification](https://aka.ms/aadpricing).

Exemple :

| Agents inscrits | Licences nécessaires | Exemple de configuration de surveillance |
| ------ | --------------- | --- |
| 1 | 1 | 1 serveur AAD Connect |
| 2 | 26| 1 serveur AAD Connect et 1 contrôleur de domaine |
| 3 | 51 | 1 serveur AD FS, 1 proxy AD FS et 1 contrôleur de domaine |
| 4 | 76 | 1 serveur AD FS, 1 proxy AD FS et 2 contrôleurs de domaine |
| 5 | 101 | 1 serveur AAD Connect, 1 serveur AD FS, 1 proxy AD FS et 2 contrôleurs de domaine |
## <a name="installation-questions"></a>Questions sur l’installation
**Q : Quel est l’impact de l’installation de l’agent Azure AD Connect Health sur des serveurs individuels ?**

L’incidence de l’installation de l’agent Microsoft Azure AD Connect Health sur des serveurs AD FS, des serveurs proxy d’application web, des serveurs (de synchronisation) Azure AD Connect ou des contrôleurs de domaine est minime en matière de consommation du processeur, de consommation de la mémoire, d’utilisation de la bande passante réseau et du stockage.

Les valeurs ci-dessous sont des approximations.

    * Consommation du processeur : environ&1;-5 % d’augmentation
    * Consommation de la mémoire : jusqu’à 10 % de la mémoire totale du système

> [!NOTE]
> Si l’agent d’intégrité perd la connexion au service Connect Health, il stocke les données localement, jusqu’à une limite maximale définie. L’agent remplace les données « mises en cache » sur la base « dernier événement traité ».
>
>

* Stockage de mémoire tampon locale pour les agents Azure AD Connect Health : environ&20; Mo
* Pour les serveurs AD FS, il est recommandé de mettre en service un espace disque de 1024 Mo (1 Go) pour permettre au canal d’audit AD FS dédié aux agents Azure AD Connect Health de traiter l’ensemble des données d’audit avant qu’elles ne soient remplacées.

**Q : Devrai-je redémarrer mes serveurs durant l’installation des agents Azure AD Connect Health ?**

Non. Vous ne devrez pas redémarrer les serveurs durant l’installation des agents.

L’exécution de certaines des étapes prérequises peut nécessiter un redémarrage du serveur. Par exemple, sur Windows Server 2008 R2, l’installation de .Net 4.5 Framework requiert un redémarrage du serveur.

**Q: Les services Azure AD Connect Health fonctionnent-ils par le biais d’un proxy HTTP intermédiaire ?**

Oui.  Pour les opérations en cours, vous pouvez configurer l'agent Health pour transmettre les requêtes HTTP sortantes à l'aide d'un HTTP Proxy. En savoir plus sur la [configuration du proxy HTTP pour les agents d’intégrité](active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)

Si vous devez configurer un serveur proxy lors de l’inscription de l’agent, il vous faudra peut-être modifier au préalable vos paramètres de proxy Internet Explorer.

1. Ouvrez Internet Explorer -> Paramètres -> Options Internet -> Connexions -> Paramètres de réseau local.
2. Sélectionnez Utiliser un serveur proxy pour votre réseau local.
3. Sélectionnez Avancé si vous disposez de ports proxy différents pour les protocoles HTTP et HTTPS sécurisé.

**Q : Les services Azure AD Connect Health prennent-ils en charge l’authentification de base lors des connexions à des proxys HTTP ?**

Non. Aucun mécanisme de définition de nom d’utilisateur/mot de passe aléatoire pour l’authentification de base n’est actuellement pris en charge.

**Q : Quels paramètres de pare-feu dois-je ouvrir pour permettre le bon fonctionnement de l’agent Azure AD Connect Health ?**

Consultez la [section Configuration requise](active-directory-aadconnect-health-agent-install.md#requirements) pour obtenir la liste des ports de pare-feu et les autres conditions à respecter en matière de connectivité.


**Q : Pourquoi deux serveurs avec le même nom s’affichent dans le portail Azure AD Connect Health ?**

Lorsque vous supprimez un agent à partir d’un serveur, le serveur n’est pas automatiquement supprimé du portail Azure AD Connect.  Si vous avez supprimé manuellement un agent d’un serveur ou le serveur lui-même, vous devez supprimer manuellement l’entrée de serveur à partir du portail Azure AD Connect Health.

Si vous avez réinitialisé un serveur ou créé un nouveau serveur avec les mêmes détails (par exemple, le nom de l’ordinateur) et que vous n’avez pas supprimé le serveur déjà enregistré à partir du portail Azure AD Connect Health et ensuite installé l’agent sur le nouveau serveur, deux entrées portant le même nom peuvent s’afficher.  
Dans ce cas, vous devez supprimer l’entrée appartenant à l’ancien serveur manuellement. Les données associées à ce serveur devraient être obsolètes.

## <a name="health-agent-registration-and-data-freshness"></a>Inscription de l’agent d’intégrité et actualisation des données

**Q : Quelles sont les raisons courantes expliquant les échecs d’inscription de l’agent d’intégrité et comment résoudre les problèmes ?**

L’inscription de l’agent d’intégrité peut échouer pour les raisons suivantes.
    * L’agent ne peut pas communiquer avec les points de terminaison requis car le pare-feu bloque le trafic. Cela est particulièrement courant sur les serveurs proxy d’application web. Assurez-vous que vous avez autorisé les communications sortantes vers les ports et les points de terminaison requis. Consultez la [section Configuration requise](active-directory-aadconnect-health-agent-install.md#requirements) pour plus d’informations.
    * Les communications sortantes sont soumises à une inspection SSL par la couche réseau. Cela entraîne le remplacement du certificat utilisé par l’agent par l’entité/le serveur d’inspection, et il ne peut pas effectuer les étapes requises pour terminer l’inscription de l’agent.
    * L’utilisateur n’a pas les droits d’accès nécessaires pour procéder à l’inscription de l’agent. Par défaut, les administrateurs globaux possèdent les droits d’accès. Vous pouvez utiliser le [contrôle d’accès en fonction du rôle](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control) pour déléguer l’accès à d’autres utilisateurs.

**Q : Je reçois le message d’alerte suivant : « Les données du service de contrôle d’intégrité ne sont pas à jour. ». Comment résoudre ce problème ?**

Cette alerte est générée par le service de contrôle d’intégrité lorsqu’il ne reçoit pas tous les points de données à partir du serveur au cours des 2 dernières heures. Le déclenchement de cette alerte peut avoir plusieurs origines.
    * L’agent ne peut pas communiquer avec les points de terminaison requis car le pare-feu bloque le trafic. Cela est particulièrement courant sur les serveurs proxy d’application web. Assurez-vous que vous avez autorisé les communications sortantes vers les ports et les points de terminaison requis. Consultez la [section Configuration requise](active-directory-aadconnect-health-agent-install.md#requirements) pour plus d’informations.
    * Les communications sortantes sont soumises à une inspection SSL par la couche réseau. Cela entraîne le remplacement du certificat utilisé par l’agent par l’entité/le serveur d’inspection, et il ne peut pas charger les données vers le service Connect Health.
    * Vous pouvez utiliser la commande de connectivité intégrée à l’agent. [En savoir plus](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service).
    * Les agents prennent également en charge la connectivité sortante via un proxy HTTP non authentifié. [En savoir plus](active-directory-aadconnect-health-agent-install.md##configure-azure-ad-connect-health-agents-to-use-http-proxy).


## <a name="operations-questions"></a>Questions sur les opérations
**Q : Dois-je activer l’audit sur les serveurs proxy d’application web ?**

Non, il n’est pas nécessaire d’activer l’audit sur les serveurs proxy d’application web (WAP).

**Q : Comment les alertes Azure AD Connect Health sont-elles résolues ?**

Les alertes Azure AD Connect Health sont résolues en cas de condition de succès. Les agents Azure AD Connect Health détectent et signalent régulièrement les conditions de succès au service. Pour certaines alertes, la suppression s’effectue en fonction d’un intervalle de temps. Concrètement, cela signifie que si la condition d’erreur n’est pas observée dans les 72 heures suivant la génération de l’alerte, cette dernière est automatiquement résolue.


## <a name="migration-questions"></a>Questions relatives à la migration

Cette section s’applique uniquement aux clients qui ont été informés d’une prochaine migration de leurs données de service Azure AD Connect Health.

**Q : Dois-je réinscrire mes agents ou reconfigurer mes paramètres de notification, une fois la migration effectuée ?**

Non, les paramètres de notification et informations d’inscription d’agent seront déplacés dans le cadre de la migration.

**Q : Combien de temps après la migration les données apparaîtront-elles dans le portail ?**

Les données commencent à apparaître dans le portail dans l’heure suivant la migration.

**Q : Que se passe-t-il pour les alertes actives existantes ?**

Les alertes applicables seront réactivées dans l’heure suivant la migration.


## <a name="related-links"></a>Liens connexes
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Installation de l'agent Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Opérations Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Utilisation d’Azure AD Connect Health avec AD FS](active-directory-aadconnect-health-adfs.md)
* [Utilisation d'Azure AD Connect Health pour la synchronisation (en Anglais)](active-directory-aadconnect-health-sync.md)
* [Utilisation d’Azure AD Connect Health avec AD DS](active-directory-aadconnect-health-adds.md)
* [Historique de publication des versions d’Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)



<!--HONumber=Feb17_HO2-->


