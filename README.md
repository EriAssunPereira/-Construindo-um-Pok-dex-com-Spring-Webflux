# Construindo-um-Pokédex-com-Spring-Webflux

Desenvolver um Pokédex utilizando Spring Webflux é uma excelente maneira de explorar programação reativa e construir uma aplicação RESTful reativa com Spring Boot. Spring Webflux é o módulo reativo do Spring Framework, que suporta programação reativa para construção de sistemas escaláveis e de alto desempenho.

### Estrutura do Projeto em Módulos

Organizar o projeto em módulos é fundamental para manter a clareza e a organização do código, especialmente em aplicações maiores. Vamos estruturar nosso projeto em módulos separados para diferentes funcionalidades, como controle de rotas, serviços, modelos de dados e integração com MongoDB.

#### Estrutura de Diretórios

1. **src/**
   - **main/**
     - **java/**
       - **com.example.pokedex/**
         - **config/**
           - `WebConfig.java`: Configuração do Spring Webflux
         - **handler/**
           - `PokemonHandler.java`: Handlers para lidar com requisições HTTP
         - **model/**
           - `Pokemon.java`: Modelo de dados para Pokémon
         - **repository/**
           - `PokemonRepository.java`: Interface para acesso ao MongoDB
         - **router/**
           - `PokemonRouter.java`: Configuração das rotas da aplicação
         - **service/**
           - `PokemonService.java`: Lógica de negócios para manipular dados de Pokémon
         - `PokedexApplication.java`: Classe principal para inicialização da aplicação

2. **resources/**
   - `application.properties`: Configurações do MongoDB (URL, porta, nome do banco)

### Descrição Detalhada dos Componentes

1. **Configuração do Spring Webflux**

   `WebConfig.java` configura as principais características do Spring Webflux, como rotas, codecs e outras configurações específicas necessárias para a aplicação reativa.

   Exemplo de `WebConfig.java`:
   ```java
   @Configuration
   public class WebConfig implements WebFluxConfigurer {
       
       @Override
       public void configureHttpMessageCodecs(ServerCodecConfigurer configurer) {
           configurer.defaultCodecs().maxInMemorySize(16 * 1024 * 1024);
       }
   }
   ```

2. **Handlers e Roteamento**

   `PokemonHandler.java` define os métodos que lidam com as requisições HTTP para operações CRUD relacionadas aos Pokémon. Este arquivo geralmente contém métodos para buscar, criar, atualizar e deletar Pokémon.

   Exemplo de `PokemonHandler.java`:
   ```java
   @Component
   public class PokemonHandler {

       private final PokemonService pokemonService;

       public PokemonHandler(PokemonService pokemonService) {
           this.pokemonService = pokemonService;
       }

       public Mono<ServerResponse> getAllPokemons(ServerRequest request) {
           Flux<Pokemon> pokemons = pokemonService.getAllPokemons();
           return ServerResponse.ok().body(pokemons, Pokemon.class);
       }

       public Mono<ServerResponse> getPokemonById(ServerRequest request) {
           String pokemonId = request.pathVariable("id");
           Mono<Pokemon> pokemonMono = pokemonService.getPokemonById(pokemonId);
           return pokemonMono.flatMap(pokemon -> ServerResponse.ok().bodyValue(pokemon))
                             .switchIfEmpty(ServerResponse.notFound().build());
       }

       public Mono<ServerResponse> savePokemon(ServerRequest request) {
           Mono<Pokemon> pokemonMono = request.bodyToMono(Pokemon.class);
           return pokemonMono.flatMap(pokemon -> {
               Mono<Pokemon> savedPokemon = pokemonService.savePokemon(pokemon);
               return ServerResponse.ok().body(savedPokemon, Pokemon.class);
           });
       }

       // Métodos para atualizar e deletar Pokémon aqui...
   }
   ```

3. **Serviços e Repositórios**

   `PokemonService.java` contém a lógica de negócios para manipular dados de Pokémon, enquanto `PokemonRepository.java` define a interface para operações de persistência no MongoDB.

   Exemplo de `PokemonService.java`:
   ```java
   @Service
   public class PokemonService {

       private final PokemonRepository pokemonRepository;

       public PokemonService(PokemonRepository pokemonRepository) {
           this.pokemonRepository = pokemonRepository;
       }

       public Flux<Pokemon> getAllPokemons() {
           return pokemonRepository.findAll();
       }

       public Mono<Pokemon> getPokemonById(String id) {
           return pokemonRepository.findById(id);
       }

       public Mono<Pokemon> savePokemon(Pokemon pokemon) {
           return pokemonRepository.save(pokemon);
       }

       // Métodos para atualizar e deletar Pokémon aqui...
   }
   ```

4. **Integração com MongoDB**

   `PokemonRepository.java` utiliza Spring Data MongoDB para realizar operações CRUD no banco de dados MongoDB.

   Exemplo de `PokemonRepository.java`:
   ```java
   public interface PokemonRepository extends ReactiveMongoRepository<Pokemon, String> {
   }
   ```

### Conclusão

Construir um Pokédex com Spring Webflux não apenas demonstra o poder da programação reativa com Spring, mas também proporciona uma base sólida para criar aplicações RESTful reativas e escaláveis. Com a estrutura organizada em módulos e os exemplos de código fornecidos, nós estaremos preparados para explorar mais a fundo os conceitos de mono e flux, além de se familiarizar com o desenvolvimento de APIs reativas utilizando Spring Webflux e MongoDB.
