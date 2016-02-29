<properties 
	pageTitle="Authentification RADIUS et serveur Azure Multi-Factor Authentication" 
	description="Il s'agit de la page d'authentification multifacteur Azure qui facilite le déploiement de l’authentification RADIUS et du serveur Azure Multi-Factor Authentication." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2016" 
	ms.author="billmath"/>



# Authentification RADIUS et serveur Azure Multi-Factor Authentication

La section Authentification RADIUS vous permet d'activer et de configurer l'authentification RADIUS pour le serveur Azure Multi-Factor Authentication. RADIUS est un protocole standard pour accepter les demandes d'authentification et traiter ces demandes. Le serveur Azure Multi-Factor Authentication fonctionne comme un serveur RADIUS et s'insère entre votre client RADIUS (par exemple, une appliance VPN) et votre cible d'authentification, qui peut être Active Directory (AD), un répertoire LDAP ou un autre serveur RADIUS, afin d'ajouter l'authentification multifacteur Azure. Pour garantir le fonctionnement Azure Multi-Factor Authentication, vous devez configurer le serveur Azure Multi-Factor Authentication pour communiquer avec les serveurs clients et la cible de l’authentication. Le serveur Azure Multi-Factor Authentication accepte les requêtes provenant d'un client RADIUS, valide les informations d'identification par rapport à la cible de l'authentification, ajoute l’authentification multifacteur Azure et renvoie une réponse au client RADIUS. L'authentification est complète uniquement si l'authentification principale et l'authentification multifacteur Azure réussissent.

>[AZURE.NOTE]
Le serveur MFA prend uniquement en charge les protocoles RADIUS PAP (Password Authentication Protocol) et MSCHAPv2 (Microsoft Challenge Handshake Authentication Protocol) lorsqu'il agit comme un serveur RADIUS. Les autres protocoles comme EAP (Extensible Authentication Protocol) peuvent être utilisés lorsque le serveur MFA agit comme un proxy RADIUS vers un autre serveur RADIUS prenant en charge ce protocole, par exemple Microsoft NPS. </br> Lorsque vous utilisez d'autres protocoles dans cette configuration, les jetons SMS et OATH unidirectionnels ne fonctionneront pas car le serveur MFA n'est pas capable d'initier correctement une réponse RADIUS Challenge à l'aide de ce protocole.


![Authentification RADIUS](./media/multi-factor-authentication-get-started-server-rdg/radius.png)

## Configuration de l'authentification RADIUS

Pour configurer l'authentification RADIUS, installez le serveur Azure Multi-Factor Authentication sur un serveur Windows. Si vous utilisez un environnement Active Directory, le serveur doit être lié au domaine à l'intérieur du réseau. Utilisez la procédure suivante pour configurer le serveur Azure Multi-Factor Authentication :

1. Depuis le serveur Azure Multi-Factor Authentication, cliquez sur l'icône de l'authentification RADIUS dans le menu de gauche.
2. Cochez la case Activer l'authentification RADIUS.
3. Sous l'onglet Clients, modifiez les ports d’authentification et de gestion si le service RADIUS Azure Multi-Factor Authentication doit être lié à des ports non standard pour écouter les requêtes RADIUS provenant des clients à configurer.
4. Cliquez sur le bouton Ajouter...
5. Dans la boîte de dialogue Ajouter un client RADIUS, entrez l'adresse IP de l'appliance ou du serveur qui s'authentifieront auprès du serveur Azure Multi-Factor Authentication, un nom d’application (facultatif) et un secret partagé. Le secret partagé doit être identique sur le serveur Azure Multi-Factor Authentication et sur l’appliance/serveur. Le nom de l'application apparaît dans les rapports du serveur Azure Multi-Factor Authentication et peut être affiché dans les messages d'authentification SMS ou d’application mobile.
6. Cochez la case de correspondance d'utilisateur Authentification multifacteur requise si tous les utilisateurs ont été ou seront importés sur le serveur et soumis à l'authentification multifacteur. Si un grand nombre d'utilisateurs n'ont pas encore été importés sur le serveur et/ou ne seront pas soumis à l'authentification multifacteur, laissez la case désactivée. Voir le fichier d’aide pour plus d’informations sur cette fonctionnalité.
7. Cochez la case Activer un jeton OATH de secours si les utilisateurs doivent utiliser l’authentification pour application mobile Azure Multi-Factor Authentication et si vous voulez utiliser les codes d’accès OATH comme authentification de secours pour les appels téléphoniques hors bande, les SMS et les notifications push.
8. Cliquez sur le bouton OK.
9. Vous pouvez répéter les étapes 4 à 8 pour ajouter d'autres clients RADIUS.
10. Cliquez sur l'onglet Cible.
11. Si le serveur Azure Multi-Factor Authentication est installé sur un serveur appartenant à un domaine dans un environnement Active Directory, sélectionnez Domaine Windows.
12. Si les utilisateurs doivent être authentifiés par rapport à un répertoire LDAP, sélectionnez la liaison LDAP. Lorsque vous utilisez la liaison LDAP, vous devez cliquer sur l'icône Intégration de répertoires et modifiez la configuration LDAP sous l'onglet Paramètres afin que le serveur puisse se lier à votre répertoire. Vous trouverez des instructions sur la configuration LDAP dans le guide de configuration du proxy LDAP. 
13. Si les utilisateurs doivent être authentifiés par rapport à un autre serveur RADIUS, sélectionnez ce serveur RADIUS.
14. Configurez le serveur vers lequel le serveur enverra demandes RADIUS par proxy en cliquant sur le bouton Ajouter...
15. Dans la boîte de dialogue Ajouter un serveur RADIUS, entrez l'adresse IP du serveur RADIUS et un secret partagé. Le secret partagé doit être identique sur le serveur Azure Multi-Factor Authentication et sur le serveur RADIUS. Modifiez le port d'authentification et le port de gestion si des ports différents sont utilisés par le serveur RADIUS.
16. Cliquez sur le bouton OK. 
17. Vous devez ajouter le serveur Azure Multi-Factor Authentication comme client RADIUS dans l'autre serveur RADIUS afin qu'il traite les demandes d'accès qu’il reçoit du serveur Azure Multi-Factor Authentication. Vous devez utiliser le même secret partagé configuré sur le serveur Azure Multi-Factor Authentication.
18. Vous pouvez répéter cette étape pour ajouter d'autres serveurs RADIUS et configurer l'ordre dans lequel le serveur doit les appeler à l’aide des boutons Monter et Descendre. Cela conclut la configuration du serveur Azure Multi-Factor Authentication. Le serveur écoute désormais sur les ports configurés pour les demandes d'accès RADIUS à partir de clients configurés.   


## Configuration du client RADIUS

Pour configurer le client RADIUS, suivez ces indications :

- Configurez votre appliance/serveur pour s'authentifier via RADIUS auprès de l’adresse IP du serveur Azure Multi-Factor Authentication, qui agira en tant que serveur RADIUS. 
- Utilisez le même secret partagé configuré ci-dessus. 
- Configurez le délai d'expiration RADIUS sur 30 à 60 secondes pour pouvoir valider les informations d'identification de l'utilisateur, effectuez l'authentification multifacteur, recevez leur réponse et répondez à la demande d'accès RADIUS.

<!---HONumber=AcomDC_0218_2016-->