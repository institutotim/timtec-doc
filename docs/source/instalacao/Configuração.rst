Configuração
============

Dados iniciais
--------------

Crie um super usuário para a aplicação:

``./manage.py createsuperuser``

Se for necessário trocar a senha:

``./manage.py changepassword admin``

Feito isso, você poderá acessar a `interface admin do
django <https://docs.djangoproject.com/en/1.6/ref/contrib/admin/>`__
pelo endereço /django/admin. Exemplo (no browser):

'http://sua-url//django/admin'

Variáveis
---------

A configuração se dá basicamente usando as variáveis definidas no módulo
`settings do
django <https://docs.djangoproject.com/en/1.6/ref/settings/>`__. No caso
do TIM Tec, temos um arquivo settings\_production.py que fica fora do
repositório, para que ele não seja sobrescrito quando a plataforma for
atualizada. Isolamos neste módulo python as variáveis relevantes de
configuração, pois o módulo settings define várias questões sobre o
projeto django, como as apps django usadas.

Após realizar qualquer alteração neste arquivo settings\_production.py,
você deve executar o comando abaixo na raiz do repositório, com o
ambiente virtual ativado, para que as mudanças tenham efeito:

``make update-production``

Se você for um usuário avançado, o arquivo settings.py que contém a
maioria das definições sobre a plataforma fica em timtec/settings.py a
partir da raiz do repositório.

Tema
----

Definição o tema do TIM Tec. Atualmente temos 3 temas:

-  **default**: tema neutro, padrão do software
-  **timtec**: tema usado em timtec.com.br
-  **ifs-colors**: tema desenvolvido para os Institutos Federais de
   Educação, Ciência e Tecnologia
-  **if**: tema desenvolvido para o Institutos Federais de Educação,
   Ciência e Tecnologia

Basta colocar o valor de uma destas 3 strings acima na variável
TIMTEC\_THEME, ex:

``TIMTEC_THEME = 'default'``

Autenticação
------------

O django-allauth é usado para implementar a autenticação, e ele possui
algumas variáveis de configuração. Ele é usado para configurar a
autenticação usando o facebook.

Você pode verificar as variáveis suportadas
`aqui <https://readthedocs.org/projects/django-allauth/>`__

Para configurar a autenticação via facebook, é preciso criar uma app no
facebook, que possui documentação própria
`aqui <https://developers.facebook.com/docs/javascript>`__

Domínios permitidos
-------------------

A Variável
`ALLOWED\_HOSTS <https://docs.djangoproject.com/en/1.6/ref/settings/#allowed-hosts>`__
define para quais domínios a aplicação vai aceitar conexões (requests).
Ex.:

``ALLOWED_HOSTS = [     'localhost',     'timtec.com.br',     '.timtec.com.br', ]``

Se você estiver rodando a aplicação numa rede local e quiser que todos
os ips da rede possam acessar a aplicação, pode colocar isto:

``ALLOWED_HOSTS = ["*", ]``

Configurações de email
----------------------

Definidas principalmente pela variável
`EMAIL\_BACKEND <https://docs.djangoproject.com/en/1.6/ref/settings/#email-backend>`__.
Verifique a documentação do django sobre envio de emails para maiores
detalhes:
https://docs.djangoproject.com/en/1.6/topics/email/#email-backends

Caso a configuração de email esteja incorreta, ocorrerá um erro durante
a criação de usuário.

Configuração da chave da api do youtube
---------------------------------------

A chave da api do youtube serve para acessar informações sobre o Vídeo
durante a criação das Unidades e do vídeo de introdução do curso.

SEM ESTA CHAVE, A ADMINISTRAÇÃO DO CURSO NÃO IRÁ FUNCIONAR
CORRETAMENTE!!! Veja como obter a chave da api do youtube neste link:
https://developers.google.com/youtube/v3/getting-started?hl=pt-br

Após obter a chave, defina a configuração:

``YOUTUBE_API_KEY = 'sua_chave_api_youtube'``
