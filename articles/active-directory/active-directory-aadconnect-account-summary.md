<properties 
	pageTitle="Autorisations et informations d’identification Azure AD Connect" 
	description="Description de la configuration personnalisée des informations d’identification et autorisations Azure AD Connect." 
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
	ms.date="07/02/2015" 
	ms.author="billmath"/>




# Autorisations et informations d’identification Azure AD Connect 


L’Assistant Azure AD Connect offre deux possibilités avec des exigences distinctes en matière d’autorisations :

* Dans Configuration rapide, nous avons besoin de davantage de privilèges pour configurer votre configuration facilement, sans que vous deviez créer des utilisateurs ou configurer des autorisations séparément. 

* Dans Configuration personnalisée, nous vous proposons davantage d’options, mais il existe certaines situations dans lesquelles vous devrez disposer vous-même des autorisations appropriées.


## Informations d’identification collectées et utilisation de ces dernières dans la configuration rapide

Page de l’Assistant | Informations d’identification collectées | Autorisations requises| Utilisation 
------------- | ------------- |------------- |------------- |
Connexion à Azure AD| Informations d’identification Azure Active Directory | Rôle Administrateur général dans Azure AD | <li>Activation de la synchronisation dans l’annuaire Azure AD.</li> <li>Création du compte Azure AD qui sera utilisé pour les opérations de synchronisation continue dans Azure AD.</li>
Connexion à AD DS | Informations d’identification Active Directory locales | Membre du groupe Administrateurs de l’entreprise dans Active Directory| Utilisé comme compte de connecteur AD local ; autrement dit, il s’agit du compte qui lit et écrit les informations d’annuaire pour la synchronisation.
N/D|Informations d’identification de l’utilisateur exécutant l’Assistant| Administrateur du serveur local|L’Assistant crée le compte Active Directory qui sera utilisé comme compte de connexion au service de synchronisation sur l’ordinateur local.

<br> <br>


## Informations d’identification collectées et utilisation de ces dernières dans la configuration personnalisée


Page de l’Assistant | Informations d’identification collectées | Autorisations requises| Utilisation 
------------- | ------------- |------------- |------------- 
N/D|Informations d’identification de l’utilisateur exécutant l’Assistant|Administrateur du serveur local| <li>Par défaut, l’Assistant crée le compte Active Directory qui sera utilisé comme compte de connexion au service de synchronisation sur l’ordinateur local</li><li>Nous créons uniquement le compte de connexion au service de synchronisation si l’administrateur ne spécifie pas de compte particulier</li> <li>Le compte est un utilisateur local, sauf dans le cas d’un contrôleur de domaine où le compte est un utilisateur de domaine</li> 
Page Installation des services de synchronisation, option Compte de service | Informations d’identification du compte d’utilisateur local ou AD | Utilisateur local|Si l’administrateur spécifie un compte, ce dernier est utilisé comme compte de connexion au service de synchronisation.
Connexion à Azure AD|Informations d’identification Azure Active Directory| Rôle Administrateur général dans Azure AD|L’Assistant crée le compte Active Directory qui sera utilisé comme compte de connexion au service de synchronisation sur l’ordinateur local.
Connexion de vos annuaires|Informations d’identification Active Directory locales pour chaque forêt connectée à Azure AD |<li>Le niveau minimal d’autorisations requis par l’Assistant est un utilisateur de domaine.</li> <li>Toutefois, le compte spécifié doit avoir les autorisations requises pour le scénario prévu.</li><li>Si vous envisagez de configurer la synchronisation de mot de passe dans Azure AD, vérifiez que ce compte dispose des autorisations suivantes :- Réplication des modifications de l’annuaire - Réplication de toutes les modifications de l’annuaire</li> <li>Si vous envisagez de configurer la synchronisation avec des informations d’écriture différée d’Azure AD vers votre annuaire Active Directory local, assurez-vous que le compte dispose des autorisations en écriture sur les objets et les attributs d’annuaire faisant l’objet de l’écriture différée.</li> <li>Si vous envisagez de configurer AD FS pour l’authentification, vérifiez que les informations d’identification Active Directory que vous fournissez pour la forêt dans laquelle résident vos serveurs AD FS possèdent des privilèges d’administrateur de domaine.</li><li>Consultez le tableau ci-dessous pour obtenir la liste des conditions requises supplémentaires pour votre scénario.</li>|<li>Il s'agit du compte qui sera utilisé pour le compte d’agent de gestion AD local (MA). Ce compte sera utilisé pour lire et écrire des objets et des attributs dans l’annuaire AD local pour l’opération de synchronisation continue.</li><li>Attribution des autorisations et des paramètres de contrôle d’accès adéquats pour vos options de synchronisation choisies au compte ci-dessus et à AD.</li>
Serveurs AD FS|Pour chaque serveur de la liste, l’Assistant recueille des informations d’identification si celles de l’utilisateur exécutant l’Assistant sont insuffisantes pour se connecter.|Administrateur de domaine|Installation et configuration du rôle de serveur AD FS.|
Serveurs proxy d’application web |Pour chaque serveur de la liste, l’Assistant recueille des informations d’identification si celles de l’utilisateur exécutant l’Assistant sont insuffisantes pour se connecter.|Administrateur local sur l’ordinateur cible.|Installation et configuration du rôle de serveur WAP
Informations d’identification de confiance du proxy |Informations d’identification de confiance du service de fédération (informations d’identification que le proxy utilise pour obtenir un certificat d’approbation à partir de FS) |Compte de domaine qui est un administrateur local du serveur AD FS|Inscription initiale du certificat d’approbation FS-WAP
Page Compte de service AD FS, option Utilisation d’un compte d’utilisateur de domaine|Informations d’identification du compte d’utilisateur AD|Utilisateur de domaine|Le compte d’utilisateur AD dont les informations d’identification sont fournies sera utilisé comme compte de connexion au service AD FS.



<br> <br>
## Autorisations requises pour des scénarios spécifiques

Scénario |Autorisation
------------- | ------------- |
Synchronisation de mot de passe| <li>Répliquer les changements d’annuaires</li> <li>Répliquer tous les changements d’annuaires</li>
Déploiement Exchange hybride|Consultez la page [Autorisations et attributs en écriture différée AAD Sync Office 365 Exchange hybride](https://msdn.microsoft.com/library/azure/dn757602.aspx#exchange).
Écriture différée de mot de passe | <li>Modifier le mot de passe</li><li>Réinitialiser le mot de passe</li>
Utilisateur, Groupe et Écriture différée des appareils|Autorisations en écriture sur les objets d’annuaire et les attributs qui feront l’objet de l’écriture différée
Authentification unique et AD FS| Autorisations d’administrateur de domaine dans le domaine dans lequel se trouvent vos serveurs fédérés.

<br> <br>
## Synthèse des comptes créés par Azure AD Connect



Compte créé |Autorisations attribuées | Utilisation
------------- | ------------- |------------- |
Compte Azure AD pour la synchronisation| Administrateur général|Opérations de synchronisation continue (compte MA Azure AD)
Configuration rapide : compte Active Directory utilisé pour la synchronisation|Autorisations en lecture/écriture sur l’annuaire, tel que requis pour la synchronisation et la synchronisation de mot de passe|Opérations de synchronisation continue (compte MA Azure AD)
Configuration rapide : compte de connexion au service de synchronisation | Informations d’identification de l’utilisateur exécutant l’Assistant|Compte de connexion au service de synchronisation
Configuration personnalisée : compte de connexion au service de synchronisation |N/D|Compte de connexion au service de synchronisation
AD FS :compte de service administré de groupe (gMSA) (aadcsvc$)|Utilisateur de domaine|Compte de connexion au service FS


**Ressources supplémentaires**



* [Autorisations pour la synchronisation de mot de passe](https://msdn.microsoft.com/library/azure/dn757602.aspx#psynch)
* [Autorisations pour Exchange hybride](https://msdn.microsoft.com/library/azure/dn757602.aspx#exchange)
* [Autorisations d’écriture différée du mot de passe](https://msdn.microsoft.com/library/azure/dn757602.aspx#pwriteback)
* [Installation personnalisée d’Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
* [Azure AD Connect sur MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx)
 

<!---HONumber=August15_HO6-->