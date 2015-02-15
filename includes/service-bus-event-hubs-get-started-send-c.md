## Envoi de messages vers les concentrateurs d'événements
Dans cette section, nous allons écrire une application en C pour envoyer des événements à votre concentrateur d'événements. Nous allons utiliser la bibliothèque Proton AMQP du projet [Apache Qpid](http://qpid.apache.org/). Cette approche est similaire à l'utilisation des files d'attente et des rubriques Service Bus avec AMQP en partant du langage C comme indiqué [ici](https://code.msdn.microsoft.com/windowsazure/Using-Apache-Qpid-Proton-C-afd76504). Pour plus d'informations, consultez la [documentation Qpid Proton](http://qpid.apache.org/proton/index.html).

1. Dans la [page Qpid AMQP Messenger](http://qpid.apache.org/components/messenger/index.html), cliquez sur le lien **Installation de Qpid Proton** et observez les instructions correspondant à votre environnement. Nous partirons du principe qu'il s'agit d'un environnement Linux, par exemple une [machine virtuelle Azure Linux](http://azure.microsoft.com/fr-fr/documentation/articles/virtual-machines-linux-tutorial/) dotée du système d'exploitation Ubuntu 14.04.

2. Pour compiler la bibliothèque Proton, installez les packages suivants :

		sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev

3. Téléchargez la [bibliothèque Qpid Proton](http://qpid.apache.org/proton/index.html) et effectuez son extraction, par exemple :
		
		wget http://apache.fastbull.org/qpid/proton/0.7/qpid-proton-0.7.tar.gz
		tar xvfz qpid-proton-0.6.tar.gz

4. Créez un répertoire de build, compilez-le et installez-le :

		mkdir build
		cd build
		cmake -DCMAKE_INSTALL_PREFIX=/usr ..
		sudo make install

5. Dans le répertoire de travail, créez un fichier nommé **sender.c** dont le contenu est le suivant. N'oubliez pas de remplacer la valeur de votre nom de concentrateur d'événements et le nom de l'espace de noms (ce dernier est généralement `{event hub name}-ns`). Vous devez également remplacer une version codée URL de la clé pour le **SendRule** précédemment créé. Vous pouvez la coder par URL [ici](http://www.w3schools.com/tags/ref_urlencode.asp).

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
		
		#define check(messenger)                                                     \
		  {                                                                          \
		    if(pn_messenger_errno(messenger))                                        \
		    {                                                                        \
		      printf("check\n");													 \
		      die(__FILE__, __LINE__, pn_error_text(pn_messenger_error(messenger))); \
		    }                                                                        \
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

6. Compilez le fichier (en supposant que **gcc** est défini) :

		gcc sender.c -o sender -lqpid-proton

> [AZURE.NOTE] Dans le code ci-dessus, nous utilisons une fenêtre sortante de 1 pour forcer l'envoi des messages dès que possible. En général, votre application doit essayer d'envoyer les messages par lot pour augmenter le débit. Consultez la [page Qpid AMQP Messenger](http://qpid.apache.org/components/messenger/index.html) pour plus d'informations sur l'utilisation de la bibliothèque Qpid Proton dans l'ensemble des environnements et à partir des plateformes pour lesquelles des liaisons sont fournies (actuellement Perl, PHP, Python et Ruby).


<!--HONumber=42-->
