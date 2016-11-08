1. Dans le fichier insert.js de la table **channels**, recherchez les lignes de code suivantes, placez-les en commentaire ou supprimez-les du fichier, puis enregistrez les modifications.
   
        sendNotifications(item.channelUri);
   
        function sendNotifications(uri) {
            console.log("Uri: ", uri);
            push.wns.sendToastText01(uri, {
                text1: "Sample toast from sample insert"
            }, {
                success: function (pushResponse) {
                    console.log("Sent push:", pushResponse);
                }
            });
        }
   
    Lors de l’enregistrement des modifications apportées au fichier insert.js, une nouvelle version du script est chargée sur votre service mobile.
2. Dans l’Explorateur de serveurs, développez la table TodoItem, ouvrez le fichier insert.js et remplacez la fonction insert actuelle par le code suivant, puis enregistrez les modifications :
   
        function insert(item, user, request) {
            request.execute({
                success: function() {
                    request.respond();
                    sendNotifications();
                }
            });
   
            function sendNotifications() {
                var channelsTable = tables.getTable('channels');
                channelsTable.read({
                    success: function(devices) {
                        devices.forEach(function(device) {
                            push.wns.sendToastText04(device.channelUri, {
                                text1: item.text
                            }, {
                                success: function(pushResponse) {
                                    console.log("Sent push:", pushResponse);
                                }
                            });
                        });
                    }
                });
            }
        }
   
    Désormais, lors de l’insertion d’un nouveau TodoItem, une notification Push est envoyée à tous les appareils inscrits.

<!---HONumber=Oct15_HO3-->