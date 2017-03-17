---
title: "Résolution des problèmes de synchronisation de mot de passe avec Azure AD Connect Sync | Microsoft Docs"
description: "Fournit des informations sur la résolution des problèmes de synchronisation de mot de passe"
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: e7ad2cb4c464c7095f704cc137f1c42422fbb40e
ms.openlocfilehash: 4c42821b95d666721b84d4976966b4886e517193
ms.lasthandoff: 03/01/2017


---
# <a name="troubleshoot-password-synchronization-with-azure-ad-connect-sync"></a>Résolution des problèmes de synchronisation de mot de passe avec Azure AD Connect Sync
Cette rubrique explique comment résoudre les problèmes de synchronisation de mot de passe. Si les mots de passe ne se synchronisent pas comme prévu, il peut s’agir d’un sous-ensemble d’utilisateurs ou de tous les utilisateurs.

* Si vous rencontrez un problème qui bloque la synchronisation des mots de passe, voir [Résoudre les problèmes bloquant la synchronisation des mots de passe](#no-passwords-are-synchronized).
* Si vous rencontrez un problème avec des objets individuels, voir [Dépanner un objet qui bloque la synchronisation des mots de passe](#one-object-is-not-synchronizing-passwords).

## <a name="no-passwords-are-synchronized"></a>Aucun mot de passe n’est synchronisé
Procédez comme suit pour déterminer la raison pour laquelle aucun mot de passe n’est synchronisé :

1. Le serveur Connect est-il en [mode intermédiaire](active-directory-aadconnectsync-operations.md#staging-mode) ? Un serveur en mode intermédiaire ne synchronise pas les mots de passe.
2. Exécutez le script dans la section [Obtenir l’état des paramètres de synchronisation de mot de passe](#get-the-status-of-password-sync-settings). Cela vous donne une vue d’ensemble de la configuration de la synchronisation de mot de passe.  
![Sortie de script PowerShell à partir des paramètres de synchronisation de mot de passe](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/psverifyconfig.png)  
3. Si cette fonction n’est pas activée dans Azure AD ou si l’état du canal de synchronisation n’est pas activé, exécutez l’Assistant Installation d’Azure AD Connect. Sélectionnez **Personnaliser les options de synchronisation** et décochez la synchronisation de mot de passe. Cette modification désactive temporairement la fonction. Réexécutez l’Assistant, puis réactivez la synchronisation de mot de passe. Réexécutez le script pour vérifier que la configuration est correcte.
4. Recherchez des erreurs dans le journal des événements. Recherchez les événements suivants, qui indiquent un problème :
    1. Source : « Synchronisation d’annuaires » ID : 0, 611, 652, 655 Si vous les voyez, vous avez un problème de connectivité. Le message du journal des événements contient des informations de forêt là où vous avez un problème. Pour plus d’informations, consultez [Problème de connectivité](#connectivity problem)
5. Si vous ne voyez aucune pulsation ou que rien d’autre n’a fonctionné, puis exécutez [Déclencher une synchronisation complète de tous les mots de passe](#trigger-a-full-sync-of-all-passwords). Vous ne devez exécuter ce script qu’une seule fois.
6. Lisez la section [Dépanner un objet qui bloque la synchronisation des mots de passe](#one-object-is-not-synchronizing-passwords).

### <a name="connectivity-problem"></a>Problème de connectivité

1. Avez-vous une connectivité avec Azure AD ?
2. Le compte dispose-t-il des autorisations requises pour lire les hachages de mot de passe dans tous les domaines ? Si vous avez installé Connect à l’aide des paramètres Express, les autorisations doivent déjà être correctes. Si vous avez utilisé une installation personnalisée, vous devez définir les autorisations manuellement.
    1. Pour trouver le compte utilisé par le connecteur Active Directory, démarrez **Synchronization Service Manager**. Accédez à **Connecteurs** et recherchez la forêt locale Active Directory que vous dépannez. Sélectionnez le connecteur et cliquez sur **Propriétés**. Accédez à **Se connecter à la forêt Active Directory**.  
    ![Compte utilisé par le connecteur AD Connector](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/connectoraccount.png)  
    Prenez note du nom d’utilisateur et du domaine dans lequel le compte se trouve.
    2. Lancez **Utilisateurs et ordinateurs Active Directory**. Vérifiez que le compte que vous avez trouvé lors des précédentes étapes dispose de ces autorisations de suivi définies à la racine de tous les domaines de votre forêt :
        * Répliquer les changements d’annuaires
        * Répliquer les changements d’annuaire Tout
3. Les contrôleurs de domaine sont-ils accessibles pour Azure AD Connect ? Si le serveur Connect ne peut pas se connecter à tous les contrôleurs de domaine, vous devez configurer **Utiliser uniquement le contrôleur de domaine préféré**.  
    ![Contrôleur de domaine utilisé par AD Connector](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/preferreddc.png)  
    Revenez à **Synchronization Service Manager** et **Configurer une partition d’annuaire**. Sélectionnez votre domaine dans **Sélectionner les partitions d’annuaire**, cochez la case **Utiliser uniquement les contrôleurs de domaine préférés**, puis cliquez sur **Configurer**. Dans la liste, entrez les contrôleurs de domaine que Connect doit utiliser pour la synchronisation de mot de passe. La même liste est également utilisée pour importer et exporter. Effectuez ces étapes pour tous vos domaines.
4. Si le script indique qu’il n’y a aucune pulsation, exécutez le script [Déclencher une synchronisation complète de tous les mots de passe](#trigger-a-full-sync-of-all-passwords).

## <a name="one-object-is-not-synchronizing-passwords"></a>Un objet qui bloque la synchronisation des mots de passe
Vous pouvez résoudre facilement les problèmes liés à la synchronisation des mots de passe en consultant l’état d’un objet.

1. Lancez **Utilisateurs et ordinateurs Active Directory**. Recherchez l’utilisateur et vérifiez que la case **L’utilisateur doit changer le mot de passe à la prochaine ouverture de session** n’est pas cochée.  
![Mots de passe productifs Active Directory](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/adprodpassword.png)  
Si elle est cochée, demandez à l’utilisateur de se connecter et de modifier son mot de passe. Les mots de passe temporaires ne se synchronisent pas sur Azure AD.
2. Si tout semble correct dans Active Directory, passez à l’étape suivante qui consiste à suivre l’utilisateur dans le moteur de synchronisation. En suivant l’utilisateur depuis le répertoire Active Directory local vers Azure AD, vous pouvez voir si un message d’erreur descriptif apparaît sur l’objet.
    1. Lancez **[Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md)**.
    2. Cliquez sur **Connecteurs**.
    3. Sélectionnez le **connecteur Active Directory** dans lequel se trouve l’utilisateur.
    4. Sélectionnez **Search Connector Space**(Rechercher l’espace de connecteur).
    5. Dans **Étendue**, sélectionnez **DN ou ancre**. Entrez le nom unique complet de l’utilisateur que vous dépannez.
    ![Rechercher un utilisateur dans cs avec un nom de domaine](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/searchcs.png)  
    6. Localisez l’utilisateur que vous recherchez et cliquez sur **Propriétés** pour voir tous ses attributs. Si l’utilisateur n’est pas dans les résultats de la recherche, vérifiez vos [règles de filtrage](active-directory-aadconnectsync-configure-filtering.md) et assurez-vous que vous exécutez [Appliquer et vérifier les modifications](active-directory-aadconnectsync-configure-filtering.md#apply-and-verify-changes) pour que l’utilisateur s’affiche dans Connect.
    7. Pour afficher les détails de synchronisation de mot de passe de l’objet pour la semaine écoulée, cliquez sur **Journal...**.  
    ![Détails d’un journal objet](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/csobjectlog.png)  
    Si le journal de l’objet est vide, cela signifie qu’Azure AD Connect n’a pas pu lire le hachage de mot de passe à partir d’Active Directory. Continuez la résolution des problèmes avec [Erreurs de connectivité](#connectivity-errors). Si vous voyez une valeur autre que **Réussite**, consultez le tableau dans [Journal de synchronisation de mot de passe](#password-sync-log).
    8. Sélectionnez l’onglet **lignage** et assurez-vous qu’au moins une règle de synchronisation utilise la valeur **True** pour **Synchronisation de mot de passe**. Dans la configuration par défaut, le nom de la règle de synchronisation est **In from AD - User AccountEnabled**.  
    ![Informations de lignage sur un utilisateur](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/cspasswordsync.png)  
    9. Cliquez sur **Propriétés de l’objet métaverse**. Vous voyez une liste des attributs de l’utilisateur.  
    ![Informations de métaverse](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/mvpasswordsync.png)  
    Vérifiez qu’il n’existe aucun attribut **cloudFiltered** présent. Assurez-vous que les attributs de domaine (domainFQDN et domainNetBios) ont les valeurs attendues.
    10. Cliquez sur l’onglet **Connecteurs**. Vérifiez que vous voyez les connecteurs pour AD local et Azure AD.
    ![Informations de métaverse](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/mvconnectors.png)  
    11. Sélectionnez la ligne représentant Azure AD et cliquez sur **Propriétés**. Cliquez sur l’onglet **Lignage**. L’objet d’espace connecteur doit avoir une règle sortante avec une **synchronisation de mot de passe** définie sur **True**. Dans la configuration par défaut, le nom de la règle de synchronisation est **Out to AAD - User Join**.  
    ![Propriétés d’espace de connecteur d’un utilisateur](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/cspasswordsync2.png)  

### <a name="password-sync-log"></a>Journal de synchronisation de mot de passe
La colonne Statut peut avoir les valeurs suivantes :

| État | Description |
| --- | --- |
| Succès |Le mot de passe a été correctement synchronisé. |
| FilteredByTarget |Le mot de passe est défini sur **L'utilisateur doit changer de mot de passe à la prochaine connexion**. Mot de passe n'a pas été synchronisé. |
| NoTargetConnection |Aucun objet dans le métaverse ou dans l'espace de connecteur Azure AD. |
| SourceConnectorNotPresent |Aucun objet trouvé dans l'espace de connecteur Active Directory local. |
| TargetNotExportedToDirectory |L'objet dans l'espace de connecteur Azure AD n'a pas encore été exporté. |
| MigratedCheckDetailsForMoreInfo |L'entrée de journal a été créée avant la version 1.0.9125.0 et est affichée dans son état hérité. |

## <a name="scripts-to-help-troubleshooting"></a>Scripts pour faciliter le dépannage

### <a name="get-the-status-of-password-sync-settings"></a>Obtenir l’état des paramètres de synchronisation de mot de passe
```
Import-Module ADSync
$connectors = Get-ADSyncConnector
$aadConnectors = $connectors | Where-Object {$_.SubType -eq "Windows Azure Active Directory (Microsoft)"}
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

#### <a name="trigger-a-full-sync-of-all-passwords"></a>Déclencher une synchronisation complète de tous les mots de passe
> [!NOTE]
> Vous ne devez exécuter ce script qu’une seule fois. Si vous devez l’exécuter plusieurs fois, le problème est ailleurs. Contactez le support technique Microsoft pour vous aider à résoudre le problème.

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

## <a name="next-steps"></a>Étapes suivantes
* [Implémentation de la synchronisation de mot de passe avec Azure AD Connect Sync](active-directory-aadconnectsync-implement-password-synchronization.md)
* [Azure AD Connect Sync : personnalisation des options de synchronisation](active-directory-aadconnectsync-whatis.md)
* [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)

