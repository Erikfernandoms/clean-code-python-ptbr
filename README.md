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
