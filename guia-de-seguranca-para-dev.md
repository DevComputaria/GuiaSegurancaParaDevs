## Guia de Segurança para Desenvolvedores

-   [Coisas realmente importantes](#coisas-realmente-importantes)
-   [Melhores práticas gerais](#melhores-praticas-gerais)
    -   [Processo de Desenvolvimento](#processo-de-desenvolvimento)
    -   [Design de aplicação](#design-de-aplicacao)
    -   [Proteção geral](#protecao-geral)
    -   [Teste](#teste)
    -   [Aplicativo em execução](#aplicativo-em-execucao)
    -   [Proteção de dados](#protecao-de-dados)
-   [Design de aplicação](#design-de-aplicacao)
    -   [Cookies](#cookies)
    -   [Validação](#validacao)
    -   [Manipulação de erros e registro](#manipulacao-de-erros-e-registro)
    -   [Autenticação e Autorização](#autenticacao-e-autorização)
    -   [Gerenciamento de Sessões](#gerenciamento-de-sessoes)
    -   [Uploads de arquivos - Varredura de vírus](#uploads-de-arquivos-varredura-de-virus)
    -   [Serviços web - chaves de API, JWTs, CORS](#servicos-web-chaves-de-api-jwts-cors)
    -   [Segurança de comunicação - TLS](#seguranca-de-comunicacao-tls)
-   [Testes automatizados de segurança](#testes-automatizados-de-segurança)
-   [Recursos](#recursos)

# Coisas muito importantes!

-   não colocar dados ao vivo em qualquer dispositivo local, a menos que tenha sido assinado para tal uso
-   apenas acessar dados ao vivo /confidenciais sob orientação estrita (cada serviço deve ter regras em torno de seu uso)
-   entender as políticas em torno de onde você deve armazenar seu código fonte. NUNCA coloque informações como senhas, chaves de API ou endereços IP em repositórios de código, mesmo os privados.

# Melhores práticas gerais

## Processo de desenvolvimento

-   segurança deve ser parte do processo de entrega ágil e ser aplicado por história
-   use o [OWASP Security Testing Framework](https://www.owasp.org/index.php/The_OWASP_Testing_Framework) para uma lista de verificação
-   impor [ramos protegidos](https://github.com/blog/2051-protected-branches-and-required-status-checks)
-   impor revisões através [de pedidos de pull](https://help.github.com/articles/using-pull-requests/)
-   exigir [compromissos assinados](https://help.github.com/articles/signing-commits-using-gpg/)
-   têm um processo de revisão por pares bem definido, compreendido e aplicado
-   certifique-se de que você tenha implantações rápidas e repetíveis com testes automatizados
-   monitorar avisos de segurança e patches e atualizar quando necessário

## Design de aplicação

-   favorecer sistemas simples; eles são mais fáceis de proteger
-   aderir aos princípios do [código limpo](https://www.amazon.co.uk/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882) - isso torna as aplicações mais fáceis de entender
-   considerar [projeto por contrato](https://en.wikipedia.org/wiki/Design_by_contract). [As pré-condições](https://en.wikipedia.org/wiki/Precondition) definem as restrições de entrada, [pós-condições](https://en.wikipedia.org/wiki/Postcondition) o que deve ser verdade; teste contra estes
-   reduzir a superfície de ataque removendo pontos finais de código/bibliotecas desnecessários, remover código de demonstração, senhas padrão etc.
-   minimizar integrações, entender e proteger contra terceiros comprometidos (por exemplo, um script originado de um terceiro não confiável pode ser malicioso)
-   favorecer pequenos componentes com uma [responsabilidade clara e única](https://blog.8thlight.com/uncle-bob/2014/05/08/SingleReponsibilityPrinciple.html)
-   favorecer o uso de bibliotecas e estruturas estabelecidas sobre a rolagem de suas próprias. No entanto, importe apenas software confiável e sempre verifique sua integridade
-   evitar o uso de variáveis compartilhadas / [globais](http://programmers.stackexchange.com/questions/148108/why-is-global-state-so-evil)
-   preferem [a imutabilidade](http://miles.no/blogg/why-care-about-functional-programming-part-1-immutability)
-   evitar nulos usando [opção](https://en.wikipedia.org/wiki/Option_type), por exemplo, [Opção Scala](http://danielwestheide.com/blog/2012/12/19/the-neophytes-guide-to-scala-part-5-the-option-type.html) e [Java Opcional](http://onelineatatime.io/optional-guava-and-java-8/)

## Proteção geral

-   tenha cuidado usando <script src> a menos que você tenha controle completo sobre o script que é carregado
-   se enviar um formulário modifica dados ou estágio, use POST não GET
-   evite [a injeção SQL](https://www.owasp.org/index.php/SQL_Injection_Prevention_Cheat_Sheet) / injeção de javascript, garantindo que todas as consultas sejam [parametrizadas](https://www.owasp.org/index.php/Query_Parameterization_Cheat_Sheet) (e/ou use, por exemplo, um [ORM](https://en.wikipedia.org/wiki/Object-relational_mapping), [Registro Ativo](http://www.martinfowler.com/eaaCatalog/activeRecord.html))
-   proteger contra scripting cross site [(XSS)](https://www.owasp.org/index.php/XSS_(Cross_Site_Scripting)_Prevention_Cheat_Sheet) escapando / higienizando dados não confiáveis usando uma biblioteca de codificação de segurança padrão. Considere também usar cabeçalhos \[Política de Segurança de Conteúdo\] ([https://w3c.github.io/webappsec-csp/2/](https://w3c.github.io/webappsec-csp/2/)) para ativos da lista branca que uma página pode carregar
-   proteger contra falsificação de solicitação de local cruzado [(CSRF)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) que visam solicitações de alteração de estado. Verifique os cabeçalhos padrão para verificar se a solicitação é a mesma origem E verifique um token CSRF
-   garantir que os recursos que você carrega sejam como esperado usando [integridade subresource](https://www.w3.org/TR/SRI/)
-   use HTTP Strict Transport Security [(HSTS)](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet) com, por exemplo, um "Strict-Transport-Security: max-age=8640000; incluiSubDomains" HTTP Header para proteger contra ataques de tiras SSL. Considere inserir seu domínio na [lista de pré-carregamento do HSTS](https://hstspreload.appspot.com/)
-   proteger contra [clickjacking](https://www.owasp.org/index.php/Clickjacking_Defense_Cheat_Sheet) usando o "X-Frame-Options: DENY" HTTP Header
-   Não use [JSONP](http://stackoverflow.com/questions/3839966/can-anyone-explain-what-jsonp-is-in-layman-terms) para enviar dados confidenciais. Uma vez que o JSONP é javaScript válido, ele não é protegido pela política de mesma origem
-   não eval qualquer String não verificada (por exemplo, não eval uma string esperada para conter JSON - use JSON.parse em vez disso)
-   não armazenar ids de sessão no [LocalStorage](https://www.sitepoint.com/html5-local-storage-revisited/). Pense cuidadosamente antes de colocar quaisquer dados confidenciais no armazenamento local, mesmo quando criptografado
-   preferir sessionStorage para localStorage se a persistência por mais tempo do que a sessão do navegador não for necessária
-   validar URLs passados para XMLHttpRequest.open (os navegadores permitem que estes sejam multi-domínio)
-   use apenas [WebSockets](http://www.html5rocks.com/en/tutorials/websockets/basics/) sobre TLS (wss://) e esteja ciente de que a comunicação pode ser falsificada / sequestrada através do XSS
-   usar [diferentes subdomínios](https://www.gov.uk/service-manual/operations/operating-servicegovuk-subdomains.html) para páginas da Web voltadas para o público, ativos estáticos e administração

## Cookies

-   usar cookies [seguros](https://www.owasp.org/index.php/SecureFlag), [assinados e httpOnly](https://www.owasp.org/index.php/HttpOnly) quando possível (e obrigatório se contiver informações da conta)
-   criptografar quaisquer dados confidenciais com, por exemplo, [encicloção de cookies (nó)](https://www.npmjs.com/package/cookie-encryption)
-   evite colocar informações confidenciais em cookies de terceiros

## Teste

-   favorecer [o Desenvolvimento Impulsionado por Testes](https://en.wikipedia.org/wiki/Test-driven_development) para incentivar uma boa cobertura de teste e design de aplicativos
-   teste em um ambiente configurado como live (infraestrutura, replicação, TLS etc.) com perfis de dados semelhantes (mas não com dados ao vivo) o mais cedo possível
-   qualquer teste contra dados vivos em ambientes não prod (mesmo que limpo /anonimizado) precisa de aprovação adequada
-   use a Integração Contínua (IC) e garanta uma boa unidade automatizada, integração, aceitação, fumaça, desempenho, testes de segurança
-   realizar uma Verificação de Saúde de TI (ITHC, [Teste de Penetração, Teste de Caneta](https://en.wikipedia.org/wiki/Penetration_test)) para novos serviços ou mudanças significativas
-   considerar o uso de uma versão do [macaco do caos](http://www.ibm.com/developerworks/library/a-devops4/), por exemplo, [Exército Simian](https://github.com/Netflix/SimianArmy) para testar falhas aleatórias de instância

## Executando o aplicativo

-   use sempre HTTPS (certifique-se de usar [O TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security) 1.2)
-   Os aplicativos web devem usar um [WAF (](https://www.owasp.org/index.php/Web_Application_Firewall)Web Application Firewall, firewall de aplicativos da Web) configurado corretamente, por exemplo, [NAXSI](https://github.com/nbs-system/naxsi)
-   remover funcionalidade e código desnecessários
-   se ocorrerem exceções, falhar com segurança
-   monitorar métricas, por exemplo, [Sysdig](http://www.sysdig.org/)
-   criar auditoria para tentativas de login bem sucedidas e mal sucedidas, tentativas de autorização mal sucedidas, logouts etc.
-   desativar [métodos HTTP](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) nãoused
-   restringir todos os aplicativos e serviços a serem executados com o conjunto mínimo de privilégios/permissões
-   isolar ambientes de desenvolvimento da rede de produção e permitir o acesso ao desenvolvimento apenas de usuários autorizados (ambientes de desenvolvimento podem ser um vetor de ataque comum)

## Validação

-   realizar verificações de integridade para garantir que não houve adulteração de campos ocultos ou identidades de transação. Pode usar [checkum](https://en.wikipedia.org/wiki/Checksum), [HMAC](https://tools.ietf.org/html/rfc2104), criptografia ou assinatura digital, dependendo do risco
-   validação do lado do servidor de todas as entradas, incluindo cabeçalhos, cookies, redirecionamentos
-   preferem [aceitar boa entrada conhecida](https://www.owasp.org/index.php/XSS_(Cross_Site_Scripting)_Prevention_Cheat_Sheet) em vez de [rejeitar entrada ruim conhecida](https://www.owasp.org/index.php/Data_Validation#Reject_known_bad)
-   higienizar a entrada, se necessário (por exemplo, retirar o espaço branco ou hífens dos números de telefone)
-   garantir que as seleções de opções, caixa de seleção e rádio contenham apenas valores permitidos (dado)
-   validar o tipo de dados / comprimento / alcance / chars permitidos
-   sempre reem validar dados de formulários previamente inseridos no caso de ter sido sub-repticiamente alterado; campos ocultos devem ser validados também
-   todas as falhas de validação devem resultar em rejeição de entrada com uma mensagem apropriada para o usuário
-   ter testes automatizados para verificar uma série razoável de falhas de validação são como esperado

## Manipulação de erros e registro de registros

-   não registrar informações confidenciais (por exemplo, informações da conta ou identificadores de sessão) a menos que seja necessário
-   garantir que nenhum depuração / traços de pilha sejam exibidos para o usuário final em produção
-   usar mensagens de erro genéricas e páginas de erro personalizadas na produção
-   evitar adulteração de logs, garantindo que eles sejam lidos apenas e não permitir exclusões
-   garantir que exista um mecanismo para realizar a análise de log
-   restringir o acesso a logs

## Proteção de dados

-   não armazenar senhas, strings de conexão etc. em texto simples
-   entender os dados que serão usados, sua política de retenção e remoção
-   entender quem estará acessando o serviço/dados, com quais dispositivos através de quais redes/serviços de terceiros
-   apenas armazenar e usar a quantidade mínima de dados necessária para atender à necessidade do usuário; permitir que os usuários visualizem apenas os dados de que precisam
-   não (fornecer interfaces que) permitem consulta arbitrária de dados
-   não permitem o download de conjuntos de dados em massa ou muitos dados para serem visíveis em uma página
-   limite de taxa acesso a grandes conjuntos de dados e tentativas de acesso de registros (também limitar o número de transações que um usuário ou dispositivo pode realizar em um determinado período de tempo)
-   impor o uso de esquemas de banco de dados, mesmo para bancos de dados noSQL usando, por exemplo, [Mongoose](http://mongoosejs.com/docs/guide.html) para MongoDB
-   evitar caching dados dentro de serviços, a menos que necessário
-   proteger caches / arquivos temporários contendo dados confidenciais de uso não autorizado e purgá-los o mais rápido possível
-   use criptografia síncrono (segredo compartilhado), por exemplo, [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) para criptografar / descriptografar seus próprios dados se for sensível.
-   Certifique-se de que uma chave compartilhada seja mantida de forma segura e separada aos dados, de preferência em um cofre de chaves separado (por exemplo, [Vault](https://www.vaultproject.io/)) que seu serviço pode acessar quando precisar de uma chave
-   usar um processo de gerenciamento chave, por exemplo, alavancando [a Amazon KMS](https://aws.amazon.com/kms/)
-   criptografar backups (você precisará saber quais chaves são necessárias para lidar com qual versão)
-   campos de codificação que têm valores especialmente sensíveis
-   desabilitar autocompletar em formulários para campos sensíveis
-   não transmitir qualquer informação sensível dentro da URL
-   desativar o cache do lado do cliente para páginas que contenham dados [confidenciais usando cabeçalhos de cache HTTP apropriados](https://www.keycdn.com/blog/http-cache-headers/), ou seja, "Cache-Control: no-store", "Expira: 0" e "Pragma: no-cache"
-   dados de anonimise (garantindo que a reidentificação não possa ocorrer) enviados para ferramentas de relatórios ou sendo usados como dados de teste
-   considere criptografar formulários parcialmente preenchidos sob uma chave mantida pelo usuário se você não precisar usar esses dados
-   os aplicativos devem se conectar a bancos de dados com diferentes credenciais para cada distinção de confiança, por exemplo, usuário, somente leitura, administrador, convidado

## Autenticação/autorização

-   consulte a [orientação de senha do CESG](https://www.cesg.gov.uk/guidance/password-guidance-simplifying-your-approach) ao decidir sua política de senha para usuários
-   se a autenticação for necessária, autenticar e autorizar em cada solicitação
-   usar controles de autenticação centralizados, favorecer o SSO
-   se os serviços de autenticação cairem, eles não devem dar aos usuários acesso não autorizado
-   falha de autenticação não deve dar nenhuma informação sobre qual parte falhou - todas as respostas de erro devem ser genéricas e as mesmas
-   autenticação e autorização separadas do recurso que está sendo solicitado
-   funções de administração/gerenciamento de contas devem ser particularmente seguras
-   qualquer armazenamento de credencial deve usar apenas hashes de uma via criptograficamente fortes que não permitem ataques de força bruta. (use bcrypt, scrypt ou PBKDF2). O comprimento do sal deve ser de pelo menos 128 bits e pode ser armazenado em db (previne [ataques arco-íris](http://security.stackexchange.com/questions/379/what-are-rainbow-tables-and-how-are-they-used))
-   impor a alteração de senhas temporárias ou padrão quando são usadas
-   links de redefinição de senha devem ser limitados por tempo e uso único apenas
-   impedir que os usuários reumem uma senha
-   notificar usuários quando ocorre uma redefinição de senha
-   indicar a última tentativa de login para um usuário
-   pensar cuidadosamente sobre as implicações de usar "Lembre-se de mim"
-   re-autenticar usuários antes de realizar qualquer operação crítica, como o upload de arquivos
-   mais seguro: use autenticação multifa (MFA / 2FA) para obter senhas únicas (OTP). Favoreça [o Google Authenticator](https://en.wikipedia.org/wiki/Google_Authenticator), [Authy](https://www.authy.com/developers/) etc. sobre SMS (que tem padrões de criptografia fracos que permitem ataques man-in-the-middle)
-   considerar a introdução captcha após uma série de falhas de login
-   bloquear a conta após uma série de falhas de login por um determinado período de tempo
-   permitir que todos os usuários sejam conectados à força (por exemplo, invalidando todos os cookies de sessão)
-   estar preparado para mudar o mecanismo de hashing; garantir que você pode fazê-lo na hora quando os usuários precisam fazer login

## Gerenciamento de sessões

-   Os IDs de sessão devem ser únicos, não adiváveis e não sequenciais e adequadamente longos
-   use [httpOnly](https://www.owasp.org/index.php/HttpOnly), [seguros](https://www.owasp.org/index.php/SecureFlag), [cookies de sessão](http://cookiecontroller.com/internet-cookies/session-cookies/) para armazenar ids de sessão do lado do cliente
-   use httpOnly, cookies de sessão criptografados, seguros, assinados, se você quiser armazenar dados de sessão do lado do cliente
-   definir o caminho e o domínio para cookies para um valor adequadamente restrito
-   tempo limite de inatividade da sessão deve ser o mais curto possível
-   logout deve estar sempre disponível
-   ids de sessão de expiração após um período definido (para reduzir o impacto do sequestro de sessão)
-   invalidação de sessão (devido ao tempo limite, logout, expiração ou reutilização não autorizada) deve excluir imediatamente o id de sessão + os dados da sessão no servidor e no cliente (inclua uma diretiva Set-Cookie na resposta com um tempo de expiração no passado)
-   sempre crie uma nova sessão (portanto, novo id de sessão em um cookie) ao re autenticar, para evitar ataques [de fixação de sessão](https://www.owasp.org/index.php/Session_fixation); nunca armazene o id de sessão na URL
-   dados de sessão sensíveis devem ser armazenados no servidor
-   limpar dados de sessão do lado do servidor com frequência
-   não permitem logins simultâneos para o mesmo id do usuário
-   Os identificadores da sessão devem estar apenas no cabeçalho de cookie HTTP (não em uma solicitação GET ou em qualquer outro lugar)
-   para dados confidenciais requerem por solicitação em vez de tokens por sessão

## Integração

-   certifique-se de que há um esquema claro e bem definido usando, por exemplo, [JSON Schema](http://json-schema.org/) para cada ponto de integração e garantir que toda a entrada seja validada contra este esquema
-   testes automatizados devem verificar se as mensagens estão em conformidade com o esquema de espera para cada ponto de integração
-   entradas limite de taxa e verificar o tamanho da carga -- considere usar o padrão de design [do disjuntor](http://martinfowler.com/bliki/CircuitBreaker.html) em pontos de integração

## Segurança de comunicação

-   implementar criptografia de transporte para a transmissão de todas as informações confidenciais e suplemento com criptografia da carga, se necessário
-   garantir que os certificados TLS cubram o domínio e os sub domínios, sejam atuais e de uma autoridade de certificado confiável e sejam instalados com certificados intermediários quando necessário
-   especificar codificações de caracteres para todas as conexões
-   não permitem a mistura de conteúdo TLS e não-TLS
-   parâmetros de filtro contendo informações confidenciais no cabeçalho do remetente HTTP ao vincular a sites externos

## Uploads de arquivos

-   requerem autenticação primeiro, se apropriado
-   verificar tipo de arquivo, caracteres, tamanho etc.
-   varredura de vírus / malware, de preferência em um recipiente descartável
-   turno de privilégios executivos em diretórios de upload de arquivos e garantir que o arquivo seja lido apenas

## Segurança de serviços web

-   OWASP tem uma boa [planilha de segurança de DESCANSO](https://www.owasp.org/index.php/REST_Security_Cheat_Sheet)
-   favorecer JSON Web Tokens [(JWT)](https://jwt.io/) no cabeçalho como o formato para tokens de segurança e proteger sua integridade com um [MAC](https://en.wikipedia.org/wiki/Message_authentication_code)
-   use chaves de API no cabeçalho de autorização para estrangular clientes e reduzir o impacto de ataques de negação de serviço. Não confie neles para proteger recursos sensíveis porque eles são fáceis de comprometer
-   considere [certs de cliente](https://www.owasp.org/index.php/Transport_Layer_Protection_Cheat_Sheet#Client-Side_Certificates) TLS de 2 vias se seu aplicativo estiver se integrando através de um serviço web. No entanto, a implementação e a solução de problemas podem ser onerosas e revogar e reemitar certificados de complexidade
-   whitelist métodos permitidos e rejeitar não permitido com 405
-   estar ciente das necessidades de autorização na comunicação de serviço-a-serviço, e evitar o problema confuso do deputado quando um serviço chama outro sem fornecer as informações de autorização adequadas. O uso [de ids externos](https://aws.amazon.com/blogs/security/how-to-use-external-id-when-granting-access-to-your-aws-resources/) pode ajudar aqui.
-   o servidor deve sempre enviar o cabeçalho tipo conteúdo com o tipo de conteúdo correto e incluir um charset
-   rejeitar um pedido com 406 resposta não aceitável se o Tipo de Conteúdo não for suportado
-   desativar cabeçalhos CORS, a menos que sejam necessárias chamadas de domínio cruzado. Se forem necessários, seja o mais específico possível
-   considere erros de validação de tokens de registro para ajudar a detectar ataques

# Testes automatizados de segurança

Embora os projetos tenham um teste de penetração e verificação de saúde de TI, estas são tarefas periódicas. Também encorajamos as equipes a executar ferramentas automatizadas de teste de segurança para que possam pegar vulnerabilidades de segurança muito mais rapidamente. Recomendamos que as ferramentas de teste de segurança sejam executadas regularmente, não apenas quando o código é empurrado. Isso ocorre porque novas vulnerabilidades podem surgir sem que você tenha feito qualquer alteração na sua aplicação.

## Teste de segurança de dependência do NodeJS - Snyk

O Snyk verifica as dependências de aplicativos NodeJS para obter vulnerabilidades.

Recomendamos 2 maneiras de usar o Snyk:

1.  Integração do Github O Snyk pode aumentar automaticamente os PRs contra o seu repositório para corrigir vulnerabilidades, mais detalhes disponíveis aqui:  
    [https://snyk.io/docs/github/](https://snyk.io/docs/github/)
    
2.  Manualmente, o Snyk tem um CLI que você pode usar manualmente ou como parte de um pipeline ci. A maneira mais fácil de configurar isso é:
    

-   Assistente **snyk** de execução local
-   O assistente oferecerá adicionar código ao seu pacote json para executar testes de vulnerabilidade snyk ao lado de seus trabalhos habituais de teste npm
-   Aceite isso e quaisquer trabalhos de teste de CI falharão se houver novas vulnerabilidades

## Teste de segurança de dependência scala - Verificação de Dependência do SBT

Dependência do SBT Verifique suas dependências no banco de dados OWASP de módulos vulneráveis. Funciona, mas é relativamente imaturo, então não é tão fácil de usar quanto Snyk. [Você pode encontrar a verificação de dependência do SBT aqui](https://github.com/albuch/sbt-dependency-check)

## Teste de segurança de dependência java

OWASP fornece algumas ferramentas para isso, que inclui uma ferramenta de linha de comando, bem como um plugin maven. Esta é essencialmente a mesma ferramenta que a Dependência do SBT Verificar acima, apenas mais para Java. [Você pode encontrar Verificação de Dependência para Java aqui](https://www.owasp.org/index.php/OWASP_Dependency_Check)

## Bibliotecas específicas da tecnologia

-   Node
    -   [Lusca](https://github.com/krakenjs/lusca)
    -   [helmet](https://github.com/helmetjs/helmet)
    -   [node security project](https://nodesecurity.io/) [no GitHub](https://github.com/nodesecurity/nsp)

## Recursos e livros úteis

-   [Guia nacional de práticas de desenvolvimento seguro do Centro Nacional de Segurança Cibernética](https://github.com/ukncsc/secure-development-and-deployment/)
-   [Projeto Top 10 da OWASP](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
-   [Segurança de aplicativos web - Um guia para iniciantes](https://www.mhprofessional.com/product.php?isbn=0071776168)
-   [Segurança de identidade e dados para desenvolvedores web](http://shop.oreilly.com/product/0636920044376.do)
-   [Manual do Hacker de Aplicativo Web](http://eu.wiley.com/WileyCDA/WileyTitle/productCd-1118026470.html)
-   [Considerações de segurança do protocolo HTTP](https://www.w3.org/Protocols/rfc2616/rfc2616-sec15.html)
-   [Folhas de trapaça OWASP](https://www.owasp.org/images/9/9a/OWASP_Cheatsheets_Book.pdf)
-   [OWASP práticas de codificação seguras](https://www.owasp.org/images/0/08/OWASP_SCP_Quick_Reference_Guide_v2.pdf)
-   [Decisões de segurança da CESG Enterprise](https://www.cesg.gov.uk/guidance/security-considerations-common-enterprise-it-decisions)
-   [Orientação de senha do CESG](https://www.cesg.gov.uk/guidance/password-guidance-simplifying-your-approach)
-   [CESG 10 passos para a segurança cibernética](https://www.cesg.gov.uk/10-steps-cyber-security)
-   [CESG Protegendo dados pessoais e em massa](https://www.cesg.gov.uk/guidance/protecting-bulk-personal-data)
-   [Princípios de Design de Segurança cesg para serviços digitais](https://www.cesg.gov.uk/guidance/security-design-principles-digital-services-0)
-   [Orientação CESG TLS para serviços externos](https://www.cesg.gov.uk/guidance/transport-layer-security-tls-external-facing-services)
