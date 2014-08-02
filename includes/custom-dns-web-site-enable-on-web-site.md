
Une fois la propagation des enregistrements de votre nom de domaine terminée, vous devez associer ces enregistrements à votre site. Procédez comme suit pour activer les noms de domaine à l'aide de votre navigateur Internet.

> [WACOM.NOTE] La propagation de vos enregistrements CNAME, créés à l'étape précédente, dans le système DNS peut prendre du temps. Il n'est pas possible d'ajouter le nom de domaine de votre site Web Azure avant la propagation de l'enregistrement CNAME. Si vous utilisez un enregistrement A, vous ne pouvez pas ajouter le nom de domaine A à votre site Web Azure avant propagation des enregistrements CNAME **awverify.www** ou **www** créés à l'étape précédente.
>
> Vous pouvez utiliser un service tel que <http://www.digwebinterface.com/> pour vérifier que l'enregistrement CNAME est disponible.

1.  Dans votre navigateur, ouvrez le [portail de gestion Azure](https://manage.windowsazure.com).

2.  Dans l'onglet **Web Sites**, cliquez sur le nom de votre site, sélectionnez **Dashboard**, puis **Manage Domains** en bas de la page.

    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3.  Utilisez les zones de texte **DOMAIN NAMES** pour entrer les noms de domaine à associer avec ce site Web.

    ![](./media/custom-dns-web-site/dncmntask-cname-7.png)

4.  Cliquez sur la coche dans le coin inférieur droit pour enregistrer la configuration de nom de domaine.

    Une fois la configuration terminée, le nom de domaine personnalisé est répertorié dans la section **domain names** de la page **Configure** de votre site Web.

Vous devez alors pouvoir entrer le nom de domaine personnalisé dans votre navigateur et constater que vous êtes bien dirigé vers votre site Web Azure.

