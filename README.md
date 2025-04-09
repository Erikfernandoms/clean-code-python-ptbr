Repositório original: [ryanmcdermott/clean-code-javascript](https://github.com/ryanmcdermott/clean-code-javascript)

# clean-code-python-ptbr

## Índice
  1. [Introdução](#introdução)
  2. [Variáveis](#variáveis)
  3. [Funções](#funções)
  4. [Objetos e Estruturas de Dados](#objetos-e-estruturas-de-dados)
  5. [Classes](#classes)
  6. [SOLID](#solid)
  7. [Testes](#testes)
  8. [Concorrência](#concorrência)
  9. [Tratamento de Erros](#tratamento-de-erros)
  10. [Formatação](#formatação)
  11. [Comentários](#comentários)
  12. [Traduções](#traduções)

## Introdução
![Imagem humorística da estimativa de qualidade do software baseado na contagem de quantos palavrões você gritou enquanto lia o código.](http://www.osnews.com/images/comics/wtfm.jpg)

Princípios da Engenharia de Software apresentados no livro de Robert C. Martin
[*Código Limpo*](https://www.amazon.com.br/C%C3%B3digo-Limpo-Habilidades-Pr%C3%A1ticas-Software/dp/8576082675), foram adaptados para Python. Este não é um manual de estilo, mas um conjunto de diretrizes para escrever código legível, reutilizável e fácil de refatorar [legível, reutilizável e refatorável](https://github.com/ryanmcdermott/3rs-of-software-architecture).

Nem todos os princípios aqui descritos devem ser seguidos à risca, tampouco representam um consenso absoluto. Eles servem como recomendações, baseadas em anos de experiência coletiva dos autores de Código Limpo.

A engenharia de software ainda é jovem — pouco mais de 50 anos — e seguimos aprendendo constantemente. Talvez, quando for tão antiga quanto a arquitetura tradicional, tenhamos regras mais definitivas. Até lá, essas diretrizes podem ajudar você e sua equipe a avaliar a qualidade do código Python que desenvolvem.

Por fim, aprender esses conceitos não fará de você um programador excepcional da noite para o dia. Mesmo depois de anos de prática, erros ainda acontecerão. Todo código nasce como um rascunho, como argila fresca moldada antes de atingir sua forma final. O refinamento acontece na revisão, no aprendizado conjunto. Não se preocupe com os primeiros esboços imperfeitos—em vez disso, concentre-se em aprimorá-los.

## **Variáveis**
### Use nomes de variáveis que tenham significado e sejam pronunciáveis

**Ruim:**
```python
yyyymmdstr = datetime.now().format('YYYY/MM/DD')
```

**Bom:**
```python
dataAtual = datetime.now().format('YYYY/MM/DD')
```
**[⬆ voltar ao topo](#Índice)**

### Use o mesmo vocabulário para o mesmo tipo de variável/dado

**Ruim:**
```python
fetchUserDetails()
retrieveClientInfo()
loadCustomerData()
```

**Bom:**
```python
getUser()
```
**[⬆ voltar ao topo](#Índice)**

### Use nomes pesquisáveis
Passamos mais tempo lendo código do que escrevendo. Por isso, é essencial que o código que produzimos seja fácil de entender e simples de encontrar. Quando não usamos nomes de variáveis claros e significativos, dificultamos a vida de quem vai ler e manter o código. Escolha nomes que facilitem a busca e a compreensão do programa.

**Ruim:**
```python
# O que significa 86400?
time.sleep(86400)

```

**Bom:**
```python
# Defina uma constante com um nome descritivo
SECONDS_PER_DAY = 86400

time.sleep(SECONDS_PER_DAY)
```
**[⬆ voltar ao topo](#Índice)**

### Use variáveis explicativas
**Ruim:**
```python
import re  

address = "One Infinite Loop, Cupertino 95014"  
city_zip_code_regex = r"^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$"  
save_city_zip_code(re.match(city_zip_code_regex, address)[1], re.match(city_zip_code_regex, address)[2])
```

**Bom:**
```python
import re  

def extract_city_and_zip(address: str):  
    city_zip_code_pattern = r"^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$"  
    match = re.match(city_zip_code_pattern, address)  
    if match:  
        return match.group(1), match.group(2)  
    return None, None  

address = "One Infinite Loop, Cupertino 95014"  
city, zip_code = extract_city_and_zip(address)  

save_city_zip_code(city, zip_code)
```
**[⬆ voltar ao topo](#Índice)**

### Evite Mapeamento Mental
Explicito é melhor que implícito.

**Ruim:**
```python
locations = ["Austin", "New York", "San Francisco"]

for l in locations:
    do_stuff()
    do_some_other_stuff()
    # ...
    # ...
    # ...
    # O que era `l` mesmo?
    dispatch(l)
```

**Bom:**
```python
locations = ["Austin", "New York", "San Francisco"]

for location in locations:
    do_stuff()
    do_some_other_stuff()
    # ...
    # ...
    # ...
    dispatch(location)
```
**[⬆ voltar ao topo](#Índice)**

### Não adicione contextos desnecessários
Se o nome de uma classe ou objeto já deixa claro o seu propósito, evite repetir essa informação nas variávei

**Ruim:**
```python
class Car:
    def __init__(self, car_make, car_model, car_color):
        self.car_make = car_make
        self.car_model = car_model
        self.car_color = car_color

def paint_car(car, color):
    car.car_color = color
```

**Bom:**
```python
class Car:
    def __init__(self, make, model, color):
        self.make = make
        self.model = model
        self.color = color

def paint_car(car, color):
    car.color = color
```
**[⬆ voltar ao topo](#Índice)**

### Use argumentos padrões ao invés de curto circuitar ou usar condicionais

Argumentos padrões tornam o código mais limpo do que usar curto-circuito ou condicionais. Além disso, eles evitam confusões ao lidar com valores como '', 0, ou False.

**Ruim:**
```python
def create_microbrewery(name):
    brewery_name = name or 'Hipster Brew Co.'
    # ...

```

**Bom:**
```python
def create_microbrewery(brewery_name='Hipster Brew Co.'):
    # ...
```
**[⬆ voltar ao topo](#Índice)**


## **Funções**
### Argumentos de funções (idealmente 2 ou menos)
Limitar a quantidade de parâmetros de uma função é essencial para manter o código mais simples, legível e fácil de testar. Funções com muitos argumentos aumentam a complexidade, tornando os testes mais difíceis devido à explosão combinatória de possibilidades.

O ideal é que uma função tenha um ou dois argumentos. Três já devem ser evitados sempre que possível, e mais que isso indica que a função pode estar assumindo responsabilidades demais. Se não for o caso, geralmente um dicionário ou um objeto pode encapsular esses argumentos.
**Exemplo ruim (muitos parâmetros nomeados)**
```python
def criar_menu(titulo, corpo, texto_botao, cancelavel):
    # ...
    pass
```
🔴 Problemas:
- Difícil lembrar a ordem correta dos argumentos ao chamar a função.
- Se a função crescer e precisar de mais parâmetros, ficará ainda mais confusa.
- Diminui a clareza da assinatura da função.

**Exemplo bom (usando dicionário):**
```python
def criar_menu(config):
    titulo = config.get("titulo")
    corpo = config.get("corpo")
    texto_botao = config.get("texto_botao")
    cancelavel = config.get("cancelavel")
    # ...

menu = {
    "titulo": "Foo",
    "corpo": "Bar",
    "texto_botao": "Baz",
    "cancelavel": True
}

criar_menu(menu)
```
✅ Vantagens:
- A função recebe apenas um argumento, reduzindo complexidade.
- Os valores podem ser manipulados antes da chamada da função.
- Evita confusão com a ordem dos argumentos.

**Exemplo ainda melhor (usando dataclass):**
```python
from dataclasses import dataclass

@dataclass
class MenuConfig:
    titulo: str
    corpo: str
    texto_botao: str
    cancelavel: bool

def criar_menu(config: MenuConfig):
    # ...
    pass

config = MenuConfig(titulo="Foo", corpo="Bar", texto_botao="Baz", cancelavel=True)
criar_menu(config)
```

✅ Vantagens:
- Torna explícito quais atributos a função espera.
- Usa tipagem, ajudando a detectar erros cedo.
- Mais organizado e sustentável conforme o código cresce.
- Essa abordagem mantém a clareza e facilita a manutenção do código. 🚀

**[⬆ voltar ao topo](#Índice)**

### Funções devem fazer uma coisa
Essa é de longe a regra mais importante em engenharia de software. Quando funções fazem mais de uma coisa, elas se tornam difíceis de serem compostas, testadas e compreendidas.

Ao isolar uma função para realizar apenas uma ação, ela pode ser refatorada facilmente, tornando o código mais legível e modular. Se você aplicar apenas esse princípio, já estará à frente de muitos desenvolvedores.

**Exemplo ruim (função faz mais de uma coisa)**
```python
def enviar_emails(clientes):
    for cliente in clientes:
        registro_cliente = banco_dados.buscar(cliente)
        if registro_cliente.ativo():
            enviar_email(cliente)
```
🔴 Problemas:
- A função busca o cliente no banco e verifica se ele está ativo e envia o e-mail.
- Dificulta a reutilização de código, pois não podemos reutilizar facilmente a verificação de clientes ativos.
- Difícil de testar separadamente cada parte da lógica.

**Exemplo bom (cada função tem uma única responsabilidade)**
```python
def enviar_emails_clientes_ativos(clientes):
    clientes_ativos = filtrar_clientes_ativos(clientes)
    for cliente in clientes_ativos:
        enviar_email(cliente)

def filtrar_clientes_ativos(clientes):
    return [cliente for cliente in clientes if cliente_esta_ativo(cliente)]

def cliente_esta_ativo(cliente):
    registro_cliente = banco_dados.buscar(cliente)
    return registro_cliente.ativo()
```
✅ Vantagens:
- Modularidade: Cada função faz apenas uma coisa.
- Reutilização: Podemos chamar filtrar_clientes_ativos() em outros contextos sem precisar de enviar_email().
- Testabilidade: Agora podemos testar cada função separadamente.
- Legibilidade: O código fica mais claro e fácil de entender.
  
Esse princípio mantém o código mais limpo e sustentável.

**[⬆ voltar ao topo](#Índice)**

### Nomes de funções devem dizer o que elas fazem
Os nomes das funções devem ser descritivos o suficiente para que qualquer pessoa lendo o código entenda claramente o que elas fazem sem precisar analisar sua implementação.

**Ruim:**
```python
def adicionar_data(data, mes):
    # ...
    pass

data = datetime.now()

# Difícil entender o que está sendo adicionado
adicionar_data(data, 1)
```
🔴 Problemas:
- O nome adicionar_data não deixa claro se estamos adicionando dias, meses ou anos.
- A ordem dos parâmetros pode gerar confusão.

**Bom:**
```python
from datetime import datetime, timedelta
from dateutil.relativedelta import relativedelta

def adicionar_meses_a_data(meses, data):
    return data + relativedelta(months=meses)

data = datetime.now()
nova_data = adicionar_meses_a_data(1, data)

```
✅ Vantagens:
- Clareza: O nome adicionar_meses_a_data explica exatamente o que a função faz.
- Legibilidade: Qualquer um pode entender o propósito sem precisar analisar o código interno.
- Boa prática: Mantém o código intuitivo e fácil de manter.

Nomear funções corretamente melhora a compreensão e evita erros.
**[⬆ voltar ao topo](#Índice)**

### Funções devem ter apenas um nível de abstração
Quando uma função mistura diferentes níveis de abstração, ela provavelmente está fazendo coisas demais. Separar essas responsabilidades melhora a legibilidade, facilita testes e permite a reutilização do código.

**Ruim:**
```python
def analisar_codigo_python(codigo):
    REGEXES = [
        # ...
    ]

    declaracoes = codigo.split(" ")
    tokens = []
    
    for regex in REGEXES:
        for declaracao in declaracoes:
            # ...
            pass

    ast = []
    for token in tokens:
        # Realiza análise léxica...
        pass

    for node in ast:
        # Realiza análise sintática...
        pass
```
🔴 Problemas:
- Mistura tokenização, análise léxica e análise sintática na mesma função.
- Código difícil de entender e manter.
- Não é reutilizável nem modular.

**Bom:**
```python
def tokenizar(codigo):
    REGEXES = [
        # ...
    ]

    declaracoes = codigo.split(" ")
    tokens = []
    
    for regex in REGEXES:
        for declaracao in declaracoes:
            tokens.append( /* ... */ )

    return tokens

def analisar_lexicamente(tokens):
    ast = []
    for token in tokens:
        ast.append( /* ... */ )

    return ast

def analisar_codigo_python(codigo):
    tokens = tokenizar(codigo)
    ast = analisar_lexicamente(tokens)
    
    for node in ast:
        # Realiza análise sintática...
        pass
```
✅ Vantagens:
- Organização clara: Cada função tem um único nível de abstração.
- Facilidade de manutenção: Se houver um bug na análise léxica, não precisamos mexer na tokenização.
- Reutilização: Podemos usar tokenizar() e analisar_lexicamente() em outros contextos sem modificar analisar_codigo_python().
- Legibilidade: O código fica mais intuitivo e fácil de entender.

Esse princípio mantém o código modular e sustentável.
**[⬆ voltar ao topo](#Índice)**

### Remova código duplicado
Evite ao máximo código duplicado. Duplicação significa que, se você precisar alterar uma lógica, terá que modificar múltiplas partes do código, aumentando as chances de erro.

Pense em um restaurante onde você gerencia o estoque de ingredientes como tomates, cebolas e temperos. Se houver várias listas separadas para esses itens, será necessário atualizar todas quando um ingrediente for utilizado. No entanto, se houver apenas uma única lista centralizada, a atualização será feita em um único local.

Código duplicado geralmente surge quando há duas ou mais partes do código que são ligeiramente diferentes, mas compartilham muito em comum. A solução é criar uma abstração capaz de lidar com essas diferenças de forma unificada.

No entanto, abstrações ruins podem ser piores que código duplicado. Por isso, siga os princípios SOLID para garantir que a abstração seja bem feita. Se puder criar uma boa abstração, faça isso! Caso contrário, corre o risco de ter que atualizar múltiplos trechos de código sempre que houver uma pequena mudança.

**Ruim:**
```python
def exibir_lista_desenvolvedores(desenvolvedores):
    for dev in desenvolvedores:
        salario_esperado = dev.calcular_salario_esperado()
        experiencia = dev.obter_experiencia()
        github = dev.obter_link_github()
        
        dados = {
            "salario_esperado": salario_esperado,
            "experiencia": experiencia,
            "github": github
        }
        
        renderizar(dados)

def exibir_lista_gerentes(gerentes):
    for gerente in gerentes:
        salario_esperado = gerente.calcular_salario_esperado()
        experiencia = gerente.obter_experiencia()
        portfolio = gerente.obter_projetos_mba()
        
        dados = {
            "salario_esperado": salario_esperado,
            "experiencia": experiencia,
            "portfolio": portfolio
        }
        
        renderizar(dados)

```
🔴 Problemas:
- Código duplicado nas duas funções.
- Difícil de manter: qualquer alteração na estrutura dos dados exigirá mudanças em vários lugares.
- Pouca flexibilidade para novos tipos de funcionários.

**Bom:**
```python
def exibir_lista_funcionarios(funcionarios):
    for funcionario in funcionarios:
        salario_esperado = funcionario.calcular_salario_esperado()
        experiencia = funcionario.obter_experiencia()
        
        dados = {
            "salario_esperado": salario_esperado,
            "experiencia": experiencia
        }
        
        if isinstance(funcionario, Gerente):
            dados["portfolio"] = funcionario.obter_projetos_mba()
        elif isinstance(funcionario, Desenvolvedor):
            dados["github"] = funcionario.obter_link_github()
        
        renderizar(dados)

```
✅ Vantagens:
- Código mais limpo e organizado: evita repetições desnecessárias.
- Facilidade de manutenção: qualquer alteração na estrutura dos dados é feita em apenas um lugar.
- Extensibilidade: se um novo tipo de funcionário for adicionado, basta incluir um novo elif.
**[⬆ voltar ao topo](#Índice)**

### Defina objetos padrões com dict.update()

**Ruim:**
```python
menu_config = {
    "title": None,
    "body": "Bar",
    "button_text": None,
    "cancellable": True
}

def create_menu(config):
    config["title"] = config["title"] or "Foo"
    config["body"] = config["body"] or "Bar"
    config["button_text"] = config["button_text"] or "Baz"
    config["cancellable"] = config.get("cancellable", True)

create_menu(menu_config)
```
🔴 Problemas:
- A função modifica diretamente o dicionário de entrada.
- Precisa verificar cada chave individualmente.

**Bom:**
```python
def create_menu(config):
    default_config = {
        "title": "Foo",
        "body": "Bar",
        "button_text": "Baz",
        "cancellable": True
    }
    final_config = {**default_config, **config}
    
    return final_config

menu_config = {
    "title": "Order",
    "button_text": "Send",
    "cancellable": True
}

menu = create_menu(menu_config)
print(menu) 
# {'title': 'Order', 'body': 'Bar', 'button_text': 'Send', 'cancellable': True}
```
✅ Vantagens:
- Mais limpo e legível.
- Não modifica o dicionário original.
- Fácil de adicionar novas chaves.

**[⬆ voltar ao topo](#Índice)**


### Não use flags como parâmetros de funções
Flags falam para o seu usuário que sua função faz mais de uma coisa. Funções devem fazer apenas uma coisa. Divida suas funções se elas estão seguindo caminhos de código diferentes baseadas em um valor booleano.

**Ruim:**
```python
import os

def create_file(name, temp):
    if temp:
        os.mkdir(f"./temp/{name}")
    else:
        os.mkdir(name)

```

**Bom:**
```python
import os

def create_file(name):
    os.mkdir(name)

def create_temp_file(name):
    create_file(f"./temp/{name}")

```
✅ Vantagens:
- Cada função tem um único propósito.
- Código mais fácil de entender e testar
  
**[⬆ voltar ao topo](#Índice)**

### Evite Efeitos Colaterais (parte 1)
Uma função não deve modificar variáveis globais inesperadamente.

**Ruim:**
```python
name = "Ryan McDermott"

def split_into_first_and_last_name():
    global name
    name = name.split()

split_into_first_and_last_name()
print(name)  # ['Ryan', 'McDermott']

```

**Bom:**
```python
def split_into_first_and_last_name(name):
    return name.split()

name = "Ryan McDermott"
new_name = split_into_first_and_last_name(name)

print(name)      # 'Ryan McDermott'
print(new_name)  # ['Ryan', 'McDermott']

```
✅ Vantagens:
- Sem efeitos colaterais.
- A variável original não é alterada.
  
**[⬆ voltar ao topo](#Índice)**

### Evite Efeitos Colaterais (parte 2)
Em Python, listas e dicionários são mutáveis. Evite modificar objetos diretamente.

**Ruim:**
```python
def add_item_to_cart(cart, item):
    cart.append({"item": item, "date": "2025-03-12"})  

```
🔴 Problema: 
- A função modifica diretamente cart.
- 
**Bom:**
```python
def add_item_to_cart(cart, item):
    return cart + [{"item": item, "date": "2025-03-12"}]

```
✅ Vantagens:

- Mantém imutabilidade.
- Evita bugs causados por referências mutáveis.
  
Não escreva em funções globais
Evite modificar métodos embutidos ou variáveis globais.

**Ruim:**
```python
def extend_list():
    list.append = lambda x: "Ops!"  # Modifica a função embutida `append`

```

**Bom:**
```python
class SuperList(list):
    def diff(self, comparison_list):
        return [x for x in self if x not in comparison_list]
```
✅ Vantagens:
- Extensível sem modificar objetos globais.
  
**[⬆ voltar ao topo](#Índice)**

### Favoreça programação funcional sobre programação imperativa
Use map e reduce em vez de loops explícitos.

**Ruim:**
```python
programmers = [
    {"name": "Uncle Bobby", "lines_of_code": 500},
    {"name": "Suzie Q", "lines_of_code": 1500},
    {"name": "Jimmy Gosling", "lines_of_code": 150},
    {"name": "Gracie Hopper", "lines_of_code": 1000}
]

total_output = 0
for p in programmers:
    total_output += p["lines_of_code"]

```

**Bom:**
```python
from functools import reduce

programmers = [
    {"name": "Uncle Bobby", "lines_of_code": 500},
    {"name": "Suzie Q", "lines_of_code": 1500},
    {"name": "Jimmy Gosling", "lines_of_code": 150},
    {"name": "Gracie Hopper", "lines_of_code": 1000}
]

total_output = reduce(lambda acc, p: acc + p["lines_of_code"], programmers, 0)
```
✅ Vantagens:
- Código mais conciso e legível.
- Evita mutação de variáveis.
    
**[⬆ volta ao topo](#Índice)**

### Encapsule condicionais
Evite condicionais complexas dentro do código.

**Ruim:**
```python
if fsm.state == "fetching" and is_empty(list_node):
    # ...

```

**Bom:**
```python
def should_show_spinner(fsm, list_node):
    return fsm.state == "fetching" and is_empty(list_node)

if should_show_spinner(fsm_instance, list_node_instance):
    # ...
```
✅ Vantagens:
- Código mais legível e reutilizável.
- 
**[⬆ voltar ao topo](#Índice)**

### Evite negações de condicionais

**Ruim:**
```python
def should_show_spinner(fsm, list_node):
    return fsm.state == "fetching" and is_empty(list_node)

if should_show_spinner(fsm_instance, list_node_instance):
    # ...

```

**Bom:**
```javascript
function isDOMNodePresent(node) {
  // ...
}

if (isDOMNodePresent(node)) {
  // ...
}
```
**[⬆ voltar ao topo](#Índice)**


### Evite checagem de tipos
Use polimorfismo para evitar checagem manual de tipos.

**Ruim:**
```python
def travel_to_texas(vehicle):
    if isinstance(vehicle, Bicycle):
        vehicle.pedal("Texas")
    elif isinstance(vehicle, Car):
        vehicle.drive("Texas")

```

**Bom:**
```python
def travel_to_texas(vehicle):
    vehicle.move("Texas")
```
✅ Vantagens:
- Código mais flexível e extensível.
  
**[⬆ voltar ao topo](#Índice)**

### Não otimize demais
Deixe que o interpretador do Python otimize as execuções.

**Ruim:**
```python
items = [1, 2, 3, 4]
length = len(items)  # Cache desnecessário

for i in range(length):
    print(items[i])

```

**Bom:**
```python
items = [1, 2, 3, 4]

for item in items:
    print(item)
```
✅ Vantagens:
- Mais simples e eficiente.
- Evita código desnecessário.
  
**[⬆ voltar ao topo](#Índice)**

### Remova código morto
Se o código não está sendo usado, remova-o

**Ruim:**
```python
def old_request_module(url):
    pass  # Código não utilizado

def new_request_module(url):
    pass

req = new_request_module
inventory_tracker("apples", req, "www.inventory-awesome.io")

```

**Bom:**
```python
def new_request_module(url):
    pass

req = new_request_module
inventory_tracker("apples", req, "www.inventory-awesome.io")

```
✅ Vantagens:
- Código mais limpo e fácil de manter.
  
**[⬆ voltar ao topo](#Índice)**

## **Objetos e Estruturas de Dados**
### Use getters e setters

Utilizar getters e setters para acessar dados de objetos é muito melhor do que simplesmente acessar diretamente os atributos. “Por quê?”, você pode perguntar. Aqui vai uma lista desorganizada de motivos:
1. Quando você quiser fazer mais do que apenas acessar um atributo, não precisará mudar todo o código que acessa esse dado;
2. Facilita a validação ao definir (set) valores;
3. Encapsula a representação interna do objeto;
4. Facilita a adição de logs e tratamento de erros nos acessos;
5. Permite o uso de lazy loading, como buscar a propriedade sob demanda de um servidor, por exemplo.

**Ruim**:
```python
class BankAccount:
    def __init__(self):
        self.balance = 0

account = BankAccount()
account.balance = 100  # acesso direto, sem validação ou encapsulamento
```

**Bom:**
```python
class BankAccount:
    def __init__(self):
        self._balance = 0  # atributo privado

    def get_balance(self):
        return self._balance

    def set_balance(self, amount):
        if amount < 0:
            raise ValueError("O saldo não pode ser negativo.")
        # aqui você poderia adicionar logs ou chamadas externas
        self._balance = amount

account = BankAccount()
account.set_balance(100)

```
**Se quiser deixar ainda mais pythonico, você pode usar a propriedade @property:**
```python
class BankAccount:
    def __init__(self):
        self._balance = 0

    @property
    def balance(self):
        return self._balance

    @balance.setter
    def balance(self, amount):
        if amount < 0:
            raise ValueError("O saldo não pode ser negativo.")
        self._balance = amount

account = BankAccount()
account.balance = 100  # usa setter com validação por baixo dos panos
print(account.balance)  # usa getter
```

**[⬆ voltar ao topo](#Índice)**

### Objetos devem ter membros privados

No Python, não existem membros verdadeiramente privados como em outras linguagens, mas convenciona-se que atributos precedidos por um sublinhado (_) são considerados "privados". Para proteção mais forte, você pode usar dois sublinhados (__), que ativa name mangling.


**Ruim:**
```python
class Employee:
    def __init__(self, name):
        self.name = name  # atributo público

    def get_name(self):
        return self.name

employee = Employee("John Doe")
print(f"Employee name: {employee.get_name()}")  # Employee name: John Doe

del employee.name
print(f"Employee name: {employee.get_name()}")  # Erro: atributo removido
```

**Bom:**
```python
class Employee:
    def __init__(self, name):
        self.__name = name  # atributo privado com name mangling

    def get_name(self):
        return self.__name

employee = Employee("John Doe")
print(f"Employee name: {employee.get_name()}")  # Employee name: John Doe

# Tentativa de deletar o atributo não afeta o valor real protegido
del employee.__dict__["_Employee__name"]
print(f"Employee name: {employee.get_name()}")  # Ainda funciona, se não deletado forçadamente
Se quiser impedir totalmente modificação externa, evite expor qualquer forma de set_name. Ou use @property com um getter apenas.
```

**[⬆ voltar ao topo](#Índice)**

## **Classes**

### Prefira classes modernas e claras

Evite herança confusa e hierarquias difíceis de seguir. Use a sintaxe limpa e moderna das classes em Python. Comece com funções simples e só crie classes quando realmente necessário.

**Ruim (imitando herança estilo "manual" ou excessivamente verboso):**
```python
class Animal:
    def __init__(self, age):
        if not isinstance(self, Animal):
            raise TypeError("Use a classe corretamente")
        self.age = age

    def move(self):
        pass

class Mammal(Animal):
    def __init__(self, age, fur_color):
        super().__init__(age)
        self.fur_color = fur_color

    def live_birth(self):
        pass

class Human(Mammal):
    def __init__(self, age, fur_color, language_spoken):
        super().__init__(age, fur_color)
        self.language_spoken = language_spoken

    def speak(self):
        pass
```
Apesar de não estar "errado", esse estilo tenta recriar proteções desnecessárias.

**Bom:**
```python
class Animal:
    def __init__(self, age):
        self.age = age

    def move(self):
        # lógica de movimento
        pass

class Mammal(Animal):
    def __init__(self, age, fur_color):
        super().__init__(age)
        self.fur_color = fur_color

    def live_birth(self):
        # lógica de nascimento vivo
        pass

class Human(Mammal):
    def __init__(self, age, fur_color, language_spoken):
        super().__init__(age, fur_color)
        self.language_spoken = language_spoken

    def speak(self):
        # lógica de fala
        pass
```
Esse é o padrão de herança limpa e simples no Python — claro, direto e fácil de manter.

**[⬆ voltar ao topo](#Índice)**

### Use encadeamento de métodos

Esse padrão torna o código mais fluido, expressivo e elegante. Embora seja mais comum em linguagens como JavaScript, ele pode ser implementado em Python retornando self ao final de cada método da classe.

**Ruim (sem encadeamento):**
```python
class Car:
    def __init__(self, make, model, color):
        self.make = make
        self.model = model
        self.color = color

    def set_make(self, make):
        self.make = make

    def set_model(self, model):
        self.model = model

    def set_color(self, color):
        self.color = color

    def save(self):
        print(self.make, self.model, self.color)

car = Car("Ford", "F-150", "red")
car.set_color("pink")
car.save()
```

**Bom (com encadeamento):**
```python
class Car:
    def __init__(self, make, model, color):
        self.make = make
        self.model = model
        self.color = color

    def set_make(self, make):
        self.make = make
        return self  # permite encadeamento

    def set_model(self, model):
        self.model = model
        return self

    def set_color(self, color):
        self.color = color
        return self

    def save(self):
        print(self.make, self.model, self.color)
        return self

car = Car("Ford", "F-150", "red")\
    .set_color("pink")\
    .set_model("Raptor")\
    .save()
```
✅ Vantagens do encadeamento de métodos em Python:
- Mais legível e conciso: reduz a repetição do nome do objeto.
- Expressivo: o código se aproxima de uma linguagem fluente, quase como uma DSL (Domain Specific Language).
- Bom para builders e objetos configuráveis: útil em APIs que montam configurações ou processos passo a passo.

⚠️ Desvantagens e cuidados:
- Debug mais difícil: em chamadas longas encadeadas, localizar qual método causou um erro pode ser mais difícil.
- Dificulta a quebra de linha lógica se não bem formatado.
- Pode incentivar métodos que fazem mais de uma coisa, quebrando o Single Responsibility Principle se não for bem usado.
- Nem sempre é idiomático no Python: o estilo funcional ou declarativo é preferido em algumas situações.
  
**[⬆ voltar ao topo](#Índice)**

### Prefira composição ao invés de herança

Em Python, como em outras linguagens, a composição costuma ser mais flexível e segura do que a herança. Ela permite trocar implementações mais facilmente e evita o acoplamento excessivo entre classes.

**Ruim (herança onde não faz sentido):**
```python
class Employee:
    def __init__(self, name, email):
        self.name = name
        self.email = email

# ERRADO: EmployeeTaxData não é um tipo de Employee
class EmployeeTaxData(Employee):
    def __init__(self, ssn, salary):
        super().__init__(name=None, email=None)
        self.ssn = ssn
        self.salary = salary
```

**Bom (composição):**
```python
class EmployeeTaxData:
    def __init__(self, ssn, salary):
        self.ssn = ssn
        self.salary = salary

class Employee:
    def __init__(self, name, email):
        self.name = name
        self.email = email
        self.tax_data = None

    def set_tax_data(self, ssn, salary):
        self.tax_data = EmployeeTaxData(ssn, salary)
        return self  # permite encadeamento, se desejado
```

🆚 Herança vs Composição — Quando usar?
Critério | Herança | Composição
Relação "é um" (is-a) | ✅ Sim | ❌ Não
Reutilização de comportamento | ✅ Mais direto  | 🔁 Possível via delegação
Flexibilidade	 | ❌ Menor (acoplamento forte)	 | ✅ Maior (acoplamento fraco)
Substituição de partes internas | ❌ Difícil | ✅ Fácil
Manutenção em longo prazo | ⚠️ Mais delicada | ✅ Mais segura
Polimorfismo | ✅ Natural | ❌ Exige interface explícita

**[⬆ voltar ao topo](#Índice)**

## **SOLID**
### Princípio da Responsabilidade Única (SRP)
Como dito em Código Limpo, "Nunca deveria haver mais de um motivo para uma classe ter que mudar". É tentador empacotar uma classe em excesso com muitas funcionalidades, como quando você pode levar apenas uma mala em seu voo. O problema com isso é que sua classe não será conceitualmente coesa e dar-lhe-á diversos motivos para mudá-la. Minimizar o número de vezes que você precisa mudar uma classe é importante, porque, se muitas funcionalidades estão em uma classe e você mudar uma porção dela, pode ser difícil entender como isto afetará outros módulos que dependem dela no seu código.

**Exemplo Ruim (violando SRP):**
```python
class UserSettings:
    def __init__(self, user):
        self.user = user

    def change_settings(self, settings):
        if self.verify_credentials():
            # aplica as configurações
            pass

    def verify_credentials(self):
        # lógica de autenticação do usuário
        pass
```
Essa classe faz duas coisas diferentes:
1. Verifica credenciais (responsabilidade de segurança/autenticação)
2. Altera configurações (responsabilidade de preferências ou dados do usuário)

**Exemplo Bom (aplicando SRP):**
```python
class UserAuth:
    def __init__(self, user):
        self.user = user

    def verify_credentials(self):
        # lógica de autenticação
        return True  # exemplo
python
```
```python
class UserSettings:
    def __init__(self, user, auth: UserAuth):
        self.user = user
        self.auth = auth

    def change_settings(self, settings):
        if self.auth.verify_credentials():
            # aplica as configurações
            print(f"Configurações aplicadas para {self.user}")
```
**Uso:**
```python
auth = UserAuth(user="alice")
settings = UserSettings(user="alice", auth=auth)
settings.change_settings({"theme": "dark"})
```
✅ Vantagens de aplicar SRP:
- Facilidade de manutenção
- Reutilização
- Testabilidade
- Menor acoplamento
  
**[⬆ voltar ao topo](#Índice)**

### Princípio do Aberto/Fechado (OCP)
Como foi dito por Bertrand Meyer, "entidades de software (classes, módulos, funções, etc.) devem se manter abertas para extensões, mas fechadas para modificações." Mas o que isso significa? Esse princípio basicamente diz que você deve permitir que usuários adicionem novas funcionalidades sem mudar código já existente.

Esse princípio é essencial para garantir que, à medida que o sistema cresce, possamos adicionar novas funcionalidades sem alterar código existente (evitando bugs em partes já testadas).

**Exemplo Ruim (violando OCP):**
```python
class HttpRequester:
    def __init__(self, adapter_name):
        self.adapter_name = adapter_name

    def fetch(self, url):
        if self.adapter_name == 'ajaxAdapter':
            return self._make_ajax_call(url)
        elif self.adapter_name == 'httpNodeAdapter':
            return self._make_http_call(url)

    def _make_ajax_call(self, url):
        # Simulando uma chamada AJAX
        print(f"Fetching via AJAX: {url}")
        return f"Response from AJAX: {url}"

    def _make_http_call(self, url):
        # Simulando uma chamada HTTP via Node
        print(f"Fetching via HTTP: {url}")
        return f"Response from HTTP: {url}"
```
🛑 Para adicionar um novo tipo de adapter, é necessário alterar HttpRequester.fetch(), o que quebra o OCP.

**Exemplo Bom (seguindo OCP):**
```python
from abc import ABC, abstractmethod

class Adapter(ABC):
    @abstractmethod
    def request(self, url):
        pass
```

```python
class AjaxAdapter(Adapter):
    def request(self, url):
        print(f"Fetching via AJAX: {url}")
        return f"Response from AJAX: {url}"
```

```python
class NodeAdapter(Adapter):
    def request(self, url):
        print(f"Fetching via Node HTTP: {url}")
        return f"Response from HTTP: {url}"
```

```python
class HttpRequester:
    def __init__(self, adapter: Adapter):
        self.adapter = adapter

    def fetch(self, url):
        return self.adapter.request(url)
```

**Uso:**
```python
adapter = AjaxAdapter()
requester = HttpRequester(adapter)
response = requester.fetch("https://api.exemplo.com")
print(response)
```
✅ Vantagens de aplicar OCP:
- Facilidade de extensão, basta criar uma nova subclasse de Adapter.
- Menor risco de bugs já que o código existente permanece intacto.
- Alta coesão, cada classe lida com um único tipo de requisição.
- Reutilização
  
⚠️ Dicas para aplicar OCP em Python:
- Use classes base abstratas (abc.ABC) para definir contratos claros.
- Use injeção de dependência: passe objetos ao invés de instanciá-los internamente.
= Evite if-elif-else para decidir comportamentos com base em tipos — isso sempre sinaliza que o código pode ser melhorado com polimorfismo.

**[⬆ voltar ao topo](#Índice)**

### Princípio de Substituição de Liskov (LSP)

Esse é um termo assustador para um conceito extremamente simples. É formalmente definido como “Se S é um subtipo de T, então objetos do tipo T podem ser substituídos por objetos com o tipo S (i.e., objetos do tipo S podem substituir objetos do tipo T) sem alterar nenhuma das propriedades desejáveis de um programa (corretude, desempenho em tarefas, etc.).” Esta é uma definição ainda mais assustadora.

Em termos simples: toda subclasse deve poder ser usada no lugar da superclasse sem que o comportamento seja quebrado.

**Exemplo Ruim (violando LSP):**
```python
class Bird:
    def fly(self):
        print("Voando alto!")

class Ostrich(Bird):
    def fly(self):
        raise Exception("Avestruzes não podem voar!")
```

**Uso:**
```python
def make_bird_fly(bird: Bird):
    bird.fly()

bird1 = Bird()
bird2 = Ostrich()

make_bird_fly(bird1)  # Ok
make_bird_fly(bird2)  # 💥 Vai lançar exceção!
```
🛑 Isso quebra o LSP, pois Ostrich não é realmente substituível por Bird se não suporta o mesmo comportamento (voar).

**Exemplo Bom (respeitando LSP):**

1. Criamos uma abstração mais precisa:
```python
from abc import ABC, abstractmethod

class Bird(ABC):
    @abstractmethod
    def make_sound(self):
        pass
```

2. Criamos uma interface separada para aves que voam:
```python
class FlyingBird(Bird):
    @abstractmethod
    def fly(self):
        pass
```

3. Subclasses especializadas:
```python
class Parrot(FlyingBird):
    def fly(self):
        print("O papagaio está voando!")

    def make_sound(self):
        print("Squawk!")

class Ostrich(Bird):
    def make_sound(self):
        print("GRRUUUN!")
```
4. Funções específicas:
```python
def make_it_fly(bird: FlyingBird):
    bird.fly()

def play_bird_sound(bird: Bird):
    bird.make_sound()
```
**Uso correto:**
```python
parrot = Parrot()
ostrich = Ostrich()

make_it_fly(parrot)       # Funciona
play_bird_sound(parrot)   # Funciona
play_bird_sound(ostrich)  # Funciona
# make_it_fly(ostrich)    # 🚫 Não permitido! Ostrich não é FlyingBird
```

✅ Vantagens de aplicar LSP:
- Previsibilidade: Subclasses não surpreendem o código que as usa.
- Facilidade de manutenção: Menos bugs relacionados à herança inadequada.
- Testes mais simples: Substituições funcionam como esperado.
- Design robusto: Separação clara de capacidades entre tipos semelhantes.
  
⚠️ Dica prática:
Se uma subclasse precisa sobrescrever um método da superclasse para desativar ou levantar exceções, isso é um sinal claro de violação do LSP. Provavelmente o design precisa ser repensado.

**[⬆ voltar ao topo](#Índice)**

### Princípio da Segregação de Interface (ISP)
ISP diz que "Clientes não devem ser forcados a depender de interfaces que eles não usam."

Em vez de criar interfaces grandes e genéricas, crie interfaces pequenas e específicas, focadas em responsabilidades isoladas.
Assim, as classes que implementam essas interfaces só precisam se preocupar com o que realmente fazem.

**Exemplo Ruim (violando ISP):**
```python
from abc import ABC, abstractmethod

class Worker(ABC):
    @abstractmethod
    def work(self):
        pass

    @abstractmethod
    def eat(self):
        pass
```
```python
class HumanWorker(Worker):
    def work(self):
        print("Trabalhando...")

    def eat(self):
        print("Comendo...")
```
```python
class RobotWorker(Worker):
    def work(self):
        print("Trabalhando...")

    def eat(self):
        raise NotImplementedError("Robôs não comem!")
```
🔴 Problema: A RobotWorker foi forçada a implementar o método eat(), mesmo não precisando dele. Isso quebra o ISP.

**Exemplo Bom (respeitando ISP):**
1. Dividimos em interfaces específicas:
```python
class Workable(ABC):
    @abstractmethod
    def work(self):
        pass

class Eatable(ABC):
    @abstractmethodmethod
    def eat(self):
        pass
```

2. Classes implementam somente o necessário:
```python
class HumanWorker(Workable, Eatable):
    def work(self):
        print("Humano trabalhando...")

    def eat(self):
        print("Humano comendo...")

class RobotWorker(Workable):
    def work(self):
        print("Robô trabalhando...")
```

3. Funções que esperam comportamentos específicos:
```python
def manage_work(worker: Workable):
    worker.work()

def manage_lunch(worker: Eatable):
    worker.eat()
```

**Uso:**
```python
human = HumanWorker()
robot = RobotWorker()

manage_work(human)   # Ok
manage_work(robot)   # Ok

manage_lunch(human)  # Ok
# manage_lunch(robot) # 🚫 Erro! Robôs não comem e nem implementam Eatable
```
✅ Vantagens do ISP:
- Classes mais simples, implementam só o necessário
- Alta coesão e baixo acoplamento, ou seja, menos dependências e responsabilidades
- Facilidade para testar e manter, menos código "morto" ou não utilizado
- Maior reutilização, com interfaces menores se encaixam melhor em outros contextos
  
**[⬆ voltar ao topo](#Índice)**

### Princípio da Inversão de Dependência  (DIP)

Este princípio nos diz duas coisas essenciais:
 - Alto nível: regras de negócio (o que o sistema faz).
 - Baixo nível: implementações (como o sistema faz).

Ao invés de um módulo de alto nível depender de um de baixo nível diretamente, ambos devem depender de interfaces/abstrações.

**Exemplo Ruim (violando DIP):**
```python
class MySQLDatabase:
    def connect(self):
        print("Conectando ao MySQL...")

class UserRepository:
    def __init__(self):
        self.db = MySQLDatabase()  # 🔴 Alta dependência da implementação concreta

    def get_user(self, user_id):
        self.db.connect()
        print(f"Buscando usuário {user_id}")
```
🔴 Problema: UserRepository está acoplado diretamente ao MySQL. Se mudar para PostgreSQL ou SQLite, você terá que editar essa classe — e isso fere o princípio do aberto/fechado (OCP) também.

**Exemplo Bom (seguindo DIP):**
1. Criamos uma abstração (interface):
```python
from abc import ABC, abstractmethod

class Database(ABC):
    @abstractmethod
    def connect(self):
        pass
```
2. Implementações específicas:
```python
class MySQLDatabase(Database):
    def connect(self):
        print("Conectando ao MySQL...")

class PostgresDatabase(Database):
    def connect(self):
        print("Conectando ao PostgreSQL...")
```
3. Módulo de alto nível depende da abstração:
```python
class UserRepository:
    def __init__(self, db: Database):
        self.db = db  # ✅ Depende da abstração

    def get_user(self, user_id):
        self.db.connect()
        print(f"Buscando usuário {user_id}")
```
4. Injetando a dependência:
```python
mysql = MySQLDatabase()
repo_mysql = UserRepository(mysql)
repo_mysql.get_user(42)

postgres = PostgresDatabase()
repo_postgres = UserRepository(postgres)
repo_postgres.get_user(99)
```
✅ Vantagens do DIP:
- Código desacoplado: Mudanças em implementações concretas não afetam o alto nível
- Facilidade para testes: Pode-se facilmente usar mocks ou stubs
- Alta flexibilidade/extensibilidade: Trocar banco, API, serviço, etc., sem quebrar o sistema
- Melhor manutenção:O código evolui com mais segurança e clareza

⚠️ Dica prática:
Você pode implementar DIP em Python sem frameworks complexos, apenas passando dependências via construtor ou métodos. Para sistemas maiores, frameworks como **FastAPI, Django ou Flask** permitem injeção de dependência com containers, mas o conceito é o mesmo.

**[⬆ voltar ao topo](#Índice)**

## **Testes**
Testes são mais importantes que entregas.
Se você não tem testes (ou tem testes ruins), você nunca saberá com confiança se quebrou algo ao fazer mudanças.

**O objetivo dos testes:**
- Confiança no que está sendo entregue.
- Manutenibilidade com menos risco.
- Paz de espírito para refatorar ou adicionar novas funcionalidades.
- Mesmo que seu time não exija cobertura de 100%, uma cobertura ampla e significativa é essencial.

✅ Ferramentas recomendadas para testes em Python
Testes:
  - pytest – simples, poderoso e altamente extensível.
  - unittest – built-in no Python.

Cobertura:
  - coverage.py – mede e reporta a cobertura de código.

**Não existe desculpa para não escrever testes.**
Você pode escolher o estilo que mais se adequa ao seu time: TDD (Test-Driven Development), BDD (Behavior-Driven Development) ou apenas escrever testes antes de entregar.
O mais importante é:
  **Nunca entregue código sem cobertura adequada**

### Um conceito por teste
Cada teste deve validar apenas um comportamento específico. Isso torna os testes:
  - Mais legíveis
  - Mais fáceis de manter
  - Mais rápidos para depurar

**Exemplo Ruim:**
```python
def test_user_settings():
    user = User("Alice")
    auth = Auth(user)
    assert auth.verify_credentials()
    
    settings = Settings(user)
    settings.change_theme("dark")
    assert settings.theme == "dark"
```
🟥 Problemas:
 - Testa duas coisas ao mesmo tempo: autenticação e mudança de tema.
 - Se falhar, não fica claro o que quebrou.

**Exemplo Bom:**
```python
def test_verify_credentials_valid_user():
    user = User("Alice", password="123")
    auth = Auth(user)
    assert auth.verify_credentials() is True


def test_change_theme_updates_setting():
    user = User("Alice")
    settings = Settings(user)
    settings.change_theme("dark")
    assert settings.theme == "dark"
```

🟩 Vantagens:
- Cada teste cobre apenas um comportamento.
- Fica fácil entender, depurar e manter.

**Cobertura de Testes**
Use coverage.py com pytest:
```bash
coverage run -m pytest
coverage report -m
coverage html  # Gera um relatório visual
```


**Dica: organize seus testes**
Estrutura recomendada de projeto:

```markdown
my_project/
├── app/
│   ├── __init__.py
│   ├── core.py
├── tests/
│   ├── __init__.py
│   ├── test_core.py
```
E dentro de cada test_*.py, foque em:
 - Testes unitários
 - Testes de integração (com dependências reais ou mocks)
 - Testes funcionais (simulando uso real do sistema)

Caso queira se aprofundar mais no tema, deixo disposto aqui 2 dos meus artigos, focados em testes:

[O que são testes unitários e como executá-los em Python](https://medium.com/itautech/o-que-são-testes-unitários-e-como-executá-los-em-python-4d4a1b780fd6)

[Testes unitários em Python: como utilizar o Unittest e executá-los na AWS](https://medium.com/itautech/testes-unitários-em-python-como-utilizar-o-unittest-e-executá-los-na-aws-70d13193e42b)
