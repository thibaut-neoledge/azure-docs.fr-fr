<properties
	pageTitle="Forum Aux Questions (FAQ) Azure AD Connect Health"
	description="Ce FAQ répond aux questions que vous pouvez vous poser au sujet d’Azure AD Connect. Ce FAQ couvre les questions sur l'utilisation du service, notamment le modèle de facturation, les fonctionnalités, les limitations et le support."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/21/2016"
	ms.author="vakarand"/>


# Forum Aux Questions (FAQ) Azure AD Connect Health

Ce FAQ répond aux questions que vous pouvez vous poser au sujet d’Azure AD Connect. Ce FAQ couvre les questions sur l'utilisation du service, notamment le modèle de facturation, les fonctionnalités, les limitations et le support.

## Questions générales



**Q : Je gère plusieurs annuaires Azure AD. Comment basculer sur le locataire associé à Azure Active Directory Premium ?**

Vous pouvez basculer entre les différents annuaires Azure AD en sélectionnant le nom d'utilisateur actuellement connecté dans le coin supérieur droit, puis en choisissant le compte approprié. Si le compte n’est pas répertorié, sélectionnez Se déconnecter, puis connectez-vous avec les informations d’identification d’administrateur général de l’annuaire Azure Active Directory Premium auquel il est possible de se connecter.

## Questions sur l’installation



**Q : Quel est l’impact de l’installation de l’agent Azure AD Connect Health sur des serveurs individuels ?**

L’incidence de l’installation de l’agent Microsoft Identity Health sur les serveurs AD FS est minime en matière de consommation du processeur, de consommation de la mémoire, d’utilisation de la bande passante réseau et du stockage.

Les valeurs ci-dessous sont des approximations.

- Consommation du processeur : environ 1 % d’augmentation
- Consommation de la mémoire : jusqu’à 10 % de la mémoire totale du système
- Utilisation de la bande passante réseau : environ 1 Mo/1 000 requêtes AD FS

>[AZURE.NOTE]Si l’agent ne parvient pas à communiquer avec Azure, il stocke les données localement, jusqu’à une limite maximale définie. Une fois que l'agent atteint cette limite, s’il n'a pas été en mesure de télécharger les données sur le service, les nouvelles transactions ADFS remplaceront toutes les transactions « mises en cache » sur la base « dernier événement traité ».

- Stockage de mémoire tampon locale pour l’agent AD Health : environ 20 Mo
- Stockage de données requis pour le canal d’audit


Il est recommandé de mettre en service un espace disque de 1 024 Mo (1 Go) pour permettre au canal d’audit AD FS dédié aux agents AD Health de traiter l’ensemble des données.

**Q : Devrai-je redémarrer mes serveurs durant l’installation des agents Azure AD Connect Health ?**

Non. Vous ne devrez pas redémarrer les serveurs durant l’installation des agents. Toutefois, l’exécution de certaines des étapes pré-requises peut nécessiter un redémarrage du serveur.

Par exemple, sur Windows Server 2008 R2, l’installation de .Net 4.5 Framework requiert un redémarrage du serveur.


**Q: Les services Azure AD Connect Health fonctionnent-ils par le biais d’un proxy HTTP intermédiaire ?**

Oui. Pour les opérations en cours, vous pouvez configurer l'agent Health pour transmettre les requêtes HTTP sortantes à l'aide d'un HTTP Proxy. Pour plus d’informations, consultez [Configuration des agents Azure AD Connect Health pour utiliser le proxy HTTP](active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy).

Si vous devez configurer un serveur proxy lors de l'inscription de l'agent, vous devez modifier vos paramètres de proxy Internet Explorer. <br> Ouvrez Internet Explorer -> Paramètres -> Options Internet -> Connexions -> Paramètres de réseau local.<br> Sélectionnez Utiliser un serveur proxy pour votre réseau local.<br> Sélectionnez Avancé SI vous disposez de ports proxy différents pour les protocoles HTTP et HTTPS sécurisé.<br>


**Q : Les services Azure AD Connect Health prennent-ils en charge l’authentification de base lors des connexions à des proxys HTTP ?**

Non. Aucun mécanisme de définition de nom d’utilisateur/mot de passe aléatoire pour l’authentification de base n’est actuellement pris en charge.



## Questions sur les opérations



**Q : Dois-je activer l’audit sur mes serveurs proxy d’application AD FS ou mes serveurs proxy d’application web ?**

Non, il n’est pas nécessaire d’activer l’audit sur les serveurs proxy d’application AD FS ou les serveurs proxy d’application web. Il doit être activé uniquement sur les serveurs AD FS fédérés.



**Q : Comment les alertes Azure AD Connect Health sont-elles résolues ?**

Les alertes Azure AD Connect Health sont résolues en cas de condition de succès. Les agents Azure AD Connect Health détectent et signalent régulièrement les conditions de succès au service. Pour certaines alertes, la suppression s’effectue en fonction d’un intervalle de temps. Concrètement, cela signifie que si la condition d’erreur n’est pas observée dans les 72 heures suivant la génération de l’alerte, cette dernière est automatiquement résolue.




**Q : Quels paramètres de pare-feu dois-je ouvrir pour permettre le bon fonctionnement de l’agent Azure AD Connect Health ?**

Les ports TCP/UDP 80, 443 et 5671 doivent être ouverts pour permettre à l’agent Azure AD Connect Health de communiquer avec les points de terminaison de service Azure AD Health.


**Q : Pourquoi deux serveurs avec le même nom s’affichent dans le portail Azure AD Connect Health ?**

Lorsque vous supprimez un agent à partir d’un serveur, le serveur n’est pas automatiquement supprimé du portail Azure AD Connect. Par conséquent, si vous avez supprimé manuellement un agent d’un serveur ou le serveur lui-même, vous devrez supprimer manuellement l’entrée de serveur à partir du portail Azure AD Connect Health. Pour plus d’informations, consultez [Supprimer une instance de serveur ou de service.](active-directory-aadconnect-health-operations.md#delete-a-server-or-service-instance) En outre, si vous avez recréé une image d’un serveur ou créé un nouveau serveur avec les mêmes détails (par exemple, le nom de l’ordinateur), mais que vous n’avez pas supprimé le serveur à partir du portail Azure AD Connect Health et ensuite installé l’agent sur le nouveau serveur, deux entrées pour le serveur peuvent s’afficher. Dans ce cas, vous devez supprimer l’entrée appartenant à l’ancien serveur manuellement. Les données de cette entrée seront généralement périmées.

## Liens connexes

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Installation de l'agent Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Opérations Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Utilisation d’Azure AD Connect Health avec AD FS](active-directory-aadconnect-health-adfs.md)
* [Utilisation d'Azure AD Connect Health pour la synchronisation (en Anglais)](active-directory-aadconnect-health-sync.md)
* [Historique de publication des versions d’Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)

<!---HONumber=AcomDC_0518_2016-->