## Clickjacking (Clickjacking)

Autor: Gustav Rydstedt

Contribuidor(es):Wichers, Jmanico, MichaelCoates, Till Maas, Ajay, Michael Monsivais, Arun Kumar V, Abhinav, Neil Smithline, kingthorin, Shai Alon

Clickjacking, também conhecido como "ataque de reparação da interface do usuário", é quando um invasor usa várias camadas transparentes ou opacas para enganar um usuário a clicar em um botão ou link em outra página quando eles estavam pretendendo clicar em a página de nível superior. Assim, o atacante está "sequestrando" cliques significando para sua página e roteando-os para outra página, provavelmente de propriedade de outro aplicativo, domínio ou ambos.

Usando uma técnica semelhante, as teclas pressionadas também podem ser sequestradas. Com um combinação cuidadosamente elaborada de folhas de estilo, iframes e caixas de texto, um o usuário pode ser levado a acreditar que está digitando a senha para o seu e-mail ou conta bancária, mas em vez disso estão digitando em um quadro invisível controlado pelo atacante.

## Exemplos

Por exemplo, imagine um invasor que cria um site que tem um botão nele que diz "clique aqui para um iPod gratuito". No entanto, além disso, página da Web, o invasor carregou um iframe com sua conta de e-mail e alinhou exatamente o botão "excluir todas as mensagens" diretamente em cima do Botão "iPod grátis". A vítima tenta clicar no botão "iPod grátis" mas, em vez disso, realmente clicou no invisível "excluir todas as mensagens" botão. Em essência, o invasor "sequestrou" o clique do usuário, portanto, o nome "Clickjacking".

Um dos exemplos mais notórios de Clickjacking foi um ataque contra as configurações do plug-in do Adobe Flash página. Ao carregar esta página em um iframe invisível, um invasor pode enganar um usuário para alterar as configurações de segurança do Flash, dando permissão para qualquer animação em Flash para utilizar o microfone e a câmera do computador.

Clickjacking também virou notícia na forma de umTwitter verme. Este ataque de clickjacking convenceu os usuários a clicar em um botão que fez com que eles retuitassem a localização da página maliciosa, e propagado massivamente.

Também houve ataques de clickjacking abusando do "Curtir" do Facebook funcionalidade. Os invasores podem enganar os usuários logados do Facebook para arbitrariamente como páginas de fãs, links, grupos, etc

## Defendendo-se contra o Clickjacking

Existem três maneiras principais de evitar o clickjacking:

Enviar os cabeçalhos de resposta de diretiva de antepassados de quadros CSP (Content Security Policy) apropriados que instruem o navegador a não permitir o enquadramento de outros domínios. Os cabeçalhos HTTP mais antigos são usados para degradação normal e compatibilidade de navegador mais antiga.X-Frame-Options
Definir corretamente os cookies de autenticação com(ou), a menos que eles explicitamente precisem (o que é raro).SameSite=StrictLaxNone
Empregar código defensivo na interface do usuário para garantir que o quadro atual seja a janela de nível mais alto.
Para obter mais informações sobre a defesa de Clickjacking, consulte aFolha de Fraude de Defesa de Clickjacking.

## Referências

- [Por que estou ansioso com o Clickjacking?](https://www.linkedin.com/pulse/20141202104842-120953718-why-am-i-anxious-about-clickjacking)
- Uma compreensão básica do ataque de clickjacking
- https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors
- Recurso de desenvolvedor Mozilla no cabeçalho de resposta Content-Security-Policy frame-ancestors.
- https://developer.mozilla.org/en-US/docs/The_X-FRAME-OPTIONS_response_header
- Recurso de desenvolvedor Mozilla no cabeçalho de resposta X-Frame-Options.
- [Busting Frame Busting: Um estudo das vulnerabilidades de clickjacking nos principais sites](http://w2spconf.com/2010/papers/p27.pdf)
- Um estudo do Stanford Web Security Group descrevendo problemas com o código de quebra de quadro implantado.
- [Clickjacking, Teoria Sec](http://www.sectheory.com/clickjacking.htm)
- Um artigo de Robert Hansen definindo o termo, suas implicações contra Flash no momento da redação deste artigo e uma linha do tempo de divulgação.
- https://www.codemagi.com/blog/post/194
- Defesa de quebra de quadros para navegadores herdados que não oferecem suporte a cabeçalhos X-Frame-Option.
- Um filtro de servlet J2EE simples que envia cabeçalhos anti-enquadramento para o navegador.
- [CSP frame-ancestors vs. X-Frame-Options para prevenção de Clickjacking](https://medium.com/@shaialon/csp-frame-ancestors-vs-x-frame-options-for-clickjacking-prevention-30383a713772)


---

[Artigo Original](https://owasp.org/www-community/attacks/Clickjacking)
