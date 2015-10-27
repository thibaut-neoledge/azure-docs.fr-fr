<properties
	pageTitle="Actions permettant de remédier aux pertes de connexion temporaires| Microsoft Azure"
	description="Actions permettant d’éviter, de diagnostiquer et de corriger des erreurs de connexion et d’autres erreurs temporaires lors de l’interaction avec la base de données SQL Azure."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="10/19/2015"
	ms.author="genemi"/>


# Actions servant à corriger les erreurs de connexion et les erreurs temporaires dans la base de données SQL


Cette rubrique décrit comment empêcher, diagnostiquer et limiter les erreurs de connexion et les erreurs temporaires que votre programme client rencontre lorsqu’il interagit avec la base de données SQL Azure.


## Connexion : chaîne de connexion


La chaîne de connexion nécessaire à la connexion à la base de données SQL Azure est légèrement différente de celle qui sert à se connecter à Microsoft SQL Server. Il est possible de copier la chaîne de connexion de votre base de données depuis le [portail Azure en version préliminaire](http://portal.azure.com/).


[AZURE.INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]



#### 30 secondes de délai de connexion


Une connexion via Internet est moins fiable qu’une connexion via un réseau privé. Par conséquent, nous vous conseillons, pour votre chaîne de connexion de : - définir le paramètre de **délai de connexion** sur **30** secondes (au lieu de 15 secondes).


## Connexion : adresse IP


Vous devez configurer le serveur de base de données SQL pour accepter les communications à partir de l’adresse IP de l’ordinateur qui héberge votre programme client. Pour ce faire, vous devez modifier les paramètres du pare-feu via le [portail Azure en version préliminaire](http://portal.azure.com/).


Si vous oubliez de configurer l’adresse IP, votre programme échouera en envoyant un message d’erreur pratique indiquant l’adresse IP nécessaire.


[AZURE.INCLUDE [sql-database-include-ip-address-22-v12portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]


Pour plus d’informations, consultez [Procédure : configuration des paramètres du pare-feu sur SQL Database](sql-database-configure-firewall-settings.md).


## Connexion : ports


En règle générale, vous devez simplement vous assurer que le port 1433 est ouvert pour la communication sortante sur l’ordinateur qui héberge le programme client.


Par exemple, lorsque votre programme client est hébergé sur un ordinateur Windows, le pare-feu Windows sur l’ordinateur hôte vous permet d’ouvrir le port 1433 :


1. Ouvrir le panneau de configuration
2. &gt; Tous les éléments du Panneau de configuration
3. &gt; Pare-feu Windows
4. &gt; Paramètres avancés
5. &gt; Règles de trafic sortant
6. &gt; Actions
7. &gt; Nouvelle règle


Si votre programme client est hébergé sur une machine virtuelle Azure (VM), consultez la section :<br/>[Ports au-delà de 1433 pour ADO .NET 4.5 et SQL Database V12](sql-database-develop-direct-route-ports-adonet-v12.md).


Pour obtenir des informations générales sur configuration de ports et l’adresse IP, consultez : [pare-feu de base de données SQL Azure](sql-database-firewall-configure.md)


## Connexion : ADO.NET 4.5


Si votre programme utilise les classes ADO.NET comme **System.Data.SqlClient.SqlConnection** pour se connecter à la base de données SQL Azure, nous vous recommandons d’utiliser .NET Framework version 4.5 ou ultérieure.


ADO.NET 4.5 : ajoute la prise en charge du protocole TDS 7.4. Cela inclut des améliorations de connexion au-delà de ceux dans 4.0. Prend en charge le regroupement de connexions. Cela contribue à garantir que l’objet de connexion qu’il donne à votre programme fonctionne.


Lorsque vous utilisez un objet de connexion à partir d’un pool de connexions, nous conseillons de faire en sorte que votre programme interrompe temporairement la connexion lorsque vous ne l’utilisez pas immédiatement. La réouverture d’une connexion n’est pas aussi coûteuse que la création d’une nouvelle connexion.


Si vous utilisez ADO.NET 4.0 ou une version antérieure, nous vous recommandons d’effectuer une mise à niveau vers la dernière version d’ADO.NET. Depuis juillet 2015, vous pouvez [Télécharger 4.6 ADO.NET](http://blogs.msdn.com/b/dotnet/archive/2015/07/20/announcing-net-framework-4-6.aspx).


## Diagnostic : vérifier si les utilitaires peuvent se connecter


Si votre programme ne peut pas se connecter à la base de données SQL Azure, une option de diagnostic consiste à essayer de se connecter à un programme utilitaire. Dans l’idéal, l’utilitaire se connecte à l’aide de la bibliothèque que votre programme utilise.


Sur n’importe quel ordinateur Windows, vous pouvez essayer ces utilitaires :-SQL Server Management Studio (ssms.exe), qui se connecte à l’aide d’ADO.NET. -sqlcmd.exe, qui se connecte à l’aide de [ODBC](http://msdn.microsoft.com/library/jj730308.aspx).


Une fois connecté, faites un test avec une courte requête SQL SELECT.


## Diagnostic : vérifier les ports ouverts


Supposons que vous soupçonnez que les tentatives de connexion échouent en raison de problèmes de port. Depuis votre ordinateur, vous pouvez exécuter un utilitaire qui génère des rapports sur les configurations de port.


Sous Linux, les utilitaires suivants peuvent s’avérer utiles :- `netstat -nap` - `nmap -sS -O 127.0.0.1` -(remplacez la valeur de l’exemple par votre adresse IP).


Sous Windows, l’utilitaire [PortQry.exe](http://www.microsoft.com/download/details.aspx?id=17148) peut s’avérer utile. Voici une exécution de l’exemple qui demandé l’état d’un port sur un serveur de base de données SQL Azure, et qui a été exécuté sur un ordinateur portable :


```
[C:\Users\johndoe]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called:
 johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe]
>>
```


## Diagnostic : consignation des erreurs dans un journal


Un problème intermittent est parfois mieux diagnostiqué par le biais de la détection d’un modèle général se déroulant sur des jours ou des semaines.


Votre client peut aider à consigner toutes les erreurs qu’il rencontre un diagnostic. Vous pourrez mettre en corrélation les entrées de journal avec des informations sur les erreurs de base consignées en interne par la base de données SQL Azure elle-même.


Enterprise Library 6 (EntLib60) offre des classes gérées .NET afin de faciliter la journalisation :- [5 - Un jeu d’enfants : utilisation du bloc d’application de journalisation](http://msdn.microsoft.com/library/dn440731.aspx)


## Diagnostics : examinez les journaux d’erreur système


Voici certaines instructions Transact-SQL SELECT qui permettent d’interroger les journaux d’erreur et d’autres informations.


| Interrogation de journaux | Description |
| :-- | :-- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` | La vue [sys.event\_log](http://msdn.microsoft.com/library/dn270018.aspx) affiche des informations sur les événements individuels, notamment les problèmes de connectivité liés à la reconfiguration, la limitation et l’accumulation excessive des ressources.<br/><br/>Dans l’idéal, vous pouvez établir une corrélation entre les valeurs **start\_time** ou **end\_time** et les données relatives au moment où votre programme a rencontré des problèmes.<br/><br/>** CONSEIL : ** vous devez vous connecter à la base de données **master** pour exécuter cet exemple. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` | L’écran [sys.database\_connection\_stats](http://msdn.microsoft.com/library/dn269986.aspx) agrège le nombre des différents types d’événements, pour permettre des diagnostics supplémentaires.<br/><br/>** Conseil : ** vous devez vous connecter à la base de données **master** pour exécuter cet exemple. |


### Diagnostics : Rechercher les événements liés aux problèmes dans le journal de base de données SQL


Vous pouvez rechercher des entrées sur les problèmes survenus dans le journal de base de données SQL Azure. Essayez l’instruction Transact-SQL SELECT qui suit dans la base de données **master** :


```
SELECT
   object_name
  ,CAST(f.event_data as XML).value
      ('(/event/@timestamp)[1]', 'datetime2')                      AS [timestamp]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="error"]/value)[1]', 'int')             AS [error]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="state"]/value)[1]', 'int')             AS [state]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="is_success"]/value)[1]', 'bit')        AS [is_success]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="database_name"]/value)[1]', 'sysname') AS [database_name]
FROM
  sys.fn_xe_telemetry_blob_target_read_file('el', null, null, null) AS f
WHERE
  object_name != 'login_event'  -- Login events are numerous.
  and
  '2015-06-21' < CAST(f.event_data as XML).value
        ('(/event/@timestamp)[1]', 'datetime2')
ORDER BY
  [timestamp] DESC
;
```


#### quelques-unes d’entre elles ont renvoyé des lignes de sys.fn\_xe\_telemetry\_blob\_target\_read\_file


Vous trouverez plus loin une ligne renvoyée qui ressemble à ce qui suit . Les valeurs null indiquées ne sont en général pas nulles dans d’autres lignes.


```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```


## Erreurs temporaires


Une erreur temporaire est une erreur dont la cause sous-jacente se résout rapidement elle-même. Une des causes des défaillances temporaires est souvent que le système Azure rapidement réaffecte rapidement des ressources matérielles pour mieux équilibrer les différentes charges de travail. Pendant le temps de reconfiguration, les connexions à la base de données SQL Azure peuvent être perdues.


Si votre programme client utilise ADO.NET, votre programme est informé de l’erreur temporaire par l’envoi d’une donnée **SqlException**. La propriété **Nombre** peut être comparée à la liste des erreurs temporaires du haut de la rubrique : [Messages d’erreur pour les programmes clients SQL Database](sql-database-develop-error-messages).


### Connexion ou commande


Lorsqu’une erreur temporaire se produit pendant une tentative de connexion, la connexion doit être retentée après un délai de quelques secondes.


Lorsqu’une erreur temporaire se produit pendant une commande de requête SQL, cette dernière ne doit pas être retentée immédiatement. Au lieu de cela, après qu’un certain délai se soit écoulé, la connexion doit être établie. Ensuite, la commande peut être relancée.


## Logique de nouvelle tentative pour les erreurs temporaires.


Les programmes client qui rencontrent occasionnellement une erreur temporaire sont plus solides lorsqu’ils contiennent un programme de nouvelle tentative.


Lorsque votre programme communique avec la base de données SQL Azure via un logiciel intermédiaire tiers, renseignez-vous auprès du fournisseur pour savoir s’il contient une logique de nouvelle tentative pour les temporaires.


### Principes de nouvelle tentative


- Une tentative d’ouverture de connexion doit être retentée si l’erreur est une erreur temporaire.


- Une instruction SQL SELECT qui échoue à la suite d’une erreur temporaire ne doit pas être retentée directement.
 - Au lieu de cela, établir une nouvelle connexion et relancer l’instruction SELECT.


- Lorsqu’une instruction SQL UPDATE échoue à la suite d’une erreur temporaire, une nouvelle connexion doit être établie avant la nouvelle tentative de mise à jour.
 - La logique de nouvelle tentative doit s’assurer que la transaction de base de données est complètement terminée ou que la transaction a été annulée.


#### Autres considérations


- Un programme de commandes démarré automatiquement après les heures de travail et qui s’achève avant le matin peut être très patient entre deux tentatives.


- Un programme d’interface utilisateur doit prendre en compte la tendance qu’ont les hommes à l’abandon en cas d’attente trop longue.
 - Toutefois, la solution ne consiste pas à retenter l’opération à chaque seconde, car une telle stratégie pourrait submerger le système de requêtes.


### Augmentation de l’intervalle entre les tentatives


Votre programme doit toujours attendre au moins 6 à 10 secondes avant la première nouvelle tentative. Dans le cas contraire, le service cloud peut subitement être submergé de demandes qu’il n’est pas encore prêt à traiter.


Si plusieurs nouvelles tentatives sont nécessaires, l’intervalle doit augmenter avant chaque nouvelle tentative, jusqu’au maximum. Voici deux stratégies alternatives :


- Augmentation régulière de l’intervalle. Par exemple, vous pouvez ajouter encore 5 secondes à chaque intervalle successif.


- Augmentation exponentielle de l’intervalle. Vous pouvez par exemple multiplier chaque intervalle successif par 1,5.


Vous pouvez également définir le nombre maximal de tentatives avant l’arrêt automatique du programme.


### Exemples de code avec la logique de nouvelle tentative


Vous trouverez des exemples de code avec logique de nouvelle tentative dans divers langages de programmation ici :

- [Exemples de code de démarrage rapide](sql-database-develop-quick-start-client-code-samples.md) 


## Tester votre logique de nouvelle tentative


Pour tester la logique de nouvelle tentative, vous devez simuler ou provoquer une erreur pouvant être corrigée alors que votre programme est en cours d’exécution.


### Test par le biais de la déconnexion du réseau


Pour tester votre logique de nouvelle tentative, vous pouvez déconnecter votre ordinateur client du réseau pendant l’exécution du programme. L’erreur sera la suivante : **SqlException.Number** = 11001 - Message : « hôte inconnu »


Dans le cadre de la première nouvelle tentative, votre programme peut corriger les fautes d’orthographe et tenter de se connecter.


Pour concrétiser cela, vous pouvez débrancher votre ordinateur du réseau avant lancer votre programme. Votre programme reconnaît alors un paramètre d’exécution qui fait en sorte que le programme : 1. Ajoute temporairement 11001 à sa liste d’erreurs à considérer comme provisoire. 2. Tente sa première connexion comme d’habitude. 3. Une fois l’erreur détectée, supprime 11001 de sa liste. 4. Affiche un message indiquant à l’utilisateur de brancher l’ordinateur dans le réseau. -Interrompt la suite de l’exécution en utilisant soit la méthode **Console.ReadLine** ou une boîte de dialogue avec un bouton OK. L’utilisateur appuie sur la touche Entrée après la connexion de l’ordinateur au réseau. 5. Essaie de nouveau de se connecter, et attend la réussite.


### Teste en fournissant un nom de base de données mal orthographié au moment de la connexion


Votre programme peut délibérément mal orthographier le nom d’utilisateur avant la première tentative de connexion. L’erreur renvoyée sera :- **SqlException.Number** = 18456 - Message : « Échec de la connexion pour l’utilisateur ’WRONG\_MyUserName’.»


Dans le cadre de la première nouvelle tentative, votre programme peut corriger les fautes d’orthographe et tenter de se connecter.


Pour mettre cela en pratique, votre programme peut reconnaître un paramètre d’exécution, qui fait en sorte que le programme 1. Ajoute temporairement 18456 à sa liste d’erreurs à prendre en compte comme provisoire. 2. Ajoute volontairement ’WRONG\_’ au nom d’utilisateur. 3. Une fois l’erreur détectée, supprime 18456 de la liste. 4. Supprime ’WRONG\_’ du nom d’utilisateur. 5. Essaie de nouveau de se connecter, et attend la réussite.


## Enterprise Library 6


Enterprise Library 6 (EntLib60) est une infrastructure de classes .NET qui vous permet d’implémenter des clients de cloud fiables, et notamment le service Azure SQL Database. Vous pouvez rechercher des rubriques dédiées à chaque zone dans laquelle EntLib60 peut aider en visitant dans un premier temps :- [Enterprise Library 6 – avril 2013](http://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx)


La logique de nouvelle tentative pour la gestion des erreurs temporaires est un domaine dans lequel EntLib60 peut s’avérer utile :- [4 - la persévérance, le secret de toutes les victoires : utilisation du bloc d’application de gestion des erreurs temporaires](http://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx)


Un court exemple de code C# qui utilise EntLib60 dans sa logique de nouvelle tentative est disponible à l’adresse :- [Exemple de code : Logique de nouvelle tentative Enterprise Library 6 en C# pour la connexion à la base de données SQL](sql-database-develop-entlib-csharp-retry-windows.md)


### Classes EntLib60 pour les défaillances temporaires et la nouvelle tentative


Les classes EntLib60 suivantes sont particulièrement utiles pour la logique de nouvelle tentative. Toutes se trouvent directement, ou après l’espace de noms **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling** :

*Dans l’espace de noms **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling** :*

- Classe **RetryPolicy**
 - Méthode **ExecuteAction**


- Classe **ExponentialBackoff**


- Classe **SqlDatabaseTransientErrorDetectionStrategy**.


- Classe **ReliableSqlConnection**
 - Méthode **ExecuteCommand**


Dans l’espace de noms **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport** :

- Classe **AlwaysTransientErrorDetectionStrategy**

- Classe **NeverTransientErrorDetectionStrategy**


Voici des liens vers des informations sur EntLib60 :

- [Livre électronique gratuit : Guide du développeur de Microsoft Enterprise Library, deuxième édition](http://www.microsoft.com/download/details.aspx?id=41145) gratuit.

- Meilleures pratiques : [Réessayer conseils généraux](best-practices-retry-general.md) possède une excellente présentation approfondie des logique de nouvelle tentative.

- Téléchargement NuGet de [Bibliothèque d’entreprise - Bloc applicatif de gestion des erreurs 6.0 temporaires de Microsoft ](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/)


### EntLib60 : le bloc de journalisation


- Le bloc de journalisation est une solution très flexible et configurable qui vous permet de :
 - Créer et stocker des messages du journal dans de nombreux emplacements.
 - Classer et filtrer les messages.
 - Recueillir des informations contextuelles utiles pour le débogage et le suivi, ainsi que pour les exigences d’audit et de journalisation en général.


- Le bloc de journalisation extrait les fonctionnalités issues de la destination de journalisation de façon que le code d’application soit cohérent, quels que soient l’emplacement et le type du magasin de journalisation cible.


Pour plus de détails, consultez : [5 - Un jeu d’enfants : utilisation du bloc d’application de journalisation](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx)


### Code source de la méthode EntLib60 IsTransient


Ensuite, à partir de la **SqlDatabaseTransientErrorDetectionStrategy** de classe, le code source C# pour le **IsTransient**. Le code source précise quelles sont les erreurs sont considérées comme temporaires et qui peuvent faire l’objet de nouvelles tentatives.

De nombreuses lignes de **//commentaires** ont été supprimées dans cet exemplaire pour mettre en évidence la lisibilité.


```
public bool IsTransient(Exception ex)
{
  if (ex != null)
  {
    SqlException sqlException;
    if ((sqlException = ex as SqlException) != null)
    {
      // Enumerate through all errors found in the exception.
      foreach (SqlError err in sqlException.Errors)
      {
        switch (err.Number)
        {
            // SQL Error Code: 40501
            // The service is currently busy. Retry the request after 10 seconds.
            // Code: (reason code to be decoded).
          case ThrottlingCondition.ThrottlingErrorNumber:
            // Decode the reason code from the error message to
            // determine the grounds for throttling.
            var condition = ThrottlingCondition.FromError(err);

            // Attach the decoded values as additional attributes to
            // the original SQL exception.
            sqlException.Data[condition.ThrottlingMode.GetType().Name] =
              condition.ThrottlingMode.ToString();
            sqlException.Data[condition.GetType().Name] = condition;

            return true;

          case 10928:
          case 10929:
          case 10053:
          case 10054:
          case 10060:
          case 40197:
          case 40540:
          case 40613:
          case 40143:
          case 233:
          case 64:
            // DBNETLIB Error Code: 20
            // The instance of SQL Server you attempted to connect to
            // does not support encryption.
          case (int)ProcessNetLibErrorCode.EncryptionNotSupported:
            return true;
        }
      }
    }
    else if (ex is TimeoutException)
    {
      return true;
    }
    else
    {
      EntityException entityException;
      if ((entityException = ex as EntityException) != null)
      {
        return this.IsTransient(entityException.InnerException);
      }
    }
  }

  return false;
}
```


Il est inutile de télécharger le code source d’EntLib60. Mais si vous souhaitez télécharger le code source, vous pouvez consulter la rubrique suivante, puis cliquer sur le bouton [Télécharger le code](http://go.microsoft.com/fwlink/p/?LinkID=290898) :- [Enterprise Library 6 – avril 2013](http://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx)


## Plus d’informations


- [Regroupement de connexions SQL Server (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx)


- [*Nouvelle tentative* est une bibliothèque de nouvelle tentative sous licence Apache 2.0 à usage général écrite en langage **Python**, pour simplifier la tâche consistant à ajouter des comportements de nouvelle tentative dans toutes les situations.](https://pypi.python.org/pypi/retrying)

<!---HONumber=Oct15_HO4-->