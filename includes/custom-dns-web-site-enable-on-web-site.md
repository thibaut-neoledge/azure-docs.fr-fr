Une fois la propagation des enregistrements de votre nom de domaine terminée, vous devez associer ces enregistrements à votre site web. Procédez comme suit pour activer les noms de domaine à l'aide de votre navigateur Internet.

> [WACOM.NOTE] La propagation de vos enregistrements CNAME, créés à l'étape précédente, dans le système DNS peut prendre du temps. Il n'est pas possible d'ajouter le nom de domaine de votre site web Azure avant la propagation de l'enregistrement CNAME. Si vous utilisez un enregistrement A, vous ne pouvez pas ajouter le nom de domaine de l'enregistrement A à votre site web Azure avant la propagation de l'enregistrement CNAME **awverify** créé à l'étape précédente.
>
> Vous pouvez utiliser un service tel que <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> pour vérifier que l'enregistrement CNAME est disponible.

1.  Dans votre navigateur, ouvrez le [portail de gestion Azure][portail de gestion Azure].

2.  Sous l'onglet **Sites Web**, cliquez sur le nom de votre site, sélectionnez **Tableau de bord**, puis **Gérer les domaines** en bas de la page.

    ![][0]

3.  Utilisez les zones de texte **NOMS DE DOMAINE** pour entrer les noms de domaine à associer avec ce site web.

    ![][1]

4.  Cliquez sur la coche dans le coin inférieur droit pour enregistrer la configuration de nom de domaine.

    Une fois la configuration terminée, le nom de domaine personnalisé est répertorié dans la section **noms de domaines** de la page **Configurer** de votre site web.

Vous devez alors pouvoir entrer le nom de domaine personnalisé dans votre navigateur et constater que vous êtes bien dirigé vers votre site web Azure.

  [portail de gestion Azure]: https://manage.windowsazure.com
  [0]: ./media/custom-dns-web-site/dncmntask-cname-6.png
  [1]: ./media/custom-dns-web-site/dncmntask-cname-7.png
