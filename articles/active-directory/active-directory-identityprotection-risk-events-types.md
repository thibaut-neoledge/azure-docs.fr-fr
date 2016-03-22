<properties
	pageTitle="Types d’événements à risque détectés par Azure Active Directory Identity Protection | Microsoft Azure"
	description="Cette rubrique présente en détail les différents types d’événements à risque disponibles dans Azure Active Directory Identity Protection"
	services="active-directory"
	keywords="azure active directory identity protection, cloud app discovery, gestion d’applications, sécurité, risque, niveau de risque, vulnérabilité, stratégie de sécurité"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/04/2016"
	ms.author="markvi"/>

#Types d’événements à risque détectés par Azure Active Directory Identity Protection 

Dans Azure Active Directory Identity Protection, les événements à risque sont des événements qui :

- ont été marqués comme suspects

- indiquent qu’une identité a peut-être été compromise.

Cette rubrique présente en détail les différents types d’événements à risque disponibles.


## Informations d’identification divulguées

Il arrive que les chercheurs en sécurité de Microsoft découvrent des informations d’identification divulguées dans le web invisible. Ces informations d’identification sont généralement publiées en texte brut. Elles sont comparées aux informations d’identification d’Azure AD, et s’il existe une correspondance, elles sont signalées comme étant des « informations d’identification divulguées » dans Identity Protection.

Les événements à risque liés à des informations d’identification divulguées sont définis sur un niveau de gravité « Élevé », car ils indiquent clairement que le nom d’utilisateur et le mot de passe sont à la disposition de personnes malveillantes.

## Voyage impossible vers des emplacements inhabituels

Ce type d’événement à risque identifie deux connexions depuis des emplacements géographiquement distants, dont l’un au moins un est inhabituel pour l’utilisateur compte tenu de son comportement passé. De plus, le délai écoulé entre les deux connexions est inférieur au temps nécessaire pour se déplacer du premier emplacement au second, ce qui indique qu’un autre utilisateur utilise les mêmes informations d’identification.

Il s’agit d’un algorithme d’apprentissage automatique qui ignore les « *faux positifs* » évidents contribuant à la condition de voyage impossible, tels que les VPN et les emplacements régulièrement utilisés par d’autres membres de l’organisation. Le système présente une période d’apprentissage initiale de 14 jours lui servant à assimiler le comportement de connexion des nouveaux utilisateurs.

En règle générale, les événements de ce type donnent une bonne indication qu’un pirate est parvenu à se connecter avec le compte correspondant. Cependant, des faux positifs peuvent se produire lorsqu’un utilisateur en déplacement utilise un nouvel appareil ou un VPN qu’aucun autre membre de l’organisation n’utilise. Des faux positifs peuvent également survenir si des applications transmettent incorrectement des adresses IP de serveurs en tant qu’adresses IP de clients, ce qui peut donner l’impression que les connexions se font depuis le centre de données où le serveur principal de cette application est hébergé (il s’agit souvent d’un centre de données Microsoft, ce qui peut donner l’impression que les connexions se font depuis des adresses IP appartenant à Microsoft). En raison de ces faux positifs, le niveau de risque de cet événement est défini sur « **Moyen** ».

##Connexions depuis des appareils infectés

Ce type d’événement à risque identifie les connexions depuis des appareils infectés par des logiciels malveillants, qui sont connus pour communiquer activement avec un serveur robot, grâce à la mise en corrélation des adresses IP des appareils des utilisateurs avec des adresses ayant été en contact avec un serveur robot.

Cet événement à risque identifie les adresses IP, pas les appareils des utilisateurs. Si plusieurs appareils utilisent une même adresse IP, mais que seuls certains sont contrôlés par un réseau de robots, les connexions depuis les autres appareils peuvent déclencher inutilement cet événement. C’est pourquoi le niveau de risque de ce type d’événement est défini sur « **Faible** ».

Nous vous recommandons de contacter l’utilisateur et d’analyser tous ses appareils. Il est également possible que les appareils personnels d’un utilisateur soient infectés ou, comme indiqué précédemment, qu’une tierce personne utilisait un appareil infecté depuis la même adresse IP. Les appareils infectés le sont souvent par des logiciels malveillants qui n’ont pas encore été identifiés par les logiciels antivirus et dont la présence peut être le signe de mauvaises habitudes de la part de l’utilisateur.

Pour plus d'informations sur le traitement des infections de logiciels malveillants, consultez le [Centre de protection contre les programmes malveillants](http://go.microsoft.com/fwlink/?linkid=335773&clcid=0x409).


## Connexions depuis des adresses IP anonymes

Ce type d’événement à risque signale les utilisateurs qui se sont connectés depuis une adresse IP ayant été identifiée comme l’adresse IP d’un proxy anonyme. Ces proxys sont utilisés par des individus souhaitant masquer l’adresse IP de leur appareil et peuvent être utilisés dans un but malveillant.

Nous vous recommandons de contacter immédiatement l’utilisateur pour vérifier s’il utilisait une adresse IP anonyme. Le niveau de risque de ce type d’événement risque est défini sur « **Moyen** », car une adresse IP anonyme n’est pas en soi une indication forte qu’un compte a été compromis.

## Connexions depuis des adresses IP avec des activités suspectes

Ce type d’événement à risque identifie les adresses IP depuis lesquelles un grand nombre de tentatives de connexion ayant échoué ont été constatées, pour plusieurs comptes d’utilisateurs et sur une courte période. Cela correspond aux modèles de trafic des adresses IP utilisées par les cybercriminels et donne une indication forte que les comptes sont déjà compromis ou sont sur le point de l’être. Il s’agit d’un algorithme d’apprentissage automatique qui ignore les « *faux positifs* » évidents, tels que les adresses IP régulièrement utilisées par d’autres membres de l’organisation. Le système présente une période d’apprentissage initiale de 14 jours lui servant à assimiler le comportement de connexion des nouveaux utilisateurs et clients.

Nous vous conseillons de contacter l’utilisateur pour vérifier s’il s’est réellement connecté depuis une adresse IP qui a été signalée comme suspecte. Le niveau de risque pour ce type d’événement est défini sur « **Moyen** », car il arrive que plusieurs appareils utilisent la même adresse IP, mais que seuls certains soient responsables des activités suspectes.


## Connexions depuis des emplacements non connus

Ce type d’événement à risque est un mécanisme d’évaluation de connexion en temps réel qui prend en compte les emplacements de connexion passés (IP, latitude/longitude et NSA) pour déterminer les emplacements non connus/nouveaux. Le système stocke les informations sur les emplacements précédents d’un utilisateur et considère ces emplacements comme « connus ». L’événement à risque est déclenché lorsque la connexion a lieu depuis un emplacement qui ne figure pas dans la liste des emplacements connus. Le système présente une période d’apprentissage initiale de 14 jours, durant laquelle il ne signale pas les nouveaux emplacements en tant qu’emplacements non connus. Le système ignore également les connexions depuis les appareils connus et les emplacements géographiquement proches d’un emplacement connu. <br> Les emplacements non connus peuvent donner une indication forte qu’un pirate tente d’utiliser une identité volée. Des faux positifs peuvent se produire lorsqu’un utilisateur est en déplacement, essaie un nouvel appareil ou utilise un nouveau VPN. C’est pourquoi le niveau de risque de ce type d’événement est défini sur « **Moyen** ».





## Voir aussi

- [Azure Active Directory Identity Protection](active-directory-identityprotection.md)

<!---------HONumber=AcomDC_0309_2016-->