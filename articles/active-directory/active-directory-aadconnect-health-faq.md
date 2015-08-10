<properties 
	pageTitle="Forum Aux Questions (FAQ) Azure AD Connect Health" 
	description="Ce FAQ répond aux questions que vous pouvez vous poser au sujet d’Azure AD Connect. Ce FAQ couvre les questions sur l'utilisation du service, notamment le modèle de facturation, les fonctionnalités, les limitations et le support." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/12/2015"
	ms.author="billmath"/>


# Forum Aux Questions (FAQ) Azure AD Connect Health

Ce FAQ répond aux questions que vous pouvez vous poser au sujet d’Azure AD Connect. Ce FAQ couvre les questions sur l'utilisation du service, notamment le modèle de facturation, les fonctionnalités, les limitations et le support.

## Questions générales



**Q : Je possède plusieurs locataires dans Azure Active Directory. Comment basculer sur le locataire associé à Azure Active Directory Premium ?**

Pour changer de locataire Azure AD, sélectionnez « Accueil » dans la barre de navigation de gauche, puis sélectionnez le nom d’utilisateur actuellement connecté dans le coin supérieur droit, puis le compte client approprié. Si le compte client n’est pas répertorié, sélectionnez Se déconnecter, puis connectez-vous avec les informations d’identification d’administrateur général du locataire Azure Active Directory Premium.


## Questions sur l’installation



**Q : Quel est l’impact de l’installation de l’agent Azure AD Connect Health sur des serveurs individuels ?**

L’incidence de l’installation de l’agent Microsoft Identity Health sur les serveurs AD FS est minime en matière de consommation du processeur, de consommation de la mémoire, d’utilisation de la bande passante réseau et du stockage.

Les valeurs ci-dessous sont des approximations.

- Consommation du processeur : environ 1 % d’augmentation
- Consommation de la mémoire : jusqu’à 10 % de la mémoire totale du système
- Utilisation de la bande passante réseau : environ 1 Mo/1 000 requêtes AD FS
>[AZURE.NOTE]Si un agent est dans l’impossibilité de communiquer à Azure, l’agent va stocker localement les données, jusqu’à une limite maximale de 10 % de la mémoire totale du système. Une fois que l’agent atteint 10 % de la mémoire physique totale, si l’agent n’a pas pu télécharger les données vers le service, les nouvelles transactions AD FS remplaceront toute transaction mise en cache, en commençant par celle qui a été traitée le moins récemment.


- Stockage de mémoire tampon locale pour l’agent AD Health : environ 20 Mo
- Stockage de données requis pour le canal d’audit


Il est recommandé de mettre en service un espace disque de 1 024 Mo (1 Go) pour permettre au canal d’audit AD FS dédié aux agents AD Health de traiter l’ensemble des données.

**Q : Devrai-je redémarrer mes serveurs durant l’installation des agents Azure AD Connect Health ?**

Non. Vous ne devrez pas redémarrer les serveurs durant l’installation des agents. Toutefois, l’exécution de certaines des étapes pré-requises peut nécessiter un redémarrage du serveur.

Par exemple, sur Windows Server 2008 R2, l’installation de .Net 4.5 Framework requiert un redémarrage du serveur.


**Q: Les services Azure AD Connect Health fonctionnent-ils par le biais d’un proxy HTTP intermédiaire ?**

Oui, le processus d’inscription et les opérations normales peuvent fonctionner par le biais d’un proxy explicite défini pour transférer les demandes HTTP sortantes. « Netsh WinHttp set Proxy » ne fonctionne pas dans ce cas, car l’agent utilise System.Net pour effectuer des requêtes web au lieu des Services HTTP Microsoft Windows.

Effectuer ceci à tout moment avant l’exécution de Register-AdHealthAgent (étape finale de l’installation)


- Étape 1 : Ajouter l’entrée au fichier machine.config


Recherchez le fichier machine.config. Le fichier est stocké à l’emplacement %windir%\Microsoft.NET\Framework64[version]\config\machine.config</li>.

Ajoutez l’entrée suivante sous l’élément <configuration></configuration> dans votre fichier machine.config.
 
		
	<system.net>  
			<defaultProxy useDefaultCredentials="true">
       		<proxy 
        usesystemdefault="true" 
        proxyaddress="http://YOUR.PROXY.HERE.com"  
        bypassonlocal="true"/>
		</defaultProxy>
	</system.net> 

 

Vous trouverez des informations supplémentaire sur <defaultProxy> [ici](https://msdn.microsoft.com/library/kd3cf2ex(v=vs.110).aspx).

Ces paramètres configurent les applications .NET au sein du système afin d’utiliser votre proxy défini de manière explicite lors de la transmission de requêtes http .NET. Il n’est pas recommandé de modifier chaque fichier app.config, car les changements seront annulés lors de la mise à jour automatique. Il vous suffit de modifier un seul fichier ; les changements survivront aux mises à jour si vous modifiez uniquement machine.config.

- Étape 2 - Configurer le proxy dans les options Internet

Ouvrez Internet Explorer -> Paramètres -> Options Internet -> Connexions -> Paramètres de réseau local.

Sélectionnez Utiliser un serveur proxy pour votre réseau local

Sélectionnez Avancé si vous disposez de ports proxy différents pour les protocoles HTTP et HTTPS sécurisé.




**Q : Les services Azure AD Connect Health prennent-ils en charge l’authentification de base lors des connexions à des proxys HTTP ?**

Non. Aucun mécanisme de définition de nom d’utilisateur/mot de passe aléatoire pour l’authentification de base n’est actuellement pris en charge.





## Questions sur les opérations



**Q : Dois-je activer l’audit sur mes serveurs proxy d’application AD FS ou mes serveurs proxy d’application web ?**

Non, il n’est pas nécessaire d’activer l’audit sur les serveurs proxy d’application AD FS ou les serveurs proxy d’application web. Il doit être activé uniquement sur les serveurs AD FS fédérés.



**Q : Comment les alertes Azure AD Connect Health sont-elles résolues ?**

Les alertes Azure AD Connect Health sont résolues en cas de condition de succès. Les agents Azure AD Connect Health détectent et signalent régulièrement les conditions de succès au service. Pour certaines alertes, la suppression s’effectue en fonction d’un intervalle de temps. Concrètement, cela signifie que si la condition d’erreur n’est pas observée dans les 48 heures suivant la génération de l’alerte, cette dernière est automatiquement résolue.




**Q : Quels paramètres de pare-feu dois-je ouvrir pour permettre le bon fonctionnement de l’agent Azure AD Connect Health ?**

Les ports TCP/UDP 80 et 443 doivent être ouverts pour permettre à l’agent Azure AD Connect Health de communiquer avec les points de terminaison de service Azure AD Health.

<!---HONumber=July15_HO5-->