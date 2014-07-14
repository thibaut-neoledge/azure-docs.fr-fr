
## <a name="what-is"> </a>Présentation des files d'attente de stockage

Les files d'attente de stockage Azure sont un service permettant de stocker un grand nombre de messages accessibles depuis n'importe où dans le monde via des appels authentifiés avec HTTP ou HTTPS. Un simple message de file d'attente peut avoir une taille de 64 Ko et une file d'attente peut contenir des millions de messages, jusqu'à la limite de capacité totale d'un compte de stockage. Pour les comptes de stockage créés après le 8 juin 2012, la capacité totale s'élève à 200 To ; pour les comptes de stockage créés avant cette date, la capacité totale s'élève à 100 To. Pour plus d'informations sur la capacité du compte de stockage, consultez la page [Objectifs de performance et évolutivité du stockage Azure][1].

Voici quelques utilisations courantes des files d'attente de stockage :

* <span>Création d'un journal des travaux en
  souffrance de travail à traiter de manière asynchrone</span>
* Transmission des messages d'un rôle Web Azure à un rôle de travail
  Azure

## <a name="concepts"> </a>Concepts

Le service de file d'attente contient les composants suivants :

![Queue1](./media/howto-queue-storage/queue1.png)

* **Format d'URL :** Les files d'attente sont adressables à l'aide du
  format d'URL suivant :   
   http://`<storage 
  account>`.queue.core.windows.net/`<queue>`

L'URL suivante traite une des files d'attente du diagramme :  
 http://myaccount.queue.core.windows.net/imagesToDownload

-**Compte de stockage :** tous les accès au stockage Azure sont effectués via un compte de stockage. Pour plus d'informations sur la capacité du compte de stockage, consultez la page [Objectifs de performance et évolutivité du stockage Azure][1].

* **File d'attente :** une file d'attente contient un ensemble de messages. Tous les messages doivent être dans une file d'attente.

* **Message :** un message, dans n'importe quel format, jusqu'à 64 Ko.



[1]: http://msdn.microsoft.com/en-us/library/dn249410.aspx