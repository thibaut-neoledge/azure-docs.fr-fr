1.  Dans Visual Studio, exécutez l'application cliente et tentez de vous authentifier à l'aide du compte de l'utilisateur Dave créé précédemment.

    ![][]

2.  Dave n'est pas membre du groupe Ventes. Par conséquent, le contrôle d'accès en fonction du rôle refusera l'accès aux opérations de table. Fermez l'application cliente.

    ![][1]

3.  Dans Visual Studio, réexécutez l'application cliente. Cette fois, authentifiez-vous à l'aide du compte de l'utilisateur Bob.

    ![][2]

4.  Bob est membre du groupe Ventes. Par conséquent, le contrôle d'accès en fonction du rôle autorisera l'accès aux opérations de table.

    ![][3]

  []: ./media/mobile-services-aad-rbac-test-app/dave-login.png
  [1]: ./media/mobile-services-aad-rbac-test-app/unauthorized.png
  [2]: ./media/mobile-services-aad-rbac-test-app/bob-login.png
  [3]: ./media/mobile-services-aad-rbac-test-app/success.png
