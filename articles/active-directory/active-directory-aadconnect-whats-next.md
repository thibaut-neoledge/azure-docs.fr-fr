<properties
	pageTitle="Azure AD Connect : Étapes suivantes et gestion d'Azure AD Connect | Microsoft Azure"
	description="Apprenez à étendre la configuration par défaut et les tâches opérationnelles pour Azure AD Connect."
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
	ms.date="02/16/2016"
	ms.author="billmath"/>

# Étapes suivantes et gestion d’Azure AD Connect
Les rubriques suivantes relatives aux fonctionnalités avancées vous permettent de personnaliser Azure Active Directory Connect pour l’adapter aux besoins et aux spécifications de votre organisation.

## Configuration facultative de votre service AD FS
Vous pouvez personnaliser l’illustration et l’image de logo de vos pages de connexion AD FS en vous connectant à AD FS, puis en utilisant PSH pour effectuer cette configuration.

	Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.png"} –Illustration @{path=”c:\Contoso\illustration.png”}

Consultez la page [Personnalisation des pages de connexion AD FS](https://technet.microsoft.com/library/dn280950.aspx) pour obtenir une description complète sur les possibilités de configuration.

## Attribution des licences aux utilisateurs Azure AD Premium et Enterprise Mobility

Maintenant que vos utilisateurs ont été synchronisés dans le cloud, vous devez leur attribuer une licence afin qu’ils puissent commencer à utiliser les applications de cloud telles qu’Office 365.

### Pour l’attribution d’une licence Azure AD Premium ou Enterprise Mobility Suite
--------------------------------------------------------------------------------
1. Connectez-vous au portail Azure en tant qu’administrateur.
2. Sélectionnez **Active Directory** à gauche.
3. Sur la page Active Directory, double-cliquez sur le répertoire qui contient les utilisateurs que vous souhaitez activer.
4. En haut de la page du répertoire, sélectionnez **Licences**.
5. Sur la page des licences, sélectionnez Active Directory Premium ou Enterprise Mobility Suite, puis cliquez sur **Attribuer**.
6. Dans la boîte de dialogue, sélectionnez les utilisateurs auxquels vous souhaitez attribuer des licences, puis cliquez sur l’icône de coche pour enregistrer les modifications.


## Vérification de la tâche de synchronisation planifiée
Si vous souhaitez vérifier l’état d’une synchronisation, vous pouvez le faire dans le portail Azure.

### Pour la vérification de la tâche de synchronisation planifiée
--------------------------------------------------------------------------------
1. Connectez-vous au portail Azure en tant qu’administrateur.
2. Sélectionnez **Active Directory** à gauche.
3. Sur la page Active Directory, double-cliquez sur le répertoire qui contient les utilisateurs que vous souhaitez activer.
4. En haut de la page du répertoire, sélectionnez **Intégration du répertoire**.
5. Sous Intégration avec un répertoire actif local, notez l’heure de la dernière synchronisation.

<center>![Cloud](./media/active-directory-aadconnect-whats-next/verify.png)</center>

## Début de la tâche de synchronisation planifiée
Si vous devez exécuter une tâche de synchronisation, vous pouvez le faire en l’exécutant une nouvelle fois depuis l’Assistant Azure AD Connect. Vous devez fournir vos informations d’identification Azure AD. Dans l’Assistant, sélectionnez la tâche **Personnalisation des options de synchronisation** et cliquez sur Suivant dans l’Assistant. À la fin, vérifiez que la case **Démarrer le processus de synchronisation dès que la configuration initiale est terminée** est cochée.

<center>![Cloud](./media/active-directory-aadconnect-whats-next/startsynch.png)</center>

Pour plus d’informations sur la synchronisation Azure AD Connect, consultez [Planificateur Azure AD Connect](active-directory-aadconnectsync-feature-scheduler.md).


## Tâches supplémentaires disponibles dans Azure AD Connect
Après l’installation initiale d’Azure AD Connect, vous pouvez toujours redémarrer l’Assistant depuis la page de démarrage ou le raccourci du bureau Azure AD Connect. Vous remarquerez qu’en passant à nouveau par l’Assistant de nouvelles options apparaissent sous forme de tâches supplémentaires.

Le tableau suivant fournit un résumé de ces tâches et une brève description de chacune d’elles.

![Règle de jointure](./media/active-directory-aadconnect-whats-next/addtasks.png)


Informations supplémentaires | Description
------------- | ------------- |
Afficher le scénario sélectionné |Vous pouvez afficher votre solution Azure AD Connect actuelle. Cela inclut les paramètres généraux, les répertoires synchronisés, les paramètres de synchronisation, etc.
Personnaliser les options de synchronisation | Vous pouvez modifier la configuration actuelle, y compris l’ajout de forêts Active Directory supplémentaires à la configuration ou l’activation des options de synchronisation telles que l’utilisateur, le groupe, l’appareil ou le mot de passe en écriture différée.
Activation du mode intermédiaire | Cela vous permet d’organiser les informations qui seront synchronisées plus tard, mais rien ne sera exporté vers Azure AD ou Active Directory. Cela vous permet d’afficher un aperçu des synchronisations avant qu’elles ne s’effectuent.

## Étapes suivantes
En savoir plus sur l’[intégration de vos identités locales à Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0224_2016-->