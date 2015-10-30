Instalação
==========

Essa é a documentação básica de instalação (deploy) TIM Tec. Para
informações sobre requisitos e dependências, veja o
`README.md <https://github.com/hacklabr/timtec/blob/master/README.md>`__.

Ubuntu 14.04 e Debian 7.7 / 8.0
-------------------------------

Para usar o TIM Tec em produção sugerimos uma arquitetura usando o nginx
como servidor web e o
`uwsgi <https://uwsgi-docs.readthedocs.org/en/latest/>`__ como proxy.

Na pasta scripts, há um shell script que executa exatamente os passos a
seguir. Se tiver pressa, olhe os scripts bootstrap-ubuntu.sh e
production-ubuntu.sh.

Sugerimos que o usuário usado para fazer a instalação não seja o root.
Aqui, o usuário que está fazendo a instalação é o **timtec-production**.
Assim, nos comandos abaixo, substitua o nome do usuário pelo que você
estiver usando. Os scripts possuem uma variável para definir o usuário
que executará o proxy (wsgi). Caso use um usuário diferente, olhe o
arquivo Makefile e faça as alterações necessárias.

Este tutorial pressupões que você esteja logado com o usuário
timtec-production, que ele esteja no grupo sudo.

É possível verificar se o usuário timtec-production existe através do
comando:

::

    grep timtec-production /etc/passwd

Caso o usuário exista, o comando acima deve retornar uma linha
semelhante a seguinte:

::

    timtec-production:x:999:999::/home/timtec-production:

Caso não exista é possível criá-lo com:

::

    sudo useradd --groups sudo --create-home --password <uma senha de sua preferência> timtec-production

Se você estiver usando Debian, pode acontecer do sistema criar uma
instância do novo usuário com acesso a um terminal sh. Se você quiser
usar o bash (terminal mais completo e com mais funcionalidades), você
pode alterar essa informação no arquivo de configuração de usuários.
Proceda da seguinte maneira:

1) Abra o arquivo /etc/passwd e verifique a linha onde está o usuário
   timtec-production. Você pode ver uma linha assim:

   timtec-production:x:1001:1001::/home/timtec-production:/bin/sh

2) Repare que a linha tem indicação para o terminal sh. Mude para bash e
   salve o arquivo com a linha desta maneira (use vim, nano ou qualquer
   editor de sua preferência):

   timtec-production:x:1001:1001::/home/timtec-production:/bin/bash

Obtendo o código
----------------

Dentro da home do usuário, primeiro vamos instalar o git e clonar o
repositório

::

    sudo apt-get install git
    git clone https://github.com/hacklabr/timtec.git

Em seguida, escolha a versão desejada e atualize o código para ela com o
comando abaixo. Aqui você encontra uma lista de versões do TIMTec:
https://github.com/hacklabr/timtec/releases

::

    git checkout <tag-da-versão>

Substitua a tag da versão por uma tag do git válida. Ex:
``git checkout v3.0.3``

Dependências
------------

Primeiro, vamos instalar as dependências:

::

    sudo apt-get update
    sudo apt-get install -y git libpq-dev libjpeg-dev libpng12-dev build-essential python-dev gettext python-virtualenv

Instalando o nodejs
-------------------

Ubuntu
------

::

    sudo apt-get install -y nodejs npm

Diferente de todas as outras distribuições, o ubuntu usa o comando node
para o nodejs por padrão, então vamos fazer isso:

::

    sudo update-alternatives --install /usr/bin/node node /usr/bin/nodejs 10

Debian
------

::

    sudo apt-get install curl
    sudo curl -sL https://deb.nodesource.com/setup | bash -
    sudo apt-get install nodejs

Mais informações `neste
link <https://github.com/joyent/node/wiki/installing-node.js-via-package-manager#debian-and-ubuntu-based-linux-distributions>`__

Banco de dados
--------------

Recomendamos o postgreSQL, mas o django suporta outros bancos de dados
relacionais.

::

    sudo apt-get install -y postgresql
    sudo su - postgres -c "createuser -d timtec-production"
    createdb --encoding "UTF-8" --locale "pt_BR.UTF-8" timtec-production

Ambiente virtual python e dependências de javascript
----------------------------------------------------

Crie um arquivo de configuração local. Na raiz da aplicação, execute:

::

    cp -a timtec/settings_local.py.template ../settings_production.py

Edite o arquivo ``settings_production.py`` e procure pelas seções
marcadas com TODO, entenda e preencha os campos faltando. **Sem o
preenchimento correto o sistema não irá funcionar corretamente**.

Este arquivo deve ficar fora do repositório para não ser sobrescrito
durante a atualização do TIM Tec.

Em seguida, vamos criar o ambiente virtual python e instalar as
dependências do python e do nodejs. O comando abaixo automatiza todo
este processo, mas deixamos abaixo a opção manual.

::

    make create-production

O script pode pedir a senha do usuário timtec-production por conta do
sudo. Se ocorrer algum erro, tente rodar o comando make novamente, pois
falhas podem ocorrer devido a problemas com a internet.

Criando ambiente virtual manualmente (opcional, use este ou o make create-production)
-------------------------------------------------------------------------------------

Em seguida, vamos criar o ambiente virtual python:

::

    virtualenv /home/timtec-production/env
    source /home/timtec-production/env/bin/activate

Agora vamos instalar as dependências:

::

    cd timtec
    make

O script pode pedir a senha do usuário timtec-production. Se ocorrer
algum erro, tente rodar o comando make novamente, pois falhas podem
ocorrer devido a problemas com a internet.

Servidor web e de aplicação
---------------------------

Instale o servidor web (nginx) e o servidor de aplicação (uwsgi):

::

    sudo apt-get install -y nginx uwsgi uwsgi-plugin-python

Na pasta timtec/scripts/conf temos exemplo de arquivos de configuração.
Copie os mesmos para seus locais e edite-os confore sua necessidade:

::

    sudo cp ~/timtec/scripts/conf/timtec-production.ini /etc/uwsgi/apps-available

Crie um link simbólico de apps-available para apps-anable:

::

    sudo ln -s /etc/uwsgi/apps-available/timtec-production.ini /etc/uwsgi/apps-enabled/timtec-production.ini

Inicie o serviço do servidor de aplicação:

::

    sudo service uwsgi start

Copie os scritps de configuração da instância timtec-production para os
sites available do nginx:

::

    sudo cp ~/timtec/scripts/conf/nginx-timtec-production /etc/nginx/sites-available/timtec-production

Crie link simbólico do projeto de sites-available para sites-enable:

::

    sudo ln -s /etc/nginx/sites-available/timtec-production /etc/nginx/sites-enabled/timtec-production

Remova o arquivo de configuração padrão do nginx para não haver
conflito:

::

    sudo rm /etc/nginx/sites-enabled/default

Faça um reload do nginx para se certificar que ele está rodando
corretamente:

::

    sudo nginx -s reload
    # se o nginx não estiver rodando, execute: sudo service nginx start

Em seguida, edite o arquivo de configuração do nginx e do django para
colocar seu domínio. No caso do arquivo do django, você deve editar o
arquivo /home/timtec-production/settings\_production.py, procurar a
variável ALLOWED\_HOSTS e definir lá seu domínio. Somente os domínios
listados nesta variável conseguirão acessar a aplicação django (se
quiser acessar local, é preciso adicionar o localhost nesta variável).

Depois de alterar o arquivo settings\_production.py, no diretório da
aplicação:

::

    make update-production

No caso do nginx, após alterar os arquivos de configuração, você deve
executar:

::

    sudo nginx -s reload


