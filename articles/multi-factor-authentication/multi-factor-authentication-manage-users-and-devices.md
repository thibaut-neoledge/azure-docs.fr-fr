<properties 
	pageTitle="Rapports Azure Multi-Factor Authentication" 
	description="Cette section vous explique comment modifier les paramètres utilisateur tels que le fait de forcer les utilisateurs à réexécuter le processus de vérification." 
	documentationCenter="" 
	services="multi-factor-authentication" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/02/2015" 
	ms.author="billmath"/>

# Gestion des paramètres utilisateur avec Azure Multi-Factor Authentication dans le cloud

En tant qu'administrateur, vous pouvez gérer les paramètres utilisateur et périphérique.

- [Demander aux utilisateurs sélectionnés de fournir à nouveau des méthodes de contact](#require-selected-users-to-provide-contact-methods-again)
- [Supprimer les mots de passe d’application existants des utilisateurs](#delete-users-existing-app-passwords)
- [Restaurer MFA sur tous les appareils interrompus d’un utilisateur (version préliminaire publique)](#restore-mfa-on-all-suspended-devices-for-a-user)






Ceci est utile si un ordinateur ou un appareil a été perdu ou volé ou si vous devez supprimer l’accès aux utilisateurs.


## Demander aux utilisateurs sélectionnés de fournir à nouveau des méthodes de contact

Ce paramètre forcera l'utilisateur à réexécuter le processus d'inscription lorsqu'il se connecte. N'oubliez pas que les applications sans navigateur continueront de fonctionner si l'utilisateur dispose pour elles de mots de passe d'application. Vous pouvez supprimer les mots de passe d’application des utilisateurs en choisissant **Supprimer tous les mots d'application existants, générés par les utilisateurs sélectionnés**.

### Demander aux utilisateurs sélectionnés de fournir à nouveau des méthodes de contact

<ol>
<li>Connectez-vous au portail de gestion Azure.</li>
<li>Cliquez à gauche sur Active&#160;Directory.</li>
<li>Sous Directory, cliquez sur le répertoire de l'utilisateur de votre choix, pour que celui-ci vous fournisse à nouveau sa méthode de contact.</li>
<li>En haut de la page, cliquez sur Utilisateurs.</li>
<li>En bas de la page, cliquez sur Gérer Multi-Factor Auth. Cette action ouvre la page relative à l’authentification multifacteur. <li>Recherchez l'utilisateur que vous souhaitez gérer et cochez la case située à côté du nom. Vous devrez peut-être modifier l'affichage en haut de la page.</li> <li>Cela fera apparaître le lien **Gérer les paramètres utilisateur** à droite de la page. Cliquez dessus.</li> <li>Cochez la case **Demander aux utilisateurs sélectionnés de fournir à nouveau des méthodes de contact**.</li>

![Fournir des méthodes de contact](./media/multi-factor-authentication-manage-users-and-devices/reproofup.png)

<li>Cliquez sur Enregistrer.</li>
<li>Cliquez sur Fermer</li>

## Supprimer les mots de passe d’application existants des utilisateurs

Cela supprime tous les mots de passe d'application créés par un utilisateur. Les applications sans navigateur qui ont été associées à ces mots de passe d'application cesseront de fonctionner jusqu'à ce qu'un nouveau mot de passe soit créé.

### Supprimer les mots de passe d’application existants des utilisateurs

<ol>
<li>Connectez-vous au portail de gestion Azure.</li>
<li>Cliquez à gauche sur Active&#160;Directory.</li>
<li>Sous Directory, cliquez sur le répertoire de l'utilisateur pour lequel vous souhaitez supprimer les mots de passe d'application.</li>
<li>En haut de la page, cliquez sur Utilisateurs.</li>
<li>En bas de la page, cliquez sur Gérer Multi-Factor Auth. Cette action ouvre la page relative à l’authentification multifacteur. <li>Recherchez l'utilisateur que vous souhaitez gérer et cochez la case située à côté du nom. Vous devrez peut-être modifier l'affichage en haut de la page.</li> <li>Cela fera apparaître le lien **Gérer les paramètres utilisateur** à droite de la page. Cliquez dessus.</li> <li>Cochez la case **Supprimer tous les mots de passe d'application existants, générés par les utilisateurs sélectionnés**.</li> ![Supprimer des mots de passe d'application](./media/multi-factor-authentication-manage-users-and-devices/deleteapppasswords.png) <li>Cliquez sur Enregistrer.</li> <li>Cliquez sur Fermer.</li>





## Restaurer MFA sur tous les appareils interrompus d’un utilisateur

Les administrateurs ont la possibilité de réinitialiser Multi-Factor Authentication sur leurs appareils et leurs navigateurs. Il suffit pour cela de restaurer Multi-Factor Authentication pour les appareils et les navigateurs d'un utilisateur. À cette occasion, ceci supprimera l’interruption de tous les appareils et les navigateurs de l’utilisateur.

### Restaurer MFA sur tous les appareils interrompus d’un utilisateur

<ol>
<li>Connectez-vous au portail de gestion Azure.</li>
<li>Cliquez à gauche sur Active&#160;Directory.</li>
<li>Sous Directory, cliquez sur le répertoire de l'utilisateur sur lequel vous souhaitez restaurer MFA.</li>
<li>En haut de la page, cliquez sur Utilisateurs.</li>
<li>En bas de la page, cliquez sur Gérer Multi-Factor Auth. Cette action ouvre la page relative à l’authentification multifacteur. <li>Recherchez l'utilisateur que vous souhaitez gérer et cochez la case située à côté du nom. Vous devrez peut-être modifier l'affichage en haut de la page.</li> <li>Cela fera apparaître le lien **Gérer les paramètres utilisateur** à droite de la page. Cliquez dessus.</li> <li>Cochez la case Restaurer Multi-Factor Authentication sur tous les appareils interrompus.</li> ![Supprimer des mots de passe d'application](./media/multi-factor-authentication-manage-users-and-devices/rememberdevices.png) <li>Cliquez sur Enregistrer.</li> <li>Cliquez sur Fermer.</li>

<!---HONumber=August15_HO6-->