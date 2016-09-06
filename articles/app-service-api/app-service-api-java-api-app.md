<properties
	pageTitle="Créer et déployer une application API Java dans Azure App Service"
	description="Découvrez comment créer un package d’application API Java et le déployer sur Azure App Service."
	services="app-service\api"
	documentationCenter="java"
	authors="bradygaster"
	manager="mohisri"
	editor="tdykstra"/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="java"
	ms.topic="get-started-article"
	ms.date="08/31/2016"
	ms.author="rachelap"/>

# Créer et déployer une application API Java dans Azure App Service

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

Ce didacticiel explique comment créer une application Java et comment la déployer dans des applications API Azure App Service en utilisant [Git]. Les instructions de ce didacticiel s’appliquent à tous les systèmes d’exploitation pouvant exécuter Java. Le code de ce didacticiel a été rédigé avec [Maven]. [Jax-RS] est utilisé pour créer le service RESTful et il est généré à partir des spécifications de métadonnées [Swagger] à l’aide de [l’éditeur Swagger].

## Composants requis

1. Le [kit 8 de développeur Java] (ou version ultérieure)
1. [Maven] doit être installé sur votre ordinateur de développement
1. [Git] doit être installé sur votre ordinateur de développement
1. Un abonnement payant ou une [version d’évaluation gratuite] dans [Microsoft Azure]
1. Une application de test HTTP comme [Postman]

## Structure de l’API avec Swagger.IO

Avec l’éditeur en ligne swagger.io, vous pouvez accéder au code JSON Swagger ou YAML qui représente la structure de votre API. Une fois la surface d’exposition de l’API conçue, vous pouvez exporter le code pour différentes plateformes et infrastructures. Dans la section qui suit, vous allez modifier la structure du code pour y inclure des fonctionnalités factices.

Cette démonstration commence par le collage d’un corps JSON Swagger dans l’éditeur swagger.io, qui sera ensuite utilisé pour générer le code qui se sert de JAX-RS pour accéder à un point de terminaison API REST. Le code structuré sera ensuite modifié pour retourner des données fictives en simulant une API REST basée sur un mécanisme de conservation des données.

1. Copiez le code JSON Swagger ci-dessous dans le Presse-papiers :

        {
            "swagger": "2.0",
            "info": {
                "version": "v1",
                "title": "Contact List",
                "description": "A Contact list API based on Swagger and built using Java"
            },
            "host": "localhost",
            "schemes": [
                "http",
                "https"
            ],
            "basePath": "/api",
            "paths": {
                "/contacts": {
                    "get": {
                        "tags": [
                            "Contact"
                        ],
                        "operationId": "contacts_get",
                        "consumes": [],
                        "produces": [
                            "application/json",
                            "text/json"
                        ],
                        "responses": {
                            "200": {
                                "description": "OK",
                                "schema": {
                                    "type": "array",
                                    "items": {
                                        "$ref": "#/definitions/Contact"
                                    }
                                }
                            }
                        },
                        "deprecated": false
                    }
                },
                "/contacts/{id}": {
                    "get": {
                        "tags": [
                            "Contact"
                        ],
                        "operationId": "contacts_getById",
                        "consumes": [],
                        "produces": [
                            "application/json",
                            "text/json"
                        ],
                        "parameters": [
                            {
                                "name": "id",
                                "in": "path",
                                "required": true,
                                "type": "integer",
                                "format": "int32"
                            }
                        ],
                        "responses": {
                            "200": {
                                "description": "OK",
                                "schema": {
                                    "type": "array",
                                    "items": {
                                        "$ref": "#/definitions/Contact"
                                    }
                                }
                            }
                        },
                        "deprecated": false
                    }
                }
            },
            "definitions": {
                "Contact": {
                    "type": "object",
                    "properties": {
                        "Id": {
                            "format": "int32",
                            "type": "integer"
                        },
                        "Name": {
                            "type": "string"
                        },
                        "EmailAddress": {
                            "type": "string"
                        }
                    }
                }
            }
        }

1. Accédez à [l’éditeur Swagger en ligne]. Une fois arrivé à ce stade, cliquez sur l’élément de menu **Fichier -> Coller JSON**.

    ![Élément de menu Coller JSON][paste-json]

1. Collez l’élément dans l’API JSON Swagger de liste de contacts que vous avez copiée précédemment.

    ![Collage du code JSON dans Swagger][pasted-swagger]

1. Affichez les pages de documentation et le résumé des API figurant dans l’éditeur.

    ![Affichage des documents Swagger générés][view-swagger-generated-docs]

1. Sélectionnez l’option de menu **Générer serveur -> JAX-RS** pour structurer le code côté serveur que vous allez modifier par la suite pour y ajouter une implémentation factice.

    ![Générer l’élément de menu de code][generate-code-menu-item]

    Une fois le code généré, vous recevrez un fichier ZIP à télécharger. Ce fichier contient le code généré automatiquement par le générateur de code Swagger et tous les scripts de compilation associés. Décompressez la totalité de la bibliothèque dans un répertoire de votre station de travail de développement.

## Modifiez le code pour ajouter l’implémentation de l’API

Dans cette section, vous allez remplacer l’implémentation côté serveur du code généré par Swagger par votre code personnalisé. Le nouveau code retournera une liste des tableaux (ArrayList) des entités de contacts au client appelant.

1. Ouvrez le fichier de modèle *Contact.java*, situé dans le dossier *src/gen/java/io/swagger/model*, avec [Visual Studio Code] ou dans votre éditeur de texte préféré.

    ![Ouvrir le fichier de modèle de contact][open-contact-model-file]

1. Ajoutez le constructeur suivant à la classe **Contact**.

        public Contact(Integer id, String name, String email) 
        {
            this.id = id;
            this.name = name;
            this.emailAddress = email;
        }

1. Ouvrez le fichier d’implémentation de service *ContactsApiServiceImpl.java*, situé dans le dossier *src/main/java/io/swagger/api/impl*, dans [Visual Studio Code] ou dans votre éditeur de texte.

    ![Ouvrir le fichier de code du service de contact][open-contact-service-code-file]

1. Remplacez le code présent dans le fichier par ce nouveau code. Vous ajouterez ainsi une implémentation factice au code de service.

        package io.swagger.api.impl;

        import io.swagger.api.*;
        import io.swagger.model.*;
        import com.sun.jersey.multipart.FormDataParam;
        import io.swagger.model.Contact;
        import java.util.*;
        import io.swagger.api.NotFoundException;
        import java.io.InputStream;
        import com.sun.jersey.core.header.FormDataContentDisposition;
        import com.sun.jersey.multipart.FormDataParam;
        import javax.ws.rs.core.Response;
        import javax.ws.rs.core.SecurityContext;

        @javax.annotation.Generated(value = "class io.swagger.codegen.languages.JaxRSServerCodegen", date = "2015-11-24T21:54:11.648Z")
        public class ContactsApiServiceImpl extends ContactsApiService {
  
            private ArrayList<Contact> loadContacts()
            {
                ArrayList<Contact> list = new ArrayList<Contact>();
                list.add(new Contact(1, "Barney Poland", "barney@contoso.com"));
                list.add(new Contact(2, "Lacy Barrera", "lacy@contoso.com"));
                list.add(new Contact(3, "Lora Riggs", "lora@contoso.com"));
                return list;
            }
  
            @Override
            public Response contactsGet(SecurityContext securityContext)
            throws NotFoundException {
                ArrayList<Contact> list = loadContacts();
                return Response.ok().entity(list).build();
                }
  
            @Override
            public Response contactsGetById(Integer id, SecurityContext securityContext)
            throws NotFoundException {
                ArrayList<Contact> list = loadContacts();
                Contact ret = null;
            
                for(int i=0; i<list.size(); i++)
                {
                    if(list.get(i).getId() == id)
                        {
                            ret = list.get(i);
                        }
                }
                return Response.ok().entity(ret).build();
            }
        }

1. Ouvrez une invite de commandes et choisissez le dossier racine de votre application comme répertoire.

1. Exécutez la commande Maven suivante pour générer le code et exécutez-le localement à l’aide du serveur d’applications Jetty localement.

        mvn package jetty:run

1. La fenêtre de commande indique en principe que Jetty a démarré votre code sur le port 8080.

    ![Ouvrir le fichier de code du service de contact][run-jetty-war]

1. Utilisez [Postman] pour exécuter une requête à la méthode API « get all contacts » sur http://localhost:8080/api/contacts.

    ![Appeler l’API de contacts][calling-contacts-api]

1. Utilisez [Postman] pour exécuter une requête sur la méthode API « get specific contact » située sur http://localhost:8080/api/contacts/2.

    ![Appeler l’API de contacts][calling-specific-contact-api]

1. Enfin, générez le fichier WAR Java (ARchive Web) en exécutant la commande Maven suivante sur votre console.

        mvn package war:war

1. Une fois le fichier WAR généré, il est placé dans le dossier **cible**. Accédez au dossier **cible**, et renommez le fichier WAR en lui affectant le nom **ROOT.war**. (Veillez à respecter la casse).

         rename swagger-jaxrs-server-1.0.0.war ROOT.war

1. Enfin, exécutez les commandes suivantes à partir du dossier racine de votre application pour créer un dossier de **déploiement** à utiliser pour déployer le fichier WAR dans Azure.

         mkdir deploy
         mkdir deploy\webapps
         copy target\ROOT.war deploy\webapps
         cd deploy

## Publication du résultat dans Azure App Service

Dans cette section, vous allez apprendre à créer une nouvelle application API App à l’aide du portail Azure, à préparer cette dernière pour l’hébergement d’applications Java et à déployer le fichier WAR récemment créé dans Azure App Service pour exécuter votre nouvelle application API.

1. Créez une application API dans le [Portail Azure]. Pour cela, cliquez sur **Nouveau -> Web + Mobile -> Application API**, entrez les informations détaillées sur votre application, puis cliquez sur **Créer**.

    ![Créer une nouvelle API App][create-api-app]

1. Une fois votre application API créée, ouvrez le panneau **Paramètres** de votre application , puis cliquez sur l’élément de menu **Paramètres de l’application**. Sélectionnez les dernières versions de Java dans les options disponibles, sélectionnez la version la plus récente de Tomcat dans le menu **Conteneur web**, puis cliquez sur **Enregistrer**.

    ![Configurer Java dans le panneau application API][set-up-java]

1. Cliquez sur l’élément de menu de paramètre **Informations d’identification de déploiement**, puis fournissez le nom d’utilisateur et le mot de passe que vous souhaitez utiliser pour la publication de fichiers dans votre application API.

    ![Définir les informations d’identification de déploiement][deployment-credentials]

1. Cliquez sur l’élément de menu de paramètre **Source de déploiement**. À ce stade, cliquez sur le bouton **Choisir la source**, sélectionnez l’option **Référentiel Git local**, puis cliquez sur **OK**. Cette opération crée un référentiel Git fonctionnant sous Azure et associé à votre application API. À chaque fois que vous validez le code dans la branche *master* du référentiel Git, votre code est publié dans l’instance d’application API en cours d’exécution en direct.

    ![Configurer un nouveau référentiel Git local][select-git-repo]

1. Copiez la nouvelle URL de référentiel Git dans votre presse-papiers. Conservez-la, car dans un instant, vous devrez l’utiliser.

    ![Configurer un nouveau référentiel Git pour votre application][copy-git-repo-url]

1. GIT transfère le fichier WAR au référentiel en ligne. Pour ce faire, accédez au dossier **deploy** créé au préalable afin de pouvoir valider le code dans le référentiel en cours d’exécution de votre App Service. Une fois que vous vous trouvez dans la fenêtre de console et que vous avez accédé au dossier contenant le dossier webapps, émettez les commandes Git qui suivent pour lancer le processus et déclencher un déploiement.

         git init
         git add .
         git commit -m "initial commit"
         git remote add azure [YOUR GIT URL]
         git push azure master

    Une fois que vous avez émis la requête **Push**, vous devez fournir le mot de passe que vous avez créé pour les informations d’identification précédemment. Lorsque vous avez saisi vos informations d’identification, votre portail vous informe en principe que la mise à jour a été déployée.

1. Si vous utilisez une fois encore Postman pour atteindre l’application API qui vient d’être déployée dans Azure App Service, vous pourrez constater que le comportement est cohérent et que désormais, il retourne les coordonnées comme prévu et qu’il utilise des modifications simples du code Java de structure Swagger.io.

    ![Utilisation de l’API REST de contacts de Java en direct dans Azure][postman-calling-azure-contacts]

## Étapes suivantes

Dans cet article, vous avez pu commencer avec un fichier Swagger JSON et un code avec structure Java obtenu à partir de l’éditeur Swagger.io. À partir de là, quelques modifications simples et le processus de déploiement Git vous ont permis d’obtenir une application API fonctionnelle rédigée en Java. Le didacticiel suivant montre comment [consommer des applications API à partir de clients JavaScript à l’aide de CORS][App Service API CORS]. Les didacticiels ultérieurs montrent comment implémenter l’authentification et l’autorisation.

À partir de cet exemple, vous pouvez en apprendre davantage sur le [kit de développement logiciel de stockage pour Java] pour conserver les objets blob JSON. Vous pouvez également utiliser le [kit de développement logiciel Java Document DB] pour enregistrer vos données de contact pour la base de données de Document Azure.

Pour plus d’informations sur l’utilisation de Java dans Azure, consultez le [Centre de développement Java].

<!-- URL List -->

[App Service API CORS]: app-service-api-cors-consume-javascript.md
[Portail Azure]: https://portal.azure.com/
[kit de développement logiciel Java Document DB]: ../documentdb/documentdb-java-application.md
[version d’évaluation gratuite]: https://azure.microsoft.com/pricing/free-trial/
[Git]: http://www.git-scm.com/
[Centre de développement Java]: /develop/java/
[kit 8 de développeur Java]: http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
[Jax-RS]: https://jax-rs-spec.java.net/
[Maven]: https://maven.apache.org/
[Microsoft Azure]: https://azure.microsoft.com/
[l’éditeur Swagger en ligne]: http://editor.swagger.io/
[Postman]: https://www.getpostman.com/
[kit de développement logiciel de stockage pour Java]: ../storage/storage-java-how-to-use-blob-storage.md
[Swagger]: http://swagger.io/
[l’éditeur Swagger]: http://editor.swagger.io/
[Visual Studio Code]: https://code.visualstudio.com

<!-- IMG List -->

[paste-json]: ./media/app-service-api-java-api-app/paste-json.png
[pasted-swagger]: ./media/app-service-api-java-api-app/pasted-swagger.png
[view-swagger-generated-docs]: ./media/app-service-api-java-api-app/view-swagger-generated-docs.png
[generate-code-menu-item]: ./media/app-service-api-java-api-app/generate-code-menu-item.png
[open-contact-model-file]: ./media/app-service-api-java-api-app/open-contact-model-file.png
[open-contact-service-code-file]: ./media/app-service-api-java-api-app/open-contact-service-code-file.png
[run-jetty-war]: ./media/app-service-api-java-api-app/run-jetty-war.png
[calling-contacts-api]: ./media/app-service-api-java-api-app/calling-contacts-api.png
[calling-specific-contact-api]: ./media/app-service-api-java-api-app/calling-specific-contact-api.png
[create-api-app]: ./media/app-service-api-java-api-app/create-api-app.png
[set-up-java]: ./media/app-service-api-java-api-app/set-up-java.png
[deployment-credentials]: ./media/app-service-api-java-api-app/deployment-credentials.png
[select-git-repo]: ./media/app-service-api-java-api-app/select-git-repo.png
[copy-git-repo-url]: ./media/app-service-api-java-api-app/copy-git-repo-url.png
[postman-calling-azure-contacts]: ./media/app-service-api-java-api-app/postman-calling-azure-contacts.png

<!---HONumber=AcomDC_0831_2016-->