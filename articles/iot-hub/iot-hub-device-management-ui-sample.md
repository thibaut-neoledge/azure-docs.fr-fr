<properties
 pageTitle="Utiliser l’interface utilisateur de gestion d’appareils IoT Hub | Microsoft Azure"
 description="Procédure pas à pas pour utiliser l’interface utilisateur de gestion d’appareils Azure IoT Hub"
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="06/08/2016"
 ms.author="dobett"/>

# Explorer la gestion d’appareils Azure IoT Hub à l’aide de l’exemple d’interface utilisateur

Interagir avec l’exemple d’interface utilisateur de gestion d’appareils vous permettra de renforcer les concepts et les fonctionnalités abordés dans les articles [Vue d’ensemble][lnk-dm-overview] et [Prise en main][lnk-get-started] de la gestion d’appareils Azure IoT Hub. Cet article vous permettra de vous familiariser avec les trois principaux concepts de gestion d’appareils (*représentation d’appareil*, *requêtes d’appareil* et *travaux d’appareil*) comme représenté dans l’exemple d’interface utilisateur Web de gestion d’appareils.

Les développeurs souhaitant créer leur propre expérience interactive de gestion d’appareils peuvent répliquer l’exemple de code base de l’interface utilisateur pour l’utiliser comme base pour un projet personnalisé. Vous pouvez consulter l’intégralité du code de projet et les documents Lisez-moi qui décrivent en détail les fonctionnalités de développement supplémentaires dans le référentiel GitHub [Azure IoT device management UI][lnk-dm-github] (Interface utilisateur de gestion d’appareils Azure IoT).

## Composants requis

Avant de commencer ce didacticiel, vous devez effectuer les étapes décrites dans l’article [Prise en main de la gestion d’appareils Azure IoT Hub][lnk-get-started]. Si vous ne l’avez pas encore fait, veuillez retourner sur cet article et effectuez toutes les étapes décrites avant de poursuivre.

Une fois le didacticiel « Prise en main » terminé, les éléments suivants s’exécuteront sur votre système de test :

- Six appareils simulés **iotdm\_simple\_sample** s’exécutent dans des fenêtres de console/terminal, chacune affichant un message « ENREGISTRÉ » de confirmation.

- L’exemple d’application d’interface utilisateur de gestion d’appareils créé et exécuté localement sur <http://127.0.0.1:3003>.

## Vue Appareils par défaut

L’écran d’accueil par défaut de l’exemple d’interface utilisateur de gestion d’appareils est la vue **Appareils** qui inclut les 5 éléments suivants :

![][1]

1.  *Boutons de navigation* : vue **Appareils** (sélectionnée), vue **Historique des travaux** et vue **Ajouter un appareil**.

2. *Recherche d’appareil* : recherchez et modifiez un appareil par ID d’appareil.

3.  *Actions de l’appareil* : action **Modifier**, action **Supprimer** et action **Exporter**.

4.  *Travaux d’appareil* : **Redémarrer** l’appareil **Mise à jour de microprogramme** et **Réinitialisation aux paramètres d’usine**.

5.  *Filtre grille d’appareils* : éditeur de filtre pour la création et l’enregistrement de vues personnalisées de la grille d’appareil.

6.  *Grille d’appareil* : affichez tous les appareils enregistrés dans votre instance IoT Hub avec les propriétés par défaut (**ID de l’appareil**, **État** et **Balises**).

La [vue d’ensemble de la gestion d’appareils][lnk-dm-overview] introduit le concept de *représentation d’appareil* qui représente un appareil physique (ou simulé) dans Azure IoT Hub. À partir de la grille d’appareil, vous pouvez sélectionner n’importe quel appareil enregistré dans la liste d’appareils pour afficher et modifier la représentation d’appareil de celui-ci.

Entrez dans cette vue détaillée sur votre premier appareil simulé, **Device11-7ce4a850**, en sélectionnant la ligne d’appareil correspondante puis en cliquant sur le bouton **Modifier** (vous pouvez également double-cliquer sur la ligne ou saisir l’ID de l’appareil dans la zone de recherche).

Vous pouvez désormais voir l’intégralité de la représentation des composants de la représentation d’appareil, dans laquelle vous pouvez mettre à jour les propriétés et exécuter d’autres opérations d’appareil, comme décrit ci-dessous :

![][2]

1.  **Edit a Device Twin** (Modifier une représentation d’appareil) : vous pouvez choisir **d’activer ou de désactiver** cette option pour l’appareil.

2.  **Propriétés du service** : cela inclut les **balises** d’appareil.

3.  **Propriétés de l’appareil** : cliquez pour développer cette section.

4.  Bouton **Actualiser** : récupérer les valeurs et les propriétés de représentation d’appareil les plus récentes.

Pour continuer, cliquez sur **Annuler** dans le coin inférieur droit de cette vue afin de retourner à la page de liste d’appareils par défaut.

## Utiliser des requêtes d’appareil pour filtrer la vue Appareils

Les requêtes d’appareil constituent un moyen efficace pour localiser rapidement un appareil ou un groupe d’appareils à l’aide d’une propriété particulière (une balise particulière, par exemple). L’exemple d’interface utilisateur utilise des requêtes d’appareil pour remplir la liste d’appareils de la page de liste d’appareils par défaut. L’exemple d’interface utilisateur vous permet d’ajouter et de supprimer des propriétés du service dans le tableau et de filtrer sur certaines de ces propriétés.

Procédez comme suit pour créer un filtre de client sur la balise de propriété du service « bacon » :

1.  Cliquez sur l’icône d’entonnoir pour ouvrir la vue de modification de requête d’appareil :

    ![][3]

2.  Cliquez sur **+ Ajouter un filtre** pour développer l’éditeur. Sélectionnez **Balises** dans la liste déroulante des propriétés, puis saisissez **bacon** dans le champ de texte et cliquez sur **Appliquer**. Seuls les trois appareils avec la balise « bacon » figurent désormais dans la liste d’appareils :

    ![][4]

3.  Dans le champ de titre de requête (à côté de l’icône d’entonnoir), nommez la requête **Only Bacon** (Bacon uniquement) et cliquez sur **Enregistrer** :

    ![][5]

4.  Cliquez sur l’icône d’entonnoir pour quitter l’éditeur de requête d’appareil.

## Utiliser un travail d’appareil pour simuler des redémarrages d’appareil 

Comme vous l’avez appris dans la vue d’ensemble de gestion d’appareils, les travaux d’appareil vous permettent d’orchestrer des actions simples ou complexes sur un ou plusieurs appareils physiques. Dans cette section, vous allez créer un travail d’appareil dans l’exemple d’interface utilisateur pour effectuer une opération de redémarrage sur tous les appareils de simulation avec la balise « bacon » :

1.  À partir de la liste de requête d’appareils **Only Bacon** (Bacon uniquement), cliquez sur chaque ligne d’appareil afin de le sélectionner pour l’opération de travail de redémarrage :

    ![][6]

2.  Cliquez sur le bouton **Redémarrer** dans la barre d’outils des travaux de l’appareil afin de créer le travail de redémarrage. Après avoir confirmé **Oui**, cliquez sur le lien hypertexte **Historique des travaux** dans la boîte de dialogue **Job for Device has started** (Le travail de l’appareil a commencé) générée pour naviguer vers la vue Devices Jobs (Travaux de l’appareil).

    ![][7]

Vous venez de créer un travail parent unique qui génère trois travaux enfants, chacun d’entre eux effectue l’opération de redémarrage sur un des trois appareils ayant la balise « bacon » :

![][8]

Si vous actualisez cet écran après quelques minutes, l’état du travail parent et celui des trois travaux enfants seront définis comme étant **terminés**, ce qui indique que les opérations de redémarrage se sont déroulées correctement et qu’elles ont bien été confirmées par les appareils simulés. Utilisez la colonne **ID de l’appareil** pour déterminer quels sont les travaux associés à tel ou tel appareil.


> [AZURE.NOTE] Si l’état de vos travaux enfants est défini comme étant « échoué », vérifiez que vos appareils simulés sont encore en cours d’exécution sur votre système de test. Si ce n’est pas le cas, exécutez à nouveau le script simulate.bat ou simulate.sh et répétez les étapes relatives au travail d’appareil de redémarrage ci-dessus.

## Étapes suivantes

Vous avez maintenant terminé d’explorer les concepts de gestion d’appareils et d’utiliser l’exemple d’interface utilisateur de gestion d’appareils. Si vous souhaitez approfondir vos connaissances sur les API de gestion d’appareils et découvrir certains exemples de code, consultez les didacticiels de développement et les ressources suivants :

- [Utilisation des représentations d’appareil physique][lnk-tutorial-twin]
- [Recherche de représentations d’appareil physique à l’aide de requêtes][lnk-tutorial-queries]
- [Utilisation de travaux d’appareils pour mettre à jour le microprogramme des appareils][lnk-tutorial-jobs]
- [Activer les appareils gérés derrière une passerelle IoT][lnk-dm-gateway]
- [Présentation de la bibliothèque cliente de gestion des appareils (DM) Azure IoT Hub][lnk-library-c]

Pour explorer davantage les capacités de IoT Hub, consultez :

- [Conception de votre solution][lnk-design]
- [Guide du développeur][lnk-devguide]
- [Simulation d’un appareil avec le Kit de développement logiciel (SDK) Gateway][lnk-gateway]
- [Utilisation du portail Azure pour gérer IoT Hub][lnk-portal]
- [Sécuriser votre solution IoT de bout en bout][lnk-securing]

[1]: media/iot-hub-device-management-ui-sample/image1.png
[2]: media/iot-hub-device-management-ui-sample/image2.png
[3]: media/iot-hub-device-management-ui-sample/image3.png
[4]: media/iot-hub-device-management-ui-sample/image4.png
[5]: media/iot-hub-device-management-ui-sample/image5.png
[6]: media/iot-hub-device-management-ui-sample/image6.png
[7]: media/iot-hub-device-management-ui-sample/image7.png
[8]: media/iot-hub-device-management-ui-sample/image8.png

[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management/

[lnk-tutorial-twin]: iot-hub-device-management-device-twin.md
[lnk-tutorial-queries]: iot-hub-device-management-device-query.md
[lnk-tutorial-jobs]: iot-hub-device-management-device-jobs.md
[lnk-dm-gateway]: iot-hub-gateway-device-management.md
[lnk-library-c]: iot-hub-device-management-library.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md
[lnk-securing]: iot-hub-security-ground-up.md

<!---HONumber=AcomDC_0727_2016-->