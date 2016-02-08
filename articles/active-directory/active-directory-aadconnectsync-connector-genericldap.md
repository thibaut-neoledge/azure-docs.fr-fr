<properties
   pageTitle="Azure AD Connect Sync : connecteur LDAP générique | Microsoft Azure"
   description="Cet article décrit comment configurer le connecteur LDAP générique Microsoft."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="01/21/2016"
   ms.author="andkjell"/>

# Référence technique au connecteur LDAP générique

Cet article décrit le connecteur LDAP générique. Cet article s’applique aux produits suivants :

- Microsoft Identity Manager 2016 (MIM2016)
- Forefront Identity Manager 2010 R2 (FIM2010R2)
    -   Doit utiliser le correctif logiciel 4.1.3461.0 ou une version [KB2870703](https://support.microsoft.com/kb/2870703) ultérieure.

Pour MIM2016 et FIM2010R2, le connecteur est disponible en téléchargement dans le [Centre de téléchargement Microsoft](http://go.microsoft.com/fwlink/?LinkId=717495).

Lorsque vous faites référence aux IETF RFC, ce document est au format (RFC [numéro RFC]/[section dans le document RFC]), par exemple (RFC 4512/4.3). Vous trouverez d’autres informations à l’adresse http://tools.ietf.org/html/rfc4500 (vous devez remplacer 4500 par le numéro de RFC correct).

## Vue d’ensemble du connecteur LDAP générique

Le connecteur LDAP générique vous permet d’intégrer le service de synchronisation au serveur LDAP v3.

Certaines opérations et certains éléments de schéma, notamment ceux qui sont nécessaires pour effectuer l’importation différentielle, ne sont pas spécifiés dans la RFC de l’IETF. Pour ces opérations, seuls les annuaires LDAP spécifiés explicitement sont pris en charge.

À un niveau élevé, les fonctionnalités suivantes sont prises en charge par la version actuelle du connecteur :

| Fonctionnalité | Support |
| --- | --- |
| Source de données connectée | Le connecteur est pris en charge avec tous les serveurs v3 LDAP (compatibles RFC 4510). Il a été testé avec les éléments suivants : <li>Microsoft Active Directory Lightweight Directory Services (AD LDS)</li><li>Catalogue global Microsoft Active Directory (AD GC)</li><li>389 Active Server </li><li>Apache Active Server</li><li>IBM Tivoli DS</li><li>Isode active</li><li>NetIQ eDirectory</li><li>Novell eDirectory</li><li>DJ Open</li><li>DS Open</li><li>Open LDAP (openldap.org)</li><li>Oracle (précédemment Sun) Directory Server Enterprise Edition</li><li>RadiantOne Virtual Directory Server (VDS)</li><li>Sun One Directory Server</li>Annuaires connus non pris en charge : <li>Microsoft Active Directory Domain Services (AD DS) [utiliser le connecteur Active Directory intégré à la place]</li><li>Oracle Internet Directory (OID)</li> |
| Scénarios | <li>Gestion du cycle de vie des objets</li><li>Gestion des groupes</li><li>Gestion des mots de passe</li> |
| Opérations |Tous les annuaires LDAP prennent en charge les opérations suivantes : <li>importation</li><li>Exportation complètes</li>Les opérations suivantes sont prises en charge sur les annuaires spécifiés uniquement :<li>importation différentielle</li><li>Définition du mot de passe, Modification du mot de passe</li> |
| Schéma | Le <li>schéma est détecté depuis le schéma LDAP (RFC3673 et RFC4512/4.2)</li><li>Prend en charge des classes structurelles, les classes auxiliaires et la classe d’objets extensibleObject (RFC4512/4.3)</li>

### Importation différentielle et prise en charge de la gestion par mot de passe

Les répertoires pris en charge pour l’importation différentielle et la gestion de mot de passe :

- Microsoft Active Directory Lightweight Directory Services (AD LDS)
    - Prend en charge toutes les opérations d’importation différentielle
    - Prend en charge la définition de mot de passe
- Catalogue global Microsoft Active Directory (AD GC)
    - Prend en charge toutes les opérations d’importation différentielle
    - Prend en charge la définition de mot de passe
- Serveur d’annuaire 389
    - Prend en charge toutes les opérations d’importation différentielle
    - Prend en charge la définition de mot de passe et la modification de mot de passe
- Apache Directory Server
    - Ne prend pas en charge l’importation différentielle depuis ce répertoire n’a pas de journal des modifications persistantes
    - Prend en charge la définition de mot de passe
- IBM Tivoli DS
    - Prend en charge toutes les opérations d’importation différentielle
    - Prend en charge la définition de mot de passe et la modification de mot de passe
- Isode Directory
    - Prend en charge toutes les opérations d’importation différentielle
    - Prend en charge la définition de mot de passe et la modification de mot de passe
- Novell eDirectory et NetIQ eDirectory
    - Prend en charge les opérations d’ajout, de mise à jour et de renommage pour l’importation différentielle
    - Ne prend pas en charge les opérations de suppression pour l’importation différentielle
    - Prend en charge la définition de mot de passe et la modification de mot de passe
- Open DJ
    - Prend en charge toutes les opérations d’importation différentielle
    - Prend en charge la définition de mot de passe et la modification de mot de passe
- Open DS
    - Prend en charge toutes les opérations d’importation différentielle
    - Prend en charge la définition de mot de passe et la modification de mot de passe
- Open LDAP (openldap.org)
    - Prend en charge toutes les opérations d’importation différentielle
    - Prend en charge la définition de mot de passe
    - Ne prend pas en charge la modification de mot de passe
- Oracle (précédemment Sun) Directory Server Enterprise Edition
    - Prend en charge toutes les opérations d’importation différentielle
    - Prend en charge la définition de mot de passe et la modification de mot de passe
- RadiantOne Virtual Directory Server (VDS)
    - Doit utiliser la version 7.1.1 ou une version ultérieure
    - Prend en charge toutes les opérations d’importation différentielle
    - Prend en charge la définition de mot de passe et la modification de mot de passe
-  Sun One Directory Server
    - Prend en charge toutes les opérations d’importation différentielle
    - Prend en charge la définition de mot de passe et la modification de mot de passe

### Composants requis

Avant d’utiliser le connecteur, vérifiez que vous disposez des éléments suivants sur le serveur de synchronisation en plus de tout autre correctif mentionné ci-dessus :

- Microsoft .NET 4.5.2 Framework ou version ultérieure

### Détection du serveur LDAP

Le connecteur utilise diverses techniques pour détecter et identifier le serveur LDAP. Le connecteur utilise la DSE racine pour trouver le nom du fournisseur et la version, et il inspecte le schéma pour rechercher des objets uniques et des attributs connus pour exister dans certains serveurs LDAP. Ces données, si elles sont détectées, sont utilisées pour prérenseigner les options de configuration du connecteur.

### Autorisations de la source de données connectée

Pour effectuer des opérations d’importation et d’exportation d’objets dans l’annuaire connecté, le compte de connecteur doit disposer des autorisations suffisantes. Le connecteur aura besoin d’autorisations en écriture pour pouvoir exporter, lire et d’autorisations en écriture pour procéder à l’importation. La configuration d’autorisation est exécutée au sein des expériences de gestion sur le répertoire cible lui-même.

### Ports et protocoles

Le connecteur doit utiliser le numéro de port spécifié dans la configuration (par défaut, le 389 pour LDAP et 636 pour les LDAPS.

Pour le protocole LDAPS, vous devez utiliser SSL 3.0 ou TLS. SSL 2.0 n’est pas pris en charge et ne peut être activé.

### Fonctionnalités et contrôles requis

Les contrôles/fonctionnalités LDAP suivants doivent être disponibles sur le serveur LDAP pour que le connecteur fonctionne correctement :

- 1\.3.6.1.4.1.4203.1.5.3 Filtres True/False

Le filtre True/False n’est souvent pas signalé comme pris en charge par les annuaires LDAP et peut apparaître sur la **Page Global** sous **fonctionnalités obligatoires introuvables**. Il est utilisé pour créer **ou** filtrer dans les requêtes LDAP, par exemple, en cas d’importation de plusieurs types d’objets. Si vous pouvez importer plusieurs types d’objets, votre serveur LDAP prend en charge ce dernier.

Si vous utilisez un répertoire dans lequel un identificateur unique est le point d’ancrage, les éléments suivants doivent également être disponibles (voir la section [Configurer les points d’ancrage](#configure-anchors) plus loin dans cet article pour plus d’informations) :

- 1\.3.6.1.4.1.4203.1.5.1 Tous les attributs opérationnels

Si le répertoire comporte plus d’objets que ce qu’un appel à l’annuaire peut contenir, il est recommandé d’utiliser la pagination. Pour que la pagination fonctionne, vous aurez besoin d’une des options qui suivent :

**Option 1 :**

- 1\.2.840.113556.1.4.319 pagedResultsControl

**Option 2 :**

- 2\.16.840.1.113730.3.4.9 VLVControl
- 1\.2.840.113556.1.4.473 SortControl

Si les deux options sont activées dans la configuration du connecteur, c’est la propriété pagedResultsControl qui sera utilisée.

- 1\.2.840.113556.1.4.417 ShowDeletedControl

ShowDeletedControl est utilisé uniquement avec la méthode d’importation différentielle USNChanged pour être en mesure de voir les objets supprimés.

Le connecteur essaie de détecter si les options sont présentes sur le serveur. Si les options ne peuvent pas être détectées, un avertissement doit figurer sur la page des généralités, dans les propriétés du connecteur. Tous les serveurs LDAP ne présentent pas l’ensemble des contrôles/fonctionnalités qu’ils prennent en charge, et, même si cet avertissement est présent, il se peut que le connecteur fonctionne sans problème.

### Importation différentielle

L’importation différentielle est disponible uniquement lorsqu’un répertoire pris en charge a été détecté. Les méthodes suivantes sont fournies actuellement :

- Accesslog LDAP. Voir [http://www.openldap.org/doc/admin24/overlays.html#Access Logging](http://www.openldap.org/doc/admin24/overlays.html#Access Logging)
- Changelog LDAP. Voir [http://tools.ietf.org/html/draft-good-ldap-changelog-04](http://tools.ietf.org/html/draft-good-ldap-changelog-04)
- Horodatage. Pour Novell/NetIQ eDirectory, le connecteur utilise les dernières date/heure pour obtenir des objets créés et mis à jour. Novell/NetIQ eDirectory ne fournit pas de moyen équivalent de récupérer les objets. Cette option peut également être utilisée si aucune autre méthode d’importation différentielle n’est active sur le serveur LDAP. Cette option ne sera pas en mesure d’importer des objets supprimés.
- USNChanged. Consulter : [https://msdn.microsoft.com/library/ms677627.aspx](https://msdn.microsoft.com/library/ms677627.aspx)

### Non pris en charge

Les fonctionnalités LDAP suivantes ne sont pas prises en charge :

- Références LDAP entre serveurs (RFC 4511/4.1.10)

## Créer un connecteur

Pour créer un connecteur générique LDAP, dans **Service de synchronisation**, sélectionnez **Agent de gestion** et **Créer**. Sélectionnez le connecteur **Generic LDAP (Microsoft)**.

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericldap/createconnector.png)

### Connectivité

Dans la page Connectivité, vous devez spécifier les informations de l’hôte, du port et de liaison. Selon la liaison sélectionnée, d’autres informations peuvent être fournies dans les sections suivantes.

![Connectivité](./media/active-directory-aadconnectsync-connector-genericldap/connectivity.png)

- Le paramètre Délai de connexion est utilisé uniquement pour la première connexion au serveur lors de la détection du schéma.
- Si la liaison est anonyme, ni le nom d’utilisateur/mot de passe ni les certificats ne sont utilisés.
- Pour les autres liaisons, entrez les informations dans les champs de nom d’utilisateur/mot de passe ou sélectionnez un certificat.
- Si vous utilisez Kerberos pour l’authentification, fournissez également le domaine/la spécialité de l’utilisateur.

La zone de texte **Alias d’attribut** est utilisée pour les attributs définis dans le schéma avec la syntaxe RFC4522. Ceux-ci ne peuvent pas être détectés à l’occasion de la détection de schéma et le connecteur a besoin d’aide pour les identifier. Par exemple les éléments suivants sont nécessaires et doivent être saisis dans la zone d’alias d’attribut pour identifier correctement l’attribut userCertificate en tant qu’attribut binaire :

`userCertificate;binary`

Vous trouverez ci-dessous un exemple de présentation :

![Connectivité](./media/active-directory-aadconnectsync-connector-genericldap/connectivityattributes.png)

Sélectionnez la case à cocher **Inclure des attributs opérationnels dans le schéma** pour inclure également les attributs créés par le serveur. Elle inclut des attributs, notamment la date à laquelle l’objet a été créé et l’heure de la dernière mise à jour.

Sélectionnez **Inclure les attributs extensibles dans le schéma** si des objets extensibles (RFC4512/4.3) sont utilisés. L’activation de cette option permettra à chaque attribut d’être utilisé sur n’importe quel objet. La sélection de cette option peut contribuer à rendre l’annuaire connecté très volumineux, alors, à moins que le répertoire connecté utilise cette fonction, il est conseillé de garder cette option désactivée.

### Paramètres globaux

Sur la page Paramètres globaux, vous configurez un nom unique pour le journal des modifications différentielles et d’autres fonctionnalités LDAP. La page sera prérenseignée avec les informations fournies par le serveur LDAP.

![Connectivité](./media/active-directory-aadconnectsync-connector-genericldap/globalparameters.png)

La partie supérieure affiche les informations fournies par le serveur lui-même, notamment le nom du serveur. Le connecteur vérifie également que les contrôles obligatoires sont présents dans la DSE racine. Si elles ne sont pas répertoriées, un avertissement s’affiche. Certains répertoires LDAP ne répertorient pas toutes les fonctionnalités de la DSE racine et il est possible que le connecteur fonctionne sans problème, même si un avertissement s’affiche.

Les cases à cocher de **prise en charge des commandes** contrôlent le comportement de certaines opérations :

- Si l’arborescence est sélectionnée, une hiérarchie va être supprimée avec un appel LDAP. Si l’arborescence n’est pas sélectionnée, le connecteur va procéder à une suppression récursive si nécessaire.
- Une fois les résultats paginés sélectionnés, le connecteur procédera à des importations paginées avec la taille spécifiée au niveau des opérations d’exécution.
- VLVControl et SortControl sont une alternative à l’élément pagedResultsControl pour lire les données de l’annuaire LDAP.
- Si les trois options (pagedResultsControl, VLVControl et SortControl) ne sont pas sélectionnées, le connecteur importe tous les objets en une seule opération, ce qui risque d’échouer s’il s’agit d’un répertoire volumineux.
- ShowDeletedControl est utilisé uniquement lorsque la méthode d’importation différentielle est USNChanged.

Le journal des modifications DN est le contexte d’attribution de noms utilisé par le journal des modifications différentielles, par exemple, **cn=changelog**. Vous devez spécifier cette valeur pour être en mesure d’effectuer l’importation différentielle.

Voici une liste par défaut du journal de modification des noms uniques :

| Répertoire | Journal des modifications différentielles |
| --- | --- |
| Microsoft AD LDS et AD GC | Détecté automatiquement USNChanged. |
| Apache Directory Server | Non disponible |
| Directory 389 | Journal des modifications Valeur à utiliser par défaut : **cn=changelog** |
| IBM Tivoli DS | Journal des modifications Valeur à utiliser par défaut : **cn=changelog** |
| Isode Directory | Journal des modifications Valeur à utiliser par défaut : **cn=changelog**
| Novell/NetIQ eDirectory | Non disponible Horodatage. Le connecteur doit utiliser la date/heure de la dernière mise à jour pour obtenir les nouveaux enregistrements et mises à jour. |
| DJ/DS Open | Journal des modifications Valeur à utiliser par défaut : **cn=changelog** |
| LDAP Open | Journal d’accès. Valeur à utiliser par défaut : **cn= accesslog** |
| Oracle DSEE | Journal des modifications Valeur à utiliser par défaut : **cn=changelog** |
| RadiantOne VDS | Répertoire virtuel. Dépend de l’annuaire connecté à VDS. |
| Sun One Directory Server | Journal des modifications Valeur à utiliser par défaut : **cn=changelog** |

L’attribut de mot de passe est le nom de l’attribut que le connecteur doit utiliser pour définir le mot de passe dans le mot de passe et les opérations de définition de mot de passe. Par défaut, il est défini à la valeur **userPassword**, mais peut être modifié en cas de nécessité pour un système particulier de LDAP.

Dans la liste des partitions supplémentaires, il est possible d’ajouter des espaces de noms supplémentaires qui ne sont pas automatiquement détectés. Cela peut, par exemple, servir si plusieurs serveurs constituent un cluster logique et doivent être importés simultanément. Active Directory peut compter plusieurs domaines partageant le même schéma dans une forêt. On peut simuler cette situation en saisissant les espaces de noms supplémentaires dans cette zone. Chaque espace de noms peut exécuter une importation à partir de différents serveurs et être configuré plus avant dans la page de configuration des partitions et des hiérarchies. Utilisez Ctrl + Entrée pour passer sur une nouvelle ligne.

### Configurer la hiérarchie de l’approvisionnement

Cette page sert à mapper le composant de nom unique, par exemple, l’unité d’opérationnelle sur le type d’objet qui doit être configuré, par exemple, l’unité d’organisation.

![Hiérarchie de l’approvisionnement](./media/active-directory-aadconnectsync-connector-genericldap/provisioninghierarchy.png)

En configurant la hiérarchie de l’approvisionnement, vous pouvez configurer le connecteur pour créer automatiquement une structure si nécessaire. Par exemple, s’il existe un espace de noms dc=contoso, dc=com et un nouvel objet cn= Joe, ou= Seattle, c=US, dc=contoso, dc=com est configuré, alors le connecteur peut créer un nouvel objet de type pays pour les États-Unis et une unité d’organisation pour Seattle si ceux-ci ne sont pas déjà présents dans le répertoire.

### Configurer des partitions et des hiérarchies

Dans la page Partitions et hiérarchies, sélectionnez tous les espaces de noms avec les objets que vous prévoyez d’importer et d’exporter.

![Partitions](./media/active-directory-aadconnectsync-connector-genericldap/partitions.png)

Pour chaque espace de noms, il est également possible de configurer les paramètres de connectivité qui remplacent les valeurs spécifiées sur l’écran de connectivité. Si ces valeurs sont laissées à leur valeur vide par défaut, les informations issues de l’écran de Connectivité seront utilisées.

Il est également possible de sélectionner les conteneurs et les unités opérationnelles depuis lesquelles importer ou vers lesquelles exporter.

### Configuration de points d’ancrage

Cette page a toujours une valeur préconfigurée et ne peut pas être modifiée. Si le fournisseur et la version du serveur ont été identifiés, ils peuvent être renseignés par le biais d’un attribut immuable, par exemple, l’identificateur global unique d’un objet. S’il n’a pas été détecté ou s’il est connu pour ne pas posséder d’attribut immuable, le connecteur utiliser un nom unique (dn) comme point d’ancrage.

![Points d’ancrage](./media/active-directory-aadconnectsync-connector-genericldap/anchors.png)

Voici une liste de serveurs LDAP et du point d’ancrage utilisé :

| Répertoire | Attribut d’ancrage |
| --- | --- |
| Microsoft AD LDS et AD GC | objectGUID |
| Serveur d’annuaire 389 | dn |
| Apache Directory | dn |
| IBM Tivoli DS | dn |
| Isode Directory | dn |
| Novell/NetIQ eDirectory | GUID |
| DJ/DS Open | dn |
| LDAP Open | dn |
| Oracle ODSEE | dn |
| RadiantOne VDS | dn |
| Sun One Directory Server | dn |

## Autres remarques

Cette section fournit des informations sur les aspects qui spécifiques à ce connecteur ou pour d’autres raisons sont importants à connaître.

### Importation différentielle

Le filigrane différentiel dans Open LDAP est date/heure UTC. Pour cette raison, les horloges entre le service de synchronisation FIM et Open LDAP doivent être synchronisées. Dans le cas contraire, certaines entrées du journal des modifications différentielles doivent être omises.

Pour Novell eDirectory, l’importation différentielle ne détecte pas les suppressions d’objet. C’est pour cette raison qu’il faut exécuter régulièrement une importation périodique pour trouver tous les objets supprimés.

Pour les répertoires avec des journaux de modification différentielle basée sur les date/heure, il est fortement recommandé d’exécuter une importation complète à intervalles réguliers pour trouver des différences entre le serveur LDAP et l’espace de connecteur actuel.

## Résolution de problèmes

-	Pour plus d’informations sur la façon d’activer la journalisation pour résoudre les problèmes du connecteur, consultez [Comment activer le suivi ETW pour les connecteurs](http://go.microsoft.com/fwlink/?LinkId=335731).

<!---HONumber=AcomDC_0128_2016-->