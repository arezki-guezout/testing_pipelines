# testing_pipelines
Dans ce projet, chaque branche simule une façon de coder son pipeline de test.
tous les fichiers de chaque branche sont des jenkinsfile, ils seront nommés selon le nom du job qui leur serait associé.

Dans ce projet, seuls 3 jobs sont créer, le job de deploiement et celui du test sont parametrés, seul celui de l'orchestration fait appel à ces jobs à tour de rôle en leur passant les bons paramètres.
