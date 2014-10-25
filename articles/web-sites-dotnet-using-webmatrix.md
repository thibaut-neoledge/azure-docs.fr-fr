<properties linkid="develop-dotnet-website-with-webmatrix" urlDisplayName="Website with WebMatrix" pageTitle=".NET website with WebMatrix - Azure tutorials" metaKeywords="WebMatrix Azure, WebMatrix Azure, Azure web site WebMatrix, Azure website WebMatrix, Web Matrix Azure, WebMatrix Azure" description="Learn how to develop and deploy an Azure website with WebMatrix." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Develop and deploy a website with Microsoft WebMatrix" authors="cephalin" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin"></tags>

# Développer et déployer un site web avec Microsoft WebMatrix

Ce guide explique comment utiliser Microsoft WebMatrix pour créer et déployer un site web sur Azure. Vous allez utiliser un exemple d'application à partir d'un modèle de site WebMatrix.

Vous apprendrez à effectuer les opérations suivantes :

-   vous connecter à Azure à partir de WebMatrix ;
-   créer un site à l'aide d'un modèle intégré avec WebMatrix ;
-   déployer le site web personnalisé directement à partir de WebMatrix sur Azure.

[WACOM.INCLUDE [create-account-and-websites-note][]]

## Connexion à Azure

1.  Lancez WebMatrix.
2.  Si vous utilisez WebMatrix 3 pour la première fois, vous êtes invité à vous connecter à Azure. Sinon, vous pouvez cliquer sur le bouton **Se connecter**, puis choisir **Ajouter un compte**. Choisissez **Se connecter** à l'aide de votre compte Microsoft.

    ![Ajouter un compte][]

3.  Si vous disposez d'un compte Azure, vous pouvez vous connecter avec votre compte Microsoft :

    ![Connexion à Azure][]

## Création d'un site à l'aide d'un modèle intégré pour Azure

1.  Sur l'écran d'accueil, cliquez sur le bouton **Nouveau**, puis choisissez **Galerie de modèles** pour créer un site à partir de la galerie de modèles :

    ![Nouveau site à partir de la galerie de modèles][]

2.  La galerie de modèles affiche une liste des modèles disponibles pouvant s'exécuter en local ou sur Azure. Sélectionnez **Bakery** dans la liste de modèles, entrez **bakerysample** pour le **Nom du site**, puis cliquez sur **Suivant**.

    ![Créer un site à partir d'un modèle][]

3.  Si vous êtes connecté à Azure, vous pouvez maintenant créer un site web Azure pour votre site web local. Choisissez un nom unique, puis sélectionnez le centre de données sur lequel vous voulez que votre site soit créé :

    ![Créer un site sur Azure][]

    Une fois que WebMatrix a fini de créer le site web, l'environnement de développement intégré (IDE) WebMatrix s'affiche :

    ![IDE WebMatrix][]

## Tester le site web

L'exemple « bakery » inclut un formulaire de commande simulée qui envoie un message électronique avec l'article commandé à un compte Windows Live Hotmail que vous avez fourni.

1.  Dans le volet de navigation gauche de WebMatrix, développez le dossier **bakerysample**.

    ![][]

2.  Ouvrez la page *Order.cshtml* en double-cliquant sur le nom du fichier.

    ![][1]

3.  Recherchez le commentaire //Configuration SMTP pour Hotmail.

    ![][2]

4.  Modifiez les valeurs des lignes suivantes pour qu'elles correspondent à votre propre fournisseur de messagerie :

        WebMail.SmtpServer = "smtp.live.com";
        WebMail.SmtpPort  = 25;
        WebMail.EnableSsl = true; 

        //Enter your Hotmail credentials for UserName/Password and a "From" address for the e-mail
        WebMail.UserName = "";
        WebMail.Password = "";
        WebMail.From = "";

    Remplacez la valeur de WebMail.SmtpServer par le nom du serveur de messagerie que vous utilisez normalement pour envoyer des courriers électroniques. Entrez ensuite les valeurs de nom d'utilisateur et de mot de passe. Définissez la propriété From sur votre adresse de messagerie.

5.  Sur le ruban WebMatrix, cliquez sur **Exécuter** pour tester le site.

    ![][3]

6.  Cliquez sur **Order Now** sur l'un des produits et envoyez-vous une commande.

7.  Vérifiez vos courriers électroniques et assurez-vous de recevoir la confirmation de la commande. Si vous rencontrez des difficultés avec l'envoi du courrier électronique, consultez la section [Problèmes avec l'envoi de courrier électronique][] du guide de résolution des problèmes ASP.NET Web Pages (Razor).

## Déployer le site web personnalisé à partir de WebMatrix sur Azure

1.  Dans WebMatrix, cliquez sur **Publier** dans le ruban **Accueil** pour afficher la boîte de dialogue **Publier l'aperçu** du site web.

    ![WebMatrix Publier l'aperçu][]

2.  Cliquez pour activer la case à cocher en regard de bakery.sdf, puis cliquez sur **Continuer**. Une fois la publication terminée, l'URL du site web mis à jour sur Azure s'affiche en bas de l'IDE WebMatrix.

    ![Publication terminée][]

3.  Cliquez sur le lien pour ouvrir le site web dans votre navigateur :

    ![Exemple de site Bakery][]

    Vous pouvez également trouver l'URL du site web dans le portail Azure en cliquant sur **Sites Web** pour afficher tous les sites web de votre abonnement. L'URL de chaque site web s'affiche dans la colonne URL de la page des sites web.

## Modifier le site web et le republier sur le site web Azure

Vous pouvez utiliser WebMatrix pour changer le site et le republier sur votre site web Azure. Dans la procédure suivante, vous allez ajouter une case à cocher pour indiquer si la commande est un cadeau.

1.  Ouvrez la page *Order.cshtml*.

2.  Recherchez la définition de formulaire de la classe « shiping ». Insérez le code suivant juste après le bloc \<li\>.

        <li class="gift">
            <div class="fieldcontainer" data-role="fieldcontain">
                <label for="isGift">This is a gift</label>           
                <div>@Html.CheckBox("isGift")</div>
            </div>
        </li>

    ![][4]

3.  Recherchez la ligne « var shipping = Request["orderShipping"]; » dans le fichier et insérez la ligne de code suivante juste après.

        var gift = Request["isGift"];

4.  Juste après le bloc de code qui valide que l'adresse d'expédition n'est pas vide dans l'extrait de code suivant.

        if(gift != null) {
            body += "This is a gift." + "<br/>";
        }

    Votre fichier *order.cshtml* doit ressembler à l'image suivante.

    ![][5]

5.  Enregistrez le fichier, puis exécutez le site en local et envoyez-vous une commande test. Pensez à tester la nouvelle case à cocher.

    ![][6]

6.  Republiez le site en cliquant sur **Publier** sur le ruban **Accueil**.

7.  Dans la boîte de dialogue **Publier l'aperçu**, vérifiez que les deux fichiers Order.cshtml sont activés, puis cliquez sur Continuer.

8.  Cliquez sur le lien pour ouvrir le site web dans votre navigateur et tester la mise à jour sur votre site web Azure.

# Étapes suivantes

Vous savez désormais comment créer et déployer un site web de WebMatrix vers Azure. Pour en savoir plus sur WebMatrix, consultez les ressources suivantes :

-   [WebMatrix pour Azure][]

-   [Site Web WebMatrix][]

  [create-account-and-websites-note]: ../includes/create-account-and-websites-note.md
  [Ajouter un compte]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-add-account.png
  [Connexion à Azure]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-sign-in.png
  [Nouveau site à partir de la galerie de modèles]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template.png
  [Créer un site à partir d'un modèle]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template-details.png
  [Créer un site sur Azure]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template-azure.png
  [IDE WebMatrix]: ./media/web-sites-dotnet-using-webmatrix/howtowebmatrixide.png
  []: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-1.png
  [1]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-2.png
  [2]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-3.png
  [3]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-4.png
  [Problèmes avec l'envoi de courrier électronique]: http://go.microsoft.com/fwlink/?LinkId=253001#email
  [WebMatrix Publier l'aperçu]: ./media/web-sites-dotnet-using-webmatrix/howtopublishpreview.png
  [Publication terminée]: ./media/web-sites-dotnet-using-webmatrix/howtopublished2.png
  [Exemple de site Bakery]: ./media/web-sites-dotnet-using-webmatrix/howtobakerysamplesite.png
  [4]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-5.png
  [5]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-6.png
  [6]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-7.png
  [WebMatrix pour Azure]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409
  [Site Web WebMatrix]: http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398
