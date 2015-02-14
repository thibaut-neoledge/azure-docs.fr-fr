
Les instructions et les captures d'écran ci-dessous s'appliquent au test d'un client Windows Store, mais vous pouvez le tester sur n'importe quelle autre plateforme prise en charge par Azure Mobile Services. 

1. Dans Visual Studio, exécutez l'application cliente et tentez de vous authentifier à l'aide du compte de l'utilisateur Dave créé précédemment. 

    ![](./media/mobile-services-aad-rbac-test-app/dave-login.png)

2. Dave n'est pas membre du groupe Ventes. Par conséquent, le contrôle d'accès en fonction du rôle refusera l'accès aux opérations de table. Fermez l'application cliente.

    ![](./media/mobile-services-aad-rbac-test-app/unauthorized.png)

3. Dans Visual Studio, réexécutez l'application cliente. Cette fois, authentifiez-vous à l'aide du compte de l'utilisateur Bob.

    ![](./media/mobile-services-aad-rbac-test-app/bob-login.png)

4. Bob est membre du groupe Ventes. Par conséquent, le contrôle d'accès en fonction du rôle autorisera l'accès aux opérations de table.

    ![](./media/mobile-services-aad-rbac-test-app/success.png)



<!--HONumber=42-->
