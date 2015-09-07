<properties
   pageTitle="Azure AD Connect : historique de publication des versions | Microsoft Azure"
	description="Cette rubrique répertorie toutes les versions d’Azure AD Connect et d’Azure AD Sync"
	services="active-directory"
	documentationCenter=""
	authors="AndKjell"
	manager="stevenpo"
	editor=""/>

<tags
   ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="08/24/2015"
	ms.author="andkjell"/>

# Azure AD Connect : historique de publication des versions

L’équipe Azure Active Directory met régulièrement à jour Azure AD Connect avec de nouvelles fonctions et fonctionnalités. Tous les ajouts ne sont pas applicables à toutes les configurations.

Cet article est conçu pour vous aider à conserver la trace des versions qui ont été publiées, et à comprendre si vous devez ou non effectuer la mise jour vers la version la plus récente.

## 1\.0.8667.0
Publiée : août 2015

**Nouvelles fonctionnalités :**

- L’Assistant Installation d’Azure AD Connect est maintenant localisé dans toutes les langues de Windows Server.
- Prise en charge du déverrouillage de compte lors de l’utilisation de la gestion des mots de passe d’Azure AD.

**Problèmes résolus :**

- L’Assistant Installation d’Azure AD Connect se bloque si un autre utilisateur continue l’installation à la place de la personne qui a commencé l’installation.
- Si une désinstallation précédente d’Azure AD Connect échoue à désinstaller correctement Azure AD Connect Sync, il n’est pas possible de le réinstaller.
- Impossible d’installer Azure AD Connect en utilisant l’installation rapide si l’utilisateur n’est pas dans le domaine racine de la forêt, ou si une version non anglaise d’Active Directory est utilisée.
- Si le nom de domaine complet du compte d’utilisateur Active Directory ne peut pas être résolu, un message d’erreur trompeur « Échec de la validation du schéma » est affiché.
- Si le compte utilisé sur le connecteur Active Directory est changé en dehors de l’Assistant, l’Assistant échoue lors des exécutions suivantes.
- L’installation d’Azure AD Connect sur un contrôleur de domaine échoue parfois.
- Impossible d’activer et de désactiver le « Mode de préproduction » si des attributs d’extension ont été ajoutés.
- L’écriture différée de mot de passe échoue dans certaines configurations en raison d’un mot de passe incorrect sur le connecteur Active Directory.
- Impossible de mettre à niveau DirSync si dn est utilisé lors du filtrage des attributs.

## 1\.0.8641.0
Publiée : juin 2015

**Version initiale d’Azure AD Connect.**

Changement de nom d’Azure AD Sync en Azure AD Connect.

## 1\.0.494.0501
Publiée : mai 2015

**Nouvelle condition requise**

- Azure AD Sync requiert maintenant que le .NET Framework version 4.5.1 soit installé.

**Problèmes résolus :**

- L’écriture différée de mot de passe depuis Azure AD échoue avec une erreur de connectivité Service Bus.

## 1\.0.491.0413
Publiée : avril 2015

**Problèmes résolus et améliorations :**

- Le connecteur Active Directory ne traite pas correctement les suppressions si la Corbeille est activée et qu’il existe plusieurs domaines dans la forêt.
- Les performances des opérations d’importation ont été améliorées pour le connecteur Azure Active Directory.
- Quand un groupe avait dépassé la limite d’appartenance (par défaut, la limite est définie à 50 000 objets), le groupe était supprimé dans Azure Active Directory. Le nouveau comportement est le suivant : le groupe est conservé, une erreur est générée et aucune modification de nouvelle appartenance n’est exportée.
- Un nouvel objet ne peut pas être configuré si une suppression intermédiaire avec le même nom de domaine est déjà présente dans l’espace du connecteur.
- Certains objets sont marqués pour synchronisation lors d’une synchronisation différentielle, bien qu’il n’existe aucune modification intermédiaire sur l’objet.
- Forcer une synchronisation de mot de passe supprime également la liste des contrôleurs de domaine favoris.
- CSExportAnalyzer a des problèmes avec certains états des objets.

**Nouvelles fonctionnalités :**

- Une jonction peut maintenant connecter au type d’objet « ANY » dans le métaverse.

## 1\.0.485.0222
Publié : février 2015

**Améliorations :**

- Performances de l’importation améliorées.

**Problèmes résolus :**

- La synchronisation de mot de passe prend en compte l’attribut cloudFiltered utilisé par le filtrage des attributs. Les objets filtrés ne seront plus dans la portée pour la synchronisation de mot de passe.
- Dans de rares situations où la topologie avait un très grand nombre de contrôleurs de domaine, la synchronisation de mot de passe ne fonctionnait pas.
- « Arrêt serveur » lors de l’importation à partir du connecteur Azure AD après l’activation de la gestion des appareils dans Azure AD/Intune.
- La jonction d’entités de sécurité externes à partir de plusieurs domaines dans la même forêt provoque une erreur de jonction ambiguë.

## 1\.0.475.1202
Publiée : décembre 2014

**Nouvelles fonctionnalités :**

- Il est maintenant possible d’effectuer la synchronisation de mot de passe avec le filtrage basé sur les attributs. Pour plus d’informations, consultez Synchronisation de mot de passe avec le filtrage.
- L’attribut msDS-ExternalDirectoryObjectID est écrit en différé dans Active Directory. Ceci permet aux applications Office 365 utilisant OAuth2 d’accéder à la fois aux boîtes aux lettres en ligne et locales dans un déploiement hybride d’Exchange.

**Problèmes de mise à niveau résolus :**

- Une version plus récente de l’Assistant Connexion est disponible sur le serveur.
- Un chemin d’installation personnalisé était utilisé pour installer Azure AD Sync.
- Un critère de jonction personnalisée non valide bloque la mise à niveau.

**Autres correctifs :**

- Résolution du problème des modèles pour Office Professionnel Plus.
- Résolution des problèmes d’installation provoqué par des noms d’utilisateurs commençant par un tiret.
- Résolution du problème de perte de la valeur de sourceAnchor lors d’une deuxième exécution de l’Assistant Installation.
- Résolution du problème de suivi d’événements pour Windows pour la synchronisation de mot de passe

## 1\.0.470.1023
Publié : octobre 2014

**Nouvelles fonctionnalités :**

- Synchronisation de mot de passe à partir de plusieurs AD locaux vers Azure AD.
- Interface utilisateur du programme d’installation localisée dans toutes les langues de Windows Server.

**Mise à niveau à partir d’Azure Active Directory Sync 1.0 GA**

Si vous avez déjà installé Azure AD Sync, vous devez effectuer une étape supplémentaire au cas où vous avez modifié les règles de synchronisation prédéfinies. Une fois que vous avez mis à niveau vers la version 1.0.470.1023, les règles de synchronisation que vous avez modifiées sont dupliquées. Pour chaque règle de synchronisation modifiée, procédez comme suit :

- Recherchez la règle de synchronisation que vous avez modifiée et notez les modifications.
- Supprimez la règle de synchronisation.
- Recherchez la nouvelle règle de synchronisation créée par Azure AD Sync et réappliquez les modifications.

**Autorisations pour le compte Active Directory**

Le compte Active Directory doit disposer d’autorisations supplémentaires pour pouvoir lire les hachages de mot de passe à partir d’Active Directory. Les autorisations à accorder sont nommées « Réplication des changements de répertoire » et « Réplication de toutes les modifications de l’annuaire ». Les deux autorisations sont nécessaires pour pouvoir lire les hachages de mot de passe.

## 1\.0.419.0911
Publiée : septembre 2014

**Version initiale d’Azure AD Sync.**

## Ressources supplémentaires
[Azure AD Connect Sync : personnalisation des options de synchronisation](active-directory-aadconnectsync-whatis.md)

[Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=August15_HO9-->