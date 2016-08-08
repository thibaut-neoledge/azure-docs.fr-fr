<properties
	pageTitle="Implémentation de la synchronisation de mot de passe avec Azure AD Connect Sync | Microsoft Azure"
	description="Fournit des informations sur le fonctionnement de la synchronisation de mot de passe et explique comment l’activer."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>
<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/20/2016"
	ms.author="markusvi;andkjell"/>


# Implémentation de la synchronisation de mot de passe avec Azure AD Connect Sync

Cette rubrique vous fournit les informations nécessaires pour synchroniser vos mots de passe utilisateur à partir d’un annuaire Active Directory (AD) local vers un Azure Active Directory (Azure AD) cloud.


## Qu'est-ce que la synchronisation de mot de passe ?

La probabilité que vous soyez bloqué dans votre travail en raison d’un mot de passe oublié est liée au nombre de mots de passe différents que vous devez vous rappeler. Plus vous avez de mots de passe à mémoriser, plus vous risquez d’en oublier. Les questions et les appels pour des réinitialisations de mot de passe et d’autres problèmes de mot de passe sont ceux qui demandent le plus de ressources au support technique.

La synchronisation de mot de passe est une fonctionnalité permettant de synchroniser des mots de passe utilisateur à partir d’un annuaire Active Directory local dans un Azure Active Directory (Azure AD) informatique basé sur le cloud. Cette fonctionnalité vous permet de vous connecter aux services Azure Active Directory (notamment Office 365, Microsoft Intune, CRM Online et les services de domaine Azure AD) à l’aide du mot de passe que vous utilisez pour vous connecter à votre Active Directory local.

![Qu’est-ce qu’Azure AD Connect ?](./media/active-directory-aadconnectsync-implement-password-synchronization/arch1.png)

En réduisant à un seul le nombre de mots de passe que vos utilisateurs doivent conserver, la synchronisation de mot de passe vous permet :

- d’améliorer la productivité de vos utilisateurs ;
- de réduire les coûts de support technique associés.

De plus, si vous choisissez d’utiliser la [**Fédération avec AD FS**](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect), vous pouvez éventuellement activer la synchronisation de mot de passe comme sauvegarde en cas de défaillance de votre infrastructure AD FS.

La synchronisation de mot de passe est une extension de la fonctionnalité de synchronisation d’annuaire implémentée par Azure AD Connect Sync. Pour utiliser la synchronisation de mot de passe dans votre environnement, vous devez :

- Installer Azure AD Connect

- Configurer la synchronisation d’annuaire entre votre annuaire AD local et votre annuaire Azure Active Directory

- Activer la synchronisation de mot de passe


Pour plus d’informations, consultez [Intégration des identités locales avec Azure Active Directory](active-directory-aadconnect.md)



> [AZURE.NOTE] Pour plus d'informations sur les services de domaine Active Directory qui sont configurés pour la synchronisation de mot de passe et FIPS, consultez [Synchronisation de mot de passe et FIPS](#password-synchronization-and-fips).

## Fonctionnement de la synchronisation de mot de passe

Le service de domaine Active Directory stocke les mots de passe sous forme de valeur de hachage du mot de passe réel de l’utilisateur. Une valeur de hachage est le résultat d’une fonction mathématique unidirectionnelle («*algorithme de hachage*»). Il n’existe aucune méthode pour retrouver la version en texte brut du mot de passe à partir du résultat d’une fonction unidirectionnelle. Vous ne pouvez pas utiliser un hachage de mot de passe pour vous connecter à votre réseau local.

Pour synchroniser votre mot de passe, Azure AD Connect Sync extrait le hachage de votre mot de passe à partir de l’annuaire Active Directory local. Un traitement de sécurité supplémentaire est appliqué au hachage du mot de passe avant sa synchronisation avec le service d’authentification Azure Active Directory. Les mots de passe sont synchronisés pour chaque utilisateur et par ordre chronologique.

Le flux de données du processus de synchronisation de mot de passe est similaire à la synchronisation des données utilisateur telles que le nom d’affichage ou les adresses de messagerie. Cependant, les mots de passe sont synchronisés plus fréquemment que la fenêtre de synchronisation d’annuaire standard pour d’autres attributs. Le processus de synchronisation de mot de passe s’exécute toutes les deux minutes. Vous ne pouvez pas modifier la fréquence de ce processus. Quand vous synchronisez un mot de passe, il remplace le mot de passe cloud existant.

La première fois que vous activez la fonctionnalité de synchronisation de mot de passe, elle effectue une synchronisation initiale des mots de passe de tous les utilisateurs concernés. Vous ne pouvez pas définir explicitement un sous-ensemble de mots de passe utilisateur à synchroniser.

Lorsque vous modifiez un mot de passe local, le mot de passe mis à jour est synchronisé, plus souvent en quelques minutes. La fonctionnalité de synchronisation de mot de passe tente automatiquement de réeffectuer les tentatives de synchronisation ayant échoué. Si une erreur se produit lors d’une tentative de synchronisation de mot de passe, une erreur est enregistrée dans l’Observateur d’événements.

La synchronisation d’un mot de passe n’a aucun impact sur l’utilisateur actuellement connecté. Votre session de service cloud en cours n’est pas immédiatement affectée par une modification de mot de passe synchronisé effectuée lorsque vous êtes connecté à un service cloud. Toutefois, lorsque le service cloud vous oblige à vous authentifier à nouveau, vous devez fournir votre nouveau mot de passe.

> [AZURE.NOTE] La synchronisation de mot de passe est uniquement prise en charge pour l'utilisateur de type d'objet dans Active Directory. Elle n'est pas prise en charge pour le type d'objet iNetOrgPerson.

### Fonctionnement de la synchronisation de mot de passe avec les services de domaine Azure AD.

Vous pouvez également utiliser la fonctionnalité de synchronisation de mot de passe pour synchroniser vos mots de passe locaux avec les [Services de domaine Azure AD](../active-directory-domain-services/active-directory-ds-overview.md). Ce scénario permet aux services de domaine Azure AD d’authentifier vos utilisateurs dans le cloud avec toutes les méthodes disponibles dans votre AD local. L’expérience de ce scénario est similaire à l’utilisation de l’outil de migration Active Directory (ADMT) dans un environnement local.


### Sécurité

Lors de la synchronisation des mots de passe, la version en texte brut de votre mot de passe n’est exposée ni à la fonctionnalité de synchronisation de mot de passe, ni à Azure AD, ni à l’un des services associés.

En outre, il n’existe aucune spécification obligeant l’annuaire Active Directory local à stocker le mot de passe dans un format chiffré réversible. Un condensé du hachage de mot de passe Active Directory est utilisé pour la transmission entre l’annuaire Active Directory local et Azure Active Directory. Vous ne pouvez pas utiliser le hachage de mot de passe pour accéder aux ressources dans l’environnement local du client.

### Remarques sur les stratégies de mot de passe

Deux types de stratégies de mot de passe sont affectés par l’activation de la synchronisation de mot de passe :

1. la stratégie de complexité de mot de passe ;
2. la stratégie d’expiration de mot de passe.

**Stratégie de complexité de mot de passe**

Quand vous activez la synchronisation de mot de passe, les stratégies de complexité de mot de passe dans votre Active Directory local remplacent les stratégies de complexité dans le cloud pour les utilisateurs synchronisés. Vous pouvez utiliser tous les mots de passe valides de votre Active Directory local pour accéder aux services Azure AD.

> [AZURE.NOTE] Les mots de passe des utilisateurs créés directement dans le cloud sont toujours soumis aux stratégies de mot de passe définies dans le cloud.

**Stratégie d'expiration de mot de passe**

Si un utilisateur est inclus dans la synchronisation de mot de passe, le mot de passe du compte cloud a la valeur « *Ne jamais expirer* ». Vous pouvez continuer à vous connecter à vos services cloud à l’aide d’un mot de passe synchronisé qui a expiré dans votre environnement local. Votre mot de passe cloud est mis à jour la prochaine fois que vous modifiez le mot de passe dans l’environnement local.

### Remplacement des mots de passe synchronisés

Un administrateur peut réinitialiser manuellement votre mot de passe à l’aide de Windows PowerShell.

Dans ce cas, le nouveau mot de passe remplace votre mot de passe synchronisé et toutes les stratégies de mot de passe définies dans le cloud s’appliquent au nouveau mot de passe.

Si vous modifiez de nouveau votre mot de passe local, le nouveau mot de passe est synchronisé avec le cloud et il remplace le mot de passe mis à jour manuellement.


## Activation de la synchronisation de mot de passe

La synchronisation de mot de passe est activée automatiquement si vous installez Azure AD Connect avec la **configuration rapide**. Pour plus d’informations, consultez [Prise en main d’Azure AD Connect avec la configuration rapide](active-directory-aadconnect-get-started-express.md).

Si vous utilisez des paramètres personnalisés lors de l’installation d’Azure AD Connect, vous activez la synchronisation de mot de passe sur la page de connexion utilisateur. Pour plus d’informations, consultez [Installation personnalisée d’Azure AD Connect](active-directory-aadconnect-get-started-custom.md)


![Activation de la synchronisation de mot de passe](./media/active-directory-aadconnectsync-implement-password-synchronization/usersignin.png)


### Synchronisation du mot de passe et FIPS

Si votre serveur a été verrouillé selon la norme Federal Information Processing Standard (FIPS), MD5 est désactivé.


**Pour activer MD5 pour la synchronisation de mot de passe, procédez comme suit :**

    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>

1. Accédez à **%programfiles%\\Azure AD Sync\\Bin**.
2. Ouvrez **miiserver.exe.config**.
2. Accédez au nœud **configuration/exécution** (à la fin du fichier).
3. Ajoutez le nœud suivant : **<enforceFIPSPolicy enabled="false"/>**
4. Enregistrez vos modifications.

Pour plus d'informations sur la sécurité et FIPS, consultez [Synchronisation, cryptage et conformité à la norme FIPS du mot de passe AAD](http://blogs.technet.com/b/ad/archive/2014/06/28/aad-password-sync-encryption-and-and-fips-compliance.aspx)


## Dépannage de la synchronisation du mot de passe

Vous pouvez résoudre facilement les problèmes liés à la synchronisation de mot de passe en consultant l’état actuel d’un objet.


**Pour résoudre les problèmes de synchronisation de mot de passe, procédez comme suit :**

1. Lancez **[Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md)**.

2. Cliquez sur **Connecteurs**.

3. Sélectionnez le **connecteur Active Directory** dans lequel se trouve l’utilisateur.

4. Sélectionnez la fonction de **recherche de l’espace de connecteur**.

5. Localisez l’utilisateur que vous recherchez.

6. Sélectionnez l’onglet **lignage** et assurez-vous qu’au moins une règle de synchronisation affiche pour la **Synchronisation de mot de passe** la valeur **True**. Dans la configuration par défaut, le nom de la règle de synchronisation est **In from AD - User AccountEnabled**.

    ![Informations de lignage sur un utilisateur](./media/active-directory-aadconnectsync-implement-password-synchronization/cspasswordsync.png)

7. Vous devez également [suivre l’utilisateur](active-directory-aadconnectsync-service-manager-ui-connectors.md#follow-an-object-and-its-data-through-the-system) par le biais du métaverse vers l’espace de connecteur Azure AD. L’objet d’espace connecteur doit avoir une règle sortante avec une **synchronisation de mot de passe** définie sur **True**. Dans la configuration par défaut, le nom de la règle de synchronisation est **Out to AAD - User Join**.

    ![Propriétés d’espace de connecteur d’un utilisateur](./media/active-directory-aadconnectsync-implement-password-synchronization/cspasswordsync2.png)

8. Pour afficher les détails de synchronisation de mot de passe de l’objet pour la semaine écoulée, cliquez sur **Journal...**.

    ![Détails d’un journal objet](./media/active-directory-aadconnectsync-implement-password-synchronization/csobjectlog.png)

La colonne Statut peut avoir les valeurs suivantes :

| Statut | Description |
| ---- | ----- |
| Succès | Le mot de passe a été correctement synchronisé. |
| FilteredByTarget | Le mot de passe est défini sur **L'utilisateur doit changer de mot de passe à la prochaine connexion**. Mot de passe n'a pas été synchronisé. |
| NoTargetConnection | Aucun objet dans le métaverse ou dans l'espace de connecteur Azure AD. |
| SourceConnectorNotPresent | Aucun objet trouvé dans l'espace de connecteur Active Directory local. |
| TargetNotExportedToDirectory | L'objet dans l'espace de connecteur Azure AD n'a pas encore été exporté. |
| MigratedCheckDetailsForMoreInfo | L'entrée de journal a été créée avant la version 1.0.9125.0 et est affichée dans son état hérité. |


## Déclenchement d’une synchronisation complète de tous les mots de passe

En règle générale, il n’est pas nécessaire de déclencher une synchronisation complète de tous les mots de passe. Toutefois, le cas échéant, vous pouvez déclencher une synchronisation complète de tous les mots de passe à l’aide du script suivant :

    $adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
    $aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
    Import-Module adsync
    $c = Get-ADSyncConnector -Name $adConnector
    $p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter “Microsoft.Synchronize.ForceFullPasswordSync”, String, ConnectorGlobal, $null, $null, $null
    $p.Value = 1
    $c.GlobalParameters.Remove($p.Name)
    $c.GlobalParameters.Add($p)
    $c = Add-ADSyncConnector -Connector $c
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true




## Étapes suivantes

* [Azure AD Connect Sync : personnalisation des options de synchronisation](active-directory-aadconnectsync-whatis.md)
* [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0727_2016-->