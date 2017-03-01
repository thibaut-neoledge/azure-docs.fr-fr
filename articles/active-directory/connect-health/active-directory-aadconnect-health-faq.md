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
ms.sourcegitcommit: 6ad2194a71e0d36ba4a0b9a46ca6dbcd58b619ff
ms.openlocfilehash: 06a8b79f0740e902bb7f9412b449a98b2f0167ea
ms.lasthandoff: 02/16/2017

---
# <a name="azure-ad-connect-health-frequently-asked-questions"></a>Forum Aux Questions (FAQ) Azure AD Connect Health
Cet article répond aux questions fréquemment posées sur Azure Active Directory (Azure AD) Connect Health. Ces FAQ abordent l’utilisation du service, notamment le modèle de facturation, les fonctionnalités, les limitations et le support.

## <a name="general-questions"></a>Questions générales
**Q : Je gère plusieurs annuaires Azure AD. Comment basculer sur le locataire associé à Azure Active Directory Premium ?**

Pour basculer entre les différents locataires Azure AD, sélectionnez le **nom d’utilisateur** actuellement connecté dans l’angle supérieur droit, puis choisissez le compte approprié. Si le compte n’est pas répertorié, sélectionnez **Se déconnecter**, puis connectez-vous avec les informations d’identification d’administrateur général de l’annuaire Azure Active Directory Premium auquel il est possible de se connecter.

**Q : Quelles versions des rôles d’identité sont prises en charge par Azure AD Connect Health ?**

Le tableau suivant répertorie les rôles et les versions de système d’exploitation prises en charge.

|Rôle| Système d’exploitation/version|
|--|--|
|Active Directory Federation Services (AD FS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|
|Azure AD Connect | Version 1.0.9125 ou supérieure|
|Active Directory Domain Services (AD DS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|

Notez que les fonctionnalités proposées par le service peuvent varier selon le rôle et le système d’exploitation. En d’autres termes, toutes les fonctionnalités peuvent ne pas être disponibles pour toutes les versions de système d’exploitation. Consultez les descriptions des fonctionnalités pour plus d’informations.

**Q : De combien de licences ai-je besoin pour surveiller mon infrastructure ?**

* Le premier agent Connect Health nécessite au moins une licence Azure AD Premium.
* Chaque nouvel agent inscrit nécessite 25 licences Azure AD Premium supplémentaires.
* Le nombre d’agents est équivalent au nombre total d’agents inscrits auprès de tous les rôles surveillés (AD FS, Azure AD Connect et/ou AD DS).

Les informations relatives aux licences se trouvent également sur la [page Azure Active Directory Tarification](https://aka.ms/aadpricing).

Exemple :

| Agents inscrits | Licences nécessaires | Exemple de configuration de surveillance |
| ------ | --------------- | --- |
| 1 | 1 | 1 serveur Azure AD Connect |
| 2 | 26| 1 serveur Azure AD Connect et 1 contrôleur de domaine |
| 3 | 51 | 1 serveur Active Directory Federation Services (AD FS), 1 proxy AD FS et 1 contrôleur de domaine |
| 4 | 76 | 1 serveur AD FS, 1 proxy AD FS et 2 contrôleurs de domaine |
| 5 | 101 | 1 serveur Azure AD Connect, 1 serveur AD FS, 1 proxy AD FS et 2 contrôleurs de domaine |


## <a name="installation-questions"></a>Questions sur l’installation

**Q : Quel est l’impact de l’installation de l’agent Azure AD Connect Health sur des serveurs individuels ?**

L’incidence de l’installation de l’agent Microsoft Azure AD Connect Health, d’AD FS, des serveurs proxy d’application web, des serveurs (de synchronisation) Azure AD Connect et des contrôleurs de domaine est minime en matière de consommation du processeur et de la mémoire, d’utilisation de la bande passante réseau et du stockage.

Les données suivantes sont fournies uniquement à titre approximatif :

* Consommation du processeur : environ&1;-5 % d’augmentation.
* Consommation de la mémoire : jusqu’à 10 % de la mémoire totale du système.

> [!NOTE]
> Si l’agent ne peut pas communiquer avec Azure, il stocke les données localement pour une limite maximale définie. L’agent remplace les données « mises en cache » sur la base « dernier événement traité ».
>
>

* Stockage de mémoire tampon locale pour les agents Azure AD Connect Health : environ&20; Mo.
* Pour les serveurs AD FS, il est recommandé de mettre en service un espace disque de 1 024 Mo (1 Go) pour permettre au canal d’audit AD FS dédié aux agents Azure AD Connect Health de traiter l’ensemble des données d’audit avant qu’elles ne soient remplacées.

**Q : Devrai-je redémarrer mes serveurs durant l’installation des agents Azure AD Connect Health ?**

Non. Vous ne devrez pas redémarrer les serveurs durant l’installation des agents. Toutefois, l’exécution de certaines des étapes pré-requises peut nécessiter un redémarrage du serveur.

Par exemple, sur Windows Server 2008 R2, l’installation de .NET 4.5 Framework requiert un redémarrage du serveur.

**Q : Le service Azure AD Connect Health fonctionne-t-il par le biais d’un proxy HTTP intermédiaire ?**

Oui. Pour les opérations en cours, vous pouvez configurer l’agent Health pour transmettre les requêtes HTTP sortantes à l’aide d’un proxy HTTP.
Pour plus d’informations sur la [configuration du proxy HTTP pour les agents Health, voir ](active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy).

Si vous devez configurer un proxy lors de l’inscription de l’agent, il vous faudra peut-être modifier au préalable vos paramètres de proxy Internet Explorer.

1. Allez dans Internet Explorer > **Paramètres** > **Options Internet** > **Connexions** > **Paramètres de réseau local**.
2. Sélectionnez **Utiliser un serveur proxy pour votre réseau local**.
3. Sélectionnez **Avancé** si vous disposez de ports proxy différents pour les protocoles HTTP et HTTPS sécurisé.

**Q : Le service Azure AD Connect Health prend-il en charge l’authentification de base lors des connexions à des proxys HTTP ?**

Non. Aucun mécanisme de définition de nom d’utilisateur/mot de passe arbitraire pour l’authentification De base n’est actuellement pris en charge.

**Q : Quels paramètres de pare-feu dois-je ouvrir pour permettre le bon fonctionnement de l’agent Azure AD Connect Health ?**

Pour obtenir la liste des ports de pare-feu et les autres conditions à respecter en matière de connectivité, voir la [section sur les conditions requises](active-directory-aadconnect-health-agent-install.md#requirements).

**Q : Pourquoi deux serveurs portant le même nom s’affichent dans le portail Azure AD Connect Health ?**

Lorsque vous supprimez un agent à partir d’un serveur, ce serveur n’est pas automatiquement supprimé du portail Azure AD Connect Health. Si vous supprimez manuellement un agent d’un serveur ou le serveur lui-même, vous devez supprimer manuellement l’entrée de serveur à partir du portail Azure AD Connect Health.

Rien ne vous empêche ensuite de réinitialiser un serveur ou de créer un nouveau serveur avec les mêmes caractéristiques (par exemple, le nom de l’ordinateur). Si vous n’avez pas supprimé le serveur déjà inscrit à partir du portail Azure AD Connect Health et si vous avez installé l’agent sur le nouveau serveur, deux entrées portant le même nom peuvent apparaître.

Dans ce cas, supprimez manuellement celle qui appartient à l’ancien serveur. Les données associées à ce serveur sont normalement obsolètes.

## <a name="health-agent-registration-and-data-freshness"></a>Inscription de l’agent Health et actualisation des données

**Q : Quelles sont les raisons courantes expliquant les échecs d’inscription de l’agent Health et comment résoudre les problèmes constatés ?**

L’inscription de l’agent Health peut échouer pour les raisons suivantes :

* L’agent ne peut pas communiquer avec les points de terminaison requis, car un pare-feu bloque le trafic. Cela est particulièrement courant sur les serveurs proxy d’application web. Assurez-vous que vous avez autorisé les communications sortantes vers les ports et les points de terminaison requis. Pour plus d’informations, voir la [section sur les composants requis](active-directory-aadconnect-health-agent-install.md#requirements).
* Les communications sortantes sont soumises à une inspection SSL par la couche réseau. Cela entraîne le remplacement du certificat que l’agent utilise par l’entité/le serveur d’inspection, et il est alors impossible d’effectuer les étapes requises pour terminer l’inscription de l’agent.
* L’utilisateur n’a pas les droits d’accès nécessaires pour procéder à l’inscription de l’agent. Par défaut, les administrateurs globaux possèdent les droits d’accès. Vous pouvez utiliser le [contrôle d’accès en fonction du rôle](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control) pour déléguer l’accès à d’autres utilisateurs.

**Q : Je reçois le message d’alerte suivant : « Les données du service de contrôle d’intégrité ne sont pas à jour ». Comment puis-je résoudre ce problème ?**

Le service Azure AD Connect Health génère cette alerte lorsqu’il ne reçoit pas tous les points de données du serveur au cours des deux dernières heures. Le déclenchement de cette alerte peut avoir plusieurs origines.

* L’agent ne peut pas communiquer avec les points de terminaison requis, car un pare-feu bloque le trafic. Cela est particulièrement courant sur les serveurs proxy d’application web. Assurez-vous que vous avez autorisé les communications sortantes vers les ports et les points de terminaison requis. Pour plus d’informations, voir la [section sur les composants requis](active-directory-aadconnect-health-agent-install.md#requirements).
* Les communications sortantes sont soumises à une inspection SSL par la couche réseau. Cela entraîne le remplacement du certificat que l’agent utilise par l’entité/le serveur d’inspection, et il est impossible de charger les données vers le service Azure AD Connect Health.
* Vous pouvez utiliser la commande de connectivité intégrée à l’agent. [En savoir plus](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service).
* Les agents prennent également en charge la connectivité sortante via un proxy HTTP non authentifié. [En savoir plus](active-directory-aadconnect-health-agent-install.md##configure-azure-ad-connect-health-agents-to-use-http-proxy).

## <a name="operations-questions"></a>Questions sur les opérations
**Q : Dois-je activer l’audit sur les serveurs proxy d’application web ?**

Non. Il n’est pas nécessaire d’activer l’audit sur les serveurs proxy d’application web.

**Q : Comment les alertes Azure AD Connect Health sont-elles résolues ?**

Les alertes Azure AD Connect Health sont résolues en cas de condition de réussite. Les agents Azure AD Connect Health détectent et signalent régulièrement au service les conditions de réussite. Pour certaines alertes, la suppression s’effectue en fonction d’un intervalle de temps. Concrètement, cela signifie que si la condition d’erreur n’est pas observée dans les 72 heures suivant la génération de l’alerte, cette dernière est automatiquement résolue.

## <a name="migration-questions"></a>Questions relatives à la migration

Cette section s’applique uniquement aux clients qui ont été informés d’une prochaine migration de leurs données Azure AD Connect Health.

**Q : Dois-je réinscrire mes agents ou reconfigurer mes paramètres de notification une fois la migration effectuée ?**

Non. Les paramètres de notification et les informations d’inscription d’agent sont déplacés dans le cadre de la migration.

**Q : Dans quel délai après la migration les données commencent-elles à apparaître dans le portail ?**

Les données commencent à apparaître dans le portail dans l’heure suivant la migration.

**Q : Que se passe-t-il pour les alertes actives existantes ?**

Les alertes applicables seront réactivées dans l’heure suivant la migration.

## <a name="related-links"></a>Liens connexes
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Installation de l’agent Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Opérations Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Utilisation d’Azure AD Connect Health avec AD FS](active-directory-aadconnect-health-adfs.md)
* [Utilisation d'Azure AD Connect Health pour la synchronisation (en Anglais)](active-directory-aadconnect-health-sync.md)
* [Utilisation d’Azure AD Connect Health avec AD DS](active-directory-aadconnect-health-adds.md)
* [Historique des versions d’Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)

