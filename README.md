## Aplicação isomórfica com React e Nashorn

# 1. Introdução
Neste tutorial, vamos entender o que exatamente é um aplicativo isomórfico. Também discutiremos o Nashorn, o mecanismo JavaScript integrado ao Java.

Além disso, exploraremos como podemos usar o Nashorn junto com uma biblioteca front-end como React para criar um aplicativo isomórfico.

# 2. Um pouco de história
Tradicionalmente, os aplicativos cliente e servidor eram escritos de uma maneira bastante pesada no lado do servidor. Pense no PHP como um mecanismo de script que gera principalmente HTML estático e navegadores da web que os renderizam.

A Netscape veio com o suporte de JavaScript em seu navegador em meados dos anos noventa. Isso começou a mudar parte do processamento do lado do servidor para o navegador do lado do cliente. Por muito tempo, os desenvolvedores lutaram com diferentes problemas relacionados ao suporte a JavaScript em navegadores da web.

Com a crescente demanda por uma experiência do usuário mais rápida e interativa, o limite já estava sendo empurrado com mais força. Um dos primeiros frameworks que mudou o jogo foi o jQuery. Ele trouxe várias funções fáceis de usar e suporte muito aprimorado para AJAX.

Logo, muitos frameworks para desenvolvimento front-end começaram a aparecer, o que melhorou muito a experiência do desenvolvedor. Começando com AngularJS do Google, React do Facebook e, posteriormente, Vue, eles começaram a chamar a atenção do desenvolvedor.

Com suporte a navegadores modernos, estruturas notáveis ​​e ferramentas necessárias, as marés estão mudando amplamente para o lado do cliente.

Uma experiência imersiva em dispositivos portáteis cada vez mais rápidos requer mais processamento do lado do cliente.

# 3. O que é um aplicativo isomórfico?
Então, vimos como os frameworks de front-end estão nos ajudando a desenvolver um aplicativo da web em que a interface do usuário é completamente renderizada no lado do cliente.

No entanto, também é possível usar a mesma estrutura no lado do servidor e gerar a mesma interface de usuário.

Agora, não temos que nos limitar a soluções apenas do lado do cliente ou apenas do lado do servidor necessariamente. A melhor maneira é ter uma solução em que o cliente e o servidor possam processar o mesmo código de front-end e gerar a mesma interface de usuário.

Há benefícios nessa abordagem, que discutiremos mais tarde.

Esses aplicativos da web são chamados de Isomórficos ou Universais. Agora, a linguagem do lado do cliente é mais exclusivamente JavaScript. Portanto, para um aplicativo isomórfico funcionar, temos que usar JavaScript também no lado do servidor.

Node.js é de longe a escolha mais comum para construir um aplicativo renderizado do lado do servidor.

# 4. O que é Nashorn?
Então, onde o Nashorn se encaixa e por que devemos usá-lo? Nashorn é um mecanismo JavaScript empacotado por padrão com Java. Portanto, se já temos um back-end de aplicativo da web em Java e queremos construir um aplicativo isomórfico, Nashorn é muito útil!

Nashorn foi lançado como parte do Java 8. Este é principalmente focado em permitir aplicativos JavaScript embutidos em Java.

Nashorn compila JavaScript na memória para Java Bytecode e o passa para a JVM para execução. Isso oferece melhor desempenho em comparação com o motor anterior, Rhino.

# 5. Criação de um aplicativo isomórfico
Já passamos por contexto suficiente agora. Nosso aplicativo aqui exibirá uma sequência de Fibonacci e fornecerá um botão para gerar e exibir o próximo número na sequência. Vamos criar um aplicativo isomórfico simples agora com back-end e front-end:

Front-end: um front-end simples baseado em React.js
Back-end: um back-end simples de Spring Boot com Nashorn para processar JavaScript

# 6. Front-end do aplicativo
Estaremos usando React.js para criar nosso front end. React é uma biblioteca JavaScript popular para a construção de aplicativos de página única. Isso nos ajuda a decompor uma interface de usuário complexa em componentes hierárquicos com estado opcional e vinculação de dados unilateral.

O React analisa essa hierarquia e cria uma estrutura de dados na memória chamada DOM virtual. Isso ajuda o React a encontrar mudanças entre diferentes estados e fazer mudanças mínimas no DOM do navegador.

### 6.1. Componente React
Vamos criar nosso primeiro componente React:

```
var App = React.createClass({displayName: "App",
    handleSubmit: function() {
    	var last = this.state.data[this.state.data.length-1];
    	var secondLast = this.state.data[this.state.data.length-2];
        $.ajax({
            url: '/next/'+last+'/'+secondLast,
            dataType: 'text',
            success: function(msg) {
                var series = this.state.data;
                series.push(msg);
                this.setState({data: series});
            }.bind(this),
            error: function(xhr, status, err) {
                console.error('/next', status, err.toString());
            }.bind(this)
        });
    },
    componentDidMount: function() {
    	this.setState({data: this.props.data});
    },	
    getInitialState: function() {
        return {data: []};
    },	
    render: function() {
        return (
            React.createElement("div", {className: "app"},
            	React.createElement("h2", null, "Fibonacci Generator"),
            	React.createElement("h2", null, this.state.data.toString()),
                React.createElement("input", {type: "submit", value: "Next", onClick: this.handleSubmit})
            )     
        );
    }
});
```

Agora, vamos entender o que o código acima está fazendo:

- Para começar, definimos um componente de classe no React chamado “App”;
- A função mais importante dentro deste componente é “render”, que é responsável por gerar a interface do usuário;
- Fornecemos um estilo className que o componente pode usar;
- Estamos utilizando o estado do componente aqui para armazenar e exibir a série;
- Enquanto o estado inicializa como uma lista vazia, ele busca os dados passados para o componente como um suporte quando o componente é montado;
- Por fim, ao clicar no botão “Adicionar”, é feita uma chamada jQuery ao serviço REST;
- A chamada busca o próximo número na sequência e o anexa ao estado do componente;
- A mudança no estado do componente renderiza novamente o componente automaticamente.

### 6.2. Usando o Componente React
O React procura por um elemento denominado “div” na página HTML para ancorar seu conteúdo. Tudo o que precisamos fazer é fornecer uma página HTML com este elemento “div” e carregar os arquivos JS:

```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Hello React</title>
    <script type="text/javascript" src="js/react.js"></script>
    <script type="text/javascript" src="js/react-dom.js"></script>
    <script type="text/javascript" src="http://code.jquery.com/jquery-1.10.0.min.js"></script>
</head>
<body>
<div id="root"></div>
<script type="text/javascript" src="app.js"></script>
<script type="text/javascript">
    ReactDOM.render(
        React.createElement(App, {data: [0,1,1]}),
        document.getElementById("root")
    );
</script>
</body>
</html>
```

Então, vamos ver o que fizemos aqui:

- Importamos as bibliotecas JS necessárias, react, react-dom e jQuery;
- Em seguida, definimos um elemento “div” denominado “root”;
- Também importamos o arquivo JS com nosso componente React;
- Em seguida, chamamos o componente React de “App” com alguns dados iniciais, os três primeiros números de Fibonacci.

# 7. Back-end do aplicativo
Agora, vamos ver como podemos criar um back-end adequado para nosso aplicativo. Já decidimos usar Spring Boot junto com Spring Web para construir este aplicativo. Mais importante, decidimos usar o Nashorn para processar o front-end baseado em JavaScript que desenvolvemos na última seção.

### 7.1. Dependências Maven
Para nosso aplicativo simples, usaremos JSP junto com Spring MVC, portanto, adicionaremos algumas dependências ao nosso POM:

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
    <groupId>org.apache.tomcat.embed</groupId>
    <artifactId>tomcat-embed-jasper</artifactId>
    <scope>provided</scope>
</dependency>
```

O primeiro é a dependência de boot de primavera padrão para um aplicativo da web. O segundo é necessário para compilar JSPs.

### 7.2. Web Controller
Vamos agora criar nosso controlador da web, que irá processar nosso arquivo JavaScript e retornar um HTML usando JSP:

```
@Controller
public class MyWebController {
    @RequestMapping("/")
    public String index(Map<String, Object> model) throws Exception {
        ScriptEngine nashorn = new ScriptEngineManager().getEngineByName("nashorn");
        nashorn.eval(new FileReader("static/js/react.js"));
        nashorn.eval(new FileReader("static/js/react-dom-server.js"));
        nashorn.eval(new FileReader("static/app.js"));
        Object html = nashorn.eval(
          "ReactDOMServer.renderToString(" + 
            "React.createElement(App, {data: [0,1,1]})" + 
          ");");
        model.put("content", String.valueOf(html));
        return "index";
    }
}
```

Então, o que exatamente está acontecendo aqui:

- Buscamos uma instância de ScriptEngine do tipo Nashorn de ScriptEngineManager;
- Em seguida, carregamos as bibliotecas relevantes para React, react.js e react-dom-server.js;
- Também carregamos nosso arquivo JS que tem nosso componente react “App”;
- Por fim, avaliamos um fragmento JS criando um elemento react com o componente “App” e alguns dados de seed;
- Isso nos fornece uma saída de React, um fragmento de HTML como objeto;
- Passamos esse fragmento de HTML como dados para a visão relevante - o JSP.

### 7.3. JSP
Agora, como processamos esse fragmento HTML em nosso JSP?

Lembre-se de que o React adiciona automaticamente sua saída a um elemento denominado “div” - “root” em nosso caso. No entanto, adicionaremos nosso fragmento HTML gerado pelo servidor ao mesmo elemento manualmente em nosso JSP.

Vamos ver como o JSP se parece agora:

```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Hello React!</title>
    <script type="text/javascript" src="js/react.js"></script>
    <script type="text/javascript" src="js/react-dom.js"></script>
    <script type="text/javascript" src="http://code.jquery.com/jquery-1.10.0.min.js"></script>
</head>
<body>
<div id="root">${content}</div>
<script type="text/javascript" src="app.js"></script>
<script type="text/javascript">
	ReactDOM.render(
        React.createElement(App, {data: [0,1,1]}),
        document.getElementById("root")
    );
</script>
</body>
</html>
```

Esta é a mesma página que criamos anteriormente, exceto pelo fato de que adicionamos nosso fragmento HTML na div “raiz”, que estava vazia anteriormente.

### 7.4. Controlador REST
Por fim, também precisamos de um endpoint REST do lado do servidor que nos forneça o próximo número de Fibonacci na sequência:

```
@RestController
public class MyRestController {
    @RequestMapping("/next/{last}/{secondLast}")
    public int index(
      @PathVariable("last") int last, 
      @PathVariable("secondLast") int secondLast) throws Exception {
        return last + secondLast;
    }
}
```

Nada sofisticado aqui, apenas um controlador Spring REST simples.

# 8. Executando o aplicativo
Agora que completamos nosso front-end, bem como nosso back-end, é hora de executar o aplicativo.

Devemos iniciar o aplicativo Spring Boot normalmente, usando a classe de bootstrapping:

```
@SpringBootApplication
public class Application extends SpringBootServletInitializer {
    @Override
    protected SpringApplicationBuilder configure(SpringApplicationBuilder application) {
        return application.sources(Application.class);
    }
    public static void main(String[] args) throws Exception {
        SpringApplication.run(Application.class, args);
    }
}
```

Quando executamos esta classe, Spring Boot compila nossos JSPs e os disponibiliza no Tomcat incorporado junto com o resto do aplicativo da web.

Vamos entender a sequência de eventos:

- O navegador solicita esta página;
- Quando a solicitação desta página chega, o controlador da web Spring processa os arquivos JS;
- O mecanismo Nashorn gera um fragmento HTML e o passa para o JSP;
- JSP adiciona este fragmento HTML ao elemento div “raiz”, finalmente retornando a página HTML acima;
- O navegador renderiza o HTML, enquanto isso começa a baixar os arquivos JS;
- Finalmente, a página está pronta para ações do lado do cliente - podemos adicionar mais números na série.

O importante a entender aqui é o que acontece se o React encontrar um fragmento HTML no elemento “div” de destino. Nesses casos, o React compara esse fragmento com o que ele possui e não o substitui se encontrar um fragmento legível. Isso é exatamente o que capacita a renderização do lado do servidor e aplicativos isomórficos.

# 9. O que mais é possível?
Em nosso exemplo simples, apenas arranhamos a superfície do que é possível. Os aplicativos front-end com estruturas modernas baseadas em JS estão ficando cada vez mais poderosos e complexos. Com essa complexidade adicional, há muitas coisas que precisamos cuidar:

- Criamos apenas um componente React em nossa aplicação quando, na realidade, podem ser vários componentes formando uma hierarquia que passa dados através de adereços;
- Gostaríamos de criar arquivos JS separados para cada componente para mantê-los gerenciáveis ​​e gerenciar suas dependências por meio de “exportações / requerer” ou “exportar / importar”;
- Além disso, pode não ser possível gerenciar o estado apenas nos componentes; podemos querer usar uma biblioteca de gerenciamento de estado como Redux;
- Além disso, podemos ter que interagir com serviços externos como efeitos colaterais das ações; isso pode exigir que usemos um padrão como redux-thunk ou Redux-Saga;
- Mais importante ainda, gostaríamos de aproveitar JSX, uma extensão de sintaxe para JS para descrever a interface do usuário.

Embora o Nashorn seja totalmente compatível com JS puro, ele pode não oferecer suporte a todos os recursos mencionados acima. Muitos deles requerem trans-compilação e polyfills devido à compatibilidade JS.

A prática usual em tais casos é aproveitar um empacotador de módulo como Webpack ou Rollup. O que eles fazem principalmente é processar todos os arquivos de origem do React e agrupá-los em um único arquivo JS junto com todas as dependências. Isso invariavelmente requer um compilador JavaScript moderno como o Babel para compilar o JavaScript para ser compatível com versões anteriores.

O pacote final tem apenas o bom e velho JS, que os navegadores podem entender e que o Nashorn também adere.

# 10. Benefícios de um aplicativo isomórfico
Então, falamos muito sobre aplicativos isomórficos e até criamos um aplicativo simples agora. Mas por que exatamente deveríamos nos preocupar com isso? Vamos entender alguns dos principais benefícios de usar um aplicativo isomórfico.

### 10.1. Renderização da primeira página
Um dos benefícios mais significativos de um aplicativo isomórfico é a renderização mais rápida da primeira página. No aplicativo renderizado do lado do cliente típico, o navegador começa fazendo o download de todos os artefatos JS e CSS.

Depois disso, eles carregam e começam a renderizar a primeira página. Se enviarmos a primeira página renderizada do lado do servidor, isso pode ser muito mais rápido, proporcionando uma experiência de usuário aprimorada.

### 10.2. SEO amigável
Outro benefício frequentemente citado com a renderização do lado do servidor está relacionado ao SEO. Acredita-se que os bots de pesquisa não são capazes de processar JavaScript e, portanto, não veem uma página de índice renderizada no lado do cliente por meio de bibliotecas como React. Uma página renderizada do lado do servidor, portanto, é mais amigável para o SEO. É importante notar, porém, que os bots de mecanismos de pesquisa modernos afirmam processar JavaScript.

# 11. Conclusão
Neste tutorial, examinamos os conceitos básicos de aplicativos isomórficos e o mecanismo Nashorn JavaScript. Exploramos mais como construir um aplicativo isomórfico com Spring Boot, React e Nashorn.

Em seguida, discutimos as outras possibilidades de estender o aplicativo front-end e os benefícios de usar um aplicativo isomórfico.