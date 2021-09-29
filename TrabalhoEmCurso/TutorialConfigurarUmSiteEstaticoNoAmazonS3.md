# Tutorial: configurar um site estático no Amazon S3

[PDF](https://docs.aws.amazon.com/pt_br/AmazonS3/latest/userguide/s3-userguide.pdf#HostingWebsiteOnS3Setup)

[RSS](https://docs.aws.amazon.com/pt_br/AmazonS3/latest/userguide/s3-userguide-rss-updates.rss)



Você pode configurar um bucket do Amazon S3 para funcionar como um site. Este exemplo conduz você pelas etapas de hospedagem de um site no Amazon S3.

**Tópicos**

- [Etapa 1: Criar um bucket](https://docs.aws.amazon.com/pt_br/AmazonS3/latest/userguide/HostingWebsiteOnS3Setup.html#step1-create-bucket-config-as-website)
- [Etapa 2: Habilitar hospedagem de site estático](https://docs.aws.amazon.com/pt_br/AmazonS3/latest/userguide/HostingWebsiteOnS3Setup.html#step2-create-bucket-config-as-website)
- [Etapa 3: editar as configurações do Bloqueio de acesso público](https://docs.aws.amazon.com/pt_br/AmazonS3/latest/userguide/HostingWebsiteOnS3Setup.html#step3-edit-block-public-access)
- [Etapa 4: Adicionar política de bucket que torna o conteúdo do bucket publicamente disponível](https://docs.aws.amazon.com/pt_br/AmazonS3/latest/userguide/HostingWebsiteOnS3Setup.html#step4-add-bucket-policy-make-content-public)
- [Etapa 5: Configurar um documento de índice](https://docs.aws.amazon.com/pt_br/AmazonS3/latest/userguide/HostingWebsiteOnS3Setup.html#step5-upload-index-doc)
- [Etapa 6: configurar um documento de erros](https://docs.aws.amazon.com/pt_br/AmazonS3/latest/userguide/HostingWebsiteOnS3Setup.html#step6-upload-error-doc)
- [Etapa 7: testar o endpoint do site](https://docs.aws.amazon.com/pt_br/AmazonS3/latest/userguide/HostingWebsiteOnS3Setup.html#step7-test-web-site)
- [Etapa 8: Limpar](https://docs.aws.amazon.com/pt_br/AmazonS3/latest/userguide/HostingWebsiteOnS3Setup.html#getting-started-cleanup-s3-website-overview)

## Etapa 1: Criar um bucket

As instruções a seguir fornecem uma visão geral de como criar seus buckets para hospedagem de sites. Para obter instruções detalhadas passo a passo sobre como criar um bucket, consulte [Criação de um bucket](https://docs.aws.amazon.com/pt_br/AmazonS3/latest/userguide/create-bucket-overview.html).

**Para criar um bucket**

1. Faça login no AWS Management Console e abra o console do Amazon S3 em https://console.aws.amazon.com/s3/.

2. Selecione **Create bucket (Criar bucket)**.

3. Insira o **Bucket name** (Nome do bucket) (por exemplo, `example.com`).

4. Selecione a região onde você deseja criar o bucket.

   Escolha uma região próxima de você para minimizar a latência e os custos ou atender a requisitos normativos. A região escolhida determina o endpoint de site do Amazon S3. Para obter mais informações, consulte [Endpoints de site](https://docs.aws.amazon.com/pt_br/AmazonS3/latest/userguide/WebsiteEndpoints.html).

5. Para aceitar as configurações padrão e criar o bucket, escolha **Create (Criar)**.

## Etapa 2: Habilitar hospedagem de site estático

Depois de criar um bucket, você pode habilitar a hospedagem de site estático para seu bucket. Você pode criar um bucket novo ou usar um existente.

**Como habilitar a hospedagem de sites estáticos**

1. Faça login no AWS Management Console e abra o console do Amazon S3 em https://console.aws.amazon.com/s3/.

2. Na lista **Buckets**, escolha o nome do bucket para o qual você deseja habilitar a hospedagem de site estático.

3. Escolha **Properties (Propriedades)**.

4. Em **Static website hosting (Hospedagem estática de sites)**, escolha **Edit (Editar)**.

5. Escolha **Use this bucket to host a website (Usar este bucket para hospedar um site)**.

6. Em **Static website hosting (Hospedagem estática de sites)**, escolha **Enable (Ativar)**.

7. Em **Index Document (Documento de índice)**, insira o nome do arquivo do documento de índice, que geralmente é `index.html`.

   O nome do documento de índice diferencia letras maiúsculas de minúsculas e deve corresponder exatamente ao nome do arquivo do documento de índice HTML do qual você planeja fazer upload para o bucket do S3. Quando você configura um bucket para hospedagem de site, deve especificar um documento de índice. O Amazon S3 retorna esse documento de índice quando as solicitações são feitas para o domínio raiz ou alguma subpasta. Para obter mais informações, consulte [Configurar um documento de índice](https://docs.aws.amazon.com/pt_br/AmazonS3/latest/userguide/IndexDocumentSupport.html).

8. Para fornecer seu próprio documento de erros personalizado para erros da classe 4XX, em **Error document (Documento de erros)**, insira o nome de arquivo do documento de erros personalizado.

   O nome do documento de erro diferencia letras maiúsculas de minúsculas e deve corresponder exatamente ao nome do arquivo do documento de erro HTML do qual você planeja fazer upload para o bucket do S3. Se você não especificar um documento de erro personalizado e ocorrer um erro, o Amazon S3 retornará um documento de erro HTML padrão. Para obter mais informações, consulte [Configurar um documento de erro personalizado](https://docs.aws.amazon.com/pt_br/AmazonS3/latest/userguide/CustomErrorDocSupport.html).

9. (Opcional) Se você especificar regras avançadas de redirecionamento em **Redirection rules (Regras de redirecionamento)**, use XML para descrever as regras.

   Por exemplo, você pode encaminhar solicitações condicionalmente de acordo com nomes de chave de objeto ou prefixos específicos na solicitação. Para obter mais informações, consulte [Configurar regras de redirecionamento para usar redirecionamentos condicionais avançados](https://docs.aws.amazon.com/pt_br/AmazonS3/latest/userguide/how-to-page-redirect.html#advanced-conditional-redirects).

10. Selecione **Save changes**.

    O Amazon S3 permite a hospedagem estática de sites para seu bucket. Na parte inferior da página, em **Static website hosting (Hospedagem estática de sites)**, você verá o endpoint do site do seu bucket.

11. Em **Static website hosting (Hospedagem de sites estáticos)**, anote o **Endpoint**.

    O **Endpoint** é o endpoint do site do Amazon S3 para o bucket. Depois de concluir a configuração do bucket como um site estático, é possível usar esse endpoint para testar o site.

## Etapa 3: editar as configurações do Bloqueio de acesso público

Por padrão, o Amazon S3 bloqueia o acesso público à sua conta e aos seus buckets. Se quiser usar um bucket para hospedar um site estático, use estas etapas para editar as configurações de bloqueio de acesso público.

**Atenção**

Antes de concluir esta etapa, revise [Bloquear o acesso público ao armazenamento do Amazon S3](https://docs.aws.amazon.com/pt_br/AmazonS3/latest/userguide/access-control-block-public-access.html) para garantir que você entenda e aceite os riscos envolvidos em permitir o acesso público. Ao desativar as configurações de bloqueio de acesso público para tornar seu bucket público, qualquer pessoa na Internet pode acessá-lo. Recomendamos que você bloqueie todo o acesso público aos buckets.

1. Abra o console do Amazon S3 em https://console.aws.amazon.com/s3/.

2. Escolha o nome do bucket configurado como um site estático.

3. Escolha **Permissions (Permissões)**.

4. Em **Block public access (bucket settings) (Bloqueio de acesso público (configurações de bucket))**, escolha **Edit (Editar)**.

5. Desmarque **Block \*all\* public access (Bloquear todo acesso público)** e escolha **Save changes (Salvar alterações)**.

   **Atenção**

   Antes de concluir esta etapa, revise [Bloquear o acesso público ao armazenamento do Amazon S3](https://docs.aws.amazon.com/pt_br/AmazonS3/latest/userguide/access-control-block-public-access.html) para garantir que você entenda e aceite os riscos envolvidos em permitir o acesso público. Ao desativar as configurações de bloqueio de acesso público para tornar seu bucket público, qualquer pessoa na Internet pode acessá-lo. Recomendamos que você bloqueie todo o acesso público aos buckets.

   ![img](https://docs.aws.amazon.com/pt_br/AmazonS3/latest/userguide/images/edit-public-access-clear.png)

   O Amazon S3 desativa as configurações do Bloqueio de acesso público para seu bucket. Para criar um site público e estático, você também pode ter que [editar as configurações de Bloqueio de acesso público](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/block-public-access-account.html) para sua conta antes de adicionar uma política de bucket. Se as configurações da conta para bloquear acesso público estiverem ativadas no momento, você verá uma observação em **Block public access (bucket settings) (Bloqueio de acesso público (configurações de bucket))**.

## Etapa 4: Adicionar política de bucket que torna o conteúdo do bucket publicamente disponível

Depois de editar as configurações do Bloqueio de acesso público do S3, é possível adicionar uma política de bucket para conceder acesso público de leitura ao bucket. Ao conceder um acesso público de leitura, qualquer pessoa na Internet pode acessar seu bucket.

**Importante**

A política a seguir é somente um exemplo e concede acesso total aos conteúdos do bucket. Antes de prosseguir com esta etapa, revise [Como posso proteger os arquivos no meu bucket do Amazon S3?](http://aws.amazon.com/premiumsupport/knowledge-center/secure-s3-resources/) para garantir que você entende as práticas recomendadas a fim de proteger os arquivos no bucket do S3 e os riscos envolvidos na concessão de acesso público.

1. Em **Buckets**, escolha o nome do seu bucket.

2. Escolha **Permissions (Permissões)**.

3. Em **Bucket Policy (Política de bucket)**, escolha **Edit (Editar)**.

4. Para conceder acesso público de leitura ao site, copie a política de bucket a seguir e cole-a no **Bucket policy editor (Editor de política de bucket)**.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Sid": "PublicReadGetObject",
               "Effect": "Allow",
               "Principal": "*",
               "Action": [
                   "s3:GetObject"
               ],
               "Resource": [
                   "arn:aws:s3:::Bucket-Name/*"
               ]
           }
       ]
   }
   ```

5. Atualize o `Resource` para o nome do bucket.

   Na política de bucket do exemplo anterior, `Bucket-Name` é um espaço reservado para o nome do bucket. Para usar essa política de bucket com seu próprio bucket, você deve atualizar esse nome para corresponder ao nome do seu bucket.

6. Selecione **Save changes**.

   Uma mensagem é exibida indicando que a política de bucket foi adicionada com sucesso.

   Se você vir um erro que diz `Policy has invalid resource`, confirme se o nome do bucket na política de bucket corresponde ao nome do seu bucket. Para obter informações sobre como adicionar uma política de bucket, consulte [Como adicionar uma política de bucket do S3?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/add-bucket-policy.html)

   Se você receber uma mensagem de erro e não puder salvar a política do bucket, verifique suas configurações de acesso público para confirmar que você permite acesso público ao bucket.

## Etapa 5: Configurar um documento de índice

Quando você habilita a hospedagem de sites estáticos para seu bucket, insere o nome do documento de índice (por exemplo, `index.html`). Depois de habilitar a hospedagem de sites estáticos para seu bucket, faça upload de um arquivo HTML com esse nome de documento de índice para o bucket.

**Como configurar o documento de índice**

1. Criar um arquivo `index.html`

   Se você não tiver um arquivo `index.html`, poderá usar o HTML a seguir para criar um:

   ```
   <html xmlns="http://www.w3.org/1999/xhtml" >
   <head>
       <title>My Website Home Page</title>
   </head>
   <body>
     <h1>Welcome to my website</h1>
     <p>Now hosted on Amazon S3!</p>
   </body>
   </html>
   ```

2. Salve o arquivo de índice localmente.

   O nome do documento de índice deve corresponder exatamente ao nome do documento de índice que você inseriu na caixa de diálogo **Hospedagem de site estático**. O nome do documento de índice diferencia maiúsculas de minúsculas. Por exemplo, se você inserir `index.html` no nome do **Documentos de índice** na caixa de diálogo **Hospedagem de site estático**, o nome do arquivo do documento de índice também deverá ser `index.html` e não `Index.html`.

3. Faça login no AWS Management Console e abra o console do Amazon S3 em https://console.aws.amazon.com/s3/.

4. Na lista **Buckets**, selecione o nome do bucket que você deseja usar para hospedar um site estático.

5. Habilite a hospedagem de sites estáticos para seu bucket e insira o nome exato do documento de índice (por exemplo, `index.html`). Para obter mais informações, consulte [Habilitar a hospedagem de sites](https://docs.aws.amazon.com/pt_br/AmazonS3/latest/userguide/EnableWebsiteHosting.html).

   Depois de habilitar a hospedagem estática do site, vá para a etapa 6.

6. Para fazer upload do documento de índice para o bucket, siga um destes procedimentos:

   - Arraste e solte o arquivo de índice na listagem de buckets do console.
   - Escolha **Upload (Fazer upload)** e siga as instruções para escolher e fazer upload do arquivo de índice.

   Para obter instruções detalhadas, consulte [Fazer upload de objetos](https://docs.aws.amazon.com/pt_br/AmazonS3/latest/userguide/upload-objects.html).

7. (Opcional) Faça upload do conteúdo de outro site para o seu bucket.

## Etapa 6: configurar um documento de erros

Ao habilitar a hospedagem de site estático para o bucket, insira o nome do documento de erro (por exemplo, `404.html`). Depois de habilitar a hospedagem de sites estáticos para seu bucket, faça upload de um arquivo HTML com esse nome de documento de erros para o bucket.

**Para configurar um documento de erros**

1. Crie um documento de erro, por exemplo `404.html`.

2. Salve o arquivo de documento de erros localmente.

   O nome do documento de erros diferencia maiúsculas e minúsculas e deve corresponder exatamente ao nome que você insere ao habilitar a hospedagem estática do site. Por exemplo, se você inserir `404.html` como o nome do **Error document (Documento de erro)** na caixa de diálogo **Static website hosting (Hospedagem de site estático)**, o nome de arquivo do documento de erro também deve ser `404.html`.

3. Faça login no AWS Management Console e abra o console do Amazon S3 em https://console.aws.amazon.com/s3/.

4. Na lista **Buckets**, selecione o nome do bucket que você deseja usar para hospedar um site estático.

5. Habilite a hospedagem de site estático para seu bucket e insira o nome exato do documento de erro (por exemplo, `404.html`). Para obter mais informações, consulte [Habilitar a hospedagem de sites](https://docs.aws.amazon.com/pt_br/AmazonS3/latest/userguide/EnableWebsiteHosting.html).

   Depois de habilitar a hospedagem estática do site, vá para a etapa 6.

6. Para fazer upload do documento de erros para o bucket, siga um destes procedimentos:

   - Arraste e solte o arquivo de documento de erros na lista de buckets do console.
   - Escolha **Upload (Fazer upload)** e siga as instruções para escolher e fazer upload do arquivo de índice.

   Para obter instruções detalhadas, consulte [Fazer upload de objetos](https://docs.aws.amazon.com/pt_br/AmazonS3/latest/userguide/upload-objects.html).

## Etapa 7: testar o endpoint do site

Depois de configurar a hospedagem de site estático para seu bucket, você pode testar o endpoint do site.

**nota**

O Amazon S3 não oferece suporte para o acesso HTTPS ao site. Se quiser usar HTTPS, você poderá usar o Amazon CloudFront para servir um site estático hospedado no Amazon S3.

Para obter mais informações, consulte [Como uso o CloudFront para veicular um site estático hospedado no Amazon S3?](http://aws.amazon.com/premiumsupport/knowledge-center/cloudfront-serve-static-website/) e [Exigir HTTPS para comunicação entre visualizadores e CloudFront](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/using-https-viewers-to-cloudfront.html).

1. Em **Buckets**, escolha o nome do seu bucket.

2. Escolha **Properties (Propriedades)**.

3. Na parte inferior da página, em **Static website hosting (Hospedagem estática de sites)**, escolha seu **Bucket website endpoint (Endpoint de site do Bucket)**.

   Seu documento de índice é aberto em uma janela separada do navegador.

Agora você tem um site hospedado no Amazon S3. Esse site está disponível no endpoint de site do Amazon S3. No entanto, você pode ter um domínio, como `example.com`, que deseja usar para exibir o conteúdo do site que criou. Talvez você também queira usar o suporte ao domínio raiz do Amazon S3 para atender solicitações para `http://www.example.com` e `http://example.com`. Isso exige etapas adicionais. Para ver um exemplo, consulte [Configurar um site estático usando um domínio personalizado registrado no Route 53](https://docs.aws.amazon.com/pt_br/AmazonS3/latest/userguide/website-hosting-custom-domain-walkthrough.html).

## Etapa 8: Limpar

Se tiver criado o site apenas como exercício de aprendizado, você poderá excluir os recursos da AWS alocados, de maneira que deixe de acumular cobranças. Depois que você excluir os recursos da AWS, o site deixará de estar disponível. Para obter mais informações, consulte [Excluir um bucket](https://docs.aws.amazon.com/pt_br/AmazonS3/latest/userguide/delete-bucket.html).





**Esta página foi útil?**

Sim

Não

[Fornecer feedback](https://docs.aws.amazon.com/forms/aws-doc-feedback?hidden_service_name=S3&topic_url=http://docs.aws.amazon.com/pt_br/AmazonS3/latest/userguide/HostingWebsiteOnS3Setup.html)

**Tópico anterior:** [Demonstrações de exemplo](https://docs.aws.amazon.com/pt_br/AmazonS3/latest/userguide/hosting-websites-on-s3-examples.html)

**Próximo tópico:** [Configurar um site estático usando um domínio personalizado](https://docs.aws.amazon.com/pt_br/AmazonS3/latest/userguide/website-hosting-custom-domain-walkthrough.html)