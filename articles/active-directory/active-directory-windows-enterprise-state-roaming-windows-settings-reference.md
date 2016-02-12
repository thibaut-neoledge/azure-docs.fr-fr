<properties
	pageTitle="Référence des paramètres d’itinérance Windows 10 | Microsoft Azure"
	description="Liste complète de tous les paramètres destinés à l’itinérance ou à la sauvegarde dans Windows 10."
	services="active-directory"
    keywords="Enterprise State Roaming, Cloud windows"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"  
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="femila"/>

# Référence des paramètres d’itinérance Windows 10

Voici une liste complète de tous les paramètres destinés à l’itinérance ou à la sauvegarde dans Windows 10.

##Appareils et points de terminaison
Consultez le tableau suivant pour obtenir un récapitulatif des périphériques et des types de comptes pris en charge par la structure de synchronisation, de sauvegarde et de restauration framework dans Windows 10.

| Type de compte et fonctionnement | Bureau | Mobile |
|----------------------------|---------|--------|
|Azure Active Directory : synchronisation| Oui | Non |
|Azure Active Directory : sauvegarde/restauration| Non | Non |
|Compte Microsoft : synchronisation|Oui | Oui |
|Compte Microsoft : sauvegarde/restauration| Non | Oui |


##Qu’est-ce qu’une sauvegarde ?
En règle générale, les paramètres Windows se synchronisent par défaut, cependant, certains paramètres sont simplement sauvegardés, notamment la liste des applications installées sur un périphérique. Si, sur le périphérique , un utilisateur désactive la synchronisation avec l’application Settings, les données de l’application normalement synchronisées se transforment en simple sauvegarde. Les données de sauvegarde sont uniquement accessibles via l’opération de restauration lors de la première utilisation d’un nouvel appareil. Les sauvegardes peuvent être désactivées via les paramètres du périphérique et peuvent être gérées et supprimées via le compte OneDrive de l’utilisateur.

## Vue d’ensemble des paramètres Windows
Les groupes de paramètres suivants sont disponibles pour les utilisateurs finaux et servent à activer/désactiver la synchronisation des paramètres sur les appareils Windows 10.

- Thème : Arrière-plan du Bureau, Vignette de l’utilisateur, Position de la barre des tâches, etc. 
- Paramètres Internet Explorer : Historique de navigation, URL saisies, Favoris, etc. 
- Mots de passe : [Stockage sécurisé des informations d’identification Windows](https://technet.microsoft.com/library/jj554668.aspx), et notamment des profils Wi-Fi 
- Préférences linguistiques : Dictionnaire, Paramètres de langue système 
- Options d’ergonomie : Narrateur, Loupe, Clavier visuel 
- Autres paramètres Windows : consultez les détails de Paramètres Windows

![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-individual-sync-settings.png)
 
## Détails des Paramètres Windows
Dans le tableau qui suit, les autres entrées de la colonne Groupe de paramètres font référence aux paramètres pouvant être désactivés en accédant à Paramètres > Comptes > Synchroniser vos paramètres > Autres Paramètres Windows.

Les entrées internes dans la colonne Groupe de paramètres font référence aux paramètres et aux applications qui ne peuvent être désactivées de la synchronisation qu’au sein de l’application elle-même ou par la désactivation de l’ensemble du périphérique à l’aide des paramètres Gestion des périphériques mobiles ou des paramètres de stratégie de groupe. Les paramètres non itinérants ou la synchronisation n’appartiennent pas à un groupe.


| Paramètres | Bureau | Mobile | Groupe |
|----------------------------------|---------|---------|-------|
| **Comptes** : avatar du compte | sync |X |Thème |
| **Comptes** : autres paramètres de compte |X |X | |
| **Bande passante mobile avancée** : nom de réseau de partage de connexion Internet (active la détection automatique des points d’accès Wi-Fi mobiles Bluetooth)|sync |sync |Mot de passe |
|**Données d’application** : les applications individuelles peuvent synchroniser des données|sauvegarde de synchronisation | sauvegarde de synchronisation|Interne |
|**Liste d’applications** : liste des applications installées |X |backup |Autres |
|**Bluetooth** : tous les paramètres Bluetooth |X |X | |
|**Invite de commande** : tous les paramètres d’invite de commande |sync| |X |Autres
|**Cortana** : activer ou désactiver |X |X | |
|**Cortana** : activer Cortana sur l’écran de verrouillage |X |X | |
|**Cortana** : nom d’utilisateur |sync |sync |Interne|
|**Cortana** : lire les SMS à haute voix |X |sync |Interne|
|**Cortana** : recherche sécurisée |X |sync |Interne|
|**Cortana** : recherche des informations sur les vols et bien plus encore|X |sync |Interne|
|**Informations d’identification** : stockage sécurisé des informations d’identification |sync |sync |password|
|**Date, heure et région** : horaire automatique (synchronisation Internet) |sync |sync |language|
|**Date, heure et région** : 24 heures|sync |sync |language|
|**Date, heure et région** : date et heure|sync |X |language|
|**Date, heure et région** : fuseau horaire | |X |language|
|**Date, heure et région** : heure d’été|sync |X |language|
|**Date, heure et région** : pays/région |sync |X |language|
|**Date, heure et région** : premier jour de la semaine |sync |X |language|
|**Date, heure et région** : format régional (paramètres régionaux) |sync |X |language|
|**Date, heure et région** : date courte |sync |X |language|
|**Date, heure et région** : date longue |sync |X |language|
|**Date, heure et région** : heure courte |sync |X |language|
|**Date, heure et région** : heure complète |sync |X |language|
|**Personnalisation du bureau** : thème du bureau (arrière-plan, couleur système, sons système par défaut, écran de veille) |sync |X |Thème|
|**Personnalisation du bureau** : papier peint en diaporama |sync |X |Thème|
|**Personnalisation du bureau** : paramètres de la barre des tâches (position, masquage automatique, etc.). |sync |X |Thème|
|**Personnalisation du bureau** : disposition de l’écran d’accueil |sync |backup ||
|**Périphériques** : imprimantes partagées auxquelles vous êtes connectées. |sync | X |autres |
|**Navigateur Edge** : liste de lecture |sync |sync |Interne|
|**Navigateur Edge** : favoris |sync |sync |Interne|
|**Navigateur Edge** : tous les autres paramètres Edge|X |X ||
|**Contraste élevé** : activé ou désactivé |sync |sync |options d’ergonomie|
|**Contraste élevé** : paramètres du thème|sync |X ||options d’ergonomie|
|**Internet Explorer** : ouvrir les onglets (URL et titre)|sync |sync |Internet Explorer|
|**Internet Explorer** : liste de lecture|sync |sync |Internet Explorer|
|**Internet Explorer** : URL saisies|sync |sync |Internet Explorer|
|**Internet Explorer** : historique de navigation|sync |sync |Internet Explorer|
|**Internet Explorer** : favoris|sync |sync |Internet Explorer|
|**Internet Explorer** : URL exclues|sync |sync |Internet Explorer|
|**Internet Explorer** : pages d’accueil|sync |sync |Internet Explorer|
|**Internet Explorer** : suggestions de domaine|sync |sync |Internet Explorer|
|**Clavier** : les utilisateurs peuvent activer/désactiver le clavier visuel|sync |X |options d’ergonomie|
|**Clavier** : activer un oui rémanent (désactivé par défaut)|sync |X |options d’ergonomie|
|**Clavier** : activer les clés de filtre (désactivé par défaut)|sync |X |options d’ergonomie|
|**Clavier** : activer les touches bascules (désactivé par défaut)|sync |X |options d’ergonomie|
|**Internet Explorer** : langue du domaine : chinois (CHS) QWERTY – activer l’apprentissage automatique|sync |X |Langage|
|**Langue** : QWERTY CHS - activer le classement c&idate dynamique|sync |X |Langage|
|**Langue** : QWERTY CHS - jeu de caractères chinois simplifié|sync |X |Langage|
|**Langue** : QWERTY CHS - jeu de caractères chinois traditionnel|sync |X |Langage|
|**Langue** : QWERTY CHS - pinyin approximatif|sync |sync |Langage|
|**Langue** : QWERTY CHS - paires approximatives|sync |sync |Langage|
|**Langue** : QWERTY CHS - pinyin complet||sync |X |Langage|
|**Langue** : QWERTY CHS - pinyin double|sync |X |Langage|
|**Langue** : QWERTY CHS - lecture de correction automatique|sync |X |Langage|
|**Langue** : QWERTY CHS - touche bascule C/E, MAJ|sync |X |Langage|
|**Langue** : QWERTY CHS - touche bascule C/E, Ctrl|sync |X |Langage|
|**Langue** : WUBI CHS - mode de saisie de caractère unique |sync |X |Langage|
|**Langue** : WUBI CHS - afficher le codage de c&idate restant |sync |X |Langage|
|**Langue** : WUBI CHS - émission d’un bip sonore lorsque le codage des 4 coins est non valide|sync |X |Langage|
|**Langue** : Bopomofo CHS - inclure CJK Ext-A|sync |X |Langage|
|**Langue** : IME japonais - frappe prédictive et mots personnalisés|sync |sync |Langage|
|**Langue** : coréen (KOR) IME|X |X |Langage|
|**Langue** : reconnaissance d’écriture manuscrite|X |X |Langage|
|**Langue** : profil de langue|sync |backup |Langage|
|**Langue** : vérification orthographique et mise en évidence des fautes d’orthographe|sync |backup |Langage|
|**Langue** : liste de claviers|sync |backup |Langage|
|**Écran de verrouillage** : tous les paramètres de verrouillage|X |X ||
|**Loupe** : activer ou désactiver (bascule maître)|X |backup |Options d’ergonomie|
|**Loupe** : activer ou désactiver les couleurs d’inversion (désactivé par défaut)|sync |X |Options d’ergonomie|
|**Loupe** : suivre les éléments sélectionnés au clavier|sync |X |Options d’ergonomie|
|**Loupe** : suivre le curseur de la souris|sync |X |Options d’ergonomie|
|**Loupe** : démarrer lorsque les utilisateurs se connectent (désactivé par défaut)|sync |X |Options d’ergonomie|
|**Souris** : modifier la taille du curseur de la souris|sync |X |autres|
|**Souris** : modifier la couleur du curseur de la souris|sync |X |autres|
|**Souris** : tous les autres paramètres|X |X ||
|**Narrateur** : lancement rapide|sync |X |Options d’ergonomie|
|**Narrateur** : les utilisateurs peuvent modifier la tonalité du narrateur|sync |X |Options d’ergonomie|
|**Narrateur** : les utilisateurs peuvent activer ou désactiver les conseils de lecture du narrateur pour les éléments communs (activé par défaut)|sync |X |Options d’ergonomie|
|**Narrateur** : les utilisateurs peuvent l’activer ou le désactiver s’ils peuvent entendre des caractères tapés (activé par défaut)|sync |X |Options d’ergonomie|
|**Narrateur** : les utilisateurs peuvent décider d’activer ou de désactiver le narrateur s’ils entendent les mots (activé par défaut)|sync |X |Options d’ergonomie|
|**Narrateur** : insérer le curseur après le Narrateur (activé par défaut)|sync |X |Options d’ergonomie|
|**Narrateur** : activer la mise en évidence visuelle du curseur du Narrateur (activé par défaut)|sync |X |Options d’ergonomie|
|**Narrateur** : lire les pistes audio (activé par défaut)|sync |X |Options d’ergonomie|
|**Narrateur** : activer les touches du clavier tactile lorsque vous levez le doigt (désactivé par défaut)|sync |sync |Options d’ergonomie|
|**Options d’ergonomie** : définir l’épaisseur du curseur clignotant|sync |X |Options d’ergonomie|
|**Options d’ergonomie** : supprimer des images d’arrière-plan (désactivé par défaut)|sync |X |Options d’ergonomie|
|**Alimentation et mise en veille** : tous les paramètres|X |X ||
|**Personnalisation de l’écran d’accueil** : couleur système|sync |sync |Thème|
|**Frappe** : dictionnaire|sync |backup |Langage|
|**Frappe** : correction automatique du mot mal orthographié|sync |backup |Langage|
|**Frappe** : mettre en surbrillance les mots mal orthographiés|sync |backup |Langage|
|**Frappe** : afficher les suggestions de texte en cours de frappe|sync |backup |Langage|
|**Frappe** : ajouter un espace après la sélection d’une suggestion de texte|sync |backup |Langage|
|**Frappe** : ajouter un point après un double-clic sur la barre d’espace|sync |backup |Langage|
|**Frappe** : mettre une majuscule sur la première lettre de chaque phrase|sync |backup |Langage|
|**Frappe** : utiliser des lettres majuscules lorsque je double-clique sur la touche Maj.|sync |backup |Langage|
|**Frappe** : les touches émettent des sons pendant la frappe|sync |backup |Langage|
|**Frappe** : données de personnalisation pour le clavier tactile|sync |backup |Langage|
|**Wi-Fi** : profils Wi-Fi (WPA uniquement)|sync |sync |Mot de passe|


## Rubriques connexes
- [Présentation d’Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
- [Activer Enterprise State Roaming dans Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md)
- [FAQ sur l’itinérance des paramètres et des données](active-directory-windows-enterprise-state-roaming-faqs.md)
- [Paramètres de stratégie de groupe et de MDM pour la synchronisation des paramètres](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)






  

<!---HONumber=AcomDC_0204_2016-->