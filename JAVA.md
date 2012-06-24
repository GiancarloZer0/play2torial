# Bem-vindo ao Play2torial para Java!
====================================
Este tutorial irá ajudá-lo e ensiná-lo a construir uma aplicação web em Java com o Play Framework 2.

Antes de começar você precisará instalar o [git](http://git-scm.com/) e o [Play 2](http://www.playframework.org/documentation/2.0.1/Installing).

Teste o funcionamento do comando `play` executando:

    play help

Você deverá ver algo semelhante a:

           _            _ 
     _ __ | | __ _ _  _| |
    | '_ \| |/ _' | || |_|
    |  __/|_|\____|\__ (_)
    |_|            |__/ 
             
    play! 2.0.1, http://www.playframework.org
    
    Welcome to Play 2.0!



Beleza! Você está pronto pra começar!



Criar uma aplicação Play
------------------------


Crie uma nova aplicação Play 2 chamada "play2tutorial" executando:

    play new play2torial

Quando solicitado selecione a opção 2 para criar uma aplicação Java.



No diretório da aplicação `play2torial` recém-criada crie um novo repositório git executando:

    git init


Adicione os arquivos ao repositório git e sincronize-os (dê commit):

    git add .
    git commit -m init


Ao longo deste tutorial você poderá acompanhar seu progresso comparando com o tutorial oficial. Para fazer isso adicione o tutorial oficial como um novo git remote chamado `upstream` executando:

    git remote add upstream git://github.com/jamesward/play2torial.git


Busque (dê fetch) o repositório remoto:

    git fetch upstream


Agora valide seu projeto local como correto executando:

    git diff upstream/java-new_project

Nota: O valor de configuração `application.secret` irá mudar, o que é normal. Além disso, às vezes o git vai avisar que algo está diferente, mesmo que não esteja. Isso ocorrerá provavelmente devido a diferenças na identação e criação de novas linhas. Você pode ignorar essas diferenças.



Configurar uma IDE
-----------------

Antes de iniciarmos um tour pelo app você pode, opcionalmente, gerar arquivos de projeto para IntelliJ ou Eclipse.

Para IntelliJ execute:

    play idea

Então, crie um novo projeto Java do zero no IntelliJ que use esse diretório como raiz. Não crie um módulo com o novo projeto. Ao invés disso, assim que criar o projeto, importe o arquivo play2torial.iml gerado como um módulo existente.

Para Eclipse execute:

    play eclipsify

Se você der um `git status`, verá que alguns arquivos não foram marcados para commit. Eles não deveriam estar no repositório git, então adicione ao arquivo `.gitignore` as seguintes linhas:

    /.idea
    /*.iml
    /.settings
    /.project
    /.classpath


Depois, dê commit na mudança:

    git commit -am "ignore IDE files"


Inicializar o servidor Play
--------------------------

Agora inicie a aplicação Play executando:

    play ~run

Abra a seguinte URL em seu navegador para verificar se a aplicação está funcionando:
[http://localhost:9000/](http://localhost:9000/)


Rotas
-----

O Play roteia requisições HTTP para o Controller usando as rotas definidas no arquivo `conf/routes`. O arquivo de rotas mapeia verbos HTTP e padrões de URL para os métodos do Controller. A rota que coincide à requisição que você acabou de fazer foi definida no arquivo "routes" com:

    GET     /                           controllers.Application.index()

Isso significa que quando uma requisição HTTP GET para a URL `/` é feita, ela será roteada para o método chamado `index` na classe `controllers.Application`.



Adicione uma nova rota para enviar requisições GET para `/foo` com uma chamada para `controllers.Application.index()` adicionando a seguinte linha no arquivo `conf/routes`:

    GET     /foo                        controllers.Application.index()


Agora tente abrir a seguinte URL em seu navegador: 
[http://localhost:9000/foo](http://localhost:9000/foo)

Você deverá ver a mesma mensagem de boas vindas que viu quando fez a requisição à URL `/`.

Dê commit e verifique suas mudanças:

    git commit -am "adicionada nova rota"
    git diff upstream/java-foo_route


Testar uma Rota
---------------


Agora que você tem uma nova rota para `/foo`, vamos criar um teste para essa rota. Crie um arquivo `test/FooTest.java` contendo:

    import org.junit.Test;
    import play.mvc.Result;
    
    import static play.test.Helpers.*;
    import static org.fest.assertions.Assertions.*;
    
    public class FooTest {
    
        @Test
        public void testFooRoute() {
            Result result = routeAndCall(fakeRequest(GET, "/foo"));
            assertThat(result).isNotNull();
        }
    
    }

Este simples teste vai simular uma chamada para `/foo` e validar o resultado como não-nulo. Execute o teste (você pode deixar o servidor Play rodando em outra janela):

    play test

Você deverá ver o seguinte:

    [info] FooTest
    [info] + FooTest.testFooRoute
    [info]
    [info]
    [info] Total for test FooTest
    [info] Finished in 0.131 seconds
    [info] 1 tests, 0 failures, 0 errors
    [info] Passed: : Total 1, Failed 0, Errors 0, Passed 1, Skipped 0


Dê commit e verifique as mudanças:

    git add test
    git commit -m "adicionado um teste para a rota foo"
    git diff upstream/java-test_route



Atualizar um Controller
-----------------------

O diretório `app` é a fonte principal de uma aplicação Play. Neste diretório há um diretório chamado `controllers` contendo um arquivo chamado `Application.java`, que por sua vez contém a classe `controllers.Application`.

O código do corpo do método `index()` é semelhante a este:

    return ok(index.render("Your new application is ready."));

Edite o arquivo `Application.java` e mude a string `Sua nova aplicação está pronta.` para `Olá, mundo!`. Salve o arquivo e recarregue a seguinte URL em seu navegador:
[http://localhost:9000/](http://localhost:9000/)

Perceba que agora o cabeçalho no topo da página exibe a mensagem `Olá, mundo!`. O Play recompilou o controller Java em segundo plano. Se você tivesse feito alguma mudança que não pudesse ser compilada, veria um erro de compilação exibido no seu navegador e em seu console.

Dê commit e verifique as mudanças:

    git commit -am "controller atualizado"
    git diff upstream/java-hello_controller



Testar um Controller
--------------------


Você pode fazer testes funcionais em um controller simplesmente criando um novo teste JUnit. Crie um novo arquivo chamado `test/ApplicationTest.java` contendo:

    import org.junit.Test;
    import play.mvc.Result;
    
    import static org.fest.assertions.Assertions.assertThat;
    import static play.mvc.Http.Status.OK;
    import static play.test.Helpers.*;
    
    public class ApplicationTest {
    
        @Test
        public void callIndex() {
            Result result = callAction(controllers.routes.ref.Application.index());
            assertThat(status(result)).isEqualTo(OK);
            assertThat(contentType(result)).isEqualTo("text/html");
            assertThat(charset(result)).isEqualTo("utf-8");
            assertThat(contentAsString(result)).contains("hello, world");
        }
    
    }

Isto simula uma requisição ao método `Application.index()` do controller e verifica se foi obtida a resposta esperada. Execute o teste:

    play test

Você deverá ver algo semelhante a:

    [info] FooTest
    [info] + FooTest.testFooRoute
    [info]
    [info]
    [info] Total for test FooTest
    [info] Finished in 0.191 seconds
    [info] 1 tests, 0 failures, 0 errors
    [info] ApplicationTest
    [info] + ApplicationTest.callIndex
    [info]
    [info]
    [info] Total for test ApplicationTest
    [info] Finished in 0.046 seconds
    [info] 1 tests, 0 failures, 0 errors
    [info] Passed: : Total 2, Failed 0, Errors 0, Passed 2, Skipped 0


Dê commit e verifique as mudanças:

    git add test/ApplicationTest.java
    git commit -am "added Application controller test"
    git diff upstream/java-test_controller



Atualize uma View
-----------------



O Play usa Scala para criar modelos no servidor (server-side). O controller `Application` renderiza o template `views.html.index`, que é compilado do arquivo `app/views/index.scala.html`. O template `index` obtém um parâmetro String:

    @(message: String)


Então o template `index` usa o template `main` (from `app/views/main.scala.html`) para obter uma página HTML básica:

    @main("Bem-vindo ao Play 2.0") {

O template principal passou um parâmetro String para o título da página e um parâmetro Html para o corpo da página. O corpo da página é a mensagem de boas vindas do Play, que vem de:

    @play20.welcome(message, style = "Java")


Mude a mensagem de boas vondas do Play para maiúsculas:

    @play20.welcome(message.toUpperCase, style = "Java")

Veja as mudanças no navegador:
[http://localhost:9000/](http://localhost:9000/)


Devido ao teste `test/ApplicationTest.java` usar esse template atualizado, atualize o teste para que ele verifique a string em caixa alta:

    assertThat(contentAsString(result)).contains("HELLO, WORLD");


Execute o teste:

    play test


Dê commit e verifique as mudanças:

    git commit -am "mudança na view"
    git diff upstream/java-hello_view



Testar uma View
---------------

Para testar um template diretamente, crie um novo arquivo `test/IndexTest.java` contendo:

    import org.junit.Test;
    import play.mvc.Content;
    
    import static org.fest.assertions.Assertions.assertThat;
    import static play.test.Helpers.*;
    
    public class IndexTest {
    
        @Test
        public void indexTemplate() {
            Content html = views.html.index.render("test");
            assertThat(contentType(html)).isEqualTo("text/html");
            assertThat(contentAsString(html)).contains("TEST");
        }
    
    }

Execute o teste:

    play test

Você deverá ver algo semelhante a:

    [info] FooTest
    [info] + FooTest.testFooRoute
    [info]
    [info]
    [info] Total for test FooTest
    [info] Finished in 0.176 seconds
    [info] 1 tests, 0 failures, 0 errors
    [info] ApplicationTest
    [info] + ApplicationTest.callIndex
    [info]
    [info]
    [info] Total for test ApplicationTest
    [info] Finished in 0.041 seconds
    [info] 1 tests, 0 failures, 0 errors
    [info] IndexTest
    [info] + IndexTest.indexTemplate
    [info]
    [info]
    [info] Total for test IndexTest
    [info] Finished in 0.004 seconds
    [info] 1 tests, 0 failures, 0 errors
    [info] Passed: : Total 3, Failed 0, Errors 0, Passed 3, Skipped 0

Verifique as mudanças:

    git add test/IndexTest.java
    git commit -m "adicionado teste index view"
    git diff upstream/java-test_view



Implantar sua aplicação na Nuvem com Heroku
-------------------------------------------

Implantar sua aplicação Play na nuvem usando Heroku é fácil! Primeiro, crie um novo arquivo no diretório raiz chamado `Procfile` que contenha:

    web: target/start -Dhttp.port=${PORT} ${JAVA_OPTS}

Isso indicará ao Heroku como iniciar a aplicação Play.


Dê commit e verifique as mudanças:

    git add Procfile
    git commit -m "adicionado Procfile ao Heroku"
    git diff upstream/java-heroku_create


Agora instale o Heroku Toolbelt:  
[http://toolbelt.heroku.com](http://toolbelt.heroku.com)


Crie uma conta no Heroku:  
[http://heroku.com/signup](http://heroku.com/signup)


Conecte-se à sua conta do Heroku através da linha de comando:

    heroku login


Forneça uma nova aplicação no Heroku:

    heroku create --stack cedar


Agora sincronize e envie a aplicação ao Heroku:

    git push heroku master

O Heroku construirá a aplicação com SBT e o executará em um [dyno](https://devcenter.heroku.com/articles/dynos).


Abra a aplicação, agora hospedada na nuvem, em seu navegador:

    heroku open



Criar um Model
--------------

O Play 2 com Java usa [Ebean](http://www.avaje.org/) para persistência RDBMS. Para configurar uma fonte de dados edite o arquivo `conf/application.conf`, descomentando as seguintes linhas:

    db.default.driver=org.h2.Driver
    db.default.url="jdbc:h2:mem:play"
    
    ebean.default="models.*"

	
Isto usará uma base de dados alocada em memória para uma fonte de dados chamada "default".


Crie uma nova classe Java para armazenar objetos `Task` em um pacote chamado `models`, criando um novo arquivo `app/models/Task.java` que contenha:

    package models;
    
    import play.db.ebean.Model;
    
    import javax.persistence.Entity;
    import javax.persistence.Id;
    
    @Entity
    public class Task extends Model {
    
        @Id
        public String id;
    
        public String contents;
    
    }


Crie um teste para o seu modelo criado um novo arquivo `test/TaskTest.java` contendo:

    import org.junit.Test;
    
    import static play.test.Helpers.fakeApplication;
    import static play.test.Helpers.running;
    
    import static org.fest.assertions.Assertions.assertThat;
    
    import models.Task;
    
    public class TaskTest {
    
        @Test
        public void create() {
            running(fakeApplication(), new Runnable() {
                public void run() {
                    Task task = new Task();
                    task.contents = "Escreve um teste";
                    task.save();
                    assertThat(task.id).isNotNull();
                }
            });
        }
    
    }


Execute o teste:

    play test

	
Ebean criará automaticamente um novo script de evolução de base de dados para você em um arquivo chamado `conf/evolutions/default/1.sql`.


Dê commit e verifique as mudanças:

    git add conf/evolutions app/models/Task.java test/TaskTest.java
    git commit -am "Adicionado um model Task, configuração de banco de dados e teste."
    git diff upstream/java-task_model



Criar Interface de Usuário (UI) para adicionar tarefas
------------------------------------------------------

Comece adicionando um novo método ao arquivo `app/controllers/Application.java` que mapeará os parâmetros de requisição para um objeto `Task`, salve-o e então redirecione para a página `index()`. Primeiro, adicione os seguintes imports:

    import models.Task;
    import play.data.Form;


Agora adicione um método chamado `addTask`:

        public static Result addTask() {
            Form<Task> form = form(Task.class).bindFromRequest();
            Task task = form.get();
            task.save();
            return redirect(routes.Application.index());
        }


Agora adicione uma nova rota no arquivo `conf/routes` que gerencie requisições `POST` para `/task` e gerencie as requisições com o método `Application.addTask()`:

    POST    /task                       controllers.Application.addTask()


	
Agora crie um formulário no template `app/views/index.scala.html` para adicionar novas `Tasks`. Substitua a linha `@play20.welcome` por:

        @message.toUpperCase
      
        @helper.form(action = routes.Application.addTask()) {
            <input name="contents"/>
            <input type="submit"/>
        }


		
Teste a nova UI carregando a aplicação no navegador: 
[http://localhost:9000/](http://localhost:9000/)

Você terá que executar as evoluções de banco de dados. Apenas clique no botão `Apply this script now!`.

Adicione uma nova `Task` e o navegador deverá atualizar a página index.

Dê commit e verifique as mudanças:

    git commit -am "Adicionada nova UI para criar Tasks"
    git diff upstream/java-task_add



Obter Tasks como JSON
---------------------

Crie um novo método de controller `getTasks` no arquivo `app/controllers/Application.java`. Primeiro adicione os imports:

    import play.db.ebean.Model;
    import java.util.List;
    import static play.libs.Json.toJson;

Então adicione os novos métodos `getTasks`:

        public static Result getTasks() {
            List<Task> tasks = new Model.Finder(String.class, Task.class).all();
            return ok(toJson(tasks));
        }



Adicione uma nova rota no arquivo `conf/routes` para obter as tasks como dados JSON serializados:

    GET     /tasks                      controllers.Application.getTasks()


Após adicionar uma nova `Task`, abra a seguinte URL em seu navegador:
[http://localhost:9000/tasks](http://localhost:9000/tasks)


Verifique se há uma ou mais Tasks no formulário JSON.

Dê commit e verifique as mudanças:

    git commit -am "Obter as tasks como JSON"
    git diff upstream/java-task_json



Exibir as Tasks via CoffeeScript e jQuery
-----------------------------------------

No corpo do arquivo `app/views/index.scala.html`, adicione um local para exibir as tasks no formulário:

    <ul id="tasks"></ul>


Crie um novo arquivo chamado `app/assets/javascripts/index.coffee` (e crie os diretórios necessários) contendo uma simples aplicação CoffeeScript que use jQuery para localizar e exibir as tasks:

    $ ->
      $.get "/tasks", (data) ->
        $.each data, (index, task) ->
          $("#tasks").append $("<li>").text task.contents

Isso fará uma requisição `get` para `/tasks` para a lista serializada JSON de objetos `Task` e então adicionará um novo item de lista ao elemento da página com a id de `tasks` para cada `Task`.



Atualize o arquivo `app/views/main.scala.html` para incluir a versão compilada e reduzida de JavaScript `index.coffee` adicionando o seguinte, após a linha que inclui o jQuery:

    <script src="@routes.Assets.at("javascripts/index.min.js")" type="text/javascript"></script>


Rode a aplicação no navegador e verifique se a lista de tasks é mostrada:
[http://localhost:9000/](http://localhost:9000/)


Dê commit e verifique as mudanças:

    git add app/views/index.scala.html app/assets/javascripts/index.coffee app/views/main.scala.html
    git commit -am "Display the list of tasks using jQuery and CoffeeScript"
    git diff upstream/java-task_coffeescript



Deixe a aplicação mais bonita com o Twitter Bootstrap
-----------------------------------------------------

[Twitter Bootstrap](http://twitter.github.com/bootstrap) é uma biblioteca CSS que facilita a criação de um visual mais bonito para a aplicação web. Para usar o Twitter Bootstrap começe adicionando a dependência [WebJar](http://webjar.github.com) no arquivo `project/Build.scala`:

          val appDependencies = Seq(
            "com.github.twitter" % "bootstrap" % "2.0.2"
          )
    
          val main = PlayProject(appName, appVersion, appDependencies, mainLang = JAVA).settings(
            resolvers += "webjars" at "http://webjars.github.com/m2"
          )


Agora reinicie o servidor do Play para que ele reconheça a nova dependência. Para usar o Twitter Bootstrap simplesmente adicione a seguinte linha no arquivo `app/views/main.scala.html`, certificando-se de que essa linha esteja entre a linha da tag `<title>` e a linha `main.css`:

    <link rel="stylesheet" media="screen" href="@routes.Assets.at("stylesheets/bootstrap.min.css")">


Adicione o seguinte código ao arquivo `public/stylesheets/main.css` para mover o conteúdo principal para uma localização fácil de visualizar:

    body {
        margin-top: 50px;
    }



Crie um novo componente de template que será usado para criar um novo campo de formulário do Bootstrap, criando um novo arquivo chamado `app/views/twitterBootstrapInput.scala.html` contendo:

    @(elements: helper.FieldElements)
    
    <div class="control-group @if(elements.hasErrors) {error}">
        <label for="@elements.id" class="control-label">@elements.label</label>
        <div class="controls">
            @elements.input
            <span class="help-inline">@elements.errors.mkString(", ")</span>
        </div>
    </div>


Atualize o arquivo `app/views/index.scala.html` para usar o cabeçalho, layout e fontes amigáveis do Bootstrap:

    @(message: String, taskForm: Form[Task])
    @implicitFieldConstructor = @{ helper.FieldConstructor(twitterBootstrapInput.render) }
    
    @main("Bem-vindo ao Play 2.0") {
    
        <div class="navbar navbar-fixed-top">
            <div class="navbar-inner">
                <div class="container-fluid">
                    <a id="titleLink" class="brand" href="/">@message.toUpperCase</a>
                </div>
            </div>
        </div>

        <div class="container">
            <ul id="tasks"></ul>

            @helper.form(action = routes.Application.addTask(), 'class -> "well form-horizontal") {
                @helper.inputText(taskForm("contents"), '_label -> "Contents")
                <div class="controls">
                    <input type="submit" class="btn btn-primary"/>
                </div>
            }
        </div>
    
    }


O template agora obterá um segundo parâmetro que é um `Form[Task]` e deve ser passado ao template. Isso será útil para a validação do formulário. Atualize o método `index()` no arquivo `app/controllers/Application.java` para passar o novo parâmetro requerido:

      public static Result index() {
          return ok(index.render("hello, world", form(Task.class)));
      }



Atualize o arquivo `test/IndexTest.java` para passar o formulário para o template. Primeiro adicione os imports:

    import play.data.Form;
    import models.Task;


Atualize a primeira linha do método de teste `indexTemplate`:

          Content html = views.html.index.render("test", new Form(Task.class));


Execute o teste para ter certeza de que está funcionando:

    play test


Abra a aplicação em seu navegador e verifique se ainda funciona:
[http://localhost:9000/](http://localhost:9000/)


Dê commit e verifique as mudanças:

    git add app/views/twitterBootstrapInput.scala.html
    git commit -am "Adicionado o Bootstrap"
    git diff upstream/java-bootstrap



Adicione a validação do formulário
----------------------------------

Adicione um simples `Required` constraint à classe `app/models/Task.java`. Primeiro adicione o import:

    import play.data.validation.Constraints;

Adicione a anotação `@Constraints.Required` ao campo `contents`:

        @Constraints.Required
        public String contents;


Atualize o método `addTask` ao controller `app/controllers/Application.java` para verificar erros no formulário e para confirmar se o formulário está sendo renderizado ao invés de a aplicação tentar salvar a Task:

        public static Result addTask() {
            Form<Task> form = form(Task.class).bindFromRequest();
            if (form.hasErrors()) {
                return badRequest(index.render("hello, world", form));
            }
            else {
                Task task = form.get();
                task.save();
                return redirect(routes.Application.index());
            }
        }


Abra a aplicação em seu navegador e verifique se a adição de uma Task vazia causa algum erro:
[http://localhost:9000/](http://localhost:9000/)


Dê commit e verifique as mudanças:

    git commit -am "Add validation"
    git diff upstream/java-validation



Atualize a aplicação no Heroku
------------------------------

Heroky fornece um pequeno banco de dados PosgreSQL gratuito para cada aplicação. Para ajustar a aplicação para o uso desse banco de dados somente quando ele estiver funcionando no Heroku, duas pequenas mudanças precisam ser feitas:

Primeiro, adicione o driver do banco de dados PostgreSQL como dependência da aplicação no arquivo `project/Build.scala` adicionando o seguinte código:

          val appDependencies = Seq(
            "com.github.twitter" % "bootstrap" % "2.0.2",
            "postgresql" % "postgresql" % "9.1-901-1.jdbc4"
          )


Então atualize o `Procfile` para substituir os ajustes padrão de banco de dados quando o Heroku executar a aplicação:

    web: target/start -Dhttp.port=${PORT} -DapplyEvolutions.default=true -Ddb.default.driver=org.postgresql.Driver -Ddb.default.url=$DATABASE_URL ${JAVA_OPTS}


Dê commit e verifique as mudanças:

    git commit -am "Atualizações para PostgreSQL no Heroku"
    git diff upstream/java-heroku_update


Sincronize (dê push) suas atualizações no Heroku:

    git push heroku master


Veja sua aplicação na nuvem:

    heroku open



Meus Parabéns!
--------------

Você construiu uma aplicação em Play 2 e a instalou em ambiente de produção na nuvem. Você aprendeu o básico de como utilizar Play 2, Ebean, CoffeeScript, Twitter Bootstrap, jQuery, serviços JSON RESTful e Heroku. Divirta-se e continue estudando e aprendendo Play 2!
