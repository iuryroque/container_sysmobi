# Use a imagem base do Ubuntu
FROM ubuntu

# Defina a constante NOME_CONTEXTO usando ARG
ARG NOME_CONTEXTO=sysmobi

# Atualize a lista de pacotes e instale o SSH, o nano, o wget e o JRE
RUN apt-get update && \
    apt-get install -y openssh-server nano wget openjdk-8-jre && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Configurar acesso root com senha
RUN echo 'root:rootpassword' | chpasswd

# Criar o diretório de privilege separation
RUN mkdir /run/sshd

# Permitir login como root via SSH com senha
RUN sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config

# Baixar e configurar o Apache Tomcat 8
RUN wget https://archive.apache.org/dist/tomcat/tomcat-8/v8.5.72/bin/apache-tomcat-8.5.72.tar.gz -O /tmp/tomcat.tar.gz && \
    mkdir /opt/tomcat && \
    tar xvzf /tmp/tomcat.tar.gz -C /opt/tomcat --strip-components=1 && \
    rm /tmp/tomcat.tar.gz

# Criar o diretório /opt/webrun e configurar a variável de ambiente WEBRUN_HOME
# RUN mkdir /opt/tomcat/settings && \
#   mkdir /opt/tomcat/settings/systems && \
#   mkdir /opt/tomcat/settings/systems/${NOME_CONTEXTO}

# Copia a partir da pasta files para a pasta tomcat do container
COPY files/ /opt/tomcat/

# Move .war para a webapps
RUN mv /opt/tomcat/${NOME_CONTEXTO}.war /opt/tomcat/webapps/

# Cria a variavel de ambiente para o webrun 
ENV WEBRUN_HOME /opt/tomcat/settings/

WORKDIR /opt/

# Expor as portas 8080 e 22 do Tomcat e SSH
EXPOSE 8080 22

# Iniciar o serviço SSH e o Tomcat como processos de fundo durante o startup do container
CMD service ssh start && /opt/tomcat/bin/catalina.sh run
