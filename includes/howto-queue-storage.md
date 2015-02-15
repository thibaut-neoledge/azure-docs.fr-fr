## <a name="what-is"> </a>Présentation des files d'attente de stockage

Les files d'attente de stockage Azure sont un service permettant de stocker un grand nombre de messages accessibles depuis n'importe où dans le monde via des appels authentifiés avec HTTP ou HTTPS. Un simple message de file d'attente peut avoir une taille de 64 Ko et une file d'attente peut contenir des millions de messages, jusqu'à la limite de capacité totale d'un compte de stockage. Chaque compte de stockage peut contenir jusqu'à 200 To de données d'objets blob, de files d'attente et de tables. Consultez la page [Objectifs de performance et d'extensibilité d'Azure Storage](http://msdn.microsoft.com/fr-fr/library/dn249410.aspx) pour en savoir plus sur la capacité d'un compte de stockage.

Voici quelques utilisations courantes des files d'attente de stockage :

-   <span>Création d'un journal des travaux en souffrance de travail à traiter de manière asynchrone</span>
-   Transmission des messages d'un rôle Web Azure à un rôle de travail
    Azure

## <a name="concepts"> </a>Concepts

Le service de file d'attente contient les composants suivants :

![File d'attente 1](./media/howto-queue-storage/queue1.png)


- **Format d'URL :** les files d'attente sont adressables à l'aide du format d'URL suivant :   
	http://`<storage account>`.queue.core.windows.net/`<queue>` 
      
L'URL suivante désigne l'une des files d'attente du diagramme :  
	http://myaccount.queue.core.windows.net/imagesToDownload 

-**Compte de stockage :** tout accès au stockage Azure s'effectue via un compte de stockage. Consultez la page [Objectifs de performance et d'extensibilité d'Azure Storage](http://msdn.microsoft.com/fr-fr/library/dn249410.aspx) pour en savoir plus sur la capacité d'un compte de stockage.

- **File d'attente :** une file d'attente contient un ensemble de messages. Tous les messages doivent être dans une file d'attente.

- **Message :** message de tout format, d'une taille maximale de 64 Ko.



<!--HONumber=42-->
