---
title: "Événements à risque dans Azure Active Directory | Microsoft Docs"
description: "Cette rubrique offre une présentation détaillée des événements à risque."
services: active-directory
keywords: "azure active directory identity protection, sécurité, risque, niveau de risque, vulnérabilité, stratégie de sécurité"
author: MarkusVi
manager: femila
ms.assetid: ce3b054c-7772-401f-9b06-3d24f6e7ca86
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 4dfd9690f2e3e017ca308649f79a84466d49d844
ms.openlocfilehash: 91ee5da0885fc0b044150e524757eac659a003a4


---
# <a name="azure-active-directory-risk-events"></a>Événements à risque dans Azure Active Directory

Azure Active Directory vous aide à protéger vos identités. Le processus de protection consiste en partie à détecter les actions suspectes liées aux comptes des utilisateurs. La détection est basée sur des algorithmes d’apprentissage automatique et une méthode heuristique. Chaque action suspecte détectée est stockée dans un enregistrement appelé *événement à risque*. 


![Événement à risque](./media/active-directory-identity-protection-risk-events/91.png)


À l’heure actuelle, Azure Active Directory détecte six types d’événements à risque.

| Type d’événement à risque | Niveau de risque | Type de détection |
| :-- | --- | --- |
| [Utilisateurs dont les informations d’identification ont fait l’objet d’une fuite](#leaked-credentials) | Élevé | Hors ligne |
| [Connexions depuis des adresses IP anonymes](#sign-ins-from-anonymous-ip-addresses) | Moyenne | Temps réel |
| [Voyage impossible vers des emplacements inhabituels](#impossible-travel-to-atypical-locations) | Moyenne | Hors ligne |
| [Connexions depuis des emplacements inconnus](#sign-in-from-unfamiliar-locations) | Moyenne | Temps réel |
| [Connexions depuis des périphériques infectés](#sign-ins-from-infected-devices) | Faible | Hors ligne |
| [Connexions depuis des adresses IP avec une activité suspecte](#sign-ins-from-ip-addresses-with-suspicious-activity) | Moyenne | Hors ligne|

## <a name="risk-level"></a>Niveau de risque

Le niveau de risque (Haut, Moyen ou Faible) indique la gravité d’un événement à risque. Cette propriété vous aide à établir la priorité des mesures à prendre. 

Le niveau de gravité de l’événement à risque signalé représente la probabilité qu’une identité soit compromise, avec la quantité de bruit que cela implique généralement.

* **Élevé**: probabilité élevée et gravité élevée de l’événement à risque. Ces événements donnent une indication forte que l’identité de l’utilisateur a été compromise et les comptes d’utilisateurs concernés doivent immédiatement faire l’objet de mesures de correction.

* **Moyen**: sévérité élevée, mais probabilité moindre de l’événement à risque, ou inversement. Ces événements présentent des risques potentiels et les comptes d’utilisateurs concernés doivent faire l’objet de mesures de correction.

* **Faible**: probabilité faible et gravité limitée de l’événement à risque. Cet événement peut ne pas nécessiter une action immédiate, mais l’association à d’autres événements à risque peut donner une indication forte que l’identité a été compromise.

![Niveau de risque](./media/active-directory-identity-protection-risk-events/01.png)


## <a name="detection-type"></a>Type de détection

Le type de détection (Temps réel, Hors connexion) indique les circonstances de détection d’un événement à risque.  
À l’heure actuelle, la plupart des événements à risque sont détectés hors connexion dans le cadre d’une opération de post-traitement après la survenue de l’événement à risque.

Le tableau suivant répertorie le temps nécessaire à un type de détection pour s’afficher dans un rapport associé.

| Type de détection | Latence dans la génération des rapports |
| --- | --- |
| Temps réel | 5 à 10 minutes |
| Hors ligne | 2 à 4 heures |



## <a name="risk-event-types"></a>Type d’événement à risque

Le type d’événement à risque détermine l’action suspecte pour laquelle un enregistrement d’événement à risque a été créé.  
À l’heure actuelle, Azure Active Directory détecte six types d’événements.

Les investissements continus de Microsoft dans le processus de détection assurent :

- L’amélioration de la précision de la détection des événements à risque existants 
- L’ajout ultérieur de nouveaux types d’événements à risque

### <a name="leaked-credentials"></a>Informations d’identification divulguées
Il arrive que les chercheurs en sécurité de Microsoft découvrent des informations d’identification divulguées dans le web invisible. Ces informations d’identification sont généralement publiées en texte brut. Elles sont comparées aux informations d’identification d’Azure AD, et s’il existe une correspondance, elles sont signalées comme étant des « informations d’identification divulguées » dans Identity Protection.

Les événements à risque liés à des informations d’identification divulguées sont définis sur un niveau de gravité « Élevé », car ils indiquent clairement que le nom d’utilisateur et le mot de passe sont à la disposition de personnes malveillantes.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Connexions depuis des adresses IP anonymes
Ce type d’événement à risque signale les utilisateurs qui se sont connectés depuis une adresse IP ayant été identifiée comme l’adresse IP d’un proxy anonyme. Ces proxys sont utilisés par des individus souhaitant masquer l’adresse IP de leur appareil et peuvent être utilisés dans un but malveillant.

Nous vous recommandons de contacter immédiatement l’utilisateur pour vérifier s’il utilisait une adresse IP anonyme. Le niveau de risque de ce type d’événement risque est défini sur**Moyen**, car une adresse IP anonyme n’est pas en soi une indication forte qu’un compte a été compromis.


### <a name="impossible-travel-to-atypical-locations"></a>Voyage impossible vers des emplacements inhabituels
Ce type d’événement à risque identifie deux connexions depuis des emplacements géographiquement distants, dont l’un au moins un est inhabituel pour l’utilisateur compte tenu de son comportement passé. De plus, le délai écoulé entre les deux connexions est inférieur au temps nécessaire pour se déplacer du premier emplacement au second, ce qui indique qu’un autre utilisateur utilise les mêmes informations d’identification. 

Il s’agit d’un algorithme d’apprentissage automatique qui ignore les «*faux positifs*» évidents contribuant à la condition de voyage impossible, tels que les VPN et les emplacements régulièrement utilisés par d’autres membres de l’organisation.  Le système présente une période d’apprentissage initiale de 14 jours lui servant à assimiler le comportement de connexion des nouveaux utilisateurs.

En règle générale, les événements de ce type donnent une bonne indication qu’un pirate est parvenu à se connecter avec le compte correspondant. Cependant, des faux positifs peuvent se produire lorsqu’un utilisateur en déplacement utilise un nouvel appareil ou un VPN qu’aucun autre membre de l’organisation n’utilise. Des faux positifs peuvent également survenir si des applications transmettent incorrectement des adresses IP de serveurs en tant qu’adresses IP de clients, ce qui peut donner l’impression que les connexions se font depuis le centre de données où le serveur principal de cette application est hébergé (il s’agit souvent d’un centre de données Microsoft, ce qui peut donner l’impression que les connexions se font depuis des adresses IP appartenant à Microsoft). En raison de ces faux positifs, le niveau de risque de cet événement est défini sur**Moyen**.

### <a name="sign-in-from-unfamiliar-locations"></a>Connexions depuis des emplacements non connus
Ce type d’événement à risque est un mécanisme d’évaluation de connexion en temps réel qui prend en compte les emplacements de connexion passés (IP, latitude/longitude et NSA) pour déterminer les emplacements non connus/nouveaux. Le système stocke les informations sur les emplacements précédents d’un utilisateur et considère ces emplacements comme « connus ». L’événement à risque est déclenché lorsque la connexion a lieu depuis un emplacement qui ne figure pas dans la liste des emplacements connus. Le système présente une période d’apprentissage initiale de 14 jours, durant laquelle il ne signale pas les nouveaux emplacements en tant qu’emplacements non connus. Le système ignore également les connexions depuis les appareils connus et les emplacements géographiquement proches d’un emplacement connu. 

Les emplacements non connus peuvent donner une indication forte qu’un pirate tente d’utiliser une identité volée. Des faux positifs peuvent se produire lorsqu’un utilisateur est en déplacement, essaie un nouvel appareil ou utilise un nouveau VPN. C’est pourquoi le niveau de risque de ce type d’événement est défini sur**Moyen**.



### <a name="sign-ins-from-infected-devices"></a>Connexions depuis des appareils infectés
Ce type d’événement à risque identifie les connexions depuis des appareils infectés par des logiciels malveillants, qui sont connus pour communiquer activement avec un serveur robot, grâce à la mise en corrélation des adresses IP des appareils des utilisateurs avec des adresses ayant été en contact avec un serveur robot. 

Cet événement à risque identifie les adresses IP, pas les appareils des utilisateurs. Si plusieurs appareils utilisent une même adresse IP, mais que seuls certains sont contrôlés par un réseau de robots, les connexions depuis les autres appareils peuvent déclencher inutilement cet événement. C’est pourquoi le niveau de risque de ce type d’événement est défini sur **Faible**.  

Nous vous recommandons de contacter l’utilisateur et d’analyser tous ses appareils. Il est également possible que les appareils personnels d’un utilisateur soient infectés ou, comme indiqué précédemment, qu’une tierce personne utilisait un appareil infecté depuis la même adresse IP. Les appareils infectés le sont souvent par des logiciels malveillants qui n’ont pas encore été identifiés par les logiciels antivirus et dont la présence peut être le signe de mauvaises habitudes de la part de l’utilisateur.

Pour plus d'informations sur le traitement des infections de logiciels malveillants, consultez le [Centre de protection contre les programmes malveillants](http://go.microsoft.com/fwlink/?linkid=335773&clcid=0x409).


### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Connexions depuis des adresses IP avec des activités suspectes
Ce type d’événement à risque identifie les adresses IP depuis lesquelles un grand nombre de tentatives de connexion ayant échoué ont été constatées, pour plusieurs comptes d’utilisateurs et sur une courte période. Cela correspond aux modèles de trafic des adresses IP utilisées par les cybercriminels et donne une indication forte que les comptes sont déjà compromis ou sont sur le point de l’être. Il s’agit d’un algorithme d’apprentissage automatique qui ignore les «*faux positifs*» évidents, tels que les adresses IP régulièrement utilisées par d’autres membres de l’organisation.  Le système présente une période d’apprentissage initiale de 14 jours lui servant à assimiler le comportement de connexion des nouveaux utilisateurs et clients.

Nous vous conseillons de contacter l’utilisateur pour vérifier s’il s’est réellement connecté depuis une adresse IP qui a été signalée comme suspecte. Le niveau de risque pour ce type d’événement est défini sur**Moyen**, car il arrive que plusieurs appareils utilisent la même adresse IP, mais que seuls certains soient responsables des activités suspectes. 





## <a name="azure-ad-anomalous-activity-reports"></a>Rapports d’activités anormales Azure AD

Dans le portail Azure Classic, certains des événements à risque sont déjà disponibles via les rapports d’activités anormales Azure AD. 

Le tableau ci-dessous répertorie les différents types d’événements à risque et le rapport d’ **activités anormales d’Azure AD** correspondant. 

| Type d’événement à risque signalé par Identity Protection | Rapport d’activités anormales d’Azure AD correspondant |
|:--- |:--- |
| Informations d’identification divulguées |Utilisateurs avec des informations d’identification volées |
| Voyage impossible vers des emplacements inhabituels |Activité de connexion anormale |
| Connexions depuis des appareils infectés |Connexions à partir d’appareils potentiellement infectés |
| Connexions depuis des adresses IP anonymes |Connexions à partir de sources inconnues |
| Connexions depuis des adresses IP avec des activités suspectes |Connexions depuis des adresses IP avec des activités suspectes |
| Connexions depuis des emplacements non connus |- |
| Événements de verrouillage |- |

Les rapports d’activités anormales d’Azure AD suivants ne sont pas inclus en tant qu’événements à risque dans Azure AD Identity Protection et ne sont donc pas disponibles via Identity Protection. Ces rapports sont toujours disponibles dans le portail Azure Classic. Cependant, ils seront déconseillés ultérieurement du fait de leur remplacement par des événements à risque dans Identity Protection.

* Connexions après plusieurs échecs
* Connexions depuis plusieurs zones géographiques






## <a name="next-steps"></a>Étapes suivantes
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md)




<!--HONumber=Jan17_HO3-->


