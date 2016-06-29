<properties
	pageTitle="Exemple Azure IoT MyDriving : Démarrage rapide | Microsoft Azure"
	description="Commencez par une application qui détaille toute la procédure permettant de concevoir un système IoT avec Microsoft Azure, comme Stream Analytics, Machine Learning et Event Hubs."
	services=""
    documentationCenter=".net"
    suite=""
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="multiple"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/25/2016"
	ms.author="awills"/>

# Système IoT MyDriving : démarrage rapide

MyDriving est un système présentant une démonstration de la conception et de l’implémentation d’une solution [Internet des objets](iot-suite-overview.md) (IoT) classique, qui collecte des informations de télémétrie d’appareils, traite ces données dans le cloud, et procède à un apprentissage automatique pour fournir une réponse adaptative. La démonstration consigne des données sur vos trajets en voiture, en exploitant les informations provenant à la fois de votre téléphone mobile et d’un dispositif qui recueille les renseignements fournis par le système de contrôle de votre voiture. Elle utilise ces données pour fournir un retour d’expérience sur votre style de conduite par rapport à d’autres utilisateurs.

Le véritable objectif de MyDriving est de vous aider à créer votre propre solution IoT. Mais avant cela, vous devez apprendre à utiliser l’application MyDriving, en tant que membre de notre équipe d’utilisateurs testeurs. Vous commencerez par une prise en main de l’application et du système sous-jacent en tant qu’utilisateur, avant d’aborder l’architecture. Vous découvrirez également HockeyApp, une solution pratique pour gérer les distributions alpha et bêta de vos applications aux utilisateurs testeurs.

## Profitez de l’expérience mobile

Vous pouvez utiliser l’application MyDriving si vous possédez un appareil Android, iOS ou Windows 10.

### Installation sous Android et Windows 10 Mobile

Sur votre appareil :

1.  Autoriser les applications de développement :

    -   Android : sous **Paramètres** > **Sécurité**, autorisez les applications de **Sources inconnues**.

    -   Windows 10 : sous **Paramètres** > **Mises à jour** > **Pour les développeurs**, sélectionnez **Mode développeur**.

2.  Rejoignez notre équipe de bêta-testeurs en vous inscrivant ou en vous connectant à [HockeyApp](https://rink.hockeyapp.net). HockeyApp facilite la distribution des versions préliminaires de votre application aux utilisateurs test.

    Si vous utilisez Windows 10, utilisez le navigateur Edge.

    Si vous avez participé à la conférence Build 2016, connectez-vous avec le compte Microsoft que vous avez spécifié lors de la conférence, en utilisant l’un des boutons Microsoft. Vous êtes déjà inscrit à HockeyApp.

    ![Écran de connexion de HockeyApp](./media/iot-solution-get-started/image1.png)

3.  Téléchargez et installez l’application à partir d’ici :

    -   [Android](http://rink.io/spMyDrivingAndroid)

    -   [Windows 10](http://rink.io/spMyDrivingUWP)

    Vous devez effectuer deux opérations. Installez le certificat dans **Personnes autorisées**. Ensuite, installez l’application.

*Des problèmes pour démarrer l’application sur Windows 10 Mobile ?* Votre téléphone a peut-être une ou deux mises à jour de retard. Vérifiez que vous disposez des dernières mises à jour ou installez :

 - [Microsoft.NET.Native.Framework.1.2.appx](https://download.hockeyapp.net/packages/win10/Microsoft.NET.Native.Framework.1.2.appx) 

 - [Microsoft.NET.Native.Runtime.1.1.appx](https://download.hockeyapp.net/packages/win10/Microsoft.NET.Native.Runtime.1.1.appx)

 - [Microsoft.VCLibs.ARM.14.00.appx](https://download.hockeyapp.net/packages/win10/Microsoft.VCLibs.ARM.14.00.appx)


### Installation sous iOS

Si vous avez participé à la conférence Build 2016, téléchargez l’application en tant que membre de notre équipe de testeurs sur HockeyApp :

1.  Sur votre appareil iOS, connectez-vous à [HockeyApp](https://rink.hockeyapp.net). Utilisez l’un des boutons de connexion Microsoft, puis connectez-vous avec le compte Microsoft que vous avez spécifié lors de la conférence. (N’utilisez pas les champs d’adresse de messagerie et de mot de passe).

    ![Écran de connexion de HockeyApp](./media/iot-solution-get-started/image1.png)

2.  Dans le tableau de bord de HockeyApp, sélectionnez l’application MyDriving, puis téléchargez-la.

3.  Autorisez la version bêta de HockeyApp :

    a. Accédez à **Paramètres** > **Général** > **Gestion des profils et de l’appareil**.

    b. Approuvez le certificat **Bit Stadium GmbH**.

Si vous n’avez pas assisté à la conférence Build 2016, vous pouvez générer et déployer l’application vous-même :

1.   Téléchargez le code [à partir de GitHub].

2.   Effectuez la génération et le déploiement [à l’aide de Xamarin].

Pour plus d’informations, consultez le document [MyDriving Reference Guide](http://aka.ms/mydrivingdocs).

## Obtenir un adaptateur OBD (facultatif)

Il s’agit de l’opération qui fait de cette application un véritable système de l’Internet des objets. Vous pouvez utiliser l’application sans ce dispositif, mais c’est plus amusant avec l’objet réel qui n’est pas coûteux.

La fonctionnalité OBD (On-Board Diagnostics) est celle que votre garagiste utilise pour paramétrer votre voiture et diagnostiquer les bruits suspects et les voyants d’avertissement. À moins que votre voiture ne soit un modèle de collection, vous trouverez un connecteur quelque part dans l’habitacle, généralement derrière un volet sous le tableau de bord. Avec le connecteur approprié, vous pouvez obtenir des mesures des performances du moteur et effectuer certains réglages. Vous pouvez acheter un connecteur OBD économique à votre fournisseur habituel. Il se connecte par Bluetooth ou Wi-Fi à une application sur votre téléphone.

Dans le cas présent, nous allons connecter votre voiture au cloud. L’adaptateur OBD se connecte directement à votre téléphone, mais notre application fonctionne comme un relais. Les données de télémétrie de votre voiture sont envoyées directement au hub IoT MyDriving, où elles sont traitées pour consigner vos déplacements routiers et évaluer votre style de conduite.

Pour connecter un appareil OBD :

1.  Vérifiez que votre voiture est équipée d’un connecteur OBD.

2.  Procurez-vous un adaptateur OBD :

    -   Si vous utilisez un téléphone Android ou Windows, vous avez besoin d’un adaptateur OBD II Bluetooth. Nous avons utilisé le modèle [BAFX Products 34t5 Bluetooth OBDII Scan Tool].

    -   Si vous utilisez un téléphone iOS, vous avez besoin d’un adaptateur OBD Wi-Fi. Nous avons utilisé le modèle [ScanTool OBDLink MX Wi-Fi: OBD Adapter/Diagnostic Scanner].

3.  Suivez les instructions fournies avec votre adaptateur OBD pour le connecter à votre téléphone. N'oubliez pas les éléments suivants :

    -   Un adaptateur Bluetooth doit être associé au téléphone dans la page **Paramètres**.

    -   Un adaptateur Wi-Fi doit avoir une adresse dans la plage 192.168.xxx.xxx.

4.  Si vous avez plusieurs voitures, vous pouvez vous procurer un adaptateur distinct pour chacune d’elles (trois au maximum).

Si vous n’avez pas d’adaptateur OBD, l’application envoie malgré tout les données de localisation et de vitesse du GPS du téléphone au serveur principal, et vous demande si vous souhaitez simuler un OBD.

Pour plus d’informations sur l’utilisation des données de l’adaptateur OBD par l’application et sur les options de création de votre propre appareil OBD, consultez la section 2.1 « IoT Devices » du document [MyDriving Reference Guide](http://aka.ms/mydrivingdocs).

## Utiliser l’application

Démarrez l’application. Une description de démarrage rapide initial est disponible pour vous aider à comprendre comment cela fonctionne.

### Suivez vos trajets

Appuyez sur le bouton d’enregistrement (grand cercle rouge en bas de l’écran) pour commencer un trajet, et appuyez à nouveau dessus pour le terminer.

![Illustration du bouton d’enregistrement pour le suivi des trajets](./media/iot-solution-get-started/image2.png)

Chaque fois que vous commencez un trajet, si vous ne disposez d’aucun appareil OBD, vous êtes invité à utiliser le simulateur.

À la fin d’un trajet, cliquez sur le bouton d’arrêt pour obtenir un résumé.

![Exemple de résumé d’un trajet](./media/iot-solution-get-started/image3.png)

### Passez en revue vos trajets

![Exemple de trajet effectué](./media/iot-solution-get-started/image4.png)

### Examinez votre profil

![Exemple de profil de style de conduite](./media/iot-solution-get-started/image5.png)

## Envoyez-nous vos commentaires sur le test

Étant donné que nous avons créé MyDriving pour vous aider à lancer vos propres systèmes IoT, nous sommes très désireux de recevoir de vos nouvelles concernant son fonctionnement. Faites-nous savoir si :

- Vous rencontrez des difficultés ou des problèmes.

- Il existe un point d’extension qui serait plus adapté à votre scénario.

- Vous avez trouvé un moyen plus efficace pour effectuer certaines tâches.

- Vous avez des suggestions pour améliorer MyDriving ou cette documentation.

Dans l’application MyDriving, vous pouvez utiliser le mécanisme de commentaires intégré de HockeyApp : sous iOS et Android, servez-vous de votre téléphone ou utilisez la commande de menu **Commentaires**. Cela a pour effet de joindre automatiquement une capture d’écran qui nous permet de comprendre ce dont vous parlez. Et, en cas d’accidents, HockeyApp collecte ces informations pour nous en informer. Vous pouvez également nous faire part de vos commentaires sur le [portail HockeyApp].

Vous pouvez également signaler un [problème sur GitHub] ou laisser un commentaire ci-dessous (édition anglaise).

Nous espérons recevoir bientôt de vos nouvelles.

## Étapes suivantes

-   Consultez le document [MyDriving Reference Guide](http://aka.ms/mydrivingdocs) pour comprendre comment nous avons conçu et construit le système MyDriving.

-   [Créez et déployez votre propre système](iot-solution-build-system.md) à l’aide de nos scripts Azure Resource Manager. Le document [MyDriving Reference Guide](http://aka.ms/mydrivingdocs) décrit également les aspects que vous allez le plus personnaliser.

  [à partir de GitHub]: https://github.com/Azure-Samples/MyDriving
  [à l’aide de Xamarin]: https://developer.xamarin.com/guides/ios/getting_started/installation/
  [BAFX Products 34t5 Bluetooth OBDII Scan Tool]: http://www.amazon.com/gp/product/B005NLQAHS
  [ScanTool OBDLink MX Wi-Fi: OBD Adapter/Diagnostic Scanner]: http://www.amazon.com/gp/product/B00OCYXTYY/ref=s9_simh_gw_g263_i1_r?pf_rd_m=ATVPDKIKX0DER&pf_rd_s=desktop-2&pf_rd_r=1MWRMKXK4KK9VYMJ44MP
  [portail HockeyApp]: https://rink.hockeyapp.org
  [problème sur GitHub]: https://github.com/Azure-Samples/MyDriving/issues

<!---HONumber=AcomDC_0615_2016-->