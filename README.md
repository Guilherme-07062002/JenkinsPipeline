# Jenkins & Docker - CI / CD

## O que é integração contínua?

**CI** (Integração Contínua) e **CD** (Entrega Contínua ou Implantação Contínua) são práticas do desenvolvimento de software que buscam automatizar e agilizar o processo de entrega de código aos usuários finais.

* **Integração Contínua (CI)**: É a prática de mesclar, validar e testar o código frequentemente em um repositório compartilhado. Isso significa que toda vez que um desenvolvedor faz uma alteração no código-fonte, essa alteração é integrada ao repositório compartilhado, onde várias etapas automatizadas são executadas, incluindo compilação, execução de testes automatizados e análise de código. O objetivo é detectar e corrigir problemas o mais cedo possível, garantindo que o código seja sempre funcional e seguro. O Jenkins é uma ferramenta popular para a implementação de CI.
* **Entrega Contínua (CD)**: É uma extensão da integração contínua e implica que, após a validação dos testes na fase de CI, o software está pronto para ser entregue ao ambiente de produção. A entrega contínua automatiza o processo de implantação e liberação de código em ambientes de teste e produção, permitindo que novas versões do software sejam entregues aos usuários de forma rápida, segura e confiável. A entrega contínua enfatiza a automação de processos e a manutenção de ambientes de teste que são o mais próximo possível do ambiente de produção. Ela busca minimizar o risco associado a novas implantações e garantir que o software esteja sempre pronto para ser liberado ao cliente.

## Pipeline

![ilustração integração contínua](imgs/integracao-continua.png)

Um pipeline é uma sequência automatizada de etapas que define a construção, testes e implantação contínua de um software. Em um contexto de desenvolvimento de software e integração contínua, um pipeline representa o fluxo de trabalho completo, desde o momento em que o código-fonte é alterado até a implantação em um ambiente de produção.

O conceito de pipeline ganhou destaque com a adoção de práticas ágeis e DevOps, onde a automação é fundamental para garantir a qualidade do software, agilizar o processo de desenvolvimento e reduzir erros humanos.

Um pipeline pode ser composto por várias etapas, como:

* Compilação: O código-fonte é compilado e transformado em um artefato executável ou em algum formato que será usado posteriormente.

* Testes: São executados testes automatizados, como testes unitários, testes de integração e testes de aceitação, para garantir que o software funcione corretamente e não tenha introduzido regressões.

* Análise de código: Pode incluir análises estáticas do código para verificar a qualidade, segurança e boas práticas de codificação.

* Implantação: O artefato construído é implantado em um ambiente de teste ou de produção, onde é executado e testado em um ambiente real.

* Monitoramento: Acompanhamento do desempenho e comportamento do software em tempo real, para identificar problemas e anomalias.

* Notificação e Feedback: O pipeline pode ser configurado para enviar notificações sobre o progresso, resultados de testes ou erros encontrados.

O **Jenkins** é uma das ferramentas mais populares para a criação e execução de pipelines de integração contínua. Ele permite configurar pipelines usando uma linguagem de script ou por meio de uma interface visual, facilitando a automação do fluxo de trabalho de desenvolvimento.

O conceito de pipeline permite que as equipes de desenvolvimento e operações colaborem de maneira mais eficiente, tornando o processo de entrega de software mais previsível, confiável e automatizado. Isso possibilita a entrega rápida e contínua de novas funcionalidades aos usuários finais e ajuda a manter a qualidade do software em todas as etapas do desenvolvimento.

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

## Gerando par de chaves SSH

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

## Configurando Jenkins

Na página inicial vá em Gerenciar Jenkins -> Credentials -> System -> Global credentials -> Add credentials

O tipo (kind) da credêncial é SSH Username with private key

Insira um ID qualquer como por exemplo: github-ssh

Forneça um descrição

E em usuário, insira o usuário do github que é sempre **git**, não precisa ser seu usuário pois a conexão será realizada por meio de chave ssh.

Em private key selecione **enter directly** e **add**.

Após isso acesse sua chave privada, ela pode ser acessada no diretorio **.ssh/** mas diferentemente da chave pública, ela é gravada no arquivo **id_rsa**. Por meio dela que o Jenkins poderá interagir com o seu Github.

Copie todo o conteúdo do arquivo e o insira no campo.

Após isso, clique em **Create**.

## Criando job

Vá para a página inicial e clique em **Novo Job / Nova Tarefa**

Dê um nome ao seu job, selecione o seu tipo e clique em prosseguir.

## Gerenciando job

Em gerenciamento de código fonte selecione Git, copie a ssh do seu repositório e insira-o no campo.

Em credenciais selecione o 'github-ssh' que você criou anteriormente.

Selecione a branch principal do seu repositório 'main' ou 'master'.

Em Gatilho de disparo para construções selecione **consultar periodicamente o SCM**, esse campo define o intervalo em que será feita a verificação no repositório em busca de alterações.

Por exemplo, inserindo `H * * * *` será realizada uma verificação a cada hora, e com `* * * * *` isso será feito a cada minuto.

Em Ambiente de Build/Construção selecione **delete workspace before build starts**. Isso limpa o workspace, que é um diretório que será criado dentro do jenkins antes de fazer a configuração.

Após isso clique em Salvar.

## Adicionando passos de build

Um "passo de build" é uma tarefa específica realizada durante o processo de construção de um software ou aplicação. São etapas que transformam o código-fonte em um artefato executável, incluindo compilação, testes, empacotamento e outras ações necessárias para preparar o software para implantação.

Para configurar, vá no seu job em configurações -> Passos de build/construção.

Existem várias opções como por exemplo, executar um shell após cada build, realizar testes, etc.

Existem vários plugins que você pode adicionar ao jenkins para aumentar a variedade de coisas que poderão ser feitas quando for realizado um build.
