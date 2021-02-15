# GuiaSegurancaParaDevs
:notebook: Guia de segurança para DevComputeiros com especialidade em GED (Gambiarra, Exploit e Desenvolvimento)

### O Público Alvo

Problemas com segurança acontecem por 2 razões - 

1. Desenvolvedores que acabaram de começar a programar e não sabem dizer a diferença entre usar MD5 ou bcrypt.
2. Desenvolvedores que sabem das coisas, mas esquecem/ignoram.

Nossas explicações detalhadas devem ajudar a primeira categoria enquanto esperamos que nosso checklist ajude a segunda a criar sistemas mais seguros. Não é o intuito aqui criar um guia compreensivo, apenas a abranger elementos baseados nos problemas mais comuns que descobrimos no passado.



### Conteúdo

1. [Checklist de Segurança](security-checklist.md)
2. O que pode dar errado?
3. Transportando dados de forma segura: HTTP explicado
4. Eu sou quem digo que sou: Autenticação 	
    1. 4.1 Autenticação baseada em formulário    
    2. 4.2 Autenticação básica	
    3. 4.3 Um não é o suficiente: 2 fatores, 3 fatores... 	
    4. 4.4 Por que usar mensgens de texto inseguras? HOTP & TOTP 	
    5. 4.5 Lidando com reset de passwords     
5. O que eu posso fazer? Autorização	
    1. 5.1 Autorização baseada em token
    2. 5.2 OAuth & OAuth2  
    3. 5.3 JWT	
6. Não confie em ninguém: Entradas (inputs) de usuários são DO MAL	
    1. 6.1 Sanitizando Entradas  	
    2. 6.2 Sanitizando Saídas 
    3. 6.3 Scriptação Entre Sites (Cross Site Scripting aka XSS)  	
    4. 6.4 Ataques de Injeção (Injection) 	
    5. 6.5 Uploas de Usuário 	
    6.6.6 Entradas de usuários à prova de alterações 	
7. Texto puro (plaintext) != Codificação (encoding) != Criptografia  != Hashing  	
    1. 7.1 Esquemas comuns de codificação 		
    2. 7.2 Criptografia 	
    3. 7.3 Hashing & Funções de uma via 	
    4.7.4 Guia rápido de velocidade de hashing 	
8. dadada, 123456, futebol@123: Senhas 	
    1. 8.1 Políticas de Senha 		
    2. 8.2 Armazenamento de Senhas  
    3. 8.3 Uma Vida sem Senhas 
9. Chave Pública de Criptografia  
10. Por favor, lembre-se de mim: Lidando com Sessões 		
    1. 10.1 Onde salvar o estado? 
    2. 10.2 Invalidando sessões
    3. 10.3 O monstro dos cookies & você 	
11. Consertando a Segurança, um cabeçalho de cada vez 
    1. 11.1 Cabeçalhos web seguros 
    2. 11.2 Checagem de integridade de dados para códigos de terceiros 
    3. 11.3 Fixando Certificados 	
12. Erros de Configuração 
    1. 12.0 Provisionamento em nuvem: Portas, Shodan & AWS 	
    2. 2.1 Amor, você deixou o debug ligado 	
    3. 12.2 Logar (ou não logar) 	
    4. 12.3 Monitoramento  
    5. 12.4 Princípio do Menos Privilegiado 
    6. 12.5 Limitação de Avaliações & Captchas 
    7. 12.6 Armazenando de segredos de projetos e senhas em arquivo 
    8. 12.7 DNS: De subdomínios e projetos menores esquecidos  	
    9. 12.7 Patching & Updates  	
13. Quando o vilão chega: Ataques 	
    1. 13.1 Sequestro de cliques 	
    2. 13.2 Falsificação de Requisições Entre Sites (CSRF) 	
    3. 13.3 Negação de Serviço (DoS) 	
    4. 13.4 Falsificação de Requisições pelo Servidor (SSRF) 	
14. [Estatísticas sobre vulnerabilidades encontradas em empresas de internet](vulnerabilities-stats.md)   
15. Sobre reinventar a roda, e torná-la quadrada 
    1. 15.1 Bibliotecas e pacotes de segurança para Python  
    2. 15.2 Bibliotecas e pacotes de segurança para Node/JS 	
    3. 15.3 Aprendendo sobre Recursos     
16. Mantendo uma boa higiene de segurança 
17. Segurança vs Usabilidade 
18. Voltando pra casa 1: O Checklist de Segurança explicado 
 



### Quem somos?

Somos DevComputeiros cansados de observar como os desenvolvedores diminuem a barreira para chamar alguma coisa de hack, escrevendo código inseguro. O seguinte projeto servirá de base de estudo e montagem de um ambiente para teste de segurança e boas práticas de código seguro.

* Se você discorda de algo ou achou um bug, por favor abra um issue ou ande um PR.*.

*Nota: Esse repositório é uma tradução do projeto [FallibleInc](https://github.com/FallibleInc/security-guide-for-developersFail). Sinta-se a vontade para me sugerir mais conteúdos aqui no githubss pra traduzir.*
