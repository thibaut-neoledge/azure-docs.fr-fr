---
title: Implémentation de la synchronisation de mot de passe avec Azure AD Connect Sync | Microsoft Docs
description: Fournit des informations sur le fonctionnement de la synchronisation de mot de passe et explique comment l’activer.
services: active-directory
documentationcenter: ''
author: markusvi
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: markusvi;andkjell

---
# Implémentation de la synchronisation de mot de passe avec Azure AD Connect Sync
Cette rubrique vous fournit les informations nécessaires pour synchroniser vos mots de passe utilisateur à partir d’un répertoire Active Directory (AD) local vers un répertoire Azure Active Directory (Azure AD) dans le cloud.

## Qu'est-ce que la synchronisation de mot de passe ?
La probabilité que vous soyez bloqué dans votre travail en raison d’un mot de passe oublié est liée au nombre de mots de passe différents que vous devez vous rappeler. Plus vous avez de mots de passe à mémoriser, plus vous risquez d’en oublier. Les questions et les appels pour des réinitialisations de mot de passe et d’autres problèmes de mot de passe sont ceux qui demandent le plus de ressources au support technique.

La synchronisation de mot de passe est une fonctionnalité permettant de synchroniser des mots de passe utilisateur à partir d’un annuaire Active Directory local dans un Azure Active Directory (Azure AD) informatique basé sur le cloud. Cette fonctionnalité vous permet de vous connecter aux services Azure Active Directory (notamment Office 365, Microsoft Intune, CRM Online et les services de domaine Azure AD) à l’aide du mot de passe que vous utilisez pour vous connecter à votre répertoire Active Directory local.

![Qu’est-ce qu’Azure AD Connect ?](./media/active-directory-aadconnectsync-implement-password-synchronization/arch1.png)

En réduisant à un seul le nombre de mots de passe que vos utilisateurs doivent conserver, la synchronisation de mot de passe vous permet :

* d’améliorer la productivité de vos utilisateurs ;
* Réduire vos coûts de support technique

De plus, si vous choisissez d’utiliser la [**Fédération avec AD FS**](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect), vous pouvez éventuellement activer la synchronisation de mot de passe comme sauvegarde en cas de défaillance de votre infrastructure AD FS.

La synchronisation de mot de passe est une extension de la fonctionnalité de synchronisation d’annuaire implémentée par Azure AD Connect Sync. Pour utiliser la synchronisation de mot de passe dans votre environnement, vous devez :

* Installer Azure AD Connect
* Configurer la synchronisation d’annuaire entre votre annuaire AD local et votre annuaire Azure Active Directory
* Activer la synchronisation de mot de passe

Pour plus d’informations, consultez [Intégration des identités locales avec Azure Active Directory](active-directory-aadconnect.md)

> [!NOTE]
> Pour plus d'informations sur les services de domaine Active Directory qui sont configurés pour la synchronisation de mot de passe et FIPS, consultez [Synchronisation de mot de passe et FIPS](#password-synchronization-and-fips).
> 
> 

## Fonctionnement de la synchronisation de mot de passe
Le service de domaine Active Directory stocke les mots de passe sous forme de valeur de hachage du mot de passe réel de l’utilisateur. Une valeur de hachage est le résultat d’une fonction mathématique unidirectionnelle (« *algorithme de hachage* »). Il n’existe aucune méthode pour retrouver la version en texte brut du mot de passe à partir du résultat d’une fonction unidirectionnelle. Vous ne pouvez pas utiliser un hachage de mot de passe pour vous connecter à votre réseau local.

Pour synchroniser votre mot de passe, Azure AD Connect Sync extrait le hachage de votre mot de passe à partir de l’annuaire Active Directory local. Un traitement de sécurité supplémentaire est appliqué au hachage du mot de passe avant sa synchronisation avec le service d’authentification Azure Active Directory. Les mots de passe sont synchronisés pour chaque utilisateur et par ordre chronologique.

Le flux de données du processus de synchronisation de mot de passe est similaire à la synchronisation des données utilisateur telles que le nom d’affichage ou les adresses de messagerie. Cependant, les mots de passe sont synchronisés plus fréquemment que la fenêtre de synchronisation d’annuaire standard pour d’autres attributs. Le processus de synchronisation de mot de passe s’exécute toutes les deux minutes. Vous ne pouvez pas modifier la fréquence de ce processus. Quand vous synchronisez un mot de passe, il remplace le mot de passe cloud existant.

La première fois que vous activez la fonctionnalité de synchronisation de mot de passe, elle effectue une synchronisation initiale des mots de passe de tous les utilisateurs concernés. Vous ne pouvez pas définir explicitement un sous-ensemble de mots de passe utilisateur à synchroniser.

Lorsque vous modifiez un mot de passe local, le mot de passe mis à jour est synchronisé, plus souvent en quelques minutes. La fonctionnalité de synchronisation de mot de passe tente automatiquement de réeffectuer les tentatives de synchronisation ayant échoué. Si une erreur se produit lors d’une tentative de synchronisation de mot de passe, une erreur est enregistrée dans l’Observateur d’événements.

La synchronisation d’un mot de passe n’a aucun impact sur l’utilisateur actuellement connecté. Votre session de service cloud en cours n’est pas immédiatement affectée par une modification de mot de passe synchronisé effectuée lorsque vous êtes connecté à un service cloud. Toutefois, lorsque le service cloud vous oblige à vous authentifier à nouveau, vous devez fournir votre nouveau mot de passe.

> [!NOTE]
> La synchronisation de mot de passe est uniquement prise en charge pour l'utilisateur de type d'objet dans Active Directory. Elle n'est pas prise en charge pour le type d'objet iNetOrgPerson.
> 
> 

### Fonctionnement de la synchronisation de mot de passe avec les services de domaine Azure AD.
Vous pouvez également utiliser la fonctionnalité de synchronisation de mot de passe pour synchroniser vos mots de passe locaux avec les [Services de domaine Azure AD](../active-directory-domain-services/active-directory-ds-overview.md). Ce scénario permet aux services de domaine Azure AD d’authentifier vos utilisateurs dans le cloud avec toutes les méthodes disponibles dans votre AD local. L’expérience de ce scénario est similaire à l’utilisation de l’outil de migration Active Directory (ADMT) dans un environnement local.

### Sécurité
Lors de la synchronisation des mots de passe, la version en texte brut de votre mot de passe n’est exposée ni à la fonctionnalité de synchronisation de mot de passe, ni à Azure AD, ni à l’un des services associés.

En outre, il n’existe aucune spécification obligeant l’annuaire Active Directory local à stocker le mot de passe dans un format chiffré réversible. Un condensé du hachage de mot de passe Active Directory est utilisé pour la transmission entre l’annuaire Active Directory local et Azure Active Directory. Vous ne pouvez pas utiliser le hachage de mot de passe pour accéder aux ressources dans l’environnement local du client.

### Remarques sur les stratégies de mot de passe
Deux types de stratégies de mot de passe sont affectés par l’activation de la synchronisation de mot de passe :

1. la stratégie de complexité de mot de passe ;
2. la stratégie d’expiration de mot de passe.

**nchronisation de mot de passe** Quand vous activez la synchronisation de mot de passe, les stratégies de complexité de mot de passe dans votre répertoire Active Directory local remplacent les stratégies de complexité dans le cloud pour les utilisateurs synchronisés. Vous pouvez utiliser tous les mots de passe valides de votre Active Directory local pour accéder aux services Azure AD.

> [!NOTE]
> Les mots de passe des utilisateurs créés directement dans le cloud sont toujours soumis aux stratégies de mot de passe définies dans le cloud.
> 
> 

**Stratégie d’expiration de mot de passe** Si un utilisateur figure dans la portée de synchronisation de mot de passe, le mot de passe de compte cloud a pour valeur « *Ne jamais expirer* ». Vous pouvez continuer à vous connecter aux services cloud à l’aide d’un mot de passe synchronisé qui a expiré dans votre environnement local. Votre mot de passe cloud est mis à jour la prochaine fois que vous modifiez le mot de passe dans l’environnement local.

### Remplacement des mots de passe synchronisés
Un administrateur peut réinitialiser manuellement votre mot de passe à l’aide de Windows PowerShell.

Dans ce cas, le nouveau mot de passe remplace votre mot de passe synchronisé et toutes les stratégies de mot de passe définies dans le cloud s’appliquent au nouveau mot de passe.

Si vous modifiez de nouveau votre mot de passe local, le nouveau mot de passe est synchronisé avec le cloud et il remplace le mot de passe mis à jour manuellement.

## Activation de la synchronisation de mot de passe
La synchronisation de mot de passe est activée automatiquement si vous installez Azure AD Connect avec la **configuration rapide**. Pour plus d’informations, voir [Prise en main d’Azure AD Connect avec la configuration rapide](active-directory-aadconnect-get-started-express.md).

Si vous utilisez des paramètres personnalisés lors de l’installation d’Azure AD Connect, vous activez la synchronisation de mot de passe sur la page de connexion utilisateur. Pour plus d’informations, voir [Installation personnalisée d’Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

![Activation de la synchronisation de mot de passe](./media/active-directory-aadconnectsync-implement-password-synchronization/usersignin.png)

### Synchronisation du mot de passe et FIPS
Si votre serveur a été verrouillé selon la norme Federal Information Processing Standard (FIPS), MD5 est désactivé.

**Pour activer MD5 pour la synchronisation de mot de passe, procédez comme suit :**

1. Accédez à **%programfiles%\\Azure AD Sync\\Bin**.
2. Ouvrez **miiserver.exe.config**.
3. Accédez au nœud **configuration/runtime** (à la fin du fichier).
4. Ajoutez le nœud suivant : `<enforceFIPSPolicy enabled="false"/>`
5. Enregistrez vos modifications.

Pour référence, cette capture indique ce que vous devez obtenir :

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

Pour plus d'informations sur la sécurité et FIPS, consultez [Synchronisation, cryptage et conformité à la norme FIPS du mot de passe AAD](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/)

## Dépannage de la synchronisation du mot de passe
Si les mots de passe ne se synchronisent pas comme prévu, il peut s’agir d’un sous-ensemble d’utilisateurs ou de tous les utilisateurs.

* Si vous rencontrez un problème avec des objets individuels, voir [Dépanner un objet qui bloque la synchronisation des mots de passe](#troubleshoot-one-object-that-is-not-synchronizing-passwords).
* Si vous rencontrez un problème qui bloque la synchronisation des mots de passe, voir [Résoudre les problèmes bloquant la synchronisation des mots de passe](#troubleshoot-issues-where-no-passwords-are-synchronized).

### Dépanner un objet qui bloque la synchronisation des mots de passe
Vous pouvez résoudre facilement les problèmes liés à la synchronisation des mots de passe en consultant l’état d’un objet.

Lancez **Utilisateurs et ordinateurs Active Directory**. Recherchez l’utilisateur et vérifiez que la case **L’utilisateur doit changer le mot de passe à la prochaine ouverture de session** n’est pas cochée. ![Mots de passe productifs Active Directory](./media/active-directory-aadconnectsync-implement-password-synchronization/adprodpassword.png) Si elle est cochée, demandez à l’utilisateur de se connecter et de modifier son mot de passe. Les mots de passe temporaires ne se synchronisent pas sur Azure AD.

Si tout semble correct dans Active Directory, passez à l’étape suivante qui consiste à suivre l’utilisateur dans le moteur de synchronisation. En suivant l’utilisateur depuis le répertoire Active Directory local vers Azure AD, vous pouvez voir si un message d’erreur descriptif apparaît sur l’objet.

1. Lancez **[Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md)**.
2. Cliquez sur **Connecteurs**.
3. Sélectionnez le **connecteur Active Directory** dans lequel se trouve l’utilisateur.
4. Sélectionnez **Search Connector Space** (Rechercher espace connecteur).
5. Localisez l’utilisateur que vous recherchez.
6. Sélectionnez l’onglet **lignage** et assurez-vous qu’au moins une règle de synchronisation utilise la valeur **True** pour **Synchronisation de mot de passe**. Dans la configuration par défaut, le nom de la règle de synchronisation est **In from AD - User AccountEnabled**. ![Informations de lignage sur un utilisateur](./media/active-directory-aadconnectsync-implement-password-synchronization/cspasswordsync.png)
7. Vous devez également [suivre l’utilisateur](active-directory-aadconnectsync-service-manager-ui-connectors.md#follow-an-object-and-its-data-through-the-system) par le biais du métaverse vers l’espace connecteur Azure AD. L’objet d’espace connecteur doit avoir une règle sortante avec une **synchronisation de mot de passe** définie sur **True**. Dans la configuration par défaut, le nom de la règle de synchronisation est **Out to AAD - User Join**. ![Propriétés d’espace de connecteur d’un utilisateur](./media/active-directory-aadconnectsync-implement-password-synchronization/cspasswordsync2.png)
8. Pour afficher les détails de synchronisation de mot de passe de l’objet pour la semaine écoulée, cliquez sur **Journal...**. ![Détails d’un journal objet](./media/active-directory-aadconnectsync-implement-password-synchronization/csobjectlog.png)

La colonne Statut peut avoir les valeurs suivantes :

| État | Description |
| --- | --- |
| Succès |Le mot de passe a été correctement synchronisé. |
| FilteredByTarget |Le mot de passe est défini sur **L'utilisateur doit changer de mot de passe à la prochaine connexion**. Mot de passe n'a pas été synchronisé. |
| NoTargetConnection |Aucun objet dans le métaverse ou dans l'espace de connecteur Azure AD. |
| SourceConnectorNotPresent |Aucun objet trouvé dans l'espace de connecteur Active Directory local. |
| TargetNotExportedToDirectory |L'objet dans l'espace de connecteur Azure AD n'a pas encore été exporté. |
| MigratedCheckDetailsForMoreInfo |L'entrée de journal a été créée avant la version 1.0.9125.0 et est affichée dans son état hérité. |

### Résoudre les problèmes bloquant la synchronisation des mots de passe
Commencez par exécuter le script dans la section [Obtenir l’état des paramètres de synchronisation de mot de passe](#get-the-status-of-password-sync-settings). Cela vous donne une vue d’ensemble de la configuration de la synchronisation de mot de passe. ![Sortie de script PowerShell à partir des paramètres de synchronisation de mot de passe](./media/active-directory-aadconnectsync-implement-password-synchronization/psverifyconfig.png) Si cette fonction n’est pas activée dans Azure AD ou si l’état du canal de synchronisation n’est pas activé, exécutez l’Assistant Installation d’Azure AD Connect. Sélectionnez **Personnaliser les options de synchronisation** et décochez la synchronisation de mot de passe. Cette modification désactive temporairement la fonction. Réexécutez l’Assistant, puis réactivez la synchronisation de mot de passe. Réexécutez le script pour vérifier que la configuration est correcte.

Si le script indique qu’il n’y a aucune pulsation, exécutez le script [Déclencher une synchronisation complète de tous les mots de passe](#trigger-a-full-sync-of-all-passwords). Ce script peut également être utilisé pour d’autres scénarios dans lesquels la configuration est correcte, mais les mots de passe ne sont pas synchronisées.

#### Obtenir l’état des paramètres de synchronisation de mot de passe
```
Import-Module ADSync
$connectors = Get-ADSyncConnector
$aadConnectors = $connectors | Where-Object {$_.SubType -eq "Microsoft Azure Active Directory (Microsoft)"}
$adConnectors = $connectors | Where-Object {$_.ConnectorTypeName -eq "AD"}
if ($aadConnectors -ne $null -and $adConnectors -ne $null)
{
    if ($aadConnectors.Count -eq 1)
    {
        $features = Get-ADSyncAADCompanyFeature -ConnectorName $aadConnectors[0].Name
        Write-Host
        Write-Host "Password sync feature enabled in your Azure AD directory: "  $features.PasswordHashSync
        foreach ($adConnector in $adConnectors)
        {
            Write-Host
            Write-Host "Password sync channel status BEGIN ------------------------------------------------------- "
            Write-Host
            Get-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector.Name
            Write-Host
            $pingEvents =
                Get-EventLog -LogName "Application" -Source "Directory Synchronization" -InstanceId 654  -After (Get-Date).AddHours(-3) |
                    Where-Object { $_.Message.ToUpperInvariant().Contains($adConnector.Identifier.ToString("D").ToUpperInvariant()) } |
                    Sort-Object { $_.Time } -Descending
            if ($pingEvents -ne $null)
            {
                Write-Host "Latest heart beat event (within last 3 hours). Time " $pingEvents[0].TimeWritten
            }
            else
            {
                Write-Warning "No ping event found within last 3 hours."
            }
            Write-Host
            Write-Host "Password sync channel status END ------------------------------------------------------- "
            Write-Host
        }
    }
    else
    {
        Write-Warning "More than one Azure AD Connectors found. Please update the script to use the appropriate Connector."
    }
}
Write-Host
if ($aadConnectors -eq $null)
{
    Write-Warning "No Azure AD Connector was found."
}
if ($adConnectors -eq $null)
{
    Write-Warning "No AD DS Connector was found."
}
Write-Host
```

#### Déclencher une synchronisation complète de tous les mots de passe
Vous pouvez déclencher une synchronisation complète de tous les mots de passe à l’aide du script suivant :

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
$aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
Import-Module adsync
$c = Get-ADSyncConnector -Name $adConnector
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1
$c.GlobalParameters.Remove($p.Name)
$c.GlobalParameters.Add($p)
$c = Add-ADSyncConnector -Connector $c
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true
```

## Étapes suivantes
* [Azure AD Connect Sync : personnalisation des options de synchronisation](active-directory-aadconnectsync-whatis.md)
* [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0824_2016-->