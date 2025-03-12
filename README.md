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

