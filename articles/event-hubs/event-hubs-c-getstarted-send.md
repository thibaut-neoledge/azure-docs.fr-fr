---
title: "Envoyer des événements vers Azure Event Hubs avec C | Microsoft Docs"
description: "Envoyer des événements vers Azure Event Hubs avec C"
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: c
ms.devlang: csharp
ms.topic: article
ms.date: 05/03/2017
ms.author: jotaub;sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c4d5e161c9f7af33609be53e7b82f156bb0e33f
ms.openlocfilehash: e1aeb2708e829480b0e4a520f6f9ee08894bfaf9
ms.contentlocale: fr-fr
ms.lasthandoff: 05/04/2017

---

# <a name="send-events-to-azure-event-hubs-using-c"></a>Envoyer des événements vers Azure Event Hubs avec C

## <a name="introduction"></a>Introduction
Les hubs d’événements représentent un système d’ingestion à l’extensibilité élevée en mesure d’absorber des millions d’événements par seconde, ce qui permet à une application de traiter et d’analyser les quantités énormes de données produites par vos périphériques connectés et vos applications. Une fois collectés dans des hubs d’événements, vous pouvez transformer et stocker des données à l’aide de n’importe quel fournisseur d’analyses en temps réel ou d’un cluster de stockage.

Pour plus d’informations, consultez la page [Vue d’ensemble d’Event Hubs][Event Hubs overview].

Dans ce didacticiel, vous allez apprendre à envoyer des événements à un Event Hub à l’aide d’une application console en C. Pour recevoir des événements, cliquez sur le langage de réception approprié dans la table des matières de gauche.

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Un environnement de développement en C. Pour ce didacticiel, nous partirons du principe que la pile GCC est sur une machine virtuelle Linux Azure dotée du système d’exploitation Ubuntu 14.04.
* Microsoft Visual Studio ou Visual Studio Community Edition
* Un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="send-messages-to-event-hubs"></a>Envoi de messages vers Event Hubs
Dans cette section, nous allons écrire une application en C pour envoyer des événements à Event Hub. Nous allons utiliser la bibliothèque Proton AMQP du [projet Apache Qpid](http://qpid.apache.org/). Cette approche est similaire à l’utilisation des rubriques et des files d’attente Service Bus avec AMQP en partant du langage C comme indiqué [ici](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504). Pour plus d’informations, consultez la [documentation Qpid Proton](http://qpid.apache.org/proton/index.html).

1. Dans la [page Qpid AMQP Messenger](http://qpid.apache.org/components/messenger/index.html), cliquez sur le lien **Installation de Qpid Proton** et observez les instructions correspondant à votre environnement.
2. Pour compiler la bibliothèque Proton, installez les packages suivants :
   
    ```shell
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```
3. Téléchargez la [bibliothèque Qpid Proton](http://qpid.apache.org/proton/index.html) et effectuez son extraction, par exemple :
   
    ```shell
    wget http://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```
4. Créez un répertoire de build, compilez-le et installez-le :
   
    ```shell
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```
5. Dans le répertoire de travail, créez un fichier nommé **sender.c** dont le contenu est le suivant. N'oubliez pas de remplacer la valeur du nom de votre hub d'événements et du nom de votre espace de noms (ce dernier est généralement `{event hub name}-ns`). Vous devez également remplacer une version codée URL de la clé pour le **SendRule** précédemment créé. Vous pouvez la coder par URL [ici](http://www.w3schools.com/tags/ref_urlencode.asp).
   
    ```c
    #include "proton/message.h"
    #include "proton/messenger.h"
   
    #include <getopt.h>
    #include <proton/util.h>
    #include <sys/time.h>
    #include <stddef.h>
    #include <stdio.h>
    #include <string.h>
    #include <unistd.h>
    #include <stdlib.h>
   
    #define check(messenger)                                                     
      {                                                                          
        if(pn_messenger_errno(messenger))                                        
        {                                                                        
          printf("check\n");                                                     
          die(__FILE__, __LINE__, pn_error_text(pn_messenger_error(messenger))); 
        }                                                                        
      }  
   
    pn_timestamp_t time_now(void)
    {
      struct timeval now;
      if (gettimeofday(&now, NULL)) pn_fatal("gettimeofday failed\n");
      return ((pn_timestamp_t)now.tv_sec) * 1000 + (now.tv_usec / 1000);
    }  
   
    void die(const char *file, int line, const char *message)
    {
      printf("Dead\n");
      fprintf(stderr, "%s:%i: %s\n", file, line, message);
      exit(1);
    }
   
    int sendMessage(pn_messenger_t * messenger) {
        char * address = (char *) "amqps://SendRule:{Send Rule key}@{namespace name}.servicebus.windows.net/{event hub name}";
        char * msgtext = (char *) "Hello from C!";
   
        pn_message_t * message;
        pn_data_t * body;
        message = pn_message();
   
        pn_message_set_address(message, address);
        pn_message_set_content_type(message, (char*) "application/octect-stream");
        pn_message_set_inferred(message, true);
   
        body = pn_message_body(message);
        pn_data_put_binary(body, pn_bytes(strlen(msgtext), msgtext));
   
        pn_messenger_put(messenger, message);
        check(messenger);
        pn_messenger_send(messenger, 1);
        check(messenger);
   
        pn_message_free(message);
    }
   
    int main(int argc, char** argv) {
        printf("Press Ctrl-C to stop the sender process\n");
   
        pn_messenger_t *messenger = pn_messenger(NULL);
        pn_messenger_set_outgoing_window(messenger, 1);
        pn_messenger_start(messenger);
   
        while(true) {
            sendMessage(messenger);
            printf("Sent message\n");
            sleep(1);
        }
   
        // release messenger resources
        pn_messenger_stop(messenger);
        pn_messenger_free(messenger);
   
        return 0;
    }
    ```
6. Compilez le fichier (en supposant que **gcc**est défini) :
   
    ```
    gcc sender.c -o sender -lqpid-proton
    ```

    > [!NOTE]
    > Dans ce code, nous utilisons une fenêtre sortante de 1 pour forcer l’envoi des messages dès que possible. En général, votre application doit essayer d’envoyer les messages par lot pour augmenter le débit. Consultez la [page Qpid AMQP Messenger](http://qpid.apache.org/components/messenger/index.html) pour plus d’informations sur l’utilisation de la bibliothèque Qpid Proton dans l’ensemble des environnements et à partir des plateformes pour lesquelles des liaisons sont fournies (actuellement Perl, PHP, Python et Ruby).


## <a name="next-steps"></a>Étapes suivantes
Vous pouvez en apprendre plus sur Event Hubs en consultant les liens suivants :

* [Vue d’ensemble des hubs d’événements][Event Hubs overview]
* [Créer un concentrateur d’événements](event-hubs-create.md)
* [FAQ sur les hubs d'événements](event-hubs-faq.md)

<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png

<!-- Links -->
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[sample application that uses Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3

