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
