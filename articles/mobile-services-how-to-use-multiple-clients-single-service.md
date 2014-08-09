<properties linkid="" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Considerations for supporting multiple clients from a single mobile service" authors="krisragh" solutions="" manager="" editor="" />

Considérations sur la prise en charge de plusieurs clients à partir d'un seul service mobile
============================================================================================

L'un des principaux avantages associés à l'utilisation d'Azure Mobile Services dans le développement des applications mobiles est la possibilité d'utiliser un seul service principal qui prend en charge une application sur plusieurs plateformes clientes. Mobile Services fournit des bibliothèques clientes natives pour les principales plateformes d'appareil. Pour plus d'informations, consultez la page [Didacticiels et ressources](/en-us/develop/mobile/resources/).

Bien que Mobile Services facilite la migration de votre application native à travers plusieurs plateformes clientes avec un seul service principal, vous devez tenir compte de certaines considérations. Cette rubrique explique comment procéder pour que les notifications Push fonctionnent sur l'ensemble de vos plateformes clientes. Elle décrit également la procédure à suivre pour contourner un problème d'utilisation d'une authentification unique pilotée par le client avec un compte Microsoft dans des applications Windows Store et Windows Phone. Enfin, elle aborde quelques unes des meilleures pratiques pour réutiliser le code dans des projets Visual Studio.

Notifications Push
------------------

Cette section présente deux approches pour envoyer des notifications Push de votre service mobile à des applications clientes sur plusieurs plateformes.

### Utilisation de Notification Hubs

Azure Notification Hubs est un service Azure qui offre une solution évolutive pour envoyer des notifications Push de votre service mobile (ou de n'importe quel serveur principal) à des applications clientes sur les principales plateformes d'appareil. Pour plus d'informations, consultez la page [Azure Notification Hubs](/en-us/develop/net/how-to-guides/service-bus-notification-hubs/).

Notification Hubs fournit une infrastructure cohérente et unifiée pour créer et gérer les inscriptions des appareils et pour envoyer des notifications Push à des appareils fonctionnant sur les principales plateformes d'appareil. Pour plus d'informations, consultez la page [Prise en main de Notification Hubs](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet/). Notification Hubs prend en charge les inscriptions de modèles propres aux différentes plateformes, ce qui vous permet d'utiliser un seul appel d'API pour envoyer une notification à votre application exécutée sur n'importe quelle plateforme enregistrée. Pour plus d'informations, consultez la page [Envoi de notifications interplateforme aux utilisateurs](/en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/).

Notification Hubs est la solution recommandée pour envoyer des notifications de votre service mobile à plusieurs plateformes clientes.

### Utilisation d'une table d'inscription et d'un identificateur de plateforme communs

Si vous choisissez d'utiliser Notification Hubs, vous pouvez quand même prendre en charge l'envoi de notifications Push à plusieurs clients depuis votre service mobile en définissant un mécanisme d'inscription d'appareil commun. Servez-vous d'une seule table pour stocker les informations propres aux appareils utilisées par les services de notifications Push de la plateforme prise en charge. Les didacticiels **Prise en main des notifications Push** ([Windows Store C\#](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012/)/[Windows Store JavaScript](/en-us/develop/mobile/tutorials/get-started-with-push-js-vs2012/)/[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-push-wp8/)/[iOS](/en-us/develop/mobile/tutorials/get-started-with-push-ios/)/[Android](/en-us/develop/mobile/tutorials/get-started-with-push-android/)) utilisent une table **Registrations** et stockent l'URI de canal (Windows), le jeton d'appareil (iOS) ou le handle (Android) dans une colonne nommée *handle*.

**Remarque**

Lorsque vous utilisez l'Assistant Ajouter une notification Push dans Visual Studio 2013 pour ajouter des notifications Push à une application Windows Store, l'Assistant crée automatiquement une table nommée **Channel** pour stocker les URI de canal. Le didacticiel **Prise en main des notifications Push dans Visual Studio 2012** ([Windows Store C\#](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012)/[Windows Store JavaScript](/en-us/develop/mobile/tutorials/get-started-with-push-js-vs2012)) explique comment activer les notifications Push avec la table **Registrations**.

Pour prendre en charge plusieurs clients dans cette unique table d'inscription, incluez une colonne *platform* dans la table, ce champ étant défini sur la plateforme du client qui insère une ligne lors du processus d'inscription. Par exemple, la définition de classe **Registrations**, provenant d'une application Windows Store C\# ou Windows Phone, mappe le champ *ChannelUri* du client à la colonne *handle* de la table Registrations :

     public class Registrations
        {
            [JsonProperty(PropertyName = "platform")]         
            public string Platform { 
                get
                {
                    return "windowsstore";
                    // renvoyer "windowsphone" ;
                }
                set {}
            }
            
            public string Id { get; set; }
        
            [JsonProperty(PropertyName = "handle")]
            public string ChannelUri { get; set; }
        }

Notez que sur cet appareil Windows, le champ *Platform* renvoie toujours `windowsstore` (ou `windowsphone`). Lorsque le schéma dynamique est activé (paramètre par défaut), Mobile Services ajoute une colonne platform à la table Registrations, si elle n'existe pas déjà. Pour plus d'informations, consultez la page [Schéma dynamique](http://msdn.microsoft.com/fr-fr/library/windowsazure/jj193175.aspx).

Dans votre script côté serveur qui envoie des notifications, utilisez le champ platform pour déterminer quelle fonction propre à la plateforme doit être appelée sur l'[objet Push](http://msdn.microsoft.com/fr-fr/library/windowsazure/jj554217.aspx). Le script suivant est une modification du script serveur des didacticiels **Prise en main des notifications Push** ([Windows Store C\#](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012/)/[Windows Store JavaScript](/en-us/develop/mobile/tutorials/get-started-with-push-js-vs2012/)/[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-push-wp8/)/[iOS](/en-us/develop/mobile/tutorials/get-started-with-push-ios/)/[Android](/en-us/develop/mobile/tutorials/get-started-with-push-android/)) pour l'activation de plusieurs plateformes clientes :

     function insert(item, user, request) {
            request.execute({
                success: function() {
                    request.respond();
                    sendNotifications();
                }
            });
        
            function sendNotifications() {
                var registrationsTable = tables.getTable('Registrations');
                registrationsTable.read({
                    success: function(registrations) {
                        registrations.forEach(function(registration) {
                            if (registration.platform === 'winstore') {
                                push.wns.sendToastText04(registration.handle, {
                                    text1: item.text
                                }, {
                                    success: pushCompleted
                                });
                            } else if (registration.platform === 'winphone') {
                                push.mpns.sendFlipTile(registration.handle, {
                                    title: item.text
                                }, {
                                    success: pushCompleted
                                });
                            } else if (registration.platform === 'ios') {
                                push.apns.send(registration.handle, {
                                    alert: "Toast: " + item.text,
                                    payload: {
                                        inAppMessage: item.text
                                    }
                                });
                            } else if (registration.platform === 'android') {
                                push.gcm.send(registration.handle, item.text, {
                                    success: pushCompleted
                                });
                            } else {
                                console.error("Unknown push notification platform");
                            }
                        });
                    }
                });
            }
        
            function pushCompleted(pushResponse) {
                console.log("Sent push:", pushResponse);
            }
        }

Inscription d'une application Windows
-------------------------------------

Pour utiliser l'authentification unique du client avec un compte Microsoft dans des applications Windows Store et Windows Phone, vous devez d'abord inscrire l'application Windows Store sur le tableau de bord Windows Store. En effet, une fois que vous créez une inscription Live Connect pour Windows Phone, vous ne pouvez pas en créer une pour Windows Store. Pour plus d'informations sur la marche à suivre, lisez la rubrique **Authentification unique de votre application Windows Store avec Live Connect** ([Windows Store](/en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/)/[Windows Phone](/en-us/develop/mobile/tutorials/single-sign-on-wp8/)).

Meilleures pratiques pour réutiliser le code dans des projets Visual Studio
---------------------------------------------------------------------------

Les bibliothèques de classes portables vous permettent d'écrire et de créer des assemblys gérés qui fonctionnent sur plusieurs plateformes telles que Windows Store et Windows Phone. Vous pouvez créer des classes contenant du code que vous souhaitez partager entre plusieurs projets, puis référencer ces classes à partir de différents types de projets.

Vous pouvez utiliser la bibliothèque de classes portable .NET Framework pour implémenter le modèle MVVM (Model-View-View Model) et partager les assemblys entre plusieurs plateformes. Vous pouvez implémenter le modèle et afficher les classes du modèle dans un projet de bibliothèque de classes portable dans Visual Studio 2012, puis créer des vues personnalisées pour différentes plateformes. Le code du modèle, commun aux différentes plateformes, peut (par exemple) récupérer les données d'une source telle qu'un service mobile Azure d'une manière indépendante de la plate-forme. La bibliothèque MSDN fournit une [vue d'ensemble et un exemple](http://msdn.microsoft.com/fr-fr/library/gg597391(v=vs.110)), une discussion sur les [différences entre les API](http://msdn.microsoft.com/fr-fr/library/gg597392(v=vs.110)), un exemple d'[utilisation de bibliothèques de classes portables pour implémenter le modèle MVVM](http://msdn.microsoft.com/fr-fr/library/hh563947(v=vs.110)), d'autres [orientations normatives](http://msdn.microsoft.com/fr-fr/library/windowsphone/develop/jj714086(v=vs.105).aspx) et des informations sur la [gestion des ressources](http://msdn.microsoft.com/fr-fr/library/hh871422(v=vs.110)) dans les projets de bibliothèque de classes portable.

