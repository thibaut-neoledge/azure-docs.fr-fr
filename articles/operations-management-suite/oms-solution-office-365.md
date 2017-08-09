---
title: Solutions Office 365 dans Operations Management Suite (OMS) | Documents Microsoft
description: "Cet article fournit des détails sur la configuration et l’utilisation de la solution Office 365 dans OMS.  Il inclut une description détaillée des enregistrements d’Office 365 créés dans le Log Analytics."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: dcc44986acbb76eafc3cfacb79acf237802de021
ms.contentlocale: fr-fr
ms.lasthandoff: 07/25/2017

---
# <a name="office-365-solution-in-operations-management-suite-oms"></a>Solutions Office 365 dans Operations Management Suite (OMS)

![Logo Office 365](media/oms-solution-office-365/icon.png)

La solution Office 365 pour Operations Management Suite (OMS) vous permet de surveiller votre environnement Office 365 dans Log Analytics.  

- Surveillez les activités des utilisateurs dans vos comptes Office 365 pour analyser les modèles d’utilisation ainsi que pour identifier les tendances de comportement. Par exemple, vous pouvez extraire des scénarios d’utilisation spécifiques, tels que les fichiers partagés en dehors de votre organisation ou les sites SharePoint les plus populaires.
- Analysez les activités d’administrateur pour effectuer le suivi des modifications de configuration ou d’opérations de privilèges élevés.
- Détectez et analysez le comportement des utilisateurs indésirables, qui peut être personnalisé pour les besoins de votre organisation.
- Présentation d’audit et de conformité. Par exemple, vous pouvez surveiller les opérations d’accès aux fichiers sur des fichiers confidentiels, ce qui peut vous aider dans le processus d’audit et de conformité.
- Effectuez le dépannage opérationnel à l’aide de la recherche OMS en haut des données d’activité Office 365 de votre organisation.

## <a name="prerequisites"></a>Composants requis
Les conditions suivantes sont requises avant l’installation et la configuration de cette solution.

- Abonnement à Office 365 organisationnel.
- Informations d’identification pour un compte d’utilisateur qui est un administrateur global.
- Pour recevoir des données d’audit, vous devez [configurer l’audit](https://support.office.com/en-us/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=en-US&rs=en-US&ad=US#PickTab=Before_you_begin) dans votre abonnement Office 365.  Notez que [l’audit de boîte aux lettres](https://technet.microsoft.com/library/dn879651.aspx) est configuré séparément.  Vous pouvez toujours installer la solution et collecter d’autres données si l’audit n’est pas configuré.
 


## <a name="management-packs"></a>Packs d’administration
Cette solution n’installe aucun pack d’administration dans les groupes d’administration connectés.
  

## <a name="configuration"></a>Configuration
Une fois [la solution Office 365 ajoutée à votre abonnement](../log-analytics/log-analytics-add-solutions.md), vous devez la connecter à votre abonnement Office 365.

1. Ajoutez la solution de gestion des alertes à votre espace de travail OMS en suivant la procédure décrite dans [Ajouter des solutions](../log-analytics/log-analytics-add-solutions.md).
2. Dans le portail OMS, accédez à **Paramètres**.
3. Sous **Sources connectées**, sélectionnez **Office 365**.
4. Cliquez sur **Connecter Office 365**.<br>![Connectez-vous à Office 365](media/oms-solution-office-365/configure.png)
5. Connectez-vous à Office 365 à l’aide d’un compte qui est un administrateur général pour votre abonnement. 
6. L’abonnement s’affiche avec les charges de travail que surveille la solution.<br>![Connectez-vous à Office 365](media/oms-solution-office-365/connected.png) 


## <a name="data-collection"></a>Collecte des données
### <a name="supported-agents"></a>Agents pris en charge
La solution Office 365 ne récupère pas des données à partir des [agents OMS](../log-analytics/log-analytics-data-sources.md).  Il récupère les données directement à partir d’Office 365.

### <a name="collection-frequency"></a>Fréquence de collecte
Office 365 envoie une [notification webhook](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications) avec des données détaillées pour le Log Analytics à chaque fois qu’un enregistrement est créé.

## <a name="using-the-solution"></a>Utilisation de la solution
Quand vous ajoutez la solution Office 365 à votre espace de travail OMS, la vignette **Office 365** est ajoutée à votre tableau de bord OMS. Cette mosaïque affiche une valeur et une représentation graphique du nombre d’ordinateurs de votre environnement et leur conformité de mise à jour.<br><br>
![Vignette du résumé Office 365](media/oms-solution-office-365/tile.png)  

Cliquez sur la vignette **Office 365** pour ouvrir le tableau de bord **Office 365**.

![Tableau de bord Office 365](media/oms-solution-office-365/dashboard.png)  

Le tableau de bord comprend les colonnes figurant dans le tableau suivant. Chaque colonne répertorie les dix premières alertes (classées par nombre d’alertes) correspondant aux critères de cette colonne pour l’étendue et l’intervalle de temps spécifiés. Vous pouvez exécuter une recherche de journaux qui fournit la liste complète. Pour cela, cliquez sur Afficher tout en bas de la colonne ou cliquez sur l’en-tête de colonne.

| Colonne | Description |
|:--|:--|
| Opérations | Fournit des informations sur les utilisateurs actifs depuis vos abonnements entièrement surveillés Office 365. Vous serez également en mesure de voir le nombre d’activités qui se produisent au fil du temps.
| Microsoft Exchange | Affiche la répartition des activités d’Exchange Server telles que l’autorisation ajouter une boîte aux lettres ou Définir une boîte aux lettres. |
| SharePoint | Affiche les activités supérieures que les utilisateurs exécutent sur les documents SharePoint. Quand vous explorez depuis cette vignette, la page de recherche affiche les détails de ces activités, tels que le document cible et l’emplacement de cette activité. Par exemple, pour un événement Fichier ouvert, vous pouvez afficher le document ouvert, son nom de compte associé et son adresse IP. |
| Azure Active Directory | Inclut les activités utilisateur supérieures, telles que la réinitialisation du mot de passe utilisateur et les tentatives de connexion. Quand vous explorez, vous pouvez afficher les détails de ces activités, telles que l’état du résultat. Ceci est particulièrement utile si vous souhaitez surveiller les activités suspectes sur votre Azure Active Directory. |




## <a name="log-analytics-records"></a>Enregistrements Log Analytics

Tous les enregistrements créés dans l’espace de travail Log Analytics par la solution Office 365 ont un **Type** de **OfficeActivity**.  La propriété **OfficeWorkload** détermine le service Office 365 auquel fait référence l’enregistrement : Exchange, AzureActiveDirectory, SharePoint ou OneDrive.  La propriété **RecordType** spécifie le type d’opération.  Les propriétés varient pour chaque type d’opération et sont affichées dans les tables ci-dessous.

### <a name="common-properties"></a>Propriétés communes
Les propriétés suivantes sont communes à tous les enregistrements d’Office 365.

| Propriété | Description |
|:--- |:--- |
| Type | *OfficeActivity* |
| ClientIP | L’adresse IP du terminal qui a été utilisée lorsque l’activité a été enregistrée. L’adresse IP s’affiche au format d’adresse IPv4 ou IPv6. |
| OfficeWorkload | Service d’Office 365 auquel fait référence l’enregistrement.<br><br>AzureActiveDirectory<br>Microsoft Exchange<br>SharePoint|
| Opération | Le nom de l’activité utilisateur ou administrateur.  |
| OrganizationId | Le GUID pour le client Office 365 de votre organisation. Cette valeur est toujours la même pour votre organisation, quel que soit le service Office 365 dans lequel il se produit. |
| RecordType | Type d’opération effectuée. |
| ResultStatus | Indique si l’action (spécifiée dans la propriété Operation) a réussi ou non. Les valeurs possibles sont Réussie, Partiellement réussie ou Échec. Pour l’activité de l’administrateur Exchange, la valeur est True ou False. |
| UserId | L’UPN (nom d’utilisateur principal) de l’utilisateur qui a exécuté l’action enregistrée dans l’enregistrement ; par exemple, my_name@my_domain_name. Notez que les enregistrements pour l’activité exécutée par les comptes système (tels que SHAREPOINT\system ou NTAUTHORITY\SYSTEM) sont également inclus. | 
| UserKey | Un autre ID pour l’utilisateur identifié dans la propriété UserId.  Par exemple, cette propriété est remplie par l’ID unique du passeport (PUID) pour les événements exécutés par les utilisateurs dans SharePoint, OneDrive entreprise et Exchange. Cette propriété peut également spécifier la même valeur que la propriété UserID pour les événements qui se produisent dans d’autres services et les événements exécutés par des comptes système|
| UserType | Le type d’utilisateur qui a effectué l’opération.<br><br>Admin<br>Application<br>DcAdmin<br>Standard <br>Réservé<br>ServicePrincipal<br>System |


### <a name="azure-active-directory-base"></a>Base Azure Active Directory
Les propriétés suivantes sont communes à tous les enregistrements d’Azure Active Directory.

| Propriété | Description |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | Le type de client Azure AD. |
| ExtendedProperties | Les propriétés étendues de l’événement d’Azure AD. |


### <a name="azure-active-directory-account-logon"></a>Connexion au compte Azure Active Directory
Ces enregistrements sont créés lorsqu’un utilisateur Active Directory tente de se connecter.

| Propriété | Description |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectoryAccountLogon |
| Application | L’application qui a déclenché l’événement de connexion du compte, telles que Office 15. |
| Client | Détails sur le terminal client, le système d’exploitation du terminal et le navigateur du terminal qui a été utilisé pour l’événement de connexion du compte. |
| LoginStatus | Cette propriété est directement issue de OrgIdLogon.LoginStatus. Le mappage de différentes connexions intéressantes peut être effectué par les alertes des algorithmes. |
| UserDomain | Les informations d’identité client (TII). | 


### <a name="azure-active-directory"></a>Azure Active Directory
Ces enregistrements sont créés lors de la modification ou des ajouts apportés aux objets Azure Active Directory.

| Propriété | Description |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AADTarget | L’utilisateur sur lequel l’action est exécutée (identifiée par la propriété Opération). |
| Acteur | L'utilisateur ou le principal du service qui a effectué l'action. |
| ActorContextId | Le GUID de l’organisation à laquelle appartient l’acteur. |
| ActorIpAddress | L’adresse IP de l’acteur au format d’adresse IPV4 ou IPV6. |
| InterSystemsId | Le GUID qui effectuent le suivi des actions sur des composants au sein du service Office 365. |
| IntraSystemId |   Le GUID généré par Azure Active Directory pour effectuer le suivi de l’action. |
| SupportTicketId | L’ID du-ticket de support client pour l’action dans les situations de « agir-au-nom-de ». |
| TargetContextId | Le GUID de l’organisation à laquelle appartient l’utilisateur cible. |


### <a name="data-center-security"></a>Sécurité du centre de données
Ces enregistrements sont créés à partir des données d’audit de sécurité du centre de données.  

| Propriété | Description |
|:--- |:--- |
| EffectiveOrganization | Le nom du client vers lequel l’élévation/l’applet de commande est ciblée. |
| ElevationApprovedTime | L’horodatage de l’approbation de l’élévation. |
| ElevationApprover | Le nom d’un gestionnaire Microsoft. |
| ElevationDuration | La durée pendant laquelle l’élévation est active. |
| ElevationRequestId |  Un identifiant unique pour la requête d’élévation. |
| ElevationRole | Le rôle pour lequel l’élévation a été demandé. |
| ElevationTime | L’heure de début de l’élévation. |
| start_time | L’heure de début de l’exécution de l’applet de commande. |


### <a name="exchange-admin"></a>Administrateur Exchange
Ces enregistrements sont créés lorsque des modifications sont apportées à la configuration Exchange.

| Propriété | Description |
|:--- |:--- |
| OfficeWorkload | Microsoft Exchange |
| RecordType     | ExchangeAdmin |
| ExternalAccess |  Spécifie si l’applet de commande a été exécutée par un utilisateur dans votre organisation, par le personnel du centre de données Microsoft ou un compte de service du centre de données ou par un administrateur délégué. La valeur False indique que l’applet de commande a été exécuté par une personne dans votre organisation. La valeur True indique que l’applet de commande a été exécutée par le personnel du centre de données, un compte de service du centre de données ou un administrateur délégué. |
| ModifiedObjectResolvedName |  Il s’agit du nom convivial de l’objet qui a été modifié par l’applet de commande. Cette opération est enregistrée uniquement si l’applet de commande modifie l’objet. |
| OrganizationName | Le nom du client. |
| OriginatingServer | Le nom du serveur à partir duquel l’applet de commande a été exécutée. |
| parameters | Le nom et la valeur de tous les paramètres utilisés avec l’applet de commande qui est identifiée dans la propriété Operations. |


### <a name="exchange-mailbox"></a>Boîte aux lettres Exchange
Ces enregistrements sont créés lorsque des modifications ou ajouts sont apportés aux boîtes aux lettres Exchange.

| Propriété | Description |
|:--- |:--- |
| OfficeWorkload | Microsoft Exchange |
| RecordType     | ExchangeItem |
| ClientInfoString | Informations sur le client de messagerie utilisé pour effectuer l’opération, telles que les informations sur la version du navigateur, la version d’Outlook et l’appareil mobile. |
| Client_IPAddress | L’adresse IP du terminal utilisée lorsque l’activité a été enregistrée. L’adresse IP s’affiche au format d’adresse IPv4 ou IPv6. |
| ClientMachineName | Le nom de l’ordinateur qui héberge le client Outlook. |
| ClientProcessName | Le client de messagerie utilisé pour accéder à la boîte aux lettres. |
| ClientVersion | La version du client de messagerie. |
| InternalLogonType | Réservé à un usage interne. |
| Logon_Type | Indique le type d’utilisateur qui a accédé à la boîte aux lettres et effectué l’opération qui a été enregistrée. |
| LogonUserDisplayName |    Le nom convivial de l’utilisateur qui a effectué l’opération. |
| LogonUserSid | Le SID de l’utilisateur qui a effectué l’opération. |
| MailboxGuid | Le GUID Exchange de la boîte aux lettres ouverte. |
| MailboxOwnerMasterAccountSid | SID de compte principal du compte du propriétaire de boîte aux lettres. |
| MailboxOwnerSid | Le SID du propriétaire de la boîte aux lettres. |
| MailboxOwnerUPN | L’adresse de messagerie de la personne qui possède la boîte ouverte. |


### <a name="exchange-mailbox-audit"></a>Audit de la boîte aux lettres Exchange
Ces enregistrements sont créés lors de la création d’une entrée d’audit de boîte aux lettres.

| Propriété | Description |
|:--- |:--- |
| OfficeWorkload | Microsoft Exchange |
| RecordType     | ExchangeItem |
| Item | Représente l’élément sur lequel l’opération a été effectuée | 
| SendAsUserMailboxGuid | Le GUID Exchange de la boîte aux lettres ouverte pour envoyer un e-mail. |
| SendAsUserSmtp | Adresse SMTP de l’utilisateur dont le nom a été emprunté. |
| SendonBehalfOfUserMailboxGuid | Le GUID Exchange de la boîte aux lettres ouverte pour envoyer un e-mail au nom de. |
| SendOnBehalfOfUserSmtp | Adresse SMTP de l’utilisateur au nom duquel le courrier électronique est envoyé. |


### <a name="exchange-mailbox-audit-group"></a>Groupe d’audit de la boîte aux lettres Exchange
Ces enregistrements sont créés lorsque des modifications ou des ajouts sont apportés aux groupes Exchange.

| Propriété | Description |
|:--- |:--- |
| OfficeWorkload | Microsoft Exchange |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | Informations sur chaque élément dans le groupe. |
| CrossMailboxOperations | Indique si l’opération a impliqué plusieurs boîtes aux lettres. |
| DestMailboxId | Définie uniquement si le paramètre CrossMailboxOperations a la valeur True. Spécifie le GUID de la boîte aux lettres cible. |
| DestMailboxOwnerMasterAccountSid | Définie uniquement si le paramètre CrossMailboxOperations a la valeur True. Spécifie le SID pour le SID du compte principal du propriétaire de la boîte aux lettres cible. |
| DestMailboxOwnerSid | Définie uniquement si le paramètre CrossMailboxOperations a la valeur True. Spécifie le SID de la boîte aux lettres cible. |
| DestMailboxOwnerUPN | Définie uniquement si le paramètre CrossMailboxOperations a la valeur True. Spécifie le nom UPN du propriétaire de la boîte aux lettres cible. |
| DestFolder | Le dossier de destination, pour les opérations telles que Déplacer. |
| Dossier | Le dossier où se trouve un groupe d’éléments. |
| Dossiers |     Plus d’informations sur les dossiers sources impliqués dans une opération ; par exemple, si les dossiers sont sélectionnés, puis supprimés. |


### <a name="sharepoint-base"></a>SharePoint Base
Ces propriétés sont communes à tous les enregistrements SharePoint.

| Propriété | Description |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| EventSource | Identifie qu’un événement s’est produit dans SharePoint. Les valeurs possibles sont ObjectModel ou SharePoint. |
| itemType | Le type d’objet qui a été ouvert ou modifié. Consultez la table ItemType pour plus d’informations sur les types d’objets. |
| MachineDomainInfo | Informations sur les opérations de synchronisation de terminal. Ces informations sont enregistrées uniquement si elles sont présente dans la requête. |
| MachineId |   Informations sur les opérations de synchronisation de terminal. Ces informations sont enregistrées uniquement si elles sont présente dans la requête. |
| Site_ | Le GUID du site où se trouve le fichier ou le dossier ouvert par l’utilisateur. |
| Source_Name | L’entité qui a déclenché l’opération d’audit. Les valeurs possibles sont ObjectModel ou SharePoint. |
| UserAgent | Informations sur le client ou le navigateur de l’utilisateur. Ces informations sont fournies par le client ou le navigateur. |


### <a name="sharepoint-schema"></a>Schéma de SharePoint
Ces enregistrements sont créés lorsque des modifications de configuration sont apportées à SharePoint.

| Propriété | Description |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| CustomEvent | Chaîne facultative pour les événements personnalisés. |
| Event_Data |  Charge facultative pour les événements personnalisés. |
| ModifiedProperties | La propriété est incluse pour les événements d’administrateur, tels que l’ajout d’un utilisateur en tant que membre d’un site ou un groupe administrateur de collection de sites. La propriété inclut le nom de la propriété modifiée (par exemple, le groupe d’administrateurs de site), la nouvelle valeur de la propriété modifiée (l’utilisateur ajouté en tant qu’un administrateur de site) et la valeur précédente de l’objet modifié. |


### <a name="sharepoint-file-operations"></a>Opérations sur les fichiers SharePoint
Ces enregistrements sont créés en réponse aux opérations de fichiers dans SharePoint.

| Propriété | Description |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | L’extension de fichier d’un fichier qui est copié ou déplacé. Cette propriété s’affiche uniquement pour les événements FileCopied et FileMoved. |
| DestinationFileName | Le nom du fichier qui est copié ou déplacé. Cette propriété s’affiche uniquement pour les événements FileCopied et FileMoved. |
| DestinationRelativeUrl | L’URL du dossier de destination dans lequel un fichier est copié ou déplacé. La combinaison des valeurs pour les paramètres SiteURL, DestinationRelativeURL et DestinationFileName est identique à la valeur de la propriété ObjectID, qui est le nom de chemin d’accès complet du fichier qui a été copié. Cette propriété s’affiche uniquement pour les événements FileCopied et FileMoved. |
| SharingType | Le type d’autorisations de partage attribuées à l’utilisateur avec lequel la ressource a été partagée. Cet utilisateur est identifié par le paramètre UserSharedWith. |
| Site_Url | L’URL du site où se trouve le fichier ou le dossier ouvert par l’utilisateur. |
| SourceFileExtension | L’extension de fichier du fichier qui est ouvert par l’utilisateur. Cette propriété est vide si l’objet a été ouvert est un dossier. |
| SourceFileName |  Le nom du fichier ou du dossier ouvert par l’utilisateur. |
| SourceRelativeUrl | L’URL du dossier qui contient le fichier ouvert par l’utilisateur. La combinaison des valeurs pour les paramètres SiteURL, DestinationRelativeURL et DestinationFileName est identique à la valeur de la propriété ObjectID, qui est le nom de chemin d’accès complet du fichier ouvert par l’utilisateur. |
| UserSharedWith |  L’utilisateur avec lequel une ressource a été partagée. |




## <a name="sample-log-searches"></a>Exemples de recherches de journaux
Le tableau suivant fournit des exemples de recherches de journaux pour les enregistrements de mises à jour collectés par cette solution.

| Requête | Description |
| --- | --- |
|Nombre de toutes les opérations sur votre abonnement Office 365 |`Type = OfficeActivity | measure count() by Operation` |
|Utilisation des sites SharePoint|' Type = OfficeActivity OfficeWorkload = sharepoint | measure count() as Count by SiteUrl | sort Count asc`|
|Opérations d’accès de fichier par type d’utilisateur|`Type=OfficeActivity OfficeWorkload=sharepoint Operation=FileAccessed | measure count() by UserType`|
|Recherche avec un mot clé spécifique|`Type=OfficeActivity OfficeWorkload=azureactivedirectory "MyTest"`|
|Analyser des actions externes sur Exchange|`Type=OfficeActivity OfficeWorkload=exchange ExternalAccess = true`|



## <a name="troubleshooting"></a>Résolution de problèmes

Si votre solution Office 365 ne collecte pas les données comme prévu, vérifiez son état dans le portail OMS sur **Paramètres** -> **Sources connectées** -> **Office 365**. La table suivante décrit chaque état.

| État | Description |
|:--|:--|
| Actif | L’abonnement Office 365 est actif et la charge de travail est correctement connectée à votre espace de travail OMS. |
| Pending | L’abonnement Office 365 est actif mais la charge de travail est correctement connectée à votre espace de travail OMS. La première fois que vous vous connectez à l’abonnement Office 365, les charges de travail seront à cet état jusqu'à ce qu’ils soient correctement connectés. Patientez 24 heures pour les charges de travail pour passer à l’état Actif. |
| Inactif | L’abonnement à Office 365 est dans un état inactif. Vérifiez votre page d’administrateur d’Office 365 pour plus d’informations. Après avoir activé votre abonnement Office 365, supprimez la liaison à partir de votre espace de travail OMS et liez-le à nouveau pour commencer à recevoir des données. |



## <a name="next-steps"></a>Étapes suivantes
* Utilisez les recherches de journaux de [Log Analytics](../log-analytics/log-analytics-log-searches.md) pour afficher des données détaillées sur les mises à jour.
* [Créez vos propres tableaux de bord](../log-analytics/log-analytics-dashboards.md) pour afficher vos requêtes de recherche favoris Office 365.
* [Créez des alertes](../log-analytics/log-analytics-alerts.md) pour être informé de façon proactive des activités importantes Office 365.  

