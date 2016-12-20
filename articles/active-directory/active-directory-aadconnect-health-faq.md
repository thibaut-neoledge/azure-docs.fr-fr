---
title: "Forum Aux Questions (FAQ) Azure AD Connect Health"
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
ms.date: 10/18/2016
ms.author: vakarand
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 907a4e73d47005d13151387b851e5640912e8cce


---
# <a name="azure-ad-connect-health-frequently-asked-questions-faq"></a>Forum Aux Questions (FAQ) Azure AD Connect Health
Ce FAQ répond aux questions que vous pouvez vous poser au sujet d’Azure AD Connect. Ce FAQ couvre les questions sur l'utilisation du service, notamment le modèle de facturation, les fonctionnalités, les limitations et le support.

## <a name="general-questions"></a>Questions générales
**Q : Je gère plusieurs annuaires Azure AD. Comment basculer sur le locataire associé à Azure Active Directory Premium ?**

Vous pouvez basculer entre les différents clients Azure AD en sélectionnant le nom d’utilisateur actuellement connecté dans le coin supérieur droit, puis en choisissant le compte approprié. Si le compte n’est pas répertorié, sélectionnez Se déconnecter, puis connectez-vous avec les informations d’identification d’administrateur général de l’annuaire Azure Active Directory Premium auquel il est possible de se connecter.

## <a name="installation-questions"></a>Questions sur l’installation
**Q : Quel est l’impact de l’installation de l’agent Azure AD Connect Health sur des serveurs individuels ?**

L’incidence de l’installation de l’agent Microsoft Azure AD Connect Health sur des serveurs AD FS, des serveurs proxy d’application web, des serveurs (de synchronisation) Azure AD Connect ou des contrôleurs de domaine est minime en matière de consommation du processeur, de consommation de la mémoire, d’utilisation de la bande passante réseau et du stockage.

Les valeurs ci-dessous sont des approximations.

* Consommation du processeur : environ 1 % d’augmentation
* Consommation de la mémoire : jusqu’à 10 % de la mémoire totale du système

> [!NOTE]
> Si l’agent ne parvient pas à communiquer avec Azure, il stocke les données localement, jusqu’à une limite maximale définie. L’agent remplace les données « mises en cache » sur la base « dernier événement traité ».
> 
> 

* Stockage de mémoire tampon locale pour les agents Azure AD Connect Health : environ 20 Mo
* Pour les serveurs AD FS, il est recommandé de mettre en service un espace disque de 1024 Mo (1 Go) pour permettre au canal d’audit AD FS dédié aux agents Azure AD Connect Health de traiter l’ensemble des données d’audit avant qu’elles ne soient remplacées.

**Q : Devrai-je redémarrer mes serveurs durant l’installation des agents Azure AD Connect Health ?**

 Non. Vous ne devrez pas redémarrer les serveurs durant l’installation des agents. Toutefois, l’exécution de certaines des étapes pré-requises peut nécessiter un redémarrage du serveur.

Par exemple, sur Windows Server 2008 R2, l’installation de .Net 4.5 Framework requiert un redémarrage du serveur.

**Q: Les services Azure AD Connect Health fonctionnent-ils par le biais d’un proxy HTTP intermédiaire ?**

 Oui.  Pour les opérations en cours, vous pouvez configurer l'agent Health pour transmettre les requêtes HTTP sortantes à l'aide d'un HTTP Proxy. Pour plus d’informations, consultez [Configuration des agents Azure AD Connect Health pour utiliser le proxy HTTP](active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy).
Si vous devez configurer un serveur proxy lors de l’inscription de l’agent, il vous faudra peut-être modifier au préalable vos paramètres de proxy Internet Explorer.

1. Ouvrez Internet Explorer -> Paramètres -> Options Internet -> Connexions -> Paramètres de réseau local.
2. Sélectionnez Utiliser un serveur proxy pour votre réseau local.
3. Sélectionnez Avancé si vous disposez de ports proxy différents pour les protocoles HTTP et HTTPS sécurisé.

**Q : Les services Azure AD Connect Health prennent-ils en charge l’authentification de base lors des connexions à des proxys HTTP ?**

 Non. Aucun mécanisme de définition de nom d’utilisateur/mot de passe aléatoire pour l’authentification de base n’est actuellement pris en charge.

**Q : Quelles versions des services AD DS sont-elles prises en charge par Azure AD Connect Health pour AD DS ?**

La surveillance des services AD DS est prise en charge pour les installations sur les versions de système d’exploitation suivantes :

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2

## <a name="operations-questions"></a>Questions sur les opérations
**Q : Dois-je activer l’audit sur mes serveurs proxy d’application AD FS ou mes serveurs proxy d’application web ?**

Non, il n’est pas nécessaire d’activer l’audit sur les serveurs proxy d’application web (WAP). En revanche, il faut les activer sur les serveurs AD FS.

**Q : Comment les alertes Azure AD Connect Health sont-elles résolues ?**

Les alertes Azure AD Connect Health sont résolues en cas de condition de succès. Les agents Azure AD Connect Health détectent et signalent régulièrement les conditions de succès au service. Pour certaines alertes, la suppression s’effectue en fonction d’un intervalle de temps. Concrètement, cela signifie que si la condition d’erreur n’est pas observée dans les 72 heures suivant la génération de l’alerte, cette dernière est automatiquement résolue.

**Q : Quels paramètres de pare-feu dois-je ouvrir pour permettre le bon fonctionnement de l’agent Azure AD Connect Health ?**

Les ports TCP/UDP 443 et 5671 doivent être ouverts pour permettre à l’agent Azure AD Connect Health de communiquer avec les points de terminaison de service Azure AD Health.

**Q : Pourquoi deux serveurs avec le même nom s’affichent dans le portail Azure AD Connect Health ?**

Lorsque vous supprimez un agent à partir d’un serveur, le serveur n’est pas automatiquement supprimé du portail Azure AD Connect.  Si vous avez supprimé manuellement un agent d’un serveur ou le serveur lui-même, vous devez supprimer manuellement l’entrée de serveur à partir du portail Azure AD Connect Health. Pour plus d’informations, consultez [Supprimer une instance de serveur ou de service.](active-directory-aadconnect-health-operations.md#delete-a-server-or-service-instance)

Si vous avez réinitialisé un serveur ou créé un nouveau serveur avec les mêmes détails (par exemple, le nom de l’ordinateur) et que vous n’avez pas supprimé le serveur déjà enregistré à partir du portail Azure AD Connect Health et ensuite installé l’agent sur le nouveau serveur, deux entrées portant le même nom peuvent s’afficher.  
Dans ce cas, vous devez supprimer l’entrée appartenant à l’ancien serveur manuellement. Les données associées à ce serveur devraient être obsolètes.

## <a name="related-links"></a>Liens connexes
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Installation de l'agent Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Opérations Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Utilisation d’Azure AD Connect Health avec AD FS](active-directory-aadconnect-health-adfs.md)
* [Utilisation d'Azure AD Connect Health pour la synchronisation (en Anglais)](active-directory-aadconnect-health-sync.md)
* [Utilisation d’Azure AD Connect Health avec AD DS](active-directory-aadconnect-health-adds.md)
* [Historique de publication des versions d’Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)




<!--HONumber=Nov16_HO3-->


