<properties 
	pageTitle="Exemple de MyDriving Azure IoT - Démarrage rapide | Microsoft Azure" 
	description="Commencez par une application offrant une démonstration complète de l’architecture d’un système IoT avec Microsoft Azure, telle que les services Stream Analytics, Machine Learning et Event Hubs." 
	services="" 
    documentationCenter=""
    suite="iot-suite"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="iot-suite" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/25/2016" 
	ms.author="awills"/>

# Système IoT MyDriving : démarrage rapide

MyDriving est un système présentant une démonstration de la conception et de l’implémentation d’une solution [Internet des objets](iot-suite-overview.md) (IoT) classique, qui collecte des informations de télémétrie d’appareils, traite ces données dans le cloud, et procède à un apprentissage automatique pour fournir une réponse adaptative. La démonstration consigne des données sur vos trajets en voiture, en exploitant des informations provenant à la fois de votre téléphone mobile et d’un dispositif de diagnostic embarqué (OBD) qui recueille des renseignements fournis par le système de contrôle de votre voiture. Elle utilise ces données pour fournir un retour d’expérience sur votre style de conduite par rapport à d’autres utilisateurs.


![](./media/iot-solution-get-started/image5.png)

Le véritable objectif de MyDriving est de vous aider à commencer à créer votre propre solution IoT. Mais avant tout, vous devez prendre en main l’application MyDriving proprement dite, en tant que membre de notre équipe d’utilisateurs test. Cela vous permettra d’acquérir une expérience de l’application et du système sous-jacent en tant que consommateur, avant d’aborder l’architecture. Vous découvrirez également HockeyApp, un moyen pratique de gérer les distributions alpha et bêta de vos applications au utilisateurs test.

## Utiliser l’expérience Mobile

**Conditions préalables** :

appareil Android, iOS ou Windows 10.

## Installation sous Android et Windows Phone 10

Sur votre appareil :

1.  **Autoriser les applications de développement**

    -   Android : sous **Paramètres**, **Sécurité**, autorisez les applications de **Sources inconnues**.

    -   Windows 10 : sous **Paramètres**, **Mises à jour**, **Pour les développeurs**, définissez **mode développeur**.

2.  **Rejoignez notre équipe de test bêta**.

    HockeyApp facilite la distribution des versions préliminaires de votre application aux utilisateurs test.

    Sur votre appareil mobile :

    -   **Inscrivez-vous/connectez-vous à** HockeyApp via la page <https://rink.hockeyapp.net>.
    
        (Si vous utilisez Windows 10, utilisez le navigateur Edge).

        *Participants à la build 2016* : connectez-vous avec le compte MSA que vous avez inscrit à la conférence, en utilisant l’un des boutons Microsoft. Vous êtes déjà inscrit à HockeyApp.

        ![](./media/iot-solution-get-started/image1.png)

3.  **Téléchargez et installez** l’application à partir d’ici :

    -   Android : <http://rink.io/spMyDrivingAndroid>

    -   Windows Phone 10 : <http://rink.io/spMyDrivingUWP>

        Vous devez effectuer deux opérations. Installez le certificat dans Personnes autorisées. Ensuite, installez l’application.
    
*Vous rencontrez des problèmes de démarrage sous Windows Phone 10 ?* Votre téléphone est peut en retard d’une ou deux mises à jour. Vérifiez que vous disposez des dernières mises à jour ou installez :

 - [Microsoft.NET.Native.Framework.1.2.appx](https://download.hockeyapp.net/packages/win10/Microsoft.NET.Native.Framework.1.2.appx) 
 - [Microsoft.NET.Native.Runtime.1.1.appx](https://download.hockeyapp.net/packages/win10/Microsoft.NET.Native.Runtime.1.1.appx) 
 - [Microsoft.VCLibs.ARM.14.00.appx](https://download.hockeyapp.net/packages/win10/Microsoft.VCLibs.ARM.14.00.appx) 
   

## Installation sous iOS

### Participants à la build 2016

Si vous en êtes à la build 2016, téléchargez l’application en tant que membre de notre équipe de test sur HockeyApp.

1.  Sur votre appareil iOS, connectez-vous à <https://rink.hockeyapp.net>. Utilisez l’un des boutons de connexion Microsoft, puis connectez-vous avec le compte MSA que vous avez inscrit à la conférence. (N’utilisez pas les champs d’adresse de messagerie et de mot de passe).

    ![](./media/iot-solution-get-started/image1.png)

2.  Dans le tableau de bord HockeyApp, sélectionnez l’application MyDriving, puis téléchargez-la.

3.  Autorisez la version bêta de HockeyApp :

    Accédez à **Paramètres** &gt;**Général** &gt;**Gestion des profils et de l’appareil**.

    Approuvez le certificat **Bit Stadium GmbH**.

### Si vous n’avez pas participé à la build 2016

Nous allons bientôt publier l’application sur l’iOS Store.

Pour le moment, vous pouvez générer et déployer l’application vous-même :

-   Téléchargez le code [à partir de GitHub].

-   Générez et déployez [à l’aide de Xamarin].

Pour plus de détails, voir le [Guide de référence MyDriving](http://aka.ms/mydrivingdocs).

## Obtenir une carte OBD (facultatif)

Il s’agit de l’opération qui fait de cette application un véritable système de l’Internet des objets. Vous pouvez utiliser l’application sans ce dispositif, mais c’est plus amusant avec l’objet réel qui n’est pas coûteux.

Le diagnostic embarqué (OBD) est la fonctionnalité de votre voiture que votre garagiste utilise pour régler celle-ci et diagnostiquer d’éventuels bruits bizarres et autres témoins d’avertissement. À moins que votre voiture soit très ancienne, vous devez trouver un connecteur sous le tableau de bord. Avec le connecteur approprié, vous pouvez obtenir des mesures des performances du moteur et effectuer certains réglages. Vous pouvez acheter un connecteur OBD économique à votre fournisseur habituel. Le dispositif se connecte à une application installée sur votre téléphone via une liaison Bluetooth ou Wi-Fi.

Dans le cas présent, nous allons connecter votre voiture au cloud. Certes, la connexion directe du système OBD est établie avec votre téléphone, mais notre application fonctionne en tant que relais. Les données de télémétrie de votre voiture sont envoyée directement à l’IoT Hub MyDriving , où elles sont traitées pour consigner votre trajet et évaluer votre style de conduite.

### Connecter un appareil OBD


1.  Vérifiez que votre voiture est équipée d’un connecteur OBD (c’est probablement la cas, sauf si votre voiture est ancienne). Il se trouve quelque part dans l’habitacle, généralement derrière un clapet sous le tableau de bord.

2.  Procurez-vous un adaptateur OBD. Nous avons utilisé les types suivants :

    Si vous utilisez :

    -   un téléphone **Android ou Windows**, vous avez besoin d’un adaptateur **OBD II compatible Bluetooth**. Nous avons utilisé le modèle [BAFX Products 34t5 Bluetooth OBDII Scan Tool].

    -   un téléphone **iOS**, vous avez besoin d’un adaptateur OBD **compatible Wi-Fi**. Nous avons utilisé le modèle [ScanTool OBDLink MX Wi-Fi: OBD Adapter/Diagnostic Scanner].

3.  Suivez les instructions fournies avec votre adaptateur OBD pour connecter celui-ci à votre téléphone.

    -   Un adaptateur Bluetooth doit être associé au téléphone dans la page Paramètres.

    -   Un adaptateur Wi-Fi doit avoir une adresse dans la plage 192.168.xxx.xxx.

4.  Si vous avez plusieurs voitures, vous pouvez vous procurer un adaptateur distinct pour chaque d’elles (3 maximum).

Si vous n’avez pas d’adaptateur OBD, l’application envoie malgré tout les données de localisation et de vitesse du GPS du téléphone au serveur principal, et vous demande si vous souhaitez simuler un OBD.

Pour plus d’informations sur la façon dont l’application utilise les données de l’adaptateur OBD et sur les options de création de votre propre appareil OBD, voir la section 2.1 relative aux « appareils IoT » dans le [Guide de référence MyDriving](http://aka.ms/mydrivingdocs).

## Utilisation de l’application

**Démarrez** l’application. Une description de démarrage rapide initial est disponible pour vous aider à comprendre comment cela fonctionne.

-   **Suivez vos trajets.** Appuyez sur le bouton d’enregistrement (grand cercle rouge en bas de l’écran) pour commencer un trajet, et appuyez à nouveau dessus pour le terminer.


    ![](./media/iot-solution-get-started/image2.png)

-   Chaque fois que vous commencez un trajet, si vous ne disposez d’aucun appareil OBD, vous êtes invité à utiliser le simulateur.

-   À la fin d’un trajet, cliquez sur le bouton d’arrêt. Vous obtenez alors un résumé :

    ![](./media/iot-solution-get-started/image3.png)

-   **Passez en revue vos trajets** :

    ![](./media/iot-solution-get-started/image4.png)

-   **Passez en revue votre profil** :

    ![](./media/iot-solution-get-started/image5.png)

-   **Envoyez-nous vos commentaires relatifs au test** en appuyant sur le bouton prévu à cet effet dans l’application, ou en agitant simplement votre téléphone. Cela a pour effet de joindre automatiquement une capture d’écran qui nous permet de comprendre ce dont vous parlez. Et, en cas d’accident regrettable, HockeyApp collecte les journaux d’incident pour nous en informer.

## Commentaires 

Étant donné que nous avons créé MyDriving pour vous aider à lancer vos propres systèmes IoT, nous sommes très désireux de recevoir de vos nouvelles concernant son fonctionnement. Faites-nous savoir si vous rencontrez des difficultés ou des problèmes, s’il existe un point d’extension qui rendrait la solution plus adaptée à votre situation, si vous trouvez un moyen plus efficace de combler certains besoins, ou si vous avez des suggestions à formuler pour améliorer MyDriving ou la présente documentation.

Dans l’application MyDriving proprement dite, vous pouvez utiliser le mécanisme de commentaires intégré de HockeyApp : sous iOS et Android, agitez simplement votre téléphone, ou utilisez la commande de menu Commentaires. Vous pouvez également nous faire part de vos commentaires via le [portail HockeyApp].

Vous pouvez également signaler un [problème sur GitHub], ou laisser un commentaire ci-dessous (édition fr-FR).

Nous espérons recevoir bientôt de vos nouvelles.

## Étapes suivantes

-   Consultez le [Guide de référence MyDriving](http://aka.ms/mydrivingdocs) pour comprendre comment nous avons conçu et généré le système MyDriving.

-   [Créez et déployez votre propre système](iot-solution-build-system.md) à l’aide de nos scripts Azure Resource Manager. Le [Guide de référence MyDriving](http://aka.ms/mydrivingdocs) montre également les aspects que vous allez le plus personnaliser.

  [à partir de GitHub]: https://github.com/Azure-Samples/MyDriving
  [à l’aide de Xamarin]: https://developer.xamarin.com/guides/ios/getting_started/installation/
  [BAFX Products 34t5 Bluetooth OBDII Scan Tool]: http://www.amazon.com/gp/product/B005NLQAHS
  [ScanTool OBDLink MX Wi-Fi: OBD Adapter/Diagnostic Scanner]: http://www.amazon.com/gp/product/B00OCYXTYY/ref=s9_simh_gw_g263_i1_r?pf_rd_m=ATVPDKIKX0DER&pf_rd_s=desktop-2&pf_rd_r=1MWRMKXK4KK9VYMJ44MP
  [portail HockeyApp]: https://rink.hockeyapp.org
  [problème sur GitHub]: https://github.com/Azure-Samples/MyDriving/issues

<!---HONumber=AcomDC_0406_2016-->