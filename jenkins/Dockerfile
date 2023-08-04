# Utiliza a imagem oficial do Jenkins LTS (Long-Term Support)
FROM jenkins/jenkins:lts

# Informa que você é o mantenedor desta imagem
# LABEL maintainer="Seu Nome <seu.email@exemplo.com>"

# Copia o arquivo de configuração personalizado (se necessário)
# COPY config.xml /var/jenkins_home/

# Instala os plugins desejados (exemplo com plugins de pipeline)
# RUN /usr/local/bin/install-plugins.sh nome-do-plugin1 nome-do-plugin2

# Expõe a porta 8080 para acesso ao Jenkins via navegador
EXPOSE 8080

# Define um volume para persistir dados do Jenkins
VOLUME ["/jenkins/app"]

# Define o usuário que executará o Jenkins (não como root)
USER jenkins

# Define o diretório de trabalho (o mesmo que você especificou)
WORKDIR /jenkins/app