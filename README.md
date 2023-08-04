# Jenkins / Docker - Pipeline de Entrega Contínua

## O que é integração contínua?

![ilustração integração contínua](imgs/integracao-continua.png)

Ela é utilizada para solucionar alguns problemas antigos do desenvolvimento de software, por exemplo:

Os times de desenvolvimento quando vão mesclar o código com a branch principal, jogam as suas alterações "por cima do muro".

Mudanças que precisam ser revisadas e aprovadas, testes que precisam ser realizados por outras integrantes da equipe, tudo isso gera um gargalo, retardando o ciclo de desenvolvimento.

A *integração contínua* automatiza esse processo, e a ferramenta mais utilizada para isso é o **Jenkins**.

## Primeiros passos

Acesse a pasta jenkins:

```bash
cd jenkins
```

Forneça a permissão de execução ao script:

```bash
chmod +x jenkins
```

Para construir a imagem Docker do Jenkins:

```bash
./jenkins build
```

Inicialize o container:

```bash
./jenkins run
```

Após isso, para visualizar a interface do jenkins, acesse a rota `localhost:8080` no navegador.
