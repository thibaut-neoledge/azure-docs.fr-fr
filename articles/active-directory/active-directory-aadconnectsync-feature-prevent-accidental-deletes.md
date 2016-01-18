<properties
   pageTitle="Azure AD Connect sync : prévention des suppressions accidentelles | Microsoft Azure"
   description="Cette rubrique décrit la fonctionnalité Prévention des suppressions accidentelles dans Azure AD Connect."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="12/29/2015"
   ms.author="andkjell"/>

# Azure AD Connect Sync : Prévention des suppressions accidentelles
Cette rubrique décrit la fonctionnalité Prévention des suppressions accidentelles dans Azure AD Connect.

Lors de l'installation d'Azure AD Connect, la fonctionnalité de prévention des suppressions accidentelles est activée par défaut et configurée de manière à interdire une exportation de plus de 500 suppressions. Cette fonctionnalité est conçue pour vous protéger contre les modifications accidentelles de la configuration et contre les modifications apportées à votre répertoire local qui auraient une incidence sur un grand nombre d'utilisateurs et d’autres objets.

Voici quelques scénarios courants :

- Modifications apportées au [filtrage](active-directory-aadconnectsync-configure-filtering.md) lorsque l'intégralité d'une [unité organisationnelle](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) ou d’un [domaine](active-directory-aadconnectsync-configure-filtering.md#domain-based-filtering) est désélectionnée.
- Tous les objets d’une unité d'organisation sont supprimés.
- Une unité d'organisation est renommée et tous les objets qu'elle contient sont considérés comme hors de portée pour la synchronisation.

La valeur par défaut de 500 objets peut être modifiée dans PowerShell à l'aide de `Enable-ADSyncExportDeletionThreshold`. Vous devez configurer cette valeur de manière à l'ajuster à la taille de votre organisation. Étant donné que le Planificateur de synchronisation est exécuté toutes les 3 heures, la valeur est le nombre de suppressions détectées dans les 3 heures.

Lorsque cette fonctionnalité est activée, l'exportation s'arrête si le nombre de suppressions vers Azure AD est trop important et vous recevez un courrier électronique similaire à celui-ci :

![E-mail de prévention des suppressions accidentelles](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/email.png)

> *Bonjour (contact technique). Au (moment) où le service de synchronisation d’identité a détecté que le nombre de suppressions avait dépassé le seuil de suppression configuré pour (nom de l’organisation). En tout, (nombre) objets ont été envoyés pour suppression au cours de ce cycle de synchronisation des identités. La valeur configurée de (nombre) objets pour le seuil de suppression a été atteinte ou dépassée. Avant que nous puissions continuer, vous devez confirmer que ces suppressions doivent être traitées. Consultez la rubrique Éviter les suppressions accidentelles pour plus d’informations concernant l’erreur indiquée dans ce message électronique.*

Si l'événement n'était pas prévu, examinez la situation et corrigez-la si nécessaire. Pour voir les objets devant être supprimés, procédez comme suit :

1. Démarrez le **Service de synchronisation** depuis le menu Démarrer.
2. Accédez à **Connecteurs**.
3. Sélectionnez le connecteur de type **Azure Active Directory**.
4. Sous **Actions**, à droite, sélectionnez **Espace de connecteur de recherche**.
5. Dans la fenêtre contextuelle, sous **Étendue**, sélectionnez **Déconnecté depuis**, puis choisissez une heure dans le passé. Cliquez sur **Rechercher**. Tous les objets sur le point d'être supprimés seront alors affichés. En cliquant sur chaque élément, vous pouvez obtenir des informations supplémentaires sur l'objet. Vous pouvez également cliquer sur **Paramètre de colonne** pour ajouter des attributs supplémentaires dans la grille.

![Espace de connecteur de recherche](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/searchcs.png)

Si vous souhaitez que tous les éléments soient supprimés, procédez comme suit :

1. Pour désactiver temporairement cette protection et procéder à ces suppressions exécutez l’applet de commande PowerShell `Disable-ADSyncExportDeletionThreshold`. En guise d’informations d’identification, indiquez un compte et un mot de passe d’administrateur général Azure AD. ![Informations d'identification](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/credentials.png)
2. Tout en maintenant le connecteur Azure Active Directory sélectionné, sélectionnez l’action **Exécuter**, puis **Exporter**.
3. Pour réactiver la protection, exécutez l’applet de commande PowerShell `Enable-ADSyncExportDeletionThreshold`.

## Étapes suivantes
En savoir plus sur la configuration de la [synchronisation Azure AD Connect](active-directory-aadconnectsync-whatis.md).

En savoir plus sur l’[intégration de vos identités locales à Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0107_2016-->