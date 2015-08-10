<properties 
	pageTitle="Considérations relatives à la confidentialité et à la sécurité de Cloud App Discovery" 
	description="Cette rubrique décrit les considérations de sécurité et de confidentialité liées à Cloud App Discovery." 
	services="active-directory" 
	documentationCenter="" 
	authors="markusvi" 
	manager="swadhwa" 
	editor="lisatoft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/23/2015" 
	ms.author="markusvi"/>

# Considérations relatives à la confidentialité et à la sécurité de Cloud App Discovery

Microsoft s’engage à protéger votre vie privée et à sécuriser vos données, tout en proposant des logiciels et des services favorisant la gestion de la sécurité de votre organisation. <br> Nous reconnaissons que lorsque vous confiez vos données à d’autres personnes, cette confiance exige un savoir-faire et des investissements en ingénierie de sécurité rigoureux. Microsoft adhère aux recommandations strictes de conformité et de sécurité, des pratiques de cycle de vie de développement de logiciels sécurisées jusqu’à l’exploitation de service. <br> La sécurisation et la protection des données constituent une priorité de premier plan pour Microsoft.

Cette rubrique explique comment les données sont recueillies, traitées et sécurisées dans Azure Active Directory Cloud App Discovery.




##Vue d'ensemble

Cloud App Discovery est une fonctionnalité d’Azure AD hébergée dans Microsoft Azure. <br> Des agents de point de terminaison Cloud App Discovery sont utilisés pour recueillir des données de détection d’application à partir d’ordinateurs gérés par le service Informatique. <br> Les données recueillies sont envoyées en toute sécurité via un canal chiffré au service Azure AD Cloud App Discovery. <br> Les données Cloud App Discovery pour une organisation sont ensuite visibles dans le portail Azure.


<center>![Fonctionnement de Cloud App Discovery](./media/active-directory-cloudappdiscovery-security-and-privacy-considerations/cad01.png)</center>


Les sections ci-dessous suivent le flux d’informations et décrivent comment elles sont sécurisées à mesure qu’elles transitent de votre organisation vers le service Cloud App Discovery et le portail Cloud App Discovery.



## Collecte de données de votre organisation

Pour utiliser la fonctionnalité Cloud App Discovery d’Azure Active Directory pour obtenir des informations sur les applications utilisées par les employés de votre organisation, vous devez tout d’abord déployer des agents de point de terminaison Azure AD Cloud App Discovery sur les ordinateurs de votre organisation.

Les administrateurs du client Azure Active Directory (ou leur délégué) peuvent télécharger le package d’installation d’agent à partir du portail Azure. Vous pouvez installer les agents manuellement ou sur plusieurs ordinateurs dans l’organisation à l’aide de SCCM ou de la stratégie de groupe.

Pour plus d’informations sur les options de déploiement, consultez le [Guide de déploiement de Cloud App Discovery avec la stratégie de groupe](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx).


### Données recueillies par l’agent

Les informations présentées dans la liste ci-dessous sont recueillies par l’agent quand des connexions sont établies à des applications web. Les informations sont recueillies uniquement pour les applications que l’administrateur a configurées pour la détection d’application. <br> La liste des applications pour lesquelles vous choisissez de recueillir des métadonnées est configurable dans le portail, sous l’onglet Paramètres.



> [AZURE.NOTE]Pour plus d’informations, consultez [Prise en main de Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx).
 
**Catégorie d’informations** : Informations utilisateur <br> **Description** :<br> Nom d’utilisateur Windows du processus qui a fait une demande à l’application web cible (par exemple : nom\_utilisateur), ainsi que l’identificateur de sécurité Windows (SID) de l’utilisateur.


**Catégorie d’informations** : Informations sur le processus <br> **Description** : <br> Nom du processus qui a fait la demande à l’application web cible (par exemple : « iexplore.exe »).

**Catégorie d’informations** : Informations sur l’ordinateurn <br> **Description** : <br> Nom NetBIOS l’ordinateur sur lequel l’agent est installé.

**Catégorie d’informations** : Informations sur le trafic d’application <br> **Description** : <br>

Les informations de connexion suivantes :

- Adresses IP source (ordinateur local) et de destination et numéros de port 

- Adresse IP publique de l’organisation par le biais de laquelle la demande est envoyée

- Heure de la demande

- Volume de trafic envoyé et reçu

- Version IP (4 ou 6)

- Pour les connexions TLS uniquement : nom d’hôte cible tiré de l’extension de l’Indication du nom du serveur ou du certificat de serveur.

Les informations HTTP suivantes :

- Méthode (GET, POST, etc.) 

- Protocole (HTTP/1.1, etc.)

- Chaîne d’agent utilisateur

- Nom d’hôte

- URI cible (à l’exclusion de la chaîne de requête)

- Informations de type de contenu

- Informations d’URL de point d’accès (à l’exception de la chaîne de requête)



> [AZURE.NOTE]Les informations HTTP ci-dessus sont recueillies pour toutes les connexions non chiffrées. Pour les connexions TLS, ces informations sont capturées uniquement quand le paramètre « Inspection approfondie » est activé dans le portail. Le paramètre est activé par défaut. Pour plus d’informations, consultez [Prise en main de Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx).
 

 
### Fonctionnement de l’agent

L’installation de l’agent comprend deux composants :

- un composant en mode utilisateur ;

- un composant de pilote en mode noyau (pilote de plateforme de filtrage Windows).



Lors de son installation initiale, l’agent stocke sur l’ordinateur un certificat approuvé spécifique à celui-ci, qu’il utilise ensuite pour établir une connexion sécurisée avec le service Cloud App Discovery. <br> L’agent extrait régulièrement la configuration de la stratégie à partir du service Cloud App Discovery via cette connexion sécurisée. <br> La stratégie comporte des informations sur les applications cloud à analyser et précise entre autres choses si la mise à jour automatique doit être activée.

Comme le trafic web est envoyé et reçu sur l’ordinateur, Cloud App Discovery analyse le trafic et extrait les métadonnées pertinentes (voir le tableau ci-dessus). <br> Chaque minute, l’agent envoie les métadonnées recueillies au service Cloud App Discovery sur un canal chiffré.

Pour les connexions chiffrées, une étape supplémentaire est effectuée dans le but d’améliorer la précision des données recueillies.<br> Il s’agit de l’inspection approfondie. <br> Le composant pilote intercepte le trafic chiffré et s’introduit dans le flux chiffré. Il crée pour cela un certificat racine auto-signé sur l’ordinateur, ce qui force l’application cliente à approuver l’agent Cloud App Discovery. Ce certificat racine auto-signé est marqué comme non exportable et une liste ACL répertorie uniquement les administrateurs. Il est censé ne jamais quitter l’ordinateur sur lequel il a été créé.


### Respect de la confidentialité de l’utilisateur

Notre objectif est de fournir aux administrateurs les outils nécessaires pour définir un équilibre entre une optique détaillée de l’utilisation d’application et la confidentialité utilisateur, conformément aux exigences de leur organisation. À cette fin, nous fournissons les boutons suivants dans la page Paramètres du portail :

- **Collection de données** : les administrateurs peuvent choisir de spécifier les applications ou les catégories d’applications pour lesquelles ils souhaitent obtenir des données de détection.


- **Inspection approfondie** : les administrateurs peuvent choisir de spécifier si l’agent recueille le trafic HTTP pour les connexions SSL/TLS.



L’agent de point de terminaison Cloud App Discovery recueille uniquement les informations décrites dans le tableau ci-dessus.



> [AZURE.NOTE]Pour plus d’informations, consultez [Prise en main de Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx).
 



## Envoi de données à Cloud App Discovery

Une fois que des métadonnées ont été recueillies par l’agent, elles sont mises en cache sur l’ordinateur pendant une minute maximum ou jusqu’à ce que les données mises en cache atteignent une taille de 5 Mo. Elles sont ensuite compressées, puis envoyées via une connexion sécurisée au service Cloud App Discovery.

Si l’agent ne peut pas communiquer avec le service Cloud App Discovery pour une raison quelconque, les métadonnées recueillies sont stockées dans un cache de fichiers local accessible uniquement par les utilisateurs disposant de privilèges sur l’ordinateur (tels que ceux appartenant au groupe Administrateurs). <br> L’agent tente automatiquement de renvoyer les métadonnées mises en cache jusqu’à ce qu’elles aient été correctement reçues par le service Cloud App Discovery.



## Réception des données à l’extrémité du service

Les agents s’authentifient auprès du service Cloud App Discovery à l’aide du certificat d’authentification client spécifique à l’ordinateur référencé ci-dessus et transfèrent les données via un canal chiffré. <br>Le pipeline d’analyse du service Cloud App Discovery traite les métadonnées de chaque client séparément en les partitionnant logiquement à toutes les étapes du pipeline d’analyse. Les métadonnées analysées permettent de générer différents rapports dans le portail.

Les métadonnées non traitées et les métadonnées analysées sont stockées pendant 180 jours. En outre, les clients peuvent choisir de capturer les métadonnées analysées dans un compte de stockage d’objets blob Azure de leur choix. Cela est utile pour effectuer une analyse hors ligne des métadonnées et pour la conservation à long terme des données.

## Accès aux données à l’aide du portail Azure

Dans le but de protéger les métadonnées recueillies, par défaut seuls les administrateurs globaux du client ont accès à la fonctionnalité Cloud App Discovery dans le portail Azure. <br> Toutefois, les administrateurs peuvent choisir de déléguer cet accès à d’autres utilisateurs ou groupes.



> [AZURE.NOTE]Pour plus d’informations, consultez [Prise en main de Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx).
 


Tout utilisateur accédant aux données dans le portail doit posséder une licence Azure AD Premium.



**Ressources supplémentaires**


* [Comment puis-je détecter les applications cloud non approuvées utilisées au sein de mon organisation ?](active-directory-cloudappdiscovery-whatis.md) 

<!---HONumber=July15_HO5-->