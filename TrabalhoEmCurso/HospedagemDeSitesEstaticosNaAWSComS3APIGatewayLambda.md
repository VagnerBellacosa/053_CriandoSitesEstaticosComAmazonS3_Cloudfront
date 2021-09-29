# Hospedagem de Sites Estáticos Na AWS Com s3, API Gateway e Lambda

[![AWS User Group São Paulo](https://miro.medium.com/fit/c/96/96/2*I-upu5nNo0neBzgSbic0VQ.png)](https://medium.com/@awsusergroupsp?source=post_page-----ef5ccdcc3311--------------------------------)

[AWS User Group São Paulo](https://medium.com/@awsusergroupsp?source=post_page-----ef5ccdcc3311--------------------------------)Follow

[Aug 7, 2017](https://medium.com/awsugsp/hospedagem-de-sites-estáticos-na-aws-com-s3-api-gateway-e-lambda-ef5ccdcc3311?source=post_page-----ef5ccdcc3311--------------------------------) · 9 min read

Tradicionalmente hospedagem de websites são feitas em servidores que necessitam atenção à escalabilidade e disponibilidade, além dos custos com manutenção. Com o s3 é possível hospedar websites com baixo custo e livre destas preocupações.

# Introdução

A hospedagem de sites corporativos tradicionalmente utiliza um servidor físico ou virtual, dedicado ou compartilhado, e algum software que irá servir os recursos do site. Tanto servidor quanto software são recursos que precisam ser monitorados e atualizados, sem falar no risco de indisponibilidade.

Para o site corporativo da [TDSiS](https://tdsis.com.br/) utilizamos uma abordagem serverless utilizando recursos e serviços da AWS (S3, API Gateway, Lambda) que não precisam de servidores e conseguem com baixo custo ter alta disponibilidade e escalabilidade.

# Por que usar o s3 para hospedar websites estáticos?

> *“*[*O Amazon s3*](https://aws.amazon.com/pt/s3/details) *armazena dados como objetos dentro de recursos denominados “buckets”. O bucket permite armazenar quantos objetos você quiser, bem como gravar, ler e excluir objetos.”*

Porém o uso do s3 pode ir muito além de um simples serviço de storage de objetos. Podemos utilizá-lo para hospedagem de websites estáticos e como integrá-lo com outras ferramentas da AWS como API Gateway e Lambda.

Utilizando o s3 com esta finalidade é possível obter diversos ganhos e facilidades que não existem ou seriam mais trabalhosos de realizar no modelo tradicional de webhosting:

- [Altíssima disponibilidade (99,99% uptime)](https://aws.amazon.com/pt/s3/sla/)
- Escalabilidade
- Possibilidade de integração com Cloudfront para certificados e cache
- Controle de acesso (autorização de acesso por ips, por exemplo)
- Regras de redirecionamento por objetos
- Réplica entre regiões (redução de latência)
- Versionamento de arquivos
- Pague de acordo com o uso

Caso o website necessite de algum processamento em back-end, como por exemplo formulário de contato, é possível fazê-lo utilizando API Gateway e Lambda. Nos casos em que as páginas do website são montadas por scripts php (Wordpress e Joomla por exemplo), python ou afins, o uso do s3 para hospedá-lo não é uma opção viável, até porque não são websites estáticos.

# Hands On

Seguindo os passos abaixo, vamos criar um simples bucket hospedando um website estático que contém o arquivo index.html como entry point.

1. Acessar a página do S3 no console da AWS
2. Clicar em create bucket

![img](https://miro.medium.com/max/60/0*bAmZKfFg2aEPhGBx.png?q=20)

![img](https://miro.medium.com/max/630/0*bAmZKfFg2aEPhGBx.png)

\3. Na tela de Name and region devemos definir o nome do bucket (nome de bucket é único em toda a AWS) e região. Clicar em next

- Se o website em questão precisar de um CNAME no DNS que aponte para o bucket, o nome do seu bucket deve ser o nome do domínio. Ex.: meu-website.com.br

![img](https://miro.medium.com/max/60/0*8QemZjRPQ79-hakS.png?q=20)

![img](https://miro.medium.com/max/630/0*8QemZjRPQ79-hakS.png)

\4. Em Set properties podemos habilitar o log de ações efetuadas no bucket, versionamento de objetos, e tags. Neste tutorial vamos deixar todas desabilitadas como é padrão. Clicar em next

![img](https://miro.medium.com/max/60/0*_uOwdZpf5Hp_IWVh.png?q=20)

![img](https://miro.medium.com/max/630/0*_uOwdZpf5Hp_IWVh.png)

\5. Em Set permissions vamos definir que esse bucket poderá ter acesso livre de leitura. Para isso devemos escolher a opção Grant read access to this bucket em Manage public permissions. Clicar em next

![img](https://miro.medium.com/max/52/0*OF7pmdiZCm58CUYV.png?q=20)

![img](https://miro.medium.com/max/630/0*OF7pmdiZCm58CUYV.png)

\6. Em Review não precisamos alterar nada, apenas clicar em Create Bucket.

![img](https://miro.medium.com/max/60/0*sFAQSeDTtSojxSDu.png?q=20)

![img](https://miro.medium.com/max/630/0*sFAQSeDTtSojxSDu.png)

\7. Agora que já temos um bucket criado com acesso público de leitura. Agora precisamos definir que esse bucket será utilizado para hospedar o website estático. Para isso, ir em Properties e clicar na caixa Static website hosting

![img](https://miro.medium.com/max/60/0*KpOlUegj6dYMtoBb.png?q=20)

![img](https://miro.medium.com/max/630/0*KpOlUegj6dYMtoBb.png)

\8. Nesta página podemos definir o arquivo Index (homepage do website), o arquivo da página de erro (caso aconteça algum erro o s3 irá redirecionar o usuário para essa página) e regras de redirecionamento. Para o nosso caso, vamos escolher a opção padrão *Use this bucket to host a website*, e nas opções definir os valores como a imagem a seguir.

![img](https://miro.medium.com/max/60/0*GMOXJVmBhJc6XUFE.png?q=20)

![img](https://miro.medium.com/max/630/0*GMOXJVmBhJc6XUFE.png)

\9. Criar o arquivo index.html localmente no formato abaixo:

```
<html>
 <head>
  <title>Meu Website</title>
 </head>
 <body>
  <h1>Hello AWS!</h1>
 </body>
 <script type="text/javascript">
  alert("javascript works!");
 </script>
</html>
```

\10. Para enviar o index.html para o s3, clicar em upload

![img](https://miro.medium.com/max/60/0*XkdQ92mV_R0jUM-h.png?q=20)

![img](https://miro.medium.com/max/481/0*XkdQ92mV_R0jUM-h.png)

\11. Escolher o arquivo index.html e clicar em Next

![img](https://miro.medium.com/max/60/0*eNXc5aowtoBsYh6B.png?q=20)

![img](https://miro.medium.com/max/508/0*eNXc5aowtoBsYh6B.png)

\12. Em Select files precisamos informar que esse arquivo tem permissão pública de leitura. Para isso escolhemos a opção *Grant public read access to this object(s)* em Manage public permissions. Clicar em upload.

![img](https://miro.medium.com/max/60/0*rAnQ_HR9AfrKkGZC.png?q=20)

![img](https://miro.medium.com/max/504/0*rAnQ_HR9AfrKkGZC.png)

\13. Agora basta entrar em meu-website.s3-website-us-east-1.amazonaws.com.

Para simular os custos com hospedagem estática no S3, vamos utilizar a [calculadora](http://calculator.s3.amazonaws.com/index.html) fornecida pela AWS. Utilizaremos como base um site que gera 10 mil GETs e 3 gigabytes de transferência por mês. De acordo com a calculadora o gasto seria em média de U$$ 0.22. Perceba que a cobrança pesa mais na quantidade de dados transferidos para fora da AWS. Então, quando criar o website estático, estimar bem a quantidade de dados que serão trafegados.

![img](https://miro.medium.com/max/60/0*BtX0iNwsEBHiXjFO.png?q=20)

![img](https://miro.medium.com/max/437/0*BtX0iNwsEBHiXjFO.png)

Utilizando uma máquina na DigitalOcean ou com o AWS Lightsail, o gasto seria de no mínimo U$$ 5.00 mensais. Com uma máquina EC2 o gasto seria de U$$ 4.00 à U$$ 8.00 para máquinas nano, micro ou small

Vimos a simplicidade de usar o s3 para hospedar um website estático, porém na maioria das vezes precisamos de algum processamento que somente com javascript e html não é possível. Um exemplo seria fornecer aos visitantes do website um formulário de contato. Vamos construir uma função de back-end, que recebe o formulário de contato utilizando Lambda e API Gateway.

# API Gateway

> *“*[*O Amazon API Gateway*](https://aws.amazon.com/pt/api-gateway/) *é um serviço totalmente gerenciado que permite que desenvolvedores criem, publiquem, mantenham, monitorem e protejam APIs em qualquer escala.”*

Puramente, o API Gateway expõe uma interface de serviços, atuando como ponte entre o serviço e seu destino (Lambda, chamada HTTP, Mocks, etc).

# Lambda

> [*“Execute códigos sem pensar sobre os servidores.”*](https://aws.amazon.com/pt/lambda/)

O AWS Lambda permite a execução de códigos em algumas linguagens sem precisar se preocupar com máquinas, provisionamento e disponibilidade.

# Continuando o Hands On

Inicialmente vamos criar uma função Lambda em NodeJS que irá receber um formulário de contato (por simplicidade não vamos fazer o Lambda enviar emails, mas é possível usando SNS). Caso não esteja confortável com NodeJS, o AWS Lambda dá suporte para Java, C# e Python.

1. No console da AWS clicar em Lambda, e no console do Lambda clicar em New Function (caso você não tenha nenhuma função, a AWS já abrirá o guia para criação)
2. Em blueprint selecionaremos a opção Blank Function

![img](https://miro.medium.com/max/60/0*3TX_pHSHdYp5ua6A.png?q=20)

![img](https://miro.medium.com/max/630/0*3TX_pHSHdYp5ua6A.png)

\3. Em Configure triggers apenas clicar em Next

\4. Em Configure function, vamos nomear a função, e criar as roles. Clicar em Next

![img](https://miro.medium.com/max/60/0*p5YepS9bNAADCons.png?q=20)

![img](https://miro.medium.com/max/630/0*p5YepS9bNAADCons.png)

![img](https://miro.medium.com/max/60/0*pG-zW--M-wChW3Xs.png?q=20)

![img](https://miro.medium.com/max/630/0*pG-zW--M-wChW3Xs.png)

\5. Em Review apenas clicar em Create Function.

\6. Agora, vamos editar o código. Por ser em NodeJS, a AWS permite edição no próprio console. Clicar em Save após edição do código

- O código apenas imprime os parâmetros que o API Gateway enviará ao Lambda, e retorna status code 200 ou 400, com uma mensagem de sucesso ou erro.

![img](https://miro.medium.com/max/60/0*giZ1Ts-ipJTYvgnJ.png?q=20)

![img](https://miro.medium.com/max/630/0*giZ1Ts-ipJTYvgnJ.png)

```
exports.handler = (event, context, callback) => {
    let body = JSON.parse(event.body);
    let from = body.from;
    let subject = body.subject;
    let message = body.message;
    console.log(`Mensagem de ${from}`);
    console.log(`Assunto: ${subject}`);
    console.log(`Mensagem: ${message}`);
    let response = {};
    if(message && message.length > 0){
        response = {
            "headers": {"Access-Control-Allow-Origin": "*"},
            "body": JSON.stringify({"resultado": "Mensagem enviada com sucesso"}),
            "statusCode": 200
        }
    }else{
        response = {
            "headers": {"Access-Control-Allow-Origin": "*"},
            "body": JSON.stringify({"resultado": "Por favor, escreva sua mensagem"}),
            "statusCode": 400
        }
    }
    callback(null, response);
};
```

\7. Agora criaremos o endpoint para envio do formulário. Basta acessar o API Gateway. Clicar em Create API

\8. Em Create new API configurar conforme imagem abaixo. Clicar em Create API

![img](https://miro.medium.com/max/60/0*1VFkE9w32W9siYUW.png?q=20)

![img](https://miro.medium.com/max/625/0*1VFkE9w32W9siYUW.png)

\9. Em MinhaAPI, criaremos o Resource */formulario*. Basta clicar em Actions > Create Resource. Também precisamos habilitar o CORS, para aceitar nossas chamadas via javascript.

![img](https://miro.medium.com/max/60/0*7pu1HBhIPx6Fol3e.png?q=20)

![img](https://miro.medium.com/max/630/0*7pu1HBhIPx6Fol3e.png)

\10. Também criaremos o método POST para o resource */formulario*. Ir em Actions > Create Method. Clicar em save

- No Integration type escolhemos a opção Lambda Function
- Deixamos a opção Use Lambda Proxy integration marcada. Assim o API Gateway irá redirecionar todo o contexto de Request para o Lambda
- No Lambda Funcion escolhemos a função que foi criada anteriormente

![img](https://miro.medium.com/max/60/0*QSdhIqmOOudwGrF0.png?q=20)

![img](https://miro.medium.com/max/629/0*QSdhIqmOOudwGrF0.png)

\11. Habilitar o CORS em Action > Enable CORS

\12. Enfim, faremos o deploy da API, clicando em Action > Deploy API. Vamos chamar a stage de v1. Após o Deploy, você poderá ver a url que a AWS criou para a API.

![img](https://miro.medium.com/max/60/0*0Pvm4UGrmyVCOb2X.png?q=20)

![img](https://miro.medium.com/max/556/0*0Pvm4UGrmyVCOb2X.png)

Agora o API Gateway Já está integrado com o Lambda. Quando um client chamar o POST [*https://****{endereco-api}\****/formulario*](https://medium.com/awsugsp/{endereco-api}/formulario) o API Gateway fará a ponte entre o Lambda e o client.

Por último, vamos criar o formulário que chamará a API e subir para o S3, como foi feito anteriormente. O index.html ficará assim:

```
<html>
<head>
 <title>Meu Website</title>
 <script src=”http://code.jquery.com/jquery-3.2.1.min.js"></script>
</head>
<body>
 <h1>Hello AWS!</h1>
 <form id=”contactForm” action=”#” method=”post”>
 <div>
 <label for=”from”>Seu nome:</label>
 <input type=”text” name=”from” value=””>
 </div>
 <div>
 <label for=”subject”>Assunto:</label>
 <input type=”text” name=”subject” value=””>
 </div>
 <div>
 <label for=”message”>Mensagem:</label>
 <textarea name=”message” rows=”8" cols=”80"></textarea>
 </div>
 <button type=”submit” name=”button”>Enviar</button>
 </form>
 <div id=”formResult”>
 </div>
</body>
<script type=”text/javascript”>
 $(‘#contactForm’).submit(function(event) {
 event.preventDefault();
 var url = ‘https://suaapi.execute-api.us-east-1.amazonaws.com/v1/formulario';
 var formData = $(this);
 var from = formData.find(‘input[name=from]’).val();
 var subject = formData.find(‘input[name=subject]’).val();
 var message = formData.find(‘textarea[name=message]’).val();
 var request = {
 from: from,
 subject: subject,
 message: message
 }
 $.ajax({
 type: “POST”,
 url: url,
 data: JSON.stringify(request),
 headers: {
 ‘Content-Type’:’application/json’
 },
 success: function(response){
 $(‘#formResult’).html(response.resultado);
 },
 error: function(err){
 var response = JSON.parse(err.responseText);
 $(‘#formResult’).html(response.resultado);
 }
 });
 });
</script>
</html>
```

Como resultado final a página web chama o POST */formulario* e o API Gateway invoca o Lambda que processa o formulário. Podemos observar os efeitos nas imagens abaixo

![img](https://miro.medium.com/max/60/0*gRtKdjDpkF9mq-nT.png?q=20)

![img](https://miro.medium.com/max/630/0*gRtKdjDpkF9mq-nT.png)

![img](https://miro.medium.com/max/60/0*VLts72Rw4e56GWsy.png?q=20)

![img](https://miro.medium.com/max/630/0*VLts72Rw4e56GWsy.png)

O uso do Lambda é cobrado por tempo de execução, em lotes de 100ms. Ou seja, se a execução durar 180 milissegundos, esta será taxada como 200 milissegundos. A AWS fornece um free tier mensal de para o Lambda de 1 milhão de execuções (considerando 100 milissegundos com 384 MegaBytes de memória alocada.

Com auxílio da calculadora da AWS, o custo de 10 mil execuções com duração de 300 milissegundos e 256 MegaBytes de memória fica dentro do free tier (não expira após 12 meses).

O API Gateway tem custo de U$$ 3.50 por milhão de chamadas e mais a transferência de dados. Para 10 mil chamadas, e um 1 GigaBytes de transferência, o custo total seria de U$$ 3.59 mensais.

# Total de custos:

Recurso Preço (U$$) s3 0.22 Api Gateway 3.59 Lambda 0.00 **Total** **3.81**

Os custos do S3, com o API Gateway e o Lambda somam um total de U$$ 3.81, o que ainda é mais barato do que uma máquina no LightSail ou na DigitalOcean, ou até mesmo uma EC2 nano.

# Conclusão:

Utilizando o S3 conseguimos facilmente e com baixo custo hospedar um website estático, livre da preocupação com máquinas, provisionamento, disponibilidade e manutenção.

Além do baixo custo, da garantia de disponibilidade, e da facilidade de manutenção, existem outras vantagens, por exemplo: é possível expandir funcionalidades do website utilizando funções Lambda e API Gateway; com o Cloudfront gerenciar certificados ssl, controlar cache de arquivos, etc; controlar acessos com simples configurações no S3 e versionar arquivos.

A abordagem serverless oferece inúmeras ferramentas que simplificam e tratam muitas preocupações que temos seguindo os modelos tradicionais, mas também apresentam novos desafios quando utilizamos esta abordagem, e devemos saber orquestrar muito bem as ferramentas para obter o máximo de aproveitamento.

Escrito por: Filipe Santana

