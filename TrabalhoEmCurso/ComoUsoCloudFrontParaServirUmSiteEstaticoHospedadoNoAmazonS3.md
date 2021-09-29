# Como uso o CloudFront para servir um site estático hospedado no Amazon S3?

*Última atualização: 05/01/2021*

**Quero hospedar um site estático em um bucket do Amazon Simple Storage Service (Amazon S3). Em seguida, quero servir meu site usando uma distribuição do Amazon CloudFront. Como posso fazer isso?**

## Descrição breve

Para servir um site estático hospedado no Amazon S3, você pode implantar uma distribuição do CloudFront aplicando uma destas configurações:

- Usar um endpoint da API REST como origem, com acesso restrito por uma [identidade do acesso de origem (OAI)](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-restricting-access-to-s3.html)
- Usar um endpoint de site como origem, com acesso anônimo (público) permitido
- Usar um endpoint de site como origem, com acesso restrito por um cabeçalho Referer
- Usar o AWS CloudFormation para implantar um endpoint de API REST como origem, com acesso restrito por uma OAI e um domínio personalizado apontando para o CloudFront

Para obter mais informações sobre os dois tipos de endpoint, consulte [Principais diferenças entre um endpoint de site e um endpoint de API REST](https://docs.aws.amazon.com/AmazonS3/latest/dev/WebsiteEndpoints.html#WebsiteRestEndpointDiff).

## Resolução

Siga estas etapas para configurar uma distribuição do CloudFront com o tipo de endpoint do S3 que deseja usar como origem:

### Usar um endpoint de API REST como origem, com acesso restrito por uma OAI

1. Use o [console do Amazon S3](https://s3.console.aws.amazon.com/s3/) para [criar um bucket](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/create-bucket.html) e para [fazer upload dos arquivos do seu site](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/upload-objects.html).
   **Observação:** não é necessário habilitar a hospedagem de sites estáticos no seu bucket para usar esta configuração. Essa configuração usa o endpoint da API REST do bucket em vez do endpoint do site no recurso de hospedagem de sites estáticos.
2. [Crie uma distribuição na Web do CloudFront](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/distribution-web-creating-console.html). Além das configurações de distribuição necessárias para seu caso de uso, insira as seguintes informações:
   Em **Origin Domain Name (Nome do domínio de origem)**, selecione o bucket que criou.
   Em **Restrict Bucket Access (Restringir acesso ao bucket)**, selecione **Yes (Sim)**.
   Em **Origin Access Identity (Identidade do acesso de origem)**, selecione **Create a New Identity (Criar uma nova identidade)**.
   Em **Comment (Comentário)**, você pode optar por manter o valor padrão ou inserir um rótulo para a OAI.
   Em **Grant Read Permissions on Bucket (Conceder permissões de leitura no bucket)**, selecione **Yes, Update Bucket Policy (Sim, atualizar a política do bucket)**.
3. O uso de SSL (HTTPS) é uma prática recomendada para seu site. Para definir o uso de SSL, em **SSL Certificate (Certificado SSL)**, selecione **Default CloudFront Certificate (Certificado padrão do CloudFront)** para usar o nome DNS padrão do CloudFront ou selecione **Custom SSL Certificate (Certificado SSL padrão)** para usar seu próprio domínio personalizado. Você pode escolher **Request or Import a Certificate with ACM (Solicitar ou importar um certificado com o ACM)** para solicitar um novo certificado.
   **Importante:** se você tiver inserido [nomes de domínio alternativos (CNAMEs)](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/distribution-web-values-specify.html#DownloadDistValuesCNAME) para sua distribuição, os CNAMEs deverão corresponder ao certificado SSL selecionado. Para solucionar problemas com seu certificado SSL, consulte [How can I troubleshoot issues with using a custom SSL certificate for my CloudFront distribution?](https://aws.amazon.com/premiumsupport/knowledge-center/custom-ssl-certificate-cloudfront/)
4. Escolha **Create Distribution (Criar distribuição)**.
5. Atualize os registros de DNS do seu domínio a fim de apontar o CNAME do site para o nome de domínio da sua distribuição do CloudFront. O nome de domínio da sua distribuição pode ser encontrado no [console do CloudFront](https://console.aws.amazon.com/cloudfront/) e tem um formato semelhante a **d1234abcd.cloudfront.net**.
6. Aguarde até que as alterações de DNS sejam propagadas e as entradas de DNS anteriores expirem.
   **Observação:** o tempo necessário para que os valores de DNS anteriores expirem [depende do valor de TTL definido na sua zona hospedada](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/troubleshooting-new-dns-settings-not-in-effect.html#troubleshooting-new-dns-settings-not-in-effect-cached-resource-record-set) e se esse valor é usado por seu resolvedor local.

### Usar um endpoint de site como origem, com acesso anônimo (público) permitido

Esta configuração permite acesso público de leitura ao bucket do seu site. Para obter mais informações, consulte [Definir permissões para acesso ao site](https://docs.aws.amazon.com/AmazonS3/latest/dev/WebsiteAccessPermissionsReqd.html).

**Observação:** quando você usa o endpoint de site estático do Amazon S3, as conexões entre o CloudFront e o Amazon S3 só estão disponíveis por HTTP. Para usar o HTTPS em conexões entre o CloudFront e o Amazon S3, configure um endpoint de API REST no S3 como sua origem.

1. Use o [console do Amazon S3](https://s3.console.aws.amazon.com/s3/) para [criar um bucket e habilitar a hospedagem de site estático](https://docs.aws.amazon.com/AmazonS3/latest/dev/HostingWebsiteOnS3Setup.html#step1-create-bucket-config-as-website) no bucket.
2. Copie o **Endpoint** do seu bucket da caixa de diálogo **Static website hosting (Hospedagem de site estático)** sem os caracteres iniciais **http://**. O formato é semelhante a **DOC-EXEMPLO-BUCKET.s3-site-região.amazonaws.com**. Você precisará usar o endpoint neste formato em uma etapa posterior.
3. [Adicione uma política de bucket para conceder acesso público de leitura ](https://docs.aws.amazon.com/AmazonS3/latest/dev/HostingWebsiteOnS3Setup.html#step3-add-bucket-policy-make-content-public)ao bucket que você criou.
4. [Crie uma distribuição na web do CloudFront](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/distribution-web-creating-console.html). Além das configurações de distribuição necessárias para seu caso de uso, insira as seguintes informações:
   Em **Origin Domain Name (Nome do domínio de origem)**, insira o endpoint copiado na etapa 2.
5. O uso de SSL (HTTPS) é uma prática recomendada para seu site. Para definir o uso de SSL, em **SSL Certificate (Certificado SSL)**, selecione **Default CloudFront Certificate (Certificado padrão do CloudFront)** para usar o nome DNS padrão do CloudFront ou selecione **Custom SSL Certificate (Certificado SSL padrão)** para usar seu próprio domínio personalizado. Você pode escolher **Request or Import a Certificate with ACM (Solicitar ou importar um certificado com o ACM)** para solicitar um novo certificado.
   **Importante:** se você tiver inserido [nomes de domínio alternativos (CNAMEs)](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/distribution-web-values-specify.html#DownloadDistValuesCNAME) para sua distribuição, os CNAMEs deverão corresponder ao certificado SSL selecionado. Para solucionar problemas com seu certificado SSL, consulte [How can I troubleshoot issues with using a custom SSL certificate for my CloudFront distribution?](https://aws.amazon.com/premiumsupport/knowledge-center/custom-ssl-certificate-cloudfront/)
6. Escolha **Create Distribution (Criar distribuição)**.
7. Atualize os registros de DNS do seu domínio a fim de apontar o CNAME do site para o nome de domínio da sua distribuição do CloudFront. O nome de domínio da sua distribuição pode ser encontrado no [console do CloudFront](https://console.aws.amazon.com/cloudfront/) e tem um formato semelhante a **d1234abcd.cloudfront.net**.
8. Aguarde até que as alterações de DNS sejam propagadas e as entradas de DNS anteriores expirem.
   **Observação:** o tempo necessário para que os valores de DNS anteriores expirem [depende do valor de TTL definido na sua zona hospedada](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/troubleshooting-new-dns-settings-not-in-effect.html#troubleshooting-new-dns-settings-not-in-effect-cached-resource-record-set) e se esse valor é usado por seu resolvedor local.

### Usar um endpoint de site como origem, com acesso restrito por um cabeçalho Referer

Para restringir o acesso, esta configuração define um cabeçalho Referer personalizado na distribuição e, em seguida, [usa uma política de bucket](https://docs.aws.amazon.com/AmazonS3/latest/dev/example-bucket-policies.html#example-bucket-policies-use-case-4) para permitir acesso apenas a solicitações que tenham o cabeçalho Referer personalizado. Não deixe de avaliar se o acesso permitido por essa configuração atende aos requisitos do seu caso de uso.

**Observação:** quando você usa o endpoint de site estático do Amazon S3, as conexões entre o CloudFront e o Amazon S3 só estão disponíveis por HTTP. Para usar o HTTPS em conexões entre o CloudFront e o Amazon S3, configure um endpoint de API REST no S3 como sua origem.

1. Use o [console do Amazon S3](https://s3.console.aws.amazon.com/s3/) para [criar um bucket e habilitar a hospedagem de site estático](https://docs.aws.amazon.com/AmazonS3/latest/dev/HostingWebsiteOnS3Setup.html#step1-create-bucket-config-as-website) no bucket.
2. Copie o **Endpoint** do seu bucket da caixa de diálogo **Static website hosting (Hospedagem de site estático)** sem os caracteres iniciais **http://**. O formato é semelhante a **DOC-EXEMPLO-BUCKET.s3-site-região.amazonaws.com**. Você precisará usar o endpoint neste formato em uma etapa posterior.
3. [Crie uma distribuição na Web do CloudFront](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/distribution-web-creating-console.html). Além das configurações de distribuição necessárias para seu caso de uso, insira as seguintes informações:
   Em **Origin Domain Name (Nome do domínio de origem)**, insira o endpoint copiado na etapa 2.
   Na seção **Origin Custom Headers (Cabeçalhos personalizados de origem)**, insira **Referer** em **Header Name (Nome do cabeçalho)**. Em **Value (Valor)**, insira o cabeçalho personalizado que deseja encaminhar à origem (bucket do S3). Insira um valor personalizado ou secreto que só você conheça se quiser restringir o acesso à origem.
4. O uso de SSL (HTTPS) é uma prática recomendada para seu site. Para definir o uso de SSL, em **SSL Certificate (Certificado SSL)**, selecione **Default CloudFront Certificate (Certificado padrão do CloudFront)** para usar o nome DNS padrão do CloudFront ou selecione **Custom SSL Certificate (Certificado SSL padrão)** para usar seu próprio domínio personalizado. Você pode escolher **Request or Import a Certificate with ACM (Solicitar ou importar um certificado com o ACM)** para solicitar um novo certificado.
   **Importante:** se você tiver inserido [nomes de domínio alternativos (CNAMEs)](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/distribution-web-values-specify.html#DownloadDistValuesCNAME) para sua distribuição, os CNAMEs deverão corresponder ao certificado SSL selecionado. Para solucionar problemas com seu certificado SSL, consulte [How can I troubleshoot issues with using a custom SSL certificate for my CloudFront distribution?](https://aws.amazon.com/premiumsupport/knowledge-center/custom-ssl-certificate-cloudfront/)
5. Escolha **Create Distribution (Criar distribuição)**.
6. Abra o bucket do seu site no [console do Amazon S3](https://s3.console.aws.amazon.com/s3/). Em seguida, [adicione uma política de bucket](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/add-bucket-policy.html) que [conceda a permissão s3:GetObject desde que a solicitação inclua o cabeçalho personalizado Referer](https://docs.aws.amazon.com/AmazonS3/latest/dev/example-bucket-policies.html#example-bucket-policies-use-case-4) que você especificou na etapa 3.
   **Observação:** use uma instrução de negação explícita na política do bucket para bloquear o acesso de qualquer solicitação que não inclua o cabeçalho personalizado Referer.
7. Atualize os registros de DNS do seu domínio a fim de apontar o CNAME do site para o nome de domínio da sua distribuição do CloudFront. O nome de domínio da sua distribuição pode ser encontrado no [console do CloudFront](https://console.aws.amazon.com/cloudfront/) e tem um formato semelhante a **d1234abcd.cloudfront.net**.
8. Aguarde até que as alterações de DNS sejam propagadas e as entradas de DNS anteriores expirem.
   **Observação:** o tempo necessário para que os valores de DNS anteriores expirem [depende do valor de TTL definido na sua zona hospedada](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/troubleshooting-new-dns-settings-not-in-effect.html#troubleshooting-new-dns-settings-not-in-effect-cached-resource-record-set) e se esse valor é usado por seu resolvedor local.

### Usar o CloudFormation para implantar um endpoint de API REST como origem, com acesso restrito por uma OAI e um domínio personalizado apontando para o CloudFront

Com esta solução, seu site:

- É implantado com o CloudFormation
- É hospedado no Amazon S3
- É distribuído pelo CloudFront
- Usa um certificado SSL/TLS do AWS Certificate Manager (ACM)
- Usa o recurso Lambda@Edge para adicionar cabeçalhos de segurança a cada resposta do servidor

Para obter instruções sobre a implantação dessa solução, consulte [Amazon CloudFront Secure Static Website (Site estático seguro do Amazon CloudFront )](https://github.com/aws-samples/amazon-cloudfront-secure-static-site#user-content-amazon-cloudfront-secure-static-website) no site do GitHub.

------

## Informações relacionadas

[Conceitos básicos de um site estático seguro](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/getting-started-secure-static-website-cloudformation-template.html)

[Conceder permissão somente leitura para um usuário anônimo](https://docs.aws.amazon.com/AmazonS3/latest/dev/example-bucket-policies.html#example-bucket-policies-use-case-2)

[Use email to validate domain ownership](https://docs.aws.amazon.com/acm/latest/userguide/gs-acm-validate-email.html)

[Use DNS to validate domain ownership](https://docs.aws.amazon.com/acm/latest/userguide/gs-acm-validate-dns.html)

[Como usar nomes de domínio alternativos e HTTPS](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/using-https-alternate-domain-names.html)