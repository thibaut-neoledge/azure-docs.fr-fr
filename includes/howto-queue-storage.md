## <a name="what-is"> </a>Présentation des files d’attente de stockage

Le stockage Azure dans des files d'attente est un service permettant de stocker un grand nombre de
messages accessibles depuis n'importe où dans le monde via
des appels authentifiés avec HTTP ou HTTPS. La taille d'un message de file d'attente peut atteindre
64 Ko et une file d'attente peut contenir des millions de messages, dans la
limite de la capacité totale du compte de stockage. Chaque compte de stockage peut contenir jusqu'à 200 To de données d'objets blob, de files d'attente et de tables. Pour plus d’informations sur la capacité du compte de stockage, consultez la page [Objectifs de performance et évolutivité du stockage Azure][].

Voici quelques utilisations courantes des files d’attente de stockage :

-   <span>Création d'un journal des travaux en souffrance de travail à traiter de manière asynchrone</span>
-   Transmission des messages d'un rôle web Azure à un
    rôle de travail Azure

## <a name="concepts"> </a>Concepts

Le service de file d'attente contient les composants suivants :

![Queue1][]

-   **Format d'URL :** Les files d'attente sont adressables à l'aide du format d'URL suivant :
    <http://>`<storage account>`.queue.core.windows.net/`<queue>`

L'URL suivante désigne l'une des files d'attente du diagramme :
 <http://myaccount.queue.core.windows.net/imagesToDownload>

-**Compte de stockage :** tous les accès à Azure Storage passent par un compte de stockage. Pour plus d’informations sur la capacité du compte de stockage, consultez la page [Objectifs de performance et évolutivité du stockage Azure][].

-   **File d'attente :** une file d'attente contient un ensemble de messages. Tous les messages doivent être dans une file d'attente.

-   **Message :** un message, dans n'importe quel format, jusqu'à 64 Ko.

  [Objectifs de performance et évolutivité du stockage Azure]: http://msdn.microsoft.com/en-us/library/dn249410.aspx
  [Queue1]: ./media/howto-queue-storage/queue1.png
