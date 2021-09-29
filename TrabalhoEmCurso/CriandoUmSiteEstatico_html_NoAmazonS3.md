# Criando um site estático (html) no Amazon S3

[![Adilson Cesar](https://miro.medium.com/fit/c/56/56/1*QDM7Xp5CBXk8vz-XZ_ewPg@2x.jpeg)](https://medium.com/@adilsonbna?source=post_page-----7067ffdae197--------------------------------)

[Adilson Cesar](https://medium.com/@adilsonbna?source=post_page-----7067ffdae197--------------------------------)

[Apr 23, 2017·3 min read](https://medium.com/@adilsonbna/criando-um-site-estático-html-no-amazon-s3-7067ffdae197?source=post_page-----7067ffdae197--------------------------------)

Consigo criar um site sem webhosting?

A resposta é sim. E nesse post vamos fazer um passo-a-passo como criar um site estático no Amazon S3.

> *Caso tenha dúvida na criação de buckets e sobre Amazon S3 consulte o post [***Criando S3 e armazenando arquivos na nuvem utilizando Amazon CLI\***](https://medium.com/@adilsonbna/criando-s3-e-armazenando-arquivos-na-nuvem-utilizando-amazon-cli-a5cf64957b8a)

Criei um bucket chamado ***devopss3website\*** localizado em ***US East (N. Virginia)\***

![img](https://miro.medium.com/max/60/1*4m6ThfD4lHTrkCQ6J95jeA.png?q=20)

![img](https://miro.medium.com/max/630/1*4m6ThfD4lHTrkCQ6J95jeA.png)

Após criação do bucket iremos em ***Properties\*** e na opção ***Static website hosting\***

![img](https://miro.medium.com/max/60/1*k1-bearrA3qZ5lvf2kQ5_w.jpeg?q=20)

![img](https://miro.medium.com/max/630/1*k1-bearrA3qZ5lvf2kQ5_w.jpeg)

Preencha os itens abaixo conforme citado abaixo:

![img](https://miro.medium.com/max/54/1*DQP8Y7h2ZfYJ-E7JCCZe6A.jpeg?q=20)

![img](https://miro.medium.com/max/630/1*DQP8Y7h2ZfYJ-E7JCCZe6A.jpeg)

E também criei uma regra de permissão via **bucket policy editor**:

> *Quando você configura um bucket como um site, você deve tornar os objetos que você deseja servir publicamente legíveis. Para fazer isso, você escreve uma política de bucket que concede a todos permissão s3: GetObject. No ponto de extremidade do site, se um usuário solicitar um objeto que não existe, o Amazon S3 retornará o código de resposta HTTP 404 (Não encontrado). Se o objeto existir, mas você não tiver concedido permissão de leitura no objeto, o ponto final do site retornará o código de resposta HTTP 403 (acesso negado). O usuário pode usar o código de resposta para inferir se um objeto específico existe ou não. Se você não desejar esse comportamento, você não deve habilitar o suporte de site para seu bucket.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::devopss3website/*"
        }
    ]
}
```

![img](https://miro.medium.com/max/60/1*ETui1ROVMPy-cthN7dMoag.png?q=20)

![img](https://miro.medium.com/max/630/1*ETui1ROVMPy-cthN7dMoag.png)

## Agora vamos para criação do website.

Criei o arquivo ***index.html\*** com o conteúdo:

```
<!DOCTYPE html>
<html>
<body>
<h1>My First S3 Webiste</h1>
<p>S3 Website hosting enabled</p>
</body>
</html>
```

E ***error.html\*** com:

```
<!DOCTYPE html>
<html>
<body>
<h1>ERROR on S3 Webiste</h1>
<p>S3 Website hosting error</p>
</body>
</html>
```

Fiz upload dos arquivos dentro do bucket ***devopss3website\*** alterando as permissões de ***Leitura (Read)\*** para ***Everyone.\***

![img](https://miro.medium.com/max/58/1*zv8AZLAUAhIBCIDdz-53Ag.png?q=20)

![img](https://miro.medium.com/max/630/1*zv8AZLAUAhIBCIDdz-53Ag.png)

Portanto já podemos testar nosso website:

http://devopss3website.s3-website-us-east-1.amazonaws.com/

Voi-là!

![img](https://miro.medium.com/max/60/1*n1yKqfL3RX3T1Ta1904H9w.png?q=20)

![img](https://miro.medium.com/max/630/1*n1yKqfL3RX3T1Ta1904H9w.png)

E para redirecionamento de nome (CNAME) usei a ferramenta [noip](https://www.noip.com/). Dessa maneira consigo traduzir a URL citada para o nome ***s3.access.ly.\***

![img](https://miro.medium.com/max/60/1*U_ecizj-2r1atULh7bUBHA.png?q=20)

![img](https://miro.medium.com/max/630/1*U_ecizj-2r1atULh7bUBHA.png)

Já podemos simular erro retirando acesso de leitura do ***index.html\*** e já tenho ***error.html\*** ativado.

![img](https://miro.medium.com/max/60/1*8ZASETQk6Xm7Wjq7mWDP_g.png?q=20)

![img](https://miro.medium.com/max/630/1*8ZASETQk6Xm7Wjq7mWDP_g.png)

Baixe seu html diretamente no S3.

![img](https://miro.medium.com/max/60/1*QkDLmyTtWcrgJMt7_m9p8Q.png?q=20)

![img](https://miro.medium.com/max/630/1*QkDLmyTtWcrgJMt7_m9p8Q.png)

Enjoy! :)