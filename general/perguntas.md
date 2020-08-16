# Perguntas

## Aula 1

1 - Atualmente estamos recebendo fortes críticas de nossos clientes, devido a baixa qualidade e a demora na entrega dos softwares que desenvolvemos aqui no bytebank. Um dos motivos da baixa qualidade, é não verificarmos se todos os testes unitários estão passando com êxito antes de enviarmos os códigos para o ambiente do cliente. Com a integração contínua, descobrimos que é possível testar uma nova funcionalidade em um ambiente semelhante ao de produção, e só publicá-la depois que todos os testes passarem. Utilizando a ferramenta GITLAB, qual o arquivo responsável por configurar essa automatização e onde ele deve ser criado?

- __O arquivo responsável por configurar todas as etapas da integração contínua é o .gitlab-ci.yml, que deve se criado na raiz do seu projeto, tendo como base uma indentação par. Caso essa regra seja quebrada, a integração não funcionará.__
> Correto! Pois é dentro desse arquivo que podemos configurar um pipeline de CI/CD.
- Podemos criar um arquivo chamado .gitlab-ci.yml em qualquer parte do projeto e não versioná-lo
- Não precisamos criar nenhum arquivo para tal configuração, pois o gitlab já se encarrega de executar as tarefas necessárias.

## Aula 2

1 - Atualmente, no Bytebank, versionamos nosso projeto no github. Para podermos utilizar a ferramenta de integração contínua do gitlab, precisamos que nosso projeto seja versionado também com o gitlab. Pensando nisso, como podemos apontar o versionamento atual para o gitlab?

- A
Como o projeto já está versionado com git, o correto seria apagar o arquivo oculto .git que fica na raiz do projeto, e iniciar um novo versionamento com o gitlab
```bash
rm -rf .git
git remote add origin https:gitlab.com/meuusuario/meuprojeto.git
```

- B
Como o projeto já está versionado com o github, não é possível fazer um novo apontamento de repositório remoto.

- __C__
Podemos adicionar um novo endereço remoto, apontando diretamente para um projeto no gitlab. Caso precise manter o alias "origin" para o versionamento antigo, podemos criar um alias novo e manter os dois endereços ativos.
```bash
git remote add meualias https://gitlab.com/meuusuario
```
> Correto! Dessa forma, além de versionar com gitlab, não precisamos nos desfazer do versionamento com github.

## Aula 3

1 - Ao implementar a integração contínua em nosso processo de desenvolvimento, conseguimos diminuir consideravelmente os problemas que tínhamos em relação às divergências entre os ambientes de produção e desenvolvimento. Implementamos na pipeline do gitlab uma tarefa de atualização da imagem Docker e publicação na nuvem, dessa forma sempre que a imagem é alterada, automaticamente atualizamos a que está no Docker hub. Para atualizarmos a imagem no docker hub, utilizamos os comandos docker pull e docker push. Nessa mesma ordem, para que serve os comandos docker pull e docker push?

- A
O comando docker pull é utilizado para criação das tags de versionamento do docker hub, e o docker push utilizamos para realizar um build da imagem docker.
```bash
docker pull minha-imagem:1.0.0 minha:imagem:1.0.0
docker push -t minha-imagem .
```

- B
O comando docker pull é utilizado para baixar uma imagem de um repositório remoto, e o docker push utilizamos para criar um container com todo nosso projeto para nuvem.

- __C__
O comando docker pull é utilizado para baixar uma imagem de um repositório remoto, e o docker push utilizamos para enviar uma imagem ao repositório remoto. Para utilizarmos os dois comandos, precisamos sempre informar o nome da imagem docker que estamos manipulando.
```bash
docker pull minha-imagem:latest || true
docker push jnlucas/minha-imagem:latest
```
> Correto! Desta forma, sempre que quisermos atualizar nosso ambiente local, utilizamos o docker pull; e sempre que quisermos atualizar nosso ambiente remoto, utilizamos o comando docker push.

## Aula 4

1 - Para garantirmos que todos os testes sejam executados em um ambiente semelhante ao de produção, precisamos garantir também que o executor de tarefas seja uma máquina igual a de produção, onde podemos configurar as variáveis de conexão e conexão com outros servidores remotos. Como esses dados são dados sensíveis a nosso negócio, para garantirmos a segurança entre esses ambientes, vamos configurar nosso próprio executor de tarefas utilizando o gitlab-runner. Com isso, além de garantir uma paridade entre os ambientes, garantimos também a segurança dos dados nesse executor. Após a criação desse novo runner, como podemos informar para os jobs dentro do pipeline qual executor deve pegar a tarefa?

- A
Dentro do pipeline, devemos utilizar a palavra reservada “dependencies”, assim colocamos uma dependência para esse runner
```yml
dependencies:
  - build-docker
```

- __B__
Dentro do pipeline, devemos utilizar a palavra reservada “tags” e nela informar o nome da tag que configuramos dentro do gitlab-runner
```yml
tags:
  - executor-tarefas
```
> Correto!, Dessa forma, o job será executado pelo runner que tenha a mesma tag informada.

- C
Dentro do pipeline, podemos adicionar na palavra reservada “stage” o nome do executor de tarefas.
```yml
stage: build
```

## Aula 5

1 - Um passo importante para a integração contínua, é o passo dos testes, pois é hora de saber se no decorrer do desenvolvimento de uma tarefa nada quebrou, ou seja, se algo que funcionava não deixou de funcionar. Porém de nada adianta os testes passarem se não conseguimos gerar um build de nossa aplicação. Com isso em mente, como podemos garantir que o passo de testes só aconteça após o build do projeto ser gerado com sucesso?

- __A__
Criando uma dependência entre tarefas no passo de testes. Podemos colocar essa dependência com o passo anterior, e dessa forma os testes só serão executados se o build estiver concluído com sucesso.
```yml
test-project:
  dependencies:
  - build-project
```
> Correto! Dessa forma, só executaremos os testes se o passo de build for executado antes com sucesso.

- B
Para criar uma dependência, basta colocar as duas tarefas no mesmo stage.
```yml
build-project:
  image: jnlucas/minha-imagem:latest
  stage: test
  tags:
  - executor-tarefas
  script:
  - python manage.py makemigrations
  - python manage.py migrate

test-project:
  image: jnlucas/minha-imagem:latest
  stage: test
```

- C
Não é possível fazer essa configuração no pipeline, pois todas as tarefas devem ser executadas isoladamente.

## Aula 6

1 - Para realizarmos um deploy da aplicação após a execução dos testes unitários, precisaremos criar mais um passo dentro da pipeline que garanta tal tarefa. Porém, para que esse passo funcione, precisamos garantir acesso ao servidor remoto, para que a entrega do software aconteça nesse ambiente. Como podemos garantir acesso ao servidor remoto para que o deploy aconteça?

- Podemos criar um par de chaves pública e privada entre o runner e o servidor de homologação com o comando “ssh-keygen”, colocando a chave privada gerada no ambiente de homologação.
- __Criando um par de chaves (pública e privada) no runner com o comando “ssh-keygen”, e em seguida disponibilizamos a chave pública no servidor de homologação.__
> Correto! Dessa forma ao gerarmos um par de chaves, podemos adicionar a chave pública no servidor de homologação e garantir o acesso do executor de tarefas a essa máquina.
- Para esse passo, o correto é realizar o deploy manualmente, pois não há maneiras de conceder acesso a outras máquinas.

## Aula 7

1 - Com a necessidade de melhorarmos a comunicação entre todos os envolvidos no projeto que estamos desenvolvendo no bytebank, precisamos que todos sejam informados sempre que uma entrega aconteça. Para isso, utilizaremos uma ferramenta chamada slack. Dentro da pipeline de integração contínua, sempre que algo aconteça de errado no processo de integração contínua, teremos que adicionar notificações de falhas ao slack. Para isso, vamos adicionar um job que cuide das notificações, mas como podemos garantir que essa notificação aconteça somente quando o pipeline falhar?

- __A__
Adicionando a tarefa a palavra reservada “when” ao job e definindo “on_failure” como parâmetro.
```yml
notificacao-falhas:
  stage: notificacao
  tags:
  - executor-deploy
  when: on_failure
  script:
  - echo sh notificacaoFalha.sh
```
> Correto! Dessa forma, esse job só será executado caso aconteça algum problema nos outros passos da pipeline.

- B
Adicionando a palavra reservada “when” ao job e definindo “always” como parâmetro.
```yml
notificacao-falhas:
  stage: notificacao
  tags:
  - executor-deploy
  when: always
  script:
  - echo sh notificacaoFalha.sh
```

- C
Adicionando a palavra reservada “when” ao job e definindo “on_success” como parâmetro.
```yml
notificacao-falhas:
  stage: notificacao
  tags:
  - executor-deploy
  when: on_success
  script:
  - echo sh notificacaoFalha.sh
```
