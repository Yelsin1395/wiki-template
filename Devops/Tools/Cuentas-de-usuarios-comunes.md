# Este archivo es altamente confidencial, no publicar

- user: juntozdevops@outlook.com
- pass: junt0zd3v0ps

Este usuario se creo hace tiempo para manejar todo el tema de devops. Sin embargo, no se sabe del todo su alcance completo.

Para lo que si se usa, ya que si tiene acceso a los repositorios es para bajar de Azure Artifacts los paquetes nuget durante los build

---

- user: juntoz.testing@gmail.com
- pass: Prueba1234

Este usuario se puede usar como cuenta de test en los ambientes de staging y produccion.

---

- user: techsupportalerter@juntoz.com
- pass: 12345678

Este usuario es para usarlo como alerta de un email que llegue a techsupport@juntoz.com y que se mueva hacia Slack. Se usa en zapier.

---

- User: tech-bot@ieholding.com
- pass: t3chb0t999

Este usuario fue creado para envio de emails de sonarqube.

---

Usuario administrador de TimeScale Forge

Staging tsdb
- superadmin: tsdbadmin
- password: nvwr0omyv5h1b90d

Production tsdb
- superadmin: tsdbadmin
- password: bgddgep4mrmpmboc

---

PAT para hacer npm install, npm publish, docker push y docker pull. Estos fueron creados por la cuenta katlim@ieholding.com

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 org_build_deploy <<< npm install, npm publish, docker push

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 org_pull_docker <<<< docker pull, instalado como secret dentro de los cluster kubernetes
