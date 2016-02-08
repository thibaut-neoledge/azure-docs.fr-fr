<properties
   pageTitle="Azure AD Connect Sync : connecteur SQL générique | Microsoft Azure"
   description="Cet article explique comment configurer le connecteur SQL générique de Microsoft."
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

# Référence technique du connecteur SQL générique

Cet article décrit le connecteur SQL générique. Cet article s’applique aux produits suivants :

- Microsoft Identity Manager 2016 (MIM2016)
- Forefront Identity Manager 2010 R2 (FIM2010R2)
    -   Doit utiliser le correctif logiciel 4.1.3461.0 ou une version [KB2870703](https://support.microsoft.com/kb/2870703) ultérieure.

Pour MIM2016 et FIM2010R2, le connecteur est disponible en téléchargement dans le [Centre de téléchargement Microsoft](http://go.microsoft.com/fwlink/?LinkId=717495).

## Vue d’ensemble du connecteur SQL générique

Le connecteur SQL générique vous permet d’intégrer le service de synchronisation avec un système de base de données qui offre la connectivité ODBC.

À un niveau élevé, les fonctionnalités suivantes sont prises en charge par la version actuelle du connecteur :

| Fonctionnalité | Support |
| --- | --- |
| Source de données connectée | Le connecteur prend en charge tous les pilotes ODBC 64 bits. Il a été testé avec les éléments suivants : <li>Microsoft SQL Server et SQL Azure</li><li>IBM DB2 10.x</li><li>IBM DB2 9.x</li><li>Oracle 10 et 11g</li><li>MySQL 5.x</li>
| Scénarios | <li>Gestion du cycle de vie des objets</li><li>Gestion de mot de passe</li> |
| Opérations | <li>Importation intégrale et importation différentielle, exportation</li><li>Pour l’exportation : ajouter, supprimer, mettre à jour et remplacer</li><li>Définir le mot de passe, Modifier le mot de passe</li>
| Schéma | <li>Découverte dynamique des objets et des attributs</li>

### Composants requis

Avant d’utiliser le connecteur, vérifiez que vous disposez des éléments suivants sur le serveur de synchronisation en plus de tout autre correctif mentionné ci-dessus :

- Microsoft .NET 4.5.2 Framework ou version ultérieure
- Pilotes clients ODBC 64 bits

### Autorisations dans la source de données connectée

Pour créer ou exécuter les tâches prises en charge dans le connecteur SQL générique, vous devez disposer de :

- db\_datareader
- db\_datawriter

### Ports et protocoles

Concernant les ports requis pour que le pilote ODBC fonctionne, veuillez consulter la documentation accompagnant la base de données.

## Créer un connecteur

Pour créer un connecteur générique SQL, dans **service de synchronisation**, sélectionnez **Agent de gestion** et **Créer**. Sélectionnez le connecteur **Generic SQL (Microsoft)**.

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericsql/createconnector.png)

### Connectivité

Le connecteur utilise un fichier DSN ODBC pour la connectivité. Créez le fichier DSN en utilisant l’option **Sources de données ODBC** qui se trouve dans le menu Démarrer, sous **Outils d’administration**. Dans l’outil d’administration, créez un **Fichier DSN** pouvant être transmis au connecteur.

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericsql/connectivity.png)

L’écran Connectivité est le premier qui s’affiche lorsque vous créez un nouveau connecteur SQL générique. Dans un premier temps, fournissez les informations suivantes :

- Chemin d’accès de fichier DSN
- Authentification
    - Nom d’utilisateur
    - Mot de passe

La base de données doit prendre en charge une des méthodes d’authentification mentionnées ci-dessous.

- **Authentification Windows** : la base de données d’authentification utilisera les informations d’identification Windows pour contrôler l’utilisateur. Dans ce cas, c’est le compte de service du service de synchronisation qui sera utilisé. Ce compte aura besoin des autorisations pour la base de données.
- **Authentification SQL** : la base de données d’authentification utilisera les nom/mot de passe d’utilisateur définis pour l’écran Connectivité en vue de se connecter à la base de données. Si vous stockez le nom d’utilisateur/mot de passe dans le fichier DSN, les informations d’identification fournies sur l’écran de connexion sont prioritaires.
- **Authentification Azure SQL Database** : pour plus d’informations, voir [Connexion à la base de données SQL avec l’authentification Azure Active Directory](sql-database-aad-authentication.md).

**Le nom unique est le point d’ancrage** : si vous sélectionnez cette option, le nom de domaine doit également être utilisé comme attribut d’ancrage. Il peut être utilisé pour une implémentation simple mais présente également les limitations suivantes :

-	Le connecteur prend en charge seulement un type d’objet. Les attributs de référence peuvent faire référence à un seul type d’objet.

**Type d’exportation : remplacement d’objet** : lors de l’exportation, lorsque seuls certains attributs ont été modifiés, l’ensemble de l’objet avec tous les attributs est exporté et remplace l’objet existant.

### Schéma 1 (Détecter les types d’objets)

Sur cette page, vous allez configurer la manière dont le connecteur va rechercher les différents types d’objets dans la base de données.

Chaque type d’objet est présenté sous la forme d’une partition et configuré de manière plus approfondie sur **Configurer des partitions et des hiérarchies**.

![schema1a](./media/active-directory-aadconnectsync-connector-genericsql/schema1a.png)

**Méthode de détection du type d’objet** : le connecteur prend en charge ces méthodes de détection de type objet.

- **Valeur fixe** : fournit la liste des types d’objets avec une liste séparée par des virgules. Par exemple, Utilisateur, Groupe, Service. ![schema1b](./media/active-directory-aadconnectsync-connector-genericsql/schema1b.png)
- **Table/Affichage/Procédure stockée** : fournissez le nom de table/affichage/procédure stockée, puis le nom de colonne qui fournira la liste des types d’objets. Si vous utilisez une procédure stockée, fournissez également des paramètres pour celui-ci dans le format **[nom]:[Direction]:[Valeur]**. Placez chacun des paramètres sur une ligne distincte (utilisez Ctrl + Entrée pour créer une ligne). ![schema1c](./media/active-directory-aadconnectsync-connector-genericsql/schema1c.png)
- **Requête SQL** : cette option permet de fournir une requête SQL qui renvoie une seule colonne avec les types d’objets, par exemple, `SELECT [Column Name] FROM TABLENAME`. La colonne retournée doit être de type chaîne (varchar).

### Schéma 2 (Détecter les types d’attribut)

Sur cette page, vous allez configurer la façon dont les noms et les types d’attribut vont être détectés. Les options de configuration sont répertoriées pour chaque type d’objet détecté dans la page précédente.

![schema2a](./media/active-directory-aadconnectsync-connector-genericsql/schema2a.png)

**Méthode de détection de type d’attribut** : le connecteur prend en charge ces méthodes de détection de type attribut avec chaque type d’objet détecté dans l’écran schéma 1.

- **Table/Affichage/Procédure stockée** : indiquez le nom de table/vue/procédure stockée qui doit être utilisé pour trouver les noms d’attribut. Si vous utilisez une procédure stockée, fournissez également des paramètres pour celui-ci dans le format **[nom]:[Direction]:[Valeur]**. Placez chaque paramètre sur une ligne distincte (utilisez Ctrl + Entrée pour créer une nouvelle ligne). Pour détecter les noms d’attribut dans un attribut à valeurs multiples, fournissez une liste séparée par des virgules des tables ou des affichages. Les scénarios à valeurs multiples ne sont pas pris en charge si les tables parent et enfant ont les mêmes noms de colonnes.
- **Requête SQL** : cette option permet de fournir une requête SQL qui renvoie une seule colonne avec des noms d’attribut, par exemple, `SELECT [Column Name] FROM TABLENAME`. La colonne retournée doit être de type chaîne (varchar).

### Schéma 3 (Définir le point d’ancrage et le nom unique)

Cette page vous permet de configurer le point d’ancrage et l’attribut de nom unique pour chaque type d’objet détecté. Vous pouvez sélectionner plusieurs attributs pour faire un point d’ancrage unique.

![schema3a](./media/active-directory-aadconnectsync-connector-genericsql/schema3a.png)

- Les attributs à valeurs multiples et booléens ne sont pas répertoriés.
- Le même attribut ne peut pas utiliser de nom de domaine et d’ancrage, à moins que l’option **Le nom unique est le point d’ancrage** soit sélectionnée dans la page de Connexion.
- Si l’option **Le nom unique est le point d’ancrage** est sélectionnée dans la page Connectivité, cette page a besoin uniquement de l’attribut de nom de domaine. Cet attribut peut également être utilisé en tant qu’attribut d’ancrage. ![schema3b](./media/active-directory-aadconnectsync-connector-genericsql/schema3b.png)

### Schéma 4 (définir le type d’attribut, la référence et la direction)

Cette page permet de configurer le type d’attribut en tant qu’entier, référence, chaîne, binaire ou valeur booléenne et direction pour chaque attribut. Tous les attributs de la page **schéma 2** sont répertoriés, et notamment des attributs à valeurs multiples.

![schema4a](./media/active-directory-aadconnectsync-connector-genericsql/schema4a.png)

- **Type de données** : utilisé pour mapper le type d’attribut sur ceux connus par le moteur de synchronisation. La valeur par défaut consiste à utiliser le type détecté dans le schéma SQL, mais DateTime et Référence ne sont pas facilement détectables. Pour ces derniers, vous devez spécifier **DateTime** ou **Référence**.
- **Direction** : vous pouvez définir la direction d’attribut à importer, exporter ou ImportExport. ImportExport est la valeur par défaut. ![schema4b](./media/active-directory-aadconnectsync-connector-genericsql/schema4b.png)

Remarques :

- Si un type d’attribut n’est pas détectable par le connecteur, il utilise le type de données chaîne.
- Les **Tables imbriquées** peuvent être considérées comme des tables de base de données à une colonne. Oracle stocke les lignes d’une table imbriquée sans aucun ordre particulier. Toutefois, lorsque vous récupérez la table imbriquée dans une variable PL/SQL, les lignes sont associées à des indices consécutifs en commençant à 1. Cela vous donne un accès de type tableau à des lignes individuelles.
- Les éléments **VARRYS** ne sont pas pris en charge dans le connecteur.

### Schéma 5 (Définir la partition pour les attributs de référence)

Sur cette page, pour tous les attributs de référence, vous allez configurer la partition (par exemple, le type d’objet) à laquelle un attribut fait référence.

![schema5](./media/active-directory-aadconnectsync-connector-genericsql/schema5.png)

Si vous utilisez **Le nom unique est le point d’ancrage**, vous devez utiliser le même type d’objet que celui à partir duquel la référence est établie. Vous ne pouvez pas référencer un autre type d’objet.

### Paramètres globaux

La page Paramètres globaux sert à configurer l’importation différentielle, le format Date/heure et la méthode de mot de passe.

![globalparameters1](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters1.png)

Le connecteur SQL générique prend en charge les méthodes suivantes pour l’importation différentielle :

- **Déclencheur** : consultez [Génération de vues différentielles à l’aide de déclencheurs](https://technet.microsoft.com/library/cc708665.aspx).
- **Filigrane** : il s’agit d’une approche numérique qui peut être utilisée avec n’importe quelle base de données. La requête en filigrane est prérenseignée en fonction du fournisseur de base de données. Une colonne de filigrane doit être présente sur chaque tableau/vue affichée. Elle doit assurer les insertions et les mises à jour des tables, de même que les tableaux dépendants (à valeurs multiples ou enfants). Les horloges entre le service de synchronisation et le serveur de base de données doivent être synchronisées. Dans le cas contraire, certaines entrées de l’importation différentielle peuvent être omises. Limite :
    - La stratégie de filigrane ne prend pas en charge les objets supprimés.
- **Instantané** (fonctionne uniquement avec Microsoft SQL Server) [Génération de vues différentielles à l’aide d’instantanés](https://technet.microsoft.com/library/cc720640.aspx)
- **Suivi des modifications** (fonctionne uniquement avec Microsoft SQL Server) Limites [À propos du suivi des modifications](https://msdn.microsoft.com/library/bb933875.aspx) :
    - Le point d’ancrage et l’attribut de nom unique doivent faire partie de la clé primaire de l’objet sélectionné dans la table.
    - La requête SQL n’est pas prise en charge pendant l’importation et l’exportation avec suivi des modifications.

**Paramètres supplémentaires** : spécifiez le fuseau horaire du serveur de base de données qui indique où se situe le serveur de base de données. Cette valeur est utilisée pour prendre en charge les différents formats d’attributs de date et heure.

Le connecteur stocke toujours les valeurs de date et date/heure au format UTC. Pour être en mesure d’établir une conversion correcte, la date et l’heure du fuseau horaire du serveur de base de données et le format utilisé doivent être spécifiés. Le format doit être exprimé au format .Net.

Pendant l’exportation, chaque attribut data/heure doit être fourni au connecteur au format horaire UTC.

![globalparameters2](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters2.png)

**Configuration de mot de passe** : le connecteur fournit des fonctionnalités de synchronisation de mot de passe et prend en charge la définition et la modification du mot de passe.

Le connecteur propose deux méthodes pour prendre en charge la synchronisation de mot de passe :

- **Procédure stockée** : cette méthode requiert deux procédures stockées pour prendre en charge la définition et la modification d’un mot de passe. Saisissez tous les paramètres pour ajouter et modifier le fonctionnement des mots de passe dans SP mot de passe défini et Modification de mot de passe SP respectivement, conformément à l’exemple ci-dessous. ![globalparameters3](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters3.png)
- **Extension de mot de passe** : cette méthode nécessite des DLL d’extension de mot de passe (vous devez fournir le nom de la DLL d’extension qui implémente l’interface [IMAExtensible2Password](https://msdn.microsoft.com/library/microsoft.metadirectoryservices.imaextensible2password.aspx)). L’assemblage d’extension de mot de passe doit être placé dans le dossier d’extension, de sorte que le connecteur puisse charger la DLL lors de l’exécution. ![globalparameters4](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters4.png)

Vous devez également activer la gestion de mot de passe sur la page **Configurer une Extension**. ![globalparameters5](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters5.png)

### Configurer des partitions et des hiérarchies

Dans la page Partitions et hiérarchies, sélectionnez tous les types d’objets. Chaque type d’objet se trouve dans sa propre partition.

![partitions1](./media/active-directory-aadconnectsync-connector-genericsql/partitions1.png)

Vous pouvez également remplacer les valeurs définies sur la page **Connectivité** ou **Paramètres globaux**.

![partitions2](./media/active-directory-aadconnectsync-connector-genericsql/partitions2.png)

### Configuration de points d’ancrage

Cette page est en lecture seule, car le point d’ancrage a déjà été défini. L’attribut d’ancrage sélectionné est toujours ajouté avec le type d’objet pour s’assurer qu’il reste unique d’un type d’objet à l’autre.

![Points d’ancrage](./media/active-directory-aadconnectsync-connector-genericsql/anchors.png)

## Configurer le paramètre d’exécution d’étape

Ces étapes sont configurées sur les profils exécutés sur le connecteur. Ces configurations font un réel travail d’importation et d’exportation de données.

### Importation complète et différentielle

Le connecteur SQL générique prend en charge les importations complètes et différentielles par le biais des méthodes qui suivent :

- Table
- Affichage
- Procédure stockée
- Requête SQL

![runstep1](./media/active-directory-aadconnectsync-connector-genericsql/runstep1.png)

**Table/Vue**

Pour importer les attributs à valeurs multiples d’un objet, vous devez fournir le nom de table/vue séparé par des virgules dans la **Nom des tables/vues à valeurs multiples** et les conditions de rattachement respectives dans la **condition de jointure** avec la table parente.

Exemple : vous voulez importer l’objet Employé et tous ses attributs à plusieurs valeurs. Il existe deux tables nommées respectivement Employé (table principale) et Service (valeur multiple). Effectuez les actions suivantes :

- Saisissez **Employé** dans **Table/Vue/Nom unique**.
- Saisissez le service dans **Nom de table/vue à valeurs multiples**.
- Saisissez la condition de jointure entre Employé et Service dans **Condition de jointure**, par exemple, `Employee.DEPTID=Department.DepartmentID`. ![runstep2](./media/active-directory-aadconnectsync-connector-genericsql/runstep2.png)

**Procédures stockées**

![runstep3](./media/active-directory-aadconnectsync-connector-genericsql/runstep3.png)

- Si vous avez beaucoup de données, il est conseillé de mettre en œuvre la pagination avec vos procédures stockées.
- Dans votre procédure stockée, pour prendre en charge la pagination, vous devez fournir un index de début et un index de fin. Voir : [Pagination efficace dans de grandes quantités de données](https://msdn.microsoft.com/library/bb445504.aspx).
- @StartIndex et @EndIndex seront remplacés au moment de l’exécution par la valeur de taille configurée sur la page **Étape de configuration**. Exemple : si le connecteur extrait la première page et si la taille de page est définie sur 500, dans une telle situation, @StartIndex a pour valeur 1 et @EndIndex est considéré comme 500 et cette valeur augmente à mesure que le connecteur extrait les pages suivantes et remplace la valeur @StartIndex et @EndIndex.
- Pour exécuter la procédure stockée paramétrable, fournissez les paramètres au format `[Name]:[Direction]:[Value]`. Saisissez chaque paramètre sur une ligne différente (Utilisez Ctrl + Entrée pour passer à la ligne).
- Le connecteur SQL générique prend également en charge l’opération d’importation à partir de l’environnement distribué, par exemple, les serveurs liés dans Microsoft SQL Server. Dans les cas où l’information doit être extraite d’une table sur un serveur lié, la table doit être fournie au format : `[ServerName].[Database].[Schema].[TableName]`
    - Dans les environnements distribués, le connecteur prend uniquement en charge le serveur lié de Microsoft.
- Le connecteur SQL générique prend en charge uniquement les objets dont la structure est similaire (les alias et le type de données) entre les informations d’étapes d’exécution et la détection de schéma. Si l’objet sélectionné dans le schéma et les informations fournies à l’étape d’exécution sont différents, le connecteur SQL est incapable de prendre en charge ce type de scénario.

**Requête SQL**

![runstep4](./media/active-directory-aadconnectsync-connector-genericsql/runstep4.png)

![runstep5](./media/active-directory-aadconnectsync-connector-genericsql/runstep5.png)

- Les requêtes définies à résultats multiples ne sont pas prises en charge.
- La requête SQL prend en charge la pagination et fournit un Index de début et un index de fin en tant que variable pour prendre en charge la pagination.

### Importation d’écart

![runstep6](./media/active-directory-aadconnectsync-connector-genericsql/runstep6.png)

La configuration de l’importation différentielle exige une configuration plus poussée, avec une autre méthode prise en charge comme l’importation complète.

- Si l’utilisateur choisit l’approche de déclencheur ou de capture instantanée pour assurer le suivi des modifications différentielles, l’utilisateur peut fournir à la table d’historique ou la base de données d’instantané dans la zone **Historique de table ou le nom de base de données d’instantanés**.
- L’utilisateur doit également fournir une condition de jointure entre la table d’historique et la table parent. Exemple : `Employee.ID=History.EmployeeID`
- Le suivi de transaction sur la table parente à partir de l’utilisateur de la table historique doit fournir le nom de la colonne qui contient les informations sur l’opération comme (Ajout/Mise à jour/Suppression).
- Si l’utilisateur choisit le filigrane pour suivre les modifications différentielles, l’utilisateur doit fournir le nom de la colonne qui contient les informations d’opération dans **Nom de colonne Filigrane**. Le connecteur peut donc prendre en compte cette colonne lors de l’importation différentielle.
- La colonne **Modifier l’attribut de type** est requise pour le type de modification. Cette colonne mappe une modification qui se produit dans la table principale ou une table à valeurs multiples sur un type de changement dans la vue delta. Cette colonne peut contenir le type de modification Modify\_Attribute pour une modification au niveau de l’attribut ou un type d’ajout, de modification, de suppression pour un type de modification au niveau de l’objet. S’il s’agit d’autre chose que de la valeur par défaut, l’ajout, la modification, la suppression d’un utilisateur peuvent définir les valeurs à l’aide de cette option.

### Exportation

![runstep7](./media/active-directory-aadconnectsync-connector-genericsql/runstep7.png)

Le connecteur SQL générique prend en charge l’exportation en utilisant quatre méthodes prises en charge telles que :

- Table
- Affichage
- Procédure stockée
- Requête SQL

**Table/Vue**

Si l’utilisateur choisit l’option Table/Vue, le connecteur génère les requêtes respectives et effectue l’exportation.

**Procédures stockées**

![runstep8](./media/active-directory-aadconnectsync-connector-genericsql/runstep8.png)

Si l’utilisateur choisit l’option de procédure stockée, l’exportation nécessite 3 procédures stockées différentes pour effectuer diverses opérations d’insertion/Mise à jour/Suppression.

- **Ajouter un nom SP** : cette procédure stockée s’exécute si un objet arrive au connecteur pour une insertion dans la table concernée.
- **Mettre à jour un nom SP** : cette procédure stockée s’exécute si un objet arrive au connecteur pour une mise à jour dans la table concernée.
- **Supprimer un nom SP** : cette procédure stockée s’exécute si un objet arrive au connecteur pour une suppression de la table concernée.
- Attribut sélectionné à partir du schéma utilisé comme valeur de paramètre à la procédure stockée. Exemple : EmployeeName :ENTRÉE : @EmployeeName (EmployeeName est sélectionné dans le schéma de connecteur et un connecteur remplace la valeur correspondante lors de l’exportation)
- L’exécution d’une procédure stockée paramétrable saisit tous les paramètres au format `[Name]:[Direction]:[Value]`. Saisissez chaque paramètre sur une ligne différente (Utilisez Ctrl + Entrée pour passer à la ligne).

**Requête SQL**

![runstep9](./media/active-directory-aadconnectsync-connector-genericsql/runstep9.png)

Si l’utilisateur choisit l’option de requête SQL, l’exportation nécessite 3 procédures stockées différentes pour effectuer diverses opérations d’insertion/Mise à jour/Suppression.

- **Requête Insert** : cette requête s’exécute si un objet arrive au connecteur pour une insertion dans la table concernée.
- **Requête de mise à jour** : cette requête s’exécute si un objet arrive au connecteur pour une mise à jour dans la table concernée.
- **Requête de suppression** : cette requête s’exécute si n’importe quel objet arrive au connecteur pour suppression dans la table concernée.
- Attribut sélectionné à partir du schéma utilisé comme valeur de paramètre à la requête. Exemple : `Insert into Employee (ID, Name) Values (@ID, @EmployeeName)`

## Résolution de problèmes

-	Pour plus d’informations sur la façon d’activer la journalisation pour résoudre les problèmes du connecteur, consultez [Comment activer le suivi ETW pour les connecteurs](http://go.microsoft.com/fwlink/?LinkId=335731).

<!---HONumber=AcomDC_0128_2016-->