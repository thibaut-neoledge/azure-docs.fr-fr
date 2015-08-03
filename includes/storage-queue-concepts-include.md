## Présentation du stockage de files d'attente

Les files d’attente de stockage Azure sont un service permettant de stocker un grand nombre de messages accessibles depuis n’importe où dans le monde via des appels authentifiés avec HTTP ou HTTPS. Un simple message de file d’attente peut avoir une taille de 64 Ko et une file d’attente peut contenir des millions de messages, jusqu’à la limite de capacité totale d’un compte de stockage. Chaque compte de stockage peut contenir jusqu’à 500 To de données d’objets blob, de files d’attente et de tables. Pour plus d’informations sur la capacité du compte de stockage, consultez la page [Objectifs de performance et évolutivité du stockage Azure](http://msdn.microsoft.com/library/azure/dn249410.aspx).

Voici quelques utilisations courantes des files d’attente de stockage :

-   <span>Création d'un journal des travaux en souffrance de travail à traiter de manière asynchrone</span>
-   Transmission des messages d’un rôle Web Azure à un rôle de travail Azure

## Concepts du service de File d’attente

Le service de file d’attente contient les composants suivants :

![File d'attente 1](./media/storage-queue-concepts-include/queue1.png)


- **Format d’URL :** les files d’attente sont adressables à l’aide du format d’URL suivant : http://`<storage account>`.queue.core.windows.net/`<queue>` 
      
L'URL suivante désigne l'une des files d'attente du schéma : http://myaccount.queue.core.windows.net/imagesToDownload

-**Compte de stockage :** tous les accès au stockage Azure sont effectués via un compte de stockage. Pour plus d’informations sur la capacité du compte de stockage, consultez la page [Objectifs de performance et évolutivité du stockage Azure](../articles/storage/storage-scalability-targets.md).

- **File d’attente :** une file d’attente contient un ensemble de messages. Tous les messages doivent être dans une file d’attente.

- **Message :** message de tout format, d’une taille maximale de 64 Ko.

<!---HONumber=July15_HO4-->