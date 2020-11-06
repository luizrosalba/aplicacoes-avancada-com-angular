# Aplicações avançadas com Angular 

## Change Detection 
- Mecanismo que nota mudanças no estado da aplicação ou componente
- Processo executados nas view começando pelo AppComponent
- Unidirecional (de cima para baixo na árvore de componentes, o filho nao manda CD par ao pai )
- O Angular entende seus componentes como Views 
-  O que causa mudanças ?
- Eventos do DOM (click focus submit ... )
- HTTP Requests
- Times setTimeout setInterval 
- Obs: todos são eventos assíncronos

## Zones 
- Executar um pedaço de código dentro de um wrapper
- wrapper sabe quando o código começou e terminou de ser executado 
- NgZone (é uma aplicação angular do zonejs)
- método tick itera pelo nosso array de changedetectionrefs  e 
![](cdref.PNG)
chama o detect changes



- Ciclo de vida : 

- Inicialização -> OnInit -> View Pronta -> ngAfterViewInit -> ngOnDestroy (lifecicle hooks)
- Existem mais ciclos 
- ngOnchanges ngDoCheck ngAgterContentInit ngAfterContentChecked 


- pai e filho trocando informações 
![](comunicacao.PNG)
- mudança
![](mudanca.PNG)
- o componente checka o binding e verifica se o novo valor mudou 
- mudanca detalhada :
![](mudancadetail.PNG)

- o codigo acima gera um erro 
- estamos rodando em developer mode 
- em developer mode temos um passo a mais no cd 
- temos um double check no old  novo binding
- se a expressão for modificada depois de calculada ele dispara um erro 
- value retorna um valor novo mas nao estamos disparando nenhuma ação assíncrona para disparar o value 
- implementar o lifecicle hook doCheck 

``` JS 
value = this._value;
ngDoCheck (){
    this.value = this._value; 
}
private get _value(): number{
    return MAth.floor(Math.random()*10);
}
```
- Solução usando zones 

``` JS 
value = this._value;
constructor (private ngZone:NgZone){
    ngZone.runOutsideAngular(()=>
    setInterval(
        ()=> this.value = this._value,1);
    );
} /// atualiza a view 1 ms rodando o bloco fora da zones do angular 
private get _value(): number{
    return MAth.floor(Math.random()*10);
}
```

Quando o change detection é executado em um componente, qual dessas ações acontece?
Executa ngAfterViewChecked no componente filho.


O que significa o erro ExpressionChangedAfterItHasBeenChecked?
Que o valor foi atualizado entre a primeira verificação e a segunda, que só acontece em modo de desenvolvimento.


Se um dos valores presentes na estrutura de dados chamada bindings tiver sido atualizado.

Qual das seguintes frases a seguir, relacionadas a Zones, está incorreta?
Não podemos executar códigos fora do NgZone.

Qual método do cíclo de vida é executado em toda execução do change detection?
ngDoCheck.

Que tipo de ações disparam uma rodada de change detection na aplicação?

Assíncronas.

O que acontece se um valor for atualizado depois de ter sido verificado?
Teremos inconsistência de informação pois o novo valor não foi renderizado na nossa View.

Quem guarda a lista de ChangeDetectorRef?
ApplicationRef.


Ao executar change detection em um componente pai:
A View de componente filho fica pronta apenas depois de o change detection ser executado nele mesmo.


Em que situação o Angular atualiza uma View durante a execução do change detection?
Se um dos valores presentes na estrutura de dados chamada bindings tiver sido atualizado.

Um pedaço de código assíncrono executado fora de uma Zone:
Não dispara change detection.

## Trabalho com estrutura e Otimização 

- Criou um <router-outlet> no html do app 
- Tudo que se repete na app inteira coloque no app component navbar , menu , footer ... 
- é interessante também não fazer isso pois a aplicação fica mais livre para mudar sem usar ngif para escoder as informações 
- cookies , bootstrap ... tudo no app component

##  app-component.ts
![](1.PNG)


-  feature A está sendo importada no appmodule pois aparece assim que a aplicação é carregada 
##  app-module.ts
![](2.PNG)

- a feature b está sendo lazy loaded (loadChieldren com bundle externo) e será acessada por /featureb
##  app-routing.ts
- pages / features informações renderizadas no router-outlet
![](3.PNG)

- 
##  featureamodule.ts
- feature a guardará todas as rotas (poderia ser um feature-module mas ele preferiu assim )
- RootFeatureAComponent raiz da feature A 
![](4.PNG)

##  featurebmodule.ts
- lazy loaded
- na raiz faz-se loadchildren
![](5.PNG)

## Estrutura de organizaçao 

![](6.PNG)
![](7.PNG)
- content c será uma feature completamente separada 
- abriu a pagina onde quero criar o modulo com o botao direito e abrindo o terminal 
- ng g m features/content-c
![](8.PNG)
- queremos ter modulos separadas , onda cada um tem sua propria responsabilidade 
- content c será um modulo completamente a parte 
- c é carregado dinamicamente pela rota 
![](9.PNG)
- rota do content c module 
![](10.PNG)
- tudo que depende de c está na feature c (isolado das features a e b)
![](11.PNG)

- shared - compartilhada para aplicação 
- declara e exporta os componentes shared 
![](12.PNG)

