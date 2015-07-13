<properties 
	pageTitle="Obtention d’informations grâce aux rapports sur la gestion des mots de passe | Microsoft Azure" 
	description="Cet article explique comment utiliser les rapports pour obtenir des informations sur les opérations de gestion des mots de passe dans votre organisation." 
	services="active-directory" 
	documentationCenter="" 
	authors="asteen" 
	manager="kbrint" 
	editor="billmath"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/08/2015" 
	ms.author="asteen"/>

# Obtention d’informations grâce aux rapports sur la gestion des mots de passe
Cette section explique comment utiliser les rapports de gestion des mots de passe Azure Active Directory pour savoir comment les utilisateurs utilisent la réinitialisation et la modification des mots de passe dans votre organisation.

- [**Vue d’ensemble des rapports de gestion des mots de passe**](#overview-of-password-management-reports)
- [**Comment visualiser les rapports de gestion des mots de passe**](#how-to-view-password-management-reports)
- [**Visualiser l’activité d’inscription à la réinitialisation de mot de passe dans votre organisation**](#view-password-reset-registration-activity)
- [**Visualiser l’activité de réinitialisation de mot de passe dans votre organisation**](#view-password-reset-activity)

## Vue d’ensemble des rapports de gestion des mots de passe
Une fois la réinitialisation des mots de passe déployée, l’une des étapes les plus courantes consiste à vérifier comment elle est utilisée dans votre organisation. Par exemple, vous souhaiterez peut-être savoir comment les utilisateurs s’inscrivent pour la réinitialisation des mots de passe ou combien de réinitialisations de mots de passe ont été effectuées au cours des derniers jours. Voici quelques-unes des questions auxquelles vous serez en mesure de répondre grâce aux rapports de gestion des mots de passe accessibles à l’heure actuelle dans le [portail de gestion Azure](https://manage.windowsazure.com) :

- Combien de personnes se sont inscrites pour la réinitialisation des mots de passe ?
- Qui s’est inscrit pour la réinitialisation des mots de passe ?
- Quelles sont les données inscrites par ces personnes ?
- Combien de personnes ont réinitialisé leurs mots de passe au cours des 7 derniers jours ?
- Quelles sont les méthodes les plus courantes employées par les utilisateurs ou les administrateurs pour réinitialiser leurs mots de passe ?
- Quels sont les problèmes courants rencontrés par les utilisateurs ou les administrateurs lors des tentatives d’utilisation de la fonctionnalité de réinitialisation des mots de passe ?
- Quels sont les administrateurs qui réinitialisent fréquemment leurs mots de passe ?
- Y a-t-il des activités suspectes en rapport avec la réinitialisation des mots de passe ?


## Comment visualiser les rapports de gestion des mots de passe
Pour accéder aux rapports de gestion des mots de passe, procédez comme suit :

1.	Cliquez sur l’extension **Active Directory** dans le [portail de gestion Azure](https://manage.windowsazure.com).
2.	Sélectionnez votre annuaire dans la liste qui apparaît dans le portail.
3.	Cliquez sur l’onglet **Rapports**.
4.	Regardez sous la section **Journaux d’activité**.
5.	Sélectionnez le rapport **Activité de réinitialisation de mot de passe** ou **Activité d’inscription de réinitialisation de mot de passe**.

    ![][001]

## Visualiser l’activité d’inscription à la réinitialisation de mot de passe dans votre organisation

Le rapport Activité d’inscription de réinitialisation de mot de passe montre toutes les inscriptions à la réinitialisation de mot de passe qui ont eu lieu dans votre organisation. Une inscription à la réinitialisation de mot de passe est affichée dans ce rapport pour tout utilisateur qui a inscrit des informations d’authentification dans le portail d’inscription à la réinitialisation de mot de passe ([http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)).

- **Période max** : 1 mois
- **Nombre maximal de lignes** : illimité
- **Téléchargeable** : Oui, via un fichier CSV

    ![][002]

### Description des colonnes du rapport
La liste suivante décrit chacune des colonnes du rapport en détail :

- **Utilisateur** : utilisateur ayant tenté d’effectuer une opération d’inscription à la réinitialisation de mot de passe.
- **Rôle** : rôle de l’utilisateur dans l’annuaire.
- **Date et heure** : date et heure de la tentative.
- **Données inscrites** : données d’authentification fournies par l’utilisateur lors de l’inscription à la réinitialisation de mot de passe.

### Description des valeurs du rapport
Le tableau suivant décrit les différentes valeurs autorisées pour chaque colonne :

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>Colonne</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Valeurs autorisées et leur signification</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Données inscrites</p>
              </td>
              <td>
                <ul>
                  <li class="unordered">
                    <strong>Adresse de messagerie de secours</strong>&#160;: l’utilisateur a utilisé une autre adresse de messagerie électronique ou une adresse électronique d’authentification pour s’authentifier.<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Téléphone professionnel</strong>&#160;: l’utilisateur a utilisé un téléphone professionnel pour s’authentifier.<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Téléphone mobile</strong>&#160;: l’utilisateur a utilisé un téléphone portable ou un téléphone d’authentification pour s’authentifier.<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Questions de sécurité</strong>&#160;: l’utilisateur a utilisé des questions de sécurité pour s’authentifier.<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Toute combinaison des éléments ci-dessus (par exemple, Adresse de messagerie de secours + Téléphone mobile)</strong>&#160;: se produit lorsqu’une stratégie à 2&#160;voies d’accès est spécifiée et indique les deux méthodes employées par l’utilisateur pour authentifier sa demande de réinitialisation de mot de passe.<br><br></li>
                </ul>
              </td>
            </tr>
          </tbody></table>

## Visualiser l’activité de réinitialisation de mot de passe dans votre organisation

Ce rapport montre toutes les tentatives de réinitialisation de mot de passe qui se sont produites dans votre organisation.

- **Période max** : 1 mois
- **Nombre maximal de lignes** : illimité
- **Téléchargeable** : Oui, via un fichier CSV

    ![][003]

### Description des colonnes du rapport
La liste suivante décrit chacune des colonnes du rapport en détail :

1. **Utilisateur** : utilisateur ayant tenté d’effectuer une opération de réinitialisation de mot de passe (basé sur le champ ID utilisateur fourni quand l’utilisateur demande à réinitialiser un mot de passe).
2. **Rôle** : rôle de l’utilisateur dans l’annuaire.
3. **Date et heure** : date et heure de la tentative.
4. **Méthode(s) utilisée(s)** : méthodes d’authentification employées par l’utilisateur pour cette opération de réinitialisation.
5. **Résultat** : résultat final de l’opération de réinitialisation de mot de passe.
6. **Détails** : détails de la raison pour laquelle la réinitialisation de mot de passe a généré ce résultat. Inclut également les étapes d’atténuation que vous pouvez suivre pour résoudre une erreur inattendue.

### Description des valeurs du rapport
Le tableau suivant décrit les différentes valeurs autorisées pour chaque colonne :

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>Colonne</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Valeurs autorisées et leur signification</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Méthodes utilisées</p>
              </td>
              <td>
                <ul>
                  <li class="unordered">
                    <strong>Adresse de messagerie de secours</strong>&#160;: l’utilisateur a utilisé une autre adresse de messagerie électronique ou une adresse électronique d’authentification pour s’authentifier.<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Téléphone professionnel</strong>&#160;: l’utilisateur a utilisé un téléphone professionnel pour s’authentifier.<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Téléphone mobile</strong>&#160;: l’utilisateur a utilisé un téléphone portable ou un téléphone d’authentification pour s’authentifier.<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Questions de sécurité</strong>&#160;: l’utilisateur a utilisé des questions de sécurité pour s’authentifier.<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Toute combinaison des éléments ci-dessus (par exemple, Adresse de messagerie de secours + Téléphone mobile)</strong>&#160;: se produit lorsqu’une stratégie à 2&#160;voies d’accès est spécifiée et indique les deux méthodes employées par l’utilisateur pour authentifier sa demande de réinitialisation de mot de passe.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Résultat</p>
              </td>
              <td>
                <ul>
                  <li class="unordered">
                    <strong>Abandonné</strong>&#160;: l’utilisateur a démarré la réinitialisation du mot de passe, puis s’est arrêté à mi-chemin sans terminer<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Bloqué</strong>&#160;: le compte d’utilisateur n’a pas été autorisé à utiliser la réinitialisation de mot de passe, car il a tenté d’utiliser la page de réinitialisation de mot de passe ou une même méthode de réinitialisation de mot de passe un trop grand nombre de fois durant une période de 24&#160;heures.<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Annulé</strong>&#160;: l’utilisateur a démarré la réinitialisation du mot de passe, puis il a cliqué sur le bouton Annuler pour annuler la session en cours. <br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Administrateur contacté</strong>&#160;: l’utilisateur a rencontré un problème insoluble lors de sa session et il a cliqué sur le lien « Contactez votre administrateur » au lieu de terminer le flux de réinitialisation de mot de passe.<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Échec</strong>&#160;: l’utilisateur n’a pas pu réinitialiser un mot de passe, probablement car il n’était pas configuré pour utiliser la fonctionnalité (par exemple, aucune licence, informations d’authentification manquantes, mot de passe géré localement mais écriture différée désactivée).<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Réussi</strong>&#160;: la réinitialisation du mot de passe a réussi.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Détails</p>
              </td>
              <td>
                <p>Voir le tableau ci-dessous</p>
              </td>
            </tr>
          </tbody></table>

### Valeurs autorisées pour la colonne Détails
Voici la liste des types de résultats que vous pouvez attendre lors de l’utilisation du rapport d’activité de réinitialisation du mot de passe :

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>Détails</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Type de résultat</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L’utilisateur a abandonné avant d’avoir effectué complètement l’option de vérification par e-mail.</p>
              </td>
              <td>
                <p>Abandonné</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L’utilisateur a abandonné avant d’avoir effectué complètement l’option de vérification par SMS sur mobile.</p>
              </td>
              <td>
                <p>Abandonné</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L’utilisateur a abandonné avant d’avoir effectué complètement l’option de vérification par appel vocal sur mobile.</p>
              </td>
              <td>
                <p>Abandonné</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L’utilisateur a abandonné avant d’avoir effectué complètement l’option de vérification par appel vocal au bureau.</p>
              </td>
              <td>
                <p>Abandonné</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L’utilisateur a abandonné avant d’avoir effectué complètement l’option de questions de sécurité.</p>
              </td>
              <td>
                <p>Abandonné</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L’utilisateur a abandonné après avoir entré son ID utilisateur.</p>
              </td>
              <td>
                <p>Abandonné</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L’utilisateur a abandonné après avoir commencé l’option de vérification par e-mail.</p>
              </td>
              <td>
                <p>Abandonné</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L’utilisateur a abandonné après avoir commencé l’option de vérification par SMS sur mobile.</p>
              </td>
              <td>
                <p>Abandonné</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L’utilisateur a abandonné après avoir commencé l’option de vérification par appel vocal sur mobile.</p>
              </td>
              <td>
                <p>Abandonné</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L’utilisateur a abandonné après avoir commencé l’option de vérification par appel vocal au bureau.</p>
              </td>
              <td>
                <p>Abandonné</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L’utilisateur a abandonné après avoir commencé l’option de vérification par questions de sécurité.</p>
              </td>
              <td>
                <p>Abandonné</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L’utilisateur a abandonné avant de choisir un nouveau mot de passe.</p>
              </td>
              <td>
                <p>Abandonné</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L’utilisateur a abandonné lors du choix d’un nouveau mot de passe.</p>
              </td>
              <td>
                <p>Abandonné</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L’utilisateur a entré un nombre trop élevé de codes de vérification d’e-mail et est bloqué pour 24&#160;heures.</p>
              </td>
              <td>
                <p>Bloqué</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L’utilisateur a entré un trop grand nombre de codes de vérification par SMS non valides et est bloqué pour 24&#160;heures.</p>
              </td>
              <td>
                <p>Bloqué</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L’utilisateur a tenté un trop grand nombre de fois la vérification de la voix par appel sur mobile et est bloqué pour 24&#160;heures.</p>
              </td>
              <td>
                <p>Bloqué</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L’utilisateur a tenté un trop grand nombre de fois la vérification de la voix par appel au bureau et est bloqué pour 24&#160;heures.</p>
              </td>
              <td>
                <p>Bloqué</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L’utilisateur a tenté un trop grand nombre de fois de répondre à des questions de sécurité et est bloqué pour 24&#160;heures.</p>
              </td>
              <td>
                <p>Bloqué</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L’utilisateur a tenté un trop grand nombre de fois de vérifier un numéro de téléphone et est bloqué pour 24&#160;heures.</p>
              </td>
              <td>
                <p>Bloqué</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L’utilisateur a abandonné avant de se soumettre aux méthodes d’authentification requises.</p>
              </td>
              <td>
                <p>Annulé</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L’utilisateur a abandonné avant de soumettre un nouveau mot de passe.</p>
              </td>
              <td>
                <p>Annulé</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L’utilisateur a contacté un administrateur après avoir essayé l’option de vérification par e-mail.</p>
              </td>
              <td>
                <p>Administrateur contacté</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L’utilisateur a contacté un administrateur après avoir essayé l’option de vérification par SMS sur mobile.</p>
              </td>
              <td>
                <p>Administrateur contacté</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L’utilisateur a contacté un administrateur après avoir essayé l’option de vérification par appel vocal sur mobile.</p>
              </td>
              <td>
                <p>Administrateur contacté</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L’utilisateur a contacté un administrateur après avoir essayé l’option de vérification par appel vocal au bureau.</p>
              </td>
              <td>
                <p>Administrateur contacté</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L’utilisateur a contacté un administrateur après avoir essayé l’option de vérification par question de sécurité.</p>
              </td>
              <td>
                <p>Administrateur contacté</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>La réinitialisation du mot de passe n’est pas activée pour cet utilisateur. Activez-la sous l’onglet Configuration pour résoudre ce problème.</p>
              </td>
              <td>
                <p>Échec</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L’utilisateur n’a pas de licence. Vous pouvez ajouter une licence à l’utilisateur pour résoudre ce problème.</p>
              </td>
              <td>
                <p>Échec</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L’utilisateur a tenté de réinitialiser depuis un appareil où les cookies ne sont pas activés.</p>
              </td>
              <td>
                <p>Échec</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Un nombre insuffisant de méthodes d’authentification sont définies pour le compte de l’utilisateur. Ajoutez des informations d’authentification pour résoudre ce problème.</p>
              </td>
              <td>
                <p>Échec</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Le mot de passe de l’utilisateur est géré localement. Vous pouvez activer l’écriture en différé du mot de passe pour résoudre ce problème.</p>
              </td>
              <td>
                <p>Échec</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Nous n’avons pas pu accéder au service de réinitialisation de votre mot de passe local. Vérifiez le journal des événements de votre ordinateur synchronisé.</p>
              </td>
              <td>
                <p>Échec</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Nous avons rencontré un problème lors de la réinitialisation du mot de passe local de l’utilisateur. Vérifiez le journal des événements de votre ordinateur synchronisé.</p>
              </td>
              <td>
                <p>Échec</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Cet utilisateur n’est pas membre du groupe utilisateurs de réinitialisation de mot de passe. Ajoutez cet utilisateur à ce groupe pour résoudre ce problème.</p>
              </td>
              <td>
                <p>Échec</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>La réinitialisation du mot de passe a été désactivée pour ce locataire. Consultez <a href="http://aka.ms/ssprtroubleshoot">http://aka.ms/ssprtroubleshoot</a> pour résoudre ce problème.</p>
              </td>
              <td>
                <p>Échec</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L’utilisateur a réinitialisé le mot de passe.</p>
              </td>
              <td>
                <p>Réussi</p>
              </td>
            </tr>
          </tbody></table>

<br/> <br/> <br/>

**Ressources supplémentaires**


* [Définition de la gestion des mots de passe](active-directory-passwords.md)
* [Fonctionnement de la gestion des mots de passe](active-directory-passwords-how-it-works.md)
* [Prise en main de la gestion des mots de passe](active-directory-passwords-getting-started.md)
* [Personnalisation de la gestion des mots de passe](active-directory-passwords-customize.md)
* [Meilleures pratiques de gestion des mots de passe](active-directory-passwords-best-practices.md)
* [FAQ sur la gestion des mots de passe](active-directory-passwords-faq.md)
* [Résolution des problèmes de gestion des mots de passe](active-directory-passwords-troubleshoot.md)
* [En savoir plus](active-directory-passwords-learn-more.md)
* [Gestion des mots de passe sur MSDN](https://msdn.microsoft.com/library/azure/dn510386.aspx)



[001]: ./media/active-directory-passwords-get-insights/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-get-insights/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-get-insights/003.jpg "Image_003.jpg"
 

<!---HONumber=62-->