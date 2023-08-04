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

Após isso, para visualizar a interface do jenkins, acesse a rota [http://localhost:8080](http://localhost:8080) no navegador.

## Criando job no Jenkins

### Gerando par de chaves SSH

Depois que você já tiver o código-fonte da sua aplicação será necessário gerar um par de chaves SSH para conexão com o github.

Para criar o par de chaves SSH, rode o terminal interativo do container:

```bash
./jenkins bash
```

```bash
ssh-keygen -t rsa -b 4096 -C "seuemailgithub@email.com"
```

Seu par de chaves será salvo em: `/var/jenkins_home/.ssh/`

Certifique-se de que o seu repositório local já está previamente configurado com as suas credenciais:

```bash
git config user.name nomeContaGithub
git config user.email seuemailgithub@email.com
```

Depois que você tiver gerado o par de chaves SSH, será preciso passá-las para o github:

Vá em: settings -> SSH and GPG keys -> new SSH key

E forneça a sua chave pública, ela por padrão é salva no diretório **.ssh/** em um arquivo **id_rsa.pub**

Para verificar se deu certo, execute:

```bash
ssh -T git@github.com
```

Se tudo correu bem será exibida uma mensagem dizendo que você foi autenticado com sucesso.

### Configurando Jenkins

Na página inicial vá em Gerenciar Jenkins -> Credentials -> System -> Global credentials -> Add credentials

O tipo (kind) da credêncial é SSH Username with private key

Insira um ID qualquer como por exemplo: github-ssh

Forneça um descrição

E em usuário, insira o usuário do github que é sempre **git**, não precisa ser seu usuário pois a conexão será realizada por meio de chave ssh.

Em private key selecione **enter directly** e **add**.

Após isso acesse sua chave privada, ela pode ser acessada no diretorio **.ssh/** mas diferentemente da chave pública, ela é gravada no arquivo **id_rsa**. Por meio dela que o Jenkins poderá interagir com o seu Github.

Copie todo o conteúdo do arquivo e o insira no campo.

Após isso, clique em **Create**.

### Criando job

Vá para a página inicial e clique em **Novo Job / Nova Tarefa**

Dê um nome ao seu job, selecione o seu tipo e clique em prosseguir.

### Gerenciando job

Em gerenciamento de código fonte selecione Git, copie a ssh do seu repositório e insira-o no campo.

Em credenciais selecione o 'github-ssh' que você criou anteriormente.

Selecione a branch principal do seu repositório 'main' ou 'master'.

Em Gatilho de disparo para construções selecione **consultar periodicamente o SCM**, esse campo define o intervalo em que será feita a verificação no repositório em busca de alterações.

Por exemplo, inserindo `H * * * *` será verificado a cada hora, e `* * * * *` será verificado a cada minuto.

Em Ambiente de Build/Construção selecione **delete workspace before build starts**. Isso limpa o workspace, que é um diretório que será criado dentro do jenkins antes de fazer a configuração.

Após isso clique em Salvar.
