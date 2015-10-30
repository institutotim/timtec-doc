Atualizando o TIMTec para uma nova versão
=========================================

As versões do TIMTec estão disponíveis aqui:
https://github.com/hacklabr/timtec/releases

Cada versão do TIMTec é uma tag no repositório git.

O primeiro passo é atualizar o código. Dentro da raiz do repositório,
execute:

``git pull`` ``git checkout <versao>``

Onde a versão deve ser substítuido pela tag da versão desejada.
Exemplos: ``git checkout v3.0.3``.

Feito isso, `ative o ambiente virtual
python <https://github.com/hacklabr/timtec/wiki/Instala%C3%A7%C3%A3o#criando-ambiente-virtual-manualmente-opcional-use-este-ou-o-make-create-production>`__
e em seguida faça:

make update-production

Feito isso, o software estará atualizado.

Fique atento se a nova versão não possui necessidade de alguma
configuração adicional. A `versão
3.0.2 <https://github.com/hacklabr/timtec/releases/tag/v3.0.2>`__, por
exemplo, exige que seja `configurado a variável
YOUTUBE\_API\_KEY <https://github.com/hacklabr/timtec/wiki/Configura%C3%A7%C3%A3o#configura%C3%A7%C3%A3o-da-chave-da-api-do-youtube>`__
