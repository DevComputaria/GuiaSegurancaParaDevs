## Transporte seguro de coisas: HTTPS explicado

## O problema

HTTP é o protocolo que os navegadores usam para se comunicar com o servidor. O problema com HTTP sem qualquer S é que ele envia e recebe dados em texto simples.

#### Bem, quem pode ver meus dados em texto simples?

Bem, qualquer um em sua rede local, seus colegas de trabalho, por exemplo, ou pessoas sentadas ao seu redor em seu café favorito.

#### Como eles vão fazer isso?

Uma vez que os dados estão em texto simples, eles podem simplesmente dizer ao [switch](https://en.wikipedia.org/wiki/Network_switch) para entregar pacotes para sua máquina em vez do seu por [ARP, tentando](https://en.wikipedia.org/wiki/ARP_spoofing) a tabela ARP mantida pelo: `switch`[![ARP poisioning](https://github.com/FallibleInc/security-guide-for-developers/raw/master/images/arp.png)](https://github.com/FallibleInc/security-guide-for-developers/blob/master/images/arp.png)

Além disso, o dono do café ou seu chefe em seu escritório podem ver seus dados programando o hub/switch facilmente já que eles possuem e têm acesso físico a ele ou [grampeando](https://en.wikipedia.org/wiki/Fiber_tapping) o próprio fio entrando no café.

**Ruim HTTP!**

## Entra HTTPS

[![https](https://github.com/FallibleInc/security-guide-for-developers/raw/master/images/https.gif)](https://github.com/FallibleInc/security-guide-for-developers/blob/master/images/https.gif)

O 'S' em HTTPS significa Secure ou seja, se você estiver visitando qualquer site na internet que tenha o protocolo no URI, então é mais provável que seja seguro. Ninguém pode farejar seu trânsito.`https``middle`

### Como funciona?

HTTPS criptografa todos os dados que são transferidos entre o navegador e o servidor. O servidor e o navegador usam uma chave simétrica conhecida por ambos para criptografar os dados. O processo pelo qual eles chegam à chave comum é chamado [de aperto de mão TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security#TLS_handshake). Em termos simples, o servidor envia o seu junto com embutido em um para o navegador, o navegador envia de volta uma encirped usando a chave pública do servidor. O servidor descriptografa a mensagem criptografada usando sua chave privada para obter a chave secreta pré-mestre. Tanto o navegador quanto o servidor agora convertem a chave pré-mestre na que é eventualmente usada para criptografia de todas as comunicações futuras entre o servidor e o navegador.`public key``domain name``certificate``pre-master secret key``master secret key`

[![Encryption](https://github.com/FallibleInc/security-guide-for-developers/raw/master/images/encryption.png)](https://github.com/FallibleInc/security-guide-for-developers/blob/master/images/encryption.png)

Ainda há um problema com o processo acima, ou seja, qualquer [homem no meio](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) também pode gerar um certificado e fingir ser o servidor de origem e enviar conteúdo malicioso para o navegador.

Para resolver esse problema, navegador como Chrome, Firefox, Safari etc. vêm incorporados com informações para descobrir quais certificados são genuínos. Os navegadores procuram assinatura no certificado, a assinatura no certificado precisa ser de uma das [autoridades de certificado confiáveis](https://en.wikipedia.org/wiki/Certificate_authority). Em termos simples, as autoridades de certificados são certas organizações bem conhecidas que todos sabem ser dignas de confiança (tudo se resume à confiança). Se não houver tal assinatura no certificado, então o navegador exibirá um aviso ao usuário de que essa conexão não é realmente HTTPS. O servidor, por outro lado, precisa obter o certificado assinado de uma das autoridades de certificado, verificando fisicamente sua identidade (enviando documentos etc.).

Assim, servidores dois propósito principal`https`

```
* It tells you that the website domain shown in the browser is the one you are actually talking to.
* It encrypts all the communication between the domain in the browser and the browser itself.
```

### Como obter HTTPS para o meu site?

#### Existem duas maneiras de obter HTTPS para o seu site

1.  Pago
    -   Você precisa comprar um certificado SSL de alguns CAs
    -   Em seguida, você precisa gerar uma solicitação de assinatura de certificado a partir de seu servidor
    -   Então eles pedem que você verifique se você realmente possui o domínio.
    -   Em seguida, eles permitem que você baixe o certificado assinado que você pode usar na configuração do seu servidor.
2.  Livre:
    -   Use [LetsEncrypt](https://letsencrypt.org/). O Letsencrypt é gratuito porque todo o processo é totalmente automatizado, portanto, livrando-se do custo manual de configuração, criação, validação, expiração etc.
    -   Para configurar, siga as etapas mencionadas aqui dependendo do servidor: [etapas de configuração](https://certbot.eff.org/#ubuntuxenial-nginx)

#### As melhores práticas para configuração https, exemplos são para [nginx](https://www.nginx.com/), mas as configurações para apache e outras também estão disponíveis ([ssl config gerador](https://mozilla.github.io/server-side-tls/ssl-config-generator/))

-   atualizar/corrigir regularmente [abre o espaço](https://www.openssl.org/source/) para a versão mais recente disponível, porque isso irá protegê-lo de bugs como [heartbleed](https://en.wikipedia.org/wiki/Heartbleed) e [muito mais](https://www.openssl.org/news/secadv/20160503.txt).
    
-   adicionar esta bandeira no servidor nginx conf para proteção do lado do servidor contra [ataques BEAST](https://en.wikipedia.org/wiki/Transport_Layer_Security#BEAST_attack) ''' ssl\_prefer\_server\_ciphers;'
    
    ssl\_ciphers "ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-SHA384:ECDHE-RSA-AES128-SHA256:ECDHE-RSA-AES256-SHA:ECDHE-RSA-AES128-SHA:DHE-RSA-AES256 -SHA256:DHE-RSA-AES128-SHA256:DHE-RSA-AES256-SHA:DHE-RSA-AES128-SHA:ECDHE-RSA-DES-CBC3-SHA:EDH-RSA-DES-CBC3-SHA:A 256-GCM-SHA384:AES128-GCM-SHA256:AES256-SHA256:AES128-SHA256:AES256-SHA:AES128-SHA:DES-CBC3-SHA:HIGH:!aNULL:! eNULL:! EXPORTAÇÃO:! DES:! MD5:! PSK:! RC4"; #Disables todas as cifras fracas '''
    
-   Versões mais antigas dos protocolos ssl têm várias vulnerabilidades graves (ex: [ataque POODLE](https://en.wikipedia.org/wiki/POODLE), [ataque DROWN](https://en.wikipedia.org/wiki/DROWN_attack)), então suporte apenas TLSv1.1 e TLSv1.2. Não suporte sslv2 e sslv3. Verifique [a adoção](https://en.wikipedia.org/wiki/Transport_Layer_Security#Web_browsers) para saber a troca de restringir a essas versões do TLS. ''' ssl\_protocols TLSv1.1 TLSv1.2;
    
-   O parâmetro Padrão Diffie-Hellman usado pela nginx é de apenas 1024 bits que é considerado não tão seguro. Além disso, é o mesmo para todos os usuários nginx que usam o config padrão. Estima-se que uma equipe acadêmica pode quebrar 768 bits primos e que um estado-nação poderia quebrar um auge de 1024 bits. Ao quebrar um prime de 1024 bits, pode-se espionar em 18% dos principais 1 milhão de domínios HTTPS, então não use o parâmetro DH padrão, gere localmente o parâmetro para mais segurança, também use um número maior de bits.
    
    ```shell
     $ cd /etc/ssl/certs
     $ openssl dhparam -out dhparam.pem 4096
    ```
    
    ssl\_dhparam /etc/nginx/ssl/dhparam.pem; \`\`\`
    
-   config para habilitar HSTS(HTTP Strict Transport Security) para evitar [despir ssl](https://en.wikipedia.org/wiki/SSL_stripping#SSL_stripping). Isso não deve ser um problema se ALL, yeah, if ALL traffic é redirecionado para https `add_header Strict-Transport-Security "max-age=31536000; includeSubdomains;";`
    

## Pinning de certificados para aplicativos (e site)

#### O que é isso agora?

Em geral, qualquer usuário que tenha acesso ao aplicativo pode ver todas as chamadas de API, mesmo que HTTPS. Para isso, ele cria uma autoridade de certificado e diz ao dispositivo (Android / iOS) para confiar nele. Agora, quando você se conecta ao servidor, ele fica entre o servidor e o aplicativo e substitui o certificado do seu servidor pelo aquele gerado com seu certificado (ter seu próprio par público/privado) assinado por sua própria autoridade de certificado e agora ele pode sentar no meio e atuar como servidor para o cliente móvel e atuar como cliente para o servidor. Sorrateiro.`on the fly``key`

#### Esperar! A HTTPS não deveria impedir isso?

Sim, mas https só pode ajudá-lo quando as autoridades confiáveis de certificado são realmente dignas de confiança. Neste caso, o usuário forçou o dispositivo a confiar em sua própria autoridade de certificado criada!

#### Então, como eu preveni isso?

Fixação de certificados - Basicamente, em seu pacote de aplicativos, código rígido o certificado do servidor e antes de fazer qualquer verificação de chamada de API se o servidor está realmente usando o mesmo certificado codificado ou alguém tentou esgueirar-se em seu próprio certificado.

#### Cuidado

-   Caso o certificado mude no lado do servidor, você terá que forçar os usuários a atualizar o aplicativo, o aplicativo deixará de funcionar.
-   Se você estragar a fixação do certificado, você terá que pedir aos usuários para atualizar o aplicativo senão o aplicativo deixará de funcionar.

#### Uma maneira melhor!

A fixação de certificados é uma boa maneira de evitar isso, mas há uma maneira melhor de garantir que ninguém possa bisbilhotar - usar . Geralmente sites como o Google giram seu certificado, então você terá que forçar os usuários a atualizar seu aplicativo. Em vez disso, o que você deve fixar em seu aplicativo é o que permanece estático mesmo quando o Google gira seu certificado, portanto, não precisa de qualquer atualização do aplicativo. Isso se chama.`public key pinning``public key``Public key Pinning`

-   Exemplos de código de amostra do Android e iOS:

```
https://www.paypal-engineering.com/2015/10/14/key-pinning-in-mobile-applications/
```

## Precauções para o público em geral

-   Quando você visitar um site no seu navegador, certifique-se de que ele exibe o cadeado assim [![padlock](https://github.com/FallibleInc/security-guide-for-developers/raw/master/img/padlock.png)](https://github.com/FallibleInc/security-guide-for-developers/blob/master/img/padlock.png) (será cinza no safári)
-   Se você estiver usando uma internet não confiável ou pública (wifi/wired) e vir um cadeado e uma página de aviso, então não procede, alguém pode estar bisbilhotando seu tráfego.
-   Aplicativos para iOS e Android não têm como dizer se estão criptografando o tráfego. Azar.
-   Não entregue seus celulares não desalojados a nenhuma pessoa não confiável. Pode instalar certas autoridades de certificados não confiáveis e pode ver todo o seu tráfego.`CAs`
-   Se você usa um celular ou laptop fornecido pela empresa, então eles podem ter instalado certas (autoridades de certificados) para serem confiáveis pelo dispositivo e podem facilmente bisbilhotar toda a sua navegação. Você deve verificar se algum está instalado em seu telefone. Passos para verificar: No iOS, vá para -> -> . Se houver algo instalado lá, então alguém pode estar farejando seu tráfego. No Android, vá para , em "Pessoal", toque , em "Armazenamento credencial", toque . Verifique os certificados instalados pelo usuário e sistema.`CAs``CA``Settings``General``Profiles``Settings``Security``Trusted credentials`

## Futuro do HTTPS

Web foi construída no protocolo HTTP que não tem a parte de segurança. Aos poucos as pessoas começaram a sentir a necessidade de ter o canal protegido, de modo que levou ao nascimento do HTTPS. Ainda hoje, a maioria dos sites são HTTP, já que esse é o . Se for preciso obter HTTPS, eles usam um dos métodos mencionados na seção acima "como obter https para o meu site".`default protocol`

Seria incrível se todos os sites usassem em vez de . Além disso, todos os navegadores devem forçar https, o que significa que eles devem falhar a solicitação se não for . Atualmente isso é implementado usando a lista de pré-carregamento, mas isso é opcional para sites optarem, mas seria bom se todos os sites fossem forçados a ser https. Isso melhoraria a segurança dos usuários finais. Há muita gente promovendo a mudança para https em todos os lugares.`https``http``https``HSTS`

Mas há um problema com a atualização para https, ou seja, se algum site foi previamente vinculado como http e agora só funciona com https então isso vai quebrar (como os links para este site não seriam atualizados pelo site do linker). Existem plugins para usar [HTTPS em todos os lugares](https://www.eff.org/Https-everywhere) que forçam toda a comunicação a ser ligada, se possível. Mas uma [proposta](https://www.w3.org/DesignIssues/Security-NotTheS.html) melhor é fazer HTTPS em todos os lugares no sentido do protocolo, mas não no prefixo URI - na qual não precisamos de dois prefixos diferentes e, basta fazer http usar TLS fundamentalmente.`http link``https://``http``https`
