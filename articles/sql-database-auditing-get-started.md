<properties title="Prise en main de l'audit de base de donn&eacute;es SQL" pageTitle="Prise en main de l'audit de base de donn&eacute;es SQL | Azure" description="Prise en main de l'audit de base de donn&eacute;es SQL" metaKeywords="" services="sql-database" solutions="data-management" documentationCenter="" authors="jeffreyg" videoId="" scriptId="" manager="jeffreyg" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jeffreyg" />

# Prise en main de l'audit de base de données SQL

L'audit de base de données SQL Azure effectue le suivi des événements de base de données et consigne les événements audités dans un journal dans votre compte Azure Storage. L'audit est disponible en version préliminaire pour les services De base, Standard et Premium. Pour l'utiliser, [inscrivez-vous pour la version préliminaire][inscrivez-vous pour la version préliminaire].

L'audit peut vous aider à respecter une conformité réglementaire, à comprendre l'activité de la base de données et à découvrir des discordances et anomalies susceptibles d'indiquer des problèmes pour l'entreprise ou des violations de la sécurité.

Les outils d'audit permettent et facilitent le respect des normes liées à la conformité, mais ne garantissent pas cette dernière. Pour plus d'informations sur les programmes Azure prenant en charge la conformité aux normes, consultez le [Centre de gestion de la confidentialité Azure][Centre de gestion de la confidentialité Azure].

-   [Principes fondamentaux de l'audit de base de données SQL Azure][Principes fondamentaux de l'audit de base de données SQL Azure]
-   [Configuration de l'audit de votre base de données][Configuration de l'audit de votre base de données]
-   [Analyse des journaux et des rapports d'audit][Analyse des journaux et des rapports d'audit]

## <span id="subheading-1"></span>Principes fondamentaux de l'audit de base de données SQL Azure</a>

Vous configurez l'audit dans la version préliminaire du portail Azure, mais peu importe que vous ayez créé la base de données à partir du portail Azure ou de sa version préliminaire. Éléments rendus possibles par l'audit de bases de données :

-   **La rétention** d'une piste d'audit d'événements sélectionnés. Définissez les catégories d'actions et d'événements de base de données à enregistrer.
-   **La génération de rapports** sur les activités de la base de données. Utilisez des rapports préconfigurés et un tableau de bord pour une prise en main rapide de la génération de rapports d'activités et d'événements.
-   **L'analyse** des rapports. Repérez les événements suspects, les activités inhabituelles et les tendances.

Avec l'audit, vous pouvez suivre les activités et événements suivants :

-   **Accès aux données**
-   **Modifications de schéma (DDL)**
-   **Modifications de données (DML)**
-   **Comptes, rôles et autorisations**
-   **Exceptions de sécurité**

Pour d'autres informations sur les activités et événements enregistrés, consultez la page [Référence sur le format du journal d'audit (téléchargement d'un fichier doc)][Référence sur le format du journal d'audit (téléchargement d'un fichier doc)].

Vous devez également choisir le compte de stockage dans lequel enregistrer les journaux d'audit.

### Chaîne de connexion sécurisée

Lors de la configuration de l'audit, Azure vous fournit une chaîne de connexion sécurisée pour la base de données. Seules les applications clientes utilisant cette chaîne de connexion verront leurs événements et activités enregistrés. Vous devez donc mettre à jour les applications clientes existantes de façon à utiliser le nouveau format de chaîne.

Format de chaîne de connexion classique : \<*nom du serveur*\>.database.windows.net

Chaîne de connexion sécurisée : \<*nom du serveur*\>.database.**secure**.windows.net

## <span id="subheading-2"></span></a>Configuration de l'audit de votre base de données

1.  [Inscrivez-vous pour la version préliminaire de l'audit][inscrivez-vous pour la version préliminaire].
2.  Lancez la [version préliminaire du portail Azure][version préliminaire du portail Azure] à l'adresse https://portal.azure.com.
3.  Cliquez sur la base de données sur laquelle vous voulez faire un audit, puis cliquez sur **Version préliminaire de l'audit** pour activer la version préliminaire de l'audit et ouvrir le volet de configuration de l'audit.

    ![][0]

4.  Dans le volet de configuration de l'audit, sélectionnez le compte de stockage Azure dans lequel les journaux seront enregistrés. **Conseil :** utilisez le même compte de stockage pour toutes les bases de données auditées afin de profiter au mieux des modèles de rapport préconfigurés.

    ![][1]

5.  Sous **Options d'audit**, cliquez sur **Tous** pour enregistrer tous les événements ou choisissez des types d'événement spécifiques.

    ![][2]

6.  Cochez **Enregistrer comme configuration par défaut** si vous voulez appliquer ces paramètres à toutes les futures bases de données du serveur et à toutes celles dont l'audit n'a pas encore été configuré. Il est possible de remplacer ces paramètres ultérieurement pour chaque base de données en suivant cette même procédure.

    ![][3]

7.  Cliquez sur **Afficher les chaînes de connexion de la base de données**, puis copiez ou notez la chaîne de connexion sécurisée de votre application. Utilisez-la pour toute application cliente sur laquelle effectuer un audit.

    ![][4]

8.  Cliquez sur **OK**.

## <span id="subheading-3"></span>Analyse des journaux et des rapports d'audit</a>

Les journaux d'audit sont agrégés dans une table de stockage Azure unique **AuditLogs** au sein du compte de stockage Azure choisi lors de la configuration. Vous pouvez afficher les fichiers journaux à l'aide d'un outil tel que [l'Explorateur de stockage Azure][l'Explorateur de stockage Azure].

Un modèle de rapport de tableau de bord préconfiguré est disponible sous forme de [feuille de calcul Excel téléchargeable][feuille de calcul Excel téléchargeable] afin de vous aider à analyser rapidement les données de journal. Pour utiliser le modèle sur vos journaux d'audit, il vous faut Excel 2013 ou une version ultérieure et Power Query, téléchargeable [ici][ici].

Le modèle contient des données d'exemple fictives. Vous pouvez configurer Power Query de façon à ce qu'il importe votre journal d'audit directement à partir de votre compte de stockage Azure.

Pour des instructions plus détaillées sur la façon de travailler avec le modèle de rapport, consultez la page [Procédure (téléchargement doc)][Procédure (téléchargement doc)].

![][5]

<!--Anchors--> <!--Image references--> <!--Link references-->

  [inscrivez-vous pour la version préliminaire]: http://go.microsoft.com/fwlink/?LinkId=404163
  [Centre de gestion de la confidentialité Azure]: http://azure.microsoft.com/fr-fr/support/trust-center/compliance/
  [Principes fondamentaux de l'audit de base de données SQL Azure]: #subheading-1
  [Configuration de l'audit de votre base de données]: #subheading-2
  [Analyse des journaux et des rapports d'audit]: #subheading-3
  [Référence sur le format du journal d'audit (téléchargement d'un fichier doc)]: http://go.microsoft.com/fwlink/?LinkId=506733
  [version préliminaire du portail Azure]: https://portal.azure.com
  [0]: ./media/sql-database-auditing-get-started/sql-database-get-started-auditingpreview.png
  [1]: ./media/sql-database-auditing-get-started/sql-database-get-started-storageaccount.png
  [2]: ./media/sql-database-auditing-get-started/sql-database-auditing-eventtype.png
  [3]: ./media/sql-database-auditing-get-started/sql-database-get-started-saveconfigasdefault.png
  [4]: ./media/sql-database-auditing-get-started/sql-database-get-started-connectionstring.png
  [l'Explorateur de stockage Azure]: http://azurestorageexplorer.codeplex.com/
  [feuille de calcul Excel téléchargeable]: http://go.microsoft.com/fwlink/?LinkId=403540
  [ici]: http://www.microsoft.com/fr-fr/download/details.aspx?id=39379
  [Procédure (téléchargement doc)]: http://go.microsoft.com/fwlink/?LinkId=506731
  [5]: ./media/sql-database-auditing-get-started/sql-database-auditing-dashboard.png
