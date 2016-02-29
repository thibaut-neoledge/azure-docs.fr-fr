<properties 
	pageTitle="Authentification LDAP et serveur Azure Multi-Factor Authentication" 
	description="Il s'agit de la page d'authentification multifacteur Azure qui facilite le déploiement de l’authentification LDAP et du serveur Azure Multi-Factor Authentication." 
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

# Authentification LDAP et serveur Azure Multi-Factor Authentication 


Par défaut, le serveur Azure Multi-Factor Authentication est configuré pour importer ou synchroniser des utilisateurs à partir d'Active Directory. Toutefois, il peut être configuré pour être lié à d’autres répertoires LDAP, comme un répertoire ADAM ou un contrôleur de domaine Active Directory spécifique. Lorsqu’il est configuré pour se connecter à un répertoire via LDAP, le serveur Azure Multi-Factor Authentication peut servir de proxy LDAP pour effectuer les authentifications. Il permet également d’utiliser une liaison LDAP comme cible RADIUS, pour la pré-authentification des utilisateurs avec l'authentification IIS ou pour l'authentification principale dans le portail de l’utilisateur Azure Multi-Factor Authentication.

Lorsque vous utilisez Azure Multi-Factor Authentication comme un proxy LDAP, le serveur Azure Multi-Factor Authentication est inséré entre le client LDAP (par exemple, un équipement VPN, une application) et le serveur du répertoire LDAP afin d'ajouter l'authentification multifacteur. Pour garantir le fonctionnement Azure Multi-Factor Authentication, le serveur Azure Multi-Factor Authentication doit être configuré pour communiquer avec les serveurs clients et le répertoire LDAP. Dans cette configuration, le serveur Azure Multi-Factor Authentication accepte les requêtes LDAP provenant des serveurs clients et des applications, puis les transmet au serveur de répertoire LDAP cible pour valider les informations d'identification principales. Si la réponse de l'annuaire LDAP indique que les informations d'identification principales sont valides, Azure multi-Factor Authentication effectue l'authentification de second facteur et renvoie une réponse au client LDAP. L'authentification est complète uniquement si l'authentification auprès du serveur LDAP et l'authentification multifacteur réussissent.





## Configuration de l'authentification LDAP


Pour configurer l'authentification LDAP, installez le serveur Azure Multi-Factor Authentication sur un serveur Windows. Procédez comme suit :

1. Depuis le serveur Azure Multi-Factor Authentication, cliquez sur l'icône de l'authentification LDAP dans le menu de gauche.
2. Cochez la case Activer l'authentification LDAP.![Authentification LDAP](./media/multi-factor-authentication-get-started-server-ldap/ldap2.png) 
3. Sous l'onglet Clients, modifiez le port TCP et le port SSL si le service LDAP Azure Multi-Factor Authentication doit être lié à des ports non standard pour écouter les requêtes LDAP provenant des clients à configurer.
4. Si vous envisagez d'utiliser LDAPS du client au serveur Azure Multi-Factor Authentication, un certificat SSL doit être installé sur le serveur où il est exécuté. Cliquez sur le bouton Parcourir... en regard de la zone Certificat SSL et sélectionnez le certificat installé qui sera utilisé pour la connexion sécurisée. 
5. Cliquez sur le bouton Ajouter...
6. Dans la boîte de dialogue Ajouter un client LDAP, entrez l'adresse IP de l'appliance, du serveur ou de l'application qui s'authentifieront auprès du serveur, et un nom d’application (facultatif). Le nom de l'application apparaît dans les rapports du serveur Azure Multi-Factor Authentication et peut être affiché dans les messages d'authentification SMS ou d’application mobile.
7. Cochez la case de correspondance d'utilisateur Authentification multifacteur Azure requise si tous les utilisateurs ont été ou seront importés sur le serveur et soumis à l'authentification multifacteur. Si un grand nombre d'utilisateurs n'ont pas encore été importés sur le serveur et/ou ne seront pas soumis à l'authentification multifacteur, laissez la case désactivée. Voir le fichier d’aide pour plus d’informations sur cette fonctionnalité. 
8. Vous pouvez répéter les étapes 5 à 7 pour ajouter d'autres clients LDAP.
9. Lorsque l'authentification multifacteur Azure est configurée pour recevoir des authentifications LDAP, il doit transférer via un proxy ces authentifications au répertoire LDAP. Par conséquent, l'onglet Cible affiche une seule option grisée pour utiliser une cible LDAP. Pour configurer la connexion au répertoire LDAP, cliquez sur l'icône Intégration de répertoires. 
10. Sous l'onglet Paramètres, sélectionnez le bouton radio Utiliser une configuration LDAP spécifique.
11. Cliquez sur le bouton Edition...
12. Dans la boîte de dialogue Modifier la configuration LDAP, remplissez les champs avec les informations requises pour se connecter au répertoire LDAP. Les champs sont décrits dans le tableau ci-dessous. Remarque : cette information est également incluse dans le fichier d'aide du serveur Azure Multi-Factor Authentication.![Intégration de répertoires](./media/multi-factor-authentication-get-started-server-ldap/ldap.png) 
13. Testez la connexion LDAP en cliquant sur le bouton Test.
14. Si le test de connexion LDAP a réussi, cliquez sur le bouton OK. 
15. Cliquez sur l'onglet Filtres. Le serveur est préconfiguré pour charger des conteneurs, des groupes de sécurité et des utilisateurs à partir d'Active Directory. En cas de liaison vers un autre répertoire LDAP, vous devrez probablement modifier les filtres affichés. Cliquez sur le lien Aide pour plus d'informations sur les filtres.
16. Cliquez sur onglet Attributs. Le serveur est préconfiguré pour mapper les attributs à partir d’Active Directory.
17. En cas de liaison vers un autre répertoire LDAP ou pour modifier les mappages d'attributs préconfigurés, cliquez sur le bouton Edition...
18. Dans la boîte de dialogue Modifier les attributs, modifiez les mappages d'attributs LDAP de votre répertoire. Les noms d'attributs peuvent être saisis ou sélectionnés en cliquant sur le bouton ... en regard de chaque champ.
19. Cliquez sur le lien Aide pour plus d'informations sur les attributs.
20. Cliquez sur le bouton OK.
21. Cliquez sur l'icône Paramètres de la société et sélectionnez l'onglet Résolution du nom d'utilisateur. 22. Si vous vous connectez à Active Directory à partir d'un serveur appartenant à un domaine, vous devriez laisser le bouton radio Utiliser les identificateurs de sécurité Windows (SID) pour établir les correspondances entre les noms d’utilisateur. Sinon, sélectionnez le bouton radio Utiliser l'attribut d'identificateur unique LDAP pour établir les correspondances entre les noms d'utilisateur. Lorsqu’il est sélectionné, le serveur Azure Multi-Factor Authentication tentera de résoudre chaque nom d'utilisateur en un identificateur unique dans le répertoire LDAP. Une recherche LDAP sera effectuée sur les attributs Nom d’utilisateur définis dans l’onglet Intégration des répertoires -> Attributs. Lorsqu'un utilisateur s'authentifie, le nom d'utilisateur sera résolu en un identificateur unique dans le répertoire LDAP, et l'identificateur unique sera utilisé pour établir la correspondance avec l'utilisateur dans le fichier de données Azure Multi-Factor Authentication. Cela permet d’effectuer des comparaisons sans respect de la casse et d’utiliser des formats de nom d'utilisateur longs et courts. Cela conclut la configuration du serveur Azure Multi-Factor Authentication. Le serveur écoute maintenant les ports configurés pour les demandes d'accès LDAP provenant des clients configurés, et transmet par proxy ces demandes au répertoire LDAP pour l'authentification.


## Configuration du client LDAP

Pour configurer le client LDAP, suivez ces indications :

- Configurez votre appliance, serveur ou application pour s'authentifier via LDAP auprès du serveur Azure Multi-Factor Authentication comme s'il s'agissait de votre répertoire LDAP. Vous devriez utiliser les mêmes paramètres que vous utiliseriez normalement pour vous connecter directement à votre répertoire LDAP, sauf pour le nom du serveur ou l'adresse IP qui correspond à celui/celle du serveur Azure Multi-Factor Authentication. 
- Configurez le délai d'expiration LDAP sur 30 à 60 secondes pour pouvoir valider les informations d'identification de l'utilisateur auprès du répertoire LDAP, effectuez l'authentification de second facteur, recevez leur réponse et répondez à la demande d'accès LDAP. 
- Si vous utilisez LDAPS, l’appliance ou le serveur qui effectue les requêtes LDAP doit approuver le certificat SSL installé sur le serveur Azure Multi-Factor Authentication.

<!---HONumber=AcomDC_0218_2016-->