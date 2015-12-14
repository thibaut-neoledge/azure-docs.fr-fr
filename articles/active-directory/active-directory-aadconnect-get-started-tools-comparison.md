<properties
	pageTitle="Azure AD Connect : comparaison des outils d’intégration de répertoire | Microsoft Azure"
	description="Cette page vous donne accès à des tableaux complets de comparaison des différents outils d’intégration de répertoire."
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
	ms.date="10/13/2015"
	ms.author="billmath"/>

# Comparaison des outils d’intégration de répertoire

Au fil des années, les outils d’intégration de répertoire ont évolué et se sont améliorés. Ce document procure une vue d’ensemble consolidée de ces outils et compare les fonctions disponibles dans chacun d’entre eux.

>[AZURE.NOTE]Azure AD Connect comprend les composants et les fonctionnalités précédemment publiés sous le nom de synchronisation d’annuaires et AAD Sync. Ces outils ne sont plus commercialisés individuellement et toutes les améliorations à venir figureront dans les mises à jour d’Azure AD Connect, afin que vous sachiez toujours où obtenir les dernières fonctionnalités.
>
>Actuellement, Dirsync est toujours pris en charge, même si son obsolescence est programmée. Une fois qu’il sera obsolète, il sera pris en charge pendant une période limitée uniquement. À l’issue de cette période, la prise en charge de Dirsync sera interrompue.


Le code suivant s’applique aux tableaux ci-dessous.

● = Disponible maintenant</br> VU = Version ultérieure</br> PP = Version préliminaire publique</br>


## Synchronisation des ressources locales avec le cloud

| Fonctionnalité | Azure Active Directory Connect | Services de synchronisation Azure Active Directory (AAD Sync) | Outil de synchronisation Azure Active Directory (DirSync)| Forefront Identity Manager 2010 R2 (FIM) |
| :-------- |:--------:|:--------:|:--------:|:--------:
| Connexion à une forêt AD locale | ● | ● | ● | ● |
| Connexion à plusieurs forêts AD locales |● | ● | | ● |
| Connexion à plusieurs organisations Exchange locales | ● | | | |
| Connexion à un répertoire LDAP local | FR | | | ● |
| Connexion à plusieurs répertoires LDAP |FR | | | ● |
| Connexion à des répertoires locaux AD et LDAP |FR | | | ● |
| Connexion à des systèmes personnalisés (comme SQL, Oracle, MySQL, etc.) | FR | | | ● |
| Synchronisation des attributs définis par le client (extensions de répertoire) | ● | | | |

## Synchronisation du cloud avec les ressources locales

| Fonctionnalité | Azure Active Directory Connect | Services de synchronisation Azure Active Directory | Outil de synchronisation Azure Active Directory (DirSync) | Forefront Identity Manager 2010 R2 (FIM) |
| :-------- |:--------:|:--------:|:--------:|:--------:
| Écriture différée des appareils | ● | | ● | |
| Écriture différée des attributs (pour un déploiement hybride Exchange) | ● | ● | ● | ● |
| Écriture différée des objets utilisateurs et groupes | ●| | | |
| Écriture différée des mots de passe (à partir de la réinitialisation de mot de passe libre-service et de la modification de mot de passe) | ● | ● | | |



## Prise en charge des fonctionnalités d’authentification

| Fonctionnalité | Azure Active Directory Connect | Services de synchronisation Azure Active Directory | Outil de synchronisation Azure Active Directory (DirSync) | Forefront Identity Manager 2010 R2 (FIM) |
| :-------- |:--------:|:--------:|:--------:|:--------:
| Synchronisation de mot de passe pour une forêt AD locale | ● | ● | ● | |
| Synchronisation de mot de passe pour plusieurs forêts AD locales | ●| ● | | |
| Authentification unique à l’aide de la fédération | ● | ● | ● | ● |
| Écriture différée des mots de passe (à partir de la réinitialisation de mot de passe libre-service et de la modification de mot de passe) |● | ● | | |



## Configuration et installation

| Fonctionnalité | Azure Active Directory Connect | Services de synchronisation Azure Active Directory | Outil de synchronisation Azure Active Directory (DirSync) | Forefront Identity Manager 2010 R2 (FIM) |
| :-------- |:--------:|:--------:|:--------:|:--------:
| Installation des supports sur un contrôleur de domaine | ● | ● | ● | |
| Installation des supports à l’aide de SQL Express | ● | ● | ● | |
| Mise à niveau facile à partir de DirSync |● | | | |
| Langues de localisation de Windows Server | FR | ● | ● | |
| Prise en charge pour Windows Server 2008 et Windows Server 2008 R2 | ● pour la synchronisation, pas pour la fédération| ● | ● | ● |
| Prise en charge pour Windows Server 2012 et Windows Server 2012 R2 | ● | ● | ● | 2012 uniquement |


## Filtrage et configuration

Fonctionnalité | Azure Active Directory Connect | Services de synchronisation Azure Active Directory | Outil de synchronisation Azure Active Directory (DirSync) | Forefront Identity Manager 2010 R2 (FIM)  
:-------- |:--------:|:--------:|:--------:|:--------:|
Filtrage sur les domaines et les unités organisationnelles | ● | ● | ● | ●  
Filtrage sur les valeurs d’attributs des objets | ● | ● | ● | ●
Autorisation d’un ensemble minimal d’attributs à synchroniser (MinSync) | ● | ● | |
Autorisation de différents modèles de service à appliquer pour les flux d’attributs |● | ● | |
Autorisation du blocage des attributs circulant d’AD vers Azure AD | ● | ● | |  
Autorisation de la personnalisation avancée pour les flux d’attributs | ● | ● | | ●  

## Étapes suivantes
En savoir plus sur l’[intégration de vos identités locales à Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_1203_2015-->